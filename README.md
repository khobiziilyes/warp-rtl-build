# Reviewed Warp RTL build

Build-only repository for public upstream [warpdotdev/warp](https://github.com/warpdotdev/warp)
at **`956994893216670cfb522e5dced2af04667a1c5b`**. No Warp source changes are applied.

Run **Actions → Build reviewed Warp RTL → Run workflow** manually in a **public**
repository. One standard `macos-26` arm64 job runs at a time, with a 180-minute
timeout, read-only token and no caches, secret inputs or paid runners.

The workflow requires Xcode 26.3 and real Metal tools, installs Rust 1.92.0 with
the minimal profile and only missing protobuf, and uses upstream's pinned
cargo-binstall 1.18.1 / cargo-bundle 0.11.0 helper. It bypasses `script/run` to
avoid internal channel configuration and common-skills installation.

Compilation is `cargo build --locked -p warp --bin warp-oss --features
gui,rtl_terminal_text,release_bundle`, with one build job, no incremental output
or debug info, and stripped symbols. This is a dev-profile build;
`release_bundle` embeds assets for portability. The upstream macOS script then
bundles without rebuilding or launching and supplies ad-hoc signing.

Download the artifact within **one day**. It contains `WarpOss-arm64.zip`
(at most **450 MiB**), `SHA256SUMS.txt`, and a generated README recording the
actual source commit, origin, tools and validation results; the same provenance
appears in the job summary. The workflow verifies real nonempty Metal output,
native arm64 executable, bundle ID `dev.warp.WarpOss`, plist validity, signing,
and unchanged source before packaging with `ditto`.

After extracting the downloaded artifact, verify `shasum -a 256 -c SHA256SUMS.txt`
before unpacking the app ZIP. This app is **not notarized**. Independently verify
its signature, architecture and app-data separation before launching it
side-by-side. Do not replace `/Applications/Warp.app`, reuse its preferences,
or disable global macOS security protections. This workflow never installs or
launches the app. Resource fit and Arabic/OpenCode behavior require an actual
build and interactive testing; failures must not silently switch to paid runners.
