---
title: Azure Opslaganalyse metrische gegevens (klassiek)
description: Meer informatie over het gebruik van Opslaganalyse metrische gegevens in Azure Storage. Meer informatie over metrische gegevens over trans acties en capaciteit, hoe metrische gegevens worden opgeslagen, metrische gegevens inschakelen en meer.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: 8f698aadc24d0dc0691743f1d8dd54c5d5fd287e
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2021
ms.locfileid: "99220953"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Azure Opslaganalyse metrische gegevens (klassiek)

Op **31 augustus 2023** worden Opslaganalyse metrische gegevens, ook wel *klassieke metrische gegevens* genoemd, buiten gebruik gesteld. Zie de [officiële aankondiging](https://azure.microsoft.com/updates/azure-storage-classic-metrics-will-be-retired-on-31-august-2023/) voor meer informatie. Als u gebruikmaakt van klassieke metrische gegevens, moet u vóór die datum overstappen op metrische gegevens in Azure Monitor. Dit artikel helpt u bij het maken van de overstap. 

Azure Storage gebruikt de Opslaganalyse oplossing voor het opslaan van metrische gegevens die geaggregeerde trans actie-statistieken en capaciteits informatie over aanvragen voor een opslag service bevatten. Trans acties worden gerapporteerd op het niveau van de API-bewerking en op het niveau van de opslag service. De capaciteit wordt gerapporteerd op het niveau van de opslag service. Metrische gegevens kunnen worden gebruikt voor het volgende:
- Analyseer het gebruik van de opslag service.
- Problemen vaststellen met aanvragen die zijn gedaan voor de opslag service.
- Verbeter de prestaties van toepassingen die gebruikmaken van een service.

 Opslaganalyse metrische gegevens zijn standaard ingeschakeld voor nieuwe opslag accounts. U kunt metrische gegevens configureren in de [Azure Portal](https://portal.azure.com/), met behulp van Power shell of met behulp van de Azure cli. Zie [Azure Storage analytic-metrische gegevens (klassiek) inschakelen en beheren](./storage-monitor-storage-account.md)voor stapsgewijze instructies. U kunt Opslaganalyse ook via een programma inschakelen via de REST API of de client bibliotheek. Gebruik de bewerkingen voor het instellen van service-eigenschappen om Opslaganalyse in te scha kelen voor elke service.  

> [!NOTE]
> Er zijn Opslaganalyse metrische gegevens beschikbaar voor Azure Blob-opslag, Azure Queue-opslag, Azure-tabel opslag en Azure Files.
> Opslaganalyse metrische gegevens zijn nu klassieke metrische gegevens. U wordt aangeraden [metrische opslag gegevens in azure monitor](../blobs/monitor-blob-storage.md) te gebruiken in plaats van Opslaganalyse metrische gegevens.

## <a name="transaction-metrics"></a>Metrische gegevens voor transacties  
 Een robuuste set gegevens wordt vastgelegd per uur of minuut intervallen voor elke opslag service en de aangevraagde API-bewerking, waaronder ingangs-en uitstaand, Beschik baarheid, fouten en gecategoriseerde aanvraag percentages. Zie [Opslaganalyse het tabel schema metrische](/rest/api/storageservices/storage-analytics-metrics-table-schema)gegevens voor een volledige lijst met transactie Details.  

 Transactie gegevens worden vastgelegd op service niveau en het API-bewerkings niveau. Op service niveau worden statistieken voor het samen vatting van alle aangevraagde API-bewerkingen elk uur naar een tabel entiteit geschreven, zelfs als er geen aanvragen voor de service zijn gedaan. Bij het API-bewerkings niveau worden statistieken alleen naar een entiteit geschreven als de bewerking binnen dat uur is aangevraagd.  

 Als u bijvoorbeeld een **GetBlob** -bewerking uitvoert op uw BLOB-service, registreert Opslaganalyse metrische gegevens de aanvraag en neemt deze op in de geaggregeerde gegevens voor de BLOB-service en de **GetBlob** -bewerking. Als er tijdens het uur geen **GetBlob** -bewerking wordt aangevraagd, wordt er geen entiteit naar *$MetricsTransactionsBlob* geschreven voor die bewerking.  

 Metrische gegevens over trans acties worden vastgelegd voor gebruikers aanvragen en aanvragen van Opslaganalyse zichzelf. Aanvragen van Opslaganalyse voor het schrijven van Logboeken en tabel entiteiten worden bijvoorbeeld vastgelegd.

## <a name="capacity-metrics"></a>Metrische gegevens over capaciteit  

> [!NOTE]
>  Op dit moment zijn capaciteits gegevens alleen beschikbaar voor de BLOB-service.

 Capaciteits gegevens worden dagelijks vastgelegd voor de BLOB-service van een opslag account en er zijn twee tabel entiteiten geschreven. De ene entiteit biedt statistieken voor gebruikers gegevens en de andere bevat statistieken over de `$logs` BLOB-container die door Opslaganalyse wordt gebruikt. De tabel *$MetricsCapacityBlob* bevat de volgende statistieken:  

- **Capaciteit**: de hoeveelheid opslag die wordt gebruikt door de BLOB-service van het opslag account, in bytes.  
- **ContainerCount**: het aantal BLOB-containers in de BLOB-service van het opslag account.  
- **ObjectCount**: het aantal doorgevoerde en niet-toegezegde blok-of pagina-blobs in de BLOB-service van het opslag account.  

  Zie [Opslaganalyse tabel met metrische](/rest/api/storageservices/storage-analytics-metrics-table-schema)gegevens voor meer informatie over metrische gegevens over capaciteit.  

## <a name="how-metrics-are-stored"></a>Hoe metrische gegevens worden opgeslagen  

 Alle metrische gegevens voor elk van de opslag services worden opgeslagen in drie tabellen die voor die service zijn gereserveerd. Een tabel is voor transactie gegevens, een tabel is voor de informatie over minuten trans acties en een andere tabel is voor capaciteits informatie. Trans actie-informatie en notulen bestaan uit aanvraag-en respons gegevens. Capaciteits gegevens bestaan uit gebruiks gegevens voor opslag. Metrische gegevens over uur, metrische gegevens over minuten en capaciteit voor de BLOB-service van een opslag account worden geopend in tabellen die worden genoemd, zoals beschreven in de volgende tabel.  

|Niveau metrische gegevens|Tabel namen|Ondersteund voor versies|  
|-------------------|-----------------|----------------------------|  
|Metrische gegevens per uur, primaire locatie|-$MetricsTransactionsBlob<br />-$MetricsTransactionsTable<br />-$MetricsTransactionsQueue|Versies vóór 15 augustus 2013. Hoewel deze namen nog steeds worden ondersteund, raden we u aan om over te scha kelen naar het gebruik van de volgende tabellen.|  
|Metrische gegevens per uur, primaire locatie|-$MetricsHourPrimaryTransactionsBlob<br />-$MetricsHourPrimaryTransactionsTable<br />-$MetricsHourPrimaryTransactionsQueue<br />-$MetricsHourPrimaryTransactionsFile|Alle versies. Ondersteuning voor metrische gegevens van bestands service is alleen beschikbaar in versie april 5, 2015 en hoger.|  
|Metrische gegevens over minuten, primaire locatie|-$MetricsMinutePrimaryTransactionsBlob<br />-$MetricsMinutePrimaryTransactionsTable<br />-$MetricsMinutePrimaryTransactionsQueue<br />-$MetricsMinutePrimaryTransactionsFile|Alle versies. Ondersteuning voor metrische gegevens van bestands service is alleen beschikbaar in versie april 5, 2015 en hoger.|  
|Metrische gegevens per uur, secundaire locatie|-$MetricsHourSecondaryTransactionsBlob<br />-$MetricsHourSecondaryTransactionsTable<br />-$MetricsHourSecondaryTransactionsQueue|Alle versies. Geo-redundante replicatie met lees toegang moet zijn ingeschakeld.|  
|Metrische gegevens over minuten, secundaire locatie|-$MetricsMinuteSecondaryTransactionsBlob<br />-$MetricsMinuteSecondaryTransactionsTable<br />-$MetricsMinuteSecondaryTransactionsQueue|Alle versies. Geo-redundante replicatie met lees toegang moet zijn ingeschakeld.|  
|Capaciteit (alleen BLOB-service)|$MetricsCapacityBlob|Alle versies.|  

 Deze tabellen worden automatisch gemaakt wanneer Opslaganalyse is ingeschakeld voor een opslag service-eind punt. Ze worden geopend via de naam ruimte van het opslag account, bijvoorbeeld `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")` . De metrische tabellen worden niet weer gegeven in een vermelding en moeten rechtstreeks via de tabel naam worden geopend.

## <a name="metrics-alerts"></a>Waarschuwingen voor metrische gegevens
U kunt waarschuwingen instellen in de [Azure Portal](https://portal.azure.com) zodat u automatisch een melding ontvangt van belang rijke wijzigingen in het gedrag van uw opslag Services. Zie [Create Metrics Alerts](storage-monitor-storage-account.md#create-metric-alerts)voor stapsgewijze instructies.

Als u een Storage Explorer-hulp programma gebruikt om deze metrische gegevens in een indeling met scheidings tekens te downloaden, kunt u micro soft Excel gebruiken voor het analyseren van de gegevens. Zie [Azure Storage-client hulpprogramma's](./storage-explorers.md)voor een lijst met beschik bare Storage Explorer-hulpprogram ma's.

> [!IMPORTANT]
> Er kan een vertraging optreden tussen een opslag gebeurtenis en wanneer de metrische gegevens van het overeenkomstige uur of minuut worden vastgelegd. In het geval van een minuut meet, kunnen enkele minuten aan gegevens tegelijk worden geschreven. Dit probleem kan leiden tot trans acties van voor gaande minuten die in de trans actie voor de huidige minuut worden geaggregeerd. Als dit probleem optreedt, heeft de waarschuwings service mogelijk niet alle beschik bare metrische gegevens voor het geconfigureerde waarschuwings interval. Dit kan leiden tot onverwacht het activeren van waarschuwingen.
>

## <a name="billing-on-storage-metrics"></a>Facturering op metrische opslag gegevens
Voor het schrijven van aanvragen voor het maken van tabel entiteiten voor metrische gegevens worden kosten in rekening gebracht tegen de standaard tarieven die van toepassing zijn op alle Azure Storage bewerkingen.  

Het lezen en verwijderen van aanvragen van metrische gegevens door een client is ook te factureren tegen standaard tarieven. Als u een Bewaar beleid voor gegevens hebt geconfigureerd, worden er geen kosten in rekening gebracht wanneer Azure Storage oude metrische gegevens verwijdert. Als u Analytics-gegevens verwijdert, worden er kosten in rekening gebracht voor de verwijderings bewerkingen.  

De capaciteit die wordt gebruikt door de tabellen met metrische gegevens is ook Factureerbaar. Gebruik de volgende informatie om de hoeveelheid capaciteit te schatten die wordt gebruikt voor het opslaan van metrische gegevens:  

-   Als u elk uur een service gebruikt voor elke API in elke service, worden er ongeveer 148 KB aan gegevens opgeslagen in de tabel met metrische trans acties als u een samen vatting op service niveau en API-niveau hebt ingeschakeld.  
-   Als u binnen elk uur een service gebruikt voor elke API in de service, wordt ongeveer 12 KB aan gegevens opgeslagen in de tabel met metrische trans acties als u alleen een overzicht op service niveau hebt ingeschakeld.  
-   De capaciteits tabel voor blobs heeft twee rijen die elke dag worden toegevoegd die u hebt gekozen voor Logboeken. Dit scenario impliceert dat de grootte van deze tabel elke dag met Maxi maal ongeveer 300 bytes wordt verhoogd.

## <a name="next-steps"></a>Volgende stappen
* [Een opslagaccount controleren](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [Tabel schema voor metrische gegevens van Opslaganalyse](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [Geregistreerde bewerkingen en status berichten Opslaganalyse](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [Opslaganalyse logboek registratie](storage-analytics-logging.md)