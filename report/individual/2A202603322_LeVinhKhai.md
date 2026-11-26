# Báo cáo Day 13 — Lê Vĩnh Khải, 2A202603322

## 1. Kết luận trước
Thông tin bài thực hành

Học viên: Lê Vĩnh Khải

MSSV: 2A202603322

Task cá nhân: D13-2A202603322

Hệ điều hành: macOS

Trình duyệt: Chrome

Phiên bản CVAT Server: 2.81.0

MSSV bạn ngồi cạnh: 2A202603333

10 frame được phân công:

s01f11, s01f35, s02f08, s02f31, s03f04, s03f57, s04f05, s04f37, s05f07, s05f44.
## 1.1. Kết quả tổng quan

Tổng số cuboid: 143 cuboid trên 10/10 frame.

Frame có ít cuboid nhất: s01f35 và s03f57, mỗi frame có 8 cuboid.

Kết quả self-check: 35/143 cuboid có cảnh báo (khoảng 24,5%).

Các loại cảnh báo ghi nhận: kích thước ngoài khoảng tham chiếu, đáy cuboid cao hoặc thấp hơn mặt đường ước lượng, một số hộp có nhiều điểm LiDAR sát ngoài mép.

Sau khi kiểm tra: hai cảnh báo cuboid rỗng (0 điểm LiDAR) ở lần kiểm trước không còn xuất hiện trong kết quả sync mới nhất.

Đánh giá cá nhân:

Lý do hai frame có ít cuboid nhất: có ít xe trong tầm lidar có thể phát hiện được

Phần tự tin nhất: Hướng của vật thể - có thể xác định qua ảnh 2D từ đó có thể dễ dàng biết được hướng của vật thể

Phần còn hạn chế: các xe ở làn ngược chiều bị che bởi cây ngăn cách giữa 2 làn nên không đánh được chính xác chiều cao của xe

### 1.2. Cách sử dụng CVAT để gán nhãn 3D

**Thao tác góc nhìn:** Trong CVAT, sử dụng chế độ Top View để quan sát vị trí và phân bố các vật thể trên mặt phẳng. Kết hợp thao tác xoay, kéo và phóng to/thu nhỏ point cloud để kiểm tra vật thể từ nhiều góc nhìn, đặc biệt tại các khu vực bị che khuất hoặc có mật độ điểm LiDAR thấp.

**Tạo và chỉnh sửa cuboid:** Sử dụng công cụ Cuboid ở chế độ Shape để tạo hộp 3D cho từng vật thể, nháy đúp để hoàn tất thao tác vẽ. Sau đó, điều chỉnh vị trí, kích thước và góc xoay của cuboid thông qua các góc nhìn Top, Side và Front, bảo đảm hộp bao sát vật thể và đáy tiếp xúc với mặt đường.

**Kiểm tra hướng và ảnh camera:** Bật tùy chọn Cuboid orientation để hiển thị mũi tên định hướng, kiểm tra đầu xe và điều chỉnh yaw cho phù hợp. Đối chiếu với các góc camera để xác định loại vật thể, hướng di chuyển và mức độ che khuất. Cuối cùng, sử dụng lệnh `python scripts/lab.py preview` để kiểm tra vị trí cuboid được chiếu lên ảnh camera, phát hiện các trường hợp lệch hộp hoặc gán nhãn chưa chính xác.

## 2. Đọc frame (Bước 2)
Hai frame đọc kỹ. Ảnh: `t1-s01f11-3d.png`, `t1-s01f11-top.png`, `t1-s01f35-3d.png`, `t1-s01f35-top.png`.

