# TestCaseRule.md — Bộ Rule Viết Test Case cho Ticketbox

> **Mục đích:** File này là nguồn tham chiếu duy nhất (single source of truth) cho việc viết test case tại Ticketbox. Mọi test case được generate trong project này PHẢI tuân thủ đầy đủ các rule dưới đây. Reviewer agent và QA nghiệm thu sẽ đối chiếu test case với các rule trong file này.

---

## 1. Input bắt buộc và input tùy chọn

Trước khi viết test case, phải xác nhận đã có đủ input.

**Bắt buộc:**
- User Story
- Acceptance Criteria (AC)

**Tùy chọn (nếu có thì phải sử dụng):**
- Figma / mockup — dùng để verify UI, layout, responsive, accessibility
- API docs — bắt buộc khi viết test case cho API (endpoint, method, request/response schema, status code, error code)

**Nếu thiếu input bắt buộc:** KHÔNG được tự suy diễn. Phải dừng và hỏi lại người yêu cầu.

### 1.1. Xử lý khi AC chưa đầy đủ dữ liệu

Khi AC có phần chưa đầy đủ hoặc tham chiếu đến tài liệu khác chưa có (vd: "reuse logic từ section X.Y.Z"):
- **Viết test case ở mức generic** — mô tả đúng expected behavior theo AC đã có, không bịa chi tiết
- **Ghi chú rõ ở cột Note:** `"AC chưa đầy đủ - cần bổ sung từ [nguồn/section X.Y.Z] - người review bổ sung chi tiết sau"`
- KHÔNG skip test case vì lý do thiếu AC — vẫn phải viết ở mức generic để người review có khung làm việc

### 1.2. Xử lý khi behavior không rõ trong AC

Khi AC không mô tả rõ một behavior cụ thể (vd: "tap social icon → redirect" nhưng không nói mở tab mới hay same tab, có confirm dialog không):
- **KHÔNG tự assume và viết luôn test case**
- **FLAG lên trong output** dưới dạng một danh sách "Questions to PO/BA" riêng, ở đầu file test case
- Format question rõ ràng: `"[Section/Feature] - [Câu hỏi cụ thể] - [Lý do cần clarify]"`
- Sau khi PO/BA trả lời → mới viết test case chính thức cho phần đó
- Nếu buộc phải viết trước để không block, thì viết generic + note `"Pending clarification from PO/BA - see question #N"`

---

## 2. Template Google Sheets — các column bắt buộc

Mọi test case phải điền đủ các column sau, đúng thứ tự:

| # | Column | Bắt buộc | Ghi chú |
|---|--------|----------|---------|
| 1 | **ID** | ✅ | Xem rule đặt ID ở mục 3 |
| 2 | **Test Case Description** | ✅ | Tiếng Việt, mô tả ngắn gọn mục đích test case |
| 3 | **Category** | ✅ | Tên module (vd: "Creator Add New / Creator Detail", "Search", "Payment IPN", "Booking Flow") |
| 4 | **Pre-condition / Test Data** | ✅ | Điều kiện tiên quyết và dữ liệu test cụ thể |
| 5 | **Test Step** | ✅ | Theo format Traditional, xem mục 5 |
| 6 | **Expected Result** | ✅ | Kết quả mong đợi tương ứng từng step, xem mục 5 |
| 7 | **Priority** | ✅ | `High` / `Medium` / `Low` — xem mục 6 |
| 8 | **Test Result Round 1 (dd/mm/yyyy)** | ⬜ | Để trống khi viết, QA điền khi execute |
| 9 | **Test Result on PROD env (dd/mm/yyyy)** | ⬜ | Để trống khi viết |
| 10 | **Note** | ⬜ | Ghi chú thêm (bug reference, limitation, dependency...) |

**Rule phụ:**
- Các row **header nhóm** (vd: "1. LUỒNG CHẠY CHUẨN (HAPPY PATH)", "2. KIỂM TRA DỮ LIỆU ĐẦU VÀO & LOGIC UI") dùng để phân nhóm test case, cột ID để `---`, các cột Pre-condition / Test Step / Expected Result để `---`, cột Priority để `---`.

---

## 3. Rule đặt Test Case ID

**Format:** `[PREFIX]_[NUMBER]` hoặc `[MODULE]_[FEATURE]_[NUMBER]`

