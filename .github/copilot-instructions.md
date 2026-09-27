<!-- graft:start -->
## Graft — repo context graph

`graft/` is a generated, gitignored cache, not source. Keep it out of commits. Follow the binding [Graft Map Policy](~/repos/governance/policies/GRAFT_MAP_POLICY.md). Only Chad builds and publishes maps; contributors must not run `graft build` or otherwise refresh a map.

Before using a map, fetch the validated published copy:
`bash ~/repos/claude-config/scripts/graft_fetch_map.sh <repo> [harpy-user]`.
The fetcher requires clean-main provenance matching published `main` and verifies the fetched wiring bytes against that provenance. If no valid published map is available or the fetch is refused, treat any local `graft/` as invalid and use source directly.

After fetching, compare `graft/PROVENANCE.json`'s source commit with this repository's `HEAD`. If the source-change gap exceeds the agreed freshness threshold, treat affected files as stale and inspect those files directly. If the source commit or threshold cannot be determined, report freshness as unknown and inspect affected files directly. A map with missing or dirty provenance is invalid.

Use read-only queries with `--no-refresh` where supported. For example, run `graft map --no-refresh`, `graft ask "<question>" --source --no-refresh`, or `graft callers <symbol> --no-refresh`. Match the query to the task and open source only when the map cannot answer the question or freshness is stale or unknown for the affected files.
<!-- graft:end -->
