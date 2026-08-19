
### 1. 📝 Comprehensive Notes

**OSI Model & Layer 2 Review**

* **Layer 1 (Physical):** Deals with mediums (cables, connectors), digital bits, and converting bits to electrical or radio signals.
* **Layer 2 (Data Link):** Provides node-to-node connectivity (e.g., PC to Switch). It uses **MAC addresses** (Layer 2 addressing) rather than IP addresses (Layer 3). Switches operate entirely at this layer.
* **Protocol Data Unit (PDU):** The L2 PDU is called a **Frame**. The process of preparing data to be sent across the network is called **encapsulation**.

**Local Area Networks (LANs)**

* A **LAN** is a network contained within a small area (home, office).
* **Routers** are used to connect separate LANs.
* **Switches** do *not* separate LANs; they expand an existing LAN. All devices connected together by switches (without passing through a router) are in the same LAN.

**The Ethernet Frame Structure (26 Bytes Total for Header & Trailer)**

* **Preamble (7 Bytes):** A series of alternating 1s and 0s. Allows devices to synchronize their receiver clocks.
* **SFD / Start Frame Delimiter (1 Byte):** Indicates the end of the preamble and the beginning of the frame.
* **Destination MAC Address (6 Bytes):** The physical address of the device intended to receive the frame.
* **Source MAC Address (6 Bytes):** The physical address of the device sending the frame.
* **Type / Length (2 Bytes):**
* If the value is **1500 or less**, it indicates the **length** of the encapsulated packet in bytes.
* If the value is **1536 or greater**, it indicates the **type** of the encapsulated packet (e.g., `0x0800` for IPv4, `0x86DD` for IPv6).
* **FCS / Frame Check Sequence (4 Bytes):** The only field in the Ethernet trailer. It uses a **CRC (Cyclic Redundancy Check)** algorithm over the received data to detect any corruption or errors.

**MAC Addresses (Media Access Control)**

* A MAC address is a 48-bit (6-byte) physical address assigned to a device's network interface card when it is manufactured. It is often called a **BIA (Burned-In Address)**.
* It is globally unique and written in 12 hexadecimal characters (e.g., `AAAA.AAAA.0001`).
* **OUI (Organizationally Unique Identifier):** The first 24 bits (3 bytes) of the MAC address. It identifies the vendor/manufacturer (e.g., Cisco).
* **Device ID:** The last 24 bits (3 bytes) are uniquely assigned to the specific device by the manufacturer.

**How Switches Learn and Forward**

* **MAC Address Table:** Switches maintain a table mapping MAC addresses to specific physical interfaces (ports).
* **Learning:** Switches populate this table *dynamically* by inspecting the **Source MAC Address** of every incoming frame.
* **Aging:** On Cisco switches, dynamically learned MAC addresses are removed from the table after **5 minutes** of inactivity.
* **Unknown Unicast:** If a switch receives a frame destined for a single host, but the destination MAC is *not* in its table, it will **flood** the frame. Flooding means sending a copy out of every interface *except* the one it was received on.
* **Known Unicast:** If the destination MAC *is* in the table, the switch simply forwards the frame out of the specific corresponding interface.

---

### 2. 🚀 Quick-Reference Cheat Sheet

**Ethernet Frame Fields & Sizes**

| Field                     | Size    | Purpose                                                         |
| ------------------------- | ------- | --------------------------------------------------------------- |
| **Preamble**        | 7 Bytes | Receiver clock synchronization                                  |
| **SFD**             | 1 Byte  | Start Frame Delimiter; marks the beginning of the MAC addresses |
| **Destination MAC** | 6 Bytes | Where the frame is going                                        |
| **Source MAC**      | 6 Bytes | Who sent the frame (Used by switches for learning)              |
| **Type/Length**     | 2 Bytes | IPv4 (0x0800), IPv6 (0x86DD), or packet payload length          |
| **FCS (Trailer)**   | 4 Bytes | Frame Check Sequence; error detection via CRC                   |

**Must-Memorize Switch Logic:**

