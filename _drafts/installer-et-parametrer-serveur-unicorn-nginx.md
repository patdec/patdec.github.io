---
layout: post
title:  "Installer Unicorn sous OpenSUSE 13.2"
date:   2015-06-01 11:03:00
categories: [unicorn,nginx,ruby on rails ,serveur]
---

Unicorn est un serveur web pour Ruby on Rails. Plus précisément un serveur Rack HTTP. Les processus sont forkés pour traiter les nouvelles requêtes entrantes. Il nécessite Nginx pour fonctionner. Nginx est un peur serveur web. Il ne peut traiter que du contenu statique. Le contenu dynamique en Ruby est transféré à Unicron via une socket commune.

Installer Nginx
-----------------

Nginx ne se trouve pas dans les dépôts par défaut OpenSUSE. Il faut le rajouter.

Lançons les  commandes suivantes:
{% highlight bash %}
$ sudo zypper addrepo http://download.opensuse.org/repositories/server:/http/openSUSE_13.2/server:http.repo
$ sudo zypper update
$ sudo zypper install nginx
$ sudo systemctl start nginx.service
{% endhighlight %}

![My helpful screenshot]({{ site.url }}/assets/nginx.png)

Paramétrer Nginx pour Ruby on Rails
-----------------------------------

{% highlight bash %}
$  sudo mkdir /etc/nginx/vhosts.d
{% endhighlight %}

