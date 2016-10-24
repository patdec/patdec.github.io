---
layout: post
title: "Utiliser des sous domaines avec Puma"
date: 2016-07-06 00:06:00
categories: [ruby on rails]
---

Editer son /etc/hosts
---------------------

{% highlight bash %}
127.0.0.1 localhost admin.puma.local www.puma.local
::1 localhost admin.puma.local www.puma.local
{% endhighlight %}

Lancer Puma sur le port 80
--------------------------

{% highlight bash %}
% rvmsudo puma s -p 80
{% endhighlight %}

Si vous utilisez les variables d'environnement
----------------------------------------------

Les variables d'environnement utilisées dans les fichiers de configuration du type <%= ENV['DATABASE_USERNAME'] %> vont être filtrées par rvmsudo. Il faudra toutes les préfixer par PATH.
Concrètement:

_/home/patrice/.profile_
{% highlight bash %}
export DATABASE_HOST=localhost
export DATABASE_USERNAME=patrice
export DATABASE_PASSWORD=password
{% endhighlight %}

devient

_/home/patrice/.profile_
{% highlight bash %}
export PATH_DATABASE_HOST=localhost
export PATH_DATABASE_USERNAME=patrice
export PATH_DATABASE_PASSWORD=password
{% endhighlight %}
