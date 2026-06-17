# Windows Server Hardening, Active Directory Infrastructure Deployment (GCP) & Cisco Packet Tracer Network Topology

An enterprise-grade implementation of a secure Windows Server environment running on Google Cloud Platform (GCP). This project demonstrates the implementation of strict security benchmarks, Active Directory (AD) infrastructure engineering, Group Policy Object (GPO) engineering, mandatory user profiles, network print management, and environment isolation designed for a public library kiosk deployment. In addition, this lab also showcases network hardware configuriation and segmentation via Cisco Packet Tracer.

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


<img width="1163" height="536" alt="Screenshot 2026-06-11 at 10 27 33 PM" src="https://github.com/user-attachments/assets/e5dac950-12eb-4abd-9613-3e5e4d5fb87d" />

<img width="1124" height="596" alt="Screenshot 2026-06-11 at 10 18 31 PM" src="https://github.com/user-attachments/assets/22fdf5c3-5ab3-4284-bcc7-550f25d58939" />


#### [Step 2] Active Directory Schema Layout
* **Timestamp / Date:** Context Snapshot (Pre-Configuration verification)
* **Technical Action:** Modeled the domain root tree hierarchy under `library.lan` and established the baseline link structure for the `Public_Kiosk_Hardening_Policy` GPO to guarantee targeted inheritance across the kiosk OUs.

<img width="754" height="530" alt="Screenshot 2026-06-11 at 10 12 40 PM" src="https://github.com/user-attachments/assets/8cfe33f8-2649-488a-ae3b-506411c2433b" />

---

### Phase 2: Peripherals & Network Print Architecture
#### [Step 3] Universal Printer Object Creation
* **Timestamp / Date:** 03:43 AM | June 12, 2026
* **Technical Action:** Initialized the Network Printer Installation Wizard on the `lib-dc-2022` host. Provisioned the public-facing printing queue object (`Lib-Public-Kiosk-Printer`) mapped to a localized port using a clean system-compatible text-only abstraction driver.

<img width="944" height="725" alt="Screenshot 2026-06-11 at 10 43 59 PM" src="https://github.com/user-attachments/assets/43f503de-f058-4863-8c3c-4395f95ffa1c" />

#### [Step 4] Staff Printer Object Deployment
* **Timestamp / Date:** 03:45 AM | June 12, 2026
* **Technical Action:** Provisioned the parallel high-privilege staff printing queue (`Lib-Staff-Only-Printer`). Verified server tracking shows both active printer queues sitting in a steady `Ready` queue status.

<img width="945" height="743" alt="Screenshot 2026-06-11 at 11 19 20 PM" src="https://github.com/user-attachments/assets/7d552af1-5bd9-46b4-8be0-dbedf534d2ea" />


#### [Step 5] Access Control List (ACL) Printer Hardening
* **Timestamp / Date:** 04:18 AM - 04:19 AM | June 12, 2026
* **Technical Action:** Hardened the print security properties by binding an explicit Role-Based Access Control list. Tied permission models specifically to the `Staff-Prnt-Allowed` security group to prevent public access or malicious print job manipulation.
<img width="945" height="743" alt="Screenshot 2026-06-11 at 11 19 20 PM" src="https://github.com/user-attachments/assets/a641487f-299b-41d1-8d93-9ec154128977" />

---

### Phase 6: Edge Routing Whitelists & Temporal Fencing
#### [Step 6] Microsoft Edge Content Isolation Whitelisting
* **Timestamp / Date:** 05:12 AM | June 12, 2026
* **Technical Action:** Programmed the browser URL exception matrix within the administrative GPO template. Formatted explicit protocol blocks allowing navigation exclusively to `https://google.com` and `https://wikipedia.org`, mitigating external command-and-control communication or recreational surfing.
<img width="1281" height="635" alt="Screenshot 2026-06-12 at 12 13 43 AM" src="https://github.com/user-attachments/assets/a522c4e4-6840-4d59-886e-e907d9e5a57a" />

#### [Step 7] Active Directory Session Hardening Integration
* **Timestamp / Date:** 05:16 AM | June 12, 2026
* **Technical Action:** Configured the security policy rule `Microsoft network server: Disconnect clients when logon hours expire` to **Enabled**, cementing active system-side enforcement of user lifecycle rules.
<img width="1117" height="573" alt="Screenshot 2026-06-12 at 12 16 35 AM" src="https://github.com/user-attachments/assets/b3a464f7-1f77-4b81-9e73-d3f331e46684" />


#### [Step 8] Temporal Fencing Matrix Construction
* **Timestamp / Date:** 05:21 AM - 05:27 AM | June 12, 2026
* **Technical Action:** Executed bulk property injections across the `Guest01` through `Guest20` account sheets. Implemented strict operational access fences restricting directory authentication capabilities exclusively to **Monday-Saturday, 09:00 AM - 08:00 PM**, while hard-blocking all access on Sundays.
<img width="1387" height="618" alt="Screenshot 2026-06-12 at 12 27 27 AM" src="https://github.com/user-attachments/assets/8d6ab88b-084c-43dd-9e5c-ad810b4d6e42" />



