# Entre potes — bêta

Application web React + TypeScript + Vite, conçue pour un téléphone partagé. Entre potes regroupe deux jeux : Mot pour mot et Dans ta tête.

## Utiliser la bêta

Ouvrir https://streiffnathan-arch.github.io/entre-potes/ dans Safari. Le site est public et hébergé gratuitement avec GitHub Pages. Les jeux fonctionnent sans compte ; les parties et les paquets locaux restent sur le téléphone. La communauté utilise un compte facultatif par code email.

Dans Safari : Partager > Sur l’écran d’accueil. Ouvrir l’application depuis son icône avec Internet et attendre « Prêt pour jouer hors connexion ». Tester ensuite en mode avion. L’ajout à l’écran d’accueil et le comportement audio restent à vérifier sur un véritable iPhone.

## Découvrir et créer

Neuf sélections utilisent la banque existante : recherche, filtres, favoris locaux, réponses masquées et ajout à Mes paquets. L'atelier permet de créer un thème, conserver son brouillon et nettoyer les doublons avant sauvegarde. Les téléchargements restent disponibles hors connexion.

Les comptes par code email, profils, amis, thèmes privés/entre amis/publics, versions, signalements et blocages sont reliés au projet Supabase Entre potes. Brevo est configuré avec un expéditeur vérifié ; le propriétaire a confirmé la réception du premier code. Le parcours complet avec deux personnes reste à confirmer avant ouverture au grand public. Voir [ACTIVER-LA-COMMUNAUTE.md](ACTIVER-LA-COMMUNAUTE.md). Aucune dépense n'a été engagée.

Validation : 27 tests de logique, 12 contrôles de permissions PostgreSQL, tests d'isolation sur le projet Supabase réel (données synthétiques annulées) et parcours WebKit en 390 et 320 pixels, dont rechargement hors connexion. Aucun test sur iPhone physique n'a encore été effectué.

## Mot pour mot

- 45 cartes uniques tirées parmi les catégories et paquets sélectionnés ; pas de consultation préalable des réponses.
- Trois manches avec les mêmes cartes : description, un mot, mime.
- Deux à quatre équipes ; tours de 30 secondes ; un point par carte trouvée.
- Passer : −5 secondes, puis −2 secondes, puis aucun malus. La carte retourne à la fin de la pioche.
- À zéro, le tour s’arrête ; la carte non trouvée reste à jouer.
- À la fin d’une manche, le temps restant est abandonné. L’équipe suivante ouvre la manche suivante avec 30 secondes.
- L’application masque la carte et met le tour en pause en arrière-plan. Après recharge, reprise manuelle.
- Le bilan de fin de tour affiche toutes les cartes vues (trouvées, passées et restée à l’écran), une seule fois chacune. Changer leur statut ajuste immédiatement le score et la pioche ; les pénalités ne sont pas remboursées. Les corrections sont closes au passage à l’équipe/manche suivante.
- Les anciennes parties de 35 cartes restent jouables. Les passages antérieurs à cette mise à jour ne peuvent pas être reconstruits ; le bilan le signale pendant le tour concerné.
- Les joueurs arbitrent les réponses. Les équipes à égalité sont déclarées ex æquo.

## Dans ta tête

Jeu coopératif à 3–8 joueurs (5 par défaut), sans chronomètre. Une partie comporte un tour par joueur, chacun étant médium une fois dans l’ordre des prénoms.

1. Le médium ouvre seul son écran secret et voit une cible sur une échelle de 0 à 100 entre deux extrêmes. Chaque tour utilise un axe différent parmi 24 axes.
2. Il choisit une référence parmi les 486 cartes et les paquets personnels, via les suggestions ou la recherche. Une référence déjà utilisée ne peut pas revenir dans cette partie.
3. « Cacher la cible et passer le téléphone » verrouille l’indice. Le médium ne donne aucun chiffre, explication ou geste supplémentaire.
4. Les autres débattent et déplacent le curseur collectivement. Une confirmation précède la révélation.
5. Écart avec le centre de la cible : 0–5 = 4 points, 6–10 = 3 points, 11–20 = 2 points, au-delà = 0. Les bornes sont inclusives. Les zones sont tronquées aux bords de l’échelle. Les points sont additionnés pour le groupe (20 au maximum à 5 joueurs).
6. La cible est révélée, le médium peut expliquer son raisonnement, puis passe au joueur suivant.

