# Windows Server Home Lab

A hands-on Windows Server and Active Directory lab running in Microsoft Azure. I built it to practice the infrastructure side of IT support that a simulator can't teach: standing up a server, creating a domain, and managing it the way a help desk or junior sysadmin would.

This is a learning project, not production experience. Each step is written up as a short runbook with screenshots, including what went wrong and how I fixed it.

It pairs with my [Admin Practice Lab](https://github.com/Markplowwright/admin-practice-lab), a browser simulator for Microsoft 365, Entra and Intune. The simulator covers the concepts. This lab covers the real servers.

## What is built so far

| Step | What I did | Runbook |
|---|---|---|
| 00 | Created a Windows Server 2025 VM in Azure with cost controls | [00 Create the Azure VM](docs/00-create-azure-vm.md) |
| 01 | Installed the Active Directory Domain Services role | [01 Install AD DS](docs/01-install-ad-ds.md) |
| 02 | Promoted the server to a domain controller | [02 Promote to a domain controller](docs/02-promote-domain-controller.md) |
| 03 | Created a domain user in an OU | [03 Create a domain user](docs/03-create-domain-user.md) |
| 04 | Built six GPOs: password policy, drive mapping, wallpaper, Control Panel restriction, USB block, account lockout | [04 Group Policy objects](docs/04-group-policy-objects.md) |
| 05 | Joined a Windows 11 client to the domain and tested the GPOs with `gpupdate` and `gpresult` | [05 Join a client and test GPOs](docs/05-join-client-and-test-gpos.md) |

## Environment

- **Cloud:** Microsoft Azure (free trial credit)
- **Server:** Windows Server 2025 Datacenter: Azure Edition, 2 vCPUs, 4 GiB RAM
- **Disk:** Standard SSD
- **Access:** Remote Desktop (RDP) from a Mac, limited to my own IP address
- **Cost controls:** auto-shutdown at night, budget alert, stop the VM when not in use

## Roadmap

Planned, not done yet. I will move items up to the table above as I finish them.

- [x] Users created in an OU. OUs and groups (`IT` security group, `DL-ITAdmins` distribution group) exist; write-up for those is pending
- [ ] DNS: read the records the domain creates and test name resolution
- [x] Group Policy: GPOs created and linked (see runbook 04)
- [x] Group Policy testing: client joined, verified with `gpupdate` and `gpresult` (see runbook 05)
- [ ] Fix drive map (real share) and wallpaper GPO, then re-test
- [ ] File server and shared folders with permissions
- [x] Join a Windows client to the domain
- [ ] Windows Server Update Services (WSUS)
- [ ] Backups and a test restore
- [ ] Imaging with Windows Deployment Services and MDT

See the [troubleshooting log](docs/troubleshooting-log.md) for problems I hit along the way.

## Safety notes

All names, passwords and addresses in this repository are for a throwaway lab. Screenshots are cropped or blurred to hide public IP addresses, subscription IDs and credentials.
