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

Voilà
