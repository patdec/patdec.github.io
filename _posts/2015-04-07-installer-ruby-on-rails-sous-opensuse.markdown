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
$ passenger-install-apache2
{% endhighlight %}

### Installer le module passenger

{% highlight bash %}
$ sudo vim /etc/apache2/conf.d/passenger.conf
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



You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. You can rebuild the site in many different ways, but the most common way is to run `jekyll serve`, which launches a web server and auto-regenerates your site when a file is updated.

To add new posts, simply add a file in the `_posts` directory that follows the convention `YYYY-MM-DD-name-of-post.ext` and includes the necessary front matter. Take a look at the source for this post to get an idea about how it works.

Jekyll also offers powerful support for code snippets:

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

Check out the [Jekyll docs][jekyll] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll’s dedicated Help repository][jekyll-help].

[jekyll]:      http://jekyllrb.com
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-help]: https://github.com/jekyll/jekyll-help
