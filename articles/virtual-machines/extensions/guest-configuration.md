---
title: Azure Policy-extensie voor gastconfiguratie
description: Meer informatie over de extensie die wordt gebruikt voor het controleren/configureren van instellingen binnen virtuele machines
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: mgreenegit
ms.author: migreene
ms.date: 04/15/2021
ms.openlocfilehash: 2fda3cc2cf9adc3a734780209a0c9cc06a04e7cf
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368619"
---
# <a name="overview-of-the-azure-policy-guest-configuration-extension"></a>Overzicht van de Azure Policy-extensie voor gastconfiguratie

De extensie gastconfiguratie is een onderdeel Azure Policy audit- en configuratiebewerkingen binnen virtuele machines uitvoert.
Beleidsregels zoals beveiligingsbasislijndefinities [voor Linux](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc9b3da7-8347-4380-8e70-0a0361d8dedd) en [Windows](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72650e9f-97bc-4b2a-ab5f-9781a9fcecbc) kunnen de instellingen in computers pas controleren als de extensie is geïnstalleerd.

## <a name="prerequisites"></a>Vereisten

De computer moet een door het systeem toegewezen beheerde identiteit hebben om de computer te kunnen verifiëren bij de [gastconfiguratieservice.](../../active-directory/managed-identities-azure-resources/overview.md)
Aan de identiteitsvereiste op een virtuele machine wordt voldaan als de volgende eigenschap is ingesteld.

  ```json
  "identity": {
    "type": "SystemAssigned"
  }
  ```

### <a name="operating-systems"></a>Besturingssystemen

Ondersteuning voor de gastconfiguratie-extensie is hetzelfde als besturingssysteemondersteuning die is gedocumenteerd [voor de end-to-end-oplossing](../../governance/policy/concepts/guest-configuration.md#supported-client-types).

### <a name="internet-connectivity"></a>Internetconnectiviteit

De agent die is geïnstalleerd door de gastconfiguratie-extensie moet inhoudspakketten kunnen bereiken die worden vermeld door toewijzingen van gastconfiguraties en de status kunnen rapporteren aan de gastconfiguratieservice.
De computer kan verbinding maken via uitgaande HTTPS via TCP-poort 443 of als er een verbinding wordt gemaakt via privénetwerken.
Zie de volgende artikelen voor meer informatie over privénetwerken:

- [Gastconfiguratie, communiceren via private link in Azure](../../governance/policy/concepts/guest-configuration.md#communicate-over-private-link-in-azure)
- [Privé-eindpunten gebruiken voor Azure Storage](../../storage/common/storage-private-endpoints.md)

## <a name="how-can-i-install-the-extension"></a>Hoe kan ik de extensie installeren?

Als u de nieuwste versie van de extensie op schaal wilt implementeren, inclusief identiteitsvereisten, wijst u de Azure Policy, Vereisten implementeren om beleidsregels voor gastconfiguratie in teschakelen op [virtuele machines toe.](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_Prerequisites.json)
Voor afzonderlijke machines kan de extensie worden geïmplementeerd met behulp van Azure CLI, PowerShell, Resource Manager-sjablonen of hulpprogramma's van derden.

De exemplaarnaam van de extensie moet worden ingesteld op 'AzurePolicyforWindows' of 'AzurePolicyforLinux', omdat de hierboven genoemde beleidsregels deze specifieke tekenreeksen vereisen.

Standaard worden alle implementaties bijgewerkt naar de nieuwste versie. De waarde van eigenschap _autoUpgradeMinorVersion_ wordt standaard ingesteld op 'true' tenzij anders aangegeven. U hoeft zich geen zorgen te maken over het bijwerken van uw code wanneer er nieuwe versies van de extensie worden uitgebracht.

### <a name="azure-cli"></a>Azure CLI

De extensie voor Linux implementeren:


```azurecli
az vm extension set  --publisher Microsoft.GuestConfiguration --name ConfigurationforLinux --extension-instance-name AzurePolicyforLinux --resource-group myResourceGroup --vm-name myVM
```

De extensie voor Windows implementeren:

```azurecli
az vm extension set  --publisher Microsoft.GuestConfiguration --name ConfigurationforWindows --extension-instance-name AzurePolicyforWindows --resource-group myResourceGroup --vm-name myVM
```

### <a name="powershell"></a>PowerShell

De extensie voor Linux implementeren:

```powershell
Set-AzVMExtension -Publisher 'Microsoft.GuestConfiguration' -Type 'ConfigurationforLinux' -Name 'AzurePolicyforLinux' -TypeHandlerVersion 1.0 -ResourceGroupName 'myResourceGroup' -Location 'myLocation' -VMName 'myVM'
```

De extensie voor Windows implementeren:

```powershell
Set-AzVMExtension -Publisher 'Microsoft.GuestConfiguration' -Type 'ConfigurationforWindows' -Name 'AzurePolicyforWindows' -TypeHandlerVersion 1.0 -ResourceGroupName 'myResourceGroup' -Location 'myLocation' -VMName 'myVM'
```

### <a name="resource-manager-template"></a>Resource Manager-sjabloon

De extensie voor Linux implementeren:

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(parameters('VMName'), '/AzurePolicyforLinux')]",
  "apiVersion": "2019-07-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.GuestConfiguration",
    "type": "ConfigurationforLinux",
    "typeHandlerVersion": "1.0"
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

De extensie voor Windows implementeren:

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(parameters('VMName'), '/AzurePolicyforWindows')]",
  "apiVersion": "2019-07-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.GuestConfiguration",
    "type": "ConfigurationforWindows",
    "typeHandlerVersion": "1.0"
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

