# Báo cáo Day 13 — Phan Hải Đăng, 2A202603229

## 1. Kết luận trước
- **Thông tin bài làm:** Mã số `2A202603229` · Task cá nhân `D13-2A202603229` · Bộ 10 frame được giao: `s01f36, s01f59, s02f15, s02f33, s03f04, s03f19, s04f02, s04f46, s05f01, s05f32`.
- **Môi trường thực hiện:** Windows 11 · Trình duyệt Microsoft Edge / Google Chrome · CVAT local Server/UI phiên bản `v2.74.1` (kết nối qua `cvat-sdk 2.82.0`).
- **Tổng kết kết quả:** Toàn bài vẽ tổng cộng **64 cuboid** (100% dạng `Shape`, `0` dạng `Track`), trong đó `55 cuboid` nằm trong bán kính `30.00 m`. Các frame có số lượng ít nhất là `s02f15, s02f33, s04f02, s05f01, s05f32` (mỗi frame `5 cuboid`) do ưu tiên gán kỹ các phương tiện ở gần (`≤ 30.00 m`) có cụm điểm rõ ràng và chủ động bỏ qua các cụm điểm quá thưa ở xa trên `40.00 m`. Lệnh `selfcheck` ghi nhận **9/64 cuboid** có cảnh báo gợi ý; tôi tin tưởng nhất ở độ cao đáy chạm mặt đường (sai lệch trung vị chỉ `0.04 m`) và hướng `yaw`, trong khi phần khó và dễ sai số nhất là ước lượng chiều dài của các xe ở xa `30.00–46.53 m` chỉ thấy điểm phản xạ ở một đầu xe.

- **Cách thao tác trên CVAT 3D:**
  1. **Đổi góc nhìn:** Kéo chuột trái trên khung 3D lớn để xoay nghiêng hoặc nhìn thẳng góc `90°` từ trên xuống bao quát `360°`, kéo chuột phải để dời tâm nhìn và lăn con lăn để phóng to/thu nhỏ.
  2. **Vẽ và chỉnh cuboid:** Chọn nhãn rồi bấm chế độ **Shape** (không dùng Track), nháy đúp chuột đặt hộp lên cụm điểm; căn tâm, chiều dài, chiều rộng và xoay góc `yaw` trên khung **Top**, sau đó chỉnh chiều cao và hạ đáy chạm mặt đường trên khung **Side** và **Front** (không xoay ở Side/Front để tránh nghiêng hộp).
  3. **Kiểm hướng và đối chiếu ảnh camera:** Tích chọn `Appearance → Cuboid orientation` để căn vạch đỏ ngắn (`+X` của hộp) chỉ đúng về đầu xe, kết hợp chạy `python scripts/lab.py preview` để kiểm tra hình chiếu 12 cạnh hộp trên cả 7 ảnh camera (`cam01` đến `cam09`).

## 2. Đọc frame (Bước 2)
Hai frame đọc kỹ (`s01f36` và `s01f59`). Ảnh minh hoạ trong gói ZIP: `t1-s01f36-3d.png`, `t1-s01f36-top.png`, `t1-s01f59-3d.png`, `t1-s01f59-top.png`.

