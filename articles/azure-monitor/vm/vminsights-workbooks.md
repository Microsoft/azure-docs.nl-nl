---
title: Interactieve rapporten maken VM Insights met werkmappen
description: Vereenvoudig complexe rapportage met vooraf gedefinieerde en aangepaste werkmappen met para meters voor VM Insights.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: bebe9424df24792f7450620657c5e2da5f08196a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102046513"
---
# <a name="create-interactive-reports-vm-insights-with-workbooks"></a>Interactieve rapporten maken VM Insights met werkmappen

Werkmappen combi neren tekst, [logboek query's](/azure/data-explorer/kusto/query/), metrische gegevens en para meters in uitgebreide interactieve rapporten. Werkmappen kunnen worden bewerkt door andere teamleden die toegang hebben tot dezelfde Azure-resources.

Werkmappen zijn handig voor scenario's zoals:

* Het gebruik van uw virtuele machine verkennen wanneer u de metrische gegevens die van belang zijn, vooraf niet kent: CPU-gebruik, schijf ruimte, geheugen, netwerk afhankelijkheden, enzovoort. In tegens telling tot andere hulpprogram ma's voor gebruiks analyse kunt u met werkmappen meerdere soorten visualisaties en analyses combi neren, waardoor ze ideaal zijn voor dit soort vrije-vorm onderzoek.
* Uitleg over uw team hoe een onlangs ingerichte virtuele machine wordt uitgevoerd, door metrische gegevens voor sleutel items en andere logboek gebeurtenissen weer te geven.
* Het delen van de resultaten van een experiment voor het wijzigen van de grootte van uw virtuele machine met andere leden van uw team. U kunt de doel stellingen voor het experiment uitleggen met tekst, vervolgens elk gebruik van metrische gegevens en analyse query's weer geven die worden gebruikt voor het evalueren van het experiment, samen met duidelijke aanroep-outs voor of elke metriek boven of onder het doel ligt.
* Rapportage van de impact van een storing in het gebruik van uw virtuele machine, het combi neren van gegevens, tekst uitleg en een bespreking van de volgende stappen om te voor komen dat er storingen in de toekomst worden vermeden.

De volgende tabel bevat een overzicht van de werkmappen die door VM Insights worden opgenomen om aan de slag te gaan.

| Werkmap | Beschrijving | Bereik |
|----------|-------------|-------|
| Prestaties | Biedt een aanpas bare versie van onze weer gave top N-lijsten en-diagrammen in één werkmap die gebruikmaakt van alle Log Analytics prestatie meter items die u hebt ingeschakeld.| Op schaal |
| Prestatiemeteritems | Een bovenste N grafiek weergave in een breed scala aan prestatie meter items. | Op schaal |
| Verbindingen | Verbindingen bieden een gedetailleerde weer gave van de inkomende en uitgaande verbindingen van uw bewaakte Vm's. | Op schaal |
| Actieve poorten | Geeft een lijst van de processen die zijn gekoppeld aan de poorten op de bewaakte Vm's en hun activiteiten in de gekozen periode. | Op schaal |
| Open poorten | Geeft het aantal poorten open op uw bewaakte Vm's en de details van die open poorten. | Op schaal |
| Mislukte verbindingen | Hier wordt het aantal mislukte verbindingen weer gegeven op uw bewaakte Vm's, de fout trend en als het percentage storingen in de loop van de tijd toeneemt. | Op schaal |
| Beveiliging en audit | Een analyse van uw TCP/IP-verkeer dat rapporteert over algemene verbindingen, schadelijke verbindingen, waarbij de IP-eind punten zich globaal bevinden.  Als u alle functies wilt inschakelen, moet u de beveiligings detectie inschakelen. | Op schaal |
| TCP Traffic | Een gerangschikt rapport voor uw bewaakte Vm's en hun verzonden, ontvangen en totale netwerk verkeer in een raster en wordt weer gegeven als een trend lijn. | Op schaal |
| Vergelijking van verkeer | Met deze werkmappen kunt u trends in het netwerk verkeer vergelijken voor één computer of een groep machines. | Op schaal |
| Prestaties | Biedt een aanpas bare versie van onze prestatie weergave die gebruikmaakt van alle Log Analytics prestatie meter items die u hebt ingeschakeld. | Eén VM | 
| Verbindingen | Verbindingen bieden een gedetailleerde weer gave van de binnenkomende en uitgaande verbindingen van uw VM. | Eén VM |
 
## <a name="creating-a-new-workbook"></a>Een nieuwe werkmap maken

Een werkmap bestaat uit secties die bestaan uit onafhankelijk Bewerk bare grafieken, tabellen, tekst en invoer besturings elementen. Voor een beter begrip van werkmappen kunt u beginnen met het openen van een sjabloon en het maken van een aangepaste werkmap door lopen. 

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Selecteer **virtual machines**.

3. Selecteer een VM in de lijst.

4. Selecteer op de pagina VM, in de sectie **bewaking** , de optie **inzichten**.

5. Selecteer op de pagina voor het maken van de VM-inzichten het tabblad **prestaties** of **kaarten** en selecteer vervolgens **werkmappen weer geven** op de koppeling op de pagina. Selecteer in de vervolg keuzelijst **naar galerie**.

    ![Scherm afbeelding van vervolg keuzelijst werkmap](media/vminsights-workbooks/workbook-dropdown-gallery-01.png)

    Hiermee opent u de werkmap galerie met een aantal vooraf gemaakte werkmappen waarmee u aan de slag kunt.

7. Maak een nieuwe werkmap door **Nieuw** te selecteren.

    ![Scherm opname van werkmap galerie](media/vminsights-workbooks/workbook-gallery-01.png)

## <a name="editing-workbook-sections"></a>Werkmapsecties bewerken

Werkmappen hebben twee modi: **bewerkings modus** en **Lees modus**. Wanneer een nieuwe werkmap voor het eerst wordt gestart, wordt deze in de **bewerkings modus** geopend. Hierin wordt alle inhoud van de werkmap weer gegeven, inclusief alle stappen en para meters die op een andere manier verborgen zijn. De **Lees modus** toont een vereenvoudigde rapport stijl weergave. Met de Lees modus kunt u de complexiteit die is opgetreden bij het maken van een rapport, opsplitsen, terwijl de onderliggende mechanismen nog maar een paar muis klikken hebben wanneer dat nodig is voor aanpassing.

![Scherm afbeelding van de sectie Virtual Machines werkmap in Azure Monitor een nieuwe werkmap weer gegeven in de bewerkings modus met bewerkings besturings elementen gemarkeerd.](media/vminsights-workbooks/workbook-new-workbook-editor-01.png)

1. Wanneer u klaar bent met het bewerken van een sectie, klikt u op **gereed bewerken** in de linkerbenedenhoek van de sectie.

2. Als u een duplicaat van een sectie wilt maken, klikt u op het pictogram **deze sectie klonen** . Het maken van dubbele secties is een uitstekende manier om een query te herhalen zonder dat vorige herhalingen verloren gaan.

3. Als u een sectie in een werkmap omhoog wilt verplaatsen, klikt u op het pictogram **omhoog** of **omlaag** .

4. Als u een sectie permanent wilt verwijderen, klikt u op het pictogram **verwijderen** .

## <a name="adding-text-and-markdown-sections"></a>Secties voor tekst en Markdown toevoegen

Door koppen, uitleg en commentaar toe te voegen aan uw werkmappen kunt u een set tabellen en grafieken omzetten in een verhalende tabel. Tekst secties in werkmappen ondersteunen de [Afkortings syntaxis](https://daringfireball.net/projects/markdown/) voor tekst opmaak, zoals kopteksten, vet, cursief en lijsten met opsommings tekens.

Als u een tekst sectie wilt toevoegen aan uw werkmap, gebruikt u de knop **tekst toevoegen** onder aan de werkmap of aan de onderkant van een sectie.

## <a name="adding-query-sections"></a>Secties voor query's toevoegen

![Query sectie in werkmappen](media/vminsights-workbooks/005-workbook-query-section.png)

Als u een query sectie wilt toevoegen aan uw werkmap, gebruikt u de knop **query toevoegen** onder aan de werkmap of aan de onderkant van een sectie.

Query secties zijn zeer flexibel en kunnen worden gebruikt om vragen te beantwoorden zoals:

* Hoe was mijn CPU-gebruik gedurende dezelfde periode als een toename in het netwerk verkeer?
* Wat was de trend in de beschik bare schijf ruimte in de afgelopen maand?
* Hoeveel problemen met de netwerk verbinding hebben mijn VM-ervaring in de afgelopen twee weken? 

U kunt ook niet alleen een query uitvoeren vanuit de context van de virtuele machine waaruit u de werkmap hebt gestart. U kunt een query uitvoeren op meerdere virtuele machines, evenals Log Analytics werk ruimten, zolang u hiervoor toegangs rechten voor deze resources hebt.

Voor het toevoegen van gegevens uit andere Log Analytics werk ruimten of van een specifieke Application Insights-app met behulp van de **werk ruimte** -id. Raadpleeg de [officiële richt lijnen](../logs/cross-workspace-query.md)voor meer informatie over query's voor meerdere resources.

### <a name="advanced-analytic-query-settings"></a>Geavanceerde instellingen voor analytische query's

Elke sectie heeft zijn eigen geavanceerde instellingen, die toegankelijk zijn via de ![ sectie instellingen werkmappen bewerkings ](media/vminsights-workbooks/006-settings.png) pictogram bevindt zich rechts van de knop **para meters toevoegen** .

![Scherm afbeelding van het dialoog venster Geavanceerde instellingen in de sectie Virtual Machines werkmap van Azure Monitor. Het pictogram waarmee het dialoog venster wordt geopend, is gemarkeerd.](media/vminsights-workbooks/007-settings-expanded.png)

|         |          |
| ---------------- |:-----|
| **Aangepaste breedte**    | Hiermee maakt u een item een wille keurige grootte, zodat u veel items op één regel kunt aanpassen, zodat u uw grafieken en tabellen beter in uitgebreide, interactieve rapporten ordent.  |
| **Voorwaardelijk zichtbaar** | Geef op of u de stappen wilt verbergen op basis van een para meter in de Lees modus. |
| **Een para meter exporteren**| Een geselecteerde rij in het raster of diagram toestaan om waarden te wijzigen of zichtbaar te maken.  |
| **Query weer geven wanneer deze niet wordt bewerkt** | Hiermee wordt de query boven de grafiek of tabel weer gegeven, zelfs in de Lees modus.
| **De knop openen in Analytics weer geven wanneer deze niet wordt bewerkt** | Hiermee voegt u het pictogram voor een blauw analyse aan de rechter bovenhoek van de grafiek toe om toegang met één klik toe te staan.|

De meeste van deze instellingen zijn redelijk intuïtief, maar om **een para meter** te kunnen exporteren, is het beter om een werkmap te onderzoeken die gebruikmaakt van deze functionaliteit.

Een van de vooraf gemaakte werkmappen- **TCP-verkeer** bevat informatie over verbindings gegevens van een virtuele machine.

De eerste sectie van de werkmap is gebaseerd op logboek query gegevens. De tweede sectie is ook gebaseerd op logboek query gegevens, maar als u een rij in de eerste tabel selecteert, wordt de inhoud van de grafieken interactief bijgewerkt:

![Scherm afbeelding van de sectie Virtual Machines in Azure Monitor waarin het vooraf gemaakte TCP-verkeer van de werkmap wordt weer gegeven.](media/vminsights-workbooks/008-workbook-tcp-traffic.png)

Het gedrag is mogelijk via het gebruik van de optie **Wanneer een item is geselecteerd, een** geavanceerde instellingen voor de para meter exporteren, die zijn ingeschakeld in de logboek query van de tabel.

![Scherm opname van het dialoog venster Geavanceerde instellingen voor een Virtual Machines werkmap met de optie ' wanneer een item is geselecteerd, een para meter exporteren ' is ingeschakeld.](media/vminsights-workbooks/009-settings-export.png)

De tweede logboek query gebruikt vervolgens de geëxporteerde waarden wanneer een rij is geselecteerd voor het maken van een set waarden die vervolgens worden gebruikt door de kop en grafieken van de sectie. Als er geen rij is geselecteerd, worden de koptekst en grafieken van de sectie verborgen. 

De verborgen para meter in de tweede sectie gebruikt bijvoorbeeld de volgende verwijzing van de rij die in het raster is geselecteerd:

```
VMConnection
| where TimeGenerated {TimeRange}
| where Computer in ("{ComputerName}") or '*' in ("{ComputerName}") 
| summarize Sent = sum(BytesSent), Received = sum(BytesReceived) by bin(TimeGenerated, {TimeRange:grain})
```

## <a name="adding-metrics-sections"></a>Secties voor metrische gegevens toevoegen

Met metrische secties beschikt u over volledige toegang tot het opnemen van Azure Monitor metrische gegevens in uw interactieve rapporten. In VM Insights bevatten de vooraf gemaakte werkmappen meestal analytische query gegevens in plaats van metrische gegevens.  U kunt ervoor kiezen om werkmappen met metrische gegevens te maken, zodat u optimaal kunt profiteren van het beste van beide functies op één plek. U hebt ook de mogelijkheid om de metrische gegevens van resources op te halen in een van de abonnementen waartoe u toegang hebt.

Hier volgt een voor beeld van gegevens van virtuele machines die worden opgehaald in een werkmap om een raster visualisatie van CPU-prestaties te bieden:

![Scherm afbeelding van de sectie Metrics van een virtuele-machine werkmap in Azure Monitor. De CPU-prestaties voor elke virtuele machine worden grafisch weer gegeven.](media/vminsights-workbooks/010-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Secties voor parameters toevoegen

Met de para meters van de werkmap kunt u waarden in de werkmap wijzigen zonder de query-of tekst secties hand matig te bewerken. Dit betekent dat het niet nodig is om inzicht te krijgen in de onderliggende analyse query taal en de potentiële doel groep van rapporten op basis van een werkmap aanzienlijk uit te breiden.

De waarden van para meters worden vervangen in de secties query, tekst of andere para meters door de naam van de para meter tussen accolades te plaatsen, zoals ``{parameterName}`` . Namen van para meters zijn beperkt tot vergelijk bare regels als Java script-id's, alfabetische tekens of onderstrepings tekens, gevolgd door een alfanumeriek of onderstrepings teken. Zo is **a1** toegestaan, maar **1a** is niet toegestaan.

Para meters zijn lineair, vanaf de bovenkant van een werkmap en doorlopend naar latere stappen.  Para meters die later in een werkmap zijn gedeclareerd, kunnen para meters overschrijven die eerder zijn gedeclareerd. Op deze manier kunt u ook para meters gebruiken om toegang te krijgen tot de waarden van eerder gedefinieerde para meters. Binnen de stap zelf van een para meter zijn para meters ook lineair, van links naar rechts, waarbij para meters aan de rechter kant kunnen afhankelijk zijn van een para meter die eerder in diezelfde stap is gedeclareerd.
 
Er zijn vier verschillende typen para meters die momenteel worden ondersteund:

|                  |      |
| ---------------- |:-----|
| **Tekst**    | Hiermee kan de gebruiker een tekstvak bewerken en kunt u optioneel een query opgeven om de standaard waarde in te vullen. |
| **Vervolgkeuzelijst** | Hiermee kan de gebruiker kiezen uit een reeks waarden. |
| **Tijds bereik kiezer**| Hiermee kan de gebruiker kiezen uit een vooraf gedefinieerde set waarden voor tijds bereik of een aangepaste periode kiezen.|
| **Resource kiezer** | Hiermee kan de gebruiker kiezen uit de resources die zijn geselecteerd voor de werkmap.|

### <a name="using-a-text-parameter"></a>Een tekst parameter gebruiken

De waarde die een gebruiker in het tekstvak typt, wordt rechtstreeks in de query vervangen zonder een Escape of aanhalings teken. Als de waarde die u nodig hebt, een teken reeks is, moet de query aanhalings tekens hebben rondom de para meter (bijvoorbeeld **{para meter}**).

Met de para meter text kan de waarde in een tekstvak overal worden gebruikt. Dit kan een tabel naam, kolom naam, functie naam, operator, enzovoort zijn.  Het type van de tekst parameter heeft de instelling **standaard waarde ophalen uit Analytics query**, waarmee de auteur van de werkmap een query kan gebruiken om de standaard waarde voor het tekstvak te vullen.

Wanneer u de standaard waarde van een logboek query gebruikt, wordt alleen de eerste waarde van de eerste rij (rij 0, kolom 0) als de standaard waarde gebruikt. Daarom is het raadzaam om uw query te beperken tot slechts één rij en één kolom. Alle andere gegevens die door de query worden geretourneerd, worden genegeerd. 

Welke waarde de query retourneert, wordt direct vervangen zonder Escape of aanhalings tekens. Als de query geen rijen retourneert, is het resultaat van de para meter een lege teken reeks (als de para meter niet is vereist) of niet gedefinieerd (als de para meter is vereist).

### <a name="using-a-drop-down"></a>Een vervolg keuzelijst gebruiken

Met het parameter type dropdown kunt u een vervolg keuzelijst maken, waardoor een of meer waarden kunnen worden geselecteerd.

De vervolg keuzelijst wordt gevuld met een logboek query of JSON. Als de query één kolom retourneert, zijn de waarden in die kolom zowel de waarde als het label in het vervolg keuzemenu. Als de query twee kolommen retourneert, is de eerste kolom de waarde en de tweede kolom is het label dat in de vervolg keuzelijst wordt weer gegeven. Als de query drie kolommen retourneert, wordt de derde kolom gebruikt om de standaard selectie in die vervolg keuzelijst aan te geven. Deze kolom kan elk wille keurig type zijn, maar het eenvoudigste is het gebruik van BOOL of numeric-typen, waarbij 0 false is en 1 True is.

Als de kolom een teken reeks type is, wordt Null/lege teken reeks als onwaar beschouwd en wordt een andere waarde als True beschouwd. Voor vervolg keuzelijsten met één selectie wordt de eerste waarde met de waarde True gebruikt als de standaard selectie.  Voor vervolg keuzelijsten met meerdere selecties worden alle waarden met de waarde True als de standaard geselecteerde set gebruikt. De items in de vervolg keuzelijst worden weer gegeven in de volg orde waarin de query rijen heeft geretourneerd. 

Laten we eens kijken naar de para meters die aanwezig zijn in het overzichts rapport verbindingen. Klik op het bewerkings symbool naast **richting**.

![Scherm afbeelding van de sectie voor het toevoegen en bewerken van rapport parameters in Azure Monitor. Het bewerkings pictogram voor de richtings parameter wordt geselecteerd.](media/vminsights-workbooks/011-workbook-using-dropdown.png)

Hiermee wordt de menu opdracht **para meter bewerken** geopend.

![Scherm afbeelding van het dialoog venster para meter bewerken. De naam van de para meter is richting, het parameter type is vervolg keuzelijst en het ophalen van gegevens uit JSON is geselecteerd.](media/vminsights-workbooks/012-workbook-edit-parameter.png)

Met de JSON kunt u een wille keurige tabel genereren die is gevuld met inhoud. De volgende JSON genereert bijvoorbeeld twee waarden in de vervolg keuzelijst:

```
[
    { "value": "inbound", "label": "Inbound"},
    { "value": "outbound", "label": "Outbound"}
]
```

Een meer van toepassing voor beeld is het gebruik van een vervolg keuzelijst voor het kiezen van een aantal prestatie meter items op naam:

```
Perf
| summarize by CounterName, ObjectName
| order by ObjectName asc, CounterName asc
| project Counter = pack('counter', CounterName, 'object', ObjectName), CounterName, group = ObjectName
```

De query geeft de resultaten als volgt weer:

![De vervolg keuzelijst voor prestatie meter items](media/vminsights-workbooks/013-workbook-edit-parameter-perf-counters.png)

De vervolg keuzelijst is uiterst krachtige hulp middelen voor het aanpassen en maken van interactieve rapporten.

### <a name="time-range-parameters"></a>Tijds bereik parameters

Hoewel u uw eigen para meter voor een aangepast tijds bereik kunt maken via het parameter type vervolg keuzelijst, kunt u ook het parameter type out-of-Box time-out gebruiken als u niet dezelfde mate van flexibiliteit nodig hebt. 

De para meters voor het tijds bereik hebben 15 standaardbereiken van vijf minuten tot de laatste 90 dagen. Er is ook een optie voor het toestaan van aangepaste tijds bereik selectie, waarmee de operator van het rapport expliciete start-en stop waarden voor het tijds bereik kan kiezen.

### <a name="resource-picker"></a>Resource kiezer

Met het parameter type voor de resource kiezer kunt u het bereik van uw rapport beperken tot bepaalde typen resources. Een voor beeld van een vooraf gemaakte werkmap die gebruikmaakt van het type resource kiezer, is de werk blad **prestaties** .

![Vervolg keuzelijst voor werk ruimten](media/vminsights-workbooks/014-workbook-edit-parameter-workspaces.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Werkmappen opslaan en delen met uw team

Werkmappen worden opgeslagen in een Log Analytics werk ruimte of een bron van een virtuele machine, afhankelijk van de manier waarop u toegang hebt tot de werkmappen-galerie. De werkmap kan worden opgeslagen in de **mijn rapporten** sectie die privé is van u of in de sectie **gedeelde rapporten** die toegankelijk is voor iedereen die toegang heeft tot de resource. Als u alle werkmappen in de resource wilt weer geven, klikt u op de knop **openen** in de actie balk.

Een werkmap delen die momenteel deel uitmaken van **mijn rapporten**:

1. Klik op **openen** in de actie balk
2. Klik op de '... ' Naast de werkmap die u wilt delen
3. Klik op **verplaatsen naar gedeelde rapporten**.

Als u een werkmap met een koppeling of via e-mail wilt delen, klikt u op **delen** in de actie balk. Houd er rekening mee dat ontvangers van de koppeling toegang nodig hebben tot deze resource in de Azure Portal om de werkmap weer te geven. Voor het maken van bewerkingen moeten ontvangers ten minste Inzender machtigingen hebben voor de resource.

Een koppeling naar een werkmap vastmaken aan een Azure-dash board:

1. Klik op **openen** in de actie balk
2. Klik op de '... ' Naast de werkmap die u wilt vastmaken
3. Klik op **Vastmaken aan dashboard**.

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure-VM-prestaties weer geven](vminsights-performance.md)om beperkingen en algemene VM-prestaties te identificeren.

- Zie de [overzichts weergave van Vm's weer geven](vminsights-maps.md)voor meer informatie over gedetecteerde toepassings afhankelijkheden.