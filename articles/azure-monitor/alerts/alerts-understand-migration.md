---
title: Migratie van Azure Monitor waarschuwingen begrijpen
description: Begrijp hoe de migratie van waarschuwingen werkt en los problemen op.
ms.topic: conceptual
ms.date: 02/14/2021
ms.author: yalavi
author: yalavi
ms.openlocfilehash: 88b75c46898e1a74b33051d8996af96201f639c7
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102037707"
---
# <a name="understand-migration-options-to-newer-alerts"></a>Meer informatie over migratie opties voor nieuwere waarschuwingen

Klassieke waarschuwingen worden [buiten gebruik gesteld](./monitoring-classic-retirement.md) voor open bare Cloud gebruikers, maar blijven beperkt tot **31 mei 2021**. Klassieke waarschuwingen voor Azure Government Cloud en Azure China 21Vianet worden op **29 februari 2024** buiten gebruik gesteld.

In dit artikel wordt uitgelegd hoe de hand matige migratie en het hulp programma voor vrijwillige migratie werken, dat wordt gebruikt voor het migreren van resterende waarschuwings regels. Daarnaast worden oplossingen beschreven voor enkele veelvoorkomende problemen.

> [!IMPORTANT]
> Waarschuwingen voor activiteiten Logboeken (met inbegrip van service status waarschuwingen) en logboek waarschuwingen worden niet beïnvloed door de migratie. De migratie is alleen van toepassing op de [hier](./monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)beschreven klassieke waarschuwings regels.

