---
layout: post
title: "Py: Frameworks"
subtitle: "Flask et Django"
date: 2017-11-16
author: Sol
category: Py
tags: 
finished: false
mathjax: true
---

# Sources

## Django
* [DjangoGirl](HTTPs://tutorial.djangogirls.org/en/django_installation/)
* [Django official](HTTPs://docs.djangoproject.com/en/1.11/howto/windows/)
* [Bases](HTTPs://docs.djangoproject.com/en/1.11/intro/tutorial01/)

## Flask
* [fullstack](HTTPs://www.fullstackpython.com)
* [Flask mega tutorial](HTTPs://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world)
* [Open class rooms](HTTPs://openclassrooms.com/courses/creez-vos-applications-web-avec-flask)

----

# Préliminaires et rappels

## Lexique

* **Framework web**: ensemble de modules qui facilite la programmation de sites web **dynamiques**.

## HTTP
Protocole qui permet la communication client/serveur.

* **Requête HTTP** (le client "demande" une page web) contient:
    * chemin (route) vers la page web
    * type
        * **POST**: utilisé pour les validations de formualire
        * **GET**: utilisé pour le reste
    * informations diverses (type de navigateur...)
    * données (optionnel) ex. Si le client valide un formulaire, il envoie des données remplies dans la requête POST. _Des données peuvent êtres transmises avec des requête GET_.

<br>

* **Réponse** (du serveur) contient:
    * page demandée
    * type de la page demandée (**mimtype**)
    * code erreur:
        * 400: Page demandée introuvable
        * 500: Erreur serveur
        * 401: Accès à cette page non-autorisée
        * 400: Requête mal formée (absence de données p.ex)
        * **200**: Tout est ok
<br>

![alt](HTTPs://user.oc-static.com/files/387001_388000/387755.png)

## WSGI (Web Server Geteway Interface)
> La Web Server Geteway Interface est une spécification qui définit une interface entre les serveurs et des applications web pour Python.

Un serveur HTTP ne sait pas interpréter du Python (ou Java ou PHP) directement, il le fait via l'usage d'un module spécifique.

WSGI est une norme qui que Django et Flask respectent et qui permet au serveur HTTP de desservir des sites web en Python.

Dans le snipet suivant, le paramètre `environ` est un dictionnaire qui contient les variables d'environment [CGI](HTTPs://fr.wikipedia.org/wiki/Common_Gateway_Interface)

```python
def application(environ, start_response):
    start_response('200 OK', 
        [('Content-Type', 'text/plain')])
    yield 'Hello World\n'
```

## Chemin (route)

HTTP://www.site.com/forum-81-407-langage-python.html

Si nous décomposons cette url nous avons:

* **HTTP://** : protocole HTTP
* **www$.$site$.$com**: domaine
* **forum-81-407-langage-python.html**: chemin de la page demandée, aussi appelé **route**. C'est le même chemin que celui demandé dans les requêtes HTTP.



# Virtualenv & virtualenvwrapper

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
```

Pour travailler dans un venv, une fois dans le dossier du projet (cmd):

```
workon myproject
```

Si le venv est créé à la sauce djangogirls"

```
myvenv\Scripts\activate
```

# Django

## Installer Django

Django s'installe via pip une fois dans le venv du nouveau projet (s'assurer qu'il est actif via `wokon myproject`:

```
pip install django
```

Tester l'installation:

```
django-admin --version
```

Lancer un serveur local:

```
python manage.py runserver
```

# Flask
C'est un micro-framwork web: il ne fait nativement pas tout mais il est possible d'étendre les possibilités via des extensions.

## Installer flask

```
pip install flask
```

## Premiers pas
L'organisation des fichiers dans le projet est importante. Il est conseillé de ségréguer les différentes applications web dans des dossier différents.


`hello.py`:
```py
from flask import Flask
app = Flask(__name__)

@app.route('/')
def index():
    return "Hello !"

if __name__ == '__main__':
    app.run(debug=True)
```

Cette application est un sie web basique. Si nous nous rendons à l'adresse http://localhost:5000 nous avons une page qui affiche "hello".

La première ligne importe la classe Flask depuis le module flask. Nous nous en servons à la ligne suivante pour instancier l'objet `app`. 

Cet objet est fondamental, il s'agit de notre application, une application WSGI. 

`__name__` le paramètre fournit à l'instanciation de `app` (qui vaut `'__main'` dans ce cas) est utile lorsqu'on a plusieurs applications WSGI.

À la fin du code, nous lançons l'application en mode debug. Cela permet non seulement d'aider à détecter les bugs, mais aussi à auto refresh le serveur local après chaque sauvegarde. C'est très pratique mais a ne surtout pas laisser qund le site sera disponible sur le net.

> Une autre façon de lancer le mode debug est d'écrire après l'instanciation de l'objet Flask `app.debug = True`.

La méthode `run()` de `app` lance le serveur HTTP+WSGI.

La fonction `index` est précédée du décorateur `@app.route` qui prend en paramètre une route. Cette route est celle par laquelle notre fonction sera accessible.

La route `'/'` est spéciale car elle représente la racine du site web. Il n'est donc pas nécéssaire de la préciser dans **l'adresse du navigateur**.

**<span style="color:red">Attention: </span>**
 
Adresse | Route |
---------|----------|
 site.com | / |
 site.com/ | / |
 site.com/forum/ | /forum/ |
 site.com/forum/python | /forum/python |

>La fonction est appelé "index" mais ce n'est pas important. "poule" est un très bon nom aussi.

<span style="color:red"> Dans le jargon, pour désigner une fonction qui renvoie une page web, on utilise le mot **vue**. Donc en Flask, toutes les fonctions décorées par `@app.route` sont des **vue**.</span> 

Si nous ajoutons la vue suivant à la suite de la vue `index`:

```py
@app.route('/contact')
def contact():
    mail = "poule@cochon.com"
    tel  = "0498 41 22 18"
    return "Mail: {} --- Tel: {}".format(mail, tel)
```

nous pouvons l'atteindre à la page http://localhost:5000/contact par contre, si nous tapons http://localhost:5000/contact/ (**noter le slash à la fin**), nous serons gratifiés d'une erreur 404. Ce qui est tout à fait normal vu le paramètre du décorateur qui définit la route de cette page. 

Si nous changons le paramètre du décorateur par `'/contact/'` non seulement nous n'avons plus d'erreur 404 si on tape http://localhost:5000/contact/ mais si nous oublions de l'ajouter, il sera ajouté automatiquement! Donc plus d'erreur 404 !

> Cette information est importante pour éviter de retomber sur des erreurs 404: Flask peut rajouter un slash manquant, mais pas enlever un slash en trop.

## debug

Si nous introduisons une erreur dans le code:

```py
@app.route('/contact/')
def contact():
    # mail = "poule@cochon.com"
    tel  = "0498 41 22 18"
    return "Mail: {} --- Tel: {}".format(mail, tel)
```

À la suite d'appel de la page http://localhost:5000/contact/ la page suivante est généré par le debbuggeur intégré de Flask (activé grace à `debug=True`). Les 3 marques rouges sont importantes:
<br>

![alt](https://user.oc-static.com/files/387001_388000/387798.png)

<br>

* En haut à gauche: Description de l'erreur
* En bas à droite: Une icone pour activer le terminal
* En bas à gauche: Une fois le terminal activé, on peut saisir les commandes python qui seront exécutées à l'endroit du code choisi.

> La commande `dump(objet)` intégrée au debuggeur (remplacer `objet` par le nom de l'objet à étudier), un tableau s'affiche, contenant tous les attributs et méthodes de l'objet étudié.

Si le debuggeur n'était pas activé, on aurait eu droit à une erreur 500 (erreur du serveur).