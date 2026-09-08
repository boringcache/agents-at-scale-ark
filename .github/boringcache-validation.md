# Dashboard cache validation

This fork compares the dashboard's GitHub-backed BuildKit `mode=min` cache with BoringCache Docker caching. Both providers use Ubuntu 24.04, `linux/amd64`, the upstream Dockerfile, and `make ark-dashboard-deps`. The validation builds the SDK prerequisites in each job; upstream supplies those artifacts from its library job. Report the Docker step separately from preparation and full-job time. Neither provider publishes an image.

The base source is `cc08cdc658acb53632ceb0007a23c93ac276f967`, five first-parent commits behind `721c3a81b4b9e72ff12a23e42aa288b34dfcb2e7`. After cold and fresh-runner warm builds, apply these source commits in order:

1. `950da10d861a6c7649458f923dc8dc6c16f46724`
2. `3ca7cc302d9e6d978d732713bb39968474cc1450`
3. `46ba66b9643eb06b4220233b2ecf5d862cf4d467`
4. `fb7290ee13ba628b217e3fc8c235bddd2d2c083f`
5. `721c3a81b4b9e72ff12a23e42aa288b34dfcb2e7`

Use `git cherry-pick --no-commit`, update `.github/boringcache-source`, and commit both with a signed commit. Each validation-branch push builds that revision. The workflow verifies workload files against the original source commit. These five commits do not change the dashboard build context, so this sequence measures retained reuse across repository changes; it does not demonstrate dashboard invalidation or dependency-change storage growth.

The cache namespace remains `ark-dashboard-validation` throughout the sequence. A repeat at the base source after cache publication is warm, even if an automatic run labels it cold. Dispatch `phase=warm` once after the initial seed; it restores without publishing and requires BoringCache import evidence.

For initial GitHub OIDC enrollment, dispatch `connect=true`, follow the browser approval URL, and select the isolated `boringcache/agents-at-scale-ark` workspace. Normal runs use pinned `boringcache/one`, short-lived OIDC credentials, and no static cache token. Limit publishing to the validation branch. The enrollment job does not run measured workloads.

Retain workflow identities, step timings, source artifacts, complete job logs, and BoringCache evidence. Cache inventory is not a transfer measurement. The GitHub CLI-install cache is separate from its BuildKit layer cache. The comparison does not measure the proposed Artifactory alternative or upstream eviction frequency.
