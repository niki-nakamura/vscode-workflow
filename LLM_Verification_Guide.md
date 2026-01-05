
# LLM API 疎通確認・検証手順 (Python / OpenRouter)

VS Codeのターミナルのみを使用して、OpenAI互換API (OpenRouter) 経由でLLMの動作を検証するためのミニマム手順書です。

## 1. 準備 (初回のみ)

VS Codeのターミナルを開き、必要なライブラリをインストールします。

```bash
pip install openai

```

## 2. 検証用コード

以下のコードをコピーし、`test_prompt.py` という名前で新規作成して貼り付けてください。
**注意:** `API_KEY` の部分は自身のキーに書き換えてください。

```python
import os
import json
from openai import OpenAI

# ==========================================
# 設定エリア (ここを書き換えて使う)
# ==========================================
# 1. APIキー (OpenRouterで取得したもの)
API_KEY = "sk-or-xxxxxxxxxxxxxxxxxxxxxxxx" 

# 2. 使用モデル (例: gpt-4o-mini, google/gemini-2.0-flash-exp:free 等)
MODEL_NAME = "openai/gpt-4o-mini"

# 3. テスト入力データ
COUNTRY = "日本"
SERVICE_NAME = "GMO AI最適化ブースト"
KEYWORDS = "AIO, SEO, 生成AI検索"
# ==========================================

def run_test():
    print(f"--- モデル: {MODEL_NAME} に問い合わせ中... ---")

    client = OpenAI(
        base_url="[https://openrouter.ai/api/v1](https://openrouter.ai/api/v1)",
        api_key=API_KEY,
    )

    # 依頼内容（プロンプト）
    prompt_content = f"""
    国: {COUNTRY}
    サービス名: {SERVICE_NAME}
    キーワード: {KEYWORDS}
    
    上記に関連する「調査用プロンプト」を3つ、JSON形式で生成してください。
    各要素は {{ "title": "...", "query": "..." }} としてください。
    """

    try:
        response = client.chat.completions.create(
            model=MODEL_NAME,
            messages=[
                {
                    "role": "system", 
                    "content": "あなたは有用な調査クエリを作成するアシスタントです。必ずJSON形式で出力してください。"
                },
                {"role": "user", "content": prompt_content}
            ],
            response_format={ "type": "json_object" }
        )
        
        # 結果の取得と表示
        result_text = response.choices[0].message.content
        parsed_json = json.loads(result_text)
        
        print("\n--- 実行結果 (JSON) ---")
        print(json.dumps(parsed_json, indent=2, ensure_ascii=False))

    except Exception as e:
        print(f"\n[Error] エラーが発生しました: {e}")

if __name__ == "__main__":
    run_test()

```

## 3. 実行方法

ターミナルで以下を実行します。

```bash
python test_prompt.py

```

## 4. トラブルシューティング

* **エラー: `ModuleNotFoundError: No module named 'openai'**`
* 手順1の `pip install openai` が完了していない可能性があります。再度実行してください。


* **エラー: `AuthenticationError**`
* `API_KEY` が間違っているか、OpenRouterのクレジットが不足している可能性があります。