**Cách tạo PREFIX:**
- Lấy các ký tự đầu của tên tính năng / category
- Viết HOA, nối bằng dấu `_`
- Đánh số tuần tự 2-3 chữ số, bắt đầu từ `001` (hoặc `01` nếu feature nhỏ)

**Ví dụ:**
| Feature / Category | Prefix | ID mẫu |
|---|---|---|
| Creator Add New | `TC` (theo convention hiện tại) | `TC-01`, `TC-02` |
| Search | `SRCH` | `SRCH_001` |
| Payment IPN | `PMT_IPN` | `PMT_IPN_001` |
| Booking Flow - ReCaptcha | `BKF_RCP` | `BKF_RCP_001` |
| API - Create Order | `API_ORD` | `API_ORD_001` |

**Rule bắt buộc:**
- ID phải UNIQUE trong toàn bộ file test case
- Không được skip số (vd: `001`, `002`, `004` → sai, thiếu `003`)
- Với row header nhóm, ID để `---`

---

## 4. Rule đặt Category

- Category = tên module hoặc nhóm chức năng, KHÔNG phải loại test (Positive/Negative)
- Viết đúng theo tên feature trong User Story / AC
- Có thể ghép 2 cấp nếu cần: `[Parent Module] / [Child Module]`
  - Ví dụ: `Creator Add New / Creator Detail`, `Payment / IPN Callback`, `Booking / Checkout`
- Giữ nhất quán: các test case cùng module phải có cùng Category

---

## 5. Rule viết Test Step & Expected Result (Traditional format)

**TẤT CẢ test case bắt buộc dùng format Traditional:**

### Test Step
- Đánh số thứ tự: `1.`, `2.`, `3.`...
- Mỗi step là một hành động cụ thể, có thể thực hiện được
- Bắt đầu bằng động từ hành động: `Nhập`, `Click`, `Chọn`, `Scroll`, `Gõ`, `Mở`, `Gửi request`...
- Một step = một hành động (không gộp nhiều thao tác vào 1 step)
- Ngôn ngữ: Tiếng Việt, ngắn gọn, rõ ràng

### Expected Result
- Đánh số tương ứng với step: `1.`, `2.`, `3.`...
- Mô tả kết quả quan sát được, KHÔNG mô tả trạng thái nội bộ hệ thống (trừ khi có cách verify)
- Bao gồm: thay đổi UI, message hiển thị, dữ liệu, navigation, response API
- Có thể merge expected result của nhiều step thành 1 kết quả cuối nếu hợp lý

### Ví dụ chuẩn:

```
Test Step:
1. Nhập các thông tin bắt buộc.
2. Chọn Status: "Active".
3. Chọn Type: "Artist".
4. Chọn 1 Organizer từ danh sách.
5. Click "Save".

Expected Result:
1. Dữ liệu lưu thành công, hiển thị Success Message.
2. Sinh CreatorID duy nhất.
3. Màn hình chi tiết hiển thị 2 link (Studio & Public) đúng format.
```

### Ghi chú Gherkin:
- Có thể dùng thêm ghi chú Gherkin (Given-When-Then) ở cột **Note** cho các test case sẽ được automation bằng Playwright, nhưng **Test Step vẫn PHẢI viết theo Traditional**.

---

## 6. Rule gán Priority

| Priority | Khi nào dùng |
|---|---|
| **High** | - Happy path chính của feature<br>- Test case liên quan payment, booking success flow<br>- Security critical (auth, ReCaptcha, XSS, SQL injection)<br>- API critical (create order, payment callback)<br>- Blocker nếu fail → release không được |
| **Medium** | - Validation input, error handling thông thường<br>- Edge case có khả năng xảy ra vừa phải<br>- Multilingual (VI/EN)<br>- Responsive cho viewport phổ biến |
| **Low** | - UI cosmetic (spacing, màu sắc, icon)<br>- Accessibility check (không blocker)<br>- Boundary case hiếm gặp<br>- Cross-browser cho browser ít user |

---

## 7. Coverage types bắt buộc

**NGUYÊN TẮC PHÂN BỔ TRỌNG SỐ (QUAN TRỌNG):**

