---
title: De open bare IP-configuratie van Azure verplaatsen naar een andere Azure-regio met behulp van Azure PowerShell
description: Gebruik Azure Resource Manager sjabloon om de open bare IP-configuratie van Azure van de ene Azure-regio naar de andere te verplaatsen met behulp van Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: a21d088680855b74e7259028ed7ef55165707c56
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98938699"
---
# <a name="move-azure-public-ip-configuration-to-another-region-using-azure-powershell"></a>De open bare IP-configuratie van Azure naar een andere regio verplaatsen met behulp van Azure PowerShell

Er zijn verschillende scenario's waarin u uw bestaande open bare IP-configuraties van Azure naar een andere regio wilt verplaatsen. U kunt bijvoorbeeld een openbaar IP-adres maken met dezelfde configuratie en SKU voor testen. Het is ook mogelijk dat u een open bare IP-configuratie wilt verplaatsen naar een andere regio als onderdeel van de planning voor nood herstel.

**Open bare Azure-Ip's zijn regio specifiek en kunnen niet worden verplaatst van de ene regio naar de andere.** U kunt echter een Azure Resource Manager sjabloon gebruiken om de bestaande configuratie van een openbaar IP-adres te exporteren.  U kunt de resource vervolgens in een andere regio zetten door het open bare IP-adres naar een sjabloon te exporteren, de para meters te wijzigen zodat deze overeenkomen met de doel regio en vervolgens de sjabloon te implementeren in de nieuwe regio.  Zie [resource groepen exporteren naar sjablonen](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates) voor meer informatie over Resource Manager en sjablonen


## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat het open bare IP-adres van Azure zich bevindt in de Azure-regio van waaruit u wilt verplaatsen.

- Open bare Azure-Ip's kunnen niet tussen regio's worden verplaatst.  U moet het nieuwe open bare IP-adres koppelen aan resources in de doel regio.

- Als u een open bare IP-configuratie wilt exporteren en een sjabloon wilt implementeren voor het maken van een openbaar IP-adres in een andere regio, hebt u de rol netwerk bijdrager of hoger nodig.
   
