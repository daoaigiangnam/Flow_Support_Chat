# Flow Support Chat — Business Completeness Audit

- Mã tài liệu: PM-AUDIT-001
- Phiên bản: 1.0
- Ngày rà soát: 2026-10-20
- Trạng thái: **AUDIT DRAFT — cần Product Owner/Tech Lead xác nhận**
- Phạm vi: Tính đầy đủ và nhất quán của tài liệu nghiệp vụ, yêu cầu, quyết định, kiểm thử, API và UI/UX cho sản phẩm Flow Support Chat.
- Ngoài phạm vi: Không mở rộng thành hệ thống ITSM/IT Outsourcing tổng thể; không tự phê duyệt chính sách thay Product Owner.
- Nguyên tắc: Phân biệt rõ nội dung đã được mô tả, khoảng trống cần xác minh và quyết định nghiệp vụ chưa được duyệt. Không coi đề xuất là chính sách production.

## 1. Kết luận điều hành

Bộ tài liệu hiện đã có nền tảng đáng kể: đặc tả nghiệp vụ, yêu cầu chức năng, bảng quyết định, 26 test case, blueprint triển khai, API/data contract draft, bộ UI/UX, 8 luồng hỗ trợ tổng thể và Decision Register.

**Kết luận audit:** Bộ tài liệu phù hợp làm baseline phân tích và review, nhưng chưa nên coi là baseline production đã khóa. Các rủi ro lớn nhất không phải thiếu thêm một danh sách nghiệp vụ tổng quát, mà là:
1. Quyết định P0 còn OPEN, đặc biệt danh tính khách hàng, vòng đời Conversation, đối tượng Ticket Bitrix24, assignment và SLA.
2. Chưa có ma trận truy vết tập trung, đầy đủ từ quyết định → requirement → flow/state → API/UI → acceptance test.
3. API đang là draft, chưa phải OpenAPI contract được duyệt; tích hợp Bitrix24 còn phụ thuộc xác minh hệ thống thực tế.
4. Cần mở rộng kiểm thử cho tính nhất quán dữ liệu, quyền, xử lý đồng thời, retry/reconciliation và các lỗi vận hành.
5. UI/UX là proposal/concept; không được hiểu là brand, nội dung hoặc hành vi đã được phê duyệt.

Đây là audit tài liệu ở mức cấu trúc và nội dung được mô tả trong các file hiện có. Các mục dưới đây cần được xác nhận bằng Product Owner/Tech Lead và kiểm chứng với hệ thống Bitrix24 thực tế trước khi đóng trạng thái.

## 2. Danh mục tài liệu đã rà soát

