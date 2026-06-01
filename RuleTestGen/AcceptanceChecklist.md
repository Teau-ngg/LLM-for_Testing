# AcceptanceChecklist.md — Checklist Nghiệm Thu Test Case cho Ticketbox

> **Đối tượng sử dụng:** QA nghiệm thu — vòng cuối cùng kiểm tra bộ test case trước khi đưa vào execute cho release.
>
> **Phạm vi:** Nghiệm thu **NỘI DUNG** test case — KHÔNG thực thi test trên môi trường thật. Execution là giai đoạn sau.
>
> **Đầu vào:** Bộ test case đã qua agent reviewer (đã fix hết MAJOR/BLOCKER issues).
>
> **Đầu ra:** Approval với điểm số chất lượng (format X/10) + quyết định assign TC vào các suite (regression / smoke / automation).

---

## 🎯 Khác biệt so với `ReviewChecklist.md`

| Khía cạnh | Agent Reviewer | QA Nghiệm Thu |
|---|---|---|
| **Góc nhìn** | Compliance với rule, technical quality | Business value, release readiness |
| **Focus chính** | Format, structure, logic đúng rule | AC tinh thần có cover đúng không, có ra được release không |
| **Kết quả** | MINOR/MAJOR/BLOCKER | Điểm số X/10 + suite assignment + sign-off |
| **Có execute không** | Không | Không (execute ở giai đoạn sau) |
| **Quyền hạn** | Comment/flag, writer tự sửa | Final sign-off, có thể reject toàn bộ |

---

## 📋 4 Focus areas của QA nghiệm thu

### FOCUS 1 — BUSINESS VALIDATION (Cover đúng tinh thần AC)

**Mục đích:** Đảm bảo bộ TC phản ánh đúng ý đồ business, không chỉ copy mechanical từ AC.

#### 1.1. AC Spirit Coverage (Tinh thần AC)
- [ ] TC phản ánh được **intent** của User Story, không chỉ letter-level của AC
- [ ] Với mỗi Scenario trong AC, TC cover được **mục đích** business, không chỉ step cụ thể
- [ ] TC có test được **critical user journey** (luồng chính user sẽ dùng nhiều nhất)
- [ ] TC có cover được các **edge case thực tế** mà user hay gặp (không chỉ edge case kỹ thuật)

#### 1.2. Business Risk Coverage
- [ ] TC cover các **failure mode** có thể gây tổn thất business:
  - Mất doanh thu (payment fail không báo user, double charge)
  - Mất trust (sai số liệu, sai order, hiển thị sai thông tin Artist/Event)
  - Complaint cao (UX frustration, flow bị stuck)
- [ ] TC có case cho các **scenario bất thường** mà user có thể rơi vào (session expired, mất mạng, double-tap)
- [ ] TC có case cho các **edge user behavior** (bỏ giữa chừng, thao tác nhanh liên tục, back navigation)

#### 1.3. Assumptions & Gaps
- [ ] Nếu có **Questions to PO/BA** trong bộ TC → tất cả đã được answer hoặc note rõ "chờ answer"
- [ ] TC viết generic (do AC thiếu) đã được bổ sung chi tiết sau khi có answer
- [ ] Không còn assumption nào chưa được xác nhận có thể ảnh hưởng logic test

#### 1.4. Domain Impact (Ticketbox-specific)
- [ ] Nếu feature liên quan **Payment** → TC đầy đủ cho các business case: refund, cancel, expired, duplicate IPN
- [ ] Nếu feature liên quan **Booking** → TC đầy đủ cho: concurrent booking, seat hold timeout, ReCaptcha integrity
- [ ] Nếu feature liên quan **Search** → TC đầy đủ cho: VI có/không dấu, Price filter, empty result UX
- [ ] Nếu feature liên quan **Artist/Organizer/Event** data → TC cover data inconsistency (profile bị xóa, event bị cancel)

---

### FOCUS 2 — CROSS-FEATURE DUPLICATE CHECK

**Mục đích:** Đảm bảo bộ TC không trùng với TC đã có trong hệ thống, tránh bloat test suite.

