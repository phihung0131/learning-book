### **1. MVCC (Multi-Version Concurrency Control)**

**Theory**

Let's revisit isolation levels. How can a database allow Transaction A to read data while Transaction B is modifying it, without having to lock everything down? The answer in most modern databases (PostgreSQL, MySQL's InnoDB, Oracle) is **MVCC - Multi-Version Concurrency Control**.

**The Core Idea of MVCC:**
Instead of storing only a single, latest version of each row, the database stores **multiple versions** of that row. When a row is `UPDATE`d, the database doesn't overwrite the old data. Instead, it does two things:
1.  It marks the old version of the row as "expired" at a certain point in time (e.g., by the ID of the transaction that modified it).
2.  It creates a new version of the row with the updated data.

*   **Analogy:** Think of a Google Doc. When you edit a sentence, the system doesn't erase the old one. It keeps a revision history. Anyone can view the document as it existed at a specific point in the past. MVCC works similarly for each row of data.

**How Does MVCC Solve the "Read-Write" Problem?**
The golden rule of MVCC is: **"Writers don't block readers, and readers don't block writers."**

When a transaction begins, it is given a "snapshot" of the database at that moment. When this transaction wants to read a row:
*   The database looks at all available versions of that row.
*   It finds and returns the **latest** version that is **"visible"** to this transaction's snapshot. (Meaning, that version must have been created by a transaction that `COMMIT`ted *before* the current transaction began).

**Example:**
*   **10:00:00 AM:** The row for `Product A` has `price = 100`.
*   **10:01:00 AM:** Transaction T1 begins. It gets a snapshot of the database at this time.
*   **10:02:00 AM:** Transaction T2 begins, `UPDATE`s `Product A` to `SET price = 120`, and then **`COMMIT`s**. Now, the database has two versions of `Product A`: `(price=100, expired)` and `(price=120, current)`.
*   **10:03:00 AM:** Transaction T1 runs `SELECT price FROM Product A`. Even though the latest version is 120, it was created *after* T1's snapshot was taken. Therefore, the database will ignore this new version and return the older, visible version: `price = 100`.

=> This allows T1 to have a consistent view of the data (as in the `REPEATABLE READ` level) without having to lock the `Product A` row, which in turn allows T2 to perform its update freely.

**The Cleanup Problem (Vacuuming/Purging):**
Of course, storing multiple versions will bloat the database. Therefore, databases that use MVCC have a background process (e.g., `VACUUM` in PostgreSQL) that cleans up old row versions that are no longer visible to any active transaction.

---

**Interview Questions**

*   **Question:** "What is MVCC and what benefit does it provide for concurrency control?"
    *   **Answer:** MVCC, or Multi-Version Concurrency Control, is a mechanism where the database maintains multiple versions of the same data row to handle concurrent access. Its greatest benefit is that it allows read and write operations to occur at the same time without conflicting with each other. Readers see a consistent "snapshot" of the data from a point in time, while writers can create new versions. This dramatically increases the level of concurrency and performance compared to purely lock-based mechanisms.

*   **Question:** "If MVCC is so good, why do we still need locking?"
    *   **Answer:** MVCC is excellent at resolving read-write conflicts. However, it does not resolve **write-write** conflicts. If two transactions try to `UPDATE` the **exact same row** at the same time, the database must still resort to locking. The first transaction to arrive will acquire a write lock on that row, and the second transaction will have to wait until the first one either `COMMIT`s or `ROLLBACK`s.

*   **Question:** "What role does the `VACUUM` process in PostgreSQL play in relation to MVCC?"
    *   **Answer:** MVCC creates a lot of "dead" row versions (or "dead tuples"). `VACUUM` has two primary roles: 1) To reclaim the storage space occupied by these dead versions so it can be reused. 2) To prevent a problem called "Transaction ID Wraparound," which ensures the database can continue to function after all transaction IDs have been used.

---

#### **2. Locking Mechanisms**

**Theory**

When MVCC is not enough (primarily for write-write conflicts), the database must use **locks**. A lock is a mechanism to ensure that only one transaction can access or modify a data resource at a time, thereby preventing conflicts.

**Main Types of Locks:**

1.  **Shared Lock (S Lock):**
    *   Also known as a **Read Lock**.
    *   Multiple transactions can hold an S Lock on the same resource simultaneously.
    *   If a transaction holds an S Lock, other transactions can also **read** (acquire another S Lock) but cannot **write** (cannot acquire an X Lock).
    *   **Analogy:** Many people can read the same book at the same time (S Lock), but while someone is reading, no one is allowed to write in the book (X Lock).

2.  **Exclusive Lock (X Lock):**
    *   Also known as a **Write Lock**.
    *   Only **one** transaction can hold an X Lock on a resource at any given time.
    *   If a transaction holds an X Lock, no other transaction can acquire any type of lock (neither S Lock nor X Lock) on that resource. They must wait.
    *   **Analogy:** When one person is writing in the book (X Lock), no one else can read it or write in it.

**Lock Granularity:**

