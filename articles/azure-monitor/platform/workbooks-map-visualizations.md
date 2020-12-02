---
title: Werkblad kaart visualisaties Azure Monitor
description: Meer informatie over Azure Monitor werkmap kaart visualisaties.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: lagayhar
ms.openlocfilehash: 9b79efeeb90627bee6096c9142d8180896150295
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96439672"
---
# <a name="map-visualization"></a>Kaartvisualisatie

De kaart visualisatie hulp middelen in de vorm van problemen met het toewijzen van fouten in bepaalde regio's en het weer geven van geaggregeerde weer gaven op hoog niveau van de bewakings gegevens door de mogelijkheid te bieden om alle gegevens te verzamelen die aan elke locatie/elk land of elke regio zijn toegewezen.

In de onderstaande scherm afbeelding ziet u het totale aantal trans acties en de end-to-end-latentie voor verschillende opslag accounts. Hier wordt de grootte bepaald aan de hand van het totale aantal trans acties en de metrische kleur waarden onder de kaart geven de end-to-end latentie weer. Na het eerste waarnemings punt is het aantal trans acties in de regio **VS-West** klein, vergeleken met de regio **VS-Oost** , maar de end-to-end-LATENTIE voor de regio VS- **West** is hoger dan de regio **VS-Oost** . Dit geeft het eerste inzicht dat er een amiss is voor de **VS-West**.

![Scherm afbeelding van de Azure-locatie toewijzing](./media/workbooks-map-visualizations/map-performance-example.png)

## <a name="adding-a-map"></a>Een kaart toevoegen

De kaart kan worden gevisualiseerd als de onderliggende gegevens/meet waarden gegevens over breedte graad/lengte graad, Azure-resource gegevens, Azure-locatie gegevens of land/regio, naam of land/regio-code hebben.

### <a name="using-azure-location"></a>Azure-locatie gebruiken

1. Schakel de werkmap over naar de bewerkings modus door op het item werkbalk opdracht **bewerken** te klikken.
2. Selecteer **toevoegen** en vervolgens *query toevoegen*.
3. Wijzig de *gegevens bron* en `Azure Resource Graph` Kies vervolgens een abonnement met een opslag account.
4. Voer de onderstaande query in voor uw analyse en selecteer **query uitvoeren**.

    ```kusto
    where  type =~ 'microsoft.storage/storageaccounts'
    | summarize count() by location
    ```

5. Stel *grootte* in op `Large` .
6. Stel de *visualisatie* in op `Map` .
7. Alle instellingen worden automatisch ingevuld. Voor aangepaste instellingen selecteert u de knop **kaart instellingen** om het deel venster instellingen te openen.
8. Hieronder ziet u een scherm opname van de kaart visualisatie waarin opslag accounts voor elke Azure-regio voor het geselecteerde abonnement worden weer gegeven.

![Scherm afbeelding van de Azure-locatie toewijzing met de bovenstaande query](./media/workbooks-map-visualizations/map-azure-location-example.png)

### <a name="using-azure-resource"></a>Azure-Resource gebruiken

1. Schakel de werkmap over naar de bewerkings modus door op het item werkbalk opdracht **bewerken** te klikken.
2. Selecteer **toevoegen** en vervolgens *metrische gegevens toevoegen*.
3. Gebruik een abonnement met opslag accounts.
4. Wijzig het *resource type* in `storage account` en in *resource* Selecteer meerdere opslag accounts.
5. Selecteer **metrische gegevens toevoegen** en een trans actie-metriek toevoegen.
    1. Naamruimte: `Account`
    2. Gemeten `Transactions`
    3. Aggregatie `Sum`
    
    ![Scherm opname van metrische gegevens van trans actie](./media/workbooks-map-visualizations/map-transaction-metric.png)
1. Selecteer **metrische gegevens toevoegen** en een geslaagde latentie E2E toevoegen.
    1. Naamruimte: `Account`
    1. Gemeten `Success E2E Latency`
    1. Aggregatie `Average`
    
    ![Scherm opname van de waarde voor end-to-end-latentie van geslaagde pogingen](./media/workbooks-map-visualizations/map-e2e-latency-metric.png)
1. Stel *grootte* in op `Large` .
1. Stel de grootte van de *visualisatie* in op `Map` .
1. Stel in de **kaart instellingen** de volgende instellingen in:
    1. Locatie-informatie met: `Azure Resource`
    1. Azure-Resource veld: `Name`
    1. Grootte op: `microsoft.storage/storageaccounts-Transaction-Transactions`
    1. Aggregatie voor locatie: `Sum of values`
    1. Kleur type: `Heatmap`
    1. Kleur: `microsoft.storage/storageaccounts-Transaction-SuccessE2ELatency`
    1. Aggregatie voor kleur: `Sum of values`
    1. Kleuren palet: `Green to Red`
    1. Minimum waarde: `0`
    1. Metrische waarde: `microsoft.storage/storageaccounts-Transaction-SuccessE2ELatency`
    1. Statistische gegevens samen voegen op basis van: `Sum of values`
    1. Het vak **aangepaste opmaak** selecteren
    1. Teleenheid `Milliseconds`
    1. Stijlen `Decimal`
    1. Maximum aantal cijfers: `2`

