# Mini annotation guideline — Ngày 3 (tracking)

> Điền file này **trong lúc gán nhãn**, không phải sau khi xong. Mỗi lần bạn dừng
> lại nghĩ "cái này tính sao nhỉ?" thì đó là một dòng phải ghi vào đây.
>
> Đây là tài liệu mà người gán nhãn tiếp theo sẽ đọc để làm giống bạn. Nếu hai
> người trong nhóm gán khác nhau, gần như luôn là vì file này chưa nói rõ — chứ
> không phải vì ai kém.

Nhóm / tên: `TruongDucThanh`
Clip: `clip_01`, `clip_02`

---

## 1. Phạm vi: gán cái gì, không gán cái gì

Một lớp duy nhất: **`vehicle`** — xe bốn bánh (xe con, van, xe buýt, xe tải).

| Gán | Không gán |
| --- | --- |
| xe con, SUV, taxi, xe bán tải | người đi bộ |
| van, minivan | xe đạp |
| xe buýt, minibus | **xe máy / mô tô** |
| xe tải, xe đầu kéo | xe trong ảnh quảng cáo, trong gương, dưới bóng nước |

Bổ sung của nhóm (nếu có): Chỉ gán xe bốn bánh nhìn thấy rõ; không gán xe máy dù đang di chuyển cùng làn.

## 2. Luật ID — phần quan trọng nhất

| Tình huống | Luật của nhóm | Vì sao |
| --- | --- | --- |
| Xe bị che một phần rồi hiện lại | giữ nguyên ID nếu bị che **dưới 25 frame** (mặc định của lab: 25 frame = 2 giây @ 12.5 fps) | Vẫn là cùng một xe trong quãng đời liên tục. |
| Xe bị che lâu hơn ngưỡng trên | kiểm tra vị trí, hướng chuyển động và tạo track mới nếu không còn đủ bằng chứng nhận dạng | Tránh nối nhầm hai xe giống nhau. |
| Xe rời khung hình rồi quay lại | mặc định: **track mới** | Lần xuất hiện sau được xem là một quãng đời mới nếu xe đã rời khung hoàn toàn. |
| Hai xe cắt nhau / chồng lên nhau | giữ ID theo quỹ đạo trước và sau crossing; không đổi ID chỉ vì bbox chạm nhau | Identity quan trọng hơn việc bbox tạm thời gần nhau. |

## 3. Luật bbox

| Tình huống | Luật của nhóm |
| --- | --- |
| Xe bị cắt bởi rìa ảnh | bbox chạm đúng rìa, không đoán phần ngoài ảnh |
| Xe bị xe khác che một phần | bbox ôm phần **nhìn thấy được** |
| Xe vừa xuất hiện, còn rất nhỏ / rất mờ | bắt đầu track từ frame đầu tiên xác định được là xe bốn bánh; không đoán trước khi đủ bằng chứng |
| Xe đang đỗ, không di chuyển | vẫn giữ track nếu xe còn nhìn thấy; chỉ `outside` khi xe rời khung hoặc biến mất hoàn toàn |
| Keyframe đặt dày ở đâu | đặt dày quanh crossing, occlusion, rìa ảnh và các đoạn chuyển động nhanh; kiểm tra thêm giữa hai keyframe xa |

## 4. Ít nhất ba ca mơ hồ đã gặp thật

Ghi **frame cụ thể** và **ID cụ thể**, không ghi chung chung.

### Ca 1
- Clip / frame / ID: `clip_01 / 46–51 / N/A`
- Tình huống: Các frame không có bbox nào.
- Quyết định: Không gán bbox.
- Lý do: Đường trống, không có xe bốn bánh hợp lệ; validator cảnh báo nhưng đây là `not-a-defect`.

### Ca 2
- Clip / frame / ID: `clip_01 / 149–151 / ID 4`
- Tình huống: Xe rời khung nhưng track còn bbox treo.
- Quyết định: Bấm `outside` ở frame xe rời khung.
- Lý do: Không được giữ bbox ở vùng không còn vật thể.

### Ca 3
- Clip / frame / ID: `clip_01 / 89, 93, 99, 104–106 / ID 5`
- Tình huống: Interpolation làm bbox lệch khỏi xe ở giữa keyframe.
- Quyết định: Thêm keyframe và kéo bbox theo phần xe nhìn thấy.
- Lý do: Giữ bbox khít với vật thể, không sửa bằng cách đoán phần bị che.

## 5. Sửa gì sau khi chấm với gold và sau khi kiểm chéo

Luật nào trong file này hoá ra còn thiếu hoặc còn mơ hồ? Viết lại cho rõ:

- Sau khi đối chiếu gold, phải kiểm tra toàn bộ quãng đời của track, không chỉ số track và validator.
- Các bbox treo sau endpoint phải được đóng bằng `outside`; các đoạn interpolation dài cần thêm keyframe tại vùng IoU thấp.
