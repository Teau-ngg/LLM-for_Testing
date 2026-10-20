# Community View Post Details — Test Cases

> **Feature:** Community View Post Details
> **Writer:** QA Writer (agent)
> **Source:** Community_ViewPostDetails_US_AC.docx (v1.0, 2026-05-28) + PRD pageId 292651046
> **Tuân thủ:** `TestCaseRule.md`
> **Ngày viết:** 10/06/2026

---

## Coverage Summary

| # | Coverage type (rule 7.x) | Status | Ghi chú |
|---|---|---|---|
| 7.1 | Positive / Happy path | ✅ Covered | VPD_001-003 (entry tap zone, deeplink), VPD_020-026 (post detail screen), VPD_050-051 (fullscreen happy) |
| 7.2 | Negative / Invalid input & Error handling | ✅ Covered | VPD_070 (post not available), VPD_056 (image load fail), VPD_071-072 (web block), VPD_027 (send disable empty/whitespace) |
| 7.3 | Boundary | ✅ Covered | VPD_040-046 (image cap 4:3/3:4 đúng ngưỡng, center-crop, carousel height/width), VPD_050/052/053 (counter, no-wrap đầu & cuối), VPD_027 (1 char boundary) |
| 7.4 | Edge case & Logic combination | ✅ Covered | VPD_010-013 (membership decision tree), VPD_082-084 (mid-session visibility & membership expire), VPD_054 (close giữ scroll), VPD_056 (retry) |
| 7.5 | Multilingual (VI/EN) | ⚠️ Merged | VPD_090 — 1 TC tổng quát + bảng kiểm VI/EN. Search có/không dấu KHÔNG áp dụng (không có search) |
| 7.6 | Responsive & Platform Support | ⚠️ Merged | VPD_091 (mobile iOS/Android merged) + VPD_092 (web/mWeb cross-browser merged) |
| 7.7 | Performance | ⚠️ Partial | VPD_093 — image load/retry timing & comment load mạng chậm. AC chỉ có timing retry (3×2s), không có ngưỡng load chung |
| 7.8 | Security | ✅ Covered (focus protection) | VPD_071-072 (block web/mWeb + direct URL), VPD_073-074 (screenshot iOS/Android), authorization non-member. XSS/SQLi: **Not applicable** — màn view-only; input comment defer sang PRD React/Comment/Share |
| 7.9 | Accessibility | ⚠️ Merged | VPD_094 — 1 TC tổng quát (screen reader cho locked content + alt + focus). Generic, cần bổ sung khi có a11y spec |
| 7.10 | API Testing | ❌ Not applicable | Không có API docs đi kèm. Khi có (comment pagination, post fetch) → bổ sung TC riêng |
| 7.11 | UI/UX | ⚠️ Limited | Gộp vào screen-render (VPD_020-026) & image-layout TC. Không tách TC cosmetic riêng |
| — | GA4 Tracking | ✅ Covered | VPD_060-062 |
| 8.x | Domain-specific (Booking/Payment/Search) | ❌ Not applicable | Feature không liên quan Booking/Payment/Search. Mua membership thuộc PRD User View (chỉ test "show modal", không test payment) |

**Phân bổ trọng số (tổng 45 TC):** Logic/Boundary/Edge/Negative ≈ 31 TC (entry decision tree, tap-zone, image math VPD_040-046, fullscreen logic, mid-session, GA4) ≈ **69%**. UI/Screen-render/Multilingual/Responsive ≈ 10 TC (VPD_020-026, 090-092) ≈ **22%**. Security/A11y/Performance ≈ 4 TC (073-074, 093, 094) ≈ **9%**. Đạt mục tiêu ~60-70% logic.

---

## Questions to PO/BA

> Theo rule 1.2: behavior chưa rõ trong AC. TC liên quan viết generic + note "Pending - see Q#N".

**Q1 — [IG / Image Grid] Giới hạn số ảnh tối đa trong 1 post.**
AC multi-media (IG-US-03→06) không nêu max số ảnh/post. Cần con số để viết boundary case (vd 10/20 ảnh). — Ảnh hưởng VPD_044, VPD_050.

**Q2 — [IG-US-01/02] "Center-crop vertically" — tiêu chí đo được.**
AC ghi "if taller than 4:3/3:4 → center-cropped vertically" nhưng không nói crop theo tâm hình học hay focal point, và % crop. — Ảnh hưởng Expected của VPD_040/041, QA khó xác định PASS chính xác.

