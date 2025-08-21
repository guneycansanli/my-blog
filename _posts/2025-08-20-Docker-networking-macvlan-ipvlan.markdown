---
title: "Docker MacVLAN vs IPVLAN - A Practical Deep Dive into Advanced Networking"
layout: post
date: 2025-08-20 11:00
image: ../assets/images/dockermac/main.jpg
headerImage: true
tag:
    - linux
    - docker
    - networking
category: blog
author: guneycansanli
description: lnav - A Smarter Way to View and Analyze Logs on Linux and Unix
---

# Introduction

Introduction

Quick recap of how Docker networking works by default (bridge, NAT, port binding).
Problem statement: Containers can’t be directly reached with their own IPs.
Solution: Advanced drivers — MacVLAN & IPVLAN.


## 1. Why Use MacVLAN or IPVLAN?

Containers act like first-class citizens on the network.

Benefits:
- Isolation & Security – separate network identities.
- Simplified Communication – direct access via IPs (great for legacy systems).
- Advanced Scenarios – firewall rules, VPNs, monitoring.

---

## 2. MacVLAN Explained

- Works at Layer 2 (L2).
- Each container gets its own unique MAC + IP.
- Analogy: Multiple virtual machines plugged into the same switch port.

How It Works:

- Host interface acts as a parent.
- Each container gets a virtual NIC with a new MAC.

Setup Example:

```bash
#Network can be different based on your network scope
docker network create -d macvlan \
  --subnet=192.168.1.0/24 \
  --gateway=192.168.1.1 \
  -o parent=enp1s0 \
  macvlan_net
```

![dockermac][1]

Run container:

```bash
#Ip can be different based on your network scope
docker run -itd --network macvlan_net --ip 192.168.1.195 nginx
```

![dockermac][2]

![dockermac][3]

Appears on LAN as its own device.

- Requires promiscuous mode on NIC.
- Breaks with switch port-security (multiple MACs on one port).


Testing nginx webserver connection from any other device in same network.
Note: **I did not expose any port to host machine and I still can able to reach nginx**

![dockermac][4]

---

## 3. IPVLAN Explained

Unlike MacVLAN, IPVLAN containers share the host’s MAC. Only IPs differ.
👉 Looks cleaner to the switch/router, no multiple MAC addresses to worry about.

IPVLAN Modes:

###  1. L2 Mode

- Works like MacVLAN but without unique MACs.
- All containers → same MAC as host.

Setup:

```bash
#Network can be different based on your network scope
docker network create -d ipvlan \
  --subnet=192.168.1.0/24 \
  --gateway=192.168.1.1 \
  -o parent=enp1s0 \
  ipvlan_l2
```

Containers reachable on LAN directly.

⚠️ Security tools may flag this as MAC spoofing since many IPs map to one MAC.


### 2. L3 Mode

- Host acts as a router for containers.
- Containers live in their own subnet.
- Other hosts need static routes pointing to the Docker host.

Example:

```bash
#Network can be different based on your network scope
docker network create -d ipvlan \
  --subnet=10.10.0.0/24 \
  -o parent=enp1s0 \
  -o ipvlan_mode=l3 \
  ipvlan_l3
```

Requires adding a static route on your router/gateway.
Great for segmentation & isolation.

---

## 4. MacVLAN vs IPVLAN: Quick Comparison

