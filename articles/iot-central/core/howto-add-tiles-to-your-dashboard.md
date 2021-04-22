---
title: Configureren naar uw Azure IoT Central dashboard | Microsoft Docs
description: Als bouwer leert u hoe u de standaardinstellingen voor Azure IoT Central-toepassingsdashboard met tegels configureert.
author: philmea
ms.author: philmea
ms.date: 12/19/2020
ms.topic: how-to
ms.service: iot-central
ms.openlocfilehash: 8a8ba765a966409c06dbba636932f7777624f6d4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864249"
---
# <a name="configure-the-application-dashboard"></a>Het toepassingsdashboard configureren

Het **dashboard** is de eerste pagina die u ziet wanneer u verbinding maakt met IoT Central toepassing. Als u uw toepassing maakt op basis van een van de toepassingssjablonen die zijn gericht op de [branche,](./concepts-app-templates.md)heeft uw toepassing een vooraf gedefinieerd dashboard om te starten. Als u uw toepassing maakt op basis van een aangepaste [toepassingssjabloon](./concepts-app-templates.md), geeft uw dashboard een aantal tips weer om aan de slag te gaan.

> [!TIP]
> Gebruikers kunnen [naast het standaardtoepassingsdashboard](howto-create-personal-dashboards.md) meerdere dashboards maken. Deze dashboards kunnen alleen persoonlijk zijn voor de gebruiker of worden gedeeld met alle gebruikers van de toepassing.  

## <a name="add-tiles"></a>Tegels toevoegen

In de volgende schermopname ziet u het dashboard in een toepassing die is gemaakt op basis van de **sjabloon Aangepaste** toepassing. Als u het huidige dashboard wilt aanpassen, **selecteert u Bewerken** om een aangepast persoonlijk of gedeeld dashboard toe te voegen, selecteert u **Nieuw:**

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png" alt-text="Dashboard voor toepassingen op basis van de aangepaste toepassingssjabloon":::

Nadat u Bewerken **of Nieuw** **hebt geselecteerd,** is het dashboard in *de bewerkingsmodus.* U kunt de hulpprogramma's in het **deelvenster Dashboard** bewerken gebruiken om tegels aan het dashboard toe te voegen en tegels op het dashboard zelf aan te passen en te verwijderen. Als u bijvoorbeeld een **telemetrietegel wilt** toevoegen om de huidige temperatuur weer te geven die door een of meer apparaten is gerapporteerd:

1. Selecteer een **apparaatgroep en** kies vervolgens uw apparaten in **de** vervolgkeuze selecteren die op de tegel moeten worden weergegeven. U ziet nu de beschikbare telemetrie, eigenschappen en opdrachten van de apparaten.

1. Indien nodig gebruikt u de vervolgkeuzekeuzerij om een telemetriewaarde te selecteren die op de tegel moet worden weergegeven. U kunt meer items aan de tegel toevoegen door **+ Telemetrie**, **+ Eigenschap** of **+ Cloud-eigenschap te selecteren.**

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/device-details.png" alt-text="Een temperatuurtegel telemetrie toevoegen aan het dashboard":::

Wanneer u alle waarden hebt geselecteerd die op de tegel moeten worden weergegeven, klikt u op **Tegel toevoegen.** De tegel wordt nu weergegeven op het dashboard, waar u de visualisatie kunt wijzigen, de weergave kunt wijzigen, verplaatsen en configureren.

Wanneer u klaar bent met het toevoegen en  aanpassen van tegels op het dashboard, selecteert u Opslaan om de wijzigingen op te slaan in het dashboard, waardoor u uit de bewerkingsmodus komt.

## <a name="customize-tiles"></a>Tegels aanpassen

