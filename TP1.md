# TP1 : Remise dans le bain

**Ce TP va servir d'introduction**, pour mieux apprendre à se connaître, et que vous mettiez la main sur des outils qu'on utilisera probablement tout le long du cours.

Le cours, justement, est intitulé "Virtualisation Réseau", **on va donc profiter de ce premier TP pour mettre en place nos outils de virtualisation**, et commencer à jouer avec.

## Sommaire

- [TP1 : Remise dans le bain](#tp1--remise-dans-le-bain)
  - [Sommaire](#sommaire)
  - [0. Setup](#0-setup)
- [I. Most simplest LAN](#i-most-simplest-lan)
- [II. Ajoutons un switch](#ii-ajoutons-un-switch)
- [III. Serveur DHCP](#iii-serveur-dhcp)
  - [2. DHCP spoofing](#2-dhcp-spoofing)
  - [3. BONUS : DHCP starvation](#3-bonus--dhcp-starvation)

## 0. Setup

➜ **Téléchargez de suite** :

- un outil pour **virtualiser des équipements réseau** et simuler des connexions réseau
  - on utilisera [**GNS3**](https://www.gns3.com/)
- un outil pour **gérer des VMs**
  - je vous recommande [**VirtualBox**](https://www.virtualbox.org/)
  - il faut qu'il soit compatible avec GNS3
- un outil de **sniffing réseau**
  - je vous recommande l'excellent [**Wireshark**](https://www.wireshark.org/) évidemment

Pour ce premier TP, on va commencer smooth, et on utilisera avec ces deux outils de virtu -GNS3 et VirtualBox- uniquement des systèmes Linux.

> *On utilisera souvent des OS Linux dans le cours, c'est pour nous un outil de choix : un outil robuste et éprouvé dans le milieources : l'énergie ça pousse pas dans les arbres 🙃

➜ Une fois les outils téléchargés et prêts à être lancer, vous pouvez passer à la suite du TP :

- chaque partie correspond à une topologie à réaliser dans GNS3
- vous vous servirez de VPCS pour les clients ou de machines Linux
- installez l'OS de votre choix sur une nouvelle VM, et éteignez là. Vous pourrez la cloner à l'infini dès que vous avez besoin d'une machine pour un TP
- rappel : je veux un compte-rendu qui contient TOUTES les commandes réalisées

> ***N'hésitez pas à m'appeler, autant de fois que nécessaire, si une étape n'est pas claire, si vous comprenez mal quelque chose, ou pour toute question.***

# I. Most simplest LAN

![Topologie n°1](./img/topo1.png)

> *VM Linux ou VPCS, libre à vous !*

Un LAN (ou "Réseau Local" en français) c'est juste un réseau formé de plusieurs machines connectées physiquement entre elles.

Pour ça, chaque machine a besoin d'au moins une carte réseau, et on relie par des câbles les machines entre elles. (Merci captain obvious ?)

On va donc commencer au plus simple : deux machines connectées par un câble.

Les objectifs de cette partie :

- monter la topologie dans GNS3 *(drag'n'drop des machins et tirer des câbles dans GNS3)*
- définir des IPs sur les deux machines
- visualiser l'IP choisie, ainsi que l'adresse MAC prédeterminée
- vérifier que les deux machines peuvent communiquer en faisant un `ping`
- visualiser le `ping` avec Wireshark

Vous utiliserez les IPs suivantes :
g

| Machine           | Adresse IP    |
| ----------------- | ------------- |
| `node1.tp1.efrei` | `10.1.1.1/24` |
| `node2.tp1.efrei` | `10.1.1.2/24` |

🌞 **Déterminer l'adresse MAC de vos deux machines**

```
PC1> sh ip

NAME        : PC1[1]
IP/MASK     : 10.1.1.1/24
GATEWAY     : 0.0.0.0
DNS         :
MAC         : 00:50:79:66:68:00
LPORT       : 10006
RHOST:PORT  : 127.0.0.1:10007
MTU:        : 1500
```

```

PC2> sh ip

NAME        : PC2[1]
IP/MASK     : 10.1.1.2/24
GATEWAY     : 0.0.0.0
DNS         :
MAC         : 00:50:79:66:68:01
LPORT       : 10008
RHOST:PORT  : 127.0.0.1:10009
MTU:        : 1500
```

🌞 **Définir une IP statique sur les deux machines**

```|
ip 10.1.1.1/24
Checking for duplicate address...
PC1 : 10.1.1.1 255.255.255.0

PC1> save
Saving startup configuration to startup.vpc
.  done

PC1> sh ip

NAME        : PC1[1]
IP/MASK     : 10.1.1.1/24
GATEWAY     : 0.0.0.0
DNS         :
MAC         : 00:50:79:66:68:00
LPORT       : 10006
RHOST:PORT  : 127.0.0.1:10007
MTU:        : 1500

PC1>

```

```
ip 10.1.1.2/24
Checking for duplicate address...
sPC1 : 10.1.1.2 255.255.255.0

PC2> save
Saving startup configuration to startup.vpc
.  done

PC2> sh ip

NAME        : PC2[1]
IP/MASK     : 10.1.1.2/24
GATEWAY     : 0.0.0.0
DNS         :
MAC         : 00:50:79:66:68:01
LPORT       : 10008
RHOST:PORT  : 127.0.0.1:10009
MTU:        : 1500

PC2>


```

🌞 **Effectuer un `ping` d'une machine à l'autre**

```
ping 10.1.1.2
84 bytes from 10.1.1.2 icmp_seq=1 ttl=64 time=0.801 ms
84 bytes from 10.1.1.2 icmp_seq=2 ttl=64 time=1.383 ms
84 bytes from 10.1.1.2 icmp_seq=3 ttl=64 time=1.165 ms
84 bytes from 10.1.1.2 icmp_seq=4 ttl=64 time=0.873 ms
84 bytes from 10.1.1.2 icmp_seq=5 ttl=64 time=0.915 ms

PC1>

```
```
PC2> ping 10.1.1.1
84 bytes from 10.1.1.1 icmp_seq=1 ttl=64 time=1.144 ms
84 bytes from 10.1.1.1 icmp_seq=2 ttl=64 time=0.739 ms
84 bytes from 10.1.1.1 icmp_seq=3 ttl=64 time=0.764 ms
84 bytes from 10.1.1.1 icmp_seq=4 ttl=64 time=1.137 ms
84 bytes from 10.1.1.1 icmp_seq=5 ttl=64 time=1.271 ms

PC2>

```
🌞 **Wireshark !**

````
le protocole utilisé est ICMP

````

🌞 **ARP**

PC1> ping 10.1.1.2
84 bytes from 10.1.1.2 icmp_seq=1 ttl=64 time=0.797 ms
84 bytes from 10.1.1.2 icmp_seq=2 ttl=64 time=1.207 ms
84 bytes from 10.1.1.2 icmp_seq=3 ttl=64 time=0.789 ms
84 bytes from 10.1.1.2 icmp_seq=4 ttl=64 time=1.042 ms
84 bytes from 10.1.1.2 icmp_seq=5 ttl=64 time=1.049 ms

PC1> show arp

````
00:50:79:66:68:01  10.1.1.2 expires in 111 seconds
````


# II. Ajoutons un switch

![Topologie n°2](./img/topo2.png)

> *VM Linux ou VPCS, libre à vous !*

Un switch vient résoudre une problématique simple : comment relier + de deux machines entre elles ? Le switch agit comme une simple multiprise réseau (dans son fonctionnement basique du moins ; un switch moderne possède des fonctionnalités plus avancées).

Le switch n'est pas vraiment un membre du réseau : il n'a pas d'adresse IP, on ne peut pas directement lui envoyer de message. Il se charge simplement de faire passer les messages d'une machine à une autre lorsqu'elles souhaitent discuter.

On va commencer simple : GNS3 fournit des switches tout nuls, prêts à l'emploi.

> *Vous pouvez réutiliser les machines de la partie précédente.*

| Machine           | Adresse IP    |
| ----------------- | ------------- |
| `node1.tp1.efrei` | `10.1.1.1/24` |
| `node2.tp1.efrei` | `10.1.1.2/24` |
| `node3.tp1.efrei` | `10.1.1.3/24` |

🌞 **Déterminer l'adresse MAC de vos trois machines**
```
PC1> sh ip

NAME        : PC1[1]
IP/MASK     : 10.1.1.1/24
GATEWAY     : 0.0.0.0
DNS         :
MAC         : 00:50:79:66:68:00
LPORT       : 10006
RHOST:PORT  : 127.0.0.1:10007
MTU:        : 1500
```

```

PC2> sh ip

NAME        : PC2[1]
IP/MASK     : 10.1.1.2/24
GATEWAY     : 0.0.0.0
DNS         :
MAC         : 00:50:79:66:68:01
LPORT       : 10008
RHOST:PORT  : 127.0.0.1:10009
MTU:        : 1500
```
```
PC3> show ip

NAME        : PC3[1]
IP/MASK     : 0.0.0.0/0
GATEWAY     : 0.0.0.0
DNS         :
MAC         : 00:50:79:66:68:00
LPORT       : 20005
RHOST:PORT  : 127.0.0.1:20006
MTU         : 1500

```

🌞 **Définir une IP statique sur les trois machines**

```
PC3> ip 10.1.1.3/24
Checking for duplicate address...
save
PC3 : 10.1.1.3 255.255.255.0

PC3> save
Saving startup configuration to startup.vpc
.  done

PC3> sh ip````

NAME        : PC3[1]
IP/MASK     : 10.1.1.3/24
GATEWAY     : 0.0.0.0
DNS         :
MAC         : 00:50:79:66:68:00
LPORT       : 20005
RHOST:PORT  : 127.0.0.1:20006
MTU         : 1500

PC3>

````

```|
ip 10.1.1.1/24
Checking for duplicate address...
PC1 : 10.1.1.1 255.255.255.0

PC1> save
Saving startup configuration to startup.vpc
.  done

PC1> sh ip

NAME        : PC1[1]
IP/MASK     : 10.1.1.1/24
GATEWAY     : 0.0.0.0
DNS         :
MAC         : 00:50:79:66:68:00
LPORT       : 10006
RHOST:PORT  : 127.0.0.1:10007
MTU:        : 1500

PC1>

```

```
ip 10.1.1.2/24
Checking for duplicate address...
sPC1 : 10.1.1.2 255.255.255.0

PC2> save
Saving startup configuration to startup.vpc
.  done

PC2> sh ip

NAME        : PC2[1]
IP/MASK     : 10.1.1.2/24
GATEWAY     : 0.0.0.0
DNS         :
MAC         : 00:50:79:66:68:01
LPORT       : 10008
RHOST:PORT  : 127.0.0.1:10009
MTU:        : 1500

PC2>


```


🌞 **Effectuer des `ping` d'une machine à l'autre**

```
PC1> ping 10.1.1.2

84 bytes from 10.1.1.2 icmp_seq=1 ttl=64 time=1.132 ms
84 bytes from 10.1.1.2 icmp_seq=2 ttl=64 time=1.447 ms
84 bytes from 10.1.1.2 icmp_seq=3 ttl=64 time=0.470 ms
84 bytes from 10.1.1.2 icmp_seq=4 ttl=64 time=2.007 ms
84 bytes from 10.1.1.2 icmp_seq=5 ttl=64 time=1.056 ms

PC1>

PC2> ping 10.1.1.3

84 bytes from 10.1.1.3 icmp_seq=1 ttl=64 time=0.413 ms
84 bytes from 10.1.1.3 icmp_seq=2 ttl=64 time=0.623 ms
84 bytes from 10.1.1.3 icmp_seq=3 ttl=64 time=0.426 ms
84 bytes from 10.1.1.3 icmp_seq=4 ttl=64 time=0.712 ms
84 bytes from 10.1.1.3 icmp_seq=5 ttl=64 time=0.539 ms

PC2>

PC3> ping 10.1.1.1

84 bytes from 10.1.1.1 icmp_seq=1 ttl=64 time=0.349 ms
84 bytes from 10.1.1.1 icmp_seq=2 ttl=64 time=0.625 ms
84 bytes from 10.1.1.1 icmp_seq=3 ttl=64 time=0.770 ms
84 bytes from 10.1.1.1 icmp_seq=4 ttl=64 time=0.534 ms
84 bytes from 10.1.1.1 icmp_seq=5 ttl=64 time=2.409 ms

PC3>
```


# III. Serveur DHCP



🌞 **Donner un accès Internet à la machine `dhcp.tp1.efrei`**

```
[root@localhost ~]$ ping google.com
PING google.com (216.58.214.174) 56(84) bytes of data.
64 bytes from mad01s26-in-f174.1e100.net (216.58.214.174): icmp_seq q = 1 ttl I = 63 time 37.4 ms
64 bytes from par10s42-in-f14.1e100.net (216.58.214.174): icmp_seq=2 ttl I = 63 time=28.7 ms
```

🌞 **Installer et configurer un serveur DHCP**

```
dnf-y install dhcp-server
```
```
vi /etc/dhcp/dhcpd.conf
```
```
authoritative;
subnet 10.1.1.0 netmask 255.255.255.0 {
	range 10.1.1.10 10.1.1.50;
	option broadcast-address 10.1.1.1;
	option routers 10.1.1.1;
}
```
```
systemctl enable --now dhcpd
```


🌞 **Récupérer une IP automatiquement depuis les 3 nodes**

```
PC1> dhcp
DORA IP 10.1.1.10/24 GW 10.1.1.1

PC1> show ip

NAME        : PC1[1]
IP/MASK     : 10.1.1.10/24
GATEWAY     : 10.1.1.1
DNS         : 
DHCP SERVER : 10.1.1.253
DHCP LEASE  : 563, 570/285/498
MAC         : 00:50:79:66:68:01
LPORT       : 20007
RHOST:PORT  : 127.0.0.1:20008
MTU         : 1500
```

```
PC2> dhcp
DORA IP 10.1.1.11/24 GW 10.1.1.1

PC2> show ip

NAME        : PC2[1]
IP/MASK     : 10.1.1.11/24
GATEWAY     : 10.1.1.1
DNS         : 
DHCP SERVER : 10.1.1.253
DHCP LEASE  : 594, 599/299/524
MAC         : 00:50:79:66:68:00
LPORT       : 20009
RHOST:PORT  : 127.0.0.1:20010
MTU         : 1500
```

```
PC3> dhcp
DORA IP 10.1.1.12/24 GW 10.1.1.1

PC3> show ip

NAME        : PC3[1]
IP/MASK     : 10.1.1.12/24
GATEWAY     : 10.1.1.1
DNS         : 
DHCP SERVER : 10.1.1.253
DHCP LEASE  : 584, 599/299/524
MAC         : 00:50:79:66:68:02
LPORT       : 20011
RHOST:PORT  : 127.0.0.1:20012
MTU         : 1500
```


🌞 **Wireshark !**

```
voir capture_dhcp_wireshark
```

## 2. DHCP spoofing
➜ **Introduire une nouvelle VM dans la topologie, OS de votre choix**

🌞 **Configurez dnsmasq**

```
dnf install -y dnsmasq
```
```
dhcp-range=10.1.1.210 10.1.1.250 255.255.255.0 8h
dhcp-authoritative
interface=enp0s3
```

🌞 **Test !**

```
PC1> dhcp
DORA IP 10.1.1.210/24 GW 10.1.1.1

PC1> show ip

NAME        : PC1[1]
IP/MASK     : 10.1.1.210/24
```


🌞 **Now race !**

```
ne sachant pas quoi mettre, je met les résultats des statistiques sur 3 vps que j'ai éteins puis rallumé 5 fois :

pc1: 5fois le bon dhcp
pc2 : 3 fois el bon dhcp, 2 fois le mauvais
pc3: pareil 

```

🌞 **Wireshark !**

- capturez cette course :d
- je veux une capture wireshark `race.pcap` qui montre les deux serveurs DHCP qui répondent

> *Si on met vraiment l'attaque en place et qu'on veut gagner la course, on n'hésite pas à mettre un coup de batte dans les jambes du concurrent : essayer de ralentir le serveur DHCP légitime. On abordera ça plus tard maybe, je vous invite fort à faire des recherches sur le sujet : comment ralentir une machine qu'on peut joindre sur le réseau, et spécifiquement un serveur DHCP ici. Aussi on remettra en place l'attaque dans des scénarios où on usurpe aussi l'identité du routeur.*

## 3. BONUS : DHCP starvation

Une attaque très débile et simple à mettre en place pour **DOS l'accès à un LAN** s'il n'y a pas de protections particulières. **C'est naze, mais c'est là** :d

Le principe est simple : **faire de multiples échanges DORA avec le serveur DHCP pour récupérer toutes les IP disponibles dans le réseau.**

On usurpe une adresse MAC (qu'elle existe ou non), on demande une adresse IP, on la récupère (merci). On répète l'opération avec une nouvelle fake adresse MAC, une nouvelle IP (merci). Etc. Jusqu'à épuiser toutes les adresses de la range.

Il existe des tools pour faire ça, vous pouvez aussi essayer (recommandé) de le **coder vous-mêmes avec Scapy** (une dinguerie cette lib) : on peut forger à peu près tout et n'importe quoi comme trame, et très facilement, avec Scapy.