## LLM05:2025 不適切な出力処理

### 説明

不適切な出力処理とは、特に、大規模な言語モデルによって生成された出力が、他のコンポーネントやシステムに渡される前に、十分に検証、サニタイズ、処理されないことを指します。LLMが生成するコンテンツはプロンプト入力によって制御できるため、この動作はユーザーに追加機能への間接的なアクセスを提供しているのと同じです。
不適切な出力処理は、LLMが生成した出力が下流に渡される前に対処するという点で、過度の信頼とは異なります。一方、過度の信頼は、LLMの出力の正確さと適切さへの過度の依存に関するより広範な懸念に焦点を当てています。
不適切な出力処理の脆弱性が悪用されるとウェブブラウザではXSSやCSRFが、バックエンドシステムではSSRFや権限昇格、リモートコード実行が発生する可能性があります。
この脆弱性の影響を増大させる可能性があるのは、以下の条件です：
- アプリケーションは LLM にエンドユーザが意図する以上の特権を与え、特権の昇格やリモートでのコード実行を可能にします。
- このアプリケーションは、間接的なプロンプトインジェクション攻撃に対して脆弱であり、攻撃者にターゲットユーザーの環境への特権アクセスを許してしまう可能性があります。
- サードパーティの拡張機能は、入力を適切に検証しません。
- 異なるコンテキスト（HTML、JavaScript、SQLなど）に対する適切な出力エンコーディングの欠如
- LLMアウトプットの不十分なモニタリングと記録
- LLM使用時のレート制限や異常検知の不在

### 脆弱性の一般的な例

1. LLMの出力がシステムシェルやexecやevalのような類似関数に直接入力され、リモートでコードが実行される。
2. JavaScriptやMarkdownはLLMによって生成され、ユーザーに返されます。そのコードはブラウザによって解釈され、XSSになります。
3. LLMが生成したSQLクエリが適切なパラメータ化なしに実行され、SQLインジェクションにつながる。
4. LLMの出力が適切なサニタイズなしにファイルパスを構築するために使用され、パストラバーサル脆弱性を引き起こす可能性がある。
5. LLMで生成されたコンテンツが適切なエスケープを施されずにEメールテンプレートで使用される。

### 予防と緩和の戦略

1. モデルを他のユーザーと同じように扱い、信頼ゼロのアプローチを採用し、モデルからバックエンド機能への応答に適切な入力検証を適用する。
2. OWASP ASVS（Application Security Verification Standard）ガイドラインに従い、効果的な入力検証とサニタイズを確実に行う。
3. JavaScriptやMarkdownによる望ましくないコード実行を緩和するために、モデル出力をエンコードしてユーザに返します。OWASP ASVS は、出力エンコードに関する詳細なガイダンスを提供しています。
4. LLMの出力が使用される場所に基づいて、コンテキストを考慮した出力エンコーディングを実装する（例えば、ウェブコンテンツのHTMLエンコーディング、データベースクエリのSQLエスケーピング）。
5. LLM出力を含むすべてのデータベース操作には、パラメータ化されたクエリまたはプリペアド・ステートメントを使用してください。
6. 厳格なコンテンツ・セキュリティ・ポリシー（CSP）を採用し、LLMが生成したコンテンツからのXSS攻撃のリスクを軽減する。
7. 搾取の試みを示す可能性のあるLLM出力の異常なパターンを検出するために、堅牢なロギングと監視システムを導入する。

### 攻撃シナリオの例

#### シナリオ #1
あるアプリケーションは、チャットボット機能の応答を生成するためにLLM拡張機能を利用している。この拡張機能は、別の特権 LLM がアクセスできる多くの管理機能も提供している。汎用の LLM は、適切な出力検証を行うことなく、レスポンスを直接拡張機能に渡し、拡張機能がメンテナンスのためにシャットダウンする原因となる。
#### シナリオ #2
ユーザーはLLMを搭載したウェブサイト要約ツールを利用し、記事の簡潔な要約を生成する。このウェブサイトには、LLMにウェブサイトまたはユーザーの会話から機密コンテンツをキャプチャするよう指示するインジェクションが含まれている。そこからLLMは機密データをエンコードし、出力の検証やフィルタリングを行うことなく、攻撃者がコントロールするサーバーに送信することができる。
#### シナリオ #3
LLMでは、ユーザーがチャットのような機能を使ってバックエンドデータベースに対するSQLクエリを作成することができます。ユーザはデータベースの全テーブルを削除するクエリを要求する。LLMが作成したクエリが精査されなければ、すべてのデータベーステーブルが削除される。
#### シナリオ #4
あるウェブアプリケーションは、LLM を使用して、出力がサニタイズされていないテキストプロンプトからコンテンツを生成します。攻撃者は細工したプロンプトを送信することで、LLMがサニタイズされていないJavaScriptペイロードを返し、被害者のブラウザでレンダリングされた際にXSSを引き起こす可能性がある。プロンプトの不十分な検証により、この攻撃が可能にした。
#### シナリオ # 5
LLMは、マーケティングキャンペーン用の動的な電子メールテンプレートを生成するために使用される。攻撃者は LLM を操作して、悪意のある JavaScript をメールコンテンツに含めます。アプリケーションが LLM の出力を適切にサニタイズしていない場合、脆弱なメールクライアントでメールを閲覧した受信者に XSS 攻撃を仕掛ける可能性がある。
#### シナリオ #6
LLMは、ソフトウェア会社で自然言語入力からコードを生成するために使用され、開発作業を効率化することを目的としている。効率的ではあるが、このアプローチには機密情報を暴露したり、安全でないデータ処理方法を作成したり、SQLインジェクションのような脆弱性を導入したりするリスクがある。また、AIは存在しないソフトウェア・パッケージを幻視し、開発者をマルウェアに感染したリソースのダウンロードに導く可能性もある。セキュリティ侵害、不正アクセス、システム侵害を防ぐには、提案されたパッケージの徹底したコードレビューと検証が極めて重要である。

### 参考リンク

1. [Proof Pudding (CVE-2019-20634)](https://avidml.org/database/avid-2023-v009/) **AVID** (`moohax` & `monoxgas`)
2. [Arbitrary Code Execution](https://security.snyk.io/vuln/SNYK-PYTHON-LANGCHAIN-5411357): **Snyk Security Blog**
3. [ChatGPT Plugin Exploit Explained: From Prompt Injection to Accessing Private Data](https://embracethered.com/blog/posts/2023/chatgpt-cross-plugin-request-forgery-and-prompt-injection./): **Embrace The Red**
4. [New prompt injection attack on ChatGPT web version. Markdown images can steal your chat data.](https://systemweakness.com/new-prompt-injection-attack-on-chatgpt-web-version-ef717492c5c2?gi=8daec85e2116): **System Weakness**
5. [Don’t blindly trust LLM responses. Threats to chatbots](https://embracethered.com/blog/posts/2023/ai-injections-threats-context-matters/): **Embrace The Red**
6. [Threat Modeling LLM Applications](https://aivillage.org/large%20language%20models/threat-modeling-llm/): **AI Village**
7. [OWASP ASVS - 5 Validation, Sanitization and Encoding](https://owasp-aasvs4.readthedocs.io/en/latest/V5.html#validation-sanitization-and-encoding): **OWASP AASVS**
8. [AI hallucinates software packages and devs download them – even if potentially poisoned with malware](https://www.theregister.com/2024/03/28/ai_bots_hallucinate_software_packages/) **Theregiste**
