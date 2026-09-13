# Computer Networks Labs

Three assignments from an undergraduate Computer Networks course (IIT Delhi,
COL334), each exploring a different layer/problem in networking through a
working client-server implementation.

| Lab | Topic | Concepts |
|---|---|---|
| [lab-01-network-diagnostics](lab-01-network-diagnostics) | Custom traceroute | ICMP TTL expiry, `ping`, network path discovery |
| [lab-02-distributed-tcp-download](lab-02-distributed-tcp-download) | Multi-client TCP protocol | TCP sockets, threading, coordination/dedup across peers |
| [lab-03-reliable-udp-transfer](lab-03-reliable-udp-transfer) | Reliable transfer over UDP | Sliding window, AIMD congestion control, RTT estimation, timeout/retransmit |

Each lab folder is self-contained (own `docs/`, own README) since the
assignments are independent.

## Structure

```
lab-XX-name/
├── README.md      # what it does, how to run it
├── *.py / *.sh    # the submitted implementation(s)
├── archive/       # earlier iterations, kept for reference
├── docs/          # original assignment brief / report
├── logs/          # sample run output (lab-03 only)
└── tools/         # provided test-server binaries (lab-03 only)
```

## Context

These were built against course-provided servers only reachable from the
IIT Delhi network (hardcoded IPs have been replaced with environment
variables so the code can be read and re-run against a local test server
instead). See each lab's README for specifics.
