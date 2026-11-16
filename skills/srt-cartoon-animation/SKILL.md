---
name: srt-cartoon-animation
extends: srt-whiteboard-animation
description: Kế thừa toàn bộ quy trình srt-whiteboard-animation (7 bước, annotation.json, script render, trạm kiểm soát) nhưng tạo video theo phong cách flat 2D cartoon có màu sắc đầy đủ trên từng vật thể thay vì doodle tối giản. Kích hoạt khi người dùng yêu cầu "flat 2D cartoon", "cartoon màu", "nhân vật có màu sắc", "video hoạt hình màu", "cartoon animation từ SRT" hoặc mô tả phong cách có màu rõ ràng trên từng đối tượng.
---

# Hoạt Hình Flat 2D Cartoon Từ Phụ Đề SRT

Skill này **kế thừa toàn bộ** từ `srt-whiteboard-animation`:
- 7 bước quy trình (phân cảnh → vẽ → annotation → preview → render → ghép)
- Tất cả trạm kiểm soát (Confirmation Checkpoints)
- Cấu trúc `.annotation.json` và toàn bộ lệnh script
- Quy ước thư mục dự án

**Chỉ ghi đè 2 phần:** Quy Chuẩn Thị Giác và Prompt mẫu Bước 2.

---

## [OVERRIDE] Quy Chuẩn Thị Giác Tranh Vẽ

> Phần này thay thế hoàn toàn mục "Quy Chuẩn Thị Giác Tranh Vẽ Bắt Buộc" trong skill gốc.

- **Phong cách & Bố cục:** Flat 2D cartoon — nhân vật và vật thể có hình dạng rõ ràng, nét viền đậm sạch, màu phẳng (flat fill) bên trong. Bố cục thoáng, nền sạch, chừa khoảng trắng giữa các đối tượng để bounding box không chồng lấn.
- **Màu sắc & Chất liệu:**
  - Mỗi vật thể có màu flat riêng biệt, tô đầy vùng bên trong nét viền.
  - Màu đặc trưng gắn với ngữ nghĩa: tiền → xanh lá, lửa → đỏ cam, nước → xanh dương, thiên nhiên → xanh lá, v.v.
  - Nền: trắng sạch `#FFFFFF` hoặc be rất nhạt `#F5EBD7` — không được dùng nền màu sặc sỡ.
  - Nét viền: đen hoặc xám rất đậm, đều nhau, stroke-width nhất quán xuyên suốt cảnh.
- **Nhân vật & Đồ vật:** Hình dạng đơn giản hoá (simplified shapes), tỷ lệ cartoon dễ thương, không cần chính xác giải phẫu. Mắt tròn, tay/chân hình que hoặc hình trụ đơn giản đều được.
- **TUYỆT ĐỐI CẤM (kế thừa từ skill gốc + bổ sung):**
  - Không có chữ viết, con số, nhãn mác trong tranh.
  - Không 3D shading, không gradient phức tạp, không đổ bóng khối.
  - Không photorealism, không ảnh chụp.
  - Không texture chất liệu phức tạp (vân gỗ, da, vải chi tiết...).

---

## [OVERRIDE] Prompt Mẫu Bước 2 — Lệnh Sinh Ảnh Flat 2D Cartoon

> Phần này thay thế prompt mẫu Bước 2 trong skill gốc. Toàn bộ logic Bước 2 khác (DỪNG chờ duyệt, trình bày ảnh, kiểm tra 3 tiêu chuẩn) giữ nguyên.

### Prompt 2A — Lệnh Sinh Ảnh (Gửi Cho AI Hoạ Sĩ)

> Bạn gõ bằng tiếng Việt mô tả nội dung cảnh. AI tự dịch sang tiếng Anh để sinh ảnh.

**Cấu trúc prompt tiếng Việt:**
```
Vẽ tranh 16:9 phong cách flat 2D cartoon, nền trắng sạch.
Bố cục ngang từ trái qua phải gồm [N] cụm:
1. (Trái) [CỤM_1]: Mô tả vật thể, màu sắc cụ thể
2. (Giữa-trái) [CỤM_2]: Mô tả vật thể, màu sắc cụ thể
3. (Giữa-phải) [CỤM_3]: Mô tả vật thể, màu sắc cụ thể
4. (Phải) [CỤM_4]: Mô tả vật thể, màu sắc cụ thể
Các cụm tách bạch, không chồng lên nhau. Tuyệt đối không có chữ số hay chữ viết trong tranh.
```

**Phần AI tự dịch và bổ sung vào prompt kỹ thuật (tiếng Anh):**
```
A 16:9 flat 2D cartoon illustration on a clean white background.
Horizontal layout left to right with [N] distinct groups:
1. (Far left): [CỤM_1 — mô tả + màu]
2. (Middle-left): [CỤM_2 — mô tả + màu]
3. (Middle-right): [CỤM_3 — mô tả + màu]
4. (Far right): [CỤM_4 — mô tả + màu]
Flat 2D cartoon style: bold clean outlines, flat color fill inside shapes,
no shading, no gradient, no 3D rendering, no texture.
Characters simplified: round eyes, simple limbs, cartoon proportions.
Groups clearly separated with generous negative space between them.
STRICTLY NO text, NO numbers, NO letters, NO labels.
Pure 2D flat cartoon aesthetic only.
```

### Prompt 2B — Lệnh Kiểm Duyệt (Khâu 2.4 — giữ nguyên từ skill gốc)

```
"Tôi đã hoàn thành tranh cartoon cho [scene-01: Tên_Cảnh].
Mời bạn kiểm tra:
1. Đúng kịch bản chưa?
2. Bố cục [N] cụm đã tách rời chưa, không chồng lấn?
3. Có chữ số hoặc chữ viết nào không?
4. Màu sắc các vật thể có flat (phẳng, không bóng đổ) không?
Hãy gõ 'DUYỆT' để chuyển sang Bước 3."
```

### Prompt 2C — Lệnh Yêu Cầu Vẽ Lại / Tinh Chỉnh

```
"Bức tranh cần chỉnh sửa:
- [Mô tả vấn đề cụ thể, ví dụ: màu áo nhân vật cần là xanh navy, không phải xanh lá]
- Giữ nguyên bố cục và vị trí các cụm.
- Đảm bảo flat 2D, không bóng đổ, không gradient.
- Tuyệt đối không có chữ số."
```

---

## Điểm Khác Biệt Kỹ Thuật Khi Render (Bước 5 & 6)

> Không cần thay đổi lệnh — script render đọc màu trực tiếp từ ảnh PNG.

| Hạng mục | Whiteboard (skill gốc) | Cartoon (skill này) |
|---|---|---|
| **Màu ảnh đầu vào** | Nét xám + điểm màu nhỏ | Màu flat đầy đủ |
| **Sobel/Canny** | Tìm nét xám đậm | Tìm nét viền đen đậm → nhiều cạnh rõ hơn |
| **Lớp Nét Mực** | Nét xám phác thảo | Nét viền đen đậm, sắc nét |
| **Lớp Tô Màu** | Màu nhạt tan ra nhẹ | Màu flat sặc sỡ hiện rõ sau nét |
| **Lệnh render** | Giữ nguyên | Giữ nguyên — không đổi |

> **Lưu ý:** Ảnh cartoon màu flat thực ra **thân thiện hơn** với Sobel/Canny vì nét viền đen đậm rõ → thuật toán tìm cạnh chính xác hơn, ít bị nhiễu hơn ảnh tả thực.
