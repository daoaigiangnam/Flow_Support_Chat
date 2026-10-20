# Flow Support Chat — Bộ câu hỏi rà soát và quyết định nghiệp vụ

- Mã tài liệu: BIZ-DECISION-REGISTER
- Phiên bản: 1.0
- Ngày lập: 2026-10-20
- Trạng thái: **DRAFT — chờ Product Owner/nghiệp vụ xác nhận**
- Phạm vi: Chỉ nghiệp vụ sản phẩm và quy tắc vận hành; không thay thế đặc tả kỹ thuật.
- Tài liệu liên quan: [01 — Đặc tả nghiệp vụ](01-business-workflow-specification.md), [02 — Yêu cầu chức năng](02-functional-requirements.md), [03 — Quyết định và trạng thái](03-decision-tables-and-state-transitions.md), [04 — Kịch bản nghiệm thu](04-acceptance-test-scenarios.md), [12 — Toàn bộ luồng hỗ trợ](12-complete-support-flows.md).

> **Lưu ý:** Tài liệu này tập hợp câu hỏi cần quyết định, không tự đưa ra câu trả lời thay doanh nghiệp. Giá trị gợi ý chỉ để thảo luận, chưa phải chính sách đã duyệt. Mỗi quyết định phải có người duyệt, ngày duyệt và phiên bản áp dụng trước khi được coi là APPROVED.

## 1. Cách sử dụng và trạng thái quyết định

Mỗi câu hỏi được gắn một mã để tiện trao đổi, ghi biên bản và truy vết sang yêu cầu/test.

- **OPEN**: Chưa có quyết định được xác nhận.
- **PROPOSED**: Có phương án đề xuất để thảo luận.
- **APPROVED**: Chủ nghiệp vụ đã xác nhận; có người và ngày duyệt.
- **OUT OF SCOPE**: Được loại khỏi phạm vi phiên bản; phải ghi rõ lý do.
- **BLOCKED**: Chưa thể quyết định do thiếu thông tin/phụ thuộc.

Các cột trạng thái trong tài liệu hiện bắt đầu ở OPEN. Không được hiểu nội dung đề xuất là chính sách production.

## 2. Các quyết định cần ưu tiên chốt

| ID | Quyết định | Vì sao quan trọng | Ưu tiên |
|---|---|---|---|
| BIZ-001 | Kênh làm việc chính của nhân viên | Quyết định Dashboard là nơi trả lời khách hay Bitrix24 là nơi làm việc chính | P0 |
| BIZ-002 | Mô hình danh tính khách hàng và tổ chức | Tránh truy cập nhầm khách hàng, quyết định cách mở/tiếp tục hội thoại | P0 |
| BIZ-003 | Quy tắc tạo hội thoại mới/tiếp tục hội thoại | Tránh gộp nhầm các vấn đề độc lập hoặc tạo quá nhiều phiên | P0 |
| BIZ-004 | Quy tắc tạo, tách, gộp và liên kết Ticket | Quyết định một Conversation có thể có bao nhiêu Ticket và ngược lại | P0 |
| BIZ-005 | Loại đối tượng Bitrix24 được dùng làm Ticket | Làm cơ sở chốt field, trạng thái, phân công và luồng vận hành | P0 |
| BIZ-006 | Ma trận nhóm hỗ trợ và nhân sự | Bảo đảm mỗi yêu cầu có đúng nhóm/người chịu trách nhiệm | P0 |
| BIZ-007 | Quy tắc handoff AI sang người thật | Xác định khi nào AI dừng và ai nhận việc | P0 |
| BIZ-008 | Role/permission matrix | Xác định ai xem, trả lời, chuyển, đóng và cấu hình | P0 |
| BIZ-009 | Chính sách SLA và escalation | Cần cho ưu tiên, cảnh báo và báo cáo đúng | P0 |
| BIZ-010 | Quy tắc giải quyết/đóng/mở lại | Tránh yêu cầu bị đóng quá sớm hoặc tồn đọng vô hạn | P0 |
| BIZ-011 | Kênh và nội dung thông báo | Xác định ai nhận thông báo, khi nào và mức độ chi tiết | P1 |
| BIZ-012 | Phản hồi khách hàng và đo chất lượng | Xác định cách đánh giá trải nghiệm, AI và nhân viên | P1 |
| BIZ-013 | File đính kèm và dữ liệu lưu trữ | Quyết định dữ liệu nào được tiếp nhận, giữ bao lâu và ai được xem | P1 |
| BIZ-014 | Phạm vi báo cáo và KPI | Xác định chỉ số vận hành và cách tính thống nhất | P1 |
| BIZ-015 | Ngôn ngữ, trình duyệt và khả năng tiếp cận | Xác định trải nghiệm được hỗ trợ và tiêu chí nghiệm thu | P1 |
| BIZ-016 | Chính sách bảo trì, ngừng dịch vụ và sự cố | Xác định cách thông báo và tiếp nhận khi dịch vụ gián đoạn | P1 |

