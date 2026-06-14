# Security Policy

This is the organization-wide security policy for all
[Forail Platform](https://github.com/forail-platform) repositories. Individual
repositories may also ship their own `SECURITY.md`; where they do, it takes
precedence for that repository.

## Reporting a vulnerability

Please report security issues **privately**. Do not open a public issue.

- Email: **office@krletron.xyz**
- Include: affected repository and version, a description of the issue, and
  steps to reproduce or a proof of concept where possible.

You can expect an acknowledgement within a few business days. Confirmed
vulnerabilities are handled privately until a fix is released, after which a
public advisory / CVE is published.

## Supported versions

Forail ships as a coordinated monthly platform release (CalVer). Security fixes
target the latest released version. Older versions are best-effort.

## Scope

In scope: the Forail backend, frontend, operator, Helm chart, deployment
tooling, and the AI assistant. The AI assistant is in preview and not yet
production-ready — treat findings there as preview-quality.
