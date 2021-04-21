---
title: Opslagoverwegingen voor Azure Functions
description: Meer informatie over de opslagvereisten van Azure Functions en over het versleutelen van opgeslagen gegevens.
ms.topic: conceptual
ms.date: 07/27/2020
ms.openlocfilehash: 5faa85a4fac9fc0b8639f33c475283f4f043c627
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779251"
---
# <a name="storage-considerations-for-azure-functions"></a>Opslagoverwegingen voor Azure Functions

Azure Functions hebt een Azure Storage nodig wanneer u een functie-app-exemplaar maakt. De volgende opslagservices kunnen worden gebruikt door uw functie-app:


|Opslagservice  | Functions-gebruik  |
|---------|---------|
| [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)     | Behoud de bindingstoestand en functiesleutels.  <br/>Wordt ook gebruikt [door taakhubs in Durable Functions](durable/durable-functions-task-hubs.md). |
| [Azure Files](../storage/files/storage-files-introduction.md)  | De bestands share die wordt gebruikt om de code van uw functie-app op te slaan en uit te voeren in een [verbruiksplan](consumption-plan.md) en [een Premium-abonnement.](functions-premium-plan.md) |
| [Azure Queue Storage](../storage/queues/storage-queues-introduction.md)     | Wordt gebruikt [door taakhubs in Durable Functions](durable/durable-functions-task-hubs.md).   |
| [Azure Table storage](../storage/tables/table-storage-overview.md)  |  Wordt gebruikt [door taakhubs in Durable Functions](durable/durable-functions-task-hubs.md).       |

> [!IMPORTANT]
> Wanneer u het Consumption/Premium-hostingplan gebruikt, worden uw functiecode en bindingsconfiguratiebestanden opgeslagen in Azure File Storage in het hoofdopslagaccount. Wanneer u het belangrijkste opslagaccount verwijdert, wordt de inhoud verwijderd en kan deze niet worden hersteld.

## <a name="storage-account-requirements"></a>Vereisten voor een opslagaccount

Wanneer u een functie-app maakt, moet u een account voor algemeen gebruik Azure Storage maken of koppelen dat ondersteuning biedt voor Blob-, Queue- en Table-opslag. Dit komt doordat Functions afhankelijk is van Azure Storage voor bewerkingen zoals het beheren van triggers en het vastleggen van functie-uitvoeringen. Sommige opslagaccounts bieden geen ondersteuning voor wachtrijen en tabellen. Deze accounts omvatten alleen blob-opslagaccounts en Azure Premium Storage.

