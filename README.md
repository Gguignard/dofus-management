# Dofus Management

Les trois outils de [@Tenmalexis](https://www.youtube.com/@Tenmalexis) — Calculateur Craft, Forgemagie Optimizer, XP Familier — réunis dans une seule page, avec un référentiel de prix commun et un registre de ventes unique.

Un seul fichier : `index.html`. Aucune dépendance, aucun build.

---

## Lancer

Double-clic sur `index.html` : tout fonctionne, sauf la recherche d'items (le navigateur bloque les appels réseau depuis `file://`). L'app le signale et bascule en saisie manuelle.

Pour retrouver la recherche et le remplissage automatique des recettes, sers le dossier :

```bash
npx --yes serve . -l 4173
```

puis ouvre `http://localhost:4173`. Un dépôt GitHub Pages fait aussi bien l'affaire.

---

## Les neuf onglets

| Onglet | Ce qu'il fait |
|---|---|
| **Bilan** | Solde cumulé, chiffre d'affaires net, rentabilité moyenne, capital immobilisé en vente, meilleures marges par item |
| **Craft** | Recherche d'équipement, recette remplie automatiquement et verrouillée, coût des ressources, comparaison prix marché / ton prix, dossiers |
| **Forgemagie** | Relevé des prix de runes, arbitrage acheter-ou-crafter, session de forge avec coût des runes et ROI |
| **Familier** | Rentabilité de chaque ressource face à la croquette, 2117 ressources filtrables par zone, favoris, planification de lots |
| **Donjon** | Sessions de farm : coût des clefs craftées d'un côté, butin ramassé de l'autre, bénéfice total, par run et par heure, multi-compte compris |
| **Brisage** | Relevés datés : valeur des composants d'un item face au gain de ses runes, coefficient du jour, évolution dans le temps |
| **Prix** | Le référentiel commun : ressources et runes au même endroit |
| **Ventes** | File des items en vente (venus du Craft ou de la Forge), puis registre des ventes conclues |
| **Données** | Export, import, réinitialisation |

### Ce que la fusion apporte

- **Les recettes ne se retouchent pas par accident.** Les ingrédients venus de l'API sont affichés en texte, quantités comprises : seul leur prix est saisissable. Un bouton déverrouille la recette si l'API se trompe, et les lignes ajoutées à la main restent libres.
- **Un prix saisi une fois vaut partout.** Le prix d'une ressource entré dans le Craft remplit la colonne « Prix HDV » du Familier et apparaît dans l'onglet Prix. Plus de triple saisie.
- **Un seul bilan.** Un craft revendu et un item forgé alimentent le même registre : le solde en haut à gauche est ton gain réel, tous ateliers confondus.
- **Le craft nourrit la forge.** Dans une session de forgemagie, « Rattacher à un craft enregistré » reprend son coût comme prix de l'item brut.
- **Rien ne se perd.** Le travail en cours (item, ressources, runes, prix) survit à un rechargement.

---

## Les formules

**Craft** — `coût = Σ(qté × prix)` · `net = prix × 0,98` · `profit = net − coût` · `rentabilité = profit ÷ coût`

**Forgemagie** — `Pa = 3 runes de base` · `Ra = 3 Pa` ou `9 runes de base` · le prix retenu est le moins cher entre le HDV et les crafts · `runes posées = stock + achetées − restantes` · `coût = Σ(posées × prix retenu)` · `ROI = profit ÷ (item brut + coût des runes)`

**Donjon** — une session est un lot de clefs craftées puis couru jusqu'au bout.

```
prix d'une clef  = Σ(quantité × prix) de sa recette
coût de session  = prix d'une clef × nombre de clefs
runs réels       = nombre de clefs ÷ personnages par run
butin            = Σ(quantité × prix) des ressources ramassées
bénéfice         = butin − coût
par run          = bénéfice ÷ runs réels
temps de farm    = durée d'un run × runs réels
par heure        = bénéfice × 60 ÷ temps de farm
```

**Les clefs portent le coût, les runs portent le temps**, et en multi-compte les deux ne se confondent pas : chaque personnage pose sa clef, mais tous courent le même donjon. 400 clefs à 4 personnages coûtent bien 400 clefs, mais ne font que 100 runs — d'où un rendement horaire quatre fois meilleur qu'en solo. Le champ « Personnages par run » vaut 1 par défaut, ce qui laisse les sessions solo inchangées.

Le butin se saisit dans le désordre, au fil des runs. Chaque ligne affiche donc sa **part du butin** avec une barre, et un tri **Les plus rentables** reclasse le tableau — c'est là qu'on voit qu'une seule ressource peut peser la moitié d'une session. L'ordre d'affichage n'affecte pas la saisie : chaque ligne garde son indice d'origine.

La durée d'un run, en minutes, donne le **rendement horaire** — le seul chiffre qui permette de comparer deux donjons entre eux, puisqu'un farm très rentable mais très lent peut valoir moins qu'un farm modeste et rapide. Sans durée renseignée, la colonne reste muette et le reste fonctionne. Le butin est compté brut, sans taxe HDV — c'est ce que valent tes ressources au moment où tu les regardes, pas ce que tu encaisserais en les vendant.

**Brisage** — un relevé compare ce que valent les composants d'un item à ce que rapportent ses runes, à une date donnée.

```
valeur des composants = Σ(quantité × prix estimé)
écart                 = gain de brisage − valeur des composants
rentabilité           = écart ÷ valeur des composants
```

Le coefficient de brisage et le gain en kamas bruts se saisissent à la main : ils dépendent du jour et du serveur. Chaque enregistrement est **horodaté**, et relever le même item plusieurs fois fait apparaître l'écart en points de rentabilité avec le relevé précédent — c'est ce qui permet de repérer un item qui devient intéressant. La liste se trie par date, rentabilité, coefficient ou item.

**Familier** — une croquette vaut toujours **500 XP** ; c'est le prix que tu la paies qui varie, et c'est lui qui fixe l'étalon.

```
prix du point d'XP  = prix d'une croquette ÷ 500
prix limite         = XP de la ressource × prix du point d'XP
prix / XP ressource = prix de la ressource ÷ XP de la ressource
rentabilité         = prix du point d'XP ÷ prix/XP ressource
quantité nécessaire = XP visée ÷ XP de la ressource      (arrondi au supérieur)
coût total          = quantité nécessaire × prix de la ressource
```

La colonne « vs croquette » donne le **rapport de force** : si le point d'XP te revient à 10 kamas en croquette et à 1 kama avec la ressource, elle affiche **10 × plus rentable**. Au-delà du prix limite, elle bascule en rouge et compte à l'envers — « 2 × moins rentable ». C'est la colonne à trier.

Le facteur affiché est toujours supérieur à 1 : ce sont le mot et la couleur qui donnent le sens. Une ressource dix fois trop chère lit mieux en « 10 × moins rentable » qu'en « 0,1 × ». Le tri s'appuie sur la valeur brute, il reste donc exact même quand l'affichage arrondit.

Les colonnes **Qté** et **Coût total** répondent à l'autre question : combien d'unités il faut pour boucler la montée, et ce que ça coûte au total. La quantité est arrondie au supérieur — on n'achète pas un demi-item. Au prix limite exactement, le coût total rejoint le budget de la montée, à l'arrondi près.

Le bonus Almanax `×1,5` multiplie l'XP rendue par les ressources (pas par les croquettes), ce qui relève d'autant leur prix limite et leur rentabilité, et réduit d'autant les quantités.

Valeurs par défaut : XP total 1→100 `179 592`, croquette `13 920` kamas — soit un budget de montée de 5 M.

La taxe HDV est fixée à **2 %** et s'applique partout, y compris au profit enregistré des sessions de forgemagie — dans l'outil d'origine, l'écran et l'historique affichaient deux montants différents pour la même session.

---

## Les données

Tout tient dans une clé de stockage local, `dofusmgr_v1`, sur ce navigateur et ce profil :

```
version, updatedAt, settings
priceBook      prix des ressources, par nom normalisé
runePrices     prix des runes, par « stat|palier »
dossiers, crafts
fmSessions     sessions de forge, avec lien facultatif vers un craft
ventesEnCours, ventes
donjonSessions sessions de farm : recette de la clef, nombre de runs, butin
brisages       relevés datés : composition, coefficient, gain de brisage
familier       paramètres, familiers suivis, planification, favoris
draft          le travail en cours
itemCache      réponses de l'API déjà obtenues
```

L'export produit ce même objet en `.json`. À la réimportation, deux modes :

- **Fusionner** — ajoute ce qui manque, ne détruit rien ; pour un prix présent des deux côtés, la valeur la plus récente gagne.
- **Remplacer** — écrase tout par le fichier.

Vider les données de navigation efface la clé. **L'export est la seule copie durable** : fais-en un avant de changer de machine.

---

## Sources

Noms d'items, recettes, images et effets : [dofusdude](https://docs.dofusdu.de) (`api.dofusdu.de`, branche Dofus 3, langue française).
Points d'entrée utilisés : `items/equipment` et `items/weapons` pour le Craft et la Forgemagie, `items/resources` et `items/consumables` pour les lignes de ressources. Les clefs de donjon sont des `items/resources` de type `Clef`, filtrées côté client comme les familiers. Les familiers n'ont pas de point d'entrée dédié — ce sont des `equipment` de type `Familier`, et l'API ne sachant pas filtrer par type, le tri se fait côté client.
Table des runes de forgemagie et table d'XP des ressources : reprises telles quelles des outils d'origine.
