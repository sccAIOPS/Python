# Word Search

## Overview
- **Category**: Backtracking / String Matching
- **Complexity**: Time: O(m×n×4^L) | Space: O(L)
- **Type**: Grid traversal, pattern matching
- **Source Files**: [backtracking/word_search.py](../../../backtracking/word_search.py)

## 1. Mathematical Foundation

### 1.1 Problem Definition

Given an $m \times n$ board of characters and a word $W$ of length $L$:
- Find if word exists in grid
- Characters can connect horizontally or vertically (adjacent cells)
- Each cell can only be used once per word

### 1.2 Search Space

From each starting cell, the search tree has:
- Branching factor: up to 4 (adjacent cells)
- Maximum depth: $L$ (word length)

Total states to explore (worst case):
$$
O(m \times n \times 4^L)
$$

### 1.3 Constraint Formulation

For a valid path $(c_0, c_1, ..., c_{L-1})$:
1. Each $c_i$ is within grid bounds
2. $\text{board}[c_i] = W[i]$ for all $i$
3. $|c_i - c_{i+1}| = 1$ (adjacent)
4. All $c_i$ are distinct

### 1.4 Pruning Conditions

**Early termination when**:
- Character mismatch at any position
- Cell already visited in current path
- Remaining word cannot be formed

**Character frequency pruning**:
$$
\text{count}(\text{board}, c) \geq \text{count}(W, c) \quad \forall c \in W
$$

## 2. Algorithm Variants

### 2.1 Single Word Search
Find if one word exists.

### 2.2 Multi-Word Search (Trie-Based)
Find multiple words efficiently using Trie.

### 2.3 All Occurrences
Find all starting positions for a word.

### 2.4 Longest Word
Find longest possible word from dictionary.

## 3. Pseudocode

### 3.1 Basic Word Search

```
ALGORITHM WordSearch(board, word)
    INPUT: m×n character board, word to find
    OUTPUT: true if word exists, false otherwise
    
    m ← rows(board)
    n ← cols(board)
    
    // Try each cell as starting position
    for i ← 0 to m-1 do
        for j ← 0 to n-1 do
            if DFS(board, word, i, j, 0) then
                return true
    
    return false

ALGORITHM DFS(board, word, i, j, index)
    // Base case: found entire word
    if index = length(word) then
        return true
    
    // Boundary and character check
    if i < 0 OR i >= m OR j < 0 OR j >= n then
        return false
    
    if board[i][j] ≠ word[index] then
        return false
    
    // Mark cell as visited
    temp ← board[i][j]
    board[i][j] ← '#'  // Sentinel
    
    // Explore 4 directions
    found ← DFS(board, word, i+1, j, index+1) OR
            DFS(board, word, i-1, j, index+1) OR
            DFS(board, word, i, j+1, index+1) OR
            DFS(board, word, i, j-1, index+1)
    
    // Restore cell
    board[i][j] ← temp
    
    return found
```

### 3.2 With Frequency Pruning

```
ALGORITHM WordSearchOptimized(board, word)
    // Early pruning: check character frequency
    board_count ← CountCharacters(board)
    word_count ← CountCharacters(word)
    
    for each char c in word_count do
        if board_count[c] < word_count[c] then
            return false
    
    // Optimization: start from rarer end
    if board_count[word[0]] > board_count[word[-1]] then
        word ← Reverse(word)
    
    return WordSearch(board, word)
```

### 3.3 Trie-Based Multi-Word Search

```
ALGORITHM FindWords(board, words)
    INPUT: Board and list of words
    OUTPUT: List of found words
    
    // Build Trie from words
    trie ← BuildTrie(words)
    found ← []
    
    for i ← 0 to m-1 do
        for j ← 0 to n-1 do
            DFSWithTrie(board, i, j, trie.root, "", found)
    
    return found

ALGORITHM DFSWithTrie(board, i, j, node, path, found)
    // Boundary check
    if i < 0 OR i >= m OR j < 0 OR j >= n then
        return
    
    char ← board[i][j]
    
    // Not in Trie
    if char NOT in node.children then
        return
    
    node ← node.children[char]
    path ← path + char
    
    // Found a word
    if node.is_end then
        found.add(path)
        node.is_end ← false  // Avoid duplicates
    
    // Mark and explore
    board[i][j] ← '#'
    
    DFSWithTrie(board, i+1, j, node, path, found)
    DFSWithTrie(board, i-1, j, node, path, found)
    DFSWithTrie(board, i, j+1, node, path, found)
    DFSWithTrie(board, i, j-1, node, path, found)
    
    board[i][j] ← char
```

