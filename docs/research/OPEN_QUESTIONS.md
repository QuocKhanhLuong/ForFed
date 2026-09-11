# Câu hỏi nghiên cứu đang mở

Cập nhật: **2026-09-11**. Nguồn xuất phát: [ghi chú SARIF v1](../papers/SARIF_v1.md), đặc biệt Eq.1–14, Tables 3/8 và failure cases ở §5 Q2 của paper. Định hướng mới từ giảng viên được ghi riêng tại [DIFFUSION_DIRECTION.md](DIFFUSION_DIRECTION.md).

Đây là danh sách điều cần kiểm chứng, **không phải kết quả thí nghiệm hoặc kiến trúc đã chọn**. SARIF vẫn là paper xuất phát để hiểu forensic localization, nhưng diffusion hiện là trục kỹ thuật cần ưu tiên khảo sát trước khi chốt bài toán cuối.

## Q0 — Baseline SARIF chính xác là phiên bản nào?

**Trạng thái:** chưa giải quyết.

Cần đối chiếu PDF v1 với một commit cụ thể của code tác giả: projection/cosine, attention, fusion prompt, số stage, LoRA placement, tham số frozen/trainable, mask threshold, gradient/detach và loss. Cần fold manifests, authentic sampling, chọn checkpoint, metric aggregation và điều kiện đo chi phí.

**Bằng chứng để đóng câu hỏi:** bảng PDF → code có SHA/đường dẫn/dòng; cấu hình có thể chạy lại; giải thích các bất nhất được ghi ở §10 của bản đọc. Không mặc định `main` hiện tại tương đương v1.

## Q1 — Frozen reference đóng góp điều gì ngoài capacity và feature đa tầng?

**Trạng thái:** câu hỏi cơ chế; chưa chạy đối chứng.

Table 3 cho thấy FSIE hữu ích nhưng chưa tách lợi ích của nhánh tham chiếu khỏi số tham số, attention và multi-level feature. Cần đối chứng giữ ngân sách, decoder, supervision và cue schedule gần tương đương rồi thay/bỏ nguồn tham chiếu.

**Điều có thể bác bỏ cách diễn giải mạnh:** single-encoder multilevel control hoặc reference không chứa thông tin đúng vẫn giữ toàn bộ lợi ích. Khi đó chưa đủ cơ sở nói cải thiện đến từ tín hiệu khác biệt adapted/frozen.

**Điều chưa được phép kết luận:** một đối chứng không chênh lệch có ý nghĩa không tự chứng minh hai cơ chế tương đương nếu thí nghiệm thiếu lực thống kê.

## Q2 — Tín hiệu chỉnh sửa có bị trộn với miền/thiết bị/tiền xử lý không?

**Trạng thái:** giả thuyết do chủ dự án yêu cầu kiểm tra, chưa xác nhận.

> Khác biệt giữa encoder thích nghi và encoder gốc có thể trộn tín hiệu can thiệp với khác biệt thiết bị, tiền xử lý hoặc miền dữ liệu.

Không suy ra tự động `domain shift → encoder difference lớn → false positive`. Cả hai encoder cùng nhận ảnh miền mới và có thể thay đổi cùng hướng hoặc khác hướng.

Cần tách ít nhất hai yếu tố: trạng thái can thiệp theo nhãn forensic và thay đổi miền/tiền xử lý hợp lệ. Câu hỏi đo lường là cue/đầu ra theo can thiệp, theo miền hay tương tác của chúng. Không mặc định có dữ liệu thiết bị hoặc nhiều bệnh viện.

**Bằng chứng làm yếu/bác bỏ dự đoán cụ thể:** với nguồn, nhãn và độ mạnh biến đổi đã kiểm soát, thay đổi miền hợp lệ không tăng đáp ứng sai như dự đoán, trong khi tín hiệu vẫn bám vùng can thiệp. Kết luận chỉ trong phạm vi miền đã thử; không tuyên bố bất biến phổ quát.

Chưa có lý do để xây module disentanglement trước khi đo hiện tượng.

## Q3 — Feedback sửa sai hay củng cố sai?

**Trạng thái:** paper có bằng chứng mean improvement và ví dụ khuếch đại lỗi, chưa có phân bố theo ảnh đầy đủ.

Cần đo theo từng ảnh và từng stage:

$$
\Delta_i^t=\operatorname{DSC}(M_i^t,Y_i)-\operatorname{DSC}(M_i^{t-1},Y_i).
$$

Bao nhiêu ảnh có delta âm? Initial mask sai vùng có được kéo về vùng đúng không? Giữ cue, số lượt decoder và supervision như nhau nhưng không dùng previous prediction thì phần cải thiện còn bao nhiêu?