| Tài liệu | Vai trò | Nhận định audit | Hành động |
|---|---|---|---|
| [01 — Business Workflow](01-business-workflow-specification.md) | Luồng nghiệp vụ tổng thể, vai trò, trạng thái, ngoại lệ | Đã có luồng từ tiếp nhận đến đóng; nhiều quyết định vẫn mở | Đồng bộ với Decision Register và state transition |
| [02 — Functional Requirements](02-functional-requirements.md) | Requirement theo module và NFR | Có ID yêu cầu và MUST/SHOULD/OPEN | Bổ sung traceability và acceptance criteria cho từng requirement |
| [03 — Decision Tables](03-decision-tables-and-state-transitions.md) | Quy tắc routing, assignment, Ticket, state | Có bảng quyết định; một số điều kiện còn OPEN | Chốt điều kiện, đầu vào, kết quả, fallback và audit event |
| [04 — Acceptance Tests](04-acceptance-test-scenarios.md) | Kịch bản nghiệm thu | Có 26 test case baseline | Bổ sung test cho retry, concurrency, security và recovery |
| [05 — Implementation Blueprint](05-implementation-blueprint.md) | Module, architecture logic, triển khai | Có nguyên tắc server-authoritative và release gates | Chuyển các giả định kỹ thuật thành ADR đã duyệt |
| [06 — API & Data Contract](06-api-and-data-contract-draft.md) | API, DTO, error contract | Được ghi rõ là draft | Chốt OpenAPI, auth, validation, pagination, rate limit, versioning |
| [07 — UI Design System](07-ui-design-system.md) | Design tokens, component, accessibility | Proposal; có danh sách UI-OPEN | PO/Design/Tech Lead xác nhận hoặc ghi rõ chưa thuộc MVP |
| [08 — Screen Inventory & User Flows](08-screen-inventory-and-user-flows.md) | Danh mục màn hình, user flow, UI states | Có screen inventory và một phần traceability | Chuẩn hóa ID và liên kết requirement/test |
| [09 — UI/UX Checklist](09-ui-ux-implementation-checklist.md) | DoR/DoD và UI QA | Có checklist nền tảng | Áp dụng cho từng màn hình và lưu bằng chứng nghiệm thu |
| [10 — Extension Screens](10-browser-extension-screen-specifications.md) | Giao diện khách hàng | Có screen spec và acceptance criteria | Đối chiếu với auth, chat lifecycle và accessibility |
| [11 — Support Dashboard](11-support-dashboard-screen-specifications.md) | Giao diện nhân viên hỗ trợ | Có queue, workspace, Ticket context và resolution | Chốt quy tắc quyền, chuyển giao và hành vi từng trạng thái |
| [12 — Complete Support Flows](12-complete-support-flows.md) | Tám luồng end-to-end và release gates | Có bao phủ luồng chính và recovery | Dùng làm master flow; tránh để trạng thái mâu thuẫn với docs 01/03 |
| [13 — Admin Portal](13-admin-portal-screen-specifications.md) | Giao diện cấu hình quản trị | Phạm vi màn hình gọn, phụ thuộc quyết định quyền/cấu hình | Chốt cấu hình nào thuộc MVP và audit trail |
| [14 — Decision Register](14-business-questions-and-decision-register.md) | Câu hỏi và phê duyệt nghiệp vụ | Có nhóm câu hỏi và checklist; mọi OPEN cần người duyệt | Dùng làm nguồn chuẩn cho quyết định; không tạo thêm danh sách song song |

## 3. Phát hiện và hành động đề xuất

### AUD-01 — Quyết định P0 chưa được khóa

- Mức độ: **P0 — Release blocker**
- Phát hiện: Tài liệu 01–06 và 12–14 cho thấy nhiều nội dung phụ thuộc quyết định chưa được duyệt.
- Rủi ro: Dev tự suy diễn chính sách; cùng một trạng thái có thể được xử lý khác nhau ở UI, API và Ticket.
- Hành động:
  1. Dùng docs/14 làm nguồn chuẩn.
  2. Với từng quyết định P0, ghi mã câu hỏi, người phê duyệt, ngày, quyết định cuối cùng và tác động đến tài liệu.
  3. Khi được duyệt, cập nhật đồng thời docs/01, 02, 03, 04, 05, 06 và các tài liệu UI liên quan.
- Điều kiện đóng: Không còn P0 OPEN nào ảnh hưởng đến luồng MVP; nếu còn ngoại lệ thì có chấp thuận rủi ro bằng văn bản.

### AUD-02 — Chưa có ma trận truy vết xuyên suốt

- Mức độ: **P0 — Release blocker**
- Phát hiện: Các tài liệu có liên kết và một số traceability, nhưng chưa có một ma trận master chứng minh mọi yêu cầu có thiết kế và test tương ứng.
- Rủi ro: Yêu cầu bị bỏ sót, test không kiểm tra đúng quy tắc hoặc thay đổi nghiệp vụ không được phản ánh đầy đủ.
- Hành động: Tạo ma trận có các cột:
  `Decision ID`, `Requirement ID`, `Flow/State ID`, `API/Entity`, `Screen ID`, `Test Case ID`, `Status`, `Owner`, `Evidence/Defect`.
- Điều kiện đóng: Mỗi requirement MUST có tiêu chí chấp nhận kiểm thử được; yêu cầu không áp dụng phải ghi lý do; không có requirement MUST không có test hoặc kiểm soát thay thế được duyệt.

### AUD-03 — Bitrix24 Ticket chưa thể chốt contract cuối cùng

