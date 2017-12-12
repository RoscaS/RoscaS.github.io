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

## URL && URN = URI

* **URI**: Uniform Resource **Identifier**
* **URL**: Uniform Resource **Locator**
* **URN**: Uniform Resource **Name**

![](https://upload.wikimedia.org/wikipedia/commons/thumb/d/dd/URI_Venn_Diagram.png/270px-URI_Venn_Diagram.png)
>Diagramme de Venn des catégories de schéma Uniform Resource Identifier (URI). 

[Wikipedia](https://fr.wikipedia.org/wiki/Uniform_Resource_Identifier): Les schémas dans les catégories URL (locator) et URN (name) qui ont toutes deux la fonction de ressource IDs, ainsi URL et URN sont des sous-ensembles de URI. Ce sont aussi, généralement, des ensembles disjoints. Cependant, beaucoup de schémas ne peuvent pas être catégorisés strictement selon l'une ou l'autre des catégories, parce que tous les URI peuvent être traités comme des noms, et certains schémas comportent des aspects des deux catégories – ou aucune.


# Deploy on digital ocean droplet

* [source1 depreciated but nice theory](https://www.digitalocean.com/community/tutorials/how-to-set-up-uwsgi-and-nginx-to-serve-python-apps-on-ubuntu-14-04#definitions-and-concepts)
* [source2: ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-serve-flask-applications-with-uwsgi-and-nginx-on-ubuntu-16-04)

In this guide, we will be setting up a simple Python application using the Flask micro-framework on Ubuntu 16.04. The bulk of this article will be about how to set up the uWSGI application server to launch the application and Nginx to act as a front end reverse proxy.

## Definitions and Concepts

Before we jump in, we should address some confusing terminology associated with the interrelated concepts we will be dealing with. These three separate terms that appear interchangeable, but actually have distinct meanings:

* WSGI: A Python spec that defines a standard interface for communication between an application or framework and an application/web server. This was created in order to simplify and standardize communication between these components for consistency and interchangeability. This basically defines an API interface that can be used over other protocols.

* uWSGI: An application server container that aims to provide a full stack for developing and deploying web applications and services. The main component is an application server that can handle apps of different languages. It communicates with the application using the methods defined by the WSGI spec, and with other web servers over a variety of other protocols. This is the piece that translates requests from a conventional web server into a format that the application can process.

* uwsgi: A fast, binary protocol implemented by the uWSGI server to communicate with a more full-featured web server. This is a wire protocol, not a transport protocol. It is the preferred way to speak to web servers that are proxying requests to uWSGI.

## Install Components

```
$ sudo apt-get update
$ sudo apt-get install python3-pip python3-dev nginx
```