**Q3 — [PD-US-04 Comment thread] Pagination / load-more.**
AC chỉ nói sort created_at DESC. Không define số comment load lần đầu, có infinite scroll/load-more không. — Ảnh hưởng VPD_023.

**Q4 — [PD-US-07 Composer] Max length & ký tự đặc biệt.**
AC chỉ define enable/disable theo ≥1 non-whitespace char. Không nêu max length comment, xử lý emoji/ký tự đặc biệt, trạng thái đang gửi. — Ảnh hưởng VPD_027.

**Q5 — [EX] Post đổi sang "Membership only" (không phải "Only me") khi mở qua deeplink.**
EX-US-01/02 chỉ cover deleted & "Only me" → modal "Post Not Available". Nếu post Public bị đổi sang **Membership only** rồi mở qua deeplink/URL thì hiển thị modal "không khả dụng" hay membership gate (login→check membership)? — Ảnh hưởng VPD_070.

**Q6 — [EX-US-03/04/05] Định nghĩa "next navigation away and return".**
Cụm "next navigation away and back" chưa liệt kê trigger cụ thể: pull-to-refresh có tính? sang tab khác quay lại? back ra feed rồi tap lại? — Ảnh hưởng VPD_082/083/084.

**Q7 — [EP-US-01 vs membership] Tap ảnh trong Membership-only post (non-member).**
EP-US-01 (tap image → fullscreen) chỉ define cho Public post. EP-US-04/05 nói membership post "tap any zone → login/membership gate". Xác nhận: tap đúng vùng ảnh của membership card (non-member) → vẫn vào membership gate, KHÔNG mở fullscreen viewer? — Ảnh hưởng VPD_011/012.

**Q8 — [RM-US-03 iOS] Screen recording trên iOS.**
AC iOS nói screenshot/recording → "black screen". iOS chặn screenshot và screen-recording bằng cơ chế khác nhau (recording dùng `isCaptured`). Xác nhận black screen áp dụng cho cả 2, hay chỉ recording? — Ảnh hưởng VPD_094.

---

## Bộ Test Case

> 10 cột theo `TestCaseRule.md` mục 2. Cột Test Result để trống (QA điền khi execute).
> Category toàn feature: `Community / View Post Details`.
>
> **Quy ước đánh số:** ID theo block chức năng (00x Entry, 02x Post Detail screen, 04x Image layout, 05x Fullscreen, 06x GA4, 07x Restrict/Negative, 08x Mid-session, 09x Merged). Trong dải liên tục không skip; khoảng trống giữa dải là ranh giới block.

### 1. ENTRY POINT (HAPPY PATH + DECISION TREE)

