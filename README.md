# pacman-1150-dogfood

Throwaway dogfood repo for pacman issue #1150 (CI grind-green loop)
verification. The workflow in `.github/workflows/gate.yml` fails while
`fail.marker` exists at the branch head and passes otherwise. This repo
exists to be ground green by a pacman server poller; it is evidence, not
a product.

2026-10-11: tracer-1153 silent-death leg 5 (clean-resume) — red-leg-5 marker injected, gate red on purpose
