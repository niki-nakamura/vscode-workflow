# llmo-platform ローカル閲覧・起動手順（Windows / 初心者向け）

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
