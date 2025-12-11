では、そのまま貼って使える形で README をまとめます。
（llmo-platform 配下に `docs/LOCAL_DEV_SETUP_WINDOWS.md` として置くイメージです）

---

````md
# llmo-platform ローカル閲覧・起動手順（Windows）

本ドキュメントは、**GMO 組織の Private Repository「llmo-platform」** を
自分の PC にクローンし、VS Code でコードを閲覧しつつ、
ローカル環境（Dashboard / Admin / API）を起動するまでの手順をまとめたものです。

> ✅ 前提：**本手順で行う操作は、本番環境には一切影響しません。**  
> ✅ API キーや本番クレデンシャルがないため、OpenAI / Firecrawl / GA4 等の **課金は発生しません**。

---

## 0. 安全性について（本番や課金への影響）

- この手順で扱うのは **自分の PC 上のローカル環境のみ** です。
- `git clone` で取得したコードは **読み取り専用のコピー** であり、
  本番サーバーには何も送信されません。
- OpenAI / Firecrawl / GA4 / Firebase などの **API キーはリポジトリに含まれていません**。
  - `.env` や Service Account JSON など、本番用クレデンシャルがないため、
    外部APIへ認証付きリクエストを送ることはできません。
  - したがって **API 課金は発生しません**。
- 本番に影響を与えるには
  1. GitHub の main/develop へ Push する権限
  2. Cloud Build / Cloud Run のデプロイ権限
  が必要ですが、閲覧専用ユーザーには付与されていません。

安心してローカル環境の起動・コードの閲覧を行って問題ありません。

---

## 1. 前提条件

### 1-1. 権限

- GitHub 組織 `gmo-am` の Private Repo  
  `gmo-am/llmo-platform` に **Read 権限** が付与されていること
  - Fork や Push 権限は不要（なくてもよい）

### 1-2. インストールしておくソフトウェア

Windows PC 上で、次のツールをインストールしておくこと：

1. **Git for Windows**
   - https://git-scm.com/download/win
2. **Visual Studio Code**
   - https://code.visualstudio.com/
3. **Node.js 22 系**
   - https://nodejs.org/  
   （推奨：LTS またはチーム指定バージョン）
4. **pnpm**
   ```powershell
   corepack enable
   corepack prepare pnpm@latest --activate
````

5. **Docker Desktop for Windows**

   * [https://www.docker.com/products/docker-desktop/](https://www.docker.com/products/docker-desktop/)
   * インストール時に **WSL2 backend を有効化** しておく
6. **WSL2（Windows Subsystem for Linux）**

   * 管理者 PowerShell で以下を実行し、再起動：

     ```powershell
     wsl --install
     ```

---

## 2. リポジトリをクローンする（Pull）

### 2-1. PowerShell を開く

* Windows キー → 「PowerShell」と入力 → 起動

### 2-2. 作業ディレクトリへ移動

ここでは `Documents` 配下に配置する例：

```powershell
cd "C:\Users\<YourUserName>\Documents"
```

※ あなたの環境の例：

```powershell
cd "C:\Users\usr0302434\Documents"
```

### 2-3. llmo-platform をクローン（Pull）

```powershell
git clone https://github.com/gmo-am/llmo-platform.git
```

成功すると以下の構造になります：

```
C:\Users\<YourUserName>\Documents\
  └─ llmo-platform\
      ├─ apis\
      ├─ apps\
      ├─ infrastructure\
      ├─ local-env\
      ├─ packages\
      ├─ scripts\
      ├─ README.md
      └─ ...
```

---

## 3. VS Code で llmo-platform を開く

### 3-1. PowerShell から VS Code を起動（推奨）

```powershell
cd "C:\Users\<YourUserName>\Documents\llmo-platform"
code .
```

* 新しい VS Code ウィンドウが開き、
  タイトルバーに
  `llmo-platform — Visual Studio Code`
  と表示されていれば成功。

### 3-2. VS Code 上での確認

* 左のアクティビティバーで **Source Control（Git アイコン）** を押すと
  `apis/` や `apps/` など各ディレクトリが一覧表示される。
* Explorer が空に見える場合でも、Source Control にディレクトリが表示されていれば
  **プロジェクトは問題なく開けています**。

  * （VS Code の表示設定の問題であり、ローカル起動には影響しない）

---

## 4. 依存パッケージのインストール

VS Code のターミナル（もしくは PowerShell）で、プロジェクトルート (`llmo-platform`) にいることを確認：

```powershell
PS C:\Users\<YourUserName>\Documents\llmo-platform>
```

### 4-1. pnpm でワークスペース全体をインストール

```powershell
pnpm i -w
```

* 初回はかなりのパッケージ（2000近く）が入るので数分かかることがあります。
* `Done in XXs` の表示が出れば成功。

---

## 5. local-env の構成

通常、`local-env` ディレクトリには以下のファイルが存在します：

```text
local-env/
  ├─ docker-compose.yml      # ローカル MySQL、CloudSQL proxy 等の定義
  └─ start-all.sh            # 各サービスをまとめて起動するスクリプト
