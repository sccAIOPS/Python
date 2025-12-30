# String Algorithms

This section contains comprehensive documentation for string processing and pattern matching algorithms.

## Pattern Matching Algorithms

| Algorithm | Time Complexity | Space | Best For |
|-----------|-----------------|-------|----------|
| [KMP (Knuth-Morris-Pratt)](kmp.md) | O(n + m) | O(m) | Single pattern, streaming |
| [Rabin-Karp](rabin_karp.md) | O(n + m) avg | O(1) | Multiple patterns, plagiarism detection |
| [Boyer-Moore](boyer_moore.md) | O(n/m) best | O(m + Σ) | Long patterns, large alphabets |
| [Aho-Corasick](aho_corasick.md) | O(n + m + z) | O(m) | Multiple patterns simultaneously |
| [Z-Function](z_function.md) | O(n) | O(n) | Pattern matching, string analysis |

## String Similarity & Distance

| Algorithm | Time Complexity | Range | Best For |
|-----------|-----------------|-------|----------|
| [Levenshtein Distance](levenshtein_distance.md) | O(mn) | [0, max(m,n)] | General edit distance |
| [Hamming Distance](hamming_distance.md) | O(n) | [0, n] | Equal-length strings, error detection |
| [Jaro-Winkler](jaro_winkler.md) | O(mn) | [0, 1] | Name matching, short strings |

## Palindrome Algorithms

| Algorithm | Time Complexity | Space | Purpose |
|-----------|-----------------|-------|---------|
| [Manacher's Algorithm](manacher.md) | O(n) | O(n) | Longest palindromic substring |

## Quick Reference: When to Use What

### Pattern Matching
```
Need to find one pattern?
├── Pattern is long → Boyer-Moore
├── Need streaming/online → KMP
└── Need simplicity → Rabin-Karp

Need to find multiple patterns?
├── Fixed set of patterns → Aho-Corasick
└── Dynamic patterns → Multiple Rabin-Karp

Need string properties?
├── All pattern positions → Z-Function
└── Palindrome info → Manacher
```

### String Similarity
```
Comparing strings?
├── Equal length, binary → Hamming Distance
├── Short strings/names → Jaro-Winkler  
├── General text → Levenshtein Distance
└── Need true metric → Levenshtein Distance
```

## Complexity Comparison Table

| Algorithm | Preprocessing | Search | Total Space |
|-----------|--------------|--------|-------------|
| Naive | O(1) | O(nm) | O(1) |
| KMP | O(m) | O(n) | O(m) |
| Boyer-Moore | O(m + Σ) | O(n/m) | O(m + Σ) |
| Rabin-Karp | O(m) | O(nm) worst | O(1) |
| Aho-Corasick | O(Σm) | O(n + z) | O(Σm) |
| Z-Function | O(n) | - | O(n) |

Where:
- n = text length
- m = pattern length (or total pattern length)
- Σ = alphabet size
- z = number of matches

## Algorithm Implementation Files

| File | Algorithm |
|------|-----------|
| [knuth_morris_pratt.py](../../../strings/knuth_morris_pratt.py) | KMP |
| [rabin_karp.py](../../../strings/rabin_karp.py) | Rabin-Karp |
| [boyer_moore_search.py](../../../strings/boyer_moore_search.py) | Boyer-Moore |
| [aho_corasick.py](../../../strings/aho_corasick.py) | Aho-Corasick |
| [z_function.py](../../../strings/z_function.py) | Z-Function |
| [manacher.py](../../../strings/manacher.py) | Manacher |
| [levenshtein_distance.py](../../../strings/levenshtein_distance.py) | Levenshtein |
| [hamming_distance.py](../../../strings/hamming_distance.py) | Hamming |
| [jaro_winkler.py](../../../strings/jaro_winkler.py) | Jaro-Winkler |
