# Aho-Corasick Algorithm

## Overview
- **Category**: Multi-Pattern String Matching
- **Complexity**: Time: O(n + m + z) | Space: O(m)
- **Type**: Automaton-based pattern matching
- **Source File**: [strings/aho_corasick.py](../../../strings/aho_corasick.py)

## 1. Mathematical Foundation

### 1.1 Problem Definition

Given a text $T[1..n]$ and a set of patterns $\mathcal{P} = \{P_1, P_2, ..., P_k\}$ with total length $m = \sum_{i=1}^{k}|P_i|$, find all occurrences of all patterns in $T$.

$$
\text{For each } P_i \in \mathcal{P}, \text{ find all } j \text{ such that } T[j..j+|P_i|-1] = P_i
$$

### 1.2 Finite Automaton Structure

The Aho-Corasick automaton $\mathcal{A} = (Q, \Sigma, \delta, q_0, F)$ where:
- $Q$: Set of states (nodes in trie)
- $\Sigma$: Alphabet
- $\delta$: Transition function (goto + failure)
- $q_0$: Initial state (root)
- $F$: Set of accepting states (pattern endpoints)

### 1.3 Key Functions

**Goto Function** $g(q, a)$:
$$
g(q, a) = \begin{cases}
p & \text{if there is edge } q \xrightarrow{a} p \\
\text{fail} & \text{otherwise}
\end{cases}
$$

**Failure Function** $f(q)$:
$$
f(q) = \text{longest proper suffix of string reaching } q \text{ that is also a prefix of some pattern}
$$

**Output Function** $\text{out}(q)$:
$$
\text{out}(q) = \{P_i : P_i \text{ ends at state } q \text{ or at states reachable via failure links}\}
$$

## 2. Algorithm Description

### 2.1 Intuition

Aho-Corasick combines:
1. **Trie**: For organizing patterns by common prefixes
2. **Failure links**: Similar to KMP, for efficient backtracking
3. **Output links**: For collecting all matching patterns at each state

### 2.2 Three Phases

1. **Build Trie**: Insert all patterns into a trie structure
2. **Build Failure Links**: Using BFS, compute failure function
3. **Search**: Process text character by character using the automaton

## 3. Pseudocode

```
ALGORITHM Aho-Corasick-Build(Patterns)
    INPUT: Set of patterns P = {P₁, P₂, ..., Pₖ}
    OUTPUT: Automaton with goto, failure, and output functions
    
    // Phase 1: Build Trie
    root ← new TrieNode()
    for each pattern P in Patterns do
        node ← root
        for each character c in P do
            if node.children[c] is null then
                node.children[c] ← new TrieNode()
            node ← node.children[c]
        node.output.add(P)
    
    // Phase 2: Build Failure Links (BFS)
    queue ← empty queue
    for each child c of root do
        c.failure ← root
        queue.enqueue(c)
    
    while queue is not empty do
        current ← queue.dequeue()
        for each (char, child) in current.children do
            queue.enqueue(child)
            
            // Find failure state
            failure ← current.failure
            while failure ≠ null AND failure.children[char] is null do
                failure ← failure.failure
            
            if failure is null then
                child.failure ← root
            else
                child.failure ← failure.children[char]
            
            // Merge outputs via failure link
            child.output ← child.output ∪ child.failure.output
    
    return root

ALGORITHM Aho-Corasick-Search(text, automaton)
    INPUT: Text T, Automaton root
    OUTPUT: List of (position, pattern) matches
    
    matches ← empty list
    state ← automaton
    
    for i ← 0 to length(text) - 1 do
        c ← text[i]
        
        // Follow failure links until match or root
        while state ≠ root AND state.children[c] is null do
            state ← state.failure
        
        if state.children[c] exists then
            state ← state.children[c]
        
        // Report all patterns ending here
        for each pattern in state.output do
            matches.add((i - length(pattern) + 1, pattern))
    
    return matches
```

## 4. Complexity Analysis

### 4.1 Time Complexity

| Operation | Complexity | Explanation |
|-----------|------------|-------------|
| Build Trie | O(m) | Each character visited once |
| Build Failure | O(m) | BFS over all nodes |
| Search | O(n + z) | n chars + z matches |
| **Total** | **O(n + m + z)** | Linear in all parameters |

Where:
- $n$ = text length
- $m$ = total pattern length
- $z$ = number of pattern occurrences

### 4.2 Space Complexity

- Trie nodes: O(m)
- Failure links: O(number of nodes)
- Output lists: O(total patterns)
- **Total**: O(m)

### 4.3 Amortized Analysis

The failure link traversal is amortized O(1) per text character because:
- We can only go "up" (via failure) as many times as we went "down"
- Total failure transitions ≤ n

## 5. Visual Representation

### 5.1 Example: Patterns = {he, she, his, hers}

```
                    root
                  /  |  \
                 h   s   (other chars → root)
                /     \
               e       h
              / \       \
             ↓   r       e
            [he] s      [she]
                 ↓
                [hers]
              
Failure Links (---→):
- e(he) ---→ root
- r ---→ root  
- s ---→ s(she)
- h(sh) ---→ h
- e(she) ---→ e(he)
```

### 5.2 Automaton Diagram

```mermaid
stateDiagram-v2
    [*] --> root
    root --> h: h
    root --> s: s
    h --> he: e
    h --> hi: i
    he --> her: r
    her --> hers: s
    s --> sh: h
    sh --> she: e
    hi --> his: s
    
    note right of he: Output: {he}
    note right of she: Output: {she, he}
    note right of his: Output: {his}
    note right of hers: Output: {hers}
```

### 5.3 Search Trace

