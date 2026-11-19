# Chauffage biomasse — outil de dimensionnement et comparaison

Application web autonome (HTML + JavaScript ES6, **aucune dépendance,
aucun backend**) pour comparer trois scénarios de chauffage au bois :

- **Granulés** (chaudière + silo)
- **Bûches** (poêle/chaudière)
- **Bûches + ballon tampon**

Elle calcule les déperditions (méthode des degrés-jours), simule la
consommation mensuelle, corrige le rendement selon la charge partielle
et l'humidité du combustible, évalue les émissions de CO₂ et les coûts
sur 15 ans, et produit graphiques et recommandations.

## Utilisation

Ouvrir `index.html` dans un navigateur moderne (aucune installation,
fonctionne hors ligne). Saisir les caractéristiques du bâtiment et les
hypothèses économiques : tous les résultats se recalculent
automatiquement.

- **Impression / PDF** : bouton « Imprimer » (mise en page N&B
  prévue dans `css/print.css`).
- **Tests** : ouvrir `tests/tests.html` (29 tests automatisés).

## Structure

```
chauffage-biomasse/
├── index.html          # page principale
├── css/                # reset, style, print
├── js/                 # modules ES6 (calculs, simulation, économie…)
├── data/               # climat.js, combustibles.js
├── docs/               # CAHIER_DES_CHARGES, PLAN, RAPPORT, SOUTENANCE
└── tests/              # cas_test.md, tests.html, tests.js
```

Voir `docs/PLAN.md` pour l'architecture et `docs/RAPPORT.md` pour la
méthodologie, les hypothèses et les limites.
