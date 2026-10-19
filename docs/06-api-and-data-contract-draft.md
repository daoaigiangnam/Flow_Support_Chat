# Flow Support Chat — API & Data Contract Draft

- Phiên bản: 0.1 Draft
- Mục đích: Điểm bắt đầu để Tech Lead review; chưa phải OpenAPI contract được duyệt.
- Liên quan: docs/05-implementation-blueprint.md

## 1. Quy ước

- Base path đề xuất: /api/v1.
- JSON cho request/response, trừ upload file.
- Thời gian ISO-8601 có timezone.
- ID tài nguyên là opaque identifier.
- Mọi tài nguyên riêng tư phải kiểm tra quyền ở server.
- POST có khả năng retry phải dùng Idempotency-Key hoặc cơ chế chống trùng tương đương.
- Không trả secret, stack trace hoặc ghi chú nội bộ trong response công khai.
- Error response chuẩn có code, message an toàn, request_id và details đã lọc.

## 2. API contract draft

| Method | Path | Mục đích | Điều kiện chính |
|---|---|---|---|
| GET | /api/v1/conversations | Liệt kê Conversation | Chỉ trả dữ liệu theo quyền; có phân trang |
| POST | /api/v1/conversations | Tạo Conversation | Server tự xác định người dùng/tổ chức từ identity |
| GET | /api/v1/conversations/{id} | Đọc Conversation | Kiểm tra quyền truy cập |
| GET | /api/v1/conversations/{id}/messages | Đọc Message | Phân trang, không trả note nội bộ |
| POST | /api/v1/conversations/{id}/messages | Gửi Message | Validate, chống trùng, lưu trước khi xử lý AI |
| POST | /api/v1/conversations/{id}/handoff | Yêu cầu người thật | Idempotent; server tự routing và assignment |
| GET | /api/v1/handoffs/{id} | Đọc trạng thái handoff | Chỉ role được phép xem assignment |
| GET | /api/v1/conversations/{id}/tickets | Đọc Ticket links | Chỉ trả liên kết được phép xem |
| GET | /api/v1/admin/assignment-rules | Quản trị xem ma trận | Admin permission |
| POST/PUT | /api/v1/admin/assignment-rules | Tạo/sửa ma trận | Validate mapping, ghi audit |

Các path là đề xuất, phải được review và version hóa trước khi frontend/backend cùng triển khai.

## 3. Request/response tham chiếu

### 3.1 Tạo Conversation

Request:
{
  "subject": "Tóm tắt yêu cầu",
  "source": "browser_extension"
}

Response 201:
{
  "data": {
    "id": "conversation-id",
    "status": "OPEN",
    "created_at": "2026-10-19T10:00:00+07:00"
  }
}

Server tự gắn danh tính và tổ chức từ phiên xác thực. Không tin organization_id do client gửi.

### 3.2 Gửi Message

Request:
{
  "client_message_id": "client-generated-unique-id",
  "content": {
    "type": "text",
    "text": "Tôi cần hỗ trợ dịch vụ"
  }
}

Response 202 nếu xử lý AI bất đồng bộ:
{
  "data": {
    "message_id": "message-id",
    "conversation_id": "conversation-id",
    "status": "ACCEPTED",
    "created_at": "2026-10-19T10:00:00+07:00"
  }
}

Quy tắc:
1. Kiểm tra quyền trên Conversation.
2. Validate loại/kích thước nội dung.
3. Chống trùng theo Idempotency-Key hoặc client_message_id.
4. Lưu Message trước khi tạo tác vụ AI.
5. Server tự xác định author/user/organization.
6. Trả lỗi có code an toàn nếu validation hoặc quyền thất bại.

### 3.3 Yêu cầu handoff

Request:
{
  "reason": "customer_requested_human"
}

Response 202:
{
  "data": {
    "handoff_id": "handoff-id",
    "status": "ROUTING",
    "ticket_sync_status": "PENDING_CREATE"
  }
}

Server:
1. Xác minh quyền và trạng thái Conversation.
2. Nếu handoff đang mở, trả trạng thái hiện tại thay vì tạo bản thứ hai.
3. Tạo handoff summary.
4. Chạy Routing Engine và Assignment Matrix.
5. Nếu không có người nhận, đưa Triage và phát sự kiện điều phối.
6. Tạo/link Ticket theo policy.
7. Không báo Ticket created trước khi Bitrix24 xác nhận.

