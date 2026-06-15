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

* **Control Plane & Utility Restrictions:**
  * Disabled Access to **Control Panel** and **PC Settings** to prevent environmental tampering.
  * Explicitly disabled the **Command Prompt** (`cmd.exe`) and associated interactive batch script processing.
  * Disabled **Registry Editing Tools** (`regedit.exe`) to prevent low-level configuration modification.
  * Blocked access to the **Task Manager** to intercept unauthorized process termination.
  * Removed and disabled the **Run Command** from the Windows Start Menu layout.
* **Storage & File System Obfuscation:**
  * Enforced **Hide these specified drives in My Computer** (targeted specifically at restricting visibility of the local C: system drive).
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

### ⚙️ Core Configuration Protocols & Controls
* **Subnet Segmentation (VLANs):** Hardened boundaries via Virtual Local Area Networks separating infrastructure traffic across distinct security zones:
  * **VLAN 10 (Public Guest Terminals):** Isolated untrusted clients from adjacent local devices.
  * **VLAN 20 (Staff & Administration):** Secured internal administrative operations.
  * **VLAN 99 (Management Native Layer):** Secure, dedicated segment for telnet/SSH and hardware configuration interfaces.
* **Routing Protocol Mechanics:** Inter-VLAN routing achieved via an optimized **Router-on-a-Stick (RoaS)** topology configuration leveraging sub-interfaces mapped directly to IEEE 802.1Q encapsulation parameters.
* **Perimeter Traffic Controls (ACLs):** Deployed standard and extended IP Access Control Lists (ACLs) to tightly govern inter-VLAN communications, denying public kiosk endpoints any visibility or packet transmission capabilities into the corporate staff network space.
* **Automated Network Services:** Configured centralized DHCP pools directly on the router core to dynamically assign appropriate lease ranges, DNS configurations, and default gateways based on connection ports.

---

## 📸 Part 3: Photo Gallery & Operational Verification Logs

The laboratory build execution, technical checkpoints, and validation behaviors have been comprehensively verified and logged below.

### 🗂️ Section A: Active Directory Infrastructure & Policy Configuration

