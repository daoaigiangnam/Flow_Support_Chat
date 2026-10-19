# Flow Support Chat — Implementation Blueprint cho Dev

- Phiên bản: 1.0
- Mục tiêu: Chuyển nghiệp vụ thành module, trình tự xử lý, contract logic, quy tắc lỗi và kế hoạch triển khai.
- Liên quan: 01 Business Workflow, 02 Functional Requirements, 03 Decision Tables, 04 Acceptance Tests.
- Trạng thái: Có thể triển khai nền tảng; phụ thuộc Bitrix24 phải xác minh trước khi tích hợp chính thức.

## 1. Nguyên tắc triển khai

1. Server là nơi kiểm soát nghiệp vụ và quyền truy cập; Extension không tự quyết định quyền.
2. Conversation, Message, Handoff, Assignment, Ticket Link và Integration Sync là các khái niệm riêng.
3. Bitrix24 là nguồn dữ liệu chính thức cho Ticket sau khi xác nhận loại đối tượng.
4. AI chỉ đề xuất intent/group/summary; Routing Engine quyết định theo rule cấu hình.
5. Không có mapping hợp lệ thì chuyển Triage, không gán ngẫu nhiên.
6. Thao tác có thể retry phải chống trùng và có khả năng đối chiếu.
7. Secret AI/Bitrix24 chỉ lưu phía server/secret manager.
8. Thay đổi cấu hình, assignment, group và trạng thái quan trọng phải audit.
9. Tác vụ gọi AI/Bitrix/notification phải có timeout, correlation ID và cách khôi phục.
10. Không hard-code SLA, nhân sự, loại Ticket, trạng thái Bitrix hoặc ngưỡng AI chưa được duyệt.

## 2. Kiến trúc logic đề xuất

Browser Extension → Chat Backend API → Authentication/Authorization → Conversation Service → Application Database.
Conversation Service gọi AI Orchestrator; AI Orchestrator gọi AI provider được cấu hình và nguồn tri thức được phê duyệt.
Khi handoff, Routing Engine áp dụng rules và Assignment Matrix. Các tác vụ Bitrix24/notification đi qua durable job/outbox và background worker. Admin/Ops quản lý ma trận, theo dõi job lỗi và audit.

## 3. Ranh giới module

| Module | Trách nhiệm | Không chịu trách nhiệm |
|---|---|---|
| Extension UI | Hiển thị Chat, gửi Message, trạng thái | Không giữ secret, không tự phân quyền |
| API/Auth | Xác thực, kiểm tra quyền, validate request | Không chứa prompt AI trực tiếp |
| Conversation | Quản lý hội thoại, Message và trạng thái hội thoại | Không thay thế Ticket Bitrix24 |
| AI Orchestrator | Gọi AI, tra cứu tri thức, chuẩn hóa kết quả, tạo summary | Không quyết định assignment cuối |
| Routing Engine | Chọn group/Triage theo rule | Không dùng LLM tự do để gán người |
| Assignment Matrix | Mapping customer + group/service → Primary/Owner/Backup/Escalation | Không hard-code nhân sự |
| Bitrix Adapter | Chuyển đổi nghiệp vụ sang đối tượng Bitrix đã chốt | Không giả định mọi đối tượng có cùng field/status |
| Worker/Outbox | Retry, reconciliation, notification, tác vụ nền | Không phụ thuộc Extension đang mở |
| Admin/Audit | Quản lý cấu hình và truy vết | Không cho chỉnh cấu hình vượt quyền |

## 4. Trình tự handoff chuẩn

1. Server nhận yêu cầu handoff, xác thực quyền và kiểm tra Conversation.
2. Chống tạo handoff trùng cho cùng yêu cầu đang mở.
3. Thu thập lịch sử liên quan và tạo handoff summary.
4. Phân loại yêu cầu hoặc dùng kết quả AI đã lưu.
5. Routing Engine áp dụng rule được cấu hình.
6. Assignment Matrix tìm Primary/Owner/Backup/Escalation hợp lệ.
7. Nếu không có người nhận hợp lệ, đặt Triage/Needs Assignment, phát cảnh báo điều phối và không tự chọn người.
8. Nếu yêu cầu cần Ticket, kiểm tra Ticket link hiện có.
9. Nếu chưa có Ticket, tạo durable job/outbox cho Bitrix24.
10. Worker gọi adapter. Nếu thành công, lưu external Ticket ID. Nếu timeout chưa rõ kết quả, chuyển Reconciliation Required trước khi retry.
11. Chỉ sau xác nhận Ticket được tạo/liên kết mới gửi thông báo tương ứng.
12. Cập nhật trạng thái Conversation và ghi audit cho group, assignment, ticket link và handoff.

