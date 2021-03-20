---
title: Indexeer functie toegang tot beveiligde bronnen
titleSuffix: Azure Cognitive Search
description: Conceptueel overzicht van de beveiligings opties op netwerk niveau voor Azure Data Access door Indexeer functies in azure Cognitive Search.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: bcb6e91bba367363385214806077146b1a24fe7b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92503484"
---
# <a name="indexer-access-to-content-protected-by-azure-network-security-features-azure-cognitive-search"></a>Indexeer toegang tot inhoud die is beveiligd met Azure-netwerk beveiligings functies (Azure Cognitive Search)

Azure Cognitive Search Indexeer functies kunnen tijdens de uitvoering uitgaande aanroepen naar verschillende Azure-resources maken. In dit artikel vindt u informatie over de concepten achter indexerings toegang tot inhoud die wordt beveiligd door IP-firewalls, persoonlijke eind punten of andere beveiligings mechanismen op Azure-netwerk niveau. Een Indexeer functie maakt uitgaande aanroepen in twee situaties: verbinding maken met gegevens bronnen tijdens het indexeren en verbinding maken met Inge kapselde code via een vakkennisset. In de onderstaande tabel vindt u een lijst met alle mogelijke resource typen die een Indexeer functie kan openen in een typische uitvoering.

| Resource | Doel in Indexeer functie uitvoeren |
| --- | --- |
| Azure Storage (blobs, tabellen, ADLS gen 2) | Gegevensbron |
| Azure Storage (blobs, tabellen) | Vaardig heden (in cache opslaan van verrijkte documenten en kennis archief-projecties opslaan) |
| Azure Cosmos DB (verschillende Api's) | Gegevensbron |
| Azure SQL Database | Gegevensbron |
| SQL Server op virtuele machines in Azure | Gegevensbron |
| SQL Managed Instance | Gegevensbron |
| Azure Functions | Host voor aangepaste web-API-vaardig heden |
| Cognitive Services | Gekoppeld aan de vaardig heden die worden gebruikt voor het factureren van verrijking buiten de limiet van 20 vrije documenten |

> [!NOTE]
> De cognitieve service resource die aan een vakkennisset is gekoppeld, wordt gebruikt voor facturering, op basis van de verrijkingen die worden uitgevoerd en geschreven in de zoek index. Het wordt niet gebruikt voor toegang tot de Cognitive Services-API's. Toegang tot de verrijkings pijplijn van een Indexeer functie op Cognitive Services-API's vindt plaats via een intern beveiligd communicatie kanaal, waarbij gegevens tijdens de overdracht sterk worden versleuteld en nooit op rest worden opgeslagen.

Klanten kunnen deze bronnen beveiligen via verschillende netwerk isolatie mechanismen die worden geboden door Azure. Met uitzonde ring van een cognitieve Service Resource hebben Indexeer functies beperkte mogelijkheid om toegang te krijgen tot alle resources, zelfs als ze zijn geïsoleerd van netwerken, zoals beschreven in de onderstaande tabel.

| Resource | IP-beperking | Privé-eindpunt |
| --- | --- | ---- |
| Azure Storage (blobs, tabellen, ADLS gen 2) | Alleen ondersteund als het opslag account en de zoek service zich in verschillende regio's bevinden | Ondersteund |
| Azure Cosmos DB-SQL-API | Ondersteund | Ondersteund |
| Azure Cosmos DB-Cassandra, Mongo en Gremlin-API | Ondersteund | Niet ondersteund |
| Azure SQL Database | Ondersteund | Ondersteund |
| SQL Server op virtuele machines in Azure | Ondersteund | N.v.t. |
| SQL Managed Instance | Ondersteund | N.v.t. |
| Azure Functions | Ondersteund | Ondersteund, alleen voor bepaalde lagen van Azure functions |

> [!NOTE]
> Naast de opties die hierboven worden vermeld, kunnen klanten met netwerk beveiliging Azure Storage accounts gebruikmaken van het feit dat Azure Cognitive Search een [vertrouwde micro soft-service](../storage/common/storage-network-security.md#trusted-microsoft-services)is. Dit betekent dat een specifieke zoek service virtuele netwerk-of IP-beperkingen kan omzeilen voor het opslag account en toegang heeft tot gegevens in het opslag account als het juiste op rollen gebaseerd toegangs beheer is ingeschakeld voor het opslag account. Zie [Indexeer functie verbindingen met behulp van de uitzonde ring voor vertrouwde services](search-indexer-howto-access-trusted-service-exception.md)voor meer informatie. Deze optie kan worden gebruikt in plaats van de IP-beperkings route, in het geval het opslag account of de zoek service niet kan worden verplaatst naar een andere regio.

Houd rekening met de volgende beperkingen bij het kiezen van het mechanisme voor beveiligde toegang dat een Indexeer functie moet gebruiken:

- Een Indexeer functie kan geen verbinding maken met een [service-eind punt van een virtueel netwerk](../virtual-network/virtual-network-service-endpoints-overview.md). Open bare eind punten met referenties, persoonlijke eind punten, vertrouwde service en IP-adres Sering zijn de enige ondersteunde methodologieën voor indexerings verbindingen.
- Een zoek service kan niet worden ingericht in een specifiek virtueel netwerk en wordt uitgevoerd op een virtuele machine. Deze functionaliteit wordt niet aangeboden door Azure Cognitive Search.
- Wanneer Indexeer functies (uitgaand) persoonlijke eind punten gebruiken om toegang te krijgen tot resources, kunnen er extra [kosten](https://azure.microsoft.com/pricing/details/search/) in rekening worden gebracht.

## <a name="indexer-execution-environment"></a>Uitvoerings omgeving van de Indexeer functie

Azure Cognitive Search Indexeer functies kunnen inhoud efficiënt ophalen uit gegevens bronnen, verrijkingen toevoegen aan de geëxtraheerde inhoud, eventueel projecties genereren voordat de resultaten naar de zoek index worden geschreven. Afhankelijk van het aantal verantwoordelijkheden dat aan een Indexeer functie is toegewezen, kan dit in een van de twee omgevingen worden uitgevoerd:

- Een omgeving die privé is voor een specifieke zoek service. Indexeer functies die in dergelijke omgevingen worden uitgevoerd, delen resources met andere workloads (zoals een door de klant geïnitieerde indexering of query uitvoeren van werk belastingen). Normaal gesp roken worden alleen Indexeer functies die op tekst gebaseerde indexering uitvoeren (bijvoorbeeld geen vaardig heden gebruiken) uitgevoerd in deze omgeving.
- Een omgeving met meerdere tenants die Indexeer functies hosten die veel resources zijn, zoals de omgevingen met vaardig heden. Deze omgeving wordt gebruikt voor het verenigen van de reken kundige verwerkte verwerking, waardoor er servicespecifieke bronnen beschikbaar zijn voor routine bewerkingen. Deze omgeving met meerdere tenants wordt beheerd en beveiligd door micro soft, zonder extra kosten aan de klant.

Voor elke gewenste uitvoering van de Indexeer functie bepaalt Azure Cognitive Search de beste omgeving waarin de Indexeer functie wordt uitgevoerd. Als u een IP-firewall gebruikt om de toegang tot Azure-resources te beheren, kunt u op de hoogte zijn van de uitvoerings omgevingen om een IP-bereik in te stellen dat van beide is inbegrepen.

## <a name="granting-access-to-indexer-ip-ranges"></a>Toegang verlenen tot de IP-bereiken van de Indexeer functie

Als de bron waartoe uw Indexeer functie probeert toegang wordt beperkt tot slechts een bepaalde set IP-bereiken, moet u de set uitbreiden om de mogelijke IP-bereiken op te nemen waaruit een Indexeer functie-aanvraag kan afkomstig zijn. Zoals hierboven vermeld, zijn er twee mogelijke omgevingen waarin Indexeer functies worden uitgevoerd en van waaruit de toegangs aanvragen afkomstig zijn. U moet de IP-adressen van **beide** omgevingen toevoegen zodat de toegang tot de Indexeer functie werkt.

- Om het IP-adres van de zoek service te verkrijgen, `nslookup` (of `ping` ) de Fully QUALIFIED domain name (FQDN) van uw zoek service. Zo zou de FQDN van een zoek service in de open bare Cloud zijn `<service-name>.search.windows.net` . Deze informatie is beschikbaar op de Azure Portal.
- De IP-adressen van de omgevingen met meerdere tenants zijn beschikbaar via de `AzureCognitiveSearch` service-tag. [Azure-service Tags](../virtual-network/service-tags-overview.md) hebben een gepubliceerd bereik van IP-adressen voor elke service: dit is beschikbaar via een [detectie-API (preview)](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) of een [downloadbaar JSON-bestand](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files). In beide gevallen worden IP-bereiken onderverdeeld per regio: u kunt alleen de IP-bereiken kiezen die zijn toegewezen voor de regio waarin uw zoek service is ingericht.

Voor bepaalde gegevens bronnen kan de servicetag zelf rechtstreeks worden gebruikt in plaats van het opsommen van de lijst met IP-bereiken (het IP-adres van de zoek service moet nog steeds expliciet worden gebruikt). Deze gegevens bronnen beperken de toegang door middel van het instellen van een regel voor een [netwerk beveiligings groep](../virtual-network/network-security-groups-overview.md), die systeem eigen ondersteuning biedt voor het toevoegen van een service-tag, in tegens telling tot IP-regels zoals die worden aangeboden door Azure Storage, Cosmos DB, Azure SQL, enzovoort. De gegevens bronnen die de mogelijkheid bieden om de servicetag `AzureCognitiveSearch` direct naast het IP-adres van de zoek service te gebruiken, zijn:

- [SQL Server op virtuele machines van Azure](./search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md#restrict-access-to-the-azure-cognitive-search)

- [Beheerde SQL-exemplaren](./search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md#verify-nsg-rules)

Zie [indexerings verbindingen via een IP-firewall](search-indexer-howto-access-ip-restricted.md)voor meer informatie over deze connectiviteits optie.

## <a name="granting-access-via-private-endpoints"></a>Toegang verlenen via persoonlijke eind punten

Indexeer functies kunnen privé- [eind punten](../private-link/private-endpoint-overview.md) gebruiken om toegang te krijgen tot resources, de toegang die is vergrendeld om virtuele netwerken te selecteren of waarvoor geen open bare toegang is ingeschakeld.
Deze functionaliteit is alleen beschikbaar in factureer bare Zoek Services, met limieten voor het aantal privé-eind punten dat wordt gemaakt. Zie [service limieten](search-limits-quotas-capacity.md#shared-private-link-resource-limits)voor meer informatie.

### <a name="step-1-create-a-private-endpoint-to-the-secure-resource"></a>Stap 1: een persoonlijk eind punt maken voor de beveiligde resource

Klanten moeten de bewerking Zoek beheer, de [CreateOrUpdate-API](/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate) op een **gedeelde persoonlijke koppelings bron**, aanroepen om een privé-eindpunt verbinding te maken met hun beveiligde bron (bijvoorbeeld een opslag account). Verkeer dat overgaat op deze (uitgaand) particuliere endpoint-verbinding, is alleen afkomstig van het virtuele netwerk in de zoek service specifieke ' persoonlijke ' uitvoerings omgeving.

Azure Cognitive Search valideert dat aanroepers van deze API Azure RBAC-machtigingen hebben voor het goed keuren van verbindings aanvragen van particuliere eind punten naar de beveiligde bron. Als u bijvoorbeeld een verbinding voor een persoonlijk eind punt met een opslag account met alleen-lezen machtigingen aanvraagt, wordt deze aanroep geweigerd.

### <a name="step-2-approve-the-private-endpoint-connection"></a>Stap 2: de verbinding met een privé-eind punt goed keuren

Wanneer de (asynchrone) bewerking die een gedeelde persoonlijke koppelings bron maakt, is voltooid, wordt een verbinding voor een persoonlijk eind punt gemaakt met de status in behandeling. Er is nog geen verkeer dat via de verbinding loopt.
De klant wordt vervolgens naar verwachting deze aanvraag te vinden op hun beveiligde resource en ' goed keuren '. Normaal gesp roken kunt u dit doen via de Azure Portal of via de [rest API](/rest/api/virtualnetwork/privatelinkservices/updateprivateendpointconnection).

### <a name="step-3-force-indexers-to-run-in-the-private-environment"></a>Stap 3: Indexeer functies kunnen worden uitgevoerd in de ' persoonlijke ' omgeving

Een goedgekeurd privé-eind punt staat uitgaande aanroepen van de zoek service toe aan een resource die een bepaalde vorm van toegangs beperkingen op netwerk niveau heeft (bijvoorbeeld een gegevens bron van het opslag account die alleen is geconfigureerd voor toegang tot bepaalde virtuele netwerken).
Dit betekent dat elke Indexeer functie die kan worden bereikt met een dergelijke gegevens bron via het persoonlijke eind punt, slaagt.
Als het persoonlijke eind punt niet is goedgekeurd of als de Indexeer functie de verbinding met het persoonlijke eind punt niet gebruikt, wordt de Indexeer functie uiteindelijk uitgevoerd in `transientFailure` .

Als u Indexeer functies wilt inschakelen voor toegang tot resources via verbindingen met een privé-eind punt, moet u de `executionEnvironment` Indexeer functie instellen op `"Private"` om ervoor te zorgen dat alle Indexeer functie-uitvoeringen het persoonlijke eind punt kunnen gebruiken. Dit komt doordat persoonlijke eind punten worden ingericht binnen de persoonlijke zoek service-specifieke omgeving.

```json
    {
      "name" : "myindexer",
      ... other indexer properties
      "parameters" : {
          ... other parameters
          "configuration" : {
            ... other configuration properties
            "executionEnvironment": "Private"
          }
        }
    }
```

Deze stappen worden uitgebreid beschreven in [indexerings verbindingen via een persoonlijk eind punt](search-indexer-howto-access-private.md).
Zodra u een goedgekeurd privé-eind punt aan een resource hebt, hebben Indexeer functies die zijn ingesteld op *persoonlijke* pogingen om toegang te verkrijgen via de verbinding van het particuliere eind punt.

### <a name="limits"></a>Limieten

Om te zorgen voor optimale prestaties en stabiliteit van de zoek service, worden er beperkingen opgelegd (door de zoek service-laag) op de volgende dimensies:

- De soorten Indexeer functies die kunnen worden ingesteld als *persoonlijk*.
- Het aantal gedeelde persoonlijke koppelings resources dat kan worden gemaakt.
- Het aantal afzonderlijke resource typen waarvoor gedeelde persoonlijke koppelings resources kunnen worden gemaakt.

Deze limieten worden beschreven in [service limieten](search-limits-quotas-capacity.md).

## <a name="next-steps"></a>Volgende stappen

- [Indexeer verbindingen via IP-firewalls](search-indexer-howto-access-ip-restricted.md)
- [Indexeer verbindingen met behulp van de uitzonde ring voor vertrouwde services](search-indexer-howto-access-trusted-service-exception.md)
- [Indexeer verbindingen met een persoonlijk eind punt](search-indexer-howto-access-private.md)