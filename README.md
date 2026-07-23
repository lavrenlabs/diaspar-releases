# Diaspar Releases

Public binary releases for **Diaspar** — persistent memory for AI coding agents.

The Diaspar source repository is private. This repository contains release artifacts only, so package managers and installers can download published binaries.

- **Website and documentation:** <https://diaspar.dev>
- **Latest release:** [releases/latest](https://github.com/lavrenlabs/diaspar-releases/releases/latest)
- **Install guide:** <https://diaspar.dev/install/>
- **License:** [Diaspar License](LICENSE) — the binary is free to use, including commercially. Also published at <https://diaspar.dev/license/>.

## Install

### Homebrew (macOS)

```bash
brew install --cask lavrenlabs/tap/diaspar
```

### Scoop (Windows)

```powershell
scoop bucket add lavrenlabs https://github.com/lavrenlabs/scoop-bucket
scoop install diaspar
```

### Direct download

Six pre-built binaries per release, named `diaspar_<os>_<arch>.<ext>`, for macOS, Linux, and Windows on x86-64 and arm64. Per-platform commands: <https://diaspar.dev/docs/installation/>.

## Connect it to your agent

After installing, run the setup wizard once and register your agent:

```bash
diaspar init
diaspar setup claude-code    # or cursor | vscode | codex | gemini-cli | opencode
```

`diaspar setup <agent>` is idempotent — re-running preserves the rest of the agent's configuration. Any other MCP-capable agent can be pointed at the `diaspar mcp` stdio server manually.

Per-agent setup guides:

| Agent | Guide |
|---|---|
| Claude Code | <https://diaspar.dev/docs/agent-setup/claude-code/> |
| Cursor | <https://diaspar.dev/docs/agent-setup/cursor/> |
| VS Code | <https://diaspar.dev/docs/agent-setup/vscode/> |
| Codex | <https://diaspar.dev/docs/agent-setup/codex/> |
| Gemini CLI | <https://diaspar.dev/docs/agent-setup/gemini-cli/> |
| OpenCode | <https://diaspar.dev/docs/agent-setup/opencode/> |

Quickstart: <https://diaspar.dev/docs/getting-started/> · MCP tool reference: <https://diaspar.dev/docs/mcp-tools/>

## Verify a download

Releases **from v3.4.1 onward** publish `checksums.txt` (a SHA-256 for each artifact) and `checksums.txt.sig`, an RSA-4096 signature over that file produced by the release pipeline.

> **Which releases this applies to.** v3.4.1 is the first release built by the signed-evidence pipeline. **v3.4.0 and earlier publish `checksums.txt` and the platform archives only** — no `checksums.txt.sig` and no evidence bundle. Step 2 below cannot be completed against them — there is no `.sig` file to verify — so only step 3, the plain checksum comparison, applies. Use a current release if you need a verifiable signature.

**Verify the signature first, then the archive.** A checksum file you have not authenticated proves nothing on its own.

### 1. Get the signing key

Also served from <https://diaspar.dev/diaspar-release-public.pem>.

```
-----BEGIN PUBLIC KEY-----
MIICIjANBgkqhkiG9w0BAQEFAAOCAg8AMIICCgKCAgEAttBp21NHGrJJPKMvt5vp
lEE9dKW95NJM7wTj29M5hzxpBmTLdHA+XsoWuMB5QOSIMwGs/Ir5zLcllSkBrqry
AGhMTRQbEtQgi1ZCWB694UTrMvqgItkyGY6nSJCffhyJzIUi88U8FahnPoFPjSpB
Wp5P0e7VrrXV6iUDw1d5AuxcG2bZWS/+L2Ugf0oeASN3HcEdxhCDq0LShDj1RB3j
q4NjQcpfipXsxsica+I+rEvKjbX/TaTFnrC2Ar4PDR8ajQ1uMl3EQReIc8S635Dy
e3vYOChu9X9W+PVv5HKvwKgk71kbSaHtF7mI2XLueF48u4WlfEZd4YK+rlxrFdoI
11JENWZJWA4LM4SHBeb+EDmCTqmscKlc9nELYJIt9kyYEdDgcTY3a0DGX7LYCZX+
6BlkppG8fgm99AlDEuSCR1k5kllJJdndVnPSBTMFgAlUmKrcuXciCrbpniKF7rSW
fYP3Sagl9m+ft8QjmDTm71O3YyTkAm4A+j1zqwEeKY6rqfIkQ8dMxvXKTYZXnG5m
nU4IXgLqLRXfb5tcVbTuRi/PhG32LxMKf9/bxg1nMWL2tiIlY7qg5jtDZrYabGUl
kBly/esMzI3YD6Y8+oWtkqF8t+CysVNwW6QGHaPoyHiFRiJbKI8qqLJpb8UCx7LQ
NTBOEVAQ3hTpsuLIYqhQ78kCAwEAAQ==
-----END PUBLIC KEY-----
```

Save it as `diaspar-release-public.pem` and confirm the key material. The expected SPKI SHA-256 fingerprint is:

```
1868fd7e1daeda15b8fa0f7c2825458e9b71b905557cf6c70463b0c24b79324f
```

```bash
openssl pkey -pubin -in diaspar-release-public.pem -outform DER | openssl dgst -sha256
```

This is a digest of the key itself, not of the `.pem` file, so it does not change with line endings.

### 2. Verify the signature over `checksums.txt`

Download both files from the **same release** as your archive:

```bash
curl -fsSLO https://github.com/lavrenlabs/diaspar-releases/releases/latest/download/checksums.txt
curl -fsSLO https://github.com/lavrenlabs/diaspar-releases/releases/latest/download/checksums.txt.sig

openssl dgst -sha256 -verify diaspar-release-public.pem \
  -signature checksums.txt.sig checksums.txt
```

Expected output is exactly `Verified OK`. Anything else means stop — do not run the binary.

### 3. Verify the archive against the checksums

```bash
shasum -a 256 -c checksums.txt --ignore-missing
```

On Windows PowerShell:

```powershell
$expected = (Select-String -Path checksums.txt -Pattern "diaspar_windows_amd64.zip").Line.Split()[0]
$actual   = (Get-FileHash -Algorithm SHA256 .\diaspar_windows_amd64.zip).Hash.ToLower()
"$expected  $actual  ->  $($expected -eq $actual)"
```

### What this proves — and what it does not

The key is published on two surfaces, this repository and <https://diaspar.dev>, **both controlled by Lavren Labs**. There is no transparency log, notary, certificate authority, or other third-party attestation behind it.

- A successful verification proves `checksums.txt` was signed by the holder of the Diaspar release key and has not been altered since, and — through the checksums — that your archive is bit-for-bit the published artifact.
- It does **not** independently prove who that key holder is. That rests on the two publication surfaces above. Cross-check the fingerprint against both and pin it locally, so a future change is something you notice rather than accept.

Full trust statement: <https://diaspar.dev/security/>.

## Release contents

Every release publishes six platform archives and `checksums.txt`. Releases **from v3.4.1 onward** additionally publish the signature and the evidence bundle:

| Asset | Since | What it is |
|---|---|---|
| `diaspar_<os>_<arch>.<ext>` | all | Six platform archives. |
| `checksums.txt` | all | SHA-256 for every archive, and for every evidence file below when present. |
| `checksums.txt.sig` | v3.4.1 | RSA-4096 signature over `checksums.txt`. |
| `release-manifest.json` | v3.4.1 | Product, version, source commit and tag, and each archive's hash and size. |
| `release-evidence.json` | v3.4.1 | Index of every published file with its hash, plus build and toolchain provenance. |
| `sbom.cdx.json` | v3.4.1 | CycloneDX software bill of materials. |
| `sbom.spdx.json` | v3.4.1 | SPDX software bill of materials. |
| `govulncheck.json` | v3.4.1 | Go vulnerability scan report. |
| `security-scan.json` | v3.4.1 | Secret-scan report. |

On releases that carry them, every one of these files is covered by `checksums.txt`, so verifying the signature covers the whole evidence bundle. The release pipeline verifies each artifact hash and the signature **before** it publishes anything — to this repository, to the Homebrew tap, or to the Scoop bucket. A failed verification publishes nothing.

## Support

- **Documentation:** <https://diaspar.dev/docs/getting-started/>
- **Download or install problems:** [open an issue](https://github.com/lavrenlabs/diaspar-releases/issues/new/choose)
- **Security reports:** private channels only — see [SECURITY.md](SECURITY.md)