- Mức độ: **P0 — Release blocker cho tích hợp production**
- Phát hiện: Tài liệu hiện xác định rõ đối tượng Ticket, field/status mapping và quyền API Bitrix24 cần xác minh; chưa nên giả định entity type.
- Rủi ro: Tạo sai loại đối tượng, trùng Ticket, sai trạng thái hoặc không thể đối soát sau timeout.
- Hành động:
  1. Xác minh tenant Bitrix24, loại đối tượng thực tế và API khả dụng.
  2. Chốt trường bắt buộc, enum/status mapping, quyền token, URL deep-link và quy tắc liên kết Conversation ↔ Ticket.
  3. Xác định idempotency key và cơ chế tra cứu sau timeout trước khi retry create.
  4. Kiểm thử lỗi mạng, API rate limit, validation error, token hết hạn và kết quả không xác định.
- Điều kiện đóng: Có mapping được duyệt, test trên môi trường phù hợp và runbook xử lý lỗi/đối soát.

### AUD-04 — State model cần một nguồn chuẩn

- Mức độ: **P0**
- Phát hiện: Trạng thái Conversation, Ticket và integration sync xuất hiện ở nhiều tài liệu.
- Rủi ro: UI hiển thị một trạng thái nhưng backend hoặc Bitrix24 lại coi là trạng thái khác.
- Hành động:
  1. Chọn docs/03 làm bảng chuyển trạng thái chuẩn; docs/12 trình bày master lifecycle.
  2. Định nghĩa mỗi trạng thái: ý nghĩa, tác nhân được phép chuyển, điều kiện vào/ra, trạng thái kế tiếp hợp lệ và event phát sinh.
  3. Tách rõ Conversation status, Ticket status, assignment status và integration sync status.
  4. Không coi `RESOLVED` là `CLOSED` nếu chưa có chính sách xác nhận.
- Điều kiện đóng: State diagram/bảng chuyển trạng thái được PO/Tech Lead duyệt và có test cho từng chuyển trạng thái trọng yếu.

### AUD-05 — Handoff và assignment cần xử lý hết các nhánh thất bại

- Mức độ: **P0**
- Phát hiện: Luồng AI → người thật và ma trận nhân sự đã có, nhưng điều kiện availability, fallback và ownership còn cần xác nhận.
- Rủi ro: Chat bị treo trong hàng chờ, giao sai người, hoặc mất trách nhiệm khi chuyển nhóm.
- Hành động: Chốt rõ queue owner, nhân viên đủ điều kiện, capacity, ca trực nếu có, timeout chờ, khách rời đi, agent offline, chuyển nhóm, từ chối nhận, chuyển lại và fallback Triage. Không tự phân công ngẫu nhiên khi thiếu mapping.
- Điều kiện đóng: Mỗi trường hợp có owner chịu trách nhiệm và trạng thái cuối rõ ràng; có test cho mapping thiếu và không có người khả dụng.

### AUD-06 — Bảo mật cần được kiểm thử như yêu cầu đầu cuối

- Mức độ: **P0**
- Phát hiện: Docs 02, 05, 09, 12 đã có nguyên tắc quyền server-side, tenant boundary và internal note; cần chứng minh qua test.
- Rủi ro: Lộ hội thoại giữa khách hàng/tổ chức, lộ ghi chú nội bộ, lạm dụng URL hoặc API trực tiếp.
- Hành động: Bổ sung test cho truy cập chéo tenant, thay ID trên request, session hết hạn, role không đủ quyền, URL Ticket không được phép, internal note qua API/notification, file không hợp lệ và audit event.
- Điều kiện đóng: Kiểm soát quyền được thực hiện ở server; test âm tính có bằng chứng; không chỉ dựa vào ẩn nút trên UI.

### AUD-07 — API contract chưa phải hợp đồng triển khai cuối

- Mức độ: **P0 trước khi nhiều đội triển khai song song; P1 cho prototype**
- Phát hiện: Docs/06 được ghi là draft; chưa phải OpenAPI contract được duyệt.
- Rủi ro: Frontend/backend hiểu khác nhau về schema, lỗi, auth, retry và pagination.
- Hành động: Chốt OpenAPI có request/response schema, field required/optional, validation, error format, authentication/authorization, idempotency, pagination, rate limiting, versioning và correlation/request ID. Quy định chuẩn timestamp/timezone và quy tắc xóa/sửa dữ liệu.
- Điều kiện đóng: Contract được review, có ví dụ thành công/thất bại và dùng làm nguồn chuẩn cho implementation/test.

