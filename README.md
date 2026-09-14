# Album Board

Un petit Kanban à deux pour suivre l'avancement de l'album. Quatre colonnes —
**Backlog → Prêt → En cours → Fini** — des tâches qu'on assigne à *Mathieu* ou
*Guillaume*, et c'est tout.

👉 **Le board : https://guillaumecharles-ch.github.io/album-board/**

## Comment ça marche

Pas de serveur, pas de base de données. La page est un fichier HTML statique
servi par GitHub Pages, et toutes les tâches vivent dans [`board.json`](board.json)
dans ce dépôt. Quand tu bouges une carte, la page réécrit `board.json` via l'API
GitHub — donc chaque modif est un commit, et on voit les mêmes tâches tous les deux.
La page se rafraîchit toute seule toutes les 15 secondes.

Sans token, la page est en **lecture seule** : les boutons d'ajout, les flèches et
le glisser-déposer sont désactivés, pour qu'on ne puisse pas croire qu'on a créé
une tâche alors que rien ne part sur GitHub. Pour modifier, il faut donner à la
page un token GitHub personnel — une fois par navigateur.

Si une modif ne peut pas être enregistrée tout de suite (réseau coupé, token
expiré), elle est gardée dans le navigateur et repart au chargement suivant ; un
bandeau indique combien de modifs sont encore en attente.

## Setup — Guillaume (propriétaire du dépôt)

Token *fine-grained* : **https://github.com/settings/personal-access-tokens/new**

- *Repository access* → **Only select repositories** → `album-board`
- *Permissions* → onglet **Repositories** → **Add permissions** → `Contents` →
  **Read and write** (l'onglet *Account* reste à 0 ; *Metadata: Read-only*
  s'ajoute tout seul, c'est normal)
- *Expiration* → ce que tu veux (1 an, c'est bien)
- **Generate token**, puis copie-le : il ne s'affiche qu'une fois

## Setup — Mathieu (collaborateur)

⚠️ La procédure n'est **pas** la même : les tokens *fine-grained* ne peuvent
accéder qu'aux dépôts dont on est soi-même propriétaire. Comme `album-board`
appartient à Guillaume, un token fine-grained de Mathieu ne verrait même pas le
dépôt. Il lui faut un token **classic**.

1. Accepter l'invitation de collaborateur (elle arrive par mail, ou sur
   https://github.com/guillaumecharles-ch/album-board/invitations).
2. Créer un token classic : **https://github.com/settings/tokens/new**
   - *Note* → `album-board`
   - *Expiration* → ce que tu veux
   - *Select scopes* → cocher **`public_repo`** uniquement (suffisant : le dépôt
     est public ; inutile de cocher `repo`, qui donnerait accès à tous tes
     dépôts privés)
   - **Generate token**, puis copier — il ne s'affiche qu'une fois

Dans les deux cas : ouvrir le board, bouton **Token** en haut à droite, coller,
**Enregistrer**.

Le token reste dans le `localStorage` de ton navigateur. Il n'est jamais envoyé
ailleurs qu'à l'API GitHub et n'est jamais écrit dans le dépôt. À refaire sur
chaque appareil (ordi, téléphone).

⚠️ Le dépôt est public, donc le contenu de `board.json` — les titres des tâches —
est visible par tout le monde. Le token, lui, ne l'est pas.

## Utilisation

- **Ajouter** : « + Ajouter une tâche » en bas d'une colonne. `Entrée` valide,
  `Maj+Entrée` fait un retour à la ligne, `Échap` ferme.
- **Déplacer** : glisser-déposer une carte entre colonnes (ou les flèches `←` `→`,
  plus pratiques sur téléphone).
- **Assigner** : clique sur la pastille du nom pour tourner
  Guillaume → Mathieu → non assigné.
- **Renommer** : clique sur le titre, tape, `Entrée`.
- **Supprimer** : `✕`.

Si vous modifiez tous les deux en même temps, la page recharge la dernière version
depuis GitHub et rejoue votre modif par-dessus — rien ne s'écrase.

La pastille en haut indique l'état : *Synchronisé*, *Enregistrement…*,
*Lecture seule* (pas de token) ou un message d'erreur.

## Bidouiller

Tout est dans [`index.html`](index.html) — un seul fichier, sans dépendance ni
build. Les colonnes et les prénoms sont en haut du `<script>` :

```js
var PEOPLE = ["Mathieu","Guillaume"];
var COLS = [
  {id:"backlog", label:"Backlog"},
  {id:"pret",    label:"Prêt"},
  {id:"encours", label:"En cours"},
  {id:"fini",    label:"Fini"}
];
```

Un `git push` sur `main` redéploie le site.
