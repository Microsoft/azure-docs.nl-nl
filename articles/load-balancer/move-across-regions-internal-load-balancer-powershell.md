---
title: Interne Azure-Load Balancer verplaatsen naar een andere Azure-regio met behulp van Azure PowerShell
description: Gebruik Azure Resource Manager sjabloon om Azure interne Load Balancer van een Azure-regio naar een andere te verplaatsen met behulp van Azure PowerShell
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: 2c89ad69207a51a92b56d268c685aa2be4118cf1
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102507582"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-powershell"></a>Interne Azure-Load Balancer naar een andere regio verplaatsen met behulp van Power shell

Er zijn verschillende scenario's waarin u uw bestaande interne load balancer van de ene naar de andere regio wilt verplaatsen. U kunt bijvoorbeeld een interne load balancer maken met dezelfde configuratie voor testen. Het is ook mogelijk dat u een interne load balancer naar een andere regio wilt verplaatsen als onderdeel van de planning voor nood herstel.

Interne load balancers van Azure kunnen niet van de ene regio naar de andere worden verplaatst. U kunt echter een Azure Resource Manager sjabloon gebruiken om de bestaande configuratie en het virtuele netwerk van een interne load balancer te exporteren.  U kunt de resource vervolgens in een andere regio zetten door de load balancer en het virtuele netwerk naar een sjabloon te exporteren, de para meters te wijzigen zodat deze overeenkomen met de doel regio en vervolgens de sjablonen te implementeren in de nieuwe regio.  Zie [resource groepen exporteren naar sjablonen](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates) voor meer informatie over Resource Manager en sjablonen


## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat de interne Azure-load balancer zich bevindt in de Azure-regio van waaruit u wilt verplaatsen.

- Interne load balancers van Azure kunnen niet tussen regio's worden verplaatst.  U moet de nieuwe load balancer koppelen aan resources in de doel regio.

- Als u een interne load balancer configuratie wilt exporteren en een sjabloon wilt implementeren voor het maken van een interne load balancer in een andere regio, hebt u de rol netwerk bijdrager of hoger nodig.
   
- Identificeer de bronnetwerkindeling en alle resources die u momenteel gebruikt. Deze indeling bevat, maar is niet beperkt tot load balancers, netwerk beveiligings groepen, virtuele machines en virtuele netwerken.

- Controleer of u met uw Azure-abonnement interne load balancers kunt maken in de doel regio die wordt gebruikt. Neem contact op met ondersteuning voor het inschakelen van het vereiste quotum.

