---
layout: post
title: "Web: Servers and Python app"
subtitle: ""
date: 2017-12-12
author: Sol
category: Web
tags: 
finished: false
mathjax: true
---

# Cultutre G

* [Python & servers](https://www.airpair.com/python/posts/python-servers)
* [Apache vs Nginx](https://www.digitalocean.com/community/tutorials/apache-vs-nginx-practical-considerations)
* [History of WSGI + imp details](https://www.digitalocean.com/community/tutorials/a-comparison-of-web-servers-for-python-based-web-applications)
* [Gunicorn](http://gunicorn-docs.readthedocs.io/en/latest/)
* [uWSGI](https://uwsgi-docs.readthedocs.io/en/latest/)


## Tutos

* [CGI implementation Tutoral](http://www.jmarshall.com/easy/cgi/)
* [WSGI implementation tutorial](http://lucumr.pocoo.org/2007/5/21/getting-started-with-wsgi/)
* [Configuring Gunicorn with Nginx](https://www.digitalocean.com/community/tutorials/how-to-deploy-python-wsgi-apps-using-gunicorn-http-server-behind-nginx)
* [Configuring uWSGI with Nginx](https://www.digitalocean.com/community/tutorials/how-to-deploy-python-wsgi-applications-using-uwsgi-web-server-with-nginx)

## URL && URN = URI

* **URI**: Uniform Resource **Identifier**
* **URL**: Uniform Resource **Locator**
* **URN**: Uniform Resource **Name**

![](https://upload.wikimedia.org/wikipedia/commons/thumb/d/dd/URI_Venn_Diagram.png/270px-URI_Venn_Diagram.png)
>Diagramme de Venn des catégories de schéma Uniform Resource Identifier (URI). 

[Wikipedia](https://fr.wikipedia.org/wiki/Uniform_Resource_Identifier): Les schémas dans les catégories URL (locator) et URN (name) qui ont toutes deux la fonction de ressource IDs, ainsi URL et URN sont des sous-ensembles de URI. Ce sont aussi, généralement, des ensembles disjoints. Cependant, beaucoup de schémas ne peuvent pas être catégorisés strictement selon l'une ou l'autre des catégories, parce que tous les URI peuvent être traités comme des noms, et certains schémas comportent des aspects des deux catégories – ou aucune.

# Introduction


## HTTP (Protocol)
Hypertext Transfer Protocol: Set of rules and specs which govern the transfer of webpages and other data files on the internet.

A browser is an HTTP client because it sends requests to an HTTP server (**web server**), which then sends responses back to the client. The standard (and default) port for HTTP servers to listen on is 80, though they can use any port. More [here](http://geekexplains.blogspot.ch/2008/06/whats-http-explain-http-request-and.html).


$$\bbox[5px,border:1px solid red] {\color{red}{\text{HTTP server = web server !}}}$$


## HTTP Server = web server (software)
HTTP Request and Response have a format! When a user enters a web site, their browser makes a connection to the site’s web server (this is called the **request**). The server looks up the file in the file system and sends it back to the user’s browser, which displays it (this is the **response**). This is roughly how the underlying protocol, HTTP, works.

**Dynamic web sites** are not based on files in the file system, but rather on programs which are run by the web server when a request comes in, and which generate the content that is returned to the user. 

Irrespective of how the client or the server has been implemented, there will always be a way to form a valid HTTP Request for the client to work and similarly the **server needs to be capable of understanding the HTTP Requests sent to it and form valid HTTP Responses to all the arrived HTTP Requests**. 

>Both the client and the server machines should also be equipped with the capability of establishing the connection to each other (in this case it'll be a TCP connection) to be able to transfer the HTTP Requests (client -> server) and HTTP Responses (server -> client).

The Http Server (a program) will accept this request and will let your python script (for example) get the **HTTP Request Method** and **URI**. 

>The HTTP server will handle many requests from images and static resources. 

What about the **dynamically generated urls** ?

```py
@app.route('\displaynews\<name_of_category>', methods=['GET'])
```

Flask will pattern match this route with the request from the browser. But **how does flask parse the http request from the browser?** 

The HTTP Server passes the **dynamically generated urls** to the application server! 

Wait .... What are **application servers** now?


**Apache HTTPD** (HTTPD is the Apache HTTP server program designed to run as a standalone daemon process) and **Nginx** are the two common web servers used with python.


## Application Servers (interface)
Most HTTP servers are written in C or C++, so **they cannot execute Python code directly** - **a bridge is needed between the server and the program**. These bridges, or rather **interfaces**, define how programs interact with the server. This is the application server. **The dynamically generated urls are passed from the Web Server to the Application server**. The application servers matches to url and runs the script for that route. It then returns the response to the WebServer which formulates an HTTP Response and returns it to the client.

Initially python developers used **low-level gateways** for deployment.

## Common Gateway Interface (CGI)
This interface, most commonly referred to as **“CGI”**, is the oldest, and is supported by nearly every web server out of the box. Programs (apps) using CGI to communicate with their web server need to be started by the server for every request. So, every request starts a new Python interpreter – which takes some time to start up – thus making the whole interface only usable for low load situations.
If you want to learn to write one. Follow [this](http://www.jmarshall.com/easy/cgi/) tutorial by JM Marshall.

#### mod_python 
**mod\_python** is an Apache HTTP Server module that integrates the Python programming language with the server. For several years in the late 1990s and early 2000s, Apache configured with mod\_python ran most Python web applications. However, mod\_python wasn't a standard specification. There were many issues while using mod_python. **A consistent way to execute Python code for web applications was needed.**

**FastCgi** and **SCGI** were other low level gateways used for deployment. They tried to solve the performance problem of CGI.
These low-level gateway interfaces are language agnostic.

## Rise of WSGI 
A Web Server Gateway Interface (WSGI) server implements the web server side of the WSGI interface for running Python web applications. WSGI scales and can work in both multithreaded and multi process environments. We can also write middlewares with WSGI. 

>Middlewares are useful for session handling, authentication and many more.

You can read about how to write your WSGI implementation on [this](http://lucumr.pocoo.org/2007/5/21/getting-started-with-wsgi/) blog. A comparison between different WSGI implementations is given at [this](https://www.digitalocean.com/community/tutorials/a-comparison-of-web-servers-for-python-based-web-applications) link.


#### Gunicorn and uWSGI 
**Gunicorn** and **uWSGI** are two different **application servers**.

* **Gunicorn** [Green Unicorn](http://gunicorn-docs.readthedocs.io/en/latest/) is a Python WSGI HTTP Server for UNIX. It is very simple to configure, compatible with many web frameworks and its fairly speedy. [This](https://www.digitalocean.com/community/tutorials/how-to-deploy-python-wsgi-apps-using-gunicorn-http-server-behind-nginx) article by digitalocean shows how to configure gunicorn with nginx.


* **uWSGI** is another option for an application server. [uWSGI](https://uwsgi-docs.readthedocs.io/en/latest/) is a high performance and a powerful WSGI server. There are many configuration options available with uWSGI. [This](https://www.digitalocean.com/community/tutorials/how-to-deploy-python-wsgi-applications-using-uwsgi-web-server-with-nginx) article by digitalocean shows how to configure uWSGI with nginx.

## Apache VS Nginx
[This](https://anturis.com/blog/nginx-vs-apache/) post explains how apache and nginx work.


To summarize:

* Apache creates processes and threads to handle additional connections. While Nginx is said to be event-driven, asynchronous, and non-blocking.
* Aache is powerful but Nginx is fast. Nginx serves up static content quicker.
* Nginx includes advanced load balancing and caching abilities.
* Nginx is a lot lighter than Apache

## Overview

* **WSGI** (Protocole): A Python spec that defines a standard interface for communication between an application or framework and an application/web server. This was created in order to simplify and standardize communication between these components for consistency and interchangeability. This basically defines an API interface that can be used over other protocols.

* **uWSGI** (software): An application server container that aims to provide a full stack for developing and deploying web applications and services. The main component is an application server that can handle apps of different languages. It communicates with the application using the methods defined by the WSGI spec, and with other web servers over a variety of other protocols. This is the piece that translates requests from a conventional web server into a format that the application can process.

* **uwsgi** (another protocol): A fast, binary protocol implemented by the uWSGI server to communicate with a more full-featured web server. This is a wire protocol, not a transport protocol. It is the preferred way to speak to web servers that are proxying requests to uWSGI.