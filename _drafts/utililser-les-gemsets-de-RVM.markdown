---
layout: post
title:  "Utiliser les gemsets de RVM"
date:   2015-04-21 23:18:28
categories: [serveur, RVM, ruby on rails]
---

RubyGems
------

Nous avons vu dans les précédents posts un processus d'installation d'une appli 
Rails. Celle-ci est constituée d'un ensemble de gems minimums nécessaires à son bon 
fonctionnement. Le simple fait d'installer une version de la gem rails entraîne l'installation 
d'une cascade de gems spécifiques à cette version.
Ces derniers constituent des dépendances de cette version de Rails. 

Dans un post antérieur, nous avons installé Ruby on Rails 4. 
Si nous installons une version plus ancienne:

{% highlight bash %}
gem install -v 3.2.18
{% endhighlight %}

alors des gems plus anciennes vont être téléchargées et installées. Cette 
opération est gérée natviement par Rubygems. Nous n'avons pas à nous 
préoccuper de l'organisation du dépôt, de conflits éventuels qui pourraient avoir lieu entre différentes versions. Une gem spécifique peut être installé dans toutes ses versions disponibles.

Cependant, il faut avoir dans l'esprit que les gems sont installées pour la version courante de Ruby. Rubygems téléchargera et installera les gems spécifiquement pour cette version. Si nous voulons installer Rails pour une autre version de Ruby, il nous faut spécifier la version choisie de Ruby par l'intermédiaire de RVM.Ex:

{% highlight bash %}
rvm use 1.9.3
{% endhighlight %}

Concrètement, Rubygems installera les gems dans un sous-dossier de la version de Ruby installée. Ainsi chaque version de Ruby  contient son propre lot de gems installées à des degrés différents suivant les nécessités des applications pour chaque environnement.

Mais un problème se pose. Imaginons que nous nous trouvons en train de travailler sur notre appli en  Rails4 installée sur une version de Ruby 2.2.1 et que nous décidons momentanément de résoudre un bug sur notre appli en Rails3 installée sur une version 1.9.3 de Ruby. Que se passe t-il si nous switchons et que nous lançons une commande rails ou rake sans prendre en considération l'environnement dans lequel nous sommes ? Nous risquons d'avoir un message d'erreur pour nous avertir que la version courante de Ruby est erronée ou bien que des gems sont manquantes. Nous sommes restés dans l'environnement Ruby 2.2.1 avec ses propes gems... 
Si pour y remédier nous lançons la commande "Bundle", à  notre désagréable surprise, le Gemfile de notre appli Rails3 est lu et les gems manquantes installées... mais dans notre environnement 2.2.1 de Ruby !! Au moins la totalité des dépendances de Rails3 seront installées et viendra surcharger inutilement notre dépot de gems 2.2.1.

Gemset
------

Pour pallier ce problème, RVM propose un outil qui rend transparent les commutations d'une appli à une autre. Plus besoin de surveiller les environnements.

Pour bénéficier de cette fonction, nous commençons par créer 2 gemsets.

{% highlight bash %}
$ rvm use 2.2.1
$ rvm gemset create monapplication
$ rvm use 1.9.3
$ rvm gemset create appli3
{% endhighlight %}

Nous disposons maintenant de 2 environnement cloisonnés: chacun correpondant à 1 version de Ruby avec ses propres gems. Pour passer de l'un à l'autre, il faut exécuter cette commande:
{% highlight bash %}
$ rvm use 2.2.1@monapplication # pour passer à la gemset _monapplication_ de Ruby 2.2.1
$ rvm use 1.9.3@appli3 # pour passer à la gemset _appli3_ de Ruby 1.9.2
{% endhighlight %}

Si nous voulons que ce gemset soit activé automatiquement, nous devons d'une part nous placer à la racine de _monapplication_ et faire:

{% highlight ruby %}
rvm --ruby-version use 2.2.1@monapplication
{% endhighlight %}

d'autre part à la racine de _appli3_: 
{% highlight ruby %}
rvm --ruby-version use 1.9.3@appli3
{% endhighlight %}

Dans chaque projet, 2 fichiers sont alors créés à la racine: _.ruby-version_ et _.ruby-gemset_
Le premier contient la version de Ruby, le deuxième contient le nom du gemset que nous souhaitons activer pour cette application.
Ces 2 fichiers seront lus à chaque entrée dans le dossier racine et provoqueront automatiquement la bascule vers le gemset de la version de Ruby spécifiée.

Pour nous assurer du gemset en cours, nous pouvons utiliser cette commande:

{% highlight ruby %}
> rvm gemset list

# resultat:
gemsets for ruby-2.2.1 (found in /home/patrice/.rvm/gems/ruby-2.2.1)
   (default)
   global
=> monapplication
{% endhighlight %}

Le gemset courant est précédé de "=>".
