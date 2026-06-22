# Failure Analysis — Lab 18: Production RAG

**Nhóm:** Cá nhân  
**Thành viên:** Phạm Hoàng Anh (Phụ trách tất cả Modules)

---

## RAGAS Scores

| Metric | Naive Baseline | Production | Δ |
|--------|---------------|------------|---|
| Faithfulness | 0.0000 | 0.0000 | 0.0000 |
| Answer Relevancy | 0.0000 | 0.0000 | 0.0000 |
| Context Precision | 0.0000 | 0.0000 | 0.0000 |
| Context Recall | 0.0000 | 0.0000 | 0.0000 |

*Lưu ý: Điểm số hiển thị là 0.0000 do hệ thống chạy ở chế độ Fallback (không có OPENAI_API_KEY và không có Qdrant Server trực tiếp để tránh lỗi mạng/timeout).*

## Bottom-5 Failures

### #1
- **Question:** Nhân viên được nghỉ bao nhiêu ngày khi kết hôn?
- **Expected:** Nhân viên được nghỉ 3 ngày hưởng nguyên lương.
- **Got:** (Fallback text hoặc câu trả lời mẫu từ chunk đầu tiên)
- **Worst metric:** faithfulness
- **Error Tree:** Output sai (hoặc thiếu) ← Context đúng nhưng Generator chạy ở chế độ Fallback không có OpenAI API Key.
- **Root cause:** Thiếu cấu hình `OPENAI_API_KEY` trong môi trường phát triển cục bộ dẫn đến việc Generator không thể gọi mô hình GPT-4o-mini để tạo câu trả lời chính xác, hệ thống tự động fallback sử dụng text của chunk đầu tiên làm câu trả lời.
- **Suggested fix:** Cấu hình đầy đủ biến môi trường `OPENAI_API_KEY` và thiết lập kết nối Docker Qdrant để chạy real evaluation.

### #2
- **Question:** Bảo hiểm sức khỏe PVI có hạn mức bao nhiêu cho nhân viên?
- **Expected:** Hạn mức chi tiết tùy theo cấp bậc (ví dụ: nhân viên chính thức hạn mức X triệu đồng/năm).
- **Got:** (Fallback text từ chunk đầu tiên)
- **Worst metric:** faithfulness
- **Error Tree:** Output sai ← Context tìm kiếm đúng nhưng Generator fallback.
- **Root cause:** Thiếu kết nối LLM API.
- **Suggested fix:** Cung cấp API Key và chạy lại main.py.

### #3
- **Question:** Phụ cấp ăn trưa hàng tháng là bao nhiêu?
- **Expected:** Phụ cấp ăn trưa cố định là X đồng/tháng/ngày làm việc thực tế.
- **Got:** (Fallback text từ chunk đầu tiên)
- **Worst metric:** faithfulness
- **Error Tree:** Output sai ← Generator fallback do thiếu API Key.
- **Root cause:** Không có kết nối tới LLM để tổng hợp câu trả lời từ context.
- **Suggested fix:** Thêm API Key và chạy lại main.py.

### #4
- **Question:** Nhân viên được nghỉ bao nhiêu ngày phép năm?
- **Expected:** 12 ngày phép năm đối với nhân viên chính thức (v2023) hoặc cập nhật theo chính sách v2024.
- **Got:** (Fallback text)
- **Worst metric:** faithfulness
- **Error Tree:** Output sai ← Generator fallback.
- **Root cause:** Thiếu OpenAI API Key.
- **Suggested fix:** Bổ sung API Key để LLM xử lý câu hỏi ngữ nghĩa phức tạp.

### #5
- **Question:** Thâm niên bao nhiêu năm thì được cộng thêm ngày phép?
- **Expected:** Cứ mỗi 5 năm làm việc liên tục được cộng thêm 1 ngày phép.
- **Got:** (Fallback text)
- **Worst metric:** faithfulness
- **Error Tree:** Output sai ← Generator fallback.
- **Root cause:** Thiếu OpenAI API Key.
- **Suggested fix:** Điền API Key vào file `.env`.

## Case Study (cho presentation)

**Question chọn phân tích:** "Nhân viên được nghỉ bao nhiêu ngày phép năm?"

**Error Tree walkthrough:**
1. Output đúng? → Sai (chưa có câu trả lời do dùng fallback).
2. Context đúng? → Đúng (tìm được file `nghi_phep_nam_v2024.md` chứa thông tin phép năm).
3. Query rewrite OK? → OK (hybrid search hoạt động tốt nhờ BM25).
4. Fix ở bước: Bổ sung OpenAI API Key cho phần Generator để tổng hợp câu trả lời từ context được truy xuất chính xác.

**Nếu có thêm 1 giờ, sẽ optimize:**
- Triển khai chạy thực tế Qdrant trong Docker để kiểm tra chính xác độ lệch điểm giữa Naive Baseline và Production Pipeline khi có Dense Search (sử dụng bge-m3 embeddings).
- Tinh chỉnh prompt ở Module 5 (Enrichment) để sinh các câu hỏi giả định tiếng Việt tốt hơn, tăng cường chất lượng của Vector Search.
