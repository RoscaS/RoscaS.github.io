---
layout: post
title: "Web: flask app sur serveur apache2"
subtitle: ""
date: 2017-12-03
author: Sol
category: Web
tags: 
finished: false
mathjax: true
---

## Tree:

path: `/var/www/jrosk.tk`

```
.
├── FlaskApp
│    ├── FlaskApp
│    │   ├── FlaskApp.py
│    │   ├── logs
│    │   │   └── error.log
│    │   ├── static
│    │   └── templates
│    └── flaskapp.wsgi
└── venv
```

## Config file apache2

path: `/etc/apache2/sites-enabled/jrosk.tk.conf`

```html
<VirtualHost *>
        ServerName jrosk.tk

        WSGIDaemonProcess FlaskApp user=sol group=sol threads=5
        WSGIScriptAlias / /var/www/jrosk.tk/FlaskApp/flaskapp.wsgi

        <Directory /var/www/jrosk.tk/FlaskApp/FlaskApp>
                WSGIProcessGroup FlaskApp
                WSGIApplicationGroup %{GLOBAL}
                Require all granted
        </Directory>
        ErrorLog /var/www/jrosk.tk/FlaskApp/FlaskApp/logs/error.log

</VirtualHost>
```

## flaskapp.wsgi (leBatard)

>note, ceci est un fichier python, oui, oui

```py
#! /usr/bin/python3

activate_this = '/var/www/jrosk.tk/venv/bin/activate_this.py'
with open(activate_this) as file_:
        exec(file_.read(), dict(__file__=activate_this))


import sys
sys.path.insert(0, '/var/www/jrosk.tk/FlaskApp/FlaskApp')

from FlaskApp import app as application
```

`from FlaskApp import app as application`
* FlaskApp est le nom du fichier qui contient l'application (.py)