---
name: research-direction-prototyping
description: "Quy trình biến một ý tưởng hướng nghiên cứu thành kế hoạch đã được kiểm chứng bằng code: viết direction doc, trinh sát code gốc, dựng prototype chạy được, chạy sanity checks định lượng, viết báo cáo và sửa ngược kế hoạch, tạo chuỗi prompts làm sâu. Dùng khi user muốn 'phát triển hướng nghiên cứu', 'lên kế hoạch cho paper tiếp theo', 'code thử ý tưởng', hoặc 'đánh giá tính khả thi' của một hướng kỹ thuật trong bất kỳ dự án nghiên cứu tính toán nào."
---

# Research Direction Prototyping — từ ý tưởng đến kế hoạch đã kiểm chứng

Nguyên tắc trung tâm: **kế hoạch chưa chạy code là kế hoạch chưa đáng tin**. Mọi ước lượng trong direction doc (chi phí, độ chính xác, kích thước bài toán) phải được coi là giả thuyết cho đến khi một prototype đo được con số thật. Kinh nghiệm thực tế: trong một lần áp quy trình này, 2/3 ước lượng then chốt của kế hoạch sai và chỉ lộ ra khi chạy thử (chi phí solver thấp hơn 100 lần so với lo ngại; một xấp xỉ tưởng "đủ tốt" hóa ra lệch gấp 30 lần target).

## Giai đoạn 1 — Direction doc (mỗi hướng một file)

Thu thập nguồn: future work trong paper hiện tại, ghi chú thảo luận với advisor/nhóm, known issues trong code, planning docs cũ. Mỗi hướng viết một file Markdown đánh số (`01_xxx.md`, `02_yyy.md`...) với cấu trúc bắt buộc:

1. **Nguồn** — ý tưởng đến từ đâu (trích được thì trích)
2. **Ý tưởng** — bài toán là gì, tại sao lợi thế của phương pháp hiện tại tăng/giữ ở bài toán mới
3. **Thách thức kỹ thuật và cách giải** — bảng phương án kèm ưu/nhược, chọn phương án chính + phương án lùi
4. **Những gì tái dùng từ codebase** — liệt kê cụ thể file/hàm nguyên vẹn vs cần viết mới
5. **Kế hoạch theo tuần** — mốc kiểm chứng được, không phải mô tả công việc
6. **Định vị** — thành paper riêng, section, hay response-to-reviewers; target venue
7. **Rủi ro** — kèm phương án phòng bị cho từng cái

Kèm một README tổng xếp ưu tiên các hướng theo 3 tiêu chí: tái dùng hạ tầng được bao nhiêu, khe hở văn liệu có thật không, nhóm có đủ năng lực/thiết bị không.

## Giai đoạn 2 — Trinh sát code gốc (TRƯỚC khi viết dòng code mới nào)

Đọc kỹ các module sẽ bị ảnh hưởng trong codebase hiện có. Câu hỏi phải trả lời:

- Cái gì đã generic sẵn mà không ai để ý? (ví dụ thật: solver SDP viết cho binary nhưng vốn đã generic theo M)
- Cái gì trông tái dùng được nhưng thật ra phải viết mới? (ví dụ thật: head chỉ xử lý ma trận thực trong khi bài mới cần phức)
- Convention nào phải giữ nguyên để so sánh được với kết quả cũ? (công thức, tham số ranges, format dataset — kể cả BUG đã biết cũng giữ và ghi chú, để nhãn tương thích)

Ghi kết quả trinh sát vào direction doc mục 4.

## Giai đoạn 3 — Prototype tối thiểu chạy được

Layout chuẩn cho mỗi prototype (folder `prototype_<tên>/`):

```text
prototype_<tên>/
├── <package>/          # logic thuần: import được, không side effect, không argparse
│   └── (chia subpackage theo domain: physics/, labeling/, models/...)
├── scripts/            # entry points có CLI, bootstrap sys.path về package
├── tests/run_sanity.py # kiểm chứng tự động (giai đoạn 4)
├── configs/*.json      # config chuẩn + config smoke
├── data/, runs/        # gitignored
├── README.md           # pipeline diagram + bảng module map (trạng thái + kế thừa từ đâu)
├── BAO_CAO.md          # giai đoạn 5
└── PROMPTS_10.md       # giai đoạn 6
```

Quy tắc viết prototype:

