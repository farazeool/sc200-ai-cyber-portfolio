# IT Ticket 001 — DNS Resolution Failure

## Ticket Metadata

- **Ticket ID:** IT-001
- **Date opened:** 2026-07-19
- **Environment:** Synthetic training scenario
- **Affected device:** FIN-MAC-01
- **Operating system:** macOS
- **Priority:** Medium
- **Status:** Resolved
- **Analyst:** Faraz Mustafa Seyed

## User-Reported Symptom

The user reports that websites fail to open when entered by domain name, while direct connectivity to a known public IP still works.

## Scope

- One endpoint affected
- No evidence yet that other users are affected
- General IP reachability appears available
- Name resolution is the suspected failure point

## Evidence and Tests

| # | Test | Result | What it proves | What it does not prove |
|---:|---|---|---|---|
| 1 | Check local IP configuration | Valid private IP present | The endpoint has an IPv4 configuration | The address is conflict-free or authorized |
| 2 | Check default gateway | Gateway configured | A route for non-local traffic exists | The gateway is reachable |
| 3 | Ping `1.1.1.1` | Success | Public IP reachability works | DNS is working |
| 4 | Ping `example.com` | Failure in scenario | The domain could not be used successfully | The website itself is unavailable |
| 5 | `nslookup example.com` | Timeout in scenario | The configured resolver did not return an answer | The domain is malicious |
| 6 | Review DNS resolver | Resolver set to unreachable `203.0.113.53` | The endpoint is configured to use that resolver | The resolver caused the issue until validated |
| 7 | Change resolver to approved `8.8.8.8` | Query succeeds | Resolution works with the approved resolver | Every DNS query will always succeed |

## Symptom Versus Root-Cause Hypothesis

### Symptom

The endpoint cannot access services by domain name.

### Root-Cause Hypothesis

The endpoint is configured to use an unreachable DNS resolver, causing name-resolution failure while basic IP reachability remains available.

## Resolution

1. Replaced the unreachable synthetic resolver `203.0.113.53`.
2. Restored the approved resolver `8.8.8.8`.
3. Re-ran `nslookup example.com`.
4. Re-tested access to `https://example.com`.

## Validation

Validation passed because:

- `nslookup example.com` returned valid A records
- `ping example.com` resolved the domain to an IP
- HTTPS connectivity to `example.com` succeeded
- Direct IP reachability remained available

## Escalation Conditions

Escalate if:

- Multiple endpoints receive the same incorrect DNS resolver
- DHCP is distributing an unauthorized resolver
- DNS responses are incorrect or inconsistent
- Suspicious long subdomains or high-volume TXT queries are observed
- The issue returns after correction

## Final Assessment

- **Classification:** Name-resolution failure
- **Root cause:** Unreachable DNS resolver in the synthetic scenario
- **Impact:** Domain-based access unavailable on one endpoint
- **Resolution status:** Restored
- **Confidence:** High within the supplied scenario
- **Limitation:** This ticket is synthetic training evidence and does not represent a real production incident
