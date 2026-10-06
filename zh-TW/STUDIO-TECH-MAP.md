# 遊戲廠商技術棧與求職技能地圖

> `[VOLATILE]` — 本文建立在徵才頁面快照上，招募週期一變內容就過期。**快照日期：2026-10-06。建議每 3 個月重跑一次。**

蒐集各大遊戲廠商分別使用什麼引擎、中介軟體與工具，並回答一個比工具清單更有用的問題：**想進這些廠商，哪些東西值得花時間練，哪些知道名字就好。**

---

## 方法

### 證據等級

| 等級 | 定義 |
|---|---|
| **A** | 官方第一手 —— 官方採用頁、技術部落格、CEDEC／GDC 講演、官方 repo |
| **B** | 可觀察事證 —— SteamDB 檔名偵測、遊戲檔案目錄、credits、中介軟體廠商客戶名單 |
| **C** | 二手轉述 —— 媒體報導、求職網站彙整。**不可單獨成立** |
| **D** | 查無 —— 留空，不推測 |

收錄門檻：每筆至少一個 A 或 B。

### 可準備性

這是本文的核心欄位。知道「CAPCOM 用 RE ENGINE」情報價值高，但求職準備價值接近零 —— 因為外面沒有任何方法去練它。

| 級別 | 意義 | 該怎麼辦 |
|---|---|---|
| 🟢 **可直接練** | 外部可取得，能做出作品 | **投資重心** |
| 🟡 **可代理練** | 自研品碰不到，但有開源對應物能展示同一種能力 | CP 值最高的一塊 |
| 🔴 **進去才碰得到** | 純內部 | 知道名字、面試能聊即可，不要花時間準備 |

### 不重建既有資料庫

- 引擎／中介軟體／SDK → 用 SteamDB `FileDetectionRuleSets`（MIT）依廠商反查
- 遊戲原始碼釋出 → 以 Wikipedia 既有清單為起點過濾，**但該清單不完整，必須逐家補查**（見第三段的方法論警告）
- 中介軟體總表 → Wikipedia 該表僅 25–30 筆且自 2017 年起標註缺乏收錄準則，值得重做

### 資料檔

完整主表（122 列，4 區 20 家 + 產業橫向共通層 + 原始碼釋出）：[`data/studio-tech-map_2026-10-06.csv`](./data/studio-tech-map_2026-10-06.csv)（UTF-8 BOM，可直接用 Excel 開啟）

---

## 一、標的概覽

目前涵蓋 4 個地區分組 20 家公司（日本 7／中國 4／台灣 11 的擴充子集／歐美 5），加一個跨廠商的「產業橫向」區塊與一個「原始碼釋出」區塊。A 級 JD 累計 14 筆，橫跨程式、技術美術、美術、動畫、音效五個職類。

台灣組已填 7 家；第三階段大廠完成 6 家（任天堂本體、疊紙仍為 D）；美術與音效管線已逐家落實到 CAPCOM、Bandai Namco、Cygames、SEGA、FromSoftware、miHoYo。

**兩個最重要的結論：**

1. **程式職的門檻練不快，技術美術職的門檻練得到。** 詳見第四段。
2. **美術職的工具門檻比想像中寬鬆 —— 日系大廠反而最寬。** CAPCOM 的角色建模師必須欄不指定任何工具[38]；FromSoftware 明寫「應徵時不問使用軟體」，入社後才用 3ds Max/ZBrush[42]；Cygames 的必須欄是「Maya、3ds Max、Softimage、**Blender** 擇一」[39]。**Blender 出現在日系大廠的必須欄，等於這條路可以零授權成本起步。** 反而是 miHoYo 點名最硬，五套軟體全要「熟練」[44]。

## 二、功能拆解

本次（2026-10-06）開啟原始頁面確認的 A 級事實：

