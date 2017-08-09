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

## Liens utiles

*[interne: Composition d'objets](/prog/composition-aggregation.html)
*[Battleship game: rules](https://www.thespruce.com/the-basic-rules-of-battleship-411069)

## Rêgles

* 2 Joueurs
* 2 Grilles par joueur
    * Une contient ses propres navires
    * L'autre contient un historique des coups faits par le joueur
* Grille de 10x10
* 5 navires par joueur
    * Carrier (5)
    * Battleship (4)
    * Cruiser (3)
    * Submarine (3)
    * Destroyer (2)


* Placements des navires
    * Horizontal
    * Vertical
* Les navires peuvent se toucher mais pas se chevaucher

### Advanced gameplay: Salvo variation

* On the first round of the game, you call out five shots (guesses) and mark each shot with a white peg in your target grid.
* After you've called out all five shots (a salvo), your opponent announces which ones were hits and which ships they hit.
* For hits, change the white pegs on your target grid to red pegs. Your opponent places red pegs in the holes of any ships that you hit.
* Continue in this manner until one of your ships is sunk. At that point, you lose one shot from your salvo. If one of your ships sinks, your salvo is reduced to four shots. When two ships sink, the salvo is three shots, and so on.
* Continue game play until one player sinks all the opposing ships and wins the game.

<img src="/00illustrations/battleShips/battleShipsULM.png" height="auto">

```
#font: operator mono light
#fontSize: 14
#spacing: 50
#padding: 10


[<abstract>Ship]+-..*[Part]
[Cruiser]-:>[Ship]
[Board]o-5[Ship]
[Player]<-2[Board]
[Player]-->[Board]
[Computer]-:>[Player]
[Computer]->[AutoSet]

[<abstract>ShipsSetup]<-[Player]
[ShipsSetup]<-[Ship]
[ShipsSetup]->[Board]

[AutoSet]-:>[ShipsSetup]
[ManualSet]-:>[ShipsSetup]

[Aim]<--[ManualSet]
[Aim]<--[Player]


[Cruiser|SIZE: int; TAG: str]

[Part
	|x: int; y: int; state: str
	|get_repr(): str
]

[Ship
	|parts: Part\[Part\]; orien: str; life: dict
    |build_parts(); push(Board); set_base(Part)
]

[Board
	|size: int; grid: dict
    |grid_generator(): generator; 
    	display(): str; display_data(): str
]

[Player
	|name: str; ship_grid: Board;
    	target_grid: Board; ships: {Ship*5}
    |auto_set(); manual_set(); targetting()
]
```