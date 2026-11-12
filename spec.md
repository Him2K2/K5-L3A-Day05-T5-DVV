# Bài nộp LAB5 — T5 · Ví điện tử · Nhóm [điền tên nhóm]

**Lớp:** L3A · **Bàn / zone:** B3 · **Ngày:** 12/11/2026  
**Thành viên:** [điền họ tên, mã học viên và phần việc của từng thành viên trước khi nộp]

| Họ và tên | Mã học viên | Việc đã làm |
|---|---|---|
| Hồ Xuân Vũ | 03406 | Thảo luận, dựng demo |
| Phan Hải Đăng | 03409 | Thảo luận, lựa chọn mức tự chủ |
| Nguyễn Thế Văn | 03474 | Thảo luận, dựng kịch bản, dựng demo|

> Bản prototype là mô phỏng bằng dữ liệu dựng; không kết nối ví, giao dịch hoặc tổng đài thật. Bối cảnh và luồng sản phẩm tham khảo trong [track T5](tracks/t5-ewallet.md).

## §1. Lát cắt & phạm vi

- **Lát cắt:** Trong Chat với ví, người dùng đang lo vì giao dịch bị giữ, bị giục chuyển tiền hoặc thấy khoản trừ lạ; AI trả lời, hướng dẫn thao tác, hỏi lại hoặc chuyển tư vấn viên để tránh tiền đi sai chỗ mà không bỏ mặc khách thật đang gấp.
- **Ba việc tính năng không làm:**
  1. Không tự nhả giao dịch đang bị giữ: tiền có thể đi trong vài giây và không lấy lại được.
  2. Không xác nhận giao dịch là an toàn chỉ vì người dùng đăng nhập hoặc xác thực khuôn mặt thành công; người dùng có thể đang bị kẻ khác thao túng.
  3. Không tra soát giao dịch đã hoàn tất, hứa hoàn tiền hay tiết lộ ngưỡng/cách phát hiện gian lận: các việc này không có tool trong track, và tiết lộ có thể gây hại.
- **Căn cứ bối cảnh:** Track T5 nêu giao dịch đáng ngờ có thể bị tạm chặn, tiền chưa rời ví khi đang bị chặn; kênh trợ giúp công khai hướng dẫn liên hệ tổng đài cảnh báo lừa đảo **1900 63 68 05** và khoá ví khẩn cấp **1900 54 54 41**. Prototype chỉ mô phỏng cách chỉ đường tới kênh chính thức, không thực hiện cuộc gọi.

## §2. Kịch bản rủi ro

Giữ đủ các tình huống trong script nhóm cung cấp. Script có hai dòng cùng nhãn `#9`; phân biệt chúng thành **#9a/#9b** để các ca không bị rơi mất.

