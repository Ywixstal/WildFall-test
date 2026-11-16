# Wildfall

Jeu 2D multijoueur compétitif dans une forêt : classes, boss, alliances et bases à trophées.
Prototype jouable dans le navigateur (solo pour l'instant).

## Lancer le prototype

Télécharger le dépôt entier (bouton vert **Code → Download ZIP**), le dézipper, puis ouvrir
`index.html` dans un navigateur. Le dossier `sprites/` doit rester à côté de `index.html`.

> Détail : ouvert en double-clic, le jeu fonctionne entièrement, mais la fine bordure d'herbe
> autour des lacs, des camps et des châteaux n'est pas dessinée, à cause d'une restriction du
> navigateur sur les fichiers locaux. Servi depuis un serveur web, le rendu est complet.

## Organisation du dépôt

```
index.html      → le prototype (code du jeu, sans images intégrées)
sprites/        → sprites PNG rangés par catégorie (voir sprites/LISEZMOI.txt)
DESIGN.md       → la "bible" du jeu : règles, classes, boss, décisions
NOTES_IA.md     → carnet de passation entre Claude, ChatGPT et moi
```

## Règle d'or

Le dépôt est la source de vérité. Une idée n'est "validée" que lorsqu'elle est
écrite dans `DESIGN.md`. Le reste n'est qu'une discussion.
