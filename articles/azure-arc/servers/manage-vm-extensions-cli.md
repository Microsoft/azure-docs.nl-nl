---
title: VM-extensie inschakelen met behulp van Azure CLI
description: In dit artikel wordt beschreven hoe u virtuele-machine uitbreidingen implementeert voor Azure Arc-servers die worden uitgevoerd in hybride Cloud omgevingen met behulp van de Azure CLI.
ms.date: 11/06/2020
ms.topic: conceptual
ms.custom: devx-track-azurecli
ms.openlocfilehash: bf0a3e0940efc7e79adbe9f763ffdf34ea690fac
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94833262"
---
# <a name="enable-azure-vm-extensions-using-the-azure-cli"></a>Azure-VM-extensies inschakelen met behulp van Azure CLI

In dit artikel wordt beschreven hoe u Azure-VM-extensies die worden ondersteund door Azure Arc-servers, kunt implementeren en verwijderen naar een virtuele Linux-of Windows-machine met behulp van Azure CLI.

[!INCLUDE [Azure CLI Prepare your environment](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="install-the-azure-cli-extension"></a>De Azure CLI-extensie installeren

De ConnectedMachine-opdrachten worden niet verzonden als onderdeel van de Azure CLI. Voordat u de Azure CLI gebruikt om VM-extensies te beheren op uw hybride server die wordt beheerd door servers met Arc-functionaliteit, moet u de ConnectedMachine-extensie laden. Voer de volgende opdracht uit om het te verkrijgen:

```azurecli
az extension add --name connectedmachine
```

## <a name="enable-extension"></a>Extensie inschakelen

Als u een VM-extensie op uw server met Arc wilt inschakelen, gebruikt u [AZ connectedmachine machine-extension Create](/cli/azure/ext/connectedmachine/connectedmachine/machine-extension#ext_connectedmachine_az_connectedmachine_machine_extension_create) met de `--machine-name` `--extension-name` `--location` `--type` `settings` `--publisher` para meters,,,, en.

In het volgende voor beeld wordt de Log Analytics VM-extensie ingeschakeld op een Linux-server die is ingeschakeld voor Arc:

```azurecli
az connectedmachine machine-extension create --machine-name "myMachineName" --name "OmsAgentforLinux" --location "eastus" --type "CustomScriptExtension" --publisher "Microsoft.EnterpriseCloud.Monitoring" --settings "{\"workspaceId\":\"workspaceId"}" --protected-settings "{\workspaceKey\":"\workspaceKey"} --type-handler-version "1.10" --resource-group "myResourceGroup"
```

In het volgende voor beeld wordt de aangepaste script extensie ingeschakeld op een server met Arc-functionaliteit:

```azurecli
az connectedmachine machine-extension create --machine-name "myMachineName" --name "CustomScriptExtension" --location "eastus" --type "CustomScriptExtension" --publisher "Microsoft.Compute" --settings "{\"commandToExecute\":\"powershell.exe -c \\\"Get-Process | Where-Object { $_.CPU -gt 10000 }\\\"\"}" --type-handler-version "1.10" --resource-group "myResourceGroup"
```

In het volgende voor beeld wordt de Key Vault VM-extensie (preview) ingeschakeld op een server met Arc-functionaliteit:

```azurecli
az connectedmachine machine-extension create --resource-group "resourceGroupName" --machine-name "myMachineName" --location "regionName" --publisher "Microsoft.Azure.KeyVault" --type "KeyVaultForLinux or KeyVaultForWindows" --name "KeyVaultForLinux or KeyVaultForWindows" --settings '{"secretsManagementSettings": { "pollingIntervalInS": "60", "observedCertificates": ["observedCert1"] }, "authenticationSettings": { "msiEndpoint": "http://localhost:40342/metadata/identity" }}'
```

## <a name="list-extensions-installed"></a>Lijst met geïnstalleerde uitbrei dingen

Als u een lijst wilt weer geven met de VM-extensies op de server waarop u een Arc hebt ingeschakeld, gebruikt u [AZ connectedmachine machine Extension List](/cli/azure/ext/connectedmachine/connectedmachine/machine-extension#ext_connectedmachine_az_connectedmachine_machine_extension_list) met de `--machine-name` `--resource-group` para meters en.

Voorbeeld:

```azurecli
az connectedmachine machine-extension list --machine-name "myMachineName" --resource-group "myResourceGroup"
```

De uitvoer van Azure CLI-opdrachten bevindt zich standaard in JSON (JavaScript Object Notation). Als u de standaard uitvoer wilt wijzigen in een lijst of tabel, gebruikt u bijvoorbeeld [AZ Configure--Output](/cli/azure/reference-index). U kunt ook toevoegen `--output` aan elke opdracht voor eenmalige wijziging in uitvoer indeling.

In het volgende voor beeld wordt de gedeeltelijke JSON-uitvoer van de opdracht weer gegeven `az connectedmachine machine-extension -list` :

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

Als u een geïnstalleerde VM-extensie wilt verwijderen van de server waarop u een Arc hebt ingeschakeld, gebruikt u [AZ connectedmachine machine extension delete](/cli/azure/ext/connectedmachine/connectedmachine/machine-extension#ext_connectedmachine_az_connectedmachine_machine_extension_delete) met de `--extension-name` `--machine-name` `--resource-group` para meters.

Als u de Log Analytics VM-extensie voor Linux bijvoorbeeld wilt verwijderen, voert u de volgende opdracht uit:

```azurecli
az connectedmachine machine-extension delete --machine-name "myMachineName" --name "OmsAgentforLinux" --resource-group "myResourceGroup"
```

## <a name="next-steps"></a>Volgende stappen

- U kunt VM-extensies implementeren, beheren en verwijderen met behulp van de [Azure PowerShell](manage-vm-extensions-powershell.md), vanuit de [Azure Portal](manage-vm-extensions-portal.md)of [Azure Resource Manager sjablonen](manage-vm-extensions-template.md).

- Informatie over probleem oplossing vindt u in de [gids voor het oplossen van problemen met VM-extensies](troubleshoot-vm-extensions.md).
