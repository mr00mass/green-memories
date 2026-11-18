# Green Memories

Jeu de plateformes et d'action 2D dans le style metroidvania, avec une interface en français, une forêt dessinée sur canvas et un éditeur de niveaux intégré.

**[Jouer en ligne](https://mr00mass.github.io/green-memories/)** · **[Consulter le code source](index.html)**

## Sommaire

- [Prérequis (requirements)](#prérequis-requirements)
- [Clonage](#clonage)
- [Environnement et lancement](#environnement-et-lancement)
- [Code source](#code-source)
- [Commandes et gameplay](#commandes-et-gameplay)
- [Sauvegardes](#sauvegardes)
- [Déploiement](#déploiement)
- [Dépannage](#dépannage)

## Prérequis (requirements)

Pour jouer :

- Un navigateur récent prenant en charge JavaScript et Canvas 2D, par exemple Chrome, Firefox, Edge ou Safari. JavaScript doit être activé.
- Un clavier pour les commandes ci-dessous ; des boutons tactiles sont également prévus.
- Le stockage local du navigateur autorisé pour conserver les sauvegardes et les niveaux créés.

Pour récupérer et modifier le projet :

- Git, uniquement si vous choisissez le clonage plutôt que le téléchargement ZIP.
- Un éditeur de texte ou de code.
- Facultativement, Python 3 ou Node.js avec npm pour lancer un serveur local.

**Aucun framework, paquet npm, outil de compilation ou serveur applicatif n'est nécessaire au jeu.** Il n'y a pas de `package.json`, de commande de build ni de variables d'environnement à renseigner.

Le HTML, le CSS, le JavaScript et les images embarquées sont réunis dans `index.html`. Une feuille de style **Google Fonts** est toutefois chargée depuis Internet pour la police Cormorant Garamond. Si elle est indisponible, le jeu utilise une police de remplacement, Georgia ou une police serif. Une connexion Internet est nécessaire pour charger cette police externe, mais pas pour utiliser sa police de remplacement.

## Clonage

```bash
git clone https://github.com/mr00mass/green-memories.git
cd green-memories
```

Sans Git, ouvrez [le dépôt GitHub](https://github.com/mr00mass/green-memories), choisissez **Code > Download ZIP**, puis extrayez l'archive avant de lancer le jeu.

## Environnement et lancement

### Ouverture directe

Ouvrez `index.html` dans votre navigateur, par exemple avec un double-clic. Aucun build n'est requis.

Cette méthode ne nécessite pas de serveur, mais le comportement des sauvegardes sous une adresse `file://` varie selon le navigateur. Pour travailler régulièrement sur le jeu, préférez un serveur local et conservez la même adresse et le même port.

### Serveur local avec Python 3

Depuis le dossier du dépôt :

```bash
python -m http.server 8000 --bind 127.0.0.1
```

Si votre installation utilise la commande `python3`, remplacez `python` par `python3`. Sous Windows, `py -3` peut également être utilisé.

Ouvrez ensuite **http://127.0.0.1:8000/**. Arrêtez le serveur avec `Ctrl+C` dans le terminal.

### Alternative avec Node.js et npm

Si Node.js et npm sont déjà installés :

```bash
npx serve . --listen tcp://127.0.0.1:8000
```

Cette commande peut demander l'autorisation de télécharger le paquet `serve` lors du premier lancement ; ce paquet sert uniquement au serveur local et n'est pas une dépendance du jeu. Ouvrez la même adresse **http://127.0.0.1:8000/**, puis utilisez `Ctrl+C` pour arrêter le serveur.

## Code source

Le dépôt contient :

```text
green-memories/
├── index.html    # Jeu : interface, styles, logique, dessins et images embarquées
├── README.md     # Documentation
└── .gitignore    # Fichiers système exclus de Git
```

Tout le code du jeu se trouve dans [index.html](index.html), sans fichier JavaScript ou CSS séparé. Les images utilisées par le code sont embarquées sous forme de données base64 ; une grande partie du décor est dessinée directement avec Canvas 2D.

### Repères pour modifier le fichier

| Zone ou symbole à rechercher | Rôle |
| --- | --- |
| `<style>` | Présentation des menus, fenêtres, panneaux d'édition et commandes tactiles. |
| `<canvas id="cv">` et éléments HTML suivants | Surface de jeu et interface. |
| `<script>` | Logique JavaScript, regroupée dans une fonction immédiatement exécutée. |
| `WW`, `GY`, `DX`, `PL` | Dimensions et repères du niveau initial, position de la porte et plateformes. |
| `upd(dt)` | Déplacements, sauts, attaques, collisions et progression de la partie. |
| `BGS`, `MIDS`, `FRONTS` | Définitions des fonds et des différents plans du décor. |
| `bg()`, `world()` et fonctions `draw...` | Dessin de la scène, des personnages et des éléments. |
| `frame(ts)` | Boucle d'animation utilisant `requestAnimationFrame`. |
| `goMenu()`, `startGame()` | Navigation vers le menu et démarrage d'une partie. |
| `saveSlot()`, `loadSlot()`, `save()`, `loadSB()` | Sauvegardes de partie et stockage du monde créé. |
| `WORLD`, `newZone()`, `enterSand()` | Données des zones et entrée dans l'éditeur. |

Pour changer une couleur ou un décor, cherchez sa définition ou sa fonction de dessin. Pour ajuster la physique et les vitesses, commencez par `upd(dt)`. Pour modifier les menus, repérez leurs éléments HTML et leurs styles.

Après une modification, enregistrez `index.html` puis rechargez la page. Il n'y a ni compilation ni rechargement automatique configuré. Vérifiez la console du navigateur et testez le menu, une partie et l'éditeur pour repérer les régressions.

Pour transmettre le jeu, fournissez le fichier `index.html` complet. Pour transmettre le projet avec les instructions, fournissez le dossier du dépôt ou son archive ZIP. Les sauvegardes personnelles du navigateur ne sont pas incluses dans le fichier source.

## Commandes et gameplay

### Objectif du niveau initial

Vainquez le **Gardien d'ombre**, ramassez la clé qu'il laisse tomber, puis rejoignez la **porte dorée à droite**. Les cinq fleurs de l'interface représentent les points de vie.

Le personnage peut sprinter, effectuer un double saut, faire un dash, attaquer à l'épée et utiliser une liane saisissante. Les effets lumineux font partie de l'ambiance visuelle ; il n'y a pas de jauge de lumière à recharger.

### Clavier

Le code utilise `KeyboardEvent.code` : les touches sont reconnues par leur position physique, et non uniquement par leur lettre. Le tableau distingue les dispositions QWERTY et AZERTY standard ; les flèches, Espace, X et F évitent plusieurs différences de disposition.

| Action | QWERTY | AZERTY standard |
| --- | --- | --- |
| Se déplacer | Flèches gauche/droite ou A / D | Flèches gauche/droite ou Q / D |
| Sprinter | Maj + déplacement | Maj + déplacement |
| Sauter / double saut | Espace, flèche haut ou W | Espace, flèche haut ou Z |
| Dash | Z (`KeyZ`) | W (`KeyZ`) |
| Attaquer à l'épée | X, J ou K | X, J ou K |
| Liane saisissante | Q ou F | A ou F |
| Pause / reprise | P | P |
| Carte en mode monde | M (`KeyM`) | Virgule (`KeyM`) |
| Recommencer après mort ou victoire | R ou bouton « Rejouer » | R ou bouton « Rejouer » |

Maintenir la touche de saut permet de moduler la hauteur. Échap met en pause le niveau initial, mais peut revenir au menu en exploration du monde : utilisez P pour la pause. Dans l'éditeur, la carte est aussi accessible par son bouton.

### Éditeur et exploration

Le **Bac à sable** permet de construire des zones avec plateformes, murs, dangers, eau, boue, ennemis, personnages et décors. Il permet également de relier les zones par des passages, de choisir un point de départ et de tester le niveau.

L'option **Explorer le monde** sert à parcourir le monde créé dans l'éditeur ; il faut d'abord en créer un. Les objets « Laine » peuvent servir de points d'accroche à la liane, et les plantes de sauvegarde placées dans le monde permettent une sauvegarde automatique à proximité.

## Sauvegardes

Les données sont enregistrées dans le `localStorage` du navigateur : trois emplacements de partie, une sauvegarde automatique et le monde de l'éditeur. Le menu pause propose une sauvegarde manuelle.

- Elles restent locales au navigateur et au profil utilisés : il n'y a pas de compte joueur ni de synchronisation en ligne.
- Le site GitHub Pages, une ouverture directe du fichier et un serveur local n'utilisent pas nécessairement le même stockage.
- Changer d'adresse ou de port, par exemple passer de `localhost` à `127.0.0.1`, change l'origine et donc les sauvegardes accessibles.
- Effacer les données du site peut supprimer les parties et les niveaux créés. En navigation privée ou si le stockage est bloqué, la persistance n'est pas garantie.
- Une sauvegarde conserve un état de progression ; elle ne constitue pas une copie exhaustive de tous les états temporaires de la scène.

## Déploiement

Le dépôt public utilise **GitHub Pages**, configuré avec **Deploy from a branch**, branche **`main`**, dossier **`/ (root)`**. Le fichier servi est `index.html` à la racine, sans étape de build propre au jeu.

Pour publier une modification du jeu, depuis une copie locale à jour et après l'avoir testée :

```bash
git status
git pull --ff-only origin main
git diff -- index.html
git add index.html
git diff --cached
git commit -m "Met à jour Green Memories"
git push origin main
```

Avant de poursuivre, vérifiez que vous êtes sur `main`, que les différences correspondent à vos modifications et qu'aucun secret ou fichier personnel n'est inclus. L'envoi nécessite un compte autorisé à écrire dans le dépôt. Si Git signale une divergence ou un conflit, résolvez-le sans forcer l'envoi.

Pour une modification de documentation seule, ajoutez et validez `README.md` à la place d'`index.html`, avec un message adapté.

Après l'envoi, suivez le déploiement dans l'onglet **Actions** du dépôt et vérifiez l'adresse dans **Settings > Pages**. Une fois le déploiement terminé, rechargez [le jeu en ligne](https://mr00mass.github.io/green-memories/).

Modifier une copie du fichier sur son ordinateur ne met pas automatiquement GitHub à jour : seule la version effectivement envoyée au dépôt est publiée.

## Dépannage

- **Page blanche ou canvas vide :** ouvrez les outils de développement du navigateur et consultez la console. Vérifiez que JavaScript est activé, que le navigateur est récent et que `index.html` a été téléchargé intégralement. Avec un ZIP, extrayez les fichiers avant de les ouvrir.
- **Le jeu ne démarre pas immédiatement :** le démarrage d'une partie comporte une transition d'environ 1,5 seconde.
- **Touches différentes de l'aide affichée :** consultez le tableau AZERTY/QWERTY ci-dessus ; le jeu utilise les positions physiques des touches. Cliquez hors des champs de saisie de l'éditeur avant de tester les commandes.
- **Sauvegarde introuvable :** utilisez le même navigateur, le même profil et la même adresse qu'auparavant. Pour éviter les particularités de `file://`, utilisez un serveur local avec une adresse et un port fixes. Ne videz pas les données du site pour tenter de récupérer une sauvegarde.
- **Police différente hors ligne :** la police Google Fonts n'a pas pu être chargée ; la police de remplacement est utilisée.
- **Commande `python`, `python3` ou `npx` inconnue :** utilisez l'ouverture directe, ou installez l'outil correspondant si vous souhaitez un serveur local.
- **Port 8000 déjà occupé :** choisissez un autre port, par exemple 8001, dans la commande du serveur et dans l'adresse du navigateur. Les sauvegardes associées à l'ancien port ne sont pas automatiquement transférées.
- **Ancienne version en ligne :** vérifiez le commit sur `main`, la réussite du déploiement Pages et effectuez un rechargement sans cache.
