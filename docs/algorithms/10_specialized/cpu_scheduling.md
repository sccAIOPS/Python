# CPU Scheduling Algorithms

## Overview
- **Category**: Operating Systems / Process Management
- **Complexity**: Time: O(n) to O(n log n) | Space: O(n)
- **Type**: Scheduling, resource allocation
- **Source Files**: [scheduling/](../../../scheduling/)

## 1. Mathematical Foundation

### 1.1 Scheduling Metrics

**Turnaround Time:**
$$
T_{turnaround} = T_{completion} - T_{arrival}
$$

**Waiting Time:**
$$
T_{wait} = T_{turnaround} - T_{burst}
$$

**Response Time:**
$$
T_{response} = T_{first\_run} - T_{arrival}
$$

### 1.2 Average Metrics

$$
\bar{T}_{wait} = \frac{1}{n}\sum_{i=1}^{n} T_{wait}^{(i)}
$$

$$
\bar{T}_{turnaround} = \frac{1}{n}\sum_{i=1}^{n} T_{turnaround}^{(i)}
$$

### 1.3 CPU Utilization

$$
U_{CPU} = \frac{\sum T_{burst}}{\sum T_{burst} + T_{idle}}
$$

### 1.4 Throughput

$$
Throughput = \frac{n}{T_{total}}
$$

Where $n$ = number of processes, $T_{total}$ = total time.

### 1.5 Scheduling Criteria

| Criterion | Goal |
|-----------|------|
| CPU Utilization | Maximize |
| Throughput | Maximize |
| Turnaround Time | Minimize |
| Waiting Time | Minimize |
| Response Time | Minimize |

## 2. Algorithm Categories

### 2.1 Non-Preemptive
- **FCFS**: First-Come, First-Served
- **SJF**: Shortest Job First
- **Priority**: Static priority scheduling

### 2.2 Preemptive
- **SRTF**: Shortest Remaining Time First
- **Round Robin**: Time quantum rotation
- **MLFQ**: Multi-Level Feedback Queue
- **Priority Preemptive**: Dynamic priority

### 2.3 Real-Time
- **EDF**: Earliest Deadline First
- **Rate Monotonic**: Fixed priority by period
- **LLF**: Least Laxity First

## 3. Pseudocode

### 3.1 First-Come, First-Served (FCFS)

```
ALGORITHM FCFS(processes)
    INPUT: List of processes with arrival_time, burst_time
    OUTPUT: Scheduling order and metrics
    
    Sort processes by arrival_time
    current_time ← 0
    
    for each process p in processes do
        if current_time < p.arrival_time then
            current_time ← p.arrival_time  // CPU idle
        
        p.start_time ← current_time
        p.completion_time ← current_time + p.burst_time
        current_time ← p.completion_time
        
        p.turnaround_time ← p.completion_time - p.arrival_time
        p.waiting_time ← p.turnaround_time - p.burst_time
    
    return processes
```

### 3.2 Shortest Job First (SJF)

```
ALGORITHM SJF(processes)
    INPUT: List of processes with arrival_time, burst_time
    OUTPUT: Scheduling order and metrics
    
    current_time ← 0
    ready_queue ← []
    completed ← 0
    
    while completed < n do
        // Add arrived processes to ready queue
        for each unscheduled p where p.arrival_time ≤ current_time do
            ready_queue.add(p)
        
        if ready_queue is empty then
            current_time ← next arrival time
            continue
        
        // Select shortest job
        p ← process with minimum burst_time in ready_queue
        ready_queue.remove(p)
        
        p.start_time ← current_time
        current_time ← current_time + p.burst_time
        p.completion_time ← current_time
        completed ← completed + 1
    
    return processes
```

### 3.3 Round Robin

```
ALGORITHM RoundRobin(processes, quantum)
    INPUT: Processes, time quantum
    OUTPUT: Scheduling order and metrics
    
    ready_queue ← Queue()
    current_time ← 0
    
    // Add first arrived processes
    for each p where p.arrival_time = 0 do
        ready_queue.enqueue(p)
        p.remaining_time ← p.burst_time
    
    while ready_queue is not empty do
        p ← ready_queue.dequeue()
        
        run_time ← min(quantum, p.remaining_time)
        current_time ← current_time + run_time
        p.remaining_time ← p.remaining_time - run_time
        
        // Add newly arrived processes
        for each unqueued q where q.arrival_time ≤ current_time do
            ready_queue.enqueue(q)
        
        if p.remaining_time > 0 then
            ready_queue.enqueue(p)
        else
            p.completion_time ← current_time
    
    return processes
```

