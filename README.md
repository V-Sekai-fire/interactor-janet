# interactor-janet

The scripted plane: policy written in Janet, reached over the iceoryx2 bus and never in the per-packet path.

## Use

In the design, the plane holds what changes at the rate a person edits a file: routing rules, admission, and what a tool may run. It reads the shared-memory ring at its own rate and is reloaded without rebuilding native code. A call from Janet into native code costs more than the whole per-packet budget of the edge, so the plane publishes and subscribes instead of sitting in that path. It holds no authority and opens no network connections.

## The measurement behind the decision

Measured on 2026-08-11 on one machine, decoding a 12-byte entity update, against an edge bar of 15 M/s:

| Decoder | Rate |
| --- | --- |
| C++, `memcpy` decode | 841.51 M/s |
| Janet, byte assembly | 5.70 M/s |
| Janet, `ffi/read` | 4.16 M/s |

One crossing from Janet into native code costs 117.8 ns. The whole per-packet budget at 15 M/s is 66.7 ns.

## Build and run

The repository holds the design and nothing builds yet.

## Licence

This repository states no licence.
