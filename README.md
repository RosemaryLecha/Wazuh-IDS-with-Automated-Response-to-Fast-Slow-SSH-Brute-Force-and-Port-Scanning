# Wazuh IDS with Automated Response to Fast/Slow SSH Brute Force and Port Scanning

> Final Year Project | Rose Mary Lecha Ngwa Mbenoh | BSc in Cybersecurity, ICT University | Supervisor: Dr Nkandeu Pascal | 2025/2026
> **Status: Complete** (defended 2026)

A low-cost intrusion detection and automated response system built on **Wazuh**. It detects **fast and slow SSH brute-force attacks** and **port-scanning reconnaissance**, and blocks the attacker automatically. It is designed for small and medium organisations that cannot afford enterprise tools.

---

## The Problem

Small organisations such as microfinance institutions, small clinics and hosting providers often expose SSH to the internet. They usually cannot afford enterprise defences like UEBA platforms or intelligent next-gen firewalls.

Wazuh's default detection has three gaps:

- **Threshold-based rules** only catch fast, high-volume brute force.
- **Slow ("low and slow") brute-force attacks** stay under the threshold and go unseen.
- **Port scans** have no native detection rule.

This project closes those gaps with a lightweight, explainable rule-based layer on top of Wazuh.

## Aim and Objectives

Design, build and evaluate a Wazuh-based system that detects and safely responds to fast and slow SSH brute force and port scanning.

1. Evaluate Wazuh's default detection (baseline)
2. Design a detection layer for slow, evasive attacks
3. Add port-scan detection
4. Design a safe automated response (allowlists, escalating blocks)
5. Evaluate with measurable metrics: detection rate, false-positive rate, detection latency, response time
6. Assess practicality for small organisations

## How It Works

1. **Detection**: Wazuh's native rules catch fast brute force. `psad` detects port scans, with a custom Wazuh decoder and rule to ingest its alerts.
2. **Correlation engine**: A rule-based (knowledge-based) Python service polls the Wazuh REST API and keeps a rolling risk score per source IP.
   - Failed logins are scored over a **72-hour window**, which exposes slow brute force.
   - If the same IP also triggered a port-scan alert in the **previous 24 hours**, a correlation bonus is added.
   - Once the score passes a threshold, the engine issues a verdict with a plain-language explanation.
3. **Safe active response**:
   - Only IPs/MACs explicitly allowlisted by the admin are exempt (never "anyone on the LAN").
   - Blocks escalate: short block, then longer block, then long-term block needing manual review.
   - The admin is alerted by **email and Telegram**.
4. **Storage and dashboard**:
   - Decisions are stored in **SQLite**.
   - **GeoIP** enrichment uses MaxMind GeoLite2.
   - A **Flask** dashboard (Bootstrap + Leaflet.js) shows an attack map and plain-language explanations.

The engine is rule-based rather than machine learning, so every verdict can be explained to non-technical staff.

## Lab Setup

A physical penetration test on a real bridged Wi-Fi network, not just a simulation:

| Machine | Role | Details |
|---|---|---|
| Physical Machine 1 | Attacker | Kali Linux (Nmap, Hydra, custom slow-attack script, aircrack-ng) |
| Physical Machine 2 | Wazuh Manager | Ubuntu Server VM (VirtualBox) with correlation engine, SQLite DB and dashboard |
| Physical Machine 3 | Victim A (protected) | Ubuntu Server VM: OpenSSH, psad, Wazuh Agent |
| Physical Machine 3 | Victim B (control) | Ubuntu Server VM: OpenSSH only, no monitoring |

Victim A and Victim B were attacked simultaneously under identical conditions for a live protected-versus-unprotected comparison.

## Tools and Technologies

Wazuh (Manager, Indexer, Dashboard, Active Response) | psad | Python 3 (`requests`, `schedule`) | SQLite | Flask | Bootstrap | Leaflet.js | MaxMind GeoLite2 | Oracle VirtualBox | Kali Linux | Nmap | Hydra | aircrack-ng suite | smtplib | Telegram Bot API

## Methodology

Design Science Research Methodology (DSRM). Testing stages:

- **Stage 0**: Wireless compromise (WPA2 handshake capture and dictionary attack)
- **Stage A**: Baseline run against default Wazuh
- **Stage B**: Build the enhancement layer
- **Stage C**: Comparative testing (Victim A vs Victim B)
- **Stage D**: Practicality assessment

## Results

> Replace with your actual findings.

| Metric | Default Wazuh | Enhanced System |
|---|---|---|
| Fast brute-force detection rate | [ ]% | [ ]% |
| Slow brute-force detection rate | [ ]% | [ ]% |
| Port-scan detection rate | [ ]% | [ ]% |
| False-positive rate | [ ]% | [ ]% |
| Detection latency | [ ] s | [ ] s |
| Response time | [ ] s | [ ] s |

**Live comparison:** Victim A [detected and blocked the attacks], while the attacks on Victim B [succeeded undetected].

## Repository Contents

```
├── report/     Full thesis report (PDF)
├── docs/       Screenshots and architecture diagram
└── README.md
```

Full report: [`report/thesis-report.pdf`](report/thesis-report.pdf)

## Limitations

- Tested on a private, controlled network, not a live production system.
- Slow-attack detection covers a **single source IP**. Distributed (botnet-style) slow attacks are left as future work.
- SSH traffic is encrypted, so detection uses connection and authentication metadata only.
- Attacks were generated by the researcher using Nmap, Hydra and a custom script, not captured from real attackers.
- Small scale (three physical machines), not enterprise scale.

## Future Work

- Detect distributed slow brute-force attacks by aggregating across many source IPs
- Compare the rule-based approach with machine-learning models
- Extend to other services beyond SSH

## Ethical Notice

All attacks, including the wireless compromise stage, were performed only on equipment and networks owned by the researcher, under supervisor guidance, for academic purposes. **Do not use these techniques against systems you do not own or have written permission to test.**

## Author

**Rose Mary Lecha Ngwa Mbenoh**
BSc in Cybersecurity, ICT University, 2025/2026
Supervisor: Dr Nkandeu Pascal
Contact: officielrosy@gmail.com

## License

[MIT / All rights reserved. Choose one.]