| 公司／職缺 | 必須條件（原文重點） | 引擎在哪一欄 | 可準備性 | 來源 |
|---|---|---|---|---|
| **雷亞 Rayark**（Software Engineer, Client） | C# 或 C/C++ 或 **Rust**；物件導向或函數式；資料結構與演算法、計算機架構與作業系統、計算機網路；**4 年以上** | **加分欄**（Unity3D 或 Unreal） | 🟢 | A[1] |
| **Bandai Namco Studios**（テクニカルアーティスト） | 「**Unreal Engine のグラフや HLSL を使用したシェーダー開発経験**」「**MAYA, Houdini 等 DCC ツールを使用した実務経験**」「Unreal Engine, Unity **あるいは**内製エンジン」 | **必須欄** | 🟢 | A[2] |
| **Nintendo Systems**（グラフィックスミドルウェア開発） | 「C++ **または**デスクトップアプリケーション開発実務経験」；利用技術：C++17/20、C#（.NET 6/WPF）、Vulkan、DirectX12、MAYA | 未列為門檻 | 🟢 | A[3] |
| **SEGA / RGG スタジオ**（ゲームエンジンプログラマ） | 「C++ によるアプリケーション開発経験（**ゲームに限らず**）」 | 歓迎欄（3D 圖形 API） | 🟢 | A[4] |
| **Square Enix**（Animator） | 「DCC ツールを用いたキャラクターアニメーション制作経験」；歓迎「**Maya での簡単なリグ設定**」；**必須提交 portfolio** | — | 🟢 | A[5] |
| **Garena 台灣**（後端工程師） | CS 學位或等同經驗；C/C++ / Python / Java / Go 擇一；資料結構、演算法、資料庫 | 無引擎 | 🟢 | A[9] |
| **泥巴娛樂 NeoBards** | 台北 6 個職缺快照；Art Director 僅要求「multiple game engines」未點名 | **無任何職缺列 RE ENGINE** | — | A[6][8] |

此外，第一階段已確認：**Cysharp（Cygames）是目前查到唯一把「OSS 貢獻／技術文章發表／研討會演講」寫進歓迎スキル的公司。** 對這類公司，送 PR 或寫一篇深入分析本身就是直接命中招募條件的行為。

**兩個容易誤判的點**

1. **泥巴娛樂的 RE ENGINE 是作品事證，不是徵才條件。** 他們做過《惡靈古堡：反抗》，但官方 careers 的 6 個台北職缺沒有一個把 RE ENGINE 列為條件[6]，Material Artist 要的是 Unreal[7]。**仍然是 🔴。**
2. **Garena 台灣招的不是遊戲開發。** 職務內容是「遊戲營收活動網頁」，技術棧是 C/C++、Python、Java、Go 加 MySQL[9]。那是 Web 後端。

### 美術職：逐家對照

| 公司／職缺 | 必須欄 | 歓迎欄 | 等級 |
|---|---|---|---|
| **Cygames**（3DCG・キャラクターアーティスト） | 「**Maya、3ds Max、Softimage、Blender など**を使用した 3DCG 実務経験 **2 年以上**」 | ZBrush、Substance、Houdini、MotionBuilder | A[39] |
| **CAPCOM**（キャラクターモデラー・大阪） | 角色製作相關知識／技術／實務經驗 —— **不指定任何工具** | 「**Maya 等、複数の DCC 使用経験**」、shader／script 知識、外包管理、英文閱讀 | A[38] |
| **Bandai Namco**（テクニカルアーティスト） | UE graph／HLSL shader、**MAYA・Houdini 等 DCC**、UE／Unity 或內製引擎 | Python 工具開發、DCC／引擎 plugin、GPU／數學／物理 | A[2] |
| **Square Enix**（Animator） | DCC 工具角色動畫製作經驗 | **Maya 簡易 rig**、cutscene camera、facial animation | A[5] |
| **FromSoftware**（背景 3D アーティスト） | 地形模型／貼圖／背景資產製作 | **入社後使用 3ds MAX／ZBrush；應徵時不問使用軟體**（年収 400–800 萬円） | B[42] |
| **miHoYo**（3D 角色・次世代 PBR 建模師） | **熟練 3ds Max、MAYA、ZBrush、Substance Painter、Marvelous Designer**；熟悉 Unreal Engine；2 年以上 | 生物解剖與造型製作經驗 | C[44] |
| **Cygames**（背景アーティスト） | Maya／3ds Max／Softimage 背景製作經驗 | ZBrush sculpting、Houdini procedural、Substance Designer、PBR 材質、引擎內背景實裝 | B[40] |
| **NeoBards**（Material Artist・台北） | UE material shader 製作與 UE 工作流；3 年以上 | — | B[7] |
| **Winking Studios**（TA・上海） | 3ds Max／Maya／ZBrush、Photoshop、精通 UE4 | — | B[28] |