| ID | Test Case Description | Category | Pre-condition / Test Data | Test Step | Expected Result | Priority | Test Result Round 1 (dd/mm/yyyy) | Test Result on PROD env (dd/mm/yyyy) | Note |
|---|---|---|---|---|---|---|---|---|---|
| --- | **1. ENTRY POINT (HAPPY PATH + DECISION TREE)** | --- | --- | --- | --- | --- | | | |
| VPD_001 | Tap ảnh trong Public post → mở Image Fullscreen Viewer | Community / View Post Details | Public post có ≥1 ảnh; thử cả guest & logged-in; ở tab "Tất cả" và tab "Community" | 1. Mở tab "Tất cả" (hoặc "Community").<br>2. Tap vào 1 ảnh trong public post card. | 1. Điều hướng tới Image Fullscreen Viewer đúng ảnh vừa tap.<br>2. Hoạt động giống nhau cho cả guest và logged-in. | High | | | |
| VPD_002 | Tap vùng ngoài ảnh trong Public post → mở Post Details | Community / View Post Details | Public post; thử guest & logged-in | 1. Mở tab "Tất cả"/"Community".<br>2. Tap vào caption (hoặc avatar / engagement bar — vùng không phải ảnh). | 1. Điều hướng tới màn Post Details.<br>2. Hoạt động cho cả guest và logged-in. | High | | | |
| VPD_003 | Deeplink/URL external → mở thẳng Post Details (Public) | Community / View Post Details | Có deeplink/URL hợp lệ tới 1 public post; thử guest & logged-in | 1. Mở deeplink từ nguồn ngoài (share link / push / email).<br>2. Quan sát màn hình landing. | 1. Điều hướng trực tiếp tới Post Details đúng post.<br>2. Cả guest và logged-in đều truy cập được. | High | | | AC deeplink back-navigation chưa nêu — cần bổ sung từ PRD (back về đâu) |
| VPD_010 | Membership post — guest tap → login → quay lại với user ID | Community / View Post Details | Post Membership-only; user CHƯA login | 1. Ở trạng thái guest, mở tab "Tất cả"/"Community".<br>2. Tap bất kỳ vùng nào của membership post.<br>3. Hoàn tất login thành công. | 1. Hiển thị màn login.<br>2. Sau login thành công: quay lại trang trước với user ID đã đăng nhập (tiếp tục check membership). | High | | | |
| VPD_011 | Membership post — logged-in KHÔNG có membership → modal mua membership | Community / View Post Details | Post Membership-only; user đã login, CHƯA mua membership của creator này | 1. Login bằng user không có membership.<br>2. Tap bất kỳ vùng nào của membership post. | 1. Hiển thị modal: mua membership cho creator này.<br>2. KHÔNG hiển thị nội dung Post Details. | High | | | Tap vùng ảnh membership card — Pending, see Q7. Chi tiết modal: cần bổ sung từ PRD User View |
| VPD_012 | Membership post — logged-in CÓ membership → Post Details + badge | Community / View Post Details | Post Membership-only; user đã login, ĐÃ mua membership của creator | 1. Login bằng user có membership.<br>2. Tap bất kỳ vùng nào của membership post. | 1. Điều hướng tới Post Details.<br>2. Hiển thị membership badge trên post. | High | | | Tap vùng ảnh — Pending, see Q7 |
| VPD_013 | Membership decision tree — chuyển trạng thái sau khi mua membership | Community / View Post Details | User logged-in, vừa hoàn tất mua membership từ modal ở VPD_011 | 1. Ở modal mua membership, hoàn tất mua (giả lập thành công).<br>2. Quan sát màn hình sau khi mua. | 1. Sau khi mua thành công, re-resolve role = member → hiển thị Post Details + badge (hoặc theo flow PRD User View). | Medium | | | AC chưa đầy đủ - flow sau mua cần bổ sung từ PRD User View |

### 2. POST DETAILS SCREEN

| ID | Test Case Description | Category | Pre-condition / Test Data | Test Step | Expected Result | Priority | Test Result Round 1 (dd/mm/yyyy) | Test Result on PROD env (dd/mm/yyyy) | Note |
|---|---|---|---|---|---|---|---|---|---|
| --- | **2. POST DETAILS SCREEN** | --- | --- | --- | --- | --- | | | |
| VPD_020 | Page title hiển thị đúng | Community / View Post Details | User ở màn Post Details | 1. Mở 1 post detail.<br>2. Quan sát tiêu đề trang. | 1. Page title hiển thị — VIE: "Bài đăng" / ENG: "Post details". | Low | | | |
| VPD_021 | Caption hiển thị FULL không truncate | Community / View Post Details | Post có caption rất dài (>5 dòng) | 1. Mở post detail của post caption dài.<br>2. Quan sát caption & các field khác. | 1. Caption hiển thị đầy đủ (không cắt, không "Xem thêm").<br>2. Avatar, tên creator, timestamp, ảnh hiển thị đúng theo post card spec. | Medium | | | |
| VPD_022 | Tap avatar/tên creator → Creator Profile tab "Tất cả" | Community / View Post Details | User ở Post Details | 1. Tap avatar creator.<br>2. Quay lại, tap tên creator. | 1. Cả 2 thao tác đều điều hướng tới Creator Profile, mở tab "Tất cả".<br>2. Fire GA4 view_artist_profile source=post_details (verify VPD_062). | Medium | | | |
| VPD_023 | Comment thread sort created_at DESC + thành phần comment | Community / View Post Details | Post có ≥2 comment tạo ở thời điểm khác nhau | 1. Mở post detail.<br>2. Quan sát thứ tự comment.<br>3. Quan sát 1 comment component. | 1. Comment sort created_at DESC (mới nhất trên cùng).<br>2. Mỗi comment hiển thị: avatar user, username, thời gian comment.<br>3. Timestamp theo cùng logic relative-time như post card. | Medium | | | Pagination/load-more comment — Pending, see Q3 |
| VPD_024 | Comment thread — empty state | Community / View Post Details | Post có 0 comment | 1. Mở post detail của post chưa có comment.<br>2. Quan sát comment section. | 1. Hiển thị empty text — VIE: "Hãy là người đầu tiên bình luận" / ENG: "Be the first to comment". | Low | | | |
| VPD_025 | Sticky composer luôn hiển thị + bottom padding | Community / View Post Details | User ở Post Details (test với guest, non-member, member) | 1. Mở post detail.<br>2. Quan sát composer ở đáy.<br>3. Scroll nội dung lên/xuống. | 1. Composer fix ở đáy màn, luôn hiển thị với mọi role.<br>2. Composer gồm: avatar user + ô input placeholder ("Viết bình luận..."/"Write a comment...") + send icon disable mặc định.<br>3. Vùng scroll có bottom padding, nội dung không bị che sau composer. | Medium | | | |
| VPD_026 | Composer — send icon disable mặc định (post mới mở) | Community / View Post Details | User vừa mở post detail, chưa gõ gì | 1. Mở post detail.<br>2. Quan sát send icon khi input rỗng. | 1. Send icon ở trạng thái disable. | Low | | | |
| VPD_027 | Composer — enable/disable theo ký tự non-whitespace (boundary 1 char) | Community / View Post Details | User đang ở composer | 1. Để input rỗng → quan sát.<br>2. Gõ chỉ dấu cách (whitespace) → quan sát.<br>3. Gõ 1 ký tự non-whitespace → quan sát.<br>4. Xóa hết ký tự non-whitespace → quan sát. | 1. Input rỗng: send disable.<br>2. Chỉ whitespace: send vẫn disable.<br>3. ≥1 ký tự non-whitespace: send enable.<br>4. Xóa hết non-whitespace: send trở lại disable. | High | | | Boundary 1 char. Max length & emoji/special char — Pending, see Q4 |

