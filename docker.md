
# llmo-platform ローカル閲覧・起動手順（Windows）

本ドキュメントは、
**GMO 組織の Private Repository「llmo-platform」** を
**自分の PC にクローンしてコードを参照できる状態にすること**を第一目的とし、
必要に応じて **ローカル起動まで進められる** 手順をまとめたものです。

> ✅ 本手順は **ローカル閲覧・検証専用** です
> ✅ **本番環境・課金・データには一切影響しません**
> ✅ まずは *clone → VS Code で閲覧* だけで完結して問題ありません

---

## 0. 安全性について（重要）

* `git clone` で取得するのは **コードのコピー** です
  → 本番サーバーやクラウドへは **一切アクセスしません**
* リポジトリには以下は **含まれていません**

  * OpenAI / Firecrawl / GA4 / Firebase 等の API キー
  * 本番用 `.env`
  * Service Account JSON
* 認証情報が無いため

  * 外部 API への **認証付き通信は不可**
  * **課金は発生しません**
* GitHub 権限が **Read のみ** の場合

  * Push / Deploy / 本番反映は **不可能**

👉 **安心して閲覧・ローカル実行して問題ありません**

---

## 1. 前提条件

 1-1. 権限

* GitHub 組織 `gmo-am`
* Private Repository
  **`gmo-am/llmo-platform` に Read 権限**

※ Fork / Push 権限は不要

---

 1-2. 事前インストール（最低限）

> 🔰 **閲覧だけなら ①② だけで OK**

1. **Git for Windows**
   [https://git-scm.com/download/win](https://git-scm.com/download/win)

2. **Visual Studio Code**
   [https://code.visualstudio.com/](https://code.visualstudio.com/)

（以下は *起動したい場合のみ*）
3. Node.js 22 系
4. pnpm
5. Docker Desktop（WSL2 有効）
6. WSL2

---

## 2. リポジトリをクローンする

 2-1. PowerShell を起動

Windows キー → `PowerShell`

---

 2-2. 作業ディレクトリへ移動

例（Documents 配下）：

```powershell
cd "C:\Users\<YourUserName>\Documents"
```

あなたの環境例：

```powershell
cd "C:\Users\xnakamura\Documents"
```

---

 2-3. クローン

```powershell
git clone https://github.com/gmo-am/llmo-platform.git
```

成功すると以下の構造になります：

```text
llmo-platform/
 ├─ apis/
 ├─ apps/
 ├─ packages/
 ├─ local-env/
 ├─ infrastructure/
 ├─ scripts/
 └─ README.md
```

---

## 3. すでに clone 済みの場合（よくあるエラー）

 ❌ エラー例

```text
fatal: destination path 'llmo-platform' already exists and is not an empty directory.
```

 ✔ 対処方法

すでに clone 済みです。**再 clone は不要**です。

```powershell
cd llmo-platform
```

中身を確認：

```powershell
git status
```

```text
On branch main
nothing to commit, working tree clean
```

→ 正常です 👍

---

## 4. VS Code でリポジトリを開く（閲覧目的）

 4-1. PowerShell から起動（推奨）

```powershell
cd llmo-platform
code .
```

 4-2. 確認ポイント

* VS Code タイトルに
  **`llmo-platform — Visual Studio Code`**
* 左ペイン（Explorer）に

  * `apps/`
  * `apis/`
  * `packages/`

が見えれば **閲覧準備は完了**です。

👉 **ここまでで目的達成です**
（以降は *任意*）

---

## 5. 依存パッケージのインストール（起動したい場合のみ）

```powershell
pnpm i -w
```

* 初回は数分かかります
* `Done in XXs` が出れば成功

---

## 6. Docker / DB（起動したい場合のみ）

 6-1. Docker 起動確認

```powershell
docker --version
```

 6-2. DB 起動

```powershell
docker compose -f local-env/docker-compose.yml up -d
```

 6-3. Prisma Client 生成

```powershell
pnpm db:generate
```

---

## 7. 全サービス起動（任意）

```powershell
bash ./local-env/start-all.sh
```

 アクセス先

* Dashboard
  [http://localhost:3001](http://localhost:3001)
* Admin
  [http://localhost:3000](http://localhost:3000)
* API
  [http://localhost:3002](http://localhost:3002)

---

## 8. よくあるトラブル

 Q. Explorer が空だが Source Control にファイルが見える

* 表示設定の問題です
* **起動・閲覧には影響ありません**

---

 Q. `docker` が認識されない

* Docker Desktop 未起動 or 未インストール
* 再起動後に再確認

---

 Q. `local-env/docker-compose.yml` が無い

```powershell
git restore local-env
```

---

 Q. 本番や課金が不安

* API キーなし → 認証不可 → 課金不可
* Read 権限のみ → 本番影響不可

---

## 9. 最小フロー（閲覧だけ）

```text
1. git clone（または既存 clone を使う）
2. cd llmo-platform
3. code .
```

これだけで **安全に repository を参照できます**。

---

 補足

* 「とりあえず中身を見たい」
* 「構成を把握したい」
* 「仕様レビューしたい」

👉 **この README の 4 章までで十分です**

---

必要であれば次に

* **リポジトリ構成の読み方（apps / apis / packages）**
* **どこから読めば全体像が分かるか**
* **llmo-platform の論理アーキテクチャ図**

まで落としますが、いかがいたしましょうか。