**模式**：ZBrush 與 Substance 幾乎永遠落在歓迎欄，但出現率極高 —— 練了有用，不是門檻。真正的門檻是「有沒有做出東西」，不是用哪套軟體。

### 音效職：逐家對照

| 公司／職缺 | 必須欄 | 歓迎欄 | 等級 |
|---|---|---|---|
| **CAPCOM**（サウンドデザイナー・効果音） | 「**DAW（Pro Tools 等）、波形編集ソフト実務使用経験**」；家用主機／電影／TV／動畫任一的效果音製作經驗 | Foley 等含錄音的效果音製作、「**Wwise などのオーディオミドルウェアの使用経験**」、サウンドディレクター／リーダー經驗 | A[36] |
| **Bandai Namco**（サウンドデザイナー・効果音） | 遊戲開發經驗、音效資產製作、「**サウンド実装スキル（UE+Wwise、CRI ADX 他）**」、Foley／MA／環繞聲、音響工程知識、音效總監或 lead 經驗 | 「ゲームオーディオミドルウェア（CRI ADX、Wwise など）」、遊戲引擎（UE、Unity）、「**ProTools、Reaper、Nuendo 等の DAW**」、音樂製作知識 | A[37] |
| **SEGA**（技術本部・サウンドデザイナー） | 1 款以上家用主機遊戲的音效開發經驗、**音訊中介軟體或遊戲引擎（UE／Unity 等）的音效實裝經驗**；想定配属：龍が如く／ソニック | — | B[41] |
| **FromSoftware**（サウンドデザイナー／コンポーザー） | 遊戲開發或影像製作 1 年以上，或 IT 業 3 年以上 | — | B[43] |

**模式**：**DAW 是必須，中介軟體多半只是歓迎。** CAPCOM 就是這個形狀 —— DAW 必須、Wwise 歓迎。Bandai Namco 是唯一的例外，把「UE+Wwise、CRI ADX」直接寫進必須欄[37]。

另外，上一版引用 GameSoundCon 統計說「Reaper 在 JD 出現率第一（39%）」[23]，當時只有第三方統計。現在 **Bandai Namco 的官方 JD 把 Reaper 與 Pro Tools、Nuendo 並列**[37]，這條有了 A 級佐證。

## 三、技術架構與相依

跨廠商共用、與單一公司無關的共通層：

| 層 | 主流品 | 性質 | 可準備性 | 等級 |
|---|---|---|---|---|
| 版控 | **Perforce Helix Core**（供應商自述「19 of top 20 AAA」[19]）／Git + LFS（獨立團隊） | 授權／開源 | 🟢 | C[19] |
| 美術 DCC | **Maya**（三家官方 JD 交叉點名[2][3][5]）、**Houdini**[2]、ZBrush[28]、Substance Painter、Marmoset、Photoshop[3][28] | 授權 | 🟢 | A／B／C |
| 掃描素材庫 | Quixel Megascans（2024-10 併入 Fab，**2025 起改逐件收費**[24][25]）；免費替代 Poly Haven／ambientCG／LotPixel（CC0 或可商用）[26] | 授權／CC0 | 🟢 | B／C |
| 音訊中介軟體 | **Wwise**（Audiokinetic，**2019-01-08 由 SIE 宣布收購**[22]）／FMOD／CRI ADX（日本第三大，歐美罕見）[20][21] | 授權 | 🟢 | A／C |
| 音訊 DAW | **Reaper 在 JD 出現率第一（39%）**，Pro Tools 33%、Nuendo／Sound Forge 各 19%、Cubase 13%[23] | 授權 | 🟢 | B[23] |
| 反作弊 | EAC（Fortnite、ELDEN RING、Apex 等）／BattlEye／Denuvo AC／騰訊 ACE[27] | 授權／自研 | 🟡／🔴 | B[27] |