## 3. Câu hỏi chi tiết theo nhóm nghiệp vụ

### A. Khách hàng, danh tính và vòng đời hội thoại

**BIZ-101 — Ai được phép bắt đầu hội thoại?**  
- Câu hỏi: Chỉ khách hàng đã đăng nhập/xác thực mới được gửi yêu cầu, hay cho phép khách chưa xác thực bắt đầu một cuộc trao đổi giới hạn?
- Cần xác nhận: Đối tượng người dùng, cơ chế xác thực và dữ liệu được phép xem trước/sau xác thực.
- Gợi ý thảo luận: Cho phép xem thông tin hướng dẫn công khai; yêu cầu xác thực trước khi truy cập dữ liệu khách hàng riêng.
- Trạng thái: OPEN.

**BIZ-102 — Định danh khách hàng dựa trên yếu tố nào?**  
- Câu hỏi: Tài khoản, email công ty, mã khách hàng, tenant hay tổ hợp các yếu tố?
- Cần xác nhận: Một người thuộc nhiều công ty/đơn vị có thể chuyển ngữ cảnh không; ai có quyền quản lý thành viên.
- Trạng thái: OPEN.

**BIZ-103 — Khi nào tiếp tục hội thoại cũ, khi nào tạo hội thoại mới?**  
- Câu hỏi: Tiếp tục theo phiên đăng nhập, theo vấn đề, theo khoảng thời gian hay do khách chủ động chọn?
- Cần xác nhận: Trường hợp khách quay lại sau khi đóng; hội thoại cũ đã Closed; vấn đề mới trong cùng phiên.
- Gợi ý thảo luận: Cho khách xem và chọn hội thoại cũ còn phù hợp, đồng thời có hành động rõ ràng để tạo yêu cầu mới.
- Trạng thái: OPEN.

**BIZ-104 — Một hội thoại có thể chứa nhiều vấn đề độc lập không?**  
- Câu hỏi: Nếu khách gửi yêu cầu kỹ thuật và yêu cầu nhân sự trong cùng cuộc trò chuyện, giữ chung hay tách Ticket?
- Cần xác nhận: Điều kiện tách, cách giữ liên kết tới hội thoại gốc và cách thông báo cho khách.
- Trạng thái: OPEN.

**BIZ-105 — Ai được xem lịch sử hội thoại?**  
- Câu hỏi: Khách có được xem tất cả hội thoại của mình không; người quản lý khách hàng có xem toàn bộ hội thoại của tổ chức không; nhân viên chỉ xem hội thoại được giao hay xem hàng đợi nhóm?
- Trạng thái: OPEN.

**BIZ-106 — Nếu khách gửi trùng hoặc gửi nhiều lần thì sao?**  
- Câu hỏi: Khi nội dung giống nhau hoặc khách nhấn gửi liên tiếp, hệ thống chỉ chống trùng kỹ thuật hay còn gộp yêu cầu theo nghiệp vụ?
- Lưu ý: Chống trùng thao tác gửi không đồng nghĩa tự động gộp hai yêu cầu giống nội dung.
- Trạng thái: OPEN.

### B. AI First Response và bàn giao người thật

**BIZ-201 — Phạm vi AI được phép trả lời là gì?**  
- Câu hỏi: AI chỉ hướng dẫn theo tài liệu đã duyệt hay được tra cứu thêm dữ liệu khách hàng/dịch vụ?
- Cần xác nhận: Nguồn tri thức được phép, dữ liệu nhạy cảm, các chủ đề cấm hoặc bắt buộc chuyển người.
- Trạng thái: OPEN.

**BIZ-202 — Trường hợp nào bắt buộc handoff?**  
- Câu hỏi: Khách yêu cầu người thật; AI không tìm được căn cứ; vấn đề nhạy cảm; khách không hài lòng; yêu cầu vượt quyền; nguy cơ SLA — trường hợp nào chuyển ngay?
- Gợi ý thảo luận: Yêu cầu gặp người thật được ưu tiên xử lý, không buộc khách tiếp tục hội thoại với AI.
- Trạng thái: OPEN.

