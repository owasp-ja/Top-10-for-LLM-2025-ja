## LLM10:2025 際限のない消費

### 説明

際限のない消費とは、大規模言語モデル（LLM）が入力クエリーやプロンプトに基づいて出力を生成するプロセスを指します。推論はLLMの重要な機能であり、関連する応答や予測を生成するために学習されたパターンや知識を適用します。

サービスを妨害したり、ターゲットの経済的リソースを枯渇させたり、あるいはモデルの動作を複製して知的財産を盗んだりするように設計された攻撃はすべて、成功するために共通のセキュリティ脆弱性に依存しています。際限のない消費は、大規模言語モデル(LLM)アプリケーションが、ユーザーに過剰で制御不能な推論を行わせることで発生し、サービス妨害(DoS)、経済的損失、モデルの盗難、サービス低下などのリスクにつながります。特にクラウド環境では、LLMの計算要求が高いため、リソースの搾取や不正使用に対して脆弱になります。

### 脆弱性のよくある例

#### 1. . Variable-Length Input Flood
  攻撃者は、処理の非効率性を悪用して、長さの異なる多数の入力でLLMに過負荷をかけることができる。これによりリソースが枯渇し、システムが応答しなくなる可能性があり、サービスの可用性に大きな影響を与える。
#### 2. ウォレット拒否（DoW）
  攻撃者は大量のオペレーションを開始することで、クラウドベースのAIサービスの利用単価モデルを悪用し、プロバイダーに持続不可能な経済的負担をもたらし、財政破綻のリスクを冒す。
#### 3. 連続入力オーバーフロー
  LLMのコンテキスト・ウィンドウを超える入力を送信し続けると、計算リソースが過剰に使用され、サービスの低下や運用の中断につながる可能性がある。
#### 4. リソース集約型クエリー
  複雑なシーケンスや複雑な言語パターンを含む異常に負荷の高いクエリを送信すると、システムリソースが消耗し、処理時間が長引いたり、システム障害が発生したりする可能性がある。
#### 5. APIによるモデル抽出
  攻撃者は、部分的なモデルを複製したり、シャドーモデルを作成するのに十分な出力を収集するために、注意深く細工された入力やプロンプトインジェクション技術を用いてモデルAPIに問い合わせることができる。これは知的財産の盗難のリスクをもたらすだけでなく、元のモデルの完全性を損なう。
#### 6. 機能モデルの複製
  ターゲットモデルを使用して合成トレーニングデータを生成することで、攻撃者は別の基礎モデルを微調整し、機能的に同等のものを作成することができる。これは、従来のクエリベースの抽出方法を回避し、独自のモデルや技術に重大なリスクをもたらす。
#### 7. サイドチャンネル攻撃
  悪意のある攻撃者は、LLMの入力フィルタリング技術を悪用してサイドチャネル攻撃を実行し、モデルの重みとアーキテクチャ情報を採取する可能性がある。これはモデルの安全性を損ない、さらなる悪用につながる可能性がある。

### 予防と緩和の戦略

#### 1. 入力検証
  入力が妥当なサイズの制限を超えないように、厳密な入力検証を実施する。
#### 2. ロジットとログプロブの露出を制限する
  API レスポンス中の `logit_bias` と `logprobs` の公開を制限または難読化する。詳細な確率を明らかにせず、必要な情報のみを提供する。
#### 3. レート制限
  レート制限とユーザークォータを適用して、1つのソースエンティティが一定期間に実行できるリクエスト数を制限する。
#### 4. 資源配分管理
  リソースの割り当てを動的に監視・管理し、単一のユーザーやリクエストが過剰なリソースを消費するのを防ぐ。
#### 5. タイムアウトとスロットリング
  リソースを大量に消費する操作にはタイムアウトを設定し、スロットル処理を行うことで、長時間のリソース消費を防ぐ。
#### 6. サンドボックス・テクニック
  LLMのネットワークリソース、内部サービス、APIへのアクセスを制限する。
  - これはインサイダーリスクと脅威を包含するため、一般的なシナリオでは特に重要である。さらに、LLM アプリケーションがデータとリソースにアクセスできる範囲を管理し、サイドチャネル攻撃を軽減または防止するための重要な制御メカニズムとして機能する。
#### 7. 包括的なログ、モニタリング、異常検知
  リソースの使用状況を継続的に監視し、異常な消費パターンを検出して対応するためのロギングを実施する。
#### 8. 電子透かし
  LLM出力を埋め込み、不正使用を検出するための電子透かしフレームワークを実装する。
#### 9. 優雅な劣化
  高負荷の下で優雅に劣化するようにシステムを設計し、完全な故障ではなく部分的な機能を維持する。
#### 10. 待ち行列アクションを制限し、ロバストに拡張する
  さまざまな需要に対応し、一貫したシステム・パフォーマンスを保証するために、動的なスケーリングと負荷分散を取り入れながら、キューに入れられたアクションの数とアクションの総数に対する制限を実装する。
#### 11. 敵対的ロバストネスのトレーニング
  敵対的なクエリや抽出の試みを検出し、軽減するためのモデルを訓練する。
#### 12. グリッチ・トークン・フィルタリング
  モデルのコンテキストウィンドウに追加する前に、既知のグリッチトークンとスキャン出力のリストを作成する。
#### 13. アクセス・コントロール
  役割ベースのアクセス制御（RBAC）や最小特権の原則を含む強力なアクセス制御を導入し、LLMモデルのリポジトリやトレーニング環境への不正アクセスを制限する。
