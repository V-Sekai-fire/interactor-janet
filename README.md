# interactor-janet

The scripted plane: policy written in Janet, reached over the iceoryx2 bus and never in the per-packet path.

## Use

In the design, the plane holds what changes at the rate a person edits a file: routing rules, admission, and what a tool may run. It reads the shared-memory ring at its own rate and is reloaded without rebuilding native code. A call from Janet into native code costs more than the whole per-packet budget of the edge, so the plane publishes and subscribes instead of sitting in that path. It holds no authority and opens no network connections.

## Build and run

The repository holds the design and nothing builds yet.

## Licence

This repository states no licence.
