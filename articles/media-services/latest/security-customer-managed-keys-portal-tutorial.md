---
title: Door de klant beheerde sleutels of BYOK-Portal gebruiken
description: In deze zelfstudie gebruikt u de Azure Portal om door klant beheerde sleutels in te schakelen of u gebruikt uw eigen sleutel (BYOK) met een Azure Media Services-opslagaccount.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 10/18/2020
ms.openlocfilehash: e34649162c7a30d4ab43aa068d2c864b5c2d90e0
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106282345"
---
# <a name="tutorial-use-the-azure-portal-to-use-customer-managed-keys-or-byok-with-media-services"></a>Zelfstudie: De Azure Portal gebruiken om door klant beheerde sleutels of BYOK met Media Services

Met de 2020-05-01 API kunt u een door de klant beheerde RSA-sleutel gebruiken met een Azure Media Services-account dat een in het systeem beheerde identiteit bevat. Deze zelfstudie bevat de stappen in de Azure Portal.

De gebruikte services zijn:

- Azure Storage
- Azure Key Vault
- Azure Media Services

In deze tutorial leert u hoe u de Azure Portal kunt gebruiken om:

> [!div class="checklist"]
> - Een resourcegroep maken.
> - Maak een opslagaccount met een in het systeem beheerde identiteit.
> - Een Media Services-account maken met een in het systeem beheerde identiteit.
> - Een sleutelkluis maken voor het opslaan van een door de klant beheerde RSA-sleutel.

## <a name="prerequisites"></a>Vereisten

Een Azure-abonnement.

Als u nog geen Azure-abonnement hebt, [maakt u een gratis proefaccount](https://azure.microsoft.com/free/).

## <a name="system-managed-keys"></a>Door systeem beheerde sleutels

<!-- Create a resource group -->
[!INCLUDE [create a resource group in the portal](./includes/task-create-resource-group-portal.md)]

> [!IMPORTANT]
> Voor de volgende stappen voor het maken van het opslagaccount selecteert u de keuze van door het systeem beheerde sleutel in Geavanceerde instellingen.

<!-- Create a media services account -->

[!INCLUDE [create a media services account in the portal](./includes/task-create-media-services-account-portal.md)]

<!-- Create a key vault -->

[!INCLUDE [create a key vaultl](./includes/task-create-key-vault-portal.md)]

<!-- Enable CMK BYOK on the account -->
[!INCLUDE [enable CMK](./includes/task-enable-cmk-byok-portal.md)]

> [!IMPORTANT]
> Voor de volgende stappen voor opslagversleuteling selecteert u de **door de klant beheerde sleutelkeuze**.

<!-- Set encryption for storage account -->
[!INCLUDE [Set encryption for storage account](./includes/task-set-storage-encryption-portal.md)]

## <a name="change-the-key"></a>De sleutel wijzigen

Media Services detecteert automatisch wanneer de sleutel wordt gewijzigd. OPTIONEEL: Om dit proces te testen, maakt u nog een sleutelversie voor dezelfde sleutel. Media Services moet detecteren dat de sleutel is gewijzigd.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources die u hebt gemaakt, verder niet meer gaat gebruiken, en *u niet meer gefactureerd wilt worden voor deze resources*, verwijdert u ze.

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel voor meer informatie over:
> [!div class="nextstepaction"]
> [Extern bestand coderen op basis van URL, en de video streamen met REST](stream-files-tutorial-with-rest.md)