| Frame | Phía trước xe ở cạnh nào của ảnh nhìn từ trên xuống; +x, +y chỉ về đâu | Mặt đường (phẳng/dốc; thấp hơn LiDAR ~ m) | Vật xa nhất đọc được (~ m) | Loại vật thấy | Vật phía sau / hai bên | Cụm chưa đọc được |
|---|---|---|---|---|---|---|
| `s01f36` | Phía trước xe (`-y`) hướng lên **cạnh trên** ảnh; trục `+x` (bên trái xe) chỉ sang **cạnh trái**; trục `+y` (phía sau xe) chỉ xuống **cạnh dưới** | Tương đối phẳng ở lòng đường chính (vỉa hè bên phải cao hơn lòng đường ~`0.40 m`); thấp hơn LiDAR khoảng `1.72 m` (`z ≈ -1.72 m`) | Khoảng `46.53 m` (xe con phía sau và phía trước còn thấy vệt điểm đuôi/đầu xe) | `Car`, `Truck`, `Cyclist` | Phía sau (`+y`, `cam09`) có xe con cách `10.41 m` và `46.53 m`, xe tải sau-trái cách `30.28 m`; bên phải (`-x`, `cam07/cam08`) có dãy người đi xe hai bánh cách `10.23–23.74 m` | Cụm điểm rất thưa xen lẫn hàng cây và xe hai bánh dựng dọc vỉa hè bên phải (`x ≈ -10.5 m`, `y ≈ -15.0 m` đến `-25.0 m`), khó tách rời với tán cây |
| `s01f59` | Phía trước xe (`-y`) hướng lên **cạnh trên** ảnh; trục `+x` (bên trái xe) chỉ sang **cạnh trái**; trục `+y` (phía sau xe) chỉ xuống **cạnh dưới** | Phẳng đều dọc tuyến đường; thấp hơn LiDAR khoảng `1.73 m` (`z ≈ -1.73 m`) | Khoảng `35.29 m` đến `40.00 m` (xe tải phía trước làn trái) | `Car`, `Truck`, `Cyclist` | Ngay sát bên trái (`+x`, `cam05/cam06`) có xe con cách `2.91 m`; phía sau (`+y`, `cam09`) có xe con cách `7.49 m`; phía sau-phải (`cam08`) có người đi xe hai bánh cách `13.85 m` | Các cụm điểm mảnh, rời rạc sau hàng xe nối đuôi ở làn trước-trái cách trên `38.0 m` bị che khuất phần lớn thân dưới |

- **Ba điều chỉ camera cho biết (trên `s01f36` và `s01f59`):**
  1. Màu sơn xe (trắng, đen, bạc), kiểu dáng chi tiết đầu/đuôi đèn hậu để khẳng định chắc chắn hướng đầu xe khi cụm điểm ở xa bị đối xứng.
  2. Vạch sơn phân làn mặt đường, mũi tên chỉ hướng rẽ và trạng thái đèn tín hiệu giao thông.
  3. Phân biệt xe hai bánh trên lề phải đang có người điều khiển (`Cyclist`) hay chỉ là xe dựng trống không người bên dưới rặng cây.
- **Ba điều chỉ LiDAR cho biết (trên `s01f36` và `s01f59`):**
  1. Khoảng cách hình học trực tiếp từ cảm biến tới từng phương tiện theo đơn vị mét (ví dụ `2.91 m`, `10.09 m`, `35.29 m`).
  2. Kích thước vật lý thực trong không gian 3 chiều (chiều dài, chiều rộng, chiều cao tính bằng `m`) không bị biến dạng phối cảnh xa-gần như ảnh 2D.
  3. Độ cao thực của mặt phẳng mặt đường (`z ≈ -1.72 m` đến `-1.73 m`) và tầm nhìn bao quát đồng thời `360°` quanh xe trong một khung quét duy nhất.

Tám frame lướt:

