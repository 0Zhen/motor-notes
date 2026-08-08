# 2026-08-09 motor-notes 功能更新 pilot：首頁「最近更新」+ footer 修正

對應待辦：`agent-dev-backlog.md` 中「motor-notes 功能更新（對應 life-os issue #5 / O-A KR3）」。
Branch: `agent/motor-notes-update-20260809`（從 `v4` 分出）。

## Proposer 完整輸出

### 一、Repo 型態確認

這是 Quartz v4（jackyzha0 開發的靜態網站產生器，用於發佈「digital garden」/知識庫）的一個完整 fork，不是自建工具鏈：

- `package.json` 的 `name` 仍是 `@jackyzha0/quartz`，`repository` 欄位也還指向上游 `jackyzha0/quartz.git`。
- Git log 前段（`59b5807`、`9576701`）是上游 Quartz 引擎本身的 bugfix commit，之後才開始是使用者自己的知識點新增 commit。
- `docs/` 整個資料夾是上游 Quartz 的產品文件（不是這個 repo 專屬的維護規則）。`.github/pull_request_template.md` 也是上游模板。
- 沒有找到任何這個 repo 專屬的 CONTRIBUTING/維護規則文件，改動範圍主要受限於「Quartz 本身的架構慣例」。

**內容組織方式**：`content/` 下依主題分類（電磁設計、繞線、材料、絕緣系統、散熱設計），每個分類有 `index.md` 導覽頁，底下是獨立知識點 `.md` 檔（共 80 篇非索引筆記）。每篇筆記有固定 frontmatter + 結構化段落，用 wikilink 手動連到其他筆記。全站共 91 個檔案含 wikilink、277 個連結。

**已內建、已啟用的功能**：Search、Darkmode、ReaderMode、Graph（本地+全域）、Backlinks、Explorer、TableOfContents、Breadcrumbs、TagList、TagPage/FolderPage、RSS+Sitemap、SEO、Mermaid（內建支援但未使用）。

**CI/部署機制**：目前分支是 `v4`。`.github/workflows/deploy.yml`：push 到 `v4` 就會 `npx quartz build` 並部署到 GitHub Pages，沒有額外 staging 關卡。`.github/workflows/ci.yaml` 和 `build-preview.yaml` 都有 `if: github.repository == 'jackyzha0/quartz'`，在 `0Zhen/motor-notes` 這個 repo 上完全不會執行——PR 不會自動跑 `npm run check` 或 build 驗證，驗證責任落在人手動執行。

沒有發現任何金錢/交易/資料庫/公司機密內容。

### 二、發現的三個具體缺口

1. **Footer 連結是上游殘留**：`quartz.layout.ts` 裡 footer 連到 `github.com/jackyzha0/quartz` 跟 Quartz 官方 Discord，跟這個知識庫無關。
2. **`RecentNotes` 元件完全沒被使用**：元件存在、已 export，但 `quartz.layout.ts` 從未使用。首頁是手動維護的分類連結清單，看不出「最近更新了什麼」。
3. **`馬達` tag 掛在全部 80 篇筆記上（100% 覆蓋率）**：稀釋知識圖分群效果（`Component.Graph()` 用預設值 `showTags: true`）。

### 三、候選方案

- **方案 A**：修正 footer 連結。極小改動，極低風險。
- **方案 B（最推薦）**：首頁加 `Component.RecentNotes({ title: "最近更新", limit: 5, showTags: true })`。對應「近期每次 commit 都是新增知識點節點」的實際維護模式；現成內建元件；風險低。
- **方案 C**：`Component.Graph()` 加 `removeTags: ["馬達"]`。價值高但性質偏「優化/移除」，建議留到下一輪先確認 tag 語意定位。

### 四、Proposer 最終推薦

方案 B，可選搭配方案 A 一起做（改的是同一檔案不同區塊，互不衝突）。方案 C 留到下一輪。

## Critic 完整輸出

### 逐項核對結果

| Proposer 宣稱 | 核對結果 |
|---|---|
| Quartz v4 fork，package.json 指向上游 | 正確 |
| Footer 連結是上游殘留（9-14 行） | 正確，逐字核對一致 |
| RecentNotes 存在、已 export（21、47 行） | 正確 |
| 馬達 tag 100% 覆蓋（80/80） | 重跑後數字完全一致：馬達 80、電磁設計 23、矽鋼片 16、繞線 13、磁鐵 13、馬達拓樸 6、控制策略 5、絕緣系統 4、標準 1 |
| CI 在此 repo 不會跑 | 結論正確，但行號有誤（實際在第 14 行，非第 11 行）。額外發現：`deploy.yml` 本身完全沒有 `npm run check`/`npm test` 步驟，push 到 v4 就直接建置部署 |

### Critic 自行查出、Proposer 沒講清楚的關鍵問題

1. **首頁跟其他筆記頁共用同一個 layout 物件**：`quartz.layout.ts` 沒有獨立首頁 layout，`contentPage.tsx` 只排除「資料夾 index」跟 `tags/*`，根目錄 index（首頁）跟其他 80 篇筆記走一樣的 `defaultContentPageLayout`。若直接把 `RecentNotes` 塞進該 layout 陣列，會讓**每篇筆記頁都出現「最近更新」**，不只首頁。需要用 `ConditionalRender` 包住，用 `page.fileData.slug === "index"` 做條件。

