# Design Notes

This repository documents design decisions for Gatepath’s VPN infrastructure.
The focus is explicit routing, clear trust boundaries, and minimal assumptions.

## Principles

- Explicit opt-in over convenience
- No transparent proxying
- Separation of Tor and non-Tor traffic intent
- Fail closed where Tor-only routing is intended
- Minimal state where possible
- Document limitations rather than making broad claims

## Network paths

### Standard VPN (clearnet egress)
- WireGuard tunnel
- NAT egress to clearnet
- Intended for general encrypted transport

### Optional Tor SOCKS gateway
- Tor runs locally as a client-only instance
- SOCKS access is provided to VPN clients via a controlled forwarder
- Applications must explicitly opt in (SOCKS)
- No transparent routing, no “magic” mixing

### Tor-only path
- Dedicated WireGuard path/subnet
- No clearnet NAT or fallback
- Egress exclusively via Tor
- Designed to fail closed if Tor is unavailable

## Why no transparent Tor routing
Transparent proxying is avoided because it:
- hides routing intent from users
- can mix Tor and non-Tor flows unintentionally
- increases the risk of accidental leakage or false expectations
- complicates debugging and review

## Intended outcome
The infrastructure should be boring and predictable:
- users can choose a standard VPN path
- or explicitly use Tor via SOCKS
- or use a Tor-only path with no clearnet escape
