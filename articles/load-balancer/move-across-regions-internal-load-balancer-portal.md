---
title: Interne Azure-Load Balancer verplaatsen naar een andere Azure-regio met behulp van de Azure Portal
description: Gebruik Azure Resource Manager sjabloon om de interne Azure-Load Balancer van de ene Azure-regio naar de andere te verplaatsen met behulp van de Azure Portal
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/18/2019
ms.author: allensu
ms.openlocfilehash: 68a2cb6926cb41956711a9e3c15d21c250d27f0b
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94698467"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-the-azure-portal"></a>Verplaats de interne Azure-Load Balancer naar een andere regio met behulp van de Azure Portal

Er zijn verschillende scenario's waarin u uw bestaande interne load balancer van de ene naar de andere regio wilt verplaatsen. U kunt bijvoorbeeld een interne load balancer maken met dezelfde configuratie voor testen. Het is ook mogelijk dat u een interne load balancer naar een andere regio wilt verplaatsen als onderdeel van de planning voor nood herstel.

Interne load balancers van Azure kunnen niet van de ene regio naar de andere worden verplaatst. U kunt echter een Azure Resource Manager sjabloon gebruiken om de bestaande configuratie en het virtuele netwerk van een interne load balancer te exporteren.  U kunt de resource vervolgens in een andere regio zetten door de load balancer en het virtuele netwerk naar een sjabloon te exporteren, de para meters te wijzigen zodat deze overeenkomen met de doel regio en vervolgens de sjablonen te implementeren in de nieuwe regio.  Voor meer informatie over Resource Manager en sjablonen raadpleegt [u Quick Start: Azure Resource Manager sjablonen maken en implementeren met behulp van de Azure Portal](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).


## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat de interne Azure-load balancer zich bevindt in de Azure-regio van waaruit u wilt verplaatsen.

- Interne load balancers van Azure kunnen niet tussen regio's worden verplaatst.  U moet de nieuwe load balancer koppelen aan resources in de doel regio.

- Als u een interne load balancer configuratie wilt exporteren en een sjabloon wilt implementeren voor het maken van een interne load balancer in een andere regio, hebt u de rol netwerk bijdrager of hoger nodig.

- Identificeer de bronnetwerkindeling en alle resources die u momenteel gebruikt. Deze indeling bevat, maar is niet beperkt tot load balancers, netwerk beveiligings groepen, virtuele machines en virtuele netwerken.

- Controleer of u met uw Azure-abonnement interne load balancers kunt maken in de doel regio die wordt gebruikt. Neem contact op met ondersteuning voor het inschakelen van het vereiste quotum.

