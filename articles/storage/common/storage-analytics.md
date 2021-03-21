---
title: Azure Storage Analytics gebruiken voor het verzamelen van Logboeken en metrische gegevens | Microsoft Docs
description: Met Opslaganalyse kunt u metrische gegevens voor alle opslag services bijhouden en logboeken voor blob-, wachtrij-en tabel opslag verzamelen.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/03/2017
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: a5ccaa95c8f29395770395076f75f36019a4355c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101737678"
---
# <a name="storage-analytics"></a>Storage Analytics

Azure Opslaganalyse voert logboek registratie uit en geeft metrische gegevens voor een opslag account. U kunt deze gegevens gebruiken om aanvragen te traceren, gebruiks trends te analyseren en problemen met uw opslag account op te sporen.

Als u Opslaganalyse wilt gebruiken, moet u deze afzonderlijk inschakelen voor elke service die u wilt bewaken. U kunt deze functie inschakelen via de [Azure Portal](https://portal.azure.com). Zie [een opslag account bewaken in de Azure Portal](./manage-storage-analytics-logs.md)voor meer informatie. U kunt Opslaganalyse ook via een programma inschakelen via de REST API of de client bibliotheek. Gebruik de [Eigenschappen van de set BLOB service](/rest/api/storageservices/set-blob-service-properties), [Stel Queue service](/rest/api/storageservices/set-queue-service-properties)-eigenschappen in, [Stel Table service-eigenschappen](/rest/api/storageservices/set-table-service-properties)in en [Stel bestands service-eigenschappen](/rest/api/storageservices/Get-File-Service-Properties) bewerkingen in om Opslaganalyse voor elke service in te scha kelen.

De geaggregeerde gegevens worden opgeslagen in een bekende BLOB (voor logboek registratie) en in bekende tabellen (voor metrieken), die mogelijk worden geopend met behulp van de Blob service-en Table service-Api's.

Opslaganalyse heeft een limiet van 20 TB voor de hoeveelheid opgeslagen gegevens die onafhankelijk is van de totale limiet voor uw opslag account. Zie [schaalbaarheids-en prestatie doelen voor standaard opslag accounts](scalability-targets-standard-account.md)voor meer informatie over limieten voor opslag accounts.

Zie [Microsoft Azure Storage controleren, diagnosticeren en problemen oplossen](storage-monitoring-diagnosing-troubleshooting.md)voor meer informatie over het gebruik van Opslaganalyse en andere hulpprogram ma's voor het identificeren, vaststellen en oplossen van problemen met Azure Storage.

## <a name="billing-for-storage-analytics"></a>Facturering voor Opslaganalyse

Alle metrische gegevens worden geschreven door de services van een opslag account. Als gevolg hiervan is elke schrijf bewerking die door Opslaganalyse is uitgevoerd Factureerbaar. Daarnaast is de hoeveelheid opslag ruimte die wordt gebruikt door de metrische gegevens ook Factureerbaar.

De volgende acties die door Opslaganalyse worden uitgevoerd, zijn Factureerbaar:

* Aanvragen voor het maken van blobs voor logboek registratie.
* Aanvragen voor het maken van tabel entiteiten voor metrische gegevens.

Als u een Bewaar beleid voor gegevens hebt geconfigureerd, worden er geen kosten in rekening gebracht voor verwijderings transacties wanneer Opslaganalyse oude logboeken en metrische gegevens verwijdert. Het verwijderen van trans acties van een client is echter Factureerbaar. Zie [een Opslaganalyse Bewaar beleid voor gegevens instellen](/rest/api/storageservices/Setting-a-Storage-Analytics-Data-Retention-Policy)voor meer informatie over het Bewaar beleid.

### <a name="understanding-billable-requests"></a>Informatie over factureer bare aanvragen

Elke aanvraag voor de opslag service van een account is Factureerbaar of niet factureerbaar. Opslaganalyse registreert elke afzonderlijke aanvraag voor een service, met inbegrip van een status bericht dat aangeeft hoe de aanvraag is verwerkt. Op dezelfde manier Opslaganalyse worden metrische gegevens opgeslagen voor zowel een service als de API-bewerkingen van die service, inclusief de percentages en het aantal bepaalde status berichten. Samen kunnen deze functies u helpen bij het analyseren van uw factureer bare aanvragen, het aanbrengen van verbeteringen in uw toepassing en het vaststellen van problemen met aanvragen voor uw services. Zie [Wat is Azure Storage facturering: band breedte, trans acties en capaciteit](/archive/blogs/windowsazurestorage/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity)voor meer informatie over facturering.

Wanneer u Opslaganalyse gegevens bekijkt, kunt u de tabellen in het onderwerp [Opslaganalyse vastgelegde bewerkingen en status berichten](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) gebruiken om te bepalen welke aanvragen Factureerbaar zijn. Vervolgens kunt u uw logboeken en metrische gegevens vergelijken met de status berichten om te zien of er een bepaalde aanvraag in rekening is gebracht. U kunt ook de tabellen in het vorige onderwerp gebruiken om de beschik baarheid voor een opslag service of afzonderlijke API-bewerking te onderzoeken.

## <a name="next-steps"></a>Volgende stappen
* [Een Storage-account bewaken in de Azure-portal](./manage-storage-analytics-logs.md)
* [Opslaganalyse metrische gegevens](storage-analytics-metrics.md)
* [Logboekregistratie van Opslaganalyse](storage-analytics-logging.md)