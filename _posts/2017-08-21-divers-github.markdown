---
layout: post
title: "Divers: github"
subtitle: "premier connexion + ssh"
date: 2017-08-21
author: Sol
category: Py
tags: divers, fr, en
finished: false
---

## Downloads & Install 
1. [github.com](https://github.com/) $$ \Rightarrow $$ Nouveau compte si pas existant
2. installer [Git Bash](https://git-for-windows.github.io/)

## SSH
1. Ouvrir **Git Bash**
2. `ssh-keygen -t rsa -b 4096 -C "adresse@email.com"` $$ \Rightarrow $$ Crée une clé ssh
3. Quand le texte suivant s'affiche `Enter a file in which to save the key (/c/Users/you/.ssh/id_rsa):[Press enter]` $$ \Rightarrow $$ `Enter`
4. Entrez un mot de passe
5. Encore...

## Ajouter la clé SSH à l'agent ssh

1. Vérifier si l'agent ssh tourne: `eval $(ssh-agent -s)` $$ \Rightarrow $$ devrait retourner un PID
2. Ajouter la clé SSH: `ssh-add ~/.ssh/id_rsa

## Lier la clé SSH au compte GitHub

1. `clip < ~/.ssh/id_rsa.pub`
2. Sur le site github, cliquez en haut à droite sur la photo de profil $$ \Rightarrow $$ **Settings** $$ \Rightarrow $$ **New SSH key** ou **Add SSH key**
3. Dans le champ **title** $$ \Rightarrow $$ nom du pc (osef pas le nom officiel)
4. Coller la clé dans le champ field
5. **Add SSH key**

<span style="color:red">Maintenant envoyez moi votre nom de compte sur Whatsapp que je vous invite sur le projet. </span>
