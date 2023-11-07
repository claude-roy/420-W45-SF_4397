#  Exercice 10 :  Démarrez avec Docker Compose 

### Informations
- Évaluation : formative.
- Durée estimée : 2 heures.
- Système d'exploitation : Ubuntu client ou Windows.
- Environnement : Docker.  

### Objectifs  

- Analyser les différents scénarios de déploiement proposés dans les documents de conception.  
- Distinguer les services à installer sur les serveurs.  
- Distinguer les services à installer sur le réseau.  
- Déterminer les étapes à entreprendre pour installer et configurer les services réseau. 

## Section 1 : commandes de base

Dans cette section, vous allez utiliser les fichiers du document [exercice10.zip](extra/exercice10.zip).

### Étape 1
1- Décompressez le document <code>exercice10.zip</code>.

2- À l’aide d’un éditeur de texte (je recommande Visual Studio Code), ouvrer les fichiers dans le répertoire.  
Observer le fichier <code>docker-compose.yml</code>.  
La partie <code>services</code> décrit les conteneurs que nous allons utiliser.  
Le premier conteneur va se nommer <code>proxy</code>, nous allons utiliser l’image alpine de nginx et le port 80 sera exposé sur l’hôte.

```Dockerfile
  proxy:
    image: nginx:alpine # on utilise la version alpine
    ports:
      - '80:80' # expose 80 a hote envoie a 80 au conteneur
```

Ici, nous utilisons le paramètre <code>volumes</code> pour faire un bind mount du fichier <code>nginx.conf</code> dans notre hôte au fichier <code>/etc/nginx/conf.d/default.conf</code> dans le conteneur. Le fichier sera en lecture seulement dans le conteneur.

```Dockerfile
    volumes:
      - ./nginx.conf:/etc/nginx/conf.d/default.conf:ro
```

Le deuxième conteneur va se nommer <code>serveurweb</code> et nous allons utiliser l’image la plus récente d’Apache.  

```Dockerfile
serveurweb:
    image: httpd  # on utilise httpd:latest
```

<details>
	<summary>Observer le fichier <code>nginx.conf</code>. Pouvez-vous trouver à quel endroit on utilise le DNS de Docker pour référencer notre deuxième conteneur (vous n’avez pas à comprendre le fichier <code>nginx.conf</code>) ?</summary>

Avec le paramètre <code>proxy_pass</code>. Il référence le nom docker DNS du fichier docker compose.
	
```Configfile
proxy_pass         http://serveurweb;
```
</details>

3- Lancer ces conteneurs. Vous devez vous placer dans le répertoire des fichiers pour les lancer (avec Visual Studio, vous pouvez ouvrir un shell dans l’éditeur de texte).

```bash
docker compose up
```

Avec cette commande, vous allez lancer vos conteneurs, créer un réseau privé virtuel, bind mount le fichier spécifié, ouvert le port sur l’hôte et envoyer les journaux à l’écran.

Ouvrez un navigateur et allez à http://localhost. Oui, vous êtes dans le serveur Apache et non nginx. Nginx sert de proxy ici. Si vous consultez vos journaux à la ligne de commande, vous pouvez voir que le <code>proxy</code> est appelé en premier et après le serveurweb.

Arrêter vos conteneurs.

```bash
Ctrl-c
```

4- Relancer vos conteneurs, mais en arrière-plan et vérifier le fonctionnement avec un navigateur Web.

```bash
docker compose up -d
```

Vérifier les commandes disponibles avec docker compose.

```bash
docker compose --help
```

Vous pouvez constater que de nombreuses commandes que vous avez déjà utilisées se retrouvent dans docker compose.

Consulter les journaux.

```bash
docker compose logs
```

Lister les conteneurs qui s’exécutent.

```bash
docker compose ps
```

Lister les services qui s’exécutent dans les conteneurs.

```bash
docker compose top
```

Finalement, arrêter vos conteneurs.

```bash
docker compose down
```

## Section 2 : application complète