| Frame | Phía trước xe ở cạnh nào của ảnh nhìn từ trên xuống; +x, +y chỉ về đâu | Mặt đường (phẳng/dốc; thấp hơn LiDAR ~ m) | Vật xa nhất đọc được (~ m) | Loại vật thấy | Vật phía sau / hai bên | Cụm chưa đọc được |
|---|---|---|---|---|---|---|
| s01f11 | Phía trước (−y) ở cạnh trái ảnh; +x (bên trái xe) chỉ xuống cạnh dưới; +y (phía sau xe) chỉ sang cạnh phải | Phẳng; thấp hơn LiDAR khoảng 1,7–1,8 m (z đáy xe gần nhất: −1,80 m) | ~60 m (xe con phía trước, idx 15, 59,96 m, chỉ 20 điểm) | Xe con, xe buýt (phía trước, 37,5 m), xe tải (43 m, chiều ngược lại), người đi bộ đứng cạnh trạm chờ bên phải (14 m) | Phía trước: xe con cùng chiều bên trái (6 m), xe con (24 m), xe buýt (37 m). Phía sau: 4 xe con cùng chiều (22–58 m). Bên trái: dải phân cách bụi hoa thấp, bên kia là chiều ngược lại với 5 xe con (13–25 m). Bên phải: vỉa hè, trạm chờ xe buýt | Điểm dày dọc dải phân cách (bụi hoa, mép đá) không phải xe nên không vẽ; cụm điểm dạng sọc trên mặt đất phía sau bên phải (~8–10 m), có thể là lối rẽ hoặc chỗ vỉa hè hạ cốt, chưa chắc |
| s01f35 | Như trên: phía trước ở cạnh trái, +x xuống dưới, +y sang phải | Phẳng; thấp hơn LiDAR khoảng 1,7–1,8 m (z đáy xe gần: −1,73 m và −1,82 m) | ~50 m (xe con cùng làn phía trước, idx 8, 50,3 m, 38 điểm) | Xe con, xe tải gắn thùng quảng cáo (15 m, bên kia dải phân cách), xe hai bánh (11–17 m, bên phải) | Phía trước bên trái: xe con cùng chiều (9 m). Phía sau: xe con đi theo sát (10 m), một xe xa (46 m). Bên trái: dải phân cách bụi hoa, bên kia có xe tải quảng cáo. Bên phải: dải cây, sau đó là vỉa hè có bãi xe đạp đỗ dày | Bãi xe đạp đỗ bên phải: khó tách xe có người với xe đỗ không người, chỉ vẽ chỗ thấy người trên camera; trên cam03 có vài xe xa hơn 60 m nhưng LiDAR quá thưa nên không vẽ |

Hướng không camera nào thấy: sát hai bên hông xe (khe giữa cam06–cam05 bên trái và cam07–cam08 bên phải) và mặt đường ngay quanh thân xe. LiDAR quét 360° nên vẫn có điểm ở các vùng này.

Chỉ camera cho biết:
1. Hướng đầu xe: ở s01f11, xe idx 1 phía trước lộ đèn hậu trên cam01, nên chạy cùng chiều (yaw 269,5°); ở s01f35, xe idx 2 phía sau lộ mặt trước trên cam09, nên đi theo sau (yaw 273,9°). Trên LiDAR, đầu và đuôi xe trông gần giống nhau.
2. Class khi hình khối dễ nhầm: ở s01f11, vật dài ~10 m phía trước là xe buýt; ở s01f35, khối dài ~6 m bên kia dải phân cách là xe tải gắn thùng quảng cáo, không phải biển quảng cáo cố định.
3. Người đứng ở trạm chờ (s01f11) và xe hai bánh có người hay chỉ là xe đỗ (s01f35); màu xe; mũi tên và vạch kẻ làn.

Chỉ LiDAR cho biết:
1. Khoảng cách bằng mét: xe buýt ở s01f11 cách 37,5 m; xe xa nhất còn đọc được cách ~60 m.
2. Độ cao mặt đường so với cảm biến (~−1,75 m) và kích thước thật: xe buýt s01f11 dài ~10,3 m, cao ~3,3 m; xe tải s01f35 dài ~5,9 m, cao ~2,2 m.
3. Mật độ điểm giảm theo khoảng cách: ở s01f11, xe con ở 6,3 m có 2289 điểm, ở 24 m còn 275 điểm, ở ~60 m chỉ 20 điểm; phía sau xe lớn có vùng "bóng" không có điểm.

Tám frame lướt:

