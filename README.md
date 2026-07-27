# Loxchek — version web installable (PWA)

Ce dossier contient l'appli Loxchek prête à héberger. Une fois en ligne, elle
s'installe sur Android **et** iPhone via "Ajouter à l'écran d'accueil", et
fonctionne hors-ligne une fois ouverte au moins une fois.

## Contenu

- `index.html` — l'appli (c'est le seul fichier à modifier pour changer le contenu)
- `manifest.json` — nom, icône, couleurs de l'app installée
- `service-worker.js` — permet le fonctionnement hors-ligne
- `icons/` — icônes de l'app
- `vendor/jspdf.umd.min.js` — génération de PDF, embarquée en local

## Héberger gratuitement avec GitHub Pages (10 minutes, sans rien installer)

1. Créer un compte sur [github.com](https://github.com) si tu n'en as pas
2. Cliquer **New repository**, nommer par exemple `loxchek`, cocher **Public**, créer
3. Sur la page du dépôt : **Add file → Upload files**, glisser-déposer **tout le
   contenu de ce dossier** (`index.html`, `manifest.json`, `service-worker.js`,
   le dossier `icons/`, le dossier `vendor/`) puis **Commit changes**
4. Aller dans **Settings → Pages** (menu de gauche)
5. Sous "Build and deployment" → Source : **Deploy from a branch**,
   Branch : **main**, dossier **/ (root)** → **Save**
6. Après ~1 minute, l'URL apparaît en haut de cette page, du type :
   `https://<ton-pseudo>.github.io/loxchek/`

C'est cette URL que tu partages à tes sous-traitants.

## Installer sur les téléphones

**Android (Chrome)** : ouvrir l'URL → menu ⋮ → "Ajouter à l'écran d'accueil"
**iPhone (Safari)** : ouvrir l'URL → icône de partage (carré avec flèche) → "Sur l'écran d'accueil"

L'icône Loxchek apparaît, l'app s'ouvre en plein écran comme une vraie application.

## Mettre à jour le contenu plus tard

Modifier `index.html`, puis sur GitHub : **Add file → Upload files** à nouveau
(ou éditer le fichier directement dans l'interface GitHub, crayon ✏️ en haut à
droite du fichier). Les téléphones récupèrent la nouvelle version à la
prochaine ouverture avec connexion.

Astuce : si une mise à jour ne semble pas s'appliquer (à cause du cache
hors-ligne), changer `const CACHE_NAME = 'loxchek-v1';` en `'loxchek-v2'`
dans `service-worker.js` force le rafraîchissement.

## Alternatives à GitHub Pages

Si tu préfères ne pas utiliser GitHub, n'importe quel hébergement de fichiers
statiques fonctionne pareil (Netlify Drop, Cloudflare Pages, ou même un petit
espace web déjà utilisé par l'entreprise) : il suffit de déposer les mêmes
fichiers à la racine.
