# Peer feedback + owner response

Phần 1 do **nhóm peer** trả lời (gửi kèm file export). Phần 2 do **nhóm owner** điền. Thay mọi placeholder mới
là xong (gate G5).

- **Nhóm peer:** Peer review độc lập
- **Người label blind:** Codex (mô phỏng annotator mới)

## 1. Peer trả lời

1. Rule rõ nhất là quy trình 6 bước, ngưỡng 12 px, bảng LABEL/IGNORE và nguyên tắc không đọc được thì dùng `unknown` thay vì đoán.
2. Rule khó nhất là `relevant_to_ego` tại đảo giao thông, nhánh rẽ và khu vực dưới cầu vượt; annotator phải đối chiếu nhiều điều kiện vị trí.
3. VN16 làm guideline khó áp dụng nhất vì có nhiều tầng đường, biển nhỏ và vị trí áp dụng không trực quan.
4. Default `__undefined__` giúp phát hiện thiếu attribute nhưng làm thao tác chậm; dễ quên tick `needs_review` khi `relevant_to_ego=unknown`.
5. Nên thêm một decision tree ngắn kèm hai crop minh họa `relevance=yes` và `relevance=unknown` ở cầu vượt/đảo giao thông.

## 2. Owner phân loại

Owner không tranh luận để bảo vệ guideline. Mỗi feedback và mỗi decision peer làm sai được xếp vào một hướng xử lý.
GTS đo được: D 73.1 % (19/26), C 100 % (8/8, không critical escape), G 100 % (5/5), I 70 % (1 câu hỏi) → **GTS 80.8**
(`gts_summary.md`, `transfer_score.csv`).

