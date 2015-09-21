---
layout: post
title:  "Utiliser les gemsets de RVM"
date:   2015-04-21 23:18:28
categories: [serveur, RVM, ruby on rails]
---

Bundle
------

Nous avons vu dans les précédents posts un processus d'installation d'une appli 
Rails.
Celle-ci est constituée d'un ensemble de gems minimum nécessaires à son bon 
fonctionnement.
Le simple fait d'installer une version de la gem rails entraîne l'installation 
d'une cascade de gems spécifiques à cette version.
Il s'agit des dépendances de rails. 
Nous avons installé antérieurement la version 4 de Ruby on Rails. 
Si nous souhaitons installer désormais la version 3 en spécifiant la version 
par ex :

``` ruby
gem install -v 3.2.18
```
de nouvelles gems plus anciennes vont être téléchargées et installées. Cette 
opération est gérée par le programme Bundle. Nous n'avons pas comme simple utilisateur à nous 
préoccuper de l'organisation du dépôt, des  versions, des conflits éventuels... Tout est piloté automatiquement par Bundle.

Cependant, il faut noter que gems sont installées pour un environnement particulier de Ruby. Bunde exécute ses opérations pour une version spécifique de Ruby: celle qui est activée actuellement par RVM. Si nous voulons exécuter Bundle pour une autre version de Ruby, il nous faut spécifier la version choisie de Ruby par l'intermédiaire de RVM.Ex:

``` ruby
rvm use 1.9.2
```

Concrètement, Bundle installera les gems dans un sous-dossier de la version de Ruby installée. Ainsi chaque version de Ruby  contient son propre lot de gems installées à des degrés différents suivant les nécessités des applications pour chaque environnement.

Mais un problème se pose. Imaginons que je me trouve en train de travailler sur mon appli en  Rails4 installée sur une version de Ruby 2.2.1 et que je décide momentanément de résoudre un bug sur mon appli en Rails3 installée sur une version 1.9.8 de Ruby. Que se passe t-il si je switch et que je lance spontanément un bundle sans prendre en considération l'environnement dans lequel je suis ? 
A ma désagréable surprise, le Gemfile de mon appli Rails3 est lu et les gems manquantes installées dans mon environnement 2.2.1 de Ruby. C'est à dire au moins la totalité des dépendances de Rails3 alors qu'elles ont déjà été installées pour mon autre environnement.

Gemset
------

Pour pallier ce problème, RVM propose un outil qui rend transparent les commutations d'une appli à une autre. Plus besoin de surveiller les environnements.

Imaginons que je veuille disposer de cet avantage si j'ai besoin de développer sur _monappli_
Je commence par faire:

``` ruby
rvm gemset create monappli
```

Cette commande me crée un gemset _monappli_ au sein de la version courante de Ruby.
Si je veux que cette gemset soit activée automatiquement, je me place alors à la racine de _monappli_ et je fais:

``` ruby
rvm --ruby-version use 2.2.1@monappli
```

2 fichiers sont alors créés à la racine du projet: _.ruby-version_ et _.ruby-gemset_
Le premier contient la version de Ruby spécifiée et qui servira de cadre pour l'environnement. En l'occurence 2.2.1.
Le deuxième contient le nom du gemset que je souhaite activer pour cette application.
Ces 2 fichiers seront lus à chaque entrée dans le dossier racine et provoquerons automatiquement la bascule vers la gemset de la version de Ruby spécifiée.