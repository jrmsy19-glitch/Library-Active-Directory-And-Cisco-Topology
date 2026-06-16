# Windows Server Hardening & Active Directory Infrastructure Deployment (GCP)

An enterprise-grade implementation of a secure Windows Server environment running on Google Cloud Platform (GCP). This project demonstrates the implementation of strict security benchmarks, Active Directory (AD) infrastructure engineering, Group Policy Object (GPO) engineering, mandatory user profiles, network print management, and environment isolation designed for a public library kiosk deployment.

---

## 🚀 Project Overview

The objective of this project was to design, provision, and secure a multi-user Windows Server environment optimized for high-risk public access kiosks (`library.lan`). By leveraging a Zero Trust configuration at the operating system level, the environment completely isolates guest sessions, enforces strict network and application controls, minimizes the attack surface, and automates policy refreshes.

### Key Highlights
- **Cloud Infrastructure:** Provisioned via Google Compute Engine with balanced persistent storage.
- **Identity & Access Management (IAM):** Engineered an Active Directory Organizational Unit (OU) hierarchy to separate workloads and handle temporary generic user sessions.
- **Group Policy Hardening:** Configured comprehensive GPOs blocking administrative tools, locking down the file system, enforcing whitelisted web browsing, and restricting storage media access.
- **Session Lifecycle Controls:** Standardized a persistent mandatory profile architecture alongside strict automated logoff time schedules.

---

## 🏗️ Architecture & Technical Specifications

### 1. Cloud Provisioning (Google Cloud Platform)
* **Instance Name:** `lib-dc-2022`
* **Operating System:** Windows Server 2022 Datacenter (Premium Image)
* **Compute Engine Tier:** `e2-standard-2` (2 vCPUs, 8 GB Virtual Memory)
* **Storage Architecture:** 50 GB Balanced Persistent Disk
* **Cost Efficiency Model:** PAYG (Pay-as-you-go) with per-second billing (~$121.08/month base estimate)

### 2. Active Directory Services (AD DS) Core Structure
The domain architecture (`library.lan`) uses a centralized container pattern under the root OU `Public-Library-Root` to segregate policy inheritance dynamically:
* `Public-Library-Root`
  * 📁 `Network-Printers` (Print server distribution endpoints)
  * 📁 `Public-Generic-Users` (Targeted guest user accounts `Guest01` to `Guest20`)
  * 📁 `Public-Kiosks` (Physical kiosk computer account targets)
  * 📁 `Security-Groups` (RBAC access provisioning groups)
  * 📁 `Staff-Accounts` / `Staff-Workstations` (Administrative and employee tracking)

### 3. Session Isolation & Mandatory Profiles
To enforce absolute data privacy and prevent local environment drift between different guest sessions:
* Configured an **Advanced Network Share** at `C:\\Profiles` mapped to `\\\\localhost\\Profiles\\mandatory`.
* Restricted share access strictly to **Read-Only** for the `Everyone` built-in principal, explicitly stripping Full Control or Write mechanics.
* Created a standardized template within `Mandatory.v6` ensuring that any desktop adjustments, cache files, or session logs are completely purged upon user logoff.

---

## 🔒 Security Hardening Policies Reference

A centralized Group Policy Object named **`Public_Kiosk_Hardening_Policy`** was constructed and enforced across the environment. Below is a matrix of the implemented baseline controls:

| Category | Policy Rule | Configured State | Objective / Mitigation |
| :--- | :--- | :--- | :--- |
| **System Security** | Prevent access to the command prompt | **Enabled** (Script processing blocked) | Blocks arbitrary CLI execution, script exploitation, and interactive `cmd.exe` access. |
| **System Security** | Prevent access to registry editing tools | **Enabled** (Silent execution blocked) | Disables `regedit.exe` completely, mitigating unauthorized configuration tampering. |
| **Hardware Control**| All Removable Storage classes: Deny all access | **Enabled** | Eliminates data exfiltration risks and halts malware injection via external USB/CD/DVD drives. |
| **File System** | Hide these specified drives in My Computer | **Enabled** (Restrict C drive only) | Conceals critical system folder hierarchies from File Explorer and Open dialogs. |
| **Interface Lockdown**| Prohibit access to Control Panel and PC settings| **Enabled** | Restricts guest users from viewing or altering system configuration menus. |
| **Shell Hardening** | Remove Run menu from Start Menu | **Enabled** | Denies access to system shortcuts, unwhitelisted paths, or raw execution via the UI. |
| **Process Control** | Remove Task Manager | **Enabled** (Ctrl+Alt+Del Options) | Prevents users from manually terminating mandatory system services or endpoint monitoring software. |
| **Inactivity Control**| Screen saver timeout / Enable screen saver | **Enabled** (**300 Seconds** / 5 mins) | Automatically locks/clears active displays during user abandonment intervals. |
| **Session Control** | Disconnect clients when logon hours expire | **Enabled** (Microsoft Network Server) | Automatically terminates sessions to ensure strict compliance with physical closing hours. |
| **Network Security**| Define a list of allowed URLs | **Enabled** (Strict Whitelist) | Restricts Microsoft Edge traffic exclusively to authorized domains: `google.com` and `wikipedia.org`. |

---

## 🗺️ Chronological Deployment & Image Glossary

