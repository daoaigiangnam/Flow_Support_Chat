# Flow Support Chat — Đặc tả yêu cầu chức năng

- Phiên bản: 1.0
- Trạng thái: Baseline cho phân tích chức năng; các điểm cần xác nhận được đánh dấu OPEN.
- Tài liệu liên quan: [Đặc tả nghiệp vụ tổng thể](01-business-workflow-specification.md).

## 1. Quy ước

- **MUST**: bắt buộc để bảo đảm nghiệp vụ/an toàn dữ liệu.
- **SHOULD**: nên thực hiện; nếu không thực hiện phải ghi rõ lý do.
- **OPEN**: cần chủ nghiệp vụ xác nhận trước khi coi là yêu cầu phát hành.
- Một yêu cầu chức năng phải có điều kiện đầu vào, hành vi, kết quả, lỗi và cách kiểm thử.
- Không quy định tên bảng, schema vật lý hay endpoint API trong tài liệu này.

## 2. Nhóm chức năng và yêu cầu chi tiết

### FR-CHAT — Chat và Conversation

| ID | Mức | Yêu cầu chức năng | Điều kiện/kết quả |
|---|---|---|---|
| FR-CHAT-001 | MUST | Hệ thống phải tạo định danh duy nhất cho mỗi Conversation. | Mỗi phiên có thể được tham chiếu từ Message và Ticket. |
| FR-CHAT-002 | MUST | Hệ thống phải gắn Conversation với danh tính đã xác thực và phạm vi khách hàng/đơn vị. | Không cho truy cập lịch sử của tổ chức khác. |
| FR-CHAT-003 | MUST | Hệ thống phải lưu Message cùng tác giả, thời điểm, loại nội dung và trạng thái xử lý. | Phân biệt khách hàng, AI, nhân viên và sự kiện hệ thống. |
| FR-CHAT-004 | MUST | Hệ thống phải hiển thị lịch sử theo quyền và thứ tự thời gian. | Không hiển thị ghi chú nội bộ như tin nhắn công khai. |
| FR-CHAT-005 | MUST | Nếu người dùng mất kết nối hoặc đóng Extension, Message đã được xác nhận lưu không được mất. | Khi mở lại, tải được lịch sử theo quyền. |
| FR-CHAT-006 | MUST | Hệ thống phải thể hiện trạng thái gửi tin nhắn. | Ít nhất: đang gửi, đã gửi, lỗi gửi; retry không nhân đôi nội dung. |
| FR-CHAT-007 | SHOULD | Hỗ trợ file đính kèm theo loại, dung lượng và quyền được cấu hình. | Từ chối file không hợp lệ; ghi nhận lỗi cho người dùng. |
| FR-CHAT-008 | MUST | Hệ thống phải cho phép tiếp tục Conversation cũ hoặc tạo Conversation mới theo chính sách. | OPEN: điều kiện phân biệt hội thoại tiếp tục và hội thoại mới. |
| FR-CHAT-009 | MUST | Hệ thống phải có cách thể hiện rằng khách đang nói chuyện với AI hay nhân viên. | Không gây hiểu nhầm phản hồi AI là phản hồi của nhân viên. |
| FR-CHAT-010 | MUST | Hệ thống phải bảo vệ chống gửi trùng do người dùng bấm nhiều lần hoặc retry mạng. | Tin nhắn logic không xuất hiện lặp ngoài ý muốn. |

### FR-AUTH — Danh tính và quyền truy cập

| ID | Mức | Yêu cầu chức năng | Điều kiện/kết quả |
|---|---|---|---|
| FR-AUTH-001 | MUST | Hệ thống phải xác minh danh tính trước khi truy cập dữ liệu khách hàng riêng. | Chưa xác thực chỉ được dùng luồng cho phép công khai. |
| FR-AUTH-002 | MUST | Mọi thao tác đọc/sửa Conversation, Ticket liên kết, file và cấu hình phải kiểm tra quyền ở phía server. | Không chỉ dựa vào ẩn/hiện nút trên Extension. |
| FR-AUTH-003 | MUST | Nhân viên chỉ xem dữ liệu trong phạm vi khách hàng/nhóm được cấp quyền. | Truy cập trái quyền bị từ chối và được ghi nhận. |
| FR-AUTH-004 | MUST | Ghi chú nội bộ và nội dung dành cho nhân viên phải được phân biệt với tin nhắn khách hàng. | Không đồng bộ ghi chú nội bộ ra kênh công khai. |
| FR-AUTH-005 | MUST | Bí mật AI/Bitrix24 phải được giữ phía server hoặc kho bí mật được bảo vệ. | Không nhúng secret dài hạn vào gói Extension. |
| FR-AUTH-006 | SHOULD | Hỗ trợ thu hồi phiên/token và yêu cầu xác thực lại. | Phiên hết hạn không được tiếp tục truy cập dữ liệu. |

