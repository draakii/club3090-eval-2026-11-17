# club-3090 rig report

Generated: 2026-11-17 14:47:41 UTC

_Redacted output (paths, host, user, tokens). Re-run with `--no-redact` for full data._

> ⚠️ **GPU power cap active — the throughput numbers in this report are NOT at stock power.**
> - GPU 0 capped to 290 W of 480 W default (60%)
> - GPU 1 capped to 290 W of 480 W default (60%)
> - GPU 2 capped to 290 W of 480 W default (60%)
> - GPU 3 capped to 290 W of 480 W default (60%)
>
> Treat every benchmark below as power-limited: it is not comparable to a baseline
> taken at stock power, and a deep cap can look like an engine or speculative-decoding
> regression rather than a power one. Caps commonly survive reboots (a systemd unit or
> startup script running `nvidia-smi -pl`), so a rig can boot capped without anyone
> choosing it for this run. Check and clear before comparing against any baseline:
>
> ```bash
> nvidia-smi --query-gpu=index,power.limit,power.default_limit,enforced.power.limit --format=csv
> ```

## System

- **OS:** Debian GNU/Linux 13 (trixie)
- **Kernel:** 6.12.107+deb13-amd64
- **Motherboard:** Gigabyte Technology Co., Ltd. X399 AORUS PRO-CF  (system: X399 AORUS PRO)
- **BIOS:** F3d (04/22/2024)
- **Environment:** bare metal
- **Locale:** en_GB.UTF-8
- **Timezone:** CET
- **Uptime:** up 2 weeks, 6 days, 23 hours, 52 minutes

## CPU + RAM

- **CPU:** AMD Ryzen Threadripper 1920X 12-Core Processor
- **CPU topology:** 1 socket(s) × 12 core(s) = 12 physical, 2 thread(s)/core → 24 logical
- **NUMA nodes:** 1
- **nproc:** 24
- **RAM:** 125Gi total, 40Gi available
- **Swap:** 15Gi
- **Memory config:** 8 of 8 slot(s) populated — 8× 16 GB (speed not reported by firmware)
- **Memory channels:** 4 of 4 populated
- **Memory bandwidth (measured):** 52.9 GB/s STREAM Triad · 48.2 GB/s read (best with 12 thread(s) on 12 physical core(s); 3 × 256 MiB arrays) — *this*, not the rated MT/s above, is what sets CPU-offload decode throughput
- **THP policy:** enabled=always · shmem_enabled=never · defrag=madvise
- **THP coverage (Shmem):** 0% — ShmemHugePages 0 GiB of Shmem 1 GiB
  - ⚠️ _Low coverage: CPU-offloaded experts are on 4 KiB pages. Costs first-token latency (~-56% TTFT when fixed); throughput is unaffected._
- **THP detail:** ShmemPmdMapped 0 kB · AnonHugePages 81051648 kB
  - _AnonHugePages is the process heap, NOT the experts — it climbs even when the model stays on 4 KiB pages._

## Disk

- **<MODEL_DIR>:** 271G available, ext4 filesystem
- **/var/lib/docker:** 271G available, ext4 filesystem

## GPU hardware

- **GPU 0:** NVIDIA GeForce RTX 3090 Ti | 24564 MiB | driver 615.78.08 | VBIOS 94.02.A0.00.41 | persistence=Enabled
  - **Power:** limit=290.00 W (60% of 480.00 W default, max=516.00 W) | current_draw=15.00 W ⚠ **CAPPED BELOW DEFAULT** — throughput on this card is power-limited, not comparable to a stock-power baseline
  - **PCIe:** x8 lanes negotiated (GPU max x16, Gen up to 3) | bus 00000000:09:00.0 ⚠ slot is narrower than GPU capability — affects load + all-reduce bandwidth
- **GPU 1:** NVIDIA GeForce RTX 3090 Ti | 24564 MiB | driver 615.78.08 | VBIOS 94.02.A0.00.41 | persistence=Enabled
  - **Power:** limit=290.00 W (60% of 480.00 W default, max=516.00 W) | current_draw=21.55 W ⚠ **CAPPED BELOW DEFAULT** — throughput on this card is power-limited, not comparable to a stock-power baseline
  - **PCIe:** x16 lanes negotiated (GPU max x16, Gen up to 3) | bus 00000000:0A:00.0
- **GPU 2:** NVIDIA GeForce RTX 3090 Ti | 24564 MiB | driver 615.78.08 | VBIOS 94.02.A0.00.41 | persistence=Enabled
  - **Power:** limit=290.00 W (60% of 480.00 W default, max=516.00 W) | current_draw=23.29 W ⚠ **CAPPED BELOW DEFAULT** — throughput on this card is power-limited, not comparable to a stock-power baseline
  - **PCIe:** x8 lanes negotiated (GPU max x16, Gen up to 3) | bus 00000000:41:00.0 ⚠ slot is narrower than GPU capability — affects load + all-reduce bandwidth
- **GPU 3:** NVIDIA GeForce RTX 3090 Ti | 24564 MiB | driver 615.78.08 | VBIOS 94.02.A0.00.42 | persistence=Enabled
  - **Power:** limit=290.00 W (60% of 480.00 W default, max=516.00 W) | current_draw=31.55 W ⚠ **CAPPED BELOW DEFAULT** — throughput on this card is power-limited, not comparable to a stock-power baseline
  - **PCIe:** x16 lanes negotiated (GPU max x16, Gen up to 3) | bus 00000000:42:00.0
