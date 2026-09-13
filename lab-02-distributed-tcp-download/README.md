# Lab 02 — Distributed TCP Download

A small team of clients cooperatively downloads 1000 numbered lines from a
course-provided TCP server, pooling what each client fetches so the group
finishes faster than any single client working alone, then submits the
combined result back to the server.

## Protocol

- `coordinator.py` connects "upstream" to the course server (`SENDLINE` →
  `<line_no>\n<content>\n`) and also listens for worker clients.
- `client.py` (one per teammate) connects to the coordinator and to its own
  upstream socket. It requests lines via `SENDLINE`, forwards newly-seen
  lines to the coordinator, and the coordinator forwards them back to the
  other workers — deduplicating on line number (`All_lines`) so nobody's
  redundant fetch is wasted work.
- Once all 1000 lines are collected, the coordinator serves any lines a
  worker is still missing, and the full set is `SUBMIT`ted upstream by both
  the coordinator and each client.

## Run it

Needs the coordinator running first, then one or more clients pointed at it:

```bash
# on the coordinator's machine
COORDINATOR_HOST=<coordinator-ip> COORDINATOR_PORT=1235 \
UPSTREAM_HOST=<course-server-ip> UPSTREAM_PORT=<port> \
python3 coordinator.py

# on each worker machine
COORDINATOR_HOST=<coordinator-ip> COORDINATOR_PORT=1235 \
UPSTREAM_HOST=<course-server-ip> UPSTREAM_PORT=<port> \
python3 client.py
```

The upstream server was only reachable from the IIT Delhi campus network
during the course, so these values are illustrative — hardcoded IPs from
the original submission have been moved to environment variables (with the
original defaults kept as fallback) so the protocol logic stays readable.

## `archive/`

Earlier single-client and simplified variants kept for reference:
`client_simple.py`, `coordinator_simple.py`, `coordinator_3client.py`
(fixed 3-worker version), and `peer_to_peer.py` (an alternate design without
a central coordinator).

## Notes

`docs/assignment-brief.pdf` is the original assignment specification.
