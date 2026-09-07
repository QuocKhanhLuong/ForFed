# ForFed

Repo chính thức của dự án **Forensic + Federated Learning**.

## Trạng thái — 2026-09-07

Dự án đang ở giai đoạn đọc sâu paper xuất phát **SARIF: Segment Anything for Robust Image Forensics**, từ file người dùng cung cấp `2606.21108v1.pdf`. Đã lập ghi chú cơ chế, bằng chứng, giới hạn và câu hỏi kiểm chứng. **Chưa tái lập thực nghiệm, chưa chốt kiến trúc mở rộng, modality, dataset hoặc thiết lập federated.**

Quyết định của chủ dự án: **mỗi lần survey hoặc cập nhật kiến thức liên quan đến ForFed, phải cập nhật tài liệu tại repo này và commit/push thực tế**, không để kết quả chỉ nằm trong chat.

## Đọc từ đây

| Tài liệu | Nội dung |
|---|---|
| [Đọc sâu SARIF v1](docs/papers/SARIF_v1.md) | Bài toán; dual encoder; FSIE; FGMD; supervision; protocol; baseline/ablation; claim → evidence → limitation; điểm chưa đủ thông tin để tái lập. |
| [Câu hỏi nghiên cứu đang mở](docs/research/OPEN_QUESTIONS.md) | Độ đặc hiệu của feature, hiệu quả và rủi ro feedback, protocol và điều kiện trước khi xét medical/FL. |
| [Nhật ký tri thức](docs/CHANGELOG.md) | Các lần cập nhật, quyết định, sửa nhận định và phần chưa hoàn thành. |
| [Quy ước cho agent](AGENTS.md) | Đọc nguồn, phân loại mức bằng chứng, cập nhật docs, bảo toàn lịch sử và xác minh commit. |

## Phạm vi hiện tại

- Hiểu và kiểm chứng **forgery localization**, không chuyển ngầm thành phân đoạn cơ quan hoặc tổn thương.
- Ảnh y tế và federated/non-IID là các hướng mở rộng cần được biện minh riêng. Không mặc định dùng siêu âm, có dữ liệu nhiều bệnh viện, metadata thiết bị hoặc dữ liệu longitudinal.
- Federated unlearning là nhánh cân nhắc phụ, chưa được chọn.
- Không coi mọi ảnh tổng hợp, artifact hoặc vùng bất thường là giả mạo. Định nghĩa positive class và quy ước mask phải được xác lập trước thực nghiệm.
- Không xem việc thay SAM bằng MedSAM hoặc thêm FedAvg là đủ novelty. Không thiết kế nhiều module trước khi có bằng chứng về vấn đề.

## Quy trình lưu tri thức

Mỗi lượt làm việc có nội dung nghiên cứu mới cần: đọc tài liệu hiện có → kiểm tra đúng nguồn/phiên bản → cập nhật ghi chú có dẫn nguồn và mức bằng chứng → cập nhật câu hỏi/nhật ký → commit/push → đọc lại trạng thái remote và báo đường dẫn cùng SHA. Khi bị chặn quyền hoặc xung đột, ghi rõ phần đã làm và phần chưa đẩy; không báo hoàn thành khi mới có bản nháp.

Tài liệu viết bằng tiếng Việt; giữ nguyên tên paper, thuật ngữ và công thức khi cần chính xác. Repo lưu bản tổng hợp nghiên cứu, không mặc định sao chép toàn văn PDF, dữ liệu cá nhân, dữ liệu y tế hoặc thông tin xác thực vào Git.
