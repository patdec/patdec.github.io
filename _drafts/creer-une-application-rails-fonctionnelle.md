---
layout: post
title:  "Créer une application Rails fonctionnelle"
date:   2015-09-03 01:17:28
categories: [serveur, postgresql, ruby on rails]
---

Nous avons vu précedemment comment mettre en place un environnement de développement pour Ruby on 
Rails et créer les conditions de son exécution. Nous allons nous lancer dans notre première application Rails 



### Créer une application rails

Ouvrons un terminal et entrons la commandes suivante.

{% highlight bash %}
$ rails new monappli
{% endhighlight %}

{% highlight bash %}
$ cd monappli
$ vim config/databases.yml
$ bundle
$ rake db:setup
$ rake db:migrate
$ sudo service apache2 restart
{% endhighlight %}

### Paramétrer Apache

Notre serveur Rails est pleinement opérationnel. Il nous reste à créer un hôte 
virtuel pour notre future application

{% highlight bash %}
$ sudo vim /etc/apache2/vhosts.d/monappli.conf
$ sudo vim /etc/hosts
{% endhighlight %}
