# Community Entry Point — Test Cases

> **Feature:** Community Entry Point & Feed Display
> **Writer:** QA Writer (agent)
> **Source:** PRD [Community] Entry point (Confluence pageId 292356165) + US/AC do PO cung cấp
> **Tuân thủ:** `TestCaseRule.md`
> **Ngày viết:** 09/06/2026

---

## Coverage Summary

| # | Coverage type (rule 7.x) | Status | Ghi chú |
|---|---|---|---|
| 7.1 | Positive / Happy path | ✅ Covered | CEP_001 → CEP_009 (hiển thị section, card render) + CEP_010 → CEP_018 (navigation) + CEP_040 (autoplay cơ bản) |
| 7.2 | Negative / Invalid input & Error handling | ✅ Covered | CEP_030 → CEP_036 — empty state, network error toast, post bị xóa, membership gate |
| 7.3 | Boundary | ✅ Covered | CEP_020 → CEP_029 — max 10 video / 5 pinned / 3 pinned-video slot, pagination 9 & 10, caption truncate 2 dòng |
| 7.4 | Edge case & Logic combination | ✅ Covered | CEP_040 → CEP_047 (autoplay single-active, transition 2 card, replay/resume) + CEP_048 → CEP_052 (mid-session: xóa post, đổi visibility, membership expire) |
| 7.5 | Multilingual (VI/EN) | ⚠️ Merged | CEP_070 — 1 TC tổng quát + bảng kiểm VI/EN cho toàn bộ label. Search có/không dấu KHÔNG áp dụng (feature không có search) |
| 7.6 | Responsive & Platform Support | ⚠️ Merged | CEP_071 (mobile iOS/Android merged) + CEP_072 (web desktop cross-browser merged) |
| 7.7 | Performance | ⚠️ Partial | CEP_075 — loading/skeleton & network chậm. AC không có ngưỡng performance cụ thể → viết generic |
| 7.8 | Security | ⚠️ Limited | CEP_073 — authorization (non-member không xem được membership content qua deeplink/web). XSS/SQLi: **Not applicable** — feature view-only, không có input text do user nhập (caption do creator nhập, ngoài scope) |
| 7.9 | Accessibility | ⚠️ Merged | CEP_074 — 1 TC tổng quát (screen reader label cho locked content + alt text + reduce-motion autoplay). Generic — cần bổ sung khi PRD thêm a11y spec |
| 7.10 | API Testing | ❌ Not applicable | Không có API docs đi kèm. Khi có API docs (feed pagination, sort) → bổ sung TC riêng |
| 7.11 | UI/UX | ⚠️ Limited | Gộp vào card-render TC (CEP_006 → CEP_009) + multilingual + responsive. Không tách TC cosmetic riêng |
| — | GA4 Tracking | ✅ Covered | CEP_060 → CEP_062 |
| 8.x | Domain-specific (Booking/Payment/Search) | ❌ Not applicable | Feature **không** liên quan Booking / Payment / Search. ReCaptcha, IPN 10 phút, search có dấu đều không áp dụng |

**Phân bổ trọng số (tổng 36 TC):** Logic/Boundary/Edge/Negative ≈ 24 TC (CEP_010-018 navigation logic, 020-029 boundary/sort, 030-036 negative, 040-047 autoplay, 048-052 mid-session) ≈ **67%**. UI/Card-render/Multilingual/Responsive ≈ 9 TC (CEP_006-009, 070-072) ≈ **25%**. Security/A11y/Performance/GA4-tool ≈ 3 TC (073-075) ≈ **8%**. Đạt mục tiêu ~60-70% logic.

---

## Questions to PO/BA

> Theo rule 1.2: các behavior dưới đây KHÔNG rõ trong AC. TC liên quan được viết **generic + note "Pending clarification - see Q#N"**. Cần PO/BA trả lời trước khi finalize Expected Result.

**Q1 — [US-10 Autoplay] Scroll-out rồi scroll-back: resume hay replay?**
AC-10.5 ghi "Card resume từ vị trí đã xem trước đó", nhưng quy tắc nền của US-10 ghi "scroll đi rồi quay lại → phát lại từ đầu (replay from the beginning)". Hai mô tả **mâu thuẫn trực tiếp**. Cần xác nhận: khi card từng autoplay bị scroll-out (pause) rồi user scroll lại đạt ≥50% → video **resume vị trí cũ** hay **replay từ 00:00**? — Lý do: quyết định Expected Result của CEP_043, không thể viết chắc nếu chưa rõ.

**Q2 — [US-11 Membership-only Video Card] Hành vi khi viewer LÀ active member.**
AC-11.1 chỉ mô tả card khóa cho guest/non-member (blur + lock). PRD không define: với **active member**, card membership-only có (a) hiển thị thumbnail thật (unlock), (b) có autoplay như public video không, (c) tap zone dẫn đi đâu? — Lý do: thiếu toàn bộ Expected cho nhánh member, ảnh hưởng CEP_046.

**Q3 — [US-11] Tap zone của Membership-only Video Card.**
Trong bảng PRD, ô "Tap zone" của Membership-only video card bị **để trống**. Membership-only Post Card thì có ("tap anywhere → membership gate"). Xác nhận video card có cùng behavior không? — Lý do: ảnh hưởng CEP_034.

