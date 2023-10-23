# Travail pratique 1 - Procédure d'installation d'un serveur

### Informations

- Évaluation : sommative
- Type de travail : équipe de 2
- Durée : 3 heures
- Système d'exploitation : Linux Ubuntu client et serveur 22.04
- Environnement : virtuel, vsphere.

### Objectifs

- Analyse la demande de déploiement de serveurs en fonction des besoins exigés et du plan de conception. 
- Détermine le type d’installation des serveurs (physique/virtuel) en fonction de la demande, des contraintes techniques et organisationnelles.
- Analyse la documentation technique des services à installer. 
- Identifie les ressources physiques nécessaires pour installer le serveur. 
- Détermine les tâches selon les méthodes d’installation des serveurs.
- Détermine un plan des étapes à entreprendre pour installer et configurer un serveur donné.
- Planifie des installations conformes dans les délais prévus.
- Dispose les serveurs en fonction des besoins exprimés et des plans de conception.
- Installe le système d’exploitation sur les serveurs selon la méthode retenue et en fonction des besoins. 	
- Configure le système d’exploitation après l’installation.
- Connecte les serveurs au réseau physique ou virtuel.
- Configure les paramètres réseau du serveur.
- Vérifie et valide la connectivité des serveurs au réseau.
- Vérifie les impacts de la connexion des nouveaux serveurs sur le fonctionnement des systèmes existants.
- Identifie chaque serveur et périphérique.
- Identifie le positionnement physique ou logique (p. ex. : nom serveur de virtualisation).
- Applique les correctifs et les mises à jour sur les systèmes d’exploitation serveur.
- Installe un système de fichiers sécurisé.
- Partitionne correctement les disques pour mieux identifier les partitions systèmes et données.
- Détermine les bons droits d’accès aux ressources.
- Gère les droits d’accès (ajout, suppression, modification, etc.).
- Applique les techniques de documentation de suivi des modifications apportées avant, pendant et après l’installation selon les normes de l’organisation.
- Effectue une mise à jour régulière et systématique de l'information répertoriée.
- Mets à jour les documents de procédure.
- Rédige la documentation des tâches effectuées.
- Note les problèmes rencontrés et les solutions apportées.

### But

Le but de ce travail est de **documenter** la procédure d'installation d'un serveur Ubuntu 22.04 LTS dans votre environnement de test.  **Attention documenter n'est pas installer**

   - Ce serveur doit utiliser un système de stockage LVM.
   - La carte réseau est présente sur le réseau 10.100.2.0/24
   - Il y a 2 processeurs et 4 Go de mémoire vive.
   - Ce serveur devrait pouvoir être accédé depuis une machine client depuis le protocole ssh. 
   - Il faut que le service ssh soit sécurisé.
   - Il devrait être prêt à servir de modèle pour le déploiement de services supplémentaires.

## Vous devez fournir :

- Un dépôt GitHub de votre travail dont 
    - Un fichier auteurs.md qui résume les informations sur le dépôt.
         - Date 
         - Membres de l'équipe
         - Contexte
- Nommez votre projet : ISS\_TP1\_[Initiales1\_Initiales2].
- La visibilité est privée.
- Le professeur doit être _Contributeur_ ainsi que les 2 membres de l'équipe
- Votre fichier avec toutes les sections demandées.
- Vous devez utiliser le format Markdown (md).
- Vous devez utiliser des images pour favoriser la compréhension.
- Vous devez donner vos sites de références.
- Vous devez déposer sur Léa un fichier texte contenant l'adresse de votre dépôt.  

## Voici les sections qui doivent être incluses dans votre procédure:

1. Caractérisation de la machine serveur :  

   - Nom de la Vm et Nom de la machine (hostname)
   - Adresse IPv4 avec les informations de masque et de passerelle.
   - Fichier /etc/hosts (partie liée à l'installation). 
   - Port des services ouverts (penser ss).
   - Usager utilisé pour l'installation, autre que root.

2. Les mises à jour préalables à l'installation et ajout de composants nécessaires.
   
   Que doit se retrouver sur votre serveur pour devenir un modèle de déploiement.
   
   - Programmes : Nom, version, procédure d'installation.
   - Pour chacun des programmes installés : 
   		- Répertoire utilisé par le programme, ses fichiers de configuration, ses données.
   		- Espace disque utilisé et droits sur les répertoires.
   		- Nom d'usager (UID) et groupe (GID) pour le programme au sein du système et dans l'application.

3. Procédure de validation de l'installation.

	Cette procédure utilisée par une tierce personne devrait démontrer le bon fonctionnent du serveur et comprendre les commandes nécessaires à la vérification des caractéristiques du serveur.

### Suggestion

Pour vous aider, consulter les exercices que vous avez faits à ce jour.

## Consignes :

- Le temps alloué en classe est d'une période.
- La date de remise est celle indiquée sur LÉA.
- Ce travail pratique vaut pour 15 % de la note finale.
- Ce travail est réalisé en équipe de 2 membres et seuls les membres de cette équipe y contribuent (pour la création).
- Tout matériel est autorisé.
- Toute copie de code, de portion de code, d’algorithme ou de texte doit faire mention de sa source.
- Tout constat de plagiat, tricherie ou fraude sera automatiquement déclaré à la Direction et les sanctions prévues seront appliquées.


## Références à consulter :

- Installation  :[ https://ubuntu.com/tutorials/install-ubuntu-server#1-overview](https://ubuntu.com/tutorials/install-ubuntu-server#1-overview)
- Markdown : 
   - [https://guides.github.com/features/mastering-markdown/](https://guides.github.com/features/mastering-markdown/)
   - [https://about.gitlab.com/handbook/markdown-guide/](https://about.gitlab.com/handbook/markdown-guide/)



## Évaluation :
|Item |Points  |
--- | --- |
|Respect des consignes sur le dépôt et léa |20|
|Respect des caractéristiques demandées |20|
|Section caractéristiques et mise à jour et ajout de composants  |30|
|Section Procédure de vérification |30|
|Total|100|

## Compétences évaluées


00SJ -  Effectuer le déploiement de serveurs intranet.

	1 – Analyser le projet de déploiement.
	2 – Monter les serveurs intranet.
	4 – Procéder au renforcement de la sécurité.
	6 – Rédiger la documentation.

00SK -  Effectuer le déploiement de serveurs internet.

	1 – Analyser le projet de déploiement.
	2 – Monter les serveurs intranet.
	4 – Procéder au renforcement de la sécurité.
	6 – Rédiger la documentation.



