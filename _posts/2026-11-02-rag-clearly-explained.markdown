Product Requirements Document (PRD) & Product Brief

Dự Án: TalentAgent AI — Trợ Lý AI Agent Thẩm Định & Đánh Giá CV Kỹ Thuật Đa Chiều



1. Tổng Quan Dự Án (Executive Summary & Product Brief)





Tên sản phẩm: TalentAgent AI (v4.2)



Slogan / Định vị: Trợ lý AI Agent thẩm định CV Kỹ thuật chuyên sâu — Minh bạch dữ liệu, hỗ trợ hội đồng, Con người quyết định sau cùng (Human-in-the-loop).



Mục tiêu cốt lõi: Khắc phục các hạn chế nghiêm trọng của các công cụ lọc CV truyền thống (ATS dựa trên từ khóa ngô nghê hoặc mô hình AI "hộp đen" thiên lệch, ảo giác). TalentAgent AI cung cấp bộ công cụ thẩm định có cấu trúc, bóc tách minh chứng thực chiến, bảo vệ tính công bằng (fairness) và tạo điều kiện cho hội đồng phỏng vấn đưa ra quyết định dựa trên bằng chứng xác thực (evidence-based).



2. Bối Cảnh & Vấn Đề Cần Giải Quyết (Problem Statement)





CV Kỹ thuật bị phóng đại & thiếu bằng chứng xác thực:





Ứng viên thường liệt kê hàng loạt buzzwords (Kubernetes, Microservices, Distributed Systems, High Availability) nhưng không nêu rõ vai trò thực tế, tải hệ thống (RPS/TPS), hoặc chỉ số đo lường hiệu năng.



Tech Lead tốn trung bình 20–30 phút/CV chỉ để đối chiếu và kiểm tra tính hợp lý của kiến trúc dự án.



Hiện tượng thiên lệch nhận thức & Hiệu ứng bầy đàn (Bandwagon Effect):





Trong hội đồng phỏng vấn kỹ thuật, ý kiến của thành viên có Seniority cao nhất hoặc người phát biểu đầu tiên dễ định hướng toàn bộ kết quả của các thành viên khác, dẫn tới đánh giá thiếu khách quan.



Lãng phí ứng viên giỏi do góc nhìn đơn lẻ (Single-Team Bias):





Ứng viên ứng tuyển Backend có thể không đạt chuẩn kiến trúc phân tán của Core team nhưng lại sở hữu nền tảng Data pipeline hoặc DevOps/SRE cực kỳ phù hợp cho team Infra hoặc Data Platform.



Phỏng vấn hành vi & tính cách mang tính hình thức:





Câu hỏi phỏng vấn thường rập khuôn, thiếu ngữ cảnh thực tế từ chính dự án quá khứ mà ứng viên đã ghi trong CV.



3. Đối Tượng Người Dùng Mục Tiêu (Target Personas)





Persona

Vai trò chính

Nhu cầu then chốt

Điểm chạm tính năng



Tech Lead / Engineering Manager

Đánh giá năng lực chuyên môn, quyết định vòng phỏng vấn chuyên sâu

Cần bóc tách năng lực thực chiến, cảnh báo kỹ năng thổi phồng, đối chiếu Must-have vs Nice-to-have

5-Stage AI Audit Workspace, Candidate Search Bar



Thành Viên Hội Đồng Tuyển Dụng (Interview Committee)

Bỏ phiếu biểu quyết ứng viên, thảo luận chuyên môn

Cần không gian bỏ phiếu ẩn danh, công bằng, đọc chuỗi suy luận (Chain-of-Thought) của AI

Hội Đồng Bỏ Phiếu Ẩn Danh (Zero-Knowledge Vote)



Head of Engineering / Tech Director

Điều phối nhân sự đa phòng ban, tối ưu nguồn lực

Muốn phát hiện ứng viên phù hợp chéo (Cross-team fit), giảm lãng phí ứng viên tiềm năng

Phân Bổ Tech Domain & Nhiệm Vụ 30-60-90



Talent Acquisition (Tech HR / Recruiter)

Tiếp nhận hồ sơ, sàng lọc ban đầu, tổ chức phỏng vấn

Gợi ý bộ câu hỏi phỏng vấn STAR thực tế, trích xuất tài liệu checklist tuyển dụng chuẩn mực

Cổng Nhập Liệu Main Intake, Bộ Câu Hỏi STAR, Checklist PDF



4. Kiến Trúc & Luồng Tính Năng Cốt Lõi (Core Feature Specifications)

