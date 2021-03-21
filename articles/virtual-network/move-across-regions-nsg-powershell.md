---
title: Een Azure-netwerk beveiligings groep (NSG) verplaatsen naar een andere Azure-regio met behulp van Azure PowerShell
description: Gebruik Azure Resource Manager sjabloon om de Azure-netwerk beveiligings groep van de ene Azure-regio naar de andere te verplaatsen met behulp van Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: ad73ef03aa9623fb724f1397697fac18f659a90c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98934997"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-azure-powershell"></a>Een Azure-netwerk beveiligings groep (NSG) verplaatsen naar een andere regio met behulp van Azure PowerShell

Er zijn verschillende scenario's waarin u uw bestaande Nsg's van de ene naar de andere regio wilt verplaatsen. U kunt bijvoorbeeld een NSG maken met dezelfde configuratie-en beveiligings regels om te testen. Het is ook mogelijk dat u een NSG naar een andere regio wilt verplaatsen als onderdeel van de planning voor nood herstel.

Azure-beveiligingsgroepen kunnen niet worden verplaatst van de ene regio naar een andere. U kunt echter een Azure Resource Manager sjabloon gebruiken om de bestaande configuratie-en beveiligings regels van een NSG te exporteren.  U kunt de resource vervolgens in een andere regio zetten door de NSG naar een sjabloon te exporteren, de para meters te wijzigen zodat deze overeenkomen met de doel regio en vervolgens de sjabloon te implementeren in de nieuwe regio.  Zie [resource groepen exporteren naar sjablonen](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)voor meer informatie over Resource Manager en sjablonen.


## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat de Azure-netwerk beveiligings groep zich bevindt in de Azure-regio van waaruit u wilt verplaatsen.

- Azure-netwerk beveiligings groepen kunnen niet worden verplaatst tussen regio's.  U moet de nieuwe NSG koppelen aan resources in de doel regio.

- Als u een NSG-configuratie wilt exporteren en een sjabloon wilt implementeren om een NSG in een andere regio te maken, hebt u de rol netwerk bijdrager of hoger nodig.
   
- Identificeer de bronnetwerkindeling en alle resources die u momenteel gebruikt. Deze indeling bevat, maar is niet beperkt tot load balancers, open bare Ip's en virtuele netwerken.

- Controleer of u met uw Azure-abonnement Nsg's kunt maken in de doel regio die wordt gebruikt. Neem contact op met ondersteuning voor het inschakelen van het vereiste quotum.

- Zorg ervoor dat uw abonnement voldoende bronnen heeft ter ondersteuning van het toevoegen van Nsg's voor dit proces.  Raadpleeg [Azure-abonnement en -servicelimieten, quotums en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).


## <a name="prepare-and-move"></a>Voorbereiden en verplaatsen
De volgende stappen laten zien hoe u de netwerk beveiligings groep voorbereidt voor de configuratie en beveiligings regel verplaatsen met behulp van een resource manager-sjabloon en de NSG-configuratie en beveiligings regels naar de doel regio verplaatst met behulp van Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>De sjabloon exporteren en implementeren vanuit een script

1. Meld u aan bij uw Azure-abonnement met de opdracht [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) en volg de instructies op het scherm:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Verkrijg de resource-ID van de NSG die u wilt verplaatsen naar de doel regio en plaats deze in een variabele met [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup):

    ```azurepowershell-interactive
    $sourceNSGID = (Get-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exporteer de bron-NSG naar een. JSON-bestand in de map waar u de opdracht [export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup)uitvoert:
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceNSGID -IncludeParameterDefaultValue
   ```

4. Het bestand dat u hebt gedownload krijgt de naam van de resource groep waaruit de resource is geëxporteerd.  Zoek het bestand dat is geëxporteerd uit de opdracht met de naam **\<resource-group-name> . json** en open het in een editor naar keuze:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. Als u de para meter van de naam van de NSG wilt bewerken, wijzigt u de eigenschap **DefaultValue** van de naam van de bron NSG in de naam van uw doel NSG, controleert u of de naam tussen aanhalings tekens is:
    
    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "networkSecurityGroups_myVM1_nsg_name": {
            "defaultValue": "<target-nsg-name>",
            "type": "String"
            }
        }

    ```


6. Als u de doel regio wilt bewerken waar de NSG-configuratie en beveiligings regels worden verplaatst, wijzigt u de eigenschap **Location** onder **resources**:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78", 
             }
            }
    ```
  
