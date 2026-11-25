# 第6回 解答

## 演習

<!-- [tool call] の行は、表示された行をそのまま貼り付けてください。終わらなかった STEP は、空欄のままでかまいません。 -->

- STEP 1 のツールの名前：[search_materials],[make_quiz],[glossary_search_glossary]
- STEP 2 の [tool call]：glossary_search_glossary({'query': '3レベル'})
- STEP 3 の [tool call]：fetch_fetch({'url': 'https://modelcontextprotocol.io/'})
- STEP 4 の [tool call]：aws_aws___search_documentation({'search_phrase': 'Amazon S3 バージョニング 概要', 'topics': ['general']})  
aws_aws___search_documentation({'search_phrase': 'Using versioning in S3 buckets Amazon Simple Storage Service', 'topics': ['general']})  
glossary_search_glossary({'query': 'S3'})  
- 気づき・疑問：STEP4では日本語と英語で２回問い合わせをしていた。

## 宿題

<!-- 基礎・実装・発展のどれか1つに取り組めば完了です。 -->

- 取り組んだレベル： 実装 
- やったこと：app/mcp_server.pyに以下を追加  
server.tool(
    description=(
        "指定した講義回の用語から確認問題と正解を返す。"
        "「第○回のクイズを作って」「第○回の確認問題を出して」"
        "という依頼で使用する。"
        "存在しない講義回の場合も、推測せず、このツールに"
        "指定された講義番号を渡して結果を確認する。"
    ) 
)(tools.make_quiz)  
　　     
app/tools.pyを修正  
修正前：return f"第{lecture}回の用語が見つかりませんでした。"  
修正後：raise ValueError(f"第{lecture}回の用語が見つかりませんでした。")    
  
- 結果：  
修正前：  
[tool call]   glossary_make_quiz({'lecture': 99})
[tool return] glossary_make_quiz -> 第99回の用語が見つかりませんでした。…
[usage] 入力 5,480 tokens / 出力 54 tokens / モデル応答 2 回  
  
修正後：  
--- trace ---
[tool call]   glossary_make_quiz({'lecture': 99})
[tool error]  glossary_make_quiz -> Error executing tool make_quiz
[usage] 入力 5,482 tokens / 出力 68 tokens / モデル応答 2 回
  
- 気づき・疑問：  
raiseに変更した場合、Error executing tool make_quizとエラー内容を返したが具体的な理由は通知されなかった。しかしLLMの方で正しい出力「指定された第99回は、現在の講座の用語集・カリキュラムに存在しないようです。申し訳ありませんが、存在する回のクイズ（例えば第1回〜第N回など）を指定していただければ、対応いたします！」が得られたが、ツールの失敗などを明確にする部分で使用するべき命令なのかと思いました。
