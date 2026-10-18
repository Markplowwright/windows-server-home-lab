# Troubleshooting log

Real problems from the lab, with the cause and the fix. Newest at the bottom.

| Date | Problem | Cause | Fix |
|---|---|---|---|
| 2026-10-15 | Azure VM size showed "NotAvailableForSubscription" | The default size is not offered on a new free-trial subscription in that region | Opened the full size list and picked a size that was not greyed out |
| 2026-10-15 | Create-VM form showed SSH key settings | The image was still the default Linux one | Switched the image to Windows Server, which changes sign-in to username and password |
| 2026-10-17 | Forgot the sign-in password for the lab VM | Azure does not store the VM password, and the portal's Reset password blade did not apply the change | Used **Run command > RunPowerShellScript** on the VM to set a new password with `net user`, then updated the saved credential in the Windows App |
| 2026-10-17 | Setting maximum password age triggered a "Suggested Value Changes" box | Windows offers matching minimum age (30 days) and, for lockout, a 10-minute duration and reset counter | Accepted the suggested values by clicking OK |
| 2026-10-17 | Drive map showed the path `\\servername\folder` | I left the default placeholder location in the new drive map | Left as a placeholder for now; next step is a real shared folder |
| 2026-10-17 | Client VM could not resolve `plowlab.local` | Client used Azure default DNS (`168.63.129.16`) | Set virtual network DNS to Custom with the DC's private IP and restarted the client |
| 2026-10-17 | RDP kept signing in as the local admin | Windows App saved credentials | Set the saved PC to "Ask when required" and signed in as the domain user |
| 2026-10-17 | "You must change your password before signing in" | "User must change password at next logon" was ticked | Reset the password in AD Users and Computers with that box unticked |
| 2026-10-17 | `gpresult /h C:\gpreport.html` returned "Access is denied" | Standard users cannot write to the root of `C:\` | Saved the report to the Desktop |
| 2026-10-18 | Windows Settings would not open on the domain controller | Restrict Control Panel was linked at the domain root, so it also applied to my admin account in the default `Users` container | Removed the domain-root link, linked the GPO to `USA > Users`, verified with `Get-GPInheritance` and `gpresult /r /scope user` |
| 2026-10-18 | Wanted to map `\\PLOWVM\TestFile` | `TestFile` is a folder name, not a share name | Mapped the share name instead: `\\PLOWVM\SHARED` |
| 2026-10-18 | FSRM quota email notification | No SMTP mail server in the lab, so email cannot be delivered | Documented as needing an SMTP server; use the event log notification instead |
| 2026-10-18 | Quota template named `10gb Limit Storage` but limit is 100 MB | Saved a custom 100 MB quota as a template with the wrong name | Noted for renaming |
| 2026-10-18 | File screen blocks Image files in `SHARED` | I selected Image Files among the blocked groups | Wallpaper image will live in a separate folder, not in `SHARED` |

More entries will be added as I build the next steps.
