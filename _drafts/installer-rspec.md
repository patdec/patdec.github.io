---
layout: post
title: "Installer RSPEC dans un engine"
date: 2016-07-05 23:00:00
categories: [ruby on rails]
---

Installer RSPEC
---------------

Ajouter les ligne suivantes dans votre _engine_name_.spec

my_app/components/my_engine/my_engine.gemspec
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
_my_app/components/my_engine/spec/rails_helper.rb_
{% highlight ruby %}
require File.expand_path('../../config/environment', __FILE__)
{% endhighlight %}
par 
{% highlight ruby %}
require File.expand_path('../dummy/config/environment', __FILE__)
{% endhighlight %}

le dossier _dummy_ simule un application pour pouvoir exécuter les tests.


Il nous reste désormais à préciser Rspec comme framework de test par défaut.

_my_app/components/my_engine/lib/my_engine/engine.rb_
{% highlight ruby %}
module MyEngine
  class Engine < ::Rails::Engine
    isolate_namespace MyEngine

    config.generators do |g|                                      #
      g.test_framework :rspec                                     # Lignes rajoutées
      g.fixture_replacement :factory_girl, dir: 'spec_factories'  #
    end                                                           #
  end
end
{% endhighlight %}


Base de données
---------------

Copier le ficher database.yml à la racine du projet dans spec/dummmy/config


Fix à ajouter sur le fichier spec servant à tester les contrôleurs
------------------------------------------------------------------

En l'état si vous exécutez la commande rspec dans votre engine, vous allez obtenir un message d'erreur vous indiquant que la route inscrite dans le contrôleur n'est pas trouvée.
Rspec va chercher les routes dans l'appli principale. Il faut lui indiquer que les routes sont à prendre dans l'engine.
on ajoutera:

_my_app/components/my_engine/spec/controllers/my_engine/my_controller_spec.rb_
{% highlight ruby %}
require 'rails_helper'

module MyEngine
  RSpec.describe MyController, type: :controller do
    routes { MyEngine::Engine.routes } # <- Ligne à rajouter

    describe "GET #index" do
      it "returns http success" do
        get :index
        expect(response).to have_http_status(:success)
      end
    end

  end
end
{% endhighlight %}

