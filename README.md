# BW Nockchain Pool – Release Bundle (miner.jam + libzkvm_jetpack.so)

**Public repository:** `bwmining/nockpool-addons`

This repo intentionally contains only:

- `README.md` (this file)
- `LICENSE` (MIT)
- **Release assets** (attached to GitHub *Releases*):
  - `miner.jam` (Hoon serialized code)
  - `libzkvm_jetpack.so` (CPU jets — current builds target AMD Zen 4/Zen 5 / Ryzen 7xxx & 9xxx)
  - `*.tar.gz` bundle(s) containing the two files above + this README + the license
  - `SHA256SUMS`

> The binaries are **not** committed to git history; they are uploaded as Release assets.

---

## Downloads

👉 Get the latest prebuilt bundle from **GitHub Releases → Latest**.

- Example asset name: `nockpool-addons_v0.1.0_linux-x86_64_zen5.tar.gz`
- Inside the tarball:
  - `miner.jam`
  - `libzkvm_jetpack.so`
  - `README.md`
  - `LICENSE`

Verify integrity:

```bash
sha256sum -c SHA256SUMS
```

---

## Quick start with `nockpool-miner`

Upstream miner: <https://github.com/SWPSCO/nockpool-miner>

### 1) Build or download `nockpool-miner`

```bash
git clone https://github.com/SWPSCO/nockpool-miner
cd nockpool-miner
cargo build --release
# miner binary path example: ./target/release/nockpool-miner
```

### 2) Place the bundle next to the miner (or anywhere you prefer)

```bash
mkdir -p ~/bw-miner && cd ~/bw-miner
# download release tar.gz here, then:
tar -xzf nockpool-addons_v0.1.0_linux-x86_64_zen5.tar.gz
ls -1
# → miner.jam, libzkvm_jetpack.so, README.md, LICENSE
```

### 3) Run with the provided helper script (recommended)

Use `scripts/nockpool-run.sh` (provided below in this repo) to launch the miner with sane defaults.

```bash
# copy the script to your working dir
cp scripts/nockpool-run.sh ./
chmod +x ./nockpool-run.sh

# start in foreground
./nockpool-run.sh start \
  --account-token nockacct_************************ \
  --threads 16

# start in background (daemon)
./nockpool-run.sh start --daemon \
  --account-token nockacct_************************ \
  --threads 16

# check status
./nockpool-run.sh status

# follow logs
./nockpool-run.sh logs

# stop
./nockpool-run.sh stop
```

> The script exports `MINER_JAM_PATH` and ensures `LD_LIBRARY_PATH` contains the directory of `libzkvm_jetpack.so`.

### 4) Or run the miner directly (manual)

```bash
export MINER_JAM_PATH="$(pwd)/miner.jam"
export LD_LIBRARY_PATH="$(pwd):${LD_LIBRARY_PATH:-}"
/path/to/nockpool-miner \
  --max-threads 16 \
  --account-token nockacct_************************
```

---

## Compatibility / ABI

The `miner.jam` and `libzkvm_jetpack.so` are built against specific upstream commits/ABI. If versions drift, the miner may refuse to start, or undefined behavior may occur.

- **CPU**: current builds target AMD Zen 4 / Zen 5 (Ryzen 7xxx and 9xxx). Other CPUs may work but are not supported in this bundle yet.
- **OS**: Linux x86_64 (glibc toolchains). macOS builds can be added later.
- **Miner**: tested with the upstream `nockpool-miner` at pinned commits (see release notes).

Release notes include the exact upstream commit hashes used for the build.

---

## Environment variables (script)

- `ACCESS_TOKEN` — default pool account token (can be overridden by CLI `--account-token`)
- `MAX_THREADS` — default thread count (overridden by `--threads`)
- `MINER_JAM_PATH` — path to `miner.jam` (overridden by `--jam`)
- `LIB_DIR` — directory containing `libzkvm_jetpack.so` (overridden by `--lib-dir`)
- `MINER_BIN` — path to the miner binary (default: `./nockpool-miner`)
- `PIDFILE` — PID file path (default: `.nockpool-miner.pid`)
- `LOG_FILE` — log file path (default: `nockpool.log`)
- `EXTRA_ARGS` — extra flags passed verbatim to the miner

---

## License

This repository’s **README** and **packaging** are APACHE-licensed. The *bundled artifacts* (`miner.jam`, `libzkvm_jetpack.so`) are subject to their own licenses and terms.

---

> In your **private** repo settings → *Secrets and variables* → *Actions*, create `PUBLIC_REPO_TOKEN` as a fine‑grained PAT with `contents:write` permission **scoped only** to the public repo `bwmining/nockpool-addons`.

---

## Roadmap (optional)

- Add additional CPU variants (generic x86_64, Intel) with a build **matrix** (`strategy.matrix`).
- Add `manifest.json` with commit pins and ABI to the tarball.
- Add macOS artifacts (if needed) and a small smoke test job that executes the miner with `--version` and validates the ABI handshake.

