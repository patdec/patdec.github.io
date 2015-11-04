---
layout: post
title:  "Créer une application Rails fonctionnelle"
date:   2015-09-03 01:17:28
categories: [serveur, postgresql, ruby on rails]
---

Nous avons vu précedemment comment mettre en place un environnement de développement pour Ruby on Rails et créer les conditions de son exécution. Nous allons nous lancer dans notre première application Rails 

Créer une application rails
---------------------------

Ouvrons un terminal et entrons donc la commande suivante.

{% highlight bash %}
$ rails new monapplication
{% endhighlight %}

L'opération prend quelques minutes. Le squelette de base de l'application Rails se met en place ainsi que le téléchargement des gems indispensables.

Si nous voulons interagir avec elle dans notre navigateur comme une appli web classique, il nous reste encore quelques paramétrages systèmes dont:

* créer un hôte virtuel Apache basé sur une adresse IP
* renseigner notre fichier /etc/hosts pour faire correspondre notre adresse IP avec un nom de domaine plus sympathique.


Ajustements avec Apache
-----------------------

Créons un fichier de configuration Apache dans le dossier /etc/apache2/vhosts.d.
Appelons le du nom de notre application: _monapplication.conf_

{% highlight bash %}
$ sudo vim /etc/apache2/vhosts.d/monapplication.conf
{% endhighlight %}

Collons-y le texte ci dessous en prenant soin de l'adapter à nos spécifications:

{% highlight bash linenos=table %}
<VirtualHost 127.0.0.2:80>
    ServerName monapplication.local

    # Tell Apache and Passenger where your app's 'public' directory is
    DocumentRoot /home/patrice/Workspace/monapplication/public

    # Relax Apache security settings
    <Directory /home/patrice/Workspace/monapplication/public>
      Options -MultiViews
      # Uncomment this if you're on Apache > 2.4:
      Require all granted
    </Directory>
</VirtualHost>
{% endhighlight %}

Nous specifions une adresse IP: 127.0.0.2. Ainsi dans un navigateur, le fait de saisir l'IP nous permet d'interroger notre appli rails. Mais nous n'allons pas nous arrêter là; nous préférons saisir un nom de domaine. 
Nous allons alors éditer le fichier /etc/hosts.
Il nous permettra de faire correspondre un nom de domaine arbitraire à cette adresse IP.

{% highlight bash %}
$ sudo vim /etc/hosts
{% endhighlight %}

Et tout en bas de ce fichier ajoutons y la ligne:
{% highlight bash %}
127.0.0.2	www.monappli.local
{% endhighlight %}

![/etc/hosts Paramétrage](/assets/host.png)

Nous devons relancer Apache pour prendre en compte le nouveau vhost:

{% highlight bash %}
$ sudo systemctl restart apache2
{% endhighlight %}

Et maintenant, ouvrons un navigateur, et entrons notre nom de domaine:
 
![Navigateur: erreur](/assets/browser2.png)

Nous avons une erreur 500.
Si nous consultons les logs d'Apache, nous constatons qu'il manque une bibliothèque: "Javascript runtime".

![Apache: erreur](/assets/rails2.png)

Pour corriger ce problème, installons NodeJS

{% highlight bash %}
$ sudo zypper install nodejs
{% endhighlight %}

Et rafraîchissons la fenêtre de notre navigateur. Nous devons à présent voir cette page:

![Apache: erreur](/assets/rails3.png)

C'est signe que notre application fonctionne correctement. Il nous reste maintenant à voir comment interagir avec Postgresql.

Dialoguer avec PostgreSQL
-------------------------

Nous allons commencer par créer notre base de données Postgres et lui donner les bons droits.
Connectons nous au serveur à l'aide du client.

{% highlight bash %}
$ psql 		# Fournir le mot de passe. 
{% endhighlight %}

Lançons les commandes suivantes:

{% highlight sql %}
=> CREATE DATABASE monapplication_development;
=> CREATE DATABASE monapplication_test;
=> GRANT ALL PRIVILEGES ON DATABASE monapplication_development TO patrice;
=> GRANT ALL PRIVILEGES ON DATABASE monapplication_test TO patrice;
{% endhighlight %}

Par convention, la base de utilisée pour le développement est suffixée par __development_, la base de test par __test_. La base de test sert exclusivement aux tests.

Nous allons maintenant indiquer à notre application Rails la base Postgresql qui sera utilisée.
Mettons nous à la racine du projet. Ouvrons le fichier _.config/database.yml_

{% highlight bash %}
$ vim config/database.yml
{% endhighlight %}

et modifions le fichier comme suit:

{% highlight bash linenos=table %}
default: &default
  adapter: postgresql
  host: localhost
  username: patrice
  password: patrice
  encoding: unicode

development:
  <<: *default
  database: monapplication_development

test:
  <<: *default
  database: monapplication_test
{% endhighlight %}

Nous avons paramétré Rails pour utiliser l'adapteur _postgres_. Celui-ci n'est pas installé par défaut. Nous devons ajouter cette ligne dans le fichier Gemfile pour le charger:

{% highlight ruby  %}
gem 'pg'
{% endhighlight %}

et installer le paquet _postgresql-devel_ nécessaire à sa compilation.

{% highlight bash %}
$ sudo zypper install postgresql-devel
{% endhighlight %}

Maintenant, exécutons _bundle_ à la racine de l'application pour télécharger la nouvelle dépendance _pg_

{% highlight bash %}
$ bundle
{% endhighlight %}

Désormais nous sommes paré.
Assurons-nous que tout est bon côté configuration PostgreSQL.

{% highlight bash %}
$ rake db:migrate
{% endhighlight %}

Nous devons constater l'absence de message d'erreur et l'apparition d'un nouveau fichier: _db/schema.rb_