**Maya 的地位有 A 級證據支撐，不只是媒體說法** —— 三家不同國家的官方 JD（Bandai Namco 必須欄、Nintendo Systems 利用技術、Square Enix 歓迎欄）同時點名它[2][3][5]。

**授權與商用限制**：🟢 工具裡，Blender、Git LFS、Poly Haven／ambientCG（CC0）零成本；Unity Personal、Unreal、Houdini Apprentice、Wwise、FMOD 皆有免費或低門檻的個人／學習層級；Maya、ZBrush、Substance、Pro Tools 需付費訂閱。**各家具體授權門檻與價格未逐一驗證，實際採購前需自行確認。**

需要留意的授權變化：**Megascans 從 2025 年起不再免費**[24][25]。2024 年底前領過 Fab 全庫的人等於鎖住了一整套掃描素材；沒領到的人現在要逐件付費，或改用 CC0 庫。

### 遊戲本體原始碼釋出

結論先講：**這 20 家裡，「官方主動、附授權條款、完整釋出遊戲本體原始碼」只有 EA 一家一例。**

| 公司 | 標的 | 性質 | 可準備性 | 等級 |
|---|---|---|---|---|
| **EA** | **Command & Conquer: Tiberian Dawn + Red Alert**（2020-06） | **官方釋出，GPLv3** | 🟢 | B[45] |
| Epic Games | ZZT（1991，Potomac Computer Systems） | 官方釋出，MIT（2023） | 🟢 | B[46] |
| CAPCOM | REDox（`CAPCOM-TD-OSS` 唯一公開 repo） | 官方開源 Apache-2.0，**引擎元件非遊戲** | 🟡 | A[47] |
| Ubisoft | Sharpmake（C# 專案產生器，AC3 2011 起內部使用）、Massgate（World in Conflict 線上後端）、Chroma（色盲模擬） | 官方開源，**工具與後端為主** | 🟡 | B[48] |
| Square Enix | 無官方釋出 —— 且**部分舊作原始碼已遺失**，FF Tactics 重製時靠分析既有版本重建 | — | — | B[49] |
| Epic／EA／任天堂／SEGA | Gears of War、B.O.B.、FIFA 97（誤放 demo disc）、F.A. Premier League Stars、FIFA 21（2021 勒索軟體外洩）、Donkey Kong（Atari 8-bit 組語）、SEGA 街機與開發機回收碼 | **外流、誤放或第三方檔案保存，不是釋出** | 🔴 | B[46] |
| Bandai Namco／FromSoftware／Cygames／Riot／騰訊／網易／miHoYo／Embark | 查無 | — | — | D |

**C&C 的細節要講清楚**：釋出的是可編出 game DLL 的那一份，**不含美術資產，也不含引擎本體**[45]。它是給模組開發者用的，不是一份可直接重建遊戲的完整程式碼。

**外流 ≠ 釋出。** 表中標 🔴 的那一列全部是外流、誤放或第三方上傳。它們在法律上不是可用的學習素材，拿來放進作品集或商用專案有風險。這一欄特地分開列，就是為了不要讓人誤用。

> **方法論警告**：這份調查原本的計畫是「原始碼釋出直接用 Wikipedia 既有清單過濾，不重建資料庫」。**這個捷徑有破口 —— 最重要的那一筆（C&C）不在 Wikipedia 的兩份清單上任何一份裡**[45][46]。清單可以當起點，但不能單獨採信。

## 四、使用情境

以下情境的對象，是一位有 Unity 實務經驗、以日系或台灣大廠為目標的工程師。

