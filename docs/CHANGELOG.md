# Nhật ký tri thức ForFed

Nhật ký ghi theo sự kiện. Khi sửa nhận định cũ, thêm mục giải thích nguồn và lý do; không xóa dấu vết thay đổi. Ngày dùng định dạng YYYY-MM-DD. Kết quả paper, suy luận và kết quả chạy lại phải được phân biệt.

## 2026-09-11 — Giảng viên yêu cầu hướng nghiên cứu làm về diffusion

### Quyết định / ràng buộc mới

Giảng viên hướng dẫn muốn dự án **làm về diffusion**. Đây được ghi nhận là thay đổi quan trọng của source of truth.

Chưa có đủ thông tin để đồng nhất yêu cầu này với một kiến trúc cụ thể. Bộ tài liệu tách ba cách hiểu:

- **D1 — Forensics of diffusion:** phát hiện hoặc định vị vùng được tạo/chỉnh sửa bằng diffusion models.
- **D2 — Diffusion for forensics:** dùng diffusion process/representation làm nguồn forensic evidence.
- **D3 — Federated diffusion / federated forensic learning:** đưa D1/D2 vào multi-client/non-IID hoặc nghiên cứu diffusion trong FL.

### Điều chỉnh ưu tiên

SARIF vẫn là paper xuất phát để hiểu image forgery localization và các câu hỏi về feature specificity/refinement, nhưng không còn mặc định kiến trúc cuối phải là SAM-based.

Ưu tiên khảo sát tiếp theo là **D1 và D2**. Federated learning vẫn là hướng quan tâm nhưng chỉ ghép sau khi có threat model và bằng chứng về vấn đề; `diffusion + FedAvg` không được coi là novelty tự thân. Federated unlearning vẫn là nhánh phụ chưa chốt.

Medical imaging tiếp tục là khả năng mở rộng, không phải mặc định và không tự gắn với ultrasound. Không chuyển forensic thành segmentation cơ quan/tổn thương.

### Tài liệu cập nhật

- [DIFFUSION_DIRECTION](research/DIFFUSION_DIRECTION.md): giải thích ba vai trò của diffusion, claim chưa được phép kết luận và câu hỏi survey tiếp theo.
- [README](../README.md): cập nhật trạng thái và phạm vi hiện tại.
- [OPEN_QUESTIONS](research/OPEN_QUESTIONS.md): thêm Q6–Q8 về vai trò diffusion, specificity của cue diffusion và điều kiện để mở medical/federated.

### Chưa quyết định

Chưa chốt detection hay localization; chưa chốt text-to-image, inpainting hay local editing; chưa chốt generator family, dataset, diffusion backbone, medical modality, federated algorithm hoặc unlearning. Chưa survey targeted literature trong lần cập nhật source-of-truth này.

### Bước tiếp theo

Thực hiện targeted survey về **diffusion image forensics**, ưu tiên paper gốc/benchmark và phân biệt image-level detection với pixel-level localization; sau đó lập bảng problem → data/label → evidence → limitation → falsifiable pilot trước khi thiết kế kiến trúc mới.

---

## 2026-09-07 — Chốt repo chính thức và lưu bản đọc SARIF

### Quyết định của chủ dự án

`QuocKhanhLuong/ForFed` là repo chính thức của dự án Forensic + Federated Learning. Mọi lần survey/cập nhật kiến thức liên quan phải được lưu docs và commit/push tại đây, không chỉ nằm trong chat.

### Tài liệu được đưa vào repo

- [README](../README.md): trạng thái, phạm vi và mục lục tri thức.
- [AGENTS](../AGENTS.md): quy trình cập nhật bắt buộc, nguồn/phiên bản, phân loại bằng chứng và xác minh remote.
- [SARIF v1](papers/SARIF_v1.md): lưu bản đọc sâu Giai đoạn 1 từ file đã truy xuất trong cuộc trao đổi; cơ chế dual encoder, công thức FSIE, FGMD, loss, dữ liệu, protocol, baseline, ablation, failure cases và bảng claim → evidence → limitation.
- [Câu hỏi mở](research/OPEN_QUESTIONS.md): việc cần xác minh về baseline, độ đặc hiệu feature, feedback và authentic ngoài miền; điều kiện trước khi mở sang medical/FL.

### Những phân biệt được lưu

FSIE không được mô tả chỉ bằng F−O hoặc pixel residual forged−pristine; previous prediction khác GT; refinement không đồng nghĩa independent audit; tên forgery-specific không phải bằng chứng đã tách nhân quả; nhãn forensic khác nhãn tổn thương.

### Trạng thái bằng chứng và giới hạn

Nguồn chính là file `2606.21108v1.pdf` đã đọc, 25 trang. Lần lưu này không xác minh lại byte-for-byte với bản công khai và chưa có checksum PDF. Không sao chép PDF vào repo.

Các nhận xét về code tác giả trong trao đổi trước được giữ ở **danh sách cần kiểm chứng**, vì chưa lưu commit SHA và trích đoạn source đi kèm trong repo. Chúng không thay thế công thức PDF hoặc được coi là kết quả code audit đã xác minh.

Đã ghi các điểm cần kiểm tra: indexing initial/refinement, trạng thái trainable, công thức Dice, split/sampling, threshold/aggregation và chênh lệch số tổng hợp giữa bảng. Đây là câu hỏi tái lập; chưa kết luận implementation sai hoặc có leakage.

### Chưa thực hiện / chưa quyết định

Chưa chạy training, inference, benchmark hoặc thực nghiệm kiểm chứng giả thuyết. Chưa chốt modality/dataset, kiến trúc mới, thiết lập federated hoặc unlearning. Chưa mở survey FL/unlearning trong đợt lưu này.

### Hành động tiếp theo

Đối chiếu một source snapshot SARIF đã khóa SHA với PDF v1 và làm rõ protocol trước khi coi SARIF là baseline tái lập. Các câu hỏi cơ chế được theo dõi trong OPEN_QUESTIONS; không tự chuyển giả thuyết về domain mixing thành kết luận.