Không giữ transaction database mở trong khi chờ AI hoặc Bitrix24. Các bước qua hệ thống ngoài phải chịu được timeout, retry và process restart.

## 5. API contract nghiệp vụ đề xuất

Các path dưới đây là draft để Tech Lead review, chưa phải OpenAPI đã duyệt.

| Use case | Contract draft | Thành công | Lỗi quan trọng |
|---|---|---|---|
| Liệt kê Conversation | GET /api/v1/conversations | Danh sách theo quyền | 401/403 |
| Tạo Conversation | POST /api/v1/conversations | Conversation ID | 401/403/422 |
| Đọc Conversation | GET /api/v1/conversations/{id} | Metadata theo quyền | 403/404 |
| Liệt kê Message | GET /api/v1/conversations/{id}/messages | Message theo cursor | 403/404 |
| Gửi Message | POST /api/v1/conversations/{id}/messages | Message ID và trạng thái | 403/409/422/429 |
| Yêu cầu người thật | POST /api/v1/conversations/{id}/handoff | Handoff ID/trạng thái | 403/409/422 |
| Xem trạng thái handoff | GET /api/v1/handoffs/{id} | Group/assignment/sync state | 403/404 |
| Xem Ticket link | GET /api/v1/conversations/{id}/tickets | Ticket link theo quyền | 403/404 |
| Admin xem mapping | GET /api/v1/admin/assignment-rules | Mapping được phép xem | 401/403 |
| Admin cập nhật mapping | POST/PUT /api/v1/admin/assignment-rules | Mapping và audit | 403/409/422 |

Quy tắc chung:
- Mọi endpoint riêng tư phải kiểm tra quyền ở server.
- POST có khả năng retry phải hỗ trợ Idempotency-Key hoặc cơ chế tương đương.
- Error response có code, message an toàn và request_id; không trả stack trace/secret.
- Server tự xác định user/organization từ identity; không tin user_id/organization_id do Extension gửi.
- Handoff request từ client không được tự quyết group/assignee.
- Phân trang có giới hạn; không tải vô hạn lịch sử.
- Ghi chú nội bộ không được đưa vào DTO công khai.

## 6. Payload nghiệp vụ tham chiếu

### Gửi Message
{
  "client_message_id": "client-generated-unique-id",
  "content": {
    "type": "text",
    "text": "Mô tả yêu cầu của khách hàng"
  }
}

client_message_id dùng chống gửi trùng khi client retry. Server tự gắn tác giả và tổ chức từ phiên xác thực. File phải qua cơ chế upload được xác thực và kiểm tra quyền.

### Handoff request
{
  "reason": "customer_requested_human"
}

Server lấy group/assignment từ rule và dữ liệu đã xác minh. Không tin group hoặc assignee_id do client tự gửi.

### Handoff summary nội bộ
{
  "customer_request": "Tóm tắt yêu cầu gốc",
  "intent": "technical",
  "group_suggestion": "technical",
  "facts_collected": [],
  "steps_already_tried": [],
  "customer_reported_result": "unknown",
  "missing_information": [],
  "risk_flags": [],
  "source_message_ids": [],
  "generated_at": "ISO-8601 timestamp"
}

Summary do AI tạo là nội dung hỗ trợ, không tự động coi mọi nội dung là dữ kiện đã xác minh.

## 7. Entity logic cần có

- User/Identity
- Organization/Customer
- Conversation
- Message
- Handoff
- Support Group
- Assignment Rule và Assignment History
- Ticket Link
- Integration Job/Sync Attempt
- Notification Job
- Audit Event
- AI Run/Knowledge Reference nếu cần truy vết

Ràng buộc:
- Mọi Message thuộc đúng Conversation.
- Conversation thuộc đúng phạm vi identity đã xác thực.
- Ticket Link lưu provider, object type đã chốt, external Ticket ID và sync status.
- Assignment History không bị xóa khi thay assignment hiện tại.
- Integration Job lưu loại tác vụ, deduplication key, số lần thử, thời điểm thử tiếp theo và lỗi đã chuẩn hóa.
- Audit không ghi secret hoặc dữ liệu nhạy cảm không cần thiết.

