# CLAUDE.md — Cẩm nang bảo trì "Bộ não thứ hai"

> File này là **schema (lược đồ cấu hình)** của wiki tri thức. Mỗi phiên làm việc mới, Claude PHẢI đọc file này trước khi làm bất cứ việc gì trong thư mục này.
> Vai trò của Claude: **người bảo trì (maintainer)** wiki.

---

## 1. Triết lý

Thay vì mỗi lần hỏi lại đi truy xuất tài liệu thô từ đầu (kiểu **RAG** — Retrieval-Augmented Generation, tức "sinh câu trả lời có tra cứu tài liệu"), hệ thống này **DUY TRÌ MỘT WIKI** liên kết chéo, tích lũy dần.

- Mỗi khi nạp nguồn mới, Claude **ĐỌC** nguồn, **TRÍCH** thông tin then chốt, rồi **TÍCH HỢP** vào wiki hiện có: cập nhật trang thực thể, chỉnh tóm tắt chủ đề, ghi chú mâu thuẫn với dữ liệu cũ.
- Kiến thức được **biên dịch MỘT LẦN** rồi giữ cập nhật — không suy luận lại từ đầu mỗi lần hỏi.
- Wiki là **tài sản bền vững**, lớn dần theo thời gian. Mỗi lần nạp nguồn, mỗi câu trả lời giá trị đều làm wiki giàu hơn.

## 2. Các tầng của hệ thống

| Tầng | Thư mục / file | Ai sở hữu | Quy tắc |
|---|---|---|---|
| 1. Nguồn thô | `raw/` (ảnh, tệp đính kèm trong `raw/assets/`) | Người dùng | **BẤT BIẾN. Chỉ đọc, KHÔNG BAO GIỜ sửa, đổi tên, xoá** — trừ ngoại lệ video gốc ở mục 2.1. Đây là nguồn chân lý. Nguồn chính là các file phụ đề `.srt`. |
| 2. Wiki | `wiki/` | Claude | Claude tạo, cập nhật, giữ liên kết chéo nhất quán. Người dùng có thể đọc và góp ý, nhưng Claude chịu trách nhiệm tính nhất quán. |
| 3. Schema | `CLAUDE.md` (file này) | Người dùng + Claude | Quy tắc tổ chức, quy ước, quy trình. Chỉ sửa khi người dùng đồng ý; ghi thay đổi vào `wiki/log.md`. |
| 4. Sản phẩm | `production/` | Claude (theo lệnh người dùng) | Sản phẩm đầu ra: blog, bài đăng Facebook, kịch bản video, PDF, tài liệu khác. Mọi sản phẩm phải dựa trên nội dung wiki và giữ trích dẫn khi phù hợp. |

Cấu trúc wiki:

```
wiki/
├── index.md          ← danh mục MỌI trang (đọc đầu tiên khi truy vấn)
├── log.md            ← nhật ký chỉ-thêm
├── tong-quan.md      ← trang tổng quan
├── thuc-the/         ← người, tổ chức, địa điểm, sản phẩm, sự kiện…
├── khai-niem/        ← ý tưởng, phương pháp, thuật ngữ
├── tom-tat-nguon/    ← mỗi nguồn trong raw/ có đúng 1 trang tóm tắt
└── so-sanh/          ← bảng so sánh, phân tích đối chiếu
```

### 2.1. Ngoại lệ: xoá video gốc để nhẹ bộ nhớ

Video gốc (`.mp4`, `.mov`, `.mkv`, `.webm`…) trong `raw/` **được phép xoá** khi ĐỦ cả hai điều kiện:
1. Đã có file **`.srt`** (lời chép) cùng tên, cùng thư mục — đây là nguồn chính để trích dẫn.
2. Đã có file **`.mp3`** nén (chỉ tiếng, mono 64 kbps ≈ 0,5 MB/phút) cùng tên, cùng thư mục — để nghe lại khi chép lời không rõ.

Cách làm: chỉ xoá SAU khi nạp xong vào wiki; liệt kê danh sách video + tổng dung lượng sẽ giải phóng và **chờ người dùng xác nhận** mới xoá. Ghi việc xoá vào `wiki/log.md`. File `.srt` và `.mp3` thì vẫn bất biến như mọi nguồn khác.
Trích dẫn mốc thời gian `(nguồn: x.srt, 00:12)` vẫn đúng với file `.mp3` cùng tên.

Các thư mục khác trong kho Obsidian (`00 Hộp thư`, `01 Lô đất`, …, `_Mẫu`, `_Ảnh`) là ghi chú cá nhân của người dùng — **không** thuộc wiki, không tự ý sửa.

