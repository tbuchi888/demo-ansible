$cert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2
$cert.Import("C:\Users\test001\Desktop\pem\cert.pem")
$store_name = [System.Security.Cryptography.X509Certificates.StoreName]::Root
$store_location = [System.Security.Cryptography.X509Certificates.StoreLocation]::LocalMachine
$store = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Store -ArgumentList $store_name, $store_location
$store.Open("MaxAllowed")
$store.Add($cert)
$store.Close()


$cert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2
$cert.Import("C:\Users\test001\Desktop\pem\cert.pem")
$store_name = [System.Security.Cryptography.X509Certificates.StoreName]::TrustedPeople
$store_location = [System.Security.Cryptography.X509Certificates.StoreLocation]::LocalMachine
$store = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Store -ArgumentList $store_name, $store_location
$store.Open("MaxAllowed")
$store.Add($cert)
$store.Close()


$username = "winusername"
$password = ConvertTo-SecureString -String "winpassword" -AsPlainText -Force
$credential = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $username, $password


$thumbprint = (Get-ChildItem -Path cert:\LocalMachine\root | Where-Object { $_.Subject -eq "CN=$username" }).Thumbprint
New-Item -Path WSMan:\localhost\ClientCertificate `
     -Subject "$username@localhost" `
     -URI * `
     -Issuer $thumbprint `
     -Credential $credential `
     -Force

winrm set winrm/config/service/Auth '@{Certificate="true"}'
```
Windows PowerShell
Copyright (C) Microsoft Corporation. All rights reserved.

PS C:\Users\winusername> $url = "https://raw.githubusercontent.com/jborean93/ansible-windows/master/scripts/Install-WMF3Hotfix.ps1"
>> $file = "$env:temp\Install-WMF3Hotfix.ps1"
>>
>> (New-Object -TypeName System.Net.WebClient).DownloadFile($url, $file)
>> powershell.exe -ExecutionPolicy ByPass -File $file -Verbose
VERBOSE: KB2842230 is only applicable with Powershell v3, no action required
PS C:\Users\winusername> $url = "https://raw.githubusercontent.com/ansible/ansible/devel/examples/scripts/ConfigureRemotingForAnsible.ps1"
>> $file = "$env:temp\ConfigureRemotingForAnsible.ps1"
>>
>> (New-Object -TypeName System.Net.WebClient).DownloadFile($url, $file)
>>
>> powershell.exe -ExecutionPolicy ByPass -File $file
Self-signed SSL certificate generated; thumbprint: 3BC8A8EC101812EFC60668017DC144C69DC63810


wxf                 : http://schemas.xmlsoap.org/ws/2004/09/transfer
a                   : http://schemas.xmlsoap.org/ws/2004/08/addressing
w                   : http://schemas.dmtf.org/wbem/wsman/1/wsman.xsd
lang                : en-US
Address             : http://schemas.xmlsoap.org/ws/2004/08/addressing/role/anonymous
ReferenceParameters : ReferenceParameters

Ok.

PS C:\Users\winusername> Set-Item -Path WSMan:localhostServiceAuthCertificate -Value $true
Set-Item : This command cannot be used in the current path because this cmdlet is not supported at this level of
Provider path.
At line:1 char:1
+ Set-Item -Path WSMan:localhostServiceAuthCertificate -Value $true
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Set-Item], InvalidOperationException
    + FullyQualifiedErrorId : System.InvalidOperationException,Microsoft.PowerShell.Commands.SetItemCommand

PS C:\Users\winusername> winrm enumerate winrm/config/Listener
>>
Listener
    Address = *
    Transport = HTTP
    Port = 5985
    Hostname
    Enabled = true
    URLPrefix = wsman
    CertificateThumbprint
    ListeningOn = 127.0.0.1, 172.17.100.7, ::1, fe80::1494:6e4b:ade0:bbd0%3

Listener
    Address = *
    Transport = HTTPS
    Port = 5986
    Hostname = win02
    Enabled = true
    URLPrefix = wsman
    CertificateThumbprint = 3BC8A8EC101812EFC60668017DC144C69DC63810
    ListeningOn = 127.0.0.1, 172.17.100.7, ::1, fe80::1494:6e4b:ade0:bbd0%3

PS C:\Users\winusername>
PS C:\Users\winusername>
PS C:\Users\winusername> $cert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2
>> $cert.Import("C:\Users\winusername\Desktop\pem\cert.pem")
>> $store_name = [System.Security.Cryptography.X509Certificates.StoreName]::Root
>> $store_location = [System.Security.Cryptography.X509Certificates.StoreLocation]::LocalMachine
>> $store = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Store -ArgumentList $store_name, $store_location
>> $store.Open("MaxAllowed")
>> $store.Add($cert)
>> $store.Close()
PS C:\Users\winusername>
PS C:\Users\winusername> $cert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2
>> $cert.Import("C:\Users\winusername\Desktop\pem\cert.pem")
>> $store_name = [System.Security.Cryptography.X509Certificates.StoreName]::TrustedPeople
>> $store_location = [System.Security.Cryptography.X509Certificates.StoreLocation]::LocalMachine
>> $store = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Store -ArgumentList $store_name, $store_location
>> $store.Open("MaxAllowed")
>> $store.Add($cert)
>> $store.Close()
PS C:\Users\winusername>
PS C:\Users\winusername>
PS C:\Users\winusername> $username = "winusername"
>> $password = ConvertTo-SecureString -String "winuserpassword" -AsPlainText -Force
>> $credential = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $username, $password
PS C:\Users\winusername>
PS C:\Users\winusername>
>> $thumbprint = (Get-ChildItem -Path cert:\LocalMachine\root | Where-Object { $_.Subject -eq "CN=$username" }).Thumbprint
>> New-Item -Path WSMan:\localhost\ClientCertificate `
>>      -Subject "$username@localhost" `
>>      -URI * `
>>      -Issuer $thumbprint `
>>      -Credential $credential `
>>      -Force


   WSManConfig: Microsoft.WSMan.Management\WSMan::localhost\ClientCertificate

Type            Keys                                Name
----            ----                                ----
Container       {URI=*, Issuer=F24BD57C95B5A3C5F... ClientCertificate_1042584556


PS C:\Users\winusername> winrm set winrm/config/service/Auth '@{Certificate="true"}'
Auth
    Basic = true
    Kerberos = true
    Negotiate = true
    Certificate = true
    CredSSP = false
    CbtHardeningLevel = Relaxed

PS C:\Users\winusername>
```