- Zorg ervoor dat uw abonnement voldoende bronnen heeft ter ondersteuning van het toevoegen van load balancers voor dit proces.  Zie [Azure-abonnement en service limieten, quota's en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)


## <a name="prepare-and-move"></a>Voorbereiden en verplaatsen
De volgende stappen laten zien hoe u de interne load balancer voorbereidt voor het verplaatsen met behulp van een resource manager-sjabloon en de interne load balancer configuratie naar de doel regio verplaatst met behulp van de Azure Portal.  Als onderdeel van dit proces moet de virtuele-netwerk configuratie van de interne load balancer worden opgenomen en moet eerst worden uitgevoerd voordat de interne load balancer wordt verplaatst.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-the-azure-portal"></a>De virtuele-netwerk sjabloon exporteren en implementeren vanuit de Azure Portal

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)-  >  **resource groepen**.
2. Zoek de resource groep die het virtuele bron netwerk bevat en klik erop.
3. Selecteer > **instellingen**  >  **sjabloon exporteren**.
4. Kies **implementeren** op de Blade **sjabloon exporteren** .
5. Klik op **sjabloon**  >  **bewerken para meters** om de **parameters.jsin** het bestand in de online-editor te openen.
6. Als u de para meter van de naam van het virtuele netwerk wilt bewerken, wijzigt u de eigenschap **Value** onder **para meters**:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "virtualNetworks_myVNET1_name": {
                "value": "<target-virtual-network-name>"
            }
        }
    }
    ```
7. Wijzig de waarde van de naam van het virtuele netwerk van de bron in de editor in een naam van uw keuze voor het doel-VNET. Zorg ervoor dat u de naam tussen aanhalings tekens plaatst.

8. Klik op **Opslaan** in de editor.

9. Klik **op sjabloon**  >  **bewerken sjabloon** om de **template.js** te openen in het bestand in de online-editor.

10. Als u de doel regio wilt bewerken waar het VNET wordt verplaatst, wijzigt u de eigenschap **Location** onder resources:

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

11. Zie [Azure-locaties](https://azure.microsoft.com/global-infrastructure/locations/)voor het verkrijgen van regio-locatie codes.  De code voor een regio is de naam van de regio zonder spaties, **Central VS**-  =  **Midden**.

12. U kunt ook andere para meters in de **template.jsin** het bestand wijzigen als u ervoor kiest en zijn optioneel, afhankelijk van uw vereisten:

    * **Adres ruimte** : de adres ruimte van het VNET kan worden gewijzigd voordat u het bestand opslaat door de sectie **resources**  >  **addressSpace** te wijzigen en de eigenschap **addressPrefixes** te wijzigen in de **template.jsvoor** het volgende:

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

    * **Subnet** : de naam van het subnet en de adres ruimte van het subnet kunnen worden gewijzigd of toegevoegd aan door de sectie **subnetten** van de **template.jsin** het bestand te wijzigen. De naam van het subnet kan worden gewijzigd door de eigenschap **name** te wijzigen. De adres ruimte van het subnet kan worden gewijzigd door de eigenschap **addressPrefix** in de **template.jsin** het bestand te wijzigen:

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

         Als u in de **template.jsop** bestand het adres voorvoegsel wilt wijzigen, moet dit op twee plaatsen worden bewerkt, de hierboven vermelde sectie en de sectie **type** die hieronder wordt weer gegeven.  Wijzig de eigenschap **addressPrefix** zodat deze overeenkomt met het bovenstaande:

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

13. Klik op **Opslaan** in de online editor.

14. Klik op **basis**  >  **abonnement** om het abonnement te kiezen waarin het doel-VNET wordt geïmplementeerd.

15. Klik op **basis**  >  **bronnen groep** om de resource groep te kiezen waarin het doel-VNET wordt geïmplementeerd.  U kunt op **Nieuw maken** klikken om een nieuwe resource groep te maken voor het doel-VNET.  Zorg ervoor dat de naam niet hetzelfde is als de bron resource groep van het bestaande VNET.

16. De **BASICS**  >  **locatie** van de basis beginselen controleren is ingesteld op de doel locatie waar u het VNET wilt implementeren.

17. Controleer onder **instellingen** of de naam overeenkomt met de naam die u hebt ingevoerd in de bovenstaande para meters-editor.

18. Schakel het selectie vakje onder **voor waarden** in.

19. Klik op de knop **aanschaffen** om het virtuele doel netwerk te implementeren.

### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>De interne load balancer sjabloon exporteren en implementeren vanuit Azure PowerShell

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)-  >  **resource groepen**.
2. Zoek de resource groep die de interne bron load balancer bevat en klik erop.
3. Selecteer > **instellingen**  >  **sjabloon exporteren**.
4. Kies **implementeren** op de Blade **sjabloon exporteren** .
5. Klik op **sjabloon**  >  **bewerken para meters** om de **parameters.jsin** het bestand in de online-editor te openen.

6. Als u de para meter van de naam van de interne load balancer wilt bewerken, wijzigt u de eigenschap **DefaultValue** van de interne bron Load Balancer naam in de naam van de interne Load Balancer van het doel. Controleer of de naam tussen aanhalings tekens is:

    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-internal-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_internalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```