**Q4 — [US-15 Membership expire mid-session] AC bị cụt.**
AC-15.1 dòng cuối: "nếu user tap card mid-session trước refresh và nội dung yêu cầu active membership →" — **không có vế Then**. Cần định nghĩa: tap card đó thì hiển thị gì (membership gate? toast? điều hướng đi đâu)? — Lý do: ảnh hưởng CEP_047.

**Q5 — [US-03 / US-04] Pinned video: max slot khác nhau giữa carousel và All Video page?**
Section Video carousel: "pinned video xếp trước, tối đa 3 slot". All Video page: "sort theo pinned order trong Studio trước" — không nêu giới hạn 3. Xác nhận All Video page có giới hạn số pinned video đầu không, hay hiển thị tất cả pinned theo order? — Lý do: ảnh hưởng Expected của CEP_022.

**Q6 — [US-01 / US-05] Thứ tự & điều kiện hiển thị Pinned posts carousel trong tab Community.**
AC-01.3 chỉ nói "tab Community hiển thị tất cả post public sort created_at DESC", không nhắc vị trí của Video carousel & Pinned posts carousel trong layout tab Community. US-05 mô tả Pinned posts riêng. Xác nhận thứ tự layout dọc của tab Community (Video carousel → Pinned posts → Feed list?) — Lý do: ảnh hưởng CEP_001/CEP_005 expected layout.

**Q7 — [US-01.3] "tất cả post public" vs feed hiển thị cả Membership.**
AC-01.3 nói tab Community hiển thị "tất cả post **public**", nhưng AC-06.1 (Feed list) nói hiển thị "tất cả post & video privacy **Public & Membership**". Mâu thuẫn về việc feed có chứa membership-only card không. Xác nhận. — Lý do: ảnh hưởng CEP_003 & CEP_006.

---

## Bộ Test Case

> Format: 10 cột theo `TestCaseRule.md` mục 2. Các cột **Test Result Round 1** và **Test Result PROD** để trống (QA điền khi execute).
> Category dùng cho toàn feature: `Community / Entry Point`.
>
> **Quy ước đánh số:** ID đánh theo block chức năng (mỗi nhóm 1 dải chục: 00x Happy path, 01x Navigation, 02x Boundary/Sort, 03x Negative, 04x Autoplay, 04x-05x Mid-session, 06x GA4, 07x Merged). Trong từng dải số liên tục không skip; khoảng trống giữa các dải là ranh giới block, có chủ đích để dễ chèn TC mới cùng nhóm. Tổng: 36 TC.

### 1. LUỒNG CHẠY CHUẨN (HAPPY PATH)

