# Annotation guideline — Phân loại biển báo giao thông Việt Nam theo tầng và xác định biển áp dụng cho xe ego — tập trung biển nhỏ/xa, biển tạm công trường và giao lộ nhiều biển

**Version:** v3.0

<!--
v1 = bản nháp đầu; v2 sau calibration; v3 sau blind handoff. Mỗi lần tăng version ghi một dòng vào 08_revision_log.md.
File này là thứ nhóm peer nhận nguyên văn trong blind pack và là Guide dán vào CVAT.
-->

Đọc hết một lượt (≈ 8 phút) trước khi vẽ. Chỗ nào guideline không đủ để quyết định thì dùng `unknown` +
`needs_review` theo mục 7, **đừng đoán**. Ảnh: camera hành trình trên đường Việt Nam, 1622×626.

## Quy trình mỗi ảnh (làm đúng thứ tự)

| Bước | Làm gì | Mục |
|---|---|---|
| 1 | Quét cả ảnh ở zoom 100 %, rồi zoom 200–400 % dọc hai lề, dải phân cách, dầm cầu, rào công trường, **cột đèn tín hiệu và biển đứng bên kia giao lộ**. Đánh dấu mọi thứ **giống biển** | 5, 6.1 |
| 2 | Với từng vật: trong scope không (bảng mục 5)? Mặt biển cao ≥ 12 px không? Không → bỏ qua | 3, 5 |
| 3 | Vẽ rectangle ôm mặt biển (không cột, không biển phụ) | 2, 3 |
| 4 | Gán `sign_family` theo **hình dạng + màu** → `sign_class` chỉ khi **đọc được** (không đọc được = `unknown`) | 4, 6.1 |
| 5 | Gán `relevant_to_ego` theo **vị trí** biển so với đường ego (thứ tự kiểm ở mục 7) | 0, 7 |
| 6 | Còn phân vân gì → tick `needs_review`. Ảnh không có box nào → tag `no_target_sign`. **Ctrl+S** | 2, 7 |

Trước khi sang ảnh kế: không box nào còn `__undefined__`; mọi box có `relevant_to_ego=unknown` đều đã tick
`needs_review`.

## 0. Quy ước trái / phải (đọc trước)

Trong toàn bộ guideline, **trái / phải luôn tính theo góc nhìn của camera ego** — tức là trái/phải **trên ảnh**,
giống người lái nhìn qua kính chắn gió. Không tính theo mặt biển, không tính theo người đứng đối diện biển.

| Cụm từ | Nghĩa chính xác |
|---|---|
| **Lề phải** | Biển đứng bên phải mép phải của phần đường ego đang đi (trên ảnh: bên phải làn xe ego) |
| **Lề trái / dải phân cách bên trái** | Biển đứng bên trái phần đường ego: trên dải phân cách giữa, đảo giao thông, hoặc lề bên kia đường một chiều |
| **Treo trên làn ego** | Biển ở phía trên phần đường ego (giá long môn, dầm cầu vượt), không lệch hẳn sang bên nào |
| **Mũi tên chỉ trái / phải** (trên biển) | Hướng **đầu mũi tên trên ảnh**. Biển luôn được nhìn từ mặt trước nên không bị lật gương; đầu mũi tên chỉ sang trái ảnh = trái |

**Không có attribute riêng cho vị trí trái/phải** — vị trí đã nằm sẵn trong toạ độ box. Annotator chỉ dùng quy ước
này để (1) chọn đúng class có hướng (`keep_left`/`keep_right`, `no_turn_left`/`no_turn_right`, `turn_left_only`/
`turn_right_only`, `no_u_and_left_turn`/`no_u_and_right_turn`) và (2) quyết định `relevant_to_ego` ở mục 7.

Class có hướng được quyết định **chỉ bằng hướng đầu mũi tên**, không phụ thuộc biển đứng bên nào của đường: biển
mũi tên chỉ sang trái cắm ở lề phải vẫn là `keep_left`.

## 1. Objective + scope

Dữ liệu huấn luyện hệ thống nhận biển báo cho xe (nhắc tốc độ, cấm rẽ/quay đầu, đường cấm, biển công trường). Với
mỗi ảnh: (1) vẽ box cho **mọi biển báo trong scope**, (2) gán nhóm biển (`sign_family`) và biển cụ thể (`sign_class`),
(3) cho biết biển có áp dụng cho xe ego (xe gắn camera, đi theo hướng camera nhìn) không.