#### [Step 9] Security Domain Policy Engine Synchronization
* **Timestamp / Date:** 05:28 AM | June 12, 2026
* **Technical Action:** Initiated a system-wide policy propagation via administrative PowerShell. Executed `gpupdate /force` to execute an immediate sync of newly committed parameters, confirming successful execution across computer and user registry blocks.
<img width="975" height="511" alt="Screenshot 2026-06-12 at 12 28 34 AM" src="https://github.com/user-attachments/assets/24956cc9-1c7b-4d9f-9bc9-4b5659ae95f6" />


---

### Phase 4: Full Shell Hardening Policies
#### [Step 10] Control Panel Interdiction Policy Execution
* **Timestamp / Date:** 11:43 PM | June 12, 2026
* **Technical Action:** Configured the `Prohibit access to Control Panel and PC settings` sub-key to block users from modifying configurations or viewing network device schemas.

<img width="1380" height="635" alt="Screenshot 2026-06-12 at 6 43 50 PM" src="https://github.com/user-attachments/assets/1b261a42-b8a2-496c-bdd6-b1c8acf4fabf" />


#### [Step 11] Start Menu Interdiction Policy Integration
* **Timestamp / Date:** 11:48 PM | June 12, 2026
* **Technical Action:** Applied strict shell path limitations by explicitly disabling the `Run` sub-menus across the start container and task sequence interfaces.

<img width="1349" height="637" alt="Screenshot 2026-06-12 at 6 48 36 PM" src="https://github.com/user-attachments/assets/1aa0013b-95d2-4dc9-821e-22c37dec9bc5" />


#### [Step 12] Task Manager Shell Removal Execution
* **Timestamp / Date:** 11:49 PM | June 12, 2026
* **Technical Action:** Disabled user diagnostic overrides by disabling `Task Manager` access under security interface triggers, preventing execution of `taskmgr.exe`.

<img width="1362" height="637" alt="Screenshot 2026-06-12 at 6 49 50 PM" src="https://github.com/user-attachments/assets/179ee7df-2cda-41d5-91d6-f2f4a3b4e785" />


#### [Step 13] Inactivity Timeout System Automation
* **Timestamp / Date:** 11:53 PM - 11:55 PM | June 12, 2026
* **Technical Action:** Automated station lockout protocols by forcing the deployment of a default screen saver thread matched against a hard **300-second** processing countdown.

<img width="1364" height="638" alt="Screenshot 2026-06-12 at 6 53 57 PM" src="https://github.com/user-attachments/assets/7faaa4ca-baa6-4518-a3bc-23139fa98ed3" />


---
## Cisco Packet Tracer Network Configuration andScreenshots

### Screenshot 01 – High-Level Network Architecture

<img width="1466" height="594" alt="Screenshot 2026-06-12 at 8 46 23 PM" src="https://github.com/user-attachments/assets/b07628c7-2043-458f-9b4a-879d3b3659fa" />

**Description**

This diagram illustrates the logical architecture of the environment. Utilizing a Cisco Catalyst 3650 Layer 3 Core Switch, which serves as the central point for routing and segmentation between operational zones.

**Highlights**

- Centralized Active Directory infrastructure
- Public Kiosk Zone
- Staff Operations Zone
- Layer 3 Core Switching
- Enterprise-style network segmentation

---

### Screenshot 02 – Full Library Network Deployment

<img width="1470" height="597" alt="Screenshot 2026-06-12 at 8 42 20 PM" src="https://github.com/user-attachments/assets/2c1da9a0-26ab-4115-a7aa-6d1598877a80" />


**Description**

This diagram shows the completed deployment consisting of twenty public kiosk workstations, four staff workstations, shared printers, access switches, and centralized domain services.

**Infrastructure Summary**

| Resource | Quantity |
|-----------|-----------|
| Public Workstations | 20 |
| Staff Workstations | 4 |
| Domain Controllers | 1 |
| Core Switches | 1 |
| Access Switches | 2 |
| Network Printers | 2 |

**Implemented Features**

- Active Directory Integration
- Centralized Authentication
- Staff and Patron Segmentation
- Shared Printing Services
- Cisco Switching Infrastructure
- Enterprise Network Design Principles!

---
## 🛠️ Key Technical Competencies Demonstrated

- **Windows Server Administration:** Core system engineering utilizing advanced configurations of Active Directory Domain Services (AD DS).
- **Group Policy Infrastructure Engineering:** Direct implementation of advanced custom registry controls, interface shielding, and whitelisting.
- **System Hardening & Information Assurance:** Practical deployment of multi-user environment configurations designed against local permission escalation vector sets.
- **Enterprise Network Services Management:** Secure mapping and ACL configuration of core infrastructure services including network share blocks and enterprise printing infrastructures.
- **Cisco Hardware Deployment & Configuration:** Designed architectual network environment to illustrate AD setup from a locigal and physcial prespective. 


