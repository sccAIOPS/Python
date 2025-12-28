# Data Structure Implementation Patterns

## Scope
This prompt provides patterns and best practices for implementing data structures in TheAlgorithms/Python.

## Node Patterns

### Basic Node (Dataclass)

```python
from dataclasses import dataclass
from typing import Any

@dataclass
class Node:
    """
    A basic node for linked structures.
    
    >>> Node(10)
    Node(data=10, next=None)
    >>> node = Node(5, Node(10))
    >>> node.next.data
    10
    """
    data: Any
    next: "Node | None" = None
```

### Tree Node

```python
@dataclass
class TreeNode:
    """
    A node for binary tree structures.
    
    >>> TreeNode(10)
    TreeNode(data=10, left=None, right=None)
    """
    data: Any
    left: "TreeNode | None" = None
    right: "TreeNode | None" = None
    
    def __repr__(self) -> str:
        return f"TreeNode(data={self.data})"
```

### Graph Node

```python
@dataclass
class GraphNode:
    """
    A node for graph structures with adjacency list.
    
    >>> node = GraphNode("A")
    >>> node.add_neighbor(GraphNode("B"))
    """
    value: Any
    neighbors: list["GraphNode"] = None
    
    def __post_init__(self) -> None:
        if self.neighbors is None:
            self.neighbors = []
    
    def add_neighbor(self, node: "GraphNode") -> None:
        self.neighbors.append(node)
```

## Collection Patterns

### Stack Implementation

```python
class Stack:
    """
    LIFO Stack implementation.
    
    >>> stack = Stack()
    >>> stack.push(1)
    >>> stack.push(2)
    >>> stack.pop()
    2
    >>> stack.peek()
    1
    >>> stack.is_empty()
    False
    """
    
    def __init__(self) -> None:
        """Initialize empty stack."""
        self._items: list[Any] = []
    
    def push(self, item: Any) -> None:
        """Add item to top of stack."""
        self._items.append(item)
    
    def pop(self) -> Any:
        """Remove and return top item."""
        if self.is_empty():
            raise IndexError("pop from empty stack")
        return self._items.pop()
    
    def peek(self) -> Any:
        """Return top item without removing."""
        if self.is_empty():
            raise IndexError("peek at empty stack")
        return self._items[-1]
    
    def is_empty(self) -> bool:
        """Check if stack is empty."""
        return len(self._items) == 0
    
    def __len__(self) -> int:
        """Return number of items."""
        return len(self._items)
```

### Queue Implementation

```python
from collections import deque

class Queue:
    """
    FIFO Queue implementation using deque for O(1) operations.
    
    >>> queue = Queue()
    >>> queue.enqueue(1)
    >>> queue.enqueue(2)
    >>> queue.dequeue()
    1
    """
    
    def __init__(self) -> None:
        """Initialize empty queue."""
        self._items: deque[Any] = deque()
    
    def enqueue(self, item: Any) -> None:
        """Add item to back of queue."""
        self._items.append(item)
    
    def dequeue(self) -> Any:
        """Remove and return front item."""
        if self.is_empty():
            raise IndexError("dequeue from empty queue")
        return self._items.popleft()
    
    def front(self) -> Any:
        """Return front item without removing."""
        if self.is_empty():
            raise IndexError("front of empty queue")
        return self._items[0]
    
    def is_empty(self) -> bool:
        """Check if queue is empty."""
        return len(self._items) == 0
```

### Binary Search Tree

```python
class BinarySearchTree:
    """
    Binary Search Tree implementation.
    
    >>> bst = BinarySearchTree()
    >>> bst.insert(5)
    >>> bst.insert(3)
    >>> bst.insert(7)
    >>> bst.search(3)
    True
    >>> bst.search(10)
    False
    >>> list(bst.inorder())
    [3, 5, 7]
    """
    
    def __init__(self) -> None:
        """Initialize empty BST."""
        self._root: TreeNode | None = None
    
    def insert(self, value: Any) -> None:
        """Insert value into BST."""
        if self._root is None:
            self._root = TreeNode(value)
        else:
            self._insert_recursive(self._root, value)
    
    def _insert_recursive(self, node: TreeNode, value: Any) -> None:
        """Recursively insert value."""
        if value < node.data:
            if node.left is None:
                node.left = TreeNode(value)
            else:
                self._insert_recursive(node.left, value)
        else:
            if node.right is None:
                node.right = TreeNode(value)
            else:
                self._insert_recursive(node.right, value)
    
    def search(self, value: Any) -> bool:
        """Search for value in BST."""
        return self._search_recursive(self._root, value)
    
    def _search_recursive(self, node: TreeNode | None, value: Any) -> bool:
        """Recursively search for value."""
        if node is None:
            return False
        if value == node.data:
            return True
        if value < node.data:
            return self._search_recursive(node.left, value)
        return self._search_recursive(node.right, value)
    
    def inorder(self) -> Iterator[Any]:
        """Yield values in sorted order."""
        yield from self._inorder_recursive(self._root)
    
    def _inorder_recursive(self, node: TreeNode | None) -> Iterator[Any]:
        """Recursive inorder traversal."""
        if node is not None:
            yield from self._inorder_recursive(node.left)
            yield node.data
            yield from self._inorder_recursive(node.right)
```

## Common Operations

### Traversal Patterns

```python
# Iterative BFS
def bfs(root: TreeNode) -> Iterator[Any]:
    """Breadth-first traversal."""
    if root is None:
        return
    queue = deque([root])
    while queue:
        node = queue.popleft()
        yield node.data
        if node.left:
            queue.append(node.left)
        if node.right:
            queue.append(node.right)

# Iterative DFS (preorder)
def dfs_preorder(root: TreeNode) -> Iterator[Any]:
    """Depth-first preorder traversal."""
    if root is None:
        return
    stack = [root]
    while stack:
        node = stack.pop()
        yield node.data
        if node.right:
            stack.append(node.right)
        if node.left:
            stack.append(node.left)
```

### Size and Height

```python
def tree_size(root: TreeNode | None) -> int:
    """Count nodes in tree."""
    if root is None:
        return 0
    return 1 + tree_size(root.left) + tree_size(root.right)

def tree_height(root: TreeNode | None) -> int:
    """Calculate tree height."""
    if root is None:
        return -1  # Empty tree has height -1
    return 1 + max(tree_height(root.left), tree_height(root.right))
```

## Best Practices

### 1. Use Sentinel Nodes for Edge Cases
```python
# Linked list with sentinel for cleaner code
class LinkedList:
    def __init__(self) -> None:
        self._sentinel = Node(None)  # Dummy head
        self._size = 0
```

### 2. Lazy Deletion for Trees
```python
@dataclass
class LazyNode:
    data: Any
    deleted: bool = False  # Mark as deleted instead of removing
```

### 3. Parent Pointers When Needed
```python
@dataclass
class ParentedNode:
    data: Any
    parent: "ParentedNode | None" = None
    left: "ParentedNode | None" = None
    right: "ParentedNode | None" = None
```

### 4. Generic Types
```python
from typing import TypeVar, Generic

T = TypeVar("T")

class GenericStack(Generic[T]):
    def __init__(self) -> None:
        self._items: list[T] = []
    
    def push(self, item: T) -> None:
        self._items.append(item)
    
    def pop(self) -> T:
        return self._items.pop()
```
