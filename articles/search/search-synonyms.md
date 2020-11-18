---
title: Synoniemen voor het uitbreiden van query's via een zoek index
titleSuffix: Azure Cognitive Search
description: Maak een synoniemen toewijzing om het bereik van een zoek query op een Azure Cognitive Search-index uit te breiden. Het bereik is uitgebreid met gelijkwaardige termen die u in een lijst opgeeft.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: a8f1fa07b94072d37cf83320b6c8956d3b412f12
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701113"
---
# <a name="synonyms-in-azure-cognitive-search"></a>Synoniemen in azure Cognitive Search

Synoniemen in zoek machines koppelen gelijkwaardige termen waarmee impliciet het bereik van een query wordt uitgebreid, zonder dat de gebruiker daad werkelijk de term hoeft op te geven. Op basis van de term "hond" en synoniemen van "Canine" en "Puppy" worden documenten met "hond", "Canine" of "Puppy" bijvoorbeeld binnen het bereik van de query weer gegeven.

In azure Cognitive Search wordt de uitbrei ding van synoniemen uitgevoerd op het moment van de query. U kunt synoniemen toevoegen aan een service zonder onderbreking van bestaande bewerkingen. U kunt een eigenschap  **synonymMaps** toevoegen aan een veld definitie zonder de index opnieuw op te bouwen.

## <a name="create-synonyms"></a>Synoniemen maken

Er is geen portal ondersteuning voor het maken van synoniemen, maar u kunt de REST API of .NET SDK gebruiken. Om aan de slag te gaan met REST, raden we u aan [postman of Visual Studio code](search-get-started-rest.md) en formulering van aanvragen te gebruiken met deze API: [Create synoniem Maps](/rest/api/searchservice/create-synonym-map). Voor C#-ontwikkel aars kunt u aan de slag met [synoniemen toevoegen in azure cognitieve Zoek opdrachten met C#](search-synonyms-tutorial-sdk.md).

Als u gebruikmaakt van door de [klant beheerde sleutels](search-security-manage-encryption-keys.md) voor versleuteling aan de service zijde, kunt u die beveiliging ook Toep assen op de inhoud van uw synoniemen kaart.

## <a name="use-synonyms"></a>Synoniemen gebruiken

In azure Cognitive Search is de ondersteuning voor synoniemen gebaseerd op synoniemen die u definieert en uploadt naar uw service. Deze kaarten vormen een onafhankelijke resource (zoals indexen of gegevens bronnen) en kunnen worden gebruikt door elk doorzoekbaar veld in een index in uw zoek service.

Synoniemen en indexen worden onafhankelijk bewaard. Wanneer u een synoniemen kaart definieert en uploadt naar uw service, kunt u de synoniemen functie inschakelen voor een veld door een nieuwe eigenschap met de naam **synonymMaps** in de veld definitie toe te voegen. Het maken, bijwerken en verwijderen van een synoniemen kaart is altijd een hele document bewerking, wat betekent dat u geen incrementele delen van de synoniemen kaart kunt maken, bijwerken of verwijderen. Als u zelfs één vermelding wilt bijwerken, moet u het opnieuw laden.

Het opnemen van synoniemen in uw zoek toepassing is een proces in twee stappen:

1.  Voeg aan de hand van de onderstaande Api's een synoniemen kaart toe aan uw zoek service.  

2.  Configureer een Doorzoek bare veld om de synoniemen toewijzing in de index definitie te gebruiken.

U kunt meerdere synoniemen kaarten maken voor uw zoek toepassing (bijvoorbeeld op taal als uw toepassing een meertalige klanten database ondersteunt). Er kan op dit moment slechts een veld worden gebruikt. U kunt de eigenschap synonymMaps van een veld op elk gewenst moment bijwerken.

### <a name="synonymmaps-resource-apis"></a>SynonymMaps-resource-Api's

#### <a name="add-or-update-a-synonym-map-under-your-service-using-post-or-put"></a>Een synoniemen kaart onder uw service toevoegen of bijwerken met behulp van POST of PUT.

Synoniemen worden geüpload naar de service via POST of PUT. Elke regel moet worden gescheiden door het nieuwe regel teken (' \n '). U kunt Maxi maal 5.000 regels per synoniem toewijzen in een gratis service en 20.000 regels per kaart in alle andere Sku's. Elke regel kan Maxi maal 20 uitbrei dingen hebben.

