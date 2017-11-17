---
layout: post
title: "Django: Introduction"
subtitle: "Venv et bases"
date: 2017-11-16
author: Sol
category: Django
tags: 
finished: false
mathjax: true
---

## Sources:

* [DjangoGirl](https://tutorial.djangogirls.org/en/django_installation/)
* [Django official](https://docs.djangoproject.com/en/1.11/howto/windows/)
* [Bases](https://docs.djangoproject.com/en/1.11/intro/tutorial01/)

## Virtualenv & virtualenvwrapper

virtualenv and virtualenvwrapper provide a dedicated environment for each Django project you create. While not mandatory, this is considered a best practice and will save you time in the future when you’re ready to deploy your project. Simply type:

### Windows

Installation:

```
pip install virtualenvwrapper-win
```

Création d'env pour un projet

```
mkvirtualenv myproject
```
ou
```
mkvirtualenv path\myproject

Pour travailler dans un venv, une fois dans le dossier du projet (cmd):

```
workon myproject
```

Si le venv est créé à la sauce djangogirls"

```
myvenv\Scripts\activate
```

## Install Django

Django s'installe via pip une fois dans le venv du nouveau projet (s'assurer qu'il est actif via `wokon myproject`:

```
pip install django
```

Pour tester l'installation:

```
django-admin --version
```

Lancer serveur local:

```
python manage.py runserver
```
