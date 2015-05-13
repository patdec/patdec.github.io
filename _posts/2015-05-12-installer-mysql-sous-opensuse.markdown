---
layout: post
title:  "Installer MySQL sous OpenSUSE 13.2!"
date:   2015-05-12 20:54:28
categories: jekyll update
---

Depuis que MySQL est tombé dans l'escarcelle d'Oracle, les distributions le remplacent peu à peu par un fork: MariaDB

Installer MariaDB
-----------------

Lançons la commandes suivante:
{% highlight bash %}
$ zypper install mariadb
{% endhighlight %}

Paramétrer MariaDB
------------------

Maintenant que le serveur MariaDB est installé, il reste quelques opérations avant qu'il soit prêt à être utilisé. 
Cela se fait par un script bien commode:

{% highlight bash %}
$ sudo mysql_secure_installation
{% endhighlight %}

Il déroule un certain nombre de questions. A chaque ligne il suffit de sélectionner l'entrée par défaut en presant "Entrée".
A la ligne "New password", saisissez le mot de passe _root_ que vous souhaitez donner à votre base.


{% highlight bash %}
Enter current password for root (enter for none): 
Set root password? [Y/n]
New password: 
Re-enter new password: 
Password updated successfully!
Remove anonymous users? [Y/n] 
Disallow root login remotely? [Y/n] 
Remove test database and access to it? [Y/n] 
Reload privilege tables now? [Y/n] 
{% endhighlight %}

Vous pouvez désormais utiliser MariaDB.


Si vous utilisez PhpMyAdmin
---------------------------

Par défaut, le temps de session est limité à 1440 secondes soit 24 minutes.
Pour l'augmenter, il faut rajouter cette ligne dans le ficher _/etc/phpMyAdmin/config.inc.php_

{% highlight bash %}
$cf['Servers'][$i]['LoginCookieValidity'] = 28800;
{% endhighlight %}

28800 correspond à au temps en secondes soit 8h.