---
title: Azure Stream Analytics query's lokaal testen in Visual Studio
description: In dit artikel wordt beschreven hoe u query's lokaal test met Azure Stream Analytics-Hulpprogram Ma's voor Visual Studio.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 07/10/2018
ms.openlocfilehash: b856826761f355e896cae48aa4a6fb62f5947e0b
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019921"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio"></a>Stream Analytics query's lokaal testen met Visual Studio

U kunt Azure Stream Analytics-hulpprogram ma's voor Visual Studio gebruiken om uw Stream Analytics-taken lokaal te testen met voorbeeld gegevens of [Live-gegevens](stream-analytics-live-data-local-testing.md). 

Gebruik deze [Quick](stream-analytics-quick-create-vs.md) start om te leren hoe u met Visual Studio een stream Analytics taak maakt.

## <a name="test-your-query"></a>De query testen

Dubbel klik in uw Azure Stream Analytics project op **script. asaql** om het script in de editor te openen. U kunt de query compileren om te zien of er syntaxis fouten zijn. De query-editor ondersteunt IntelliSense, syntaxis kleuren en een fout markering.

![Query-editor](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="add-local-input"></a>Lokale invoer toevoegen

Als u uw query wilt valideren op basis van lokale statische gegevens, klikt u met de rechter muisknop op de invoer en selecteert u **lokale invoer toevoegen**.
   
![Scherm afbeelding met de optie voor het toevoegen van een lokaal invoer menu.](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-01.png)
   
Selecteer in het pop-upvenster voorbeeld gegevens uit het lokale pad en **Sla** het op.
   
![Lokale invoer toevoegen](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-02.png)
   
Een bestand met de naam **local_EntryStream.jsop** wordt automatisch toegevoegd aan de map met invoer gegevens.
   
![Bestand lijst met lokale invoer mappen](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-03.png)
   
Selecteer **lokaal uitvoeren** in de query-editor. U kunt ook op F5 drukken.
   
![Lokaal uitvoeren](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-01.png)
   
De uitvoer kan rechtstreeks vanuit Visual Studio in een tabel indeling worden weer gegeven.

![Uitvoer in tabel indeling](./media/stream-analytics-vs-tools-local-run/stream-analytics-for-vs-local-result.png)

U vindt het uitvoerpad vanuit de uitvoer van de console. Druk op een wille keurige toets om de map met het resultaat te openen.
   
![Lokaal uitvoeren](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-02.png)
   
Controleer de resultaten in de lokale map.
   
![Resultaat van lokale map](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-03.png)
   

### <a name="sample-input"></a>Voorbeeldinvoer
U kunt ook voorbeeld invoer gegevens verzamelen van uw invoer bronnen naar een lokaal bestand. Klik met de rechter muisknop op het invoer configuratie bestand en selecteer **voorbeeld gegevens**. 

![Voorbeeldgegevens](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-01.png)

U kunt alleen voorbeeld gegevens stromen van Event Hubs-of IoT-hubs. Andere invoer bronnen worden niet ondersteund. Vul in het pop-upvenster het lokale pad in om de voorbeeld gegevens op te slaan en selecteer voor **beeld**.

![Voorbeeld gegevens configuratie](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-02.png)
 
U kunt de voortgang bekijken in het **uitvoer** venster. 

![Voorbeeld gegevens uitvoer](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-03.png)

## <a name="next-steps"></a>Volgende stappen

* [Snelstartgids: een Stream Analytics-taak maken met behulp van Visual Studio](stream-analytics-quick-create-vs.md)
* [Visual Studio gebruiken om Azure Stream Analytics-taken weer te geven](stream-analytics-vs-tools.md)
* [Live-gegevens lokaal testen met Azure Stream Analytics-hulpprogram ma's voor Visual Studio (preview)](stream-analytics-live-data-local-testing.md)
* [Continue integratie en ontwikkeling met Stream Analytics-hulpprogramma’s](stream-analytics-tools-for-visual-studio-cicd.md)
