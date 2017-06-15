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

<!--# Programmation socket en C sous Linux-->

# 1. Qu'est-ce qu'un socket?

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

### Architectures T2 et T3

* **T2**: le client interagit directement avec le serveur. Ce type d'architecture peut avoir des problèmes de sécurité (gérés grâce au SSL: Secure Socket Layer) et des soucis de performance.

* **T3**: Un logiciel appelé **Middlewarese** placé entre client et serveur et est utilisé pour effectuer des tests de sécurité.

### Types de serveurs

* Serveurs **itératifs**: forme la plus simple. Le serveur gère un client à la fois et les clients attendent pour s'y connecter.

* Serveurs **concurents (Concurrent(en))**: gènre plusieurs processus en parallèle et permet de servire plusieurs requêtes à la fois. La forme la plus simple pour implémenter un serveur concurent sous Unix est de **FORKER** un processus enfant pour prendre en charge chaque client.

### Interaction entre client et serveur
Ce schéma décrit l'intéraction complète entre un client et un serveur.

<img src="/00illustrations/socket2/diag1.png" height="auto">

# 5. Unix Socket - Structures

Plusieurs structures sont utilisées pour la programmations socket sous Unix. Celles-ci contiennent des infomations concernant entre autre l'adresse et le port. La plupart des fonctions socket nécéssitent un pointeur sur l'adresse d'une structure comme argument.

## sockaddr
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




## sockaddr_in

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



## in_addr

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



## hostent

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


## servent

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


## Conseils d'utilisation

* Ces structures sont au coeur de la programmation réseau. Nous les initialisons, remplissons et leurs passons des pointeurs qui pointent vers diverses fonctions de sockets. Parfois nous passons un pointeur à l'une de ces structure ce qui à pour effet de compléter les données de la structure.

* Toujours passer ces structures par référence (pointeur) accompagné de la taill de cette dernière.

* Quand une fonction socket remplit une de ces structures, nous passons également la longueure de la strucure par référence pour que sa valeur puisse être modifié par la fonction.


# 6. Ports et services (fonctions)
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

# 7. Ordre des bytes (représentation des bytes en mémoire)

## endiannes

> Ordre dans lequel les octets sont organisés dans une case mémoire **ou dans une communication**. Big endian et Little endian sont deux architectures différentes.

### Big endian
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


### Little endian
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

## Fonctions pour changer l'ordre des bytes

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

## Fonction pour déterminter l'endiannes d'une machine


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

# 8. Fonctions pour manipuler les adresses IP

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

# Fonctions principales 

<img src="/00illustrations/socket2/diag1.png" height="auto">

Reprenons les fonctions sur ce diagramme:

## socket()
Pour effectuer des taches I/O, la première chose dont un process a besoin est d'appeler la fonction `socket()` en spécifiant le type de protocol de communication (TCP/UDP) et la famille (IPv4/6).


```c
#include <sys/types.h>
#include <sys/socket.h>

int socket (int family, int type, int protocol);
```

Cet appel retourne un **descripteur de socket** (descripteur de fichier) qu'on peut utiliser pour les appels système.

### Paramètres:

* Famille: spécifie la famille du protocol parmis les suivants

| famille | desciption |
|:--:|:--:| 
| AF_INET | protocol IPv4 |
| AF_INET6 | protocol IPv6 |
| AF_LOCAL | protocol domaine Unix |
| AF_ROUTE | socket de routage |
| AF_KEY | clé |

* Type: spécifie le type de socket à utiliser parmis les suivants

| type | desciption |
|:--:|:--:| 
| SOCK_STREAM | stream socket (TCP) |
| SOCK_DGRAM | datagrm socket (UDP) |
| SOCK_SEQPACKET | sequence packet socket |
| SOCK_RAW | raw socket |

* Protocole: doit être cohérent avec le choix précédent ou à `0` pour laisser le système en choisir un par défaut.

| protocol | desciption |
|:--:|:--:| 
| IPROTO_TCP | TCP transport protocol |
| IPROTO_UDP | UDP transport protocol |
| IPROTO_SCTP | SCTP transport protocol |

## connect()
Utilisée par les clients TCP pour établir une connextion avec un serveur TCP

