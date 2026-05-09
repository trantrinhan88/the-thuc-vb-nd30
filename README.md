---
name: cong-van-giam-dinh-bhyt
description: >
  Sử dụng skill này khi người dùng muốn soạn thảo, tạo, chỉnh sửa hoặc xuất công văn / thông báo
  giám định chi phí khám chữa bệnh BHYT theo chuẩn Nghị định 30/2020/NĐ-CP. Trigger khi người dùng
  nhắc đến: "công văn giám định", "thông báo từ chối thanh toán BHYT", "soạn thảo văn bản BHXH",
  "Nghị định 30", "xuất DOCX công văn", "kiểm tra thể thức văn bản hành chính", "lỗi chính tả
  công văn", hoặc bất kỳ yêu cầu nào liên quan đến soạn thảo văn bản hành chính chuẩn nhà nước.
  Kể cả khi người dùng chỉ nói "soạn công văn" hay "làm văn bản hành chính" cũng nên dùng skill này.
---

# Công văn Giám định BHYT — Soạn thảo chuẩn Nghị định 30

Skill soạn thảo, kiểm tra thể thức và xuất file DOCX công văn/thông báo giám định chi phí KCB BHYT,
tuân thủ đầy đủ Nghị định 30/2020/NĐ-CP về công tác văn thư.

---

## Tổng quan

Công cụ gồm hai thành phần chính:

1. **File HTML ứng dụng** (`cong_van_giam_dinh.html`) — giao diện soạn thảo đầy đủ chạy trên trình duyệt
2. **Skill này (SKILL.md)** — hướng dẫn Claude tái tạo, cập nhật hoặc mở rộng ứng dụng khi cần

---

## Cấu trúc ứng dụng

```
cong_van_giam_dinh.html      ← File duy nhất, self-contained (HTML + CSS + JS)
SKILL.md                     ← Skill hướng dẫn (file này)
```

### Bố cục giao diện

```
┌─────────────────────────────────────────────────────────┐
│  SIDEBAR (400px)              │  WORKSPACE (còn lại)    │
│  ┌─────────────────────────┐  │  ┌───────────────────┐  │
│  │ 1. Thông tin cơ quan    │  │  │  Preview A4       │  │
│  │ 2. Trích yếu & Nơi nhận│  │  │  (210mm × 297mm)  │  │
│  │ 3. Căn cứ & Đặt vấn đề │  │  │  realtime update  │  │
│  │ 4. Nội dung chính       │  │  └───────────────────┘  │
│  │ 5. Kết luận & Chữ ký   │  │                         │
│  │ 6. Kiểm tra lỗi AI      │  │                         │
│  ├─────────────────────────┤  │                         │
│  │ [Lưu nháp] [Tải lại]   │  │                         │
│  │ [Xuất DOCX]             │  │                         │
│  └─────────────────────────┘  │                         │
└─────────────────────────────────────────────────────────┘
```

---

## Nguyên tắc thể thức Nghị định 30

### Bố cục tiêu đề (Quốc hiệu — Tiêu ngữ)

```
[CỘT TRÁI 48%]                    [CỘT PHẢI 52%]
CƠ QUAN CHỦ QUẢN (12pt, HOA)      CỘNG HÒA XÃ HỘI CHỦ NGHĨA VIỆT NAM (12pt, HOA, đậm)
CƠ QUAN BAN HÀNH (12pt, HOA, đậm) Độc lập - Tự do - Hạnh phúc (13pt, đậm, gạch chân)
─────────────────                  ──────────────────────────────
Số: .../BHXH-XX                    Địa danh, ngày DD tháng MM năm YYYY (13pt, nghiêng)
V/v [trích yếu]
```

### Kính gửi

- Căn giữa, font 14pt, không in đậm
- Không thụt lề

### Căn cứ pháp lý

- **Thụt lề đầu dòng 1.27cm** — giống các đoạn văn thường
- **Không in nghiêng** (italic chỉ dùng cho tên văn bản quy phạm nằm trong câu)
- Mỗi căn cứ kết thúc bằng **dấu chấm phẩy (`;`)**, căn cứ cuối kết thúc bằng **dấu phẩy (`,`)**
- Tự động thêm dấu câu nếu người dùng chưa nhập

### Nội dung

- Thụt lề đầu dòng 1.27cm, căn đều hai lề (justify)
- Font Times New Roman 14pt, line-height 1.5
- Mỗi dòng textarea → một đoạn văn riêng

### Chữ ký

- Phó Giám đốc ký thay → tự động thêm dòng "KT. GIÁM ĐỐC" phía trên
- Khoảng cách chữ ký: 6 dòng trống (≈ 90px trong preview, 6 `emptyLine()` trong DOCX)

### Nơi nhận (footer trái)

- Font 11pt, in nghiêng, gạch chân chữ "Nơi nhận:"
- Danh sách font 11pt thường

---

## Dữ liệu cơ quan (agencyData)