| Frame | Phía trước ở cạnh nào của ảnh nhìn từ trên xuống | Điều lạ (nếu có) |
|---|---|---|
| `s02f15` | Cạnh trên (`-y` hướng lên trên, `+x` sang trái, `+y` xuống dưới) | Tuyến đường hai chiều có dải xe đi ngược chiều ở làn bên trái (`yaw ≈ 89.3°`); có một số điểm nhiễu âm dưới mặt đường ở `y ≈ +28.7 m, z ≈ -9.9 m` |
| `s02f33` | Cạnh trên (`-y` hướng lên trên, `+x` sang trái, `+y` xuống dưới) | Cả làn cùng chiều (`yaw ≈ 271.6°`) và làn ngược chiều bên trái (`yaw ≈ 85.7°–91.0°`) đều có phương tiện rải từ `7.94 m` tới `33.72 m` |
| `s03f04` | Cạnh trên (`-y` hướng lên trên, `+x` sang trái, `+y` xuống dưới) | Đoàn xe nối đuôi sát nhau ở làn trái gây hiện tượng bóng che rõ rệt từ xe gần (`4.26 m`) lên các xe phía trước (`11.14 m` và `17.25 m`) |
| `s03f19` | Cạnh trên (`-y` hướng lên trên, `+x` sang trái, `+y` xuống dưới) | Khu vực giao lộ rộng có đủ `Bus` (`44.25 m`), nhóm `Pedestrian` băng qua đường bên phải (`19.02–19.99 m`), `Cyclist` (`9.13 m`) và cụm điểm ma âm sâu ở `z ≈ -22.1 m` |
| `s04f02` | Cạnh trên (`-y` hướng lên trên, `+x` sang trái, `+y` xuống dưới) | Biển báo giao thông bên trái cách `~9.0 m` phản xạ cường độ rất mạnh (trắng sáng), tương phản rõ với chiếc SUV màu đen gần đó (`4.70 m`) |
| `s04f46` | Cạnh trên (`-y` hướng lên trên, `+x` sang trái, `+y` xuống dưới) | Có dải phân cách cứng giữa đường; các xe ở phía sau cách `34.85–39.01 m` mật độ điểm giảm mạnh chỉ còn vài vệt ngang |
| `s05f01` | Cạnh trên (`-y` hướng lên trên, `+x` sang trái, `+y` xuống dưới) | Phía sau xe (`12.29 m`) có xe tải thùng lớn (`Truck` dài `9.000 m`, cao `3.155 m`) tạo mảng chắn tia lớn |
| `s05f32` | Cạnh trên (`-y` hướng lên trên, `+x` sang trái, `+y` xuống dưới) | Mật độ phương tiện tập trung chủ yếu ở tầm gần và trung bình (`7.03–21.40 m`) cả phía trước, phía sau và ngang hông trái |

## 3. Ba cuboid có hướng dẫn (Bước 3)
Ảnh minh hoạ trong gói ZIP: `t2-a-car.png`, `t2-b-bus-truck.png`, `t2-c-ped-cyclist.png`.

| Vật | Frame, idx | Class | Mũi tên yaw đúng hướng? | Đáy chạm đường? | Đã kiểm Top/Side/Front + ảnh? | Kích thước trong bảng 4.3? |
|---|---|---|---|---|---|---|
| (a) xe con gần | `s04f02, idx 5` | `Car` | Đúng (`yaw_deg = 271.6°`, chỉ thẳng hướng đi `-y`) | Chạm khít mặt đường (`z_bottom = -1.771 m`, `ground_gap = -0.02 m`) | Đã kiểm đủ Top, Side, Front và đối chiếu `cam01`/`cam07` | Nằm trọn trong chuẩn SUV Bảng 4.3 (`4.874 m × 1.902 m × 1.920 m` so với `4.4–5.3 m × 1.80–2.10 m × 1.65–2.10 m`) |
| (b) xe buýt hoặc xe tải | `s03f04, idx 6` | `Truck` | Đúng (`yaw_deg = 270.8°`, dọc thân xe về phía đầu xe) | Chạm khít mặt đường (`z_bottom = -1.740 m`, `ground_gap = 0.00 m`) | Đã kiểm đủ Top, Side, Front và đối chiếu ảnh preview `cam08`/`cam09` | Nằm trọn trong chuẩn Bán tải Bảng 4.3 (`5.644 m × 1.886 m × 1.941 m` so với `5.2–6.2 m × 1.85–2.10 m × 1.80–2.00 m`) |
| (c) người đi bộ hoặc xe hai bánh | `s03f19, idx 4` | `Cyclist` | Đúng (`yaw_deg = 338.1°`, hướng di chuyển qua nút giao) | Chạm mặt đường (`z_bottom = -1.668 m`, `ground_gap = 0.09 m`) | Đã kiểm đủ Top, Side, Front và đối chiếu ảnh camera bên phải | Nằm trọn trong chuẩn Xe hai bánh có người Bảng 4.3 (`1.650 m × 0.639 m × 1.610 m` so với `1.6–1.9 m × 0.50–0.70 m × 1.60–1.90 m`) |

