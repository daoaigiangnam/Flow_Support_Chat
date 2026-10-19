# Flow Support Chat — Bảng quyết định và chuyển trạng thái

- Phiên bản: 1.0
- Mục đích: Biến các nguyên tắc nghiệp vụ thành bảng quyết định có thể kiểm thử.
- Liên quan: [Đặc tả nghiệp vụ tổng thể](01-business-workflow-specification.md), [Yêu cầu chức năng](02-functional-requirements.md).

## 1. Bảng quyết định AI → người thật

Áp dụng theo thứ tự ưu tiên từ trên xuống. Nếu một điều kiện bắt buộc handoff đúng, không tiếp tục vòng AI tự động.

| Ưu tiên | Điều kiện | Hành động | Lý do cần ghi |
|---:|---|---|---|
| 1 | Có dấu hiệu rủi ro bảo mật, hành động quản trị hoặc cần phê duyệt | Handoff đến người có thẩm quyền | Security/approval required |
| 2 | Khách hàng yêu cầu gặp nhân viên | Handoff | Customer requested human |
| 3 | Yêu cầu thuộc nhóm phải xử lý bởi Sales/HR theo chính sách | Handoff/định tuyến đúng nhóm | Business-owned request |
| 4 | Sự cố nghiêm trọng/nguy cơ SLA theo quy tắc được duyệt | Handoff ưu tiên | Impact/SLA risk |
| 5 | AI không tìm được nguồn phù hợp hoặc không thể đưa hướng dẫn an toàn | Handoff hoặc hỏi bổ sung theo chính sách | Insufficient evidence |
| 6 | Khách phản hồi hướng dẫn chưa giải quyết | Tiếp tục hỏi có mục tiêu một lần/đợt theo cấu hình, sau đó handoff khi không tiến triển | Unresolved by AI |
| 7 | AI có hướng dẫn có căn cứ, khách xác nhận đã giải quyết | Kết thúc hỗ trợ AI theo chính sách | AI resolved |
| 8 | Chưa đủ thông tin nhưng vẫn có thể hỏi | Hỏi bổ sung | Missing required information |

**OPEN:** Cần duyệt mức độ nghiêm trọng, ngưỡng confidence, số vòng hỏi và trường hợp nào bắt buộc tạo Ticket.

## 2. Bảng quyết định phân loại nhóm

| Tín hiệu chính | Nhóm đề xuất | Điều kiện giao tự động | Nếu thiếu chắc chắn |
|---|---|---|---|
| Sự cố/cấu hình/vận hành hệ thống CNTT | Technical | Khớp danh mục dịch vụ và mapping được phép | Triage |
| Báo giá/hợp đồng/gia hạn/tư vấn mua mới | Sales | Mapping Sales có hiệu lực | Triage |
| Tuyển dụng/chính sách/thủ tục nhân sự | HR | Mapping HR có hiệu lực và người nhận có quyền | Triage |
| Yêu cầu hành chính/khác | Other | Có hàng đợi/người nhận được cấu hình | Triage |
| Nhiều ý định xung đột hoặc nội dung mơ hồ | Triage | Không tự giao nếu chưa rõ | Điều phối viên |

Nhóm AI dự đoán không được dùng để bỏ qua quyền truy cập hoặc danh sách nhân sự hợp lệ.

## 3. Bảng quyết định phân công M-Tech Care

| Điều kiện | Người/nhóm nhận | Trạng thái phân công | Thông báo |
|---|---|---|---|
| Có mapping hiệu lực, Primary hoạt động và đủ quyền | Primary hoặc assignee được mapping chỉ định | Assigned | Gửi thông báo nhận việc |
| Primary không hoạt động/không có quyền, Backup hợp lệ | Backup theo mapping | Assigned via Backup | Thông báo Backup và quản lý theo chính sách |
| Primary/Backup không hợp lệ nhưng có escalation | Hàng đợi/cấp escalation đã cấu hình | Escalated | Thông báo cấp escalation |
| Không có mapping hoặc tất cả người nhận không hợp lệ | Triage/Unassigned | Needs Triage | Cảnh báo điều phối |
| Mapping bị thay đổi sau khi Ticket đã được gán | Không tự động đổi Ticket hiện hữu trừ khi có quy tắc | Giữ assignment hiện tại hoặc reassign theo quyết định được duyệt | Ghi audit |
| Nhân viên không nhận việc trong thời hạn | Backup/escalation nếu rule đã cấu hình | Reassignment pending/Assigned | Ghi lý do và thông báo |

