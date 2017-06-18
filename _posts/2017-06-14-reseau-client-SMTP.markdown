---
layout: post
title: "Reseau: Client SMTP"
subtitle: "Linux, langage C"
date: 2017-06-14
author: Sol
category: Reseau
tags: reseau sockets smtp tcp udp c fr
finished: false
---
## liens
[repo](https://github.com/RoscaS/c_client_SMTP)

## Client smtp 

### Overview

Les fonctions de communication de ce client sont sous la forme d'une **machine d'état** dont voici le diagramme:
<img src="/00illustrations/SMTP/etat.png" align="middle" height="auto">

Explications:
<img src="/00illustrations/SMTP/etats.png" align="middle" height="auto">

Avant de rentrer dans l’automate, les paramètres rentrés en ligne de commande sont transmis à la fonction `initMD()` qui effectue l’initialiasation d’un objet de type **MailData**.  Cet objet nous permet de facilement déplacer les parametres.  

L’objet de type **MailData** est transmis à la fonction `machinEtat()` qui crée d'abord une connexion (via la fonction `connexion()`) au serveur spécifié en paramètre à la ligne de commande avant de rentrer dans la machine d'état. Nos données passent ensuite par plusieurs états qui permettent la détection d’erreurs et le bon fonctionnement d'un envoi d'email. Le passage d’un état à l’autre se fait en fonction du premier chiffre (poids fort) du code erreur renvoyé par le serveur.

Le logiciel est capable de détecter si l'envoie a été **grey-listé**. Si tel est le cas, il va `fork()` le process et relancer une instance de la fonction `machinEtat()` dans le process enfant pendant que le process parent est terminé. Cette manipulation permet d'entre autres récupérer le prompt après un avertissement. Après 20 minutes le process fils va faire une nouvelle tentative d'envoi de l'email.

## Compilation

Elle se fait à l'aide d'un simple make dans le folder où se trouve le fichier `.c`.

```makefile
TARGET=clientSMTP
CFLAGS=-Wall -lpthread

all: $(TARGET)

clean:
	rm -f $(TARGET)
```

## Utilisation
Il suffit de lancer le client en remplaçant les paramètres par ceux désirés:

```sh
$ ./clientSMTP from subject filePath domainDNS destination [port]
```

## Code source:

```c++
#include<string.h>
#include<stdio.h>
#include<stdlib.h>
#include<unistd.h>
#include<netdb.h>

#define PORT_SMTP 25 // SMTP: port 25, (587: auth, 465: ssl)
#define BUFFER_SIZE 1024

// etats (DECOMMENTER CES LIGNES POUR USAGE !)
// #define START 0
// #define HELO 1
// #define FROM 2
// #define TO   3
// #define DATA 4
// #define SUBJ 5
// #define BODY 6
// #define DOT  7
// #define QUIT 8
// #define ERROR4 9
// #define ERROR5 10

// machineEtat: on/off
#define OFF 0
#define ON  1

// struct parametres
typedef struct MailData MailData;
struct MailData {
    const char *source;
    const char *sujet;
    const char *filePath;
    const char *domainDns;
    const char *destination;
          int   portno;
};

// initialisation des parametres
void initMD(MailData *md, char **arg) {
     md->source      = arg[1];
     md->sujet       = arg[2];
     md->filePath    = arg[3];
     md->domainDns   = arg[4];
     md->destination = arg[5];

     if(arg[6]!=NULL) {
        md->portno  = atoi(arg[6]);
     }
     else {
        md->portno  = PORT_SMTP;
     }
}

// prototypes
int  machineEtat(const MailData *args, const int sleepTime);
int  connexion(const MailData *md);
void finConnexion(const int sock);
void errExit(char *err, const int sock);


// =================================================
// ==========           Main              ========== 
// =================================================

int main(int argc, char **argv) {
    //check args
    if (argc < 6) {
        printf("ERROR bad-args\nusage:\t%s source subject filePath domainDNS destination [port]\n", argv[0]);
        exit(1);
    }
    // declaration & initialisation objet MailData
    MailData md1;
    initMD(&md1, argv);
    // lancement machine d'etat
    machineEtat(&md1,0);
    return EXIT_SUCCESS;
}
// ========= End of Main ==========



// =================================================
// ==========           Connexion         ========== 
// =================================================

int connexion(const MailData *md) {
    // declaration des variables utiles
    int                sock;
    struct sockaddr_in servAddr;    
    struct hostent    *serv;        

    // creation socket
    if (!(sock = socket(AF_INET, SOCK_STREAM, 0))) {
        errExit("ERROR: error opening socket", sock);
    }

    // initialisation des attributs descripteurs socket
    if (!(serv = gethostbyname(md->domainDns))) {
        errExit("ERROR: no such host", sock);
    }

    bzero( ( char *) &servAddr, sizeof(servAddr));    
    servAddr.sin_family = AF_INET;

    bcopy( (char *)  serv->h_addr, 
           (char *) &servAddr.sin_addr.s_addr, 
                     serv->h_length);

    servAddr.sin_port = htons(md->portno);

    // connexion au serveur
    if (connect(sock, 
               (struct sockaddr*) &servAddr, 
               sizeof(servAddr)) < 0) {
        errExit("ERROR: error connecting", sock);
    }

    printf("\nConnected to %s\n", md->domainDns);
    return sock;
}

// ========= End of connexion ==========


// =================================================
// ========== connexionFin ==========         
// =================================================

void finConnexion(const int sock) {
    if (sock < 0) {
        return;
    }
    printf("Closing connexion...\n");
    close(sock);
}

// ========= End of connexionFin ==========


// =================================================
// ========== machineEtat ==========         
// =================================================

int machineEtat(const MailData *args, int sleepTime) {

	sleep(sleepTime);

    int  etat, sock, onOff, recup, pid;
    char buffer[BUFFER_SIZE];
    FILE *f   = NULL;
    FILE *txt = NULL;

    onOff = ON; 
    etat  = START;
    sock  = connexion(args);

    if (!(f = fdopen(sock, "r+"))) {
        errExit("ERROR: can't open socket", sock);
    }

    while(onOff) {
        recup = 0;

        switch(etat) {

            case START:
                printf("\nEtat: START: Initialisation de la communication\n");
                break;

            case HELO:
                printf("\nEtat: HELO\n");
                fprintf(f, "HELO host\r\n");
                break;

            case FROM:
                printf("\nEtat: FROM\n");
                fprintf(f,"MAIL FROM: <%s>\r\n",args->source);
                printf("MAIL FROM: <%s>\r\n",args->source);
                break;

            case TO:
                printf("\nEtat: TO\n");
                fprintf(f,"RCPT TO: <%s>\r\n",args->destination);
                printf("RCPT TO: <%s>\r\n",args->destination);
                break;

            case DATA:
                printf("\nEtat: DATA\n");
                fprintf(f,"DATA\r\n");
                break;

            case SUBJ:
                printf("\nEtat: SUBJ\n");
                fprintf(f,"Subject: %s\r\n",args->sujet);
                printf("Subject: %s\r\n",args->sujet);
                recup =1;
                break;

            case BODY:
                printf("\nEtat: BODY\n");

                if (!(txt = fopen(args->filePath, "r"))) {           // DRY
                    errExit("ERROR: can't open mail file", sock);
                }
                while (fgets(buffer, sizeof(buffer), txt)) {
                    fputs(buffer,f);
                    printf("%s\n",buffer );
                }
                fclose(txt);
                recup=1;
                break;

            case DOT:
                printf("\nEtat: DOT\n");
                fprintf(f,"\r\n.\r\n");
                break;

            case QUIT:
                printf("\nEtat: QUIT\n");
                fprintf(f,"QUIT\r\n");
                onOff = OFF;                                
                break;

            case ERROR4:
                printf("\nEtat: ERROR4\n");
                fprintf(stderr, "ERROR %c%c%c: grey-listed\n", buffer[0], buffer[1], buffer[2]);
                fprintf(stderr, "forking process...\n");
                onOff = OFF;

                pid = fork();

                // process enfant
                if (pid == 0) {
                    printf("Child process: retrying to send in 20'...\n");
                    machineEtat(args, 1200);
                }

                // process parent
                if (pid > 0) {
                    printf("Exit parent process..\n");
                    fclose(f);
                    finConnexion(sock);
                    exit(0);
                }

	            // echec forking
	            else {
                    errExit("ERROR: fork failed", sock);   
	            }

                break;     

            case ERROR5:
                printf("\nEtat: ERROR5\n");
                fprintf(stderr, "ERROR %c%c%c: final-error\n", buffer[0], buffer[1], buffer[2]);
                finConnexion(sock);
                fprintf(stderr, "exit...\n");
                exit(1);
                break;
            
            default:
                errExit("ERROR: unknown error", sock);       
        }

        if(recup == 0) {
            fgets(buffer, sizeof(buffer), f);
            printf("%s", buffer);

            
            if(buffer[0] == '2' || buffer[0] == '3') {
                ++etat; 
            }
            else if(buffer[0] == '4') {
                etat = ERROR4;
            }
            else if(buffer[0] == '5') {
                etat = ERROR5;
            }  
            else {
                etat = -1;
            }
        }

        else {
            ++etat;
        }
    }
    
    fclose(f);
    finConnexion(sock);
    return 0;
}

// ========= End of machineEtat ==========


// =================================================
// ========== errExit ==========         
// =================================================

void errExit(char *err, const int sock) {
    fprintf(stderr, "%s\n", err);
    finConnexion(sock);
    fprintf(stderr, "exit...\n");
    exit(-1);
}

// ========= End of errExit ==========

```

## Tests


### 1. localhost

```
sol@debian:~/code/clientSMTP/SMTP/clientSMTP$ ./clientSMTP3 un@deux.com test mail.txt localhost un@trois.com
Trying localhost
Connected to localhost

Etat: START
220 debian.home ESMTP Exim 4.84_2 Sun, 18 Jun 2017 10:32:02 -0500

Etat: HELO
250 debian.home Hello localhost [127.0.0.1]

Etat: FROM
MAIL FROM: <un@deux.com>
250 OK

Etat: TO
RCPT TO: <un@trois.com>
250 Accepted

Etat: DATA
354 Enter message, ending with "." on a line by itself

Etat: SUBJ
Subject: test

Etat: BODY
C'est pour ce soir!


Etat: DOT
250 OK id=1dMcBG-000179-FR

Etat: QUIT
221 debian.home closing connection
sol@debian:~/code/clientSMTP/SMTP/clientSMTP$ 
```

<a href="/00illustrations/SMTP/wiresharkLocalHost.png"><img src="/00illustrations/SMTP/wiresharkLocalHost.png" height="auto"></a>

### 2. serveur mail réel

```
sol@debian:~/code/clientSMTP/SMTP/clientSMTP$ ./clientSMTP3 sol.rosca@he-arc.ch test mail.txt smtprel.he-arc.ch steven.jeanneret@outlook.com 
Trying smtprel.he-arc.ch
Connected to smtprel.he-arc.ch

Etat: START
220 ***************************************************

Etat: HELO
250 srv-smtpapp1.he-arc.ch

Etat: FROM
MAIL FROM: <sol.rosca@he-arc.ch>
250 2.1.0 Ok

Etat: TO
RCPT TO: <steven.jeanneret@outlook.com>
250 2.1.5 Ok

Etat: DATA
354 End data with <CR><LF>.<CR><LF>

Etat: SUBJ
Subject: test

Etat: BODY
C'est pour ce soir!


Etat: DOT
250 2.0.0 Ok: queued as 1F45B60DC2

Etat: QUIT
221 2.0.0 Bye
sol@debian:~/code/clientSMTP/SMTP/clientSMTP$ 
```

<a href="/00illustrations/SMTP/wiresharkSMTPREL.png"><img src="/00illustrations/SMTP/wiresharkSMTPREL.png" height="auto"></a>

Ici, nous testons donc avec un serveur fonctionnel. Voici une copie d'écran du mail reçu

<a href="/00illustrations/SMTP/SMTPREL-Steven.jpg"><img src="/00illustrations/SMTP/SMTPREL-Steven.jpg" height="auto" align="middle"></a>


### 3. Erreur 4xx et fork()

```
sol@debian:~/code/clientSMTP/SMTP/clientSMTP$ ./clientSMTP3 nathan.latino@he-arc.ch test mail.txt smtp.alphanet.ch schaefer@alphanet.ch
Trying smtp.alphanet.ch
Connected to smtp.alphanet.ch

Etat: START
220 shakotay.alphanet.ch ESMTP Postfix (Debian/GNU)

Etat: HELO
250 shakotay.alphanet.ch

Etat: FROM
MAIL FROM: <nathan.latino@he-arc.ch>
250 2.1.0 Ok

Etat: TO
RCPT TO: <schaefer@alphanet.ch>
450 4.2.0 <schaefer@alphanet.ch>: Recipient address rejected: Greylisted, see http://postgrey.schweikert.ch/help/alphanet.ch.html

Etat: ERROR4
ERROR 450: grey-listed
Forking process...
Exit parent process..
Child process: retrying to send in 20'...
sol@debian:~/code/clientSMTP/SMTP/clientSMTP$ ls
total 28
-rwxr-xr-x 1 sol sol 12008 Jun 18 10:15 clientSMTP3
-rwxr-xr-x 1 sol sol  6886 Jun 18 10:15 clientSMTP3.c
-rwxr-xr-x 1 sol sol    20 Jun 18 09:23 mail.txt
-rwxr-xr-x 1 sol sol    84 Jun 18 09:23 makefile
sol@debian:~/code/clientSMTP/SMTP/clientSMTP$ Trying smtp.alphanet.ch
Connected to smtp.alphanet.ch

Etat: START
220 shakotay.alphanet.ch ESMTP Postfix (Debian/GNU)

Etat: HELO
250 shakotay.alphanet.ch

Etat: FROM
MAIL FROM: <nathan.latino@he-arc.ch>
250 2.1.0 Ok

Etat: TO
RCPT TO: <schaefer@alphanet.ch>
450 4.2.0 <schaefer@alphanet.ch>: Recipient address rejected: Greylisted, see http://postgrey.schweikert.ch/help/alphanet.ch.html

Etat: ERROR4
ERROR 450: grey-listed
Forking process...
Exit parent process..
Child process: retrying to send in 20'...
```


<a href="/00illustrations/SMTP/wiresharkForkGreyList.png"><img src="/00illustrations/SMTP/wiresharkForkGreyList.png" height="auto"></a>

Si nous observons le timing des différentes entrées, nous voyons qu’elles correspondent au log précédent. Pour faciliter les tests du méchanisme de `fork()` le temps de réémission a été réduit à 30 secondes (dans la version finale ce temps à été ajusté à 20 minutes). La partie du milieux ne nous interesse pas, c’est l’activité réseau normale. 

Voici également une capture d’un track du process clientSMTP qui nous montre le process parent dans un premier temps et qui laisse sa place au process enfant (2e capture). Dans la pratique, au moment de l’arrêt du process parent, nous récupérons le prompts. 

<a href="/00illustrations/SMTP/PIDparent.png"><img src="/00illustrations/SMTP/PIDparent.png" height="auto"></a>

<a href="/00illustrations/SMTP/PIDenfant.png"><img src="/00illustrations/SMTP/PIDenfant.png" height="auto"></a>

### 3. erreur 5xx 

```
sol@debian:~/code/clientSMTP/SMTP/clientSMTP$ ./clientSMTP3 sol.rosca@gmail test mail.txt aspmx.l.google.com test@testt.ch
Trying aspmx.l.google.com
Connected to aspmx.l.google.com

Etat: START
220 mx.google.com ESMTP h32si6380560edc.376 - gsmtp

Etat: HELO
250 mx.google.com at your service

Etat: FROM
MAIL FROM: <sol.rosca@gmail>
250 2.1.0 OK h32si6380560edc.376 - gsmtp

Etat: TO
RCPT TO: <test@testt.ch>
550-5.1.1 The email account that you tried to reach does not exist. Please try

Etat: ERROR5
ERROR 550: final error
exit...
sol@debian:~/code/clientSMTP/SMTP/clientSMTP$ 
```

<a href="/00illustrations/SMTP/wiresharkERR5xx.png"><img src="/00illustrations/SMTP/wiresharkERR5xx.png" height="auto"></a>

Pour forcer une erreur de type 5xx, nous avons volontairement entré une adresse de destination invalide pour ce serveur (gmail). 
Nous pouvons constater que malgré l’erreur, la fermeture de la communication se fait de façon gracieuse. 