| Frame | Phía trước ở cạnh nào của ảnh nhìn từ trên xuống | Điều lạ (nếu có) |
|---|---|---|
| s02f08 | Cạnh trái | Hai xe buýt lớn ngay bên trái chiều ngược lại (~15–18 m) che khuất phía sau chúng; ở ~25–30 m bên phải có xe đi ngang qua giao lộ (yaw ~3–5°) |
| s02f31 | Cạnh trái | Hàng rào giữa đường sát ngay bên trái xe; một xe ngược chiều chạy sát bên kia hàng rào (~4 m, rất nhiều điểm); đường cong nhẹ về bên trái phía trước |
| s03f04 | Cạnh trái | Kẹt xe: mọi xe cùng chiều, xếp sát nhau (gần nhất ~3,7 m); một xe van cao ~1,9 m phía sau bên phải |
| s03f57 | Cạnh trái | Đường hẹp, hai bên dày cây và bụi nên rất nhiều điểm thực vật; ít xe; xe buýt phía sau (~25 m) |
| s04f05 | Cạnh trái | Xe MPV lớn sát bên phải (3,7 m, hơn 6600 điểm) che gần hết phía bên phải; hàng rào bên trái; xe ở chiều ngược lại nằm sau hàng rào |
| s04f37 | Cạnh trái | Xe tải ngược chiều ngay bên trái (~8 m) tạo bóng che lớn; có xe máy chạy bên phải phía trước |
| s05f07 | Cạnh trái | Dừng ở giao lộ chờ đèn; xe ngược chiều đứng chờ bên trái; xe tải lớn ngay phía sau; xe máy đang qua vạch sang đường bên phải |
| s05f44 | Cạnh trái | Giao lộ rộng; xe ở đường cắt ngang bên phải đứng chờ (yaw ~8–11°); nhiều xe xa 40–50 m phía trái, thưa điểm, khó đọc hướng |

## 3. Ba cuboid có hướng dẫn (Bước 3)
Ảnh: `t2-a-car.png`, `t2-b-bus-truck.png`, `t2-c-ped-cyclist.png` (chọn cuboid trong CVAT, chụp kèm Top/Side/Front và ảnh camera).

| Vật | Frame, idx | Class | Mũi tên yaw đúng hướng? | Đáy chạm đường? | Đã kiểm Top/Side/Front + ảnh? | Kích thước trong bảng 4.3? |
|---|---|---|---|---|---|---|
| (a) xe con gần | s01f11, idx 1 (6,28 m, phía trước bên trái) | Car | Đúng: yaw 269,5°, xe cùng chiều phía trước; cam01 thấy đèn hậu | Có: z đáy −1,80 m, lệch mặt đường −0,03 m | Có: Top/Side/Front bao trọn cụm điểm (2289 điểm), đối chiếu cam01 | Có: 4,32 × 1,82 × 1,55 m nằm trong khoảng sedan (3,8–5,0 × 1,70–1,90 × 1,40–1,60) |
| (b) xe buýt hoặc xe tải | s01f11, idx 11 (37,5 m, phía trước) | Bus | Đúng chiều (cùng chiều phía trước, cam03 thấy đuôi xe buýt); yaw 282,2°, lệch ~12° so với 270° vì xe buýt nằm hơi chéo trên làn | Có: z đáy −1,83 m, lệch mặt đường 0,00 m | Có: Top/Side/Front (468 điểm), đối chiếu cam03 | Dài 10,27 m, cao 3,35 m nằm trong khoảng xe buýt thường (10,0–12,5; 3,00–3,50). Rộng 2,81 m vượt 2,60 m: ở 37 m điểm hai bên sườn thưa, mép khó xác định, có thể dính gương chiếu hậu |
| (c) người đi bộ hoặc xe hai bánh | s01f11, idx 3 (14,3 m, phía trước bên phải, cạnh trạm chờ) | Pedestrian | Người đứng yên; yaw 341,4° đặt theo hướng thân người thấy trên cam07, chưa chắc | Có: z đáy −1,75 m, lệch mặt đường 0,00 m | Có: Top/Side/Front (143 điểm), đối chiếu cam07 | Có: 0,74 × 0,73 × 1,80 m nằm trong khoảng người lớn (0,4–0,9 × 0,5–0,9 × 1,50–1,95). `selfcheck` báo 73 điểm sát ngoài mép: đó là cột và mái trạm chờ ngay cạnh người, nên giữ nguyên hộp |

Vòng hiệu chuẩn (task chung `D13-HC485087`, frame `s05f48`, 3 cuboid):

