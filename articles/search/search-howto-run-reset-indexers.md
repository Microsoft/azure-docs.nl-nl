---
title: Indexeer functies uitvoeren of opnieuw instellen
titleSuffix: Azure Cognitive Search
description: Stel een indexer, vaardig heden of afzonderlijke documenten opnieuw in om alle of een deel van de index of het hele kennis archief te vernieuwen.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/09/2021
ms.openlocfilehash: bf8a4e51e23f438265af706914a6bc73ec30f64d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101667666"
---
# <a name="how-to-run-or-reset-indexers-skills-or-documents"></a>Indexeer functies, vaardig heden of documenten uitvoeren of opnieuw instellen

De uitvoering van de Indexeer functie kan optreden wanneer u de [Indexeer](search-indexer-overview.md)functie voor het eerst maakt, wanneer u een Indexeer functie op aanvraag uitvoert, of wanneer u een Indexeer functie op basis van een schema instelt. Na de eerste uitvoering houdt een Indexeer functie bij welke Zoek documenten zijn geïndexeerd door middel van een intern ' hoog water merk '. De markering wordt nooit weer gegeven in de API, maar intern de Indexeer functie weet waar het indexeren is gestopt, zodat het kan worden opgehaald waar het bij de volgende uitvoering was gebleven.

U kunt het hoge water merk wissen door de Indexeer functie opnieuw in te stellen als u helemaal opnieuw wilt verwerken. Reset Api's zijn beschikbaar op aflopende niveaus in de object hiërarchie:

+ De volledige Zoek verzameling (gebruik [Indexeer functies opnieuw instellen](#reset-indexers))
+ Een specifiek document of een lijst met documenten (gebruik [opnieuw ingestelde documenten-preview](#reset-docs))
+ Een specifieke vaardigheid of verrijking in een document (gebruik [opnieuw instellen vaardig heden-preview](#reset-skills))

De reset-Api's worden gebruikt om in de cache opgeslagen inhoud te vernieuwen (van toepassing in [AI](cognitive-search-concept-intro.md) -scenario's), of om de bovengrens te wissen en de index opnieuw op te bouwen.

Opnieuw instellen, gevolgd door uitvoeren, kan bestaande documenten en nieuwe documenten opnieuw verwerken, maar verwijdert geen zwevende Zoek documenten in de zoek index die op eerdere uitvoeringen zijn gemaakt. Zie [documenten toevoegen, bijwerken of verwijderen](/rest/api/searchservice/addupdate-or-delete-documents)voor meer informatie over verwijderen.

## <a name="run-indexers"></a>Indexeer functies uitvoeren

Met [Create Indexeer functie](/rest/api/searchservice/create-indexer) wordt de Indexeer functie gemaakt en uitgevoerd, tenzij u deze in een uitgeschakelde status maakt ("uitgeschakeld": True). De eerste uitvoering neemt iets langer in beslag omdat het object ook wordt gemaakt.

Met [Indexeer functie](/rest/api/searchservice/run-indexer) wordt alleen gedetecteerd en verwerkt wat het nodig is om de zoek index te synchroniseren met de gegevens bron. Blob-opslag heeft ingebouwde detectie van wijzigingen. Andere gegevens bronnen, zoals Azure SQL of Cosmos DB, moeten worden geconfigureerd voor de detectie van wijzigingen voordat de Indexeer functie alleen de nieuwe en bijgewerkte rijen kan lezen.

U kunt een Indexeer functie uitvoeren met een van de volgende methoden:

+ Azure Portal, met behulp van de opdracht **uitvoeren** op de pagina Indexeer functie
+ [Indexeer functie uitvoeren (REST)](/rest/api/searchservice/run-indexer)
+ [Methode RunIndexers](/dotnet/api/azure.search.documents.indexes.searchindexerclient.runindexer) in de Azure .NET SDK (of met behulp van de equivalente RunIndexer-methode in een andere SDK)

De uitvoering van de Indexeer functie is onderhevig aan de volgende limieten:

+ Het maximum aantal Indexeer functies is 1 per replica zonder gelijktijdige taken.

  Als de uitvoering van de Indexeer functie al is ingesteld op capaciteit, ontvangt u deze melding: "kan Indexeer functie niet uitvoeren <naam>", fout: "er wordt momenteel een andere Indexeer functie uitgevoerd. gelijktijdige aanroepen zijn niet toegestaan. "

+ De maximale uitvoerings tijd is 2 uur als u een vaardig heden gebruikt, en 24 uur zonder. 

  U kunt de verwerking uitrekken door de Indexeer functie op basis van een schema te plaatsen. De laag gratis heeft de tijds limieten voor lagere uitvoeringen. Zie [limieten voor Indexeer functie](search-limits-quotas-capacity.md#indexer-limits) voor de volledige lijst.

<a name="reset-indexers"></a>

## <a name="reset-an-indexer"></a>Een Indexeer functie opnieuw instellen

Een Indexeer functie wordt opnieuw ingesteld. In de zoek index is elk zoek document dat oorspronkelijk door de Indexeer functie is gevuld, gemarkeerd voor volledige verwerking. Nieuwe documenten gevonden de onderliggende bron worden toegevoegd aan de index als Zoek documenten. Als de Indexeer functie is geconfigureerd voor het gebruik van een vaardig heden en [caching](search-howto-incremental-index.md), wordt de vaardig heden opnieuw uitgevoerd en wordt de cache vernieuwd.

U kunt een Indexeer functie opnieuw instellen met behulp van een van deze benaderingen, gevolgd door een indexer dat wordt uitgevoerd met een van de hierboven beschreven methoden.

+ Azure Portal met behulp van de opdracht **opnieuw instellen** op de pagina Indexeer functie
+ [Indexeer functie opnieuw instellen (REST)](/rest/api/searchservice/reset-indexer)
+ [Methode ResetIndexers](/dotnet/api/azure.search.documents.indexes.searchindexerclient.resetindexer) in de Azure .NET SDK (of met behulp van de equivalente RunIndexer-methode in een andere SDK)

Een reset-markering wordt gewist nadat de uitvoering is voltooid. Een regel matige detectie logica die van kracht is voor uw gegevens bron wordt hervat bij de volgende uitvoering, waarbij alle andere nieuwe of bijgewerkte waarden worden opgehaald in de rest van de gegevensset.

> [!NOTE]
> Een aanvraag voor opnieuw instellen bepaalt wat opnieuw wordt verwerkt (indexer, vaardigheid of document), maar heeft geen invloed op het gedrag van de indexer-runtime. Als de Indexeer functie run time-para meters, veld toewijzingen, caching, batch opties, enzovoort bevat, zijn die instellingen allemaal van kracht wanneer u een Indexeer functie uitvoert nadat u deze opnieuw hebt ingesteld.

<a name="reset-skills"></a>

## <a name="reset-skills-preview"></a>Vaardig heden opnieuw instellen (preview-versie)

> [!IMPORTANT] 
> Het [opnieuw instellen van vaardig heden](/rest/api/searchservice/preview-api/reset-skills) bevindt zich in de open bare preview, alleen via de preview-versie rest API. Preview-functies worden onder [aanvullende gebruiks voorwaarden](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)aangeboden.

Voor Indexeer functies met vaardig heden kunt u specifieke vaardig heden opnieuw instellen om de verwerking van die vaardigheid af te dwingen en de downstream-vaardig heden die afhankelijk zijn van de uitvoer. [Verrijkingen in de cache](search-howto-incremental-index.md) worden ook vernieuwd. Door vaardig heden opnieuw in te stellen, worden de vaardigheids resultaten in de cache ongeldig. Dit is handig wanneer een nieuwe versie van een vaardigheid wordt geïmplementeerd en u wilt dat de Indexeer functie die kwalificatie voor alle documenten opnieuw moet uitvoeren. 

Het [opnieuw instellen van vaardig heden](/rest/api/searchservice/preview-api/reset-skills) is beschikbaar via rest **`api-version=2020-06-30-Preview`** .

```http
POST https://[service name].search.windows.net/skillsets/[skillset name]/resetskills?api-version=2020-06-30-Preview
{
    "skillNames" : [
        "#1",
        "#5",
        "#6"
    ]
}
```

U kunt afzonderlijke vaardig heden opgeven, zoals in het bovenstaande voor beeld is aangegeven, maar als een van deze vaardig heden uitvoer vereist van niet-vermelde vaardig heden (#2 via #4), worden niet-vermelde vaardig heden uitgevoerd, tenzij de cache de benodigde gegevens kan verstrekken. Als u dit wilt doen, moet u in cache opgeslagen verrijkingen voor vaardig heden #2 via #4 geen afhankelijkheid hebben van #1 (vermeld voor opnieuw instellen).

Als er geen vaardig heden zijn opgegeven, wordt de volledige vaardig heden uitgevoerd en als caching is ingeschakeld, wordt de cache ook vernieuwd.

<a name="reset-docs"></a>

## <a name="reset-docs-preview"></a>Documenten opnieuw instellen (preview-versie)

> [!IMPORTANT] 
> Het [opnieuw instellen van documenten](/rest/api/searchservice/preview-api/reset-documents) is in open bare preview, die alleen beschikbaar is via de preview-versie rest API. Preview-functies worden onder [aanvullende gebruiks voorwaarden](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)aangeboden.

De [API voor het opnieuw instellen van documenten](/rest/api/searchservice/preview-api/reset-documents) accepteert een lijst met document sleutels zodat u specifieke documenten kunt vernieuwen. Indien opgegeven, worden de para meters opnieuw instellen de enige determinant van wat er wordt verwerkt, ongeacht andere wijzigingen in de onderliggende gegevens. Als er bijvoorbeeld 20 blobs zijn toegevoegd of bijgewerkt nadat de laatste Indexeer functie is uitgevoerd, maar u slechts één document opnieuw hebt ingesteld, wordt alleen het ene document verwerkt.

Alle velden in dat Zoek document worden per document vernieuwd met waarden uit de gegevens bron. U kunt niet kiezen en selecteren welke velden u wilt vernieuwen. 

Als het document wordt verrijkt via een vaardig heden en gegevens in de cache is opgeslagen, wordt de vaardig heden aangeroepen voor alleen de opgegeven documenten en wordt het cache geheugen bijgewerkt voor de opnieuw verwerkte documenten.

Wanneer u deze API voor het eerst test, kunnen de volgende Api's u helpen bij het valideren en testen van het gedrag:

+ De [Indexeer functie-status ophalen](/rest/api/searchservice/get-indexer-status) met API `2020-06-30-Preview` -versie, om de status van opnieuw instellen en de uitvoerings status te controleren. Aan het einde van de status reactie vindt u informatie over de aanvraag voor opnieuw instellen.
+ [Documenten opnieuw instellen](/rest/api/searchservice/preview-api/reset-documents) met API `2020-06-30-Preview` -versie om op te geven welke documenten moeten worden verwerkt.
+ [Voer Indexeer functie uit](/rest/api/searchservice/run-indexer) om de Indexeer functie (elke API-versie) uit te voeren.
+ [Documenten zoeken](/rest/api/searchservice/search-documents) om te controleren op bijgewerkte waarden en om document sleutels te retour neren als u niet zeker weet wat de waarde is. Gebruik `"select": "<field names>"` deze als u wilt beperken welke velden in het antwoord worden weer gegeven.

### <a name="formulate-and-send-the-reset-request"></a>De aanvraag voor opnieuw instellen formuleren en verzenden

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/resetdocs?api-version=2020-06-30-Preview
{
    "documentKeys" : [
        "1001",
        "4452"
    ]
}
```

De document sleutels die in de aanvraag worden gegeven, zijn waarden uit de zoek index. deze kunnen afwijken van de overeenkomende velden in de gegevens bron. Als u niet zeker weet wat de sleutel waarde is, [verzendt u een query](search-query-create.md) om de waarde te retour neren. U kunt gebruiken `select` om alleen het document sleutel veld te retour neren.

Voor blobs die worden geparseerd in meerdere zoek documenten (bijvoorbeeld als u [jsonLines of jsonArrays](search-howto-index-json-blobs.md), of [delimitedText](search-howto-index-csv-blobs.md)) hebt gebruikt als een parseringsfout, wordt de document sleutel gegenereerd door de Indexeer functie en is deze mogelijk niet bekend voor u. In deze situatie is een query voor de document sleutel instrumentatie voor het leveren van de juiste waarde.

Het aanroepen van de API meerdere keren met verschillende sleutels voegt de nieuwe sleutels toe aan de lijst met document sleutels resetten. Als u de API aanroept met de **`overwrite`** para meter ingesteld op True, wordt de huidige lijst met document sleutels die opnieuw moeten worden ingesteld, overschreven met de nettolading van de aanvraag:

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/resetdocs?api-version=2020-06-30-Preview
{
    "documentKeys" : [
        "200",
        "630"
    ],
    "overwrite": true
}
```

## <a name="check-reset-status"></a>Status van opnieuw instellen controleren

Als u de status van een opnieuw instellen wilt controleren en wilt zien welke document sleutels in de wachtrij staan voor verwerking, gebruikt u de status van de [Indexeer functie ophalen](/rest/api/searchservice/get-indexer-status) met **`api-version=06-30-2020-Preview`** . De preview-API retourneert de **`currentState`** sectie, die u aan het einde van het antwoord status van de Indexeer functie ophalen kunt vinden.

De ' modus ' is **`indexingAllDocs`** voor het opnieuw instellen van vaardig heden (omdat het mogelijk is dat alle documenten worden beïnvloed, voor de velden die worden gevuld met AI-verrijking).

Voor het opnieuw instellen van documenten wordt de modus ingesteld op **`indexingResetDocs`** . De Indexeer functie behoudt deze status totdat alle document sleutels die zijn opgenomen in de aanroep voor het opnieuw instellen van documenten worden verwerkt en er geen andere Indexeer functie taken worden uitgevoerd terwijl de bewerking wordt uitgevoerd. Als u alle documenten in de lijst document sleutels zoekt, moet u elk document kraken om de sleutel te zoeken en te vergelijken. Dit kan enige tijd duren als de gegevensset erg groot is. Als een BLOB-container honderden blobs bevat en de documenten die u wilt herstellen aan het einde zijn, worden de overeenkomende blobs pas gevonden nadat alle andere zijn gecontroleerd.

```json
"currentState": {
    "mode": "indexingResetDocs",
    "allDocsInitialTrackingState": "{\"LastFullEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"LastAttemptedEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"NameHighWaterMark\":null}",
    "allDocsFinalTrackingState": "{\"LastFullEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"LastAttemptedEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"NameHighWaterMark\":null}",
    "resetDocsInitialTrackingState": null,
    "resetDocsFinalTrackingState": null,
    "resetDocumentKeys": [
        "200",
        "630"
    ]
}
```

Nadat de documenten opnieuw zijn verwerkt, keert de Indexeer functie terug naar de **`indexingAllDocs`** modus en worden eventuele andere nieuwe of bijgewerkte documenten verwerkt bij de volgende uitvoering.

## <a name="next-steps"></a>Volgende stappen

Reset Api's worden gebruikt om het bereik van de uitvoering van de volgende Indexeer functie te informeren. Voor de daad werkelijke verwerking moet u een Indexeer functie op aanvraag uitvoeren of een geplande taak toestaan om het werk te volt ooien. Nadat de uitvoering is voltooid, keert de Indexeer functie terug naar de normale verwerking, of dat nu volgens een planning of op aanvraag wordt verwerkt.

Nadat u de Indexeer functies opnieuw hebt ingesteld en opnieuw hebt uitgevoerd, kunt u de status van de zoek service controleren of gedetailleerde informatie verkrijgen via diagnostische logboek registratie.

+ [Indexeer bewerkingen (REST)](/rest/api/searchservice/indexer-operations)
+ [Status van de zoek Indexeer functie bewaken](search-howto-monitor-indexers.md)
+ [Logboek gegevens verzamelen en analyseren](search-monitor-logs.md)
+ [Een Indexeer functie plannen](search-howto-schedule-indexers.md)