# DOCS_SIMILARITY

**Document Similarity Analyzer** — a DSA-3 project built around a genuine Suffix Array + Kasai's LCP Algorithm implementation.

---

## 1. Abstract

DOCS_SIMILARITY compares multiple documents — **TXT, PDF, and DOCX** — and identifies
duplicate or highly similar content between them. Instead of relying on off-the-shelf
similarity libraries (cosine similarity, TF-IDF, Levenshtein distance, etc.), the core
engine is a hand-built **Suffix Array**, constructed with the prefix-doubling technique,
paired with **Kasai's Algorithm** for O(n) **LCP (Longest Common Prefix) Array**
construction. A lightweight extraction layer (Apache PDFBox for PDF, Apache POI for DOCX)
converts every supported format into plain text first; the DSA engine itself is completely
format-agnostic and unchanged by which file type a document arrived in.

Documents are compared at the **word level**: text is normalized, split into words, and each
word is mapped to an integer token ID. The Suffix Array and Kasai's LCP Array are built over
that integer word sequence, so LCP values count shared **words** and the dashboard reports
meaningful common word sequences such as `machine learning algorithms`, never character
fragments such as `nvironment`.

## 2. Objectives

- Implement a genuine, from-scratch Suffix Array construction algorithm (no libraries).
- Implement Kasai's Algorithm to build the LCP Array in linear time.
- Use the Suffix Array / LCP Array over word token IDs to detect common word sequences **across** documents,
  without false matches crossing document boundaries.
- Accept documents in multiple real-world formats (TXT, PDF, DOCX) by extracting plain
  text before analysis, without altering the DSA engine itself.
- Derive a mathematically defined, defensible similarity score from that shared content.
- Present overlapping suffix-derived matches as clean, distinct shared regions rather than
  dozens of redundant, nested fragments.
- Rank all document pairs by similarity.
- Present the results — including the underlying DSA structures — in a clear web dashboard.

## 3. Features

- Upload 2+ documents in **any mix of TXT, PDF, and DOCX** through a browser dashboard.
- Client- and server-side validation of unsupported, empty, and corrupt files, with a
  specific, user-facing error for scanned/image-only PDFs that contain no extractable text.
- Real-time-feeling analysis progress checklist (stages reported by the backend), including
  a dedicated text-extraction stage.
- Summary cards: documents analyzed, pairs compared, common word sequences, highest similarity.
- A documents table showing each file's original name, detected format, extracted length,
  and extraction status (length in words).
- Ranked similarity table with a visual similarity bar per pair.
- Pair detail view showing the common word sequences — merged into distinct maximal
  sequences, not raw overlapping suffix matches — with word count and word offsets.
- Algorithm pipeline diagram.
- "Algorithm Details" section exposing the real Suffix Array / LCP technical data
  (paginated), plus pipeline status flags.
- No fake data anywhere — every number in the UI comes from the Java backend's response.

## 4. Architecture

```
Browser (React)  --REST/JSON-->  Spring Boot API  --extracts via-->  PDFBox / POI
                                          |
                                          v
                                  Suffix Array / Kasai LCP engine (plain text only)
```

The DSA algorithms live in plain, framework-free Java classes (`com.docs_similarity.dsa`),
completely decoupled from the web layer, so they can be read, tested, and understood on
their own. The extraction layer (`com.docs_similarity.extraction`) is equally decoupled:
it knows about file formats so the DSA layer never has to. By the time
`CommonWordSequenceAnalyzer` runs, every document is just a `Document` object holding plain
text — it has no idea whether that text came from a `.txt`, a `.pdf`, or a `.docx` file.

## 5. Technology Stack

**Frontend:** React 18 + Vite, plain CSS (no UI framework)
**Backend:** Java 17+, Spring Boot 3 (Spring Web, Spring Validation)
**Document extraction:** Apache PDFBox 2.0.31 (PDF), Apache POI 5.2.5 / poi-ooxml (DOCX)
**Build tools:** Maven (backend, with wrapper), npm (frontend)

