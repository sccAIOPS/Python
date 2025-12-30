# Activity Selection Problem

## Overview
- **Category**: Greedy Algorithm / Interval Scheduling
- **Complexity**: Time: O(n log n) | Space: O(n)
- **Type**: Optimal interval scheduling
- **Source Files**: [greedy_methods/activity_selection.py](../../../greedy_methods/activity_selection.py)

## 1. Mathematical Foundation

### 1.1 Problem Definition

Given n activities with start times $s_i$ and finish times $f_i$, select the **maximum number** of non-overlapping activities.

$$
\text{Maximize } |S| \text{ where } S \subseteq \{1, 2, ..., n\}
$$

Subject to: $\forall i, j \in S: f_i \leq s_j \text{ or } f_j \leq s_i$

### 1.2 Greedy Choice Property

**Theorem**: Always selecting the activity with the **earliest finish time** leads to an optimal solution.

**Proof sketch**:
1. Let $A$ be any optimal solution
2. Let $a_1$ be the activity with earliest finish time
3. If $a_1 \in A$, we're done
4. If $a_1 \notin A$, let $a_k$ be first activity in $A$
5. Since $f_1 \leq f_k$, we can replace $a_k$ with $a_1$
6. This gives another optimal solution containing $a_1$

### 1.3 Optimal Substructure

After selecting activity $a_i$ with finish time $f_i$:
- Subproblem: Select maximum activities with $s_j \geq f_i$
- Optimal solution to subproblem + $a_i$ = Optimal solution

### 1.4 Compatibility Relation

Activities $i$ and $j$ are **compatible** if:

$$
[s_i, f_i) \cap [s_j, f_j) = \emptyset
$$

Equivalently: $f_i \leq s_j$ or $f_j \leq s_i$

## 2. Algorithm Variants

### 2.1 Standard Activity Selection
Select maximum number of non-overlapping activities.

### 2.2 Weighted Activity Selection
Each activity has weight $w_i$; maximize total weight.
(Requires dynamic programming, not greedy.)

### 2.3 Interval Scheduling Maximization
Equivalent to activity selection.

### 2.4 Interval Partitioning
Minimum resources to schedule all activities.

## 3. Pseudocode

### 3.1 Greedy Activity Selection

```
ALGORITHM ActivitySelection(activities)
    INPUT: List of activities with (start, finish)
    OUTPUT: Maximum set of non-overlapping activities
    
    // Sort by finish time
    Sort activities by finish time
    
    selected ← [activities[0]]
    last_finish ← activities[0].finish
    
    for i ← 1 to n-1 do
        if activities[i].start >= last_finish then
            selected.append(activities[i])
            last_finish ← activities[i].finish
    
    return selected
```

### 3.2 Recursive Version

```
ALGORITHM RecursiveActivitySelection(activities, k, n)
    INPUT: activities sorted by finish time, current index k, total n
    OUTPUT: Maximum activities starting from k
    
    // Find first activity after k that's compatible
    m ← k + 1
    while m <= n AND activities[m].start < activities[k].finish do
        m ← m + 1
    
    if m <= n then
        return {activities[m]} ∪ RecursiveActivitySelection(activities, m, n)
    else
        return ∅
```

### 3.3 Interval Partitioning

```
ALGORITHM IntervalPartitioning(activities)
    INPUT: List of activities with (start, finish)
    OUTPUT: Minimum number of resources needed
    
    Sort activities by start time
    
    resources ← MinHeap()  // Track finish times
    
    for each activity in activities do
        if resources is not empty AND activity.start >= resources.min() then
            // Reuse resource
            resources.extractMin()
        
        resources.insert(activity.finish)
    
    return |resources|
```

## 4. Step-by-Step Example

### Example: Meeting Room Scheduling

