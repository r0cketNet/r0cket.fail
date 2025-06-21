---
title: "Coordinated DDoS Attack Targeting TOR Exit Nodes"
date: 2025-06-20T17:00:00Z
lastmod: 2025-06-21T08:00:00Z
status: "resolved"
severity: "major"
impact: ["DDoS", "TOR Exit Nodes", "Packet Loss", "AWS EC2", "Hetzner", "BGP MED"]
duration: "7h 45m"
affected_services: ["TOR Exit Nodes", "Transit Services", "Customer Traffic"]
incident_id: "INC-2025-002"
tags: ["ddos", "tor", "exit-nodes", "aws", "ec2", "hetzner", "packet-loss", "bgp", "med", "coordinated-attack"]
summary: "Coordinated DDoS attack targeting TOR exit nodes from AWS/Hetzner infrastructure causing elevated packet loss"
draft: false
---

## Summary

On June 20, 2025, AS214503 experienced a coordinated distributed denial-of-service (DDoS) attack primarily targeting r0cket's TOR exit nodes. The attack originated primarily from AWS EC2 instances with secondary attack vectors from Hetzner infrastructure. The DDoS attack traffic from AWS peaked at 15.24 Gbit/s (95th percentile) and caused elevated packet loss up to 20% due to a misconfigured BGP Multi-Exit Discriminator (MED) that prevented optimal traffic engineering during the incident.

## Impact

- **Primary Target**: r0cket TOR exit nodes
- **Attack Vectors**: 
  - Primary: AWS EC2 instances (15.24 Gbit/s peak, several factors above baseline)
  - Secondary: Hetzner infrastructure (2x baseline traffic)
- **DDoS Volume**: 15.24 Gbit/s (95th percentile, AWS traffic only)
- **Packet Loss**: Up to 20% during peak attack periods
- **TOR Network Impact**: Degraded TOR exit capacity, potential traffic redirection
- **Customer Impact**: Degraded service performance and intermittent connectivity issues
- **Geographic Scope**: Global impact across all transit services

## Timeline (All times UTC)

**17:00** - Hetzner traffic volume doubles baseline levels, NOC alerts triggered  
**18:30** - AWS EC2 traffic escalates to several factors above baseline, targeting TOR exit nodes  
**20:17** - Multiple TOR operators report coordinated attacks on their exit infrastructure  
**21:30** - AWS attack traffic reaches peak volume of 15.24 Gbit/s (95th percentile)  
**22:00** - BGP MED misconfiguration identified and corrected  
**23:00** - Packet loss normalized to baseline levels following BGP optimization  
**00:45** - Attack traffic ceases, all network metrics return to normal operation  
**08:00** - Post-incident analysis completed 

## Root Cause

**Primary Cause**: Coordinated DDoS attack targeting r0cket's TOR exit nodes, originating from compromised or malicious infrastructure across multiple cloud providers:
- **Primary Vector**: AWS EC2 instances (several factors above baseline traffic)
- **Secondary Vector**: Hetzner infrastructure (2x baseline traffic)

**Attack Pattern**: Multiple TOR operators reported simultaneous attacks on their exit nodes, suggesting a coordinated effort to overload TOR exit capacity. This appears to be an attempt to force TOR traffic to exit through specific nodes by making other exit nodes unavailable.

**Amplifying Factor**: Misconfigured BGP Multi-Exit Discriminator (MED) values prevented optimal traffic engineering during the attack. The incorrect MED configuration forced traffic through congested paths instead of distributing load across available upstream providers, significantly increasing packet loss beyond what the attack volume alone would have caused.

## Resolution

**Immediate Actions**:
- Activated DDoS mitigation systems and traffic filtering
- Corrected BGP MED configuration to enable proper traffic distribution
- Implemented temporary rate limiting on affected interfaces
- Attempted coordination with AWS abuse team (no response received)

**Technical Changes**:
- BGP MED values adjusted from incorrectly uniform 100 to proper tiered values (50/100/150)
- Enhanced DDoS detection thresholds for AWS-sourced traffic
- Improved traffic engineering policies for asymmetric attack scenarios

## Prevention Measures

- **BGP Configuration Review**: Comprehensive audit of all MED configurations completed
- **Monitoring Enhancement**: Added specific alerting for MED-related traffic engineering issues
- **DDoS Response**: Updated runbooks to include BGP traffic engineering verification
- **TOR Network Coordination**: Established communication channels with other TOR operators for coordinated defense
- **Multi-Vector Detection**: Enhanced monitoring to detect coordinated attacks across multiple cloud providers
- **AWS/Hetzner Coordination**: Established direct escalation paths with cloud provider abuse teams for future incidents
- **Capacity Planning**: Evaluated additional DDoS mitigation capacity requirements

## Lessons Learned

1. BGP traffic engineering configuration must be regularly validated, especially MED values
2. DDoS mitigation effectiveness can be significantly impacted by routing configuration
3. Multi-vendor DDoS protection requires coordination with upstream traffic engineering
4. Cloud provider abuse reporting channels may be unavailable during incidents, requiring alternative mitigation strategies
5. Coordinated attacks against TOR infrastructure represent a significant threat to network anonymity
6. TOR exit node operators should coordinate defense strategies against systematic overload attacks
7. Multi-vector attacks (AWS + Hetzner) require comprehensive monitoring across all major cloud providers

## TOR Network Security Implications

This incident appears to be part of a broader coordinated attack targeting multiple TOR exit operators simultaneously. The attack pattern suggests an attempt to selectively overload TOR exit nodes, potentially forcing traffic through specific exit points controlled by the attacker. This represents a significant threat to the TOR network's decentralized design and user anonymity.

---

*Incident resolved. All systems operating normally with improved BGP configuration and enhanced DDoS protection.*