| Cơ quan | Địa danh | Ký hiệu | Giám đốc | Phó Giám đốc |
|---------|----------|---------|----------|--------------|
| BHXH Cơ sở Cao Lãnh | Cao Lãnh | .../BHXH-CL | Đoàn Văn Đông | Nguyễn Văn Sáu, Nguyễn Hoàng Hoa |
| BHXH Cơ sở Tháp Mười | Tháp Mười | .../BHXH-TM | Phạm Hiếu Nghĩa | Bùi Ngọc Hiếu |
| BHXH Cơ sở Tam Nông | Tam Nông | .../BHXH-TN | Lê Văn Thức | Nguyễn Văn Nguyên |
| BHXH Cơ sở Thanh Bình | Thanh Bình | .../BHXH-TB | Nguyễn Văn Xuân | Võ Sĩ Nguyên |

Khi thay đổi **Cơ quan ban hành**, các trường Địa danh, Số ký hiệu, danh sách người ký tự động cập nhật.

---

## Tính năng kiểm tra lỗi AI (Mục 6)

### Hai provider hỗ trợ

| Provider | Cách dùng | Ghi chú |
|----------|-----------|---------|
| **Deepseek** | Nhập API key → Lưu → Kiểm tra | Hoạt động khi mở file HTML local (Chrome/Edge). Key lưu localStorage. |
| **Claude (Anthropic)** | Không cần key | Chỉ hoạt động khi file được nhúng trong **claude.ai** — bị CORS khi chạy local |

### Luồng kiểm tra

```
Nhấn [Kiểm tra lỗi & Thể thức]
    ↓
Gửi toàn bộ nội dung các trường dưới dạng JSON
    ↓
AI trả về mảng JSON các lỗi
    ↓
Highlight vị trí lỗi trên preview (vàng = warning, đỏ = error)
    ↓
Danh sách lỗi hiện trong panel sidebar
    ↓
Click lỗi → scroll đến vị trí highlight
Click [Sửa] → áp dụng fixedText vào field tương ứng
Click [Áp dụng tất cả gợi ý] → sửa toàn bộ cùng lúc
```

### Cấu trúc JSON lỗi từ AI

```json
{
  "id": 1,
  "field": "legalBasis",
  "severity": "error | warning",
  "type": "Tên lỗi ngắn (<30 ký tự)",
  "description": "Mô tả lỗi tiếng Việt",
  "originalText": "đoạn gốc bị lỗi (≤50 ký tự, tồn tại chính xác trong field)",
  "suggestion": "Gợi ý sửa ngắn",
  "fixedText": "TOÀN BỘ nội dung mới của field sau khi sửa",
  "previewSelector": "#preview-doc-summary | #preview-legal-basis | ..."
}
```

### Map field → previewSelector

| field | previewSelector | Element ID (textarea/input) |
|-------|-----------------|-----------------------------|
| `docSummary` | `#preview-doc-summary` | `doc-summary` |
| `legalBasis` | `#preview-legal-basis` | `legal-basis` |
| `issueStatement` | `#preview-issue-statement` | `issue-statement` |
| `mainContent` | `#preview-main-body` | `main-content` |
| `conclusion` | `#preview-conclusion` | `conclusion` |
| `recipientMain` | `#preview-recipient-main` | `recipient-main` |

---

## Xuất DOCX (thư viện docx.js v8.5)

Sử dụng CDN: `https://cdn.jsdelivr.net/npm/docx@8.5.0/build/index.umd.js`

### Cấu hình trang

```javascript
page: {
  margin: { top: mmToTwip(20), bottom: mmToTwip(20), left: mmToTwip(30), right: mmToTwip(15) }
}
// mmToTwip(mm) = Math.round(mm * 56.6929133858)
```

### Cấu trúc sections trong Document

```
Document
└── Section
    ├── Header (số trang, căn giữa, Times New Roman 14pt)
    └── Children
        ├── headerTable (Table 2 cột: thông tin cơ quan | quốc hiệu)
        ├── emptyLine()
        ├── Paragraph "Kính gửi: ..." (căn giữa)
        ├── emptyLine()
        ├── [Căn cứ pháp lý] → mkPara(line, {align:JUSTIFIED, indent:true, sp:true})
        ├── [Đặt vấn đề]     → mkPara(text, {align:JUSTIFIED, indent:true, sp:true})
        ├── [Nội dung chính] → mkPara(line, {align:JUSTIFIED, indent:true, sp:true})
        ├── [Kết luận]       → mkPara(text, {align:JUSTIFIED, indent:true, sp:true})
        ├── emptyLine()
        └── footerTable (Table 2 cột: Nơi nhận | Chữ ký)
```

### Hàm mkPara (tạo đoạn văn chuẩn)