7. Als u regio codes wilt ophalen, kunt u de Azure PowerShell cmdlet [Get-AzLocation](/powershell/module/az.resources/get-azlocation) gebruiken door de volgende opdracht uit te voeren:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. U kunt ook andere para meters in de **\<resource-group-name> . json** wijzigen als u ervoor kiest en zijn optioneel, afhankelijk van uw vereisten:

    * **Beveiligings regels** : u kunt bewerken welke regels in de doel-NSG worden geïmplementeerd door regels toe te voegen aan of te verwijderen uit de sectie **securityRules** in het **\<resource-group-name> JSON** -bestand:

        ```json
           "resources": [
                  {
                  "type": "Microsoft.Network/networkSecurityGroups",
                  "apiVersion": "2019-06-01",
                  "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
                  "location": "TARGET REGION",
                  "properties": {
                       "provisioningState": "Succeeded",
                       "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
                  "securityRules": [
                    {
                        "name": "RDP",
                        "etag": "W/\"c630c458-6b52-4202-8fd7-172b7ab49cf5\"",
                        "properties": {
                             "provisioningState": "Succeeded",
                             "protocol": "TCP",
                             "sourcePortRange": "*",
                             "destinationPortRange": "3389",
                             "sourceAddressPrefix": "*",
                             "destinationAddressPrefix": "*",
                             "access": "Allow",
                             "priority": 300,
                             "direction": "Inbound",
                             "sourcePortRanges": [],
                             "destinationPortRanges": [],
                             "sourceAddressPrefixes": [],
                             "destinationAddressPrefixes": []
                            }
                        ]
            }  
            
        ```

        Voor het volt ooien van de toevoeging of het verwijderen van de regels in de doel-NSG moet u ook de aangepaste regel typen aan het einde van het **\<resource-group-name> JSON** -bestand bewerken in de indeling van het voor beeld hieronder:

        ```json
           {
            "type": "Microsoft.Network/networkSecurityGroups/securityRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('networkSecurityGroups_myVM1_nsg_name'), '/Port_80')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('networkSecurityGroups_myVM1_nsg_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "protocol": "*",
                "sourcePortRange": "*",
                "destinationPortRange": "80",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 310,
                "direction": "Inbound",
                "sourcePortRanges": [],
                "destinationPortRanges": [],
                "sourceAddressPrefixes": [],
                "destinationAddressPrefixes": []
            }
        ```

9. Sla het **\<resource-group-name> JSON** -bestand op.

10. Maak een resource groep in de doel regio voor de doel-NSG die moet worden geïmplementeerd met [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. Implementeer het bewerkte **\<resource-group-name> . json** -bestand in de resource groep die u in de vorige stap hebt gemaakt met behulp van [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Als u wilt controleren of de resources zijn gemaakt in de doel regio, gebruikt u [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup) en [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzNetworkSecurityGroup -Name <target-nsg-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>Verwijderen 

Als u na de implementatie wilt beginnen of de NSG in het doel wilt negeren, verwijdert u de resource groep die is gemaakt in het doel en de verplaatste NSG worden verwijderd.  Gebruik [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)om de resource groep te verwijderen:

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>Opschonen

Als u de wijzigingen wilt door voeren en de NSG wilt verplaatsen, verwijdert u de bron-NSG of de resource groep, gebruikt u [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) of [Remove-AzNetworkSecurityGroup](/powershell/module/az.network/remove-aznetworksecuritygroup):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>

```

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een Azure-netwerk beveiligings groep verplaatst van de ene regio naar een andere en de bron resources opgeschoond.  Raadpleeg voor meer informatie over het verplaatsen van resources tussen regio's en herstel na nood gevallen in Azure:


- [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Virtuele Azure-machines verplaatsen naar een andere regio](../site-recovery/azure-to-azure-tutorial-migrate.md)
