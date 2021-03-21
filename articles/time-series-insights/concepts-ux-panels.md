---
title: Gegevens visualiseren in de Time Series Insights Explorer-Azure Time Series Insights Gen2 | Microsoft Docs
description: Meer informatie over de functies en opties die beschikbaar zijn in de Azure Time Series Insights Explorer.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/22/2021
ms.custom: seodec18
ms.openlocfilehash: 77c6e8790451ef830b37cc5914ce9de8e92174f5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103464085"
---
# <a name="azure-time-series-insights-explorer"></a>Azure Time Series Insights Explorer

In dit artikel worden de verschillende functies en opties beschreven die beschikbaar zijn in de Azure Time Series Insights Gen2- [demo omgeving](https://insights.timeseries.azure.com/preview/demo).

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan met de Azure Time Series Insights Explorer, moet u het volgende doen:

* Een Azure Time Series Insights Gen2-omgeving hebben ingericht. Lees de [Azure time series Insights Gen2](./tutorial-set-up-environment.md) -zelf studie voor meer informatie over het inrichten van een exemplaar.
* [Geef gegevens toegang](./concepts-access-policies.md) tot de Azure time series Insights Gen2-omgeving die u hebt gemaakt voor het account. U kunt ook toegang verlenen aan anderen en aan uzelf.
* Voeg een gebeurtenis bron toe aan de Azure Time Series Insights Gen2-omgeving om gegevens naar de omgeving te pushen:
  * Meer informatie [over verbinding maken met een event hub](./how-to-ingest-data-event-hub.md)
  * Meer informatie [over verbinding maken met een IOT-hub](./how-to-ingest-data-iot-hub.md)

## <a name="explore-the-azure-time-series-insights-explorer"></a>De Azure Time Series Insights Explorer verkennen

De Azure Time Series Insights Explorer bestaat uit de volgende zeven elementen:

[![Overzicht van Azure Time Series Insights Explorer](media/v2-update-explorer/preview-explorer-overview.png)](media/v2-update-explorer/preview-explorer-overview.png#lightbox)

1. [Omgevings paneel](#1-environment-panel): geeft al uw Azure time series Insights Gen2 omgevingen.
1. [Navigatie balk](#2-navigation-bar): Hiermee kunt u scha kelen tussen de pagina's **analyseren** en **model** .
1. [Hiërarchie structuur en zoek paneel](#3-hierarchy-tree-and-search-panel): Hiermee kunt u specifieke gegevens elementen selecteren en zoeken om in een grafiek te plaatsen.
1. Hele [tijd Series](#4-time-series-well): hier ziet u alle gegevens elementen die momenteel zijn geselecteerd.
1. [Deel venster grafiek](#5-chart-panel): Hiermee wordt uw huidige werk grafiek weer gegeven.
1. [Tijd lijn](#6-time-editor-panel): Hiermee kunt u de tijds duur van uw werk wijzigen.
1. [App-balk](#7-app-bar): bevat uw opties voor gebruikers beheer (zoals de huidige Tenant) en stelt u in staat om deze te wijzigen en taal instellingen.

## <a name="1-environment-panel"></a>1. Omgevings paneel

In het deel venster omgeving worden alle Azure Time Series Insights Gen2 omgevingen weer gegeven waartoe u toegang hebt. De lijst bevat Gen2-omgevingen en gen1-omgevingen. Selecteer gewoon de omgeving die u wilt gebruiken om daar onmiddellijk mee te maken.

1. Selecteer de vervolg keuze pijl naast de weer gegeven omgeving.

   [![Omgevings paneel](media/v2-update-explorer/environment-panel.png)](media/v2-update-explorer/environment-panel.png#lightbox)

1. Selecteer vervolgens de gewenste omgeving.

## <a name="2-navigation-bar"></a>2. navigatie balk

  [![De navigatie balk](media/v2-update-explorer/tsi-preview-navigation-bar.png)](media/v2-update-explorer/tsi-preview-navigation-bar.png#lightbox)

Gebruik de navigatie balk om te kiezen tussen twee weer gaven:

* **Analyseren**: gebruik dit diagram om uitgebreide analyses uit te voeren op uw gemodelleerde of niet-gemodelleerde time series-gegevens.
* **Model**: gebruik it om nieuwe Azure time series Insights Gen2,-hiërarchieën en-instanties naar uw time series model te pushen.

### <a name="model-authoring"></a>Model ontwerpen

De Azure Time Series Insights Gen2 ondersteunt volledige Create-, Read-, update-en delete-bewerkingen (ruw) in uw time series-model.

[![Het zoek paneel van de model](media/v2-update-explorer/model-search-panel.png)](media/v2-update-explorer/model-search-panel.png#lightbox)

* **Type time series-model**: u kunt de typen tijd reeks modellen gebruiken om variabelen of formules te definiëren voor het uitvoeren van berekeningen. Ze zijn gekoppeld aan een bepaald exemplaar van het time series-model. Een type kan een of meer variabelen hebben.
* **Hiërarchie van Time Series-model**: hiërarchieën zijn systematische organisaties van uw gegevens. Hiërarchieën zijn de relaties tussen verschillende instanties in uw time series-model.
* **Exemplaar van de time series-model**: exemplaren zijn de tijd reeks zelf. In de meeste gevallen zijn het **DeviceID** of **AssetID**, de unieke id van de asset in de omgeving.

Lees [Times-serie modellen](./concepts-model-overview.md)voor meer informatie over het time series-model.

## <a name="3-hierarchy-tree-and-search-panel"></a>3. hiërarchie structuur en zoek paneel

Met de hiërarchie structuur en het zoek paneel kunt u eenvoudig uw [Time Series-model](./concepts-model-overview.md) hiërarchie doorzoeken en ernaar bladeren om de specifieke time series-exemplaren te vinden die u wilt weer geven in de grafiek. Wanneer u uw instanties selecteert, worden deze niet alleen toegevoegd aan de huidige grafiek, maar ook toegevoegd aan de gegevens bron.

[![Hiërarchie structuur en zoek paneel](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png)](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png#lightbox)

In het deel venster met zoek resultaten kunt u ook uw resultaten bekijken in een hiërarchie weergave of een lijst weergave, zodat u gemakkelijk de instanties kunt vinden die u wilt weer geven.

## <a name="4-time-series-well"></a>4. tijd Series goed

Op de juiste weer gave worden exemplaar velden en andere meta gegevens gekoppeld aan geselecteerde exemplaren van de tijd reeks modellen. Door de selectie vakjes aan de rechter kant in te scha kelen, kunt u specifieke exemplaren verbergen of weer geven in de huidige grafiek.

  [![De Gen2](media/v2-update-explorer/tsi-preview-ui-explorer-well.png)](media/v2-update-explorer/tsi-preview-ui-explorer-well.png#lightbox)

U kunt specifieke gegevens elementen uit uw huidige gegevens bron verwijderen door het besturings element rood **verwijderen** (prullenbak kan) aan de linkerkant van het element te selecteren. Daarnaast kunt u zelf bepalen hoe elk element in de grafiek wordt weer gegeven. U kunt ervoor kiezen om min/Maxe scha duwen, gegevens punten toe te voegen, het element in tijd te verschuiven en het exemplaar op een getrapte manier te visualiseren.

Daarnaast kunt u met het besturings element verkennen eenvoudig tijd verschuivingen en spreidings diagrammen maken.

  [![Opties voor goed lay-out](media/v2-update-explorer/well-layout-options.png)](media/v2-update-explorer/well-layout-options.png#lightbox)

> [!NOTE]
> Als het volgende bericht wordt weer gegeven, heeft het exemplaar geen gegevens tijdens de geselecteerde periode. Om het probleem op te lossen, verg root u de tijds Panne of bevestigt u dat het exemplaar gegevens pusht.
>
> ![Geen gegevens melding](media/v2-update-explorer/tsi-preview-no-data-warning.png)

## <a name="5-chart-panel"></a>5. deel venster grafiek

Met de grafiek kunt u Time Series-exemplaren als regels weer geven. U kunt het deel venster omgeving, het gegevens model en het configuratie scherm samen vouwen door te klikken op de Webbe sturings elementen om de grafiek groter te maken.

  [![Overzicht van Gen2-grafiek](media/v2-update-explorer/tsi-preview-chart-overview.png)](media/v2-update-explorer/tsi-preview-chart-overview.png#lightbox)

1. **Grafiek type**: Hiermee bepaalt u welke gegevens elementen beschikbaar zijn voor visualisatie.

1. **Interval grootte**: met de schuif regelaar voor interval grootte kunt u in-en uitzoomen op dezelfde tijds duur. Dit biedt een nauw keurigere controle over de verplaatsing tussen grote tijds segmenten die soepele trends weer geven naar segmenten zo klein als de milliseconde, zodat u nauw keurige delen van uw gegevens met hoge resolutie kunt bekijken. Het standaard start punt van de schuif regelaar is ingesteld als de meest optimale weer gave van de gegevens uit uw selectie. Balancing-resolutie, query snelheid en granulatie.

1. **Zoomen en pannen**: Selecteer dit besturings element om het diagram in te zoomen en te pannen.

1. **Besturings element voor y-as**: doorloopt de beschik bare opties voor de weer gave van de y-as:

    * `Stacked`: Elke regel heeft een afzonderlijke Y-as.
    * `Overlap`: Gebruik dit om meerdere lijnen op dezelfde Y-as te stapelen, waarbij de gegevens van de Y-as worden gewijzigd op basis van de geselecteerde regel.
    * `Shared`: Alle gegevens van de Y-as worden samen weer gegeven.

1. **Element marker**: het momenteel geselecteerde gegevens element en de bijbehorende details.

U kunt verder inzoomen op een specifiek gegevens segment door met de rechter muisknop op een gegevens punt in de huidige grafiek **te klikken** terwijl u de muis ingedrukt houdt en vervolgens het geselecteerde gebied naar het gewenste eind punt sleept. **Klik** met de rechter muisknop op het blauwe, geselecteerde gebied en selecteer vervolgens **Inzoomen** , zoals hieronder wordt weer gegeven. U kunt ook de telemetrie-gebeurtenissen in de geselecteerde tijds periode weer geven en downloaden.

  [![Zoomen voor Gen2-grafiek](media/v2-update-explorer/preview-chart-zoom.png)](media/v2-update-explorer/preview-chart-zoom.png#lightbox)

Nadat u de actie voor **Inzoomen** hebt uitgevoerd, wordt uw geselecteerde gegevensset weer gegeven. Selecteer het besturings element opmaken om de drie weer gaven van de y-as van uw gegevens te door lopen.

  [![Y-as van Gen2-grafiek](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png)](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png#lightbox)

Hier wordt een voor beeld van een **overlappende grafiek** gegeven:

  [![De optie overlappende grafieken](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png)](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png#lightbox)

De knop **meer acties** wordt uitgebreid om de **down load als CSV** weer te geven, **verbinding te maken met Power bi**, **diagram gegevens weer te geven als een tabel** en opties voor **onbewerkte gebeurtenissen te verkennen** .

  [![Optie voor meer acties](media/v2-update-explorer/more-actions-icon.png)](media/v2-update-explorer/more-actions-icon.png#lightbox)

Meer informatie over de optie **verbinding maken met Power bi** in [Power bi connector](concepts-power-bi.md).

## <a name="6-time-editor-panel"></a>6. time editor-paneel

Wanneer u werkt met Azure Time Series Insights Gen2, selecteert u eerst een tijds Panne. De geselecteerde tijds periode bepaalt de gegevensset die beschikbaar is voor manipulatie met de Azure Time Series Insights Gen2-update widgets.

  [![Venster Time selection](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png)](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png#lightbox)

> [!TIP]
> Een deel van de tijd lijn is gemarkeerd in een oranje of oranje kleur om de hoeveelheid gegevens aan te geven die beschikbaar is in de warme opslag.

De volgende Webbe sturings elementen zijn beschikbaar in Azure Time Series Insights Gen2 om uw werk periode te selecteren.

  [![Goed beheer van exploratie](media/v2-update-explorer/exploration-well-control.png)](media/v2-update-explorer/exploration-well-control.png#lightbox)

1. **Besturings element voor de binnenste datum bereik schuif regelaar**: gebruik de twee eindpunt besturings elementen door ze over de gewenste periode te slepen. Dit binnenste datum bereik wordt beperkt door de schuif regelaar voor het buitenste datum bereik.

1. **Knoppen voor het datum bereik verg Roten** of verkleinen: u kunt uw tijds periode verg Roten of verkleinen door een van de knoppen voor het gewenste interval te selecteren.

1. **Besturings element voor het samen vouwen van tijds duur**: met dit Webbe sturings element kunt u alle besturings elementen verbergen, behalve de schuif regelaar binnen het bereik van de binnenkant.

1. **Besturings element voor schuif regelaar voor datum bereik**: gebruik de besturings elementen voor het eind punt om het buitenste datum bereik te selecteren. Dit is beschikbaar voor het besturings element binnen het bereik van datums.

1. **Besturings element voor schuif regelaar voor tijd bereik**: met deze optie kunt u snel scha kelen tussen vooraf ingestelde tijdgebonden selecties, zoals de laatste **30 minuten**, de **laatste 12 uur** of een **aangepast bereik**. Als u deze waarde wijzigt, worden ook de beschik bare afdrukbereiken gewijzigd die worden besproken in het hulp programma voor de schuif regelaar.

   [![Van en naar selectie paneel](media/v2-update-explorer/to-and-from-element.png)](media/v2-update-explorer/to-and-from-element.png#lightbox)

## <a name="7-app-bar"></a>7. app-balk

Het navigatie venster Azure Time Series Insights Gen2 wordt boven aan uw app weer gegeven. Het biedt de volgende functies:

### <a name="current-session-share-link-control"></a>Koppelings besturings element huidige sessie delen

  [![Deelpictogram](media/v2-update-explorer/tsi-preview-explorer-share-icon.png)](media/v2-update-explorer/tsi-preview-explorer-share-icon.png#lightbox)

Selecteer het pictogram nieuwe **share** om een URL-koppeling met uw team te delen.

  [![Uw exemplaar-URL delen](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png)](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png#lightbox)

### <a name="tenant-section"></a>Tenant sectie

  [![Tenant selectie](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png)](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png#lightbox)

* Hier worden de gegevens van uw huidige Azure Time Series Insights Gen2-aanmeldings account weer gegeven.
* Gebruik deze optie om te scha kelen tussen de beschik bare Thema's.
* Gebruik het om de Gen2- [demo omgeving](https://insights.timeseries.azure.com/preview/demo)weer te geven.

### <a name="theme-selection"></a>Thema selecteren

Als u een nieuw thema wilt selecteren, selecteert u het profiel pictogram in de rechter bovenhoek. Selecteer vervolgens **thema wijzigen**.

  [![Thema selecteren](media/v2-update-explorer/tsi-preview-theme-selection.png)](media/v2-update-explorer/tsi-preview-theme-selection.png#lightbox)

> [!TIP]
> Taal selectie is ook beschikbaar door uw profiel pictogram te selecteren.

Azure Time Series Insights Explorer ondersteunt twee Thema's:

* **Licht thema**: het standaard thema dat in dit document wordt weer gegeven.
* **Donker thema**: Hiermee wordt de Explorer weer gegeven zoals hier:

  [![Geselecteerd donker thema](media/v2-update-explorer/tsi-preview-dark-theme-selected.png)](media/v2-update-explorer/tsi-preview-dark-theme-selected.png#lightbox)

## <a name="gen1-environment-controls"></a>Besturings elementen voor gen1 omgeving

### <a name="gen2-terms-panel"></a>Gen2-termen paneel

Deze sectie is alleen van toepassing op bestaande gen1-omgevingen die proberen om de Explorer in de bijgewerkte gebruikers interface te gebruiken. U kunt het product van de gen1 product en het Gen2 in combi natie gebruiken. We hebben een aantal functies van de bestaande gebruikers interface aan de bijgewerkte Explorer toegevoegd, maar u kunt de volledige UI-ervaring voor een gen1-omgeving in de nieuwe Azure Time Series Insights Explorer krijgen.

In plaats van de hiërarchie wordt het deel venster Azure Time Series Insights Gen2-voor waarden weer gegeven. In het deel venster voor waarden kunt u query's definiëren in uw omgeving. Gebruik deze functie om ook gegevens te filteren op basis van een predikaat.

  [![Where-query paneel](media/v2-update-explorer/s1-s2-preview-query.png)](media/v2-update-explorer/s1-s2-preview-query.png#lightbox)

In het deel venster Editor voor Azure Time Series Insights termen van Gen2 worden de volgende para meters gebruikt:

**Waar**: gebruik de component WHERE om snel uw gebeurtenissen te filteren met behulp van de set operands die in de volgende tabel worden weer gegeven. Als u een zoek opdracht uitvoert door een operand te selecteren, wordt het predikaat automatisch bijgewerkt op basis van die zoek opdracht. Ondersteunde typen operanden zijn onder andere:

| Bewerking    | Ondersteunde typen    | Notities |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double, DateTime, time span | |
| `=`, `!=`, `<>` | Teken reeks, BOOL, double, DateTime, time span, NULL |
| `IN` | Teken reeks, BOOL, double, DateTime, time span, NULL | Alle operanden moeten van hetzelfde type zijn of NULL-constante zijn. |
| `HAS` | Tekenreeks | Aan de rechter kant zijn alleen letterlijke teken reeksen met constanten toegestaan. Lege teken reeks en NULL zijn niet toegestaan. |

Lees de [expressie time series (TSX)](/rest/api/time-series-insights/reference-time-series-expression-syntax)voor meer informatie over ondersteunde query bewerkingen en gegevens typen.

### <a name="examples-of-where-clauses"></a>Voor beelden van WHERE-componenten

  [![Voor beelden van WHERE-clausule](media/v2-update-explorer/tsi-preview-example-queries.png)](media/v2-update-explorer/tsi-preview-example-queries.png#lightbox)

**Meting**: een vervolg keuzelijst waarin alle numerieke kolommen worden weer gegeven (**dubbels**) die u als elementen voor uw huidige grafiek kunt gebruiken.

**Splitsen op**: in deze vervolg keuzelijst worden alle beschik bare categorische-kolommen (teken reeksen) in uw model weer gegeven waarop u uw gegevens kunt groeperen. U kunt Maxi maal vijf termen toevoegen die op dezelfde x-as worden weer gegeven. Voer de gewenste para meters in en selecteer **toevoegen** om een nieuwe term toe te voegen.

  [![Er is een query uitgevoerd en gefilterde weer gave 1](media/v2-update-explorer/s1-s2-preview-filtered-view.png)](media/v2-update-explorer/s1-s2-preview-filtered-view.png#lightbox)

U kunt elementen weer geven en verbergen in het deel venster grafiek door het zicht bare pictogram te selecteren, zoals wordt weer gegeven in de volgende afbeelding. Als u query's volledig wilt verwijderen, selecteert u de rode **X**.

  [![Een aangevraagde en gefilterde optie annuleren](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png)](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het opnemen van [gegevens](./concepts-ingestion-overview.md) in uw omgeving.

* Bekijk het artikel over [opslag](concepts-storage.md).

* Meer informatie over [gegevens modellering](./concepts-model-overview.md) in azure time series Insights Gen2.

* Meer informatie [over het vaststellen en oplossen van problemen met](./how-to-diagnose-troubleshoot.md) uw omgeving.
