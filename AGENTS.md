# AGENTS.md

## Cursor Cloud specific instructions

This is the **Linux kernel v7.0.0-rc2** source tree. It is built with the Kbuild system (`make`), not a package manager.

### Key commands

| Task | Command |
|---|---|
| Configure (default x86_64) | `make defconfig` |
| Build kernel | `make -j$(nproc)` |
| Lint a patch/commit | `perl scripts/checkpatch.pl --git <commit-range>` |
| Run KUnit tests (UML) | `python3 tools/testing/kunit/kunit.py run --arch=um --kunitconfig=lib/kunit/.kunitconfig` |
| Clean build artifacts | `make mrproper` |

### Non-obvious caveats

- **KUnit (UML) requires `/dev/shm` mounted with exec.** The default Cloud Agent VM mounts it `noexec`. Run `sudo mount -o remount,exec /dev/shm` before executing KUnit tests with `--arch=um`.
- **KUnit and x86 builds conflict.** The KUnit UML runner uses `O=.kunit` as an out-of-tree build directory, but it checks that the source tree itself is clean. If you previously ran `make defconfig && make`, you must run `make mrproper` before running KUnit. Conversely, after KUnit the x86 tree is clean and you can `make defconfig && make -j$(nproc)` directly.
- **checkpatch.pl** exits non-zero even for warnings (no errors). A warning-only result is normal for upstream commits.
- **Build dependencies** (installed via apt): `gcc`, `make`, `flex`, `bison`, `bc`, `libelf-dev`, `libssl-dev`, `dwarves`, `cpio`, `kmod`. These are system packages, not managed by the update script.
