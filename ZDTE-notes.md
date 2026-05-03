# ZDTE Notes

## Table of Contents
1. ZPA vs ZIA Rule Processing
2. Zscaler Architecture Concepts
3. API Authentication
4. AI / ML Techniques
5. Zero Trust Automation
6. Branch Connector
7. Service Edges (SE)
8. Nanolog Data Reduction
9. Tenant Restrictions
10. IPS and Sandbox
11. Zscaler API Use Cases
12. Data Security Services
13. Data Center Restrictions
14. ZID Using OpenID Connect
15. Authentication Notes
16. API Advantages
17. DNS Blocking Recommendations
18. Browser Isolation
19. AppTotal
20. Endpoint DLP Report
21. Risk 360
22. Data Fabric
23. Deception Technology
24. ZDX Probes
25. Diagnostics
26. Hi-Fi Traffic Forwarding
27. Process Inventory
28. Traffic Forwarding
29. ZCC Health Check Behavior
30. Workloads and Access
31. UEBA Tracking
32. VDI / PRA / Thick Clients
33. DLP Technologies
34. EASM
35. MITRE
36. Data Fabric Functions
37. Breach Predictor
38. ZDX API Flow
39. Workflow Automation
40. Shared Configuration
41. Admin Notes
42. Provisioning Key Deletion
43. Performance Bottlenecks

---

# 1. ZPA vs ZIA Rule Processing
- ZPA does not use top-down rule order; uses bitmap structure.
- Most specific application segment wins.
- ZIA uses top-down rule order.

---

# 2. Zscaler Architecture Concepts
- Public Service Edge: N+1 redundancy.
- Central Authority and Logging: N+2 redundancy.
- SSMA: Single-scan multi-action.
- ECMP: Equal-cost multi-path.
- JTI: JSON Token Identifier.
- NTLM: New Technology LAN Manager.
- IWA: Integrated Windows Authentication.
- Failover to PoP: Traffic routing to alternate SE.
- CMDB: Configuration Management Database (ServiceNow).
- CSAM: Cybersecurity Asset Management.
- CDR: Content Disarm and Reconstruction.
  - Votiro sanitizes files via Browser Isolation.
- Cloud App Control is ZIA-only.

---

# 3. API Authentication
- User generates API token.
- System returns JSESSIONID.
- JSESSIONID used for subsequent API calls.
- Java-based workflow.

---

# 4. AI / ML Techniques
- Entropy analysis.
- Subdomain enumeration.
- Request volume analysis.
- Domain reputation scoring.

---

# 5. Zero Trust Automation
- API token lifetime: 1 minute to 24 hours.
- Secret key duration: 30 to 365 days (max 2 secrets).
- Up to 16 APIs per resource.
- Update policy: PUT /policysetID/rule/ruleID.
- Update app segment: PUT /application/applicationID.

### Maximum Segment Size (MSS)
- 1 Gbps per GRE tunnel if not behind NAT.
- 250 Mbps per tunnel if behind NAT.
- Ensure correct MTU.

### Operational Notes
- Test auto failover quarterly.
- PACs not used in Z-Tunnel 2.0.
- App Profile PACs = Tunnel 1.0 direct SE.
- Forwarding Profile PACs = local proxy.
- macOS does not support CrowdStrike sensor-state posture check.

---

# 6. Branch Connector
- Used where ZCC cannot be installed.
- Supports IoT devices.
- Avoids hairpinning.
- Replaces site-to-site VPN / hub-and-spoke.
- Does not support bare metal.
- Best practice: App Connectors at both DCs/AWS + SD-WAN + Branch Connectors.

---

# 7. Service Edges (SE)

### Performance
- 500 Mbps peak (no SSL).
- 600 Mbps with SSL inspection.

### Types
- Public SE: Web + Firewall modules.
- Private SE: SE instances + customer load balancers.

### Sub-Types
#### Physical (ZT400, ZT600, ZT800)
- Web, Firewall, Management modules.

#### Virtual
- VMware ESXi/vSphere.
- Web, Firewall, Management, Log Streaming modules.

### Arms (Private SE only)
- Single-arm: out-of-path, supports physical/virtual, DSR bypasses LB.
- Dual-arm: in-path, virtual only.

### Dashboard Retention
- ZIA: 7 days.
- ZPA: 14 days.

### SE Models
- SE3: 1G NIC, 1.3 Gbps.
- SE5: 10G NIC, 2.5 Gbps.
- Up to 16 VMs per cluster.

---

# 8. Nanolog Data Reduction
- NSS: ZIA.
- LSS: ZPA.
- 1 hour log buffering.
- Logs every DNS transaction for 180-184 days.
- Compression ratio: 50:1.