### FR-AI — AI First Response và Handoff

| ID | Mức | Yêu cầu chức năng | Điều kiện/kết quả |
|---|---|---|---|
| FR-AI-001 | MUST | AI phải tiếp nhận nội dung Conversation và ngữ cảnh được phép sử dụng. | Không lấy dữ liệu ngoài phạm vi quyền. |
| FR-AI-002 | MUST | AI phải phân biệt dữ kiện đã biết và thông tin còn thiếu. | Có thể hỏi bổ sung trước khi trả lời. |
| FR-AI-003 | MUST | AI phải tra cứu nguồn tri thức được phê duyệt khi loại yêu cầu yêu cầu tra cứu. | Nếu không có nguồn phù hợp, không bịa câu trả lời như sự thật. |
| FR-AI-004 | MUST | AI phải trả lời bằng ngôn ngữ phù hợp với khách hàng và nêu bước thực hiện rõ ràng. | Lưu được câu trả lời đã gửi. |
| FR-AI-005 | MUST | Hệ thống phải cho phép khách hàng yêu cầu nhân viên theo luồng được cấu hình. | Không bắt buộc khách tiếp tục hỏi AI. |
| FR-AI-006 | MUST | AI phải bàn giao tóm tắt và dữ kiện đã thu thập. | Nhân viên không cần yêu cầu khách lặp lại dữ liệu đã có. |
| FR-AI-007 | MUST | Hệ thống phải hỗ trợ các điều kiện handoff cấu hình được. | Khách yêu cầu người thật, AI không đủ căn cứ, vấn đề nhạy cảm, nguy cơ SLA. |
| FR-AI-008 | MUST | AI không được tự thực hiện thao tác quản trị/nhạy cảm nếu chưa có quyền và quy trình phê duyệt. | Handoff đến người có thẩm quyền. |
| FR-AI-009 | SHOULD | AI nên ghi nhận nguồn tri thức tham khảo hoặc phiên bản nguồn dùng để trả lời. | Phục vụ truy vết chất lượng câu trả lời. |
| FR-AI-010 | OPEN | Cấu hình nhà cung cấp AI và fallback khi nhà cung cấp lỗi. | Cần thống nhất provider ưu tiên, Ollama/external API và chính sách fallback. |
| FR-AI-011 | OPEN | Ngưỡng confidence, số vòng hỏi và thời gian chờ khách hàng. | Không tự chốt con số trong code. |
| FR-AI-012 | MUST | Khi AI lỗi/timeout, hệ thống phải cho phép thử lại có giới hạn hoặc handoff. | Không để Conversation bị treo vô thời hạn. |

### FR-ROUTE — Phân loại và định tuyến

| ID | Mức | Yêu cầu chức năng | Điều kiện/kết quả |
|---|---|---|---|
| FR-ROUTE-001 | MUST | Hệ thống phải hỗ trợ nhóm Technical, Sales, HR, Other và Triage/Unclassified. | Danh sách nhóm có thể cấu hình. |
| FR-ROUTE-002 | MUST | AI có thể đề xuất nhóm; Routing Engine phải áp dụng quy tắc được phê duyệt. | Không giao người chỉ dựa vào suy đoán tự do của AI. |
| FR-ROUTE-003 | MUST | Nếu phân loại mơ hồ hoặc xung đột, chuyển Triage. | Có hàng đợi và người/nhóm chịu trách nhiệm phân loại. |
| FR-ROUTE-004 | MUST | Khi người có quyền sửa nhóm thủ công, hệ thống phải ghi nhóm trước/sau, người sửa, thời gian và lý do. | Truy vết được phân loại sai. |
| FR-ROUTE-005 | MUST | Phải giữ nguyên lịch sử và Ticket ID khi chuyển nhóm. | Không tạo Conversation/Ticket mới chỉ vì chuyển nhóm. |
| FR-ROUTE-006 | OPEN | Quy tắc xử lý một Message có nhiều yêu cầu độc lập. | Nghiệp vụ xác nhận giữ chung hay tách Ticket. |

