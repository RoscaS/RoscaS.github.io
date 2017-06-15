---
layout: post
title: "Reseau: Programmation sockets (théorie)"
subtitle: "Linux, langage C"
date: 2017-06-14
author: Sol
category: Reseau
tags: reseau sockets smtp tcp udp c fr
finished: false
---

# Programmation socket en C sous Linux

## 1. Qu'est-ce qu'un socket?

Un socket autorise la communication entre deux process différents.
* Sur la même machine
* Entre des machines distantes

Sur Unix, toutes les actions I/O sont faites en écrivant ou en lisant un **descripteur de fichier**. 

> L'entrée standard STDIN, la sortie standard STDOUT et l'erreur standard STDERR sont les trois descripteurs de fichier POSIX standard pour tout processus qui n'est pas un _daemon_.  
[wikipedia: descripteur de fichiers](https://fr.wikipedia.org/wiki/Descripteur_de_fichier)

Un **descripteur de fichier** est un entier `int` associé avec un **fichier ouvert**. Ce fichier peut être:
* Une connexion réseau
* Un fichier texte
* Un terminal
* ...

D'un point de vue programmatique, un socket ressemble et se comporte comme un descripteur de fichier. On utilise sur lui des fonctions comme `read()` et `write()` qui fonctionnent de la même façon avec un socket que sur des fichiers ou sur des pipes.

## 2. Contexte d'utilisation

Un socket Unix est utilisé dans un contexte **client-serveur**.
Un **Serveur** est un process qui effectue des actions (fonctions) à la demande d'un **client**. La plupart des protocoles de la couche application (FTP, SMTP, POP3...) utilisent des sockets pour:

1. établir une connexion entre client et serveur
2. échager des donnnées

## 3. Types de sockets

types de sockets sont disponible pour les utilisateurs. 

Stream Sockets (**TCP**)
* fiables
* orienté connexion
* pas de limite de taille
* message d'erreur en cas d'échec de l'envoi

Datagram Sockets (**UDP**)
* non-fiable
* non-orienté connexion (pas de phase connexion comme en TCP,  construction d'un paquet avec les coordonnées de la destination et puis envoi)

Raw Sockets
* utilisés dans le developpement de protocoles de communication ...
* ... et le [hacking](https://fr.wikipedia.org/wiki/Berkeley_sockets)


Les process sont théoriquement censés communiquer uniquement entre sockets de même type mais il n'y a aucune restriction qui empêche la communication entre sockets de type différent.

## 4. Modèle "Client Server"
La majorité des applications d'Internet utilisent ce Modèle il se réfère à:
Deux process ou deux applications qui communiquent en échangeant des informations. L'un des deux process agit comme un client et l'autre comme un serveur.

### Processus Client
Typiquement celui qui fait  la requête d'informations. Après avoir reçu la résponse, ce process soit se termine soit continue à faire des traitements.

**exemple**: Un navigateur internet est une application **client** qui envoie une requête à un serveur Web pour recevoir une page HTML.

### Processus Serveur
C'est le process qui reçoit les requêtes de la part des clients. Après avoir reçu une requête d'un client, ce process va effectuer le traitement demandé (rassembler des informations, et les renvoyer au client). Une fois ces taches éffectuées le process est disponible pour servir un autre client. **Les process serveurs sont toujours prêts à servir les requêtes qui leurs parviennent**.

**exemple**: Un serveur web est toujours entrain d'attendre des requêtes de navigateurs internet. Aussi tôt qu'un client (navigateur) lui fait parvenir une requête, il lui renvoie la page demandée.

> Un client a besoin de connaitre l'adresse d'un serveur mais pas l'inverse. 

### Architectures T2 et T3

* **T2**: le client interagit directement avec le serveur. Ce type d'architecture peut avoir des problèmes de sécurité (gérés grâce au SSL: Secure Socket Layer) et des soucis de performance.

* **T3**: Un logiciel appelé **Middlewarese** placé entre client et serveur et est utilisé pour effectuer des tests de sécurité.

### Types de serveurs

* Serveurs **itératifs**: forme la plus simple. Le serveur gère un client à la fois et les clients attendent pour s'y connecter.

* Serveurs **concurents (Concurrent(en))**: gènre plusieurs processus en parallèle et permet de servire plusieurs requêtes à la fois. La forme la plus simple pour implémenter un serveur concurent sous Unix est de **FORKER** un processus enfant pour prendre en charge chaque client.

### Interaction entre client et serveur
Ce schéma décrit l'intéraction complète entre un client et un serveur.

<img src="/00illustrations/socket2/diag1.png" height="auto">

## 5. Unix Socket - Structures

Plusieurs structures sont utilisées pour la programmations socket sous Unix. Celles-ci contiennent des infomations concernant entre autre l'adresse et le port. La plupart des fonctions socket nécéssitent un pointeur sur l'adresse d'une structure comme argument.

### sockaddr
Contient des informations sur le socket

```c
struct sockaddr {
   unsigned short   sa_family;
   char             sa_data[14];
};
```
Structure d'adresse générique qui sera passé dans la majorité des appels de fonction. 

**description des membres:**

| attribut | valeur | description |  
|:--:|:--:|:--:|  
| sa_family | AF\_INET<br>AF\_UNIX<br>AF\_NS<br>AF\_IMPLIK | représente une famille d'adresses (ipv4/6). On utilise généralement AF\_INET (protocole IP)|  
| sa\_data | Protocol-specific Adress | les 14 bytes de ce champ sont interprété en fonction du type d'adresse. |  




### sockaddr_in

```c
struct sockaddr_in {
   short int            sin_family;
   unsigned short int   sin_port;
   struct in_addr       sin_addr;
   unsigned char        sin_zero[8];
};
```
Permet de référencer (pointer) les divers éléments du socket

**description des membres:**

| attribut | valeur | description |  
|:--:|:--:|:--:|  
| sa_family | AF\_INET<br>AF\_UNIX<br>AF\_NS<br>AF\_IMPLIK | représente une famille d'adresses (ipv4/6). On utilise généralement AF\_INET (protocole IP)|
| sin_port | Service Port | numéro de port sur 16 bits (Network Byte Order) |  
| sin_addr | IP Address | adresse IP sur 32 bits (Network Byte Order) |  
| sin_zero | ne s'utilise pas/plus | valeur à set sur NULL |  



### in_addr

```c
struct in_addr {
   unsigned long s_addr;
};
```
Cette structure est uniquement utilisée dans la structure précédente et contient le netid/hostid sur 32 bits

**description des membres:**

| attribut | valeur | description |  
|:--:|:--:|:--:|  
| s_addr | service port | adresse IP sur 32 bits (Network Byte Order) |  



### hostent

```c
struct hostent {
   char  *h_name; 
   char **h_aliases; 
   int    h_addrtype;  
   int    h_length;    
   char **h_addr_list
	
// define h_addr  h_addr_list[0]
};
```
Contient des informations sur l'hôte

**description des membres:**

| attribut | valeur | description |  
|:--:|:--:|:--:|  
| h_name | url | nom de domaine de l'hôte |  
| h_aliases | TI | liste d'alias de l'hôte |  
| h_addrtype | AF\_INET | représente une famille d'adresses (ipv4/6). On utilise généralement AF\_INET (protocole IP)  |  
| h_lenght | 4 | longeur de l'ip (byte). 4 pour IP |  
| h_addr_list | in_addr | liste de pointeurs (pour le protocol IP) |  

_note: h\_addr est définis comme étant h\_addr\_list[0] pour assurer une retro compatibilité_


### servent

```c
struct servent {
   char   *s_name; 
   char  **s_aliases; 
   int     s_port;  
   char   *s_proto;
};
```
Contient des informations sur les service et les ports associés

**description des membres:**

| attribut | valeur | description |  
|:--:|:--:|:--:|  
| s_name | http | nom officiel du serveur. (SMTP, FTP, POP3,...) |    
| s_aliases  | ALIAS | liste d'alias du service. Le plus souvent sera set à NULL |  
| s_port | 80 | numéro de port associé (ex. Si HTTP => 80) |  
| s_proto | TCP<br>UDP | protocole utilisé |  


### Conseils d'utilisation

* Ces structures sont au coeur de la programmation réseau. Nous les initialisons, remplissons et leurs passons des pointeurs qui pointent vers diverses fonctions de sockets. Parfois nous passons un pointeur à l'une de ces structure ce qui à pour effet de compléter les données de la structure.

* Toujours passer ces structures par référence (pointeur) accompagné de la taill de cette dernière.

* Quand une fonction socket remplit une de ces structures, nous passons également la longueure de la strucure par référence pour que sa valeur puisse être modifié par la fonction.


## 6. Ports et services (fonctions)
Unix nous fournit les fonctions suivantes pour fetch un nom de service du répertoire /etc/services
```c
struct servent *getservbyname(char *name, char *proto)
```
Cet appel prend le nom du service et le nom du protocole et retourne le **numéro de port** correspondant au service.

```c
struct servent *getservbyport(int port, char *proto)
```
Cet appel prend le port et le nom du protocole et retourne le **nom** du service correspondant.

La valeur de retour des deux fonctions est un ponteur sur la structure `servent` vue un peu plus haut.

## 7. Ordre des bytes (représentation des bytes en mémoire)

### endiannes

> Ordre dans lequel les octets sont organisés dans une case mémoire **ou dans une communication**. Big endian et Little endian sont deux architectures différentes.

#### Big endian
**byte de poids fort** à gauche.  
Rangement en mémoire de la valeur `0xA0B70708` dans une structure mémoire de cases de 1 byte

|adr:| 0 | 1 | 2 | 3 |  |
| :--: | :--: | :--: | :--: | :--: | :--: |
|**val:**| A0 | B7 | 07 | 08 | ... |

Rangement en mémoire de la valeur `0xA0B70708` dans une structure mémoire de cases de 2 byte:  

| 0 | 1 | | 2 | 3 | |
| :--: | :--: | :--: | :--: | :--: | :--: |
| A0 | B7 | | 07 | 08 | ... |


<span style="color:#F92672">**Tous les protocoles TCP/IP communiquent en big-endian**</span>


#### Little endian
**byte de poid faible** à gauche.   
Rangement en mémoire de la valeur `0xA0B70708` dans une structure mémoire de cases de 1 byte

|adr:|  0 | 1 | 2 | 3 |  |
| :--: | :--: | :--: | :--: | :--: | :--: | :--: |
|**val:**| 08 | 07 | B7 | A0 | ... |

Rangement en mémoire de la valeur `0xA0B70708` dans une structure mémoire de cases de 2 byte:  

| 0 | 1 | | 2 | 3 | |
| :--: | :--: | :--: | :--: | :--: | :--: |
| 07 | 08 | | A0 | B7 | ... |

<span style="color:#F92672">**X86 fonctionne en Little endian**</span>

### Fonctions pour changer l'ordre des bytes

| attribut | description |    
|:--:|:--:|    
| htons() | host to Network Short |  
| htonl() | host to Network Long |  
| ntohl() | Network to Host Long |  
| ntohs() | Network to Host Short |  

```c
unsigned short htons(unsigned short hostshort) 
```
Cette fonction converti 16-bit (2-byte) de **host byte order** vers network byte order.

```c
unsigned long htonl(unsigned long hostlong) 
```
Cette fonction converti 32-bit (4-byte) de **host byte order** vers network byte order.

```c
unsigned short ntohs(unsigned short netshort) 
```
Cette fonction converti 16-bit (2-byte) de network byte order vers **host byte order**.

```c
unsigned long ntohl(unsigned long netlong) 
```
Cette fonction converti 32-bit de network byte order vers **host byte order**.

### Fonction pour déterminter l'endiannes d'une machine


```c
#include <stdio.h>

int main(int argc, char **argv) {

   union {
      short s;
      char c[sizeof(short)];
   }un;
	
   un.s = 0x0102;
   
   if (sizeof(short) == 2) {
      if (un.c[0] == 1 && un.c[1] == 2)
         printf("big-endian\n");
      
      else if (un.c[0] == 2 && un.c[1] == 1)
         printf("little-endian\n");
      
      else
         printf("unknown\n");
   }
   else {
      printf("sizeof(short) = %d\n", sizeof(short));
   }
	
   exit(0);
}
```
output sur ma machine (core I7)

```sh
$> gcc byteorder.c
$> ./a.out
little-endian
$>
```

## 8. Fonctions pour manipuler les adresses IP

Ces fonctions convertissent des IP entre le format ASCII string (lecture par un humain) et le format Network Byte Order (valeurs binaires contenues dans les structures vues plus haut).

```c
int inet_aton(const char *strptr, struct in_addr *addrptr)
```

* Converti la string spécifiée (au format _Internet standard dot notation_) en format Network Adress 
* Range l'adresse dans la structure spécifiée. 
* Le format de l'adresse convertie est Network Bye Order (bytes de gauches à droite). 
* Retourne 1 si la string était valide et 0 si une erreur survient.

**Exemple d'utilisation:**

```c
#include <arpa/inet.h>
(...)

   int retval;
   struct in_addr addrptr
   
   memset(&addrptr, '\0', sizeof(addrptr));
   retval = inet_aton("68.178.157.132", &addrptr);

(...)
```

```c
in_addr_t inet_addr(const char *strptr)
```
* Converti la string spécifiée (au format _Internet standard dot notation_) en format entier `int` modifié
* Le format de l'adresse convertie est Network Bye Order (bytes de gauches à droite). 
* Retourne une adresse IPv4 au format binaire sur 32 bits et une erreur de type INADDR_NONE

**Exemple d'utilisation:**

```c
#include <arpa/inet.h>

(...)

   struct sockaddr_in dest;

   memset(&dest, '\0', sizeof(dest));
   dest.sin_addr.s_addr = inet_addr("68.178.157.132");
   
(...)
```

```c
char *inet_ntoa(struct in_addr inaddr)
```
* Converti l'adresse de l'hôte spécifié en une string dans le format _Internet standard dot notation_.

## Fonctions principales 