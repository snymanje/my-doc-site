---
id: Nodejs-API-wrapper-for-PowerShell
title: Nodejs API wrapper for PowerShell
description: In this post I'm going to create a very simple Web API in Node, that will run a Powershell command on the back-end to retrieve information about a running process. I'll be using three modules to assist me with this, express.js, nodemon and node-powershell.
---

In this post I'm going to create a very simple Web API in Node, that will run a Powershell command on the back-end to retrieve information about a running process.
I'll be using three modules to assist me with this, express.js, nodemon and node-powershell.

Lets get started,

1.  Create a new folder and open it with VSCode

2.  Open the Terminal with Ctrl + `

3.  Run npm init and except all the defaults

4.  npm install express node-powershell nodemon

5.  Edit the package.json file and the 2 lines below in the scripts section.
    a. "dev": "nodemon server.js",
    b. "start":"node server.js",

```javascript
{
  "name": "powershellapi",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "dev": "nodemon server.js",
    "start": "node server.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "express": "^4.16.4",
    "node-powershell": "^4.0.0"
  }
}
```

5.  In the root of the project create a new file and call it server.js and add the below code.

```javascript
//Imports
const express = require("express");

//Setup and configure app
const app = express();
const port = 5000;

//route request
app.use("/", (req, res) => {
  res.json("Hello from Node");
});

//Initialize app
app.listen(port, () => console.log(`Server listening on port ${port}!`));
```

Execute npm run dev, open your browser and navigate to http://localhost:5000, you should get the results below.

```javascript
Hello from Node
```

7.  Next we need to import node-powershell, create a new shell instance and run a powershell command when we get a request.

```javascript
//Imports
const express = require("express");
const Shell = require("node-powershell");

//initialize a shell instance
const ps = new Shell({
  executionPolicy: "Bypass",
  noProfile: true,
});

//Setup and configure app
const app = express();
const port = 5000;

//route request
app.use("/", (req, res) => {
  ps.addCommand(`Get-Process | ? { $_.name -like '*code*' }`);
  ps.invoke()
    .then((response) => {
      res.send(response);
    })
    .catch((err) => {
      res.json(err);
    });
});

//Initialize app
app.listen(port, () => console.log(`Server listening on port ${port}!`));
```

In the example above we are running the Get-Process cmdlet and filtering on processes that contain the word "code".

Refreshing the browser will output the below results.

![PowerShell](/img/Nodejs/Executing-Powershell-commands-in-a-Nodejs-API/output.png)

You will need powershell preintalled on you machine where node will be running. ( This is usually preinstalled automatically )

Thanks for reading.
