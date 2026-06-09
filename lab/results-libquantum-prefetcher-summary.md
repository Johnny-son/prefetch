# Libquantum Prefetcher Smoke Summary

Trace: `traces/speccpu/462.libquantum-714B.champsimtrace.xz`

Run size: warmup `100000`, simulation `200000`

Configuration: based on `ChampSim/dpc4/1C.fullBW.nopref.json`, only changing `L2C.prefetcher`.

| prefetcher | IPC | speedup vs no | cycles | L2C issued | useful | accuracy | L1D miss lat | L2C miss lat | LLC accesses |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| sms | 2.470 | 2.63x | 80973 | 4402 | 4119 | 93.6% | 40.95 | 111.50 | 4553 |
| ipcp | 1.702 | 1.81x | 117502 | 25184 | 4198 | 16.7% | 90.60 | 84.47 | 4555 |
| va_ampm_lite | 1.647 | 1.75x | 121407 | 6467 | 3879 | 60.0% | 82.53 | 149.10 | 7070 |
| pythia | 1.569 | 1.67x | 127461 | 23384 | 4193 | 17.9% | 97.96 | 102.40 | 4554 |
| spp_dev | 1.540 | 1.64x | 129848 | 33044 | 1130 | 3.4% | 97.40 | 89.98 | 31105 |
| berti | 1.495 | 1.59x | 133792 | 4048 | 3817 | 94.3% | 98.21 | 115.40 | 4558 |
| ip_stride | 1.405 | 1.50x | 142333 | 12736 | 4194 | 32.9% | 118.70 | 111.20 | 4554 |
| next_line | 1.082 | 1.15x | 184847 | 4548 | 3622 | 79.6% | 166.40 | 160.00 | 4626 |
| no | 0.9395 | 1.00x | 212868 | 0 | 0 | 0.0% | 197.20 | 188.20 | 4555 |

## Quick Analysis

`sms` is the clear winner on this short libquantum trace. It issues only 4402 prefetches, 4119 of them become useful, and the L1D miss latency drops from 197.2 cycles to 40.95 cycles. This suggests the trace has strong spatial locality that SMS captures very well.

`berti` also has excellent accuracy, but its IPC is lower than `sms`, `ipcp`, `va_ampm_lite`, and `pythia`. High accuracy alone is not enough; timeliness matters too. A useful prefetch that arrives too late still leaves demand loads waiting.

`ipcp` and `pythia` both issue many more L2C prefetches. Their accuracy looks low by the simple useful/issued metric, but they still improve IPC a lot because they cover many demand misses early enough.

`spp_dev` is aggressive here: it issues 33044 L2C prefetches and causes 31105 LLC accesses, far above the other prefetchers. IPC still improves, but the traffic cost is high, so it would be risky under tighter memory bandwidth or longer runs.

`next_line` improves over no-prefetch, but much less than the pattern-aware prefetchers. For this trace, simply fetching A+1 helps, but it does not capture enough of the useful stream/spatial pattern.

Because this is only a smoke test with 200K simulated instructions, treat the ordering as a quick signal, not a final conclusion. For a report-quality result, rerun with the lab's longer instruction counts and more traces.
