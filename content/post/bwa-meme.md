---
title: "BWA-MEME: BWA-MEM emulated with a machine learning approach"
date: 2023-12-15T11:11:00Z
draft: False
categories: ["Alignment", "Machine learning"]
tags: ["NGS", "ShortRead", "Alignment", "BWA-MEME", "BWA-MEM2", "BWA", "Machine learning", "learned-index"]
author: "Me"
Summary: "Paper summary of BWA-MEME."
Description: "Paper summary of BWA-MEME."
---

### Paper: [BWA-MEME: BWA-MEM emulated with a machine learning approach](https://academic.oup.com/bioinformatics/advance-article/doi/10.1093/bioinformatics/btac137/6543607)

### Summary
BWA-MEM, a widely used alignment tool, typically encounters bottlenecks in the seeding phase of its seed-and-extend alignment paradigm.
The paper propose BWA-MEME that emulates BWA MEM algorithm with a novel contraction-based approach in seeding.
This approach uses learned-index to enhance the longest-common-prefix (LCP) search efficiency between the query sequence and the suffix array, which represents the reference genome.


### BWA-MEME uses Contraction-based approach.
Existing works FM-index and ERT index uses a elongation-based approach for exact match search.
Those methods elongates the exact match between query and reference genome, which requires random memory access during index lookup.
BWA-MEME takes a contraction-based approach:
1. Finds LCP between query and suffix array using learned-index.
    Predicting the position of LCP is done using learned-index.
    Binary search is performed from the predicted position to find LCP. The region for binary search is determined by the max error is provided from the learned-index model (which is pre-calculated at the training stage).
2. Finds seeds nearby the LCP. (the positions of seeds in suffix array always includes LCP)

Seed is SMEM which is defined by:
- exact match between query and reference genome that cannot be extended in either direction without mismatch
- the number of exact matches to the reference genome should be larger than the given input threshold N.

FM-index or ERT keeps track of the number of exact match while elogating the exact match.
However, BWA-MEME first finds the longest exact match and finds a shorter exact match that adhere to the definition of seeds.
BWA-MEME performs seeding in zig-zag which is similar to the proposed approach in ERT, however, BWA-MEME shows the left-extension points (LEP) used in ERT are not required [[ref]](https://www.biorxiv.org/content/10.1101/2020.03.23.003897v1.full.pdf). 

### Better memory efficiency with contraction-based approach
- The LCP's position is efficiently predicted using a learned-index, typically requiring only one or two instances of random memory access.
- The memory access pattern becomes more predictable, with a binary search initiated from the predicted position and seeds searched near the LCP, either through linear or exponential methods.

Putting together, contraction-based approach opens up various ways of improving memory-efficiency of exact match search in BWA-MEME (including data-locality in index and memory prefetching)

### Components of BWA-MEME for further improvment in memory-efficiency
- Optimization of the RMI (Recursive Model Index) structure tailored for the reference genome.
- Improved data locality, achieved by integrating suffix characters directly next to suffix array position values.
- Efficient reuse of exact match search results, reducing redundant computations.

### Runtime index building
When disk I/O is limited, loading large index into memory can take significant time.
To address this issue, BWA-MEME implemented an runtime index building that only loads suffix array and builds the other indices at start-up.
The speed are equivalent to disk read of speed 3-5 GB per second. 