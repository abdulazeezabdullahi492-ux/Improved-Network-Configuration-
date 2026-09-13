# Isolated Multi-Segment LAN with Wireless Access
### A Network Design and Implementation Report

### Author: Abdulazeez Abdullahi
### Platform: Cisco Packet Tracer


---

## 1. Executive Summary

This project presents the design, implementation, and verification of a
network consisting of four completely independent switch-based segments,
each built around its own access switch. No segment is connected to any
other — every switch operates as its own standalone LAN with no physical
or logical path to the rest of the network. One segment (D) additionally
provides wireless access to mobile clients via an access point attached to
its switch. The network contains no routing and no inter-switch uplinks
between segments; isolation is total and enforced simply by the absence of
any cable between switches. This report documents the objectives,
architecture, addressing plan, design rationale, configuration, and test
results for the completed network.

---

## 2. Objectives

The network was designed to meet the following objectives:

1. Establish four switch-based LAN segments, each anchored on its own access switch, with no connection to any other segment.
2. Ensure complete isolation between all four segments — no segment should be able to reach any other.
3. Provide wireless connectivity for mobile and portable devices within Segment D only, via an access point attached to its switch.
4. Support wired server infrastructure within the segments that require it.
5. Validate, through direct testing, that every segment is fully isolated from the others and that intended connectivity works correctly within each segment.

---

## 3. Network Architecture

### 3.1 Topology Diagram

<img width="1920" height="1080" alt="Screenshot (39)" src="https://github.com/user-attachments/assets/44e685b8-3c6d-4308-994d-78615e6f9a19" />


### 3.2 Device Inventory

| Device | Function | Model |
|---|---|---|
| Switch0 | Segment A access switch (standalone) | Cisco 2960-24TT |
| Switch1 | Segment B access switch (standalone) | Cisco 2960-24TT |
| Switch2 | Segment C access switch (standalone) | Cisco 2960-24TT |
| Switch3 | Segment D access switch (standalone, wireless-enabled) | Cisco 2960-24TT |
| Access Point0 | Wireless access for Segment D only | Access Point-PT |

### 3.3 Segment Composition

| Segment | Switch | End Devices | Isolation Status |
|---|---|---|---|
| A | Switch0 | PC0, PC1, PC2, PC3, Laptop0 | Fully isolated |
| B | Switch1 | PC4, PC5, PC6, PC7 | Fully isolated |
| C | Switch2 | PC8, PC9, PC10, PC11, Server0, Server1 | Fully isolated |
| D | Switch3 | PC12, PC13, Laptop2, Server2, PC15 (wireless), Smartphone0 (wireless) | Fully isolated (wireless clients join via Access Point0, internal to this segment only) |

All four segments are entirely standalone. There is no switch-to-switch
uplink anywhere in this topology — each switch is its own isolated
broadcast domain. Access Point0 extends Segment D to wireless clients but
does not connect Segment D to any other segment.

---

## 4. Addressing Plan

Each of the four segments was assigned its own subnet, keeping
addressing simple and segment membership self-evident from the IP address
alone.

| Device | IP Address | Subnet Mask | Segment |
|---|---|---|---|
| PC0 | 192.168.10.2 | 255.255.255.0 | A |
| PC1 | 192.168.10.3 | 255.255.255.0 | A |
| PC2 | 192.168.10.4 | 255.255.255.0 | A |
| PC3 | 192.168.10.5 | 255.255.255.0 | A |
| Laptop0 | 192.168.10.6 | 255.255.255.0 | A |
| PC4 | 192.168.11.2 | 255.255.255.0 | B |
| PC5 | 192.168.11.3 | 255.255.255.0 | B |
| PC6 | 192.168.11.4 | 255.255.255.0 | B |
| PC7 | 192.168.11.5 | 255.255.255.0 | B |
| PC8 | 192.168.12.2 | 255.255.255.0 | C |
| PC9 | 192.168.12.3 | 255.255.255.0 | C |
| PC10 | 192.168.12.4 | 255.255.255.0 | C |
| PC11 | 192.168.12.5 | 255.255.255.0 | C |
| Server0 | 192.168.12.6 | 255.255.255.0 | C |
| Server1 | 192.168.12.7 | 255.255.255.0 | C |
| PC12 | 192.168.13.2 | 255.255.255.0 | D |
| PC13 | 192.168.13.3 | 255.255.255.0 | D |
| Server2 | 192.168.13.4 | 255.255.255.0 | D |
| Laptop2 | 192.168.13.5 | 255.255.255.0 | D |