### Syslog Check
```
tail -f /var/log/syslog
```

---

# 9. Tenant Restrictions
### Version 1
- Tenant directory ID + profile name.
- 3rd parties restricted to their own tenants.

### Version 2
- Define tenant + specific apps 3rd parties can access.

---

# 10. IPS and Sandbox
- Custom IPS signatures using Snort syntax.
- Sandbox performs:
  1. Static analysis
  2. Dynamic analysis
  3. Behavioral/memory/ML analysis
- Decoys invisible to legitimate users.
- MirageMaker provides:
  - Static app decoys
  - CVE-based decoys
  - SCADA/ICS decoys
  - Custom decoys

### AD Attack Techniques
- DCSync: impersonate DC to extract password hashes.
- DCShadow: modify AD replication.

---

# 11. Zscaler API Use Cases
- Custom denylist.
- Ingest external threat intel.
- User group updates and deletion.

---

# 12. Data Security Services

### DSPM Classification Tools
- AI classifiers.
- Dictionaries.
- DLP engines.
- Functions: Discover, Map & Track, Remediate.

### AppProtection Modes
- Redirect.
- Active Prevention.
- Silent Monitoring.

### DLP Email Notification Parameters
- Metadata.
- Snippet of matching content.
- DLP/CASB artifacts.

### Application Dashboard Sorting
- ZDX Score Trend.
- Most Impacted Region.
- Most Impacted Location.
- Most Impacted Department.

### EASM Lookalike Categories
- Preventative Lookalike.
- Registered Lookalike.
- Verified Phishing.

---

# 13. Data Center Restrictions
- Restrict users to specific DCs.
- PAC returns ${gateway.subcloud.zscaler.net}.
- Combine with ${COUNTRY} or ${SRCIP}.
- Public cloud = gateway.[cloudname].net.
- If DNS server IP is wrong region, use ${GATEWAY}.

---

# 14. ZID Using OpenID Connect
1. ZCC sends request.
2. ZID returns clientID.
3. ZCC -> IdP.
4. User authenticates.
5. Auth server sends authorization code.
6. ZCC sends code to ZID.
7. ZID exchanges for ID/Access tokens.
8. ZID calls UserInfo endpoint.
9. ZID enrolls user into ZIA/ZPA/ZDX.

- Timeout: 5 to 600 minutes.

---

# 15. Authentication Notes
- FIDO2 allowed as primary authenticator.
- NAT Control uses user attributes, location, time, device type, destination, port.
- Threat Protection bypasses Malware Protection.

### Supported IdPs
- PingOne
- Okta
- MS ADFS
- OneLogin
- Auth0 (SAML/OIDC)

### Not Supported
- OAuth 2.0 for authentication.

---

# 16. API Advantages
- CRUD operations.
- Firewall integrations.
- Automation.
- Access control updates.
- Uses external OAuth 2.0 server.
- OneAPI uses JSON.
- APIs = control plane; enforcement plane receives policies.

---

# 17. DNS Blocking Recommendations
- Advanced Security.
- Commonly Allowed.
- Miscellaneous.

---

# 18. Browser Isolation
- Turbo mode: rendering command streaming.
- Smart Host inserts DLP action headers.
- Reduces VDI reliance with PRA.

---

# 19. AppTotal
- User confirmation via Slack/email.
- Revoke supported by Google.
- Ban supported by Microsoft.

---

# 20. Endpoint DLP Report
- Tabs: Overview, Incidents.
- Filters: 7 days, 15 days, 1 month.
- Filter by action, severity, content type.

---

# 21. Risk 360
- Annotated Risk Score trend (90 days).
- Shows top 10 events impacting score.

### Alert Rule Levels
- Organization.
- Category.
- Factor Group.
- Factor.

### Financial Risk Metrics
- Inherent Risk Score.
- Residual Risk Score.
- Last 30-day average.
- Industry peer score.

---

# 22. Data Fabric
- Unified Vulnerability Management.
- Risk360.
- Deception.
- ITDR.
- ID Risk Summary: AD, Entra ID, Credential Exposure.
- Breach Predictor uses Sankey charts.

---

# 23. Deception Technology
### Landmine
- Endpoint agent deploying fake creds/files/processes/lures.

### Decoy Connector
- VM creating decoy apps in VLAN.

### AD Change Detection
- Detects changes to users, groups, computers.
- Requires Entra ID Custom Change Detection Policy.

---

# 24. ZDX Probes