**Input Activities:**
| Activity | Start | Finish |
|----------|-------|--------|
| A | 1 | 4 |
| B | 3 | 5 |
| C | 0 | 6 |
| D | 5 | 7 |
| E | 3 | 9 |
| F | 5 | 9 |
| G | 6 | 10 |
| H | 8 | 11 |
| I | 8 | 12 |
| J | 2 | 14 |

**Step 1: Sort by finish time**
| Activity | Start | Finish |
|----------|-------|--------|
| A | 1 | 4 |
| B | 3 | 5 |
| C | 0 | 6 |
| D | 5 | 7 |
| E | 3 | 9 |
| F | 5 | 9 |
| G | 6 | 10 |
| H | 8 | 11 |
| I | 8 | 12 |
| J | 2 | 14 |

**Step 2: Greedy selection**

```
Select A (finish=4), last_finish=4
B (start=3 < 4)  → Skip
C (start=0 < 4)  → Skip
D (start=5 >= 4) → Select D (finish=7), last_finish=7
E (start=3 < 7)  → Skip
F (start=5 < 7)  → Skip
G (start=6 < 7)  → Skip
H (start=8 >= 7) → Select H (finish=11), last_finish=11
I (start=8 < 11) → Skip
J (start=2 < 11) → Skip
```

**Result: {A, D, H}** - 3 activities (maximum possible)

### Visual Timeline

```
Time:  0  1  2  3  4  5  6  7  8  9  10 11 12 13 14
       |--|--|--|--|--|--|--|--|--|--|--|--|--|--|
A:        =========                              
B:              ======
C:     ==================
D:                 ======
E:              ==================
F:                 ============
G:                    ============
H:                       =========
I:                       ============
J:           ==========================

Selected:
A:        =========                              
D:                 ======
H:                       =========
```

## 5. Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Sorting | O(n log n) | O(n) |
| Selection | O(n) | O(1) |
| **Total** | O(n log n) | O(n) |

### If already sorted:
| Operation | Time | Space |
|-----------|------|-------|
| Selection | O(n) | O(1) |

## 6. Visual Representation

### 6.1 Algorithm Flow

```
┌─────────────────────────────────────────┐
│         Input Activities                │
│  [(1,4), (3,5), (0,6), (5,7), ...]     │
└────────────────┬────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────┐
│     Sort by Finish Time                 │
│  [(1,4), (3,5), (0,6), (5,7), ...]     │
└────────────────┬────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────┐
│     Select First Activity               │
│     selected = [(1,4)]                  │
│     last_finish = 4                     │
└────────────────┬────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────┐
│     Iterate & Select Compatible         │
│                                         │
│     (3,5): 3 < 4 → skip                │
│     (0,6): 0 < 4 → skip                │
│     (5,7): 5 >= 4 → SELECT             │
│     ...                                 │
└────────────────┬────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────┐
│     Result: [(1,4), (5,7), (8,11)]     │
│     Maximum: 3 activities               │
└─────────────────────────────────────────┘
```

### 6.2 Why Earliest Finish Works

```
Alternative: "Shortest duration first"
    [====]           (duration 4)
      [=]            (duration 1, but conflicts!)
        [====]       (duration 4)
    
    Greedy earliest finish:
    [====]           (finishes first)
          [====]     (next compatible)
                [==] (next compatible)
    → 3 activities

Compare to shortest:
      [=]            (shortest)
    [====]           (conflicts with [=])
        [====]       (also conflicts)
    → 1 activity

Earliest finish = optimal!
```

## 7. Implementation