```

もし `local-env` が壊れたり、誤って削除した場合は Git で復元できます：

```powershell
git restore local-env
# または古い Git:
# git checkout HEAD -- local-env
```

---

## 6. Docker / DB / Prisma のセットアップ

### 6-1. Docker Desktop の起動

* Docker Desktop を起動（タスクトレイに 🐳 アイコンが出る）
* PowerShell で動作確認：

```powershell
docker --version
```

バージョンが表示されれば OK。
`docker : 用語 'docker' は…認識されません` と出る場合、
Docker Desktop のインストール or PATH 設定を見直してください。

### 6-2. ローカル MySQL（docker-compose）起動

```powershell
docker compose -f local-env/docker-compose.yml up -d
```

* 初回起動時には MySQL イメージのダウンロードが走ります。
* 成功すると `mysql` コンテナがバックグラウンドで起動。

### 6-3. Prisma Client の生成

```powershell
pnpm db:generate
```

* `Generated Prisma Client …` と表示されれば OK。

---

## 7. 全サービス起動（Dashboard / Admin / API）

WSL2 + bash が使える前提で、以下を実行：

```powershell
bash ./local-env/start-all.sh
```

※ `./local-env/start-all.sh` だけでは Windows の PowerShell からは直接実行できないため、
`bash` 経由で起動します。

### 起動後のアクセス URL

* Dashboard（ユーザー向けUI）
  [http://localhost:3001](http://localhost:3001)
* Admin（管理画面）
  [http://localhost:3000](http://localhost:3000)
* LLMO API（統合API）
  [http://localhost:3002](http://localhost:3002)

---

## 8. よくある質問・トラブルシュート

### Q1. Explorer が空なのに、Source Control にはファイルが見える

* VS Code がフォルダとして開いていないか、
  Explorer が折りたたまれているケースです。
* `code .` でフォルダを開いている & Source Control に `apps/` などが見えていれば、
  **ローカル起動には支障ありません**。
* Explorer 表示は必要に応じて整えればよいので、まずはローカル起動を優先して問題ありません。

---

### Q2. `docker: 用語 'docker' は…認識されません` と出る

* Docker Desktop がインストールされていないか、PATH に追加されていません。

  1. Docker Desktop をインストール
  2. 再起動
  3. `docker --version` で確認
* 解決するまでは `docker compose` は実行できません。

---

### Q3. `open ... local-env/docker-compose.yml: The system cannot find the file specified.` と出る

* `local-env/docker-compose.yml` が存在しません。
* 誤操作で削除した可能性があります。
* 以下で Git から復元してください：

```powershell
git restore local-env
# または:
# git checkout HEAD -- local-env
```

復元後、再度：

```powershell
docker compose -f local-env/docker-compose.yml up -d
```

---

### Q4. `bash ./local-env/start-all.sh` で `execvpe(/bin/bash) failed: No such file or directory` と出る

* WSL2 がインストールされていないか、bash が存在しない状態です。
* 管理者 PowerShell で：

```powershell
wsl --install
```

* 再起動後、再度 `bash ./local-env/start-all.sh` を実行。

---

### Q5. 本番環境や API 課金が心配です

* リポジトリには API キーや本番クレデンシャルは含まれていません。
* `.env` も各自で用意しない限り、外部 API に認証付き通信はできません。
* 認証できないリクエストは各プロバイダ側で拒否され、**課金対象になりません**。
* GitHub に対しても Read のみで、Push / デプロイ権限はない前提のため、
  **本番環境に影響を与えることはできません**。

---

## 9. 最小運用フロー（おさらい）

1. GitHub から `llmo-platform` を clone
2. `code .` で VS Code を開く
3. `pnpm i -w` で依存インストール
4. Docker Desktop & WSL2 をセットアップ
5. `docker compose -f local-env/docker-compose.yml up -d`
6. `pnpm db:generate`
7. `bash ./local-env/start-all.sh`
8. ブラウザで [http://localhost:3001（Dashboard）を確認](http://localhost:3001（Dashboard）を確認)

以上で、**エンジニアでも本番に影響を与えず、安全に llmo-platform をローカルで起動・検証できる状態**になります。

```

::contentReference[oaicite:0]{index=0}
```
