---
title: Integratie van dash board Power BI met Azure Stream Analytics
description: In dit artikel wordt beschreven hoe u een real-time Power BI dash board gebruikt om gegevens uit een Azure Stream Analytics taak te visualiseren.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 11/16/2020
ms.openlocfilehash: 6dd855695a155e924f7c46bdb17449c5e6504ca6
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94745357"
---
# <a name="stream-analytics-and-power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Stream Analytics en Power BI: een real-time analyse dashboard voor het streamen van gegevens

Met Azure Stream Analytics kunt u profiteren van een van de toonaangevende hulpprogram ma's voor business intelligence, [micro soft power bi](https://powerbi.com/). In dit artikel leert u hoe u business intelligence-hulpprogram ma's maakt door Power BI als uitvoer voor uw Azure Stream Analytics taken te gebruiken. U leert ook hoe u een realtime-dash board kunt maken en gebruiken dat voortdurend wordt bijgewerkt door de Stream Analytics taak.

Dit artikel gaat verder met de Stream Analytics zelf studie voor [fraude detectie in realtime](stream-analytics-real-time-fraud-detection.md) . Het is gebaseerd op de werk stroom die in deze zelf studie is gemaakt en voegt een Power BI uitvoer toe, zodat u frauduleuze telefoon gesprekken kunt visualiseren die worden gedetecteerd door een streaming Analytics-taak. 