```c
#include <sys/types.h>
#include <sys/socket.h>

int connect(int sockfd, struct sockaddr *serv_addr, int addrlen);
```
Cet appel retourne 0 si il se connecte avec succes à un serveur -1 dans le cas d'une erreur.

### Paramètres

* **sockfd**: descripteur de socket retourné par la fonction socket()
* **serv_addr**: pointeur sur la structure `sockaddr` qui contient l'IP et le port destination
* **addrlen**: à set sur `sizeof(struct sockaddr)`

## bind()
Cette fonction assigne un l'adresse d'un protocol local à un socket. Avec les protocoles IP, l'adresse du protocol est une combinaison de:
* l'IPv4 sur 32 bits et le port TCP ou UDP sur 16 bits
* l'iPv6 sur 128 bits et le port rcp ou UDP sur 16 bits

**Cette fonction est uniquement employée par les serveurs TCP**

```c
#include <sys/types.h>
#include <sys/socket.h>

int bind(int sockfd, struct sockaddr *my_addr,int addrlen);
```
Cet appel retourn 0 si le bind à l'adresse est réussit, -1 dans le cas d'une erreur.

### Paramètres
* **sockfd**: c'est un descritpeur de socket que retourne la fonction socket()
* **my_addr**: pointeur sur la structure `sockaddr` qui contient l'IP et le port local
* **addrlen**: à set sur `sizeof(struct sockaddr)`

Une valeur de `0` pour le numéro de port veut dire que le système va choisir un port aléatoire.

Si on passe `INNDDR_ANY` comme valeur pour l'adresse IP, celà veut dire que l'IP sera assignée automatiquement.

```c
server.sin_port = 0;
server.sin_addr.s_addr = INADDR_ANY;
```

> note: Tous les ports jusque 1024 sont réservés (well known ports). Le choix d'un port se fait dans la plage des 1024-65535 sauf si se sont ceux utilisés par d'autres programmes

## listen()
Cette fonction est **uniquement appellée par les serveurs TCP** et effectue deux actions:

* transforme un socket non-connecté en socket passif (indique que le Kernel devrait accepter les connextions entrantes sur ce socket).

* le second paramètre de cette fonction spécifie le nombre maximum de connexions que le Kernel doit autoriser en queue de ce socket.

```c
#include <sys/types.h>
#include <sys/socket.h>

int listen(int sockfd,int backlog);
```

Cet appel retourn 0 en cas de réussite et -1 dans le cas d'une erreur.

### Paramètres
* **sockfd**: descripteur de socket retourné par la fonction `socket()`
* **backlog**: nombre de connexions autorisés

## accept()
Cette fonction est appellée par un serveur TCP pour retourner le prochainne connextion en queue.

```c
#include <sys/types.h>
#include <sys/socket.h>

int accept (int sockfd, struct sockaddr *cliaddr, socklen_t *addrlen);
```

Cet appel retourne un descripteur (strictement positif) en casss de success et -1 en cas d'erreur. Le descripteur retourné est assumé être un descripteur de socket client et nous pourons utiliser les opérations read/write sur ce descripteur pour commmuniquer avec le client.

### Paramètres
* **sockfd**: descripteur de socket retourné par la fonction `socket()`
* **clientaddr**: pointeur sur la structure `sockaddr` qui contient l'IP et le port du client
* **addrlen**: à set sur `sizeof(struct sockaddr)`

## send()
Utilisé pour envoyer des données via socket stream (TCP)

Pour envoyer des données, nous pouvons utiliser `write()`

```c
int send(int sockfd, const void *msg, int len, int flags);
```
Cet appel retourne le nombre de bytes envoyés et -1 dans le cas d'une erreur.

### Paramètres
* **sockfd**: descripteur de socket retourné par la fonction `socket()`
* **msg**: pointeur sur la donnée qu'on veut envoyer
* **len**: longueur de la donnée qu'on veut envoyer (bytes) 
* **flags**: set sur 0;

## recv()
Est utilisé pour recevoir des données d'un socket strean (TCP)

Nous pouvons utiliser `read()` pour lire la donnée.

```c
int recv(int sockfd, void *buf, int len, unsigned int flags);
```
Cet appel retourne le nombre de bytes lu dans le buffer ou -1 en cas d'erreur.