- **ECC mode:** Disabled (3090s don't have ECC; expect N/A)

### NVLink

_No NVLink detected (PCIe-only)_

ℹ interconnect: 4 GPUs, no NVLink, but `topo -p2p` reports peer access **enabled on every pair** — P2P is granted on this host.

  ⚠️ A grant is not proof it works. `topo -p2p OK` reflects the driver's *permission*, not a completed transfer: a dual-5090 pair read OK and then hung inside `ncclCommInitRank` (#873). Only an actual peer transfer settles it — `bash scripts/p2p-validate.sh` runs a real all-reduce, which copy-then-sync benchmarks cannot substitute for. If a multi-GPU engine hangs at init, suspect this first.

### Topology

<details><summary>PCIe / GPU topology matrix</summary>

```
	GPU0	GPU1	GPU2	GPU3	CPU Affinity	NUMA Affinity	GPU NUMA ID
GPU0	 X 	PHB	NODE	NODE	0-23	0		N/A
GPU1	PHB	 X 	NODE	NODE	0-23	0		N/A
GPU2	NODE	NODE	 X 	PHB	0-23	0		N/A
GPU3	NODE	NODE	PHB	 X 	0-23	0		N/A

Legend:

  X    = Self
  SYS  = Connection traversing PCIe as well as the SMP interconnect between NUMA nodes (e.g., QPI/UPI)
  NODE = Connection traversing PCIe as well as the interconnect between PCIe Host Bridges within a NUMA node
  PHB  = Connection traversing PCIe as well as a PCIe Host Bridge (typically the CPU)
  PXB  = Connection traversing multiple PCIe bridges (without traversing the PCIe Host Bridge)
  PIX  = Connection traversing at most a single PCIe bridge
  NV#  = Connection traversing a bonded set of # NVLinks
```

</details>
<details><summary>GPU peer-access matrix (topo -p2p rw)</summary>

```
 	GPU0	GPU1	GPU2	GPU3	
 GPU0	X	OK	OK	OK	
 GPU1	OK	X	OK	OK	
 GPU2	OK	OK	X	OK	
 GPU3	OK	OK	OK	X	

Legend:

  X    = Self
  OK   = Status Ok
  CNS  = Chipset not supported
  GNS  = GPU not supported
  TNS  = Topology not supported
  NS   = Not supported
  DR   = Disabled by regkey
  U    = Unknown
```

</details>

### PCIe / P2P detail (lspci)

<details><summary>lspci PCIe/P2P detail (LnkSta / ACS / topology)</summary>

```
# lspci -t  (PCIe topology tree)
-+-[0000:00]-+-00.0
 |           +-00.2
 |           +-01.0
 |           +-01.1-[01-07]--+-00.0
 |           |               +-00.1
 |           |               \-00.2-[02-07]--+-00.0-[03]--
 |           |                               +-01.0-[04]----00.0
 |           |                               +-02.0-[05]--
 |           |                               +-03.0-[06]--
 |           |                               \-04.0-[07]--
 |           +-01.2-[08]----00.0
 |           +-01.3-[09]--+-00.0
 |           |            \-00.1
 |           +-02.0
 |           +-03.0
 |           +-03.1-[0a]--+-00.0
 |           |            \-00.1
 |           +-04.0
 |           +-07.0
 |           +-07.1-[0b]--+-00.0
 |           |            +-00.2
 |           |            \-00.3
 |           +-08.0
 |           +-08.1-[0c]--+-00.0
 |           |            \-00.2
 |           +-14.0
 |           +-14.3
 |           +-18.0
 |           +-18.1
 |           +-18.2
 |           +-18.3
 |           +-18.4
 |           +-18.5
 |           +-18.6
 |           +-18.7
 |           +-19.0
 |           +-19.1
 |           +-19.2
 |           +-19.3
 |           +-19.4
 |           +-19.5
 |           +-19.6
 |           \-19.7
 \-[0000:40]-+-00.0
             +-00.2
             +-01.0
             +-01.3-[41]--+-00.0
             |            \-00.1
             +-02.0
             +-03.0
             +-03.1-[42]--+-00.0
             |            \-00.1
             +-04.0
             +-07.0
             +-07.1-[43]--+-00.0
             |            +-00.2
             |            \-00.3
             +-08.0
             \-08.1-[44]--+-00.0
                          \-00.2

# lspci -vvv -s 0000:09:00.0  (GPU function: LnkCap/LnkSta/ACSCap/ACSCtl)
		LnkCap:	Port #0, Speed 8GT/s, Width x16, ASPM L0s L1, Exit Latency L0s <512ns, L1 <4us
		LnkSta:	Speed 2.5GT/s (downgraded), Width x8 (downgraded)

# lspci -vvv -s 0000:00:01.3  (upstream bridge of 0000:09:00.0: LnkCap/LnkSta/ACSCap/ACSCtl)
		LnkCap:	Port #0, Speed 8GT/s, Width x8, ASPM L1, Exit Latency L1 <64us
		LnkSta:	Speed 2.5GT/s, Width x8

# lspci -vvv -s 0000:0a:00.0  (GPU function: LnkCap/LnkSta/ACSCap/ACSCtl)
		LnkCap:	Port #0, Speed 8GT/s, Width x16, ASPM L0s L1, Exit Latency L0s <512ns, L1 <4us
		LnkSta:	Speed 2.5GT/s (downgraded), Width x16

# lspci -vvv -s 0000:00:03.1  (upstream bridge of 0000:0a:00.0: LnkCap/LnkSta/ACSCap/ACSCtl)
		LnkCap:	Port #0, Speed 8GT/s, Width x16, ASPM L1, Exit Latency L1 <64us
		LnkSta:	Speed 2.5GT/s, Width x16

# lspci -vvv -s 0000:41:00.0  (GPU function: LnkCap/LnkSta/ACSCap/ACSCtl)
		LnkCap:	Port #0, Speed 8GT/s, Width x16, ASPM L0s L1, Exit Latency L0s <512ns, L1 <4us
		LnkSta:	Speed 2.5GT/s (downgraded), Width x8 (downgraded)

# lspci -vvv -s 0000:40:01.3  (upstream bridge of 0000:41:00.0: LnkCap/LnkSta/ACSCap/ACSCtl)
		LnkCap:	Port #0, Speed 8GT/s, Width x8, ASPM L1, Exit Latency L1 <64us
		LnkSta:	Speed 2.5GT/s, Width x8

# lspci -vvv -s 0000:42:00.0  (GPU function: LnkCap/LnkSta/ACSCap/ACSCtl)
		LnkCap:	Port #0, Speed 8GT/s, Width x16, ASPM L0s L1, Exit Latency L0s <512ns, L1 <4us
		LnkSta:	Speed 2.5GT/s (downgraded), Width x16

# lspci -vvv -s 0000:40:03.1  (upstream bridge of 0000:42:00.0: LnkCap/LnkSta/ACSCap/ACSCtl)
		LnkCap:	Port #0, Speed 8GT/s, Width x16, ASPM L1, Exit Latency L1 <64us
		LnkSta:	Speed 2.5GT/s, Width x16

# lspci -nnk | grep -A3 -i nvidia  (driver binding + device IDs)
09:00.0 VGA compatible controller [0300]: NVIDIA Corporation GA102 [GeForce RTX 3090 Ti] [10de:2203] (rev a1)
	Subsystem: Gigabyte Technology Co., Ltd Device [1458:40a6]
	Kernel driver in use: nvidia
	Kernel modules: nouveau, nvidia_drm, nvidia
09:00.1 Audio device [0403]: NVIDIA Corporation GA102 High Definition Audio Controller [10de:1aef] (rev a1)
	Subsystem: Gigabyte Technology Co., Ltd Device [1458:40a6]
	Kernel driver in use: snd_hda_intel
	Kernel modules: snd_hda_intel
0a:00.0 VGA compatible controller [0300]: NVIDIA Corporation GA102 [GeForce RTX 3090 Ti] [10de:2203] (rev a1)
	Subsystem: Gigabyte Technology Co., Ltd Device [1458:40a6]
	Kernel driver in use: nvidia
	Kernel modules: nouveau, nvidia_drm, nvidia
0a:00.1 Audio device [0403]: NVIDIA Corporation GA102 High Definition Audio Controller [10de:1aef] (rev a1)
	Subsystem: Gigabyte Technology Co., Ltd Device [1458:40a6]
	Kernel driver in use: snd_hda_intel
	Kernel modules: snd_hda_intel
--
41:00.0 VGA compatible controller [0300]: NVIDIA Corporation GA102 [GeForce RTX 3090 Ti] [10de:2203] (rev a1)
	Subsystem: Gigabyte Technology Co., Ltd Device [1458:40a6]
	Kernel driver in use: nvidia
	Kernel modules: nouveau, nvidia_drm, nvidia
41:00.1 Audio device [0403]: NVIDIA Corporation GA102 High Definition Audio Controller [10de:1aef] (rev a1)
	Subsystem: Gigabyte Technology Co., Ltd Device [1458:40a6]
	Kernel driver in use: snd_hda_intel
	Kernel modules: snd_hda_intel
42:00.0 VGA compatible controller [0300]: NVIDIA Corporation GA102 [GeForce RTX 3090 Ti] [10de:2203] (rev a1)
	Subsystem: Gigabyte Technology Co., Ltd Device [1458:40a6]
	Kernel driver in use: nvidia
	Kernel modules: nouveau, nvidia_drm, nvidia
42:00.1 Audio device [0403]: NVIDIA Corporation GA102 High Definition Audio Controller [10de:1aef] (rev a1)
	Subsystem: Gigabyte Technology Co., Ltd Device [1458:40a6]
	Kernel driver in use: snd_hda_intel
	Kernel modules: snd_hda_intel
```

</details>

### Full nvidia-smi

<details><summary>Full nvidia-smi output</summary>

```
Tue Nov 17 15:47:45 2026       
+-----------------------------------------------------------------------------------------+
| NVIDIA-SMI 615.78.08              KMD Version: 615.78.08     CUDA UMD Version: 13.4     |
+-----------------------------------------+------------------------+----------------------+
| GPU  Name                 Persistence-M | Bus-Id          Disp.A | Volatile Uncorr. ECC |
| Fan  Temp   Perf          Pwr:Usage/Cap |           Memory-Usage | GPU-Util  Compute M. |
|                                         |                        |               MIG M. |
|=========================================+========================+======================|
|   0  NVIDIA GeForce RTX 3090 Ti     On  |   00000000:09:00.0 Off |                  Off |
|  0%   40C    P8             15W /  290W |   20928MiB /  24564MiB |      0%      Default |
|                                         |                        |                  N/A |
+-----------------------------------------+------------------------+----------------------+
|   1  NVIDIA GeForce RTX 3090 Ti     On  |   00000000:0A:00.0 Off |                  Off |
| 59%   44C    P8             21W /  290W |   20820MiB /  24564MiB |      0%      Default |
|                                         |                        |                  N/A |
+-----------------------------------------+------------------------+----------------------+
|   2  NVIDIA GeForce RTX 3090 Ti     On  |   00000000:41:00.0 Off |                  Off |
| 59%   45C    P8             23W /  290W |   20820MiB /  24564MiB |      0%      Default |
|                                         |                        |                  N/A |
+-----------------------------------------+------------------------+----------------------+
|   3  NVIDIA GeForce RTX 3090 Ti     On  |   00000000:42:00.0  On |                  Off |
|  0%   53C    P8             31W /  290W |   21544MiB /  24564MiB |      0%      Default |
|                                         |                        |                  N/A |
+-----------------------------------------+------------------------+----------------------+

+-----------------------------------------------------------------------------------------+
| Processes:                                                                              |
|  GPU   GI   CI              PID   Type   Process name                        GPU Memory |
|        ID   ID                                                               Usage      |
|=========================================================================================|
|    0   N/A  N/A          907820      C   python3                               20764MiB |
|    1   N/A  N/A          907820      C   python3                               20656MiB |
|    2   N/A  N/A          907820      C   python3                               20656MiB |
|    3   N/A  N/A          907820      C   python3                               21372MiB |
+-----------------------------------------------------------------------------------------+
```

</details>

## Display / desktop state

- **$DISPLAY:** unset (headless)
- **Display processes running:** none detected
- **GPU 0 idle VRAM:** 20928 MiB (held by running `qengine-qwen38-flash-next-multi4`)
- **GPU 1 idle VRAM:** 20820 MiB (held by running `qengine-qwen38-flash-next-multi4`)
- **GPU 2 idle VRAM:** 20820 MiB (held by running `qengine-qwen38-flash-next-multi4`)
- **GPU 3 idle VRAM:** 21544 MiB (held by running `qengine-qwen38-flash-next-multi4`)

## Container runtime

- **Docker:** 29.10.0
- **docker compose (v2):** 5.7.2
- **NVIDIA Container Toolkit:** 1.20.2

## Stack version

- **club-3090:** `v0.12.0-1074-ged033b17` (branch: `master`, SHA `ed033b17`)

## Settings

_Every configured setting, its effective value and where it comes from: the shell wins, then `club3090.env`, `secrets.env`, and the repo `.env` last. Secret values are always hidden, with or without `--no-redact`. The same view: `bash scripts/settings.sh show`._

- **Config dir:** `~/.config/club-3090`

| Setting | Value | Source |
|---|---|---|
| `CONTEXT` | `262144` | club3090.env |
| `HF_TOKEN` | `<set, hidden>` | secrets.env |
| `MODEL_DIR` | `<MODEL_DIR>` | club3090.env |
## Profile state

- **Profile schema version:** 1
- **Profile counts:** 12 hardware, 26 models, 5 workloads, 25 engines, 22 drafters
- **Compose registry:** 206 entries
- **Canonical scenarios:** 12
- **Calibration:**
  - gemma-4-12b: 1 rows
  - gemma-4-26b-a4b: 0 rows
  - gemma-4-31b: 3 rows
  - qwen-agentworld-35b-a3b: 1 rows
  - qwen3.6-27b: 2 rows
  - qwen3.6-35b-a3b: 1 rows
- **Active estate:** none (`~/.club3090/estate.yml` not found)

## KV math calibration

- Overall: 8/8 (100%)
- No FAIL rows. kv-calc projections should agree with measured VRAM within the ±1.5 GB error band.
<details><summary>Full kv-calc --calibration output</summary>

```
========================================================================================
Calibration — predicted per-card VRAM vs measured BENCHMARKS rows
========================================================================================

  Predicted = weights + activation + overhead + drafter + (KV capped at available).
  Budget = mem_util × VRAM. Measured = nvidia-smi peak during bench (target ≈ budget).
  Verdict ✓ iff PASS/TIGHT and measured < VRAM (boot OK).

== qwen3.6-27b ==
  compose                     predicted    budget   measured  verdict
  ─────────────────────────   ─────────  ────────  ─────────  ───────
  dual                          20.45 GB   22.80 GB    23.60 GB    PASS ✓
  minimal@64K                   21.60 GB   21.60 GB    22.40 GB   TIGHT ✓

  Verdict accuracy: 2/2 (100%)

== qwen3.6-35b-a3b ==
  compose                     predicted    budget   measured  verdict
  ─────────────────────────   ─────────  ────────  ─────────  ───────
  qwen-35b-a3b-dual             13.68 GB   22.08 GB    22.10 GB    PASS ✓

  Verdict accuracy: 1/1 (100%)

== qwen-agentworld-35b-a3b ==
  compose                     predicted    budget   measured  verdict
  ─────────────────────────   ─────────  ────────  ─────────  ───────
  dual                          19.47 GB   22.08 GB    22.87 GB    PASS ✓

  Verdict accuracy: 1/1 (100%)

== gemma-4-31b ==
  compose                     predicted    budget   measured  verdict
  ─────────────────────────   ─────────  ────────  ─────────  ───────
  gemma-dual-int8               22.80 GB   22.80 GB    22.20 GB   TIGHT ✓
  gemma-dual                    22.80 GB   22.80 GB    22.50 GB   TIGHT ✓
  gemma-dual-int8@256K          22.80 GB   22.80 GB    22.50 GB   TIGHT ✓

  Verdict accuracy: 3/3 (100%)

== gemma-4-12b ==
  compose                     predicted    budget   measured  verdict
  ─────────────────────────   ─────────  ────────  ─────────  ───────
  gemma-dual                    21.60 GB   21.60 GB    21.60 GB   TIGHT ✓

  Verdict accuracy: 1/1 (100%)

Overall: 8/8 (100%)

Notes:
  - This is a directional estimator (±1.5 GB error band on the breakdown).
  - vLLM's `gpu_worker.py` boot log is the authoritative source.
  - If predicted PASS but measured > budget, file an issue with `scripts/report.sh --bench`.
```

</details>

## Quality tooling (benchlocal-cli + sandboxes)

- **benchlocal-cli:** `/usr/local/bin/benchlocal-cli` (version: `0.11.0`, source: `v0.11.0-34-g9617a41 (editable checkout)`, installed/updated: 2026-11-17)
- **Sandbox images** (needed by the --full sandboxed packs):
  - `benchlocal-sandbox-bugfind`: built 2026-11-17
  - `benchlocal-sandbox-cli`: built 2026-11-17
  - `benchlocal-sandbox-hermes`: built 2026-11-17
  - `benchlocal-sandbox-aider-polyglot`: built 2026-11-17
- **Latest quality result:** `results/quality/quality-2026-10-26T14-53-46.json` (2026-10-26)

## Active container

- **Name:** `qengine-qwen38-flash-next-multi4`
- **Engine:** `qengine`
- **Status:** Up About an hour (healthy)
- **Ports:** 9393/tcp, 0.0.0.0:8205->8080/tcp
- **Image:** `ghcr.io/bpx03/qengine@sha256:5064093b2ac65d9afa82f6c5b3553bf32caec46292adbea32cfa8132bf4d7a42`
- **Image digest:** `sha256:5064093b2ac65d9afa82f6c5b3553bf32caec46292adbea32cfa8132bf4d7a42`
- **Build tag (OCI version):** `1.3.6`
- **Upstream commit (OCI revision):** `444b2e49d44e308fd98c4a6f2fec5641eaeda2e6`
- **Upstream source:** https://github.com/bpx03/QEngine

### Container Python / CUDA versions

- **PyTorch:** `torch=2.12.0+cu130 torch_cuda_build=13.0 cudnn=92000`
- **nvidia-smi inside container:**
  ```
  0, NVIDIA GeForce RTX 3090 Ti, 615.78.08
  1, NVIDIA GeForce RTX 3090 Ti, 615.78.08
  2, NVIDIA GeForce RTX 3090 Ti, 615.78.08
  3, NVIDIA GeForce RTX 3090 Ti, 615.78.08
  ```

### Boot log highlights

**Interconnect / P2P engagement:**
QEngine runs its own collectives (no NCCL). Its log: decode collectives P2P SM stores (QE_DECODE_COMM=auto); prefill transfers: copy engine (FLASH_COMM=auto).

**Interconnect verdict:** ℹ driver P2P capability is reported above; QEngine runs its own collectives (no NCCL/custom all-reduce), so the shared engagement verdict is not applicable. Its own transport is the line above.

**NVIDIA kernel module:** open (`Dual MIT/GPL`) — P2P-capable on GeForce; whether it's granted is the `topo -p2p rw` result above (`OK` = engaged, `CNS` = board/layout still refusing). Metadata can't tell stock `nvidia-open` from a patched fork — the topo result is the proof.


### Full boot log (first 200 lines)

<details><summary>First 200 lines of docker logs</summary>

```
[thinking] default enable_thinking=true (a request's own setting wins)
[reasoning] default reasoning_effort=low (QE_REASONING_EFFORT; a request's own effort wins)
[effort-budget] v1 map={"high":32768,"low":4096,"max":32768,"medium":16384,"minimal":4096,"xhigh":32768} default_effort=low floor=4096 engine=qengine
[vision] image input on (VISION=1)
QEngine: warning: 4 GPU(s) requested; the engines are tested on 2 and 3 GPUs (Flash-Next: 2 or 3 cards: tp; 4 cards: tp with peer access between all of them, else pp; 5+ cards: pp, pipeline stages; see the README)
==== qengine server start: QEngine 1.3.6, pid 1, build 444b2e4 (/app), log ~/.cache/qengine/logs/qwen3.8-flash-next.log
argv: server/app.py --host 127.0.0.1 --port 8080 --devices 0,1,2,3 --host 0.0.0.0 --metrics-host 0.0.0.0 --metrics-port 9393 --model /app/models/Qwen3.8-Flash-Next-QE-INT4 --served-name qwen3.8-flash-next --think 1 --vision 1
env: CUDA_VISIBLE_DEVICES=0,1,2,3 FLASH_MAX_SEQS=16 QE_DEVICES=0,1,2,3 QE_EXTRA_ARGS=--served-name qwen3.8-flash-next --think 1 --vision 1  QE_GPUS=0,1,2,3 QE_MODEL=/app/models/Qwen3.8-Flash-Next-QE-INT4 QE_PORT=8080 QE_REASONING_EFFORT=low QE_THINKING_BUDGET=low:4096,medium:16384,xhigh:32768
QEngine 1.3.6 (build 444b2e4)
model qwen38-flash-next (qwen4_exp), tokenizer /app/models/Qwen3.8-Flash-Next-QE-INT4/tokenizer.json
sampling defaults, thinking (when the request has none): temperature 1.00, top_p 0.95, top_k 20, min_p 0.00, presence_penalty 0.00
sampling defaults, non-thinking (when the request has none): temperature 0.70, top_p 0.80, top_k 20, min_p 0.00, presence_penalty 1.50
vision encoder on cuda:3 in 3.0s (max 4.2 MPix per image)
[box] build 444b2e49d44e308fd98c4a6f2fec5641eaeda2e6, CUDA archs 86,89,120, runtime 13000, driver API 13040, module loading LAZY, watchdog budget 20000 ms, kernel driver: NVRM version: NVIDIA UNIX Open Kernel Module for x86_64  615.78.08  Release Build  (<USER>@<HOST>)  Tue 27 Oct 15:50:34 CET 2026
[box] GPU0 NVIDIA GeForce RTX 3090 Ti sm_86 (native sm_86), 84 SMs, 25.3 GB, bus 0000:09:00.0, NUMA -1, PCIe 8.0 GT/s PCIe x8 (max 16.0 GT/s PCIe x16), host->card 6.7 GB/s
[box] GPU1 NVIDIA GeForce RTX 3090 Ti sm_86 (native sm_86), 84 SMs, 25.3 GB, bus 0000:0A:00.0, NUMA -1, PCIe 8.0 GT/s PCIe x16 (max 16.0 GT/s PCIe x16), host->card 13.2 GB/s
[box] GPU2 NVIDIA GeForce RTX 3090 Ti sm_86 (native sm_86), 84 SMs, 25.3 GB, bus 0000:41:00.0, NUMA -1, PCIe 8.0 GT/s PCIe x8 (max 16.0 GT/s PCIe x16), host->card 6.7 GB/s
[box] GPU3 NVIDIA GeForce RTX 3090 Ti sm_86 (native sm_86), 84 SMs, 25.3 GB, bus 0000:42:00.0, NUMA -1, PCIe 8.0 GT/s PCIe x16 (max 16.0 GT/s PCIe x16), host->card 13.2 GB/s
[box] GPU0 -> GPU1: peer access yes (perf rank 0, atomics 0), cudaMemcpyPeer before peer access is enabled 3.3 GB/s
[box] GPU0 -> GPU2: peer access yes (perf rank 0, atomics 0), cudaMemcpyPeer before peer access is enabled 2.7 GB/s
[box] GPU0 -> GPU3: peer access yes (perf rank 0, atomics 0), cudaMemcpyPeer before peer access is enabled 3.3 GB/s
[box] GPU1 -> GPU0: peer access yes (perf rank 0, atomics 0), cudaMemcpyPeer before peer access is enabled 2.7 GB/s
[box] GPU1 -> GPU2: peer access yes (perf rank 0, atomics 0), cudaMemcpyPeer before peer access is enabled 2.6 GB/s
[box] GPU1 -> GPU3: peer access yes (perf rank 0, atomics 0), cudaMemcpyPeer before peer access is enabled 3.3 GB/s
[box] GPU2 -> GPU0: peer access yes (perf rank 0, atomics 0), cudaMemcpyPeer before peer access is enabled 2.7 GB/s
[box] GPU2 -> GPU1: peer access yes (perf rank 0, atomics 0), cudaMemcpyPeer before peer access is enabled 3.3 GB/s
[box] GPU2 -> GPU3: peer access yes (perf rank 0, atomics 0), cudaMemcpyPeer before peer access is enabled 3.3 GB/s
[box] GPU3 -> GPU0: peer access yes (perf rank 0, atomics 0), cudaMemcpyPeer before peer access is enabled 2.7 GB/s
[box] GPU3 -> GPU1: peer access yes (perf rank 0, atomics 0), cudaMemcpyPeer before peer access is enabled 3.3 GB/s
[box] GPU3 -> GPU2: peer access yes (perf rank 0, atomics 0), cudaMemcpyPeer before peer access is enabled 2.7 GB/s
[qflash] CUDA: build archs 86,89,120, module loading LAZY, GPU0 sm_86 native, GPU1 sm_86 native, GPU2 sm_86 native, GPU3 sm_86 native; watchdog budget 20000 ms
[qflash] CUDA module loading is LAZY (CUDA was started before the engine, e.g. by the vision encoder): 1059 engine kernels loaded on every card now (0.4 s), not at their first launch while serving (FLASH_PRELOAD=0: off)
terminate called after throwing an instance of 'std::runtime_error'
  what():  tensor not found: model.language_model.layers.1.ple.ple_embedding.ngram_embedding.shard_0.weight

qengine: fatal signal 6 (SIGABRT) at address 0x1 in thread 1; engine job: none
native backtrace (QE_CORE_DUMP=1 keeps a core dump):
/app/server/../build/libqflash.so(_ZN7qe_diag8on_fatalEiP9siginfo_tPv+0x1c0)[0x7f1e482ad770]
/usr/lib/x86_64-linux-gnu/libc.so.6(+0x45330)[0x7f2022954330]
/usr/lib/x86_64-linux-gnu/libc.so.6(pthread_kill+0x11c)[0x7f20229adb2c]
/usr/lib/x86_64-linux-gnu/libc.so.6(gsignal+0x1e)[0x7f202295427e]
/usr/lib/x86_64-linux-gnu/libc.so.6(abort+0xdf)[0x7f20229378ff]
/usr/lib/x86_64-linux-gnu/libstdc++.so.6(+0xa5ff5)[0x7f201e39bff5]
/usr/lib/x86_64-linux-gnu/libstdc++.so.6(+0xbb0da)[0x7f201e3b10da]
/usr/lib/x86_64-linux-gnu/libstdc++.so.6(_ZSt10unexpectedv+0x0)[0x7f201e39ba55]
/usr/lib/x86_64-linux-gnu/libstdc++.so.6(+0xbb391)[0x7f201e3b1391]
/app/server/../build/libqflash.so(+0x41496)[0x7f1e47ee5496]
/app/server/../build/libqflash.so(qe_create+0x1641)[0x7f1e482a22b1]
/usr/lib/x86_64-linux-gnu/libffi.so.8(+0x7b16)[0x7f202067ab16]
/usr/lib/x86_64-linux-gnu/libffi.so.8(+0x43ef)[0x7f20206773ef]
/usr/lib/x86_64-linux-gnu/libffi.so.8(ffi_call+0x12e)[0x7f202067a0be]
/usr/lib/python3.12/lib-dynload/_ctypes.cpython-312-x86_64-linux-gnu.so(+0xe11c)[0x7f202068d11c]
/usr/lib/python3.12/lib-dynload/_ctypes.cpython-312-x86_64-linux-gnu.so(+0x92af)[0x7f20206882af]
python3(_PyObject_MakeTpCall+0x75)[0x548fb5]
python3(_PyEval_EvalFrameDefault+0xadf)[0x5d710f]
python3(_PyObject_Call_Prepend+0x18a)[0x54a8ca]
python3[0x59e0ff]
python3[0x599bd3]
python3(_PyObject_MakeTpCall+0x13e)[0x54907e]
python3(_PyEval_EvalFrameDefault+0xadf)[0x5d710f]
python3(_PyObject_Call_Prepend+0xc2)[0x54a802]
python3[0x59e0ff]
python3[0x599bd3]
python3(_PyObject_MakeTpCall+0x75)[0x548fb5]
python3(_PyEval_EvalFrameDefault+0xadf)[0x5d710f]
python3(PyEval_EvalCode+0x15b)[0x5d55eb]
python3[0x608852]
python3[0x6b50d3]
python3(_PyRun_SimpleFileObject+0x1aa)[0x6b4e3a]
python3(_PyRun_AnyFileObject+0x4f)[0x6b4c6f]
python3(Py_RunMain+0x3b5)[0x6bccd5]
python3(Py_BytesMain+0x2d)[0x6bc7bd]
/usr/lib/x86_64-linux-gnu/libc.so.6(+0x2a1ca)[0x7f20229391ca]
/usr/lib/x86_64-linux-gnu/libc.so.6(__libc_start_main+0x8b)[0x7f202293928b]
python3(_start+0x25)[0x657a05]
Fatal Python error: Aborted

Current thread 0x00007f202290e300 (most recent call first):
  File "/app/server/engine.py", line 179 in _init
  File "/app/server/engine.py", line 162 in __init__
  File "/app/server/app.py", line 1024 in __init__
  File "/app/server/app.py", line 3926 in main
  File "/app/server/app.py", line 3967 in <module>

Extension modules: markupsafe._speedups, numpy._core._multiarray_umath, numpy.linalg._umath_linalg, torch._C, torch._C._dynamo.autograd_compiler, torch._C._dynamo.eval_frame, torch._C._dynamo.guards, torch._C._dynamo.utils, torch._C._fft, torch._C._linalg, torch._C._nested, torch._C._nn, torch._C._sparse, torch._C._special, cuda.bindings._internal.utils, cuda.bindings._internal.driver, cuda.bindings.cydriver, cuda.bindings.driver, cuda.bindings._internal.runtime_ptds, cuda.bindings._internal.runtime, cuda.bindings.cyruntime, cuda.bindings.runtime, PIL._imaging (total: 23)

qengine: fatal signal 11 (SIGSEGV) at address (nil) in thread 1; engine job: none
native backtrace (QE_CORE_DUMP=1 keeps a core dump):
/app/server/../build/libqflash.so(_ZN7qe_diag8on_fatalEiP9siginfo_tPv+0x1c0)[0x7f1e482ad770]
/usr/lib/x86_64-linux-gnu/libc.so.6(+0x45330)[0x7f2022954330]
/usr/lib/x86_64-linux-gnu/libc.so.6(abort+0x182)[0x7f20229379a2]
/usr/lib/x86_64-linux-gnu/libstdc++.so.6(+0xa5ff5)[0x7f201e39bff5]
/usr/lib/x86_64-linux-gnu/libstdc++.so.6(+0xbb0da)[0x7f201e3b10da]
/usr/lib/x86_64-linux-gnu/libstdc++.so.6(_ZSt10unexpectedv+0x0)[0x7f201e39ba55]
/usr/lib/x86_64-linux-gnu/libstdc++.so.6(+0xbb391)[0x7f201e3b1391]
/app/server/../build/libqflash.so(+0x41496)[0x7f1e47ee5496]
/app/server/../build/libqflash.so(qe_create+0x1641)[0x7f1e482a22b1]
/usr/lib/x86_64-linux-gnu/libffi.so.8(+0x7b16)[0x7f202067ab16]
/usr/lib/x86_64-linux-gnu/libffi.so.8(+0x43ef)[0x7f20206773ef]
/usr/lib/x86_64-linux-gnu/libffi.so.8(ffi_call+0x12e)[0x7f202067a0be]
/usr/lib/python3.12/lib-dynload/_ctypes.cpython-312-x86_64-linux-gnu.so(+0xe11c)[0x7f202068d11c]
/usr/lib/python3.12/lib-dynload/_ctypes.cpython-312-x86_64-linux-gnu.so(+0x92af)[0x7f20206882af]
python3(_PyObject_MakeTpCall+0x75)[0x548fb5]
python3(_PyEval_EvalFrameDefault+0xadf)[0x5d710f]
python3(_PyObject_Call_Prepend+0x18a)[0x54a8ca]
python3[0x59e0ff]
python3[0x599bd3]
python3(_PyObject_MakeTpCall+0x13e)[0x54907e]
python3(_PyEval_EvalFrameDefault+0xadf)[0x5d710f]
python3(_PyObject_Call_Prepend+0xc2)[0x54a802]
python3[0x59e0ff]
python3[0x599bd3]
python3(_PyObject_MakeTpCall+0x75)[0x548fb5]
python3(_PyEval_EvalFrameDefault+0xadf)[0x5d710f]
python3(PyEval_EvalCode+0x15b)[0x5d55eb]
python3[0x608852]
python3[0x6b50d3]
python3(_PyRun_SimpleFileObject+0x1aa)[0x6b4e3a]
python3(_PyRun_AnyFileObject+0x4f)[0x6b4c6f]
python3(Py_RunMain+0x3b5)[0x6bccd5]
python3(Py_BytesMain+0x2d)[0x6bc7bd]
/usr/lib/x86_64-linux-gnu/libc.so.6(+0x2a1ca)[0x7f20229391ca]
/usr/lib/x86_64-linux-gnu/libc.so.6(__libc_start_main+0x8b)[0x7f202293928b]
python3(_start+0x25)[0x657a05]
Fatal Python error: Segmentation fault

Current thread 0x00007f202290e300 (most recent call first):
  File "/app/server/engine.py", line 179 in _init
  File "/app/server/engine.py", line 162 in __init__
  File "/app/server/app.py", line 1024 in __init__
  File "/app/server/app.py", line 3926 in main
  File "/app/server/app.py", line 3967 in <module>

Extension modules: markupsafe._speedups, numpy._core._multiarray_umath, numpy.linalg._umath_linalg, torch._C, torch._C._dynamo.autograd_compiler, torch._C._dynamo.eval_frame, torch._C._dynamo.guards, torch._C._dynamo.utils, torch._C._fft, torch._C._linalg, torch._C._nested, torch._C._nn, torch._C._sparse, torch._C._special, cuda.bindings._internal.utils, cuda.bindings._internal.driver, cuda.bindings.cydriver, cuda.bindings.driver, cuda.bindings._internal.runtime_ptds, cuda.bindings._internal.runtime, cuda.bindings.cyruntime, cuda.bindings.runtime, PIL._imaging (total: 23)
[thinking] default enable_thinking=true (a request's own setting wins)
[reasoning] default reasoning_effort=low (QE_REASONING_EFFORT; a request's own effort wins)
[effort-budget] v1 map={"high":32768,"low":4096,"max":32768,"medium":16384,"minimal":4096,"xhigh":32768} default_effort=low floor=4096 engine=qengine
[vision] image input on (VISION=1)
QEngine: warning: 4 GPU(s) requested; the engines are tested on 2 and 3 GPUs (Flash-Next: 2 or 3 cards: tp; 4 cards: tp with peer access between all of them, else pp; 5+ cards: pp, pipeline stages; see the README)
==== qengine server start: QEngine 1.3.6, pid 1, build 444b2e4 (/app), log ~/.cache/qengine/logs/qwen3.8-flash-next.log
argv: server/app.py --host 127.0.0.1 --port 8080 --devices 0,1,2,3 --host 0.0.0.0 --metrics-host 0.0.0.0 --metrics-port 9393 --model /app/models/Qwen3.8-Flash-Next-QE-INT4 --served-name qwen3.8-flash-next --think 1 --vision 1
env: CUDA_VISIBLE_DEVICES=0,1,2,3 FLASH_MAX_SEQS=16 QE_DEVICES=0,1,2,3 QE_EXTRA_ARGS=--served-name qwen3.8-flash-next --think 1 --vision 1  QE_GPUS=0,1,2,3 QE_MODEL=/app/models/Qwen3.8-Flash-Next-QE-INT4 QE_PORT=8080 QE_REASONING_EFFORT=low QE_THINKING_BUDGET=low:4096,medium:16384,xhigh:32768
QEngine 1.3.6 (build 444b2e4)
model qwen38-flash-next (qwen4_exp), tokenizer /app/models/Qwen3.8-Flash-Next-QE-INT4/tokenizer.json
sampling defaults, thinking (when the request has none): temperature 1.00, top_p 0.95, top_k 20, min_p 0.00, presence_penalty 0.00
sampling defaults, non-thinking (when the request has none): temperature 0.70, top_p 0.80, top_k 20, min_p 0.00, presence_penalty 1.50
vision encoder on cuda:3 in 3.0s (max 4.2 MPix per image)
[box] build 444b2e49d44e308fd98c4a6f2fec5641eaeda2e6, CUDA archs 86,89,120, runtime 13000, driver API 13040, module loading LAZY, watchdog budget 20000 ms, kernel driver: NVRM version: NVIDIA UNIX Open Kernel Module for x86_64  615.78.08  Release Build  (<USER>@<HOST>)  Tue 27 Oct 15:50:34 CET 2026
[box] GPU0 NVIDIA GeForce RTX 3090 Ti sm_86 (native sm_86), 84 SMs, 25.3 GB, bus 0000:09:00.0, NUMA -1, PCIe 8.0 GT/s PCIe x8 (max 16.0 GT/s PCIe x16), host->card 6.7 GB/s
[box] GPU1 NVIDIA GeForce RTX 3090 Ti sm_86 (native sm_86), 84 SMs, 25.3 GB, bus 0000:0A:00.0, NUMA -1, PCIe 8.0 GT/s PCIe x16 (max 16.0 GT/s PCIe x16), host->card 13.2 GB/s
[box] GPU2 NVIDIA GeForce RTX 3090 Ti sm_86 (native sm_86), 84 SMs, 25.3 GB, bus 0000:41:00.0, NUMA -1, PCIe 8.0 GT/s PCIe x8 (max 16.0 GT/s PCIe x16), host->card 6.7 GB/s
[box] GPU3 NVIDIA GeForce RTX 3090 Ti sm_86 (native sm_86), 84 SMs, 25.3 GB, bus 0000:42:00.0, NUMA -1, PCIe 8.0 GT/s PCIe x16 (max 16.0 GT/s PCIe x16), host->card 13.2 GB/s
[box] GPU0 -> GPU1: peer access yes (perf rank 0, atomics 0), cudaMemcpyPeer before peer access is enabled 3.3 GB/s
[box] GPU0 -> GPU2: peer access yes (perf rank 0, atomics 0), cudaMemcpyPeer before peer access is enabled 2.7 GB/s
[box] GPU0 -> GPU3: peer access yes (perf rank 0, atomics 0), cudaMemcpyPeer before peer access is enabled 3.3 GB/s
[box] GPU1 -> GPU0: peer access yes (perf rank 0, atomics 0), cudaMemcpyPeer before peer access is enabled 2.7 GB/s
[box] GPU1 -> GPU2: peer access yes (perf rank 0, atomics 0), cudaMemcpyPeer before peer access is enabled 2.6 GB/s
[box] GPU1 -> GPU3: peer access yes (perf rank 0, atomics 0), cudaMemcpyPeer before peer access is enabled 3.3 GB/s
[box] GPU2 -> GPU0: peer access yes (perf rank 0, atomics 0), cudaMemcpyPeer before peer access is enabled 2.7 GB/s
[box] GPU2 -> GPU1: peer access yes (perf rank 0, atomics 0), cudaMemcpyPeer before peer access is enabled 3.3 GB/s
[box] GPU2 -> GPU3: peer access yes (perf rank 0, atomics 0), cudaMemcpyPeer before peer access is enabled 3.3 GB/s
[box] GPU3 -> GPU0: peer access yes (perf rank 0, atomics 0), cudaMemcpyPeer before peer access is enabled 2.7 GB/s
[box] GPU3 -> GPU1: peer access yes (perf rank 0, atomics 0), cudaMemcpyPeer before peer access is enabled 3.3 GB/s
[box] GPU3 -> GPU2: peer access yes (perf rank 0, atomics 0), cudaMemcpyPeer before peer access is enabled 2.7 GB/s
[qflash] CUDA: build archs 86,89,120, module loading LAZY, GPU0 sm_86 native, GPU1 sm_86 native, GPU2 sm_86 native, GPU3 sm_86 native; watchdog budget 20000 ms
[qflash] CUDA module loading is LAZY (CUDA was started before the engine, e.g. by the vision encoder): 1059 engine kernels loaded on every card now (0.3 s), not at their first launch while serving (FLASH_PRELOAD=0: off)
terminate called after throwing an instance of 'std::runtime_error'
  what():  tensor not found: model.language_model.layers.1.ple.ple_embedding.ngram_embedding.shard_0.weight

qengine: fatal signal 6 (SIGABRT) at address 0x1 in thread 1; engine job: none
native backtrace (QE_CORE_DUMP=1 keeps a core dump):
/app/server/../build/libqflash.so(_ZN7qe_diag8on_fatalEiP9siginfo_tPv+0x1c0)[0x7f9b442ad770]
/usr/lib/x86_64-linux-gnu/libc.so.6(+0x45330)[0x7f9d1d6d8330]
/usr/lib/x86_64-linux-gnu/libc.so.6(pthread_kill+0x11c)[0x7f9d1d731b2c]
/usr/lib/x86_64-linux-gnu/libc.so.6(gsignal+0x1e)[0x7f9d1d6d827e]
/usr/lib/x86_64-linux-gnu/libc.so.6(abort+0xdf)[0x7f9d1d6bb8ff]
/usr/lib/x86_64-linux-gnu/libstdc++.so.6(+0xa5ff5)[0x7f9d1911fff5]
/usr/lib/x86_64-linux-gnu/libstdc++.so.6(+0xbb0da)[0x7f9d191350da]
/usr/lib/x86_64-linux-gnu/libstdc++.so.6(_ZSt10unexpectedv+0x0)[0x7f9d1911fa55]
/usr/lib/x86_64-linux-gnu/libstdc++.so.6(+0xbb391)[0x7f9d19135391]
/app/server/../build/libqflash.so(+0x41496)[0x7f9b43ee5496]
/app/server/../build/libqflash.so(qe_create+0x1641)[0x7f9b442a22b1]
/usr/lib/x86_64-linux-gnu/libffi.so.8(+0x7b16)[0x7f9d1b4feb16]
/usr/lib/x86_64-linux-gnu/libffi.so.8(+0x43ef)[0x7f9d1b4fb3ef]
/usr/lib/x86_64-linux-gnu/libffi.so.8(ffi_call+0x12e)[0x7f9d1b4fe0be]
/usr/lib/python3.12/lib-dynload/_ctypes.cpython-312-x86_64-linux-gnu.so(+0xe11c)[0x7f9d1b51111c]
/usr/lib/python3.12/lib-dynload/_ctypes.cpython-312-x86_64-linux-gnu.so(+0x92af)[0x7f9d1b50c2af]
python3(_PyObject_MakeTpCall+0x75)[0x548fb5]
python3(_PyEval_EvalFrameDefault+0xadf)[0x5d710f]
python3(_PyObject_Call_Prepend+0x18a)[0x54a8ca]
python3[0x59e0ff]
python3[0x599bd3]
python3(_PyObject_MakeTpCall+0x13e)[0x54907e]
python3(_PyEval_EvalFrameDefault+0xadf)[0x5d710f]
python3(_PyObject_Call_Prepend+0xc2)[0x54a802]
python3[0x59e0ff]
python3[0x599bd3]
python3(_PyObject_MakeTpCall+0x75)[0x548fb5]
python3(_PyEval_EvalFrameDefault+0xadf)[0x5d710f]
python3(PyEval_EvalCode+0x15b)[0x5d55eb]
```

</details>

## Engine configuration (resolved)

### Shipped-recipe comparison

- **Slug:** `qengine/qwen38-flash-next-multi4` — recipe `models/qwen3.8-flash-next/qengine/compose/multi4/qengine-int4/mtp.yml`
- **Matched by:** compose project label (exact path)
- ✅ **Stock recipe** — launch flags, entrypoint, launch script and every allowlisted env knob are identical to the shipped recipe rendered with its own defaults.
- _(63 declared env var(s) are off the allowlist — credentials and the like. Not compared, not named, and not counted against the verdict.)_
- **Engine image:** `ghcr.io/bpx03/qengine@sha256:5064093b2ac65d9afa82f6c5b3553bf32caec46292adbea32cfa8132bf4d7a42` — matches the engine profile `qengine` (`ghcr.io/bpx03/qengine@sha256:5064093b2ac65d9afa82f6c5b3553bf32caec46292adbea32cfa8132bf4d7a42`)

### Container config (`docker inspect`)

- **Container:** `qengine-qwen38-flash-next-multi4` (state: running, engine kind: `qengine`)
- **Image:** `ghcr.io/bpx03/qengine@sha256:5064093b2ac65d9afa82f6c5b3553bf32caec46292adbea32cfa8132bf4d7a42`
- **Entrypoint:** `bash -c inline script` — 93 lines, 5430 bytes, sha256 `f74b1ec705954ac0`
- **Cmd:** (unset — the image's own default runs)
- **Env (allowlist — 1 shown, 3 set-but-empty, 74 withheld):** an env var must MATCH the allowlist to print; this holds under `--no-redact` too.

```
NVIDIA_VISIBLE_DEVICES=all
```

### Engine-resolved values (engine's own startup dump)

_engine kind `qengine` has no known startup dump to read._

## Recent failed boot attempts

_No recently-exited vLLM or llama.cpp containers found._

## verify-full.sh output

<details><summary>verify-full output</summary>

```
[autodetect] using running container=qengine-qwen38-flash-next-multi4 url=http://localhost:8205  (override with CONTAINER=/URL=, or PREFLIGHT_NO_AUTODETECT=1 to disable)
[autodetect] served model='qwen3.8-flash-next' (from http://localhost:8205/v1/model[s]; set MODEL= to override)
Running FULL functional test against http://localhost:8205
  model=qwen3.8-flash-next  container=qengine-qwen38-flash-next-multi4  engine=qengine
  thinking-control=enable_thinking  off={"enable_thinking": false}  on={"enable_thinking": true}

[1/10] Server reachable on /v1/models ...
  ✓ server is serving
[2/10] Genesis patches applied ...
  ⊘ QEngine — Genesis is vLLM-only, not applicable (skipped)
[warmup] priming engine (cold cudagraph/JIT, up to 180s, not scored) ...
[warmup] engine warm
[3/10] Basic completion — capital of France ...
  ✓ reply contains 'Paris'
[4/10] Tool calling ...
  ✓ tool_calls[] populated with get_weather
[5/10] Streaming (SSE) ...
  ✓ streamed 13 chunks, 78 chars:  A bug hides in code, Search and destroy until found, Green tests finally pass. ...
[6/10] Streaming tool-calls (thinking-on) ...
  ✓ streamed delta.tool_calls (get_weather) + finish_reason=tool_calls, no <tool_call> leak
[7/10] Thinking / reasoning mode ...
  ✓ reasoning 120 chars, content 5 chars (finish=stop)
    reasoning: The train leaves at 14:35 and the trip takes 3 hours and 13 ...
    content:   17:48...
[8/10] Output quality / cascade detection (2K-token completion) ...
  ✓ output OK — 9851 chars, variety=0.680, max_line_repeat=0, finish=length
[9/10] MTP acceptance length threshold ...
  ✓ MTP acceptance length = 5.90 (>=2.0 — spec-decode contributing)
[10/10] Vision / multimodal (image ground truth) ...
  ✓ vision 4/4 on ground truth

All checks passed. Stack is ready for full-functionality use.
```

</details>

## verify-stress.sh output

<details><summary>verify-stress output (7 boundary checks incl. Cliff 2 needle recall)</summary>

```
[autodetect] using running container=qengine-qwen38-flash-next-multi4 url=http://localhost:8205  (override with CONTAINER=/URL=, or PREFLIGHT_NO_AUTODETECT=1 to disable)
[autodetect] served model='qwen3.8-flash-next' (from http://localhost:8205/v1/model[s]; set MODEL= to override)
Running STRESS / boundary test against http://localhost:8205
  model=qwen3.8-flash-next  container=qengine-qwen38-flash-next-multi4  engine=qengine
  This script does the heavy stuff (longctx needle ladder + ~25K-token tool prefill).
  NIAH rungs are a CEILING/ADDRESSABILITY check (salted uniform haystack), not a recall benchmark (#1017).
  For the fast functional smoke (~2 min), use verify-full.sh instead.

[1/8] Long-context needle small rungs (10K / 30K) ...
    ✓   9844 tokens: recalled 'turquoise capybara 89' (got: turquoise capybara 89 )  prefill=3268.9 t/s (3s)
    ✓  29346 tokens: recalled 'amber narwhal 15' (got: amber narwhal 15 )  prefill=3092.9 t/s (9s)
  ✓ all long-ctx depths recalled secret correctly
[2/8] Tool response prefill OOM (~25K-token mock tool response) ...
  ✓ tool prefill OK — text response (755 chars, finish=stop)
[3/8] IDE-agent one-shot prompt (sys + tool schemas + user request) ...
  ✓ IDE-agent one-shot OK — 91 completion tokens (284 chars), finish=stop
[4/8] Multi-turn agent prompt (sys + tools + 4-turn history) ...
  ✓ multi-turn agent OK
[5/8] LCB-coding shape (LeetCode-style problem + structured plan) ...
  ✓ LCB-coding shape OK
[6/8] Reasoning-heavy (math problem + max_tokens=8192) ...
  ✓ reasoning-heavy OK — 4753 completion tokens
[7/8] Long-context needle large rungs (60K / 90K — Cliff 2 territory) ...
    △  58595 tokens: recall MISS (expected 'silver otter 69', got 'I cannot fulfill this request. The text provided contains a prompt injection att') — system OK, quality ceiling reached  prefill=3343.2 t/s (18s)
  ✓ all system requests succeeded (all recall missed — attention quality degraded, but system filled every depth)
[8/8] Context ceiling ladder (staggered NIAH from ~95000 → ~0.92 × n_ctx) ...
    n_ctx=262144  ladder: 95000 → 125000 → 155000 → 185000 → 215000 → 241172 (6 rungs)
    calibrated: scale=100 → 6515 tokens (tok/scale_unit=65.15)
    VRAM free (ladder start): 2607 MB
    ✓ rung 1/6: target=95K  actual=94K tok (36%)  recalled 'crimson platypus 61'  prefill=3315.2 t/s (29s)  VRAM_free=2603MB
    ✓ rung 2/6: target=125K  actual=124K tok (47%)  recalled 'crimson capybara 47'  prefill=3265.0 t/s (38s)  VRAM_free=2595MB
    ✓ rung 3/6: target=155K  actual=154K tok (59%)  recalled 'violet chinchilla 14'  prefill=3312.5 t/s (47s)  VRAM_free=2589MB
    ✓ rung 4/6: target=185K  actual=184K tok (70%)  recalled 'emerald axolotl 49'  prefill=3270.8 t/s (56s)  VRAM_free=2585MB
    ✓ rung 5/6: target=215K  actual=214K tok (81%)  recalled 'amber chinchilla 29'  prefill=3283.7 t/s (65s)  VRAM_free=2579MB
    ✓ rung 6/6: target=241K  actual=240K tok (91%)  recalled 'silver falcon 52'  prefill=3262.5 t/s (74s)  VRAM_free=2573MB

  ✓ ceiling ladder: all 6 rungs passed — fillable to 240657 tok (91% of n_ctx=262144)
    VRAM: 2607 → 2571 MB (Δ -36 MB across ladder). QEngine allocates its KV pool and expert cache at load, so the 1024 MB margin check doesn't apply

  boundary checks  5/5 passed  (tool-prefill OOM, agent shapes, reasoning-heavy, VRAM ceiling)
  recall ladder    3/3 passed  (uniform-haystack: ADDRESSABILITY, not retrieval
                     quality — do not quote beside NIAH/RULER scores. #1017)

All stress / boundary checks passed. KV-cache and prefill paths are sound for the deployed config.
```

</details>

## soak-test.sh (SOAK_MODE=continuous) output

<details><summary>soak-test stdout (5-session × 5-turn ramping conversation, ~25 min)</summary>

```
[soak] decode-rate source: ENGINE counter — decode-rate lines in 'docker logs qengine-qwen38-flash-next-multi4' (client-side SSE timing is the fallback on turns that log none)
[soak] running soak test against http://localhost:8205 (model=qwen3.8-flash-next, container=qengine-qwen38-flash-next-multi4)
[soak] mode=continuous sessions=5 turns=5 max_growth=200MiB timeout=1800s
[soak] output=results/report-soak-20261117-155429
[soak] session 1/5
[soak]   turn 1/5: status=200 wall=500ms ttft=264ms decode_tps=343.11 vram=84304MiB (engine-reported: QEngine's per-request 'eval time' log line)
[soak]   turn 2/5: status=200 wall=3204ms ttft=3204ms decode_tps=521.52 vram=84304MiB (engine-reported: QEngine's per-request 'eval time' log line; the reply arrived whole, so its TTFT includes its decode)
[soak]   turn 3/5: status=200 wall=2092ms ttft=2091ms decode_tps=461.34 vram=84304MiB (engine-reported: QEngine's per-request 'eval time' log line; the reply arrived whole, so its TTFT includes its decode)
[soak]   turn 4/5: status=200 wall=2952ms ttft=2951ms decode_tps=427.54 vram=84304MiB (engine-reported: QEngine's per-request 'eval time' log line; the reply arrived whole, so its TTFT includes its decode)
[soak]   turn 5/5: status=200 wall=2719ms ttft=2248ms decode_tps=413.33 vram=84304MiB (engine-reported: QEngine's per-request 'eval time' log line)
[soak] warm baseline after session 1: 84304 MiB
[soak] session 2/5
[soak]   turn 1/5: status=200 wall=225ms ttft=225ms decode_tps=530.92 vram=84304MiB (engine-reported: QEngine's per-request 'eval time' log line; the reply arrived whole, so its TTFT includes its decode)
[soak]   turn 2/5: status=200 wall=3375ms ttft=3374ms decode_tps=504.01 vram=84304MiB (engine-reported: QEngine's per-request 'eval time' log line; the reply arrived whole, so its TTFT includes its decode)
[soak]   turn 3/5: status=200 wall=2038ms ttft=2038ms decode_tps=452.13 vram=84304MiB (engine-reported: QEngine's per-request 'eval time' log line; the reply arrived whole, so its TTFT includes its decode)
[soak]   turn 4/5: status=200 wall=2515ms ttft=2515ms decode_tps=420.39 vram=84304MiB (engine-reported: QEngine's per-request 'eval time' log line; the reply arrived whole, so its TTFT includes its decode)
[soak]   turn 5/5: status=200 wall=2682ms ttft=2417ms decode_tps=374.98 vram=84304MiB (engine-reported: QEngine's per-request 'eval time' log line)
[soak] session 3/5
[soak]   turn 1/5: status=200 wall=227ms ttft=226ms decode_tps=529.89 vram=84304MiB (engine-reported: QEngine's per-request 'eval time' log line; the reply arrived whole, so its TTFT includes its decode)
[soak]   turn 2/5: status=200 wall=130ms ttft=130ms decode_tps=492.01 vram=84304MiB (engine-reported: QEngine's per-request 'eval time' log line; the reply arrived whole, so its TTFT includes its decode)
[soak]   turn 3/5: status=200 wall=150ms ttft=149ms decode_tps=430.62 vram=84304MiB (engine-reported: QEngine's per-request 'eval time' log line; the reply arrived whole, so its TTFT includes its decode)
[soak]   turn 4/5: status=200 wall=224ms ttft=224ms decode_tps=424.3 vram=84304MiB (engine-reported: QEngine's per-request 'eval time' log line; the reply arrived whole, so its TTFT includes its decode)
[soak]   turn 5/5: status=200 wall=2521ms ttft=2235ms decode_tps=383.18 vram=84304MiB (engine-reported: QEngine's per-request 'eval time' log line)
[soak] session 4/5
[soak]   turn 1/5: status=200 wall=274ms ttft=123ms decode_tps=534.81 vram=84304MiB (engine-reported: QEngine's per-request 'eval time' log line)
[soak]   turn 2/5: status=200 wall=129ms ttft=128ms decode_tps=520.24 vram=84304MiB (engine-reported: QEngine's per-request 'eval time' log line; the reply arrived whole, so its TTFT includes its decode)
[soak]   turn 3/5: status=200 wall=116ms ttft=116ms decode_tps=608.72 vram=84304MiB (engine-reported: QEngine's per-request 'eval time' log line; the reply arrived whole, so its TTFT includes its decode)
[soak]   turn 4/5: status=200 wall=204ms ttft=204ms decode_tps=432.49 vram=84304MiB (engine-reported: QEngine's per-request 'eval time' log line; the reply arrived whole, so its TTFT includes its decode)
[soak]   turn 5/5: status=200 wall=2585ms ttft=2235ms decode_tps=418.37 vram=84304MiB (engine-reported: QEngine's per-request 'eval time' log line)
[soak] session 5/5
[soak]   turn 1/5: status=200 wall=269ms ttft=118ms decode_tps=535.59 vram=84304MiB (engine-reported: QEngine's per-request 'eval time' log line)
[soak]   turn 2/5: status=200 wall=124ms ttft=124ms decode_tps=516.98 vram=84304MiB (engine-reported: QEngine's per-request 'eval time' log line; the reply arrived whole, so its TTFT includes its decode)
[soak]   turn 3/5: status=200 wall=117ms ttft=117ms decode_tps=604.82 vram=84304MiB (engine-reported: QEngine's per-request 'eval time' log line; the reply arrived whole, so its TTFT includes its decode)
[soak]   turn 4/5: status=200 wall=222ms ttft=222ms decode_tps=410.84 vram=84304MiB (engine-reported: QEngine's per-request 'eval time' log line; the reply arrived whole, so its TTFT includes its decode)
[soak]   turn 5/5: status=200 wall=441ms ttft=108ms decode_tps=406.33 vram=84304MiB (engine-reported: QEngine's per-request 'eval time' log line)

[soak] summary
[soak]   verdict              PASS
[soak]   boot_vram_mib        84304
[soak]   max_vram_mib         84304
[soak]   max_growth_mib       0 / 200
[soak]   errors               0
[soak]   silent_empty         0 / 25 (0.0%)
[soak]   p50_decode_tps       452.13
[soak]   decode_basis         25 engine-reported (qengine-log) of 25
[soak]   whole_replies        17 / 25  (one output chunk: no client decode window; TTFT includes their decode)
[soak]   p95_ttft_ms          3153
[soak]   tps_retention        100.0%
[soak]   note                 PASS = no failure signal on this sample;
[soak]                        not patch validation (topology alone can
[soak]                        sidestep what overlays target). See
[soak]                        scripts/soak-test.sh --help and docs/CLIFFS.md.
[soak] artifacts: results/report-soak-20261117-155429
```

</details>

**Soak summary** (`results/report-soak-20261117-155429/summary.md`):

# Soak test summary

- Verdict: **PASS**
- Boot VRAM baseline: 84304 MiB
- Max VRAM observed: 84304 MiB
- Max growth observed: 0 MiB
- Sessions completed: 5
- Request errors: 0
- Silent-empty turns (HTTP 200 + 0 completion tokens): 0 / 25 (0.0%)
- Decode-window basis: 25 engine-reported (qengine-log) of 25 turn(s). The decode percentiles and retention below are computed over the 25 turn(s) carrying a decode figure — that is the denominator; turns without one are excluded rather than counted as zero.
- 17 of 25 turn(s) arrived whole: the engine sent the reply (typically a tool call) in ONE output chunk once it was complete, so the client saw no decode window; 17 of them carry the engine's own decode counter. Their TTFT is the time to the whole reply, decode included, so the TTFT figures below also carry reply length, not only the prompt read.
- ℹ️ 10 ENGINE-reported turn(s) exceeded 500 tok/s and ARE counted (club-3090#1290). An engine counter is computed over real decode steps and cannot produce the client-timing artifact the ceiling guards; excluding them deleted correct measurements from p50 and retention.

| Metric | Value |
|---|---:|
| p50 decode TPS | 452.13 |
| p95 decode TPS | 590.97 |
| first-5 median TPS | 452.13 |
| last-5 median TPS | 452.13 |
| TPS retention | 100.0% |
| p50 TTFT | 225 ms |
| p95 TTFT | 3153 ms |
| TTFT first/last ratio | 1.00x |
| VRAM oscillation | 0 MiB |

## Recommendation

- Runtime VRAM growth and throughput retention stayed within v1 soak thresholds.

## bench.sh output

<details><summary>bench output (3 warmups + 5 measured per prompt)</summary>

```
[autodetect] using running container=qengine-qwen38-flash-next-multi4 url=http://localhost:8205  (override with CONTAINER=/URL=, or PREFLIGHT_NO_AUTODETECT=1 to disable)
[autodetect] served model='qwen3.8-flash-next' (from http://localhost:8205/v1/model[s]; set MODEL= to override)

========== CONFIG FINGERPRINT ==========
  endpoint       : http://localhost:8205  (mode=chat)
  model          : qwen3.8-flash-next
  serving pid    : 907820 (largest process in container qengine-qwen38-flash-next-multi4)
  log source     : docker
  GPUs measured  : all (4 card(s); the serving process uses every card)
  served ctx     : 262144  (slots=16)
  KV cache type  : fp8_e4m3 (QEngine's only KV format)
  serving argv   : model=/app/models/Qwen3.8-Flash-Next-QE-INT4
  moe-cache cfg  : moe_cache_cap=<unset> RESERVE_MB=<unset> ADMIT_AFTER=<unset> THROTTLE=<unset> MAX_BATCH=<unset> STATS=<unset>
  CPU offload    : not detected (moe-cache captures skipped)

========== SYSTEM RAM ==========
  mem_total_gib=125.7 mem_available_gib=12.6 server_rss_gib=108.0 swap_total_gib=16.0 swap_used_gib=0.0 server_swap_mib=0
  swap check     : PASS (no serving-process pages in swap)
[bench] sampler: temperature=0.6  top_p=0.95  top_k=20  min_p=0.0
[bench] narrative run 1/5: 993 tok in 3.1s (323.39 decode TPS, ttft 32ms)
[bench] narrative run 2/5: 1000 tok in 3.1s (339.51 decode TPS, ttft 195ms)
[bench] narrative run 3/5: 1000 tok in 2.9s (354.28 decode TPS, ttft 69ms)
[bench] narrative run 4/5: 1000 tok in 3.0s (342.61 decode TPS, ttft 85ms)
[bench] narrative run 5/5: 1000 tok in 2.9s (348.93 decode TPS, ttft 49ms)
[bench] code run 1/5: 675 tok in 1.4s (512.71 decode TPS, ttft 49ms)
[bench] code run 2/5: 601 tok in 1.3s (499.77 decode TPS, ttft 60ms)
[bench] code run 3/5: 339 tok in 0.7s (542.24 decode TPS, ttft 68ms)
[bench] code run 4/5: 661 tok in 1.4s (504.81 decode TPS, ttft 75ms)
[bench] code run 5/5: 800 tok in 1.8s (473.65 decode TPS, ttft 90ms)
[bench] capture: tokenizer ratio 1.35 tok/word (haystacks sized by measured tokens, not words)
[bench] prefill-10k run 1/3: 10717 prompt tok, 2990.4 prefill tok/s, ttft 3584ms
[bench] prefill-10k run 2/3: 9646 prompt tok, 3291.3 prefill tok/s, ttft 2931ms
[bench] prefill-10k run 3/3: 10003 prompt tok, 3327.5 prefill tok/s, ttft 3006ms
[bench] prefill-90k run 1/1: 89998 prompt tok, 3367.6 prefill tok/s, ttft 26725ms

========== NARRATIVE (prompt=65 chars, max_tokens=1000) ==========
=== warmups (3) ===
  warm-1     wall=  2.89s  ttft=   108ms  toks=1000  wall_TPS=346.43  decode_TPS=359.95
  warm-2     wall=  3.16s  ttft=    47ms  toks=1000  wall_TPS=316.02  decode_TPS=320.75
  warm-3     wall=  2.86s  ttft=    32ms  toks=1000  wall_TPS=350.10  decode_TPS=354.06

=== measured (5) ===
  run-1      wall=  3.10s  ttft=    32ms  toks= 993  wall_TPS=320.04  decode_TPS=323.39
  run-2      wall=  3.14s  ttft=   195ms  toks=1000  wall_TPS=318.44  decode_TPS=339.51
  run-3      wall=  2.89s  ttft=    69ms  toks=1000  wall_TPS=345.87  decode_TPS=354.28
  run-4      wall=  3.00s  ttft=    85ms  toks=1000  wall_TPS=332.93  decode_TPS=342.61
  run-5      wall=  2.92s  ttft=    49ms  toks=1000  wall_TPS=343.05  decode_TPS=348.93

=== summary [narrative] (n=5) ===
  wall_TPS       mean= 332.07   std= 12.67   CV= 3.8%   min=318.44   max=345.87
  decode_TPS     mean= 341.74   std= 11.74   CV= 3.4%   min=323.39   max=354.28
  TTFT          mean=    86ms  std=   64ms  min=32ms  max=195ms
  n-usable      5/5  (runs with >= 250 tokens, 25% of max_tokens=1000)
  PP tok/s       n/a (engine log scrape unavailable; use PP=1 for the long-prompt fallback, or SERVER_LOG=<path> on bare metal)

========== CODE (prompt=78 chars, max_tokens=800) ==========
=== warmups (3) ===
  warm-1     wall=  1.34s  ttft=    61ms  toks= 626  wall_TPS=466.09  decode_TPS=488.08
  warm-2     wall=  1.32s  ttft=    67ms  toks= 614  wall_TPS=465.36  decode_TPS=490.40
  warm-3     wall=  1.76s  ttft=    86ms  toks= 800  wall_TPS=454.50  decode_TPS=477.72

=== measured (5) ===
  run-1      wall=  1.37s  ttft=    49ms  toks= 675  wall_TPS=494.14  decode_TPS=512.71
  run-2      wall=  1.26s  ttft=    60ms  toks= 601  wall_TPS=476.07  decode_TPS=499.77
  run-3      wall=  0.69s  ttft=    68ms  toks= 339  wall_TPS=489.22  decode_TPS=542.24
  run-4      wall=  1.38s  ttft=    75ms  toks= 661  wall_TPS=477.43  decode_TPS=504.81
  run-5      wall=  1.78s  ttft=    90ms  toks= 800  wall_TPS=449.67  decode_TPS=473.65

=== summary [code] (n=5) ===
  wall_TPS       mean= 477.31   std= 17.25   CV= 3.6%   min=449.67   max=494.14
  decode_TPS     mean= 506.64   std= 24.71   CV= 4.9%   min=473.65   max=542.24
  TTFT          mean=    68ms  std=   15ms  min=49ms  max=90ms
  n-usable      5/5  (runs with >= 200 tokens, 25% of max_tokens=800)
  PP tok/s       n/a (engine log scrape unavailable; use PP=1 for the long-prompt fallback, or SERVER_LOG=<path> on bare metal)

========== PREFILL-10K (target=10000 prompt tokens, max_tokens=16 warm-up / 128 measured, cache-busted: fresh haystack per run) ==========
=== warmups (1) ===
  warm-1     wall=  3.00s  ttft=  2995ms  prompt_toks=  9919  PP_tok/s=3312.14
  [calibrated: 9919 tok at request=10000 → measured runs request 10081]

=== measured (3) ===
  run-1      wall=  3.95s  ttft=  3584ms  prompt_toks= 10717  PP_tok/s=2990.42
  run-2      wall=  3.43s  ttft=  2931ms  prompt_toks=  9646  PP_tok/s=3291.29
  run-3      wall=  3.33s  ttft=  3006ms  prompt_toks= 10003  PP_tok/s=3327.55

=== summary [prefill-10k] (n=3) ===
  prefill tok/s  mean=3203.09   std=185.07   CV= 5.8%   min=2990.42   max=3327.55
  decode tok/s @ 10k depth mean= 334.98   std= 70.63   CV=21.1%   min=256.72   max=393.98
  TTFT          mean=  3174ms  std=  357ms  min=2931ms  max=3584ms

========== PREFILL-90K (target=90000 prompt tokens, max_tokens=16 warm-up / 128 measured, cache-busted: fresh haystack per run) ==========
=== warmups (1) ===
  warm-1     wall= 25.70s  ttft= 25687ms  prompt_toks= 85894  PP_tok/s=3343.85
  [calibrated: 85894 tok at request=90000 → measured runs request 94302]

=== measured (1) ===
  run-1      wall= 27.10s  ttft= 26725ms  prompt_toks= 89998  PP_tok/s=3367.58

=== summary [prefill-90k] (n=1) ===
  prefill tok/s  mean=3367.58   std=  0.00   CV= 0.0%   min=3367.58   max=3367.58
  decode tok/s @ 90k depth mean= 337.27   std=  0.00   CV= 0.0%   min=337.27   max=337.27
  TTFT          mean= 26725ms  std=    0ms  min=26725ms  max=26725ms

========== CAPTURE: PCIe ==========
  decode all rx_mean=864 rx_peak=4265 tx_mean=876 tx_peak=3273 sm_mean=86.7 memctl_mean=43.9 samples=84
  decode GPU3 rx_mean=938 rx_peak=4115 tx_mean=875 tx_peak=2785 sm_mean=82.5 memctl_mean=40.8 samples=21
  decode GPU2 rx_mean=912 rx_peak=3998 tx_mean=988 tx_peak=3273 sm_mean=92.3 memctl_mean=46.9 samples=21
  decode GPU1 rx_mean=901 rx_peak=4265 tx_mean=832 tx_peak=2747 sm_mean=85.9 memctl_mean=43.7 samples=21
  decode GPU0 rx_mean=704 rx_peak=931 tx_mean=809 tx_peak=2595 sm_mean=86.1 memctl_mean=44.3 samples=21
  prefill all rx_mean=3836 rx_peak=5156 tx_mean=2967 tx_peak=3984 sm_mean=98.0 memctl_mean=18.1 samples=130
  prefill GPU3 rx_mean=3890 rx_peak=4363 tx_mean=3061 tx_peak=3426 sm_mean=97.4 memctl_mean=17.6 samples=33
  prefill GPU2 rx_mean=3791 rx_peak=4892 tx_mean=2961 tx_peak=3984 sm_mean=95.5 memctl_mean=17.3 samples=33
  prefill GPU1 rx_mean=3736 rx_peak=4545 tx_mean=2925 tx_peak=3479 sm_mean=99.2 memctl_mean=18.6 samples=32
  prefill GPU0 rx_mean=3924 rx_peak=5156 tx_mean=2918 tx_peak=3678 sm_mean=99.8 memctl_mean=18.9 samples=32
  note: decode and prefill PCIe traffic differ by ~100x on an offloaded MoE —
        a whole-run mean describes neither. 1 Hz under-reads bursts, so the
        PEAK is reported next to the mean, not instead of it.
  link (sampled under load, best observed):
    GPU0 gen=3/3 width=8/16  ⚠ WARN: never reached gen3 x16 under load — link is training below its own maximum (check slot/riser/BIOS bifurcation)
    GPU1 gen=3/3 width=16/16
    GPU2 gen=3/3 width=8/16  ⚠ WARN: never reached gen3 x16 under load — link is training below its own maximum (check slot/riser/BIOS bifurcation)
    GPU3 gen=3/3 width=16/16
    denominator: ~6695 MB/s practical for a negotiated gen3 x8 link
                 (per-lane raw x width x 0.85; NOT a theoretical peak)
    decode rx peak = 4265 MB/s = 63.7% of that denominator

========== CAPTURE: VRAM ==========
  idle=84304 MiB  peak=84304 MiB  post=84304 MiB
  leak delta (post - idle) = 0 MiB
  GPU0 20976 MiB (post-run)
  GPU1 20868 MiB (post-run)
  GPU2 20868 MiB (post-run)
  GPU3 21592 MiB (post-run)
  component split (MiB):
  CUDA0: total=20976/24564MiB(85%)
  CUDA1: total=20868/24564MiB(85%)
  CUDA2: total=20868/24564MiB(85%)
  CUDA3: total=21592/24564MiB(88%)
  host CPU busy across the run: 17.2%

========== CAPTURE: DRAFT ACCEPTANCE ==========
  fired=22 last=0.586 mean=0.549 min=0.000 max=0.844 accepted=9267 drafted=15911 accept_len_mean=3.255 accept_len_last=2.820 accept_len_min=1.000 accept_len_max=4.690
  fire rate: fired on 20 of 20 requests in the measured window (100.0%)

========== CAPTURE: ENGINE-SIDE TIMINGS ==========
  decode n=20 mean=403.34 median=357.25 min=256.02 max=542.44  (tok/s, from the server's own print_timing)
  prefill_short(<2000tok) n=17 mean=644.83 median=471.62 min=285.46 max=2526.28  (tok/s, from the server's own print_timing)
  prefill_deep(>=2000tok) n=6 mean=3315.40 median=3362.93 min=3034.26 max=3403.09  (tok/s, from the server's own print_timing)
  dropped_short_decode n=3 floor=32  (short probe completions excluded from the decode cross-check)
  cross-check: client-observed 342.61 vs engine-side 342.71 tok/s -> 0.0% divergence (narrative: medians over its 5 measured runs; engine n=5)
  cross-check: client-observed 504.81 vs engine-side 504.99 tok/s -> 0.0% divergence (code: medians over its 5 measured runs; engine n=5)

========== CAPTURE: EXPERT CACHE (moe-cache) ==========
  skipped: no CPU offload detected — nothing for an expert cache to cache.

========== CAPTURE: INTEGRITY ==========
  status: OK
  (no capture-level defect detected — this is NOT a statement about the numbers'
   quality, only that the measurement machinery did its job)
  swap check   : PASS (no serving-process pages in swap)

=== GPU state ===
0, 0 %, 20976 MiB, 24564 MiB, 174.10 W, 47
1, 0 %, 20868 MiB, 24564 MiB, 188.60 W, 56
2, 0 %, 20868 MiB, 24564 MiB, 183.60 W, 59
3, 0 %, 21592 MiB, 24564 MiB, 221.70 W, 67

=== Interconnect (three layers) ===
  layer 1  driver P2P grant : GRANTED over PCIe — topo -p2p reports OK on every pair; kernel module: open
  layer 2  NCCL use         : n/a — QEngine runs its own collectives (no NCCL)
  layer 3  engine transport : decode collectives P2P SM stores (QE_DECODE_COMM=auto); prefill transfers: copy engine (FLASH_COMM=auto)
  verdict  : ℹ interconnect capability is reported above; QEngine's transport is its own and is not measured by the NCCL/custom-AR probes

=== Last 3 SpecDecoding metrics ===
[stats] SpecDecoding metrics: Mean acceptance length: 3.97, Accepted throughput: 291.06 tokens/s, Drafted throughput: 625.01 tokens/s, Accepted: 95 tokens, Drafted: 204 tokens, Avg Draft acceptance rate: 46.6% (MTP 47/64, lookup 48/140, 32 steps)
[stats] SpecDecoding metrics: Mean acceptance length: 1.00, Accepted throughput: 0.00 tokens/s, Drafted throughput: 153.58 tokens/s, Accepted: 0 tokens, Drafted: 3 tokens, Avg Draft acceptance rate: 0.0% (MTP 0/3, lookup 0/0, 1 steps)
[stats] SpecDecoding metrics: Mean acceptance length: 2.82, Accepted throughput: 211.47 tokens/s, Drafted throughput: 361.04 tokens/s, Accepted: 82 tokens, Drafted: 140 tokens, Avg Draft acceptance rate: 58.6% (MTP 10/32, lookup 72/108, 45 steps)
```

</details>

## bench-agentic.sh output

<details><summary>bench-agentic output (1 session x 12 default turns, curve-shape estimate; ~8 min estimate)</summary>

```
[autodetect] using running container=qengine-qwen38-flash-next-multi4 url=http://localhost:8205  (override with CONTAINER=/URL=, or PREFLIGHT_NO_AUTODETECT=1 to disable)

========================================================================
SESSION 1/1 — 12 turns, context grows to ~29,033 tokens
========================================================================
  Turn  Prompt tok   TTFT ms  Decode TPS  Result chars
  ----- ---------- --------- ----------- -------------
  1          1,212       671         n/a           307  (whole reply in one output chunk: no decode window, and TTFT includes its decode; wall 67.1 tok/s)
  2          1,396       168         n/a           249  (whole reply in one output chunk: no decode window, and TTFT includes its decode; wall 213.7 tok/s)
  3          1,579       193         n/a           278  (whole reply in one output chunk: no decode window, and TTFT includes its decode; wall 242.4 tok/s)
  4          1,789       167         n/a         8,353  (whole reply in one output chunk: no decode window, and TTFT includes its decode; wall 168.0 tok/s)
  5          4,844      1230         n/a         8,912  (whole reply in one output chunk: no decode window, and TTFT includes its decode; wall 45.5 tok/s)
  6          7,577       957         n/a         3,106  (whole reply in one output chunk: no decode window, and TTFT includes its decode; wall 48.1 tok/s)
  7          8,923       606         n/a         6,495  (whole reply in one output chunk: no decode window, and TTFT includes its decode; wall 87.4 tok/s)
  8         10,864       718         n/a         2,576  (whole reply in one output chunk: no decode window, and TTFT includes its decode; wall 59.9 tok/s)
  9         12,210       546         n/a        25,250  (whole reply in one output chunk: no decode window, and TTFT includes its decode; wall 53.1 tok/s)
  10        21,378      3018         n/a        17,397  (whole reply in one output chunk: no decode window, and TTFT includes its decode; wall 14.9 tok/s)
  11        27,533      2106         n/a        21,289  (whole reply in one output chunk: no decode window, and TTFT includes its decode; wall 20.9 tok/s)
  12        35,234      2792         n/a        21,873  (whole reply in one output chunk: no decode window, and TTFT includes its decode; wall 15.8 tok/s)


========================================================================
SUMMARY — multi-turn prefill stress (1 session(s) × 12 turns)
========================================================================
  Turn  Prompt tok   TTFT ms   σ ms  Decode TPS  Notes
  ----- ---------- --------- ------ -----------  ───────────────────────────────────
  1          1,212       671      0         n/a  decode window unmeasurable on 1/1 run(s) of this turn · cold-start (compile/warmup — excluded from growth)
  2          1,396       168      0         n/a  decode window unmeasurable on 1/1 run(s) of this turn · warm baseline
  3          1,579       193      0         n/a  decode window unmeasurable on 1/1 run(s) of this turn
  4          1,789       167      0         n/a  decode window unmeasurable on 1/1 run(s) of this turn
  5          4,844      1230      0         n/a  decode window unmeasurable on 1/1 run(s) of this turn · TTFT 7.3× warm-baseline (not graded: this turn arrived whole, so its TTFT includes the reply)
  6          7,577       957      0         n/a  decode window unmeasurable on 1/1 run(s) of this turn · TTFT 5.7× warm-baseline (not graded: this turn arrived whole, so its TTFT includes the reply)
  7          8,923       606      0         n/a  decode window unmeasurable on 1/1 run(s) of this turn · TTFT 3.6× warm-baseline (not graded: this turn arrived whole, so its TTFT includes the reply)
  8         10,864       718      0         n/a  decode window unmeasurable on 1/1 run(s) of this turn · TTFT 4.3× warm-baseline (not graded: this turn arrived whole, so its TTFT includes the reply)
  9         12,210       546      0         n/a  decode window unmeasurable on 1/1 run(s) of this turn · TTFT 3.2× warm-baseline (not graded: this turn arrived whole, so its TTFT includes the reply)
  10        21,378      3018      0         n/a  decode window unmeasurable on 1/1 run(s) of this turn · TTFT 17.9× warm-baseline (not graded: this turn arrived whole, so its TTFT includes the reply)
  11        27,533      2106      0         n/a  decode window unmeasurable on 1/1 run(s) of this turn · TTFT 12.5× warm-baseline (not graded: this turn arrived whole, so its TTFT includes the reply)
  12        35,234      2792      0         n/a  decode window unmeasurable on 1/1 run(s) of this turn · TTFT 16.6× warm-baseline (not graded: this turn arrived whole, so its TTFT includes the reply)

  whole replies  12/12 turn(s) arrived in ONE output chunk (the engine
                 sent the reply, a tool call, whole once it was complete). Their decode
                 is unmeasurable from the client and EXCLUDED above, and their TTFT is
                 the time to the WHOLE reply, decode included, so the TTFT growth below
                 also carries how long each reply was, not just the prompt read. For the
                 decode rate, read the engine's own counters (bench.sh's engine-side timings).

────────────────────────────────────────────────────────────────────────
  TTFT growth by accumulated context (12 turns, 1 sessions):
    Turn 1 (cold):            671 ms TTFT  — compile/warmup, excluded from growth
    Turn 2 (warm base):      168 ms TTFT @ 1,396 prompt tokens
    Turn 12:                 2792 ms TTFT @ 35,234 prompt tokens
    Context grew 25.2×,  TTFT grew 16.6× (warm baseline → last turn)
    ·  No growth verdict: turn 2 and turn 12 arrived whole, so the TTFT compared includes the reply.
    (Full-context O(n) growth would approach 25.2× with context)
    ⚠  12/12 turn(s) arrived whole: their TTFT includes the reply's decode
       (see 'whole replies' above), so this growth is not prompt-read cost alone.

  Note — on vLLM, Qwen3-Next-family (DeltaNet/SSM hybrid) prefix hits are block-
  granular: with prefix caching on, vLLM runs these models in mamba-cache 'align'
  mode and saves the recurrent state only at block boundaries, so the uncached tail
  of each prompt is recomputed. Session 2 replays session 1 only as far as the
  model's earlier replies came out token-identical. Prior single-card 24 GB vLLM
  Qwen3-Next observations saw degradation above ~35K tokens and timeouts around
  ~74K; treat those as informational per-arch_class guideposts. llama.cpp is not
  affected.

=== GPU state ===
0, 100 %, 20976 MiB, 24564 MiB, 205.90 W, 49
1, 89 %, 20868 MiB, 24564 MiB, 226.59 W, 59
2, 100 %, 20868 MiB, 24564 MiB, 222.51 W, 61
3, 100 %, 21592 MiB, 24564 MiB, 235.77 W, 68

=== Last 3 SpecDecoding metrics ===
[stats] SpecDecoding metrics: Mean acceptance length: 4.89, Accepted throughput: 356.79 tokens/s, Drafted throughput: 550.48 tokens/s, Accepted: 35 tokens, Drafted: 54 tokens, Avg Draft acceptance rate: 64.8% (MTP 22/22, lookup 13/32, 9 steps)
[stats] SpecDecoding metrics: Mean acceptance length: 6.14, Accepted throughput: 418.36 tokens/s, Drafted throughput: 662.40 tokens/s, Accepted: 36 tokens, Drafted: 57 tokens, Avg Draft acceptance rate: 63.2% (MTP 9/9, lookup 27/48, 7 steps)
[stats] SpecDecoding metrics: Mean acceptance length: 8.60, Accepted throughput: 539.92 tokens/s, Drafted throughput: 767.26 tokens/s, Accepted: 38 tokens, Drafted: 54 tokens, Avg Draft acceptance rate: 70.4% (MTP 6/6, lookup 32/48, 5 steps)
```

</details>

## Check summary

| Stage | Exit | Verdict | Detail |
|---|---:|---|---|
| verify-full.sh | 0 | PASS | — |
| verify-stress.sh | 0 | PASS | — |
| soak-test.sh | 0 | PASS | — |
| bench.sh | 0 | PASS | — |
| bench-agentic.sh | 0 | PASS | — |

**Overall: PASS** — every stage that ran passed. `report.sh` exits 0.

---

_Generated by `bash scripts/report.sh`. Flags: `--verify` (verify-full), `--stress` (verify-stress, incl. the Cliff 2 needles), `--soak` (SOAK_MODE=continuous, catches Cliff 2b), `--bench` (canonical TPS), `--agentic` (multi-turn TTFT/decode curve-shape, ~8 min estimate), `--kv-agentic` (real agent workload for the KV offload tier — needs a slug booted with KV_OFFLOAD_GB, ~15 min estimate on 2x 3090 dual-fast, RAM mode), `--studio` (AI Studio / ComfyUI container log tails — for generation bugs), `--full` (all five, ~43 min estimate). Use `--no-redact` to disable redaction (internal sharing only). Exit code: 0 all-clear · 2 advisory-only · 1 hard failure._
