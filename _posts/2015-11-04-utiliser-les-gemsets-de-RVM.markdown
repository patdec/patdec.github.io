---
layout: post
title:  "Utiliser les gemsets de RVM"
date:   2015-04-21 23:18:28
categories: [serveur, RVM, ruby on rails]
---

RubyGems
--------

Nous avons vu dans les précédents posts un processus d'installation d'une appli 
Rails. Celle-ci est constituée d'un ensemble de gems minimums nécessaires à son bon 
fonctionnement. L'installation d'une version de la gem _rails_ s'accompagne de l'installation de nombreuses autres gems spécifiques à cette version.

C'est l'outil RubyGems qui gère cette tâche. Il s'agit d'un gestionnaire de package pour les librairies et programmes 
Ruby empaquetés sous la dénomination _gem_.
 
Des gems de différentes versions peuvent en théorie cohabiter sans problème. Pour des dépendances simples, Rubygems
est bien adapté.

Bundler
-------

Cependant, pour organiser un ensemble de gems avec des versions particulières pour une application donnée, il vaut 
mieux utiliser Bundler. Car celui-ci est à la fois une commande -_bundle_ - mais aussi un fichier texte éditable - 
_Gemfile_ -  qui est recense toutes les Gems, à une version donnée, indispensables à notre application. 
Pour notre application Rails qui utilise de nombreuses gems qu'on va télécharger au fil de nos développement, on va
se retrouver,le temps avançant, confronter à des problèmes de compatibilité entre notre code qui correspond à une 
version de gem donnée et les nouvelles versions de cette gem (pb de compatibilié ascendante). Pour éviter cet écueil, nous allons versionner les gems dans ce fichier Gemfile de façon à ne plus installer les nouvelles versions qui 
peuvent provoquer ces dysfonctionnements. On grave donc dans le marbre une bonne fois pour toute les Gems 
utilisées ainsi que la version utilisée.


Malgré tout, même avec cet outil providentiel,il faut avoir dans l'esprit que les gems sont installées pour la version 
courante de Ruby. Bundler téléchargera et installera les gems spécifiquement pour cette version. 
Si nous voulons installer Rails pour une autre version de Ruby, il nous faut spécifier la version choisie de Ruby par l'intermédiaire de RVM.Ex:

{% highlight bash %}
rvm use 1.9.3
{% endhighlight %}

Il faudra donc bien veiller à lancer cette commande si nous utilisons plusieurs versions de Ruby simultanément.  

Gemset
------

Mais n'existe-t-il pas un moyen d'automatiser cela ? Quand on se place dans une appli rails, ne peut-on pas charger 
l'environnement adéquat sans effort ? RVM propose un outil qui répond positivement à cette question. On peut commuter d'une appli à une autre de façon transparente grâce aux Gemsets.

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