**BIZ-203 — AI được hỏi bổ sung bao nhiêu lần?**  
- Câu hỏi: Khi thiếu thông tin, AI hỏi tiếp hay chuyển người sau một số vòng/điều kiện?
- Cần xác nhận: Ngưỡng số vòng, thời gian chờ và cách xử lý khi khách không trả lời.
- Trạng thái: OPEN.

**BIZ-204 — AI có được tự đánh dấu đã giải quyết không?**  
- Câu hỏi: AI chỉ đề xuất giải pháp hay có thể đưa hội thoại sang Resolved khi khách xác nhận?
- Cần xác nhận: Điều kiện bắt buộc, loại yêu cầu ngoại lệ và quyền mở lại.
- Trạng thái: OPEN.

**BIZ-205 — Khi AI provider lỗi thì trải nghiệm khách ra sao?**  
- Câu hỏi: Hiển thị thông báo và cho thử lại, tự chuyển người, hay tùy loại yêu cầu?
- Cần xác nhận: Có phân biệt lỗi tạm thời, hết hạn mức và dịch vụ không khả dụng không.
- Trạng thái: OPEN.

**BIZ-206 — Nhân viên có nhìn thấy nội dung AI tạo không?**  
- Câu hỏi: Bàn giao transcript đầy đủ, tóm tắt, nguồn tham khảo, dữ liệu đã thu thập và bước đã hướng dẫn ở mức nào?
- Lưu ý: Tóm tắt do AI tạo cần được nhận diện là nội dung AI, không coi là dữ kiện đã được nhân viên xác minh.
- Trạng thái: OPEN.

### C. Phân loại, định tuyến và phân công

**BIZ-301 — Danh mục nhóm hỗ trợ chính thức gồm những gì?**  
- Câu hỏi: Technical, Sales, HR, Other đã đủ chưa; có nhóm Billing, Account, Service Request hoặc Incident riêng không?
- Cần xác nhận: Chủ sở hữu từng nhóm, mô tả phạm vi và nhóm nhận các trường hợp không rõ.
- Trạng thái: OPEN.

**BIZ-302 — Ai chịu trách nhiệm chính khi yêu cầu vào hàng đợi?**  
- Câu hỏi: Primary chịu trách nhiệm theo dõi hay Assignee là người chịu trách nhiệm cuối cùng? Hai vai trò có thể khác nhau không?
- Trạng thái: OPEN.

**BIZ-303 — Khi nào dùng Primary, Specialist và Backup?**  
- Câu hỏi: Điều kiện chuyển từ Primary sang Backup; có cần Primary phê duyệt chuyển giao; ai chịu trách nhiệm nếu người nhận không nhận việc?
- Trạng thái: OPEN.

**BIZ-304 — Khi nào được coi là nhân viên không khả dụng?**  
- Câu hỏi: Nghỉ phép, ngoài giờ, trạng thái offline, quá tải, chưa nhận việc sau một khoảng thời gian hay không đủ quyền?
- Cần xác nhận: Nguồn dữ liệu xác định lịch làm việc/trạng thái và quy tắc fallback.
- Trạng thái: OPEN.

**BIZ-305 — Nếu không có mapping hoặc không ai nhận việc thì sao?**  
- Câu hỏi: Ai nhận cảnh báo Triage; thời hạn xử lý; ai có quyền sửa mapping và phân công thủ công?
- Nguyên tắc hiện có: Không gán ngẫu nhiên; phải có đường xử lý ngoại lệ.
- Trạng thái: OPEN.

**BIZ-306 — Một yêu cầu có thể có nhiều người xử lý không?**  
- Câu hỏi: Một Assignee chính với người phối hợp, hay nhiều Assignee cùng chịu trách nhiệm? Ai được gửi phản hồi khách?
- Trạng thái: OPEN.

**BIZ-307 — Ai được đổi nhóm, ưu tiên hoặc người xử lý?**  
- Câu hỏi: Nhân viên tự chuyển được không; cần lý do; có cần Supervisor duyệt; lịch sử nào cần lưu?
- Trạng thái: OPEN.

### D. Ticket và quy trình Bitrix24

**BIZ-401 — Đối tượng Bitrix24 nào là Ticket chính thức?**  
- Câu hỏi: Task, CRM entity, Smart Process hay module Ticket đang dùng?
- Cần xác nhận: Tên đối tượng thực tế, quyền API, trường bắt buộc, trạng thái và URL mở bản ghi.
- Trạng thái: OPEN — chặn chốt mapping production.

