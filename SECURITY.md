# Security Policy

## Reporting a Vulnerability

If you discover a security vulnerability, please **do not open a public issue**. Instead, email:

**goodeplays@gmail.com** with the subject line `SECURITY: <repo-name>`.

Include:
- Description of the vulnerability
- Steps to reproduce
- Affected versions / commits
- Suggested fix if you have one

You will receive an acknowledgment within 48 hours.

## Supported Versions

This is a portfolio project. Only the `main` branch is supported.

## Scope

In scope:
- Authentication and authorization bypass
- Injection (SQL, LLM prompt injection, command)
- Cryptographic weakness in chaincode or token handling
- PII / PHI handling violations
- Dependency vulnerabilities with a confirmed exploit path

Out of scope:
- Issues requiring physical access
- Issues in test/mock providers (`MOCK_FABRIC=true`, mock LLM provider) used only in local development
- Theoretical issues without a demonstrated impact

## Responsible Disclosure

Please give us a reasonable window (90 days) to address the issue before any public disclosure.
