
### 1. 📝 Comprehensive Notes

**Connecting to a Cisco Device**

* **Cisco IOS (Internetwork Operating System)**: The operating system used on most Cisco devices like routers and switches.
* **CLI (Command Line Interface)**: The primary text-based interface used by network engineers to configure Cisco devices.
* **Console Port**: Used for the initial, out-of-the-box configuration of a device. You connect a PC to this port using a **rollover cable** (RJ45 to DB9/USB adapter) or a **USB Mini-B** cable.
* **Terminal Emulator**: Software used to access the CLI (e.g., PuTTY).
* **Default Console Port Settings** (Must know for PuTTY):
  * **Speed (Baud rate)**: 9600 bps
  * **Data bits**: 8
  * **Stop bits**: 1
  * **Parity**: None
  * **Flow control**: None

**Cisco IOS CLI Modes**

* **User EXEC Mode** (`Router>`): The default mode. Very limited; allows basic viewing of some settings but no configuration changes.
* **Privileged EXEC Mode** (`Router#`): Allows full viewing of the device configuration, restarting the device, and saving configurations. Still cannot make active changes to the network settings.
* **Global Configuration Mode** (`Router(config)#`): The mode where you actually make active changes to the device's configuration.

**Cisco IOS Passwords & Security**

* `enable password [password]`: Sets a password to protect Privileged EXEC mode. **Insecure** because it stores the password in plain text.
* `enable secret [password]`: Sets a heavily encrypted (MD5 - Type 5) password to protect Privileged EXEC mode. *Note: If both are configured, `enable secret` takes precedence over `enable password`.*
* `service password-encryption`: A global configuration command that lightly encrypts (Type 7) all plain-text passwords currently in the configuration file. It is easily cracked and should not be relied upon for high security, but it prevents "shoulder-surfing" (people seeing your password over your shoulder).

**Configuration Files**

* **running-config**: The *active* configuration currently running in the device's RAM. Changes made in the CLI apply to this file immediately. If the router reboots, this file is lost.
* **startup-config**: The *saved* configuration stored in NVRAM (Non-Volatile RAM). When a router boots up, it loads this file to become the new running-config.

**Essential CLI Commands & Shortcuts**

* `enable` (or `en`): Moves from User EXEC to Privileged EXEC mode.
* `exit` (or `ex`): Moves back down one layer (e.g., Global Config to Privileged EXEC).
* `configure terminal` (or `conf t`): Moves from Privileged EXEC to Global Configuration mode.
* `show running-config` (or `sh run`): Displays the current, active configuration.
* `show startup-config` (or `sh start`): Displays the saved configuration.
* `no [command]`: Used in Global Config mode to negate or remove a previously entered command (e.g., `no service password-encryption`).
* `do [command]`: Placed in front of a Privileged EXEC command to run it while you are inside Global Configuration mode (e.g., `do sh run`).
* **CLI Hotkeys**:
  * `?`: Shows all available commands for your current mode.
  * `Tab`: Auto-completes a partially typed command.

---

### 2. 🚀 Quick-Reference Cheat Sheet

**CLI Navigation Matrix**

| Mode                      | Prompt              | How to Enter                                 | How to Exit / Go Back                   |
| :------------------------ | :------------------ | :------------------------------------------- | :-------------------------------------- |
| **User EXEC**       | `Router>`         | Default upon login                           | `logout` or `exit` (closes session) |
| **Privileged EXEC** | `Router#`         | Type`enable` (from User EXEC)              | Type`disable` or `exit`             |
| **Global Config**   | `Router(config)#` | Type`configure terminal` (from Priv. EXEC) | Type`exit`                            |

**Saving the Configuration (Privileged EXEC Mode)**
You must save the `running-config` to the `startup-config` so changes survive a reboot. All three commands do the exact same thing:

1. `write` (or `wr`)
2. `write memory` (or `wr mem`)
3. `copy running-config startup-config` (or `copy run start`)

**Console Port Default Settings (PuTTY)**

* **9600 / 8 / 1 / None / None** (Speed / Data Bits / Stop Bits / Parity / Flow Control)

---

### 3. 🧠 Practice Q&A

