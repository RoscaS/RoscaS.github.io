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

## Création d'env pour un projet

### : façon 1

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

### Façon 2

Une façon commune de nommer l'environement virtuel est `venv`

```
virtualenv venv
```

Pour l'activer:

```
venv\Scripts\activate
```

Pour le stopper:

```
deactivate
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

## Requêtes, Routes et Réponses

### Requêtes

Dans le code suivant noter l'import de `request`. Cet objet représente la requête HTTP envoyée par le client et reçue par le serveur. Tout ce qui est contenu dans la requête HTTP se retrouve dans cet objet: 

* **chemin** de la page demandée 
* **type** de la requête
* **informations** supplémentaires à propos du client
* **données** transmises

```py
from flask import Flask, request
app = Flask(__name__)

@app.route('/coucou')
def dire_coucou():
    return 'Coucou !'

if __name__ == '__main__':
    app.run()
```

#### Chemin de la page demandée

Il est accessible via l'attribut path de l'objet `request`:

```py
@app.route('/')
def racine():
    return "Le chemin de 'racine' est : " + request.path

@app.route('/la')
def ici():
    return "Le chemin de 'ici' est : " + request.path
```

#### Filtrage des méthodes HTTP

Le **type** est plus connu sous le nom de "**méthode HTTP**". Les méthodes HTTP les plus répandues sont GET et POST (pas forcément vrais depuis HTML 5!)

> La méthode POST est géneralement employée lorsque le visiteur valide un formulaire (et envoie donc ses données au serveur dans la requête). Le reste du temps, la méthode GET est employée.

Si nous voulons une page accessible au chemin `'/contact'` et que cette dernière affiche un formulaire de contact, que le visiteur remplit le formulaire, le valide, et qu'un remerciement lui soit affiché à la suite de cela le tout dans une seul et même page `'/contact'`, nous pouvons regarder la méthode HTTP utilisée:

Si le visiteur vient d'arriver sur la page (en cliquant sur un lien ou en tapant directement l'url dans son navigateur), la méthode employée sera GET. En revanche, si il vient de valider le formulaire, ça sera POST qui sera employé:

```py
@app.route('/contact', methods=['GET', 'POST'])
def contact():
    if request.method == 'GET':
        # afficher le formulaire
    else:
        # traiter les données reçues
        # afficher : "Merci de m'avoir laissé un message !"
```

Deux nouveautés dans ce code:
* la méthode employée est accessible via l'attribut `method` de l'objet `request`. 
* on a précisé les méthodesautorisées dans le décorateur. **Sans cela, la seul méthode autorisée est GET**.

on aurait pu écrire ce code différament, en faisant une vue pour la méthode GET et une autre pour la méthode POST:

```py
@app.route('/contact', methods=['GET'])
def contact_formulaire():
    # afficher le formulaire

@app.route('/contact', methods=['POST'])
def contact_traiter_donnees():
    # traiter les données reçues
    # afficher : "Merci de m'avoir laissé un message !"
```

<span style="color:red"> Toujours bien faire attention à ne pas avoir deux routes identiques ou deux vues portant le même nom! </span> Ici, nos routes sont différentes malgré les apparences comme elles ne sont pas accessibles par les mêmes méthodes HTTP.


## Les routes

Les routes (chemin de la page demandée) est accessible dans `request.path`. En pratique, ce n'est pas utile car Flask gère lui même pour exécuter la bonne vue selon l'url demandée grace à `@app.route` qui permet de spécifier une URL d'accès à une vue en filtrant les méthodes HTTP autorisées.

### Une vue plusieurs routes

On peut décorer plusieurs fois une vue avec `@app.route` afin de rendre accessible cette pages par plusieurs adresses:

```py
@app.route('/f_python')
@app.route('/forum/python')
@app.route('/poule')
def forum_python():
    return 'contenu forum python'
```

Cela dit, ce n'est pas une très bonne chose d'utiliser cette possibilité de cette façon car les moteurs de recherche qui veulent indexer notre site web pour le référencer vont croire qu'il s'agit de 3 pages différentes, et on risque de retrouver ces 3 résultats en cherchant cette page dans Google par exemple.

Cette possibilité est utile quand elle est combinée aux **routes personnalisées.**

### Routes personnalisées

<span style="color:red"> Fonctionnalité très utile pour le développement de sites web. </span> 

Disons que nous souhaitons mettre en place une page de discussion sur notre site où les utilisateurs peuvent poster des messages et lire ceux des autres. Les messages seraient triés dans l'ordre décroissant afin de voir les plus récents en premier. Le soucis c'est qu'au bout de quelque temps, on se retrouvera avec trop de messages à afficher et il faudra limiter le nombre de messages par page et ajouter des boutons pour naviguer entre les pages.

Voici ce que nous voulons obtenir:

* La page `'/discussion'` affiche les 50 messages les plus récents
* La page `'/discussion/page/3'` affiche la 3e page de messages (de 101 à 150)
* La page `'/discussion/page/1'` affiche la même chose que la page `'/discussion'`

Les routes personnalisées sont un moyen d'obtenir de telles url. Nous aurions quelque chose de ce genre là:

```py
@app.route('/discussion/page/<num_page>')
def mon_chat(num_page):
    num_page    = int(num_page)
    premier_msg = 1 + 50*(num_page - 1)
    dernier_msg = premier_msg + 50
    return 'affichage des messages {} à {}'.format( premier_msg, dernier_msg)
