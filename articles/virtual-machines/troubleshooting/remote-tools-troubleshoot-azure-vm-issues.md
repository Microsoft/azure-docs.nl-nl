---
title: Externe hulpprogram ma's gebruiken voor het oplossen van problemen met virtuele Azure-machines | Microsoft Docs
description: Meer informatie over PsExec, Power shell-scripts en andere externe hulpprogram ma's die u kunt gebruiken om problemen met externe Azure-VM'S op te lossen zonder RDP te gebruiken.
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 01/11/2018
ms.author: delhan
ms.openlocfilehash: c5974388c096c9bc8693c5fc2cf918989c6eadd3
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96488728"
---
# <a name="use-remote-tools-to-troubleshoot-azure-vm-issues"></a>Externe hulpprogram ma's gebruiken voor het oplossen van problemen met Azure VM

Wanneer u problemen met een virtuele Azure-machine (VM) oplost, kunt u verbinding maken met de VM met behulp van de externe hulpprogram ma's die in dit artikel worden besproken in plaats van de Remote Desktop Protocol (RDP) te gebruiken.

## <a name="serial-console"></a>Seriële console

Gebruik een [seriële console voor Azure virtual machines](serial-console-windows.md) om opdrachten uit te voeren op de externe Azure-VM.

## <a name="remote-cmd"></a>Externe CMD

Down load [PsExec](/sysinternals/downloads/psexec). Maak verbinding met de virtuele machine door de volgende opdracht uit te voeren:

```cmd
psexec \\<computer>-u user -s cmd
```

>[!NOTE]
>* De opdracht moet worden uitgevoerd op een computer die zich in hetzelfde virtuele netwerk bevindt.
>* DIP of hostnaam kan worden gebruikt om te vervangen \<computer> .
>* De para meter-s zorgt ervoor dat de opdracht wordt aangeroepen met behulp van systeem account (Administrator-machtiging).
>* PsExec maakt gebruik van TCP-poorten 135 en 445. Als gevolg hiervan moeten de twee poorten open zijn op de firewall.

## <a name="run-command"></a>Opdracht uitvoeren

Zie [Power shell-scripts uitvoeren in uw Windows-VM met de opdracht uitvoeren](../windows/run-command.md)voor meer informatie over het gebruik van de functie opdracht uitvoeren om scripts uit te voeren op de virtuele machine.

## <a name="custom-script-extension"></a>Aangepaste scriptextensie

U kunt de functie aangepaste script extensie gebruiken om een aangepast script uit te voeren op de doel-VM. Als u deze functie wilt gebruiken, moet aan de volgende voor waarden worden voldaan:

* De virtuele machine heeft connectiviteit.
* De agent van de virtuele machine van Azure is geïnstalleerd en werkt zoals verwacht op de VM.
* De extensie is niet eerder geïnstalleerd op de virtuele machine.
 
  De uitbrei ding injecteert het script alleen de eerste keer dat deze wordt gebruikt. Als u deze functie later gebruikt, detecteert de uitbrei ding dat deze al is gebruikt en uploadt het nieuwe script niet.

Upload uw script naar een opslag account en Genereer een eigen container. Voer vervolgens het volgende script uit in Azure PowerShell op een computer die verbinding met de virtuele machine heeft.

### <a name="for-classic-deployment-model-vms"></a>Voor het klassieke implementatie model Vm's

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


```powershell
#Set up the basic variables.
$subscriptionID = "<<SUBSCRIPTION ID>>" 
$storageAccount = "<<STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage.
$vmName = "<<VM NAME>>" 
$vmCloudService = "<<CLOUD SERVICE>>" #Resource group or cloud service where the VM is hosted. For example, for "demo305.cloudapp.net" the cloud service is going to be demo305.

#Set up the Azure PowerShell module, and ensure the access to the subscription.
Import-Module Azure
Add-AzureAccount  #Ensure login with the account associated with the subscription ID.
Get-AzureSubscription -SubscriptionId $subscriptionID | Select-AzureSubscription

#Set up the access to the storage account, and upload the script.
$storageKey = (Get-AzureStorageKey -StorageAccountName $storageAccount).Primary
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)<
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM.
$vm = Get-AzureVM -ServiceName $vmCloudService -Name $vmName
Set-AzureVMCustomScriptExtension "CustomScriptExtension" -VM $vm -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName | Update-AzureVM
```

