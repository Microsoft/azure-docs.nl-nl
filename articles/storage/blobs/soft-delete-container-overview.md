---
title: Voorlopig verwijderen voor containers (preview-versie)
titleSuffix: Azure Storage
description: Zacht verwijderen voor containers (preview) beveiligt uw gegevens, zodat u uw gegevens eenvoudiger kunt herstellen wanneer deze foutief worden gewijzigd of verwijderd door een toepassing of door een andere gebruiker van het opslag account.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/05/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: af9d520bab3ff49b30672717414fbd651c915dd4
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552374"
---
# <a name="soft-delete-for-containers-preview"></a>Voorlopig verwijderen voor containers (preview-versie)

Met voorlopig verwijderen voor containers (preview) wordt voor komen dat uw gegevens per ongeluk of opzettelijk worden verwijderd. Wanneer de container soft delete is ingeschakeld voor een opslag account, wordt een verwijderde container en de inhoud ervan bewaard in Azure Storage voor de periode die u opgeeft. Tijdens de bewaarperiode kunt u eerder verwijderde containers herstellen. Als u een container herstelt, worden ook de blobs hersteld die op het moment van verwijderen aanwezig waren in de container.

Micro soft raadt u aan de volgende functies voor gegevens beveiliging in te scha kelen voor end-to-end-beveiliging voor uw BLOB-gegevens:

- Container soft delete, om een verwijderde container te herstellen. Zie voor meer informatie over het inschakelen van de container Soft soft delete [voor containers, voorlopig verwijderen inschakelen en beheren](soft-delete-container-enable.md).
- BLOB-versie beheer om eerdere versies van een blob automatisch te onderhouden. Wanneer BLOB-versie beheer is ingeschakeld, kunt u een eerdere versie van een BLOB herstellen om uw gegevens te herstellen als deze ten onrechte zijn gewijzigd of verwijderd. Zie [BLOB-versie beheer inschakelen en beheren](versioning-enable.md)voor meer informatie over het inschakelen van BLOB-versies.
- Zacht verwijderen van BLOB, voor het herstellen van een BLOB of versie die is verwijderd. Zie voor het inschakelen van het voorlopig verwijderen van blobs de optie [voorlopig verwijderen inschakelen en beheren voor blobs](soft-delete-blob-enable.md).

> [!IMPORTANT]
> Er is momenteel **een preview-versie** van de container-Soft-verwijdering. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of nog niet beschikbaar zijn.

## <a name="how-container-soft-delete-works"></a>De werking van de functie voor voorlopig verwijderen van containers

Wanneer u de optie voor het voorlopig verwijderen van een container inschakelt, kunt u een Bewaar periode voor verwijderde containers opgeven tussen 1 en 365 dagen. De standaard Bewaar periode is 7 dagen. Tijdens de retentie periode kunt u een verwijderde container herstellen door de **herstel container** bewerking aan te roepen.

Wanneer u een container herstelt, worden de blobs en BLOB-versies van de container ook hersteld. U kunt de container soft delete echter alleen gebruiken om blobs te herstellen als de container zelf is verwijderd. Als u een verwijderde BLOB wilt herstellen wanneer de bovenliggende container niet is verwijderd, moet u de eigenschap zacht verwijderen of BLOB-versie beheer gebruiken.

> [!WARNING]
> Met de tijdelijke verwijdering van een container kunnen alleen hele containers en de inhoud ervan worden hersteld op het moment dat deze wordt verwijderd. U kunt een verwijderde BLOB niet herstellen binnen een container met behulp van de tijdelijke verwijdering van de container. Micro soft raadt aan voor het voorlopig verwijderen van blobs en BLOB-versie beheer in te scha kelen om afzonderlijke blobs in een container te beveiligen.

In het volgende diagram ziet u hoe een verwijderde container kan worden hersteld wanneer de container zacht verwijderen is ingeschakeld:

:::image type="content" source="media/soft-delete-container-overview/container-soft-delete-diagram.png" alt-text="Diagram waarin wordt getoond hoe een voorlopig verwijderde container kan worden hersteld":::

Wanneer u een container herstelt, kunt u deze terugzetten naar de oorspronkelijke naam als die naam niet opnieuw is gebruikt. Als de oorspronkelijke container naam is gebruikt, kunt u de container herstellen met een nieuwe naam.