| ID | Test Case Description | Category | Pre-condition / Test Data | Test Step | Expected Result | Priority | Test Result Round 1 (dd/mm/yyyy) | Test Result on PROD env (dd/mm/yyyy) | Note |
|---|---|---|---|---|---|---|---|---|---|
| --- | **1. LUỒNG CHẠY CHUẨN (HAPPY PATH)** | --- | --- | --- | --- | --- | | | |
| CEP_001 | Hiển thị section Video trong tab "Tất cả" | Community / Entry Point | Creator có ≥1 video (mix Public & Membership); user mở Artist Profile, đang ở tab "Tất cả" | 1. Mở Artist Profile Page của creator có video.<br>2. Scroll qua khỏi block "Event calendar".<br>3. Quan sát section "Video". | 1. Tab "Tất cả" hiển thị.<br>2. Section "Video" xuất hiện ngay sau "Event calendar".<br>3. Hiển thị carousel video + CTA "Xem tất cả", thumb 9:16 portrait. | High | | | |
| CEP_002 | Hiển thị section "Community" preview (5 post mới nhất) trong tab "Tất cả" | Community / Entry Point | Creator có ≥5 post (không tính video); user ở tab "Tất cả" | 1. Mở Artist Profile, tab "Tất cả".<br>2. Scroll qua khỏi block "Fan Survey".<br>3. Quan sát section "Community". | 1. Section "Community" hiển thị.<br>2. Hiển thị tối đa 5 post mới nhất (loại trừ video, có thể gồm pinned post).<br>3. Thứ tự sort theo created_at DESC (post mới nhất trên cùng). | High | | | |
| CEP_003 | Vào tab "Community" qua sticky tab bar | Community / Entry Point | Creator có post & video; user ở Artist Profile | 1. Mở Artist Profile.<br>2. Tap tab "Community" trên sticky tab bar.<br>3. Quan sát danh sách. | 1. Chuyển sang tab "Community".<br>2. Hiển thị feed các post/video sort created_at DESC.<br>3. Fire GA4 view_artist_tab (verify ở CEP_050). | High | | | Note: phạm vi "public" vs "public & membership" — Pending clarification, see Q7 |
| CEP_004 | Mở trang All Video từ CTA "Xem tất cả" | Community / Entry Point | Section Video đang hiển thị (≥1 video) | 1. Ở section Video, tap CTA "Xem tất cả".<br>2. Quan sát màn hình mới. | 1. Điều hướng sang trang "All Video".<br>2. Tiêu đề "Video của {creator_name}", lưới 3 video/hàng.<br>3. Fire GA4 view_all_video source=feed_community_tab (verify ở CEP_052). | High | | | |
| CEP_005 | Hiển thị Pinned posts carousel | Community / Entry Point | Creator có ≥1 pinned post (Public/Membership) | 1. Mở tab Community.<br>2. Scroll đến section Pinned posts.<br>3. Quan sát carousel. | 1. Section "Pinned posts" hiển thị.<br>2. Hiển thị pinned post (tối đa 5), sort theo pinned order trong Star Studio.<br>3. Carousel hiển thị 1 thumb full-width, không horizontal scroll. | Medium | | | Vị trí layout pinned trong tab Community — Pending clarification, see Q6 |
| CEP_006 | Render đầy đủ Public Post Card | Community / Entry Point | Feed có ≥1 post public; 1 post đã được edit | 1. Mở tab Community.<br>2. Quan sát 1 post public.<br>3. Quan sát post đã edit. | 1. Creator block: avatar tròn + tên + verified tick + ngày tạo + icon Public.<br>2. Caption truncate 2 dòng + "Xem thêm"; engagement bar ❤️·💬·↗️ (chỉ count); image giữ tỉ lệ gốc.<br>3. Post đã edit hiển thị nhãn "Đã chỉnh sửa"/"Edited". | Medium | | | |
| CEP_007 | Render đầy đủ Public Video Card | Community / Entry Point | Feed có ≥1 video public | 1. Mở tab Community.<br>2. Quan sát 1 video card public. | 1. Author block giống Public Post Card.<br>2. Media = thumbnail (frame đầu) + center play icon overlay; tỉ lệ video gốc cap 9:16; engagement bar giống post card. | Medium | | | |
| CEP_008 | Render Membership-only Post Card (guest/non-member) | Community / Entry Point | Feed có ≥1 post membership-only; viewer = guest hoặc non-member | 1. Login bằng tài khoản non-member (hoặc xem ở trạng thái guest).<br>2. Mở tab Community.<br>3. Quan sát post membership-only. | 1. Creator block: avatar + tên + verified tick + timestamp + badge "Dành cho thành viên"/"Membership only".<br>2. Caption ẩn hoàn toàn.<br>3. Media = ảnh/thumbnail đầu bị blur (~20–30px) + lock icon trắng 48px giữa; engagement bar giống Public Post Card. | Medium | | | |
| CEP_009 | Render Membership-only Video Card (guest/non-member) | Community / Entry Point | Feed có ≥1 video membership-only; viewer = guest/non-member | 1. Xem feed ở trạng thái guest/non-member.<br>2. Quan sát video card membership-only. | 1. Creator block giống Membership-only Post Card.<br>2. Media = video thumbnail bị blur + lock icon giữa.<br>3. KHÔNG autoplay (autoplay chỉ áp dụng Public video). | Medium | | | |

### 2. NAVIGATION & TAP ZONE

| ID | Test Case Description | Category | Pre-condition / Test Data | Test Step | Expected Result | Priority | Test Result Round 1 (dd/mm/yyyy) | Test Result on PROD env (dd/mm/yyyy) | Note |
|---|---|---|---|---|---|---|---|---|---|
| --- | **2. NAVIGATION & TAP ZONE** | --- | --- | --- | --- | --- | | | |
| CEP_010 | Tap video thumb ở section Video → mở Video Player | Community / Entry Point | Section Video có ≥1 video | 1. Ở section Video carousel, tap 1 video thumb. | 1. Mở Video Player scroll (chi tiết ở PRD Video Player). | High | | | AC chưa đầy đủ - cần bổ sung từ PRD Video Player (pageId 296517651) |
| CEP_011 | Tap video card ở trang All Video → mở Video Player | Community / Entry Point | Trang All Video có ≥1 video | 1. Mở trang All Video.<br>2. Tap 1 video card. | 1. Mở Video Player scroll. | Medium | | | AC chưa đầy đủ - cần bổ sung từ PRD Video Player |
| CEP_012 | Tap pinned post card → mở Post Detail | Community / Entry Point | Section Pinned posts có ≥1 post | 1. Ở Pinned posts carousel, tap 1 post card. | 1. Mở màn hình "Post Detail". | Medium | | | AC chưa đầy đủ - cần bổ sung từ PRD View Post Details |
| CEP_013 | Tap card body Public Post Card → mở Post Detail | Community / Entry Point | Feed có ≥1 public post | 1. Mở tab Community.<br>2. Tap vào vùng card (ngoài engagement icon & image). | 1. Mở "Post Detail". | High | | | |
| CEP_014 | Tap image trong Public Post Card → mở View image details | Community / Entry Point | Feed có ≥1 public post chứa image | 1. Tap vào image trong post card. | 1. Mở View image details (PRD View Post Details), KHÔNG mở Post Detail. | Medium | | | AC chưa đầy đủ - cần bổ sung từ PRD View Post Details |
| CEP_015 | Tap "Xem thêm" caption → expand inline, không điều hướng | Community / Entry Point | Feed có post caption dài hơn 2 dòng | 1. Tìm post có caption truncate + "Xem thêm".<br>2. Tap "Xem thêm". | 1. Caption expand inline trong card.<br>2. KHÔNG điều hướng sang Post Detail. | Medium | | | |
| CEP_016 | Tap card body Public Video Card → mở Video Player | Community / Entry Point | Feed có ≥1 video public | 1. Mở tab Community.<br>2. Tap bất kỳ đâu trên video card. | 1. Mở Video Player. | High | | | |
| CEP_017 | Tap engagement icon KHÔNG điều hướng vào Post Detail | Community / Entry Point | Feed có ≥1 public post | 1. Tap icon ❤️ (hoặc 💬 / ↗️) trên engagement bar. | 1. KHÔNG mở Post Detail (engagement icon là vùng loại trừ). Hành vi tap chi tiết định nghĩa ở PRD React/Comment/Share. | Low | | | AC chưa đầy đủ - cần bổ sung từ PRD React+Comment+Share (pageId 295337986) |
| CEP_018 | Deeplink external → mở thẳng tab Community | Community / Entry Point | Có deeplink trỏ tới Community tab của 1 creator | 1. Đóng app/chưa mở app.<br>2. Mở deeplink Community tab.<br>3. Quan sát màn hình landing. | 1. App/web client mở.<br>2. Điều hướng trực tiếp vào tab Community của đúng creator. | Medium | | | |

