---
title: Zacht verwijderen voor containers (preview)
titleSuffix: Azure Storage
description: Met de optie Voor het verwijderen van containers (preview) worden uw gegevens beschermd, zodat u uw gegevens gemakkelijker kunt herstellen wanneer deze per ongeluk zijn gewijzigd of verwijderd door een toepassing of door een andere gebruiker van een opslagaccount.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/05/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: 2efd673d26231e83d820f7971a740d06e9b2a1d2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785411"
---
# <a name="soft-delete-for-containers-preview"></a>Zacht verwijderen voor containers (preview)

Met voorlopig verwijderen voor containers (preview) worden uw gegevens niet per ongeluk of met kwaadwillende bedoelingen verwijderd. Wanneer de functie voor het zacht verwijderen van containers is ingeschakeld voor een opslagaccount, worden een verwijderde container en de inhoud ervan bewaard in Azure Storage voor de periode die u opgeeft. Tijdens de bewaarperiode kunt u eerder verwijderde containers herstellen. Als u een container herstelt, worden ook de blobs hersteld die op het moment van verwijderen aanwezig waren in de container.

Voor end-to-end-beveiliging voor uw blobgegevens raadt Microsoft u aan de volgende functies voor gegevensbeveiliging in te stellen:

- Container voor het herstellen van een container die is verwijderd. Zie Voor meer informatie over het inschakelen van de functie voor het inschakelen van de functie Voor het inschakelen en beheren van de functie Voor het verwijderen [van containers.](soft-delete-container-enable.md)
- Blobversies, om automatisch eerdere versies van een blob te onderhouden. Wanneer blobversies zijn ingeschakeld, kunt u een eerdere versie van een blob herstellen om uw gegevens te herstellen als deze per ongeluk worden gewijzigd of verwijderd. Zie Blobversies inschakelen en beheren voor meer informatie over het inschakelen van [blobversies.](versioning-enable.md)
- Blob soft delete om een verwijderde blob of versie te herstellen. Zie Voor meer informatie over het inschakelen van blobs voor het inschakelen van de functie Voor het inschakelen en beheren van de functie Voor [het verwijderen van blobs.](soft-delete-blob-enable.md)

