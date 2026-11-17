
========== CONFIG FINGERPRINT ==========
  endpoint       : http://localhost:8205  (mode=chat)
  model          : qwen3.8-flash-next
  serving pid    : 990476 (largest process in container qengine-qwen38-flash-next-multi4)
  log source     : docker
  GPUs measured  : all (4 card(s); the serving process uses every card)
  served ctx     : 262144  (slots=16)
  KV cache type  : fp8_e4m3 (QEngine's only KV format)
  serving argv   : model=/app/models/Qwen3.8-Flash-Next-QE-INT4
  moe-cache cfg  : moe_cache_cap=<unset> RESERVE_MB=<unset> ADMIT_AFTER=<unset> THROTTLE=<unset> MAX_BATCH=<unset> STATS=<unset>
  CPU offload    : not detected (moe-cache captures skipped)

========== SYSTEM RAM ==========
  mem_total_gib=125.7 mem_available_gib=64.5 server_rss_gib=57.7 swap_total_gib=16.0 swap_used_gib=0.0 server_swap_mib=0
  swap check     : PASS (no serving-process pages in swap)

========== NARRATIVE (prompt=65 chars, max_tokens=1000) ==========
=== warmups (3) ===
  warm-1     wall=  3.13s  ttft=   117ms  toks=1000  wall_TPS=319.67  decode_TPS=332.06
  warm-2     wall=  3.04s  ttft=    87ms  toks=1000  wall_TPS=328.82  decode_TPS=338.55
  warm-3     wall=  3.28s  ttft=    29ms  toks=1000  wall_TPS=305.21  decode_TPS=307.94

=== measured (5) ===
  run-1      wall=  2.92s  ttft=    31ms  toks=1000  wall_TPS=342.28  decode_TPS=345.90
  run-2      wall=  2.95s  ttft=    29ms  toks=1000  wall_TPS=339.25  decode_TPS=342.65
  run-3      wall=  2.99s  ttft=    30ms  toks=1000  wall_TPS=334.13  decode_TPS=337.49
  run-4      wall=  2.95s  ttft=    29ms  toks= 975  wall_TPS=330.84  decode_TPS=334.16
  run-5      wall=  2.91s  ttft=    30ms  toks=1000  wall_TPS=343.71  decode_TPS=347.27

=== summary [narrative] (n=5) ===
  wall_TPS       mean= 338.04   std=  5.44   CV= 1.6%   min=330.84   max=343.71
  decode_TPS     mean= 341.49   std=  5.56   CV= 1.6%   min=334.16   max=347.27
  TTFT          mean=    30ms  std=    1ms  min=29ms  max=31ms
  n-usable      5/5  (runs with >= 250 tokens, 25% of max_tokens=1000)
  PP tok/s       n/a (engine log scrape unavailable; use PP=1 for the long-prompt fallback, or SERVER_LOG=<path> on bare metal)

========== CODE (prompt=78 chars, max_tokens=800) ==========
=== warmups (3) ===
  warm-1     wall=  1.71s  ttft=    31ms  toks= 800  wall_TPS=467.34  decode_TPS=475.84
  warm-2     wall=  1.46s  ttft=    29ms  toks= 690  wall_TPS=472.96  decode_TPS=482.68
  warm-3     wall=  1.31s  ttft=    30ms  toks= 622  wall_TPS=475.53  decode_TPS=486.70

=== measured (5) ===
  run-1      wall=  1.33s  ttft=    30ms  toks= 638  wall_TPS=481.44  decode_TPS=492.67
  run-2      wall=  1.14s  ttft=    29ms  toks= 527  wall_TPS=462.43  decode_TPS=474.36
  run-3      wall=  1.21s  ttft=    29ms  toks= 563  wall_TPS=465.40  decode_TPS=476.96
  run-4      wall=  1.66s  ttft=    29ms  toks= 800  wall_TPS=480.92  decode_TPS=489.56
  run-5      wall=  0.91s  ttft=    30ms  toks= 441  wall_TPS=483.85  decode_TPS=500.05

=== summary [code] (n=5) ===
  wall_TPS       mean= 474.81   std= 10.06   CV= 2.1%   min=462.43   max=483.85
  decode_TPS     mean= 486.72   std= 10.83   CV= 2.2%   min=474.36   max=500.05
  TTFT          mean=    29ms  std=    1ms  min=29ms  max=30ms
  n-usable      5/5  (runs with >= 200 tokens, 25% of max_tokens=800)
  PP tok/s       n/a (engine log scrape unavailable; use PP=1 for the long-prompt fallback, or SERVER_LOG=<path> on bare metal)

========== PREFILL-10K (target=10000 prompt tokens, max_tokens=16 warm-up / 128 measured, cache-busted: fresh haystack per run) ==========
=== warmups (1) ===
  warm-1     wall=  3.02s  ttft=  2964ms  prompt_toks=  9565  PP_tok/s=3226.57
  [calibrated: 9565 tok at request=10000 → measured runs request 10454]

=== measured (3) ===
  run-1      wall=  3.95s  ttft=  3538ms  prompt_toks=  9997  PP_tok/s=2825.73
  run-2      wall=  3.48s  ttft=  3139ms  prompt_toks= 10367  PP_tok/s=3303.07
  run-3      wall=  3.68s  ttft=  3362ms  prompt_toks= 10367  PP_tok/s=3083.92

=== summary [prefill-10k] (n=3) ===
  prefill tok/s  mean=3070.91   std=238.93   CV= 7.8%   min=2825.73   max=3303.07
  decode tok/s @ 10k depth mean= 362.97   std= 46.49   CV=12.8%   min=312.32   max=403.68
  TTFT          mean=  3346ms  std=  200ms  min=3139ms  max=3538ms

========== PREFILL-90K (target=90000 prompt tokens, max_tokens=16 warm-up / 128 measured, cache-busted: fresh haystack per run) ==========
=== warmups (1) ===
  warm-1     wall= 25.88s  ttft= 25868ms  prompt_toks= 85894  PP_tok/s=3320.45
  [calibrated: 85894 tok at request=90000 → measured runs request 94302]

=== measured (1) ===
  run-1      wall= 27.22s  ttft= 26850ms  prompt_toks= 89998  PP_tok/s=3351.90

=== summary [prefill-90k] (n=1) ===
  prefill tok/s  mean=3351.90   std=  0.00   CV= 0.0%   min=3351.90   max=3351.90
  decode tok/s @ 90k depth mean= 349.91   std=  0.00   CV= 0.0%   min=349.91   max=349.91
  TTFT          mean= 26850ms  std=    0ms  min=26850ms  max=26850ms

========== CAPTURE: PCIe ==========
  decode all rx_mean=793 rx_peak=4254 tx_mean=820 tx_peak=2471 sm_mean=92.0 memctl_mean=47.2 samples=76
  decode GPU3 rx_mean=717 rx_peak=859 tx_mean=822 tx_peak=2471 sm_mean=93.9 memctl_mean=48.0 samples=19
  decode GPU2 rx_mean=747 rx_peak=1004 tx_mean=833 tx_peak=1959 sm_mean=93.9 memctl_mean=48.0 samples=19
  decode GPU1 rx_mean=751 rx_peak=973 tx_mean=768 tx_peak=958 sm_mean=89.5 memctl_mean=45.9 samples=19
  decode GPU0 rx_mean=954 rx_peak=4254 tx_mean=858 tx_peak=2401 sm_mean=90.7 memctl_mean=46.7 samples=19
  prefill all rx_mean=4020 rx_peak=5395 tx_mean=3086 tx_peak=3534 sm_mean=97.2 memctl_mean=16.6 samples=140
  prefill GPU3 rx_mean=4019 rx_peak=4980 tx_mean=2948 tx_peak=3481 sm_mean=96.2 memctl_mean=17.2 samples=35
  prefill GPU2 rx_mean=3927 rx_peak=4792 tx_mean=3088 tx_peak=3507 sm_mean=96.3 memctl_mean=16.6 samples=35
  prefill GPU1 rx_mean=4019 rx_peak=5395 tx_mean=3175 tx_peak=3488 sm_mean=97.5 memctl_mean=16.3 samples=35
  prefill GPU0 rx_mean=4115 rx_peak=4705 tx_mean=3132 tx_peak=3534 sm_mean=98.6 memctl_mean=16.3 samples=35
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
    decode rx peak = 4254 MB/s = 63.5% of that denominator

========== CAPTURE: VRAM ==========
  idle=83062 MiB  peak=83790 MiB  post=83822 MiB
  leak delta (post - idle) = 760 MiB
  GPU0 20850 MiB (post-run)
  GPU1 20750 MiB (post-run)
  GPU2 20752 MiB (post-run)
  GPU3 21474 MiB (post-run)
  component split (MiB):
  CUDA0: total=20850/24564MiB(85%)
  CUDA1: total=20752/24564MiB(84%)
  CUDA2: total=20752/24564MiB(84%)
  CUDA3: total=21476/24564MiB(87%)
  host CPU busy across the run: 17.2%

========== CAPTURE: DRAFT ACCEPTANCE ==========
  fired=22 last=0.599 mean=0.530 min=0.000 max=0.774 accepted=9190 drafted=16192 accept_len_mean=3.318 accept_len_last=2.820 accept_len_min=1.000 accept_len_max=4.700
  fire rate: fired on 20 of 20 requests in the measured window (100.0%)

========== CAPTURE: ENGINE-SIDE TIMINGS ==========
  decode n=20 mean=399.81 median=359.56 min=308.06 max=500.22  (tok/s, from the server's own print_timing)
  prefill_short(<2000tok) n=17 mean=1127.04 median=1201.16 min=316.04 max=1828.26  (tok/s, from the server's own print_timing)
  prefill_deep(>=2000tok) n=6 mean=3240.09 median=3327.26 min=2877.79 max=3392.52  (tok/s, from the server's own print_timing)
  dropped_short_decode n=3 floor=32  (short probe completions excluded from the decode cross-check)
  cross-check: client-observed 342.65 vs engine-side 342.77 tok/s -> 0.0% divergence (narrative: medians over its 5 measured runs; engine n=5)
  cross-check: client-observed 489.56 vs engine-side 489.69 tok/s -> 0.0% divergence (code: medians over its 5 measured runs; engine n=5)

========== CAPTURE: EXPERT CACHE (moe-cache) ==========
  skipped: no CPU offload detected — nothing for an expert cache to cache.

========== CAPTURE: INTEGRITY ==========
  status: OK
  (no capture-level defect detected — this is NOT a statement about the numbers'
   quality, only that the measurement machinery did its job)
  swap check   : PASS (no serving-process pages in swap)

=== GPU state ===
0, 2 %, 20854 MiB, 24564 MiB, 164.07 W, 47
1, 58 %, 20756 MiB, 24564 MiB, 218.59 W, 57
2, 0 %, 20756 MiB, 24564 MiB, 202.20 W, 58
3, 2 %, 21480 MiB, 24564 MiB, 208.52 W, 65

=== Interconnect (three layers) ===
  layer 1  driver P2P grant : GRANTED over PCIe — topo -p2p reports OK on every pair; kernel module: open
  layer 2  NCCL use         : n/a — QEngine runs its own collectives (no NCCL)
  layer 3  engine transport : decode collectives P2P SM stores (QE_DECODE_COMM=auto); prefill transfers: copy engine (FLASH_COMM=auto)
  verdict  : ℹ interconnect capability is reported above; QEngine's transport is its own and is not measured by the NCCL/custom-AR probes

=== Last 3 SpecDecoding metrics ===
[stats] SpecDecoding metrics: Mean acceptance length: 4.70, Accepted throughput: 314.56 tokens/s, Drafted throughput: 739.21 tokens/s, Accepted: 100 tokens, Drafted: 235 tokens, Avg Draft acceptance rate: 42.6% (MTP 25/41, lookup 75/194, 27 steps)
[stats] SpecDecoding metrics: Mean acceptance length: 1.00, Accepted throughput: 0.00 tokens/s, Drafted throughput: 179.00 tokens/s, Accepted: 0 tokens, Drafted: 3 tokens, Avg Draft acceptance rate: 0.0% (MTP 0/3, lookup 0/0, 1 steps)
[stats] SpecDecoding metrics: Mean acceptance length: 2.82, Accepted throughput: 219.60 tokens/s, Drafted throughput: 366.89 tokens/s, Accepted: 82 tokens, Drafted: 137 tokens, Avg Draft acceptance rate: 59.9% (MTP 8/26, lookup 74/111, 45 steps)
