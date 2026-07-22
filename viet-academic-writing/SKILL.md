---
name: viet-academic-writing
description: "Dùng skill này khi tạo bất kỳ văn bản học thuật tiếng Việt nào: bài tiểu luận, báo cáo nhóm, slide thuyết trình, bài tập môn học, luận văn, hay bất kỳ tài liệu nào xuất ra file .docx hoặc LaTeX. Trigger khi người dùng yêu cầu viết bài, soạn thảo, tạo file Word, tạo slide nội dung, hoặc viết văn bản tiếng Việt dùng cho mục đích học thuật hoặc nộp bài. LUÔN dùng skill này trước khi viết bất kỳ đoạn văn tiếng Việt nào có độ dài từ 100 chữ trở lên."
---

# Viết văn bản học thuật tiếng Việt tự nhiên

Mục tiêu: tạo ra văn bản đọc lên nghe như do người thật viết, không lộ dấu AI.

---

## I. ĐỊNH DẠNG FILE (Word / LaTeX)

### Font và màu sắc
- Font: **Times New Roman**, 13pt hoặc 14pt, màu đen tuyệt đối
- KHÔNG dùng chữ màu, tiêu đề bảng màu, tô nền ô bảng
- Giãn dòng: 1.5, lề trái 3cm, lề phải 2cm
- Đầu dòng thụt vào 1.27cm, không thêm dòng trắng giữa các đoạn

### Ký tự TUYỆT ĐỐI không dùng
| Ký tự cấm | Thay bằng |
|---|---|
| Em dash – hoặc en dash — | Dấu phẩy, chấm phẩy, hoặc viết lại câu |
| \*\*từ\*\* (Markdown bold) | Tag định dạng thực trong docx/LaTeX |
| Dấu ~ và \| ngoài bảng | Viết lại câu |
| Gạch đầu dòng – hoặc • | Số thứ tự hoặc văn xuôi liệt kê |
| Ký hiệu Markdown ##, \`\`\` | Xóa toàn bộ khỏi output |

### Bảng biểu
- Viền đơn giản, không màu nền, không chữ màu ở hàng tiêu đề
- Hàng tiêu đề: in đậm, nền trắng, viền đen

---

## II. NGÔN NGỮ VÀ VĂN PHONG

### Cấu trúc câu
- Chủ ngữ luôn xuất hiện rõ ràng — không viết kiểu "Được xem là...", "Có thể thấy rằng..." khi không rõ ai làm chủ
- Xen kẽ câu ngắn (5-10 chữ), câu trung bình (15-20 chữ) và câu dài có mệnh đề phụ
- Không lặp cùng cấu trúc câu hai lần liền nhau

### Từ ngữ cần TRÁNH (đặc trưng AI viết tiếng Việt)
- "Điều này cho thấy rằng..." dùng quá mức
- "Bên cạnh đó", "Ngoài ra", "Hơn nữa" xuất hiện liên tiếp trong cùng một đoạn
- "Nhìn chung", "Tóm lại" ở giữa bài, không phải phần kết
- "Đóng vai trò quan trọng", "có ý nghĩa quan trọng", "không thể phủ nhận"
- Cụm danh từ ghép phức tạp dịch thẳng từ tiếng Anh: "quá trình tối ưu hóa hiệu suất học tập đa chiều"
- Mở đầu bằng: "Trong bối cảnh hiện nay...", "Với sự phát triển nhanh chóng của..."

### Giọng văn và xưng hô
- Dùng đại từ nhất quán: "nhóm em", "chúng tôi", hoặc "tác giả" — không trộn lẫn trong cùng một bài
- Không dùng thể bị động liên tục: "được thực hiện", "được xem xét", "được áp dụng" nhiều lần liên tiếp
- Đôi khi một câu có thể ngắn gọn, trực tiếp — không cần mọi câu đều "đều và trơn"

### Liên kết đoạn
- Dùng từ nối tự nhiên, không gượng ép: "theo đó", "vì vậy", "tuy nhiên", "cụ thể là"
- Không mở đầu mọi đoạn bằng từ nối — câu mở đoạn đôi khi đứng độc lập hoàn toàn

---

## III. CẤU TRÚC BÀI

- Không mở đầu phần nào bằng định nghĩa từ điển ("Theo từ điển... X được hiểu là...")
- Không kết bài bằng cách liệt kê lại toàn bộ các ý đã nêu — thay vào đó hãy tổng hợp hoặc nêu hàm ý
- Tránh cấu trúc lặp cho mỗi mục: [Định nghĩa] → [Liệt kê đặc điểm] → [Ví dụ]
- Phần thân bài phải có sự chuyển tiếp ý tự nhiên, không phải chỉ các mục liệt kê nối tiếp

---

## IV. CHECKLIST TRƯỚC KHI XUẤT OUTPUT

Trước khi đưa ra kết quả cuối, tự kiểm tra từng mục:

- [ ] Có câu nào nghe như template AI không? Viết lại.
- [ ] Có dấu gạch ngang dài (– hoặc —) nào không? Xóa hoặc thay thế.
- [ ] Có từ nối nào xuất hiện quá 2 lần trong một đoạn không? Bỏ bớt.
- [ ] Chủ ngữ có rõ ràng ở mọi câu không?
- [ ] Có ký hiệu Markdown (\*\*, ##, ~, \`\`\`) nào lọt vào output không? Xóa hết.
- [ ] Đại từ xưng hô có nhất quán trong toàn bài không?
- [ ] Có đoạn nào dùng thể bị động quá 2 lần liên tiếp không? Viết lại dạng chủ động.