4.1. Cổng Nhập Liệu & Trung Tâm Điều Khiển (Main Intake Hub)





Mục tiêu: Điểm chạm đầu tiên tinh gọn, trực quan, hỗ trợ đa dạng phương thức tiếp nhận hồ sơ.



Tính năng chi tiết:





Nhập liệu linh hoạt: Kéo thả file (.PDF, .DOCX, .TXT tối đa 25MB), dán văn bản / link GitHub/LinkedIn, hoặc chọn nhanh 3 hồ sơ mẫu (Go Tech Lead, DevOps K8s, Lead Data/ML).



Tự động làm sạch dữ liệu PII (PII Redaction: che mờ số điện thoại, địa chỉ cá nhân, tuổi tác, hình ảnh nhằm chống vô thức thiên vị).



Khởi chạy dàn 5 Sub-Agents thẩm định song song với thời gian phản hồi nhanh (~15 giây).



Thanh tìm kiếm nhanh ứng viên (⌘K) với truy vấn đa thông tin (tên, tech stack, số lượng hồ sơ đã duyệt).

4.2. Không Gian Thẩm Định CV 5 Bước (5-Stage AI Audit Workspace)





Mục tiêu: Bóc tách toàn diện hồ sơ kỹ thuật theo 5 phân đoạn minh bạch:





Stage 1 - Trích Xuất & Cấu Trúc Hóa: Định lượng chỉ số dự án (RPS, Latency, Data Volume, SLA).



Stage 2 - Thẩm Định Năng Lực Cốt Lõi (Must-have vs Nice-to-have): So sánh trực diện với chuẩn mực JD yêu cầu.



Stage 3 - Cảnh Báo Phóng Đại & Kiểm Tra Tính Khả Thi: Phát hiện độ vênh giữa số năm kinh nghiệm và chiều sâu công nghệ.



Stage 4 - Kiểm Định Bằng Chứng (Evidence Grounding): Trích dẫn trực tiếp dòng văn bản nguồn trong CV để đối chứng, loại trừ ảo giác (Hallucination).



Stage 5 - Đánh Giá Tính Công Bằng & Tuân Thủ (Fairness & Bias Audit): Đảm bảo không có yếu tố kỳ thị trường học, vùng miền, giới tính.

4.3. Hội Đồng Phỏng Vấn: Bỏ Phiếu Ẩn Danh & Chain-of-Thought





Mục tiêu: Bảo đảm tính độc lập trong quyết định của hội đồng, ngăn chặn hiệu ứng bầy đàn.



Tính năng chi tiết:





Cơ chế Zero-Knowledge Blind Review (Mã hóa SHA-256): Các thành viên không thể thấy điểm số và nhận xét của người khác cho đến khi toàn bộ thành viên nộp phiếu hoặc đến hạn deadline.



Hiển thị chuỗi suy luận từng bước (Chain-of-Thought Transparency): Minh bạch lý do tại sao AI chấm điểm hoặc đưa ra cảnh báo.



Phân quyền biểu quyết nghiêm ngặt: Chỉ thành viên hội đồng được gán quyền mới có nút Vote; quản trị viên có thể theo dõi tiến độ nộp phiếu thời gian thực (Consensus Tracking).

4.4. Bộ Câu Hỏi Phỏng Vấn Tính Cách & Hành Vi (STAR & DISC)





Mục tiêu: Biến CV thành ngân hàng câu hỏi tình huống có ngữ cảnh rõ ràng.



Tính năng chi tiết:





Sinh câu hỏi chuẩn khung STAR (Situation - Task - Action - Result) dựa trên chính sự cố/thành tích ghi trong CV ứng viên.



Bộ tiêu chí nhận diện phản ứng: Red Flags (đùn đẩy trách nhiệm, phóng đại đóng góp cá nhân) và Green Flags (tư duy giải quyết vấn đề, tinh thần ownership, khiêm tốn học hỏi).



Phân tích xu hướng phong cách làm việc (DISC / Cultural Fit) hỗ trợ hội đồng đánh giá khả năng hòa nhập văn hóa kỹ thuật.

4.5. Phân Bổ Tech Domain & Nhiệm Vụ 30-60-90 Ngày





Mục tiêu: Đánh giá độ phù hợp của ứng viên trên nhiều phòng ban công nghệ khác nhau trong tổ chức.



Tính năng chi tiết:





Ma trận đo lường mức độ tương thích kỹ năng đa đội ngũ (Backend Core, DevOps & SRE, Data Engineering, AI/ML Platform).



