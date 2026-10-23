---
date: 2026-10-23
maj: 2026-10-23
---
# hugobernier.info

Page professionnelle de Hugo Bernier, publiée par GitHub Pages à partir de ce dépôt.
Contenu repris de `Accueil_Publish.md`, l'ancienne page Obsidian Publish.

## Ce que contient le dépôt

| Fichier | Rôle |
| --- | --- |
| `index.md` | Le contenu de la page, en Markdown. **C'est le seul fichier à modifier au quotidien.** |
| `_layouts/default.html` | Le gabarit HTML : métadonnées, titre, pied de page |
| `assets/css/style.css` | Les styles. Clair et sombre selon le réglage du visiteur |
| `assets/hugo-bernier.jpg` | Le portrait |
| `_config.yml` | Titre et description du site |
| `CNAME` | Le domaine personnalisé |
| `apercu.html` | Aperçu local du rendu, hors Jekyll. Pratique, inutile à la publication — supprimable |

Pas de thème Jekyll : la mise en page tient dans un gabarit et une feuille de style, lisibles et modifiables sans rien installer.

## Mise en ligne, dans l'ordre

L'ordre compte : GitHub recommande de déclarer le domaine **avant** de toucher au DNS, faute de quoi quelqu'un d'autre pourrait héberger un site sur votre domaine.

### 1. Créer le dépôt

Sur GitHub, **sur votre compte personnel** — pas dans `rcs-udem` :

- Nom : `hugobibliothecarius.github.io`
- Visibilité : **Public** (GitHub Pages exige un dépôt public sur un compte gratuit)
- Sans README, sans `.gitignore`

### 2. Y déposer ces fichiers

**Voie recommandée : le navigateur, sans jeton.** Dans le dépôt, **Add file → Upload files**, glissez le contenu de ce dossier — en conservant les sous-dossiers `_layouts/` et `assets/` —, puis **Commit changes**. Deux minutes. Pour modifier la page ensuite, le crayon dans l'interface web suffit.

<details>
<summary>Voie par le terminal, et le jeton qu'elle exige</summary>

#### D'abord, apprendre à Git à distinguer les dépôts

Votre trousseau contient **une seule entrée pour `github.com`** : celle du jeton de `rcs-udem/suivi-rcs`. Par défaut, Git ne tient pas compte du chemin dans l'URL — un identifiant enregistré pour un dépôt sert à tous les autres du même hôte ([gitcredentials, *useHttpPath*](https://git-scm.com/docs/gitcredentials#Documentation/gitcredentials.txt-useHttpPath)). Sans précaution, le `push` réutiliserait le jeton de l'organisation, qui n'a aucun droit ici, et échouerait sur une erreur 403 peu bavarde.

```zsh
git config --global credential.https://github.com.useHttpPath true
```

Chaque dépôt obtient désormais sa propre entrée. À la prochaine opération sur `suivi-rcs`, Git redemandera une fois l'identifiant et le jeton : c'est normal, il crée la nouvelle entrée.

#### Créer le jeton de ce dépôt

Photo de profil → **Settings** → **Developer settings** → **Personal access tokens** → **Fine-grained tokens** → **Generate new token**.

| Champ | Valeur |
| --- | --- |
| *Token name* | `hugobernier.info — Mac` |
| *Resource owner* | **Votre compte** (`hugobibliothecarius`), surtout pas `rcs-udem` |
| *Repository access* | **Only select repositories** → `hugobibliothecarius.github.io` |
| *Permissions → Repository permissions → Contents* | **Read and write** |
| *Expiration* | À votre convenance. Le plafond de 366 jours est une règle d'organisation; il ne s'applique pas ici |

Copiez le jeton immédiatement : GitHub ne le réaffichera jamais.

#### Envoyer

```zsh
git init -b main
git add .
git commit -m "Page professionnelle"
git remote add origin https://github.com/hugobibliothecarius/hugobibliothecarius.github.io.git
git push -u origin main
```

Au nom d'utilisateur : `hugobibliothecarius`. Au mot de passe : le jeton. **Rien ne s'affiche à la saisie** — Git désactive l'écho, c'est voulu.

#### Si le push échoue quand même

