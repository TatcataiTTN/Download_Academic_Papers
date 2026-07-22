---
name: self-study-bilingual-en-viet
description: "Dùng khi người dùng muốn xây bộ tài liệu tự học/ôn tập song ngữ Anh-Việt (cẩm nang công thức, bài tập có lời giải đầy đủ, ngân hàng trắc nghiệm, ghi chú lý thuyết đầy đủ theo slide bản tiếng Anh và bản tiếng Việt riêng, đề thi trắc nghiệm số) từ một thư mục slide bài giảng PDF của một môn học đại học. Trigger khi người dùng nói: "làm tài liệu ôn tập cho môn X", "hệ thống lại công thức/bài tập trong slide", "làm bộ đề trắc nghiệm cho môn này", "đọc toàn bộ slide rồi tóm tắt/làm lại đầy đủ", "làm bản song ngữ", hoặc chỉ tay vào một thư mục chứa nhiều file PDF slide và yêu cầu tạo tài liệu học tập từ đó. Áp dụng cho bất kỳ môn học nào (không riêng Fluid Mechanics/Thermodynamics), miễn đầu vào là các PDF slide bài giảng."
---

# Self-Study Bilingual EN-VIET — quy trình dựng tài liệu tự học song ngữ từ slide PDF

Skill này đúc kết quy trình đã dùng thành công cho nhiều môn học (vd Thermodynamics, Fluid
Mechanics, Mechanics — ban đầu ở USTH, nhưng áp dụng được cho **bất kỳ trường/tổ chức/khóa học
nào**, không riêng gì USTH). Mục tiêu: từ một thư mục PDF slide bài giảng, tạo ra bộ tài liệu
tự học LaTeX hoàn chỉnh, biên dịch sạch, giữ đúng số liệu gốc, không bỏ sót bài tập nào trong
slide.

## I. Khi nào dùng, và không dùng

Dùng khi: người dùng có 1 thư mục slide PDF của một môn học và muốn có công thức/bài
tập/trắc nghiệm tổng hợp để ôn thi.

Không dùng khi: người dùng chỉ hỏi giải 1 bài tập cụ thể, hoặc chỉ cần dịch/định dạng lại 1
file có sẵn (khi đó dùng thẳng `viet-academic-writing`/`en-academic-writing` cho phần văn
phong, không cần toàn bộ quy trình này).

## II. Quy trình tổng thể (bắt buộc theo đúng thứ tự)

1. **Đọc toàn bộ thư mục dự án** trước khi làm gì khác — liệt kê hết các PDF, tìm xem đã có
   thư mục `Tự học`/`Study_Materials`/`Exam_Practice` nào của môn khác trong cùng máy để
   dùng làm **template tham chiếu** (bắt chước đúng preamble LaTeX, văn phong, cấu trúc
   thư mục, tên file). Không tự bịa cấu trúc mới nếu đã có template tốt sẵn.
2. **Kiểm kê nguồn**: map mỗi file PDF vào đúng chương trong đề cương môn học (đọc slide
   tiêu đề/mục lục của Lecture 1 để lấy danh sách chương chính thức — đừng đoán tên chương
   từ tên file). Ghi rõ file nào ứng với chương nào, bao nhiêu trang, chương nào không có
   slide nguồn (bỏ qua, không tự chế nội dung cho chương thiếu).
3. **Đọc đầy đủ 100% từng PDF**, theo đoạn ≤20 trang một lần gọi tool đọc (giới hạn cứng của
   tool). Đọc xong mỗi đoạn thì ghi note cô đọng ngay ra **một thư mục thực trong dự án**
   (`<TênMôn>_<TênTrường>/Reading_Notes/notes_<tên_file>.md` — `<TênTrường>` là hậu tố tùy dự
   án cụ thể, ví dụ `_USTH`, `_PTIT`, `_HUST`, hoặc bỏ hẳn hậu tố nếu người dùng không nêu rõ
   trường/tổ chức) — đừng để dồn đến cuối mới ghi, dễ quên chi tiết số liệu.
   - **KHÔNG ghi note vào scratchpad/thư mục tạm** (vd `/tmp`, thư mục scratchpad phiên làm
     việc). Scratchpad bị dọn sạch khi phiên kết thúc — nếu phiên bị ngắt giữa chừng (hết
     context, mất kết nối, v.v.), toàn bộ note đã đọc sẽ mất và phải đọc lại từ đầu. Ghi thẳng
     vào `Reading_Notes/` trong thư mục dự án ngay từ đầu để note tồn tại lâu dài, xem lại
     được, và người dùng có thể tự kiểm tra tiến độ đọc bất cứ lúc nào.
   - Note phải giữ **nguyên số liệu** (không làm tròn, không đổi đơn vị tùy tiện).
   - Với mỗi ví dụ/bài tập trong slide, gắn cờ rõ: **ĐÃ GIẢI trong slide** (có đáp án/lời giải
     mẫu) hay **CHỈ NÊU ĐỀ** (cần tự giải sau).
   - Nếu 1 PDF có mật độ note quá thấp so với số trang (ví dụ ghi 40 dòng cho 74 trang), đó là
     dấu hiệu cần đọc lại kỹ hơn trước khi viết tài liệu cuối — người dùng có thể phát hiện ra
     và sẽ đúng.