### <a name="using-countryregion"></a>Land/regio gebruiken

1. Schakel de werkmap over naar de bewerkings modus door op het item werkbalk opdracht **bewerken** te klikken.
2. Selecteer **toevoegen* en vervolgens *query toevoegen*.
3. Wijzig de *gegevens bron* in `Log` .
4. Selecteer *resource type* als `Application Insights` en kies vervolgens een Application Insights resource met page views-gegevens.
5. Gebruik de query-editor om de KQL voor uw analyse in te voeren en selecteer **query uitvoeren**.

    ```kusto
    pageViews
    | project duration, itemCount, client_CountryOrRegion
    | limit 20
    ```

6. Stel de grootte waarden in op `Large` .
7. Stel de visualisatie in op `Map` .
8. Alle instellingen worden automatisch ingevuld. Selecteer **kaart instellingen** voor aangepaste instellingen.

### <a name="using-latitudelocation"></a>Latitude/location gebruiken

1. Schakel de werkmap over naar de bewerkings modus door op het item werkbalk opdracht **bewerken** te klikken.
2. Selecteer **toevoegen* en vervolgens *query toevoegen*.
3. Wijzig de *gegevens bron* in `JSON` .
1. Voer de JSON-gegevens hieronder in de query-editor in en selecteer **query uitvoeren**.
1. Stel de *grootte* waarden in op `Large` .
1. Stel de *visualisatie* in op `Map` .
1. Stel in **instellingen toewijzen** onder metrische instellingen de optie *metrische label* in op `displayName` vervolgens **opslaan en sluiten**.

De kaart visualisatie hieronder toont gebruikers voor elke locatie van de breedte-en lengte graad met het geselecteerde label voor metrische gegevens.

![Scherm opname van een kaart visualisatie waarin gebruikers voor elke geografische breedte en lengte graad worden weer gegeven met het geselecteerde label voor metrische gegevens](./media/workbooks-map-visualizations/map-latitude-longitude-example.png)

## <a name="map-settings"></a>Kaart instellingen

### <a name="layout-settings"></a>Indelings instellingen