```
Et qui se comporterait de cette façon:

<div class="image">
    <img src="/00illustrations/py-Frameworks/pages.gif">
</div>

Ce qu'on a fait ici, c'est de rajouter un paramètre obligatoire dans la route: un numéro de page à préciser pour pouvoir calculer les messages à afficher. Pour cela, il y a deux choses à faire:

* Placer dans la route un nom de variable écrit entre `<` et `>`
* Ajouter en paramètre à la vue décorée une variable possédant ce même nom.

En procédant ainsi, on obtient alors un variable `num_page` qui correspond à ce qui a été mis à cet endroit dans l'url. Cette variable est une string or nous voudrions un int, c'est pour cela que nous effectuons une conversion dans le corp de la fonction. Malgré ça, cela va créer une erreur si l'utilisateur entre autre chose qu'un nombre. Pour compenser Flask nous propose une meilleur alternative:

```py
@app.route('/discussion/page/<int:num_page>')
def mon_chat(num_page):
    premier_msg = 1 + 50 * (num_page - 1)
    dernier_msg = premier_msg + 50
    return 'affichage des messages {} à {}'.format( premier_msg, dernier_msg)
```

En ajoutant ce `int:` avant le nom de la variable, les seules valeurs acceptées seront des entiers (sinon erreur 404), et la variable sera automatiquement convertie (plus d'erreur 500)!

> Il existe aussi `float:` pour les nombres à virgule et `path:` pour les strings contenant le symbole `/` (slash)

Il nous reste un dernier problème: pouvoir accéder aux 50 messages les plus récents en nous rendant à l'adress `'/discussion'`. Si on tente de bêtement rajouter une route:

```py
@app.route('/discussion')
```
On obtient une erreur comme il manque une valeur pour `num_page`... La solution est d'utiliser une vleur par défaut pour le premier paramètre `num_page` de notre vue `mon_chat`:

```py
@app.route('/discussion/page/<int:num_page>')
def mon_chat(num_page=1):
    premier_msg = 1 + 50 * (num_page - 1)
    dernier_msg = premier_msg + 50
    return 'affichage des messages {} à {}'.format( premier_msg, dernier_msg) 
```

Il est bien entendu possible de mettre plusieurs variables dans nos routes, et nous ne sommes pas obligés de les séparés par un slash. Par exemple voici une page qui affiche le prénom et le nom passé dans l'adresse:

```py
@app.route('/afficher')
@app.route('/afficher/mon_nom_est_<nom>_et_mon_prenom_<prenom>')
def afficher(nom=None, prenom=None):
    if nom is None or prenom is None:
        return "Entrez votre nom et prénom dans l'url"
    return "Vous vous appelez {} {}!".format(prenom, nom)
```

Si on entre comme url http://localhost:5000//afficher/mon_nom_est_moran_et_mon_prenom_bob
La page affichera bien "vous vous appelez bob moran!

Ce n'est qu'un exemple, et il n'est pas une bonne pratique! Les routes personnalisées nous permettent d'avoir de belles url, pas de transmettre des données. Pour cela, il y a les formulaires !

## Les réponses
Jusque ici, nos pages se contentaient de renvoyer une string. Flask transformait tout seul cette string en une réponse HTTP, et lui attribuait le mimetype `"text/html"` (ce qui signifie que le contenu de la page est du HTML) et le code d'erreur 200 (tout est ok). Voyons maintenant comment modifier ce comportement.

### Changer le mimetype

Essayons de concevoir une page qui génère une image et qui la renvoie telle quelle. Pour ce faire nous allons utiliser le module PIL (Python Image Library) à installer via pip.

```py
from flask import Flask, request
from PIL import Image
from io import StringIO

app = Flask(__name__)

@app.route('/image')
def genere_image():
    mon_image = StringIO()
    Image.new("RGB", (300, 300), "#92C41D").save(mon_image, 'BMP')
    return mon_image.getvalue()

if __name__ == '__main__':
    app.run()
```

En ligne 9, on crée l'objet qui va contenir l'image (un `StringIO` est un fichier socké dans la RAM et non sur le DD), la ligne suivante nous générons l'image de dimensions 300 sur 300 et de couleur verte, au format BMP, et que nous stockons dans l'objet `mon_image`.
Sur la ligne suivante, nous renvoyons le contenu de l'objet `mon_image`... donc l'image elle même.

En se rendant à l'adresse `'/image` on s'attend donc à voir apparaitre notre carré.

Sauf que non. Le problème est que Flask nous renvoie bien l'image mais en oubliant de préciser qu'il s'agit d'une image, justement, et au format BMP. Flask fait croire à notre navigateur qu'il s'agit d'une page HTML comme une autre et le navigateur tente de l'afficher sous forme de texte.

La solution est de simplement dire à Flask que le **mimetype** de cette réponse HTTP n'est pas `'text/html'` comme d'habitude, mais `'image/bmp'`.

Nous allons créer la réponse à partir de l'image puis on va changer son mimetype en manipulant l'objet réponse. Pour ce faire nous avons besoin d'un `import`:

```py
from flask import make_response
```

Ensuite nous pouvons créer un objet réponse HTTP à partir de l'image, changer son mimetype (qui est simplement un attribut de la réponse), et renvoyer la réponse:

```py
@app.route('/image')
def genere_image():
    mon_image = StringIO()
    Image.new("RGB", (300,300), "#92C41D").save(mon_image, 'BMP')
    reponse = make_response(mon_image.getvalue())
    reponse.mimetype = "image/bmp"  # à la place de "text/html"
    return reponse
```

