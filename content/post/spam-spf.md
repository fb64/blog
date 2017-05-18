+++
author = "Florian"
categories = ["administration"]
date = "2017-05-18T18:17:52"
description = "Problème de vérification SPF avec un serveur SMTP"
draft = false
tags = ["mail","dns","spf"]
title = "Spam et champ SPF"
+++
## Problématique

Il y a peut de temps j'ai installé un serveur [SMTP](https://fr.wikipedia.org/wiki/Simple_Mail_Transfer_Protocol) afin de pouvoir envoyer des mails en utilisant un nom de domaine particulier. Après avoir installé et configuré [Postfix](http://www.postfix.org/) sur un serveur Ubuntu, je me suis rendu compte que les mails partaient bien mais que la plus part des destinataires les recevaient pas.

Je me suis rendu compte du problème en affichant la sources d'un des mails reçu :
```
Received-SPF: None (protection.outlook.com: mon.domaine.com does not
 designate permitted sender hosts)
```
[SPF](https://fr.wikipedia.org/wiki/Sender_Policy_Framework) est une norme qui permet de vérifier que le nom de domaine appartient bien à l'expéditeur.

## Solution

La solution est assez simple, il suffit d'ajouter un champs [DNS](https://fr.wikipedia.org/wiki/Domain_Name_System) de type [SPF](https://fr.wikipedia.org/wiki/Sender_Policy_Framework) pour le domaine ou le sous domaine concerné pour préciser les adresses des serveurs autorisés à envoyer des mails.

Exemple de champs SPF :
```
"v=spf1 a ip4:10.10.10.10 ~all"
```

* **a**  --> autorise l'addresse ip du domaine par défaut
* **ip4:10.10.10.10** --> autorise l'adresse ipv4 10.10.10.10 (à remplacer avec l'adresse du serveur)

## Sources 

* https://docs.ovh.com/fr/fr/web/domains/le-champ-spf/
* https://www.oceanet-technology.com/blog/post/34-processus-d-authentification-spf-sender-policy-framework