### 3. LOGIC HIỂN THỊ, SORT & BOUNDARY

| ID | Test Case Description | Category | Pre-condition / Test Data | Test Step | Expected Result | Priority | Test Result Round 1 (dd/mm/yyyy) | Test Result on PROD env (dd/mm/yyyy) | Note |
|---|---|---|---|---|---|---|---|---|---|
| --- | **3. LOGIC HIỂN THỊ, SORT & BOUNDARY** | --- | --- | --- | --- | --- | | | |
| CEP_020 | Section Video carousel — giới hạn tối đa 10 video & sort created_at DESC | Community / Entry Point | Creator có 15 video (mix Public & Membership), không có pinned | 1. Mở section Video.<br>2. Đếm số video hiển thị.<br>3. Kiểm tra thứ tự. | 1. Carousel hiển thị đúng 10 video (không quá 10).<br>2. 10 video là các video mới nhất.<br>3. Sort theo created_at DESC. | High | | | Boundary: max=10 |
| CEP_021 | Section Video carousel — pinned video xếp trước, tối đa 3 slot | Community / Entry Point | Creator có 5 pinned video + 10 non-pinned video | 1. Mở section Video.<br>2. Quan sát 3 vị trí đầu.<br>3. Quan sát các vị trí còn lại. | 1. 3 slot đầu là pinned video (tối đa 3), theo pinned order.<br>2. Các slot còn lại fill bằng non-pinned theo auto sort (created_at DESC).<br>3. Tổng không vượt 10. | High | | | Boundary: pinned slot=3, total=10 |
| CEP_022 | Trang All Video — sort pinned-first rồi created_at DESC, 3 video/hàng | Community / Entry Point | Creator có pinned + non-pinned video | 1. Mở trang All Video.<br>2. Quan sát thứ tự.<br>3. Quan sát layout lưới. | 1. Pinned video (theo order Studio) hiển thị trước.<br>2. Tiếp theo là non-pinned sort created_at DESC.<br>3. Layout 3 video/hàng. | Medium | | | Pending clarification giới hạn pinned ở All Video - see Q5 |
| CEP_023 | Trang All Video — pagination infinite scroll page size = 9 | Community / Entry Point | Creator có ≥19 video | 1. Mở trang All Video (load 9 đầu).<br>2. Scroll xuống cuối danh sách.<br>3. Quan sát load thêm. | 1. Lần đầu hiển thị 9 video.<br>2. Khi scroll tới cuối, fetch thêm batch tiếp theo 9 video.<br>3. Tiếp tục cho đến hết, không trùng/không miss. | Medium | | | Boundary: page size=9 |
| CEP_024 | Pinned posts — giới hạn tối đa 5 post & sort theo pinned order | Community / Entry Point | Creator có 7 pinned post | 1. Mở section Pinned posts.<br>2. Đếm số post.<br>3. Kiểm tra thứ tự. | 1. Hiển thị đúng 5 pinned post (không quá 5).<br>2. Sort theo pinned order trong Star Studio. | Medium | | | Boundary: max=5 |
| CEP_025 | Feed list — pagination infinite scroll page size = 10 | Community / Entry Point | Creator có ≥21 post/video | 1. Mở tab Community (load 10 đầu).<br>2. Scroll xuống cuối.<br>3. Tiếp tục scroll. | 1. Lần đầu hiển thị 10 item.<br>2. Mỗi lần scroll tới cuối fetch thêm 10 item.<br>3. Sort created_at DESC duy trì xuyên suốt, không trùng/miss. | High | | | Boundary: page size=10 |
| CEP_026 | Feed list — sort created_at DESC cho mix post & video | Community / Entry Point | Creator có post và video xen kẽ thời gian tạo | 1. Mở tab Community.<br>2. Kiểm tra thứ tự thời gian các item. | 1. Tất cả post & video sắp xếp chung theo created_at DESC, không tách riêng theo loại. | High | | | |
| CEP_027 | Caption truncate đúng 2 dòng (post) — boundary độ dài | Community / Entry Point | 3 post: caption 1 dòng, caption đúng 2 dòng, caption >2 dòng | 1. Quan sát post caption 1 dòng.<br>2. Quan sát post caption đúng 2 dòng.<br>3. Quan sát post caption >2 dòng. | 1. Caption ≤2 dòng: hiển thị đầy đủ, KHÔNG có "Xem thêm".<br>2. Caption đúng 2 dòng: hiển thị đủ, không "Xem thêm".<br>3. Caption >2 dòng: cắt ở 2 dòng + "Xem thêm". | Medium | | | Boundary: ngưỡng 2 dòng |
| CEP_028 | Multi-media layout — normalize chiều cao, horizontal scroll khi tràn | Community / Entry Point | Post có 4 ảnh tỉ lệ khác nhau, tổng width vượt screen | 1. Quan sát post nhiều media.<br>2. Kiểm tra chiều cao & tỉ lệ.<br>3. Vuốt ngang khu media. | 1. Các media normalize cùng chiều cao cố định, giữ tỉ lệ gốc, không crop/stretch.<br>2. Khi tổng width vượt screen → enable horizontal scroll trong khu media. | Low | | | |
| CEP_029 | Ngày tạo hiển thị theo logic Time display | Community / Entry Point | Post tạo: vài phút trước, hôm nay, hôm qua, tuần trước, >1 năm | 1. Quan sát created date của từng post theo từng mốc thời gian. | 1. Định dạng ngày tạo theo logic Time display (PRD 8778271) tương ứng từng mốc. | Low | | | AC chưa đầy đủ - cần bổ sung từ PRD Time display (pageId 8778271) |