#### 2.1. Đối chiếu với existing test suite
- [ ] Check TC có duplicate với feature liên quan đã test trước đây không
- [ ] Với reused component/logic (vd: Avatar fallback, Follow/Unfollow logic) → đã có TC ở feature gốc chưa? Nếu có thì feature mới chỉ cần reference
- [ ] Common flow (login, logout, navigation) → không cần test lại trong mỗi feature

#### 2.2. Regression overlap
- [ ] TC mới không duplicate TC đã nằm trong regression suite hiện có
- [ ] Nếu có overlap → đánh dấu TC nào sẽ **thay thế** TC cũ (tránh 2 TC test cùng 1 thứ trong regression)

#### 2.3. Integration với feature khác
- [ ] TC có cover integration point với feature liên quan (vd: About Artist → click event → redirect Event Detail có OK không)
- [ ] Không có gap giữa các feature (vd: feature A test đến bước X, feature B test từ bước Y → khoảng X→Y không ai test)

#### 2.4. Terminology consistency
- [ ] Thuật ngữ dùng trong TC nhất quán với các feature khác trong hệ thống
- [ ] Naming convention của ID nhất quán (không tự tạo prefix mới nếu đã có prefix tương tự)

---

### FOCUS 3 — SUITE ASSIGNMENT (Regression / Smoke / Automation)

**Mục đích:** Quyết định TC nào vào suite nào, tối ưu effort execution.

#### 3.1. Smoke Test Suite
TC được assign vào Smoke suite nếu đạt đủ:
- [ ] Là Happy path của critical feature
- [ ] Priority = High
- [ ] Thời gian execute ngắn (< 5 phút/TC)
- [ ] Cover được "feature còn sống/chết" sau mỗi deploy

**Đánh dấu trong cột Note:** `[SMOKE]`

#### 3.2. Regression Test Suite
TC được assign vào Regression suite nếu đạt đủ:
- [ ] Priority = High hoặc Medium
- [ ] Cover logic nghiệp vụ core (không phải UI thuần)
- [ ] Có khả năng bị break bởi thay đổi ở feature khác
- [ ] Bao gồm các case bug đã từng xảy ra trong quá khứ

**Đánh dấu trong cột Note:** `[REGRESSION]`

#### 3.3. Automation Candidate
TC được assign làm Automation candidate nếu đạt đủ:
- [ ] Có Expected Result rõ ràng, deterministic (không phụ thuộc subjective judgment)
- [ ] Test Data không thay đổi nhiều qua các lần run
- [ ] Không phụ thuộc external system không control được (trừ khi có mock)
- [ ] Execute frequency cao (regression thường xuyên) → auto sẽ tiết kiệm effort
- [ ] Phù hợp với Playwright (JavaScript) — framework Ticketbox đang dùng

**Đánh dấu trong cột Note:** `[AUTO-CANDIDATE]`

**Ưu tiên automation theo thứ tự:**
1. Happy path + critical flow (Payment, Booking, Search)
2. Regression high-value TC
3. Negative/Boundary có pattern rõ ràng
4. API TC có response schema cố định

**KHÔNG ưu tiên automation:**
- TC UI thuần (visual check)
- TC Accessibility (dùng screen reader manual)
- TC Security đặc thù (pentest)
- TC Performance benchmark (cần tool riêng)

#### 3.4. Manual-only TC
TC chỉ execute manual, không auto:
- Exploratory testing
- UX/UI subjective check
- Multi-device physical test (iOS 12 physical device)
- Accessibility screen reader test

**Đánh dấu trong cột Note:** `[MANUAL-ONLY]`

---

### FOCUS 4 — RELEASE READINESS (Final Sign-off)

**Mục đích:** Quyết định bộ TC đủ chất lượng để đưa vào execute cho release không.

#### 4.1. Completeness check
- [ ] Tất cả AC Scenario đã có TC cover (không miss Scenario nào)
- [ ] Coverage Summary không còn ❌ Skip mà chưa có lý do thuyết phục
- [ ] Questions to PO/BA đã được resolve hết (hoặc note rõ những câu pending không blocker)
- [ ] Số lượng TC hợp lý cho scope feature (không quá ít → thiếu cover, không quá nhiều → overkill)

