---
title: Een externe Azure-load balancer verplaatsen naar een andere Azure-regio door gebruik te maken van de Azure Portal
description: Gebruik een Azure Resource Manager sjabloon om een extern load balancer van een Azure-regio naar een andere te verplaatsen met behulp van de Azure Portal.
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: f83ff3d1d03354daef3466c1f48eaa505e378634
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94693746"
---
# <a name="move-an-external-load-balancer-to-another-region-by-using-the-azure-portal"></a>Een externe load balancer naar een andere regio verplaatsen met behulp van de Azure Portal

Er zijn verschillende scenario's waarin u een extern load balancer van de ene naar de andere regio wilt verplaatsen. Stel dat u een andere externe load balancer wilt maken met dezelfde configuratie voor testen. Het is ook mogelijk dat u een externe load balancer naar een andere regio wilt verplaatsen als onderdeel van de planning voor nood herstel.

U kunt in een letterlijke zin geen externe Azure-load balancer verplaatsen van de ene regio naar een andere. U kunt echter een Azure Resource Manager sjabloon gebruiken om de bestaande configuratie en het open bare IP-adres van een externe load balancer te exporteren. U kunt de resource vervolgens in een andere regio zetten door de load balancer en het open bare IP-adres naar een sjabloon te exporteren, de para meters te wijzigen zodat deze overeenkomen met de doel regio en vervolgens de sjabloon te implementeren in de nieuwe regio. Zie [resource groepen exporteren naar sjablonen](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)voor meer informatie over Resource Manager en sjablonen.


## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat de externe Azure-load balancer zich bevindt in de Azure-regio van waaruit u wilt verplaatsen.

- Externe Azure load balancers kunnen niet tussen regio's worden verplaatst. U moet de nieuwe load balancer koppelen aan resources in de doel regio.

- Als u een externe load balancer configuratie wilt exporteren en een sjabloon wilt implementeren voor het maken van een externe load balancer in een andere regio, moet u de rol netwerk bijdrager of hoger toewijzen.

- Identificeer de bronnetwerkindeling en alle resources die u momenteel gebruikt. Deze indeling bevat, maar is niet beperkt tot load balancers, netwerk beveiligings groepen, open bare Ip's en virtuele netwerken.

- Controleer of u met uw Azure-abonnement externe load balancers kunt maken in de doel regio. Neem contact op met ondersteuning voor het inschakelen van het vereiste quotum.

