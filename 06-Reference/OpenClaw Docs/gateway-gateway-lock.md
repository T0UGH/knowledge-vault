# gateway/gateway-lock

Source: https://docs.openclaw.ai/gateway/gateway-lock

Title: Gateway Lock - OpenClaw

URL Source: https://docs.openclaw.ai/gateway/gateway-lock

Markdown Content:
##### Gateway

*   [Gateway Runbook](https://docs.openclaw.ai/gateway)
*       *   [Configuration](https://docs.openclaw.ai/gateway/configuration)
    *   [Configuration Reference](https://docs.openclaw.ai/gateway/configuration-reference)
    *   [Configuration Examples](https://docs.openclaw.ai/gateway/configuration-examples)
    *   [Authentication](https://docs.openclaw.ai/gateway/authentication)
    *   [Auth Credential Semantics](https://docs.openclaw.ai/auth-credential-semantics)
    *   [Secrets Management](https://docs.openclaw.ai/gateway/secrets)
    *   [Secrets Apply Plan Contract](https://docs.openclaw.ai/gateway/secrets-plan-contract)
    *   [Trusted Proxy Auth](https://docs.openclaw.ai/gateway/trusted-proxy-auth)
    *   [Health Checks](https://docs.openclaw.ai/gateway/health)
    *   [Heartbeat](https://docs.openclaw.ai/gateway/heartbeat)
    *   [Doctor](https://docs.openclaw.ai/gateway/doctor)
    *   [Gateway Logging](https://docs.openclaw.ai/gateway/logging)
    *   [Logging Overview](https://docs.openclaw.ai/logging)
    *   [Gateway Lock](https://docs.openclaw.ai/gateway/gateway-lock)
    *   [Background Exec and Process Tool](https://docs.openclaw.ai/gateway/background-process)
    *   [Multiple Gateways](https://docs.openclaw.ai/gateway/multiple-gateways)
    *   [Troubleshooting](https://docs.openclaw.ai/gateway/troubleshooting)

##### Security

*   [Formal Verification (Security Models)](https://docs.openclaw.ai/security/formal-verification)
*   [Threat Model (MITRE ATLAS)](https://docs.openclaw.ai/security/THREAT-MODEL-ATLAS)
*   [Contributing to the Threat Model](https://docs.openclaw.ai/security/CONTRIBUTING-THREAT-MODEL)

*   [Gateway lock](https://docs.openclaw.ai/gateway/gateway-lock#gateway-lock)
*   [Why](https://docs.openclaw.ai/gateway/gateway-lock#why)
*   [Mechanism](https://docs.openclaw.ai/gateway/gateway-lock#mechanism)
*   [Error surface](https://docs.openclaw.ai/gateway/gateway-lock#error-surface)
*   [Operational notes](https://docs.openclaw.ai/gateway/gateway-lock#operational-notes)

Configuration and operations

## Gateway lock

Last updated: 2025-12-11

## Why

*   Ensure only one gateway instance runs per base port on the same host; additional gateways must use isolated profiles and unique ports.
*   Survive crashes/SIGKILL without leaving stale lock files.
*   Fail fast with a clear error when the control port is already occupied.

## Mechanism

*   The gateway binds the WebSocket listener (default `ws://127.0.0.1:18789`) immediately on startup using an exclusive TCP listener.
*   If the bind fails with `EADDRINUSE`, startup throws `GatewayLockError("another gateway instance is already listening on ws://127.0.0.1:<port>")`.
*   The OS releases the listener automatically on any process exit, including crashes and SIGKILL—no separate lock file or cleanup step is needed.
*   On shutdown the gateway closes the WebSocket server and underlying HTTP server to free the port promptly.

## Error surface

*   If another process holds the port, startup throws `GatewayLockError("another gateway instance is already listening on ws://127.0.0.1:<port>")`.
*   Other bind failures surface as `GatewayLockError("failed to bind gateway socket on ws://127.0.0.1:<port>: …")`.

## Operational notes

*   If the port is occupied by _another_ process, the error is the same; free the port or choose another with `openclaw gateway --port <port>`.
*   The macOS app still maintains its own lightweight PID guard before spawning the gateway; the runtime lock is enforced by the WebSocket bind.

[Logging Overview](https://docs.openclaw.ai/logging)[Background Exec and Process Tool](https://docs.openclaw.ai/gateway/background-process)
