---
layout: post
title:  "Utiliser les variables d'environnement de bash sous Ruby on Rails"
date:   2015-11-04 00:27:00
categories: [ruby on rails ,bash, opensuse]
---

Quand on est plusieurs à travailler sur une applications, nous pouvons nous trouver confronter à quelques problèmes d'ajustements entre notre environnement et celui des autres développeurs. Par exemple nous récupérons un fichier de configuration depuis Github, mais les paramètres ne coïncident pas avec ceux de notre environnement de travail. Que faire dans ce cas-là ? Prenons le database.yml pour illustrer cela.

Remplacer toutes les variables spécifiques
------------------------------------------

L'identifant et le mot de passe de la base de données locale qui sert au développement sont souvent des données propres à chaque développeur.Il s'avère donc nécessaire de les variabiliser.

Prenons les données suivantes comme example:

_my_app/config/database.yml_
{% highlight yaml %}
default: &default
  host: 'localhost'
  username: 'patrice'
  password: 'password'
  adapter: postgresql
  encoding: unicode
  pool: 5
{% endhighlight %}

Je modifie les lignes correspondantes à host,username et password:

{% highlight yaml %}
default: &default
  host: <%= ENV['DATABASE_HOST'] %>
  username: <%= ENV['DATABASE_USERNAME'] %>
  password: <%= ENV['DATABASE_PASSWORD'] %>
  adapter: postgresql
  encoding: unicode
  pool: 5
{% endhighlight %}
 
 
 
 
Ajouter les variables dans le .profile
--------------------------------------

ajoutons y ces lignes:

_/home/patrice/.profile_
{% highlight bash %}
export DATABASE_HOST=localhost
export DATABASE_USERNAME=patrice
export DATABASE_PASSWORD=password
{% endhighlight %}
