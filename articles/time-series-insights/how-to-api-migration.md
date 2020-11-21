---
title: Migreren naar nieuwe Azure Time Series Insights Gen2 API-versies | Microsoft Docs
description: Azure Time Series Insights Gen2-omgevingen bijwerken om nieuwe, algemeen beschik bare versies te gebruiken.
ms.service: time-series-insights
services: time-series-insights
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/01/2020
ms.custom: shresha
ms.openlocfilehash: 3aedbef079ba62f42ea79afdcd9995d7ee23d9fa
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020722"
---
# <a name="migrating-to-new-azure-time-series-insights-gen2-api-versions"></a>Migreren naar nieuwe Azure Time Series Insights API-versies Gen2

## <a name="overview"></a>Overzicht

Als u een Azure Time Series Insights Gen2-omgeving hebt gemaakt op het moment dat deze in open bare Preview was (vóór 16 juli 2020), moet u de omgeving van de TSI bijwerken met de nieuwe algemene beschik bare versies van Api's door de stappen te volgen die in dit artikel worden beschreven. Deze wijziging is niet van invloed op gebruikers die gebruikmaken van de gen1-versie van Azure Time Series Insights.

> [!IMPORTANT]
> Met de updates die in dit artikel worden beschreven, worden alleen de API-versies bijgewerkt die worden gebruikt in uw TSI-omgeving. Deze wijziging is niet gerelateerd aan de nieuwe [JSON afvlakking-en Escape-regels](./concepts-json-flattening-escaping-rules.md) die zijn geïntroduceerd voor Gen2-omgevingen.

De nieuwe API-versie is `2020-07-31` en gebruikt een bijgewerkte syntaxis van de [expressie time series](/rest/api/time-series-insights/reference-time-series-expression-syntax).

Gebruikers moeten de [tijdreeks model variabelen](./concepts-variables.md), opgeslagen query's, Power bi query's en aangepaste hulp middelen voor het maken van aanroepen naar de API-eind punten migreren. Als u vragen hebt of problemen hebt met betrekking tot het migratie proces, verzendt u een ondersteunings ticket via de Azure Portal en vermeldt u dit document.

> [!IMPORTANT]
> De preview API `2018-11-01-preview` -versie wordt nog steeds ondersteund tot en met 31 oktober 2020. Voltooi alle van toepassing zijnde stappen van deze migratie voordat u onderbrekingen in de service kunt voor komen.

## <a name="migrate-time-series-model-and-saved-queries"></a>Time Series model en opgeslagen query's migreren

Om gebruikers te helpen hun [Time Series-model variabelen](./concepts-variables.md) en opgeslagen query's te migreren, is er een ingebouwd hulp programma beschikbaar via de [Azure time series Insights Explorer](https://insights.timeseries.azure.com). Ga naar de omgeving die u wilt migreren en voer de onderstaande stappen uit. **U kunt de migratie gedeeltelijk volt ooien en teruggaan om deze later te volt ooien, maar geen van de updates kan worden teruggedraaid.**

> [!NOTE]
> U moet een bijdrager aan de omgeving zijn om updates te kunnen aanbrengen in het tijdreeks model en opgeslagen query's. Als u geen bijdrager bent, kunt u uw persoonlijke opgeslagen query's alleen migreren. Controleer het [toegangs beleid](./concepts-access-policies.md) voor de omgeving en uw toegangs niveau voordat u verdergaat.

