# Next stage steps

**Status: planned, none of this is done yet.** This page is my build queue for the lab. Each item says what it teaches, why a school IT team cares, and the exact steps I will follow. When I finish one I will write a numbered runbook, add screenshots, and tick it off in the [README](../README.md) roadmap.

Suggested order (quickest wins first):

1. Prerequisites: an `All Employees` group and a test user
2. Service account and a single-purpose display PC
3. NTFS and share permissions deep dive
4. Install software by Group Policy (MSI)
5. WSUS
6. MDT deployment share, then WDS/PXE
7. Backups and a test restore
8. Everyday school AD tasks to practice

---

## 1. Prerequisites: group and test user

**Why:** other labs need a normal (non-admin) user and a group to target.

Run on `PlowVM`:

```powershell
New-ADGroup -Name "All Employees" -GroupScope Global -GroupCategory Security -Path "OU=Users,OU=USA,DC=plowlab,DC=local"
New-ADUser -Name "Test Employee" -SamAccountName temployee -UserPrincipalName temployee@plowlab.local -Path "OU=Users,OU=USA,DC=plowlab,DC=local" -AccountPassword (Read-Host -AsSecureString "Password") -Enabled $true
Add-ADGroupMember -Identity "All Employees" -Members temployee
Get-ADGroupMember "All Employees"
```

To sign in to the client over RDP as this user, add them to Remote Desktop Users on `PLOWCLIENT`:

```
net localgroup "Remote Desktop Users" "PLOWLAB\temployee" /add
```

Then test the drive map and Control Panel GPOs as a normal user.

---

## 2. Service account and a single-purpose display PC

**What it is:** a service account is an AD account used by software or a machine, not a person. Examples in a school: MDT joining computers to the domain, backup jobs, scripts, a web filter reading users, or a hallway display PC.

**Good practice:** least privilege, never Domain Admin, own OU, long password, documented owner, restricted logon.

**Goal (from the video activity):** a PC that signs in automatically, opens one web page full screen, stays on, and uses an account that can only log on to that one PC. Not kiosk mode.

Steps:

1. Create an OU and the account on `PlowVM`:
   ```powershell
   New-ADOrganizationalUnit -Name "Service Accounts" -Path "OU=USA,DC=plowlab,DC=local"
   New-ADUser -Name "svc-display" -SamAccountName svc-display -UserPrincipalName svc-display@plowlab.local -Path "OU=Service Accounts,OU=USA,DC=plowlab,DC=local" -AccountPassword (Read-Host -AsSecureString "Password") -Enabled $true -PasswordNeverExpires $true -CannotChangePassword $true
   ```
   A separate OU keeps the user GPOs (linked to `USA > Users`) from hitting it.
2. Restrict where it can log on:
   ```powershell
   Set-ADUser svc-display -LogonWorkstations "PLOWCLIENT"
   Get-ADUser svc-display -Properties LogonWorkstations
   ```
3. On `PLOWCLIENT`, allow RDP for setup and sign in once to create its profile:
   ```
   net localgroup "Remote Desktop Users" "PLOWLAB\svc-display" /add
   ```
4. While signed in as `svc-display`, open `shell:startup` and create a shortcut with this target (full screen, not kiosk):
   ```
   "C:\Program Files\Google\Chrome\Application\chrome.exe" --start-fullscreen https://www.bing.com
   ```
5. Turn on auto-login on `PLOWCLIENT` (admin PowerShell):
   ```powershell
   $p = "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon"
   Set-ItemProperty $p AutoAdminLogon "1"
   Set-ItemProperty $p DefaultUserName "svc-display"
   Set-ItemProperty $p DefaultDomainName "PLOWLAB"
   Set-ItemProperty $p DefaultPassword "<password>"
   ```
   **Security note:** `DefaultPassword` is stored in plain text. In production use the Sysinternals **Autologon** tool, which stores it encrypted.
6. Keep it on:
   ```
   powercfg /change standby-timeout-ac 0
   powercfg /change monitor-timeout-ac 0
   powercfg /change hibernate-timeout-ac 0
   ```
7. Restart and verify (Azure boot diagnostics screenshot, or RDP as `svc-display`).

**Lab limits:** Azure VMs are billed while running and I stop them, so "always on" is only a setting here. The logon restriction is proven by `Get-ADUser`, unless I have a second domain PC to try.

---

## 3. NTFS and share permissions deep dive

**Why:** permission problems are one of the most common school IT tickets ("I can't open the folder," "a teacher can see a folder they shouldn't").

Concepts to learn and demonstrate:

| Topic | What to know |
|---|---|
| Local vs network sharing | Local = a user signed in at the PC; network = access through `\\server\share`. |
| Domain-based sharing | Permissions are given to domain groups, not individual people. |
| Public folder sharing | A folder everyone can read (for example a school-wide forms folder), with write limited to staff. |
| Share permissions | Read, Change, Full Control. Apply only over the network. |
| NTFS permissions | Read, Write, Modify, Read & execute, Full control. Apply locally and over the network. |
| Combining them | The **most restrictive** of share and NTFS wins. Common practice: share = Everyone/Authenticated Users Full control, then control access with NTFS groups. |
| Allow vs Deny | Deny overrides Allow. Avoid Deny unless needed. |
| Inheritance | Child folders inherit from the parent. Break inheritance only on purpose. |
| Effective access | Properties > Security > Advanced > **Effective Access** tab shows what a given user actually gets. |
| Access-based enumeration (ABE) | Users only **see** folders they have permission to open. Turn it on in the share's advanced settings or Server Manager > File and Storage Services > Shares. |
| AGDLP | Accounts go in Global groups, which go in Domain Local groups, which get Permissions. |
| Ownership | The owner can always change permissions; know how to take ownership. |