| Instelling | Uitleg |
|:-------------|:-------------|
| `Location info using` | Selecteer een manier om de locatie op te halen van items die op de kaart worden weer gegeven. <br> **Breedte graad/lengte graad**: Selecteer deze optie als er kolommen zijn met informatie over de breedte graad en lengte graad. Elke rij met gegevens over de breedte graad en lengte graad wordt weer gegeven als een afzonderlijk item op de kaart. <br> **Azure-locatie**: Selecteer deze optie als er sprake is van een kolom met informatie over de Azure-locatie (Oost-, Europa West-, centralindia-, enz.). Geef die kolom op en haalt de bijbehorende breedte-en lengte graad op voor elke Azure-locatie, groeperen van dezelfde locatie rijen (op basis van de aggregatie opgegeven) samen om de locaties op de kaart weer te geven. <br> **Azure-resource**: Selecteer deze optie als er een kolom is met Azure-resource gegevens (opslag account, cosmosdb-account, enzovoort). Geef deze kolom op en haalt de bijbehorende breedte-en breedte graad op voor elke Azure-resource. Groepeer dezelfde locatie (Azure Location) rijen (op basis van de aggregatie opgegeven) samen om de locaties op de kaart weer te geven. <br> **Land/regio**: Selecteer deze optie als er een kolom is met naam/code van land/regio (vs, Verenigde Staten, in, India, CN, China). Geef die kolom op en haalt de bijbehorende breedte-en breedte graad voor elk land/regio/code op en groepeert de rijen samen met dezelfde Country-Region code/land-regio naam om de locaties op de kaart weer te geven. Land naam en land code worden niet als één entiteit gegroepeerd op de kaart.
| `Latitude/Longitude` | Deze twee opties worden weer gegeven als locatie gegevens veld waarde is: breedte graad/lengte graad. Selecteer de kolom met een breedte graad in het veld met de breedte graad en lengte graad in het veld lengte graad. |
| `Azure location field` | Deze optie wordt weer gegeven als locatie gegevens veld waarde is: Azure-locatie. Selecteer de kolom die de locatie gegevens van Azure heeft. |
| `Azure resource field` | Deze optie wordt weer gegeven als locatie gegevens veld waarde is: Azure resource. Selecteer de kolom die de informatie over de Azure-resource heeft. |
| `Country/Region field` | Deze optie wordt weer gegeven als locatie-informatie veld waarde is: land of regio. Selecteer de kolom die de land-of regio gegevens heeft. |
| `Size by` | Met deze optie bepaalt u de grootte van de items die op de kaart worden weer gegeven. Grootte is afhankelijk van de waarde in de kolom die is opgegeven door de gebruiker. Op dit moment is de RADIUS van de cirkel direct evenredig met de vierkantswortel van de waarde van de kolom. Als ' geen... ' is geselecteerd, worden in alle cirkels de standaard regio grootte weer gegeven.|
| `Aggregation for location` | Dit veld geeft aan hoe de grootte moet worden geaggregeerd **op** kolommen met dezelfde Azure-locatie/Azure-resource/land-regio. |
| `Minimum region size` | In dit veld wordt aangegeven wat de minimale RADIUS is van het item dat wordt weer gegeven op de kaart. Dit wordt gebruikt wanneer er sprake is van een aanzienlijk verschil tussen de waarden van de kolom grootte, waardoor kleinere items niet zichtbaar zijn op de kaart. |
| `Maximum region size` | Dit veld geeft aan wat de maximale RADIUS is van het item dat wordt weer gegeven op de kaart. Dit wordt gebruikt wanneer de waarden van de grootte op kolom extreem groot zijn en ze een enorme Opper vlakte van de kaart bedekken.|
| `Default region size` | Dit veld geeft aan wat de standaard RADIUS is van het item dat wordt weer gegeven op de kaart. De standaard RADIUS wordt gebruikt wanneer de kolom grootte op ' geen... ' is. of de waarde is 0.|
| `Minimum value` | De minimum waarde die wordt gebruikt voor het berekenen van de regio grootte. Als dat niet is opgegeven, is de minimum waarde de kleinste waarde na aggregatie. |
| `Maximum value` | De maximum waarde die wordt gebruikt voor het berekenen van de regio grootte. Als dat niet is opgegeven, is de maximum waarde de grootste waarde na aggregatie.|
| `Opacity of items on Map` | In dit veld wordt aangegeven hoe transparant de items zijn die worden weer gegeven op de kaart. Dekking van 1 betekent, geen transparantie, waarbij de dekking 0 betekent dat items niet zichtbaar zijn op de kaart. Als er te veel items op de kaart staan, kan de dekking worden ingesteld op een lage waarde zodat alle overlappende items zichtbaar zijn.|

### <a name="color-settings"></a>Kleur instellingen

| Kleur type | Uitleg |
|:------------- |:-------------|
| `None` | Alle knoop punten hebben dezelfde kleur. |
| `Thresholds` | In dit type worden celkleur ingesteld op basis van drempel waarden (bijvoorbeeld _CPU > 90% => rood, 60% > CPU > 90% => geel, cpu < 60% => groen_). <ul><li> **Color by**: de waarde van deze kolom wordt gebruikt door drempel waarden/heatmap Logic.</li> <li>**Aggregatie voor kleur: in** dit veld wordt aangegeven hoe u de kleur kunt samen voegen **met** kolommen met dezelfde Azure-locatie/Azure-resource/land-regio. </li> <ul> |
| `Heatmap` | In dit type worden de cellen gekleurd op basis van het kleuren palet en de kleur op veld. Dit heeft ook dezelfde **kleur door** en **aggregatie voor kleur** opties als drempel waarden. |

### <a name="metric-settings"></a>Instellingen voor metrische gegevens
| Instelling | Uitleg |
|:------------- |:-------------|
| `Metric Label` | Deze optie wordt weer gegeven als locatie-informatie veld waarde is: breedte graad/lengte graad. Met deze functie kan de gebruiker het label kiezen dat moet worden weer gegeven voor metrische gegevens die onder de kaart worden weer gegeven. |
| `Metric Value` | In dit veld wordt de metrische waarde opgegeven die onder de kaart moet worden weer gegeven. |
| `Create 'Others' group after` | In dit veld wordt de limiet opgegeven voordat een groep ' anderen ' wordt gemaakt. |
| `Aggregate 'Others' metrics by` | Dit veld geeft de aggregatie aan die wordt gebruikt voor de groep ' overige ' als deze wordt weer gegeven. |
| `Custom formatting` | Gebruik dit veld om eenheden, stijl en opmaak opties voor nummer waarden in te stellen. Dit komt overeen met [de aangepaste opmaak van het raster](workbooks-grid-visualizations.md#custom-formatting).|

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het maken [van honing kam-visualisaties in werkmappen](workbooks-honey-comb.md).