## 6. DSA Algorithms

### 6.1 Word Tokenization and Token IDs (`util/WordTokenizer.java`, `util/TokenCombiner.java`)

```
TXT/PDF/DOCX -> text extraction -> normalization -> words -> token IDs
  -> integer token sequence with unique document sentinels -> Suffix Array -> Kasai LCP
  -> common word sequences -> overlap merging -> similarity -> ranking
```

Tokenization is deterministic: Unicode NFKC normalization, lower-casing (`Locale.ROOT`),
apostrophes inside words dropped (`don't` -> `dont`), and a word is a maximal run of
letters/digits. All other characters (whitespace, punctuation, hyphens, quotes) separate
words. Each distinct word gets an integer ID in order of first appearance; the reverse
mapping (ID -> word) is kept so matched ID runs can be shown as readable words.

Each document is followed by a **unique negative sentinel token** `-(docId + 1)`. It can
never equal a word ID and no two documents share one, so a common sequence can never run
from the end of one document into the next.

### 6.2 Suffix Array (`dsa/SuffixArray.java`)

Built over the integer token sequence with **prefix doubling** (Manber–Myers style): in
round *k*, suffixes are ranked by their first 2^k **words**, reusing the previous round's
ranks so each suffix is a pair of integers, sorted with a two-pass counting (radix) sort.
This gives **O(n log n)** construction where n is the number of words, not characters.

### 6.3 LCP Array + Kasai's Algorithm (`dsa/KasaiLCP.java`)

Given the Suffix Array, Kasai's Algorithm computes `LCP[i]` — the number of **words** the
i-th and (i-1)-th suffixes in sorted order have in common at their start — for every i in a
single **O(n)** pass, using the fact that walking the sequence in original order the LCP can
drop by at most 1 between consecutive positions.

### 6.3b Common Word Sequence Analysis (`dsa/CommonWordSequenceAnalyzer.java`)

For a suffix s and another document Y, the longest word sequence starting at s that also
occurs in Y is the largest LCP between s and any suffix of Y. In sorted order that is
achieved by the **nearest** Y-suffix above or below s, and the LCP between two sorted
positions is the minimum of the LCP array between them. One forward and one backward sweep
over the Suffix Array per document gives every suffix its longest match in every other
document, in O(n × documents). This also finds matches between documents A and C when a
suffix of B sorts between them (e.g. three copies of the same paragraph). Matches of at
least `MIN_SEQUENCE_WORDS` = 3 words are kept, and consecutive suffixes on the same
alignment are joined into runs while scanning.

### 6.4 Document Extraction (`extraction/`)

Before any DSA code runs, each uploaded file is converted to plain text by
`DocumentTextExtractor`, which dispatches by file extension to one of three extractors:

| Format | Extractor | Library |
|--------|-----------|---------|
| `.txt` | `TxtTextExtractor` | none — UTF-8 decode |
| `.pdf` | `PdfTextExtractor` | Apache PDFBox (`PDFTextStripper`) |
| `.docx` | `DocxTextExtractor` | Apache POI (`XWPFDocument` + `XWPFWordExtractor`) |

This layer is where every format-specific edge case is resolved, **before** anything
reaches the Suffix Array:

- An unsupported extension (`.xlsx`, `.pptx`, legacy `.doc`, etc.) is rejected immediately
  with a message naming the file and listing the supported formats.
- A corrupt or unreadable file (bad ZIP structure, malformed PDF, etc.) is caught and
  reported with a clear, file-specific error rather than propagating a raw stack trace.
- A password-protected PDF is detected and rejected with a specific message.
- A **scanned/image-only PDF** — one PDFBox can open but that has no embedded text
  layer — is explicitly detected (the extracted text is blank) and rejected with a message
  stating that OCR is not supported in this version, rather than silently "succeeding" with
  an empty, meaningless document.

OCR, `.pptx`, `.xlsx`, and legacy binary `.doc` are intentionally out of scope for this
version — files in those formats are rejected the same way any other unsupported type is.

