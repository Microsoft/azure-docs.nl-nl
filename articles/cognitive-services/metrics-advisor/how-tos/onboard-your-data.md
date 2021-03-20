---
title: De gegevensfeed onboarden naar metrische gegevens Advisor
titleSuffix: Azure Cognitive Services
description: Hoe u aan de slag gaat met het voorbereiden van uw gegevensfeeds voor metrische gegevens Advisor.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: fe3b87c733f54d8bd52c4d973977e3c8cbfefe19
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92043194"
---
# <a name="how-to-onboard-your-metric-data-to-metrics-advisor"></a>Instructies: uw metrische gegevens opwaarderen naar metrische Advisor

In dit artikel vindt u meer informatie over het onboarden van uw gegevens naar metrische Advisor. 

## <a name="data-schema-requirements-and-configuration"></a>Vereisten en configuratie voor gegevensschema

[!INCLUDE [data schema requirements](../includes/data-schema-requirements.md)]

## <a name="avoid-loading-partial-data"></a>Voorkom het laden van gedeeltelijke gegevens

Gedeeltelijke gegevens worden veroorzaakt door inconsistenties tussen de gegevens die zijn opgeslagen in Metrics Advisor en de gegevens bron. Dit kan gebeuren wanneer de gegevens bron wordt bijgewerkt nadat het ophalen van gegevens is voltooid. Met metrische gegevens adviseur worden gegevens slechts eenmaal opgehaald uit een bepaalde data source.

Als er bijvoorbeeld een metriek voor de bewaking van metrische gegevens is uitgevoerd. Metrics Advisor trekt metrische gegevens bij tijds tempel A en voert anomalie detectie uit. Als de metrische gegevens van het specifieke tijds tempel A echter zijn vernieuwd nadat de gegevens zijn opgenomen. Er wordt geen nieuwe gegevens waarde opgehaald.

