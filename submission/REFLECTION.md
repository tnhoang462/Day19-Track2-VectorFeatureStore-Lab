# Reflection — Lab 19

**Tên:** Trần Nhật Hoàng
**Cohort:** 2A202600431
**Path đã chạy:** lite

---

## Câu hỏi (≤ 200 chữ)

> Trên golden set 50 queries, mode nào thắng ở loại query nào (`exact` /
> `paraphrase` / `mixed`), và tại sao? Khi nào bạn **không** dùng hybrid
> (i.e. khi nào pure BM25 hoặc pure vector là lựa chọn đúng)?

Kết quả Precision@10 trên 50 golden queries:

| type       | n  | kw    | sem   | hyb    |
|------------|----|-------|-------|--------|
| exact      | 15 | 96.7% | 88.7% | 96.7%  |
| paraphrase | 15 | 33.3% | 24.0% | 32.0%  |
| mixed      | 20 | 97.0% | 98.5% | 100.0% |
| **avg**    | 50 | 77.8% | 73.2% | **78.6%** |

`exact` queries chứa từ kỹ thuật verbatim → BM25 thắng, hybrid hoà vì keyword signal đã đủ mạnh. `paraphrase` dùng từ tiếng Việt không có trong corpus → cả ba mode đều yếu do `bge-small-en` train trên English (đổi `bge-m3` sẽ giúp semantic vượt). `mixed` kết hợp exact + paraphrase → hybrid thắng rõ (100% vs 97-98%) — pattern production-relevant nhất.

**Khi không dùng hybrid:** (1) exact-term lookup ngắn → BM25 đủ, hybrid chỉ tăng latency (10ms vs 1ms P99). (2) Corpus paraphrase nặng + embedding multilingual mạnh → vector-only thắng và đỡ chi phí BM25 index. (3) Autocomplete <1ms budget → BM25 hoặc prefix trie. Hybrid là default tốt khi không biết phân bố query, không phải mọi case.

---

## Điều ngạc nhiên nhất khi làm lab này

RRF k=60 trông arbitrary nhưng thực tế là sweet spot industry-standard — score `1/(60+rank)` đủ smooth để rank thứ 1 không "đè bẹp" rank 5-10, nhưng vẫn đủ dốc để top results có signal. Đổi k=10 (nghiêng về top) hay k=200 (gần uniform) đều giảm precision. Hybrid P99 server-side chỉ 23ms (rubric < 50ms) — fastembed CPU + Qdrant in-memory vẫn rất nhanh, không cần GPU cho lab scale 1000 docs.

---

## Bonus challenge

- [ ] Đã làm bonus (xem `bonus/`)
- [ ] Pair work với: _<tên đồng đội nếu có>_