> [!NOTE]
> Als uw klassieke waarschuwings regels ongeldig zijn, d.w.z. ze worden [afgeschaft op gedeprecieerde metrische gegevens](#classic-alert-rules-on-deprecated-metrics) of resources die zijn verwijderd, worden ze niet gemigreerd en zijn ze niet beschikbaar nadat de service buiten gebruik is gesteld.

## <a name="manually-migrating-classic-alerts-to-newer-alerts"></a>Klassieke waarschuwingen hand matig migreren naar nieuwere waarschuwingen

Klanten die hun resterende waarschuwingen hand matig willen migreren, kunnen dit ook doen met behulp van de volgende secties. Het bevat ook metrische gegevens die buiten gebruik zijn gesteld en niet rechtstreeks kunnen worden gemigreerd.

### <a name="guest-metrics-on-virtual-machines"></a>Metrische gegevens van de gast op virtuele machines

Voordat u nieuwe metrische waarschuwingen kunt maken voor metrische gegevens van de gast, moeten de metrische gegevens van de gast worden verzonden naar de Azure Monitor logboeken Store. Volg deze instructies om waarschuwingen te maken:

- [Verzameling van metrische gegevens voor gasten inschakelen voor log Analytics](../agents/agent-data-sources.md)
- [Logboek waarschuwingen maken in Azure Monitor](./alerts-log.md)

Er zijn meer opties voor het verzamelen van metrische gegevens over de gast en [waarschuwingen.](../agents/agents-overview.md)

### <a name="storage-and-classic-storage-account-metrics"></a>Metrische gegevens over opslag en klassiek opslag account

Alle klassieke waarschuwingen voor opslag accounts kunnen worden gemigreerd, met uitzonde ring van waarschuwingen over deze metrische gegevens:

- PercentAuthorizationError
- PercentClientOtherError
- PercentNetworkError
- PercentServerOtherError
- PercentSuccess
- Percentthrottlingerror aan
- Percenttimeouterror aan
- AnonymousThrottlingError
- SASThrottlingError
- ThrottlingError

Klassieke waarschuwings regels op percentage metrieken moeten worden gemigreerd op basis van [de toewijzing tussen oude en nieuwe metrische opslag gegevens](../../storage/common/storage-metrics-migration.md#metrics-mapping-between-old-metrics-and-new-metrics). Drempel waarden moeten op de juiste wijze worden aangepast, omdat de nieuwe beschik bare waarde een absoluut item is.

Klassieke waarschuwings regels op AnonymousThrottlingError, SASThrottlingError en ThrottlingError moeten worden opgesplitst in twee nieuwe waarschuwingen omdat er geen gecombineerde metriek is die dezelfde functionaliteit biedt. Drempel waarden moeten op de juiste manier worden aangepast.

### <a name="cosmos-db-metrics"></a>Cosmos DB-metrische gegevens

Alle klassieke waarschuwingen op Cosmos DB metrieken kunnen worden gemigreerd, behalve waarschuwingen over deze metrische gegevens:

- Gemiddeld aantal aanvragen per seconde
- Consistentieniveau
- Http-2xx
- HTTP-3xx
- Maxi maal aantal RUPM verbruikt per minuut
- Max. RUs per seconde
- Mongo overige aanvraag kosten
- Mongo andere aanvraag frequentie
- Waargenomen lees latentie
- Waargenomen schrijf latentie
- Service beschikbaarheid
- Opslagcapaciteit

Gemiddeld aantal aanvragen per seconde, consistentie niveau, Maxi maal RUPM verbruikt per minuut, Max. RUs per seconde, waargenomen lees latentie, waargenomen schrijf latentie en opslag capaciteit zijn momenteel niet beschikbaar in het [nieuwe systeem](../essentials/metrics-supported.md#microsoftdocumentdbdatabaseaccounts).

Waarschuwingen voor metrische gegevens van aanvragen, zoals http-2xx, HTTP-3xx en service beschikbaarheid, worden niet gemigreerd, omdat de manier waarop aanvragen worden geteld, afwijkt van de klassieke metrische gegevens en de nieuwe metrische gegevens. Waarschuwingen voor deze metrische gegevens moeten hand matig opnieuw worden gemaakt met drempel waarden die zijn aangepast.

### <a name="classic-alert-rules-on-deprecated-metrics"></a>Klassieke waarschuwings regels voor gedeprecieerde metrische gegevens

Hieronder vindt u de klassieke waarschuwings regels voor metrische gegevens die eerder werden ondersteund, maar die uiteindelijk zijn afgeschaft. Een klein percentage van de klant heeft mogelijk ongeldige klassieke waarschuwings regels voor deze metrische gegevens. Omdat deze waarschuwings regels ongeldig zijn, worden ze niet gemigreerd.

| Resourcetype| Afgeschafte metriek (en) |
|-------------|----------------- |
| Microsoft.DBforMySQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.DBforPostgreSQL/servers | compute_consumption_percent, compute_limit |
| Micro soft. Network/publicIPAddresses | defaultddostriggerrate |
| Micro soft. SQL/servers/data bases | service_level_objective, storage_limit, storage_used, beperken, dtu_consumption_percent, storage_used |
| Micro soft. Web/hostingEnvironments/multirolepools | averagememoryworkingset |
| Micro soft. Web/hostingEnvironments/workerpools | BytesReceived, httpqueuelength |

## <a name="how-equivalent-new-alert-rules-and-action-groups-are-created"></a>Hoe gelijkwaardige nieuwe waarschuwings regels en actie groepen worden gemaakt

Het hulp programma voor migratie converteert uw klassieke waarschuwings regels naar gelijkwaardige nieuwe waarschuwings regels en actie groepen. Voor de meeste klassieke waarschuwings regels worden gelijkwaardige nieuwe waarschuwings regels op dezelfde metrische waarde met dezelfde eigenschappen, zoals `windowSize` en genoemd `aggregationType` . Er zijn echter enkele klassieke waarschuwings regels voor metrische gegevens die een andere, gelijkwaardige metriek hebben in het nieuwe systeem. De volgende principes zijn van toepassing op de migratie van klassieke waarschuwingen, tenzij aangegeven in de volgende sectie:

- **Frequentie**: definieert hoe vaak een klassieke of nieuwe waarschuwings regel controleert op de voor waarde. De `frequency` regel voor klassieke waarschuwingen kan niet door de gebruiker worden geconfigureerd en is altijd 5 minuten voor alle resource typen. De frequentie van gelijkwaardige regels is ook ingesteld op 5 minuten.
- **Aggregatie type**: definieert hoe de metrische gegevens worden geaggregeerd over het venster van belang. De `aggregationType` is ook hetzelfde tussen klassieke waarschuwingen en nieuwe waarschuwingen voor de meeste metrische gegevens. In sommige gevallen, aangezien de metriek verschilt van klassieke waarschuwingen en nieuwe waarschuwingen, gelijkwaardig `aggregationType` of de `primary Aggregation Type` gedefinieerde metrische gegevens worden gebruikt.
- **Units**: eigenschap van de metrische gegevens waarop een waarschuwing wordt gemaakt. Sommige equivalente metrische gegevens hebben verschillende eenheden. De drempel waarde wordt zo nodig aangepast. Als de oorspronkelijke metrische waarde bijvoorbeeld seconden heeft als eenheden, maar een vergelijk bare nieuwe metrische waarde milliseconden heeft als eenheden, wordt de oorspronkelijke drempel waarde vermenigvuldigd met 1000 om hetzelfde gedrag te garanderen.
- **Venster grootte**: definieert het venster waarin metrische gegevens worden geaggregeerd om te vergelijken met de drempel waarde. Voor standaard `windowSize` waarden zoals 5 minuten, 15 minuten, 30 minuten, 1 uur, 3 uur, 6 uur, 12 uur, 1 dag, is er geen wijziging aangebracht voor de equivalente nieuwe waarschuwings regel. Voor andere waarden wordt het dichtstbijzijnde `windowSize` gebruikt. Voor de meeste klanten is er geen effect op deze wijziging. Voor een klein percentage klanten is het mogelijk dat de drempel waarde moet worden gewijzigd om precies hetzelfde gedrag te krijgen.

In de volgende secties beschrijven we de metrische gegevens die een andere, gelijkwaardige metriek hebben in het nieuwe systeem. Alle metrische gegevens die voor klassieke en nieuwe waarschuwings regels hetzelfde blijven, worden niet weer gegeven. U vindt [hier](../essentials/metrics-supported.md)een lijst met metrische gegevens die in het nieuwe systeem worden ondersteund.

### <a name="microsoftstoragestorageaccounts-and-microsoftclassicstoragestorageaccounts"></a>Micro soft. Storage/Storage accounts en micro soft. ClassicStorage/Storage accounts

Voor Storage-account services, zoals blob, tabel, bestand en wachtrij, worden de volgende metrische gegevens toegewezen aan gelijkwaardige metrische gegevens, zoals hieronder wordt weer gegeven:

| Metrische gegevens in klassieke waarschuwingen | Equivalente metrische gegevens in nieuwe waarschuwingen | Opmerkingen|
|--------------------------|---------------------------------|---------|
| AnonymousAuthorizationError| Metrische gegevens van trans acties met de dimensies ' ResponseType ' = ' AuthorizationError ' en ' Authentication ' = ' Anonymous '| |
| AnonymousClientOtherError | Metrische gegevens van trans acties met de dimensies ' ResponseType ' = ' ClientOtherError ' en ' Authentication ' = ' Anonymous ' | |
| AnonymousClientTimeOutError| Metrische gegevens van trans acties met de dimensies ' ResponseType ' = ' ClientTimeOutError ' en ' Authentication ' = ' Anonymous ' | |
| AnonymousNetworkError | Metrische gegevens van trans acties met de dimensies ' ResponseType ' = ' NetworkError ' en ' Authentication ' = ' Anonymous ' | |
| AnonymousServerOtherError | Metrische gegevens van trans acties met de dimensies ' ResponseType ' = ' ServerOtherError ' en ' Authentication ' = ' Anonymous ' | |
| AnonymousServerTimeOutError | Metrische gegevens van trans acties met de dimensies ' ResponseType ' = ' ServerTimeOutError ' en ' Authentication ' = ' Anonymous ' | |
| AnonymousSuccess | Metrische gegevens van trans acties met de dimensies "ResponseType" = "geslaagd" en "authenticatie" = "Anonymous" | |
| AuthorizationError | Metrische gegevens van trans acties met dimensies "ResponseType" = "AuthorizationError" | |
| AverageE2ELatency | SuccessE2ELatency | |
| AverageServerLatency | SuccessServerLatency | |
| Capaciteit | BlobCapacity | Gebruik `aggregationType` ' gemiddelde ' in plaats van ' last '. Metrische gegevens zijn alleen van toepassing op BLOB-Services |
| ClientOtherError | Metrische gegevens van trans acties met dimensies "ResponseType" = "ClientOtherError"  | |
| ClientTimeoutError | Metrische gegevens van trans acties met dimensies "ResponseType" = "ClientTimeOutError" | |
| ContainerCount | ContainerCount | Gebruik `aggregationType` ' gemiddelde ' in plaats van ' last '. Metrische gegevens zijn alleen van toepassing op BLOB-Services |
| NetworkError | Metrische gegevens van trans acties met dimensies "ResponseType" = "NetworkError" | |
| ObjectCount | BlobCount| Gebruik `aggregationType` ' gemiddelde ' in plaats van ' last '. Metrische gegevens zijn alleen van toepassing op BLOB-Services |
| SASAuthorizationError | Metrische gegevens van trans acties met de dimensies ' ResponseType ' = ' AuthorizationError ' en ' Authentication ' = ' SAS ' | |
| SASClientOtherError | Metrische gegevens van trans acties met de dimensies ' ResponseType ' = ' ClientOtherError ' en ' Authentication ' = ' SAS ' | |
| SASClientTimeOutError | Metrische gegevens van trans acties met de dimensies ' ResponseType ' = ' ClientTimeOutError ' en ' Authentication ' = ' SAS ' | |
| SASNetworkError | Metrische gegevens van trans acties met de dimensies ' ResponseType ' = ' NetworkError ' en ' Authentication ' = ' SAS ' | |
| SASServerOtherError | Metrische gegevens van trans acties met de dimensies ' ResponseType ' = ' ServerOtherError ' en ' Authentication ' = ' SAS ' | |
| SASServerTimeOutError | Metrische gegevens van trans acties met de dimensies ' ResponseType ' = ' ServerTimeOutError ' en ' Authentication ' = ' SAS ' | |
| SASSuccess | Metrische gegevens van trans acties met de dimensies "ResponseType" = "geslaagd" en "authenticatie" = "SAS" | |
| ServerOtherError | Metrische gegevens van trans acties met dimensies "ResponseType" = "ServerOtherError" | |
| ServerTimeOutError | Metrische gegevens van trans acties met dimensies "ResponseType" = "ServerTimeOutError"  | |
| Geslaagd | Metrische gegevens van trans acties met dimensies "ResponseType" = "geslaagd" | |
| TotalBillableRequests| Transacties | |
| TotalEgress | Uitgaand verkeer | |
| TotalIngress | Inkomend verkeer | |
| TotalRequests | Transacties | |

### <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

Voor Cosmos DB zijn equivalente gegevens zoals hieronder weer gegeven:

| Metrische gegevens in klassieke waarschuwingen | Equivalente metrische gegevens in nieuwe waarschuwingen | Opmerkingen|
|--------------------------|---------------------------------|---------|
| AvailableStorage | AvailableStorage||
| Gegevensgrootte | DataUsage| |
| Aantal documenten | DocumentCount||
| Index grootte | IndexUsage||
| Service niet beschikbaar | ServiceAvailability||
| TotalRequestUnits | TotalRequestUnits||
| Vertraagde aanvragen | TotalRequests met dimensie "status code" = "429"| Gemiddeld aggregatie type is gecorrigeerd voor Count|
| Interne serverfouten | TotalRequests met dimensie "status code" = "500"}| Gemiddeld aggregatie type is gecorrigeerd voor Count|
| HTTP 401 | TotalRequests met dimensie "status code" = "401"| Gemiddeld aggregatie type is gecorrigeerd voor Count|
| Http 400 | TotalRequests met dimensie "status code" = "400"| Gemiddeld aggregatie type is gecorrigeerd voor Count|
| Totaal aantal aanvragen | TotalRequests| Het aggregatie type ' Max ' is gecorrigeerd voor ' count '|
| Aanvraag kosten voor aantal Mongo| MongoRequestCharge met dimensie "opdrachtnaam" = "aantal"||
| Aanvraag frequentie aantal Mongo | MongoRequestsCount met dimensie "opdrachtnaam" = "aantal"||
| Kosten voor het verwijderen van Mongo-aanvragen | MongoRequestCharge met dimensie "opdrachtnaam" = "verwijderen"||
| Aantal Mongo-aanvragen voor verwijderen | MongoRequestsCount met dimensie "opdrachtnaam" = "verwijderen"||
| Kosten voor invoeging van Mongo-aanvragen | MongoRequestCharge met dimensie "opdrachtnaam" = "Insert"||
| Aantal Mongo invoegen | MongoRequestsCount met dimensie "opdrachtnaam" = "Insert"||
| Kosten voor Mongo-query aanvragen | MongoRequestCharge met dimensie "opdrachtnaam" = "zoeken"||
| Frequentie van Mongo-query aanvragen | MongoRequestsCount met dimensie "opdrachtnaam" = "zoeken"||
| Kosten voor Mongo-update aanvragen | MongoRequestCharge met dimensie "opdrachtnaam" = "update"||
| Mongo mislukte aanvragen invoegen | MongoRequestCount met de dimensies "opdrachtnaam" = "Insert" en "status" = "Failed"| Gemiddeld aggregatie type is gecorrigeerd voor Count|
| Mislukte aanvragen voor Mongo-query | MongoRequestCount met de dimensies "opdrachtnaam" = "query" en "status" = "Failed"| Gemiddeld aggregatie type is gecorrigeerd voor Count|
| Aantal mislukte aanvragen van Mongo | MongoRequestCount met de dimensies "opdrachtnaam" = "Count" en "status" = "Failed"| Gemiddeld aggregatie type is gecorrigeerd voor Count|
| Mislukte aanvragen voor Mongo bijwerken | MongoRequestCount met de dimensies ' opdrachtnaam ' = ' update ' en ' status ' = ' failed '| Gemiddeld aggregatie type is gecorrigeerd voor Count|
| Andere mislukte aanvragen Mongo | MongoRequestCount met de dimensies "opdrachtnaam" = "other" en "status" = "Failed"| Gemiddeld aggregatie type is gecorrigeerd voor Count|
| Mislukte aanvragen voor Mongo verwijderen | MongoRequestCount met de dimensies "opdrachtnaam" = "verwijderen" en "status" = "Failed"| Gemiddeld aggregatie type is gecorrigeerd voor Count|

### <a name="how-equivalent-action-groups-are-created"></a>Hoe gelijkwaardige actie groepen worden gemaakt

Klassieke waarschuwings regels hebben een e-mail, webhook, een logische app en runbook-acties die zijn gekoppeld aan de waarschuwings regel zelf. Nieuwe waarschuwings regels gebruiken actie groepen die opnieuw kunnen worden gebruikt in meerdere waarschuwings regels. Het hulp programma voor migratie maakt één actie groep voor dezelfde acties, ongeacht het aantal waarschuwings regels dat de actie gebruikt. Actie groepen die zijn gemaakt met het hulp programma voor migratie, gebruiken de naamgevings indeling Migrated_AG *.

> [!NOTE]
> Klassieke waarschuwingen verzonden gelokaliseerde e-mail berichten op basis van de land instelling van de klassieke beheerder wanneer ze worden gebruikt voor het melden van klassieke beheerders rollen. Nieuwe e-mail meldingen worden verzonden via actie groepen en zijn alleen beschikbaar in het Engels.

## <a name="rollout-phases"></a>Implementatiefasen

Het migratie programma wordt in fasen geïmplementeerd voor klanten die klassieke waarschuwings regels gebruiken. Abonnements eigenaren ontvangen een e-mail wanneer het abonnement gereed is om te worden gemigreerd met het hulp programma.

> [!NOTE]
> Omdat het hulp programma in fasen wordt geïmplementeerd, ziet u mogelijk dat sommige van uw abonnementen nog niet klaar zijn om te worden gemigreerd tijdens de vroege fasen.

De meeste abonnementen zijn momenteel gemarkeerd als gereed voor migratie. Alleen abonnementen met klassieke waarschuwingen voor de volgende resource typen zijn nog steeds niet gereed voor migratie.

- Micro soft. classicCompute/domein naam/sleuven/rollen
- Micro soft. Insights/onderdelen

## <a name="who-can-trigger-the-migration"></a>Wie kan de migratie activeren?

Elke gebruiker die de ingebouwde rol van het bijhouden van mede werkers op het abonnements niveau heeft, kan de migratie activeren. Gebruikers met een aangepaste rol met de volgende machtigingen kunnen ook de migratie activeren:

- */read
- Micro soft. Insights/actiongroups/*
- Micro soft. Insights/AlertRules/*
- Micro soft. Insights/metricAlerts/*
- Micro soft. AlertsManagement/smartDetectorAlertRules/*

> [!NOTE]
> Naast de machtigingen hierboven moet uw abonnement ook worden geregistreerd bij de resource provider micro soft. AlertsManagement. Dit is vereist voor het migreren van fout afwijkingen op Application Insights. 

## <a name="common-problems-and-remedies"></a>Veelvoorkomende problemen en oplossingen

Nadat u [de migratie hebt geactiveerd](alerts-using-migration-tool.md), ontvangt u een e-mail op de adressen die u hebt ontvangen om u te laten weten dat de migratie is voltooid of dat u een actie moet ondernemen. In deze sectie worden enkele veelvoorkomende problemen beschreven en wordt uitgelegd hoe u deze kunt verwerken.

### <a name="validation-failed"></a>Validatie is mislukt

Vanwege een aantal recente wijzigingen in de regels voor klassieke waarschuwingen in uw abonnement, kan het abonnement niet worden gemigreerd. Dit probleem is tijdelijk. U kunt de migratie opnieuw starten nadat de migratie status in een paar dagen weer **gereed is voor migratie** .

### <a name="scope-lock-preventing-us-from-migrating-your-rules"></a>Bereik vergrendeling voor komen dat uw regels worden gemigreerd

Als onderdeel van de migratie worden nieuwe metrische waarschuwingen en nieuwe actie groepen gemaakt en vervolgens worden klassieke waarschuwings regels verwijderd. Een bereik vergrendeling kan er echter voor zorgen dat we geen resources kunnen maken of verwijderen. Afhankelijk van de bereik vergrendeling kunnen sommige of alle regels niet worden gemigreerd. U kunt dit probleem oplossen door de bereik vergrendeling voor het abonnement, de resource groep of de resource die wordt vermeld in het [migratie hulpprogramma](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel), te verwijderen en de migratie opnieuw te activeren. Bereik vergrendeling kan niet worden uitgeschakeld en moet worden verwijderd tijdens het migratie proces. Meer [informatie over het beheren van bereik vergrendelingen](../../azure-resource-manager/management/lock-resources.md#portal).

### <a name="policy-with-deny-effect-preventing-us-from-migrating-your-rules"></a>Beleid met een ' deny '-effect waardoor we uw regels niet kunnen migreren

Als onderdeel van de migratie worden nieuwe metrische waarschuwingen en nieuwe actie groepen gemaakt en vervolgens worden klassieke waarschuwings regels verwijderd. Een [Azure Policy](../../governance/policy/index.yml) toewijzing kan er echter voor zorgen dat we geen resources kunnen maken. Afhankelijk van de beleids toewijzing kunnen sommige of alle regels niet worden gemigreerd. De beleids toewijzingen die het proces blok keren, worden weer gegeven in het [hulp programma voor migratie](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel). Los dit probleem op door een van de volgende:

- Met uitzonde ring van de abonnementen, resource groepen of afzonderlijke resources tijdens het migratie proces van de beleids toewijzing. Meer [informatie over het beheren van beleids regels voor uitsluiting](../../governance/policy/tutorials/create-and-manage.md#remove-a-non-compliant-or-denied-resource-from-the-scope-with-an-exclusion).
- Stel de afdwingings modus in op **uitgeschakeld** op de beleids toewijzing. Meer [informatie over de eigenschap enforcementMode van de beleids toewijzing](../../governance/policy/concepts/assignment-structure.md#enforcement-mode).
- Stel een Azure Policy-uitzonde ring (preview) in voor de abonnementen, resource groepen of afzonderlijke resources aan de beleids toewijzing. Meer [informatie over de Azure Policy](../../governance/policy/concepts/exemption-structure.md)-uitzonderings structuur.
- Het verwijderen of wijzigen van het effect op ' disabled ', ' audit ', ' append ' of ' Modify ' (dat kan bijvoorbeeld problemen met betrekking tot ontbrekende labels oplossen). Meer [informatie over het beheren van beleids effecten](../../governance/policy/concepts/definition-structure.md#policy-rule).

## <a name="next-steps"></a>Volgende stappen

- [Het hulpprogramma voor migratie gebruiken](alerts-using-migration-tool.md)
- [Voorbereiden voor de migratie](alerts-prepare-migration.md)