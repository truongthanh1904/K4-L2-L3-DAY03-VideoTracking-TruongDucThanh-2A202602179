# Báo cáo Ngày 3 — Tracking Annotation

Họ tên / nhóm: `TruongDucThanh`
Ngày: `2026-09-15`

---

## 1. Quá trình gán nhãn

| Mục | Giá trị |
| --- | --- |
| Công cụ | CVAT |
| Thời gian gán `clip_02` (warm-up) | `N/A` |
| Thời gian gán `clip_01` | `N/A` |
| Số track đã vẽ trong `clip_01` | `8` |
| Số keyframe trung bình mỗi track | `N/A` |

Ba tình huống khó nhất khi gán clip này, và bạn xử lý thế nào:

1. Xe xuất hiện và rời khung ở các thời điểm khác nhau: giữ ID trong quãng nhìn thấy và dùng `outside` khi xe rời khung.
2. Bbox bị trôi giữa các keyframe: kiểm tra các frame giữa và thêm keyframe tại vùng chuyển động nhanh.
3. Track bị thiếu và bbox treo sau khi xe rời khung: đối chiếu visualization, sửa trong CVAT, export lại rồi chạy validator và evaluator.

## 2. Tự kiểm và kiểm chéo

Ba lượt tua bắt được gì (lượt 1 nhìn ID, lượt 2 frame đầu/cuối, lượt 3 frame giữa):

- Lượt 1: Không phát hiện ID nhảy hoặc reuse; có 8 track hợp lệ.
- Lượt 2: Kiểm tra frame đầu/cuối và sửa các endpoint theo kết quả đối chiếu.
- Lượt 3: Kiểm tra geometry/interpolation; các frame 46–51 không có bbox vì đường trống.

Kiểm chéo với: `self-review`. Chi tiết ở `reports/review_partner.md`.
Số lỗi bạn tìm được trong bản của bạn ấy: `N/A`. Số lỗi bạn ấy tìm được trong bản của bạn: `0`.

Ca nào hai người quyết khác nhau, và luật nào còn thiếu trong `GUIDELINE_MINI.md`?

Không có peer độc lập; không ghi nhận bất đồng chưa giải quyết.

## 3. Pre-gold lock và chấm trước/sau rework

Snapshot cuối được khóa tại `evidence/pre-gold/clip_01_final/` sau rework. Snapshot độc lập trước đó vẫn được giữ tại `evidence/pre-gold/clip_01_rework/`.

| Evidence | Giá trị |
| --- | --- |
| SHA-256 snapshot cuối | `5dabf1f936f257f2cd7a0bed77750733f3512b582b2d142c47397103f9aab8a7` |
| Thời điểm khóa | `2026-09-15T05:04:48.980258+00:00` |
| Số row / frame / track snapshot cuối | `570 / 190 / 8` |

| | HOTA | DetA | AssA | LocA | IDF1 | MOTA | MOTP | FP | FN | IDSW |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Bản pre-gold rework | 0.629 | 0.510 | 0.776 | 0.864 | 0.730 | 0.551 | 0.855 | 32 | 225 | 0 |
| Sau rework | 0.788 | 0.774 | 0.802 | 0.865 | 0.936 | 0.873 | 0.857 | 35 | 38 | 0 |

Qua cổng (`IDF1 >= 0.80`, `MOTA >= 0.75`, `MOTP >= 0.70`): **có**

Sau khi đọc danh sách lỗi, bạn đã sửa cụ thể những gì:

| Loại lỗi | Frame | ID | Đã sửa thế nào |
| --- | --- | --- | --- |
| Bỏ sót track | `1–190` | `1` | Bổ sung track xe bị thiếu trong CVAT |
| Thiếu đoạn / bbox trôi | `79–138`, các frame `89, 92, 93, 99, 104, 105, 106, 112–114` | `5, 6` | Bổ sung keyframe và kéo bbox theo phần xe nhìn thấy |
| Bbox treo | `149–151`, `169–171` | `4, 8` | Đặt `outside` đúng frame xe rời khung |

## 4. Kết quả model: ByteTrack control vs ReID treatment

Cấu hình từ `outputs/model_run_config.json`:

| Mục | Giá trị |
| --- | --- |
| Python / ultralytics / torch / lap | `3.13.13 / 8.4.145 / 2.14.0+cpu / 0.5.13` |
| weights / hai tracker | `yolo26n.pt / bytetrack.yaml / configs/trackers/botsort-reid.yaml` |
| conf / IoU / imgsz / classes | `0.25 / 0.7 / 960 / car,bus,truck (2,5,7)` |
| device | `cpu` |

| So sánh | HOTA | DetA | AssA | LocA | IDF1 | MOTA | MOTP | FP | FN | IDSW |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| bạn vs gold | 0.788 | 0.774 | 0.802 | 0.865 | 0.936 | 0.873 | 0.857 | 35 | 38 | 0 |
| ByteTrack control vs gold | 0.709 | 0.649 | 0.776 | 0.846 | 0.875 | 0.749 | 0.823 | 88 | 54 | 2 |
| BoT-SORT + ReID vs gold | 0.763 | 0.711 | 0.820 | 0.872 | 0.900 | 0.792 | 0.860 | 91 | 26 | 2 |
| ReID vs bạn | 0.749 | 0.692 | 0.814 | 0.850 | 0.897 | 0.784 | 0.836 | 95 | 27 | 1 |

## 5. Phân tích — năm câu hỏi

**1. MOTA của bạn cao hơn hay thấp hơn IDF1?**

MOTA `0.873` thấp hơn IDF1 `0.936`. Với ByteTrack, MOTA `0.749` thấp hơn IDF1 `0.875`; ReID cũng có MOTA `0.792` thấp hơn IDF1 `0.900`. MOTA cộng FP, FN và ID switch theo frame nên không phản ánh đầy đủ chất lượng identity trên toàn quãng đời; một lỗi ID có thể bị MOTA phạt ít hơn tác động của nó lên IDF1/AssA.

**2. ByteTrack và ReID khác nhau thế nào?**

ReID tăng AssA từ `0.776` lên `0.820`, IDF1 từ `0.875` lên `0.900`, HOTA từ `0.709` lên `0.763`, và MOTA từ `0.749` lên `0.792`. Cả hai có 2 ID switch theo gold. ReID giảm FN `54 -> 26` nhưng tăng FP `88 -> 91`. Đây là so sánh hai implementation tracker, không cô lập causal effect của ReID.

**3. DetA, FP và FN đổi thế nào?**

ReID tăng DetA `0.649 -> 0.711` và giảm FN `54 -> 26`, cho thấy coverage detection/association tốt hơn. FP tăng nhẹ `88 -> 91`, nên treatment vẫn tạo thêm một số bbox không khớp gold. Lỗi còn lại nghiêng về FP và association hơn là bỏ sót nghiêm trọng.

**4. Một chỗ bạn đúng và ReID sai:**

Ở các frame `46–51`, annotation có chủ đích không có bbox vì đường trống; cảnh báo validator là `not-a-defect`. Model có thể tạo detection ở vùng rìa trong các đoạn khác, nhưng gold là chuẩn adjudication nên các bbox đó được tính là FP.

**5. Một chỗ ReID làm bạn xem lại annotation:**

So sánh ReID với annotation ở frame `110`, track người `6` đang tương ứng ID `28` trong annotation nhưng ReID dùng ID `31`. Đây là một bất đồng association; cần xem motion/occlusion trực tiếp, không dùng model làm đáp án thay cho rule CVAT.

## 6. Nếu phải gán thêm 10 clip nữa

Ghi ngay frame bắt đầu/kết thúc của mỗi track, đặt keyframe dày hơn quanh crossing và chuyển động nhanh, và ghi riêng các frame đường trống để phân biệt với frame bỏ sót. Giữ quy trình: Save, reload, validator, visualization, peer review, rồi mới lock pre-gold.

## 7. Tệp đã nộp

- [x] `annotations/clip_01/gt.txt`
- [x] `annotations/clip_02/gt.txt`
- [x] `evidence/pre-gold/clip_01_final/gt.txt` và `manifest.json`
- [ ] `GUIDELINE_MINI.md` đã điền
- [x] `outputs/eval_vs_gold.json`
- [x] `outputs/model_bytetrack_clip_01.txt`
- [x] `outputs/model_reid_clip_01.txt`
- [x] `outputs/model_run_config.json`
- [x] `outputs/eval_bytetrack_vs_gold.json`, `outputs/eval_reid_vs_gold.json`, `outputs/eval_reid_vs_me.json`
- [x] `reports/review_partner.md`
- [x] `reports/REPORT.md`