Đề xuất phỏng vấn kép (Dual-Interview Invitation) nếu phát hiện ứng viên có tiềm năng lớn ở domain thứ hai.



Lộ trình thử việc tự sinh 30-60-90 Ngày: Đề xuất cụ thể mục tiêu bàn giao (Deliverables) và thước đo KPI trong 3 tháng đầu.

4.6. Tài Liệu Hướng Dẫn Sử Dụng & Checklist PDF Dành Cho Tech Lead





Mục tiêu: Chuẩn hóa quy trình vận hành và nâng cao năng lực thẩm định cho toàn bộ đội ngũ Tech Leads.



Tính năng chi tiết:





Hướng dẫn trực quan về nguyên lý hoạt động của AI Agent: Minh bạch dữ liệu, loại bỏ hộp đen, người ra quyết định tối hậu.



Mẫu Checklist Thẩm Định CV chuẩn dạng Print/PDF Dossier: Bộ tiêu chuẩn 8 bước kiểm duyệt hồ sơ kèm ô ký duyệt (Digital Approval Signature) đảm bảo tính pháp lý tuyển dụng.



5. Yêu Cầu Phi Chức Năng (Non-Functional Requirements)





Hiệu năng & Tốc độ phản hồi (Performance & Latency):





Thời gian phân tích và bóc tách song song 5 Sub-Agents: < 20 giây / CV.



Độ trễ phản hồi giao diện tìm kiếm nhanh (⌘K): < 100ms.



Bảo mật & Tuân thủ (Security, Privacy & Compliance):





Tiêu chuẩn tuân thủ: Hướng tới chứng chỉ SOC-2 Type II và GDPR/CCPA.



Cơ chế bảo mật dữ liệu PII: Che giấu thông tin định danh cá nhân trước khi đưa vào LLM engine.



Quyền riêng tư của phiếu biểu quyết: Khóa mã hóa bất đối xứng, cam kết không can thiệp kết quả trước giờ công bố.



Tính minh bạch & Tin cậy (Explainability & Hallucination Mitigation):





Mọi kết luận thẩm định kỹ năng đều phải có liên kết trích xuất văn bản nguồn (Source Citation Anchor) tới CV gốc với độ tin cậy ≥ 98%.



Khả năng mở rộng (Scalability):





Hỗ trợ chế độ xử lý hồ sơ hàng loạt (Batch Intake) lên tới 50 CV cùng lúc cho các đợt tuyển dụng quy mô lớn.



6. Thước Đo Thành Công & KPIs (Success Metrics)





Hiệu suất thời gian: Giảm thời gian Tech Lead đọc và lọc CV kỹ thuật từ trung bình 25 phút xuống còn dưới 7 phút (tiết kiệm ~65% thời gian).



Độ chuẩn xác: Tỷ lệ ứng viên vượt qua vòng CV vào vòng phỏng vấn kỹ thuật đạt kết quả tốt tăng ≥ 35%.



Độ khách quan của hội đồng: Giảm độ lệch kết quả vote giữa người đầu tiên và người sau cùng xuống dưới 15% (loại bỏ hiệu ứng tâm lý bầy đàn).



Tỷ lệ giữ lại nhân tài phù hợp chéo: Cứ 10 hồ sơ trượt ở team chính thì ít nhất 2 hồ sơ được tiếp nhận và phỏng vấn thành công ở team domain thay thế.



7. Lộ Trình Phát Triển Sản Phẩm (Product Roadmap)





Giai đoạn 1 (Hiện tại - v4.2): 





Hoàn thiện trọn bộ giao diện 5 phân hệ chuyên sâu, cổng nhập liệu Main Intake Hub, thanh TopBar chuẩn hóa đồng bộ với Search nhanh, bộ câu hỏi STAR và Checklist PDF Dossier.



Giai đoạn 2 (v4.3 - v4.5):





Tích hợp kết nối trực tiếp kho mã nguồn (GitHub / GitLab API) để thẩm định commit history và chất lượng pull request thực tế của ứng viên.



Bổ sung webhook tự động thông báo kết quả vote hội đồng qua Slack / Microsoft Teams / Lark.



Giai đoạn 3 (v5.0 Enterprise):





Tích hợp 2 chiều với các nền tảng ATS lớn (Greenhouse, Lever, Workable, BambooHR).



Tự động ghi âm, bóc băng và chấm điểm tự động buổi phỏng vấn trực tiếp đối chiếu lại với cam kết trong CV.

