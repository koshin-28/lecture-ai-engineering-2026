# 第5回 解答

## 演習

- `--list-tools` で見えたツール名と、第3回（`--mcp` なし）との違い：

  `glossary_search_glossary`、`glossary_get_lecture_summary`、
  `fetch_fetch` の3個が表示された。
  第3回はアプリ内の関数を直接ツールとして利用していたが、
  今回はMCPサーバーが公開したツールをクライアントから利用した。
  ツール名には `glossary_` や `fetch_` の接頭辞が付いていた。

- `--mcp --trace` で見えたツール呼び出し（ツール名と引数）と、`requests` の回数：

    fetch_fetch({'url': 'https://modelcontextprotocol.io/'})、続いて glossary_search_glossary({'query': 'MCP'}) が呼び出された。`requests` は3回、`tool_calls` は2回だった。

- MCP サーバーに公開したツールと、その説明文：  

    `search_glossary` と `get_lecture_summary` を公開した。
  `search_glossary` は講座の用語を検索し、定義と関連する講義回を返す。
  変更前の説明には、AIエンジニアリングや講座固有の言葉について
  回答する前に使用し、queryには用語を1つだけ入れるよう記載されていた。
  `get_lecture_summary` は指定した回の講義の要点を返し、
  講義の内容や関係を質問されたときに使うと説明されていた。

- fetch サーバーを足したときのツール呼び出し（ツール名と引数）：  
    `fetch_fetch({'url': 'https://modelcontextprotocol.io/'})`
  でWebページを取得した。
  docstring変更前は、その後に
  `glossary_search_glossary({'query': 'MCP'})` も呼び出され、
  回答にはWebページの要約と関連する講義回が表示された。

## 宿題

- 取り組んだレベル：基礎
- やったこと：
    glossary の接続設定をstdioから http://127.0.0.1:8000/mcp に変更した。別のターミナルで uv run python -m app.mcp_server --http を起動し、変更前後で同じ質問を --mcp --trace 付きで実行した。 次に、search_glossaryの処理内容や引数は変えず、docstringを次の内容に変更した。「講義の用語集から、用語の定義と関連する講義回を検索する。
    講義での用語の意味や、どの回で扱ったかを確認するときに使う。
    外部Webページの取得や要約には使わない。」
    変更前後で `--mcp --list-tools` を実行し、
    同じWebページの冒頭を日本語3行に要約する質問でも比較した。
- 結果：

  通信方式の比較では、stdioとHTTPのどちらも
  fetch_fetch、glossary_search_glossaryの順に呼び出された。
  requestsは3回、tool_callsは2回で、回答の主要な内容はほぼ同じだった。

  stdioではクライアントがサーバーを起動し、標準入出力で通信する。
  今回のHTTP構成ではサーバーを別に起動し、
  8000番ポートでクライアントからの接続を待ち受けた。

  docstring変更後の `--list-tools` には、新しい説明文が反映された。
  ツール数は3個のままで、ツール名と引数の形式も変わらなかった。
  回答は変更前後ともWebページの内容を要約していたが、
  表現や構成には違いが見られた。

- 気づき・疑問：
stdioとStreamable HTTPの違いは、回答内容よりもサーバーの起動方法と通信経路にあると理解した。stdioではクライアントがサーバーを起動して標準入出力で通信するが、今回のHTTP構成ではサーバーを別に起動し、8000番ポートで接続を待ち受ける。通信方式を変更しても、ツールの機能が同じであれば、同様の回答が得られることを確認した。また、docstringはコードを読む人向けの説明だけでなく、MCPを通じてモデルに渡されるツールの使用説明になると分かった。今回は、使用すべき場面と使用しない場面を明記した。