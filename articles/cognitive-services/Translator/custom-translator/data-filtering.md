---
title: Gegevens filtering-aangepaste vertaler
titleSuffix: Azure Cognitive Services
description: Wanneer u documenten indient die moeten worden gebruikt voor het trainen van een aangepast systeem, worden de documenten een reeks verwerkings-en filter stappen voor het voorbereiden op training.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 53dea20e356f735a521dec8c22edf8cb2aa7122d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "98895863"
---
# <a name="data-filtering"></a>Gegevens filteren

Wanneer u documenten indient die moeten worden gebruikt voor het trainen van een aangepast systeem, worden de documenten een reeks verwerkings-en filter stappen voor het voorbereiden op training. Deze stappen worden hier beschreven. De kennis van het filteren kan u helpen inzicht te krijgen in het aantal zinnen dat wordt weer gegeven in aangepaste Vertaler en de stappen die u kunt nemen om de documenten voor te bereiden voor de training met aangepaste vertaler.

## <a name="sentence-alignment"></a>Uitlijning van zinnen
Als uw document zich niet in een XLIFF-, TMX-of ALIGN-vorm bevindt, worden de zinnen van de bron-en doel documenten door aangepaste vertalers op zin uitgelijnd. Het aangepaste conversie programma voert geen document uitlijning uit. het volgt de naam van de documenten om het overeenkomende document van de andere taal te vinden. In het document probeert aangepaste vertaler de overeenkomstige zin in de andere taal te vinden. Het maakt gebruik van document aantekeningen zoals Inge sloten HTML-tags om u te helpen bij het uitlijnen.  

Als u een grote discrepantie ziet tussen het aantal zinnen in de bron-en doel documenten, is het mogelijk dat uw document niet parallel is in de eerste plaats of om andere redenen niet kan worden uitgelijnd. De document paren met een groot verschil (>10%) van de zinnen aan elke zijde geven een tweede blik om er zeker van te zijn dat ze inderdaad parallel zijn. Er wordt een waarschuwing weer gegeven naast het document als het aantal zinnen verdacht is.  


## <a name="deduplication"></a>Ontdubbeling
Aangepaste Translator verwijdert de zinnen die aanwezig zijn bij het testen en het afstemmen van documenten uit de trainings gegevens.Het verwijderen vindt dynamisch plaats in de uitvoering van de training, niet in de stap voor gegevens verwerking. Aangepaste Translator rapporteert het aantal zinnen aan u in het project overzicht voordat deze worden verwijderd.  

## <a name="length-filter"></a>Filter lengte
* Zinnen met slechts één woord aan beide zijden verwijderen.
* Verwijder zinnen met meer dan 100 woorden aan beide zijden.Chinees, Japans, Koreaans, zijn uitgezonderd.
* Zinnen met minder dan drie tekens verwijderen. Chinees, Japans, Koreaans, zijn uitgezonderd.
* Verwijder zinnen met meer dan 2000 tekens voor Chinees, Japans, Koreaans.
* Verwijder zinnen met minder dan 1% alfanumerieke tekens.
* Verwijder woordenlijst vermeldingen die meer dan 50 woorden bevatten.

## <a name="white-space"></a>Witruimte
* Vervang een wille keurige reeks witruimte tekens, inclusief tabbladen en CR/LF-reeksen met één spatie.
* Voor loop-of volg spaties verwijderen uit de zin

## <a name="sentence-end-punctuation"></a>Interpunctie van zin
Meerdere interpunctie tekens voor zinnen vervangen door één exemplaar.  

## <a name="japanese-character-normalization"></a>Japanse teken normalisatie
Letters en cijfers met volledige breedte converteren naar tekens met halve breedte.

## <a name="unescaped-xml-tags"></a>XML-tags zonder escape-teken
Met filters worden niet-verescapede Tags getransformeerd in Tags met escape-teken:
* `&lt;` steeds `&amp;lt;`
* `&gt;` steeds `&amp;gt;`
* `&amp;` steeds `&amp;amp;`

## <a name="invalid-characters"></a>Ongeldige tekens
Aangepaste Translator verwijdert zinnen die Unicode-teken U en FFFD bevatten. Het teken U + FFFD geeft aan dat de conversie is mislukt.

## <a name="next-steps"></a>Volgende stappen

- [Train een model](how-to-train-model.md) in Custom Translator.
