# Data Structures Documentation

This section documents all data structure implementations in the repository.

## Categories

### 1. [Binary Trees](binary_trees/index.md)
Tree-based data structures including BST, AVL, Red-Black trees, and specialized trees.

- Binary Search Tree (BST)
- AVL Tree (Self-balancing)
- Red-Black Tree
- Segment Tree
- Fenwick Tree (Binary Indexed Tree)
- Treap
- Wavelet Tree

### 2. [Linked Lists](linked_lists/index.md)
Linear data structures with dynamic memory allocation.

- Singly Linked List
- Doubly Linked List
- Circular Linked List
- Skip List

### 3. [Heaps](heaps/index.md)
Priority queue implementations.

- Min Heap
- Max Heap
- Binomial Heap
- Skew Heap
- Randomized Heap

### 4. [Stacks](stacks/index.md)
LIFO (Last-In-First-Out) data structures and applications.

- Basic Stack
- Stack Applications (Expression Evaluation, Balancing)

### 5. [Queues](queues/index.md)
FIFO (First-In-First-Out) data structures.

- Basic Queue
- Circular Queue
- Deque (Double-Ended Queue)
- Priority Queue

### 6. [Hash Tables](hash_tables/index.md)
Key-value storage with O(1) average operations.

- Hash Map
- Hash Table with Chaining
- Open Addressing (Linear, Quadratic, Double Hashing)
- Bloom Filter

### 7. [Tries](tries/index.md)
Tree structures for string operations.

- Standard Trie
- Radix Tree (Compressed Trie)
- Suffix Tree

### 8. [Disjoint Sets](disjoint_sets/index.md)
Union-Find data structure for set operations.

- Union-Find with Path Compression
- Union by Rank

### 9. [Advanced Structures](advanced/index.md)
Specialized data structures.

- KD-Tree
- Sparse Table
- Suffix Array

---

## Complexity Summary

| Data Structure | Insert | Delete | Search | Space |
|---------------|--------|--------|--------|-------|
| Array | O(n) | O(n) | O(n) | O(n) |
| Linked List | O(1) | O(1)* | O(n) | O(n) |
| BST | O(log n)** | O(log n)** | O(log n)** | O(n) |
| AVL Tree | O(log n) | O(log n) | O(log n) | O(n) |
| Red-Black Tree | O(log n) | O(log n) | O(log n) | O(n) |
| Hash Table | O(1)*** | O(1)*** | O(1)*** | O(n) |
| Heap | O(log n) | O(log n) | O(n) | O(n) |
| Trie | O(m) | O(m) | O(m) | O(ALPHABET × m × n) |

*With reference to node
**Average case; worst case O(n) for unbalanced
***Average case; worst case O(n) for collisions
m = key length, n = number of keys
