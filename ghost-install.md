# Installer Ghost et Ghost-Admin depuis les sources
Dérivé de [docs.ghost.org/install/source/](https://docs.ghost.org/install/source/)

## Prérequis
##### Node.js dans l'une des [versions supportées par Ghost](https://docs.ghost.org/faq/node-versions/)
En utilisant nvm :\
Nvm vous permet d'avoir plusieur versions de node.js, les installer facilement
et de les changer tout aussi facilement. Tout d'abord, tappez
`curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh |
bash`.
L'installation ajoute les lignes d'exportation des variables dans l'un des
fichiers suivants : `~/.bash-profile`, `~/.zshrc`, `~/.profile`, `~/.bashrc`.\
Si plusieurs de ces fichiers sont définits dans votre environnement, faites
attention à ce que les lignes soient installées dans le bon : j'utilise ZSH au
lieu de Bash et ZSH ne lit pas les lignes de `~/.bashrc` mais de `~/.zshrc`.\
Ouvrez un nouveau terminal pour avoir les exports pris en compte. Vous pouvez
aussi taper :
```
export NVM_DIR="${XDG_CONFIG_HOME/:-$HOME/.}nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
```
Si `nvm --version` retourne la version de nvm, comme 0.33.11, vous pouvez
continuer. Sinon, essayez de recommencer.\
Maintenant, il faut installer la bonne version de node via nvm. Pour Ghost,
j'utilise le dernier patch de la version 8, soit la 8.12.0. Tappez simplement
`nvm install 8.12` puis `nvm use 8.12`. Pas d'erreur ? Parfait !

##### Yarn
`curl -o- -L https://yarnpkg.com/install.sh | bash -s -- --rc` pour faire
simple. Votre gestionnaire de paquets est susceptible de posséder un paquet de
Yarn, si c'est le cas, c'est encore plus simple. Je vous invite à aller
visiter [le site officiel](https://yarnpkg.com/en/docs/install) pour voir les
autres méthodes d'installation. Faites un `yarn -v` pour voir si tout est bien
installé.

##### Autres dépendances
`yarn global add knex-migrator grunt-cli ember-cli bower` installera les
dernière dépendances nécessaires à Ghost.

## Fork
J'ai déjà créé les forks du projet sous [e-ol/Ghost](https://github.com/e-ol/Ghost.git)
et [e-ol/Ghost-Admin](https://github.com/e-ol/Ghost-Admin.git). Vous m'envoyez
votre identifiant Github, je vous invite dans le fork et vous acceptez
l'invitation. Je ne suis pas sûr de ce que vous avez le droit de faire sur mon
fork sans ça. Sinon, vous pouvez le fork vous-même mais ça complique inutilement
les choses...

## Ghost
Finalement, on va installer Ghost. Pour simplifier, les liens SSH ont été
remplacés par des liens HTTPS.\
D'abord : `git clone --recurse-submodules https://github.com/TryGhost/Ghost.git
&& cd Ghost`
Si tout se passe bien :
```
git remote rename origin upstream
git remote set-url upstream https://github.com/TryGhost/Ghost.git
```
Pour finir, si vous voulez utiliser mon fork :
`git remote add origin https://github.com/e-ol/Ghost.git`
Sinon, faites la commande suivante sur votre fork **en prenant soin de changer
<identifiant> par votre identifiant Github** : `git remote add origin
https://github.com/<identifiant>/Ghost.git`.

Pour Ghost-Admin maintenant :
`cd core/client` (Ghost/core/client est le répertoire de Ghost-Admin)
```
git remote rename origin upstream
git remote set-url upstream https://github.com/TryGhost/Ghost-Admin.git
```
Puis *mon* fork : `git remote add origin https://e-ol/Ghost-Admin.git` **ou** le
votre (en changeant *<identifiant>* encore) : `git remote add origin
https://<identifiant>/Ghost-Admin.git`.
Un dernier détail : `git checkout master` puis `cd ../..` pour revenir dans de
répertoir de Ghost.\
On peut vérifier plus tard que les remotes sont correctement configurés en
lançant `git remote -v` sous le répértoire de Ghost et sous celui de Ghost-Admin
(sous core/client).

Si tout est bon, on peut tapper `yarn setup` pour finaliser l'installation (
vérifiez bien que vous êtes sous le répertoire de Ghost et pas sous core/client
avant ça).

## Lancement
Une fois l'installation terminée, on peut lancer `grunt dev` pour lancer le
service.\
Ghost est disponible sous http://localhost:2368 et la partie admin sous
http://localhost:2368/ghost
