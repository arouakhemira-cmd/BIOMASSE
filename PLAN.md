# PLAN.md — Contexte permanent du projet

## 1. Architecture

Projet **100 % client-side**, modules ES (`type="module"`), aucun build, aucun backend.

```
index.html          → coquille : en-tête, panneaux de saisie, zone résultats, impression
css/reset.css       → reset minimal
css/style.css       → mise en page, palette bois/vert, cartes, tableaux, curseurs
css/print.css       → mise en page d'impression (fiche N&B)
js/constantes.js    → constantes physiques (PCI, densités, chaleurs spécifiques…)
js/parametres.js    → objet PARAMS unique : constantes + climat + combustibles + courbes de rendement
js/calculs.js       → fonctions pures : déperditions, besoin, interpolation rendement
js/humidite.js      → correction humidité du bois, classification
js/simulation.js    → simulation mensuelle, comparaison scénarios, émissions, stockage
js/economie.js      → coûts, annuité, cumul 15 ans, temps de retour, sensibilité
js/graphiques.js    → Canvas 2D natif : courbes, barres, camembert, N&B impression
js/ui.js            → SEULE couche DOM : lecture/validation formulaires, injection, écouteurs, formatage
js/app.js           → orchestration : calculs → simulation → économie → graphiques → recommandation
js/validation.js    → validation des entrées utilisateur (bornes physiques)
data/climat.js      → zones climatiques : T_base, DJU, profil mensuel (12 %)
data/combustibles.js→ caractéristiques granulés / bûches : PCI, humidité, densité, rendement, prix…
docs/             → cahier des charges, rapport, soutenance, guide
tests/cas_test.md   → cas de référence à calcul manuel
tests/tests.html    → page de tests autonome (aucun framework)
```

## 2. Conventions de nommage

- **Fichiers** : `kebab-case.js` / `kebab-case.css` / `kebab-case.md`.
- **Variables et fonctions** : `camelCase`.
- **Constantes** : `SCREAMING_SNAKE_CASE`.
- **Objets de données** : clés en `camelCase` (`granules`, `buches`).
- **Modules** : un export nommé par fonction publique ; pas d'export par défaut.
- **Fonctions de calcul** : pures (aucun effet de bord, aucune lecture DOM), JSDoc complet avec `@param`/`@returns`/`@throws`.

## 3. Unités imposées

| Grandeur | Unité |
|----------|-------|
| Énergie | kWh (et kWh/an) |
| Puissance | kW |
| Masse combustible | kg/an (granulés), stère/an (bûches) |
| Volume stockage | m³ (silo), stère (bûcher) |
| Température | °C |
| Degrés-jours | DJU (base 18 °C) |
| Coût | € |
| Émissions | tCO₂/an |

Conversions internes : 1 kWh = 3,6 MJ ; 1 stère de bois ≈ 0,7 m³ de bois plein ; humidité en fraction massique (0–1) ou % selon contexte — **toujours documenter**.

## 4. Règles de code

1. **Fonctions pures** : tous les modules de calcul (`calculs`, `humidite`, `simulation`, `economie`) ne touchent ni DOM ni `window`.
2. **Zéro logique DOM hors `ui.js`** : `app.js` orchestre, `ui.js` manipule le DOM, les modules métier calculent.
3. **Constantes centralisées** : toute constante physique ou économique vit dans `constantes.js`, `parametres.js` ou `data/`. Aucune valeur « magique » ailleurs.
4. **Règle des constantes commentées** : *toute constante physique doit être commentée avec sa plage usuelle et sa source* (norme, littérature, ordre de grandeur).
5. **Validation en entrée** : chaque fonction publique valide ses arguments (`validation.js` ou checks internes) et lève `Error` avec message explicite.
6. **JSDoc obligatoire** en tête de chaque module (responsabilité + exports) et sur chaque fonction publique.
7. **Pas de dépendance externe** : ni CDN, ni framework, ni `fetch`.
8. **Impression** : `css/print.css` + `window.print()` ; les graphiques doivent avoir une variante N&B.

## 5. Flux de données

```
ui.js (saisie) → app.js → calculs.js (déperditions, besoin)
              → humidite.js (correction PCI/rendement)
              → simulation.js (mensuel, scénarios, émissions, stockage)
              → economie.js (coûts, annuité, sensibilité)
              → graphiques.js (rendu Canvas)
              → ui.js (affichage)
```

## 6. Hypothèses structurantes (à documenter dans le rapport)

- Rendement nominal des équipements issu de `data/combustibles.js`, corrigé par la courbe à charge partielle (`parametres.js`) et par l'humidité (`humidite.js`).
- Ballon tampon : réduit le nombre de démarrages et lisse le taux de charge → rendement saisonnier amélioré (hypothèse paramétrable).
- Émissions : facteur d'émission par combustible issu de `PARAMS` ; référence gaz/fioul pour comparaison.
- Économie : annuité sur durée de vie, inflation du prix de l'énergie, temps de retour différentiel entre scénarios.