**Trong scope:** biển báo theo **QCVN 41:2019/BGTVT** (ảnh dataset chụp trước 2023, khi bản 2019 còn hiệu lực) nhìn thấy **mặt trước**, mặt biển cao ≥ 12 px: biển
cấm, biển nguy hiểm/cảnh báo, biển hiệu lệnh, biển chỉ dẫn luật (qua đường, một chiều, cầu vượt…), biển phụ gắn dưới
biển chính — **kể cả biển tạm** gắn trên rào/giá chắn công trường.

**Ngoài scope — không vẽ:** xem bảng mục 5.

## 2. Annotation unit

- Một box cho **một mặt biển vật lý**. Nhiều biển cùng một cột: mỗi mặt biển một box (biển chính và biển phụ là 2
  box).
- Biển làn (tấm chữ nhật xanh chia nhiều cột làn, có hình xe và/hoặc số tốc độ): **1 box cho cả tấm**, không vẽ từng
  cột.
- Biển khu vực (tấm chữ nhật trắng có chữ **ZONE** bao quanh một biển tròn): **1 box cho cả tấm**, class theo biển
  tròn bên trong (mục 4).
- Hai biển giống nhau hai bên đường: 2 box.
- Nhiều biển chồng lên nhau trên cùng cột (biển sau bị biển trước che một phần): vẫn vẽ biển phía sau nếu phần
  thấy cao ≥ 12 px; box ôm phần thấy; class theo mục 6 (bị che > 50 % → `unknown`).
- Ảnh đã kiểm hết mà không có biển trong scope: tag ảnh `no_target_sign`, không vẽ box. Ảnh có box thì không gắn tag
  này.

## 3. Geometry rule

- **Draw new rectangle → label `traffic_sign` → Shape**.
- Box ôm sát **mặt biển nhìn thấy**, gồm viền màu. **Không** gồm cột, giá rào, biển phụ, bóng đổ.
- Biển tròn/tam giác: hình chữ nhật nhỏ nhất chứa toàn bộ biên ngoài.
- Bị che một phần: chỉ ôm phần thấy. Bị cắt mép ảnh: box chạy tới mép.
- Dây rào, cọc, cành mảnh **vắt qua** mặt biển không tính là bị che: box vẫn ôm trọn biển.
- **Đo 12 px:** khi kéo rectangle, CVAT hiện kích thước `rộng × cao` cạnh con trỏ — đọc số chiều cao. Chiều cao mặt
  biển < 12 thì xoá box (IGNORE).
- **Sát ngưỡng (cao 11–13 px):** vẽ hay không vẽ đều chấp nhận, vì đo tay lệch ±1 px. **Đã vẽ thì phải gán đúng** theo
  mục 6.1 (thấy tròn viền đỏ thì vẫn là `prohibitory`). Biển cao ≤ 10 px: luôn không vẽ.
- Tolerance: mỗi cạnh lệch ≤ 2 px (biển cao < 30 px) hoặc ≤ 10 % chiều cao biển (biển lớn hơn). Zoom khi vẽ biển nhỏ.

## 4. Taxonomy

`sign_family` luôn gán được nếu thấy hình dạng + màu. `sign_class` chỉ gán khi **đọc được** ký hiệu/chữ số.

