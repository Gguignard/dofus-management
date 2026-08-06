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

## Les sept onglets

| Onglet | Ce qu'il fait |
|---|---|
| **Bilan** | Solde cumulé, chiffre d'affaires net, rentabilité moyenne, capital immobilisé en vente, meilleures marges par item |
| **Craft** | Recherche d'item, recette remplie automatiquement, coût des ressources, comparaison prix marché / ton prix, dossiers |
| **Forgemagie** | Relevé des prix de runes, arbitrage acheter-ou-crafter, session de forge avec coût des runes et ROI |
| **Familier** | Prix limite par ressource selon ton budget, 2117 ressources filtrables par zone, favoris, planification de lots |
| **Prix** | Le référentiel commun : ressources et runes au même endroit |
| **Ventes** | File des items en vente (venus du Craft ou de la Forge), puis registre des ventes conclues |
| **Données** | Export, import, réinitialisation |

### Ce que la fusion apporte

- **Un prix saisi une fois vaut partout.** Le prix d'une ressource entré dans le Craft remplit la colonne « Prix HDV » du Familier et apparaît dans l'onglet Prix. Plus de triple saisie.
- **Un seul bilan.** Un craft revendu et un item forgé alimentent le même registre : le solde en haut à gauche est ton gain réel, tous ateliers confondus.
- **Le craft nourrit la forge.** Dans une session de forgemagie, « Rattacher à un craft enregistré » reprend son coût comme prix de l'item brut.
- **Rien ne se perd.** Le travail en cours (item, ressources, runes, prix) survit à un rechargement.

---

## Les formules

**Craft** — `coût = Σ(qté × prix)` · `net = prix × 0,98` · `profit = net − coût` · `rentabilité = profit ÷ coût`

**Forgemagie** — `Pa = 3 runes de base` · `Ra = 3 Pa` ou `9 runes de base` · le prix retenu est le moins cher entre le HDV et les crafts · `runes posées = stock + achetées − restantes` · `coût = Σ(posées × prix retenu)` · `ROI = profit ÷ (item brut + coût des runes)`

**Familier** — `croquettes = XP total ÷ XP par croquette` · `prix max par croquette = budget ÷ croquettes` · `quantité = XP par croquette ÷ (XP ressource × bonus)` · `prix limite = prix max par croquette ÷ quantité` · bonus Almanax `×1,5`

Valeurs par défaut : XP total `179 592`, XP par croquette `500`, budget `5 000 000`.

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
Table des runes de forgemagie et table d'XP des ressources : reprises telles quelles des outils d'origine.
