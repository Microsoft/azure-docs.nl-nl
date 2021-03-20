---
title: Cache en incrementele verrijking configureren (preview-versie)
titleSuffix: Azure Cognitive Search
description: Schakel caching in en behoud de status van verrijkte inhoud voor beheerde verwerking in een cognitieve vaardigheids. Deze functie is momenteel beschikbaar als openbare preview-versie.
author: vkurpad
manager: eladz
ms.author: vikurpad
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: a1b317b651b0e17c07eb17dbdb8a7c6657d39564
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90971614"
---
# <a name="how-to-configure-caching-for-incremental-enrichment-in-azure-cognitive-search"></a>Caching configureren voor incrementele verrijking in azure Cognitive Search

> [!IMPORTANT] 
> Incrementele verrijking is momenteel beschikbaar als open bare preview. Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie. 
> [Rest API Preview-versies](search-api-preview.md) bieden deze functie. Er is op dit moment geen portal-of .NET SDK-ondersteuning.

In dit artikel wordt beschreven hoe u caching toevoegt aan een verrijkings pijplijn, zodat u de stappen kunt aanpassen zonder dat elke keer opnieuw moet worden opgebouwd. Een vaardig heden is standaard stateless en het wijzigen van een deel van de samen stelling vereist een volledige herinstallatie van de Indexeer functie. Met incrementele verrijking kan de Indexeer functie bepalen welke delen van de document structuur moeten worden vernieuwd op basis van wijzigingen die zijn gedetecteerd in de vaardig heden of indexer definities. Bestaande verwerkte uitvoer blijft behouden en kan waar mogelijk opnieuw worden gebruikt. 

Inhoud in de cache wordt in Azure Storage geplaatst met behulp van de account gegevens die u opgeeft. De container met `ms-az-search-indexercache-<alpha-numerc-string>` de naam wordt gemaakt wanneer u de Indexeer functie uitvoert. Het moet worden beschouwd als een intern onderdeel dat wordt beheerd door uw zoek service en niet moet worden gewijzigd.

Als u niet bekend bent met het instellen van Indexeer functies, start u met [Indexeer functie-overzicht](search-indexer-overview.md) en gaat u verder met [vaardig heden](cognitive-search-working-with-skillsets.md) voor meer informatie over verrijkings pijplijnen. Zie [incrementele verrijking](cognitive-search-incremental-indexing-conceptual.md)voor meer achtergrond informatie over de belangrijkste concepten.

## <a name="enable-caching-on-an-existing-indexer"></a>Opslaan in cache inschakelen voor een bestaande Indexeer functie

Als u een bestaande Indexeer functie hebt die al een vaardig heden hebt, volgt u de stappen in deze sectie om caching toe te voegen. Als eenmalige bewerking moet u de Indexeer functie opnieuw instellen en opnieuw uitvoeren voordat de incrementele verwerking van kracht kan worden.

> [!TIP]
> Als test-of-concept kunt u de Snelstartgids van deze [Portal](cognitive-search-quickstart-blob.md) uitvoeren om de benodigde objecten te maken en vervolgens postman of de portal gebruiken om uw updates te maken. Mogelijk wilt u een factureer bare Cognitive Services resource koppelen. Als u de Indexeer functie meerdere keren uitvoert, wordt de gratis dagelijkse toewijzing uitgeput voordat u alle stappen kunt volt ooien.

### <a name="step-1-get-the-indexer-definition"></a>Stap 1: de definitie van de Indexeer functie ophalen

Begin met een geldige, bestaande Indexeer functie die deze onderdelen bevat: gegevens bron, vaardig heden, index. De Indexeer functie moet uitvoer bare zijn. 

Maak met behulp van een API-client een [Get Indexeer functie-aanvraag](/rest/api/searchservice/get-indexer) om de huidige configuratie van de Indexeer functie op te halen. Wanneer u de preview-API-versie gebruikt voor de Indexeer functie ophalen, `cache` wordt een eigenschap ingesteld op null toegevoegd aan de definities.