```javascript
const mkPara = (text, opts = {}) => new Paragraph({
  alignment: opts.align || AlignmentType.LEFT,
  indent: opts.indent ? { firstLine: mmToTwip(12.7) } : undefined,
  spacing: opts.isLine ? { before:0, after:0, line:160, lineRule:'exact' }
         : opts.tight  ? { before:0, after:0, line:240, lineRule:'auto' }
         : opts.sp     ? { after:120, line:360, lineRule:'auto' }
         : undefined,
  children: [new TextRun({
    text: String(text ?? ''),
    font: 'Times New Roman',
    size: opts.sz || 28,        // 28 half-points = 14pt
    bold: opts.bold || false,
    italics: opts.ital || false,
    underline: opts.ul ? { type: UnderlineType.SINGLE } : undefined,
    characterSpacing: opts.cs   // dùng -5 cho text ALL CAPS dài
  })]
});
```

### Bảng không viền (noBorder)

```javascript
const noBorder = {
  top:              { style: BorderStyle.NONE, size: 0, color: 'FFFFFF' },
  bottom:           { style: BorderStyle.NONE, size: 0, color: 'FFFFFF' },
  left:             { style: BorderStyle.NONE, size: 0, color: 'FFFFFF' },
  right:            { style: BorderStyle.NONE, size: 0, color: 'FFFFFF' },
  insideHorizontal: { style: BorderStyle.NONE, size: 0, color: 'FFFFFF' },
  insideVertical:   { style: BorderStyle.NONE, size: 0, color: 'FFFFFF' }
};
```

---

## Lưu / Tải nháp

Dùng `localStorage` key `congvan_saved_data`:

```javascript
// Lưu
const data = {};
['agencyUpper','agencyMain','docNumber','location','date','docSummary',
 'recipientMain','legalBasis','issueStatement','mainContent',
 'conclusion','signPosition','signName','recipientsCc']
  .forEach(k => data[k] = document.getElementById(k).value);
localStorage.setItem('congvan_saved_data', JSON.stringify(data));

// Tải
const data = JSON.parse(localStorage.getItem('congvan_saved_data'));
Object.keys(data).forEach(k => { if (document.getElementById(k)) document.getElementById(k).value = data[k]; });
```

Deepseek API key lưu riêng ở key `deepseek_api_key`.

---

## Hướng dẫn cập nhật / mở rộng skill

### Thêm cơ quan mới

Chỉnh `agencyData` trong JS:

```javascript
"BẢO HIỂM XÃ HỘI CƠ SỞ [TÊN]": {
  location: "[Địa danh]",
  symbol: ".../BHXH-[KH]",
  signatories: {
    "GIÁM ĐỐC":   ["Họ và tên"],
    "PHÓ GIÁM ĐỐC": ["Họ và tên 1", "Họ và tên 2"]
  }
}
```

Và thêm `<option>` tương ứng vào `<select id="agency-main">`.

### Thêm loại văn bản mới

Nếu cần soạn **Quyết định** hoặc **Thông báo** thay vì Công văn:
- Quyết định có thêm phần "CĂN CỨ" dạng block riêng (không thụt lề, không có "Kính gửi")
- Thông báo giữ nguyên cấu trúc hiện tại, chỉ thay label "Kính gửi" → "Kính gửi" hoặc bỏ

### Thay đổi font/cỡ chữ

| Vị trí | Font | Cỡ (pt) | Ghi chú |
|--------|------|---------|---------|
| Cơ quan chủ quản / ban hành | Times New Roman | 12pt (size:24) | |
| Quốc hiệu | Times New Roman | 12pt (size:24) | bold, characterSpacing:-5 |
| Tiêu ngữ | Times New Roman | 13pt (size:26) | bold |
| Số, ký hiệu | Times New Roman | 13pt (size:26) | |
| Ngày tháng | Times New Roman | 14pt (size:28) | italics |
| Nội dung chính | Times New Roman | 14pt (size:28) | |
| Nơi nhận (label) | Times New Roman | 12pt (size:24) | italics, bold, underline |
| Nơi nhận (list) | Times New Roman | 11pt (size:22) | |

---

## Yêu cầu môi trường

- **Trình duyệt**: Chrome 90+ hoặc Edge 90+ (cần hỗ trợ ES2020, localStorage)
- **Kết nối Internet**: Cần khi tải CDN (docx.js, file-saver, Google Fonts)
- **Deepseek API**: Cần key từ [platform.deepseek.com](https://platform.deepseek.com) để dùng tính năng AI
- **Không cần server**: File HTML chạy hoàn toàn client-side

---

## Checklist thể thức trước khi xuất

- [ ] Số ký hiệu đúng định dạng: `DD/BHXH-KH` (năm 2 chữ số hoặc không có năm)
- [ ] Ngày tháng đầy đủ: ngày DD tháng MM năm YYYY
- [ ] Căn cứ: dấu `;` giữa các dòng, dấu `,` cuối dòng cuối
- [ ] Không có lỗi chính tả (chạy AI kiểm tra)
- [ ] Tên người ký khớp chức vụ
- [ ] Nơi nhận có "- Như trên;" và "- Lưu: VT."
