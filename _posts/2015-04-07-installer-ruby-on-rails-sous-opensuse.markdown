---
layout: post
title:  "Installer Ruby on Rails sous OpenSUSE 13.2"
date:   2015-04-07 20:54:28
categories: [serveur, postgresql, ruby on rails]
---

L'objectif est d'installer un environnement complet de développement en 
utilisant les serveurs Apache et PostgreSQL. 
Webrick le serveur web "maison" intégré dans Ruby on Rails demeure excellent 
pour un site monodomaine. Mais dès lors que notre application devient 
ambitieuse 
et qu'elle nécessite l'utilisation de plusieurs domaines, nous devons nous 
tourner vers un serveur web mieux outillé.
Apache est parfait dans ce rôle.



Installer le langage Ruby et Ruby on Rails
------------------------------------------

Ruby peut être installé de plusieurs façons. Par le gestionnaire de package de 
la distribution 
ou par un gestionnaire de version indépendant. Nous privilégions la 2 ème option 
pour les raisons suivantes:

*  possibilité d'installer la version de Ruby voulue.
*  possibilité d'utiliser RubyGems et donc la commande "gem" pour installer un 
programme ou une bibliothèque spécifique.

Dans ce tutoriel, nous utiliserons RVM comme gestionnaire de versions de Ruby.

### Installer RVM

