---
title: Upload files với Nodejs và Express
description:
date: 2023-08-27T01:35:13-07:00
slug: upload-files-voi-nodejs-va-express
image: 
math: 
license: 
hidden: false
comments: true
draft: false
categories:
    - Node.js
---

## Tổng quan
Trong bài này mình sẽ hướng dẫn các bạn upload files lên server Node.js kết hợp Express.js framework, và module **formidable**.

- **Express.js** là một web framework dành cho Nodejs giúp bạn xây các úng dụng web.

- **Formidable** là một module nodejs được sử dụng để phân tích dữ liệu form, cùng với các file được tải lên.

## Tạo thư mục dự án
Giả sử bạn đã cài **Node.js**, bây giờ sẽ tạo một thư mục tên là **uploads** cho dự án.

```shell
mkdir uploads
```
Đi vào bên trong thu mục **uploads**, và chạy lệnh `npm init` để bắt đầu một sự án Nodejs
```shell
cd uploads
npm init
```
Khi chạy lệnh `npm init` nó sẽ hỏi bạn một số thứ, nhấn Enter để nó tự động theo mặc định. Khi chạy xong sẽ có một file **package.json** được tạo ra. file này chứa thông tin về dự án **Node.js** của bạn.

## Cài đặt các package cần thiết
Ta sẽ cài **Express.js** framework,  module **formidable** và **EJS**. **Express.js** và **formidable** như đã nói ở trên, còn **EJS** là một template engine giúp bạn tạo giao diện.
```shell
npm install express formidable@3 ejs 
```
Sau khi chạy lệnh, bạn sẽ có thêm thự mục **node_modules** và file **package-lock.json** 
```shell
uploads
│   node_modules
│   package.json
|   package-lock.json
```
## Tạo file app.js và thư mục chứa giao diện

Bên trong thư mục dự án tạo file **app.js** và thư mục **views**. Bên trong thư mục **views** tạo file **index.ejs**
```shell
uploads
│   node_modules
└───views
│   │   index.ejs
│   app.js 
│   package.json
|   package-lock.json 
```
File **app.js** là file chính chứa code **Node.js** để sử lý phía server, còn file **index.ejs** sẽ hiển thị giao diện form để bạn tải file lên.

## Import các package
Chúng ta cần import các package: **express**, **nodejs file system**, **path**, **url**, và **formidable**. Dán đoạn mã sau vào file **app.js**
```javascript
import express from 'express';
import fs from 'node:fs';
import path from 'path';
import { fileURLToPath } from 'url';
import { IncomingForm } from 'formidable';
```

Để chạy được dụng, mở file **package.json** và thêm dòng code dưới đây vào cuối:
```shell
"type": "module"
```

## Cấu hình ứng dụng
Cấu hình **express** framework cho ứng dụng, sử dụng bộ phân tích dữ liệu Json của **express**, thiết lập **EJS** là view engine cho ứng dụng
```javascript
// Định nghĩa một ứng dụng Express.js 
const app = express();

// Sử dụng bộ phân tích dữ liệu Json của Express.js 
app.use(express.json());

// Thiết lập EJS là view engine cho ứng dụng
app.set('view engine', 'ejs');

// Tìm thư mộc gốc của ứng dụng
const __filename = fileURLToPath(import.meta.url);
const __dirname = path.dirname(__filename);
```

## Khởi động server và thiết lập route
Ứng dụng của bạn sẽ khởi động và chạy trên port 3000.
```javascript
// Ứng dụng lắng nghe trên port 3000
app.listen(3000);
```

Thiết lập route để render file **index.ejs** trong thư mục **views** tới client khi truy cập vào root URL (/)
```javascript
// Render index.ejs tới client khi truy cập vào url trang chủ (/)
app.get('/', (req, res) => {
    res.status(200).render('index');
});
```

Mở file **index.ejs** và paste đoạn code HTML dưới đây vào để hiện thị form cho người dùng tải file lên
```html
<div style="width:800px; margin:0 auto;" >
    <h2>Upload file sử dụng Formidable</h2>
    <form action="/upload" enctype="multipart/form-data" method="post">
        <div style="margin-bottom: 15px;">
            <input type="file" name="myFiles" multiple="multiple" />
        </div>
        <input type="submit" value="Upload" />
    </form>
</div>
```
Trong đoạn code trên bạn chú ý:
- Thẻ `input` có `type="file` để bạn tải file, `name="myFiles"` để lấy thông tin file nó nó được tải lên.
- Thẻ `form` có thuộc tính `enctype="multipart/form-data"` để mã hoá dữ liệu form khi nó được submit lên server, bắt buộc khi bạn tải file lên. `action="/upload"` và `method="post"` sẽ đưa bạn đến route là `/upload` với phương thức `post` khi bạn submit dữ liệu lên server.

Khi người dùng submit dữ liệu lên server, nó sẽ đi vào đường dẫn `/upload` với phương thức `post`, server sẽ phát hiện ra và gọi tới hàm `upload` để xử lý các dữ liệu form mà người dùng tải lên với dòng code sau
```javascript
app.post('/upload', upload);
```

Tiếp theo chúng ta sẽ khám phá chức năng chính của ứng dụng là xử lý dữ liệu upload và lưu file phía server.

## Xử lý file upload trên server
Tạo thư mục **uploads** để lưu các file bạn tải lên. Đoạn code dưới đây sẽ kiểm tra nếu không có thư mục **uploads** nó sẽ tạo ra thư mục này
```javascript
const uploadDir = path.join(__dirname + '/uploads');
if (!fs.existsSync(uploadDir)) fs.mkdirSync(uploadDir,'0777', true);
```

