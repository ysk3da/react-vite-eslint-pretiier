# development memo

```sh
encoolus@EncoolUs-2 react-vite-eslint-pretiier % npm create vite react-vite --template react-ts 
Need to install the following packages:
  create-vite
Ok to proceed? (y) y
✔ Select a framework: › React
✔ Select a variant: › TypeScript + SWC

Scaffolding project in /Users/encoolus/Desktop/Github/@ysk3da/react-vite-eslint-pretiier/react-vite...

Done. Now run:

  cd react-vite
  npm install
  npm run dev

encoolus@EncoolUs-2 react-vite-eslint-pretiier % npm create @eslint/config
Need to install the following packages:
  @eslint/create-config
Ok to proceed? (y) y
✔ How would you like to use ESLint? · style
✔ What type of modules does your project use? · esm
✔ Which framework does your project use? · react
✔ Does your project use TypeScript? · No / Yes
✔ Where does your code run? · browser
✔ How would you like to define a style for your project? · prompt
✔ What format do you want your config file to be in? · YAML
✔ What style of indentation do you use? · 4
✔ What quotes do you use for strings? · single
✔ What line endings do you use? · unix
✔ Do you require semicolons? · No / Yes
The config that you've selected requires the following dependencies:

eslint-plugin-react@latest @typescript-eslint/eslint-plugin@latest @typescript-eslint/parser@latest
✔ Would you like to install them now? · No / Yes
✔ Which package manager do you want to use? · npm
Installing eslint-plugin-react@latest, @typescript-eslint/eslint-plugin@latest, @typescript-eslint/parser@latest

added 222 packages, and audited 223 packages in 28s

88 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities
Successfully created .eslintrc.yml file in /Users/encoolus/Desktop/Github/@ysk3da/react-vite-eslint-pretiier
encoolus@EncoolUs-2 react-vite-eslint-pretiier % 
```

##

```
npm i -D prettier eslint-config-prettier
```

設定ファイル

prettierrc.yml

```yml
tabWidth: 2
singleQuote: true
trailingComma: all
semi: false
useTabs: false
```

必須設定

その１：eslint-config-prettier を .eslintrc.yml に指定

```yml
extends:
  - ...
  - prettier #eslint-config-prettier
```

その２：react/react-in-jsx-scope: off を .eslintrc.yml に設定

このまま lint すると、`’React’ must be in scope when using JSX のエラーがでますが、現在はimport React from ‘react’ は不要になったとのことなので、rule に追加します。

```yml
rules:
  react/react-in-jsx-scope: off
```

その３：react のバージョンを .eslintrc.yml に設定

lint を実行すると以下の警告もでます。github に書いてあるように設定の追加をします。

```yml
settings:
  react:
    version: detect
```

その４：package.json にスクリプトを追加

最後に、lint で eslint と prettier 両方を実行するようにしています。

```json
"scripts": {
    ...
    "lint": "eslint --ext .js,.tsx,.ts ./src && prettier --write \"**/*.+(js|json|yml|ts|tsx)\""
}
```

## その他 追加設定

### import の並び順 / 未使用 import の自動整列,削除

eslint-plugin-import や eslint-plugin-unused-imports もありますが、フォーマットは prettier の役割なので prettier-plugin-organize-imports を使用します。これはインストールするだけでOKです。

```sh
npm i --dev prettier-plugin-organize-imports
```

### react hooks のルールチェック

```
npm i --dev eslint-plugin-react-hooks
```

ymlファイルに追加します。

.eslintrc.yml

```diff
extends:
  - eslint:recommended
  - plugin:react/recommended
+  - plugin:react-hooks/recommended
  - plugin:@typescript-eslint/recommended
  - prettier #eslint-config-prettier
  ```

## パスのエイリアス解決

import などのときに、@ などエイリアスが指定できるようにします。以下を追加することで、vite 側で alias を書かなくても、tsconfig の設定を見て解決してくれるようになります。

```sh
npm i --dev vite-tsconfig-paths
```

vite.config.ts

```ts
import react from '@vitejs/plugin-react'
import { defineConfig } from 'vite'
import tsconfigPaths from 'vite-tsconfig-paths'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [react(), tsconfigPaths()],
})
```

あとは、tsconfig.json にエイリアス設定をします。

```json
"compileOptions": {
    ...
    "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"]
    },  
}
```

## References

[React + Vite + eslint + prettier でプロジェクトひな形作成](https://kasyalog.site/blog/project-base-react-vite-eslint-prettier/)