## 8. Error contract

| Code | Ví dụ | Hành vi |
|---|---|---|
| AUTH_REQUIRED | Phiên hết hạn | Yêu cầu xác thực lại |
| ACCESS_DENIED | Không có quyền | Từ chối, ghi security event theo policy |
| VALIDATION_FAILED | Payload sai/thiếu | Không tạo đối tượng |
| RATE_LIMITED | Gửi quá nhanh | Thông báo chờ, giới hạn tần suất |
| AI_TEMPORARY_FAILURE | Provider lỗi | Retry giới hạn hoặc handoff |
| ROUTING_UNRESOLVED | Không rõ nhóm | Đưa Triage |
| ASSIGNMENT_UNAVAILABLE | Không có người hợp lệ | Cảnh báo điều phối |
| BITRIX_PENDING_RECONCILIATION | Timeout chưa rõ kết quả | Đối chiếu trước retry |
| BITRIX_ACTION_REQUIRED | Lỗi quyền/validation | Cảnh báo vận hành, không retry vô hạn |
| RESOURCE_NOT_FOUND | Không tồn tại/ngoài quyền | Không tiết lộ tài nguyên ngoài quyền |

## 9. Definition of Done mỗi module

- Requirement ID đã triển khai hoặc có lý do loại khỏi release.
- Có unit test cho rules/validation và integration test cho phụ thuộc ngoài.
- Có test phân quyền và truy cập chéo tổ chức.
- Có test retry/idempotency.
- Có correlation ID và log an toàn.
- UI có loading, empty, error và retry khi cần.
- Có tài liệu cấu hình và xử lý lỗi.
- Acceptance criteria liên quan đã PASS.
- Không có quyết định OPEN bị âm thầm hard-code.

## 10. ADR cần ghi nhận

1. Backend runtime/framework và cách chạy worker.
2. Xác thực Extension và nhân viên.
3. Realtime: polling, SSE hoặc WebSocket.
4. Database và migration strategy.
5. Durable queue/outbox và retry.
6. AI provider, timeout, fallback và nguồn tri thức.
7. Bitrix24 object type, field/status mapping và reconciliation.
8. Observability, retention, backup.
9. Deploy, rollback và quản lý secret.

## 11. Trình tự triển khai đề xuất

### Sprint 0 — Khóa quyết định
- Xác nhận stack/repository conventions.
- Xác minh Bitrix24 object/API/permissions trên môi trường thử nghiệm.
- Chốt auth, ma trận mẫu, policy Ticket và SLA baseline.
- Tạo ADR và Definition of Done.

### Sprint 1 — Chat nền tảng
- Auth/authorization.
- Conversation/Message.
- Extension gửi/nhận Message.
- Lịch sử, lỗi gửi, chống trùng Message.
- Test tenant isolation.

### Sprint 2 — AI First Response
- AI adapter interface và provider config phía server.
- Knowledge retrieval được phê duyệt.
- Hỏi bổ sung, phản hồi, lưu AI run.
- Timeout/fallback và handoff trigger.

### Sprint 3 — Routing và Assignment
- Chuẩn hóa intent/group.
- Routing rules và Triage queue.
- Assignment matrix, lịch sử và handoff summary.
- Test thiếu mapping/Primary không khả dụng.

### Sprint 4 — Bitrix24
- Adapter theo đúng object đã xác nhận.
- Create/find/link Ticket.
- Status/user mapping.
- Outbox, retry và reconciliation.
- Sandbox integration tests.

### Sprint 5 — Notification, SLA và vận hành
- Notification jobs.
- SLA/escalation theo policy được duyệt.
- Admin/audit và màn hình theo dõi lỗi.

### Sprint 6 — UAT và release
- Chạy acceptance tests.
- Kiểm tra bảo mật, tải cơ bản, backup/restore và rollback.
- Pilot một nhóm khách hàng trước khi mở rộng.

## 12. Điều kiện chặn production

Không phát hành production nếu chưa có:
- Authentication và authorization phía server.
- Kiểm thử truy cập chéo khách hàng.
- Chống trùng/đối chiếu khi tạo Ticket timeout.
- Secret không nằm trong Extension.
- Hàng đợi xử lý khi assignment unresolved.
- Bitrix24 object/status mapping đã xác minh.
- Theo dõi lỗi tích hợp.
- Quy định dữ liệu nhạy cảm và ghi chú nội bộ.
