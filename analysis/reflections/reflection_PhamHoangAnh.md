# Individual Reflection — Lab 18

**Tên:** Phạm Hoàng Anh  
**Module phụ trách:** Toàn bộ Module (M1, M2, M3, M4, M5)

---

## 1. Đóng góp kỹ thuật

- Module đã implement: M1 Chunking, M2 Search, M3 Reranking, M4 Evaluation, M5 Enrichment
- Các hàm/class chính đã viết:
  - M1: `chunk_semantic`, `chunk_hierarchical`, `chunk_structure_aware`
  - M2: `segment_vietnamese`, `BM25Search`, `DenseSearch`, `reciprocal_rank_fusion`
  - M3: `CrossEncoderReranker.rerank`, `benchmark_reranker`
  - M4: `evaluate_ragas`, `failure_analysis`
  - M5: `enrich_chunks`, `_enrich_single_call`
- Số tests pass: 37/37 tests passed (100%)

## 2. Kiến thức học được

- Khái niệm mới nhất: Hiểu rõ cơ chế hoạt động của RRF (Reciprocal Rank Fusion) để kết hợp kết quả tìm kiếm ngữ nghĩa (Dense) và tìm kiếm từ khóa (BM25). Hiểu phương pháp Chunking phân cấp (Hierarchical Chunking - Parent/Child relationship) và cách Enrichment dữ liệu trước khi index để tăng Context Recall.
- Điều bất ngờ nhất: Reranking bằng CrossEncoder giúp tối ưu thứ tự chunk cực kỳ hiệu quả, giúp đưa thông tin chính xác nhất lên top đầu mà không làm tăng tải xử lý cho LLM ở bước Generator.
- Kết nối với bài giảng (slide nào): Slide Production RAG Pipeline, các slide về Hybrid Search & RRF, Reranking, RAGAS Evaluation metrics.

## 3. Khó khăn & Cách giải quyết

- Khó khăn lớn nhất: Lỗi không tương thích thư viện `qdrant-client` trên môi trường Python 3.14 (do chưa có pre-built wheels cho `grpcio` và `qdrant-client` hỗ trợ phiên bản Python mới này).
- Cách giải quyết: Sử dụng môi trường ảo Python 3.12 để đảm bảo tương thích hoàn toàn các gói thư viện từ PyPI, đồng thời cấu trúc mã nguồn có cơ chế Fallback thông minh giúp hệ thống vẫn chạy ổn định.
- Thời gian debug: Khoảng 30 phút.

## 4. Nếu làm lại

- Sẽ làm khác điều gì: Sử dụng cơ sở dữ liệu vector Qdrant chạy trên Docker cục bộ để lưu trữ và truy vấn Dense Search thay vì chỉ chạy chế độ Fallback ở local máy phát triển nếu môi trường mạng cho phép tải Docker Image nhanh hơn.
- Module nào muốn thử tiếp: M5 Enrichment (thử thêm nhiều prompt template nâng cao để trích xuất metadata phong phú hơn).

## 5. Tự đánh giá

| Tiêu chí | Tự chấm (1-5) |
|----------|---------------|
| Hiểu bài giảng | 5 |
| Code quality | 5 |
| Teamwork | 5 |
| Problem solving | 5 |
