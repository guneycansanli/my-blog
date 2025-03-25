---
title: "Serving Files with Python's SimpleHTTPServer"
layout: post
date: 2025-03-23 12:20
image: ../assets/images/file-server/main.jpg
headerImage: true
tag:
    - python
    - linux
category: blog
author: guneycansanli
description: Serving Files with Python's SimpleHTTPServer
---


# Serving Files with Python's SimpleHTTPServer

## Introduction

A server processes requests and delivers data over a network. Web servers like Apache and Nginx are powerful but often unnecessary for simple tasks. Python's built-in `http.server` module provides an easy way to serve files locally.

**Note:** This should not be used in production due to basic security checks.

## What is an HTTP Server?

HTTP (HyperText Transfer Protocol) defines rules for communication between a client (e.g., web browser) and a server. When you visit a website, the browser sends a request to a server, which responds with HTML content.

## Python's SimpleHTTPServer Module

For quick file sharing, Python provides `http.server`. This built-in module eliminates the need for complex server setup.

### Starting a Simple HTTP Server

Let's create dummy files to test:
```bash
for i in {1..5}; do touch file-$i; done
```

![file][1]

Run the following command in your terminal from the directory you want to serve:

#### Python 2:

```sh
python -m SimpleHTTPServer 8000
```

#### Python 3:

```sh
python3 -m http.server 8000
```

Now, open `http://localhost:8000` in your browser to access the files in that directory. (or use your remote box ip)

![file][2]

![file][3]


## Using Python Code to Start a Server

Instead of running it via the terminal, we can set up an HTTP server in a script:

```python
import http.server
import socketserver

PORT = 8000
handler = http.server.SimpleHTTPRequestHandler

with socketserver.TCPServer(("", PORT), handler) as httpd:
    print(f"Server started at localhost:{PORT}")
    httpd.serve_forever()
```

Run the script using:

```sh
python3 server.py
```

This serves files from the script's directory at `http://localhost:8000`. (or use your remote box ip)

![file][4]

## Customizing Request Handling

We can extend `SimpleHTTPRequestHandler` to customize response handling. Below, we serve a specific file on the root path (`/`):

```python
import http.server
import socketserver

class CustomHandler(http.server.SimpleHTTPRequestHandler):
    def do_GET(self):
        if self.path == '/':
            self.path = 'index.html'
        return http.server.SimpleHTTPRequestHandler.do_GET(self)

PORT = 8000
server = socketserver.TCPServer(("", PORT), CustomHandler)
server.serve_forever()
```

## Serving Dynamic HTML

We can generate dynamic responses based on query parameters:

```python
import http.server
import socketserver
from urllib.parse import urlparse, parse_qs

class DynamicHandler(http.server.SimpleHTTPRequestHandler):
    def do_GET(self):
        self.send_response(200)
        self.send_header("Content-type", "text/html")
        self.end_headers()
        
        query = parse_qs(urlparse(self.path).query)
        name = query.get("name", ["World"])[0]
        html = f"<html><body><h1>Hello, {name}!</h1></body></html>"
        self.wfile.write(bytes(html, "utf8"))

PORT = 8000
server = socketserver.TCPServer(("", PORT), DynamicHandler)
server.serve_forever()
```

Accessing `http://localhost:8000?name=Guney` displays `Hello, Guney!`.

![file][5]

![file][6]

## Conclusion

Python's `http.server` module is a simple and effective tool for serving local files. While great for development and testing, it should not be used in production environments due to security limitations.


---

* * *

---

Thanks for reading...

---

---

---

---

> :+1: :+1: :+1: :+1: :+1: :+1: :+1: :+1:

---

Guneycan Sanli.

---

[1]: ../assets/images/file-server/file-1.jpg
[2]: ../assets/images/file-server/file-2.jpg
[3]: ../assets/images/file-server/file-3.jpg
[4]: ../assets/images/file-server/file-4.jpg
[5]: ../assets/images/file-server/file-5.jpg
[6]: ../assets/images/file-server/file-6.jpg