**BIZ-402 — Khi nào bắt buộc tạo Ticket?**  
- Câu hỏi: Mọi yêu cầu, chỉ yêu cầu cần người xử lý, hay tùy nhóm/loại yêu cầu?
- Cần xác nhận: Yêu cầu hỏi thông tin đơn giản, yêu cầu Sales/HR, sự cố và yêu cầu dịch vụ có cùng chính sách không.
- Trạng thái: OPEN.

**BIZ-403 — Một Conversation liên kết với bao nhiêu Ticket?**  
- Câu hỏi: Một-một, một-nhiều hay nhiều-một; điều kiện tách/gộp và xử lý khi Ticket hiện hữu đã đóng?
- Trạng thái: OPEN.

**BIZ-404 — Cách nhận diện Ticket hiện hữu để tránh trùng?**  
- Câu hỏi: Dùng mã Conversation, mã yêu cầu ngoài, khóa nghiệp vụ hay tổ hợp trường?
- Lưu ý: Không chỉ dựa vào nội dung giống nhau; phải có quy tắc xác định rõ ràng.
- Trạng thái: OPEN.

**BIZ-405 — Trường dữ liệu nào được đồng bộ?**  
- Câu hỏi: Tiêu đề, mô tả, khách hàng, dịch vụ, nhóm, người phụ trách, ưu tiên, SLA, transcript, file và liên kết — trường nào được tạo/cập nhật?
- Cần xác nhận: Trường nào chỉ đồng bộ một chiều, trường nào là nguồn chuẩn và dữ liệu nào không được đưa vào Bitrix24.
- Trạng thái: OPEN.

**BIZ-406 — Hệ thống nào là nguồn chuẩn cho từng thông tin?**  
- Câu hỏi: Bitrix24 quản lý trạng thái Ticket và assignee; Chat quản lý transcript và trạng thái AI — có ngoại lệ nào?
- Cần xác nhận: Khi dữ liệu hai bên khác nhau, dữ liệu nào thắng và ai xử lý xung đột.
- Trạng thái: OPEN.

**BIZ-407 — Nhân viên làm việc và trả lời khách ở đâu?**  
- Câu hỏi: Trả lời trực tiếp trên Support Dashboard hay làm việc chủ yếu trong Bitrix24?
- Nếu dùng cả hai: Có đồng bộ message hai chiều không; xử lý chỉnh sửa/xóa tin nhắn; xác định tác giả; chống vòng lặp đồng bộ như thế nào?
- Trạng thái: OPEN — quyết định ưu tiên P0.

**BIZ-408 — Khi Ticket bị xóa, đóng hoặc chuyển người trên Bitrix24 thì sao?**  
- Câu hỏi: Chat cập nhật theo, cảnh báo quản lý hay giữ trạng thái riêng? Ai được khôi phục liên kết?
- Trạng thái: OPEN.

**BIZ-409 — Khi Bitrix24 lỗi hoặc timeout thì ai xử lý?**  
- Câu hỏi: Yêu cầu được giữ ở trạng thái nào; có cho nhân viên tiếp tục xử lý; ai xem hàng đợi đối soát và khi nào cần thao tác thủ công?
- Trạng thái: OPEN.

### E. Ưu tiên, SLA và chuyển cấp

**BIZ-501 — Danh mục ưu tiên và điều kiện xác định là gì?**  
- Câu hỏi: Các mức ưu tiên; tác động kinh doanh; số người bị ảnh hưởng; dịch vụ trọng yếu; ai được nâng/hạ mức.
- Trạng thái: OPEN.

**BIZ-502 — SLA nào được cam kết?**  
- Câu hỏi: SLA phản hồi đầu tiên, thời gian nhận việc, thời gian cập nhật tiến độ và thời gian giải quyết có áp dụng riêng không?
- Cần xác nhận: Mỗi nhóm/dịch vụ/ưu tiên có mục tiêu nào; không tự đặt con số.
- Trạng thái: OPEN.

**BIZ-503 — Lịch tính SLA là gì?**  
- Câu hỏi: 24x7 hay giờ làm việc; múi giờ; ngày nghỉ; lịch riêng theo khách hàng/dịch vụ?
- Trạng thái: OPEN.

**BIZ-504 — Khi nào đồng hồ SLA tạm dừng hoặc tiếp tục?**  
- Câu hỏi: Chờ khách, chờ nhà cung cấp, chờ phê duyệt, ngoài giờ hay chuyển nhóm có tạm dừng/reset không?
- Cần xác nhận: Tách thời gian chờ khỏi thời gian xử lý và cách tính báo cáo.
- Trạng thái: OPEN.