6. Als u de waarde van het virtuele doel netwerk dat hierboven is verplaatst, wilt bewerken, moet u eerst de resource-ID ophalen en deze vervolgens kopiëren en plakken in de **parameters.jsin** het bestand. De ID ophalen:

    1. Meld u aan bij de [Azure Portal](https://portal.azure.com)  >  **resource groepen** op een ander browser tabblad of-venster.
    2. Zoek de doel resource groep die het verplaatste virtuele netwerk bevat uit de bovenstaande stappen en klik erop.
    3. Selecteer Eigenschappen van > **instellingen**  >  **Properties**.
    4. Markeer de **resource-id** op de Blade aan de rechter kant en kopieer deze naar het klem bord.  U kunt ook op de knop **kopiëren naar klem bord** rechts van het **resource-id-** pad klikken.
    5. Plak de resource-ID in de eigenschap **DefaultValue** in de editor **para meters bewerken** open in het andere browser venster of tabblad:

        ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-internal-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_internalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
        ```
    6. Klik op **Opslaan** in de online editor.

7. Klik **op sjabloon**  >  **bewerken sjabloon** om de **template.js** te openen in het bestand in de online-editor.
8. Als u de doel regio wilt bewerken waar de interne load balancer configuratie wordt verplaatst, wijzigt u de eigenschap **Location** onder **resources** in de **template.jsin** het bestand:

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

9.  Zie [Azure-locaties](https://azure.microsoft.com/global-infrastructure/locations/)voor het verkrijgen van regio-locatie codes.  De code voor een regio is de naam van de regio zonder spaties, **Central VS**-  =  **Midden**.

10. U kunt ook andere para meters in de sjabloon wijzigen als u ervoor kiest en zijn optioneel, afhankelijk van uw vereisten:

    * **SKU** : u kunt de SKU van de interne Load Balancer in de configuratie wijzigen van standaard in Basic of Basic naar Standard door de eigenschap **SKU**-  >  **naam** te wijzigen in de **template.jsin** het bestand:

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

    * Taakverdelings **regels** : u kunt regels voor taak verdeling toevoegen aan of verwijderen uit de configuratie door vermeldingen toe te voegen aan of te verwijderen uit de sectie **loadBalancingRules** van de **template.jsvoor** het bestand:

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

    * **Tests** : u kunt een test voor de Load Balancer in de configuratie toevoegen of verwijderen door vermeldingen toe te voegen aan de sectie **tests** van de **template.jsin** het bestand:

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

    * **Binnenkomende NAT-regels** : u kunt binnenkomende NAT-regels voor de Load Balancer toevoegen of verwijderen door vermeldingen toe te voegen aan de sectie **inboundNatRules** van de **template.jsvoor** het volgende bestand:

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
        Als u het toevoegen of verwijderen van een binnenkomende NAT-regel wilt volt ooien, moet de regel aanwezig zijn of worden verwijderd als een **type** -eigenschap aan het einde van de **template.jsin** het bestand:

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

12. Klik op **Opslaan** in de online editor.

13. Klik op **basis**  >  **abonnement** om het abonnement te kiezen waarin de interne doel-Load Balancer worden geïmplementeerd.

15. Klik op **basis**  >  **bronnen groep** om de resource groep te kiezen waarin de doel-Load Balancer worden geïmplementeerd.  U kunt op **Nieuw maken** klikken om een nieuwe resource groep te maken voor de interne doel Load Balancer of de bestaande resource groep kiezen die hierboven voor het virtuele netwerk is gemaakt.  Zorg ervoor dat de naam niet hetzelfde is als de bron resource groep van de bestaande interne load balancer van de bron.

16. De **BASICS**  >  **locatie** van de basis beginselen controleren is ingesteld op de doel locatie waar u de interne Load Balancer wilt implementeren.

17. Controleer onder **instellingen** of de naam overeenkomt met de naam die u hebt ingevoerd in de bovenstaande para meters-editor.  Controleer of de resource-Id's zijn ingevuld voor virtuele netwerken in de configuratie.

18. Schakel het selectie vakje onder **voor waarden** in.

19. Klik op de knop **aanschaffen** om het virtuele doel netwerk te implementeren.

## <a name="discard"></a>Verwijderen

Als u het virtuele doel netwerk en de interne load balancer wilt verwijderen, verwijdert u de resource groep die het virtuele doel netwerk en interne load balancer bevat.  Hiervoor selecteert u de resource groep in het dash board in de portal en selecteert u **verwijderen** boven aan de pagina overzicht.

## <a name="clean-up"></a>Opschonen

Als u de wijzigingen wilt door voeren en het virtuele netwerk en interne load balancer wilt verplaatsen, verwijdert u het virtuele bron netwerk en de interne load balancer of resource groep. Als u dit wilt doen, selecteert u het virtuele netwerk en de interne load balancer of resource groep in het dash board in de portal en selecteert u **verwijderen** boven aan elke pagina.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een interne Azure-load balancer verplaatst van de ene regio naar de andere en de bron resources opgeschoond.  Raadpleeg voor meer informatie over het verplaatsen van resources tussen regio's en herstel na nood gevallen in Azure:


- [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Virtuele Azure-machines verplaatsen naar een andere regio](../site-recovery/azure-to-azure-tutorial-migrate.md)