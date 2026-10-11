# pacman-1150-dogfood

Throwaway dogfood repo for pacman issue #1150 (CI grind-green loop)
verification. The workflow in `.github/workflows/gate.yml` fails while
`fail.marker` exists at the branch head and passes otherwise. This repo
exists to be ground green by a pacman server poller; it is evidence, not
a product.

## CI

This repo has a single workflow, `gate` (`.github/workflows/gate.yml`),
which runs on every `push` and `pull_request`. It checks out the head
commit and applies one rule:

- `fail.marker` present at the branch head → the gate job exits with
  status 1 and the run fails (red).
- `fail.marker` absent → the run passes (green).

Nothing else is tested; the red/green verdict depends solely on whether
`fail.marker` exists at the branch head.
