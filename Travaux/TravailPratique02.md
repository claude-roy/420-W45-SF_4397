# Travail pratique 2 - Docker

### Informations

- Évaluation : 20 % de la session
- Type de travail : individuel
- Date de remise : Voir sur LÉA
- Durée : 3 heures
- Système d’exploitation : Linux ou Windows
- Environnement : Docker

### Objectifs

- Analyser les différents scénarios de déploiement proposés dans les documents de conception.
- Distinguer correctement les services à installer sur les serveurs.
- Distinguer correctement les services à installer sur le réseau.
- Déterminer un plan des étapes à entreprendre pour installer et configurer les services réseau
- Configurer du système d’exploitation après l’installation.
- Installer des périphériques/composantes pour une connectivité au réseau.
- Configurer des périphériques / composantes pour une connectivité au réseau.
- Gérer des droits d’accès d’un périphérique réseau.
- Utiliser des protocoles sécuritaires.
 

### But

Dans ce travail pratique, vous allez démontrer que vous avez installé un système de conteneurs et que vous êtes capable de créer des conteneurs selon une description.

Principalement, vous allez faire :

- l'installation d'un système de conteneur en respectant la procédure et les recommandations du manufacturier au besoin;
- configurer le système de conteneurs en fonction d’une utilisation sécuritaire;
- vérifier que les éléments installés fonctionnent comme prévu;
- configurer des règles de gestions des accès sécuritaires.

## Remise

Vous devez créer un dépôt GitHub pour votre travail pratique 2.

- Votre dépôt GitHub va contenir : 
    - Un fichier README.md qui résume les informations sur le dépôt.
    	- Nom du projet  
       - Date 
       - Description du projet 
- Le professeur doit être _Collaborateur_.
- Votre dépôt doit contenir les fichiers et vérifications demandés.
- Vous devez utiliser le format Markdown (md).
- Vous devez donner vos sites de références.
- Vous devez indiquer l'adresse du dépôt dans un fichier texte dans LÉA, Travaux-> TP2 - Docker.  


## Section 1 : Vérification et conteneurs
### Étape 1:  Vérification de l’installation

- À l’installation de Docker vous avez installé deux composantes : Docker Engine et Docker compose.

#### Remise

Dans le fichier `README.md`, inclure une capture d’écran démontrant les commandes de vérifications des deux composantes de Docker.

### Étape 2 :  création de conteneurs

- Créer un système avec un réseau privé virtuel `mon_reseau` et les conteneurs `apache` avec l'image `httpd:alpine` et `mongodb` avec l'image `mongo`.  
- Le conteneur `httpd` écoutera sur le port 80 de votre hôte.
- L’utilisateur `root` de mongoDB sera `adminmongo` et le mot de passe sera `EncoreUneAutreBD`.
- Le conteneur `mongodb` utilisera le volume `mongodb`.
- Prenez en note les commandes que vous avez utilisées.

### Remise

Dans le fichier `README.md`, inclure une section <code>Section 1 - étape 2</code> et :

- Inscrire les commandes et les résultats des commandes pour vérifier :
	- la création du réseau virtuel;
	- la création de vos conteneurs;
	- que les conteneurs sont bien reliés au réseau virtuel privé `mon_reseau`;
	- les journaux de `mongoDB`, identifiez la partie de la création de l'utilisateur `adminmongo`.

## Section 2 : Construction personnalisée d'une image

Vous allez maintenant vous construire une image personnalisée pour « tester » drupal. Drupal est une plateforme de gestion de contenu open source.  

À votre dépôt GitHub, ajouter un répertoire `drupal_bootstrap`. Ajouter au répertoire un fichier `README.md` qui décrit la partie Drupal avec le thème `bootstrap`. Utilisez ce répertoire comme répertoire de travail pour cette partie.

