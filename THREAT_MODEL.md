# Gatepath Threat Model
Scope: WireGuard VPN paths, optional Tor SOCKS gateway, Tor-only paths

## 1. Scope and goals

This document describes the threat model for the Gatepath VPN infrastructure.

Gatepath provides:
- a standard WireGuard VPN path (clearnet egress)
- an optional Tor SOCKS gateway (explicit, application-scoped)
- Tor-only WireGuard paths with no clearnet fallback

The goals are to:
- reduce accidental network exposure
- provide explicit routing choices with clear trust boundaries
- fail closed where Tor is intended

This document does not make anonymity guarantees and does not attempt to replace
or modify Tor Browser.

## 2. System overview

### 2.1 Standard VPN path (non-Tor)
- WireGuard tunnel to the server
- Egress to clearnet via NAT
- Intended for general encrypted transport
- No transparent Tor usage

### 2.2 Tor SOCKS gateway (optional)
- Tor runs as a client-only instance on the server
- SOCKS is exposed to VPN clients via a controlled forwarder
- Applications must explicitly opt in by using SOCKS
- Tor traffic is not transparently mixed with non-Tor traffic

### 2.3 Tor-only WireGuard paths
- A dedicated WireGuard path/subnet for Tor-only traffic
- No clearnet NAT is provided for that path
- Traffic exits exclusively via Tor
- If Tor is unavailable, traffic fails closed (no fallback)

## 3. Threats this setup helps mitigate

This infrastructure helps protect against:
- passive local network observers (e.g. public Wi-Fi)
- ISP-level visibility into destinations and content metadata (outside the VPN)
- accidental IP exposure when Tor-aware applications are used incorrectly
- DNS leaks on Tor-only paths (by enforcing Tor-handled DNS)
- accidental clearnet fallback where Tor-only routing is intended
- unclear trust boundaries created by transparent proxying

## 4. Threats this setup does NOT mitigate

This setup does NOT protect against:
- malware or a compromised endpoint/device
- browser fingerprinting, tracking scripts, or account-based tracking
- identity leaks at the application layer (logins, cookies, telemetry)
- a user misconfiguring applications (e.g. bypassing proxy)
- targeted attacks against Tor itself
- global passive adversaries
- traffic correlation attacks outside Tor’s threat model
- adversaries controlling both Tor entry and exit (or significant parts of the network)

Gatepath does not claim:
- anonymity by default
- protection against endpoint compromise
- Tor Browser equivalence

## 5. Assumptions

Design assumptions:
- Tor usage must be explicit and opt-in
- Tor Browser is not replaced or modified
- users are responsible for application behavior
- clarity is preferred over convenience
- failing closed is preferable to silent fallback

## 6. Design decisions

### 6.1 No transparent proxying
Gatepath intentionally avoids transparent Tor routing.
Transparent proxying can cause unintended Tor usage, mixed flows, and unclear trust boundaries.

### 6.2 Separation of Tor and non-Tor paths
Tor-only routing is provided as a separate path/subnet to avoid “mixed intent” traffic.

### 6.3 Tor-only path fails closed
Where Tor-only routing is intended, clearnet egress is not provided.

### 6.4 DNS handling on Tor paths
Direct DNS egress (UDP/TCP 53) is not relied upon for Tor paths.
DNS is expected to be handled through Tor mechanisms to reduce leakage.

## 7. Logging and observability

The infrastructure is designed to minimize stored data.
Operational telemetry (uptime, service health) may exist, but this repository
does not define a formal logging policy.

## 8. Intended users

This setup is intended for users who:
- want explicit routing control
- understand Tor limitations and tradeoffs
- do not expect anonymity guarantees
- prefer documented trust boundaries

## 9. Non-goals

Gatepath does not aim to:
- defeat browser fingerprinting
- bypass account-based tracking
- provide censorship circumvention guarantees
- act as a Tor Browser replacement
- provide “automatic anonymity”

## 10. Summary

Gatepath provides:
- encrypted transport via WireGuard
- explicit, opt-in Tor usage via SOCKS
- Tor-only paths that fail closed without clearnet fallback

Users remain responsible for application-level behavior and their personal threat model.
