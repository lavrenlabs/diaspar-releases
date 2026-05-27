# Security Policy

Diaspar security reports are handled privately until a fix is ready or a
coordinated disclosure date is agreed. Do not open a public GitHub issue
for suspected vulnerabilities.

## Reporting a Vulnerability

Use one of these private channels:

- Email: security@lavren.dev
- Private GitHub advisory:
  https://github.com/lavrenlabs/diaspar-releases/security/advisories/new

Include enough detail to reproduce and assess the report:

- affected Diaspar version;
- operating system and CPU architecture;
- install method or release asset used;
- steps to reproduce;
- observed behavior and expected behavior;
- impact assessment;
- relevant logs with secrets, tokens, and personal data removed.

## Scope

Reports about the following are in scope:

- the Diaspar binary;
- unsafe handling of credentials, local files, or MCP data;
- vulnerabilities reachable through Diaspar's MCP surface;
- release artifact integrity, checksum coverage, or download tampering;
- diaspar.dev security issues.

Reports about third-party services that Diaspar can connect to should go
to those vendors directly unless the vulnerability is caused by Diaspar's
behavior.

## Response Process

- Reports are acknowledged within 7 days.
- Triage happens privately.
- Fixes are prepared outside this public release mirror.
- When appropriate, a new Diaspar release and a public advisory describe
  the affected versions, the fix, and the recommended user action.

## Supported Versions

The latest published Diaspar release receives security fixes. Older
releases may be assessed case by case when users cannot upgrade
immediately.

## Bug Bounty

Diaspar does not currently run a monetary bug-bounty program. If you want
public credit in an advisory or changelog, say so in the report.

## About This Repository

This repository is a public binary release mirror. It hosts release
artifacts, checksums, release notes, and public security advisories. It is
not the Diaspar source-code repository.
