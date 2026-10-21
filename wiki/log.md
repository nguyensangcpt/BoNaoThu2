---
tieu_de: Nhật ký wiki
loai: tong-quan
ngay_tao: 2026-10-11
ngay_cap_nhat: 2026-10-11
nguon: []
tags: [nhat-ky]
---

# Nhật ký wiki

> Nhật ký **CHỈ-THÊM (append-only)** theo thời gian — không sửa, không xoá mục cũ. Mục mới thêm ở CUỐI file.
> Mỗi mục bắt đầu bằng tiền tố: `## [YYYY-MM-DD] ingest | …`, `query | …`, `lint | …`, `schema | …`, `production | …`
> Lấy 5 mục gần nhất: `grep "^## \[" wiki/log.md | tail -5`

## [2026-10-11] schema | Khởi tạo hệ thống hôm nay
- Tạo `CLAUDE.md` (schema), các thư mục `raw/`, `raw/assets/`, `wiki/` (`thuc-the/`, `khai-niem/`, `tom-tat-nguon/`, `so-sanh/`), `production/`.
- Tạo `wiki/index.md`, `wiki/log.md`, `wiki/tong-quan.md`.
- Số nguồn trong `raw/`: 0 — chờ người dùng bổ sung file `.srt`.

## [2026-10-11] schema | Đổi chủ đề sang BĐS + quy ước nguồn chép lời tự động
- Không có nguồn "IPS 16" trên máy; theo lựa chọn của người dùng, chủ đề wiki đổi thành bất động sản / video review BĐS.
- Thêm quy ước vào `CLAUDE.md`: nguồn `.srt` chép lời tự động có lỗi chính tả, khi hiểu lại phải ghi "(suy luận)".

## [2026-10-11] ingest | video-mau-1.srt, video-mau-2.srt, video-mau-3.srt, video-mau-4.srt (nạp hàng loạt)
- Nguồn: chép lời bằng whisper (máy chép lời tự động) từ 4 video trong `video mẫu/`.
- Tạo tóm tắt nguồn: [[video-mau-1]], [[video-mau-2]], [[video-mau-3]], [[video-mau-4]].
- Tạo thực thể: [[lo-203m2-gan-da-lat]], [[lo-1000m2-ngay-da-lat]], [[lo-337m2-gan-da-lat]], [[nha-dat-120m2-le-trong-tan]], [[co-gai-da-lat]], [[mai-an]], [[da-lat]], [[duong-le-trong-tan]].
- Tạo khái niệm: [[cau-truc-video-review-bds]], [[cau-chot-keu-goi]], [[tho-cu]], [[mat-tien]], [[ha-tang]], [[phap-ly-so-hong]], [[dong-tien-cho-thue]].
- Tạo so sánh: [[so-sanh-4-video-mau]]. Cập nhật [[index]], [[tong-quan]].
- Mâu thuẫn ghi nhận: video 4 — giá 2 tỷ/120 m² ≈ 16,7 tr/m² nhưng nói "24 triệu/m²".

## [2026-10-12] ingest | Thư mục BĐS-DONG-TIEN (5 video, nạp hàng loạt)
- Chép lời 5 video trong `raw/BĐS-DONG-TIEN/` bằng whisper → `dong-tien-1.srt` … `dong-tien-5.srt` (đặt cùng thư mục, không sửa video gốc).
- Tạo tóm tắt nguồn: [[dong-tien-1]], [[dong-tien-2]], [[dong-tien-3]], [[dong-tien-4]], [[dong-tien-5]].
- Tạo thực thể: [[hoa-lac]], [[khu-do-thi-thanh-ha]], [[nha-26-ngo-18-luong-ngoc-khuyen]].
- Tạo khái niệm: [[ty-suat-cho-thue]], [[hang-cat-lo]], [[giay-phep-xay-dung-va-pccc]], [[moi-gioi]].
- Tạo so sánh: [[so-sanh-ty-suat-dong-tien]].
- Cập nhật: [[dong-tien-cho-thue]] (viết lại, 2 mô hình), [[cau-chot-keu-goi]] (thêm kiểu "mời vào nhóm"), [[cau-truc-video-review-bds]] (thêm dạng video chuyên gia), [[phap-ly-so-hong]], [[index]], [[tong-quan]].
- (Ghi chú 2026-10-21: video gốc của thư mục này đã được người dùng xoá trước khi có quy tắc 2.1 — chỉ còn `.srt`, không có `.mp3`.)
- Mâu thuẫn ghi nhận: dong-tien-1 nêu tỷ suất lúc 10–12%, lúc 12–15%, lúc 10–15%; ví dụ cụ thể chỉ ≈ 9,6%. Góc nhìn khác nhau: dong-tien-1 "mua rẻ tự xây" vs dong-tien-2 "chấp nhận mua đắt mà đúng luật".

## [2026-10-21] schema | Cho phép xoá video gốc sau khi có .srt + .mp3
- Thêm mục 2.1 vào `CLAUDE.md`: video gốc trong `raw/` được xoá khi đã có `.srt` và `.mp3` nén cùng tên, sau khi nạp xong và người dùng xác nhận.
- Thêm bước 8 vào quy trình nạp nguồn: đề nghị xoá video gốc.