### 3. IMAGE GRID LAYOUTS (BOUNDARY & LOGIC)

| ID | Test Case Description | Category | Pre-condition / Test Data | Test Step | Expected Result | Priority | Test Result Round 1 (dd/mm/yyyy) | Test Result on PROD env (dd/mm/yyyy) | Note |
|---|---|---|---|---|---|---|---|---|---|
| --- | **3. IMAGE GRID LAYOUTS (BOUNDARY & LOGIC)** | --- | --- | --- | --- | --- | | | |
| VPD_040 | Single landscape — full-width, giữ tỉ lệ, cap 4:3, center-crop khi cao hơn | Community / View Post Details | 3 post 1 ảnh landscape: ratio 16:9 (thấp hơn cap), 4:3 (đúng cap), ảnh cao hơn 4:3 (vd 1:1.2 — vẫn ratio≥1:1 dạng gần vuông) | 1. Mở post ảnh 16:9.<br>2. Mở post ảnh đúng 4:3.<br>3. Mở post ảnh cao hơn 4:3. | 1. Ảnh full container width, giữ tỉ lệ gốc.<br>2. Ảnh ≤4:3 hiển thị nguyên, không crop.<br>3. Ảnh cao hơn 4:3: bị center-crop dọc về khung 4:3. | High | | | "Center-crop vertically" tiêu chí % crop / focal point — Pending, see Q2 |
| VPD_041 | Single portrait — cột hẹp giữa ~60%, giữ tỉ lệ, cap 3:4, center-crop | Community / View Post Details | 3 post 1 ảnh portrait: ratio 3:4 (đúng cap), 9:16 (cao hơn cap), ảnh dọc nhẹ | 1. Mở post ảnh đúng 3:4.<br>2. Mở post ảnh 9:16 (cao hơn cap).<br>3. Quan sát container. | 1. Ảnh nằm cột hẹp giữa màn (~60% screen width), giữ tỉ lệ gốc.<br>2. Ảnh ≤3:4 hiển thị nguyên.<br>3. Ảnh cao hơn 3:4: center-crop dọc về 3:4. | High | | | Center-crop tiêu chí — Pending, see Q2 |
| VPD_042 | Carousel — chiều cao cố định theo ảnh ĐẦU (không đổi khi swipe) | Community / View Post Details | Post ≥2 ảnh; ảnh đầu landscape | 1. Mở post carousel (ảnh đầu landscape).<br>2. Ghi nhận chiều cao carousel.<br>3. Swipe qua các ảnh sau. | 1. Chiều cao carousel = chiều cao ảnh đầu sau khi áp cap single-media (4:3 vì landscape).<br>2. Chiều cao này cố định, KHÔNG đổi khi swipe sang ảnh khác. | High | | | |
| VPD_043 | Carousel — ảnh sau giữ tỉ lệ gốc, width co giãn, không crop/stretch | Community / View Post Details | Post ≥2 ảnh tỉ lệ khác nhau | 1. Mở carousel.<br>2. Quan sát từng ảnh sau ảnh đầu.<br>3. Kiểm tra có méo/cắt không. | 1. Mỗi ảnh sau: height = carousel height; width = height × (w gốc / h gốc).<br>2. Ảnh KHÔNG bị stretch hay crop. | High | | | |
| VPD_044 | Carousel — ảnh portrait theo sau ảnh landscape đầu → hẹp hơn | Community / View Post Details | Ảnh đầu landscape 16:9; ảnh sau portrait 9:16 | 1. Mở carousel (đầu 16:9).<br>2. Swipe tới ảnh portrait 9:16.<br>3. So sánh bề rộng với ảnh đầu. | 1. Ảnh portrait có height = carousel height (bằng ảnh đầu).<br>2. Width co giãn theo tỉ lệ → trông HẸP hơn ảnh đầu.<br>3. Không crop/stretch. | Medium | | | Max số ảnh/post — Pending, see Q1 |
| VPD_045 | Carousel — ảnh landscape theo sau ảnh portrait đầu → rộng hơn | Community / View Post Details | Ảnh đầu portrait 9:16; ảnh sau landscape 16:9 | 1. Mở carousel (đầu 9:16).<br>2. Swipe tới ảnh landscape 16:9.<br>3. So sánh bề rộng. | 1. Ảnh landscape height = carousel height.<br>2. Width co giãn → trông RỘNG hơn ảnh đầu.<br>3. Không crop/stretch. | Medium | | | |
| VPD_046 | Carousel — khoảng trống 2 bên ảnh hẹp (letterbox/align) | Community / View Post Details | Carousel ảnh đầu landscape + ảnh sau rất hẹp | 1. Mở carousel.<br>2. Swipe tới ảnh hẹp hơn khung.<br>3. Quan sát phần trống 2 bên ảnh. | 1. (Generic) Phần trống hai bên ảnh hẹp được xử lý nhất quán (căn giữa / nền) — chờ spec. | Low | | | AC chưa định nghĩa xử lý khoảng trống — cần bổ sung từ Figma/Design |