| `sign_family` | Nhận biết | `sign_class` cho phép |
|---|---|---|
| `prohibitory` (biển cấm) | Tròn, viền đỏ, nền trắng (hoặc nền xanh với cấm dừng/đỗ); hoặc tròn trắng có vạch chéo đen | `no_entry` (tròn đỏ, vạch trắng ngang — cấm đi ngược chiều), `road_closed` (tròn trắng viền đỏ, **trống** — đường cấm), `no_stopping_parking` (nền xanh, viền đỏ, gạch chéo X), `no_parking` (nền xanh, viền đỏ, 1 gạch), `no_turn_left`, `no_turn_right`, `no_u_turn` (một mũi tên chữ U bị gạch, kể cả khi có hình ô tô bên trong), `no_u_and_left_turn` / `no_u_and_right_turn` (mũi tên rẽ **và** chữ U), `no_motorbike`, `no_car`, `no_truck`, `no_overtaking` (hai ô tô, không có số), `min_distance` (hai xe + **số mét** — cự ly tối thiểu giữa hai xe), `speed_limit_30/40/50/60/70/80`, `speed_limit_other` (số khác, gồm 90/100/110/120, và tấm ghép tốc độ nền trắng theo loại xe/làn), `weight_limit` (số + "t"), `height_limit` (số + "m", mũi tên **dọc**; mũi tên ngang = hạn chế chiều rộng, hình trục xe = tải trọng trục → `prohibitory_other`), `end_of_prohibition` (vạch chéo đen trên biển hoặc trên tấm ZONE = hết lệnh cấm/hết khu vực), `prohibitory_other` |
| `danger` (nguy hiểm/cảnh báo) | Tam giác đỉnh lên, viền đỏ, nền vàng — **và** tam giác **đỉnh xuống** viền đỏ (`give_way`) | `danger_intersection` (giao nhau), `danger_road` (đường cong, hẹp, dốc, trơn, gồ ghề), `danger_pedestrian` (người đi bộ, trẻ em), `danger_construction` (người xúc đất / công trường), `danger_slow` (chữ "ĐI CHẬM"), `give_way` (tam giác **đỉnh xuống** — giao nhau với đường ưu tiên, nhường đường), `danger_other` |
| `mandatory` (hiệu lệnh) | Tròn nền xanh, ký hiệu trắng; tấm chữ nhật xanh biển làn; **bát giác đỏ chữ STOP** | `keep_right` (mũi tên chỉ xuống/sang **phải** — đi vòng bên phải), `keep_left` (mũi tên chỉ xuống/sang **trái**, chéo hoặc ngang — đi vòng bên trái), `stop` (bát giác đỏ chữ STOP — dừng lại), `ahead_only`, `turn_left_only`, `turn_right_only`, `roundabout`, `lane_vehicle_permission` (biển làn chỉ loại xe), `lane_vehicle_speed` (biển làn có loại xe + số tốc độ), `min_speed` (tròn **nền xanh, số trắng** — tốc độ **tối thiểu**), `end_min_speed` (như trên có vạch chéo đỏ), `mandatory_other` |
| `informative` (chỉ dẫn) | Vuông/chữ nhật **xanh** có **một ký hiệu/hình vẽ** hoặc chữ ngắn theo mẫu QCVN (không phải tên địa danh, không phải bảng nhiều dòng chữ) | `pedestrian_crossing`, `one_way`, `overpass_route` (cầu vượt/hầm), `priority_road` (hình thoi vàng viền trắng), `end_priority_road` (hình thoi có vạch chéo), `informative_other` (bến xe buýt, chợ, bắt đầu/hết khu đông dân cư…) |
| `supplementary` (biển phụ) | Tấm chữ nhật nhỏ gắn **ngay dưới biển chính và giải thích biển đó** (loại xe, khoảng cách, giờ, mũi tên hướng tác dụng) — **bất kể màu nền** | `supplementary_plate` |
| `unknown` | Chỉ ở mức C/D mục 6.1: biết là biển nhưng không xác định được hình dạng hoặc màu | `unknown` |

Quy tắc:

- `sign_class=unknown` khi thấy nhóm biển nhưng không đọc được ký hiệu/chữ số. **Không đoán số tốc độ, số tấn, số
  mét.** Đọc được số nhưng không có trong danh sách: `speed_limit_other`.
- **Phép thử "đọc được":** ở zoom 400 %, mọi chữ số / nét ký hiệu phải phân biệt rõ. Biển có giá trị (tốc độ, tấn,
  mét) cao < 20 px mà chữ số có thể là 3/5/6/8 → `unknown`. Biển nền xanh viền đỏ nhỏ mà không thấy rõ 1 gạch hay
  gạch chéo X → `prohibitory/unknown`.
- **Phân vân giữa hai class** (ví dụ `no_u_and_left_turn` hay `no_u_and_right_turn`) → `unknown`. Không dùng
  `needs_review` để đánh dấu "không chắc class".
