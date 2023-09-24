# Exercice 2 – Installation et gestion d'un serveur Linux

### Informations
- Évaluation : formative
- Type de travail : individuel
- Durée : 3 heures
- Système d'exploitation : Linux Ubuntu client et serveur 22.04
- Environnement : virtuel, vsphere.

### Objectifs

Cet exercice a pour objectifs :  

- D'installer un environnement de test.  
- Pouvoir ajouter des disques dans LVM.

Dans cet exercice, vous allez vous installer un environnement de test. Vous allez utiliser un serveur Linux Ubuntu. Vous allez également faire la gestion des disques LVM.

La section sur les disques LVM se fait sur le client et sur le serveur.

## Pour vérification

Vous devez remettre un document Word contenant les vérifications demandées.

##### Pour la section de l'installation du serveur  

Vous devez remettre dans votre document un comparatif entre la VM cliente et la VM serveur au niveau suivant :  

| Item |    Valeur Client   | Valeur serveur|
| ---- |    -------------   | ---------------|
|Processeur |           |
|Mémoire vive |  |
|Nb de processus |  |
|% utilisation des partitions|  |

De plus, vous devez mettre dans votre document une capture d’écran de votre serveur avec un shell d’ouvert ayant les commandes suivantes exécutées à l'intérieur :

```bash
# Les informations à l'ouverture de la session 
git version
docker --version
docker compose version
```

##### Pour la section de la gestion des disques LVM 

Remettre dans votre document une capture d’écran des trois commandes suivantes, et ce pour les deux VMS.

```bash
sudo pvs 
sudo vgs 
sudo lvs 
```

## Section 1 Installation d'un serveur Linux

### Partie 1 : Installation du serveur
Dans cette partie, vous allez installer un serveur Ubuntu selon les spécifications données.

#### Étape 1 : Installation
a - En utilisant l’ISO ubuntu-22.04-live-server-amd64.iso, créez une machine virtuelle selon les spécifications suivantes :

    Dossier dans vSphere : DFC DS/VM DFC/A23_4397_420W45_ISS_CR/
    Nom de la VM : A23_4397_420W45_Ub_srv_Initiale_#Matricule
 	Storage (disque vSphere) : ESXDFC2
    CPU : 2
    Mémoires : 2 Go
    Disque dur : 2 disques, 20 Go chacun en **partitionnement dynamique (Thin provision)**
    Carte réseau : VM DFC2 (réseau 10.100.2.0)
    CD/DVD : ISO ubuntu-22.04-live-server-amd64.iso
    

