---
title: Problemen met BitLocker-opstart fouten op een Azure VM oplossen | Microsoft Docs
description: Meer informatie over het oplossen van BitLocker-opstart fouten in een Azure VM
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/20/2020
ms.author: genli
ms.custom: has-adal-ref
ms.openlocfilehash: f69d81656358b8ee9a5fa51cb8261e3115729714
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96511556"
---
# <a name="bitlocker-boot-errors-on-an-azure-vm"></a>BitLocker-opstart fouten op een virtuele Azure-machine

 In dit artikel worden BitLocker-fouten beschreven die zich kunnen voordoen wanneer u een virtuele Windows-machine (VM) start in Microsoft Azure.

 

## <a name="symptom"></a>Symptoom

 Een Windows-VM start niet. Wanneer u de scherm opnamen in het venster [Diagnostische gegevens over opstarten](./boot-diagnostics.md) controleert, ziet u een van de volgende fout berichten:

- Het USB-stuur programma met de BitLocker-sleutel aansluiten

- U bent vergrendeld. Voer de herstel sleutel in om weer aan de slag te gaan (toetsenbord indeling: VS) de onjuiste aanmeldings gegevens zijn te vaak ingevoerd, waardoor uw PC is vergrendeld om uw privacy te beschermen. Als u de herstel sleutel wilt ophalen, gaat u naar https://windows.microsoft.com/recoverykeyfaq vanaf een andere PC of een mobiel apparaat. Als u het nodig hebt, is de sleutel-ID XXXXXXx. U kunt ook uw PC opnieuw instellen.

- Voer het wacht woord in om dit station te ontgrendelen [] Druk op de INSERT-toets om het wacht woord te zien terwijl u typt.
- Voer uw herstel sleutel in om de herstel sleutel van een USB-apparaat te laden.

## <a name="cause"></a>Oorzaak

Dit probleem kan optreden als de virtuele machine het BitLocker-herstel sleutel bestand (BEK) niet kan vinden om de versleutelde schijf te ontsleutelen.

## <a name="solution"></a>Oplossing

U kunt dit probleem oplossen door de virtuele machine te stoppen en de toewijzing ervan ongedaan te maken en vervolgens te starten. Met deze bewerking wordt de virtuele machine gedwongen het BEK-bestand op te halen uit de Azure Key Vault en vervolgens op de versleutelde schijf te plaatsen. 

Als deze methode het probleem niet oplost, voert u de volgende stappen uit om het BEK-bestand hand matig te herstellen:

1. Maak een moment opname van de systeem schijf van de betrokken VM als back-up. Zie [snap shot a disk](../windows/snapshot-copy-managed-disk.md)(Engelstalig) voor meer informatie.
2. [Koppel de systeem schijf aan een herstel-VM](troubleshoot-recovery-disks-portal-windows.md). Als u de opdracht [Manage-BDE](/windows-server/administration/windows-commands/manage-bde) in stap 7 wilt uitvoeren, moet u de functie **BitLocker-stationsversleuteling** ingeschakeld hebben op de virtuele machine voor herstel.

    Wanneer u een beheerde schijf koppelt, ontvangt u mogelijk een fout bericht ' bevat versleutelings instellingen en kan daarom niet worden gebruikt als een gegevens schijf '. In dit geval voert u het volgende script uit om opnieuw te proberen de schijf te koppelen:

    ```Powershell
    $rgName = "myResourceGroup"
    $osDiskName = "ProblemOsDisk"
    # Set the EncryptionSettingsEnabled property to false, so you can attach the disk to the recovery VM.
    New-AzDiskUpdateConfig -EncryptionSettingsEnabled $false |Update-AzDisk -diskName $osDiskName -ResourceGroupName $rgName

    $recoveryVMName = "myRecoveryVM" 
    $recoveryVMRG = "RecoveryVMRG" 
    $OSDisk = Get-AzDisk -ResourceGroupName $rgName -DiskName $osDiskName;

    $vm = get-AzVM -ResourceGroupName $recoveryVMRG -Name $recoveryVMName 

    Add-AzVMDataDisk -VM $vm -Name $osDiskName -ManagedDiskId $osDisk.Id -Caching None -Lun 3 -CreateOption Attach 

    Update-AzVM -VM $vm -ResourceGroupName $recoveryVMRG
    ```
     U kunt een beheerde schijf niet koppelen aan een virtuele machine die is hersteld vanuit een BLOB-installatie kopie.

