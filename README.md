# suprminer-amd-opencl

A multi-threaded, multi-pool OpenCL GPU miner for cryptocurrency mining.

**Repository:** https://github.com/ocminer/suprminer-amd-opencl

**Bug Tracker:** https://github.com/ocminer/suprminer-amd-opencl/issues

**License:** GPLv3 (see `COPYING`)


## What's New

### v1.4.0

- **Verthash (VTC)** algorithm with optimized 4-way coalesced kernel (449 kH/s on RX 7600 XT)
- **XEL (XelisHash v3)** algorithm with HBM2 kernel variant for MI50/MI60/VII/MI100/MI200/MI300
- **QHash** algorithm (Qubitcoin PoW)
- HiveOS and MMPOS integration scripts
- HTTP stats API (`--api` flag, port 4068)
- sysfs GPU monitoring (temperature, fan, clocks)
- ADL removed (sysfs only)


## Supported Algorithms

Use `-k <name>` or `--algorithm <name>` to select an algorithm.

### New Algorithms

| Algorithm (`-k`) | Coin | Notes |
|-------------------|------|-------|
| `verthash` | Vertcoin (VTC) | Requires `verthash.dat` (see below). Aliases: `vtc` |
| `xel` | Xelis (XEL) | Auto-detects HBM2 GPUs. Aliases: `xelis`, `xelishashv3` |
| `qhash` | Qubitcoin (Q2C) | Aliases: `qtc`, `qubitcoin-pow` |
| `neoscrypt` | Feathercoin, Phoenixcoin, etc. | Optimized for AMD RDNA |

### Scrypt Variants

| Algorithm (`-k`) | Description |
|-------------------|-------------|
| `bufius` | Scrypt (bufius kernel) |
| `psw` | Scrypt (psw kernel) |
| `zuikkis` | Scrypt (zuikkis kernel) |
| `arebyp` | Scrypt (arebyp kernel) |

### Memory-Hard Algorithms

| Algorithm (`-k`) | Description |
|-------------------|-------------|
| `pluck` | Pluck (Supcoin) |
| `yescrypt` | Yescrypt (GlobalBoost-Y) |
| `yescrypt-multi` | Yescrypt multi-threaded variant |
| `cryptonight` | CryptoNight (CryptoNote coins) |

### SHA/Keccak/Blake Family

| Algorithm (`-k`) | Description |
|-------------------|-------------|
| `blake256r8` | Blake-256 8-round (Blakecoin) |
| `blake256r14` | Blake-256 14-round (Decred) |
| `vanilla` | Blake-256 8-round (Vanilla) |
| `maxcoin` | Keccak-256 (Maxcoin) |
| `credits` | Keccak-256 (Credits) |

### X-Series (Chained Hash)

| Algorithm (`-k`) | Description |
|-------------------|-------------|
| `darkcoin` | X11 (Dash, etc.) |
| `darkcoin-mod` | X11 optimized kernel |
| `marucoin` | X13 |
| `marucoin-mod` | X13 optimized kernel |
| `marucoin-modold` | X13 legacy optimized kernel |
| `x14` | X14 |
| `x14old` | X14 legacy kernel |
| `bitblock` | X15 (Bitblock) |
| `bitblockold` | X15 legacy kernel |
| `talkcoin-mod` | NIST5 (Talkcoin) |
| `fresh` | Fresh (SHAvite3-based) |

### Quark Family

| Algorithm (`-k`) | Description |
|-------------------|-------------|
| `quarkcoin` | Quark (Quarkcoin) |
| `qubitcoin` | Qubit (Qubitcoin) |
| `animecoin` | Quark variant (Animecoin) |
| `sifcoin` | Quark variant (Sifcoin) |

### Groestl / Fugue Family

| Algorithm (`-k`) | Description |
|-------------------|-------------|
| `groestlcoin` | Groestl-512 double (Groestlcoin) |
| `fuguecoin` | Fugue-256 (Fuguecoin) |
| `diamond` | Groestl variant (Diamond) |
| `myriadcoin-groestl` | Myriad-Groestl |

### Whirlpool

| Algorithm (`-k`) | Description |
|-------------------|-------------|
| `whirlcoin` | Whirlpool (Whirlcoin) |
| `whirlpoolx` | WhirlpoolX |

### Lyra2

| Algorithm (`-k`) | Description |
|-------------------|-------------|
| `lyra2re` | Lyra2RE v1 |
| `lyra2rev2` | Lyra2RE v2 |

### Other