**BIZ-505 — Cấp escalation và người nhận là ai?**  
- Câu hỏi: Cảnh báo trước hạn, khi vi phạm và sau vi phạm gửi cho Assignee, Primary, Supervisor hay quản lý khách hàng?
- Cần xác nhận: Mốc cảnh báo, thời gian chuyển cấp, người thay thế và điều kiện dừng.
- Trạng thái: OPEN.

**BIZ-506 — Thay đổi ưu tiên hoặc người xử lý ảnh hưởng SLA ra sao?**  
- Câu hỏi: Giữ nguyên mốc ban đầu, tính lại theo chính sách hay cần phê duyệt?
- Trạng thái: OPEN.

### F. Giải quyết, đóng, mở lại và phản hồi

**BIZ-601 — Điều kiện nào cho phép chuyển Resolved?**  
- Câu hỏi: Nhân viên xác nhận hoàn tất, khách xác nhận, có kết quả bắt buộc hay phụ thuộc loại yêu cầu?
- Trạng thái: OPEN.

**BIZ-602 — Điều kiện nào cho phép Closed?**  
- Câu hỏi: Đóng ngay khi Resolved, sau khi khách xác nhận, hay sau thời gian chờ? Ai có quyền đóng?
- Trạng thái: OPEN.

**BIZ-603 — Khách không phản hồi thì xử lý thế nào?**  
- Câu hỏi: Nhắc bao nhiêu lần, khoảng cách nhắc, thời điểm tự đóng và cách ghi lý do?
- Lưu ý: Chỉ cấu hình thời hạn sau khi được nghiệp vụ duyệt.
- Trạng thái: OPEN.

**BIZ-604 — Khi nào được mở lại?**  
- Câu hỏi: Khách trả lời trong hội thoại cũ, cùng lỗi tái diễn, ngoài thời hạn reopen hoặc Ticket Bitrix24 đã đóng — mở lại hay tạo yêu cầu mới?
- Trạng thái: OPEN.

**BIZ-605 — Đóng Conversation và đóng Ticket có liên quan thế nào?**  
- Câu hỏi: Hai trạng thái độc lập hay có quy tắc đồng bộ; nếu một bên đóng nhưng bên kia chưa đóng thì xử lý ra sao?
- Nguyên tắc hiện có: Không mặc định hai vòng đời là một.
- Trạng thái: OPEN.

**BIZ-606 — Khách đánh giá ở thời điểm nào?**  
- Câu hỏi: Sau mỗi yêu cầu hay theo chu kỳ; có cho đánh giá lại; cần điểm số, bình luận hay cả hai?
- Trạng thái: OPEN.

**BIZ-607 — Phản hồi xấu được xử lý thế nào?**  
- Câu hỏi: Điểm thấp có mở cảnh báo, yêu cầu Supervisor xem xét hoặc mở lại không? Ai chịu trách nhiệm và thời hạn xử lý?
- Trạng thái: OPEN.

### G. Thông báo và giao tiếp

**BIZ-701 — Những sự kiện nào cần thông báo?**  
- Câu hỏi: Yêu cầu mới, đã nhận, chuyển người, cần bổ sung, gần vi phạm SLA, Ticket lỗi đồng bộ, giải quyết, đóng và phản hồi xấu — sự kiện nào là bắt buộc?
- Trạng thái: OPEN.

**BIZ-702 — Thông báo qua kênh nào?**  
- Câu hỏi: Thông báo trong Dashboard, Bitrix24, email hay kênh khác; kênh chính và kênh dự phòng?
- Trạng thái: OPEN.

**BIZ-703 — Ai nhận thông báo và có cơ chế xác nhận không?**  
- Câu hỏi: Assignee, Primary, Backup, Supervisor hay khách; sự kiện nào cần xác nhận đã nhận?
- Trạng thái: OPEN.

**BIZ-704 — Nội dung nào được phép đưa vào thông báo?**  
- Câu hỏi: Có hiển thị nội dung yêu cầu, thông tin cá nhân hoặc dữ liệu khách hàng không?
- Nguyên tắc: Không đưa ghi chú nội bộ hoặc dữ liệu nhạy cảm vào kênh không được phép.
- Trạng thái: OPEN.

### H. File, dữ liệu, quyền riêng tư và kiểm toán

