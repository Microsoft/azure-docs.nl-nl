---
title: Gegevens verwerken van Event Hubs Azure met behulp van Stream Analytics | Microsoft Docs
description: In dit artikel wordt beschreven hoe u gegevens uit uw Azure-Event Hub verwerkt met behulp van een Azure Stream Analytics taak.
ms.date: 06/23/2020
ms.topic: article
ms.openlocfilehash: 2db4fba59a1a06a24ee2939c51ecdf65aa06cef3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85312893"
---
# <a name="process-data-from-your-event-hub-using-azure-stream-analytics"></a>Gegevens uit uw Event Hub verwerken met Azure Stream Analytics 
De Azure Stream Analytics-service maakt het eenvoudig om streaminggegevens van Azure Event Hubs op te nemen, te verwerken en te analyseren, waardoor het mogelijk is om in real-time acties in realtime te maken. Met deze integratie kunt u snel een analytische hotspot-pijp lijn maken. U kunt de Azure Portal gebruiken om binnenkomende gegevens te visualiseren en een Stream Analytics query te schrijven. Als uw query klaar is, kunt u deze in slechts enkele klikken naar productie verplaatsen. 

## <a name="key-benefits"></a>Belangrijkste voordelen
Dit zijn de belangrijkste voor delen van Azure Event Hubs en Azure Stream Analytics integratie: 
- **Preview-gegevens** : u kunt de inkomende gegevens van een event hub in het Azure Portal bekijken.
- **Test uw query** : bereid een transformatie query voor en test deze rechtstreeks in het Azure Portal. Raadpleeg de documentatie over de query [taal stream Analytics](/stream-analytics-query/built-in-functions-azure-stream-analytics) voor de syntaxis van de query taal.
- **Uw query implementeren voor productie** : u kunt de query in productie implementeren door een Azure stream Analytics-taak te maken en te starten.

## <a name="end-to-end-flow"></a>End-to-end-stroom

1. Meld u aan bij [Azure Portal](https://portal.azure.com). 
1. Navigeer naar uw **Event hubs-naam ruimte** en navigeer vervolgens naar de **Event hub**, die de inkomende gegevens bevat. 
1. Selecteer **gegevens verwerken** op de pagina Event hub.  

    ![Gegevens tegel verwerken](./media/process-data-azure-stream-analytics/process-data-tile.png)
1. Selecteer **verkennen** op de tegel **real-time inzichten van gebeurtenissen inschakelen** . 

    ![Stream Analytics selecteren](./media/process-data-azure-stream-analytics/process-data-page-explore-stream-analytics.png)
1. U ziet een query pagina met waarden die al zijn ingesteld voor de volgende velden:
    1. Uw **Event hub** als invoer voor de query.
    1. Voor beeld van een **SQL-query** met een SELECT-instructie. 
    1. Een **uitvoer** alias om te verwijzen naar de resultaten van de query test. 

        ![Query-editor](./media/process-data-azure-stream-analytics/query-editor.png)
        
        > [!NOTE]
        >  Wanneer u deze functie voor de eerste keer gebruikt, vraagt deze pagina u om toestemming om een consumenten groep te maken en een beleid voor uw Event Hub om binnenkomende gegevens te bekijken.
1. Selecteer **maken** in het deel venster **invoer voorbeeld** , zoals wordt weer gegeven in de vorige afbeelding. 
1. U ziet onmiddellijk een moment opname van de meest recente binnenkomende gegevens op dit tabblad.
    - Het type serialisatie in uw gegevens wordt automatisch gedetecteerd (JSON/CSV). U kunt dit ook hand matig wijzigen in JSON/CSV/AVRO.
    - U kunt een voor beeld bekijken van inkomende gegevens in de tabel indeling of de RAW-indeling. 
    - Als uw weer gegeven gegevens niet actueel zijn, selecteert u **vernieuwen** om de meest recente gebeurtenissen te bekijken. 

        Hier volgt een voor beeld van gegevens in de **tabel indeling**:   ![ resulteert in de tabel indeling](./media/process-data-azure-stream-analytics/snapshot-results.png)

        Hier volgt een voor beeld van de gegevens in **RAW-indeling**: 

        ![Resulteert in de RAW-indeling](./media/process-data-azure-stream-analytics/snapshot-results-raw-format.png)
1. Selecteer **test query** om de moment opname van de test resultaten van uw query te bekijken op het tabblad **test resultaten** . U kunt de resultaten ook downloaden.

    ![Query resultaten testen](./media/process-data-azure-stream-analytics/test-results.png)
1. Schrijf uw eigen query om de gegevens te transformeren. Zie [Stream Analytics Naslag informatie voor de query taal](/stream-analytics-query/stream-analytics-query-language-reference).
1. Wanneer u de query hebt getest en u deze wilt verplaatsen naar productie, selecteert u **query implementeren**. Als u de query wilt implementeren, maakt u een Azure Stream Analytics taak waarmee u een uitvoer voor uw taak kunt instellen en de taak moet starten. Als u een Stream Analytics taak wilt maken, geeft u een naam op voor de taak en selecteert u **maken**.

      ![Een Azure Stream Analytics-taak maken](./media/process-data-azure-stream-analytics/create-stream-analytics-job.png)

      > [!NOTE] 
      >  We raden u aan om een Consumer groep en een beleid te maken voor elke nieuwe Azure Stream Analytics-taak die u maakt op de Event Hubs pagina. Consumenten groepen staan slechts vijf gelijktijdige lezers toe, dus als u een speciale Consumer groep voor elke taak opgeeft, worden eventuele fouten voor komen die kunnen ontstaan door het overschrijden van die limiet. Met een speciaal beleid kunt u uw sleutel draaien of machtigingen intrekken zonder dat dit van invloed is op andere resources. 
1. Uw Stream Analytics-taak wordt nu gemaakt op dezelfde locatie als de query die u hebt getest en de invoer is uw Event Hub. 

9.  Als u de pijp lijn wilt volt ooien, stelt u de **uitvoer** van de query in en selecteert u **starten** om de taak te starten.

    > [!NOTE]
    > Vergeet niet om de outputalias te vervangen door de naam van de uitvoer die u in Azure Stream Analytics hebt gemaakt voordat u de taak start.

      ![Uitvoer instellen en de taak starten](./media/process-data-azure-stream-analytics/set-output-start-job.png)


## <a name="known-limitations"></a>Bekende beperkingen
Tijdens het testen van uw query duurt het laden van de test resultaten ongeveer 6 seconden. We werken aan het verbeteren van de prestaties van testen. Wanneer de implementatie echter in productie is geïmplementeerd, heeft Azure Stream Analytics een subseconde.

## <a name="streaming-units"></a>Streaming-eenheden
Uw Azure Stream Analytics-taak wordt standaard ingesteld op drie streaming-eenheden (SUs). Als u deze instelling wilt aanpassen, selecteert u **schalen** in het menu links op de pagina **Stream Analytics-taak** in de Azure Portal. Zie over het [begrijpen en aanpassen van streaming](../stream-analytics/stream-analytics-streaming-unit-consumption.md)-eenheden voor meer informatie over streaming-eenheden.

![Streaming-eenheden schalen](./media/process-data-azure-stream-analytics/scale.png)

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over Stream Analytics query's [Stream Analytics query taal](/stream-analytics-query/built-in-functions-azure-stream-analytics)
