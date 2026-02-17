

# VS CodeでClaude Codeを起動し、MCP（BigQuery）接続まで完了させる手順

## 1. VS Codeの統合ターミナルを開く

1. VS Codeを開きます。
2. 上部メニューから **[表示] → [ターミナル]** を選び、統合ターミナルを表示します。  
   もしくはショートカットで開きます。  
   - Windows/Linux: `Ctrl + @`  
   - macOS: `Control + @`（環境によっては `Cmd + J` などの設定の場合もあります）

以降のコマンドは、この統合ターミナルで実行します。

---

## 2. Claude Codeをインストールする（初回のみ）

### 2-1. まず通常のインストールを試す
ターミナルで実行します。

```bash
npm install -g @anthropic-ai/claude-code
````

### 2-2. 権限エラー（EACCES）が出た場合（macOSでよくある）

エラー例：`npm error code EACCES` / `/usr/local/lib/node_modules/... permission denied`

この場合は、次のいずれかで解決します。

#### 方法A：sudoでインストール（手早い）

```bash
sudo npm install -g @anthropic-ai/claude-code
```

* パスワード入力が求められたら、OSログインのパスワードを入力します（入力中は表示されないことがあります）。
* 成功すると `changed ... packages` のように表示されます。

#### 方法B：npmのグローバル先をユーザー配下に変更（継続運用向き）

```bash
mkdir -p ~/.npm-global
npm config set prefix '~/.npm-global'
```

シェルがzshの場合（macOSの標準が多い）：

```bash
echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.zshrc
source ~/.zshrc
```

bashの場合：

```bash
echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.bash_profile
source ~/.bash_profile
```

その後、sudoなしでインストールします。

```bash
npm install -g @anthropic-ai/claude-code
```

---

## 3. Claude CodeをVS Codeのターミナルから起動する

### 3-1. ワークスペース（プロジェクト）を開いた状態で起動

* VS Codeで対象プロジェクト（例：`BQ_PoC`）を開いている状態で、統合ターミナルを開きます。
* そのまま起動します。

```bash
claude-code
```

### 3-2. 現在のフォルダを明示して起動（任意）

```bash
claude-code .
```

#### 補足：ファイル（例：BQ_POC）を開いたままでよいか

* エディタでどのファイルを開いていても問題ありません。
* Claude Codeは「ターミナルのセッション」として動作するため、ファイル表示とは独立です。
* ターミナルを閉じたりセッションを終了すると、Claude Codeも終了します。

---

## 4. 初回起動時のUI操作（テーマ・ログイン・ターミナル設定）

起動すると、ターミナル上に選択画面が出ます。基本は「矢印キーで選択 → Enterで決定」です。

### 4-1. テーマ選択

例：
`Choose the text style...`
`1. Dark mode` など

* 自分のターミナルに合わせて選び、Enter。

### 4-2. ログイン方法の選択（APIキー不要のログイン）

例：
`Select login method:`
`1. Claude account with subscription`
`2. Anthropic Console account`

* **Claudeのサブスク（Pro/Team等）でログインする場合は「1」** を選び、Enter。
* ブラウザが開いてログインが進むので、画面に従って認証します。
* 認証完了後、ターミナルに戻って続行します。

### 4-3. ターミナル推奨設定（Shift+Enterで改行など）

例：
`Use Claude Code's terminal setup?`
`1. Yes, use recommended settings`

* 基本は **1（推奨設定）** を選びます。
* 後から変更する場合は、Claude Code内コマンドで `/terminal-setup` を使います。

---

## 5. Claude Codeの終了・再起動方法

### 5-1. 終了（セッションを閉じる）

Claude Codeが動いているターミナルで、次のいずれかを行います。

* `Ctrl + C`（プロセス停止）
* もしくは Claude Code 内で `/exit`

### 5-2. 再起動

同じターミナルで再度実行します。

```bash
claude-code
```

---

## 6. MCP（BigQuery）接続の確認・管理

### 6-1. MCP一覧画面を開く

Claude Code内で次を入力します。

```text
/mcp
```

すると、MCPサーバーの一覧が表示されます（例：`bigquery ✔ connected`）。

### 6-2. 表示される「MCP Config locations」の意味

`/mcp` 画面に以下のような表示が出ます。

* User config（全プロジェクト共通）：`~/.claude.json`
* Project config（プロジェクト共有）：`<project>/.mcp.json`（無ければ「file does not exist」と出ることがあります）
* Local config（このプロジェクトで自分だけ）：`~/.claude.json [project: ...]`

状況により、接続設定が「ユーザー全体」または「プロジェクト単位」どちらに保存されているかが分かります。

