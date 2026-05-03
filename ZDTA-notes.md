These notes summarize my study and hands‑on learning for the Zscaler Digital Transformation Architect (ZDTA) certification. They cover architecture, identity, tunneling, policy logic, DLP, ZDX, and operational considerations across ZIA, ZPA, and ZDX.

This document is structured and — it’s meant to serve as a reusable reference for Zero Trust engineering work.

Table of Contents
1. Architecture Overview

2. ZPA Architecture

3. ZIA Architecture

4. Zero Trust Exchange (ZTX) Principles

5. Traffic Forwarding & Tunneling

6. Identity & Authentication

7. ZDX (Digital Experience Monitoring)

8. Data Protection & DLP

9. Private Application Access (ZPA)

10. Device Posture & Client Connector

11. TLS/SSL Inspection

12. Bandwidth, NAT & Firewall Behavior

13. Domain Intelligence & Threat Detection

14. Monitoring, Diagnostics & UCaaS

15. API & Automation

Architecture Overview
  Zscaler replaces traditional network‑centric security with a cloud‑native Zero Trust Exchange (ZTX) that connects users to applications — not networks.

  Key components:
  
    ZIA – Secure Internet Access
    ZPA – Private App Access
    ZDX – Digital Experience Monitoring
    ZCC – Client Connector
    App Connectors – Broker access to private apps
    Public Service Edges – Policy enforcement & inspection

# ZPA Architecture

```
User Device
   ↓
ZCC or Clientless Access
   ↓
Cloud Broker (Central Authority, Public Service Edge)
   ↓
App Connector (Tunnel Broker)
   ↓
Server Group
   ↓
Application Segment
   ↓
Private Application
```

  ZPA eliminates VPNs, inbound DMZ, and lateral movement by brokering user‑to‑app connections through identity and context.

# ZIA Architecture

```
User Device
   ↓
Traffic Forwarding (ZCC, PAC, GRE, IPSec)
   ↓
Cloud Forward Proxy
   ↓
Security Stack (FW, IPS, DLP, CASB, SSL Inspection)
   ↓
[Optional] Browser Isolation
   ↓
Internet Destination
```

ZIA replaces outbound DMZ, SWG, and MPLS with a cloud‑native proxy architecture.

Zero Trust Exchange (ZTX) Principles
  Zero attack surface
  Connect users to apps, not networks
  Proxy architecture, not passthrough
  Secure Access Service Edge (SASE)
  Multitenant cloud architecture

Traffic Forwarding & Tunneling
  Supported Tunnels

    HTTP CONNECT
    SSH Port Forwarding
    GRE (L3, scalable, no encryption)
    GRE + IPSec (ESP encryption)
    DTLS/TLS (L7, optimized for real‑time traffic)
    Microtunnels (ZPA)
    Z‑Tunnel 1.0 / 2.0
    Route‑based forwarding
    Local proxy mode
    Enforced PAC mode

Z‑Tunnel Notes
1.0: DTLS, HTTP only

2.0: TLS, broader traffic support

Proxy settings must be removed for packet‑filter mode

Identity & Authentication
Protocols
SAML – static, XML, best for device policies

OIDC – JSON, mobile/web apps

OAuth 2.0 – API access

SCIM – provisioning (40‑minute sync cycle)

Authentication Flow
User authenticates with IdP

IdP issues signed SAML assertion

Browser POSTs assertion to Zscaler

Zscaler validates and issues token/cookie

Traffic processed through ZTX

ZDX (Digital Experience Monitoring)
Capabilities
Endpoint performance

Network path visibility

ISP/gateway issues

ZPA path visibility

Teams/Zoom call quality

Automated RCA (Y‑Engine)

ZDX Score
Range: 1–100

Based on: page fetch time + availability

Probes: every 5 minutes

Data Protection & DLP
DLP Techniques
EDM – Exact Data Match

IDM – Index Document Matching

OCR – Text extraction from images

DSPM
Discover services

Map & track risk

Remediate risk

SSPM
Cloud misconfiguration

Compliance mapping

Third‑party app discovery

Private Application Access (ZPA)
Three Pillars
Reachability (App Connectors)

Application Details (Segments & Groups)

Access Policies

App Connector Notes
Lightweight VM

Destination sees connector IP, not user IP

UDP apps require ICMP enabled

Supported Access Types
RDP

SSH

Browser‑based apps

Device Posture & Client Connector
ZCC Security Checks
Certificate trust

Domain join status

Antivirus

Disk encryption

Third‑party integrations

Trusted Network Criteria
DNS server IPs

DNS search domains

FQDN/IP resolution

ANY vs ALL matching

Posture Check Frequency
Every 2–15 minutes

TLS/SSL Inspection
Deployment Phases
Pre‑work

Root CA enrollment

Initial rollout

Measure & report

Extended rollout

Important Notes
ZPA does not support SSL inspection

ZIA certificate rotates every 7 days (14‑day validity)

Must bypass certificate‑pinned apps

Block QUIC (UDP 443) and Apple Private Relay

Bandwidth, NAT & Firewall Behavior
Bandwidth Control
8 predefined classes

17 custom classes

Location‑based enforcement

NAT
Destination + port translation (L3–L4)

IPS
Identity, location, and service‑based enforcement

Firewall Defaults
Allow HTTP/HTTPS proxy

Keep “one‑click” rules at top

Handles UCaaS and M365 optimally

Domain Intelligence & Threat Detection
Domain Classifications
NRD – Newly Registered Domains

NOD – Newly Observed Domains

ATP Risk Factors
TLD reputation

User agent anomalies

Missing headers

Domain entropy

Zero‑pixel iFrames

Obfuscated JavaScript

Suspicious URL paths

ThreatLabz
Zscaler’s internal threat research team.

Monitoring, Diagnostics & UCaaS
Monitoring Types
Endpoint

Network

Application

UCaaS

Diagnostics
Max duration: 60 minutes

Reverse path probes available via connector

UCaaS Optimization
Teams/Zoom scoring

TCP window scaling (64 KB → 4 MB)

API & Automation
API Response Components
HTTP status codes

Response headers

Response body

AI Insights
Can classify up to 8,000 files
