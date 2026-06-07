# RunLight《溯光》— 專案記憶與開發筆記

> 這份文件是專案的背景、技術決策與進度紀錄,跟著 repo 走,方便任何人(或 AI 助手)快速接續開發。
> 最後更新:2026-06-07

---

## 一、專案是什麼

**RunLight**(中文名《溯光》,副標 *VERSO — Find the Light Within*)是一款
**敘事解謎 × 心理探索 × 情感療癒** 遊戲。專案名以 **RunLight** 為主。

- **主題**:主角簡程熠(26 歲,左臉有胎記)工作中昏倒後,意識進入由情緒碎片構成的「內心世界」,
  經歷五個情緒關卡(**哀、怒、樂、喜、自我**),核心是「自我認同」。三種結局,**無失敗設計**。
- **平台**:PC(優先)/ 主機(次要)。目標 Steam / itch.io。
- **規模**:4～8 人小型獨立團隊,預估工期約 12 個月。

設計文件在 repo 根目錄:
- `溯光_遊戲設計企劃書.pdf`
- `溯光_開發指南.pdf`(**技術聖經**,以此為準)

> 本機無 poppler / python,要讀這兩份 PDF 需用 **Word COM** 轉文字
> (開 Word → `Document.Content.Text` → 以 UTF-8 寫出;直接存 ANSI 會變亂碼)。

---

## 二、技術選型(出自開發指南)

| 類別 | 選用 | 狀態 |
|---|---|---|
| 引擎 | Unity 6 LTS(6000.4.9f1) | ✅ 已裝 |
| 渲染 | URP(2.5D 光影、Bloom/Vignette) | ✅ 已裝 |
| 輸入 | 新版 Input System | ✅ 已裝 |
| 對話 | **Yarn Spinner**(`.yarn` 劇本) | ❌ 待裝 |
| 動畫 | DOTween Pro($15) | ❌ 待裝 |
| 音樂 | FMOD(動態音樂) | ❌ 待裝 |
| 鏡頭 | Cinemachine | ❌ 待裝 |
| 多語 | Unity Localization | ❌ 待裝 |

> 開發指南把遊戲定位成融合《槍彈辯駁》機制(非停論戰 / 言彈辯論、2.5D 渲染)。
> 這與企劃書「無動作挑戰」的描述有張力,**以開發指南為準**。

**C# 命名空間** 統一用 `RunLight`。

---

## 三、資料夾結構(開發指南指定,底線開頭)

```
Assets/
  _Scenes/      場景(按章節分)
  _Scripts/     程式(按系統分:Core / SceneManagement / Dialogue / Flags / Save / Identity)
  _Art/         美術(Characters / Backgrounds / UI / VFX)
  _Audio/       音訊(BGM / SFX / Voice)
  _Yarn/        .yarn 對話腳本
  _Prefabs/     預製物件
  _Settings/    渲染 / 輸入 / 本地化設定
```

---

## 四、開發路線圖(開發指南 5.1)

- **P0(第 1 月)**:場景管理器、對話系統(Yarn)、事件旗標系統、存檔系統
- **P1(2～3 月)**:角色立繪管理、調查模式、證據資料庫(ScriptableObject)、自我認同值系統
- **P2(4～6 月)**:辯論小遊戲(言彈)、2.5D 渲染、FMOD、CG 演出
- **P3(7～9 月)**:Cinemachine、Spine/Live2D、UI 潤色、音效、多語言

---

## 五、目前進度(2026-06-07)

### 已完成並推上 GitHub
- ✅ git 版控 + Unity `.gitignore` + `.gitattributes`(LFS:圖片/音訊/模型/字型)
- ✅ 開發指南指定的資料夾結構
- ✅ **P0 地基系統**(純 C#,共 ~674 行),位於 `Assets/_Scripts/`:

| 檔案 | 作用 |
|---|---|
| `Flags/FlagSystem.cs` + `FlagData.cs` | 全域旗標(bool/int/float/string)+ 變動事件 + 序列化 |
| `Identity/IdentityMeter.cs` + `Ending.cs` + `EmotionDimension.cs` + `IdentityData.cs` | 五情緒面向隱性認同值、正規化、三結局判定 |
| `Save/SaveSystem.cs` + `SaveData.cs` | JSON 多存檔槽、原子寫入(先寫 .tmp 再覆蓋) |
| `Core/GameManager.cs` | 跨場景單例,串接旗標/認同值/記憶碎片/存讀檔 |
| `Core/Singleton.cs` | 泛型 MonoBehaviour 單例基底 |
| `Core/FoundationSmokeTest.cs` | Play 模式一鍵驗證元件(正式版可移除) |

#### 三結局判定邏輯(`IdentityMeter`)
- 每面向上限 100,正規化 = 總和 ÷ (面向數 × 100)
- 比例 ≥ 0.80 且全收集碎片 → **Verso(溯光)**
- 比例 ≥ 0.50 → **Daybreak(破曉)**
- 其餘 → **Glimmer(微光)**

### 驗證方式
1. 開 Unity 編譯,確認 Console 無紅字錯誤
2. 場景建空物件 → Add Component → `FoundationSmokeTest` → 按 Play
3. Console 每行括號內有「應 X」預期值,全符合即通過

### 下一步
- ⏭️ **對話系統**:先在 Unity 裝 **Yarn Spinner**(Package Manager → Add package from git URL),
  裝好後接上對話樹,讓選擇寫入旗標、影響認同值。

---

## 六、GitHub 與認證(重要)

- **Repo**:`https://github.com/claire20081124-blip/RunLight`
- **正確帳號**:`claire20081124-blip`(repo 擁有者)
- **陷阱**:本機 Git Credential Manager 預設會用 Edge 裡登入的**錯誤帳號 `AnthonyFuFu`** 靜默認證 → 推送 403
- **解法(已全域設定)**:`git config --global credential.gitHubAuthModes device`
  改用**裝置代碼模式** → 推送時跳出含代碼的視窗 → **手動開 Chrome**(登入 claire 的)
  前往 `github.com/login/device` 輸入代碼授權,繞開 Edge
- 若又登錯:`printf "protocol=https\nhost=github.com\n\n" | git credential-manager erase` 清掉再重試

---

## 七、待辦提醒

- ⚠️ 開 Unity 後會自動產生 `.meta` 檔,**記得一併提交**(GUID 才會穩定,避免團隊協作衝突)。
