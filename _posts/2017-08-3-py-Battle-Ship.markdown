---
layout: post
title: "Py: Projet Battle-Ships"
subtitle: ""
date: 2017-08-03
author: Sol
category: Py
tags: divers, fr, en
finished: false
---

## En vrac



### Dictionnaire (Board)

Toutes les postions qui se trouvent dans le dictionnaire sont des cases interdites

Exemple de ce à quoi la logique doit arriver suite à l'appel de `set_ship('D3)`:

```python
_board = {}
# occupé par le bateau
_board['D3'] = [(3,3), "state-str", "part-objet_Part"] 
_board['E3'] = [(4,3), "state-str", "part-objet_Part"] 
_board['F3'] = [(5,3), "state-str", "part-objet_Part"] 
_board['G3'] = [(6,3), "state-str", "part-objet_Part"]
# autour du bateau
_board['C2'] = [(2,2)]
_board['D2'] = [(3,2)] 
_board['E2'] = [(4,2)] 
_board['F2'] = [(5,2)] 
_board['G2'] = [(6,2)]
_board['H2'] = [(7,2)]

_board['C3'] = [(2,3)]
_board['H3'] = [(7,3)]

_board['C4'] = [(2,4)]
_board['D4'] = [(3,4)] 
_board['E4'] = [(4,4)] 
_board['F4'] = [(5,4)] 
_board['G4'] = [(6,4)]
_board['H4'] = [(7,4)]

```

Types de valeurs dans le dico:

#### Occupée par ship

* Key $$ \Rightarrow $$  `str` ID case (`str(xy)` du bateau)
* Value $$ \Rightarrow $$  `Forbiden` objet contenant:
    * _pos $$ \Rightarrow $$ `tuple` x,y version int de l'ID de la case.
    * _tag $$ \Rightarrow $$ `str` etat à afficher (string du tag du ship)
    * _part $$ \Rightarrow $$ `Part` objet partie de bateau 
        * garde trace de l'etat du ship, interragit avec Ship en f(etat)

#### Interdite (toutes les cases autour du bateau)

* Key $$ \Rightarrow $$  `str` ID case
* Value $$ \Rightarrow $$  `list` liste
    * _pos $$ \Rightarrow $$ `tuple` x,y version int de l'ID de la case.
    * _tag $$ \Rightarrow $$ `None`
    * _part $$ \Rightarrow $$ None


### Cases interdides

La logique doit vérifier la viabilité d'un placement:

#### Empecher la supperposition de Ships

```python
if liste_coords_ship[i] in board._board.values[0]:
    break
    msg erreur
```

 1. En fonction de l'orientation du Ship et de sa taille, on crée dynamiquement une liste contenant les coordonnées des cases composant le Ship.
 2. On compare cette liste avec [ x for x in board._board.values[0]]. Je pense qu'il y a une built-in qui fait exactement ça.
 3. 2 cas:
    * Si aucune case du nouveau Ship à placer n'est dedans, on place le Ship
    * Dés qu'une case est commune aux deux listes on break et retourne un message d'erreur.

#### Empecher le placement hors-map

```python
for i in liste_coords_ship:
    if liste_coords_ship[i] > size-1 or liste_coords_ship[i] < 0 :
        break
        msg erreur
```

* <span style="color:red">Suggestion: toujours commencer par le point le plus éloigné du point initial du bateau !!  </span>



### Ships

* orientation:
    * 'h' $$ \Rightarrow $$ horizontal, de gauche à droite
    * 'v' $$ \Rightarrow $$ vertical, de haut en bas