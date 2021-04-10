---
title: Azure DSC-extensie voor Linux
description: Installeert OMI-en DSC-pakketten zodat een virtuele Azure Linux-machine kan worden geconfigureerd met behulp van desired state Configuration.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: mgoedtel
ms.author: magoedte
ms.collection: linux
ms.date: 06/12/2018
ms.openlocfilehash: 3a9478f93a2a6f8e31be39a58a18e0e394b9d5c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102560097"
---
# <a name="dsc-extension-for-linux-microsoftostcextensionsdscforlinux"></a>DSC-extensie voor Linux (micro soft. OSTCExtensions. DSCForLinux)

Desired state Configuration (DSC) is een beheer platform dat u kunt gebruiken voor het beheren van uw IT-en ontwikkelings infrastructuur met configuratie als code.

> [!NOTE]
> De DSC-extensie voor Linux en de [log Analytics extensie voor virtuele machines voor Linux](./oms-linux.md) bieden momenteel een conflict en worden niet ondersteund in een side-by-side-configuratie. Gebruik niet de twee oplossingen tegelijk op dezelfde VM.

De DSCForLinux-extensie wordt gepubliceerd en ondersteund door micro soft. Met de uitbrei ding wordt de OMI-en DSC-agent geïnstalleerd op virtuele machines van Azure. De DSC-extensie kan ook de volgende acties uitvoeren:

- Registreer de virtuele Linux-machine in een Azure Automation-account om configuraties te halen uit de Azure Automation-Service (Registreer ExtensionAction).
- MOF-configuraties naar de Linux-VM pushen (push ExtensionAction).
- Meta MOF-configuratie Toep assen op de Linux-VM om een pull-server te configureren om knooppunt configuratie (pull-ExtensionAction) te kunnen halen.
- Aangepaste DSC-modules installeren op de virtuele Linux-machine (ExtensionAction installeren).
- Verwijder aangepaste DSC-modules van de virtuele Linux-machine (Verwijder ExtensionAction).

## <a name="prerequisites"></a>Vereisten

### <a name="operating-system"></a>Besturingssysteem

Voor knoop punten waarop Linux wordt uitgevoerd, ondersteunt de DSC Linux-extensie alle Linux-distributies die worden vermeld in de [Power shell DSC-documentatie](/powershell/scripting/dsc/getting-started/lnxgettingstarted).

### <a name="internet-connectivity"></a>Internetconnectiviteit

Voor de DSCForLinux-extensie moet de virtuele doel machine zijn verbonden met internet. De registratie-uitbrei ding vereist bijvoorbeeld connectiviteit met de Automation-Service.
Voor andere acties, zoals pull, pull, moet de installatie verbinding hebben met Azure Storage en GitHub. Dit is afhankelijk van de instellingen van de klant.

## <a name="extension-schema"></a>Extensieschema

### <a name="public-configuration"></a>Open bare configuratie

Hier vindt u alle ondersteunde para meters voor de open bare configuratie:

* `FileUri`: (optioneel, String) de URI van het MOF-bestand, meta MOF-bestand of aangepaste zip-bestand van de resource.
* `ResourceName`: (optioneel, String) de naam van de aangepaste resource module.
* `ExtensionAction`: (optioneel, String) geeft aan wat een extensie doet. Geldige waarden zijn registreren, pushen, pull, installeren en verwijderen. Als deze niet wordt opgegeven, wordt standaard een push-actie beschouwd.
* `NodeConfigurationName`: (optioneel, String) de naam van een knooppunt configuratie die moet worden toegepast.
* `RefreshFrequencyMins`: (optioneel, int) Hiermee geeft u op hoe vaak (in minuten) DSC probeert de configuratie van de pull-server op te halen.
       Als de configuratie op de pull-server afwijkt van het huidige op het doel knooppunt, wordt deze gekopieerd naar de in behandeling zijnde Store en toegepast.
* `ConfigurationMode`: (optioneel, teken reeks) Hiermee geeft u op hoe DSC de configuratie moet Toep assen. Geldige waarden zijn ApplyOnly, ApplyAndMonitor en ApplyAndAutoCorrect.
* `ConfigurationModeFrequencyMins`: (optioneel, int) Hiermee geeft u op hoe vaak (in minuten) DSC ervoor zorgt dat de configuratie de gewenste status heeft.

