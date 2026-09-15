# Peer review — Day 3

| Trường | Giá trị |
| --- | --- |
| Author | `TruongDucThanh` |
| Reviewer | `self-review` |
| Pair ID | `N/A` |
| CVAT version | `N/A` |
| Thời điểm review | `2026-09-15` |

## Danh sách finding

Không có lỗi cần sửa trong ba lượt kiểm tra thủ công. Các frame 46–51 không có
bbox vì đường trống, được xác nhận là `not-a-defect`.

| # | CVAT frame | MOT frame | ID | Loại lỗi | Quan sát + rule áp dụng | Cách sửa đề xuất | Closure: fixed / not-a-defect / needs-review |
| ---: | ---: | ---: | ---: | --- | --- | --- | --- |
| 1 | 46–51 | 46–51 | N/A | Không có bbox | Đường trống, không có xe hợp lệ trong các frame này. | Không sửa. | `not-a-defect` |

## Reviewer checklist

| Hạng mục | PASS / FINDING / N/A | Frame–ID–evidence |
| --- | --- | --- |
| Có tối thiểu 6 track hợp lệ; chỉ gồm xe bốn bánh | PASS | 8 track trong annotation; validator xác nhận |
| Một xe giữ một ID; không reuse ID cho xe khác | PASS | Không phát hiện lỗi trong lượt identity/timeline |
| Occlusion ngắn giữ ID; crossing không đổi ID | PASS | Không phát hiện lỗi trong lượt identity/timeline |
| Entry/exit đúng; không box treo sau khi xe rời khung | PASS | Không phát hiện lỗi trong lượt endpoint/scope |
| Bbox ôm phần nhìn thấy, không đoán phần bị che/ngoài khung | PASS | Không phát hiện lỗi trong lượt endpoint/scope |
| Frame giữa hai keyframe không bị interpolation drift | PASS | Không phát hiện lỗi trong lượt geometry/interpolation |
| Export đúng MOT 1.1; frame bắt đầu từ 1; cột 2 là track ID | PASS | 570 bbox, frame 1..190, 8 track; validator 0 lỗi |
| Mọi finding có cách sửa và closure do tác giả điền | PASS | Finding duy nhất đã đóng `not-a-defect` |

## Self-QC attestation của reviewer

| Lượt | PASS / ĐÃ SỬA / NEEDS-REVIEW | Frame–ID–evidence |
| --- | --- | --- |
| 1 — identity/timeline | PASS | Không phát hiện ID nhảy hoặc reuse |
| 2 — endpoint/scope | PASS | Entry/exit và phạm vi bbox hợp lý |
| 3 — geometry/interpolation | PASS | Bbox ổn định giữa các keyframe |

## Exit ticket

1. Finding quan trọng nhất và rule dùng để kết luận: Không có finding; frame 46–51 không có bbox vì đường trống.
2. Một finding tác giả đóng là `not-a-defect`, kèm lý do (nếu có): Frame 46–51; không có xe hợp lệ xuất hiện.
3. Một rule cần Lab Coach làm rõ (nếu có): Không có.