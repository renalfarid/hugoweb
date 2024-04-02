---
title: 'Golang net/http Router Enhancement'
date: 2024-04-01T21:47:41+00:00
draft: false
tags:
  - golang
  - whats new
---
## whats new ?
Go 1.22 brings two enhancements to the net/http package’s router: method matching and wildcards. These features let you express common routes as patterns instead of Go code. Although they are simple to explain and use, it was a challenge to come up with the right rules for selecting the winning pattern when several match a request.

The new routing features almost exclusively affect the pattern string passed to the two net/http.ServeMux methods Handle and HandleFunc, and the corresponding top-level functions http.Handle and http.HandleFunc. The only API changes are two new methods on net/http.Request for working with wildcard matches.

Before Go 1.22, the code for handling those requests would start with a line like this:

```
http.HandleFunc("/posts/", handlePost)

```

The trailing slash routes all requests beginning /posts/ to the handlePost function, which would have to check that the HTTP method was GET, extract the identifier, and retrieve the post. Since the method check isn’t strictly necessary to satisfy the request, it would be a natural mistake to omit it. That would mean that a request like DELETE /posts/234 would fetch the post, which is surprising at the least.

In Go 1.22, the existing code will continue to work, or you could instead write this: 

```
http.HandleFunc("GET /posts/{id}", handlePost2)

```
This pattern matches a GET request whose path begins “/posts/” and has two segments. (As a special case, GET also matches HEAD; all the other methods match exactly.) The handlePost2 function no longer needs to check the method, and extracting the identifier string can be written using the new PathValue method on Request:

```
idString := req.PathValue("id")

```
The rest of handlePost2 would behave like handlePost, converting the string identifier to an integer and fetching the post.

Requests like DELETE /posts/234 will fail if no other matching pattern is registered. In accordance with HTTP semantics, a net/http server will reply to such a request with a 405 Method Not Allowed error that lists the available methods in an Allow header.

## Precedence
Every HTTP router must deal with overlapping patterns, like /posts/{id} and /posts/latest. Both of these patterns match the path “posts/latest”, but at most one can serve the request. Which pattern takes precedence?

Some routers disallow overlaps; others use the pattern that was registered last. Go has always allowed overlaps, and has chosen the longer pattern regardless of registration order. Preserving order-independence was important to us (and necessary for backwards compatibility), but needed a better rule than “longest wins.” That rule would select /posts/latest over /posts/{id}, but would choose /posts/{identifier} over both. That seems wrong: the wildcard name shouldn’t matter. It feels like /posts/latest should always win this competition, because it matches a single path instead of many.

a good precedence rule led us to consider many properties of patterns. For example, considered preferring the pattern with the longest literal (non-wildcard) prefix. That would choose /posts/latest over /posts/ {id}. But it wouldn’t distinguish between /users/{u}/posts/latest and /users/{u}/posts/{id}, and it seems like the former should take precedence. 

The precedence rule is simple: the most specific pattern wins. This rule matches our intuition that posts/latests should be preferred to posts/{id}, and /users/{u}/posts/latest should be preferred to /users/{u}/posts/{id}. It also makes sense for methods. For example, GET /posts/{id} takes precedence over /posts/{id} because the first only matches GET and HEAD requests, while the second matches requests with any method.

The “most specific wins” rule generalizes the original “longest wins” rule for the path parts of original patterns, those without wildcards or {$}. Such patterns only overlap when one is a prefix of the other, and the longer is the more specific.

What if two patterns overlap but neither is more specific? For example, /posts/{id} and /{resource}/latest both match /posts/latest. There is no obvious answer to which takes precedence, so consider these patterns to conflict with each other. Registering both of them (in either order!) will panic.

Defining precedence by languages instead of expressions makes it easy to state and understand. But there is a downside to having a rule based on potentially infinite sets: it isn’t clear how to implement it efficiently. It turns out can determine whether two patterns conflict by walking them segment by segment. Roughly speaking, if one pattern has a literal segment wherever the other has a wildcard, it is more specific; but if literals align with wildcards in both directions, the patterns conflict.

As new patterns are registered on a ServeMux, it checks for conflicts with previously registered patterns. But checking every pair of patterns would take quadratic time. Use an index to skip patterns that cannot conflict with a new pattern; in practice, it works quite well. In any case, this check happens when patterns are registered, usually at server startup. The time to match incoming requests in Go 1.22 hasn’t changed much from previous versions.
