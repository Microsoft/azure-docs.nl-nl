---
title: Time Series-model-Azure Time Series Insights Gen2 | Microsoft Docs
description: Meer informatie over time series model in Azure Time Series Insights Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/22/2021
ms.custom: seodec18
ms.openlocfilehash: 37c24f2d785bbdd9847e0dadaa47969f5b090ef2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101698090"
---
# <a name="time-series-model-in-azure-time-series-insights-gen2"></a>Time Series-model in Azure Time Series Insights Gen2

In dit artikel worden de tijdreeks modellen, de mogelijkheden en het maken en bijwerken van uw eigen modellen in de Azure Time Series Insights Gen2 omgeving beschreven.

> [!TIP]
>
> * Ga naar de [Contoso wikkeling-Farm demo](https://insights.timeseries.azure.com/preview/samples) omgeving voor een live time series model-voor beeld.
> * Meer informatie [over het werken met een time series-model](./how-to-edit-your-model.md) met behulp van de Azure time series Insights Explorer.

## <a name="summary"></a>Samenvatting

De gegevens die op IoT-apparaten worden verzameld, hebben doorgaans geen contextuele informatie, waardoor het lastig is om Sens oren snel te vinden en te analyseren. De belangrijkste motivatie voor time series-model is het vereenvoudigen van het vinden en analyseren van IoT-of time series-gegevens. Deze doel stelling wordt gerealiseerd door het inschakelen, onderhouds-en verrijken van tijdreeks gegevens in te scha kelen om voor bereide gegevens sets voor analyse te maken.

## <a name="scenario-contosos-new-smart-oven"></a>Scenario: de nieuwe Smart oven van contoso

**Bekijk het fictieve scenario van een slimme oven van contoso.** In dit scenario wordt ervan uitgesteld dat elke contoso-Smart oven vijf temperatuur Sens oren heeft, één voor elk van de vier meest meest gebranders en één voor de oven zelf. Tot onlangs is elke contoso-temperatuur sensor de gegevens afzonderlijk verzonden, opgeslagen en gevisualiseerd. Voor de bewaking van de keuken apparaten is contoso afhankelijk van eenvoudige grafieken, één voor elke afzonderlijke sensor.

Hoewel contoso tevreden was met de oorspronkelijke gegevens-en visualisatie oplossing, zijn er enkele beperkingen zichtbaar:

* Klanten wilden weten hoe heet de algehele oven wanneer het meren deel van de meeste branders was ingeschakeld. Contoso had problemen met het analyseren en presen teren van een uniform antwoord over de voor waarden van de algemene oven.
* Contoso-technici wilden controleren of de meest voorkomende branders die gelijktijdig worden uitgevoerd, niet kunnen leiden tot een inefficiënte stroom teken. Er is een probleem opgetreden bij het kruisen van de Tempe ratuur en spannings Sens oren en het vinden daarvan in de Store.
* Het contoso Quality Assurance-team wilde de geschiedenis tussen twee sensor versies controleren en vergelijken. Er is een probleem opgetreden bij het bepalen van de gegevens van de sensor versie.

Zonder de mogelijkheid om het time series-model van de overkoepelend-Smart oven te structureren, te organiseren en te definiëren, wordt elke temperatuur sensor die niet gevonden, geïsoleerd en minder informatieve gegevens punten bevindt, bewaard. Het is lastiger om deze gegevens punten in te scha kelen op inzichten omdat elke gegevensset onafhankelijk van de andere wordt bewaard.

Deze beperkingen onthulden het belang van slimme gegevens aggregatie en visualisatie tools om de nieuwe oven van Contoso te begeleiden:

* Gegevens visualisatie is handig wanneer u gegevens in een handige weer gave kunt koppelen en combi neren. Er wordt een voor beeld weer gegeven met spannings sensoren en temperatuur sensors.
* Het beheren van multidimensionale gegevens voor verschillende entiteiten, samen met de functies voor vergelijking, zoomen en tijds bereik kan moeilijk zijn.

**Time Series-model biedt een handige oplossing** voor veel van de scenario's die in dit fictieve voor beeld zijn aangetroffen:

[![Voor beeld van een Smart oven voor time series model](media/v2-update-tsm/time-series-model-smart-oven.png)](media/v2-update-tsm/time-series-model-smart-oven.png#lightbox)

* Time Series-model speelt een cruciale rol in query's en navigatie, omdat deze gegevens contextualizes, doordat er vergelijkingen kunnen worden gemaakt over Peri Oden en tussen sensor-en apparaattypen. (**A**)
* Gegevens worden nader gecontextd, omdat de gegevens die in het tijdreeks model worden bewaard, de tijdreeks query berekeningen als variabelen behouden en ze opnieuw gebruiken op het moment van de query.
* Met de time series-model worden gegevens ingedeeld en geaggregeerd voor verbeterde visualisatie-en beheer mogelijkheden. (**B**)

### <a name="key-capabilities"></a>Belangrijkste mogelijkheden

Met het doel om eenvoudig en moeiteloos tijd Series contextualization te beheren, maakt time series model de volgende mogelijkheden in Azure Time Series Insights Gen2. U kunt het volgende doen:

* Berekenings-of formules ontwerpen en beheren met scalaire functies, aggregatie bewerkingen, enzovoort.
* Definieer de relaties tussen bovenliggende en onderliggende items om navigatie, zoek acties en verwijzingen in te scha kelen.
* Definieer eigenschappen die gekoppeld zijn aan de instanties, gedefinieerd als *exemplaar velden*, en gebruik deze om hiërarchieën te maken.

### <a name="components"></a>Onderdelen

Het Time Series-model heeft drie kernonderdelen:

* [Exemplaren van de time series-model](#time-series-model-instances)
* [Time Series-model hiërarchieën](#time-series-model-hierarchies)
* [Typen Time Series-modellen](#time-series-model-types)

Deze onderdelen worden gecombineerd om een time series-model op te geven en om uw gegevens te organiseren.

[![Overzichts grafiek voor tijdreeks model](media/v2-update-tsm/time-series-model-overview.png)](media/v2-update-tsm/time-series-model-overview.png#lightbox)

Een time series-model kan worden gemaakt en beheerd via de [Azure time series Insights Explorer](./concepts-ux-panels.md). Instellingen voor tijdreeks modellen kunnen worden beheerd via de [API voor model instellingen](/rest/api/time-series-insights/reference-model-apis).

## <a name="time-series-model-instances"></a>Exemplaren van de time series-model

De time series-model *instanties* zijn virtuele voors tellingen van de tijd reeks zelf.

In de meeste gevallen worden instanties uniek geïdentificeerd door **deviceId** of **assetId**, die worden opgeslagen als tijd reeks-id's.

Instanties bevatten beschrijvende informatie die betrekking heeft op *instantie-eigenschappen*, zoals een tijd reeks-id, type, naam, beschrijving, hiërarchieën en instantie velden. Instantie-eigenschappen bevatten ten minste hiërarchie-informatie.

*Exemplaar velden* zijn een verzameling beschrijvende gegevens die waarden voor hiërarchie niveaus kunnen bevatten, evenals de fabrikant, operator, enzovoort.

Nadat een gebeurtenis bron is geconfigureerd voor de Azure Time Series Insights Gen2-omgeving, worden exemplaren automatisch gedetecteerd en gemaakt in een time series-model. De exemplaren kunnen via de Azure Time Series Insights Explorer worden gemaakt of bijgewerkt door gebruik te maken van Time Series-model query's.

De [demo](https://insights.timeseries.azure.com/preview/samples) van de contoso-Farm bevat enkele voor beelden van Live instances.

[![Voor beeld van een exemplaar van het time series-model](media/v2-update-tsm/time-series-model-instance.png)](media/v2-update-tsm/time-series-model-instance.png#lightbox)

### <a name="instance-properties"></a>Instantie-eigenschappen

Instanties worden gedefinieerd door **timeSeriesId**, **typeId**, **name**, **Description**, **hierarchyIds** en **instanceFields**. Elk exemplaar wordt toegewezen aan slechts één *type* en een of meer *hiërarchieën*.

| Eigenschap | Beschrijving |
| --- | ---|
| timeSeriesId | De unieke ID van de tijd reeks waaraan het exemplaar is gekoppeld. In de meeste gevallen worden instanties uniek geïdentificeerd door een eigenschap zoals deviceId of assetId. In sommige gevallen kan een specifiek samengestelde ID worden gebruikt die Maxi maal drie eigenschappen combineert. |
| typeId | De hoofdletter gevoelige unieke teken reeks-ID van het tijds reeks model waaraan het exemplaar is gekoppeld. Alle gedetecteerde nieuwe instanties worden standaard gekoppeld aan een standaard type.
| naam | De eigenschap **name** is optioneel en hoofdletter gevoelig. Als de **naam** niet beschikbaar is, wordt standaard **timeSeriesId**. Als er een naam wordt gegeven, is **timeSeriesId** nog steeds beschikbaar [.](./concepts-ux-panels.md#4-time-series-well) |
| beschrijving | Een tekst beschrijving van het exemplaar. |
| hierarchyIds | Hiermee definieert u de hiërarchieën waarvan het exemplaar deel uitmaakt. |
| instanceFields | De eigenschappen van een exemplaar en alle statische gegevens waarmee een exemplaar wordt gedefinieerd. Ze definiëren waarden van hiërarchie-of niet-hiërarchie-eigenschappen, terwijl indexeren ook ondersteuning biedt voor het uitvoeren van zoek bewerkingen. |

> [!NOTE]
> Hiërarchieën worden gebouwd met behulp van exemplaar velden. Aanvullende **instanceFields** kunnen worden toegevoegd voor de verdere definities van instantie-eigenschappen.

Exemplaren hebben de volgende JSON-weer gave:

```JSON
{
  "timeSeriesId": ["PU2"],
  "typeId": "545314a5-7166-4b90-abb9-fd93966fa39b",
  "hierarchyIds": ["95f0a8d1-a3ef-4549-b4b3-f138856b3a12"],
  "description": "Pump #2",
  "instanceFields": {
    "Location": "Redmond",
    "Fleet": "Fleet 5",
    "Unit": "Pump Unit 3",
    "Manufacturer": "Contoso",
    "ScalePres": "0.54",
    "scaleTemp": "0.54"
  }
}
```

> [!TIP]
> Lees het artikel over het [uitvoeren van gegevens query's](./concepts-query-overview.md#time-series-model-query-tsm-q-apis) en de [API-documentatie](/rest/api/time-series-insights/reference-model-apis#instances-api)van het exemplaar van API voor instance-api's maken, lezen, bijwerken en verwijderen (ruw).

## <a name="time-series-model-hierarchies"></a>Time Series-model hiërarchieën

Met *hiërarchieën* voor time series-modellen worden instanties geordend door eigenschapnamen en hun relaties op te geven.

U kunt meerdere hiërarchieën configureren in een bepaalde Azure Time Series Insights Gen2-omgeving. Een exemplaar van een time series-model kan worden toegewezen aan een enkele of meerdere hiërarchieën (veel-op-veel-relatie).

In de demo van de [Contoso-Wind Farm](https://insights.timeseries.azure.com/preview/samples) wordt een standaard exemplaar en een type hiërarchie weer gegeven.

[![Voor beeld van hiërarchie van Time Series-model](media/v2-update-tsm/time-series-model-hierarchies.png)](media/v2-update-tsm/time-series-model-hierarchies.png#lightbox)

### <a name="hierarchy-definition"></a>Hiërarchie definitie

Hiërarchieën worden gedefinieerd door de hiërarchie **-id**, **naam** en **bron**.

| Eigenschap | Beschrijving |
| ---| ---|
| id | De unieke id voor de hiërarchie die wordt gebruikt, bijvoorbeeld wanneer u een exemplaar definieert. |
| naam | Een teken reeks die wordt gebruikt om een naam op te geven voor de hiërarchie. |
| source | Hiermee geeft u de organisatie hiërarchie of het pad op. Dit is een bovenliggende en onderliggende volg orde van de hiërarchie die gebruikers willen maken. De bovenliggende/onderliggende eigenschappen van het toewijzings exemplaar velden. |

Hiërarchieën worden weer gegeven in JSON als:

```JSON
{
  "hierarchies": [
    {
      "id": "6e292e54-9a26-4be1-9034-607d71492707",
      "name": "Location",
      "source": {
        "instanceFieldNames": [
          "state",
          "city"
        ]
      }
    },
    {
      "id": "a28fd14c-6b98-4ab5-9301-3840f142d30e",
      "name": "ManufactureDate",
      "source": {
        "instanceFieldNames": [
          "year",
          "month"
        ]
      }
    }
  ]
}
```

In het vorige voor beeld van JSON:

* `Location` Hiermee definieert u een hiërarchie met bovenliggend `states` en onderliggend item `cities` . Elk `location` kan meerdere hebben `states` , die op zijn beurt meerdere kunnen bevatten `cities` .
* `ManufactureDate` Hiermee definieert u een hiërarchie met bovenliggend `year` en onderliggend item `month` . Elk `ManufactureDate` kan meerdere hebben `years` , die op zijn beurt meerdere kunnen bevatten `months` .

> [!TIP]
> Lees voor de hiërarchie-API maken, lezen, bijwerken en verwijderen (ruwe) ondersteuning het artikel [gegevens query's](concepts-query-overview.md#time-series-model-query-tsm-q-apis) en de documentatie van de [hiërarchie-API](/rest/api/time-series-insights/reference-model-apis#hierarchies-api).

### <a name="hierarchy-example"></a>Voorbeeld van een hiërarchie

Bekijk een voor beeld waarin  hiërarchie H1 `building` , `floor` en `room` als onderdeel van de **instanceFieldNames** -definitie:

```JSON
{
  "id": "aaaaaa-bbbbb-ccccc-ddddd-111111",
  "name": "H1",
  "source": {
    "instanceFieldNames": [
      "building",
      "floor",
      "room"
    ]
  }
}
```

Op basis van de instantie velden die worden gebruikt in de vorige definitie en een aantal Peri Series, worden de hiërarchie kenmerken en-waarden weer gegeven, zoals wordt weer gegeven in de volgende tabel:

| Time Series-ID | Exemplaar velden |
| --- | --- |
| ID1 | ' buil ding ' = ' 1000 ', ' Floor ' = ' 10 ', ' room ' = ' 55 '  |
| ID2 | "buil ding" = "1000", "room" = "55" |
| ID3 | ' Floor ' = ' 10 ' |
| ID4 | ' buil ding ' = ' 1000 ', ' Floor ' = ' 10 '  |
| ID5 | Geen: ' buil ding ', ' Floor ' of ' room ' is ingesteld. |

Time Series **id1** en **ID4** worden weer gegeven als onderdeel van de hiërarchie **H1** in de [Azure time series Insights Explorer](./concepts-ux-panels.md) , omdat ze volledig gedefinieerde en correct bestelde *bouw*-, *vloer*-en *room* -para meters hebben gedefinieerd.

De andere zijn ingedeeld onder niet- *bovenliggende instanties* , omdat ze niet voldoen aan de opgegeven gegevens hiërarchie.

## <a name="time-series-model-types"></a>Typen Time Series-modellen

Met de *typen* tijd reeks modellen kunt u variabelen of formules voor het uitvoeren van berekeningen definiëren. Typen zijn gekoppeld aan een specifiek exemplaar.

Een type kan een of meer variabelen hebben. Een exemplaar van een time series-model kan bijvoorbeeld van het type *temperatuur sensor* zijn, dat bestaat uit de *gemiddelde Tempe ratuur*, *minimale Tempe ratuur* en de *maximale Tempe ratuur*.

In de demo van de [Contoso wikkeling-Farm](https://insights.timeseries.azure.com/preview/samples) worden verschillende typen Time Series-modellen gevisualiseerd die aan hun respectieve instanties zijn gekoppeld.

[![Voor beeld van type time series-model](media/v2-update-tsm/time-series-model-types.png)](media/v2-update-tsm/time-series-model-types.png#lightbox)

> [!TIP]
> Lees voor de typen API maken, lezen, bijwerken en verwijderen (ruwe) ondersteuning, het [gegevens query](concepts-query-overview.md#time-series-model-query-tsm-q-apis) -artikel en de [type API rest-documentatie](/rest/api/time-series-insights/reference-model-apis#types-api).

### <a name="type-properties"></a>Type-eigenschappen

De typen van de tijd reeks model worden gedefinieerd op basis van **id**, **naam**, **Beschrijving** en **variabelen**.

| Eigenschap | Beschrijving |
| ---| ---|
| id | De hoofdletter gevoelige unieke teken reeks-ID voor het type. |
| naam | Een teken reeks die wordt gebruikt om een naam op te geven voor het type. |
| beschrijving | Een beschrijving van de teken reeks voor het type. |
| variabelen | Geef de variabelen op die zijn gekoppeld aan het type. |

Typen voldoen aan het volgende JSON-voor beeld:

```JSON
{
  "types": [
    {
      "id": "1be09af9-f089-4d6b-9f0b-48018b5f7393",
      "name": "DefaultType",
      "description": "Default type",
      "variables": {
        "EventCount": {
          "kind": "aggregate",
          "value": null,
          "filter": null,
          "aggregation": {
            "tsx": "count()"
          }
        },
        "Interpolated Speed": {
          "kind": "numeric",
          "value": {
              "tsx": "$event['Speed-Sensor'].Double"
          },
          "filter": null,
          "interpolation": {
              "kind": "step",
              "boundary": {
                  "span": "P1D"
              }
          },
          "aggregation": {
              "tsx": "right($value)"
          }
        }
      }
    }
  ]
}
```

De tijdreeks model typen kunnen veel variabelen hebben die formule-en verwerkings regels op gebeurtenissen opgeven. Meer informatie over [het definiëren van tijd reeks model variabelen](./concepts-variables.md)

## <a name="next-steps"></a>Volgende stappen

* Lees de referentie documentatie voor [Time Series model](/rest/api/time-series-insights/reference-model-apis) voor meer informatie over het bewerken van het model via api's.

* Verken de formules en berekeningen die u kunt maken met [Time Series model-variabelen](./concepts-variables.md)

* Meer informatie over het [opvragen van gegevens](concepts-query-overview.md) in azure time series Insights Gen2