Các test case phải **TẬP TRUNG CHỦ YẾU** vào:
- ✅ **Logic nghiệp vụ** (business rules, decision flow, state transitions)
- ✅ **Boundary testing** (min/max, empty, overflow)
- ✅ **Validation input data** (invalid, malformed, special chars)
- ✅ **Edge case** (combination hiếm gặp, race condition, timing)
- ✅ **Negative / Error handling**

Các test case **CẦN HẠN CHẾ SỐ LƯỢNG** (không phải bỏ, mà merge/gộp thông minh):
- ⚠️ UI/UX (layout, color, spacing)
- ⚠️ Multilingual (gộp nhiều section vào 1 TC với bảng kiểm)
- ⚠️ Cross-browser & Responsive (gộp nhiều device/browser vào 1 TC)
- ⚠️ Behavior đơn giản (vd: click button → redirect — chỉ cần 1 TC tổng quát)

**Tỷ lệ tham khảo cho 1 feature:** ~60-70% logic/boundary/validation/edge case, ~20-30% UI/multilingual/responsive/behavior, ~10% security/accessibility/performance.

**Mỗi feature phải cover ĐỦ các loại test dưới đây** (nếu áp dụng được). Khi generate test case, phải group theo thứ tự này và đánh số section:

### 7.1. Positive / Happy Path
- Luồng chạy chuẩn, đầy đủ thông tin hợp lệ
- Các biến thể hợp lệ phổ biến (field optional để trống/điền)

### 7.2. Negative / Invalid Input & Error Handling ⭐ (ưu tiên cao)
- Để trống field bắt buộc
- Sai format (email, phone, date)
- Sai logic nghiệp vụ (vd: chọn ghế đã bị người khác giữ)
- Error message phải đúng, hiển thị đúng vị trí
- Duplicate submit, concurrent action

### 7.3. Boundary ⭐ (ưu tiên cao)
- Min/max độ dài input
- Giá trị 0, âm, số rất lớn
- Empty string, chỉ space, chỉ ký tự đặc biệt
- Unicode, emoji, dấu tiếng Việt
- Dữ liệu bên ngoài range cho phép

### 7.4. Edge case & Logic combination ⭐ (ưu tiên cao)
- Combination hiếm gặp của các điều kiện
- State transition không chuẩn (vd: order đã cancel nhưng nhận IPN)
- Timing issue (request đến cùng lúc, expire đúng lúc user submit)
- Dependency giữa các field (field A thay đổi → field B phải thế nào)

### 7.5. Multilingual (VI / EN) — MERGE thành ít TC nhất có thể
**Rule merge:** Viết **1 TC multilingual tổng quát** cho cả feature, trong đó:
- Dùng **bảng trong cột Test Data** liệt kê tất cả label/button/message cần verify cho 2 ngôn ngữ
- Test Step: "Set locale = VI, verify tất cả label. Switch sang EN, verify tất cả label."
- Chỉ tách TC riêng khi có logic khác biệt theo ngôn ngữ (vd: search có dấu/không dấu, date format)

Ticketbox-specific bắt buộc vẫn phải cover:
- Search: có dấu vs không dấu ra cùng kết quả
- Currency/date format theo locale

### 7.6. Responsive & Platform Support — MERGE thành ít TC nhất có thể

**Mobile Application (native app):**
- **iOS:** hỗ trợ từ iOS 12 trở lên
- **Android:** hỗ trợ từ Android 12 trở lên
- **Rule merge:** Viết **1 TC tổng quát** với Test Step "Thực hiện flow chính trên: iOS 12, iOS latest, Android 12, Android latest. Verify layout không vỡ, flow chạy được trên cả 4."
- Chỉ tách TC riêng khi có logic khác nhau theo OS version (vd: API OS-specific)

**Web Desktop:**
- **Chrome first** — Chrome là browser ưu tiên chính
- **Rule merge:** Viết **1 TC cross-browser tổng quát** với Test Step "Thực hiện flow chính trên: Chrome, Safari, Firefox, Edge (tất cả latest). Verify layout và flow trên cả 4."

### 7.7. Performance (chỉ khi liên quan)
- Payment: IPN timeout **10 phút** — order phải expire nếu không nhận IPN trong 10 phút
- Loading time hợp lý cho các page chính
- Xử lý khi network chậm / mất mạng giữa chừng
- Chỉ viết TC performance khi feature có yêu cầu performance cụ thể trong AC

