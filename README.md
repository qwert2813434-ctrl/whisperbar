# WhisperBar — 本機語音輸入（給 AI 蓋的規格書）

> 這個 repo **沒有程式碼**。它是一份施工圖：把整包丟給你的 AI（Claude Code / Codex / Cursor 都行），
> 它會照著蓋出一個能跑的版本，跑在你自己的機器上、用你自己的詞彙。
>
> **English:** No source here. This is a build spec dense enough that a coding agent can implement
> a full local push-to-talk dictation tool from scratch. Read `BUILD.md`.

## 蓋出來的東西長怎樣

按一下 Fn 開始講，再按一下送出。語音在**你自己的機器上**轉成文字，
交給一個常駐的語言模型清成「打字會打的樣子」，直接貼進游標所在的輸入框。

- **全程本機轉譯**：Whisper（MLX，Apple Silicon）常駐記憶體，錄音不上傳
- **清稿層**：去贅詞、去口吃、把改口的前半句丟掉、補標點、依話題分段、該條列就條列
- **辭典**：你講的專有名詞會被聽錯，`錯=對` 一行硬性替換，不靠模型猜
- **辭典會學**：在歷史面板改一個字，它 diff 出你改了哪個詞，問你要不要記起來
- **邊錄邊轉**：錄音中每 8 秒先轉一段，放開只轉尾巴 → 放開後等待跟你講多久幾乎無關
- **回音消除**：喇叭正在放的聲音從麥克風訊號扣掉，一邊放影片一邊講也不會錄進去

我自己拿它做什麼：我有閱讀障礙，講比打快很多。整天的指令、筆記、訊息都用講的。

## 怎麼叫 AI 蓋

把這個資料夾放進一個空專案，然後跟你的 agent 說：

```
讀 BUILD.md，照第 0 到第 8 階段依序蓋。
每階段做完先跑 ACCEPTANCE.md 對應那節的驗收，過了才准做下一階段。
遇到 PITFALLS.md 提到的症狀先查那份，別自己重推。
```

**階段 1–4 先蓋完就已經能用了**（命令列版）。選單列 App 是階段 5，邊錄邊轉是階段 6，都可以晚點再說。

## 你要自己準備的

| 需要 | 說明 |
|---|---|
| Apple Silicon Mac | MLX 只跑 Apple Silicon；Intel／別的平台請把轉譯層換成 `whisper.cpp`，規格其他部分不用動 |
| ~8 GB 空間 | Whisper 模型權重 |
| macOS 13+、Xcode CLT | 只有選單列 App 需要 |
| 一個清稿用的 LLM | 本機小模型（快、免費）或你已經在付的 CLI／API。見 `SPEC.md`「清稿層是可換的」 |

## 授權與界線

- 這份規格書 MIT（`LICENSE`）
- 這份 repo **不含任何錄音、任何轉譯稿、任何辭典內容、任何測試題庫**。
  那些東西必然帶著我的客戶名、專案名、私人對話——你也一樣，所以：
  **你蓋出來的版本，資料留在你自己機器上，不要往 repo 裡放。** `.gitignore` 已經先幫你擋掉了
- 規格裡不含、也請你不要用任何商業 App 的資料庫、錄音或輸出當測試集或訓練材料。
  `ACCEPTANCE.md` 教你怎麼用**自己的錄音**建一份夠用的考卷
- 模型權重的授權看各自 repo（Whisper 系列多為 MIT/Apache），我不轉散布

## 檔案

```
BUILD.md        施工順序（給 AI 讀的主文件）
SPEC.md         契約：HTTP 端點、守門規則、清稿提示、參數表
PITFALLS.md     二十幾個靜默地雷，這份最值錢
ACCEPTANCE.md   每階段的驗收＋怎麼自建考卷
CLAUDE.md       agent 一進 repo 先讀的東西
```
