---
name: editorial-self-study-handbook
description: 當使用者要求製作「自學手冊」「教學文件」「課程教材」「內部訓練教材」，尤其是給前端/工程團隊使用的繁體中文教材，且希望同時輸出成一份帶側邊選單的 HTML 檔與一份對應的 Markdown 檔時，請使用這個 skill。看到「教學文件產生」「自學手冊」「課程教材」「training handbook」「self-study guide」，或使用者提到「依照之前課程的風格」「跟 XX 課程一樣的風格」時都應觸發。當使用者要求為既有教材製作延伸版本（例如測驗卷、深度解析版、HTML轉MD）且要保持視覺與結構一致時，也應觸發。不適用於：一般對話式回答、純程式碼交付、或使用者明確要求完全不同視覺風格的文件。
---

# 編輯風格自學手冊產生器

一套可重複使用的設計系統與寫作範本，用來產生內部技術訓練教材，並同時輸出成兩份檔案：一份有側邊選單、可導覽的**HTML手冊**，以及一份對應的**Markdown手冊**（方便貼到 wiki/Notion/GitHub）。這套風格最初是為了一個繁體中文前端工程團隊的自學系列課程（SOLID、FP、IoC/DI、API 設計）而開發的。使用這個 skill 可以讓新課程與既有系列在視覺和結構上保持一致。

## 什麼時候該用這個 skill

- 使用者要求製作「自學手冊」「教學文件」，尤其是針對某個明確的技能落差設計的（例如「我是前端，後端不熟」）
- 使用者希望同時有 HTML 版（用來閱讀/展示）與 MD 版（用來貼進 wiki）
- 使用者提到某份之前的課程，想要「一樣的風格」
- 後續要求在既有手冊上加測驗卷、加深度概念版、或把手冊轉成其他格式

## 交付檔案的整體形式

除非使用者只要求一種格式，否則一律產出**兩份檔案**：

1. `{主題slug}.html` — 自包含的 HTML，不依賴外部框架，`<style>` 與 `<script>` 都寫在檔案裡
2. `{主題slug}.md` — 內容相同，但重新排版成純 Markdown（沒有 HTML 側邊選單/JS，改用引用區塊與 emoji 標籤取代彩色卡片）

兩者都採用**編號章節結構**（`01`、`02`……視主題而定，通常 9～14 章），每章要能獨立閱讀，但觀念上會延續前面章節。

## 內容設計哲學（不論主題都要套用）

- **先講「為什麼」，再講「怎麼做」。** 每章開頭都先講這個觀念在解決什麼問題、為什麼重要，再進入語法或步驟。
- **從讀者已經懂的東西搭橋過去。** 如果讀者是前端背景，就要把後端/不熟的觀念透過前端熟悉的比喻翻譯過去，並明確對照「前端該注意什麼」vs「後端在想什麼」。
- **先講反例，再講正解。** 把「✕ 錯誤示範」和「✓ 正確示範」成對放在一起，而不是只展示正確版本——對比才是讓規則被記住的關鍵。
- **每個抽象觀念都要配一個具體的載體**：一張對照表、一個標籤卡、或一段程式碼。不要讓觀念只停留在純文字，如果一張表或一個範例能更快說清楚，就用它。
- **每章結尾都要有簡短的條列式「本章結論」**（3～5條，重申核心規則，不是總結整章內容）。
- **語言**：配合使用者一直使用的語言（這個系列是繁體中文，術語可以中英並列——中文解釋 + 英文原文放括號或當作 tagline）。

## HTML 設計系統

### 字體與配色（這個系列統一採用的「學術編輯風」）

```css
:root{
  --paper:#faf6ee;        /* 頁面背景 — 暖米白，不是純白 */
  --paper-alt:#f2ecdd;     /* 側邊欄/替代背景 */
  --ink:#2b2620;           /* 正文文字 */
  --ink-soft:#5c554a;      /* 次要文字 */
  --line:#e1d8c3;          /* 邊框/分隔線 */
  --accent:#8f2d2d;        /* 磚紅強調色 — 連結、active狀態、標題 */
  --accent-bg:#f6e9e6;
  --good:#2f6b4f;          /* 綠色 — 用於「正確示範」程式碼區塊 */
  --bad:#8f2d2d;           /* 紅色 — 用於「錯誤示範」程式碼區塊 */
  --tip:#7a5a12;           /* 琥珀色 — 比喻類提示框 */
  --code-bg:#2b2620;       /* 深色程式碼區塊背景 */
  --code-ink:#f2ecdd;
}
```

- 標題／正文：`'Noto Serif TC', 'Source Serif 4', serif` — 營造「教科書感」而非「網頁應用感」
- 程式碼／標籤／輔助文字：`'JetBrains Mono', monospace`
- 這個風格絕不使用一般 UI 無襯線字體（Arial/Helvetica/system-ui）——襯線字體是整體調性的核心

### 結構元件（每次做新課程都直接沿用，不要重新發明）

