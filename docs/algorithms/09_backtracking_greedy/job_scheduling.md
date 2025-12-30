# Job Scheduling with Deadlines

## Overview
- **Category**: Greedy Algorithm / Scheduling
- **Complexity**: Time: O(n²) or O(n log n) with DSU | Space: O(n)
- **Type**: Profit maximization with constraints
- **Source Files**: [greedy_methods/job_scheduling.py](../../../greedy_methods/job_scheduling.py)

## 1. Mathematical Foundation

### 1.1 Problem Definition

Given $n$ jobs with:
- Each job $i$ has profit $p_i$ and deadline $d_i$
- Each job takes 1 unit of time
- Only one job can be scheduled at a time

**Objective**: Maximize total profit while meeting deadlines

$$
\text{Maximize } \sum_{i \in S} p_i
$$

**Subject to**:
- Each job $i \in S$ is scheduled before its deadline $d_i$
- At most one job per time slot

### 1.2 Greedy Choice Property

**Theorem**: Scheduling jobs in decreasing order of profit gives optimal solution.

**Proof Sketch**:
- If a higher-profit job can fit, it should always be included
- If slots conflict, prefer higher-profit job

### 1.3 Feasibility Condition

A set $S$ of jobs is feasible if and only if there exists a valid schedule.

**Check**: Sort by deadlines and assign to latest available slot.

### 1.4 Exchange Argument

If optimal solution has job $j$ instead of more profitable job $i$ in a slot:
- Swapping $i$ for $j$ gives profit increase $p_i - p_j > 0$
- This contradicts optimality, so greedy is optimal

### 1.5 Time Slot Assignment

For job with deadline $d$:
- Try slots $d, d-1, d-2, ..., 1$
- Assign to first available slot
- Skip if no slot available

## 2. Algorithm Variants

### 2.1 Simple Greedy
O(n²) - try each slot for each job.

### 2.2 Disjoint Set Union (DSU)
O(n log n) - efficient slot finding using union-find.

### 2.3 With Job Dependencies
Topological sort + greedy selection.

### 2.4 Weighted Job Scheduling
DP for overlapping jobs with different durations.

## 3. Pseudocode

### 3.1 Simple Greedy Approach

```
ALGORITHM JobScheduling(jobs)
    INPUT: List of (job_id, deadline, profit)
    OUTPUT: Maximum profit and scheduled jobs
    
    // Sort by profit descending
    Sort jobs by profit descending
    
    // Find maximum deadline
    max_deadline ← max(deadline for all jobs)
    
    // Slot array (-1 means empty)
    slots ← array of size max_deadline, all -1
    
    total_profit ← 0
    scheduled ← []
    
    for each job in jobs do
        // Try to find slot from deadline down to 1
        for slot ← min(job.deadline, max_deadline) down to 1 do
            if slots[slot] = -1 then
                slots[slot] ← job.id
                total_profit ← total_profit + job.profit
                scheduled.append(job)
                break
    
    return (total_profit, scheduled)
```

### 3.2 DSU-Based Optimization

```
ALGORITHM JobSchedulingDSU(jobs)
    INPUT: List of jobs
    OUTPUT: Maximum profit and scheduled jobs
    
    Sort jobs by profit descending
    max_deadline ← max(deadline for all jobs)
    
    // DSU parent array
    parent ← [0, 1, 2, ..., max_deadline]
    
    FUNCTION Find(slot)
        if parent[slot] = slot then
            return slot
        parent[slot] ← Find(parent[slot])  // Path compression
        return parent[slot]
    
    total_profit ← 0
    scheduled ← []
    
    for each job in jobs do
        // Find available slot
        available_slot ← Find(min(job.deadline, max_deadline))
        
        if available_slot > 0 then
            // Schedule job in this slot
            scheduled.append(job)
            total_profit ← total_profit + job.profit
            
            // Union with previous slot
            parent[available_slot] ← Find(available_slot - 1)
    
    return (total_profit, scheduled)
```

### 3.3 Weighted Job Scheduling (DP)

