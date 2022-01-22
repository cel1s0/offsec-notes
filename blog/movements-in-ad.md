---
description: >-
  It contains basic methodology for movements in Active Directory. I used these
  methodology in exam. You can use any tool to identify the way.
---

# Movements in AD

You should approach to first machine in AD as regular standalone windows machine. Lateral movement or privilege escalation as well. Movement is not so hard. You can easily take over domain.

After the getting system shell,

Check groups where the user is in. If the user is in some privileged group, it can help a lot.

Check another users in the domain.

**Search files in the system for finding passwords, important notes and information, mail etc.**

**Extract hashes, secrets, kerberos tickets**

{% content-ref url="../windows/password-extraction.md" %}
[password-extraction.md](../windows/password-extraction.md)
{% endcontent-ref %}

Try pass the hash methods like using psexec or crackmapexec from impacket, evil-winrm, xfreerdp etc. (If the target user has not Administrator rights, you cannot get shell with impacket).

Try cracking extracted hashes.

{% content-ref url="../readme/password-cracking.md" %}
[password-cracking.md](../readme/password-cracking.md)
{% endcontent-ref %}

Check SPNs. If there is SPN in system, try extract and crack it.

{% content-ref url="../windows/useful-ps-scripts/getuserspns.ps1.md" %}
[getuserspns.ps1.md](../windows/useful-ps-scripts/getuserspns.ps1.md)
{% endcontent-ref %}

Check GMSA. If there is gmsa account in the system, try extract and crack it.

{% content-ref url="../readme/windows/privesc/gmsa.md" %}
[gmsa.md](../readme/windows/privesc/gmsa.md)
{% endcontent-ref %}

If you have cleartext password you can use Spray-Passwords.ps1 to identify the another user is using the same password.

{% content-ref url="../windows/useful-ps-scripts/spray-passwords.ps1.md" %}
[spray-passwords.ps1.md](../windows/useful-ps-scripts/spray-passwords.ps1.md)
{% endcontent-ref %}

**Try all hashes and passwords on another machine: PTH, RDP, SSH, SMB, FTP, WinRM, Web service etc.**

**May the force be with you.**
