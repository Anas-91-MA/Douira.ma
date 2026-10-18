# Mettre Douira en ligne

Ce dossier est le site prêt à déposer chez un hébergeur. Il fonctionne sans serveur ni base de données.

## Deux versions, un seul réglage

Dans `config.js`, la ligne `mode:` choisit la version affichée :

- `mode: 'demo'` : le **site complet** (logements d'exemple, comptes client, propriétaire, collaborateur et administrateur, réservations, factures, avis, favoris). Les paiements et les e-mails sont simulés et les données restent dans le navigateur de l'ordinateur. Pour l'ouvrir : double-cliquez sur `index.html`, puis choisissez un profil dans la barre « Démo » en haut.
- `mode: 'vitrine'` : la version à mettre en ligne, décrite ci-dessous.

Pour un **essai** (sur votre ordinateur ou sur une adresse de test comme Render), `'demo'` convient : en ligne, le site demande aux moteurs de recherche de ne pas l'indexer. Chaque visiteur a sa propre copie des données d'exemple, dans son navigateur : ce que vous ajoutez sur votre téléphone n'apparaît ni sur votre ordinateur ni chez les autres.

**Pour ouvrir le site au vrai public, remettez `mode: 'vitrine'`.**

## Ce que fait le site en ligne (mode « vitrine »)

- Accueil, recherche par quartier avec carte, fiches des appartements et villas, offres, label Diamond.
- Prix affiché = prix du propriétaire + 12,5 % (taux réglable dans `config.js`, jamais affiché).
- « Demander une réservation », « Contact et réclamations » et « Proposer mon bien » préparent un message et l'ouvrent dans WhatsApp, adressé à votre numéro. L'adresse e-mail est aussi proposée.
- Fonctionne sur téléphone et ordinateur ; le bouton retour du téléphone et les liens partagés (`#annonce-…`) fonctionnent.

## Ce qu'il ne fait pas encore

Les comptes (clients, propriétaires, collaborateurs, administrateur), le paiement en ligne, les e-mails automatiques, les avis, les favoris, l'espace d'administration et le calendrier des véhicules fonctionnent dans la **démo**, mais leurs données restent dans le navigateur de chaque visiteur. Pour les ouvrir au public, il faut un serveur :

- une base de données et un serveur d'application (comptes, réservations, disponibilités) ;
- un prestataire de paiement agréé par Bank Al-Maghrib (contrat commerçant, page de paiement hébergée, remboursements) ;
- un service d'envoi d'e-mails (adresse d'expédition sur votre domaine) ;
- un stockage pour les photos.

En attendant, la vitrine permet de recevoir de vraies demandes sur WhatsApp et de les traiter vous-même.

## Étapes

1. **Remplir `config.js`** (seul fichier à modifier) :
   - `whatsapp` et `email` : sans eux, les demandes ne peuvent pas vous parvenir. `email` est l'e-mail de l'administrateur (déjà rempli : mohmed.anas.mosseddaq@gmail.com) ; il est affiché aux visiteurs comme adresse de contact ;
   - `societe` : raison sociale, ICE, RC, adresse, n° CNDP ;
   - `legal` : mentions légales, conditions générales, confidentialité, annulation, validées par votre juriste ;
   - `biens` : vos biens réels (un exemple de format est en commentaire). N'y mettez jamais l'adresse exacte ni le code d'accès : ce fichier est public.
2. **Ajouter les photos** dans le dossier `photos/` et indiquer leurs chemins dans `config.js`.
3. **Vérifier en local** : ouvrez `index.html` dans votre navigateur. Si un champ manque, la console du navigateur (F12, onglet Console) affiche « À compléter dans config.js ».
4. **Choisir un hébergement** de fichiers statiques :
   - Netlify ou Cloudflare Pages : glissez-déposez le dossier ; le fichier `_headers` applique les en-têtes de sécurité ;
   - hébergement mutualisé (Apache) : envoyez le contenu du dossier dans `public_html` ; le fichier `.htaccess` force le HTTPS et applique les mêmes en-têtes ;
   - Render : voir « Héberger sur Render » ci-dessous ; le fichier `render.yaml` applique les mêmes en-têtes.
5. **Acheter le nom de domaine** (.ma auprès d'un bureau d'enregistrement agréé, ou .com) et le relier à l'hébergeur (enregistrements DNS indiqués par l'hébergeur). Activez le certificat HTTPS proposé par l'hébergeur.
6. **Après l'achat du domaine** :
   - renseignez `siteUrl` dans `config.js` ;
   - dans `robots.txt`, décommentez la ligne `Sitemap:` avec votre adresse et créez `sitemap.xml` :
     ```xml
     <?xml version="1.0" encoding="UTF-8"?>
     <urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9"><url><loc>https://www.votre-domaine.ma/</loc></url></urlset>
     ```
   - dans `index.html`, remplacez `content="og-image.png"` par l'adresse complète (`https://www.votre-domaine.ma/og-image.png`) pour les aperçus de liens.
7. **Tester en ligne** sur un téléphone et un ordinateur : une demande de réservation doit ouvrir WhatsApp avec le message prérempli vers votre numéro.

## Héberger sur Render

Render publie gratuitement un site statique à partir d'un dépôt Git (GitHub par exemple). Le fichier `render.yaml` contient déjà les réglages : Render ne publie que les fichiers du site (pas ce guide ni les fichiers de configuration) et applique les mêmes en-têtes de sécurité.

1. **GitHub** : créez un dépôt **privé** (par exemple `douira-site`), puis « Add file > Upload files ». Ouvrez le dossier `Douira.com.site`, sélectionnez tout son contenu (Cmd + A) et glissez-le dans la page : les fichiers doivent être à la racine du dépôt, `render.yaml` compris. Validez avec « Commit changes ».
2. **Render** : New > Blueprint, connectez votre compte GitHub si ce n'est pas fait, choisissez le dépôt, puis « Deploy Blueprint ».
3. Render affiche l'adresse du site en `onrender.com` (par exemple `https://douira.onrender.com` si ce nom est libre).
4. **Mettre à jour** : remplacez le fichier sur GitHub (par exemple `config.js`) ; Render redéploie le site automatiquement.
5. **Nom de domaine** : page du site sur Render > Settings, rubrique Custom Domains, puis ajoutez chez votre registraire les enregistrements DNS indiqués.

Ne modifiez pas `render.yaml` à la main : il contient l'empreinte des scripts de `index.html`, comme `_headers` et `.htaccess`.

## Changer l'e-mail de l'administrateur

À tout moment : ouvrez `config.js` avec un éditeur de texte (TextEdit en mode texte brut, Bloc-notes, VS Code), remplacez l'adresse entre guillemets sur la ligne `email:`, enregistrez, puis renvoyez ce seul fichier chez l'hébergeur. Inutile de toucher à `index.html`.

Dans la plateforme complète (démo), l'administrateur la change dans **Paramètres et thème > Contacts et notifications** ; la nouvelle adresse sert aussi d'e-mail à son compte.

## À ne pas faire

- Ne présentez pas le mode `'demo'` à de vrais clients comme un service réel : il simule les paiements.
- Ne modifiez pas le code JavaScript de `index.html` à la main : les en-têtes de sécurité (`_headers`, `.htaccess`, `render.yaml`) contiennent son empreinte et le navigateur le bloquerait. Les modifications passent par `build/build.py`, qui régénère les empreintes.

## Points à faire valider avant l'ouverture

- Loi 80-14 : autorisation ou déclaration de chaque bien, numéro à afficher (`numeroAutorisation`).
- Loi 09-08 : déclaration à la CNDP des traitements (demandes reçues par WhatsApp et e-mail), politique de confidentialité.
- Loi 31-08 : information du consommateur (prix, conditions d'annulation) dans les conditions générales.
- Les polices de caractères sont chargées depuis Google Fonts, ce qui transmet l'adresse IP des visiteurs à Google. Vous pouvez les héberger vous-même si votre juriste le recommande.
