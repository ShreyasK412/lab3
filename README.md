# UID: 005717129
This laboratory develops a concurrent-safe implementation of a hash table. The initial implementation, called hash-table-base, sequentially handles collisions using chaining. Two additional implementations, hash-table-v1 and hash-table-v2, enable concurrent execution with multiple threads. hash-table-v1 adopts a coarse-grained locking approach, while hash-table-v2 employs a fine-grained locking strategy.

## Building
To construct the program, begin by downloading all the files from this repository (excluding README.md). Next, navigate to the directory where the files are located and execute the provided command.
```shell
make
```

## Running
To run the program, use the following command:
```shell
./hash-table-tester -t 8 -s 50000
```
 The "number of threads to test with" refers to the quantity of threads you wish to use for testing. Specify the desired number of threads after the -t argument (default value is 4). By using the -s option, you can modify the number of hash table entries to add per thread (default value is 25,000).. 

## First Implementation
To enable proper concurrency in the hash_table_v1_add_entry function, a coarse-grained locking structure was implemented. This structure involved locking and unlocking the entire critical section within the function. The critical section encompassed obtaining the hash table entry, the list head, and the list entry, as well as creating and adding the list entry to the list. Consequently, a global lock was established. Prior to accessing the hash table to retrieve the appropriate hash table entry, the lock was acquired, and it was released after the entry was successfully created and added. When the hash table was destroyed in the hash_table_v1_destroy function, the lock was also destroyed as part of the hash table's cleanup process.

### Performance
To test the correctness and implementation of the hash table, the same test was run three times. 
```shell
//test code
./hash-table-tester -t 4 -s 50000
```
Test 1 Results
```
# Generation: 44,712 usec
# Hash table base: 82,385 usec
#   - 0 missing
# Hash table v1: 217,132 usec
#   - 0 missing
```
Test 2 Results
```
# Generation: 55,765 usec
# Hash table base: 333,802 usec
#   - 0 missing
# Hash table v1: 689,030 usec
#   - 0 missing
```
Test 3 Results
```
# Generation: 66,962 usec
# Hash table base: 348,511 usec
#   - 0 missing
# Hash table v1: 695,645 usec
#   - 0 missing
```
The v1 hash table exhibits zero missing entries, indicating the correct implementation of concurrency. Nevertheless, the v1 implementation experiences approximately twice the execution time compared to the base implementation. This is attributed to the overhead incurred by the locking mechanism with threads. As there is only a single lock available, all threads must share it, resulting in a sequential execution where only one thread can access the lock at any given time. Consequently, despite multiple threads running concurrently, the presence of the lock creates a bottleneck, leading to increased overhead and decreased performance.

## Second Implementation
To optimize the time required for adding all the entries while maintaining correctness, the hash_table_v2_add_entry function employed a fine-grained locking structure. This involved adding a lock to each table entry within the hash_table_v2_create function. Subsequently, in the hash_table_v2_add_entry function, the lock associated with the corresponding hash_table_entry was acquired after accessing the table entry, and then released after adding the new list entry to the list. Finally, in the hash_table_v2_destroy function, all locks were appropriately destroyed to prevent any memory leaks from occurring.

### Performance
To test the correctness and implementation of the hash table, the same test was run three times. 
```shell
//test code
./hash-table-tester -t 4 -s 50000
```
Test 1 Results
```
# Generation: 43,005 usec
# Hash table base: 80,527 usec
#   - 0 missing
# Hash table v2: 20,940 usec
#   - 0 missing
```
Test 2 Results
```
# Generation: 41,061 usec
# Hash table base: 81,970 usec
#   - 0 missing
# Hash table v2: 22,156 usec
#   - 0 missing
```
Test 3 Results
```
# Generation: 43,603 usec
# Hash table base: 85,381 usec
#   - 0 missing
# Hash table v2: 23,280 usec
#   - 0 missing
```

This time, the speedup achieved is approximately four times faster compared to the sequential hash table implementation that lacks concurrency. The reason for this significant improvement lies in the fine-grained locking approach used in hash_table_v2_add_entry. By locking each individual hash table entry instead of using a coarse-grained lock like in v1, the bottleneck effect is mitigated. The test results align with the expected speed of adding entries into the hash table. As long as the threads do not contend for the same hash table entry, the hash table should exhibit performance improvements that scale linearly with the number of threads, resulting in a faster execution. 

## Cleaning up
To clean up, run the following command on the shell:
```shell
make clean

```
