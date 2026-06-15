








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
      <img src="Screenshot 2026-06-11 at 10.09.48 PM.jpg" alt="Removable Media Restriction" width="100%"/>
      <br/><b>Ref #AD-05:</b> Activating strict Deny-All controls for removable media storage classes to mitigate USB malware risks.
    </td>
    <td width="50%" align="center">
      <img src="Screenshot 2026-06-11 at 10.12.37 PM.jpg" alt="OU GPO Association - Users" width="100%"/>
      <br/><b>Ref #AD-06:</b> Linking the hardened Kiosk policy directly to the <code>Public-Generic-Users</code> Organizational Unit.
    </td>
  </tr>
  <tr>
    <td width="50%" align="center">
      <img src="Screenshot 2026-06-11 at 10.12.40 PM.jpg" alt="OU GPO Association - Terminals" width="100%"/>
      <br/><b>Ref #AD-07:</b> Validating GPO inheritance and linkage across the <code>Public-Kiosks</code> device container.
    </td>
    <td width="50%" align="center">
      <img src="Screenshot 2026-06-11 at 10.17.38 PM.jpg" alt="Profile Root Directory Structure" width="100%"/>
      <br/><b>Ref #AD-08:</b> Initializing the centralized <code>Profiles</code> repository folder structure directly within the local disk file system.
    </td>
  </tr>
  <tr>
    <td width="50%" align="center">
      <img src="Screenshot 2026-06-11 at 10.18.31 PM.jpg" alt="Mandatory Architecture Folder Directory" width="100%"/>
      <br/><b>Ref #AD-09:</b> Defining the <code>Mandatory.v6</code> profile target template mapping path.
    </td>
    <td width="50%" align="center">
      <img src="Screenshot 2026-06-11 at 10.20.31 PM.jpg" alt="Share Level Access Governance" width="100%"/>
      <br/><b>Ref #AD-10:</b> Assigning Read-Only network sharing security privileges for the <code>Everyone</code> logical group.
    </td>
  </tr>
  <tr>
    <td width="50%" align="center">
      <img src="Screenshot 2026-06-11 at 10.27.33 PM.jpg" alt="NTFS Security Configuration Matrix" width="100%"/>
      <br/><b>Ref #AD-11:</b> Overhauling NTFS security inheritance schemas to harden local profile assets from modifications.
    </td>
    <td width="50%" align="center">
      <img src="Screenshot 2026-06-11 at 10.28.25 PM.jpg" alt="Advanced Security Permission Trees" width="100%"/>
      <br/><b>Ref #AD-12:</b> Verifying advanced local security entries match strict baseline read-only access objectives.
    </td>
  </tr>
  <tr>
    <td width="50%" align="center">
      <img src="Screenshot 2026-06-11 at 10.43.59 PM.jpg" alt="Active Directory Identity Records" width="100%"/>
      <br/><b>Ref #AD-13:</b> Provisioning testing user accounts within the Active Directory Users and Computers management console.
    </td>
    <td width="50%" align="center">
      <img src="Screenshot 2026-06-11 at 10.45.40 PM.jpg" alt="User Network Profile Redirection Pathing" width="100%"/>
      <br/><b>Ref #AD-14:</b> Mapping the individual account profile path variables to the explicit network profile network distribution path.
    </td>
  </tr>
  <tr>
    <td width="50%" align="center">
      <img src="Screenshot 2026-06-11 at 10.45.54 PM.jpg" alt="Account Profile Attribute Mapping" width="100%"/>
      <br/><b>Ref #AD-15:</b> Finalizing credential initialization parameters for target validation testing accounts.
    </td>
    <td width="50%" align="center">
      <img src="Screenshot 2026-06-11 at 11.18.49 PM.jpg" alt="Client System Modification Enforcement Validation" width="100%"/>
      <br/><b>Ref #AD-16:</b> Operational confirmation demonstrating systemic failure alert when guest accounts attempt unauthorized Registry Tool access.
    </td>
  </tr>
  <tr>
    <td width="50%" align="center">
      <img src="Screenshot 2026-06-11 at 11.19.20 PM.jpg" alt="Client Shell Restriction Enforcement Validation" width="100%"/>
      <br/><b>Ref #AD-17:</b> Verification testing confirming the successful execution inhibition of the command line interface from a restricted endpoint session.
    </td>
    <td width="50%" align="center">
      <img src="Screenshot 2026-06-12 at 12.12.10 AM.jpg" alt="Exposing Guarded Operating System Structures" width="100%"/>
      <br/><b>Ref #AD-18:</b> Modifying folder presentation layouts to unhide protected operating system assets.
    </td>
  </tr>
  <tr>
    <td width="50%" align="center">
      <img src="Screenshot 2026-06-12 at 12.13.43 AM.jpg" alt="Locating Hidden Hive Targets" width="100%"/>
      <br/><b>Ref #AD-19:</b> Identifying the hidden user registry hive footprint within the staging space directory.
    </td>
    <td width="50%" align="center">
      <img src="Screenshot 2026-06-12 at 12.16.35 AM.jpg" alt="Hive Extension Transformation Stage" width="100%"/>
      <br/><b>Ref #AD-20:</b> Mutating the user runtime profile hive structural classification from a mutable state file to a read-only mandatory asset file extension (<code>.man</code>).
    </td>
  </tr>
  <tr>
    <td width="50%" align="center">
      <img src="Screenshot 2026-06-12 at 8.11.03 PM.jpg" alt="GPO Construction - Staff Profiles" width="100%"/>
      <br/><b>Ref #AD-21:</b> Provisioning a new dedicated Group Policy structural object framework for internal Staff data management.
    </td>
    <td width="50%" align="center">
      <img src="Screenshot 2026-06-12 at 8.11.11 PM.jpg" alt="GPO Link Verification" width="100%"/>
      <br/><b>Ref #AD-22:</b> Mapping the administrative data optimization policies to the specialized Staff-Accounts structural OU.
    </td>
  </tr>
  <tr>
    <td width="50%" align="center">
      <img src="Screenshot 2026-06-12 at 8.15.37 PM.jpg" alt="Security Filtering Optimization Scopes" width="100%"/>
      <br/><b>Ref #AD-23:</b> Applying GPO Security Filtering parameters to exclusively constrain policy application to staff personnel identities.
    </td>
    <td width="50%" align="center">
      <img src="Screenshot 2026-06-12 at 8.16.46 PM.jpg" alt="Delegated Authority Evaluation Matrices" width="100%"/>
      <br/><b>Ref #AD-24:</b> Fine-tuning permissions criteria within the GPMC scope interface to allow appropriate domain read access parameters.
    </td>
  </tr>
  <tr>
    <td width="50%" align="center">
      <img src="Screenshot 2026-06-12 at 8.19.18 PM.jpg" alt="Shell Directory Redirection Configurations" width="100%"/>
      <br/><b>Ref #AD-25:</b> Modifying user environment parameters to intercept Desktop and Document storage calls and transparently guide them to centralized network resources.
    </td>
    <td width="50%" align="center">
      <img src="Screenshot 2026-06-12 at 8.40.53 PM.jpg" alt="Documents Storage Redirection Target Configurations" width="100%"/>
      <br/><b>Ref #AD-26:</b> Establishing absolute network UNC pathways for centralized Documents folder synchronization mappings.
    </td>
  </tr>
  <tr>
    <td width="50%" align="center">
      <img src="Screenshot 2026-06-12 at 8.41.00 PM.jpg" alt="Data Preservation Target Specifications" width="100%"/>
      <br/><b>Ref #AD-27:</b> Verifying redirection behaviors to automatically move legacy content objects smoothly into secure server shares.
    </td>
    <td width="50%" align="center">
      <img src="Screenshot 2026-06-12 at 8.42.20 PM.jpg" alt="Desktop Interface Redirection Mapping Specifications" width="100%"/>
      <br/><b>Ref #AD-28:</b> Linking user Desktop workspace structures to centralized storage areas using targeted UNC routing methods.
    </td>
  </tr>
