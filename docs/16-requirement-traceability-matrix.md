# Flow Support Chat — Requirement Traceability Matrix

- Mã tài liệu: PM-TRACE-001
- Phiên bản: 1.0
- Ngày lập: 2026-10-20
- Trạng thái: **BASELINE — cần QA/PO xác minh mapping**
- Nguồn yêu cầu: [02 — Functional Requirements](02-functional-requirements.md)
- Nguồn test hiện có: [04 — Acceptance Test Scenarios](04-acceptance-test-scenarios.md)
- Decision Register: [14 — Business Questions and Decision Register](14-business-questions-and-decision-register.md)
- Audit: [15 — Business Completeness Audit](15-business-completeness-audit.md)

## 1. Mục đích

Tạo danh mục tập trung cho **73 requirement ID hiện có** để PM, Dev và QA quản lý truy vết. Đây là ma trận khởi tạo, không phải bằng chứng mọi requirement đã được kiểm thử hoặc nghiệm thu.

- Cột “Test case hiện có — ứng viên” chỉ gợi ý các test có thể liên quan theo module; QA phải xác minh từng expected result trước khi xác nhận traceability.
- Requirement có trạng thái OPEN hoặc acceptance ghi OPEN không được coi là đã chốt.
- Các cột API/Screen/Owner/Evidence cần được điền khi contract và thiết kế được duyệt; không tự suy ra ID chưa có.
- Nếu một requirement không áp dụng cho MVP, phải ghi rõ quyết định scope, người duyệt và ngày duyệt.

## 2. Tình trạng nguồn

- Requirement ID được đọc từ bảng yêu cầu chức năng trong docs/02.
- Có 73 requirement ID được lập chỉ mục trong phiên bản nguồn được rà soát.
- Bộ test hiện có gồm 26 test case, TC-001 đến TC-026.
- Chưa có bằng chứng trong tài liệu cho thấy toàn bộ 73 requirements đã được gắn test case cụ thể và thực thi; cần QA hoàn tất mapping và ghi kết quả chạy thực tế.

## 3. Ma trận requirement

### CHAT — Chat và vòng đời Conversation

| Requirement ID | Mức hiện tại | Yêu cầu | Tiêu chí/ghi chú nguồn | Test case hiện có — ứng viên | Trạng thái truy vết |
|---|---|---|---|---|---|
| FR-CHAT-001 | MUST | Hệ thống phải tạo định danh duy nhất cho mỗi Conversation. | Mỗi phiên có thể được tham chiếu từ Message và Ticket. | TC-001, TC-002, TC-017, TC-019, TC-020, TC-022 | Cần QA xác minh |
| FR-CHAT-002 | MUST | Hệ thống phải gắn Conversation với danh tính đã xác thực và phạm vi khách hàng/đơn vị. | Không cho truy cập lịch sử của tổ chức khác. | TC-001, TC-002, TC-017, TC-019, TC-020, TC-022 | Cần QA xác minh |
| FR-CHAT-003 | MUST | Hệ thống phải lưu Message cùng tác giả, thời điểm, loại nội dung và trạng thái xử lý. | Phân biệt khách hàng, AI, nhân viên và sự kiện hệ thống. | TC-001, TC-002, TC-017, TC-019, TC-020, TC-022 | Cần QA xác minh |
| FR-CHAT-004 | MUST | Hệ thống phải hiển thị lịch sử theo quyền và thứ tự thời gian. | Không hiển thị ghi chú nội bộ như tin nhắn công khai. | TC-001, TC-002, TC-017, TC-019, TC-020, TC-022 | Cần QA xác minh |
| FR-CHAT-005 | MUST | Nếu người dùng mất kết nối hoặc đóng Extension, Message đã được xác nhận lưu không được mất. | Khi mở lại, tải được lịch sử theo quyền. | TC-001, TC-002, TC-017, TC-019, TC-020, TC-022 | Cần QA xác minh |
| FR-CHAT-006 | MUST | Hệ thống phải thể hiện trạng thái gửi tin nhắn. | Ít nhất: đang gửi, đã gửi, lỗi gửi; retry không nhân đôi nội dung. | TC-001, TC-002, TC-017, TC-019, TC-020, TC-022 | Cần QA xác minh |
| FR-CHAT-007 | SHOULD | Hỗ trợ file đính kèm theo loại, dung lượng và quyền được cấu hình. | Từ chối file không hợp lệ; ghi nhận lỗi cho người dùng. | TC-001, TC-002, TC-017, TC-019, TC-020, TC-022 | Cần QA xác minh |
| FR-CHAT-008 | MUST | Hệ thống phải cho phép tiếp tục Conversation cũ hoặc tạo Conversation mới theo chính sách. | OPEN: điều kiện phân biệt hội thoại tiếp tục và hội thoại mới. | TC-001, TC-002, TC-017, TC-019, TC-020, TC-022 | Cần QA xác minh |
| FR-CHAT-009 | MUST | Hệ thống phải có cách thể hiện rằng khách đang nói chuyện với AI hay nhân viên. | Không gây hiểu nhầm phản hồi AI là phản hồi của nhân viên. | TC-001, TC-002, TC-017, TC-019, TC-020, TC-022 | Cần QA xác minh |
| FR-CHAT-010 | MUST | Hệ thống phải bảo vệ chống gửi trùng do người dùng bấm nhiều lần hoặc retry mạng. | Tin nhắn logic không xuất hiện lặp ngoài ý muốn. | TC-001, TC-002, TC-017, TC-019, TC-020, TC-022 | Cần QA xác minh |

