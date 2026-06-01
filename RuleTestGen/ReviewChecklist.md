# ReviewChecklist.md — Checklist Review Test Case cho Ticketbox

> **Đối tượng sử dụng:** Agent reviewer — thực hiện review test case do agent writer tạo, trước khi chuyển cho QA nghiệm thu.
>
> **Quyền hạn:** Reviewer **CHỈ comment/flag**, **KHÔNG tự sửa** test case. Writer chịu trách nhiệm fix theo feedback.
>
> **Nguồn tham chiếu:** `TestCaseRule.md` (rule gốc) + User Story + Acceptance Criteria + tài liệu bổ sung (Figma, API docs).

---

## 🎯 Mục tiêu review

Đảm bảo bộ test case:
1. Tuân thủ rule trong `TestCaseRule.md`
2. Cover đủ và đúng trọng tâm theo AC
3. Logic test match với business logic
4. Chất lượng viết đủ để QA execute được ngay
5. Không trùng lặp, không conflict
6. Nhất quán với các feature liên quan

---

## 📋 6 Dimensions review (theo thứ tự ưu tiên)

### DIMENSION 1 — COMPLIANCE (Tuân thủ `TestCaseRule.md`)

**Mục đích:** Check format, structure, convention có đúng rule không.

#### 1.1. Template & Columns
- [ ] Đủ 10 column theo đúng thứ tự: ID / Description / Category / Pre-condition & Test Data / Test Step / Expected Result / Priority / Test Result Round 1 / Test Result PROD / Note
- [ ] Các column Test Result Round 1, Test Result PROD để trống (writer không điền)
- [ ] Row header nhóm (vd "1. LUỒNG CHẠY CHUẨN") có ID = `---`, các column còn lại đúng format `---`

#### 1.2. Test Case ID
- [ ] ID theo format `[PREFIX]_[NUMBER]` — prefix viết HOA, lấy ký tự đầu của feature
- [ ] ID unique trong toàn file, không duplicate
- [ ] Đánh số tuần tự, không skip số (vd: không nhảy từ 003 sang 005)
- [ ] Prefix nhất quán trong cùng 1 feature

#### 1.3. Category
- [ ] Category là tên module, KHÔNG phải loại test (không được ghi "Positive", "Negative" vào Category)
- [ ] Format `[Parent Module] / [Child Module]` nếu có cấp con
- [ ] Tên module nhất quán trong các TC cùng feature

#### 1.4. Test Step & Expected Result
- [ ] Test Step viết theo Traditional, đánh số `1.`, `2.`, `3.`
- [ ] Mỗi step bắt đầu bằng động từ (Nhập, Click, Chọn, Scroll, Gõ, Mở, Gửi request...)
- [ ] Mỗi step = 1 hành động, không gộp nhiều thao tác
- [ ] Expected Result đánh số tương ứng với Test Step
- [ ] Expected Result quan sát được, không mô tả trạng thái nội bộ (trừ khi có cách verify)
- [ ] KHÔNG dùng Gherkin (Given-When-Then) trong Test Step

#### 1.5. Priority
- [ ] Chỉ dùng 3 giá trị: `High` / `Medium` / `Low`
- [ ] Priority gán đúng theo rule mục 6 của `TestCaseRule.md`:
  - High: Happy path chính, payment/booking critical, security critical, blocker
  - Medium: Validation thường, edge case vừa phải, multilingual, responsive phổ biến
  - Low: UI cosmetic, accessibility non-blocker, boundary hiếm gặp

#### 1.6. Pre-condition / Test Data
- [ ] Pre-condition mô tả rõ trạng thái hệ thống/user/dữ liệu trước khi test
- [ ] Test Data cụ thể, không ghi chung chung "dữ liệu hợp lệ"
- [ ] Với API: có request body, header, params cụ thể
- [ ] Không hardcode token/password/API key thật — dùng placeholder `<valid_token>`

---

### DIMENSION 2 — COVERAGE ADEQUACY (Đủ coverage, skip đúng)

**Mục đích:** Check bộ test case có cover đủ các loại test cần thiết không, skip có hợp lý không.

