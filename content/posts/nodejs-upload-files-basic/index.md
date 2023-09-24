---
title: "Nodejs Upload Files - Basic"
description: 
date: 2023-08-31T00:19:22-07:00
slug: nodejs-upload-files-basic
image: nodejs-upload-files-basic.jpg
math: 
license: 
hidden: false
comments: true
categories:
    - Node.js
draft: false
---

## Overview
This article I'll walk you through how to upload files using **Node.js** and combine the **Formidable** module.
- The **Formidable** module is a Node.js module for parsing form data, especially file uploads.

## Initialize App

Assuming you’ve already installed Node.js, create a directory to hold your application. For instance, create **my-app** directory:
```shell
mkdir my-app
``` 

Go into **my-app** directory and run the `npm init` command to initialize a **Node.js** application.
```shell
cd my-app
npm init
``` 

- This command prompts you for a number of things, such as the name and version of your application. To simplify, you just hit **ENTER** to accept the defaults of them. The `npm init` will generate a **package.json** file, this file contains the information of **Node.js** application.

## Install the Formidable
Install the **Formidable** module:
```shell
npm install formidable
```

After installing, you have an extra **node_modules** folder and a **package-lock.json** file. The app directory should look like:
```shell
my-app
│   node_modules
│   package.json
|   package-lock.json
```

## Create app.js and form.html files
Insides the **my-app** directory, create the **app.js** and **form.html** files. Should look like:
```shell
my-app
│   node_modules
|   app.js
|   form.html
│   package.json
|   package-lock.json
```

The **app.js** file will contain **Node.js** codes for handling server side. And the **form.html** will display a form to upload files.

## Importing Modules
We need to import some modules: **HTTP**, **File System**, and **Formidable**. The **Formidable** that we have installed above, and other modules is build-in in **Node.js**. Copy and paste the codes below into **app.js** file:
```javascript
const http = require('http');
const fs = require('fs');
const formidable = require('formidable');
```

## Build upload files page
Now, build an upload page to display a form that users can select file upload. Open the **form.html** file then copy and paste the HTML codes below into it.
```html
<div style="width: 200px; margin: auto; padding-top: 24px;">
    <form action="/fileupload" method="post" enctype="multipart/form-data">
        <input type="file" name="fileToUpload"><br>
        <input type="submit">
    </form>
</div>
```
- The `input` element has `type="file"` attribute to upload files, and `name="fileToUpload"` to get information about files uploaded.
- The `form` element has `enctype="multipart/form-data"` attribute specifying  how the form-data should be encoded when submitting it to the server, this attribute is required. The `action="/fileupload"` and `method="post"` will redirect you to the `/fileupload` route with the `post` method when submitting data.

## Create a server
Use the **HTTP** module to create a server that listens on port 3000 for connections.
```javascript
http.createServer(function (req, res) {
  // Code block here
}).listen(3000);
```

## Display the upload files page.
If the user doesn't submit the form, we'll display a form to users select the file upload. And if the user submitted the form, then they will be redirected to the `/fileupload` route with the **post** method.
```javascript
http.createServer(function (req, res) {

  if (req.url == '/fileupload') {

    // Handling files here when the user submitted the form
  } else {

    // Display a form to users select the file upload
    fs.readFile('form.html', function(err, data) {
      res.writeHead(200, {'Content-Type': 'text/html'});
      res.write(data);
      return res.end();
    }); 
  }
}).listen(3000);
```

## Parse the Uploaded File
When a user clicks submit the form, now the server will detect and we can use the **Formidable** module to parse the uploaded file once it reaches the server.

Initialize the form instance of the **Formidable**:
```javascript
const form = new formidable.IncomingForm();
```

Now, we can use the `form` object to parse data from the request when a user submits the form.
```javascript
form.parse(req, (err, fields, files) => {
  // Callback function
})
```
- The `form.parse` takes a request and runs a callback function. This callback function takes 3 arguments **err**, **fields**, **files**

- The `files` argument is an object returned when a user submits the form. This object includes an array and the name of this array is the `fileToUpload` that is the same with the `name="fileToUpload"` that we have set for the `input` element in the `form.html` file. 

Print the `files` argument in the console to see the `fileToUpload` array.
```javascript
console.log(files);
```

The object returned in the console is similar:
```javascript
{
  fileToUpload: [
    PersistentFile {
      _events: [Object: null prototype],
      _eventsCount: 1,
      _maxListeners: undefined,
      lastModifiedDate: 2023-09-01T07:35:30.959Z,
      filepath: 'C:\\Users\\Administrator\\AppData\\Local\\Temp\\86856d601f72dd96aadf71800',
      newFilename: '86856d601f72dd96aadf71800',
      originalFilename: 'chou4.mp3',
      mimetype: 'audio/mpeg',
      hashAlgorithm: false,
      size: 15926,
      _writeStream: [WriteStream],
      hash: null,
      [Symbol(kCapture)]: false
    }
  ]
}
```

We can see different attributes of the `fileToUpload` array above. But pay attention:
- `filepath` is a temporary folder that the file is uploaded to.
- `newFilename` is a random name of the file’s uploaded in the temporary folder.
- `originalFilename` is the original name of the file’s uploaded.

We need to save the file in the new folder with the original name of this file. Now use the **File System** module to move the file from the temporary folder to the folder of your choice, and rename the file.

```javascript
files.fileToUpload.forEach((file) => {

  // Get the temporary folder that the file is stored in
  const oldpath = file.filepath;
  // The new folder that the file is stored in with its original name
  const newpath = 'C:/Users/Administrator/Desktop/NodeJS/ghost_theme/' + file.originalFilename;

  // Use the File System module to rename and save in the new location
  fs.rename(oldpath, newpath, function (err) {
    if (err) throw err;
    res.write('File uploaded and moved!');
    res.end();
  });
});
```

Show a message if the user submits the form without selecting a file.
```javascript
if(!files.fileToUpload) {
  res.write("No file selected!");
  res.end();
}
```
## The full code
The full code of the **app.js**:
```javascript
const http = require('http');
const fs = require('fs');
const formidable = require('formidable');

http.createServer(function (req, res) {

  if (req.url == '/fileupload') {

    // Initialize the form instance of the Formidable
    const form = new formidable.IncomingForm();

    // Parse data from the request when a user submits the form
    form.parse(req, (err, fields, files) => {

      // Print the files object in the console to see infos
      console.log(files);

      // Show a message if the user submits the form without selecting a file
      if(!files.fileToUpload) {
        res.write("No file selected!");
        res.end();
      }      

      files.fileToUpload.forEach((file) => {

        // Get the temporary folder that the file is stored in
        const oldpath = file.filepath;

        // The new folder that the file is stored in with its original name
        const newpath = 'C:/Users/Administrator/Desktop/NodeJS/ghost_theme/' + file.originalFilename;
      
        // Use the File System module to rename and save in the new location
        fs.rename(oldpath, newpath, function (err) {
          if (err) throw err;
          res.write('File uploaded and moved!');
          res.end();
        });

      });      
    })
  } else {

    // Display a form to users select the file upload
    fs.readFile('form.html', function(err, data) {
      res.writeHead(200, {'Content-Type': 'text/html'});
      res.write(data);
      return res.end();
    }); 
  }
}).listen(3000);
```