Khởi tạo một đối tượng form formidable. Ta sẽ truyền cho đối tượng form formidable này một số tuỳ biến và một hàm filter được chỉ định cho biến `customOptions`
```javascript
const customOptions = { uploadDir: uploadDir , keepExtensions: true, allowEmptyFiles: false, maxFileSize: 5 * 1024 * 1024 * 1024, multiples: true ,filter: filterFunction };
```

Khởi tạo đối tượng form formidable và truyền các tuỳ biến cho nó trong biến `customOptions`
```javascript
const form = new IncomingForm(customOptions);
```

Bây giờ bạn có thế sử dụng đối tượng `form` để phân tích dữ liệu từ `request`
```javascript
form.parse(req, (err, field, file) => {
    // Hàm callback
})
```

- Hàm `form.parse` ở trên sẽ nhận vào request và chạy hàm callback. Hàm callback nhận 3 tham số là `err`, `field`, `file`.

- Khi người dùng submit dữ liệu lên, nó sẽ trả về một object bao gồm một mảng chứa thông tin file bạn tải lên. Mảng này tên là `myFiles` trùng với `name="myFiles"` mà chúng ta đã thiết lập trong file `index.ejs`

Bây giờ ta sẽ lấy thông tin file tải lên từ mảng `myFiles` và lưu trữ file này vào thu mục **uploads** trên server với tên file là tên ban đầu của nó.
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

## Hàm filter
Nhớ lại hàm filter mà chúng ta đã truyền cho biến `customOptions` ở trên, bây giờ sẽ xây dựng nó
```javascript
const filterFunction = ({ name, originalFilename }) => {
    if (!(originalFilename && name)) return 0;
    return 1;
}
```

- Hàm filter ở trên nhận 2 tham số là tên của form và tên gốc của file được tải lên.

Hiển thị một thông báo nếu người dùng submit form nhưng chưa chọn file
```javascript
if (!file.myfiles) return res.status(401).json({ message: 'No file Selected' });
```

## Full code
**app.js**
```javascript
    import express from 'express';
    import fs from 'node:fs';
    import path from 'path';
    import { fileURLToPath } from 'url';
    import { IncomingForm } from 'formidable';

    // Tìm thư mộc gốc của ứng dụng
    const __filename = fileURLToPath(import.meta.url);
    const __dirname = path.dirname(__filename);

    // Định nghĩa một ứng dụng Express.js 
    const app = express();

    // Sử dụng bộ phân tích dữ liệu Json của Express.js 
    app.use(express.json());

    // Thiết lập EJS là view engine cho ứng dụng
    app.set('view engine', 'ejs');

    const upload = async (req, res) => {
        try {
            // Kiểm tra có thư mục uploads hay không, không có thì tạo ra
            const uploadDir = path.join(__dirname + '/uploads');
            if (!fs.existsSync(uploadDir)) fs.mkdirSync(uploadDir,'0777', true);
        
            // Hàm filter
            const filterFunction = ({ name, originalFilename }) => {
                if (!(originalFilename && name)) return 0;
                return 1;
            }            
            
            // Thiết lập các tuỳ biến và một hàm filter cho biến customOptions
            const customOptions = { uploadDir: uploadDir , keepExtensions: true, allowEmptyFiles: false, maxFileSize: 5 * 1024 * 1024 * 1024, multiples: true ,filter: filterFunction };
            // Khởi tạo đối tượng form formidable với các tuỳ biến được thiết lập trong biến customOptions
            const form = new IncomingForm(customOptions);

            // Dùng form formidable để phân tích dữ liệu được submit
            form.parse(req, (err, field, file) => {
                console.log(file)
                if (err) throw err;
                
                // Nếu người dùng submit form nhưng chưa chọn file thì hiển thị một thông báo
                if (!file.myFiles) return res.status(401).json({ message: 'No file Selected' });

                // Lấy thông tin file tải lên từ mảng myFiles và lưu file này vào thu mục uploads
                file.myFiles.forEach((file) => {
                    const newFilepath = `${uploadDir}/${file.originalFilename}`;
                    fs.rename(file.filepath, newFilepath, err => err);
                });
                // Trả về một thông báo nếu thành công
                return res.status(200).json({ message: ' File Uploaded ' });
            })

        }
        catch (err) {
            res.status(400).json({ message: 'Error occured', error: err });
        }

    }

    // Render index.ejs tới client khi truy cập vào url trang chủ (/)
    app.get('/', (req, res) => {
        res.status(200).render('index');
    });

    // Gọi tới hàm hàm upload khi người dùng submit form với phương thức post
    app.post('/upload', upload);


    // Ứng dụng lắng nghe trên port 3000
    app.listen(3000);
```

**index.ejs**
```html
<div style="width:800px; margin:0 auto;" >
    <h2>Upload file sử dụng Formidable</h2>
    <form action="/upload" enctype="multipart/form-data" method="post">
        <div style="margin-bottom: 15px;">
            <input type="file" name="myFiles" multiple="multiple" />
        </div>
        <input type="submit" value="Upload" />
    </form>
</div>
```

**package.json**

Chú ý bổ sung `"type": "module"` vào cuối để chạy được úng dụng
```json
{
  "name": "uploads",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "ejs": "^3.1.9",
    "express": "^4.18.2",
    "formidable": "^3.5.0"
  },
  "type": "module"
}
```