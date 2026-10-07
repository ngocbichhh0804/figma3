# figma3 — Figma → HTML/CSS

Chuyển thiết kế Figma sang HTML semantic + CSS thuần. Không framework, không
bước build, **không một dòng JavaScript nào được gửi xuống trình duyệt**.

Chú thích trong code viết tiếng Việt cho dễ đọc lúc phát triển — chuyển sang
tiếng Anh trước khi bàn giao.

## Quy ước bắt buộc — đọc trước khi sửa bất cứ gì

- **Không flexbox, không grid, không `gap`.** Bố cục dựng bằng `float` +
  `calc()`, `clear`, `flow-root`, `inline-block` + `font-size: 0` ở cha,
  `line-height` + `vertical-align`, `position: absolute`.
- **Không JavaScript** trên trang giao cho khách. Trạng thái mở/đóng dùng
  `:target`, `<details>`, radio/checkbox + `:has()`. `dev/*-overlay.html` là
  công cụ nội bộ (cũng không JS).
- **CSS chia theo cascade layer**, khai báo một lần trong `<head>`:
  `reset, tokens, base, layout, components, utilities`. Không `!important`
  (trừ khối `prefers-reduced-motion`).
- **`css/tokens.css` chỉ chứa giá trị dùng chung** (màu, font, thang độ đậm,
  leading, chuyển động, bo góc). Số đo riêng của component ghi thẳng trong
  file component. Số dùng ở nhiều rule trong cùng component thì làm biến cục
  bộ trên selector gốc (vd. `.card { --card-title-gap: 87px; }`), không đưa lên
  `:root`. Mọi con số đều ghi nhãn nguồn gốc ngay bên cạnh:
  - `[FIGMA]` — đọc thẳng từ panel inspector
  - `[ĐO]` — đo bằng script quét pixel trên ảnh export 1:1
  - `[MẪU]` — lấy từ file asset được cung cấp
  - `[ƯỚC]` — ước lượng, chờ xác nhận lại
- Đặt tên BEM, specificity phẳng, không selector lồng quá một cấp, không dùng
  ID để style.
- **Pixel-perfect**: mỗi trang render đúng khổ khung Figma; đối chiếu bằng
  overlay + script so pixel. Đã chốt số đo thì không đổi.
- Font thương mại → thay bằng font OFL, tự lưu `.woff2` trong `assets/fonts/`
  (không gọi Google Fonts).

## Cấu trúc

```
*.html                   mỗi trang Figma một file
index.html               Landing page "Furnics" (1920 × 7786)
css/
├── reset.css / fonts.css / tokens.css / base.css / layout.css
├── components/          mỗi khối một file BEM: logo, button, slider-dots,
│                        site-header, hero, features, about, section-head,
│                        product-track, testimonial, categories, subscribe,
│                        articles, brands, site-footer
└── utilities.css        .visually-hidden, .skip-link
assets/fonts|icon|img/
dev/pages.json           danh sách trang (nguồn sinh overlay)
dev/*-overlay.html       đè ảnh Figma lên bản code (Code / 50% / Figma / Slider) — home-overlay.html
docs/comparison/<slug>/  <slug>-figma.png, -build, -side-by-side, -overlay, -difference
```

## Trạng thái các trang

- **Landing page** (`index.html`, khung "Furnics" 1920 × 7786): xong. Sai lệch
  TB 4.4/765 (ảnh Figma là JPG), 97.8 % pixel ≤ 30, 31/79 vùng khớp 0px,
  còn lại ≤ 1px (5 dòng lẻ 2px). Letter-spacing đã dò theo ảnh, xem README.
  Responsive 320 → 2560. Ảnh hero là ảnh tạm, chờ file gốc.

## Font

| Font Figma | Dùng | Ghi chú |
| --- | --- | --- |
| Cinzel 400 | Cinzel (OFL) | hộp chữ = 1.348em, ghi line-height theo px hộp Figma |
| Poppins 300/400/500 (+ italic 300/400) | Poppins (OFL) | dòng 29px ở cỡ 16; letter-spacing dò lại theo ảnh (README) |
| Bebas Neue 400 | Bebas Neue (OFL) | logo |

## Chạy thử

```bash
npm install     # chỉ để có browser-sync
npm run dev     # http://localhost:3000
```

Không có npm: `python3 -m http.server 3000`.
