

````markdown
# VS Code + ChatGPT (Codex) による GAS 開発・運用ガイド

本ドキュメントは、Google Apps Script（GAS）を  
**VS Code 上でローカル管理**し、**ChatGPT（Codex）を活用して安全かつ効率的に修正・運用する**ための手順書です。

---

## 📋 前提条件

- **Node.js**：インストール済み
- **VS Code**：インストール済み  
  - 拡張機能「Codex - OpenAI's coding agent」を導入済み
- **Google アカウント**：対象 GAS プロジェクトの編集権限あり
- **OpenAI アカウント**：ChatGPT Plus / Pro / Team などの有料プラン

---

## 🛠 1. 環境構築（初回のみ）

### 1-1. clasp のインストール

Google Apps Script をローカル管理する公式 CLI ツールです。

```bash
npm install -g @google/clasp
````

### 1-2. Google ログイン

ブラウザが開くので、GAS を所有・編集できる Google アカウントでログインします。

```bash
clasp login
```

### 1-3. プロジェクトの取得（Clone）

GAS の「プロジェクト設定」にある **スクリプト ID** を使用します。

```bash
mkdir my-gas-project
cd my-gas-project
clasp clone <スクリプトID>
```

#### ⚠️ Windows ユーザーの注意

* GAS 側のファイル名に `* ? /` などが含まれていると clone に失敗します
* 事前に **ブラウザ版 GAS エディタでファイル名を修正**してください

---

## 📂 重要：VS Code でフォルダを開く

`clasp clone` は **VS Code とは無関係に** ファイルを生成します。
**clone 後は必ず VS Code でフォルダを開き直してください。**

### 推奨手順（確実）

```powershell
code C:\Users\xnakamura\my-gas-project
```

※ VS Code は「開いているフォルダ」しかエクスプローラーに表示しません。

---

## 🔄 2. 日々の開発フロー

### Step 1. 最新状態を取得（Pull）

ブラウザ側で変更が入っている可能性がある場合は、作業前に必ず実行します。

```bash
clasp pull
```

※ 未保存のローカル変更は上書きされるため注意してください。

---

### Step 2. VS Code で編集 & Codex 活用

`.js` ファイルを編集し、Codex チャットパネルで修正・レビューを行います。

#### 🤖 Codex モデルの使い分け

* **GPT-5.1-Codex-Max（推奨）**
  複雑なロジック解析、GAS 特有の副作用を考慮した修正向け
* **GPT-5.1-Codex-Mini**
  コメント追加、軽微な修正、高速作業向け
* **GPT-5.1-Codex**
  標準用途

---

### Step 3. クラウドへ反映（Push）

ローカルでの修正は **push しない限り GAS に反映されません**。

```bash
clasp push
```

---

## ⚠️ トラブルシューティング

### Q. VS Code にファイルが表示されない

* フォルダを開いていない可能性があります
* `code <project-path>` で開き直してください

### Q. 保存したのに GAS が動かない

* `Ctrl + S` はローカル保存のみです
* 必ず `clasp push` を実行してください

### Q. 認証エラーが出る

* 認証期限切れの可能性があります

```bash
clasp login
```

---

## 📂 .claspignore（除外設定）

以下は Google ドライブへアップロードされません。

```text
.git/
.gitignore
README.md
.vscode/
node_modules/
```

---

## 📝 README 更新時の Git 操作

```powershell
git add README.md
git commit -m "Update README: GAS + VS Code + Codex workflow"
git push
```
