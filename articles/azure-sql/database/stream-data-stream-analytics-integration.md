---
title: Gegevens streamen met behulp van Azure Stream Analytics-integratie (preview-versie)
description: Gebruik Azure Stream Analytics-integratie voor het streamen van gegevens naar Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: ajetasin
ms.author: ajetasi
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 36a96a1927aeedb5f841083241d487e0c61d6813
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96454027"
---
# <a name="stream-data-into-azure-sql-database-using-azure-stream-analytics-integration-preview"></a>Gegevens streamen naar Azure SQL Database met behulp van Azure Stream Analytics-integratie (preview)

Gebruikers kunnen nu direct vanuit een data base in Azure SQL Database gegevens in realtime streamen, verwerken, bekijken en analyseren in een tabel. Dit doet u in het Azure Portal met behulp van [Azure stream Analytics](../../stream-analytics/stream-analytics-introduction.md). Deze ervaring maakt een groot aantal scenario's mogelijk, zoals verbonden auto, externe controle, fraude detectie en nog veel meer. In de Azure Portal kunt u een bron van gebeurtenissen (Event hub/IoT Hub) selecteren, inkomende realtime gebeurtenissen weer geven en een tabel selecteren om gebeurtenissen op te slaan. U kunt ook Azure Stream Analytics query taal query's in de portal schrijven om binnenkomende gebeurtenissen te transformeren en op te slaan in de geselecteerde tabel. Dit nieuwe toegangs punt bevindt zich naast het maken en configureren van ervaringen die al bestaan in Stream Analytics. Deze ervaring begint met de context van uw data base, zodat u snel een Stream Analytics-taak kunt instellen en naadloos tussen de data base in Azure SQL Database en Stream Analytics ervaring kunt navigeren.

![Stream Analytics stroom](./media/stream-data-stream-analytics-integration/stream-analytics-flow.png)

## <a name="key-benefits"></a>Belangrijkste voordelen

- Minimale context overschakelen: u kunt beginnen met een data base in Azure SQL Database in de portal en in realtime gegevens opnemen in een tabel zonder over te scha kelen naar een andere service.
- Verminderd aantal stappen: de context van uw data base en tabel wordt gebruikt om een Stream Analytics taak vooraf te configureren.
- Extra gebruiks gemak met voorbeeld gegevens: binnenkomende gegevens van de bron van de gebeurtenissen (Event hub/IoT Hub) in de context van de geselecteerde tabel bekijken

> [!IMPORTANT]
> Een Azure Stream Analytics-taak kan worden uitgevoerd naar Azure SQL Database, Azure SQL Managed instance of Azure Synapse Analytics. Zie [uitvoer](../../stream-analytics/stream-analytics-define-outputs.md)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende resources nodig om de stappen in dit artikel uit te voeren:

- Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [maak dan een gratis account](https://azure.microsoft.com/free/).
- Een database in Azure SQL Database. Zie [een enkele data base maken in Azure SQL database](single-database-create-quickstart.md)voor meer informatie.
- Een firewall regel waarmee de computer verbinding kan maken met de server. Zie [een firewall regel op server niveau maken](firewall-create-server-level-portal-quickstart.md)voor meer informatie.

## <a name="configure-stream-analytics-integration"></a>Stream Analytics-integratie configureren

1. Meld u aan bij Azure Portal.
2. Ga naar de Data Base waarin u de streaminggegevens wilt opnemen. Selecteer **Stream Analytics (preview)**.

    ![Stream Analytics](./media/stream-data-stream-analytics-integration/stream-analytics.png)

3. Als u uw streaminggegevens wilt opnemen in deze data base, selecteert u **maken** en geeft u een naam op voor de streaming-taak en selecteert u vervolgens **volgende: invoer**.

    ![basis beginselen van de Stream Analytics-taak configureren](./media/stream-data-stream-analytics-integration/create-job.png)

4. Voer de gegevens van uw gebeurtenis bron in en selecteer **volgende: uitvoer**.

   - **Invoer type**: Event Hub/IOT hub
   - **Invoer alias**: Voer een naam in om de bron van de gebeurtenissen te identificeren
   - **Abonnement**: hetzelfde als Azure SQL database abonnement
   - **Event hub-naam ruimte**: naam voor naam ruimte
   - **Event hub-naam**: naam van Event hub binnen de geselecteerde naam ruimte
   - **Naam van het event hub-beleid** (standaard voor het maken van nieuwe): Geef een beleids naam op
   - **Event hub-consumenten groep** (standaard voor het maken van nieuwe): Geef een naam op voor de Consumer groep  

      We raden u aan om een Consumer groep en een beleid te maken voor elke nieuwe Azure Stream Analytics-taak die u hier maakt. Consumenten groepen staan slechts vijf gelijktijdige lezers toe, dus als u een speciale Consumer groep voor elke taak opgeeft, worden eventuele fouten voor komen die kunnen ontstaan door het overschrijden van die limiet. Met een speciaal beleid kunt u uw sleutel draaien of machtigingen intrekken zonder dat dit van invloed is op andere resources.

     ![Stream Analytics taak uitvoer configureren](./media/stream-data-stream-analytics-integration/create-job-output.png)

5. Selecteer de tabel waarin u de streaminggegevens wilt opnemen. Wanneer u klaar bent, selecteert u **maken**.

   - **Gebruikers naam**, **wacht woord**: Voer uw referenties in voor SQL Server-verificatie. Selecteer **Valideren**.
   - **Tabel**: Selecteer **nieuwe maken** of **bestaande gebruiken**. In deze stroom selecteren we **maken**. Hiermee wordt een nieuwe tabel gemaakt wanneer u de stream Analytics-taak start.

     ![Stream Analytics-taak maken](./media/stream-data-stream-analytics-integration/create.png)

6. Er wordt een query pagina geopend met de volgende details:

   - Uw **invoer** (bron van invoer gebeurtenissen) van waaruit u gegevens opneemt  
   - Uw **uitvoer** (uitvoer tabel) waarmee getransformeerde gegevens worden opgeslagen
   - Een voor beeld van een [SAQL-query](../../stream-analytics/stream-analytics-stream-analytics-query-patterns.md) met een SELECT-instructie.
   - **Invoer voorbeeld: een** moment opname van de meest recente binnenkomende gegevens van de bron van de invoer gebeurtenissen weer geven.
     - Het type serialisatie in uw gegevens wordt automatisch gedetecteerd (JSON/CSV). U kunt dit ook hand matig wijzigen in JSON/CSV/AVRO.
     - U kunt een voor beeld bekijken van inkomende gegevens in de tabel indeling of de RAW-indeling.
     - Als uw weer gegeven gegevens niet actueel zijn, selecteert u **vernieuwen** om de meest recente gebeurtenissen te bekijken.
     - Selecteer **tijds bereik selecteren** om uw query te testen op basis van een bepaald tijds bereik van binnenkomende gebeurtenissen.
     - Selecteer **voorbeeld invoer uploaden** om uw query te testen door een voor beeld van een JSON/CSV-bestand te uploaden. Zie [een Azure stream Analytics-taak testen met voorbeeld gegevens](../../stream-analytics/stream-analytics-test-query.md)voor meer informatie over het testen van een SAQL-query.

     ![query testen](./media/stream-data-stream-analytics-integration/test-query.png)

   - **Test resultaten**: Selecteer **test query** en u kunt de resultaten van uw streaming-query weer geven

     ![test resultaten](./media/stream-data-stream-analytics-integration/test-results.png)

   - **Schema voor test resultaten**: toont het schema van de resultaten van uw streaming-query na het testen. Zorg ervoor dat het resultaten schema van de test overeenkomt met uw uitvoer schema.

     ![schema voor test resultaten](./media/stream-data-stream-analytics-integration/test-results-schema.png)

   - **Uitvoer schema**: dit bevat het schema van de tabel die u in stap 5 (nieuw of bestaand) hebt geselecteerd.

      - Nieuwe maken: als u deze optie in stap 5 hebt geselecteerd, wordt het schema nog niet weer geven tot u de streaming-taak start. Selecteer de juiste tabel index bij het maken van een nieuwe tabel. Zie [geclusterde en niet-geclusterde indexen beschreven](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described/)voor meer informatie over het indexeren van tabellen.
      - Bestaande gebruiken: als u deze optie in stap 5 hebt geselecteerd, ziet u het schema van de geselecteerde tabel.

7. Nadat u klaar bent met het ontwerpen van & de query te testen, selecteert u **query opslaan**. Selecteer **starten stream Analytics taak** om het opnemen van getransformeerde gegevens in de SQL-tabel te starten. Zodra u de volgende velden hebt voltooid, **Start** u de taak.
   - **Begin tijd uitvoer**: Hiermee definieert u de tijd van de eerste uitvoer van de taak.  
     - Nu: de taak wordt nu gestart en de nieuwe inkomende gegevens worden verwerkt.
     - Aangepast: de taak wordt nu gestart, maar verwerkt gegevens van een bepaald punt in de tijd (die in het verleden of de toekomst kan worden uitgevoerd). Zie [How to start a Azure stream Analytics job](../../stream-analytics/start-job.md)(Engelstalig) voor meer informatie.
   - **Streaming-eenheden**: de prijs van Azure stream Analytics is afhankelijk van het aantal streaming-eenheden dat nodig is voor het verwerken van de gegevens in de service. Zie [Azure stream Analytics prijzen](https://azure.microsoft.com/pricing/details/stream-analytics/)voor meer informatie.
   - **Fout afhandeling van uitvoer gegevens**:  
     - Opnieuw proberen: wanneer er een fout optreedt, wordt Azure Stream Analytics de gebeurtenis voor onbepaalde tijd opnieuw geprobeerd te schrijven totdat de schrijf bewerking slaagt. Er is geen time-out voor nieuwe pogingen. Uiteindelijk zijn alle volgende gebeurtenissen geblokkeerd voor verwerking door de gebeurtenis die opnieuw wordt geprobeerd. Deze optie is het standaard beleid voor het afhandelen van uitvoer fouten.
     - Drop: er wordt een uitvoer gebeurtenis door Azure Stream Analytics verwijderd die resulteert in een fout in de gegevens conversie. De neergezette gebeurtenissen kunnen later niet worden hersteld voor reverwerking. Alle tijdelijke fouten (bijvoorbeeld netwerk fouten) worden opnieuw geprobeerd, ongeacht de configuratie van het afhandelings beleid voor uitvoer fouten.
   - **SQL database uitvoer instellingen**: een optie voor het overnemen van het partitie schema van uw vorige query stap, om een volledig parallelle topologie met meerdere schrijvers voor de tabel in te scha kelen. Zie [Azure stream Analytics uitvoer naar Azure SQL database](../../stream-analytics/stream-analytics-sql-output-perf.md)voor meer informatie.
   - **Maximum aantal batches**: de aanbevolen bovengrens voor het aantal records dat wordt verzonden met elke bulksgewijze insert-trans actie.  
    Zie [uitvoer fout beleid in azure stream Analytics](../../stream-analytics/stream-analytics-output-error-policy.md)voor meer informatie over het afhandelen van uitvoer fouten.  

     ![taak starten](./media/stream-data-stream-analytics-integration/start-job.png)

8. Zodra u de taak hebt gestart, ziet u de taak die wordt uitgevoerd in de lijst en kunt u de volgende acties uitvoeren:
   - **De taak starten/stoppen**: als de taak wordt uitgevoerd, kunt u de taak stoppen. Als de taak is gestopt, kunt u de taak starten.
   - **Taak bewerken**: u kunt de query bewerken. Als u meer wijzigingen wilt aanbrengen aan het project ex, voegt u meer invoer/uitvoer toe en opent u de taak in Stream Analytics. De optie bewerken is uitgeschakeld wanneer de taak wordt uitgevoerd.
   - **Voorbeeld uitvoer tabel**: u kunt een voor beeld van de tabel in de SQL-query-editor bekijken.
   - **Open in stream Analytics**: Open de taak in stream Analytics om de bewaking te bekijken, fout opsporingsgegevens over de taak.

     ![Stream Analytics-taken](./media/stream-data-stream-analytics-integration/jobs.png)

## <a name="next-steps"></a>Volgende stappen

- [Documentatie voor Azure Stream Analytics](../../stream-analytics/index.yml)
- [Azure Stream Analytics-oplossingspatronen](../../stream-analytics/stream-analytics-solution-patterns.md)
