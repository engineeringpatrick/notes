Safe transactions are usually referred to as ACID.

### Atomicity
All writes succeed or none of them do. Imagine having to withdraw $10 from Jordan to give $10 to Patrick. Both of them have to succeed.
### Consistency
Changes made within a transaction are consistent with database constraints. All rules, constraints and triggers. If the data gets into an illegal state, the whole transaction fails.
### Isolation
No race conditions. All transactions are executed independently of each other. 
If two transactions for a withdrawal of $100 run at the same time, at the end the balance should be -200.
### Durability
Once the transaction completes and changes are written to the DB, they are persisted. 

## Write Ahead Log
We use a WAL to achieve atomicity, consistency and durability. Isolation, on the other hand, is hard and takes long to implement.
Every transaction, we write all operations in a transaction, and at the end we write commit. Once we write commit, we know all the writes we put in the WAL are "safe". If the system were to shutdown, we'd go through the WAL and replay all transactions to make sure everything is good.

# Levels of Transaction Isolation
## Read Committed Isolation
A database is said to be read committed isolated if it protects against these two race conditions:
1) Dirty Write
	- let's say two users try to write to the same row, in this order:
		- user 1 sets the name as "patrick"
		- user 2 as "george"
		- user 2 sets the address as "seattle"
		- user 2 commits
		- user 1 sets address as "los angeles"
		- user 1 commits
	- and if there's no type of thread safety, the result could be:
		- name = "george", address = "los angeles"
	- which is not what we want. to fix, we use a lock for every row.
	- user has to acquire a lock before writing to it.
2) Dirty read
	- Let's say this happens:
		- thread 1 removes 10 from patrick's balance
		- thread 2 (patrick) reads balance (-10!!)
		- thread 1 pays patrick 10 bucks
		- thread 1 commits
		- thread 2 commits
	- what happened? patrick saw a balance of -10, even though it should've been 0. patrick's balance shouldn't have updated UNTIL thread 1 committed.
	- So how to fix? well, during a transaction, for every operation that happens, the thread actually updates a temporary value.
	- when the thread commits, then the original value points to the temporary (new) value.


## Snapshot Isolation
Long reads (more than a minute long) risk to see inconsistent data throughout the transaction. Let's say row 1 has 100 dollars, and row 100 has 100 dollars. During the reading, someone writes to the table (reads are non-blocking) and moves the 100th row's money to the 1st row. 
The read transaction could've read 100 dollars for row 1 and then 0 dollars for row 100 since that value was changed by the write operation while the read was ongoing.

How do you fix this? Simple, reads read a snapshot of the data. 
Snapshots are implemented by storing old values of modified data. Every write, you keep it in the WAL with a certain timestamp. So basically for every row you will have a list of different values in time.
If you start a read transaction at timestamp 15, you will only read values with a timestamp of 15 max. If a write happens at timestamp 20, you won't read that value.