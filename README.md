# pacman-1150-dogfood

Throwaway dogfood repo for pacman issue #1150 (CI grind-green loop)
verification. The workflow in `.github/workflows/gate.yml` fails while
`fail.marker` exists at the branch head and passes otherwise. This repo
exists to be ground green by a pacman server poller; it is evidence, not
a product.

2026-10-11: tracer-1153 silent-death leg 4 (clean-resume) — this PR injects fail.marker (red-leg-4) at the branch head; gate goes red by design and turns green once the marker is removed.