Lab plan on `C:\SHARED`:

1. Create subfolders: `IT`, `Public`, `Teachers`, `Students`.
2. Create groups `GG-IT`, `GG-Teachers`, `GG-Students` (global) and domain-local groups such as `DL-IT-Modify`.
3. Set the share to Authenticated Users Full control. Turn off inheritance on `IT` and `Teachers` and grant only the matching group Modify.
4. Give `Public` Read to Domain Users and Modify to `GG-Teachers`.
5. Sign in as users from different groups. Use the **Effective Access** tab and real access tests to prove it.
6. Turn on **access-based enumeration** and confirm that a student no longer sees `IT` or `Teachers`.
7. Re-run the FSRM quota and file screen tests from client machines (still outstanding from runbook 07).
8. Also fix the share permission issue from runbook 06 (Domain Users had Read only, which blocks writes).

Troubleshooting commands to practice: `icacls C:\SHARED\IT`, `Get-SmbShare`, `Get-SmbShareAccess`, `net use`, `whoami /groups`.

---

## 4. Install software by Group Policy (MSI)

**Why:** schools need the same software on many machines without visiting each one.

Steps:

1. Download an MSI (for example the Chrome enterprise MSI) and place it in a software share, with Read for Domain Computers.
2. Move `PLOWCLIENT` into `USA > Computer` (an open item from runbook 04).
3. New GPO > Computer Configuration > Policies > Software Settings > Software Installation > New > Package. Choose the UNC path (`\\PLOWVM\...`, not a local path). Set Assigned.
4. Link the GPO to `USA > Computer`.
5. Reboot `PLOWCLIENT` and confirm the install. Check with `gpresult /r` and the Application event log.

Know the limits: assigned MSI packages install at startup, only MSI works with this method, and larger environments use Intune or SCCM instead.

---

## 5. WSUS

**Why:** the job involves maintaining updates and patches. WSUS lets IT download updates once and approve them before computers get them.

Steps on `PlowVM` (needs disk space; limit products and classifications to keep it small):

1. Add the Windows Server Update Services role. Choose WID database and a content folder.
2. Run the post-install configuration and choose a few product categories (for example Windows 11) and Critical/Security updates only.
3. Create computer groups: `Test`, `Staff`, `Labs`.
4. Create a GPO linked to `USA > Computer` with **Specify intranet Microsoft update service location** pointing to `http://PLOWVM:8530`, and enable client-side targeting.
5. On `PLOWCLIENT`, `gpupdate /force`, then `usoclient StartScan` and confirm it appears in the WSUS console.
6. Approve an update for `Test` first, then widen to everyone.

Practice questions: how do you test updates before rolling them out, and how do you fix a PC that is not reporting?

---

## 6. MDT and PXE imaging

**Why:** the role includes imaging computers with PXE, MDT and Microsoft provisioning tools.

Key terms:

- **PXE:** boot from the network card to start an install.
- **WDS:** the server role that answers PXE requests and serves the boot image.
- **MDT:** builds the deployment share, drivers, apps and task sequences that automate the install.
- **Autopilot/Intune:** cloud-based provisioning for new devices, an alternative to imaging.

Part A, which I can do in the Azure lab (MDT console only):

1. Install the Windows ADK and the WinPE add-on, then MDT, on `PlowVM`.
2. Create a deployment share (`C:\DeploymentShare`).
3. Import an operating system from a Windows ISO, add a sample app and driver folder.
4. Create a standard client task sequence.
5. Update the deployment share to generate boot images.

Part B, which needs a different environment:

- PXE needs DHCP broadcast, and Azure virtual networks do not pass it. Real imaging requires Hyper-V or VirtualBox on a local PC with enough RAM, or nested virtualization on a larger Azure VM.
- There: add the WDS role, add the MDT boot image, then PXE boot a blank VM and run the task sequence.

I will not claim I can image a machine until Part B is done.

---

## 7. Backups and a test restore

- 3-2-1 rule: 3 copies, 2 media types, 1 off-site.
- Full, incremental, differential.
- Install Windows Server Backup, back up `C:\SHARED`, delete a file, and restore it. Also practice **Previous Versions** (shadow copies) on the share.
- A backup that has never been test-restored is not a backup.

---

## 8. Everyday school AD tasks to practice

- Reset a password and unlock an account
- Create a student or staff user in the right OU, add to groups
- Offboard a user (disable, remove from groups, move to a Disabled OU)
- Move a computer to another OU and run `gpupdate`
- Find stale computer accounts (`Search-ADAccount -ComputersOnly -AccountInactive`)
- Bulk create users from a CSV (test with two rows first)
- Map drives and printers by security group (item-level targeting)
- Fix a broken domain trust (`Test-ComputerSecureChannel -Repair`)
- Use `gpresult`, `nslookup`, `dsregcmd /status`, `whoami /groups` for troubleshooting
- Later: Microsoft 365 admin center tasks, Intune enrollment, VoIP and A/V basics, inventory tracking