### <a name="for-azure-resource-manager-vms"></a>Voor Azure Resource Manager Vm's

 

```powershell
#Set up the basic variables.
$subscriptionID = "<<SUBSCRIPTION ID>>"
$storageAccount = "<<STORAGE ACCOUNT>>"
$storageRG = "<<RESOURCE GROUP OF THE STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage.
$vmName = "<<VM NAME>>" 
$vmResourceGroup = "<<RESOURCE GROUP>>"
$vmLocation = "<<DATACENTER>>" 
 
#Set up the Azure PowerShell module, and ensure the access to the subscription.
Login-AzAccount #Ensure login with the account associated with the subscription ID.
Get-AzSubscription -SubscriptionId $subscriptionID | Select-AzSubscription

#Set up the access to the storage account, and upload the script.
$storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageRG -Name $storageAccount).Value[0]
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM.
Set-AzVMCustomScriptExtension -Name "CustomScriptExtension" -ResourceGroupName $vmResourceGroup -VMName $vmName -Location $vmLocation -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName
```

## <a name="remote-powershell"></a>Externe Power shell

>[!NOTE]
>TCP-poort 5986 (HTTPS) moet zijn geopend, zodat u deze optie kunt gebruiken.
>
>Voor Azure Resource Manager Vm's moet u poort 5986 openen voor de netwerk beveiligings groep (NSG). Zie Beveiligingsgroepen voor meer informatie. 
>
>Voor virtuele machines van RDFE moet u een eind punt hebben met een particuliere poort (5986) en een open bare poort. Daarna moet u die open bare poort ook openen op de NSG.

### <a name="set-up-the-client-computer"></a>De client computer instellen

Als u Power shell wilt gebruiken om op afstand verbinding te maken met de virtuele machine, moet u eerst de client computer instellen om de verbinding toe te staan. U doet dit door de virtuele machine toe te voegen aan de lijst met vertrouwde hosts van Power shell door de volgende opdracht uit te voeren, indien van toepassing.

Eén virtuele machine toevoegen aan de lijst met vertrouwde hosts:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName>
```

Meerdere Vm's toevoegen aan de lijst met vertrouwde hosts:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName1>,<ComputerName2>
```

