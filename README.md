# Google Apps Script 開発コンテナ

Visual Studio Code の DevContainer 内で TypeScript を使って快適に GAS 開発をしたい。

## 使用方法

1. build container

   プロジェクトルートフォルダを Visual Studio Code 開き、画面左下の ><ボタンかコマンドパレットから`Remote-Containers: Open Folder in container`を選択してビルド開始する。

1. login

   ビルドが終わったら新しくターミナルを開き以下のコマンドを実行する。

   ```
   clasp login --no-localhost
   ```

   画面の指示に従って Google にログインする。

1. create project

   新しく Google Apps Script プロジェクトを作成する場合は以下のコマンドを実行する。

   ```
   clasp create --title <Project Title> --type <standalone | sheets | froms | ...> --rootDir ./src
   ```

   - Project Title: GAS プロジェクト名と Sheets を使う場合の Sheets ファイル名になる。

- update project

  Google Apps Script に反映するには、以下のコマンドを実行する。

  ```
  clasp push
  ```

  ※ push 時、同じ名前のファイル（拡張子が ts と js で違うだけ）があるとエラーとなるので、.claspignore に\*.js を指定しておく

## おまけ

- node.js からの移植ライブラリを使用する。

  ex.cheerio の場合

  1. src/appsscript.json の dependencies に GAS ライブラリを追加する。
     ```
     "dependencies": {
         "libraries": [{
         "userSymbol": "Cheerio",
         "libraryId": "1ReeQ6WO8kKNxoaA_O0XEQ589cIrRvEBA9qcWpNqdOP17i47u6N9M5Xh0",
         "version": "12"
         }]
     },
     ```
  1. yarn で型定義ファイルのみを追加する。

     ```
     yarn add --dev @types/cheerio
     ```

  1. src/index.d.ts を作成し、型ファイルへの参照を設定する。

     ```
     /// <reference path="../node_modules/@types/cheerio/index.d.ts" />
     ```

     ※ ローカルで開いている時は node_modules 内が空の為、エラーになっているが気にしない。

  1. 使用するファイル内で import する

     ```
     import * as Cheerio from "cheerio"
     ```

     ※ ローカルで開いている時は node_modules 内が空の為、エラーになっているが気にしない。

     ※ GAS ライブラリの名前と合わせる必要がある。

  ※ 型定義ファイルと import は gs ファイルへの変換時に無視される。

  ※ 残ったライブラリ使用箇所は GAS ライブラリへの参照として実行される。

  ※ index.d.ts ファイルが残ってしまうので.claspignore に指定して除外する。

  ※ interface が異なったり、Promise を使用しているライブラリは動かない

- private 関数にする

  function をそのまま定義するとマクロ、スクリプトエディタ上から直接実行可能になる。

  プライベイト関数としたい場合は関数名の末尾に「\_」をつける。
