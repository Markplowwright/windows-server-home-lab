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

## Environment

- **Cloud:** Microsoft Azure (free trial credit)
- **Server:** Windows Server 2025 Datacenter: Azure Edition, 2 vCPUs, 4 GiB RAM
- **Disk:** Standard SSD
- **Access:** Remote Desktop (RDP) from a Mac, limited to my own IP address
- **Cost controls:** auto-shutdown at night, budget alert, stop the VM when not in use

## Roadmap

Planned, not done yet. I will move items up to the table above as I finish them.

- [ ] Organizational units, users and security groups (the same ideas as my simulator, on a real domain)
- [ ] DNS: read the records the domain creates and test name resolution
- [ ] Group Policy: create a real GPO, link it to an OU, test with `gpupdate` and `gpresult`
- [ ] File server and shared folders with permissions
- [ ] Join a Windows client to the domain
- [ ] Windows Server Update Services (WSUS)
- [ ] Backups and a test restore
- [ ] Imaging with Windows Deployment Services and MDT

See the [troubleshooting log](docs/troubleshooting-log.md) for problems I hit along the way.

## Safety notes

All names, passwords and addresses in this repository are for a throwaway lab. Screenshots are cropped or blurred to hide public IP addresses, subscription IDs and credentials.
