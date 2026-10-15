# 00 Create the Azure VM

## Goal

Get a Windows Server I can practice on without buying hardware, and keep the cost near zero.

## Why a cloud VM

My laptop has an Apple chip (ARM), and Windows Server images are built for Intel/AMD (x86). Emulating x86 on it works but is slow. A small Azure VM avoids that, and connecting to it over Remote Desktop is how servers are managed in real jobs anyway.

## Settings I chose

| Setting | Choice | Why |
|---|---|---|
| Image | Windows Server 2025 Datacenter: Azure Edition (x64 Gen2) | Current version, same AD, DNS and Group Policy tools as 2022 |
| Size | 2 vCPUs, 4 GiB RAM | Enough for a domain controller in a lab |
| OS disk | Standard SSD | Cheaper than Premium and fast enough |
| Delete with VM | OS disk, public IP and NIC | Nothing left behind to bill me |
| Inbound ports | RDP (3389) only | The one port I need |
| Auto-shutdown | On, at 10 PM Eastern | Protects against forgetting to stop it |
| Backup, Site Recovery, alerts | Off | Extra cost the lab does not need |

## Steps

1. In the Azure portal, create a virtual machine in a new resource group.
2. Choose the image, size and an administrator account with a long password stored in a password manager.
3. Allow RDP only. After creation, edit the inbound rule so the source is **My IP address** instead of any address.
4. Set the OS disk to Standard SSD.
5. Turn on auto-shutdown on the Management tab.
6. Review the price on the last screen (about 20 cents per hour at the size I chose), then create the VM.
7. Set a budget alert in Cost Management.
8. Connect with the Windows App for Mac using the VM's public IP address.

## Cost rules I follow

- Stop the VM from the Azure portal when I am done. The status must read **Stopped (deallocated)**. Shutting down inside Windows does not stop billing.
- Check Cost Management every day for the first week.
- Delete the whole resource group when the project ends.

## What I learned

- A VM is a computer in a data center. Remote Desktop is just a window into it.
- A public IP is needed to reach the VM from home, so the real protection is limiting the RDP rule to my own address.
- The first size Azure suggested was unavailable on my subscription, so I had to pick another size in the list.
