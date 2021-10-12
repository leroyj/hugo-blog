---
title: "🏗️ Comment ce blog a été bâti"
date: 2021-10-12T15:28:09+02:00
draft: false
tags: ["Howto", "Hugo"]
description: "Donne les détails techniques sur la manière dont ce blog a été monté"
---

_N'est on pas censé parler de technologie, après tout ?_

## Mes attentes

Je cherche avant tout une solution de publication sans maintenance qui me permette de me concentrer sur l'écriture de contenu et non pas sur la maintenance de la solution. Alors vous n'entendrez pas parler ici de CMS à architecture microservice à base d'AWS Lambda même si on pourrait mettre ça en place dans de futurs projets :)  
Je souhaite également disposer du support multilingue pour écrire en "Globish" :gb::us: et en Français :fr:.

## Hugo: Un générateur de site web statique

{{< figure src="https://upload.wikimedia.org/wikipedia/commons/a/af/Logo_of_Hugo_the_static_website_generator.svg" title="Logo du projet Hugo" alt="Logo du projet Hugo" height=100 >}}

Ce journal est généré par [Hugo](https://gohugo.io/) qui est un générateur de site web statique vraiment sympa comme peuvent l'être par exemple [Jekyll](https://jekyllrb.com/) ou [Gatsby](https://www.gatsbyjs.com/).  
Il prend en entrée de simple fichiers [Markdown (md)](https://en.wikipedia.org/wiki/Markdown) et génère la structure de votre site web dôté de fichiers html sympas, optimisés et rapides. (Hugo n'est pas responsable de la mauvaise note en accessibilité, je dois y travailler)

{{< figure src="/performance.png" title="Regardez un peu ces performances !" alt="Regardez un peu ces performances !" height=250 >}}

Par génération statique, on entend qu'aucune machine ou VM serveur n'est requise pour faire tournet votre site. Il vous suffit d'heberger vos fichiers sur un partage http tout bête.
Par conséquent, vous n'utilisez peu de ressources côté serveur et côté navigateur client.

## :art: Thème graphique

J'utilise le thème graphique [m10c](https://themes.gohugo.io/themes/hugo-theme-m10c/) légèrement personnalisé. Je l'ai trouvé simple et élégant. J'ai simplement ajusté la palette de couleur, changer l'avatar et modifié le gabarit liste pour ajouter la description du contenu.

Vous aurez besoin de [Sass](https://sass-lang.com/) pour modifier les fichiers scss.
Vous pouvez l'installer facilement sur Mac si vous avez déjà npm installé.

``` bash
npm -g sass
```

J'ai également procédé aux personnalisation suivantes.

## :fr::us: Support multilingue

Pour configurer le support multilingue, vous devez mettre en place la traduction des menus, dupliquer les contenus et ajouter quelques liens pour aider l'utilisateur à changer de langue.

Malheureusement, lorsque vous activez le multilingue, les pages 404 personnalisées par langue ne fonctionnent pas vraiment bien avec **GitHub Pages**.
**GitHub Pages** ne supporte qu'une unique page 404 nommée `404.html` à la racine du repo.
La seule manière alors est de copier la page 404 de la langue que vous aurez choisi à la racine du repo.
Par chance, cette tâche peut être automatisée avec **GitHub Actions** comme nous le verrons au prochain paragraphe.

## :building_construction: chaîne de déploiement

{{< figure src="../../../en/posts/deploy_workflow.drawio.png" title="Schéma de la chaîne de déploiement" alt="Schéma de la chaîne de déploiement" height=200 >}}

J'ai choisi GitHub Page comme solution d'hébergement. C'est aussi simple qu'un simple copier du contenu généré par Hugo (habituellement le contenu du dossier `public`) et d'un coller dans votre repo `<nickname>.github.io`.
Pour automatiser la tâche, vous pouvez utiliser un repo pour la source du contenu de votre blog et d'utiliser **Github Actions** pour automatiser le déploiement en utilisant ce [tutoriel d'intégration de Hugo dans GitHub](https://ruddra.com/hugo-deploy-static-page-using-github-actions/).
Voici comment.

```yaml {linenos=table,hl_lines=["4-8"],linenostart=24}
      - name: Build
        run: hugo --minify

      - name: copy 404 from en language to root
        uses: canastro/copy-file-action@master
        with:
          source: "./public/en/404.html"
          target: "./public/404.html"

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        if: github.ref == 'refs/heads/main'
        with:
          deploy_key: ${{ secrets.ACTIONS_DEPLOY_KEY }}
          external_repository: leroyj/leroyj.github.io
          publish_dir: ./public
````

## Support de Google Analytics [^1]

Pour suivre ma toute petite audience, j'utilise Google Analytics comme c'est l'outil que je connais le mieux et qu'on peut le mettre en oeuvre gratuitement.
La configuration est directe. Il vous suffit d'ajouter `googleAnalytics = 'G-YOURGAID'` au fichier de configuration `config.toml`.

## Reste à faire

Il reste beaucoup à faire pour rendre le blog un peu plus chatoyant.  
Ma liste de course est la suivante :

* configurer ma CMP pour supporter le [Réglement Général sur la protection des Données Européen (RGPD)](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX%3A32016R0679) :flag-eu:
* Ajouter des images et des vidéos
* Améliore l'accessibilité
* essayer le frontend graphique pour hugo
* essayer un module ecommerce for le fun et pas les bénéfices comme [Snipcart](https://snipcart-hugo-forestry.netlify.app/) :dollar:

[^1]: I only use Google Analytics for basic anonymous audience measurement. The configuration was tailored to explicitely avoid targeting and advertising. I will publish the details in a further post.