## 4. Step-by-Step Example

### Example: Find "ABCCED"

```
Board:          Word: "ABCCED"
A B C E
S F C S
A D E E
```

### Search Process

```
Step 1: Start at (0,0) = 'A' ✓
  Board:           Path: "A"
  [#,B,C,E]        
  [S,F,C,S]
  [A,D,E,E]

Step 2: Try neighbors of (0,0)
  (1,0)='S' ≠ 'B' ✗
  (0,1)='B' = 'B' ✓ → Continue

Step 3: At (0,1) = 'B' ✓
  Board:           Path: "AB"
  [#,#,C,E]
  [S,F,C,S]
  [A,D,E,E]

Step 4: Try neighbors of (0,1)
  (0,0)='#' visited ✗
  (1,1)='F' ≠ 'C' ✗
  (0,2)='C' = 'C' ✓ → Continue

Step 5: At (0,2) = 'C' ✓
  Board:           Path: "ABC"
  [#,#,#,E]
  [S,F,C,S]
  [A,D,E,E]

Step 6: Try neighbors of (0,2)
  (0,1)='#' visited ✗
  (1,2)='C' = 'C' ✓ → Continue

Step 7: At (1,2) = 'C' ✓
  Board:           Path: "ABCC"
  [#,#,#,E]
  [S,F,#,S]
  [A,D,E,E]

Step 8: Try neighbors of (1,2)
  (0,2)='#' visited ✗
  (2,2)='E' = 'E' ✓ → Continue

Step 9: At (2,2) = 'E' ✓
  Board:           Path: "ABCCE"
  [#,#,#,E]
  [S,F,#,S]
  [A,D,#,E]

Step 10: Try neighbors of (2,2)
  (1,2)='#' visited ✗
  (2,1)='D' = 'D' ✓ → Continue

Step 11: At (2,1) = 'D' ✓
  Path: "ABCCED" - LENGTH MATCHES!
  
FOUND! Return true
```

## 5. Complexity Analysis

| Metric | Complexity |
|--------|------------|
| Time (worst) | O(m×n×4^L) |
| Time (with pruning) | O(m×n×3^L) |
| Space | O(L) recursion stack |
| Space (Trie) | O(total word chars) |

Where:
- m, n = board dimensions
- L = word length

## 6. Visual Representation

### 6.1 Search Path

```
Finding "ABCCED" in grid:

  0   1   2   3
┌───┬───┬───┬───┐
│ A │ B │ C │ E │  0
│ ↓ │ ↓ │ ↓ │   │
├───┼───┼───┼───┤
│ S │ F │ C │ S │  1
│   │   │ ↓ │   │
├───┼───┼───┼───┤
│ A │ D │ E │ E │  2
│   │ ← │ ← │   │
└───┴───┴───┴───┘

Path: (0,0)→(0,1)→(0,2)→(1,2)→(2,2)→(2,1)
```

### 6.2 DFS Tree

```
                    (0,0) 'A'
                      │
                    (0,1) 'B'
                      │
                    (0,2) 'C'
                      │
                    (1,2) 'C'
                      │
                    (2,2) 'E'
                      │
                    (2,1) 'D' ✓
```

## 7. Implementation