**OPEN:** “Không khả dụng” phải được định nghĩa bằng dữ liệu trạng thái nhân sự, lịch nghỉ, lịch trực hay thời gian không nhận việc; không suy luận chỉ từ việc chưa trả lời chat.

## 4. Bảng quyết định tạo/liên kết Ticket

| Đã có Ticket liên kết? | Yêu cầu cần theo dõi? | Cùng yêu cầu? | Hành động |
|---|---|---|---|
| Có | Có | Có | Dùng Ticket hiện tại; thêm thông tin theo quy tắc |
| Có | Có | Không | Triage quyết định tách Ticket mới |
| Không | Không | Không áp dụng | Không bắt buộc tạo Ticket; lưu Conversation |
| Không | Có | Không áp dụng | Tạo Ticket |
| Không rõ do timeout lần tạo trước | Có | Chưa xác định | Đối chiếu Bitrix24 trước retry |
| Có Ticket đã đóng | Có | Có vấn đề tái phát | Áp dụng chính sách mở lại hoặc tạo Ticket mới |

Không được dựa duy nhất vào tiêu đề giống nhau để kết luận hai yêu cầu là một. Quy tắc nhận diện cùng yêu cầu phải được chốt dựa trên đối tượng Bitrix24 và quy trình vận hành.

## 5. Bảng xử lý lỗi tích hợp Bitrix24

| Loại kết quả | Hành động | Có retry? | Điều kiện |
|---|---|---|---|
| Thành công và có Ticket ID | Lưu ID, mapping và trạng thái SYNCED | Không | Có phản hồi xác nhận |
| Timeout/connection reset, kết quả không rõ | Chuyển RECONCILIATION_REQUIRED | Chưa retry tạo ngay | Tra cứu/đối chiếu trước |
| Lỗi giới hạn tốc độ/tạm thời đã nhận diện | Chuyển RETRY_WAIT | Có | Backoff và giới hạn số lần |
| Lỗi xác thực/quyền | FAILED_REQUIRES_ACTION | Không lặp vô hạn | Cần sửa credential/quyền |
| Lỗi dữ liệu/validation | FAILED_REQUIRES_ACTION | Không lặp nguyên payload | Cần sửa dữ liệu/mapping |
| Ticket đã tồn tại khi đối chiếu | Lưu Ticket ID tìm được | Không tạo mới | Xác minh đúng khách và yêu cầu |

**Yêu cầu vận hành:** Phải có cách xem các bản ghi Pending/Reconciliation/Failed và cách xử lý lại có kiểm soát.

## 6. Trạng thái Conversation — chuyển trạng thái hợp lệ

| Trạng thái hiện tại | Sự kiện | Trạng thái tiếp theo | Ghi chú |
|---|---|---|---|
| OPEN | Khách gửi yêu cầu | AI_HANDLING | Nếu chính sách bật AI |
| AI_HANDLING | AI hỏi bổ sung | WAITING_CUSTOMER | Chờ trả lời |
| WAITING_CUSTOMER | Khách trả lời | AI_HANDLING | Tiếp tục phân tích |
| AI_HANDLING | Khách xác nhận đã giải quyết | RESOLVED | Theo chính sách |
| AI_HANDLING | Điều kiện handoff đúng | HUMAN_HANDOFF | Tạo gói bàn giao |
| HUMAN_HANDOFF | Có nhân viên nhận | HUMAN_HANDLING | Gắn người/nhóm |
| HUMAN_HANDLING | Đang chờ khách | WAITING_CUSTOMER | Phải phân biệt người chờ là ai trong thiết kế chi tiết |
| HUMAN_HANDLING | Yêu cầu được giải quyết | RESOLVED | Không đồng nghĩa Ticket đã đóng |
| RESOLVED | Khách gửi yêu cầu mới/cùng vấn đề | OPEN hoặc quy trình reopen | OPEN: xác định điều kiện tiếp tục/tạo Conversation mới |
| Bất kỳ trạng thái hợp lệ | Đóng theo chính sách | CLOSED | Không xóa lịch sử |

