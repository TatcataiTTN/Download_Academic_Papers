---
name: qml-hazard-research
description: "Use when starting or extending research for a Quantum Machine Learning (QRC/VQC/QNN) project applied to a natural-hazard forecasting problem (flood, landslide, drought, salinity intrusion, wildfire, etc.). Drives literature review of classical/traditional baselines before quantum methods, sourcing of vetted datasets, and a verification protocol that prevents citing wrong or fabricated papers."
---

# QML Hazard Research Skill

## Khi nào dùng skill này
Dùng khi bắt đầu (hoặc mở rộng) nghiên cứu cho một bài toán QML áp dụng cho hiểm hoạ tự
nhiên (flood, landslide, drought, salinity intrusion, wildfire, v.v.). Mục tiêu: đảm bảo có nền
tảng traditional ML/DL/Physics-Informed vững trước khi build phần quantum, tránh 2 lỗi đã từng
gặp trong thực tế:
1. Nhảy thẳng vào quantum method mà chưa có baseline classical để so sánh.
2. Trích dẫn sai bài báo (đã từng tải nhầm 4/15 bài vì arXiv redirect sai, và từng có agent
   nhầm case-study Nepal thành Vietnam do tin 1 kết quả search duy nhất) hoặc bịa DOI/quartile.

## Nguyên tắc cốt lõi: Traditional trước, Quantum sau
Với MỌI hiểm hoạ mới, luôn review theo đúng 5 hạng mục sau, theo thứ tự — không được bỏ
qua hạng mục 1–2 để nhảy thẳng đến QML:

1. **Classical ML** (Random Forest, XGBoost, SVM, ANFIS, Logistic Regression, LightGBM...) —
   baseline dễ reimplement, thường dùng cho susceptibility mapping (bài toán không gian/
   classification).
2. **Deep Learning / Neural Network** (CNN/U-Net cho segmentation ảnh vệ tinh, LSTM/TCN/
   ConvLSTM cho time-series/spatio-temporal, vd. InSAR displacement) — baseline mạnh hơn,
   cần nhiều data hơn.
3. **Physics-Informed models** (PINN kết hợp phương trình vật lý — shallow water equations cho
   flood, infinite-slope/Newmark displacement/Bishop's effective-stress cho landslide — với
   neural network). Đây là sub-field non trẻ; nếu tìm không ra nhiều bài thì báo cáo trung thực,
   không nhồi bài yếu hoặc bài conference/proceedings (không có quartile) vào cho đủ số lượng.
4. **Remote-sensing indices / geomorphometric factors** cơ bản — đây là input feature, không
   phải model, nhưng bắt buộc phải biết trước khi thiết kế pipeline:
   - Flood: NDWI, MNDWI, AWEI, SAR backscatter change-detection (Otsu threshold)
   - Landslide: NDVI (mất thảm thực vật), TWI, Slope/Aspect/Curvature, SPI (Stream Power
     Index), khoảng cách tới fault/road/river, ngưỡng Intensity-Duration mưa (Caine 1980),
     InSAR displacement/coherence
