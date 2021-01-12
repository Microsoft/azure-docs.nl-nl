---
title: Koppelings acties voor Azure Monitor werkmappen
description: Koppelings acties gebruiken in Azure Monitor werkmappen
ms.topic: conceptual
ms.date: 01/07/2021
author: lgayhardt
ms.author: lagayhar
ms.openlocfilehash: 3e94f1a70d5fa2a6e132dc6dc6f95439959b07f0
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98122122"
---
# <a name="link-actions"></a>Koppelings acties

Koppelings acties kunnen worden geopend via werkmap koppelings stappen of via kolom instellingen van [rasters](workbooks-grid-visualizations.md), [titels](workbooks-tile-visualizations.md)of [grafieken](workbooks-graph-visualizations.md).

## <a name="general-link-actions"></a>Algemene koppelings acties

| Koppelings actie | Actie bij klikken |
|:------------- |:-------------|
| `Generic Details` | Toont de rijwaarden in een eigenschappen raster context weergave. |
| `Cell Details` | Toont de celwaarde in een eigenschappen raster context weergave. Dit is handig wanneer de cel een dynamisch type bevat met informatie (bijvoorbeeld JSON met aanvraag eigenschappen zoals locatie, rolinstantie, enzovoort). |
| `Url` | De waarde van de cel verwacht een geldige http-URL en de cel is een koppeling waarmee die URL wordt geopend op een nieuw tabblad.|

## <a name="application-insights"></a>Application Insights

| Koppelings actie | Actie bij klikken |
|:------------- |:-------------|
| `Custom Event Details` | Hiermee opent u de Application Insights Zoek Details met de aangepaste gebeurtenis-ID ( `itemId` ) in de cel. |
| `* Details` | Vergelijkbaar met details van aangepaste gebeurtenissen, met uitzonde ring van afhankelijkheden, uitzonde ringen, pagina weergaven, aanvragen en traceringen. |
| `Custom Event User Flows` | Hiermee opent u de Application Insights Gebruikersstromen ervaring die is gedraaid op de aangepaste gebeurtenis naam in de cel. |
| `* User Flows` | Vergelijkbaar met aangepaste gebeurtenis Gebruikersstromen, behalve voor uitzonde ringen, pagina weergaven en aanvragen. |
| `User Timeline` | Hiermee opent u de gebruikers tijdlijn met de gebruikers-ID ( `user_Id` ) in de cel. |
| `Session Timeline` | Hiermee opent u de Application Insights Zoek ervaring voor de waarde in de cel (bijvoorbeeld zoek naar tekst ' ABC ' waarbij ABC de waarde in de cel is). |

`*` Hiermee wordt een Joker teken voor de bovenstaande tabel aangegeven

## <a name="azure-resource"></a>Azure-resource

