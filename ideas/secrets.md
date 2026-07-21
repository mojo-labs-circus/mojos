# Secrets

Decided 2026-07-21: **sops-nix**, not agenix — and needed immediately, not deferred.
Even single-host daily driving needs somewhere for SSH keys/wifi/API tokens that isn't
plaintext in git.

Why sops-nix over agenix: agenix is one-secret-one-file, genuinely simpler on day one,
but every host-key rotation forces re-encrypting every secret that host can see.
sops-nix bundles related secrets in one YAML/JSON file, which scales better once a
host wants several secrets for one service — likely fast, once a fleet exists. The
extra concept sops-nix adds (a `.sops.yaml` describing who can decrypt what) is a real
but small learning cost, not a beginner blocker — and starting on agenix just means
migrating later for no real reason.
