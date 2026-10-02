# 2026年9月 情報収集 探索特化AIエージェント調査レポート

調査基準日は2026年9月30日です。本レポートは、公開Web・学術文献・社内情報を対象とする「情報収集・探索特化AIエージェント」の直近動向、代表製品、評価軸、実務での選び方を整理し、NOETIDEへ取り込むべき設計上の示唆をまとめます。

結論として、市場は単なる検索要約から、**調査計画、反復探索、証拠検証、出典付き統合、成果物作成または実操作**を一つの長時間ワークフローとして扱う方向へ進んでいます。ただし、用途は一枚岩ではありません。2026年時点では、網羅的な対象発見を重視するWide Research、論点を深く調べるDeep Research、学術文献専用のEvidence Research、社内情報や認証済みサイトを含むEnterprise Researchの四系統に分けて評価する必要があります。

> [!CALLOUT]
> **重要な事実修正**
>
> - ChatGPT AgentによるOperator機能の統合は2026年9月ではなく、OpenAIが発表した**2025年7月17日**です。元のDeep Researchも別モードとして残っています。
> - Exa Agent Ultraの比較値はExaによる公式発表ですが、未公表の競合値についてExa自身が測定したケースを含みます。独立第三者が統一条件で確認した順位としては扱えません。
> - Paper2AgentはNature掲載が2026年9月16日ですが、初期版は2025年9月にarXivへ公開されています。

## 調査結果の要点

1. **探索の単位が「回答」から「調査案件」に変わった。** 主要製品は、問いをサブタスクへ分解し、複数回の検索と資料読解を行い、引用付きレポートを生成します。OpenAI Deep Research、Gemini Deep Research、Claude Researchはいずれもこの流れを公式に説明しています。

2. **WideとDeepが別の最適化問題として認識された。** Wide Researchは「条件を満たす対象を漏れなく列挙する」こと、Deep Researchは「論点を掘り下げて整合した報告書を作る」ことを重視します。Exa Agent UltraとWANDRは前者を明確に押し出しています。

3. **調査と実操作が接続した。** ChatGPT Agent、Perplexity Computer、Manus、Grok Botなどは、検索結果をレポート、表、スライド、サイト操作や後続ワークフローへつなげます。調査専用機能と汎用実行エージェントの境界は薄くなっています。

4. **ソース範囲が競争軸になった。** 公開Webだけでなく、Google Workspace、接続アプリ、MCP、契約データベース、アップロード資料を横断する構成が一般化しています。情報源の権限、鮮度、出所管理がモデル性能と同じくらい重要です。

5. **学術探索は専用化が進んだ。** Elicit、Consensus、Undermind、Sciteは文献検索、スクリーニング、引用文脈、研究ギャップなどに特化しています。Paper2Agentはさらに、論文を読ませるだけでなく、論文のコードとデータを実行可能なMCPツールへ変換します。

6. **評価は最終文章の流暢さから、網羅性、証拠、相互作用、再現性へ移った。** WANDR、AutoResearchBench、DeepResearch Bench II、IDRBenchは、現在の最先端システムにも大きな未解決領域が残ることを示しています。

## 2026年9月の注目発表

### Exa Agent Ultra

