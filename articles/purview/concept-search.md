---
title: Zoek functies in azure controle sfeer liggen (preview-versie) begrijpen
description: In dit artikel wordt uitgelegd hoe u met Azure controle sfeer liggen gegevens detectie kunt herkennen via zoek functies.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: af8ec9e0aac38240c7da92edd614892ff65712e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96552697"
---
# <a name="understand-search-features-in-azure-purview"></a>Zoek functies in azure controle sfeer liggen begrijpen

Dit artikel bevat een overzicht van de zoek ervaring in azure controle sfeer liggen. Search is een kern platform mogelijkheid van controle sfeer liggen, die de ervaring voor gegevens detectie en gegevens gebruik in een organisatie toestuurt.

## <a name="search"></a>Zoeken

De controle sfeer liggen-Zoek ervaring wordt mogelijk gemaakt door een beheerde zoek index. Nadat een gegevens bron is geregistreerd met controle sfeer liggen, worden de meta gegevens ervan geïndexeerd door de zoek service om eenvoudige detectie mogelijk te maken. De index biedt relevantie mogelijkheden voor zoeken en voltooit Zoek opdrachten door een query uit te voeren op miljoenen meta gegevensassets. Zoeken helpt u bij het detecteren, begrijpen en gebruiken van de gegevens om de meest waardevolle waarde te verkrijgen.

De zoek ervaring in controle sfeer liggen is een proces dat uit drie fasen bestaat:

1. Het zoekvak bevat de geschiedenis met recent gebruikte tref woorden en assets.
1. Wanneer u begint met het typen van de toetsaanslagen, worden de overeenkomende tref woorden en assets door de zoek opdracht voorgesteld. 
1. De zoek resultaten pagina wordt weer gegeven met de activa die overeenkomen met het opgegeven tref woord.

## <a name="reduce-the-time-to-discover-data"></a>De tijd beperken om gegevens te detecteren

Gegevens detectie is de eerste stap voor een gegevens analyse of Data Governance-werk belasting voor gegevens gebruikers. Gegevens detectie kan tijdrovend zijn, omdat u mogelijk niet weet waar u de gewenste gegevens kunt vinden. Zelfs nadat u de gegevens hebt gevonden, weet u misschien zeker of u de gegevens kunt vertrouwen en er afhankelijkheden voor moet maken. 

Het doel van zoeken in azure controle sfeer liggen is het proces van gegevens detectie te versnellen door bewegingen te bieden, zodat u snel de gegevens kunt vinden die van belang zijn.

## <a name="recent-search-and-suggestions"></a>Recente Zoek opdrachten en suggesties

Vaak kunt u tegelijkertijd aan meerdere projecten werken. Om het gemakkelijker te maken om vorige projecten te hervatten, biedt controle sfeer liggen Search de mogelijkheid om recente Zoek woorden en suggesties te bekijken. U kunt ook de recente Zoek geschiedenis beheren door **alles weer geven** te selecteren in de vervolg keuzelijst zoeken.

## <a name="filters"></a>Filters

Filters (ook wel *facetten* genoemd) zijn ontworpen om een aanvulling op de zoek actie te maken. Filters worden weer gegeven op de pagina met zoek resultaten. De filters op de pagina Zoek resultaten bevatten classificatie, gevoeligheids label, gegevens bron en eigen aren. Gebruikers kunnen specifieke waarden in een filter selecteren om alleen overeenkomende gegevensassets te bekijken en het Zoek resultaat te beperken tot de overeenkomende activa.

## <a name="hit-highlighting"></a>Markeren

Overeenkomende tref woorden in de zoek resultaten pagina zijn gemarkeerd om het gemakkelijker te maken om te identificeren waarom een gegevensasset door de zoek opdracht is geretourneerd. Het sleutel woord matching kan voor komen in meerdere velden, zoals de naam, beschrijving en eigenaar van het gegevens element.

Het kan echter niet duidelijk zijn waarom een gegevensasset is opgenomen in de zoek opdracht, zelfs wanneer het markeren van treffers is ingeschakeld. Standaard worden alle eigenschappen doorzocht. Daarom kan een gegevensasset worden geretourneerd vanwege een overeenkomst met een eigenschap op kolom niveau. En omdat meerdere gebruikers aantekeningen kunnen maken op de gegevensassets met hun eigen classificaties en beschrijvingen, worden niet alle meta gegevens weer gegeven in de lijst met zoek resultaten.

## <a name="sort"></a>Sorteren

Gebruikers hebben twee opties om de zoek resultaten te sorteren. Ze kunnen sorteren op de naam van het activum of op basis van de relevantie van de zoek opdracht.

## <a name="search-relevance"></a>Relevantie zoeken

Relevantie is de standaard sorteer volgorde in de zoek resultaten pagina. De relevantie van zoeken zoekt documenten met het zoek woord (sommige of alle). Activa die veel exemplaren van het zoek sleutelwoord bevatten, worden hoger gerangschikt. Daarnaast worden aangepaste Score mechanismen voortdurend afgestemd om de relevantie van de zoek actie te verbeteren.

## <a name="next-steps"></a>Volgende stappen

* [Quickstart: Een Azure Purview-account maken in Azure Portal](create-catalog-portal.md)
* [Quickstart: Een Azure Purview-account maken met behulp van Azure PowerShell/Azure CLI](create-catalog-powershell.md)
* [Snelstartgids: controle sfeer liggen Studio gebruiken](use-purview-studio.md)
