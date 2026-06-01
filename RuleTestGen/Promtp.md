# Prompt Templates cho Project "Ticketbox TCs Gen"

> **Mục đích:** Các prompt template dùng để chat với Cowork agent, đảm bảo agent follow đúng workflow đã setup trong 4 file rule/checklist.
>
> **Điều kiện tiên quyết:** Đã add 4 file vào Context của project: `TestCaseRule.md`, `ReviewChecklist.md`, `AcceptanceChecklist.md`, `AboutArtist_TestCases_v2.md` (file tham khảo).

---

## 💡 Lưu ý chung về cách Cowork hoạt động với Context

**Cowork tự động load context mỗi lần chat**, bạn KHÔNG cần viết kiểu "hãy đọc file TestCaseRule.md". Tuy nhiên, để đảm bảo agent follow đúng, bạn nên:

1. **Nhắc explicit vai trò** agent đang đóng (Writer / Reviewer / Acceptor)
2. **Chỉ rõ file nào là nguồn tham chiếu chính** cho task đó
3. **Cung cấp input đầy đủ** (User Story + AC, và Figma/API docs nếu có)
4. **Yêu cầu output format cụ thể** (markdown table để copy-paste sang Google Sheets)

---

## 🎯 PROMPT 1 — WRITER (Viết test case mới)

Dùng khi: Bạn có User Story + AC và muốn agent generate bộ test case.

```
Tôi cần bạn đóng vai trò QA Writer, viết bộ test case cho feature dưới đây.

## Vai trò & Quy tắc
- Tuân thủ NGHIÊM NGẶT file `TestCaseRule.md` trong context
- Tham khảo file `AboutArtist_TestCases_v2.md` làm ví dụ về format và mức độ chi tiết
- Output sẽ được agent khác review theo `ReviewChecklist.md` và QA nghiệm thu theo `AcceptanceChecklist.md`

## Input

**Feature name:** [Điền tên feature]

**User Story:**
[Paste User Story]

**Acceptance Criteria:**
[Paste toàn bộ AC, giữ nguyên format Scenario]

**Figma/Mockup:** [Paste link hoặc "Không có"]

**API docs:** [Paste link/nội dung hoặc "Không có"]

## Yêu cầu output

1. **Coverage Summary** ở đầu file - liệt kê 12 coverage types, đánh dấu covered/merged/skipped kèm lý do
2. **Questions to PO/BA** (nếu có behavior không rõ trong AC) - liệt kê câu hỏi cần clarify
3. **Bộ test case** theo format markdown table với đủ 10 column như template Google Sheets
4. Tỷ lệ TC: ~60-70% logic/boundary/validation/edge case, ~20-30% UI/multilingual/responsive, ~10% security/accessibility/performance
5. Merge mạnh: multilingual thành 1 TC với bảng kiểm, responsive/cross-browser thành TC merged
6. Nếu AC thiếu chi tiết → viết generic + note "cần bổ sung từ [nguồn]"
7. Nếu feature liên quan Booking/Payment/Search → áp dụng rule domain-specific mục 8 của TestCaseRule.md

Hãy bắt đầu bằng cách phân tích AC trước, xác nhận hiểu đúng, rồi mới generate test case.
```

---

## 🔍 PROMPT 2 — REVIEWER (Review test case do agent khác viết)

Dùng khi: Bộ test case đã được viết, bạn muốn agent đóng vai reviewer để check quality.

```
Tôi cần bạn đóng vai trò Agent Reviewer, review bộ test case dưới đây.

## Vai trò & Quy tắc
- Tuân thủ NGHIÊM NGẶT file `ReviewChecklist.md` trong context
- Đối chiếu với `TestCaseRule.md` để check compliance
- Quyền hạn: CHỈ comment/flag, KHÔNG tự sửa test case
- Phân loại issue: MINOR (comment trong Sheet) / MAJOR (tạo Review Report) / BLOCKER (escalate)

## Input

**Feature name:** [Điền tên feature]

**User Story & AC gốc:**
[Paste lại để reviewer đối chiếu]

**Bộ test case cần review:**
[Paste bộ test case, hoặc nếu đã upload file trong cùng session thì reference file đó]

## Yêu cầu output

Review theo đủ 6 dimensions:
1. COMPLIANCE (TestCaseRule.md)
2. COVERAGE ADEQUACY
3. LOGIC CORRECTNESS
4. QUALITY OF WRITING
5. DUPLICATE DETECTION
6. CROSS-CHECK với feature liên quan

Output cần có:
1. **Summary:** Tổng TC reviewed, số MINOR/MAJOR/BLOCKER
2. **MINOR issues:** Liệt kê dưới dạng comment (ghi rõ TC nào, vị trí nào, đề xuất)
3. **MAJOR issues:** Theo format Review Report trong ReviewChecklist.md mục "Format Review Report"
4. **BLOCKER issues:** (nếu có)
5. **Status cuối:** 🔴 Rejected / 🟡 Needs revision / 🟢 Approved with minor
6. **Next step:** Cho writer fix hoặc pass qua QA nghiệm thu

Mỗi issue phải có reference cụ thể đến TestCaseRule.md mục X.Y hoặc AC Scenario Z.
```