**BIZ-801 — Có cho phép đính kèm file không?**  
- Câu hỏi: Các định dạng, dung lượng tối đa, số lượng, cách tải xuống và nhóm người được xem là gì?
- Cần xác nhận: Quy trình kiểm tra mã độc, link hết hạn và cách xử lý file không hợp lệ.
- Trạng thái: OPEN.

**BIZ-802 — Dữ liệu được lưu trong bao lâu?**  
- Câu hỏi: Transcript, file, log, dữ liệu AI, dữ liệu Ticket và audit có cùng thời hạn không?
- Cần xác nhận: Quy trình xóa/ẩn danh, ngoại lệ phục vụ điều tra và người phê duyệt.
- Trạng thái: OPEN.

**BIZ-803 — Nội dung nào không được gửi cho AI?**  
- Câu hỏi: Dữ liệu cá nhân, thông tin xác thực, hợp đồng, dữ liệu nhân sự hoặc thông tin nhạy cảm nào phải che hoặc loại bỏ?
- Trạng thái: OPEN.

**BIZ-804 — Ai được xem ghi chú nội bộ và transcript đầy đủ?**  
- Câu hỏi: Phạm vi theo nhóm, khách hàng, vai trò; quản lý có quyền xem mọi ghi chú không?
- Trạng thái: OPEN.

**BIZ-805 — Sự kiện nào bắt buộc có audit?**  
- Câu hỏi: Đổi phân loại, đổi assignee, sửa ưu tiên, đóng/mở lại, thay quyền, xem/tải dữ liệu nhạy cảm, thay cấu hình và thao tác đối soát — mục nào cần lưu?
- Trạng thái: OPEN.

**BIZ-806 — Khách được thông báo thế nào về việc sử dụng AI và dữ liệu?**  
- Câu hỏi: Cần thông báo AI đang tham gia, mục đích xử lý dữ liệu, phạm vi lưu trữ và cách liên hệ hỗ trợ không?
- Cần phối hợp với chính sách bảo mật/quy định áp dụng của doanh nghiệp.
- Trạng thái: OPEN.

### I. Quản trị hệ thống và quản lý thay đổi

**BIZ-901 — Ai là chủ sở hữu từng cấu hình?**  
- Câu hỏi: Ai quản lý danh mục nhóm, nhân sự, mapping, SLA, AI provider, nguồn tri thức, mẫu thông báo và quyền?
- Trạng thái: OPEN.

**BIZ-902 — Thay đổi mapping có hiệu lực khi nào?**  
- Câu hỏi: Áp dụng ngay hay theo thời điểm; yêu cầu đang mở giữ người cũ hay tính lại theo mapping mới?
- Trạng thái: OPEN.

**BIZ-903 — Có cần phê duyệt cấu hình nhạy cảm không?**  
- Câu hỏi: Thay đổi quyền, SLA, routing, prompt AI hoặc Bitrix24 connection có cần người thứ hai duyệt không?
- Trạng thái: OPEN.

**BIZ-904 — Cần hỗ trợ những ngôn ngữ và trình duyệt nào?**  
- Câu hỏi: Tiếng Việt/tiếng Anh; phiên bản trình duyệt; nền tảng hệ điều hành và cách phát hành Extension.
- Trạng thái: OPEN.

**BIZ-905 — Khi hệ thống bảo trì hoặc gián đoạn thì quy trình nào áp dụng?**  
- Câu hỏi: Khách thấy thông báo gì; có kênh tiếp nhận dự phòng; yêu cầu đang mở được xử lý và thông báo lại ra sao?
- Trạng thái: OPEN.

### J. Báo cáo, KPI và chất lượng dịch vụ

**BIZ-1001 — Quản lý cần xem những báo cáo nào?**  
- Gợi ý thảo luận: Tổng yêu cầu theo thời gian/khách hàng/nhóm/loại; tồn đọng; thời gian phản hồi; thời gian giải quyết; tỷ lệ SLA; yêu cầu quá hạn; tỷ lệ handoff; lỗi đồng bộ; đánh giá khách hàng.
- Cần xác nhận: Bộ báo cáo bắt buộc, người xem, kỳ báo cáo và cách lọc.
- Trạng thái: OPEN.

**BIZ-1002 — Định nghĩa chỉ số thời gian thế nào?**  
- Câu hỏi: Thời gian phản hồi tính từ lúc gửi hay lúc backend nhận; thời gian giải quyết có trừ thời gian chờ khách; dùng giờ lịch hay giờ làm việc?
- Trạng thái: OPEN.