| Message | Cause | Solution |
| --- | --- | --- |
| `403` ou `Permission denied` | Le trousseau a servi l'ancien jeton | Vérifiez que `useHttpPath` est bien à `true`, puis effacez l'entrée : `printf "protocol=https\nhost=github.com\n\n" \| git credential-osxkeychain erase` |
| `Repository not found` | Le dépôt n'existe pas encore, ou son nom diffère | Créez-le d'abord sur GitHub (étape 1) |
| `could not read Password` | Terminal non interactif | Relancez la commande dans une fenêtre de Terminal ordinaire |

</details>

### 3. Activer Pages

Dépôt → **Settings** → **Pages** → *Build and deployment* → **Source : Deploy from a branch**, branche `main`, dossier `/ (root)`.

Le site paraît en une à deux minutes à `https://hugobibliothecarius.github.io`. **Vérifiez qu'il s'affiche correctement avant de passer au domaine.**

### 4. Déclarer le domaine

Dépôt → **Settings** → **Pages** → *Custom domain* → `hugobernier.info` → **Save**.

Le fichier `CNAME` est déjà dans le dépôt, ce qui revient au même; déclarer le domaine dans l'interface vous donne en plus la vérification de la configuration.

### 5. Vérifier le domaine

Dépôt → **Settings** → **Pages** → **Verify domain**. GitHub fournit un enregistrement `TXT` à ajouter chez Porkbun. Cette étape empêche tout autre compte GitHub d'utiliser votre domaine.

### 6. Configurer le DNS chez Porkbun

Retirez d'abord les enregistrements de stationnement que Porkbun a pu créer.

| Type | Hôte | Valeur |
| --- | --- | --- |
| `ALIAS` | *(vide)* | `hugobibliothecarius.github.io` |
| `CNAME` | `www` | `hugobibliothecarius.github.io` |

Porkbun gère les `ALIAS`, ce qui est la façon la plus simple pour un domaine racine. Si vous préférez les enregistrements `A`, il en faut quatre — `185.199.108.153`, `185.199.109.153`, `185.199.110.153`, `185.199.111.153` — plus quatre `AAAA` pour IPv6 : `2606:50c0:8000::153`, `2606:50c0:8001::153`, `2606:50c0:8002::153`, `2606:50c0:8003::153`.

L'enregistrement `www` n'est pas facultatif en pratique : GitHub recommande de le configurer, et crée alors la redirection entre `hugobernier.info` et `www.hugobernier.info` dans les deux sens.

### 7. Forcer HTTPS

Dépôt → **Settings** → **Pages** → cochez **Enforce HTTPS**.

L'option peut mettre jusqu'à 24 heures à devenir disponible, le temps que le certificat soit émis. La propagation DNS prend elle aussi jusqu'à 24 heures.

## Modifier la page ensuite

Modifiez `index.md`, enregistrez, envoyez. GitHub reconstruit le site en une minute. Vous pouvez éditer directement dans l'interface web de GitHub : ouvrez le fichier, cliquez le crayon, modifiez, **Commit changes**.

Pour voir le rendu avant d'envoyer, ouvrez `apercu.html` dans un navigateur. Il reproduit les styles réels, mais n'est pas régénéré automatiquement : c'est un aperçu de la mise en forme, pas du contenu à jour.

## Si ça ne marche pas

| Symptôme | Cause habituelle |
| --- | --- |
| Erreur 404 à l'adresse `github.io` | Pages pas encore activé, ou la construction a échoué — voir l'onglet **Actions** du dépôt |
| La page s'affiche sans styles | Le dossier `assets/` n'a pas été téléversé, ou son nom a changé |
| Le domaine ne répond pas | DNS pas encore propagé; attendez, puis vérifiez avec `dig hugobernier.info +noall +answer` |
| « Enforce HTTPS » reste grisé | Le certificat n'est pas encore émis. Jusqu'à 24 heures |
| Le portrait ne s'affiche pas | Vérifiez que `assets/hugo-bernier.jpg` est bien dans le dépôt, nom et casse compris |

## Sources

- [Creating a GitHub Pages site](https://docs.github.com/en/pages/getting-started-with-github-pages/creating-a-github-pages-site)
- [Managing a custom domain for your GitHub Pages site](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site)
- [Verifying your custom domain for GitHub Pages](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/verifying-your-custom-domain-for-github-pages)
- [Securing your GitHub Pages site with HTTPS](https://docs.github.com/en/pages/getting-started-with-github-pages/securing-your-github-pages-site-with-https)
