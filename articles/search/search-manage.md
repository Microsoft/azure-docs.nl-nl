---
title: Service beheer in de portal
titleSuffix: Azure Cognitive Search
description: Een Azure Cognitive Search-service beheren, een gehoste service voor zoeken in de Cloud op Microsoft Azure met behulp van de Azure Portal.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: azure-portal
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/24/2020
ms.openlocfilehash: 814a5afbde548891a30d941365cdd71d227b4767
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101674407"
---
# <a name="service-administration-for-azure-cognitive-search-in-the-azure-portal"></a>Service beheer voor Azure Cognitive Search in het Azure Portal

> [!div class="op_single_selector"]
>
> * [PowerShell](search-manage-powershell.md)
> * [Azure-CLI](search-manage-azure-cli.md)
> * [REST API](/rest/api/searchmanagement/)
> * [.NET SDK](/dotnet/api/microsoft.azure.management.search)
> * [Portal](search-manage.md)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Azure Cognitive Search is een volledig beheerde, op de cloud gebaseerde zoek service die wordt gebruikt voor het bouwen van een uitgebreide zoek ervaring in aangepaste apps. In dit artikel worden de Service beheer taken behandeld die u kunt uitvoeren in de [Azure Portal](https://portal.azure.com) voor een zoek service die u al hebt ingericht. Service beheer is lichter ontwerp, beperkt tot de volgende taken:

* Controleer de opslag met behulp van de koppeling voor het **gebruik** van middel grote pagina's.
* Controleer de query volumes en latentie met behulp van de **bewakings** koppeling middel grote pagina's en of aanvragen zijn beperkt.
* Toegang beheren via de pagina **sleutels** aan de linkerkant.
* Pas de capaciteit aan met behulp van de pagina **schalen** aan de linkerkant.

Dezelfde taken die in de portal worden uitgevoerd, kunnen ook programmatisch worden verwerkt via de [beheer-api's](/rest/api/searchmanagement/) en [AZ. Search Power shell-module](search-manage-powershell.md). Beheer taken worden volledig weer gegeven in de portal-en programmatische interface. Er is geen specifieke beheer taak die alleen beschikbaar is in één modale versie.

Azure Cognitive Search maakt gebruik van andere Azure-Services voor uitgebreidere bewaking en beheer. De enige gegevens die in een zoek service zijn opgeslagen, zijn inhoud (indexen, indexerings-en gegevens bron definities en andere objecten). De metrische gegevens die worden gerapporteerd aan portal pagina's, worden uit interne logboeken getrokken tijdens een cyclus van 30 dagen. Voor door de gebruiker bewaakte logboek registratie en aanvullende gebeurtenissen hebt u [Azure monitor](../azure-monitor/index.yml)nodig. 

## <a name="fixed-service-properties"></a>Eigenschappen vaste service

Verschillende aspecten van een zoek service worden bepaald wanneer de service wordt ingericht en kan later niet worden gewijzigd:

* Service naam (u kunt de naam van een service niet wijzigen)
* Service locatie (u kunt momenteel geen onbeschadigde service verplaatsen naar een andere regio)
* Maximum aantal replica's en partities (bepaald door de laag, Basic of Standard)

Als u bent begonnen met het maximum van één partitie en u nu meer partities nodig hebt, moet u [een nieuwe service](search-create-service-portal.md) op een hogere laag maken en uw inhoud opnieuw maken op de nieuwe service. 

## <a name="administrator-rights"></a>Beheerders rechten

Het inrichten of buiten gebruik stellen van de service zelf kan worden uitgevoerd door een beheerder van een Azure-abonnement of een mede beheerder.

Met betrekking tot toegang tot het eind punt heeft iedereen die toegang heeft tot de service-URL en een API-sleutel toegang tot inhoud. Zie [de API-sleutels beheren](search-security-api-keys.md)voor meer informatie over sleutels.

* Alleen-lezen toegang tot de service is query rechten, die meestal worden verleend aan een client toepassing door de URL en een query-API-sleutel op te geven.
* Lees-/schrijftoegang biedt de mogelijkheid om Server objecten toe te voegen, te verwijderen of te wijzigen, met inbegrip van API-sleutels, indexen, Indexeer functies, gegevens bronnen en schema's. Toegang voor lezen-schrijven wordt verleend door de URL, een beheer-API-sleutel te geven.

Rechten voor service-inrichtings toestellen worden verleend via roltoewijzingen. [Toegangs beheer op basis van rollen (Azure RBAC) van Azure](../role-based-access-control/overview.md) is een autorisatie systeem dat is gebouwd op [Azure Resource Manager](../azure-resource-manager/management/overview.md) voor het inrichten van Azure-resources. 

In de context van Azure Cognitive Search bepaalt [Azure-roltoewijzingen](search-security-rbac.md) wie taken kan uitvoeren, ongeacht of ze de [Portal](search-manage.md), [Power shell](search-manage-powershell.md)of de [rest-api's van beheer](/rest/api/searchmanagement/search-howto-management-rest-api)gebruiken:

* Een service maken of verwijderen
* De service schalen
* API-sleutels verwijderen of opnieuw genereren
* Diagnostische logboek registratie inschakelen (Services maken)
* Traffic Analytics inschakelen (Services maken)

> [!TIP]
> Met behulp van Azure-mechanismen kunt u een abonnement of resource vergren delen om te voor komen dat uw zoek service per ongeluk of onbevoegde wordt verwijderd door gebruikers met beheerders rechten. Zie voor meer informatie [bronnen vergren delen om onverwachte verwijdering te voor komen](../azure-resource-manager/management/lock-resources.md).

## <a name="logging-and-system-information"></a>Logboek registratie-en systeem gegevens

Op de basis-laag en hierboven bewaakt micro soft alle Azure Cognitive Search Services voor 99,9% Beschik baarheid per service level agreements (SLA). Als de service traag is of de door Voer van een aanvraag onder de drempel waarde voor de SLA ligt, worden de beschik bare logboek bestanden door ondersteunings teams gecontroleerd en wordt het probleem opgelost.

Azure Cognitive Search maakt gebruik van [Azure monitor](../azure-monitor/index.yml) voor het verzamelen en opslaan van indexering en query-activiteiten. In een zoek service zelf wordt alleen de inhoud opgeslagen (indexen, Indexer definities, definities van gegevens bronnen, definities van vaardig heden, synoniemen kaarten). Cache-en logboek gegevens worden in een Azure Storage-account buiten gebruik gestored. Zie [logboek gegevens verzamelen en analyseren](search-monitor-logs.md)voor meer informatie over het registreren van indexering en het uitvoeren van query-workloads.

In het kader van algemene informatie over uw service kunt u op de volgende manieren informatie verkrijgen met alleen de ingebouwde voorzieningen in azure Cognitive Search:

* Via de pagina **overzicht** van services, via meldingen, eigenschappen en status berichten.
* [Power shell](search-manage-powershell.md) of het [beheer rest API](/rest/api/searchmanagement/) gebruiken om [service-eigenschappen te verkrijgen](/rest/api/searchmanagement/services). Er zijn geen nieuwe gegevens of bewerkingen beschikbaar op de programma-laag. De interfaces bestaan zodat u scripts kunt schrijven.

## <a name="monitor-resource-usage"></a>Resource gebruik bewaken

In het dash board is bron bewaking beperkt tot de informatie die wordt weer gegeven in het service dashboard en enkele metrische gegevens die u kunt verkrijgen door de service te doorzoeken. In het service dashboard, in de sectie gebruik, kunt u snel bepalen of partitie bron niveaus voldoende zijn voor uw toepassing. U kunt externe resources, zoals Azure-bewaking, inrichten als u vastgelegde gebeurtenissen wilt vastleggen en persistent wilt maken. Zie [Azure Cognitive Search bewaken](search-monitor-usage.md)voor meer informatie.

Met de REST API van de zoek service kunt u via programma code een aantal op documenten en indexen ontvangen: 

* [Indexstatistieken ophalen](/rest/api/searchservice/Get-Index-Statistics)
* [Documenten tellen](/rest/api/searchservice/count-documents)

## <a name="disaster-recovery-and-service-outages"></a>Herstel na nood gevallen en service storingen

Hoewel we uw gegevens kunnen bijvoegen, biedt Azure Cognitive Search geen directe failover van de service als er een storing optreedt op het niveau van het cluster of het Data Center. Als een cluster in het Data Center uitvalt, detecteert en werkt het operations-team de service te herstellen. U krijgt tijdens het herstellen van de service een uitval tijd, maar u kunt Service tegoed aanvragen om service niet-beschik baarheid per [Service Level Agreement (Sla)](https://azure.microsoft.com/support/legal/sla/search/v1_0/)te compenseren. 

Als er een continue service vereist is in het geval van fatale storingen buiten de controle van micro soft, kunt u [een extra service](search-create-service-portal.md) in een andere regio inrichten en een geo-replicatie strategie implementeren om ervoor te zorgen dat indexen volledig redundant zijn in alle services.

Klanten die [Indexeer functies](search-indexer-overview.md) gebruiken om indexen in te vullen en te vernieuwen, kunnen herstel na nood gevallen afhandelen via geo-specifieke Indexeer functies die gebruikmaken van dezelfde gegevens bron. Twee services in verschillende regio's, elk waarop een Indexeer functie wordt uitgevoerd, kunnen dezelfde gegevens bron indexeren om geo-redundantie te garanderen. Als u gegevens bronnen indexeert die ook geo-redundant zijn, moet u er rekening mee houden dat Azure Cognitive Search-Indexeer functies alleen incrementele indexering kunnen uitvoeren (het samen voegen van updates van nieuwe, gewijzigde of verwijderde documenten) van primaire replica's. Zorg er bij een failover-gebeurtenis voor dat u de Indexeer functie opnieuw naar de nieuwe primaire replica toewijst. 

Als u geen Indexeer functies gebruikt, gebruikt u uw toepassings code om objecten en gegevens parallel naar verschillende zoek services te pushen. Zie [prestaties en optimalisatie in Azure Cognitive Search](search-performance-optimization.md)voor meer informatie.

## <a name="backup-and-restore"></a>Back-ups en herstellen

Omdat Azure Cognitive Search geen primaire oplossing voor gegevens opslag is, bieden we geen formeel mechanisme voor Self-Service back-up en herstel. U kunt echter de voorbeeld code **index-Backup-Restore** in dit [Azure Cognitive Search .net-voor beeld opslag plaats](https://github.com/Azure-Samples/azure-search-dotnet-samples) gebruiken om een back-up te maken van de index definitie en moment opname naar een reeks json-bestanden en vervolgens deze bestanden te gebruiken om de index te herstellen, indien nodig. Met dit hulp programma kunt u ook indexen verplaatsen tussen service lagen.

Anders is uw toepassings code die wordt gebruikt voor het maken en vullen van een index de optie voor het terugzetten van de index. Als u een index opnieuw wilt samen stellen, verwijdert u deze (ervan uitgaande dat deze bestaat), maakt u de index opnieuw in de service en laadt u deze opnieuw door gegevens op te halen uit uw primaire gegevens opslag.

## <a name="scale-up-or-down"></a>Omhoog of omlaag schalen

Elke zoek service begint met mini maal één replica en één partitie. Als u zich hebt geregistreerd voor een [laag die meer capaciteit ondersteunt](search-limits-quotas-capacity.md), klikt u op **schalen** in het navigatie deel venster aan de linkerkant om het resource gebruik aan te passen.

Wanneer u capaciteit toevoegt via een van de resources, gebruikt de service deze automatisch. Er is geen verdere actie vereist voor uw onderdeel, maar er is een lichte vertraging voordat de impact van de nieuwe resource wordt gerealiseerd. Het kan 15 minuten of langer duren om aanvullende resources in te richten.

### <a name="add-replicas"></a>Replica's toevoegen

Het verhogen van query's per seconde (QPS) of het bereiken van hoge Beschik baarheid geschiedt door het toevoegen van replica's. Elke replica heeft één exemplaar van een index, dus er wordt nog één replica omgezet in een meer index die beschikbaar is voor het verwerken van aanvragen voor service query's. Er zijn mini maal drie replica's vereist voor hoge Beschik baarheid (Zie [capaciteit aanpassen](search-capacity-planning.md) voor meer informatie).

Een zoek service met meer replica's kan Load Balancing query-aanvragen over een groter aantal indexen. Op basis van een niveau van het query volume is de query doorvoer sneller wanneer er meer exemplaren van de index beschikbaar zijn voor de service van de aanvraag. Als u een query latentie ondervindt, kunt u een positieve invloed op de prestaties verwachten zodra de extra replica's online zijn.

Hoewel de query doorvoer tijdens het toevoegen van replica's wordt bereikt, is dit niet precies twee of drie maal wanneer u replica's aan uw service toevoegt. Alle zoek toepassingen zijn onderhevig aan externe factoren die de query prestaties kunnen impinge. Complexe query's en netwerk latentie zijn twee factoren die bijdragen aan variaties in de reactie tijden van query's.

### <a name="add-partitions"></a>Partities toevoegen

Het is gebruikelijk om replica's toe te voegen, maar wanneer opslag is beperkt, kunt u partities toevoegen om meer capaciteit te krijgen. De laag waarop u de service hebt ingericht, bepaalt of de partities kunnen worden toegevoegd. De laag basis is op één partitie vergrendeld. Voor de standaard lagen en de bovenstaande ondersteuning worden extra partities ondersteund.

Partities worden toegevoegd in de delers van 12 (met name 1, 2, 3, 4, 6 of 12). Dit is een artefact van sharding. Er wordt een index gemaakt in 12 Shards, die allemaal op één partitie kan worden opgeslagen of op dezelfde manier kan worden onderverdeeld in 2, 3, 4, 6 of 12 partities (één Shard per partitie).

### <a name="remove-replicas"></a>Replica's verwijderen

Na Peri Oden van hoge query volumes kunt u de schuif regelaar gebruiken om replica's te reduceren nadat de geladen Zoek query's zijn genormaliseerd (bijvoorbeeld nadat de verkoop van de feest dagen is voltooid). Er zijn geen verdere stappen voor uw onderdeel vereist. Het aantal replica's relinquishes virtuele machines in het Data Center verlagen. Uw bewerkingen voor het opnemen van query's en gegevens worden nu op minder Vm's uitgevoerd dan voorheen. De minimum vereiste is één replica.

### <a name="remove-partitions"></a>Partities verwijderen

In tegens telling tot het verwijderen van replica's, waarvoor u geen extra moeite hoeft te doen, hebt u mogelijk werk nodig als u meer opslag gebruikt dan kan worden verkleind. Als uw oplossing bijvoorbeeld drie partities gebruikt, genereert Overweeg naar een of twee partities een fout als de nieuwe opslag ruimte minder is dan vereist is om uw index te hosten. Zoals u kunt verwachten, zijn uw keuzes het verwijderen van indexen of documenten binnen een gekoppelde index om ruimte vrij te maken of de huidige configuratie te houden.

Er is geen detectie methode die aangeeft welke index Shards worden opgeslagen op specifieke partities. Elke partitie biedt ongeveer 25 GB opslag ruimte, dus u moet de opslag beperken tot een grootte die kan worden aangepast aan het aantal partities dat u hebt. Als u wilt terugkeren naar één partitie, moeten alle 12 Shards passen.

Als u hulp nodig hebt bij het plannen van de toekomst, wilt u mogelijk de opslag controleren (met behulp van [index statistieken ophalen](/rest/api/searchservice/Get-Index-Statistics)) om te zien hoeveel u werkelijk hebt gebruikt. 

## <a name="next-steps"></a>Volgende stappen

* Automatiseren met [Power shell](search-manage-powershell.md) of de [Azure cli](search-manage-azure-cli.md)

* [Prestaties en optimalisatie](search-performance-optimization.md) technieken controleren

* [Beveiligings functies](search-security-overview.md) voor het beveiligen van inhoud en bewerkingen controleren

* [Diagnostische logboek registratie](search-monitor-logs.md) inschakelen voor het bewaken van query's en het indexeren van werk belastingen