```python
from typing import List, Set, Optional, Tuple
from dataclasses import dataclass, field
from collections import Counter


def word_search(board: List[List[str]], word: str) -> bool:
    """
    Search for word in character board.
    
    >>> board = [['A','B','C','E'],['S','F','C','S'],['A','D','E','E']]
    >>> word_search(board, 'ABCCED')
    True
    >>> word_search(board, 'SEE')
    True
    >>> word_search(board, 'ABCB')
    False
    """
    if not board or not board[0] or not word:
        return False
    
    m, n = len(board), len(board[0])
    
    def dfs(i: int, j: int, index: int) -> bool:
        # Found entire word
        if index == len(word):
            return True
        
        # Boundary and character check
        if i < 0 or i >= m or j < 0 or j >= n:
            return False
        if board[i][j] != word[index]:
            return False
        
        # Mark as visited
        temp = board[i][j]
        board[i][j] = '#'
        
        # Explore 4 directions
        found = (dfs(i + 1, j, index + 1) or
                 dfs(i - 1, j, index + 1) or
                 dfs(i, j + 1, index + 1) or
                 dfs(i, j - 1, index + 1))
        
        # Restore
        board[i][j] = temp
        return found
    
    for i in range(m):
        for j in range(n):
            if dfs(i, j, 0):
                return True
    
    return False


def word_search_optimized(board: List[List[str]], word: str) -> bool:
    """
    Optimized word search with frequency pruning.
    
    >>> board = [['A','B'],['C','D']]
    >>> word_search_optimized(board, 'ABCD')
    False
    >>> word_search_optimized(board, 'ABD')
    False
    """
    if not board or not board[0] or not word:
        return False
    
    m, n = len(board), len(board[0])
    
    # Frequency check
    board_count = Counter()
    for row in board:
        board_count.update(row)
    
    word_count = Counter(word)
    
    for char, count in word_count.items():
        if board_count[char] < count:
            return False
    
    # Start from rarer end
    if board_count[word[0]] > board_count[word[-1]]:
        word = word[::-1]
    
    def dfs(i: int, j: int, index: int) -> bool:
        if index == len(word):
            return True
        
        if (i < 0 or i >= m or j < 0 or j >= n or
            board[i][j] != word[index]):
            return False
        
        temp = board[i][j]
        board[i][j] = '#'
        
        for di, dj in [(0, 1), (0, -1), (1, 0), (-1, 0)]:
            if dfs(i + di, j + dj, index + 1):
                board[i][j] = temp
                return True
        
        board[i][j] = temp
        return False
    
    for i in range(m):
        for j in range(n):
            if dfs(i, j, 0):
                return True
    
    return False


def find_word_positions(
    board: List[List[str]],
    word: str
) -> List[List[Tuple[int, int]]]:
    """
    Find all occurrences of word in board.
    
    Returns list of paths (each path is list of positions).
    
    >>> board = [['A','A'],['A','A']]
    >>> paths = find_word_positions(board, 'AA')
    >>> len(paths)
    8
    """
    if not board or not board[0] or not word:
        return []
    
    m, n = len(board), len(board[0])
    all_paths = []
    
    def dfs(i: int, j: int, index: int, path: List[Tuple[int, int]]) -> None:
        if index == len(word):
            all_paths.append(path[:])
            return
        
        if (i < 0 or i >= m or j < 0 or j >= n or
            board[i][j] != word[index]):
            return
        
        temp = board[i][j]
        board[i][j] = '#'
        path.append((i, j))
        
        for di, dj in [(0, 1), (0, -1), (1, 0), (-1, 0)]:
            dfs(i + di, j + dj, index + 1, path)
        
        path.pop()
        board[i][j] = temp
    
    for i in range(m):
        for j in range(n):
            dfs(i, j, 0, [])
    
    return all_paths


class TrieNode:
    """Trie node for multi-word search."""
    
    def __init__(self):
        self.children = {}
        self.word = None  # Store complete word at end


class WordSearcher:
    """
    Advanced word searcher with Trie support.
    """
    
    def __init__(self, board: List[List[str]]):
        """
        Initialize with board.
        
        >>> ws = WordSearcher([['A','B'],['C','D']])
        >>> ws.m, ws.n
        (2, 2)
        """
        self.board = board
        self.m = len(board) if board else 0
        self.n = len(board[0]) if self.m > 0 else 0
        self.directions = [(0, 1), (0, -1), (1, 0), (-1, 0)]
    
    def search(self, word: str) -> bool:
        """Search for single word."""
        return word_search_optimized(
            [row[:] for row in self.board],  # Copy
            word
        )
    
    def search_multiple(self, words: List[str]) -> List[str]:
        """
        Search for multiple words using Trie.
        
        >>> ws = WordSearcher([['o','a','t'],['e','t','h']])
        >>> sorted(ws.search_multiple(['eat','oath','oat']))
        ['eat', 'oat', 'oath']
        """
        if not self.board or not self.m or not words:
            return []
        
        # Build Trie
        root = TrieNode()
        for word in words:
            node = root
            for char in word:
                if char not in node.children:
                    node.children[char] = TrieNode()
                node = node.children[char]
            node.word = word
        
        found = []
        board = [row[:] for row in self.board]  # Copy
        
        def dfs(i: int, j: int, node: TrieNode) -> None:
            if i < 0 or i >= self.m or j < 0 or j >= self.n:
                return
            
            char = board[i][j]
            if char == '#' or char not in node.children:
                return
            
            node = node.children[char]
            
            if node.word:
                found.append(node.word)
                node.word = None  # Avoid duplicates
            
            board[i][j] = '#'
            
            for di, dj in self.directions:
                dfs(i + di, j + dj, node)
            
            board[i][j] = char
        
        for i in range(self.m):
            for j in range(self.n):
                dfs(i, j, root)
        
        return found
    
    def find_longest_word(self, dictionary: Set[str]) -> Optional[str]:
        """
        Find longest word from dictionary that exists in board.
        """
        # Sort by length descending
        sorted_words = sorted(dictionary, key=len, reverse=True)
        
        for word in sorted_words:
            if self.search(word):
                return word
        
        return None
    
    def get_all_words(self, min_length: int = 3) -> Set[str]:
        """
        Find all possible words of minimum length.
        
        Uses DFS to generate all paths.
        """
        words = set()
        board = [row[:] for row in self.board]
        
        def dfs(i: int, j: int, path: str) -> None:
            if len(path) >= min_length:
                words.add(path)
            
            if len(path) >= 15:  # Limit path length
                return
            
            for di, dj in self.directions:
                ni, nj = i + di, j + dj
                if (0 <= ni < self.m and 0 <= nj < self.n and
                    board[ni][nj] != '#'):
                    char = board[ni][nj]
                    board[ni][nj] = '#'
                    dfs(ni, nj, path + char)
                    board[ni][nj] = char
        
        for i in range(self.m):
            for j in range(self.n):
                char = board[i][j]
                board[i][j] = '#'
                dfs(i, j, char)
                board[i][j] = char
        
        return words


# Demo
if __name__ == "__main__":
    print("Word Search Demo")
    print("=" * 50)
    
    board = [
        ['A', 'B', 'C', 'E'],
        ['S', 'F', 'C', 'S'],
        ['A', 'D', 'E', 'E']
    ]
    
    print("\n1. Board:")
    for row in board:
        print("  ", row)
    
    # Single word search
    print("\n2. Single Word Search:")
    words = ['ABCCED', 'SEE', 'ABCB', 'ABCD']
    for word in words:
        result = word_search([row[:] for row in board], word)
        print(f"   '{word}': {result}")
    
    # Optimized search
    print("\n3. Optimized Search:")
    result = word_search_optimized([row[:] for row in board], 'ABCCED')
    print(f"   'ABCCED': {result}")
    
    # Find all positions
    print("\n4. Find All Positions:")
    small_board = [['A', 'A'], ['A', 'A']]
    paths = find_word_positions(small_board, 'AA')
    print(f"   'AA' in 2x2 grid of A's: {len(paths)} paths")
    
    # Multi-word search
    print("\n5. Multi-Word Search (Trie-based):")
    ws = WordSearcher(board)
    found = ws.search_multiple(['ABCCED', 'SEE', 'ABCB', 'SAD', 'FCS'])
    print(f"   Found: {found}")
    
    # All possible words
    print("\n6. All Possible Words (length >= 3):")
    all_words = ws.get_all_words(min_length=3)
    print(f"   Found {len(all_words)} possible paths")
    sample = list(all_words)[:10]
    print(f"   Sample: {sample}")
```