### 3.4 Shortest Remaining Time First (SRTF)

```
ALGORITHM SRTF(processes)
    INPUT: Processes with arrival_time, burst_time
    OUTPUT: Scheduling order
    
    current_time ← 0
    completed ← 0
    
    while completed < n do
        // Get processes that have arrived
        available ← {p : p.arrival_time ≤ current_time AND not completed}
        
        if available is empty then
            current_time ← min(p.arrival_time for unfinished p)
            continue
        
        // Select process with shortest remaining time
        p ← argmin(p.remaining_time for p in available)
        
        // Run until completion or new arrival
        next_arrival ← min arrival time > current_time
        run_until ← min(current_time + p.remaining_time, next_arrival)
        
        p.remaining_time -= (run_until - current_time)
        current_time ← run_until
        
        if p.remaining_time = 0 then
            p.completion_time ← current_time
            completed ← completed + 1
    
    return processes
```

### 3.5 Priority Scheduling

```
ALGORITHM PriorityScheduling(processes, preemptive)
    INPUT: Processes with priority (lower = higher priority)
    OUTPUT: Scheduling order
    
    if preemptive then
        Use priority queue, preempt on higher priority arrival
    else
        Use priority queue, run to completion
    
    ready_queue ← PriorityQueue()  // Min-heap by priority
    
    while not all completed do
        current ← ready_queue.extractMin()
        
        if preemptive then
            next_event ← min(arrival of higher priority, completion)
            run until next_event
            if not completed then
                ready_queue.insert(current)
        else
            run current to completion
    
    return processes
```

## 4. Step-by-Step Example

### Example 1: FCFS

```
Processes:
  P1: arrival=0, burst=5
  P2: arrival=1, burst=3
  P3: arrival=2, burst=8
  P4: arrival=3, burst=2

Timeline:
  0----5--------8--------------16--------18
  |P1  |   P2   |      P3      |   P4    |
  
Results:
  P1: start=0,  end=5,  wait=0,  turnaround=5
  P2: start=5,  end=8,  wait=4,  turnaround=7
  P3: start=8,  end=16, wait=6,  turnaround=14
  P4: start=16, end=18, wait=13, turnaround=15

Average wait: (0+4+6+13)/4 = 5.75
Average turnaround: (5+7+14+15)/4 = 10.25
```

### Example 2: SJF (Non-preemptive)

```
Same processes:
  P1: arrival=0, burst=5
  P2: arrival=1, burst=3
  P3: arrival=2, burst=8
  P4: arrival=3, burst=2

Timeline:
  0----5---7-----10--------------18
  |P1  |P4 | P2  |      P3      |
  
Order: P1→P4→P2→P3 (select shortest at each decision point)

Results:
  P1: start=0,  end=5,  wait=0,  turnaround=5
  P4: start=5,  end=7,  wait=2,  turnaround=4
  P2: start=7,  end=10, wait=6,  turnaround=9
  P3: start=10, end=18, wait=8,  turnaround=16

Average wait: (0+6+8+2)/4 = 4.0
Average turnaround: (5+9+16+4)/4 = 8.5
```

### Example 3: Round Robin (quantum=2)

```
Processes (all arrive at 0):
  P1: burst=5
  P2: burst=3
  P3: burst=8

Timeline:
  0--2--4--6--8--10-12-14-16
  |P1|P2|P3|P1|P2|P3|P1|P3|P3|...
  
Execution trace:
  t=0-2:   P1 runs (remaining=3)
  t=2-4:   P2 runs (remaining=1)
  t=4-6:   P3 runs (remaining=6)
  t=6-8:   P1 runs (remaining=1)
  t=8-9:   P2 runs (remaining=0) ✓
  t=9-11:  P3 runs (remaining=4)
  t=11-12: P1 runs (remaining=0) ✓
  t=12-14: P3 runs (remaining=2)
  t=14-16: P3 runs (remaining=0) ✓

Completion: P2@9, P1@12, P3@16
```

## 5. Complexity Analysis

| Algorithm | Time | Space | Preemptive |
|-----------|------|-------|------------|
| FCFS | O(n log n) | O(n) | No |
| SJF | O(n²) | O(n) | No |
| SRTF | O(n log n) | O(n) | Yes |
| Round Robin | O(n × total/q) | O(n) | Yes |
| Priority | O(n log n) | O(n) | Optional |
| MLFQ | O(n log n) | O(n) | Yes |