```
Text: "ushers"

Position 0: 'u' → stay at root
Position 1: 's' → goto s
Position 2: 'h' → goto sh  
Position 3: 'e' → goto she, OUTPUT: [she, he] at pos 1, 2
Position 4: 'r' → failure to root, goto root
Position 5: 's' → goto s

Matches: [(1, "she"), (2, "he")]
```

## 6. Implementation Details

### 6.1 Trie Node Structure

```python
from collections import deque
from dataclasses import dataclass, field

@dataclass
class AhoCorasickNode:
    """Node in Aho-Corasick automaton."""
    children: dict = field(default_factory=dict)
    failure: 'AhoCorasickNode' = None
    output: list = field(default_factory=list)
    depth: int = 0
```

### 6.2 Optimizations

1. **Array-based children** for small alphabets (DNA: ACGT)
2. **Compressed output** using suffix links
3. **Double-array trie** for memory efficiency
4. **SIMD acceleration** for parallel character matching

## 7. Comparison with Alternatives

| Algorithm | Patterns | Preprocessing | Search | Memory |
|-----------|----------|---------------|--------|--------|
| **Aho-Corasick** | Multiple | O(m) | O(n+z) | O(m) |
| Multiple KMP | Multiple | O(m) | O(n×k) | O(m) |
| Multiple Boyer-Moore | Multiple | O(m×Σ) | O(n×k/m) | O(m×Σ) |
| Rabin-Karp | Multiple | O(m) | O(n×k) avg | O(k) |
| Commentz-Walter | Multiple | O(m) | O(n/m) | O(m) |

## 8. Real-World Software Engineering Applications

### 8.1 Industry Use Cases

1. **Network Intrusion Detection (Snort, Suricata)**
   - Match thousands of attack signatures simultaneously
   - Line-rate packet inspection
   - Used in enterprise firewalls

2. **Antivirus Engines**
   - Malware signature matching
   - ClamAV uses Aho-Corasick for pattern database
   - Scan files against millions of signatures

3. **Spam Filters**
   - Match spam keywords and phrases
   - URL blacklist matching
   - Header pattern detection

4. **DNA Sequence Analysis**
   - Find multiple gene sequences in genome
   - Primer matching
   - Variant detection

5. **Content Filtering**
   - Profanity filters
   - Sensitive data detection (PII, credit cards)
   - Content moderation

### 8.2 Production Example: Log Analysis

```python
class MultiPatternLogScanner:
    """High-performance multi-pattern log scanner."""
    
    def __init__(self, error_patterns: list[str]):
        self.root = self._build_automaton(error_patterns)
        self.pattern_severity = {p: self._classify(p) for p in error_patterns}
    
    def _build_automaton(self, patterns):
        """Build Aho-Corasick automaton."""
        root = AhoCorasickNode()
        
        # Build trie
        for pattern in patterns:
            node = root
            for char in pattern:
                if char not in node.children:
                    node.children[char] = AhoCorasickNode()
                node = node.children[char]
            node.output.append(pattern)
        
        # Build failure links (BFS)
        queue = deque()
        for child in root.children.values():
            child.failure = root
            queue.append(child)
        
        while queue:
            current = queue.popleft()
            for char, child in current.children.items():
                queue.append(child)
                failure = current.failure
                while failure and char not in failure.children:
                    failure = failure.failure
                child.failure = failure.children[char] if failure else root
                child.output += child.failure.output
        
        return root
    
    def scan(self, text: str) -> dict[str, list[int]]:
        """Find all pattern matches in text."""
        matches = {p: [] for p in self.pattern_severity}
        state = self.root
        
        for i, char in enumerate(text):
            while state != self.root and char not in state.children:
                state = state.failure
            state = state.children.get(char, self.root)
            
            for pattern in state.output:
                matches[pattern].append(i - len(pattern) + 1)
        
        return matches
    
    def analyze_log_file(self, filepath: str) -> dict:
        """Analyze entire log file."""
        alerts = {'critical': [], 'warning': [], 'info': []}
        
        with open(filepath, 'r') as f:
            for line_num, line in enumerate(f, 1):
                matches = self.scan(line)
                for pattern, positions in matches.items():
                    if positions:
                        severity = self.pattern_severity[pattern]
                        alerts[severity].append({
                            'line': line_num,
                            'pattern': pattern,
                            'positions': positions
                        })
        
        return alerts
```

### 8.3 Real-World Libraries

| Library | Language | Use Case |
|---------|----------|----------|
| **pyahocorasick** | Python | General text search |
| **aho-corasick** | Rust | High-performance NLP |
| **Snort** | C | Network IDS |
| **Hyperscan** | C | Intel's regex engine |
| **FlashText** | Python | Keyword extraction |

## 9. Variations and Extensions

### 9.1 Aho-Corasick with Wildcards
Support single-character wildcards in patterns.

### 9.2 Approximate Aho-Corasick
Allow up to k mismatches using automaton product construction.

### 9.3 Streaming Aho-Corasick
Process infinite streams with bounded memory.

## 10. Edge Cases

| Edge Case | Handling |
|-----------|----------|
| Empty pattern set | Return empty matches |
| Overlapping patterns | Report all matches |
| Pattern is prefix of another | Both reported |
| Case insensitivity | Normalize during build |
| Unicode text | Use character-based (not byte) transitions |

## 11. References

- Aho, A.V., Corasick, M.J. (1975). "Efficient String Matching: An Aid to Bibliographic Search"
- [Wikipedia: Aho-Corasick](https://en.wikipedia.org/wiki/Aho%E2%80%93Corasick_algorithm)
- Commentz-Walter, B. (1979). "A String Matching Algorithm Fast on the Average"
