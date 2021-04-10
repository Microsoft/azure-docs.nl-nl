---
title: Bewerkingen en activiteiten bewaken
titleSuffix: Azure Cognitive Search
description: Schakel logboek registratie in, Bekijk metrische gegevens over de query activiteit, het resource gebruik en andere systeem data van een Azure Cognitive Search-service.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: aa224a09317aafd49ae10c89ae0c50455ddd4602
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105709919"
---
# <a name="monitor-operations-and-activity-of-azure-cognitive-search"></a>Bewerkingen en activiteiten van Azure Cognitive Search bewaken

Dit artikel bevat een overzicht van de bewakings concepten en hulpprogram ma's voor Azure Cognitive Search. Voor holistische bewaking kunt u een combi natie van ingebouwde functionaliteit en add-on-services zoals Azure Monitor gebruiken.

Samen kunt u het volgende volgen:

* Service: status/Beschik baarheid en wijzigingen in service configuratie.
* Opslag: zowel gebruikt als beschikbaar, met aantallen voor elk inhouds type ten opzichte van het quotum dat is toegestaan voor de servicelaag.
* Query activiteit: volume, latentie en beperkte of verwijderde query's. Voor geregistreerde query aanvragen is [Azure monitor](#add-azure-monitor)vereist.
* Indexerings activiteit: vereist [Diagnostische logboek registratie](#add-azure-monitor) met Azure monitor.

Een zoek service biedt geen ondersteuning voor verificatie per gebruiker, zodat er geen identiteits gegevens worden gevonden in de logboeken.

## <a name="built-in-monitoring"></a>Ingebouwde bewaking

De ingebouwde bewaking verwijst naar activiteiten die door een zoek service worden vastgelegd. Met uitzonde ring van diagnostische gegevens is geen configuratie vereist voor dit bewakings niveau.

Azure Cognitive Search onderhoudt interne gegevens over een rolling planning van 30 dagen voor rapportage over de gegevens over de service status en de query, die u kunt vinden in de portal of via deze [rest-api's](#monitoring-apis).

De volgende scherm afbeelding helpt u bij het vinden van controle-informatie in de portal. Gegevens worden beschikbaar zodra u de service gaat gebruiken. Portal pagina's worden elke paar minuten vernieuwd.

* Op het tabblad **controle** , op de hoofd pagina overzicht, wordt een query volume, latentie en of de service onder druk staat weer gegeven.
* **Activiteiten logboek**, in het navigatie deel venster aan de linkerkant, is verbonden met Azure Resource Manager. Het activiteiten logboek rapporteert over acties die door Resource Manager worden uitgevoerd: Beschik baarheid van de service en status, wijzigingen in capaciteit (replica's en partities) en activiteiten die betrekking hebben op de API-sleutel.
* **Bewakings** instellingen, verder omlaag, biedt Configureer bare waarschuwingen, de visualisatie van metrische gegevens en Diagnostische logboeken. Maak deze wanneer u ze nodig hebt. Zodra de gegevens zijn verzameld en opgeslagen, kunt u de informatie voor inzichten opvragen of visualiseren.

  ![Integratie in een zoek service Azure Monitor](./media/search-monitor-usage/azure-monitor-search.png
 "Integratie in een zoek service Azure Monitor")

> [!NOTE]
> Omdat Portal pagina's om de paar minuten worden vernieuwd, zijn de gerapporteerde aantallen ongeveer zo ontworpen dat u een algemeen idee hebt van de manier waarop uw systeem bezig is met het verwerken van aanvragen. Werkelijke metrische gegevens, zoals query's per seconde (QPS), kunnen hoger of lager zijn dan het nummer dat op de pagina wordt weer gegeven. Als Precision een vereiste is, kunt u overwegen Api's te gebruiken.

<a name="monitoring-apis"> </a>

### <a name="apis-useful-for-monitoring"></a>Api's die nuttig zijn voor bewaking

U kunt de volgende Api's gebruiken voor het ophalen van dezelfde informatie die is gevonden op de tabbladen bewaking en gebruik van de portal.

* [Service statistieken ophalen](/rest/api/searchservice/get-service-statistics)
* [Index statistieken ophalen](/rest/api/searchservice/get-index-statistics)
* [Aantal documenten ophalen](/rest/api/searchservice/count-documents)
* [De Indexeer functie-status ophalen](/rest/api/searchservice/get-indexer-status)

### <a name="activity-logs-and-service-health"></a>Activiteiten logboeken en service status

Op de pagina [**activiteiten logboek**](../azure-monitor/essentials/activity-log.md#view-the-activity-log) in de portal wordt informatie verzameld van Azure Resource Manager en rapporten over wijzigingen in de status van de service. U kunt het activiteiten logboek controleren op kritieke, fout-en waarschuwings voorwaarden met betrekking tot de service status.

Algemene vermeldingen bevatten verwijzingen naar API-sleutels-algemene informatie meldingen, zoals *admin-sleutel ophalen* en *query sleutels ophalen*. Deze activiteiten wijzen op aanvragen die zijn gemaakt met behulp van de beheerder sleutel (objecten maken of verwijderen) of de query sleutel, maar niet de aanvraag zelf weer geven. Voor informatie over deze korrel moet u diagnostische logboek registratie configureren.

U kunt het **activiteiten logboek** openen vanuit het navigatie deel venster, of vanuit meldingen in de bovenste venster opdracht balk, of via de pagina **problemen vaststellen en oplossen** .

### <a name="monitor-storage-in-the-usage-tab"></a>Opslag bewaken op het tabblad gebruik

Voor visuele bewaking in de portal toont het tabblad **gebruik** de beschik baarheid van resources ten opzichte van de huidige [limieten](search-limits-quotas-capacity.md) die door de servicelaag worden opgelegd. Als u besluit [om te bepalen welke laag moet worden gebruikt voor werk belastingen voor de productie](search-sku-tier.md), of als u [het aantal actieve replica's en partities wilt aanpassen](search-capacity-planning.md), kunt u aan de hand van deze maat regelen zien hoe snel resources worden verbruikt en hoe goed de huidige configuratie de bestaande belasting afhandelt.

De volgende afbeelding is voor de gratis service, die wordt afgelimiteerd bij 3 objecten van elk type en 50 MB aan opslag. Een Basic-of Standard-Service heeft hogere limieten en als u het aantal partities verhoogt, wordt de maximale opslag proportioneel.

![Gebruiks status relatief ten opzichte van laag limieten](./media/search-monitor-usage/usage-tab.png
 "Gebruiks status relatief ten opzichte van laag limieten")

> [!NOTE]
> Waarschuwingen met betrekking tot opslag zijn momenteel niet beschikbaar. het opslag verbruik is niet geaggregeerd of aangemeld bij de **AzureMetrics** -tabel in azure monitor. Als u waarschuwingen voor opslag wilt ontvangen, moet u [een aangepaste oplossing bouwen](../azure-monitor/insights/solutions.md) die meldingen over resources verzendt, waarbij uw code controleert op opslag grootte en het antwoord verwerkt.

<a name="add-azure-monitor"></a>

## <a name="add-on-monitoring-with-azure-monitor"></a>Bewaking van invoeg toepassingen met Azure Monitor

Veel services, met inbegrip van Azure Cognitive Search, kunnen worden geïntegreerd met [Azure monitor](../azure-monitor/index.yml) voor extra waarschuwingen, metrische gegevens en Diagnostische logboeken voor registratie. 

[Schakel diagnostische logboek registratie in](search-monitor-logs.md) voor een zoek service als u wilt bepalen hoe gegevens moeten worden verzameld en opgeslagen. Geregistreerde gebeurtenissen die zijn vastgelegd door Azure Monitor worden opgeslagen in de tabel **AzureDiagnostics** en bestaan uit operationele gegevens met betrekking tot query's en indexering.

Azure Monitor biedt verschillende opslag opties en uw keuze bepaalt hoe u de gegevens kunt gebruiken:

* Kies Azure Blob-opslag als u logboek gegevens in een Power BI rapport wilt [visualiseren](search-monitor-logs-powerbi.md) .
* Kies Log Analytics als u gegevens wilt verkennen via Kusto-query's.

Azure Monitor heeft een eigen facturerings structuur en de diagnostische logboeken waarnaar in deze sectie wordt verwezen, hebben een bijbehorende prijs. Zie [verbruik en geschatte kosten in azure monitor](../azure-monitor//usage-estimated-costs.md)voor meer informatie.

## <a name="monitor-user-access"></a>Gebruikers toegang controleren

Omdat zoek indexen een onderdeel zijn van een grotere client toepassing, is er geen ingebouwde methodologie voor het beheren of bewaken van toegang per gebruiker tot een index. Er wordt van uitgegaan dat aanvragen afkomstig zijn van een client toepassing die ofwel een beheerder of een query aanvraag aanbiedt. Lees-en schrijf bewerkingen door de beheerder omvatten het maken, bijwerken en verwijderen van objecten in de gehele service. Alleen-lezen bewerkingen zijn query's voor de verzameling documenten, met een bereik van één index. 

Wat u in de activiteiten logboeken ziet, zijn verwijzingen naar aanroepen met behulp van beheer sleutels of query sleutels. De juiste sleutel is opgenomen in aanvragen die afkomstig zijn van client code. De service is niet ingericht voor het afhandelen van identiteits tokens of imitatie.

Wanneer er bedrijfs vereisten bestaan voor autorisatie per gebruiker, wordt de aanbeveling geïntegreerd met Azure Active Directory. U kunt $filter en gebruikers identiteiten gebruiken om de [Zoek resultaten](search-security-trimming-for-azure-search-with-aad.md) van documenten die een gebruiker niet mag zien te knippen. 

Het is niet mogelijk om deze informatie afzonderlijk te registreren vanuit de query reeks die de para meter $filter bevat. Zie [query's bewaken](search-monitor-queries.md) voor meer informatie over het rapporteren van query teken reeksen.

## <a name="next-steps"></a>Volgende stappen

Fluency met Azure Monitor is essentieel voor het toezicht op elke Azure-service, inclusief resources zoals Azure Cognitive Search. Als u niet bekend bent met Azure Monitor, neemt u de tijd voor het beoordelen van artikelen die betrekking hebben op resources. Naast zelf studies is het volgende artikel een goede plaats om te beginnen.

> [!div class="nextstepaction"]
> [Azure-resources bewaken met Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md)