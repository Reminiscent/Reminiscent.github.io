# GOO + rollout + LinDP as a GEQO replacement

Open `index.html` in a browser. It is a single self-contained page: all data,
charts and the algorithm animation are inlined, with no external requests.

## Published site

https://reminiscent.github.io/ (Chinese) and
https://reminiscent.github.io/index.en.html (English).

The site is plain static HTML, so the only build step is copying the files to
the Pages artifact. Push to `main`; `.github/workflows/pages.yml` deploys it.
Preview locally with:

```sh
python3 -m http.server 8000
```

then open http://localhost:8000/.

## Contents

| Path | What it is |
| --- | --- |
| `index.html` | The article (Chinese), with interactive charts and the algorithm animation |
| `data/*.csv` | Per-query numbers behind the sections 1, 3, 5 and 8 charts (one selected measurement per query/method); the plan-level statistics in section 4 are inlined in `index.html` |
| `patches/` | The 5-commit PostgreSQL patch series used for all measurements (`git am`) |


## CSV columns

`snapshot` is the ANALYZE snapshot (1-5 for the stability runs, 1 for the
others). For each method (`dp`, `geqo`, `new` = GOO + rollout + LinDP):
estimated `cost`, `planning_ms`, `execution_ms`, and
`planner_memory_kb` (`EXPLAIN (MEMORY)` Planning Memory Used). An empty
execution value means the run timed out (GEQO, JOB-Complex q19, snapshot 1).

`music_business44.csv` holds the 44 MusicBrainz business queries (GEQO and
`new` only, native estimates, 900 s timeout), used only as a reference. Values
are per-query medians of three measured repetitions after one warmup.
`status = timeout` means the method did not finish within 900 s.

## Measurement protocol

O2 build with ICU; JOB and JOB-Complex queries with at least 12 relations
(33 queries). One warmup and three measured repetitions per query and method;
the measured repetition with the median total time is reported, and every
metric comes from that same repetition. GEQO uses all default settings.
The raw evidence (all repetitions, plan JSON, statistics snapshots, replay
cache) is about 130 MB and is available separately.
