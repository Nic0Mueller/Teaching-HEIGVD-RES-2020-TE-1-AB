# Protocole JOCC 0.9 

**Auteur: Nicolas Müller (`nicolas.muller1@heig-vd.ch`)**

## Introduction

### Objectifs du protocole

Le protocole JOCC est conçu pour que les clients puissent envoyer et recevoir des Jokes au/depuis le serveur. Il se fera au travers d'un message TCP/IP. 

### Fonctionnement général

Le protocole utilise le port 3101 comme port par défaut. Après avoir accepté une demande, le serveur envoie un PRE_WELCOME et attend que l'utilisateur se login avec un pseudo. Le client peut se login avec LOGIN. À partir de là, le serveur envoie un WELCOME et le client pourra demander des Jokes(GET), envoyer des jokes(SEND), évaluer des jokes(RATE), lister les Jokes les mieux évalués(TOPRATE et TOPRECENT), lister les Jokes les plus récents et demander un classement aux membres(TOPMEMBERS). Ce sont les fonctionnalités minimales demandées pour respecter le protocole  JOCC.

Le serveur répondra avec ERROR, s'il y a une erreur lors de la communication, la synthaxe ou l'opération demandée.

Le client pourra fermer la communication en envoyant QUIT.

C'est un protocole à état. Le pseudo du client sera conservé tout au long de sa connexion pour faciliter les échanges.

Le caractère de fin de ligne sera '\n'.

## Synthaxe des messages

Voici la liste de tous les messages obligatoires que l'implémentation du protocole JOCC doit implémenter.

#### PRE_WELCOME

Message envoyé par le serveur à la connexion pour informer le client qu'il est bien connecté. Le serveur envoie ensuite des informations pour le login d'un utilisateur. Se termine par END_PRE_WELCOME. Le serveur peut ajouter autant de ligne que souhaité entre deux.

Exemple de message reçu:

PRE_WELCOME \n

Veuillez vous login avec LOGIN \<Nom> \n

END_PRE_WELCOME \n

#### LOGIN

Message envoyé par l'utilisateur pour s'authentifier. Doit être fait avec d'accéder aux autres commandes.

Exemple:

LOGIN Nic0Mueller \n

#### WELCOME

Envoyé lorsque l'utilisateur a accès à toutes les commandes pour lui en informer. Fini par END_WELCOME. Le serveur peut ajouter autant de ligne que souhaité entre deux.

Exemple:
WELCOME \n

You can now access to all these commands \n

// ... Example... // \n

END_WELCOME \n

#### SEND \<Category>* JOKE \<Joke>

Commande qui permet au client d'envoyer une blague au serveur. L'option Category est optionnelle.

Exemple:
SEND covid19 JOKE This is my joke \n

#### GET \<Category>* JOKE

Commande qui demande au serveur d'envoyer un Joke aléatoire. Une catégorie peut être précisée si voulu. Si aucune Joke n'a été trouvé pour la catégorie, le serveur répond avec ERROR.

Exemple:
GET dark JOKE \n

#### SENT \<ID> JOKE \<Joke>

Réponse du serveur au client qui lui envoie un Joke avec son ID.

Exemple:
SENT 123456789 JOKE This is Funny \n

#### RATE \<ID> \<RATE>

Permet au client d'évaluer un Joke. L'id doit être spécifié et la note aussi. La note peut être GOOD ou BAD. Si elle ne respecte pas cela, ou que l'id n'existe pas, le serveur répondra ERROR.

Exemple:

RATE 123456789 BAD \n

#### TOPRATE \<Category>

Permet au client de demander les meilleures Jokes au serveur. La catégorie est optionnelle. Si aucune Joke n'a été trouvé pour la catégorie, le serveur répond avec ERROR.

Exemple:

TOPRATE Dark \n

#### TOPRECENT 

Permet au client de demander les Jokes les plus récentes au serveur.

Exemple:

TOPRECENT \n

#### TOPIS 

Envoyé du serveur au client en réponse à TOPRECENT ou TOPRATE. Envoie d'abord TOPIS, puis la liste des jokes demandées. Le nombre de Jokes est libre pour le serveur. Fini par END_TOPIS.

Exemple:

TOPIS \n

This is joke 1. \n

This is Joke 2. \n

...

END_TOPIS \n

#### TOPMEMBERS \<Type>

Le client demande au serveur le classement des utilisateurs. Le Type est obligatoire et doit être AMOUNT ou BEST.

Exemple:

TOPMEMBERS BEST \n

#### MEMBERIS

Le serveur envoie au client la liste des meilleurs membres. Le message commence par MEMBERIS. Puis, le nombre de lignes est libre. Le message se termine par END_MEMBERIS

Exemple:

MEMBERIS \n

Nic0Mueller \n

...

END_MEMBERIS \n

#### QUIT

Ce message est envoyé du client vers le serveur, pour indiquer que la session est terminée.

Exemple:

QUIT \n

#### ERROR

Ce message arrive en réponse du serveur lorsque l'utilisateur n'a pas envoyé des informations correctes. Il arrive avec un code d'erreur et des informations.

Exemple:

ERROR 300 Synthax Error \n

## Details de fonctionnement

Le serveur DOIT impémenter toutes les commandes mentionnées au dessus. Il peut en ajouter, mais cela doit être précisé au client dans le message WELCOME

#### Classement

Le serveur classe les Jokes de la manière suivante: Il garde, pour chaque Joke, un score. C'est un nombre qui commence à 0. Chaque fois qu'un GOOD arrive, le serveur incrémente le score. L'inverse sera fait pour  un BAD.

#### Gestion des erreurs

Le serveur répondra avec le code:

300 -> Si la synthaxe d'un message n'est pas correcte.

301 -> Si la catégorie n'a pas pu être trouvée

302 -> Si un paramètre n'est pas valide. (Par exemple de respecte pas GOOD ou BAD)

Il peut implémenter plus de code d'erreur mais ces derniers doivent commencer par '9'.

## StateFull vs StateLess

Le serveur est StateFull car il garde en mémoire le nom de l'utilisateur. Ce choix à été fait pour faciliter les messages. En effet, le client ne devra pas recommuniquer à chaque fois son nom d'utilisateur. 

## Exemple d'utilisation

S: PRE_WELCOME \n

S: Veuillez vous login avec LOGIN \<Pseudo> \n

S: END_PRE_WELCOME \n

C: LOGIN Nic0Mueller \n

S: WELCOME \n

S: Les commandes disponibles sont: \n

S: ... Liste ... \n

S: END_WELCOME \n

C: SEND TE1 JOKE This was easy \n

C: GET TE1 JOKE \n

S: SENT 1 JOKE This was easy \n

C: RATE 1 BADD \n

S: ERROR 302 : Incorrect Argument \n

C: RATE 1 BAD \n

C: TOPRATE TE1 \n

S: TOPIS \n

S: This was easy \n

S: END_TOPIS \n

C: QUIT \n

## Remarques

Aucune.