Nadat de Bewaar periode is verlopen, wordt de container permanent verwijderd uit Azure Storage en kan deze niet worden hersteld. De klok begint op de Bewaar periode op het punt dat de container wordt verwijderd. U kunt de Bewaar periode op elk gewenst moment wijzigen, maar onthoud dat een bijgewerkte Bewaar periode alleen van toepassing is op onlangs verwijderde containers. Eerder verwijderde containers worden definitief verwijderd op basis van de Bewaar periode die van kracht was op het moment dat de container werd verwijderd.

Het uitschakelen van de container soft delete heeft geen permanente verwijdering van containers die eerder zijn verwijderd. Alle voorlopig verwijderde containers worden permanent verwijderd bij het verlopen van de Bewaar periode die van kracht was op het moment dat de container werd verwijderd.

> [!IMPORTANT]
> Het dynamisch verwijderen van een container biedt geen bescherming tegen het verwijderen van een opslag account. Het wordt alleen beschermd tegen het verwijderen van containers in dat account. Als u een opslag account wilt beveiligen tegen verwijderen, configureert u een vergren deling voor de bron van het opslag account. Zie een [Azure Resource Manager Lock Toep assen op een opslag account](../common/lock-account-resource.md)voor meer informatie over het vergren delen van een opslag account.

## <a name="about-the-preview"></a>Over de preview-versie

De container soft delete is beschikbaar als preview-versie in alle Azure-regio's.

Versie 2019-12-12 of hoger van de Azure Storage REST API ondersteunt het voorlopig verwijderen van containers.

### <a name="storage-account-support"></a>Ondersteuning voor opslag accounts

De container soft delete is beschikbaar voor de volgende typen opslag accounts:

- Opslag accounts voor algemeen gebruik v2 en v1
- Blob Storage-accounts blok keren
- Blob Storage-accounts

Opslag accounts met een hiërarchische naam ruimte die is ingeschakeld voor gebruik met Azure Data Lake Storage Gen2 worden ook ondersteund.

### <a name="register-for-the-preview"></a>Registreren voor de preview-versie

Als u wilt registreren in de preview voor het voorlopig verwijderen van een container, gebruikt u Power shell of Azure CLI om een aanvraag in te dienen voor het registreren van de functie bij uw abonnement. Nadat uw aanvraag is goedgekeurd, kunt u de optie voor het voorlopig verwijderen van een container inschakelen met een nieuwe of bestaande v2-, Blob Storage-of Premium-opslag accounts voor algemeen gebruik.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u zich wilt registreren bij Power shell, roept u de opdracht [REGI ster-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) aan.

```powershell
# Register for container soft delete (preview)
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName ContainerSoftDelete

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u zich wilt registreren bij Azure CLI, roept u de opdracht [AZ feature REGI ster](/cli/azure/feature#az-feature-register) aan.

```azurecli
az feature register --namespace Microsoft.Storage --name ContainerSoftDelete
az provider register --namespace 'Microsoft.Storage'
```

---

### <a name="check-the-status-of-your-registration"></a>Controleer de status van uw registratie

Als u de status van uw registratie wilt controleren, gebruikt u Power shell of Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u de status van uw registratie met Power shell wilt controleren, roept u de opdracht [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) aan.

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName ContainerSoftDelete
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u de status van uw registratie met Azure CLI wilt controleren, roept u de opdracht [AZ functie](/cli/azure/feature#az-feature-show) aan.

```azurecli
az feature show --namespace Microsoft.Storage --name ContainerSoftDelete
```

---

## <a name="pricing-and-billing"></a>Prijzen en facturering

Er worden geen extra kosten in rekening gebracht voor het inschakelen van het voorlopig verwijderen van containers. Gegevens in voorlopig verwijderde containers worden gefactureerd tegen hetzelfde aantal als actieve gegevens.

## <a name="next-steps"></a>Volgende stappen

- [Zacht verwijderen van container configureren](soft-delete-container-enable.md)
- [Blobs voorlopig verwijderen](soft-delete-blob-overview.md)
- [BLOB-versie beheer](versioning-overview.md)