4. **Lập danh sách tổng hợp mọi bài tập** xuất hiện trong toàn bộ slide (không chỉ các "Example"
   đánh số — quét cả các slide "Problem", bài tập không đánh số, ví dụ minh họa trong phần lý
   thuyết). Đây là danh sách sẽ dùng xuyên suốt cho Exercises_Solutions và MCQ.

## III. Bộ sản phẩm chuẩn (đặt tên và vị trí)

Theo đúng cấu trúc `<TênMôn>_<TênTrường>/` (hậu tố trường/tổ chức tùy dự án, xem ghi chú ở
bước II.3 — có thể là `_USTH`, `_PTIT`, tên trường/tổ chức khác, hoặc bỏ hậu tố):
```
Reading_Notes/                  (note đọc PDF theo bước II.3 — ghi ở đây ngay khi đọc, KHÔNG
  notes_<tên_file>.md           ghi vào scratchpad; giữ lại sau khi hoàn thành để người dùng
  ...                           đối chiếu/tra cứu lại, không cần xoá)
Study_Materials/
  <TenMon>_Formula_Guide.tex
  <TenMon>_Full_EN.tex          (nếu người dùng yêu cầu bản đầy đủ song ngữ)
  <TenMon>_Full_VN.tex
Exam_Practice/
  <TenMon>_Exercises_Solutions.tex
  <TenMon>_MCQ_80.tex
  <TenMon>_Numeric_MockExam_EN.tex   (nếu người dùng yêu cầu đề thi số riêng)
```

### 1. Formula Guide
Tổ chức theo chương → mỗi công thức 1 `formulabox` (định nghĩa/công thức + điều kiện áp
dụng) + **2 ví dụ tự soạn** (không copy nguyên bài slide) trong `examplebox`, số liệu tự chọn
nhưng vật lý hợp lý. Mục đích: người học nắm công thức qua ví dụ mới, không học vẹt bài cũ.

### 2. Exercises \& Solutions — quan trọng nhất, dễ bị làm ẩu nhất
- Tổ chức theo `Chương → Dạng N.M — <tên dạng>`.
- **PHẢI liệt kê đủ 100% bài tập/ví dụ tìm được ở bước II.4**, không được bỏ sót để "gọn".
- Mỗi bài: đề bài đầy đủ (giữ nguyên số liệu gốc) + lời giải đầy đủ từng bước ngay tại chỗ.
  **Tuyệt đối không dùng kiểu "Xem mục X ở trên"** để thay cho lời giải — kể cả khi lời giải y
  hệt đã xuất hiện ở Formula Guide hay ở phần lý thuyết, vẫn phải chép đầy đủ lại vào đây.
  Đây là lỗi đã bị người dùng phản hồi trực tiếp một lần ("Đừng có làm kiểu [SOLVED IN
  SLIDE], làm đầy đủ, chi tiết") — coi đây là mặc định, không đợi bị nhắc.
- Bài có đáp án trong slide: gắn nhãn **[ĐÃ GIẢI TRONG SLIDE]**, trình bày lại đủ bước, đối
  chiếu khớp đáp số gốc.
- Bài chỉ nêu đề: gắn nhãn **[TỰ GIẢI]**, tự giải chi tiết bằng đúng công thức trong Formula
  Guide, nêu rõ đây là tự giải.

### 3. MCQ 80 câu
8 nhóm × 10 câu, mỗi nhóm bám 1 chương/cụm chủ đề, trộn lý thuyết + tính toán. Cuối tài
liệu: bảng đáp án dạng lưới (`|c|c||c|c||c|c||c|c|`, 4 cột câu/đáp án) + 1 `infobox` "spot-check"
tính lại độc lập ~6-8 câu tính toán để tự kiểm tra không có lỗi rải rác.