* **Source MAC** = Used to **LEARN** ports.
* **Destination MAC** = Used to **FORWARD** frames.
* **MAC Address Size** = 48 bits (6 bytes).
* **Dynamic MAC Aging Timer** = 5 minutes (300 seconds).

---

### 3. 🧠 Practice Q&A

**Question 1:** Which field of an Ethernet frame does a switch use to populate its MAC address table?
A) Destination MAC Address
B) Type
C) Source MAC Address
D) Preamble

**Answer: C.**
*Explanation:* A switch dynamically learns the location of devices on the network by inspecting the *Source MAC Address* of incoming frames and associating that address with the port the frame entered on.

**Question 2:** A switch receives a unicast frame, but it does not have an entry for the destination MAC address in its MAC address table. What will the switch do?
A) Drop the frame immediately.
B) Flood the frame out all interfaces except the one it was received on.
C) Send a request back to the sender asking for directions.
D) Forward the frame out the lowest-numbered interface.

**Answer: B.**
*Explanation:* When a switch encounters an "unknown unicast" frame (a frame destined for a specific host that is not yet in the MAC table), it must flood the frame out of all active ports except the receiving port to ensure the frame reaches its intended destination.

**Question 3:** What is the purpose of the 4-byte Frame Check Sequence (FCS) at the end of an Ethernet frame?
A) To synchronize the clock of the receiving device.
B) To identify the manufacturer of the network card.
C) To specify whether the payload is IPv4 or IPv6.
D) To detect if data was corrupted during transmission.

**Answer: D.**
*Explanation:* The FCS field resides in the Ethernet trailer and uses a Cyclic Redundancy Check (CRC) algorithm to verify data integrity. Clock synchronization is done by the Preamble (A). Manufacturer identification is the OUI portion of the MAC address (B). Payload type is specified by the Type field (C).

---

### 4. 💡 Deep-Dive Explanation with Examples

**Concept: Switch MAC Learning and Unknown Unicast Flooding**

Imagine a network switch is like a new mailroom clerk in a large office building. On their first day, their building directory (the **MAC Address Table**) is completely blank. They don't know where anyone's desk is.

**The Scenario:**
We have a Switch connecting three computers:

* **PC1** is connected to interface **F0/1**
* **PC2** is connected to interface **F0/2**
* **PC3** is connected to interface **F0/3**

**Step 1: The First Message**
PC1 wants to send a private message to PC3. PC1 builds a frame with:

* *Source MAC:* PC1
* *Destination MAC:* PC3
  PC1 sends this frame down the wire, and it arrives at the switch on port **F0/1**.

**Step 2: Learning (Source MAC)**
The mailroom clerk (the Switch) receives the frame and looks at the *Source MAC* address first. The clerk says, "Ah! PC1 sent this message, and it came through door F0/1. Therefore, PC1 must live down hallway F0/1." The switch immediately writes this down in its MAC Address Table:
*(PC1 MAC Address = Port F0/1)*

**Step 3: Flooding (Unknown Unicast)**
Next, the clerk looks at the *Destination MAC* address. It says "To: PC3". The clerk checks the directory, but PC3 isn't listed yet. Because the switch doesn't know where PC3 is, it can't just guess a single port. Instead, it photocopies the message and yells it down *every other hallway* except the one it came from.
The switch sends the frame out of **F0/2** and **F0/3**. This is called **flooding** an **unknown unicast** frame.

**Step 4: The Response**

* PC2 receives the flooded frame, realizes it's addressed to PC3, and silently drops it in the trash.
* PC3 receives the frame, sees its own MAC address on the "To" line, and accepts it.

**Step 5: Known Unicast Forwarding**
PC3 decides to write a reply back to PC1.

* *Source MAC:* PC3
* *Destination MAC:* PC1
  PC3 sends the frame, and it hits the switch on port **F0/3**.
  The switch does its learning step again: "Ah, PC3 lives down hallway F0/3!" It adds this to the table.
  Then, it looks at the destination: "To: PC1". The switch checks its directory and says, "I already know where PC1 is! It's on F0/1."
  Instead of flooding the message this time, the switch sends it *directly* and *only* out of port F0/1. This direct delivery is called forwarding a **known unicast** frame.
