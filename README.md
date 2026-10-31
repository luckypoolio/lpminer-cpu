# lpminer-cpu

**LuckyPool's CPU cryptocurrency miner for Linux — with optimized Blocknet mining and the cpuminer-opt algorithm catalog.**

One universal x86-64 binary selects a compatible CPU profile at startup. A separate ARM64 build supports ARMv8-A / NEON processors.

## DOWNLOAD

**[Latest release](https://github.com/luckypoolio/lpminer-cpu/releases/latest)** · [All releases](https://github.com/luckypoolio/lpminer-cpu/releases)

| Version 1.0.2 | Download |
| --- | --- |
| Linux x86-64 — Intel / AMD, universal | [Linux x86-64 archive](https://github.com/luckypoolio/lpminer-cpu/releases/download/v1.0.2/lpminer-cpu-1.0.2-bb5708863661-linux-x86_64-universal.tar.gz) |
| Linux ARM64 — ARMv8-A / NEON | [Linux ARM64 archive](https://github.com/luckypoolio/lpminer-cpu/releases/download/v1.0.2/lpminer-cpu-1.0.2-bb5708863661-linux-arm64.tar.gz) |
| Corresponding miner source | [Source archive](https://github.com/luckypoolio/lpminer-cpu/releases/download/v1.0.2/lpminer-cpu-1.0.2-source.tar.gz) |
| Archive checksums | [SHA256SUMS](https://github.com/luckypoolio/lpminer-cpu/releases/download/v1.0.2/SHA256SUMS) |

The binary displays `1.0.2 (bb5708863661)`. This repository contains release documentation; use the **corresponding miner source** asset above for the CMake project and source code. GitHub's automatically generated source archives contain this documentation repository.

## REQUIREMENTS

- **Linux, 64-bit**, with glibc **2.34 or newer**, `libcurl.so.4` and `libgmp.so.10`. Ubuntu 22.04 or newer is a suitable starting point. Jansson is bundled.
- **x86-64:** Intel or AMD processor with SSE2. AVX2, AVX512, AES, SHA and VAES are selected only when supported by the CPU and operating system.
- **ARM64:** ARMv8-A / NEON. Use the ARM64 archive; the x86-64 binary cannot run on ARM64.
- **Blocknet RAM:** a 2 GiB hashing arena per worker, plus overhead. Automatic sizing budgets **2.25 GiB per worker + 2 GiB reserve** and accounts for visible Linux container limits. An explicit thread count that exceeds a known memory budget fails with an explanation.

These downloads are Linux builds. ARM64 correctness has been checked under emulation; native performance depends on the hardware.

## FEATURES

- Universal x86-64 dispatch across eight CPU profiles in one executable.
- Blocknet kernels for SSE2, AVX2, AVX512 and ARM NEON, with optional ARM SHA3 acceleration.
- Bounded kernel comparisons using productive mining work, with validated tuning results reused on matching clean Linux builds and placement.
- Blocknet cancellation when work becomes obsolete; memory arenas are reused between hashes.
- Automatic page allocation, optional required hugepage modes, and container-aware thread sizing.
- Rolling **1m / 5m / 15m** hashrates and a statistics table every **120 seconds**.
- Compact accepted/rejected/stale totals, job rate, last-job age, average job interval and average accepted-share interval.
- Source commit in the version string; modified source builds are marked `dirty`.

## ALGORITHMS

**`blocknet`** — Blocknet Argon2id, 2 GiB per worker.

The inherited algorithm catalog remains available, including:

```text
allium       anime         argon2d250    argon2d500    argon2d1000
argon2d4096  argon2d16000  axiom         blake         blake2b
blake2s      blakecoin     bmw           bmw512        c11
deep         dmd-gr        groestl       hex           hmq1725
jha          keccak        keccakc       lbry          lyra2h
lyra2re      lyra2rev2     lyra2rev3     lyra2z        lyra2z330
m7m          minotaur      minotaurx     myr-gr        neoscrypt
nist5        pentablake    phi1612       phi2          polytimos
power2b      quark         qubit         scrypt        scryptn2
sha256d      sha256dt      sha256q       sha256t       sha3d
sha512256d   skein         skein2        skunk         sonoa
timetravel   timetravel10  tribus        vanilla       veltor
verthash     whirlpool     whirlpoolx    x11           x11evo
x11gost      x12           x13           x13bcd        x13sm3
x14          x15           x16r          x16rv2        x16rt
x16rt-veil   x16s          x17           x20r          x21s
x22i         x25x          xevan         yescrypt      yescryptr8
yescryptr8g  yescryptr16   yescryptr32   yespower      yespowerr16
yespower-b2b zr5
```

Use `./lpminer-cpu --help` for the executable's algorithm names and options. Availability in the catalog does not establish that a coin or pool is currently active.

## USAGE

Extract the archive for your architecture, open its directory, and run:

```sh
./lpminer-cpu -a blocknet \
  -o stratum+tcp://eu.lproute.com:3348 \
  -u YOUR_BNT_WALLET -p x -t 2
```

Replace `YOUR_BNT_WALLET` with your own **Blocknet mainnet** address. Run the miner as a normal user. Press **Ctrl+C** to stop.

**[Parameters](Parameters.md)** · **[Examples](examples.md)**

## SUPPORT

Report problems in [GitHub Issues](https://github.com/luckypoolio/lpminer-cpu/issues). Include the miner version and commit, CPU model, Linux distribution, thread count, relevant startup/error output, and a command with wallet credentials removed.

## CREDITS AND SOURCE

lpminer-cpu is maintained by LuckyPool and is based on [cpuminer-opt by JayDDee](https://github.com/JayDDee/cpuminer-opt), itself derived from cpuminer-multi and earlier contributors. Blocknet work also incorporates adaptations from [Seine](https://github.com/zpalmtree/seine).

Release archives retain `COPYING`, `LICENSE`, `AUTHORS` and third-party license notices, including Jansson and Seine. The matching miner source archive is attached to each release; its `docs/cmake-build.md` describes the build process.

## LICENSE

lpminer-cpu is distributed under the [GNU General Public License version 2](https://www.gnu.org/licenses/old-licenses/gpl-2.0.html). The license and third-party notices are included in each release archive.
