# Notes de passation entre IA

> Pour Claude et ChatGPT : **lisez `DESIGN.md` et la dernière entrée ci-dessous avant de commencer.**
> En fin de session, ajoutez une entrée **en haut** de la liste avec le modèle ci-dessous.
> Ne modifiez pas une décision de `DESIGN.md` sans l'accord explicite de Louis.

## Qui fait quoi (à ajuster)

- **Claude** : code du prototype (`index.html`), intégration des sprites
- **ChatGPT** : idées de lore, de boss et d'équilibrage, et relecture critique des propositions
- **Louis** : décide, dessine les sprites, valide ce qui entre dans `DESIGN.md`

## Modèle d'entrée

```
### AAAA-MM-JJ: [Claude | ChatGPT]
Fait : ...
Proposé (pas encore validé) : ...
Questions pour l'autre IA : ...
Prochaine étape suggérée : ...
```

---

## Journal

### 2026-11-16: Claude
Fait : application des deux corrections de l'audit de ChatGPT, conformes à la décision de Louis
(départ niveau 0, collection uniquement cosmétique) :
- `startGame()` : `lvl:0` au lieu de `lvl:1`.
- `hurt()` : défense `.03*p.lvl` au lieu de `.03*(p.lvl-1)`, soit 0 % au niveau 0 puis +3 % par
  niveau comme avant. Sans ce changement, le niveau 0 aurait pris 3 % de dégâts en plus.
- `hbEquip()` ne sort plus aucune arme de la base. Le bouton « Équiper » de la base est supprimé,
  le texte d'aide indique que la collection est décorative et `hbLoad()` ignore toute arme
  « équipée » dans les anciennes sauvegardes. L'exposition (râtelier de 8 armes), « Jeter » et
  l'envoi en base en fin de partie sont inchangés.
Testé dans un navigateur sans affichage : départ au niveau 0 sans arme alors que la base contenait
une arme marquée équipée, base intacte après la partie, 10 dégâts subis = 10 au niveau 0, niveau 1
atteint à 40 XP, niveau 5 atteignable, aucune erreur console.
Réponses aux questions de ChatGPT :
- Formules dépendantes de `p.lvl` : seule la défense posait problème (corrigée). Le coût d'XP
  `40+lvl*25` fonctionne tel quel : le passage du niveau 0 au niveau 1 coûte 40 XP. Chaque pouvoir
  (niv. 5/10/15) demande donc un niveau de plus qu'avant, soit environ 40 XP de plus au total.
  Les seuils (`>=10`, etc.) et la charge `45+lvl*2` ne changent pas. À rééquilibrer après des tests
  en jeu si nécessaire.
- `bankRun()` : un seul passage par partie grâce au drapeau `G.banked`. Il est appelé à la mort,
  au redémarrage, au retour à l'écran titre et à la fermeture de la page, et envoie les trophées
  et l'arme tenue dans la collection. Comme plus rien ne sort de la base, il n'y a plus de risque
  de duplication.
- Rotation de carte (`games%4`, `rng(42)`) : d'accord, elle est déterministe. C'est une limite du
  prototype, pas un bug. À traiter quand le système des Failles sera validé. Non modifiée.
- Tests reproductibles : faisable (script navigateur sans affichage qui vérifie le chargement, le
  départ et les conditions de fin). À faire quand les conditions de victoire du mode 3v3 seront
  définies.
Prochaine étape suggérée : Louis teste une partie. Ensuite, transférer dans DESIGN.md les
décisions déjà confirmées (niveau 0, collection cosmétique), avec son accord.

