# Hướng Diffusion cho ForFed

Cập nhật: **2026-09-11**.

## Tín hiệu định hướng mới

Giảng viên hướng dẫn muốn dự án **làm về diffusion**. Đây là một thay đổi quan trọng của source of truth, nhưng hiện mới là **ràng buộc định hướng**, chưa phải bài toán, dataset hay kiến trúc đã chốt.

SARIF vẫn được giữ làm paper xuất phát về **image forgery localization** và là nguồn để đặt câu hỏi về feature specificity, iterative refinement và cross-domain robustness. Tuy nhiên, không mặc định kiến trúc cuối cùng phải là SAM-based hoặc mở rộng trực tiếp SARIF.

Federated learning vẫn là trục nghiên cứu đang quan tâm nhưng **chưa được phép trở thành phần ghép cơ học** kiểu `diffusion + FedAvg`. Federated unlearning tiếp tục là nhánh phụ, chưa chốt.

## Ba cách hiểu cần tách riêng

### D1 — Forensics *of* diffusion

Phát hiện hoặc định vị vùng được **tạo/chỉnh sửa bằng diffusion models**: text-to-image, image-to-image, inpainting, local editing hoặc các thao tác tương tự.

Đây là cách hiểu gần nhất với mục tiêu forensic hiện tại. Positive class phải là một thao tác can thiệp đã định nghĩa rõ; không gọi mọi ảnh tổng hợp, artifact hoặc vùng bất thường là giả mạo.

### D2 — Diffusion *for* forensics

Dùng diffusion model, denoising process, reconstruction trajectory, score/noise prediction hoặc representation bên trong diffusion model làm **nguồn forensic evidence**.

Đây là hướng kỹ thuật khác với D1: diffusion model là công cụ hoặc backbone của detector/localizer, không chỉ là nguồn sinh ảnh giả.

### D3 — Federated diffusion / federated forensic learning

Đưa bài toán D1 hoặc D2 vào môi trường nhiều client/non-IID, hoặc nghiên cứu bản thân diffusion model trong federated setting.

Chỉ mở nhánh này khi threat model và nhu cầu phân tán dữ liệu được biện minh. `Thêm FedAvg` không tự động là novelty.

## Khuyến nghị phạm vi trước mắt

Ưu tiên khảo sát và kiểm chứng theo thứ tự:

1. **D1: diffusion-based image forgery localization/detection** — xác định chính xác đối tượng forensic và các benchmark gần nhất.
2. **D2: diffusion-derived forensic cues** — xem diffusion có cung cấp dấu vết/representation khác gì các cue của SARIF và các forensic backbone hiện tại.
3. Chỉ sau đó mới đánh giá có lý do thật sự cho **D3: federated/non-IID** hay không.

Lý do: thứ tự này giữ được bài toán forensic, đáp ứng yêu cầu làm về diffusion, đồng thời tránh xây một hệ thống nhiều module trước khi xác nhận vấn đề.

## Các claim chưa được phép kết luận

- Ảnh do diffusion sinh ra luôn dễ phát hiện hơn ảnh chỉnh sửa truyền thống.
- Diffusion reconstruction error hoặc denoising residual mặc nhiên là forensic-specific.
- Một diffusion backbone sẽ tổng quát tốt hơn SAM/CNN/Transformer trên unseen generators.
- Medical images cần diffusion-specific detector chỉ vì miền dữ liệu khác ảnh tự nhiên.
- Non-IID giữa bệnh viện/client chắc chắn làm hỏng cue forensic.
- Federated learning hoặc federated unlearning là cần thiết cho novelty của paper.

## Câu hỏi cần survey/đo tiếp

1. Bài toán gần nhất hiện nay là image-level diffusion detection hay pixel-level localization của diffusion editing/inpainting?
2. Có benchmark nào chứa cả ảnh pristine và vùng chỉnh sửa diffusion với mask đáng tin cậy không?
3. Các phương pháp hiện tại khai thác frequency/noise artifacts, reconstruction/denoising behavior, latent features hay provenance/model fingerprints?
4. Cue của diffusion có bền qua JPEG, resize, denoise, screenshot, re-encoding và unseen generators không?
5. Có thể thiết kế paired/control experiments để tách **manipulation signal** khỏi **generator/domain signal** không?
6. Nếu sau này xét medical hoặc multi-site, client/domain shift có làm thay đổi cue diffusion nhiều hơn chính thao tác can thiệp không?
7. Điều gì có thể bác bỏ giả thuyết nghiên cứu trước khi xây thêm module?

## Trạng thái

- **Đã chốt:** dự án phải nghiêm túc xem diffusion là trục kỹ thuật chính theo định hướng của giảng viên.
- **Chưa chốt:** D1, D2 hay D3 là bài toán cuối; medical modality; dataset; generator family; federated algorithm; unlearning; kiến trúc mới.
- **Bước kế tiếp hợp lý:** targeted survey về diffusion forensics, ưu tiên paper gốc và benchmark, rồi lập bảng problem → data/label → evidence → limitation → falsifiable pilot.