### AUTH — Danh tính, phân quyền và bảo mật

| Requirement ID | Mức hiện tại | Yêu cầu | Tiêu chí/ghi chú nguồn | Test case hiện có — ứng viên | Trạng thái truy vết |
|---|---|---|---|---|---|
| FR-AUTH-001 | MUST | Hệ thống phải xác minh danh tính trước khi truy cập dữ liệu khách hàng riêng. | Chưa xác thực chỉ được dùng luồng cho phép công khai. | TC-001, TC-002, TC-023, TC-024 | Cần QA xác minh |
| FR-AUTH-002 | MUST | Mọi thao tác đọc/sửa Conversation, Ticket liên kết, file và cấu hình phải kiểm tra quyền ở phía server. | Không chỉ dựa vào ẩn/hiện nút trên Extension. | TC-001, TC-002, TC-023, TC-024 | Cần QA xác minh |
| FR-AUTH-003 | MUST | Nhân viên chỉ xem dữ liệu trong phạm vi khách hàng/nhóm được cấp quyền. | Truy cập trái quyền bị từ chối và được ghi nhận. | TC-001, TC-002, TC-023, TC-024 | Cần QA xác minh |
| FR-AUTH-004 | MUST | Ghi chú nội bộ và nội dung dành cho nhân viên phải được phân biệt với tin nhắn khách hàng. | Không đồng bộ ghi chú nội bộ ra kênh công khai. | TC-001, TC-002, TC-023, TC-024 | Cần QA xác minh |
| FR-AUTH-005 | MUST | Bí mật AI/Bitrix24 phải được giữ phía server hoặc kho bí mật được bảo vệ. | Không nhúng secret dài hạn vào gói Extension. | TC-001, TC-002, TC-023, TC-024 | Cần QA xác minh |
| FR-AUTH-006 | SHOULD | Hỗ trợ thu hồi phiên/token và yêu cầu xác thực lại. | Phiên hết hạn không được tiếp tục truy cập dữ liệu. | TC-001, TC-002, TC-023, TC-024 | Cần QA xác minh |

### AI — AI First Response và handoff

