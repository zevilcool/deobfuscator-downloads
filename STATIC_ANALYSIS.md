# Deobfuscator.zip — Static Analysis Report

## Scope and method

- Archive analyzed: `Deobfuscator-mediafire.zip`
- ZIP SHA-256: `81a56453387a7b57c0422b2601a369a9b37cd72c50e1e22f5ad29c8e640e10b5`
- Extraction was performed under `/home/ubuntu/downloads_task/extracted_mediafire`.
- No Python, Luau, shell, or compiled code from the archive was executed.
- Checks performed: ZIP integrity, path/symlink review, file-type inventory, Python AST parsing, and static searches for networking, process execution, credentials, persistence, and exfiltration indicators.

## Archive inventory

- **96 ZIP entries** total
- **47 Python source files**; all parsed successfully with Python 3.11 AST parsing
- **19 precompiled Python `.pyc` files** under `__pycache__`
- Luau source/runtime files, documentation, and sample inputs/outputs
- Unpacked payload size: **3,141,327 bytes**
- No ZIP comment
- No symlinks or path-traversal filenames detected
- ZIP integrity test passed with no errors

## Project purpose

The contents are a Roblox Luau deobfuscation/devirtualization project. The documented plugins target:

- IronBrew 1
- Luraph v15
- Generic Luau obfuscation

The package includes devirtualization logic, a Luau tracing harness, Roblox API metadata, research/check scripts, and sample obfuscated scripts with expected output.

## Network behavior found

Network access is limited to development/runtime dependencies:

1. `deobf/harness.py` can download the Luau Windows release ZIP from GitHub when a local Luau executable is missing.
2. `deobf/gen_roblox.py` can download the Roblox API dump from `raw.githubusercontent.com`.
3. `deobf/build_luau.py` can clone the Luau Git repository and build the local analyzer.
4. `deobf/harness.py` contains an optional local-only Studio bridge using `127.0.0.1`.

No hard-coded upload destination, webhook, Discord/Telegram endpoint, socket-based exfiltration, or credential/token collection routine was found in the non-sample source.

## Process and file behavior

The project intentionally uses subprocesses to:

- Run local `luau` / `luau-ast` analyzers against input scripts
- Run regression/check scripts
- Optionally re-execute under PyPy for large inputs
- Build Luau from source when requested

It also creates temporary files/directories and removes them after processing. These behaviors are consistent with the stated deobfuscator design, but running the tool on untrusted Luau input should still be done in a sandbox because interpreting arbitrary scripts is its core function.

## Findings and upload recommendation

**Static assessment:** No obvious malware indicators were found in the extracted source. The package is best classified as a source-code analysis tool that executes/analyzes Luau input and may fetch build/runtime dependencies.

Before uploading to GitHub, I recommend excluding the 19 `__pycache__/*.pyc` files because they are generated, platform/interpreter-specific artifacts. Keep the Python/Luau source, documentation, samples, and output fixtures unless a different repository policy is desired.

This analysis does not prove the behavior of every future input script passed to the tool, and it is not a cryptographic or antivirus certification.