1. **有 Unity 經驗但沒有 C++ 主機經驗 → 用 SEGA RGG 與 Nintendo Systems 的「不限遊戲」條款切入**[3][4] → 省下的是「先去累積三年主機 C++ 年資」這整段前置，因為它們的必須欄接受非遊戲的 C++／桌面應用經驗。
2. **想找一條靠練習就能命中必須條件的路 → 轉／兼技術美術**[2] → 省下的是等年資。TA 的必須欄是 UE + HLSL + Maya + Houdini，四樣全部外部可取得；程式職的必須欄是年資與 CS 基礎，練習無法加速。
3. **想做 AAA 但不離開台灣 → 本土自研廠碰不到 AAA 管線 → 進泥巴娛樂或唯晶科技接大廠專案**[6][28] → 省下簽證與搬遷成本，代價是做的是共同開發／外包段落而非主導。
4. **想轉遊戲音效、不確定先買哪個 DAW → 先裝 Reaper 而不是 Pro Tools**[23] → 省下 Pro Tools 的訂閱費與學習時間，而 JD 出現率反而更高（39% vs 33%）。
5. **美術／TA 轉職者不確定素材庫要不要花錢 → 用 Poly Haven／ambientCG 的 CC0 庫做作品集**[26] → 省下 Megascans 自 2025 年起的逐件費用[24][25]，且 CC0 無授權風險。

## 五、競品／替代方案

四條準備路徑的比較：

| 方案 | 定位 | 相對優勢 | 相對劣勢 |
|---|---|---|---|
| **A. 深化 Unity + 加練 TA** | 從現職往外延伸 | 唯一能靠練習直接命中大廠「必須」欄的路徑[2]；Maya／Houdini／HLSL 有作品集可展示 | TA 是獨立職類，不是 Unity 工程師的自然升級；需要美術感 |
| **B. 補 C++ 走主機／引擎程式** | 換語言換跑道 | SEGA、Nintendo Systems 接受非遊戲 C++ 經驗[3][4]，門檻比想像低 | 要與科班 C++ 背景者競爭年資；日文通常另成門檻 |
| **C. 進代工廠（泥巴／唯晶）** | 用在地職缺換 AAA 管線經驗 | 不用簽證就碰得到 UE5 AAA 專案與大廠美術標準[6][28] | 做的是專案段落；自研引擎（RE ENGINE）仍碰不到[6] |
| **D. OSS 貢獻路線** | 用公開產出代替年資 | Cysharp 是目前查到唯一把 OSS 貢獻／技術文章／演講寫進歓迎スキル的公司，命中率明確 | 只對極少數公司有效；產出到被看見的週期長 |

**🟡 代理練習對照表** —— 自研品碰不到，但有開源對應物能展示同一種能力：

| 想證明的能力 | 對應的大廠內部技術 | 可練的開源代理品 |
|---|---|---|
| 高效序列化／記憶體配置控制 | CAPCOM RE:Dox | REDox 本身已開源（Apache-2.0）、Cysharp **MemoryPack** |
| Unity 非同步／無配置寫法 | 各家自研 Unity 框架 | Cysharp **UniTask** |
| 即時連線遊戲後端 | 各家自研 gameserver | Cysharp **MagicOnion** |
| 即時渲染／GI | 各家自研渲染器 | Embark **kajiya**（Rust + Vulkan） |
| 遊戲伺服器網路基礎設施 | 各家自研 | Embark **quilkin**（UDP proxy for game servers） |

## 六、風險與限制

**來源衝突（4 件，皆未裁決）**

1. **唯晶科技基本資料自相矛盾。** 一說「上海總部／宏碁子公司／亞洲第三大／25 年經驗」；另一說「**新加坡總部**／全球最大外部開發工作室之一／**1,400+ 員工 14 個工作室**／20 年以上經驗」[28][29]。兩說並存，不裁決。
2. **Audiokinetic 收購年份。** 一筆二手比較文章稱「2024 年 5 月 SIE 收購 Audiokinetic」；Gematsu 與 Sony 公告為 **2019-01-08 宣布、預計 2019-01-31 完成**[22]。**以 2019 為準**，該二手文章有誤。
3. ~~**CAPCOM 音效中介軟體（ADX2 vs Wwise）未裁決。**~~ **已裁決，傾向 Wwise。** CAPCOM 自家官方採用頁的サウンドデザイナー（効果音）職缺，歓迎欄明寫「**Wwise などのオーディオミドルウェアの使用経験**」，**同一份 JD 完全沒有提到 CRI ADX**[36]。這是目前唯一的 A 級第一手來源，指向 Wwise。
   **但不要過度解讀**：這是一份 JD，而且 Wwise 落在歓迎欄不是必須欄。JD 沒提 ADX2，不等於證明 CAPCOM 從未使用過 ADX2 —— 只能說「唯一的第一手證據指向 Wwise」。原本的 C 級 ADX2 說法在沒有新證據前不再採信。