> [!IMPORTANT]
> Voorlopig verwijderen van containers is momenteel in **preview.** Zie de Aanvullende gebruiksvoorwaarden voor [Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor juridische voorwaarden die van toepassing zijn op Azure-functies die bètaversies, preview-functies of anderszins nog niet algemeen beschikbaar zijn.

## <a name="how-container-soft-delete-works"></a>Hoe de container voor het verwijderen van containers werkt

Wanneer u de functie voor het verwijderen van containers inschakelen, kunt u een retentieperiode voor verwijderde containers opgeven die tussen 1 en 365 dagen ligt. De standaardretentieperiode is 7 dagen. Tijdens de bewaarperiode kunt u een verwijderde container herstellen door de bewerking **Container herstellen aan te** roepen.

Wanneer u een container herstelt, worden de blobs van de container en eventuele blobversies ook hersteld. U kunt de container echter alleen gebruiken voor het herstellen van blobs als de container zelf is verwijderd. Als u een verwijderde blob wilt herstellen wanneer de bovenliggende container niet is verwijderd, moet u Blob Soft Delete of Blob Versioning gebruiken.

> [!WARNING]
> Met een zachte verwijdering van containers kunnen alleen hele containers en de inhoud ervan worden hersteld op het moment van verwijdering. U kunt een verwijderde blob in een container niet herstellen met behulp van de container voor soft delete. Microsoft raadt ook aan om blobs voor het verwijderen van blobs en blobversies in te stellen om afzonderlijke blobs in een container te beveiligen.

In het volgende diagram ziet u hoe een verwijderde container kan worden hersteld wanneer de functie voor het zacht verwijderen van containers is ingeschakeld:

:::image type="content" source="media/soft-delete-container-overview/container-soft-delete-diagram.png" alt-text="Diagram waarin wordt getoond hoe een zacht verwijderde container kan worden hersteld":::

Wanneer u een container herstelt, kunt u deze herstellen naar de oorspronkelijke naam als deze naam niet opnieuw is gebruikt. Als de oorspronkelijke containernaam is gebruikt, kunt u de container herstellen met een nieuwe naam.

Nadat de bewaarperiode is verlopen, wordt de container permanent verwijderd uit Azure Storage kan deze niet worden hersteld. De klok begint bij de bewaarperiode op het moment dat de container wordt verwijderd. U kunt de retentieperiode op elk moment wijzigen, maar houd er rekening mee dat een bijgewerkte retentieperiode alleen van toepassing is op nieuw verwijderde containers. Eerder verwijderde containers worden permanent verwijderd op basis van de bewaarperiode die van kracht was op het moment dat de container werd verwijderd.

Het uitschakelen van de tijdelijke verwijdering van containers leidt niet tot permanente verwijdering van containers die eerder zijn verwijderd. Alle tijdelijke verwijderde containers worden permanent verwijderd na het verstrijken van de bewaarperiode die van kracht was op het moment dat de container werd verwijderd.

> [!IMPORTANT]
> De tijdelijke verwijdering van containers biedt geen bescherming tegen het verwijderen van een opslagaccount. Het beschermt alleen tegen het verwijderen van containers in dat account. Als u een opslagaccount wilt beveiligen tegen verwijdering, configureert u een vergrendeling voor de resource van het opslagaccount. Zie Apply an Azure Resource Manager lock to a storage account (Een opslagvergrendeling toepassen op een [opslagaccount) voor meer informatie over het](../common/lock-account-resource.md)vergrendelen van een opslagaccount.

## <a name="about-the-preview"></a>Over de preview

De container is in alle Azure-regio's beschikbaar als preview-versie.

Versie 2019-12-12 of hoger van de Azure Storage REST API ondersteuning voor het verwijderen van containers.

### <a name="storage-account-support"></a>Ondersteuning voor opslagaccounts

De container is voor de volgende typen opslagaccounts beschikbaar:

- V2- en v1-opslagaccounts voor algemeen gebruik
- Blok-blob-opslagaccounts
- Blob Storage-accounts

Opslagaccounts met een hiërarchische naamruimte die is ingeschakeld voor gebruik met Azure Data Lake Storage Gen2 worden ook ondersteund.

### <a name="register-for-the-preview"></a>Registreren voor de preview

Als u zich wilt registreren voor de preview voor het verwijderen van containers, gebruikt u PowerShell of Azure CLI om een aanvraag in te dienen om de functie te registreren bij uw abonnement. Nadat uw aanvraag is goedgekeurd, kunt u het verwijderen van containers inschakelen met nieuwe of bestaande accounts voor algemeen gebruik v2, Blob Storage of Premium blok-blobopslag.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u zich wilt registreren bij PowerShell, roept [u de opdracht Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) aan.

```powershell
# Register for container soft delete (preview)
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName ContainerSoftDelete

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u zich wilt registreren bij Azure CLI, roept u de [opdracht az feature register](/cli/azure/feature#az_feature_register) aan.

```azurecli
az feature register --namespace Microsoft.Storage --name ContainerSoftDelete
az provider register --namespace 'Microsoft.Storage'
```

---

### <a name="check-the-status-of-your-registration"></a>De status van uw registratie controleren

Gebruik PowerShell of Azure CLI om de status van uw registratie te controleren.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u de status van uw registratie met PowerShell wilt controleren, roept u [de opdracht Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) aan.

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName ContainerSoftDelete
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u de status van uw registratie met Azure CLI wilt controleren, roept u de [opdracht az feature](/cli/azure/feature#az_feature_show) aan.

```azurecli
az feature show --namespace Microsoft.Storage --name ContainerSoftDelete
```

---

## <a name="pricing-and-billing"></a>Prijzen en facturering

Er worden geen extra kosten in rekening brengen voor het inschakelen van de functie voor het verwijderen van containers. Gegevens in zacht verwijderde containers worden gefactureerd tegen hetzelfde tarief als actieve gegevens.

## <a name="next-steps"></a>Volgende stappen

- [Container voor soft delete configureren](soft-delete-container-enable.md)
- [Blobs voorlopig verwijderen](soft-delete-blob-overview.md)
- [Blobversies](versioning-overview.md)
