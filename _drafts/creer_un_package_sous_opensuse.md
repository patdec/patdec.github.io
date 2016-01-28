---
layout: post
title:  "Créer un package RPM sous OpenSuse"
date:   2015-06-01 14:58:00
categories: [rpm ,obs, opensuse, administration]
---

Nous allons créer depuis les sources un packet RPM en utilisant OBS.

Prérequis
_________

Il faut créer un compte depuis [OpenSuse Build Service](https://build.opensuse.org).
Il faut également installer le package osc.

{% highlight bash %}
# sudo zypper install osc
{% endhighlight %}

Préparer l'espace de travail
----------------------------

Nous avons créé un compte sur OBS. Nous pouvons récupérer cet espace de travail sur notre machine pour y contruire nos paquets. 
Depuis un dossier qui servira à construire nos packages lançons la commande suivante.

{% highlight bash %}
$ osc checkout home:patdec
{% endhighlight %}

Sur le site OBS, si ce n'est pas fait, ajouter vous comme "maintener" du projet avec le lien "Add user".

Initialiser le package
----------------------

Depuis le site, cliquer sur "Create Package". Donner lui un nom, un titre et une description.
Ici "watchman4".
Ajoutons lui un lien vers la release 4.3.0. sur Github.
Cliquer sur le lien "Add file"
https://github.com/facebook/watchman/archive/v4.3.0.tar.gz

Ensuite depuis la console, faites:

{% highlight bash %}
$ osc up home:patdec
{% endhighlight %}

Nous avons désormais un package vide _watchman4_ dans notre espace de travail.

Créer le fichier spec
---------------------

Commençons par installer rpmdevtools pour obtenir les outils pour générer le fichier spec.

{% highlight bash %}
$ sudo zypper -p http://download.opensuse.org/repositories/devel:/tools/openSUSE_Leap_42.1/ -v in rpmdevtools
{% endhighlight %}

Générons à présent un fichier spec générique:

{% highlight bash %}
$ cd home:patdec/watchman4
$ rpmdev-newspec watchman4
{% endhighlight %}

Editer le fichier spec
----------------------

{% highlight bash %}
$ vim spec 
{% endhighlight %}

{% highlight bash %}
#
# spec file for package watchman4
#
# Copyright (c) 2016 mail@patriceandre.com
#
# All modifications and additions to the file contributed by third parties
# remain the property of their copyright owners, unless otherwise agreed
# upon. The license for this file, and modifications and additions to the
# file, is the same license as for the pristine package itself (unless the
# license for the pristine package is not an Open Source License, in which
# case the license is the MIT License). An "Open Source License" is a
# license that conforms to the Open Source Definition (Version 1.9)
# published by the Open Source Initiative.

# Please submit bugfixes or comments via http://bugs.opensuse.org/
#


# See also http://en.opensuse.org/openSUSE:Specfile_guidelines

Name:           watchman4
Version:        4.3.0
Release:        0
Summary:        A file watching service
License:        Apache License 2.0
Group:          Development/Tools/Other
Url:            https://facebook.github.io/watchman/
Source0:        https://github.com/facebook/watchman/archive/v4.3.0.tar.gz
BuildRequires:  autoconf,automake
#Requires:       

%description
Watchman exists to watch files and record when they change.
It can also trigger actions (such as rebuilding assets) when matching files change.


%prep
%setup -q -n watchman-4.3.0

./autogen.sh
%build
%configure
make %{?_smp_mflags}

%install
make install DESTDIR=%{buildroot}

%files
/usr/bin/watchman
/usr/share/doc/watchman-4.3.0/README.markdown
{% endhighlight %}


{% highlight bash %}
$ osc add *
$ osc commit
{% endhighlight %}