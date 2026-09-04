Here is your structured study guide based on Jeremy's IT Lab video covering **Switch Interfaces**.

### 📝 Comprehensive Notes

**Switch Interface Defaults vs. Router Interfaces**
*   Unlike Cisco router interfaces (which are **administratively down** by default), Cisco **switch interfaces** are **enabled by default**. 
*   If you connect a PC to a switch, the interface will automatically transition to an **up/up** state (Layer 1 physical connection is up, Layer 2 protocol is up) without requiring the `no shutdown` command.

**Speed, Duplex, and Autonegotiation**
*   **Speed:** The data rate of the interface, measured in bits per second (e.g., 10 Mbps, 100 Mbps, 1000 Mbps). 
*   **Duplex:** Determines if a device can send and receive traffic simultaneously.
    *   **Half-Duplex:** The device cannot send and receive at the same time. If it receives a frame, it must wait to send. Required when connected to older **hub** devices.
    *   **Full-Duplex:** The device can send and receive data simultaneously. Preferred for modern networks.
*   **Autonegotiation:** Cisco switches default to `speed auto` and `duplex auto`. They will communicate with the connected device to negotiate the fastest speed and best duplex (Full) both devices support.

**Autonegotiation Failure Rules (Crucial for CCNA)**
If autonegotiation is disabled on the connected device (e.g., a PC has its speed and duplex hardcoded), the switch uses a fallback method:
1.  **Speed:** The switch attempts to *sense* the electrical signaling speed. If it fails to sense it, it defaults to the slowest supported speed (usually **10 Mbps**).
2.  **Duplex:** The switch *cannot* sense duplex. It relies on a strict rule:
    *   If the resulting speed is **10 Mbps or 100 Mbps**, it defaults to **Half-Duplex**.
    *   If the resulting speed is **1000 Mbps (1 Gbps) or higher**, it defaults to **Full-Duplex**.
*   *Note:* This fallback often causes a **Duplex Mismatch**, which leads to massive packet loss and **collisions**.

**Collision Domains & CSMA/CD**
*   **Collision Domain:** A network segment where data packets can collide with one another when being sent. 
    *   All ports on a **hub** belong to a *single* collision domain.
    *   Every individual port on a **switch** is its *own* separate collision domain.
*   **CSMA/CD (Carrier Sense Multiple Access with Collision Detection):** The mechanism used on half-duplex networks to prevent and handle collisions. Devices "listen" to the wire before sending. If a collision happens, they send a jamming signal, wait a random amount of time, and try sending again. 

**Interface Errors (`show interfaces` output)**
*   **Runts:** Frames smaller than the minimum Ethernet frame size (**64 bytes**).
*   **Giants:** Frames larger than the maximum Ethernet frame size (**1518 bytes**).
*   **CRC:** Frames that failed the Cyclic Redundancy Check (corrupted data detected in the **FCS - Frame Check Sequence** trailer).
*   **Frame:** Frames with an incorrect or illegal format (e.g., a frame that didn't end on a complete byte).
*   **Input errors:** A total aggregate counter of various incoming errors (runts, giants, CRC, etc.).
*   **Output errors:** Frames the switch tried to send but failed due to an error.

**Cisco IOS CLI Commands**
*   `interface range [type] [range]`: Configures multiple interfaces simultaneously. 
    *   *Example:* `interface range f0/5 - 12` or `interface range f0/5 - 6, f0/9 - 12`
*   `speed [10 | 100 | 1000 | auto]`: Manually sets the interface speed.
*   `duplex [half | full | auto]`: Manually sets the interface duplex.
*   `show interfaces status`: A highly useful command for switches. Displays the port, description (name), status, VLAN, duplex, speed, and media type in a clean table.
*   `show interfaces description`: Displays a concise list of interfaces, their up/down status, and their configured text descriptions.
*   `show interfaces [interface]`: Displays highly detailed statistics for a specific interface, including the **BIA (Burned-In MAC Address)** and detailed error counters (runts, giants, CRCs).

---

### 🚀 Quick-Reference Cheat Sheet

**Interface Status Meanings**
| Status | Meaning |
| :--- | :--- |
| **connected** | Cable is plugged in, device on other end is active (Up/Up). |
| **notconnect** | Port is enabled, but no physical link is detected (Down/Down). |
| **disabled / admin down** | Port is manually turned off via `shutdown` command. |

**Autonegotiation Fallback Matrix (If Auto fails)**
| Sensed Speed | Fallback Duplex Setting |
| :--- | :--- |
| 10 Mbps | **Half-Duplex** |
| 100 Mbps | **Half-Duplex** |
| 1000 Mbps (1 Gbps) | **Full-Duplex** |

**Ethernet Error Identifiers**
| Error Type | Definition |
| :--- | :--- |
| **Runt** | Frame is `< 64 bytes` |
| **Giant** | Frame is `> 1518 bytes` |
| **CRC** | Corrupted frame (failed FCS check) |

---

### 🧠 Practice Q&A

**1. You connect a PC to a Cisco switch. The PC has autonegotiation disabled and is hardcoded to a speed of 100 Mbps and Full-Duplex. The switchport is left at its default settings. What will be the resulting speed and duplex on the switchport?**
*Answer:* **100 Mbps and Half-Duplex.** The switch will successfully sense the 100 Mbps electrical signal. Because autonegotiation failed (the PC isn't sending auto-negotiation messages), the switch falls back to the default duplex for 100 Mbps, which is Half-Duplex. This will cause a duplex mismatch.

**2. Which of the following interface errors indicates a frame that is smaller than 64 bytes?**
A) Giant
B) CRC
C) Runt
D) Frame
*Answer:* **C) Runt.** Runts are frames smaller than the minimum standard Ethernet frame size of 64 bytes. Giants are larger than 1518 bytes, and CRC indicates corruption.