- **Rút kinh nghiệm quy chuẩn guideline:** Khi căn cuboid cần thống nhất quy tắc: bán tải xếp vào `Truck` (không xếp vào `Car`), người đang điều khiển xe hai bánh gộp chung người và xe thành một hộp `Cyclist` (xe hai bánh đỗ trống bên vỉa hè không vẽ), luôn bật `Cuboid orientation` để kiểm vạch đỏ ngắn chỉ về đầu xe và chỉ chỉnh chiều cao/đáy trên `Side`/`Front` (tuyệt đối không xoay nghiêng ở `Side`/`Front`).
- **Các vật bỏ qua vì không đủ cơ sở:**
  1. `s01f36` (vỉa hè bên phải `x ≈ -10.0 m`, `y ≈ -15.0 m` đến `-25.0 m`): Cụm điểm rất thưa bị tán cây che khuất và ảnh `cam07`/`cam08` mờ, không đủ cơ sở xác định là người đang đạp xe hay xe đạp dựng trống trên vỉa hè (`LOW_VISIBILITY`, `UNCERTAIN_CLASS`).
  2. `s04f46` và `s03f19` (các cụm điểm xe ở khoảng cách `46.0–50.0 m`): Chỉ còn dưới `10–15` điểm lác đác ở một cạnh (< `15–20%` thể tích hộp), không đủ cơ sở suy luận kích thước và hướng đầu xe (`LOW_VISIBILITY`, `UNCERTAIN_SCOPE`).

## 4. Bốn hiện tượng chất lượng (Bước 4)
Cả 4 hiện tượng đều được tìm và chụp trực tiếp trên **task cá nhân `D13-2A202603229`** (không mượn frame ngoài). Mọi nguyên nhân đều ghi dưới dạng **giả thuyết**.