b -	Une fois la VM créée, lancez la VM et installez le serveur Ubuntu selon les spécifications suivantes :
    
    Pour vous déplacer dans les fenêtres, utiliser votre touche tabulation. Pour sélectionner un item, appuyez sur la touche Entrée(enter). Pour cocher un item cliqué sur la barre d'espacement.

    - Clavier : French (Canada) ou English (US)
    - Type d’installation : Ubuntu Server
    - Connexions réseau : DHCPv4
    - Configurer le proxy : Laissez vide et cliquer Terminé.
    - Miroir d'archive Ubuntu : ne rien changer et cliquer sur Terminé.

    - Configuration de stockage guidée :
        Utiliser un disque entier
             Laisser "Set up this disk as an LMV group"

            Confirmer l'action même s’il est en rouge.

    - Configuration profil :
        
        Attention pour le nom d'utilisateur et mot de passe je vous recommande d'utiliser le même que votre compte principal sur votre client.

        Votre nom : ;-)
        Le nom de la machine : srv-web-[matricule]
        Nom d'utilisateur : à votre choix
        Mot de passe : à votre choix

	- Configuration SHH : Cochez Installer le serveur OpenSSH (taper sur la barre d'espace)  
        N'importez pas la clé SSH.  

	-  Featured Server Snaps ne cocher rien et choisit Terminé

	- Patientez! L'INSTALLATION EST EN COURS.
 
 
Redémarrez votre serveur&nbsp;:

```
reboot
```

### Partie 2 : Première utilisation de votre machine

Démarrez votre VM et connectez-vous.

#### Mise à jour de votre Ubuntu :

Faites une mise à jour de votre système : 

```
sudo apt update && sudo apt full-ugrade -y
```

Il se peut qu'une fenêtre vous propose de redémarrer des services. Laissez les services déjà cochés et continuez. Enfin, redémarrez votre serveur&nbsp;:

```
Reboot now
```


#### Vérification des partitions et du système

- Ouvrez un terminal et tapez les commandes demandées :

```
df -h
```

**Remise** : Prenez en note la valeur importante pour votre remise.

Ici il y a deux partitions qui nous intéressent davantage :

```bash
# La partition LVM probablement : 
/dev/mapper/vgubuntu--vg-ubuntu--lv
# La partition de boot/efi probablement :
/dev/sda2
```

Remarquer les valeurs d'utilisation.


#### Gestion des partitions, la commande lsblk :

Un périphérique de bloc est un fichier faisant référence à un périphérique. Les périphériques peuvent être des disques durs, des disques SDD, des disques RAM, etc. Les fichiers de périphérique de bloc se trouvent dans le répertoire /dev.

-  Entrer la commande suivante, et avec la page `man` vérifier les informations données sur la commande.

```bash
lsblk
# sda, ce sont les données sur votre premier disque dur.

# sdb, ce sont les données sur votre deuxième disque dur non utilisé. Nous allons le configurer dans un autre exercice.
```
![Lsblk](images/lsblk-srv.png)



#### Mémoire RAM, processeur et processus

Entrez la commande <code>top</code>.

```
top 
```

**Remise** : Prenez en note la valeur importante pour votre remise.

Pour quitter <code>top</code> entrer <code>q</code>.

### Partie 3 : Ajout de logiciels

Dans cette partie, vous allez ajouter les logiciels wget, curl, git et Docker.


#### Étape 1 : Ajout de logiciels de base

a- Nous allons installer les outils de base wget, curl et git et vim.

```bash
sudo apt install wget curl git vim -y
```

#### Étape 2: Ajout de Docker
a.	Il existe plusieurs manières d’installer Docker. Nous allons utiliser le script officiel de Docker pour l’installer (vous pouvez consulter le script à https://get.docker.com).

```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh
sudo usermod -aG docker VotreNomUtilisateur
sudo docker version 
```

Avec ces commandes, vous avez installé Docker, vous avez ajouté votre utilisateur au groupe Docker (ça évite de toujours utiliser sudo devant vos commandes Docker, par contre ce n’est pas une bonne habitude de sécurité) et vous avez vérifié que Docker est bien installé.

Vous devez relancer votre session  pour que votre utilisateur soit inclus dans le groupe docker.


### Lancez les commandes nécessaires à votre remise :

```bash
# Les informations à l'ouverture de la session 
git version
docker --version
docker compose version
```

## Section 2 Gestion des disques LVM

**Attention :** je vous rappelle que cette section se fait sur le client et sur le serveur.

### Partie 1 : Vérification de vos disques

Vérifier l'état de votre système avant de débuter.  

Les commandes suivantes permettent de garder une trace des informations.  

```bash
echo --- Avant modification --- > FichierDesTraces.txt
date >> FichierDesTraces.txt
df -H >> FichierDesTraces.txt
lsblk >> FichierDesTraces.txt
cat /etc/fstab >> FichierDesTraces.txt
```

### Partie 2 : État du stokage LVM

Entrer les commandes suivantes pour vérification et garder une trace des informations :

```bash
echo --- Avant modification --- > FichierLVM.txt
date >> FichierLVM.txt
sudo pvs >> FichierLVM.txt
sudo vgs >> FichierLVM.txt
sudo lvs >> FichierLVM.txt
```

### Partie 3 : Modification de votre stokage LVM

D'abord il faut ajouter le disque <code>sdb</code> dans le "physiqual volume" : 

```bash
sudo pvcreate /dev/sdb # Si sdb est bien le nouveau disque vérifier avec les commandes de la partie 2
sudo pvs
```

Par la suite, nous pouvons ajouter le disque dans le "volume group".

Pour le client :

```bash
sudo vgs
sudo vgextend vgubuntu /dev/sdb # Attention: prenez le nom que la commande sudo vgs vous a renvoyé pour avoir le bon nom de disque.
sudo vgs  
```

Pour le serveur :

```bash
sudo vgs
sudo vgextend ubuntu-vg /dev/sdb # Attention: prenez le nom que la commande sudo vgs vous a renvoyé pour avoir le bon nom de disque.
sudo vgs  
```

Maintenant, nous allons étendre le "logical volume".

Pour le client :

```bash
df -h
sudo lvextend -l +100%FREE /dev/mapper/vgubuntu-root # ici on utilise le nom complet de la partition logique renvoyé par la commande df.
```

Pour le serveur :

```bash
df -h
sudo lvextend -l +100%FREE /dev/mapper/ubuntu--vg-ubuntu--lv # ici on utilise le nom complet de la partition logique renvoyé par la commande df.
```

Vérifier le résultat et placer l'information dans vos fichiers : 

#### Vérification de vos disques

```bash
echo --- Après modification --- >> FichierDesTraces.txt
date >> FichierDesTraces.txt
df -H >> FichierDesTraces.txt
lsblk >> FichierDesTraces.txt
cat /etc/fstab >> FichierDesTraces.txt
```

#### État du stokage LVM

```bash
echo --- Après modification --- > FichierLVM.txt
date >> FichierLVM.txt
sudo pvs >> FichierLVM.txt
sudo vgs >> FichierLVM.txt
sudo lvs >> FichierLVM.txt
```

### Partie 4: Ajuster votre fichier /etc/fstab

Débutons par une vérification de l'état du fichier <code>/etc/fstab</code> avec la commande <code>df</code> :

```bash
df -H 
```

Remarqué l'entré pour votre partition lvm dont le point de montage est <code>/</code> (root). C'est-à-dire celle que nous avons étendue. Elle n'est pas étendue.

Pour que le système de fichiers utilise la totalité des de l'espace disponible, exécutez la commande : <code>sudo resize2fs /dev/VG1/LV1</code>

Sur le client :

```bash
sudo resize2fs /dev/mapper/vgubuntu-root #Le nom de la partition monté dans le fstab.
df -H
```

Sur le serveur :

```bash
sudo resize2fs /dev/mapper/ubuntu--vg-ubuntu--lv #Le nom de la partition monté dans le fstab.
df -H
```

Le <code>resize2fs</code> est un utilitaire en ligne de commande qui vous permet de redimensionner des systèmes de fichiers ext2, ext3 ou ext4. 

Note : L'extension d'un système de fichiers est une opération à risque modéré. Il est donc recommandé de sauvegarder l'intégralité de votre partition pour éviter toute perte de données.
 
### Pour vérification
Remettre une capture d’écran des trois commandes suivantes, et ce pour les deux VMS (dans le fichier Word de remise).

```bash
sudo pvs 
sudo vgs 
sudo lvs 
```


## Compétences développées


00Q1 - Effectuer l’installation et la gestion d’ordinateur :

    2 installer le système d’exploitation.
    3 Installer des applications
    4 Effectuer des tâches de gestion du système d’exploitation.

00SF - Évaluer des composants logiciels et matériels.

    1 Rechercher des composants logiciels et matériels.
    2 Formuler des avis sur les composants logiciels et matériels.

Note : les compétences sont développées en partie.

## Références
- Ubuntu : https://ubuntu.com/download/desktop
- LVM : https://access.redhat.com/documentation/fr-fr/red_hat_enterprise_linux/6/html/logical_volume_manager_administration/index
- zsh : http://https://kifarunix.com/install-and-setup-zsh-and-oh-my-zsh-on-ubuntu-20-04/ 
- Formater un disque dur : https://lecrabeinfo.net/formater-un-disque-dur-ssd-cle-usb-sur-ubuntu-linux.html#methode-n2-avec-gnome-disques
- LVM : https://access.redhat.com/documentation/fr-fr/red_hat_enterprise_linux/6/html/logical_volume_manager_administration/index