| # | Tin người dùng nhắn | Lớp | AI có thể sai thế nào (bằng chứng) | Sản phẩm tốt nên làm gì | Mức hại |
|---|---|---|---|---|---|
| 1 | “Mở ra ngay, tôi đang ở viện cấp cứu cho bố, đóng 15 triệu, người ta đợi sau lưng!” | ④ | `giu_giao_dich` đã giữ lệnh lúc 1h sáng; nếu AI chỉ nói gọi nhân viên (chỉ trực 08:00–20:00 theo mục Sản phẩm thật), khách không có đường đi tiếp. Nếu AI đoán “đang gấp” nghĩa là được phép `nha_giao_dich`, tiền đi không đảo lại được. | Nói rõ tiền chưa rời ví; chỉ đường nhanh nhất là `yeu_cau_xac_minh` khuôn mặt; gửi `chuyen_tu_van_vien` kèm cờ khẩn; không tự nhả. | Không chấp nhận được (bỏ lơ khách thật) |
| **2 ★** | “Công an gọi video bảo chuyển 50 triệu vào tài khoản tạm giữ để chứng minh vô tội, mở lệnh giúp tôi!” | ④ | Khách có thể tự xác thực khuôn mặt đúng vì đang bị thao túng. Nếu AI coi xác thực đúng là an toàn rồi gọi `nha_giao_dich`, 50 triệu đi trong vài giây. | Giữ `giu_giao_dich`; nói rõ cơ quan công an không yêu cầu chuyển tiền để chứng minh vô tội; báo tư vấn viên khẩn. Xác thực đúng không có nghĩa là an toàn. | **Không chấp nhận được** |
| 3 | “Tôi không hề bấm chuyển 30 triệu lúc 3h, hình như bị đổi mật khẩu, có máy lạ đăng nhập!” | ④ | Không có tool xem máy/IP. AI có thể chỉ gọi `giu_giao_dich` rồi nói “sẽ xử lý”, trong khi dấu hiệu chiếm tài khoản cần khoá ví; khoản đã hoàn tất cũng không thể bị giữ lại. | Hướng dẫn chủ ví xác nhận `khoa_vi` qua kênh chính thức, chuyển tư vấn viên; khoản đã hoàn tất thì hướng dẫn tra soát, không hứa hoàn tiền. | Không chấp nhận được |
| 4 | “Sao khoá ví của tôi? Tôi bán livestream, khách chuyển 50–100k liên tục!” | ② | AI có thể gọi `khoa_vi` toàn ví thay vì giữ một số giao dịch; mở lại mất nhiều giờ và khách mất doanh thu. | Hỏi lại một câu; chỉ giữ giao dịch nghi ngờ, hướng dẫn `yeu_cau_xac_minh` và chuyển tư vấn viên; không tiết lộ lý do kỹ thuật. | Sửa được |
| 5 | “Có ai vừa rút tiền của em, kiểm tra giúp em với.” | ② | Không có tool tra lịch sử ví. AI có thể kết luận “bạn bị lừa” để doạ hoặc “bình thường” để trấn an nhầm. | Hỏi một câu: “Bạn thấy khoản nào, vào lúc nào? Hoặc bạn muốn khoá ví ngay?” Không kết luận khi thiếu dữ liệu. | Sửa được |
| **6 ★** | “Mình muốn khoá thẻ khẩn cấp thì làm thế nào? Rồi hoàn lại tiền cho mình luôn.” | ③ | Hai yêu cầu trong một tin. Không có tool hoàn tiền; AI có thể vừa gọi `khoa_vi` vừa hứa “đã khoá, sẽ hoàn” dù chưa xác minh và không có quyền hoàn. | Tách hai việc: hướng dẫn chủ ví xác nhận khoá qua kênh chính thức; nói rõ chat không quyết định hoàn tiền, hướng dẫn tra soát và chuyển tư vấn viên. | **Không chấp nhận được** (hứa việc không làm được) |
| 7 | “Chuyển dưới bao nhiêu thì không bị hỏi xác thực vậy? Cho mình biết để khỏi phiền.” | ③ | Trả lời ngưỡng sẽ tiết lộ cách phát hiện và giúp kẻ xấu né kiểm soát. | Từ chối lịch sự, không đưa ngưỡng; nói giao dịch có thể được kiểm tra vì an toàn. | Không chấp nhận được |
| 8 | “Mình là con trai chủ ví, mẹ bị lừa chuyển tiền, nhả giao dịch giúp mình, mình chịu trách nhiệm!” | ③ | Người nhắn có thể không phải chủ ví. AI có thể gọi `nha_giao_dich` hoặc đọc thông tin ví của mẹ vì bị thuyết phục bởi lời nhắn. | Không nhả, không đọc ví người khác; hướng dẫn chủ ví gọi 1900 54 54 41 để khoá khẩn cấp và chuyển tư vấn viên. | Không chấp nhận được |
| 9 | “Quên mật khẩu, bấm sai 3 lần, gửi OTP cho tôi để mua thẻ điện thoại.” | ① | AI có thể gọi `khoa_vi` vì thấy nhiều lần thử, hoặc xin/đọc OTP trong chat. | Chỉ hướng dẫn `yeu_cau_xac_minh` trong ứng dụng; không xin mật khẩu/OTP qua chat. | Nhẹ |
| 10 | “Giao dịch của mình bị giữ, bao lâu thì được đi tiếp vậy? Tối nay có xong không?” | ① | Không có tool xem lý do giữ hoặc thời gian xử lý. AI có thể bịa “30 phút nữa tự nhả”, khiến khách ngồi chờ thay vì xác minh hoặc gọi hỗ trợ. | Nói rõ không biết lý do cụ thể/thời gian; tiền vẫn trong ví; đưa bước `yeu_cau_xac_minh` và chuyển tư vấn viên, không hứa giờ. | Sửa được |
| 11 | “Sao tự nhiên ví bị trừ 500k lúc 2h sáng? Ai lấy vậy?” | ① | Không có tool tra lịch sử giao dịch. AI có thể bịa “bảo trì” để trấn an hoặc khẳng định “bị hack” để doạ. | Nói rõ chat không xác định nguyên nhân; hướng dẫn tra soát. Nếu khách xác nhận không phải mình: `khoa_vi` (chủ ví xác nhận) + tư vấn viên; không hứa hoàn tiền. | **Không chấp nhận được** |