## 8. Applications

### 8.1 Common Use Cases

- **Word games**: Boggle, Word Search puzzles
- **Spell checking**: Finding valid words
- **DNA sequencing**: Pattern matching in sequences
- **Image recognition**: Pattern finding in pixel grids
- **Code search**: Finding patterns in code matrices

### 8.2 Related Problems

| Problem | Description |
|---------|-------------|
| Boggle | Find all dictionary words in grid |
| Crossword Solver | Fill crossword with valid words |
| DNA Alignment | Sequence pattern matching |
| Image Segmentation | Pattern recognition in images |

## 9. Real-World Software Engineering Applications

### 9.1 Production Example: Crossword Puzzle Generator

```python
from typing import List, Set, Dict, Optional, Tuple
from dataclasses import dataclass, field
from enum import Enum
from collections import defaultdict
import random


class Direction(Enum):
    ACROSS = "across"
    DOWN = "down"


@dataclass
class WordPlacement:
    """Represents a word placed in the crossword."""
    word: str
    row: int
    col: int
    direction: Direction
    clue: str = ""


@dataclass
class CrosswordCell:
    """Single cell in crossword grid."""
    letter: Optional[str] = None
    is_black: bool = False
    number: Optional[int] = None
    across_word: Optional[str] = None
    down_word: Optional[str] = None


class CrosswordGrid:
    """
    Crossword puzzle grid with word placement.
    """
    
    def __init__(self, rows: int, cols: int):
        self.rows = rows
        self.cols = cols
        self.grid: List[List[CrosswordCell]] = [
            [CrosswordCell() for _ in range(cols)] for _ in range(rows)
        ]
        self.placements: List[WordPlacement] = []
    
    def set_black(self, row: int, col: int):
        """Mark cell as black (blocked)."""
        if 0 <= row < self.rows and 0 <= col < self.cols:
            self.grid[row][col].is_black = True
    
    def can_place_word(
        self,
        word: str,
        row: int,
        col: int,
        direction: Direction
    ) -> bool:
        """
        Check if word can be placed at position.
        """
        if direction == Direction.ACROSS:
            if col + len(word) > self.cols:
                return False
            # Check cell before word is empty/black
            if col > 0 and self.grid[row][col - 1].letter is not None:
                return False
            # Check cell after word is empty/black
            if col + len(word) < self.cols and self.grid[row][col + len(word)].letter is not None:
                return False
        else:
            if row + len(word) > self.rows:
                return False
            if row > 0 and self.grid[row - 1][col].letter is not None:
                return False
            if row + len(word) < self.rows and self.grid[row + len(word)][col].letter is not None:
                return False
        
        # Check each cell
        for i, char in enumerate(word):
            if direction == Direction.ACROSS:
                r, c = row, col + i
            else:
                r, c = row + i, col
            
            cell = self.grid[r][c]
            
            if cell.is_black:
                return False
            
            if cell.letter is not None and cell.letter != char:
                return False
        
        return True
    
    def place_word(
        self,
        word: str,
        row: int,
        col: int,
        direction: Direction,
        clue: str = ""
    ) -> bool:
        """
        Place word on grid.
        """
        if not self.can_place_word(word, row, col, direction):
            return False
        
        for i, char in enumerate(word):
            if direction == Direction.ACROSS:
                r, c = row, col + i
                self.grid[r][c].letter = char
                self.grid[r][c].across_word = word
            else:
                r, c = row + i, col
                self.grid[r][c].letter = char
                self.grid[r][c].down_word = word
        
        self.placements.append(WordPlacement(word, row, col, direction, clue))
        return True
    
    def remove_word(self, word: str) -> bool:
        """Remove placed word."""
        for placement in self.placements:
            if placement.word == word:
                # Clear cells (only if no crossing word)
                for i in range(len(word)):
                    if placement.direction == Direction.ACROSS:
                        r, c = placement.row, placement.col + i
                        self.grid[r][c].across_word = None
                        if self.grid[r][c].down_word is None:
                            self.grid[r][c].letter = None
                    else:
                        r, c = placement.row + i, placement.col
                        self.grid[r][c].down_word = None
                        if self.grid[r][c].across_word is None:
                            self.grid[r][c].letter = None
                
                self.placements.remove(placement)
                return True
        return False
    
    def display(self) -> str:
        """Display grid as string."""
        lines = []
        for row in self.grid:
            line = []
            for cell in row:
                if cell.is_black:
                    line.append("█")
                elif cell.letter:
                    line.append(cell.letter)
                else:
                    line.append("·")
            lines.append(" ".join(line))
        return "\n".join(lines)


class CrosswordGenerator:
    """
    Generate crossword puzzles from word list.
    
    Uses backtracking to find valid word placements.
    """
    
    def __init__(self, rows: int, cols: int, dictionary: List[str]):
        self.rows = rows
        self.cols = cols
        self.dictionary = sorted(dictionary, key=len, reverse=True)
        
        # Index words by length and pattern
        self.by_length: Dict[int, List[str]] = defaultdict(list)
        for word in dictionary:
            self.by_length[len(word)].append(word.upper())
    
    def generate(
        self,
        min_words: int = 5,
        max_attempts: int = 1000
    ) -> Optional[CrosswordGrid]:
        """
        Generate crossword puzzle.
        """
        for _ in range(max_attempts):
            grid = CrosswordGrid(self.rows, self.cols)
            placed = self._generate_recursive(grid, 0, set(), min_words)
            
            if placed >= min_words:
                self._number_cells(grid)
                return grid
        
        return None
    
    def _generate_recursive(
        self,
        grid: CrosswordGrid,
        depth: int,
        used_words: Set[str],
        target: int
    ) -> int:
        """Recursive backtracking for word placement."""
        if len(used_words) >= target:
            return len(used_words)
        
        if depth > 50:  # Limit recursion
            return len(used_words)
        
        # Find potential placements
        positions = self._find_placement_positions(grid)
        
        for row, col, direction, length in positions:
            candidates = self._get_matching_words(
                grid, row, col, direction, length, used_words
            )
            
            random.shuffle(candidates)
            
            for word in candidates[:3]:  # Try top 3 candidates
                if grid.place_word(word, row, col, direction):
                    used_words.add(word)
                    
                    result = self._generate_recursive(
                        grid, depth + 1, used_words, target
                    )
                    
                    if result >= target:
                        return result
                    
                    grid.remove_word(word)
                    used_words.remove(word)
        
        return len(used_words)
    
    def _find_placement_positions(
        self,
        grid: CrosswordGrid
    ) -> List[Tuple[int, int, Direction, int]]:
        """Find potential word placement positions."""
        positions = []
        
        # Find empty rows/columns or partial fills
        for r in range(self.rows):
            for c in range(self.cols):
                # Across
                if c == 0 or grid.grid[r][c - 1].is_black:
                    length = self._count_available(grid, r, c, Direction.ACROSS)
                    if length >= 3:
                        positions.append((r, c, Direction.ACROSS, length))
                
                # Down
                if r == 0 or grid.grid[r - 1][c].is_black:
                    length = self._count_available(grid, r, c, Direction.DOWN)
                    if length >= 3:
                        positions.append((r, c, Direction.DOWN, length))
        
        return positions
    
    def _count_available(
        self,
        grid: CrosswordGrid,
        row: int,
        col: int,
        direction: Direction
    ) -> int:
        """Count available cells in direction."""
        count = 0
        
        while True:
            if direction == Direction.ACROSS:
                if col + count >= self.cols:
                    break
                if grid.grid[row][col + count].is_black:
                    break
            else:
                if row + count >= self.rows:
                    break
                if grid.grid[row + count][col].is_black:
                    break
            count += 1
        
        return count
    
    def _get_matching_words(
        self,
        grid: CrosswordGrid,
        row: int,
        col: int,
        direction: Direction,
        length: int,
        used: Set[str]
    ) -> List[str]:
        """Get words that match current pattern."""
        candidates = []
        
        for word_len in range(min(length, max(self.by_length.keys())), 2, -1):
            for word in self.by_length.get(word_len, []):
                if word in used:
                    continue
                
                if grid.can_place_word(word, row, col, direction):
                    candidates.append(word)
        
        return candidates
    
    def _number_cells(self, grid: CrosswordGrid):
        """Add numbers to cells that start words."""
        number = 1
        
        for r in range(self.rows):
            for c in range(self.cols):
                cell = grid.grid[r][c]
                
                if cell.is_black or cell.letter is None:
                    continue
                
                starts_across = (c == 0 or grid.grid[r][c - 1].is_black or 
                                grid.grid[r][c - 1].letter is None)
                starts_down = (r == 0 or grid.grid[r - 1][c].is_black or
                              grid.grid[r - 1][c].letter is None)
                
                if starts_across or starts_down:
                    cell.number = number
                    number += 1


class WordGameSolver:
    """
    Solves word games like Boggle.
    
    Uses Trie for efficient multi-word search.
    """
    
    def __init__(self, dictionary: Set[str]):
        self.dictionary = dictionary
        self.root = TrieNode()
        
        for word in dictionary:
            self._add_to_trie(word.upper())
    
    def _add_to_trie(self, word: str):
        """Add word to Trie."""
        node = self.root
        for char in word:
            if char not in node.children:
                node.children[char] = TrieNode()
            node = node.children[char]
        node.word = word
    
    def solve_boggle(
        self,
        board: List[List[str]],
        min_length: int = 3
    ) -> List[str]:
        """
        Find all dictionary words in Boggle grid.
        
        Allows 8-directional movement.
        """
        if not board or not board[0]:
            return []
        
        m, n = len(board), len(board[0])
        found = set()
        
        # 8 directions including diagonals
        directions = [
            (-1, -1), (-1, 0), (-1, 1),
            (0, -1),          (0, 1),
            (1, -1), (1, 0), (1, 1)
        ]
        
        def dfs(i: int, j: int, node: TrieNode, visited: Set[Tuple[int, int]]):
            char = board[i][j].upper()
            
            if char not in node.children:
                return
            
            node = node.children[char]
            
            if node.word and len(node.word) >= min_length:
                found.add(node.word)
            
            visited.add((i, j))
            
            for di, dj in directions:
                ni, nj = i + di, j + dj
                if (0 <= ni < m and 0 <= nj < n and 
                    (ni, nj) not in visited):
                    dfs(ni, nj, node, visited)
            
            visited.remove((i, j))
        
        for i in range(m):
            for j in range(n):
                dfs(i, j, self.root, set())
        
        return sorted(found, key=lambda x: (-len(x), x))
    
    def calculate_score(self, words: List[str]) -> int:
        """
        Calculate Boggle score.
        
        Scoring:
        - 3-4 letters: 1 point
        - 5 letters: 2 points
        - 6 letters: 3 points
        - 7 letters: 5 points
        - 8+ letters: 11 points
        """
        score = 0
        for word in words:
            length = len(word)
            if length <= 4:
                score += 1
            elif length == 5:
                score += 2
            elif length == 6:
                score += 3
            elif length == 7:
                score += 5
            else:
                score += 11
        return score


# Demo
if __name__ == "__main__":
    print("Word Search Applications Demo")
    print("=" * 50)
    
    # Crossword Generator
    print("\n1. Crossword Generator:")
    dictionary = [
        "PYTHON", "JAVA", "CODE", "DATA", "ALGORITHM",
        "ARRAY", "TREE", "GRAPH", "NODE", "STACK",
        "QUEUE", "HASH", "SORT", "SEARCH", "LIST"
    ]
    
    generator = CrosswordGenerator(10, 10, dictionary)
    crossword = generator.generate(min_words=3, max_attempts=100)
    
    if crossword:
        print("   Generated crossword:")
        print(crossword.display())
        print(f"\n   Words placed: {len(crossword.placements)}")
        for p in crossword.placements:
            print(f"   - {p.word} at ({p.row},{p.col}) {p.direction.value}")
    else:
        print("   Could not generate crossword")
    
    # Boggle Solver
    print("\n2. Boggle Solver:")
    boggle_board = [
        ['T', 'H', 'E'],
        ['Q', 'U', 'I'],
        ['C', 'K', 'S']
    ]
    
    # Simple dictionary for demo
    boggle_dict = {
        "THE", "THUS", "THIS", "HIS", "SIT", "QUIT", "QUITE",
        "QUICK", "QUICKS", "HUE", "SUE", "USE"
    }
    
    solver = WordGameSolver(boggle_dict)
    words = solver.solve_boggle(boggle_board)
    score = solver.calculate_score(words)
    
    print("   Board:")
    for row in boggle_board:
        print("   ", row)
    print(f"\n   Found words: {words}")
    print(f"   Score: {score}")
```

## 10. Comparison

| Algorithm | Time | Space | Use Case |
|-----------|------|-------|----------|
| Basic DFS | O(m×n×4^L) | O(L) | Single word |
| Trie-based | O(m×n×4^L) | O(W) | Multiple words |
| BFS | O(m×n×L) | O(m×n) | Shortest path |

W = total characters in word list

## 11. Best Practices

1. **Use Trie** for multiple word searches
2. **Frequency pruning** reduces search space
3. **Start from rarer character** end
4. **Mark visited in-place** to save memory
5. **Prune early** on character mismatch

## 12. References

- [LeetCode: Word Search](https://leetcode.com/problems/word-search/)
- [LeetCode: Word Search II](https://leetcode.com/problems/word-search-ii/)
- Cormen, T. et al. "Introduction to Algorithms" - String Matching