#### 4.2. Risk assessment
- [ ] Nếu release với bộ TC này, các rủi ro business **chính** đã được bảo vệ
- [ ] Các critical path không có gap
- [ ] Bộ TC có đủ để detect regression khi deploy sau này
- [ ] Có plan fallback nếu execution phát hiện TC chưa đủ

#### 4.3. Dependency check
- [ ] Các dependency đã được clarify (dependency với feature khác, với API, với data)
- [ ] Test data cho execution đã được chuẩn bị hoặc có plan chuẩn bị
- [ ] Môi trường test đã sẵn sàng (hoặc có plan setup)

#### 4.4. Release fit
- [ ] Scope bộ TC match với scope release (không dư thừa TC cho feature chưa release)
- [ ] TC có note rõ TC nào test trên dev / staging / production
- [ ] Priority phân bổ hợp lý với timeline release (đủ High-priority TC để cover must-have feature)

---

## 🎯 Scoring System (Chấm điểm chất lượng)

QA nghiệm thu chấm điểm bộ TC theo thang **10 điểm**, phân bổ theo 4 focus:

| Focus | Trọng số | Tiêu chí chấm điểm |
|---|---|---|
| **Focus 1 - Business Validation** | **4 điểm** | AC coverage (2đ) + Business risk (1đ) + Domain impact (1đ) |
| **Focus 2 - Cross-feature Duplicate** | **1.5 điểm** | Không duplicate (1đ) + Integration coverage (0.5đ) |
| **Focus 3 - Suite Assignment** | **1.5 điểm** | Đã assign đủ suite (1đ) + Tính hợp lý của assignment (0.5đ) |
| **Focus 4 - Release Readiness** | **3 điểm** | Completeness (1đ) + Risk protection (1đ) + Release fit (1đ) |
| **TOTAL** | **10 điểm** | |

### Grading thresholds

| Điểm | Status | Ý nghĩa | Action |
|---|---|---|---|
| **9.0 - 10** | 🟢 **EXCELLENT - Approved** | Chất lượng cao, đi execute ngay | Sign-off, proceed to execution |
| **7.0 - 8.9** | 🟢 **PASS - Approved with notes** | Đủ chất lượng, có note minor cần cải thiện ở round sau | Sign-off, log notes cho writer |
| **5.0 - 6.9** | 🟡 **CONDITIONAL PASS** | Pass nếu các issue không critical | QA trưởng quyết định; có thể pass với risk acceptance |
| **3.0 - 4.9** | 🟠 **REWORK REQUIRED** | Không đủ chất lượng, cần sửa trước khi đi tiếp | Reject, writer rework |
| **< 3.0** | 🔴 **REJECTED** | Không đạt, cần làm lại gần như toàn bộ | Reject, rewrite từ đầu |

**Rule quan trọng:**
- Điểm < 5 nếu có **ít nhất 1 critical issue** ở Focus 1 (business validation) HOẶC Focus 4 (release readiness)
- Có thể pass với điểm thấp hơn nếu các issue đều ở Focus 2/3 và được document rõ để fix round sau
- QA trưởng có thể override pass/fail khi có tranh chấp

---

## 📄 Format Acceptance Report

Sau nghiệm thu, QA tạo report theo format:

```markdown
# Acceptance Report — [Feature Name]

**QA Acceptor:** [Tên]
**Writer:** [Tên]
**Reviewer:** [Tên]
**Date:** [dd/mm/yyyy]
**Release:** [Release name/version]

## Summary

**Quality Score: [X.X/10]**
**Status:** 🟢 APPROVED / 🟡 CONDITIONAL PASS / 🟠 REWORK / 🔴 REJECTED

**Breakdown:**
- Focus 1 - Business Validation: X/4
- Focus 2 - Cross-feature Duplicate: X/1.5
- Focus 3 - Suite Assignment: X/1.5
- Focus 4 - Release Readiness: X/3

## Suite Assignment

| Suite | Số TC | IDs |
|---|---|---|
| Smoke | X | TC_XX, TC_XX |
| Regression | X | TC_XX, TC_XX |
| Automation Candidate | X | TC_XX, TC_XX |
| Manual-only | X | TC_XX, TC_XX |

## Strengths
- [Điểm mạnh của bộ TC]
- ...

## Issues / Notes

### Critical (blocker cho release)
- [Nếu có]

### Major (cần fix round sau nhưng không blocker release hiện tại)
- Issue #1 - [Description] - TC affected: XXX
- ...

### Minor (improvement suggestions)
- ...

## Business Risk Assessment
- Các risk đã được bảo vệ: [list]
- Các risk chưa đầy đủ: [list, note mức độ]
- Risk acceptance (nếu pass conditional): [Ai accept, lý do]

## Final Decision

- [x] Approved for execution
- [ ] Conditional pass - risk acceptance by: [Name]
- [ ] Rework required - deadline: [Date]
- [ ] Rejected - reason: [Detail]

## Handoff to Execution

- [ ] Test data prepared
- [ ] Test environment ready
- [ ] Dependencies clarified
- [ ] Assigned executor: [Name]
- [ ] Planned execution date: [Date]

---

**Signed off:** [QA Acceptor Name & Date]
```

