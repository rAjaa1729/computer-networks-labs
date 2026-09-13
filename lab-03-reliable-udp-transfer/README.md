# Lab 03 — Reliable File Transfer over UDP

UDP gives no delivery guarantee, ordering, or congestion control. This
assignment builds those properties on top of it: a client requests a file
from a UDP server in fixed-size chunks, detects loss via timeout, retransmits,
reassembles the chunks in order, and verifies the result with an MD5
checksum — while a congestion-control scheme decides how many chunks to
have in flight at once.

## Protocol (server-defined)

```
SendSize\nReset\n\n          -> Size: <N>
Offset: <o>\nNumBytes: <n>\n\n -> Offset: <o>\nNumBytes: <n>\n\n<data>
Submit: <id>\nMD5: <hash>\n\n  -> Result: ...\nTime: ...\nPenalty: ...
```
Requests can be dropped or delayed by the network/server, so every client
below is really an exercise in **when to resend** and **how many requests
to have outstanding at once**.

## Client variants

Each file is a complete, independently runnable client implementing the
same protocol with a different retransmission/congestion strategy:

| File | Strategy |
|---|---|
| `udp_client_stop_and_wait.py` | One outstanding request at a time, fixed timeout, retry on loss. Simplest and slowest — the baseline. |
| `udp_client_aimd.py` | Window (`cwnd`) of concurrent requests: +1 chunk per clean round (additive increase), halved on any timeout (multiplicative decrease). |
| `udp_client_aimd_rtt.py` | Same AIMD window, but the timeout is now a live RTT estimate (`EstimatedRTT`/`DevRTT`, TCP-style: `timeout = RTT + 4·dev`) instead of a fixed value, so it adapts to actual network conditions. |
| `udp_client_aimd_logged.py` | AIMD with EMA RTT estimation that also logs burst size and per-offset request/reply timestamps to `logs/*.csv`, used to plot window growth/backoff behavior. |
| `udp_client_threaded.py` | Separate sender/receiver threads: the sender paces requests independently of replies (sleep-throttled to avoid the server's `Squished` rate-limit signal) instead of a request/response round trip gating throughput. Achieved the highest throughput of the set at the cost of no real congestion control. |

## Run it

Point a client at the course server, or at the provided test server in
`tools/` for local testing:

```bash
# local test server (provided .class file, needs a JRE)
# Usage: java UDPServer [port] [filename] [maxlines] [variablerate] [tournament] [verbose]
java -cp lab-03-reliable-udp-transfer/tools UDPServer 9801 testdata/big.txt 100 0 0 1

# then, from another terminal
UDP_SERVER_HOST=127.0.0.1 UDP_SERVER_PORT=9801 python3 udp_client_aimd.py
```

Hardcoded course-server IPs from the original submission are now
environment-variable overrides (`UDP_SERVER_HOST`, `UDP_SERVER_PORT`) with
the original values kept as defaults for reference.

## `archive/`

Earlier iterations kept for reference, showing the progression from a
minimal stop-and-wait client to windowed/threaded designs:
`client_basic.py`, `aimd_minimal.py`, `aimd_v1_sequential.py`,
`aimd_v3_paced.py`, `threaded_v0.py`, `threaded_v1.py`, `threaded_rtt.py`.

## `logs/` and `testdata/`

`logs/data_log.csv` and `logs/offset_log.csv` are sample traces from a real
run of `udp_client_aimd_logged.py` (burst size over time, and per-request/
per-reply offsets), checked in for reference. `testdata/big.txt` is sample
input data used against a local test server.

## Notes

`docs/` contains the original assignment specs (`assignment-brief.pdf`,
`a3-spec.pdf`, `a3-part2-spec.pdf`).
