---
title: Verplaats een virtueel Azure-netwerk naar een andere Azure-regio met behulp van de Azure Portal.
description: Verplaats een virtueel Azure-netwerk van de ene Azure-regio naar een andere met behulp van een resource manager-sjabloon en de Azure Portal.
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: 0a80c731e4245b1a295364e5b8c87f90290f7f74
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98223305"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-the-azure-portal"></a>Een virtueel Azure-netwerk verplaatsen naar een andere regio met behulp van de Azure Portal

Er zijn verschillende scenario's voor het verplaatsen van een bestaand virtueel Azure-netwerk van de ene regio naar een andere. Stel dat u een virtueel netwerk met dezelfde configuratie wilt maken voor testen en beschik baarheid als uw bestaande virtuele netwerk. Het is ook mogelijk dat u een virtueel netwerk voor productie naar een andere regio wilt verplaatsen als onderdeel van de planning voor herstel na nood gevallen.

U kunt een Azure Resource Manager sjabloon gebruiken om de verplaatsing van het virtuele netwerk naar een andere regio te volt ooien. Hiervoor exporteert u het virtuele netwerk naar een sjabloon, wijzigt u de para meters zodat deze overeenkomen met de doel regio en implementeert u de sjabloon vervolgens in de nieuwe regio. Voor meer informatie over Resource Manager-sjablonen raadpleegt [u Quick Start: Azure Resource Manager sjablonen maken en implementeren met behulp van de Azure Portal](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).


## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat het virtuele netwerk zich in de Azure-regio bevindt waaruit u wilt verplaatsen.

- Als u een virtueel netwerk wilt exporteren en een sjabloon wilt implementeren om een virtueel netwerk te maken in een andere regio, moet u de rol netwerk bijdrager of hoger hebben.

- Peerings voor virtuele netwerken worden niet opnieuw gemaakt en mislukken als ze nog steeds in de sjabloon aanwezig zijn. Voordat u de sjabloon exporteert, moet u alle virtuele netwerk peers verwijderen. U kunt ze vervolgens opnieuw instellen nadat het virtuele netwerk is verplaatst.