>En principe on ne fait pas appel à `make_response()` car Flask le fait pour nous: Lorsqu'on `return` qulque chose, Flask applique `make_response()` dessus. C'est dans des cas comme celui ci qu'on est amenés à le faire nous même.

### Changer le code d'erreur HTTP

Dans le même registre, on peut éventuellement avoir besoin de changer le code d'erreur HTTp d'une page. Imaginons la page suivante (totalement inutile, mais c'est pour l'exemple):

```py
@app.route('404')
def page_non_trouvee():
    return 'Cette page devrait vous avoir envoyé une err 404'
```

Si on consulte cette page, le texte s'ffiche bien... Donc nous n'avons pas reçu de code d'erreur 404. Nous avons bien obtenu le code 200 (tout est ok). Pour forcer un code d'erreur différent, il faut créer un objet réponse et modifier son attribut `status_code`:

```py
@app.route('/404')
def page_non_trouvee():
    reponse = make_response('Cette page devrait vous avoir renvoyé une err 404')
    reponse.status_code = 404
    return reponse
```

Et de fait l'output dans la console montre:

```
127.0.0.1 - - [17/Nov/2017 21:58:23] "GET /404 HTTP/1.1" 404 -
```

Dans le cas particulier du code d'erreur, on peut le spécidier directement à l'intérier du `make_response()`:

```py
@app.route('/404')
def page_non_trouvee():
    reponse = make_response('Cette page devrait vous avoir renvoyé une err 404', 404)
    return reponse
```

Et même mieux: Comme dit plus haut, Flask applique tout seul `make_response()` à nos return quand on ne l'a pas fait. On peut donc écrire tout simplement ceci:

```py
@app.route('/404')
def page_non_trouvee():
    return 'Cette page devrait vous avoir renvoyé une err 404', 404 
```

On envoie en réalité un tuple dont la seconde valeur est le code d'erreur HTTP. Plus besoin de `make_response()`!


### Personnaliser ses pages d'erreur

Ce que nous venons de faire est en quelque sorte de créer une page d'erreur perso, mais cela ne présente pas beaucoup d'intérêt, car dans le cas général, le visiteur entre une adresse inconnue, et Flask lui renvoie une page d'erreur 404 assez austère. Ce qui serait intéressant serait justement de modifier cette page 404 affichée par défaut.

Flask nous permet de mettre cela en place facilement pour n'importe quel code d'erreu, grace au décorateur `@app.errorhandler`.

Ce décorateur est comparable à `@app. route` à la différence que `@app.route` ssocie une vue à la route présente dans la requête, tandis que `@app.errorhandler` associe une vue à une erreur survenue sur le serveur:

```py
@app.errorhandler(404)
def ma_page_404(error):
    return "Ma jolie page 404", 404
```
Donc que nous allions à la page `.../404` ou sur une page qui n'existe pas nous allons maintenant tomber sur la même page. 

>Noter que nous renvoyons un tuple dont la seconde valeur est le code d'erreur comme vu plus tot. Sans ça notre page d'erreur 404 aurait renvoyé le code 200...

Nous ne nous sommes pas servi du paramètre error de notre vue, car celui-ci contient des informations sur le type d'erreur survenue, or, **dans ce cas là**, il s'agit forcément de l'erreur 404. Mais ce paramètre est utile si on fait une seul vue pour plusieurs codes d'erreur:

```py
@app.errorhandler(401)
@app.errorhandler(404)
@app.errorhandler(500)
def ma_page_erreur(error):
    return "Ma jolie page {}".format(error.code), error.code
```

### Émettre soi-même des erreurs HTTP

Nous avons vu comment modifier le code d'erreur pour une réponse donnée, mais imaginons que nous voulions carrément qu'une page (qui existe bien) se comporte comme si elle n'existait pas? Il faudrait en quelques sortes "rediriger" vers la page 404. Ce comportement est exactement celui permis par la fonction `abort`.

Prenons un exemple concret: Si l'utilisteur n'est pas identifié, la page `'/profil'` affiche une page d'erreur 401 (accès non autorisé):

```py
from flask import abort

@app.route('/profil')
def profil():
    if utilisateur_non_identifie:
        abort(401)
    return "Vous êtes bien identifié, voici la page demandée: ..."
```

Ce petit système de redirection spécifique aux erreurs est bien pratique... Et il existe le même système de redirection pour les pages normale...

### Les redirections

Nous préférerions que la page `'/profil'` redirige vers la page `/'login'` si l'utilisateur n'est pas identifié. Voyons comment faire.

> Commençons par un petit détail technique: lorsqu'on veut rediriger le visiteur vers une autre page, on lui envoie un code d'erreur HTTP 302 accompagné de l'adresse de la page où il doit se rendre. Le navigateur charge alors cette page automatiquement, c'est pour ça qu'on ne voir jamais la page d'erreur 302 apparaitre, tout s'enchaine rapidement.

#### La fonction `redirect`

Elle s'utilise un peu à la façon d'`abort`, sauf qu'au lieu de prendre en paramètre un code d'erreur, elle prend une route. De plus, il faut retourner ce que renvoie `redirect`:

```py
from flask import redirect

@app.route('/google')
def redirection_google():
    return redirect('http://www.google.com')
```

Et si nous revenons à notre problème initial, nous pouvons maintenant le résoudre en faisant ainsi:

```py
from flask import redirect

@app.route('/profil')
def profil():
    if utilisateur_non_identifie:
        return redirect('/login')
    return "Vous êtes bien identifié, voici la page demandée: ..."

@app.route('/login')
def page_de_login():
    # ...
```