| Hiện tượng | Frame / tên ảnh / nguồn | Nguyên nhân (giả thuyết) | Thấy gì trên point cloud | Hệ quả khi gán (theo guideline) |
|---|---|---|---|---|
| 1. Bóng che | `s03f04` / `t4-1-bong-che.png` / task cá nhân `D13-2A202603229` | **Giả thuyết:** Thân xe con ở gần (`idx 2`, cách `4.26 m`) chắn chùm tia laser phát ra từ nóc xe thu dữ liệu khiến vùng không gian phía sau nó không nhận được tia tới | Xuất hiện dải tối khuyết điểm phía trước-trái; xe con `idx 3` (`11.14 m`) và xe tải `idx 1` (`17.25 m`) bị mất một phần điểm thân dưới và sườn phải | Bật thuộc tính `occluded = 1` cho các vật bị che và kéo cuboid bao trọn cả phần thể tích bị khuất suy luận được nhờ ảnh camera và Bảng 4.3 |
| 2. Thưa ở xa | `s04f46` / `t4-2-thua-o-xa.png` / task cá nhân `D13-2A202603229` | **Giả thuyết:** Các tia laser của LiDAR 40 tia phân kỳ theo góc ngang và góc đứng khi truyền đi xa nên khoảng cách giữa các vòng quét giãn rộng | Tại `idx 4 Car` cách `39.01 m`, cả chiếc xe chỉ còn `78 điểm` xếp thành 2–3 vạch ngang mảnh ở đuôi xe, các vòng quét mặt đường quanh đó thưa rõ rệt | Chỉ vẽ cuboid khi còn quan sát được khoảng `15–20%` thể tích hoặc suy luận chắc chắn nhờ ảnh camera; nếu ít điểm hơn và mờ thì bỏ qua, ghi nhận `LOW_VISIBILITY` |
| 3. Phản xạ thấp / ít điểm bất thường | `s04f02` / `t4-3-phan-xa-thap.png` / task cá nhân `D13-2A202603229` | **Giả thuyết:** Kính cửa sổ truyền qua/phản xạ gương làm tia laser lệch hướng không quay về đầu thu, còn lớp sơn đen hấp thụ năng lượng hồng ngoại mạnh hơn bề mặt phủ màng phản quang của biển báo | Dù ở rất gần (`4.70 m`), chiếc SUV màu đen (`idx 5`) bị rỗng điểm hoàn toàn ở các ô kính và điểm thân xe xám mờ, trái ngược hẳn với biển báo bên trái (`~9.0 m`) dày điểm trắng sáng gắt | Chỉ ghi nhận nguyên nhân như giả thuyết tham khảo; khi vẽ vẫn căn hộp bao đủ khung thân và mui xe thực tế (không co hộp theo lỗ thủng kính và không nới hộp theo quầng sáng biển báo) |
| 4. Điểm lạ | `s03f19` / `t4-4-diem-la.png` / task cá nhân `D13-2A202603229` | **Giả thuyết:** Hiện tượng phản xạ đa đường (multi-path reflection) của tia laser qua kính xe hoặc bề mặt kim loại bóng khiến cảm biến tính thời gian bay (ToF) dài hơn thực tế và chiếu điểm xuống dưới mặt đất | Cụm `5 điểm` sáng nằm cô lập ở tọa độ `x ≈ -7.5 m, y ≈ -46.8 m, z ≈ -22.1 m`, tức nằm âm sâu dưới mặt phẳng mặt đường (`z ≈ -1.7 m`) khoảng `20.4 m` giữa khoảng không đen | Tuyệt đối **không gán thành vật** (bỏ qua điểm nhiễu hình học để tránh đưa nhãn giả vào tập huấn luyện) |

## 5. Số từ cuboid của tôi (Bước 5)
Số liệu trích xuất trực tiếp từ `submission/D13-2A202603229-bangA.csv` và cột `points_in`, `ground_gap_m` của `submission/D13-2A202603229-tukiem.csv`:

| Cuboid (frame, idx, class) | z | h | z đáy = z − h/2 | dist_m | r = √(x²+y²+z²) | yaw_deg | Nhận xét (lơ lửng / chìm / chạm đường) |
|---|---|---|---|---|---|---|---|
| `s04f02, idx 5, Car` | `-0.811 m` | `1.920 m` | `-1.771 m` | `4.70 m` | `4.77 m` | `271.6°` | **Chạm đường** (mặt đường quanh xe `≈ -1.75 m`, độ hở `ground_gap = -0.02 m`) |
| `s03f04, idx 6, Truck` | `-0.769 m` | `1.941 m` | `-1.740 m` | `9.47 m` | `9.50 m` | `270.8°` | **Chạm đường** (khớp khít mặt đường quanh xe, độ hở `ground_gap = 0.00 m`) |
| `s01f59, idx 11, Cyclist` | `-0.555 m` | `1.469 m` | `-1.290 m` | `13.85 m` | `13.86 m` | `271.3°` | **Cao hơn lòng đường chính** (`z đáy = -1.290 m` so với `-1.73 m`, `ground_gap = +0.47 m` do di chuyển sát dải nền lề đường cao hơn mặt nhựa chính và cụm điểm chân bánh xe bị khuất nhẹ) |

- **(a) Khoảng cách gần nhất và xa nhất:**
  - Cuboid **gần nhất**: `s01f59, idx 1, Car` (`x = 2.897 m, y = -0.218 m, z = -0.961 m`) có khoảng cách ngang `dist_m = 2.91 m` và khoảng cách 3D `r = 3.06 m`.
  - Cuboid **xa nhất**: `s01f36, idx 3, Car` (`x = -0.524 m, y = 46.523 m, z = -1.140 m`) có khoảng cách ngang `dist_m = 46.53 m` và khoảng cách 3D `r = 46.54 m`.