| Feedback / decision sai | Nguyên nhân (guideline gap / data ambiguity / execution error) | Xử lý (accept + revise / reject with evidence / add escalation rule) | Bằng chứng |
|---|---|---|---|
| VN12 d1: peer vẽ thêm 1 box (đốm tối bầu dục dưới gầm cầu) ngoài 2 biển gold yêu cầu, đã tick `needs_review` | guideline gap: mục 6.1 mức D bảo "không chắc là biển → vẽ + needs_review" nhưng mục 5 "mặt sau biển → IGNORE" không nói rõ cách phân biệt khi vật quá tối/mờ dưới gầm cầu — peer hedge đúng quy trình, chỉ khác gold ở việc đây có phải mục tiêu thật hay không | accept + revise | `transfer_score.csv` VN12/d1; `02_guideline.md` v3.0 mục 6.1 (đoạn "Đốm tối/mờ dưới gầm cầu…") + ví dụ VN12 mục 9 |
| VN14 d6, d8: box `danger_road` và `danger_construction` lệch sang vị trí biển bên cạnh (chỉ ~34 % và ~50 % diện tích trùng biển thật), dù thứ tự class theo trái→phải đúng | execution error: 4 biển đứng sát nhau trên cùng rào, guideline geometry (mục 3, tolerance ≤ 10 %) đã đủ rõ, peer chỉ kéo ẩu | reject with evidence | `transfer_score.csv` VN14/d6, VN14/d8 (toạ độ box vs toạ độ gold); `02_guideline.md` v3.0 mục 10 dòng 22 (mistake mới) |
| VN16 d1, d2: biển `no_car` thật ở (335-357;465-482) bị bỏ sót; 2 box peer vẽ ngay cạnh đó thực ra nằm trên 2 đèn tín hiệu giao thông (đã xác nhận bằng ảnh tăng sáng) | execution error: mục 5 đã liệt "đèn tín hiệu giao thông" là IGNORE rõ ràng; vùng ảnh rất tối khiến peer nhầm, nhưng rule không mơ hồ | reject with evidence + escalation rule | `transfer_score.csv` VN16/d1, VN16/d2; `02_guideline.md` v3.0 mục 10 dòng 23 + ví dụ VN16 mục 9 |
| VN16 d4: tấm biển xanh `informative` tại (736-761;470-486) cạnh đèn tín hiệu, bên kia giao lộ, bị bỏ sót hoàn toàn (không có box) | guideline gap nhẹ: bước quét (mục "Quy trình mỗi ảnh" bước 1) trước v3.0 không nhắc annotator quét khu vực cột đèn tín hiệu/biển bên kia giao lộ, dễ bỏ sót ở giao lộ rộng nhiều tầng đường | accept + revise | `transfer_score.csv` VN16/d4; `02_guideline.md` v3.0 mục "Quy trình mỗi ảnh" bước 1 (đã thêm "cột đèn tín hiệu và biển đứng bên kia giao lộ") + mục 10 dòng 24 |
| VN16 d5: biển xa & mờ (850-860;485-497) peer gán `relevant_to_ego=unknown` + `needs_review`, gold yêu cầu `yes` | guideline gap: mục 7 đã có rule "vị trí quyết định relevance kể cả khi không đọc được biển" (mục 6.1) nhưng không nói rõ "xa/mờ" không tự động kéo relevance về `unknown` — dễ nhầm lẫn giữa độ rõ nét (ảnh hưởng `sign_class`) và vị trí (ảnh hưởng `relevant_to_ego`) | accept + revise | `transfer_score.csv` VN16/d5; `02_guideline.md` v3.0 mục 7 (dòng "Biển xa và mờ không tự động là `unknown`") |
| Peer câu 1: rule 6 bước, ngưỡng 12 px, bảng LABEL/IGNORE, "không đọc được thì `unknown`" là rõ nhất | không phải lỗi — xác nhận rule hoạt động tốt | giữ nguyên (không đổi) | `02_guideline.md` mục "Quy trình mỗi ảnh", mục 3, mục 4 |
| Peer câu 2: `relevant_to_ego` ở đảo giao thông/nhánh rẽ/gầm cầu là khó nhất | guideline gap một phần (đã có bảng 6 dòng mục 7 nhưng thiếu ví dụ trực quan cho các case biên) | accept + revise | `02_guideline.md` v3.0 mục 9 (ví dụ VN12, VN16 mới); tương ứng VN12 d1 và VN16 d5 ở trên |
| Peer câu 3: VN16 khó áp dụng nhất (nhiều tầng đường, biển nhỏ, vị trí không trực quan) | trùng khớp với các lỗi thực đo được ở VN16 (d1, d2, d4, d5) — xác nhận đây đúng là điểm yếu của guideline v2.0, không phải peer bất cẩn đơn thuần | accept + revise | Toàn bộ 4 dòng VN16 phía trên; `02_guideline.md` v3.0 |
| Peer câu 4: default `__undefined__` giúp phát hiện thiếu attribute nhưng làm chậm thao tác; dễ quên tick `needs_review` khi `relevant_to_ego=unknown` | data ambiguity/tooling, không phải lỗi nội dung guideline — default `__undefined__` là cơ chế QA bắt lỗi thiếu attribute, đánh đổi lấy một chút thao tác chậm là chấp nhận được | reject with evidence (giữ nguyên default; checklist cuối mỗi ảnh trong mục "Quy trình mỗi ảnh" đã nhắc kiểm `needs_review` trước khi sang ảnh kế) | `02_guideline.md` dòng "Trước khi sang ảnh kế…" (đã có từ v2.0, không đổi) |
| Peer câu 5: thêm decision tree ngắn + 2 crop minh hoạ relevance `yes`/`unknown` ở cầu vượt/đảo giao thông | guideline gap — đề xuất cụ thể, khớp với lỗi thực đo (VN16 d5) | accept + revise | `02_guideline.md` v3.0 mục 9 (ví dụ VN16), mục 7 (rule biển xa/mờ) |

**Tổng kết root cause:** 3/7 dòng là execution error thuần (VN14 d6, VN14 d8, VN16 d1+d2 gộp), có bằng chứng toạ độ
rõ ràng nên reject không sửa guideline. 4/7 dòng là guideline gap thật (VN12 d1, VN16 d4, VN16 d5, và câu hỏi
độc lập trong `clarification_log.csv`) → đã revise trong `02_guideline.md` v3.0, chi tiết đổi gì/vì sao ghi ở
`08_revision_log.md` dòng v3. Không có critical decision nào bị bỏ lỡ (8/8 critical đúng) nên không áp dụng
critical cap.
