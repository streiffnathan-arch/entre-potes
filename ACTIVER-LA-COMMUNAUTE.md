# Activer les vrais comptes Entre potes

Le site reste sur GitHub Pages. Supabase fournit seulement les comptes et les données communautaires. Les jeux, les parties et les paquets téléchargés continuent à fonctionner localement. Aucun abonnement payant n’a été souscrit.

## État au 12 octobre 2026

- Projet Supabase `ecdpimgsmnxoxyqnqgwr`, Entre potes, organisation entrepotes.Org, offre Free, région européenne eu-west-1.
- Tables et politiques installées ; les fonctions de sécurité sont dans le schéma privé. Les contrôles SQL réels ont validé l'accès privé, public, entre amis et le blocage, puis annulé les données d'essai. L'audit de sécurité ne signale aucune alerte.
- URL publique et clé publiable configurées dans `.env.local` ; seules ces valeurs publiques sont incluses dans l'application compilée.
- SMTP Brevo enregistré dans Supabase, expéditeur vérifié, modèles d'inscription et de connexion en français avec `{{ .Token }}`. La clé SMTP reste dans Supabase, jamais dans le site ni ce dossier.
- Premier code déclaré délivré par Brevo et réception confirmée par le propriétaire. Connexion complète et essai entre deux personnes encore à confirmer. Les étapes ci-dessous servent aussi à reproduire la configuration sur un projet neuf : ne pas recréer le projet existant.

## Ce qui fonctionne déjà sans compte

- Neuf sélections composées à partir des 486 références existantes.
- Recherche, filtres, favoris de sélections et fiches qui cachent les réponses.
- Téléchargement dans Mes paquets, préparation de Mot pour mot et accès aux cartes dans Dans ta tête.
- Atelier avec brouillon local, nettoyage des doublons, avertissements sur les longueurs et objectif de 45 cartes.
- Partage des paquets locaux par export/import JSON existant.

## Fonctions communautaires

Comptes par code email, profils publics, demandes d’amis à accepter, blocage, publication privée/amis/public, versions de paquets, favoris communautaires, signalements, export des données de compte et suppression du compte. Sans configuration, l’interface annonce clairement que les comptes ne sont pas encore ouverts.

## 1. Créer le projet gratuit

1. Ouvrir https://supabase.com/dashboard et créer son compte personnel. Cette étape et l’acceptation des conditions se font par le propriétaire de l’application.
2. Créer une organisation sur le plan **Free**, puis un projet nommé **Entre potes**. Choisir une région européenne disponible. Garder le mot de passe de la base dans son gestionnaire de mots de passe : il n’est jamais utilisé dans l’app.
3. Attendre que le projet soit prêt.

L’offre gratuite existe au moment de cette préparation. Vérifier le plan affiché avant validation. Aucun passage à une offre payante n’est nécessaire pour préparer cette bêta.

## 2. Installer les tables et les droits

Dans le projet, ouvrir **SQL Editor > New query**. Copier le contenu complet de :

`supabase/migrations/20261011_community.sql`

Exécuter cette migration **une seule fois sur un projet neuf**, puis `supabase/migrations/20261012145903_community_private_helpers_and_indexes.sql`. Ces deux migrations sont déjà appliquées sur le projet Entre potes. Ne pas les exécuter en morceaux. Elles créent les tables, la sécurité par ligne, les limites de demandes et la fonction de suppression de compte. Aucune clé d’administration n’est nécessaire dans le code du site.

Les tests `pnpm test` vérifient les politiques dans PostgreSQL embarqué via PGlite. Après installation, il faut aussi valider le projet réel avec deux comptes de test avant d’inviter des utilisateurs.

## 3. Régler la connexion par email

