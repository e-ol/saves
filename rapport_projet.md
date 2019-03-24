## Contribution - effaçage de `null` parfois affiché dans un article
Un blog sous Ghost est alimenté par des articles. Le bug étudié ici consiste
au fait qu'un article sous Ghost pouvait afficher `null` sous certaines
conditions lorsqu'il était vide (cf.
[issue #10612](https://github.com/TryGhost/Ghost/issues/10612)).
Bien entendu, le comportement attendu visait à afficher un contenu vide. Pour
reproduire le bug, il fallait, lors de la création dudit article (sous la partie
d'administration du site) :
- cliquer sur le champs de titre
- cliquer sur le champs de contenu, écrire un espace et le supprimer
- publier l'article
Ces étapes de reproduction du bug étaient déjà donnés par un des développeur de
Ghost lorsqu'on a commencé à le corriger.

Il a été nécessaire de comprendre le workflow de Ghost pour gérer ses articles
pour comprendre agir et régler le bug au bon endroit : le problème peut venir de
l'affichage de l'article ou de sa sauvegarde.  

### Workflow de Ghost sur la création d'articles
Tout d'abord, l'article est sauvegardé sous un format Mobiledoc. Le principe de
Mobiledoc est de conserver les données et les métadonnées des articles d'un
journal, d'un blog etc. sous un format basé sur le Json, pour ensuite les rendre
(notamment) au format HTML, DOM et TEXT - grâce à l'utilitaire
[Mobiledoc-Kit](https://github.com/bustle/mobiledoc-kit).

Ghost utilise aussi un système de sauvegarde sous Ajax pour enregistrer
l'article en temps réel pendant sa rédaction. On a supposé que le problème
pouvait aussi venir de ce système, étant donné les étapes de reproduction :
l'article est correctement vide à la base - il est donc correctement
initialisé - et n'affiche `null` qu'après avoir été modifié, donc après avoir
fait appel à la fonction de sauvegarde automatique.

Ghost utilise des thèmes pour gérer le front-end. Pour faire l'interface entre
les thèmes et le contenu présent dans les bases de données, Ghost utilise des
"helpers". Les helpers agissent comme un moteur de templates : dans le contenu
HTML, préciser `{{content}}` fait appel au helper du même nom (sous
`Ghost/core/server/helpers`). Dans le thème par défaut de Ghost, l'article est
visible sous plusieurs formes : des "cards", où un résumé de l'article est
présenté, sont visibles sur la page d’accueil et des pages où l'article est
présenté dans son intégralité. Le bug ne s'affichait que dans le deuxième cas.
Vu que les helpers appelés dans les deux cas pour afficher le contenu ne sont
pas les mêmes - respectivement `excerpt` et `content` - on en a déduit que le
problème venait probablement (au moins en partie) de là : si deux helpers censés
afficher presque la même chose (`excerpt` renvoi juste les premières lignes que
`content` est censé renvoyer).

### Résolution
A cause du manque de documentation vis-à-vis du fonctionnement du code de Ghost,
que ce soit au niveau des commentaires dans les fichiers source ou au niveau de
la documentation sur internet, il a fallu chercher dans les fichiers un à un
pour parvenir à comprendre comment Ghost gère ses messages, et c'est ce qui a
été le plus long et fastidieux. Cependant, Ghost possède une arborescence de
fichiers relativement claire et explicite, facilitant la tâche.

Avec toutes ces informations en main, la suite n'est pas compliquée et tiens en
trois lignes (cf. [pull request](https://github.com/TryGhost/Ghost/pull/10617)) :
``` javascript
// In `Ghost/core/server/helpers/content.js`
if (this.html === null) {
    this.html = '';
}
```
A la suite de la proposition de cette résolution, les mainteneurs de Ghost ont
découvert qu'il y avait également un problème avec leur parser html pour
Mobiledoc. Il devait normalement toujours renvoyer `null` sur un contenu vide
et ensuite l'helper devait se charger d'enlever ce `null` pour  le remplacer
par une chaine vide, comme dans la solution que nous avons proposé (cf.
[réponse de Naz Gargol à la PR](https://github.com/TryGhost/Ghost/pull/10617#issuecomment-473875886))
