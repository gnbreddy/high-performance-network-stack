# High-Performance Hybrid Network Protocol Stack

A multi-threaded, event-driven network server implementing a custom binary protocol over TCP and UDP. Designed for high-throughput, low-latency systems using the **Reactor Pattern** and **Linux Epoll**.

## Architecture
* **Core Engine:** Single-threaded Reactor loop using `epoll` (Level-Triggered for TCP, Edge-Triggered ready).
* **Concurrency:** Custom Thread Pool for offloading CPU-intensive business logic from the I/O thread.
* **Protocol:** Custom binary framing with `0xCAFE` sync word, sequence tracking, and header validation.
* **Hybrid Mode:** * **TCP (Port 8080):** Reliable control plane for Login/Transaction data.
    * **UDP (Port 8080):** High-frequency telemetry (Battery/Temp) for real-time status updates.

## Technical Highlights
* **Zero-Copy Principles:** Minimized memory allocations by reusing packet buffers.
* **Endianness Safety:** Full `htonl`/`ntohl` serialization for cross-architecture compatibility.
* **Systems Programming:** Direct usage of Linux syscalls (`socket`, `bind`, `epoll_wait`, `fcntl`).

## Performance
* **Non-Blocking I/O:** Handles 10k+ concurrent connections (theoretical) by avoiding "thread-per-client" overhead.
* **Latency:** UDP path processes telemetry in <50 microseconds (kernel to user space).

## Build & Run
```bash
# Compile
g++ -o reactor src/ReactorServer.cpp -Isrc -pthread
g++ -o client src/client.cpp -Isrc
g++ -o udp_client src/udp_client.cpp -Isrc

# Run
./reactor
