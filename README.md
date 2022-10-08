# NextJS + Eslint + Prettier + husky

## eslint --init

```
$ npx eslint --init

You can also run this command directly using 'npm init @eslint/config'.
? How would you like to use ESLint? …
  To check syntax only
  To check syntax and find problems
❯ To check syntax, find problems, and enforce code style

? What type of modules does your project use? …
❯ JavaScript modules (import/export)
  CommonJS (require/exports)
  None of these

? Which framework does your project use? …
❯ React
  Vue.js
  None of these

? Does your project use TypeScript? › No / Yes

? Where does your code run? · browser

? How would you like to define a style for your project? …
❯ Use a popular style guide
  Answer questions about your style

? Which style guide do you want to follow? …
❯ Standard: https://github.com/standard/eslint-config-standard-with-typescript
  XO: https://github.com/xojs/eslint-config-xo-typescript

? What format do you want your config file to be in? · JavaScript

? Would you like to install them now? › No / Yes

? Which package manager do you want to use? …
  npm
❯ yarn
  pnpm
```

### 最終的に

```
✔ How would you like to use ESLint? · style
✔ What type of modules does your project use? · esm
✔ Which framework does your project use? · react
✔ Does your project use TypeScript? · No / Yes
✔ Where does your code run? · browser
✔ How would you like to define a style for your project? · guide
✔ Which style guide do you want to follow? · standard-with-typescript
✔ What format do you want your config file to be in? · JavaScript
Checking peerDependencies of eslint-config-standard-with-typescript@latest
The config that you've selected requires the following dependencies:

eslint-plugin-react@latest eslint-config-standard-with-typescript@latest @typescript-eslint/eslint-plugin@^5.0.0 eslint@^8.0.1 eslint-plugin-import@^2.25.2 eslint-plugin-n@^15.0.0 eslint-plugin-promise@^6.0.0 typescript@*
✔ Would you like to install them now? · No / Yes
✔ Which package manager do you want to use? · yarn
Installing eslint-plugin-react@latest, eslint-config-standard-with-typescript@latest, @typescript-eslint/eslint-plugin@^5.0.0, eslint@^8.0.1, eslint-plugin-import@^2.25.2, eslint-plugin-n@^15.0.0, eslint-plugin-promise@^6.0.0, typescript@*
```

## .eslintingnore

```
$ touch .eslintignore
```

.eslintignore

```
.next
next-env.d.ts
node_modules
yarn.lock
package-lock.json
public
```

## prettier パッケージインストール

```
$ yarn add -D prettier eslint-config-prettier
```

## .prettierrc

```
$ touch .prettierrc
```

.prettierrc

```
{
  "endOfLine": "lf",
  "printWidth": 80,
  "tabWidth": 2,
  "trailingComma": "es5",
  "singleQuote": true,
  "jsxSingleQuote": true,
  "semi": true
}
```

## .prettieringnore

```
$ touch .prettierignore
```

.prettierignore

```
.next
next-env.d.ts
node_modules
yarn.lock
package-lock.json
public
```

## .vscode

```
$ mkdir .vscode
$ cd .vscode
$ touch setting.json
```

setting.json

```
{
  "window.zoomLevel": 1,
  "liveSassCompile.settings.autoprefix": [],
  "liveSassCompile.settings.includeItems": ["**/scss/*.scss"],
  "liveSassCompile.settings.formats": [
    {
      "format": "expanded",
      "extensionName": ".css",
      "savePath": "~/../css"
    },
    {
      "format": "compressed",
      "extensionName": ".min.css",
      "savePath": "~/../css"
    }
  ],
  "workbench.statusBar.visible": true,
  "editor.minimap.enabled": true,
  "workbench.activityBar.visible": true,
  "workbench.sideBar.location": "left",
  "[scss]": {
    "editor.suggest.insertMode": "replace"
  },
  "diffEditor.ignoreTrimWhitespace": false,
  "workbench.panel.defaultLocation": "right",
  "workbench.editorAssociations": {
    "*.ipynb": "jupyter.notebook.ipynb",
    "*.mp4": "default"
  },
  "editor.formatOnSave": true,
  "editor.suggestSelection": "first",
  "vsintellicode.modify.editor.suggestSelection": "automaticallyOverrodeDefaultValue",
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "prettier.prettierPath": "node_modules/prettier",
  "liveServer.settings.donotShowInfoMsg": true,
  "eslint.format.enable": true,
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  },
  "workbench.iconTheme": "vscode-icons",
  "git.autofetch": true,
  "eslint.execArgv": null
}
```

## .eslintrc.js

```
module.exports = {
  env: {
    browser: true,
    es2021: true,
  },
  extends: ["plugin:react/recommended", "standard", "prettier"],
  parser: "@typescript-eslint/parser",
  parserOptions: {
    ecmaFeatures: {
      jsx: true,
    },
    ecmaVersion: "latest",
    sourceType: "module",
  },
  plugins: ["react", "@typescript-eslint"],
  rules: {
    "react/react-in-jsx-scope": "off",
  },
  settings: {
    react: {
      version: "18.2.0",
    },
  },
};
```

## husky

```
$ npx husky-init
```

.husky/pre-commit

```
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

# bash echo color
RED='\033[1;31m'
GREEN='\033[1;32m'
BLUE='\033[1;36m'
BOLD='\033[1;37m'
NC='\033[0m'

echo "\n 🚧🏗️  ${BOLD}Checking format, lint and types in your project before committing${NC}"

# Check Prettier standards
npm run check-format ||
(
    echo "\n ❌🟨 Prettier Check ${RED}Failed${NC}. 🟨❌\n Run ${BLUE}npm run format${NC}, add changes and try commit again.\n";
    false;
)

# Check ESLint Standards
npm run check-lint ||
(
    echo "\n ❌🟪 ESLint Check ${RED}Failed${NC}. 🟪❌\n Make the required changes listed above, add changes and try to commit again.\n"
    false;
)

# Check tsconfig standards
npm run check-types ||
(
    echo "\n ❌🟦 Type check ${RED}Failed${NC}. 🟦❌\n Make the changes required above.\n"
    false;
)

# If everything passes... Now we can build
echo "🔥🚀 ${BOLD}All passed... Now we can build.${NC} 🚀🔥"

npm run build ||
(
    echo "\n ❌🟩 Next build ${RED}Failed${NC}. 🟩❌\n View the errors above to see why.\n"
    false;
)

# If everything passes... Now we can commit
echo "✅✅ ${GREEN}Build is completed... I am committing this now.${NC} ✅✅\n"
```

## package.json の script

```
"scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint",
    "prepare": "husky install",
    "check-types": "tsc --pretty --noEmit",
    "check-format": "prettier --check .",
    "check-lint": "eslint . --ext ts --ext tsx --ext js",
    "format": "prettier --write .",
    "test-all": "npm run check-format && npm run check-lint && npm run check-types"
},
```