---

## ✅ PROMPT 3 — ACCEPTOR (QA nghiệm thu)

Dùng khi: Bộ test case đã qua reviewer, cần QA nghiệm thu final để quyết định release.

```
Tôi cần bạn đóng vai trò QA Acceptor, nghiệm thu bộ test case dưới đây.

## Vai trò & Quy tắc
- Tuân thủ NGHIÊM NGẶT file `AcceptanceChecklist.md` trong context
- Focus vào BUSINESS VALUE và RELEASE READINESS, không focus compliance (reviewer đã làm)
- Chỉ nghiệm thu NỘI DUNG test case, KHÔNG thực thi test trên env
- Output: Điểm số X/10 + suite assignment + sign-off decision

## Input

**Feature name:** [Điền tên feature]

**Release target:** [Điền release version/date]

**User Story & AC gốc:**
[Paste]

**Bộ test case đã qua reviewer:**
[Paste hoặc reference file]

**Review Report từ reviewer:**
[Paste hoặc note "Reviewer đã approve với status X"]

## Yêu cầu output

Nghiệm thu theo đủ 4 focus areas:
1. BUSINESS VALIDATION (4đ) - AC tinh thần, business risk, domain impact
2. CROSS-FEATURE DUPLICATE CHECK (1.5đ)
3. SUITE ASSIGNMENT (1.5đ) - smoke/regression/automation/manual-only
4. RELEASE READINESS (3đ)

Output theo format Acceptance Report trong AcceptanceChecklist.md, bao gồm:
1. **Quality Score** và breakdown từng focus
2. **Suite Assignment table** - TC nào vào suite nào, đánh dấu [SMOKE]/[REGRESSION]/[AUTO-CANDIDATE]/[MANUAL-ONLY]
3. **Strengths** của bộ test case
4. **Issues/Notes** phân mức Critical/Major/Minor
5. **Business Risk Assessment**
6. **Final Decision:** APPROVED / CONDITIONAL PASS / REWORK / REJECTED (theo thang điểm của AcceptanceChecklist.md)
7. **Handoff to Execution** checklist

Grading thresholds (tham khảo AcceptanceChecklist.md):
- 9.0-10: EXCELLENT
- 7.0-8.9: PASS with notes
- 5.0-6.9: CONDITIONAL PASS
- 3.0-4.9: REWORK REQUIRED
- < 3.0: REJECTED

Nếu điểm < 5 do critical issue ở Focus 1 hoặc 4 → Reject, specify rework scope.
```

---

## 🔄 PROMPT 4 — MASTER (Chạy full pipeline 1 lần)

Dùng khi: Bạn muốn agent chạy cả 3 bước Writer → Reviewer → Acceptor trong 1 session (tốt để bạn kiểm chứng flow, không khuyến khích cho production vì không có separation of concerns).

```
Tôi cần bạn chạy full pipeline gen test case cho feature dưới đây, qua 3 vai trò: Writer → Reviewer → QA Acceptor.

## Quy tắc chung
- Tuân thủ 3 file: `TestCaseRule.md`, `ReviewChecklist.md`, `AcceptanceChecklist.md` trong context
- Mỗi vai trò làm xong thì chuyển vai trò tiếp theo, đánh dấu rõ "--- CHUYỂN SANG VAI TRÒ X ---"
- Ở vai trò Reviewer, phải tự tìm ra ít nhất 2-3 issue nếu có (đừng approve luôn để làm đẹp)
- Ở vai trò Acceptor, chấm điểm khách quan theo rubric

## Input

**Feature name:** [Điền]

**User Story:**
[Paste]

**Acceptance Criteria:**
[Paste]

**Figma/Mockup:** [Paste link hoặc "Không có"]

**API docs:** [Paste hoặc "Không có"]

## Yêu cầu output

### STAGE 1 — WRITER
[Bộ test case đầy đủ theo TestCaseRule.md, có Coverage Summary + Questions to PO/BA]

--- CHUYỂN SANG VAI TRÒ REVIEWER ---

### STAGE 2 — REVIEWER
[Review Report theo ReviewChecklist.md, liệt kê MINOR/MAJOR/BLOCKER]
[Nếu có MAJOR → note cho writer fix, rồi SIMULATE writer đã fix và present bộ TC v2]

--- CHUYỂN SANG VAI TRÒ QA ACCEPTOR ---

### STAGE 3 — QA ACCEPTOR
[Acceptance Report theo AcceptanceChecklist.md, có Quality Score + Suite Assignment + Final Decision]

### STAGE 4 — DELIVERABLES
1. **Bộ test case final** (đã fix issue từ reviewer)
2. **Suite assignment table**
3. **Acceptance report summary**

Trình bày theo thứ tự, rõ ràng từng stage.
```

---

## 🎯 PROMPT 5 — QUICK WRITER (Dùng hàng ngày, ngắn gọn)

Dùng khi: Đã quen workflow, chỉ muốn gen nhanh mà không cần viết prompt dài.