- **Tấm dưới biển chính:** tấm gắn ngay dưới biển chính trên cùng cột → `supplementary` **trước tiên**, kể cả tấm xanh
  không đọc được chữ. Rule "tấm xanh không đọc được → `informative/unknown`" (mục 5) chỉ áp cho tấm đứng riêng.
- Class phải thuộc đúng family trong bảng.
- **Số trên nền trắng viền đỏ = tốc độ tối đa** (`speed_limit_*`); **số trắng trên nền xanh = tốc độ tối thiểu**
  (`min_speed`). Nhầm hai loại này làm hệ thống hiểu ngược biển.
- Tấm ghép nhiều biển tròn trên một nền chữ nhật (tốc độ theo loại xe/làn): **1 box cả tấm** như biển làn — nền xanh
  → `lane_vehicle_speed`, nền trắng → `speed_limit_other`.
- Biển cấm còn lại (cấm đi thẳng, cấm rẽ cả hai bên, cấm đỗ ngày chẵn/lẻ, hạn chế chiều rộng/dài, tải trọng trục…)
  → `prohibitory_other`.
- Mọi attribute mặc định `__undefined__`; còn `__undefined__` trong export = chưa gán = lỗi.

## 5. Inclusion / exclusion

| Thấy gì | Quyết định |
|---|---|
| Biển QCVN (bảng mục 4) mặt trước, cao ≥ 12 px — gồm biển tạm trên rào công trường | **LABEL** |
| Biển phụ gắn dưới biển chính | **LABEL** riêng `supplementary/supplementary_plate`, relevance = như biển chính |
| Bảng chỉ đường/địa danh (nền xanh lá/xanh dương có tên địa điểm, km), bảng trên giá long môn | **IGNORE** — vẫn là biển chỉ dẫn nhóm I theo QCVN, nhưng **ngoài scope** vì downstream (nhắc tốc độ, cấm, công trường) không dùng |
| Tấm tên cầu / tên đường (chữ tên cầu, chiều dài, chiều rộng cầu), kể cả khi gắn dưới biển cấm | **IGNORE** — không giải thích biển chính nên không phải biển phụ |
| Bảng thông tin dự án, bảng "CÔNG TRƯỜNG ĐANG THI CÔNG", băng rôn, bảng tuyên truyền | **IGNORE** (không phải biển QCVN, dù có hình biển nhỏ in bên trong) |
| Bảng thông tin nhà chờ / lộ trình / giờ chạy xe buýt (nhiều dòng chữ, số tuyến) | **IGNORE** — chỉ biển vuông xanh **một hình xe buýt** (bến xe buýt) mới là `informative/informative_other` |
| Tấm xanh nhỏ **đứng riêng** (không gắn dưới biển chính), **không đọc được chữ**, không phân biệt được biển chỉ dẫn luật hay bảng địa danh | **LABEL** `informative/unknown`, tick `needs_review` (quy tắc "phân vân thì vẽ", mục 7) |
| Biển quảng cáo, biển quán, "BÁN ĐẤT", tờ rơi dán cột | **IGNORE** |
| Mặt sau biển; biển nhìn thấy từ cạnh (chỉ thấy một vệt mỏng) | **IGNORE** |
| Dây rào, cọc tiêu, barie, vạch sơn trên đường, đèn giao thông | **IGNORE** |
| Biển cao < 12 px | **IGNORE** |

## 6. Visibility / occlusion

- Bị che ≤ 50 %, còn đọc được: label bình thường, box ôm phần thấy.
- Bị che > 50 % hoặc chỉ còn thấy màu/hình: label, `sign_class=unknown`.
- Ngược sáng / chói / mờ do chuyển động: còn nhận được ký hiệu thì gán bình thường; không thì family theo hình dạng,
  class `unknown`.
- Nhỏ/xa 12–20 px: thường chỉ gán được family; class chỉ gán khi đọc được thật sự sau khi zoom.

### 6.1 Biển quá xa / quá mờ: gán đến mức nào

Trước khi quyết định, **zoom 200–400 %** (cuộn chuột trong CVAT) lên biển. Không suy từ ảnh khác hay từ "chỗ này
thường có biển gì". Sau khi zoom, xếp biển vào đúng một mức:

| Mức | Thấy được gì sau khi zoom | `sign_family` | `sign_class` | `needs_review` |
|---|---|---|---|---|
| A | Hình dạng + màu + **đọc được** ký hiệu/chữ số | theo bảng mục 4 | biển cụ thể | tắt |
| B | Hình dạng **và** màu rõ (tròn viền đỏ, tam giác vàng viền đỏ, tròn xanh, vuông xanh…) nhưng **không đọc được** ký hiệu | theo hình dạng + màu | `unknown` | tắt |
| C | Chắc chắn là **biển báo giao thông** (tấm biển gắn trên cột/giá biển, đứng cạnh đường, quay mặt về camera) nhưng **không xác định được hình dạng hoặc màu** (chỉ là một đốm mờ, ngược sáng thành bóng đen) | `unknown` | `unknown` | tắt |
| D | **Không chắc** đó là biển giao thông hay vật khác (đèn, gương, bảng quán nhỏ, tờ rơi) | — | — | vẽ box theo mức B hoặc C, **tick** `needs_review` |

Quy tắc đi kèm:

- Ngưỡng 12 px vẫn áp dụng trước mọi mức: mặt biển cao < 12 px → **IGNORE**, kể cả khi thấy rõ là biển.
- Mức C vẫn phải có box: model cần biết "ở đây có biển" dù không biết loại. Box ôm phần đốm/tấm biển thấy được.
- `relevant_to_ego` ở mức B/C vẫn gán theo **vị trí** (mục 7) — vị trí luôn nhìn thấy được kể cả khi không đọc được
  biển. Chỉ dùng `unknown` khi vị trí thật sự không cho biết (mục 7).
- Chỉ dùng `sign_family=unknown` ở mức C/D. Đã thấy tròn viền đỏ thì phải là `prohibitory`, không được để `unknown`.
- **Xác định màu khi biển nhỏ:** zoom 400 %, nhìn **viền ngoài cùng** — viền đỏ (biển cấm/nguy hiểm) hay cả mặt xanh
  (hiệu lệnh/chỉ dẫn). Không suy family từ vị trí hay biển bên cạnh (ví dụ "đứng cạnh đèn nên chắc là biển chỉ dẫn").
  Vẫn không chắc đỏ hay xanh → mức C.
- Mức D: vẽ box + tick `needs_review`. Vật mức D cao < 16 px (tấm tối nhỏ có thể là mặt sau biển, bảng màu nhỏ ở xa)
  không vẽ cũng chấp nhận; **đã vẽ thì bắt buộc tick** `needs_review`.
- Nhiều biển xa dính nhau trên cùng một cột mà không tách được từng mặt: vẽ **1 box cho cả cụm**, mức C, tick
  `needs_review`.
- **Đốm tối/mờ dưới gầm cầu, dưới dầm, trong bóng đổ:** trước khi xếp mức D, kiểm xem có thấy **mặt phẳng với viền
  khép kín** (dấu hiệu chắc chắn là một mặt biển, dù không rõ mặt trước hay sau) hay chỉ là **khối bóng đổ không
  viền** (kết cấu cầu, giá đỡ, dây điện). Có viền khép kín nhưng không chắc mặt nào → vẫn mức D (vẽ + `needs_review`),
  **không tự suy diễn là mặt sau rồi IGNORE** chỉ vì nó tối. Không có viền, chỉ là mảng tối vô định hình → IGNORE,
  không vẽ. Annotator đã tick `needs_review` cho một vật mức D không khớp gold vẫn được ghi nhận là làm đúng quy
  trình (peer đã hedge đúng cách); owner xử lý sai lệch này ở revision log, không quy là lỗi coaching riêng của
  annotator.

## 7. Ambiguity / escalation

**`relevant_to_ego`** (xe đi bên phải đường; trái/phải theo quy ước mục 0). Căn cứ: QCVN đặt biển **bên phải theo
chiều đi**, có thể đặt bổ sung bên trái hoặc phía trên — vì vậy lề phải → `yes`, lề trái không có bản lặp → `unknown`.

Kiểm **theo thứ tự**, dừng ở dòng đầu tiên đúng:

