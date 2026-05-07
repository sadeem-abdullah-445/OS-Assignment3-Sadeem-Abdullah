# Assignment 3 - Complete Documentation

**Student Name**: [Sadeem Abdullah Alajalen]  
**Student ID**: [445052054]  
**Date Submitted**: [May 7, 2026]

---

## 🎥 VIDEO DEMONSTRATION LINK (REQUIRED)

> **⚠️ IMPORTANT: This section is REQUIRED for grading!**
> 
> Upload your 3-5 minute video to your **PERSONAL Gmail Google Drive** (NOT university email).
> Set sharing to "Anyone with the link can view".
> Test the link in incognito/private mode before submitting.

**Video Link**: [https://drive.google.com/file/d/1S8LGGQkybjOwp1upR3mEK8KICBV4ASnz/view?usp=sharing]

**Video filename**: `445052054_Assignment3_Synchronization.mp4`

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

### Entry 4 - [2026-05-06, 10:30 PM]
**What I implemented**: 

Recorded the demonstration video, updated the ASSIGNMENT_DOCUMENTATION.md file with the video link, and reviewed the final repository before submission.

**Challenges encountered**: 

Ensuring that the video link was accessible and that all assignment requirements were included.

**How I solved it**: 

I tested the Google Drive link in private mode and reviewed the documentation and repository carefully.

**Testing approach**: 

I verified that the program runs correctly and that all synchronization features work without errors.

**Time spent**: 
45 minutes

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

completedProcessCount should match the number of created processes, and all counters should remain consistent after every run

**Actual values**: 

The final values were correct and consistent in all executions. The counters and statistics matched the expected program behavior.

**Analysis**: 

The synchronization mechanisms correctly protected the shared resources without changing the program logic. The program produced deterministic and accurate results under concurrency.

---

### Test 4: Different Scenarios
**Scenario tested**: [I tested the program with different generated values, such as different time quantum and number of processes based on the student ID seed.]

**Purpose**: 

To verify that the synchronization still works correctly under different scheduling conditions.

**Results**: 

The program still executed correctly, and the shared counters and execution log remained consistent.

**What I learned**: 

Synchronization should protect shared resources correctly even when the number of processes or execution timing changes.

---

## Part 5: Reflection and Learning

### What I learned about synchronization:

I learned that synchronization is important when multiple threads use shared resources. Without synchronization, race conditions can cause incorrect results. I learned how ReentrantLock protects shared data and how Semaphore controls CPU access between threads. I also learned that try-finally is important to make sure locks are always released. Fine-grained locking improves concurrency because independent resources do not block each other. Overall, synchronization makes multithreaded programs more stable and predictable.



---

### Real-world applications:

Give TWO examples where synchronization is critical:

**Example 1**: 

Banking systems use synchronization to prevent multiple transactions from updating the same account balance incorrectly.

**Example 2**: 

Print servers use semaphores to control how many users can access printers at the same time.

---

### How I would explain synchronization to others:

Synchronization is a way to organize how threads share resources safely. It is similar to giving only one person access to a shared object at a time to avoid conflicts. Locks protect shared data from race conditions, while semaphores control how many threads can use a resource simultaneously. Without synchronization, multithreaded programs may produce inconsistent or incorrect results.


---

## Part 6: GitHub Repository Information

**Repository URL**: 

https://github.com/sadeem-abdullah-445/OS-Assignment3-Sadeem-Abdullah.git

**Number of commits**: 

**Commit messages**: 
1. Set my student ID: 445052054
2. Added ReentrantLock and Semaphore for synchronization
3. Protected context switch counter with lock
4. Protected completed process counter with lock

---

## Summary

**Total time spent on assignment**: 

Approximately 8 hour

**Key takeaways**: 
1. Synchronization prevents race conditions.
2. ReentrantLock protects shared resources.
3. Semaphore controls concurrent access.

**Most challenging aspect**: 

Understanding how to protect shared resources without reducing concurrency.

**What I'm most proud of**: 

The program runs correctly with consistent results and no synchronization errors.

---

**End of Documentation**