**BIZ-1003 — Đo chất lượng AI bằng cách nào?**  
- Câu hỏi: Tỷ lệ tự giải quyết, tỷ lệ handoff, khách đánh giá, tỷ lệ câu trả lời phải sửa, độ chính xác theo mẫu kiểm tra — chỉ số nào được dùng?
- Lưu ý: Không coi tỷ lệ tự giải quyết cao là tốt nếu khách không thực sự được giải quyết.
- Trạng thái: OPEN.

**BIZ-1004 — Đánh giá hiệu suất nhân viên ra sao?**  
- Câu hỏi: Số lượng yêu cầu, chất lượng, SLA, phản hồi khách, độ phức tạp và công việc phối hợp có được tính không?
- Cần xác nhận: Tránh dùng một chỉ số đơn lẻ để đánh giá nhân viên hoặc khuyến khích đóng yêu cầu sớm.
- Trạng thái: OPEN.

**BIZ-1005 — Có cần báo cáo theo khách hàng/hợp đồng dịch vụ không?**  
- Câu hỏi: Có theo dõi phạm vi dịch vụ, cam kết SLA, chi phí hoặc phân loại yêu cầu theo hợp đồng không?
- Trạng thái: OPEN.

### K. Ngoại lệ và xử lý sự cố nghiệp vụ

**BIZ-1101 — Nếu nhân viên nhận việc nhưng không cập nhật thì sao?**  
- Câu hỏi: Khi nào cảnh báo; có tự chuyển Backup; ai quyết định điều phối lại?
- Trạng thái: OPEN.

**BIZ-1102 — Nếu khách và nhân viên gửi tin nhắn đồng thời thì thứ tự hiển thị thế nào?**  
- Câu hỏi: Có cần thông báo tin mới, đánh dấu đã đọc, hoặc xử lý khi người khác đã trả lời từ Bitrix24?
- Trạng thái: OPEN.

**BIZ-1103 — Nếu Ticket tạo thành công nhưng Chat không nhận được phản hồi thì sao?**  
- Câu hỏi: Ai kiểm tra đối soát; khách thấy trạng thái gì; khi nào cho phép thao tác lại?
- Nguyên tắc hiện có: Không thông báo thành công khi chưa xác minh; không tạo lại mù quáng sau timeout.
- Trạng thái: OPEN.

**BIZ-1104 — Nếu khách gửi yêu cầu trùng lặp hoặc yêu cầu có liên quan thì sao?**  
- Câu hỏi: Gắn quan hệ, hợp nhất, đóng như trùng hay xử lý riêng; ai có quyền quyết định?
- Trạng thái: OPEN.

**BIZ-1105 — Khi phát hiện gửi nhầm dữ liệu cho khách thì quy trình xử lý là gì?**  
- Câu hỏi: Ai nhận cảnh báo; cách thu hồi/giảm tác động; lưu bằng chứng; thông báo người chịu trách nhiệm và khách hàng theo chính sách nào?
- Trạng thái: OPEN.

**BIZ-1106 — Khi có khiếu nại hoặc sự cố nghiêm trọng thì chuyển cấp ra sao?**  
- Câu hỏi: Ai là đầu mối; có quy trình riêng cho sự cố an ninh, mất dữ liệu hoặc dịch vụ trọng yếu không?
- Trạng thái: OPEN.

## 4. Quy tắc nghiệp vụ tối thiểu cần thống nhất

Trước khi nghiệm thu nghiệp vụ, cần có câu trả lời được phê duyệt cho các quy tắc sau:

1. Mỗi yêu cầu có một mã định danh và lịch sử truy vết.
2. Xác định rõ chủ sở hữu yêu cầu tại mọi trạng thái, kể cả Triage và chờ khách.
3. Phân biệt trạng thái Conversation, Ticket và trạng thái đồng bộ.
4. Quy định rõ một Conversation có thể gắn nhiều Ticket hay không.
5. Không xác nhận tạo Ticket thành công khi chưa được xác minh.
6. Quy định rõ ai được trả lời khách và kênh trả lời chính thức.
7. AI không được tự vượt quyền hoặc tự đóng yêu cầu ngoài policy.
8. Handoff phải giữ ngữ cảnh và xác định người/nhóm tiếp nhận.
9. Có quy tắc cho giờ ngoài ca, nhân sự vắng mặt và không ai nhận việc.
10. SLA có định nghĩa cách tính, tạm dừng, cảnh báo và chuyển cấp.
11. Có điều kiện giải quyết, đóng, mở lại và xử lý khách không phản hồi.
12. Có chính sách thông báo, file, dữ liệu lưu trữ và ghi chú nội bộ.
13. Có định nghĩa thống nhất cho các báo cáo/KPI.
14. Có người chịu trách nhiệm xử lý các ngoại lệ và lỗi đối soát.
15. Có tiêu chí nghiệm thu và bằng chứng kiểm thử cho từng quy tắc quan trọng.