| # | Biển ở đâu (biển quay mặt về camera) | `relevant_to_ego` |
|---|---|---|
| 1 | Quay lệch hẳn sang đường ngang, hoặc ở phía chiều ngược lại bên kia dải phân cách | `no` |
| 2 | (d) Trên rào chắn / giá tạm đặt trên chính phần đường ego | `yes` |
| 3 | (c) Treo trên làn ego (giá long môn) hoặc gắn trên dầm cầu vượt mà ego sắp chui qua | `yes` |
| 4 | (a) Lề **phải** đường ego | `yes` |
| 5 | (b) **Đầu** dải phân cách ngăn ego với chiều ngược lại, đứng chắn ngay trước hướng đi của ego, **không có nhánh đường nào** giữa ego và biển (ví dụ cấm đi ngược chiều + đi vòng bên phải) | `yes` |
| 6 | Còn lại: lề trái, đảo giữa một **nhánh rẽ / đường gom / đường dưới gầm cầu** và đường ego, góc giao lộ — không suy ra được biển phục vụ ego hay nhánh kia | `unknown` + **bắt buộc** `needs_review` |

- Biển phụ: relevance **giống biển chính** nó gắn dưới.
- Relevance gán theo vị trí nên vẫn gán được khi không đọc được biển (mức B/C).
- Biển ở dòng 6 mà có **bản lặp** ở lề phải (cùng loại biển, cùng khoảng cách): cả hai `yes`.
- **Biển xa và mờ không tự động là `unknown`:** "xa/mờ" chỉ ảnh hưởng `sign_class` (mục 6.1), không ảnh hưởng
  `relevant_to_ego`. Nếu vị trí biển vẫn xác định rõ là lề phải đường ego (dòng 4) dù ở cuối một giao lộ dài, vẫn gán
  `yes`. Chỉ gán `unknown` khi **vị trí** (không phải độ rõ nét) rơi vào dòng 6.

**Biển tạm mâu thuẫn biển cố định** (ví dụ mũi tên đi vòng trái trên rào công trường và mũi tên đi vòng phải cố định ở
dải phân cách): label **cả hai** với relevance theo vị trí như bình thường. Theo QCVN, người đi đường chấp hành **biển
tạm**; downstream tự xử lý thứ tự ưu tiên — annotator không bỏ biển nào.

| Quyết định | Khi nào | Trong CVAT |
|---|---|---|
| LABEL | Biển trong scope, đủ bằng chứng | Box `traffic_sign` + đủ 3 attribute khác `__undefined__` |
| IGNORE | Vật trong bảng IGNORE mục 5 | Không vẽ gì |
| UNKNOWN | Không đọc được class, hoặc không suy ra được relevance | `sign_class=unknown` và/hoặc `relevant_to_ego=unknown` |
| ESCALATE (object) | Relevance `unknown`, hoặc phân vân LABEL/IGNORE | Vẫn vẽ box, tick `needs_review` |
| ESCALATE (ảnh) | Cả ảnh không đủ bằng chứng | Tag ảnh `image_escalate` |

Phân vân "có phải biển trong scope không" → **vẽ box**, gán theo thứ thấy được, tick `needs_review`.

`needs_review` chỉ dùng cho ba trường hợp: relevance `unknown`, phân vân LABEL/IGNORE, vật mức D. Không chắc class →
`sign_class=unknown`, không tick.

## 8. Temporal rule

Không áp dụng — task ảnh tĩnh, dùng **Shape**, không dùng Track.

## 9. Examples

Ảnh ví dụ trong `data/vtsd/` (split example). Toạ độ (x, y) gần đúng trên ảnh 1622×626.

