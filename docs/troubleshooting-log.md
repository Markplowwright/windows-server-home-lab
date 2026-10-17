# Troubleshooting log

Real problems from the lab, with the cause and the fix. Newest at the bottom.

| Date | Problem | Cause | Fix |
|---|---|---|---|
| 2026-10-15 | Azure VM size showed "NotAvailableForSubscription" | The default size is not offered on a new free-trial subscription in that region | Opened the full size list and picked a size that was not greyed out |
| 2026-10-15 | Create-VM form showed SSH key settings | The image was still the default Linux one | Switched the image to Windows Server, which changes sign-in to username and password |
| 2026-10-17 | Forgot the sign-in password for the lab VM | Azure does not store the VM password, and the portal's Reset password blade did not apply the change | Used **Run command > RunPowerShellScript** on the VM to set a new password with `net user`, then updated the saved credential in the Windows App |
| 2026-10-17 | Setting maximum password age triggered a "Suggested Value Changes" box | Windows offers matching minimum age (30 days) and, for lockout, a 10-minute duration and reset counter | Accepted the suggested values by clicking OK |
| 2026-10-17 | Drive map showed the path `\\servername\folder` | I left the default placeholder location in the new drive map | Left as a placeholder for now; next step is a real shared folder |

More entries will be added as I build the next steps.