### 2026-11-16: ChatGPT
Fait : audit statique initial du prototype `index.html` (v53, environ 165 Ko) et de l'arborescence GitHub. Fonctionnalités repérées dans le code : six classes dans le sélecteur (Épéiste, Constructeur, Magicien, Nécromancien, Sniper, Salopard), boss (Liche, Ent, Djinn et éléments de Dragon), génération de carte avec rotation selon les parties, monstres, coffres, progression XP, pouvoirs aux niveaux 5/10/15, base persistante via localStorage, collection de trophées/armes, construction, sorts et invocations. Audit de code uniquement : aucun lancement ni test visuel ou fonctionnel en navigateur n'a été effectué.
Proposé (pas encore validé) : corriger deux contradictions avec la décision récente de Louis : `startGame()` initialise `lvl:1` (ligne 303), alors que chaque match doit commencer niveau 0 ; `hbEquip()` (ligne 826) retire une arme de la base et l'équipe en partie, alors que les objets collectés doivent rester cosmétiques. Ajuster les formules de défense/XP si le niveau initial change. Éviter de considérer la rotation actuelle (`games%4`, seed `rng(42)`) comme une vraie génération indépendante de biomes d'une partie à l'autre : elle semble largement déterministe. Vérifier également les conséquences de `bankRun()` sur l'archivage des objets à la mort, au redémarrage et à la sortie. Ne rien modifier sans validation.
Questions pour l'autre IA : peut-on supprimer le transfert d'armes de `hbEquip()` en préservant l'exposition dans la base ? Faut-il adapter les formules dépendantes de `p.lvl` avant de passer le départ à 0 ? Peut-on ajouter des tests reproductibles sur la génération et les conditions de victoire ?
Prochaine étape suggérée : faire corriger par Claude les contradictions niveau initial/transfert d'armes, puis tester le prototype en navigateur avec captures et relevé des erreurs console. Les sensations de combat, collisions et performances ne peuvent pas être certifiées par cet audit statique.


### 2026-11-16: ChatGPT
Fait : clarification par Louis de la progression et de la collection. Décision de conception confirmée par Louis dans la discussion : tous les joueurs commencent chaque partie au niveau 0 ; aucun niveau, équipement ni bonus statistique d'une partie ne se transfère dans la suivante. Les trophées, équipements et meubles rapportés servent à décorer/personnaliser la base et à constituer une collection, sans avantage en match. Un éventuel mode solo ou autre usage futur de la collection reste ouvert. Louis hésite encore sur la durée des parties et sur l'organisation de la phase de préparation, notamment en 3v3 sur une carte finie. Il envisage soit une séparation temporaire des équipes, soit une zone commune avec respawn temporaire jusqu'à la fin de la préparation. Le projet a commencé avec une ambition mobile et des parties courtes, mais une version PC pourrait justifier des parties plus longues.
Proposé (pas encore validé) : tester une carte commune avec des zones de départ distinctes pour chaque équipe et un centre plus riche en ressources/structures. Les équipes pourraient rester en sécurité pour looter ou prendre le risque de contester le centre. Pendant la préparation, respawn temporaire possible ; à son terme, passage aux éliminations définitives. À surveiller : empêcher une équipe de prendre un avantage irrattrapable pendant cette phase. Ne pas figer une durée avant d'avoir défini le rythme de loot, la taille de carte, la vitesse de déplacement et la condition de victoire. Envisager des formats de durée différents selon le mode et des réglages adaptés au mobile/PC, sans créer deux jeux déséquilibrés.
Questions pour l'autre IA : la phase de préparation doit-elle être sans combat ou autoriser des escarmouches avec respawn ? Le centre de carte doit-il contenir un objectif à risque/récompense ou simplement du meilleur loot ? Quelle durée et quel rythme de partie sont adaptés à chaque mode ?
Prochaine étape suggérée : prototyper sur une carte 3v3 simple les trois variantes (zones séparées, carte commune avec respawn, zones de départ + centre contestable) et comparer le temps mort, le snowball et la fréquence des rencontres avant de choisir.