| Algorithm (`-k`) | Description |
|-------------------|-------------|
| `twecoin` | TWE (Twecoin) |
| `inkcoin` | Shavite3 (Inkcoin) |
| `ethash` | Ethash (DAG-based) |
| `ethash-genoil` | Ethash (Genoil variant) |
| `ethash-new` | Ethash (new variant) |
| `equihash` | Equihash (Zcash-derived) |


## Usage

### Quick Start

NeoScrypt (Feathercoin/Phoenixcoin):
```bash
./suprminer-amd -k neoscrypt -o stratum+tcp://pool:port -u wallet -p x
```

Verthash (Vertcoin):
```bash
./suprminer-amd -k verthash -o stratum+tcp://pool:port -u wallet -p x --verthash-dat /path/to/verthash.dat
```

XEL (Xelis):
```bash
./suprminer-amd -k xel -o stratum+tcp://pool:port -u wallet -p x
```

QHash (Qubitcoin):
```bash
./suprminer-amd -k qhash -o stratum+tcp://pool:port -u wallet -p x
```

Multiple pools (failover):
```bash
./suprminer-amd \
  -o stratum+tcp://pool1:port -u wallet1 -p x \
  -o stratum+tcp://pool2:port -u wallet2 -p x \
  -k neoscrypt
```

### Verthash Data File

Verthash mining requires a 1.2 GB data file (`verthash.dat`). This file is static and identical for all miners -- you only need to create or download it once.

**Option 1: Download (recommended)**

Download a pre-generated `verthash.dat` from:
- https://vtc.suprnova.cc/verthash.dat
- Or use the [Vertcoin One-Click Miner](https://vertcoin.io/download-one-click-miner/) which generates it automatically

**Option 2: Generate with VerthashMiner**

```bash
./VerthashMiner --gen-verthash-data verthash.dat
```

Generation takes about 3 minutes on a modern CPU.

**Specifying the path:**

By default, the miner looks for `verthash.dat` in the current working directory. Use `--verthash-dat` to specify a different path:

```bash
./suprminer-amd -k verthash -o stratum+tcp://pool:port -u wallet -p x --verthash-dat /home/user/verthash.dat
```

The file is loaded into GPU memory at startup (requires ~1.2 GB VRAM in addition to normal usage).

### XEL HBM2 GPUs

For AMD HBM2 GPUs (Vega, MI50, MI60, Radeon VII, MI100, MI200, MI300), an optimized kernel variant is auto-detected. To force it manually:

```bash
./suprminer-amd -k xel --xel-hbm2 -o stratum+tcp://pool:port -u wallet -p x
```

### Configuration File

Save your configuration:
```bash
./suprminer-amd -o stratum+tcp://pool:port -u wallet -p x -k neoscrypt -S settings.conf
```

Run from saved config:
```bash
./suprminer-amd -c settings.conf
```

Default config file: `suprminer-amd.conf` in the current directory.

### Pool Configuration (JSON)

```json
{
    "pools": [
        {
            "url": "stratum+tcp://pool1:port",
            "user": "wallet1",
            "pass": "x"
        }
    ],
    "algorithm": "verthash",
    "verthash-dat": "/path/to/verthash.dat",
    "intensity": "17"
}
```

### Stats API

Enable the HTTP stats API for monitoring:

```bash
./suprminer-amd --api [options]
```

Returns JSON on port 4068 (configurable with `--api-port`):
```bash
curl http://localhost:4068/
```

### Pool Strategies

- **Failover** (default): Priority-based, falls back to next pool on failure
- **Round Robin**: Moves to next pool only when current one fails
- **Rotate**: Switches between pools at timed intervals
- **Load Balance**: Distributes work across pools by quota
- **Balance**: Equalizes work done across all pools

### Runtime Keys

| Key | Action |
|-----|--------|
| `P` | Pool management |
| `G` | GPU management |
| `S` | Settings |
| `D` | Display options |
| `Q` | Quit |


## Proxy Support

Supported proxy types: `http:`, `http0:`, `socks4:`, `socks5:`, `socks4a:`, `socks5h:`

```bash
./suprminer-amd -o "socks5:proxy:port|stratum+tcp://pool:port" -u wallet -p x
```

Or use `--socks-proxy` for a global proxy setting.


## Status Line

```
ST: 1  SS: 0  NB: 1  LW: 8  GF: 1  RF: 1
```

| Code | Meaning |
|------|---------|
| ST | Staged work items (ready to use) |
| SS | Stale shares discarded |
| NB | New blocks detected |
| LW | Locally generated work items |
| GF | Getwork fail occasions |
| RF | Remote fail occasions |
