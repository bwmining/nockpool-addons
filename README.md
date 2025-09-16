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
### Downloads

👉 Get the latest prebuilt bundle from **GitHub Releases → Latest** [Go to bwming github](https://github.com/bwmining/nockpool-addons/releases).

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

### Install

You can download the prebuilt binaries in the release tab. The Linux bins are SLSA3 attested -- we recommend [verifying](https://github.com/slsa-framework/slsa-verifier).

### Run

### 2) Place the bundle next to the miner (or anywhere you prefer)

```bash
cd nockpool-miner # OR where you installed nockpool-miner

mkdir -p addons && cd addons
# download release tar.gz here, then:
tar -xzf nockpool-addons_v0.1.3_linux-amd_zen4_x86_64.tar.gz
ls -1
# → miner.jam, libzkvm_jetpack.so, README.md, LICENSE
```

### 3) Run with the provided helper script (recommended)

Use `nockpool-run.sh` (provided in nockpool-miner) to launch the miner with sane defaults.


> NOTE: ONLY if you didn't extract file next to the miner binary.
The script exports `MINER_JAM_PATH` where MINER_JAM_PATH is the full path to the miner.jam file and ensures `LIB_DIR` contains the directory of `libzkvm_jetpack.so` 


```bash
chmod +x ./nockpool-run.sh

# HELP for full options possibility
./nockpool-run.sh

# start in foreground
./nockpool-run.sh start \
  --max-threads 16 --jam addons/miner.jam --lib-dir ./addons \
  --account-token nockacct_************************ \
  

# start in background (daemon)
./nockpool-run.sh start --daemon \
  --max-threads 16 --jam ./addons/miner.jam --lib-dir ./addons \
  --account-token nockacct_************************


# check status
./nockpool-run.sh status

# follow logs
./nockpool-run.sh logs

# stop
./nockpool-run.sh stop
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
- `MAX_THREADS` — default thread count (overridden by `--max-threads`)
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

