---
title: Overzicht van Azure Monitor Workbooks
description: Meer informatie over hoe werkmappen een flexibel canvas bieden voor gegevensanalyse en het maken van uitgebreide visuele rapporten binnen de Azure Portal.
services: azure-monitor
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 3d75d7605ba082aac84973aef247de79d55b4c9c
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482769"
---
# <a name="azure-monitor-workbooks"></a>Azure Monitor Workbooks

Werkmappen bieden een flexibel canvas voor gegevensanalyse en het maken van uitgebreide visuele rapporten in Azure Portal. Ze stellen u in staat om meerdere gegevensbronnen uit Azure aan te boren en deze te combineren tot uniforme interactieve ervaringen.

Hier is een video-overzicht van het maken van werkmappen.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B4Ap]

## <a name="data-sources"></a>Gegevensbronnen

Werkmappen kunnen gegevens uit meerdere bronnen in Azure opvragen. Auteurs van werkmappen kunnen deze gegevens transformeren om inzicht te bieden in de beschikbaarheid, de prestaties, het gebruik en de algemene status van de onderliggende onderdelen. Bijvoorbeeld het analyseren van prestatielogboeken van virtuele machines om instanties met een hoog CPU- of weinig geheugen te identificeren en de resultaten weer te geven als een raster in een interactief rapport.
  
Maar het echte voordeel van werkmappen is de mogelijkheid om gegevens uit verschillende bronnen in één rapport te combineren. Hierdoor kunnen samengestelde resourceweergaven of joins worden gemaakt tussen resources, waardoor uitgebreidere gegevens en inzichten mogelijk zijn die anders onmogelijk zouden zijn.

Werkmappen zijn momenteel compatibel met de volgende gegevensbronnen:

