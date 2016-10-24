---
layout: post
title: "Installer SLIM dans un engine"
date: 2016-07-19 21:49:00
categories: [ruby on rails]
---

Installer Slim
--------------

Ajouter les ligne suivantes:

_my_app/components/my_engine/my_engine.gemspec_
{% highlight ruby %}
s.add_dependency "slim-rails", "~> 3.1.0"
{% endhighlight %}


Ensuite, vous devez ajouter:

_my_app/components/my_engine/lib/my_engine.rb_
{% highlight ruby %}
require "slim-rails"
{% endhighlight %}

Pour quelle raison ? Contrairement à une application Rails, les engines ne chargent pas automatiquement les gems indiquées dans le fichier gemspec ci-dessus. Il faut spécifier leur chargement.

Paramétrer votre engine pour utiliser Slim
-----------------------------------------

Afin que par défaut un ficher .slim soit généré au lieu d'un .erb dans les views à chaque appel du generator rails, il faut ajouter quelques lignes dans le fichier de configuration de l'engine:

_my_app/components/my_engine/lib/my_engine/engine.rb_
{% highlight ruby %}
module MyEngine
  class Engine < ::Rails::Engine
    isolate_namespace MyEngine

    config.generators do |g|
      g.test_framework :rspec
      g.fixture_replacement :factory_girl, dir: 'spec_factories'
      g.template_engine :slim # <- nouvelle ligne
    end
  end
end
{% endhighlight %}
