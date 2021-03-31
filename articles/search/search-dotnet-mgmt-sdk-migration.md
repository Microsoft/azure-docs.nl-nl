---
title: Upgrade uitvoeren naar de Azure Search .NET Management SDK
titleSuffix: Azure Cognitive Search
description: Voer een upgrade uit naar de Azure Search .NET Management SDK van eerdere versies. Meer informatie over nieuwe functies en de code wijzigingen die nodig zijn voor de migratie.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 8648347eb48081389cf360fa949b31bbd0b8c71e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88936704"
---
# <a name="upgrading-versions-of-the-azure-search-net-management-sdk"></a>Versies van de Azure Search .NET Management SDK bijwerken

In dit artikel wordt uitgelegd hoe u kunt migreren naar opeenvolgende versies van de Azure Search .NET Management SDK, die wordt gebruikt om Zoek Services in te richten of te verwijderen, de capaciteit aan te passen en de API-sleutels te beheren.

Management-Sdk's richten zich op een specifieke versie van de beheer REST API. Zie [Zoek beheer (rest)](/rest/api/searchmanagement/)voor meer informatie over concepten en bewerkingen.

## <a name="versions"></a>Versies

| SDK-versie | Overeenkomende REST API versie | Wijziging van de functie of het gedrag |
|-------------|--------------------------------|-------------------------------------|
| [3,0](https://www.nuget.org/packages/Microsoft.Azure.Management.Search/3.0.0) | API-Version = 2020-30-20 | Endpoint Security toevoegen (IP-firewalls en integratie met [persoonlijke Azure-koppeling](../private-link/private-endpoint-overview.md)) |
| [2,0](https://www.nuget.org/packages/Microsoft.Azure.Management.Search/2.0.0) | API-Version = 2019-10-01 | Verbeteringen in het gebruik. De wijziging van de [lijst query sleutels](/rest/api/searchmanagement/querykeys/listbysearchservice) opsplitsen (Get wordt stopgezet). |
| [1,0](https://www.nuget.org/packages/Microsoft.Azure.Management.Search/1.0.1) | API-Version = 2015-08-19  | Eerste versie |

## <a name="how-to-upgrade"></a>Upgrade uitvoeren

1. Werk uw NuGet-verwijzing bij voor `Microsoft.Azure.Management.Search` het gebruik van de NuGet Package Manager-console of klik met de rechter muisknop op uw project verwijzingen en selecteer vervolgens NuGet-pakketten beheren... in Visual Studio.

1. Nadat NuGet de nieuwe pakketten en hun afhankelijkheden heeft gedownload, bouwt u het project opnieuw op. Afhankelijk van hoe uw code is gestructureerd, kan deze opnieuw worden opgebouwd, in welk geval u dat hebt gedaan.

1. Als uw build mislukt, is het mogelijk dat u een aantal van de SDK-interfaces (bijvoorbeeld voor het testen van eenheden) hebt geïmplementeerd, die zijn gewijzigd. Om dit probleem op te lossen, moet u nieuwere methoden implementeren, zoals `BeginCreateOrUpdateWithHttpMessagesAsync` .

1. Nadat u opgebouwde fouten hebt opgelost, kunt u wijzigingen aanbrengen in uw toepassing om te profiteren van de nieuwe functionaliteit. 

## <a name="upgrade-to-30"></a>Voer een upgrade uit naar 3,0

Versie 3,0 voegt persoonlijke Endpoint Protection toe door de toegang tot IP-bereiken te beperken en door desgewenst te integreren met een persoonlijke Azure-koppeling voor zoek services die niet zichtbaar moeten zijn op het open bare Internet.

### <a name="new-apis"></a>Nieuwe Api's

| API | Categorie| Details |
|-----|--------|------------------|
| [NetworkRuleSet](/rest/api/searchmanagement/services/createorupdate#networkruleset) | IP-firewall | Beperk de toegang tot een service-eind punt tot een lijst met toegestane IP-adressen. Zie [Configure IP firewall](service-configure-firewall.md) for concepten and portal instructies (Engelstalig). |
| [Gedeelde persoonlijke koppelings resource](/rest/api/searchmanagement/sharedprivatelinkresources) | Private Link | Maak een gedeelde persoonlijke koppelings resource die moet worden gebruikt door een zoek service.  |
| [Verbindingen met privé-eind punten](/rest/api/searchmanagement/privateendpointconnections) | Private Link | Verbindingen tot stand brengen en beheren met een zoek service via een persoonlijk eind punt. Zie [een persoonlijk eind punt maken](service-create-private-endpoint.md) voor concepten en Portal-instructies.|
| [Persoonlijke koppelings resources](/rest/api/searchmanagement/privatelinkresources/) | Private Link | Voor een zoek service met een particuliere eindpunt verbinding krijgt u een lijst met alle services die in hetzelfde virtuele netwerk worden gebruikt. Als uw zoek oplossing Indexeer functies bevat die worden opgehaald uit Azure-gegevens bronnen (Azure Storage, Cosmos DB, Azure SQL) of gebruikt Cognitive Services of Key Vault, moeten al deze resources eind punten in het virtuele netwerk hebben en moet deze API een lijst retour neren. |
| [PublicNetworkAccess](/rest/api/searchmanagement/services/createorupdate#publicnetworkaccess)| Private Link | Dit is een eigenschap voor het maken of bijwerken van service aanvragen. Als deze is uitgeschakeld, is persoonlijke koppeling de enige modale toegang. |

### <a name="breaking-changes"></a>Wijzigingen die fouten veroorzaken

U kunt de aanvraag voor het ophalen van een [lijst met zoek sleutels](/rest/api/searchmanagement/querykeys/listbysearchservice) niet meer gebruiken. In eerdere releases kunt u in deze versie GET of POST, in deze release en in alle releases die vooruit verplaatsen, alleen POST worden ondersteund. 

## <a name="upgrade-to-20"></a>Voer een upgrade uit naar 2,0

Versie 2 van de Azure Search .NET Management SDK is een kleine upgrade, zodat het wijzigen van uw code slechts minimale inspanning vereist. De wijzigingen in de SDK zijn strikt aan de client zijde gewijzigd om de bruikbaarheid van de SDK zelf te verbeteren. Deze veranderingen houden onder meer het volgende in:

* `Services.CreateOrUpdate` en de bijbehorende asynchrone versies worden nu automatisch door gegeven aan de inrichting `SearchService` en worden niet teruggestuurd totdat het inrichten van de service is voltooid. Zo hoeft u niet zelf een dergelijke polling code te schrijven.

* Als u nog steeds hand matig Service Provisioning wilt pollen, kunt u de nieuwe `Services.BeginCreateOrUpdate` methode of een van de bijbehorende asynchrone versies gebruiken.

* Nieuwe methoden `Services.Update` en de bijbehorende asynchrone versies zijn toegevoegd aan de SDK. Deze methoden gebruiken HTTP-PATCH ter ondersteuning van incrementele update van een service. Zo kunt u een service nu schalen door een `SearchService` exemplaar door te geven aan deze methoden die alleen de gewenste `partitionCount` Eigenschappen en bevatten `replicaCount` . De oude manier van aanroepen `Services.Get` , wijzigen van de geretourneerde `SearchService` en door geven aan `Services.CreateOrUpdate` wordt nog steeds ondersteund, maar is niet langer nodig. 

## <a name="next-steps"></a>Volgende stappen

Als u problemen ondervindt, is het beste forum voor het plaatsen van vragen [stack overflow](https://stackoverflow.com/questions/tagged/azure-cognitive-search?tab=Newest). Als u een bug vindt, kunt u een probleem in de [Azure .NET SDK github-opslag plaats](https://github.com/Azure/azure-sdk-for-net/issues)opslaan. Zorg ervoor dat u de titel van het probleem labelt met ' [Search] '.