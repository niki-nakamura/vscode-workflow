ご提示いただいた最新情報（Codexのモデル選び、クラウド側からの同期方法など）をすべて反映させた、**完全版の README.md** を作成しました。

これをコピーして、VS Codeの `README.md` に上書き保存してください。

-----

````markdown
# VS Code + ChatGPT (Codex) による GAS 開発・運用ガイド

Google Apps Script (GAS) を VS Code 上でローカル管理し、OpenAI の最新コーディングAI (Codex) を活用して効率的に修正・運用するための手順書です。

## 📋 前提条件

* **Node.js**: インストール済みであること
* **VS Code**: 拡張機能「Codex - OpenAI's coding agent」を導入済みであること
* **Google アカウント**: GAS 編集権限があること
* **OpenAI アカウント**: ChatGPT Plus/Pro/Team などの有料プラン（API従量課金ではなく、プラン内で利用可能）

---

## 🛠 1. 環境構築 (初回のみ)

### 1-1. clasp のインストール
GAS をローカルで管理するための Google 公式ツールをインストールします。

```bash
npm install -g @google/clasp
````

### 1-2. Google ログイン

ブラウザ認証を行い、PC と Google アカウントを紐付けます。

```bash
clasp login
```

### 1-3. プロジェクトの取得 (Clone)

GAS の「プロジェクト設定」にある **スクリプトID** をコピーし、以下のコマンドでダウンロードします。

```bash
# フォルダ作成
mkdir my-gas-project
cd my-gas-project

# コードのダウンロード
clasp clone <スクリプトID>
```

> **⚠️ Windows ユーザーの注意**: GAS 上のファイル名に `*` `?` `/` などの記号が含まれているとダウンロードに失敗します。事前にブラウザ上の GAS エディタでファイル名を変更してください。

-----

## 🔄 2. 日々の開発フロー

### Step 1: 最新状態の同期 (Pull)

作業を始める前や、ブラウザ側で誰かが修正を行った場合は、必ず手元のコードを更新します。
**※注意: 手元で保存していない変更がある状態で実行すると、上書きされて消える可能性があります。**

```bash
clasp pull
```

### Step 2: VS Code で編集 & AI 活用

ファイルを開き、修正を行います。右側の Codex チャットパネルを活用してください。

#### 🤖 AIモデルの選び方

チャット欄のモデル選択プルダウンから用途に合わせて選択します。

  * **GPT-5.1-Codex-Max**: **推奨**。複雑なロジック解析、バグ修正、大規模なリファクタリングに最適。
  * **GPT-5.1-Codex-Mini**: 高速。コメント追加や単純な構文修正向け。
  * **GPT-5.1-Codex**: 標準モデル。

### Step 3: クラウドへの反映 (Push)

修正が終わったら、以下のコマンドで Google のサーバーへアップロードします。
**これを実行しないと、実際の GAS は更新されません。**

```bash
clasp push
```

-----

## ⚠️ トラブルシューティング

### Q. 「拡張機能が見つからない」と言われた

`clasp` は VS Code の拡張機能ではなく、ターミナルで動くツールです。コマンドライン（ターミナル）から操作してください。

### Q. アップロード (Push) を忘れて動かない

VS Code で「保存 (Ctrl+S)」しただけでは GAS に反映されません。必ず `clasp push` を行ってください。

### Q. 認証エラーが出る

長期間使用していない場合、認証が切れることがあります。その際は再度 `clasp login` を実行してください。

-----

## 📂 除外設定 (.claspignore)

`.git` フォルダなどが Google ドライブにアップロードされないよう、プロジェクトルートに `.claspignore` ファイルを作成し、以下を記述してあります。

```text
.git/
.gitignore
README.md
.vscode/
node_modules/
```

````

---

### 作業手順
1.  VS Codeで `README.md` を開きます。
2.  中身をすべて消して、上記のコードを貼り付けます。
3.  保存 (`Ctrl + S`) します。
4.  ターミナルで以下を実行して GitHub に更新を反映させます。

```powershell
git add README.md
git commit -m "Update README with latest Codex and sync instructions"
git push
````

これでドキュメントも最新の状態になります！
