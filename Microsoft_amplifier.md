

Microsoft Amplifier 開発スタート手順（0→開始まで / 非エンジニア向け）

これは何？

Microsoft Amplifier は、AIをコマンドライン（CLI）から使えるようにする「モジュール式のAI支援環境」です。まずは CLIをインストールして、初期設定（APIキー登録）を済ませ、VS Codeのターミナルから使える状態にします。  ￼

⸻

重要（Windowsの方へ）

Amplifierは macOS / Linux / Windows Subsystem for Linux (WSL) での動作が主に想定されており、Windowsの“ネイティブ”なシェル（PowerShell/コマンドプロンプト）には既知の問題があるため、基本はWSLを使います。  ￼

⸻

ゴール（このREADMEの到達点）
	•	VS Codeからターミナルを開き
	•	amplifier コマンドが動き
	•	amplifier init で初期設定が完了し
	•	amplifier run "..." で質問・作業指示ができる

⸻

0. 事前に用意するもの（共通）
	•	VS Code（インストール済み）
	•	Git（インストール済み推奨）
	•	AI ProviderのAPIキー（いずれか）
	•	Anthropic / OpenAI / Azure OpenAI / Ollama（ローカル）
※Amplifierは複数Providerに対応し、後から切り替えできます。  ￼

⸻

1. Mac の手順

1-1. VS Codeでターミナルを開く

VS Code → メニュー → Terminal（ターミナル） → New Terminal（新しいターミナル）

⸻

1-2. uv をインストール（最重要）

Amplifierは uv を使って導入する手順が用意されています。  ￼

curl -LsSf https://astral.sh/uv/install.sh | sh

※インストール後、ターミナルを一度閉じて開き直すと確実です。

⸻

1-3. Amplifier をインストール

uv tool install git+https://github.com/microsoft/amplifier

￼

⸻

1-4. 初期設定ウィザードを起動（APIキー登録など）

amplifier init

￼

画面の質問に沿って Provider を選び、APIキー等を入力します。
（例：Anthropic / OpenAI / Azure OpenAI / Ollama）  ￼

⸻

1-5. 動作確認（まずは1行）

amplifier run "Explain async/await in Python"

￼

⸻

2. Windows の手順（WSL推奨ルート）

2-1. WSL2（Ubuntu）を準備する

Windowsでは基本的に WSL（Ubuntu等）内でAmplifierを動かすのが推奨です。  ￼
	•	既にWSLがある方：次へ
	•	ない方：Microsoft公式のWSL導入手順に従い、Ubuntuを入れてください（WSL2推奨）

⸻

2-2. VS Code を WSL と連携する

VS Codeで Remote Development / WSL を使うと、WSL内のフォルダをそのまま開けます。
	•	VS Code → Extensions（拡張機能）で「WSL」を検索して導入
	•	左下のリモートアイコンから “Open Folder in WSL” を選択

⸻

2-3. WSLのターミナルで uv をインストール

（VS CodeのWSLターミナルで実行）

curl -LsSf https://astral.sh/uv/install.sh | sh

￼

⸻

2-4. Amplifier をインストール（WSL内）

uv tool install git+https://github.com/microsoft/amplifier

￼

⸻

2-5. 初期設定（APIキー登録など）

amplifier init

￼

⸻

2-6. 動作確認

amplifier run "Explain async/await in Python"

￼

⸻

3. よく使う基本操作（覚えるのはこれだけでOK）

3-1. チャットモードで起動

amplifier

￼

3-2. 1回だけ実行（単発）

amplifier run "やりたいことを1文で書く"

￼

3-3. Provider（Anthropic/OpenAI等）の切替

amplifier provider use openai
# または
amplifier provider use anthropic --model claude-opus-4-1

￼

3-4. Profile（用途別セット）の切替

開発用途では dev が推奨の既定プロファイルです。  ￼

amplifier profile list
amplifier profile use dev

￼

⸻

4. 開発が捗る「コレクション」を追加（推奨）

Amplifierは追加パッケージ（collection）で、エージェントやテンプレート等を拡張できます。

amplifier collection add git+https://github.com/microsoft/amplifier-collection-toolkit@main
amplifier collection add git+https://github.com/microsoft/amplifier-collection-design-intelligence@main

￼

（入ったか確認）

amplifier collection list


⸻

5. VS Codeでの基本運用（おすすめ流れ）
	1.	対象のリポジトリをVS Codeで開く
	2.	ターミナルを開く（Macは通常ターミナル／WindowsはWSLターミナル）
	3.	まずは現状把握をAIに投げる例：

amplifier run "このリポジトリの目的をREADMEと構成から推測し、改善点を箇条書きで出してください。"


⸻

6. 重要な注意（安全・取り扱い）

Amplifierは研究デモ段階で、強い権限を持つAIツールをローカルで動かす場合があります。機密情報や本番データの取り扱いには十分注意し、必ず人間が最終確認してください。  ￼

⸻

7. つまずいたとき（最小チェック）
	•	Windows：WSLで実行しているか（PowerShell直は避ける）  ￼
	•	uv が入っているか：uv --version
	•	amplifier が入っているか：amplifier --help
	•	初期化済みか：amplifier init を再実行

⸻

付録：最短コマンドまとめ

Mac / Linux / WSL

curl -LsSf https://astral.sh/uv/install.sh | sh
uv tool install git+https://github.com/microsoft/amplifier
amplifier init
amplifier run "Hello"

￼

⸻

必要でしたら、このREADMEの続きとして「開発フロー（例：要件→設計→実装→テスト→PR説明文まで）をAmplifierのprofile/agent前提でテンプレ化」も、同じくリポジトリに残せる形で追記いたします。