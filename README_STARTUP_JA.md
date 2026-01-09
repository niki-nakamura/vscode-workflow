# Amplifier 起動手順（Windows PowerShell ）

この手順は、Windows PowerShell 上で Amplifier をインストールして起動するまでの流れを
まとめたものです。次回以降はこのファイルを見れば再現できます。

> 注意: 公式 README では「Windows ネイティブは既知の問題あり」とされています。
> もし挙動が不安定な場合は WSL での利用を検討してください。

## 1. 事前準備

- インターネット接続が必要です（初回インストール時）。
- 利用する AI プロバイダの API キーが必要です（OpenAI / Anthropic / Azure OpenAI 等）。
  - Ollama を使う場合はローカルで Ollama を起動しておきます。

## 2. uv をインストール

PowerShell で以下を実行します。

```powershell
powershell -c "irm https://astral.sh/uv/install.ps1 | iex"
```

インストール後、`uv` を使えるようにするため一時的に PATH を通します。

```powershell
$env:Path = "${env:USERPROFILE}\.local\bin;$env:Path"
uv --version
```

`uv --version` が表示されれば OK です。

## 3. Amplifier をインストール

```powershell
uv tool install git+https://github.com/microsoft/amplifier
```

## 4. 初回セットアップ（amplifier init）

```powershell
uv tool run amplifier init
```

対話式のウィザードが表示されるので、以下を順に設定します。

- Provider（Anthropic / OpenAI / Azure OpenAI / Ollama など）
- API キーやエンドポイント（必要な場合）
- モデル名

## 5. Amplifier を起動（チャットモード）

```powershell
uv tool run amplifier
```

単発コマンドで使いたい場合は以下の形式です。

```powershell
uv tool run amplifier run "Explain async/await in Python"
```

## 6. SEOコンテンツ自動生成システムの最初のプロンプト例

起動後、以下のような設計指示から始めるとスムーズです。

```text
Use zen-architect to design a production SEO content automation system.
Constraints:
- Target market/language: 日本/日本語
- Content types: ブログ/LP/FAQ
- Publishing: WordPress or Headless CMS
- Human review: 必須
Output:
- System architecture (components & data flow)
- Risks/mitigations (quality, compliance, hallucination)
- MVP scope & milestones
```

## 7. よくあるトラブルと対処

### `uv` が見つからない

以下を実行して PATH を通してください。

```powershell
$env:Path = "${env:USERPROFILE}\.local\bin;$env:Path"
uv --version
```

### `amplifier` が見つからない

`uv tool run` 経由で実行できます。

```powershell
uv tool run amplifier
```

### Windows ネイティブで不安定

WSL で実行すると安定する場合があります。  
どうしても問題が出る場合は WSL を検討してください。
