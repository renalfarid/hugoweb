---
title: 'MongoDb'
date: 2023-09-12T21:47:41+00:00
draft: false
tags:
  - database
  - nosql
---

## Install Mongodb on Mac OS

To install MongoDB on a Mac OS, you can follow these steps:

1. Open a browser and navigate to the MongoDB download page at [https://www.mongodb.com/download-center/community](https://www.mongodb.com/download-center/community).
2. Scroll down to the "MongoDB Community Server" section and click on the "Download" button for the latest stable release.
3. Once the download is complete, open the downloaded file and follow the installation instructions provided by the installer.
4. During installation, you will be prompted to choose the components you want to install, including the MongoDB server, command line tools, and MongoDB Compass (a graphical user interface for MongoDB).
5. Once the installation is complete, you can start the MongoDB server by running the following command in a terminal window:

```
enalfarid@Nals-MacBook-Air ~ % brew services start mongodb/brew/mongodb-community
enalfarid@Nals-MacBook-Air ~ % brew services stop mongodb/brew/mongodb-community
enalfarid@Nals-MacBook-Air ~ % mongosh

```

This will start the MongoDB server on the default port 27017.

This will open the MongoDB shell, where you can interact with the server and perform various operations.

That's it! You now have MongoDB installed on your Mac OS and are ready to start using it.