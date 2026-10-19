# Flow_Support_Chat

Tài liệu nghiệp vụ cho hệ thống Browser Extension Chat Support, AI First Response, định tuyến và phân công nhân sự hỗ trợ khách hàng, tích hợp Bitrix24.

## Bộ tài liệu cho đội Dev

1. [01 — Đặc tả nghiệp vụ tổng thể](docs/01-business-workflow-specification.md)  
   Mục tiêu, phạm vi, vai trò, luồng tổng thể, quy trình từng bước, ngoại lệ, bảo mật và quyết định còn mở.

2. [02 — Đặc tả yêu cầu chức năng](docs/02-functional-requirements.md)  
   Các yêu cầu chức năng có ID theo từng module: Chat, Auth, AI, Routing, Assignment, Ticket, Staff Workflow, Notification, SLA, Admin; kèm yêu cầu phi chức năng và Definition of Ready.

3. [03 — Bảng quyết định và chuyển trạng thái](docs/03-decision-tables-and-state-transitions.md)  
   Bảng quyết định AI handoff, phân loại, phân công M-Tech Care, tạo Ticket, lỗi Bitrix24, chuyển trạng thái Conversation/Ticket và thông báo.

4. [04 — Kịch bản nghiệm thu chi tiết](docs/04-acceptance-test-scenarios.md)  
   26 kịch bản nghiệm thu có tiền điều kiện, thao tác và kết quả mong đợi.

## Cách sử dụng tài liệu

- **Nghiệp vụ/Product Owner:** Xác nhận các mục OPEN, ma trận phân công, quy tắc tạo Ticket, SLA, quyền và chính sách đóng.
- **Solution Architect/Tech Lead:** Dựa trên yêu cầu đã duyệt để thiết kế kiến trúc, tích hợp, API contract và mô hình dữ liệu.
- **Dev:** Triển khai theo ID yêu cầu; không tự suy diễn các quyết định nghiệp vụ còn mở.
- **QA:** Dùng bộ test case làm baseline; bổ sung dữ liệu test, bằng chứng, PASS/FAIL và defect ID.

## Trạng thái và giới hạn

Đây là baseline phân tích nghiệp vụ và yêu cầu chức năng. Các điểm còn mở phải được xác nhận trước khi chốt thiết kế kỹ thuật. Chưa chốt schema database, endpoint API, loại đối tượng Bitrix24, SLA thực tế hoặc danh sách nhân sự phân công thực tế.