Locks can be applied at different levels. There is a trade-off between concurrency and the overhead of managing locks.

*   **Row-level Lock:**
    *   The lock is applied only to the individual rows being affected.
    *   **Pros:** Provides the highest level of concurrency. Transactions operating on different rows in the same table can proceed in parallel.
    *   **Cons:** High memory and management overhead if a transaction needs to lock millions of rows.
    *   This is the default mechanism for modern databases like InnoDB and PostgreSQL.

*   **Page-level Lock:**
    *   The lock is applied to an entire data "page" (typically 8KB or 16KB) on disk.
    *   It's a balance between row and table-level locking.

*   **Table-level Lock:**
    *   The entire table is locked.
    *   **Pros:** Very low management overhead.
    *   **Cons:** Kills concurrency. When one transaction locks the whole table, no other transaction can access it.
    *   Often used for DDL operations (`ALTER TABLE`) or when a transaction updates a very large portion of the table.

*   **Lock Escalation:**
    *   An automatic mechanism in the database. When a transaction acquires too many fine-grained locks (e.g., thousands of row locks), the database might decide to "escalate" it to a higher-level lock (e.g., a table lock) to save on management resources.

---

**Interview Questions**

*   **Question:** "Compare and contrast Shared Locks and Exclusive Locks."
    *   **Answer:** A Shared Lock (S Lock) allows multiple transactions to read a resource concurrently but prevents any writing. It is compatible with other S Locks. An Exclusive Lock (X Lock) allows only a single transaction to write to a resource and prevents all other transactions from both reading and writing. It is not compatible with any other type of lock.

*   **Question:** "Why are row-level locks preferred in OLTP (Online Transaction Processing) systems?"
    *   **Answer:** OLTP systems (like e-commerce sites, banking systems) are characterized by having many small, short, concurrent transactions that typically only operate on a few rows of data (e.g., updating a single order, checking one account balance). Row-level locking allows these transactions to run in parallel without interfering with each other, as long as they don't touch the exact same row. This maximizes the system's throughput and responsiveness.

*   **Question:** "What does the `SELECT ... FOR UPDATE` statement do?"
    *   **Answer:** This is a very important statement. A normal `SELECT` in an MVCC system does not take a write lock. `SELECT ... FOR UPDATE` reads the data but simultaneously places an **Exclusive Lock (X Lock)** on the rows it reads. The purpose is to "lock" those rows to prevent any other transaction from changing them, before the current transaction performs a subsequent `UPDATE` on those same rows. It's commonly used in "read-modify-write" scenarios to prevent race conditions.

---

#### **3. Deadlocks: Detection and Prevention**

**Theory**

A **Deadlock** is a situation that occurs when two (or more) transactions are waiting for each other to release locks, and neither can proceed.

*   **Classic Analogy:**
    1.  Transaction A locks Row 1 and is trying to acquire a lock on Row 2.
    2.  Transaction B locks Row 2 and is trying to acquire a lock on Row 1.
*   A cannot proceed because B is holding Row 2.
*   B cannot proceed because A is holding Row 1.
*   They will both wait for each other forever.

**How Do Databases Handle Deadlocks?**
Databases don't let them wait forever. Most systems have a **deadlock detection process** that runs periodically in the background.
1.  It builds a "waits-for graph" of transactions and the locks they are requesting.
2.  If it detects a **cycle** in the graph (A waits for B, B waits for A), it identifies that a deadlock has occurred.
3.  It will choose one of the transactions as the **"victim"**.
4.  It will **automatically `ROLLBACK`** the entire victim transaction, which releases the locks it was holding.
5.  This allows the other transaction(s) to proceed.
6.  The client application of the victim transaction will receive a deadlock error message.

**How to Prevent Deadlocks (from the application side):**

Prevention is better than cure. The following are the most common techniques:

1.  **Access Resources in a Consistent Order:**
    *   This is the most effective method. Establish a convention that all transactions in your application must lock resources (tables, rows) in the same exact order.
    *   For example: Always lock the `Accounts` table before the `Transactions` table. If both A and B follow this rule, a deadlock will never happen. A will lock `Accounts` first, and B will have to wait. A will then proceed to lock `Transactions` and complete its work.

2.  **Keep Transactions Short and Fast:**
    *   The longer a transaction runs, the longer it holds locks, and the higher the probability of conflicts and deadlocks.

3.  **Lock at the Lowest Granularity Possible:**
    *   Use row-level locks instead of table-level locks where possible. Locking fewer resources reduces the chance of conflicts.

4.  **Use a Lower Isolation Level (if feasible):**
    *   Lower isolation levels (like `READ COMMITTED`) use fewer locks and hold them for shorter durations, which reduces the risk of deadlocks.

5.  **Use `SELECT ... FOR UPDATE` with `NOWAIT` or `SKIP LOCKED`:**
    *   **`NOWAIT`:** If a row is already locked, the statement fails immediately instead of waiting. The application can catch this error and retry later.
    *   **`SKIP LOCKED`:** Skips any rows that are already locked and only processes the available ones. This is very useful in queue processing scenarios where multiple workers are pulling jobs from a table.