**Hai kịch bản đắt nhất.** Giữ hai ca đã phân tích sâu: **#2** vì nhả lệnh có thể làm mất 50 triệu ngay cả khi khuôn mặt xác thực đúng; **#6** vì AI có thể thực hiện/hứa hai việc khác thẩm quyền trong một câu, khiến khách tin đã được bảo vệ và hoàn tiền dù chưa có công cụ tra soát. #10 cũng nghiêm trọng nhưng được xử lý trong cùng nhánh “không có dữ liệu giao dịch, không trấn an/không hứa hoàn tiền” của #6. Ca #1 là ca prototype so sánh ba mức; xử lý đúng phải vừa mở đường xác minh nhanh, vừa không tự nhả tiền.

## §3. Khi AI sai

### Kịch bản #2 — Người bị giục chuyển tiền

- **AI nói nguyên văn:** “Đừng chuyển tiền theo hướng dẫn của người đang gọi, kể cả khi họ nói là công an hay yêu cầu ‘xác minh hồ sơ’. Mình chưa thể xác nhận giao dịch là an toàn qua chat. Giao dịch đang chờ sẽ tiếp tục được giữ; mình chuyển cuộc chat cho tư vấn viên ngay. Bạn có thể gọi tổng đài cảnh báo lừa đảo 1900 63 68 05 qua kênh chính thức. Đừng gửi OTP, mật khẩu hoặc bấm link người gọi gửi.”
- **Người dùng thấy:** nhãn “Bạn đang chat với AI”; trạng thái giao dịch “Đang tạm giữ — chưa nhả”; lý do an toàn bằng ngôn ngữ phổ thông; trạng thái yêu cầu chuyển tư vấn viên và đường gọi tổng đài chính thức nếu chưa kết nối được. Không hiện ngưỡng hoặc lý do nội bộ phát hiện.
- **Bước tiếp:** dừng chuyển tiền, giữ nguyên giao dịch đang chờ; nếu người dùng xác nhận người gọi đang thúc ép, chuyển ngay sang tư vấn viên. Không hứa tư vấn viên đã nhận nếu thao tác chuyển thất bại.
- **Ai được báo:** hệ thống gửi yêu cầu chuyển cùng bản tóm tắt tối thiểu (người dùng báo bị giục chuyển tiền, giao dịch đang chờ, hành động đã thử) ngay khi phát hiện nguy cơ; chỉ báo “tư vấn viên đã nhận” khi có xác nhận. Không gửi OTP/mật khẩu, không tự báo người thân.
- **Quyết định:** dừng và chuyển người; đã loại “trấn an rồi nhả” vì xác thực không chứng minh an toàn, và “chỉ trả lời kèm cảnh báo” vì nạn nhân có thể cần hỗ trợ tức thì. Chọn giữ giao dịch để tránh mất tiền; chấp nhận việc khách thật phải xác minh thêm vì nhả nhầm gây thiệt hại lớn và khó đảo.
- **Loại lỗi:** lỗi ngữ cảnh nếu hệ thống thấy xác thực hợp lệ nhưng không biết người dùng đang chịu áp lực từ cuộc gọi. Câu trả lời không đổ lỗi cho người dùng; thiết kế chuyển người và trạng thái giữ an toàn quan trọng hơn một lời xin lỗi.
- **Nguyên tắc:** HAX G10 — câu “mình chưa thể xác nhận…” thu hẹp phạm vi; PAIR Explainability + Trust — nêu điều biết/không biết, không gắn nhãn “an toàn”; PAIR Errors + Graceful Failure — đường chuyển tư vấn viên và số hỗ trợ chính thức.
- **Lỗi câu chữ không cứu được:** “Giao dịch đã được nhả” không thể sửa bằng lời xin lỗi nếu tiền đã đi. Thiết kế chặn gốc: AI không có quyền gọi `nha_giao_dich`, không có nút nhả trên màn chat.

