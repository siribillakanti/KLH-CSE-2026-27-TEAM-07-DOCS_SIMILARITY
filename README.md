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

DOCS_SIMILARITY is a document similarity analyzer designed to compare multiple large text documents and identify duplicate or highly similar documents. The system analyzes common substrings and matching patterns between documents, computes similarity scores, and ranks documents according to their degree of similarity. The project uses suffix-based string-processing techniques, particularly Suffix Arrays and Longest Common Prefix (LCP) Arrays, with the Kasai Algorithm used for LCP construction. These techniques enable efficient analysis of shared textual content across multiple documents. The system is intended for applications such as plagiarism detection, duplicate document identification, and document clustering. The approach directly aligns with the DSA-3 TextHack framework, which maps document similarity analysis to suffix structures and includes Suffix Arrays, LCP Arrays, and Kasai's Algorithm in the string-algorithm module.
---

## Objectives

The main objective of **DOCS_SIMILARITY** is to develop an efficient suffix-based system for analyzing similarity among multiple large text documents.

### Specific Objectives

- Compare **multiple text documents** and identify duplicate or highly similar documents.
- Analyze **common substrings and matching patterns** between documents.
- Construct and utilize **Suffix Arrays** for efficient string organization and analysis.
- Construct the **LCP Array using the Kasai Algorithm** to identify common textual segments.
- Compute **similarity scores** based on shared textual content.
- **Rank document pairs** according to their similarity scores.
- Design the system to efficiently handle **large text documents and multiple documents**.
- Evaluate the **time and space complexity** of the implemented algorithms.
- Demonstrate applications in **plagiarism detection, duplicate-document identification, and document clustering**.
  
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

## Design Methodology

DOCS_SIMILARITY will compare multiple text documents and identify documents that contain a high amount of common textual content.

### Implementation Steps

1. **Input Documents**
   - Accept multiple text documents as input.
   - Assign a unique ID to each document.

2. **Text Preprocessing**
   - Normalize the text and remove unnecessary formatting.
   - Preserve document boundaries for comparison.

3. **Suffix Array Construction**
   - Combine the processed documents using unique separators.
   - Construct a **Suffix Array** for the combined text.

4. **LCP Array Construction**
   - Construct the **LCP (Longest Common Prefix) Array**.
   - Use the **Kasai Algorithm** to calculate LCP values efficiently.

5. **Common Substring Analysis**
   - Analyze LCP values to identify common textual segments between different documents.

6. **Similarity Calculation**
   - Use the identified common text to calculate a similarity score for each document pair.

7. **Ranking**
   - Rank document pairs from highest to lowest similarity.
   - Display the most similar documents as the final result.

### Algorithms and Data Structures

- **Suffix Array** – organizes suffixes of the document collection.
- **LCP Array** – identifies the length of common prefixes between suffixes.
- **Kasai Algorithm** – efficiently constructs the LCP Array.
- **Document IDs and boundaries** – identify which document each suffix belongs to.
- **Similarity Score** – measures the amount of shared textual content.

### Why These Methods?

Suffix Arrays and LCP Arrays are chosen because the project focuses on finding **common substrings and matching patterns in large text documents**. These techniques are also directly related to the DSA-3 syllabus, which maps document similarity to suffix structures.

## Setup and Execution

### Prerequisites

- Java Development Kit (JDK)
- Java-compatible IDE
- Git

### Course Information

**Course:** Data Structures and Algorithms - 3
**Course Code:** 25CS2103E
**Academic Year:** 2026–2027
**Team:** 07


### Clone the Repository

```bash
git clone <REPOSITORY-URL>
cd KLH-CSE-2026-27-TEAM-07-DOCS_SIMILARITY
---