[Le site de RVM ](http://rvm.io/)


Avant tout, il nous faut installer la clé publique GPG du mainteneur de RVM, 
Michal Papis. Elle nous 
permettra de vérifier la signature du script d'installation téléchargé depuis 
son site.
{% highlight bash %}
$ gpg --keyserver hkp://keys.gnupg.net --recv-keys 
409B6B1796C275462A1703113804BB82D39DC0E3
{% endhighlight %}

A comparer avec le fichier 
[https://rvm.io/mpapis.asc](https://rvm.io/mpapis.asc)

![GPG Installation](/assets/rvm1.png)

Nous sommes prêts à lancer la commande d'installation de RVM:
{% highlight bash %}
$ \curl -sSL https://get.rvm.io | bash -s stable
{% endhighlight %}

![RVM Installation](/assets/rvm2.png)

Exécutons la commande suivante afin d'avoir la commande "rvm" disponible 
immédiatement sans avoir à ouvrir une nouvelle session bash:
{% highlight bash %}
$ source ~/.profile
{% endhighlight %}

### Installer Ruby

[Le site de Ruby ](https://www.ruby-lang.org)

La commande suivante nous permet  de lister les versions de Ruby disponibles:
{% highlight bash %}
$ rvm list known
{% endhighlight %}

Nous installons la version 2.2.1
{% highlight bash %}
$ rvm install 2.2.1
{% endhighlight %}

Le mot de passe administrateur peut vous être demandé pour l'installation de 
packages nécessaires à la compilation de ruby:

> root password required for 'zypper --gpg-auto-import-keys refresh':

Fournissez-le et laisser les opérations suivre leurs cours. La compilation de 
Ruby peut durer quelques longues minutes.

![Ruby Installation](/assets/rvm5.png)

Indiquons la version de Ruby que nous allons utiliser:
{% highlight bash %}
$ rvm --default use 2.2.1
{% endhighlight %}

L'option "--default" précise que la version de Ruby est mémorisée et sera 
utilisée à chaque connexion.

La commande précédente provoque un message d'erreur:
![RVM Erreur](/assets/rvm11.png)

Nous devons modifier le paramétrage de notre terminal. Pour Konsole de KDE, il 
suffit de rajouter l'option --login après la commande d'appel du bash comme suit:

![RVM Konsole](/assets/rvm6.png)

Ruby est installé. Vous pouvez vous en rendre compte avec la commande suivante:

{% highlight bash %}
$ ruby -v
{% endhighlight %}

![Ruby version check](/assets/rvm10.png)

### Installer Ruby on Rails

[Le site de Ruby on Rails ](http://rubyonrails.org/)

Maintenant que le langage Ruby est disponible sur notre machine, nous allons 
utiliser RubyGems, le gestionnaire de paquet de Ruby pour installer Ruby on 
Rails.

{% highlight bash %}
$ gem install rails
{% endhighlight %}

c'est tout.

Installer la base de données PostgreSQL
---------------------------------------

[Le site de PostreSQL ](http://www.postgresql.org/)

### Installer le paquet postgresql

Nous installerons le package de la distribution. Dans un terminal, nous 
procédons comme suit:

{% highlight bash %}
$ sudo zypper install postgresql-server
{% endhighlight %}

Reste à démarrer le service:
{% highlight bash%}
sudo systemctl start postgresql.service
{% endhighlight %}

### Paramétrage

Afin d'exploiter Postgres, il faut au préalable exécuter quelques opérations.
Connectons nous en tant qu'utilisateur _postgres_. Pour cela il faut avoir les 
droits _root_.
{% highlight bash %}
$ su
# su - postgres
{% endhighlight %}

Nous sommes à présent dans le shell de l'utilisateur _postgres_. Nous allons 
nous 
connecter au service postgresql à l'aide la commande client suivante:
{% highlight bash %}
$ psql
{% endhighlight %}

Nous allons créer un utilisateur ou rôle _postgres_ identique à celui 
utilisé pour notre session Linux. Si votre identifiant de connexion Linux est 
"lambda" alors votre devez prendre comme identifiant de connexion postres 
"lambda". Dans mon exemple, mon indentifiant est "patrice".
Exécuter les lignes suivantes. A la place de 'password', indiquez votre propre 
mot de passe. Veillez à bien conserver les simples quotes.
{% highlight sql %}
postgres=# CREATE USER patrice ENCRYPTED PASSWORD 'password' CREATEDB;
postgres=# CREATE DATABASE patrice;
postgres=# GRANT ALL PRIVILEGES ON DATABASE patrice TO patrice;
{% endhighlight %}

Enfin, pour sortir du client postgres:
{% highlight sql %}
\quit
{% endhighlight %}

Quittons la session _postgres_, pour nous retrouver dans la session _root_:
{% highlight bash %}
$ exit
{% endhighlight %}

Editons le fichier suivant:
{% highlight bash %}
# vim /var/lib/pgsql/data/pg_hba.conf
{% endhighlight %}

Vers la fin, nous avons les lignes suivantes:
{% highlight bash %}
# TYPE  DATABASE        USER            ADDRESS                 METHOD

# "local" is for Unix domain socket connections only
local   all             all                                  	peer
# IPv4 local connections:
host    all             all             127.0.0.1/32            ident
# IPv6 local connections:
host    all             all             ::1/128                 ident
{% endhighlight %}

Modifions les 3 ligne non commentées de la façon suivante:
{% highlight bash %}
# TYPE  DATABASE        USER            ADDRESS                 METHOD

# "local" is for Unix domain socket connections only
local   all             all                                  	md5
# IPv4 local connections:
host    all             all             127.0.0.1/32            md5
# IPv6 local connections:
host    all             all             ::1/128                 md5
{% endhighlight %}

Redémarrons le service postgres
{% highlight bash %}
# systemctl restart postgresql.service
{% endhighlight %}

Installer le serveur web Apache et le module Phusion Passenger
--------------------------------------------------------------

Passenger sera installé comme module d'Apache pour assurer la communication avec 
Rack, l'interface HTTP de Ruby on Rails. 

### Installer le paquet Apache

[Le site d'Apache ](http://www.postgresql.org/)

Nous l'installons via le package de la distribution.

{% highlight bash %}
$ sudo zypper install apache2
{% endhighlight %}

### Installer la gem passenger

[Le site de Phusion Passenger ](https://www.phusionpassenger.com/)

{% highlight bash %}
$ gem install passenger
{% endhighlight %}

### Compiler et installer le module passenger

Lançons le script de compilation:

{% highlight bash %}
$ passenger-install-apache2
{% endhighlight %}

Nous cochons Ruby dans la liste proposée et validons.

![Passenger Installation](/assets/passenger1.png)

Le script nous fait alors remarquer qu'il manque des dépendances pour lancer la 
compilation. Faisons donc CTRL+C, comme on nous y invite, pour interrompre le 
processus.

![Passenger Erreur](/assets/passenger4.png)

Installons donc les paquets pour satisfaire les dépendances:
{% highlight bash %}
$ sudo zypper install libcurl-devel apache2-devel
{% endhighlight %}

et relançons:
{% highlight bash %}
$ passenger-install-apache2
{% endhighlight %}

à la fin de la compilation, le script nous invite à copier quelques lignes 
d'instructions qui sont requises 
pour charger le module passenger dans Apache:
{% highlight bash %}
LoadModule passenger_module 
/home/patrice/.rvm/gems/ruby-2.2.1/gems/passenger-5.0.16/buildout/apache2/
mod_passenger.so
<IfModule mod_passenger.c>
  PassengerRoot /home/patrice/.rvm/gems/ruby-2.2.1/gems/passenger-5.0.16
  PassengerDefaultRuby /home/patrice/.rvm/gems/ruby-2.2.1/wrappers/ruby
  RailsEnv development
</IfModule>
{% endhighlight %}

Créons donc le fichier suivant et collons-y le bloc ci dessus.
Notons qu'une ligne a été rajoutée: _RailsEnv development_
Elle spécifie l'environnement dans lequel l'application Rails sera exécutée.

{% highlight bash %}
$ sudo vim /etc/apache2/conf.d/passenger.conf
{% endhighlight %}

![Passenger Paramétrage](/assets/passenger2.png)

Pour tenir compte du fichier dee configuration de passenger, il nous faut relancer Apache.


{% highlight bash %}
$ sudo systemctl restart apache2
{% endhighlight %}


Conclusion
----------

Après toutes ces étapes, nous sommes désormais prêt à accueillir une première 
application Rails.
Sa création et les ajustements de configuration seront vus dans un autre 
tutoriel. 
