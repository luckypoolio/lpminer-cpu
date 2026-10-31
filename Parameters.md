# lpminer-cpu parameters

These options describe **1.0.2**. Run `./lpminer-cpu --help` for the complete command-line reference from your installed binary.

## Mining

| Option | Purpose |
| --- | --- |
| `-a, --algo=ALGO` | Algorithm name, for example `blocknet`. |
| `-o, --url=URL` | Pool or RPC URL. Use the protocol and port supplied by the pool. |
| `-u, --user=USERNAME` | Pool login; for Blocknet, use your wallet address as required by the pool. |
| `-p, --pass=PASSWORD` | Pool password or pool-specific options; commonly `x`. |
| `-O, --userpass=USER:PASS` | Supply login and password together. |
| `-t, --threads=N` | Mining worker count. Blocknet automatic sizing also considers available RAM. |
| `-c, --config=FILE` | Load a JSON configuration file. |
| `--time-limit=N` | Stop after a requested mining duration in seconds. |
| `--benchmark` | Run an offline benchmark without a pool or wallet. |
| `--cpu-affinity=MASK` | Limit process affinity using a CPU bitmask; `0x3` selects logical CPUs 0 and 1. |
| `--max-temp=N` | Linux temperature threshold for mining. |
| `--data-file=FILE` | Data file path for algorithms that require one. |

Blocknet uses a **2 GiB arena per worker**. Memory budgeting reserves additional overhead. Leave sufficient memory for the operating system and other workloads.

## Connection

| Option | Purpose |
| --- | --- |
| `-r, --retries=N` | Retry count after network failures; default is unlimited. |
| `--retry-pause=N` | Delay between retries, in seconds; default 30. |
| `-T, --timeout=N` | Long-poll/Stratum timeout, in seconds; default 300. |
| `--cert=FILE` | Certificate file for an SSL connection. |
| `-x, --proxy=[PROTOCOL://]HOST[:PORT]` | Connect through a proxy. |
| `--no-redirect` | Ignore pool requests to change the server URL. |
| `--stratum-keepalive` | Enable Stratum keepalive behavior. |

## Logs and information

| Option | Purpose |
| --- | --- |
| `-V, --version` | Show version, source commit and CPU information. |
| `-h, --help` | Show all command-line options. |
| `--no-color` | Plain-text output, useful when saving logs. |
| `-q, --quiet` | Reduce output and suppress the statistics table. |
| `--hash-meter` | Include individual worker speeds. |
| `-D, --debug` | More detailed diagnostic output. |
| `-P, --protocol-dump` | Log protocol traffic; remove credentials before sharing these logs. |
| `-S, --syslog` | Send output to the system log. |

`FORCE_COLOR=1` keeps terminal colors when piping through `tee`. `NO_COLOR=1` disables automatic color; `--no-color` overrides both variables.

The normal table appears every **120 seconds**. Hashrate windows remain `-` until enough history exists. `Avg job` is uptime divided by received jobs; `Avg share` is uptime divided by accepted shares. Both include idle and reconnect time since process startup.

## Blocknet environment controls

Set these before the command, for example `CPUMINER_BLOCKNET_PAGES=regular ./lpminer-cpu ...`.

| Variable | Values and behavior |
| --- | --- |
| `CPUMINER_BLOCKNET_KERNEL` | `auto` or a supported kernel name. A forced kernel disables tuning; unsupported choices fail before hashing. |
| `CPUMINER_BLOCKNET_TUNE` | `0` disables automatic kernel comparisons. Unset allows them. |
| `CPUMINER_BLOCKNET_TUNE_CACHE` | `0` disables reading/writing saved tuning results. |
| `CPUMINER_BLOCKNET_PAGES` | `auto` (default), `regular`, `large` (required 2 MiB HugeTLB), or `large-1g` (required 1 GiB HugeTLB). |
| `CPUMINER_CPU` | x86-64 universal profile: `auto`, `sse2`, `sse42-aes`, `avx-aes`, `avx2-aes`, `avx2-sha`, `avx2-sha-vaes`, `avx512`, or `avx512-sha-vaes`. |

Required hugepage modes fail if the requested pages cannot be allocated. The miner does not reserve host hugepages or change system settings. Default `auto` can fall back to ordinary allocation with a transparent-hugepage hint.

### x86-64 Blocknet kernels

```text
sse2
avx2
avx2-stream
avx2-stream-skip
avx512
avx512-stream
avx512-stream-skip
avx512-256-stream
avx512-stream-l2
avx512-256-stream-l2
avx512-stream-cache512
avx512-stream-cache1024
```

### ARM64 Blocknet kernels

```text
neon
neon-umlal
neon-sha3
```

`neon-sha3` requires the ARM SHA3 extension. The `-skip` kernels are experimental opt-in choices: they trade fewer writes for reference reconstruction, still allocate 2 GiB per worker, and are not automatically selected. Measure on your own CPU before choosing a forced kernel.

The release's `docs/blocknet-kernels.md` contains the complete tuning, placement, memory and validation details.