### 3.4 Đọc trạng thái handoff

Response draft:
{
  "data": {
    "id": "handoff-id",
    "status": "ASSIGNED",
    "group": "TECHNICAL",
    "assignment": {
      "primary_id": "staff-id",
      "owner_id": "staff-id",
      "backup_id": "staff-id"
    },
    "ticket": {
      "status": "SYNCED",
      "provider": "bitrix24",
      "external_id": "bitrix-ticket-id"
    }
  }
}

Ví dụ ID là dữ liệu minh họa, không phải ID thật. Các trường assignment/ticket chỉ hiển thị cho role có quyền.

## 4. DTO logic

### Conversation
- id
- organization/customer context (server-owned)
- created_by
- status
- source
- created_at, updated_at, last_activity_at
- subject (optional)

### Message
- id
- conversation_id
- author_type: CUSTOMER, AI, STAFF, SYSTEM
- author_id (nullable theo loại tác giả)
- content_type, content payload đã validate
- created_at
- delivery/status metadata
- client_message_id/idempotency reference

### Handoff
- id, conversation_id, reason, summary
- suggested_group, resolved_group
- status, created_at, updated_at
- assignment reference, ticket link reference

### Assignment Rule
- customer/organization reference
- group/service scope
- primary reference
- technical/business owner reference
- backup reference
- escalation reference
- effective_from, effective_to, active flag
- version/audit metadata

### Ticket Link
- id, conversation_id, provider, object_type
- external_ticket_id
- sync_status, last_sync_at
- last_error_code đã chuẩn hóa
- created_at, updated_at

### Integration Job
- id, job_type, aggregate reference
- idempotency/deduplication key
- status, attempts, next_attempt_at
- last_error_code, created_at, updated_at

Đây là DTO logic, không quyết định tên bảng/cột.

## 5. Error codes

| Code | Ý nghĩa | Hành vi |
|---|---|---|
| AUTH_REQUIRED | Chưa xác thực/hết phiên | Yêu cầu xác thực lại |
| ACCESS_DENIED | Không có quyền | Từ chối |
| VALIDATION_FAILED | Payload không hợp lệ | Không tạo đối tượng |
| RATE_LIMITED | Gửi quá nhanh | Thông báo chờ |
| AI_TEMPORARY_FAILURE | Provider AI lỗi | Retry giới hạn hoặc handoff |
| ROUTING_UNRESOLVED | Không xác định nhóm | Đưa Triage |
| ASSIGNMENT_UNAVAILABLE | Không có người hợp lệ | Cảnh báo điều phối |
| BITRIX_PENDING_RECONCILIATION | Timeout chưa rõ kết quả | Đối chiếu trước retry |
| BITRIX_ACTION_REQUIRED | Lỗi quyền/validation | Cảnh báo vận hành, không retry vô hạn |
| RESOURCE_NOT_FOUND | Không tồn tại hoặc ngoài quyền | Không tiết lộ tài nguyên ngoài quyền |

## 6. HTTP status dự kiến

- 200: đọc/cập nhật thành công.
- 201: tạo tài nguyên đồng bộ thành công.
- 202: tiếp nhận xử lý bất đồng bộ.
- 400: request sai cấu trúc.
- 401: chưa xác thực.
- 403: không có quyền.
- 404: không tồn tại hoặc ngoài quyền.
- 409: xung đột trạng thái/idempotency.
- 422: validation nghiệp vụ thất bại.
- 429: rate limit.
- 500: lỗi không dự kiến.
- 502/503/504: lỗi phụ thuộc ngoài/timeout khi phù hợp.

## 7. Các quyết định phải chốt trước OpenAPI chính thức

1. Cơ chế xác thực Extension và nhân viên.
2. Polling, SSE hay WebSocket.
3. Upload file và lưu trữ file.
4. Error envelope và mã lỗi cuối cùng.
5. Phân trang, retention và archive.
6. Kênh nhân viên phản hồi khách.
7. Loại đối tượng Bitrix24, field/status/permission.
8. Cách tìm Ticket sau timeout.
9. Endpoint admin và permission matrix.
10. Schema OpenAPI và versioning chính thức.