```python
from typing import List, Tuple, Optional, NamedTuple
from dataclasses import dataclass
from functools import total_ordering
import heapq


@total_ordering
@dataclass
class Activity:
    """
    Represents an activity with start and finish times.
    
    >>> a = Activity("Meeting", 1, 4)
    >>> a.duration
    3
    """
    name: str
    start: int
    finish: int
    
    @property
    def duration(self) -> int:
        return self.finish - self.start
    
    def overlaps(self, other: 'Activity') -> bool:
        """Check if this activity overlaps with another."""
        return not (self.finish <= other.start or other.finish <= self.start)
    
    def __eq__(self, other):
        if not isinstance(other, Activity):
            return NotImplemented
        return self.finish == other.finish
    
    def __lt__(self, other):
        if not isinstance(other, Activity):
            return NotImplemented
        return self.finish < other.finish


class ActivitySelector:
    """
    Greedy activity selection algorithm.
    
    Selects maximum number of non-overlapping activities.
    """
    
    @staticmethod
    def select(activities: List[Activity]) -> List[Activity]:
        """
        Select maximum non-overlapping activities.
        
        >>> activities = [
        ...     Activity("A", 1, 4),
        ...     Activity("B", 3, 5),
        ...     Activity("C", 0, 6),
        ...     Activity("D", 5, 7),
        ...     Activity("E", 8, 11),
        ... ]
        >>> result = ActivitySelector.select(activities)
        >>> [a.name for a in result]
        ['A', 'D', 'E']
        """
        if not activities:
            return []
        
        # Sort by finish time
        sorted_activities = sorted(activities, key=lambda a: a.finish)
        
        selected = [sorted_activities[0]]
        last_finish = sorted_activities[0].finish
        
        for activity in sorted_activities[1:]:
            if activity.start >= last_finish:
                selected.append(activity)
                last_finish = activity.finish
        
        return selected
    
    @staticmethod
    def select_recursive(
        activities: List[Activity],
        k: int = -1
    ) -> List[Activity]:
        """
        Recursive activity selection.
        
        >>> activities = [
        ...     Activity("A", 1, 4),
        ...     Activity("B", 5, 7),
        ...     Activity("C", 8, 10),
        ... ]
        >>> sorted_acts = sorted(activities, key=lambda a: a.finish)
        >>> result = ActivitySelector.select_recursive(sorted_acts)
        >>> len(result)
        3
        """
        if not activities:
            return []
        
        # Find first compatible activity
        if k == -1:
            # Sort and start with first activity
            sorted_acts = sorted(activities, key=lambda a: a.finish)
            return [sorted_acts[0]] + ActivitySelector.select_recursive(
                sorted_acts, 0
            )
        
        last_finish = activities[k].finish
        
        # Find next compatible activity
        for i in range(k + 1, len(activities)):
            if activities[i].start >= last_finish:
                return [activities[i]] + ActivitySelector.select_recursive(
                    activities, i
                )
        
        return []
    
    @staticmethod
    def select_with_tuples(
        intervals: List[Tuple[int, int]]
    ) -> List[Tuple[int, int]]:
        """
        Select from simple (start, finish) tuples.
        
        >>> intervals = [(1, 4), (3, 5), (5, 7), (8, 10)]
        >>> ActivitySelector.select_with_tuples(intervals)
        [(1, 4), (5, 7), (8, 10)]
        """
        if not intervals:
            return []
        
        sorted_intervals = sorted(intervals, key=lambda x: x[1])
        
        selected = [sorted_intervals[0]]
        last_finish = sorted_intervals[0][1]
        
        for start, finish in sorted_intervals[1:]:
            if start >= last_finish:
                selected.append((start, finish))
                last_finish = finish
        
        return selected


class IntervalPartitioner:
    """
    Find minimum number of resources to schedule all activities.
    
    Also known as "classroom scheduling" or "minimum meeting rooms".
    """
    
    @staticmethod
    def min_resources(activities: List[Activity]) -> int:
        """
        Find minimum resources needed for all activities.
        
        >>> activities = [
        ...     Activity("A", 0, 30),
        ...     Activity("B", 5, 10),
        ...     Activity("C", 15, 20),
        ... ]
        >>> IntervalPartitioner.min_resources(activities)
        2
        """
        if not activities:
            return 0
        
        # Sort by start time
        sorted_activities = sorted(activities, key=lambda a: a.start)
        
        # Min-heap of finish times (representing resources)
        resources = []
        
        for activity in sorted_activities:
            # If a resource is free (finished), reuse it
            if resources and resources[0] <= activity.start:
                heapq.heappop(resources)
            
            # Add this activity's finish time
            heapq.heappush(resources, activity.finish)
        
        return len(resources)
    
    @staticmethod
    def partition(activities: List[Activity]) -> List[List[Activity]]:
        """
        Partition activities into minimum number of groups.
        
        >>> activities = [
        ...     Activity("A", 0, 30),
        ...     Activity("B", 5, 10),
        ...     Activity("C", 15, 20),
        ... ]
        >>> groups = IntervalPartitioner.partition(activities)
        >>> len(groups)
        2
        """
        if not activities:
            return []
        
        sorted_activities = sorted(activities, key=lambda a: a.start)
        
        # Each resource tracks its activities and finish time
        # (finish_time, resource_index)
        resources: List[Tuple[int, List[Activity]]] = []
        heap: List[Tuple[int, int]] = []  # (finish_time, resource_index)
        
        for activity in sorted_activities:
            if heap and heap[0][0] <= activity.start:
                # Reuse existing resource
                _, resource_idx = heapq.heappop(heap)
                resources[resource_idx][1].append(activity)
                heapq.heappush(heap, (activity.finish, resource_idx))
            else:
                # Create new resource
                resource_idx = len(resources)
                resources.append((activity.finish, [activity]))
                heapq.heappush(heap, (activity.finish, resource_idx))
        
        return [r[1] for r in resources]


class WeightedActivitySelector:
    """
    Weighted activity selection using dynamic programming.
    
    Maximizes total weight of selected activities.
    """
    
    @staticmethod
    def select(activities: List[Tuple[int, int, int]]) -> Tuple[int, List[int]]:
        """
        Select activities to maximize total weight.
        
        Args:
            activities: List of (start, finish, weight)
        
        Returns:
            Tuple of (max_weight, selected_indices)
        
        >>> activities = [(1, 2, 50), (3, 5, 20), (6, 19, 100), (2, 100, 200)]
        >>> weight, indices = WeightedActivitySelector.select(activities)
        >>> weight
        250
        """
        if not activities:
            return (0, [])
        
        n = len(activities)
        
        # Sort by finish time, keep original indices
        indexed = [(i, s, f, w) for i, (s, f, w) in enumerate(activities)]
        indexed.sort(key=lambda x: x[2])  # Sort by finish
        
        # Find latest non-conflicting activity for each
        def find_prev(j: int) -> int:
            """Find latest activity that doesn't conflict with j."""
            target_start = indexed[j][1]  # start of j
            for i in range(j - 1, -1, -1):
                if indexed[i][2] <= target_start:  # finish[i] <= start[j]
                    return i
            return -1
        
        # DP: dp[i] = max weight considering activities 0..i
        dp = [0] * n
        dp[0] = indexed[0][3]  # weight of first activity
        
        for i in range(1, n):
            # Option 1: Don't include activity i
            exclude = dp[i - 1]
            
            # Option 2: Include activity i
            include = indexed[i][3]  # weight of i
            prev = find_prev(i)
            if prev >= 0:
                include += dp[prev]
            
            dp[i] = max(exclude, include)
        
        # Backtrack to find selected activities
        selected = []
        i = n - 1
        while i >= 0:
            prev = find_prev(i)
            include = indexed[i][3] + (dp[prev] if prev >= 0 else 0)
            
            if i == 0 or include > dp[i - 1]:
                selected.append(indexed[i][0])  # original index
                i = prev
            else:
                i -= 1
        
        return (dp[n - 1], list(reversed(selected)))


def activity_selection(activities: List[Tuple[int, int]]) -> List[Tuple[int, int]]:
    """
    Convenience function for activity selection.
    
    >>> activity_selection([(1, 4), (3, 5), (5, 7), (8, 10)])
    [(1, 4), (5, 7), (8, 10)]
    """
    return ActivitySelector.select_with_tuples(activities)
```