### 4. IMAGE FULLSCREEN VIEWER

| ID | Test Case Description | Category | Pre-condition / Test Data | Test Step | Expected Result | Priority | Test Result Round 1 (dd/mm/yyyy) | Test Result on PROD env (dd/mm/yyyy) | Note |
|---|---|---|---|---|---|---|---|---|---|
| --- | **4. IMAGE FULLSCREEN VIEWER** | --- | --- | --- | --- | --- | | | |
| VPD_050 | Mở viewer đúng ảnh đã tap + counter đúng | Community / View Post Details | Post có 5 ảnh | 1. Mở post detail có 5 ảnh.<br>2. Tap ảnh thứ 3. | 1. Viewer mở, hiển thị ảnh 3 ở giữa.<br>2. Counter hiển thị "3/5". | High | | | |
| VPD_051 | Swipe trái/phải điều hướng ảnh + counter cập nhật real-time | Community / View Post Details | Đang ở viewer, post 5 ảnh, đang ở ảnh 2 | 1. Swipe trái → quan sát.<br>2. Swipe phải → quan sát.<br>3. Quan sát counter mỗi lần. | 1. Swipe trái → ảnh kế tiếp (next).<br>2. Swipe phải → ảnh trước (previous).<br>3. Counter cập nhật real-time theo vị trí (vd "2/5"). | High | | | |
| VPD_052 | Boundary — ảnh đầu (1/5) swipe phải → không wrap, bounce | Community / View Post Details | Đang ở ảnh 1 của 5 | 1. Ở ảnh 1, swipe phải (về previous). | 1. Vẫn ở ảnh 1 (không wrap về ảnh cuối).<br>2. Có bounce animation báo biên. | Medium | | | Boundary đầu |
| VPD_053 | Boundary — ảnh cuối (5/5) swipe trái → không wrap, bounce | Community / View Post Details | Đang ở ảnh 5 của 5 | 1. Ở ảnh 5, swipe trái (về next). | 1. Vẫn ở ảnh 5 (không wrap về ảnh đầu).<br>2. Có bounce animation báo biên. | Medium | | | Boundary cuối |
| VPD_054 | Đóng viewer → về Post Details đúng scroll position | Community / View Post Details | User đã scroll tới vị trí cụ thể trong Post Details rồi mở viewer | 1. Scroll Post Details tới giữa comment.<br>2. Mở viewer từ 1 ảnh.<br>3. Tap nút Close. | 1. Viewer đóng.<br>2. Post Details hiển thị lại đúng scroll position trước khi mở viewer. | Medium | | | |
| VPD_055 | Render ảnh trong viewer — original aspect, nền đen, không action, no zoom | Community / View Post Details | Viewer đang mở | 1. Mở viewer.<br>2. Quan sát ảnh & nền.<br>3. Thử pinch-to-zoom & double-tap. | 1. Ảnh hiển thị nguyên tỉ lệ gốc, fit viewport (letterbox).<br>2. Nền đen, không có nút share/action khác.<br>3. Pinch-zoom & double-tap zoom KHÔNG hoạt động (Phase 1). | Low | | | |
| VPD_056 | Image load fail — auto-retry 3 lần × 2s, lỗi → text, vẫn swipe được | Community / View Post Details | Giả lập 1 ảnh trong viewer fetch fail (chặn URL ảnh đó) | 1. Mở viewer tới ảnh bị fail.<br>2. Quan sát spinner & retry.<br>3. Để retry hết 3 lần.<br>4. Swipe sang ảnh khác rồi quay lại ảnh fail. | 1. Hiển thị spinner, auto-retry tối đa 3 lần, cách 2 giây.<br>2. Hết 3 lần fail: hiển thị text giữa màn — EN: "Could not load image" / VN: "Không thể tải ảnh".<br>3. Vẫn swipe được sang ảnh trước/sau.<br>4. Quay lại ảnh fail → trigger load lại. | Medium | | | Negative + timing |