Zie [Introductie van de Azure Storage-services](../storage/common/storage-introduction.md#core-storage-services) voor meer informatie over opslagaccounttypen. 

Hoewel u een bestaand opslagaccount met uw functie-app kunt gebruiken, moet u ervoor zorgen dat het aan deze vereisten voldoet. Opslagaccounts die zijn gemaakt als onderdeel van de stroom voor het maken van de functie-app in de Azure Portal voldoen gegarandeerd aan deze opslagaccountvereisten. In de portal worden niet-ondersteunde accounts uitgefilterd bij het kiezen van een bestaand opslagaccount tijdens het maken van een functie-app. In deze stroom kunt u alleen bestaande opslagaccounts kiezen in dezelfde regio als de functie-app die u maakt. Zie Opslagaccountlocatie [voor meer informatie.](#storage-account-location)

<!-- JH: Does using a Premium Storage account improve perf? -->

## <a name="storage-account-guidance"></a>Richtlijnen voor opslagaccounts

Voor elke functie-app is een opslagaccount vereist. Als dat account wordt verwijderd, wordt uw functie-app niet uitgevoerd. Zie Opslaggerelateerde problemen oplossen voor informatie over het oplossen van problemen [met opslag.](functions-recover-storage-account.md) De volgende aanvullende overwegingen zijn van toepassing op het opslagaccount dat wordt gebruikt door functie-apps.

### <a name="storage-account-location"></a>De locatie van het opslagaccount

Voor de beste prestaties moet uw functie-app een opslagaccount in dezelfde regio gebruiken, waardoor de latentie wordt verkleind. De Azure Portal dwingt deze best practice. Als u om de een of andere reden een opslagaccount moet gebruiken in een andere regio dan uw functie-app, moet u uw functie-app buiten de portal maken. 

### <a name="storage-account-connection-setting"></a>Verbindingsinstelling voor opslagaccount

De verbinding met het opslagaccount wordt onderhouden in de [toepassingsinstelling AzureWebJobsStorage.](./functions-app-settings.md#azurewebjobsstorage) 

De opslagaccountsleutels connection string bijgewerkt wanneer u opslagsleutels opnieuw wilt maken. [Lees hier meer over opslagsleutelbeheer.](../storage/common/storage-account-create.md)

### <a name="shared-storage-accounts"></a>Gedeelde opslagaccounts

Het is mogelijk dat meerdere functie-apps hetzelfde opslagaccount zonder problemen delen. Zo kunt u in Visual Studio apps ontwikkelen met behulp van de Azure Storage emulator. In dit geval fungeert de emulator als één opslagaccount. Hetzelfde opslagaccount dat door uw functie-app wordt gebruikt, kan ook worden gebruikt voor het opslaan van uw toepassingsgegevens. Deze aanpak is echter niet altijd een goed idee in een productieomgeving.

### <a name="optimize-storage-performance"></a>Opslagprestaties optimaliseren

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>Opslaggegevensversleuteling

[!INCLUDE [functions-storage-encryption](../../includes/functions-storage-encryption.md)]

### <a name="in-region-data-residency"></a>Gegevenslocatie in uw regio

Wanneer alle klantgegevens binnen één regio moeten blijven, moet het opslagaccount dat is gekoppeld aan de functie-app er een zijn met [redundantie binnen de regio.](../storage/common/storage-redundancy.md) Er moet ook een redundant opslagaccount in de regio worden gebruikt met [Azure Durable Functions.](./durable/durable-functions-perf-and-scale.md#storage-account-selection)

Andere door het platform beheerde klantgegevens worden alleen opgeslagen in de regio wanneer ze worden hosten in een interne load balanced App Service Environment (ASE). Zie [ASE-zone-redundantie voor meer informatie.](../app-service/environment/zone-redundancy.md#in-region-data-residency)

## <a name="mount-file-shares"></a>Bestands shares toevoegen

_Deze functionaliteit is momenteel alleen beschikbaar wanneer deze wordt uitgevoerd op Linux._ 

U kunt bestaande shares Azure Files aan uw Linux-functie-apps. Door een share te koppelen aan uw Linux-functie-app, kunt u gebruikmaken van bestaande machine learning-modellen of andere gegevens in uw functies. U kunt de opdracht gebruiken [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add) om een bestaande share aan uw Linux-functie-app te maken. 

In deze opdracht is de naam van de bestaande Azure Files share en kan elke tekenreeks zijn die de share uniek definieert wanneer deze aan de `share-name` `custom-id` functie-app wordt bevestigd. Is ook `mount-path` het pad van waaruit de share wordt gebruikt in uw functie-app. `mount-path` moet de indeling `/dir-name` hebben en kan niet beginnen met `/home` .

Zie voor een volledig voorbeeld de scripts in [Een Python-functie-app maken](scripts/functions-cli-mount-files-storage-linux.md)en een Azure Files delen. 

Momenteel wordt alleen `storage-type` een van `AzureFiles` ondersteund. U kunt slechts vijf shares aan een bepaalde functie-app monteren. Het toevoegen van een bestands share kan de koude starttijd met ten minste 200-300 ms verhogen, of zelfs meer wanneer het opslagaccount zich in een andere regio.

De mounted share is beschikbaar voor uw functiecode op `mount-path` de opgegeven. Wanneer bijvoorbeeld is, hebt u toegang tot de doelmap via API's van het `mount-path` bestandssysteem, zoals in het volgende `/path/to/mount` Python-voorbeeld:

```python
import os
...

files_in_share = os.listdir("/path/to/mount")
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Azure Functions hostingopties.

> [!div class="nextstepaction"]
> [Schaal en hosting van Azure Functions](functions-scale.md)
