# Examen-Administration-syst-me-16-janvier-2026

## Les deux familles

Quelles sont les principales distributions de la famille Debian, de la famille Red Hat, et les autres ?

Comment pourriez-vous décrire, succintement, leurs positionnements respectifs ? Les différences au niveau des outils d'admnistration ?

### Famille Debian
- Debian
- Ubuntu
- Linux Mint
- Kali Linux

Outils : `apt`, `dpkg`

Positionnement :
- Stabilité, simplicité, énorme documentation
- Ubuntu est plus orienté grand public / entreprise

### Famille Red Hat
- Red Hat Enterprise Linux (RHEL)
- Rocky Linux / AlmaLinux
- CentOS Stream
- Fedora

Outils : `dnf`, `rpm`

Positionnement :
- Monde entreprise, serveurs professionnels
- Fedora = laboratoire de nouveautés

### Autres
- Arch (rolling release, pacman)
- SUSE / OpenSUSE (zypper)
- Gentoo (compilation depuis sources)

### Différences outils d'administration
- Paquets : apt vs dnf
- Configs proches
- Services : presque partout `systemd`


## Back to basics 

Quelle est la différence fondamentale entre du code qui tourne en mode kernel/superviseur et en mode utilisateur ?

## Différence kernel / user

- Mode kernel/superviseur : accès total au matériel
- Mode user : accès restreint et protégé
- Séparation = stabilité + sécurité


## Qui est où ?

Indiquez, en quelques mots, pour ces répertoires, ce qu'ils sont supposés contenir :

- `/etc` : configuration système
- `/bin` et `/usr/bin` : programmes utilisateur
- `/sbin` et `/usr/sbin` : programmes administrateur
- `/home` : dossiers utilisateurs
- `/var` : données variables
- `/var/log` : logs
- `/var/lib` : données internes des services


## Où est le pilote ?

Comment retrouver, sous GNU/Linux :

- La liste des pilotes chargées en mémoire ?   lsmod

- La liste des fichiers binaires _(modules)_ qui les fournissent ?    /lib/modules/$(uname -r)/

- Forcer leur chargement et leur déchargement ?
  Chargement : modprobe nom_module
  Déchargement : modprobe -r nom_module

- Les messages qu'ils ont émis ?
  
  dmesg : affiche les messages du noyau Linux stockés en mémoire, notamment ceux émis par les pilotes lors de leur chargement, de la détection du matériel ou en cas d’erreur.
  
  journalctl -k : affiche les logs du noyau enregistrés par systemd-journald, y compris les messages des pilotes, avec horodatage et historique persistant entre les redémarrages.


## MS Windows

Comment fonctionne en pratique WSL _(Windows Subsystem for Linux)_ ? Que permet-il de faire ? Quels protocoles utilise-t-il ?

### Fonctionnement

- WSL1 : traduction des appels système Linux en appels Windows
- WSL2 : vraie machine virtuelle Linux avec vrai noyau Linux

### Permet :

- Utiliser outils Linux natifs sous Windows
- Développement, scripts, serveurs locaux, Docker, etc.

### Technologies / protocoles :

- Hyper-V
- Réseau virtuel
- Partage fichiers via 9p / intégration FS Windows/Linux


## On commence

Vous venez d'installer un système GNU/Linux que faites-vous pour en sécuriser les accès via SSH ?

- Interdire root :
PermitRootLogin no

- Forcer clés SSH :
PasswordAuthentication no

- Restreindre utilisateurs :
AllowUsers alice bob

- Activer un firewall


## Alerte !

Vous vous connectez à un système GNU/Linux, le client ssh vous averti qu'il a un problème de reconnaissance de sa clef publique
d'hôte. Que faites-vous ? Pourquoi ?

Cause possible :
- Attaque Man-In-The-Middle
- Serveur réinstallé
- Clé changée

Vérifier l’empreinte de la clé

Si légitime, supprimer l’ancienne : ~/.ssh/known_hosts


## Oh les gourmands !

Comment identifier rapidement, avec une seule ligne de commande, les utilisateurs qui consomment le plus d'espace de stockage.
_N.B._ Vous pouvez supposer que les répertoires personnels sont tous des sous-répertoires de `/home` 

