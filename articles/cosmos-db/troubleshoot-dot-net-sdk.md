---
title: Problemen vaststellen en oplossen bij het gebruik van Azure Cosmos DB .NET SDK
description: Gebruik functies als logboek registratie aan client zijde en andere hulpprogram ma's van derden voor het identificeren, diagnosticeren en Azure Cosmos DB oplossen van problemen met het gebruik van .NET SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 02/05/2021
ms.author: anfeldma
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: 04813b9d70557314e619fded5294644f5f6fadf5
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831243"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Problemen vaststellen en oplossen bij het gebruik van Azure Cosmos DB .NET SDK
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Java SDK v4](troubleshoot-java-sdk-v4-sql.md)
> * [Async Java-SDK v2](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

Dit artikel heeft betrekking op veelvoorkomende problemen, tijdelijke oplossingen, diagnostische stappen en hulpprogram ma's wanneer u de [.NET SDK](sql-api-sdk-dotnet.md) gebruikt met Azure Cosmos DB SQL API-accounts.
De .NET-SDK biedt logische weer gave aan de client zijde om toegang te krijgen tot de Azure Cosmos DB SQL-API. In dit artikel worden tools en benaderingen beschreven om u te helpen bij eventuele problemen.

## <a name="checklist-for-troubleshooting-issues"></a>Controle lijst voor het oplossen van problemen

Bekijk de volgende controle lijst voordat u uw toepassing naar productie gaat verplaatsen. Met de controle lijst voor komt u dat er verschillende veelvoorkomende problemen worden weer geven. U kunt ook snel vaststellen wanneer er een probleem optreedt:

*    De nieuwste [SDK](sql-api-sdk-dotnet-standard.md)gebruiken. Preview-Sdk's mogen niet worden gebruikt voor productie. Dit voor komt dat bekende problemen die al zijn opgelost, worden gerepareerd.
*    Bekijk de [Tips voor prestaties](performance-tips.md)en volg de aanbevolen procedures. Dit helpt voor komen dat schalen, latentie en andere prestatie problemen worden opgelost.
*    Schakel de SDK-logboek registratie in om u te helpen bij het oplossen van een probleem. Het inschakelen van de logboek registratie kan van invloed zijn op de prestaties, zodat het het beste kan worden ingeschakeld wanneer u problemen oplost. U kunt de volgende logboeken inschakelen:
*    [Metrische gegevens vastleggen](./monitor-cosmos-db.md) met behulp van de Azure Portal. Met metrische gegevens van de portal wordt de Azure Cosmos DB telemetrie weer gegeven. Dit is handig om te bepalen of het probleem overeenkomt met Azure Cosmos DB of van de client zijde.
*    Registreer de [Diagnostische teken reeks](/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring) in de v2 SDK of [diagnoses](/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics) in de V3 SDK van de punt bewerkings reacties.
*    De [metrische gegevens](sql-api-query-metrics.md) van de SQL-query registreren bij alle antwoorden op query's 
*    Volg de installatie van de [SDK-logboek registratie]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md)

