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


![alt](http://www.nomnoml.com/#view/%23font%3A%20operator%20mono%20light%0A%23fontSize%3A%2014%0A%23spacing%3A%2050%0A%23padding%3A%2010%0A%0A%0A%5B%3Cabstract%3EShip%5D%2B-..*%5BPart%5D%0A%5BCruiser%5D-%3A%3E%5BShip%5D%0A%5BBoard%5Do-5%5BShip%5D%0A%5BPlayer%5D%3C-2%5BBoard%5D%0A%5BPlayer%5D--%3E%5BBoard%5D%0A%5BComputer%5D-%3A%3E%5BPlayer%5D%0A%5BComputer%5D-%3E%5BAutoSet%5D%0A%0A%5B%3Cabstract%3EShipsSetup%5D%3C-%5BPlayer%5D%0A%5BShipsSetup%5D%3C-%5BShip%5D%0A%5BShipsSetup%5D-%3E%5BBoard%5D%0A%0A%5BAutoSet%5D-%3A%3E%5BShipsSetup%5D%0A%5BManualSet%5D-%3A%3E%5BShipsSetup%5D%0A%0A%5BAim%5D%3C--%5BManualSet%5D%0A%5BAim%5D%3C--%5BPlayer%5D%0A%0A%0A%0A%0A%0A%5BCruiser%7CSIZE%3A%20int%3B%20TAG%3A%20str%5D%0A%0A%5BPart%0A%09%7Cx%3A%20int%3B%20y%3A%20int%3B%20state%3A%20str%0A%09%7Cget_repr()%3A%20str%0A%5D%0A%0A%5BShip%0A%09%7Cparts%3A%20Part%5C%5BPart%5C%5D%3B%20orien%3A%20str%3B%20life%3A%20dict%0A%20%20%20%20%7Cbuild_parts()%3B%20push(Board)%3B%20set_base(Part)%0A%5D%0A%0A%5BBoard%0A%09%7Csize%3A%20int%3B%20grid%3A%20dict%0A%20%20%20%20%7Cgrid_generator()%3A%20generator%3B%20%0A%20%20%20%20%09display()%3A%20str%3B%20display_data()%3A%20str%0A%5D%0A%0A%5BPlayer%0A%09%7Cname%3A%20str%3B%20ship_grid%3A%20Board%3B%0A%20%20%20%20%09target_grid%3A%20Board%3B%20ships%3A%20%7BShip*5%7D%0A%20%20%20%20%7Cauto_set()%3B%20manual_set()%3B%20targetting()%0A%5D%0A%0A%0A)


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