... sauf qu'il ne faut **surtout pas** procéder ainsi. On vient de faire quelquechose de vraiment mal: On a indiqué une route en paramètre de `redirect()`. Les routes sont faites pour être écrites une seule fois, dans le `@app.route`. De ctte façon, si on veut changer les routes, cela se fera à un seul endroit, tandis que si on écrit également les routes dans les `redirect()`, il faudra les changer partout!

La bonne méthode est d'utiliser non pas la route, qui peut changer, mais le **nom** de la vue ciblée, qui restera inchangé, même si on modifie sa ou ses routes d'accès. En l'occurence, le nom de la vue est `page_de_login`.

Le problème est que si l'on passe le nom de cette vue à `redirect`, celui ci ne va pas savoir quoi en faire, car il ne peut recevoir qu'une adresse. Il faut donc utiliser une autre fonction, très pratique appelée `url_for()`. Elle va transformer le nom de vue que vous lui passerez (sous forme de **string**) en une adresse, exploitable par `redirect`.

#### la fonction `url_for`

```py
from flask import redirect, url_for

@app.route('/profil')
def profil():
    if user_non_identifie:
        return redirect(url_for('page_de_login'))
    return "Vous êtes bien identifié, voici la page demandée: ..."

@app.route('/login')
def page_de_login():
    # ...
```

Un dernier détail cependant: `url_for` peut aussi générer des routes personnalisées, il suffit de lui passer en paramètre les variables nécessaires dans la route.

Par exemple, si notre page `'/profil'` est en réalité accessible uniquement avec un pseudo de la façon suivante:

```py
@app.route('/profil/<pseudo>')
def afficher_profil(pseudo):
    # ...
```

Alors pour générer l'adresse vers le profil du membre "Luc1664", on utilisera `url_for` de la façon suivante:

```py
url_for('afficher_profil', pseudo="luc1664")
```

## Moteur de templates

Pour intégrer du HTML5/CSS3, flask intègre ce qu'on appelle un moteur de templates/

### Comparaison

Nous pouvons si nous les souhaitons (movaise idée), insérer du HTML dans les strings renvoyé par nos pages:

```py
@app.route('/')
def acceuil():
    return "<h1>Bienvenue !</h1>"
```

Cette façon de faire est suffisante pour un exemple aussi minimal, mais dans le cas d'une page web un peu plus compliquée, cela devient lourd:

```py
@app.route('/')
def accueil():
    mots = ["bonjour", "à", "toi,", "visiteur."]
    puces = ''.join("<li>{}</li>".format(m) for m in mots)
    return """<!DOCTYPE html>
        <html>
            <head>
                <meta charset="utf-8" />
                <title>{titre}</title>
            </head>
        
            <body>
                <h1>{titre}</h1>
                <ul>
                    {puces}
                </ul>
            </body>
        </html>""".format(titre="Bienvenue !", puces=puces)
```

C'est vraiment moche et la page est encore très basique. Il est donc évident que ce n'est pas une bonne façon de faire, sans parler de la difficulté que serait la maintenance de ce site.

Toute la puissance des moteurs de templates est qu'ils nous permettent de coder quelque chose qui évite toute cette répétition, qui permet d'insérer intelligemment et élégamment le contenu de nos variables dans des pages HTML.

Voici à quoi ressemblerait le code précédent en l'adaptant au moteur de template intégré à Flask. Nous aurions deux fichiers:
* Le fichier python `hello.py` qui contient notre vue `acceuil()`
* Le fichier template, que nous appellerons par exemple `acceuil.html`

`hello.py`

```py
from flask import Flask, render_template
app = Flask(__name__)

@app.route('/')
def accueil():
    mots = ["bonjour", "à", "toi,", "visiteur."]
    return render_template('accueil.html', titre="Bienvenue !", mots=mots)

if __name__ == '__main__':
    app.run(debug=True)
```

`accueil.html`
```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8" />
        <title>{{ titre }}</title>
    </head>

    <body>
        <h1>{{ titre }}</h1>
        <ul>
        {% for mot in mots %}
            <li>{{ mot }}</li>
        {% endfor %}
        </ul>
    </body>
</html>
```

### Intégrer les fichiers CSS et Javascript

Question piège: quel chemin écrire dans l'attribut `href` de l'élément HTML `<link>`?

```html
<link href="chemin/vers/mon_style.css" rel="stylesheet" type="text/css" />
```

On n'écrit pas les chemins en dur ! On utilise `url_for()` Cette fonction est utilisable à l'intérieur des templates. Maintenant l'autre questione est de savoir où placer notre fichier `mon_style.css` et comment faire comprendre à `url_for()` qu'on veut l'adresse de ce fichier et pas d'une vue comme auparavant?

C'est tout simple, les fichiers CSS, JS, tout comme les images, c'est à dire les fichiers **statiques**, vont être placés dans un dossier appelé `static` justement. Puis on utilisera `url_for()` de la façon suivante:

```html
url_for('static', filename='mon_style.css')
```

Notre balise `<link>` ressemblera donc à ça au final:

```html
<link href="{{ url_for('static', filename='mon_style.css') }}" rel="stylesheet" type="text/css" />
```


### Organisation des fichiers

Nous avons typiquement l'arborescence suivante dans un projet Flask:

```
projet/
    application.py
    static/
        mon_style.css
        images/
            ma_banniere.png
    templates/
        accueil.html
        login.html
```