| Requirement ID | Mức hiện tại | Yêu cầu | Tiêu chí/ghi chú nguồn | Test case hiện có — ứng viên | Trạng thái truy vết |
|---|---|---|---|---|---|
| FR-AI-001 | MUST | AI phải tiếp nhận nội dung Conversation và ngữ cảnh được phép sử dụng. | Không lấy dữ liệu ngoài phạm vi quyền. | TC-003, TC-004, TC-005, TC-006 | Cần QA xác minh |
| FR-AI-002 | MUST | AI phải phân biệt dữ kiện đã biết và thông tin còn thiếu. | Có thể hỏi bổ sung trước khi trả lời. | TC-003, TC-004, TC-005, TC-006 | Cần QA xác minh |
| FR-AI-003 | MUST | AI phải tra cứu nguồn tri thức được phê duyệt khi loại yêu cầu yêu cầu tra cứu. | Nếu không có nguồn phù hợp, không bịa câu trả lời như sự thật. | TC-003, TC-004, TC-005, TC-006 | Cần QA xác minh |
| FR-AI-004 | MUST | AI phải trả lời bằng ngôn ngữ phù hợp với khách hàng và nêu bước thực hiện rõ ràng. | Lưu được câu trả lời đã gửi. | TC-003, TC-004, TC-005, TC-006 | Cần QA xác minh |
| FR-AI-005 | MUST | Hệ thống phải cho phép khách hàng yêu cầu nhân viên theo luồng được cấu hình. | Không bắt buộc khách tiếp tục hỏi AI. | TC-003, TC-004, TC-005, TC-006 | Cần QA xác minh |
| FR-AI-006 | MUST | AI phải bàn giao tóm tắt và dữ kiện đã thu thập. | Nhân viên không cần yêu cầu khách lặp lại dữ liệu đã có. | TC-003, TC-004, TC-005, TC-006 | Cần QA xác minh |
| FR-AI-007 | MUST | Hệ thống phải hỗ trợ các điều kiện handoff cấu hình được. | Khách yêu cầu người thật, AI không đủ căn cứ, vấn đề nhạy cảm, nguy cơ SLA. | TC-003, TC-004, TC-005, TC-006 | Cần QA xác minh |
| FR-AI-008 | MUST | AI không được tự thực hiện thao tác quản trị/nhạy cảm nếu chưa có quyền và quy trình phê duyệt. | Handoff đến người có thẩm quyền. | TC-003, TC-004, TC-005, TC-006 | Cần QA xác minh |
| FR-AI-009 | SHOULD | AI nên ghi nhận nguồn tri thức tham khảo hoặc phiên bản nguồn dùng để trả lời. | Phục vụ truy vết chất lượng câu trả lời. | TC-003, TC-004, TC-005, TC-006 | Cần QA xác minh |
| FR-AI-010 | OPEN | Cấu hình nhà cung cấp AI và fallback khi nhà cung cấp lỗi. | Cần thống nhất provider ưu tiên, Ollama/external API và chính sách fallback. | TC-003, TC-004, TC-005, TC-006 | Cần QA xác minh |
| FR-AI-011 | OPEN | Ngưỡng confidence, số vòng hỏi và thời gian chờ khách hàng. | Không tự chốt con số trong code. | TC-003, TC-004, TC-005, TC-006 | Cần QA xác minh |
| FR-AI-012 | MUST | Khi AI lỗi/timeout, hệ thống phải cho phép thử lại có giới hạn hoặc handoff. | Không để Conversation bị treo vô thời hạn. | TC-003, TC-004, TC-005, TC-006 | Cần QA xác minh |

### ROUTE — Phân loại và định tuyến

| Requirement ID | Mức hiện tại | Yêu cầu | Tiêu chí/ghi chú nguồn | Test case hiện có — ứng viên | Trạng thái truy vết |
|---|---|---|---|---|---|
| FR-ROUTE-001 | MUST | Hệ thống phải hỗ trợ nhóm Technical, Sales, HR, Other và Triage/Unclassified. | Danh sách nhóm có thể cấu hình. | TC-007, TC-008, TC-009, TC-010, TC-018 | Cần QA xác minh |
| FR-ROUTE-002 | MUST | AI có thể đề xuất nhóm; Routing Engine phải áp dụng quy tắc được phê duyệt. | Không giao người chỉ dựa vào suy đoán tự do của AI. | TC-007, TC-008, TC-009, TC-010, TC-018 | Cần QA xác minh |
| FR-ROUTE-003 | MUST | Nếu phân loại mơ hồ hoặc xung đột, chuyển Triage. | Có hàng đợi và người/nhóm chịu trách nhiệm phân loại. | TC-007, TC-008, TC-009, TC-010, TC-018 | Cần QA xác minh |
| FR-ROUTE-004 | MUST | Khi người có quyền sửa nhóm thủ công, hệ thống phải ghi nhóm trước/sau, người sửa, thời gian và lý do. | Truy vết được phân loại sai. | TC-007, TC-008, TC-009, TC-010, TC-018 | Cần QA xác minh |
| FR-ROUTE-005 | MUST | Phải giữ nguyên lịch sử và Ticket ID khi chuyển nhóm. | Không tạo Conversation/Ticket mới chỉ vì chuyển nhóm. | TC-007, TC-008, TC-009, TC-010, TC-018 | Cần QA xác minh |
| FR-ROUTE-006 | OPEN | Quy tắc xử lý một Message có nhiều yêu cầu độc lập. | Nghiệp vụ xác nhận giữ chung hay tách Ticket. | TC-007, TC-008, TC-009, TC-010, TC-018 | Cần QA xác minh |