### <a name="terraform"></a>Terraform

De extensie voor Linux implementeren:

```terraform
resource "azurerm_virtual_machine_extension" "gc" {
  name                  = "AzurePolicyforLinux"
  virtual_machine_id    = "myVMID"
  publisher             = "Microsoft.GuestConfiguration"
  type                  = "ConfigurationforLinux"
  type_handler_version  = "1.0"
}
```

De extensie voor Windows implementeren:

```terraform
resource "azurerm_virtual_machine_extension" "gc" {
  name                  = "AzurePolicyforWindows"
  virtual_machine_id    = "myVMID"
  publisher             = "Microsoft.GuestConfiguration"
  type                  = "ConfigurationforWindows"
  type_handler_version  = "1.0"
}
```

## <a name="settings"></a>Instellingen

U hoeft geen instellingen of eigenschappen voor beveiligde instellingen op te nemen in de extensie.
Al deze informatie wordt door de agent opgehaald uit resources voor toewijzing [van gastconfiguratie.](/rest/api/guestconfiguration/guestconfigurationassignments) De eigenschappen [ConfigurationUri,](/rest/api/guestconfiguration/guestconfigurationassignments/createorupdate#guestconfigurationnavigation) [Mode](/rest/api/guestconfiguration/guestconfigurationassignments/createorupdate#configurationmode)en [ConfigurationSetting](/rest/api/guestconfiguration/guestconfigurationassignments/createorupdate#configurationsetting) worden bijvoorbeeld elk beheerd per configuratie in plaats van op de VM-extensie.

## <a name="next-steps"></a>Volgende stappen

* Zie Inzicht in Azure Policy gastconfiguratie van Azure Policy meer informatie over [gastconfiguratie](../../governance/policy/concepts/guest-configuration.md)
* Zie Azure VM extensions and features for Linux (Azure VM-extensies en -functies voor Linux) voor meer informatie over de manier waarop de Linux-agent en -extensies [werken.](features-linux.md)
* Zie Azure [VM extensions and features for Windows](features-windows.md)(Azure VM-extensies en -functies voor Windows) voor meer informatie over de manier waarop de Windows-gastagent en -extensies werken.  
* Zie Overzicht van Azure Windows Virtual Machine Agent voor het installeren van de [Windows-gastagent.](agent-windows.md)  
* Zie Overzicht van Azure Linux Virtual Machine Agent voor het installeren van de [Linux-agent.](agent-linux.md)  
