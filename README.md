
# VS Code + ChatGPT (Codex) による GAS 開発・運用ガイド

本ドキュメントは、Google Apps Script（GAS）を VS Code 上でローカル管理し、ChatGPT（Codex）を活用して安全かつ効率的に修正・運用するための手順書です。
基本方針は「クラウド（Apps Script）とローカル（VS Code）を clasp で同期し、編集は必ずローカルで行い、反映は必ず push で行う」です。

## 0. 用語と前提の理解

* clasp

  * Google 公式の Apps Script CLI。クラウドの GAS とローカルフォルダを同期する。
* clone

  * 初回のみ実行する「プロジェクトの紐づけ（.clasp.json の作成）＋クラウドからファイル取得」。
* pull

  * クラウド → ローカルへ最新を取得する。
* push

  * ローカル → クラウドへ変更を反映する。
* 「Project settings not found.」

  * そのフォルダに `.clasp.json` がない。同期対象の設定が存在しないため pull/push はできない。
* 重要：URL の projectId と scriptId は別物になり得る

  * Apps Script の画面URLに出るID（projects/…）と、プロジェクト設定にある「スクリプトID（scriptId）」は一致しない場合がある。
  * 原則、clasp は「スクリプトID（scriptId）」を使うのが安全。

## 1. 前提条件

* Node.js：インストール済み
* VS Code：インストール済み
* VS Code 拡張機能：Codex - OpenAI's coding agent（任意だが推奨）
* Google アカウント：対象 GAS プロジェクトの編集権限あり
* OpenAI アカウント：ChatGPT Plus / Pro / Team 等（Codex利用想定）

## 2. 初回セットアップ（1プロジェクトにつき1回）

### 2-1. clasp のインストール

目的：clasp コマンドを使えるようにする。

```bash
npm install -g @google/clasp
```

確認：

```bash
clasp -v
# または
clasp --version
```

### 2-2. Google ログイン

目的：clasp が Google アカウントへアクセスできるように認証する（編集権限のあるアカウントでログインすること）。

```bash
clasp login
```

注意：

* 「You seem to already be logged in.」は異常ではない。
* 複数Googleアカウントを使っていると、意図しないアカウントで認証されていることがある。
* 認証を切り替えたい場合：

```bash
clasp logout
clasp login
```

### 2-3. Apps Script API を有効化

目的：clasp が Apps Script を操作するために必要。

* Google アカウントの Apps Script 設定で Apps Script API を有効化する。
* これが無効だと権限系のエラーになることがある。

### 2-4. スクリプトID（scriptId）を取得する

目的：clasp clone の対象を指定するため。

* Apps Script エディタ → プロジェクトの設定（歯車） → 「ID」欄の「スクリプトID」をコピーする。
* URL（projects/…）のIDをそのまま使わない（一致することもあるが、混乱の元になるため）。

### 2-5. Windows のファイル名制約を先に潰す（重要）

目的：clone / pull が Windows のファイル名禁止文字で失敗するのを防ぐ。

Windows で使えない文字：
`< > : " / \ | ? *`

Apps Script 側のファイル名（左のファイル一覧）に上記が含まれると、以下のようなエラーで失敗することがある。

* ENOENT: no such file or directory, open '...* 〜.js'

対処：

* Apps Script エディタで対象ファイル名から禁止文字を除去し、保存する。
* 例：先頭に `*` があるなら削除する。

### 2-6. 空フォルダを作成して clone（推奨）

目的：そのフォルダを「このGASプロジェクトの作業場所」に固定し、`.clasp.json` を生成する。

```powershell
mkdir C:\Users\xnakamura\gas-project-management
cd C:\Users\xnakamura\gas-project-management
clasp clone <スクリプトID>
```

成功時の目安：

* `.clasp.json` が作成される
* `appsscript.json` と `.js` / `.html` などが取得される
* 例：Cloned X files. が表示される

確認：

```powershell
cd C:\Users\xnakamura\gas-project-management
dir -Force
type .clasp.json
```

`.clasp.json` が無い場合は clone が完了していない。pull/push はできない。

## 3. 開発の基本ルール（毎回ここを守る）

### 3-1. clasp の「作業フォルダ」と VS Code の「開いているフォルダ」を必ず一致させる

目的：編集している場所と、push している場所の不一致事故を防ぐ。

clasp は PowerShell の「現在のディレクトリ（pwd）」を基準に動く。
VS Code は「いま開いているフォルダ」しか編集対象にならない。
この2つがズレると、編集したつもりでも push されず「反映されない」状態になる。

毎回の開始手順（推奨）：

```powershell
cd C:\Users\xnakamura\gas-project-management
code .
```

確認ポイント：

* PowerShell が `PS C:\Users\xnakamura\gas-project-management>` になっている
* `dir -Force` で `.clasp.json` が見える
* VS Code のエクスプローラー最上部のフォルダ名が `gas-project-management` になっている

### 3-2. 日々の運用フロー（Pull → 編集 → Push）

目的：競合と反映漏れを防ぐ。

1. 作業前に pull（クラウドが正）

