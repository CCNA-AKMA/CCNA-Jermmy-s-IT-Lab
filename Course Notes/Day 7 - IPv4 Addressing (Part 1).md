Here is your structured study guide based on Jeremy's IT Lab video covering **IPv4 Addressing (Part 1)**.

### 📝 Comprehensive Notes

**Layer 3 (The Network Layer) Overview**
*   **Layer 3** is responsible for routing traffic *between* different networks (outside of a single LAN).
*   It provides **logical addressing** (IP addresses), whereas Layer 2 provides physical addressing (MAC addresses).
*   **Routers** operate at Layer 3. They use IP addresses to make **path selection** decisions to route packets from a source to a destination.
*   **IP (Internet Protocol)** is the primary Layer 3 protocol used today, with **IPv4** being the most common version.

**The IPv4 Header & IP Addresses**
*   The IPv4 header contains many fields, but the two most important for routing are the **Source IP Address** and the **Destination IP Address**.
*   An IPv4 address is exactly **32 bits** (4 bytes) long.
*   To make it readable for humans, we use **dotted decimal notation**. The 32 bits are divided into four groups of 8 bits (called **octets**), separated by periods (e.g., `192.168.1.254`).

**Binary vs. Decimal**
*   **Decimal (Base 10):** Uses digits 0-9. Each position increases by a factor of 10 (1s, 10s, 100s, 1000s).
*   **Binary (Base 2):** Uses digits 0 and 1. Each position increases by a factor of 2. 
*   The positional values of an 8-bit octet from left to right are: **128, 64, 32, 16, 8, 4, 2, 1**. 
*   *Conversion Example:* To find the binary equivalent of decimal `168`, find the positional values that add up to 168 (128 + 32 + 8). Place a `1` in those positions and a `0` in the rest: `10101000`.

**Network and Host Portions**
*   Every IP address is split into two parts: the **Network portion** (which network the device is on) and the **Host portion** (the specific device on that network).
*   **Prefix Length:** Written with a slash (e.g., **/24**), this tells you exactly how many bits of the address belong to the network portion.
*   **Subnet Mask (Netmask):** Another way to write the prefix length using dotted decimal. A `/24` means 24 network bits (all `1`s) and 8 host bits (all `0`s), which translates to `255.255.255.0`.

**Special IPv4 Addresses**
*   **Network Address:** When the host portion of an IP address is all `0`s in binary, it represents the network itself. **It cannot be assigned to a host.** (e.g., `192.168.1.0/24`).
*   **Broadcast Address:** When the host portion of an IP address is all `1`s in binary, it represents the broadcast address. Traffic sent here goes to all devices on the network. **It cannot be assigned to a host.** (e.g., `192.168.1.255/24`).
*   **Loopback Address (127.0.0.0/8):** Any address beginning with `127` is reserved for loopback testing. 
    *   *CLI Command Mentioned:* `ping 127.0.0.1` (Command Prompt) — Pings your own device to test if the local TCP/IP "network stack" is functioning properly.

**IPv4 Address Classes**
Historically, IP addresses were grouped into default "classes" determined by their leading bits:
*   **Class A:** Leading bit `0`. Range: **0 - 127**. Default Prefix: **/8**
*   **Class B:** Leading bits `10`. Range: **128 - 191**. Default Prefix: **/16**
*   **Class C:** Leading bits `110`. Range: **192 - 223**. Default Prefix: **/24**
*   **Class D:** Leading bits `1110`. Range: **224 - 239**. Reserved for **Multicast**.
*   **Class E:** Leading bits `1111`. Range: **240 - 255**. Reserved for **Experimental** use.
*   *Note:* The 127.x.x.x network technically falls in Class A but is strictly reserved for loopback. Therefore, Class A usable networks stop at 126.

---

### 🚀 Quick-Reference Cheat Sheet

**IPv4 Classes & Ranges (Must Memorize!)**
| Class | Leading Bits | 1st Octet Range | Default Prefix | Default Netmask | Purpose |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **A** | `0` | 0 - 127* | `/8` | `255.0.0.0` | Large networks (16.7M hosts) |
| **B** | `10` | 128 - 191 | `/16` | `255.255.0.0` | Medium networks (65k hosts) |
| **C** | `110` | 192 - 223 | `/24` | `255.255.255.0` | Small networks (254 hosts) |
| **D** | `1110` | 224 - 239 | N/A | N/A | **Multicast** |
| **E** | `1111` | 240 - 255 | N/A | N/A | Experimental / Reserved |

*(Note: 127 is reserved for the Loopback testing range)*

**Binary to Decimal Positional Values (8 bits):**
`128 | 64 | 32 | 16 | 8 | 4 | 2 | 1`

**Special Address Rules:**
*   **Host bits = All 0s:** Network Address (Unusable for hosts)
*   **Host bits = All 1s:** Broadcast Address (Unusable for hosts)

---

### 🧠 Practice Q&A

**1. You are configuring a PC with the IP address `192.168.5.15`. What class of IPv4 address does this belong to, and what is the default prefix length for this class?**
*Answer:* It belongs to **Class C**. Class C ranges from 192-223 in the first octet. The default prefix length for a Class C address is **/24**.

**2. Why can you NOT assign the IP address `10.0.0.0/8` or `10.255.255.255/8` to a computer?**
*Answer:* In a `/8` network, the last 24 bits belong to the host. In `10.0.0.0`, all host bits are `0`, making it the **Network Address**. In `10.255.255.255`, all host bits are `1`s (255 in decimal is 11111111 in binary), making it the **Broadcast Address**. Neither can be assigned to a single end host.

**3. You open the command line on your PC and execute the command `ping 127.0.0.1`. What is the purpose of this command?**
*Answer:* This is a ping to a **Loopback Address**. It is used to test the local TCP/IP network stack on your own device to verify that the networking software is installed and functioning correctly, without actually sending traffic out to the physical network.

**4. Convert the binary octet `10110000` to decimal.**
*Answer:* **176**. Using the positional values (128, 64, 32, 16, 8, 4, 2, 1), you add the values where the bit is a `1`: 128 + 32 + 16 = 176.

---

### 💡 Deep-Dive Explanation with Examples

**Understanding Network vs. Host Portions (The "Street" and "House" Analogy)**

Imagine an IP address as a home mailing address. A mailing address has two main components: the **Street Name** and the specific **House Number**.

In networking, the IP address works exactly the same way. It is split into the **Network portion** (the Street) and the **Host portion** (the House). But how do we know where the street name ends and the house number begins? We use a **Prefix Length** or a **Subnet Mask**.

**Scenario:** 
Imagine **PC1** has the IP address `192.168.1.50 /24`. 

1. **The Mask (/24):** The `/24` tells us that the *first 24 bits* (the first 3 octets: 192.168.1) represent the Street Name. The remaining 8 bits (the last octet: .50) represent the specific House Number on that street.
2. **The Network Address (The Street Name):** If we want to refer to the *entire street* rather than a specific house, we set the house number to zero. Therefore, `192.168.1.0` is the **Network Address**. You can't assign this to PC1, just like you can't hand a letter to an entire street.
3. **The Broadcast Address (The Megaphone):** If you want to shout a message so every single house on the street hears it, you use the Broadcast Address. You find this by turning all the Host bits to `1`s. In binary, an octet of all `1`s equals 255. So, `192.168.1.255` is the **Broadcast Address**.
4. **Valid Host Addresses (The Houses):** This leaves the numbers from `192.168.1.1` all the way up to `192.168.1.254` as valid, assignable IP addresses for devices (like PC1, PC2, printers, and router interfaces) living on that specific street!