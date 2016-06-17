# Du développement à l'architecture Front-End

Le document suivant présente les recommandations en matière de code (HTML, CSS, JS) pour écrire un code réutilisable, propre et facile à maintenir, spécifiquement sous Drupal. (standards crées dans le cadre de mon travail de Front End Architecte à [Studio Umi](https://www.studio-umi.jp/))

Ce document a été traduit du japonais et a été créé avec la collaboration de [Hayato Goto](https://github.com/gh640). Merci GOTO-san :)

**Disclaimer:**

- Les recommandations présentées dans ce document proviennent de réflexions personnelles et sont inspirées par certains développeurs Front End ([Remerciements](#awesome-people) en fin de page). Nous sommes ouverts aux suggestions, remarques et questions. N'hésitez pas à me contacter : [Twitter](http://www.twitter.com/c_c_l_) ou même à contribuer :D
- Je n'ai jamais travaillé en français donc n'hésitez pas à m'indiquer les erreurs de traduction aussi ;)

## Table des matières

1. [Traductions](#translations)
2. [Pourquoi utiliser l'architecture Front-End](#archi-front-end)
3. [Arborescence SMACSS](#smacss)
4. [Structure des fichiers](#structure)
5. [Règles](#rules)
6. [Self-check](#self-check)
7. [Remerciements](#awesome-people)

<a name="translations"></a>
## Traductions

- English *coming soon*

<a name="archi-front-end"></a>
## Pourquoi utiliser l'architecture Front-End

**Référence：** [Front-End Architecture for Design Systems](http://www.goodreads.com/book/show/25977680-frontend-architecture-for-design-systems)  

- Augmenter la productivité en augmentant la vitesse de codage
- Offrir un code plus facile à maintenir  
- Offrir un code plus compréhensible
- Structurer son code de manière correcte
- Produire un code pouvant être réutilisable  

<a name="smacss"></a>
## Arborescence SMACSS

Afin de faciliter la compréhension du code, et la facilité à retrouver les fichier, nous structurons nos fichiers de la manière suivante (basé sur les standards SMACSS) :

**Référence :** [SMACSS](https://smacss.com/)

1. Variables SASS/SCSS -- `_variable.scss`
2. Mixin SASS/SCSS -- `_mixin.scss`
3. Base --  `_base.scss`
4. Layout -- `_layout.scss`
5. Components (composé des éléments parents et éléments enfants) --  `_component.scss` *possibilité de diviser en plusieurs parties si plus pratique*
6. Single-style -- `_single-element` *composé d'éléments uniques, non réutilisables*
6. State -- `_state.scss` *si utilisé*
7. Thème -- `_theme.scsss` *si utilisé*

<a name="structure"></a>
## Structure des fichiers

*sites/all/theme/MY-THEME* (sous Drupal 7)

- css/
- docs/ → Documentation [Hologram](https://github.com/trulia/hologram)
- images/
- lib/ → su-mixins.scss (Librairie Umi mixin)
- sass/
  - base/ → base.scss
  - components/ → components.scss, utility-class.scss（classes réutilisables）
  - single-style/ → components-drupal.scss(pour le style de l'interface admin de Drupal seulement), single-element.scss (éléments au style non-réutilisable)
  - layout/ → layout.scss
  - state/ →　state.scss
- mixins.scss  
- styles.scss  
- variables.scss  
- templates/ → page.tpl.php, .region--footer.tpl.php (etc, sous Drupal)
- hologram_config.yml

<a name="rules"></a>
## Règles

1. [Base](#base)
2. [Noms](#naming)
  - Variables
  - Types de variables
3. [Layout](#layout)
4. [Tableau des règles de style](#table-rules)
  - Single Style
5. [State](#state)
6. [Commentaires](#comments)
 - Block
 - Sass/SCSS
 - CSS
7. [Ajout de classe](#add-class)
 - Principe de responsabilité unique (SRP)
 - Principe de source fiable et unique (SSOT)

<a name="base"></a>
### Base

- On ne stylise pas les tags html suivant directement h1, h2, h3, h4, h5, (et autres tags selon design) on les stylise avec la classe de leur conteneur devant afin d'éviter les vues et les blocks de prendre le même style que les titres de contenu.
- Dans ce fichier on utilise uniquement des tags HTML.
- On ne fait pas d'imbrication (du moins le moins possible) d'éléments HTML pour éviter que le code soit confus.

<a name="naming"></a>
### Noms

#### Noms de classes

- Pour les classes utilisées dans le Layout on utilise le préfixe `l-`; exemple :　`.l-main`  [SMACSS Layout rules](https://smacss.com/book/type-layout)  
- Component (pour les *components* qui sont parents on utilise la/e CamelCase (exemple : `.BlockCalendar`). La raison pour laquelle on utilise un système de noms différents pour les *components* qui sont parents et leurs enfants est pour le **name space**, il est plus facile de faire des changements sans aucune incidence sur d'autres éléments. (Voir aussi [BEM](https://en.bem.info/methodology/))
  - Pour plus d'informations concernant le choix de la Camel Case : Nicolas Gallagher - [Questioning Best practices - Video](https://www.youtube.com/watch?v=XQWOKBBJ114)
  - [The purification of web development](https://css-tricks.com/w3conf-nicolas-gallagher-questioning-best-practices/)
  - [Undescores in class and ID](https://developer.mozilla.org/en-US/docs/Web/Guide/CSS/Underscores_in_class_and_ID_Names)  
- Pour les components enfants (Elements BEM) on garde les noms conventionnels : `.block-calendar-title`  

#### Variables

Pour une compréhension rapide, on utilise un préfixe :

```
$color-white: #fff;

$border-white: 1px solid #fff;
```

On évite la **SUR SEMANTIQUE**, les noms de classes doivent être compréhensibles au premier coup d'oeil pour un développeur qui n'a jamais touché votre code auparavant. Il est complètement possible d'utiliser des noms de variables tels que : `color-red`. Cependant il est recommandé pour la compréhension de penser à faire ceci en cas de besoin :

```
$color-black: #000;
$color-base-text: $color-black;
```

##### Type de variables

- colors（jusqu'à 9）
- border
- border-radius
- font-family
- font-size
- margin/padding (création d'une variable type : `$space-small` avec une valeur réutilisable)
- indent
- *Variables utilisées dans notre librairie su-mixin pour notre cas*

<a name="layout"></a>
### Layout

#### Séparation entre les components et les éléments du layout

Sur une approche du CSS Orienté Objet, il est important de faire la distinction entre les éléments du layout (typiquement la position) et components.

Préfixe `l-`  

```
<nav class="l-menu"></nav>
```

<a name="table-rules"></a>
### Tableau des règles de style

|          | LAYOUT     | COMPONENT PARENT  | COMPONENT ENFANT      |
|----------|------------|-------------------|-----------------------|
| BG       | x          | o                 | o                     |
| DISPLAY  | o          | x                 | o                     |
| FLOAT    | o          | x                 | x                     |
| HEIGHT   | o          | x                 | x                     |
| MARGIN   |   top: X   | x                 | top : x               |
| PADDING  | o          | o                 | o                     |
| POSITION | o          | RELATIVE          | x                     |
| WIDTH    | o          | x                 | Valeur relative       |

*o : OK, x : NON*

Layout -> Component -> Element
- Le style de layout ne doit pas influer sur les components
- Pour éviter les margin inconsistantes on n'utilise pas les margin-top
- On n'utilise pas de margin sur les components enfants
- Si possible on évite de faire une imbrication de plus de deux étages de components enfants（Standards D8）  
- Note: il est possible de faire un layout dans un layout si besoin (exemple : `.l-main-menu` dans `.l-header`)
- On rajoute des fichiers templates au besoin (Drupal)
- On ne stylise pas avec des id, car ils/elles surplombent les classes [Measure specifity of ID and classes](https://www.smashingmagazine.com/2007/07/css-specificity-things-you-should-know/#how-to-measure-specificity)

#### Single style

Fichier CSS utilisé pour styliser les éléments non réutilisables et qui sont uniques au projet, tel que le menu principal ou les fils d'Ariane etc... Nous plaçons également les fichiers de style pour l'interface administration de Drupal car ils sont en général uniques et non réutilisés, dans le cas où ils doivent être réutilisés, nous les plaçons dans *components*.

Dans cette situation il n'y a pas d'intérêt à séparer le layout des components car le style n'est pas censé être réutilisé.

<a name="state"></a>
### State

- On utilise le fichier state pour les classes de Javacript et on sépare les classes JS des classes pour le style : **.is-state**

<a name="comments"></a>
### Commentaires

#### Block

```
/**
 * Titre
 *
 * Détails si nécessaire
 *
 * @path chemin/de-la-page
 *
 * @module machine_name_du_module_sous_Drupal
 *
 * TODO / @see
 */
 ```

#### Pour les fichiers Sass/SCSS uniquement

` // Mon super commentaire méga utile`

#### Pour les fichiers CSS uniquement

`/* Mon super commentaire extrêmement utile*/`

<a name="add-class"></a>
### Ajout de classes

#### Principe de responsabilité unique / Single responsibility principle (SRP)

```
<div class="calendar">
  <h2 class="calendar-header">Calendrier</h2>
</div>
<div class="blog">  
  <h2 class="blog-header">Blog</h2>
</div>
```
```
.calendar-header {
  color: red;
  font-size: 2em;
}
.blog-header {
  color: red;
  font-size: 2.4em;
}
```

**On fait qu'une seule chose et on la fait bien**

#### Principe de source fiable et unique / Single source of truth (SSOT)

```
<div class="blog">  
  <h2 class="blog-header">Blog</h2>
  <div class="calendar">
    <h2 class="calendar-header">Calendar</h2>
  </div>
</div>
```
```
.calendar-header {
  color: red;
  font-size: 2em;
}
.blog .calendar-header {
  font-size: 1.6em;
}
.blog-header {
  color: red;
  font-size: 2.4em;
}
```
**On groupe les styles entre eux et on cesse de dispatcher divers styles dans tout le code**

### Mauvaises pratiques

**Basées sur les standards Drupal**

#### Style dépendant du contexte  

- BAD :  
`.page-about .component {}`　　

- GOOD :  
`.about-component {}`  

#### Style dépendant de la structure HTML  

- BAD :   
`nav > ul > li > a {}`   

→ Style non stable, compréhension des conséquences difficile et style non réutilisable

#### Les noms de classes trop généraux

- BAD :   
`.title {}`  
`.widget {}`  

- GOOD :  
`.this-specific-block-title {}`  
`.this-specific-widget {}`  

#### Les classes qui réinitialisent le style

- BAD :  
`.this-component-no-margin {}`  

→　Le problème vient clairement de la base de style, il vaut mieux ajouter une classe qui ajoute des margins quand nécessaire dans cette situation.

<a name="self-check"></a>
## Self-check

Je tiens à remercier à nouveau [Hayato Goto](https://github.com/gh640) pour avoir pris le temps de relire cette self-check-sheet et l'avoir réorganisée de manière compréhensible :)

### Structure des fichiers

- Les fichiers et dossiers sont ils bien organisés ?

### Documentation et commentaires

- Le code est il suffisamment documenté pour que le code soit repris de manière efficace et sans problème de compréhension ?
- La documentation comporte-t-elle des exemples pertinents ?
- Les fichiers sont ils suffisamment commentés ?
- Les commentaires sont ils écrits en une seule et même langue ?
- Après compilation les commentaires affichés sont ils explicites selon le fichier ? (les commentaires CSS sont bien affichés et les commentaires SCSS ne le sont pas sur les fichiers CSS)
- Les références à d'autres fichiers sont elles écrites ? (Pour la maintenance)

### Styles dans les fichiers
- Les styles sont ils groupés selon les standards :
  - Le fichier `_base.scss` contient il uniquement des tags HTML ?
  - Le fichier `_layout.scss` contient il les classes qui correspondent aux standards stipulés plus haut ? (`.l-class`)
- Les règles générales sont elles respectées ?
  - Le principe de responsabilité unique (SRP) est il respecté ?
  - Le principe de source fiable et unique (SSOT) est il respecté ?

### Noms
- Les noms de classe respectent ils les normes ? (ex: CamelCase, hyphens etc..)
  - Si les standards ne sont pas respectés, un commentaire explique-t-il la raison ?
- Les id sont elles évitées pour styliser les éléments ?
  - Si un(e) id est utilisé(e), un commentaire justifie-t-il le choix ?
- Les noms de variables respectent ils les standards (anglais compréhensible pour toute nationalité + ordre des mots (typiquement ordre grammatical français (couleur du texte : color-text)))
- Les classes utilisées pour le js respectent-elles les standards ? (`.js-class` pour les fichiers `.js` et `.is-state` pour le fichier `state.scss`)
- Les classes de JS sont elles uniquement utilisées pour du JS (et non pour styliser les éléments) ?

### Layout et components
- Le fichier layout comprend-il uniquement des éléments de style pour le positionnement (voir Tableau des règles) ?
- Le layout n'a-t-il aucune incidence sur le style des components et vice versa ?
- Le style des components n'est il aucunement dépendant de la localisation ? (stylisation en fonction de la page)
- `margin-top` est il évité ? Si utilisé, le choix est il justifié par un commentaire ?
- `padding` n'est il pas utilisé pour positionner les éléments ?
- Les imbrications de plus de 2 components sont elles évités ? Si utilisés, le choix est il justifié par un commentaire ?

<a name="awesome-people"></a>
## Remerciements

Merci à [Hayato Goto](https://github.com/gh640) (une troisième fois n'est jamais de trop) pour avoir travaillé sur les standards avec moi de nombreuses heures et d'avoir écouté et compris mes explications approximatives, d'avoir apporté un nouveau point de vue à tout cela et d'avoir testé et proposé des modifications.

Merci à [Lazuli](http://www.twitter.com/lazulirondoudou) d'avoir pris le temps de checker sur Internet comment on disait "nesting" en français :D et [Mr Pingouino](http://www.twitter.com/MrPingouino) pour avoir proposé un terme des plus obscurs mais pour avoir essayé quand même :)

Merci à [Micah GodBolt](https://twitter.com/micahgodbolt), [Harry Roberts](http://csswizardry.com/) et [Nicolas Gallagher](http://nicolasgallagher.com/) pour avoir inspiré ce travail :)
