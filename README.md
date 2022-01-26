# electron-vite-boilerplate

![GitHub license](https://img.shields.io/github/license/caoxiemeihao/electron-vite-boilerplate?style=flat)
![GitHub stars](https://img.shields.io/github/stars/caoxiemeihao/electron-vite-boilerplate?color=fa6470&style=flat)

## Overview

This is a project structure compact enough Electron + Vite template  

The project focus on the cooperation between Vite and Electron

:electron: Enabled Electron and NodeJs in Renderer-process by **[vite-plugin-electron-renderer](https://www.npmjs.com/package/vite-plugin-electron-renderer)**.  

**Like this**

```js
// In Renderer-process

import { readFileSync } from 'fs'
import { ipcRenderer } from 'electron'

readFileSync(/* something */)
ipcRenderer.on(/* something */)
```

## Feature

- [x] [SQLite3](https://www.npmjs.com/package/sqlite3)
- [x] [SerialPort](https://www.npmjs.com/package/serialport)
- [x] [electron-store](https://www.npmjs.com/package/electron-store)
- [x] `Vue3`

## Run Setup

  ```bash
  # clone the project
  git clone git@github.com:caoxiemeihao/electron-vite-boilerplate.git

  # enter the project directory
  cd electron-vite-boilerplate

  # install dependency
  npm install

  # develop
  npm run dev
  ```

## Directory

Once `dev` or `build` npm-script executed will be generate named `dist` folder. It has children dir of same as `src` folder, the purpose of this design can ensure the correct path calculation.

```tree
├── dist                      Tt's generated according to the "src" directory
├   ├── main
├   ├── preload
├   ├── renderer
├
├── scripts
├   ├── build.mjs             Build script, for -> npm run build
├   ├── vite.config.mjs       Marin-process, Preload-script vite-config
├   ├── watch.mjs             Develop script, for -> npm run dev
├
├── src
├   ├── main                  Main-process source code
├   ├── preload               Preload-script source code
├   ├── renderer              Renderer-process source code
├       ├── vite.config.ts    Renderer-process vite-config
├
```

## 🚧 Use SerialPort, SQLite3 or other node-native addons in Renderer-process

1. First, yout need to make sure the deps in "dependencies". Because the project still needs it after packaged.

2. Second, node-native addons may not be [Pre-Bundling](https://vitejs.dev/guide/dep-pre-bundling.html) correctly by Vite. So you need avoid this, and import it like ordinary NodeJs module.

3. Third, through the above description, you can correctly import node-native addons in two ways.

**The first way** - Use CommonJS syntax

```js
// In Renderer-process
const SerialPort = require('serialport')
const sqlite3 = require('sqlite3')
```

**The second way** - Use ESModule syntax - **Recommend 🎉**

```js
// In Renderer-process
import serialport from 'serialport'
import sqlite3 from 'sqlite3'
```

This way is actually just a syntax-sugar of CommonJS syntax. At the same time, [vite-plugin-electron-renderer](https://www.npmjs.com/package/vite-plugin-electron-renderer) needs to be configured.

**Click to view more** 👉 [src/renderer/vite.config.ts](https://github.com/caoxiemeihao/electron-vite-boilerplate/blob/main/src/renderer/vite.config.ts)

```js
import electronRenderer from 'vite-plugin-electron-renderer'

export default {
  plugins: [
    electronRenderer({
      // configuration here
      resolve: {
        serialport: 'export default require("serialport");',
        sqlite3: 'export default require("sqlite3");',
      },
    }),
  ],
}
```

## Use SerialPort, SQLite3 or other node-native addons in Main-process

Main-process, Preload-script are also built with Vite, and they are just built as [build.lib](https://vitejs.dev/config/#build-lib).  
So they just need to configure Rollup.  

**Click to view more** 👉 [scripts/vite.config.mjs](https://github.com/caoxiemeihao/electron-vite-boilerplate/blob/main/scripts/vite.config.mjs)

```js
export default {
  build: {
    // built lib for Main-process, Preload-script
    lib: {
      entry: 'index.ts',
      formats: ['cjs'],
      fileName: () => '[name].js',
    },
    rollupOptions: {
      // configuration here
      external: [
        'serialport',
        'sqlite3',
      ],
    },
  },
}
```

## ⚠️ pnpm

> The "node_modules" file structure generated by "pnpm" will lead to errors in the packaging of "electron builder". For example, the project needs to use "SQLite3"

## Branch

#### [esbuild](https://github.com/caoxiemeihao/electron-vite-boilerplate/tree/esbuild)
- Use `Vite` build `Renderer-process`.
- Use `esbuild` build `Main-process` and `Preload-script`.

#### [webpack](https://github.com/caoxiemeihao/electron-vite-boilerplate/tree/webpack)

- [x] `React` + `Antd` + `Less`
- [x] Support `swc-loader`
- [x] Support `babel-lader`
- [x] Integrate `electron-store`
- [x] Integrate `sqlite3`

## Wechat

<img width="244px" src="https://raw.githubusercontent.com/caoxiemeihao/blog/main/assets/wechat/group/qrcode.jpg" />