## 5. Mẫu ghi nhận quyết định chính thức

Sao chép mẫu này cho mỗi quyết định. Không đánh dấu APPROVED nếu chưa có người có thẩm quyền xác nhận.

| Trường | Nội dung |
|---|---|
| Decision ID | Ví dụ: BIZ-407 |
| Câu hỏi | Ghi câu hỏi nghiệp vụ cần quyết định |
| Phương án được chọn | Ghi chính xác phương án |
| Lý do | Vì sao chọn phương án |
| Phạm vi áp dụng | Khách hàng/nhóm/phiên bản áp dụng |
| Ngoại lệ | Các trường hợp không theo quy tắc chung |
| Người đề xuất | Tên/vai trò |
| Người phê duyệt | Chủ nghiệp vụ có thẩm quyền |
| Ngày phê duyệt | YYYY-MM-DD |
| Trạng thái | OPEN / PROPOSED / APPROVED / OUT OF SCOPE / BLOCKED |
| Tài liệu bị ảnh hưởng | Requirement, flow, API, UI, test |
| Test nghiệm thu | Mã test chứng minh quy tắc |

## 6. Checklist xác nhận nghiệp vụ

- [ ] Đã xác định chủ nghiệp vụ/Product Owner.
- [ ] Đã chốt kênh làm việc chính của nhân viên.
- [ ] Đã chốt mô hình khách hàng, tổ chức và xác thực.
- [ ] Đã chốt vòng đời Conversation.
- [ ] Đã chốt danh mục nhóm và ma trận Primary/Specialist/Backup.
- [ ] Đã chốt điều kiện AI tiếp tục, hỏi bổ sung và handoff.
- [ ] Đã chốt loại đối tượng Ticket Bitrix24.
- [ ] Đã chốt quy tắc tạo/tách/gộp và đồng bộ Ticket.
- [ ] Đã chốt role/permission matrix.
- [ ] Đã chốt ưu tiên, SLA, lịch làm việc và escalation.
- [ ] Đã chốt điều kiện Resolved/Closed/Reopened.
- [ ] Đã chốt thông báo và phản hồi khách hàng.
- [ ] Đã chốt file, retention, privacy và audit policy.
- [ ] Đã chốt báo cáo/KPI và định nghĩa chỉ số.
- [ ] Đã chốt quy trình ngoại lệ và người chịu trách nhiệm.
- [ ] Đã liên kết các quyết định với yêu cầu và test case.
- [ ] Các quyết định P0 đã APPROVED trước khi khóa đặc tả nghiệp vụ.
- [ ] Product Owner xác nhận phiên bản nghiệp vụ dùng cho triển khai.

## 7. Tiêu chí hoàn tất rà soát nghiệp vụ

Không đánh giá nghiệp vụ là hoàn tất chỉ vì đã có sơ đồ luồng. Chỉ chốt khi:

- Tất cả quyết định P0 có trạng thái APPROVED hoặc được ghi rõ OUT OF SCOPE với lý do và người duyệt.
- Mọi câu hỏi OPEN còn lại đều có người chịu trách nhiệm và thời điểm dự kiến quyết định.
- Các quyết định đã duyệt được phản ánh nhất quán trong tài liệu nghiệp vụ, state transition, UI flow và acceptance tests.
- Có tình huống nghiệm thu cho luồng bình thường, lỗi, ngoại lệ và khôi phục.
- Product Owner ký xác nhận baseline nghiệp vụ.

## 8. Lịch sử thay đổi

| Phiên bản | Ngày | Thay đổi |
|---|---|---|
| 1.0 | 2026-10-20 | Tập hợp bộ câu hỏi nghiệp vụ, danh mục quyết định ưu tiên, checklist và mẫu phê duyệt. |

---
**Kết luận:** Đây là bộ câu hỏi để hoàn thiện nghiệp vụ, không phải tuyên bố rằng tất cả chính sách đã được quyết định. Hãy chốt các quyết định P0 trước, sau đó cập nhật tài liệu liên quan và kiểm thử theo quyết định đã duyệt.
