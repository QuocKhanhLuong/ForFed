# Nhật ký tri thức ForFed

Nhật ký ghi theo sự kiện. Khi sửa nhận định cũ, thêm mục giải thích nguồn và lý do; không xóa dấu vết thay đổi. Ngày dùng định dạng YYYY-MM-DD. Kết quả paper, suy luận và kết quả chạy lại phải được phân biệt.

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