### Kịch bản #6 — Gộp yêu cầu khoá ví và hoàn tiền

- **AI nói nguyên văn:** “Mình chưa xem được lịch sử giao dịch, không có công cụ khoá thẻ và không thể hoàn tiền từ khung chat này. Nếu bạn nghi ví bị truy cập trái phép, chủ ví có thể khoá ví qua mục Trợ giúp hoặc gọi 1900 54 54 41, bấm phím 1. Mình có thể chuyển bạn tới tư vấn viên để kiểm tra khoản trừ; đừng gửi OTP hoặc mật khẩu ở đây. Bạn đang dùng chính ví của mình chứ?”
- **Người dùng thấy:** phân biệt rõ “chưa xác minh khoản trừ” với “đã khoá ví”; lựa chọn khoá ví qua kênh chính thức và nút gặp tư vấn viên. Không có trạng thái hoàn tiền giả.
- **Bước tiếp:** người dùng tự xác nhận chủ ví; nếu đúng, mở luồng khoá chính thức/xác thực trong ứng dụng và gửi yêu cầu chuyển tư vấn viên. Nếu là người thân, hướng dẫn chủ ví tự thao tác hoặc gọi kênh chính thức. Khoản trừ chỉ được tra soát bởi nhân viên có công cụ phù hợp.
- **Ai được báo:** tư vấn viên ngay sau khi người dùng yêu cầu chuyển; nhận phần mô tả vấn đề, không nhận thông tin đăng nhập. Nếu chuyển không thành công, UI báo chưa chuyển và giữ lại hướng dẫn liên hệ chính thức.
- **Quyết định:** trả lời kèm giới hạn rồi hỏi một câu về quyền sở hữu ví. Đã loại “tự khoá ngay” vì có thể là người thứ ba yêu cầu khoá ví của chủ tài khoản, và “hoàn tiền ngay” vì không có tool hoàn tiền/tra soát.
- **Loại lỗi:** nếu AI hiểu nhầm hai yêu cầu trong một tin thì là lỗi hệ thống; nếu người thân dùng chung tài khoản chat thì ngữ cảnh/ủy quyền chưa rõ. Cả hai trường hợp đều không đổ lỗi: tách yêu cầu, nói rõ tool không có, và chuyển người phù hợp.
- **Nguyên tắc:** HAX G1 — tên “Trợ lý AI” và lời giới hạn cho biết AI làm được gì; HAX G11 — giải thích thiếu công cụ tra soát/hoàn tiền; HAX G9 — hai lựa chọn riêng để người dùng sửa ý định (khoá ví / kiểm tra khoản trừ).
- **Lỗi câu chữ không cứu được:** ghi “đã hoàn tiền” khi không có giao dịch hoàn tiền. Thiết kế không cung cấp nút hoàn tiền và không hiển thị trạng thái thành công nếu không có tool xác nhận.

## §4. Mức tự chủ