### 4. (Khi được yêu cầu mở rộng) Bản đầy đủ song ngữ + đề thi số
Nếu người dùng phàn nàn tài liệu quá cô đọng / thiếu độ chi tiết theo từng slide, hoặc yêu cầu
bản tiếng Anh riêng, tiếng Việt riêng:
- Viết `Full_EN.tex` và `Full_VN.tex` **cấu trúc giống hệt nhau**, mỗi `\subsection` tương ứng 1
  cụm slide (không phải 1 file gộp chung theo kiểu chèn cả 2 ngôn ngữ xen kẽ, trừ khi người
  dùng nói rõ muốn xen kẽ trong 1 file).
  - **Hỏi rõ (AskUserQuestion) nếu người dùng dùng từ "song ngữ" mơ hồ**: xen kẽ Anh-Việt
    trong 1 file, hay 2 file riêng biệt? Đây là 2 cách hiểu khác nhau, đừng đoán.
- Cuối mỗi `\subsection`, chèn dòng trích dẫn nguồn slide chính xác — xem mục V bên dưới về
  cách lấy số trang **chính xác**, không đoán từ trí nhớ.
- Phần bài tập trong 2 file này cũng phải đầy đủ như mục III.2, không dùng pointer.
- Nếu người dùng muốn thêm 1 đề thi thuần số (plug-and-chug, áp công thức thay số, không lý
  thuyết): tạo file riêng, toàn bộ câu hỏi liệt kê trước, phần lời giải đầy đủ ở cuối, dùng
  `\hypertarget{qN}{}` / `\hyperlink{solN}{...}` (có sẵn trong `hyperref`, không cần package
  thêm) để bấm nhảy thẳng 2 chiều giữa câu hỏi và lời giải. Chia độ khó dễ→khó theo phần
  (Part A/B/C), số liệu mới tự soạn nhưng bám sát phong cách công thức trong slide.

## IV. Preamble LaTeX chuẩn (copy nguyên, đổi tiêu đề/màu nếu cần)

```latex
\documentclass[11pt,a4paper]{article}
\usepackage{fontspec}
\setmainfont{Times New Roman}
\usepackage{amsmath,amssymb}
\usepackage{geometry}
\usepackage{booktabs}
\usepackage{array}
\usepackage{graphicx}
\usepackage{xcolor}
\usepackage{tcolorbox}
\usepackage{enumitem}
\usepackage{fancyhdr}
\usepackage{colortbl}
\usepackage{hyperref}

\geometry{margin=2cm,headheight=14pt}
\pagestyle{fancy}
\fancyhf{}
\fancyhead[L]{<Tiêu đề ngắn tài liệu>}
\fancyhead[R]{\thepage}
\renewcommand{\headrulewidth}{0.4pt}

\tcbuselibrary{theorems,skins,breakable}
\newtcolorbox{formulabox}[1][]{colback=blue!5,colframe=blue!50!black,title=#1,fonttitle=\bfseries,breakable}
\newtcolorbox{examplebox}[1][]{colback=green!5,colframe=green!50!black,title=#1,fonttitle=\bfseries,breakable}
\newtcolorbox{notebox}[1][]{colback=yellow!10,colframe=orange!50!black,title=#1,fonttitle=\bfseries,breakable}
```
Với file MCQ dùng thêm `infobox` (giống `notebox`) thay vì `formulabox`/`examplebox`.

Trang bìa mẫu: `\author{Tài liệu tự học --- <Tên người dùng> (<Tên trường/tổ chức>, MSSV <mã
số nếu có>)}`, có
`\tableofcontents`, banner phân đoạn `%===...===%` trước mỗi `\section`.

## V. Lấy số trang/slide chính xác — không bao giờ đoán

Khi cần trích dẫn "slide số mấy" cho một nội dung, **không dựa vào trí nhớ đọc PDF trước đó**
(dễ sai vài trang, và sai thì bị coi là không nghiêm túc). Thay vào đó dùng Python để trích
text kèm số trang rồi grep từ khóa:

