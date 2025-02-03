## LLM01:2025 プロンプトインジェクション

### 説明
プロンプトインジェクションの脆弱性とは、ユーザーが入力するプロンプトが、意図しない方法でLLM（大規模言語モデル）の動作や出力を変更してしまう場合に発生するものです。このような入力は、人間には認識できない場合でもモデルに影響を与える可能性があるため、プロンプトインジェクションが成立するために人間にとって可視的または読み取り可能である必要はありません。モデルがその内容を解析できれば十分となります。

プロンプトインジェクション脆弱性は、モデルがプロンプトを処理する方法や、入力がモデルに対してプロンプトデータを他の部分に誤って渡すよう強制する可能性がある場合に存在します。これにより、ガイドラインの違反、有害なコンテンツの生成、不正アクセスの許可、または重要な意思決定への影響が引き起こされる可能性があります。RAG（Retrieval Augmented Generation）やファインチューニングなどの技術は、LLMの出力をより適切かつ正確にすることを目的としていますが、研究によると、これらの手法はプロンプトインジェクションの脆弱性を完全には軽減できないことが示されています。

プロンプトインジェクションとジェイルブレイキングは、LLMセキュリティにおいて関連する概念ですが、しばしば同義として扱われます。プロンプトインジェクションは、特定の入力を通じてモデルの応答を操作し、その挙動を変化させるものであり、安全対策を回避することが含まれる場合があります。一方、ジェイルブレイキングは、プロンプトインジェクションの一形態であり、攻撃者がモデルに入力を提供し、それによってモデルが安全プロトコルを完全に無視するよう誘導するものです。開発者はシステムプロンプトや入力処理に安全対策を組み込むことでプロンプトインジェクション攻撃を軽減できますが、ジェイルブレイキングの効果的な防止には、モデルのトレーニングおよび安全メカニズムを継続的に更新する必要があります。

### プロンプトインジェクション脆弱性の種類

#### ダイレクトプロンプトインジェクション
プロンプトの直接入力は、ユーザーのプロンプト入力がモデルの動作を意図しないか予期しない方法で直接変更する場合に発生する。入力には、意図的なもの（悪意のある行為者が意図的にプロンプトを作成し、モデルを悪用する場合）と、意図的でないものユーザーが不注意に入力を行い、予期しない動作を引き起こす場合）がある。

#### インダイレクトプロンプトインジェクション
インダイレクトプロンプトインジェクションは、LLMがウェブサイトやファイルなどの外部ソースからの入力を受け入れるときに発生する。そのコンテンツは、外部コンテンツデータを持っている可能性があり、モデルの動作を意図しない、あるいは予期しない方法で変化させます。ダイレクトインジェクションと同様、インダイレクトインジェクションにも意図的なものと意図的でないものがあります。

プロンプトインジェクションが成功した場合の影響の重大性と性質は、ケースによって大きく異なる可能性があり、モデルに関連したビジネス的な文脈と、モデルが設計されたエージェンシーの両方に大きく依存します。しかし、一般的に、プロンプトインジェクションは、以下を含むがこれに限定されない、または予期せぬ結果につながる可能性があります：
- 機密情報の開示
- AIシステムインフラやシステムプロンプトに関する機密情報の漏洩
- 不正確または偏った出力につながるコンテンツ操作
- LLMが利用可能な機能への不正アクセスを実施
- 接続されたシステムで任意のコマンドを実行
- 重要な意思決定プロセスを操作

複数のデータタイプを同時に処理するマルチモーダルAIの台頭は、独自のプロンプトインジェクションリスクをもたらす。悪意のある行為者は、良質のテキストに付随する画像に指示を隠すなど、モダリティ間の相互作用を悪用する可能性がある。このようなシステムの複雑さは、攻撃対象領域を拡大する。また、マルチモーダルモデルは、現在の技術では検出や軽減が困難な、新しいクロスモーダル攻撃の影響を受けやすい可能性がある。ロバストなマルチモーダル特有の防御は、さらなる研究開発のための重要な分野である。