| sample_id | Thấy gì | Expected output | Rule áp dụng |
|---|---|---|---|
| VN01 | Cột bên phải: tròn "50" trên, tròn cấm rẽ trái dưới; dải cây bên trái: tròn cấm rẽ phải + tấm phụ hình xe tải; biển nhỏ xa ~14 px | Phải: `prohibitory/speed_limit_50/yes` (≈ 811–843, 195–226), `prohibitory/no_turn_left/yes` (≈ 804–839, 228–262). Trái: `prohibitory/no_turn_right` + `supplementary/supplementary_plate`, relevance `unknown` + `needs_review` (lề trái, không có bản lặp bên phải). Biển xa ≈ 672–686, 275–288: `prohibitory/unknown` | mục 2, mục 4 (không đoán), mục 7 |
| VN02 | Dải phân cách trái: biển làn xanh + cột có cấm dừng đỗ và biển tròn nhỏ; lề phải: biển làn có số tốc độ, cấm đỗ + vuông xanh mũi tên | Biển làn: 1 box cả tấm — `mandatory/lane_vehicle_permission` (trái) và `mandatory/lane_vehicle_speed` (phải ≈ 1021–1059, 332–393). `no_stopping_parking`, `no_parking` đọc theo hình. Vuông xanh mũi tên gắn dưới cấm đỗ, giải thích hướng tác dụng: `supplementary/supplementary_plate` | mục 2 (biển làn), mục 4 (biển phụ bất kể màu nền) |
| VN03 | Lề phải: biển vuông xanh "CHỢ – MARKET"; xa: tam giác giao nhau ~21 px; biển rất nhỏ < 12 px; bảng "BÁN TRÀ" | `informative/informative_other/yes`; tam giác xa: `danger/danger_intersection/yes`. Biển < 12 px và bảng quảng cáo: IGNORE | mục 5, mục 6 |
| VN04 | Đường ngoại ô, chỉ có bảng quán ("CẦM ĐỒ", "BÚN PHỞ CƠM") và bảng xanh địa danh nhỏ | Không box; tag ảnh `no_target_sign` | mục 2, mục 5 |

Ví dụ từ ảnh calibration (lấy từ edge-case card của nhóm, không phải ảnh blind):

| sample_id | Thấy gì | Expected output | Rule áp dụng |
|---|---|---|---|
| VN11 | Cột lề phải 3 tấm: tròn đỏ "13 t"; tròn đỏ hai ô tô + "30 m"; tấm xanh "CẦU KIỆU — DÀI 38.9 m — RỘNG 15.2 m" | `prohibitory/weight_limit/yes`; `prohibitory/min_distance/yes` (có số mét nên **không** phải `no_overtaking`). Tấm tên cầu: IGNORE (không phải biển phụ, không phải `height_limit`) | mục 4, mục 5 |
| VN08 | Trụ bên trái: bảng thông tin nhà chờ xe buýt nhiều dòng chữ (số tuyến, giờ chạy) | Không box — bảng thông tin, không phải biển pictogram bến xe buýt | mục 5 |
| VN06 | Lề phải: tròn đỏ cấm xe tải + tấm trắng ghi giờ bên dưới; biển xa bên kia ngã tư; bảng quảng cáo xanh lá trên nóc nhà | 2 box riêng: `prohibitory/no_truck/yes` và `supplementary/supplementary_plate/yes`. Biển xa ≥ 12 px: family theo hình, class `unknown` nếu không đọc được. Quảng cáo, bảng tên trạm xăng: IGNORE | mục 2, mục 5, mục 6.1 |

Ví dụ rút ra từ blind handoff v2.0 (sau khi gold đã freeze — dùng để huấn luyện vòng sau, không phải câu hỏi calibration):

| sample_id | Thấy gì | Expected output | Rule áp dụng |
|---|---|---|---|
| VN12 | Dưới gầm cầu, cạnh biển tròn nhỏ rõ, có một đốm tối bầu dục mờ (ảnh chụp ngược sáng) sát cùng cột | Đốm tối không thấy viền khép kín rõ ràng, giống khối bóng đổ của kết cấu cầu hơn là một mặt biển → **IGNORE**, không vẽ. Nếu zoom 400 % vẫn thấy một viền/mặt phẳng khép kín mà không chắc mặt nào → vẽ mức D + `needs_review` (mục 6.1) | mục 5, mục 6.1 (đốm tối dưới gầm cầu) |
| VN16 | Tấm biển xanh nhỏ đứng trên cột cạnh đèn tín hiệu, bên kia một giao lộ rộng dưới gầm cầu vượt; đèn tín hiệu (2 đèn xanh) đứng gần một biển cấm tròn viền đỏ cùng khu vực tối | (1) Đèn tín hiệu giao thông luôn IGNORE (mục 5) dù ở vùng tối dễ nhầm hình tròn với biển — phân biệt bằng **màu phát sáng đều** (đèn) so với **viền sơn cố định** (biển). (2) Tấm xanh cạnh đèn tín hiệu bên kia giao lộ vẫn phải quét và LABEL nếu ≥ 12 px, dù ở xa và dễ bị bỏ sót khi mắt tập trung vào phần đường gần ego trước | mục 1 (bước quét), mục 5 |