### 4. EMPTY STATE & ERROR HANDLING (NEGATIVE)

| ID | Test Case Description | Category | Pre-condition / Test Data | Test Step | Expected Result | Priority | Test Result Round 1 (dd/mm/yyyy) | Test Result on PROD env (dd/mm/yyyy) | Note |
|---|---|---|---|---|---|---|---|---|---|
| --- | **4. EMPTY STATE & ERROR HANDLING (NEGATIVE)** | --- | --- | --- | --- | --- | | | |
| CEP_030 | Ẩn section Video khi creator có 0 video | Community / Entry Point | Creator có 0 video, vẫn có post | 1. Mở tab "Tất cả" / tab Community.<br>2. Tìm section Video. | 1. Section Video bị ẩn hoàn toàn (hidden entirely), không hiển thị header rỗng hay CTA. | Medium | | | |
| CEP_031 | Ẩn section Pinned posts khi 0 pinned post | Community / Entry Point | Creator có 0 pinned post | 1. Mở tab Community.<br>2. Tìm section Pinned posts. | 1. Section Pinned posts bị ẩn hoàn toàn. | Medium | | | |
| CEP_032 | Empty state khi creator có 0 post & 0 video | Community / Entry Point | Creator có 0 post & 0 video | 1. Mở Artist Profile.<br>2. Tap tab "Community". | 1. Hiển thị màn hình rỗng với text — VIE: "Hiện chưa có bài đăng nào" / ENG: "No posts available". | Medium | | | |
| CEP_033 | Empty state trang All Video khi 0 video | Community / Entry Point | Creator có 0 video | 1. Mở trang All Video (qua deeplink hoặc khi vừa xóa hết video). | 1. Hiển thị trang rỗng + text — VIE: "Hiện không có video nào" / ENG: "No videos available". | Low | | | Edge: All Video chỉ vào được khi có ≥1 video; case này xảy ra khi video bị xóa hết sau khi đã ở trang |
| CEP_034 | Tap Membership-only card (guest/non-member) → trigger membership gate | Community / Entry Point | Feed có post membership-only; viewer = guest/non-member | 1. Login non-member (hoặc guest).<br>2. Tap bất kỳ đâu trên membership-only POST card. | 1. Trigger membership gate flow (PRD User View), không vào Post Detail. | High | | | Video card membership-only tap zone — Pending clarification, see Q3. AC chưa đầy đủ membership gate - cần bổ sung từ PRD User View |
| CEP_035 | Network error khi load feed — toast + skeleton giữ nguyên | Community / Entry Point | Tắt mạng trước khi feed load xong | 1. Bật chế độ máy bay / ngắt mạng.<br>2. Mở tab Community.<br>3. Quan sát màn hình & chờ toast biến mất.<br>4. Bật lại mạng, pull-to-refresh. | 1. Hiển thị error toast — VIE: "Không thể tải bài viết. Vui lòng kiểm tra kết nối mạng" / ENG: "Could not load feed. Please check your connection".<br>2. Sau khi toast biến mất, skeleton placeholder vẫn giữ (KHÔNG sập về empty state).<br>3. Pull-to-refresh thủ công → feed load lại thành công. | High | | | |
| CEP_036 | Pull-to-refresh feed lấy data mới nhất (App) | Community / Entry Point | User ở tab Community trên App; creator vừa đăng post mới | 1. Mở tab Community.<br>2. Kéo pull-to-refresh từ đầu danh sách. | 1. Feed reload, hiển thị data mới nhất bao gồm post vừa đăng ở đầu danh sách (created_at DESC). | Medium | | | Chỉ áp dụng App (không áp dụng web theo AC) |

