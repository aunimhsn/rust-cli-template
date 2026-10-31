# Technical choices

The technical choices of the [roadmap](../roadmap.md), with their reasons.

| Topic | Choice | Reason |
|---|---|---|
| Releases | GitHub Actions workflow triggered by version tags | Builds every system on its own runner and publishes the archives with `gh release`, without extra tooling. |
| Mock-up server | `python3 docs/mockups/serve.py <port>`, port `<port>` | Only for a terminal interface: serves the mock-up without cache, on a port of its own (choose one between 8700 and 8799, unused by the other projects), so that two projects never take each other's server over. |
