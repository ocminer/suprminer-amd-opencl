# suprminer-amd

A multi-threaded, multi-pool OpenCL GPU miner for cryptocurrency mining.

**Repository:** https://github.com/ocminer/suprminer-amd

**Bug Tracker:** https://github.com/ocminer/suprminer-amd/issues

**License:** GPLv3 (see `COPYING`)


## What's New in v1.2.0

- Builds cleanly on Ubuntu 22.04/24.04 with GCC 12/13 (zero warnings)
- OpenCL 2.0+ / 3.0 compatible (tested with NVIDIA and AMD ICDs)
- OpenCL kernels compile on OpenCL 2.0+ platforms (automatic `-cl-std=CL1.2` fallback)
- Updated Jansson to v2.15.0
- Modernized libcurl API usage (no deprecated functions)
- Security fixes: buffer overflow prevention, integer overflow fix, undefined behavior removal
- Fixed working difficulty ramp-up (set to pool difficulty immediately)
- GitHub Actions CI pipeline

See [CHANGELOG.md](CHANGELOG.md) for full details.


## Supported Algorithms

Use `-k <name>` or `--algorithm <name>` to select an algorithm.

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
| `neoscrypt` | NeoScrypt (Feathercoin, etc.) |
| `pluck` | Pluck (Supcoin) |
| `yescrypt` | Yescrypt (GlobalBoost-Y) |
| `yescrypt-multi` | Yescrypt multi-threaded variant |
| `cryptonight` | CryptoNight (CryptoNote coins; use `--monero` for Monero variants) |

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
| `lyra2rev2` | Lyra2RE v2 (Vertcoin) |

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

Single pool:
```bash
./suprminer-amd -o stratum+tcp://pool:port -u wallet_address -p x -k groestlcoin
```

Multiple pools (failover):
```bash
./suprminer-amd \
  -o stratum+tcp://pool1:port -u wallet1 -p x \
  -o stratum+tcp://pool2:port -u wallet2 -p x \
  -k groestlcoin
```

### Configuration File

Save your configuration:
```bash
./suprminer-amd -o stratum+tcp://pool:port -u wallet -p x -k groestlcoin -S settings.conf
```

Run from saved config:
```bash
./suprminer-amd -c settings.conf
```

Default config file: `suprminer-amd.conf` in the current directory.

Configuration files can include other files:
```json
{
    "include": "other-config.conf"
}
```

### Pool Configuration

```json
{
    "pools": [
        {
            "url": "stratum+tcp://pool1:port",
            "user": "wallet1",
            "pass": "x"
        },
        {
            "quota": "2;stratum+tcp://pool2:port",
            "user": "wallet2",
            "pass": "x"
        }
    ],
    "algorithm": "groestlcoin",
    "intensity": "15"
}
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

### Logging

Redirect stderr for logging:
```bash
./suprminer-amd [options] 2>mining.log
```

Share logging:
```bash
./suprminer-amd --sharelog shares.csv [options]
```


## Documentation

Additional documentation in `doc/`:

| File | Topic |
|------|-------|
| `API.md` | RPC API specification |
| `configuration.md` | Configuration options reference |
| `FAQ.md` | Frequently asked questions |
| `GPU.md` | GPU configuration and tuning |
| `kernel.md` | OpenCL kernel development |
| `MINING.md` | Mining optimization guide |
| `BUGS.md` | Known bugs and workarounds |
| `coding.md` | Coding style and contributing |
| `windows-build.txt` | Windows build instructions |
| `cygwin-build.txt` | Cygwin build instructions |


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


## History

The v1.2.0 release modernizes the codebase for current compilers (GCC 12/13), current OpenCL implementations (2.0+/3.0), and current dependencies (libcurl 8.x, Jansson 2.15).