## 8. Applications

### 8.1 Common Use Cases

- **Meeting room scheduling**: Maximize meetings in a room
- **CPU scheduling**: Schedule non-preemptive jobs
- **Resource allocation**: Assign limited resources
- **Event planning**: Schedule non-overlapping events
- **Classroom assignment**: Minimum rooms for classes

### 8.2 Related Problems

| Problem | Objective | Algorithm |
|---------|-----------|-----------|
| Activity Selection | Max activities | Greedy (finish time) |
| Interval Partitioning | Min resources | Greedy (start time) |
| Weighted Scheduling | Max weight | DP |
| Interval Graph Coloring | Min colors | Greedy |

## 9. Real-World Software Engineering Applications

### 9.1 Production Example: Meeting Room Scheduler

```python
from typing import Dict, List, Optional, Set
from dataclasses import dataclass, field
from datetime import datetime, time, timedelta
from enum import Enum
import heapq


class MeetingPriority(Enum):
    LOW = 1
    NORMAL = 2
    HIGH = 3
    URGENT = 4


@dataclass
class Meeting:
    """Represents a meeting request."""
    id: str
    title: str
    start: datetime
    end: datetime
    organizer: str
    attendees: List[str] = field(default_factory=list)
    priority: MeetingPriority = MeetingPriority.NORMAL
    room_requirements: Set[str] = field(default_factory=set)
    
    @property
    def duration_minutes(self) -> int:
        return int((self.end - self.start).total_seconds() / 60)
    
    def overlaps(self, other: 'Meeting') -> bool:
        return not (self.end <= other.start or other.end <= self.start)


@dataclass
class MeetingRoom:
    """Represents a meeting room."""
    id: str
    name: str
    capacity: int
    features: Set[str] = field(default_factory=set)
    
    def meets_requirements(self, requirements: Set[str]) -> bool:
        return requirements.issubset(self.features)


class MeetingScheduler:
    """
    Production meeting room scheduler using activity selection.
    
    Handles multiple rooms, priorities, and constraints.
    """
    
    def __init__(self, rooms: List[MeetingRoom]):
        self.rooms = {room.id: room for room in rooms}
        self.schedules: Dict[str, List[Meeting]] = {
            room.id: [] for room in rooms
        }
    
    def schedule_meetings(
        self,
        meetings: List[Meeting]
    ) -> Dict[str, List[Meeting]]:
        """
        Schedule meetings across rooms, maximizing acceptance.
        
        Returns mapping of room_id to scheduled meetings.
        """
        # Sort by priority (high first) then by end time (early first)
        sorted_meetings = sorted(
            meetings,
            key=lambda m: (-m.priority.value, m.end)
        )
        
        scheduled: Dict[str, List[Meeting]] = {
            room_id: [] for room_id in self.rooms
        }
        unscheduled: List[Meeting] = []
        
        for meeting in sorted_meetings:
            room_id = self._find_available_room(meeting, scheduled)
            
            if room_id:
                scheduled[room_id].append(meeting)
                # Keep sorted by start time
                scheduled[room_id].sort(key=lambda m: m.start)
            else:
                unscheduled.append(meeting)
        
        self.schedules = scheduled
        return scheduled
    
    def _find_available_room(
        self,
        meeting: Meeting,
        schedules: Dict[str, List[Meeting]]
    ) -> Optional[str]:
        """Find first available room for meeting."""
        for room_id, room in self.rooms.items():
            # Check room requirements
            if not room.meets_requirements(meeting.room_requirements):
                continue
            
            # Check capacity
            if room.capacity < len(meeting.attendees) + 1:
                continue
            
            # Check for conflicts
            room_schedule = schedules[room_id]
            if not any(meeting.overlaps(m) for m in room_schedule):
                return room_id
        
        return None
    
    def minimum_rooms_needed(self, meetings: List[Meeting]) -> int:
        """Calculate minimum rooms needed for all meetings."""
        if not meetings:
            return 0
        
        # Sort by start time
        sorted_meetings = sorted(meetings, key=lambda m: m.start)
        
        # Use heap to track room end times
        room_end_times: List[datetime] = []
        
        for meeting in sorted_meetings:
            if room_end_times and room_end_times[0] <= meeting.start:
                heapq.heappop(room_end_times)
            heapq.heappush(room_end_times, meeting.end)
        
        return len(room_end_times)
    
    def optimize_schedule(
        self,
        meetings: List[Meeting],
        room_id: str
    ) -> List[Meeting]:
        """
        Optimize for maximum meetings in a single room.
        
        Uses activity selection algorithm.
        """
        # Filter compatible meetings
        room = self.rooms[room_id]
        compatible = [
            m for m in meetings
            if (room.meets_requirements(m.room_requirements) and
                room.capacity >= len(m.attendees) + 1)
        ]
        
        if not compatible:
            return []
        
        # Activity selection by end time
        sorted_meetings = sorted(compatible, key=lambda m: m.end)
        
        selected = [sorted_meetings[0]]
        last_end = sorted_meetings[0].end
        
        for meeting in sorted_meetings[1:]:
            if meeting.start >= last_end:
                selected.append(meeting)
                last_end = meeting.end
        
        return selected
    
    def find_free_slots(
        self,
        room_id: str,
        date: datetime,
        work_start: time,
        work_end: time
    ) -> List[Tuple[datetime, datetime]]:
        """Find free time slots in a room for a given day."""
        room_schedule = self.schedules.get(room_id, [])
        
        # Get day boundaries
        day_start = datetime.combine(date.date(), work_start)
        day_end = datetime.combine(date.date(), work_end)
        
        # Filter meetings for this day
        day_meetings = sorted(
            [m for m in room_schedule 
             if day_start <= m.start < day_end],
            key=lambda m: m.start
        )
        
        free_slots = []
        current = day_start
        
        for meeting in day_meetings:
            if current < meeting.start:
                free_slots.append((current, meeting.start))
            current = max(current, meeting.end)
        
        if current < day_end:
            free_slots.append((current, day_end))
        
        return free_slots
    
    def get_room_utilization(
        self,
        room_id: str,
        start_date: datetime,
        end_date: datetime
    ) -> float:
        """Calculate room utilization percentage."""
        room_schedule = self.schedules.get(room_id, [])
        
        # Filter meetings in date range
        relevant = [
            m for m in room_schedule
            if start_date <= m.start < end_date
        ]
        
        total_minutes = (end_date - start_date).total_seconds() / 60
        used_minutes = sum(m.duration_minutes for m in relevant)
        
        return used_minutes / total_minutes if total_minutes > 0 else 0


class ConflictResolver:
    """
    Resolves scheduling conflicts using various strategies.
    """
    
    @staticmethod
    def resolve_by_priority(conflicts: List[Meeting]) -> Meeting:
        """Keep highest priority meeting."""
        return max(conflicts, key=lambda m: m.priority.value)
    
    @staticmethod
    def resolve_by_attendees(conflicts: List[Meeting]) -> Meeting:
        """Keep meeting with most attendees."""
        return max(conflicts, key=lambda m: len(m.attendees))
    
    @staticmethod
    def split_meeting(
        meeting: Meeting,
        available_slots: List[Tuple[datetime, datetime]]
    ) -> List[Meeting]:
        """Split a long meeting into available slots."""
        # Find slots that fit within meeting time
        relevant_slots = [
            (max(s, meeting.start), min(e, meeting.end))
            for s, e in available_slots
            if s < meeting.end and e > meeting.start
        ]
        
        parts = []
        for i, (start, end) in enumerate(relevant_slots):
            if end > start:
                part = Meeting(
                    id=f"{meeting.id}_part{i+1}",
                    title=f"{meeting.title} (Part {i+1})",
                    start=start,
                    end=end,
                    organizer=meeting.organizer,
                    attendees=meeting.attendees.copy(),
                    priority=meeting.priority,
                    room_requirements=meeting.room_requirements.copy()
                )
                parts.append(part)
        
        return parts


# Demo
if __name__ == "__main__":
    print("Activity Selection Demo")
    print("=" * 50)
    
    # Basic activity selection
    print("\n1. Basic Activity Selection:")
    activities = [
        Activity("A", 1, 4),
        Activity("B", 3, 5),
        Activity("C", 0, 6),
        Activity("D", 5, 7),
        Activity("E", 3, 9),
        Activity("F", 5, 9),
        Activity("G", 6, 10),
        Activity("H", 8, 11),
        Activity("I", 8, 12),
        Activity("J", 2, 14),
    ]
    
    selected = ActivitySelector.select(activities)
    print(f"   Total activities: {len(activities)}")
    print(f"   Selected: {[a.name for a in selected]}")
    print(f"   Count: {len(selected)}")
    
    # Interval partitioning
    print("\n2. Interval Partitioning (Minimum Rooms):")
    min_rooms = IntervalPartitioner.min_resources(activities)
    print(f"   Minimum rooms needed: {min_rooms}")
    
    groups = IntervalPartitioner.partition(activities)
    for i, group in enumerate(groups):
        print(f"   Room {i+1}: {[a.name for a in group]}")
    
    # Weighted selection
    print("\n3. Weighted Activity Selection:")
    weighted = [
        (1, 4, 50),   # weight 50
        (3, 5, 20),   # weight 20
        (0, 6, 100),  # weight 100
        (5, 7, 30),   # weight 30
    ]
    
    max_weight, indices = WeightedActivitySelector.select(weighted)
    print(f"   Maximum weight: {max_weight}")
    print(f"   Selected indices: {indices}")
    
    # Meeting scheduler
    print("\n4. Meeting Room Scheduler:")
    rooms = [
        MeetingRoom("R1", "Conference A", 10, {"projector", "whiteboard"}),
        MeetingRoom("R2", "Conference B", 6, {"projector"}),
        MeetingRoom("R3", "Small Room", 4, set()),
    ]
    
    scheduler = MeetingScheduler(rooms)
    
    base = datetime(2024, 1, 15, 9, 0)
    meetings = [
        Meeting("M1", "Team Standup", base, base + timedelta(hours=1),
                "alice", ["bob", "carol"]),
        Meeting("M2", "Project Review", base + timedelta(minutes=30),
                base + timedelta(hours=2), "bob", ["alice", "dave"]),
        Meeting("M3", "1:1", base + timedelta(hours=1),
                base + timedelta(hours=2), "carol", ["eve"]),
    ]
    
    result = scheduler.schedule_meetings(meetings)
    print(f"   Scheduled:")
    for room_id, room_meetings in result.items():
        if room_meetings:
            print(f"   {rooms[0].name if room_id == 'R1' else room_id}:")
            for m in room_meetings:
                print(f"      - {m.title}: {m.start.strftime('%H:%M')}-{m.end.strftime('%H:%M')}")
```

## 10. Comparison

| Strategy | Optimality | Use Case |
|----------|------------|----------|
| Earliest Finish | Optimal | Maximum count |
| Shortest Duration | Not optimal | Sometimes wrong |
| Earliest Start | Not optimal | Sometimes wrong |
| Fewest Conflicts | Not optimal | Heuristic only |

## 11. Best Practices

1. **Always sort by finish time** for optimal selection
2. **Use heap** for interval partitioning
3. **Consider weights** when activities have different values
4. **Handle ties** consistently (by start time, then duration)
5. **Precompute conflicts** for weighted version

## 12. References

- [Wikipedia: Activity Selection Problem](https://en.wikipedia.org/wiki/Activity_selection_problem)
- [Wikipedia: Interval Scheduling](https://en.wikipedia.org/wiki/Interval_scheduling)
- Cormen, T. et al. "Introduction to Algorithms" - Chapter 16