## 6. Visual Representation

### 6.1 Gantt Chart

```
FCFS Example:
Time: 0  1  2  3  4  5  6  7  8  9  10 11 12
      |--P1---|--P2--|-----P3-----|--P4--|
      ████████▓▓▓▓▓▓░░░░░░░░░░░░░▒▒▒▒▒▒
      
Legend: █ P1  ▓ P2  ░ P3  ▒ P4
```

### 6.2 Round Robin Queue

```
Initial: [P1, P2, P3] → Running: P1 (quantum=2)

After t=2:
  [P2, P3, P1] → Running: P2

After t=4:
  [P3, P1, P2] → Running: P3

...rotates until all complete
```

### 6.3 MLFQ Structure

```
Queue 0 (Highest Priority, quantum=8ms):
  [New processes enter here]
         ↓ (if uses full quantum)
         
Queue 1 (Medium Priority, quantum=16ms):
  [Demoted processes]
         ↓ (if uses full quantum)
         
Queue 2 (Lowest Priority, FCFS):
  [Long-running processes]
```

## 7. Implementation

```python
from typing import List, Dict, Optional, Tuple
from dataclasses import dataclass, field
from enum import Enum
import heapq
from collections import deque


@dataclass
class Process:
    """Represents a process/task."""
    pid: int
    arrival_time: float
    burst_time: float
    priority: int = 0
    
    # Computed at runtime
    remaining_time: float = field(init=False)
    start_time: float = field(default=-1, init=False)
    completion_time: float = field(default=-1, init=False)
    
    def __post_init__(self):
        self.remaining_time = self.burst_time
    
    @property
    def waiting_time(self) -> float:
        if self.completion_time < 0:
            return -1
        return self.turnaround_time - self.burst_time
    
    @property
    def turnaround_time(self) -> float:
        if self.completion_time < 0:
            return -1
        return self.completion_time - self.arrival_time
    
    @property
    def response_time(self) -> float:
        if self.start_time < 0:
            return -1
        return self.start_time - self.arrival_time


@dataclass
class SchedulingResult:
    """Result of scheduling simulation."""
    processes: List[Process]
    schedule: List[Tuple[int, float, float]]  # (pid, start, end)
    
    @property
    def avg_waiting_time(self) -> float:
        times = [p.waiting_time for p in self.processes 
                 if p.waiting_time >= 0]
        return sum(times) / len(times) if times else 0
    
    @property
    def avg_turnaround_time(self) -> float:
        times = [p.turnaround_time for p in self.processes 
                 if p.turnaround_time >= 0]
        return sum(times) / len(times) if times else 0
    
    @property
    def avg_response_time(self) -> float:
        times = [p.response_time for p in self.processes 
                 if p.response_time >= 0]
        return sum(times) / len(times) if times else 0
    
    @property
    def throughput(self) -> float:
        if not self.schedule:
            return 0
        total_time = self.schedule[-1][2]
        return len(self.processes) / total_time


def fcfs(processes: List[Process]) -> SchedulingResult:
    """
    First-Come, First-Served scheduling.
    
    >>> p1 = Process(1, 0, 5)
    >>> p2 = Process(2, 1, 3)
    >>> result = fcfs([p1, p2])
    >>> result.avg_waiting_time
    2.0
    """
    # Sort by arrival time
    sorted_procs = sorted(processes, key=lambda p: p.arrival_time)
    schedule = []
    current_time = 0
    
    for p in sorted_procs:
        # Handle idle time
        if current_time < p.arrival_time:
            current_time = p.arrival_time
        
        p.start_time = current_time
        p.completion_time = current_time + p.burst_time
        schedule.append((p.pid, p.start_time, p.completion_time))
        current_time = p.completion_time
    
    return SchedulingResult(sorted_procs, schedule)


def sjf(processes: List[Process]) -> SchedulingResult:
    """
    Shortest Job First (non-preemptive).
    
    >>> p1 = Process(1, 0, 5)
    >>> p2 = Process(2, 1, 3)
    >>> p3 = Process(3, 2, 2)
    >>> result = sjf([p1, p2, p3])
    >>> [p.pid for p in result.processes]
    [1, 3, 2]
    """
    n = len(processes)
    current_time = 0
    completed = 0
    scheduled = [False] * n
    schedule = []
    result_order = []
    
    while completed < n:
        # Find available processes
        available = [
            (i, p) for i, p in enumerate(processes)
            if not scheduled[i] and p.arrival_time <= current_time
        ]
        
        if not available:
            # Jump to next arrival
            next_arrival = min(
                p.arrival_time for i, p in enumerate(processes)
                if not scheduled[i]
            )
            current_time = next_arrival
            continue
        
        # Select shortest job
        idx, proc = min(available, key=lambda x: x[1].burst_time)
        
        proc.start_time = current_time
        proc.completion_time = current_time + proc.burst_time
        schedule.append((proc.pid, proc.start_time, proc.completion_time))
        
        current_time = proc.completion_time
        scheduled[idx] = True
        completed += 1
        result_order.append(proc)
    
    return SchedulingResult(result_order, schedule)


def srtf(processes: List[Process]) -> SchedulingResult:
    """
    Shortest Remaining Time First (preemptive SJF).
    
    >>> p1 = Process(1, 0, 5)
    >>> p2 = Process(2, 1, 3)
    >>> result = srtf([p1, p2])
    >>> result.processes[0].completion_time  # P2 finishes first
    4.0
    """
    n = len(processes)
    # Reset remaining times
    for p in processes:
        p.remaining_time = p.burst_time
    
    current_time = 0
    completed = 0
    schedule = []
    last_pid = -1
    last_start = 0
    
    while completed < n:
        # Find process with shortest remaining time
        available = [
            p for p in processes
            if p.arrival_time <= current_time and p.remaining_time > 0
        ]
        
        if not available:
            next_arrival = min(
                p.arrival_time for p in processes
                if p.remaining_time > 0
            )
            if last_pid != -1:
                schedule.append((last_pid, last_start, current_time))
                last_pid = -1
            current_time = next_arrival
            continue
        
        current = min(available, key=lambda p: p.remaining_time)
        
        # Record first start
        if current.start_time < 0:
            current.start_time = current_time
        
        # Record schedule segment
        if current.pid != last_pid:
            if last_pid != -1:
                schedule.append((last_pid, last_start, current_time))
            last_pid = current.pid
            last_start = current_time
        
        # Find next event (arrival or completion)
        next_arrival = min(
            (p.arrival_time for p in processes
             if p.arrival_time > current_time and p.remaining_time > 0),
            default=float('inf')
        )
        run_until = min(
            current_time + current.remaining_time,
            next_arrival
        )
        
        current.remaining_time -= (run_until - current_time)
        current_time = run_until
        
        if current.remaining_time == 0:
            current.completion_time = current_time
            completed += 1
    
    if last_pid != -1:
        schedule.append((last_pid, last_start, current_time))
    
    return SchedulingResult(
        sorted(processes, key=lambda p: p.completion_time),
        schedule
    )


def round_robin(
    processes: List[Process],
    quantum: float = 2
) -> SchedulingResult:
    """
    Round Robin scheduling.
    
    >>> p1 = Process(1, 0, 5)
    >>> p2 = Process(2, 0, 3)
    >>> result = round_robin([p1, p2], quantum=2)
    >>> len(result.schedule) > 2  # Multiple time slices
    True
    """
    # Reset remaining times
    for p in processes:
        p.remaining_time = p.burst_time
    
    # Sort by arrival for initial queue
    sorted_procs = sorted(processes, key=lambda p: p.arrival_time)
    
    current_time = 0
    ready_queue = deque()
    schedule = []
    
    # Add initial processes
    i = 0
    while i < len(sorted_procs) and sorted_procs[i].arrival_time <= 0:
        ready_queue.append(sorted_procs[i])
        i += 1
    
    while ready_queue or i < len(sorted_procs):
        if not ready_queue:
            # CPU idle, jump to next arrival
            current_time = sorted_procs[i].arrival_time
            while i < len(sorted_procs) and \
                  sorted_procs[i].arrival_time <= current_time:
                ready_queue.append(sorted_procs[i])
                i += 1
        
        if not ready_queue:
            break
        
        current = ready_queue.popleft()
        
        if current.start_time < 0:
            current.start_time = current_time
        
        # Run for quantum or until completion
        run_time = min(quantum, current.remaining_time)
        start = current_time
        current_time += run_time
        current.remaining_time -= run_time
        
        schedule.append((current.pid, start, current_time))
        
        # Add newly arrived processes
        while i < len(sorted_procs) and \
              sorted_procs[i].arrival_time <= current_time:
            ready_queue.append(sorted_procs[i])
            i += 1
        
        # Re-add current if not complete
        if current.remaining_time > 0:
            ready_queue.append(current)
        else:
            current.completion_time = current_time
    
    return SchedulingResult(processes, schedule)


def priority_scheduling(
    processes: List[Process],
    preemptive: bool = False
) -> SchedulingResult:
    """
    Priority scheduling (lower number = higher priority).
    
    >>> p1 = Process(1, 0, 5, priority=2)
    >>> p2 = Process(2, 0, 3, priority=1)
    >>> result = priority_scheduling([p1, p2])
    >>> result.schedule[0][0]  # P2 runs first (higher priority)
    2
    """
    for p in processes:
        p.remaining_time = p.burst_time
    
    if preemptive:
        return _priority_preemptive(processes)
    else:
        return _priority_non_preemptive(processes)


def _priority_non_preemptive(processes: List[Process]) -> SchedulingResult:
    """Non-preemptive priority scheduling."""
    n = len(processes)
    current_time = 0
    completed = 0
    scheduled = [False] * n
    schedule = []
    result_order = []
    
    while completed < n:
        available = [
            (i, p) for i, p in enumerate(processes)
            if not scheduled[i] and p.arrival_time <= current_time
        ]
        
        if not available:
            next_arrival = min(
                p.arrival_time for i, p in enumerate(processes)
                if not scheduled[i]
            )
            current_time = next_arrival
            continue
        
        # Select highest priority (lowest number)
        idx, proc = min(available, key=lambda x: x[1].priority)
        
        proc.start_time = current_time
        proc.completion_time = current_time + proc.burst_time
        schedule.append((proc.pid, proc.start_time, proc.completion_time))
        
        current_time = proc.completion_time
        scheduled[idx] = True
        completed += 1
        result_order.append(proc)
    
    return SchedulingResult(result_order, schedule)


def _priority_preemptive(processes: List[Process]) -> SchedulingResult:
    """Preemptive priority scheduling."""
    n = len(processes)
    current_time = 0
    completed = 0
    schedule = []
    last_pid = -1
    last_start = 0
    
    while completed < n:
        available = [
            p for p in processes
            if p.arrival_time <= current_time and p.remaining_time > 0
        ]
        
        if not available:
            next_arrival = min(
                p.arrival_time for p in processes
                if p.remaining_time > 0
            )
            if last_pid != -1:
                schedule.append((last_pid, last_start, current_time))
                last_pid = -1
            current_time = next_arrival
            continue
        
        current = min(available, key=lambda p: p.priority)
        
        if current.start_time < 0:
            current.start_time = current_time
        
        if current.pid != last_pid:
            if last_pid != -1:
                schedule.append((last_pid, last_start, current_time))
            last_pid = current.pid
            last_start = current_time
        
        # Run until next event
        next_arrival = min(
            (p.arrival_time for p in processes
             if p.arrival_time > current_time and p.remaining_time > 0),
            default=float('inf')
        )
        run_until = min(
            current_time + current.remaining_time,
            next_arrival
        )
        
        current.remaining_time -= (run_until - current_time)
        current_time = run_until
        
        if current.remaining_time == 0:
            current.completion_time = current_time
            completed += 1
    
    if last_pid != -1:
        schedule.append((last_pid, last_start, current_time))
    
    return SchedulingResult(
        sorted(processes, key=lambda p: p.completion_time),
        schedule
    )


# Demo
if __name__ == "__main__":
    print("CPU Scheduling Algorithms Demo")
    print("=" * 50)
    
    # Create test processes
    def create_processes():
        return [
            Process(1, 0, 5),
            Process(2, 1, 3),
            Process(3, 2, 8),
            Process(4, 3, 2)
        ]
    
    # FCFS
    print("\n1. FCFS:")
    result = fcfs(create_processes())
    print(f"   Order: {[s[0] for s in result.schedule]}")
    print(f"   Avg Wait: {result.avg_waiting_time:.2f}")
    print(f"   Avg Turnaround: {result.avg_turnaround_time:.2f}")
    
    # SJF
    print("\n2. SJF (Non-preemptive):")
    result = sjf(create_processes())
    print(f"   Order: {[s[0] for s in result.schedule]}")
    print(f"   Avg Wait: {result.avg_waiting_time:.2f}")
    print(f"   Avg Turnaround: {result.avg_turnaround_time:.2f}")
    
    # SRTF
    print("\n3. SRTF (Preemptive):")
    result = srtf(create_processes())
    print(f"   Schedule: {[(s[0], f'{s[1]:.0f}-{s[2]:.0f}') for s in result.schedule]}")
    print(f"   Avg Wait: {result.avg_waiting_time:.2f}")
    
    # Round Robin
    print("\n4. Round Robin (quantum=2):")
    result = round_robin(create_processes(), quantum=2)
    print(f"   Schedule: {[(s[0], f'{s[1]:.0f}-{s[2]:.0f}') for s in result.schedule]}")
    print(f"   Avg Wait: {result.avg_waiting_time:.2f}")
    
    # Priority
    print("\n5. Priority Scheduling:")
    procs = [
        Process(1, 0, 5, priority=3),
        Process(2, 1, 3, priority=1),
        Process(3, 2, 8, priority=4),
        Process(4, 3, 2, priority=2)
    ]
    result = priority_scheduling(procs)
    print(f"   Order: {[s[0] for s in result.schedule]}")
    print(f"   Avg Wait: {result.avg_waiting_time:.2f}")
```

