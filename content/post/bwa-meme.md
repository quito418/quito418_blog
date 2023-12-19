---
title: "BWA-MEME: BWA-MEM emulated with a machine learning approach"
date: 2023-12-15T11:11:00Z
draft: False
categories: ["Alignment", "Machine learning"]
tags: ["NGS", "ShortRead", "Alignment", "BWA-MEME", "BWA-MEM2", "BWA", "Machine learning", "learned-index"]
author: "Me"
hidemeta: false
comments: false
Summary: "Paper summary of BWA-MEME."
Description: "Paper summary of BWA-MEME."
---

### Paper: [BWA-MEME: BWA-MEM emulated with a machine learning approach](https://academic.oup.com/bioinformatics/advance-article/doi/10.1093/bioinformatics/btac137/6543607)

### Summary
Emulates BWA MEM algorithm with a novel contraction-based approach.
Uses learned-index to optimize the longest-common-prefix (LCP) search between query and suffix array (reference genome).


### Contraction-based approach.
Existing works FM-index and ERT index uses a elongation-based approach for exact match search.
Those methods elongates the exact match between query and reference genome, which requires random memory access during index lookup.
BWA-MEME takes a contraction-based approach:
1. Finds LCP between query and suffix array using learned-index.
    Predicting the position of LCP is done using learned-index.
    Binary search is performed from the predicted position to find LCP.
2. Finds seeds (seed and extension) nearby the LCP. (the positions of seeds in suffix array always includes LCP)


### Better memory efficiency with contraction-based approach
Fewer random memory access in index lookup
- Predicting the position of LCP is done using learned-index with one or two random memory access

Memory access pattern becomes predictable
- Binary search is performed from the predicted position. 
- Seed is searched near the longest common prefix (using linear or exponential search)

Putting together, contraction-based approach opens up various ways of improving memory-efficiency of exact match search in BWA-MEME (including data-locality in index and memory prefetching)

### Components of BWA-MEME for further improvment in memory-efficiency
- Optimizing RMI structure for reference genome
- Better data-locality: Suffix characters integrated into suffix array
- Re-use exact match search results