### FR-ASSIGN — Ma trận nhân sự M-Tech Care

| ID | Mức | Yêu cầu chức năng | Điều kiện/kết quả |
|---|---|---|---|
| FR-ASSIGN-001 | MUST | Có cấu hình mapping khách hàng + nhóm/dịch vụ → Primary, người xử lý chuyên môn, Backup và cấp escalation. | Mapping có trạng thái hiệu lực. |
| FR-ASSIGN-002 | MUST | Chỉ gán nhân sự còn hoạt động và có quyền phù hợp. | Không gán người nghỉ việc/vô hiệu hóa hoặc ngoài phạm vi. |
| FR-ASSIGN-003 | MUST | Nếu Primary không khả dụng, áp dụng Backup/escalation theo cấu hình. | Không âm thầm chọn người bất kỳ. |
| FR-ASSIGN-004 | MUST | Nếu không có mapping hợp lệ, chuyển Triage/Unassigned và cảnh báo. | Có người chịu trách nhiệm xử lý ngoại lệ. |
| FR-ASSIGN-005 | MUST | Ghi lịch sử mỗi lần gán/chuyển người. | Có người thực hiện, thời điểm và lý do. |
| FR-ASSIGN-006 | MUST | Cho phép Primary khác người xử lý chuyên môn. | Sales/HR không mặc định đi vào Primary kỹ thuật. |
| FR-ASSIGN-007 | OPEN | Điều kiện kích hoạt Backup: nghỉ, quá tải, không nhận việc hay quá thời gian phản hồi. | Cần định nghĩa rule cụ thể. |
| FR-ASSIGN-008 | OPEN | Một Ticket có thể có nhiều người xử lý đồng thời hay chỉ một assignee chính. | Chốt theo khả năng đối tượng Bitrix24. |

### FR-TICKET — Ticket và Bitrix24

| ID | Mức | Yêu cầu chức năng | Điều kiện/kết quả |
|---|---|---|---|
| FR-TICKET-001 | MUST | Conversation và Ticket phải có liên kết bền vững. | Có thể mở từ hai phía theo quyền. |
| FR-TICKET-002 | MUST | Trước khi tạo Ticket phải kiểm tra liên kết hiện hữu và áp dụng quy tắc chống trùng. | Retry không tạo Ticket trùng. |
| FR-TICKET-003 | MUST | Chỉ xác nhận Ticket đã tạo sau khi có xác nhận thành công từ Bitrix24. | Không hiển thị thành công giả. |
| FR-TICKET-004 | MUST | Phải ghi nhận trạng thái đồng bộ riêng với trạng thái nghiệp vụ Ticket. | Lỗi tích hợp không làm sai trạng thái công việc. |
| FR-TICKET-005 | MUST | Timeout khi tạo Ticket phải đưa vào đối chiếu trước khi retry. | Xử lý kết quả không rõ ràng. |
| FR-TICKET-006 | MUST | Lỗi quyền/validation phải được ghi nhận và đưa ra xử lý; không retry vô hạn. | Có thông tin để vận hành khắc phục. |
| FR-TICKET-007 | MUST | Phải có mapping trạng thái nghiệp vụ sang trạng thái thực tế của Bitrix24. | Mapping được kiểm thử. |
| FR-TICKET-008 | MUST | Thông tin tạo Ticket phải gồm mã hội thoại, khách hàng, nội dung, nhóm và thông tin phân công theo mapping đã chốt. | Field bắt buộc phụ thuộc đối tượng Bitrix24. |
| FR-TICKET-009 | OPEN | Chọn đối tượng Bitrix24: Task, CRM, Smart Process hoặc module Ticket hiện có. | Không thể chốt field/API trước quyết định này. |
| FR-TICKET-010 | OPEN | Quy tắc tách/gộp Ticket và nhận diện Ticket hiện hữu. | Cần chốt khóa đối chiếu và điều kiện nghiệp vụ. |
| FR-TICKET-011 | MUST | Nội dung nội bộ và nội dung công khai phải được phân biệt. | Không lộ note nội bộ cho khách hàng. |