<table style="border-collapse: collapse; width: 100%; font-family: Arial, sans-serif; text-align: center;">
  <thead>
    <tr style="background-color: #f2f2f2;">
      <th style="border: 1px solid #ccc; padding: 8px; text-align:left;">Feature</th>
      <th style="border: 1px solid #ccc; padding: 8px;">MacVLAN</th>
      <th style="border: 1px solid #ccc; padding: 8px;">IPVLAN L2</th>
      <th style="border: 1px solid #ccc; padding: 8px;">IPVLAN L3</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="border: 1px solid #ccc; padding: 8px; text-align:left;">MAC per container</td>
      <td style="border: 1px solid #ccc; padding: 8px;">Unique MACs</td>
      <td style="border: 1px solid #ccc; padding: 8px;">Shared with host</td>
      <td style="border: 1px solid #ccc; padding: 8px;">Shared with host</td>
    </tr>
    <tr style="background-color: #f9f9f9;">
      <td style="border: 1px solid #ccc; padding: 8px; text-align:left;">Works without promiscuous mode</td>
      <td style="border: 1px solid #ccc; padding: 8px;">❌ No</td>
      <td style="border: 1px solid #ccc; padding: 8px;">✅ Yes</td>
      <td style="border: 1px solid #ccc; padding: 8px;">✅ Yes</td>
    </tr>
    <tr>
      <td style="border: 1px solid #ccc; padding: 8px; text-align:left;">Requires static routes</td>
      <td style="border: 1px solid #ccc; padding: 8px;">❌ No</td>
      <td style="border: 1px solid #ccc; padding: 8px;">❌ No</td>
      <td style="border: 1px solid #ccc; padding: 8px;">✅ Yes</td>
    </tr>
    <tr style="background-color: #f9f9f9;">
      <td style="border: 1px solid #ccc; padding: 8px; text-align:left;">LAN reachability (same subnet)</td>
      <td style="border: 1px solid #ccc; padding: 8px;">✅ Yes</td>
      <td style="border: 1px solid #ccc; padding: 8px;">✅ Yes</td>
      <td style="border: 1px solid #ccc; padding: 8px;">⚠️ Needs routes</td>
    </tr>
    <tr>
      <td style="border: 1px solid #ccc; padding: 8px; text-align:left;">Isolation / segmentation</td>
      <td style="border: 1px solid #ccc; padding: 8px;">➖ Moderate</td>
      <td style="border: 1px solid #ccc; padding: 8px;">➖ Moderate</td>
      <td style="border: 1px solid #ccc; padding: 8px;">✅ Strong (routed subnets)</td>
    </tr>
    <tr style="background-color: #f9f9f9;">
      <td style="border: 1px solid #ccc; padding: 8px; text-align:left;">Switch port-security friendly</td>
      <td style="border: 1px solid #ccc; padding: 8px;">❌ Can break (many MACs)</td>
      <td style="border: 1px solid #ccc; padding: 8px;">✅ Friendlier (one MAC)</td>
      <td style="border: 1px solid #ccc; padding: 8px;">✅ Friendlier (one MAC)</td>
    </tr>
    <tr>
      <td style="border: 1px solid #ccc; padding: 8px; text-align:left;">IDS/IPS false-positive risk</td>
      <td style="border: 1px solid #ccc; padding: 8px;">➖ Normal</td>
      <td style="border: 1px solid #ccc; padding: 8px;">⚠️ Many IPs → one MAC</td>
      <td style="border: 1px solid #ccc; padding: 8px;">⚠️ Many IPs → one MAC</td>
    </tr>
    <tr style="background-color: #f9f9f9;">
      <td style="border: 1px solid #ccc; padding: 8px; text-align:left;">Typical use case</td>
      <td style="border: 1px solid #ccc; padding: 8px;">Legacy/LAN realism</td>
      <td style="border: 1px solid #ccc; padding: 8px;">Simpler LAN access</td>
      <td style="border: 1px solid #ccc; padding: 8px;">Routed/isolated networks</td>
    </tr>
  </tbody>
</table>


## 5. Choosing the Right One

- MacVLAN → Use when you want containers to behave like separate physical machines.
- IPVLAN L2 → Use in environments where multiple MACs would break things.
- IPVLAN L3 → Use for isolation, custom routing, or multi-subnet designs.


# Conclusion

MacVLAN and IPVLAN unlock powerful networking capabilities in Docker beyond the default bridge. They let containers integrate directly into existing networks or create isolated routing domains.

Like all advanced networking, the right choice depends on your environment:

- Want realism? → MacVLAN.
- Want simplicity? → IPVLAN L2.
- Want flexibility and control? → IPVLAN L3.

---

Thanks for reading!

—

**Guneycan Sanli**


[1]: ../assets/images/dockermac/dockermac-1.jpg
[2]: ../assets/images/dockermac/dockermac-2.jpg
[3]: ../assets/images/dockermac/dockermac-3.jpg
[4]: ../assets/images/dockermac/dockermac-4.jpg




