---
layout: post
title: "Configurer Fedora pour Docker"
date: 2017-01-12 01:09:00
categories: [ fedora, docker ]
---

Arreter Docker
{% highlight bash %}
# systemctl stop docker
{% endhighlight %}

Effacer toute trace anterieure:

{% highlight bash %}
# rm /etc/sysconfig/docker-storage
# rm -rf /var/lib/docker
# rm -rf /var/run/docker
{% endhighlight %}



Creer partition /dev/sda5 _Linux LVM_ (8e)

Creer les volumes physiques et le groupe de volume _docker_vol_

{% highlight bash %}
# pvcreate /dev/sda5
# vgcreate docker_vol /dev/sda5
{% endhighlight %}

Editer le fichier /etc/sysconfig/docker-storage-setup
Mettre la ligne suivante:
{% highlight bash %}
VG=docker_vol
{% endhighlight %}


Lancer les commandes suivantes:

{% highlight bash %}
# docker-storage-setup
# systemctl start docker
{% endhighlight %}
