---
title: Visualisaties van Azure Monitor werkmap rasters
description: Meer informatie over alle Azure Monitor werkmap raster visualisaties.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: 114245030ba2356861b83a580f103d3b84c0eae3
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100610017"
---
# <a name="grid-visualizations"></a>Rastervisualisaties

Rasters of tabellen zijn een veelgebruikte manier om gegevens te presen teren aan gebruikers. Met werkmappen kunnen gebruikers de kolommen van het raster afzonderlijk opmaken om een uitgebreide gebruikers interface te bieden voor hun rapporten.

In het onderstaande voor beeld ziet u een raster waarin pictogrammen, Heatmaps en Spark-balken worden gecombineerd om complexe gegevens te presen teren. De werkmap bevat ook sorteren, een zoekvak en een go-to-Analytics-knop.

[![Scherm opname van raster op basis van logboek](./media/workbooks-grid-visualizations/grid.png)](./media/workbooks-grid-visualizations/grid.png#lightbox)

## <a name="adding-a-log-based-grid"></a>Een op een logboek gebaseerd raster toevoegen

1. Schakel de werkmap over naar de bewerkings modus door te klikken op het item werkbalk opdracht **bewerken** .
2. Gebruik de koppeling **query toevoegen** om een besturings element voor een logboek query toe te voegen aan de werkmap.
3. Selecteer het query type als **logboek**, resource type (bijvoorbeeld Application Insights) en de resources om het doel te bereiken.
4. Gebruik de query-editor om de KQL voor uw analyse in te voeren (bijvoorbeeld Vm's met geheugen onder een drempel waarde)
5. Visualisatie instellen op **raster**
6. Stel andere para meters in als dat nodig is, zoals het tijds bereik, de grootte, het kleuren palet en de legenda.

[![Scherm opname van raster query op basis van logboek](./media/workbooks-grid-visualizations/grid-query.png)](./media/workbooks-grid-visualizations/grid-query.png#lightbox)

## <a name="log-chart-parameters"></a>Para meters van het logboek diagram

| Parameter | Uitleg | Voorbeeld |
| ------------- |:-------------|:-------------|
| `Query Type` | Het type query dat moet worden gebruikt. | Log, Azure-resource grafiek, etc. |
| `Resource Type` | Het bron type van het doel. | Application Insights, Log Analytics of Azure-eerst |
| `Resources` | Een set resources waaruit de metrische waarde moet worden opgehaald. | MyApp1 |
| `Time Range` | Het tijd venster om de logboek grafiek weer te geven. | Vorig uur, afgelopen 24 uur, etc. |
| `Visualization` | De visualisatie die moet worden gebruikt. | Raster |
| `Size` | De verticale grootte van het besturings element. | Klein, gemiddeld, groot of volledig |
| `Query` | Een KQL-query waarmee gegevens worden geretourneerd in de indeling die wordt verwacht door de grafiek visualisatie. | _aanvragen \| samenvatte aanvragen = aantal () op naam_ |

## <a name="simple-grid"></a>Eenvoudig raster

Werkmappen kunnen KQL-resultaten weer geven als een eenvoudige tabel. In het onderstaande raster ziet u het aantal aanvragen en unieke gebruikers per aanvraag type in een app.

```kusto
requests
| where name !endswith('.eot')
| summarize Requests = count(), Users = dcount(user_Id) by name
| order by Requests desc
```

[![Scherm opname van een raster op basis van een logboek in de bewerkings modus](./media/workbooks-grid-visualizations/log-chart-simple-grid.png)](./media/workbooks-grid-visualizations/log-chart-simple-grid.png#lightbox)

## <a name="grid-styling"></a>Raster stijl

Hoewel in een tabel zonder opmaak gegevens worden weer gegeven, is het moeilijk te lezen en is inzichten niet altijd zichtbaar. Als u het raster ontwerpt, kunt u de gegevens gemakkelijker lezen en interpreteren.

Hieronder ziet u hetzelfde raster van de vorige sectie, opgemaakt als Heatmaps.

[![Scherm opname van een raster op basis van een logboek met kolommen die zijn opgemaakt als Heatmaps](./media/workbooks-grid-visualizations/log-chart-grid-heatmap.png)](./media/workbooks-grid-visualizations/log-chart-grid-heatmap.png#lightbox)

Dit is hetzelfde raster als balken: [ ![ scherm afbeelding van een raster op basis van een logboek met kolommen die als balken zijn opgemaakt](./media/workbooks-grid-visualizations/log-chart-grid-bar.png)](./media/workbooks-grid-visualizations/log-chart-grid-bar.png#lightbox)

### <a name="styling-a-grid-column"></a>Een raster kolom een stijl

1. Selecteer de knop **kolom instelling** op de werk balk query beheer.
2. Selecteer in de *kolom instellingen bewerken* de kolom die moet worden opgemaakt.
3. Kies een kolom weergave (bijvoorbeeld heatmap, balk, balk eronder enzovoort) en gerelateerde instellingen om uw kolom te opmaken.

Hieronder ziet u een voor beeld van de kolom *aanvraag* als een balk:

[![Scherm opname van een raster op basis van een logboek met een aanvraag kolom stijl als een balk.](./media/workbooks-grid-visualizations/log-chart-grid-column-settings-start.png)](./media/workbooks-grid-visualizations/log-chart-grid-column-settings-start.png#lightbox)

### <a name="column-renderers"></a>Kolom weergave

| Kolom weergave | Uitleg | Aanvullende opties |
|:------------- |:-------------|:-------------|
| `Automatic` | De standaard-maakt gebruik van de meest geschikte renderer op basis van het kolom Type.  |  |
| `Text` | Hiermee worden de kolom waarden als tekst weer gegeven. | |
| `Right Aligned` | Vergelijkbaar met tekst, behalve dat deze rechts is uitgelijnd. | |
| `Date/Time` | Hiermee wordt een lees bare datum tijd teken reeks weer gegeven. | |
| `Heatmap` | Kleuren de raster cellen op basis van de waarde van de cel. | Het kleuren palet en de minimale/maximale waarde die wordt gebruikt voor schalen. |
| `Bar` | Hiermee wordt een balk weer gegeven naast de cel op basis van de waarde van de cel. | Het kleuren palet en de minimale/maximale waarde die wordt gebruikt voor schalen. |
| `Bar underneath` | Hiermee wordt een balk weer gegeven in de buurt van de onderkant van de cel op basis van de waarde van de cel. | Het kleuren palet en de minimale/maximale waarde die wordt gebruikt voor schalen. |
| `Composite bar` | Hiermee wordt een samengestelde balk weer gegeven met de opgegeven kolommen in die rij. Raadpleeg de [samengestelde balk](workbooks-composite-bar.md) voor meer informatie. | Kolommen met bijbehorende kleuren voor het weer geven van de balk en een label die boven aan de balk worden weer gegeven. |
| `Spark bars` | Hiermee wordt een Spark-balk in de cel weer gegeven op basis van de waarden van een dynamische matrix in de cel. Bijvoorbeeld, de kolom trend van de scherm opname bovenaan. | Het kleuren palet en de minimale/maximale waarde die wordt gebruikt voor schalen. |
| `Spark lines` | Hiermee wordt een Spark-lijn in de cel weer gegeven op basis van de waarden van een dynamische matrix in de cel. | Het kleuren palet en de minimale/maximale waarde die wordt gebruikt voor schalen. |
| `Icon` | Hiermee worden pictogrammen weer gegeven op basis van de tekst waarden in de cel. Ondersteunde waarden zijn: `cancelled` , `critical` , `disabled` ,,,, `error` `failed` ,,,, `info` `none` `pending` `stopped` `question` `success` , `unknown` , `warning` `uninitialized` , `resource` , `up` , `down` `left` `right` `trendup` `trenddown` `4` `3` `2` `1` `Sev0` `Sev1` `Sev2` `Sev3` `Sev4` `Fired` `Resolved` `Available` `Unavailable` `Degraded` `Unknown` `Blank` ,,,,,,,,,,,,,,,,,,, en.|  |
| `Link` | Hiermee wordt een koppeling weer gegeven die wordt geklikt of een Configureer bare actie uitvoert. Gebruik deze optie als u wilt dat het item *alleen* een koppeling is.  Een van de andere typen kan *ook* een koppeling zijn door gebruik te maken van de `Make this item a link` instelling. Zie [koppelings acties](#link-actions) hieronder voor meer informatie. |  |
| `Location` | Hiermee wordt een beschrijvende Azure-regio naam weer gegeven op basis van een regio-id. |  |
| `Resource type` | Hiermee wordt een beschrijvende resource type teken reeks weer gegeven op basis van een resource type-id  |  |
| `Resource` | Hiermee wordt een beschrijvende resource naam en koppeling weer gegeven op basis van een resource-id  | Optie voor het weer geven van het pictogram voor het resource type  |
| `Resource group` | Hiermee wordt een beschrijvende naam van een resource groep en een koppeling op basis van een resource groep-ID weer gegeven. Als de waarde van de cel geen resource groep is, wordt deze geconverteerd naar een.  | Optie voor het weer geven van het pictogram van de resource groep  |
| `Subscription` | Hiermee wordt een beschrijvende abonnements naam en koppeling weer gegeven op basis van een abonnements-id.  Als de waarde van de cel geen abonnement is, wordt deze geconverteerd naar één.  | Optie voor het weer geven van het pictogram abonnement.  |
| `Hidden` | Hiermee wordt de kolom in het raster verborgen. Dit is handig wanneer de standaard query meer kolommen retourneert dan nodig is, maar een project niet gewenst is |  |

### <a name="link-actions"></a>Acties koppelen

Als de `Link` renderer is geselecteerd of het selectie vakje *dit item als koppeling maken* is geselecteerd, kan de auteur een koppelings actie configureren die wordt uitgevoerd bij het selecteren van de cel. Dit betekent meestal dat de gebruiker een andere weer gave met context uit de cel neemt of dat er een URL kan worden geopend.

### <a name="custom-formatting"></a>Aangepaste opmaak

Met werkmappen kunnen gebruikers ook de getalnotatie van hun celwaarden instellen. U kunt dit doen door op het selectie vakje voor *aangepaste opmaak* te klikken wanneer dit beschikbaar is.

| Opmaak optie | Uitleg |
|:------------- |:-------------|
| `Units` | De eenheden voor de kolom: diverse opties voor percentage, aantallen, tijd, byte, aantal/tijd, bytes/tijd, enzovoort. De eenheid voor de waarde 1234 kan bijvoorbeeld worden ingesteld op milliseconden en wordt weer gegeven als 1,234 s. |
| `Style` | De notatie om deze weer te geven als-decimaal, valuta, procent. |
| `Show group separator` | Selectie vakje om groeps scheidings tekens weer te geven. 1234 wordt weer gegeven als 1.234 in de Verenigde Staten. |
| `Minimum integer digits` | Minimum aantal te gebruiken integere cijfers (standaard 1). |
| `Minimum fractional digits` | Minimum aantal decimalen dat moet worden gebruikt (standaard 0).  |
| `Maximum fractional digits` | Het maximum aantal decimalen dat moet worden gebruikt. |
| `Minimum significant digits` | Minimum aantal significante cijfers dat moet worden gebruikt (standaard 1). |
| `Maximum significant digits` | Het maximum aantal significante cijfers dat moet worden gebruikt. |
| `Custom text for missing values` | Wanneer een gegevens punt geen waarde heeft, wordt deze aangepaste tekst weer gegeven in plaats van een leeg item. |

### <a name="custom-date-formatting"></a>Aangepaste datum notatie

Wanneer de auteur heeft opgegeven dat een kolom is ingesteld op de datum-/tijdnotatie, kan de auteur aangepaste opties voor datum notatie opgeven met behulp van het selectie vakje *aangepaste datum notatie* .

| Opmaak optie | Uitleg |
|:------------- |:-------------|
| `Style` | De notatie voor het weer geven van een datum als korte, lange, volledige notatie of een tijd als korte of lange tijd notaties. |
| `Show time as` | Hiermee kan de auteur bepalen of de tijd in lokale tijd (standaard) of als UTC wordt weer gegeven. Afhankelijk van de datum notatie stijl geselecteerd, worden de UTC/tijdzone gegevens mogelijk niet weer gegeven. |

## <a name="custom-column-width-setting"></a>Instelling voor aangepaste kolom breedte

De auteur kan de breedte van een kolom in het raster aanpassen met behulp van het veld *aangepaste kolom breedte* in *kolom instellingen*.

![Scherm afbeelding van kolom instellingen met het veld voor de aangepaste kolom breedte in een rood vak](./media/workbooks-grid-visualizations/custom-column-width-setting.png)

Als het veld zwart wordt gelaten, wordt de breedte automatisch bepaald op basis van het aantal tekens in de kolom en het aantal zicht bare kolommen. De standaard eenheid is ' Ch ' (tekens).

Als u op de knop Blue **(huidige breedte)** in het label klikt, wordt het tekst veld gevuld met de huidige breedte van de geselecteerde kolom. Als een waarde aanwezig is in het veld aangepaste breedte zonder maat eenheid, wordt de standaard instelling gebruikt.

De beschik bare meet eenheden zijn:

| Meeteenheid | Definitie           |
|:--------------------|:---------------------|
| ch                  | tekens (standaard) |
| px                  | pixel               |
| fr                  | gebroken eenheden     |
| %                   | percentage           |

Invoer validatie: als de validatie mislukt, ziet u een rood hulp bericht popup onder het veld, maar de gebruiker kan nog steeds de breedte Toep assen. Als er een waarde in de invoer aanwezig is, wordt deze uitgeparseerd. Als er geen geldige maat eenheid wordt gevonden, wordt de standaard waarde gebruikt.

Er is geen minimale/maximale breedte, omdat deze naar keuze van de auteur blijft. Het veld voor de aangepaste kolom breedte is uitgeschakeld voor verborgen kolommen.

## <a name="examples"></a>Voorbeelden

### <a name="spark-lines-and-bar-underneath"></a>Vonk lijnen en staaf eronder

In het onderstaande voor beeld ziet u het aantal aanvragen en de trend op aanvraag naam.

```kusto
requests
| make-series Trend = count() default = 0 on timestamp from ago(1d) to now() step 1h by name
| project-away timestamp
| join kind = inner (requests
    | summarize Requests = count() by name
    ) on name
| project name, Requests, Trend
| order by Requests desc
```

[![Scherm afbeelding van een logboek op basis van een raster met een streep eronder en een Spark-lijn](./media/workbooks-grid-visualizations/log-chart-grid-spark-line.png)](./media/workbooks-grid-visualizations/log-chart-grid-spark-line.png#lightbox)

### <a name="heatmap-with-shared-scales-and-custom-formatting"></a>Heatmap met gedeelde schalen en aangepaste opmaak

Dit voor beeld toont diverse metrische gegevens over de duur van aanvragen en het aantal. In de heatmap-renderer wordt gebruikgemaakt van de minimum waarden die zijn ingesteld in instellingen of worden de minimum-en maximum waarde voor de kolom berekend en wordt een achtergrond kleur van het geselecteerde palet voor de cel toegewezen op basis van de waarde van de cel ten opzichte van de minimum-en maximum waarde van de kolom.

```
requests
| summarize Mean = avg(duration), (Median, p80, p95, p99) = percentiles(duration, 50, 80, 95, 99), Requests = count() by name
| order by Requests desc
```

[![Scherm opname van een raster op basis van een logboek met een heatmap met een gedeelde schaal over kolommen](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale.png)](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale.png#lightbox)

In het bovenstaande voor beeld wordt een gedeeld palet (groen of rood) en schaal gebruikt voor het kleuren van de kolommen (gemiddelde, mediaan, P80, P95 en P99). Een apart palet (blauw) dat wordt gebruikt voor de aanvraag kolom.

#### <a name="shared-scale"></a>Gedeelde schaal

Een gedeelde schaal ophalen:

1. Gebruik reguliere expressies om meer dan één kolom te selecteren waarop een instelling moet worden toegepast. Stel bijvoorbeeld de kolom naam in om `Mean|Median|p80|p95|p99` deze allemaal te selecteren.
2. De standaard instellingen voor de afzonderlijke kolommen verwijderen.

Dit zorgt ervoor dat de nieuwe instelling voor meerdere kolommen de instellingen toepast op het toevoegen van een gedeelde schaal.

[![Scherm opname van een raster instelling op basis van een logboek voor het verkrijgen van een gedeelde schaal in kolommen](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale-settings.png)](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale-settings.png#lightbox)

### <a name="icons-to-represent-status"></a>Pictogrammen die de status vertegenwoordigen

In het onderstaande voor beeld ziet u de aangepaste status van aanvragen op basis van de duur van de P95.

```
requests
| summarize p95 = percentile(duration, 95) by name
| order by p95 desc
| project Status = case(p95 > 5000, 'critical', p95 > 1000, 'error', 'success'), name, p95
```

[![Scherm opname van een raster op basis van een logboek met een heatmap met een gedeelde schaal over kolommen met behulp van de bovenstaande query.](./media/workbooks-grid-visualizations/log-chart-grid-icons.png)](./media/workbooks-grid-visualizations/log-chart-grid-icons.png#lightbox)

Ondersteunde pictogram namen zijn: `cancelled` , `critical` , `disabled` , `error` , `failed` , `info` , `none` , `pending` , `stopped` `question` `success` `unknown` `warning` `uninitialized` `resource` `up` `down` `left` `right` `trendup` `trenddown` `4` `3` `2` `1` `Sev0` `Sev1` `Sev2` `Sev3` `Sev4` `Fired` `Resolved` `Available` `Unavailable` `Degraded` `Unknown` `Blank` ,,,,,,,,,,,,,,,,,,,,,,,,,, en.

### <a name="using-thresholds-with-links"></a>Drempel waarden gebruiken met koppelingen

In de onderstaande instructies ziet u hoe u drempels kunt gebruiken met koppelingen om pictogrammen toe te wijzen en verschillende werkmappen te openen. Met elke koppeling in het raster wordt een andere werkmap sjabloon voor die Application Insights resource geopend.

1. Schakel de werkmap over naar de bewerkings modus door de werk balk item *bewerken* te selecteren.
2. Selecteer **toevoegen** en vervolgens *query toevoegen*.
3. Wijzig de *gegevens bron* in JSON en *visualisatie* in grid.
4. Voer de volgende query in.

```json
[ 
    { "name": "warning", "link": "Community-Workbooks/Performance/Performance Counter Analysis" },
    { "name": "info", "link": "Community-Workbooks/Performance/Performance Insights" },
    { "name": "error", "link": "Community-Workbooks/Performance/Apdex" }
]
```

5. Voer de query uit.
6. Selecteer **kolom instellingen** om de instellingen te openen.
7. Selecteer ' naam ' uit *kolommen*.
8. Kies onder *kolom weergave* de optie drempels.
9.  Voer de volgende *drempel* waarden in en kies deze.
   
    | Operator | Waarde   | Pictogrammen   |
    |----------|---------|---------|
    | ==       | waarschuwing | Waarschuwing |
    | ==       | fout   | Mislukt  |

    ![Scherm afbeelding van het tabblad instellingen van kolom bewerken met de bovenstaande instellingen.](./media/workbooks-grid-visualizations/column-settings.png)

    Laat de standaard rij ongewijzigd. U kunt ook de tekst opgeven die u wilt. De tekst kolom krijgt een teken reeks indeling als invoer en vult deze met de waarde en eenheid van de kolom, indien opgegeven. Als bijvoorbeeld een waarschuwing de kolom waarde ' {0} {1} link! ' is, wordt deze weer gegeven als waarschuwings koppeling!.
10. Selecteer het vak *dit item een koppeling maken* .
    1. Kies werkmap (sjabloon) onder *weer te geven venster*.
    2. Kies onder *koppelings waarde is* de optie koppeling.
    3. Selecteer het vak *koppeling openen in context Blade* .
    4. Kies de volgende instellingen in de *werkmap koppelings instellingen*
        1. Kies onder *sjabloon-id is* de optie kolom.
        2. Kies koppelen onder *kolom* .

    ![Scherm opname van koppelings instellingen met de bovenstaande instellingen.](./media/workbooks-grid-visualizations/make-this-item-a-link.png)

11. Selecteer koppelen uit *kolommen*. Onder instellingen naast *kolom weergave* selecteert u **(kolom verbergen)**.
1. Als u de weergave naam van de kolom naam wilt wijzigen, selecteert u het tabblad **labels** . Voer in de rij met de *kolom-id* onder * kolom Label de naam in die u wilt weer geven.
2. Selecteer **Toep assen**

![Scherm opname van drempel waarden in raster met de bovenstaande instellingen](./media/workbooks-grid-visualizations/thresholds-workbooks-links.png)

## <a name="fractional-units-percentages"></a>Percentages van gebroken eenheden

De Fractie eenheid (FR) is een veelgebruikte dynamische maat eenheid in verschillende typen rasters. Naarmate het formaat/de resolutie van het venster verandert, wordt de FR breedte ook gewijzigd.

In de onderstaande scherm afbeelding ziet u een tabel met acht kolommen met een breedte van 1fr en alle gelijke breedten. Wanneer de venster grootte wordt gewijzigd, wordt de breedte van elke kolom proportioneel gewijzigd.

[![Scherm opname van kolommen in raster met de kolom breedte waarde 1fr elke](./media/workbooks-grid-visualizations/custom-column-width-fr.png)](./media/workbooks-grid-visualizations/custom-column-width-fr.png#lightbox)

In de onderstaande afbeelding ziet u dezelfde tabel, met uitzonde ring van de eerste kolom is ingesteld op breedte 50%. Hiermee wordt de kolom in de helft van de totale raster breedte dynamisch ingesteld. Als u het formaat van het venster wijzigt, blijft de breedte van 50% behouden, tenzij de grootte van het venster te klein wordt. Deze dynamische kolommen hebben een minimum breedte op basis van de inhoud. De resterende 50% van het raster wordt opgedeeld op basis van de acht totale deel eenheden. De kolom ' kind ' is ingesteld op 2FR, zodat deze een vierde van de resterende ruimte in beslag neemt. Aangezien de andere kolommen 1fr zijn, nemen ze elk een-achtste van de rechter helft van het raster.

[![Scherm afbeelding van kolommen in raster met 1 kolom breedte waarde van 50% en de rest als 1fr](./media/workbooks-grid-visualizations/custom-column-width-fr2.png)](./media/workbooks-grid-visualizations/custom-column-width-fr2.png#lightbox)

De combi natie van FR,%, px en CH breedten is mogelijk en werkt op dezelfde manier als de vorige voor beelden. De breedten die zijn ingesteld door de statische eenheden (CH en px) zijn vaste constanten die niet worden gewijzigd, zelfs niet als het venster of de resolutie wordt gewijzigd. De kolommen die door% zijn ingesteld, nemen het percentage op basis van de totale raster breedte (mogelijk niet exact als gevolg van eerdere minimum breedten). De kolommen die met fr zijn ingesteld, splitsen de resterende raster ruimte op basis van het aantal gebroken eenheden dat ze toegewezen hebben.

[![Scherm opname van kolommen in raster met assortiment van verschillende breedte-eenheden die worden gebruikt](./media/workbooks-grid-visualizations/custom-column-width-fr3.png)](./media/workbooks-grid-visualizations/custom-column-width-fr3.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het maken [van een structuur in werkmappen](workbooks-tree-visualizations.md).
* Meer informatie over het maken van [tekst parameters](workbooks-text.md)voor een werkmap.