Source : [Get started with Docker Compose](https://docs.docker.com/compose/gettingstarted/)

Sur cette page, vous construisez une application web simple en Python  fonctionnant sur Docker Compose. L'application utilise le framework Flask et maintient un compteur de hits dans Redis. Bien que l'exemple utilise Python, les concepts démontrés ici devraient être compréhensibles même si vous n'êtes pas familier avec ce langage.

## Conditions préalables : 

Assurez-vous que vous avez déjà installé Docker Engine et Docker Compose. Vous n'avez pas besoin d'installer Python ou Redis, car ils sont tous deux fournis par les images Docker.

## Étape 1 : Configuration

Définissez les dépendances de l'application.

1- Créez un répertoire pour le projet :

```bash
 mkdir composetest
 cd composetest
```
2- Créez un fichier appelé app.py dans le répertoire de votre projet et collez-y ceci :

```python
import time

import redis 
from flask import Flask 

app = Flask(__name__)
cache = redis.Redis(host='redis', port=6379)

def get_hit_count():
    retries = 5
    while True:
        try:
            return cache.incr('hits')
        except redis.exceptions.ConnectionError as exc:
            if retries == 0:
                raise exc
            retries -= 1
            time.sleep(0.5)

@app.route('/')
def hello():
    count = get_hit_count()
    return 'Hello World! I have been seen {} times.\n'.format(count)
```

Dans cet exemple, redis est le nom d'hôte du conteneur redis sur le réseau de l'application. Nous utilisons le port par défaut de Redis, 6379.

>Gestion des erreurs transitoires  
Notez la façon dont la fonction get\_hit\_count est écrite. Cette boucle de réessaie de base nous permet de tenter notre requête plusieurs fois si le service Redis n'est pas disponible. Ceci est utile au démarrage lorsque l'application est en ligne, mais rend également notre application plus résiliente si le service Redis doit être redémarré à tout moment pendant la durée de vie de l'application. Dans un cluster, cela permet également de gérer les coupures momentanées de connexion entre les nœuds.   

3- Créez un autre fichier appelé `requirements.txt` dans le répertoire de votre projet et collez-y le code suivant :

```python
flask
redis
```

## Étape 2 : Créer un Dockerfile

Dans cette étape, vous écrivez un Dockerfile qui construit une image Docker. L'image contient toutes les dépendances dont l'application Python a besoin, y compris Python lui-même.

Dans le répertoire de votre projet, créez un fichier nommé `Dockerfile` et collez ce qui suit :


```Dockerfile
# syntax=docker/dockerfile:1
FROM python:3.7-alpine
WORKDIR /code
ENV FLASK_APP=app.py
ENV FLASK_RUN_HOST=0.0.0.0
RUN apk add --no-cache gcc musl-dev linux-headers
COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt
EXPOSE 5000
COPY . .
CMD ["flask", "run"]

```
Cela indique à Docker de :

- Construisez une image en commençant par l'image Python 3.7.
-  Définir le répertoire de travail à /code.
- Définir les variables d'environnement utilisées par la commande flask.
- Installer gcc et les autres dépendances
- Copier le fichier requirements.txt
- Installer les dépendances de Python.
- Ajouter des métadonnées à l'image pour décrire que le conteneur écoute sur le port 5000.
- Copier le répertoire actuel . dans le projet vers le répertoire de travail . dans l'image.
- Définir la commande par défaut du conteneur à flask run.

Pour plus d'informations sur la rédaction de Dockerfiles, consultez [le guide de l'utilisateur de Docker](https://docs.docker.com/engine/reference/builder/) et [la référence Dockerfile](https://docs.docker.com/engine/reference/builder/).

## Étapes 3 : Définir les services dans un fichier docker-compose

Créez un fichier appelé `docker-compose.yml` dans le répertoire de votre projet et collez ce qui suit:

```docker-compose.yml
version: "3.9"
services:
  web:
    build: .
    ports:
      - "5000:5000"
  redis:
    image: "redis:alpine"
```

Ce fichier Compose définit deux services : `web` et `redis`.


### Service Web

Le service `web` utilise une image créée à partir du `Dockerfile` dans le répertoire actuel. Il lie ensuite le conteneur et la machine hôte au port exposé, `5000`. Cet exemple de service utilise le port par défaut pour le serveur Web Flask, `5000`.

### Service Redis

Le service `redis` utilise une image `Redis` publique extraite du registre Docker Hub.

## Étapes 4 : Céez et exécutez votre application avec Compose

1- Depuis le répertoire de votre projet, démarrez votre application en exécutant `docker compose up`.

```bash
docker compose up
```

Compose extrait une image Redis, construit une image pour votre code et démarre les services que vous avez définis. Dans ce cas, le code est copié statiquement dans l'image au moment de la construction.

2- Entrez http://localhost:5000/ dans un navigateur pour voir l'application fonctionner.

Vous devriez voir un message dans votre navigateur disant :

![Image](https://docs.docker.com/compose/images/quick-hello-world-1.png)

3- Rafraichissez votre page, le nombre devrait s'incrémenter.

![Image](https://docs.docker.com/compose/images/quick-hello-world-2.png)

4- Passez à une autre fenêtre de terminal, et tapez `docker image ls` pour lister les images locales.

L'énumération des images à ce stade devrait retourner `redis` et `web` (plus précisément, *nom_répertoire*-web).

```bash
docker image ls
```

Vous pouvez inspecter les images avec `docker inspect <tag ou id>`.


5- Arrêtez l'application, soit en exécutant `docker compose down` depuis le répertoire de votre projet dans le second terminal, soit en appuyant sur CTRL+C dans le terminal d'origine où vous avez démarré l'application.

## Étapes 5 : Éditer le fichier compose pour ajouter un montage bind

Modifiez le fichier `docker-compose.yml` dans le répertoire de votre projet afin d'ajouter un montage bind pour le service `web` :

```Dockerfile
version: "3.9"
services:
  web:
    build: .
    ports:
      - "5000:5000"
    volumes:
      - .:/code
    environment:
      FLASK_ENV: development
  redis:
    image: "redis:alpine"

```
La nouvelle clé `volumes` monte le répertoire du projet (répertoire courant) sur l'hôte vers `/code` à l'intérieur du conteneur, vous permettant de modifier le code à la volée, sans avoir à reconstruire l'image. La clé `environment` définit la variable d'environnement `FLASK_ENV`, qui indique à `flask run` de s'exécuter en mode développement et de recharger le code en cas de modification. Ce mode ne doit être utilisé qu'en développement.

## Étapes 6 : Reconstruire et exécuter l'application avec Compose

Depuis le répertoire de votre projet, tapez `docker compose up` pour construire l'application avec le fichier Compose mis à jour, puis exécutez-la.

```bash
docker compose up
```

Vérifiez à nouveau le message Hello World dans un navigateur Web, et actualisez pour voir le compte s'incrémenter.

<details>    
<summary>Dossiers partagés, volumes, et montages bind </summary>

- Si votre projet se trouve en dehors du répertoire `Users` (`cd ~`), vous devez partager le lecteur ou l'emplacement du fichier Docker et du volume que vous utilisez. Si vous obtenez des erreurs d'exécution indiquant qu'un fichier d'application est introuvable, qu'un montage de volume est refusé ou qu'un service ne peut pas démarrer, essayez d'activer le partage de fichiers ou de lecteurs. Le montage de volume nécessite des lecteurs partagés pour les projets qui se trouvent en dehors de `C:\Users` (Windows) ou `/Users` (Mac), et est requis pour tout projet sur Docker Desktop pour Windows qui utilise des conteneurs Linux. Pour plus d'informations, consultez la rubrique Partage de fichiers sur Docker pour Mac, ainsi que les exemples généraux sur la gestion des données dans les conteneurs.

- Si vous utilisez Oracle VirtualBox sur un ancien système d'exploitation Windows, il se peut que vous rencontriez un problème avec les dossiers partagés, comme décrit dans ce ticket de problème VB. Les systèmes Windows plus récents répondent aux exigences de Docker Desktop pour Windows et n'ont pas besoin de VirtualBox.

</details>

## Étapes 7 : Mettre à jour l'application

Comme le code de l'application est maintenant monté dans le conteneur à l'aide d'un volume, vous pouvez apporter des modifications à son code et voir les changements instantanément, sans avoir à reconstruire l'image.

Modifiez le message d'accueil dans `app.py` et enregistrez-le. Par exemple, remplacez le message `Hello World !` par `Hello from Votre_nom !`

Actualisez l'application dans votre navigateur. Le message d'accueil devrait être mis à jour, et le compteur devrait continuer à s'incrémenter.

![Image](https://docs.docker.com/compose/images/quick-hello-world-3.png)

## Pour vérification

Remettre une capture d’écran de votre site Application dans l'espace travaux, sur LÉA.

![Exemple de vérification.](images/Exerc10.png)

## Étapes 8 : Expérimentez d'autres commandes

Je vous rappelle que si vous souhaitez exécuter vos services en arrière-plan, vous pouvez passer le paramètre `-d` (pour le mode "détaché") à `docker compose up` et utiliser `docker compose ps` pour voir ce qui est en cours d'exécution :

```bash
$docker compose up -d
[+] Running 2/2
 ⠿ Container composetest-redis-1  Started
 ⠿ Container composetest-web-1    Started

$docker compose ps 
```

La commande `docker compose run` vous permet d'exécuter des commandes ponctuelles pour vos services. Par exemple, pour voir quelles variables d'environnement sont disponibles pour le service `web` :

```bash
$docker compose run web env
```

Voir `docker compose --help` pour voir les autres commandes disponibles.

Vous pouvez également installer la complétion de commande pour les shells bash et zsh, qui vous indique également les commandes disponibles.

Si vous avez démarré Compose avec `docker compose up -d`, vous pouvez seulement arrêter les services :

```bash
$docker compose stop
$docker container ls -a
```

Vous pouvez tout démonter, en supprimant entièrement les conteneurs, avec la commande `down`. Ajoutez le paramètre `--volumes` pour supprimer également le volume de données utilisé par le conteneur Redis :

```bash
$docker compose down --volumes
$docker container ls -a
```

À ce stade, vous avez vu les bases du fonctionnement de Compose.

