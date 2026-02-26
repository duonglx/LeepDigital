# PROPOSAL FOUNDATION: SHINHAN BANK VIETNAM - CUSTOMER 360 & DIGITAL INFLOW TRANSFORMATION

## 1. BỐI CẢNH & HIỆN TRẠNG (AS-IS)
**1.1. Hạ tầng Dữ liệu (Infrastructure & Integration)**
* **Data Silos:** Dữ liệu phân mảnh, rải rác ở nhiều hệ thống (Core Banking, Core Thẻ, CRM).
* **Customer Mapping:** Đang map khách hàng thủ công giữa các hệ thống bằng CIF và CMND/CCCD (Dễ gặp lỗi trùng lặp/Duplicate khi khách hàng đổi giấy tờ). Chưa có Master ID (Single Customer View).
* **Data Latency:** Dữ liệu đẩy về Data Warehouse là T+1 (chạy batch cuối ngày), thiếu khả năng phản ứng theo thời gian thực (Real-time).
* **Unstructured Data:** Chưa khai thác dữ liệu phi cấu trúc (App Logs, Voice, Chat).
* **Governance:** Môi trường tuân thủ bảo mật cao. Có khả năng triển khai Data Lakehouse và LLM On-premise (trong nội bộ ngân hàng).

**1.2. Nghiệp vụ & Vấn đề (Business Pain Points)**
* **Segmentation:** Đang phân khúc theo tiêu chí tĩnh (Nhân khẩu học, số dư bình quân).
* **Pain points lớn nhất:** Tỷ lệ khách hàng rời bỏ (Churn) cao, tỷ lệ chuyển đổi (Conversion rate) thấp, khó bán chéo (Cross-sell) thẻ tín dụng hoặc các khoản vay cho khách hàng hiện hữu (ví dụ khách gửi tiết kiệm).

---

## 2. CHIẾN LƯỢC MỚI: DỰ ÁN "LEEP DIGITAL INFLOW CHANNELS"
**Mục tiêu cốt lõi:** Chuyển đổi các điểm chạm vật lý (53 chi nhánh, 600 RMs, 6.000 Merchants) thành các kênh thu hút khách hàng số hóa, có thể theo dõi (traceable) và xây dựng bộ máy bán hàng tự tiến hóa dựa trên AI.

**Cơ chế hoạt động (The Virtuous Cycle):**
`Offline → Digital Conversion → CRM Accumulation → AI-Driven Follow-up Sales`

**2.1. Các thành phần chính của hệ thống:**
* **Digital Business Cards (QR):** Gắn mã QR định danh cho từng RM, nhân viên chi nhánh, chủ Merchant, nhân viên Merchant. Đảm bảo mọi luồng khách hàng (inflow) đều được ghi nhận nguồn gốc.
* **Landing Platform (Conversion Hub):** Nơi khách hàng quét QR bước vào để xem sản phẩm, mở tài khoản, đăng ký thẻ. Mọi hành vi (Click, Dwell time, Drop-off) đều được ghi nhận làm dữ liệu nền tảng.
* **AI-Driven Growth Engine (Hybrid AI):**
    * *Predictive AI (Machine Learning):* Phân tích hành vi, chấm điểm xác suất chuyển đổi, phát hiện rủi ro rời bỏ (Churn), và tạo Next Best Action (NBA).
    * *Generative AI (LLMs):* Tạo kịch bản bán hàng (Sales scripts), tóm tắt insight khách hàng, hướng dẫn hành động cho RMs.

**2.2. Chiến lược ứng dụng theo phân khúc (Segments):**
* **Retail/SOHO/Corporate:** RMs dùng QR thu thập lead -> Đổ về CRM -> AI phân tích nhu cầu (CASA, Loan, Card) -> Đẩy task/action list lại cho RMs.
* **Mũi nhọn - 6.000 Merchants:** Chuyển đổi Merchant từ "Kênh thanh toán" thành "Kênh thu hút khách hàng" (Acquisition Channel). Khi khách thanh toán tại POS/Quán, nhân viên quán mời quét QR. Dữ liệu đổ về CRM, kích hoạt trả thưởng (Incentive) cho nhân viên quán và RM quản lý Merchant đó.

---

## 3. TƯ VẤN KIẾN TRÚC & ĐIỀU CHỈNH KỸ THUẬT (TO-BE)

Để dự án "Leep Digital Inflow" hoạt động trơn tru trên nền hạ tầng T+1 hiện tại, cần áp dụng các chiến lược sau:

**3.1. Giải quyết Nút thắt Định danh (Identity Resolution)**
* *Vấn đề:* Khách quét QR vào Landing Page sẽ là khách ẩn danh.
* *Giải pháp:* Tích hợp Mini-eKYC, OCR giấy tờ hoặc OTP qua Số điện thoại tại Landing Platform. Xây dựng **Identity Graph** tại Data Lakehouse để tự động map/merge dữ liệu (Fuzzy Matching) tạo ra Master ID dùng chung cho AI, vượt qua giới hạn của CIF/CMND truyền thống.

**3.2. Vượt qua giới hạn Data Latency (T+1)**
* *Vấn đề:* Core banking chậm cập nhật dữ liệu.
* *Giải pháp:* Lấy tín hiệu Real-time trực tiếp từ **App Logs / Landing Page Events** (Event-driven architecture). Hành vi quét QR/Click chọn sản phẩm là dữ liệu thời gian thực giúp AI chấm điểm và kích hoạt thông báo (Trigger) ngay lập tức mà không cần đợi Core Banking.

**3.3. Xây dựng Vòng lặp Phản hồi (Feedback Loop) cho AI**
* *Vấn đề:* AI không thể "tự tiến hóa" nếu không biết kết quả thực thi.
* *Giải pháp:* RMs bắt buộc phải cập nhật Trạng thái xử lý (Disposition Codes - ví dụ: Khách chê lãi suất cao, Khách đã mở thẻ) trên CRM. Dữ liệu này quay lại train AI model.

**3.4. Triển khai Phase 1 cho Merchant POS**
* *Thực tế:* Tích hợp API sâu vào máy POS của nhiều hãng là rất phức tạp.
* *Workaround:* Dùng **Dynamic QR Standee** hoặc QR trên điện thoại nhân viên quán. Ưu tiên xây dựng luồng trả thưởng hoa hồng ngay lập tức (Instant Gratification/Referral System) để tạo động lực cho nhân viên Merchant.

---

## 4. LỘ TRÌNH TRIỂN KHAI DỰ KIẾN (IMPLEMENTATION PHASING)
* **Phase 1 (Foundation):** Triển khai Digital QR định danh, Landing platform, Luồng auto-transfer data về CRM, và Dashboard theo dõi inflow cơ bản. Thiết lập Pilot Project: "Credit Line Pre-Approval Engine" dựa trên dữ liệu hiện tại để ra Quick-win.
* **Phase 2 (Merchant Ecosystem):** Triển khai mô hình nhân viên Merchant (Quét QR - Mở sản phẩm - Nhận hoa hồng). Hoàn thiện hệ thống Incentive structure.
* **Phase 3 (AI Intelligence):** Đưa Advanced AI (Predictive + Generative) vào phân tích Next Best Action, chấm điểm tự động đa phân khúc (Retail, SOHO, Corporate).