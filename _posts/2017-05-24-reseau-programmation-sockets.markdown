---
layout: post
title: "Reseau: Programmation sockets (tuto)"
subtitle: "tuto pratique"
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
[addrinfo](https://www.inetdoc.net/dev/socket-c-4and6/socket-c-4and6.getaddrinfo.html)  
[manPage addrinfo](http://manpagesfr.free.fr/man/man3/getaddrinfo.3.html)  
[fameux Beej's guide to network programming](http://beej.us/guide/bgnet/)  
[Le même mais traduit en français](http://vidalc.chez.com/lf/socket.html)    
[tutorialsPoint](https://www.tutorialspoint.com/unix_sockets/index.htm)  
[best](https://www.tutorialspoint.com/unix_sockets/socket_summary.htm)  


# Qu'est-ce qu'un socket?

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

# 2. Contexte d'utilisation

Un socket Unix est utilisé dans un contexte **client-serveur**.
Un **Serveur** est un process qui effectue des actions (fonctions) à la demande d'un **client**. La plupart des protocoles de la couche application (FTP, SMTP, POP3...) utilisent des sockets pour:

1. établir une connexion entre client et serveur
2. échager des donnnées

# 3. Types de sockets

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

# 4. Modèle "Client Server"
La majorité des applications d'Internet utilisent ce Modèle il se réfère à:
Deux process ou deux applications qui communiquent en échangeant des informations. L'un des deux process agit comme un client et l'autre comme un serveur.

### Processus Client
Typiquement celui qui fait  la requête d'informations. Après avoir reçu la résponse, ce process soit se termine soit continue à faire des traitements.

**exemple**: Un navigateur internet est une application **client** qui envoie une requête à un serveur Web pour recevoir une page HTML.

### Processus Serveur
C'est le process qui reçoit les requêtes de la part des clients. Après avoir reçu une requête d'un client, ce process va effectuer le traitement demandé (rassembler des informations, et les renvoyer au client). Une fois ces taches éffectuées le process est disponible pour servir un autre client. **Les process serveurs sont toujours prêts à servir les requêtes qui leurs parviennent**.

**exemple**: Un serveur web est toujours entrain d'attendre des requêtes de navigateurs internet. Aussi tôt qu'un client (navigateur) lui fait parvenir une requête, il lui renvoie la page demandée.

> Un client a besoin de connaitre l'adresse d'un serveur mais pas l'inverse. 

### Types de serveurs

* Serveurs **itératifs**: forme la plus simple. Le serveur gère un client à la fois et les clients attendent pour s'y connecter.

* Serveurs **concurents (Concurrent(en))**: gènre plusieurs processus en parallèle et permet de servire plusieurs requêtes à la fois. La forme la plus simple pour implémenter un serveur concurent sous Unix est de **FORKER** un processus enfant pour prendre en charge chaque client.

### Interaction entre client et serveur
Ce schéma décrit l'intéraction complète entre un client et un serveur.

<img src="/00illustrations/socket2/diag1.png" height="auto">

# 5. Client HTTP


### a. Création de socket

La première chose à faire est de créer un socket. C'est le rôle de la fonction `socket`.

```c
#include<stdio.h>
#include<sys/socket.h>

int main(int argc, char **argv) 
{
    // variables
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

### b. Connecter un socket à un serveur

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
    // variables
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

### c. Envoyer des données

La fonction `send` nous permet d'envoyer des données. Elle a besoin en paramètre du descripteur de socket, la donnée à envoyer et sa taille.

```c
#include<stdio.h>
#include<string.h>
#include<sys/socket.h>
#include<arpa/inet.h> // inet_addr

int main(int argc, char **argv) 
{
    // variables
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

### d. Recevoir des données sur un socket.

La fonction `recv` est utilisée pour recevoir des données sur un socket. Dans le prochain exemple, nous allons envoyer le même message quand dans le précédent exemple et recevoir une réponse du serveur.


```c
#include<stdio.h>
#include<string.h>
#include<sys/socket.h>
#include<arpa/inet.h> // inet_addr
#include<unistd.h> // close

int main(int argc, char **argv) 
{
    // variables
    int socket_desc;
    char* message;
    char server_reply[2000];
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

    // recevoir une reponse du serveur
    if (recv(socket_desc, server_reply, 2000, 0) < 0)
    {
        puts("recv failed");
    }

    puts("Reply received\n");
    puts(server_reply);

    puts("Data Send\n");

    close(socket_desc)
    return 0;
}
```
output:
```
Connected

Reply received

HTTP/1.1 302 Found
Cache-Control: private
Content-Type: text/html; charset=UTF-8
Referrer-Policy: no-referrer
Location: http://www.google.ch/?gfe_rd=cr&ei=9oMsWbfPHJOT8QfFgK2IBg
Content-Length: 258
Date: Mon, 29 May 2017 20:26:30 GMT

<HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">
<TITLE>302 Moved</TITLE></HEAD><BODY>
<H1>302 Moved</H1>
The document has moved
<A HREF="http://www.google.ch/?gfe_rd=cr&amp;ei=9oMsWbfPHJOT8QfFgK2IBg">here</A>.
</BODY></HTML>

Data Send
```

Nous voyons ici la réponse de google qui nous envoie le contenu de la page demandé.

> Quand nous recevons des données sur un socket, nous lisons les données dans le socket comme nous le ferions pour un simple fichier. Nous pourions utiliser la fonction `read` pour lire des données sur un socket.  
`read(socket_desc, server_reply, 2000);`

Maintenant que nous avons reçu notre réponse, nous pouvons fermer le socket (_fichier_).


### e. Fermeture de socket

Je ne l'ai pas dit dans le précédent exemple mais j'ai également ajouter la fonction `close` qui vit dans le header `<unistd.h>` a la fin de notre main.

```c
close(socket_desc)
```

Voici une capture wireshark de l'échange:

[<a href="/00illustrations/sockets/wireshark2.png"><img src="/00illustrations/sockets/wireshark2.png"></a>]()


### f. Récapitulatif

1. Création d'un socket
2. Connexion à un serveur distant
3. Envoie de données
4. Réception de données
5. Fermeture du socket

C'est exactement ce que fait notre navigateur quand nous ouvrons la page www.google.com
Ce genre d'utilisation d'un socket est appelé **socket client**. 

> Un client est une application qui se connecte sur un serveur pour y fetcher des données.

L'autre genre d'application socket sont donc naturellement les **sockets serveurs**. Un serveur est un système qui utilise des sockets pour recevoir des connexions et fournir des données par ce biais.

Dans notre exemple www.google.com est un serveur HTTP et notre navigateur est client HTTP.

Avant de faire notre propre serveur, nous allons passer par quelques explications d'ordre général sur la programmation de sockets.


# 6. Trouver l'ip d'un domaine

Quand on se connecte à un hôte distant, il est nécéssaire d'avoir son adresse IP. C'est là que la fonction `gethostbyname` nous est utile. Elle prend un nom de domaine comme paramètre et retourne une structure de type `hostent` et contient l'IP que l'on cherche. `gethostbynam` vit dans le header `<netdb.h>`.

Voici cette structure:

```c
struct hostent
{
  char *h_name;         // Official name of host.
  char **h_aliases;     // Alias list.  
  int h_addrtype;       // Host address type.  
  int h_length;         // Length of address.  
  char **h_addr_list;   // List of addresses from name server.
};
```
h\_addr\_list contient les adresses pour joindre notre hôte.

```c
#include<stdio.h> //printf
#include<string.h> //strcpy
#include<sys/socket.h>
#include<netdb.h> //hostent
#include<arpa/inet.h>

int main(int argc, char **argv)
{
    char *hostname = "www.google.com";
    char ip[16];
    struct hostent *he;
    struct in_addr **addr_list;

    if ((he = gethostbyname(hostname)) == NULL)
    {
        herror("gethostbyname");
        return 1;
    }

    addr_list = (struct in_addr ** )he->h_addr_list;

    strcpy(ip, inet_ntoa(*addr_list[0]));
    
    printf("%s resolved to : %s\n", hostname, ip);

    return 0;
}
```

output:
```
www.google.com resolved to : 216.58.205.164
```

Cette fonction très pratique nous permet de résoudre un nom de domaine en adresse IP et ensuite nous pouvons utiliser l'adresse IP pour créér une connexion à l'aide d'un Socket.

La fonction `inet_ntoa` converti une adresse IP stoquée dans un long (type) en notation à point.

### Récapitulatif des structures

Nous avons vu quelques structure importantes qui reviennent tout le temps dans le domaine de la progrmtion de sockets:

1. `sockaddr_in` : information de connexion principalement utilisées par les fonctions `connect`, `send`, `recv`
2. `in_addr` : adresse IP au format long (type)
3. sockaddr : informations sur le socket
4. `hostent` : l'IP de l'hôte utilisé par la fonction `gethostbyname`

## 7. Serveur HTTP

### a. cycle de vie

Passons à un serveur. Les **sockets serveur** fonctionne de la façon suivante:

1. création d'un socket
2. bind (lie) du socket à une adresse(et un port)
3. écoute du port en attente d'une connexion
4. acceptation de la connexion
5. lecture/envoi


### b. implémentation

Nous savons déja comment créer un socket donc nous allons directement passer à la seconde étape.

La fonction `bind` sert à lier un socket à un couple adresse port spécifique. Tout comme la fonction `connect` elle a besoin d'une structure `sockaddr_in`.

```c
#include<stdio.h>
#include<sys/socket.h>
#include<arpa/inet.h> // inet_addr

int main(int argc, char **argv)
{
    // variables
    int socket_desc, new_socket, c;
    struct sockaddr_in server, client;

    // creation de socket
    socket_desc = socket(AF_INET, SOCK_STREAM, 0);
    if (socket_desc == -1)
    {
        printf("\nCould not create socket\n");
    }

    // initialisation des membres de l'objet server
    server.sin_family = AF_INET;
    server.sin_addr.s_addr = INADDR_ANY;
    server.sin_port = htons (8888);

    // bind
    if (bind(socket_desc, (struct sockaddr * )&server, sizeof(server)) < 0)
    {
        puts("\nbind failded\n");
    }
    puts("\nbind done\n");

    // ecoute
    listen(socket_desc, 3);

    // Accept incoming connection
    puts("Waiting for incoming connections...\n");
    c = sizeof(struct sockaddr_in);
    new_socket = accept(socket_desc, (struct sockaddr * )&client, (socklen_t*)&c);
    if (new_socket < 0)
    {
        perror("accept failed\n");
    }

    puts("Connection accepted\n");

    return 0;
}
```
On lance le programme dans un premier terminal:
```sh
sol@debian:~/code/sockets/serveurHTTP$ ./serveur

bind done

Waiting for incoming connections...
```

dans un second terminal on lancer un `telnet localhost 888`:

```sh
sol@debian:~/LABOS/labo5/03Trace$ telnet localhost 8888
Trying ::1...
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
Connection closed by foreign host.
```

Dans le premier terminal un nouveau message apparait avant de nous rendre le prompt:

```sh
sol@debian:~/code/sockets/serveurHTTP$ ./serveur

bind done

Waiting for incoming connections...

Connection accepted
sol@debian:~/code/sockets/serveurHTTP$
```

[<a href="/00illustrations/sockets/wireshark3.png"><img src="/00illustrations/sockets/wireshark3.png"></a>]()

### c. IP et port du client qui se connecte

Pour avoir l'IP du client et le port de connexion en utilisant les informations de la structure `sockaddr_in` qu'on a passé en argument de la fonction `accept`:

```c
char *client_ip = inet_ntoa(client.sin_addr);
int client_port = ntohs(client.sin_port);
```

### d. rendre tout ça plus productif

Dans cet exemple nous acceptons une connexion mais la refermons directement. Ce n'est pas très interessant.

Répondons donc quelque chose au client qui se connecte à nous dire faire genre on est pas totalement innutile.

On peut utiliser la fonction `write` pour écire qqch dans le socket qui se connecte à nous et le client devrait le voir.

Il suffit d'ajouter ce bout de code à la fin de notre main en prenant soins d'ajouter les header `<string.h>` et `<unistd.h>`


```c
    // repond au client
    char *message = "Salut client, je te sens bien en moi mais k thx bye!\n.";
    write(new_socket, message, strlen(message));
```

Sur son terminal, le client verra:

```sh
sol@debian:~/LABOS/labo5/03Trace$ telnet localhost 8888
Trying ::1...
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
Salut client, je te sens bien en moi mais k thx bye!
.Connection closed by foreign host.
```

## 4. Serveur live (multiprocessus)

Notre serveur coupe la connexion directement après avoir envoyé sa réponse. Un serveur comme google.com est toujours à l'écoute de nouvelles connexions entrantes.

Celà veut dire que ce serveur est sensé tourner et accepter des connexions non-stop. Pour y arriver, la façon la plus facile est de mettre la fonction `accept` dans une boucle.

```c
#include<stdio.h>
#include<string.h>    //strlen
#include<sys/socket.h>
#include<arpa/inet.h> //inet_addr
#include<unistd.h>    //write

int main(int argc, char **argv)
{
    // variables
    int socket_desc, new_socket, c;
    struct sockaddr_in server, client;
    char* message;

    // creation de socket
    socket_desc = socket(AF_INET, SOCK_STREAM, 0);
    if (socket_desc == -1)
    {
        printf("\nCould not create socket\n");
    }

    // initialisation des membres de l'objet server
    server.sin_family = AF_INET;
    server.sin_addr.s_addr = INADDR_ANY;
    server.sin_port = htons (8888);

    // bind
    if (bind(socket_desc, (struct sockaddr * )&server, sizeof(server)) < 0)
    {
        puts("\nbind failded\n");
    }
    puts("\nbind done\n");

    // ecoute
    listen(socket_desc, 3);

    // Accepte les connexions
    puts("Waiting for incoming connections...\n");
    c = sizeof(struct sockaddr_in);

    while ( (new_socket = accept(socket_desc, (struct sockaddr * )&client, (socklen_t * )&c)) )
    {
        // affiche les infos du client
        printf("Connection from %s:%d accepted\n",inet_ntoa(client.sin_addr), ntohs(client.sin_port));

        // repond au client
        message = "Salut client, je te sens bien en moi mais k thx bye!\n.";
        write(new_socket, message, strlen(message));
    }

    if (new_socket < 0)
    {
        perror("accept failed\n");
    }

    return 0;
}
```

[<a href="/00illustrations/sockets/wireshark4.png"><img src="/00illustrations/sockets/wireshark4.png"></a>]()

En décortiquant le screenshot, nous pouvons voir que le Serveur tourne en permanance et accepte toutes les connexions telnet qu'on lui propose. Une fois qu'on coupe le serveur, les 3 connexions cessent automatiquement (logique).


# 5. Serveur TCP de base avec fork() des process enfants


```c
#include<sys/socket.h>
#include<stdio.h>
#include<stdlib.h>
#include<string.h>      // strlen
#include<arpa/inet.h>   // inet_addr
#include<unistd.h>      // write

void childProcess(int sock);

int main (int argc, char **argv) {
    int socket_desc;
    int new_socket;
    int n, pid;
    char buffer[256];
    struct sockaddr_in server;
    struct sockaddr_in client;

    // creation du socket
    socket_desc = socket(AF_INET, SOCK_STREAM, 0);

    if (socket_desc == -1) {
        perror("ERROR opening socket");
        exit(1);
    }

    // initialisation des membres de l'objet server
    server.sin_family = AF_INET;
    server.sin_addr.s_addr = INADDR_ANY;
    server.sin_port = htons(8888);

    // bind
    if (bind(socket_desc, (struct sockaddr * )&server, sizeof(server)) < 0) {
        perror("ERROR on binding");
        exit(1);
    }
    printf("\nbind done\n");

    // sleep mode en attendant un client
    listen(socket_desc, 3);
    printf("\nwaiting for incoming connections...\n");

    n = sizeof(struct sockaddr_in);

    // boucle infinie qui accepte et fork les clients distant
    while(1) {
        new_socket = accept(socket_desc, (struct sockaddr *) &client, (socklen_t *) &n);

        if (new_socket < 0) {
            perror("ERROR on accept");
            exit(1);
        }

        // affiche les infos du client
        printf("Connection from %s:%d accepted\n", 
            inet_ntoa(client.sin_addr), // conversion adr en un format Inet Std dot Ntation (str)
            ntohs(client.sin_port)); // conversion Network Byte Order vers Host Byte Order (endianess)
            
        // creation process enfant
        pid = fork();

        if (pid < 0) {
            perror("ERROR on fork");
            exit(1);
        }

        if (pid == 0) {
            // processus du client
            close(socket_desc);
            childProcess(new_socket);
            exit(0);
        }
        else {
            close(new_socket);
        }
    }
    return 0;
}

void childProcess(int sock) {
    int n;
    char buffer[256];
    bzero(buffer,256);

    // lecture du message du client
    n = read(sock, buffer, 255);

    if (n < 0) {
        perror("ERROR reading from socket\n");
        exit(1);
    }
    // affichage message client
    printf("Message from client: %s\n", buffer);

    // ecrit une reponse au client
    n = write(sock, "ok", 18);

    if (n < 0) {
        perror("ERROR reading from socket");
        exit(1);
    }
}
```

# 6. client TCP basique 

```c
#include<stdio.h>
#include<stdlib.h>
#include<netdb.h>
#include<unistd.h>
#include<string.h>


int main (int argc, char **argv) {
    int socket_desc;
    int portno;
    int n;
    struct sockaddr_in server_addr;
    struct hostent *server;

    char buffer[256];

    if (argc < 3) {
        fprintf(stderr, "usage %s hostname port\n", argv[0]);
        exit(0);
    }

    portno = atoi(argv[2]);

    // creation socket
    socket_desc = socket(AF_INET, SOCK_STREAM, 0);
    if (socket_desc < 0) {
        perror("ERROR opening socket");
        exit(1);
    }

    server = gethostbyname(argv[1]);

    if (server == NULL) {
        fprintf(stderr, "ERROR, no such host\n");
        exit(0);
    }

    bzero(( char *) &server_addr, sizeof(server_addr));
    server_addr.sin_family = AF_INET;
    bcopy(( char *)server->h_addr, (char *) &server_addr.sin_addr.s_addr, server->h_length);
    server_addr.sin_port = htons(portno);

    // connexion au serveur
    if (connect(socket_desc, (struct sockaddr*) &server_addr, sizeof(server_addr)) < 0) {
        perror("ERROR connecting");
        exit(1);
    }

    // demande a l'utilisateur d'ecrire un message qui sera affiche chez le serveur
    printf("Please enter a message: ");
    bzero(buffer,256);
    fgets(buffer,255,stdin);

    // envoie du message
    n = write(socket_desc, buffer, 255);

    if (n < 0) {
        perror("ERROR reading from socket");
        exit(1);
    }

    printf("%s\n", buffer);

    return 0;
}
```
