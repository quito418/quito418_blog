---
title: "Minimap and miniasm: fast mapping and de novo assembly for noisy long sequences"
date: 2022-02-18T06:22:18Z
draft: false
categories: ["Alignment"]
tags: ["Longread","Alignment","NGS", "minimap", "minimizer"]
author: "Me"
hidemeta: false
comments: false
Summary: "Understanding the algorithm of Minimap."
Description: "Understanding the algorithm of Minimap. I don't describe the algorithm of Miniasm here."
---

## Summary
Software that maps long reads to the reference genome.
> Minimap adopts the
> idea of sketch like MHAP but takes minimizers as a reduced representation instead; it
> stores k-mers in a hash table like BLAT and MHAP but also uses
> sorting extensively like DALIGNER. In addition, minimap is designed
> not only as a read overlapper but also as a read-to-genome
> and genome-to-genome mapper.
## Algorithms explained 

<!-- ### List of concepts to know
- how minimizer works
-  -->

### A-1. MinimizerSketch(s, w, k)
![Algorithm 1](/assets/data/minimap/A-1.JPG)
<!-- ### Case for  -->
> minimizer is the smallest K-mer hash value within window size w.
> finding minimizer is 

### For each character in input string s
1. Calculates the hash value of both K-mer and reverse-complement K-mer. Choose the one with the smaller hash value.
   - Strand number is stored along with the K-mer hash value, therefore, if strand is ambiguous (ambiguous when hash values of s and RC of s are identical), the K-mer hash value is discarded.
   - Minimizer is compared with the minimum K-mer hash value between the one from the original strand and the one from the RC strand. This enables to align reads to the reference regardless of the strand the read aligns to.
2. All calculated K-mer hash value is appended to circular queue.
   - Minimap2 uses a circular queue (variable buf in minimap2/sketch.c) to track the minimum K-mer hash value.
   - (position in input string s, reference_id, strand number (0 or 1)) are also stored in the queue along with the hash value.
3. The minimum k-mer hash value is updated and appended to output minimizer in 2 cases.
   1. New K-mer hash value from 1 is smaller than current minimum K-mer hash value
   2. Minimum K-mer hash value is not within window size w anymore.


<!-- ```
M = set()
for i to |s| - w - k + 1 do
    m = 'inf'
    for j = 0 to w - 1 do
    u, v = hash( )
``` -->

### A-2. InvertibleHash(x, p)
> Reference: https://naml.us/post/inverse-of-a-hash-function/
> 
> Thomas Wang’s integer hash functions
> 
![Algorithm 2](/assets/data/minimap/A-2.JPG)
- To avoid oversampling poly-A (K-mer hash of poly-A as minimizer), minimap uses an invertible Hash function. As a result, poly-A which might have been 0 hash value with naive 2-bit encoding, results in non-zero hash value. Minimizer is the minimum K-mer hash value within window size w, hence, poly-A are less likely to be selected as minimizer which diversifies the minimizers and avoid oversampling poly-A as minimizer.
- Key properties include avalanche (changing any input bit changes about half of the output bits) and invertibility.
<!-- - poly-A which might have been 0 with naive encoding, results in non-zero hash value. -->

### A-3. Index(T, w, k)
Given a multiple strings T.
1. For each reference string t in T, collect the minimizers (A-1).
2. Sort the minimizers and store it in buckets (index). The bucket uses b bit for hash value instead of 2k bit.
   - Sorted with the (1: minimizer, 2:position) (K-mer hash value of minimizers).
   - Values are the reference index, position in the reference, strand, number of minimizers.
   - Using a bucket reduces the memory size and increases the cache hit ratio.
3. The bucket stores the interval of minimizers' position.
   - In particular, bucket stores the start position and the number of minimzers in the corresponding bucket.
4. The actual position can be obtained by lookup in 1) bucket hash table and 2) minimizer-position array.


### A-4. Map(H, q, w, k, e)

Given a query q.
1. Get minimizers of query q
2. Sort minimizers
3. Do Chaining (find seeds that are colinear), Seed extension (Needleman–Wunsch algorithm, Global alignment).

Chaining and Seed extension will be covered in other Paper Reading post.