**Đối chứng có thể làm yếu claim về feedback:** lợi ích tương đương khi bỏ đường mask hoặc chỉ lặp decoding với cùng ngân sách. Mean tăng chưa đủ gọi hệ thống là audit; muốn audit phải có tiêu chuẩn xác minh transition riêng, nhưng chưa chọn thiết kế đó.

## Q4 — Mô hình nhận biết thao tác hay hình dạng gắn với nhãn?

**Trạng thái:** câu hỏi đặc hiệu, chưa kiểm nghiệm.

Cần trường hợp nội dung/hình dạng tương tự nhưng khác trạng thái can thiệp, và biến đổi hợp lệ không thuộc positive class. Kiểm tra việc cue bám object boundary thay vì vùng thao tác. Nếu thay semantic content làm đổi prediction trong khi trạng thái can thiệp được kiểm soát, phải xem lại diễn giải forgery-specific.

Không gọi tổn thương thật là giả chỉ vì bất thường; không gắn nhãn mọi synthetic image hoặc artifact là tampered khi chưa có threat model và annotation protocol.

## Q5 — Khả năng không báo giả có giữ ngoài miền train không?

**Trạng thái:** cần authentic ngoài miền; Table 6 hiện chỉ hỗ trợ pixel FPR trên CASIAv2 authentic.

Cần tách pixel localization khỏi image-level decision; chốt threshold mà không dùng test GT. Kiểm tra đáp ứng trên authentic ngoài miền, xử lý mask rỗng và calibration nếu có. Không quy FPR 5.1 trong paper thành tỷ lệ ảnh báo sai hoặc accuracy xác thực.

## Q6 — Diffusion sẽ đóng vai trò nào trong bài toán forensic?

**Trạng thái:** ưu tiên cao; chưa chốt.

Ba cách hiểu phải được tách riêng:

- **D1 — Forensics of diffusion:** phát hiện/định vị ảnh hoặc vùng được tạo/chỉnh sửa bằng diffusion models.
- **D2 — Diffusion for forensics:** dùng denoising/reconstruction/score/latent representations của diffusion làm forensic evidence.
- **D3 — Federated diffusion / federated forensic learning:** đưa D1/D2 vào multi-client/non-IID hoặc nghiên cứu diffusion trong FL.

Trước mắt cần survey D1 và D2. D3 chỉ được đưa vào sau khi có threat model và dữ liệu/nhu cầu biện minh. Không xem `diffusion + FedAvg` là contribution tự thân.

**Bằng chứng cần để chốt hướng:** paper gần nhất, benchmark phù hợp, định nghĩa positive class, nhãn có sẵn, generalization gap rõ, và một pilot có thể bác bỏ giả thuyết.

## Q7 — Cue liên quan diffusion có thực sự đặc hiệu cho manipulation không?

**Trạng thái:** câu hỏi mới cần kiểm tra sau targeted survey.

Nếu dùng reconstruction error, denoising trajectory, predicted noise, latent feature hoặc model fingerprint, cần kiểm tra cue có phản ứng chủ yếu với **thao tác diffusion** hay chỉ với generator family, compression, resolution, semantic content hoặc preprocessing.

Pilot tối thiểu nên có các factor được tách:

1. pristine vs manipulated;
2. seen vs unseen diffusion generator/editor;
3. benign post-processing như JPEG/resize/screenshot;
4. nếu có thể, cùng semantic content nhưng khác provenance/manipulation history.

Một cue vẫn phân biệt generator nhưng thất bại trên unseen editor hoặc benign transform không được gọi là manipulation-specific.

## Q8 — Dữ liệu và nhu cầu nào đủ biện minh hướng medical/federated?

**Trạng thái:** để sau khi D1/D2 đủ rõ.

Ảnh y tế và federated/non-IID vẫn là các hướng mở rộng, không phải mặc định. Chưa chọn modality, cơ quan, bệnh, manipulation generator, site split hoặc thuật toán FL. Unlearning là nhánh phụ chưa được chọn.

Trước khi mở rộng, cần xác định đối tượng phát hiện, nhãn, dữ liệu authentic/tampered, quyền truy cập của từng bên, đối chứng công bằng và điều kiện có thể bác bỏ giả thuyết. Không bắt đầu bằng thay SAM thành MedSAM, thay backbone thành diffusion hoặc thêm FedAvg rồi coi đó là đóng góp.

## Điều kiện chuyển bước

1. Khóa nguồn và baseline đủ để phân biệt lỗi tái lập với giới hạn phương pháp.
2. Hoàn thành targeted survey D1/D2 để biết bài toán diffusion forensic gần nhất và benchmark thực sự tồn tại.
3. Chọn một câu hỏi cơ chế có bằng chứng ban đầu hoặc phép kiểm tra khả thi; chưa cần kiến trúc mới.
4. Chỉ mở medical/federated khi phạm vi được biện minh bởi dữ liệu và threat model; ghi kết quả mới vào docs và nhật ký, giữ giả thuyết tách khỏi kết luận.