## 3. Quy ước ngôn ngữ

- Toàn bộ wiki, schema và sản phẩm viết bằng **TIẾNG VIỆT**.
- Nếu buộc phải dùng thuật ngữ nước ngoài, **luôn kèm chú thích tiếng Việt trong ngoặc ngay lần đầu xuất hiện** trên mỗi trang. Ví dụ: "embedding (vector nhúng)".

## 4. Quy ước trang wiki

1. **Tên file:** chữ thường, không dấu, nối bằng gạch ngang. Ví dụ: `pham-thanh-long.md`. Tiêu đề có dấu đặt trong frontmatter.
2. **Frontmatter YAML** bắt buộc ở đầu mỗi trang:

   ```yaml
   ---
   tieu_de: Phạm Thanh Long
   loai: thuc-the        # thuc-the | khai-niem | tom-tat-nguon | so-sanh | tong-quan | tong-hop
   ngay_tao: 2026-10-11
   ngay_cap_nhat: 2026-10-11
   nguon:
     - ten-file-nguon.srt
   tags: [nhan-vat]
   ---
   ```

   Thư mục theo `loai`: `thuc-the` → `wiki/thuc-the/`; `khai-niem` → `wiki/khai-niem/`; `tom-tat-nguon` → `wiki/tom-tat-nguon/`; `so-sanh` → `wiki/so-sanh/`; `tong-quan`, `tong-hop` → `wiki/`.
3. **Liên kết chéo** bằng cú pháp `[[ten-file-khong-duoi]]` (ví dụ `[[pham-thanh-long]]`). Mỗi trang nên có mục `## Liên quan` ở cuối liệt kê các trang liên kết.
4. **Trích dẫn:** mọi khẳng định rút ra từ nguồn phải ghi `(nguồn: ten-file.srt, 00:12:34)` — kèm mốc thời gian nếu có. Khẳng định không có trích dẫn = suy luận của Claude, phải ghi rõ "(suy luận)".
5. **Nguồn chép lời tự động** (file `.srt` do whisper, tức "máy chép lời", tạo ra từ video) có lỗi chính tả và nhầm số. Khi trích, ghi theo nghĩa đã hiểu và đánh dấu "(suy luận)" nếu phải đoán; chỗ không hiểu được ghi "chép lời không rõ" — tuyệt đối không bịa con số.
6. Khi cập nhật trang, sửa `ngay_cap_nhat` và bổ sung file vào `nguon`.
7. Mâu thuẫn ghi trong khối:

   ```
   > [!warning] Mâu thuẫn
   > Nguồn A nói X (nguồn: a.srt, 00:05:10), nguồn B nói Y (nguồn: b.srt, 00:20:01). Chưa xác định.
   ```

## 5. Quy trình "NẠP NGUỒN" (Ingest — đưa nguồn vào wiki)

Kích hoạt khi người dùng nói: "nạp nguồn …", "đọc file …", "ingest …".

1. **Đọc** file nguồn trong `raw/` (đọc toàn bộ, không sửa). Nguồn là video/ghi âm: chép lời ra `.srt` (whisper, tiếng Việt) và tách tiếng ra `.mp3` nén cùng tên trước — xem mục 2.1.
2. **Trao đổi** với người dùng 3–5 ý chính vừa rút ra; hỏi cần nhấn mạnh gì, bỏ qua gì.
3. **Viết trang tóm tắt** trong `wiki/tom-tat-nguon/` (tên file = tên nguồn dạng không dấu). Gồm: bối cảnh, ý chính có trích dẫn mốc thời gian, thực thể và khái niệm xuất hiện, câu hỏi còn mở.
4. **Cập nhật `wiki/index.md`** — thêm mọi trang mới, sửa tóm tắt một dòng của trang đã đổi.
5. **Cập nhật / tạo các trang thực thể và khái niệm** liên quan khắp wiki; thêm liên kết chéo hai chiều.
6. **Ghi chú mâu thuẫn** nếu nguồn mới trái với nguồn cũ (khối `[!warning] Mâu thuẫn` trên cả hai trang liên quan).
7. **Thêm 1 mục vào `wiki/log.md`**: `## [YYYY-MM-DD] ingest | Tên nguồn` + danh sách trang đã tạo/sửa.
8. (Nguồn video) **Đề nghị xoá video gốc** theo mục 2.1 — chờ người dùng đồng ý.