### ASSIGN — Phân công nhân sự

| Requirement ID | Mức hiện tại | Yêu cầu | Tiêu chí/ghi chú nguồn | Test case hiện có — ứng viên | Trạng thái truy vết |
|---|---|---|---|---|---|
| FR-ASSIGN-001 | MUST | Có cấu hình mapping khách hàng + nhóm/dịch vụ → Primary, người xử lý chuyên môn, Backup và cấp escalation. | Mapping có trạng thái hiệu lực. | TC-011, TC-012, TC-018, TC-026 | Cần QA xác minh |
| FR-ASSIGN-002 | MUST | Chỉ gán nhân sự còn hoạt động và có quyền phù hợp. | Không gán người nghỉ việc/vô hiệu hóa hoặc ngoài phạm vi. | TC-011, TC-012, TC-018, TC-026 | Cần QA xác minh |
| FR-ASSIGN-003 | MUST | Nếu Primary không khả dụng, áp dụng Backup/escalation theo cấu hình. | Không âm thầm chọn người bất kỳ. | TC-011, TC-012, TC-018, TC-026 | Cần QA xác minh |
| FR-ASSIGN-004 | MUST | Nếu không có mapping hợp lệ, chuyển Triage/Unassigned và cảnh báo. | Có người chịu trách nhiệm xử lý ngoại lệ. | TC-011, TC-012, TC-018, TC-026 | Cần QA xác minh |
| FR-ASSIGN-005 | MUST | Ghi lịch sử mỗi lần gán/chuyển người. | Có người thực hiện, thời điểm và lý do. | TC-011, TC-012, TC-018, TC-026 | Cần QA xác minh |
| FR-ASSIGN-006 | MUST | Cho phép Primary khác người xử lý chuyên môn. | Sales/HR không mặc định đi vào Primary kỹ thuật. | TC-011, TC-012, TC-018, TC-026 | Cần QA xác minh |
| FR-ASSIGN-007 | OPEN | Điều kiện kích hoạt Backup: nghỉ, quá tải, không nhận việc hay quá thời gian phản hồi. | Cần định nghĩa rule cụ thể. | TC-011, TC-012, TC-018, TC-026 | Cần QA xác minh |
| FR-ASSIGN-008 | OPEN | Một Ticket có thể có nhiều người xử lý đồng thời hay chỉ một assignee chính. | Chốt theo khả năng đối tượng Bitrix24. | TC-011, TC-012, TC-018, TC-026 | Cần QA xác minh |

### TICKET — Ticket và Bitrix24