### 6-3. bigqueryに「connected」と出ていれば成功

* `✔ connected` が出ていれば、再起動なしで利用可能です。

---

## 7. BigQuery MCPの「Tools（3 tools）」表示の意味

`/mcp` で bigquery を選択すると、次のような表示が出ることがあります。

* `execute-query`
* `list-tables`
* `describe-table`

これは「BigQuery MCPサーバーが提供する機能一覧」です。

* `execute-query`：SQLを実行する
* `list-tables`：データセット内のテーブル一覧を取得する
* `describe-table`：テーブルのスキーマ（カラム等）を確認する

通常は、この画面で何か操作する必要はありません。会話で依頼すれば、Claude Codeが適切に使い分けます。

---

## 8. 出力が大きすぎて止まる（トークン上限）ときの対処

### 8-1. 典型的な表示

* `[OUTPUT TRUNCATED - exceeded ... token limit]`
* `Context low · Run /compact to compact & continue`

これは「返ってきた結果が大きすぎて、会話の処理上限に達した」状態です。

### 8-2. まず試す：/compact

Claude Code内で次を実行します。

```text
/compact
```

### 8-3. /compact が失敗する場合

例：
`Error during compaction: Conversation too long. Press esc twice to go up a few messages and try again.`

この場合は、次の順で対応します。

1. 指示通り **Escを2回** 押して、少し前のメッセージ位置に戻る
2. そこで再度 `/compact`

それでも厳しい場合は、確実に整理できる方法として「再起動」を使います。

* `Ctrl + C` で終了
* `claude-code` で再起動

---

## 9. BigQueryの大量結果でトークンを消費しない運用（重要）

### 9-1. 基本方針

* 画面に大量行を表示させない
* SQLは「必要最低限の列」「絞り込み」「期間制限」「LIMIT」を徹底する
* 結果はファイルに保存し、Claude Codeにはサマリーだけ返す

### 9-2. SQL側でまず小さく確認する（例）

#### スキーマ確認（表示は軽い）

* 「このテーブルのスキーマを教えて」

#### 件数確認（結果は1行）

```sql
SELECT COUNT(*) AS cnt
FROM `poc_takaba.boy-co-jp_kojin_kouza_access_user_logs_20250701-1130`;
```

#### サンプル確認（必ずLIMIT）

```sql
SELECT *
FROM `poc_takaba.boy-co-jp_kojin_kouza_access_user_logs_20250701-1130`
LIMIT 10;
```

### 9-3. 要件が「ユーザー別の時系列・大量ログ」の場合の現実解

* 期間は「前後10日」より「前後5日」などに縮める
* ユーザー（client_id）ごとに分割する
* さらに、1ユーザーあたりレコード数が多い場合は「5000件ごとに分割」してファイル保存する

この場合、Claude Codeへの依頼文は次のように組み立てます。

* 対象の dataset / table を明記
* 対象 client_id を列挙
* 対象URL（例：`boy.co.jp/kojin/kouza/index.html`）へのアクセス時刻を起点に、前後N日で抽出
* 取得する項目（時刻、アクセス先ドメイン等）を明確化
* **画面には出さず、client_id単位でJSONファイル出力**
* **1ファイルあたり5000件で分割保存**
* 実行後は、ファイルごとのレコード数・期間などのサマリーのみ返す

---

## 10. VS Codeのタスクにして起動を楽にする（任意）

1. VS Codeで `Ctrl + Shift + P`（macOSは `Cmd + Shift + P`）を開く
2. `Tasks: Configure Task` を選択
3. `.vscode/tasks.json` にタスクを追加

例：

```json
{
  "version": "2.0.0",
  "tasks": [
    {
      "label": "Launch Claude Code",
      "type": "shell",
      "command": "claude-code",
      "problemMatcher": []
    }
  ]
}
```

以後は、

* `Ctrl + Shift + P`（macOSは `Cmd + Shift + P`）
* `Tasks: Run Task`
* `Launch Claude Code`

で起動できます。

---

## 付録：よくある判断ポイント（迷いやすい箇所）

* 「ファイルを閉じる必要があるか」：不要（エディタとターミナルは独立）
* 「ターミナルを新しくするべきか」：不要（同じターミナルで起動してよい）
* 「ログインはAPIキー必須か」：不要（サブスクログインを選べる）
* 「MCPは接続できているか」：`/mcp` で `✔ connected` を確認
* 「止まった」：まず `/compact`、無理なら `Ctrl+C → claude-code` で再起動
* 「大量結果」：画面出力しない設計（ファイル保存＋サマリー返却）に切り替える
