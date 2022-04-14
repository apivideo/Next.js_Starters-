# Setup ESLint Prettier and Husky with Next.js

## Table of contents

-   [Setup ESLint Prettier and Husky with Next.js](#setup-eslint-prettier-and-husky-with-nextjs)
    -   [Table of contents](#table-of-contents)
    -   [Project description](#project-description)
    -   [Getting Started](#getting-started)
        -   [Creating the Next.JS App with npm](#creating-the-nextjs-app-with-npm)
        -   [Setting up TypeScript](#setting-up-typescript)
        -   [Custom tsconfig.json](#custom-tsconfigjson)
    -   [Setting up ESLint](#setting-up-eslint)
        -   [eslint-config-airbnb](#eslint-config-airbnb)
    -   [Setting up Prettier](#setting-up-prettier)
        -   [Installation Prettier](#installation-prettier)
        -   [Customize Prettier](#customize-prettier)
        -   [Ignore Prettier](#ignore-prettier)
    -   [Setting up Husky](#setting-up-husky)
        -   [Install Husky](#install-husky)
        -   [Update package.json](#update-packagejson)
        -   [Editing the pre-commit hook](#editing-the-pre-commit-hook)
        -   [Configure vs code to format on save](#configure-vs-code-to-format-on-save)

## Project description

How to set up ESLint, Prettier, and Husky to consistently write and style your code using and extending Airbnb’s Typescript style guidelines. and in bonus part, we will setup **vs code** configuration to enable `auto-format` and `auto-style`.

## Getting Started

### Creating the Next.JS App with npm

```bash
npx create-next-app starter_next.js --use-npm
```

### Setting up TypeScript

To enable TypeScript in a Next.js app, add a `tsconfig.json` file to the root of the project.

```bash
npm install --save-dev typescript @types/react @types/node
```

We can now create files with `.ts` or `.tsx` extensions. Change our `index.js` file to `index.tsx` and run `npm run dev`

Next.js will automatically configure the `tsconfig` file with default values for Next.JS.

### Custom tsconfig.json

Modify the `tsconfig.json` file a little bit to match our standards. More [details](https://www.typescriptlang.org/tsconfig)

```JSON
{
  "compilerOptions": {
    "target": "es6", // upgrade to from es5 to es6
    "allowUnreachableCode": false, // add => raises compiler errors about unreachable code
    "allowUnusedLabels": false, // add =>  raises compiler errors about unused labels
    "noFallthroughCasesInSwitch": true, // add => Ensures that any non-empty case inside a switch statement includes either break or return
    "noImplicitReturns": true, // add => Check all code paths in a function to ensure they return a value.
    "sourceMap": true, // add => Enables the generation of sourcemap files. Easier to debug
    "strict": true, // turn to true => Enables a wide range of type checking behavior
    "lib": ["dom", "dom.iterable", "esnext"],
    "allowJs": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "noEmit": true,
    "incremental": true,
    "esModuleInterop": true,
    "module": "esnext",
    "moduleResolution": "node",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "jsx": "preserve"
  },
  "include": ["next-env.d.ts", "**/*.ts", "**/*.tsx"],
  "exclude": ["node_modules"]
}
```

## Setting up ESLint

```bash
# If you encounter some conflicts, remove eslint-config-next package
npm uninstall eslint-config-next

# Configure
npx eslint --init
```

### eslint-config-airbnb

We choose eslint google config in this case, it looks like that

````md
✔ How would you like to use ESLint? · style
✔ What type of modules does your project use? · esm
✔ Which framework does your project use? · react
✔ Does your project use TypeScript? · No / Yes
✔ Where does your code run? · browser
✔ How would you like to define a style for your project? · guide
✔ Which style guide do you want to follow? · google
✔ What format do you want your config file to be in? · JavaScript

```bash
# Don't forget to reinstall the package
npm install eslint-config-next
```
````

## Setting up Prettier

Codes formatter ⇒ It removes all original styling and ensures that all outputted code conforms to a consistent style.

### Installation Prettier

```bash
#Install Prettier
npm install --save-dev --save-exact prettier

#To avoid format conflicts between ESLint and Prettierr.
# It turns off all ESLint rules that are unnecessary or might conflict with Prettier.
npm install --save-dev eslint-config-prettier

#To make Prettier cooperate with ESLint; add "prettier" to the extends array in your eslintrc.js file.
# So it now should look like this:
extends: ["plugin:react/recommended", "airbnb", "prettier"],
```

### Customize Prettier

To customize Prettier, create a `.prettierrc` file in the root of your directory

```JSON
{
    "trailingComma": "es5",
    "tabWidth": 4,
    "semi": false,
    "singleQuote": true,
    "printWidth": 120
}
```

### Ignore Prettier

Create `.prettierignore` and `.eslintignore`. Configure both _ESLint_ and _Prettier_ to ignore formatting or linting certain files or directories.

```bash
.next
next-env.d.ts
node_modules
yarn.lock
package-lock.json
public
```

## Setting up Husky

We are going to be using husky to perform the following whenever a git commit is made:

-   Check there are no Prettier no errors on our code.
-   Check there are no ESLint no errors on our code.
-   Check there are no errors compiling our code from TypeScript.
-   Check we can build our project using next build

### Install Husky

```bash
npm install husky --save-dev
npx husky-init
```

### Update package.json

```json
"scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "check-types": "tsc --pretty --noEmit",
    "check-format": "prettier --check .",
    "check-lint": "eslint . --ext ts --ext tsx --ext js",
    "format": "prettier --write .",
    "test-all": "npm run check-format && npm run check-lint && npm run check-types && npm run build",
    "prepare": "husky install"
  },
```

-   **check-types** runs TypeScript's tsc CLI command and pretty prints any warnings/errors.
-   **check-format** asks Prettier to check all of our files (excluding the ones in .prettierignore) for formatting issues.
-   check-lint asks ESLint to check for any linting warnings/errors on an .ts, .tsx or .js files.
-   format tells prettier to automatically re-write all of our files with proper formatting
-   test-all runs a number of the above commands in sequence.

### Editing the pre-commit hook

it runs prior to executing a git commit. It is fully customable [Example](https://github.com/jarrodwatts/code-like-google/blob/main/.husky/pre-commit)

```bash
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

npm test-all
```

### Configure vs code to format on save

1. Create a `.vscode` folder at the root of your directory
2. Within that folder, create a `settings.json` file.
3. Change your settings.json to:

```JSON
{
"editor.formatOnPaste": true,
"editor.formatOnSave": true,
"editor.defaultFormatter": "esbenp.prettier-vscode",
"editor.codeActionsOnSave": {
 "source.fixAll.eslint": true,
 "source.fixAll.format": true
}
}
```
