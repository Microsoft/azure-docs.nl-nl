---
title: VM-extensie inschakelen met Azure PowerShell
description: In dit artikel wordt beschreven hoe u extensies van virtuele machines implementeert op Azure Arc servers die worden uitgevoerd in hybride cloudomgevingen met behulp van Azure PowerShell.
ms.date: 04/13/2021
ms.topic: conceptual
ms.openlocfilehash: 0cb854c9745e8bd7eef35c6f6467c284a6327349
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388581"
---
# <a name="enable-azure-vm-extensions-using-azure-powershell"></a>Azure VM-extensies inschakelen met Azure PowerShell

In dit artikel wordt beschreven hoe u Azure VM-extensies, ondersteund door Azure Arc-servers, implementeert en verwijdert op een linux- of Windows-hybride machine met behulp van Azure PowerShell.

> [!NOTE]
> Azure Arc-servers biedt geen ondersteuning voor het implementeren en beheren van VM-extensies op virtuele Azure-machines. Zie het volgende artikel overzicht van VM-extensies voor [Azure-VM's.](../../virtual-machines/extensions/overview.md)

## <a name="prerequisites"></a>Vereisten

- Een computer met Azure PowerShell. Zie Install and configure Azure PowerShell [voor instructies.](/powershell/azure/)

Voordat u Azure PowerShell voor het beheren van VM-extensies op uw hybride server die wordt beheerd door Servers met Arc, moet u de `Az.ConnectedMachine` module installeren. Voer de volgende opdracht uit op uw Arc-server:

`Install-Module -Name Az.ConnectedMachine`.

Wanneer de installatie is voltooid, wordt het volgende bericht geretourneerd:

`The installed extension `Az.ConnectedMachine` is experimental and not covered by customer support. Please use with discretion.`

## <a name="enable-extension"></a>Extensie inschakelen

Als u een VM-extensie op uw Arc-server wilt inschakelen, gebruikt u [New-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/new-azconnectedmachineextension) met de `-Name` parameters , , , , , en `-ResourceGroupName` `-MachineName` `-Location` `-Publisher` `ExtensionType` `-Settings` .

In het volgende voorbeeld wordt de Log Analytics VM-extensie ingeschakeld op een Linux-server met Arc:

```powershell
PS C:\> $Setting = @{ "workspaceId" = "workspaceId" }
PS C:\> $protectedSetting = @{ "workspaceKey" = "workspaceKey" }
PS C:\> New-AzConnectedMachineExtension -Name OMSLinuxAgent -ResourceGroupName "myResourceGroup" -MachineName "myMachine" -Location "eastus" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -TypeHandlerVersion "1.10" -Settings $Setting -ProtectedSetting $protectedSetting -ExtensionType "OmsAgentForLinux"
```

Als u de Log Analytics VM-extensie wilt inschakelen op een Windows-server met Arc, wijzigt u de waarde voor `-ExtensionType` de parameter in in het vorige `"MicrosoftMonitoringAgent"` voorbeeld.

In het volgende voorbeeld wordt de aangepaste scriptextensie ingeschakeld op een Arc-server:

```powershell
PS C:\> $Setting = @{ "commandToExecute" = "powershell.exe -c Get-Process" }
PS C:\> New-AzConnectedMachineExtension -Name custom -ResourceGroupName myResourceGroup -MachineName myMachineName -Location eastus -Publisher "Microsoft.Compute" -TypeHandlerVersion 1.10 -Settings $Setting -ExtensionType CustomScriptExtension
```

### <a name="key-vault-vm-extension-preview"></a>Key Vault VM-extensie (preview)

> [!WARNING]
> PowerShell-clients voegen vaak toe aan in de settings.jswaardoor de `\` akvvm_service mislukt met een `"` fout: `[CertificateManagementConfiguration] Failed to parse the configuration settings with:not an object.`

In het volgende voorbeeld wordt de Key Vault VM-extensie (preview) ingeschakeld op een Arc-server:

```powershell
# Build settings
    $settings = @{
      secretsManagementSettings = @{
       observedCertificates = @(
        "observedCert1"
       )
      certificateStoreLocation = "myMachineName" # For Linux use "/var/lib/waagent/Microsoft.Azure.KeyVault.Store/"
      certificateStore = "myCertificateStoreName"
      pollingIntervalInS = "pollingInterval"
      }
    authenticationSettings = @{
     msiEndpoint = "http://localhost:40342/metadata/identity"
     }
    }

    $resourceGroup = "resourceGroupName"
    $machineName = "myMachineName"
    $location = "regionName"

    # Start the deployment
    New-AzConnectedMachineExtension -ResourceGroupName $resourceGRoup -Location $location -MachineName $machineName -Name "KeyVaultForWindows or KeyVaultforLinux" -Publisher "Microsoft.Azure.KeyVault" -ExtensionType "KeyVaultforWindows or KeyVaultforLinux" -Setting (ConvertTo-Json $settings)
```

## <a name="list-extensions-installed"></a>Lijst met geïnstalleerde extensies

Gebruik [Get-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/get-azconnectedmachineextension) met de parameters en om een lijst met de VM-extensies op uw `-MachineName` Arc-server op te `-ResourceGroupName` halen.

Voorbeeld:

```powershell
Get-AzConnectedMachineExtension -ResourceGroupName myResourceGroup -MachineName myMachineName

Name    Location  PropertiesType        ProvisioningState
----    --------  --------------        -----------------
custom  westus2   CustomScriptExtension Succeeded
```

## <a name="remove-an-installed-extension"></a>Een geïnstalleerde extensie verwijderen

Als u een geïnstalleerde VM-extensie op uw Arc-server wilt verwijderen, gebruikt u [Remove-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/remove-azconnectedmachineextension) met `-Name` de parameters , en `-MachineName` `-ResourceGroupName` .

Als u bijvoorbeeld de Log Analytics VM-extensie voor Linux wilt verwijderen, moet u de volgende opdracht uitvoeren:

```powershell
Remove-AzConnectedMachineExtension -MachineName myMachineName -ResourceGroupName myResourceGroup -Name OmsAgentforLinux
```

## <a name="next-steps"></a>Volgende stappen

- U kunt VM-extensies implementeren, beheren en verwijderen met behulp van de [Azure CLI](manage-vm-extensions-cli.md)vanuit de [Azure Portal](manage-vm-extensions-portal.md)of [Azure Resource Manager sjablonen.](manage-vm-extensions-template.md)

- Informatie over probleemoplossing vindt u in de handleiding Problemen met [VM-extensies oplossen.](troubleshoot-vm-extensions.md)