* [Logboeken](../visualize/workbooks-data-sources.md#logs)
* [Metrische gegevens](../visualize/workbooks-data-sources.md#metrics)
* [Azure Resource Graph](../visualize/workbooks-data-sources.md#azure-resource-graph)
* [Waarschuwingen (preview)](../visualize/workbooks-data-sources.md#alerts-preview)
* [Workload Health](../visualize/workbooks-data-sources.md#workload-health)
* [Azure Resource Health](../visualize/workbooks-data-sources.md#azure-resource-health)
* [Azure Data Explorer](../visualize/workbooks-data-sources.md#azure-data-explorer)

## <a name="visualizations"></a>Visualisaties

Werkmappen bieden een uitgebreide set mogelijkheden voor het visualiseren van uw gegevens. Voor gedetailleerde voorbeelden van elk visualisatietype kunt u de onderstaande koppelingen raadplegen:

* [Tekst](../visualize/workbooks-text-visualizations.md)
* [Grafieken](../visualize/workbooks-chart-visualizations.md)
* [Rasters](../visualize/workbooks-grid-visualizations.md)
* [Tiles](../visualize/workbooks-tile-visualizations.md)
* [Bomen](../visualize/workbooks-tree-visualizations.md)
* [Grafieken](../visualize/workbooks-graph-visualizations.md)
* [Samengesteld staafdiagram](../visualize/workbooks-composite-bar.md)

:::image type="content" source="./media/workbooks-overview/visualizations.png" alt-text="Voorbeeld van werkmapvisualisaties." border="false" lightbox="./media/workbooks-overview/visualizations.png":::

### <a name="pinning-visualizations"></a>Visualisaties vastmaken

Stappen voor tekst, query's en metrische gegevens in een werkmap kunnen worden vastgemaakt met behulp van de knop Vastmaken aan die items terwijl de werkmap in de pincodemodus staat, of als de auteur van de werkmap instellingen voor dat element heeft ingeschakeld om het speldpictogram zichtbaar te maken.

Als u de pincodemodus wilt openen, klikt **u op Bewerken** om de bewerkingsmodus te openen en selecteert u het blauwe speldpictogram in de bovenste balk. Er wordt vervolgens een afzonderlijk speldpictogram weergegeven  boven het vak Bewerken van elk bijbehorend werkmapgedeelte aan de rechterkant van het scherm.

:::image type="content" source="./media/workbooks-overview/pin-experience.png" alt-text="Schermopname van de speldervaring." border="false":::

> [!NOTE]
> De status van de werkmap wordt op het moment van de pincode opgeslagen en vastgemaakte werkmappen op een dashboard worden niet bijgewerkt als de onderliggende werkmap wordt gewijzigd. Als u een vastgemaakt werkmapdeel wilt bijwerken, moet u dat onderdeel verwijderen en opnieuw vastmaken.

## <a name="getting-started"></a>Aan de slag

Als u de werkmappenervaring wilt verkennen, gaat u eerst naar de Azure Monitor service. U kunt dit doen door **Monitor te typen** in het zoekvak in de Azure Portal.

Selecteer vervolgens **Werkmappen.**

:::image type="content" source="./media/workbooks-overview/workbooks.png" alt-text="Schermopname van de knop Workbooks gemarkeerd in een rood vak." border="false":::

### <a name="gallery"></a>Galerie

Met de galerie kunt u werkmappen van alle typen ordenen, sorteren en beheren.

:::image type="content" source="./media/workbooks-overview/gallery-all-tab.png" alt-text="Schermopname van de galerie op het tabblad Alle." lightbox="media/workbooks-overview/gallery-all-tab.png":::

#### <a name="gallery-tabs"></a>Galerietabbladen

Er zijn vier tabbladen in de galerie om werkmaptypen te organiseren.

| Tabblad              | Beschrijving                                       |
|------------------|---------------------------------------------------|
| Alles | Toont de vier belangrijkste items voor elk type: werkmappen, openbare sjablonen en mijn sjablonen. Werkmappen worden gesorteerd op wijzigingsdatum, zodat u de meest recente acht gewijzigde werkmappen ziet.|
| Werkmappen | Toont de lijst met alle beschikbare werkmappen die u hebt gemaakt of die met u zijn gedeeld. |
| Openbare sjablonen | Toont de lijst met alle beschikbare kant-en-klare werkmapsjablonen die door Microsoft zijn gepubliceerd. Gegroepeerd op categorie. |
| Mijn sjablonen | Toont de lijst met alle beschikbare geïmplementeerde werkmapsjablonen die u hebt gemaakt of met u hebt gedeeld. Gegroepeerd op categorie. |

#### <a name="features"></a>Functies

* Op elk tabblad staat een raster met informatie over de werkmappen. Het bevat een beschrijving, datum van laatste wijziging, tags, abonnement, resourcegroep, regio en gedeelde status. U kunt de werkmappen ook sorteren op deze informatie.
* Filter op resourcegroep, abonnementen, werkmap-/sjabloonnaam of sjablooncategorie.
* Selecteer meerdere werkmappen die u wilt verwijderen of bulksgewijs wilt verwijderen.
* Elke werkmap heeft een contextmenu (beletselteken/drie punten aan het einde). Als u werkmappen selecteert, wordt er een lijst met snelle acties geopend.
    * Resource weergeven: open het tabblad Werkmapresource om de resource-id van de werkmap weer te geven, tags toe te voegen, vergrendelingen te beheren, enzovoort.
    * Werkmap verwijderen of de naam wijzigen.
    * Werkmap vastmaken aan dashboard.

### <a name="workbooks-versus-workbook-templates"></a>Werkmappen versus werkmapsjablonen

U ziet een werkmap in _het_ groen en een aantal _paars sjablonen._ Sjablonen fungeren als gecureerde rapporten die zijn ontworpen voor flexibel hergebruik door meerdere gebruikers en teams. Als u een sjabloon opent, wordt er een tijdelijke werkmap gemaakt die is gevuld met de inhoud van de sjabloon.

U kunt de parameters van de werkmap op basis van sjablonen aanpassen en analyses uitvoeren zonder dat u de toekomstige rapportage-ervaring voor collega's wilt verbreken. Als u een sjabloon opent, enkele aanpassingen maakt en vervolgens het pictogram Opslaan selecteert, wordt de sjabloon opgeslagen als een werkmap die vervolgens groen wordt weergegeven, waardoor de oorspronkelijke sjabloon ongewijzigd blijft.

Achter de basis verschillen sjablonen ook van opgeslagen werkmappen. Door een werkmap op te slaan, wordt Azure Resource Manager gekoppelde resource, terwijl aan de tijdelijke werkmap die is gemaakt bij het openen van een sjabloon geen unieke resource is gekoppeld. Raadpleeg het artikel toegangsbeheer voor werkmappen voor meer informatie over hoe toegangsbeheer wordt beheerd in [werkmappen.](../visualize/workbooks-access-control.md)

### <a name="exploring-a-workbook-template"></a>Een werkmapsjabloon verkennen

Selecteer **Analyse van toepassingsfout** om een van de standaardsjablonen voor toepassingswerkboeken weer te geven.

:::image type="content" source="./media/workbooks-overview/failure-analysis.png" alt-text="Schermopname van sjabloon voor analyse van toepassingsfout." border="false" lightbox="./media/workbooks-overview/failure-analysis.png":::

Zoals eerder vermeld, wordt met het openen van de sjabloon een tijdelijke werkmap gemaakt om mee te kunnen communiceren. De werkmap wordt standaard geopend in de leesmodus waarin alleen de informatie wordt weergegeven voor de beoogde analyse-ervaring die is gemaakt door de oorspronkelijke auteur van de sjabloon.

In het geval van deze specifieke werkmap is de ervaring interactief. U kunt het abonnement, de doel-apps en het tijdsbereik aanpassen van de gegevens die u wilt weergeven. Zodra u deze selecties hebt gemaakt, is het raster met HTTP-aanvragen ook interactief. Als u een afzonderlijke rij selecteert, wordt gewijzigd welke gegevens worden weergegeven in de twee grafieken onderaan het rapport.

### <a name="editing-mode"></a>Bewerkingsmodus

Als u wilt weten hoe deze werkmapsjabloon wordt gemaakt, moet u wisselen naar de bewerkingsmodus door **Bewerken te selecteren.**

:::image type="content" source="./media/workbooks-overview/edit.png" alt-text="Schermopname van de knop Bewerken in werkmappen." border="false" :::

Wanneer u bent overgeschakeld naar de  bewerkingsmodus, ziet u aan de rechterkant een aantal bewerkvakken die overeenkomen met elk afzonderlijk aspect van uw werkmap.

:::image type="content" source="./media/workbooks-overview/edit-mode.png" alt-text="Schermopname van de knop Bewerken." border="false" lightbox="./media/workbooks-overview/edit-mode.png":::

Als we direct onder het raster met aanvraaggegevens de knop Bewerken selecteren, zien we dat dit deel van onze werkmap bestaat uit een Kusto-query op gegevens uit een Application Insights resource.

:::image type="content" source="./media/workbooks-overview/kusto.png" alt-text="Schermopname van de onderliggende Kusto-query." border="false" lightbox="./media/workbooks-overview/kusto.png":::

Als u de andere knoppen Bewerken aan de rechterkant selecteert, ziet u een aantal [](../visualize/workbooks-parameters.md) kernonderdelen die uit werkmappen bestaan, zoals tekstvakken op basis van Markdown, [](../visualize/workbooks-text-visualizations.md)UI-elementen voor parameterselectie en andere typen [grafieken/visualisaties.](#visualizations) 

Het verkennen van de vooraf gebouwde sjablonen in de bewerkingsmodus en deze vervolgens aanpassen aan uw behoeften en uw eigen aangepaste werkmap opslaan is een uitstekende manier om te leren wat er mogelijk is met Azure Monitor werkmappen.

## <a name="dashboard-time-ranges"></a>Dashboardtijdsbereiken

Onderdelen van vastgemaakte werkmapquery's respecteren het tijdsbereik van het  dashboard als het vastgemaakte item is geconfigureerd voor het gebruik van een tijdsbereikparameter. De waarde voor het tijdsbereik van het dashboard wordt gebruikt als de waarde van de parameter voor het tijdsbereik en elke wijziging van het dashboardtijdsbereik zorgt ervoor dat het vastgemaakte item wordt bijgewerkt. Als een vastgemaakt onderdeel het tijdsbereik van het dashboard gebruikt, ziet u de subtitel van de update van het vastgemaakte onderdeel om het tijdsbereik van het dashboard weer te geven wanneer het tijdsbereik verandert.

Daarnaast worden vastgemaakte werkmapdelen met behulp van een tijdsbereikparameter automatisch vernieuwd met een snelheid die wordt bepaald door het tijdsbereik van het dashboard. De laatste keer dat de query is uitgevoerd, wordt weergegeven in de ondertitel van het vastgemaakte deel.

Als een vastgemaakte stap een expliciet ingesteld tijdsbereik heeft (geen tijdsbereikparameter gebruikt), wordt dat tijdsbereik altijd gebruikt voor het dashboard, ongeacht de instellingen van het dashboard. De ondertitel van het vastgemaakte onderdeel geeft het tijdsbereik van het dashboard niet weer en de query wordt niet automatisch vernieuwd op het dashboard. De ondertitel toont de laatste keer dat de query is uitgevoerd.

> [!NOTE]
> Query's die gebruikmaken van de samenvoegingsgegevensbron worden momenteel niet ondersteund bij het vastmaken aan dashboards. 

## <a name="sharing-workbook-templates"></a>Werkmapsjablonen delen

Zodra u begint met het maken van uw eigen werkmapsjablonen, wilt u deze wellicht delen met de bredere community. Ga naar onze [GitHub-opslagplaats](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md)voor meer informatie en om andere sjablonen te verkennen die geen deel uitmaken van de standaardweergave Azure Monitor galerieweergave. Als u door bestaande werkmappen wilt bladeren, gaat u [naar de Werkmapbibliotheek](https://github.com/microsoft/Application-Insights-Workbooks/tree/master/Workbooks) op GitHub.

## <a name="next-step"></a>Volgende stap

* [Ga aan de](#visualizations) slag met meer informatie over veel uitgebreide visualisatieopties voor werkmappen.
* [Toegang](../visualize/workbooks-access-control.md) tot uw werkmapbronnen beheren en delen.