# 簡體中文 Markdown → 台灣繁體中文 轉換工具

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Platform](https://img.shields.io/badge/platform-Cloudflare%20Pages-orange)](https://pages.cloudflare.com)

透過 [OpenCC](https://github.com/BYVoid/OpenCC)，在瀏覽器中將簡體中文 Markdown 轉換為台灣用語的繁體中文。這是一個**單一 HTML 檔案**的靜態網頁，不需要伺服器、不需要建置步驟，可直接部署到 Cloudflare Pages。

本專案是 [parrotchang/simplified-to-tw-markdown](https://github.com/parrotchang/simplified-to-tw-markdown)（Google Colab 版本）的網頁版重構，沿用其 `s2twp` 轉換方式與專案說明架構。

---

## ✨ 功能特色

- **單一網頁**：整個工具只有一個 `index.html`，無須安裝任何環境
- **隱私優先**：轉換在瀏覽器本機執行，檔案不會上傳到任何伺服器
- **台灣用語**：預設使用 `s2twp` 模式，自動替換為台灣慣用詞彙
- **批次處理**：可一次拖放多個 `.md` / `.txt` 檔案，並打包成 `.zip` 下載
- **保護程式碼**：可選擇略過圍欄程式碼區塊、行內程式碼與連結網址（Colab 版沒有此功能）
- **並排比對**：轉換後以行為單位並排顯示簡繁差異，異動行會標色
- **即時轉換**：貼上文字或切換模式後立即更新結果
- **深色模式**：自動跟隨系統設定

---

## 🚀 快速開始

### 本機試用

```bash
git clone https://github.com/parrotchang/md-translator.git
cd <md-translator>
python3 -m http.server 8788
# 瀏覽器開啟 http://localhost:8788
```

也可以直接以瀏覽器開啟 `index.html`，只要能連上網路載入轉換引擎即可。

### 部署到 Cloudflare Pages

**方法一：連結 Git 倉庫（推薦）**

1. 將本專案推送到 GitHub
2. 登入 Cloudflare Dashboard，前往 **Workers & Pages → Create → Pages → Connect to Git**
3. 選擇此倉庫，設定如下：

   | 設定項目 | 值 |
   |---|---|
   | Framework preset | None |
   | Build command | （留空） |
   | Build output directory | `/` |

4. 按下 **Save and Deploy**，之後每次 push 都會自動重新部署

**方法二：Wrangler 直接上傳**

```bash
npx wrangler pages deploy . --project-name simplified-to-tw-markdown
```

---

## 📋 使用步驟

| Step | 說明 |
|---|---|
| Step 1 | 拖放或選擇簡體中文 `.md` 檔案（可多選），或直接貼上文字 |
| Step 2 | 選擇轉換模式（預設 `s2twp`），需要時勾選「保護程式碼區塊、行內程式碼與連結網址」 |
| Step 3 | 在結果清單中查看每個檔案的異動行數 |
| Step 4 | 點選「比對」並排檢視簡繁差異 |
| Step 5 | 單檔「下載」，或多檔「全部下載（.zip）」 |

---

## 🔧 OpenCC 轉換模式說明

| 模式 | 說明 |
|---|---|
| `s2t` | 簡體 → 繁體（通用） |
| `s2tw` | 簡體 → 台灣繁體（字形） |
| `s2twp` | 簡體 → 台灣繁體 + 台灣慣用詞 ✅ **預設** |

### 台灣用語替換範例

| 簡體 | 台灣繁體 |
|---|---|
| 软件 | 軟體 |
| 鼠标 | 滑鼠 |
| 视频 | 影片 |
| 内存 | 記憶體 |
| 硬盘 | 硬碟 |
| 网络 | 網路 |
| 程序 | 程式 |
| 打印机 | 印表機 |

---

## 📁 專案結構

```
.
├── index.html            # 單頁轉換工具（HTML + CSS + JS 全部在此）
├── _headers              # Cloudflare Pages 安全標頭（可選）
├── sample_simplified.md  # 範例簡體中文 Markdown
├── LICENSE               # MIT 授權
├── .gitignore
└── README.md             # 本說明文件
```

---

## 🛠 運作方式與自行託管

- 轉換核心為 [opencc-js](https://github.com/nk2028/opencc-js)，由 `index.html` 從 jsDelivr CDN 載入（固定版本 `1.0.5`）
- 若希望完全不依賴外部 CDN：
  1. 下載 `opencc-js` 的 `dist/umd/full.js`，放到專案內，例如 `vendor/opencc-full.js`
  2. 將 `index.html` 中的 `<script src="https://cdn.jsdelivr.net/...">` 改為 `<script src="/vendor/opencc-full.js">`
  3. 將 `_headers` 中 `script-src` 的 `https://cdn.jsdelivr.net` 移除

---

## ❓ 常見問題

**Q：Markdown 語法會被破壞嗎？**
A：不會。OpenCC 只轉換中文字元，`#`、`**`、`-` 等符號不受影響。

**Q：程式碼區塊內的中文註解會被轉換嗎？**
A：預設會。若要保留，請勾選「保護程式碼區塊、行內程式碼與連結網址」。勾選後，連結中的網址（`](...)` 內的第一段）也會原樣保留，但連結標題文字（`"title"`）仍會被轉換。

**Q：轉換結果完全準確嗎？**
A：OpenCC 以詞庫為基礎，絕大多數情況表現良好，但仍可能有一對多字元或專有名詞的誤判，重要文件建議人工校對。

**Q：我的檔案會被上傳嗎？**
A：不會。所有轉換都在你的瀏覽器中執行；只有載入轉換引擎時會向 CDN 請求程式碼。

**Q：支援哪些檔案格式？**
A：任何 UTF-8 純文字檔，預設可選取 `.md`、`.markdown`、`.txt`。

---

## 🤝 貢獻

歡迎提交 Issue 或 Pull Request！

1. Fork 本專案
2. 建立你的 Feature Branch（`git checkout -b feature/amazing-feature`）
3. Commit 變更（`git commit -m 'Add amazing feature'`）
4. Push 到 Branch（`git push origin feature/amazing-feature`）
5. 開啟 Pull Request

---

## 📄 授權

本專案採用 [MIT License](./LICENSE) 開源授權：你可以自由使用、複製、修改、合併、發布、散布、再授權及販售本軟體，只需在所有副本或重要部分中保留版權聲明與授權聲明。本軟體依「現狀」提供，不附任何形式的保證。

第三方元件各自適用其授權條款，請以其官方倉庫為準。

---

## 🙏 致謝

- [parrotchang/simplified-to-tw-markdown](https://github.com/parrotchang/simplified-to-tw-markdown) — 原始 Colab 版本與專案說明
- [OpenCC](https://github.com/BYVoid/OpenCC) — 繁簡中文轉換核心引擎與詞庫
- [opencc-js](https://github.com/nk2028/opencc-js) — OpenCC 的 JavaScript 實作