#### 2.1. Coverage Summary (ở đầu file)
- [ ] Có **Coverage Summary** liệt kê 12 coverage types (7.1 → 7.12 của rule)
- [ ] Mỗi coverage có status rõ: ✅ Covered / ⚠️ Merged / ❌ Skip
- [ ] Coverage skip có **lý do cụ thể** (không chung chung "không áp dụng")

#### 2.2. Trọng số coverage
- [ ] Tỷ lệ TC **logic/boundary/validation/edge case** chiếm **~60-70%** tổng số TC
- [ ] Tỷ lệ TC **UI/multilingual/responsive/behavior** ở mức **~20-30%**, không dominant
- [ ] Nếu tỷ lệ lệch đáng kể (vd: 50% là multilingual), flag để writer rebalance

#### 2.3. Merge đúng rule
- [ ] **Multilingual:** có 1 TC tổng quát với bảng kiểm VI/EN cho toàn bộ label — KHÔNG viết mỗi section 1 TC multilingual
- [ ] **Responsive Mobile:** có 1 TC merged cho iOS 12, iOS latest, Android 12, Android latest — trừ khi có logic khác biệt
- [ ] **Cross-browser Web:** có 1 TC merged cho Chrome/Safari/Firefox/Edge — trừ khi có logic khác biệt
- [ ] **Accessibility:** có 1 TC merged tổng quát (alt text + aria-label + keyboard nav + focus visible)

#### 2.4. Coverage bắt buộc theo domain (Ticketbox-specific)
- [ ] **Booking Flow** → có đủ 5 case ReCaptcha Token (valid/missing/expired/reused/fake)
- [ ] **Payment Flow** → có đủ 6 case IPN (success/timeout 10 phút/fail/duplicate/late/fake signature)
- [ ] **Search** → có case có dấu vs không dấu, có case Price filter (Free/Fee)

#### 2.5. Coverage theo loại test
- [ ] **Positive/Happy path:** có ít nhất 1 TC cho luồng chính
- [ ] **Negative:** có TC cho invalid input, error handling, required field
- [ ] **Boundary:** có TC min/max, empty, special char, Unicode/emoji
- [ ] **Edge case:** có TC cho combination hiếm, state transition bất thường
- [ ] **Security:** nếu có user input → có TC XSS và SQL injection
- [ ] **API Testing:** nếu có API docs → có TC cho status code, schema, auth

---

### DIMENSION 3 — LOGIC CORRECTNESS (Match AC, logic đúng)

**Mục đích:** Check test case có phản ánh đúng business logic không.

#### 3.1. Match với AC
- [ ] Mỗi Scenario trong AC đều có ít nhất 1 TC cover
- [ ] Expected Result của TC khớp với behavior mô tả trong AC
- [ ] KHÔNG có TC test cho yêu cầu không có trong AC (tức là writer bịa ra)
- [ ] KHÔNG có TC mâu thuẫn với AC (test behavior ngược với AC)

#### 3.2. Xử lý AC không đầy đủ
- [ ] Nếu AC reference tài liệu khác (vd: "reuse logic từ 5.3.1") mà chưa có → TC được viết **generic** và **note rõ "cần bổ sung từ [nguồn]"**
- [ ] KHÔNG có TC bịa chi tiết dựa trên assumption không có trong AC
- [ ] Nếu AC không rõ behavior → có **"Questions to PO/BA"** block ở đầu file, list câu hỏi cần clarify

#### 3.3. Logic nghiệp vụ
- [ ] Test flow logic đúng (vd: không thể confirm order trước khi IPN về)
- [ ] State transition đúng (vd: expired order không thể revive khi IPN đến muộn)
- [ ] Dependency giữa các field được test đúng (field A thay đổi → field B phải thế nào)
- [ ] Test case cho user role khác nhau (nếu có) phản ánh đúng quyền hạn

#### 3.4. Domain-specific logic
- [ ] Payment: IPN timeout đúng **10 phút** (không phải số khác)
- [ ] Booking: ReCaptcha Token phải được check trước khi submit booking
- [ ] Search VI: có dấu và không dấu phải ra **cùng kết quả** (không phải kết quả khác nhau)

