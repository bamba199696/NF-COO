# Assistant Notice Financière — déploiement Vercel

Application 100 % côté navigateur (aucun backend, aucune donnée transmise à
un serveur — tout le traitement PDF/OCR/Excel, l'analyse financière, le
scoring, les garanties et le chat local se font dans le navigateur du
client). Elle se déploie donc comme un **simple site statique** sur Vercel,
sans build ni fonction serverless.

## Contenu du dossier
- `index.html` — l'application (identique à la version testée dans Claude,
  avec balise `robots: noindex` ajoutée car c'est un outil interne).
- `vercel.json` — en-têtes de sécurité (anti-clickjacking, anti-sniffing,
  pas de référent transmis, pas de cache agressif sur `index.html`).

## Le chat intégré

L'application inclut un assistant de recherche **100 % local** (bouton en
bas à droite) : il répond aux questions sur les documents déjà analysés en
cherchant dans les données extraites (codes SYSCOHADA, montants, ratios,
cohérence du bilan) et dans le texte brut des documents. Aucune donnée n'est
envoyée à un service externe — c'est un moteur de recherche, pas une IA
conversationnelle, donc il répond mieux à des questions factuelles précises
qu'à du raisonnement libre.

## Option A — via l'interface Vercel (le plus simple)
1. Créez un dépôt Git (GitHub/GitLab/Bitbucket) contenant ces 2 fichiers
   (ou glissez-déposez directement le dossier sur vercel.com si vous ne
   voulez pas passer par Git).
2. Sur [vercel.com](https://vercel.com) → **Add New → Project**.
3. Importez le dépôt. Vercel détecte automatiquement un projet statique
   (**Framework Preset : Other**) — aucun réglage de build à faire :
   - Build Command : *(vide)*
   - Output Directory : *(racine, laisser vide ou `.`)*
4. **Deploy**. Vous obtenez une URL du type
   `https://votre-projet.vercel.app`.

## Option B — via la CLI Vercel
```bash
npm i -g vercel      # une seule fois
cd vercel-deploy
vercel               # déploiement de preview
vercel --prod        # déploiement en production
```

## Restreindre l'accès (recommandé)
L'outil manipule des états financiers clients. Avant un déploiement en
production, activez sur Vercel l'une de ces options (Project → Settings) :
- **Vercel Authentication** / **Password Protection** (plans Pro/Enterprise),
  ou
- placez l'app derrière votre SSO/VPN d'entreprise, ou
- restreignez le déploiement à un domaine interne uniquement accessible via
  le réseau de la Société Générale.

Sans protection, l'URL `.vercel.app` est accessible à quiconque la connaît.

## Nom de domaine personnalisé
Project → Settings → Domains, puis ajoutez par exemple
`notice-financiere.interne.societegenerale.sn` (nécessite d'ajouter
l'enregistrement DNS CNAME correspondant chez votre fournisseur DNS interne).

## Dépendances externes (CDN)
L'app charge au chargement de la page :
- `pdf.js`, `tesseract.js`, `xlsx` (SheetJS), `mammoth.js` depuis cdnjs.cloudflare.com
- les langues OCR (français/anglais) depuis tessdata.projectnaptha.com
- les polices Inter / JetBrains Mono depuis fonts.googleapis.com

Si le réseau de l'entreprise bloque ces domaines (proxy filtrant), l'outil
ne fonctionnera pas correctement. Deux solutions :
1. Faire autoriser ces domaines par l'équipe sécurité/réseau, ou
2. Héberger ces bibliothèques en local dans le dépôt (dites-le-moi si besoin).

## Vérifier après déploiement
Testez le parcours complet sur l'URL Vercel : chargement du modèle Excel,
chargement d'un PDF test, extraction, revue, export, puis le chat local —
exactement comme dans Claude, aucune fonctionnalité ne doit manquer puisque
le code est strictement identique.