### 5. GA4 TRACKING

| ID | Test Case Description | Category | Pre-condition / Test Data | Test Step | Expected Result | Priority | Test Result Round 1 (dd/mm/yyyy) | Test Result on PROD env (dd/mm/yyyy) | Note |
|---|---|---|---|---|---|---|---|---|---|
| --- | **5. GA4 TRACKING** | --- | --- | --- | --- | --- | | | |
| VPD_060 | Fire view_post_details với post_id & source đúng | Community / View Post Details | GA4 DebugView bật | 1. Mở GA4 DebugView.<br>2. Vào Post Details từ post_all_tab → kiểm tra log.<br>3. Lặp lại từ pinned_post_community_tab và feed_community_tab. | 1. Fire `view_post_details` với `post_id` (String) đúng giá trị.<br>2. `source` đúng 1 trong: post_all_tab / pinned_post_community_tab / feed_community_tab theo điểm vào. | Medium | | | Cần tool: GA4 DebugView |
| VPD_061 | Fire view_image_fullscreen với source đúng (gồm post_details) | Community / View Post Details | GA4 DebugView bật | 1. Tap ảnh để mở viewer từ feed → kiểm tra log.<br>2. Tap ảnh mở viewer từ trong Post Details → kiểm tra log. | 1. Fire `view_image_fullscreen` với `post_id`.<br>2. `source` đúng 1 trong: post_all_tab / pinned_post_community_tab / feed_community_tab / post_details. | Medium | | | Cần tool: GA4 DebugView. post_id type "String/Number" cần chốt — see review report |
| VPD_062 | Fire view_artist_profile source=post_details | Community / View Post Details | GA4 DebugView bật; user ở Post Details | 1. Ở Post Details, tap avatar/tên creator.<br>2. Kiểm tra event log. | 1. Fire `view_artist_profile` với `source = post_details`. | Medium | | | Cần tool: GA4 DebugView |

### 6. RESTRICT MEMBERSHIP CONTENT & EXCEPTIONAL (NEGATIVE)