---

### DIMENSION 4 — QUALITY OF WRITING (Ngôn ngữ, clarity, executable)

**Mục đích:** Check test case có đủ rõ ràng để QA execute mà không cần hỏi thêm không.

#### 4.1. Ngôn ngữ
- [ ] Tiếng Việt chuẩn, chính tả đúng
- [ ] Không viết tắt khó hiểu (trừ các thuật ngữ chuẩn: API, UI, IPN, XSS, SQL)
- [ ] Thuật ngữ nhất quán trong toàn file (không lúc gọi "người dùng", lúc gọi "user")

#### 4.2. Clarity
- [ ] Test Case Description nêu được **MỤC ĐÍCH** test (test cái gì, trong điều kiện gì)
- [ ] KHÔNG có TC mô tả mơ hồ như "kiểm tra hoạt động bình thường", "test chức năng X"
- [ ] Expected Result có thể verify được bằng quan sát hoặc công cụ

#### 4.3. Executable
- [ ] QA đọc TC có thể execute ngay mà không cần hỏi thêm writer
- [ ] Pre-condition đủ thông tin để setup môi trường test
- [ ] Test Data cụ thể, có thể copy-paste hoặc generate dễ dàng
- [ ] Nếu TC cần tool đặc biệt (DevTools, Postman, emulator) → có note rõ

#### 4.4. Format
- [ ] Không có cell bị tràn / xuống dòng sai vị trí
- [ ] Link/URL/code snippet format đúng (không bị cắt)
- [ ] Số đếm step và expected result match nhau (step có 5 số thì expected cũng phải có 5 số tương ứng, hoặc merge hợp lý)

---

### DIMENSION 5 — DUPLICATE DETECTION (Trùng lặp trong cùng feature)

**Mục đích:** Phát hiện TC trùng logic dù description khác nhau.

#### 5.1. Duplicate rõ ràng
- [ ] Không có 2 TC có cùng Pre-condition + Test Step + Expected Result
- [ ] Không có 2 TC khác ID nhưng test cùng 1 scenario

#### 5.2. Duplicate logic (ẩn)
- [ ] 2 TC khác data nhưng cùng logic → check xem có cần merge không (vd: test Facebook icon và Instagram icon có cùng logic "tap → redirect" thì nên merge)
- [ ] TC boundary và TC negative đôi khi overlap → check xem có thực sự test 2 khía cạnh khác nhau không

#### 5.3. Merge opportunity
- [ ] Nếu có nhiều TC đang viết tách ra nhưng logic gần giống nhau → flag để writer merge theo rule 7.5/7.6 của rule

---

### DIMENSION 6 — CROSS-CHECK với feature liên quan

**Mục đích:** Đảm bảo TC feature này nhất quán với TC feature khác đã có.

#### 6.1. Tránh conflict
- [ ] TC không conflict với TC của feature related (vd: feature Artist About dùng logic khác với feature Organizer About)
- [ ] Terminology nhất quán giữa các feature (vd: dùng "Artist Profile" hay "Artist Page" thống nhất)

#### 6.2. Tránh duplicate cross-feature
- [ ] Nếu feature A đã test logic chung X → feature B chỉ cần reference, không cần test lại
- [ ] Với reused component (vd: Avatar fallback logic dùng ở nhiều nơi) → có TC riêng cho component, không duplicate ở mỗi feature

#### 6.3. Dependency check
- [ ] Nếu feature này phụ thuộc feature khác (vd: About Artist phụ thuộc Artist Info 5.3.1) → có note rõ dependency
- [ ] TC có reference đến section khác → section đó có tồn tại và accessible cho QA

---

## 🚦 Workflow xử lý issue

### Phân loại issue theo mức độ

**🟢 MINOR — comment trực tiếp trong Google Sheet:**
- Typo, chính tả
- Format sai nhẹ (thiếu số thứ tự step, sai indent)
- Priority gán chưa chính xác 1 mức (High → Medium)
- Wording chưa rõ nhưng không làm sai logic
- Thiếu note nhỏ