- Activer le fournisseur Email dans Authentication.
- Mettre l’URL du site à `https://streiffnathan-arch.github.io/entre-potes/`.
- L’app utilise un **code saisi dans le navigateur**, et non un lien magique. Dans le modèle d’email **Magic Link**, afficher `{{ .Token }}`. Exemple : « Ton code Entre potes : {{ .Token }} ». Depuis le 3 juin 2026, les nouveaux projets Free ne peuvent personnaliser ces modèles qu'après configuration d'un SMTP externe : l'envoi intégré ne permet donc pas à lui seul d'activer le parcours actuel.
- Pour permettre aux amis de recevoir les codes, configurer un serveur SMTP approprié dans Authentication. Le service email de test intégré à Supabase est limité et restreint aux destinataires autorisés du projet ; il ne suffit pas pour ouvrir une communauté publique.
- Tester l’envoi, la réception, un code incorrect, un code expiré, puis un code correct. Ne pas désactiver la confirmation d’email pour contourner l’envoi.

Brevo a été choisi et configuré par le propriétaire. Le compte gratuit utilise actuellement son adresse Gmail vérifiée ; un domaine d'envoi propre reste recommandé pour la délivrabilité avant un lancement public. Ne rien acheter sans accord. Conserver les limites d'envoi de la bêta (30 emails par heure côté Supabase, intervalle de 60 secondes par utilisateur).

## 4. Relier le site

Dans les paramètres du projet, récupérer :

- **Project URL** : `https://identifiant.supabase.co`
- **Publishable key** : commence par `sb_publishable_`

Ces deux valeurs sont destinées au navigateur. Ne jamais communiquer ni placer dans Vite une clé `sb_secret_`, une clé `service_role`, le mot de passe de la base ou un jeton personnel Supabase.

Copier `.env.example` vers `.env.local` et renseigner les deux valeurs publiques. Puis compiler :

```powershell
$env:BASE_PATH='/entre-potes/'
pnpm test
pnpm build
```

Publier le contenu compilé de `dist/` sur GitHub Pages selon README.md. Ne pas publier `.env.local`, les sauvegardes de joueurs ou node_modules. Le nom de variable VITE signifie que sa valeur est publique dans le JavaScript compilé.

## 5. Faire le vrai essai à deux comptes

1. Alice et Bob se connectent avec des emails différents et créent leurs profils.
2. Alice publie trois paquets : privé, amis, public. Bob ne voit que le public.
3. Alice ajoute Bob. Bob ne voit pas encore le paquet amis. Bob accepte : il devient accessible.
4. Un visiteur sans compte ouvre le lien public ; le lien privé et celui réservé aux amis restent inaccessibles.
5. Bob télécharge un paquet. Après une mise à jour d’Alice, sa copie reste inchangée jusqu’à confirmation de la mise à jour.
6. Bloquer un profil retire l’amitié et l’accès aux paquets amis ; débloquer ne rétablit pas l’amitié automatiquement.
7. Tester favoris, signalement, export et suppression avec un compte jetable. La suppression d’Alice ne doit jamais toucher Bob.
8. Sur l’iPhone : installer la PWA, attendre le cache prêt, couper Internet et jouer avec un paquet téléchargé.

## Modérer au début

Le propriétaire du projet utilise Supabase Table Editor pour consulter `reports` (les utilisateurs ne voient que leurs propres signalements). En cas de retrait justifié, passer `community_packs.moderation_status` à `hidden`, puis le signalement à `resolved`. Un auteur ne peut ni changer ce statut ni se rendre modérateur. Modifier un paquet masqué ne le republie pas.

Les règles de communauté et les explications de données sont visibles dans Mon compte. Avant un lancement public, compléter les informations de l’éditeur et un canal de contact réel, organiser le suivi des signalements et tester la sauvegarde de la base. Ces coordonnées ne sont pas inventées dans la bêta.

## Sources techniques

- Authentification par code : https://supabase.com/docs/guides/auth/auth-email-passwordless
- SMTP et limites du fournisseur intégré : https://supabase.com/docs/guides/auth/auth-smtp
- Clés publiques et secrètes : https://supabase.com/docs/guides/getting-started/api-keys
- Sécurité par ligne : https://supabase.com/docs/guides/database/postgres/row-level-security
- Plans : https://supabase.com/docs/guides/platform/billing-on-supabase
- Modèles email sur Free : https://supabase.com/changelog/46599-changes-to-email-template-customisation-on-free-tier