## Templates coté utilisateur

### Passer du contenu au template

Les template sont des fichiers HTML dans lesquels on place des sectios de code Jinja2 (qui ressemble fort à du Python). L'exemple le plus simple qu'on puisse imaginer serait un template ne contenant que du HTML:

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8" />
        <title>Accueil</title>
    </head>

    <body>
        <h1>Ceci est la page d'accueil.</h1>
    </body>
</html>
```

Pour faire afficher ce template par notre vue `acceuil()` Flask nous met à disposition la fonction render_template(), à laquelle on va passer le nom du fichier template (par exemple, pour être cohérent, `acceuil.html` en l'occurence). Les fichiers templates doivent être placés dans le dossier nommé `templates`


```py
from flask import Flask, render_template

app = Flask(__name__)

@app.route('/')
def acceuil():
    return render_template('accueil.html')

if __name__ == '__main__':
    app.run(debug=True)
```

> La fonction `rendender_template()` renvoie le contenue de la page web qui va être envoyée au client. Ce contenu n'est rien d'autre qu'une simple string. Tout ce qui a été vu précédament sur les réponses HTTP est donc valable en utilisant les templates.

Si on veut passer au template des variables à afficher, cela se fait également dans la fonction `render_template()`. Il suffit de lui passer ces variable en tant qu'argument optionnels. Par exemple, si on désire afficher la date actuelle dans la page web, on fera ainsi:


```py
from datetime import date

@app.route('/date')
def _date():
    d = date.today().isoformat()
    return render_template('accueil.html', la_date=d)
```

On passe au template une variable qui s'appellera `la_date`. Cette variable sera une simple string qu'on affichera de la façon suivante:


`accueil.html`

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8" />
        <title>Date</title>
    </head>

    <body>
        <h1>La date d'aujourd'hui est : {{ la_date }}</h1>
    </body>
</html>
```

Pour afficher quelque chose dans un template, il suffit de mettre ce quelque chose entre `{{` et `}}`

Il existe deux autres façons de faire en sorte de passer des variables depuis le code Python vers le template:

#### @app.context_processor

Si on se rend compte que dans chque vue, on passe une variable qui est utilisée dans chaque template, il existe une façon desupprimer cette répétition. Par exemple, si on utilise la variable `titre` dans chaque template, voilà comment faire pour la passer à un seul endroit:

```py
@app.context_processor
def passer_titre():
    return dict(titre="Bienvenue!")
```

Il suffit de créer une autre fonction décorée par `@app.context_processor`. Cette fonction **doit renvoyer un dictionnaire où chaque clé sera une variable accessible dans les templates Jinja**

#### Sans rien faire

Certaines variables sont accessibles par défaut dans les templates. C'est le cas des variables 

* `request` : Déja vu plus haut, nous sert entre autres à afficher la valeur de son attribut method (`{{ request.method }}`)
* `config` : Représente la configuration de Flask. Dans le paramètre `debug=True`, `debug` est un item du dictionnaire `config`
* `session` : Représente la session de l'utilisateur
* `g` : Objet fourre-tout où on peut placer ce un peu ce qu'on veut en ajoutant des attributs (ex. `g.titre = "Bienvenue !")

### Afficher le contenu des variables
Il faut se servir de `{{ obj['attr'] }}`, cela v d'abord chercher si un item `attr` est contenu dans `obj`, puis, si il n'y en a pas, si `obj` possède un attribut nommé `attr`.

Ce fonctionnement est le même pour les objets, les listes et les dictionnaires!

> Si il n'y a ni item ni attribut, cela renverra la valeur spéciale `undefined` et ça n'affiche rien.

{% raw %}
### Créer des varibles dans les templates 

On n'est pas obligé de se contenter des variables passées depuis l'extérieur dans nos templates. On peut très bien créernos propres variables. Les int, floats, strings, listes, tuples et dictionnaires sont disponibles. Les opérations mathématiques de base également (+,-,*,/,%,**). La syntaxe pour créer une variables est la même qu'en Python mais précédée du mot clé `set`:

```html
{% set menu = ['accueil', 'news', 'contact'] %}
```

**<span style="color:red"> Attention: </span>**, On a pas utilisé `{{ }}` mais `{% %}`.
* `{{ }}` ne fait qu'**afficher** le contenu d'une vriable
* `{% %}` sert à tout le reste

> Le code HTML du templatate est en dehors de ces sections.

En faisant cela, on crée une variable appelée menu, qui est une liste. Cette variable sera accessible dans tout le reste du template. Si l'on désire que cette variale soit accessible seule,ent dans une certaine zone du template, alors on doit la créer dans un **tag**`with`:

```py
{$ with menu = ['accueil', 'news', 'contact'] $}
    {{ menu[0] }}
    {{ menu[1] }}
    {{ menu[2] }}
{% endwith %}
```

