


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

<table width="100%">
  <tr>
    <td width="50%" align="center">
      <img src="https://github.com/user-attachments/assets/af63e638-e03d-45e4-bfa4-d5e8617475eb" alt="GCP Compute Provisioning" width="100%"/>
      <br/><b>Ref #AD-01:</b> Provisioning the <code>lib-dc-2022</code> Windows Server Compute Instance within the Google Cloud Platform console.
    </td>
    <td width="50%" align="center">
      <img src="https://github.com/user-attachments/assets/68962673-88a7-492a-ba0b-806ddc5cb177" alt="Command Prompt GPO Lockdown" width="100%"/>
      <br/><b>Ref #AD-02:</b> Group Policy Management Editor enabling interactive Command Prompt execution restrictions.
    </td>
  </tr>
  <tr>
    <td width="50%" align="center">
      <img src="https://github.com/user-attachments/assets/f09d3e69-b970-4140-87b3-6b39d5ae32ca" alt="Registry Editor Block" width="100%"/>
      <br/><b>Ref #AD-03:</b> Enforcing systemic Registry Editing utility blocks under User Configuration Templates.
    </td>
    <td width="50%" align="center">
      <img src="https://github.com/user-attachments/assets/cb917a6c-464e-4441-ab46-51630b55dca2" alt="Local Drive Visibility Manipulation" width="100%"/>
      <br/><b>Ref #AD-04:</b> Restricting local File Explorer volume navigation to obscure the system <code>C:</code> drive.
    </td>
  </tr>
  <tr>
    <td width="50%" align="center">
      <img src="https://github.com/user-attachments/assets/ae6b7d4c-5bac-416f-9d25-d0b2887c5834" alt="Removable Media Restriction" width="100%"/>
      <br/><b>Ref #AD-05:</b> Activating strict Deny-All controls for removable media storage classes to mitigate USB malware risks.
    </td>
    <td width="50%" align="center">
      <img src="https://github.com/user-attachments/assets/68d877ff-66de-4ee7-8bcc-6fb9d323ad23" alt="OU GPO Association - Users" width="100%"/>
      <br/><b>Ref #AD-06:</b> Linking the hardened Kiosk policy directly to the <code>Public-Generic-Users</code> Organizational Unit.
    </td>
  </tr>
  <tr>
    <td width="50%" align="center">
      <img src="https://github.com/user-attachments/assets/c640c1e9-bd42-4f51-8938-85db69d3484e" alt="OU GPO Association - Terminals" width="100%"/>
      <br/><b>Ref #AD-07:</b> Validating GPO inheritance and linkage across the <code>Public-Kiosks</code> device container.
    </td>
    <td width="50%" align="center">
      <img src="https://github.com/user-attachments/assets/eafd206b-c438-478b-ba90-ed24e1e42ea0" alt="Profile Root Directory Structure" width="100%"/>
      <br/><b>Ref #AD-08:</b> Initializing the centralized <code>Profiles</code> repository folder structure directly within the local disk file system.
    </td>
  </tr>
  <tr>
    <td width="50%" align="center">
      <img src="https://github.com/user-attachments/assets/5887ebb0-705a-41b5-a090-f57cbc3996e1" alt="Mandatory Architecture Folder Directory" width="100%"/>
      <br/><b>Ref #AD-09:</b> Defining the <code>Mandatory.v6</code> profile target template mapping path.
    </td>
    <td width="50%" align="center">
      <img src="https://github.com/user-attachments/assets/ed869d36-4899-4717-ae26-0013b72281c8" alt="Share Level Access Governance" width="100%"/>
      <br/><b>Ref #AD-10:</b> Assigning Read-Only network sharing security privileges for the <code>Everyone</code> logical group.
    </td>
  </tr>
  <tr>
    <td width="50%" align="center">
      <img src="https://github.com/user-attachments/assets/67f7eeca-887f-4e04-82fe-be274f45a387" alt="NTFS Security Configuration Matrix" width="100%"/>
      <br/><b>Ref #AD-11:</b> Overhauling NTFS security inheritance schemas to harden local profile assets from modifications.
    </td>
    <td width="50%" align="center">
      <img src="https://github.com/user-attachments/assets/19248eae-e27b-4dbc-a5b0-0ce4d20d4692" alt="Advanced Security Permission Trees" width="100%"/>
      <br/><b>Ref #AD-12:</b> Verifying advanced local security entries match strict baseline read-only access objectives.
   
  


### 📡 Section C: Cisco Networking Topology Visualizations

*(Note: Upload and link your Cisco Packet Tracer / Visio export diagrams inside this section to complete the infrastructure documentation map).*

<table width="100%">
  <tr>
    <td width="100%" align="center">
      <img src="/api/placeholder/800/450" alt="Cisco Packet Tracer Logical Topology Mapping Placeholder"/>
      <br/><b>Ref #NET-01:</b> Detailed logical network layout demonstrating Inter-VLAN routing, interface assignments, trunk configuration paths, and network boundaries.
    </td>
  </tr>
</table>
