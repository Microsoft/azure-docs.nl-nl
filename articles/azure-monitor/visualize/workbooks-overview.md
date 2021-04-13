---
title: Overzicht van Azure Monitor Workbooks
description: Meer informatie over hoe werkmappen een flexibel canvas vormen voor gegevens analyse en het maken van uitgebreide visuele rapporten in de Azure Portal.
services: azure-monitor
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: a02e5fced0a9e338a32d8d8beaa9e4b5fca994e8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309471"
---
# <a name="azure-monitor-workbooks"></a>Azure Monitor werkmappen

Werkmappen bieden een flexibel canvas voor gegevensanalyse en het maken van uitgebreide visuele rapporten in Azure Portal. Ze stellen u in staat om meerdere gegevensbronnen uit Azure aan te boren en deze te combineren tot uniforme interactieve ervaringen.

Hier volgt een video-overzicht van het maken van werkmappen.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B4Ap]

## <a name="data-sources"></a>Gegevensbronnen

Werkmappen kunnen gegevens uit meerdere bronnen in Azure opvragen. Auteurs van werkmappen kunnen deze gegevens transformeren om inzicht te bieden in de beschikbaarheid, de prestaties, het gebruik en de algemene status van de onderliggende onderdelen. U kunt bijvoorbeeld prestatie logboeken analyseren van virtuele machines om een hoog CPU-of laag geheugen te identificeren en de resultaten weer te geven als een raster in een interactief rapport.
  
Maar het echte voordeel van werkmappen is de mogelijkheid om gegevens uit verschillende bronnen in één rapport te combineren. Dit maakt het mogelijk om samengestelde resource weergaven te maken of samen te voegen over resources, waardoor rijkere gegevens en inzichten worden ingeschakeld die anders niet mogelijk zouden zijn.

Werkmappen zijn momenteel compatibel met de volgende gegevensbronnen:

* [Logboeken](../visualize/workbooks-data-sources.md#logs)
* [Metrische gegevens](../visualize/workbooks-data-sources.md#metrics)
* [Azure Resource Graph](../visualize/workbooks-data-sources.md#azure-resource-graph)
* [Waarschuwingen (preview-versie)](../visualize/workbooks-data-sources.md#alerts-preview)
* [Werk belasting status](../visualize/workbooks-data-sources.md#workload-health)
* [Azure Resource Health](../visualize/workbooks-data-sources.md#azure-resource-health)
* [Azure Data Explorer](../visualize/workbooks-data-sources.md#azure-data-explorer)

## <a name="visualizations"></a>Visualisaties

Werkmappen bieden een uitgebreide set mogelijkheden voor het visualiseren van uw gegevens. Raadpleeg de onderstaande koppelingen voor gedetailleerde voor beelden van elk type visualisatie:

* [Tekst](../visualize/workbooks-text-visualizations.md)
* [Grafieken](../visualize/workbooks-chart-visualizations.md)
* [Rasters](../visualize/workbooks-grid-visualizations.md)
* [Tiles](../visualize/workbooks-tile-visualizations.md)
* [Mapstructuren](../visualize/workbooks-tree-visualizations.md)
* [Grafieken](../visualize/workbooks-graph-visualizations.md)
* [Samengesteld staafdiagram](../visualize/workbooks-composite-bar.md)

:::image type="content" source="./media/workbooks-overview/visualizations.png" alt-text="Voor beeld van werkmap visualisaties" border="false" lightbox="./media/workbooks-overview/visualizations.png":::

## <a name="getting-started"></a>Aan de slag

Als u de werkmappen-ervaring wilt verkennen, gaat u eerst naar de Azure Monitor-service. U kunt dit doen door **monitor** te typen in het zoekvak in de Azure Portal.

Selecteer vervolgens **werkmappen**.

:::image type="content" source="./media/workbooks-overview/workbooks.png" alt-text="Scherm afbeelding van de knop werkmappen gemarkeerd in een rood vak" border="false":::

### <a name="gallery"></a>Galerie

De galerie maakt het handig om werkmappen van alle typen te organiseren, te sorteren en te beheren.

:::image type="content" source="./media/workbooks-overview/gallery-all-tab.png" alt-text="Scherm afbeelding van de galerie op het tabblad alle." lightbox="media/workbooks-overview/gallery-all-tab.png":::

#### <a name="gallery-tabs"></a>Galerie tabbladen

De galerie bevat vier tabbladen waarmee u de werkmap typen kunt ordenen.

| Tabblad              | Beschrijving                                       |
|------------------|---------------------------------------------------|
| Alles | Hiermee worden de vier meest voorkomende items voor elk type werkmappen, open bare sjablonen en mijn sjablonen weer gegeven. Werkmappen worden gesorteerd op wijzigings datum, zodat de meest recente acht gewijzigde werkmappen worden weer geven.|
| Werkmappen | Hier wordt de lijst weer gegeven met alle beschik bare werkmappen die u hebt gemaakt of die met u zijn gedeeld. |
| Open bare sjablonen | Toont de lijst met alle beschik bare, aan de slag-werkmap sjablonen die zijn gepubliceerd door micro soft. Gegroepeerd op categorie. |
| Mijn sjablonen | Hier wordt de lijst weer gegeven met alle beschik bare geïmplementeerde werkmap sjablonen die u hebt gemaakt of die met u zijn gedeeld. Gegroepeerd op categorie. |

#### <a name="features"></a>Functies

* Op elk tabblad bevindt zich een raster met informatie over de werkmappen. Het bevat beschrijving, datum van laatste wijziging, tags, abonnement, resource groep, regio en gedeelde status. U kunt de werkmappen ook sorteren op basis van deze gegevens.
* Filteren op resource groep, abonnementen, naam van werkmap/sjabloon of sjabloon categorie.
* Selecteer meerdere werkmappen om te verwijderen of bulksgewijs te verwijderen.
* Elke werkmap heeft een context menu (beletsel tekens/drie puntjes aan het einde). Als u dit selecteert, wordt er een lijst met snelle acties geopend.
    * Het tabblad Resource-Access-werkmap weer geven om de resource-ID van de werkmap te bekijken, tags toe te voegen, vergren delingen te beheren, enzovoort.
    * De werkmap verwijderen of een andere naam geven.
    * Werk de werkmap vast aan het dash board.

### <a name="workbooks-versus-workbook-templates"></a>Werkmappen versus werkmap sjablonen

U kunt een _werkmap_ in een groen en een aantal _werkmap sjablonen_ in paars weer geven. Sjablonen fungeren als gerapporteerde rapporten die zijn ontworpen voor flexibel gebruik door meerdere gebruikers en teams. Als u een sjabloon opent, wordt een tijdelijke werkmap gemaakt die is gevuld met de inhoud van de sjabloon.

U kunt de para meters van de werkmap op basis van een sjabloon aanpassen en analyses uitvoeren zonder bang te zijn voor het verbreken van de toekomstige rapportage ervaring voor collega's. Als u een sjabloon opent, wijzigingen aanbrengt en vervolgens het pictogram opslaan selecteert, wordt de sjabloon opgeslagen als een werkmap die vervolgens groen wordt weer gegeven, zodat de oorspronkelijke sjabloon ongewijzigd blijft.

Onder de motorkap verschillen sjablonen ook van opgeslagen werkmappen. Als u een werkmap opslaat, wordt een gekoppelde Azure Resource Manager resource gemaakt, terwijl de tijdelijke werkmap die wordt gemaakt wanneer alleen een sjabloon wordt geopend, geen unieke resource is gekoppeld. Raadpleeg het [artikel werkmappen Access Control](../visualize/workbooks-access-control.md)voor meer informatie over het beheren van toegangs beheer in werkmappen.

### <a name="exploring-a-workbook-template"></a>Een werkmap sjabloon verkennen

Selecteer **analyse van toepassings fouten** om een van de standaard sjablonen voor toepassings werkmappen te bekijken.

:::image type="content" source="./media/workbooks-overview/failure-analysis.png" alt-text="Scherm opname van sjabloon voor toepassings fout analyse" border="false" lightbox="./media/workbooks-overview/failure-analysis.png":::

Zoals eerder is aangegeven, maakt het openen van de sjabloon een tijdelijke werkmap waarmee u kunt werken. De werkmap wordt standaard geopend in de Lees modus, waarin alleen de informatie wordt weer gegeven voor de beoogde analyse-ervaring die is gemaakt door de oorspronkelijke auteur van de sjabloon.

In het geval van deze bepaalde werkmap is de ervaring interactief. U kunt het abonnement, de beoogde apps en het tijds bereik van de gegevens die u wilt weer geven aanpassen. Wanneer u deze selecties hebt gemaakt, wordt het raster van HTTP-aanvragen ook interactief weer gegeven. Als u een afzonderlijke rij selecteert, worden de gegevens in de twee grafieken onder aan het rapport gewijzigd.

### <a name="editing-mode"></a>Bewerkings modus

Als u wilt weten hoe deze werkmap sjabloon samen wordt geplaatst, moet u de bewerkings modus **wijzigen door bewerken** te selecteren.

:::image type="content" source="./media/workbooks-overview/edit.png" alt-text="Scherm afbeelding van de knop bewerken in werkmappen." border="false" :::

Wanneer u overschakelt naar de bewerkings modus, ziet u dat er een aantal **bewerkings** vakken worden weer gegeven aan de rechter kant die overeenkomt met elk afzonderlijk aspect van uw werkmap.

:::image type="content" source="./media/workbooks-overview/edit-mode.png" alt-text="Scherm afbeelding van de knop bewerken" border="false" lightbox="./media/workbooks-overview/edit-mode.png":::

Als we de knop bewerken direct onder het raster van aanvraag gegevens selecteren, kunnen we zien dat dit deel van onze werkmap bestaat uit een Kusto-query op basis van gegevens uit een Application Insights bron.

:::image type="content" source="./media/workbooks-overview/kusto.png" alt-text="Scherm opname van onderliggende Kusto-query" border="false" lightbox="./media/workbooks-overview/kusto.png":::


Als u op de andere **bewerkings** knoppen aan de rechter kant klikt, wordt een aantal van de belangrijkste onderdelen voor werkmappen, zoals op [prijs op basis](../visualize/workbooks-text-visualizations.md)van korting, UI-elementen voor [parameter selectie](../visualize/workbooks-parameters.md) en andere [grafiek/visualisatie typen](#visualizations)weer geven.

Het verkennen van de vooraf gemaakte sjablonen in de Bewerk modus en deze vervolgens aanpassen aan uw behoeften en uw eigen aangepaste werkmap opslaan is een uitstekende manier om te leren wat er mogelijk is met Azure Monitor-werkmappen.

## <a name="pinning-visualizations"></a>Visualisaties vastmaken

De stappen voor tekst, query's en metrische gegevens in een werkmap kunnen worden vastgemaakt met behulp van de knop vastmaken op deze items terwijl de werkmap zich in de pincode modus bevindt, of als de auteur van de werkmap instellingen heeft ingeschakeld voor dat element om het speld pictogram zichtbaar te maken.

Als u toegang wilt krijgen tot de modus pincode, klikt u op **bewerken** om de bewerkings modus in te voeren en selecteert u het pictogram blauwe pincode in de bovenste balk. Er wordt dan een afzonderlijk speld pictogram weer gegeven boven elk *invoervak* van het werkmap onderdeel aan de rechter kant van het scherm.

:::image type="content" source="./media/workbooks-overview/pin-experience.png" alt-text="Scherm opname van de pincode-ervaring." border="false":::

> [!NOTE]
> De status van de werkmap wordt opgeslagen op het moment van de pincode en vastgemaakte werkmappen op een dash board worden niet bijgewerkt als de onderliggende werkmap wordt gewijzigd. Als u een vastgemaakte werkmap onderdeel wilt bijwerken, moet u dat onderdeel verwijderen en opnieuw vastmaken.

## <a name="dashboard-time-ranges"></a>Tijd bereik van dash board

Met de vastgemaakte werkmap-onderdelen wordt het tijds bereik van het dash board in acht genomen als het vastgemaakte item is geconfigureerd voor het gebruik van een *tijds bereik* parameter. De waarde van het tijds bereik van het dash board wordt gebruikt als de waarde van de tijds bereik parameter en elke wijziging van het tijds bereik van het dash board zorgt ervoor dat het vastgemaakte item wordt bijgewerkt. Als een vastgemaakt deel het tijds bereik van het dash board gebruikt, ziet u de ondertitel van de bijgewerkte onderdeel Update om het tijds bereik van het dash board weer te geven wanneer het tijds bereik verandert.

Vastgemaakte werkmap onderdelen die gebruikmaken van een tijds bereik parameter, worden automatisch vernieuwd tegen een snelheid die wordt bepaald door het tijds bereik van het dash board. De laatste keer dat de query is uitgevoerd, wordt deze weer gegeven in de ondertitel van het vastgemaakte gedeelte.

Als een vastgemaakte stap een expliciet ingesteld tijds bereik heeft (geen tijds bereik parameter gebruikt), wordt dat tijds bereik altijd gebruikt voor het dash board, ongeacht de instellingen van het dash board. De ondertitel van het vastgemaakte deel geeft niet het tijds bereik van het dash board weer en de query wordt niet automatisch vernieuwd op het dash board. De ondertitel toont de laatste keer dat de query wordt uitgevoerd.

> [!NOTE]
> Query's die de gegevens bron voor *samen voegen* gebruiken, worden momenteel niet ondersteund bij het vastmaken aan dash boards.

## <a name="sharing-workbook-templates"></a>Werkmap Sjablonen delen

Wanneer u begint met het maken van uw eigen werkmap sjablonen, wilt u deze mogelijk delen met de bredere community. Ga naar onze [github-opslag plaats](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md)voor meer informatie en om andere sjablonen te verkennen die geen deel uitmaken van de standaard Azure monitor galerie weergave. Als u door bestaande werkmappen wilt bladeren, gaat u naar de [werkmap bibliotheek](https://github.com/microsoft/Application-Insights-Workbooks/tree/master/Workbooks) op github.

## <a name="next-step"></a>Volgende stap

* [Ga](#visualizations) voor meer informatie over werkmappen veel uitgebreide visualisaties opties.
* De toegang tot uw werkmap resources [beheren](../visualize/workbooks-access-control.md) en delen.