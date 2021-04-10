---
title: Diagnostische logboeken inschakelen en er query's op uitvoeren
titleSuffix: Azure Digital Twins
description: Zie logboek registratie met Diagnostische instellingen inschakelen en de logboeken doorzoeken op direct weer geven.
author: baanders
ms.author: baanders
ms.date: 2/24/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 08db4d92da5213b1ce1b79867650da9df8c38ee4
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106385076"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>Problemen oplossen met Azure Digital Apparaatdubbels: logboek registratie van diagnostische gegevens

Azure Digital Apparaatdubbels kan Logboeken voor uw service-exemplaar verzamelen om de prestaties, toegang en andere gegevens te bewaken. U kunt deze logboeken gebruiken om een idee te krijgen van wat er gebeurt in uw Azure Digital Apparaatdubbels-exemplaar en om de hoofd oorzaak analyse van problemen uit te voeren zonder dat u contact hoeft op te nemen met de ondersteuning van Azure.

In dit artikel wordt beschreven hoe u [**Diagnostische instellingen kunt configureren**](#turn-on-diagnostic-settings) in de [Azure Portal](https://portal.azure.com) om logboeken te verzamelen van uw Azure Digital apparaatdubbels-exemplaar. U kunt ook opgeven waar de logboeken moeten worden opgeslagen (zoals Log Analytics of een opslag account van uw keuze).

Dit artikel bevat ook een lijst met alle [logboek categorieën](#log-categories) en [logboek Schema's](#log-schemas) die door Azure Digital apparaatdubbels worden verzameld.

Nadat u Logboeken hebt ingesteld, kunt u ook [**een query uitvoeren op de logboeken**](#view-and-query-logs) om snel aangepaste inzichten te verzamelen.

## <a name="turn-on-diagnostic-settings"></a>Diagnostische instellingen inschakelen 

Schakel Diagnostische instellingen in om te beginnen met het verzamelen van Logboeken in uw Azure Digital Apparaatdubbels-exemplaar. U kunt ook het doel kiezen waar de geëxporteerde logboeken moeten worden opgeslagen. Hier vindt u informatie over het inschakelen van diagnostische instellingen voor uw Azure Digital Apparaatdubbels-exemplaar.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en navigeer naar uw Azure Digital apparaatdubbels-exemplaar. U kunt het vinden door de naam ervan in de zoek balk van de portal te typen. 

2. Selecteer **Diagnostische instellingen** in het menu en vervolgens **Diagnostische instelling toevoegen**.

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="Scherm opname van de pagina met Diagnostische instellingen en de knop die u wilt toevoegen" lightbox="media/troubleshoot-diagnostics/diagnostic-settings.png":::

3. Vul op de onderstaande pagina de volgende waarden in:
     * **Naam van de diagnostische instelling**: Geef de diagnostische instellingen een naam.
     * **Categorie Details**: Kies welke bewerkingen u wilt bewaken en schakel de selectie vakjes in om diagnostische gegevens voor deze bewerkingen in te scha kelen. De bewerkingen waarvan de diagnostische instellingen kunnen rapporteren, zijn:
        - DigitalTwinsOperation
        - EventRoutesOperation
        - ModelsOperation
        - QueryOperation
        - AllMetrics
        
        Zie de sectie [*logboek categorieën*](#log-categories) hieronder voor meer informatie over deze categorieën en de informatie die ze bevatten.
     * **Doel Details**: Kies waar u de logboeken wilt verzenden. U kunt een combi natie van de drie opties selecteren:
        - Verzenden naar Log Analytics
        - Archiveren naar een opslagaccount
        - Streamen naar een Event Hub

        U wordt mogelijk gevraagd aanvullende gegevens in te vullen als deze nodig zijn voor uw doel selectie.  
    
4. Sla de nieuwe instellingen op. 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="Scherm afbeelding van de pagina met Diagnostische instellingen waarop de gebruiker de naam van de diagnostische instelling heeft ingevuld en enkele selectie vakjes hebt geselecteerd voor categorie Details en doel gegevens. De knop Opslaan is gemarkeerd." lightbox="media/troubleshoot-diagnostics/diagnostic-settings-details.png":::

Nieuwe instellingen worden in ongeveer 10 minuten van kracht. Daarna worden logboeken weer gegeven in de geconfigureerde doel weer op de pagina **Diagnostische instellingen** voor uw exemplaar. 

Ga voor meer informatie over diagnostische instellingen en de bijbehorende installatie opties naar [*Diagnostische instellingen maken om platform logboeken en metrische gegevens naar verschillende bestemmingen te verzenden*](../azure-monitor/essentials/diagnostic-settings.md).

## <a name="log-categories"></a>Logboek Categorieën

Hier vindt u meer informatie over de logboeken die door Azure Digital Apparaatdubbels worden verzameld.

| Logboekcategorie | Beschrijving |
| --- | --- |
| ADTModelsOperation | Alle API-aanroepen voor modellen vastleggen |
| ADTQueryOperation | Alle API-aanroepen met betrekking tot Query's vastleggen |
| ADTEventRoutesOperation | Registreer alle API-aanroepen die betrekking hebben op gebeurtenis routes en voor het uitvoeren van gebeurtenissen van Azure Digital Apparaatdubbels naar een eindpunt service, zoals Event Grid, Event Hubs en Service Bus |
| ADTDigitalTwinsOperation | Alle API-aanroepen vastleggen die betrekking hebben op afzonderlijke apparaatdubbels |

Elke logboek categorie bestaat uit bewerkingen van schrijven, lezen, verwijderen en actie.  Deze toewijzing REST API aanroepen als volgt:

| Gebeurtenistype | REST API bewerkingen |
| --- | --- |
| Schrijven | PUT and PATCH |
| Lezen | GET |
| Verwijderen | DELETE |
| Bewerking | POST |

Hier volgt een uitgebreide lijst met de bewerkingen en bijbehorende [Azure Digital apparaatdubbels rest API-aanroepen](/rest/api/azure-digitaltwins/) die in elke categorie zijn geregistreerd. 

>[!NOTE]
> Elke logboek categorie bevat verschillende bewerkingen/REST API-aanroepen. In de onderstaande tabel wordt elke logboek categorie toegewezen aan alle bewerkingen/REST API-aanroepen, totdat de volgende logboek categorie wordt weer gegeven. 

| Logboekcategorie | Bewerking | REST API-aanroepen en andere gebeurtenissen |
| --- | --- | --- |
| ADTModelsOperation | Micro soft. DigitalTwins/modellen/schrijven | Update-API voor digitale dubbele modellen |
|  | Micro soft. DigitalTwins/modellen/lezen | Digitale twee modellen ontvangen op ID en lijst-Api's |
|  | Micro soft. DigitalTwins/modellen/verwijderen | Digitale dubbele modellen API verwijderen |
|  | Micro soft. DigitalTwins/modellen/actie | Digitale dubbele modellen API toevoegen |
| ADTQueryOperation | Micro soft. DigitalTwins/query/actie | Query Apparaatdubbels-API |
| ADTEventRoutesOperation | Micro soft. DigitalTwins/eventroutes/schrijven | Gebeurtenis routes toevoegen API |
|  | Micro soft. DigitalTwins/eventroutes/lezen | Gebeurtenis routes ophalen op basis van ID en lijst-Api's |
|  | Micro soft. DigitalTwins/eventroutes/verwijderen | Gebeurtenis routes verwijderen API |
|  | Micro soft. DigitalTwins/eventroutes/actie | Fout bij het publiceren van gebeurtenissen naar een eindpunt service (geen API-aanroep) |
| ADTDigitalTwinsOperation | Micro soft. DigitalTwins/DigitalTwins/schrijven | Digitale Apparaatdubbels toevoegen, relatie toevoegen, bijwerken, onderdeel bijwerken |
|  | Micro soft. DigitalTwins/DigitalTwins/lezen | Digital Apparaatdubbels get by ID, component ophalen, relatie ophalen op ID, binnenkomende relaties weer geven, lijst relaties |
|  | Micro soft. DigitalTwins/DigitalTwins/verwijderen | Digitale Apparaatdubbels verwijderen, relatie verwijderen |
|  | Micro soft. DigitalTwins/DigitalTwins/actie | Digital Apparaatdubbels, telemetrie van het onderdeel verzenden, telemetrie verzenden |

## <a name="log-schemas"></a>Logboek schema's 

Elke logboek categorie heeft een schema dat definieert hoe gebeurtenissen in die categorie worden gerapporteerd. Elke afzonderlijke logboek vermelding wordt als tekst opgeslagen en opgemaakt als een JSON-blob. De velden in de logboeken en voor beeld-JSON-instanties worden aangegeven voor elk logboek type hieronder. 

`ADTDigitalTwinsOperation`, `ADTModelsOperation` en `ADTQueryOperation` gebruik een consistent API-logboek schema. `ADTEventRoutesOperation` Hiermee wordt het schema uitgebreid met een `endpointName` veld in eigenschappen.

### <a name="api-log-schemas"></a>API-logboek schema's

Dit logboek schema is consistent voor `ADTDigitalTwinsOperation` , `ADTModelsOperation` ,, `ADTQueryOperation` . Er wordt ook hetzelfde schema gebruikt voor `ADTEventRoutesOperation` , met **uitzonde ring** van de naam van de `Microsoft.DigitalTwins/eventroutes/action` bewerking (Zie de volgende sectie, [*Logboeken*](#egress-log-schemas)van de uitgevende logaritmen) voor meer informatie over dat schema.

Het schema bevat informatie die betrekking heeft op API-aanroepen naar een Azure Digital Apparaatdubbels-exemplaar.

Dit zijn de omschrijvingen van velden en eigenschappen voor API-Logboeken.

| Veldnaam | Gegevenstype | Beschrijving |
|-----|------|-------------|
| `Time` | DateTime | De datum en tijd waarop deze gebeurtenis is opgetreden, in UTC |
| `ResourceId` | Tekenreeks | De Azure Resource Manager Resource-ID voor de resource waar het evenement plaatsvond |
| `OperationName` | Tekenreeks  | Het type actie dat wordt uitgevoerd tijdens de gebeurtenis |
| `OperationVersion` | Tekenreeks | De API-versie die wordt gebruikt tijdens de gebeurtenis |
| `Category` | Tekenreeks | Het type bron dat wordt verzonden |
| `ResultType` | Tekenreeks | Resultaat van de gebeurtenis |
| `ResultSignature` | Tekenreeks | Http-status code voor de gebeurtenis |
| `ResultDescription` | Tekenreeks | Aanvullende informatie over de gebeurtenis |
| `DurationMs` | Tekenreeks | Hoe lang het duurt om de gebeurtenis in milliseconden uit te voeren |
| `CallerIpAddress` | Tekenreeks | Een gemaskeerde IP-adres van de bron voor de gebeurtenis |
| `CorrelationId` | Guid | Door de klant ingevoerde unieke id voor de gebeurtenis |
| `ApplicationId` | Guid | Toepassings-ID gebruikt in Bearer-autorisatie |
| `Level` | Int | De ernst van de logboek registratie van de gebeurtenis |
| `Location` | Tekenreeks | De regio waar het evenement plaatsvond |
| `RequestUri` | URI | Het eind punt dat wordt gebruikt tijdens de gebeurtenis |
| `TraceId` | Tekenreeks | `TraceId`Als onderdeel van de [W3C's-tracerings context](https://www.w3.org/TR/trace-context/). De ID van de volledige tracering die wordt gebruikt om een gedistribueerde tracering over systemen op unieke wijze te identificeren. |
| `SpanId` | Tekenreeks | `SpanId` Als onderdeel van de [W3C's-tracerings context](https://www.w3.org/TR/trace-context/). De ID van deze aanvraag in de tracering. |
| `ParentId` | Tekenreeks | `ParentId` Als onderdeel van de [W3C's-tracerings context](https://www.w3.org/TR/trace-context/). Een aanvraag zonder een bovenliggende ID is de hoofdmap van de tracering. |
| `TraceFlags` | Tekenreeks | `TraceFlags` Als onderdeel van de [W3C's-tracerings context](https://www.w3.org/TR/trace-context/). Hiermee beheert u tracerings vlaggen zoals steek proeven, traceer niveau, enzovoort. |
| `TraceState` | Tekenreeks | `TraceState` Als onderdeel van de [W3C's-tracerings context](https://www.w3.org/TR/trace-context/). Aanvullende leverancierspecifieke traceer identificatie gegevens die moeten worden verdeeld over verschillende gedistribueerde tracerings systemen. |

Hieronder ziet u voor beelden van JSON-instanties voor deze typen logboeken.

#### <a name="adtdigitaltwinsoperation"></a>ADTDigitalTwinsOperation

```json
{
  "time": "2020-03-14T21:11:14.9918922Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/digitaltwins/write",
  "operationVersion": "2020-10-31",
  "category": "DigitalTwinOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": 8,
  "callerIpAddress": "13.68.244.*",
  "correlationId": "2f6a8e64-94aa-492a-bc31-16b9f0b16ab3",
  "identity": {
    "claims": {
      "appId": "872cd9fa-d31f-45e0-9eab-6e460a02d1f1"
    }
  },
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/digitaltwins/factory-58d81613-2e54-4faa-a930-d980e6e2a884?api-version=2020-10-31",
  "properties": {},
  "traceContext": {
    "traceId": "95ff77cfb300b04f80d83e64d13831e7",
    "spanId": "b630da57026dd046",
    "parentId": "9f0de6dadae85945",
    "traceFlags": "01",
    "tracestate": "k1=v1,k2=v2"
  }
}
```

#### <a name="adtmodelsoperation"></a>ADTModelsOperation

```json
{
  "time": "2020-10-29T21:12:24.2337302Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/models/write",
  "operationVersion": "2020-10-31",
  "category": "ModelsOperation",
  "resultType": "Success",
  "resultSignature": "201",
  "resultDescription": "",
  "durationMs": "80",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "9dcb71ea-bb6f-46f2-ab70-78b80db76882",
  "identity": {
    "claims": {
      "appId": "872cd9fa-d31f-45e0-9eab-6e460a02d1f1"
    }
  },
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/Models?api-version=2020-10-31",
  "properties": {},
  "traceContext": {
    "traceId": "95ff77cfb300b04f80d83e64d13831e7",
    "spanId": "b630da57026dd046",
    "parentId": "9f0de6dadae85945",
    "traceFlags": "01",
    "tracestate": "k1=v1,k2=v2"
  }
}
```

#### <a name="adtqueryoperation"></a>ADTQueryOperation

```json
{
  "time": "2020-12-04T21:11:44.1690031Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/query/action",
  "operationVersion": "2020-10-31",
  "category": "QueryOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": "314",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "1ee2b6e9-3af4-4873-8c7c-1a698b9ac334",
  "identity": {
    "claims": {
      "appId": "872cd9fa-d31f-45e0-9eab-6e460a02d1f1"
    }
  },
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/query?api-version=2020-10-31",
  "properties": {},
  "traceContext": {
    "traceId": "95ff77cfb300b04f80d83e64d13831e7",
    "spanId": "b630da57026dd046",
    "parentId": "9f0de6dadae85945",
    "traceFlags": "01",
    "tracestate": "k1=v1,k2=v2"
  }
}
```

#### <a name="adteventroutesoperation"></a>ADTEventRoutesOperation

Hier volgt een voor beeld van een JSON-hoofd tekst voor een `ADTEventRoutesOperation` die **niet** van het `Microsoft.DigitalTwins/eventroutes/action` type is (Zie de volgende sectie, [*logaritmische logboek schema's*](#egress-log-schemas)) voor meer informatie over dat schema.

```json
  {
    "time": "2020-10-30T22:18:38.0708705Z",
    "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
    "operationName": "Microsoft.DigitalTwins/eventroutes/write",
    "operationVersion": "2020-10-31",
    "category": "EventRoutesOperation",
    "resultType": "Success",
    "resultSignature": "204",
    "resultDescription": "",
    "durationMs": 42,
    "callerIpAddress": "212.100.32.*",
    "correlationId": "7f73ab45-14c0-491f-a834-0827dbbf7f8e",
    "identity": {
      "claims": {
        "appId": "872cd9fa-d31f-45e0-9eab-6e460a02d1f1"
      }
    },
    "level": "4",
    "location": "southcentralus",
    "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/EventRoutes/egressRouteForEventHub?api-version=2020-10-31",
    "properties": {},
    "traceContext": {
      "traceId": "95ff77cfb300b04f80d83e64d13831e7",
      "spanId": "b630da57026dd046",
      "parentId": "9f0de6dadae85945",
      "traceFlags": "01",
      "tracestate": "k1=v1,k2=v2"
    }
  },
```

### <a name="egress-log-schemas"></a>Uitgangs logboek schema's

Dit is het schema voor `ADTEventRoutesOperation` logboeken die specifiek zijn voor de `Microsoft.DigitalTwins/eventroutes/action` bewerkings naam. Deze bevatten informatie over uitzonde ringen en de API-bewerkingen rond eind punten die zijn verbonden met een Azure Digital Apparaatdubbels-exemplaar.

|Veldnaam | Gegevenstype | Beschrijving |
|-----|------|-------------|
| `Time` | DateTime | De datum en tijd waarop deze gebeurtenis is opgetreden, in UTC |
| `ResourceId` | Tekenreeks | De Azure Resource Manager Resource-ID voor de resource waar het evenement plaatsvond |
| `OperationName` | Tekenreeks  | Het type actie dat wordt uitgevoerd tijdens de gebeurtenis |
| `Category` | Tekenreeks | Het type bron dat wordt verzonden |
| `ResultDescription` | Tekenreeks | Aanvullende informatie over de gebeurtenis |
| `CorrelationId` | Guid | Door de klant ingevoerde unieke id voor de gebeurtenis |
| `ApplicationId` | Guid | Toepassings-ID gebruikt in Bearer-autorisatie |
| `Level` | Int | De ernst van de logboek registratie van de gebeurtenis |
| `Location` | Tekenreeks | De regio waar het evenement plaatsvond |
| `TraceId` | Tekenreeks | `TraceId`Als onderdeel van de [W3C's-tracerings context](https://www.w3.org/TR/trace-context/). De ID van de volledige tracering die wordt gebruikt om een gedistribueerde tracering over systemen op unieke wijze te identificeren. |
| `SpanId` | Tekenreeks | `SpanId` Als onderdeel van de [W3C's-tracerings context](https://www.w3.org/TR/trace-context/). De ID van deze aanvraag in de tracering. |
| `ParentId` | Tekenreeks | `ParentId` Als onderdeel van de [W3C's-tracerings context](https://www.w3.org/TR/trace-context/). Een aanvraag zonder een bovenliggende ID is de hoofdmap van de tracering. |
| `TraceFlags` | Tekenreeks | `TraceFlags` Als onderdeel van de [W3C's-tracerings context](https://www.w3.org/TR/trace-context/). Hiermee beheert u tracerings vlaggen zoals steek proeven, traceer niveau, enzovoort. |
| `TraceState` | Tekenreeks | `TraceState` Als onderdeel van de [W3C's-tracerings context](https://www.w3.org/TR/trace-context/). Aanvullende leverancierspecifieke traceer identificatie gegevens die moeten worden verdeeld over verschillende gedistribueerde tracerings systemen. |
| `EndpointName` | Tekenreeks | De naam van het uitgangs eindpunt dat is gemaakt in azure Digital Apparaatdubbels |

Hieronder ziet u voor beelden van JSON-instanties voor deze typen logboeken.

#### <a name="adteventroutesoperation-for-microsoftdigitaltwinseventroutesaction"></a>ADTEventRoutesOperation voor micro soft. DigitalTwins/eventroutes/Action

Hier volgt een voor beeld van een JSON-hoofd tekst voor een `ADTEventRoutesOperation` `Microsoft.DigitalTwins/eventroutes/action` type.

```json
{
  "time": "2020-11-05T22:18:38.0708705Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/eventroutes/action",
  "operationVersion": "",
  "category": "EventRoutesOperation",
  "resultType": "",
  "resultSignature": "",
  "resultDescription": "Unable to send EventHub message to [myPath] for event Id [f6f45831-55d0-408b-8366-058e81ca6089].",
  "durationMs": -1,
  "callerIpAddress": "",
  "correlationId": "7f73ab45-14c0-491f-a834-0827dbbf7f8e",
  "identity": {
    "claims": {
      "appId": "872cd9fa-d31f-45e0-9eab-6e460a02d1f1"
    }
  },
  "level": "4",
  "location": "southcentralus",
  "uri": "",
  "properties": {
    "endpointName": "myEventHub"
  },
  "traceContext": {
    "traceId": "95ff77cfb300b04f80d83e64d13831e7",
    "spanId": "b630da57026dd046",
    "parentId": "9f0de6dadae85945",
    "traceFlags": "01",
    "tracestate": "k1=v1,k2=v2"
  }
},
```

## <a name="view-and-query-logs"></a>Logboeken weer geven en opvragen

Eerder in dit artikel hebt u de typen logboeken geconfigureerd voor het opslaan en opgeven van de opslag locatie.

Voor het oplossen van problemen en het genereren van inzichten uit deze logboeken kunt u **aangepaste query's** genereren. Om aan de slag te gaan, kunt u ook profiteren van een aantal voor beelden van query's die u door de service hebt ontvangen, waarmee u rekening moet houden met veelgestelde vragen die klanten kunnen hebben over hun exemplaar.

Hier vindt u informatie over het uitvoeren van een query op de logboeken voor uw exemplaar.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en navigeer naar uw Azure Digital apparaatdubbels-exemplaar. U kunt het vinden door de naam ervan in de zoek balk van de portal te typen. 

2. Selecteer **Logboeken** in het menu om de pagina logboek query te openen. De pagina wordt geopend met een venster met de naam *query's*.

    :::image type="content" source="media/troubleshoot-diagnostics/logs.png" alt-text="Scherm afbeelding van de logboeken-pagina voor een Azure Digital Apparaatdubbels-exemplaar. Er wordt een query venster weer gegeven met een vooraf gedefinieerde query met de naam na verschillende logboek opties, zoals DigitalTwin API-latentie en model-API-latentie." lightbox="media/troubleshoot-diagnostics/logs.png":::

    Dit zijn vooraf ontwikkelde voorbeeld query's die zijn geschreven voor verschillende logboeken. U kunt een van de query's selecteren om deze in de query-editor te laden en uit te voeren om deze logboeken voor uw exemplaar te bekijken.

    U kunt ook het venster *query's* sluiten zonder dat u iets hoeft te doen om direct naar de pagina query-editor te gaan, waar u aangepaste query code kunt schrijven of bewerken.

3. Nadat het venster *query's* is afgesloten, ziet u de hoofd pagina van de query-editor. Hier kunt u de tekst van de voorbeeld query's weer geven en bewerken of uw eigen query's schrijven.
    :::image type="content" source="media/troubleshoot-diagnostics/logs-query.png" alt-text="Scherm afbeelding van de logboeken-pagina voor een Azure Digital Apparaatdubbels-exemplaar. Het venster Query's is verdwenen en in plaats daarvan wordt er een lijst met verschillende logboeken weer gegeven, een bewerkings venster met Bewerk bare query code en een deel venster met de geschiedenis van Query's." lightbox="media/troubleshoot-diagnostics/logs-query.png":::

    In het linkerdeel venster, 
    - Op het tabblad *tabellen* worden de verschillende Azure Digital apparaatdubbels- [logboek categorieën](#log-categories) weer gegeven die beschikbaar zijn voor gebruik in uw query's. 
    - Het tabblad *query's* bevat de voorbeeld query's die u in de editor kunt laden.
    - Op het tabblad *filter* kunt u een gefilterde weer gave van de gegevens aanpassen die de query retourneert.

Voor gedetailleerde informatie over logboek query's en hoe u deze schrijft, kunt u een [*overzicht van logboek query's in azure monitor*](../azure-monitor/logs/log-query-overview.md)bekijken.

## <a name="next-steps"></a>Volgende stappen

* Zie [*logboek gegevens verzamelen en gebruiken van uw Azure-resources*](../azure-monitor/essentials/platform-logs-overview.md)voor meer informatie over het configureren van diagnostiek.
* Zie voor meer informatie over de metrische gegevens van de Azure Digital Apparaatdubbels [*problemen oplossen: metrische gegevens weer geven met Azure monitor*](troubleshoot-metrics.md).
* Zie [*probleem oplossing: waarschuwingen instellen*](troubleshoot-alerts.md)voor meer informatie over het inschakelen van waarschuwingen voor uw metrische gegevens.