- Identificeer de bronnetwerkindeling en alle resources die u momenteel gebruikt. Deze indeling bevat, maar is niet beperkt tot load balancers, netwerk beveiligings groepen (Nsg's) en open bare Ip's.

- Controleer of u met uw Azure-abonnement virtuele netwerken in de doel regio kunt maken. Neem contact op met de ondersteuning om het vereiste quotum in te scha kelen.

- Zorg ervoor dat uw abonnement voldoende bronnen heeft ter ondersteuning van het toevoegen van virtuele netwerken voor dit proces. Zie [Azure-abonnement- en servicelimieten, quota en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) voor meer informatie.


## <a name="prepare-for-the-move"></a>Voorbereiden voor de verhuizing
In deze sectie gaat u het virtuele netwerk voorbereiden voor de verplaatsing met behulp van een resource manager-sjabloon. U verplaatst het virtuele netwerk vervolgens naar de doel regio met behulp van de Azure Portal.

Ga als volgt te werk om het virtuele netwerk te exporteren en het virtuele doel netwerk te implementeren met behulp van de Azure Portal:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en selecteer vervolgens **resource groepen**.
1. Zoek de resource groep die het virtuele bron netwerk bevat en selecteer deze.
1. **Instellingen**  >  **export sjabloon** selecteren.
1. Selecteer in het deel venster **sjabloon exporteren** de optie **implementeren**.
1. Als u de *parameters.jsvoor* het bestand in uw online editor wilt openen, selecteert u **sjabloon**  >  **bewerken para meters**.
1. Als u de para meter van de naam van het virtuele netwerk wilt bewerken, wijzigt u de eigenschap **Value** onder **para meters**:

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

1. Wijzig in de editor de waarde van de virtuele bron netwerk naam in de editor in een naam die u wilt instellen voor het virtuele netwerk. Zorg ervoor dat u de naam tussen aanhalings tekens plaatst.

1. Selecteer **Opslaan** in de editor.

1. Selecteer **sjabloon** bewerken als u de *template.jsvoor* het bestand in de online editor wilt openen  >  .

1. Als u in de online editor de doel regio wilt bewerken waar het virtuele netwerk wordt verplaatst, wijzigt u de eigenschap **Location** onder **resources**:

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

1. Zie [Azure-locaties](https://azure.microsoft.com/global-infrastructure/locations/)voor het verkrijgen van regio-locatie codes. De code voor een regio is de naam van de regio, zonder spaties (bijvoorbeeld **centrale VS**  =  -**middenus**).

1. Beschrijving U kunt ook andere para meters in de sjabloon wijzigen, afhankelijk van uw vereisten:

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

    * **Subnet**: u kunt wijzigen of toevoegen aan de naam van het subnet en de adres ruimte van het subnet door de sectie **subnetten** van de sjabloon te wijzigen. U kunt de naam van het subnet wijzigen door de eigenschap **name** te wijzigen. En u kunt de adres ruimte van het subnet wijzigen door de eigenschap **addressPrefix** te wijzigen:

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

        Als u het adres voorvoegsel in de *template.js* in het bestand wilt wijzigen, bewerkt u het op twee plaatsen: in de code in de voor gaande sectie en in het gedeelte **type** van de volgende code. Wijzig de eigenschap **addressPrefix** in de volgende code zodat deze overeenkomt met de eigenschap **addressPrefix** in de code in de voor gaande sectie.

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

1. Selecteer in de online editor **Opslaan**.

1. Als u het abonnement wilt kiezen waarin het virtuele netwerk van het doel wordt geïmplementeerd, selecteert u **basis**  >  **abonnement**.

1. Als u de resource groep wilt kiezen waarin het virtuele netwerk van het doel wordt geïmplementeerd, selecteert u **basis**  >  **resource groep**. 

    Als u een nieuwe resource groep voor het virtuele doel netwerk wilt maken, selecteert u **nieuwe maken**. Zorg ervoor dat de naam niet hetzelfde is als de naam van de bron resource groep in het bestaande virtuele netwerk.

1. Controleer of de locatie van de **basis beginselen**  >   is ingesteld op de doel locatie waar u het virtuele netwerk wilt implementeren.

1. Controleer onder **instellingen** of de naam overeenkomt met de naam die u eerder hebt ingevoerd in de para meters-editor.

1. Schakel het selectie vakje voor **waarden** in.

1. Als u het virtuele doel netwerk wilt implementeren, selecteert u **kopen**.

## <a name="delete-the-target-virtual-network"></a>Het virtuele netwerk van het doel verwijderen

Als u het virtuele doel netwerk wilt verwijderen, verwijdert u de resource groep die het virtuele netwerk van het doel bevat. Hiervoor doet u het volgende:
1. Selecteer de resource groep in het dash board Azure Portal.
1. Selecteer boven in het deel venster **overzicht** de optie **verwijderen**.

## <a name="clean-up"></a>Opschonen

Als u de wijzigingen wilt door voeren en de virtuele netwerk verplaatsing wilt volt ooien, verwijdert u het virtuele bron netwerk of de resource groep. Hiervoor doet u het volgende:
1. Selecteer op het dash board Azure Portal het virtuele netwerk of de resource groep.
1. Selecteer boven aan elk deel venster de optie **verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een virtueel Azure-netwerk verplaatst van de ene regio naar een andere met behulp van de Azure Portal en vervolgens de overbodige bron bronnen verwijderd. Zie voor meer informatie over het verplaatsen van resources tussen regio's en herstel na nood gevallen in Azure:


- [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Virtuele Azure-machines naar een andere regio verplaatsen](../site-recovery/azure-to-azure-tutorial-migrate.md)