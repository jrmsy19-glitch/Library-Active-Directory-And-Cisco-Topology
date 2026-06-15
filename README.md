# 🌐 Enterprise Infrastructure Lab: Active Directory Hardening & Cisco Network Topology

## 📌 Project Overview
This laboratory demonstrates the design, deployment, and testing of a dual-segmented enterprise network architecture modeled for a public library system. The project is split into two foundational operational pillars:
1. **Identity & Access Management (IAM):** A heavily hardened Microsoft Active Directory environment hosted on Google Cloud Platform (GCP) utilizing strict Group Policy Objects (GPOs) to manage high-turnover public terminals and secure administrative staff workflows.
2. **Network Infrastructure Architecture:** A resilient Cisco switching and routing topology providing secure VLAN segmentation, dynamic addressing, routing protocols, and perimeter access control layers.

---

## 🛡️ Part 1: Microsoft Active Directory Configuration & System Hardening

### 🏗️ Directory Architecture & Organizational Unit (OU) Topology
The domain infrastructure is built upon the **`library.lan`** namespace managed by a virtualized Domain Controller running **Windows Server 2022 Datacenter** (`lib-dc-2022`). To manage assets efficiently and prevent policy bloat, resources were structured into a dedicated hierarchy under the `Public-Library-Root` root container:

| Organizational Unit (OU) | Description / Scoped Resources |
| :--- | :--- |
| **`Network-Printers`** | Houses active print objects including `Public Printer` and `Staff Printer`. |
| **`Public-Kiosks`** | Computer accounts for all open-access public terminal hardware. |
| **`Public-Generic-Users`**| Container for the 20 pre-provisioned guest accounts (`pkiosk1` through `pkiosk20`). |
| **`Staff-Accounts`** | Identity assets for library personnel with elevated operational needs. |
| **`Staff-Workstations`** | Secure computer endpoints restricted to authorized staff personnel usage. |

---

### 🔒 Policy Implementation Matrices & Endpoint Hardening
System behavior is dictated by granular Group Policy Objects designed to fulfill precise compliance mandates across user classifications.

#### 🏢 1. Guest Terminal Hardening & Session Isolation Policy
Applied directly to the `Public-Kiosks` and `Public-Generic-Users` OUs to completely lock down the operating system footprint:

* **Control Plane & Utility Restrictions:** * Disabled Access to **Control Panel** and **PC Settings** to prevent environmental tampering.
  * Explicitly disabled the **Command Prompt** (`cmd.exe`) and associated interactive batch script processing.
  * Disabled **Registry Editing Tools** (`regedit.exe`) to prevent low-level configuration modification.
  * Blocked access to the **Task Manager** to intercept unauthorized process termination.
  * Removed and disabled the **Run Command** from the Windows Start Menu layout.
* **Storage & File System Obfuscation:**
  * Enabled **Hide these specified drives in My Computer** (targeted specifically at restricting visibility of the local $C:$ system drive).
  * Enforced **All Removable Storage classes: Deny all access** to eliminate malware vectors via physical USB drives and mitigate unauthorized local data extraction.
* **Session Lifecycle Control:**
  * Active screensaver deployment paired with a rigid **Screen Timeout lock (10 minutes)** to protect unattended terminals.
  * Network security filtering implemented via explicit **URL Restriction and Allowance listings** inside browser configuration templates.

#### 👥 2. High-Turnover Mandated Profile Architecture
To guarantee a zero-persistence terminal behavior across sessions, standard roaming profiles were structurally engineered out of the environment:
* **Centralized Distribution Share:** Provisioned a network-accessible data share at `\\lib-dc-2022\Profiles\`.
* **Access Control Lists (ACLs):** Configured permissive Share boundaries alongside restrictive **NTFS Security Permissions** granting `Everyone` explicit **Read-Only** capabilities, blocking guest validation profiles from uploading localized changes back to the root deployment directory.
* **Registry Hive Mutation:** Manually exposed hidden operating system data structures to rename the user baseline configuration hive from **`NTUSER.DAT`** to **`NTUSER.MAN`** (Mandatory Profile configuration). This forces Windows to drop all cache files and session logs instantly upon user logoff.

#### 👔 3. Corporate Staff & Personnel Management Policy
Applied strictly to the `Staff-Accounts` security group utilizing Group Policy **Security Filtering**:
* **Enterprise Folder Redirection:** Configured active target redirection for shell directories including **Desktop** and **Documents** to centralize staff work footprints directly to `\\lib-dc-2022\Staff_Data\`.
* **Temporal Access Control (Logon Hours):** Bound staff interaction windows strictly to operational hours (**Monday through Saturday, 9:00 AM to 8:00 PM**). 
* **Session Expired Disconnection:** Enforced the GPO security flag: *Disconnect network clients when logon hours expire*, mitigating unauthorized after-hours facility access risks.
* **Identity Lifecyle Enforcement:** Configured new object profile defaults requiring that the **User must change password at next logon** to guarantee absolute credential privacy during provisioning phases.

---

## ⚡ Part 2: Cisco Networking & Topology Configuration

### 🌐 Network Core Layout
The underlying network layer utilizes a secure, hierarchical Cisco architecture designed to maintain strict traffic boundaries between unvetted public data and sensitive library enterprise communications.
