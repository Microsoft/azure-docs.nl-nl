---
title: VM-extensie inschakelen met Behulp van Azure CLI
description: In dit artikel wordt beschreven hoe u extensies van virtuele machines implementeert op Azure Arc servers die worden uitgevoerd in hybride cloudomgevingen met behulp van de Azure CLI.
ms.date: 04/13/2021
ms.topic: conceptual
ms.custom: devx-track-azurecli
ms.openlocfilehash: 25e75ede30139201789cd86e6ebddda09a664eb4
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388734"
---
# <a name="enable-azure-vm-extensions-using-the-azure-cli"></a>Azure VM-extensies inschakelen met de Azure CLI

In dit artikel wordt beschreven hoe u VM-extensies, ondersteund door Azure Arc-servers, implementeert en verwijdert op een hybride Linux- of Windows-machine met behulp van de Azure CLI.

> [!NOTE]
> Azure Arc-servers biedt geen ondersteuning voor het implementeren en beheren van VM-extensies op virtuele Azure-machines. Zie het volgende artikel overzicht van VM-extensies voor [Azure-VM's.](../../virtual-machines/extensions/overview.md)

[!INCLUDE [Azure CLI Prepare your environment](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="install-the-azure-cli-extension"></a>De Azure CLI-extensie installeren

De ConnectedMachine-opdrachten worden niet verzonden als onderdeel van de Azure CLI. Voordat u de Azure CLI gebruikt voor het beheren van VM-extensies op uw hybride server die wordt beheerd door Servers met Arc, moet u de ConnectedMachine-extensie laden. Voer de volgende opdracht uit om deze op te halen:

```azurecli
az extension add --name connectedmachine
```

## <a name="enable-extension"></a>Extensie inschakelen

Als u een VM-extensie op uw [](/cli/azure/ext/connectedmachine/connectedmachine/extension#ext_connectedmachine_az_connectedmachine_extension_create) Arc-server wilt `--machine-name` `--extension-name` `--location` `--type` `settings` `--publisher` inschakelen, gebruikt u az connectedmachine extension create met de parameters , , , , en .

In het volgende voorbeeld wordt de Log Analytics VM-extensie op een Arc-server ingeschakeld:

```azurecli
az connectedmachine extension create --machine-name "myMachineName" --name "OmsAgentForLinux or MicrosoftMonitoringAgent" --location "eastus" --settings '{\"workspaceId\":\"myWorkspaceId\"}' --protected-settings '{\"workspaceKey\":\"myWorkspaceKey\"}' --resource-group "myResourceGroup" --type-handler-version "1.13" --type "OmsAgentForLinux or MicrosoftMonitoringAgent" --publisher "Microsoft.EnterpriseCloud.Monitoring" 
```

In het volgende voorbeeld wordt de aangepaste scriptextensie ingeschakeld op een Arc-server:

```azurecli
az connectedmachine extension create --machine-name "myMachineName" --name "CustomScriptExtension" --location "eastus" --type "CustomScriptExtension" --publisher "Microsoft.Compute" --settings "{\"commandToExecute\":\"powershell.exe -c \\\"Get-Process | Where-Object { $_.CPU -gt 10000 }\\\"\"}" --type-handler-version "1.10" --resource-group "myResourceGroup"
```

In het volgende voorbeeld wordt de Key Vault VM-extensie (preview) ingeschakeld op een Arc-server:

```azurecli
az connectedmachine extension create --resource-group "resourceGroupName" --machine-name "myMachineName" --location "regionName" --publisher "Microsoft.Azure.KeyVault" --type "KeyVaultForLinux or KeyVaultForWindows" --name "KeyVaultForLinux or KeyVaultForWindows" --settings '{"secretsManagementSettings": { "pollingIntervalInS": "60", "observedCertificates": ["observedCert1"] }, "authenticationSettings": { "msiEndpoint": "http://localhost:40342/metadata/identity" }}'
```

## <a name="list-extensions-installed"></a>Lijst met geïnstalleerde extensies

Gebruik az [connectedmachine extension list](/cli/azure/ext/connectedmachine/connectedmachine/extension#ext_connectedmachine_az_connectedmachine_extension_list) met de parameters en om een lijst met VM-extensies op te halen op uw Arc-server. `--machine-name` `--resource-group`

Voorbeeld:

```azurecli
az connectedmachine extension list --machine-name "myMachineName" --resource-group "myResourceGroup"
```

Standaard is de uitvoer van Azure CLI-opdrachten in JSON (JavaScript Object Notation). Als u de standaarduitvoer wilt wijzigen in een lijst of tabel, gebruikt u [bijvoorbeeld az configure --output](/cli/azure/reference-index). U kunt ook toevoegen `--output` aan een opdracht voor een een keer wijzigen in de uitvoerindeling.

In het volgende voorbeeld ziet u de gedeeltelijke JSON-uitvoer van de `az connectedmachine extension -list` opdracht :

```json
[
  {
    "autoUpgradingMinorVersion": "false",
    "forceUpdateTag": null,
    "id": "/subscriptions/subscriptionId/resourceGroups/resourceGroupName/providers/Microsoft.HybridCompute/machines/SVR01/extensions/DependencyAgentWindows",
    "location": "eastus",
    "name": "DependencyAgentWindows",
    "namePropertiesInstanceViewName": "DependencyAgentWindows",
```

## <a name="remove-an-installed-extension"></a>Een geïnstalleerde extensie verwijderen

Als u een geïnstalleerde VM-extensie op uw Arc-server wilt verwijderen, gebruikt u [az connectedmachine extension delete met](/cli/azure/ext/connectedmachine/connectedmachine/extension#ext_connectedmachine_az_connectedmachine_extension_delete) de parameters , en `--extension-name` `--machine-name` `--resource-group` .

Als u bijvoorbeeld de Log Analytics VM-extensie voor Linux wilt verwijderen, moet u de volgende opdracht uitvoeren:

```azurecli
az connectedmachine extension delete --machine-name "myMachineName" --name "OmsAgentForLinux" --resource-group "myResourceGroup"
```

## <a name="next-steps"></a>Volgende stappen

- U kunt VM-extensies implementeren, beheren en verwijderen met behulp van [de Azure PowerShell](manage-vm-extensions-powershell.md), uit de [Azure Portal](manage-vm-extensions-portal.md)of [Azure Resource Manager sjablonen.](manage-vm-extensions-template.md)

- Informatie over probleemoplossing vindt u in de handleiding Problemen met [VM-extensies oplossen.](troubleshoot-vm-extensions.md)

- Lees het artikel Overzicht van de Azure CLI [VM-extensie](/cli/azure/ext/connectedmachine/connectedmachine/extension) voor meer informatie over de opdrachten.
