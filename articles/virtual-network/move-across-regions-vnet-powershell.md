---
title: Een virtueel Azure-netwerk verplaatsen naar een andere Azure-regio met behulp van Azure PowerShell
description: Verplaats een virtueel Azure-netwerk van de ene Azure-regio naar een andere met behulp van een resource manager-sjabloon en Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: fa89a458b23d18bc6fddfca9cf6d9f2a16f91669
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98934920"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-azure-powershell"></a>Een virtueel Azure-netwerk verplaatsen naar een andere regio met behulp van Azure PowerShell

Er zijn verschillende scenario's voor het verplaatsen van een bestaand virtueel Azure-netwerk van de ene regio naar een andere. Stel dat u een virtueel netwerk met dezelfde configuratie wilt maken voor testen en beschik baarheid als uw bestaande virtuele netwerk. Het is ook mogelijk dat u een virtueel netwerk voor productie naar een andere regio wilt verplaatsen als onderdeel van de planning voor herstel na nood gevallen.

U kunt een Azure Resource Manager sjabloon gebruiken om de verplaatsing van het virtuele netwerk naar een andere regio te volt ooien. Hiervoor exporteert u het virtuele netwerk naar een sjabloon, wijzigt u de para meters zodat deze overeenkomen met de doel regio en implementeert u de sjabloon vervolgens in de nieuwe regio. Zie [resource groepen exporteren naar sjablonen](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)voor meer informatie over Resource Manager-sjablonen.


## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat het virtuele netwerk zich in de Azure-regio bevindt waaruit u wilt verplaatsen.

- Als u een virtueel netwerk wilt exporteren en een sjabloon wilt implementeren om een virtueel netwerk te maken in een andere regio, moet u de rol netwerk bijdrager of hoger hebben.

- Peerings voor virtuele netwerken worden niet opnieuw gemaakt en mislukken als ze nog steeds in de sjabloon aanwezig zijn. Voordat u de sjabloon exporteert, moet u alle virtuele netwerk peers verwijderen. U kunt ze vervolgens opnieuw instellen nadat het virtuele netwerk is verplaatst.
    