Once extraction succeeds, the resulting plain text is handed to the exact same
`preprocessText()` step, tokenizer and `CommonWordSequenceAnalyzer` that process `.txt` uploads — the
Suffix Array, Kasai's Algorithm, and the LCP Array were not modified to add multi-format
support.

### 6.5 Overlap Merging (`service/CommonSequenceMerger.java`)

A shared passage of k words is found once for (almost) every word in it, because each of its
suffixes finds its partner: `machine learning algorithms`, `learning algorithms`, ... These
must be neither displayed separately nor counted repeatedly.

1. **Diagonal merging.** Matches with the same alignment (`offsetInA - offsetInB`) whose
   word intervals overlap or touch are merged into one maximal sequence. Its words are
   rebuilt from token IDs via the reverse mapping.
2. **Contained variations dropped.** A sequence whose word interval in A (or in B) lies
   inside a longer kept sequence is a fragment of it (typically a repeated phrase matched at
   another alignment) and is not shown separately.

Each displayed sequence shows the word sequence, its word count, and its word offset in
document A and in document B.

### 6.6 Similarity Formula (`service/DocumentSimilarityService.java`)

For a document pair (A, B), after merging:

```
sharedWordsInA = words of A inside at least one merged common sequence with B
                 (union of intervals: a word covered by several matches counts once)
sharedWordsInB = the same, within B
sharedWords    = min(sharedWordsInA, sharedWordsInB)

Similarity(A, B) = (2 × sharedWords / (wordCountA + wordCountB)) × 100
```

Raw LCP values are never summed. Taking the smaller side means a phrase that occurs once in
A and three times in B contributes its words once. Identical documents score 100%; documents
without a common sequence of at least 3 words score 0%. This value drives the ranking
(ties are broken by shared words, then by document order, so the ranking is deterministic).

### 6.7 Document Boundaries

Token positions are mapped back to `(documentId, wordOffsetWithinDocument)` via a lookup
built alongside the token sequence (`util/TokenCombiner.java`). Combined with the unique
per-document sentinels, every reported sequence can be traced back to its exact word
position in both source documents.

### 6.8 Upload Limits

25 MB per file and 100 MB per request, set both in `application.properties`
(`spring.servlet.multipart.*`) and programmatically as a `MultipartConfigElement` bean in
`config/CorsConfig.java`. An upload over the limit returns HTTP 413 with a JSON error.

## 7. Project Structure

```
DOCS_SIMILARITY/
├── backend/
│   ├── pom.xml
│   ├── mvnw / mvnw.cmd / .mvn/
│   └── src/
│       ├── main/java/com/docs_similarity/
│       │   ├── DocsSimilarityApplication.java
│       │   ├── controller/AnalysisController.java, UploadLimitExceptionHandler.java
│       │   ├── config/CorsConfig.java
│       │   ├── service/
│       │   │   ├── DocumentSimilarityService.java
│       │   │   └── CommonSequenceMerger.java
│       │   ├── dsa/
│       │   │   ├── SuffixArray.java
│       │   │   ├── KasaiLCP.java
│       │   │   └── CommonWordSequenceAnalyzer.java
│       │   ├── extraction/
│       │   │   ├── TextExtractor.java
│       │   │   ├── TxtTextExtractor.java
│       │   │   ├── PdfTextExtractor.java
│       │   │   ├── DocxTextExtractor.java
│       │   │   ├── DocumentTextExtractor.java
│       │   │   └── ExtractionException.java
│       │   ├── model/
│       │   │   ├── Document.java
│       │   │   ├── DocumentResult.java
│       │   │   ├── CommonWordSequence.java
│       │   │   ├── SimilarityResult.java
│       │   │   ├── SuffixArrayEntry.java
│       │   │   ├── AlgorithmDetails.java
│       │   │   └── AnalysisResponse.java
│       │   └── util/WordTokenizer.java, util/TokenCombiner.java
│       └── test/java/com/docs_similarity/
│           ├── dsa/SuffixArrayTest.java
│           ├── dsa/KasaiLCPTest.java
│           ├── dsa/CommonWordSequenceAnalyzerTest.java
│           ├── util/WordTokenizerTest.java, util/TokenCombinerTest.java
│           ├── config/UploadLimitsIntegrationTest.java
│           ├── extraction/TxtTextExtractorTest.java
│           ├── extraction/PdfTextExtractorTest.java
│           ├── extraction/DocxTextExtractorTest.java
│           ├── extraction/DocumentTextExtractorTest.java
│           ├── service/DocumentSimilarityServiceTest.java
│           └── service/CommonSequenceMergerTest.java
├── frontend/
│   ├── package.json, vite.config.js, index.html
│   └── src/
│       ├── main.jsx, App.jsx, index.css
│       └── components/
│           ├── UploadPanel.jsx
│           ├── ProgressChecklist.jsx
│           ├── SummaryCards.jsx
│           ├── DocumentsList.jsx
│           ├── RankingTable.jsx
│           ├── PairDetail.jsx
│           ├── Pipeline.jsx
│           └── AlgorithmDetails.jsx
├── sample-documents/
│   ├── document1.txt   (shares a large passage with document3 — high similarity)
│   ├── document2.txt   (shares a large passage with document4 — high similarity)
│   ├── document3.txt   (near-duplicate of document1 with edits)
│   └── document4.txt   (near-duplicate of document2 with edits)
└── README.md
```

