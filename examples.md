# lpminer-cpu examples

These examples use **1.0.2** and assume you are inside the extracted release directory.

## Download and verify — Linux x86-64

```sh
curl -fLO https://github.com/luckypoolio/lpminer-cpu/releases/download/v1.0.2/lpminer-cpu-1.0.2-bb5708863661-linux-x86_64-universal.tar.gz
curl -fLO https://github.com/luckypoolio/lpminer-cpu/releases/download/v1.0.2/SHA256SUMS
sha256sum --check --ignore-missing SHA256SUMS
tar -xzf lpminer-cpu-1.0.2-bb5708863661-linux-x86_64-universal.tar.gz
cd lpminer-cpu-1.0.2-bb5708863661-linux-x86_64-universal
./lpminer-cpu --version
```

For ARM64, download and extract `lpminer-cpu-1.0.2-bb5708863661-linux-arm64.tar.gz` instead. Confirm the checksum check reports `OK` for the archive you downloaded before extracting it.

On Ubuntu 22.04, missing runtime libraries can be installed with:

```sh
sudo apt-get update
sudo apt-get install libcurl4 libgmp10
```

The miner itself can run as a normal user.

## Blocknet mainnet — two workers

```sh
./lpminer-cpu -a blocknet \
  -o stratum+tcp://eu.lproute.com:3348 \
  -u YOUR_BNT_WALLET -p x -t 2
```

Replace `YOUR_BNT_WALLET` with your own mainnet address. Change the pool URL to another compatible endpoint if desired. Press **Ctrl+C** to stop.

Two workers allocate 4 GiB of hashing arenas. The sizing guard budgets 6.5 GiB available in total: 2.25 GiB per worker plus a 2 GiB reserve.

## JSON configuration

Save as `blocknet.json`:

```json
{
  "algo": "blocknet",
  "url": "stratum+tcp://eu.lproute.com:3348",
  "user": "YOUR_BNT_WALLET",
  "pass": "x",
  "threads": 2
}
```

Then run:

```sh
./lpminer-cpu -c blocknet.json
```

## Offline benchmark — one worker, 60 seconds

```sh
./lpminer-cpu -a blocknet --benchmark -t 1 --time-limit 60
```

This does not connect to a pool. Startup and completion of an in-flight hash can add time around the requested duration. A short run may end before automatic kernel comparisons finish.

For a repeatable fixed-kernel comparison on a compatible x86-64 CPU:

```sh
CPUMINER_BLOCKNET_KERNEL=avx2-stream \
  ./lpminer-cpu -a blocknet --benchmark -t 1 --time-limit 60
```

Use the same worker count, CPU affinity, page mode and other running workloads when comparing results. A kernel name cannot bypass CPU/OS feature checks.

## Save a plain-text log

```sh
./lpminer-cpu -a blocknet \
  -o stratum+tcp://eu.lproute.com:3348 \
  -u YOUR_BNT_WALLET -p x -t 2 --no-color 2>&1 | tee miner.log
```

## Regular pages

```sh
CPUMINER_BLOCKNET_PAGES=regular \
  ./lpminer-cpu -a blocknet \
  -o stratum+tcp://eu.lproute.com:3348 \
  -u YOUR_BNT_WALLET -p x -t 2
```

The default `auto` page mode is suitable for starting out. Explicit `large` and `large-1g` require already available Linux HugeTLB pages and fail when those pages cannot be allocated.

## Inspect available options

```sh
./lpminer-cpu --help
./lpminer-cpu --version
```

## Build from the corresponding source

Download `lpminer-cpu-1.0.2-source.tar.gz` from the release and verify it with `SHA256SUMS`. This is the complete miner source; GitHub's automatic source archives contain the release-page documents.

Extract it and follow `docs/cmake-build.md` for development dependencies. For the universal x86-64 build, preserve the source identity when configuring the archive:

```sh
tar -xzf lpminer-cpu-1.0.2-source.tar.gz
cd lpminer-cpu-1.0.2-source
cmake --preset universal \
  -DCPUMINER_SOURCE_REVISION=bb570886366105918fc649170d1a4b56998e6449 \
  -DCPUMINER_SOURCE_DIRTY=0
cmake --build --preset universal --parallel 8
ctest --preset universal
./build/cmake/universal/lpminer-cpu --version
```

Use these identity values only for the unchanged release source. The source guide also covers native ARM64 and cross builds.

See [Parameters](Parameters.md) for commonly used controls and the release archive's `docs/` directory for build and Blocknet details.