Le secret est caché après passage en arrière-plan, fermeture ou recharge. Le curseur et l’indice sont conservés lors d’une interruption du débat. Après révélation, le curseur est verrouillé et le score ne peut pas être ajouté deux fois.

La sauvegarde utilise une clé distincte de Mot pour mot : les deux parties coexistent. Les cibles ne sont pas affichées dans le DOM pendant le débat ; comme toutes les données d’un jeu local, elles ne sont pas protégées contre une inspection technique du stockage par le propriétaire du téléphone.

## Cartes personnelles

Mes paquets > Créer un paquet. Une ligne par carte, maximum 100 caractères. Les espaces et doublons de casse sont normalisés. Maximum 1 000 cartes par paquet et 100 paquets.

Les données sont locales à l’appareil et à l’origine du site. Un autre navigateur, un autre lien ou l’application installée peuvent avoir un stockage distinct. Exporter régulièrement les paquets ; importer le fichier sur le nouvel appareil. Les imports ajoutent des copies et ne remplacent jamais les paquets existants. Les réponses sont masquées dans l’interface, mais le catalogue embarqué n’est pas un secret protégé contre une inspection technique.

## Scénario de test avec les amis

1. Créer un paquet « Notre groupe » avec cinq cartes. Fermer et rouvrir : le paquet doit rester.
2. Exporter le paquet, puis le réimporter : une copie doit apparaître sans remplacer l’original.
3. Lancer une partie avec la banque et le paquet. Vérifier que personne ne voit les réponses avant son tour.
4. En manche 1, passer une carte : environ cinq secondes doivent être retirées immédiatement. Tester −2 en manche 2 et aucun malus en manche 3.
5. Valider puis annuler : le score et la carte doivent être rétablis.
6. Quitter l’application pendant un tour et revenir : la carte reste cachée jusqu’à la reprise.
7. Finir les trois manches : 135 points au total.
8. Après préparation du cache, activer le mode avion, fermer et rouvrir le jeu, puis lancer une partie.

## Développement

Prérequis : Node.js récent et pnpm. Installer les dépendances avec `pnpm install`, puis `pnpm dev`. `pnpm test` vérifie les règles et l’import/export. `pnpm build` vérifie TypeScript, produit le site et génère le cache hors connexion. Le mode hors connexion s’évalue sur la version compilée, pas sur le serveur de développement.

Le moteur de compilation esbuild-wasm remplace le binaire natif pour la compatibilité de l’environnement Windows utilisé lors de la création. Il est plus lent mais conserve la chaîne Vite.

- src/catalog.ts : 486 cartes validées et leurs catégories.
- src/game.ts : règles indépendantes de l’interface.
- src/storage.ts : validation et import/export.
- src/App.tsx : écrans et actions.
- src/mind.ts : règles, validation et score de Dans ta tête.
- src/MindGame.tsx : écran secret, recherche d’indices, débat, révélation et sauvegarde indépendante.
- src/style.css : présentation.
- scripts/offline.mjs : cache versionné des fichiers de la version compilée.

Le cache installe une nouvelle version complète en arrière-plan, sans recharger une partie en cours. À la prochaine navigation, l’application privilégie la page à jour puis revient à sa copie hors connexion si le réseau échoue. Les deux caches précédents restent disponibles pour les fenêtres déjà ouvertes. Les paquets et la session de connexion restent dans le stockage local. L’intégration Capacitor pourra ensuite réutiliser la version web ; distribution native et achats ne sont pas implémentés.

## Publication GitHub Pages

Dépôt : https://github.com/streiffnathan-arch/entre-potes

Site : https://streiffnathan-arch.github.io/entre-potes/

