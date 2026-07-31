## エージェントオーケストレーション · 決定論的ゲート · AI拡張開発

[English](README.md) · [한국어](README.ko.md) · **日本語** · [简体中文](README.zh-CN.md) · [繁體中文](README.zh-TW.md) · [Español](README.es.md) · [Português (BR)](README.pt-BR.md) · [Deutsch](README.de.md) · [Français](README.fr.md) · [Русский](README.ru.md) · [Italiano](README.it.md) · [Bahasa Indonesia](README.id.md) · [Türkçe](README.tr.md)

コードベースの自動化と、エージェントがオーケストレーションするシステム群です。fail-closed な
パーミッションレイヤーを備えたエージェントプラットフォーム、評価ゲート付きのローカル検索パイプライン、
そしてビット単位で同一にリプレイできる決定論的シミュレーションを含みます。

このページに載っている数字はすべて実測値であり、推定値ではありません。その数字を算出したコマンドは
対象のリポジトリ内に置かれており、公開前にリポジトリ側で再検証されます。

---

## フラッグシップ

| プロジェクト | 概要 | 実測された事実 | ライブ |
|---|---|---|---|
| **[baton](https://github.com/euuuuuuan/baton-public)** | 範囲を限定したエージェント作業のためのローカルファーストなオーケストレーションプラットフォーム。fail-closed なパーミッションゲートの背後に置かれた Swift 製 macOS 制御デーモンを、MCP 経由の TypeScript オーケストレーターが駆動します。*(プラットフォーム)* | `docs/TOOL_SURFACE.json` に文書化された **63 個の MCP ツール**、**234 のテスト/スペックファイル**、**15 件の ADR**、さらにグラントがフィンガープリントに紐づき単回使用となるリモート承認ブローカー(ADR-003)。 | — |
| **[marketing-knowledge-rag](https://github.com/euuuuuuan/marketing-knowledge-rag-public)** | ネットワーク分離(망분리)環境向けに構築された韓国語 RAG。標準ライブラリのみのハイブリッド検索、PII マスキング、スコープ外の質問に対する拒否ゲートを備えます。*(ツール)* | **サードパーティ依存 0 件**。ビルドは **50 問のゴールデンセット**でゲートされます:hit@3 **100 %**(39/39)、拒否の適合率 **100 %** / 再現率 **81.8 %** — そして引用精度は **36.75 %**。最も弱い指標ですが、隠さずに公開しています。 | — |
| **[crm-mcp-agent](https://github.com/euuuuuuan/crm-mcp-agent-public)** | ライフサイクル/CRM マーケターの SQL 業務を、MCP 経由のエージェントが代行します。エージェントがどこまで踏み込めるか、何を記録に残すかを決めるガバナンスカーネルの背後で動作します。*(ツール)* | **108 件のテストが通過**(MCP SDK インストール時)。うち、カーネルに対する **33 件の PII レッドチーム**攻撃と **12 件のガバナンスレッドチーム**攻撃を含みます。データは **100 % 合成**でバイト単位に再現可能:シード 42 → 同一の SQLite ダイジェスト、顧客 5,000 件 / イベント 99,922 件。 | — |
| **[fatal-funnel](https://github.com/euuuuuuan/fatal-funnel-public)** | ブラウザで動く戦術ブリーチプランナー兼リアルタイム分隊シミュレーション。完了したレイドは URL として共有でき、他の人のブラウザで再シミュレーションされます。*(プレイ可能なビルドであり、商用リリースではありません)* | **30 Hz 固定ティックのシミュレーション**。純粋性ゲートが sim とコンテンツのパッケージから **11 種の非決定的 API** を締め出し、これが通過していることこそが、シード + 入力ログのリプレイを同一結果にする根拠です。**55 のテスト/スペックファイル**、`tools/` には 40 本のスクリプトがあり、うち 35 本はヘッドレスハーネスです(残り 5 本はアセット取得とコード生成)。 | **[プレイ](https://fatal-funnel.vercel.app)** |
| **[reasonforge](https://github.com/euuuuuuan/reasonforge-public)** | 決定論的な推論装置。8 個の MCP ツールが、スコープのロック、最も安価な反証ステップを優先する計画、盲点スイープ、そして実行証明のない主張を却下する判定ゲートを強制します。*(ツール)* | **ランタイム依存 0 件**、**33/33 のテストが通過** — そのうちの一つのテスト自体が保証になっています:ツールサーフェス全体が、ファイルシステムへの書き込みも、プロセス生成も、ネットワーク呼び出しも一切行わないこと。 | — |

---

## これらはどう作られているか

面白いのは「AI で作った」という点ではありません。モデルとリポジトリの*あいだ*に何を置いているか、です。

- **2 つのコーディングエージェントに、1 人の決定論的なレフェリー。** [`agent-relay`](https://github.com/euuuuuuan/agent-relay-public)
  は外部ドライバーで、2 つのエージェント CLI をゲートが通過するまで互いに走らせ続けます。「完了」を
  決めるのはゲートであり、エージェント自身による自己評価では決してありません。12 件のユニットテストは
  `PATH` をシステムディレクトリのみに縮めた状態で通過するため、どちらのエージェントもインストール
  しなくても、ドライバーの拒否動作を証明できます。
- **決定論的ゲートは、モデルが呼ばれる前に走ります。** 雰囲気ではなく純粋性チェッカーです:
  fatal-funnel の sim ゲートは 11 種の非決定的 API を禁止し、reasonforge の読み取り専用テストは 6 種の
  禁止パターンを強制します。検索品質もビルドゲートの一部です — ゴールデンセットとしきい値により、
  リグレッションが起きた実行は失敗します([rag-eval-demo](https://github.com/euuuuuuan/rag-eval-demo-public))。
- **UI に関する主張は、記憶ではなくプローブとスクリーンショットから。** [hollowmere](https://github.com/euuuuuuan/hollowmere-public)
  は `shot`、`relic-qa`、`mobile-qa` のハーネスを同梱しています。fatal-funnel は `tools/` の 40 本のスクリプトのうち
  35 本がヘッドレスハーネスで、加えて 4 台の固定スクリーンショットカメラを備えます。この教訓は高くつきました:
  「動いている」はずのツールチップが、入力フィルタのフラグ一つで死んでいることがあります。合成入力プローブは
  それを捕まえますが、コードを読むだけでは捕まえられません。
- **アセットは、生成される前にライセンスでゲートされます。** [assetforge](https://github.com/euuuuuuan/assetforge-public)
  は、レジストリに載っていないモデルからの生成を拒否します。その下流では、**公開済みの 27 スナップショット
  すべてが、4 段階の再配布テーブルを持つ `CREDITS.md` を備えています** — フォークする人は、
  何を取り除くべきかを正確に把握できます。
- **正直なラベルを、スキーマで強制。** [euan-portfolio](https://github.com/euuuuuuan/euan-portfolio-public)
  では、公開されるすべてのメトリクスに `kind: z.enum(['measured', 'estimated'])` という型が付きます。
  どちらであるかを宣言しない限り、数字は公開できません。このページにキリのいいマーケティング数字が
  一つもないのは、このルールのおかげです。
- **公開はパイプラインであり、コピー&ペーストではありません。** ここにある各公開リポジトリは、繰り返し
  実行可能なサニタイズ付き公開処理によって生成されます。その claims ファイルは README 内のすべての数量を
  再実行し、一つでもずれていればコミットの作成を拒否します。27 のスナップショット全体では
  **629 のテスト/スペックファイル**にのぼります。

---

## 主な作品

27 リポジトリすべてではなく、それぞれ異なる能力を示すものを選んでいます。

**ブラウザゲーム(リンクを開けばすぐ遊べます)**
- **[fatal-funnel](https://github.com/euuuuuuan/fatal-funnel-public)** — 決定論的な戦術シミュレーション、40 種の武器、12 の手作業で設計したミッション · [ライブ](https://fatal-funnel.vercel.app)
- **[hollowmere](https://github.com/euuuuuuan/hollowmere-public)** — コンパクトなアクション RPG:パリィ、レリック、2 つのゾーン。*v0.5.1 の垂直スライス* · [ライブ](https://hollowmere-two.vercel.app)
- **[neko-shift](https://github.com/euuuuuuan/neko-shift-public)** — 人員配置比率をテーマにした小さな完結型放置ゲーム、18 のテストファイル · [ライブ](https://neko-shift.vercel.app)
- **[ashglass-reliquary](https://github.com/euuuuuuan/ashglass-reliquary-public)** — アイソメトリック ARPG のスライス。**決定論的シミュレーションコアを hollowmere と共有**。戦闘ルール、エンカウンター、コンテンツは独自のものです。 · [ライブ](https://ashglass-reliquary.vercel.app)
- **[moonshard-warden](https://github.com/euuuuuuan/moonshard-warden-public)** — 3 ウェーブ制のアイソメトリックアリーナ:テレグラフを読み、mooncut を連鎖させ、振り抜かれる一撃をダッシュで躱します。**43 のテストファイル**と、このセットで最も厳格なアセット台帳 — ファイル単位の SHA-256 行が 100 件、すべて CC0。 · [ライブ](https://moonshard-warden.vercel.app)

**エンジンゲーム(Godot / Unity — 垂直スライスとプロトタイプで、商用リリースはありません)**
- **[voidclad](https://github.com/euuuuuuan/voidclad-public)** — 宇宙を舞台にした主力艦の砲撃戦:砲弾の飛翔時間、慣性を織り込んだ見越し射撃、装甲の入射角による貫通判定を、決定論的な 30 Hz シミュレーションの上で実現(27 のテストファイル)
- **[cairnfall](https://github.com/euuuuuuan/cairnfall-public)** — ソロのボスレイド ARPG、3 フェーズ制のボスが 10 体。「レイドはあなたを運ばない」という主張は、強制された不変条件ではなく戦闘後に測定される統計値です — 味方がとどめを刺せば、リザルトレポートにそう記録されます(33 のテストファイル)
- **[todak](https://github.com/euuuuuuan/todak-public)** — 画面の下端で暮らすデスクトップピクセルペット(36 のテストファイル)
- **[hordecaller](https://github.com/euuuuuuan/hordecaller-public)**(30) · **[ragtail](https://github.com/euuuuuuan/ragtail-public)**(29) · **[driftfolk](https://github.com/euuuuuuan/driftfolk-public)** + その **[Unity ポート](https://github.com/euuuuuuan/driftfolk-unity-public)** — ポートの狙いはオリジナルとの数値的パリティです · **[gatewarden](https://github.com/euuuuuuan/gatewarden-public)** · **[emberline](https://github.com/euuuuuuan/emberline-public)**
- **[tidewrack](https://github.com/euuuuuuan/tidewrack-public)** — 指揮型自律のバトルロイヤル、Unity 製。このスナップショットは**プレイ可能なスライスではなく検証スケルトン**であるため、別枠に挙げています:出荷されているのは決定論的 sim とそのゴールデンファイルハーネスであり、15 本の番号付きソークアサーションのうち 11 本はまだ未実施です。

**AI・エージェントツーリング**
- **[baton](https://github.com/euuuuuuan/baton-public)** — エージェントプラットフォーム:63 ツールの MCP サーフェス、承認ブローカー、16 のオーケストレーターパッケージ、90 の Swift ソース
- **[reasonforge](https://github.com/euuuuuuan/reasonforge-public)** — 読み取り専用の推論ハーネス、8 個の MCP ツール、依存 0 件
- **[agent-relay](https://github.com/euuuuuuan/agent-relay-public)** — 2 つのコーディングエージェント間の、ゲートで終端されるループ
- **[assetforge](https://github.com/euuuuuuan/assetforge-public)** — fail-closed なライセンスゲートの背後で行う、ローカルかつゼロコストのアセット生成
- **[content-qa-pipeline](https://github.com/euuuuuuan/content-qa-pipeline-public)** — 生成 → 判定 → 構成 → 公開。「確信が持てない」場合は出荷せず、実行を保留します

**データ・バックエンド・ドメインツーリング**
- **[officegrid](https://github.com/euuuuuuan/officegrid-public)** — テナント境界を PostgreSQL の内側に置くマルチテナント資産管理:11 のマイグレーションファイルにまたがる **82 の行レベルセキュリティ(RLS)ポリシー**と、それらを狙い撃ちする 6 つの pgTAP テストファイル — アプリケーション層のフィルタリングではありません
- **[marketing-knowledge-rag](https://github.com/euuuuuuan/marketing-knowledge-rag-public)** · **[rag-eval-demo](https://github.com/euuuuuuan/rag-eval-demo-public)** — 評価ゲートこそが主眼の検索システム
- **[crm-mcp-agent](https://github.com/euuuuuuan/crm-mcp-agent-public)** — 全合成 CDP の上に載るエージェント + ガバナンスカーネル
- **[ad-compliance-linter](https://github.com/euuuuuuan/ad-compliance-linter-public)** — 韓国の金融・保険広告コピー向けの一次チェック用リンター。同梱の 5 つのルールセットはすべてデータ自体に `"verified": false` と記されており、レポートは免責事項なしには出力できません:人間のレビューに回すためにコピーをフラグするものであり、コンプライアンスを保証するものではありません
- **[loopsmith](https://github.com/euuuuuuan/loopsmith-public)** — 楽器を演奏しても、パターンを書いても構いません。同じ曲を、両側からロスレスに編集できます(27 のテストファイル)
- **[euan-portfolio](https://github.com/euuuuuuan/euan-portfolio-public)** — すべての数字が measured か estimated かを宣言する、バイリンガル静的サイト · [ライブ](https://euuuuuuan.pages.dev)

---

## これらのリポジトリについて

27 のリポジトリはすべてサニタイズ済みのスナップショットです:コードは Apache-2.0 の下で公開され、
アセットはリポジトリごとに 4 段階の `CREDITS.md` で管理されています。ゲームは個人ビルドと
垂直スライスであり、プレイ可能で、ゲートを通し、正直にラベル付けしたものです。商用リリースではありません。

**連絡先:** ここにあるいずれかのリポジトリで issue または discussion を開いてください。
