# Day 02 — IP, DNS, DHCP and Ports as Investigation Evidence

## Objective

Treat network configuration and traffic as investigation evidence rather than isolated networking facts.

## Network Evidence Summary

| Evidence field | Observed value |
|---|---|
| UTC timestamp | Sun Jul 19 17:27:20 UTC 2026 |
| Active interface | `en0` |
| Local IPv4 address | `172.20.10.4` |
| Default gateway | `172.20.10.1` |
| DNS resolver | `8.8.8.8` |

## Raw Network Configuration Evidence

### UTC timestamp

```text
Sun Jul 19 17:27:20 UTC 2026
```

### Active interface

```text
interface: en0
```

### Local IPv4 address

```text
172.20.10.4
```

The address belongs to the private IPv4 range `172.16.0.0/12`.

### Default route and gateway

```text
route to: default
destination: default
mask: default
gateway: 172.20.10.1
interface: en0
flags: <UP,GATEWAY,DONE,STATIC,PRCLONING,GLOBAL>
recvpipe  sendpipe  ssthresh  rtt,msec  rttvar  hopcount  mtu  expire
0         0         0         0         0       0         1500 0
```

### DNS resolver

```text
nameserver[0] : 8.8.8.8
nameserver[0] : 8.8.8.8
```

## Connectivity and DNS Tests

### Ping public IP

Command:

```bash
ping -c 3 1.1.1.1
```

Result:

```text
PING 1.1.1.1 (1.1.1.1): 56 data bytes
64 bytes from 1.1.1.1: icmp_seq=0 ttl=55 time=35.589 ms
64 bytes from 1.1.1.1: icmp_seq=1 ttl=55 time=32.527 ms
64 bytes from 1.1.1.1: icmp_seq=2 ttl=55 time=51.732 ms

--- 1.1.1.1 ping statistics ---
3 packets transmitted, 3 packets received, 0.0% packet loss
round-trip min/avg/max/stddev = 32.527/39.949/51.732/8.425 ms
```

**This proves:** IP reachability to `1.1.1.1` worked at test time.

**This does not prove:** DNS, HTTPS, or all internet services were working.

### Ping domain

Command:

```bash
ping -c 3 example.com
```

Result:

```text
PING example.com (172.66.147.243): 56 data bytes
64 bytes from 172.66.147.243: icmp_seq=0 ttl=55 time=125.604 ms
64 bytes from 172.66.147.243: icmp_seq=1 ttl=55 time=70.525 ms
64 bytes from 172.66.147.243: icmp_seq=2 ttl=55 time=52.971 ms

--- example.com ping statistics ---
3 packets transmitted, 3 packets received, 0.0% packet loss
round-trip min/avg/max/stddev = 52.971/83.033/125.604/30.943 ms
```

**This proves:** `example.com` resolved to an IP and responded to ICMP at test time.

**This does not prove:** The website or HTTPS service was healthy.

### DNS A lookup

Command:

```bash
nslookup -type=A example.com
```

Result:

```text
Server: 8.8.8.8
Address: 8.8.8.8#53

Non-authoritative answer:
Name: example.com
Address: 172.66.147.243
Name: example.com
Address: 104.20.23.154
```

**This proves:** The configured resolver returned IPv4 addresses for `example.com`.

**This does not prove:** The domain is trustworthy or that a browser connected to it.

### DNS MX lookup

Command:

```bash
nslookup -type=MX example.com
```

Result:

```text
Server: 8.8.8.8
Address: 8.8.8.8#53

Non-authoritative answer:
example.com mail exchanger = 0 .
```

**This proves:** The resolver returned an MX response indicating a null mail exchanger.

**This does not prove:** Any email service is available for the domain.

### Traceroute

Command:

```bash
traceroute -m 8 example.com
```

Result:

```text
traceroute: Warning: example.com has multiple addresses; using 172.66.147.243
traceroute to example.com (172.66.147.243), 8 hops max, 40 byte packets
1  172.20.10.1 (172.20.10.1)  6.910 ms  4.125 ms  4.984 ms
2  * *
```

The traceroute was manually stopped after hop 2.

**This proves:** The first responding hop was the configured gateway `172.20.10.1`.

**This does not prove:** Hop 2 or the destination was unavailable, because devices may ignore traceroute probes.

## Decision

**Classification: Neither reachability failure nor name-resolution failure.**

Evidence:

- Public-IP ping succeeded with 0% packet loss.
- Domain ping succeeded with 0% packet loss.
- DNS A lookup returned two IPv4 addresses.
- The configured resolver `8.8.8.8` responded.
- Traceroute reached the local gateway before later probes timed out or were stopped.

## Initial Analyst Interpretation

The endpoint had a private IPv4 address, a configured default gateway, and a configured DNS resolver. Both direct IP reachability and DNS resolution worked during testing. The available evidence therefore does not support either a general reachability failure or a DNS name-resolution failure.

## Remaining Day 2 Work

- Capture and document at least five Wireshark packets using `dns`, `tcp.port == 443`, and `ip.addr == 172.20.10.4`.
- Complete `network/it_ticket_001_dns_failure.md`.
- Complete `network/common_ports_security_cheatsheet.csv`.
- Create `ai-cyber-triage-agent/examples/suspicious_dns_port_activity.txt`.

## Wireshark Packet Evidence

Capture interface: `en0`

Capture file: `day_02_basic_network_capture.pcapng`

Display filters used:

```text
dns
tcp.port == 443
ip.addr == 172.20.10.4
ip.addr == 104.20.23.154 || ip.addr == 172.66.147.243
```

| Frame | Time | Source | Destination | Protocol | Relevant field |
|---:|---:|---|---|---|---|
| 12 | 4.573633 | 172.20.10.4 | 8.8.8.8 | DNS | A query for `example.com`, UDP destination port 53 |
| 13 | 4.739621 | 8.8.8.8 | 172.20.10.4 | DNS | A response returning `172.66.147.243` and `104.20.23.154` |
| 19 | 6.372769 | 172.20.10.4 | 172.66.147.243 | TCP | SYN from source port 57014 to destination port 443 |
| 21 | 6.501145 | 172.66.147.243 | 172.20.10.4 | TCP | SYN/ACK from source port 443 to destination port 57014 |
| 23 | 6.501748 | 172.20.10.4 | 172.66.147.243 | TLS | Client Hello with `SNI=example.com` |

### DNS Observation

The endpoint `172.20.10.4` sent an A-record query for `example.com` to resolver `8.8.8.8`. The resolver returned two IPv4 addresses: `172.66.147.243` and `104.20.23.154`.

**This proves:** The configured resolver received the query and returned IPv4 answers.

**This does not prove:** That the domain is safe or that the user completed a web session.

### TCP Observation

Frames 19, 21 and 22 show the TCP three-way handshake:

1. Client SYN
2. Server SYN/ACK
3. Client ACK

**This proves:** A TCP connection to destination port 443 was established.

**This does not prove:** That the application-layer content was legitimate or safe.

### TLS Observation

Frame 23 shows a TLS Client Hello with `SNI=example.com`.

**This proves:** The client began a TLS session and exposed the requested server name in visible handshake metadata.

**This does not prove:** The encrypted application payload contents, user intent or domain reputation.

### Analyst Conclusion

The DNS response and subsequent TCP/TLS packets correlate the domain `example.com` with destination IP `172.66.147.243` during this capture. The evidence supports successful name resolution and successful establishment of an encrypted HTTPS session.

Port `443` indicates HTTPS/TLS use, but it does not automatically mean the traffic is safe. Malicious infrastructure can also use encrypted traffic.