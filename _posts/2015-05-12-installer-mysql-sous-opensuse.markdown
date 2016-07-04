---
layout: post
title:  "Installer MySQL sous OpenSUSE"
date:   2015-05-12 20:54:28
categories: [mysql, mariadb ,serveur]
---

Depuis que MySQL est tombé dans l'escarcelle d'Oracle, les distributions le remplacent peu à peu par un fork de plus en plus prisé: MariaDB. Nous allons passer en revue quelques points de son installation. 

Installer MariaDB
-----------------

Lançons la commandes suivante:
{% highlight bash %}
$ sudo zypper install mariadb
{% endhighlight %}

Paramétrer MariaDB
------------------

Le serveur MariaDB installé, quelques opérations doivent être réalisées avant son utilisation. 
Exécutons le script suivant:

{% highlight bash %}
$ sudo mysql_secure_installation
{% endhighlight %}

Ce script propose d'initialiser un environnement Mysql sécurisé. Quelques questions vont être posées.  
A la ligne "New password", saisissons le mot de passe que nous souhaitons donner à notre utilisateur _root_ Mysql. Aux 
autres, nous conservons le choix par défaut.


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

MariaDB est prêt à l'emploi.


Détour par PhpMyAdmin
---------------------------

Par défaut, le temps de session est limité à 1440 secondes soit 24 minutes.
Pour l'augmenter, il faut rajouter cette ligne dans le ficher _/etc/phpMyAdmin/config.inc.php_

{% highlight bash %}
$cf['Servers'][$i]['LoginCookieValidity'] = 28800;
{% endhighlight %}

28800 correspond à au temps en secondes soit 8h.
