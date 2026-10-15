# 02 Promote the server to a domain controller

## Goal

Turn the server into the first domain controller of a new domain, so it holds the directory of users, computers and groups.

## Steps

1. In Server Manager, click the yellow warning flag and choose **Promote this server to a domain controller**.
2. Choose **Add a new forest** and enter a root domain name for the lab (a `.local` name).
3. Keep the default domain controller options (DNS server and Global Catalog). Set a Directory Services Restore Mode (DSRM) password and store it in a password manager.
4. Ignore the warning that a DNS delegation cannot be created. That is expected for a new, private domain.
5. Accept the default NetBIOS name and the default file paths.
6. Run the prerequisites check, then click **Install**.
7. The server restarts on its own. Reconnect after a few minutes and sign in with the domain account (`NETBIOSNAME\username`).

## Check that it worked

In Server Manager, open **Tools** and confirm these appear:

- Active Directory Users and Computers
- DNS
- Group Policy Management

## What I learned

- A forest is the top of an Active Directory setup. A domain lives inside it.
- DNS and AD are tied together: the promotion installs DNS because computers find the domain controller through DNS records.
- The DSRM password is a separate recovery password for the server. It is easy to forget, so it belongs in the password manager.

## Screenshot to add

Server Manager's Tools menu after the restart, showing the three tools above.

## Next

Create organizational units, users and security groups.
