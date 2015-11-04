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


{% highlight yaml %}
default: &default
  host: 'localhost'
  username: 'patrice'
  password: 'password'
  adapter: postgresql
  encoding: unicode
  pool: 5
{% endhighlight %}

devient naturellement:

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

Editons le fichier /home/patrice/.profile 
{% highlight bash %}
vim ~/.profile
{% endhighlight %}

et ajoutons y ces lignes:

{% highlight bash %}
export DATABASE_HOST=localhost
export DATABASE_USERNAME=patrice
export DATABASE_PASSWORD=password
{% endhighlight %}