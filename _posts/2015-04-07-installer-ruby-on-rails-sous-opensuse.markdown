---
layout: post
title:  "Installer Ruby on Rails sous OpenSUSE 13.2!"
date:   2015-04-07 20:54:28
categories: jekyll update
---

L'objectif est d'installer un environnement complet de développement en utilisant les serveurs Apache et Postgres. 
Webrick le serveur web "maison" intégré dans Ruby on Rails demeure excellent pour un site monodomaine. Mais dès lors que notre application devient ambitieuse et qu'elle nécessite l'utilisation de plusieurs domaines, nous devons nous tourner vers un serveur web mieux outillé.
Apache est parfait dans ce rôle.
Nous choisissons aussi Postgres plutôt que SQLite pour la simple raison d'utiliser un serveur SGBDR. Pourquoi Postgres que MariaDB ? Parceque traditionnellement RoR préfère Postgresql et que celui-ci est plus complexe à paramétrer que MySQL.


Installer Ruby
--------------

Ruby peut être installé de multiples façons. Par le système de la distribution ou bien par un gestionnaire de version. Ici nous utiliserons RVM.

### Installer RVM


Avant toute chose, il vous faut récupérer la clé publique de l'auteur. Elle nous permettra de réupérer le script d'installation sur son site.
{% highlight bash %}
$ gpg --keyserver hkp://keys.gnupg.net --recv-keys 
409B6B1796C275462A1703113804BB82D39DC0E3
{% endhighlight %}

Nous sommes prêts à lancer la commande d'installation de RVM:
{% highlight bash %}
$ \curl -sSL https://get.rvm.io | bash -s stable
{% endhighlight %}

Exécutons la commande suivante afin d'avoir la commande "rvm" disponible immédiatement:
{% highlight bash %}
$ source ~/.profile
{% endhighlight %}

### Installer Ruby

La commande suivante nous permet  de lister les versions de ruby disponibles:
{% highlight bash %}
$ rvm list known
{% endhighlight %}

Nous choisissons d'installer la version 2.2.1
{% highlight bash %}
$ rvm install 2.2.1
{% endhighlight %}
Le mot de passe administrateur peut vous être demandé pour l'installation de packages nécessaires à la compilation de ruby:

> root password required for 'zypper --gpg-auto-import-keys refresh':

Entrez-le et laisser les opérations suivre leurs cours.

Mettons par défaut cette version:
{% highlight bash %}
$ rvm --default use 2.2.1
{% endhighlight %}

### Installer Rails

{% highlight bash %}
$ gem install rails
{% endhighlight %}

Installer Postgresql
--------------------

### Installer le paquet postgresql

{% highlight bash %}
$ sudo zypper install postgresql-server
{% endhighlight %}

Reste à démarrer le service:
{% highlight bash%}
sudo systemctl start postgresql.service
{% endhighlight %}

### Paramétrage

Afin d'utiliser le service, il faut au préalable exécuter quelques opérations.
Pour cela, il faut se connecter en tant qu'utilisateur _postgres_ tout en étant loggé sous l'utilisateur _root_.
{% highlight bash %}
$ su
# su - postgres
{% endhighlight %}

Vous êtes à présent dans le shell de l'utilisateur _postgres_. Vous allez vous connecter au serveur postgresql à l'aide la commande client suivante:
{% highlight bash %}
$ psql
{% endhighlight %}

Cela vous permettra de créer un utilisateur ou rôle _postgres_ identique à celui utilisé pour votre session Linux. Si votre identifiant de connexion Linux est "lambda" alors votre devez prendre comme identifiant de connexion postres "lambda". Dans mon exemple, mon indetifiant est "patrice".
Exécuter les lignes suivantes. A la place de 'password', indiquez votre propre mot de passe. Veillez à bien conserver les simples quotes.
{% highlight sql %}
# CREATE USER patrice ENCRYPTED PASSWORD 'password' CREATEDB;
# CREATE DATABASE patrice;
# GRANT ALL PRIVILEGES ON DATABASE patrice TO patrice;
{% endhighlight %}

Enfin, pour sortir du client postgres:
{% highlight sql %}
\quit
{% endhighlight %}

Quittez la session _postgres_, pour nous retrouver dans la session _root_
{% highlight bash %}
$ exit
{% endhighlight %}

Il nous reste à éditer le fichier suivant:
{% highlight bash %}
# vim /var/lib/pgsql/data/pg_hba.conf
{% endhighlight %}
Vers la fin du fichier, vous devez avoir les lignes suivantes:
{% highlight bash %}
# TYPE  DATABASE        USER            ADDRESS                 METHOD

# "local" is for Unix domain socket connections only
local   all             all                                  	peer
# IPv4 local connections:
host    all             all             127.0.0.1/32            ident
# IPv6 local connections:
host    all             all             ::1/128                 ident
{% endhighlight %}
Modifier la 1ère ligne non commentée de la façon suivante:
{% highlight bash %}
# TYPE  DATABASE        USER            ADDRESS                 METHOD

# "local" is for Unix domain socket connections only
local   all             all                                  	md5
# IPv4 local connections:
host    all             all             127.0.0.1/32            ident
# IPv6 local connections:
host    all             all             ::1/128                 ident
{% endhighlight %}

Redémarrer le service postgres
{% highlight bash %}
# systemctl restart postgresql.service
{% endhighlight %}

Installer Apache
================

### Installer le paquet Apache

{% highlight bash %}
$ sudo zypper install apache2
{% endhighlight %}

### Installer la gem passenger

{% highlight bash %}
$ gem install passenger
{% endhighlight %}

### Compiler et installer le module passenger

{% highlight bash %}
$ passenger-install-apache2
{% endhighlight %}
Ne sélectionner que Ruby dans la liste proposée.
Le script nous fait remarquer qu'il manque des dépendances pour lancer la compilation. Faisons donc CTRL+C quand on nous y invite pour interrompre le processus.

Installez les paquets suivants:
{% highlight bash %}
$ sudo zypper install libcurl-devel apache2-devel
{% endhighlight %}

et relançez:
{% highlight bash %}
$ passenger-install-apache2
{% endhighlight %}

à la fin de la compilationsn le script nous indique de copier un morceau de code qui correspond au chargement du module passenger par Apache:
{% highlight bash %}
LoadModule passenger_module /home/patrice/.rvm/gems/ruby-2.2.1/gems/passenger-5.0.6/buildout/apache2/mod_passenger.so
<IfModule mod_passenger.c>
  PassengerRoot /home/patrice/.rvm/gems/ruby-2.2.1/gems/passenger-5.0.6
  PassengerDefaultRuby /home/patrice/.rvm/gems/ruby-2.2.1/wrappers/ruby
</IfModule>
{% endhighlight %}

Créeez le fichier suivant et collez-y le bloc ci dessus.
{% highlight bash %}
$ sudo vim /etc/apache2/conf.d/passenger.conf
{% endhighlight %}

### Paramétrer Apache

{% highlight bash %}
$ sudo vim /etc/apache2/vhosts.d/monappli.conf
$ sudo vim /etc/hosts
{% endhighlight %}

### Créer une application rails

{% highlight bash %}
$ rails new monappli
$ cd monappli
$ vim config/databases.yml
$ bundle
$ rake db:setup
$ rake db:migrate
$ sudo service apache2 restart
{% endhighlight %}
