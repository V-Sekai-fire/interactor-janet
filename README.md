# fabric-janet-plane

The plane that is scripted. It holds policy, rules and anything that changes at the rate a
person edits a file, and it reaches the rest of the system over iceoryx2.

## Why it is a plane and not part of the edge

Janet was first proposed for the edge itself, and the edge is on the client packet path. It
was measured rather than argued about, on one machine, decoding a 12-byte entity update:

| | rate | against the 15 M/s bar |
| --- | --- | --- |
| C++, `memcpy` decode | **841.51 M/s** | 56 times over |
| Janet, byte assembly | 5.70 M/s | **2.6 times under** |
| Janet, `ffi/read` | 4.16 M/s | 3.6 times under |

One crossing from Janet into native code costs **117.8 ns**, and the whole per-packet budget
at 15 M/s is **66.7 ns**. So a single call per packet is 1.8 times the entire budget before
any work happens. Janet cannot be in the packet path, and that is arithmetic.

**Separating it into a plane removes the question.** A plane does not call another plane. It
publishes and it subscribes, over a shared memory ring, and it reads at whatever rate suits
it. The 117.8 ns stops being a tax on every packet and becomes the cost of one sample, taken
when this plane decides to take one. The edge keeps 841.51 M/s, and the two never share a
call stack.

## What this plane may do

- Hold policy that changes often: routing rules, admission, what a tool is allowed to run.
- Read the ring at its own rate and act on what it sees.
- Be edited and reloaded without rebuilding anything native.

## What it must never do

- **It must never sit in the per-packet path.** That is the measurement above, and it is the
  reason this repository exists.
- It holds no authority. `Weft.Authority` decides which controller drives an avatar, in
  FoundationDB, because two connections may land on two machines that never talk.
- It has no networking. A plane has none, and an edge is the thing that does.

## The bus

iceoryx2, brokerless, so no daemon runs beside it. `sigs/` lists the C ABI this plane calls,
the way `fabric-harness/iceoryx2.sigs` does, so it builds on a machine that has never seen
iceoryx2 and fails at start with a named symbol rather than failing to link.

Janet embeds as a C library, which is the same arrangement: a dependency, not weft code.

## State

**Not built.** This holds the decision and the measurement behind it. The harness subtree,
the ring subscription and the reload path come next.
