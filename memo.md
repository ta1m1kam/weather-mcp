# package.json の設定メモ

## モジュールシステム

- `"type": "module"`
  - Node.js のモジュールシステムの設定
  - ES モジュール（ECMAScript Modules）を使用することを指定
  - `import/export`構文が使用可能

### モジュールタイプの比較

1. `"type": "module"` (ES モジュール)

   - モダンな JavaScript 仕様に準拠
   - `import/export`構文を使用
   - ファイル拡張子を必ず指定する必要がある
   - Top-level `await`が使用可能
   - `.js`ファイルがデフォルトで ES モジュールとして扱われる

2. `"type": "commonjs"` (デフォルト)
   - 従来の Node.js のモジュールシステム
   - `require()/module.exports`を使用
   - ファイル拡張子は省略可能
   - Top-level `await`は使用不可
   - `.js`ファイルが CommonJS として扱われる

### ファイル拡張子による挙動

- `.mjs` → 常に ES モジュールとして扱われる
- `.cjs` → 常に CommonJS として扱われる
- `.js` → package.json の`type`フィールドに従う

### 使い分けのポイント

- 新規プロジェクト → `"type": "module"`を推奨
- レガシープロジェクト → `"type": "commonjs"`を維持
- ライブラリ開発 → Dual Package（両方をサポート）を検討

## バイナリ設定

- `"bin"` セクション

```json
"bin": {
  "my-mcp-server": "./build/index.js"
}
```

- CLI ツールとしてインストールした時のエントリーポイントを指定
- `my-mcp-server`というコマンドで`./build/index.js`が実行可能

## スクリプト設定

- `"scripts"` セクション

```json
"scripts": {
  "build": "tsc && chmod 755 build/index.js"
}
```

- `npm run build`で実行されるスクリプトを定義
- TypeScript のコンパイル（`tsc`）を実行
- ビルドされたファイルに実行権限を付与（`chmod 755`）

## 配布ファイル設定

- `"files"` セクション

```json
"files": [
  "build"
]
```

- npm パッケージとして公開する際に含めるファイル/ディレクトリを指定
- `build`ディレクトリのみが含まれる

## 依存関係

- `dependencies`

  - 実行時に必要なパッケージ
  - `@modelcontextprotocol/sdk`が指定されている

- `devDependencies`
  - 開発時のみ必要なパッケージ
  - TypeScript と@types/node が指定されている

## プロジェクトの概要

このプロジェクトは TypeScript で書かれた MCP サーバーの CLI ツールとして設計されています。

## TypeScript 設定 (tsconfig.json)

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "Node16",
    "moduleResolution": "Node16",
    "outDir": "./build",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules"]
}
```

### コンパイラオプションの説明

1. ビルド出力の設定

   - `"target": "ES2022"` → コンパイル後の JavaScript バージョンを指定
   - `"outDir": "./build"` → コンパイル後のファイルの出力先
   - `"rootDir": "./src"` → ソースファイルのルートディレクトリ

2. モジュール関連の設定

   - `"module": "Node16"` → Node.js 16 以降のモジュールシステムを使用
   - `"moduleResolution": "Node16"` → モジュール解決方式を Node.js 16 準拠に設定
   - `"esModuleInterop": true` → CommonJS モジュールを ES モジュールとしてインポート可能に

3. 型チェックの設定
   - `"strict": true` → 厳密な型チェックを有効化
   - `"skipLibCheck": true` → 型定義ファイル（.d.ts）の型チェックをスキップ
   - `"forceConsistentCasingInFileNames": true` → ファイル名の大文字小文字を厳密にチェック

### ファイル設定

- `"include"`: コンパイル対象のファイル（`src`ディレクトリ以下のすべてのファイル）
- `"exclude"`: コンパイル対象から除外するファイル（`node_modules`ディレクトリ）

### 特徴

- Node.js 16 以降の最新機能を活用
- 厳密な型チェックによる高い型安全性
- ES モジュールとの互換性を確保
- クリーンなビルド出力構造（src → build）

## Weather Forecast MCP サーバーについて

このプロジェクトは、Model Context Protocol (MCP) を使用して天気予報情報を提供するサーバーを実装しています。

### 主な機能

- 指定された緯度・経度の位置情報に基づいて天気予報データを提供
- MCP プロトコルを使用してクライアントとサーバー間の通信を実現
- TypeScript による型安全な実装

### システム構成

1. MCP サーバー

   - `@modelcontextprotocol/sdk`を使用して MCP サーバーを実装
   - ES Modules を採用し、モダンな JavaScript 環境で動作
   - Node.js 環境で実行

2. 天気予報 API
   - 外部の天気予報 API と連携して気象データを取得
   - 位置情報（緯度・経度）をパラメータとして利用

### 使用方法

1. サーバーの起動

   ```bash
   node build/index.js
   ```

2. Cursor との連携
   - `.cursor/mcp.json`にサーバー設定を記述
   - Cursor IDE から直接天気予報データにアクセス可能

### 開発環境

- Node.js
- TypeScript
- pnpm（パッケージマネージャー）
- Model Context Protocol SDK

この MCP サーバーは、Cursor IDE のエコシステムの一部として機能し、
開発者が簡単に天気予報データにアクセスできる環境を提供します。