### FR-STAFF — Nhân viên xử lý

| ID | Mức | Yêu cầu chức năng | Điều kiện/kết quả |
|---|---|---|---|
| FR-STAFF-001 | MUST | Nhân viên phải xem được gói bàn giao và lịch sử liên quan. | Không yêu cầu khách nhắc lại thông tin đã có. |
| FR-STAFF-002 | MUST | Nhân viên phải xác nhận tiếp nhận hoặc trạng thái nhận việc theo quy trình. | Hệ thống biết Ticket đã có người nhận. |
| FR-STAFF-003 | MUST | Cập nhật tiến độ/kết quả phải đồng bộ theo quy tắc Bitrix24. | Có lịch sử và người thực hiện. |
| FR-STAFF-004 | MUST | Chuyển nhóm/người phải có lý do và giữ lịch sử. | Tránh Ticket bị bỏ rơi. |
| FR-STAFF-005 | MUST | Chờ khách hàng/bên thứ ba phải được biểu diễn rõ. | Hỗ trợ theo dõi và tính SLA theo chính sách. |
| FR-STAFF-006 | OPEN | Nhân viên trả lời khách trực tiếp từ Extension hay qua Bitrix24. | Chốt kênh giao tiếp chính thức. |

### FR-NOTIFY — Thông báo

| ID | Mức | Yêu cầu chức năng | Điều kiện/kết quả |
|---|---|---|---|
| FR-NOTIFY-001 | MUST | Phát thông báo theo sự kiện đã được xác nhận. | Ví dụ: Ticket đã tạo, đã giao, cần bổ sung, đã giải quyết. |
| FR-NOTIFY-002 | MUST | Retry thông báo phải có giới hạn và chống gửi trùng. | Một sự kiện logic không tạo spam. |
| FR-NOTIFY-003 | MUST | Không gửi nội dung nội bộ/nhạy cảm cho khách hàng. | Kiểm tra quyền và loại thông báo. |
| FR-NOTIFY-004 | MUST | Các công việc thông báo quan trọng phải chạy phía server/worker, không phụ thuộc Extension đang mở. | Không mất thông báo khi browser đóng. |
| FR-NOTIFY-005 | OPEN | Kênh thông báo và ưu tiên kênh. | Chat, email, Bitrix notification hoặc kênh khác. |

### FR-SLA — Ưu tiên và escalation

| ID | Mức | Yêu cầu chức năng | Điều kiện/kết quả |
|---|---|---|---|
| FR-SLA-001 | MUST | Ưu tiên/SLA phải lấy từ cấu hình đã được duyệt. | Không hard-code thời hạn nghiệp vụ. |
| FR-SLA-002 | MUST | Cần phân biệt thời hạn phản hồi đầu tiên và thời hạn giải quyết nếu chính sách có cả hai. | Báo cáo đúng loại SLA. |
| FR-SLA-003 | MUST | Cảnh báo theo các mốc đã cấu hình và ghi nhận lịch sử. | Có thể kiểm tra sau sự kiện. |
| FR-SLA-004 | OPEN | Lịch làm việc, ngày nghỉ, tạm dừng khi chờ khách/bên thứ ba, reset khi chuyển nhóm. | Cần chính sách chính thức. |

### FR-ADMIN — Cấu hình và kiểm toán

| ID | Mức | Yêu cầu chức năng | Điều kiện/kết quả |
|---|---|---|---|
| FR-ADMIN-001 | MUST | Quản trị viên được quản lý nhóm hỗ trợ và ma trận phân công theo quyền. | Thay đổi không cần sửa code nếu nằm trong phạm vi cấu hình. |
| FR-ADMIN-002 | MUST | Mapping phải có hiệu lực, trạng thái hoạt động và lịch sử thay đổi. | Tránh mapping cũ tiếp tục được dùng. |
| FR-ADMIN-003 | MUST | Ghi audit cho thay đổi cấu hình, quyền, phân công và trạng thái quan trọng. | Có người, thời điểm, giá trị trước/sau và lý do khi cần. |
| FR-ADMIN-004 | MUST | Có màn hình/khả năng tra cứu lỗi đồng bộ và yêu cầu đang chờ điều phối. | Vận hành có thể xử lý mà không truy cập trực tiếp database. |
| FR-ADMIN-005 | OPEN | Vai trò quản trị và phạm vi quyền chi tiết. | Cần ma trận quyền được duyệt. |