| Requirement ID | Mức hiện tại | Yêu cầu | Tiêu chí/ghi chú nguồn | Test case hiện có — ứng viên | Trạng thái truy vết |
|---|---|---|---|---|---|
| FR-TICKET-001 | MUST | Conversation và Ticket phải có liên kết bền vững. | Có thể mở từ hai phía theo quyền. | TC-013, TC-014, TC-015, TC-016, TC-017 | Cần QA xác minh |
| FR-TICKET-002 | MUST | Trước khi tạo Ticket phải kiểm tra liên kết hiện hữu và áp dụng quy tắc chống trùng. | Retry không tạo Ticket trùng. | TC-013, TC-014, TC-015, TC-016, TC-017 | Cần QA xác minh |
| FR-TICKET-003 | MUST | Chỉ xác nhận Ticket đã tạo sau khi có xác nhận thành công từ Bitrix24. | Không hiển thị thành công giả. | TC-013, TC-014, TC-015, TC-016, TC-017 | Cần QA xác minh |
| FR-TICKET-004 | MUST | Phải ghi nhận trạng thái đồng bộ riêng với trạng thái nghiệp vụ Ticket. | Lỗi tích hợp không làm sai trạng thái công việc. | TC-013, TC-014, TC-015, TC-016, TC-017 | Cần QA xác minh |
| FR-TICKET-005 | MUST | Timeout khi tạo Ticket phải đưa vào đối chiếu trước khi retry. | Xử lý kết quả không rõ ràng. | TC-013, TC-014, TC-015, TC-016, TC-017 | Cần QA xác minh |
| FR-TICKET-006 | MUST | Lỗi quyền/validation phải được ghi nhận và đưa ra xử lý; không retry vô hạn. | Có thông tin để vận hành khắc phục. | TC-013, TC-014, TC-015, TC-016, TC-017 | Cần QA xác minh |
| FR-TICKET-007 | MUST | Phải có mapping trạng thái nghiệp vụ sang trạng thái thực tế của Bitrix24. | Mapping được kiểm thử. | TC-013, TC-014, TC-015, TC-016, TC-017 | Cần QA xác minh |
| FR-TICKET-008 | MUST | Thông tin tạo Ticket phải gồm mã hội thoại, khách hàng, nội dung, nhóm và thông tin phân công theo mapping đã chốt. | Field bắt buộc phụ thuộc đối tượng Bitrix24. | TC-013, TC-014, TC-015, TC-016, TC-017 | Cần QA xác minh |
| FR-TICKET-009 | OPEN | Chọn đối tượng Bitrix24: Task, CRM, Smart Process hoặc module Ticket hiện có. | Không thể chốt field/API trước quyết định này. | TC-013, TC-014, TC-015, TC-016, TC-017 | Cần QA xác minh |
| FR-TICKET-010 | OPEN | Quy tắc tách/gộp Ticket và nhận diện Ticket hiện hữu. | Cần chốt khóa đối chiếu và điều kiện nghiệp vụ. | TC-013, TC-014, TC-015, TC-016, TC-017 | Cần QA xác minh |
| FR-TICKET-011 | MUST | Nội dung nội bộ và nội dung công khai phải được phân biệt. | Không lộ note nội bộ cho khách hàng. | TC-013, TC-014, TC-015, TC-016, TC-017 | Cần QA xác minh |

### STAFF — Quy trình nhân viên hỗ trợ

| Requirement ID | Mức hiện tại | Yêu cầu | Tiêu chí/ghi chú nguồn | Test case hiện có — ứng viên | Trạng thái truy vết |
|---|---|---|---|---|---|
| FR-STAFF-001 | MUST | Nhân viên phải xem được gói bàn giao và lịch sử liên quan. | Không yêu cầu khách nhắc lại thông tin đã có. | TC-018, TC-019, TC-020, TC-024 | Cần QA xác minh |
| FR-STAFF-002 | MUST | Nhân viên phải xác nhận tiếp nhận hoặc trạng thái nhận việc theo quy trình. | Hệ thống biết Ticket đã có người nhận. | TC-018, TC-019, TC-020, TC-024 | Cần QA xác minh |
| FR-STAFF-003 | MUST | Cập nhật tiến độ/kết quả phải đồng bộ theo quy tắc Bitrix24. | Có lịch sử và người thực hiện. | TC-018, TC-019, TC-020, TC-024 | Cần QA xác minh |
| FR-STAFF-004 | MUST | Chuyển nhóm/người phải có lý do và giữ lịch sử. | Tránh Ticket bị bỏ rơi. | TC-018, TC-019, TC-020, TC-024 | Cần QA xác minh |
| FR-STAFF-005 | MUST | Chờ khách hàng/bên thứ ba phải được biểu diễn rõ. | Hỗ trợ theo dõi và tính SLA theo chính sách. | TC-018, TC-019, TC-020, TC-024 | Cần QA xác minh |
| FR-STAFF-006 | OPEN | Nhân viên trả lời khách trực tiếp từ Extension hay qua Bitrix24. | Chốt kênh giao tiếp chính thức. | TC-018, TC-019, TC-020, TC-024 | Cần QA xác minh |

