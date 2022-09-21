---
title: "HTML Introduction"
description:
date: "2022-09-10"
slug: "html-introduction"
image: "html-introduction.jpg"
categories:
    - Web Development
---

In this article, I will be generalizing about **HTML** and **HTML5**, considering a simple **HTML document** and anatoming it. What are **HTML tags** and **HTML elements** and how to use them to embed an image, marking up text on your web page for the viewers to see when visiting your site. Finally you will be redirected when clicking on a link on a web page.

## What is HTML?

HTML stands for Hyper Text Markup Language, it’s not a programming language, it’s a markup language for creating Web pages.
- HTML describes the structure of a Web page
- HTML consists of a series of elements
- HTML elements tell the browser how to display the content

**Note: HTML elements** are represented by **HTML tags**. The elements are used to markup text, images, videos, games, playable audio tracks, or whatever else for display in a web browser.

## What is HTML5?

HTML5 is a new version of HTML language that is used for developing modern web. From this point forward, all the tutorials and examples follow the latest HTML5 standard.

## A Simple HTML Document

An HTML document (known as an HTML page) wraps up the basic HTML elements. Now we'll consider how individual HTML elements are combined to form an HTML document (called an HTML page).

```html
<!DOCTYPE html>
<html>
<head>
	<meta charset="UTF-8">
	<title>CLog95</title>
</head>
<body>
	<h1>CLog95</h1>

	<h2>Learn web development</h2>

	<p>Something you need to learn: HTML, CSS, Javascript, PHP</p>

	<p>Here I will show you some basic HTML elements and how to use CSS to style for my web page.</p>

</body>
</html>
```

**Example Explained:**
- `<!DOCTYPE html>` : doctype declaration, this is a required preamble. Here, defines this document as an HTML5 document.
- `<html>` : this element is the root element of an HTML page that wraps all the content on the entire page.
- `<head>` : this element contains meta information about the HTML page. Acts as a container for all the stuff you need to build the site that isn't the content you are showing to your page's viewers.
- `<meta charset="UTF-8">` : this element sets the character set your document should use to UTF-8. Essentially, with this set it can now handle any textual context you might put on it. So, no reason not to set this and it can help avoid some problems later on.
- `<title>` : this element specifies a title for your HTML page, which is the title that is shown in the browser tab the page is loaded in.
- `<body>` : this element defines the document's body. Acts as a container for all the contents that you want to show to web users when they visit your web page, such as text, images, videos, games, playable audio tracks, or whatever else.
- `<h1>` : this element defines a larger heading.
- `<h2>` :  like `<h1>` element, also defines a heading but smaller.
- `<p>` : this element defines a paragraph.

## HTML Tags

HTML tags are element names surrounded by angle brackets

```html
<tagname>content goes here…</tagname>
```

Example of `<h2>` and `<p>` tags:

```html
<h2>Learn web development</h2>
<p>Something you need to learn: HTML, CSS, Javascript, PHP</p>
```