---

## 5. Design Rationale

**Total physical isolation.** VLANs and routing were deliberately not used
anywhere in this design. Isolation between all four segments is achieved
purely by not cabling any switch to another. This is the simplest possible
isolation mechanism — there is no tag to forget and no trunk to
misconfigure — but it comes with an obvious limitation: it cannot scale
past a small, fixed number of segments, since every additional isolated
group requires its own switch and its own dedicated cabling with no shared
infrastructure.

**Wireless confined to a single segment.** Access Point0 was attached only
to Switch3 (Segment D) and configured within Segment D's own addressing
range. This keeps wireless access scoped to the one segment that needs it,
rather than introducing a shared wireless network that could otherwise
create an unintended bridge between segments.

**No inter-segment connectivity at all.** Unlike designs that interconnect
some segments while isolating others, this network treats all four
segments identically: fully separate, fully independent. This was chosen
because none of the four groups have any operational need to reach one
another — each functions as a completely self-contained LAN.

---

## 6. Implementation Details

```
! Switch0 - management interface
interface vlan 1
 ip address 192.168.10.0 255.255.255.0
```

```
! Access Point0 - wireless configuration (Segment D only)
SSID: SegmentD-WiFi
Authentication: Disable
IP Address: 192.168.13.0 / 255.255.255.0
```

Wireless clients (PC15, Smartphone0) were configured with matching WPA2-PSK
credentials and assigned static IP addresses within the 192.168.13.0/24
range, keeping them within Segment D's addressing scheme.

---

## 7. Testing and Validation

The following tests were performed to confirm the network met its design
objectives.

| Test Case | Purpose | Expected Outcome | Observed Result |
|---|---|---|---|
| PC0 → PC1 | Confirm intra-segment connectivity (Segment A) | Success | Reply received |

<img width="303" height="353" alt="Screenshot 2026-09-13 115445" src="https://github.com/user-attachments/assets/c26be33f-ad6a-4b6c-8cd5-9454a359fbce" />

| Test Case | Purpose | Expected Outcome | Observed Result |
|---|---|---|---|
| PC0 → PC4 | Confirm isolation between Segment A and Segment B | Failure | Request timed out |

<img width="517" height="304" alt="Screenshot 2026-09-13 120551" src="https://github.com/user-attachments/assets/05c5da70-666e-4a99-89f2-cae28f7aca9a" />

| Test Case | Purpose | Expected Outcome | Observed Result |
|---|---|---|---|
| PC4 → PC8 | Confirm isolation between Segment B and Segment C | Failure | Request timed out |

<img width="518" height="370" alt="Screenshot 2026-09-13 120800" src="https://github.com/user-attachments/assets/629f0eab-fa29-465d-9405-506d1e8e9e6d" />

| Test Case | Purpose | Expected Outcome | Observed Result |
|---|---|---|---|
| PC0 → PC12 | Confirm isolation between Segment A and Segment D | Failure | Request timed out |

<img width="520" height="261" alt="Screenshot 2026-09-13 121923" src="https://github.com/user-attachments/assets/4cb3a1f1-f6e6-4e78-a8fb-55ccb6e50b58" />

| Test Case | Purpose | Expected Outcome | Observed Result |
|---|---|---|---|
| PC15 (wireless) → Server2 | Confirm wireless-to-wired connectivity within Segment D | Success | Reply received |

<img width="519" height="271" alt="Screenshot 2026-09-13 123802" src="https://github.com/user-attachments/assets/87b1229e-d6f4-45ca-ada5-2807339ca104" />

| Test Case | Purpose | Expected Outcome | Observed Result |
|---|---|---|---|
| PC15 (wireless) → PC4 | Confirm wireless clients in Segment D cannot reach Segment B | Failure | Request timed out |

<img width="514" height="231" alt="Screenshot 2026-09-13 124328" src="https://github.com/user-attachments/assets/e01f172b-7c99-48ee-8bc0-86d6f298aba3" />

---

## 8. Results and Discussion

Testing confirmed that the network performed exactly as designed: devices
within a segment could communicate freely, and every cross-segment test —
including from Segment D's wireless clients — correctly failed. This
validates the core design approach: with no uplinks, no VLANs, and no
routing, four independently cabled switches produce four completely
separate networks by default, and attaching a wireless access point to one
segment does not create any path into the others.

---

## 9. Conclusion

The implemented network successfully met all stated objectives — complete
isolation across all four segments, with wireless access correctly scoped
to Segment D alone. 
