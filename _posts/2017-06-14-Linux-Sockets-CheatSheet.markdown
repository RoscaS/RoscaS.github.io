---
layout: post
title: "Reseau: Linux socket cheatSheet"
subtitle: "Linux, langage C"
date: 2017-06-14
author: Sol
category: Reseau
tags: reseau sockets smtp tcp udp c fr
finished: false
---

## 1. Interaction client serveur
<img src="/00illustrations/socket2/diag1.png" height="auto">


## 2. Port and Service Functions
Unix provides the following functions to fetch service name from the /etc/services file.

```c
struct servent *getservbyname(char *name, char *proto)
```
This call takes a service name and a protocol name and returns the corresponding port number for that service.

```c
struct servent *getservbyport(int port, char *proto)
```
This call takes a port number and a protocol name and returns the corresponding service name.


## 3. Byte Ordering Functions
```c
unsigned short htons (unsigned short hostshort) 
```
This function converts 16-bit (2-byte) quantities from host byte order to network byte order.
```c
unsigned long htonl (unsigned long hostlong) 
```
This function converts 32-bit (4-byte) quantities from host byte order to network byte order.
```c
unsigned short ntohs (unsigned short netshort) 
```
This function converts 16-bit (2-byte) quantities from network byte order to host byte order.
```c
unsigned long ntohl (unsigned long netlong) 
```
This function converts 32-bit quantities from network byte order to host byte order.



## 4. IP Address Functions
```c
int inet_aton (const char *strptr, struct in_addr *addrptr) 
```
This function call converts the specified string,in the Internet standard dot notation, to a network address, and stores the address in the structure provided. The converted address will be in Network Byte Order (bytes ordered from left to right). It returns 1 if the string is valid and 0 on error.

```c
in_addr_t inet_addr (const char *strptr)
```
This function call converts the specified string, in the Internet standard dot notation, to an integer value suitable for use as an Internet address. The converted address will be in Network Byte Order (bytes ordered from left to right). It returns a 32-bit binary network byte ordered IPv4 address and INADDR_NONE on error.

```c
char *inet_ntoa (struct in_addr inaddr) 
```

This function call converts the specified Internet host address to a string in the Internet standard dot notation.

## 5. Socket Core Functions

```c
int socket (int family, int type, int protocol) 
```
This call returns a socket descriptor that you can use in later system calls or it gives you -1 on error.

```c
int connect (int sockfd, struct sockaddr *serv_addr, int addrlen) 
```
The connect function is used by a TCP client to establish a connection with a TCP server. This call returns 0 if it successfully connects to the server, otherwise it returns -1.


```c
int bind(int sockfd, struct sockaddr *my_addr,int addrlen) 
```
The bind function assigns a local protocol address to a socket. This call returns 0 if it successfully binds to the address, otherwise it returns -1.

```c
int listen(int sockfd, int backlog) 
```
The listen function is called only by a TCP server to listen for the client request. This call returns 0 on success, otherwise it returns -1.

```c
int accept (int sockfd, struct sockaddr *cliaddr, socklen_t *addrlen) 
```
The accept function is called by a TCP server to accept client requests and to establish actual connection. This call returns a non-negative descriptor on success, otherwise it returns -1.

```c
int send(int sockfd, const void *msg, int len, int flags) 
```
The send function is used to send data over stream sockets or CONNECTED datagram sockets. This call returns the number of bytes sent out, otherwise it returns -1.


```c
int recv (int sockfd, void *buf, int len, unsigned int flags) 
```
The recv function is used to receive data over stream sockets or CONNECTED datagram sockets. This call returns the number of bytes read into the buffer, otherwise it returns -1 on error.


```c
int sendto (int sockfd, const void *msg, int len, unsigned int flags, const struct sockaddr *to, int tolen) 
```
The sendto function is used to send data over UNCONNECTED datagram sockets. This call returns the number of bytes sent, otherwise it returns -1 on error.


```c
int recvfrom (int sockfd, void *buf, int len, unsigned int flags struct sockaddr *from, int *fromlen) 
```
The recvfrom function is used to receive data from UNCONNECTED datagram sockets. This call returns the number of bytes read into the buffer, otherwise it returns -1 on error.


```c
int close (int sockfd) 
```
The close function is used to close a communication between the client and the server. This call returns 0 on success, otherwise it returns -1.


```c
int shutdown (int sockfd, int how) 
```
The shutdown function is used to gracefully close a communication between the client and the server. This function gives more control in comparison to close function. It returns 0 on success, -1 otherwise.


```c
int select (int nfds, fd_set *readfds, fd_set *writefds, fd_set *errorfds, struct timeval *timeout) 
```
This function is used to read or write multiple sockets.



## 6. Socket Helper Functions
```c
int write (int fildes, const void *buf, int nbyte) 
```
The write function attempts to write nbyte bytes from the buffer pointed to by buf to the file associated with the open file descriptor, fildes. Upon successful completion, write() returns the number of bytes actually written to the file associated with fildes. This number is never greater than nbyte. Otherwise, -1 is returned.

```c
int read (int fildes, const void *buf, int nbyte) 
```

The read function attempts to read nbyte bytes from the file associated with the open file descriptor, fildes, into the buffer pointed to by buf. Upon successful completion, write() returns the number of bytes actually written to the file associated with fildes. This number is never greater than nbyte. Otherwise, -1 is returned.


```c
int fork (void) 
```
The fork function creates a new process. The new process, called the child process, will be an exact copy of the calling process (parent process).


```c
void bzero (void *s, int nbyte) 
```

The bzero function places nbyte null bytes in the string s. This function will be used to set all the socket structures with null values.


```c
int bcmp (const void *s1, const void *s2, int nbyte) 
```

The bcmp function compares the byte string s1 against the byte string s2. Both the strings are assumed to be nbyte bytes long.


```c
void bcopy (const void *s1, void *s2, int nbyte) 
```

The bcopy function copies nbyte bytes from the string s1 to the string s2. Overlapping strings are handled correctly.


```c
void *memset(void *s, int c, int nbyte) 
```

The memset function is also used to set structure variables in the same way as bzero.




# 7. Linux Socket: Structures


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