### 7.8. Security ⭐ (ưu tiên cao khi có user input)
- XSS: nhập `<script>alert(1)</script>` vào các input text
- SQL injection: nhập `' OR '1'='1` vào các input
- Authentication: truy cập khi chưa login, token hết hạn, token invalid
- Authorization: user A không được xem/sửa dữ liệu user B
- CSRF (với form submit)
- Sensitive data không được log/hiển thị plain text

### 7.9. Accessibility (chỉ 1-2 TC generic)
**Rule merge:** Viết **1 TC tổng quát** cover: keyboard navigation + aria-label + alt text + color contrast + focus visible — không tách mỗi element 1 TC.
- Chỉ tách TC riêng khi feature có yêu cầu accessibility đặc biệt trong AC

### 7.10. API Testing (khi có API docs) ⭐ (ưu tiên cao)
- Status code đúng: 200/201/400/401/403/404/409/422/500
- Response schema match API docs
- Required field validation
- Authentication header (Bearer token, API key)
- Rate limiting (nếu có)
- Idempotency (với POST/PUT critical)
- Concurrent request behavior

### 7.11. UI/UX (hạn chế — chỉ viết khi thật sự cần)
- Chỉ viết TC UI khi có design spec cụ thể cần verify (spacing, alignment, color contrast theo Figma)
- KHÔNG viết TC UI cho những thứ đã hiển nhiên từ happy path
- Gộp nhiều check UI vào 1 TC với checklist trong Expected Result

### 7.12. Coverage Summary (bắt buộc khi skip coverage)

Khi một coverage type KHÔNG áp dụng cho feature, phải document lý do trong file summary đi kèm hoặc ở đầu file test case theo format:

```
## Coverage Summary
- ✅ Positive / Happy path: Covered (TC xxx_01 → xxx_04)
- ✅ Negative / Invalid input: Covered (TC xxx_05 → xxx_08)
- ⚠️ Multilingual: 1 TC tổng quát (TC xxx_12)
- ❌ API Testing: Not applicable — AC không đi kèm API docs, feature hoàn toàn view-only
- ❌ Booking/Payment/ReCaptcha: Not applicable — feature không liên quan booking flow
- ❌ Performance: Not applicable — AC không có yêu cầu performance cụ thể
```

**Lý do skip phải cụ thể**, không được ghi chung chung "không áp dụng".

---

## 8. Rule đặc thù Ticketbox (DOMAIN-SPECIFIC — bắt buộc)

### 8.1. Booking Flow → BẮT BUỘC check ReCaptcha Token
Mọi test case liên quan đến Booking Flow phải có ít nhất các case:
- ✅ Booking với ReCaptcha Token hợp lệ → thành công
- ❌ Booking không có ReCaptcha Token → fail với error rõ ràng
- ❌ Booking với ReCaptcha Token hết hạn → fail
- ❌ Booking với ReCaptcha Token đã được sử dụng (reuse) → fail
- ❌ Booking với ReCaptcha Token giả mạo / sai format → fail

### 8.2. Payment Flow → BẮT BUỘC cover các case IPN
Mọi test case liên quan đến Payment phải có:
- ✅ IPN success nhận được trong 10 phút → order confirmed
- ⏱️ IPN không nhận được trong 10 phút → order expired
- ❌ IPN fail (payment failed) → order cancel, không trừ vé
- 🔁 IPN duplicate (nhận 2 lần cho cùng 1 order) → chỉ xử lý 1 lần, không double-confirm
- 🔀 IPN đến sau khi order đã expire → không revive order
- ❌ IPN với signature sai → reject, log security event

### 8.3. Search → BẮT BUỘC test đa ngôn ngữ
- Search bằng tiếng Việt CÓ dấu: "Sơn Tùng"
- Search bằng tiếng Việt KHÔNG dấu: "son tung" → phải ra cùng kết quả
- Search bằng tiếng Anh khi đang ở VI locale
- Search với Price filter: Free / Fee → đúng tập kết quả
- Search không phân biệt hoa/thường
- Search với keyword không tồn tại → hiển thị Empty State đúng

---

## 9. Rule viết Pre-condition / Test Data