De cette façon, menu est inacessible en dehors des **with**/**endwith**.

### Commentaire en jinja
```py
{# commentaire blah blah #}
```

### Les filtres

Jinja introduit une syntaxe particulièrement élégante pour appliquer une fonction sur une variable: les filtres. De nombreux filtres sont déjà prédéfinis. Les filtres s'utilisent de la façon suivante: (dans cet exemple, on applique un filtre à une variable appelée x):

```py
{{ x|filtre }}
```
Si le filtre dispose de paramètres:
```py
{{ x|filtre('parametre') }}
```
En fin de compte, l'application de filtres utilise la même syntaxe que l'appel d'une méthode, sauf que l'on remplace le symbole `.` par `|` sauf que les filtres ne sont pas des méthodes propres à une classe, ce sont simplement des fonctions.

Il existe beaucoup de filtres disponibles dans Jinja, certains sont là uniquement pour la mise en forme, d'autres non. La liste de tous ces filtres est disponible dans la [doc de Jinja](http://jinja.pocoo.org/docs/2.10/templates/#list-of-builtin-filters) . Voici quelques exemples:

* `capitalize`: met en majuscule la première lettre d'une string et en minuscule la suite. Pas de paramètres. (ex. `{{ titre|capitalize }}`)
* `join`: concatène les valeurs d'une liste en les séparant par le délimiteur passé en paramètre (qui vaut par défaut `''`). Cela fonctionne comme la méthode `join` de `str` (ex. `{{ ["Une", "Petite", "Poule"] | join(', ') }}`)

### Les conditions

Elles se font de la même façon qu'en Python. `if`, `elif`, `else`. Et comme il ne s'agit pas d'un affichge, on utilise les balises `{% %}` et non `{{ }}`. Cependant, Pyhton se base sur l'indendation, alors que Jinja non (on peut placer nos balises comme on le souhaite). Par conséquent, il faut préciser la fin du bloc `if\elif\else` avec le mot clé `endif`:

```py
<p>
    {% if age < 18 %}
            Accès non autorisé aux mineurs.
    {% elif age < 90 %}
            Accès autorisé.
    {% else %}
            Doucement !
    {% endif %}
</p>
```
 Maintenant voici un exemple qui reprend ce qu'on a découvert juste avant (déclaration de variables et utilisation de filtres):

 ```py
{% set age = "20" %}
{% if age|int == 20 %}
        <p>Vous avez 20 ans, vous bénéficiez d'une remise!</p>
{% endif %}
 ```
Ici, on utilise le filtre `int` qui convertit une valeur en entier.

Dans les conditions, on peut utiliser les opérateurs habituels:

```py
==, !=, <, >, <=, >=, is, and, or, not, in
```

### Tests intégrés à Jinja2

Les tests sont des fonctions disponibles dans Jinja et qui s'utilisent dans les conditions grace au mot clé `is`. Tout comme les filtres, il existe des tests qui ne prennent pas de paramètres, et d'autres qui en nécessitent. La liste de tous ce tests est disponible dans la [documentation de jinja](http://jinja.pocoo.org/docs/2.10/templates/#list-of-builtin-tests).

Voici deux exemples avec les tests `defined` et `divisibleby`:

```py
{% if truc is defined %}
        <p>La variable truc n'est pas undefined</p>
{% endif %}
```

```py
{% set age = 20 %}
{% if age is divisibleby(10) %}
        <p>Votre age est un multiple de 10.</p>
{% endif %}
```

### Les boucles

Les `for` aussi fonctionnent comme en Python à la différence qu'on ajoute `{% endfor %}` à la fin. Cependant, il n'y a pas de boucles `while` dans Jinja.

```py
{% set mots = ["bonjour", "à", "toi,", "visiteur."] %}

<ul>
    {% for mot in mots %}
        <li>{{ mot }}</li>
    {% endfor %}
</ul>
```

On peut inverser l'affichage en appliquant un filtre `reverse` sur la liste `mots`:

```py
<ul>
    {% for mot in mots|reverse %}
        <li>{{ mot }}</li>
    {% endfor %}
</ul>
```

Si on ne désire afficher que les 10 premiers mots:

```py
{% if mots|lenght > 10 %}
    <ul>
    {% for i in range(10) %}
        <li>{{ mots[i] }}</li>
    {% endfor %}
    </ul>
{% else %}
    <p>Vous devez entrer dix mots au minimum.</p>
{% endif %}
```

> Cet exemple inutile était destiné à vous présenter le filtre `lenght` ainsi que la fonction `range()`, disponible dans Jinja. Voir la [documentation de jinja](http://jinja.pocoo.org/docs/2.10/templates/#list-of-builtin-tests) pour d'autres fonctions !

### Include

Nos templates commencent à être bien pratiques, mais il reste un problème essentiel: le code se répète entre nos templates, ce qui induit des risques d'erreur lors des modifications, et ce qui alourdit la quantité totale de code inutilement. C'est sur ce problème que vont intervenir l'héritage de template (un peu plus loin dans cet article), ainsi qu'include, que l'on va voir tout de suite.

Include permet, comme son nom l'indique, d'aller inclure le contenu d'un autre fichier template, comme si on effectuait un copier-coller du contenu. Cela peut se revéler particulièrement utile, si, par exemple, tous nos templates commencent ainsi:

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8" />
        <title>{{ titre }}</title>
        <link rel="stylesheet" href="{{ url_for('static', filename='style.css') }}" type="text/css" />
    </head>
</html>
```
Dans un cas comme celui là, il suffit de placer ce code dans un template nommé par exemple `header.html`, puis, dans les templates qui voudront inclure ce code, il suffira de placer, à l'endroit désiré:

```py
{{ include 'header.html' }}
```

Par conséquent, il faudra veiller à ce que les templates qui incluront `header.html` connaissent tous une variable nommée `titre`.

>En réalité, on n'utilisera pas include pour ce genre de choses, on préfèrera utiliser l'héritage de templates. Include sera plutot utile, si, par exemple, certaines pages de notre site affichent un gros formulaire: Il suffira d'écrire une seule fois ce formulaire dans un template séparé et de l'inclure. Le code en sera ainsi mieux découpé.

### L'héritage

L'idée de base est que, au fond, la plupart des pages de notre site sont composées du même squelette, seul le contenu change un peu. On a vu qu'on pouvait utiliser include pour simplifier cela, mais l'héritage de templates est plus puissant. Son principe est de structurer une page en une série de "blocks". Les blocks du template parent qu'on ne veut pas changer dans les templates enfants n'ont pas à être reprécisés. Par contre, ceux que l'on veut modifier n'ont qu'à être réécrits en remplaçnt leur contenu par ce qu l'on veut.

Pour cet exemple, on va se contenter de deux templates:
* Un template qui ne contiendra que le squelette du site
* Un autre qui sera utilisé par la page contact du site

Voyons tout d'abord le template `squelette.html`. C'est une page HTML classique dans laquelle on précise des blocks:

`squelette.html`
```html
<!DOCTYPE html>
<html>
    <head>
    {% block header %}
        <meta charset="utf-8" />
        <title>{% block titre %}Titre par défaut{% endblock %}</title>
    {% endblock %}
    </head>

    <body>
        {% block corps %}{% endblock %}
        {% block footer %}{% endblock %}
    </body>
</html>
```

Comme on le voit, un block se crée en faisant:
```py
{% block nom_du_block %}{% endblock %}
```

Un block peut être initialement vide (il pourra être rempli dans les templates fils), ou il peut contenir déjà du code (qui sera donc présent dans les templates fils, par défaut). Comme on le voit dans cet exemple, un block peut contenir d'autres blocks, ça n'est pas un problème. Voyons maintenant comment réaliser le template de la page contact:

`contact.html`
```html
{% extends "squelette.html" %}

{% block titre %}Page de contact{% endblock %}

{% block corps %}
<h1>Page de contact</h1>
<form action="" method="post">
    <input type="text" name="nom" value="votre nom" />
    <textarea name="message">Votre message ici.</textarea>
    <input type="submit" value="Envoyer" />
</form>
{% endblock %}


{% block footer %}
<footer>Copyright monsite.com 2015-1017</footer>
{% endblock %}
```
On note plusieurs choses:
* Pour préciser qu'on souhaite hériter du template `squelette.html`, on utilise le tag **extends** en lui précisant simplement le nom du template.
* On peut changer le contenu d'un block en le réecrivant (ici on change le titre de la page de cette façon)
* On peut mettre du contenu dans les blocks qui étaient vides (les blocks corps et footer)

L'avantage est qu'on a pas besoin de repréciser le squelette HTML de la page, on se contente simplement de remplir les blocks. On raurait même pu créer de nouveaux blocks, à l'intérieur du formulaire par exemple, et ensuite remplir ces blocks dans un template fils, et ainsi de suite.

Il reste un détail génant: On écrit deux fois le titre de la page (une fois dans le block titre, qui va remplir la balise `<title>`, et une seconde fois dans la balise `<h1>`) alors que le titre est le même. Pour éviter cela, on peut afficher le contenu du block titre dans la balise `<h1>`:

```html
<h1>{{ self.titre() }}</h1>
```

`self.nom_du_block()` renvoie le contenu `nom_du_block` (`self` représente le template actuel).

Il existe une autre astuce très pratique quand on veut garder le contenu du block du parent mais y **rajouter** quelque chose en plus. Par exemple, on pourrait imaginer vouloir garder le contenu du block header, mais y ajouter un `<link>` CSS. Pour cela, on peut utiliser `super()`, qui renvoie le contenu du même block dans le parent:

```html
{% block header %}
    {{ self.supper() }} {# renvoie le contenu du block header 
        (celui où on se situe du template parent (squelette.html)) #}
    <link rel="stylesheet" href="{{ url_for('static', filename='style_contact.css') }}" type="text/css" />
{% endblock %}
```

L'héritage de templates simplifie vraiment le contenu des templates en nous évitant de nous répéter. **<span style="color:red"> Tout comme avec include il ne faut pas oublier, dans nos vues, de donner des valeurs aux variables utilisées dans les templates parents! </span>**


## Templates cotés programmeur

Découvrons comment étendre les possibilités des templates. C'est relativement simple car les filtres et les tests ne sont en fait que de simples fonctions, à écrire dans le code Python.

### Rendre ses fonctions utilisable dans les templates

Pour paser les fonctions aux templates, cela se fait de l'une des façons qu'on a vues pour passer les variables avec `@app.context_processor`:

```py
@app.context_processor
def passer_aux_templates():
    def formater_distance(dist):
        unite = 'm'
        if dist > 1000:
            dist /= 1000.0
            unite = 'km'
        return u'{0:.2f}{1}'.format(dist, unite)
    return dict(format_dist=formater_distance)
```

De cette façon, la fonction `formater_distance` sera disponible dans les templates sous le nom `format_dist`. Cela dit, on préfère généralement utiliser les filtres pour cela, et écrire `distance|format_dist` plutot que `format_dist(distance). Voyons donc comment créer ses propres filtres.

### Filtres perso
Flask nous met à disposition un autre décorateur appelé: `@app.template\_filter('nom\_du\_filtre\_dans\_le\_template') pour créer ses propres filtres.

```py
@app.template_filter('format_dist')
def formater_distance(dist):
    unite = 'm'
    if dist > 1000:
        dist /= 1000.0
        unite = 'km'
    return u'{0:.2f}{1}'.format(dist, unite)
```
>Le code de la fonction n'a ps changé

En faisant comme ça, on peut utiliser `distance|format_dist`!
Si l'on veut créer un filtre prenant des paramètres, il suffit de rajouter des paramètres à cette fonction. <span style="color:red">Le premier paramètre (en l'occurence, dist) représente toujours l'objet auquel on applique le filtre dans le template.</span>

### Tests perso

C'est la même chose: Il suffit d'écrire une fonction dans le code Python puis de la passer aux templates en précisant qu'il s'agit d'un test. Dans cet exemple, on va créer un test pour savoir si un nombre est impair, que l'on utilisera ainsi dans les templates:

```py
{% if nombre is impaire %}
    {{ nombre }} est impaire.
{% else %}
    {{ nombre }} est pair.
{% endif %}
```

Commençons par créer la fonction. **Tout comme pour les filtres, le premier paramètre de cette fonction sera l'objet auqel on l'applique dans le template** (c'est à dire l'objet avant le `is`).

```py
def est_impaire(n):
    if n % 2 == 1:
        return True
    return False
```

Puise rendons accessible cette fonction en tant que test Jinja portant le nom `impair`:

```py
app.jinja_env.test['impaire'] = est_impair
```
Il n'existe malheureusement pas de décorateur pour faire ça.

> De la même manière, on peut passer un filtre à Jinja en faisant `app.jinja\_env.filters['format\_dist'] = formater_distance`, et on peut passer une fonction (ou même une classe) en faisant `app.jinja\_env.global['fonction'] = ma\_fonction`

### Créer des macros Jinja

Une dernière façon d'augmenter les possibilités de Jinja est la créationde macros. À la différnece de la création de fonctions, de filtres ou de tests, cela ne se passe pas dans le code Pyhton, mais uniquement dans les templates. Les macros ne sont ni plus ni moins que des fonctions, leur seul intérêt est de facilement afficher de l'HTML étant donné qu'elle bénéficient de la puissance des temlpates.

Par exemple, si on est lassés d'écrire des balises `<link>` à rallonge, parce qu'on utilise de nombreux fichiers CSS différents selon les templates, on peut se simplifier la vie avec une macro:

```py
{% macro link(nom_fichiers, extension='css') %}
    {% for fichier in nom_fichiers %}
        <link href="{{ url_for('static', filname=fichier + '.' +extension) }}" rel="stylesheet" type="text/css" />
    {% endfor %}
{% endmacro %}
```

Grace à cette macro, on peut remplacer les interminables:

```html
<link href="{{ url_for('static', filename="4.css") }}" rel="stylesheet" type="text/css" />
<link href="{{ url_for('static', filename="8.css") }}" rel="stylesheet" type="text/css" />
<link href="{{ url_for('static', filename="15.css") }}" rel="stylesheet" type="text/css" />
<link href="{{ url_for('static', filename="16.css") }}" rel="stylesheet" type="text/css" />
```

par un simple:

```py
{{ link ['4', '8', '15', '16']}}
```

### utiliser les macros définies dans un autre template

Ce cas est très courant: En pratique, on a même souvent tendance à mettre toutes ses macros dans un seul template, et à les importer dans les templates où on en a besoin. L'import se fait de la même façon qu'on Python:

```py
{% from 'mes_macros.html' import link %}
```

ou bien:

```py
{% import 'mes_macros.html' as macros %}
```

ou encore:

```py
{% from 'mes_macros.html' import link as generer_css %}
```

> Même si ça semble faire beaucoup de nouveautés, la syntaxe de Jinja reprend celle de Python sur de nombreux points, on ne devrait donc normalement pas se perdre.

## Flash

**<span style="color:red"> A REFAIRE !!! </span>**

Cette fonctionnalité est très pratique pour notifier le visiteur de différents messages. 

Exemple typique: quand le visiteur nous écrit un long message sur notre page de contact et valide le formualire, il est bien plus agréable de voir apparaitre un message "Votre mail a bien été envoyé!", plutot que simplement faire réafficher la page. Il en est de même pour toutes les autres actions que peut effectuer un visiteur: Il est important qu'il sache toujours où il en est, si ce qu'il vient d'effectuer a fonctionné, ou sinon, pourquoi ça a échoué.

Flask propose donc deux fonctions pour faciliter la mise en place d'un tel système: `flash()` et `get\_flashed\_message()`

**<span style="color:red"> Pour de mystérieuses raisons, `app.secret_key` doit être définie pour que flash fonctionne </span>**

```py
@app.route('/contact', methods=['GET', 'POST'])
def contact():
    if request.method == 'POST':
        flash(u'Votre message a bien été envoyé!')
        traiter_donnees()
    else:
        flash(u'Erreur dans les données envoyées.')
    return render_template('contact.html')
```

`Flash` prend simlement une chaine en paramètre. Il s'agit du message qui sera affiché au visiteur. A chaque nouveau flash, un message est ajouté à la liste des messages flashés.

Ensuite, dans le template, il suffit de récupérer les messages flashés et de les afficher. Flask nous fournit pour cela la fonction `get_flashed_message()`.

### `get_flashed_message()`

Cette fonction renvoie simplement les messages flashés sous la forme d'une liste. Il suffit donc de le parcourir. Dans cet exemple, l'affichages des messages flashés sera réalisé dans le template `squelette.html`, dont le template `contact.html` héritera. En effet, généralement, on désire que les messages flashés s'affichent quelle que soit la page du site web, il est donc avantageux de placer cette partie dans le template parent.












{% endraw %}