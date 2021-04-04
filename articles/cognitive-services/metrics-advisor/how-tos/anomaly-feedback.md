---
title: Afwijkende feedback geven aan de service voor metrische gegevens Advisor
titleSuffix: Azure Cognitive Services
description: Meer informatie over het verzenden van feedback op afwijkingen die worden gevonden door uw metrische Advisor-instantie en het afstemmen van de resultaten.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: mbullwin
ms.openlocfilehash: 96a6dfebdd0047d5a6f4ff3e295ab96dd8c7226a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96184854"
---
# <a name="provide-anomaly-feedback"></a>Afwijkende feedback geven

Feedback van gebruikers is een van de belangrijkste methoden om defecten binnen het anomalie detectie systeem te ontdekken. Hier bieden we een manier om gebruikers in staat te stellen onjuiste detectie resultaten rechtstreeks op een tijd reeks te markeren en de feedback onmiddellijk toe te passen. Op deze manier kan een gebruiker het afwijkings detectie systeem een afwijkings detectie uitvoeren voor een specifieke tijd reeks via actieve interacties. 

> [!NOTE]
> Momenteel heeft feedback alleen invloed op de resultaten van anomalie detectie door **Slimme detectie** , maar niet op een **harde drempel** waarde en **drempel waarde voor wijzigingen**.

## <a name="how-to-give-time-series-feedback"></a>Feedback over de tijd reeks geven

U kunt feedback geven via de detail pagina van de metrische gegevens op elke wille keurige serie. Selecteer een wille keurig punt. u ziet nu het dialoog venster met feedback. Hier ziet u de afmetingen van de reeks die u hebt gekozen. U kunt dimensie waarden opnieuw selecteren of zelfs een deel ervan verwijderen om een batch van Time Series-gegevens te verkrijgen. Nadat u een tijd reeks hebt gekozen, selecteert u de knop **toevoegen** om de feedback toe te voegen. er zijn vier soorten feedback die u kunt geven. Als u meerdere feedback items wilt toevoegen, selecteert u de knop **Opslaan** zodra u uw aantekeningen hebt voltooid.

:::image type="content" source="../media/feedback/click-on-any-point.png" alt-text="Grafiek met gegevens van een tijd reeks met een blauwe lijn en rode punten op verschillende punten. Rood vak rond één punt met de tekst: Selecteer een wille keurig punt":::

:::image type="content" source="../media/feedback/select-or-remove.png" alt-text="Het dialoog venster feedback toevoegen met twee dimensies en de optie voor het selecteren of verwijderen van dimensies en het toevoegen van feedback.":::

### <a name="mark-the-anomaly-point-type"></a>Het type afwijkings punt markeren

Zoals in de onderstaande afbeelding wordt weer gegeven, vult het dialoog venster feedback automatisch de tijds tempel van het gekozen punt in, maar u kunt deze waarde bewerken. Vervolgens selecteert u of u dit item wilt identificeren als een `Anomaly` , `NotAnomaly` of `AutoDetect` .

:::image type="content" source="../media/feedback/anomaly-value.png" alt-text="Vervolg keuzemenu met opties voor afwijkingen, NotAnomaly en automatische detectie":::

Met de selectie worden uw feedback toegepast op de toekomstige afwijkings detectie verwerking van dezelfde reeks. De verwerkte punten worden niet opnieuw berekend. Dit betekent dat als u een afwijkingen als NotAnomaly markeert, er in de toekomst vergelijk bare afwijkingen worden onderdrukt en als u een punt markeert `NotAnomaly` als `Anomaly` , we zullen in de toekomst vergelijk bare punten kunnen detecteren `Anomaly` . Als `AutoDetect` deze optie is gekozen, worden eerdere feedback op hetzelfde punt in de toekomst genegeerd.

## <a name="provide-feedback-for-multiple-continuous-points"></a>Feedback geven voor meerdere doorlopende punten 

