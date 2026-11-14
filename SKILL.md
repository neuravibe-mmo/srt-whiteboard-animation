---
name: srt-whiteboard-animation
description: Biến phụ đề SRT (hoặc từ ý tưởng/kịch bản lời thoại ban đầu) thành video hoạt hình vẽ tay bảng trắng trên nền giấy vàng be ấm (#F5EBD7). Quy trình: Lên ý tưởng/kịch bản → Xuất/phân tích SRT → Lập chiến lược phân cảnh → Tạo tranh phác thảo thống nhất → Đánh dấu phân vùng theo ngữ nghĩa kịch bản → Tinh chỉnh trên bàn xem trước Web → Render video MP4 với nét vẽ dòng chảy liên tục (stream ink→color). Kích hoạt khi người dùng yêu cầu "làm video vẽ tay từ phụ đề SRT", "chuyển kịch bản thành video bảng trắng", "whiteboard animation từ SRT" hoặc "phân cảnh vẽ tay theo câu thoại".
---

# Hoạt Hình Vẽ Tay Bảng Trắng Từ Phụ Đề SRT (Mask Orchestration + Stream Handwriting)

Hệ thống chuyển đổi phụ đề SRT hoặc kịch bản lời thoại thành video hoạt hình vẽ tay bảng trắng:
- **Biên đạo (Orchestration):** Điều khiển xuất hiện theo từng phân vùng mặt nạ ngữ nghĩa (xuất hiện tuần tự theo mạch kể chuyện, các chi tiết chưa tới thời điểm sẽ được giấu kín hoàn toàn, các vùng giao nhau được bảo vệ bằng `protectedRegions`).
- **Nét vẽ (Stream Drawing):** Đầu ngòi bút lướt vẽ liên tục trong từng vùng mặt nạ cho phép (phác nét mực `ink` $\rightarrow$ phủ màu nhẹ `color`), tất cả các vùng dùng chung một mặt giấy canvas; vùng đã vẽ xong được giữ nguyên vẹn trên giấy.
- **Ngôn ngữ phản hồi:** Giao tiếp, giải thích, phân cảnh và văn bản cấu hình tương tác linh hoạt theo ngôn ngữ người dùng đang sử dụng (mặc định ưu tiên Tiếng Việt, hỗ trợ đầy đủ English và 中文).

Khác biệt với phương pháp lật từng khung hình rời rạc hay xóa khối chữ nhật thô: nét bút của skill này **liên tục và chuyển động tự nhiên**; khác với stream toàn ảnh ngẫu nhiên: skill này **vẽ lần lượt theo từng phân cảnh ngữ nghĩa của phụ đề**, kiểm soát chặt chẽ thứ tự và thời gian xuất hiện của từng phần tử.

---

## Bảng Thông Số Kỹ Thuật Mặc Định

| Hạng mục | Quy chuẩn mặc định |
|---|---|
| **Nền giấy** | Màu be giấy ấm cũ (khuyên dùng `#F5EBD7`); khi render lấy mẫu màu từ 4 góc ảnh nguồn để nhuộm nền, cấm dùng màu trắng tinh. |
| **Cách vẽ** | Nét vẽ lướt liên tục (stream): bắt đầu bằng nét mực phác thảo `ink` $\rightarrow$ phủ màu nhẹ `color`; tỉ lệ thời gian `ink : color = 2 : 1`. |
| **Quỹ đạo nét vẽ** | `--ink-path grid` (lưới zíc-zắc, mặc định, ổn định cao) hoặc `skeleton` (bám khung xương, hợp tranh nét mảnh rõ ràng). |
| **Phong cách tô màu** | `--color-fill contour-wipe` (quét theo đường viền, mặc định) hoặc `brush` (quét cọ dọc theo quỹ đạo). |
| **Vùng chưa vẽ** | Mặt nạ cho phép = hộp `region` trừ đi 「các vùng kế tiếp + `protectedRegions`」; vùng chưa tới lượt bị ẩn hoàn toàn. |
| **Thời lượng cảnh** | `sceneDurationMs` lấy từ khoảng thời gian của đoạn phụ đề tương ứng (khuyên dùng 25–35 giây/cảnh). |
| **Hộp biên tập** | Bàn Preview Web mặc định hiển thị viền các hộp đã đánh số; hộp này chỉ là công cụ căn chỉnh, không thuộc nội dung video. |

---

## Quy Chuẩn Thị Giác Tranh Vẽ Bắt Buộc

Tất cả các hình ảnh nguồn phải tuân thủ nghiêm ngặt cùng một ngôn ngữ thiết kế:

- **Phong cách & Bố cục:** Tranh phác thảo doodle tối giản (tương tự phong cách minh họa Notion). Tập trung diễn tả khái niệm, không tả thực; bố cục thoáng đãng, nền giấy sạch sẽ, chừa nhiều khoảng trắng (negative space), nét vẽ đồng nhất xuyên suốt các cảnh.
- **Màu sắc & Chất liệu:** Nền giấy be ấm `#F5EBD7`, đường nét bút chì xám đậm; chỉ được dùng thêm màu đỏ, cam, hoặc xanh dương với liều lượng rất nhỏ để nhấn mạnh khái niệm. Tuyệt đối không dùng màu có độ bão hòa cao hoặc họa tiết chất liệu phức tạp.
- **Nhân vật & Đồ vật:** Thể hiện bằng đường nét viền đơn giản, tập trung vào mối quan hệ, sự thay đổi hoặc ý tưởng cốt lõi, không đòi hỏi tỷ lệ giải phẫu chân thực hay chi tiết vụn vặt.
- **TUYỆT ĐỐI CẤM:** Không được có bất kỳ chữ viết, từ ngữ, chữ cái, con số, font chữ hay nhãn mác nào xuất hiện sẵn trong tranh; cấm phong cách ảnh chụp, chi tiết 3D đổ bóng khối hoặc bề mặt sơn dầu.
- **Ngoại lệ bàn tay cầm bút:** Nếu người dùng yêu cầu giữ logo trên cán bút của file `drawing-hand.png`, cho phép giữ lại; chi tiết này thuộc công cụ vẽ, không thuộc tranh cảnh.

---

## Trạm Kiểm Soát Bắt Buộc (Confirmation Checkpoints)

> [!IMPORTANT]
> **Quy tắc bất di bất dịch:** Sau khi hoàn thành mỗi bước lớn, **bắt buộc DỪNG LẠI và CHỜ XÁC NHẬN MINH BẠCH** từ người dùng mới được tiến hành bước tiếp theo.
> - Tuyệt đối không tự ý sinh ảnh, sinh file annotation, render video hoặc gộp file khi người dùng chưa đồng ý bước trước.
> - Không xem sự im lặng hay phê duyệt chung chung trước đó là sự xác nhận.
> - **Ngoại lệ duy nhất:** Sau khi tạo xong file cấu hình `.annotation.json`, hệ thống được phép tự động mở ngay trang `assets/preview.html` trên trình duyệt để người dùng xem trước (đây là phần bàn giao của Bước 3).

---

## Quy Trình Làm Việc Hoàn Chỉnh (7 Bước)

### 1. Từ Ý Tưởng Đến Kịch Bản, File SRT & Phân Cảnh (Không tạo ảnh)
- **1.1 Ý tưởng & Kịch bản thoại:** Nếu người dùng bắt đầu từ con số 0, hỗ trợ lên dàn ý và viết kịch bản thoại ngắn gọn 25–30s (khoảng 60–80 từ), cấu trúc: `Hook mở đầu → Giải pháp cốt lõi → Kêu gọi hành động`.
- **1.2 Thu âm & Xuất file SRT:** 
  - *Chuẩn sản xuất:* Hướng dẫn thu âm rồi xuất file `.srt` chuẩn 100% qua CapCut/Premiere/Whisper.
  - *Làm nhanh bằng AI:* Ước lượng mốc thời gian `startMs --> endMs` chuẩn 25–30s.
  - *Nếu đã có sẵn file SRT:* Bỏ qua 1.1 và 1.2, đi thẳng vào 1.3.
- **1.3 Phân tích SRT & Lập phân cảnh:** Chạy `scripts/parse_srt.py <phụ_đề.srt> --target-sec 30` để phân cảnh (25–35s/màn). Lập danh sách 3–4 cụm hình ảnh theo mạch: `Bối cảnh → Nhân vật/Chủ thể → Hành động → Kết quả`.
- **1.4 Trạm kiểm duyệt phân cảnh (dạng text):** Xuất bảng mô tả phân cảnh bằng chữ. **DỪNG LẠI và chờ người dùng duyệt.**

### 2. Tạo Tranh Vẽ Nét (Line Art)
- Chỉ sau khi người dùng duyệt phân cảnh ở Bước 1, tiến hành tạo ảnh phác thảo tỉ lệ 16:9, nền be `#F5EBD7`, chừa nhiều khoảng trắng quanh từng đối tượng, tuyệt đối không có chữ.
- **DỪNG LẠI, hiển thị ảnh và chờ người dùng xác nhận.**

### 3. Đọc Kịch Bản, Xem Tranh, Đánh Dấu Phân Vùng & Mở Bàn Preview
- Đọc kỹ mốc câu thoại và quan sát vị trí thực tế của từng đối tượng trên ảnh (lấy kích thước pixel nguyên bản `canvas.width` và `canvas.height`).
- Gán tọa độ bounding box (`x, y, width, height`), thứ tự vẽ (`sequence`), mốc thời gian (`startMs`, `durationMs`) và nội dung câu thoại tương ứng vào file `<tên_ảnh>.annotation.json`.
- Tự động mở tệp `assets/preview.html` trên trình duyệt mặc định để người dùng xem trước.
- **DỪNG LẠI và chờ người dùng duyệt cấu hình phân vùng.**

### 4. Tạo Ảnh Sơ Đồ Kiểm Tra Phân Vùng
- Chạy `scripts/render_annotation_preview.py` để xuất ảnh sơ đồ các hộp chữ nhật có đánh số và mũi tên hướng vẽ, kiểm tra xem các vùng có nằm trọn trong canvas và bao kín chủ thể hay không.
- **DỪNG LẠI và chờ người dùng duyệt ảnh sơ đồ.**

### 5. Tinh Chỉnh Trên Bàn Preview Web
- Người dùng (hoặc AI hỗ trợ) điều chỉnh trực tiếp các cạnh hộp chữ nhật, căn thời gian bắt đầu/kết thúc khớp từng câu nói trên giao diện `preview.html` và nhấn Lưu.
- **DỪNG LẠI và chờ xác nhận thông số cuối cùng.**

### 6. Render Video Hoạt Hình Bút Vẽ Tay (MP4)
- Chạy script render chất lượng cao:
  ```bash
  python scripts/render_stream_whiteboard.py <ảnh.png> <cấu_hình.json> <kết_quả.mp4>
  ```
- Kiểm tra ngẫu nhiên 3 thời điểm: lúc mở màn, lúc đang vẽ giữa chừng và khung hình hoàn thiện cuối cùng.
- **DỪNG LẠI và bàn giao video cho người dùng duyệt.**

### 7. Ghép Nối Đa Cảnh & Hoàn Thiện Âm Thanh
- Nếu video gồm nhiều phân cảnh (`scene-01`, `scene-02`...), dùng `scripts/merge_scenes.py` để ghép liền mạch theo thứ tự.
- Ghép file thu âm giọng đọc (hoặc giọng AI) và hiệu ứng tiếng ngòi bút vào video bằng FFmpeg.
- **Bàn giao thành phẩm cuối cùng.**

---

## Quy Ước Thư Mục Dự Án

Trong dự án của người dùng, các tệp nên được tổ chức thống nhất:

```text
projects/<tên_dự_án>/
  <tên_dự_án>.srt
  scene-01-<tên_cảnh>.png
  scene-01-<tên_cảnh>.annotation.json     # Cùng tên với file PNG
  scene-01-<tên_cảnh>.mp4                 # Video thành phẩm từng cảnh
  final_video.mp4                         # Video hoàn thiện có tiếng
```

---

## Cấu Trúc File Cấu Hình Mẫu (`.annotation.json`)

```json
{
  "sceneId": "scene-01",
  "canvas": { "width": 1376, "height": 768 },
  "storyBasis": "Tóm tắt ngắn gọn diễn biến câu chuyện trong cảnh",
  "sceneDurationMs": 26000,
  "elements": [
    {
      "id": "element-1",
      "label": "Tên đối tượng",
      "sequence": 1,
      "narrativeRole": "Bối cảnh / Nhân vật / Hành động / Kết quả",
      "subtitle": "Nội dung câu phụ đề tương ứng trong file SRT",
      "type": "character",
      "region": { "x": 40, "y": 220, "width": 380, "height": 350 },
      "reveal": {
        "direction": "top_to_bottom",
        "startMs": 500,
        "durationMs": 4700,
        "maskPaddingPx": 20,
        "protectedRegions": []
      },
      "handPath": { "start": [230, 240], "end": [230, 550], "easing": "easeInOut" }
    }
  ]
}
```

---

## Hướng Dẫn Dòng Lệnh Thực Thi

Chạy toàn bộ script bằng Python trong môi trường ảo `.venv`:

1. **Khởi tạo môi trường (Lần đầu):**
   ```bash
   python scripts/prepare_env.py
   ```
2. **Phân tích phụ đề SRT:**
   ```bash
   python scripts/parse_srt.py <phụ_đề.srt> --target-sec 30 --min-sec 25 --max-sec 35
   ```
3. **Xuất ảnh sơ đồ phân vùng kiểm tra:**
   ```bash
   python scripts/render_annotation_preview.py <ảnh.png> <cấu_hình.json> <ảnh_preview.png>
   ```
4. **Mở bàn Preview trực quan trên Web:**
   ```bash
   open assets/preview.html
   ```
5. **Render video hoạt hình vẽ tay:**
   ```bash
   python scripts/render_stream_whiteboard.py <ảnh.png> <cấu_hình.json> <video_ra.mp4>
   ```
6. **Ghép nối nhiều phân cảnh:**
   ```bash
   python scripts/merge_scenes.py --inputs scene1.mp4 scene2.mp4 --output full_video.mp4
   ```
