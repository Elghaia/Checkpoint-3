### Partie 1 : Gestion des utilisateurs

**Q.2.1.1** Sur le serveur, créer un compte pour ton usage personnel.

![Pasted image 20250117104237](https://github.com/user-attachments/assets/41017582-6357-4446-a53f-2a74a8c4444f)


**Q.2.1.2** Quelles préconisations proposes-tu concernant ce compte ?
- **Mot de passe fort** : Utiliser des mots de passe complexes et uniques.
- **Authentification SSH** : Désactiver les mots de passe, utiliser des clés SSH.
- **Utilisation de `sudo`** : Privilégier `sudo` plutôt que l'accès direct en `root`.
- **Journalisation** : Configurer la journalisation pour tracer les actions système.

### Partie 2 : Configuration de SSH

**Q.2.2.1** Désactiver complètement l'accès à distance de l'utilisateur root.

![Pasted image 20250117111113](https://github.com/user-attachments/assets/eb8b4445-bfff-425e-bf95-67dd396cb2a0)
![Pasted image 20250117112227](https://github.com/user-attachments/assets/209b3cd6-3e46-4746-85c4-835d9b412f04)


**Q.2.2.2** Autoriser l'accès à distance à ton compte personnel uniquement.
![Pasted image 20250117130428](https://github.com/user-attachments/assets/dd63a743-9bf2-4058-b154-0a2f896fe6c6)
![Pasted image 20250117112214](https://github.com/user-attachments/assets/7a4b3d9d-3f84-48fe-a204-d4093436970b)


**Q.2.2.3** Mettre en place une authentification par clé valide 

![Pasted image 20250117111543](https://github.com/user-attachments/assets/453181f5-9faa-43fc-92bb-65a5bd162408)
![Pasted image 20250117111719](https://github.com/user-attachments/assets/dd84c7e5-f05b-4c32-96b0-870340cbba23)
![Pasted image 20250117111850](https://github.com/user-attachments/assets/a3134eb5-762d-4426-aee5-051a6be9ab05)


**Q.2.2.3** Désactiver l'authentification par mot de passe

![Pasted image 20250117112058](https://github.com/user-attachments/assets/a3f2424b-b674-4b38-b4f9-a86685c62838)


### Partie 3 : Analyse du stockage

Q.2.3.1 Quels sont les systèmes de fichiers actuellement montés ?

![Pasted image 20250117112322](https://github.com/user-attachments/assets/8cb86111-092c-4b5e-9829-e662d22dcdff)



- **udev** monté sur `/dev`
- **tmpfs** monté sur `/run`
- **/dev/mapper/cp3--vg-root** monté sur `/`
- **tmpfs** monté sur `/dev/shm`
- **tmpfs** monté sur `/run/lock`
- **/dev/md0p1** monté sur `/boot`
- **tmpfs** monté sur /run/user/1001

Q.2.3.2 Quel type de système de stockage ils utilisent ?

![Pasted image 20250117112423](https://github.com/user-attachments/assets/f97dc21d-c4e4-405e-8b53-6b6fc755f624)

Le système utilise principalement un **RAID 1** 
Ce RAID 1 est configuré avec `md0` et ses partitions (`md0p1`, `md0p2`, `md0p5`) et le stockage logique est géré par LVM.

**Q.2.3.3** Ajouter un nouveau disque de 8,00 Gio au serveur et réparer le volume RAID



![Pasted image 20250117112616](https://github.com/user-attachments/assets/0c049bec-b252-4ca0-8759-ef88630c2ea7)


![Pasted image 20250117120647](https://github.com/user-attachments/assets/c7e277d5-67a7-4299-be4c-361a0be8c754)
![Pasted image 20250117120748](https://github.com/user-attachments/assets/e1c1d868-207e-4a46-9f77-c86697dad2cc)
![Pasted image 20250117120820](https://github.com/user-attachments/assets/ea455de7-5167-4241-9d6d-4a81faf01eaf)
![Pasted image 20250117120938](https://github.com/user-attachments/assets/fa1af137-5dbd-4ee7-85e9-bbcc6f3c68d3)



**Q.2.3.4** Ajouter un nouveau volume logique LVM de 2 Gio qui servira à héberger des sauvegardes. Ce volume doit être monté automatiquement à chaque démarrage dans l'emplacement par défaut : `/var/lib/bareos/storage`.

![Pasted image 20250117124456](https://github.com/user-attachments/assets/a9ddbc65-8e90-4225-87a2-47f95a5f2701)

![Pasted image 20250117124615](https://github.com/user-attachments/assets/d3d90742-61c3-4592-a3e9-0589dcb702d7)


**Q.2.3.5** Combien d'espace disponible reste-t-il dans le groupe de volume ?

![Pasted image 20250117124710](https://github.com/user-attachments/assets/77a9a960-7f1a-486b-abbc-accd122ac98b)


D'après la sortie de la commande `vgs` il  reste **1,79 Gio** d'espace libre dans le groupe de volumes `cp3-vg`.

### Partie 4 : Sauvegardes

**Q.2.4.1** Expliquer succinctement les rôles respectifs des 3 composants bareos installés sur la VM.

- **bareos-dir** : Gère les sauvegardes, coordonne les tâches entre les autres composants.
- **bareos-sd** : Gère le stockage des données de sauvegarde.
- **bareos-fd** : Collecte et transmet les données à sauvegarder depuis les machines clientes.

  
### Partie 5 : Filtrage et analyse réseau


**Q.2.5.1** Quelles sont actuellement les règles appliquées sur Netfilter ?


![Pasted image 20250117121608](https://github.com/user-attachments/assets/90fdeaa0-9fa1-404c-a206-da26edf714ba)


**Q.2.5.2 : Quels types de communications sont autorisées ?**

Les communications autorisées actuellement sont les suivantes :

- Les connexions déjà établies ou liées à des connexions existantes
- Les connexions provenant de l'interface loopback de la machine.
- Le trafic sur le port 22 donc SSH
- Les paquets ICMP 
- Le trafic ICMPv6.

**Q.2.5.3 : Quels types sont interdits ?**

Tous les autres types de communications que ceux spécifiés par les règles sont interdits. 

**Q.2.5.4 : Ajouter les règles nécessaires pour autoriser Bareos à communiquer avec les clients Bareos sur le réseau local.**

commande : sudo nft add rule inet inet_filter_table input in_chain ip saddr 192.168.1.0/24 tcp dport {9101, 9102, 9103} accept
![Pasted image 20250117123134](https://github.com/user-attachments/assets/334b0144-aa33-41a0-8865-ddeaa80507fe)


### Partie 6 : Analyse de logs

**Q.2.6.1 Lister les 10 derniers échecs de connexion ayant eu lieu sur le serveur en indiquant pour chacun :**

commande : `sudo grep 'Failed password' /var/log/auth.log | tail -n 10`
![Pasted image 20250117121949](https://github.com/user-attachments/assets/b24a8da0-88bf-4906-ad31-358f0974d2e8)






