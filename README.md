# Lerna Monorepo Test

[![lerna](https://img.shields.io/badge/maintained%20with-lerna-cc00ff.svg)](https://lerna.js.org/) [![Test](https://github.com/nukopy/lerna-monorepo-test/actions/workflows/test.yml/badge.svg)](https://github.com/nukopy/lerna-monorepo-test/actions/workflows/test.yml)

- Lerna - A tool for managing JavaScript projects with multiple packages.
  - Official Page: https://lerna.js.org/

## Software Versions

- Node.js v14.16.1
- Lerna v4.0.0

## How to Publish

- Requirements
  - Login npm in CLI: `npm login`
  - `packages/` 配下の `package.json` のパッケージ名を `@organization/package-name` にする
  - `package.json` に public access の設定を追加する

```sh
npm run versionup:minor
npm run publish
```

---

## Lerna の背景

以下、[ドキュメントの About](https://lerna.js.org/#about) の日本語訳。

大規模なコードベースを別々のバージョン管理されたパッケージに分割することは、コード共有に非常に便利。しかし、多くのリポジトリをまたいだ変更を加えるのは面倒で、トラッキングも難しく、リポジトリをまたいだテストは非常に複雑になる。

これらの問題を解決するために、いくつかのプロジェクトはコードベースをマルチパッケージレポジトリ（時には monorepo と呼ばれる）に編成する。Babel、React、Angular、Ember、Meteor、Jest などのプロジェクトは、単一のリポジトリ内ですべてのパッケージを開発する（Lerna を使用しているプロジェクトは[ここ](https://lerna.js.org/#users)を参照）。

**Lerna は、マルチパッケージリポジトリの管理に関するワークフローを git と npm で最適化するツールである**。

## Install Lerna

```sh
node i -g lerna
```

## Commands

Lerna の主なコマンドは 2 つ。

- `lerna bootstrap`：リポジトリの依存関係をまとめてリンクする
  - CLI から実行する際は、その前に npm アカウントの作成後、`npm adduser` を実行する必要がある。
- `lerna publish`：更新されたパッケージを公開する

他のコマンドは以下を参照。

- [Lerna - Commands](https://lerna.js.org/#commands)

## Introduction

- [Lerna - README.md](https://github.com/lerna/lerna#readme)

### Lerna によるプロジェクトの初期化

`lerna init` コマンドにより、`lerna.json`、`package.json` という 2 つのファイルと、`packages` ディレクトリが作成される。

```sh
lerna init
```

- `lerna init` 後のディレクトリ構造

```sh
lerna-repo/
  packages/
  package.json
  lerna.json
```

### Lerna によるプロジェクト管理のモード

Lerna では、2 つのモードのいずれかを使って、プロジェクトを管理することができる。

- Fixed/Locked モード（デフォルト）
- Independent モード

#### Fixed/Locked モード

```sh
lerna init
```

Fixed モードの Lerna プロジェクトは、単一のバージョンラインで動作する（**Lerna で管理されるパッケージ群が全て同一のバージョンで管理される**）。バージョンは、プロジェクトのルートにある `lerna.json` ファイルの `version` 下に保持される。`lerna publish` を実行すると、前回のリリース以降にモジュールが更新されていた場合、そのモジュールはリリースする新バージョンに更新される。つまり、パッケージの新バージョンを公開するのは、必要なときだけということになる。

これは、Babel が現在使用しているモードである。全てのパッケージのバージョンを自動的に結びつけたい場合に使用する。**この方法の問題点は、いずれかのパッケージに大きな変更があった場合、全てのパッケージに新しいメジャーバージョンが適用されること**である。

#### Independent モード

```sh
lerna init --independent
```

Independent モードの Lerna プロジェクトでは、メンテナがパッケージのバージョンを互いに独立してインクリメントさせることができる。公開するたびに、変更のあった各パッケージに対して、パッチ、マイナー、メジャー、カスタムのいずれの変更かを指定するプロンプトが表示される。

Independent モードでは、各パッケージのバージョンをより具体的に更新することができ、コンポーネントのグループには意味がある。このモードを [semantic-release](https://github.com/semantic-release/semantic-release) のようなものと組み合わせれば、それほど苦にならないだろう。(すでに [atlassian/lerna-semantic-release](https://github.com/atlassian/lerna-semantic-release) でこの作業が行われている)。

なお、`lerna.json`の`version`キーは、Independent モードでは無視される。

## トラブルシューティング / FAQ

一旦ここ読め。

- Troubleshooting
  - https://github.com/lerna/lerna/blob/main/doc/troubleshooting.md
- Frequently asked questions(FAQ)
  - https://github.com/lerna/lerna/blob/main/FAQ.md

### FAQ まとめ

FAQ で気になったものをいくつか取り上げる。

#### Lerna リポジトリにパッケージを追加したい場合はどうするか？

- 新規パッケージ
  - `packages` 配下にディレクトリを作成し、通常のパッケージを作成するときのようにそのディレクトリで `npm init` を実行すれば良い。
- 既存のパッケージ
  - `lerna import <package: path/to/package | URL>`

#### プロジェクトルートの `package.json` の意味は？

ルートの `package.json` は、少なくとも、CI ビルド時にローカルに lerna をインストールする方法である。ルートの `package.json` のおかげで、CI ビルド時に `npm install` すれば Lerna がインストールでき、その後 `lerna bootstrap` を実行すれば `packages` 配下の依存関係もインストールできる。

また、testing や linting などのタスクをルートから実行するためにも、`package.json` をルートに置くべきである。ルートには、すべての `"hoist"` パッケージを置くことができ、`--hoist` フラグを使用する際のブートストラップを高速化する。

#### `--hoist` とは？

- Lerna Hoisting
  - https://github.com/lerna/lerna/blob/main/doc/hoist.md

`lerna bootstrap --hoist` を実行すると、ルートディレクトリ、各パッケージ配下で共通する devDependencies をルートに引っ張ってくれる。これにより、インストール時間、ディスク容量の節約などにつながる。

## Concepts

Lerna に関する概念の整理。

- [Lerna - Concepts](https://github.com/lerna/lerna#concepts)

- Concepts
  - `lerna.json`
  - Common `devDependencies`
  - [Git Hosted Dependencies](https://github.com/lerna/lerna#git-hosted-dependencies)
  - [README Badge](https://github.com/lerna/lerna#readme-badge)
    - 「このプロジェクトは Lerna で管理している」ということを表すためのバッジ。README の先頭に設置できる。
  - [Wizard](https://github.com/lerna/lerna#wizard)
    - Lerna の CLI の操作でのガイダンスをしてくれる wizard がある。
    - リポジトリ：[webuniverseio/lerna-wizard](https://github.com/webuniverseio/lerna-wizard)

解説が込み入るものだけ以下に整理していく。

### `lerna.json`

[README のこれ](https://github.com/lerna/lerna#lernajson)見れば OK。`lerna.json` の各設定についての説明が書いてある。

### Common `devDependencies`：共通する devDependencies

ほとんどの `devDependencies` は `lerna link convert` で Lerna リポジトリのルートに引き上げることができる。上記のコマンドを実行すると、自動的にファイルをホイストし、相対的な `file:` 指定を使用する。

ホイストにはいくつかの利点がある：

- 全てのパッケージが特定の依存関係の同じバージョンを使用できる
- Snyk のような自動化されたツールを使い、ルートの依存関係を最新に保つことができる
- 依存関係のインストール時間が短縮される
- 必要なストレージが少なくて済む

なお、npm scripts で使用される「バイナリ」実行ファイルを提供する `devDependencies` は、それが使用される各パッケージに直接インストールする必要がある。

例えば、以下のような npm scripts でバイナリ実行ファイルを使用するケースでは、`lerna run nsp`（およびパッケージのディレクトリ内の `npm run nsp`）が正しく動作するためには、ルートの `devDependencies` ではなく、そのパッケージのディレクトリ内で `nsp` という依存関係をインストールする必要がある。

```json
{
  "scripts": {
    "nsp": "nsp"
  },
  "devDependencies": {
    "nsp": "^2.3.3"
  }
}
```

### Git Hosted Dependencies

Lerna では、Git Remote URL でローカルのパッケージ（`packages` 配下のパッケージ）を dependencies として `package.json` に追加できる。以下に例を示す。

```json
// packages/pkg-1/package.json
{
  name: "pkg-1",
  version: "1.0.0",
  dependencies: {
    "pkg-2": "github:example-user/pkg-2#v1.0.0"
  }
}

// packages/pkg-2/package.json
{
  name: "pkg-2",
  version: "1.0.0"
}
```

この書き方により、以下が実現できる。

- `lerna bootstrap` 実行時に、ローカルのパッケージに symlink を貼ることができる
- `lerna publish` 時に、依存するローカルのパッケージのバージョン（上記の `#v1.0.0`）が自動でアップデートされる

## 各コマンドの詳細

手元で一度でも実行してみたらチェック付ける。

- [x] `init`
- [x] `bootstrap`
- [x] `publish`
- [ ] `updated`
- [ ] `clean`
- [ ] `diff`
- [ ] `ls`
- [ ] `run`
- [ ] `exec`

### init

```sh
lerna init
lerna init --independent
```

### bootstrap

```sh
lerna bootstrap
```

`lerna bootstrap` を実行すると、以下の手順で処理が実行される。

1. `packages` 配下の各パッケージのすべての外部依存関係を `npm install` する
2. 互いの依存関係である全ての Lerna packages をシンボリックリンクする
3. 全てのブートストラップされたパッケージを `npm prepublish` する

### publish

現在の Lerna プロジェクトにあるパッケージを公開する。

```sh
lerna publish
```

`lerna publish` を実行すると、以下の手順で処理が実行される。

### updated

pass

### clean

pass

### diff

pass

### ls

pass

### run

pass

### exec

pass