**Question 1:** You are logged into a Cisco switch and the prompt currently shows `Switch(config)#`. You want to quickly view the active configuration without leaving your current mode. Which command should you type?
A) `show running-config`
B) `do show running-config`
C) `show startup-config`
D) `exit show running-config`

**Answer:** **B) `do show running-config`**
*Explanation:* `show running-config` is a Privileged EXEC mode command. Because you are currently in Global Configuration mode (`(config)#`), you must append the word `do` in front of the command to execute it successfully without dropping back down to Privileged EXEC mode.

**Question 2:** An administrator types `enable password CISCO` and then immediately types `enable secret CCNA` on a brand new router. When a user tries to enter Privileged EXEC mode from User EXEC mode, which password will they be required to enter?
A) CISCO
B) CCNA
C) They will be prompted for both passwords.
D) The router will lock out because of a configuration conflict.

**Answer:** **B) CCNA**
*Explanation:* The `enable secret` command uses strong (MD5) encryption and always takes precedence over the plain-text `enable password` command if both are configured on the same device.

**Question 3:** Which of the following correctly describes the function of the `service password-encryption` command?
A) It secures the console port so a password is required upon physical plug-in.
B) It upgrades the `enable password` to the same MD5 encryption used by `enable secret`.
C) It prevents shoulder-surfing by applying a weak (Type 7) encryption to all plain-text passwords in the configuration file.
D) It encrypts data traffic as it flows through the router interfaces.

**Answer:** **C) It prevents shoulder-surfing by applying a weak (Type 7) encryption to all plain-text passwords in the configuration file.**
*Explanation:* `service password-encryption` scrambles plain-text passwords in the `running-config` so they cannot be read over your shoulder, but it is considered weak encryption and can be easily reversed using online tools. It does not replace the high-security MD5 hash of `enable secret`.

**Question 4:** You just spent 30 minutes configuring IP addresses on a Cisco router and verifying they work. Suddenly, the power goes out in the building. When the router boots back up, your configurations are completely gone. Why did this happen?
A) You forgot to copy the startup-config to the running-config.
B) You forgot to save the running-config to the startup-config using the `write` command.
C) The router experienced a flash memory failure.
D) You did not encrypt the configuration using `enable secret`.

**Answer:** **B) You forgot to save the running-config to the startup-config using the `write` command.**
*Explanation:* The active configuration (`running-config`) is stored in volatile RAM, which is wiped when power is lost. To make changes permanent, they must be saved to NVRAM (`startup-config`) using commands like `write` or `copy running-config startup-config`.

---

### 4. 💡 Deep-Dive Explanation with Examples

**Running-Config vs. Startup-Config: The "Word Document" Analogy**

Understanding the difference between the **running-config** and the **startup-config** is one of the most critical concepts for a network engineer to grasp, because messing it up can cost you hours of work.

Imagine you sit down at your PC and open Microsoft Word to write a 10-page essay.

* **The Running-Config (RAM):**
  As you are actively typing your essay, your words are being held in the computer's **RAM (Random Access Memory)**. In the Cisco world, this is the **running-config**. As soon as you type a command like `hostname Router1` into the CLI, the router instantly applies it. The router's name literally changes that very second. However, RAM is *volatile*. If your laptop battery dies before you hit "Save" on your essay, the entire document is gone forever. The same is true for the router; if someone trips over the power cord, the `running-config` is erased.
* **The Startup-Config (NVRAM):**
  To protect your essay, you go to File > Save. This copies your document from temporary RAM onto your permanent Hard Drive. In the Cisco world, this is the **startup-config**, which is saved in **NVRAM (Non-Volatile RAM)**. NVRAM survives power outages.
* **The Golden Rule:**
  When a Cisco device boots up, it looks into its NVRAM, grabs the `startup-config`, and loads it into RAM to become the active `running-config`.

  Therefore, every time you make changes to a router, you are only changing the `running-config`. You **must** remember to "hit save" by typing `copy running-config startup-config` (or simply `write`). If you don't, the next time the router reboots, it will load the old, outdated `startup-config`, and all your hard work will disappear into the digital void!
