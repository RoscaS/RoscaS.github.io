---
layout: post
title: "Web: Nginx reverse proxy et uWSGI HTTP server"
subtitle: "... on digitalOcean droplet for flask app"
date: 2017-12-12
author: Sol
category: Web
tags: 
finished: false
mathjax: true
---

* [digitalOcean](https://www.digitalocean.com/community/tutorials/how-to-serve-flask-applications-with-uwsgi-and-nginx-on-ubuntu-16-04)

## Install Components

```
$ sudo apt-get update
$ sudo apt-get install python3-pip python3-dev nginx
```

## Python venv

Install:

```
$ sudo pip3 install virtualenv
```

Making the parrent directory for our Flask project:
```
$ mkdir ~/myproject
$ cd ~/myproject
```

Create a virtual environement to store our Flask project requirements:

```
$ virtualenv -p python3 myprojectenv
$ source myprojectenv/bin/activate
```

## Set Up a Flask Application
Now that you are in your virtual environment, we can install Flask and uWSGI and get started on designing our application:

### Install Flask and uWSGI
We can use the local instance of pip to install Flask and uWSGI. Type the following commands to get these two components:

```
$ pip install uwsgi flask
```
> Regardless of which version of Python you are using, when the virtual environment is activated, you should use the pip command (not pip3).

### Create a Sample App
While your application might be more complex, we'll create our Flask app in a single file, which we will call myproject.py:

```
$ nano ~/myproject/myproject.py
```

Basically, we need to import Flask and instantiate a Flask object. We can use this to define the functions that should be run when a specific route is requested:

`~/myproject/myproject.py`:
```py
from flask import Flask
app = Flask(__name__)

@app.route("/")
def hello():
    return "<h1 style='color:blue'>Hello There!</h1>"

if __name__ == "__main__":
    app.run(host='0.0.0.0')
```

You should have a UFW firewall enabled. In order to test our application, we need to allow access to port 5000.

Open up port 5000 by typing:
```
$ sudo ufw allow 5000
Rule added
Rule added (v6)
```

Now, you can test your Flask app by typing:
```
$ python myproject.py
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
```

Visit your server's domain name or IP address followed by :5000 in your web browser:

```
http://server_domain_or_IP:5000
```
When you are finished, hit CTRL-C in your terminal window a few times to stop the Flask development server.

## Create the WSGI Entry Point
Next, we'll create a file that will serve as the entry point for our application. This will tell our uWSGI server how to interact with the application.

We will call the file wsgi.py:
```
$ nano ~/myproject/wsgi.py
```

The file is incredibly simple, we can simply import the Flask instance from our application and then run it:

`~/myproject/wsgi.py`:
```py
from myproject import app

if __name__ == "__main__":
    app.run()
```

### Configure uWSGI
Our application is now written and our entry point established. We can now move on to uWSGI.

#### Testing uWSGI Serving
The first thing we will do is test to make sure that uWSGI can serve our application.

We can do this by simply passing it the name of our entry point. This is constructed by the name of the module (minus the `.py` extension, as usual) plus the name of the callable within the application. In our case, this would be `wsgi:app`.

We'll also specify the socket so that it will be started on a publicly available interface and the protocol so that it will use **HTTP instead of the uwsgi binary protocol**. We'll use the same port number that we opened earlier:

```
$ uwsgi --socket 0.0.0.0:5000 --protocol=http -w wsgi:app
```

returns:
```
*** Starting uWSGI 2.0.15 (64bit) on [Tue Dec 12 14:15:16 2017] ***
compiled with version: 5.4.0 20160609 on 12 December 2017 11:14:36
os: Linux-4.4.0-101-generic #124-Ubuntu SMP Fri Nov 10 18:29:59 UTC 2017
nodename: jrosk
machine: x86_64
clock source: unix
detected number of CPU cores: 1
current working directory: /home/sol/myproject
detected binary path: /home/sol/myproject/myprojectenv/bin/uwsgi
!!! no internal routing support, rebuild with pcre support !!!
*** WARNING: you are running uWSGI without its master process manager ***
your processes number limit is 3913
your memory page size is 4096 bytes
detected max file descriptor number: 1024
lock engine: pthread robust mutexes
thunder lock: disabled (you can enable it with --thunder-lock)
uwsgi socket 0 bound to TCP address 0.0.0.0:5000 fd 3
Python version: 3.5.2 (default, Nov 23 2017, 16:37:01)  [GCC 5.4.0 20160609]
*** Python threads support is disabled. You can enable it with --enable-threads ***
Python main interpreter initialized at 0x13dfd30
your server socket listen backlog is limited to 100 connections
your mercy for graceful operations on workers is 60 seconds
mapped 72760 bytes (71 KB) for 1 cores
*** Operational MODE: single process ***
WSGI app 0 (mountpoint='') ready in 1 seconds on interpreter 0x13dfd30 pid: 7894 (default app)
*** uWSGI is running in multiple interpreter mode ***
spawned uWSGI worker 1 (and the only) (pid: 7894, cores: 1)
[pid: 7894|app: 0|req: 1/1] 178.195.37.4 () {36 vars in 749 bytes} [Tue Dec 12 14:15:35 2017] GET / => generated 40 bytes in 1 msecs (HTTP/1.1 200) 2 headers in 79 bytes (1 switches on core 0)
```

When you have confirmed that it's functioning properly, press CTRL-C in your terminal window.

We're now done with our virtual environment, so we can deactivate it:

```
$ deactivate
```

Any Python commands will now use the system's Python environment again.

## Creating a uWSGI Configuration File
We have tested that uWSGI is able to serve our application, but we want something more robust for long-term usage. We can create a uWSGI configuration file with the options we want.

Let's place that in our project directory and call it `myproject.ini`: