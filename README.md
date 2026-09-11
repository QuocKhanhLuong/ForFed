# ForFed

Repo chính thức của dự án **Forensic + Federated Learning**, hiện được điều chỉnh để **diffusion trở thành trục kỹ thuật chính cần khảo sát** theo định hướng của giảng viên.

## Trạng thái — 2026-09-11

Dự án đã đọc sâu paper xuất phát **SARIF: Segment Anything for Robust Image Forensics** từ file người dùng cung cấp `2606.21108v1.pdf`, và đã lập ghi chú cơ chế, bằng chứng, giới hạn cùng các câu hỏi kiểm chứng.

**Cập nhật mới:** giảng viên muốn hướng nghiên cứu làm về **diffusion**. Đây là ràng buộc định hướng quan trọng nhưng **chưa chốt bài toán cuối cùng**. Hiện cần tách rõ ba khả năng: (D1) forensics *of* diffusion-generated/edited images, (D2) diffusion *for* forensic detection/localization, và (D3) federated diffusion / federated forensic learning. Ưu tiên trước mắt là làm rõ D1 và D2 trước khi ghép federated.

**Chưa tái lập thực nghiệm, chưa chốt kiến trúc, modality, dataset, generator family hoặc thiết lập federated.**

Quyết định của chủ dự án: **mỗi lần survey hoặc cập nhật kiến thức liên quan đến ForFed, phải cập nhật tài liệu tại repo này và commit/push thực tế**, không để kết quả chỉ nằm trong chat.

## Đọc từ đây

| Tài liệu | Nội dung |
|---|---|
| [Định hướng diffusion](docs/research/DIFFUSION_DIRECTION.md) | Ý nghĩa của yêu cầu “làm về diffusion”; tách D1/D2/D3; phạm vi ưu tiên; claim chưa được phép kết luận; câu hỏi survey tiếp theo. |
| [Đọc sâu SARIF v1](docs/papers/SARIF_v1.md) | Bài toán; dual encoder; FSIE; FGMD; supervision; protocol; baseline/ablation; claim → evidence → limitation; điểm chưa đủ thông tin để tái lập. |
| [Câu hỏi nghiên cứu đang mở](docs/research/OPEN_QUESTIONS.md) | Độ đặc hiệu của feature, hiệu quả và rủi ro feedback, diffusion direction, protocol và điều kiện trước khi xét medical/FL. |
| [Nhật ký tri thức](docs/CHANGELOG.md) | Các lần cập nhật, quyết định, sửa nhận định và phần chưa hoàn thành. |
| [Quy ước cho agent](AGENTS.md) | Đọc nguồn, phân loại mức bằng chứng, cập nhật docs, bảo toàn lịch sử và xác minh commit. |

## Phạm vi hiện tại

- Giữ mục tiêu **image forensics / forgery detection-localization**; không chuyển ngầm thành phân đoạn cơ quan hoặc tổn thương.
- **Diffusion là trục kỹ thuật cần ưu tiên khảo sát**, nhưng chưa mặc định phải dùng diffusion làm backbone; phải phân biệt forensics *of diffusion* với diffusion *for forensics*.
- Ảnh y tế vẫn là một khả năng mở rộng cần biện minh riêng. Không mặc định dùng siêu âm, có dữ liệu nhiều bệnh viện, metadata thiết bị hoặc dữ liệu longitudinal.
- Federated/non-IID vẫn là hướng quan tâm nhưng chưa được ghép cơ học vào bài. Federated unlearning là nhánh phụ chưa được chọn.
- Không coi mọi ảnh tổng hợp, artifact hoặc vùng bất thường là giả mạo. Định nghĩa positive class và quy ước mask phải được xác lập trước thực nghiệm.
- Không xem việc thay SAM bằng MedSAM, thay backbone bằng diffusion hoặc thêm FedAvg là đủ novelty. Không thiết kế nhiều module trước khi có bằng chứng về vấn đề.

## Quy trình lưu tri thức

Mỗi lượt làm việc có nội dung nghiên cứu mới cần: đọc tài liệu hiện có → kiểm tra đúng nguồn/phiên bản → cập nhật ghi chú có dẫn nguồn và mức bằng chứng → cập nhật câu hỏi/nhật ký → commit/push → đọc lại trạng thái remote và báo đường dẫn cùng SHA. Khi bị chặn quyền hoặc xung đột, ghi rõ phần đã làm và phần chưa đẩy; không báo hoàn thành khi mới có bản nháp.

Tài liệu viết bằng tiếng Việt; giữ nguyên tên paper, thuật ngữ và công thức khi cần chính xác. Repo lưu bản tổng hợp nghiên cứu, không mặc định sao chép toàn văn PDF, dữ liệu cá nhân, dữ liệu y tế hoặc thông tin xác thực vào Git.
