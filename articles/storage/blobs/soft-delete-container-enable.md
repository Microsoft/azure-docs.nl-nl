---
title: Voorlopig verwijderen voor containers inschakelen en beheren (preview-versie)
titleSuffix: Azure Storage
description: Schakel container Soft zacht verwijderen (preview) in om uw gegevens eenvoudiger te kunnen herstellen wanneer deze foutief is gewijzigd of verwijderd.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/05/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 2097c1743e07b5563bc75d3d1cce48aa11b98e5f
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102216340"
---
# <a name="enable-and-manage-soft-delete-for-containers-preview"></a>Voorlopig verwijderen voor containers inschakelen en beheren (preview-versie)

Met de tijdelijke verwijdering van de container (preview) wordt voor komen dat uw gegevens per ongeluk of onbedoeld worden gewijzigd of verwijderd. Wanneer het dynamisch verwijderen van een container is ingeschakeld voor een opslag account, kan een container en de inhoud ervan worden hersteld nadat deze is verwijderd, binnen een Bewaar periode die u opgeeft.

Als er een mogelijkheid is dat uw gegevens per ongeluk worden gewijzigd of verwijderd door een toepassing of een ander opslag account, wordt door micro soft aangeraden om de optie voor het voorlopig verwijderen van containers in te scha kelen. In dit artikel wordt beschreven hoe u de optie voorlopig verwijderen voor containers inschakelt. Zie [voorlopig verwijderen voor containers (preview)](soft-delete-container-overview.md)voor meer informatie over het voorlopig verwijderen van een container, inclusief hoe u zich kunt registreren voor de preview-versie.

Voor end-to-end gegevens beveiliging raadt micro soft u aan om ook voor het dynamisch verwijderen van blobs en BLOB-versie beheer in te scha kelen. Zie voor meer informatie over het inschakelen van zacht verwijderen voor blobs de [optie voorlopig verwijderen inschakelen en beheren voor blobs](soft-delete-blob-enable.md). Zie [BLOB-versie beheer](versioning-overview.md)voor meer informatie over het inschakelen van BLOB-versie beheer.

> [!IMPORTANT]
>
> Er is momenteel **een preview-versie** van de container-Soft-verwijdering. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of nog niet beschikbaar zijn.

## <a name="enable-container-soft-delete"></a>Voorlopig verwijderen van container inschakelen

U kunt voorlopig verwijderen van een container voor het opslag account op elk gewenst moment in-of uitschakelen met behulp van de Azure Portal of een Azure Resource Manager sjabloon.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Voer de volgende stappen uit om de container soft delete voor uw opslag account in te scha kelen met behulp van Azure Portal:

1. Ga in [Azure Portal](https://portal.azure.com/) naar uw opslagaccount.
1. Zoek de instellingen voor **gegevens beveiliging** onder **BLOB service**.
1. Stel de eigenschap voor het **voorlopig verwijderen** van de container in op *ingeschakeld*.
1. Geef onder **Bewaar beleid** op hoe lang voorlopig verwijderde containers door Azure Storage worden bewaard.
1. Sla uw wijzigingen op.

:::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-configure.png" alt-text="Scherm afbeelding die laat zien hoe u een tijdelijke verwijdering van een container in Azure Portal kunt inschakelen":::

# <a name="template"></a>[Sjabloon](#tab/template)

Als u de optie voor het voorlopig verwijderen van een container wilt inschakelen met een Azure Resource Manager sjabloon, maakt u een sjabloon waarmee de eigenschap **containerDeleteRetentionPolicy** wordt ingesteld. In de volgende stappen wordt beschreven hoe u een sjabloon maakt in de Azure Portal.

1. Kies in het Azure Portal **een resource maken**.
1. Typ in **de Marketplace zoeken de** **sjabloon implementatie** en druk vervolgens op **Enter**.
1. Kies **Sjabloonimlementatie**, kies **maken** en kies vervolgens **uw eigen sjabloon bouwen in de editor**.
1. Plak in de sjabloon editor de volgende JSON. Vervang de tijdelijke plaatsaanduiding `<account-name>` door de naam van uw opslagaccount.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "variables": {},
      "resources": [
          {
              "type": "Microsoft.Storage/storageAccounts/blobServices",
              "apiVersion": "2019-06-01",
              "name": "<account-name>/default",
              "properties": {
                  "containerDeleteRetentionPolicy": {
                      "enabled": true,
                      "days": 7
                  }
              }
          }
      ]
    }
    ```

---

1. Geef de Bewaar periode op. De standaardwaarde is 7.
1. Sla de sjabloon op.
1. Geef de resource groep van het account op en klik vervolgens op de knop **beoordeling + maken** om de sjabloon te implementeren en dynamisch verwijderen van de container in te scha kelen.

## <a name="view-soft-deleted-containers"></a>Voorlopig verwijderde containers weer geven

Als zacht verwijderen is ingeschakeld, kunt u in de Azure Portal de voorlopig verwijderde containers weer geven. Voorlopig verwijderde containers zijn zichtbaar tijdens de opgegeven Bewaar periode. Nadat de Bewaar periode is verlopen, wordt een voorlopig verwijderde container permanent verwijderd en is deze niet meer zichtbaar.

Voer de volgende stappen uit om de verwijderde containers in het Azure Portal weer te geven:

1. Navigeer naar uw opslag account in de Azure Portal en Bekijk de lijst met containers.
1. Schakel de optie verwijderde containers weer geven in of uit om verwijderde containers op te laten opnemen in de lijst.

    :::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-list.png" alt-text="Scherm afbeelding die laat zien hoe u de voorlopig verwijderde containers in de Azure Portal kunt weer geven":::

## <a name="restore-a-soft-deleted-container"></a>Een voorlopig verwijderde container herstellen

U kunt een voorlopig verwijderde container en de inhoud ervan herstellen binnen de Bewaar periode. Als u een voorlopig verwijderde container wilt herstellen in de Azure Portal, volgt u deze stappen:

1. Navigeer naar uw opslag account in de Azure Portal en Bekijk de lijst met containers.
1. Geef het context menu weer voor de container die u wilt herstellen en kies **verwijderen ongedaan** maken in het menu.

    :::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-restore.png" alt-text="Scherm afbeelding die laat zien hoe u een voorlopig verwijderde container kunt herstellen in Azure Portal":::

## <a name="next-steps"></a>Volgende stappen

- [Voorlopig verwijderen voor containers (preview-versie)](soft-delete-container-overview.md)
- [Blobs voorlopig verwijderen](soft-delete-blob-overview.md)
- [BLOB-versie beheer](versioning-overview.md)
