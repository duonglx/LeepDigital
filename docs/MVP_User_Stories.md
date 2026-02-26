# TÀI LIỆU YÊU CẦU NGƯỜI DÙNG (USER STORIES) - PHASE 1 MVP

**Dự án:** LEEP DIGITAL INFLOW CHANNELS
**Phân hệ áp dụng:** Mobile App (SOL), Landing Page (Web), CRM (VCRM), Gateway (ZNS/SMS), Core Banking (AITHER/PBD)

---

## EPIC 1: HỆ THỐNG MÃ GIỚI THIỆU TRÊN SOL APP
*Cung cấp công cụ chia sẻ để biến mọi khách hàng thành Digital Agent.*

### Feature 1.1: Tạo và chia sẻ mã QR Giới thiệu
* **[US1.1.1]** Là **Người dùng app SOL (Người giới thiệu)**, tôi muốn có một nút/chức năng "Mời bạn bè / Tạo mã QR giới thiệu" trên app để tôi có thể bắt đầu quá trình giới thiệu khách hàng mới.
* **[US1.1.2]** Là **Hệ thống SOL App**, khi người dùng bấm tạo mã, tôi cần tự động sinh ra một **mã QR tĩnh (Permanent)** và một **đường link gốc (URL)**. Trong mã QR/URL này phải chứa tham số `encrypted_cif` (được mã hoá từ số CIF gốc của khách hàng) để đảm bảo không bị lộ CIF ra ngoài.
* **[US1.1.3]** Là **Người dùng app SOL**, tôi muốn có thể lưu ảnh mã QR xuống Thư viện ảnh (Gallery) hoặc bấm nút "Chia sẻ" để gửi trực tiếp hình ảnh/link qua các ứng dụng OTT (Zalo, Facebook Messenger) nhằm thao tác nhanh chóng.

---

## EPIC 2: LANDING PAGE & HỆ THỐNG ANTI-BOT
*Nơi khách hàng mới (Referred User) nhập thông tin và đăng ký sản phẩm.*

### Feature 2.1: Truy cập và Định danh Nguồn Giới Thiệu
* **[US2.1.1]** Là **Khách hàng được giới thiệu**, khi tôi dùng điện thoại quét mã QR hoặc bấm vào Link, tôi muốn được điều hướng ngay đến một Landing Page chuẩn Mobile-friendly của Shinhan Bank để tìm hiểu dịch vụ.
* **[US2.1.2]** Là **Backend của Landing Page**, khi nhận được Request truy cập chứa tham số `encrypted_cif`, tôi cần dùng Private Key nội bộ để giải mã thành số CIF gốc. Thông tin này sẽ được lưu ở Session/State để đính kèm vào dữ liệu đăng ký mà không hiển thị cho người truy cập thấy.

### Feature 2.2: Đăng ký Dịch vụ & Chống Spam
* **[US2.2.1]** Là **Khách hàng được giới thiệu**, tôi muốn chọn Loại dịch vụ mong muốn (Mở máy POS, Mở thẻ tín dụng, Vay vốn, Mở tài khoản) và nhập các thông tin cơ bản: *Họ và tên, Số điện thoại, Email, Tỉnh/Thành phố, Phường/Xã cư trú*.
* **[US2.2.2]** Là **Hệ thống Landing Page**, tôi cần tích hợp cơ chế chống Spam **Invisible Captcha / Smart Anti-bot**. Tôi sẽ giám sát IP và hành vi (thời gian lưu lại trang, tốc độ điền form). Nếu khách hàng là người thật, tôi sẽ cho Submit form trơn tru mà không cần click chọn Captcha.
* **[US2.2.3]** Là **Hệ thống Landing Page**, nếu tôi phát hiện hành vi bất thường (quá nhiều request từ 1 IP trong 5 phút, form điền < 2 giây), tôi sẽ chặn lại và hiển thị thử thách Captcha hình ảnh để xác minh người dùng, nhằm bảo vệ hệ thống VCRM không bị ddos/spam rác.
* **[US2.2.4]** Là **Khách hàng được giới thiệu**, sau khi đăng ký thành công, tôi muốn nhận được một thông báo xác nhận trên màn hình (Success Screen) cảm ơn tôi đã đăng ký dịch vụ.

---

## EPIC 3: TƯƠNG TÁC VCRM & HỆ THỐNG THÔNG BÁO CHỦ ĐỘNG
*Kiểm soát luồng Lead và trải nghiệm chăm sóc khách hàng.*