1. **固定左側 Sidebar（約 280px）**：課程標題 + tagline，接著是分組的導覽連結（分組名稱如「導引」「基礎觀念」「進階議題」「綜合應用」，依主題調整）。1024px 以下收起，改用 `☰ 目錄` 按鈕展開。
2. **閱讀進度條**：固定在頂部的 3px 長條，寬度隨滾動位置變化。
3. **滾動高亮（scroll-spy）**：對每個 `<section class="chapter">` 用 `IntersectionObserver` 偵測，並切換對應側邊連結的 `.active` 狀態。頁面載入時預設將第一個連結設為 active。
4. **封面區塊**：小型 eyebrow 標籤（強調色、大寫字距拉開）→ H1 標題 → 副標 → meta 行（章節數、格式、適用對象）。
5. **章節區塊**（`<section class="chapter" id="chXX">`）：章節編號（mono字體、淺強調色）、H2 標題、斜體的一行 tagline（用英文或更精準的說法重新框一次）。
6. **概念卡（Concept Card）**：深色標頭列（`CONCEPT · <名稱>`）+ 白色內容區。用來放這個小節裡最重要的單一觀念——先給定義，再給更深一層的「為什麼要這樣設計」段落。
7. **標籤引言（Tag-line）**——四種顏色，左側色條 + 第一行粗體 mono 標籤：
   - `tag-analogy`（琥珀色）— 比喻
   - `tag-why`（強調紅）— 「為什麼這樣設計」的設計理由
   - `tag-model`（藍色）— 心智模型
   - `tag-antipattern`（紅底）— 常見誤用警示
8. **帶顏色標頭列的程式碼區塊**：`.code-block.bad`（紅色標頭列，「✕ ...」）、`.code-block.good`（綠色標頭列，「✓ ...」）、`.code-block.neutral`（灰色標頭列）用於只是舉例、不做對錯判斷的程式碼。程式碼本身放在深色 `<pre>` 裡，跟米白頁面形成對比。
9. **前端/後端對照條（fe-be）**：兩欄式格線，對照「前端在意什麼」vs「後端在想什麼」（或任何兩種立場的張力）——只要主題天然存在兩邊視角的落差，就可以重複使用這個元件。
10. **參考表格**（`table.ref`）：表頭用 `--paper-alt` 淺色底、mono 字體——用來列舉選項/狀態碼/方法等等。
11. **檢查清單框**：純白卡片、條列清單——用於「上線/review前該檢查什麼」。
12. **總結框**：深色（`--ink`）背景、mono字體標頭「本章結論」或「全書總結」、白色條列清單——永遠放在每章的最後一個元件。

### Script 行為（直接照抄，只有在 section class 名稱不同時才需要調整 selector）

```javascript
const progress = document.getElementById('progress');
window.addEventListener('scroll', () => {
  const h = document.documentElement;
  const pct = (h.scrollTop) / (h.scrollHeight - h.clientHeight) * 100;
  progress.style.width = pct + '%';
});

const menuItems = document.querySelectorAll('#sidebar nav a');
const sections = document.querySelectorAll('main .chapter, main .cover');
const observer = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      const id = entry.target.getAttribute('id');
      menuItems.forEach(a => a.classList.remove('active'));
      const active = document.querySelector(`#sidebar nav a[href="#${id}"]`);
      if (active) active.classList.add('active');
    }
  });
}, { rootMargin: '-20% 0px -60% 0px', threshold: 0 });
sections.forEach(s => observer.observe(s));
if (menuItems.length > 0) menuItems[0].classList.add('active');

menuItems.forEach(a => {
  a.addEventListener('click', () => {
    if (window.innerWidth <= 1024) document.getElementById('sidebar').classList.remove('open');
  });
});
```

手機版：Sidebar 預設 `transform: translateX(-100%)`（在 1024px 以下），透過 `#menu-toggle` 按鈕切換；`main`/`footer` 在該斷點下也會移除 `margin-left`。

## Markdown 對照版規則

`.md` 版本要保留一樣的內容與章節順序，但拿掉所有 HTML/CSS/JS。上述元件在 Markdown 裡對應的純文字寫法如下：

| HTML 元件 | Markdown 對應寫法 |
|---|---|
| 概念卡 | `###` 小標題下的一般段落 |
| `tag-analogy` | `> 💡 **比喻**` 引用區塊 |
| `tag-why` | `> ⚡ **為什麼這樣設計**` 引用區塊 |
| `tag-model` | `> 🧠 **心智模型**` 引用區塊 |
| `tag-antipattern` | `> ⚠️ **常見誤用 / 反模式**` 引用區塊 |
| bad/good 程式碼區塊 | 一般 fenced code block，區塊內第一行用 `✕ ...` 或 `✓ ...` 當作註解 |
| 參考表格 | 標準 Markdown 表格 |
| 檢查清單 | `-` 條列清單 |
| 總結框 | `**本章結論**` 粗體行 + 條列清單 |
| Sidebar 導覽 | 開頭放一個 `## 目錄` 區塊，用錨點連結指向每個 `## NN · 標題` |

Markdown 的章節標題統一用 `## 01 · <標題>`（兩位數編號、中間點、標題），這樣在任何 renderer 裡呈現都一致，也跟 HTML sidebar 的標籤對得上。

## 每章寫作前的檢查清單

- [ ] 開頭是「為什麼這個觀念/問題重要」，不是先丟定義
- [ ] 至少有一個比喻、心智模型、或設計理由的標籤卡
- [ ] 至少有一個具體載體（表格、程式碼對照、或對照條）
- [ ] 如果有常見錯誤，要明確標成反模式
- [ ] 結尾有 3～5 條「本章結論」
- [ ] 用詞跟前面章節一致（不要中途幫同一個觀念換名字）

## 延伸格式（如果之後使用者要求更多）

這個系列之前為手冊做過的延伸版本：
- **深度概念解析版** — 沿用一樣的設計系統，但整個結構改成圍繞「這個東西為什麼存在」而不是「怎麼用」，幾乎全部用概念卡元件撐起內容
- **測驗卷** — 同時輸出 `.xlsx`（有格式）、`.txt`（tab分隔，方便貼進既有表格）、`.md` 三種格式，附難度分級，再搭配一份 HTML 詳解文件（正確答案理由 + 每個錯誤選項為什麼錯 + 記憶技巧）

之後如果要用這種方式延伸手冊，記得沿用一樣的視覺系統與章節編號方式，讓整個系列讀起來像同一套產品。