1. U wordt gevraagd om de syntaxis bij te werken die wordt gebruikt door uw time series model-variabelen en opgeslagen query's.

    [![Vraag](media/api-migration/ux-prompt.png)](media/v2-update-overview/overview-one.png#lightbox)

    Als u de melding per ongeluk hebt gesloten, kunt u deze vinden in het deel venster meldingen.

1. Klik op **updates weer geven** om het migratie programma te openen.

1. Klik op **typen downloaden**. Aangezien de huidige typen door de migratie worden overschreven om de syntaxis van variabelen te wijzigen, moet u een kopie van uw huidige typen opslaan. Het hulp programma waarschuwt u wanneer er typen zijn gedownload.

    [![Download typen](media/api-migration/ux-migration-tool.png)](media/v2-update-overview/overview-one.png#lightbox)

1. Klik op **variabelen bijwerken**. Het hulp programma waarschuwt u wanneer er variabelen zijn bijgewerkt.

    > [!IMPORTANT]
    > Als u uw typen bijwerkt, moeten aangepaste toepassingen die gebruikmaken van een oudere API-versie (), `2018-11-01-preview` de nieuwe API-versie ( `2020-07-31` ) gebruiken om verder te werken. Als u niet zeker weet welke API-versie u gebruikt, neem dan contact op met uw beheerder voordat u de update uitvoert. U kunt het hulp programma voor migratie sluiten en later terugkeren naar deze stappen. Meer informatie over [het migreren van aangepaste toepassingen](#migrate-custom-applications).

    [![Variabelen bijwerken](media/api-migration/ux-migration-tool-downloaded-types.png)](media/v2-update-overview/overview-one.png#lightbox)

1. Klik op **opgeslagen Query's bijwerken**. Het hulp programma waarschuwt u wanneer opgeslagen query's zijn bijgewerkt.

    [![Opgeslagen query's bijwerken](media/api-migration/ux-migration-tool-updated-variables.png)](media/v2-update-overview/overview-one.png#lightbox)

1. Klik op **Gereed**.

    [![Migratie is voltooid](media/api-migration/ux-migration-tool-updated-saved-queries.png)](media/v2-update-overview/overview-one.png#lightbox)

Controleer de bijgewerkte omgeving door een aantal van de nieuwe variabelen en opgeslagen query's te maken. Als u een onverwacht gedrag tijdens het maken van grafieken ziet, kunt u ons feedback sturen met het feedback-hulp programma in de Explorer.

## <a name="migrate-power-bi-queries"></a>Power BI query's migreren

Als u query's hebt gegenereerd met behulp van de Power BI-connector, worden deze aanroepen naar Azure Time Series Insights met behulp van de API-preview-versie en de syntaxis van de expressie voor de oude tijd reeks. Deze query's blijven gegevens ophalen totdat de preview-API is afgeschaft.

Voor het bijwerken van de query's voor het gebruik van de nieuwe API-versie en de syntaxis van de nieuwe time series-expressie moeten query's opnieuw worden gegenereerd vanuit de Explorer. Lees meer over [het maken van query's met behulp van de Power bi-connector](./how-to-connect-power-bi.md).

> [!NOTE]
> U moet de versie van Power BI Desktop van juli 2020 gebruiken. Als dat niet het geval is, ziet u mogelijk een [Ongeldige query Payload-versie fout](./how-to-diagnose-troubleshoot.md#problem-power-bi-connector-shows-unable-to-connect).

## <a name="migrate-custom-applications"></a>Aangepaste toepassingen migreren

Als uw aangepaste toepassing aanroepen naar de volgende REST-eind punten aanroept, is het voldoende om de API-versie bij te werken naar `2020-07-31` in de URI:

- Api's voor time series model
  - Api's voor model instellingen
    - [Ophalen](/rest/api/time-series-insights/dataaccessgen2/modelsettings/get)
    - [Bijwerken](/rest/api/time-series-insights/dataaccessgen2/modelsettings/update)
  - Exemplaar-Api's
    - [Alle batch bewerkingen](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/executebatch)
    - [List](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/list)
    - [Zoeken](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/search)
    - [Voorstellen](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/suggest)
  - Hiërarchie-Api's
    - [Alle batch bewerkingen](/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies/executebatch)
    - [List](/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies/list)
  - Typen-Api's
    - [Verwijderen, bewerkingen ophalen](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch)
    - [List](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/list)

Voor de volgende REST-eind punten moet u de API-versie bijwerken naar `2020-07-31` in de URI en zorgen dat alle exemplaren van de `tsx` eigenschap de bijgewerkte syntaxis van de [expressie time series](/rest/api/time-series-insights/reference-time-series-expression-syntax)gebruiken.

- Typen-Api's
  - [Put-bewerking](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch#typesbatchput)
- Query-API's
  - [GetEvents](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents)
  - [GetSeries](/rest/api/time-series-insights/dataaccessgen2/query/execute#getseries)
  - [GetAggregateSeries](/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries)

### <a name="examples"></a>Voorbeelden

#### <a name="typesbatchput"></a>TypesBatchPut

Oude aanvraag tekst (gebruikt door `2018-11-01-preview` ):

```JSON
{
  "put": [
    {
      "id": "c1cb7a33-ed9b-4cf1-9958-f3162fed8ee8",
      "name": "OutdoorTemperatureSensor",
      "description": "This is an outdoor temperature sensor.",
      "variables": {
        "AverageTemperature": {
          "kind": "numeric",
          "value": {
            "tsx": "$event.[Temperature_Celsius].Double"
          },
          "filter": {
            "tsx": "$event.[Mode].String = 'outdoor'"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        }
      }
    }
  ]
}
```

Bijgewerkte aanvraag tekst (gebruikt door `2020-07-31` ):

```JSON
{
  "put": [
    {
      "id": "c1cb7a33-ed9b-4cf1-9958-f3162fed8ee8",
      "name": "OutdoorTemperatureSensor",
      "description": "This is an outdoor temperature sensor.",
      "variables": {
        "AverageTemperature": {
          "kind": "numeric",
          "value": {
            "tsx": "$event['Temperature_Celsius'].Double"
          },
          "filter": {
            "tsx": "$event['Mode'].String = 'outdoor'"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        }
      }
    }
  ]
}
```

U `filter` kunt de ook `$event.Mode.String = 'outdoor'` . De `value` moet de haakjes gebruiken om het speciale teken () te escapeel `_` .

#### <a name="getevents"></a>GetEvents

Oude aanvraag tekst (gebruikt door `2018-11-01-preview` ):

```JSON
{
  "getEvents": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952",
      "T1"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "filter": {
      "tsx": "($event.[Value].Double != null) OR ($event.[Status].String = 'Good')"
    },
    "projectedProperties": [
      {
        "name": "Temperature",
        "type": "Double"
      }
    ]
  }
}
```

Bijgewerkte aanvraag tekst (gebruikt door `2020-07-31` ):

```JSON
{
  "getEvents": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952",
      "T1"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "filter": {
      "tsx": "($event.Value.Double != null) OR ($event.Status.String = 'Good')"
    },
    "projectedProperties": [
      {
        "name": "Temperature",
        "type": "Double"
      }
    ]
  }
}
```

U `filter` kunt de ook `($event['Value'].Double != null) OR ($event['Status'].String = 'Good')` .

#### <a name="getseries"></a>GetSeries

Oude aanvraag tekst (gebruikt door `2018-11-01-preview` ):

```JSON
{
  "getSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "inlineVariables": {
      "pressure": {
        "kind": "numeric",
        "value": {
          "tsx": "$event.[Bar-Pressure-Offset]"
        },
        "aggregation": {
          "tsx": "avg($value)"
        }
      }
    },
    "projectedVariables": [
      "pressure"
    ]
  }
}
```

Bijgewerkte aanvraag tekst (gebruikt door `2020-07-31` ):

```JSON
{
  "getSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "inlineVariables": {
      "pressure": {
        "kind": "numeric",
        "value": {
          "tsx": "$event['Bar-Pressure-Offset']"
        },
        "aggregation": {
          "tsx": "avg($value)"
        }
      }
    },
    "projectedVariables": [
      "pressure"
    ]
  }
}
```

U `value` kunt de ook `$event['Bar-Pressure-Offset'].Double` . Als er geen gegevens type is opgegeven, wordt ervan uitgegaan dat het gegevens type Double is. De haakjes notatie moet worden gebruikt om het speciale teken () te escapeel `-` .

#### <a name="aggregateseries"></a>AggregateSeries

Oude aanvraag tekst (gebruikt door `2018-11-01-preview` ):

```JSON
{
  "aggregateSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "interval": "PT1M",
    "inlineVariables": {
      "MinTemperature": {
        "kind": "numeric",
        "value": {
          "tsx": "coalesce($event.[Temp].Double, toDouble($event.[Temp].Long))"
        },
        "aggregation": {
          "tsx": "min($value)"
        }
      },
    },
    "projectedVariables": [
      "MinTemperature"
    ]
  }
}
```

Bijgewerkte aanvraag tekst (gebruikt door `2020-07-31` ):

```JSON
  "aggregateSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "interval": "PT1M",
    "inlineVariables": {
      "MinTemperature": {
        "kind": "numeric",
        "value": {
          "tsx": "coalesce($event.Temp.Double, toDouble($event.Temp.Long))"
        },
        "aggregation": {
          "tsx": "min($value)"
        }
      },
    },
    "projectedVariables": [
      "MinTemperature"
    ]
  }
}
```

U `value` kunt de ook `coalesce($event['Temp'].Double, toDouble($event['Temp'].Long))` .

### <a name="potential-errors"></a>Mogelijke fouten

#### <a name="invalidinput"></a>InvalidInput

Als u de volgende fout ziet, gebruikt u de nieuwe API-versie ( `2020-07-31` ), maar de TSX-syntaxis is niet bijgewerkt. Controleer de syntaxis van de expressie voor de [tijd reeks](/rest/api/time-series-insights/reference-time-series-expression-syntax) en de bovenstaande migratie voorbeelden. Zorg ervoor dat alle `tsx` eigenschappen correct zijn bijgewerkt voordat u de API-aanvraag opnieuw verzendt.

```JSON
{
    "error": {
        "code": "InvalidInput",
        "message": "Unable to parse 'value' time series expression (TSX) in variable 'Temperature'.",
        "target": "projectedVariables.Temperature.value",
        "innerError": {
            "parseErrorDetails": [
                {
                    "pos": [
                        0,
                        5
                    ],
                    "line": 1,
                    "msg": "Unsupported Time Series Expression version TSX01 used instead of TSX00.",
                    "code": "UnsupportedTSXVersionTSX01",
                    "target": "$event"
                }
            ],
            "code": "TsxParseError"
        }
    }
}
```

## <a name="next-steps"></a>Volgende stappen

- Test uw omgeving via de [Azure time series Insights Explorer](./concepts-ux-panels.md) of via uw aangepaste toepassing.