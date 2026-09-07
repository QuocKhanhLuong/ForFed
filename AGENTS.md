# Hướng dẫn làm việc trong ForFed

## 1. Nguồn tri thức chính thức

Repo chính thức: `QuocKhanhLuong/ForFed`.

Theo yêu cầu chủ dự án ngày 2026-09-07, **mọi lượt survey hoặc cập nhật kiến thức về ForFed phải được lưu thành docs và commit/push vào repo**, không chỉ trả lời trong chat. Đây là bước hoàn tất của lượt làm việc đang diễn ra; không được hứa có tiến trình nền hoặc lịch tự động khi chưa thực sự thiết lập.

Trước khi ghi: đọc README, hướng dẫn áp dụng cho thư mục đích, tài liệu hiện có và trạng thái nhánh. Bảo toàn thay đổi của cộng tác viên. Không force-push, reset lịch sử hoặc ghi đè file chưa đọc. Tôn trọng nhánh bảo vệ và quy trình PR hiện có; không tự thay cấu hình bảo vệ.

## 2. Phạm vi nghiên cứu

- Điểm xuất phát là SARIF, từ đúng file `2606.21108v1.pdf` người dùng đã cung cấp; đọc cơ chế trước khi survey mở rộng.
- Không đổi forensic thành phân đoạn cơ quan/tổn thương. Ghi rõ đối tượng chỉnh sửa và quy ước nhãn.
- Không mặc định dùng siêu âm hoặc dùng dữ liệu của dự án khác. Không mặc định đã có multi-site data, metadata thiết bị, nhãn hay quyền chia sẻ dữ liệu.
- Medical, federated/non-IID và tổ hợp hai hướng phải được biện minh riêng. Federated unlearning chưa được chốt.
- Không xem thay backbone hoặc thêm FedAvg là đủ novelty. Không thiết kế hệ nhiều module trước khi xác nhận vấn đề.
- Không gọi mọi ảnh sinh, artifact, bất thường hoặc phép xử lý hợp lệ là forgery.

## 3. Quy tắc bằng chứng

Mỗi ghi chú paper/survey cần ghi ngày, nguồn, phiên bản, phạm vi đã đọc và những bước chưa thực hiện. Tách rõ:

1. **Paper báo cáo:** dẫn section, equation, figure hoặc table.
2. **Suy luận:** lập luận của người đọc, không giả làm kết quả tác giả.
3. **Giả thuyết:** cần thí nghiệm, kèm khả năng bị bác bỏ.
4. **Chưa xác minh:** thông tin thiếu nguồn, thiếu quyền truy cập hoặc chưa khóa phiên bản.

Ưu tiên paper gốc và tài liệu chính thức. Khi tra công trình mới hoặc tình trạng có thể thay đổi, kiểm tra nguồn công khai hiện hành và ghi ngày truy cập. Với file đã tải lên, tìm đúng file/phiên bản trong nguồn được cho phép; không âm thầm thay bằng paper gần tên. Link công khai và bản người dùng tải lên phải được phân biệt; chưa có checksum thì không khẳng định đồng nhất từng byte.

Với code: lưu repo, commit SHA, đường dẫn và dòng/hàm đã kiểm tra. Không coi `main` luôn trùng với PDF. Không mang nhận xét code từ chat cũ thành sự thật đã xác minh nếu chưa có source snapshot. Với metric: ghi dataset, split, preprocessing, threshold, aggregation và đơn vị; không so số giữa protocol khác nhau như thể tương đương.

## 4. Cấu trúc tài liệu

- `docs/papers/`: ghi chú chuyên sâu từng paper, công thức, bảng claim → evidence → limitation.
- `docs/research/`: câu hỏi mở, giả thuyết, tiêu chí đối chứng và quyết định nghiên cứu.
- `docs/CHANGELOG.md`: nhật ký cập nhật tri thức; ghi sửa sai bằng mục mới, không xóa dấu vết của nhận định cũ.
- `README.md`: mục lục và trạng thái dự án hiện tại.

Tận dụng file đang có nếu cùng chủ đề; không tạo bản trùng chỉ vì sang chat mới. Thêm thư mục khác khi có nhu cầu thực, không dựng sẵn một kiến trúc nghiên cứu chưa được chọn. Viết nội dung bằng tiếng Việt, giữ thuật ngữ và công thức cần thiết.

## 5. Điều kiện hoàn tất một lượt cập nhật

- Nội dung mới đã được tích hợp vào file thích hợp, có nguồn và trạng thái bằng chứng.
- README/câu hỏi mở được cập nhật nếu có thay đổi thực; changelog ghi ngày và điều đã thay đổi.
- Kiểm tra liên kết nội bộ, số liệu, công thức và các câu khẳng định quá mức.
- Commit mô tả rõ thay đổi; push thành công hoặc dùng API tạo commit remote thành công.
- Đọc lại file/nhánh remote để xác minh; báo đường dẫn và commit SHA trong phản hồi.
- Nếu bị chặn, nêu chính xác phần chưa lưu/push và nguyên nhân. Không nói đã đẩy khi mới soạn nội dung.

Chỉ thay đổi tài liệu liên quan theo phạm vi lượt làm việc. Không tự sửa code nghiên cứu hoặc triển khai thí nghiệm nếu lượt hiện tại chỉ yêu cầu đọc/survey.

## 6. Dữ liệu và công bố

Repo ở chế độ public khi khởi tạo tài liệu. Chỉ đưa ghi chú nghiên cứu phù hợp để công khai và nguồn dẫn. Không đưa thông tin tài khoản, bí mật, dữ liệu người bệnh, thông tin quản lý cá nhân hay toàn văn tài liệu không có quyền phân phối vào Git. Không tự chuyển dữ liệu từ dịch vụ riêng tư sang repo public.
