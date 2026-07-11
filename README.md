# PDF 轉 EPUB 工具

將 PDF 電子書轉換為 EPUB 格式，方便匯入 Kobo 等電子閱讀器閱讀。

本工具使用 [PyMuPDF](https://pymupdf.readthedocs.io/) 解析 PDF 文字區塊，自動清理頁首、頁尾與頁碼，依字體大小辨識標題層級，再以 [EbookLib](https://github.com/aerkalov/ebooklib) 封裝成 EPUB。

## 功能特色

- 自動提取 PDF 第一頁作為 EPUB 封面
- 過濾頁首、頁尾與常見頁碼格式
- 依字體大小自動辨識 h1～h3 標題
- 合併相鄰段落，還原閱讀流暢度
- 依 h1 標題或固定區塊數自動分章
- 支援自訂書名、作者、語言等中繼資料

## 環境需求

- Python 3.10 以上
- 僅需以下兩個 Python 套件：
  - `PyMuPDF` — PDF 解析與封面渲染
  - `EbookLib` — EPUB 封裝

## 安裝

```bash
git clone https://github.com/jooodie/pdf2epub.git
cd pdf2epub

python3 -m venv .venv
source .venv/bin/activate   # Windows: .venv\Scripts\activate

pip install -r requirements.txt
```

## 基本用法

```bash
python pdf_to_epub.py <輸入PDF> <輸出EPUB>
```

### 範例

```bash
python pdf_to_epub.py 快思慢想.pdf 快思慢想.epub
```

轉換完成後，將 `.epub` 檔案傳到 Kobo 閱讀器即可。

## 命令列參數

| 參數 | 說明 | 預設值 |
|------|------|--------|
| `input_pdf` | 輸入 PDF 檔案路徑 | （必填） |
| `output_epub` | 輸出 EPUB 檔案路徑 | （必填） |
| `--title` | 電子書標題 | PDF 檔名 |
| `--author` | 作者名稱 | `Unknown` |
| `--language` | 語言代碼（如 `zh`、`en`） | `zh` |
| `--header-ratio` | 頁首區域高度比例（0～1） | `0.08` |
| `--footer-ratio` | 頁尾區域高度比例（0～1） | `0.08` |

### 進階範例

```bash
python pdf_to_epub.py book.pdf book.epub \
  --title "快思慢想" \
  --author "丹尼爾·卡尼曼" \
  --language zh
```

若 PDF 的頁首或頁尾較高，可調整比例以改善過濾效果：

```bash
python pdf_to_epub.py book.pdf book.epub --header-ratio 0.12 --footer-ratio 0.10
```

## 轉換流程

執行時會依序完成以下四個步驟：

```
[1/4] 提取封面：將 PDF 第 1 頁渲染為封面圖片
[2/4] 解析 PDF 內文：讀取文字區塊（跳過封面頁）
[3/4] 清理結構並辨識標題：過濾頁首/頁尾、分類標題、合併段落
[4/4] 封裝 EPUB：產生含封面、目錄、章節的 EPUB 檔案
```

## 適用與限制

### 適用的 PDF

- 有**文字層**的 PDF（例如從 Word、LaTeX 匯出的電子書）
- 第一頁可作為封面（內文從第 2 頁開始解析，避免重複）

### 不適用的 PDF

- **掃描版 PDF**（整頁為圖片、無文字層）— 本工具無法辨識圖片中的文字
- 文字以圖片嵌入的 PDF
- 複雜排版（多欄、表格、公式）可能無法完美還原

若轉換時出現「未從 PDF 提取到可用文字」錯誤，通常表示 PDF 沒有可提取的文字層。

## 授權

本專案供個人使用。轉換受版權保護的書籍時，請遵守相關法律規定。
