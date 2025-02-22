# ICP Hackathon 2025 Wave3：サンプルキャニスター・デプロイ

2/22(土)に竹芝で開催されたハンズオンイベントに参加し、Helloサンプルを作成、Playground環境にデプロイしました。

https://lu.ma/9obsceeh

## チュートリアル

以下のチュートリアルを参考に作業を行いました。

https://zenn.dev/halifax/books/icpbook-rust/

## 1. 事前準備

Windowsの場合、WSL (Windows Subsystem for Linux)上で開発を行う必要があります。

Backend CanisterをRust言語で開発しますので、build-essentialとRustをインストールします。

また、Frontent Canisterを開発するのにNode.jsが必要となります。

### (1) build-essentialのインストール

CコンパイラやmakeなどRustプログラムのビルドに必要となる開発ツールがインストールされます。

```
$ sudo apt install build-essential
```

### (2) Rustのインストール

[Rust](https://www.rust-lang.org/)をインストールし、ビルドターゲットを`wasm32-unknown-unknown`を追加します。

```
$ curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
$ rustup target add wasm32-unknown-unknown
$ rustc --version
rustc 1.85.0 (4d91de4e4 2025-02-17)
```

※`rustc`コマンドが実行できない場合、環境変数`PATH`がまだ通っていないことが原因なので`source ~/.profile`で読み直すか、ターミナルを再起動すること

### (3) Node.jsのインストール

Node.jsのインストールは、nvm (Node Version Manager)経由で行うと便利なので、nvmをインストールします。

```
$ curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash
```

ここでは、最新のNode.jsを入れることにします。

```
$ nvm install node
$ nvm alias default node
$ node --version
v23.8.0
```

※`nvm`コマンドが実行できない場合、環境変数`PATH`がまだ通っていないことが原因なので`source ~/.bashrc`で読み直すか、ターミナルを再起動すること

### (4) IC CDKのインストール

IC SDK (Internet Computer Software Development Kit)をインストールします。

```
$ sh -ci "$(curl -fsSL https://internetcomputer.org/install.sh)"
$ dfx --version
dfx 0.25.0
```

※`dfx`コマンドが実行できない場合、環境変数`PATH`がまだ通っていないことが原因なので`source ~/.profile`で読み直すか、ターミナルを再起動すること

## 2. プロジェクト作成

`dfx new`コマンドでプロジェクトのテンプレートを生成します。

```
$ dfx new hello
```

### (1) Backend language

Rustを選択します。

```
? Select a backend language: ›  
  Motoko
❯ Rust
  Python (Kybra)
  Typescript (Azle)
```

### (2) Frontend framework

チュートリアルと同じく、SvelteKitを選びます。

```
? Select a frontend framework: ›  
❯ SvelteKit
  React
  Vue
  Vanilla JS
  No JS template
  None
```

### (3) Extra features

今回は不要です。

```
? Add extra features (space to select, enter to confirm) ›
⬚ Internet Identity
⬚ Bitcoin (Regtest)
⬚ Frontend tests
```

コマンドで指定したプロジェクト名(`hello`)のディレクトリが作成されて、サンプルソース一式が出力されます。

```
cd hello
```

## 3. Local Canister実行環境

以下のコマンドを実行して、まずはローカルPC上でCanisterを実行する環境を起動します。  
開発中はローカル環境にデプロイしてテストなどします。

```
$ dfx start --clean --background
```

## 4. Localへデプロイ

```
$ dfx deploy
Deploying all canisters.
︙
Deployed canisters.
URLs:
  Frontend canister via browser:
    hello_frontend:
      - http://bd3sg-teaaa-aaaaa-qaaba-cai.localhost:4943/ (Recommended)
      - http://127.0.0.1:4943/?canisterId=bd3sg-teaaa-aaaaa-qaaba-cai (Legacy)
  Backend canister via Candid interface:
    hello_backend: http://127.0.0.1:4943/?canisterId=be2us-64aaa-aaaaa-qaabq-cai&id=bkyz2-fmaaa-aaaaa-qaaaq-cai
```

Local Canister実行環境にBackendが正しくデプロイされているか確認してみます。

```
$ dfx canister call hello_backend greet '("TM")'
("Hello, TM!")
```

## 5. Playgroundへデプロイ

Internet Computerには、Playground環境と呼ばれるCycle不要で20分間だけ使用可能な環境が用意されていますので、こちらにデプロイしてみます。  

`--playground`を指定することで、Playground環境へデプロイできます。

外部にデプロイする際には、Identityを意識する必要があります。
`--identity`を指定することで、開発で使用している`default`以外を指定することができます。  
新しいIdentityは、`dfx identity new <名前>`で作成することができます。

```
$ dfx deploy --identity tm --playground
dfx deploy --identity tm --playground
Please enter the passphrase for your identity: [hidden]
Decryption complete.
Deploying all canisters.
︙
Deployed canisters.
URLs:
  Frontend canister via browser:
    hello_frontend: https://xtnc2-uaaaa-aaaab-qadaq-cai.icp0.io/
  Backend canister via Candid interface:
    hello_backend: https://a4gq6-oaaaa-aaaab-qaa4q-cai.raw.icp0.io/?id=6zfvq-kiaaa-aaaab-qacra-cai
```

## 実行結果

`dfx deploy`コマンドを実行した結果です。

![](/images/vscode.png)

`dfx deploy`コマンドで表示されたURLにWebブラウザでアクセスして動作確認してみました。

![](/images/playground.png)

コマンドラインからBackend Canisterを呼び出すこともできます。

```
$ dfx canister --playground call hello_backend greet '("TM")'
("Hello, TM!")
```

また、Canister IDを直接指定して呼び出すこともできます。

```
$ dfx canister --playground call 6zfvq-kiaaa-aaaab-qacra-cai greet '("TM")'
("Hello, TM!")
```
