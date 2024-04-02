---
title: 'Good Practice Using Vue 3 Composables'
date: 2024-04-01T22:47:41+00:00
draft: false
tags:
  - vue 3
  - beginners
---

## Composition API
What i learn when move to Vue 3 is moving from Options API to Composition API. 
Composition API is a set of APIs that allows us to author Vue components using imported functions instead of declaring options. It is an umbrella term that covers the following APIs: 
- Reactivity API 
- Lifecycle Hooks
- Dependency Injection

I want to share about some of good practice when use composables on Vue 3 with a focus on producing clean, maintainable, and testable code. Though composables may appear to be a new beast, they are fundamentally just functions. Hence, this guide grounds its recommendations in time-tested principles of good software design.

**1. File Naming**

Prefix with use and Follow PascalCase : 

```
:white_check_mark: useAuthServices.js
:white_check_mark: useApiRequest.js

:x: AuthServices.js
:x: APIRequest.js
```

**2. Composable Naming**

Use Descriptive Names

```
:white_check_mark: export function useUserData() {}

:x: export function useData() {}

```

**3. Folder Structure**
Place in composables Directory

```bash
├── src
│   ├── composables
│   │   ├── useAuthServices.js
│   │   ├── useApiRequest.js

```

**4. Argument Passing**

Use Object Arguments for Four or More Parameters

```bash
// Good: For Multiple Parameters
useUserData({ id: 1, fetchOnMount: true, token: 'abc', locale: 'en' });

// Also Good: For Fewer Parameters
useCounter(1, true, 'session');

// Bad
useUserData(1, true, 'abc', 'en');
```

**5. Error Handling**

Expose Error State

```bash
// Good
const error = ref(null);
try {
  // Do something
} catch (err) {
  error.value = err;
}
return { error };

// Bad
try {
  // Do something
} catch (err) {
  console.error("An error occurred:", err);
}
return {};
```

**6. Avoid Mixing UI and Business Logic**

Decouple UI from Business Logic in Composables

```bash

// Good
export function useUserData(userId) {
  const user = ref(null);
  const error = ref(null);

  const fetchUser = async () => {
    try {
      const response = await axios.get(`/api/users/${userId}`);
      user.value = response.data;
    } catch (e) {
      error.value = e;
    }
  };

  return { user, error, fetchUser };
}

// In component
setup() {
  const { user, error, fetchUser } = useUserData(userId);

  watch(error, (newValue) => {
    if (newValue) {
      showToast("An error occurred.");  // UI logic in component
    }
  });

  return { user, fetchUser };
}


```

```bash
// Bad
export function useUserData(userId) {
  const user = ref(null);

  const fetchUser = async () => {
    try {
      const response = await axios.get(`/api/users/${userId}`);
      user.value = response.data;
    } catch (e) {
      showToast("An error occurred."); // UI logic inside composable
    }
  };

  return { user, fetchUser };
}
```

**7. Anatomy of a Composable**
Structure Your Composables Well

A composable that adheres to a well-defined structure is easier to understand, use, and maintain. Ideally, it should consist of the following components:

- **Primary State:** The main read-only state that the composable manages.
- **Supportive State:** Additional read-only states that hold values like the status of API requests or errors.
- **Methods:** Functions responsible for updating the Primary and Supportive states. These can call APIs, manage cookies, or even call other composables.
By ensuring your composables follow this anatomical structure, you make it easier for developers to consume them, which can improve code quality across your project.

```bash

// Good Example: Anatomy of a Composable
// Well-structured according to Anatomy of a Composable
export function useUserData(userId) {
  // Primary State
  const user = ref(null);

  // Supportive State
  const status = ref('idle');
  const error = ref(null);

  // Methods
  const fetchUser = async () => {
    status.value = 'loading';
    try {
      const response = await axios.get(`/api/users/${userId}`);
      user.value = response.data;
      status.value = 'success';
    } catch (e) {
      status.value = 'error';
      error.value = e;
    }
  };

  return { user, status, error, fetchUser };
}


```

