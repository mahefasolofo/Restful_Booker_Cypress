# Installation et configuration Cypress

## 1. Créer un nouveau projet
    - Création nouveau Repository dans Github avec fichier Readme.md
    - npm init : fichier Json créé avec les paramètres par défaut

## 2. Installer Cypress
    - ``npm install cypress --save-dev`` : 
        node_modules et package-lock.json créés 

## 3. Configurer Cypress avec Typescript
    - ``npm install --save-dev typescript`` : 
    - Création de tsconfig.json à la racine du projet : avec les valeurs donné dans la documentation de cypress : `npx tsc --init`
    - - essai d'un premier tests E2E : ``npx cypress open`` :
fichier : cypress.config.ts example.cy.ts fichiers dans support er fixtures . créés


- Ajouter  `lint` commande au fichier `package.json`: 
```js
{  
  "scripts": {  
    "lint": "tsc --noEmit"  
  }  
}
```

    


## 4. Ajouter une intégration CI dans Github Actions
1.  Créez un nouveau dépôt GitHub ou utilisez un dépôt existant pour votre projet.
    
2.  Accédez à l'onglet "Actions" dans votre dépôt GitHub pour créer un nouveau workflow.
    
3.  Utilisez l'éditeur de workflow intégré pour créer une configuration de workflow `ci.yml`. La configuration de workflow doit définir les étapes de votre pipeline CI, comme le déploiement, les tests et les vérifications de build.
    
4.  Ajoutez des actions GitHub prédéfinies ou des actions personnalisées pour automatiser les étapes de votre pipeline CI.
    
5.  Testez votre workflow en effectuant une modification dans votre dépôt GitHub et vérifiez que les étapes de votre pipeline CI sont déclenchées correctement.
    
6.  Configurez les paramètres de déclenchement pour définir quand le workflow doit être déclenché, comme lorsque des modifications sont apportées à un certain type de fichier ou lorsque une demande de tirage est ouverte ou fusionnée.
    
7.  Enregistrez et publiez votre workflow pour qu'il soit automatiquement déclenché lorsque des modifications sont apportées à votre dépôt.


## 5. Lancer les tests Cypress dans Github Actions

Création de rapport à l'aide de mochawesome
```bash
- `npm i mochawesome --save-dev`
- cypress.config.ts
	reporter: 'mochawesome',
- ` npx cypress run --reporter mochawesome`
```
Création d'un rapport HTML en local avec mochawesome

- installation de mochawesome-merge pour avoir un seul fichier .json 
`npm install mochawesome-merge --save-dev`

- merge : 
`npx mochawesome-merge "cypress/results/*.json" > mochawesome.json`

- générer HTML : 
`npx marge mochawesome.json`

les commandes sont enregistrés dans package.json


## 6. Configuration eslint et Prettier
### 0. Créer une nouvelle branche sur GIT
git branch eslint

pusher la branche dans Github

### a. Installer Prettier
npm install --save-dev --save-exact prettier

### b. Créer fichier .prettierrc.json 
On a le choix entre le style ES5 

```json
{  
  "trailingComma":"none",  
  "tabWidth": 4,  
  "semi": true,  
  "singleQuote": false  
}
```

ou une style plus moderne : sans point-virgule et avec des virgules à la fin 

```json
{  
  "trailingComma": "all",  
  "tabWidth": 2,  
  "semi": false,  
  "singleQuote": true  
}
```

### c. Configuration de VSCode
1.  Lancer l'ouverture rapide de VS Code (Ctrl+P)
2.  Exécutez la commande suivante  `ext install esbenp.prettier-vscode`
(plus précis que l'installation dans l'onglet extension de vscode, on est sûr de tomber sur la bonne extension)
4.  Ajouter les configurations dans .vscode/settings.json pour être sûr d'utiliser les bonnes extensions de formatage de code
	```json
	{  
  "editor.defaultFormatter": "esbenp.prettier-vscode",  
  "editor.formatOnSave": true  
}

NB : 
- Désormais, chaque fois que nous enregistrons un fichier JavaScript, il sera automatiquement formaté à l'aide de Prettier
- Il est mieux de formater le code quand on enregistre, mais pas quand on colle - car il ajoute toujours des sauts de ligne supplémentaires. Il est fortement recommandé de mettre les paramètres VSCode comme suivant

```json
{  
  "editor.defaultFormatter": "esbenp.prettier-vscode",  
  "editor.formatOnSave": true,  
  "editor.formatOnPaste": false  
}
```
### d. Formater à partir de la CLI
Formater chaque fichier au fur et à mesure que vous l'enregistrez est agréable, mais nous pouvons également formater TOUS les fichiers source à la fois à l'aide de Prettier CLI. Dans le `package.json`ajoutez un script pour formater les fichiers correspondant au masque et pour les réécrire sur le disque.
`Package.json`
```json
{  
  "name": "prettier-config-example",  
  "scripts": {  
    "format": "prettier --write \"./**/*.{js,ts}\""  //à personnaliser selon l'emplacement des fichiers
  },  
  "devDependencies": {  
    "prettier": "1.18.2"  
  }  
}
```

- Exécutez ce script NPM et les fichiers seront formatés pour suivre le style Prettier.
`npm run format`

### e. Formatage lors des commits (Husky)
Chaque fois que nous travaillons avec des fichiers localement, nous pouvons accidentellement faire des commits sans le formatage approprié. C'est là que les Git Hooks et le formatage des fichiers "staged" sont utiles. Pour formater de manière cohérente tous les fichiers avant de faire des commits , puis de valider les commits, il est recommandé d'utiliser la combinaison d'outils [husky](https://github.com/typicode/husky) + [lint-stage .](https://github.com/okonet/lint-staged)


Initialisons Husky à notre projet :

```Bash
npx husky-init && npm install       
```

Cette commande va fraîchement ajouter Husky à notre projet dans le dossier `.husky`. Dans ce dossier, nous pouvons créer des fichiers qui correspondent aux Git hooks que nous voulons utiliser.

installons lint-staged :

```Bash
npm i --save-dev lint-staged
```

Modifiez le Git Hook `pre-commit` dans `.husky`. Ceci lancera lint-staged avant qu'un commit puisse être poussé vers notre codebase.

```Shell
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

