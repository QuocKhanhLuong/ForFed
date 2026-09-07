# Đọc sâu SARIF v1 — Giai đoạn 1

Ngày ghi nhận: **2026-09-07**. Trạng thái: **đã đọc PDF; chưa tái lập thực nghiệm; chưa chốt kiến trúc mở rộng**.

## 0. Nguồn và phạm vi xác minh

**[S1] Nguồn chính:** file người dùng đã tải lên `2606.21108v1.pdf`, 25 trang, gồm phần chính và supplementary material. Nội dung đã truy xuất trong buổi đọc ghi tên *SARIF: Segment Anything for Robust Image Forensics*, tác giả Dong-Hyun Moon, Ju-Hyeon Nam và Sang-Chul Lee. Trang đầu ghi `arXiv:2606.21108v1`, ngày 19/6/2026.

Các trỏ dẫn công khai theo mã phiên bản trong PDF: [trang v1](https://arxiv.org/abs/2606.21108v1), [PDF v1](https://arxiv.org/pdf/2606.21108v1). Chúng là địa chỉ tham chiếu; lần lưu docs này dựa trên nội dung file đã đọc trong cuộc trao đổi, không thực hiện lại xác minh byte-for-byte với bản công khai. Chưa có SHA-256 của PDF để lưu trong repo. Không đính kèm hoặc tái phân phối toàn văn PDF.

Các dẫn `[S1, §…, Eq.…, Table…]` dưới đây chỉ đúng vị trí trong file nguồn, không dùng ID trích dẫn nội bộ của chat làm tài liệu tham khảo công khai. Khi ghi số trang supplementary, dùng thứ tự trang PDF: trang 21–25, dù số trang in trong phụ lục bắt đầu lại từ 1.

**Về mã nguồn:** trao đổi trước có nhắc `https://github.com/Inha-CVAI/SARIF_ECCV2026/blob/main/model_list/SARIF.py`. Chưa lưu snapshot và commit SHA có thể kiểm chứng trong bản ghi này. Các nhận xét về code không được dùng để âm thầm thay thế công thức PDF; xem §10.5 để biết những điểm cần kiểm tra lại.

**Mức bằng chứng:** “paper báo cáo” là nội dung S1; “suy luận” là lập luận từ cấu trúc; “giả thuyết” là điều cần thử; “chưa xác minh” là thông tin chưa có nguồn/phiên bản đủ chặt. Chưa chạy training, inference, đánh giá hay kiểm tra leakage của implementation.

## 1. Bài toán, đầu vào và đầu ra

SARIF giải **image forgery localization có giám sát**: từ một ảnh cần kiểm tra, dự đoán bản đồ theo pixel rồi tạo mask của vùng chỉnh sửa. Luồng dự đoán được mô tả không cần ảnh pristine tương ứng trước chỉnh sửa, point, box hay mask thủ công. [S1, §3.1, §3.3, Eq.9–14]

Nhãn positive là vùng can thiệp theo annotation của dataset, không mặc định là vật thể, cơ quan, tổn thương hay mọi vùng bất thường. Dữ liệu chính gồm copy-move và splicing; đánh giá bổ sung có CocoGlide và TGIF cho những kiểu chỉnh sửa liên quan đến inpainting. [S1, §4.1, §5 Q4, Tables 4–5]

Ví dụ phân biệt về khái niệm: một tổn thương có thật và một tổn thương được chèn có thể có hình dạng tương tự, nhưng nhãn forensic phải dựa vào trạng thái can thiệp chứ không chỉ vào hình dạng. Đây là ví dụ diễn giải, không phải thí nghiệm của SARIF.

**Điểm chưa rõ:** với copy-move, cần kiểm tra annotation từng dataset đánh dấu vùng nguồn, vùng đích hay cả hai. §7 mô tả dữ liệu nhưng chưa giải thích đủ quy ước mask cho mọi trường hợp.

### Những dấu vết được kỳ vọng khai thác

Introduction nhắc illumination inconsistency, JPEG artifacts, sensor pattern noise và CFA. Tuy nhiên, FSIE không định nghĩa các bộ đo riêng cho từng dấu vết vật lý này. Feature được học thông qua thích nghi và supervision localization. Không có ràng buộc được trình bày buộc từng channel phải tương ứng với một loại artifact cụ thể. [S1, §1, §3.2–3.3]

Cách hiểu thận trọng: tác giả kỳ vọng thích nghi SAM làm xuất hiện các biến đổi biểu diễn hữu ích cho định vị can thiệp; FSIE khai thác hai biểu diễn và quan hệ giữa chúng. Không đồng nhất kỳ vọng này với bằng chứng đã tách được tín hiệu nhân quả của chỉnh sửa.

## 2. Hai encoder: cùng ảnh, khác biểu diễn

Dùng ký hiệu diễn giải:

$$
\{O^\ell,E_{\mathrm{orig}}\}=E_{\theta_0}(x),
\qquad
\{F^\ell,E_{\mathrm{fine}}\}=E_{\theta_0,\Delta\theta_{\mathrm{LoRA}}}(x).
$$

Cả hai nhánh nhận **cùng ảnh x**. Nhánh original giữ trọng số SAM gốc; nhánh adapted thêm LoRA. “Original encoder” không có nghĩa nó nhận ảnh nguyên bản trước chỉnh sửa. Nó là mốc biểu diễn, không phải một bộ xác thực độc lập. [S1, §3.1, Fig.2]

Bài dùng SAM ViT-L, LoRA rank 32; trích feature tại các block có chỉ số `{5,11,17,23}` và final embedding. Chỉ số này nên được giữ đúng như paper, không tự đổi quy ước đếm block khi chưa đối chiếu code. [S1, §4.2]

| Thành phần | Điều PDF nói / điều còn thiếu |
|---|---|
| Original SAM encoder | Frozen. |
| Backbone SAM ở nhánh adapted | Frozen; thích nghi thông qua LoRA. |
| LoRA | Được cập nhật cho forgery localization. |
| Mask decoder | Được cập nhật và dùng chung trọng số qua các vòng. |
| FSIE | Có các convolution và attention học được; câu “only adapters and mask decoder” ở §3.1 không liệt kê đầy đủ optimizer membership của mọi module. |
| Prompt encoder | Được tái sử dụng; trạng thái cập nhật cần xác nhận ở implementation đã khóa SHA. |

Nguồn: [S1, §3.1–3.3, Fig.2, Eq.2–8]. Không diễn giải câu mô tả ngắn ở §3.1 thành “FSIE hoàn toàn không có tham số học”.

Ở mỗi refinement stage, image embedding chính đưa vào decoder vẫn là **final embedding E_fine**. Feature tầng đang xét đi theo đường tạo prompt. Mô hình không được mô tả như chạy lại hai encoder sau mỗi mask; stage không phải thời gian hay chuỗi frame. [S1, §3.3, Eq.10–13, Fig.3]

## 3. FSIE: residual thực sự là gì?

### 3.1. Không phải phép trừ feature thuần

Motivation dùng các từ differences, domain gap và residual cues, nhưng Eq.1–8 không định nghĩa `T_fs = F - O`. Triển khai toán học là **cosine similarity + feature của cả hai nhánh + channel attention + convolution có skip connection**. [S1, §3.2]

Cũng không có pixel residual giữa ảnh chỉnh sửa và ảnh pristine. Mô hình chỉ cần một ảnh trong luồng dự đoán được mô tả.

### 3.2. Cosine similarity theo vị trí — Eq.1

Với $O^t,F^t\in\mathbb R^{C\times H\times W}$:

$$
\mathrm{cos}_{ij}^{t}
=\frac{\langle O^t_{:,ij},F^t_{:,ij}\rangle}
{\|O^t_{:,ij}\|_2\,\|F^t_{:,ij}\|_2},
\qquad
\mathrm{cos}^{t}\in\mathbb R^{1\times H\times W}.
$$

Theo thứ tự trong PDF, cosine được tính trước; sau đó hai nhánh được chiếu xuống $C'<C$ bằng convolution $1\times1$. [S1, §3.2, Eq.1, PDF tr.6]

**Suy luận:** cosine so hướng vector channel, không đo mọi khác biệt. Khi $F=aO$ với $a>0$, cosine bằng 1 dù độ lớn thay đổi. Paper không định nghĩa $1-\cos$, và cosine không tự có ý nghĩa như xác suất forgery. Muốn nói cosine thấp là giả phải có kiểm nghiệm riêng.

### 3.3. Feature enhancement — Eq.2–6

PDF dùng lại ký hiệu F/O sau projection. Để rõ kích thước, ở đây gọi feature đã chiếu là $\widetilde F^t,\widetilde O^t$:

$$
C^t=\operatorname{Cat}(\widetilde F^t,\widetilde O^t)
\in\mathbb R^{2C'\times H\times W}.
$$

$$
g_{\max}^t=\operatorname{Conv}_{1\times1}
\left(\operatorname{ReLU}\left(
\operatorname{Conv}_{1\times1}(\operatorname{GMP}(C^t))
\right)\right),
$$

và tương tự với GAP để tạo $g_{\mathrm{avg}}^t$.

$$
G^t=\sigma(g_{\max}^t+g_{\mathrm{avg}}^t),
\qquad
T_{\mathrm{ca}}^t=C^t\odot G^t.
$$

Pooling tạo descriptor channel; attention reweight channel rồi broadcast theo không gian. Hai feature vẫn được giữ trong tensor ghép, không bị thay bằng duy nhất một bản đồ khoảng cách. [S1, §3.2, Eq.2–6]

### 3.4. Fusion và skip — Eq.7–8

$$
Z^t=\operatorname{Cat}(T_{\mathrm{ca}}^t,\mathrm{cos}^t).
$$

Phương trình (8) trong PDF:

$$
T_{\mathrm{fs}}^t=F^t\oplus
\mathrm{CGB}_{1\times1}^{C'}\left(
\mathrm{CGB}_{3\times3}\left(
\mathrm{CGB}_{1\times1}^{r}(Z^t)
\right)\right).
$$

CGB gồm convolution, GELU, batch normalization. Bottleneck giảm channel xuống $r<C'$, xử lý bằng convolution $3\times3$ rồi chiếu lại. Kết quả cộng với feature nhánh adapted. Ký hiệu r của bottleneck không nên tự đồng nhất với LoRA rank. [S1, §3.2, Eq.7–8, PDF tr.6–7]

Kích thước của $F^t$ tại skip phải phù hợp với đầu ra $C'$. PDF tái sử dụng ký hiệu chưa chặt; đây là chỗ cần làm rõ khi implement, không tự coi feature C-channel trước projection có thể cộng trực tiếp.

### 3.5. Những điều công thức cho phép và không bảo đảm

**Suy luận từ cấu trúc:**

- Có skip từ F nên semantic information không bị cưỡng chế loại bỏ.
- Khi hai nhánh giống nhau, FSIE không bắt buộc bằng zero: feature, cosine và skip vẫn tồn tại.
- Loss localization có thể chọn mọi tương quan giúp dự đoán nhãn; không tự bảo đảm bất biến với thiết bị, miền, nội dung hay tiền xử lý.

Không điều nào ở trên là bằng chứng rằng SARIF chắc chắn đang dùng shortcut. Bằng chứng hiện có cho thấy FSIE **hữu ích trong cấu hình được thử**; chưa chứng minh tín hiệu **chỉ đặc hiệu với chỉnh sửa**. [S1, Eq.8, Eq.14, Table 3]

## 4. FGMD: feedback từ prediction, không phải GT

### 4.1. Initial mask và năm refinement stage

Theo mô tả và Fig.3, initial mask sinh từ E_fine mà không có prompt bên ngoài; sau đó năm vòng nhận cue từ bốn block đã chọn và final embedding. Decoder dùng chung trọng số. [S1, §3.3, Fig.3, Table 8]

Ký hiệu diễn giải cho initial mask:

$$
M^0=\sigma\big(D(E_{\mathrm{fine}},\text{no external prompt})\big).
$$

Không có prompt thủ công không nhất thiết có nghĩa tensor prompt bằng zero.

### 4.2. Đường feedback — Eq.10–13

Gọi $\widehat M^{t-1}$ là prediction trước sau threshold:

$$
Q^t=\operatorname{PromptEncoder}(\widehat M^{t-1}),
\qquad
S^t=\operatorname{FSIE}(F^t,O^t),
$$

$$
P^t=Q^t\oplus S^t,
\qquad
M^t=\sigma\big(D(E_{\mathrm{fine}},P^t)\big).
$$

Luồng: **prediction trước → prompt encoder → mask embedding → cộng FSIE cue → decoder**. Paper nói prediction được threshold trước khi làm prompt ở vòng tiếp theo, nhưng đoạn phương pháp chưa nêu giá trị threshold. [S1, §3.3, Eq.10–14]

GT không nằm trên đường prompt được mô tả. Nó là đích loss khi train và đáp án metric khi đánh giá.

### 4.3. Bất nhất chỉ số cần xử lý

Eq.9 ghi previous mask là `None` khi t=1, trong khi mô tả có initial mask và Eq.14 cộng t=0 đến 5. Table 8 có initial mask và năm stage. Cách hiểu “initial + năm refinement” phù hợp với phần mô tả và bảng; vẫn phải xác nhận cách đánh số/khởi tạo trong code. [S1, Eq.9, Eq.14, Table 8]

### 4.4. Evidence theo stage

Table 8 đánh giá mask trung gian của cùng mô hình đã train, không train lại từng stage:

| Dataset | Initial DSC | Final DSC | Chênh lệch tự tính |
|---|---:|---:|---:|
| CASIAv2 | 61.9 | 63.1 | +1.2 điểm |
| DIS25K | 45.8 | 47.8 | +2.0 điểm |
| CASIAv1 | 57.3 | 58.4 | +1.1 điểm |
| IMD2020 | 46.6 | 48.4 | +1.8 điểm |

Nguồn số liệu: [S1, Table 8, PDF tr.24].

Bằng chứng: trung bình tốt hơn trên bốn dataset được báo cáo. Giới hạn: mỗi stage đồng thời đưa thêm cue tầng mới, nên chưa tách lợi ích riêng của mask feedback khỏi cue, số lần decode và deep supervision. Mean tăng không bảo đảm mọi ảnh đều tăng.

### 4.5. Refinement không phải independent audit

Không có module chấm độc lập transition, accept/reject, rollback hoặc bảo đảm cải thiện đơn điệu được mô tả. Tác giả ghi nhận initial mask sai rồi các vòng sau củng cố vùng sai. [S1, §5 Q2, Fig.5]

Vì vậy không diễn giải `mask_{t-1} → mask_t` thành “mô hình tự kiểm chứng được tính đúng đắn”.

**Suy luận về gradient:** nếu threshold được triển khai bằng hard comparison thông thường, không có gradient khả vi xuyên qua phép threshold về prediction trước. Supervision mỗi stage và các đường feature/tham số chung vẫn có thể huấn luyện mô hình. Cần xem code để biết detach hoặc surrogate gradient; không tự khẳng định toàn vòng lặp khả vi.

## 5. Loss và hợp đồng dữ liệu

Paper trình bày:

$$
\mathcal L_{\mathrm{BCE}}=\sum_{t=0}^{5}\operatorname{BCE}(M^t,Y).
$$

Cùng GT manipulation mask Y giám sát initial mask và năm refinement output. Đó là deep supervision. DSC/IoU là metric, không phải loss được viết ở Eq.14. Không có loss riêng trong phương pháp buộc feature là JPEG, thiết bị hay causal tampering evidence. [S1, §3.3, Eq.14]

| Dữ liệu | Train | Luồng dự đoán inference được mô tả |
|---|---|---|
| Ảnh cần kiểm tra | Có | Có |
| GT manipulation mask | Có, cho loss | Không cần để sinh prediction; chỉ cần khi tính metric |
| Prediction trước | Tự sinh | Tự sinh |
| Ảnh pristine tương ứng | Không được định nghĩa là đầu vào bắt buộc | Không cần |
| Point/box/mask thủ công | Không phải yêu cầu của phương pháp | Không cần |

Nguồn: [S1, §3.1, §3.3]. Việc thiết kế không cần GT ở inference chưa phải chứng nhận implementation không leakage. Cần kiểm tra data loader, chọn checkpoint, threshold và đường prediction độc lập với target.

## 6. Protocol đánh giá

### 6.1. Dữ liệu và training

Paper train trên CASIAv2, đánh giá ngoài miền trên CASIAv1, DIS25K, Columbia, IMD2020, CoMoFoD, In the Wild và MISD. Theo tác giả, các benchmark ngoài không dùng train. [S1, §4.1]

Table 5 liệt kê CASIAv2 gồm 5,123 ảnh tampered: 3,274 copy-move, 1,849 splicing. Đây không phải số liệu đủ để suy ra tỷ lệ authentic trong training. [S1, Table 5, §7]

Thiết lập báo cáo: resize 256×256; Adam; LR 1e-4 giảm cosine về 1e-6; 100 epoch; batch 32; ViT-L; LoRA rank 32; mean/std của five-fold cross-validation. [S1, §4.1–4.2]

Các baseline được train trong thiết lập thống nhất của bài. Do đó đây không phải phép so trực tiếp các kết quả tối ưu được từng paper công bố dưới protocol riêng. [S1, §4.1]

### 6.2. Baseline

Tables 1–2 có 14 baseline: UNet, MantraNet, RRUNet, TransForensic, FBINet, MT-SENet, MVSSNet, PIMNet, EITLNet, M2SFormer, SAM, AutoSAM, IMDPrompter và SAFIRE. Phần văn bản nói “thirteen” nhưng danh sách/hàng bảng có 14; cần dùng danh sách thực thay vì số đếm đó. [S1, Tables 1–2, §4.1]

## 7. Kết quả và giới hạn

### 7.1. Không thắng mọi miền

| Dataset | SARIF DSC | Đối chiếu |
|---|---:|---|
| CASIAv2 | 63.1 ± 12.9 | M2SFormer 58.8 ± 12.8 |
| DIS25K | 47.8 ± 2.5 | SAFIRE 50.7 ± 0.9, tốt hơn |
| CASIAv1 | 58.4 ± 1.3 | SAFIRE 61.6 ± 0.9, tốt hơn |
| Columbia | 58.4 ± 3.4 | SAFIRE 53.1 ± 4.9 |
| IMD2020 | 48.4 ± 1.7 | SAFIRE 53.1 ± 0.6, tốt hơn |
| CoMoFoD | 65.4 ± 2.0 | SAFIRE 39.5 ± 1.0 |
| In the Wild | 55.7 ± 3.5 | SAFIRE 63.3 ± 2.2, tốt hơn |
| MISD | 66.2 ± 1.9 | M2SFormer 69.1 ± 0.7, tốt hơn |

Nguồn: [S1, Table 1]. Không suy ra statistical significance chỉ từ chênh lệch mean; chưa có paired test hoặc confidence interval cho chênh lệch được báo cáo ở bảng.

AUC CASIAv2: SARIF 83.3, M2SFormer 83.8. Metric thay đổi có thể đổi thứ hạng. Kết luận phù hợp là mạnh trên tổng thể thiết lập được thử, không phải vượt trội trên mọi miền/metric. [S1, Table 7]

### 7.2. Ablation

| Cấu hình | Seen DSC | Unseen DSC theo cách gộp của bài |
|---|---:|---:|
| SAM | 23.6 | 23.3 |
| + Adapter | 41.2 | 33.0 |
| + Adapter + FSIE | 62.4 | 44.7 |
| + Adapter + FGMD | 62.0 | 42.6 |
| Full SARIF | 63.1 | 57.9 |

Nguồn: [S1, Table 3]. Hai thành phần kết hợp có lợi trong thiết lập báo cáo. Chưa tách riêng ảnh hưởng của frozen reference, multi-level feature, capacity, attention, số vòng decoder và deep supervision. Chưa có đầy đủ đối chứng matched-budget cho single-encoder multilevel feature hoặc cosine/difference variants.

**Không nhầm initial mask của full model với baseline adapter-only:** 61.9 ở Table 8 là output của model đã được train cùng cả hệ thống; 41.2 ở Table 3 thuộc một cấu hình train khác. Không lấy hai số này để gán toàn bộ khác biệt cho refinement lúc inference.

### 7.3. Authentic images

Table 6 báo cáo FPR 5.1 ± 0.8 cho SARIF trên CASIAv2 authentic; FBINet 3.0 ± 0.7. SARIF thấp nhất trong nhóm SAM được so sánh, không thấp nhất toàn bộ. Mọi pixel kích hoạt trên GT toàn zero được tính là false positive. [S1, Table 6, §9]

Đây là FPR pixel-level; không được diễn giải thành “5.1% ảnh thật bị báo giả” hoặc “94.9% accuracy xác thực ảnh”. Cần image score, threshold và protocol riêng để kết luận ở cấp ảnh. Chưa có đủ đánh giá authentic ngoài miền để suy ra hiệu quả triển khai đa nguồn.

### 7.4. Failure cases

Tác giả nêu hai nhóm: initial localization sai và refinement củng cố sai vùng; mask blocky/staircase liên quan tới embedding độ phân giải thấp và upsampling. [S1, §5 Q2, Fig.5]

Failure thứ nhất trực tiếp hạn chế diễn giải feedback như một bộ audit. Không có phân bố tỷ lệ lỗi tăng/giảm theo từng ảnh trong Table 8.

### 7.5. Corruption và benchmark khó

Fig.7 thử Gaussian noise (σ 0.1/0.3/0.5), Gaussian blur (σ 3/5/9), JPEG (q 100/50/10) và bốn tổ hợp; mỗi thành phần trong tổ hợp dùng mức 2. [S1, Fig.7 caption]

Phạm vi này hỗ trợ robustness đối với các corruption đã thử, không đại diện mọi pipeline thiết bị/tiền xử lý hay bảo đảm deployment. Cần điều kiện scale/range, thứ tự transform và implementation để tái lập đúng.

Table 4: CocoGlide DSC 34.0 ± 3.7; TGIF DSC 30.8 ± 2.6. SARIF tốt nhất trong nhóm baseline của bảng nhưng giá trị tuyệt đối còn hạn chế; không nói đã giải quyết localization các chỉnh sửa hiện đại nói chung. [S1, Table 4]

### 7.6. Chi phí

Table 4 báo cáo SARIF: Params 52.62M, FLOPs 998.43G, inference 47.5 ms; SAM: 13.90M, 490.65G, 18.8 ms. Cần làm rõ Params đếm toàn bộ hay chỉ phần trainable, điều kiện phần cứng/batch/đồng bộ và cách đo FLOPs. Không đồng nhất cột Params với footprint tổng của hai nhánh. LoRA ít tham số cập nhật không có nghĩa inference tự động nhẹ. [S1, Tables 3–4, §5 Q4]

## 8. Claim → evidence → limitation

| Claim | Evidence trong S1 | Giới hạn |
|---|---|---|
| Tự động localization không prompt thủ công | §3.3, Eq.9–14 | Vẫn cần GT mask khi train; không phải unsupervised. |
| FSIE khai thác quan hệ adapted/frozen | §3.2, Eq.1–8 | Learned fusion; không phải phép trừ thuần hoặc bộ tách chỉ giữ tampering. |
| FSIE hữu ích cho kết quả | Table 3 | Chưa cô lập reference branch khỏi capacity/multilevel cues. |
| FGMD cải thiện mask | Table 8 | Mean tăng trên bốn tập; chưa bảo đảm từng ảnh, chưa cô lập feedback. |
| Hai module bổ trợ | Table 3 | Bằng chứng cấu hình thực nghiệm, không xác lập nguyên nhân duy nhất. |
| Generalization mạnh | Tables 1–2, 7 | Không thắng mọi miền; chưa có medical, multi-site hoặc non-IID experiment. |
| Authentic response tương đối hạn chế | Table 6, §9 | Pixel FPR trên một nguồn; không phải image-level rejection guarantee. |
| Robust với corruption/chỉnh sửa mới | Fig.7, Table 4 | Tập corruption và baseline hữu hạn; metric trên tập khó vẫn hạn chế. |
| Feedback tự audit được transition | Không có accept/reject; §5 Q2 mô tả khuếch đại sai | Claim này không được hỗ trợ. |

## 9. Những phân biệt bắt buộc giữ

| Dễ nhầm | Cách đọc đúng |
|---|---|
| Feature residual = pixel residual forged − pristine | Hai encoder xử lý cùng ảnh; FSIE dùng cosine và learned fusion. |
| Previous mask = GT mask | Previous mask là prediction; GT dùng trong loss/metric. |
| Refinement = audit độc lập | Không có cơ chế xác minh transition và từ chối cập nhật xấu. |
| Tên forgery-specific = đã chứng minh đặc hiệu | Đó là cách gọi/ý đồ; specificity cần đối chứng riêng. |
| Forgery localization = lesion segmentation | Nhãn theo can thiệp, không theo hình dạng tổn thương. |
| Domain shift chắc chắn gây feature difference rồi false positive | Đây là giả thuyết; phải đo, không suy ra tự động. |
| Initial full-model = adapter-only baseline | Hai mô hình có quá trình train khác nhau. |

## 10. Điểm cần xác minh trước khi tái lập

### 10.1. Dice formula

Eq.15 dùng mẫu số tập hợp $|A\cup B|$ nhưng vế TP/FP/FN là Dice chuẩn. Công thức tập hợp đúng:

$$
\mathrm{DSC}(A,B)=\frac{2|A\cap B|}{|A|+|B|}.
$$

Đây là bất nhất công thức trong PDF, chưa phải bằng chứng code metric sai. Eq.16 trình bày IoU; cách macro/micro averaging và xử lý mask rỗng vẫn cần kiểm tra. [S1, §8, Eq.15–16]

### 10.2. Split và authentic sampling

Phần chính dẫn tới §7 cho “exact split definitions”, nhưng nội dung phụ lục truy xuất chủ yếu là dataset descriptions. Chưa có fold manifests, seed, grouping theo ảnh nguồn, loại trùng, validation selection và tỷ lệ authentic trong train. Không kết luận có leakage; đây là các mục thiếu để kiểm tra leakage. [S1, §4.1, §7]

### 10.3. Số liệu tổng hợp

SAM seen DSC ở Table 1 là 27.1, ở Table 3 là 23.6. Trung bình không trọng số bảy unseen DSC của SARIF ở Table 1 tự tính là khoảng 57.19, khác 57.9 ở Table 3. Cần tác giả hoặc implementation giải thích cấu hình/cách gộp. Không tự coi chúng là cùng statistic. [S1, Tables 1, 3]

### 10.4. Chi tiết kiến trúc/huấn luyện

Cần khóa vị trí LoRA, dimension C/C'/r, số FSIE instance và sharing, freeze mask prompt encoder, BN train/eval behavior, initial-mask indexing, threshold, gradient/detach, loss reduction/weighting, logits/probabilities, chọn checkpoint và output resolution. PDF chưa đủ chi tiết cho mọi mục này. [S1, §3.1–3.3, §4.2]

### 10.5. Các nhận xét code ở trao đổi trước: CHƯA XÁC MINH TRONG REPO

Trao đổi trước đã nêu những khác biệt có thể có: cosine sau projection; tổng hai sigmoid thay vì sigmoid của tổng; prompt fusion concat + conv thay vì cộng; threshold 0.5; BCE-with-logits có pos_weight; prompt encoder frozen; một backbone chạy bật/tắt LoRA; wrapper nhận target để tính loss.

**Không dùng danh sách này như kết luận implementation.** Nó được lưu để không thất lạc câu hỏi, nhưng chưa có commit SHA, trích đoạn code và kiểm tra độc lập đi kèm ở lần lưu này. Việc cần làm là đọc đúng repo tác giả, khóa SHA và đối chiếu từng công thức. Nếu không xác minh được, giữ trạng thái chưa xác minh hoặc sửa nhận xét có nhật ký. Tên repo có chuỗi hội nghị không phải bằng chứng paper được chấp nhận ở hội nghị đó.

## 11. Điểm xuất phát cho bước kế tiếp

Câu hỏi còn mở là: **tín hiệu tham chiếu giữa hai encoder đặc hiệu với can thiệp tới mức nào, và khi tín hiệu sai, refinement sửa hay khuếch đại lỗi?**

Đây chưa phải kết luận cần một kiến trúc mới. Danh sách kiểm tra tiếp theo ở [OPEN_QUESTIONS.md](../research/OPEN_QUESTIONS.md). Không có survey FL/unlearning hoặc quyết định modality mới trong bản ghi Giai đoạn 1 này.
