# Flow_Support_Chat

Tài liệu nghiệp vụ và kỹ thuật cho Browser Extension Chat Support, AI First Response, định tuyến, phân công nhân sự và tích hợp Bitrix24.

## Bộ tài liệu cho đội Dev

1. [01 — Đặc tả nghiệp vụ tổng thể](docs/01-business-workflow-specification.md) — Luồng nghiệp vụ, vai trò, ngoại lệ, bảo mật và quyết định còn mở.
2. [02 — Đặc tả yêu cầu chức năng](docs/02-functional-requirements.md) — Yêu cầu có ID theo module và yêu cầu phi chức năng.
3. [03 — Bảng quyết định và chuyển trạng thái](docs/03-decision-tables-and-state-transitions.md) — Routing, assignment, Ticket, retry và lifecycle.
4. [04 — Kịch bản nghiệm thu chi tiết](docs/04-acceptance-test-scenarios.md) — 26 kịch bản test baseline.
5. [05 — Implementation Blueprint cho Dev](docs/05-implementation-blueprint.md) — Ranh giới module, trình tự handoff, kiến trúc logic, API draft, lỗi, Definition of Done và kế hoạch sprint.
6. [06 — API & Data Contract Draft](docs/06-api-and-data-contract-draft.md) — API paths, payload mẫu, DTO logic, error codes và quyết định cần chốt trước OpenAPI.

## Cách sử dụng

- Product Owner/nghiệp vụ: xác nhận các mục OPEN, ma trận M-Tech Care, policy Ticket, SLA và quyền.
- Tech Lead: duyệt architecture decision records (ADR), API contract và đối tượng Bitrix24.
- Developer: triển khai theo requirement ID và module; không tự suy diễn quyết định nghiệp vụ còn mở.
- QA: dùng tài liệu 03 và 04, bổ sung dữ liệu kiểm thử, bằng chứng, PASS/FAIL và defect ID.

## Trạng thái

Bộ tài liệu đã đủ chi tiết để bắt đầu các phần nền tảng như Chat/Conversation, Auth, Message và khung AI/Routing. API ở tài liệu 06 là draft. Trước khi hoàn thiện tích hợp Bitrix24/production phải xác minh loại đối tượng Ticket, field/status mapping, quyền API, ma trận nhân sự thực tế, SLA và kênh phản hồi nhân viên. Chưa có schema database hoặc OpenAPI contract cuối cùng.