Lưu ý:
- Một nguồn có thể chạm **10–15 trang**. Đó là bình thường — đừng tiết kiệm liên kết.
- **Mặc định nạp TỪNG nguồn một, có giám sát** (dừng ở bước 2 để người dùng góp ý).
- Hỗ trợ **nạp hàng loạt** nếu người dùng yêu cầu rõ: khi đó bỏ bước 2, làm tuần tự từng nguồn, cuối cùng báo cáo tổng hợp.
- Cập nhật `wiki/tong-quan.md` (số nguồn, chủ đề chính) sau mỗi lần nạp.

## 6. Quy trình "TRUY VẤN" (Query — hỏi đáp dựa trên wiki)

1. Đọc `wiki/index.md` trước để tìm trang liên quan.
2. Đọc các trang đó (và theo liên kết chéo khi cần).
3. Tổng hợp câu trả lời **CÓ TRÍCH DẪN** (trang wiki và/hoặc file nguồn + mốc thời gian).
4. Nếu wiki không đủ, nói rõ khoảng trống; chỉ khi cần mới mở lại `raw/` để kiểm chứng.
5. Nếu câu trả lời có giá trị (so sánh, phân tích, phát hiện mối liên hệ), **đề nghị lưu ngược lại** thành trang wiki mới (`so-sanh` hoặc `tong-hop`) để tích lũy.
6. Thêm mục `## [YYYY-MM-DD] query | Câu hỏi tóm tắt` vào `wiki/log.md` (ghi rõ có lưu trang mới hay không).

## 7. Quy trình "RÀ SOÁT" (Lint — kiểm tra sức khoẻ wiki)

Kích hoạt khi người dùng nói "rà soát", "lint", hoặc định kỳ sau ~5 lần nạp nguồn. Kiểm tra:

1. **Mâu thuẫn** giữa các trang.
2. **Khẳng định lỗi thời** (nguồn mới hơn đã thay đổi thông tin).
3. **Trang mồ côi** — không có liên kết nào trỏ đến.
4. **Khái niệm quan trọng chưa có trang riêng** (được nhắc nhiều nhưng chỉ là chữ thường).
5. **Thiếu liên kết chéo**, liên kết hỏng (trỏ đến trang không tồn tại).
6. **Khoảng trống dữ liệu**; frontmatter thiếu trường; khẳng định thiếu trích dẫn.
7. Trang có trong thư mục nhưng thiếu trong `index.md` (và ngược lại).

Kết quả: báo cáo danh sách vấn đề + đề xuất sửa + **câu hỏi mới nên tìm hiểu** và **nguồn nên tìm thêm**. Chỉ sửa sau khi người dùng đồng ý. Ghi `## [YYYY-MM-DD] lint | Tóm tắt` vào `wiki/log.md`.

## 8. Vai trò của `index.md` và `log.md`

- **`wiki/index.md`** — bản đồ của wiki. Danh mục **MỌI** trang, chia theo hạng mục (Thực thể / Khái niệm / Tóm tắt nguồn / So sánh / Khác), mỗi trang một dòng `- [[ten-file]] — tóm tắt một dòng`. Là điểm vào đầu tiên khi truy vấn. Cập nhật **MỖI** lần nạp nguồn hoặc tạo trang.
- **`wiki/log.md`** — nhật ký **CHỈ-THÊM (append-only)**, theo thời gian. Không bao giờ sửa/xoá mục cũ. Mỗi mục bắt đầu bằng tiền tố nhất quán để grep (tìm kiếm bằng dòng lệnh) được:
  - `## [YYYY-MM-DD] ingest | Tên nguồn`
  - `## [YYYY-MM-DD] query | Câu hỏi`
  - `## [YYYY-MM-DD] lint | Tóm tắt`
  - `## [YYYY-MM-DD] schema | Thay đổi CLAUDE.md`
  - `## [YYYY-MM-DD] production | Tên sản phẩm`

  Xem 5 mục gần nhất: `grep "^## \[" wiki/log.md | tail -5` (PowerShell: `Select-String '^## \[' wiki/log.md | Select-Object -Last 5`).

## 9. Sản phẩm (`production/`)

- Khi người dùng yêu cầu blog, bài Facebook, kịch bản video, PDF…: đọc `index.md` → các trang liên quan → viết sản phẩm vào `production/` với tên file `YYYY-MM-DD-ten-san-pham.md`.
- Ghi `## [YYYY-MM-DD] production | Tên sản phẩm` vào `wiki/log.md`.
- Không đưa thông tin không có trong wiki/nguồn vào sản phẩm mà không báo người dùng.