Avec la commande : du -sh /home/* | sort -h

La commane va calculé la taille totale de chaque répertoire personnel dans /home, puis trié les résultats par taille croissante, ce qui permetra d’identifier rapidement quels utilisateurs occupent le plus d’espace disque.


## On va aider les devs...

Une équipe de développeur.se.s vous demande de préparer un système GNU/Linux pour du développent système (C, make, etc.)

Que faites-vous en pratique en tant qu'administrateur.trice pour commencer ?

- Sur une distribution de la famille Debian :
   
  apt install build-essential make cmake gdb git

- Sur une distribution de la famille Red Hat :
  
  dnf groupinstall "Development Tools"
  
  dnf install gcc gcc-c++ make cmake gdb git



## Qui fait quoi ?

Comment pourriez-vous retrouver la liste des dernières connexions d'utilisateurs à un système GNU/Linux, autant à distance que localement ?
Avec la commande : last et ou lastlog


Comment identifier les sessions où un.e utilisateur.trice à utilisé _sudo_ pour avoir les droit d'administration ?

Avec :
  - grep sudo /var/log/auth.log
  - journalctl _COMM=sudo


## Post mortem

Le système que vous administrez est tombé cette nuit. Comment récupérer des informations sur ce qui s'est passé ?
- journalctl -b -1 : affiche les journaux du système du démarrage précédent, ce qui permet d’analyser ce qui s’est passé avant le crash ou le redémarrage.

- journalctl -p err : affiche uniquement les messages de niveau erreur ou plus critique (erreur, critique, alerte, urgence) pour repérer rapidement les problèmes importants.

- dmesg : affiche les messages du noyau Linux en mémoire, notamment ceux liés au matériel, aux pilotes et aux erreurs bas niveau survenues au démarrage ou en cours de fonctionnement.


## On surveille...

Sur un serveur GNU/Linux qui démarre via _systemd_, un service nommé _bidule_, comment :

- Savoir si le service est lancé au démarrage automatiquement
  
    systemctl is-enabled bidule

- Savoir s'il est en cours de fonctionnement
  
    systemctl status bidule
  
- Faire en sorte qu'il le soit
  
    systemctl enable --now bidule

- Examiner les messages qu'il émet
  
    journalctl -u bidule

- Le redémarrer
  
    systemctl restart bidule

- Déterminer de quel paquet (Debian ou Red Hat) il provient
  
  Debian :
    - dpkg -S $(which bidule)
  
  RedHat :
    - rpm -qf $(which bidule)


## Zut

Vous devez reprendre la main, en tant qu'administrateur, d'un système GNU/Linux mais vous n'avez aucun mot de passe à votre disposition, ni _root_ ni utilisateur. Que pouvez-vous faire ?
Solution :

  - Démarrer en mode recovery / single user
  - Monter la racine en RW
  - Changer le mot de passe :
      passwd


## C'est la faute à Rémy

Un service réseau http(s) ne fonctionne plus. Vous avez accès à ce système comme administrateur.trice. Quels outils utilisez-vous pour diagnostiquer le problème, indiquez précisément ce que chaque outil permet de vérifier.

Outils de diagnostic

### Service :
  - systemctl status apache2   : Affiche l’état du service Apache2 (démarré, arrêté, en erreur…).
  - systemctl status nginx    : Indique si Nginx est actif, en panne ou en cours de redémarrage.

### Logs :
  - journalctl     : Affiche les logs du système gérés par systemd.
  - tail -f /var/log/apache2/error.log    : Affiche en continu les dernières lignes du fichier de logs d’erreur d’Apache.

### Ports :
  - ss -lntup     : Affiche les ports ouverts et les services qui les utilisent.

### Test HTTP :
  - curl http://localhost      : Effectue une requête HTTP vers le serveur local.

### Processus :
  - ps aux     : Liste tous les processus en cours avec :
  - lsof -i     : Liste les fichiers ouverts liés aux connexions réseau (sockets). Permet de savoir quel processus écoute sur quel port, très utile pour le debugging réseau.

### Debug avancé :
  - strace     : Trace les appels système d’un processus. Montre en détail ce que le processus fait : ouverture de fichiers, accès réseau, erreurs système.


## Au charbon !

On vous demande d'installer un serveur de base de données _Elastic_ (autrefois nommé _Elastic Search_) sur un système Debian (ou autre si vous préférez)

Décrivez votre recherche documentaire, la méthode que vous sélectionnez, les commandes que vous exécutez et les fichiers de configuration impliqués.

### Méthode :
  - Lecture documentation officielle Elastic
  - Ajout dépôt officiel
  - Installation du paquet
  - Configuration
  - Démarrage du service

### Fichier principal :
  /etc/elasticsearch/elasticsearch.yml

Commandes :
  - systemctl enable --now elasticsearch     : Crée un lien symbolique pour démarrer Elasticsearch automatiquement au boot.
  - curl http://localhost:9200

## Le Web

Vous avez la charge de gérer un serveur Web sous Debian GNU/Linux, pour héberger plusieurs sites Web (hébergement mutualisé).

- Quels paquets installez-vous ?
    - apt install apache2
    - apt install php php-fpm php-mysql
    - apt install mariadb-server

- Comment organisez sous le stockage des différents contenus ?
    - /var/www/site1
    - /var/www/site2
    - /var/www/site3

    
- Certains sites ont besoin de PHP, comment l'installez-vous ?
    - apt install php php-fpm
  
- Comment pouvez-vous organiser l'enregistrement séparé des visites des différents sites ?
    - CustomLog /var/log/apache2/site1_access.log
    - ErrorLog /var/log/apache2/site1_error.log
  
On peut organiser l’enregistrement séparé des visites en configurant, pour chaque site (VirtualHost), des fichiers de logs distincts (par exemple avec CustomLog et ErrorLog sous Apache, ou access_log et error_log sous Nginx), afin que chaque site écrive ses accès dans ses propres fichiers.
  
- Quel(s) outil(s) peuvent vous permettre de fournir une vison des visites de chaque site à chaque Webmaster ?

  Statistiques de fréquentation :
  
    - AWStats : outil d’analyse de logs web qui génère des rapports statistiques détaillés (visites, pages vues, pays, navigateurs, robots, etc.) à partir des fichiers de journaux du serveur.
    - GoAccess : analyseur de logs web en temps réel qui fournit une vue interactive dans le terminal ou via une interface web sur le trafic, les visiteurs et les performances.
    - Matomo : plateforme complète de web analytics qui fonctionne par suivi via code JavaScript sur les sites (et/ou par analyse de logs) pour fournir des statistiques avancées de fréquentation, de comportement utilisateur et de performance.

