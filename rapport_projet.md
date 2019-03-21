## Contribution - effaçage de `null` parfois affiché dans un article
Un blog sous Ghost est alimenté par des articles. Le bug étudié ici consiste
au fait qu'un article sous Ghost pouvait afficher `null` sous certaines
conditions lorsqu'il était vide. Bien entendu, le comportement attendu visait à
afficher un contenu vide. Pour reproduire le bug, il fallait, lors de la
création dudit article (sous la partie d'administration du site) :
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
(notamment) au format HTML, DOM et TEXT - grâce à l'utilitaire Mobiledoc-Kit *TODO: Link here*.

Ghost utilise aussi un système de sauvegarde sous Ajax pour enregistrer
l'article en temps réel pendant sa rédaction. On a supposé que le problème
pouvait aussi venir de ce système, étant donné les étapes de reproduction :
l'article est correctement vide à la base - il est donc correctement initialisé -
et n'affiche `null` qu'après avoir été modifié, donc après avoir fait appel à la
fonction de sauvegarde automatique.

Ghost utilise des thèmes pour afficher ses pages HTML. Pour faire l'interface
entre les thèmes et le contenu présent dans