### NOTIFY — Thông báo

| Requirement ID | Mức hiện tại | Yêu cầu | Tiêu chí/ghi chú nguồn | Test case hiện có — ứng viên | Trạng thái truy vết |
|---|---|---|---|---|---|
| FR-NOTIFY-001 | MUST | Phát thông báo theo sự kiện đã được xác nhận. | Ví dụ: Ticket đã tạo, đã giao, cần bổ sung, đã giải quyết. | TC-022, TC-025 | Cần QA xác minh |
| FR-NOTIFY-002 | MUST | Retry thông báo phải có giới hạn và chống gửi trùng. | Một sự kiện logic không tạo spam. | TC-022, TC-025 | Cần QA xác minh |
| FR-NOTIFY-003 | MUST | Không gửi nội dung nội bộ/nhạy cảm cho khách hàng. | Kiểm tra quyền và loại thông báo. | TC-022, TC-025 | Cần QA xác minh |
| FR-NOTIFY-004 | MUST | Các công việc thông báo quan trọng phải chạy phía server/worker, không phụ thuộc Extension đang mở. | Không mất thông báo khi browser đóng. | TC-022, TC-025 | Cần QA xác minh |
| FR-NOTIFY-005 | OPEN | Kênh thông báo và ưu tiên kênh. | Chat, email, Bitrix notification hoặc kênh khác. | TC-022, TC-025 | Cần QA xác minh |

### SLA — Ưu tiên và SLA

| Requirement ID | Mức hiện tại | Yêu cầu | Tiêu chí/ghi chú nguồn | Test case hiện có — ứng viên | Trạng thái truy vết |
|---|---|---|---|---|---|
| FR-SLA-001 | MUST | Ưu tiên/SLA phải lấy từ cấu hình đã được duyệt. | Không hard-code thời hạn nghiệp vụ. | TC-019, TC-021 | Cần QA xác minh |
| FR-SLA-002 | MUST | Cần phân biệt thời hạn phản hồi đầu tiên và thời hạn giải quyết nếu chính sách có cả hai. | Báo cáo đúng loại SLA. | TC-019, TC-021 | Cần QA xác minh |
| FR-SLA-003 | MUST | Cảnh báo theo các mốc đã cấu hình và ghi nhận lịch sử. | Có thể kiểm tra sau sự kiện. | TC-019, TC-021 | Cần QA xác minh |
| FR-SLA-004 | OPEN | Lịch làm việc, ngày nghỉ, tạm dừng khi chờ khách/bên thứ ba, reset khi chuyển nhóm. | Cần chính sách chính thức. | TC-019, TC-021 | Cần QA xác minh |

### ADMIN — Quản trị và kiểm toán

| Requirement ID | Mức hiện tại | Yêu cầu | Tiêu chí/ghi chú nguồn | Test case hiện có — ứng viên | Trạng thái truy vết |
|---|---|---|---|---|---|
| FR-ADMIN-001 | MUST | Quản trị viên được quản lý nhóm hỗ trợ và ma trận phân công theo quyền. | Thay đổi không cần sửa code nếu nằm trong phạm vi cấu hình. | TC-012, TC-026 | Cần QA xác minh |
| FR-ADMIN-002 | MUST | Mapping phải có hiệu lực, trạng thái hoạt động và lịch sử thay đổi. | Tránh mapping cũ tiếp tục được dùng. | TC-012, TC-026 | Cần QA xác minh |
| FR-ADMIN-003 | MUST | Ghi audit cho thay đổi cấu hình, quyền, phân công và trạng thái quan trọng. | Có người, thời điểm, giá trị trước/sau và lý do khi cần. | TC-012, TC-026 | Cần QA xác minh |
| FR-ADMIN-004 | MUST | Có màn hình/khả năng tra cứu lỗi đồng bộ và yêu cầu đang chờ điều phối. | Vận hành có thể xử lý mà không truy cập trực tiếp database. | TC-012, TC-026 | Cần QA xác minh |
| FR-ADMIN-005 | OPEN | Vai trò quản trị và phạm vi quyền chi tiết. | Cần ma trận quyền được duyệt. | TC-012, TC-026 | Cần QA xác minh |