- Identificeer de bronnetwerkindeling en alle resources die u momenteel gebruikt. Deze indeling bevat, maar is niet beperkt tot load balancers, netwerk beveiligings groepen (Nsg's) en open bare Ip's.

- Controleer of u met uw Azure-abonnement virtuele netwerken in de doel regio kunt maken. Neem contact op met de ondersteuning om het vereiste quotum in te scha kelen.

- Zorg ervoor dat uw abonnement voldoende bronnen heeft ter ondersteuning van het toevoegen van virtuele netwerken voor dit proces. Zie [Azure-abonnement- en servicelimieten, quota en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) voor meer informatie.


## <a name="prepare-for-the-move"></a>Voorbereiden voor de verhuizing
In deze sectie gaat u het virtuele netwerk voorbereiden voor de verplaatsing met behulp van een resource manager-sjabloon. Vervolgens verplaatst u het virtuele netwerk naar de doel regio met behulp van Azure PowerShell-opdrachten.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ga als volgt te werk om het virtuele netwerk te exporteren en het virtuele doel netwerk te implementeren met behulp van Power shell:

1. Meld u aan bij uw Azure-abonnement met de opdracht [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) en volg de instructies op het scherm:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. Haal de bron-ID op van het virtuele netwerk dat u wilt verplaatsen naar de doel regio en plaats deze in een variabele met behulp van [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork):

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id
    ```

1. Exporteer het virtuele bron netwerk naar een. JSON-bestand in de map waar u de opdracht [export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup)uitvoert:
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

1. Het gedownloade bestand heeft dezelfde naam als de resource groep waaruit de resource is geëxporteerd. Zoek het *\<resource-group-name> JSON* -bestand, dat u met de opdracht hebt geëxporteerd, en open het in uw editor:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

1. Als u de para meter van de naam van het virtuele netwerk wilt bewerken, wijzigt u de eigenschap **DefaultValue** van de virtuele bron netwerk naam in de naam van het virtuele netwerk van het doel. Zorg ervoor dat u de naam tussen aanhalings tekens plaatst.
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

1. Als u de doel regio wilt bewerken waar het virtuele netwerk wordt verplaatst, wijzigt u de eigenschap **Location** onder resources:

    ```json
    "resources": [
                {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region>",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                        "addressSpace": {
                            "addressPrefixes": [
                                "10.0.0.0/16"
                            ]
                        },

    ```
  
1. Als u regio codes wilt ophalen, kunt u de Azure PowerShell cmdlet [Get-AzLocation](/powershell/module/az.resources/get-azlocation) gebruiken door de volgende opdracht uit te voeren:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    ```

1. Beschrijving U kunt ook andere para meters in het *\<resource-group-name> JSON* -bestand wijzigen, afhankelijk van uw vereisten:

    * **Adres ruimte**: voordat u het bestand opslaat, kunt u de adres ruimte van het virtuele netwerk wijzigen door de sectie **resources**  >  **addressSpace** te wijzigen en de eigenschap **addressPrefixes** te wijzigen:

        ```json
                "resources": [
                    {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                    "addressSpace": {
                        "addressPrefixes": [
                        "10.0.0.0/16"
                        ]
                    },
        ```

    * **Subnet**: u kunt wijzigen of toevoegen aan de naam van het subnet en de adres ruimte van het subnet door de sectie **subnetten** van het bestand te wijzigen. U kunt de naam van het subnet wijzigen door de eigenschap **name** te wijzigen. En u kunt de adres ruimte van het subnet wijzigen door de eigenschap **addressPrefix** te wijzigen:

        ```json
                "subnets": [
                    {
                    "name": "subnet-1",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.0.0/24",
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    },
                    {
                    "name": "GatewaySubnet",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.1.0/29",
                    "serviceEndpoints": [],
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    }

                ]
        ```

        Als u het adres voorvoegsel wilt wijzigen, bewerkt u het bestand op twee plaatsen: in de code in de voor gaande sectie en in het gedeelte **type** van de volgende code. Wijzig de eigenschap **addressPrefix** in de volgende code zodat deze overeenkomt met de eigenschap **addressPrefix** in de code in de voor gaande sectie.

        ```json
         "type": "Microsoft.Network/virtualNetworks/subnets",
           "apiVersion": "2019-06-01",
           "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/GatewaySubnet')]",
              "dependsOn": [
                 "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                   ],
              "properties": {
                 "provisioningState": "Succeeded",
                 "addressPrefix": "10.0.1.0/29",
                 "serviceEndpoints": [],
                 "delegations": [],
                 "privateEndpointNetworkPolicies": "Enabled",
                 "privateLinkServiceNetworkPolicies": "Enabled"
                  }
                 },
                  {
                  "type": "Microsoft.Network/virtualNetworks/subnets",
                  "apiVersion": "2019-06-01",
                  "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/subnet-1')]",
                     "dependsOn": [
                        "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                          ],
                     "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "10.0.0.0/24",
                        "delegations": [],
                        "privateEndpointNetworkPolicies": "Enabled",
                        "privateLinkServiceNetworkPolicies": "Enabled"
                         }
                  }
         ]
        ```

1. Sla het *\<resource-group-name> JSON* -bestand op.

1. Maak een resource groep in de doel regio voor het virtuele doel netwerk dat moet worden geïmplementeerd met behulp van [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
1. Implementeer het bewerkte *\<resource-group-name> . json* -bestand in de resource groep die u in de vorige stap hebt gemaakt met behulp van [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    ```

1. Gebruik [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup) en [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork)om te controleren of de resources zijn gemaakt in de doel regio:
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>
    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>
    ```

## <a name="delete-the-virtual-network-or-resource-group"></a>Het virtuele netwerk of de resource groep verwijderen 

Nadat u het virtuele netwerk hebt geïmplementeerd, moet u de resource groep die u hebt gemaakt in de doel regio en het verplaatste virtuele netwerk verwijderen om te beginnen of het virtuele netwerk te negeren in de doel regio. 

Gebruik [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)om de resource groep te verwijderen:

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>
```

## <a name="clean-up"></a>Opschonen

Ga op een van de volgende manieren te werk om uw wijzigingen door te voeren en de virtuele netwerk verplaatsing te volt ooien:

* Verwijder de resource groep met behulp van [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup):

    ```azurepowershell-interactive

    Remove-AzResourceGroup -Name <source-resource-group-name>
    ```

* Verwijder het virtuele bron netwerk met behulp van [Remove-AzVirtualNetwork](/powershell/module/az.network/remove-azvirtualnetwork):  
    ``` azurepowershell-interactive

    Remove-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>
    ```

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een virtueel netwerk verplaatst van de ene regio naar een andere met behulp van Power shell en vervolgens de overbodige bron bronnen verwijderd. Zie voor meer informatie over het verplaatsen van resources tussen regio's en herstel na nood gevallen in Azure:

- [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Virtuele Azure-machines naar een andere regio verplaatsen](../site-recovery/azure-to-azure-tutorial-migrate.md)
