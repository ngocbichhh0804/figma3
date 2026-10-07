# figma3 — Figma → HTML/CSS (Furnics)

Cắt tay từ Figma sang HTML semantic và **CSS thuần**. Không framework, không
bước build, **không JavaScript**.

**Live:** https://ngocbichhh0804.github.io/figma3/ · **Overlay so Figma:** https://ngocbichhh0804.github.io/figma3/dev/home-overlay.html · **Source:** https://github.com/ngocbichhh0804/figma3 · **Thiết kế:** _điền link Figma_

## Độ khớp với bản thiết kế

| Trang | Khung Figma | Sai lệch màu TB (/765) | Pixel lệch ≤ 30 | ≤ 60 | Vùng khớp 0px | Ghi chú |
| --- | --- | --- | --- | --- | --- | --- |
| Landing page (`index.html`) | 1920 × 7786 | 4.1 | 97.9 % | 99.0 % | 34 / 79 (còn lại lệch 1px; 5 dòng lẻ lệch 2px) | ảnh Figma là JPG |

Ảnh đối chiếu nằm trong `docs/comparison/home/` (`-figma`, `-build`,
`-side-by-side`, `-overlay`, `-difference`; danh sách vùng kiểm ở
`regions.txt`). Tự kiểm trực tiếp: mở `dev/home-overlay.html` (bốn chế độ
Code only / Overlay 50% / Figma only / Slider — không JavaScript, nút là radio
input đọc bằng `:has()`).

### Vì sao chưa đạt 0px ở mọi vùng

- **Ảnh Figma là JPG**: nén JPG làm nhiễu ảnh chụp và viền chữ.
- **Lệch 1px ở mép chữ**: Figma và Chrome khử răng cưa khác nhau; gạch chân
  "Read More" của Figma nằm ở nửa pixel (y = 6796.5) nên Chrome không vẽ trùng được.
- **Bề ngang chữ** đã dò theo vị trí từng chữ trên ảnh Figma (script chụp nhiều
  giá trị rồi so), không lấy nguyên % trong panel:

  | Chữ | Figma | Dùng |
  | --- | --- | --- |
  | Poppins 300 thân bài | 2% | `0.017em` |
  | Trích dẫn Poppins 300 italic 29px | 2% | `0.012em`; dòng 2 `0.0125em`, dòng 3 `0.014em` + dời từng dòng 1px |
  | Placeholder email | 2% | `0.017em`, `word-spacing: -1px` |
  | Nút "Shop now", "Subscribe now", "Add to cart" (Poppins 500 in hoa) | 8% | `0.07em` |
  | Nút "About us" | 8% | `0.055em` (hộp chữ Figma 86px) |
  | Link "Shop all", "See all articles" | 8% | `0.075em` |
  | Footer: email | 8% | `0.085em`, `word-spacing: -0.5px` |
  | Footer: địa chỉ | 8% | `0.08em`, `word-spacing: -0.5px` |
  | Footer: dòng 2 đoạn giới thiệu (`.site-footer__fit`) | 2% | `0.02em` |
  | Footer: số điện thoại | 8% | `0.068em`, `word-spacing: -1px` |
  | Nav, link footer, Cinzel | 8% / 2% | giữ nguyên số Figma |

  Sau khi dò, vị trí từng chữ lệch tối đa 1px; riêng 5 dòng (dòng 2 đoạn
  "Since 1990's", dòng 3 trích dẫn, gạch chân 2 "Read More")
  lệch 2px ở cuối dòng vì Figma dựng mỗi dòng hơi khác nhau, không có một giá
  trị chung nào khớp tất cả.
- **Dấu “ , ” trong trích dẫn**: Figma vẽ bằng hình glyph khác Poppins italic
  của Chrome (dạng "66/99" có chấm tròn). Ký tự thật vẫn nằm trong HTML (đọc,
  copy được) nhưng trong suốt; hình dấu là mask nhỏ tách từ ảnh Figma, nhúng
  thẳng vào CSS bằng data URI (vì Chrome không nạp ảnh mask qua `file://`).
  Ký tự "@" ở email cũng được phóng 1.24 lần cho bằng hình Figma.
- **Thẻ sản phẩm**: "Add to cart" nằm đúng chỗ giá và ẩn sẵn; rê chuột, chạm
  hoặc focus (Tab) vào thẻ thì hiện nút, ẩn giá, tên đổi màu olive — đúng trạng
  thái Figma vẽ ở thẻ "Black Sofa Set". Ở overlay, rê chuột vào thẻ đầu để so.
- **Bài viết đầu**: Figma vẽ ở trạng thái hover (tiêu đề, "Read More" màu olive);
  trang thật chỉ đổi màu khi rê chuột.

## Không flexbox, không grid

Bố cục dựng bằng kỹ thuật trước thời flexbox: `float` + `calc()`, `flow-root`,
`inline-block` + `font-size: 0`, `line-height` + `vertical-align`,
`position: absolute`.

Mẹo đáng nhớ trên trang này:

