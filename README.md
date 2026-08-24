# KLH-CSE-2026-27-TEAM-07-DOCS_SIMILARITY

# DOCS_SIMILARITY

## Project Title

**Document Similarity Analyzer Using Suffix Structures and Similarity Metrics**

**Repository Name:** `KLH-CSE-2026-27-TEAM-07-DOCS_SIMILARITY`

---

## Team Members

| S. No. | Team Member            | Roll Number    |
| ------ | ---------------------- | -------------- |
| 1      | **Siri Billakanti**    | **2520030601** |
| 2      | **Bhavana Kondakrindi**| **2520030082** |
| 3      | **Aniketh Pani**       | **2520030184** |


---

## Supervisor

**Dr Anuradha**

---

## Abstract

DOCS_SIMILARITY is a document similarity analyzer designed to compare large text documents and identify duplicate or highly similar documents. The system analyzes common substrings and matching patterns between documents, computes similarity scores, and ranks documents according to their similarity.

The project uses suffix-based string-processing techniques as its primary algorithmic approach. **Suffix Arrays** and **Longest Common Prefix (LCP) Arrays**, with LCP computation using the **Kasai Algorithm**, are used to analyze common textual segments between documents.

The system is designed to efficiently process multiple documents and can support applications such as **plagiarism detection, duplicate document identification, and document clustering**.

The project directly aligns with the DSA-3 course's TextHack framework, where document similarity is mapped to suffix structures. The course specifically covers Suffix Arrays, LCP Arrays, and Kasai's Algorithm for document similarity and common-substring analysis.

---
## Literature Grounding

| Research Article | What it did / Method | Key Finding & Conclusion | Relevance to DOCS_SIMILARITY |
|---|---|---|---|
| **Manber & Myers (1993) — "Suffix Arrays: A New Method for On-Line String Searches"** | Introduced Suffix Arrays as a compact alternative to suffix trees for efficient string searching. | Demonstrated that suffix arrays provide efficient searching with lower practical space requirements. | Provides the foundation for using Suffix Arrays in our document similarity system. [Paper](https://epubs.siam.org/doi/10.1137/0222058) |
| **Kasai et al. (2001) — "Linear-Time Longest-Common-Prefix Computation in Suffix Arrays and Its Applications"** | Proposed the Kasai Algorithm for constructing the LCP array efficiently from a suffix array. | Showed that LCP information can be computed in linear time. | Provides the LCP/Kasai component used to identify long common textual segments. [Paper](https://doi.org/10.1007/3-540-48194-2_17) |
| **Gagie et al. (2017) — "Document Retrieval on Repetitive String Collections"** | Applied suffix-array/LCP-based structures to collections of documents and document retrieval. | Demonstrated efficient document-level retrieval on repetitive text collections. | Supports our use of suffix structures for multiple documents. [Paper](https://link.springer.com/article/10.1007/s10791-017-9297-7) |
| **Louza et al. (2020) — "gsufsort: Constructing Suffix Arrays, LCP Arrays and BWTs for String Collections"** | Developed practical methods for constructing Suffix Arrays, LCP Arrays and document-related structures for string collections. | Demonstrated efficient construction for large collections. | Supports the scalability and implementation feasibility of our suffix-based approach. [Paper](https://doi.org/10.1186/s13015-020-00177-y) |
| **Baba et al. (2017) — "Plagiarism Detection Using Document Similarity Based on Distributed Representation"** | Used Longest Common Subsequence (LCS) and distributed word representations for plagiarism detection. | Showed that sequence-based document similarity can be used for plagiarism detection. | Supports our application of textual similarity to plagiarism detection. [Paper](https://doi.org/10.1016/j.procs.2017.06.038) |
| **Hussain & Suryani (2015) — "On Retrieving Intelligently Plagiarized Documents Using Semantic Similarity"** | Used semantic similarity to detect plagiarism where wording is changed but meaning is retained. | Showed that semantic methods can detect plagiarism that direct text matching may miss. | Defines a limitation of our system: DOCS_SIMILARITY focuses on textual overlap rather than full semantic plagiarism. [Paper](https://doi.org/10.1016/j.engappai.2015.06.007) |
| **Amirzhanov et al. (2025) — "Plagiarism Types and Detection Methods: A Systematic Survey of Algorithms in Text Analysis"** | Reviewed plagiarism detection methods including string matching, semantic methods, ML and deep learning. | Concluded that different plagiarism types require different detection techniques. | Supports our focus on duplicate and highly textually similar documents using suffix structures. [Paper](https://doi.org/10.3389/fcomp.2025.1504725) |

### Overall Literature Conclusion

Existing research establishes that Suffix Arrays and LCP structures provide efficient mechanisms for string and document-level analysis, while plagiarism research demonstrates the usefulness of textual similarity for detecting duplicated content. Semantic approaches are more suitable for paraphrased or meaning-preserving plagiarism. Therefore, DOCS_SIMILARITY focuses specifically on efficient detection of duplicate and highly textually similar documents using Suffix Arrays, LCP, and common-substring analysis.

## Setup and Execution Instructions

### Prerequisites

* Java Development Kit (JDK)
* Java-compatible IDE or command-line environment
* Git

### Clone the Repository

```bash
git clone <REPOSITORY-URL>
cd KLH-CSE-2026-27-TEAM-07-DOCS_SIMILARITY
```

### Compilation

The compilation command will be added once the Java project structure and source files are finalized.

### Execution

The execution command and input format will be updated after the initial implementation is completed.

### Input

The system will accept multiple text documents as input for similarity analysis.

### Output

The planned output includes:

* Similar document pairs
* Similarity scores
* Common textual segments or matching patterns
* Ranked similarity results

The DSA-3 practical plan specifically includes constructing suffix-array and LCP representations, identifying common textual segments, and generating similarity scores between documents.

---

## Current Phase Status

### Phase 1 — Problem Understanding and Algorithm Design

**Status: In Progress**

### Completed

* [x] Problem statement understood and analyzed
* [x] Project scope identified
* [x] Project mapped to DSA-3 String Algorithms


### In Progress

* [ ] Design document preprocessing
* [ ] Design Suffix Array construction
* [ ] Design LCP computation using Kasai Algorithm
* [ ] Define similarity scoring method
* [ ] Design multi-document comparison
* [ ] Design document ranking
* [ ] Define input and output formats

### Planned

* [ ] Implement Suffix Array
* [ ] Implement LCP Array using Kasai Algorithm
* [ ] Implement common-substring analysis
* [ ] Implement similarity score calculation
* [ ] Implement multi-document comparison
* [ ] Implement similarity ranking
* [ ] Test using sample documents
* [ ] Analyze time and space complexity
* [ ] Test performance on larger document collections
* [ ] Complete final documentation and demonstration

---

## Course Information

**Course:** Data Structures and Algorithms - 3
**Course Code:** 25CS2103E
**Academic Year:** 2026–2027
**Team:** 07