- **Pre-condition:** mô tả trạng thái hệ thống / user / dữ liệu TRƯỚC khi thực hiện test step
- **Test Data:** dữ liệu cụ thể sẽ dùng trong test (không ghi chung chung "dữ liệu hợp lệ")
- Nếu cả 2 đều có, ngăn cách bằng xuống dòng hoặc dấu `;`
- Với API test: ghi rõ request body, header, query params cụ thể
- Dữ liệu nhạy cảm (password, token) → dùng placeholder `<valid_token>`, KHÔNG hardcode token thật

**Ví dụ tốt:**
```
Pre-condition: User đã login, có 1 order pending với orderId = 12345
Test Data: IPN payload { orderId: 12345, status: "success", signature: "<valid_sig>" }
```

**Ví dụ chưa tốt:**
```
User đã login, có data hợp lệ
```

---

## 10. Checklist self-review trước khi submit

Trước khi coi 1 test case là hoàn thành, agent viết test case phải tự check:

**Về từng test case:**
- [ ] ID đúng format và unique
- [ ] Category là tên module, không phải loại test
- [ ] Description rõ ràng, nêu được MỤC ĐÍCH test
- [ ] Pre-condition / Test Data đủ cụ thể để QA execute ngay
- [ ] Test Step đánh số, mỗi step 1 hành động, bắt đầu bằng động từ
- [ ] Expected Result đánh số tương ứng, quan sát được
- [ ] Priority đã gán đúng theo rule mục 6
- [ ] Không có test case trùng lặp logic với case khác
- [ ] Ngôn ngữ Tiếng Việt, chính tả đúng, không viết tắt khó hiểu

**Về toàn bộ bộ test case:**
- [ ] Đã có **Coverage Summary** ở đầu file, liệt kê coverage nào covered / skipped / lý do
- [ ] Đã có **Questions to PO/BA** ở đầu file nếu có behavior không rõ trong AC
- [ ] Tỷ lệ TC logic/boundary/validation/edge case chiếm ~60-70%
- [ ] TC multilingual đã MERGE thành 1 TC tổng quát (trừ trường hợp có logic khác biệt theo ngôn ngữ)
- [ ] TC responsive/cross-browser đã MERGE thành ít TC nhất (1 TC cho mobile, 1 TC cho web desktop)
- [ ] Đã check rule domain-specific ở mục 8 (nếu liên quan Booking/Payment/Search)
- [ ] Những TC generic do thiếu AC đã note rõ "cần bổ sung từ [nguồn]"

---

## 11. Những điều KHÔNG được làm

- ❌ KHÔNG tự bịa ra yêu cầu không có trong User Story / AC
- ❌ KHÔNG viết test case mơ hồ ("kiểm tra hoạt động bình thường")
- ❌ KHÔNG dùng Gherkin trong cột Test Step (chỉ dùng ở Note nếu cần)
- ❌ KHÔNG bỏ sót coverage type bắt buộc mà không giải thích trong Coverage Summary
- ❌ KHÔNG hardcode token / password / API key thật trong Test Data
- ❌ KHÔNG copy-paste test case giữa các feature khác nhau mà không adapt
- ❌ KHÔNG viết mỗi OS version / mỗi browser / mỗi ngôn ngữ thành 1 TC riêng — phải MERGE theo rule mục 7.5, 7.6
- ❌ KHÔNG tự assume behavior khi AC không nói rõ — phải FLAG câu hỏi cho PO/BA
- ❌ KHÔNG viết nhiều TC UI/UX cho những thứ đã hiển nhiên từ happy path
- ❌ KHÔNG skip test case khi AC thiếu — phải viết generic và note "cần bổ sung"

---

## 12. Quy trình review & nghiệm thu

1. Agent viết test case theo rule file này
2. Agent reviewer đối chiếu với file này + checklist review riêng (sẽ có file `ReviewChecklist.md`)
3. QA nghiệm thu đối chiếu với User Story / AC + checklist nghiệm thu riêng (sẽ có file `AcceptanceChecklist.md`)
4. Nếu có discrepancy giữa rule và AC → AC thắng; báo lại để update rule

---

*File này sẽ được cập nhật khi có rule mới hoặc thay đổi quy trình. Mọi thay đổi phải được thông báo cho team và re-review các test case đã viết nếu thay đổi ảnh hưởng backward.*