---

## 🔄 Workflow sau nghiệm thu

### Nếu APPROVED (≥ 7.0):
1. QA ký Acceptance Report
2. Notify team writer + reviewer về kết quả
3. Log bộ TC vào test management system (Google Sheet master)
4. Handoff cho executor (có thể là chính QA nghiệm thu hoặc QA khác)
5. Lịch execution theo release timeline

### Nếu CONDITIONAL PASS (5.0 - 6.9):
1. QA trưởng review risk acceptance
2. Nếu chấp nhận risk → sign-off với note rõ các rủi ro
3. Writer fix các issue được log, không blocker release này
4. Re-nghiệm thu ở round sau cho các fix đã làm

### Nếu REWORK / REJECTED (< 5.0):
1. Quay về writer, không qua reviewer lại (vì reviewer đã pass rồi)
2. Writer fix theo Acceptance Report
3. Reviewer check lại các phần đã fix
4. QA nghiệm thu lại toàn bộ (vì có thay đổi structure)

---

## ✅ Self-check của QA trước khi sign-off

- [ ] Đã check đủ 4 focus areas
- [ ] Đã chấm điểm cho từng focus, không bỏ trống
- [ ] Đã assign TC vào các suite (smoke/regression/auto/manual)
- [ ] Đã check duplicate với existing test suite
- [ ] Đã xác nhận bộ TC đủ để release (hoặc ghi rõ lý do conditional)
- [ ] Đã tạo Acceptance Report với đủ các phần
- [ ] Đã notify writer + reviewer về kết quả
- [ ] Đã log vào test management system (nếu approved)
- [ ] Đã schedule execution (nếu approved)

---

## 📊 Metrics tracking (cho QA team)

Sau mỗi lần nghiệm thu, QA ghi lại metrics để team improve:

- **Score average** theo writer / reviewer / feature
- **Rework rate**: % bộ TC phải rework sau nghiệm thu
- **Issue pattern**: focus nào hay có issue nhất → cần update rule hoặc training
- **Suite assignment ratio**: % TC vào smoke / regression / automation (theo dõi trend)
- **Time to approve**: thời gian từ lúc submit đến khi approve

Dùng metrics để:
- Phát hiện pattern writer hay sai → update `TestCaseRule.md` hoặc training
- Phát hiện pattern reviewer hay miss → update `ReviewChecklist.md`
- Tối ưu suite assignment (nếu tỷ lệ Automation Candidate thấp → đánh giá framework/automation strategy)

---

## 🔗 Liên kết với các file khác

Acceptance workflow là **vòng cuối** trong chuỗi quality gates:

```
1. Writer viết TC (theo TestCaseRule.md)
   ↓
2. Reviewer review (theo ReviewChecklist.md)
   → Writer fix
   → Re-review (nếu cần)
   ↓
3. QA nghiệm thu (theo AcceptanceChecklist.md) ← File này
   → Approved / Rework
   ↓
4. Execution (giai đoạn sau, không thuộc scope file này)
```

Nếu issue phát hiện trong Execution mà lẽ ra phải catch ở Acceptance → feedback loop về QA để update `AcceptanceChecklist.md`.

---

*File này sẽ được cập nhật khi có thay đổi về quy trình release, tiêu chí suite assignment, hoặc scoring model. Khi `TestCaseRule.md` hoặc `ReviewChecklist.md` thay đổi → check xem file này có cần update tương ứng không.*