| Koppelings actie | Actie bij klikken |
|:------------- |:-------------|
| `ARM Deployment` | Een Azure Resource Manager-sjabloon implementeren.  Wanneer dit item is geselecteerd, worden er extra velden weer gegeven waarmee de auteur kan configureren welke Azure Resource Manager sjabloon moet worden geopend, de para meters voor de sjabloon enzovoort. [Zie instellingen voor de implementatie koppeling Azure Resource Manager](#azure-resource-manager-deployment-link-settings). |
| `Create Alert Rule` | Hiermee maakt u een waarschuwings regel voor een resource.  |
| `Custom View` | Hiermee opent u een aangepaste weer gave. Wanneer dit item is geselecteerd, worden er extra velden weer gegeven waarmee de auteur de weergave-uitbrei ding, de weergave naam en eventuele para meters voor het openen van de weer gave kan configureren. [Zie aangepaste weer gave](#custom-view-link-settings). |
| `Metrics` | Hiermee opent u een weer gave met metrische gegevens.  |
| `Resource overview` | Open de weer gave van de resource in de portal op basis van de resource-ID-waarde in de cel. De auteur kan ook optioneel een waarde instellen `submenu` waarmee een specifiek menu-item in de resource weergave wordt geopend. |
| `Workbook (template)` | Open een werkmap sjabloon.  Wanneer dit item is geselecteerd, worden er extra velden weer gegeven waarmee de auteur kan configureren welke sjabloon moet worden geopend, enzovoort.  |

## <a name="link-settings"></a>Koppelings instellingen

Wanneer u de koppelings weergave gebruikt, zijn de volgende instellingen beschikbaar:

![Scherm opname van koppelings instellingen](./media/workbooks-link-actions/link-settings.png)

| Instelling | Uitleg |
|:------------- |:-------------|
| `View to open` | Hiermee kan de auteur een van de hierboven opgesomde acties selecteren. |
| `Menu item` | Als ' resource overzicht ' is geselecteerd, is dit de menu opdracht in het overzicht van de resource dat u wilt openen. Dit kan worden gebruikt voor het openen van waarschuwingen of activiteiten Logboeken in plaats van de ' overzicht ' voor de resource. De waarden voor menu-items verschillen per Azure `Resourcetype` .|
| `Link label` | Indien opgegeven, wordt deze waarde weer gegeven in de raster kolom. Als deze waarde niet is opgegeven, wordt de waarde van de cel weer gegeven. Als u wilt dat een andere waarde wordt weer gegeven, zoals een heatmap of-pictogram, gebruikt u de `Link` renderer niet, gebruikt u in plaats daarvan de juiste renderer en selecteert u de `Make this item a link` optie. |
| `Open link in Context Blade` | Als deze is opgegeven, wordt de koppeling geopend als een pop-upvenster ' context ' aan de rechter kant van de Window in plaats van een volledige weer gave te openen. |

Wanneer u de `Make this item a link` optie gebruikt, zijn de volgende instellingen beschikbaar:

| Instelling | Uitleg |
|:------------- |:-------------|
| `Link value comes from` | Wanneer een cel wordt weer gegeven als een renderer met een koppeling, wordt in dit veld aangegeven waar de waarde ' link ' moet worden gebruikt voor de koppeling, waardoor de auteur kan kiezen uit een vervolg keuzelijst van de andere kolommen in het raster. De cel kan bijvoorbeeld een heatmap-waarde zijn, maar u wilt dat de koppeling het resource overzicht opent voor de resource-ID in de rij. In dat geval moet u de koppelings waarde instellen op basis van het `Resource Id` veld.
| `View to open` | hetzelfde als hierboven. |
| `Menu item` | hetzelfde als hierboven. |
| `Open link in Context Blade` | hetzelfde als hierboven. |

## <a name="azure-resource-manager-deployment-link-settings"></a>Instellingen voor koppeling van Azure Resource Manager-implementatie

Als het geselecteerde koppelings type is, `ARM Deployment` moet de auteur extra instellingen opgeven om een Azure Resource Manager-implementatie te openen. Er zijn twee hoofd tabbladen voor configuraties.

### <a name="template-settings"></a>Sjabloon instellingen

In deze sectie wordt gedefinieerd waar de sjabloon vandaan komt en de para meters die worden gebruikt om de Azure Resource Manager-implementatie uit te voeren.

| Bron | Uitleg |
|:------------- |:-------------|
|`Resource group id comes from` | De resource-ID wordt gebruikt om geïmplementeerde resources te beheren. Het abonnement wordt gebruikt om geïmplementeerde resources en kosten te beheren. De resource groepen worden gebruikt als mappen voor het organiseren en beheren van al uw resources. Als deze waarde niet is opgegeven, mislukt de implementatie. Selecteer uit `Cell` , `Column` , `Static Value` of `Parameter` in [koppelings bronnen](#link-sources).|
|`ARM template URI from` | De URI naar de Azure Resource Manager sjabloon zelf. De URL van de sjabloon moet toegankelijk zijn voor de gebruikers die de sjabloon zullen implementeren. Selecteer uit `Cell` , `Column` , `Parameter` of `Static Value`  in [koppelings bronnen](#link-sources). Raadpleeg voor starters onze [Snelstartgids-sjablonen](https://azure.microsoft.com/resources/templates/).|
|`ARM Template Parameters` | In deze sectie worden de sjabloon parameters gedefinieerd die worden gebruikt voor de hierboven gedefinieerde sjabloon-URI. Deze para meters worden gebruikt om de sjabloon te implementeren op de pagina uitvoeren. Het raster bevat een knop voor het uitvouwen van een werk balk om de para meters in te vullen met de namen die zijn gedefinieerd in de sjabloon-URI en stel deze in op statische lege waarden. Deze optie kan alleen worden gebruikt wanneer er geen para meters in het raster staan en de sjabloon-URI is ingesteld. In het onderste gedeelte ziet u een voor beeld van de parameter uitvoer. Selecteer vernieuwen om de preview bij te werken met de huidige wijzigingen. Para meters zijn doorgaans waarden, terwijl verwijzingen iets zijn dat kan wijzen naar sleutel kluis geheimen waarmee de gebruiker toegang heeft. <br/><br/> **Beperking Blade voor sjabloon Viewer** : Hiermee worden de referentie parameters niet correct weer gegeven en worden ze als null/waarde vermeld, zodat gebruikers de verwijzings parameters niet op de juiste wijze kunnen implementeren vanuit het tabblad sjabloon viewer.|

![Scherm opname van Azure Resource Manager sjabloon instellingen](./media/workbooks-link-actions/template-settings.png)

### <a name="ux-settings"></a>UX-instellingen

In deze sectie configureert u wat de gebruikers te zien krijgen voordat ze de Azure Resource Manager-implementatie uitvoeren.

| Bron | Uitleg |
|:------------- |:-------------|
|`Title from` | De titel die wordt gebruikt in de weer gave uitvoeren. Selecteer uit `Cell` , `Column` , `Parameter` of `Static Value` in [koppelings bronnen](#link-sources).|
|`Description from` | Dit is de geprijsde tekst die wordt gebruikt om gebruikers een nuttige beschrijving te geven wanneer ze de sjabloon willen implementeren. Selecteer uit `Cell` , `Column` , `Parameter` of `Static Value`  in [koppelings bronnen](#link-sources). <br/><br/> **Opmerking:** Als `Static Value` is geselecteerd, wordt er een tekstvak met meerdere regels weer gegeven. In dit tekstvak kunt u de para meters omzetten met `{paramName}` . U kunt ook kolommen als para meters behandelen door toe te voegen `_column` na de kolom naam zoals `{columnName_column}` . In de onderstaande voorbeeld afbeelding kunnen we naar de kolom verwijzen `VMName` door te schrijven `{VMName_column}` . De waarde na de dubbele punt is de [parameter formatter](workbooks-parameters.md#parameter-options), in dit geval `value` .|
|`Run button text from` | Label dat wordt gebruikt voor het implementeren van de Azure Resource Manager-sjabloon op de knop uitvoeren (uitvoeren). Dit is wat gebruikers gaan selecteren om te beginnen met de implementatie van de Azure Resource Manager sjabloon.|

![Scherm opname van Azure Resource Manager UX-instellingen](./media/workbooks-link-actions/ux-settings.png)

Wanneer deze configuraties zijn ingesteld en de gebruiker de koppeling selecteert, wordt de weer gave geopend met de UX die wordt beschreven in de UX-instellingen. Als de gebruiker het selecteert `Run button text from` , wordt een Azure Resource Manager sjabloon geïmplementeerd met de waarden uit de [sjabloon instellingen](#template-settings). Met weergave sjabloon opent u het tabblad sjabloon viewer waarmee de gebruiker de sjabloon en de para meters kan controleren voordat deze wordt geïmplementeerd.

![Scherm opname van uitvoeren Azure Resource Manager weer geven](./media/workbooks-link-actions/run-tab.png)

## <a name="custom-view-link-settings"></a>Instellingen voor aangepaste weergave koppeling

Hiermee opent u aangepaste weer gaven in de Azure Portal. Controleer alle configuratie-en instellingen. Onjuiste waarden veroorzaken fouten in de portal of de weer gaven worden niet correct geopend. Er zijn twee manieren om de instellingen te configureren via de `Form` of `URL` .

> [!NOTE]
> Weer gaven met een menu kunnen niet worden geopend op het tabblad context. Als een weer gave met een menu zodanig is geconfigureerd dat deze op een context tabblad wordt geopend, wordt er geen context tabblad weer gegeven wanneer de koppeling is geselecteerd.

### <a name="form"></a>Formulier

| Bron | Uitleg |
|:------------- |:-------------|
|`Extension name` | De naam van de uitbrei ding die als host fungeert voor de naam van de weer gave.|
|`View name` | De naam van de weer gave die u wilt openen.|

#### <a name="view-inputs"></a>Invoer weer geven

Er zijn twee soorten invoer, rasters en JSON. Gebruiken `Grid` voor eenvoudige invoer van sleutel en waarde of selecteren `JSON` om een geneste JSON-invoer op te geven.

- Raster
    - `Parameter Name`: De naam van de invoer parameter voor weer gave.
    - `Parameter Comes From`: Waarvan de waarde van de para meter View moet afkomstig zijn. Selecteer uit `Cell` , `Column` , `Parameter` of `Static Value` in [koppelings bronnen](#link-sources).
    > [!NOTE]
    > Als `Static Value` is geselecteerd, kunnen de para meters worden omgezet met behulp van de koppeling tussen haakjes `{paramName}` in het tekstvak. Kolommen kunnen worden behandeld als parameter kolommen door toe te voegen `_column` na de kolom naam zoals `{columnName_column}` .

    - `Parameter Value`: `Parameter Comes From` Dit is een vervolg keuzelijst met beschik bare para meters, kolommen of een statische waarde.

    ![Scherm afbeelding van de instelling kolom bewerken toont aangepaste weer gave-instellingen van het formulier.](./media/workbooks-link-actions/custom-tab-settings.png)
- JSON
    - Geef de invoer van het tabblad op in een JSON-indeling in de editor. Net als de `Grid` modus, para meters en kolommen kan worden verwezen met behulp `{paramName}` van voor-para meters en `{columnName_column}` kolommen. Als u selecteert `Show JSON Sample` , wordt de verwachte uitvoer van alle omgezette para meters en kolommen gebruiker voor de weergave-invoer weer gegeven.

    ![Scherm opname van open aangepaste weer gave-instellingen met weer gave-invoer voor JSON.](./media/workbooks-link-actions/custom-tab-json.png)

### <a name="url"></a>URL

Plak een portal-URL met de uitbrei ding, de naam van de weer gave en alle invoer gegevens die nodig zijn om de weer gave te openen. Nadat u hebt geselecteerd `Initialize Settings` , wordt het `Form` voor de auteur gevuld om een van de weer gave-invoer toe te voegen/te wijzigen of te verwijderen.

![Scherm opname van de kolom instelling aangepaste weer gave-instellingen weer geven van URL. ](./media/workbooks-link-actions/custom-tab-settings-url.png)

## <a name="workbook-template-link-settings"></a>Koppelings instellingen van werkmap (sjabloon)

Als het geselecteerde type koppeling is `Workbook (Template)` , moet de auteur aanvullende instellingen opgeven om de juiste werkmap sjabloon te openen. De onderstaande instellingen bevatten opties voor het vinden van de juiste waarde voor elk van de instellingen van het raster.

| Instelling | Uitleg |
|:------------- |:-------------|
| `Workbook owner Resource Id` | Dit is de resource-ID van de Azure-resource die ' eigenaar ' is van de werkmap. Doorgaans is het een Application Insights resource of een Log Analytics-werk ruimte. In Azure Monitor kan dit ook de letterlijke teken reeks zijn `"Azure Monitor"` . Wanneer de werkmap is opgeslagen, is dit de koppeling naar de werkmap. |
| `Workbook resources` | Een matrix met Azure-resource-Id's waarmee de standaard resource wordt opgegeven die in de werkmap wordt gebruikt. Als de sjabloon die wordt geopend bijvoorbeeld de metrische gegevens van de virtuele machine bevat, worden de waarden hier ook de resource-Id's van de virtuele machine.  Vaak worden de eigenaar en de resources ingesteld op dezelfde instellingen. |
| `Template Id` | Geef de ID op van de sjabloon die moet worden geopend. Als dit een community-sjabloon is uit de galerie (het meest voorkomende geval), moet u het pad voor het sjabloon opgeven met `Community-` , zoals `Community-Workbooks/Performance/Apdex` voor de `Workbooks/Performance/Apdex` sjabloon. Als dit een koppeling naar een opgeslagen werkmap/sjabloon is, is dit de volledige Azure-Resource-ID van het item. |
| `Workbook Type` | Geef het soort werkmap sjabloon op dat moet worden geopend. De meest voorkomende gevallen gebruiken de `Default` or- `Workbook` optie om de waarde in de huidige werkmap te gebruiken. |
| `Gallery Type` | Hiermee geeft u het Galerie type op dat wordt weer gegeven in de weer gave ' galerie ' van de sjabloon die wordt geopend. De meest voorkomende gevallen gebruiken de `Default` or- `Workbook` optie om de waarde in de huidige werkmap te gebruiken. |
|`Location comes from` | Het locatie veld moet worden opgegeven als u een specifieke werkmap resource opent. Als er geen locatie is opgegeven, is het zoeken naar de inhoud van de werkmap veel langzamer. Als u de locatie kent, geeft u deze op. Als u de locatie niet weet of een sjabloon opent die geen specifieke locatie heeft, laat u dit veld de standaard instelling.|
|`Pass specific parameters to template` | Selecteer deze optie om specifieke para meters door te geven aan de sjabloon. Als u dit selectie vakje inschakelt, worden alleen de opgegeven para meters aan de sjabloon door gegeven. alle para meters in de huidige werkmap worden door gegeven aan de sjabloon. in dat geval moeten de parameter *namen* in beide werkmappen hetzelfde zijn om deze parameter waarde te kunnen gebruiken.|
|`Workbook Template Parameters` | In deze sectie worden de para meters gedefinieerd die worden door gegeven aan de doel sjabloon. De naam moet overeenkomen met de naam van de para meter in de doel sjabloon. Selecteer een waarde in `Cell` , `Column` , en `Parameter` `Static Value` . Naam en waarde mogen niet leeg zijn om deze para meter door te geven aan de doel sjabloon.|

Voor elk van de bovenstaande instellingen moet de auteur kiezen waar de waarde in de gekoppelde werkmap afkomstig is. Zie [koppelings bronnen](#link-sources)

Wanneer de werkmap koppeling wordt geopend, wordt de nieuwe werkmap weergave door gegeven aan alle waarden die zijn geconfigureerd in de bovenstaande instellingen.

![Scherm opname van werkmap koppelings instellingen](./media/workbooks-link-actions/workbook-link-settings.png)

![Scherm afbeelding van de instellingen van de sjabloon parameters](./media/workbooks-link-actions/workbook-template-link-settings-parameter.png)

## <a name="link-sources"></a>Bronnen koppelen

| Bron | Uitleg |
|:------------- |:-------------|
| `Cell` | Hiermee wordt de waarde in die cel in het raster als koppelings waarde gebruikt |
| `Column` | Als u dit selectie vakje inschakelt, wordt er een ander veld weer gegeven waarmee de auteur een andere kolom in het raster kan selecteren.  De waarde van die kolom voor de rij wordt gebruikt in de waarde van de koppeling. Dit wordt vaak gebruikt om elke rij van een raster een andere sjabloon te laten openen, door `Template Id` veld in te stellen `column` of om dezelfde werkmap sjabloon voor verschillende resources te openen als het `Workbook resources` veld is ingesteld op een kolom die een Azure-resource-id bevat |
| `Parameter` | Als u dit selectie vakje inschakelt, wordt er een ander veld weer gegeven waarmee de auteur een para meter kan selecteren. De waarde van die para meter wordt gebruikt voor de waarde wanneer op de koppeling wordt geklikt |
| `Static value` | Als u dit selectie vakje inschakelt, wordt er een ander veld weer gegeven om het type auteur in te stellen op een statische waarde die wordt gebruikt in de gekoppelde werkmap. Dit wordt meestal gebruikt wanneer alle rijen in het raster dezelfde waarde voor een veld gebruiken. |
| `Step` | Gebruik de waarde die is ingesteld in de huidige stap van de werkmap. Dit is gebruikelijk in de stappen query en metrische gegevens om de werkmap resources in de gekoppelde werkmap in te stellen op die *in de stap query/metrische gegevens*, niet in de huidige werkmap |
| `Workbook` | Gebruik de waarde die in de huidige werkmap is ingesteld. |
| `Default` | Gebruik de standaard waarde die moet worden gebruikt als er geen waarde is opgegeven. Dit is gebruikelijk voor het Galerie type, waarbij de standaard galerie wordt ingesteld op het type van de eigenaar-resource |

## <a name="next-steps"></a>Volgende stappen

- De toegang tot uw werkmap resources [beheren](workbooks-access-control.md) en delen.
- Meer informatie over het gebruik [van groepen in werkmappen](workbooks-groups.md).