### Web Probes
- Page fetch, DNS, server response, availability.
- Main page only.
- 8 parallel requests.
- 1-minute diagnostic interval.
- No caching.
- TTFB, TTLB, total fetch time.

### Cloud Path Probe
- Maps hops, latency, packet loss.
- Follow Web Probe recommended.
- Required for Z-Tunnel 1.0.
- Only probe supporting ICMP/UDP.

### Autosense Cloud Path Probe
- Requires WFP driver.

### Reverse Cloud Path Probe
- Reverse path visibility.

### HTTP Status Codes
- Codes not in list reduce ZDX score.

---

# 25. Diagnostics
- Max duration: 60 minutes.
- Intervals: 5, 15, 30, 60 minutes.

### Deep Trace
- Use for traffic/policy/access issues.
- Do not use for auth/install/licensing issues.

---

# 26. Hi-Fi Traffic Forwarding
- TTL: 20-300 (default 300).
- Time intervals: 1000-3000 ms.
- Timeout must match interval.

---

# 27. Process Inventory
- Updated every 1 minute.
- Top processes every 5 minutes.

### ZDX Score
- Good: 66-100.
- Okay: 34-65.
- Poor: 0-33.
- Calculated daily.

### Data Explorer
- 14-day retention.
- Alerts when 25% of users report poor performance.

### Risk Algorithm
- 75 core attributes.
- 200 risk attributes.
- 100,000+ apps.

---

# 28. Traffic Forwarding
- For 1 Gbps+: 2 GRE/IPSec tunnels, NAT disabled.
- Local proxy: check $(ZAPP_LOCAL_PROXY).
- Tunnel 2.0: check any.broker.prod.zpath.net.
- GeoIP resolution within target subcloud.

---

# 29. ZCC Health Check Behavior
- Probes to 100.64.0.6.
- If no response: Endpoint FW/AV Error.

### Troubleshooting
```
Find-NetRoute -RemoteIPAddress 100.64.0.6
```
- Check InterfaceAlias.

### Windows Firewall
```
netsh advfirewall firewall show rule name="Zscaler App Rule"
```

### Routing Fix
```
route -p add destination MASK subnet gateway metric
```

---

# 30. Workloads and Access
- Cloud Connector extends ZIA/ZPA to workloads.
- SD-WAN supports branches, DCs, clouds.
- Browser Access flow:
  Browser -> ZTE -> IdP -> ZTE -> App Connector.

### 409 Error
- Resource state issue.

---

# 31. UEBA Tracking
- Login attempts.
- Session duration.
- File access/transfers.
- App usage.
- Data exfiltration.

---

# 32. VDI / PRA / Thick Clients
- VDIs may be needed for thick clients.
- BI reduces VDI footprint.
- PRA supports SSH/RDP.
- Persistent desktops do not need BI/PRA.

---

# 33. DLP Technologies
### OCR
- Not PII-based.
- Extracts text from images/scans.

### IDM
- Unstructured docs: W-2, tax docs, contracts, medical forms.

### EDM
- Structured data: employee lists, customer records, SSNs, accounts.

---

# 34. EASM
- Tracks domains, IPs, subdomains, SSL/TLS certs.

### Risk Management Relationships
- Risk Mgmt = EASM + UVM + Deception.
- Risk Mgmt + Data Protection = DSPM.
- ZTE + API Fabric = UVM + Risk360.

---

# 35. MITRE
- Identifies weak points, misconfigurations, vulnerabilities, lateral movement.

---

# 36. Data Fabric Functions
- Harmonize.
- Deduplicate.
- Correlate.
- Enrich.
- Enhanced by AI/ML.

---

# 37. Breach Predictor
- PreDR mapping to MITRE.

---

# 38. ZDX API Flow
```
API key + secret
-> OAuth token endpoint
-> OAuth 2.0 client credentials
-> Zscaler issues JWT
-> JWT used for ZDX API calls
```

---

# 39. Workflow Automation
- Helps admins resolve incidents.
- Tickets enriched with thresholds.

---

# 40. Shared Configuration
- Location and location groups.
- Add/edit/view/delete.

---

# 41. Admin Notes
- Uses AI/ML.
- Copilot requires admin permission + early availability.
- Remote Access: view-only or full.
- Software Inventory: enable in ZDX Admin.

### Tenant Control
- Managed in Zscaler Experience Center, not Zidentity.

### Logs
- Managed by Central Authority.
- Generated in Enforcement Plane.
- Managed in Control Plane.

---

# 42. Provisioning Key Deletion
- Cannot be recovered.
- App Connectors remain active.

---

# 43. Performance Bottlenecks
- CPU and throughput rarely bottlenecks.
- Number of connections is the real bottleneck.