- Zorg ervoor dat uw abonnement voldoende bronnen heeft voor het ondersteunen van de toevoeging van load balancers. Raadpleeg [Azure-abonnement en -servicelimieten, quotums en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

## <a name="prepare-and-move"></a>Voorbereiden en verplaatsen
De volgende procedures laten zien hoe u de externe load balancer voor de verplaatsing voorbereidt met behulp van een resource manager-sjabloon en de configuratie van de externe load balancer naar de doel regio kunt verplaatsen met behulp van de Azure Portal. U moet eerst de open bare IP-configuratie van externe load balancer exporteren.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-the-public-ip-from-the-portal"></a>De open bare IP-sjabloon exporteren en het open bare IP-adres implementeren via de portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en selecteer **Resourcegroepen**.
2. Zoek de resource groep die de bron-open bare IP bevat en selecteer deze.
3. **Instellingen**  >  **export sjabloon** selecteren.
4. Selecteer **implementeren** onder **sjabloon exporteren**.
5. Selecteer **sjabloon**  >  **bewerken para meters** om de parameters.jsin het bestand in de online-editor te openen.
8. Als u de para meter van de naam van het open bare IP-adres wilt bewerken, wijzigt u de eigenschap **Value** onder **para meters** van de open bare IP-bron naam in de naam van het open bare IP-adres. Plaats de naam tussen aanhalings tekens.

    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "publicIPAddresses_myVM1pubIP_name": {
            "value": "<target-publicip-name>"
              }
             }
            }

    ```

    Selecteer **Opslaan** in de editor.

9.  Selecteer **sjabloon**  >  **bewerken sjabloon** om de template.jsin het bestand in de online-editor te openen.

10. Als u de doel regio wilt bewerken waarnaar het open bare IP-adres wordt verplaatst, wijzigt u de eigenschap **Location** onder **resources**:

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
  
    Zie [Azure-locaties](https://azure.microsoft.com/global-infrastructure/locations/)om regio-locatie codes op te halen. De code voor een regio is de naam van de regio zonder spaties. De code voor Central US is bijvoorbeeld **Centraal**.
    
12. U kunt indien nodig ook andere para meters in de sjabloon wijzigen, afhankelijk van uw vereisten:

    * **SKU**. U kunt de SKU van het open bare IP-adres in de configuratie wijzigen van standaard in Basic of van Basic naar Standard door de eigenschap **name** onder **SKU** in het template.jsvoor bestand te wijzigen:

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

        Zie [een openbaar IP-adres maken, wijzigen of verwijderen](../virtual-network/virtual-network-public-ip-address.md)voor meer informatie over de verschillen tussen open bare ip's van de Basic-en Standard-SKU.

    * **Open bare IP-toewijzings methode** en **time-out voor inactiviteit**. U kunt de open bare IP-toewijzings methode wijzigen door de eigenschap **publicIPAllocationMethod** van **dynamisch** naar **statisch** of van **statisch** naar **dynamisch** te wijzigen. U kunt de time-out voor inactiviteit wijzigen door de eigenschap **idleTimeoutInMinutes** te wijzigen in de gewenste waarde. De standaard waarde is **4**.

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

        ```

        Zie [een openbaar IP-adres maken, wijzigen of verwijderen](../virtual-network/virtual-network-public-ip-address.md)voor meer informatie over de toewijzings methoden en time-outwaarden.

 
13. Selecteer **Opslaan** in de online editor.

14. Selecteer **basis**  >  **abonnement** om het abonnement te kiezen waarin het open bare doel-IP-adres wordt geïmplementeerd.

15. Selecteer   >  de **resource groep** basis beginselen om de resource groep te kiezen waarin het open bare doel-IP-adres wordt geïmplementeerd. U kunt **nieuwe maken** selecteren om een nieuwe resource groep te maken voor het open bare doel-IP-adres. Zorg ervoor dat de naam niet hetzelfde is als de bron resource groep van de bestaande open bare bron-IP.

16. Controleer of de locatie van de **basis beginselen**  >   is ingesteld op de doel locatie waar u het open bare IP-adres wilt implementeren.

17. Controleer onder **instellingen** of de naam overeenkomt met de naam die u eerder hebt ingevoerd in de para meters-editor.

18. Schakel het selectie vakje voor **waarden** in.

19. Selecteer **kopen** om het doel-open bare IP-adres te implementeren.

20. Als u een ander openbaar IP-adres gebruikt voor een uitgaand NAT-verkeer voor het load balancer dat wordt verplaatst, herhaalt u de vorige stappen om het tweede uitgaande open bare IP-adres te exporteren en te implementeren naar de doel regio.

### <a name="export-the-external-load-balancer-template-and-deploy-the-load-balancer-from-the-azure-portal"></a>De externe load balancer sjabloon exporteren en de load balancer implementeren vanuit de Azure Portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en selecteer **Resourcegroepen**.
2. Zoek de resource groep die de bron externe load balancer bevat en selecteer deze.
3. **Instellingen**  >  **export sjabloon** selecteren.
4. Selecteer **implementeren** onder **sjabloon exporteren**.
5. Selecteer **sjabloon**  >  **bewerken para meters** om de parameters.jsin het bestand in de online-editor te openen.

