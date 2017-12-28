---
layout: post
title: "Linux: CheatSheet"
subtitle: "Commandes utiles"
date: 2017-06-19
author: Sol
category: Linux
tags: Linux
finished: false
mathjax: true
---

$ Linux\; users \; have \; one \; thing \; that \; often \; sets $
$them \; apart \; from \; their \; Windows \; and \; Mac $
$using\; colleagues. \; They \; often \; spend \; a \; lot \; more$ 
$time \; fixing \; things \; or \; finding \; out \; how \; to $
$fix \; things. $

# Specifique au Dell xps 13 9343

## Carte reseau
```
Network controller: Broadcom Limited BCM4352 802.11ac
```

```
$ sudo apt-get purge bcmwl-kernel-source
$ sudo apt-get install bcmwl-kernel-source

# relaunch wifi card
$ sudo modprobe -r b43 ssb wl
$ sudo modprobe wl 
```

## Touchpad issues

Libinput doesn't allow for inertia in the scroll so...

```
$ sudo apt purge xserver-xorg-input-synaptics
$ sudo apt install xserver-xorg-input-synaptics
```

after a reboot:

```
$ cp /usr/share/X11/xorg.conf.d/70-synaptics.conf /etc/X11/xorg.conf.d/
```

If needed: `mkdir xorg.conf.d`
Then, replace the first section of the file with:

```
Section "InputClass"
        Identifier "touchpad catchall"
        Driver "synaptics"
        MatchIsTouchpad "on"

        Option "TapButton1" "1"
        Option "TapButton2" "3"
        Option "TapButton3" "2"

        Option "EmulateTwoFingerMinZ" "40"
        Option "EmulateTwoFingerMinW" "8"

		Option "CoastingSpeed" "20"


    	Option "VertScrollDelta" "-35"
    	Option "HorizScrollDelta" "-35"

        Option "VertEdgeScroll" "on"
        Option "VertTwoFingerScroll" "on"
        Option "HorizTwoFingerScroll" "on"
EndSection
```