### Feature 3.1: Giao tiếp Hệ thống & Cảnh báo (Notification)
* **[US3.1.1]** Là **Backend của Landing Page**, ngay khi nhận được Data khách hàng đăng ký thành công, tôi cần gọi API đẩy gói tin (Họ Tên, SĐT, Email, Tỉnh/TP, Dịch vụ, CIF_Người_Giới_Thiệu) sang **VCRM** với trạng thái `New Lead`.
* **[US3.1.2]** Là **Hệ thống ZNS/SMS Gateway**, ngay khi hệ thống báo ghi nhận thành công, tôi phải gửi 1 tin nhắn vào Zalo/SMS của Khách hàng được giới thiệu với nội dung: *"Shinhan Bank đã nhận được yêu cầu đăng ký [Dịch vụ] của Quý khách"*.
* **[US3.1.3]** Là **Hệ thống ZNS/SMS Gateway**, tôi cũng cần gửi 1 tin nhắn Zalo/SMS thông báo cho Người giới thiệu (Dùng CIF và SĐT tra xuất từ Core): *"Bạn vừa giới thiệu thành công 1 khách hàng đăng ký dịch vụ của Shinhan Bank. Hệ thống đang tiến hành xử lý"*.

### Feature 3.2: Bộ phận Telesale & Quản lý Chi nhánh
* **[US3.2.1]** Là **Nhân viên Telesale (Hội sở)**, tôi muốn thấy danh sách các thẻ Lead mới đổ về VCRM theo thời gian thực để tiến hành gọi điện thoại lọc Lead theo chuẩn SLA (ví dụ: 04 giờ làm việc).
* **[US3.2.2]** Là **Nhân viên Telesale**, sau khi gọi điện, tôi muốn được phép điền bổ sung thêm các trường: *Địa chỉ chi tiết, Nhu cầu cụ thể*, và Assign (chỉ định) Lead này sang cho **Chi nhánh trực thuộc** phù hợp trên VCRM.
* **[US3.2.3]** Là **Giám đốc/Quản lý Chi nhánh**, tôi muốn danh sách khách hàng Telesale assign được đẩy tự động vào Pool (rổ) khách hàng của chi nhánh tôi, để tôi phân bổ chuyển tiếp cho **RM (Relationship Manager)** phù hợp.

### Feature 3.3: RM và Chốt Sale (Closed Won)
* **[US3.3.1]** Là **Nhân viên RM**, tôi muốn nhận được Notification hoặc thấy Task mới giao trên VCRM để bắt đầu đi gặp khách hàng thu thập hồ sơ. Trạng thái Lead lúc này chuyển thành `Processing`.
* **[US3.3.2]** Là **Nhân viên RM**, TÔI BẮT BUỘC phải tạo các Note (ghi chú) trên từng bước tương tác với khách hàng (Đã gặp, Đang bổ sung giấy tờ...) trên file Lead của VCRM để bộ phận Hội sở có thể theo dõi.
* **[US3.3.3]** Là **Nhân viên RM**, khi khách hàng hoàn tất việc phát hành Thẻ/Vay/Mở POS, tôi muốn có thể thay đổi trạng thái Lead thành `Closed Won / Thành công`. (Tương tự, đổi thành `Rejected / Thất bại` nếu hồ sơ bị từ chối kèm lý do).

---

## EPIC 4: RULE ENGINE & TRẢ THƯỞNG HOA HỒNG (COMMISSION)
*Xử lý hạch toán độc lập.*

### Feature 4.1: Đối soát và Xử lý Hoa hồng
* **[US4.1.1]** Là **Hệ thống Rule Engine (AITHER) hoặc Bộ phận PBD (Hội Sở)**, hàng ngày/tháng, tôi cần lấy danh sách toàn bộ các Lead có trạng thái `Closed Won` từ VCRM và trích xuất thông tin `CIF_Người_Giới_Thiệu`.
* **[US4.1.2]** Là **Hệ thống Rule Engine (hoặc Nhân viên PBD)**, tôi sẽ tính toán số tiền hoa hồng cần trả dựa trên Bộ Rule ngân hàng (Ví dụ: Thẻ = xx VNĐ, POS = yy VNĐ, Vay = zz% giá trị).
* **[US4.1.3]** Là **Hệ thống Core Banking / Kế toán**, tôi muốn tự động hoá việc hạch toán tiền hoa hồng thẳng vào Tài khoản thanh toán (CASA) của Người giới thiệu (theo CIF) một cách minh bạch.
* **[US4.1.4]** Là **Hệ thống ZNS/SMS Gateway**, sau khi tiền được hạch toán, tôi cần phát đi thông báo Zalo/SMS cho Người giới thiệu báo tin: *"Chúc mừng, bạn vừa nhận được [Số tiền] VNĐ hoa hồng từ việc giới thiệu khách hàng..."* để khích lệ động lực (Incentive).

---
*(End of Document)*