## 8. How to Run — Backend

Requires **Java 17+**. From `backend/`:

```bash
./mvnw spring-boot:run
```

(Windows: `mvnw.cmd spring-boot:run`)

The first run downloads Maven itself via the wrapper, then all dependencies — an internet
connection is required for this step. The API starts on **http://localhost:8080**.

To run the test suite:

```bash
./mvnw test
```

## 9. How to Run — Frontend

Requires **Node.js 18+**. From `frontend/`:

```bash
npm install
npm run dev
```

The dashboard opens on **http://localhost:5173** and talks to the backend at
`http://localhost:8080`. Start the backend first.

## 10. API Endpoints

### `POST /api/analyze`

`multipart/form-data`, field name `files`, 2 or more documents in any mix of **TXT, PDF,
and DOCX**.

On a validation or extraction failure (unsupported type, corrupt file, empty document,
scanned/image-only PDF with no text layer, etc.), returns **HTTP 400** with
`{ "error": "<specific message>" }` — no partial or fake results are ever returned.

On success, returns JSON:

```json
{
  "documents": [
    { "id": 0, "filename": "document1.txt", "length": 1288, "wordCount": 187, "sourceFormat": "TXT", "extractionStatus": "SUCCESS" },
    { "id": 1, "filename": "document3.docx", "length": 1147, "wordCount": 164, "sourceFormat": "DOCX", "extractionStatus": "SUCCESS" }
  ],
  "ranking": [
    {
      "rank": 1, "docAId": 0, "docBId": 1,
      "docAName": "document1.txt", "docBName": "document3.docx",
      "similarityPercent": 72.36,
      "wordCountA": 187, "wordCountB": 164,
      "sharedWordsInA": 127, "sharedWordsInB": 127, "sharedWords": 127,
      "totalCommonSequences": 1,
      "topCommonSequences": [ { "text": "renewable energy sources such as ...", "wordCount": 127, "offsetInA": 0, "offsetInB": 11 } ]
    }
  ],
  "summary": { "documentsAnalyzed": 2, "pairsCompared": 1, "commonSegmentsFound": 1, "highestSimilarity": 72.36 },
  "algorithmDetails": { "combinedTokenCount": 353, "minSequenceWords": 3, "...": "suffix array / LCP status and word-based sample rows" },
  "pipelineStages": ["Documents loaded", "Text extracted from all documents (TXT/PDF/DOCX)", "..."]
}
```

`totalCommonSequences` / `summary.commonSegmentsFound` count the distinct maximal common word
sequences shown in the UI; `algorithmDetails.rawSuffixMatches` is the suffix-level match
count before merging. All lengths and offsets are in words.