- **Tiêu đề hero chạy đè lên ảnh** mà ảnh vẫn là `float`: ảnh có
  `shape-outside: inset(0 0 0 100%)` nên vùng tránh float co về 0, dòng chữ
  không bị ngắt.
- **Khoảng trắng giữa các thẻ `inline-block` vẫn ăn `letter-spacing`** dù cha có
  `font-size: 0` → mỗi hàng `font-size: 0` đều kèm `letter-spacing: 0` (trước đó
  thẻ sản phẩm thứ 3, 4 lệch 1px).
- Hàng "Trending" dời bằng `position: relative; left: …` chứ không dùng
  `margin-left` âm (margin âm nới rộng hàng → thẻ to ra).

## Font

Cả ba font trong Figma đều là font OFL, dùng đúng font gốc, không phải thay.

| Font Figma | Dùng (OFL, tự lưu woff2) | Weight | Vai trò |
| --- | --- | --- | --- |
| Cinzel | Cinzel | 400 | tiêu đề, tên sản phẩm, cột footer |
| Poppins | Poppins | 300, 300 italic, 400, 400 italic, 500 | chữ thân, nav, nút, giá |
| Bebas Neue | Bebas Neue | 400 | logo "FURNICS." |

Figma ghi line-height Cinzel "100%" nhưng hộp chữ cao bằng ascent + descent của
font (1.348em: 108 → 145, 72 → 97, 28 → 38). CSS dùng đúng chiều cao hộp đó.
Chữ thân Figma ghi 180% nhưng dựng dòng cách đúng 29px ở cỡ 16 → dùng 29/16.

## Responsive

Đã kiểm 17 khổ 320 → 2560 và điện thoại xoay ngang (568 / 667 / 844 × 320):
**0 khổ cuộn ngang**, không chữ bị cắt, không ảnh méo. Khổ 320 và 375 kiểm
thêm bằng trình duyệt thật (Chrome headless không cho cửa sổ hẹp hơn ~500px).

| Khổ | Cách xử lý |
| --- | --- |
| < 48rem (điện thoại) | 1 cột, lề 20px; menu thu vào nút ☰ (checkbox + `:has()`); hàng sản phẩm cuộn ngang bằng tay (scroll-snap); 4 dịch vụ 1 cột |
| 48rem – 64rem (tablet) | dịch vụ 2 cột, 3 phòng 3 cột, footer 3 cột dưới logo; lề 40px |
| 64rem – 90rem (laptop) | bố cục desktop theo tỉ lệ khung 1540 (hero/ảnh "About" nổi hai bên, bài viết 3 cột, mũi tên trích dẫn hiện); lề 64px |
| ≥ 90rem | dịch vụ 4 cột, footer 4 cột như Figma |
| 1920 | khớp pixel (số liệu ở trên) |
| > 1920 (2K/4K) | khung nội dung giữ 1540 canh giữa, kẻ ngang chạy hết bề ngang; ảnh không giãn |

- Cỡ chữ và khoảng cách dọc dùng `clamp(min, N/19.2 vw, N px)`: đúng số Figma ở
  1920 trở lên, co dần ở khổ nhỏ.
- Bề ngang cột ở desktop là phân số của khung 1540 (`calc(425 / 1540 * 100%)`).
- Màn cảm ứng: icon header, link footer nới vùng chạm lên 44px trong
  `@media (pointer: coarse)`.
- Số chỉ dùng cho khổ nhỏ ghi nhãn `[ƯỚC]` (Figma không vẽ khổ này).

## Chỗ khác bản thiết kế

- **Ảnh hero là ảnh tạm**: chưa có file export nên cắt từ ảnh chụp Figma
  (`assets/img/hero-wooden-table-set.jpg`), phần chữ "E SET" đè lên ảnh đã được
  xoá bằng nội suy dọc → cành hoa khô sau chữ hơi mờ. Cần ảnh gốc.
- **Chấm slider và mũi tên trích dẫn chỉ để trang trí** (`aria-hidden`): Figma
  chỉ có một slide cho mỗi khối nên không làm chuyển slide.
- **Nút Play** trỏ tạm về `#about-video` (link YouTube trong Figma bị cắt cụt).
- Mũi tên trích dẫn: Figma ghi viền #B1B1B1 nhưng ảnh hiện màu #787D62 → dùng
  màu đo được.
- Trạng thái hover Figma vẽ sẵn (thẻ "Black Sofa Set", bài viết đầu) chỉ hiện
  khi hover; ảnh đối chiếu chụp với CSS ép hover.

## Còn cần từ phía khách

- Ảnh hero "Wooden Table Set" gốc (760 × 682, tốt nhất có bản @2x).
- Ảnh đầy đủ 425 × 548 cho "Pattern Tea Table" và "Sofa Set & Vase Table"
  (hiện chỉ có phần 305px lộ ra trong khung).
- Bản SVG hoặc @2x cho mọi icon (`assets/icon/*.png` đều 1x) và logo đối tác.
- Link video "Since 1990's".
- Đề xuất đổi tên asset sang kebab-case có nghĩa (vd. `Rectangle 168.png` →
  `article-bedroom-pillows.png`) — chờ chị duyệt.