Alle computers toevoegen aan de lijst met vertrouwde hosts:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value *
```

### <a name="enable-remoteps-on-the-vm"></a>RemotePS inschakelen op de VM

Voor virtuele machines die zijn gemaakt met het klassieke implementatie model, gebruikt u de aangepaste script extensie om het volgende script uit te voeren:

```powershell
Enable-PSRemoting -Force
New-NetFirewallRule -Name "Allow WinRM HTTPS" -DisplayName "WinRM HTTPS" -Enabled True -Profile Any -Action Allow -Direction Inbound -LocalPort 5986 -Protocol TCP
$thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME -CertStoreLocation Cert:\LocalMachine\My).Thumbprint
$command = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:computername""; CertificateThumbprint=""$thumbprint""}"
cmd.exe /C $command
```

Voor Azure Resource Manager Vm's gebruikt u de opdrachten uitvoeren vanuit de portal om het EnableRemotePS-script uit te voeren:

![Opdracht uitvoeren](./media/remote-tools-troubleshoot-azure-vm-issues/run-command.png)

### <a name="connect-to-the-vm"></a>Verbinding maken met de virtuele machine

Voer de volgende opdracht uit op basis van de locatie van de client computer:

* Buiten het virtuele netwerk of de implementatie

  * Voer de volgende opdracht uit voor een VM die is gemaakt met het klassieke implementatie model:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName  "<<CLOUDSERVICENAME.cloudapp.net>>" -port "<<PUBLIC PORT NUMBER>>" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

  * Voor een Azure Resource Manager virtuele machine voegt u eerst een DNS-naam toe aan het open bare IP-adres. Zie [een Fully Qualified Domain name maken in de Azure portal voor een Windows-VM](../create-fqdn.md)voor gedetailleerde stappen. Voer vervolgens de volgende opdracht uit:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName "<<DNSname.DataCenter.cloudapp.azure.com>>" -port "5986" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

* Voer in het virtuele netwerk of de implementatie de volgende opdracht uit:
  
  ```powershell
  $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
  Enter-PSSession -ComputerName  "<<HOSTNAME>>" -port 5986 -Credential (Get-Credential) -useSSL -SessionOption $Skip
  ```

>[!NOTE] 
>Als u de vlag SkipCaCheck instelt, wordt de vereiste voor het importeren van een certificaat naar de VM tijdens het starten van de sessie omzeild.

U kunt ook de cmdlet Invoke-Command gebruiken om op afstand een script uit te voeren op de virtuele machine.

```powershell
Invoke-Command -ComputerName "<<COMPUTERNAME>" -ScriptBlock {"<<SCRIPT BLOCK>>"}
```

## <a name="remote-registry"></a>Extern register

>[!NOTE]
>TCP-poort 135 of 445 moet geopend zijn om deze optie te kunnen gebruiken.
>
>Voor Azure Resource Manager Vm's moet u poort 5986 openen op de NSG. Zie Beveiligingsgroepen voor meer informatie. 
>
>Voor virtuele machines van RDFE moet u een eind punt hebben met een particuliere poort 5986 en een open bare poort. U moet deze open bare poort ook openen op de NSG.

1. Open de REGI ster-editor (regedit.exe) van een andere VM in hetzelfde virtuele netwerk.

2. Selecteer **bestand**  >  **Connect netwerk Registry**.

   ![REGI ster-editor](./media/remote-tools-troubleshoot-azure-vm-issues/remote-registry.png) 

3. Zoek de doel-VM op basis van de **hostnaam** of het **dynamische IP-adres** (voor keur) door deze in het vak **Geef de object naam op te** geven.

   ![Geef de object naam op om het vak te selecteren](./media/remote-tools-troubleshoot-azure-vm-issues/input-computer-name.png) 
 
4. Voer de referenties in voor de doel-VM.

5. Breng de benodigde wijzigingen aan in het REGI ster.

## <a name="remote-services-console"></a>Externe Services-console

>[!NOTE]
>TCP-poorten 135 of 445 moeten zijn geopend om deze optie te kunnen gebruiken.
>
>Voor Azure Resource Manager Vm's moet u poort 5986 openen op de NSG. Zie Beveiligingsgroepen voor meer informatie. 
>
>Voor virtuele machines van RDFE moet u een eind punt hebben met een particuliere poort 5986 en een open bare poort. U moet deze open bare poort ook openen op de NSG.

1. Open vanuit een andere VM in hetzelfde virtuele netwerk een exemplaar van **Services. msc**.

2. Klik met de rechter muisknop op **Services (lokaal)**.

3. Selecteer **verbinding maken met een andere computer**.

   ![Externe service](./media/remote-tools-troubleshoot-azure-vm-issues/remote-services.png)

4. Voer het dynamische IP-adres van de doel-VM in.

   ![Dynamische IP-invoer](./media/remote-tools-troubleshoot-azure-vm-issues/input-ip-address.png)

5. Breng de benodigde wijzigingen aan in de services.

## <a name="next-steps"></a>Volgende stappen

- Zie voor meer informatie over de Enter-PSSession-cmdlet [Enter-PSSession](/powershell/module/microsoft.powershell.core/enter-pssession?view=powershell-5.1).
- Zie [aangepaste script extensie voor Windows](../extensions/custom-script-windows.md)voor meer informatie over de aangepaste script extensie voor Windows met behulp van het klassieke implementatie model.
- PsExec maakt deel uit van de [PSTools-Suite](https://download.sysinternals.com/files/PSTools.zip).
- Zie [PSTools](/sysinternals/downloads/pstools)voor meer informatie over de PSTools-Suite.