**3. Which command allows you to configure FastEthernet interfaces 1 through 5, and interface 10, all at the same time?**
*Answer:* `interface range f0/1 - 5 , f0/10`. The `interface range` command allows you to use hyphens for contiguous ranges and commas to separate non-contiguous ranges.

**4. What protocol is used on half-duplex Ethernet links to detect and manage data collisions?**
*Answer:* **CSMA/CD** (Carrier Sense Multiple Access with Collision Detection). Devices listen to the wire, and if a collision occurs, they send a jamming signal and wait a random back-off timer before re-transmitting.

---

### 💡 Deep-Dive Explanation with Examples

**Understanding Duplex Mismatch & Autonegotiation Failure**

One of the most common—and most tested—Layer 2 issues in networking is the **Duplex Mismatch**. 

**The Analogy: The One-Lane Bridge vs. The Two-Way Highway**
Imagine network data as cars. 
*   **Half-Duplex** is a narrow, **one-lane bridge**. Cars can only go one direction at a time. If two cars try to cross from opposite sides at the same time, they crash (a **Collision**). To avoid this, drivers stop, look, and listen (CSMA/CD).
*   **Full-Duplex** is a modern **two-way highway**. Cars can easily travel in both directions at the exact same time without ever crashing into each other.

**The Autonegotiation Process:**
When a Cisco Switch (Switch 1) and a PC connect, they perform a "handshake" (autonegotiation). 
Switch 1: *"I can do 1000 Mbps and a two-way highway (Full-Duplex). What about you?"*
PC: *"Me too! Let's use that."* 
Result = Perfect performance.

**The Mismatch Scenario (The CCNA Trap):**
Let's say a junior admin hardcodes the PC to 100 Mbps and Full-Duplex, effectively turning *off* the PC's ability to "handshake" (autonegotiate). 

1. Switch 1 reaches out: *"Hello? Let's negotiate!"*
2. The PC stays completely silent because autonegotiation is disabled.
3. Switch 1 thinks: *"Okay, no response. Let me sense the electrical signal. Ah, I sense 100 Mbps. I'll set my speed to 100 Mbps."*
4. Switch 1 then thinks: *"I can't sense the duplex. My Cisco programming rules state that if the speed is 10 or 100, I MUST assume the other side is an old, dumb device. I will default to a one-lane bridge (Half-Duplex)."*

**The Result:** The PC thinks it's on a two-way highway (Full-Duplex) and sends traffic constantly. The Switch thinks it's on a one-lane bridge (Half-Duplex) and expects the PC to take turns. The PC doesn't take turns, causing massive **collisions**, resulting in dropped packets and terrible network performance.