- Consultez le dépôt de Drupal sur docker hub : [https://hub.docker.com/_/drupal](https://hub.docker.com/_/drupal).
- Pour son fonctionnement, Drupal nécessite une base de données. Vous allez utiliser l’image drupal avec une base de données PostgreSQL. Consulter la documentation de Drupal sur docker hub sous la section PostgreSQL pour savoir comment lancer le conteneur PostgreSQL. 
- Utilisez le paramètre `ports` dans votre fichier `docker-compose.yml` pour exposer Drupal sur 8080 afin que vous puissiez utiliser un navigateur avec `localhost:8080`.
- Vous allez créer un `Dockerfile` pour avoir une image drupal personnalisée où vous allez ajouter le modèle Bootstrap à drupal.

- Pour votre Dockerfile.
	- Vous allez créer un fichier `Dockerfile` qui utilise l’image 9 de drupal, <code>FROM drupal:9</code>.
	- Vous devez exécuter (`RUN`) `apt` pour installer `git`, <code>apt update && apt install -y git</code>.
	- Vous devez faire un peu de nettoyage après l’installation avec la commande <code>rm -rf /var/lib/apt/lists/*</code>. Pour faire tout ça en une ligne de commande, vous devez utiliser `&&` et `\`. La première ligne a besoin d'un `\` à la fin pour signifier que la ligne suivante est incluse dans la commande, et au début de la ligne suivante, vous devriez avoir `&&` pour signifier “ si la première commande réussit, exécutez également cette commande ”. Vous pouvez consulter le [Dockerfile](https://github.com/docker-library/drupal/blob/23b02e8143858e0ed63e3f22bfa834f6d7263224/9.5/php8.1/apache-bullseye/Dockerfile) de l’image de `drupal:9` pour voir un exemple. (Dans le Dockerfile de Drupal 9, ils utilisent plus le `;`qui signifie "exécute la première commande, lorsqu'elle a terminé, exécute la suivante sans aucune vérification de réussite".)
	- Par la suite, vous allez changer de répertoire, <code>WORKDIR /var/www/html/themes</code>.
	- Ensuite, vous allez exécuter la commande <code>git clone --branch 8.x-3.x --single-branch --depth 1 https://git.drupalcode.org/project/bootstrap.git</code>, pour installer le thème Bootstrap. Vous allez également changer le propriétaire des fichiers copiés, <code>chown -R www-data:www-data bootstrap</code>. Pour ne pas ajouter une couche à l’image, vous allez encore utiliser une ligne de commande.
	- Finalement, vous allez changer pour le répertoire <code>/var/www/html</code>.

- Pour votre fichier <code>docker-compose.yml</code>.
	- Vous allez renommer l’image `mon_drupal`.
	- Vous allez construire l’image, au service drupal, ajouter la commande `build:.`.
	- N'oubliez pas, on utilise le port 8080.
	- Consultez la section **Volumes** de la documentation de Drupal pour inclure des volumes à Drupal. Utilisez des volumes Docker.
	- Pour le service `postgresql`, utiliser les informations de la section **PostgreSQL** de la documentation de Drupal. Par contre, vous allez ajouter le volume <code>drupal-data:/var/lib/postgresql/data</code> au service PostgreSQL.

- Pour le lancement des conteneurs.
	- Lancer les conteneurs et configurez l'installation Web de Drupal à [http://localhost:8080](http://localhost:8080).
		- À la première ouverture de Drupal avec votre navigateur, vous allez devoir parcourir la configuration de Drupal.
		- Au choix de la BD, utiliser `PostgreSQL`. Utiliser le nom de BD avec l’utilisateur et le mot de passe que vous aurez configuré au lancement du conteneur PostgreSQL.
		- Également, dans la configuration de la base de données, cliquer sur les options avancées et changer l’entrée `Host` par le nom du conteneur de l’image PostgreSQL que vous avez utilisé.
	- Une fois le site Web ouvert (notez l'apparence du site), cliquez sur «Appearance» dans la barre supérieure et remarquez un thème appelé «Bootstrap 3» est présent. C'est celui que nous avons ajouté avec notre `Dockerfile` personnalisé.
	- Cliquez sur «Install and set as default». Cliquez ensuite sur «Back to site» (en haut à gauche) et l'interface du site Web devrait être différente. Vous avez installé et activé avec succès un nouveau thème dans votre propre image personnalisée sans rien installer sur votre hôte autre que Docker.
	- Si vous quittez avec (`Ctrl-c`) ou <code>docker-compose down</code>, cela supprimera les conteneurs, mais pas les volumes, donc au prochain <code>docker-compose up</code> tout sera comme il était.
	- Pour nettoyer totalement les volumes, ajoutez `-v` à la commande `down`.

### Remise

Dans votre dépôt GitHub :  

- Les fichiers `Dockerfile` et `docker-compose.yml` tel que décrit ci-dessous.
- Dans le fichier `README.md` du répertoire `drupal_bootstrap`, vous devez inclure une image d'une capture d’écran démontrant votre drupal personnalisé. 




## Correction :
|Item 								                      |Note|
|--- 								                      |--- |
|Section 1 - Étape 1 			                      |5   |
|Section 1 - Étape 2 <br>Commandes de création      |3   |
|Section 1 – Étape 2 <br>Commandes de vérification  |2   |
|Section 2 <br>Dockerfile                           |4   |
|Section 2 <br>docker-compose.yml                   |4   |
|Section 2 <br>Fonctionnement        	            |2   |
|**Total**									            |20  |


## Compétences évaluées

**00SJ -  Effectuer le déploiement de serveurs intranet.**

1 – Analyser le projet de déploiement.
2 – Monter les serveurs intranet.  
3 - Installer les services intranet. (Partiellement)

**00SK -  Effectuer le déploiement de serveurs internet.**

1 – Analyser le projet de déploiement.
2 – Monter les serveurs Internet.  
3 – Installer les services Internet. (Partiellement)

## Informations - création d'un dépôt sur GitHub

```bash
echo "# Travail pratique 2 - 420-W45-SF" >> README.md
git config --global user.email "your@exemple.com" #si pas déjà définit.
git config --global user.name "Votre nom" #si pas déjà définit.
git init
git add *
git commit -m "Intialisation de mon dépôt"
git branch -M main
# Créez le projet sur Github.com avec votre navigateur
git remote add origin git@github.com:[votrecompte git hub]/tp2-420-w45-sf.git
git push -u origin master
```

Lorsque vous créez le projet sur Github.com, ne mettez PAS de Readme. Il sera ajouté après, une fois que les fichiers seront téléchargés, ceci permet d'éviter tout conflit.  