Als u een tegel wilt bewerken, moet u zich in de bewerkingsmodus hebben.  De beschikbare aanpassingsopties zijn afhankelijk van het [tegeltype](#tile-types):

* Met het liniaalpictogram op een tegel kunt u de visualisatie wijzigen. Visualisaties zijn onder andere lijndiagrammen, staafdiagrammen, cirkeldiagrammen, laatst bekende waarden, Key Performance Indicators (of KPI's), heatmaps en kaarten.

* Met het vierkantspictogram kunt u de tegel het grootser maken.

* Met het tandwielpictogram kunt u de visualisatie configureren. Voor een visualisatie van een lijndiagram kunt u er bijvoorbeeld voor kiezen om de legenda en assen weer te geven en het tijdsbereik te kiezen dat u wilt plotten.


## <a name="tile-types"></a>Tegeltypen

In de volgende tabel worden de verschillende typen tegels beschreven die u aan een dashboard kunt toevoegen:

| Tegel             | Description |
| ---------------- | ----------- |
| Markdown         | Markdown-tegels zijn tegels die kunnen worden geklikt om een koptekst en beschrijving weer te geven die zijn opgemaakt met markdown. De URL kan een relatieve koppeling zijn naar een andere pagina in de toepassing of een absolute koppeling naar een externe site.|
| Installatiekopie            | Afbeeldingtegels geven een aangepaste afbeelding weer en kunnen erop worden geklikt. De URL kan een relatieve koppeling zijn naar een andere pagina in de toepassing of een absolute koppeling naar een externe site.|
| Label            | Labeltegels geven aangepaste tekst weer op een dashboard. U kunt de grootte van de tekst kiezen. Gebruik een labeltegel om relevante informatie aan het dashboard toe te voegen, zoals beschrijvingen, contactgegevens of help.|
| Count            | Tegels voor aantal geven het aantal apparaten in een apparaatgroep weer.|
| Kaart              | Kaarttegels geven de locatie van een of meer apparaten op een kaart weer. U kunt ook maximaal 100 punten van de locatiegeschiedenis van een apparaat weergeven. U kunt bijvoorbeeld een voorbeeldroute weergeven van waar een apparaat de afgelopen week is geweest.|
| KPI              |  KPI-tegels geven geaggregeerde telemetriewaarden weer voor een of meer apparaten gedurende een bepaalde periode. U kunt deze bijvoorbeeld gebruiken om de maximale temperatuur en druk weer te geven die het afgelopen uur voor een of meer apparaten is bereikt.|
| Lijndiagram       | Lijndiagramtegels plotten een of meer geaggregeerde telemetriewaarden voor een of meer apparaten voor een bepaalde periode. U kunt bijvoorbeeld een lijndiagram weergeven om de gemiddelde temperatuur en druk van een of meer apparaten voor het afgelopen uur te plotten.|
| Staafdiagram        | Staafdiagramtegels plotten een of meer geaggregeerde telemetriewaarden voor een of meer apparaten voor een bepaalde periode. U kunt bijvoorbeeld een staafdiagram weergeven om de gemiddelde temperatuur en druk van een of meer apparaten in het afgelopen uur weer te geven.|
| Cirkeldiagram        | In tegels met cirkeldiagramnen worden een of meer geaggregeerde telemetriewaarden weergegeven voor een of meer apparaten voor een bepaalde periode.|
| Heatmap         | Heatmaptegels geven informatie weer over een of meer apparaten, weergegeven als kleuren.|
| Laatst bekende waarde | In tegels met laatst bekende waarden worden de meest recente telemetriewaarden voor een of meer apparaten weergegeven. U kunt deze tegel bijvoorbeeld gebruiken om de meest recente temperatuur-, druk- en vochtigheidswaarden voor een of meer apparaten weer te geven. |
| Gebeurtenisgeschiedenis    | Tegels voor gebeurtenisgeschiedenis geven de gebeurtenissen voor een apparaat gedurende een bepaalde periode weer. U kunt deze bijvoorbeeld gebruiken om alle klepgebeurtenissen weer te geven die het afgelopen uur zijn geopend en gesloten voor een of meer apparaten.|
| Eigenschap         |  Eigenschappentegels geven de huidige waarde weer voor eigenschappen en cloudeigenschappen van een of meer apparaten. U kunt deze tegel bijvoorbeeld gebruiken om apparaateigenschappen weer te geven, zoals de fabrikant of firmwareversie van een apparaat. |

Op dit moment kunt u maximaal 10 apparaten toevoegen aan tegels die ondersteuning bieden voor meerdere apparaten.

### <a name="customizing-visualizations"></a>Visualisaties aanpassen

In lijndiagrammen worden gegevens standaard gedurende een tijdsbereik weergegeven. Het geselecteerde tijdsbereik wordt gesplitst in 50 buckets van gelijke grootte en de apparaatgegevens worden vervolgens per bucket geaggregeerd om 50 gegevenspunten over het geselecteerde tijdsbereik te geven. Als u onbewerkte gegevens wilt weergeven, kunt u uw selectie wijzigen om de laatste 100 waarden weer te geven. Als u het tijdsbereik wilt wijzigen of onbewerkte gegevensvisualisatie wilt selecteren, gebruikt u de vervolgkeuzelijst Weergavebereik in het **deelvenster Grafiek configureren.**

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/display-range.png" alt-text="Het weergavebereik van een lijndiagram wijzigen":::

Voor tegels die geaggregeerde waarden weergeven, selecteert u  het tandwielpictogram naast het telemetrietype in het deelvenster Grafiek configureren om de aggregatie te kiezen. U kunt kiezen uit gemiddelde, som, maximum, minimum en aantal.

Voor lijndiagrammen, staafdiagrammen en cirkeldiagrammen kunt u de kleur van de verschillende telemetriewaarden aanpassen. Selecteer het paletpictogram naast de telemetrie die u wilt aanpassen:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/color-customization.png" alt-text="De kleur van een telemetriewaarde wijzigen":::

Voor tegels met tekenreekseigenschappen of telemetriewaarden kunt u kiezen hoe de tekst moet worden weergegeven. Als het apparaat bijvoorbeeld een URL in een tekenreeks-eigenschap op slaat, kunt u deze weergeven als een koppeling die kan worden geklikt. Als de URL naar een afbeelding verwijst, kunt u de afbeelding in een laatst bekende waarde of eigenschapstegel renderen. Als u wilt wijzigen hoe een tekenreeks wordt weergegeven, selecteert u in de configuratie van de tegel het tandwielpictogram naast het telemetrietype of de eigenschap :

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/string-customization.png" alt-text="Wijzigen hoe een tekenreeks wordt weergegeven op een tegel":::

Voor numerieke **KPI-,** **laatst**  bekende waarde- en eigenschapstegels kunt u voorwaardelijke opmaak gebruiken om de kleur van de tegel aan te passen op basis van de huidige waarde. Als u voorwaardelijke opmaak wilt toevoegen, **selecteert u Configureren** op de tegel en selecteert u vervolgens het pictogram **Voorwaardelijke** opmaak naast de waarde die u wilt aanpassen:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-1.png" alt-text="Schermopname die laat zien hoe u de configuratieoptie voor een tegel kunt vinden en vervolgens het pictogram voor voorwaardelijke opmaak":::

Voeg de regels voor voorwaardelijke opmaak toe:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-2.png" alt-text="Schermopname met regels voor voorwaardelijke opmaak voor de gemiddelde stroom. Er zijn drie regels: minder dan 20 is groen, minder dan 50 is geel en alles boven de 50 is rood":::
   
In de volgende schermopname ziet u het effect van de regel voor voorwaardelijke opmaak:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-3.png" alt-text="Schermopname met de rode achtergrondkleur op de tegel Gemiddelde waterstroom. Het getal op de tegel is 50,54":::

### <a name="tile-formatting"></a>Tegelopmaak
Met deze functie, die beschikbaar is in KPI-, WASV- en Eigenschapstegels, kunnen gebruikers de tekengrootte aanpassen, decimale precisie kiezen, numerieke waarden afkorten (bijvoorbeeld 1700 opmaken als 1,7 kB) of tekenreekswaarden in hun tegels verpakken.

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/tile-format.png" alt-text="Tegelindeling":::

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u uw standaardtoepassingsdashboard Azure IoT Central configureren, kunt u leren hoe u [een persoonlijk dashboard maakt.](howto-create-personal-dashboards.md)