**🟡 MAJOR — tạo Review Report riêng:**
- Thiếu TC bắt buộc (vd: thiếu case IPN timeout cho Payment)
- TC bịa behavior không có trong AC
- TC conflict với AC (test ngược với requirement)
- Duplicate logic nhiều TC
- Coverage skip không có lý do
- Tỷ lệ trọng số lệch nặng (vd: 60% là multilingual)
- Logic test sai nghiệp vụ

**🔴 BLOCKER — tạo Review Report + escalate:**
- Toàn bộ bộ TC không tuân thủ `TestCaseRule.md`
- TC không executable (QA không hiểu phải làm gì)
- Thiếu Coverage Summary / Questions to PO/BA khi cần
- Sai toàn bộ format template

### Format Review Report (cho MAJOR/BLOCKER)

Tạo file riêng với format đề xuất:

```markdown
# Review Report — [Feature Name]

**Reviewer:** [Tên]
**Writer:** [Tên]
**Date:** [dd/mm/yyyy]
**Round:** [1/2/3...]
**Status:** 🔴 Rejected / 🟡 Needs revision / 🟢 Approved with minor

## Summary
- Tổng số TC reviewed: XX
- Số MAJOR issue: X
- Số MINOR issue: X (xem comment trong Sheet)
- Số BLOCKER: X

## MAJOR Issues

### Issue #1 — [Tiêu đề ngắn]
- **Category:** Compliance / Coverage / Logic / Quality / Duplicate / Cross-check
- **Affected TC:** ABA_05, ABA_07
- **Problem:** [Mô tả]
- **Recommendation:** [Đề xuất fix]
- **Reference:** TestCaseRule.md mục X.Y / AC Scenario Z

### Issue #2 ...

## BLOCKER Issues
[Nếu có]

## Next step
- [ ] Writer fix các MAJOR/BLOCKER issues
- [ ] Writer reply comment MINOR trong Sheet
- [ ] Reviewer re-review sau khi writer fix
- [ ] Nếu pass → chuyển QA nghiệm thu
```

### Quyền hạn reviewer
- ✅ Comment trong Sheet
- ✅ Tạo Review Report
- ✅ Flag issue, đề xuất recommendation
- ❌ KHÔNG tự sửa test case của writer
- ❌ KHÔNG thay đổi format/structure file gốc

### Re-review sau khi writer fix
- Chỉ check lại các issue đã flag, không review lại toàn bộ
- Nếu writer cãi lại feedback và reviewer vẫn giữ ý kiến → escalate lên QA lead để quyết định

---

## ✅ Self-check của reviewer trước khi submit report

- [ ] Đã check đủ 6 dimensions
- [ ] Mỗi issue flag có reference cụ thể (TestCaseRule.md mục nào / AC Scenario nào)
- [ ] Recommendation cụ thể, không chung chung "cần cải thiện"
- [ ] Phân loại MINOR/MAJOR/BLOCKER đúng mức độ
- [ ] MINOR comment trong Sheet, MAJOR/BLOCKER tạo Report
- [ ] Không tự sửa test case của writer
- [ ] Review ngôn ngữ tôn trọng, constructive, focus vào nội dung không chỉ trích cá nhân

---

## 📊 Metrics tham khảo (để reviewer tự kiểm tra chất lượng review)

Sau mỗi round review, ghi lại:
- Số TC total / số TC có issue
- Số MINOR / MAJOR / BLOCKER
- Số issue theo từng dimension (để phát hiện pattern: writer hay sai ở đâu?)
- Thời gian review trung bình / TC

Dùng metrics này để:
- Update `TestCaseRule.md` nếu pattern issue lặp lại (có thể rule chưa rõ)
- Feedback cho writer nâng cao chất lượng
- Điều chỉnh checklist này nếu dimension nào quá nặng/nhẹ

---

*File này sẽ được cập nhật song song với `TestCaseRule.md`. Khi rule thay đổi → checklist phải update tương ứng.*