### 防御と緩和の戦略

プロンプトインジェクションの脆弱性は、ジェネレーティブAIの性質上起こりうる。モデルの動作方法の中核に確率的な影響があることを考えると、プロンプト・インジェクションを防ぐ確実な方法があるかどうかは不明である。しかし、以下の対策はプロンプト・インジェクションの影響を軽減することができる：

#### 1. モデルの動作を制約する
　システムプロンプト内でのモデルの役割、能力、制限について具体的な指示を与える。コンテキストの厳守を徹底し、特定のタスクやトピックに回答を限定し、中核となる指示を修正しようとする試みを無視するようモデルに指示する。
#### 2. 期待される出力形式の定義と検証
　明確な出力形式を指定し、詳細な理由とソースの引用を要求し、決定論的コードを使用してこれらの形式への準拠を検証する。
#### 3. 入出力フィルタリングの実装
　センシティブなカテゴリーを定義し、そのようなコンテンツを識別して処理するためのルールを構築する。セマンティックフィルタを適用し、文字列チェックを使用して許可されていないコンテンツをスキャンする。RAGトライアドを使用して回答を評価する：文脈の関連性、根拠、質問と回答の関連性を評価し、悪意のある可能性のある出力を特定する。
#### 4. 権限制御と最小権限アクセスの強制
　拡張可能な機能のためにアプリケーションに独自のAPIトークンを提供し、これらの機能をモデルに提供するのではなく、コードで処理する。モデルのアクセス権限を、意図した操作に必要な最小限のものに制限する。
#### 5. リスクの高い行為には人間の承認が必要
　権限のない操作を防止するために、特権操作に対してヒューマンインザループ・コントロールを導入する。
#### 6. 外部コンテンツの分離と識別
　信頼できないコンテンツを分離して明示し、ユーザーのプロンプトへの影響を制限する。
#### 7. 敵対的テストと攻撃シミュレーションの実施
　信頼境界とアクセス制御の有効性をテストするために、モデルを信頼されていないユーザーとして扱い、定期的な侵入テストと侵入シミュレーションを実施する。

### 攻撃シナリオの例

#### シナリオ #1: ダイレクトインジェクション
　攻撃者は、カスタマーサポートのチャットボットにプロンプトを注入し、以前のガイドラインを無視して個人データストアに問い合わせたり、電子メールを送信したりするよう指示し、不正アクセスや権限の昇格を引き起こす。
#### シナリオ #2:インダイレクトインジェクション
　あるユーザーがLLMを使ってウェブページを要約すると、LLMがURLへのリンク画像を挿入し、プライベートな会話が流出する。
#### シナリオ #3: 意図しないインジェクション
　ある企業が、AIが作成した応募書類を特定するための指示を職務経歴書に記載した。この指示を知らない応募者は、履歴書を最適化するためにLLMを使用し、不注意にもAIによる検出を引き起こしてしまう。
#### シナリオ #4: 意図的なモデルの影響力
　攻撃者は、RAG(Retrieval-Augmented Generation)アプリケーションで使用されるリポジトリ内の文書を改ざんする。ユーザーのクエリが変更されたコンテンツを返すと、悪意のある命令がLLMの出力を変更し、誤解を招く結果を生成する。
#### シナリオ #5: コード・インジェクション
　攻撃者は、LLMを使用した電子メールアシスタントの脆弱性 (CVE-2024-5184) を悪用して悪意のあるプロンプトを挿入し、機密情報へのアクセスや電子メールコンテンツの操作を可能にします。
#### シナリオ #6: ペイロードの分割
　攻撃者は悪意のあるプロンプトを分割した履歴書をアップロードする。LLMが候補者の評価に使用された場合、プロンプトが組み合わされることでモデルの応答が操作され、実際の履歴書の内容とは裏腹に肯定的な推薦がなされる。
#### シナリオ #7: マルチモーダル・インジェクション
　攻撃者は、悪意のあるプロンプトを、良性のテキストに付随する画像内に埋め込む。その際マルチモーダルAIが画像とテキストを同時に処理すると、隠されたプロンプトがモデルの行動を変化させ、不正な行動や機密情報の漏洩につながる可能性がある。
#### シナリオ #8: 敵対的接尾辞
　攻撃者は、一見無意味な文字列をプロンプトに追加し、LLMの出力に悪意のある影響を与え、安全対策を回避する。
#### シナリオ #9: 多言語 / 難読化攻撃
　攻撃者は、フィルターを回避し、LLMの動作を操作するために、複数の言語を使用したり、悪意のある命令をエンコードしたりする（Base64や絵文字を使用するなど）。

