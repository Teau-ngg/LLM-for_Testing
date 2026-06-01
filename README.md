# 🧪 LLM-for_Testing — Nghiên cứu ứng dụng LLM trong việc sinh Test Case tự động

> **Đề tài nghiên cứu:** So sánh hiệu quả sinh Test Case tự động bằng các mô hình LLM (Qwen2.5-3B vs Gemini 2.5 Pro) với việc viết Test Case thủ công bởi con người.
>
> **Đơn vị:** UIT — Trường Đại học Công nghệ Thông tin, ĐHQG-HCM

---

## 📑 Mục lục

- [Tổng quan dự án](#-tổng-quan-dự-án)
- [Kiến trúc hệ thống & Workflow](#-kiến-trúc-hệ-thống--workflow)
- [Cấu trúc thư mục](#-cấu-trúc-thư-mục)
- [Bước 1 — Chuẩn bị Rule & Prompt (RuleTestGen)](#-bước-1--chuẩn-bị-rule--prompt-ruletestgen)
- [Bước 2 — Sinh Test Case bằng Qwen2.5-3B trên Google Colab](#-bước-2--sinh-test-case-bằng-qwen25-3b-trên-google-colab)
- [Bước 3 — Sinh Test Case bằng Gemini 2.5 Pro](#-bước-3--sinh-test-case-bằng-gemini-25-pro)
- [Bước 4 — Convert Markdown → XLSX](#-bước-4--convert-markdown--xlsx)
- [Bước 5 — Đánh giá & So sánh kết quả (Evaluation Sheet)](#-bước-5--đánh-giá--so-sánh-kết-quả-evaluation-sheet)
- [Bước 6 — Phân tích thống kê (Statistical Analysis)](#-bước-6--phân-tích-thống-kê-statistical-analysis)
- [Kết luận & Đánh giá tổng quan](#-kết-luận--đánh-giá-tổng-quan)
- [Cài đặt & Yêu cầu](#-cài-đặt--yêu-cầu)
- [Hướng phát triển](#-hướng-phát-triển)

---

## 🎯 Tổng quan dự án

Dự án nghiên cứu khả năng ứng dụng **Large Language Models (LLMs)** vào quy trình kiểm thử phần mềm, cụ thể là việc **sinh Test Case tự động** từ User Story và Acceptance Criteria.

### Mục tiêu nghiên cứu

1. **Xây dựng bộ Rule chuẩn** để hướng dẫn LLM sinh Test Case chất lượng cao
2. **So sánh 2 mô hình LLM:**
   - 🤖 **Qwen2.5-3B** — mô hình open-source, chạy local/Google Colab
   - 🌐 **Gemini 2.5 Pro** — mô hình thương mại của Google
3. **Đánh giá hiệu quả** của LLM so với việc viết Test Case thủ công bởi QA Engineer
4. **Phân tích chi phí - lợi ích** khi áp dụng LLM vào quy trình Testing thực tế

### Tiêu chí đánh giá

| Tiêu chí | Mô tả |
|---|---|
| ⏱️ **Thời gian** | Thời gian sinh Test Case từ input đến output hoàn chỉnh |
| 🎯 **Test Coverage** | Độ phủ các loại test: Happy Path, Negative, Boundary, Edge Case, Security... |
| 📊 **Chất lượng** | Tính chính xác, khả năng thực thi (executable), tuân thủ rule |
| 💰 **Chi phí** | Chi phí vận hành (API cost, GPU cost) vs nhân công QA |
| 🔄 **Tính nhất quán** | Mức độ ổn định của output qua nhiều lần chạy |

---

## 🏗️ Kiến trúc hệ thống & Workflow

```
┌─────────────────────────────────────────────────────────────────────┐
│                        RESEARCH PIPELINE                            │
│                                                                     │
│  ┌──────────────┐                                                   │
│  │  RuleTestGen │ ── Rule, Prompt, Checklist ──┐                    │
│  │  (Bước 1)    │                              │                    │
│  └──────────────┘                              ▼                    │
│                                    ┌────────────────────┐           │
│        User Story                  │                    │           │
│            +          ────────────►│  Qwen2.5-3B        │           │
│   Acceptance Criteria              │  (Google Colab)    │           │
│                                    │  Bước 2            │           │
│                                    └────────┬───────────┘           │
│                                             │                       │
│                                             ▼                       │
│                                    ┌────────────────────┐           │
│        User Story                  │                    │           │
│            +          ────────────►│  Gemini 2.5 Pro    │           │
│   Acceptance Criteria              │  Bước 3            │           │
│     + Rule Prompt                  │                    │           │
│                                    └────────┬───────────┘           │
│                                             │                       │
│                                             ▼                       │
│                                    ┌────────────────────┐           │
│                                    │ convert_md_to_xlsx │           │
│                                    │  (Bước 4)          │           │
│                                    └────────┬───────────┘           │
│                                             │                       │
│                                             ▼                       │
│                                    ┌────────────────────┐           │
│                                    │ Evaluation Sheet   │           │
│                                    │  (Bước 5)          │           │
│                                    └────────┬───────────┘           │
│                                             │                       │
│                                             ▼                       │
│                                    ┌────────────────────┐           │
│                                    │ Statistical        │           │
│                                    │ Analysis (Bước 6)  │           │
│                                    └────────┬───────────┘           │
│                                             │                       │
│                                             ▼                       │
│                                    ┌────────────────────┐           │
│                                    │ Kết luận &         │           │
│                                    │ Đánh giá           │           │
│                                    └────────────────────┘           │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 📂 Cấu trúc thư mục

```
LLM-for_Testing/
│
├── 📄 README.md                          # Tài liệu hướng dẫn (file này)
│
├── 📁 RuleTestGen/                       # Bước 1: Bộ Rule & Prompt
│   ├── TestCaseRule.md                   # Rule viết Test Case (single source of truth)
│   ├── Promtp.md                         # Prompt template cho LLM
│   ├── ReviewChecklist.md                # Checklist review Test Case
│   └── AcceptanceChecklist.md            # Checklist nghiệm thu Test Case
│
├── 📁 Train_LLM/                         # Bước 2 & 6: Notebook chạy model & phân tích
│   ├── LLM_TestCase_Generator.ipynb      # Notebook Google Colab - load & chạy Qwen2.5-3B
│   └── Statistical_Analysis.ipynb        # Notebook phân tích thống kê kết quả
│
├── 🐍 convert_md_to_xlsx.py             # Bước 4: Script convert Markdown → XLSX
│
└── 📊 LLM_Evaluation_Sheet.xlsx         # Bước 5: Bảng đánh giá & so sánh kết quả
```

---

## 📘 Bước 1 — Chuẩn bị Rule & Prompt (`RuleTestGen`)

> **Mục đích:** Xây dựng bộ tiêu chuẩn (Rule) và Prompt template để đảm bảo cả 2 model LLM đều sinh Test Case theo cùng một chuẩn, phục vụ việc so sánh công bằng.

### Các file trong `RuleTestGen/`

| File | Vai trò | Mô tả |
|---|---|---|
| `TestCaseRule.md` | 📏 **Rule gốc** | Bộ 12 rule viết Test Case: template 10 column, cách đặt ID, viết Test Step (Traditional format), gán Priority, 12 loại coverage bắt buộc, rule domain-specific |
| `Promtp.md` | 💬 **Prompt template** | 7 prompt template cho các vai trò: Writer, Reviewer, Acceptor, Master pipeline, Quick Writer/Reviewer/Acceptor |
| `ReviewChecklist.md` | 🔍 **Review checklist** | 6 dimensions review: Compliance, Coverage Adequacy, Logic Correctness, Quality of Writing, Duplicate Detection, Cross-check |
| `AcceptanceChecklist.md` | ✅ **Acceptance checklist** | 4 focus areas nghiệm thu: Business Validation, Cross-feature Duplicate, Suite Assignment, Release Readiness. Scoring system 10 điểm |

### Cách sử dụng

Bộ Rule này được dùng làm **input context** cho cả 2 model LLM:

1. **Với Qwen2.5-3B (Colab):** Nội dung các file rule được nhúng trực tiếp vào prompt khi gọi model
2. **Với Gemini 2.5 Pro:** Các file rule được upload làm context, kết hợp với prompt template từ `Promtp.md`

---

## 🤖 Bước 2 — Sinh Test Case bằng Qwen2.5-3B trên Google Colab

> **Mục đích:** Sử dụng model open-source Qwen2.5-3B chạy trên Google Colab (free GPU) để sinh Test Case từ User Story + Acceptance Criteria.

### Notebook: `Train_LLM/LLM_TestCase_Generator.ipynb`

### Workflow

```
1. Mở notebook trên Google Colab (GPU runtime: T4 / L4)
         │
         ▼
2. Cài đặt dependencies (transformers, torch, accelerate, ...)
         │
         ▼
3. Load model Qwen2.5-3B từ HuggingFace
         │
         ▼
4. Chuẩn bị input:
   - Rule từ RuleTestGen/TestCaseRule.md
   - Prompt template từ RuleTestGen/Promtp.md
   - User Story + Acceptance Criteria của feature cần test
         │
         ▼
5. Gọi model generate Test Case
         │
         ▼
6. Export kết quả ra file .md tại local
```

### Cách chạy

1. **Upload notebook** `LLM_TestCase_Generator.ipynb` lên Google Colab
2. **Chọn runtime:** `Runtime → Change runtime type → T4 GPU`
3. **Chạy tuần tự các cell:**
   - Cell 1-2: Cài đặt thư viện & load model Qwen2.5-3B
   - Cell 3: Nhập User Story + Acceptance Criteria
   - Cell 4: Chạy sinh Test Case
   - Cell 5: Export kết quả ra file `.md`
4. **Download file kết quả** về local

### Lưu ý

- Qwen2.5-3B là model **3 tỷ tham số**, phù hợp chạy trên Colab free tier (T4 GPU)
- Thời gian sinh Test Case phụ thuộc vào độ dài input và số lượng TC cần sinh
- Kết quả output ở dạng **Markdown table** tuân theo format 10 column của `TestCaseRule.md`

---

## 🌐 Bước 3 — Sinh Test Case bằng Gemini 2.5 Pro

> **Mục đích:** Sử dụng cùng bộ Rule + Prompt + User Story + Acceptance Criteria đã dùng ở Bước 2, nhưng chạy trên Gemini 2.5 Pro để so sánh kết quả.

### Workflow

```
1. Truy cập Gemini 2.5 Pro (Google AI Studio / API)
         │
         ▼
2. Upload context:
   - Các file rule từ RuleTestGen/ (TestCaseRule.md, Promtp.md, ...)
         │
         ▼
3. Nhập CÙNG User Story + Acceptance Criteria đã dùng ở Bước 2
         │
         ▼
4. Sử dụng Prompt template từ Promtp.md (WRITER prompt)
         │
         ▼
5. Gemini sinh Test Case
         │
         ▼
6. Copy output → lưu file .md tại local
```

### Điểm khác biệt so với Qwen2.5-3B

| Tiêu chí | Qwen2.5-3B | Gemini 2.5 Pro |
|---|---|---|
| **Loại model** | Open-source | Commercial (Google) |
| **Kích thước** | 3B params | Không công bố (ước tính lớn hơn nhiều) |
| **Chạy trên** | Google Colab (tự host) | Google AI Studio / API |
| **Chi phí** | Free (Colab GPU) | Theo API pricing hoặc free tier giới hạn |
| **Context window** | ~8K tokens | ~1M tokens |
| **Tốc độ** | Chậm hơn (phụ thuộc GPU) | Nhanh hơn (server Google) |

### Lưu ý quan trọng

⚠️ **Đảm bảo tính công bằng:** Phải sử dụng **CÙNG input** (User Story, AC, Rule) cho cả 2 model để kết quả so sánh có giá trị.

---

## 🔄 Bước 4 — Convert Markdown → XLSX

> **Mục đích:** Khi các model LLM sinh Test Case ở dạng file `.md` (Markdown), cần convert sang file `.xlsx` (Excel/Sheets) để thuận tiện cho việc đánh giá, so sánh, và import vào hệ thống quản lý test. Điều này **tối ưu hoá chi phí** vì không cần copy-paste thủ công từng cell.

### Script: `convert_md_to_xlsx.py`

### Cài đặt

```bash
pip3 install openpyxl
```

### Cách sử dụng

```bash
# Cú pháp cơ bản
python3 convert_md_to_xlsx.py input.md

# Chỉ định tên output
python3 convert_md_to_xlsx.py input.md output.xlsx
```

### Tính năng nổi bật

| Tính năng | Mô tả |
|---|---|
| 🎨 **Auto styling** | Header xanh đậm, section xanh nhạt, Priority color coding (High=đỏ, Medium=vàng, Low=xanh) |
| 📐 **10 column template** | Tự động map đúng 10 column của Ticketbox template |
| 🧠 **Smart parsing** | Tự skip Coverage Summary, Questions to PO/BA — chỉ convert bảng Test Case |
| 🔄 **Auto shift columns** | Nếu input có 8 columns (thiếu 2 cột Test Result) → tự shift cột Note về đúng vị trí |
| 📑 **Nested table support** | Xử lý bảng mapping lồng trong cell (vd: bảng VI/EN trong Test Data) |
| ✨ **HTML decode** | Convert `<br>` → newline, decode `&lt;` `&gt;` `&amp;` về ký tự gốc |
| 📦 **Multi-table concat** | Nhiều bảng Test Case trong 1 file MD → concat vào 1 sheet XLSX |

### Ví dụ sử dụng trong nghiên cứu

```bash
# Convert kết quả từ Qwen2.5-3B
python3 convert_md_to_xlsx.py qwen_testcases.md qwen_output.xlsx

# Convert kết quả từ Gemini 2.5 Pro
python3 convert_md_to_xlsx.py gemini_testcases.md gemini_output.xlsx
```

---

## 📊 Bước 5 — Đánh giá & So sánh kết quả (Evaluation Sheet)

> **Mục đích:** Ghi nhận và so sánh kết quả sinh Test Case từ 2 model LLM (Qwen2.5-3B vs Gemini 2.5 Pro) theo các tiêu chí đánh giá thống nhất.

### File: `LLM_Evaluation_Sheet.xlsx`

### Nội dung đánh giá

Bảng Evaluation Sheet chứa các thông tin so sánh:

| Hạng mục đánh giá | Mô tả |
|---|---|
| **Số lượng TC sinh ra** | Tổng số Test Case mỗi model sinh cho cùng 1 feature |
| **Coverage breakdown** | Phân bổ theo 12 loại coverage (Happy Path, Negative, Boundary, Edge Case, ...) |
| **Tỷ lệ trọng số** | % TC logic/boundary/validation vs UI/multilingual/responsive |
| **Chất lượng TC** | Đánh giá theo ReviewChecklist: Compliance, Coverage, Logic, Quality, Duplicate |
| **Điểm nghiệm thu** | Scoring theo AcceptanceChecklist (thang 10 điểm) |
| **Thời gian sinh** | Thời gian từ lúc input đến output hoàn chỉnh |
| **Chi phí** | GPU cost (Colab) vs API cost (Gemini) |
| **Tính nhất quán** | Kết quả có ổn định qua nhiều lần chạy không |

### Cách điền kết quả

1. **Chạy Bước 2** → ghi nhận kết quả Qwen2.5-3B vào sheet
2. **Chạy Bước 3** → ghi nhận kết quả Gemini 2.5 Pro vào sheet
3. **So sánh song song** các tiêu chí giữa 2 model
4. *(Tuỳ chọn)* Bổ sung thêm kết quả viết thủ công bởi QA Engineer để đối chiếu 3 chiều

---

## 📈 Bước 6 — Phân tích thống kê (Statistical Analysis)

> **Mục đích:** Sử dụng các phương pháp thống kê để phân tích dữ liệu từ Evaluation Sheet, rút ra kết luận có cơ sở khoa học.

### Notebook: `Train_LLM/Statistical_Analysis.ipynb`

### Các phân tích thực hiện

| Phân tích | Phương pháp | Mô tả |
|---|---|---|
| **So sánh số lượng TC** | Descriptive statistics | Thống kê mô tả: mean, median, std |
| **So sánh chất lượng** | Scoring comparison | So sánh điểm số theo từng dimension |
| **Coverage analysis** | Distribution analysis | Phân bổ % coverage theo từng loại test |
| **Thời gian sinh** | Time comparison | So sánh thời gian sinh TC giữa các phương pháp |
| **Chi phí - hiệu quả** | Cost-benefit analysis | ROI khi áp dụng LLM vs nhân công QA |
| **Tính nhất quán** | Variance analysis | Đánh giá mức độ ổn định output |

### Cách chạy

1. **Mở notebook** `Statistical_Analysis.ipynb` trên Jupyter / Google Colab
2. **Import dữ liệu** từ `LLM_Evaluation_Sheet.xlsx`
3. **Chạy các cell phân tích** theo thứ tự
4. **Xem kết quả** visualisation (biểu đồ, bảng so sánh)

---

## 🏁 Kết luận & Đánh giá tổng quan

> **Mục đích:** Tổng hợp kết quả nghiên cứu, so sánh toàn diện giữa LLM và con người trong việc viết Test Case.

### Các chiều so sánh chính

```
┌────────────────────┬──────────────┬──────────────┬──────────────┐
│    Tiêu chí        │ Qwen2.5-3B   │ Gemini Pro   │ QA Engineer  │
├────────────────────┼──────────────┼──────────────┼──────────────┤
│ ⏱️ Thời gian        │ Vài phút     │ Vài giây     │ Vài giờ      │
│ 🎯 Test Coverage   │ Cao (auto)   │ Rất cao      │ Phụ thuộc KN │
│ 📊 Chất lượng      │ Trung bình   │ Cao          │ Cao          │
│ 💰 Chi phí         │ Thấp (free)  │ Trung bình   │ Cao          │
│ 🔄 Nhất quán       │ Trung bình   │ Cao          │ Thấp         │
│ 🧠 Hiểu context    │ Hạn chế      │ Tốt          │ Rất tốt      │
│ 🔧 Tuỳ chỉnh      │ Cần rule rõ  │ Cần rule rõ  │ Linh hoạt    │
│ 📈 Scalability     │ Rất cao      │ Rất cao      │ Thấp         │
└────────────────────┴──────────────┴──────────────┴──────────────┘
```

> **Lưu ý:** Bảng trên là khung đánh giá tham khảo. Kết quả thực tế sẽ được cập nhật sau khi hoàn thành phân tích thống kê ở Bước 6.

### Câu hỏi nghiên cứu cần trả lời

1. **LLM có thể thay thế hoàn toàn QA Engineer trong việc viết Test Case không?**
2. **Model nào (Qwen2.5-3B vs Gemini 2.5 Pro) phù hợp hơn cho tác vụ sinh Test Case?**
3. **Chi phí đầu tư vào LLM có tiết kiệm hơn so với nhân công QA không?**
4. **Bộ Rule có ảnh hưởng đáng kể đến chất lượng output của LLM không?**
5. **Workflow nào là tối ưu: LLM sinh + con người review, hay con người viết + LLM review?**

---

## ⚙️ Cài đặt & Yêu cầu

### Yêu cầu hệ thống

| Thành phần | Yêu cầu |
|---|---|
| **Python** | >= 3.8 |
| **Google Colab** | Tài khoản Google (cho Qwen2.5-3B) |
| **Gemini 2.5 Pro** | Tài khoản Google AI Studio hoặc API key |

### Cài đặt nhanh

```bash
# 1. Clone repository
git clone https://github.com/Teau-ngg/LLM-for_Testing.git
cd LLM-for_Testing

# 2. Cài đặt dependencies cho convert script
pip3 install openpyxl

# 3. (Optional) Tạo alias cho script convert
echo 'alias tc2xlsx="python3 $(pwd)/convert_md_to_xlsx.py"' >> ~/.zshrc
source ~/.zshrc
```

### Dependencies cho Google Colab (tự cài trong notebook)

```bash
pip install transformers torch accelerate
```

---

## 🚀 Hướng phát triển

- [ ] **Fine-tuning Qwen2.5-3B** trên bộ dữ liệu Test Case thực tế của Ticketbox
- [ ] **Mở rộng so sánh** thêm các model khác: GPT-4o, Claude Sonnet, Llama 3, ...
- [ ] **Automation pipeline:** tích hợp LLM vào CI/CD để tự sinh TC khi có User Story mới
- [ ] **Multi-sheet output:** mỗi section 1 sheet riêng trong file XLSX
- [ ] **Google Sheets API:** push trực tiếp kết quả lên Google Sheets thay vì upload thủ công
- [ ] **Batch mode:** convert nhiều file MD cùng lúc
- [ ] **RAG (Retrieval-Augmented Generation):** kết hợp LLM với database TC cũ để sinh TC nhất quán hơn

---

## 📎 Tham khảo

- [Qwen2.5-3B — HuggingFace](https://huggingface.co/Qwen/Qwen2.5-3B)
- [Gemini 2.5 Pro — Google AI](https://ai.google.dev/)
- [openpyxl Documentation](https://openpyxl.readthedocs.io/)

---

*Nghiên cứu được thực hiện tại UIT — ĐHQG-HCM. Mọi đóng góp và feedback xin gửi qua Issues trên GitHub.*
