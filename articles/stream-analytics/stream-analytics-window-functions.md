---
title: Inleiding tot Azure Stream Analytics-venster functies
description: In dit artikel worden vier venster functies (tumblingvenstertriggers, verspringen, verschuiving, sessie) beschreven die worden gebruikt in Azure Stream Analytics taken.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/16/2020
ms.openlocfilehash: 4765c0c43cfd9760eb5b4392604bfaeedc69c388
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96511437"
---
# <a name="introduction-to-stream-analytics-windowing-functions"></a>Inleiding tot Stream Analytics-venster functies

In time-streaming scenario's is het uitvoeren van bewerkingen op de gegevens in tijdelijke Vensters een gemeen schappelijk patroon. Stream Analytics heeft systeem eigen ondersteuning voor Windows-functies, waardoor ontwikkel aars complexe stroom verwerkings taken met minimale inspanning kunnen ontwerpen.

Er zijn vijf soorten tijdelijke Vensters waaruit u kunt kiezen: [**tumblingvenstertriggers**](/stream-analytics-query/tumbling-window-azure-stream-analytics), [**verspringen**](/stream-analytics-query/hopping-window-azure-stream-analytics), [**schuiven**](/stream-analytics-query/sliding-window-azure-stream-analytics), [**sessie**](/stream-analytics-query/session-window-azure-stream-analytics)en [**moment opnamen**](/stream-analytics-query/snapshot-window-azure-stream-analytics) .  U gebruikt de venster functies in de [**Group by**](/stream-analytics-query/group-by-azure-stream-analytics) -component van de query syntaxis in uw stream Analytics-taken. U kunt ook gebeurtenissen met meerdere vensters samen voegen met behulp van de [functie **Windows ()**](/stream-analytics-query/windows-azure-stream-analytics).

Alle uitvoer resultaten van de [venster](/stream-analytics-query/windowing-azure-stream-analytics) bewerkingen aan het **einde** van het venster. Houd er rekening mee dat wanneer u een stream Analytics-taak start, u de *Start tijd van de taak uitvoer* kunt opgeven, waarna het systeem automatisch eerdere gebeurtenissen in de binnenkomende stromen ophaalt om het eerste venster op de opgegeven tijd uit te voeren. Als u bijvoorbeeld met de optie *nu* begint, wordt het onmiddellijk om gegevens te verzenden. De uitvoer van het venster is één gebeurtenis op basis van de statistische functie die wordt gebruikt. De uitvoer gebeurtenis heeft het tijds tempel van het einde van het venster en alle venster functies worden gedefinieerd met een vaste lengte. 

![Concepten van Stream Analytics-venster functies](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Venster tumblingvenstertriggers
Functies van het tumblingvenstertriggers-venster worden gebruikt om een gegevens stroom te segmenteren in verschillende tijds segmenten en een functie uit te voeren, zoals in het onderstaande voor beeld. De belangrijkste onderscheidende factoren van een TumblingWindow zijn dat ze herhalend zijn, niet overlappend, en dat een gebeurtenis maar tot één TumblingWindow kan behoren.

![Stream Analytics venster tumblingvenstertriggers](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Venster verspringen
HoppingWindow-functies worden gebruikt om met een vaste tijdstuur vooruit te gaan in de tijd. Het kan handig zijn om deze te zien als Tumblingvenstertriggers Vensters die elkaars overlappen en vaker kunnen worden verzonden dan de grootte van het venster. Gebeurtenissen kunnen deel uitmaken van meer dan één verspringen-venster met resultaten. Als u een verspringen-venster hetzelfde wilt maken als een Tumblingvenstertriggers-venster, geeft u de grootte van de hop op die hetzelfde is als de venster grootte. 

![Stream Analytics venster verspringen](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Schuif venster

Verschuiving van Vensters, in tegens telling tot Tumblingvenstertriggers-of verspringen-Vensters, uitvoer gebeurtenissen alleen voor tijdstippen waarop de inhoud van het venster daad werkelijk wordt gewijzigd. Met andere woorden, wanneer er een gebeurtenis in het venster wordt ingevoerd of afgesloten. Daarom heeft elk venster ten minste één gebeurtenis. Net als bij verspringen Windows kunnen gebeurtenissen tot meer dan één sliding window behoren.

![Stream Analytics sliding window](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="session-window"></a>Sessie venster
Sessie venster functioneert groeps gebeurtenissen die op vergelijk bare tijden aankomen, waarna er tijd wordt gefilterd waarbij er geen gegevens zijn. Dit trefwoord heeft drie belangrijke parameters: time-out, maximale duur en partitioneringssleutel (optioneel).

![Venster Stream Analytics sessie](media/stream-analytics-window-functions/stream-analytics-window-functions-session-intro.png)

Er wordt een sessie venster gestart wanneer de eerste gebeurtenis plaatsvindt. Als er een andere gebeurtenis plaatsvindt binnen de opgegeven time-out van de laatste opgenomen gebeurtenis, wordt het venster uitgebreid om de nieuwe gebeurtenis op te nemen. Anders wordt het venster tijdens de time-out gesloten wanneer er geen gebeurtenissen binnen de time-out optreden.

Als er gebeurtenissen blijven optreden binnen de opgegeven time-out, blijft het sessie venster uitbrei ding totdat de maximale duur is bereikt. De maximum duur voor de controle intervallen is ingesteld op dezelfde grootte als de opgegeven maximale duur. Als de max-duur bijvoorbeeld 10 is, worden de controles op als het venster de maximum duur overschrijdt op t = 0, 10, 20, 30, enzovoort.

Wanneer een partitie sleutel wordt gegeven, worden de gebeurtenissen gegroepeerd op basis van het sleutel-en sessie venster, onafhankelijk van elkaar toegepast op elke groep. Deze partitionering is handig voor gevallen waarin u verschillende sessie Vensters nodig hebt voor verschillende gebruikers of apparaten.

## <a name="snapshot-window"></a>Venster moment opname

Moment opname van Windows-groeps gebeurtenissen met dezelfde tijds tempel. In tegens telling tot andere venster typen waarvoor een specifieke venster functie (zoals [SessionWindow ()](/stream-analytics-query/session-window-azure-stream-analytics)is vereist, kunt u een snap shot-venster Toep assen door System. Time Stamp () toe te voegen aan de component Group by.

![Venster voor moment opname van Stream Analytics](media/stream-analytics-window-functions/snapshot.png)

## <a name="next-steps"></a>Volgende stappen
* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaggids voor Azure Stream Analytics Query](/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](/rest/api/streamanalytics/)