npx lint-staged
```

Ajoutez la configuration de lint-staged à `package.json` pour que lorsque certains fichiers sont mis à disposition pour un commit, nous lancions ESLint et Prettier.

package.json

```JSON
...,
"lint-staged":{
    "**/*.{js,jsx,ts,tsx}":[
      "npx prettier --write",
      "npx eslint --fix",
    ]
  }
```

### f. Utilisez Eslint avec Prettier 

Prettier reformate le code JavaScript pour suivre un certain style, il ne vérifie pas la signification du code. Par exemple, Prettier reformate avec plaisir le mauvais
Les linters statiques, comme [ESLint](https://eslint.org/) peuvent capturer l'affectation à une variable constante, nous avons donc besoin des deux :

-   Prettier reformatera le code pour être cohérent dans le style
-   ESLint analysera la signification du code et détectera les problèmes potentiels

#### Désactiver les règles de style dans ESLint

ESLint exécute une longue liste de règles par rapport au code, et certaines de ces règles sont stylistiques et peuvent entrer en conflit avec le style de Prettier. Nous devons donc configurer ESLint pour ignorer ces règles. Cette configuration se trouve dans le module [eslint-config-prettier](https://prettier.io/docs/en/integrating-with-linters.html#eslint) . Installez-le
`npm i -D eslint eslint-config-prettier`

et peut être ajouté à votre fichier de projet `.eslintrc.json` (à créer à la racine) . ESLint ne fonctionnera pas sans un fichier de configuration valide.
```json
{  
  "env": {  
    "es6": true  
  },  
  "extends": ["eslint:recommended", "prettier"]  
}
```
Exemple pour l'exécuter : `npx eslint projectC/index.js`


#### ESLint et React
Si vous souhaitez vérifier le code React qui utilise JSX, les mots-clés`import / export`, installez un plugin [eslint-plugin-react](https://www.npmjs.com/package/eslint-plugin-react)
`npm i -D eslint-plugin-react`

Et configurer`.eslintrc.json`

```json
{  
  "parserOptions": {  
    "ecmaVersion": 6,  
    "sourceType": "module"  
  },  
  "env": {  
    "browser": true  
  },  
  "extends": ["eslint:recommended", "plugin:react/recommended"]  
}
```

#### Intégrer ESLint dans VSCode

Ctrl+ P >_ ext install dbaeumer.vscode-eslint

Activer cette extension dans les paramètres de l'espace de travail VSCode : .vscode > setting.json

```json
{  
  "editor.defaultFormatter": "dbaeumer.vscode-eslint",  
  "editor.formatOnSave": true,  
  "eslint.enable": true  
}
```

#### Exécutez Prettier depuis ESLint

Installez la configuration et le plug-in ESLint Prettier

```bash
npm i -D eslint-config-prettier eslint-plugin-prettier
```
Pointez ESLint sur les paramètres recommandés qui incluent les styles Prettier
```json
{  
  "env": {  
    "node": true,  
    "es6": true  
  },  
  "extends": ["eslint:recommended", "plugin:prettier/recommended"],  
  "rules": {  
    "no-console": "off"  
  }  
}
```

Si vous décidez d'utiliser ESLint avec des règles Prettier et que vous avez configuré `husky`pour exécuter `lint-staged`, pointez-le sur au `eslint --fix`lieu de `prettier --write`.
package.json:

```json
"lint-staged": {
    "*.{js,md}": ["eslint --fix"]
  }
```

#### Configuration de VSCode + ESLint + Prettier + TypeScript
installez un nouveau module d'analyseur et de plug-in

npm i -D @typescript-eslint/parser @typescript-eslint/eslint-plugin

Ensuite, définissez les paramètres de l'espace de travail VSCode pour pelucher les fichiers TypeScript
``.vscode/settings.json
```json
{  
  "editor.defaultFormatter": "dbaeumer.vscode-eslint",  
  "editor.formatOnSave": true,  
  "eslint.enable": true,  
  "eslint.alwaysShowStatus": true,  
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  }, 
  "eslint.validate": [  
    "javascript",  
    "typescript"  
  ]  
}
```

#### Utilisez Prettier + ESLint + Cypress
installez le plugin : 
> npm i -D eslint-plugin-cypress

Ajouter les configurations dans .eslintrc.json

```json
{  
  "env": {  
    "cypress/globals": true  
  },  
  "extends": [  
    "eslint:recommended",  
    "plugin:prettier/recommended",  
    "plugin:cypress/recommended"  
  ],  
  "plugins": ["cypress"]  
}
```
