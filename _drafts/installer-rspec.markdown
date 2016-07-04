---
layout: post
title: "Installer RSPEC dans un engine"
date: 2016-07-02 23:00:00
categories:	 [ruby on rails]
---

Installer RSPEC
---------------

Ajouter les ligne suivantes dans votre _engine_name_.spec

{% highlight ruby %}
s.add_development_dependency "rspec_rails", "~> 3.5.0"
s.add_development_dependency "capybara", "~> 2.7.1"
s.add_development_dependency "factory_girl_rails", "~> 4.7.0"
{% endhighlight %}

Ensuite exécuter les commandes bash suivantes:

{% highlight bash %}
bundle
rails g rspec:install
{% endhighlight %}

Cette dernière commande créé 2 fichiers spec_helper.rb et rails_helper.rb dans le dossier _spec_

Paramétrer RSPEC
----------------

Editons le fichier rails_helper.rb

Nous remplaçons la ligne suivante 
{% highlight ruby %}
require File.expand_path('../../config/environment', __FILE__)
{% endhighlight %}
par 
{% highlight ruby %}
require File.expand_path('../dummy/config/environment', __FILE__)
{% endhighlight %}

le dossier _dummy_ simule un application pour pouvoir exécuter les tests.


Il nous reste désormaos à préciser que Rspec constitue notre framework de test par défaut.
Nous éditons le ficher lib/_engine_name_/engine.rb

{% highlight ruby %}
config.generators do |g|
      g.test_framework :rspec
      g.fixture_replacement :factory_girl, dir: 'spec_factories'
end
{% endhighlight %}


Base de données
---------------

Copier le ficher database.yml à la racine du projet dans spec/dummmy/config

