# Lab 01 — Network Diagnostics

A from-scratch `traceroute` built on top of `ping`, demonstrating how
traceroute actually works at the protocol level.

## What it does

`custom_traceroute.sh` discovers the path to a destination by sending a
`ping` with an increasing TTL (starting at 1). Each router along the path
decrements the TTL and, when it hits 0, replies with an ICMP "Time to live
exceeded" message instead of forwarding the packet — revealing that hop's
address. The script keeps incrementing TTL until the destination itself
responds, or gives up after 30 hops.

This is the same mechanism real `traceroute` implementations use; the
difference is real traceroute typically sends three probes per hop and
parses raw ICMP responses, while this version reuses `ping -c 1 -t <ttl>`
per hop and greps its output.

## Run it

```bash
./custom_traceroute.sh <destination>
# e.g. ./custom_traceroute.sh google.com
```

## Notes

- `docs/Report.pdf` is the original write-up submitted for the assignment.
- Requires a `ping` that supports `-t` as a TTL flag (BSD/macOS ping; on
  Linux this flag means "deadline seconds", so use `-t` → `-T` there).