- HTML tags normally come in pair like `<h2>` and `</h2>`, or `<p>` and `</p>`
- The first tag in a pair is **the start tag** (or **opening tag**), the second tag is **the end tag** (or **closing tag**)
- The end tag is written like the start tag, but with **a forward slash** ( **/** ) inserted before the tag name

## What is an HTML Element?

**An HTML element** is defined by **a start tag**, **some content**, and **an end tag**

```html
<tagname>content goes here…</tagname>
```

The HTML element is everything from the start tag to the end tag:

```html
<h1>This is a heading.</h1>
<p>This is a paragraph.</p>
<br>
```

Now, anatomy the `<h1>`, `<p>`, and `<br>` elements above in following table:

| Start tag   | Content     | End tag   |
| --------  | -------- | ------ |
| `<h1>` | This is a heading. | `</h1>` |
| `<p>` | This is a paragraph. | `</p>` |
| `<br>` | none | none |

- Some HTML elements don't have content (like the `<br>` element). These elements are called empty elements. **Empty elements** do not have an end tag!
- Tags in HTML are case-insensitive. This means they can be written in uppercase or lowercase. For example, a `<title>` tag could be written as `<title>`, `<TITLE>`, `<Title>`, `<TiTlE>`, etc.. and it will work well. However, it is best practical to write all tags in lowercase for consistency, readability, and other reasons.

## Images

To embed an image in the HTML page, you need to use the `<img>` element.

Now, I will embed my image ([download here](https://drive.google.com/file/d/1IMvdKo-FqEMBc9KjYi1QDgQ5Wv1Tk-gH/view?usp=sharing)) that has the name **clog95-logo.png**. The code as follow:

```html
<img src="clog95-logo.png" alt="CLog95">
```

Because the `<img>` element is **an empty element** so there is no closing `</img>` tag and no inner content. Its purpose is to embed an image in the HTML page in the place it appears.
You can note `src` and `alt` that I have added to the `<img>` element, which are **two attributes** for the `<img>` element.

- The `src` attribute has value is **unityx-logo.png** placed between quotation marks( "" ). This value is the file path of the image, in this case it is the name of the image.
- The `alt` attribute has value is **CLog95** placed between quotation marks( "" ). This value is a text that will be shown to the viewers in case the image is lost due to some errors.

Now, I will embed the image below the `<h1>` element of the HTML document:

```html
<!DOCTYPE html>
<html>
<head>
	<meta charset="UTF-8">
	<title>CLog95</title>
</head>
<body>
	
	<h1>CLog95</h1>
	
	<img src="clog95-logo.png" alt="CLog95">
	
	<h2>Learn web development</h2>

	<p>Something you need to learn: HTML, CSS, Javascript, PHP</p>

	<p>Here I will show you some basic HTML elements and how to use CSS to style for my web page.</p>

</body>
</html>
```

## Marking up text

HTML has some essential elements for marking up text that include marking up **headings**, **paragraphs**, **lists**.

#### Headings

As you know, a book has the main title, chapter titles, and subtitles, an HTML document can too.

HTML has 6 heading levels from `<h1>` to `<h6>`, in order from largest heading( `<h1>` ) to smallest heading( `<h6>` ). In the example of the HTML document above I have used `<h1>` and `<h2>`.

```html
<h1>UnityX</h1>
<h2>Learn web development</h2>
```

#### Paragraphs

The `<p>` elements are used for containing paragraphs of text, you'll use these frequently when marking up regular text content. In the example above I have used two `<p>` elements.

```html
<p>Something you need to learn: HTML, CSS, Javascript, PHP</p>
<p>Here I will show you some basic HTML elements and how to use CSS to style for my web page.</p>
```

#### Lists

A lot of the web's content is lists and HTML has special elements for these. The most common list types are **ordered** and **unordered** lists:

- **Unordered lists** are lists where the order of the items doesn't matter. Use a `<ul>` element to wrap the unordered items inside.
- **Ordered lists** are lists where the order of the items does matter. Use a `<ol>` element to wrap the ordered items inside.

Each item inside the lists is put inside an `<li>` (list item) element.

In the HTML document above, we have a marking up paragraph as below:

```html
<p>Something you need to learn: HTML, CSS, Javascript, PHP</p>
```

Now, I want to turn the HTML, CSS, Javascript, PHP fragments into an unordered list. I could modify this markup to as bellow:

```html
<p>Something you need to learn:</p>

<ul>
  <li>HTML</li>
  <li>CSS</li>
  <li>Javascript</li>
  <li>PHP</li>
</ul>
```
Now, our HTML document will look like this:
```html
<!DOCTYPE html>
<html>
<head>
	<meta charset="UTF-8">
	<title>CLog95</title>
</head>
<body>
	
	<h1>CLog95</h1>
	
	<img src="clog95-logo.png" alt="CLog95">
	
	<h2>Learn web development</h2>

	<p>Something you need to learn:</p>

	<ul>
	  <li>HTML</li>
	  <li>CSS</li>
	  <li>Javascript</li>
	  <li>PHP</li>
	</ul>

	<p>Here I will show you some basic HTML elements and how to use CSS to style for my web page.</p>

</body>
</html>
```

#### Links

Links are very important - they help you to go anywhere in the internet world! By clicking on the links, you can be redirected to the posts, download files, play an audio track, watch a video, go into other sites or somewhere else.

To add a link, we need to use a simple element - this is the `<a>` element.

For example, I wanted that when I click on the CLog95 heading then I will be redirected to the home of my site at the address https://clog95.com/ - I will use the code below:

```html
<a href="https://clog95.com/">
	<h1>UnityX</h1>
</a>
```

- You can see, I have nested the `<h1>` element inside the `<a>` element.
- The `href` is **an attribute** for the `<a>` element and it has the value is **https://clog95.com/** placed between quotation marks( "" ). This value is an address that you will be redirected to when clicking on the `<a>` element, in this case it is my website address: https://clog95.com/

## The final code of our HTML document:

```html
<!DOCTYPE html>
<html>
<head>
	<meta charset="UTF-8">
	<title>CLog95</title>
</head>
<body>
	
	<a href="https://clog95.com/">
		<h1>CLog95</h1>
	</a>
	
	<img src="clog95-logo.png" alt="CLog95">
	
	<h2>Learn web development</h2>

	<p>Something you need to learn:</p>

	<ul>
	  <li>HTML</li>
	  <li>CSS</li>
	  <li>Javascript</li>
	  <li>PHP</li>
	</ul>

	<p>Here I will show you some basic HTML elements and how to use CSS to style for my web page.</p>

</body>
</html>
```

Now, you can download [the code and image sample here](https://drive.google.com/drive/folders/1Ym2PUT3vCC4s-kWzdJyDl_LkRT8U47ch?usp=sharing) then drag and drop it into your browser to check  the result.

{{< youtube kRPTdSRvX4I >}}