| Vật | Cuboid của tôi | Điểm có thể lệch so với các bạn |
|---|---|---|
| 1. Xe van xanh sát bên phải (~4,6 m, cùng chiều) | Car; 5,04 × 2,33 × 2,41 m; yaw 270,9°; z đáy −1,80 m | **Class**: xe van có cửa sổ, nằm giữa "van chở người" (Car) và "xe tải nhẹ" (Truck), nên dễ mỗi người chọn một kiểu (UNCERTAIN_CLASS). **Kích thước**: rộng 2,33 m, cao 2,41 m vượt khoảng của Car (≤ 2,10 m), hộp hơi rộng do bao cả gương |
| 2. Taxi trắng bên kia dải phân cách (~9,4 m, ngược chiều) | Car; 5,29 × 2,13 × 1,62 m; yaw 92,2° | **Chiều dài** 5,29 m dài hơn sedan (≤ 5,0 m), **tâm hộp** lệch về phía sau xe trên ảnh cam06. Hướng ngược chiều thì nhiều khả năng cả lớp giống nhau |
| 3. Xe con trắng xa (~30 m, bên trái) | Car; 4,34 × 2,02 × 1,97 m; yaw 87,9°; z đáy −2,56 m | **Đáy** thấp hơn mặt đường quanh xe khoảng 0,2 m; **cao/rộng** quá khổ so với sedan. Ở 30 m điểm thưa nên tâm và kích thước dễ lệch nhất giữa các bạn |

Sau khi thảo luận với bạn ngồi cạnh (MSSV 2A202603333), chúng tôi chốt cách hiểu guideline: (1) van chở người có cửa sổ là Car, van chở hàng thùng kín là Truck, nếu không chắc thì ghi UNCERTAIN_CLASS; (2) hộp bao trọn điểm của vật, kể cả phần chìa ra như gương, thùng, nhưng không bao dư; kích thước đối chiếu bảng 4.3; (3) đáy hộp đặt chạm mặt đường tại chỗ xe đứng, không lấy −1,7 m cố định; (4) hướng mũi tên lấy theo đầu xe trên ảnh camera.

Vật bỏ qua vì không đủ cơ sở:
- s01f35, bên phải (~10–17 m): các xe đạp đỗ không thấy người trong bãi xe sau dải cây. Không chắc có người lái hay không nên không vẽ (UNCERTAIN_CLASS).
- s01f35, phía trước, xa hơn 60 m: vài xe thấy trên cam03 nhưng LiDAR gần như không còn điểm (UNCERTAIN_SCOPE / LOW_VISIBILITY).
- s03f57, trong làn phía trước ~18–20 m: một cụm nhỏ chỉ vài điểm, bị xe phía trước che (LOW_VISIBILITY).
- s01f11, mặt đất phía sau bên phải ~8–10 m: cụm điểm dạng sọc, không phải vật cần gán.

## 4. Bốn hiện tượng chất lượng (Bước 4)
Cả bốn hiện tượng đều tìm trong 10 frame của tôi, không mượn frame. Nguyên nhân ghi là **giả thuyết**.

