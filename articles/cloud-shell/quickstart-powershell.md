---
title: Azure Cloud Shell Snelstartgids-Power shell
description: Meer informatie over het gebruik van Power shell in uw browser met Azure Cloud Shell.
author: maertendmsft
ms.author: damaerte
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 10/18/2018
ms.openlocfilehash: 7ff58c4e463b4ad47680b9140403e9ae5e22b057
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105045277"
---
# <a name="quickstart-for-powershell-in-azure-cloud-shell"></a>Snelstartgids voor Power shell in Azure Cloud Shell

Dit document bevat informatie over het gebruik van Power shell in Cloud Shell in de [Azure Portal](https://portal.azure.com/).

> [!NOTE]
> Er is ook een [bash in azure Cloud shell](quickstart.md) Quick Start beschikbaar.

## <a name="start-cloud-shell"></a>Cloud Shell starten

1. Klik op **Cloud shell** knop in de bovenste navigatie balk van de Azure Portal

   ![Scherm afbeelding die laat zien hoe u Azure Cloud Shell start vanuit de Azure Portal.](media/quickstart-powershell/shell-icon.png)

2. Selecteer de Power shell-omgeving in de vervolg keuzelijst en u zich in azure Drive bevindt `(Azure:)`

   ![Scherm afbeelding die laat zien hoe u de Power shell-omgeving voor de Azure Cloud Shell selecteert.](media/quickstart-powershell/environment-ps.png)

## <a name="run-powershell-commands"></a>Power shell-opdrachten uitvoeren

Voer normale Power shell-opdrachten uit in de Cloud Shell, zoals:

```azurepowershell-interactive
PS Azure:\> Get-Date

# Expected Output
Friday, July 27, 2018 7:08:48 AM

PS Azure:\> Get-AzVM -Status

# Expected Output
ResourceGroupName       Name       Location                VmSize   OsType     ProvisioningState  PowerState
-----------------       ----       --------                ------   ------     -----------------  ----------
MyResourceGroup2        Demo        westus         Standard_DS1_v2  Windows    Succeeded           running
MyResourceGroup         MyVM1       eastus            Standard_DS1  Windows    Succeeded           running
MyResourceGroup         MyVM2       eastus   Standard_DS2_v2_Promo  Windows    Succeeded           deallocated
```

### <a name="interact-with-virtual-machines"></a>Interactie met virtuele machines

U kunt alle virtuele machines vinden onder het huidige abonnement via de `VirtualMachines` Directory.

```azurepowershell-interactive
PS Azure:\MySubscriptionName\VirtualMachines> dir

    Directory: Azure:\MySubscriptionName\VirtualMachines


Name       ResourceGroupName  Location  VmSize          OsType              NIC ProvisioningState  PowerState
----       -----------------  --------  ------          ------              --- -----------------  ----------
TestVm1    MyResourceGroup1   westus    Standard_DS2_v2 Windows       my2008r213         Succeeded     stopped
TestVm2    MyResourceGroup1   westus    Standard_DS1_v2 Windows          jpstest         Succeeded deallocated
TestVm10   MyResourceGroup2   eastus    Standard_DS1_v2 Windows           mytest         Succeeded     running
```

#### <a name="invoke-powershell-script-across-remote-vms"></a>Power shell-script aanroepen op externe Vm's

 > [!WARNING]
 > Raadpleeg het [oplossen van het externe beheer van virtuele Azure-machines](troubleshooting.md#troubleshooting-remote-management-of-azure-vms).

  Als u een VM-MyVM1 hebt, kunt u `Invoke-AzVMCommand` een Power shell-script blok aanroepen op de externe computer.

  ```azurepowershell-interactive
  Enable-AzVMPSRemoting -Name MyVM1 -ResourceGroupname MyResourceGroup
  Invoke-AzVMCommand -Name MyVM1 -ResourceGroupName MyResourceGroup -Scriptblock {Get-ComputerInfo} -Credential (Get-Credential)
  ```

  U kunt ook eerst naar de map informatie gaan en het `Invoke-AzVMCommand` volgende uitvoeren.

  ```azurepowershell-interactive
  PS Azure:\> cd MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines
  PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Invoke-AzVMCommand -Scriptblock {Get-ComputerInfo} -Credential (Get-Credential)

  # You will see output similar to the following:

  PSComputerName                                          : 65.52.28.207
  RunspaceId                                              : 2c2b60da-f9b9-4f42-a282-93316cb06fe1
  WindowsBuildLabEx                                       : 14393.1066.amd64fre.rs1_release_sec.170327-1835
  WindowsCurrentVersion                                   : 6.3
  WindowsEditionId                                        : ServerDatacenter
  WindowsInstallationType                                 : Server
  WindowsInstallDateFromRegistry                          : 5/18/2017 11:26:08 PM
  WindowsProductId                                        : 00376-40000-00000-AA947
  WindowsProductName                                      : Windows Server 2016 Datacenter
  WindowsRegisteredOrganization                           :
   ...
  ```

#### <a name="interactively-log-on-to-a-remote-vm"></a>Interactief aanmelden bij een externe virtuele machine

U kunt gebruiken `Enter-AzVM` om zich interactief aan te melden bij een VM die wordt uitgevoerd in Azure.

  ```azurepowershell-interactive
  PS Azure:\> Enter-AzVM -Name MyVM1 -ResourceGroupName MyResourceGroup -Credential (Get-Credential)
  ```

U kunt ook naar de `VirtualMachines` map gaan en het `Enter-AzVM` volgende uitvoeren

  ```azurepowershell-interactive
 PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Enter-AzVM -Credential (Get-Credential)
 ```

### <a name="discover-webapps"></a>Webapps detecteren

Door in de Directory in te voeren `WebApps` , kunt u eenvoudig navigeren in uw web apps-resources

```azurepowershell-interactive
PS Azure:\MySubscriptionName> dir .\WebApps\

    Directory: Azure:\MySubscriptionName\WebApps

Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Stopped  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US

# You can use Azure cmdlets to Start/Stop your web apps
PS Azure:\MySubscriptionName\WebApps> Start-AzWebApp -Name mywebapp1 -ResourceGroupName MyResourceGroup1

Name           State    ResourceGroup        EnabledHostNames                   Location
----           -----    -------------        ----------------                   --------
mywebapp1      Running  MyResourceGroup1     {mywebapp1.azurewebsites.net ...   West US

# Refresh the current state with -Force
PS Azure:\MySubscriptionName\WebApps> dir -Force

    Directory: Azure:\MySubscriptionName\WebApps

Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Running  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US
```

## <a name="ssh"></a>SSH

Als u via SSH wilt verifiëren bij servers of Vm's, genereert u het persoonlijke sleutel paar openbaar-persoonlijk in Cloud Shell en publiceert u de open bare sleutel naar `authorized_keys` op de externe computer, zoals `/home/user/.ssh/authorized_keys` .

> [!NOTE]
> U kunt privé-en open bare SSH-sleutels maken met `ssh-keygen` en publiceren `$env:USERPROFILE\.ssh` in Cloud shell.

### <a name="using-ssh"></a>SSH gebruiken

Volg de instructies [hier](../virtual-machines/linux/quick-create-powershell.md) om een nieuwe VM-configuratie te maken met behulp van Azure PowerShell-cmdlets.
Voordat `New-AzVM` u zich aanmeldt om de implementatie te starten, moet u de open bare SSH-sleutel toevoegen aan de VM-configuratie.
De zojuist gemaakte virtuele machine bevat de open bare sleutel op de `~\.ssh\authorized_keys` locatie en schakelt daarom de SSH-sessie met referentie-gratis in voor de virtuele machine.

```azurepowershell-interactive
# Create VM config object - $vmConfig using instructions on linked page above

# Generate SSH keys in Cloud Shell
ssh-keygen -t rsa -b 2048 -f $HOME\.ssh\id_rsa 

# Ensure VM config is updated with SSH keys
$sshPublicKey = Get-Content "$HOME\.ssh\id_rsa.pub"
Add-AzVMSshPublicKey -VM $vmConfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

# Create a virtual machine
New-AzVM -ResourceGroupName <yourResourceGroup> -Location <vmLocation> -VM $vmConfig

# SSH to the VM
ssh azureuser@MyVM.Domain.Com
```

## <a name="list-available-commands"></a>Beschik bare opdrachten weer geven

Onder `Azure` station typt `Get-AzCommand` u om context afhankelijke Azure-opdrachten op te halen.

U kunt ook altijd gebruiken `Get-Command *az* -Module Az.*` om te zien wat de beschik bare Azure-opdrachten zijn.

## <a name="install-custom-modules"></a>Aangepaste modules installeren

U kunt uitvoeren `Install-Module` om modules van de [PowerShell Gallery][gallery]te installeren.

## <a name="get-help"></a>Get-Help

Typ `Get-Help` om informatie over Power shell op te halen in azure Cloud shell.

```azurepowershell-interactive
Get-Help
```

Voor een specifieke opdracht kunt u nog steeds, `Get-Help` gevolgd door een cmdlet.

```azurepowershell-interactive
Get-Help Get-AzVM
```

## <a name="use-azure-files-to-store-your-data"></a>Azure Files gebruiken om uw gegevens op te slaan

U kunt een script maken, zeggen `helloworld.ps1` en opslaan op uw `clouddrive` om het te gebruiken in shell-sessies.

```azurepowershell-interactive
cd $HOME\clouddrive
# Create a new file in clouddrive directory
New-Item helloworld.ps1
# Open the new file for editing
code .\helloworld.ps1
# Add the content, such as 'Hello World!'
.\helloworld.ps1
Hello World!
```

De volgende keer dat u Power shell gebruikt in Cloud Shell, `helloworld.ps1` bestaat het bestand in de `$HOME\clouddrive` map die uw Azure Files share koppelt.

## <a name="use-custom-profile"></a>Aangepast profiel gebruiken

U kunt uw Power shell-omgeving aanpassen door een Power shell-Profiel (en) te maken ( `profile.ps1` of `Microsoft.PowerShell_profile.ps1` ).
Sla het bestand op onder `$profile.CurrentUserAllHosts` (of `$profile.CurrentUserAllHosts` ), zodat het in elke Power shell in Cloud shell-sessie kan worden geladen.

Raadpleeg [over profielen voor meer][profile]informatie over het maken van een profiel.

## <a name="use-git"></a>Git gebruiken

Als u een Git-opslag plaats in de Cloud Shell wilt klonen, moet u een [persoonlijk toegangs token][githubtoken] maken en dit gebruiken als de gebruikers naam. Als u uw token hebt, moet u de opslag plaats als volgt klonen:

```azurepowershell-interactive
  git clone https://<your-access-token>@github.com/username/repo.git
```

## <a name="exit-the-shell"></a>De shell afsluiten

Typ `exit` om de sessie te beëindigen.

[bashqs]:quickstart.md
[gallery]:https://www.powershellgallery.com/
[customex]:https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript
[profile]: /powershell/module/microsoft.powershell.core/about/about_profiles
[azmount]: ../storage/files/storage-how-to-use-files-windows.md
[githubtoken]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
