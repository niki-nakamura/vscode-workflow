これまでのトラブルシューティング（Git の強制リセット手順や、特定のアプリのみを起動する方法）を踏まえ、**「安全に閲覧する」** 手順と、**「確実に動く状態に戻して起動する」** 手順を明確に分けた、より実践的な README に書き直しました。

そのままコピー＆ペーストして `.md` ファイルとして保存できる形式です。

---

# llmo-platform ローカル閲覧・起動ガイド（Windows）

本ドキュメントは、**GMO 組織の Private Repository「llmo-platform」** を、安全かつ確実に手元の PC で閲覧・実行するための手順書です。

> **✅ 本手順の安全性について**
> * **本番影響なし:** 本番データベースやサーバーには一切接続しません。
> * **課金なし:** API キーが含まれていないため、課金は発生しません。
> * **編集しても安心:** ローカルでファイルを書き換えても、GitHub への書き込み権限（Push）がなければ本番コードは変わりません。
> 
> 

---

## 📚 1. 閲覧だけしたい場合（推奨）

コードの中身を確認したい、構成を把握したい場合は、以下の手順だけで完了です。

### 1-1. 前提ツール

* [Git for Windows](https://git-scm.com/download/win)
* [Visual Studio Code (VS Code)](https://code.visualstudio.com/)

### 1-2. コードの取得（Clone）

PowerShell を開き、以下のコマンドを実行します。

```powershell
# 保存したい場所へ移動（例: ドキュメントフォルダ）
cd "C:\Users\$env:USERNAME\Documents"

# コードをダウンロード
git clone https://github.com/gmo-am/llmo-platform.git

```

### 1-3. VS Code で開く

```powershell
cd llmo-platform
code .

```

VS Code が立ち上がり、フォルダ構成が見えれば **閲覧準備は完了** です。

---

## 🚀 2. アプリを起動したい場合（開発・検証）

ローカルで画面を操作したい場合は、追加のセットアップが必要です。

### 2-1. 前提ツール

* Node.js (v22 推奨)
* pnpm (`npm install -g pnpm`)
* Docker Desktop (起動しておくこと)

### 2-2. 依存関係のインストール

まず、必要なライブラリをインストールします。

```powershell
pnpm install

```

### 2-3. アプリケーションの起動

本プロジェクトは複数のアプリが含まれています。目的に合わせてコマンドを使い分けてください。

**🅰️ ダッシュボード（ユーザー画面）を起動する場合**

```powershell
pnpm --filter "./apps/dashboard" dev

```

→ ブラウザで [http://localhost:3000](https://www.google.com/search?q=http://localhost:3000) にアクセス

**🅱️ 管理画面 (Admin) を起動する場合**

```powershell
pnpm --filter "./apps/admin" dev

```

→ ブラウザで [http://localhost:3000](https://www.google.com/search?q=http://localhost:3000) (ポートが競合する場合はログを確認) にアクセス

---

## 🛠 3. トラブルシューティング（困ったときは）

「エラーが出る」「フォルダがおかしい」「余計なファイルが増えた」など、環境が汚れてしまった場合は、以下の **【完全リセット手順】** を実行してください。

**⚠️ 注意:** 自分で書いた未保存のコードは消えますが、確実に GitHub 上の最新の状態に戻ります。

### 手順：ローカル環境の強制クリーンアップ

VS Code のターミナルで順に実行してください。

```powershell
# 1. 最新情報を取得
git fetch origin

# 2. 強制的にリモートと同じ状態にする（手元の変更を破棄）
git reset --hard origin/main

# 3. ゴミファイル（新規作成された不要ファイル）を削除
git clean -fd

# 4. ライブラリの再同期
pnpm install

```

実行後、`git status` で `nothing to commit, working tree clean` と表示されれば正常です。

---

## 📂 4. フォルダ構成の歩き方

VS Code で見るべき主要なディレクトリです。

| ディレクトリ | 説明 |
| --- | --- |
| **`apps/dashboard`** | ユーザー向けチャット画面のソースコード (Nuxt) |
| **`apps/admin`** | 管理者向け設定画面のソースコード (Nuxt) |
| **`packages/`** | 共通で使用されるロジックや型定義 |
| **`apis/`** | バックエンド API (Python/FastAPI 等が含まれる場合) |

---

### 次のアクション

とりあえず画面を動かしてみたい方は **「2-3. アプリケーションの起動」** のコマンドを試してください。

---

### 修正のポイント

1. **「閲覧（Lv.1）」と「起動（Lv.2）」の完全分離:** 閲覧だけしたい人が、Docker や pnpm のエラーでつまづかないようにしました。
2. **`git reset` 手順の標準化:** 今回解決に役立った「強制リセット＆クリーン」を、トラブルシューティングの第一手として記載しました。
3. **具体的な起動コマンド:** 汎用的なスクリプトではなく、今回成功した `pnpm --filter` を明記し、ダッシュボードと管理画面を区別しました。
4. **`$env:USERNAME` の使用:** Windows ユーザー名部分を自動補完する書き方に変更し、コピペしやすくしました。
