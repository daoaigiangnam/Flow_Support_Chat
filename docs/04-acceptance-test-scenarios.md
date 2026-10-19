# Flow Support Chat — Kịch bản nghiệm thu chi tiết

- Phiên bản: 1.0
- Mục tiêu: Cho QA, nghiệp vụ và Dev dùng chung bộ kịch bản kiểm thử.
- Ghi chú: Đây là expected behavior baseline. Các chính sách có nhãn OPEN phải được xác nhận trước khi chốt test chính thức.

## 1. Cách sử dụng

Mỗi test case gồm: ID, mục tiêu, tiền điều kiện, thao tác, kết quả mong đợi. Khi thực hiện thực tế cần bổ sung: môi trường, build/version, người test, kết quả PASS/FAIL, bằng chứng và defect ID.

## 2. Test case

### TC-001 — Khách hàng đã xác thực mở Chat
**Tiền điều kiện:** Tài khoản hợp lệ, thuộc tổ chức A, có quyền dùng Extension.
**Thao tác:** Mở Extension và vào Chat.
**Kết quả mong đợi:**
- Conversation được tạo hoặc mở đúng theo chính sách.
- Conversation gắn với tổ chức A.
- Lịch sử hiển thị đúng quyền.
- Có thể gửi Message và nhận trạng thái gửi.

### TC-002 — Khách hàng chưa xác thực
**Tiền điều kiện:** Không có phiên hợp lệ.
**Thao tác:** Mở Chat và yêu cầu xem lịch sử.
**Kết quả mong đợi:**
- Hệ thống yêu cầu xác thực.
- Không trả về lịch sử/ticket riêng tư.
- Không gán người dùng vào một khách hàng chỉ dựa trên lời khai.

### TC-003 — AI hỏi bổ sung
**Tiền điều kiện:** Yêu cầu thiếu thông tin bắt buộc theo intent.
**Thao tác:** Gửi yêu cầu thiếu dữ liệu.
**Kết quả mong đợi:**
- AI hỏi thông tin còn thiếu cụ thể.
- Không hỏi lại dữ kiện đã có.
- Tin nhắn và câu trả lời được lưu.
- Sau khi khách trả lời, AI tiếp tục cùng Conversation.

### TC-004 — AI trả lời có căn cứ và khách xác nhận
**Tiền điều kiện:** Có nguồn tri thức phù hợp.
**Thao tác:** Gửi câu hỏi, nhận hướng dẫn và xác nhận đã giải quyết.
**Kết quả mong đợi:**
- Câu trả lời AI được lưu.
- Hội thoại kết thúc theo chính sách AI.
- Không tạo Ticket bắt buộc nếu policy xác định câu hỏi này không cần theo dõi.
- Không báo rằng nhân viên đã xử lý.

### TC-005 — Khách yêu cầu người thật
**Tiền điều kiện:** Conversation đang ở AI_HANDLING.
**Thao tác:** Khách chọn/yêu cầu gặp nhân viên.
**Kết quả mong đợi:**
- Handoff được ưu tiên thực hiện.
- Không tiếp tục bắt khách hoàn thành các vòng hỏi AI không cần thiết.
- Tạo gói bàn giao gồm lịch sử, tóm tắt, dữ liệu đã thu thập, bước đã thử.
- Hiển thị trạng thái đang chuyển hỗ trợ.

### TC-006 — AI không có căn cứ
**Tiền điều kiện:** Không có tài liệu phù hợp hoặc kết quả tri thức mâu thuẫn.
**Thao tác:** Gửi câu hỏi ngoài phạm vi tri thức.
**Kết quả mong đợi:**
- AI không bịa đáp án.
- AI nói rõ giới hạn và chuyển người nếu yêu cầu cần xử lý.
- Lý do handoff được ghi nhận.

### TC-007 — Định tuyến Technical
**Tiền điều kiện:** Mapping khách hàng M-Tech Care + Technical hợp lệ.
**Thao tác:** Gửi yêu cầu kỹ thuật và chuyển người thật.
**Kết quả mong đợi:**
- Yêu cầu vào Technical.
- Assignment theo mapping hiệu lực.
- Ticket/Conversation giữ đúng khách hàng và nội dung.