### 参考リンク

1. [ChatGPT Plugin Vulnerabilities - Chat with Code](https://embracethered.com/blog/posts/2023/chatgpt-plugin-vulns-chat-with-code/) **Embrace the Red**
2. [ChatGPT Cross Plugin Request Forgery and Prompt Injection](https://embracethered.com/blog/posts/2023/chatgpt-cross-plugin-request-forgery-and-prompt-injection./) **Embrace the Red**
3. [Not what you’ve signed up for: Compromising Real-World LLM-Integrated Applications with Indirect Prompt Injection](https://arxiv.org/pdf/2302.12173.pdf) **Arxiv**
4. [Defending ChatGPT against Jailbreak Attack via Self-Reminder](https://www.researchsquare.com/article/rs-2873090/v1) **Research Square**
5. [Prompt Injection attack against LLM-integrated Applications](https://arxiv.org/abs/2306.05499) **Cornell University**
6. [Inject My PDF: Prompt Injection for your Resume](https://kai-greshake.de/posts/inject-my-pdf) **Kai Greshake**
8. [Not what you’ve signed up for: Compromising Real-World LLM-Integrated Applications with Indirect Prompt Injection](https://arxiv.org/pdf/2302.12173.pdf) **Cornell University**
9. [Threat Modeling LLM Applications](https://aivillage.org/large%20language%20models/threat-modeling-llm/) **AI Village**
10. [Reducing The Impact of Prompt Injection Attacks Through Design](https://research.kudelskisecurity.com/2023/05/25/reducing-the-impact-of-prompt-injection-attacks-through-design/) **Kudelski Security**
11. [Adversarial Machine Learning: A Taxonomy and Terminology of Attacks and Mitigations (nist.gov)](https://nvlpubs.nist.gov/nistpubs/ai/NIST.AI.100-2e2023.pdf)
12. [2407.07403 A Survey of Attacks on Large Vision-Language Models: Resources, Advances, and Future Trends (arxiv.org)](https://arxiv.org/abs/2407.07403)
13. [Exploiting Programmatic Behavior of LLMs: Dual-Use Through Standard Security Attacks](https://ieeexplore.ieee.org/document/10579515)
14. [Universal and Transferable Adversarial Attacks on Aligned Language Models (arxiv.org)](https://arxiv.org/abs/2307.15043)
15. [From ChatGPT to ThreatGPT: Impact of Generative AI in Cybersecurity and Privacy (arxiv.org)](https://arxiv.org/abs/2307.00691)


### 関連フレームワークと分類

インフラ配備に関する包括的な情報、シナリオ戦略、適用される環境管理、その他の
ベストプラクティスについては、以下のセクションを参照してください。

- [AML.T0051.000 - LLM Prompt Injection: Direct](https://atlas.mitre.org/techniques/AML.T0051.000) **MITRE ATLAS**
- [AML.T0051.001 - LLM Prompt Injection: Indirect](https://atlas.mitre.org/techniques/AML.T0051.001) **MITRE ATLAS**
- [AML.T0054 - LLM Jailbreak Injection: Direct](https://atlas.mitre.org/techniques/AML.T0054) **MITRE ATLAS**　