## 3. Yêu cầu phi chức năng

| ID | Yêu cầu | Cách kiểm tra |
|---|---|---|
| NFR-SEC-001 | Kiểm tra quyền phía server cho mọi dữ liệu khách hàng. | Test truy cập chéo tổ chức/khách hàng. |
| NFR-SEC-002 | Secret không xuất hiện trong Extension bundle, log hoặc thông báo. | Kiểm tra bundle/log. |
| NFR-REL-001 | Lỗi tạm thời của AI/Bitrix/notification không làm mất yêu cầu đã lưu. | Mô phỏng timeout và khởi động lại worker. |
| NFR-REL-002 | Retry không tạo trùng Message/Ticket/notification logic. | Test gửi lặp và timeout. |
| NFR-OBS-001 | Có correlation identifier để truy vết Conversation → handoff → Ticket → notification. | Tra cứu một yêu cầu từ log. |
| NFR-AUD-001 | Thay đổi phân công, nhóm, trạng thái và cấu hình có audit. | Kiểm tra lịch sử. |
| NFR-MAINT-001 | Quy tắc phân công, SLA, trạng thái và thông báo được cấu hình thay vì hard-code. | Thay đổi cấu hình trong môi trường thử nghiệm. |
| NFR-UX-001 | Giao diện thể hiện rõ trạng thái gửi tin nhắn, AI/nhân viên, Ticket và lỗi. | Kiểm thử UI. |
| NFR-PRIV-001 | Chỉ lưu dữ liệu cần thiết theo chính sách lưu trữ được duyệt. | Rà soát dữ liệu/log/file. |

## 4. Quy tắc nghiệp vụ tham chiếu

- BR-01: AI là lớp phản hồi đầu tiên, không thay thế trách nhiệm của nhân viên trong yêu cầu cần xử lý.
- BR-02: Handoff phải bảo toàn ngữ cảnh.
- BR-03: AI đề xuất phân loại; hệ thống áp dụng routing rules.
- BR-04: Không có mapping hợp lệ thì Triage, không gán ngẫu nhiên.
- BR-05: Bitrix24 là nguồn chuẩn của Ticket sau khi loại đối tượng và mapping được xác nhận.
- BR-06: Timeout không đồng nghĩa với tạo Ticket thất bại.
- BR-07: Conversation state, Ticket state và integration sync state độc lập.
- BR-08: Ghi chú nội bộ không được gửi ra khách hàng.
- BR-09: Không tự đặt SLA hoặc thời gian tự đóng chưa được nghiệp vụ phê duyệt.
- BR-10: Mọi thay đổi phân loại/phân công quan trọng phải truy vết được.

## 5. Phụ thuộc và điều kiện chặn trước khi chốt thiết kế kỹ thuật

1. Xác nhận loại đối tượng Bitrix24 làm Ticket.
2. Xác nhận danh sách khách hàng/dịch vụ và ma trận nhân sự M-Tech Care.
3. Xác nhận kênh nhân viên phản hồi khách hàng.
4. Xác nhận chính sách tạo/tách/gộp Ticket.
5. Xác nhận SLA, lịch làm việc và escalation.
6. Xác nhận vai trò/quyền và chính sách lưu trữ.
7. Xác nhận provider AI, nguồn tri thức và fallback.

## 6. Definition of Ready cho mỗi chức năng

Một chức năng chỉ được chuyển sang triển khai khi:
- Có ID chức năng và chủ nghiệp vụ.
- Có luồng chính và ít nhất các nhánh lỗi liên quan.
- Có dữ liệu vào/ra ở mức nghiệp vụ.
- Có điều kiện phân quyền.
- Có quy tắc trạng thái và audit.
- Có tiêu chí nghiệm thu.
- Các điểm OPEN ảnh hưởng chức năng đã được quyết định hoặc được loại rõ khỏi phạm vi release.

