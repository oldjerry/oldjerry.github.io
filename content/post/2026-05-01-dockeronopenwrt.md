---
title: "When Docker Breaks IPv6 on OpenWrt: A Real-World Troubleshooting Case"
description: "A practical troubleshooting case where enabling Docker IPv6 on OpenWrt caused LAN-wide IPv6 failure."
date: 2026-04-12
---

## Overview

This article describes a real-world troubleshooting case involving IPv6 connectivity issues in a home lab environment.

The issue appeared after enabling IPv6 support in Docker running on an OpenWrt router. While the router itself still had working IPv6 connectivity, all LAN clients lost IPv6 access.

This type of problem is difficult to diagnose because the symptoms often point to common causes such as firewall or routing issues, while the actual root cause lies deeper in system-level interactions.

---

## The Problem

After enabling IPv6 in Docker:

- The router maintained IPv6 connectivity  
- LAN clients could no longer access IPv6 resources  
- Existing IPv4 connectivity remained unaffected  
- Disabling Docker restored normal IPv6 behavior  

From a user perspective, it appeared that:

> “IPv6 suddenly stopped working on all devices.”

---

## Initial Assumptions

The first troubleshooting steps focused on typical network issues:

- Firewall rules (forwarding, blocking, NAT)  
- IPv6 routing configuration  
- Prefix delegation and address assignment  
- DNS resolution  

These are the most common causes of IPv6 connectivity problems.

However, none of these checks revealed a clear issue.

---

## Investigation Process

### 1. Verifying Router Connectivity

The router itself was able to:

- Ping external IPv6 addresses  
- Resolve IPv6 DNS records  

This confirmed that:

> The upstream IPv6 connection was working correctly.

---

### 2. Checking LAN Clients

On LAN devices:

- IPv6 addresses were present  
- Default gateway appeared correct  
- However, outbound IPv6 traffic failed  

This suggested:

> The issue was not address assignment, but traffic handling.

---

### 3. Reviewing Firewall and Forwarding

Firewall rules were reviewed:

- No obvious blocking rules  
- Forwarding between LAN and WAN appeared correct  

At this point, the issue still resembled a firewall problem, but without clear evidence.

---

### 4. Identifying the Trigger

Further testing showed:

- The issue only occurred when Docker IPv6 support was enabled  
- Disabling Docker or its IPv6 configuration immediately restored connectivity  

This narrowed the scope significantly:

> The problem was linked to Docker’s interaction with the network stack.

---

## Root Cause (Practical Understanding)

Enabling IPv6 in Docker introduces several changes:

- Creation of virtual bridge networks  
- Modification of iptables / nftables rules  
- Changes to forwarding behavior  
- Interaction with IPv6 routing and neighbor discovery  

In standard Linux environments, this usually works as expected.

However, OpenWrt has a different network architecture:

- Firewall is managed via fw4 (nftables abstraction)  
- IPv6 relies heavily on Router Advertisements (RA)  
- Services like odhcpd manage address distribution  

When Docker modifies networking behavior:

> It can interfere with how IPv6 traffic and routing information are handled on the router.

This leads to a situation where:

- The router itself still works  
- But LAN clients cannot properly route IPv6 traffic  

---

## Why This Issue Is Hard to Diagnose

This type of issue has several characteristics that make troubleshooting difficult:

### 1. Misleading Symptoms

It looks like:

- A firewall problem  
- A routing issue  
- Or an ISP-related IPv6 problem  

---

### 2. Partial Functionality

- Router works  
- Clients fail  

This creates confusion about where the issue actually exists.

---

### 3. Limited Visibility

- No obvious error messages  
- Logs may not clearly indicate the conflict  
- Behavior depends on internal interactions between components  

---

### 4. Non-Standard Environment

Running Docker on OpenWrt is not a typical use case:

- OpenWrt is optimized as a router and firewall  
- Docker assumes a more general-purpose Linux environment  

This mismatch increases the likelihood of unexpected behavior.

---

## Resolution

The most practical solution was:

> Disable IPv6 support in Docker on the OpenWrt router.

After doing so:

- LAN IPv6 connectivity returned to normal  
- No further issues were observed  

---

## Alternative Approaches

If IPv6 is required for containerized applications:

- Run Docker on a separate Linux host (e.g., mini PC, NAS)  
- Keep OpenWrt focused on routing and network management  
- Avoid overlapping control of the network stack  

---

## Lessons Learned

### 1. Not All Linux Environments Behave the Same

Even though OpenWrt is Linux-based:

> Its networking model is significantly different from standard distributions.

---

### 2. Intermittent or Partial Failures Can Indicate Deeper Conflicts

When:

- Some components work  
- Others fail without clear reason  

It often points to:

> System-level interaction issues rather than simple misconfiguration.

---

### 3. Avoid Overloading the Router

Routers are best suited for:

- Routing  
- Firewall  
- VPN  

Running complex application stacks (like Docker with advanced networking) can introduce unnecessary risk.

---

### 4. Ownership of Network Control Must Be Clear

When multiple systems try to manage networking:

- Docker modifies rules  
- OpenWrt manages firewall and routing  

Conflicts are likely.

---

## Final Thoughts

This case highlights an important aspect of troubleshooting:

> The root cause is not always where the symptoms appear.

In environments where multiple systems interact, understanding how components influence each other is critical.

Recognizing these patterns helps move from trial-and-error troubleshooting to more structured problem analysis.

---

## References

- https://github.com/openwrt/packages/issues/24492#issuecomment-2211509498  
- https://www.reddit.com/r/openwrt/comments/1mboedr/ipv6_router_works_fine_but_clients_cant_reach/