| Hiện tượng | Frame / tên ảnh / nguồn | Nguyên nhân (giả thuyết) | Thấy gì trên point cloud | Hệ quả khi gán (theo guideline) |
|---|---|---|---|---|
| 1. Bóng che | s05f07, `t4-1-bong-che.png`, frame của tôi | Xe tải cao ~2,9 m (idx 8, ngay phía sau, 16,5 m) chắn tia laser; LiDAR không nhìn xuyên vật rắn | Sau xe tải là một vùng hình nêm gần như không có điểm: dải x −1,5…1 m, y 21…35 m có 0 điểm, trong khi dải bên cạnh (x −6…−3,5 m) cùng tầm có 71 điểm. Xe con idx 14 (25,9 m, phía sau bên phải) nằm một phần trong vùng này, chỉ còn 106 điểm | Bật occluded cho vật bị che (idx 14 đã bật); hộp phải bao phần thân suy luận được theo ảnh camera và bảng 4.3, không thu hộp theo phần điểm còn sót. Hộp idx 14 hiện còn ngắn hơn bảng 4.3 (dài 3,06 m), đúng ra phải nới. Vùng trống sau xe tải là **chưa quan sát được**, không kết luận là đường trống |
| 2. Thưa ở xa | s01f35 idx 7, `t4-2-thua-o-xa.png`, frame của tôi | Các tia LiDAR toả ra theo góc nên càng xa thì khoảng cách giữa hai tia càng lớn, ít tia trúng vật | Xe con phía sau cách 46,1 m chỉ còn 45 điểm (xe con cách 10,1 m trong cùng frame có 1160 điểm); trên Top/Side/Front chỉ thấy vài vệt điểm ở mặt quay về phía xe thu dữ liệu | Còn nhận ra hình xe (khoảng 15–20% thể tích) và cam09 thấy rõ là xe con, nên vẽ, kích thước phải dựa vào bảng 4.3. Hộp idx 7 hiện còn ngắn hơn bảng 4.3 (dài 3,04 m), đúng ra phải nới. Nếu chỉ còn vài điểm không ra hình thì bỏ qua và ghi LOW_VISIBILITY |
| 3. Ít điểm bất thường | s02f08 idx 2, `t4-3-phan-xa-thap.png`, frame của tôi | Giả thuyết: sơn xám tối và kính sau hấp thụ hoặc phản xạ lệch tia laser nên ít tia quay về cảm biến | Sedan xám tối cách 10,6 m có 646 điểm, trong khi sedan trắng cách 10,1 m (s01f35 idx 2) có 1160 điểm. Chưa khẳng định được vì còn yếu tố khác: xe trắng quay đầu về phía cảm biến, và hộp idx 2 hiện hơi nhỏ (dài 3,20 m, cao 1,22 m) nên một phần điểm có thể nằm ngoài hộp | Chỉ ghi giả thuyết, không khẳng định nguyên nhân; hộp phải vẽ theo kích thước thật của xe (camera + bảng 4.3), không thu hộp theo số điểm ít. Hộp idx 2 hiện còn ngắn hơn bảng 4.3 (dài 3,20 m), đúng ra phải nới |
| 4. Điểm lạ | s03f04, `t4-4-diem-la.png` (khung Side của xe idx 6), frame của tôi | Giả thuyết: tia laser phản xạ nhiều lần (từ thân xe hoặc mặt đường sang vật khác rồi mới quay về) nên đo ra quãng đường dài hơn thật, sinh điểm "ảo" dưới mặt đường | Cụm 7 điểm tại x ≈ 2,4–2,8 m, y ≈ −13,2 m, z ≈ −6,3 m, tức thấp hơn mặt đường khoảng 4,5 m, nằm ngay giữa hai xe phía trước bên trái (idx 6 và idx 9). Cũng gặp ở s05f44 (7 điểm ở z ≈ −11,9 m) và s01f35 (2 điểm ở z ≈ −11,3 m) | Không gán thành vật; không kéo đáy hộp của xe idx 6 hay idx 9 xuống theo các điểm này |

## 5. Số từ cuboid của tôi (Bước 5)
Số lấy từ `submission/D13-2A202603322-bangA.csv` và `submission/D13-2A202603322-tukiem.csv` (lần `sync` cuối, khớp với task trên CVAT: 143 cuboid). Mặt đường khoảng −1,7 m: z đáy lệch hơn 0,3 m là lơ lửng hoặc chìm.

| Cuboid (frame, idx, class) | z | h | z đáy = z − h/2 | dist_m | r = √(x²+y²+z²) | yaw_deg | Nhận xét (lơ lửng / chìm / chạm đường) |
|---|---|---|---|---|---|---|---|
| s01f11, 1, Car | −1,020 m | 1,554 m | −1,797 m | 6,28 m | 6,37 m | 269,5° | Chạm đường: lệch mặt đường tại chỗ −0,03 m (`ground_gap_m`) |
| s05f07, 20, Car | −0,658 m | 1,525 m | −1,420 m | 36,23 m | 36,24 m | 91,2° | So với −1,7 m thì đáy cao hơn 0,28 m, nhưng `selfcheck` báo **chìm 0,91 m** so với mặt đất quanh hộp. Xe ở chiều ngược lại, bị che (occluded), vòng điểm quanh hộp lẫn dải phân cách và bồn cây cao hơn lòng đường, nên mặt đất ước lượng bị đẩy lên. Giữ nguyên, ghi chưa chắc |
| s03f04, 13, Cyclist | −0,533 m | 1,250 m | −1,158 m | 31,23 m | 31,23 m | 272,8° | **Lơ lửng**: đáy cao hơn −1,7 m khoảng 0,54 m, `selfcheck` báo cao hơn mặt đất 0,50 m. Chỉ có 19 điểm ở 31 m, phần bánh xe sát đất không có điểm nên đáy đặt theo điểm thấp nhất thấy được. Đây là cuboid tôi kém tin nhất trong bảng |