| ID | Test Case Description | Category | Pre-condition / Test Data | Test Step | Expected Result | Priority | Test Result Round 1 (dd/mm/yyyy) | Test Result on PROD env (dd/mm/yyyy) | Note |
|---|---|---|---|---|---|---|---|---|---|
| --- | **6. RESTRICT MEMBERSHIP CONTENT & EXCEPTIONAL (NEGATIVE)** | --- | --- | --- | --- | --- | | | |
| VPD_070 | Post Not Available — deleted / Only me khi mở qua deeplink | Community / View Post Details | Post đã bị xóa; post khác đã đổi privacy "Only me"; có deeplink tới từng post | 1. Mở deeplink tới post đã xóa.<br>2. Mở deeplink tới post "Only me".<br>3. Tap nút trên modal. | 1. Cả 2 hiển thị modal — Title "Bài đăng không khả dụng"/"Post Not Available" + description đúng.<br>2. Nút "Về trang Cộng đồng"/"Go to Community" → redirect tới tab Community. | High | | | Case đổi sang "Membership only" qua deeplink — Pending, see Q5 |
| VPD_071 | Block Membership-only trên Web/mWeb — click từ feed | Community / View Post Details | Trên Web/mWeb; post membership-only | 1. Trên Web/mWeb, click post membership-only từ feed.<br>2. Quan sát.<br>3. Tap "Đã hiểu"/"Got it". | 1. Hiển thị modal app-redirect đúng text VI/EN.<br>2. KHÔNG hiển thị nội dung post detail.<br>3. Tap nút → đóng modal, ở lại trang hiện tại. | High | | | |
| VPD_072 | Block Membership-only trên Web/mWeb — truy cập URL trực tiếp | Community / View Post Details | Trên Web/mWeb; URL trực tiếp tới membership post detail | 1. Mở URL trực tiếp tới membership post trên Web/mWeb.<br>2. Quan sát. | 1. Hiển thị cùng modal app-redirect (text & nút như VPD_071).<br>2. KHÔNG hiển thị nội dung post. | High | | | Security/authorization — không bypass được qua URL |
| VPD_073 | Screenshot prevention — iOS (black screen) | Community / View Post Details | iOS app; đang xem membership-only post detail | 1. Mở membership post detail trên iOS.<br>2. Chụp màn hình.<br>3. Quay màn hình. | 1. Ảnh chụp / bản ghi hiển thị màn hình đen (không lộ nội dung). | High | | | Screen recording iOS cơ chế khác screenshot — Pending, see Q8. Cần thiết bị iOS thật |
| VPD_074 | Screenshot prevention — Android (system warning) | Community / View Post Details | Android app; đang xem membership-only post detail | 1. Mở membership post detail trên Android.<br>2. Chụp màn hình / quay màn hình. | 1. Hiển thị cảnh báo/lỗi cấp hệ điều hành theo OS & ngôn ngữ thiết bị; chặn theo cơ chế Android. | High | | | Cần thiết bị Android thật |

### 7. MID-SESSION STATE TRANSITION (EDGE CASE)

| ID | Test Case Description | Category | Pre-condition / Test Data | Test Step | Expected Result | Priority | Test Result Round 1 (dd/mm/yyyy) | Test Result on PROD env (dd/mm/yyyy) | Note |
|---|---|---|---|---|---|---|---|---|---|
| --- | **7. MID-SESSION STATE TRANSITION (EDGE CASE)** | --- | --- | --- | --- | --- | | | |
| VPD_082 | Public → Membership-only giữa session (non-member đang xem) | Community / View Post Details | Non-member đang ở Full View của 1 public post; creator đổi sang Membership only | 1. Non-member mở full view public post P.<br>2. Creator đổi P sang "Membership only" (lúc user còn trên màn).<br>3. Quan sát màn hiện tại.<br>4. Điều hướng đi rồi quay lại P. | 1. Session hiện tại tiếp tục hiển thị Full View, không force refresh/lỗi.<br>2. Lần điều hướng đi-về kế: P render dạng membership card thumb (locked view). | Medium | | | Định nghĩa "navigation away and return" — Pending, see Q6 |
| VPD_083 | Membership-only → Public giữa session (non-member locked) | Community / View Post Details | Non-member đang ở Locked Post Detail; creator đổi sang Public | 1. Non-member ở màn locked của membership post P.<br>2. Creator đổi P sang "Public".<br>3. Refresh / re-entry vào P. | 1. Lần refresh/re-entry kế: hệ thống render Full View của P. | Medium | | | Pending, see Q6 |
| VPD_084 | Membership expire giữa session (member đang xem) | Community / View Post Details | Member đang ở Full View membership post; membership hết hạn khi đang trên màn | 1. Member mở full view membership post P.<br>2. Membership hết hạn lúc còn trên màn.<br>3. Quan sát màn hiện tại.<br>4. Điều hướng đi rồi quay lại P. | 1. Session hiện tại tiếp tục Full View, không bị kick/lỗi.<br>2. Lần điều hướng đi-về kế: re-resolve role = non-member → render membership card thumb (locked view). | Medium | | | Pending, see Q6 |