5. Als u de para meter van de naam van de externe load balancer wilt bewerken, wijzigt u de eigenschap **Value** van de bron externe Load Balancer naam in de naam van uw doel externe Load Balancer. Plaats de naam tussen aanhalings tekens.

    ```json
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
          "loadBalancers_myLoadbalancer_ext_name": {
          "value": "<target-external-lb-name>"
    },
          "publicIPAddresses_myPubIP_in_externalid": {
          "value": "<target-publicIP-resource-ID>"
    },

    ```

6.  Als u de waarde van de open bare doel-IP wilt bewerken die u in de voor gaande stappen hebt verplaatst, moet u eerst de resource-ID ophalen en deze vervolgens in de parameters.jsin het bestand plakken. De ID ophalen:

    1. Meld u in een ander browser tabblad of-venster aan bij de [Azure Portal](https://portal.azure.com) en selecteer **resource groepen**.
    2. Zoek de doel resource groep die het open bare IP-adres bevat dat u in de voor gaande stappen hebt verplaatst. Selecteer deze.
    3. Selecteer **Instellingen** > **Eigenschappen**.
    4. Markeer de **resource-id** op de Blade aan de rechter kant en kopieer deze naar het klem bord. U kunt ook **kopiëren naar het klem bord** rechts van het **resource-id-** pad selecteren.
    5. Plak de resource-ID in de eigenschap **waarde** in de editor **para meters bewerken** die is geopend in het andere browser venster of tabblad:

        ```json
           ```json
           "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
           "contentVersion": "1.0.0.0",
           "parameters": {
              "loadBalancers_myLoadbalancer_ext_name": {
              "value": "<target-external-lb-name>"
        },
              "publicIPAddresses_myPubIP_in_externalid": {
              "value": "<target-publicIP-resource-ID>"
        },

        ```
    6. Selecteer **Opslaan** in de online editor.


7.  Als u uitgaande NAT en regels voor uitgaande verbindingen voor de load balancer hebt geconfigureerd, ziet u in dit bestand een derde vermelding voor de externe ID van het uitgaande open bare IP-adres. Herhaal de voor gaande stappen in de **doel regio** om de id voor het uitgaande open bare IP-adres te verkrijgen. Plak die ID in de parameters.jsin het bestand:

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "loadBalancers_myLoadbalancer_ext_name": {
                "value": "<target-external-lb-name>",

            },
                "publicIPAddresses_myPubIP_in_externalid": {
                "value": "<target-publicIP-resource-ID>",

            },
                "publicIPAddresses_myPubIP_out_externalid": {
                "defaultValue": "<target-publicIP-outbound-resource-ID>",

            }
        },
    ```

8.  Selecteer **sjabloon**  >  **bewerken sjabloon** om de template.jsin het bestand in de online-editor te openen.
9.  Als u de doel regio wilt bewerken waarnaar de configuratie van de externe load balancer wordt verplaatst, wijzigt u de eigenschap **Location** onder **resources** in het template.jsbestand:

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-external-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

10. Zie [Azure-locaties](https://azure.microsoft.com/global-infrastructure/locations/)om regio-locatie codes op te halen. De code voor een regio is de naam van de regio zonder spaties. De code voor Central US is bijvoorbeeld **Centraal**.

11. U kunt indien nodig ook andere para meters in de sjabloon wijzigen, afhankelijk van uw vereisten:

    * **SKU**. U kunt de SKU van de externe load balancer in de configuratie wijzigen van standaard in Basic of van Basic naar Standard door de eigenschap **name** onder **SKU** in de template.jsvoor bestand te wijzigen:

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-external-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      Zie [overzicht van Azure Standard Load Balancer](./load-balancer-overview.md)voor informatie over de verschillen tussen de basis-en standaard SKU load balancers.

    * Taakverdelings **regels**. U kunt regels voor taak verdeling toevoegen of verwijderen in de configuratie door vermeldingen toe te voegen aan of te verwijderen uit de sectie **loadBalancingRules** van de template.jsvoor het bestand:

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
       Zie [Wat is Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)voor informatie over regels voor taak verdeling.

    * **Tests**. U kunt een test toevoegen aan of verwijderen uit de load balancer in de configuratie door vermeldingen toe te voegen aan of te verwijderen in het gedeelte **tests** van de template.jsin het bestand:

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
       Zie [Load Balancer Health probe](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)(Engelstalig) voor meer informatie.

    * **Binnenkomende NAT-regels**. U kunt binnenkomende NAT-regels voor de load balancer toevoegen of verwijderen door vermeldingen toe te voegen aan of te verwijderen uit de sectie **inboundNatRules** van de template.jsvoor het volgende bestand:

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
        Als u het toevoegen of verwijderen van een binnenkomende NAT-regel wilt volt ooien, moet de regel aanwezig zijn of worden verwijderd als een **type** -eigenschap aan het einde van de template.jsin het bestand:

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
        Zie [Wat is Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)voor meer informatie over binnenkomende NAT-regels.

    * **Uitgaande regels**. U kunt uitgaande regels toevoegen aan of verwijderen uit de configuratie door de eigenschap **outboundRules** in de template.jsvoor het bestand te bewerken:

        ```json
        "outboundRules": [
                    {
                        "name": "myOutboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "allocatedOutboundPorts": 10000,
                            "protocol": "All",
                            "enableTcpReset": false,
                            "idleTimeoutInMinutes": 15,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolOutbound')]"
                            },
                            "frontendIPConfigurations": [
                                {
                                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPoutbound')]"
                                }
                            ]
                        }
                    }
                ]
        ```

         Zie [Load Balancer regels voor uitgaande verbindingen](./load-balancer-outbound-connections.md#outboundrules)voor meer informatie.

12. Selecteer **Opslaan** in de online editor.

13. Selecteer **basis**  >  **abonnement** om het abonnement te kiezen waarin de externe Load Balancer worden geïmplementeerd.

15. Selecteer   >  de **resource groep** basis beginselen om de resource groep te kiezen waarin de doel-Load Balancer worden geïmplementeerd. U kunt **nieuwe maken** selecteren om een nieuwe resource groep te maken voor de externe Load Balancer doel. U kunt ook de bestaande resource groep kiezen die u eerder hebt gemaakt voor het open bare IP-adres. Zorg ervoor dat de naam niet hetzelfde is als de bron resource groep van de bestaande externe bron load balancer.

16. Controleer of de locatie van de **basis beginselen**  >   is ingesteld op de doel locatie waar u de externe Load Balancer wilt implementeren.

17. Controleer onder **instellingen** of de naam overeenkomt met de naam die u eerder hebt ingevoerd in de para meters-editor. Controleer of de resource-Id's zijn ingevuld voor open bare Ip's in de configuratie.

18. Schakel het selectie vakje voor **waarden** in.

19. Selecteer **kopen** om het doel-open bare IP-adres te implementeren.

## <a name="discard"></a>Verwijderen

Als u het doel-open bare IP-adres en de externe load balancer wilt verwijderen, verwijdert u de resource groep waarin deze zich bevinden. Als u dit wilt doen, selecteert u de resource groep in het dash board in de portal en selecteert u vervolgens **verwijderen** boven aan de pagina overzicht.

## <a name="clean-up"></a>Opschonen

Als u de wijzigingen wilt door voeren en de open bare IP-en externe load balancer wilt verplaatsen, verwijdert u de open bare IP-bron en de externe load balancer of resource groep. Hiertoe selecteert u die resource groep in het dash board in de portal en selecteert u vervolgens **verwijderen** boven aan elke pagina.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een externe Azure-load balancer verplaatst van de ene regio naar de andere en de bron resources opgeschoond. Zie voor meer informatie over het verplaatsen van resources tussen regio's en herstel na nood gevallen in Azure:


- [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Virtuele Azure-machines verplaatsen naar een andere regio](../site-recovery/azure-to-azure-tutorial-migrate.md)