- Zorg ervoor dat uw abonnement voldoende bronnen heeft ter ondersteuning van het toevoegen van load balancers voor dit proces.  Zie [Azure-abonnement en service limieten, quota's en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)


## <a name="prepare-and-move"></a>Voorbereiden en verplaatsen
De volgende stappen laten zien hoe u de interne load balancer voorbereidt voor het verplaatsen met behulp van een resource manager-sjabloon en de interne load balancer configuratie met behulp van Azure PowerShell verplaatst naar de doel regio.  Als onderdeel van dit proces moet de virtuele-netwerk configuratie van de interne load balancer worden opgenomen en moet eerst worden uitgevoerd voordat de interne load balancer wordt verplaatst.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-azure-powershell"></a>De virtuele-netwerk sjabloon exporteren en implementeren vanuit Azure PowerShell

1. Meld u aan bij uw Azure-abonnement met de opdracht [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) en volg de instructies op het scherm:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
2.  Haal de bron-ID op van het virtuele netwerk dat u wilt verplaatsen naar de doel regio en plaats deze in een variabele met [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork):

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exporteer het virtuele bron netwerk naar een. JSON-bestand naar de map waar u de opdracht [export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup)uitvoert:
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. Het bestand dat u hebt gedownload krijgt de naam van de resource groep waaruit de resource is geëxporteerd.  Zoek het bestand dat is geëxporteerd uit de opdracht met de naam **\<resource-group-name> . json** en open het in een editor naar keuze:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Als u de para meter van de naam van het virtuele netwerk wilt bewerken, wijzigt u de eigenschap **DefaultValue** van de virtuele bron netwerk naam in de naam van het virtuele netwerk van het doel. Controleer of de naam tussen aanhalings tekens is:
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

6.  Als u de doel regio wilt bewerken waar het VNET wordt verplaatst, wijzigt u de eigenschap **Location** onder resources:

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
  
7. Als u regio codes wilt ophalen, kunt u de Azure PowerShell cmdlet [Get-AzLocation](/powershell/module/az.resources/get-azlocation) gebruiken door de volgende opdracht uit te voeren:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8.  U kunt ook andere para meters in het **\<resource-group-name> JSON** -bestand wijzigen als u ervoor kiest en zijn optioneel, afhankelijk van uw vereisten:

    * **Adres ruimte** : de adres ruimte van het VNET kan worden gewijzigd voordat u opslaat door de **sectie Resources**  >  **addressSpace** te wijzigen en de eigenschap **addressPrefixes** in het **\<resource-group-name> JSON** -bestand te wijzigen:

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

    * **Subnet** : de naam van het subnet en de adres ruimte van het subnet kunnen worden gewijzigd of toegevoegd aan door de sectie **subnets** van het **\<resource-group-name> JSON** -bestand te wijzigen. De naam van het subnet kan worden gewijzigd door de eigenschap **name** te wijzigen. De adres ruimte van het subnet kan worden gewijzigd door de eigenschap **addressPrefix** in het **\<resource-group-name> JSON** -bestand te wijzigen:

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

         In het **\<resource-group-name> JSON** -bestand moet het adres voorvoegsel worden bewerkt op twee plaatsen, de hierboven vermelde sectie en de sectie **type** die hieronder wordt weer gegeven.  Wijzig de eigenschap **addressPrefix** zodat deze overeenkomt met het bovenstaande:

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

9.  Sla het **\<resource-group-name> JSON** -bestand op.

10. Maak een resource groep in de doel regio voor het doel-VNET dat moet worden geïmplementeerd met [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. Implementeer het bewerkte **\<resource-group-name> . json** -bestand in de resource groep die u in de vorige stap hebt gemaakt met behulp van [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```
12. Als u wilt controleren of de resources zijn gemaakt in de doel regio, gebruikt u [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup) en [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>

    ```
### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>De interne load balancer sjabloon exporteren en implementeren vanuit Azure PowerShell

1. Meld u aan bij uw Azure-abonnement met de opdracht [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) en volg de instructies op het scherm:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Haal de resource-ID op van de interne load balancer die u wilt verplaatsen naar de doel regio en plaats deze in een variabele met [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer):

    ```azurepowershell-interactive
    $sourceIntLBID = (Get-AzLoadBalancer -Name <source-internal-lb-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exporteer de interne load balancer configuratie van de bron naar een. JSON-bestand naar de map waar u de opdracht [export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup)uitvoert:
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceIntLBID -IncludeParameterDefaultValue
   ```
4. Het bestand dat u hebt gedownload krijgt de naam van de resource groep waaruit de resource is geëxporteerd.  Zoek het bestand dat is geëxporteerd uit de opdracht met de naam **\<resource-group-name> . json** en open het in een editor naar keuze:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Als u de para meter van de naam van de interne load balancer wilt bewerken, wijzigt u de eigenschap **DefaultValue** van de interne bron Load Balancer naam in de naam van de interne Load Balancer van het doel. Controleer of de naam tussen aanhalings tekens is:

    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_externalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```
 
6. Als u de waarde van het virtuele doel netwerk dat hierboven is verplaatst, wilt bewerken, moet u eerst de resource-ID ophalen en deze vervolgens kopiëren en plakken in het **\<resource-group-name> JSON** -bestand.  Gebruik [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork)om de id te verkrijgen:
   
   ```azurepowershell-interactive
    $targetVNETID = (Get-AzVirtualNetwork -Name <target-vnet-name> -ResourceGroupName <target-resource-group-name>).Id
    ```
    Typ de variabele en druk op ENTER om de resource-ID weer te geven.  Markeer het pad naar de ID en kopieer het naar het klem bord:

    ```powershell
    PS C:\> $targetVNETID
    /subscriptions/7668d659-17fc-4ffd-85ba-9de61fe977e8/resourceGroups/myResourceGroupVNET-Move/providers/Microsoft.Network/virtualNetworks/myVNET2-Move
    ```

7.  Plak in het **\<resource-group-name> JSON** -bestand de **resource-id** uit de variabele in plaats van de **DefaultValue** in de tweede para meter voor de virtuele netwerk-id van het doel, zorg ervoor dat u het pad tussen aanhalings tekens plaatst:
   
    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_externalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```

8. Als u de doel regio wilt bewerken waar de interne load balancer configuratie wordt verplaatst, wijzigt u de eigenschap **Location** onder **resources** in het **\<resource-group-name> JSON** -bestand:

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-internal-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

11. Als u regio codes wilt ophalen, kunt u de Azure PowerShell cmdlet [Get-AzLocation](/powershell/module/az.resources/get-azlocation) gebruiken door de volgende opdracht uit te voeren:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
12. U kunt ook andere para meters in de sjabloon wijzigen als u ervoor kiest en zijn optioneel, afhankelijk van uw vereisten:
    
    * **SKU** : u kunt de SKU van de interne Load Balancer in de configuratie wijzigen van standaard in Basic of Basic naar Standard door de eigenschap **SKU**-  >  **naam** in het **\<resource-group-name> JSON** -bestand te wijzigen:

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-internal-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      Zie [overzicht van Azure Standard Load Balancer](./load-balancer-overview.md) voor meer informatie over de verschillen tussen de Basic-en Standard SKU load balancers

    * Taakverdelings **regels** : u kunt regels voor taak verdeling toevoegen aan of verwijderen uit de configuratie door vermeldingen toe te voegen aan of te verwijderen uit de sectie **loadBalancingRules** van het **\<resource-group-name> JSON** -bestand:

        ```json
        "loadBalancingRules": [
                    {
                        "name": "myInboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 80,
                            "backendPort": 80,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false,
                            "loadDistribution": "Default",
                            "disableOutboundSnat": true,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolInbound')]"
                            },
                            "probe": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/probes/myHTTPProbe')]"
                            }
                        }
                    }
                ]
        ```
       Zie [Wat is Azure Load Balancer?](./load-balancer-overview.md) voor meer informatie over taakverdelings regels.

    * **Tests** : u kunt een test toevoegen aan of verwijderen uit de Load Balancer in de configuratie door vermeldingen toe te voegen aan of te verwijderen uit het gedeelte **tests** van het **\<resource-group-name> JSON** -bestand:

        ```json
        "probes": [
                    {
                        "name": "myHTTPProbe",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "Http",
                            "port": 80,
                            "requestPath": "/",
                            "intervalInSeconds": 15,
                            "numberOfProbes": 2
                        }
                    }
                ],
        ```
       Zie [Load Balancer Health probe](./load-balancer-custom-probe-overview.md) (Engelstalig) voor meer informatie over Azure Load Balancer status tests

    * **Binnenkomende NAT-regels** : u kunt binnenkomende NAT-regels voor de Load Balancer toevoegen of verwijderen door vermeldingen toe te voegen aan de sectie **inboundNatRules** van het **\<resource-group-name> JSON** -bestand:

        ```json
        "inboundNatRules": [
                    {
                        "name": "myInboundNATRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 4422,
                            "backendPort": 3389,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false
                        }
                    }
                ]
        ```
        Als u het toevoegen of verwijderen van een binnenkomende NAT-regel wilt volt ooien, moet de regel aanwezig zijn of worden verwijderd als een **type** -eigenschap aan het einde van het **\<resource-group-name> JSON** -bestand:

        ```json
        {
            "type": "Microsoft.Network/loadBalancers/inboundNatRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('loadBalancers_myLoadBalancer_name'), '/myInboundNATRule')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "frontendIPConfiguration": {
                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                },
                "frontendPort": 4422,
                "backendPort": 3389,
                "enableFloatingIP": false,
                "idleTimeoutInMinutes": 4,
                "protocol": "Tcp",
                "enableTcpReset": false
            }
        }
        ```
        Zie [Wat is Azure Load Balancer?](./load-balancer-overview.md) voor meer informatie over binnenkomende NAT-regels.
    
13. Sla het **\<resource-group-name> JSON** -bestand op.
    
10. Maak een resource groep in de doel regio voor de interne load balancer van het doel dat moet worden geïmplementeerd met [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). De bestaande resource groep van boven kan ook opnieuw worden gebruikt als onderdeel van dit proces:
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. Implementeer het bewerkte **\<resource-group-name> . json** -bestand in de resource groep die u in de vorige stap hebt gemaakt met behulp van [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Als u wilt controleren of de resources zijn gemaakt in de doel regio, gebruikt u [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup) en [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzLoadBalancer -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>Verwijderen 

Als u na de implementatie wilt beginnen of het virtuele netwerk wilt negeren en load balancer in het doel wilt verwijderen, verwijdert u de resource groep die is gemaakt in het doel en het verplaatste virtuele netwerk en de load balancer worden verwijderd.  Gebruik [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)om de resource groep te verwijderen:

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

## <a name="clean-up"></a>Opschonen

Als u de wijzigingen wilt door voeren en de NSG wilt verplaatsen, verwijdert u de bron-NSG of de resource groep, gebruikt u [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) of [Remove-AzVirtualNetwork](/powershell/module/az.network/remove-azvirtualnetwork) en [Remove-AzLoadBalancer](/powershell/module/az.network/remove-azloadbalancer)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

``` azurepowershell-interactive

Remove-AzLoadBalancer -name <load-balancer> -ResourceGroupName <resource-group-name>

Remove-AzVirtualNetwork -Name <virtual-network-name> -ResourceGroupName <resource-group-name>


```

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een interne Azure-load balancer verplaatst van de ene regio naar de andere en de bron resources opgeschoond.  Raadpleeg voor meer informatie over het verplaatsen van resources tussen regio's en herstel na nood gevallen in Azure:


- [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Virtuele Azure-machines verplaatsen naar een andere regio](../site-recovery/azure-to-azure-tutorial-migrate.md)