---

**Interview Questions**

*   **Question:** "What is a deadlock? Give an example."
    *   **Answer:** (Use the definition and the classic analogy of Transaction A and B locking Row 1 and Row 2).

*   **Question:** "How can you minimize the likelihood of deadlocks in your application?"
    *   **Answer:** The most important way is to ensure transactions access resources in a consistent order. For instance, if updating user accounts and products, always update the `users` table first, then the `products` table. Additionally, I would strive to keep transactions as short as possible, only performing essential operations within the transaction block, and using `SELECT ... FOR UPDATE` carefully to lock necessary rows upfront.

*   **Question:** "When your application receives a deadlock error from the database, what should it do?"
    *   **Answer:** A deadlock error is a type of transient error. The application should be designed to catch this specific error and automatically **retry** the entire transaction after a short, randomized delay (a technique called exponential backoff). Since the database has already rolled back the "victim" transaction, a retry is likely to succeed the next time.

---

#### **4. Database Scaling**

**Theory**

As your application grows, a single database server will eventually be unable to handle the load. There are two primary strategies for scaling.

**1. Vertical Scaling (Scale Up):**
*   **What it is:** Making the current server more powerful.
*   **Actions:** Upgrading the CPU, adding more RAM, using faster storage (SSDs/NVMe).
*   **Pros:**
    *   Easy to implement. Requires no application code changes.
*   **Cons:**
    *   **Has a limit:** You will eventually hit a ceiling where you can't upgrade the hardware any further (you've bought the most powerful server available).
    *   **Expensive:** The cost increases exponentially.
    *   **Single Point of Failure:** If this one server goes down, the entire system is offline.

**2. Horizontal Scaling (Scale Out):**
*   **What it is:** Adding more servers to the system.
*   **Actions:** Instead of one "giant" server, you have many "normal" servers working together.
*   **Pros:**
    *   **Virtually limitless scalability:** Need more capacity? Just add another server.
    *   **Cost-effective:** Commodity servers are much cheaper.
    *   **High Availability:** If one server fails, the others can continue to operate, preventing a total system outage.
*   **Cons:**
    *   **Complex:** Requires architectural changes to the system and application.

**Horizontal Scaling Techniques for Databases:**

*   **Read Replicas:**
    *   **How it works:** Create multiple copies of the primary (master) database. All **write** operations (`INSERT`, `UPDATE`, `DELETE`) go to the master. The master then replicates the data to the copies (replicas). All **read** operations (`SELECT`) are distributed across the replicas.
    *   **When to use:** Highly effective for applications with a much higher read-to-write ratio (e.g., blogs, news sites, e-commerce).
    *   **Downside:** There is a **replication lag**. The data on a replica might be slightly out of date compared to the master.

*   **Sharding:**
    *   **How it works:** Partition your data and store it across multiple different database servers (called shards). Each shard holds a subset of the data.
    *   **Example:** Shard 1 holds data for users with IDs 1-1,000,000. Shard 2 holds data for users with IDs 1,000,001-2,000,000.
    *   **When to use:** When your database is too large to fit on a single server, or when the write throughput is too high for a single master to handle. This is the ultimate solution for very large-scale systems.
    *   **Downside:** Extremely complex to implement and manage. `JOIN`ing across different shards is difficult and costly. Requires a routing layer to know which shard holds which data.

*   **Partitioning:**
    *   **Note:** Often confused with Sharding, but they are different. Partitioning is the act of splitting a **single large table** into smaller pieces, but all pieces remain **on the same database server**.
    *   **Example:** Partitioning a `logs` table by month. You would have partitions like `logs_2024_01`, `logs_2024_02`, etc.
    *   **Benefits:** Improves query performance (the database only needs to scan the relevant partition) and simplifies data management (e.g., deleting old data by `DROP`ping an entire partition instead of `DELETE`ing millions of rows).

---

**Interview Questions**

*   **Question:** "Compare Vertical Scaling and Horizontal Scaling."
    *   **Answer:** Vertical scaling is making one server more powerful. Horizontal scaling is adding more servers. Vertical scaling is simple but expensive and limited. Horizontal scaling is more complex but offers near-infinite scalability and high availability.

*   **Question:** "What is replication lag, and how would you handle it in your application?"
    *   **Answer:** Replication lag is the time delay between data being written to the master and it appearing on a read replica. To handle it, an application can implement a "read-after-write consistency" strategy. This means that immediately after a user performs a write operation (e.g., posts an article), any subsequent read queries from that same user for a short period are routed directly to the master to ensure they see their own latest data. Reads from other users can still go to the replicas.

*   **Question:** "What is the difference between Sharding and Partitioning?"
    *   **Answer:** Both are techniques for splitting up data. The key difference is where the data is stored. Partitioning splits a table into smaller pieces on the **same database server**. Sharding splits your data and stores it on **multiple different database servers**. Sharding is a horizontal scaling strategy, whereas partitioning is primarily a performance and management optimization technique on a single server.