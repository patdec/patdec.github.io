---
layout: post
title:  "Créer une application Rails fonctionnelle"
date:   2015-09-03 01:17:28
categories: [serveur, postgresql, ruby on rails]
---

Nous avons vu précedemment comment mettre en place un environnement de 
développement pour Ruby on 
Rails et créer les conditions de son exécution. Nous allons nous lancer dans 
notre première application Rails 

### Créer une application rails

Ouvrons un terminal et entrons donc la commande suivante.

{% highlight bash %}
$ rails new monappli
{% endhighlight %}

L'opération prend quelques minutes.
Le squelette de base de l'application Rails se met en place ainsi que le 
téléchargement des gems indispensables.

Si nous voulons l'interroger dans notre navigateur, il nous reste encore 
quelques paramétrages systèmes. 2 choses surtout:
- créer un hôte virtuel Apache basé sur une adresse IP
- renseigner notre fichier /etc/hosts pour faire correspondre notre adresse IP 
avec un nom de domaine plus sympathique.


### Ajustements avec Apache

Créons un fichier de configuration Apache dans le dossier /etc/apache2/vhosts.d.
Appelons le du nom de notre application: _monappli.conf_

{% highlight bash %}
$ sudo vim /etc/apache2/vhosts.d/monappli.conf
{% endhighlight %}

Collons-y le texte ci dessous en prenant soin de l'adapter à vos spécifications:

{% highlight bash linenos %}
<VirtualHost 127.0.0.2:80>
    ServerName monappli.local

    # Tell Apache and Passenger where your app's 'public' directory is
    DocumentRoot /home/patrice/Workspace/monappli/public

    # Relax Apache security settings
    <Directory /home/patrice/Workspace/monappli/public>
      Options -MultiViews
      # Uncomment this if you're on Apache > 2.4:
      Require all granted
    </Directory>
</VirtualHost>
{% endhighlight %}

Nous specifions une adresse IP: 127.0.0.2. Ainsi dans un navigateur, le fait de saisir l'IP nous permet d'interroger notre appli rails. 
Mais nous n'allons pas nous arrêter là; nous préférons saisir un nom de domaine. Nous allons alors éditer le fichier /etc/hosts.
Il nous permettra de faire correpondre un nom de domaine arbitraire à cette adresse IP.

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

### Dialoguer avec PostgreSQL


{% highlight bash %}
$ cd monappli
$ vim config/databases.yml
$ bundle
$ rake db:setup
$ rake db:migrate
$ sudo service apache2 restart
{% endhighlight %}