### 5. AUTOPLAY VIDEO TRONG FEED (EDGE CASE & LOGIC COMBINATION)

| ID | Test Case Description | Category | Pre-condition / Test Data | Test Step | Expected Result | Priority | Test Result Round 1 (dd/mm/yyyy) | Test Result on PROD env (dd/mm/yyyy) | Note |
|---|---|---|---|---|---|---|---|---|---|
| --- | **5. AUTOPLAY VIDEO TRONG FEED (EDGE CASE & LOGIC COMBINATION)** | --- | --- | --- | --- | --- | | | |
| CEP_040 | Cuộn tới video public đạt ≥50% → autoplay muted từ 00:00 | Community / Entry Point | Feed có video public; không có video nào đang active | 1. Mở tab Community.<br>2. Scroll để 1 video card public đạt ≥50% visibility.<br>3. Quan sát. | 1. Card bắt đầu phát từ 00:00s, ở chế độ muted.<br>2. Center play icon overlay ẩn khi đang phát. | High | | | |
| CEP_041 | Chỉ MỘT video autoplay tại một thời điểm trong toàn feed | Community / Entry Point | Feed có ≥2 video public (1 ở Video carousel + 1 ở Feed list) | 1. Scroll sao cho 1 video đang autoplay.<br>2. Tiếp tục scroll để video thứ 2 vào vùng nhìn.<br>3. Quan sát đồng thời 2 card. | 1. Tại mọi thời điểm chỉ tối đa 1 card autoplay.<br>2. Khi video 2 trở thành active, video 1 phải pause (không có 2 video phát cùng lúc). | High | | | Logic combination: scope = carousel + feed list gộp chung |
| CEP_042 | Cuộn đi <50% → card pause & chuyển active sang card khác | Community / Entry Point | 1 video đang autoplay; có video khác phía dưới | 1. Để 1 video đang autoplay.<br>2. Scroll cho card đó tụt dưới 50% visibility.<br>3. Quan sát card cũ và card mới. | 1. Card cũ pause tại vị trí hiện tại, play icon overlay hiện lại.<br>2. Nếu có card khác đạt ≥50% → card đó thành active mới, phát từ 00:00. | High | | | |
| CEP_043 | Cuộn ra rồi cuộn lại cùng card — resume hay replay? | Community / Entry Point | 1 video từng autoplay, bị pause do scroll-out | 1. Để 1 video autoplay được 1 đoạn (vd 5s).<br>2. Scroll ra cho card tụt <50%.<br>3. Scroll lại cho đúng card đó đạt ≥50% lần nữa.<br>4. Quan sát vị trí phát. | 1. (Pending) Card resume từ vị trí trước (theo AC-10.5) HOẶC replay từ 00:00 (theo quy tắc nền US-10) — chốt sau khi PO trả lời. | High | | | **Pending clarification - see Q1** (AC-10.5 mâu thuẫn description). Viết generic, QA chờ chốt expected |
| CEP_044 | Video autoplay phát hết 1 lượt → tự pause, không loop | Community / Entry Point | 1 video public ngắn (vd 6s) đang là active autoplay | 1. Để video autoplay tới khi hết 1 lượt.<br>2. Giữ card vẫn ở ≥50% visibility.<br>3. Quan sát sau khi video kết thúc. | 1. Video tự pause khi phát hết (KHÔNG loop lại).<br>2. Center play icon overlay hiện lại. | High | | | |
| CEP_045 | Hai video cùng ≥50% → chọn card visibility cao hơn làm active | Community / Entry Point | 2 video card cạnh nhau, trong lúc scroll cả 2 cùng đạt ≥50% | 1. Scroll chậm sao cho 2 video card cùng đạt ≥50% visibility đồng thời.<br>2. Quan sát card nào phát. | 1. Card có % visibility CAO HƠN được chọn active & phát.<br>2. Card còn lại giữ pause với play icon hiển thị. | Medium | | | Edge: tie-break theo % visibility |
| CEP_046 | Tap video card đang phát → mở fullscreen → back → card resume vị trí cũ | Community / Entry Point | 1 video public đang autoplay trong feed | 1. Để 1 video đang phát ở feed.<br>2. Tap để mở Video Player fullscreen.<br>3. Xem 1 đoạn rồi tap back.<br>4. Quan sát card ở feed. | 1. Vào fullscreen Video Player.<br>2. Back về feed: card resume từ vị trí đã xem trước đó. | Medium | | | |
| CEP_047 | Video card membership-only KHÔNG autoplay dù ở ≥50% (guest/non-member) | Community / Entry Point | Feed có video membership-only; viewer guest/non-member | 1. Scroll cho video membership-only đạt ≥50% visibility.<br>2. Quan sát. | 1. Card KHÔNG autoplay, giữ thumbnail blur + lock icon (autoplay chỉ áp dụng Public video). | Medium | | | Hành vi khi viewer LÀ active member — Pending clarification, see Q2 |

### 6. MID-SESSION STATE TRANSITION (EDGE CASE)