```
ALGORITHM WeightedJobScheduling(jobs)
    INPUT: Jobs with (start, end, profit)
    OUTPUT: Maximum profit
    
    // Sort by end time
    Sort jobs by end_time
    
    n ← length(jobs)
    dp ← array of size n
    dp[0] ← jobs[0].profit
    
    for i ← 1 to n-1 do
        // Include current job
        include ← jobs[i].profit
        
        // Find latest non-conflicting job
        latest ← BinarySearchLatest(jobs, i)
        if latest ≠ -1 then
            include ← include + dp[latest]
        
        // Exclude current job
        exclude ← dp[i-1]
        
        dp[i] ← max(include, exclude)
    
    return dp[n-1]
```

## 4. Step-by-Step Example

### Example: 5 Jobs

```
Jobs: [(id, deadline, profit)]
  J1: (1, 2, 100)
  J2: (2, 1, 19)
  J3: (3, 2, 27)
  J4: (4, 1, 25)
  J5: (5, 3, 15)
```

### Execution

```
Step 1: Sort by profit descending
  J1: profit=100, deadline=2
  J3: profit=27, deadline=2
  J4: profit=25, deadline=1
  J2: profit=19, deadline=1
  J5: profit=15, deadline=3

Initial slots: [-, -, -]  (for deadlines 1, 2, 3)

Step 2: Schedule J1 (profit=100, deadline=2)
  Try slot 2: EMPTY → Schedule
  Slots: [-, J1, -]
  Profit: 100

Step 3: Schedule J3 (profit=27, deadline=2)
  Try slot 2: OCCUPIED (J1)
  Try slot 1: EMPTY → Schedule
  Slots: [J3, J1, -]
  Profit: 127

Step 4: Schedule J4 (profit=25, deadline=1)
  Try slot 1: OCCUPIED (J3)
  Cannot schedule J4

Step 5: Schedule J2 (profit=19, deadline=1)
  Try slot 1: OCCUPIED (J3)
  Cannot schedule J2

Step 6: Schedule J5 (profit=15, deadline=3)
  Try slot 3: EMPTY → Schedule
  Slots: [J3, J1, J5]
  Profit: 142

RESULT: Jobs {J1, J3, J5}, Profit = 142
```

### Visual Timeline

```
Time:    1      2      3
       ┌────┐ ┌────┐ ┌────┐
       │ J3 │ │ J1 │ │ J5 │
       │ 27 │ │100 │ │ 15 │
       └────┘ └────┘ └────┘
                        
Total Profit: 100 + 27 + 15 = 142
```

## 5. Complexity Analysis

| Approach | Time | Space |
|----------|------|-------|
| Simple Greedy | O(n²) | O(n) |
| DSU-based | O(n log n) | O(n) |
| Weighted DP | O(n log n) | O(n) |

Where n = number of jobs.

## 6. Visual Representation

### 6.1 Algorithm Flow

```
Jobs: J1(100,d=2) J3(27,d=2) J4(25,d=1) J2(19,d=1) J5(15,d=3)
      ↓ (sorted by profit)

┌─────────────────────────────────────────┐
│ Try J1: deadline=2, slot 2 free? YES    │
│ Schedule J1 at slot 2                   │
│ Slots: [_, J1, _]                       │
└───────────────────┬─────────────────────┘
                    ↓
┌─────────────────────────────────────────┐
│ Try J3: deadline=2, slot 2 free? NO     │
│         slot 1 free? YES                │
│ Schedule J3 at slot 1                   │
│ Slots: [J3, J1, _]                      │
└───────────────────┬─────────────────────┘
                    ↓
┌─────────────────────────────────────────┐
│ Try J4: deadline=1, slot 1 free? NO     │
│ Cannot schedule J4                      │
└───────────────────┬─────────────────────┘
                    ↓
┌─────────────────────────────────────────┐
│ Try J5: deadline=3, slot 3 free? YES    │
│ Schedule J5 at slot 3                   │
│ Slots: [J3, J1, J5]                     │
└───────────────────┬─────────────────────┘
                    ↓
        Result: Profit = 142
```

