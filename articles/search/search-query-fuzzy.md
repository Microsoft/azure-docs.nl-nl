---
title: Fuzzy zoekopdracht
titleSuffix: Azure Cognitive Search
description: Implementeer een ' bedoelde ' Zoek ervaring om automatisch een verkeerd gespelde term of type fout te corrigeren.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: ac2690a5f18bb58c29b433f4a07e52096bbd268b
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701582"
---
# <a name="fuzzy-search-to-correct-misspellings-and-typos"></a>Fuzzy zoeken om spel fouten en typfouten te corrigeren

Azure Cognitive Search ondersteunt fuzzy Search, een type query waarmee type fouten en verkeerd gespelde termen in de invoer teken reeks worden gecompenseerd. Dit wordt gedaan door te scannen op termen met een vergelijk bare samen stelling. Een uitgebreidere zoek opdracht om bijna-overeenkomsten te best rijken is het effect van het automatisch corrigeren van een type fout wanneer het verschil slechts enkele verkeerd geplaatste tekens is. 

## <a name="what-is-fuzzy-search"></a>Wat is fuzzy zoeken?

Het is een uitbrei ding die een overeenkomst produceert met termen die een vergelijk bare samen stelling hebben. Wanneer u een fuzzy zoek opdracht opgeeft, bouwt de engine een grafiek (op basis van een [deterministische, beperkte Automation theorie](https://en.wikipedia.org/wiki/Deterministic_finite_automaton)) van vergelijk bare voor waarden voor alle volledige termen in de query. Als uw query bijvoorbeeld drie termen ' University of Washington ' bevat, wordt er een grafiek gemaakt voor elke term in de query `search=university~ of~ washington~` (er is geen stop-Word-verwijdering in fuzzy Search, dus wordt een grafiek opgehaald).

De grafiek bestaat uit Maxi maal 50 uitbrei dingen of permutaties, van elke term, waarbij zowel juiste als onjuiste varianten in het proces worden vastgelegd. De engine retourneert vervolgens de bovenste relevante overeenkomsten in het antwoord. 

Voor een term als "Universiteit" kan de grafiek "unversty, universty, University, universum, inverse" hebben. Alle documenten die overeenkomen met die in de grafiek, worden opgenomen in de resultaten. In tegens telling tot andere query's voor het analyseren van de tekst voor het afhandelen van verschillende formulieren van hetzelfde woord ("muizen" en "muis"), worden de vergelijkingen in een fuzzy-query gemaakt op basis van de nominale waarde zonder enige taal analyse voor de tekst. "Universum" en "inverse", die semantisch verschillen, zullen overeenkomen omdat de syntaxis van de verschillen klein is.

Een match slaagt als de verschillen beperkt zijn tot twee of minder bewerkingen, waarbij een bewerking een ingevoegd, verwijderd, vervangen of gegetransponeerd teken is. Het teken reeks correctie algoritme waarmee wordt aangegeven dat het verschil is [tussen de Damerau Levenshtein-afstand](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance) , aangeduid als het minimale aantal bewerkingen (invoegingen, verwijderingen, vervangingen of trans acties van twee aangrenzende tekens) dat is vereist voor het wijzigen van één woord in de andere. 

In azure Cognitive Search:

+ Fuzzy query is van toepassing op hele voor waarden, maar u kunt wel zinnen door lopen en bouwen. Zo komt ' Unviersty ~ van ~ ' Wshington ~ ' overeen met ' University of Washington '.

+ De standaard afstand van een bewerking is 2. Een waarde van `~0` geeft aan dat er geen uitbrei ding is (alleen de exacte term wordt beschouwd als een overeenkomst), maar u kunt wel een `~1` mate van verschil of één bewerking opgeven. 

+ Met een fuzzy query kan een term worden uitgebreid tot 50 extra permutaties. Deze limiet kan niet worden geconfigureerd, maar u kunt het aantal uitbrei dingen effectief verminderen door de bewerkings afstand te verlagen tot 1.

+ Antwoorden bestaan uit documenten met een relevante overeenkomst (Maxi maal 50).

De grafieken worden gezamenlijk verzonden als match criteria op basis van tokens in de index. Zo kunt u zich Voorst Ellen dat er minder Zoek resultaten zijn dan andere query formulieren. De grootte en complexiteit van uw index kunnen bepalen of de voor delen voldoende zijn om de latentie van de reactie te verschuiven.

> [!NOTE]
> Omdat fuzzy Search doorgaans langzaam is, is het wellicht de moeite waard om alternatieven zoals n-gram indexering te onderzoeken, met de voortgang van korte teken reeksen (twee en drie teken reeksen voor bigram-en Trigram-tokens). Afhankelijk van uw taal en query-Opper vlak kan n-gram u betere prestaties bieden. De afweging is dat n-gram-indexering zeer veel opslag intensief is en veel grotere indexen genereert.
>
> Een ander alternatief, waarmee u rekening moet houden als u alleen de meest egregiouse cases wilt verwerken, zou een [synoniemen kaart](search-synonyms.md)zijn. Stel bijvoorbeeld ' Search ' in op ' zoeken, serch, sarch ' of ' retrieve ' naar ' ophalen '.

## <a name="indexing-for-fuzzy-search"></a>Indexeren voor fuzzy zoeken

Analyse functies worden niet gebruikt tijdens het verwerken van query's om een uitbreidings grafiek te maken, maar dat betekent dat er geen analysereners moeten worden genegeerd in fuzzy Zoek scenario's. Na alle kunnen analysen worden gebruikt tijdens het indexeren om tokens te maken die overeenkomen met de zoek opdracht, of de query een gratis formulier, een gefilterde zoekpad of een fuzzy zoek opdracht met een grafiek als invoer is. 

Over het algemeen, wanneer u analyse functies per veld toewijst, is de beslissing om de analyse keten af te stemmen, gebaseerd op de primaire use-case (een filter of een zoek opdracht in volledige tekst) in plaats van gespecialiseerde query formulieren zoals fuzzy zoeken. Daarom is er geen specifieke analyse aanbeveling voor fuzzy zoeken. 

Als test query's echter niet de overeenkomen die u verwacht, kunt u de Indexing Analyzer variëren en instellen op een [taal analyse](index-add-language-analyzers.md)om te zien of u betere resultaten krijgt. Sommige talen, met name voor wat betreft de klinker mutaties, kunnen profiteren van de verbuiging en onregelmatige woord formulieren die worden gegenereerd door de micro soft Natural Language-processors. In sommige gevallen kan het gebruik van de juiste taal analyse een verschil maken in de vraag of een term wordt getokend op een manier die compatibel is met de waarde van de gebruiker.

## <a name="how-to-use-fuzzy-search"></a>Fuzzy Search gebruiken

Fuzzy query's worden gemaakt met behulp van de volledige lucene-query syntaxis, waarbij de [lucene-query-parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)wordt aangeroepen.

1. Stel de volledige lucene-parser in voor de query ( `queryType=full` ).

1. U kunt eventueel de aanvraag beperken tot specifieke velden, met behulp van deze para meter ( `searchFields=<field1,field2>` ). 

1. Voeg de tilde ( `~` )-operator aan het einde van de volledige term ( `search=<string>~` ) toe.

   Neem een optionele para meter, een getal tussen 0 en 2 (standaard) op als u de bewerkings afstand ( `~1` ) wilt opgeven. Bijvoorbeeld: ' Blue ~ ' of ' Blue ~ 1 ' retourneert ' Blue ', ' blauw ' en ' lijm '.

In azure Cognitive Search, naast de term en afstand (Maxi maal 2), zijn er geen aanvullende para meters ingesteld voor de query.

> [!NOTE]
> Tijdens de query verwerking worden fuzzy query's geen [lexicale analyse](search-lucene-query-architecture.md#stage-2-lexical-analysis). De query-invoer wordt rechtstreeks aan de query structuur toegevoegd en uitgevouwen om een grafiek met termen te maken. De enige trans formatie die wordt uitgevoerd, is een lagere behuizing.

## <a name="testing-fuzzy-search"></a>Zoek actie op fuzzy testen

Voor eenvoudige tests raden wij u aan [Zoek Verkenner](search-explorer.md) of [postman te plaatsen](search-get-started-rest.md) voor het herhalen van een query-expressie. Beide hulpprogram ma's zijn interactief. Dit betekent dat u snel meerdere varianten van een periode kunt door lopen en de reacties die terugkomen te evalueren.

Als de resultaten dubbel zinnig zijn, kunt u met [treffers markeren](search-pagination-page-layout.md#hit-highlighting) de overeenkomst in het antwoord identificeren. 

> [!Note]
> Het gebruik van treffer markeringen om fuzzy overeenkomsten te identificeren, heeft beperkingen en werkt alleen voor eenvoudige Zoek opdrachten met fuzzy. Als uw index Score profielen heeft, of als u de query laagt met extra syntaxis, kan het zijn dat de overeenkomst niet kan worden gevonden met de functie voor het markeren van treffers. 

### <a name="example-1-fuzzy-search-with-the-exact-term"></a>Voor beeld 1: fuzzy Search met de exacte term

Stel dat de volgende teken reeks bestaat in een `"Description"` veld in een zoek document: `"Test queries with special characters, plus strings for MSFT, SQL and Java."`

Begin met een zoek actie op ' Special ' en voeg treffers markeren toe aan het veld Beschrijving:

```console
search=special~&highlight=Description
```

In het antwoord, omdat u treffer markeringen hebt toegevoegd, wordt de opmaak toegepast op ' Special ' als overeenkomende term.

```output
"@search.highlights": {
    "Description": [
        "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
    ]
```

Probeer de aanvraag opnieuw uit te voeren. u kunt het probleem ' Special ' door meerdere letters te maken (' PE '):

```console
search=scial~&highlight=Description
```

Tot nu toe is geen wijziging in de reactie. Als u de standaard waarde van 2 graden gebruikt, wordt bij het verwijderen van twee tekens ' PE ' van ' Special ' nog steeds een succes volle overeenkomst voor die periode gemaakt.

```output
"@search.highlights": {
    "Description": [
        "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
    ]
```

Probeer een extra aanvraag, wijzig de zoek term verder door een laatste teken te nemen voor een totaal van drie verwijderingen (van ' Special ' tot ' schaal '):

```console
search=scal~&highlight=Description
```

U ziet dat hetzelfde antwoord wordt geretourneerd, maar nu in plaats van te voldoen aan ' speciaal ', is de benadering gelijk aan ' SQL '.

```output
        "@search.score": 0.4232868,
        "@search.highlights": {
            "Description": [
                "Mix of special characters, plus strings for MSFT, <em>SQL</em>, 2019, Linux, Java."
            ]
```

Het punt van dit uitgevouwen voor beeld is om de duidelijkheid te illustreren dat treffers markeren tot onduidelijke resultaten kan leiden. In alle gevallen wordt hetzelfde document geretourneerd. U hebt zich op document-Id's bevonden om een overeenkomst te controleren. u hebt de Shift-toets mogelijk niet van ' speciaal ' naar ' SQL ' gemist.

## <a name="see-also"></a>Zie ook

+ [De manier waarop zoeken in volledige tekst werkt in azure Cognitive Search (architectuur voor het parseren van query's)](search-lucene-query-architecture.md)
+ [Zoek Verkenner](search-explorer.md)
+ [Query's uitvoeren in .NET](./search-get-started-dotnet.md)
+ [Query's uitvoeren in REST](./search-get-started-powershell.md)