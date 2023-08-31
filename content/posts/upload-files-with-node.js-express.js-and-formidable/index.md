---
title: "Upload Files With Node.js + Express.js and Formidable"
description: 
date: 2023-08-29T01:43:15-07:00
image: upload-files-with-node.js-express.js-and-formidable.jpg
math: 
license: 
hidden: false
comments: true
draft: false
---

## Overview
In this article, I will walk you how to upload files to a **Node.js** server using **Express.js** and **Formidable**.
- **Express.js** is a web framework for Node.js that provides a robust set of features for web and mobile applications.
- **Formidable** is a **Node.js** module for parsing form data, especially file uploads.

## Initialize App
Assuming, you've already installed **Node.js**, create a directory to hold your application, and make that your working directory.
```shell
$ mkdir uploads
```

Go into **uploads** directory and run the `npm init` command to initialize a **Node.js** application.
```shell
$ cd uploads
$ npm init
```

This command prompts you for a number of things, such as the name and version of your application. To simplify, you just hit ENTER to accept the defaults of them. The `npm init` will generate a **package.json** file, this file contains the informations of **Node.js** application.

## Installing required packages
Install **Express.js** framework, **Formidable** module, and **EJS**. **Express.js** and **Formidable** as described above. And **EJS** is a template engine to generate the front end.
```shell
$ npm install express formidable@3 ejs 
```

After installed, you have extra **node_modules** folder and **package-lock.json** file. The app directory should look like:
```shell
uploads
│   node_modules
│   package.json
|   package-lock.json
```

## Create app.js file and views folder
Insides **uploads** directory, create **app.js** file and **views** folder. And go into **views** folder, create **index.ejs** file. Should look like:
```shell
uploads
│   node_modules
└───views
│   │   index.ejs
│   app.js 
│   package.json
|   package-lock.json 
```

The **app.js** file will contain Node.js codes for handling server side. And **index.ejs** will display the form to upload file.

## Importing package
We need to import some packages: **express**, **nodejs file system**, **path**, **url**, and **formidable**. Copy and paste the codes bellow into **app.js** file:
```javascript
import express from 'express';
import fs from 'node:fs';
import path from 'path';
import { fileURLToPath } from 'url';
import { IncomingForm } from 'formidable';
```

To run the app, we need to insert ` "type": "module"` line into **package.json** file. I place this line at the end, should look like:
```json
{
  // Some codes here
  "author": "",
  "license": "ISC",
  "dependencies": {
    "ejs": "^3.1.9",
    "express": "^4.18.2",
    "formidable": "^3.5.0"
  },
  "type": "module" // This line at the end
}

```

## Configuration the app
Configure **Express.js** framework. To parsing data we are using built-in express parser `express.json()`. And set **EJS** as view engine for the app.
```javascript
// Define Express.js application
const app = express();

// Config the built-in JSON parser
app.use(express.json());

// Config EJS as view engine
app.set('view engine', 'ejs');

// Find the root app directory ( your working directory )
const __filename = fileURLToPath(import.meta.url);
const __dirname = path.dirname(__filename);

```

## Start the server and routes
This app starts a server listens on port 3000 for connections.
```javascript
// Listening on port 3000
app.listen(3000);
```

Config route to render **index.ejs** file that displays a form to users when them visit the root URL (/)
```javascript
// Rendering index.ejs for clients when accessing the root URL (/)
app.get('/', (req, res) => {
    res.status(200).render('index');
});
```

Open **index.ejs** file then copy and paste the HTML codes bellow into it. This codes will display a form for users to upload files.
```html
<div style="width:800px; margin:0 auto;" >
    <h2>Upload files using Formidable</h2>
    <form action="/upload" enctype="multipart/form-data" method="post">
        <div style="margin-bottom: 15px;">
            <input type="file" name="myFiles" multiple="multiple" />
        </div>
        <input type="submit" value="Upload" />
    </form>
</div>
```
Notes:
- The `input` element has  `type="file"` attribute to upload files, and `name="myFiles"` to get information of file uploaded.
- The `form` element has `enctype="multipart/form-data"` attribute specifies how the form-data should be encoded when submitting it to the server, this attribute is required. The `action="/upload"` and `method="post"` will redirect you to `/upload` route with `post` method when submit data.

When a user submit data to server, then redirect user to `/upload` route with `post` method. Now, server will detect and invoke the `upload ` callback function.
```javascript
app.post('/upload', upload);
```

