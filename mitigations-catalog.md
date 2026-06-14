# Mitigations Catalog

Controls approved for use in column 7 (Mitigation Measures). Select by ID. Adapt the standard wording to the system context - do not copy verbatim without filling placeholders.

Rules:
- A control may only justify a **Likelihood** drop if its Reduces column says Likelihood (or Both) AND the vulnerability class is in its Applicable-to column.
- A control may only justify an **Impact** drop if its Reduces column says Impact (or Both).
- Controls marked "Baseline" are assumed pre-existing; citing them does NOT justify any rating change (RA-08). They may be listed for completeness only, clearly labelled "(baseline, no rating credit)".
- If no catalog entry fits, write the mitigation and tag it [NON-STANDARD] so the reviewer scrutinises it.
- Product names: replace `[product per CMDB]` with the organisation's approved product for that control class. Do not name products not deployed in the environment.

## Endpoint (EP)

| ID | Control | Product | Reduces | Applicable to | Standard wording |
|---|---|---|---|---|---|
| EP-01 | Anti-malware, signature + behavioural | [product per CMDB] | Likelihood | Malware-delivery vectors (phishing payloads, drive-by, malicious upload). NOT applicable to logic flaws, SSRF, injection, privilege escalation via legitimate binaries | "(In place) [product] anti-malware on all hosts in [zone], signatures auto-updated, detections routed to SOC" |
| EP-02 | EDR with auto-containment | [product per CMDB] | Both | Post-exploitation activity: privilege escalation, credential theft, lateral movement. Likelihood credit only where behavioural detection covers the exploit technique; Impact credit via auto-isolation limiting spread | "(In place) [product] EDR with auto-isolate policy on [scope]; [technique]-class behavioural detection enabled" |
| EP-03 | Application allowlisting | [product per CMDB] | Likelihood | Arbitrary-binary execution vectors. NOT applicable to in-memory or living-off-the-land techniques unless policy covers them | "(In place) application allowlisting enforced on [scope]; unauthorised binaries blocked" |
| HD-01 | Host hardening per baseline (account/service/SUID reduction) | - | Likelihood | Privilege escalation, local attack surface | "(In place / Planned [date]) hardening review completed on [scope]: [specific items removed/disabled]" |

## Network (NW)

| ID | Control | Product | Reduces | Applicable to | Standard wording |
|---|---|---|---|---|---|
| NW-01 | Egress ACL / allowlist | - | Impact | SSRF, C2 callback, data exfiltration paths | "(In place) egress ACL on [host/zone] restricting outbound to [N] allowlisted endpoints: [list]" |
| NW-02 | Network segmentation / micro-segmentation | - | Impact | Lateral movement, blast-radius containment | "(In place) [zone] segmented; access to [data tier] restricted to [service ports]; no interactive protocols" |
| NW-03 | Ingress restriction (source IP allowlist / VPN-only) | - | Likelihood | Any network-reachable vulnerability; converts internet-facing exposure to restricted exposure | "(In place / Planned [date]) [endpoint] restricted to [source set] via [mechanism]" |
| NW-04 | IPS virtual patch / signature block | [product per CMDB] | Likelihood | Network-exploitable CVEs with released signatures; cite the signature ID | "(In place, [date]) IPS signature [ID] blocking [CVE] exploitation attempts at [boundary]" |

## Web application (WA)

| ID | Control | Product | Reduces | Applicable to | Standard wording |
|---|---|---|---|---|---|
| WA-01 | WAF virtual patch / rule | [product per CMDB] | Likelihood | Web-app CVEs and weakness classes with expressible signatures (SQLi, SSRF payload patterns, path traversal). Note documented bypass risk - rarely justifies more than a one-level likelihood drop | "(In place, [date]) WAF rule [ID/class] blocking [pattern] to [endpoint]" |
| WA-02 | Authentication gate added in front of vulnerable function | - | Likelihood | Pre-auth vulnerabilities; raises attacker prerequisites | "(In place / Planned [date]) [function] now requires [auth level]; pre-auth reachability removed" |

## Application/configuration (AP)

| ID | Control | Product | Reduces | Applicable to | Standard wording |
|---|---|---|---|---|---|
| AP-01 | Feature flag / vulnerable function disabled | - | Likelihood | Any vulnerability in a disable-able code path; strongest likelihood reducer - can support Rare if path is unreachable | "(In place / Planned [date]) [feature] disabled in [environment]; vulnerable code path unreachable" |
| AP-02 | Functionality degraded to safe mode | - | Likelihood | Partial disablement (e.g., preview off, download-only) | "(Planned [date]) [function] degraded to [safe mode] pending patch" |
| AP-03 | Least-privilege service account scoping | - | Impact | Limits what a compromised component can access | "(In place / Planned [date]) [service account] scoped to [minimum permissions]; [removed grants]" |
| AP-04 | Data minimisation / purge in reachable store | - | Impact | Reduces the data exposed in worst case | "(In place / Planned [date]) [data set] purged/tokenised in [store]; residual data: [description]" |

## Monitoring and response (MN)

| ID | Control | Product | Reduces | Applicable to | Standard wording |
|---|---|---|---|---|---|
| MN-01 | Targeted SOC alert with triage SLA | [product per CMDB] | Impact (containment speed only) | Supporting control. NEVER justifies a likelihood drop (RA-09); supports at most a one-level impact drop when paired with a containment action and SLA | "(In place) alert on [specific trigger] routed to SOC, [N]-min triage SLA, containment action: [action]" |
| MN-02 | Enhanced logging on vulnerable component | - | Neither (evidentiary) | Forensics readiness. No rating credit; list as supporting only | "(In place) [component] logging enabled at [level], retained [period]" |

## Baseline controls (no rating credit - RA-08)

Standard environment controls assumed present: perimeter firewall, OS anti-malware at default policy, patch management process, backup regime, password policy. Citing these as mitigations for a specific finding is the most common reviewer rejection. List only if directly and newly relevant, labelled "(baseline, no rating credit)".

## Extending this catalog

Add rows per organisation. Keep under ~150 rows in one file; beyond that, split by domain (mitigations-endpoint.md, mitigations-network.md, ...) and add an index in SKILL.md mapping vulnerability class -> file.