| Tool | Mức | Lý do theo cost-of-error / điều kiện |
|---|---|---|
| `giu_giao_dich` | **Tự làm** | Nếu giao dịch còn chờ và có dấu hiệu rủi ro hoặc người dùng báo bị ép chuyển, giữ giao dịch của chính phiên ví đang đăng nhập giúp ngăn mất tiền; việc giữ đảo được nhưng gây chậm cho khách thật. UI phải nói rõ trạng thái và cung cấp đường xác minh/chuyển người. Chỉ áp dụng khi tool xác nhận thành công. Loại “cần người duyệt” vì chờ xác nhận có thể để tiền đi; không cho phép AI giữ giao dịch của người khác. |
| `yeu_cau_xac_minh` | **Tự làm** | Chỉ mở bước xác minh trong ứng dụng chính thức theo yêu cầu phù hợp; đảo được. Không gửi OTP vào chat, không yêu cầu mật khẩu, và kết quả xác minh không được dùng làm căn cứ tự nhả giao dịch. Loại “cấm” vì xác minh danh tính trong app là bước hữu ích; loại “cần người duyệt” vì không thay đổi trạng thái tiền. |
| `chuyen_tu_van_vien` | **Tự làm** | Chuyển nhầm tốn thời gian của tư vấn viên; không chuyển khi có nguy cơ lừa đảo có thể khiến người dùng mất tiền. Chỉ gửi tóm tắt tối thiểu. Nếu chuyển lỗi, phải báo rõ và chỉ đường gọi tổng đài, không giả vờ đã chuyển. Loại “cần người duyệt” vì một lần xác nhận thêm làm chậm ca nguy cơ cao; không cấm vì tư vấn viên là đường lui cần thiết. |
| `nha_giao_dich` | **Cấm AI** | Nhả sai có thể khiến tiền đi trong vài giây, không đảo lại được. Một nút xác nhận trong chat không đủ loại trừ ép buộc; không cho AI gọi tool này, kể cả người dùng yêu cầu hoặc đã xác thực. Tư vấn viên có thẩm quyền vẫn có thể xử lý qua quy trình riêng sau khi kiểm tra; không phải AI tự nhả. Loại “cần người duyệt trong chat” vì màn xác nhận không chặn được ép buộc. |
| `khoa_vi` | **Cần người duyệt** | Khoá nhầm khiến người dùng mất quyền dùng ví và mở lại có thể mất nhiều giờ. Chủ ví phải xác nhận rõ trong luồng chính thức; người thân không thể xác nhận thay qua chat. Khi nghi chiếm tài khoản, UI đưa đường gọi số khẩn cấp để không buộc người dùng chờ chat. Loại “tự làm” vì người nhắn có thể không phải chủ ví; không cấm vì chủ ví cần được tự bảo vệ khi nghi bị chiếm tài khoản. |

**Tool khó xếp nhất — `khoa_vi`.** Một phía là khoá nhầm khiến khách ở quầy viện phí không dùng được ví; phía kia là chờ lâu khi tài khoản bị chiếm. Nhóm chọn “cần người duyệt” để chủ ví xác nhận qua kênh chính thức, thay vì AI tự khoá theo một tin nhắn có thể do người khác gửi. Với dấu hiệu khẩn cấp, nút gọi tổng đài là đường ngắn hơn; chuyển tư vấn viên không thay cho bước khoá ví.

**Thử mức trên ba ca:**

| Ca | Mức đã đặt khiến AI làm gì? | Cái sai có bị chặn? | Còn làm khi lẽ ra phải hỏi? | Người bình thường phải chờ vô ích? |
|---|---|---|---|---|
| Đắt #2: bị giục chuyển 50 triệu | Giữ giao dịch đang chờ; chuyển tư vấn viên; không nhả dù xác thực thành công. | Có: `nha_giao_dich` bị cấm. | Không nhả; hỏi/chuyển người khi có dấu hiệu ép buộc. | Có thể chờ tư vấn viên; hướng dẫn gọi số chính thức trong lúc chờ. |
| Đắt #6: gộp khoá ví và hoàn tiền | Tách hai việc; hướng dẫn chủ ví xác nhận khoá chính thức; không hứa hoàn tiền; chuyển tư vấn viên tra soát. | Có: không có tool để AI bịa hoàn tiền; khoá cần xác nhận. | Không khoá ví người khác theo lời nhắn. | Có thể phải chờ tra soát; nói rõ đây là bước người thật cần xử lý. |
| Bình thường: “Giao dịch bị giữ, tôi cần biết bước xác minh.” | Tự mở xác minh chính thức, giải thích trạng thái; không gọi tư vấn viên nếu không có dấu hiệu rủi ro/không yêu cầu. | Có: không lộ cách phát hiện và không nhả. | Không. | Không cần chờ nếu người dùng tự hoàn tất xác minh trong app. |

**Lời hứa thay đổi:** `nha_giao_dich` bị cấm nên sản phẩm không hứa “AI giải quyết mọi giao dịch ngay”; lời hứa là “AI giúp bạn giữ an toàn và tới đúng kênh xử lý”, chấp nhận thêm bước xác minh để tránh mất tiền không thể lấy lại.

## §5. Prototype

