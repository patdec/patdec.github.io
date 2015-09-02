--
layout: post
title:  "Créer un application Rails fonctionnelle"
date:   2015-09-03 01:17:28
categories: [serveur, postgresql, ruby on rails]
---

Nous avons vu comment créer un environnement de dévellopement pour Ruby on Rails.
Nous allons donc voir maintenant comment créer et paramétrer cet environnement pour rendre opérante une application Rails.


### Créer une application rails

Nous avons désormais un environnement de développement Ruby on Rails pleinement opérationnel.
Nous pouvons donc créer une application.

{% highlight bash %}
$ rails new monappli
$ cd monappli
$ vim config/databases.yml
$ bundle
$ rake db:setup
$ rake db:migrate
$ sudo service apache2 restart
{% endhighlight %}

### Paramétrer Apache

Notre serveur Rails est pleinement opérationnel. Il nous reste à créer un hôte virtuel pour notre future application

{% highlight bash %}
$ sudo vim /etc/apache2/vhosts.d/monappli.conf
$ sudo vim /etc/hosts
{% endhighlight %}