(a) Cuboid **gần nhất**: s04f05 idx 1 (Car, xe MPV sát bên phải), dist_m = 3,67 m, r = 3,75 m. Cuboid **xa nhất**: s04f05 idx 10 (Truck, phía trước bên trái), dist_m = 63,58 m, r = 63,59 m. r lớn hơn dist_m một chút vì r tính cả chiều cao z (dist_m chỉ đo trên mặt phẳng ngang); ở xa thì z rất nhỏ so với khoảng cách nên hai số gần như bằng nhau.

(b) Kích thước so với bảng 4.3:
- Người đi bộ s04f05 idx 14: 0,95 × 0,65 × 1,00 m. Dài 0,95 m vượt khoảng 0,4–0,9 m, cao 1,00 m thấp hơn khoảng người lớn 1,50–1,95 m. Người này ở 32 m chỉ có 32 điểm và đáy hộp cao hơn mặt đất 0,36 m: có thể phần thân dưới bị che hoặc không có tia trúng, nên hộp chỉ bao phần thấy được (cũng có thể là trẻ em, chưa chắc). Đối chiếu: người đi bộ s01f11 idx 3 (14 m, 143 điểm) là 0,74 × 0,73 × 1,80 m, nằm trọn trong khoảng.
- Xe con s01f11 idx 2: 4,13 × 1,66 × 1,16 m. Rộng 1,66 m hơi thấp hơn 1,70 m, cao 1,16 m thấp hơn khoảng 1,40–1,60 m. Xe ở chiều ngược lại sau dải bụi hoa (occluded), phần dưới thân bị che nên chiều cao bị thiếu. Đối chiếu: xe con s01f11 idx 1 (không bị che) là 4,32 × 1,82 × 1,55 m, nằm trọn trong khoảng.

(c) Hướng: xe cùng chiều phía trước s01f11 idx 1 có yaw 269,5°, gần 270°. Xe ngược chiều s05f07 idx 4 có yaw 90,0° và s01f11 idx 4 có yaw 89,1°, gần 90°. Ngoại lệ: xe buýt s01f11 idx 11 có yaw 282,2°, lệch ~12° vì xe nằm hơi chéo trên làn.

Mật độ theo khoảng cách (4 xe con không bị che):

| Cuboid (frame, idx, class) | dist_m | Số điểm (`points_in`) |
|---|---|---|
| s01f11, 1, Car | 6,28 m | 2289 |
| s05f07, 6, Car | 14,77 m | 834 |
| s01f11, 7, Car | 24,03 m | 275 |
| s01f11, 10, Car | 36,03 m | 77 |

Quy luật: xa gấp khoảng 2 lần thì số điểm còn khoảng 1/3–1/4. Từ 6,28 m đến 36,03 m (xa gấp 5,7 lần), số điểm giảm từ 2289 còn 77 (còn 1/30), gần với quy luật 1/d² (số mũ ước tính ≈ 1,9). So với đường cong trên slide (1968 / 718 / 214 / 72 điểm ở 0–10 / 10–20 / 20–30 / 30–40 m), số của tôi cùng xu hướng và cùng bậc: 2289 ở 6 m, 834 ở 15 m, 275 ở 24 m, 77 ở 36 m, chỉ cao hơn một chút vì các xe này ở gần đầu mỗi khoảng.

Tự kiểm, hai cuboid **đã sửa** sau `selfcheck` (sửa trên CVAT cùng Claude, xem §6b):
- s01f11 idx 12 (Car, phía sau 37,5 m): `selfcheck` báo đáy **lơ lửng 0,66 m** và dài 2,12 m. Đã hạ đáy xuống mặt đường (z đáy −1,78 m) và kéo dài thành 4,20 m về phía xa cảm biến (phần đầu xe bị xe trước che).
- s02f08 idx 9 (Bus, phía sau 44,8 m): `selfcheck` báo đáy **lơ lửng 1,26 m** và cao 2,08 m. Đã hạ đáy (z đáy −1,95 m) và đặt chiều cao 3,10 m theo bảng 4.3 xe buýt thường.