Als u op hetzelfde moment afwijkende feedback wilt geven voor meerdere doorlopende punten, selecteert u de groep punten waarnaar u aantekeningen wilt maken. U ziet dat het gekozen tijds bereik automatisch wordt ingevuld wanneer u afwijkende feedback geeft.

:::image type="content" source="../media/feedback/continuous-anomaly-feedback.png" alt-text="Snelmenu met afwijkende feedback waarvoor afwijking is geselecteerd en een specifiek tijds bereik":::

Als u wilt weer geven of een afzonderlijk punt wordt beïnvloed door uw afwijkingen, selecteert u één punt in een tijd reeks. Als het resultaat van de afwijkings detectie is gewijzigd door feedback, wordt de knop Info weer gegeven **met betrekking tot feedback: True**. Als de melding wordt **beïnvloed door feedback: onwaar**, betekent dit dat er een afwijkende feedback berekening is uitgevoerd voor dit punt, maar het resultaat van de afwijkings detectie mag niet worden gewijzigd.

:::image type="content" source="../media/feedback/affected-by-feedback.png" alt-text="Knop Info weer geven met de woorden: beïnvloed door feedback: True, gemarkeerd in rood":::

Er zijn enkele situaties waarin we geen feedback kunnen geven:

- De afwijkende oorzaak is een feestdag. U kunt het beste een vooraf ingestelde gebeurtenis gebruiken om dit soort valse waarschuwing op te lossen, omdat deze nauw keuriger is.
- De afwijkingen worden veroorzaakt door een wijziging van een bekende gegevens bron. Zo is een upstream-systeem wijziging op dat moment opgetreden. In deze situatie wordt verwacht dat er een afwijkende waarschuwing wordt gegeven, omdat het systeem niet weet wat de gewijzigde waarde heeft veroorzaakt en wanneer vergelijk bare waarden weer worden gewijzigd. Daarom wordt er geen aantekeningen voor dit soort probleem voorgesteld `NotAnomaly` .

## <a name="change-points"></a>Punten wijzigen

Soms is de trend wijziging van gegevens van invloed op de resultaten van anomalie detectie. Wanneer een beslissing wordt genomen over de vraag of een punt een afwijkend of niet is, wordt het laatste venster met geschiedenis gegevens in aanmerking genomen. Als uw tijd reeks een trend heeft gewijzigd, kunt u het exacte wijzigings punt markeren. Dit helpt onze afwijkings detector in toekomstige analyses.

Zoals in de afbeelding hieronder wordt weer gegeven, kunt u `ChangePoint` voor het feedback type selecteren `ChangePoint` en `NotChangePoint` , of `AutoDetect` in de vervolg keuzelijst selecteren.

:::image type="content" source="../media/feedback/changepoint.png" alt-text="Menu met het punt wijzigen met een vervolg keuzelijst met opties voor ChangePoint, NotChangePoint en automatische detectie":::

> [!NOTE]
> Als uw gegevens worden gewijzigd, hoeft u slechts één punt als een `ChangePoint` te markeren, dus als u een hebt gemarkeerd `timerange` , worden de tijds tempel en tijd van het laatste punt automatisch ingevuld. In dit geval is de aantekening alleen van invloed op de resultaten van anomalie detectie na 12 punten.

## <a name="seasonality"></a>Seizoensgebonden

Voor seizoen gegevens geldt dat als er afwijkings detectie wordt uitgevoerd, één stap de periode (seizoensgebondenheid) van de tijd reeks moet worden geschat en toegepast op de afwijkings detectie fase. Soms is het moeilijk om een exacte periode te identificeren en de periode kan ook worden gewijzigd. Een onjuist gedefinieerde periode kan neven effecten hebben op de resultaten van de anomalie detectie. U kunt de huidige periode vinden vanuit een knop Info, de naam is `Min Period` .

:::image type="content" source="../media/feedback/min-period.png" alt-text="Knop info overlay met de woorden period en het getal 7 rood gemarkeerd.":::

