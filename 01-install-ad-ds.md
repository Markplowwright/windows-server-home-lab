# 01 Install the Active Directory Domain Services role

## Goal

Add the Active Directory Domain Services (AD DS) role to the server. This installs the software. The server does not become a domain controller until the next step.

## Steps

1. In Server Manager, choose **Manage > Add Roles and Features**.
2. Pick a role-based installation on this server.
3. On Server Roles, check **Active Directory Domain Services** only, then **Add Features** when prompted. DNS is added later by the promotion wizard, and Remote Access is a different role (VPN and routing) that this lab does not need.
4. Click through Features and the AD DS page.
5. On Confirmation, leave "Restart the destination server automatically if required" unchecked. This role does not need a restart, and an automatic restart could drop the connection.
6. Click **Install** and wait for the progress bar to finish.

## Screenshot

The installation in progress. The wizard lists the role and the management tools it adds: Group Policy Management, the AD DS snap-ins, Active Directory Administrative Center and the Active Directory module for Windows PowerShell.

![Add Roles and Features Wizard showing AD DS installation progress](images/01-ad-ds-install-progress.png)

## What I learned

- A server "role" is a job the server is set up to do. Installing the role and configuring it are two separate steps.
- The install also adds the tools I will use as an admin later: Active Directory Users and Computers, Group Policy Management and the PowerShell module.
- You can close the wizard while it runs. Progress is available from the notifications flag in Server Manager.

## Next

[02 Promote to a domain controller](02-promote-domain-controller.md)
