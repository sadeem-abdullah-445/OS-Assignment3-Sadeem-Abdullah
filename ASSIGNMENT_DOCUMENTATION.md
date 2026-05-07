# Assignment 3 - Complete Documentation

**Student Name**: [Your Full Name]  
**Student ID**: [Your ID]  
**Date Submitted**: [Submission Date]

---

## 🎥 VIDEO DEMONSTRATION LINK (REQUIRED)

> **⚠️ IMPORTANT: This section is REQUIRED for grading!**
> 
> Upload your 3-5 minute video to your **PERSONAL Gmail Google Drive** (NOT university email).
> Set sharing to "Anyone with the link can view".
> Test the link in incognito/private mode before submitting.

**Video Link**: [Paste your personal Gmail Google Drive link here]

**Video filename**: `[YourStudentID]_Assignment3_Synchronization.mp4`

**Verification**:
- [ ] Link is accessible (tested in incognito mode)
- [ ] Video is 3-5 minutes long
- [ ] Video shows code walkthrough and commits
- [ ] Video has clear audio
- [ ] Uploaded to PERSONAL Gmail (not @std.psau.edu.sa)

---

## Part 1: Development Log (1 mark)

Document your development process with **minimum 3 entries** showing progression:

### Entry 1 - [2026-05-06, 2:00 PM]
**What I implemented**: 

Set up the repository, updated the student ID, and reviewed the scheduler simulation code.
**Challenges encountered**: 

Understanding the shared resources and identifying possible race conditions.

**How I solved it**: 

I analyzed the shared variables and checked where multiple threads access the same data.

**Testing approach**: 

I tested the original program before adding synchronization.

**Time spent**:

30 minutes

---

### Entry 2 - [2026-05-06, 4:30 PM]
**What I implemented**: 

Added ReentrantLock objects for contextSwitchCount, completedProcessCount, totalWaitingTime, and executionLog.

**Challenges encountered**: 

Making sure every lock is released correctly.

**How I solved it**: 

I used try-finally blocks with lock() and unlock() methods.

**Testing approach**: 

I tested the program multiple times to verify stable counter values and correct logging.

**Time spent**: 

1 hour

---

### Entry 3 - [2026-05-06, 7:30 PM]
**What I implemented**: 

Added Semaphore(1) in run() and runToCompletion() to control CPU access and completed the documentation.

**Challenges encountered**: 

Understanding how semaphores control concurrent execution.

**How I solved it**: 

I used acquire() before execution and release() inside finally blocks.

**Testing approach**: 

I tested the program several times and confirmed that only one process executes in the CPU section at a time.

**Time spent**: 

1.5 hours

---

### Entry 4 - [Date, Time]
**What I implemented**: 

**Challenges encountered**: 

**How I solved it**: 

**Testing approach**: 

**Time spent**: 

---

### Entry 5 - [Date, Time]
**What I implemented**: 

**Challenges encountered**: 

**How I solved it**: 

**Testing approach**: 

**Time spent**: 

---

## Part 2: Technical Questions (1 mark)

### Question 1: Race Conditions
**Q**: Identify and explain TWO race conditions in the original code. For each:
- What shared resource is affected?
- Why is concurrent access a problem?
- What incorrect behavior could occur?

**Your Answer**:

[The first race condition was in the shared counters such as contextSwitchCount++ and completedProcessCount++. Multiple threads could update these variables at the same time, causing lost updates and incorrect final counter values.

The second race condition was in executionLog.add(message). The shared resource was the ArrayList<String> executionLog, which is not thread-safe. Concurrent access could cause missing log entries or ConcurrentModificationException.]

---

### Question 2: Locks vs Semaphores
**Q**: Explain the difference between ReentrantLock and Semaphore. Where did you use each in your code and why?

**Your Answer**:

[ReentrantLock is used for mutual exclusion, which means only one thread can enter a protected critical section at a time. In my code, I used ReentrantLock to protect the shared counters and the execution log, such as contextSwitchLock, completedProcessLock, waitingTimeLock, and logLock.

Semaphore controls how many threads can access a resource at the same time. In my code, I used Semaphore(1) as a binary semaphore for CPU access in run() and runToCompletion(). This ensures that only one process uses the simulated CPU at a time.]

---

### Question 3: Deadlock Prevention
**Q**: What is deadlock? Explain TWO prevention techniques and what you did to prevent deadlocks in your code.

**Your Answer**:

[Deadlock happens when two or more threads wait forever because each one is holding a resource and waiting for another resource to be released. To prevent deadlock, I used try-finally blocks so every lock() is always followed by unlock() even if an error occurs.

I also avoided nested locks in my code. Each method uses only one lock at a time, such as contextSwitchLock, completedProcessLock, waitingTimeLock, or logLock. For the semaphore, I used acquire() before CPU execution and release() inside the finally block, so the CPU permit is always returned.]

---

### Question 4: Lock Granularity Design Decision 
**Q**: For Task 1 (protecting the three counters), explain your lock design choice:
- Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?
- Explain WHY you made this choice
- What are the trade-offs between the two approaches?
- Given that the three counters are independent, which approach provides better concurrency and why?