### AUD-08 — Bộ acceptance test cần mở rộng

- Mức độ: **P0 cho các đường đi trọng yếu; P1 cho các nhánh phụ**
- Phát hiện: Docs/04 hiện có 26 test case hữu ích nhưng cần bảo đảm kiểm thử cả tính nhất quán, concurrency và recovery.
- Hành động đề xuất thêm:
  - Hai lần gửi cùng Idempotency-Key không tạo hai message/Ticket.
  - Hai agent đồng thời nhận hoặc chuyển cùng Conversation.
  - Khách gửi message trong lúc Conversation đang được đóng.
  - Retry notification không gửi lặp không kiểm soát.
  - Bitrix24 tạo Ticket thành công nhưng response timeout; reconciliation tìm lại Ticket thay vì tạo trùng.
  - Webhook/event đến lặp hoặc sai thứ tự.
  - Nhân viên mất quyền khi đang mở Conversation.
  - Session hết hạn khi gửi tin.
  - Rate limit, payload quá lớn, file bị từ chối và lỗi upload.
  - Worker/job restart trong lúc xử lý; job được tiếp tục hoặc đối soát.
  - Đồng hồ SLA qua ngoài giờ làm việc/ngày nghỉ theo policy đã duyệt.
  - Đóng rồi mở lại theo đúng cửa sổ và quyền đã duyệt.
- Điều kiện đóng: Mỗi test có tiền điều kiện, bước thực hiện, expected result, môi trường, bằng chứng và defect ID; test chính sách OPEN không được coi là final.

### AUD-09 — UI/UX còn ở trạng thái đề xuất

- Mức độ: **P1 — cần trước UI production**
- Phát hiện: Docs/07–13 và SVG được mô tả là proposal/concept.
- Rủi ro: Nhầm dữ liệu minh họa với dữ liệu thật hoặc xây UI dựa trên chính sách chưa được duyệt.
- Hành động: Duyệt brand, copy, browser target/extension mode, responsive, accessibility, loading/empty/error/denied/offline states, realtime behavior, notification behavior và deep link Ticket.
- Điều kiện đóng: PO/Design/Tech Lead duyệt các quyết định liên quan; mọi giá trị minh họa được thay bằng dữ liệu API hoặc placeholder có chủ đích.

### AUD-10 — NFR cần có tiêu chí đo lường

- Mức độ: **P1**
- Phát hiện: Docs/02 có phần yêu cầu phi chức năng, nhưng release gate cần ngưỡng đo được và được phê duyệt.
- Hành động: Chốt các mục áp dụng cho MVP: hiệu năng gửi/nhận message, thời gian phản hồi API, số kết nối đồng thời, độ sẵn sàng, backup/restore, RPO/RTO, retention, observability, alerting và mức hỗ trợ trình duyệt. Không tự điền ngưỡng khi chưa có mục tiêu sản phẩm/hạ tầng.
- Điều kiện đóng: Mỗi NFR có metric, ngưỡng, phương pháp đo, môi trường test và người duyệt.

### AUD-11 — Vận hành lỗi cần runbook tối thiểu

- Mức độ: **P1**
- Phát hiện: Docs/05 và 12 có error/retry/recovery; cần chuyển thành quy trình vận hành có thể thực hiện.
- Hành động: Viết runbook cho Bitrix24 unavailable, queue/job backlog, AI provider unavailable, notification failure, token hết hạn, reconciliation backlog và dữ liệu không đồng bộ. Chỉ rõ cảnh báo, người nhận, bước chẩn đoán, thao tác an toàn và điều kiện đóng incident.
- Điều kiện đóng: Mỗi lỗi P0/P1 có alert hoặc cách phát hiện, owner, hướng khôi phục và cách kiểm tra dữ liệu sau phục hồi.

### AUD-12 — Quy tắc thay đổi và quản lý phiên bản tài liệu