Exaは2026年9月25日、Exa Agentの最大努力モードとしてAgent Ultraを発表しました。多数のサブエージェントを用い、数千件のソースを横断しながら、対象の発見と各対象の根拠収集を並列化します。主用途は市場マップ、企業リスト、デューデリジェンス、文献と実装の網羅収集、エンティティ属性の証拠付き補完です。[Exa公式発表](https://exa.ai/blog/exa-agent-ultra)

Exaの公表値では、WANDRでOpus 5.5を12.6%上回り、タスク当たりコストは約半分とされています。DeepSearchQAではPerplexity比4.7%、GPT-6 Astra比10.1%、WideSearchではPerplexity比5.2%の改善を主張しています。これらは有用な参考値ですが、Exaは「公開済みの競合値がない場合は自社で測定した」と説明しており、ツール構成と判定モデルにも差があります。したがって、導入判断では自社タスクによる再評価が必要です。

NOETIDEの観点では、Agent UltraはTargeted Researchそのものというより、**Coverage Modelが不足を示した領域を広く埋める外部Research Provider**として適しています。取得結果をそのままEvidenceにせず、レコード単位の出典、引用箇所、取得日時、重複関係を保持する必要があります。

### Paper2Agent

Paper2Agentの査読論文は2026年9月16日にNatureへ掲載されました。論文本文、コード、データ、チュートリアルを解析し、実行可能なMCPサーバーとツールを生成、テスト、修復します。生成されたPaper Agentは質問への回答だけでなく、元論文の手法を新しいデータへ適用できます。[Nature論文](https://www.nature.com/articles/s41586-026-11044-y) [公式GitHub](https://github.com/jmiao24/Paper2Agent)

100本の計算生物学論文では74本のエージェント化に成功し、599個の提案ツールのうち593個が自動検証を通過しました。AlphaGenomeの例では22ツールを約45分、約14米ドルで生成し、15件のチュートリアル由来質問で98.7%、15件の新規質問で100%の精度を報告しています。大規模評価では300問に対して91.2%を達成し、論文とリポジトリへ直接アクセスするClaude Codeの比較条件を上回りました。

一方、26本は実行可能コード、データ、モデル成果物、依存環境などの不足で完全なエージェント化に失敗しました。Paper2Agentは一般Web探索の代替ではなく、**検証済み研究成果を実行可能な探索対象へ変える仕組み**です。複数のPaper Agentを組み合わせた乾癬関連座位のケースでは、GPR137を候補遺伝子として優先し、別論文由来の実験データで整合性を検証しています。

## 代表的な製品と適合用途

| 系統と製品 | 2026年9月時点の特徴 | 適合する用途 | 留意点 |
| --- | --- | --- | --- |
| Exa Agent Ultra | 多数のサブエージェント、数千ソース、WideかつDeepな構造化収集 | 市場マップ、企業探索、網羅リスト、属性補完 | ベンチマークはベンダー公表値。自社条件で再測定が必要 |
| OpenAI Deep Research | 調査計画、反復検索、ソース指定、接続アプリとMCP、引用付きレポート | 汎用の複雑調査、特定サイトや接続ソースを指定した調査 | ChatGPT Agentは実操作向け、元のDeep Researchは詳細調査向けとして併存 |
| Gemini Deep ResearchとDeep Research Max | Google Search、Gmail、Drive、Notebook、アップロード資料。MaxはGemini 3.1 Pro、MCP、可視化を強化 | Google Workspaceを含む横断調査、企業内レポート | Workspace情報を使う場合の権限境界と引用確認が必要 |
| Perplexity Deep Research in Computer | Search as Codeを用いるDeep Researchから表、スライド、ダッシュボード、後続処理へ接続 | 市場調査から成果物作成までを一環実行 | 製品内ベンチの優位性主張は独立評価と分ける |
| Claude Research | Web、Google Workspace、接続Integrationを横断し、5分から最大45分規模で調査 | 長文資料読解、論点整理、内部外部情報の統合 | 主要機能の起点は2025年。2026年新製品として扱わない |
| Grok DeepSearchとMulti Agent | WebとXのリアルタイム性、APIのMulti Agentモデルによる並列Deep Research | 速報、X上の反応、進行中トピック、並列探索 | X由来情報は一次情報、意見、未確認情報を明確に分離する |
| Manus Wide Research | 汎用Manusインスタンスを多数並列化。Browser Operatorで認証済みサイトも利用 | 数百対象の比較、ログイン後情報を含む調査、実操作 | 汎用実行エージェントであり、証拠品質管理は別途必要 |
| Genspark | Deep Researchからスライド、表、各種Office成果物へ展開 | 調査結果をすぐ説明資料に変換する業務 | 成果物品質と調査網羅性を別々に評価する |
| Felo AI Research | 日本語を含む30言語超の横断検索、論文検索、レポート、スライド、マインドマップ | 日本語起点の多言語調査、短時間の初期探索 | 2億件、約3分などは自社公表値 |
| 日経バリューサーチ AIリサーチ | 日経記事、業界レポート、統計、開示情報などを基盤に出典付き整理と比較 | 日本企業、業界、非上場企業を含む国内ビジネス調査 | 提供開始日は2026年4月8日。公開Web全体の探索ではない |
| secondz Deep Research | 公開情報を使った調査計画と多段階調査、営業準備に特化 | 顧客理解、商談準備、提案支援 | 2025年開始。汎用調査製品ではなく営業業務特化 |
| ElicitとConsensus | 学術検索、スクリーニング、比較表、研究ギャップ、引用付き回答 | 系統的レビュー、医療・科学のエビデンス統合 | 対象コーパス、全文アクセス、研究デザイン判定を確認 |
| UndermindとScite | Undermindは探索過程、Sciteは引用文脈と支持・反証の把握に強い | 重要論文の発見、引用関係の検証 | 単体で完全な調査報告書を作る用途とは役割が異なる |

## 主要製品の確認済み仕様

OpenAIは2025年7月17日にChatGPT Agentを発表し、Operatorのブラウザ操作、Deep Researchの多段階調査、ターミナルと接続ソースを一つのエージェントへ統合しました。ただし元のDeep Researchは残り、2026年2月にはMCPやアプリ接続、信頼サイトへの検索限定、途中介入が追加されています。[ChatGPT Agent公式発表](https://openai.com/index/introducing-chatgpt-agent/) [Deep Research公式情報](https://openai.com/index/introducing-deep-research/)

GoogleはGemini Deep ResearchでGoogle Searchに加えGmail、Drive、アップロードファイル、Gemini Notebookを選択可能にしています。2026年4月のDeep Research MaxはGemini 3.1 Pro、MCP対応、ネイティブ可視化を掲げ、同じ研究基盤をGemini、NotebookLM系製品、Search、Financeへ展開しています。[Google Deep Research Max](https://blog.google/innovation-and-ai/models-and-research/gemini-models/next-generation-gemini-deep-research/) [Geminiヘルプ](https://support.google.com/gemini/answer/15719111)

Perplexityは2026年6月、Deep ResearchをComputerへ統合し、調査結果からレポート、表、デッキ、ダッシュボード、Webサイトや追加ワークフローを作れるようにしました。[Perplexity更新情報](https://www.perplexity.ai/changelog/deep-research-command-panel-forking-inline-actions-and-enterprise-controls)

AnthropicのClaude Researchは2025年4月に始まり、同年5月にはWeb、Google Workspace、接続Integrationを横断するAdvanced Researchへ拡張されました。複雑な調査は最大45分、数百の内部外部ソースを扱うと説明されています。[Anthropic公式発表](https://www.anthropic.com/news/integrations)

xAIはGrok 3でDeepSearchを導入し、2026年にはAPI向けGrok 4.20 Multi Agent Betaを提供しています。後者は複数エージェントが並列にDeep Researchを行うモデルです。提示文にある `/deep-research` という一般向け正式名称は、確認した公式資料では裏付けられませんでした。[xAI Multi Agentモデル](https://docs.x.ai/developers/models/grok-4.20-multi-agent-0309) [Grok DeepSearch発表](https://x.ai/blog/grok-3)

日本関連では、Feloが多言語検索と日本語最適化、日経バリューサーチが日経独自情報、secondzが営業調査特化という異なる強みを持ちます。日経AIリサーチの提供開始は2026年4月8日で、発表日は5月25日です。[日経発表](https://prtimes.jp/main/html/rd/p/000000642.000011115.html) [secondz公式サービス](https://deep-research.secondz.digital/) [Felo公式サービス](https://felo.ai/ja/tools/research)

## 探索能力を測るベンチマーク

| 評価系 | 何を測るか | 2026年時点の示唆 |
| --- | --- | --- |
| WANDR | 500件の実務的タスクで、条件に合う多数の対象を発見し、各レコードを引用と抜粋で検証する | 高努力設定でも論文中の最良システムはsoft F1 0.363、hard F1 0.133。網羅収集は未解決 |
| AutoResearchBench | 特定論文を多段階で追うDeep Researchと、条件に合う論文群を集めるWide Research | 最良モデルでもDeep 9.39%、Wide IoU 9.31%。科学文献探索は一般ブラウズより難しい |
| DeepResearch Bench II | 22分野132タスクを9,430個の専門家由来ルーブリックで評価 | 最強システムでも適合率50%未満。情報想起、分析、表現を分けて測る必要 |
| IDRBench | 調査途中の質問、ユーザーフィードバック、調整効果と会話コスト | 対話は品質と意図整合を改善するが、ターン数とトークンのコストが増える |
| S1 DeepResearch | 長時間の計画、証拠収集、統合、指示遵守、ファイル理解、レポート生成を一体で学習 | 検索QAだけでは不足し、調査軌跡全体を学習・評価する方向を示す |

[WANDR論文](https://arxiv.org/abs/2608.14747) [AutoResearchBench論文](https://arxiv.org/abs/2604.25256) [DeepResearch Bench II論文](https://arxiv.org/abs/2601.08536) [IDRBench論文](https://arxiv.org/abs/2601.06676) [S1 DeepResearch論文](https://arxiv.org/abs/2606.15367)

これらの評価から、探索専用エージェントの品質は一つの総合点では捉えられません。最低でも、対象発見のRecall、レコード単位のPrecision、主張ごとの引用妥当性、ソース独立性、鮮度、矛盾検出、途中での計画修正、再実行コストを分けて測る必要があります。

## 実務での選び方

### 網羅的な市場マップや企業リスト

Exa Agent Ultra、Manus Wide Research、GrokのMulti Agent系が候補です。評価時は、既知企業を何件当てたかだけでなく、未知企業の追加発見、重複排除、各属性の根拠URL、条件を満たさない対象の除外精度を測ります。

### 論点を掘り下げた調査報告書

OpenAI Deep Research、Gemini Deep Research、Claude Research、Perplexity Deep Researchが中心です。対象サイト指定、アップロード資料、接続アプリ、途中介入、引用の主張単位対応、報告書の編集可能性で選びます。

### 社内情報と公開情報の横断

Google Workspace中心ならGemini、複数IntegrationやMCPならClaude、接続アプリと特定サイト制御ならOpenAIが有力です。日経バリューサーチは日本企業と業界調査で公開Webとは異なる価値があります。権限継承、監査ログ、情報の学習利用、引用から原資料へ戻れるかを確認します。

### 学術文献とエビデンス統合

系統的レビューや構造化抽出はElicit、学術検索と研究ギャップ探索はConsensus、候補論文の深い発見はUndermind、引用の支持・反証文脈はSciteが適しています。再現可能なコードを持つ論文の手法適用にはPaper2Agentが別枠で有力です。

### 日本語中心の初期調査

多言語横断と日本語出力を短時間で行うならFelo、日経一次情報で国内企業を調べるなら日経AIリサーチ、営業案件へ定型導入するならsecondzが候補です。日本語表示の良さと、日本語ソースを実際に探索できることは別なので、ソース言語別のRecallを試験します。

## NOETIDEへの設計上の示唆

今回の動向は、NOETIDEが採用するResearch、Synthesize、Plan、PatchとStrategy Graphの方針を置き換えるものではありません。むしろ、Research層を用途別にルーティングし、外部探索エージェントの結果をEvidenceへ昇格させる前の検証を強化する材料になります。

1. **Research Providerを探索形態で分類する。** Providerに `deep_report`、`wide_collection`、`academic_evidence`、`interactive_research`、`executable_paper` の能力フラグを持たせます。同じ問いをすべてのProviderへ投げない設計が必要です。

2. **Wide Researchの出力をレコード集合として扱う。** レポート本文ではなく、対象、属性、条件判定、主張、引用箇所、取得日時、検証状態を個別に保存します。WANDR型のsoftとhard completenessをCoverage Modelへ取り込めます。

3. **Paper Agentを新しいSource Adapterとして扱う。** Paper2Agent由来MCPはSourceそのものではなく、論文、コード、データへ到達する実行アダプターです。ツール実行結果はObservationとして保存し、検証後にEvidenceへ変換します。

4. **ベンダー主張と独立証拠を分離する。** 製品ベンチマーク、製品ブログ、独立論文、利用者報告にEvidence Classを付与します。Exaの数値は重要ですが、自己評価であることをグラフ上に保持すべきです。

5. **調査途中の対話をResearch Planの変更イベントとして保存する。** IDRBenchが示すように、ユーザーとの途中調整は品質向上に寄与します。対話内容をチャット記憶ではなく、調査範囲、除外条件、優先順位のPatchとして永続化します。

6. **Cache as Memoryを結果だけでなく探索軌跡へ広げる。** 検索クエリ、除外候補、失敗した取得、重複判定、ソースの最終確認日時を残すことで、同じ調査を別Providerで再実行しやすくなります。

7. **評価をProvider非依存にする。** NOETIDE内に小規模な代表タスクセットを持ち、Recall、引用妥当性、独立ソース数、鮮度、コスト、所要時間、Patch発生率を継続測定します。

## 推奨する検証手順

導入候補を一つのデモ回答で選ばず、同じタスクを複数系統へ流す小規模な比較試験を推奨します。

1. 既知の正解集合が一部あるWideタスクを3件用意する。

2. 複数の論点と対立証拠を含むDeepタスクを3件用意する。

3. 社内資料と公開情報を組み合わせるタスクを2件用意する。

4. 学術文献の再現性確認または手法適用タスクを2件用意する。

5. 各結果をレコードRecall、誤包含、引用妥当性、根拠の独立性、鮮度、実行時間、費用で採点する。

6. 同じProviderを再実行し、結果の安定性と差分説明能力を確認する。

7. 高影響な主張は人間が原文を確認し、そのレビュー結果をNOETIDEのEvidence Provenanceへ戻す。

短期的には、Exa Agent UltraをWide Researchの比較候補、OpenAI、Gemini、Claude、PerplexityをDeep Researchの比較候補、ElicitまたはConsensusを学術探索候補、Paper2Agentを実行可能研究資産の実験候補として評価すると、探索系統の違いを把握しやすくなります。

## 情報源

### 直近発表と製品

- [Exa Agent Ultra公式発表 2026年9月25日](https://exa.ai/blog/exa-agent-ultra)

- [Paper2Agent Nature論文 2026年9月16日](https://www.nature.com/articles/s41586-026-11044-y)

- [Paper2Agent公式GitHub](https://github.com/jmiao24/Paper2Agent)

- [OpenAI ChatGPT Agent公式発表 2025年7月17日](https://openai.com/index/introducing-chatgpt-agent/)

- [OpenAI Deep Research公式発表と2026年更新](https://openai.com/index/introducing-deep-research/)

- [Google Deep Research Max 2026年4月21日](https://blog.google/innovation-and-ai/models-and-research/gemini-models/next-generation-gemini-deep-research/)

- [Gemini Deep Research公式ヘルプ](https://support.google.com/gemini/answer/15719111)

- [Perplexity Deep Research in Computer](https://www.perplexity.ai/changelog/deep-research-command-panel-forking-inline-actions-and-enterprise-controls)

- [Anthropic Advanced Research](https://www.anthropic.com/news/integrations)

- [xAI Grok Multi Agentモデル](https://docs.x.ai/developers/models/grok-4.20-multi-agent-0309)

- [Manus Wide Research](https://manus.im/blog/introducing-wide-research)

- [Gemini Notebookへの名称変更](https://blog.google/innovation-and-ai/products/gemini-notebook/notebooklm-gemini-notebook/)

- [Felo AI Research](https://felo.ai/ja/tools/research)

- [日経バリューサーチ AIリサーチ](https://prtimes.jp/main/html/rd/p/000000642.000011115.html)

- [secondz Deep Research](https://deep-research.secondz.digital/)

- [Elicit Research Agent](https://elicit.com/blog/introducing-elicit-research-agent)

- [Consensus Research Agent](https://help.consensus.app/en/articles/12641232-research-agent)

### 評価研究

- [WANDR](https://arxiv.org/abs/2608.14747)

- [AutoResearchBench](https://arxiv.org/abs/2604.25256)

- [DeepResearch Bench II](https://arxiv.org/abs/2601.08536)

- [IDRBench](https://arxiv.org/abs/2601.06676)

- [S1 DeepResearch](https://arxiv.org/abs/2606.15367)

製品ベンダーが公表する能力値、速度、コスト、優位性は、特に明記しない限り各社の自己評価です。本レポートでは公式情報を優先しましたが、契約、料金、利用上限、地域提供、モデル名は変わりやすいため、導入時に再確認してください。