4. **「台灣進 AAA 的主流路徑是代工，不是自研」屬推論**，無產業統計佐證，尚未被證實也未被推翻。
5. **Wikipedia 的原始碼清單不完整**（新發現）。EA 的 C&C 原始碼釋出是這 20 家裡最重要的一筆，卻不在 Wikipedia 兩份清單中的任何一份[45][46]。依賴該清單過濾會整筆漏掉。

**空白（D 級，不推測）**

- 任天堂**本體**採用頁未取得（已取得的是子公司 Nintendo Systems，不等同）[3]
- 疊紙 Papergames 技術棧查無[30]；SIGONO、宇峻奧汀、大宇、智冠、赤燭的公開職缺查無
- **台灣廠商的美術與音效職缺尚未逐家落實** —— 目前只有 NeoBards 與 Winking 兩筆，雷亞、IGS、赤燭的美術／音效 JD 未取得
- 騰訊、網易、EA／DICE、Epic、Riot、Ubisoft 的**美術與音效**職缺未查（本輪音效只覆蓋日本四家）
- 104（HTTP 402）、EA ATS（HTTP 403）、SEGA 音效 JD 原頁（404）被擋，該些只能停在 B 級

**其他風險**：主機獨佔作品無 Steam depot 可偵測，該區塊預期整片 D；JD 內容隨招募週期變動，所有條目已標快照日期。

## 七、延伸實作規劃

**MVP 範圍**

做什麼：
- 把 CSV 主表接成一個可篩選的單頁（依「可準備性」「地區」「職類」三軸過濾）
- 補完台灣剩餘 4 家（SIGONO、宇峻奧汀、大宇、智冠）與疊紙的技術棧
- 針對 🟢 清單，逐項標出「可免費／低成本取得的個人授權層級」並實際驗證一次
- 產出一份「TA 轉職 90 天練習清單」，對齊 Bandai Namco 必須欄的四項工具

明確不做什麼：
- 不逐家補齊歐美與中國廠商的美術／音效 JD —— 對台灣求職者可及性低，不在 MVP
- 不做薪資分析與面試題庫
- 不重建中介軟體總表（留到主表穩定後再議）
- 不碰主機獨佔作品的中介軟體偵測（無資料來源）

**里程碑**

| 階段 | 產出物 | 粗估人天 |
|---|---|---|
| M1 資料補完 | 台灣 4 家 + 疊紙 + 任天堂本體的技術棧，主表補到 20/20 家 | 3 |
| M2 授權驗證 | 🟢 清單逐項的個人授權層級與成本，已實際開頁確認 | 2 |
| M3 可篩選單頁 | 三軸過濾的 HTML 主表（讀 CSV） | 3 |
| M4 TA 練習清單 | 90 天練習清單 + 作品集驗收點 | 2 |
| M5 衝突裁決 | 唯晶基本資料與 CAPCOM 音效中介軟體兩案的結案或正式標為無解 | 1 |

## 八、參考來源

