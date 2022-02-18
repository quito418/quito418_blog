---
title: "Minimap and miniasm: fast mapping and de novo assembly for noisy long sequences"
date: 2022-02-18T06:22:18Z
draft: true
categories: ["Alignment"]
tags: ["Longread","Alignment","NGS", "minimap"]
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

### A-1. MinimizerSketch(s, w, k)
![Algorithm 1](/assets/data/minimap/A-1.JPG)
```
M = set()
for i to |s| - w - k + 1 do
    m = 'inf'
    for j = 0 to w - 1 do
    u, v = hash( )
```

### A-2. InvertibleHash(x, p)


```
```

### A-3. Index(T, w, k)

```
```

### A-4. Map(H, q, w, k, e)

```
```
