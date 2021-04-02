---
template: post
title: Dockerize a Node Js Application
slug: docker-node
socialImage: /media/docker-node.png
draft: false
date: 2021-04-02T03:57:43.938Z
description: This beginners guide aims to assist you in building and deploying a
  simple Node Js application image.
category: Docker, Node
tags:
  - Docker
  - Node
  - Express
---
Docker enables us to avoid the ‘its working on my machine’ pit-hole. It packages our application into a sort of box or in other words, container. You can easily run this container in any machine with its contents, i.e environments intact.

This beginners guide aims to assist you in building and deploying a simple Node Js application image. This guide also assumes you have some basic knowledge of node and express framework. You also need to have docker installed on your machine.

### Create a Node Application with Express

### To create a node app run the command below

```javascript
npm init
```

The command above will walk you through setting up a node application. For beginners, you can choose to press enter after every choice to have the default setup. You can also choose to have your entry point as index.js or app.js. The entry point is where your app will start.

Your package.json should probably look like this once everything is complete.

```javascript
{
  "name": "my-app",
  "version": "1.0.0",
  "description": "",
  "main": "app.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC"
}
```

Installing Express comes next. Express is a really cool framework for node js applications. Install express by running this command.

```javascript
npm install express --save

```

Create your app.js or index.js file in the root directory and paste the code below.

```javascript
const express = require('express')
const app = express()
const port = 3000

app.get('/', (req, res) => {
    res.send('Hello World!')
})

app.listen(port, () => {
    console.log(`Example app listening at http://localhost:${port}`)
})
```

The first part declares a constant variable containing our express module. The application will run on port 3000. A basic route is also defined which will respond with ‘Hello World’. Finally the last part will start the server at port 3000.

You can try testing out your application by running the command:

```javascript
node app.js

```



### Create a docker image

For creation of docker images, you’ll require a Dockerfile. The Dockerfile will contain information on how to set up your image as well as details of your application and how it will run. This includes your application itself, environments as well as configuration details e.t.c

Create a file in your root directory with the name Dockerfile.

```shell
touch Dockerfile

```

Open the file with your favourite text editor. Add the code below.

```dockerfile
FROM node:14

RUN mkdir -p /app

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY app.js .

EXPOSE 8080

CMD [ "node", "app.js" ]

```



1. FROM : the image you will be building from.
2. RUN mkdir : create a directory which will hold your application code.
3. WORKDIR : set the working directory
4. COPY : copy your package.json and package-lock.json files to your directory.
5. RUN : installs your applications dependencies
6. COPY : copy application files.
7. EXPOSE : your application binds to the port initialized here.
8. CMD : command used to start the application.

### Building the image

The command below will build your docker image. The additional -t flag is used to tag your image, sort of like naming your image.

```shell
docker build -t node-docker-app .

```



Make sure you are in the directory that has the Dockerfile. You should see a bunch of commands running on your cli.

```shell
Sending build context to Docker daemon  2.006MB
Step 1/8 : FROM node:14
 ---> 67f79f1b32a3
Step 2/8 : COPY package*.json ./
 ---> Using cache
 ---> de0f1d3680b9
Step 3/8 : RUN mkdir -p /app

....
Step 7/8 : EXPOSE 8080
 ---> Using cache
 ---> 0b0a873879d0
Step 8/8 : CMD [ "node", "app.js" ]
 ---> Running in dc153dd3b2d5
Removing intermediate container dc153dd3b2d5
 ---> a4bf20c4bb2d
Successfully built a4bf20c4bb2d
Successfully tagged node-docker-app:latest
```

Check to see if your image has been built successfully.

```shell
docker images

```

You should see your image.

```shell
REPOSITORY                        TAG                 IMAGE ID            CREATED             SIZE
node-docker-app                          latest              0b700b0d0fc7        4 minutes ago       945MB

```

Now run your image with the following command.

```shell
docker run -p 8081:8080 node-docker-app:latest

```

The -p flag tells your image to run on port 8081 publicly(outside our image) which is similar to the internal port 8080. In your browser, navigate to port 8081 which is publicly accessible. You should see ‘Hello World’ printed on your page.

Thats it!!!