## 8. Applications

### 8.1 Operating Systems
- Process scheduling in Linux, Windows
- Thread scheduling
- I/O scheduling

### 8.2 Cloud Computing
- VM scheduling
- Container orchestration (Kubernetes)
- Serverless function scheduling

### 8.3 Real-Time Systems
- Embedded systems
- Industrial control
- Multimedia streaming

## 9. Real-World Software Engineering Applications

### 9.1 Production Example: Task Scheduler System

```python
from typing import Dict, List, Optional, Callable
from dataclasses import dataclass, field
from datetime import datetime, timedelta
from enum import Enum
from collections import deque
import threading
import time
import heapq


class TaskPriority(Enum):
    CRITICAL = 0
    HIGH = 1
    NORMAL = 2
    LOW = 3
    BACKGROUND = 4


class TaskStatus(Enum):
    PENDING = "pending"
    RUNNING = "running"
    COMPLETED = "completed"
    FAILED = "failed"
    CANCELLED = "cancelled"


@dataclass
class Task:
    """A schedulable task."""
    id: str
    name: str
    handler: Callable
    args: tuple = ()
    kwargs: dict = field(default_factory=dict)
    priority: TaskPriority = TaskPriority.NORMAL
    estimated_duration: float = 1.0
    deadline: Optional[datetime] = None
    
    # Runtime state
    status: TaskStatus = TaskStatus.PENDING
    submitted_at: datetime = field(default_factory=datetime.now)
    started_at: Optional[datetime] = None
    completed_at: Optional[datetime] = None
    result: Optional[any] = None
    error: Optional[str] = None
    
    def __lt__(self, other):
        """For heap comparison."""
        return self.priority.value < other.priority.value


class MLFQScheduler:
    """
    Multi-Level Feedback Queue Scheduler.
    
    Production-grade task scheduler with multiple priority queues.
    """
    
    def __init__(
        self,
        num_queues: int = 4,
        base_quantum: float = 0.1,
        boost_interval: float = 10.0
    ):
        """
        Initialize MLFQ scheduler.
        
        Args:
            num_queues: Number of priority queues
            base_quantum: Time quantum for highest priority queue
            boost_interval: Interval for priority boost
        """
        self.num_queues = num_queues
        self.base_quantum = base_quantum
        self.boost_interval = boost_interval
        
        # Priority queues (0 = highest priority)
        self.queues: List[deque] = [deque() for _ in range(num_queues)]
        self.quantums = [base_quantum * (2 ** i) for i in range(num_queues)]
        
        # Task tracking
        self.task_levels: Dict[str, int] = {}  # task_id -> queue level
        self.tasks: Dict[str, Task] = {}
        
        # Statistics
        self.completed_count = 0
        self.total_wait_time = 0.0
        
        # Thread safety
        self._lock = threading.Lock()
        self._running = False
        self._last_boost = time.time()
    
    def submit(self, task: Task) -> str:
        """Submit a task for scheduling."""
        with self._lock:
            self.tasks[task.id] = task
            
            # Determine initial queue based on priority
            if task.priority == TaskPriority.CRITICAL:
                level = 0
            elif task.priority == TaskPriority.HIGH:
                level = 0
            elif task.priority == TaskPriority.NORMAL:
                level = 1
            else:
                level = min(2, self.num_queues - 1)
            
            self.queues[level].append(task)
            self.task_levels[task.id] = level
            
        return task.id
    
    def _select_next_task(self) -> Optional[Task]:
        """Select next task from highest non-empty queue."""
        for level, queue in enumerate(self.queues):
            if queue:
                return queue.popleft()
        return None
    
    def _demote_task(self, task: Task):
        """Move task to lower priority queue."""
        current_level = self.task_levels.get(task.id, 0)
        new_level = min(current_level + 1, self.num_queues - 1)
        
        self.task_levels[task.id] = new_level
        self.queues[new_level].append(task)
    
    def _boost_all_tasks(self):
        """Move all tasks to highest priority queue."""
        with self._lock:
            all_tasks = []
            for queue in self.queues:
                all_tasks.extend(queue)
                queue.clear()
            
            for task in all_tasks:
                self.queues[0].append(task)
                self.task_levels[task.id] = 0
    
    def run_one(self) -> Optional[Task]:
        """Execute one scheduling cycle."""
        # Check for priority boost
        if time.time() - self._last_boost >= self.boost_interval:
            self._boost_all_tasks()
            self._last_boost = time.time()
        
        with self._lock:
            task = self._select_next_task()
            if not task:
                return None
            
            level = self.task_levels.get(task.id, 0)
            quantum = self.quantums[level]
        
        # Execute task
        task.status = TaskStatus.RUNNING
        task.started_at = datetime.now()
        
        start_time = time.time()
        try:
            task.result = task.handler(*task.args, **task.kwargs)
            task.status = TaskStatus.COMPLETED
        except Exception as e:
            task.error = str(e)
            task.status = TaskStatus.FAILED
        
        elapsed = time.time() - start_time
        task.completed_at = datetime.now()
        
        # Update statistics
        wait_time = (task.started_at - task.submitted_at).total_seconds()
        self.total_wait_time += wait_time
        self.completed_count += 1
        
        # Check if task used full quantum (simulated for real tasks)
        if elapsed >= quantum and task.status != TaskStatus.COMPLETED:
            self._demote_task(task)
        
        return task
    
    def get_stats(self) -> Dict:
        """Get scheduler statistics."""
        queue_sizes = [len(q) for q in self.queues]
        avg_wait = (self.total_wait_time / self.completed_count 
                   if self.completed_count else 0)
        
        return {
            "queue_sizes": queue_sizes,
            "total_tasks": len(self.tasks),
            "completed": self.completed_count,
            "avg_wait_time": avg_wait
        }


class DeadlineScheduler:
    """
    Earliest Deadline First (EDF) Scheduler.
    
    For real-time systems with deadline requirements.
    """
    
    def __init__(self):
        self._heap: List[Tuple[datetime, Task]] = []
        self._tasks: Dict[str, Task] = {}
        self._lock = threading.Lock()
    
    def submit(self, task: Task) -> bool:
        """
        Submit task with deadline.
        
        Returns True if deadline is feasible.
        """
        if not task.deadline:
            task.deadline = datetime.now() + timedelta(hours=1)
        
        with self._lock:
            # Check feasibility (simplified)
            now = datetime.now()
            if task.deadline < now:
                return False  # Already missed
            
            heapq.heappush(self._heap, (task.deadline, task))
            self._tasks[task.id] = task
        
        return True
    
    def run_one(self) -> Optional[Task]:
        """Execute task with earliest deadline."""
        with self._lock:
            if not self._heap:
                return None
            
            deadline, task = heapq.heappop(self._heap)
        
        now = datetime.now()
        if deadline < now:
            task.status = TaskStatus.FAILED
            task.error = "Deadline missed"
            return task
        
        # Execute
        task.status = TaskStatus.RUNNING
        task.started_at = datetime.now()
        
        try:
            task.result = task.handler(*task.args, **task.kwargs)
            task.status = TaskStatus.COMPLETED
        except Exception as e:
            task.error = str(e)
            task.status = TaskStatus.FAILED
        
        task.completed_at = datetime.now()
        return task
    
    def get_feasibility(self) -> float:
        """Calculate system utilization."""
        total_util = 0
        for deadline, task in self._heap:
            if task.deadline:
                period = (task.deadline - datetime.now()).total_seconds()
                if period > 0:
                    total_util += task.estimated_duration / period
        
        return total_util  # Should be <= 1 for feasibility


class TaskPool:
    """
    Worker pool with configurable scheduling.
    """
    
    def __init__(
        self,
        num_workers: int = 4,
        scheduler_type: str = "mlfq"
    ):
        self.num_workers = num_workers
        
        if scheduler_type == "mlfq":
            self.scheduler = MLFQScheduler()
        elif scheduler_type == "edf":
            self.scheduler = DeadlineScheduler()
        else:
            raise ValueError(f"Unknown scheduler: {scheduler_type}")
        
        self._workers: List[threading.Thread] = []
        self._running = False
    
    def submit(self, task: Task) -> str:
        """Submit task to pool."""
        return self.scheduler.submit(task)
    
    def start(self):
        """Start worker threads."""
        self._running = True
        
        for i in range(self.num_workers):
            worker = threading.Thread(
                target=self._worker_loop,
                name=f"Worker-{i}"
            )
            worker.daemon = True
            worker.start()
            self._workers.append(worker)
    
    def _worker_loop(self):
        """Worker thread main loop."""
        while self._running:
            task = self.scheduler.run_one()
            if not task:
                time.sleep(0.01)  # No work, brief pause
    
    def shutdown(self, wait: bool = True):
        """Stop the pool."""
        self._running = False
        if wait:
            for worker in self._workers:
                worker.join(timeout=1.0)


# Demo
if __name__ == "__main__":
    print("Task Scheduler System Demo")
    print("=" * 50)
    
    # Simple task handlers
    def compute_task(n: int) -> int:
        return sum(range(n))
    
    def io_task(delay: float) -> str:
        time.sleep(delay)
        return f"Slept for {delay}s"
    
    # MLFQ Demo
    print("\n1. MLFQ Scheduler:")
    mlfq = MLFQScheduler(num_queues=3)
    
    # Submit various priority tasks
    tasks = [
        Task("t1", "Critical", compute_task, (1000,), 
             priority=TaskPriority.CRITICAL),
        Task("t2", "High", compute_task, (500,), 
             priority=TaskPriority.HIGH),
        Task("t3", "Normal", compute_task, (100,), 
             priority=TaskPriority.NORMAL),
        Task("t4", "Low", compute_task, (50,), 
             priority=TaskPriority.LOW),
    ]
    
    for task in tasks:
        mlfq.submit(task)
    
    print("   Queue sizes:", [len(q) for q in mlfq.queues])
    
    # Execute all
    while any(mlfq.queues):
        result = mlfq.run_one()
        if result:
            print(f"   Completed: {result.name} (priority={result.priority.name})")
    
    print(f"   Stats: {mlfq.get_stats()}")
    
    # EDF Demo
    print("\n2. EDF Scheduler:")
    edf = DeadlineScheduler()
    
    now = datetime.now()
    deadline_tasks = [
        Task("d1", "Urgent", compute_task, (100,),
             deadline=now + timedelta(seconds=5)),
        Task("d2", "Soon", compute_task, (100,),
             deadline=now + timedelta(seconds=10)),
        Task("d3", "Later", compute_task, (100,),
             deadline=now + timedelta(seconds=15)),
    ]
    
    for task in deadline_tasks:
        feasible = edf.submit(task)
        print(f"   Submitted {task.name}: feasible={feasible}")
    
    print(f"   System utilization: {edf.get_feasibility():.2%}")
    
    # Execute in deadline order
    while edf._heap:
        result = edf.run_one()
        if result:
            print(f"   Completed: {result.name} (status={result.status.value})")
    
    # Task Pool Demo
    print("\n3. Task Pool with Workers:")
    pool = TaskPool(num_workers=2, scheduler_type="mlfq")
    pool.start()
    
    # Submit batch
    for i in range(5):
        task = Task(f"batch_{i}", f"Task-{i}", compute_task, (1000,))
        pool.submit(task)
    
    time.sleep(0.5)  # Let workers process
    pool.shutdown()
    
    print(f"   Pool stats: {pool.scheduler.get_stats()}")
```

## 10. Algorithm Comparison

| Algorithm | Fairness | Overhead | Starvation | Best For |
|-----------|----------|----------|------------|----------|
| FCFS | Poor | Low | No | Batch |
| SJF | Poor | Medium | Yes | Batch |
| SRTF | Medium | High | Yes | Interactive |
| RR | High | High | No | Time-sharing |
| Priority | Poor | Low | Yes | Real-time |
| MLFQ | High | High | No | General |

## 11. Best Practices

1. **Choose based on requirements**: Batch vs interactive
2. **Tune quantum carefully**: Too small = overhead, too large = poor response
3. **Implement aging**: Prevent starvation
4. **Monitor metrics**: Wait time, throughput, utilization
5. **Consider I/O**: CPU-bound vs I/O-bound processes

## 12. References

- [Wikipedia: Scheduling](https://en.wikipedia.org/wiki/Scheduling_(computing))
- Operating Systems: Three Easy Pieces (OSTEP)
- Tanenbaum, A. "Modern Operating Systems"