### Paramètres
* **sockfd**: descripteur de socket retourné par la fonction `socket()`
* **buf** : le buffer qui contient les  données
* **len**: longueur de la donnée qu'on veut envoyer (bytes) 
* **flags**: set sur 0

## sendto()
Utilisée pour envoyer des données via socket datagram(UDP)

```c
int sendto(int sockfd, const void *msg, int len, unsigned int flags, const struct sockaddr *to, int tolen);
```

Cet appel retourne  le nombre de bytes envoyés et -1 en cas d'erreur

### Paramètres

* **sockfd**: descripteur de socket retourné par la fonction `socket()`
* **msg** : pointeur sur la donnée à envoyer
* **len**: longueur de la donnée qu'on veut envoyer (bytes) 
* **flags**: set sur 0
* **to**: pointeur sur la strucuture `sockaddr` coté hôte
* **tolen**: set sur zero

## recvfrom()
Utilisée pour recevoir des données d'un socket datagram

```c
int recvfrom(int sockfd, void *buf, int len, unsigned int flags struct sockaddr *from, int *fromlen);
```

Cet appel retourne le nombre de bytes lu dans le buffer ou 01 dans le cas d'une erreur

### Paramètres

* **sockfd**: descripteur de socket retourné par la fonction `socket()`
* **buf** : le buffer qui contient les  données
* **len**: longueur de la donnée qu'on veut envoyer (bytes) 
* **flags**: set sur 0
* **from**: pointeur sur la structure `sockaddr`
* **fromlen** set to `sizeof(struct sockaddr)`

## close()
Cette fonction est utilisée pour fermer la communication entre client et serveur.

```c
int close(int sockdf);
```
retourne 0 en cas de succes et -1 en cas d'erreur.

### Paramètres
* **sockfd**: descripteur de socket retourné par la fonction `socket()`

## shutdown()
Utilisée pour mettre fin à la communication (gracefully). Entre client et serveur. Cette fonction donne plusde contrôle que la fonction `close()`

```c
int shutdown(int sockfd, int how)
```

Cet appel retourne 0 en cas desucces et -1 en cas d'erreur

### Paramètres

* **sockfd**: descripteur de socket retourné par la fonction `socket()`
* **how**: une des trois valeurs suivantes:
      * **0**: indique que recevoir des données n'est pas permis
      * **1**: indique que l'envoi de données est autorisé
      * **2**: indique que l'envoi ET la réception ne sont pas autorisé.

> Quand when = 2 c'est équivalent à close()

## select()
Cette fonction indique quel descripteur de fichier est prêt à être lu, ecrit ou a des erreurs en attente.

<span style="color:red">chercher de la doc</span>

```c
 int select(int  nfds, fd_set  *readfds, fd_set  *writefds, fd_set *errorfds, struct timeval *timeout);
```
Cet appel retourne 0 en cas de succes et -1 en cas d'erreur.

### Paramètres

<span style="color:red">chercher de la doc</span>


# Fonctions utiles

## write()
Tente d'écrire `nbytes` du buffer pointé par `buf` dans le fichier associé au descripteur de fichier `fieldes`

```c
#include <unistd.h>
int write(int fildes, const void *buf, int nbyte);
```
Cet appel retourne le nombre de bytes dans le fichiers associé avec le paramètre `fildes`.

### Paramètres

* **fildes**: descripteur de socket retourné par la fonction socket()
* **buf**: pointeur  qui pointe sur les données à envoyer
* **nbyte**: nombre de bytes à écrire.

## read()

```c
#include <unistd.h>

int read(int fildes, const void *buf, int nbyte);
```

### Paramètres
* **fildes**: descripteur de socket retourné par la fonction socket()
* **buf**: pointeur  qui pointe sur les données à lire
* **nbyte**: nombre de bytes à lire.

## fork()
Crée un nouveau process (process enfant) qui sera la copie conforme du process parent .

```c
#include <sys/types.h>
#include <unistd.h>

int fork(void);
```

## bzero()

Place `nbyte` bytes "\0" (NULL) dans la string _s_. Cette fonction est utile pour set les strucures socket à des valeurs = NULL.

```c
void bzero(void *s, int nbyte);
```

### Paramètres
* **s**: spécifie la string à remplire
* **nbyte**: spécifie le nombre de bytes à remplire


# Mise en pratique: serveur TCP

* Créér un socket avec l'appel système`socket()`