## 4. Trường cần hoàn thiện khi đi vào delivery

Đối với từng requirement, PM/QA cần bổ sung hoặc xác minh:

| Trường | Quy tắc |
|---|---|
| Decision ID | Mã BIZ tương ứng trong docs/14; nếu không có, ghi “chưa liên kết” để PO bổ sung |
| Flow/State ID | Luồng/state trong docs/01, 03 hoặc 12; ghi đúng tên/ID hiện có |
| API/Entity | Endpoint/DTO/entity từ docs/06 hoặc contract đã duyệt |
| Screen ID | EXT/SUP/ADM tương ứng trong docs/08, 10, 11, 13 |
| Test Case ID | Chỉ gắn khi expected result thật sự kiểm tra requirement |
| Owner | Người chịu trách nhiệm xác minh |
| Status | OPEN / IN PROGRESS / BLOCKED / VERIFIED / ACCEPTED RISK / CLOSED |
| Evidence | Link kết quả test, log, screenshot hoặc defect ID phù hợp |

## 5. Các điều kiện bắt buộc trước khi đánh dấu VERIFIED

1. Requirement được PO xác nhận hoặc trạng thái OPEN được giải quyết.
2. Tiêu chí chấp nhận có thể kiểm thử, không dùng từ mơ hồ như “nhanh”, “đúng”, “đủ” nếu chưa có định nghĩa.
3. Có test case cụ thể với precondition, steps và expected result.
4. Test đã chạy trên môi trường/build được ghi nhận, có PASS/FAIL và bằng chứng.
5. Các lỗi liên quan có defect ID; test thất bại không được đánh dấu VERIFIED.
6. Nếu requirement bị loại khỏi MVP, cần decision ID, approver và ngày quyết định.

## 6. Kiểm thử bổ sung cần tạo

Các test dưới đây chưa được tính là đã có trong TC-001–TC-026; QA cần tạo ID mới theo quy ước của dự án:

- Duplicate message và retry cùng idempotency key.
- Hai agent cùng nhận/chuyển một Conversation đồng thời.
- Event/webhook Bitrix24 lặp hoặc đến sai thứ tự.
- Ticket được tạo thành công ở Bitrix24 nhưng response timeout ở Flow Support Chat.
- Worker/job restart giữa chừng và recovery sau restart.
- Session hết hạn giữa lúc gửi message hoặc cập nhật Ticket.
- Thay đổi quyền trong khi nhân viên đang mở Conversation.
- File sai loại/quá dung lượng/lỗi upload (nếu file nằm trong MVP).
- Rate limit và payload không hợp lệ.
- SLA ngoài giờ/ngày nghỉ/tạm dừng (sau khi policy SLA được duyệt).
- Khách gửi message đồng thời với thao tác resolve/close.
- Notification retry không gửi lặp không kiểm soát.
- Kiểm tra mọi đường truy cập API để đảm bảo internal note không thể lộ qua response, notification hoặc Ticket công khai.

## 7. Báo cáo trạng thái

Không tự đánh dấu các requirement là “đã test” chỉ vì đã liệt kê test ứng viên. Khi QA thực hiện, cập nhật trạng thái tại từng dòng hoặc quản lý kết quả chạy trong công cụ QA chính thức và liên kết lại tại đây.

| Chỉ số | Giá trị khởi tạo |
|---|---|
| Requirement ID được lập chỉ mục | 73 |
| Test case baseline hiện có | 26 |
| Requirement đã VERIFIED | 0 — chưa ghi nhận bằng chứng trong ma trận này |
| Requirement bị BLOCKED | Cần xác minh sau khi PO rà soát |
| Test bổ sung cần tạo | Theo mục 6 |

## 8. Lịch sử thay đổi

| Phiên bản | Ngày | Nội dung |
|---|---|---|
| 1.0 | 2026-10-20 | Lập chỉ mục 73 requirement ID, gắn test case ứng viên theo module và ghi rõ trạng thái cần QA xác minh. |
