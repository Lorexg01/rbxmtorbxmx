FULLY BY AI!!

# RBXM Studio 1.0

An offline Windows 10/11 x64 desktop converter and Roblox model inspector. The executables are unsigned. This build was cross-compiled and inspected here; Windows and Roblox Studio runtime testing were not available.

## Use

Extract the entire Windows ZIP first and keep its runtime DLLs beside the executables. Run `RBXM-Studio.exe`, select **Open files…**, or drag multiple `.rbxm`, `.rbxmx`, `.rbxl`, and `.rbxlx` files onto the window.

- Explorer: browse instances and properties; search script paths or all source text.
- Source: syntax-highlighted, read-only source viewer and Copy source.
- Bytecode: disassembly of supported original compiled Source values.
- Save model: export binary or XML model/place. Choose the correct extension. This does not turn arbitrary models into functioning games.
- Export scripts + report: choose a parent directory. The app creates `<input-name>-scripts` with unique filenames, original bytecode, and a JSON path/status manifest.
- Batch loading runs in the background. Cancellation is checked between scripts. An individual decompiler call cannot be interrupted.
- Existing output files/folders are never overwritten. Your input is not modified.

## Recovery support and limitations

Ordinary source text is retained. For the supplied 2023 client DataModelPatch, `Source` has binary type `0x1D`, containing compiled Luau v3 with a known 8-byte wrapper and Roblox opcode multiplier. The app recognizes that exact wrapper, normalizes opcodes, reconstructs source with Medal, and compares compiled reconstructions using a lightweight structural heuristic (calls, reads, writes, prototype counts, and string constants). It tries Pumbaa when Medal fails compilation or its structural score is below 0.999, keeping the higher-scoring compilable result. Low-scoring output is flagged for review. The similarity score is not a correctness probability.

**This is not exact source restoration.** Original comments and most variable names are not present in bytecode. Syntax validation does not prove equivalent behavior. Review and test reconstructed code. Scripts that cannot be reconstructed get clearly marked, comment-only source rather than invented functional code. Their original bytes remain available in the scripts export. Unsupported wrappers/versions are reported, not guessed. Unknown Source encodings other than the supported binary type are not decoded.

Recovery changes the signed data. The original Roblox `SIGN` chunk is removed when compiled Source is replaced; the tool cannot create Roblox's private signature. Studio source recovery is the goal, not signed client execution. CoreScripts and CorePackages may depend on Roblox-only permissions and services and are not drop-in game scripts.

Input and individual decompressed chunks have a 512 MiB safety limit. Processing stays on your computer; no scripts are executed or uploaded. The app is not a malware sandbox: use trusted model files and keep a copy of the original.

Binary-to-binary saves retain the original decompressed chunks except the intentionally replaced Source properties and invalidated SIGN. XML conversion and XML-to-binary use rbx-dom; unsupported properties/classes may be subject to that library's format support. Original compressed byte streams are not reproduced.

## Command line

```powershell
.\RBXM-Studio.exe --cli "input.rbxm" --out "new-output.rbxmx" --scripts "new-scripts-folder"
```

Validate a flat scripts export without executing any code:

```powershell
.\RBXM-Studio-CLI.exe --validate-scripts "scripts-folder"
```

The GUI-subsystem Windows executable can be launched from PowerShell with `Start-Process -Wait` if you need to wait for completion. For console output and exit-code workflows, use the included `RBXM-Studio-CLI.exe` build. CLI options are identical. Outputs must be new paths.

## Build from source

Dependencies are fetched from crates.io and the pinned petgraph git revision in Cargo.lock.

```sh
rustup toolchain install nightly-2025-03-01 --profile minimal
cargo +nightly-2025-03-01 build --release --locked
cargo +nightly-2025-03-01 test --locked
```

On Windows, install MSVC C++ build tools and use the native target. The tested cross-build target is `x86_64-pc-windows-gnu`, with MinGW-w64 gcc/g++ available. The bundled `.cargo/config.toml` selects its linker for that target. The source archive includes `converter/` and its sibling `ext/` dependencies; preserve this layout. The nightly toolchain is pinned for Medal's box-pattern features. The vendored rbx_binary 3.0.0 has a one-line feature-gate compatibility patch for this nightly; its algorithm is unchanged. The Windows ZIP includes the MinGW C++ runtime DLLs; keep them beside the executables. async-io is pinned to 2.4.1 in Cargo.lock.

## Credits

Built using egui/eframe, rbx-dom, mlua/Luau, Medal (shrimp-nz/medal), and PumbaaDev/luau-decompiler. Third-party notices and licenses are included in the distribution. This tool is not affiliated with Roblox.