```bash

// Bad Example: Anatomy of a Composable
// Lacks well-defined structure and mixes concerns
export function useUserDataAndMore(userId) {
  // Muddled State: Not clear what's Primary or Supportive
  const user = ref(null);
  const count = ref(0);
  const message = ref('Initializing...');

  // Methods: Multiple responsibilities and side-effects
  const fetchUserAndIncrement = async () => {
    message.value = 'Fetching user and incrementing count...';
    try {
      const response = await axios.get(`/api/users/${userId}`);
      user.value = response.data;
    } catch (e) {
      message.value = 'Failed to fetch user.';
    }
    count.value++;  // Incrementing count, unrelated to user fetching
  };

  // More Methods: Different kind of task entirely
  const setMessage = (newMessage) => {
    message.value = newMessage;
  };

  return { user, count, message, fetchUserAndIncrement, setMessage };
}

```

**8.Functional Core, Imperative Shell**

(optional) use functional core imperative shell pattern

Structure your composable such that the core logic is functional and devoid of side effects, while the imperative shell handles the Vue-specific or side-effecting operations. Following this principle makes your composable easier to test, debug, and maintain.

Example: Functional Core, Imperative Shell

```bash

// good
// Functional Core
const calculate = (a, b) => a + b;

// Imperative Shell
export function useCalculatorGood() {
  const result = ref(0);

  const add = (a, b) => {
    result.value = calculate(a, b);  // Using the functional core
  };

  // Other side-effecting code can go here, e.g., logging, API calls

  return { result, add };
}



```

```bash

// wrong
// Mixing core logic and side effects
export function useCalculatorBad() {
  const result = ref(0);

  const add = (a, b) => {
    // Side-effect within core logic
    console.log("Adding:", a, b);
    result.value = a + b;
  };

  return { result, add };
}

```

**9. Single Responsibility Principle**

Use SRP for composables

A composable should adhere to the Single Responsibility Principle, meaning it should have only one reason to change. In other words, a composable should have only one job or responsibility. A violation of this principle can result in composables that are difficult to understand, maintain, and test.

```bash

// Good
export function useCounter() {
  const count = ref(0);

  const increment = () => {
    count.value++;
  };

  const decrement = () => {
    count.value--;
  };

  return { count, increment, decrement };
}

```

```bash

// Bad

export function useUserAndCounter(userId) {
  const user = ref(null);
  const count = ref(0);

  const fetchUser = async () => {
    try {
      const response = await axios.get(`/api/users/${userId}`);
      user.value = response.data;
    } catch (error) {
      console.error("An error occurred while fetching user data:", error);
    }
  };

  const increment = () => {
    count.value++;
  };

  const decrement = () => {
    count.value--;
  };

  return { user, fetchUser, count, increment, decrement };
}

```

**10. File Structure of a Composable**

Consistent Ordering of Composition API Features

While the precise order can be adapted to meet the needs of your project or team, it is crucial that the chosen order is maintained consistently throughout your codebase.
Here's a suggestion for a file structure:

- Initializing: Code for setting up initialization logic.
- Refs: Code for reactive references.
- Computed: Code for computed properties.
- Methods: Functions and methods that will be used.
- Lifecycle Hooks: Lifecycle hooks like onMounted, onUnmounted, etc.
- Watch

this is just one example of a possible order, its just important that you have a order and ideally in your project the order is always the same

```bash

import { ref, computed, onMounted } from "vue";

export default function useCounter() {

  // Initializing
  // Initialize variables, make API calls, or any setup logic
  // For example, using a router
  // ...

  // Refs
  const count = ref(0);

  // Computed
  const isEven = computed(() => count.value % 2 === 0);

  // Methods
  const increment = () => {
    count.value++;
  };

  const decrement = () => {
    count.value--;
  };

  // Lifecycle
  onMounted(() => {
    console.log("Counter is mounted");
  });

  return {
    count,
    isEven,
    increment,
    decrement,
  };
}


```
Programming is often more of an art than a science. As you grow in your Vue journey, you may find different strategies and patterns that work better for your specific use-cases. The key is to maintain a consistent, scalable, and maintainable codebase. Therefore, feel free to adapt these guidelines according to the needs of your project.