Bekijk de sectie [veelvoorkomende problemen en tijdelijke oplossingen](#common-issues-workarounds) in dit artikel.

Raadpleeg de [sectie met github-problemen](https://github.com/Azure/azure-cosmos-dotnet-v2/issues) die actief worden bewaakt. Controleer of er een soortgelijk probleem met een tijdelijke oplossing al is ingediend. Als u geen oplossing hebt gevonden, kunt u een GitHub-probleem oplossen. U kunt een ondersteunings streepje openen voor urgente problemen.


## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Veelvoorkomende problemen en tijdelijke oplossingen

### <a name="general-suggestions"></a>Algemene suggesties
* Voer, indien mogelijk, uw app uit in dezelfde Azure-regio als uw Azure Cosmos DB-account. 
* U kunt problemen met de connectiviteit/Beschik baarheid ondervinden als gevolg van een gebrek aan resources op de client computer. U wordt aangeraden uw CPU-gebruik te bewaken op knoop punten waarop de Azure Cosmos DB-client wordt uitgevoerd, en omhoog/omlaag te schalen als deze bij hoge belasting worden uitgevoerd.

### <a name="check-the-portal-metrics"></a>Controleer de metrische gegevens van de portal
Door de [metrische gegevens](./monitor-cosmos-db.md) van de portal te controleren, kunt u bepalen of het een probleem aan de client zijde is of dat er een probleem is met de service. Als de metrische gegevens bijvoorbeeld een hoog aantal aanvragen met een rente beperking bevatten (HTTP-status code 429), wat betekent dat de aanvraag wordt beperkt, controleert u de sectie [aanvraag snelheid is te groot](troubleshoot-request-rate-too-large.md) . 

## <a name="retry-logic"></a>Logica voor opnieuw proberen <a id="retry-logics"></a>
Cosmos DB SDK bij een IO-fout probeert de mislukte bewerking opnieuw uit te voeren als opnieuw proberen in de SDK kans van slagen heeft. Als er een nieuwe poging wordt gedaan om fouten te voor komen, is het een goed idee. Het is raadzaam om de nieuwste SDK te gebruiken als de logica voor nieuwe pogingen voortdurend wordt verbeterd.

1. Bij lees-en query-i/o-fouten wordt opnieuw geprobeerd door de SDK zonder de halen aan de eind gebruiker.
2. Schrijf bewerkingen (maken, Upsert, vervangen, verwijderen) zijn ' not ' idempotent en daarom kan SDK niet altijd een blinde schrijf bewerking uitvoeren. De toepassings logica van de gebruiker is vereist voor het afhandelen van de fout en het opnieuw proberen.
3. [Problemen met SDK-Beschik baarheid](troubleshoot-sdk-availability.md) legt nieuwe pogingen voor Cosmos DB accounts met meerdere regio's uit.

## <a name="common-error-status-codes"></a>Algemene fout status codes <a id="error-codes"></a>

| Statuscode | Description | 
|----------|-------------|
| 400 | Ongeldige aanvraag (afhankelijk van het fout bericht)| 
| 401 | [Niet geautoriseerd](troubleshoot-unauthorized.md) | 
| 403 | [Verboden](troubleshoot-forbidden.md) |
| 404 | [Kan de resource niet vinden](troubleshoot-not-found.md) |
| 408 | [Time-out van aanvraag](troubleshoot-dot-net-sdk-request-timeout.md) |
| 409 | Conflict fout is opgetreden wanneer de ID van een resource voor een schrijf bewerking is genomen door een bestaande resource. Gebruik een andere ID voor de resource om dit probleem op te lossen. de ID moet uniek zijn binnen alle documenten met dezelfde partitie sleutel waarde. |
| 410 | Verdwenen uitzonde ringen (tijdelijke storing die SLA niet overtreden) |
| 412 | Fout voor de voor waarde is waar de bewerking een eTag heeft opgegeven die afwijkt van de versie die beschikbaar is op de server. Dit is een optimistische gelijktijdigheids fout. Voer de aanvraag opnieuw uit nadat u de meest recente versie van de resource hebt gelezen en de eTag voor de aanvraag hebt bijgewerkt.
| 413 | [De aanvraag entiteit is te groot](concepts-limits.md#per-item-limits) |
| 429 | [Te veel aanvragen](troubleshoot-request-rate-too-large.md) |
| 449 | Tijdelijke fout die alleen optreedt bij schrijf bewerkingen en veilig kan worden geprobeerd |
| 500 | De bewerking is mislukt vanwege een onverwachte service fout. Neem contact op met ondersteuning. Zie een [ondersteunings probleem voor Azure](https://aka.ms/azure-support)archiveren. |
| 503 | [Service niet beschikbaar](troubleshoot-service-unavailable.md) | 

### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Uitputting van de poort van Azure SNAT (PAT)

Als uw app is geïmplementeerd op [Azure virtual machines zonder een openbaar IP-adres](../load-balancer/load-balancer-outbound-connections.md), worden de standaard [Azure SNAT-poorten](../load-balancer/load-balancer-outbound-connections.md#preallocatedports) gebruikt om verbinding te maken met een wille keurig eind punt buiten uw VM. Het aantal verbindingen dat van de virtuele machine naar het Azure Cosmos DB-eind punt is toegestaan, wordt beperkt door de [Azure SNAT-configuratie](../load-balancer/load-balancer-outbound-connections.md#preallocatedports). Deze situatie kan leiden tot verbindings beperking, sluiting verbinding of de hierboven vermelde [time-outs van aanvragen](troubleshoot-dot-net-sdk-request-timeout.md).

 Azure SNAT-poorten worden alleen gebruikt als uw virtuele machine een privé-IP-adres heeft om verbinding te maken met een openbaar IP-adres. Er zijn twee manieren om de limiet voor Azure SNAT te voor komen (als u al een exemplaar van één client gebruikt voor de hele toepassing):

* Voeg uw Azure Cosmos DB Service-eind punt toe aan het subnet van uw virtuele Azure Virtual Machines-netwerk. Zie [Azure Virtual Network Service-eind punten](../virtual-network/virtual-network-service-endpoints-overview.md)voor meer informatie. 

    Wanneer het service-eind punt is ingeschakeld, worden de aanvragen niet langer verzonden vanuit een openbaar IP-adres naar Azure Cosmos DB. In plaats daarvan worden de identiteit van het virtuele netwerk en het subnet verzonden. Deze wijziging kan ertoe leiden dat de firewall wordt neergezet als alleen open bare IP-adressen zijn toegestaan. Als u een firewall gebruikt en u het service-eind punt inschakelt, voegt u een subnet toe aan de firewall door gebruik te maken van [Virtual Network acl's](/previous-versions/azure/virtual-network/virtual-networks-acl).
* Wijs een [openbaar IP-adres toe aan uw Azure-VM](../load-balancer/troubleshoot-outbound-connection.md#assignilpip).

### <a name="high-network-latency"></a><a name="high-network-latency"></a>Hoge netwerk latentie
Hoge netwerk latentie kan worden geïdentificeerd met behulp van de [Diagnostische teken reeks](/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?preserve-view=true&view=azure-dotnet) in de v2 SDK of [diagnoses](/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics?preserve-view=true&view=azure-dotnet#Microsoft_Azure_Cosmos_ResponseMessage_Diagnostics) in de V3 SDK.

Als er geen [time-outs](troubleshoot-dot-net-sdk-request-timeout.md) aanwezig zijn en de diagnostische gegevens afzonderlijke aanvragen weer geven waarbij de hoge latentie duidelijk is voor het verschil tussen `ResponseTime` en `RequestStartTime` , bijvoorbeeld (>300 milliseconden in dit voor beeld):

```bash
RequestStartTime: 2020-03-09T22:44:49.5373624Z, RequestEndTime: 2020-03-09T22:44:49.9279906Z,  Number of regions attempted:1
ResponseTime: 2020-03-09T22:44:49.9279906Z, StoreResult: StorePhysicalAddress: rntbd://..., ...
```

Deze latentie kan meerdere oorzaken hebben:

* Uw toepassing wordt niet uitgevoerd in dezelfde regio als uw Azure Cosmos DB-account.
* De configuratie van de [PreferredLocations](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) of [ApplicationRegion](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) is onjuist en er wordt geprobeerd verbinding te maken met een andere regio waar uw toepassing momenteel wordt uitgevoerd.
* Er is mogelijk een knel punt op de netwerk interface vanwege veel verkeer. Als de toepassing wordt uitgevoerd op Azure Virtual Machines, zijn er mogelijke oplossingen:
    * Overweeg het gebruik [van een virtuele machine met versneld netwerken ingeschakeld](../virtual-network/create-vm-accelerated-networking-powershell.md).
    * [Versneld netwerken inschakelen op een bestaande virtuele machine](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms).
    * Overweeg het gebruik van een [hogere virtuele machine](../virtual-machines/sizes.md).

### <a name="common-query-issues"></a>Veelvoorkomende query problemen

Met de [metrische gegevens](sql-api-query-metrics.md) van de query kunt u bepalen waar de query de meeste tijd in bebesteding neemt. Vanuit de metrische gegevens van de query kunt u zien hoeveel er wordt uitgegeven aan de back-end van de client. Meer informatie over het [oplossen van query prestaties](troubleshoot-query-performance.md).

* Als de back-end-query snel wordt geretourneerd en een grote tijd op de client wordt gespendeerd, controleert u de belasting van de computer. Het is waarschijnlijk dat er onvoldoende bronnen zijn en de SDK wacht totdat de resources beschikbaar zijn voor het verwerken van het antwoord.
* Als de back-end-query langzaam is, [optimaliseert u de query](troubleshoot-query-performance.md) en bekijkt u het huidige [indexerings beleid](index-overview.md)

    > [!NOTE]
    > Voor betere prestaties raden we u aan Windows 64-bits host te verwerken. De SQL SDK bevat een systeem eigen ServiceInterop.dll om query's lokaal te parseren en te optimaliseren. ServiceInterop.dll wordt alleen ondersteund op het Windows x64-platform. Voor Linux en andere niet-ondersteunde platforms waarbij ServiceInterop.dll niet beschikbaar is, wordt er een extra netwerk aanroep naar de gateway verzonden om de geoptimaliseerde query te krijgen.

Als de volgende fout optreedt: `Unable to load DLL 'Microsoft.Azure.Cosmos.ServiceInterop.dll' or one of its dependencies:` en u Windows gebruikt, moet u een upgrade uitvoeren naar de nieuwste versie van Windows.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over prestatie richtlijnen voor [.net v3](performance-tips-dotnet-sdk-v3-sql.md) en [.NET v2](performance-tips.md)
* Meer informatie over de [op reactor gebaseerde Java-sdk's](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-pattern-guide.md)

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Azure SNAT (PAT) port exhaustion]: #snat
[Production check list]: #production-check-list