### TC-008 — Định tuyến Sales
**Tiền điều kiện:** Có mapping Sales hợp lệ.
**Thao tác:** Khách hỏi báo giá hoặc gia hạn.
**Kết quả mong đợi:**
- Chuyển Sales theo quy tắc.
- Không mặc định giao cho Primary kỹ thuật.
- Thông tin liên quan được giữ trong gói bàn giao.

### TC-009 — Định tuyến HR
**Tiền điều kiện:** Có mapping HR và quyền xem phù hợp.
**Thao tác:** Khách gửi yêu cầu HR.
**Kết quả mong đợi:**
- Chuyển đúng nhóm HR.
- Dữ liệu chỉ được hiển thị cho người có quyền.
- Nội dung nhạy cảm không được phát tán qua thông báo công khai.

### TC-010 — Phân loại mơ hồ
**Tiền điều kiện:** Không có đủ thông tin để xác định nhóm.
**Thao tác:** Gửi yêu cầu chung chung.
**Kết quả mong đợi:**
- Hệ thống hỏi thêm hoặc đưa Triage theo rule.
- Không tự chọn người nhận ngẫu nhiên.
- Điều phối viên có thể phân loại và ghi lý do.

### TC-011 — Primary không khả dụng
**Tiền điều kiện:** Primary inactive hoặc không có quyền; Backup hợp lệ.
**Thao tác:** Tạo yêu cầu cần handoff.
**Kết quả mong đợi:**
- Áp dụng Backup theo ma trận.
- Lưu lý do dùng Backup.
- Thông báo đúng người.

### TC-012 — Không có mapping hợp lệ
**Tiền điều kiện:** Không có Primary/Backup hợp lệ.
**Thao tác:** Tạo yêu cầu cần nhân viên.
**Kết quả mong đợi:**
- Yêu cầu ở Triage/Unassigned.
- Điều phối/Quản lý nhận cảnh báo.
- Không tự gán nhân sự ngoài danh sách.

### TC-013 — Tạo Ticket thành công
**Tiền điều kiện:** Ticket cần tạo; Bitrix24 sẵn sàng; mapping trường hợp lệ.
**Thao tác:** Chuyển yêu cầu sang xử lý người thật.
**Kết quả mong đợi:**
- Ticket được tạo trên đối tượng Bitrix đã được phê duyệt.
- Ticket ID được lưu và liên kết với Conversation.
- Chỉ sau xác nhận mới báo khách đã tạo Ticket.
- Người nhận đúng theo ma trận.

### TC-014 — Timeout khi tạo Ticket
**Tiền điều kiện:** Bitrix24 nhận request nhưng response bị timeout hoặc không rõ kết quả.
**Thao tác:** Thực hiện tạo Ticket.
**Kết quả mong đợi:**
- Ghi trạng thái cần đối chiếu.
- Không tạo lại mù quáng.
- Nếu đối chiếu tìm thấy Ticket đúng, liên kết Ticket đó.
- Nếu xác nhận không có Ticket, mới thực hiện retry có kiểm soát.

### TC-015 — Bitrix24 từ chối dữ liệu
**Tiền điều kiện:** Thiếu trường bắt buộc hoặc credential không đủ quyền.
**Thao tác:** Gửi yêu cầu tạo Ticket.
**Kết quả mong đợi:**
- Lưu lỗi có thể vận hành xử lý.
- Không retry vô hạn.
- Yêu cầu không bị mất và không báo thành công giả.
- Có thể retry sau khi sửa lỗi.

### TC-016 — Ticket đã tồn tại
**Tiền điều kiện:** Conversation đã liên kết Ticket đang mở cho cùng yêu cầu.
**Thao tác:** Khách tiếp tục gửi thông tin cùng vấn đề.
**Kết quả mong đợi:**
- Bổ sung thông tin vào Ticket hiện hữu theo policy.
- Không tạo Ticket trùng.
- Lịch sử mới liên kết đúng Conversation/Ticket.

### TC-017 — Hai yêu cầu độc lập trong một Conversation
**Tiền điều kiện:** Một Conversation chứa hai yêu cầu không cùng mục đích.
**Thao tác:** Khách gửi yêu cầu thứ hai.
**Kết quả mong đợi:**
- Hệ thống áp dụng quy tắc tách/gộp đã được phê duyệt.
- Nếu cần quyết định thủ công, chuyển Triage.
- Không tự gộp làm sai nhóm/SLA.