</table>

### 🎫 Section B: ITSM Incident Documentation & Verification Traces
Demonstrates real-world ticket handling workflows utilizing an enterprise ticketing platform to track infrastructure hardening deployments from authorization requests through production testing verification signs.

<table width="100%">
  <tr>
    <td width="50%" align="center">
      <img src="Screenshot 2026-06-12 at 6.43.50 PM.jpg" alt="ITSM Incident Record Creation" width="100%"/>
      <br/><b>Ref #ITSM-01:</b> Logging Ticket #1 inside the Spiceworks Cloud Helpdesk to address vulnerability exposure on the unsecured kiosk devices.
    </td>
    <td width="50%" align="center">
      <img src="Screenshot 2026-06-12 at 6.48.36 PM.jpg" alt="ITSM Incident Progress Tracking Updates" width="100%"/>
      <br/><b>Ref #ITSM-02:</b> Mapping out remediation architectures inside the engineering task workflow notes.
    </td>
  </tr>
  <tr>
    <td width="50%" align="center">
      <img src="Screenshot 2026-06-12 at 6.49.50 PM.jpg" alt="ITSM Action Execution Auditing Updates" width="100%"/>
      <br/><b>Ref #ITSM-03:</b> Notifying domain stakeholders of planned active system transformations across the kiosk environments.
    </td>
    <td width="50%" align="center">
      <img src="Screenshot 2026-06-12 at 6.53.57 PM.jpg" alt="ITSM Mandatory Profile Validation Log entries" width="100%"/>
      <br/><b>Ref #ITSM-04:</b> Registering proof-of-concept testing details within technical change management fields.
    </td>
  </tr>
  <tr>
    <td width="50%" align="center">
      <img src="Screenshot 2026-06-12 at 6.55.28 PM.jpg" alt="ITSM Security Task Validation & Close" width="100%"/>
      <br/><b>Ref #ITSM-05:</b> Formally verifying remediation steps and closing out Ticket #1 with a complete summary audit trail.
    </td>
    <td width="50%" align="center">
      <img src="Screenshot 2026-06-12 at 12.21.54 AM.jpg" alt="ITSM Staff Case Ingestion Stages" width="100%"/>
      <br/><b>Ref #ITSM-06:</b> Initiating Ticket #2 within Spiceworks to address vulnerabilities associated with local workspace data preservation issues on staff assets.
    </td>
  </tr>
  <tr>
    <td width="50%" align="center">
      <img src="Screenshot 2026-06-12 at 12.27.27 AM.jpg" alt="ITSM Engineering Assignment Updates" width="100%"/>
      <br/><b>Ref #ITSM-07:</b> Updating infrastructure project boards to reflect active work states for staff folder redirection mechanics.
    </td>
    <td width="50%" align="center">
      <img src="Screenshot 2026-06-12 at 8.46.23 PM.jpg" alt="ITSM Staff System Deployment Resolution Closures" width="100%"/>
      <br/><b>Ref #ITSM-08:</b> Resolving Ticket #2 to document full GPO compliance and active network file tracking for staff profiles.
    </td>
  </tr>
</table>

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
