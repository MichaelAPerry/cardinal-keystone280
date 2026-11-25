# Keystone 280 for CardinalMini

This fork combines [DISTRHO Cardinal](https://github.com/DISTRHO/Cardinal),
licensed GPL-3.0-or-later, with the [Keystone 280 VCV Rack plugin](https://github.com/MichaelAPerry/keystone280-vcv),
also GPL-3.0-or-later. It provides a CardinalMini web build for
[michaelperry.org](https://michaelperry.org) that opens the **Dark-eyed Junco —
Snowbirds at the Feeder** demo on first launch. The original Cardinal Mini
welcome patch is still bundled as `patches/welcome-wasm-mini.vcv`.

## Build locally

Use a Linux build environment with Git, GNU Make, gperf, and Meson. Initialize
the submodules and bootstrap Cardinal's Emscripten toolchain with PawPaw.

```bash
git submodule update --init --recursive
export PAWPAW_FAST_MATH=1 PAWPAW_SKIP_GLIB=1 PAWPAW_SKIP_LTO=1
export PAWPAW_SKIP_LV2=1 PAWPAW_SKIP_OPENSSL=1 PAWPAW_SKIP_SAMPLERATE=1 PAWPAW_SKIP_TESTS=1
./deps/PawPaw/bootstrap-cardinal.sh wasm
source deps/PawPaw/local.env wasm
# Apply the same Surge Emscripten workaround as CI.
sed -i -e 's/defined(__riscv)/defined(__riscv) || defined(__EMSCRIPTEN__)/' plugins/surgext/surge/src/common/globals.h
make -j "$(nproc)" HAVE_LIBLO=false NOOPT=true NOSIMD=false mini
```

Serve `bin/CardinalMini.html`, `bin/CardinalMini.js`,
`bin/CardinalMini.wasm`, and `bin/CardinalMini.data` from the same directory.
This build requires WebAssembly SIMD support.

## Build with GitHub Actions

A push to `main` runs the `build` workflow. Download
`CardinalMini-wasm-simd` from the completed run to get the four web files.
