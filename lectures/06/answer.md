# 第6回 解答

## 演習

<!-- [tool call] の行は、表示された行をそのまま貼り付けてください。終わらなかった STEP は、空欄のままでかまいません。 -->

- STEP 1 のツールの名前：glossary_search_glossary
- STEP 2 の [tool call]：glossary_search_glossary({'query': '3 levels'})
- STEP 3 の [tool call]：fetch_fetch({'url': 'https://modelcontextprotocol.io/'})
- STEP 4 の [tool call]：aws_aws___search_documentation({'search_phrase': 'Amazon S3 versioning overview'})
- 気づき・疑問：MCP を利用すると、エージェントのコードにツールを直接追加せずに、
設定ファイルで接続先のサーバーを追加して機能を拡張できると分かりました。

glossary と fetch は command によって自分の環境で起動するのに対し、
AWS Knowledge は url によって外部で動いているサーバーに接続するという違いを学びました。

また、--trace を使うことで、最終的な回答だけでなく、
実際に呼び出されたツールの名前と引数を確認できました。

## 宿題

<!-- 基礎・実装・発展のどれか1つに取り組めば完了です。 -->

- 取り組んだレベル：基礎
- やったこと：app/mcp_server.py に以下の1行を追加し、
既存の get_lecture_summary を MCP ツールとして公開しました。

```python
server.tool()(tools.get_lecture_summary)
Exercise 完了後、エージェント側のコードや設定ファイルは変更せず、
サーバー側にツールを追加しました。
追加後、以下のコマンドを実行し、第2回の講義内容を質問しました。
uv run python -m app.mcp_agent --trace "What do we learn in Lecture 2?"
- 結果：
    実行時の trace で、追加したツールが呼び出されたことを確認しました。
[tool call]   glossary_get_lecture_summary({'lecture': 2})
[tool return] glossary_get_lecture_summary -> # 第2回 ファインチューニングとLoRA

## この回のねらい

- 事前学習とファインチューニングの違い、ファインチューニングが向く場面と向かない場面を理解する
- LoRAの考え方を理解し、Google Colab上で小さなモデルを…
[usage] 入力 5,942 tokens / 出力 264 tokens / モデル応答 2 回
ツールから第2回「ファインチューニングとLoRA」の概要が返され、
最終回答では、事前学習とファインチューニングの違い、
SFT、PEFT・LoRA、過学習への注意、Colab での演習について説明されました。

モデル応答は2回で、ツールの呼び出しと、その結果に基づく回答が行われたことを確認しました。
- 気づき・疑問：
MCP では、エージェントがサーバーから公開ツールの一覧を取得するため、
サーバー側でツールを追加することで、
エージェント側のコードを変更せずに機能を拡張できると分かりました。

また、自然な回答が出たことだけで判断せず、
--trace で目的のツールと引数、返された内容を確認することが重要だと感じました。