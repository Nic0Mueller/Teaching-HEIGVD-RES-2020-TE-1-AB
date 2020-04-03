# Rendu de l'exercice Docker 

**Auteur: Nicolas Müller (`nicolas.muller1@heig-vd.ch`)**

## Compte rendu

Expliquez ici quelle stratégie vous avez utilisée pour vous connecter au service exécuté dans le container, en décrivant les étapes suivies:

* L'image est publiée, on peut la lancer facilement car docker run va la chercher sur le net.
* Pour se connecter dessus, il faut trouver le port. Il est affiché au lancement des containers.
* Pour récupérer l'adresse IP, il faut afficher les containers pour récupérer leur nom. Puis, les inspecter avec docker inspect pour retrouver avec quelle adresse ils tournent.
* Pour se connecter on peut maintenant utiliser netcat avec l'ip trouvée et le port en question

## Capture d'écran

Assurez-vous que vous avez placé la capture d'écran dans un fichier nommé `CAPTURE-RES.png`.

## Remarques

Aucune.

