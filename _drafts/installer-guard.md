---
layout: post
date: 2016-07-21 20:32:00
title: "Installer Guard pour RSPEC"
categories: [ruby on rails]
---

Rien de plus fastidieux que de taper la commande rspec. Guard se charge "d'écouter" pour vous les modifications apportées dans vos fichiers spec et d'exécuter le cas échéant commande rspec.
En ce qui me concerne, comme utilisateur de KDE/Plasma, il s'interface très bien avec le gestionnaire de notification.
A chaque fin d'exécution, une notification s'ouvre dans le systray pour me donner le résumé.


Installer Guard
---------------

_my_app/components/my_engine/my_engine.gemspec_
{% highlight ruby %}
s.add_development_dependency  'guard-rspec', '~> 4.7.2'
{% endhighlight %}


Exécuter depuis la racine de votre engine:

{% highlight bash %}
bundle install
guard init rspec
{% endhighlight %}

Cette dernière commande crée un fichier _Guardfile_ à la racine de l'engine.
Pensez à rajouter _rspec_ pour le  remplit automatiquement dans le cadrede l'utilisation avec Rspec.

Editer Guardfile
----------------

Avec le fichier généré ci-dessus, tout marche à merveille sauf pour les vues.
J'ai dû rajouter la ligne suivante pour que les feature tests se déclenche lors d'une modification dans un fichier view. 

_my_app/components/my_engine/Guardfile_
{% highlight ruby %}
# Rails config changes
  watch(rails.spec_helper)     { rspec.spec_dir }
  watch(rails.routes)          { "#{rspec.spec_dir}/routing" }
  watch(rails.app_controller)  { "#{rspec.spec_dir}/controllers" }
  watch(rails.views)           { "#{rspec.spec_dir}/features" } # <- Nouvelle ligne
{% endhighlight %}


Et pour finir

{% highlight bash %}
guard
{% endhighlight %}