5. **Dataset catalog** (3–10 bộ, ưu tiên chất lượng > số lượng) — phải verify thực sự tồn tại và
   truy cập được, không chỉ dựa vào tên nghe quen (vd. một dataset tên "ETH Zurich flood
   dataset" từng được đề xuất nhưng search kỹ thì không tồn tại — phải loại bỏ ngay).

Chỉ sau khi có đủ 5 mục trên mới đến bước thiết kế QRC/VQC/QNN, và bước thiết kế phải nói
rõ **quantum method nào phù hợp với sub-bài-toán nào** (xem phần "Đánh giá độ phù hợp
quantum" bên dưới).

## Verification protocol — bắt buộc trước khi trích dẫn bất kỳ bài báo nào
- Không bao giờ tin 1 kết quả search duy nhất. Luôn cross-check bằng ít nhất 2 nguồn độc lập
  (trang publisher, DOI resolver, ResearchGate/Scimago mirror, hoặc bản preprint song song trên
  arXiv) để xác nhận title/author/journal/year/**case-study location** khớp nhau — một agent
  từng bị 1 tóm tắt WebFetch báo sai cả địa điểm nghiên cứu (Nepal → tưởng Vietnam) lẫn tên
  tạp chí; chỉ khi kéo full-text trực tiếp mới phát hiện ra và sửa lại.
- Nếu không verify được quartile (Q1/Q2 theo Scimago/JCR), ghi rõ "quartile unverified" hoặc
  "category-dependent" thay vì đoán một con số duy nhất. Nhiều tạp chí (vd. Journal of Water
  and Climate Change, International Journal of Remote Sensing) có quartile khác nhau tuỳ
  category Scimago — phải nêu rõ thay vì làm tròn. Nếu trang Scimago chặn fetch trực tiếp
  (403), dùng nguồn phụ (Resurchify, mirror khác) và ghi rõ đây là gián tiếp, chưa confirm trực
  tiếp.
- Phân biệt rõ **journal article** (có quartile) với **conference/book chapter/preprint** (không
  có quartile) — không được gán quartile bịa cho loại thứ hai.
- Với dataset: xác minh link còn hoạt động và mô tả nội dung khớp thực tế (không suy diễn từ
  tên gọi). Nếu dataset có vẻ chỉ phủ 1 khu vực địa lý (vd. EGMS chỉ có Châu Âu, COMET-LiCS
  ưu tiên vùng Alpine-Himalaya/Nhật/Đông Phi), phải nói rõ có/không phủ Việt Nam thay vì mặc
  định là có.
- Cuối báo cáo luôn có mục riêng "Flags / không verify được" liệt kê rõ ràng, kể cả những chỗ
  agent tự phát hiện và tự sửa lỗi giữa chừng — không âm thầm sửa mà không báo.
- Khi giao việc research cho sub-agent, luôn nhắc lại protocol này trong prompt (đừng giả định
  agent tự nhớ).

## Đánh giá độ phù hợp quantum (áp dụng sau khi có traditional baseline)
Dùng heuristic sau để quyết định QRC (Quantum Reservoir Computing) hay QNN/hybrid CNN-QNN
phù hợp hơn cho từng sub-bài-toán trong cùng 1 hiểm hoạ:

| Dạng bài toán | Ví dụ | Method quantum phù hợp | Lý do |
|---|---|---|---|
| Time-series forecasting, chuỗi có memory ngắn hạn, phi tuyến/chaotic | Rainfall→discharge→flood level; InSAR displacement chuỗi thời gian cho landslide early warning | **QRC (fixed VQC reservoir + classical readout)** | QRC không cần gradient training (tránh vanishing gradient của LSTM), có fading memory tự nhiên — khớp đặc tính chuỗi khí hậu/dịch chuyển đất có lag dài |
| Spatial classification / susceptibility mapping | Landslide susceptibility map từ slope/geology/NDVI; flood extent segmentation từ ảnh vệ tinh | **QNN/hybrid CNN-QNN** (không phải QRC) | Đây là bài toán không gian tĩnh, không có cấu trúc chuỗi thời gian để reservoir khai thác |

Ghi rõ trong mọi literature review: sub-bài-toán nào của hiểm hoạ này là time-series (ưu tiên
QRC) và sub-bài-toán nào là spatial classification (không dùng QRC, cân nhắc QNN khác).

## Cấu trúc output & quy ước thư mục
Với mỗi hiểm hoạ, tạo/cập nhật đúng các file sau (theo cấu trúc `Project_QML_<Hazard>/`):
- `Literature/Literature_Review_Traditional.md` — 5 hạng mục trên, mỗi bài báo dùng template:
  `Tác giả (năm). "Tên bài." Tạp chí, vol(issue), trang. DOI: ... | Quartile: ... (nguồn) | Method: ... | Relevance: 1-2 câu.`
- `Datasets/Dataset_Catalog.md` — mỗi dataset: tên, nguồn/URL, nội dung, quy mô, lý do hữu ích,
  caveat truy cập/phạm vi địa lý nếu có.
- Tài nguyên method-chung không đặc thù hiểm hoạ (QRC/VQC papers, tool scripts) → để ở
  `Shared_Resources/`, không lặp lại trong từng project riêng.
- Cuối mỗi review, thêm mục "Việc tiếp theo gợi ý" nối vào README.md của project đó.

## Checklist nhanh trước khi báo cáo xong
- [ ] Đủ 5 hạng mục (Classical ML, DL/NN, Physics-Informed, Indices/Factors, Datasets)?
- [ ] Mỗi paper có DOI/link thật, verify ≥2 nguồn (kể cả địa điểm case-study, không chỉ tên bài)?
- [ ] Quartile ghi rõ nguồn, không đoán, không làm tròn khi category-dependent?
- [ ] Dataset đã verify tồn tại + truy cập được + phạm vi địa lý rõ ràng, không suy diễn từ tên?
- [ ] Có mục "Flags / không verify được" trung thực ở cuối, kể cả lỗi tự phát hiện/tự sửa?
- [ ] Đã phân loại sub-bài-toán time-series (QRC) vs spatial (QNN khác)?
- [ ] File output đặt đúng vị trí theo cấu trúc thư mục project?