- Identificeer de bronnetwerkindeling en alle resources die u momenteel gebruikt. Deze indeling bevat, maar is niet beperkt tot load balancers, netwerk beveiligings groepen (Nsg's) en virtuele netwerken.

- Controleer of u met uw Azure-abonnement open bare Ip's kunt maken in de doel regio die wordt gebruikt. Neem contact op met ondersteuning voor het inschakelen van het vereiste quotum.

- Zorg ervoor dat uw abonnement voldoende bronnen heeft ter ondersteuning van het toevoegen van open bare IP-adressen voor dit proces.  Raadpleeg [Azure-abonnement en -servicelimieten, quotums en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).


## <a name="prepare-and-move"></a>Voorbereiden en verplaatsen
De volgende stappen laten zien hoe u de open bare IP voorbereidt voor de configuratie verplaatsing met behulp van een resource manager-sjabloon en de open bare IP-configuratie verplaatst naar de doel regio met behulp van Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>De sjabloon exporteren en implementeren vanuit een script

1. Meld u aan bij uw Azure-abonnement met de opdracht [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) en volg de instructies op het scherm:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Haal de resource-ID op van het open bare IP-adres dat u wilt verplaatsen naar de doel regio en plaats deze in een variabele met [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress):

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exporteer het virtuele bron netwerk naar een. JSON-bestand naar de map waar u de opdracht [export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup)uitvoert:
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. Het bestand dat u hebt gedownload krijgt de naam van de resource groep waaruit de resource is geëxporteerd.  Zoek het bestand dat is geëxporteerd uit de opdracht met de naam **\<resource-group-name> . json** en open het in een editor naar keuze:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. Als u de para meter van de naam van het open bare IP-adres wilt bewerken, wijzigt u de eigenschap **DefaultValue** van de bron-open bare IP-naam in de naam van het open bare IP-adres. Controleer of de naam tussen aanhalings tekens is:
    
    ```json
        {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_myVM1pubIP_name": {
        "defaultValue": "<target-publicip-name>",
        "type": "String"
        }
    }

    ```

6. Als u de doel regio wilt bewerken waar het open bare IP-adres wordt verplaatst, wijzigt u de eigenschap **Location** onder resources:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]             
    ```
  
7. Als u regio codes wilt ophalen, kunt u de Azure PowerShell cmdlet [Get-AzLocation](/powershell/module/az.resources/get-azlocation) gebruiken door de volgende opdracht uit te voeren:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. U kunt ook andere para meters in de sjabloon wijzigen als u ervoor kiest en zijn optioneel, afhankelijk van uw vereisten:

    * **SKU** : u kunt de SKU van het open bare IP-adres in de configuratie wijzigen van standaard in Basic of Basic naar Standard door de eigenschap **SKU**  >  **name** in het **\<resource-group-name> JSON** -bestand te wijzigen:

         ```json
            "resources": [
                   {
                    "type": "Microsoft.Network/publicIPAddresses",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                    "location": "<target-region>",
                    "sku": {
                        "name": "Basic",
                        "tier": "Regional"
                    },
         ```

         Zie [een openbaar IP-adres maken, wijzigen of verwijderen](./virtual-network-public-ip-address.md)voor meer informatie over de verschillen tussen open bare ip's van de Basic-en Standard-SKU.

    * **Toewijzings methode voor openbaar IP-adres** en **time-out voor inactiviteit** : u kunt beide opties in de sjabloon wijzigen door de eigenschap **publicIPAllocationMethod** van **dynamisch** naar **statisch** of **statisch** naar **dynamisch** te veranderen. U kunt de time-out voor inactiviteit wijzigen door de eigenschap **idleTimeoutInMinutes** te wijzigen in de gewenste hoeveelheid.  De standaard waarde is **4**:

         ```json
         "resources": [
                {
                "type": "Microsoft.Network/publicIPAddresses",
                "apiVersion": "2019-06-01",
                "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                "location": "<target-region>",
                  "sku": {
                  "name": "Basic",
                  "tier": "Regional"
                 },
                "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
                   }
                }            
         ```

        Zie [een openbaar IP-adres maken, wijzigen of verwijderen](./virtual-network-public-ip-address.md)voor meer informatie over de toewijzings methoden en de time-outwaarden voor inactiviteit.


9. Sla het **\<resource-group-name> JSON** -bestand op.

10. Maak een resource groep in de doel regio voor het open bare doel-IP-adres dat moet worden geïmplementeerd met [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. Implementeer het bewerkte **\<resource-group-name> . json** -bestand in de resource groep die u in de vorige stap hebt gemaakt met behulp van [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Als u wilt controleren of de resources zijn gemaakt in de doel regio, gebruikt u [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup) en [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzPublicIPAddress -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```
## <a name="discard"></a>Verwijderen 

Als u na de implementatie wilt beginnen of het open bare IP-adres wilt negeren in het doel, verwijdert u de resource groep die is gemaakt in het doel en wordt het verplaatste open bare IP-adres verwijderd.  Gebruik [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)om de resource groep te verwijderen:

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>Opschonen

Als u de wijzigingen wilt door voeren en de verplaatsing van het virtuele netwerk wilt volt ooien, verwijdert u het virtuele bron netwerk of de resource groep, gebruikt u [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) of [Remove-AzPublicIPAddress](/powershell/module/az.network/remove-azpublicipaddress):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzPublicIpAddress -Name <source-publicip-name> -ResourceGroupName <resource-group-name>

```

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een open bare Azure-IP van de ene regio naar de andere verplaatst en worden de bron bronnen opgeruimd.  Raadpleeg voor meer informatie over het verplaatsen van resources tussen regio's en herstel na nood gevallen in Azure:


- [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Virtuele Azure-machines verplaatsen naar een andere regio](../site-recovery/azure-to-azure-tutorial-migrate.md)