```
[WRITER] Feature: [tên]

US: [User Story]

AC:
[Paste AC]

[Figma/API nếu có]

Hãy gen test case theo TestCaseRule.md. Output markdown table để tôi copy sang Sheets. Nhớ Coverage Summary + Questions to PO/BA nếu có.
```

---

## 🔍 PROMPT 6 — QUICK REVIEWER

```
[REVIEWER] Review bộ TC dưới đây theo ReviewChecklist.md đủ 6 dimensions.

AC: [paste hoặc reference]

TC: [paste hoặc "xem file đã upload"]

Output: MINOR comments + MAJOR report + Status cuối.
```

---

## ✅ PROMPT 7 — QUICK ACCEPTOR

```
[ACCEPTOR] Nghiệm thu bộ TC dưới đây theo AcceptanceChecklist.md.

Feature: [tên] - Release: [version]

AC: [paste hoặc reference]

TC: [paste hoặc reference]

Reviewer status: [Approved / Approved with minor]

Output: Quality Score /10 + Suite Assignment + Final Decision.
```

---

## 📌 Tips sử dụng Cowork hiệu quả

### 1. Về Context files
- Cowork load CÁC FILE TRONG CONTEXT mỗi lần chat, bạn KHÔNG cần upload lại
- Khi sửa file rule → update file trong Context, mọi chat sau sẽ dùng version mới
- File test case mẫu (`AboutArtist_TestCases_v2.md`) chỉ để tham khảo, agent không bắt buộc copy style y hệt

### 2. Về cách phân session
**Best practice:**
- **1 session = 1 feature** (Writer → Reviewer → Acceptor trong cùng session OK, giữ được context)
- **Hoặc 3 session riêng cho 3 vai trò** (cleaner, reviewer không bị ảnh hưởng bởi writer's reasoning)

**Không nên:**
- 1 session cho nhiều feature khác nhau → context bị rối
- Dùng lại session cũ sau nhiều ngày → Cowork có thể đã lose context một phần

### 3. Về việc output sang Google Sheets
Sau khi agent output markdown table, bạn có thể:
- **Cách 1:** Copy trực tiếp table vào Google Sheets (Sheets tự parse)
- **Cách 2:** Dùng Apps Script hoặc extension "Markdown Tables Converter"
- **Cách 3:** Yêu cầu agent output dạng TSV: thêm câu "Output dưới dạng TSV (tab-separated) để paste thẳng vào Sheets"

### 4. Về việc feedback cải thiện rule
Nếu trong quá trình dùng, bạn phát hiện:
- Rule chưa cover case thực tế → update `TestCaseRule.md`
- Reviewer hay miss điểm nào → update `ReviewChecklist.md`
- QA thường reject vì cùng 1 pattern → update `AcceptanceChecklist.md` hoặc `TestCaseRule.md`

Gắn với metrics tracking ở cuối mỗi file để self-improve.

### 5. Khi agent output không đúng ý
- **Không đủ TC:** "Bạn đang thiếu coverage cho [X]. Vui lòng gen thêm theo mục 7.Y của TestCaseRule.md"
- **Quá nhiều TC UI/multilingual:** "Tỷ lệ coverage đang lệch, hãy merge lại theo rule 7.5/7.6"
- **Viết mơ hồ:** "TC [ID] viết chưa đủ executable, vui lòng làm rõ Pre-condition và Test Data cụ thể"
- **Thiếu Coverage Summary/Questions to PO/BA:** "Vui lòng bổ sung Coverage Summary ở đầu file theo format rule mục 7.12"

---

## 🎁 BONUS — Prompt cho trường hợp đặc biệt

### A. Khi chỉ cần viết TC cho 1 Scenario cụ thể

```
[WRITER] Cho feature [tên], tôi chỉ cần gen TC cho Scenario dưới đây (không cần full AC).

Scenario: [paste]

Context: [giải thích ngắn về feature nếu agent cần]

Gen TC theo TestCaseRule.md, focus vào edge case và boundary.
```

### B. Khi cần update TC đã có sau khi AC thay đổi

```
[WRITER] AC đã được cập nhật. Vui lòng update bộ TC hiện có theo AC mới.

AC cũ: [paste hoặc note diff]
AC mới: [paste]

TC hiện tại: [paste hoặc reference]

Yêu cầu:
1. Liệt kê TC nào cần thay đổi (update/remove/add)
2. Present bộ TC mới hoàn chỉnh
3. Note rõ changelog
```

### C. Khi cần extract TC cho 1 suite cụ thể

```
[ACCEPTOR] Cho bộ TC đã approved dưới đây, hãy extract ra:
1. TC vào Smoke suite
2. TC vào Regression suite
3. TC vào Automation candidates (phù hợp Playwright JS)
4. TC Manual-only

TC: [paste hoặc reference]

Output từng suite list với ID + Description + lý do chọn.
```

---

*Các prompt này là starting point. Điều chỉnh theo thực tế dùng. Nếu tìm ra prompt nào work tốt, hãy save lại và share cho team.*