Các trạng thái chính thức cần được rút gọn hoặc mở rộng sau khi thiết kế UX; không được sử dụng Conversation status thay cho Ticket status.

## 7. Quy tắc chuyển trạng thái Ticket

Ticket phải tuân theo trạng thái thực tế của loại đối tượng Bitrix24 được chọn. Bảng dưới là quy tắc nghiệp vụ mong muốn, không phải tên status Bitrix đã xác nhận.

| Từ | Sự kiện | Sang | Điều kiện |
|---|---|---|---|
| New | Nhân viên xác nhận tiếp nhận | Accepted/In Progress | Có assignee hợp lệ |
| Accepted | Bắt đầu xử lý | In Progress | Có người chịu trách nhiệm |
| In Progress | Cần khách cung cấp thông tin | Waiting Customer | Ghi rõ thông tin cần |
| In Progress | Chờ bên thứ ba | Waiting Third Party | Ghi rõ bên/việc đang chờ |
| Waiting Customer | Khách phản hồi | In Progress | Cập nhật dữ liệu mới |
| Waiting Third Party | Có kết quả từ bên thứ ba | In Progress | Ghi kết quả |
| In Progress | Đã giải quyết | Resolved | Có nội dung kết quả |
| Resolved | Khách xác nhận/đủ điều kiện đóng | Closed | Theo chính sách |
| Resolved/Closed | Khách báo chưa giải quyết | Reopened hoặc In Progress | Theo khả năng đối tượng và chính sách |
| Bất kỳ trạng thái mở | Hủy hợp lệ | Cancelled | Bắt buộc có lý do |

## 8. Bảng sự kiện và thông báo

| Sự kiện | Người nhận dự kiến | Điều kiện gửi | Nội dung cần tránh |
|---|---|---|---|
| Handoff được tạo | Primary/nhóm nhận | Có nhóm/assignment hợp lệ | Không gửi dữ liệu khách khác |
| Ticket được tạo | Khách hàng và người nhận theo chính sách | Bitrix xác nhận thành công | Không báo mã Ticket giả |
| Không tìm được người nhận | Điều phối/Quản lý | Assignment không hợp lệ | Không lộ cấu hình nội bộ cho khách |
| Cần khách bổ sung | Khách hàng | Có câu hỏi cụ thể | Không gửi ghi chú nội bộ |
| Ticket chuyển nhóm/người | Người nhận mới và bên liên quan | Assignment được xác nhận | Không thông báo lặp vô hạn |
| SLA sắp/quá hạn | Assignee/Primary/quản lý | Rule SLA kích hoạt | Không cảnh báo sai timezone |
| Đã giải quyết/đóng | Khách hàng | Trạng thái được xác nhận | Không tuyên bố hài lòng nếu chưa xác nhận |

## 9. Bộ tình huống kiểm thử bảng quyết định

1. AI giải quyết FAQ và khách xác nhận.
2. AI trả lời nhưng khách báo chưa giải quyết.
3. Khách bấm “Gặp nhân viên” khi AI đang tạo câu trả lời.
4. Yêu cầu Sales chứa từ khóa kỹ thuật nhưng mục đích là mua dịch vụ.
5. Yêu cầu HR có dữ liệu nhạy cảm.
6. Không có mapping cho khách hàng + nhóm.
7. Primary không hoạt động nhưng Backup hợp lệ.
8. Primary và Backup đều không hợp lệ.
9. Bitrix tạo Ticket thành công nhưng phản hồi bị timeout.
10. Bitrix từ chối vì thiếu field bắt buộc.
11. Khách gửi lại cùng yêu cầu sau khi Ticket đã đóng.
12. Khách gửi hai yêu cầu độc lập trong một Conversation.
13. Extension đóng trước khi thông báo được gửi.
14. Nhân viên chuyển nhóm nhưng không ghi lý do.
15. Người dùng thử mở Conversation của tổ chức khác.

