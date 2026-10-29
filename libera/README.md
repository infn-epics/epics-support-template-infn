# Libera templates

| template | unit | MCI application |
|---|---|---|
| [`libera-sppp`](libera-sppp) | Libera Brilliance single pass | (legacy driver) |
| [`libera-spe`](libera-spe) | Libera Single Pass E (`rfspe` boards) | `libera-spe` |
| [`libera-bplus`](libera-bplus) | Libera Brilliance+ eBPM (`raf` boards) | `libera-ebpm` |
| [`libera-llrf`](libera-llrf), [`libera-llrf2`](libera-llrf2) | Libera LLRF | |

## Common BPM interface

Every BPM template exposes the same PVs, so that clients (e.g. unibpm-opi `bpm_trajectory.bob`)
don't depend on the unit type. `<BPM>` is `<iocprefix>` for `libera-sppp` (one BPM per ioc) and
`<prefix>:<device name>` for `libera-spe` / `libera-bplus` (one BPM per device).

| PV | meaning |
|---|---|
| `<BPM>:X`, `<BPM>:Y` | position (nm) |
| `<BPM>:SUM` | sum |
| `<BPM>:X_BUF`, `<BPM>:Y_BUF`, `<BPM>:SUM_BUF` | position/sum buffer, see table below |

| template | X/Y/SUM from | X_BUF/Y_BUF/SUM_BUF | size (`buf_nelm`) | valid samples |
|---|---|---|---|---|
| `libera-sppp` | `SA:SA_{X,Y,SUM}_MONITOR` (existing `libera_sa.template` records) | SA history, compress circular buffer (`db/bpm_hist.db`) | 1000 | `.NUSE` (`.OFF` next slot) |
| `libera-spe` | `<BPM>:spe.{x,y,sum}` element 0 (first bunch) | `<BPM>:spe.{x,y,sum}`, one sample per bunch (`db/bpm_common.db`) | 1024 | `.NORD` |
| `libera-bplus` | `<BPM>:sa.{X,Y,Sum}` | `<BPM>:ddc_synth.{X,Y,Sum}`, turn by turn on demand (`db/bpm_common.db`) | 10000 | `.NORD` |

The buffer size can be changed per ioc (`libera-sppp`) or per device (`libera-spe`, `libera-bplus`)
with `buf_nelm`. `bpm_common.db` reads the `liberaSignal` components through CP (Channel Access)
links, so `start.sh` exports `EPICS_CA_MAX_ARRAY_BYTES=5000000`.