U kunt feedback geven voor de periode om dit type anomalie detectie fout op te lossen. Zoals in de afbeelding wordt weer gegeven, kunt u een periode waarde instellen. De eenheid `interval` betekent één granulariteit. Hier zijn nul intervallen: de gegevens zijn niet-seizoen. U kunt ook selecteren `AutoDetect` of u eerdere feedback wilt annuleren en de periode voor de pijp lijn automatisch wilt detecteren. 
 
> [!NOTE]
> Wanneer u voor het instellen van de periode geen tijds tempel of time Range hoeft toe te wijzen, is de periode van invloed op toekomstige anomalie detecties op de hele tijds Erie, vanaf het moment dat u feedback geeft.


:::image type="content" source="../media/feedback/period-feedback.png" alt-text="Menu met de periode voor automatische detectie ingesteld op 28 en het interval ingesteld op nul, wat niet-seizoen is.":::

## <a name="provide-comment-feedback"></a>Feedback geven over opmerkingen

U kunt ook opmerkingen toevoegen om aantekeningen te maken en uw gegevens te voorzien van context. Als u opmerkingen wilt toevoegen, selecteert u een tijds bereik en voegt u de tekst voor uw opmerking toe.

:::image type="content" source="../media/feedback/feedback-comment.png" alt-text="Menu met de mogelijkheid om een tijds bereik en een vak in te stellen voor het toevoegen van een opmerking op basis van tekst":::

## <a name="time-series-batch-feedback"></a>Feedback voor time series-batch

Zoals eerder beschreven, kunt u met de optie voor het uitvoeren van feedback dimensie waarden opnieuw selecteren of verwijderen om een batch van de tijd reeks te verkrijgen die is gedefinieerd door een dimensie filter. U kunt dit modale ook openen door te klikken op de knop + voor feedback in het linkerdeel venster en dimensies en dimensie waarden te selecteren.

:::image type="content" source="../media/feedback/feedback-time-series.png" alt-text="Menu met een blauw plus teken rood gemarkeerd naast feedback over Word":::

:::image type="content" source="../media/feedback/feedback-dimension.png" alt-text="Feedback menu toevoegen met twee dimensies aangegeven door Dim1 en Dim2":::

## <a name="how-to-view-feedback-history"></a>Feedback geschiedenis weer geven

Er zijn twee manieren om de feedback geschiedenis weer te geven. U kunt de knop feedback geschiedenis selecteren in het linkerdeel venster en er wordt een feedback lijst weer gegeven. Hierin worden alle feedback weer gegeven die u hebt gegeven voor een enkele reeks of dimensie filters.

:::image type="content" source="../media/feedback/feedback-history-options.png" alt-text="Menu feedback lijst":::

Een andere manier om de feedback geschiedenis vanuit een serie weer te geven. Er worden verschillende knoppen in de rechter bovenhoek van elke reeks weer geven. Selecteer de knop feedback weer geven en de regel schakelt afwijkende punten weer geven in om feedback vermeldingen weer te geven. De groene vlag vertegenwoordigt een wijzigings punt en de blauwe punten zijn andere feedback punten. U kunt ze ook selecteren en er wordt een lijst met feedback weer gegeven met de details van de feedback die voor dit punt is opgegeven.

:::image type="content" source="../media/feedback/feedback-history-graph.png" alt-text="Grafiek met feedback geschiedenis":::

:::image type="content" source="../media/feedback/feedback-list.png" alt-text="Menu met feedback lijst met twee dimensies":::

> [!NOTE]
> Iedereen die toegang heeft tot de metriek, mag feedback geven, waardoor er mogelijk feedback wordt gegeven door andere datafeed-eigen aren. Als u hetzelfde punt wijzigt als iemand anders, wordt de vorige feedback vermelding overschreven door uw feedback.       

## <a name="next-steps"></a>Volgende stappen
- [Een incident diagnosticeren](diagnose-incident.md).
- [Metrische gegevens configureren en detectieconfiguratie afstemmen](configure-metrics.md)
- [Waarschuwingen configureren en meldingen ontvangen met behulp van een hook](../how-tos/alerts.md)