* クラウド側で誰かが編集している可能性があるため、作業開始時に pull を行う。

```powershell
clasp pull
```

意味：

* クラウドの最新状態でローカルを更新する。
* ローカル未保存の変更があると上書きされうるため注意。

2. VS Code で編集（保存が必要）

* `.js` / `.html` / `appsscript.json` を編集する。
* 必ず保存（Ctrl+S）する。保存していない変更は push されない。

3. push（ローカル→クラウド反映）

```powershell
clasp push
```

意味：

* ローカルの差分をクラウドへ反映する。
* 差分がなければ push は行われず、以下のように出ることがある：

  * Script is already up to date.

4. ブラウザの Apps Script エディタで確認

* 反映が見えない場合は強制リロード（Ctrl+F5）を行う。

## 4. Codex（ChatGPT）活用の基本

目的：安全に修正するための運用。

* 依頼前に必ず pull 済みの最新コードを VS Code 上で開く。
* Codex には「何をどう変えるか」「触ってはいけない範囲」「実行時間制限」「トリガー運用」など制約を明示する。
* 変更は小さく分割し、push → 動作確認 → 次の変更、の順で進める。

## 5. 反映されないときの最短切り分け

### 5-1. まず「差分があるか」を確認する

```powershell
clasp status
```

* Tracked files は clasp が同期管理しているファイル一覧。
* `.clasp.json` が Untracked に出るのは正常（クラウドへ push する対象ではない）。

差分が無い場合：

* 編集していない
* 保存していない
* 別フォルダを編集している
  のいずれか。

### 5-2. 最小の確認（1行テスト）で経路を確定する

目的：push 経路が正常かを確実に判断する。

* `.js` の先頭コメントに 1 行追加して保存する。
* `clasp push` を実行する。
* GAS エディタを強制リロードして、その1行が見えるか確認する。

これで反映できれば「同期経路は正常」。以降は修正内容の問題に切り替えて考える。

## 6. よくあるエラーと対処

### 6-1. Project settings not found.

原因：

* `.clasp.json` が存在しないフォルダで clasp を実行している。

対処：

* `pwd` と `dir -Force` で `.clasp.json` の有無を確認する。
* `.clasp.json` が無ければ、そのフォルダは未紐づけ。
* 空フォルダで `clasp clone <スクリプトID>` を実行して紐づける。

### 6-2. ENOENT / ファイルが作れない（Windows）

原因：

* Apps Script 側のファイル名に Windows 禁止文字（`* ? : / \` など）が含まれている。

対処：

* GAS エディタでファイル名から禁止文字を削除する。
* 修正後に clone/pull をやり直す。

### 6-3. Script is already up to date.

意味：

* ローカルとクラウドに差分がないため、push しても送るものがない。

対処：

* VS Code で編集して保存したか確認する（タブの未保存表示を消す）。
* VS Code が clasp の作業フォルダを開いているか確認する（code . を推奨）。
* `clasp status` で差分の有無を確認する。

### 6-4. 認証は通るが権限エラーが出る

原因：

* ログインしている Google アカウントに対象スクリプトの権限がない。
* Apps Script API が無効。
* 複数アカウント混在で別アカウント認証になっている。

対処：

* `clasp logout` → `clasp login` で正しいアカウントに揃える。
* 対象プロジェクトの共有設定で編集権限を確認する。
* Apps Script API を有効化する。

### 6-5. push したのに画面で見えない

原因：

* GAS エディタ側の表示キャッシュ、または別プロジェクトを見ている。

対処：

* GAS エディタを Ctrl+F5 で強制リロードする。
* `.clasp.json` の scriptId が意図したプロジェクトか確認する。
* Apps Script のプロジェクト設定でスクリプトIDを再確認する。

### 6-6. VS Code で編集しているのに push されない

原因：

* VS Code が別フォルダを開いている。
* 保存していない。

対処：

* PowerShell で `cd <作業フォルダ>` の後に `code .` を必ず実行する。
* 保存（Ctrl+S）してから `clasp status` → `clasp push`。

### 6-7. `--verbose` が使えない

原因：

* clasp のバージョンや実装差で `--verbose` オプションが存在しない。

対処：

* `clasp --version` でバージョン確認。
* エラー解析は通常の出力ログで十分な場合が多い。

## 7. 安全運用（推奨）

* 作業開始時：必ず pull
* 作業単位：小さく変更し、都度 push と動作確認
* 変更前：1行テストで push 経路が生きているか確認してもよい
* 重要修正：ローカルでバックアップ（別フォルダへコピー）を取る
* 複数人運用：作業前 pull、作業後 push、衝突時は調整してから push

## 8. .claspignore（任意）

目的：クラウドへ不要なファイルを送らない。

例：

```text
.git/
.gitignore
README.md
.vscode/
node_modules/
```

## 9. Git 管理（任意）

目的：変更履歴を残し、差分確認とロールバックを可能にする。

```powershell
git add .
git commit -m "Update GAS scripts"
git push
```

---

必要であれば、この README に「このプロジェクトでの定型コマンド（フォルダパス固定版）」を追記した版（あなたの環境パスに完全固定）も作れます。