* [full reference](https://wiki.archlinux.org/index.php/Touchpad_Synaptics#Using_syndaemon)


## Connexion drop fix


In `/etc/NetworkManager/NetworkManager.conf`

add:
```
[device]
wifi.scan-rand-mac=address=no
```

and then `sudo service network-manager restart`

If the issue start again, we can comment both lines in NetworkManager.conf, restart the NetworkManager and then add them again...

## Hibernate on lid close
*[systemctl](http://tipsonubuntu.com/2017/04/13/shutdown-hibernate-ubuntu-17-04-lid-closed/)


## Carte son
Si le driver saute lors de switchs entre OSs:
```
pulseaudio -k && sudo alsa force-reload
```

## Edit keyboard layout
* [people.uleth.ca](http://people.uleth.ca/~daniel.odonnell/Blog/custom-keyboard-in-linuxx11#e)
* [unicode char list](https://en.wikipedia.org/wiki/List_of_Unicode_characters)


# Launch command on startup
```
sudo nano /etc/rc.local
```

```
#!/bin/bash

add command here
```

then `sudo chmod 755 /etc/rc.local`



# CCSM (CompizConfig)

Undecorate window:
In **Window Decoration**, in **Decoration Windows** add for example (undecorate Terminals):

```
(any) & !(class=Mate-terminal) & !(class=Terminator)
```

# TLP (power mangement)
* `sudo tlp start`
* `sudo tlp-stat -s`

To disable:
```
sudo nano /etc/default/tlp
```
Change
`TLP_ENABLE=1` to `TLP_ENABLE=0`

# Defaulting different shell

First find the path to the fish shell:
```
$ which fish
/usr/bin/fish
```


Next, we can change the shell by typing:
```
$ chsh -s /usr/bin/fish
```
We will be asked for our password to confirm. Once this is complete, every time we login, we'll have a fish prompt.

To go back to zsh:
```
chsh -s /bin/zsh
```

# Fish shell

* [Tutorial](https://fishshell.com/docs/current/tutorial.html)

* `fish_update_completions`: scan les habitudes d'écriture
* omf init.fish file: `/home/XXX/.local/share/omf`

## Oh-my-fish (OMF)

* Install:
```
curl -L https://get.oh-my.fish | fish
```

* Theme bobthefish:
```
omf install bobthefish
```

* In settings: `/home/uname/.config/fish/functions/fish_prompt.fish`

```
set -g theme_newline_cursor yes
set -x VIRTUAL_ENV_DISABLE_PROMPT 1
set -g theme_display_user yes
set -g theme_color_scheme solarized
set -g fish_prompt_pwd_dir_length 3

function fish_right_prompt

end
```




# General

* `sudo dpkg -i *.deb` $ \Rightarrow $ installer  
* `apt-get install` _softName_ $ \Rightarrow $ dld et installe  
* `apt-cache policy` _pkg-name_ $ \Rightarrow $ check if a package is installed  
* `wget` _"url"_ $ \Rightarrow $ download  
* `apt-get --purge remove` _softName_ $ \Rightarrow $ uninstall
* `sudo apt-get autoremove` $ \Rightarrow $ remove unused dependencies
* `sudo apt-get purge --auto-remove gimp` $ \Rightarrow $ combine previous 2
* `sudo apt-get clean` $ \Rightarrow $ remove downloaded archives files
* `sudo apt-get purge --auto-remove` _softName_ $ \Rightarrow $ COMPLETE REMOVE

> This command removes the aptitude cache in “/var/cache/apt/archives”. When you install a program, the package file is downloaded and stored in that directory. You don’t need to keep the files in that directory. However, the only drawback of deleting them, is that if you decide to install any of those programs again, the packages would have to be downloaded again.




* `caja .` $ \Rightarrow $ open file browser here
* `su` _username_ $ \Rightarrow $ switch user
* `df` $ \Rightarrow $ HD space
* `sleep` _seconds_ $ \Rightarrow $ pause terminal
* `gcc -Wall file.c -o file` $ \Rightarrow $ compile
* `whoami` $ \Rightarrow $ user
* `ipcalc` `ip` `-s` SR1 SR2 SR3 $ \Rightarrow $ subnet calculatord
* `which` _make_ $ \Rightarrow $ vérifie présence de make sur la machine

# DPI

* dConf Editor

# find

### liens

* [tecmint.com](https://www.tecmint.com/35-practical-examples-of-linux-find-command/)

La commande `find` permet de chercher des fichiers, et éventuellement d'éxecuter une action dessus. 

```sh
find . -name "*markdown" | less
```

### Principales options

| Option   | Signification                            |
| -------- | ---------------------------------------- |
| `-name`  | Recherche par **nom** de fichier.        |
| `-type`  | Recherche par **type** de fichier.       |
| `-user`  | Recherche par **propriétaire**.          |
| `-group` | Recherche par appartenance à un **groupe**. |
| `-size`  | Recherche par **taille** de fichier.     |
| `-atime` | Recherche par date de **dernier accès**. |
| `-mtime` | Recherche par date de **dernière modification**. |
| `-ctime` | Recherche par date de **création**.      |
| `-perm`  | Recherche par **autorisation d'accès**.  |
| `-links` | Recherche par **nombre de liens** au fichier. |

### Exemples

```sh
$ touch {cochon,mouton,poule,vache}.txt
$ mkdir poule
$ ls
cochon.txt  mouton.txt  poule  poule.txt  vache.txt
```



* Trouve tous les fichiers dont le nom est _poule.txt_ à partir du répertoire courant (récursif)

  ```sh
  $ find . -name "poule.txt"
  ./poule.txt
  ```


* Trouve tous les fichiers qui **contiennent** _poule_ à partir du répertoire courant

  ```sh
  $ find . -name "*poule*"
  ./poule.txt
  ./poule
  ```

* Trouve tous les fichiers dont le nom est _Code_ dans l'user folder uniquement (**pas de récursion**)

  ```sh
  $ find ~ -maxdepth 1 -name "Code"
  /home/sol/Code
  ```

* Ignorer la **casse**

  ```sh
  $ find . -iname POULE.txt
  ./poule.txt
  ```

* Uniquement les **répertoires** (directory)

  ```sh
  $ find . -type d -name poule
  ./poule
  ```

* Tous les fichiers _.txt_ dans un répertoire

  ```sh
  $ find . -type f -name "*.txt"
  ./vache.txt
  ./cochon.txt
  ./mouton.txt
  ./poule.txt
  ```

### Avancé

* Trouve et supprime un fichier unique

  ```sh
  $ find . -type f -name "poule.txt" -exec rm -f {} \;
  $ ls
  cochon.txt  mouton.txt  poule  vache.txt
  ```

* Trouve et supprime plusieurs fichiers

  ```sh
  $ find . -type f -name "*.txt" -exec rm -f {} \;
  $ ls
  poule
  ```

* Trouve et déplace plusieurs fichiers

  ```sh
  $ mkdir un deux trois final
  $ touch un/lala.txt deux/baba.txt trois/gaga.txt
  $ find . -name "*.txt"
  ./un/lala.txt
  ./trois/gaga.txt
  ./deux/baba.txt
  $ find . -type f -name "*.txt" -exec mv {} final/ \;
  $ find . -name "*.txt"                              
  ./final/baba.txt
  ./final/gaga.txt
  ./final/lala.txt
  ```

# Power tweak

## tools

* i-Nex $ \Rightarrow $ gui monitor
* tlp $ \Rightarrow $ power management
* powertop $ \Rightarrow $ stats

## usefull commands
* `lcpu | grep MHz` $ \Rightarrow $ real time cores freq
* `tlp-stat` $ \Rightarrow $ show various info on power consuption
* `powertop` $ \Rightarrow $ various info on power consuption
* `sudo powertop --time=60 --html=power_report.html` $ \Rightarrow $ stats

## disable touche screen


1. `xinput --list`
2. `find touchscreen system id
3. disable it with `xinput disable xx`

* turn it back on with `xinput enable xx`


# Customization

[Heaps of collections](https://github.com/erikdubois/Ultimate-Ubuntu-17.04/blob/master/README.md)

# If big issue

1. Shutdown 
2. boot into _Recovery mode_
3. select _enter root session_

* `mount -o rw,remount /` $ \Rightarrow $ gives the ability to modify files

# Foucs issue with conky

[bug.launchpad](https://bugs.launchpad.net/ubuntu/+source/compiz/+bug/934189)

> I recently installed the Compiz Settings Manager and using their Window Rules option I was able to tell Compiz to never focus on my Conky window. I needed to restart for the change to take effect, but that has totally solved the problem on my system.


# Custom keyboard (X11)

* [askubuntu](https://askubuntu.com/questions/510024/what-are-the-steps-needed-to-create-new-keyboard-layout-on-ubuntu)
* [original](http://people.uleth.ca/~daniel.odonnell/Blog/custom-keyboard-in-linuxx11)



# Networking

Configuration des adresses IP des interfaces via `ifconfig`
chaque interface est identifiée par un nom:

* **eth0**     première carte Ethernet
* **eth0:0**   alias de la première carte Ethernet
* **eth0:1**   alias de la seconde carte Ethernet
* **lo:**      loopback ou interface de bouclage

## fourre tout

* ICMP echo:  
  `ping`
* traceroute (/usr/sbin):  
  `traceroute`
* super traceroute:  
  `mtr`
* sniffer:  
  `tcpdump -i eth0` `protocol`
* infos sur l'ip:  
  `whois` `url/ip`
* check ARP cache:  
  `arp`
* flush entry found:  
  `arp -d`
* better tool to clear arp:  
  `ip (-s) (-s) neigh flush all`

## ifconfig

* Liste des interfaces réseau configurées:  
  `ifconfig`
* Configurer une interface réseau:  
  `ifconfig ethX` _ip_ `netmask` _msk_ `up`
* fonctionne aussi:  
  `ifconfig` `ethX` _CIDR_ `up` 
* ajouter carte reseau:  
  `ifconfig` `eth0:0` _ip_ `netmask` _msk_  



## route
* affiche table de routage:  
  `route` `-n`
* ajoute route (dev est utile pour les routeurs?):  
  `route` `add` `-net` _ip_ `netmask` _msk_ `gw` _routeur_ `[dev ethX]`
* supprime route:  
  `route` `del` `-net` _ip_ `netmask` _msk_ `gw` _routeur_ `[dev ethX]`
* supprime route: (par defau)  
  `route` `del` `-net` 0.0.0.0/24
* ajoute default gw:  
  `route` `add` `default gw` _ip\_gw_ `netmask` _msk_ `[dev ethX]`
* ajoute default gw:  
  `route` `add` `default gw` _ip\_gw_ **SANS MASK**

 Exemple de route valable: (gw = 192.168.50.2)

 ```
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
192.168.50.0    0.0.0.0         255.255.255.0   U     0      0        0 eth0
0.0.0.0         192.168.50.2    0.0.0.0         UG    0      0        0 eth0
 ```

> Modifier le serveur DNS dans `/etc/resolv.conf`


|Destination  |Passerelle |Genmask        |Indic|Metric|Ref|Use|Iface|
|-------------|-----------|---------------|-----|------|---|---|-----| 
|139.124.187.4|0.0.0.0    |255.255.255.255|UH   |0     |0  |0  |eth0 |   
|139.124.187.0|0.0.0.0    |255.255.255.0  |U    |10    |0  |0  |eth0 |   
|127.0.0.0    |0.0.0.0    |255.0.0.0      |U    |0     |0  |0  |lo   |   
|0.0.0.0      |139.24.87.1|0.0.0.0        |UG   |10    |0  |0  |eth0 |

* **Indic**: flags, indications sur la route.
* **U**: route est en service (activée).
* **H**: destination est un ordinateur (host). Si pas **h** => dest = réseau.
* **G**: route pas directe & passerelle est un routeur (gateway).
* **D**: route a été créée par une redirection (message ICMP).
* **M**: route a été modifiée par une redirection (message ICMP).
* **!**: route est rejetée (option reject).


## Serveur DHCP

### Création de serveur:

Dans **/etc/dhcp3/dhcpd.conf**

```
subnet 192.168.1.0 netmask 255.255.255.0 {
    range 192.168.1.51  192.168.1.99;     
}
ddns-update-style none;
```

### lancer serveur dhcp:
```
/etc/init.d/dhcpd start
```

### passage en mode auto

Dans **/etc/network/interfaces**

```
auto eth0
allow-hotplug eth0
iface eth0 inet dhcp
```


### divers

#### add nameserver (DNS)

Dans **/etc/resolv.conf**
```
nameserver ipv  (pc1: 192.168.43.1)
```

* defini le nom de la machine:
  `hostname` nom 
* requete DHCP:
  sudo `dhclient` eth0 
* ports:
  `/etc/services`
* config ip (Obsolet?):
  `/etc/hosts`

#### config IP data:
Dans **/etc/network/interfaces**
```
allow-hotplug eth0
#iface eth0 inet dhcp 
iface eth0 inet static
            adress 192.168.0.3
            netmask 255.255.255.0
            network 192.168.0.0
            broadcast 192.168.0.255
            gw ?
```

#### Passage en mode routeur
```sh
echo > /proc/sys/net/ipv4/ip_forward 1
```


## ARP <span style="color:red">**Approfondir!**</span>
/usr/sbin/arp      (si pas dans le path)
/usr/sbin/arp -n   (si pas dans le path)

-a, -n, -d et -s.

Defaut arp entry ttl
```sh
cat /proc/sys/net/ipv4/neigh/default/gc_stale_time
```

## netstat
Utile pour tout ce qui concerne les **sockets** et les **ports**

* voir tous les sockets:  
  `netstat -an`
* sockets ipv4:  
  `netstat -an --inet`
* sockets ipv6:  
  `netstat -an --inet6`
* socket en écoute:  
  `netstat --inet --listen -NNNN`
* socket en écoute:  
  `netstat --inet6 --listen -NNNN`
* identification du process lié (PID/prgrm name):  
  `sudo netstat -p`

>* `-a` display all sockets
>* `-n` ne résoud pas les noms


```
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


## strace
Traçage des **appels système**

```sh
strace -e socket,connect ./simple-client localhost 7000
```

* `-e` **expr**: permet de filtrer les résultats. Séparer par une virgule les mot clés.

## Ltrace
Comme `strace` mais pour les fonctions de la **LIBC**

> Penser à utiliser Wireshark!

## netcat <span style="color:red">**Approfondir!**</span>
Création de serveurs/clients ? (tcp only?)  

* `nc -l -p` _port_: Création d'un serveur TCP
    * `-l` : listen mode des connexions entrantes
    * `-p` : port local


# Divers

## oneLiners

* entrées uniques:  
  `cat log.txt | uniq -u | wc -l`  
* 2 entrées les plus fréquentes:  
  `cat log.txt | uniq -c | sort -n | tail -2`  
* find recursive and move:  
  `find ./ -type f -name '*.ttf' -exec mv -i {} ./  \;`  

## process:
* current shell process:  
  `ps`          
* nice display:  
  `pstree`      
* all actifs process:  
  `ps -u`       
* processus actifs + PID current shell:  
  `jobs -l`     
* suspend le process:  
  `CTRL + Z`    
* bring a bg process to fg:  
  `fg` _[PID]_
* put process into back ground:  
  `bg`          
* kill processkill:  
  `kill` _pid_  
* kill signals:  
  `kill -l` 

## tee

* Envoie en même temps stdout vers un fichier et vers l'écran:  
  `tee [-a] file`  
    `-a`: append [CTRL]+c to exit  

* Lance make et stocke sa sortie dans build.log:
  `make` | `tee` _build.log_

Lance la commande make install et rajoute sa sortie à la fin du fichier build.log:
`make` _install_ | `tee` `-a` 

## uniq:
`-u`:only uniq lines
`-c`:count occurences repetition

## sort:
`-n`:numeric sort. bigger -> end
`-d`:alphabetic sort (nie les chifres)
`-f`:ignore la cassecat

##shutdown 
```sh
init 0          # shutdown
shutdown -h now # shutdown
-r now          # reboot
```

## tool screen

`screen`:       *start*  
`ctrl`-`a` `c`: *create windows*  
`ctrl`-`a` `n`: *switch next*  
`ctrl`-`a` `p`: *switch previous*  


Command key:  ^A   Literal ^A:  a
``` 
break      ^B b          only       Q
clear      C             other      ^A
colon      :             pow_break  B
copy       ^[ [          pow_detach D
detach     ^D d          prev       ^P p ^?
digraph    ^V            readbuf    &amp;amp;amp;lt;
displays   *             redisplay  ^L l
fit        F             removebuf  =
flow       ^F f          reset      Z
focus      ^I            screen     ^C c
hardcopy   h             select     '
help       ?             silence    _
```

## users
* add user:    
  `sudo adduser username`    
* add sudo:  
  `sudo usermod -aG sudo username`   
  * delete all from user:  
    `userdel -r username`	  


## shell log

`script` `-a` _my-file_       : (myfile is the output file)(proactif)  
`history` `>` _file-name_     :(save the session) 


## Tar

`tar cvf` _ar­chi­ve_ _fi­chiers ou répert­oir­es_  
* `-c`: créer  
* `-x`: extract  
* `v` : suit progre­ssion   
* `f` : fichier  

tar -cf nomFichierCompresse.tar fichierAcompresser :compresse

tar -tvf <ar­chi­ve> : affiche le contenu d'une archive ou vérifie son intégrité (t : test)
tar -xvf <ar­chi­ve> : ext tous les fichiers d'une archive
tar -xvf <ar­chi­ve> <fi­chiers ou rép.> : ext seulement quelques fichiers d'une archive

## rar

`unrar` `x` _Filename.rar_ : (x = crée Filename et décompresse dedans)

## zip

* Compresser:  
  `zip` `-r` _nom\_fichier.zip_ _repertoire\_ou\_ fichier_  
* decompresser:    
  `unzip` _nom\_fichier.zip_ `-d` _destination_  


## scripts:
tous les scripts commencent par:
```
#!/bin/bash
```

```
chmod +x script.sh
```
ou
```
chmod 0755 script.sh
```
launch:
`./script.sh`

## cut:

Extract portions of text from a file by selecting column.
```sh
$ cat test.txt
```
`cat``:for file oriented operations.
`cp`` :for copy files or directories.
`ls`` :to list out files and directories with its attributes.

*select column of char:*
```sh
$ cut -c2 test.txt
a
p
s
```

*using range:*
```sh
$ cut -c1-3 test.txt
cat
cp
ls
```


*start pos:*
```sh
$ cut -c3- test.txt
```


*end pos:*
```sh
$ cut -c-8 test.txt
cat comm
cp comma
ls comma
```

*start-end:*
```sh
$ cut -c3-8 test.txt 
t comm
 comma
 comma
```

#### Cool stuff

`-f`: FIELD-LIST (Print only the fields listed in FIELD-LIST.)
`-d`: specifies what is the field delimiter

```
$ cat /etc/passwd | tail -5
hplip:x:118:7:HPLIP system user,,,:/var/run/hplip:/bin/false
sol:x:1000:1000:sol,,,:/home/sol:/bin/bash
git:x:1001:1001:,,,:/home/git:/bin/bash
vde2-net:x:119:130::/var/run/vde2:/bin/false
uml-net:x:120:131::/home/uml-net:/bin/false
```
```
cut -d':' -f1 /etc/passwd | tail -5
hplip
sol
git
vde2-net
uml-net
```

## diff and patching

`$ diff originalfile updatedfile > patchfile.patch`  
`$ patch originalfile -i patchfile.patch -o updatedfile`  
`$ diff -s updatedfile [/path/to/the/original/updatedfile]/updatefile (check)`  
`$ gcc -Wall updatedfile.c -o updatedfile`  




## Running a command as another local user

`sudo -u` _username_ _command_: (replace username)



## Sudo list:

The list of users that can execute sudo can be modified
by running (req root): sudo /usr/sbin/visudo
The syntax of every sudo line is:

user machine=(effective_user) command

```
user 			: name of the new sudo user
machine			: host name in which sudo is valid
effective_user	: stands for the effective users that are 
				  allowed to execute the commands.
command			: represents a set of commands that the
				  can run.
```

## The sticky bit permission:

It's a permission bit that protects the files within a directory. 
If the directory have the sticky bit set, a file can be deleted
only by the owner of the file, the owner of the directory or root.

* set the sticky bit:
  `chmod` `+t` file: 
* unset it:
  `chmod` `-t` file: 

## guest extension

`sudo apt-get update`   
`sudo apt-get upgrade`   
`sudo apt-get install virtualbox-guest-x11`   

##grep:

`grep` _option_ _motif_ _path_  
Affiche chaque ligne des file contenant le motif. Le motif est une expression régulière

`-v`: lignes qui ne contie­nnent pas le motif  
`-c`: seulement le nombre de lignes  
`-n`: numéros des lignes trouvées  
`-i`: pas sensible à la casse  
`-r`: recursif  
`-E`: extended regex  

* parcourt les f et affiche les lignes qui corresp au motif:  
  `grep` _mo­tif_ _fi­chi­ers­_    
* affiche les lignes contenant erreur dans les fichiers *.log:  
  `grep` _erreur_ \*.log   
* idem + osef casse:  
  `grep` -i erreur \*.log 
* idem + récursif:  
  `grep` `-ri` _erreur_ 
* affiche toutes les lignes des fichiers sauf celles qui contie­nnent info:  
  `grep` `-v` _info_ \*.log 

ex:
```sh
grep -r He-Arc ./
```

## sed:
Voici une petite astuce bien pratique qui permet de changer une chaîne de caractères par une autre, contenue dans un fichier texte. Tout ceci se réalise en une seule ligne de commande, grâce à sed.
Voici un exemple d'utilisation :

`sed -i -e "s/chaines1/chaine2/g"` _fichier_

A noter que si la chaîne contient des caractères spéciaux, 
on devra les échapper avec '\'. Voici un exemple oû l
'on remplace "/home" par "/tmp":

`sed -i -e "s/\/home/\/tmp/g"` _fichier_ 

Si on a besoin de remplacer une chaîne par une autre pour toute 
une liste de fichiers contenue dans un répertoire, 
on pourra utiliser ce petit script basé sur le même principe :

```sh
#!/bin/bash
for file in *.txt
do
  echo "Traitement de $file ..."
  sed -i -e "s/chaine1/chaine2/g" "$file"
done 
```

Ce dernier va :
1. établir la liste de tous les fichiers dont l'extension est .txt
2. puis effectuer le remplacement

Une autre méthode donnée en commentaire qui utilise 
la commande find et qui permet de traiter des sous-dossier en plus :

```
find . -name "*.txt" -type f -exec sed -i "s/chaine1/chaine2/g" {} \;
```

#### FIND AND REPLACE with SED

Let us start off simple:
Imagine you have a large file ( txt, php, html, anything ) and you want to 
replace all the words "ugly" with "beautiful" because you just met your old 
friend Sue again and she/he is coming over for a visit.

This is the command:

```
$ sed -i 's/ugly/beautiful/g' /home/bruno/old-friends/sue.txt
```

Well, that command speaks for itself "sed" edits "-i in place ( on the spot ) 
and replaces the word "ugly with "beautiful" in the file "/home/bruno/old-friends/sue.txt"

Now, here comes the real magic:
Imagine you have a whole lot of files in a directory 
( all about Sue ) and you want the same command to do
all those files in one go because she/he is standing right 
at the door . . 
Remember the find command ? We will combine the two:

```
$ find /home/bruno/old-friends -type f -exec sed -i 's/ugly/beautiful/g' {} \;
```

Sure in combination with the find command you can do all 
kind of nice tricks, even if you don't remember where 
the files are located !

