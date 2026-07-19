# SC-200 + AI Cyber Portfolio

## Goal

Build defensible, evidence-first skills for Microsoft SC-200 and entry-to-intermediate security operations work through the **35-Day Exact Action Manual v2.0**. Every completed day must produce an artifact that can be explained, tested, and honestly defended.

## Target Roles

- SOC Analyst / Security Operations Analyst
- IT Security Operations / NOC Security
- GRC / Information Security Analyst
- IAM / PAM-aware Security Analyst
- Microsoft Security Operations / Sentinel-focused Analyst

## Lab Environment

- Primary device: M1 MacBook Pro, 16 GB RAM
- Windows work: Windows 11 ARM in Parallels when a Windows-only step is required
- Networking: Wireshark and Cisco Packet Tracer
- Microsoft exercises: authorised tenant when available, otherwise the manual's synthetic/no-tenant KQL track
- AI companion project: separate `ai-cyber-triage-agent` repository with mandatory human review

## Evidence Index

### Day 01 — Career OS and proof-first workflow

- [`notes/day_01_foundation_terms.md`](notes/day_01_foundation_terms.md) — completion template; explanations must be written in the learner's own words
- [`notes/day_01_raw_network_commands.txt`](notes/day_01_raw_network_commands.txt) — consolidated real Mac network output with proof/limitation notes
- [`network/DAY_01_MAP_REQUIRED.md`](network/DAY_01_MAP_REQUIRED.md) — reminder for the still-missing required PNG map
- [`job-search/cv_baseline_day_01.md`](job-search/cv_baseline_day_01.md) — baseline career facts and target roles

### Day 02 — IP, DNS, DHCP and ports as investigation evidence

- [`network/day_02_network_commands.md`](network/day_02_network_commands.md) — exact evidence sheet awaiting fresh command and Wireshark results
- [`network/it_ticket_001_dns_failure.md`](network/it_ticket_001_dns_failure.md) — ticket template awaiting the supplied synthetic case
- [`network/common_ports_security_cheatsheet.csv`](network/common_ports_security_cheatsheet.csv) — ports seed awaiting investigation-focused completion

### Manual tracking

- [`MANUAL_ALIGNMENT.md`](MANUAL_ALIGNMENT.md) — current pass/fail and missing-artifact status

## Honest Limitations

- Work is in progress and should not be interpreted as production deployment experience.
- Day 1 is not fully passed until the required home-network PNG exists and the 12 terms are explained from memory.
- Day 2 is not complete until fresh raw outputs, five Wireshark packet records, the supplied DNS case ticket, the completed ports CSV, independent practice, and closed-note recall pass.
- Microsoft cloud work must be labelled `conceptual`, `synthetic-manual-validation`, or `tested` according to the actual environment.
- AI output is advisory only. Verdict, severity, evidence interpretation, and containment approval remain human responsibilities.
