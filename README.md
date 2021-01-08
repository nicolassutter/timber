# Timber, pourquoi écrire du Twig dans Wordpress ?

Nous partons à la découverte de `Timber` pour `Wordpress`: un plugin ou une dépendance `Composer` qui permet de mettre en place une architecture MVC au sein de Wordpress.

De ce fait, il est possible de séparer le template HTML du code logique PHP. `Timber` réalisé par [Upstatement](https://upstatement.com/) permet concrètement d'écrire du Twig pour remplacer le templating PHP de `Wordpress`.

## Quel est l'intérêt ?

En séparant la logique et le template, nous nous retrouvons avec un code non seulement plus propre mais également plus maintenable et facile à comprendre.

En prenant l'exemple ci-dessous, nous pouvons voir qu'il est plus facile de lire et comprendre Twig que PHP lorsqu'il s'agit de templating.

```php
<?php get_header(); ?>
<?php if( have_posts() ) : while( have_posts() ) : the_post(); ?>
<article>
  <h1><?php the_title(); ?></h1>
  <div>
    <?php the_content(); ?>
  </div>
</article>
<?php endwhile; endif; ?>
<?php get_footer(); ?>
```

```html
{% extends "base.twig" %}
{% block content %}
<article>
  <h1>{{ post.title }}</h1>
  <div>
      {{ post.content }}
  </div>
</article>
{% endblock %}
```

## Comment le mettre en place ?

L'installation de `Timber` est en réalité très simple et n'a pas besoin d'être effectuée sur un `Wordpress` vierge. Il est donc possible de le mettre en place dans un projet déjà existant sans risquer de tout casser...

Il existe deux solutions pour l'installer: avec `Composer` ou via `Wordpress`. Le résultat est le même mais il faut noter que la version `Wordpress` n'est pas toujours la plus récente.

### Avec Composer

Il suffit de rentrer la commande suivante, à la racine de votre projet `Wordpress` ou bien à la racine de votre thème.

```sh
composer require timber/timber
```

Ensuite, si ce n'est pas déjà le cas, il faudra charger les dépendances `Composer` dans le projet. Il suffit d'inclure le code ci-dessous dans votre `functions.php`.

```php
<?php
// Dépendances composer.
require_once __DIR__ . '/vendor/autoload.php';

// Initialisation de Timber.
new Timber\Timber();
```

Et c'est terminé, `Timber` est maintenant installé dans le projet et prêt à être utilisé.

### Avec le plugin officiel

Il suffit de se rendre sur la page du [plugin](https://wordpress.org/plugins/timber-library/) et de l'installer comme d'habitude dans l'admin de `Wordpress`. Une fois téléchargé et activé, `Timber` est prêt à être utilisé.

## Utilisation concrète

Comme dit précédemment, `Timber` met en place une architecture MVC (Modèle, Vue, Contrôleur).

Au sein de `Wordpress`, le modèle est géré par la `Template Hierachy` et la `WP Query`. Nous n'avons donc qu'à nous occuper de la Vue et du contrôleur.

La vue sera gérée par `Timber` et `Twig`, et le contrôleur par le code `PHP`.

### Affichage d'un post

De base, `Wordpress` va chercher le fichier `single-post.php`, avec `Timber`, cela ne change pas!

Cependant `single-post.php` ne contiendra que le code `PHP` et pas le template `HTML`.

```php
<?php
$context = Timber::context();
$context['post'] = Timber::get_post();

Timber::render('templates/single-post.twig', $context);
```

Danc cet exemple, le `$context` est un tableau PHP que `Timber` nous génère et qui contient un tas d'infomations utiles dans la plupart des templates. Nous aurons par exemple accès au nom du site, la description du site etc...

Ensuite, nous ajoutons la clé `post` dans ce tableau qui contiendra les données du post en question. `Timber` nous propose sa propre méthode pour le récupérer.

Enfin, nous disons à `Timber` de rendre le template `single-post.twig` en lui passant les informations dont il a besoin.

Dans `single-post.twig` nous accédons au post avec la clé `post`, comme défini dans le fichier PHP. Il nous suffit ensuite d'accéder aux différentes propriétés comme le titre, la description, etc.

```html
{% extends "base.twig" %}
{% block content %}

<h1>{{ post.title }}</h1>

{% endblock %}
```

Cet exemple reprend tout ce dont vous aurez besoin dans la plupart des cas.

Pas si compliqué que ça non ?

### Utilisation de fonctions PHP

Dans un fichier Twig il nous est tout de même possible d'utiliser des fonctions PHP native et Wordpress.
Par exemple `get_header()` et `get_footer()`.

```html
{{ function('get_header') }}
```

### Découpage en block

Dans les exemples précédents, vous aurez peut-être remarqué les mots `extends` et `block`. En effet `Timber` et `Twig` ne permettent pas seulement de gérer le templating dans un seul et même fichier.

Partons du principe que nous avons un fichier `base.twig` regroupant des éléments `HTML` qui seront présent partout sur le site comme le header et le footer.

```html
{{ function('get_header') }}
  {% block content %}
  {% endblock %}
{{ function('get_footer') }}
```

En reprenant l'exemple de tout à l'heure, nous spécifions à Timber que nous voulons "compléter" le fichier `base.twig`. Ensuite il suffit de définir le bloc dans lequel nous voulons injecter le contenu. Ici c'est le bloc `content`.

```html
{% extends "base.twig" %}
{% block content %}

<h1>{{ post.title }}</h1>

{% endblock %}
```

Notre résultat sera donc:

```html
{{ function('get_header') }}
  <h1>{{ post.title }}</h1>
{{ function('get_footer') }}
```

### La boucle for

La plus grosse simplification apportée par Timber est la boucle `for`.

En effet, plus besoin d'ouvrir et fermer des tags `<?php` et `?>` partout, jusqu'à s'y perdre.

Nous nous retrouvons avec une boucle similaire avec ce que nous avons dans `Vue.js` par exemple.

```html
{% for post in posts %}
<article>
  <h1>{{ post.title }}</h1>
  <div>
      {{ post.content }}
  </div>
</article>
{% enfor %}
```

## Le starter theme

En installant `Timber` dans un projet vierge, il est vivement conseillé d'utiliser le [stater thème](https://github.com/timber/starter-theme) officiel.

Il vous donnera directement l'architecture de dossiers correcte pour la maintenabilité du thème. Il vous facilitera notamment tout le travail à faire pour rendre un fichier Twig avec les informations correspondantes etc.

## Conclusion

`Timber` propose une approche à `Wordpress` plus propre et "future-proof" que la méthode traditionnelle. D'ailleurs, il fontionne parfaitement dans un workflow `Wordplate` et rend le développement `Wordpress` beaucoup plus moderne et agréable.

Enfin, si vous êtes un développeur `Javascript`, et surtout `Vue`, la syntaxe `Twig` sera comme une seconde nature.

Il est difficile de ne pas recommander Timber étant donné qu'il ne fait que rajouter des fonctionnalités sans nous forcer la main.

Et vous, avez-vous déjà pu tester Timber ?