U kunt proberen om historische gegevens te [backfill](manage-data-feeds.md#backfill-your-data-feed) (die later worden beschreven) om inconsistenties te verhelpen, maar er worden geen nieuwe afwijkingen geactiveerd, als waarschuwingen voor deze tijd punten al zijn geactiveerd. Dit proces kan extra werk belasting aan het systeem toevoegen en is niet automatisch.

We raden u aan twee benaderingen te nemen om te voor komen dat er gedeeltelijke gegevens worden geladen:

* Gegevens in één trans actie genereren:

    Zorg ervoor dat de metrische waarden voor alle dimensie combinaties met dezelfde tijds tempel worden opgeslagen in de gegevens bron in één trans actie. In het bovenstaande voor beeld wacht u tot de gegevens van alle gegevens bronnen gereed zijn en laadt u deze vervolgens in Metrics Advisor in één trans actie. Met metrische gegevens adviseur kan de gegevensfeed regel matig worden gecontroleerd totdat de gegevens zijn opgehaald (of gedeeltelijk).

* Vertraagde gegevens opname door een juiste waarde in te stellen voor de para meter voor de **opname tijd offset** :

    Stel de offset-para meter voor de **opname tijd** voor uw gegevensfeed in om de opname te vertragen totdat de gegevens volledig zijn voor bereid. Dit kan handig zijn voor sommige gegevens bronnen die geen ondersteuning bieden voor trans acties zoals Azure Table Storage. Zie [Geavanceerde instellingen](manage-data-feeds.md#advanced-settings) voor meer informatie.

## <a name="add-a-data-feed-using-the-web-based-workspace"></a>Een gegevensfeed toevoegen via de werk ruimte op het web

Klik op **aan de slag** nadat u zich hebt aangemeld bij uw gegevens adviseur-Portal en uw werk ruimte hebt gekozen. Klik vervolgens op de hoofd pagina van de werk ruimte op **gegevensfeed toevoegen** in het menu links.

### <a name="add-connection-settings"></a>Verbindings instellingen toevoegen

Vervolgens voert u een set para meters in om verbinding te maken met de gegevens bron van uw tijd reeks. 
* **Brontype**: Het type gegevensbron waarin uw tijdreeksgegevens zijn opgeslagen.
* **Granulatie**: het interval tussen opeenvolgende gegevens punten in uw time series-gegevens. De Advisor voor metrische gegevens biedt ondersteuning voor: jaarlijks, maandelijks, wekelijks, dagelijks, per uur en aangepast. Het laagste interval dat de aanpassings optie ondersteunt, is 60 seconden.
  * **Seconden**: het aantal seconden dat *granularityName* is ingesteld om te worden *aangepast*.
* **Opname gegevens sinds (UTC)**: de begin tijd van de basis lijn voor gegevens opname. *startOffsetInSeconds* wordt vaak gebruikt om een offset toe te voegen om te helpen bij het consistentie van gegevens.

Vervolgens moet u de verbindings gegevens voor de gegevens bron opgeven en de aangepaste query's die worden gebruikt om de gegevens te converteren naar het vereiste schema. Zie [gegevensfeeds van verschillende gegevens bronnen toevoegen](../data-feeds-from-different-sources.md)voor meer informatie over de andere velden en het verbinden van verschillende soorten gegevens bronnen.

[!INCLUDE [query requirements](../includes/query-requirements.md)]

### <a name="verify-and-get-schema"></a>Schema verifiëren en ophalen

Nadat u de connection string en query reeks hebt ingesteld, selecteert u **verifiëren en schema ophalen** om de verbinding te controleren en voert u de query uit om uw gegevens schema op te halen uit de gegevens bron. Normaal gesproken duurt dit slechts enkele seconden, afhankelijk van de verbinding met de gegevensbron. Als er een fout optreedt bij deze stap, controleert u het volgende:

* Uw verbindingsreeks en query zijn juist.
* Uw Metrics Advisor-instantie kan verbinding maken met de gegevensbron als er firewallinstellingen zijn.

### <a name="schema-configuration"></a>Schemaconfiguratie

Zodra het gegevens schema is geladen, selecteert u de gewenste velden.

Als de tijds tempel van een gegevens punt wordt wegge laten, gebruikt metrische Advisor de tijds tempel wanneer het gegevens punt in plaats daarvan wordt opgenomen. Voor elke gegevensfeed kunt u Maxi maal één kolom opgeven als een tijds tempel. Als u een bericht krijgt dat een kolom niet kan worden opgegeven als tijds tempel, controleert u uw query of gegevens bron en of er meerdere tijds tempels in het query resultaat voor komen, niet alleen in de preview-gegevens. Bij het uitvoeren van een gegevens opname kan de biometrische Advisor slechts één segment (bijvoorbeeld één dag, één uur, op basis van de granulatie) van gegevens van de tijd reeks van de opgegeven bron elke keer gebruiken.

|Selectie  |Beschrijving  |Notities  |
|---------|---------|---------|
| **Weergave naam** | De naam die moet worden weer gegeven in uw werk ruimte in plaats van de oorspronkelijke kolom naam. | |
|**Tijdstempel**     | Het tijdstempel van een gegevenspunt. Als u niets invult, wordt in Metrics Advisor het tijdstempel van opname van de gegevenspunten gebruikt. Voor elke gegevensfeed kunt u Maxi maal één kolom als tijds tempel opgeven.        | Optioneel. Moet worden opgegeven met maximaal één kolom. Als u een kolom ontvangt die **niet als tijds tempel fout kan worden opgegeven** , controleert u uw query of gegevens bron op dubbele tijds tempels.      |
|**Measure**     |  De numerieke waarden in de gegevensfeed. Voor elke gegevensfeed kunt u meerdere metingen opgeven, maar u moet ten minste één kolom als meting selecteren.        | Moet worden opgegeven met minimaal één kolom.        |
|**Dimensie**     | Categorische waarden. Een combinatie van verschillende waarden identificeert een bepaalde tijdreeks met één dimensie, bijvoorbeeld: land, taal, tenant. U kunt nul of meer kolommen als dimensies selecteren. Opmerking: Wees voorzichtig bij het selecteren van een niet-teken reeks kolom als een dimensie. | Optioneel.        |
|**Negeren**     | De geselecteerde kolom negeren.        | Optioneel. Zie de onderstaande tekst.       |

Als u kolommen wilt negeren, raden we u aan om uw query of gegevens bron bij te werken om die kolommen uit te sluiten. U kunt kolommen ook negeren met **kolommen negeren** en vervolgens **negeren** voor de specifieke kolommen. Als een kolom een dimensie moet zijn en per ongeluk als *genegeerd* wordt ingesteld, kan het zijn dat metrische gegevens door Advisor worden opgenomen. Stel bijvoorbeeld dat de gegevens van uw query als volgt zijn:

| Rij-ID | Tijdstempel | Land/regio | Taal | Inkomsten |
| --- | --- | --- | --- | --- |
| 1 | 2019/11/10 | China | ZH-CN | 10.000 |
| 2 | 2019/11/10 | China | EN-US | 1000 |
| 3 | 2019/11/10 | VS | ZH-CN | 12000 |
| 4 | 2019/11/11 | VS | EN-US | 23000 |
| ... | ...| ... | ... | ... |

Als *land* een dimensie is en de *taal* wordt ingesteld op *genegeerd*, hebben de eerste en tweede rij dezelfde dimensies. Met metrische gegevens Advisor wordt een wille keurige waarde uit de twee rijen gebruikt. Met metrische gegevens adviseur worden de rijen in dit geval niet geaggregeerd.

### <a name="automatic-roll-up-settings"></a>Instellingen voor automatisch verzamelen

> [!IMPORTANT]
> Als u de analyse van de hoofd oorzaak en andere diagnostische mogelijkheden wilt inschakelen, moeten de **instellingen voor automatisch totaliseren** worden geconfigureerd. Wanneer deze functie is ingeschakeld, kunnen de instellingen voor automatisch totaliseren niet worden gewijzigd.

Met metrische gegevens adviseur kunnen automatisch aggregatie (bijvoorbeeld SUM, MAX, MIN) worden uitgevoerd voor elke dimensie tijdens opname. vervolgens wordt er een hiërarchie gemaakt die wordt gebruikt in hoofd case analyses en andere diagnostische functies. 

Denk eens na over de volgende scenario's:

* *Ik hoef niet de rollup-analyse voor mijn gegevens op te nemen.*

    Het is niet nodig om de metrische gegevens Advisor samen te gebruiken.

* *Mijn gegevens zijn al samengevouwen en de dimensie waarde wordt vertegenwoordigd door: NULL of leeg (standaard), alleen NULL, overige.*

    Met deze optie wordt aangegeven dat metrische gegevens niet in het totaal hoeven te worden samengevouwen omdat de rijen al zijn opgeteld. Als u bijvoorbeeld *alleen null* selecteert, wordt de tweede gegevensrij in het onderstaande voor beeld gezien als aggregatie van alle landen en taal en *-US*. de vierde gegevensrij met een lege waarde voor het *land* zal echter worden gezien als een gewone rij die kan duiden op onvolledige gegevens.
    
    | Land/regio | Taal | Inkomsten |
    |---------|----------|--------|
    | China   | ZH-CN    | 10.000  |
    | Null  | EN-US    | 999999 |
    | VS      | EN-US    | 12000  |
    |         | EN-US    | 5000   |

* *Ik heb metrieken Advisor nodig om mijn gegevens te totaliseren door som/max/min/Gem/AVG/Count te berekenen en te vertegenwoordigen <some string>*

    Sommige gegevens bronnen, zoals Cosmos DB of Azure Blob Storage, bieden geen ondersteuning voor bepaalde berekeningen zoals *groeperen op* of *kubus*. Met metrische gegevens adviseur beschikt u over de optie samen vouwen om automatisch een Data kubus te genereren tijdens de opname.
    Deze optie betekent dat metrische gegevens Advisor moeten worden gebruikt om de samen telling te berekenen met de algoritme die u hebt geselecteerd en de opgegeven teken reeks te gebruiken voor het samen stellen van metrische gegevens Advisor. Dit heeft geen invloed op de gegevens in uw gegevens bron.
    Stel bijvoorbeeld dat u een set Time Series hebt waarmee verkoop cijfers worden aangegeven met de dimensie (land, regio). Voor een bepaalde tijds tempel kan het er als volgt uitzien:


    | Land       | Region           | Verkoop |
    |---------------|------------------|-------|
    | Canada        | Alberta          | 100   |
    | Canada        | Brits-Columbia | 500   |
    | Verenigde Staten | Montana          | 100   |


    Nadat u automatisch samen vouwen met *Sum* hebt ingeschakeld, worden de dimensie combinaties door Advisor berekend en worden de metrische gegevens tijdens het opnemen van de opname som. Het resultaat kan zijn:

    | Land       | Region           | Verkoop |
    | ------------ | --------------- | ---- |
    | Canada        | Alberta          | 100   |
    | NULL          | Alberta          | 100   |
    | Canada        | Brits-Columbia | 500   |
    | NULL          | Brits-Columbia | 500   |
    | Verenigde Staten | Montana          | 100   |
    | NULL          | Montana          | 100   |
    | NULL          | NULL             | 700   |
    | Canada        | NULL             | 600   |
    | Verenigde Staten | NULL             | 100   |

    `(Country=Canada, Region=NULL, Sales=600)` betekent dat de som van de verkoop in Canada (alle regio's) 600 is.

    Hier volgt de trans formatie in SQL-taal.

    ```mssql
    SELECT
        dimension_1,
        dimension_2,
        ...
        dimension_n,
        sum (metrics_1) AS metrics_1,
        sum (metrics_2) AS metrics_2,
        ...
        sum (metrics_n) AS metrics_n
    FROM
        each_timestamp_data
    GROUP BY
        CUBE (dimension_1, dimension_2, ..., dimension_n);
    ```

    Houd rekening met het volgende voordat u de functie voor automatisch oprollen gebruikt:

    * Als u de *som* wilt gebruiken om uw gegevens samen te voegen, moet u ervoor zorgen dat uw waarden additief zijn in elke dimensie. Hier volgen enkele voor beelden van *niet-additieve* metrische gegevens:
      * Metrische gegevens op basis van breuken. Dit omvat de verhouding, het percentage, enzovoort. U moet bijvoorbeeld niet de werkloosheids graad van elke status toevoegen om het werkloosheids tempo van het hele land te berekenen.
      * Overlap in dimensie. Het is bijvoorbeeld niet mogelijk om het aantal personen in te voegen aan elke sport om het aantal mensen te berekenen dat als sport gaat, omdat er een overlap ping tussen de mensen is, maar één persoon kan net als in meerdere sporten.
    * De grootte van de kubus is beperkt om de status van het hele systeem te garanderen. De limiet is momenteel 1.000.000. Als uw gegevens de limiet overschrijden, mislukt de opname voor die tijds tempel.

## <a name="advanced-settings"></a>Geavanceerde instellingen

Er zijn verschillende geavanceerde instellingen voor het inschakelen van gegevens die op een aangepaste manier zijn opgenomen, zoals het opgeven van opname verschuiving of gelijktijdigheid. Zie de sectie [Geavanceerde instellingen](manage-data-feeds.md#advanced-settings) in het artikel gegevensfeed Management voor meer informatie.

## <a name="specify-a-name-for-the-data-feed-and-check-the-ingestion-progress"></a>Geef een naam op voor de gegevensfeed en controleer de voortgang van de opname
 
Geef een aangepaste naam op voor gegevensfeed op. Deze naam wordt weergegeven in uw werkruimte. Klik vervolgens op **verzenden**. Op de pagina Details gegevensfeed kunt u de voortgangs balk van de opname gebruiken om status informatie weer te geven.

:::image type="content" source="../media/datafeeds/ingestion-progress.png" alt-text="Voortgangs balk opname" lightbox="../media/datafeeds/ingestion-progress.png":::


Details van opname fout controleren: 

1. Klik op **Details weer geven**.
2. Klik op **status** en vervolgens op **mislukt** of **fout**.
3. Beweeg de muis aanwijzer over een mislukte opname en Bekijk het detail bericht dat wordt weer gegeven.

:::image type="content" source="../media/datafeeds/check-failed-ingestion.png" alt-text="Mislukte opname controleren":::

Een *mislukte* status geeft aan dat de opname van deze gegevens bron later opnieuw wordt uitgevoerd.
Een *fout* status geeft aan dat de gegevens bron niet opnieuw wordt geprobeerd door Advisor. Als u gegevens opnieuw wilt laden, moet u een backfill/opnieuw laden hand matig activeren.

U kunt ook de voortgang van een opname opnieuw laden door op **voortgang vernieuwen** te klikken. Nadat de gegevens opname is voltooid, kunt u op metrische waarden klikken en de resultaten van de anomalie detectie controleren.

## <a name="next-steps"></a>Volgende stappen
- [Uw gegevensfeeds beheren](manage-data-feeds.md)
- [Configuraties voor verschillende gegevens bronnen](../data-feeds-from-different-sources.md)
- [Metrische gegevens configureren en detectieconfiguratie afstemmen](configure-metrics.md)