Le dépôt contient les fichiers compilés de dist/ et .nojekyll. GitHub Pages sert main / (root). Le code source reste dans ce dossier local : conserver une copie du projet.

Pour compiler pour cette adresse dans PowerShell :

```powershell
$env:BASE_PATH='/entre-potes/'
pnpm build
```

Le chemin de base doit rester identique pour Vite et le script de cache. Pour revenir à une compilation à la racine, retirer BASE_PATH de l’environnement. Chaque publication met à jour index.html, sw.js et les fichiers de dist/, mais conserve les anciens fichiers versionnés dans assets/ : une page gardée en cache peut encore les demander. Ne pas envoyer les sauvegardes personnelles, node_modules ou .openai sur GitHub.

En cas de chargement interrompu, un écran « Relancer l’application » reste visible même si le JavaScript principal échoue. Ce bouton ne supprime ni les paquets ni les sessions. La connexion reste enregistrée dans le même navigateur ou la même installation ; navigation privée, suppression des données, déconnexion explicite et révocation de session peuvent imposer une nouvelle connexion.

Les anciennes métadonnées Sites sont conservées localement mais ne servent pas à cette publication GitHub Pages.

## Vérifications de la version à deux jeux

- 15 tests automatisés : règles de Mot pour mot, banque, import/export, distances et bornes du score de Dans ta tête, transitions, cinq médiums, sauvegardes invalides et reprises.
- Parcours WebKit à 390 px et contrôle à 320 px : partie de cinq tours, recherche dans la banque, indice personnalisé, cible absente de l’écran de débat, réglage du curseur, confirmation, verrouillage et résultat 15/20.
- Recharge et passage en arrière-plan pendant l’écran secret ; recharge pendant le débat ; sauvegarde de Mot pour mot et paquets conservés après la partie du second jeu.
- Nouvelle partie hors connexion après mise en cache et arrêt du serveur de test.

Ces vérifications ont été exécutées sur ordinateur. L’installation et le geste de glissement du curseur sur un iPhone physique restent à essayer.

## Mise à jour du bilan de Mot pour mot (11 octobre 2026)

- Nouvelles parties à 45 cartes ; les anciennes sauvegardes de 35 cartes sont migrées sans changer la pioche.
- Bilan éditable des cartes vues, sans doublon. Les corrections ajustent les points et la pioche, sans relancer le temps ni rembourser les pénalités.
- 20 tests automatisés réussis, dont corrections répétées, carte passée plusieurs fois, fin de manche, dernière carte de la troisième manche, annulation et sauvegardes anciennes.
- Parcours complet WebKit à 390 px : correction de trois cartes, rechargement, écran de passage masqué, trois manches et 135 points, nouvelle partie et correction hors connexion après arrêt du serveur. Contrôle de débordement à 320 px.
- Régression du parcours complet de Dans ta tête : cinq tours, indice personnalisé, secrets masqués, sauvegardes séparées, reprise et hors connexion.

Ces tests utilisent un navigateur WebKit sur ordinateur, pas un iPhone physique.


## Refonte visuelle et allègement (13 octobre 2026)

Direction visuelle préparée dans Canva : https://canva.link/4xa0pk33548wu8c. L’interface finale est construite en CSS et avec les icônes existantes, sans intégrer Canva dans le site, sans image de décoration ni police distante. Accueil à deux cartes citron/lavande, cinq filtres visibles, champs natifs lisibles et visibilité explicite des publications.

Les données de communauté sont demandées pour la rubrique affichée. Les requêtes simultanées identiques sont mutualisées, le profil est gardé une minute en mémoire et les listes sont rafraîchies à leur ouverture. Les retours au premier plan sont regroupés. Le cache hors connexion est vérifié à l’activation du service worker plutôt que toutes les quatre secondes. Le minuteur ne tourne que pendant un tour, et la sélection des cartes n’est plus recalculée à chaque tick.

