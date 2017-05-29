---
layout: post
title: "Reseau: programmation de sockets (fr)"
subtitle: "en c et C++"
date: 2017-05-24
author: Sol
category: Reseau
tags: reseau sockets couche4 tcp udp Cpp fr
finished: false
---

# Programmation de sockets

## ressources:
[simple-client.c (base)](http://cvs.alphanet.ch/cgi-bin/cvsweb/~checkout~/schaefer/public/cours/HE-ARC/DECOUVERTE-OS-et-RESEAUX/script/code/client-TCP/donnee/RELEASES/simple-client.tar.gz?rev=HEAD;content-type=application%2Fx-gzip)  
[video explicative](http://fs.teleinf.labinfo.eiaj.ch/samba/scratch/RVO/compilation-c-socket.mp4)  
[tuto sockets](http://broux.developpez.com/articles/c/sockets/)   
[second tuto sockets](http://www.binarytides.com/socket-programming-c-linux-tutorial/)

# Linux

## 1. Création de socket

La première chose à faire est de créer un socket. C'est le rôle de la fonction `socket`.

```c
#include<stdio.h>
#include<sys/socket.h>

int main(int argc, char **argv) 
{
    int socket_desc;
    socket_desc = socket(AF_INET, SOCK_STREAM, 0);

    if (socket_desc == -1) 
    {
        printf("Could not create socket");
    }

    return 0;
}
```
La fonction `socket()` crée un socket et retourne un descripteur qui peut être utilisé dans d'autres fonctions. Le code précédent crée un socket avec les propriétés suivantes:

```
Address Family - AF_INET (ipv4)
Type - SOCK_STREAM (protocol TCP)
Protocol - 0 [ou IPPROTO_IP c'est le protocol IP]
```

> * type `SOCK_STREAM` désigne TCP  
> * type `SOCK_DGRAM` désigne UDP

## 2. Connecter un socket à un serveur

Pour se connecter à un serveur nous avons donc besoins de deux choses:

* adresse IP
* numéro de port

Pour se connecter à un serveur **distant** il nous faut également d'autres choses.
La première est de créer une structure `sockaddr_in` avec les bonnes valeures:

Voici à quoi ressemble cette structure:
```c
// IPv4 AF_INET sockets:
struct sockaddr_in {
    short            sin_family;   // AF_INET pour ipv4 et AF_INET6 pour ipv6
    unsigned short   sin_port;     // port
    struct in_addr   sin_addr;     // voir in_addr plus bas
    char             sin_zero[8];  // optionnel
};
 
struct in_addr {
    unsigned long s_addr;          // load with inet_pton()
};
 
struct sockaddr {
    unsigned short    sa_family;    // address family, AF_xxx
    char              sa_data[14];  // 14 bytes of protocol address
};
```
`sockaddr_in` possède un membre appelé `sin_addr` de type `in_addr` qui a un membre `s_adr` qui n'est rien d'autre qu'un `long`.

La fonction `inet_addr` est très pratique dans le sens où elle converti une adresse IP en un type long. Voici comment faire:

```c
server.sin_addr.s_addr = inet_addr("74.125.235.20"); // ip de google
```

Il est donc nécéssaire de connaitre l'adress IP du serveur distant qu'on veut connecter. 

La dernière chose dont on a besoin c'est de la fonction `connect` qui prend en paramètre un socket et la structure `sockaddr`.


```c
#include<stdio.h>
#include<sys/socket.h>
#include<arpa/inet.h> // inet_addr

int main(int argc, char **argv) 
{
    int socket_desc;
    // creation d'un objet de type sockaddr_in
    struct sockaddr_in server;

    // creation du socket -> socket_desc
    socket_desc = socket(AF_INET, SOCK_STREAM, 0);
    if (socket_desc == -1) 
    {
        printf("Could not create socket");
    }

    // initialisation des membres de l'objet server
    server.sin_addr.s_addr = inet_addr("172.217.22.78");
    server.sin_family = AF_INET;
    server.sin_port = htons(80);

    // connexion a un serveur distant
    if (connect(socket_desc, (struct sockaddr * )&server, sizeof(server)) < 0)
    {
        puts("connect error");
        return 1;
    }

    puts("Connected");
    return 0;
}
```

<span style="color:#F92672">**Les connexions ne sont nécéssaires uniquement dans le cas du l'utilisation de TCP**</span>

Le concepte de connexion ne s'applique que dans le cas de l'utilisation de SOCK\_**STREAM** (socket `TCP`). Une connexion veut dire "reliable _stream_ of data" de telle façon qu'on puisse avoir plusieurs de ces "stream" qui effectue chacun une communication. 

> think of this as a pipe which is not interfered by other data.

D'autres sockets comme UDP, ICMP ou ARP par exemple n'ont pas besoin de se connecter. Se sont des protocoles non-orienté connexion.

## 3. Envoyer des données

La fonction `send` nous permet d'envoyer des données. Elle a besoin en paramètre du descripteur de socket, la donnée à envoyer et sa taille.

```c
#include<stdio.h>
#include<string.h>
#include<sys/socket.h>
#include<arpa/inet.h> // inet_addr

int main(int argc, char **argv) 
{
    int socket_desc;
    char* message;
    // creation d'un objet de type sockaddr_in
    struct sockaddr_in server;

    // creation du socket -> socket_desc
    socket_desc = socket(AF_INET, SOCK_STREAM, 0);
    if (socket_desc == -1) 
    {
        printf("Could not create socket");
    }

    // initialisation des membres de l'objet server
    server.sin_addr.s_addr = inet_addr("172.217.22.78");
    server.sin_family = AF_INET;
    server.sin_port = htons(80);

    // connexion a un serveur distant
    if (connect(socket_desc, (struct sockaddr * )&server, sizeof(server)) < 0)
    {
        puts("connect error\n");
        return 1;
    }

    puts("Connected\n");

    // envoie de donnees 
    message = "GET / HTTP/1.1\r\n\r\n";
    if (send(socket_desc, message, strlen(message), 0) < 0)
    {
        puts("Send failed\n");
        return 1;
    }

    puts("Data Send\n");
    return 0;
}
```
 Dans cette exemple, dans un premier temps nous nous connectons à l'adresse IP et ensuite envoyons notre chaîne de caractères. Ce message est une commande HTTP pour fetch la mainpage d'un site web.

 [<a href="/00illustrations/sockets/wireshark1.png"><img src="/00illustrations/sockets/wireshark1.png"></a>]()


 Maintenant que nous avons envoyé des données, il faut faire en sorte de recevoir une réponse du serveur.

 > Quand on envoie des données à un socket, de façon similaire à écrire dans un fichier, nous écrivons dans le socket, par exemple nous pouvons utiliser la fonction `write` pour envoyer des données à un socket.

## 4. Recevoir des données sur un socket.

La fonction `recv` est utilisée pour recevoir des données sur un socket. Dans le prochain exemple, nous allons envoyer le même message quand dans le précédent exemple et recevoir une réponse du serveur.


















<!--
## 1. Introduction

Un socket est une prise qui permet à une application d'envoyer et de recevoir des données.

Une connexion TCP est identifiée par 4 valeurs (adresse IP source et destination, ports source et destination). Un socket est identifié par une valeur spécifique au système d'exploitation.

## 2. Nouvelles notions et rappels
### int main
int main(int _argc_, char _argv)_

```sh
sol@debian:~/test$ ./programme arg1 arg2
```

`argv[idx]` nous sert à récuperer les arguments dans le code.
* argc: nombre d'arguments + 1 (nom programme)
* argv[0]: nom du programme
* argv[1]: argument 1
* argv[2]: argument 2

### fprintf
Permet de choisir le flux de sortie.

### perror
perror("message")  
renvoie `message + : dernière erreur`

```c
#include <stdio.h>
#include <stdlib.h>

void test(char* a, char* b)
{
    printf("arg1: %s\narg2: %s\n", a,b);
}

int main(int argc, char **argv)
{
    perror("etat");
    fprintf(stderr,"test: %d\nnom de la fonction: %s\n",argc, argv[0]);
    test(argv[1], argv[2]);
    return 0;
}
```

```sh
sol@debian:~/LABOS/labo5/04CliendTCP/test$ ./test poule cochon
etat: Succes
n: 3
nom de la fonction: ./test
arg1: poule
arg2: cochon
```

## socket
```c
int socket (int domaine, int mode, int protocole) 
```
Elle permet de créer une socket de type quelconque : 
* Le paramètre domaine précise la famille de protocoles è utiliser pour les communications. Il y a deux familles principales : 
    * `PF UNIX` pour les communications locales n’utilisant pas le réseau; Communication entre processus sur une même machine 
    * `AF INET` pour les communications réseaux utilisant le protocole IPv4. 
    * `AF INET6` pour les communications réseaux utilisant le protocole IPv6. Nous n’étudierons par la suite que la famille AF INET. 

* Le paramètre mode précise le type de la socket è créer : 
    * `SOCK STREAM` pour une communication bidirectionnelle sûre. Dans ce cas, la socket utilisera le protocole TCP pour communiquer. 
    * `SOCK DGRAM` pour une communication sous forme de datagrammes. Dans ce cas, la socket utilise le protocole UDP pour communiquer. 
    * `SOCK RAW` pour pouvoir créer soi-même ses propres paquets IP ou les recevoir sans traitement de la part de la couche Transport. 
* Le paramètre protocole précise le protocole 
utilisé (dépend bien sûr du champ mode) : 
    * `IPPROTO UDP` 
    * `IPPROTO TCP` 
    * `IPPROTO ICMP` 
    * `IPPROTO RAW` La valeur renvoyée par la fonction socket() est -1 en cas d’erreur, sinon c’est un descripteur de ﬁchier qu’on peut par la suite utiliser avec les autres fonctions de la librairie des Sockets

## structure sockaddr
Dans le cas des communications IP, le pointeur vers la structure de type `sockaddr` est en fait un pointeur vers une structure de **type** sockaddr\_in pour IPV4 ou sockaddr\_in6 pour IPV6. Ces structures sont déﬁnies dans le ﬁchier d’en-tˆete **<netinet/in.h>** (d’où la **conversion de pointeur**).

```c
struct sockaddr_in 
{ 
    short sin_family;
    u_short sin_port;
    struct in_addr sin_addr;
    char sin_zero[8];
    };
struct sockaddr_in6 
{ 
    sa_family_t sin6_family; // AF_INET6
    in_port_t sin6_port;     // port number
    uint32_t sin6_flowinfo;  // IPv6 flow information

struct in6_addr sin6_addr; // IPv6 address  
{
    uint32_t sin6_scope_id; // Scope ID (new in 2.4) 
};

struct in6_addr { 
    unsigned char s6_addr[16]; // IPv6 address 
};
```

La structure sockaddr_in contient trois champ utiles : 
* `short sin_family` est la famille de protocoles utilisée, AF_INET pour IPv4; 
* `unsigned short sin_port` est le port à aﬀecter à la structure; (0 pour une allocation dynamique cˆoté client) 
* `unsigned long sin addr.s_addr` est l’adresse IP à aﬀecter à la structure. 
La fonction **getaddrinfo()** permet de remplir ces structures de façon simple que ce soit avec des adresses IPV4 ou IPV6.

> Le type struct sockaddr n’est plus réellementutilisé: en fonction de la famille de socket utilisée(IPv4, IPv6, UNIX domainsocket,...), des structures spéciﬁques sont utilisées en interne. Par exemple la structure `struct sockaddr_in`.
>
>Aujourd’hui, il ne faudrait plus yaccéder directement. C’est justement le but des fonctions **getaddrinfo(3)** et **getnameinfo(3)**


<span style="color:#F92672"> **Pourquoi (3) ?** </span>

## getaddrinfo
```c
int getaddrinfo(const char *node, 
                const char *service, 
                const struct addrinfo *hints, 
                struct addrinfo **res);
```

**getaddrinfo**(*hostname, *port,... **a completer**)
permet de trouver l’adresse ou les adresses IP (IPV4  et  IPV6)  associées  à  un  nom  soit  **DNS**,  soit  donné dans le fichier */etc/hosts** de  la **machine**.

Elle retourne une liste de structure `addrinfo` dans lesquelles on retrouve un pointeur sur la sructure `sockaddr` qui peut ensuite être utilisée pour les appels aux fonctions **bind**, **connect**

La structure **sockaddr** contient après l’appel une des adresses IP de node et le numéro de port donné par service (**numéro de port ou nom associé dans /etc/services**). La structure pointée par hints peut être remplie avant l’appel pour ﬁxer la valeur de certains champs (par exemple le type de la socket dans `hints->ai socktype`).

```c
struct addrinfo { 
    int ai_flags;
    int ai_family;
    int ai_socktype;
    int ai_protocol;
    size_t ai_addrlen;
    struct sockaddr *ai_addr;
    char *ai_canonname;
    struct addrinfo *ai_next;
    };
```

Les structures pointées par *res(ipv4 ou ipv6) sont allouées par la fonction. On peut les désalouer à l’aide de:

```c
void freeaddrinfo(struct addrinfo *res); 
```



 **completer:** gai...

```c
const char *gai strerror(int errcode);
```

#### exemple d'utilisation:

```c
void *addr;
int status;
char ipstr[INET6_ADDRSTRLEN], ipver;

memset(&hints, 0, sizeof hints);
hints.ai_family = AF_UNSPEC // ipv4 ou ipv6
hints.ai_socktype = SOCK_STREAM // socket TCP

status = getaddrinfo("5000", "domaine.com", &hints, &res);

while(res !- NULL)
{
    // identification de l'adresse courante
    if (res->ai_family == AF_iNET) // ipv4
    {                                       // cast
        struct sockaddr_in *ipv4 = (struct sockaddr_in * )res->ai_addr;
        addr =&(ipv4->sin_addr);
        ipver = '4';
    }
    else // ipv6
    {
        struct sockaddr_in6 *ipv6 = (struct sockaddr_in6 * )res->ai_addr;
        addr = &(ipv6->sin6_addr);
        ipver = '6';
    }

    // conversion de l'adresse IP en str
    inet_ntop(res->ai_family, addr, ipstr, sizeof ipstr)
    printf("IPv%c: %s\n", ipver, ipstr);

    // Adresse suivante
    res = res->ai_next;
}

// liberation de la memoire occupee par les enregistrements
freeaddrinfo(res);
```
 -->
