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

Pour travailler dans un venv, une fois dans le dossier du projet (cmd):

```
workon myproject
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
