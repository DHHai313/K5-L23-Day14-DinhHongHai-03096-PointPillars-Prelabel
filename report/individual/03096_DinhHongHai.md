# Báo cáo Day 14 — Đinh Hồng Hải — 03096

- Mã học viên dùng với `--student`: `096`
- `seed_tag` trong `frames_A.txt`: `393590736f`

## 1. Dự đoán trước sweep (bước 5b)

- Tham số tôi nghĩ giảm công sửa nhiều nhất: tăng **`score_thr`** (lên khoảng `0.3`–`0.4`), kết hợp lọc nhẹ **`min_points`** (khoảng `5`).
- Vì sao (nhìn ảnh BEV tập A: box thừa nhiều hay box sót nhiều?):
  - Quan sát 3 ảnh 7-camera và BEV trong `d14_096/png_A/` (`s01f33`: 32 box, `s02f20`: 28 box, `s04f12`: 27 box — tổng 87 box) và kết quả đo cấu hình gốc ở bước 5 (**GT 40, dự đoán 87, khớp 33, xóa 54, thêm 7, sửa 2 → chi phí 24.2**), **box thừa (false positive) chiếm áp đảo** (`54 × 0.3 = 16.2`, chiếm ~67% tổng chi phí sửa).
  - Cụ thể: ở `s01f33` (xóa 23, thêm 1) có rất nhiều box `Car`/`Cyclist` vẽ nhầm lên bụi cây, dải phân cách và hàng xe đạp dựng trên vỉa hè; ở `s02f20` (xóa 16, thêm 3) có nhiều box thừa trên vỉa hè và gần hàng rào; ở `s04f12` (xóa 15, thêm 3) có nhiều box thừa dính vào hàng rào tôn xanh và dải phân cách.
  - Do cấu hình gốc đặt `score_thr = 0.1` và `min_points = 0` quá thấp, các cụm điểm nhiễu từ cây/hàng rào tạo ra rất nhiều box điểm tin cậy thấp hoặc ít điểm LiDAR. Vì vậy tôi dự đoán tăng `score_thr` ở mức vừa phải sẽ loại được lượng lớn box thừa (giảm mạnh phần `0.3 × Xóa`) mà chưa làm sót thêm quá nhiều xe thật (tránh tăng nặng phần `1.0 × Thêm`).

## 2. Tập A: sửa tay và tune

| Cấu hình | Xóa | Thêm | Sửa | Chi phí |
|---|---:|---:|---:|---:|
| Gốc | 54 | 7 | 2 | 24.2 |
| `best_config.json` | | | | |

- Tham số đã đổi trong `best_config.json`: ...
- Kết quả leave-one-out (dán các dòng `scripts/pp_sweep.py` in ra): ...
- Cấu hình có ổn định trên từng frame không? Vì sao tôi vẫn chọn (hoặc không chọn) nó: ...

## 3. Trước/sau (bước 8)

![so sánh](../../d14_096/compare/<frame>.jpg)

- `scripts/pp_compare.py` báo giảm (hoặc tăng) ...% công sửa trên tập A.
- Dự đoán ở mục 1 đúng hay sai? Giải thích bằng số box xóa/thêm/sửa: ...

## 4. Tập B

Số đo từ `scripts/pp_prelabel.py --frames d14_096/frames_B.txt --config d14_096/best_config.json --gt d14_096/ref_B.json`:

| Xóa | Thêm | Sửa | Chi phí |
|---:|---:|---:|---:|
| | | | |

- Loại box tốn công sửa nhất ở tập B: ...

## 5. Giới hạn

- Nêu ít nhất 2 giới hạn (ví dụ: chỉ 3 frame tune; model KITTI không có Truck/Bus; chi phí là công thức, chưa phải thời gian sửa thật): ...