- Mức độ: **P1**
- Phát hiện: Docs/14 có mẫu quyết định và lịch sử thay đổi; chưa có một quy trình bắt buộc thể hiện tác động xuyên tài liệu.
- Hành động: Mỗi thay đổi quyết định phải ghi ngày, người duyệt, lý do, requirement bị ảnh hưởng, screen/API/test cần cập nhật và phiên bản release mục tiêu.
- Điều kiện đóng: Không merge thay đổi nghiệp vụ làm lệch requirements, API và test; quyết định cũ vẫn truy vết được.

## 4. Phạm vi MVP — xác nhận trước khi khóa

Đây là **khung đề xuất để PO xác nhận**, không phải quyết định đã duyệt.

### Phần lõi nên có trong MVP
- Mở/tiếp tục Conversation theo chính sách danh tính đã duyệt.
- Gửi/nhận và lưu Message bền vững; hiển thị trạng thái gửi.
- AI First Response với hành vi fallback rõ ràng.
- Handoff sang hàng chờ/người thật, có ownership và xử lý không khả dụng.
- Dashboard cho agent xử lý chat và trao đổi nội bộ an toàn.
- Tạo/liên kết Ticket Bitrix24 theo contract được xác minh.
- Trạng thái, SLA tối thiểu và cảnh báo theo policy đã duyệt.
- Đóng/giải quyết/mở lại theo quy tắc đã duyệt.
- Phân quyền server-side, tenant isolation và audit log cho thao tác nhạy cảm.
- Retry/idempotency/reconciliation cho các thao tác có side effect.
- Cấu hình tối thiểu cho mapping nhóm/nhân sự và giám sát lỗi tích hợp.
- Bộ acceptance test và runbook tối thiểu cho các luồng quan trọng.

### Chỉ đưa vào MVP nếu có nhu cầu được xác nhận
- Upload nhiều loại file, quét malware và preview.
- Đa ngôn ngữ, dark mode, hỗ trợ trình duyệt bổ sung.
- Báo cáo nâng cao, phân tích hiệu quả AI và dashboard tùy biến.
- CSAT/NPS nâng cao, survey nhiều bước.
- Quản trị tri thức AI với workflow phê duyệt phức tạp.
- Worklog/chi phí tính phí hoặc các chức năng quản lý dịch vụ ngoài luồng chat cốt lõi.

Không đưa quản lý hợp đồng, chấm công, payroll hay ITSM tổng thể vào phạm vi chỉ vì chúng có liên quan gián tiếp đến vận hành hỗ trợ.

## 5. Ma trận truy vết — mẫu bắt buộc

Tạo và duy trì ma trận này khi chuyển từ audit sang delivery. Điền mã chính xác sau khi quyết định và requirements được xác nhận.

| Decision ID | Requirement ID | Flow/State | API/Entity | Screen | Test | Trạng thái |
|---|---|---|---|---|---|---|
| BIZ-002 / BIZ-003 | FR-CHAT / FR-AUTH tương ứng | Intake, identity, conversation lifecycle | Conversation, Message | EXT-01/02 và màn hình liên quan | TC-001/002 cùng test bổ sung | Cần đối chiếu từng requirement |
| BIZ-005 / BIZ-004 | FR-TICKET tương ứng | Ticket create/link/update/reconcile | Ticket Link, Integration Job | SUP-03/SUP-05 và Ticket card | TC-013–016 cùng test bổ sung | Chờ xác minh Bitrix24 |
| BIZ-006 | FR-ROUTE / FR-ASSIGN tương ứng | Handoff, queue, assignment | Assignment Rule, Handoff | SUP-01/SUP-04 | TC-007–012/018/026 | Chờ chốt matrix |
| Quyết định SLA trong docs/14 | FR-SLA | SLA start/pause/escalation | SLA policy/event | Dashboard/operations | TC-021 cùng test lịch/ngoài giờ | OPEN cho đến khi duyệt |
| Quyết định quyền trong docs/14 | FR-AUTH / security requirements | Authorization/audit | User, Role, Tenant scope, Audit Event | Tất cả màn hình được bảo vệ | TC-023/024 cùng test âm tính | Cần bổ sung bằng chứng |
| Quyết định closure trong docs/14 | FR-CHAT / resolution requirements | Resolve/close/reopen | Conversation, feedback | EXT feedback, SUP-06 | TC-019/020 cùng test bổ sung | Chờ chốt policy |