- **(b) Đối chiếu kích thước với Bảng 4.3:**
  - Người đi bộ `s03f19, idx 1, Pedestrian`: `l = 0.959 m, w = 0.639 m, h = 1.762 m`. Chiều rộng `w` và chiều cao `h` nằm gọn trong khoảng Người lớn của Bảng 4.3 (`0.5–0.9 m` và `1.50–1.95 m`), riêng chiều dài `l = 0.959 m` vượt nhẹ ngưỡng trên (`0.4–0.9 m`) khoảng `0.059 m` vì người đi bộ đang bước sải chân và đánh tay qua vạch sang đường.
  - Xe con `s05f01, idx 3, Car`: `l = 6.174 m, w = 2.077 m, h = 1.726 m`. Chiều rộng và chiều cao khớp chuẩn SUV/MPV (`1.80–2.10 m` và `1.65–2.10 m`), nhưng chiều dài `l = 6.174 m` vượt khoảng `4.4–5.3 m` do xe cách `15.36 m` có vệt điểm phản xạ kéo giãn nhẹ ở cản trước/sau khi nhìn chéo góc.
- **(c) Góc `yaw_deg` theo chiều giao thông:**
  - Xe đi **cùng chiều phía trước**: `s04f02, idx 1, Car` (`x = -0.538 m, y = -15.991 m`) có `yaw_deg = 270.9°` — rất sát mốc lý thuyết `270.0°` (hướng `-y`).
  - Xe đi **ngược chiều**: `s02f15, idx 1, Car` (`x = 3.774 m, y = 22.039 m`) có `yaw_deg = 89.3°` — rất sát mốc lý thuyết `90.0°` (hướng `+y`).

Mật độ theo khoảng cách (4 cuboid `Car` ở 4 khoảng `dist_m` khác nhau):

| Cuboid (frame, idx, class) | dist_m | Số điểm (`points_in`) |
|---|---|---|
| `s02f33, idx 5, Car` | `7.94 m` | `2206 điểm` |
| `s04f02, idx 1, Car` | `16.00 m` | `713 điểm` |
| `s02f33, idx 3, Car` | `26.03 m` | `212 điểm` |
| `s04f46, idx 4, Car` | `39.01 m` | `78 điểm` |

- **Quy luật mật độ theo khoảng cách:** Khi khoảng cách từ LiDAR tới xe tăng lên gấp `2 lần` thì số điểm phản xạ thu được trên xe giảm mạnh chỉ còn khoảng `1/3` đến `1/4` (xấp xỉ tỉ lệ nghịch với bình phương khoảng cách $1/d^2$). Bốn mốc đo thực tế trên (`2206`, `713`, `212`, `78 điểm`) khớp rất sát với đường cong trung vị xe con ở slide bài giảng (`1968 điểm` ở `0–10 m`, `718 điểm` ở `10–20 m`, `214 điểm` ở `20–30 m`, `72 điểm` ở `30–40 m`).
- **Tự kiểm và chỉnh sửa sau `preview` / `selfcheck`:**
  - **Hai cuboid đã sửa:** (1) `s02f15, idx 5, Car` (`dist_m = 18.47 m`): qua ảnh `preview` phát hiện mũi tên hướng đang quay `89.3°` (ngược đầu) do sao chép hộp từ làn đối diện sang làn xe thu dữ liệu, đã xoay lại `180.0°` thành `271.2°`; (2) `s05f32, idx 4, Car` (`dist_m = 21.40 m`): qua `selfcheck` phát hiện đáy hộp bị lơ lửng cao hơn mặt đường `0.91 m` (`z_bottom = -0.833 m`), đã hạ tâm `z` trên khung `Side`/`Front` để đưa `z_bottom` về `-1.732 m` (`ground_gap = 0.02 m`). Ngoài ra đã tinh chỉnh lại đáy hộp `s01f59, idx 11, Cyclist` từ `-1.195 m` xuống `-1.290 m`.
  - **Cảnh báo giữ nguyên:** Giữ nguyên `s03f19, idx 1, Pedestrian` (`38 điểm sát ngay ngoài mép`) vì đây là nhóm 3 người đi bộ đi sát nhau qua đường (`idx 1, 2, 3`), các điểm nằm trong lớp vỏ `0.25 m` bên ngoài thực chất là điểm cơ thể của người đi bộ kế bên chứ hộp không bị chật; giữ nguyên một số hộp `Car` có `length` từ `6.03 m` đến `6.42 m` vì hộp đã bao sát cụm điểm quan sát được và khớp vị trí trên ảnh `preview`.