```http
GET https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

Kopieer de definitie van de Indexeer functie van het antwoord.

### <a name="step-2-modify-the-cache-property-in-the-indexer-definition"></a>Stap 2: de cache-eigenschap in de definitie van de Indexeer functie wijzigen

De `cache` eigenschap is standaard NULL. Gebruik een API-client om de cache configuratie in te stellen (de portal biedt geen ondersteuning voor deze deeltjes update). 

Wijzig het cache object zodat de volgende vereiste en optionele eigenschappen zijn opgegeven: 

+ De `storageConnectionString` is vereist en moet worden ingesteld op een Azure Storage-Connection String. 
+ De `enableReprocessing` Booleaanse eigenschap is optioneel ( `true` standaard) en geeft aan dat incrementele verrijking is ingeschakeld. Als dat nodig is, kunt u deze instellen op `false` om incrementele verwerking uit te stellen, terwijl andere resources in de resource worden uitgevoerd, zoals het indexeren van nieuwe documenten, en deze vervolgens weer spie gelen naar een `true` later tijdstip.

```json
{
    "name": "<YOUR-INDEXER-NAME>",
    "targetIndexName": "<YOUR-INDEX-NAME>",
    "dataSourceName": "<YOUR-DATASOURCE-NAME>",
    "skillsetName": "<YOUR-SKILLSET-NAME>",
    "cache" : {
        "storageConnectionString" : "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
}
```

### <a name="step-3-reset-the-indexer"></a>Stap 3: de Indexeer functie opnieuw instellen

Het opnieuw instellen van de Indexeer functie is vereist bij het instellen van incrementele verrijking voor bestaande Indexeer functies om ervoor te zorgen dat alle documenten een consistente status hebben. U kunt de portal of een API-client en de [rest API voor het opnieuw instellen van de Indexeer functie](/rest/api/searchservice/reset-indexer) voor deze taak gebruiken.

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/reset?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

### <a name="step-4-save-the-updated-definition"></a>Stap 4: de bijgewerkte definitie opslaan

[De Indexeer functie bijwerken](/rest/api/searchservice/preview-api/update-indexer) met een put-aanvraag, de hoofd tekst van de aanvraag moet de bijgewerkte indexerings definitie bevatten met de cache-eigenschap. Als u een 400 krijgt, controleert u de definitie van de Indexeer functie om er zeker van te zijn dat aan alle vereisten wordt voldaan (gegevens bron, vaardig heden, index).

```http
PUT https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
{
    "name" : "<YOUR-INDEXER-NAME>",
    ...
    "cache": {
        "storageConnectionString": "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    }
}
```

Als u nu een nieuwe GET-aanvraag voor de Indexeer functie levert, bevat het antwoord van de service een `ID` eigenschap in het cache-object. De alfanumerieke teken reeks wordt toegevoegd aan de naam van de container die alle in de cache opgeslagen resultaten en de tussenliggende status van elk document verwerkt door deze Indexeer functie bevat. De ID wordt gebruikt om de cache in Blob Storage een unieke naam te krijgen.

```http
    "cache": {
        "ID": "<ALPHA-NUMERIC STRING>",
        "enableReprocessing": true,
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-STORAGE-KEY>;EndpointSuffix=core.windows.net"
    }
```

### <a name="step-5-run-the-indexer"></a>Stap 5: de Indexeer functie uitvoeren

Als u Indexeer functie wilt uitvoeren, kunt u de portal of de API gebruiken. Selecteer in de portal in de lijst Indexeer functies de Indexeer functie en klik op **uitvoeren**. Een voor deel van het gebruik van de portal is dat u de Indexeer functie-status kunt bewaken, de duur van de taak ziet en hoeveel documenten worden verwerkt. Portal pagina's worden elke paar minuten vernieuwd.

U kunt ook REST gebruiken om [de Indexeer functie uit te voeren](/rest/api/searchservice/run-indexer):

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/run?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

Nadat de Indexeer functie is uitgevoerd, kunt u de cache vinden in Azure Blob-opslag. De container naam heeft de volgende indeling: `ms-az-search-indexercache-<YOUR-CACHE-ID>`

> [!NOTE]
> Het opnieuw instellen en opnieuw uitvoeren van de Indexeer functie resulteert in een volledige heropbouw zodat inhoud in de cache kan worden opgeslagen. Alle cognitieve verrijkingen worden opnieuw op alle documenten uitgevoerd.
>

### <a name="step-6-modify-a-skillset-and-confirm-incremental-enrichment"></a>Stap 6: een vaardig heden wijzigen en een incrementele verrijking bevestigen

Als u een vaardig heden wilt aanpassen, kunt u de portal of de API gebruiken. Als u bijvoorbeeld tekst vertalingen gebruikt, is een eenvoudige inline wijziging van `en` naar of een `es` andere taal voldoende voor het testen van het concept van incrementele verrijking.

Voer de Indexeer functie opnieuw uit. Alleen de onderdelen van een verrijkte document structuur worden bijgewerkt. Als u de [Portal Snelstartgids](cognitive-search-quickstart-blob.md) hebt gebruikt als concept van het proef versie niveau en de tekst Vertaal vaardigheid wijzigt in es, zult u merken dat slechts 8 documenten worden bijgewerkt in plaats van de oorspronkelijke 14. Afbeeldings bestanden die niet worden beïnvloed door het Vertaal proces, worden opnieuw gebruikt vanuit de cache.

## <a name="enable-caching-on-new-indexers"></a>Opslaan in cache inschakelen voor nieuwe Indexeer functies

Als u incrementele verrijking wilt instellen voor een nieuwe Indexeer functie, hoeft u alleen de `cache` eigenschap op te geven in de nettolading van de definitie van de Indexeer functie bij het aanroepen van [Create Indexeer functie (2020-06-30-preview)](/rest/api/searchservice/preview-api/create-indexer). 


```json
{
    "name": "<YOUR-INDEXER-NAME>",
    "targetIndexName": "<YOUR-INDEX-NAME>",
    "dataSourceName": "<YOUR-DATASOURCE-NAME>",
    "skillsetName": "<YOUR-SKILLSET-NAME>",
    "cache" : {
        "storageConnectionString" : "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
    }
}
```

## <a name="checking-for-cached-output"></a>Controleren op uitvoer in de cache

De cache wordt gemaakt, gebruikt en beheerd door de Indexeer functie en de inhoud ervan wordt niet weer gegeven in een indeling die door een mens kan worden gelezen. De beste manier om te bepalen of de cache wordt gebruikt, is door de Indexeer functie uit te voeren en te vergelijken vóór-en-na de metrische gegevens voor de uitvoerings tijd en het aantal documenten. 

Stel dat er een vakkennisset is die begint met het analyseren van afbeeldingen en optische teken herkenning (OCR) van gescande documenten, gevolgd door downstream-analyse van de resulterende tekst. Als u een stroomafwaartse tekst vaardigheid wijzigt, kan de Indexeer functie alle eerder verwerkte afbeeldings-en OCR-inhoud ophalen uit de cache, en alleen tekst wijzigingen die door uw bewerkingen worden aangegeven, bijwerken en verwerken. U kunt verwachten dat er minder documenten per document worden weer geven (bijvoorbeeld 8/8, in tegens telling tot 14/14 in de oorspronkelijke uitvoering), korte uitvoerings tijden en minder kosten op uw factuur.

## <a name="working-with-the-cache"></a>Werken met de cache

Zodra de cache operationeel is, controleren indexers de cache telkens wanneer [Run Indexeer functie](/rest/api/searchservice/run-indexer) wordt aangeroepen om te zien welke delen van de bestaande uitvoer kunnen worden gebruikt. 

De volgende tabel bevat een overzicht van de werking van verschillende Api's met betrekking tot de cache:

| API           | Cache-impact     |
|---------------|------------------|
| [Indexeer functie maken (2020-06-30-preview)](/rest/api/searchservice/preview-api/create-indexer) | Maakt en voert een indexer uit bij het eerste gebruik, inclusief het maken van een cache als de definitie van de Indexeer functie deze specificeert. |
| [Indexeer functie uitvoeren](/rest/api/searchservice/run-indexer) | Voert een verrijkings pijplijn op aanvraag uit. Deze API leest uit de cache als deze bestaat, of maakt een cache als u de cache hebt toegevoegd aan een bijgewerkte indexers definitie. Wanneer u een Indexeer functie uitvoert waarvoor caching is ingeschakeld, worden stappen door de Indexeer functie wegge laten als de in de cache geplaatste uitvoer kan worden gebruikt. U kunt de algemeen beschik bare of preview-API-versie van deze API gebruiken.|
| [Indexeer functie opnieuw instellen](/rest/api/searchservice/reset-indexer)| Hiermee wist u de Indexeer functie van alle incrementele indexerings gegevens. De volgende uitvoering van de Indexeer functie (op aanvraag of planning) is volledig opnieuw verwerken, inclusief het opnieuw uitvoeren van alle vaardig heden en het opnieuw opbouwen van de cache. Het is functioneel equivalent om de Indexeer functie te verwijderen en opnieuw te maken. U kunt de algemeen beschik bare of preview-API-versie van deze API gebruiken.|
| [Vaardig heden opnieuw instellen](/rest/api/searchservice/preview-api/reset-skills) | Hiermee geeft u op welke vaardig heden moeten worden uitgevoerd bij het uitvoeren van de volgende Indexeer functie, zelfs als u geen enkele vaardig heden hebt gewijzigd. De cache wordt dienovereenkomstig bijgewerkt. Uitvoer, zoals een kennis archief of een zoek index, worden vernieuwd met herbruikbare gegevens uit de cache plus nieuwe inhoud per de bijgewerkte vaardigheid. |

Zie [cache beheer](cognitive-search-incremental-indexing-conceptual.md#cache-management)voor meer informatie over het beheren van wat er gebeurt met de cache.

## <a name="next-steps"></a>Volgende stappen

Incrementele verrijking is van toepassing op Indexeer functies die vaardig heden bevatten. Als volgende stap gaat u naar de documentatie van de vaardig heden om inzicht te krijgen in concepten en samen stelling. 

Wanneer u de cache inschakelt, moet u ook weten over de para meters en Api's die in de cache worden opgeslagen, inclusief het negeren of afdwingen van bepaalde gedragingen. Zie de volgende koppelingen voor meer informatie.

+ [Concepten en samen stelling van vaardig heden](cognitive-search-working-with-skillsets.md)
+ [Een vaardighedenset maken](cognitive-search-defining-skillset.md)
+ [Inleiding tot incrementele verrijking en caching](cognitive-search-incremental-indexing-conceptual.md)