---
title: Voorlopig verwijderen voor containers (preview-versie)
titleSuffix: Azure Storage
description: Zacht verwijderen voor containers (preview) beveiligt uw gegevens, zodat u uw gegevens eenvoudiger kunt herstellen wanneer deze foutief worden gewijzigd of verwijderd door een toepassing of door een andere gebruiker van het opslag account.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/06/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: 85d880966c4c3864206c7e92256eb8e705812f20
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/07/2021
ms.locfileid: "97962173"
---
# <a name="soft-delete-for-containers-preview"></a>Voorlopig verwijderen voor containers (preview-versie)

Met voorlopig verwijderen voor containers (preview) wordt voor komen dat uw gegevens per ongeluk of onbedoeld worden gewijzigd of verwijderd. Wanneer de container soft delete is ingeschakeld voor een opslag account, worden verwijderde containers en de inhoud ervan bewaard in Azure Storage voor de periode die u opgeeft. Tijdens de retentie periode kunt u eerder verwijderde containers en alle blobs erin herstellen.

Micro soft raadt u aan de volgende functies voor gegevens beveiliging in te scha kelen voor end-to-end-beveiliging voor uw BLOB-gegevens:

- Container soft delete, ter bescherming tegen onbedoeld verwijderen of overschrijven van een container. Zie voor meer informatie over het inschakelen van de container Soft soft delete [voor containers, voorlopig verwijderen inschakelen en beheren](soft-delete-container-enable.md).
- Zacht verwijderen van BLOB, om te beschermen tegen onbedoeld verwijderen of overschrijven van een afzonderlijke blob. Zie [voorlopig verwijderen voor blobs](soft-delete-blob-overview.md)voor meer informatie over het inschakelen van de optie voor het voorlopig verwijderen van blobs.
- BLOB-versie beheer om eerdere versies van een blob automatisch te onderhouden. Wanneer BLOB-versie beheer is ingeschakeld, kunt u een eerdere versie van een BLOB herstellen om uw gegevens te herstellen als deze ten onrechte zijn gewijzigd of verwijderd. Zie [BLOB-versie beheer inschakelen en beheren](versioning-enable.md)voor meer informatie over het inschakelen van BLOB-versies.

> [!WARNING]
> Het verwijderen van een opslag account kan niet ongedaan worden gemaakt. Zacht verwijderen beschermt niet tegen het verwijderen van een opslag account. Configureer een **CannotDelete** -vergren deling voor de bron van het opslag account om onbedoeld verwijderen van een opslag account te voor komen. Zie [resources vergren delen om onverwachte wijzigingen](../../azure-resource-manager/management/lock-resources.md)te voor komen voor meer informatie over het vergren delen van Azure-resources.

## <a name="how-container-soft-delete-works"></a>De werking van de functie voor voorlopig verwijderen van containers

Wanneer u de optie voor het voorlopig verwijderen van een container inschakelt, kunt u een Bewaar periode voor verwijderde containers opgeven tussen 1 en 365 dagen. De standaard Bewaar periode is 7 dagen. Tijdens de retentie periode kunt u een verwijderde container herstellen door de bewerking voor het verwijderen van een **container** op te roepen.

Wanneer u een container herstelt, kunt u deze terugzetten naar de oorspronkelijke naam als die naam niet opnieuw is gebruikt. Als de oorspronkelijke container naam is gebruikt, kunt u de container herstellen met een nieuwe naam.

Nadat de Bewaar periode is verlopen, wordt de container permanent verwijderd uit Azure Storage en kan deze niet worden hersteld. De klok begint op de Bewaar periode op het punt dat de container wordt verwijderd. U kunt de Bewaar periode op elk gewenst moment wijzigen, maar onthoud dat een bijgewerkte Bewaar periode alleen van toepassing is op onlangs verwijderde containers. Eerder verwijderde containers worden definitief verwijderd op basis van de Bewaar periode die van kracht was op het moment dat de container werd verwijderd.

Het uitschakelen van de container soft delete heeft geen permanente verwijdering van containers die eerder zijn verwijderd. Alle voorlopig verwijderde containers worden permanent verwijderd bij het verlopen van de Bewaar periode die van kracht was op het moment dat de container werd verwijderd.

## <a name="about-the-preview"></a>Over de preview-versie

De container soft delete is beschikbaar als preview-versie in alle open bare Azure-regio's.

> [!IMPORTANT]
> De container Soft-voor beeld van verwijderen is alleen bedoeld voor niet-productie gebruik. Service Level Agreements (Sla's) op het niveau van de productie zijn momenteel niet beschikbaar.

Versie 2019-12-12 en hoger van de Azure Storage REST API ondersteunt het voorlopig verwijderen van containers.

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

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

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

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

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
