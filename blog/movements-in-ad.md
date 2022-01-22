---
description: It contains basic methodology for post exploitation.
---

# Movements in AD

You should approach to first machine in AD as regular standalone windows machine. Lateral movement or privilege escalation as well. Movement is not so hard. You must do post exploitation very well.

You can do post exploitation steps as follows:

Check groups where the user is in. Check the user in some privileged group.

Check another users in the domain.

Search files in the system for finding passwords, important notes and information, mail etc.

Check services on the system.

Some services contains cleartext passwords, hashes in some files.

Extract hashes, secrets, kerberos tickets with mimikatz etc. (requires system shell)

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

Check the user has capabilities for explotiable GPO.

{% content-ref url="../readme/windows/privesc/abuse-gpo.md" %}
[abuse-gpo.md](../readme/windows/privesc/abuse-gpo.md)
{% endcontent-ref %}

You can try recent AD exploits to get domain admin privileges.



There are perfect tools to using in AD. These can be helpful.

You will find a way, people build systems that so there is always a weakness.



never give up!
