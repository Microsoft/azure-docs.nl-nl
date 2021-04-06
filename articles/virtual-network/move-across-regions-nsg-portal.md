---
title: Verplaats de Azure-netwerk beveiligings groep (NSG) naar een andere Azure-regio met behulp van de Azure Portal
description: Gebruik Azure Resource Manager sjabloon om de Azure-netwerk beveiligings groep van de ene Azure-regio naar de andere te verplaatsen met behulp van de Azure Portal.
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: 11b0260eb6c1f5edfb246dcf97a43bdd874bf337
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98217274"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-the-azure-portal"></a>Verplaats de Azure-netwerk beveiligings groep (NSG) naar een andere regio met behulp van de Azure Portal

Er zijn verschillende scenario's waarin u uw bestaande Nsg's van de ene naar de andere regio wilt verplaatsen. U kunt bijvoorbeeld een NSG maken met dezelfde configuratie-en beveiligings regels om te testen. Het is ook mogelijk dat u een NSG naar een andere regio wilt verplaatsen als onderdeel van de planning voor nood herstel.

Azure-beveiligingsgroepen kunnen niet worden verplaatst van de ene regio naar een andere. U kunt echter een Azure Resource Manager sjabloon gebruiken om de bestaande configuratie-en beveiligings regels van een NSG te exporteren.  U kunt de resource vervolgens in een andere regio zetten door de NSG naar een sjabloon te exporteren, de para meters te wijzigen zodat deze overeenkomen met de doel regio en vervolgens de sjabloon te implementeren in de nieuwe regio.  Voor meer informatie over Resource Manager en sjablonen raadpleegt [u Quick Start: Azure Resource Manager sjablonen maken en implementeren met behulp van de Azure Portal](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).


## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat de Azure-netwerk beveiligings groep zich bevindt in de Azure-regio van waaruit u wilt verplaatsen.

- Azure-netwerk beveiligings groepen kunnen niet worden verplaatst tussen regio's.  U moet de nieuwe NSG koppelen aan resources in de doel regio.

- Als u een NSG-configuratie wilt exporteren en een sjabloon wilt implementeren om een NSG in een andere regio te maken, hebt u de rol netwerk bijdrager of hoger nodig.

- Identificeer de bronnetwerkindeling en alle resources die u momenteel gebruikt. Deze indeling bevat, maar is niet beperkt tot load balancers, open bare Ip's en virtuele netwerken.

- Controleer of u met uw Azure-abonnement Nsg's kunt maken in de doel regio die wordt gebruikt. Neem contact op met ondersteuning voor het inschakelen van het vereiste quotum.

- Zorg ervoor dat uw abonnement voldoende bronnen heeft ter ondersteuning van het toevoegen van Nsg's voor dit proces.  Raadpleeg [Azure-abonnement en -servicelimieten, quotums en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).


## <a name="prepare-and-move"></a>Voorbereiden en verplaatsen
De volgende stappen laten zien hoe u de netwerk beveiligings groep voorbereidt voor de configuratie en beveiligings regel verplaatsen met behulp van een resource manager-sjabloon en de NSG-configuratie en beveiligings regels naar de doel regio verplaatst met behulp van de portal.


### <a name="export-the-template-and-deploy-from-the-portal"></a>De sjabloon exporteren en implementeren vanuit de portal

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)-  >  **resource groepen**.
2. Zoek de resource groep die de bron NSG bevat en klik erop.
3. Selecteer > **instellingen**  >  **sjabloon exporteren**.
4. Kies **implementeren** op de Blade **sjabloon exporteren** .
5. Klik op **sjabloon**  >  **bewerken para meters** om de **parameters.jsin** het bestand in de online-editor te openen.
6. Als u de para meter van de naam van de NSG wilt bewerken, wijzigt u de eigenschap **Value** onder **para meters**:

    ```json
            {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
            "networkSecurityGroups_myVM1_nsg_name": {
               "value": "<target-nsg-name>"
                }
               }
            }
    ```

7. Wijzig de waarde van de bron NSG in de editor in een naam van uw keuze voor de doel-NSG. Zorg ervoor dat u de naam tussen aanhalings tekens plaatst.

8.  Klik op **Opslaan** in de editor.

9.  Klik **op sjabloon**  >  **bewerken sjabloon** om de **template.js** te openen in het bestand in de online-editor.

10. Als u de doel regio wilt bewerken waar de NSG-configuratie en beveiligings regels worden verplaatst, wijzigt u de eigenschap **Location** onder **resources** in de online-editor:

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
           ]

    ```

11. Zie [Azure-locaties](https://azure.microsoft.com/global-infrastructure/locations/)voor het verkrijgen van regio-locatie codes.  De code voor een regio is de naam van de regio zonder spaties, **Central VS**-  =  **Midden**.

12. U kunt ook andere para meters in de sjabloon wijzigen als u ervoor kiest en zijn optioneel, afhankelijk van uw vereisten:

    * **Beveiligings regels** : u kunt bewerken welke regels in de doel-NSG worden geïmplementeerd door regels toe te voegen aan of te verwijderen uit de sectie **securityRules** in de **template.jsin** het bestand:

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
                    },
                ]
            }
        ```

      Voor het volt ooien van de toevoeging of het verwijderen van de regels in de doel-NSG moet u ook de aangepaste regel typen aan het einde van de **template.js** in het bestand bewerken in de indeling van het voor beeld hieronder:

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

13. Klik op **Opslaan** in de online editor.

14. Klik op **basis**  >  **abonnement** om het abonnement te kiezen waarin de doel-NSG wordt geïmplementeerd.

15. Klik op **basis**  >  **bronnen groep** om de resource groep te kiezen waarin de doel-NSG wordt geïmplementeerd.  U kunt op **Nieuw maken** klikken om een nieuwe resource groep te maken voor de doel-NSG.  Zorg ervoor dat de naam niet hetzelfde is als de bron resource groep van de bestaande NSG.

16. De   >  **locatie** van de basis beginselen controleren is ingesteld op de doel locatie waar u de NSG wilt implementeren.

17. Controleer onder **instellingen** of de naam overeenkomt met de naam die u hebt ingevoerd in de bovenstaande para meters-editor.

18. Schakel het selectie vakje onder **voor waarden** in.

19. Klik op de knop **aanschaffen** om de netwerk beveiligings groep te implementeren.

## <a name="discard"></a>Verwijderen

Als u de doel-NSG wilt verwijderen, verwijdert u de resource groep die de doel-NSG bevat.  Hiervoor selecteert u de resource groep in het dash board in de portal en selecteert u **verwijderen** boven aan de pagina overzicht.

## <a name="clean-up"></a>Opschonen

Als u de wijzigingen wilt door voeren en de NSG wilt verplaatsen, verwijdert u de bron-NSG of de resource groep. Hiertoe selecteert u de netwerk beveiligings groep of de resource groep in het dash board in de portal en selecteert u **verwijderen** boven aan elke pagina.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een Azure-netwerk beveiligings groep verplaatst van de ene regio naar een andere en de bron resources opgeschoond.  Raadpleeg voor meer informatie over het verplaatsen van resources tussen regio's en herstel na nood gevallen in Azure:


- [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Virtuele Azure-machines verplaatsen naar een andere regio](../site-recovery/azure-to-azure-tutorial-migrate.md)