**Quy tắc:** Không dùng tên module thay cho ID requirement cụ thể khi nghiệm thu. Ma trận này là ví dụ định hướng; phải điền tất cả requirement ID chính xác trước release sign-off.

## 6. Release gates đề xuất

### Gate 1 — Business Ready
- [ ] Tất cả quyết định P0 có người duyệt và ngày duyệt.
- [ ] State model và decision tables thống nhất.
- [ ] MVP scope và ngoài phạm vi được xác nhận.
- [ ] Không còn yêu cầu MUST mơ hồ hoặc mâu thuẫn.

### Gate 2 — Design/API Ready
- [ ] OpenAPI/Data Contract được review.
- [ ] Bitrix24 object/fields/status/permissions được xác minh.
- [ ] Mọi màn hình MVP có role, data scope, state và error behavior.
- [ ] NFR và chính sách file/auth/realtime được chốt hoặc loại khỏi MVP có chủ đích.

### Gate 3 — QA Ready
- [ ] Ma trận truy vết requirement → test đầy đủ.
- [ ] Test cho luồng thành công, lỗi, retry, concurrency và quyền.
- [ ] Dữ liệu test không chứa thông tin khách hàng thật ngoài phạm vi được phép.
- [ ] Môi trường, build/version, test owner và tiêu chí PASS/FAIL rõ ràng.

### Gate 4 — Production Ready
- [ ] Không còn defect blocker.
- [ ] Retry/idempotency/reconciliation đã kiểm thử trên tích hợp thực tế.
- [ ] Monitoring, alerting, backup/restore và runbook được xác nhận theo phạm vi triển khai.
- [ ] Quyền, audit, tenant isolation và dữ liệu nhạy cảm đã được kiểm tra.
- [ ] PO, Tech Lead và QA ghi nhận sign-off theo trách nhiệm của mình.

## 7. Quy tắc xử lý các mục audit

Mỗi mục phải ở một trong các trạng thái:
- **OPEN** — chưa phân tích hoặc chưa có quyết định.
- **IN PROGRESS** — đang sửa tài liệu hoặc triển khai kiểm chứng.
- **BLOCKED** — cần quyết định, quyền truy cập hoặc hệ thống ngoài.
- **VERIFIED** — đã có bằng chứng kiểm tra.
- **ACCEPTED RISK** — người có thẩm quyền chấp nhận rủi ro, kèm lý do và ngày.
- **CLOSED** — đáp ứng điều kiện đóng và được xác nhận.

Không chuyển sang CLOSED chỉ vì đã viết tài liệu hoặc tạo commit. Cần bằng chứng kiểm tra hoặc phê duyệt tương ứng.

## 8. Thứ tự thực hiện đề xuất

1. PO xử lý các quyết định P0 trong docs/14.
2. Tech Lead/PO xác minh Bitrix24 và chốt data contract.
3. Đồng bộ state model và bảng quyết định.
4. Tạo ma trận truy vết hoàn chỉnh.
5. Bổ sung acceptance tests còn thiếu.
6. Duyệt màn hình và các quyết định UI/UX thuộc MVP.
7. Chốt NFR, runbook và release gates.
8. Chỉ sau đó mới đánh dấu baseline production-ready.

## 9. Kết luận

Bộ tài liệu hiện tại đã có nền tảng tốt để tiếp tục phân tích và triển khai prototype, nhưng không nên tuyên bố hoàn chỉnh hoặc production-ready khi quyết định P0, contract Bitrix24, traceability và test coverage chưa được đóng.

Mục tiêu của audit là giữ Flow Support Chat tập trung vào chat hỗ trợ khách hàng, AI handoff, nhân viên hỗ trợ, Ticket Bitrix24 và vận hành an toàn — không mở rộng thành một hệ thống quản lý dịch vụ tổng thể.

## 10. Lịch sử thay đổi

| Phiên bản | Ngày | Nội dung |
|---|---|---|
| 1.0 | 2026-10-20 | Tạo audit baseline cho tài liệu 01–14; ghi nhận phạm vi, rủi ro, hành động, MVP đề xuất và release gates. |