| ID | Test Case Description | Category | Pre-condition / Test Data | Test Step | Expected Result | Priority | Test Result Round 1 (dd/mm/yyyy) | Test Result on PROD env (dd/mm/yyyy) | Note |
|---|---|---|---|---|---|---|---|---|---|
| --- | **6. MID-SESSION STATE TRANSITION (EDGE CASE)** | --- | --- | --- | --- | --- | | | |
| CEP_048 | Post bị creator xóa giữa session — biến mất sau refresh, không thông báo | Community / Entry Point | 1 post đang hiển thị trong feed; creator xóa post đó ở Studio | 1. Mở tab Community, thấy post P.<br>2. Creator xóa post P (thao tác phía Studio).<br>3. User pull-to-refresh (hoặc paginate / re-access).<br>4. Quan sát feed. | 1. Trước refresh: không có thông báo chủ động.<br>2. Sau refresh: post P không còn xuất hiện trong feed.<br>3. Không crash, không card vỡ. | Medium | | | |
| CEP_049 | Tap post đã bị xóa trước khi refresh → route sang Post Detail xử lý | Community / Entry Point | Post P đã bị xóa nhưng feed chưa refresh | 1. Creator xóa post P.<br>2. Trước khi refresh, user tap card P. | 1. Route sang Post Detail; màn Post Detail xử lý case post đã xóa (PRD View Post Details). | Medium | | | AC chưa đầy đủ - cần bổ sung từ PRD View Post Details (case post no longer exists) |
| CEP_050 | Visibility đổi Public → Membership-only giữa session (non-member) | Community / Entry Point | Post hiển thị Public trong feed; viewer non-member; creator đổi sang Membership only | 1. User non-member thấy post P dạng public.<br>2. Creator đổi visibility P sang "Membership only".<br>3. User refresh.<br>4. Tap card P. | 1. Sau refresh: P re-render dạng membership card (blur + lock + badge) vì viewer là non-member.<br>2. Tap card khóa → trigger membership gate. | Medium | | | |
| CEP_051 | Visibility đổi Membership-only → Public giữa session | Community / Entry Point | Post hiển thị Member-only trong feed; creator đổi sang Public | 1. User thấy post P dạng member-only.<br>2. Creator đổi visibility P sang "Public".<br>3. User refresh.<br>4. Quan sát card P. | 1. Sau refresh: P re-render dạng public card.<br>2. Caption và media hiển thị bình thường (không blur, không lock). | Medium | | | |
| CEP_052 | Membership hết hạn giữa session — card cũ giữ nguyên tới refresh | Community / Entry Point | Active member đang xem member-only content; membership hết hạn khi feed đang mở | 1. Active member mở feed, xem được member-only card bình thường.<br>2. Membership hết hạn trong lúc feed đang mở (không thao tác refresh).<br>3. Quan sát card đã render.<br>4. Pull-to-refresh.<br>5. Quan sát lại. | 1. Card đã render tiếp tục hiển thị bình thường tới lần refresh.<br>2. Không bị force logout / crash.<br>3. Sau refresh: member-only card render dạng membership card (khóa). | Medium | | | Hành vi tap card mid-session trước refresh — Pending clarification, see Q4 (AC-15.1 bị cụt) |

### 7. GA4 TRACKING

| ID | Test Case Description | Category | Pre-condition / Test Data | Test Step | Expected Result | Priority | Test Result Round 1 (dd/mm/yyyy) | Test Result on PROD env (dd/mm/yyyy) | Note |
|---|---|---|---|---|---|---|---|---|---|
| --- | **7. GA4 TRACKING** | --- | --- | --- | --- | --- | | | |
| CEP_060 | Fire event view_artist_tab khi xem tab Community | Community / Entry Point | GA4 DebugView bật; user ở Artist Profile | 1. Mở GA4 DebugView (hoặc charles/proxy log).<br>2. Tap tab "Community".<br>3. Kiểm tra event log. | 1. Fire event `view_artist_tab` với param `tab_name = community` (đúng tên & value). | Medium | | | Cần tool: GA4 DebugView / proxy |
| CEP_061 | Fire event view_all_post khi tap View all section Community | Community / Entry Point | GA4 DebugView bật; user ở tab "Tất cả" | 1. Mở tab "Tất cả".<br>2. Tap CTA "View all"/"Xem thêm bài đăng" của section Community.<br>3. Kiểm tra event log. | 1. Fire event `view_all_post` với param `source = post_all_tab`. | Medium | | | Cần tool: GA4 DebugView |
| CEP_062 | Fire event view_all_video với source đúng theo điểm vào | Community / Entry Point | GA4 DebugView bật | 1. Tap CTA "Xem tất cả" Video từ tab "Tất cả" → kiểm tra log.<br>2. Tap CTA "Xem tất cả" Video từ Community feed → kiểm tra log. | 1. Từ tab "Tất cả": `view_all_video` source=`post_all_tab`.<br>2. Từ Community feed: `view_all_video` source=`feed_community_tab`. | Medium | | | Cần tool: GA4 DebugView. Verify source param mapping đúng từng entry |

### 8. MULTILINGUAL / RESPONSIVE / SECURITY / ACCESSIBILITY / PERFORMANCE (MERGED)