### TC-018 — Chuyển nhóm
**Tiền điều kiện:** Ticket đã được giao nhóm Technical nhưng xác định thuộc Sales.
**Thao tác:** Người có quyền chuyển nhóm và nhập lý do.
**Kết quả mong đợi:**
- Nhóm mới được lưu.
- Lịch sử nhóm cũ được giữ.
- Assignment mới được kiểm tra theo ma trận.
- Ticket ID và Conversation không thay đổi.
- SLA xử lý theo chính sách đã chốt.

### TC-019 — Chờ khách hàng
**Tiền điều kiện:** Nhân viên cần khách cung cấp thông tin.
**Thao tác:** Đặt trạng thái Waiting Customer.
**Kết quả mong đợi:**
- Khách được yêu cầu thông tin cụ thể.
- Ticket và Conversation không bị nhầm trạng thái.
- Quy tắc tính SLA được áp dụng theo policy.

### TC-020 — Khách báo chưa giải quyết sau khi Resolved
**Tiền điều kiện:** Ticket đang Resolved.
**Thao tác:** Khách phản hồi vấn đề vẫn tồn tại.
**Kết quả mong đợi:**
- Ticket được mở lại hoặc chuyển trạng thái xử lý theo policy.
- Lịch sử giải quyết trước đó vẫn tồn tại.
- Nhân viên/nhóm chịu trách nhiệm được xác định.

### TC-021 — Cảnh báo SLA
**Tiền điều kiện:** Ticket có SLA đã cấu hình và sắp/quá hạn.
**Thao tác:** Đưa thời gian kiểm thử qua mốc cảnh báo.
**Kết quả mong đợi:**
- Thông báo đến đúng vai trò.
- Ghi thời gian, loại cảnh báo và Ticket.
- Không gửi lặp vô hạn do worker retry.

### TC-022 — Extension đóng
**Tiền điều kiện:** Ticket đang được nhân viên xử lý.
**Thao tác:** Đóng trình duyệt/Extension.
**Kết quả mong đợi:**
- Ticket, đồng bộ và tác vụ thông báo server-side vẫn hoạt động.
- Mở lại Extension thấy trạng thái mới nhất.
- Không mất Message đã xác nhận lưu.

### TC-023 — Truy cập chéo khách hàng
**Tiền điều kiện:** Người dùng A và B thuộc hai tổ chức khác nhau.
**Thao tác:** A thử mở Conversation/Ticket/file của B.
**Kết quả mong đợi:**
- Server từ chối.
- Không trả nội dung riêng tư.
- Ghi log bảo mật đủ để truy vết.

### TC-024 — Ghi chú nội bộ
**Tiền điều kiện:** Ticket có ghi chú dành riêng cho nhân viên.
**Thao tác:** Đồng bộ nội dung và hiển thị cho khách hàng.
**Kết quả mong đợi:**
- Ghi chú nội bộ không xuất hiện trong Chat công khai/thông báo khách.
- Quyền hiển thị được kiểm tra phía server.

### TC-025 — Notification retry
**Tiền điều kiện:** Sự kiện thông báo hợp lệ nhưng kênh gửi lỗi tạm thời.
**Thao tác:** Gây lỗi tạm thời và khôi phục.
**Kết quả mong đợi:**
- Hệ thống thử lại theo giới hạn.
- Không gửi nhiều thông báo trùng cho cùng sự kiện logic.
- Trạng thái lỗi có thể tra cứu.

### TC-026 — Thay đổi ma trận phân công
**Tiền điều kiện:** Mapping khách hàng + nhóm có hiệu lực; quản trị viên cập nhật mapping.
**Thao tác:** Đổi Primary/Backup.
**Kết quả mong đợi:**
- Lưu người sửa, thời gian, giá trị trước/sau.
- Ticket mới dùng mapping hiệu lực mới.
- Ticket đang mở không tự chuyển người trừ khi có quy tắc chuyển giao đã duyệt.

## 3. Mẫu ghi nhận kết quả kiểm thử

| Trường | Giá trị |
|---|---|
| Test Case ID | Ví dụ TC-013 |
| Môi trường/Build | Điền khi chạy |
| Tiền điều kiện thực tế | Điền khi chạy |
| Kết quả thực tế | Điền khi chạy |
| PASS/FAIL/BLOCKED | Điền khi chạy |
| Bằng chứng | Screenshot/log/link Ticket |
| Defect ID | Nếu có |
| Người kiểm thử/Ngày | Điền khi chạy |