Below is the verified chronological ledger of the system configuration, capturing real-time evidence of the provisioning, file system development, printer object security mapping, identity constraints, and system-wide policy flashes on **June 12, 2026**.

### Phase 1: Environment Isolation & Directory Foundation
#### [Step 1] Local Security File System Provisions
* **Timestamp / Date:** 03:17 AM - 03:18 AM | June 12, 2026
* **Technical Action:** Built out the physical profile directories on the server's primary storage block (`C:\\Profiles\\Mandatory.v6`). Established the network share definitions and locked down security properties to ensure guest sessions pull a static profile without modification capacity.

#### [Step 2] Active Directory Schema Layout
* **Timestamp / Date:** Context Snapshot (Pre-Configuration verification)
* **Technical Action:** Modeled the domain root tree hierarchy under `library.lan` and established the baseline link structure for the `Public_Kiosk_Hardening_Policy` GPO to guarantee targeted inheritance across the kiosk OUs.

---

### Phase 2: Peripherals & Network Print Architecture
#### [Step 3] Universal Printer Object Creation
* **Timestamp / Date:** 03:43 AM | June 12, 2026
* **Technical Action:** Initialized the Network Printer Installation Wizard on the `lib-dc-2022` host. Provisioned the public-facing printing queue object (`Lib-Public-Kiosk-Printer`) mapped to a localized port using a clean system-compatible text-only abstraction driver.

#### [Step 4] Staff Printer Object Deployment
* **Timestamp / Date:** 03:45 AM | June 12, 2026
* **Technical Action:** Provisioned the parallel high-privilege staff printing queue (`Lib-Staff-Only-Printer`). Verified server tracking shows both active printer queues sitting in a steady `Ready` queue status.

#### [Step 5] Access Control List (ACL) Printer Hardening
* **Timestamp / Date:** 04:18 AM - 04:19 AM | June 12, 2026
* **Technical Action:** Hardened the print security properties by binding an explicit Role-Based Access Control list. Tied permission models specifically to the `Staff-Prnt-Allowed` security group to prevent public access or malicious print job manipulation.

---

### Phase 6: Edge Routing Whitelists & Temporal Fencing
#### [Step 6] Microsoft Edge Content Isolation Whitelisting
* **Timestamp / Date:** 05:12 AM | June 12, 2026
* **Technical Action:** Programmed the browser URL exception matrix within the administrative GPO template. Formatted explicit protocol blocks allowing navigation exclusively to `https://google.com` and `https://wikipedia.org`, mitigating external command-and-control communication or recreational surfing.

#### [Step 7] Active Directory Session Hardening Integration
* **Timestamp / Date:** 05:16 AM | June 12, 2026
* **Technical Action:** Configured the security policy rule `Microsoft network server: Disconnect clients when logon hours expire` to **Enabled**, cementing active system-side enforcement of user lifecycle rules.

#### [Step 8] Temporal Fencing Matrix Construction
* **Timestamp / Date:** 05:21 AM - 05:27 AM | June 12, 2026
* **Technical Action:** Executed bulk property injections across the `Guest01` through `Guest20` account sheets. Implemented strict operational access fences restricting directory authentication capabilities exclusively to **Monday-Saturday, 08:00 AM - 08:00 PM**, while hard-blocking all access on Sundays.

#### [Step 9] Security Domain Policy Engine Synchronization
* **Timestamp / Date:** 05:28 AM | June 12, 2026
* **Technical Action:** Initiated a system-wide policy propagation via administrative PowerShell. Executed `gpupdate /force` to execute an immediate sync of newly committed parameters, confirming successful execution across computer and user registry blocks.

---

### Phase 4: Full Shell Hardening Policies
#### [Step 10] Control Panel Interdiction Policy Execution
* **Timestamp / Date:** 11:43 PM | June 12, 2026
* **Technical Action:** Configured the `Prohibit access to Control Panel and PC settings` sub-key to block users from modifying configurations or viewing network device schemas.

#### [Step 11] Start Menu Interdiction Policy Integration
* **Timestamp / Date:** 11:48 PM | June 12, 2026
* **Technical Action:** Applied strict shell path limitations by explicitly disabling the `Run` sub-menus across the start container and task sequence interfaces.

#### [Step 12] Task Manager Shell Removal Execution
* **Timestamp / Date:** 11:49 PM | June 12, 2026
* **Technical Action:** Disabled user diagnostic overrides by disabling `Task Manager` access under security interface triggers, preventing execution of `taskmgr.exe`.

#### [Step 13] Inactivity Timeout System Automation
* **Timestamp / Date:** 11:53 PM - 11:55 PM | June 12, 2026
* **Technical Action:** Automated station lockout protocols by forcing the deployment of a default screen saver thread matched against a hard **300-second** processing countdown.

---

## 🛠️ Key Technical Competencies Demonstrated

- **Windows Server Administration:** Core system engineering utilizing advanced configurations of Active Directory Domain Services (AD DS).
- **Group Policy Infrastructure Engineering:** Direct implementation of advanced custom registry controls, interface shielding, and whitelisting.
- **System Hardening & Information Assurance:** Practical deployment of multi-user environment configurations designed against local permission escalation vector sets.
- **Enterprise Network Services Management:** Secure mapping and ACL configuration of core infrastructure services including network share blocks and enterprise printing infrastructures.
"""

with open("README.md", "w") as f:
    f.write(content)

print("SUCCESS")
