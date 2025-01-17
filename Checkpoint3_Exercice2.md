### Partie 1 : Gestion des utilisateurs

**Q.2.1.1** Sur le serveur, créer un compte pour ton usage personnel.
![[Pasted image 20250117104237.png]]

**Q.2.1.2** Quelles préconisations proposes-tu concernant ce compte ?
- **Mot de passe fort** : Utiliser des mots de passe complexes et uniques.
- **Authentification SSH** : Désactiver les mots de passe, utiliser des clés SSH.
- **Utilisation de `sudo`** : Privilégier `sudo` plutôt que l'accès direct en `root`.
- **Journalisation** : Configurer la journalisation pour tracer les actions système.

### Partie 2 : Configuration de SSH

**Q.2.2.1** Désactiver complètement l'accès à distance de l'utilisateur root.

![[Pasted image 20250117111113.png]]
![[Pasted image 20250117112227.png]]

**Q.2.2.2** Autoriser l'accès à distance à ton compte personnel uniquement.

![[Pasted image 20250117130428.png]]

**Q.2.2.3** Mettre en place une authentification par clé valide 

![[Pasted image 20250117111543.png]]

![[Pasted image 20250117111719.png]]
![[Pasted image 20250117111850.png]]
**Q.2.2.3** Désactiver l'authentification par mot de passe
![[Pasted image 20250117112058.png]]
![[Pasted image 20250117112214.png]]

### Partie 3 : Analyse du stockage

Q.2.3.1 Quels sont les systèmes de fichiers actuellement montés ?

![[Pasted image 20250117112322.png]]
- **udev** monté sur `/dev`
- **tmpfs** monté sur `/run`
- **/dev/mapper/cp3--vg-root** monté sur `/`
- **tmpfs** monté sur `/dev/shm`
- **tmpfs** monté sur `/run/lock`
- **/dev/md0p1** monté sur `/boot`
- **tmpfs** monté sur /run/user/1001

Q.2.3.2 Quel type de système de stockage ils utilisent ?

![[Pasted image 20250117112423.png]]
Le système utilise principalement un **RAID 1** 
Ce RAID 1 est configuré avec `md0` et ses partitions (`md0p1`, `md0p2`, `md0p5`) et le stockage logique est géré par LVM.

**Q.2.3.3** Ajouter un nouveau disque de 8,00 Gio au serveur et réparer le volume RAID

![[Pasted image 20250117112616.png]]


![[Pasted image 20250117120647.png]]


![[Pasted image 20250117120748.png]]

![[Pasted image 20250117120820.png]]
![[Pasted image 20250117120938.png]]

**Q.2.3.4** Ajouter un nouveau volume logique LVM de 2 Gio qui servira à héberger des sauvegardes. Ce volume doit être monté automatiquement à chaque démarrage dans l'emplacement par défaut : `/var/lib/bareos/storage`.

![[Pasted image 20250117124456.png]]
![[Pasted image 20250117124615.png]]


**Q.2.3.5** Combien d'espace disponible reste-t-il dans le groupe de volume ?

![[Pasted image 20250117124710.png]]

D'après la sortie de la commande `vgs` il  reste **1,79 Gio** d'espace libre dans le groupe de volumes `cp3-vg`.

### Partie 4 : Sauvegardes

**Q.2.4.1** Expliquer succinctement les rôles respectifs des 3 composants bareos installés sur la VM.

- **bareos-dir** : Gère les sauvegardes, coordonne les tâches entre les autres composants.
- **bareos-sd** : Gère le stockage des données de sauvegarde.
- **bareos-fd** : Collecte et transmet les données à sauvegarder depuis les machines clientes.
### Partie 5 : Filtrage et analyse réseau


**Q.2.5.1** Quelles sont actuellement les règles appliquées sur Netfilter ?

![[Pasted image 20250117121608.png]]

#### Q.2.5.2 : Quels types de communications sont autorisées ?

Les communications autorisées actuellement sont les suivantes :

- Les connexions déjà établies ou liées à des connexions existantes
- Les connexions provenant de l'interface loopback de la machine.
- Le trafic sur le port 22 donc SSH
- Les paquets ICMP 
- Le trafic ICMPv6.

#### Q.2.5.3 : Quels types sont interdits ?

Tous les autres types de communications que ceux spécifiés par les règles sont interdits. 

Q.2.5.4 : Ajouter les règles nécessaires pour autoriser Bareos à communiquer avec les clients Bareos sur le réseau local.

commande : sudo nft add rule inet inet_filter_table input in_chain ip saddr 192.168.1.0/24 tcp dport {9101, 9102, 9103} accept

![[Pasted image 20250117123134.png]]


### Partie 6 : Analyse de logs

#### Q.2.6.1 Lister les 10 derniers échecs de connexion ayant eu lieu sur le serveur en indiquant pour chacun :

commande : `sudo grep 'Failed password' /var/log/auth.log | tail -n 10`

![[Pasted image 20250117121949.png]]