- Bám sát API của code gốc (tên hàm, format data) để merge lại dễ.
- Cái gì chưa làm được ngay thì viết KHUNG có docstring giải thích vai trò + `TODO(prompt-N)` trỏ đến prompt sẽ hoàn thiện nó. Khung phải import được, chạy raise NotImplementedError có chỉ dẫn.
- Tái dùng package của prototype trước qua sys.path nếu các hướng chia sẻ hạ tầng.
- Kiểm tra thư viện có sẵn trước khi viết code phụ thuộc (`python3 -c "import X"`).

## Giai đoạn 4 — Sanity checks định lượng (phần quan trọng nhất)

Viết `tests/run_sanity.py` gồm các check ĐÁNH SỐ, mỗi check trả lời MỘT câu hỏi lập kế hoạch cụ thể, in kết quả PASS/FAIL kèm con số đo được. Chọn check theo nguyên tắc: **kiểm những giả định mà kế hoạch đứng lên trên** (load-bearing assumptions), không kiểm những gì hiển nhiên đúng. Các loại check điển hình:

1. **Giới hạn biểu diễn** — kích thước bài toán thật sự cần bao nhiêu (truncation, độ phân giải, dimension)? Thường lộ ra ước lượng cũ quá thận trọng hoặc quá lạc quan.
2. **Chi phí thật** — đo ms/sample của thành phần đắt nhất trên máy thật, so với ước lượng lý thuyết. Big-O của paper thường không áp cho solver/hardware thực tế.
3. **Độ chính xác của xấp xỉ then chốt** — gap giữa phương pháp nhanh và ground truth, đo trên phân bố bài toán thật chứ không phải case đẹp.
4. **Tính đúng chéo** — case đặc biệt có nghiệm giải tích/phương pháp cũ phải khớp (regression test với kết quả đã publish).
5. **Tính hợp lệ theo cấu trúc** — ràng buộc (vật lý, toán) có giữ tại trọng số ngẫu nhiên không, gradient có chảy không.
6. **Smoke train/run end-to-end** — pipeline nhỏ nhất chạy trọn vòng, metric có giảm không.

Chạy thật, không mô phỏng kết quả. Nếu môi trường thiếu thư viện thì check đó chuyển thành khung + TODO, không giả số.

## Giai đoạn 5 — Báo cáo và SỬA NGƯỢC kế hoạch

Viết `BAO_CAO.md` (ngôn ngữ của nhóm; nếu có skill viết học thuật thì invoke trước): mục tiêu, module đã dựng kèm lựa chọn thiết kế đáng ghi, kết quả kiểm chứng với số liệu, **các phát hiện làm thay đổi kế hoạch**, rủi ro còn lại, việc tiếp theo.

Bước bắt buộc hay bị quên: **quay lại sửa direction doc gốc**. Ước lượng nào sai thì gạch (strikethrough) kèm chú thích "SAI" và con số đo được, có ngày tháng. Direction doc phải luôn phản ánh hiểu biết mới nhất, không được để người đọc sau tin vào số cũ.

## Giai đoạn 6 — Chuỗi prompts làm sâu

Viết `PROMPTS_10.md`: chuỗi ~10 prompts tuần tự cho các phiên làm việc sau (với AI coding assistant hoặc người). Mỗi prompt:

- Tự chứa đủ context: path đầy đủ, file liên quan, con số tham chiếu
- Có câu hỏi khoa học cụ thể phải trả lời, không chỉ mô tả việc
- Có tiêu chí chấp nhận hoặc ngưỡng quyết định ("nếu <2s/batch thì làm tiếp X, nếu chậm thì chốt Y và dừng")

Đánh dấu trong phần ghi chú: prompt nào là **gate** (kết quả quyết định đi tiếp hay đổi hướng), sau prompt nào cần dừng review với người khác trước khi đi tiếp.

## Checklist toàn quy trình

- [ ] Direction doc đủ 7 mục, có README xếp ưu tiên
- [ ] Đã đọc code gốc trước khi viết code mới; mục "tái dùng" ghi cụ thể đến mức file/hàm
- [ ] Prototype theo layout package/scripts/tests/configs; khung có TODO(prompt-N)
- [ ] Sanity checks đánh số, mỗi check gắn với một giả định của kế hoạch, chạy thật
- [ ] BAO_CAO có mục "phát hiện làm thay đổi kế hoạch"
- [ ] Direction doc gốc đã được sửa ngược với số đo thật (kể cả khi phải tự nhận ước lượng cũ SAI)
- [ ] PROMPTS có gate và điểm dừng review