## 10. Common mistakes

1. Box gồm cả cột, giá rào hoặc biển phụ → box chỉ ôm mặt biển; biển phụ là box riêng.
2. Đoán số tốc độ/tấn/mét khi mờ → `unknown`.
3. Vẽ bảng "CÔNG TRƯỜNG ĐANG THI CÔNG", bảng dự án, bảng địa danh → IGNORE.
4. Bỏ qua biển tạm trên rào công trường vì "không cắm cột" → vẫn LABEL.
5. Nhầm `keep_left` / `keep_right`: nhìn **đầu mũi tên chỉ về phía nào** thì đi vòng phía đó.
6. Nhầm `road_closed` (tròn trắng trống) với `no_entry` (tròn đỏ vạch trắng).
7. Vẽ từng cột của biển làn → 1 box cả tấm.
8. Tấm ZONE có vạch chéo đen → `end_of_prohibition`, không phải biển cấm bên trong.
9. Để `__undefined__` ở `relevant_to_ego`; hoặc relevance `unknown` mà quên tick `needs_review`.
10. Không vẽ biển xa vì "không biết biển gì" → vẫn vẽ nếu ≥ 12 px, gán theo mức B/C mục 6.1.
11. Chọn `keep_left`/`keep_right` theo vị trí biển trên đường thay vì theo đầu mũi tên (mục 0).
12. Để `sign_family=unknown` dù đã thấy rõ hình dạng + màu → `unknown` chỉ dùng ở mức C/D.
13. Gán số trên nền xanh thành `speed_limit_*` → đó là `min_speed` (tốc độ tối thiểu).
14. Gán tam giác đỉnh xuống thành `danger_other` hoặc `unknown` → `give_way`; bát giác STOP → `mandatory/stop`.
15. Vẽ tấm tên cầu dưới biển cấm thành biển phụ → IGNORE.
16. Gán `yes` cho biển trên đảo giữa ego và một nhánh rẽ / đường dưới gầm cầu → `unknown` + `needs_review` (mục 7,
    dòng 6). Chỉ biển ở **đầu dải phân cách chắn trước hướng đi ego** mới là `yes`.
17. Vẽ bảng lộ trình / giờ chạy xe buýt thành `informative_other` → IGNORE.
18. Đoán family của biển nhỏ theo ngữ cảnh (gần đèn, gần biển khác) → xem **màu viền** sau khi zoom 400 % (mục 6.1).
19. Gán biển hai xe + số mét thành `no_overtaking` → `min_distance`.
20. Đọc số trên biển tốc độ nhỏ < 20 px khi chữ số chưa rõ → `unknown` (mục 4, phép thử đọc được).
21. Box biển nhỏ rộng hơn mặt biển vài px → zoom ≥ 200 % khi vẽ; biển < 30 px chỉ được lệch ≤ 2 px mỗi cạnh.
22. Kéo box lệch sang biển bên cạnh khi nhiều biển đứng sát nhau trên cùng rào/cột (tam giác—tròn—tam giác…) → sau
    khi vẽ, phóng to kiểm lại từng box có đúng ôm **mặt biển của chính nó**, không trùm một phần biển kế bên.
23. Nhầm đèn tín hiệu giao thông (đặc biệt đèn xanh trong vùng tối/ngược sáng) với biển tròn → xem mục 5; đèn tín
    hiệu **luôn** IGNORE, kể cả khi hình tròn phát sáng trông giống biển.
24. Bỏ sót biển ở xa cuối giao lộ hoặc cạnh cột đèn tín hiệu bên kia đường vì mắt tập trung vào phần đường gần ego
    trước → bước quét (mục "Quy trình mỗi ảnh", bước 1) nay gồm cả khu vực này.

Chưa có ảnh ví dụ trong bộ example/calibration cho STOP, nhường đường, đường ưu tiên, tốc độ tối thiểu — nhận biết theo
mô tả hình ở bảng mục 4.
