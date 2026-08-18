# Reasoning Ctrl+F: Intelligent Document Navigation

> Reasoning-based semantic search that goes beyond keyword matching — describe what you mean, not what word you're looking for.

**Course:** Unstructured Data Analysis (Group 1)
**Team:** Hyeondong Lee, Jaejun Kim, **Jihong Min**, Uijae Ryu

📄 [Full Report (PDF)](./docs/UD_Term_Project_Report_Group1.pdf) · 📊 [Slides (PDF)](./docs/UD_Term_Project_Slides_Group1.pdf)

---

## Overview

Traditional tools like Ctrl+F rely on exact keyword matching, which fails whenever a user
remembers a *concept* but not the exact term (e.g. searching "zero-calorie powdered
sweetener" instead of "stevia"). This project builds a **reasoning-based retrieval system**
that combines LLM query expansion with dense sentence embeddings to search by meaning
rather than literal text overlap.

```
User Query → LLM Reasoning (Query/Answer) → Semantic Space (Doc/Corpus) → Top-K → Result
```

## My Role

- Data preprocessing pipeline (sentence tokenization, POS tagging, stopword/punctuation removal, WordNet lemmatization) and exploratory analysis of the geographic Wikipedia subset
- Contributed to the embedding/retrieval experiments comparing TF-IDF, MPNet, and SimCSE

<!-- 실제로 다른 파트(예: LLM reasoning 프롬프트 설계, Likert 평가 등)를 맡으셨다면 자유롭게 수정해주세요 -->

## Dataset

- **Source:** Wikimedia English Wikipedia dump (`wikimedia/wikipedia`, 2023-11-01), ~6.4M documents
- **Subset:** All country names + top 1,000 populated cities → 891 geographic articles → ~174K sentences
- **Preprocessing:** NLTK Punkt tokenization, POS tagging, stopword/punctuation removal, WordNet lemmatization

## Method

| Component | Choice |
|---|---|
| Sentence embedding | SimCSE (`sup-simcse-roberta-large`) |
| Reasoning LLM | GPT-5-nano (vs. Gemini, compared in ablation) |
| Query strategy | Query + LLM reasoning sentence (concatenated) |
| Retrieval | Top-K (k=5), cosine similarity |
| Evaluation | Cosine similarity + 1–5 Likert semantic-accuracy scale (AI-judged, human-verified) |

## Results

**Final configuration:** SimCSE + GPT-5-nano reasoning + Query+LLM input

| Metric | Score |
|---|---|
| Cosine similarity (avg) | 0.7926 |
| Likert — AI evaluation | 4.0 / 5 |
| Likert — Human verification | 3.92 / 5 |

### Ablation highlights
- **TF-IDF vs. dense embeddings:** TF-IDF failed on paraphrased/abstract queries (e.g. matched "Auckland" on surface tokens); SimCSE consistently outperformed MPNet (Likert 3.78 vs 3.12).
- **LLM-only vs. Query+LLM input:** combining the raw query with the LLM's reasoning sentence beat LLM-only reformulation across every metric (Likert 3.92 vs 3.4).
- **GPT vs. Gemini:** GPT produced more explicit relational cues and won on Likert score (4.0 vs 3.36); Gemini was competitive on straightforward factual queries.

## Repo Structure

```
├── docs/                # report + slides
├── notebooks/           # data construction, preprocessing, embedding, retrieval, evaluation
├── src/                 # (optional) reusable pipeline code
└── README.md
```

<!-- TODO: 실제 코드/노트북을 올린 뒤 위 구조를 실제 폴더 구성에 맞게 수정해주세요 -->

## Lessons Learned

- Cosine similarity alone is not a reliable proxy for semantic correctness — qualitative (Likert) evaluation was essential.
- Even a structured corpus like Wikipedia has messy entity naming ("ROK" / "Republic of Korea" / "South Korea").
- No single embedding model or LLM dominated universally; performance depended on query type and phrasing.

## Future Work

The Query+Reasoning approach is domain-agnostic and could extend to specialized domains
(legal, medical) where users often describe situations without knowing exact terminology.
