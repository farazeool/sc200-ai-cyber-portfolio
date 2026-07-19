# Day 02 — IP, DNS, DHCP and Ports as Investigation Evidence

**Verification status:** not yet run for Day 02  
**Environment:** M1 MacBook Pro / macOS  
**Rule:** paste raw command output. Keep facts, interpretation, and limitations separate.

## Six-row evidence table

| Evidence field | Actual value | Source command | Investigation meaning |
|---|---|---|---|
| UTC timestamp | TODO | `date -u` | Correlates evidence across logs |
| Interface name | TODO | `route get default \| grep interface` | Identifies the active network interface |
| Local IP | TODO | `ipconfig getifaddr <interface>` | Identifies the endpoint on the local network |
| Prefix/netmask | TODO | `ifconfig <interface>` | Defines the local subnet boundary |
| Default gateway | TODO | `route -n get default` | Identifies the next hop for non-local traffic |
| DNS resolver | TODO | `scutil --dns` | Identifies the resolver used for name resolution |

## 1. UTC timestamp

```text
TODO: paste output of date -u
```

**This proves:** TODO  
**This does not prove:** TODO

## 2. Interface and local addressing

Commands:

```bash
route get default | grep interface
ipconfig getifaddr en0
ifconfig en0
```

```text
TODO: paste raw output
```

**This proves:** TODO  
**This does not prove:** TODO

## 3. Default gateway

Command:

```bash
route -n get default
```

```text
TODO: paste raw output
```

**This proves:** TODO  
**This does not prove:** TODO

## 4. DNS resolver

Command:

```bash
scutil --dns
```

```text
TODO: paste relevant raw output without inventing values
```

**This proves:** TODO  
**This does not prove:** TODO

## 5. DHCP evidence

Command:

```bash
ipconfig getpacket en0
```

```text
TODO: paste raw output or document that no packet details were returned
```

Record when available: assigned address, subnet mask, router, DNS server, lease time.

**This proves:** TODO  
**This does not prove:** TODO

## 6. Reachability test

Command:

```bash
ping -c 3 1.1.1.1
```

```text
TODO: paste raw output
```

**This proves:** TODO  
**This does not prove:** TODO

## 7. Name-resolution and domain test

Commands:

```bash
ping -c 3 example.com
nslookup example.com
nslookup -type=A example.com
nslookup -type=MX example.com
```

```text
TODO: paste raw output
```

**This proves:** TODO  
**This does not prove:** TODO

## 8. Route test

Command:

```bash
traceroute -m 8 example.com
```

```text
TODO: paste raw output
```

**This proves:** TODO  
**This does not prove:** TODO

## Decision

Choose one and justify it with the evidence above:

- [ ] Reachability failure
- [ ] Name-resolution failure
- [ ] Both
- [ ] Neither

**Decision and rationale:** TODO

## Wireshark packet evidence

Capture only traffic you own or are authorised to inspect. Start a capture, visit `https://example.com`, stop after about 30 seconds, and apply these filters separately:

```text
dns
tcp.port == 443
ip.addr == <your-IP>
```

| Frame | Timestamp | Source | Destination | Protocol | Relevant field/observation |
|---:|---|---|---|---|---|
| TODO | TODO | TODO | TODO | TODO | TODO |
| TODO | TODO | TODO | TODO | TODO | TODO |
| TODO | TODO | TODO | TODO | TODO | TODO |
| TODO | TODO | TODO | TODO | TODO | TODO |
| TODO | TODO | TODO | TODO | TODO | TODO |

## Analyst conclusion

- Observed facts: TODO
- Assumptions/hypotheses: TODO
- Missing evidence: TODO
- Next investigation action: TODO