### 6.2 DSU Slot Finding

```
Initial parent: [0, 1, 2, 3]
                 ↓  ↓  ↓  ↓
Slots:          [0][1][2][3]

After J1 at slot 2:
parent: [0, 1, 1, 3]  (slot 2 → slot 1)
        
After J3 at slot 1:
parent: [0, 0, 1, 3]  (slot 1 → slot 0)

After J5 at slot 3:
parent: [0, 0, 1, 2]  (slot 3 → slot 2 → slot 1 → slot 0)
```

## 7. Implementation

```python
from typing import List, Tuple, Optional
from dataclasses import dataclass, field
from collections import defaultdict


@dataclass
class Job:
    """Represents a job with deadline and profit."""
    id: str
    deadline: int
    profit: int
    
    def __lt__(self, other):
        return self.profit > other.profit  # For max-heap


def job_scheduling_simple(jobs: List[Job]) -> Tuple[int, List[Job]]:
    """
    Simple greedy job scheduling.
    
    Time: O(n²), Space: O(n)
    
    >>> jobs = [Job('a', 2, 100), Job('b', 1, 19), Job('c', 2, 27)]
    >>> profit, scheduled = job_scheduling_simple(jobs)
    >>> profit
    127
    """
    if not jobs:
        return 0, []
    
    # Sort by profit descending
    sorted_jobs = sorted(jobs, key=lambda j: j.profit, reverse=True)
    
    # Find max deadline
    max_deadline = max(j.deadline for j in jobs)
    
    # Slot array (None = empty)
    slots = [None] * (max_deadline + 1)
    
    total_profit = 0
    scheduled = []
    
    for job in sorted_jobs:
        # Find slot from deadline down to 1
        for slot in range(min(job.deadline, max_deadline), 0, -1):
            if slots[slot] is None:
                slots[slot] = job
                total_profit += job.profit
                scheduled.append(job)
                break
    
    return total_profit, scheduled


class DSU:
    """Disjoint Set Union for efficient slot finding."""
    
    def __init__(self, n: int):
        self.parent = list(range(n + 1))
    
    def find(self, x: int) -> int:
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]
    
    def union(self, x: int, y: int):
        px, py = self.find(x), self.find(y)
        if px != py:
            self.parent[px] = py


def job_scheduling_dsu(jobs: List[Job]) -> Tuple[int, List[Job]]:
    """
    DSU-based job scheduling.
    
    Time: O(n log n), Space: O(n)
    
    >>> jobs = [Job('a', 2, 100), Job('b', 1, 19), Job('c', 2, 27)]
    >>> profit, scheduled = job_scheduling_dsu(jobs)
    >>> profit
    127
    """
    if not jobs:
        return 0, []
    
    sorted_jobs = sorted(jobs, key=lambda j: j.profit, reverse=True)
    max_deadline = max(j.deadline for j in jobs)
    
    dsu = DSU(max_deadline)
    
    total_profit = 0
    scheduled = []
    
    for job in sorted_jobs:
        available = dsu.find(min(job.deadline, max_deadline))
        
        if available > 0:
            scheduled.append(job)
            total_profit += job.profit
            dsu.union(available, available - 1)
    
    return total_profit, scheduled


def job_scheduling_with_duration(
    jobs: List[Tuple[int, int, int]]
) -> int:
    """
    Weighted job scheduling with variable durations.
    
    Args:
        jobs: List of (start, end, profit)
    
    Returns:
        Maximum profit
    
    >>> jobs = [(1, 2, 50), (3, 5, 20), (6, 19, 100), (2, 100, 200)]
    >>> job_scheduling_with_duration(jobs)
    250
    """
    if not jobs:
        return 0
    
    # Sort by end time
    sorted_jobs = sorted(jobs, key=lambda x: x[1])
    n = len(sorted_jobs)
    
    # dp[i] = max profit considering jobs 0..i
    dp = [0] * n
    dp[0] = sorted_jobs[0][2]
    
    def binary_search_latest(index: int) -> int:
        """Find latest non-conflicting job."""
        target_end = sorted_jobs[index][0]
        lo, hi = 0, index - 1
        result = -1
        
        while lo <= hi:
            mid = (lo + hi) // 2
            if sorted_jobs[mid][1] <= target_end:
                result = mid
                lo = mid + 1
            else:
                hi = mid - 1
        
        return result
    
    for i in range(1, n):
        # Include current job
        include = sorted_jobs[i][2]
        latest = binary_search_latest(i)
        if latest != -1:
            include += dp[latest]
        
        # Exclude current job
        exclude = dp[i - 1]
        
        dp[i] = max(include, exclude)
    
    return dp[n - 1]


@dataclass
class ScheduledJob:
    """Job with assigned time slot."""
    job: Job
    slot: int


class JobScheduler:
    """
    Comprehensive job scheduler.
    """
    
    def __init__(self):
        self.jobs: List[Job] = []
    
    def add_job(self, job_id: str, deadline: int, profit: int):
        """Add job to scheduler."""
        self.jobs.append(Job(job_id, deadline, profit))
    
    def schedule_greedy(self) -> Tuple[int, List[ScheduledJob]]:
        """
        Schedule using greedy approach.
        
        >>> scheduler = JobScheduler()
        >>> scheduler.add_job('a', 2, 100)
        >>> scheduler.add_job('b', 1, 50)
        >>> profit, scheduled = scheduler.schedule_greedy()
        >>> profit
        150
        """
        if not self.jobs:
            return 0, []
        
        sorted_jobs = sorted(self.jobs, key=lambda j: j.profit, reverse=True)
        max_deadline = max(j.deadline for j in self.jobs)
        
        slots = [None] * (max_deadline + 1)
        scheduled = []
        total_profit = 0
        
        for job in sorted_jobs:
            for slot in range(min(job.deadline, max_deadline), 0, -1):
                if slots[slot] is None:
                    slots[slot] = job
                    scheduled.append(ScheduledJob(job, slot))
                    total_profit += job.profit
                    break
        
        scheduled.sort(key=lambda x: x.slot)
        return total_profit, scheduled
    
    def schedule_earliest_deadline_first(self) -> List[ScheduledJob]:
        """
        Schedule using EDF (for real-time systems).
        """
        sorted_jobs = sorted(self.jobs, key=lambda j: j.deadline)
        
        scheduled = []
        current_time = 1
        
        for job in sorted_jobs:
            if current_time <= job.deadline:
                scheduled.append(ScheduledJob(job, current_time))
                current_time += 1
        
        return scheduled
    
    def get_schedule_timeline(
        self,
        scheduled: List[ScheduledJob]
    ) -> str:
        """Generate visual timeline."""
        if not scheduled:
            return "No jobs scheduled"
        
        max_slot = max(s.slot for s in scheduled)
        lines = []
        
        # Header
        header = "Time: " + " ".join(f"{i:^6}" for i in range(1, max_slot + 1))
        lines.append(header)
        
        # Schedule row
        slot_map = {s.slot: s.job for s in scheduled}
        row = "Jobs: "
        
        for i in range(1, max_slot + 1):
            if i in slot_map:
                job = slot_map[i]
                row += f"[{job.id:^4}]"
            else:
                row += f"[{'':^4}]"
        
        lines.append(row)
        
        return "\n".join(lines)


# Demo
if __name__ == "__main__":
    print("Job Scheduling Demo")
    print("=" * 50)
    
    # Example jobs
    jobs = [
        Job('J1', 2, 100),
        Job('J2', 1, 19),
        Job('J3', 2, 27),
        Job('J4', 1, 25),
        Job('J5', 3, 15)
    ]
    
    print("\n1. Jobs:")
    for job in jobs:
        print(f"   {job.id}: deadline={job.deadline}, profit={job.profit}")
    
    # Simple greedy
    print("\n2. Simple Greedy Scheduling:")
    profit, scheduled = job_scheduling_simple(jobs)
    print(f"   Maximum profit: {profit}")
    print(f"   Scheduled jobs: {[j.id for j in scheduled]}")
    
    # DSU-based
    print("\n3. DSU-Based Scheduling:")
    profit, scheduled = job_scheduling_dsu(jobs)
    print(f"   Maximum profit: {profit}")
    print(f"   Scheduled jobs: {[j.id for j in scheduled]}")
    
    # Weighted with duration
    print("\n4. Weighted Job Scheduling (variable duration):")
    weighted_jobs = [
        (1, 2, 50),   # Job from time 1-2, profit 50
        (3, 5, 20),   # Job from time 3-5, profit 20
        (6, 19, 100), # Job from time 6-19, profit 100
        (2, 100, 200) # Job from time 2-100, profit 200
    ]
    max_profit = job_scheduling_with_duration(weighted_jobs)
    print(f"   Maximum profit: {max_profit}")
    
    # JobScheduler class
    print("\n5. JobScheduler with Timeline:")
    scheduler = JobScheduler()
    for job in jobs:
        scheduler.add_job(job.id, job.deadline, job.profit)
    
    profit, scheduled = scheduler.schedule_greedy()
    print(f"   Maximum profit: {profit}")
    print("\n   Timeline:")
    print(scheduler.get_schedule_timeline(scheduled))
```

