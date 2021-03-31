---
title: Veelgestelde vragen
titleSuffix: Azure Cognitive Search
description: Krijg antwoorden op veelgestelde vragen over Microsoft Azure Cognitive Search-service, een in de Cloud gehoste zoek service op Microsoft Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: 9d6acdcf9487b2d1a5964d4ec686cd23666275b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88923089"
---
# <a name="azure-cognitive-search---frequently-asked-questions-faq"></a>Veelgestelde vragen over Azure Cognitive Search

 Hier vindt u antwoorden op veelgestelde vragen over concepten, code en scenario's met betrekking tot Azure Cognitive Search.

## <a name="platform"></a>Platform

### <a name="how-is-azure-cognitive-search-different-from-full-text-search-in-my-dbms"></a>Hoe verschilt Azure Cognitive Search van zoeken in volledige tekst in mijn DBMS?

Azure Cognitive Search ondersteunt meerdere gegevens bronnen, een [taal kundige analyse voor veel talen](/rest/api/searchservice/language-support), [aangepaste analyse voor interessante en ongebruikelijke gegevens invoer](/rest/api/searchservice/custom-analyzers-in-azure-search), zoek classificatie besturings elementen door middel van [Score profielen](/rest/api/searchservice/add-scoring-profiles-to-a-search-index)en gebruikers ervaring-functies zoals typeahead, markeren en facet navigatie. Het bevat ook andere functies, zoals synoniemen en uitgebreide query syntaxis, maar dit zijn doorgaans geen onderscheid tussen functies.

### <a name="can-i-pause-azure-cognitive-search-service-and-stop-billing"></a>Kan ik de Azure Cognitive Search-service onderbreken en de facturering stoppen?

U kunt de service niet onderbreken. Reken-en opslag resources worden toegewezen voor exclusief gebruik wanneer de service wordt gemaakt. Het is niet mogelijk om deze resources op aanvraag vrij te geven en te claimen.

## <a name="indexing-operations"></a>Indexerings bewerkingen

### <a name="move-backup-and-restore-indexes-or-index-snapshots"></a>Wilt u indexen of index momentopnamen verplaatsen, back-ups maken en herstellen?

Tijdens de ontwikkelings fase wilt u mogelijk de index verplaatsen tussen de zoek services. U kunt bijvoorbeeld een basis-of gratis prijs categorie gebruiken om uw index te ontwikkelen en deze vervolgens te verplaatsen naar de Standard-of hogere laag voor productie gebruik. 

U kunt ook een back-up maken van een index momentopname naar bestanden die kunnen worden gebruikt om deze later te herstellen. 

