---
layout: post
title: "Reseau: Labo 5 (fr)"
subtitle: "labo 5: Application réseaux et sockets"
date: 2017-05-21
author: Sol
category: Reseau
tags: reseau labo couche4 tcp udp fr
finished: false
---


# Labo 5: applications réseaux socket

## 1. Makefile

#### vérification de la présence de `make`

```sh
sol@debian:~/LABOS/labo5$ which make
/usr/bin/make

```

#### s'assurer que c'est GNU make:

```sh
sol@debian:~/LABOS/labo5$ dpkg -s make | grep GNU
 GNU Make is a utility which controls the generation of executables
```

#### affichage du contenu de Makefile

```makefile
sol@debian:~/LABOS/labo5/makefile$ cat --show-tabs Makefile 

DEST_IMAGES=neuchatel.jpeg jura.jpeg

all: $(DEST_IMAGES)
^Ifile *.jpeg

clean:
^Irm -f $(DEST_IMAGES)

neuchatel.jpeg: neuchatel.png
^Ipngtopnm < $< | pnmtojpeg > $@
^I@# n'oubliez pas le TAB � gauche!
^I@echo converti $< en $@

jura.jpeg: jura.png
^Ipngtopnm < $< | pnmtojpeg > $@
^I@# n'oubliez pas le TAB � gauche!
^I@echo converti $< en $@
```

#### jeu de char de Makefille

```sh
sol@debian:~/LABOS/labo5/makefile$ file -i Makefile
Makefile: text/x-makefile; charset=iso-8859-1
```

#### rencodage de Makefille

```sh
sol@debian:~/LABOS/labo5/makefile$ recode iso-8859-1..UTF-8 Makefile 
sol@debian:~/LABOS/labo5/makefile$ file -i Makefile
Makefile: text/x-makefile; charset=utf-8
```

```makefile
sol@debian:~/LABOS/labo5/makefile$ cat --show-tabs Makefile 
DEST_IMAGES=neuchatel.jpeg jura.jpeg

all: $(DEST_IMAGES)
^Ifile *.jpeg

clean:
^Irm -f $(DEST_IMAGES)

neuchatel.jpeg: neuchatel.png
^Ipngtopnm < $< | pnmtojpeg > $@
^I@# n'oubliez pas le TAB à gauche!
^I@echo converti $< en $@

jura.jpeg: jura.png
^Ipngtopnm < $< | pnmtojpeg > $@
^I@# n'oubliez pas le TAB à gauche!
^I@echo converti $< en $@
```

### question 6

tuto [makefile](/Cpp/makefile.html)

```makefile
cible: dependance
    commandes

$@  # nom de la cible actuelle.
```

Le Makefille va réencoder les fichier `neuchatel.png` et `jura.png` en `neuchatel.jpg` et `jura.jpg`

### question 7

```makefile
fichier1.txt: fichier0.txt
    cat fichier0.txt > fichier1.txt
```

Cette règle ne sera exécutée que si fichier0.txt est plus récent que fichier1.txt

Dans notre cas:

```makefile
neuchatel.jpeg: neuchatel.png
	pngtopnm < $< | pnmtojpeg > $@
	@# n'oubliez pas le TAB à gauche!
	@echo converti $< en $@
```

La règle n'est pas exécutée car neuchatel.png n'est pas plus récent que neuchatel.jpg et pareil pour jura.png.

### question 8

```sh
sol@debian:~/LABOS/labo5/makefile$ touch jura.png
sol@debian:~/LABOS/labo5/makefile$ make 
pngtopnm < jura.png | pnmtojpeg > jura.jpeg
converti jura.png en jura.jpeg
file *.jpeg
jura.jpeg:      JPEG image data, JFIF standard 1.01, aspect ratio, density 1x1, segment length 16, baseline, precision 8, 406x493, frames 3
neuchatel.jpeg: JPEG image data, JFIF standard 1.01, aspect ratio, density 1x1, segment length 16, baseline, precision 8, 406x493, frames 3
```

Comme `touch jura.png` modifie la date de modification de jura.png, il devient donc plus récent que jura.jpeg et la règle...

```makefile
jura.jpeg: jura.png
	pngtopnm < $< | pnmtojpeg > $@
```

... est denouveau valable.

### question 9

`@` empêche l'affichage de la sortie texte d'une commande.

### question 10

En utilisant le **Pattern matching** on peut créer des règles génériques pour entre autres convertir les fichiers d'un certain format en un autre:

> Make exécute une règle même si le fichier correspondant est situé dans un sous dossier ! En cas de conflit, la règle avec la meilleure correspondance est choisie.

La syntaxe générale d'une fonction:
```makefile
$(fonction arg0,arg1,arg2...).
```

La fonction `patsubst` remplace par une paterne chaque mot dans une liste donnée.

Normalement les remplacement de Wildcards se font automatiquement dans les règles mais comme dans notre cas on veut utiliser un wildcard en argument d'une fonction on doit utiliser la fonction `wildcard`.

syntaxe:
```makefile
$(patsubst %.c,%.o,$(wildcard *.c))
```

Élimination de la redondance dans le Makefile original + optimisation via fonctions.

```makefile
DEST_IMAGES=$(patsubst %.png, %.jpeg, $(wildcard *.png))

all: $(DEST_IMAgES)
	file *.jpeg

%.jpeg: %.png
	pngtopnm < $< | pnmtojpeg > $@
	@echo converti $< en $@

clean:
	rm -f $(DEST_IMAGES)
```

sources:  
[gnu.org](https://www.gnu.org/software/make/manual/html_node/Wildcard-Function.html)  
[stackoverflow.com](https://stackoverflow.com/questions/33740270/makefile-patsubst-multiple-expressions)  
[learnxinyminutes.com](https://learnxinyminutes.com/docs/make/)

### question 11
#### a.
```sh
sol@debian:~/LABOS/labo5/02tests$ make a.o
cc    -c -o a.o a.c
sol@debian:~/LABOS/labo5/02tests$ ls
a.c  a.o
```

Si on utilise le `-d` de make on a tout un paquet d'informations mais dans les premières lignes on peut voir que c'est bien GCC qui est utilisé pour le build.

```sh
sol@debian:~/LABOS/labo5/02tests$ make -d a.o
GNU Make 4.0
Built for x86_64-pc-linux-gnu
# ...
Finished prerequisites of target file 'a.o'.
Must remake target 'a.o'.
cc    -c -o a.o a.c
Putting child 0x10dfde0 (a.o) PID 6042 on the chain.
Live child 0x10dfde0 (a.o) PID 6042 
Reaping winning child 0x10dfde0 PID 6042 
Removing child 0x10dfde0 PID 6042 from chain.
Successfully remade target file 'a.o'.
sol@debian:~/LABOS/labo5/02tests$ ls
a.c  a.o
```

#### b.

`CFLAGS` et `CXXFLAGS` sont les noms de variables d'environnement ou de variables du Makefile qui peuvent être utilisées pour paramétrer la compilation d'un logiciel.

Ces variables sont habituellement positionnées dans un Makefile et sont ajoutées quand le compilateur est appelé. Si elles ne sont pas spécifiées dans le Makefile, alors elles seront prises directement à partir de l'environnement, si elles sont présentes.

`CFLAGS` permet d'ajouter des paramètres sur la ligne de commande qui appelle le compilateur C, alors que `CXXFLAGS` est utilisé pour la compilation **C++**. De même, une variable similaire, `CPPFLAGS`, permet de passer des paramètres **sur la ligne de commande du Préprocesseur C**.

* **-Wall : -W represents warnings. -Wall means it will show all possible warnings.**

* -O2 : means optimize it. These are used in creating machine code from source code by compiler. There are many levels of optimizing. O2 is generally considered good enough, while O3 makes your file size bigger without optimizing significantly, and may make your program slower on some sort of algorithms.

* -g means debug. This will add many debugging "notes" in your final binary, so that when you run a debugger on your final binary file, the debugger will know what part of this binary file is related to which part of the original source code.

* -I/path/to/include/files : In C/C++, you can include files which compiler already knows where they are present with command: #include <filename.h>. If the compiler does not know where the include file you want to include is present, you can specify the path to that directory with -I/the/directory


Il nous faut donc créer un Makefile et y ajouter une ligne `CFLAGS=-Wall`

```sh
sol@debian:~/LABOS/labo5/02tests$ make -Wall a.o
cc -Wall   -c -o a.o a.c
sol@debian:~/LABOS/labo5/02tests$ rm a.0
sol@debian:~/LABOS/labo5/02tests$ make a.o
cc -Wall   -c -o a.o a.c
```



sources:  
[man gcc](https://linux.die.net/man/1/gcc)  
[wikipedia.fr](https://fr.wikipedia.org/wiki/CFLAGS)  
[cs.colby.edu](http://www.cs.colby.edu/maxwell/courses/tutorials/maketutor/)  
[wiki.gentoo.org](https://wiki.gentoo.org/wiki/GCC_optimization)  (très bon site) qui explique ce point en détails  

### question 12

Voir question 10

### question 13

voir question 10

## 2. Traçage et simulation

* `netstat -an`         *voir tous les sockets*  
* `netstat -an --inet`  *sockets ipv4*  
* `netstat -an --inet6` *sockets ipv6*  
* `netstat --inet --listen -NNNN` *socket en écoute*  
* `netstat --inet6 --listen -NNNN` *socket en écoute*  
* `-p` (sudo) *identification du process lié*  
* `strace`    *traçage des appels system*  
* `ltrace`    *traçage des fonctions de la LIBC*  
* *penser à utiliser Wireshark !*

### question 1

```sh
-rwxr-xr-x 1 sol sol 10995 Mar 25  2015 a-tracer
```

### question 2

```sh
sol@debian:~/LABOS/labo5/03Trace$ ./a-tracer &
[1] 6834
```

### question 3


```sh
sol@debian:~$ sudo netstat -p -n --inet --listen 6834
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      453/sshd        
tcp        0      0 0.0.0.0:12345           0.0.0.0:*               LISTEN      6834/a-tracer   
tcp        0      0 127.0.0.1:25            0.0.0.0:*               LISTEN      844/exim4       
udp        0      0 0.0.0.0:59362           0.0.0.0:*                           828/dhclient    
udp        0      0 0.0.0.0:68              0.0.0.0:*                           828/dhclient    
udp        0      0 10.0.2.15:123           0.0.0.0:*                           498/ntpd        
udp        0      0 127.0.0.1:123           0.0.0.0:*                           498/ntpd        
udp        0      0 0.0.0.0:123             0.0.0.0:*                           498/ntpd        
```

L'option `-n` permet de ne pas résoudre les noms.  
Notre application utilise donc le port **12345**.

### question 4

Après un kill du process...

```sh
sol@debian:~$ sudo netstat -p -n --inet --listen 6834
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      453/sshd        
tcp        0      0 127.0.0.1:25            0.0.0.0:*               LISTEN      844/exim4       
udp        0      0 0.0.0.0:59362           0.0.0.0:*                           828/dhclient    
udp        0      0 0.0.0.0:68              0.0.0.0:*                           828/dhclient    
udp        0      0 10.0.2.15:123           0.0.0.0:*                           498/ntpd        
udp        0      0 127.0.0.1:123           0.0.0.0:*                           498/ntpd        
udp        0      0 0.0.0.0:123             0.0.0.0:*                           498/ntpd  
```

GONE !

### question 5

#### a.

Dans le cycle de vie des sockets d'un serveur TCP nous retrouvons les fonctions socket() bind() listen() et accept() qui sont utilisées avant l'établissement de la connexion.

```c
///...
socket      (PF_INET, SOCK_STREAM, IPPROTO_IP)      = 3
setsockopt  (3, SOL_SOCKET, SO_REUSEADDR, [1], 4)   = 0
bind        (3, {sa_family=AF_INET, sin_port=htons(12345), sin_addr=inet_addr("0.0.0.0")}, 16) = 0
listen      (3, 1)                                  = 0
accept      (3, 
```

 Sur cette première capture des appels system nous retrouvons les fonctions nos fonctions:

```c
int socket(int domain, int type, int protocol);
```
Cette fonction crée un socket. On remarque qu'un socket est donc de type **int**.
Renvoie le descripteur du socket nouvellement créé.

```c
int bind(int sockfd, struct sockaddr *my_addr, socklen_t addrlen);
```
Lie un socket avec une structure `sockaddr`.

```c
int listen(int s, int backlog);
```
Définie la taille de la file de connexions en attente pour le socket `s`.

```c
int accept(int sock, struct sockaddr *adresse, socklen_t *longueur);
```
Accepte la connexion d'un socket sur le socket `sock`. Le socket aura été préalablement lié avec un port avec la fonction `bind`
L'argument adresse sera remplie avec les informations du client qui s'est connecté.
Cette fonction retourne un nouveau socket, qui devra être utilisé pour communiquer avec le client.


**La dernière fonction est particulièrement révélatrice. C'est bien le cycle de vie d'un serveur TCP qui attend un qu'un client se connecte. D'où le fait que la capture de `strace` soie en stand by à cet endroit.**

sources  
[super tuto programmation sockets](http://broux.developpez.com/articles/c/sockets/)

#### b.

>##### Cycle de vie sockets TCP  
>
> Il s'agit des appels système utilisés pour:
>* créér des sockets
>* les lier à un port connu
>* accepter des connexions TCP


Les étapes passées dans notre cas sont donc: création et bind

#### c.

acceptation (c'est la phase la plus dure et la plus longue d'une rupture)

### question 6

Après la commande `nc localhost 12345` nous avons:

```c
accept(3, Process 7127 attached
 <unfinished ...>
[pid  7127] close(3)                    = 0
[pid  7127] fstat(1, {st_mode=S_IFCHR|0620, st_rdev=makedev(136, 1), ...}) = 0
[pid  7127] mmap(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7fe86d900000
[pid  7127] write(1, "7127: new connection from client"..., 547127: new connection from client 127.0.0.1 port 47530
) = 54
[pid  7127] fcntl(4, F_GETFL)           = 0x2 (flags O_RDWR)
[pid  7127] brk(0)                      = 0x245a000
[pid  7127] brk(0x247b000)              = 0x247b000
[pid  7127] fstat(4, {st_mode=S_IFSOCK|0777, st_size=0, ...}) = 0
[pid  7127] mmap(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7fe86d8ff000
[pid  7127] lseek(4, 0, SEEK_CUR)       = -1 ESPIPE (Illegal seek)
[pid  7127] read(4, 
```

#### a.

si nous reprenons l'explication de la fonction accept():

>Accepte la connexion d'un socket sur le socket `sock`. Le socket aura été préalablement lié avec un port avec la fonction `bind`
L'argument adresse sera remplie avec les informations du client qui s'est connecté.
Cette fonction retourne un nouveau socket, qui devra être utilisé pour communiquer avec le client.

```c
accept(3, Process 7127 attached
```
nous dit donc que le le process avec le PID 7127  s'est connecté sur le socket 3.

`ps -u` nous renvoie que le process 7127 est:

```sh
sol       7127  0.0  0.0   4220    80 pts/1    S+   18:01   0:00 ./a-tracer
```

#### b.

Ici nous voyons qu'une seconde instance de a-tracer est apparue ce qui colle avec l'explication de la fonction `accept()`

```sh
sol@debian:~/LABOS/labo5/03Trace$ sudo netstat -anp --inet
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      453/sshd        
tcp        0      0 0.0.0.0:12345           0.0.0.0:*               LISTEN      7125/a-tracer   
tcp        0      0 127.0.0.1:25            0.0.0.0:*               LISTEN      844/exim4       
tcp        0      0 127.0.0.1:12345         127.0.0.1:47534         ESTABLISHED 7127/a-tracer   
tcp        0      0 127.0.0.1:12345         127.0.0.1:47533         TIME_WAIT   -               
tcp        0      0 127.0.0.1:47534         127.0.0.1:12345         ESTABLISHED 7126/nc         
udp        0      0 0.0.0.0:59362           0.0.0.0:*                           828/dhclient    
udp        0      0 0.0.0.0:68              0.0.0.0:*                           828/dhclient    
udp        0      0 10.0.2.15:123           0.0.0.0:*                           498/ntpd        
udp        0      0 127.0.0.1:123           0.0.0.0:*                           498/ntpd        
udp        0      0 0.0.0.0:123             0.0.0.0:*                           498/ntpd    
```


#### c.

Encore une fois, grace à l'explication de la fonction `accept()` nous pouvons dire que le processus initial, est toujours en écoute alors que le process qu'elle a retourné s'est lié au socket 47534. Nous le voyons sur le `netstat -anp --inet` précédent.

et voilà la dernière ligne qui désigne le process de `netcat` propriétaire du socket 47534

```sh
tcp        0      0 127.0.0.1:47534         127.0.0.1:12345         ESTABLISHED 7126/nc  
```


#### d.

Il y a `n + 1` = 3 sockets.

a-tracer est donc un **server** `TCP` en mode `flux`.

#### e.

`accept()`

#### f.

`-f -- follow forks`

#### g.

`-p pid -- trace process with process id PID, may be repeated`



## 7.

si on reprend tout depuis le début en monitorant `Loopback` avec Wireshark, c'est la fête:

1. création du premier process de a-tracer.
```sh
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
cp        0      0 0.0.0.0:12345           0.0.0.0:*               LISTEN      7303/a-tracer   
```

2. première connexion sur le process de `a-tracer`

[<a href="/00illustrations/wireshark1.png"><img src="/00illustrations/wireshark1.png"></a>]()

Le joli 3 way hand shake ! Les adresses IP sont les mêmes comme c'est un une communication entre un client TCP et un client au sein de la même machine.

nous avons tous les informations concernant les ports dans wireshark mais netstat nous donnes ces infos aussi:

```sh
tcp        0      0 127.0.0.1:12345         127.0.0.1:47541         ESTABLISHED 7308/a-tracer   
tcp        0      0 127.0.0.1:47541         127.0.0.1:12345         ESTABLISHED 7307/nc  
```

7303 reste en écoute 


3. seconde connexion sur le process de `a-tracer`

```sh
tcp        0      0 127.0.0.1:12345         127.0.0.1:47545         ESTABLISHED 7343/a-tracer   
tcp        0      0 127.0.0.1:47545         127.0.0.1:12345         ESTABLISHED 7342/nc   
```

nouveau 3 hand shake, 7303 fidèle au poste toujours en écoute pendant que son fils fait sa fête au le port 47545

4. création d'un nouveau processus `netcat` que nous trackons avec `strace nc localhost 12345`

une foule d'informations dont la fin nous montre le début du cycle de vie coté client.
Les appels système interessants sont:

```c
socket(PF_INET, SOCK_STREAM, IPPROTO_TCP) = 3
```

et

```c
connect(3, {sa_family=AF_INET, sin_port=htons(12345), sin_addr=inet_addr("127.0.0.1")}, 16) = 0
```

## 8.

```sh
sol@debian:~$ strace -e socket,bind,recvfrom nc -l -p 12345 -u localhost
socket(PF_LOCAL, SOCK_STREAM|SOCK_CLOEXEC|SOCK_NONBLOCK, 0) = 3
socket(PF_LOCAL, SOCK_STREAM|SOCK_CLOEXEC|SOCK_NONBLOCK, 0) = 3
socket(PF_LOCAL, SOCK_STREAM|SOCK_CLOEXEC|SOCK_NONBLOCK, 0) = 3
socket(PF_LOCAL, SOCK_STREAM|SOCK_CLOEXEC|SOCK_NONBLOCK, 0) = 3
socket(PF_INET, SOCK_DGRAM, IPPROTO_UDP) = 3
bind(3, {sa_family=AF_INET, sin_port=htons(12345), sin_addr=inet_addr("0.0.0.0")}, 16) = 0
recvfrom(3, "Wed May 24 19:48:45 CDT 2017\n", 8192, MSG_PEEK, {sa_family=AF_INET, sin_port=htons(46262), sin_addr=inet_addr("127.0.0.1")}, [16]) = 29
invalid connection to [127.0.0.1] from (UNKNOWN) [127.0.0.1] 46262
```

```sh
 = -1 ENOENT (No such file or directory)
socket(PF_LOCAL, SOCK_STREAM|SOCK_CLOEXEC|SOCK_NONBLOCK, 0) = 3
connect(3, {sa_family=AF_LOCAL, sun_path="/var/run/nscd/socket"}, 110) = -1 ENOENT (No such file or directory)
socket(PF_LOCAL, SOCK_STREAM|SOCK_CLOEXEC|SOCK_NONBLOCK, 0) = 3
connect(3, {sa_family=AF_LOCAL, sun_path="/var/run/nscd/socket"}, 110) = -1 ENOENT (No such file or directory)
socket(PF_INET, SOCK_DGRAM, IPPROTO_UDP) = 3
connect(3, {sa_family=AF_INET, sin_port=htons(12345), sin_addr=inet_addr("127.0.0.1")}, 16) = 0
write(3, "Wed May 24 19:48:45 CDT 2017\n", 29) = 29
```

[<a href="/00illustrations/wireshark2.png"><img src="/00illustrations/wireshark2.png"></a>]()

## 9.

```sh
sol       7303  0.0  0.0   4080   660 pts/1    S+   18:56   0:00 ./a-tracer
sol       7308  0.0  0.0      0     0 pts/1    Z+   18:56   0:00 [a-tracer] <defunct>
sol       7342  0.0  0.0   6328  1660 pts/3    S+   19:15   0:00 nc localhost 12345
sol       7343  0.0  0.0   4220    80 pts/1    S+   19:15   0:00 ./a-tracer
sol       7390  0.0  0.0      0     0 pts/1    Z+   19:36   0:00 [a-tracer] <defunct>
```

Concernant les processus fils terminés qui se transforment en "processus" zombie:

Un soucis de reallocation mémoire après la destruction d'un objet?