3. Nadat de schijf is aangesloten, maakt u een verbinding met een extern bureau blad met de virtuele machine voor herstel zodat u enkele Azure PowerShell scripts kunt uitvoeren. Zorg ervoor dat de [meest recente versie van Azure PowerShell](/powershell/azure/) is geïnstalleerd op de virtuele machine voor herstel.

4. Open een verhoogde Azure PowerShell-sessie (als administrator uitvoeren). Voer de volgende opdrachten uit om u aan te melden bij een Azure-abonnement:

    ```Powershell
    Add-AzAccount -SubscriptionID [SubscriptionID]
    ```

5. Voer het volgende script uit om de naam van het BEK-bestand te controleren:

    ```powershell
    $vmName = "myVM"
    $vault = "myKeyVault"
    Get-AzKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.ContentType -match 'BEK')} `
            | Sort-Object -Property Created `
            | ft  Created, `
                @{Label="Content Type";Expression={$_.ContentType}}, `
                @{Label ="MachineName"; Expression = {$_.Tags.MachineName}}, `
                @{Label ="Volume"; Expression = {$_.Tags.VolumeLetter}}, `
                @{Label ="DiskEncryptionKeyFileName"; Expression = {$_.Tags.DiskEncryptionKeyFileName}}
    ```

    Hier volgt een voor beeld van de uitvoer. In dit geval gaan we ervan uit dat de bestands naam EF7B2F5A-50C6-4637-0001-7F599C12F85C is. BEK.

    ```
    Created               Content Type Volume MachineName DiskEncryptionKeyFileName
    -------               ------------ ------ ----------- -------------------------
    11/20/2020 7:41:56 AM BEK          C:\    myVM   EF7B2F5A-50C6-4637-0001-7F599C12F85C.BEK
    ```
    Als er twee dubbele volumes worden weer geven, is het volume met de nieuwere tijds tempel het huidige BEK-bestand dat wordt gebruikt door de herstel-VM.

    Als de waarde van het **inhouds type** **verpakte bek** is, gaat u naar de [Kek-Scenario's (Key Encryption Key)](#key-encryption-key-scenario).

    Nu u de naam van het BEK-bestand voor het station hebt, moet u de naam van het geheime bestand maken. BEK-bestand voor het ontgrendelen van het station.

6.  Down load het BEK-bestand naar de herstel schijf. In het volgende voor beeld wordt het BEK-bestand opgeslagen in de map C:\BEK. Zorg ervoor dat het `C:\BEK\` pad bestaat voordat u de scripts uitvoert.

    ```powershell
    $vault = "myKeyVault"
    $bek = "EF7B2F5A-50C6-4637-0001-7F599C12F85C"
    $keyVaultSecret = Get-AzKeyVaultSecret -VaultName $vault -Name $bek
    $bstr = [Runtime.InteropServices.Marshal]::SecureStringToBSTR($keyVaultSecret.SecretValue)
    $bekSecretBase64 = [Runtime.InteropServices.Marshal]::PtrToStringAuto($bstr)
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = "C:\BEK\DiskEncryptionKeyFileName.BEK"
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7.  Als u de gekoppelde schijf wilt ontgrendelen met behulp van het BEK-bestand, voert u de volgende opdracht uit.

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-0001-7F599C12F85C.BEK
    ```
    In dit voor beeld is de gekoppelde besturingssysteem schijf station F. Zorg ervoor dat u de juiste stationsletter gebruikt. 

8. Nadat de schijf is ontgrendeld met behulp van de BEK-sleutel, ontkoppelt u de schijf van de virtuele machine voor herstel en maakt u de virtuele machine opnieuw met behulp van deze nieuwe besturingssysteem schijf.

    > [!NOTE]
    > Het wisselen van de besturingssysteem schijf wordt niet ondersteund voor Vm's die gebruikmaken van schijf versleuteling.

9. Als de nieuwe virtuele machine nog steeds niet normaal kan worden opgestart, voert u een van de volgende stappen uit nadat u het station hebt ontgrendeld:

    - Stop de beveiliging om BitLocker tijdelijk uit te scha kelen door het volgende uit te voeren:

    ```console
    manage-bde -protectors -disable F: -rc 0
    ```

    - Het station volledig ontsleutelen. Voer hiervoor de volgende opdracht uit:

    ```console
    manage-bde -off F:
    ```

### <a name="key-encryption-key-scenario"></a>Key Encryption Key-scenario

Voer de volgende stappen uit voor een Key Encryption Key-scenario:

1. Zorg ervoor dat voor het aangemelde gebruikers account de machtiging ' onverpakt ' is vereist in het Key Vault toegangs beleid in de **gebruiker | Sleutel machtigingen | Cryptografische bewerkingen | De sleutel voor uitpakken**.
2. Sla het volgende script op in een. PS1-bestand:

    ```powershell
    #Set the Parameters for the script
    param (
            [Parameter(Mandatory=$true)]
            [string] 
            $keyVaultName,
            [Parameter(Mandatory=$true)]
            [string] 
            $kekName,
            [Parameter(Mandatory=$true)]
            [string]
            $secretName,
            [Parameter(Mandatory=$true)]
            [string]
            $bekFilePath,
            [Parameter(Mandatory=$true)]
            [string] 
            $adTenant
            )
    # Load ADAL Assemblies
    $adal = "${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts\$(((dir ${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts).name) | select -last 1)\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts\$(((dir ${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts).name) | select -last 1)\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.Platform.dll"
    If ((Test-Path -Path $adal) -and (Test-Path -Path $adalforms)) { 

    [System.Reflection.Assembly]::LoadFrom($adal)
    [System.Reflection.Assembly]::LoadFrom($adalforms)
     }
     else
     {
    $adal="${env:userprofile}\Documents\WindowsPowerShell\Modules\Az.Accounts\$(((dir ${env:userprofile}\Documents\WindowsPowerShell\Modules\Az.Accounts).name) | select -last 1)\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms ="${env:userprofile}\Documents\WindowsPowerShell\Modules\Az.Accounts\$(((dir ${env:userprofile}\Documents\WindowsPowerShell\Modules\Az.Agit pgit ccounts).name) | select -last 1)\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.Platform.dll"
    [System.Reflection.Assembly]::LoadFrom($adal)
    [System.Reflection.Assembly]::LoadFrom($adalforms)
     }  

    # Set well-known client ID for AzurePowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2" 
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURI = "https://vault.azure.net"
    # Set Authority to Azure AD Tenant
    $authority = "https://login.windows.net/$adtenant"
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $platformParameters = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList "Auto"
    $authResult = $authContext.AcquireTokenAsync($resourceAppIdURI, $clientId, $redirectUri, $platformParameters).result
    # Generate auth header 
    $authHeader = $authResult.CreateAuthorizationHeader()
    # Set HTTP request headers to include Authorization header
    $headers = @{'x-ms-version'='2014-08-01';"Authorization" = $authHeader}

    ########################################################################################################################
    # 1. Retrieve wrapped BEK
    # 2. Make KeyVault REST API call to unwrap the BEK
    # 3. Convert the Base64Url string returned by KeyVault unwrap to Base64 string 
    # 4. Convert Base64 string to bytes and write to the BEK file
    ########################################################################################################################

    #Get wrapped BEK and place it in JSON object to send to KeyVault REST API
    $keyVaultSecret = Get-AzKeyVaultSecret -VaultName $keyVaultName -Name $secretName
    $bstr = [Runtime.InteropServices.Marshal]::SecureStringToBSTR($keyVaultSecret.SecretValue)
    $wrappedBekSecretBase64 = [Runtime.InteropServices.Marshal]::PtrToStringAuto($bstr)
    $jsonObject = @"
    {
    "alg": "RSA-OAEP",
    "value" : "$wrappedBekSecretBase64"
    }
    "@

    #Get KEK Url
    $kekUrl = (Get-AzKeyVaultKey -VaultName $keyVaultName -Name $kekName).Key.Kid;
    $unwrapKeyRequestUrl = $kekUrl+ "/unwrapkey?api-version=2015-06-01";

    #Call KeyVault REST API to Unwrap 
    $result = Invoke-RestMethod -Method POST -Uri $unwrapKeyRequestUrl -Headers $headers -Body $jsonObject -ContentType "application/json" -Debug

    #Convert Base64Url string returned by KeyVault unwrap to Base64 string
    $base64UrlBek = $result.value;
    $base64Bek = $base64UrlBek.Replace('-', '+');
    $base64Bek = $base64Bek.Replace('_', '/');
    if($base64Bek.Length %4 -eq 2)
    {
        $base64Bek+= '==';
    }
    elseif($base64Bek.Length %4 -eq 3)
    {
        $base64Bek+= '=';
    }

    #Convert base64 string to bytes and write to BEK file
    $bekFileBytes = [System.Convert]::FromBase64String($base64Bek);
    [System.IO.File]::WriteAllBytes($bekFilePath,$bekFileBytes)

    #Delete the key from the memory
    [Runtime.InteropServices.Marshal]::ZeroFreeBSTR($bstr)
    clear-variable -name wrappedBekSecretBase64
    ```
3. Stel de para meters in. Het script verwerkt het KEK-geheim voor het maken van de BEK-sleutel en slaat deze vervolgens op in een lokale map op de virtuele machine voor herstel. Als er fouten optreden wanneer u het script uitvoert, raadpleegt u de sectie [script Troubleshooting](#script-troubleshooting) (Engelstalig).

4. U ziet de volgende uitvoer wanneer het script begint:

    Locatie van GAC-versie                                                                              
    ---    -------        --------                                                                              
    ONWAAR v 4.0.30319 C:\Program Files\WindowsPowerShell\Modules\Az.Accounts \. ..  ONWAAR v 4.0.30319 C:\Program Files\WindowsPowerShell\Modules\Az.Accounts \. ..

    Wanneer het script is voltooid, ziet u de volgende uitvoer:

    ```output
    VERBOSE: POST https://myvault.vault.azure.net/keys/rondomkey/<KEY-ID>/unwrapkey?api-
    version=2015-06-01 with -1-byte payload
    VERBOSE: received 360-byte response of content type application/json; charset=utf-8
    ```

5. Als u de gekoppelde schijf wilt ontgrendelen met behulp van het BEK-bestand, voert u de volgende opdracht uit:

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    In dit voor beeld is de gekoppelde besturingssysteem schijf station F. Zorg ervoor dat u de juiste stationsletter gebruikt. 

6. Nadat de schijf is ontgrendeld met behulp van de BEK-sleutel, ontkoppelt u de schijf van de virtuele machine voor herstel en maakt u de virtuele machine opnieuw met behulp van deze nieuwe besturingssysteem schijf. 

    > [!NOTE]
    > Het wisselen van de besturingssysteem schijf wordt niet ondersteund voor Vm's die gebruikmaken van schijf versleuteling.

7. Als de nieuwe virtuele machine nog steeds niet normaal kan worden opgestart, voert u een van de volgende stappen uit nadat u het station hebt ontgrendeld:

    - Stop de beveiliging om BitLocker tijdelijk uit te scha kelen door de volgende opdracht uit te voeren:

    ```console
    manage-bde -protectors -disable F: -rc 0
    ```

    - Het station volledig ontsleutelen. Voer hiervoor de volgende opdracht uit:

    ```console
    manage-bde -off F:
    ```

## <a name="script-troubleshooting"></a>Script problemen oplossen

**Fout: kan bestand of assembly niet laden**

Deze fout treedt op omdat de paden van de ADAL-Assembly's onjuist zijn. U kunt zoeken naar `Az.Accounts` een map om het juiste pad te vinden.

**Fout: Get-AzKeyVaultSecret of Get-AzKeyVaultSecret wordt niet herkend als de naam van een cmdlet**

Als u de oude AZ Power shell-module gebruikt, moet u de twee opdrachten wijzigen in `Get-AzureKeyVaultSecret` en `Get-AzureKeyVaultSecret` .

**Voor beelden van para meters**

| Parameters  | Voor beeld van waarde  |Opmerkingen   |
|---|---|---|
|  $keyVaultName | myKeyVault2112852926  | De naam van de sleutel kluis die de sleutel opslaat |
|$kekName   |mykey   | De naam van de sleutel die wordt gebruikt om de virtuele machine te versleutelen|
|$secretName   |7EB4F531-5FBA-4970-8E2D-C11FD6B0C69D  | De naam van het geheim van de VM-sleutel|
|$bekFilePath   |c:\bek\7EB4F531-5FBA-4970-8E2D-C11FD6B0C69D. BEK |Het pad voor het schrijven van BEK-bestand.|
|$adTenant  |contoso.onmicrosoft.com   | FQDN of GUID van uw Azure Active Directory die als host fungeert voor de sleutel kluis |