## 8. Applications

### 8.1 Common Use Cases

- **CPU scheduling**: Maximize throughput with deadlines
- **Project management**: Schedule tasks with deadlines
- **Manufacturing**: Optimize production schedules
- **Resource allocation**: Assign limited resources
- **Advertising**: Schedule ad slots for maximum revenue

### 8.2 Related Problems

| Problem | Description |
|---------|-------------|
| Interval Scheduling | Maximize non-overlapping intervals |
| Task Assignment | Assign tasks to workers |
| Knapsack | Select items with weight constraints |
| Flow Shop | Multi-machine scheduling |

## 9. Real-World Software Engineering Applications

### 9.1 Production Example: Cloud Task Scheduler

```python
from typing import Dict, List, Optional, Set, Tuple
from dataclasses import dataclass, field
from datetime import datetime, timedelta
from enum import Enum
from collections import defaultdict
import heapq
import threading
import time
import uuid


class TaskPriority(Enum):
    CRITICAL = 0
    HIGH = 1
    MEDIUM = 2
    LOW = 3


class TaskStatus(Enum):
    PENDING = "pending"
    SCHEDULED = "scheduled"
    RUNNING = "running"
    COMPLETED = "completed"
    FAILED = "failed"
    MISSED = "missed"


@dataclass
class CloudTask:
    """Represents a cloud computing task."""
    id: str
    name: str
    deadline: datetime
    estimated_duration: timedelta
    profit: float  # Revenue or priority value
    priority: TaskPriority = TaskPriority.MEDIUM
    dependencies: List[str] = field(default_factory=list)
    resource_requirements: Dict[str, int] = field(default_factory=dict)
    status: TaskStatus = TaskStatus.PENDING
    scheduled_time: Optional[datetime] = None
    completion_time: Optional[datetime] = None
    
    def __hash__(self):
        return hash(self.id)


@dataclass
class ResourcePool:
    """Pool of computing resources."""
    cpu_cores: int
    memory_gb: int
    gpu_count: int
    
    def can_accommodate(self, requirements: Dict[str, int]) -> bool:
        """Check if resources can accommodate task."""
        return (requirements.get('cpu', 0) <= self.cpu_cores and
                requirements.get('memory', 0) <= self.memory_gb and
                requirements.get('gpu', 0) <= self.gpu_count)
    
    def allocate(self, requirements: Dict[str, int]) -> bool:
        """Allocate resources."""
        if not self.can_accommodate(requirements):
            return False
        self.cpu_cores -= requirements.get('cpu', 0)
        self.memory_gb -= requirements.get('memory', 0)
        self.gpu_count -= requirements.get('gpu', 0)
        return True
    
    def release(self, requirements: Dict[str, int]):
        """Release resources."""
        self.cpu_cores += requirements.get('cpu', 0)
        self.memory_gb += requirements.get('memory', 0)
        self.gpu_count += requirements.get('gpu', 0)


@dataclass
class ScheduleSlot:
    """Time slot for task execution."""
    start_time: datetime
    end_time: datetime
    task: Optional[CloudTask] = None


class CloudTaskScheduler:
    """
    Production cloud task scheduler.
    
    Maximizes revenue/priority while meeting deadlines.
    """
    
    def __init__(self, resources: ResourcePool):
        self.resources = resources
        self.tasks: Dict[str, CloudTask] = {}
        self.schedule: List[ScheduleSlot] = []
        self.completed_tasks: List[CloudTask] = []
        self._lock = threading.Lock()
    
    def add_task(
        self,
        name: str,
        deadline: datetime,
        duration_minutes: int,
        profit: float,
        priority: TaskPriority = TaskPriority.MEDIUM,
        dependencies: List[str] = None,
        resources: Dict[str, int] = None
    ) -> str:
        """
        Add task to scheduler.
        """
        task_id = str(uuid.uuid4())[:8]
        
        task = CloudTask(
            id=task_id,
            name=name,
            deadline=deadline,
            estimated_duration=timedelta(minutes=duration_minutes),
            profit=profit,
            priority=priority,
            dependencies=dependencies or [],
            resource_requirements=resources or {'cpu': 1, 'memory': 1}
        )
        
        with self._lock:
            self.tasks[task_id] = task
        
        return task_id
    
    def schedule_tasks(self) -> Tuple[float, List[CloudTask]]:
        """
        Schedule all pending tasks.
        
        Uses profit-maximizing greedy with deadline constraints.
        """
        with self._lock:
            pending = [t for t in self.tasks.values() 
                      if t.status == TaskStatus.PENDING]
        
        if not pending:
            return 0.0, []
        
        # Check dependencies
        ready_tasks = self._get_ready_tasks(pending)
        
        # Sort by profit/priority (profit first, then priority)
        sorted_tasks = sorted(
            ready_tasks,
            key=lambda t: (-t.profit, t.priority.value, t.deadline)
        )
        
        # Schedule greedily
        current_time = datetime.now()
        scheduled = []
        total_profit = 0.0
        
        for task in sorted_tasks:
            slot_time = self._find_slot(task, current_time)
            
            if slot_time and slot_time + task.estimated_duration <= task.deadline:
                if self.resources.can_accommodate(task.resource_requirements):
                    task.scheduled_time = slot_time
                    task.status = TaskStatus.SCHEDULED
                    scheduled.append(task)
                    total_profit += task.profit
                    
                    self.schedule.append(ScheduleSlot(
                        start_time=slot_time,
                        end_time=slot_time + task.estimated_duration,
                        task=task
                    ))
        
        # Mark missed deadlines
        for task in sorted_tasks:
            if task.status == TaskStatus.PENDING:
                if datetime.now() > task.deadline:
                    task.status = TaskStatus.MISSED
        
        return total_profit, scheduled
    
    def _get_ready_tasks(self, tasks: List[CloudTask]) -> List[CloudTask]:
        """Get tasks with satisfied dependencies."""
        completed_ids = {t.id for t in self.completed_tasks}
        
        ready = []
        for task in tasks:
            if all(dep in completed_ids for dep in task.dependencies):
                ready.append(task)
        
        return ready
    
    def _find_slot(
        self,
        task: CloudTask,
        earliest_start: datetime
    ) -> Optional[datetime]:
        """Find earliest available slot for task."""
        # Simple implementation: find gap in schedule
        if not self.schedule:
            return earliest_start
        
        sorted_slots = sorted(self.schedule, key=lambda s: s.start_time)
        
        # Check before first slot
        if earliest_start + task.estimated_duration <= sorted_slots[0].start_time:
            return earliest_start
        
        # Check gaps between slots
        for i in range(len(sorted_slots) - 1):
            gap_start = sorted_slots[i].end_time
            gap_end = sorted_slots[i + 1].start_time
            
            potential_start = max(earliest_start, gap_start)
            if potential_start + task.estimated_duration <= gap_end:
                return potential_start
        
        # After last slot
        return max(earliest_start, sorted_slots[-1].end_time)
    
    def execute_task(self, task_id: str) -> bool:
        """Execute a scheduled task."""
        with self._lock:
            if task_id not in self.tasks:
                return False
            
            task = self.tasks[task_id]
            
            if task.status != TaskStatus.SCHEDULED:
                return False
            
            if not self.resources.allocate(task.resource_requirements):
                return False
            
            task.status = TaskStatus.RUNNING
        
        # Simulate execution (in production, this would be async)
        # time.sleep(task.estimated_duration.total_seconds())
        
        with self._lock:
            task.status = TaskStatus.COMPLETED
            task.completion_time = datetime.now()
            self.completed_tasks.append(task)
            self.resources.release(task.resource_requirements)
        
        return True
    
    def get_schedule_summary(self) -> Dict:
        """Get summary of current schedule."""
        with self._lock:
            status_counts = defaultdict(int)
            for task in self.tasks.values():
                status_counts[task.status.value] += 1
            
            total_profit = sum(
                t.profit for t in self.tasks.values()
                if t.status == TaskStatus.COMPLETED
            )
            
            return {
                'total_tasks': len(self.tasks),
                'status_breakdown': dict(status_counts),
                'total_profit': total_profit,
                'scheduled_slots': len(self.schedule)
            }


class BatchJobProcessor:
    """
    Batch job processor for data pipelines.
    
    Optimizes job ordering for throughput and latency.
    """
    
    def __init__(self, max_concurrent: int = 4):
        self.max_concurrent = max_concurrent
        self.jobs: Dict[str, CloudTask] = {}
        self.dependency_graph: Dict[str, Set[str]] = defaultdict(set)
    
    def add_batch_job(
        self,
        job_id: str,
        name: str,
        duration_minutes: int,
        priority: float,
        depends_on: List[str] = None
    ):
        """Add batch job with dependencies."""
        deadline = datetime.now() + timedelta(hours=24)  # Default 24h deadline
        
        task = CloudTask(
            id=job_id,
            name=name,
            deadline=deadline,
            estimated_duration=timedelta(minutes=duration_minutes),
            profit=priority,
            dependencies=depends_on or []
        )
        
        self.jobs[job_id] = task
        
        for dep in (depends_on or []):
            self.dependency_graph[dep].add(job_id)
    
    def get_execution_order(self) -> List[str]:
        """
        Get optimal execution order using topological sort + priority.
        """
        # Calculate in-degree for each job
        in_degree = {job_id: len(job.dependencies) 
                    for job_id, job in self.jobs.items()}
        
        # Priority queue: (-priority, job_id)
        ready = []
        for job_id, degree in in_degree.items():
            if degree == 0:
                job = self.jobs[job_id]
                heapq.heappush(ready, (-job.profit, job_id))
        
        order = []
        
        while ready:
            _, job_id = heapq.heappop(ready)
            order.append(job_id)
            
            # Update dependents
            for dependent in self.dependency_graph[job_id]:
                in_degree[dependent] -= 1
                if in_degree[dependent] == 0:
                    dep_job = self.jobs[dependent]
                    heapq.heappush(ready, (-dep_job.profit, dependent))
        
        # Check for cycles
        if len(order) != len(self.jobs):
            raise ValueError("Circular dependency detected")
        
        return order
    
    def estimate_completion_time(self) -> timedelta:
        """Estimate total completion time with parallelism."""
        order = self.get_execution_order()
        
        # Simulate parallel execution
        # Level-based parallelism
        levels = self._compute_levels()
        
        total_time = timedelta()
        
        for level_jobs in levels:
            # Jobs at same level can run in parallel
            max_duration = max(
                self.jobs[job_id].estimated_duration
                for job_id in level_jobs
            )
            total_time += max_duration
        
        return total_time
    
    def _compute_levels(self) -> List[List[str]]:
        """Compute dependency levels."""
        levels = []
        remaining = set(self.jobs.keys())
        completed = set()
        
        while remaining:
            # Find jobs with all dependencies satisfied
            level = []
            for job_id in remaining:
                job = self.jobs[job_id]
                if all(dep in completed for dep in job.dependencies):
                    level.append(job_id)
            
            if not level:
                raise ValueError("Circular dependency detected")
            
            levels.append(level)
            for job_id in level:
                remaining.remove(job_id)
                completed.add(job_id)
        
        return levels


# Demo
if __name__ == "__main__":
    print("Cloud Task Scheduler Demo")
    print("=" * 50)
    
    # Create scheduler
    resources = ResourcePool(cpu_cores=8, memory_gb=32, gpu_count=2)
    scheduler = CloudTaskScheduler(resources)
    
    # Add tasks
    now = datetime.now()
    
    scheduler.add_task(
        name="Data ETL",
        deadline=now + timedelta(hours=2),
        duration_minutes=30,
        profit=100.0,
        priority=TaskPriority.HIGH
    )
    
    scheduler.add_task(
        name="ML Training",
        deadline=now + timedelta(hours=4),
        duration_minutes=120,
        profit=500.0,
        priority=TaskPriority.CRITICAL,
        resources={'cpu': 4, 'memory': 16, 'gpu': 1}
    )
    
    scheduler.add_task(
        name="Report Generation",
        deadline=now + timedelta(hours=1),
        duration_minutes=15,
        profit=50.0,
        priority=TaskPriority.MEDIUM
    )
    
    scheduler.add_task(
        name="Backup",
        deadline=now + timedelta(hours=6),
        duration_minutes=60,
        profit=25.0,
        priority=TaskPriority.LOW
    )
    
    # Schedule
    print("\n1. Scheduling Tasks:")
    profit, scheduled = scheduler.schedule_tasks()
    
    print(f"   Total potential profit: {profit}")
    print(f"   Scheduled tasks:")
    for task in scheduled:
        print(f"   - {task.name}: ${task.profit}, deadline {task.deadline}")
    
    # Summary
    print("\n2. Schedule Summary:")
    summary = scheduler.get_schedule_summary()
    print(f"   Total tasks: {summary['total_tasks']}")
    print(f"   Status breakdown: {summary['status_breakdown']}")
    
    # Batch processor
    print("\n3. Batch Job Processor:")
    processor = BatchJobProcessor()
    
    processor.add_batch_job("extract", "Extract Data", 10, 1.0)
    processor.add_batch_job("transform", "Transform Data", 20, 2.0, ["extract"])
    processor.add_batch_job("load", "Load Data", 5, 1.5, ["transform"])
    processor.add_batch_job("validate", "Validate", 10, 3.0, ["load"])
    processor.add_batch_job("notify", "Send Notification", 2, 0.5, ["validate"])
    
    order = processor.get_execution_order()
    print(f"   Execution order: {order}")
    
    estimated = processor.estimate_completion_time()
    print(f"   Estimated completion: {estimated}")
```

## 10. Comparison

| Algorithm | Time | Optimality | Use Case |
|-----------|------|------------|----------|
| Greedy | O(n²) | Optimal | Unit jobs |
| DSU Greedy | O(n log n) | Optimal | Unit jobs |
| Weighted DP | O(n log n) | Optimal | Variable duration |
| EDF | O(n log n) | Optimal for EDF | Real-time |

## 11. Best Practices

1. **Sort by profit** for maximum revenue
2. **Use DSU** for large job counts
3. **Check dependencies** before scheduling
4. **Handle missed deadlines** gracefully
5. **Consider resource constraints** in real systems

## 12. References

- [Wikipedia: Job Shop Scheduling](https://en.wikipedia.org/wiki/Job_shop_scheduling)
- Pinedo, M. "Scheduling: Theory, Algorithms, and Systems"
- Cormen, T. et al. "Introduction to Algorithms" - Greedy Algorithms