1. Rayark 雷亞 — Software Engineer, Client（Yourator 公司張貼，已開啟原頁確認）— https://www.yourator.co/companies/rayark/jobs/35007
2. 株式会社バンダイナムコスタジオ — テクニカルアーティスト（エンジニア部門），HRMOS 官方採用頁 — https://hrmos.co/pages/bandainamcostudios/jobs/0000015
3. ニンテンドーシステムズ株式会社 — グラフィックスミドルウェア開発エンジニア，HERP 官方採用頁 — https://herp.careers/v1/nscareer/4XQZ69T5cGm6
4. 株式会社セガ —【RGGスタジオ】ゲームアプリケーションプログラマ・ゲームエンジンプログラマ，HRMOS 官方採用頁 — https://hrmos.co/pages/sega/jobs/27148
5. 株式会社スクウェア・エニックス — Animator モーションデザイナー，HRMOS 官方採用頁 — https://hrmos.co/pages/square-enix/jobs/110104001
6. NeoBards Entertainment — Careers（官方職缺列表）— https://neobards.com/careers/
7. NeoBards — Material Artist [Taipei, Taiwan] — https://neobards.com/?p=15621
8. NeoBards — Art Director [Taipei, Taiwan] — https://neobards.com/category/job/art
9. Garena 台灣 — 後端工程師 Back End Engineer（Yourator 公司張貼，已開啟原頁確認）— https://www.yourator.co/companies/garena/jobs/13018
10. 鈊象電子 — Unity 遊戲開發工程師（104；原頁 HTTP 402，內容為搜尋摘要）— https://www.104.com.tw/job/6g7ck
11. 鈊象電子股份有限公司 — Cake 公司職缺頁 — https://www.cake.me/companies/igs?locale=en
12. セガ 各職種求人（careerconnection 轉載）— https://careerconnection.jp/job/160859-detail
13. Bandai Namco is still working on new in-house game engine — AUTOMATON — https://automaton-media.com/en/news/bandai-namco-is-still-working-on-new-in-house-game-engine-update-reveals/
14. Bandai Namco is developing its own game engine — 80.lv — https://80.lv/articles/bandai-namco-is-developing-its-own-game-engine/
15. 暗黑手游将采用网易自研引擎 Messiah（NeoX／Messiah 沿革）— 17173 — https://news.17173.com/content/11042018/142843117.shtml
16. Tencent — UE4 Senior Game Client Architect（Hitmarker 轉載）— https://hitmarker.net/jobs/tencent-ue4-senior-game-client-architect-712877
17. Tencent Games is Hiring（technical-art 討論串；TA 職缺細節僅為搜尋摘要，未開原頁）— https://tech-artists.org/t/tencent-games-is-hiring-animation-engineer-in-europe-open-from-senior-level-to-principal-level/15950
18. Software Engineer — Frostbite Engine Tools，EA Careers（原頁 HTTP 403，內容為搜尋摘要）— https://ea.gr8people.com/jobs/168293/software-engineer-frostbite-engine-tools
19. Perforce Helix Core for Game Development（「19 of top 20 AAA」為**供應商自述**）— https://www.perforce.com/
20. Wwise vs FMOD vs MetaSounds: Choosing Audio Middleware for Your UE5 Game in 2026 — Strayspark Studio — https://www.strayspark.studio/blog/wwise-fmod-metasounds-audio-middleware-comparison
21. Game Audio Middleware in 2026 — A Deep Dive — youngju.dev — https://www.youngju.dev/blog/culture/2026-05-16-game-audio-middleware-2026-wwise-sony-fmod-steam-audio-resonance-tempest-3d-metasounds-deep-dive.en
22. Sony Interactive Entertainment acquires audio technology provider Audiokinetic（2019-01-08）— Gematsu — https://gematsu.com/2019/01/sony-interactive-entertainment-acquires-audio-technology-provider-audiokinetic
23. Game Audio Job Skills: How to Get Hired as a Game Sound Designer（DAW 在 JD 的出現率統計）— GameSoundCon — https://www.gamesoundcon.com/post/game-audio-job-skills-how-to-get-hired-as-a-game-sound-designer
24. Epic Games has made Megascans free to all, but only until the end of 2024 — CG Channel — https://www.cgchannel.com/2024/10/epic-games-has-made-megascans-free-to-all-but-only-until-the-end-of-2024/
25. Megascans no longer free after 2024 — 80.lv — https://80.lv/articles/megascans-no-longer-free-after-2024/
26. Free PBR Textures, Materials and HDRIs for Games (2026)（Poly Haven／ambientCG／LotPixel 等 CC0 庫）— https://app.cinevva.com/guides/free-textures-hdris-materials
27. Easy Anti-Cheat vendor game list — GamingOnLinux — https://www.gamingonlinux.com/anticheat/vendor/easy-anti-cheat/
28. Winking Studios Limited — SGX IPO prospectus（公司規模與據點）— https://links.sgx.com/1.0.0/ipo-prospectus/3392
29. Winking Studios — LinkedIn 公司頁（「22 of top 25 publishers」為**官方自述**）— https://linkedin.com/company/winkingstudios
30. 叠纸游戏 — 萌娘百科 — https://zh.moegirl.org.cn/%E5%8F%A0%E7%BA%B8%E6%B8%B8%E6%88%8F
31. CRIWARE 採用タイトル数（**CRI 自述**，2013-07 時點 2,595 作）— GameBusiness.jp — https://www.gamebusiness.jp/article/2013/07/23/8294.html
32. Hedgehog Engine — Sonic Retro — https://info.sonicretro.org/Hedgehog_Engine
33. Best 3D Software（美術 DCC 彙整）— The Rookies — https://www.therookies.co/blog/resources/best-3d-software
34. 遊戲橘子 Gamania 職缺列表 — Yourator — https://www.yourator.co/companies/gamania
35. Next-gen Square Enix engine（Luminous Studio 不對外授權）— MCV/Develop — https://www.mcvuk.com/next-gen-square-enix-engine-direct-x-11-native/
36. 株式会社カプコン — サウンドデザイナー（ゲーム開発／効果音），HRMOS 官方採用頁 — https://hrmos.co/pages/capcom/jobs/01_04_020
37. 株式会社バンダイナムコスタジオ — サウンドデザイナー（効果音），HRMOS 官方採用頁 — https://hrmos.co/pages/bandainamcostudios/jobs/0000050
38. 株式会社カプコン —【大阪勤務】キャラクターモデラー，HRMOS 官方採用頁 — https://hrmos.co/pages/capcom/jobs/01_03_010
39. Cygames グループ —【CySphere】3DCG／キャラクターアーティスト，HRMOS 官方採用頁 — https://hrmos.co/pages/cygames/jobs/0000335
40. Cygames — 3DCG 背景アーティスト（RECRUIT DIRECT SCOUT 轉載）— https://directscout.recruit.co.jp/job_descriptions/3078784
41. 株式会社セガ — 技術本部 サウンドデザイナー（原頁 404，內容為搜尋摘要）— https://hrmos.co/pages/sega/jobs/27902
42. フロム・ソフトウェア — 背景 3D グラフィックアーティスト（careerconnection 轉載）— https://careerconnection.jp/job/142784-detail
43. フロム・ソフトウェア 中途採用説明会（サウンドデザイナー／コンポーザー 應募條件）— https://cgworld.jp/regular/from1.html
44. 米哈游 3D 角色（次世代 PBR）建模師 招聘要求（鳳凰網科技轉載）— https://tech.ifeng.com/c/8c4NETVA3tL
45. EA releases source code for Command & Conquer Remastered Collection（2020-06，GPLv3）— Slashdot／DSOGaming — https://games.slashdot.org/story/20/06/06/1948222/ea-releases-source-code-and-4k-remasters-for-two-command-and-conquer-games ；repo — https://github.com/electronicarts/CnC_Remastered_Collection
46. Wikipedia — List of commercial video games with available source code／with later released source code — https://en.wikipedia.org/wiki/List_of_commercial_video_games_with_available_source_code ；https://en.wikipedia.org/wiki/List_of_commercial_video_games_with_later_released_source_code
47. CAPCOM-TD-OSS GitHub 組織公開 repo 列表 — https://github.com/orgs/CAPCOM-TD-OSS/repositories
48. Ubisoft 開源專案（Sharpmake／Massgate／Chroma）— https://github.com/ubisoft
49. Square Enix 舊作原始碼遺失（Final Fantasy Tactics 重製）— Gameranx — https://gameranx.com/updates/id/550595/article/square-enix-didnt-exactly-lose-final-fantasy-tactics-source-code-its-stranger-than-that/

---

**查證方式說明**：2026-10-06 共執行 40 次網路搜尋與 26 次原始頁面抓取，成功開啟並確認原頁 16 筆，列為 A 級。104（HTTP 402）、EA ATS（HTTP 403）、SEGA 音效 JD 與 NeoBards 部分子頁（404）抓取失敗，相關內容停在 B 級並已標註。「技術美術是唯一可靠練習命中的路徑」為依 A 級 JD 比對後的**推論**，非來源直述。CAPCOM 音效中介軟體一案依單一第一手 JD 裁決，裁決理由與其侷限已寫在第六段。
