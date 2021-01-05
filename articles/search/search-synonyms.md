---
title: Synoniemen voor het uitbreiden van query's via een zoek index
titleSuffix: Azure Cognitive Search
description: Maak een synoniemen toewijzing om het bereik van een zoek query op een Azure Cognitive Search-index uit te breiden. Het bereik is uitgebreid met gelijkwaardige termen die u in een lijst opgeeft.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/18/2020
ms.openlocfilehash: b62621a77f383b5c6413e7c187e7ba3d60beabad
ms.sourcegitcommit: a89a517622a3886b3a44ed42839d41a301c786e0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/22/2020
ms.locfileid: "97732084"
---
# <a name="synonyms-in-azure-cognitive-search"></a>Synoniemen in azure Cognitive Search

Met synoniemen kaarten kunt u gelijkwaardige voor waarden koppelen door het bereik van een query uit te breiden zonder dat de gebruiker de term daad werkelijk hoeft op te geven. Voor beeld: aangenomen dat "hond", "Canine" en "Puppy" synoniemen zijn, komt een query op "Canine" overeen met een document dat "hond" bevat.

## <a name="create-synonyms"></a>Synoniemen maken

Een synoniem toewijzing is een Asset die eenmaal kan worden gemaakt en door veel indexen kan worden gebruikt. De [servicelaag bepaalt hoeveel](search-limits-quotas-capacity.md#synonym-limits) synoniemen er u kunt maken, variërend van 3 synoniemen voor de lagen gratis en Basic, Maxi maal 20 voor de standaard lagen. 

U kunt meerdere synoniemen kaarten maken voor verschillende talen, zoals Engels en Franse versies, of lexicons als uw inhoud technisch of onduidelijke terminologie bevat. Hoewel u meerdere synoniemen kaarten kunt maken, kunt u op dit moment slechts een van de toewijzingen gebruiken.

Een synoniemen kaart bestaat uit een naam, indeling en regels die als synoniemen worden gebruikt voor het toewijzen van vermeldingen. De enige indeling die wordt ondersteund `solr` , is en de `solr` indeling bepaalt de constructie van de regel.

```http
POST /synonymmaps?api-version=2020-06-30
{
    "name": "geo-synonyms",
    "format": "solr",
    "synonyms": "
        USA, United States, United States of America\n
        Washington, Wash., WA => WA\n"
}
```

Als u een synoniemen toewijzing wilt maken, gebruikt u de [map synoniem maken (rest API)](/rest/api/searchservice/create-synonym-map) of een Azure-SDK. Voor C#-ontwikkel aars raden we u aan te beginnen met het [toevoegen van synoniemen in azure cognitieve Zoek opdrachten met C#](search-synonyms-tutorial-sdk.md).

## <a name="define-rules"></a>Regels definiëren

Toewijzings regels voldoen aan de open-source synoniem filter specificatie van Apache solr, zoals beschreven in dit document: [SynonymFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter). De `solr` indeling ondersteunt twee soorten regels:

+ equivalentie (waarbij de voor waarden gelijk zijn aan de vervangingen in de query)

+ expliciete toewijzingen (waarbij voor waarden worden toegewezen aan één expliciete termijn voorafgaand aan het uitvoeren van query's)

Elke regel moet worden gescheiden door het nieuwe regel teken ( `\n` ). U kunt Maxi maal 5.000 regels per synoniem toewijzen in een gratis service en 20.000 regels per kaart in andere lagen. Elke regel kan Maxi maal 20 uitbrei dingen (of items in een regel) bevatten. Zie [synoniemen limieten](search-limits-quotas-capacity.md#synonym-limits)voor meer informatie.

Query-parsers verlaagt alle hoofd letters en andere voor waarden, maar als u speciale tekens in de teken reeks wilt behouden, zoals een komma of streepje, voegt u de juiste escape tekens toe wanneer u de synoniemen kaart maakt. 

### <a name="equivalency-rules"></a>Gelijkwaardige regels

Regels voor equivalente termen worden in dezelfde regel door komma's gescheiden. In het eerste voor beeld wordt een query op `USA` uitgebreid naar `USA` of `"United States"` of `"United States of America"` . Houd er rekening mee dat als u wilt overeenkomen met een woord groep, de query zelf een woordgroepen query met een aanhalings teken moet zijn.

In het geval van de gelijkwaardige aanvraag wordt met een query voor `dog` de query uitgebreid om ook en toe te voegen `puppy` `canine` .

```json
{
"format": "solr",
"synonyms": "
    USA, United States, United States of America\n
    dog, puppy, canine\n
    coffee, latte, cup of joe, java\n"
}
```

### <a name="explicit-mapping"></a>Expliciete toewijzing

Regels voor een expliciete toewijzing worden aangeduid met een pijl `=>` . Als deze is opgegeven, wordt een term volgorde van een zoek query die overeenkomt met de linkerkant van, `=>` vervangen door de alternatieven aan de rechter kant op het moment van de query.

In het expliciete geval wordt een query `Washington` voor `Wash.` of `WA` wordt herschreven als `WA` , en in de query-engine wordt alleen gezocht naar overeenkomsten voor de term `WA` . Expliciete toewijzing is alleen van toepassing in de opgegeven richting en de query wordt `WA` `Washington` in dit geval niet opnieuw geschreven.

```json
{
"format": "solr",
"synonyms": "
    Washington, Wash., WA => WA\n
    California, Calif., CA => CA\n"
}
```

### <a name="escaping-special-characters"></a>Speciale tekens in een escape teken

Als u synoniemen wilt definiëren die komma's of andere speciale tekens bevatten, kunt u ze met een back slash, zoals in dit voor beeld:

```json
{
"format": "solr",
"synonyms": "WA\, USA, WA, Washington\n"
}
```

Omdat de back slash zelf een speciaal teken in andere talen is, zoals JSON en C#, moet u dit waarschijnlijk ook met dubbele Escape. De JSON die wordt verzonden naar de REST API voor de bovenstaande synoniemen kaart ziet er bijvoorbeeld als volgt uit:

```json
{
"format":"solr",
"synonyms": "WA\\, USA, WA, Washington"
}
```

## <a name="upload-and-manage-synonym-maps"></a>Synoniemen overzichten uploaden en beheren

Zoals eerder vermeld, kunt u een synoniemen kaart maken of bijwerken zonder de werk belasting van de query en het indexeren te onderbreken. Een synoniem toewijzing is een zelfstandig object (zoals indexen of gegevens bronnen), en zolang er geen veld wordt gebruikt, worden de indexering of query's niet door updates uitgevoerd. Als u echter een synoniemen toewijzing aan een veld definitie hebt toegevoegd en u vervolgens een synoniemen kaart verwijdert, mislukt alle query's die de betreffende velden bevatten, met een 404-fout.

Het maken, bijwerken en verwijderen van een synoniemen kaart is altijd een hele document bewerking, wat betekent dat u geen incrementele onderdelen van de synoniemen kaart kunt bijwerken of verwijderen. Als u zelfs een regel wilt bijwerken, moet u het opnieuw laden.

## <a name="assign-synonyms-to-fields"></a>Synoniemen toewijzen aan velden

Nadat u een synoniemen kaart hebt geüpload, kunt u de synoniemen voor velden van het type `Edm.String` of gebruiken `Collection(Edm.String)` in velden met `"searchable":true` . Zoals aangegeven, kan een veld definitie slechts één synoniemen kaart gebruiken.

```http
POST /indexes?api-version=2020-06-30
{
    "name":"hotels-sample-index",
    "fields":[
        {
            "name":"description",
            "type":"Edm.String",
            "searchable":true,
            "synonymMaps":[
            "en-synonyms"
            ]
        },
        {
            "name":"description_fr",
            "type":"Edm.String",
            "searchable":true,
            "analyzer":"fr.microsoft",
            "synonymMaps":[
            "fr-synonyms"
            ]
        }
    ]
}
```

## <a name="query-on-equivalent-or-mapped-fields"></a>Query op equivalente of toegewezen velden

Het toevoegen van synoniemen biedt geen nieuwe vereisten voor het bouwen van query's. U kunt termen en woordgroepen query's verzenden net zoals u eerder hebt gedaan voordat u synoniemen hebt toegevoegd. Het enige verschil is dat als een query term bestaat in de synoniemen kaart, de query-engine de term of woord groep uitvouwt of herschrijft, afhankelijk van de regel.

## <a name="how-synonyms-interact-with-other-features"></a>Hoe synoniemen werken met andere functies

De synoniemen functie schrijft de oorspronkelijke query opnieuw met synoniemen met de operator OR. Daarom behandelen treffers markeren en Score profielen de oorspronkelijke term en synoniemen als gelijkwaardig.

Synoniemen zijn alleen van toepassing op zoek query's en worden niet ondersteund voor filters, facetten, automatisch aanvullen of suggesties. Automatisch aanvullen en suggesties zijn alleen gebaseerd op de oorspronkelijke periode. overeenkomsten met synoniemen worden niet weer gegeven in het antwoord.

Synoniemen uitbreidingen zijn niet van toepassing op zoek termen met Joker tekens. voor waarden voor voor voegsels, fuzzy en regex worden niet uitgevouwen.

Als u één query wilt uitvoeren die synoniemen uitbrei ding en Joker teken, regex of fuzzy zoek acties uitvoert, kunt u de query's combi neren met behulp van de of-syntaxis. Als u bijvoorbeeld synoniemen met Joker tekens wilt combi neren voor eenvoudige query syntaxis, is de term `<query> | <query>*` .

Als u een bestaande index in een ontwikkelings omgeving (niet-productie) hebt, kunt u experimenteren met een kleine woorden lijst om te zien hoe het toevoegen van synoniemen de zoek ervaring wijzigt, met inbegrip van de impact op Score profielen, het markeren van treffers en suggesties.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een synoniemen kaart maken (REST API)](/rest/api/searchservice/create-synonym-map)