2. **`RecentNotes` 預設 `filter: () => true`，會混入首頁自己跟分類導覽頁**：用 git log 實測驗證，若不設 filter，`limit: 5` 排序結果裡很可能包含 `content/index.md` 自己跟資料夾 index 頁，稀釋「最近更新的知識點」的實際效果。必須加 `filter: (f) => f.slug !== "index" && !f.slug?.endsWith("/index")`。

3. CSS：`ConditionalRender.css` 會轉發被包住元件的 CSS，走既有 emitter 機制自動收集，不需手動處理。

4. 中文渲染：`zh-TW.ts` 已有 `recentNotes` i18n key，機制跟已在生產環境運作的 Graph/Search 相同，無特別風險。

5. Footer 目標網址：從 `git remote -v` 取得 repo 網址，從 `content/index.md` 既有的「回到主站 0箴」連結取得主站網址，維持站內用語一致。

6. 方案 C 的 `removeTags` API 用法 Proposer 寫錯（巢狀在 `localGraph`/`globalGraph` 底下，非頂層），記錄供下一輪參考。

### Critic 最終判斷

同意 Proposer 整體推薦（方案 B + 方案 A，方案 C 留到下一輪），但方案 B 規格需修正兩點：`ConditionalRender` 包住 `RecentNotes` 只在首頁渲染、加 `filter` 排除所有 index 頁。

## 實作紀錄：與 Critic 規格的差異

Critic 給的規格是把 `afterBody` 欄位加到 `defaultContentPageLayout`（型別 `PageLayout`）。**實作時發現這個規格有型別錯誤**：`quartz/cfg.ts` 定義 `PageLayout = Pick<FullPageLayout, "beforeBody" | "left" | "right">`，這個型別根本沒有 `afterBody` 這個 key，`export const defaultContentPageLayout: PageLayout = { ..., afterBody: [...] }` 這種寫法在 TypeScript 的 excess property check 下會直接編譯失敗。

改為將同一組 `ConditionalRender(RecentNotes, condition: slug === "index")` 放進 `sharedPageComponents.afterBody`（型別 `SharedLayout = Pick<FullPageLayout, "head" | "header" | "footer" | "afterBody">`，本來就有 `afterBody`）。核對 `quartz/plugins/emitters/{contentPage,folderPage,tagPage,404}.tsx`，確認每個頁面 emitter 組 `opts` 時都是 `{ ...sharedPageComponents, ...defaultContentPageLayout(或defaultListPageLayout) }`——而 `defaultContentPageLayout`/`defaultListPageLayout` 兩者都不含 `afterBody`，所以不會覆寫掉 `sharedPageComponents.afterBody`。放在 `sharedPageComponents.afterBody` 會被所有頁面型態 spread 進去，但 `ConditionalRender` 的 `slug === "index"` 判斷確保只有首頁真的渲染出來，視覺效果跟 Critic 原本想要的完全一致，只是型別正確的寫法不同。

Footer 改動則完全照 Critic 規格實作，無差異。

## 驗證結果

環境限制：repo 的 `.npmrc` 有 `engine-strict=true`，`package.json` 要求 `node>=22`，這台機器系統 Node 是 20.20.1（且沒有更高版本可用，未安裝 nvm 等版本管理工具，未變更系統環境）。用 `npm ci --engine-strict=false` / `npm_config_engine_strict=false npx quartz build` 只針對本次指令覆蓋 engine 檢查（不改動任何設定檔），僅為跑通驗證用。

- `tsc --noEmit`：通過，無型別錯誤（證實上面提到的 `afterBody` 位置修正是必要的且正確）。
- `npx prettier . --check`：僅既有的 2 個內容檔（`content/絕緣系統/介電常數.md`、`content/絕緣系統/局部放電.md`）有格式警告，跟本次改動的 `quartz.layout.ts` 無關，不在這次改動範圍內，未處理。
- `npx quartz build`：成功，處理 92 個檔案，輸出 208 個檔案，無 error。
- 人工核對 `public/index.html`：「最近更新」區塊出現，列出 5 篇真正的知識點筆記（IE 效率等級、效率、PDIV、介電常數等），依日期遞減排序，未混入首頁自己或分類導覽頁。
- 人工核對單篇筆記頁（`public/電磁設計/基礎物理量/IE效率等級.html`）：確認**沒有**「最近更新」區塊，`ConditionalRender` 的 slug 判斷正常運作，沒有全站擴散。
- 人工核對 footer：所有頁面的 footer 連結指向 `https://github.com/0Zhen/motor-notes` 和 `https://0zhen.github.io/`，`jackyzha0/quartz`、`discord.gg` 殘留連結已完全移除（grep 確認 0 筆殘留）。

## 最終改動

`quartz.layout.ts`：
1. `sharedPageComponents.footer` 的 `links` 從 Quartz 官方連結改成 `0Zhen/motor-notes` repo 連結 + 作者主站 `0zhen.github.io`。
2. `sharedPageComponents.afterBody` 從空陣列改成 `[ConditionalRender(RecentNotes({title:"最近更新", limit:5, showTags:true, filter: 排除所有 index 頁}), condition: slug === "index")]`，只在首頁顯示最近更新的 5 篇知識點筆記。

方案 C（Graph `removeTags` 排除「馬達」tag）留到下一輪，需先跟使用者確認 tag 長期語意定位。
