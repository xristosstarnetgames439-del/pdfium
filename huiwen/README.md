# Huiwen OpenHarmony build

This branch starts from an exact source snapshot of the official PDFium upstream.
The initial synchronization commit records the upstream SHA and retains the GitHub
fork ancestry. The upstream remote is https://pdfium.googlesource.com/pdfium.git.

Local changes:

- `pdf_is_shared_lib=true` builds one shared library with non-component internal
  dependencies and exports the public PDFium C API.
- `huiwen/BUILD.gn` targets `aarch64-linux-ohos`, using the OpenHarmony NDK sysroot,
  archiver and linker driver, and the Clang compiler pinned in upstream DEPS.
- `chromium-build.patch` skips unrelated macOS SDK logging during OHOS builds
  and lets the NDK linker driver supply the target compiler builtins.
- `chromium-buildtools.patch` enables the bundled libc++ musl configuration on
  OpenHarmony. Its private ABI namespace and static linkage isolate it from Qt's
  C++ runtime. Optional SME/SVE libyuv optimizations are disabled for this NDK.

From the parent HuiWenMCP repository, run:

```sh
python3 packaging/ohos/build_pdfium.py --sync
```

The parent build script disables V8, XFA, Skia and PartitionAlloc, links bundled
libc++ statically, checks the required rendering/editing/save exports and dynamic
dependencies, and deploys `libpdfium.so` to `entry/libs/arm64-v8a/`.
Later runs use GN/Ninja incremental compilation against the current working tree.
They do not reset or update the source branch. Build dependency versions stay pinned
by DEPS; dependency synchronization regenerates an ignored `.huiwen_DEPS` with only
the required Git checkouts, without modifying the original DEPS.

No preview, editor or application NAPI interface is included in this change.
