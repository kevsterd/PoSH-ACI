﻿# PowerShell for Cisco ACI (ACI-PoSH)
 
 [![published](https://static.production.devnetcloud.com/codeexchange/assets/images/devnet-published.svg)](https://developer.cisco.com/codeexchange/github/repo/kevsterd/ACI-PoSH)

This is a set of PowerShell modules for Cisco ACI. These drive the native ACI RESTful API exposed by systems APIC's and expose these functions in PowerShell Commandlets.
> **NOTE:**
> You obviously need a PowerShell envionment that works.  This has been tested with Windows 10/Server 2012 R2 on Windows and PowerShell Core on Linux along with Cisco ACI Versions 3.x
> - Access to your Cisco ACI environment via HTTPS <b>and</b> credentials that have relevant access
> - You should be aware of commands you are running as well as the implications of doing so.
> - At present, input is **not** filtered. If you make a typo, the API will execute it.
> - This set of modules is still under development. So please check back for more updates. There is a lot to do.
#### <i class="icon-file"></i> Installation
Copy the modules to either your PowerShell module directories (either for the system
or per user) <b>or</b>
Import the modules:
<pre>
>	import-module .\aci-functions.psml
>	import-module .\aci-services.psml
</pre>
Your other alternative is to add these into a PowerShell script you run to start:
<pre>
#Import Functions
Write-Host "Importing ACI Functions" -ForegroundColor Green
 Import-Module .\aci-functions.psml -WarningAction SilentlyContinue

Write-Host "Importing ACI Services" -ForegroundColor Green
 Import-Module .\aci-services.psml -WarningAction SilentlyContinue

#Login (Optional)
 New-Aci-Login -Apic MyAPIC -Username MyUsername -Password MyPassword
</pre>
#### <i class="icon-folder-open"></i> Authenticate to ACI
First step is to authenticate to the APIC.
<pre>> New-Aci-Login -Apic MyAPIC -Username MyUsername -Password MyPassword</pre>
You should see the message <b>Authenticated!</b> 

If it fails, run the same command again. Occasionally the APIC API sometimes fails for no apparent reason.  Need to get to the bottom of this.
*	If you fail to supply a username then the currently logged in userlD (%username%) from Windows is used.
*	If you fail to supply a password, then you are prompted for it.
*	If you fail to supply a APIC name, then <b>APIC</b> is used as the hostname
You can also use the -StoreLocation argument to specify a credential file rather than being prompted for passwords.
>	**Tip:** ACI has very short session timers (300 seconds) and thus you will find you need to authenticate frequently.
#### <i class="icon-pencil"></i> Commands
Currently defined commands are:
<pre>
# I removed the list of commands here, as it was getting too long.   To find all of them run:

Get-Command | where-Object {$_.name -like '*-ACI-*'}

</pre>
All modules now have updated help text.   Hopefully that will be useful!
<pre>
Get-ACI-Tenant

name        descr dn                
----        ----- --                
infra             uni/tn-infra      
common            uni/tn-common     
mgmt              uni/tn-mgmt       
companyA          uni/tn-companyA   
companyB    Co B  uni/tn-companyB   
companyC          uni/tn-companyC   
cloudMgmt         uni/tn-cloudMgmt  
secretAudit       uni/tn-secretAudit
</pre>	
As you see, we get useful paremeters shown along with the actual object (dn). The dn is not used by PoshACI but shown for completeness.
You can then run additional commands such as
<pre>get-ACI-AppProfile-All -tenant ACI-TenX</pre>

**Tip:** - Remember ACI is case sensitive, including all configuration.
The above command will show all of the Application Profiles for Tenant TenX.

The **-All** identifier is used for some commandlets, rather than being the default for the commands. One to fix for later releases.

Now I have fixed the output/pipeline support remember how powerful PowerShell is so you can chain commands together.  For example: 

<pre>
Get-ACI-tenant | where-Object {$_.name -like '*Hos*'} | Get-ACI-vrf | Where-Object {$_.name -notlike '*vrf*'} | Format-Table
</pre>

This searches for Tenants that have the string 'Hos' within, then gets their VRF's which don't have the string 'vrf' within.  Then outputs as a table !