```python
import pdfplumber, json
with pdfplumber.open("Lecture1.pdf") as pdf:
    pages = [{"page": i, "text": p.extract_text() or ""} for i, p in enumerate(pdf.pages, 1)]
# rồi tìm từ khóa tiêu đề slide trong pages[*]["text"] để lấy đúng số trang
```
Lưu ý: tiêu đề slide lớn thường bị PDF tách chữ cách rời từng ký tự khi extract text (ví dụ
`"N E W T O N ' S   L A W"`) — nếu search theo cụm từ tiêu đề không ra kết quả, thử search
theo câu văn thường (bullet content) thay vì tiêu đề, nội dung đó luôn extract bình thường.

Nếu không có `pdfplumber`/`pdftotext` sẵn, kiểm tra `pip3 list | grep -i pdf` trước khi quyết
định đọc lại toàn bộ PDF bằng ảnh (tốn nhiều lượt gọi tool hơn nhiều).

## VI. Lỗi LaTeX/xelatex đã gặp thực tế — luôn kiểm tra trước

1. **Dấu phẩy trong tiêu đề tcolorbox làm vỡ pgfkeys.** `\begin{formulabox}[Density, Specific
   Weight, Specific Gravity]` → lỗi `pgfkeys Error: I do not know the key '/tcb/Specific
   Weight'`, vì `title=#1` được chèn thẳng vào chuỗi option `key=value,key=value`, dấu phẩy bị
   hiểu là ranh giới option mới. **Cách tránh**: không bao giờ để dấu phẩy trong `[...]` của
   `formulabox`/`examplebox`/`notebox`; thay bằng `--` hoặc `/`. Quét trước khi biên dịch:
   `grep -n '\\begin{\(formulabox\|examplebox\|notebox\)}\[[^]]*,'`.
2. **`\boxed{...}` (amsmath) ép nội dung vào math mode**, kể cả khi bên trong là văn xuôi lẫn
   biến. Nếu văn xuôi có dấu tiếng Việt (`ó đ ơ ị ...`) nằm trong `\boxed{}`, font toán học
   `cmmi` không có glyph đó → lỗi `Missing character`. **Cách tránh**: `\boxed{}` chỉ chứa
   thuần công thức toán (vd `\boxed{[G] = T^{-1}}`), câu chữ mô tả để ngoài `\boxed`.
3. **Subscript chứa từ tiếng Việt không bọc `\text{}`** cũng lỗi tương tự, ví dụ `\dot
   m_{vào}` phải viết `\dot m_{\text{vào}}`.
4. Luôn dùng **xelatex** (không dùng pdflatex) — cần cho Unicode tiếng Việt + font Times New
   Roman qua `fontspec`.
5. Biên dịch **2 lần** cho mỗi file (lần 1 tạo `.aux`, lần 2 mới ra đúng mục lục/tham chiếu).
6. Trong shell mới, `xelatex`/`pdftoppm`/`pdftotext` có thể **không có sẵn trong PATH** dù đã
   cài — kiểm tra bằng `which xelatex`; nếu không thấy, tìm bằng
   `find / -name xelatex -type f 2>/dev/null` (thường ở
   `/usr/local/texlive/<năm>/bin/universal-darwin/`) rồi
   `export PATH="<đường_dẫn_đó>:$PATH"` ở đầu mỗi lệnh Bash cần dùng.

## VII. Kiểm tra trước khi báo hoàn thành

- [ ] Đã đọc 100% số trang của mọi PDF nguồn (không chỉ đọc mẫu vài trang rồi suy diễn phần
      còn lại)?
- [ ] Danh sách bài tập trong Exercises_Solutions khớp đủ với bước II.4 (đếm lại số bài)?
- [ ] Không còn bài nào dùng kiểu "xem mục X ở trên" thay lời giải?
- [ ] Mọi số liệu trong lời giải khớp đúng số liệu gốc trong slide (không làm tròn/đổi đơn vị
      nhầm)?
- [ ] Đã tự tính lại (không chỉ chép) từng bước lời giải để bắt lỗi số học trước khi đưa vào tài
      liệu?
- [ ] `grep -n '\\begin{\(formulabox\|examplebox\|notebox\)}\[[^]]*,'` trên mọi file — rỗng?
- [ ] Mỗi file `.tex` đã `xelatex` 2 lần, không còn dòng `^!` (lỗi) và không còn "Missing
      character" trong log?
- [ ] Nếu có yêu cầu trích dẫn số trang: đã lấy bằng pdfplumber/pdftotext, không đoán?
- [ ] Cấu trúc thư mục và tên file khớp quy ước ở mục III?