**Your Answer**:

[I used separate locks for each counter, which is fine-grained locking. In my code, I used contextSwitchLock for contextSwitchCount, completedProcessLock for completedProcessCount, and waitingTimeLock for totalWaitingTime. I chose this design because the three counters are independent and do not need to block each other.

Fine-grained locking gives better concurrency because one thread can update contextSwitchCount while another thread updates completedProcessCount. The trade-off is that it requires more code and careful handling. A single coarse-grained lock would be simpler, but it would reduce concurrency because all counter updates would wait for the same lock.]

---

## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables

**Which variables**: 

contextSwitchCount, completedProcessCount, and totalWaitingTime.

**Why they need protection**: 

These variables are shared between multiple process threads. They need protection because increment and addition operations are not atomic, so concurrent updates can cause lost updates and incorrect statistics.

**Synchronization mechanism used**: 

I used separate ReentrantLock objects: contextSwitchLock, completedProcessLock, and waitingTimeLock.

**Code snippet**:
```java
// Paste your implementation here

public static void incrementContextSwitch() {
    contextSwitchLock.lock();
    try {
        contextSwitchCount++;
    } finally {
        contextSwitchLock.unlock();
    }
}

```



**Justification**: 

Each counter has its own lock because the counters are independent. This protects the shared data while allowing better concurrency than using one lock for all counters.

---

### Critical Section #2: Execution Log

**What resource**: 

ArrayList<String> executionLog.

**Why it needs protection**: 

The execution log is shared between multiple threads. Since ArrayList is not thread-safe, concurrent add() operations could cause missing log entries or ConcurrentModificationException.

**Synchronization mechanism used**: 

I used ReentrantLock logLock to protect the execution log.

**Code snippet**:
```java
// Paste your implementation here

public static void logExecution(String message) {
    logLock.lock();
    try {
        executionLog.add(message);
    } finally {
        logLock.unlock();
    }
}

```

**Justification**: 

Exclusive access is required to preserve the integrity and correct order of the execution log.

---

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**: 

To simulate a single-core CPU and ensure that only one process executes at a time.

**Number of permits and why**: 

I used Semaphore(1) with one permit because only one process should access the simulated CPU at a time.

**Where implemented**: 

The semaphore was implemented in run() and runToCompletion() methods.

**Code snippet**:
```java
// Paste your implementation here

SharedResources.cpuSemaphore.acquire();
try {
    // execution code
} finally {
    SharedResources.cpuSemaphore.release();
}

```

**Effect on program behavior**: 

The semaphore controls CPU access and prevents multiple processes from executing simultaneously. This makes the simulation behave like a real single-core CPU system.

---

## Part 4: Testing and Verification (2 marks)

### Test 1: Consistency Check
**What I tested**: Running program multiple times to verify consistent results

**Testing procedure**: 
```bash
# Commands used (run the program at least 5 times)

I ran the program using java SchedulerSimulationSync five times and compared the output results after each run.

```

**Results**: 
(Show that running multiple times produces consistent, correct results)

The results were consistent in every run. The values of contextSwitchCount, completedProcessCount, and waiting time statistics remained correct and stable.


**Why synchronization is necessary**: 
(Explain what race conditions COULD occur without synchronization, even if you didn't observe them. Explain which shared resources need protection and why.)

Without synchronization, multiple threads could update shared counters at the same time, causing race conditions and incorrect values. The shared executionLog could also experience missing entries or ConcurrentModificationException because ArrayList is not thread-safe.


**Conclusion**: 

Synchronization using ReentrantLock and Semaphore successfully protected shared resources and ensured deterministic program behavior.

---

### Test 2: Exception Testing
**What I tested**: Checking for ConcurrentModificationException

**Testing procedure**: 

I ran the program many times with multiple threads writing to the execution log simultaneously.


**Results**: 

No ConcurrentModificationException or missing log entries occurred during execution.

**What this proves**: 

The logLock successfully protected the shared executionLog and made access to the ArrayList thread-safe.

---

### Test 3: Correctness Verification
**What I tested**: Verifying correct final values (total burst time, context switches, etc.)

**Expected values**: 

**Actual values**: 

**Analysis**: 

---

### Test 4: Different Scenarios
**Scenario tested**: [e.g., different time quantum, more processes, etc.]

**Purpose**: 

**Results**: 

**What I learned**: 

---

## Part 5: Reflection and Learning

### What I learned about synchronization:

[6-8 sentences about key concepts, challenges, insights]

---

### Real-world applications:

Give TWO examples where synchronization is critical:

**Example 1**: 

**Example 2**: 

---

### How I would explain synchronization to others:

[Explain to someone who just finished Assignment 1 - use simple terms and analogies]

---

## Part 6: GitHub Repository Information

**Repository URL**: 

**Number of commits**: 

**Commit messages**: 
1. 
2. 
3. 
4. 

---

## Summary

**Total time spent on assignment**: 

**Key takeaways**: 
1. 
2. 
3. 

**Most challenging aspect**: 

**What I'm most proud of**: 

---

**End of Documentation**