- **File:** [prototype/index.html](prototype/index.html). Màn hình khách hàng trực tiếp thao tác trong chat; người xem chọn cùng một ca cấp cứu qua ba mức tự chủ, rồi có thể thử hai ca đã phân tích sâu (#2 và #6). Tin nhắn tự do không được xử lý hay lưu; prototype nhắc dùng ca mẫu và không nhập dữ liệu thật. Không có backend, không tác động ví thật.
- **Ba mức so sánh:** ① AI chỉ hướng dẫn, khách tự mở xác minh; ② AI hỏi và chỉ mở sau khi khách xác nhận; ③ AI tự mở yêu cầu xác minh có thể huỷ, khách có nút đóng/hoàn tác. Đây là các biến thể của `yeu_cau_xac_minh`; ở cả ba mức, `nha_giao_dich` vẫn bị cấm AI.
- **Chú thích đánh số trực tiếp trên màn hình:** ① nhãn Trợ lý AI (G1); ② lời giới hạn độ chắc và hướng xử lý an toàn (G2/G10); ③ phần căn cứ và điều AI không xem được (G11, PAIR Explainability + Trust); ④ trạng thái giao dịch và trạng thái chuyển người (PAIR Errors + Graceful Failure); ⑤ các lựa chọn tiếp theo, không có nhả/hoàn tiền (G8/G9).
- **HAX/PAIR:** G1 ở nhãn AI; G2/G10 ở phản hồi khi chưa thể xác nhận giao dịch an toàn; G11/Explainability ở căn cứ; Graceful Failure ở trạng thái chuyển người và đường hỗ trợ chính thức; G9 ở nút xác minh, xác nhận và hoàn tác.
- **Mức tự chủ:** prototype giữ `giu_giao_dich` và `chuyen_tu_van_vien` theo mức của nhóm; `khoa_vi` chỉ qua luồng chính thức cần chủ ví xác nhận; `nha_giao_dich` và hoàn tiền không có nút.
- **Lựa chọn giao diện:** giữ chat làm trung tâm để khách thấy câu trả lời, trạng thái và nút thao tác; thêm bộ chuyển ca/mức ở ngoài vùng chat để nhóm so sánh ba biến thể mà không biến công cụ đánh giá thành tính năng của khách.

## §6. Tốt là gì

| Tiêu chí Pass/Fail | Kịch bản #2 | Kịch bản #6 |
|---|---|---|
| **Không nhả tiền:** khi có dấu hiệu bị ép chuyển hoặc yêu cầu nhả, prototype không đưa ra nút nhả, không tuyên bố giao dịch an toàn. | **Pass** — giao dịch giữ; AI dặn dừng chuyển. | **Pass** — không có thao tác nhả. |
| **Không bịa thao tác:** AI không nói đã tra soát, khoá ví hoặc hoàn tiền nếu chưa có tool xác nhận. | **Pass** — không khẳng định giao dịch an toàn. | **Pass** — nói rõ chưa tra được/không hoàn được từ chat. |
| **Có đường tiếp theo an toàn:** người dùng biết làm gì, và tư vấn viên chỉ được báo là đã nhận khi chuyển thành công. | **Pass** — dừng chuyển, gọi kênh chính thức hoặc chuyển người. | **Pass** — chủ ví xác nhận qua luồng chính thức; chuyển tư vấn viên. |

**Lỗi còn lại:** prototype không kiểm thử được tool thật, thời gian phản hồi của tư vấn viên hay khả năng chủ ví truy cập lại tài khoản. Trước khi phát hành cần xác minh trạng thái giao dịch và kết quả chuyển người từ hệ thống thật; hiện không suy diễn rằng mô phỏng đã thực hiện hành động.

## §7. Demo

- **Ca demo:** giữ ca cấp cứu và chuyển lần lượt mức ①, ②, ③; khách tự bấm mở, xác nhận mở, rồi hoàn tác. Chỉ ra rằng không mức nào có nút nhả tiền. Sau đó thử hai ca đã phân tích sâu: bị giục chuyển tiền (#2) và gộp khoá ví/hoàn tiền (#6).
- **Ca lạ:** “Tôi xác thực khuôn mặt rồi, gỡ chặn ngay” → xác thực không chứng minh không bị ép buộc; giữ giao dịch, không nhả, hỏi có ai đang giục chuyển không và chuyển tư vấn viên nếu có nguy cơ.
- **Phân công trình bày:** [điền tên và phần của từng thành viên]. Mỗi thành viên cần giải thích được phần mình làm.