### 8. MULTILINGUAL / RESPONSIVE / PERFORMANCE / ACCESSIBILITY (MERGED)

| ID | Test Case Description | Category | Pre-condition / Test Data | Test Step | Expected Result | Priority | Test Result Round 1 (dd/mm/yyyy) | Test Result on PROD env (dd/mm/yyyy) | Note |
|---|---|---|---|---|---|---|---|---|---|
| --- | **8. MULTILINGUAL / RESPONSIVE / PERFORMANCE / A11Y (MERGED)** | --- | --- | --- | --- | --- | | | |
| VPD_090 | Multilingual VI/EN — verify toàn bộ label/text (MERGED) | Community / View Post Details | App đổi locale VI/EN.<br>**Bảng kiểm:**<br>• Page title: "Bài đăng" / "Post details"<br>• Composer placeholder: "Viết bình luận..." / "Write a comment..."<br>• Empty comment: "Hãy là người đầu tiên bình luận" / "Be the first to comment"<br>• Image load error: "Không thể tải ảnh" / "Could not load image"<br>• Web block modal + nút: "...tải và mở ứng dụng..." + "Đã hiểu" / "...download and open app..." + "Got it"<br>• Post Not Available: title + description + nút "Về trang Cộng đồng" / "Go to Community" | 1. Set locale = VI, đi qua các màn (post detail, empty comment, viewer error, web block, post-not-available).<br>2. Verify từng label theo cột VI.<br>3. Switch EN.<br>4. Verify từng label theo cột EN. | 1. Tất cả label đúng ngôn ngữ theo bảng kiểm, không lẫn, không thiếu key, không vỡ layout do độ dài text. | Medium | | | MERGE multilingual theo rule 7.5. Search có/không dấu không áp dụng |
| VPD_091 | Responsive Mobile — flow chính trên iOS & Android (MERGED) | Community / View Post Details | iOS 12, iOS latest, Android 12, Android latest | 1. Thực hiện flow chính (mở post detail → xem caption/comment → mở image viewer → swipe → đóng → composer) trên iOS 12.<br>2. Lặp lại trên iOS latest.<br>3. Lặp lại Android 12.<br>4. Lặp lại Android latest. | 1. Cả 4 cấu hình: layout không vỡ, image layout/carousel/viewer đúng, composer sticky đúng, không crash. | Medium | | | MERGE platform theo rule 7.6 (iOS ≥12, Android ≥12) |
| VPD_092 | Responsive Web/mWeb — cross-browser (MERGED) | Community / View Post Details | Chrome, Safari, Firefox, Edge (latest) | 1. Thực hiện flow xem public post detail + mở image viewer trên Chrome.<br>2. Lặp lại Safari, Firefox, Edge.<br>3. Trên mỗi browser, thử mở membership post. | 1. Public post detail & viewer đúng trên cả 4 browser (Chrome-first).<br>2. Membership post → modal app-redirect đúng (theo VPD_071/072). | Medium | | | MERGE cross-browser theo rule 7.6 |
| VPD_093 | Performance — image load/retry timing & comment khi mạng chậm | Community / View Post Details | Throttle mạng 3G; post nhiều ảnh & nhiều comment | 1. Throttle 3G, mở post detail nhiều ảnh.<br>2. Quan sát loading ảnh & spinner viewer.<br>3. Quan sát load comment. | 1. Có loading/spinner trong lúc tải, không màn trắng/đơ.<br>2. Retry ảnh đúng nhịp 3 lần × 2s (theo VPD_056).<br>3. Comment load không crash khi mạng chậm. | Low | | | Generic - AC chỉ có timing retry, không có ngưỡng load chung |
| VPD_094 | Accessibility — screen reader + alt + focus (MERGED, generic) | Community / View Post Details | Bật VoiceOver (iOS)/TalkBack (Android) | 1. Bật screen reader.<br>2. Swipe qua: avatar/tên creator, ảnh post, nút close viewer, counter, composer, modal locked/web-block.<br>3. Kiểm tra label đọc được. | 1. Mọi element có accessibility label/alt mô tả được (ảnh có alt; nút close, counter đọc rõ; modal membership đọc được nội dung, không đọc trống "image"). | Low | | | Generic - AC chưa có a11y spec, cần bổ sung từ Design. MERGE a11y theo rule 7.9 |

