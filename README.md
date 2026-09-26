## Suzanne Streich

Computer Science · Systems & Distributed Infrastructure

### Professional Focus

I design and build distributed systems that keep working when the network does not. My work centers on consensus protocols, replication, and the storage engines underneath them, with an emphasis on correctness under partition, bounded memory, and predictable recovery times.

### Flagship Projects & Architecture

#### Raft-Go: A Minimal Raft Implementation

A from-scratch implementation of the Raft consensus algorithm in Go, designed to survive leader failures, network partitions, and reordering.

- **Architecture**: Core components are a state machine, replicated log, and transport layer. The concurrency model is a single-threaded event loop per Raft node, with a dedicated goroutine for RPC handling. The on-disk format is a length-prefixed log of entries, each with a term and index, fsynced on commit. The wire protocol is a custom JSON-RPC over TCP.
- **Trade-offs**: Chose synchronous fsync on commit over batching for durability, paying a 3x write-latency penalty under sustained load. Chose in-memory log storage over an on-disk log for simplicity, paying a recovery-time cost after a crash.
- **Results**: Under a 3-node cluster with 1KB payloads and 10ms network latency, commit latency p50 = 25ms, p95 = 40ms, p99 = 60ms. Throughput of 1,200 commits/sec with a single leader. Recovery time after leader crash is 2.5 seconds with a 10,000-entry log.

#### KV-Store: LSM-Tree Storage Engine

A storage engine implementing an LSM-tree with a write-ahead log and periodic compaction, designed for high write throughput with bounded read amplification.

- **Architecture**: The engine uses a memtable (in-memory skip list) and a set of immutable SSTables on disk. The concurrency model is a single writer with multiple readers, using a read-write lock. The on-disk format is a sorted string table with a block index and bloom filter. Compaction is triggered by size-based levels.
- **Trade-offs**: Chose a size-based compaction policy over leveled compaction for lower write amplification, paying a higher read amplification on range scans. Chose a block size of 4KB over 64KB for better cache locality, paying a 10% increase in index size.
- **Results**: Under a write-heavy workload (90% writes, 10% reads) with 1KB values, sustained write throughput of 50,000 ops/sec with p99 latency of 2ms. Read amplification of 3.2 on average. Memory footprint of 128MB for a 10GB dataset.

### Technical Foundation

- **Core Systems**: `Go`, `Rust`, `C++`
- **Storage & Data**: `LevelDB`, `RocksDB`, `etcd`, `BoltDB`
- **Infrastructure & Observability**: `Docker`, `Kubernetes`, `Prometheus`, `Grafana`

### How I Build

- **Failure isolation**: I design each component to fail independently, so a crash in one does not cascade.
- **Backpressure**: I apply backpressure at every boundary, ensuring slow consumers do not cause unbounded memory growth.
- **Deterministic replay**: I make all non-determinism explicit, so bugs can be reproduced and fixed in a test harness.
- **Bounded queues**: I use bounded queues with a defined overflow policy, preventing resource exhaustion under load.

### Current Explorations

- **Raft paper (In Search of an Understandable Consensus Algorithm)**: Studying the leader election and log replication mechanisms for edge cases in my implementation.
- **Paxos Made Simple**: Comparing the Paxos and Raft approaches to consensus, focusing on the trade-offs in simplicity vs. generality.
- **Linux kernel's `epoll` subsystem**: Understanding the event notification mechanism to improve the performance of my networking layer.

### Contact

GitHub: [@rufpwbyb](https://github.com/rufpwbyb)