---
title: Onboarding van een machine Azure Automanage met een ARM-sjabloon
description: Meer informatie over het onboarden van een machine Azure Automanage met een Azure Resource Manager sjabloon.
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 04/09/2021
ms.author: alsin
ms.openlocfilehash: 78cf28903311c542a83c9ace4f794e1cdda9a61c
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368610"
---
# <a name="onboard-a-machine-to-automanage-with-an-azure-resource-manager-arm-template"></a>Een machine onboarden voor automatisch gebruik met een ARM Azure Resource Manager sjabloon


## <a name="overview"></a>Overzicht
Volg de onderstaande stappen om de onboarding van een machine uit te voeren op Automanage Best Practices. Met de onderstaande ARM-sjabloon wordt een -object gemaakt. Dit is de Azure-resource die een machine vertegenwoordigt waarvoor onboarding is uitgevoerd `configurationProfileAssignment` voor Automanage.

## <a name="prerequisites"></a>Vereisten
* U moet een bestaand Automanage-account hebben gemaakt. Zie [dit document](./automanage-account.md) voor meer informatie over het Automanage-account en hoe u er een maakt.
* U moet de rol **Inzender** hebben voor de resourcegroep met de machines die u wilt onboarden voor Automanage

## <a name="arm-template-overview"></a>Overzicht van ARM-sjablonen
Met de volgende ARM-sjabloon wordt de opgegeven machine ge onboardd op Azure Automanage best practices. Meer informatie over de ARM-sjabloon en de stappen voor het implementeren vindt u in de sectie ARM-sjabloonimplementatie [hieronder.](#arm-template-deployment)
```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "machineName": {
            "type": "String"
        },
        "automanageAccountName": {
            "type": "String"
        },
        "configurationProfileAssignment": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Compute/virtualMachines/providers/configurationProfileAssignments",
            "apiVersion": "2020-06-30-preview",
            "name": "[concat(parameters('machineName'), '/Microsoft.Automanage/', 'default')]",
            "properties": {
                "configurationProfile": "[parameters('configurationProfileAssignment')]",
                "accountId": "[concat(resourceGroup().id, '/providers/Microsoft.Automanage/accounts/', parameters('automanageAccountName'))]"
            }
        }
    ]
}
```

## <a name="arm-template-deployment"></a>ARM-sjabloonimplementatie
Met de bovenstaande ARM-sjabloon maakt u een configuratieprofieltoewijzing voor de opgegeven computer met behulp van een opgegeven Automanage-account. Als u nog geen Automanage-account hebt gemaakt, vindt u meer informatie in [dit document.](./automanage-account.md)

De `configurationProfileAssignment` waarde kan een van de volgende waarden zijn:
* 'Productie'
* 'DevTest'

Volg deze stappen om de ARM-sjabloon te implementeren:
1. Sla de onderstaande ARM-sjabloon op als `azuredeploy.json`
1. De ARM-sjabloonimplementatie uitvoeren met `az deployment group create --resource-group myResourceGroup --template-file azuredeploy.json`
1. Geef de waarden op voor machineName, automanageAccountName en configurationProfileAssignment wanneer u hier om wordt gevraagd
1. U bent klaar.

Net als bij elke ARM-sjabloon is het mogelijk om de parameters in een afzonderlijk bestand te factoreren en deze te gebruiken als `azuredeploy.parameters.json` argument bij het implementeren.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over Automanage voor [Linux](./automanage-linux.md) en [Windows](./automanage-windows-server.md)