Synoniemen moeten de Apache solr-indeling hebben die hieronder wordt uitgelegd. Als u een bestaande synoniemen lijst in een andere indeling hebt en deze rechtstreeks wilt gebruiken, laat het ons dan weten op [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

U kunt met behulp van HTTP POST een nieuwe synoniemen toewijzing maken, zoals in het volgende voor beeld:

```synonym-map
    POST https://[servicename].search.windows.net/synonymmaps?api-version=2020-06-30
    api-key: [admin key]

    {
       "name":"mysynonymmap",
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }
```

U kunt ook PUT gebruiken en de naam van de toewijzing van het synoniem opgeven op de URI. Als de synoniemen toewijzing niet bestaat, wordt deze gemaakt.

```synonym-map
    PUT https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2020-06-30
    api-key: [admin key]

    {
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }
```

##### <a name="apache-solr-synonym-format"></a>Indeling van Apache solr-synoniem

De solr-indeling ondersteunt gelijkwaardige en expliciete synoniemen toewijzingen. Toewijzings regels voldoen aan de open-source synoniem filter specificatie van Apache solr, zoals beschreven in dit document: [SynonymFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter). Hieronder volgt een voorbeeld regel voor equivalente synoniemen.

```
USA, United States, United States of America
```

Met de bovenstaande regel wordt een zoek opdracht "USA" uitgebreid naar "USA" of "Verenigde Staten" of "Verenigde Staten van Amerika".

Expliciete toewijzing wordt aangeduid met een pijl "=>". Als deze is opgegeven, wordt een term volgorde van een zoek query die overeenkomt met de linkerkant van "=>" vervangen door de alternatieven aan de rechter kant. Op basis van de onderstaande regel, zoek query's "Washington", "wassen" of "WA" wordt allemaal herschreven naar "WA". Expliciete toewijzing is alleen van toepassing in de opgegeven richting en schrijft in dit geval de query "WA" niet opnieuw naar "Washington".

```
Washington, Wash., WA => WA
```

Als u synoniemen wilt definiëren die komma's bevatten, kunt u ze met een back slash, zoals in dit voor beeld:

```
WA\, USA, WA, Washington
```

Omdat de back slash zelf een speciaal teken in andere talen is, zoals JSON en C#, moet u dit waarschijnlijk ook met dubbele Escape. De JSON die wordt verzonden naar de REST API voor de bovenstaande synoniemen kaart ziet er bijvoorbeeld als volgt uit:

```json
    {
       "format":"solr",
       "synonyms": "WA\\, USA, WA, Washington"
    }
```

#### <a name="list-synonym-maps-under-your-service"></a>Lijst met synoniemen in uw service.

```synonym-map
    GET https://[servicename].search.windows.net/synonymmaps?api-version=2020-06-30
    api-key: [admin key]
```

#### <a name="get-a-synonym-map-under-your-service"></a>Maak een synoniemen toewijzing onder uw service.

```synonym-map
    GET https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2020-06-30
    api-key: [admin key]
```

#### <a name="delete-a-synonyms-map-under-your-service"></a>Verwijder een synoniemen toewijzing onder uw service.

```synonym-map
    DELETE https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2020-06-30
    api-key: [admin key]
```

### <a name="configure-a-searchable-field-to-use-the-synonym-map-in-the-index-definition"></a>Configureer een Doorzoek bare veld om de synoniemen toewijzing in de index definitie te gebruiken.

Een nieuwe veld eigenschap **synonymMaps** kan worden gebruikt om een synoniemen toewijzing op te geven die moet worden gebruikt voor een Doorzoek bare veld. Synoniemen zijn service niveau resources en kunnen worden verwezen door elk veld van een index onder de service.

```synonym-map
    POST https://[servicename].search.windows.net/indexes?api-version=2020-06-30
    api-key: [admin key]

    {
       "name":"myindex",
       "fields":[
          {
             "name":"id",
             "type":"Edm.String",
             "key":true
          },
          {
             "name":"name",
             "type":"Edm.String",
             "searchable":true,
             "analyzer":"en.lucene",
             "synonymMaps":[
                "mysynonymmap"
             ]
          },
          {
             "name":"name_jp",
             "type":"Edm.String",
             "searchable":true,
             "analyzer":"ja.microsoft",
             "synonymMaps":[
                "japanesesynonymmap"
             ]
          }
       ]
    }
```

**synonymMaps** kan worden opgegeven voor Doorzoek bare velden van het type EDM. String of Collection (EDM. String).

> [!NOTE]
> U kunt slechts één synoniemen kaart per veld hebben. Als u meerdere synoniemen kaarten wilt gebruiken, laat het ons dan weten op [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="impact-of-synonyms-on-other-search-features"></a>Gevolgen van synoniemen voor andere zoek functies

De synoniemen functie schrijft de oorspronkelijke query opnieuw met synoniemen met de operator OR. Daarom behandelen treffers markeren en Score profielen de oorspronkelijke term en synoniemen als gelijkwaardig.

De functie synoniem is van toepassing op zoek query's en is niet van toepassing op filters of facetten. Op dezelfde manier zijn suggesties alleen gebaseerd op de oorspronkelijke periode. overeenkomsten met synoniemen worden niet weer gegeven in het antwoord.

Synoniemen uitbreidingen zijn niet van toepassing op zoek termen met Joker tekens. voor waarden voor voor voegsels, fuzzy en regex worden niet uitgevouwen.

Als u één query wilt uitvoeren die synoniemen uitbrei ding en Joker teken, regex of fuzzy zoek acties uitvoert, kunt u de query's combi neren met behulp van de of-syntaxis. Als u bijvoorbeeld synoniemen met Joker tekens wilt combi neren voor eenvoudige query syntaxis, is de term `<query> | <query>*` .

Als u een bestaande index in een ontwikkelings omgeving (niet-productie) hebt, kunt u experimenteren met een kleine woorden lijst om te zien hoe het toevoegen van synoniemen de zoek ervaring wijzigt, met inbegrip van de impact op Score profielen, het markeren van treffers en suggesties.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een synoniemen kaart maken](/rest/api/searchservice/create-synonym-map)