- **Đổi kiểm:** Đã thực hiện đối chiếu kiểm chéo trực tiếp trên màn hình hai con số trong mục 5 (`z đáy = -1.771 m`, `dist_m = 4.70 m` của `s04f02, idx 5` và `points_in = 713 điểm` của `s04f02, idx 1`) với bảng A và phiếu tự kiểm (cùng học viên nhóm lớp L2, kết quả khớp chính xác 100% không có sai lệch).

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc
- **(a) Ý nghĩa từ phía mô hình học máy 3D:**
  1. Nhãn 3D bắt buộc đo bằng **mét (`m`)** thay vì pixel vì các mạng phát hiện vật thể 3D trên đám mây điểm (như PointPillars, CenterPoint) học trực tiếp hình học không gian thực để ước lượng khoảng cách an toàn, vận tốc và kích thước vật lý bất biến theo vị trí xe, không bị phụ thuộc vào tiêu cự ống kính hay phép chiếu phối cảnh làm vật ở xa co nhỏ lại như trên ảnh 2D.
  2. Vật quá thưa điểm phải có quy tắc ngưỡng riêng (`15–20%` thể tích, dưới ngưỡng thì bỏ qua/ghi `LOW_VISIBILITY`) vì nếu gán hộp đoán mò trên vài điểm rời rạc, mô hình sẽ bị nhiễu nhãn (label noise), học nhầm các cụm điểm thưa ngẫu nhiên thành vật thể thật gây tăng vọt báo động giả (False Positives) và làm mất ổn định hàm mất mát góc hướng `yaw`.
- **(b) Sử dụng AI trong buổi lab:**
  - **Công cụ:** Trợ lý AI lập trình (Antigravity / Gemini).
  - **Câu hỏi chính:** Hỏi quy trình chạy lệnh kiểm tra môi trường, chuyển đổi hộp vẽ nhầm từ chế độ `Track` sang `Shape` qua `cvat-sdk`, giải thích quy ước hệ tọa độ VF (`x` trái, `y` sau, `z` lên) và kiểm tra điều kiện nộp bài của `check_submission.py`.
  - **Cách sử dụng kết quả:** Dùng lệnh để đồng bộ task CVAT local, tự kiểm tra lỗi định dạng và đối chiếu lại trực tiếp toàn bộ hộp bao trên khung 3D/ảnh `preview` của CVAT.
- **(c) Điều còn chưa chắc:**
  - Ở các khoảng cách `30.0–46.5 m` (như `s01f36 idx 3, idx 10` và `s04f46 idx 4, idx 5`), điểm LiDAR chỉ bám ở mặt đuôi hoặc mặt đầu xe nên chiều dài hộp phụ thuộc một phần vào suy luận theo Bảng 4.3, có thể chênh lệch `0.3–0.5 m` so với thân xe thực.
  - Tại khu vực lề đường có bậc vỉa hè hoặc mặt đường hơi nghiêng ở rìa ngoài (`s01f36`, `s01f59`), cao độ mặt đất cục bộ dưới bánh xe hai bánh cao hơn mặt nhựa trung tâm khoảng `0.3–0.4 m`.