U kunt [een video](https://www.youtube.com/watch?v=SGUpT-a99MA)  bekijken waarin dit scenario wordt geïllustreerd.


## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u het volgende hebt voordat u begint:

* Een Azure-account.
* Een account voor Power BI Pro. U kunt een werk account of een school account gebruiken.
* Een voltooide versie van de zelf studie voor [fraude detectie in realtime](stream-analytics-real-time-fraud-detection.md) . De zelf studie bevat een app die fictieve telefonische meta gegevens genereert. In de zelf studie maakt u een Event Hub en verzendt u de gegevens van de telefoon oproep naar de Event Hub. U schrijft een query die frauduleuze aanroepen detecteert (aanroepen vanaf hetzelfde nummer op hetzelfde tijdstip op verschillende locaties). 


## <a name="add-power-bi-output"></a>Power BI uitvoer toevoegen
In de zelf studie voor fraude detectie wordt de uitvoer naar Azure Blob-opslag verzonden. In deze sectie voegt u een uitvoer toe waarmee gegevens naar Power BI worden verzonden.

1. Open in de Azure Portal de streaming Analytics-taak die u eerder hebt gemaakt. Als u de voorgestelde naam hebt gebruikt, wordt de taak een naam genoemd `sa_frauddetection_job_demo` .

2. Selecteer in het linkermenu **uitvoer** onder **taak topologie**. Selecteer vervolgens **+ toevoegen** en kies **Power bi** in het vervolg keuzemenu.

3. Selecteer **+ Toevoegen** > **Power BI**. Vul vervolgens het formulier in met de volgende gegevens en selecteer **autoriseren** om uw eigen gebruikers-id te gebruiken om verbinding te maken met Power bi (het token is geldig gedurende 90 dagen). 

>[!NOTE]
>Voor productie taken wordt u aangeraden om verbinding te maken met [beheerde identiteit om uw Azure stream Analytics-taak te verifiëren voor Power bi](./powerbi-output-managed-identity.md).

   |**Instelling**  |**Voorgestelde waarde**  |
   |---------|---------|
   |Uitvoeralias  |  CallStream-PowerBI  |
   |Naam van de gegevensset  |   sa-gegevensset  |
   |Tabelnaam |  frauduleuze oproepen  |

   ![Azure Stream Analytics-uitvoer configureren](media/stream-analytics-power-bi-dashboard/configure-stream-analytics-output.png)

   > [!WARNING]
   > Als Power BI een gegevensset en tabel heeft met dezelfde namen als de records die u in de Stream Analytics taak opgeeft, worden de bestaande gegevens overschreven.
   > U wordt aangeraden deze gegevensset en tabel niet expliciet in uw Power BI-account te maken. Ze worden automatisch gemaakt wanneer u de Stream Analytics-taak start en de taak wordt gestart met de pomp uitvoer in Power BI. Als uw taak query geen resultaten oplevert, worden de gegevensset en tabel niet gemaakt.
   >

4. Als u **Autoriseren** hebt geselecteerd, wordt er een pop-upvenster geopend en wordt u gevraagd referenties te verstrekken als verificatie voor uw Power BI-account. Zodra de autorisatie geslaagd is, kunt u de instellingen **Opslaan**.

8. Klik op **Create**.

De gegevensset wordt gemaakt met de volgende instellingen:

* **defaultRetentionPolicy: BasicFIFO** -data is FIFO, met maxi maal 200.000 rijen.
* **defaultMode: Hybrid** -de gegevensset ondersteunt zowel streaming-tegels (ook wel pushen genoemd) als traditionele visuele elementen op basis van een rapport. Voor de push-inhoud worden de gegevens voortdurend bijgewerkt van de stream Analytics-taak in dit geval, zonder dat u de vernieuwing hoeft te plannen vanaf de Power BI-zijde.

Op dit moment kunt u geen gegevens sets maken met andere vlaggen.

Zie voor meer informatie over Power BI gegevens sets de referentie [Power BI rest API](/rest/api/power-bi/) .


## <a name="write-the-query"></a>De query schrijven

1. Sluit de Blade **uitvoer** en ga terug naar de taak Blade.

2. Klik op het **query** -vak. 

3. Voer de volgende query in. Deze query is vergelijkbaar met de Self-deelname query die u hebt gemaakt in de zelf studie voor fraude detectie. Het verschil is dat met deze query resultaten worden verzonden naar de nieuwe uitvoer die u hebt gemaakt ( `CallStream-PowerBI` ). 

    >[!NOTE]
    >Als u de invoer niet `CallStream` in de zelf studie voor fraude detectie hebt benoemen, vervangt u uw naam door `CallStream` in de componenten **from** en **joinexpressie** in de query.

   ```SQL
   /* Our criteria for fraud:
   Calls made from the same caller to two phone switches in different locations (for example, Australia and Europe) within five seconds */

   SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
   INTO "CallStream-PowerBI"
   FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
   JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime

   /* Where the caller is the same, as indicated by IMSI (International Mobile Subscriber Identity) */
   ON CS1.CallingIMSI = CS2.CallingIMSI

   /* ...and date between CS1 and CS2 is between one and five seconds */
   AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5

   /* Where the switch location is different */
   WHERE CS1.SwitchNum != CS2.SwitchNum
   GROUP BY TumblingWindow(Duration(second, 1))
   ```

4. Klik op **Opslaan**.


## <a name="test-the-query"></a>De query testen

Deze sectie is optioneel, maar wordt aanbevolen. 

1. Als de TelcoStreaming-app momenteel niet wordt uitgevoerd, start u deze door de volgende stappen uit te voeren:

    * Open de opdracht prompt.
    * Ga naar de map met de telcogenerator.exe en gewijzigde telcodatagen.exe.config bestanden.
    * Voer de volgende opdracht uit:

       `telcodatagen.exe 1000 .2 2`

2. Klik op de **query** pagina voor uw stream Analytics-taak op de punten naast de `CallStream` invoer en selecteer vervolgens **voorbeeld gegevens uit invoer**.

3. Geef op dat u wilt dat er drie minuten aan gegevens zijn en klik op **OK**. Wacht tot u een melding krijgt dat de gegevens zijn verzameld.

4. Klik op **testen** en Bekijk de resultaten.

## <a name="run-the-job"></a>De taak uitvoeren

1. Zorg ervoor dat de TelcoStreaming-app wordt uitgevoerd.

2. Ga naar de **overzichts** pagina voor uw stream Analytics-taak en selecteer **starten**.

    ![De Stream Analytics-taak starten](./media/stream-analytics-power-bi-dashboard/stream-analytics-sa-job-start-output.png)

De stream Analytics-taak begint met zoeken naar frauduleuze aanroepen in de binnenkomende stroom. De taak maakt ook de gegevensset en de tabel in Power BI en begint met het verzenden van gegevens over de frauduleuze aanroepen.


## <a name="create-the-dashboard-in-power-bi"></a>Het dash board maken in Power BI

1. Ga naar [Powerbi.com](https://powerbi.com) en meld u aan met uw werk-of school account. Als de Stream Analytics taak query resultaten oplevert, ziet u dat uw gegevensset al is gemaakt:

    ![Locatie van streaming-gegevensset in Power BI](./media/stream-analytics-power-bi-dashboard/stream-analytics-streaming-dataset.png)

2. Klik in uw werk ruimte op **+ &nbsp; maken**.

    ![De knop maken in Power BI werk ruimte](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard.png)

3. Maak een nieuw dash board en geef het een naam `Fraudulent Calls` .

    ![Een dash board maken en dit een naam geven in Power BI werk ruimte](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard-name.png)

4. Klik boven aan het venster op **tegel toevoegen**, selecteer **aangepaste streaminggegevens** en klik vervolgens op **volgende**.

    ![Tegel aangepaste streaming-gegevensset in Power BI](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

5. Selecteer uw gegevensset onder **uw DATSETS** en klik vervolgens op **volgende**.

    ![Uw streaming-gegevensset in Power BI](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

6. Selecteer **kaart** onder **type visualisatie** en selecteer vervolgens **fraudulentcalls** in de lijst **velden** .

    ![Visualisatie Details voor nieuwe tegel](./media/stream-analytics-power-bi-dashboard/add-fraudulent-calls-tile.png)

7. Klik op **Volgende**.

8. Vul de tegel gegevens in, zoals een titel en een ondertitel.

    ![Titel en Ondertitel voor nieuwe tegel](./media/stream-analytics-power-bi-dashboard/pbi-new-tile-details.png)

9. Klik op **Toepassen**.

    U hebt nu een fraude teller.

    ![Fraude teller in Power BI dash board](./media/stream-analytics-power-bi-dashboard/power-bi-fraud-counter-tile.png)

8. Volg de stappen opnieuw om een tegel toe te voegen (te beginnen met stap 4). Deze keer doet u het volgende:

    * Wanneer u het **visualisatie type** krijgt, selecteert u **lijn diagram**. 
    * Voeg een as toe en selecteer **windowend**. 
    * Voeg een waarde toe en selecteer **fraudulentcalls**.
    * Selecteer bij **Tijdvenster voor weergave** de laatste 10 minuten.

      ![Tegel maken voor lijn diagram in Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-tile-line-chart.png)

9. Klik op **volgende**, voeg een titel en subtitel toe en klik op **Toep assen**.

     Het Power BI-dash board biedt nu twee weer gaven van gegevens over frauduleuze aanroepen zoals gedetecteerd in de streaminggegevens.

     ![Voltooide Power BI dash board met twee tegels voor frauduleuze aanroepen](./media/stream-analytics-power-bi-dashboard/pbi-dashboard-fraudulent-calls-finished.png)

## <a name="learn-about-limitations-and-best-practices"></a>Meer informatie over beperkingen en aanbevolen procedures
Op dit moment kunnen Power BI ongeveer één keer per seconde worden aangeroepen. Het streamen van visuele elementen ondersteunt pakketten van 15 KB. Dit houdt in dat het streamen van visuele elementen mislukt (maar pushen blijft werken). Als gevolg van deze beperkingen heeft Power BI zichzelf het belangrijkst voor gevallen waarin Azure Stream Analytics een aanzienlijke vermindering van gegevens belasting ondergaat. We raden u aan om een Tumblingvenstertriggers-venster of verspringen-venster te gebruiken om ervoor te zorgen dat de gegevens push per seconde Maxi maal één push is en dat uw query binnen de doorvoer vereisten valt.

U kunt de volgende vergelijking gebruiken om de waarde voor het venster in seconden te berekenen:

![Vergelijking voor het berekenen van de waarde om binnen een paar seconden een venster te geven](./media/stream-analytics-power-bi-dashboard/compute-window-seconds-equation.png)  

Bijvoorbeeld:

* Met 1.000 apparaten worden gegevens per seconde verzonden.
* U gebruikt de Power BI Pro SKU die 1.000.000 rijen per uur ondersteunt.
* U wilt de hoeveelheid gemiddelde gegevens per apparaat publiceren naar Power BI.

Als gevolg hiervan wordt de vergelijking:

![Vergelijking op basis van voorbeeld criteria](./media/stream-analytics-power-bi-dashboard/power-bi-example-equation.png)  

Op basis van deze configuratie kunt u de oorspronkelijke query wijzigen in het volgende:

```SQL
    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO "CallStream-PowerBI"
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,4),
        dspl
```

### <a name="renew-authorization"></a>Autorisatie vernieuwen
Als het wacht woord is gewijzigd sinds de taak is gemaakt of de laatste keer is geverifieerd, moet u uw Power BI-account opnieuw verifiëren. Als Azure Multi-Factor Authentication is geconfigureerd op uw Azure Active Directory-Tenant (Azure AD), moet u Power BI autorisatie ook elke twee weken vernieuwen. Als u het abonnement niet verlengt, ziet u symptomen zoals een gebrek aan taak uitvoer of een `Authenticate user error` in de bewerkings Logboeken.

Als een taak wordt gestart nadat het token is verlopen, treedt er een fout op en wordt de taak mislukt. U kunt dit probleem oplossen door de taak die wordt uitgevoerd, te stoppen en naar uw Power BI-uitvoer te gaan. Om gegevens verlies te voor komen, selecteert u de koppeling **autorisatie vernieuwen** en start u de taak opnieuw vanaf de **laatste keer** dat deze is gestopt.

Nadat de autorisatie is vernieuwd met Power BI, wordt een groene waarschuwing weer gegeven in het autorisatie gebied om aan te geven dat het probleem is opgelost.

## <a name="next-steps"></a>Volgende stappen
* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics uitvoer](stream-analytics-define-outputs.md)
* [Naslag informatie voor de query taal Azure Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference)
* [Naslag informatie over Azure Stream Analytics beheer REST API](/rest/api/streamanalytics/)
* [Beheerde identiteit gebruiken om uw Azure Stream Analytics-taak te verifiëren voor Power BI](./powerbi-output-managed-identity.md)
