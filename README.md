# Concaténation et Gestion des Traductions pour un Projet Angular

Bienvenue dans ce projet Angular qui intègre un **script Node.js** permettant de :

1. Concaténer et fusionner automatiquement les fichiers de traduction `.json` par langue.
2. Générer ou mettre à jour un seul fichier de traduction consolidé pour chaque langue.
3. Mettre à jour le `TRANSLATION_GUID` dans le fichier de configuration (`translation.ts`) afin de garder un suivi précis des versions de traductions.

---

## Aperçu du script

Le script concat-i18n.js permet de :

-   Parcourir tous les sous-répertoires de `src/assets/i18n/<lang>` pour collecter les fichiers `.json`.
-   Fusionner ces fichiers de traduction pour créer ou mettre à jour un seul fichier `<lang>_<GUID>.json`.
-   Maintenir un identifiant unique (`TRANSLATION_GUID`) dans votre fichier de config (`translation.ts`), afin de toujours charger la version la plus récente des traductions.

> Note: Dans ce script, la langue gérée par défaut est le français (fr). Un exemple pour l'anglais (en) est également présent mais commenté.

---

## Prérequis

-   [Node.js](https://nodejs.org/) (version 14+ recommandée).
-   [npm](https://buff.ly/3EhUsuf) ou [yarn](https://yarnpkg.com/) pour exécuter les scripts.
-   Un projet Angular configuré avec des dossiers de traduction localisés sous `src/assets/i18n/`.

---

## Installation

1. **Cloner ou télécharger** le projet.
2. **Placez le script `concat-i18n.js`** à la racine du projet ou dans le dossier de votre choix (ex. : `./src/config/translation/`).

---

## Configuration du script

-   **Fichiers de ressources** : par défaut, le script lit tous les fichiers se terminant par `.json` dans le dossier `./src/assets/i18n/<lang>/` (n'hésitez pas à modifier selon votre architecture).
-   **Dossier de sortie** : les fichiers concaténés sont générés dans `./src/assets/i18n/langs/` (n'hésitez pas à modifier selon votre architecture).
-   **Fichier de configuration** : réglez la constante `configFile` dans le script vers votre fichier `translation.ts` (ou un autre fichier TypeScript), où se trouve la variable `TRANSLATION_GUID` (n'hésitez pas à modifier selon votre architecture).
    Voici un exemple de configuration pour @ngx-translate:

    ```
    import { registerLocaleData } from '@angular/common';
    import { HttpClient } from '@angular/common/http';
    import localeFrExtra from '@angular/common/locales/extra/fr';
    import localeFr from '@angular/common/locales/fr';
    import {
    provideTranslateService,
    TranslateLoader,
    TranslateModuleConfig,
    } from '@ngx-translate/core';
    import { TranslateHttpLoader } from '@ngx-translate/http-loader';

    // register locale fr
    registerLocaleData(localeFr, 'fr-FR', localeFrExtra);

    export const TRANSLATION*GUID = 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx';
    export function HttpLoaderFactory(http: HttpClient) {
        return new TranslateHttpLoader(
            http,
            './assets/i18n/langs/',
            '*' + TRANSLATION_GUID + '.json',
        );
    }

    const config: TranslateModuleConfig = {
        defaultLanguage: 'fr',
        loader: {
            provide: TranslateLoader,
            useFactory: HttpLoaderFactory,
            deps: [HttpClient],
        },
    };

    export function provideTranslation() {
        return provideTranslateService(config);
    }

    ```

Assurez-vous que le chemin vers `translation.ts` dans le script (`configFile`) est correct :

```
const configFile = './src/config/translation/translation.ts';
```

---

## Utilisation

1. **Préparer vos fichiers de traductions** :
    - Créez un dossier par langue : `./src/assets/i18n/fr/` pour le français, `./src/assets/i18n/en/` pour l’anglais, etc.
    - Dans chacun de ces dossiers, placez vos fichiers `.json` (par exemple : `common.json`, `user.json`, `task.json`, etc.).
2. **Lancer le script** :

    - Depuis la racine du projet, exécutez :

        ```bash
        node src/config/translation/concat-i18n.js
        ```

    - Le script va :
        1. Récupérer tous les fichiers de la langue (`fr` par défaut).
        2. Les fusionner.
        3. Vérifier si un fichier existant `<lang>_<GUID>.json` est déjà présent dans `./src/assets/i18n/langs/`.
        4. Si les traductions sont inchangées, il met à jour le `TRANSLATION_GUID` si nécessaire. Sinon, il génère un nouveau fichier de traduction unique pour la langue avec un GUID mis à jour.

3. **Inclure le nouveau fichier de traduction** :

    - Dans votre application Angular, assurez-vous que le chargement des ressources de traduction est basé sur la valeur de `TRANSLATION_GUID` stockée dans `translation.ts`.
    - Par exemple, un service d’internationalisation pourrait charger les traductions à partir de l’URL :

        ```bash
        /assets/i18n/langs/fr_<TRANSLATION_GUID>.json

        ```

4. **Activer d’autres langues** (exemple `en`) :

    - Dans le script, décommentez l’appel :

        ```
        // if (!concat('en')) {
        //   process.exit(1);
        // }

        ```

    - Créez et renseignez vos fichiers de traduction en anglais dans `./src/assets/i18n/en/`.
    - Relancez le script.

---

## Arborescence recommandée

Voici un exemple simplifié de l’organisation de vos fichiers :

```lua
my-angular-project/
├─ src/
│  ├─ app/
│  ├─ assets/
│  │  └─ i18n/
│  │     ├─ fr/
│  │     │  ├─ common.json
│  │     │  └─ errors.json
│  │     └─ langs/
│  │        └─ fr_<GUID>.json
│  └─ config/
│     └─ translation/
│        └─ translation.ts
│        └─ concat-i18n.js
├─ package.json
└─ README.md


```

---

## Exemple de configuration `package.json`

Pour automatiser l’exécution du script, vous pouvez ajouter un script dans votre `package.json` :

```json
{
	"scripts": {
		"concat-i18n": "node src/config/translation/concat-i18n.js"
	}
}
```

Ainsi, vous pouvez simplement exécuter :

```bash
npm run concat-i18n

```

ou

```bash
yarn concat-i18n

```