| ID | Test Case Description | Category | Pre-condition / Test Data | Test Step | Expected Result | Priority | Test Result Round 1 (dd/mm/yyyy) | Test Result on PROD env (dd/mm/yyyy) | Note |
|---|---|---|---|---|---|---|---|---|---|
| --- | **8. MULTILINGUAL / RESPONSIVE / SECURITY / A11Y / PERFORMANCE (MERGED)** | --- | --- | --- | --- | --- | | | |
| CEP_070 | Multilingual VI/EN — verify toàn bộ label/empty/error text (MERGED) | Community / Entry Point | App hỗ trợ đổi locale VI/EN.<br>**Bảng kiểm:**<br>• CTA Video: "Xem tất cả" / "View all"<br>• Tiêu đề All Video: "Video của {creator_name}" / "Videos by {creator_name}"<br>• Empty video: "Hiện không có video nào" / "No videos available"<br>• Empty feed: "Hiện chưa có bài đăng nào" / "No posts available"<br>• Badge membership: "Dành cho thành viên" / "Membership only"<br>• Caption suffix: "Xem thêm" / "View more"<br>• Nhãn edited: "Đã chỉnh sửa" / "Edited"<br>• Toast lỗi: "Không thể tải bài viết..." / "Could not load feed..." | 1. Set locale = VI, đi qua tất cả màn (section Video, All Video, feed, empty, error).<br>2. Verify từng label theo bảng kiểm cột VI.<br>3. Switch locale = EN.<br>4. Verify từng label theo bảng kiểm cột EN. | 1. Tất cả label hiển thị đúng ngôn ngữ tương ứng theo bảng kiểm, không lẫn ngôn ngữ, không thiếu key, không vỡ layout do độ dài text. | Medium | | | MERGE multilingual theo rule 7.5. Search có/không dấu không áp dụng (feature không có search) |
| CEP_071 | Responsive Mobile — flow chính trên iOS & Android (MERGED) | Community / Entry Point | Thiết bị/emulator: iOS 12, iOS latest, Android 12, Android latest | 1. Thực hiện flow chính (mở tab "Tất cả" → section Video → tab Community → scroll feed → autoplay → tap card) trên iOS 12.<br>2. Lặp lại trên iOS latest.<br>3. Lặp lại trên Android 12.<br>4. Lặp lại trên Android latest. | 1. Trên cả 4 cấu hình: layout không vỡ, carousel/lưới/feed hiển thị đúng, autoplay & navigation chạy được, không crash. | Medium | | | MERGE platform theo rule 7.6 (iOS ≥12, Android ≥12) |
| CEP_072 | Responsive Web Desktop — cross-browser (MERGED) | Community / Entry Point | Chrome, Safari, Firefox, Edge (latest) | 1. Thực hiện flow chính trên Chrome.<br>2. Lặp lại trên Safari.<br>3. Lặp lại trên Firefox.<br>4. Lặp lại trên Edge. | 1. Layout và flow đúng trên cả 4 browser (Chrome ưu tiên).<br>2. Public video xem được; membership-only áp dụng restrict riêng (xem CEP_073). | Medium | | | MERGE cross-browser theo rule 7.6, Chrome-first |
| CEP_073 | Security/Authorization — non-member không xem được membership content trên Web/mWeb & qua deeplink | Community / Entry Point | Video/post membership-only; viewer non-member; truy cập qua Web/mWeb và qua deeplink trực tiếp | 1. Trên Web/mWeb, mở feed có card membership-only.<br>2. Cố mở chi tiết membership-only qua URL/deeplink trực tiếp.<br>3. Quan sát. | 1. Card membership-only luôn ở trạng thái khóa (blur + lock) với non-member.<br>2. Truy cập trực tiếp không bypass được nội dung (không lộ media/caption thật). | High | | | Authorization-focused. XSS/SQLi không áp dụng — feature view-only, user không nhập input text. Chi tiết restrict ở PRD Video Player / User View |
| CEP_074 | Accessibility — screen reader + alt + reduce-motion (MERGED, generic) | Community / Entry Point | Bật VoiceOver (iOS)/TalkBack (Android); bật OS Reduce Motion | 1. Bật screen reader, swipe qua card public, card membership-only (locked), CTA, tab.<br>2. Kiểm tra label đọc được.<br>3. Bật OS Reduce Motion, mở feed có video.<br>4. Quan sát autoplay. | 1. Mọi element có accessibility label/alt mô tả được (locked card đọc rõ là nội dung dành cho thành viên, không đọc trống "image").<br>2. (Generic) Khi Reduce Motion bật, autoplay nên dừng/hiển thị thumbnail tĩnh — chờ PRD bổ sung. | Low | | | Generic - AC chưa có a11y spec, cần bổ sung từ PRD/Design. MERGE a11y theo rule 7.9 |
| CEP_075 | Performance — loading skeleton & xử lý mạng chậm | Community / Entry Point | Giả lập network chậm (throttle 3G); feed nhiều item | 1. Throttle mạng 3G, mở tab Community.<br>2. Quan sát trạng thái loading.<br>3. Scroll trigger pagination khi mạng chậm. | 1. Hiển thị skeleton/loading trong lúc tải, không màn trắng/đơ.<br>2. Pagination khi mạng chậm vẫn load đúng, không trùng/miss item, không crash. | Low | | | Generic - AC không có ngưỡng performance cụ thể. Cần bổ sung tiêu chí thời gian nếu PRD định nghĩa |