Cảnh báo **giữ nguyên** (35/143 cuboid còn cờ):
- Kích thước nhỏ hơn bảng 4.3 ở các xe xa 43–64 m chỉ có 14–57 điểm (ví dụ s02f31 idx 9, s05f07 idx 21, s04f05 idx 10): chỉ thấy một mặt của xe, không đủ cơ sở suy kích thước, xếp LOW_VISIBILITY.
- "Chìm" ở xe chiều ngược lại sau dải phân cách (s01f11 idx 2, s05f07 idx 20, s03f57 idx 8): mặt đất ước lượng bị bồn cây/dải phân cách đẩy cao như phân tích ở bảng trên; đáy đã đặt theo mặt đường lòng đường.
- "Lơ lửng" ~0,5 m ở người đi bộ và xe hai bánh ở 30–35 m (s04f05 idx 15, s05f07 idx 18, s03f04 idx 13): có thể đứng trên vỉa hè cao hơn lòng đường; ít điểm nên chưa chắc.
- "Điểm sát ngoài mép" ở s01f11 idx 3 (người cạnh trạm chờ) và s04f05 idx 3 (xe sau hàng rào): điểm đó thuộc mái trạm chờ và hàng rào, không phải của vật.
- s03f04 idx 8 (Car rộng 2,47 m, cao 2,55 m): xe van cao, nghi giữa Car và Truck (UNCERTAIN_CLASS), giữ Car và hỏi coach.

Đổi kiểm: kiểm chéo với bạn MSSV 2A202603333: bạn kiểm có 132 cuboid ít hơn tôi 11 cuboid vì bạn không đánh các xe ở xa. 

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc
**(a) Ý nghĩa của nhãn.** Model 3D phải dự đoán vị trí, kích thước và hướng của vật trong không gian thật để xe tự lái tính khoảng cách phanh, làn đường và va chạm, nên nhãn phải đo bằng mét trong hệ tọa độ LiDAR; pixel chỉ là hình chiếu và đổi theo khoảng cách, camera, góc nhìn. Vật thưa điểm cần quy tắc riêng vì nếu mỗi người lúc vẽ lúc bỏ, lúc co hộp theo vài điểm lúc vẽ đủ cỡ thật, model sẽ học tín hiệu mâu thuẫn ở vùng xa, đúng nơi nó dễ sai nhất; quy tắc 15–20% thể tích và LOW_VISIBILITY giúp nhãn nhất quán.

**(b) Dùng AI:**
- Công cụ: Claude (Anthropic) qua ứng dụng Claude trên máy
- Việc đã nhờ: hướng dẫn các bước làm bài lab; trợ giúp hỏi đáp trong việc làm Cvat; hỗ trợ làm báo cáo sửa lại câu từ
- Cách tôi dùng kết quả: tôi tự vẽvà chỉnh trên CVAT, chạy `sync`, rồi đọc lại và sửa từng mục báo cáo; những chỗ AI không có căn cứ (kết luận của lớp ở vòng hiệu chuẩn, số liệu đổi kiểm) do tôi tự điền, kiểm tra lại toàn bộ AI đã làm.

**(c) Điều còn chưa chắc:**
- s01f35 idx 3, 4, 6 (Cyclist): đứng giữa bãi xe đạp đỗ, chưa chắc có người ngồi trên xe (UNCERTAIN_CLASS).
- Vị trí xe cứu thương sát hàng rào ở s04f05 (idx 6): điểm của xe lẫn với hàng rào nên tâm hộp có thể lệch.
- Ba hộp xe con dẫn ở §4 hiện còn ngắn hơn bảng 4.3 (3,8–5,0 m): s05f07 idx 14 dài 3,06 m, s01f35 idx 7 dài 3,04 m, s02f08 idx 2 dài 3,20 m. Tôi đã co hộp theo phần điểm thấy được, trái với guideline; đúng ra phải nới theo kích thước thật của xe.
- Nguyên nhân của hiện tượng 3 và 4 ở §4 mới là giả thuyết.
