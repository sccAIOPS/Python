# String Algorithms

## Overview

This section covers string processing algorithms including pattern matching, string similarity, and text manipulation.

## Algorithm Categories

| Category | Algorithms | Primary Use Case |
|----------|------------|------------------|
| **Pattern Matching** | KMP, Rabin-Karp, Boyer-Moore, Aho-Corasick, Z-function | Find substrings efficiently |
| **String Similarity** | Levenshtein, Hamming, Jaro-Winkler, Damerau-Levenshtein | Fuzzy matching, spell check |
| **Palindromes** | Manacher's Algorithm | Find palindromic substrings |
| **Text Processing** | Anagrams, N-grams, Frequency Analysis | Text analysis, NLP |

## Complexity Summary

| Algorithm | Preprocessing | Search | Space |
|-----------|---------------|--------|-------|
| **Naive Search** | - | O(nm) | O(1) |
| **KMP** | O(m) | O(n) | O(m) |
| **Rabin-Karp** | O(m) | O(n) avg | O(1) |
| **Boyer-Moore** | O(m + σ) | O(n/m) best | O(σ) |
| **Aho-Corasick** | O(Σm) | O(n + z) | O(Σm) |
| **Z-function** | - | O(n) | O(n) |
| **Manacher** | - | O(n) | O(n) |
| **Levenshtein** | - | O(nm) | O(min(n,m)) |

Where:
- n = text length
- m = pattern length
- σ = alphabet size
- z = number of matches

## Algorithms Documentation

### Pattern Matching
- [Knuth-Morris-Pratt (KMP)](kmp.md)
- [Rabin-Karp](rabin_karp.md)
- [Boyer-Moore](boyer_moore.md)
- [Aho-Corasick](aho_corasick.md)
- [Z-Function](z_function.md)

### String Distance
- [Levenshtein Distance](levenshtein.md)
- [Hamming Distance](hamming_distance.md)
- [Jaro-Winkler](jaro_winkler.md)

### Palindromes
- [Manacher's Algorithm](manacher.md)

### Text Processing
- [Anagrams](anagrams.md)
- [N-gram Analysis](ngram.md)

## Source Files

| File | Description |
|------|-------------|
| [knuth_morris_pratt.py](../../../strings/knuth_morris_pratt.py) | KMP algorithm |
| [rabin_karp.py](../../../strings/rabin_karp.py) | Rolling hash search |
| [boyer_moore_search.py](../../../strings/boyer_moore_search.py) | Bad character rule |
| [aho_corasick.py](../../../strings/aho_corasick.py) | Multi-pattern search |
| [z_function.py](../../../strings/z_function.py) | Z-array construction |
| [manacher.py](../../../strings/manacher.py) | Palindrome finding |
| [levenshtein_distance.py](../../../strings/levenshtein_distance.py) | Edit distance |
| [hamming_distance.py](../../../strings/hamming_distance.py) | Binary string distance |
| [jaro_winkler.py](../../../strings/jaro_winkler.py) | Name matching similarity |