Sur le même parcours connecté (Découvrir, compte, Découvrir, amis, catalogue public, créations) : 42 requêtes de données avant, 7 après. Il s’agit d’un test WebKit avec réponses simulées, pas d’une mesure sur l’iPhone physique. Le catalogue public réel répond aussi correctement. Le bundle complet reste sous 650 Ko non compressés, environ 195 Ko en gzip mesuré localement ; le nouveau style augmente légèrement le total par rapport à la version précédente. Aucun asset ancien n’est retiré du dépôt, afin de préserver les pages déjà en cache.

Validation : 27 tests de logique, 12 contrôles PostgreSQL, parcours complets des jeux et bilans, mode hors connexion, session persistante, partage depuis l’éditeur, rafraîchissement des amis, lien partagé, contrôle des cinq filtres sur 320/390/430/900 px et des boutons de publication en portrait/paysage.

## Sauvegarde privée des paquets — 13 octobre 2026

Avec un compte, Mes paquets est synchronisé avec `private_libraries` sur Supabase. Chaque utilisateur ne peut lire et modifier que sa propre bibliothèque (RLS, colonnes autorisées, révision optimiste). Cette sauvegarde ne publie rien dans la communauté. Une copie locale par compte permet de jouer hors connexion et de reprendre les envois au retour du réseau.

Sur un téléphone contenant des anciens paquets : se connecter, ouvrir Mes paquets et toucher « Sauvegarder les N paquet(s) de ce téléphone sur mon compte ». Attendre « Synchronisé ». Sur un autre appareil, se connecter avec le même email ; les paquets arrivent automatiquement. Les bibliothèques de comptes différents restent séparées. Une suppression de paquet se propage aux appareils synchronisés. Les modifications simultanées incompatibles sont conservées dans une copie.

Migration SQL : `20261013120000_private_library_sync.sql`. Le CLI installé a échoué à initialiser son répertoire utilisateur malgré l’autorisation accordée ; fichier SQL créé localement et testé avec PGlite avant application via le connecteur Supabase. Tests : `node scripts/test.mjs`, puis le scénario WebKit `work/library-ui-test.cjs` depuis la racine du travail.

## Groupes privés — 13 octobre 2026

Groupes remplace la navigation Mes potes et le filtre Mes amis. Les données des anciennes amitiés et les anciens partages restent inchangés. Chaque groupe contient une discussion, les copies des paquets partagés et ses membres. Le créateur peut renommer, inviter par lien aléatoire, renouveler le lien, retirer des membres et supprimer le groupe. Le retrait d’un membre révoque aussi l’ancien lien. Les nouveaux membres accèdent à l’historique ; les personnes qui quittent le groupe perdent les lectures et écritures. Les jeux proposés ouvrent les jeux locaux existants, sans session multijoueur distante.

Un partage de paquet envoie une copie explicite (1–200 cartes) ; le chat affiche uniquement son nom et son nombre de cartes. Les jeux et les bibliothèques privées restent indépendants. Le chat exige Internet et conserve un brouillon local par compte et par groupe. Un envoi incertain peut être réessayé sans duplication grâce à un identifiant stable. Pas d’envoi automatique du brouillon au retour du réseau.

RLS sur party_groups, party_members et party_messages. Invitations et compteurs dans le schéma privé. Les RPC publiques sont SECURITY INVOKER ; les opérations internes contrôlent auth.uid(), le profil, l’appartenance et le rôle de créateur. Realtime INSERT/UPDATE est limité aux membres ; un contrôle toutes les 15 secondes, uniquement dans le groupe visible, couvre les reconnexions et retraits. Aucun abonnement temps réel sur l’accueil ni les jeux. Les lectures de discussion sont paginées par date + UUID, 50 messages par page.

Migration : 20261013170000_private_groups.sql. Le CLI a de nouveau échoué avant son aide (EPERM sur son répertoire de configuration) ; migration écrite localement et testée via PGlite avant application MCP. Validation : tests/groups-rls.mjs et work/groups-ui-test.cjs (deux navigateurs WebKit, transport simulé, vrai SQL local). Aucun message envoyé à un utilisateur réel par les tests.