U kunt al deze dingen doen met behulp van de voorbeeld code **index-Backup-Restore** in deze [Azure Cognitive Search .net-voorbeeld opslag plaats](https://github.com/Azure-Samples/azure-search-dotnet-samples). 

U kunt ook op elk gewenst moment [een index definitie ophalen](/rest/api/searchservice/get-index) met behulp van de REST API Azure Cognitive Search.

Er is momenteel geen ingebouwde index voor het uitpakken, maken van een moment opname of het herstellen van back-ups in de Azure Portal. We gaan echter overwegen de functionaliteit voor back-up en herstel toe te voegen in een toekomstige release. Als u de ondersteuning voor deze functie wilt weer geven, moet u een stem op de [gebruikers stem](https://feedback.azure.com/forums/263029-azure-search/suggestions/8021610-backup-snapshot-of-index)casten.

### <a name="can-i-restore-my-index-or-service-once-it-is-deleted"></a>Kan ik mijn index of service herstellen nadat deze is verwijderd?

Nee, als u een Azure Cognitive Search-index of-service verwijdert, kan deze niet worden hersteld. Wanneer u een Azure Cognitive Search-service verwijdert, worden alle indexen in de service permanent verwijderd. Als u een Azure-resource groep verwijdert die een of meer Azure Cognitive Search Services bevat, worden alle services permanent verwijderd.  

Als u resources zoals indexen, Indexeer functies, gegevens bronnen en vaardig heden opnieuw wilt maken, moet u ze opnieuw maken op basis van code. 

Als u een index opnieuw wilt maken, moet u de gegevens van externe bronnen opnieuw indexeren. Daarom is het raadzaam om een hoofd kopie of een back-up van de oorspronkelijke gegevens in een ander gegevens archief, zoals Azure SQL Database of Cosmos DB, te bewaren.

Als alternatief kunt u de voorbeeld code **index-Backup-Restore** in deze [Azure Cognitive Search .net](https://github.com/Azure-Samples/azure-search-dotnet-samples) -voor beeld-opslag plaats gebruiken om een back-up te maken van een index definitie en index MOMENTOPNAME naar een reeks json-bestanden. Later kunt u het hulp programma en de bestanden gebruiken om de index, indien nodig, te herstellen.  

### <a name="can-i-index-from-sql-database-replicas-applies-to-azure-sql-database-indexers"></a>Kan ik indexeren vanuit SQL Database replica's (van toepassing op [Azure SQL database Indexeer functies](./search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md))

Er zijn geen beperkingen voor het gebruik van primaire of secundaire replica's als gegevens bron wanneer u een volledig nieuwe index bouwt. Voor het vernieuwen van een index met incrementele updates (op basis van gewijzigde records) is echter de primaire replica vereist. Deze vereiste is van SQL Database, die alleen het bijhouden van wijzigingen op primaire replica's waarborgt. Als u secundaire replica's probeert te gebruiken voor een werk belasting voor het vernieuwen van de index, is er geen garantie dat u alle gegevens kunt ophalen.

## <a name="search-operations"></a>Zoek bewerkingen

### <a name="can-i-search-across-multiple-indexes"></a>Kan ik zoeken in meerdere indexen?

Nee, deze bewerking wordt niet ondersteund. Het bereik van de zoek opdracht is altijd hetzelfde als een enkele index.

### <a name="can-i-restrict-search-index-access-by-user-identity"></a>Kan ik de toegang tot de zoek index beperken door de identiteit van de gebruiker?

U kunt [beveiligings filters](./search-security-trimming-for-azure-search.md) met behulp van `search.in()` filter implementeren. Het filter is goed samen met [identiteits beheer Services als Azure Active Directory (Aad)](./search-security-trimming-for-azure-search-with-aad.md) om Zoek resultaten te beperken op basis van gedefinieerd lidmaatschap van de gebruikers groep.

### <a name="why-are-there-zero-matches-on-terms-i-know-to-be-valid"></a>Waarom zijn er geen overeenkomsten met termen waarvan ik weet dat ze geldig zijn?

Het meest voorkomende geval is niet weten dat elk query type verschillende Zoek gedragingen en niveau taal kundige analyses ondersteunt. Zoeken in volledige tekst, wat de meest voorkomende werk belasting is, omvat een taal analyse fase die de voor waarden in hoofd formulieren opsplitsen. Met dit aspect van het parseren van query's wordt een breder net over mogelijke overeenkomsten gecast, omdat de token-term overeenkomt met een groter aantal varianten.

Joker tekens, fuzzy-en regex-query's worden echter niet geanalyseerd zoals normale term of woordgroepen query's en kunnen leiden tot slecht terughalen als de query niet overeenkomt met de geanalyseerde vorm van het woord in de zoek index. Zie [query architectuur](./search-lucene-query-architecture.md)voor meer informatie over het parseren en analyseren van query's.

### <a name="my-wildcard-searches-are-slow"></a>Mijn Zoek opdrachten met Joker tekens zijn langzaam.

De meeste zoek query's met Joker tekens, zoals voor voegsel, fuzzy en regex, worden intern herschreven met overeenkomende voor waarden in de zoek index. Deze extra verwerking van het scannen van de zoek index wordt toegevoegd aan de latentie. Uitgebreide zoek query's, zoals `a*` bijvoorbeeld, die waarschijnlijk worden herschreven, kunnen erg traag zijn. Voor het uitvoeren van zoek opdrachten met Joker tekens kunt u een [aangepaste analyse functie](/rest/api/searchservice/custom-analyzers-in-azure-search)definiëren.

### <a name="why-is-the-search-rank-a-constant-or-equal-score-of-10-for-every-hit"></a>Waarom rangschikt de zoek opdracht een constante of gelijke Score van 1,0 voor elke treffer?

Standaard worden Zoek resultaten gescoord op basis van de [statistische eigenschappen van overeenkomende voor waarden](search-lucene-query-architecture.md#stage-4-scoring)en in de resultatenset van hoog naar laag geordend. Sommige query typen (joker tekens, voor voegsel, regex) bijdragen altijd een constante score naar de algehele document Score. Dit gedrag is standaard. Azure Cognitive Search maakt een constante Score om overeenkomsten te toestaan die zijn gevonden via query expansie om te worden opgenomen in de resultaten, zonder dat dit van invloed is op de rang schikking.

Stel bijvoorbeeld dat een invoer van ' Tour * ' in een zoek opdracht met Joker tekens resulteert in ' rond leidingen ', ' Tourettes ' en ' Tourmaline '. Gezien de aard van deze resultaten is er geen enkele manier om redelijkerwijs te afleiden welke termen waardevoler zijn dan andere. Daarom negeren we de term frequenties wanneer de Score resulteert in query's van het type Joker teken, voor voegsel en regex. Zoek resultaten op basis van een gedeeltelijke invoer krijgen een constante Score om bias te voor komen op mogelijke onverwachte overeenkomsten.

## <a name="skillset-operations"></a>Vaardig heden-bewerkingen

### <a name="are-there-any-tips-or-tricks-to-reduce-cognitive-services-charges-on-ingestion"></a>Zijn er tips of trucs voor het verlagen van de kosten voor cognitieve Services bij opname?

Het is begrijpelijk dat u geen ingebouwde vaardig heden of aangepaste vaardig heden meer hoeft uit te voeren dan absoluut nood zakelijk is, met name als u met miljoenen documenten te maken hebt. Daarbij hebben we ' incrementele verrijking ' mogelijkheden toegevoegd aan de vaardig heden-uitvoering. In essentie kunt u een cache locatie (een Blob Storage connection string) opgeven die wordt gebruikt voor het opslaan van de uitvoer van ' tussenliggende ' verrijkings stappen.  Zo kan de verrijkings pijplijn slim worden en worden alleen verrijkingen toegepast die nodig zijn wanneer u uw vaardig heden wijzigt. Dit zal natuurlijk ook de indexerings tijd opslaan, omdat de pijp lijn efficiënter werkt.

Meer informatie over [incrementele verrijking](cognitive-search-incremental-indexing-conceptual.md)

## <a name="design-patterns"></a>Ontwerppatronen

### <a name="what-is-the-best-approach-for-implementing-localized-search"></a>Wat is de beste benadering voor het implementeren van gelokaliseerde zoek acties?

De meeste klanten kiezen voor een verzameling specifieke velden wanneer het gaat om verschillende land instellingen (talen) in dezelfde index te ondersteunen. Met taalspecifieke velden kunt u een geschikte analyse toewijzen. U kunt bijvoorbeeld de micro soft-Franse analyse toewijzen aan een veld met Franse teken reeksen. Het vereenvoudigt het filteren. Als u weet dat er een query op een fr-FR pagina wordt gestart, kunt u de zoek resultaten beperken tot dit veld. U kunt ook een [Score profiel](/rest/api/searchservice/add-scoring-profiles-to-a-search-index) maken om het veld een groter relatief gewicht te geven. Azure Cognitive Search ondersteunt meer dan [50 taal analyse](./search-language-support.md) functies om uit te kiezen.

## <a name="next-steps"></a>Volgende stappen

Is uw vraag over een ontbrekend onderdeel of functionaliteit? Vraag de functie aan op de website van de [gebruikers spraak](https://feedback.azure.com/forums/263029-azure-search).

## <a name="see-also"></a>Zie ook

 [Stack overflow: Azure Cognitive Search](https://stackoverflow.com/questions/tagged/azure-search)   
 [Hoe zoeken in de volledige tekst werkt in Azure Cognitive Search](search-lucene-query-architecture.md)  
 [Wat is Azure Cognitive Search?](search-what-is-azure-search.md)