#### 14. MLモデルの一元管理
  適切なガバナンスとアクセス制御を確保し、本番環境で使用されるMLモデルのインベントリまたはレジストリを一元管理する。
#### 15. MLOpsの自動展開
  ガバナンス、トラッキング、承認ワークフローを備えた自動化されたMLOpsデプロイメントを導入し、インフラストラクチャ内のアクセスとデプロイメントのコントロールを強化する。

### 攻撃シナリオの例

#### Scenario #1: 入力サイズの制御不能
  攻撃者は、テキストデータを処理する LLM アプリケーションに異常に大きな入力を送信します。その結果、メモリ使用量と CPU 負荷が過大になり、システムがクラッシュしたり、サービスが著しく遅くなったりする可能性がある。
#### Scenario #2: 繰り返されるリクエスト
  攻撃者がLLM APIに大量のリクエストを送信することで、計算リソースが過剰に消費され、正規ユーザーがサービスを利用できなくなる。
#### Scenario #3: リソース集約型クエリ
  攻撃者は、LLMの最も計算量の多いプロセスをトリガーするように設計された特定の入力を細工し、CPU使用率の長期化とシステム障害の可能性を引き起こす。
#### Scenario #4: ウォレット拒否 (DoW)
  攻撃者は、クラウドベースのAIサービスの従量課金モデルを悪用するために過剰なオペレーションを生成し、サービスプロバイダーに持続不可能なコストをもたらす。
#### Scenario #5: 機能モデルの複製
  攻撃者はLLMのAPIを使用して合成トレーニングデータを生成し、別のモデルを微調整することで、機能的に同等のモデルを作成し、従来のモデル抽出をバイパスする。
#### Scenario #6: システム入力フィルタリングのバイパス
  悪意のある攻撃者は、LLMの入力フィルタリング技術とプリアンブルを迂回してサイドチャネル攻撃を行い、自分の制御下にある遠隔制御リソースにモデル情報を取得する。

### 参考リンク

1. [Proof Pudding (CVE-2019-20634)](https://avidml.org/database/avid-2023-v009/) **AVID** (`moohax` & `monoxgas`)
2. [arXiv:2403.06634 Stealing Part of a Production Language Model](https://arxiv.org/abs/2403.06634) **arXiv**
3. [Runaway LLaMA | How Meta's LLaMA NLP model leaked](https://www.deeplearning.ai/the-batch/how-metas-llama-nlp-model-leaked/): **Deep Learning Blog**
4. [I Know What You See:](https://arxiv.org/pdf/1803.05847.pdf): **Arxiv White Paper**
5. [A Comprehensive Defense Framework Against Model Extraction Attacks](https://ieeexplore.ieee.org/document/10080996): **IEEE**
6. [Alpaca: A Strong, Replicable Instruction-Following Model](https://crfm.stanford.edu/2023/03/13/alpaca.html): **Stanford Center on Research for Foundation Models (CRFM)**
7. [How Watermarking Can Help Mitigate The Potential Risks Of LLMs?](https://www.kdnuggets.com/2023/03/watermarking-help-mitigate-potential-risks-llms.html): **KD Nuggets**
8. [Securing AI Model Weights Preventing Theft and Misuse of Frontier Models](https://www.rand.org/content/dam/rand/pubs/research_reports/RRA2800/RRA2849-1/RAND_RRA2849-1.pdf)
9. [Sponge Examples: Energy-Latency Attacks on Neural Networks: Arxiv White Paper](https://arxiv.org/abs/2006.03463) **arXiv**
10. [Sourcegraph Security Incident on API Limits Manipulation and DoS Attack](https://about.sourcegraph.com/blog/security-update-august-2023) **Sourcegraph**

### 関連フレームワークと分類

インフラ配備に関する包括的な情報、シナリオ戦略、適用される環境管理、その他のベストプラクティスについては、下記のセクションを参照してください。

- [MITRE CWE-400: リソースの無制限消費](https://cwe.mitre.org/data/definitions/400.html) **MITRE 共通脆弱性列挙プログラム**
- [AML.TA0000 MLモデルアクセス: Mitre ATLAS](https://atlas.mitre.org/tactics/AML.TA0000) & [AML.T0024 推論 API による流出](https://atlas.mitre.org/techniques/AML.T0024) **MITRE ATLAS**
- [AML.T0029 - MLサービス拒否](https://atlas.mitre.org/techniques/AML.T0029) **MITRE ATLAS**
- [AML.T0034 - コストはーべスティング](https://atlas.mitre.org/techniques/AML.T0034) **MITRE ATLAS**
- [AML.T0025 - サイバー手段による流出](https://atlas.mitre.org/techniques/AML.T0025) **MITRE ATLAS**
- [OWASP 機械学習セキュリティトップ10- ML05:2023 モデル盗難](https://owasp.org/www-project-machine-learning-security-top-10/docs/ML05_2023-Model_Theft.html) **OWASP ML Top 10**
- [API4:2023 - 無制限のリソース消費](https://owasp.org/API-Security/editions/2023/en/0xa4-unrestricted-resource-consumption/) **OWASP ウェブアプリケーション Top 10**
- [OWASP リソース管理](https://owasp.org/www-project-secure-coding-practices-quick-reference-guide/) **OWASP セキュアコーディングプラクティス**


### 日本語版の作成
Teresa Tsukiji
Yuki Kashiwada
