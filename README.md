# Entre potes — bêta

Deux jeux d’ambiance francophones sur un seul téléphone : **Mot pour mot** et **Dans ta tête**, avec 486 références et des paquets personnalisés partagés.

## Jouer

[Ouvrir le jeu](https://streiffnathan-arch.github.io/entre-potes/)

Sur iPhone : ouvrir le lien dans Safari, puis Partager > Sur l’écran d’accueil. Ouvrir l’application et attendre « Prêt pour jouer hors connexion » avant de couper Internet.

**Mot pour mot** : 45 cartes, deux à quatre équipes, tours de 30 secondes. Les mêmes cartes reviennent en description, un mot, puis mime. Passer coûte respectivement 5 secondes, 2 secondes et aucun malus.

En fin de tour, un bilan liste les cartes vues : trouvées, passées et non trouvées. Touchez leur statut pour corriger les points et la pioche avant de passer à l’équipe suivante. Les pénalités restent appliquées. Les anciennes parties de 35 cartes sont conservées ; les nouvelles utilisent 45 cartes.

Les paquets et parties sont enregistrés uniquement dans le navigateur utilisé. Exporter les paquets pour les sauvegarder ou les transférer. Aucun compte de jeu requis.

## Dans ta tête

Jeu coopératif à 3–8 joueurs (5 par défaut), sans chronomètre. Chaque joueur devient médium une fois. Il voit seul une cible entre deux extrêmes et choisit un indice dans la banque ou les paquets personnels. Les autres débattent puis placent ensemble le curseur.

Une confirmation révèle la cible et verrouille le score : écart de 0–5 = 4 points, 6–10 = 3 points, 11–20 = 2 points, au-delà = 0. À cinq joueurs, le groupe vise 20 points. La cible est cachée au retour d’une interruption et les parties des deux jeux sont sauvegardées séparément.

## Hébergement

Ce dépôt contient la version compilée du site. GitHub Pages publie la branche main depuis la racine. Les sources React/TypeScript du projet sont conservées dans le dossier de développement local. Pour republier, compiler avec BASE_PATH=/entre-potes/, puis remplacer les fichiers de cette version par ceux de dist/ en conservant .nojekyll.