Next, we explore the main upload function that handles upload data and save file on the server side.

## Handling file upload on server
Let's initialize the **uploads** directory to hold your upload files. Checking if the **uploads** directory does not exist then create it.
```javascript
const uploadDir = path.join(__dirname + '/uploads');
if (!fs.existsSync(uploadDir)) fs.mkdirSync(uploadDir,'0777', true);
```

Sets some options when initialize a form formidable. For our app we are setting upload directory,maximum file size , keep extension of files, empty files and also a filter function. The filter function is discussed later. Assigns the custom options for `customOptions` variable.
```javascript
const customOptions = { uploadDir: uploadDir , keepExtensions: true, allowEmptyFiles: false, maxFileSize: 5 * 1024 * 1024 * 1024, multiples: true ,filter: filterFunction };
```
Initialize the form formidable with custom options that assigned for `customOptions` variable.
```javascript
const form = new IncomingForm(customOptions);
```

Now, we can use `form` object to handles data from `request`
```javascript
form.parse(req, (err, field, file) => {
    // Callback function
})
```
- The `form.parse` function takes request and run a callback function. The callback function takes 3 arguments **err**, **field**, **file**.
- When a user submits data. It returns an object that includes an array and the name of this array is the `myFiles` that the same with `name="myFiles"` that we have set for the `input` element in `index.ejs` file.

Now, we get the file upload information from `myFiles` array. And store the file upload in the **uploads** folder on server with the name is its original name.
```javascript
form.parse(req, (err, field, file) => {
    if (err) throw err;

    file.myFiles.forEach((file) => {
        const newFilepath = `${uploadDir}/${file.originalFilename}`;
        fs.rename(file.filepath, newFilepath, err => err);
    });
    return res.status(200).json({ message: ' File Uploaded ' });
})
```

## The filter function
Remember that we set a filter function in `customOptions` variable. Now, built it:
```javascript
const filterFunction = ({ name, originalFilename }) => {
    if (!(originalFilename && name)) return 0;
    return 1;
}
```
- The function takes 2 arguments is the form name and the original file name.

Show a message if the user submit the form without select the file.
```javascript
if (!file.myfiles) return res.status(401).json({ message: 'No file Selected' });
```

## Full code
This is full code of **app.js**:
```javascript
import express from 'express';
import fs from 'node:fs';
import path from 'path';
import { fileURLToPath } from 'url';
import { IncomingForm } from 'formidable';

// Define Express.js application
const app = express();

// Config the built-in JSON parser
app.use(express.json());

// Config EJS as view engine
app.set('view engine', 'ejs');

// Find the root app directory ( your working directory )
const __filename = fileURLToPath(import.meta.url);
const __dirname = path.dirname(__filename);

const upload = async (req, res) => {
    try {
        // Checking if the uploads directory does not exist then create it.
        const uploadDir = path.join(__dirname + '/uploads');
        if (!fs.existsSync(uploadDir)) fs.mkdirSync(uploadDir,'0777', true);

        // The filter function
        const filterFunction = ({ name, originalFilename }) => {
            if (!(originalFilename && name)) return 0;
            return 1;
        }        

        // Assigns the custom options and a filter function for customOptions variable
        const customOptions = { uploadDir: uploadDir , keepExtensions: true, allowEmptyFiles: false, maxFileSize: 5 * 1024 * 1024 * 1024, multiples: true ,filter: filterFunction };
        // Initialize the form formidable with custom options
        const form = new IncomingForm(customOptions);

        // Use form object to handles data from request
        form.parse(req, (err, field, file) => {
            console.log(file);
            if (err) throw err;

            // Show a message if the user submit the form without select the file
            if (!file.myFiles) return res.status(401).json({ message: 'No file Selected' });

            // Get the file upload information from myFiles array. And store the file upload in the uploads folder 
            file.myFiles.forEach((file) => {
                const newFilepath = `${uploadDir}/${file.originalFilename}`;
                fs.rename(file.filepath, newFilepath, err => err);
            });
            // Show a message when upload file successfully
            return res.status(200).json({ message: 'File Uploaded' });
        })

    } catch (err) {
        res.status(400).json({ message: 'Error occured', error: err });
    }
}

// Rendering index.ejs for clients when accessing the root URL (/)
app.get('/', (req, res) => {
    res.status(200).render('index');
});

// Invoke the upload function when a user submit form with post method
app.post('/upload', upload);

// Listening on port 3000
app.listen(3000);
```