### `GET /api/health`

Simple liveness check.

## 11. Sample Input

`sample-documents/` contains 4 files demonstrating all three cases required by the brief:

- **Highly similar:** `document1.txt` ↔ `document3.txt` (shared renewable-energy passage)
- **Highly similar (second pair):** `document2.txt` ↔ `document4.txt` (shared urban-gardening passage)
- **Unrelated / low similarity:** every other pair (e.g. `document1.txt` ↔ `document2.txt`)

## 12. Expected Output

Running the sample set through the real engine (minimum sequence length = 3 words) produces:

| Pair                          | Similarity | Shared words |
|--------------------------------|-----------:|-------------:|
| document1.txt ↔ document3.txt  | 72.36%     | 127          |
| document2.txt ↔ document4.txt  | 69.50%     | 98           |
| every other pair               | 0.00%      | 0            |

(Computed live — this table is a sanity reference, not a hardcoded output. The unrelated
pairs no longer pick up coincidental character fragments.)

## 13. Testing

JUnit 5 tests cover:

- **Token-based Suffix Array** — word-ID suffix order for a known sentence, brute-force
  cross-check on random integer sequences, plus the classic `"banana$"` examples.
- **Kasai LCP in words** — a shared 3-word phrase gives LCP 3 (not its character length),
  brute-force cross-check on random token sequences.
- **Tokenization** — case, punctuation, whitespace, contractions, ligatures, token IDs and
  reverse mapping.
- **Common word sequences** — identical, unrelated, partially similar documents, repeated
  words, punctuation/case normalization, document boundary protection, three identical
  documents, and character fragments (`environment` / `nvironment`) not matching.
- **Overlap merging** — overlapping and nested suffix matches collapse into one sequence,
  distinct alignments stay separate, contained variations are dropped.
- **Similarity** — exact formula value, overlapping matches not double-counted, repeated
  words cannot inflate the score, identical content across TXT/PDF/DOCX.
- **Ranking** — sorted by word-based similarity with correct rank numbers.
- **Extraction** — TXT, PDF and DOCX extraction, corrupt files, scanned PDFs, unsupported types.
- **Upload limits** — a ~16 MB PDF is accepted by the real embedded server; a 26 MB file gets 413.

Run with `./mvnw test` from `backend/`.

## 14. Limitations

- Only TXT, PDF, and DOCX are accepted. Legacy binary `.doc`, `.pptx`, and `.xlsx` are
  rejected with a clear error, as is any other file type.
- PDF/DOCX extraction reads the embedded text layer only — **no OCR** is performed. A
  scanned or image-only PDF is rejected with a specific error rather than silently analyzed
  as empty.
- The similarity formula weights exact word-sequence overlap; it does not account for
  paraphrasing, synonym substitution, or reordering of ideas — by design, since this is a
  Suffix-Array-based lexical engine, not a semantic one.
- `MIN_SEQUENCE_WORDS` (3 words) is a fixed constant; shorter coincidental matches are
  ignored. Common 3-word phrases (e.g. "one of the") can still match between unrelated
  news articles; raise the constant to 4 or 5 for stricter results.
- Words split across a PDF line break with a hyphen ("environ-" / "ment") become two words.
- The Suffix Array uses prefix doubling (O(n log n)) rather than a linear-time construction
  (e.g. SA-IS/DC3) — a deliberate trade-off favoring code clarity and readability for a
  DSA-3 academic demonstration over maximal asymptotic performance.

## 15. Future Enhancements

- Linear-time Suffix Array construction (SA-IS).
- OCR support for scanned/image-only PDFs.
- Support for legacy `.doc`, `.pptx`, and `.xlsx` input formats.
- Semantic similarity as a secondary, clearly-labeled metric alongside the lexical score.
- Persisted analysis history and exportable reports.

## 16. Team

| Roll No.     | Name                    |
|--------------|-------------------------|
| 2520030601   | Billakanti Siri         |
| 2520030082   | Bhavana Kondakrindi     |
| 2520030184   | Aniketh Pani            |