| Visual Proof Component (Left) | Visual Proof Component (Right) |
| :---: | :---: |
| <img src="https://github.com/user-attachments/assets/af63e638-e03d-45e4-bfa4-d5e8617475eb" width="100%" alt="GCP Compute Provisioning"/><br>**Ref #AD-01:** Provisioning the `lib-dc-2022` Windows Server Compute Instance within the Google Cloud Platform console. | <img src="https://github.com/user-attachments/assets/68962673-88a7-492a-ba0b-806ddc5cb177" width="100%" alt="Command Prompt GPO Lockdown"/><br>**Ref #AD-02:** Group Policy Management Editor enabling interactive Command Prompt execution restrictions. |
| <img src="https://github.com/user-attachments/assets/f09d3e69-b970-4140-87b3-6b39d5ae32ca" width="100%" alt="Registry Editor Block"/><br>**Ref #AD-03:** Enforcing systemic Registry Editing utility blocks under User Configuration Templates. | <img src="https://github.com/user-attachments/assets/cb917a6c-464e-4441-ab46-51630b55dca2" width="100%" alt="Local Drive Visibility Manipulation"/><br>**Ref #AD-04:** Restricting local File Explorer volume navigation to obscure the system `C:` drive. |
| <img src="https://github.com/user-attachments/assets/ae6b7d4c-5bac-416f-9d25-d0b2887c5834" width="100%" alt="Removable Media Restriction"/><br>**Ref #AD-05:** Activating strict Deny-All controls for removable media storage classes to mitigate USB malware risks. | <img src="https://github.com/user-attachments/assets/68d877ff-66de-4ee7-8bcc-6fb9d323ad23" width="100%" alt="OU GPO Association - Users"/><br>**Ref #AD-06:** Linking the hardened Kiosk policy directly to the `Public-Generic-Users` Organizational Unit. |
| <img src="https://github.com/user-attachments/assets/c640c1e9-bd42-4f51-8938-85db69d3484e" width="100%" alt="OU GPO Association - Terminals"/><br>**Ref #AD-07:** Validating GPO inheritance and linkage across the `Public-Kiosks` device container. | <img src="https://github.com/user-attachments/assets/eafd206b-c438-478b-ba90-ed24e1e42ea0" width="100%" alt="Profile Root Directory Structure"/><br>**Ref #AD-08:** Initializing the centralized `Profiles` repository folder structure directly within the local disk file system. |
| <img src="https://github.com/user-attachments/assets/5887ebb0-705a-41b5-a090-f57cbc3996e1" width="100%" alt="Mandatory Architecture Folder Directory"/><br>**Ref #AD-09:** Defining the `Mandatory.v6` profile target template mapping path. | <img src="https://github.com/user-attachments/assets/ed869d36-4899-4717-ae26-0013b72281c8" width="100%" alt="Share Level Access Governance"/><br>**Ref #AD-10:** Assigning Read-Only network sharing security privileges for the `Everyone` logical group. |
| <img src="https://github.com/user-attachments/assets/67f7eeca-887f-4e04-82fe-be274f45a387" width="100%" alt="NTFS Security Configuration Matrix"/><br>**Ref #AD-11:** Overhauling NTFS security inheritance schemas to harden local profile assets from modifications. | <img src="https://github.com/user-attachments/assets/19248eae-e27b-4dbc-a5b0-0ce4d20d4692" width="100%" alt="Advanced Security Permission Trees"/><br>**Ref #AD-12:** Verifying advanced local security entries match strict baseline read-only access objectives. |
| <img src="https://github.com/user-attachments/assets/67f7eeca-887f-4e04-82fe-be274f45a387" width="100%" alt="NTFS Security Configuration Matrix"/><br>**Ref #AD-11:** Overhauling NTFS security inheritance schemas to harden local profile assets from modifications. | <img src="https://github.com/user-attachments/assets/19248eae-e27b-4dbc-a5b0-0ce4d20d4692" width="100%" alt="Advanced Security Permission Trees"/><br>**Ref #AD-12:** Verifying advanced local security entries match strict baseline read-only access objectives. |
| <img src="https://github.com/user-attachments/assets/910f7c2e-e24f-4ddc-8801-684a767a96a7" width="100%" alt="Active Directory Identity Records"/><br>**Ref #AD-13:** Provisioning testing user accounts within the Active Directory Users and Computers management console. | <img src="https://github.com/user-attachments/assets/a13a24b6-8444-4e38-8da2-cf74f0ffcfb2" width="100%" alt="User Network Profile Redirection Pathing"/><br>**Ref #AD-14:** Mapping individual account profile path variables to the explicit mandatory network profile distribution path. |
| <img src="https://github.com/user-attachments/assets/9b1a8a7f-5d4b-49a5-bce8-0cabd73fda0f" width="100%" alt="Account Profile Attribute Mapping"/><br>**Ref #AD-15:** Finalizing credential initialization and profile environment properties for target validation testing accounts. | <img src="https://github.com/user-attachments/assets/6b520369-9d2e-407b-bd6a-3d69bec5c825" width="100%" alt="Client System Modification Enforcements"/><br>**Ref #AD-16:** Operational endpoint confirmation showing systemic registry restriction blocks are active on a guest session. |
| <img src="https://github.com/user-attachments/assets/50dad029-8e1c-44b2-94d7-18d8ff434b59" width="100%" alt="Client Shell Restriction Enforcements"/><br>**Ref #AD-17:** Endpoint validation testing confirming successful execution inhibition of the command line interface from a kiosk account. | <img src="https://github.com/user-attachments/assets/72d1227d-319d-4332-8bfb-50bbb77b14c3" width="100%" alt="Domain Controller Policy Checkpoint"/><br>**Ref #AD-18:** Reviewing applied kiosk policy structure and user configuration containers on the primary controller. |
| <img src="https://github.com/user-attachments/assets/dc030e2e-bece-4d9b-a1ee-e62979fa5442" width="100%" alt="Group Policy Replication Status"/><br>**Ref #AD-19:** Validating domain-wide Group Policy propagation metrics and path linkages across the Active Directory structure. | <img src="https://github.com/user-attachments/assets/91eca108-5287-49ed-b31c-ecae7c9537ac" width="100%" alt="Security Filtering Access Check"/><br>**Ref #AD-20:** Auditing Security Filtering criteria inside the GPMC interface to confirm targeted execution scopes. |
| <img src="https://github.com/user-attachments/assets/a6bca630-2ea6-4500-b999-28b7ae3d310f" width="100%" alt="Mandatory Hive Validation Logs"/><br>**Ref #AD-21:** Reviewing user environment parameters to ensure read-only baseline mandatory profiles deploy flawlessly. | <img src="https://github.com/user-attachments/assets/3d5f83f8-9c02-40f3-8700-0eb1519d7a24" width="100%" alt="Kiosk Lockdown Final Audit"/><br>**Ref #AD-22:** Final GPO validation audit verifying total restriction and endpoint compliance across the public kiosk user layer. |