> [!NOTE]
> Als u een eerdere versie dan 2,3 gebruikt, is de modus parameter hetzelfde als ExtensionAction. De modus lijkt een overbelaste term te zijn. Om Verwar ring te voor komen, wordt ExtensionAction vanaf versie 2,3 gebruikt. Voor achterwaartse compatibiliteit ondersteunt de uitbrei ding zowel de modus als de ExtensionAction.
>

### <a name="protected-configuration"></a>Beveiligde configuratie

Hier vindt u alle ondersteunde beveiligings configuratie parameters:

* `StorageAccountName`: (optioneel, String) de naam van het opslag account dat het bestand bevat
* `StorageAccountKey`: (optioneel, String) de sleutel van het opslag account dat het bestand bevat
* `RegistrationUrl`: (optioneel, String) de URL van het Azure Automation-account
* `RegistrationKey`: (optioneel, String) de toegangs sleutel van het Azure Automation-account

## <a name="scenarios"></a>Scenario's

### <a name="register-an-azure-automation-account"></a>Een Azure Automation-account registreren

protected.jsop
```json
{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}
```
public.jsop
```json
{
  "ExtensionAction" : "Register",
  "NodeConfigurationName" : "<node-configuration-name>",
  "RefreshFrequencyMins" : "<value>",
  "ConfigurationMode" : "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins" : "<value>"
}
```

Power shell-indeling
```powershell
$privateConfig = '{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}'

$publicConfig = '{
  "ExtensionAction" : "Register",
  "NodeConfigurationName": "<node-configuration-name>",
  "RefreshFrequencyMins": "<value>",
  "ConfigurationMode": "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins": "<value>"
}'
```

### <a name="apply-an-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>Een MOF-configuratie bestand (in een Azure-opslag account) Toep assen op de VM

protected.jsop
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.jsop
```json
{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}
```

Power shell-indeling
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}'
```

### <a name="apply-an-mof-configuration-file-in-public-storage-to-the-vm"></a>Een MOF-configuratie bestand (in open bare opslag) Toep assen op de VM

public.jsop
```json
{
  "FileUri": "<mof-file-uri>"
}
```

Power shell-indeling
```powershell
$publicConfig = '{
  "FileUri": "<mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>Een meta MOF-configuratie bestand (in een Azure-opslag account) Toep assen op de VM

protected.jsop
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.jsop
```json
{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}
```

Power shell-indeling
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-public-storage-to-the-vm"></a>Een meta MOF-configuratie bestand (in open bare opslag) Toep assen op de VM

public.jsop

```json
{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}
```

Power shell-indeling

```powershell
$publicConfig = '{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}'
```

### <a name="install-a-custom-resource-module-a-zip-file-in-an-azure-storage-account-to-the-vm"></a>Een aangepaste resource module (een zip-bestand in een Azure Storage-account) installeren op de VM

protected.jsop

```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.jsop

```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```

Power shell-indeling
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="install-a-custom-resource-module-a-zip-file-in-public-storage-to-the-vm"></a>Een aangepaste resource module (een zip-bestand in de open bare opslag) installeren op de VM

public.jsop

```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}

```

Power shell-indeling

```powershell
$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="remove-a-custom-resource-module-from-the-vm"></a>Een aangepaste resource module verwijderen van de VM

public.jsop

```json
{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}
```

Power shell-indeling

```powershell
$publicConfig = '{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}'
```

## <a name="template-deployment"></a>Sjabloonimplementatie

Azure VM-extensies kunnen worden geïmplementeerd met Azure Resource Manager sjablonen. Sjablonen zijn ideaal wanneer u een of meer virtuele machines implementeert waarvoor de configuratie na implementatie vereist is, zoals het onboarden van Azure Automation.

Het voor beeld van een resource manager-sjabloon is [201-DSC-Linux-Azure-Storage-on-Ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-azure-storage-on-ubuntu) en [201-DSC-Linux-Public-Storage-on-Ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-public-storage-on-ubuntu).

Zie [Azure Resource Manager sjablonen ontwerpen](../../azure-resource-manager/templates/template-syntax.md)voor meer informatie over de Azure Resource Manager sjabloon.

## <a name="azure-cli-deployment"></a>Implementatie van Azure CLI

### <a name="use-azure-cliazure-cli"></a>[Azure CLI] [Azure-cli] gebruiken

Voordat u de DSCForLinux-extensie implementeert, moet u uw `public.json` en `protected.json` volgens de verschillende scenario's in sectie 3 configureren.

#### <a name="classic"></a>Klassiek

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

De klassieke implementatie modus wordt ook Azure Service Management-modus genoemd. U kunt overschakelen naar het bestand door het volgende uit te voeren:

```
$ azure config mode asm
```

U kunt de DSCForLinux-extensie implementeren door het volgende uit te voeren:

```
$ azure vm extension set <vm-name> DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```

Voor meer informatie over de nieuwste beschik bare extensie versie voert u de volgende handelingen uit:

```
$ azure vm extension list
```

#### <a name="resource-manager"></a>Resource Manager

U kunt overschakelen naar Azure Resource Manager modus door uit te voeren:

```
$ azure config mode arm
```

U kunt de DSCForLinux-extensie implementeren door het volgende uit te voeren:

```
$ azure vm extension set <resource-group> <vm-name> \
DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```

> [!NOTE]
> In Azure Resource Manager modus `azure vm extension list` is nu niet beschikbaar.
>

### <a name="use-azure-powershellazure-powershell"></a>[Azure PowerShell] [Azure-Power Shell] gebruiken

#### <a name="classic"></a>Klassiek

U kunt zich aanmelden bij uw Azure-account in de Azure Service Management-modus door het volgende uit te voeren:

```powershell
Add-AzureAccount
```

En implementeer de DSCForLinux-extensie door het volgende uit te voeren:

```powershell
$vmname = '<vm-name>'
$vm = Get-AzureVM -ServiceName $vmname -Name $vmname
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Wijzig de inhoud van $privateConfig en $publicConfig volgens de verschillende scenario's in de vorige sectie.

```
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'
```

```
$publicConfig = '{
  "ExtensionAction": "Push",
  "FileUri": "<mof-file-uri>"
}'
```

```powershell
Set-AzureVMExtension -ExtensionName $extensionName -VM $vm -Publisher $publisher `
  -Version $version -PrivateConfiguration $privateConfig `
  -PublicConfiguration $publicConfig | Update-AzureVM
```

#### <a name="resource-manager"></a>Resource Manager

U kunt u aanmelden bij uw Azure-account in Azure Resource Manager modus door uit te voeren:

```powershell
Login-AzAccount
```

Zie [Azure-resources beheren met behulp van Azure PowerShell](../../azure-resource-manager/management/manage-resources-powershell.md)voor meer informatie over het gebruik van Azure PowerShell met Azure Resource Manager.

U kunt de DSCForLinux-extensie implementeren door het volgende uit te voeren:

```powershell
$rgName = '<resource-group-name>'
$vmName = '<vm-name>'
$location = '< location>'
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Wijzig de inhoud van $privateConfig en $publicConfig volgens de verschillende scenario's in de vorige sectie.

```
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'
```

```
$publicConfig = '{
  "ExtensionAction": "Push",
  "FileUri": "<mof-file-uri>"
}'
```

```powershell
Set-AzVMExtension -ResourceGroupName $rgName -VMName $vmName -Location $location `
  -Name $extensionName -Publisher $publisher -ExtensionType $extensionName `
  -TypeHandlerVersion $version -SettingString $publicConfig -ProtectedSettingString $privateConfig
```

## <a name="troubleshoot-and-support"></a>Problemen oplossen en ondersteuning

### <a name="troubleshoot"></a>Problemen oplossen

Gegevens over de status van uitbreidings implementaties kunnen worden opgehaald uit de Azure Portal en met behulp van de Azure CLI. Als u de implementatie status van extensies voor een bepaalde virtuele machine wilt bekijken, voert u de volgende opdracht uit met behulp van de Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Uitvoer voor uitvoering van extensie wordt vastgelegd in het volgende bestand:

```
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Fout code: 51 staat voor een niet-ondersteunde distributie of een niet-ondersteunde extensie actie.
In sommige gevallen kan de DSC Linux-uitbrei ding OMI niet installeren wanneer er al een hogere versie van OMI op de computer aanwezig is. [fout bericht: (000003) downgrade niet toegestaan]

### <a name="support"></a>Ondersteuning

Als u op elk gewenst moment meer hulp nodig hebt, neemt u contact op met de Azure-experts op [MSDN Azure en stack overflow forums](https://azure.microsoft.com/support/community/). U kunt ook een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/)en selecteer **ondersteuning verkrijgen**. Lees de [Veelgestelde vragen over ondersteuning voor Microsoft Azure](https://azure.microsoft.com/support/faq/)voor meer informatie over het gebruik van Azure-ondersteuning.

## <a name="next-steps"></a>Volgende stappen

Zie [virtuele machines en functies voor Linux](features-linux.md)voor meer informatie over uitbrei dingen.