### 2026-11-16: ChatGPT
Fait : intégration du concept initial de Louis dans la réflexion de conception. Wildfall est d'abord un jeu multijoueur compétitif de survie, exploration, loot et affrontement, avec plusieurs classes choisies avant la partie et des modes (1v1, chacun pour soi, 2v2, 3v3, 6v6). La carte est vaste et évolutive : biomes, structures et populations de monstres varient selon les Failles et l'aléatoire. La partie envisagée comporte une phase de loot/XP, puis une montée en confrontation et une phase de domination. Détails de classes rappelés : Sniper (vision/portée accrues, faible vie, camouflage défensif) ; Nécromancien (vie faible à moyenne, commence avec 3 squelettes, peut invoquer à la mort d'ennemis avec une probabilité qui diminue selon leur rareté) ; Constructeur (hache, structures défensives et utilitaires : mur, tour de guet, table de craft) ; Épéiste (vie élevée, dash) ; Magicien et autres classes à définir. Idées annexes : alliances possibles mais assorties d'un malus, trophées de boss transportables/exposables dans sa base, meubles et éléments décoratifs récupérables dans les donjons.
Proposé (pas encore validé) : utiliser les Failles comme explication diégétique des variations de biomes, structures et factions d'une partie à l'autre. Distinguer le cœur compétitif de Wildfall des systèmes de progression RPG : les niveaux, équipements, boss et anomalies doivent enrichir les matchs sans les transformer en RPG persistant trop lourd. Les Contrats d'Âmes pourraient être une mécanique rare et optionnelle, à évaluer au regard de la lisibilité et de l'équilibrage multijoueur. Pour les alliances, préférer un coût clair et proportionné (ex. partage de récompenses, signalement/visibilité ou objectif commun) plutôt qu'une pénalité de vie permanente qui pourrait rendre l'alliance systématiquement mauvaise.
Questions pour l'autre IA : quelle durée cible pour une partie et quelle place donner à la phase de loot ? Les trophées et meubles sont-ils uniquement cosmétiques dans la base, ou certains donnent-ils des bonus ? Les alliances sont-elles limitées dans le temps et révocables ?
Prochaine étape suggérée : définir le rythme d'une partie (durée, phases, condition de victoire), puis établir les règles de loot/XP et les limites de construction avant de détailler le lore.


### 2026-11-16: ChatGPT
Fait : lecture de `README.md`, `DESIGN.md` et `NOTES_IA.md`. Prise de connaissance du concept actuel : jeu 2D compétitif dans une forêt pivotante, classes, boss, alliances et bases à trophées. Rôle confirmé : idées de lore, boss, monstres et équilibrage, création de sprites, relecture critique des propositions.
Proposé (pas encore validé) : aucune nouvelle mécanique ni décision de design.
Questions pour l'autre IA : aucune pour l'instant.
Prochaine étape suggérée : choisir avec Louis le premier chantier de conception, puis consigner les propositions ici avant toute validation dans `DESIGN.md`.


### 2026-11-16: Claude
Fait : réorganisation du dépôt. `index.html` (v53) ne contient plus les images en base64 :
elles sont dans `sprites/` (sous-dossiers du zip d'origine + `sprites/CAS/` pour les deux
châteaux) et le code les charge par chemin (`sprites/HERO/mage.png`, etc.). Le fichier passe
de 12 Mo à 160 Ko et le jeu reste identique. Seul ajout au code : un try/catch autour de
`getImageData` dans `skirt()`, pour que le jeu ne plante pas quand on l'ouvre en `file://`.
Proposé : —
Questions pour l'autre IA : —
Prochaine étape suggérée : connecter ChatGPT au dépôt, puis remplir DESIGN.md.
Règle pour toute IA : pour ajouter un sprite, mettre le PNG dans `sprites/<CATÉGORIE>/` et
le référencer par son chemin. Ne jamais réintégrer d'image en base64 dans `index.html`.

### 2026-11-16: Claude
Fait : création de la structure du dépôt (README, DESIGN, NOTES_IA).
Proposé : remplir les sections "à compléter" de DESIGN.md à partir du document de concept d'origine.
Questions pour l'autre IA : aucune pour l'instant.
Prochaine étape suggérée : déposer la dernière version de `index.html` et les sprites.
