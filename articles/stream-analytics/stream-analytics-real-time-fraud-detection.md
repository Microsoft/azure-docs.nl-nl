---
title: 'Zelfstudie: frauduleuze gegevens van telefoongesprekken analyseren met Azure Stream Analytics en resultaten visualiseren in Power BI-dashboard'
description: Deze zelfstudie biedt een end-to-end-demonstratie van het gebruik van Azure Stream Analytics voor het analyseren van frauduleuze gesprekken in een reeks telefoongesprekken.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.custom: contperf-fy21q2
ms.date: 12/17/2020
ms.openlocfilehash: b8744d86300287403ca390d93c70b25215bcac4f
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/30/2020
ms.locfileid: "97822128"
---
# <a name="tutorial-analyze-fraudulent-call-data-with-stream-analytics-and-visualize-results-in-power-bi-dashboard"></a>Zelfstudie: Frauduleuze gegevens van telefoongesprekken analyseren met Stream Analytics en resultaten visualiseren in Power BI-dashboard

In deze zelfstudie leert u hoe u telefoongesprekken kunt analyseren met Azure Stream Analytics. De gegevens van telefoongesprekken die door de clienttoepassing worden gegenereerd, bevatten frauduleuze gesprekken die worden uitgefilterd door de Stream Analytics-taak. U kunt de technieken in deze zelfstudie gebruiken voor andere soorten fraudedetectie, zoals creditcardfraude of identiteitsdiefstal.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Gegevens van een voorbeeld van een telefoongesprek genereren en deze verzenden naar Azure Event Hubs.
> * Een Stream Analytics-taak maken.
> * Taakinvoer en -uitvoer configureren.
> * Query's definiëren om frauduleuze gesprekken eruit te filteren.
> * De taak testen en starten.
> * Resultaten visualiseren in Power BI.

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u de volgende stappen hebt uitgevoerd voordat u begint:

* Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) aan.
* Download de app voor het genereren van telefoongesprekken [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) in het Microsoft Downloadcentrum of haal de broncode op van [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator).
* U hebt een Power BI-account nodig.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="create-an-azure-event-hub"></a>Een Azure Event Hub maken

Voordat Stream Analytics de gegevensstroom van frauduleuze gesprekken kan analyseren, moeten de gegevens naar Azure worden verzonden. In deze zelfstudie verzendt u gegevens naar Azure met behulp van [Azure Event Hubs](../event-hubs/event-hubs-about.md).

Gebruik de volgende stappen voor het maken van een Event Hub en verzenden van gespreksgegevens naar die Event Hub:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Selecteer **Een resource maken** > **Internet of Things** > **Event Hubs**.

   ![Azure Event Hub in de portal maken](media/stream-analytics-real-time-fraud-detection/find-event-hub-resource.png)
3. Voer in het deelvenster **Naamruimte maken** de volgende waarden in:

   |**Instelling**  |**Voorgestelde waarde** |**Beschrijving**  |
   |---------|---------|---------|
   |Naam     | asaTutorialEventHub        |  Een unieke naam voor het identificeren van de event hub-naamruimte.       |
   |Abonnement     |   \<Your subscription\>      |   Selecteer een Azure-abonnement waarvoor u de event hub wilt maken.      |
   |Resourcegroep     |   MyASADemoRG      |  Selecteer **Nieuwe maken** en voer een naam voor de nieuwe resourcegroep voor uw account in.       |
   |Locatie     |   VS - west 2      |    De locatie waar de event hub-naamruimte kan worden geïmplementeerd.     |

4. Gebruik standaardopties voor de resterende instellingen en selecteer **Beoordelen en maken**. Selecteer vervolgens **Maken** om het implementatieproces te starten.

   ![Event hub-naamruimte in de Azure-portal maken](media/stream-analytics-real-time-fraud-detection/create-event-hub-namespace.png)

5. Nadat de naamruimte is geïmplementeerd, gaat u naar **Alle resources** en zoekt u *asaTutorialEventHub* in de lijst met Azure-resources. Selecteer *asaTutorialEventHub* om deze te openen.

6. Selecteer vervolgens **+ Event Hub** en voer een **Naam** in voor de Event hub. Stel **Aantal partities** in op 2.  Gebruik standaardopties voor de resterende instellingen en selecteer **Maken**. Wacht tot de implementatie is voltooid.

   ![Event Hub-configuratie in de Azure-portal](media/stream-analytics-real-time-fraud-detection/create-event-hub-portal.png)

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Toegang verlenen tot de event hub en een verbindingsreeks ophalen

Voordat een toepassing gegevens naar Azure Event Hubs kan verzenden, moet de event hub een beleid hebben waarmee toegang wordt verleend. Het toegangsbeleid genereert een verbindingsreeks die autorisatiegegevens bevat.

1. Ga naar de Event Hub die u in de vorige stap hebt gemaakt, *MyEventHub*. Selecteer onder **Instellingen** **Beleid voor gedeelde toegang** en selecteer vervolgens **+ Toevoegen**.

2. Geef het beleid de naam **MyPolicy** en controleer of het selectievakje **Beheren** is ingeschakeld. Selecteer vervolgens **Maken**.

   ![Gedeeld toegangsbeleid voor event hub maken](media/stream-analytics-real-time-fraud-detection/create-event-hub-access-policy.png)

3. Nadat het beleid is gemaakt, selecteert u de naam van het beleid om het beleid te openen. Zoek **Verbindingsreeks - primaire sleutel**. Selecteer de knop **Kopiëren** naast de verbindingsreeks.

   ![De verbindingsreeks voor het beleid voor gedeelde toegang opslaan](media/stream-analytics-real-time-fraud-detection/save-connection-string.png)

4. Plak de verbindingsreeks in een teksteditor. U hebt deze verbindingsreeks nodig in de volgende sectie.

   De verbindingsreeks ziet er als volgt uit:

   `Endpoint=sb://<Your event hub namespace>.servicebus.windows.net/;SharedAccessKeyName=<Your shared access policy name>;SharedAccessKey=<generated key>;EntityPath=<Your event hub name>`

   U ziet dat de verbindingsreeks meerdere sleutel-/waardeparen bevat, gescheiden door puntkomma's: **Endpoint**, **SharedAccessKeyName**, **SharedAccessKey** en **EntityPath**.

## <a name="start-the-event-generator-application"></a>De app voor het genereren van gebeurtenissen starten

Voordat u de app TelcoGenerator start, moet u deze configureren voor het verzenden van gegevens naar de Azure Event Hubs die u eerder hebt gemaakt.

1. Pak de inhoud van het bestand [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) uit.
2. Open het bestand `TelcoGenerator\TelcoGenerator\telcodatagen.exe.config` in een teksteditor van uw keuze. Er is meer dan één `.config`-bestand, let er dus op dat u het juiste bestand opent.

3. Update het `<appSettings>`-element in het configuratiebestand met de volgende details:

   * Stel de waarde van de sleutel *EventHubName* in op de waarde van EntityPath in de verbindingsreeks.
   * Stel de waarde van de sleutel *Microsoft.ServiceBus.ConnectionString* in op de verbindingsreeks zonder de EntityPath-waarde. Vergeet niet om de puntkomma te verwijderen die voor de waarde EntityPath staat.

4. Sla het bestand op.

5. Open vervolgens een opdrachtvenster en ga naar de map waar u de toepassing TelcoGenerator hebt uitgepakt. Voer vervolgens de volgende opdracht in:

   ```cmd
   .\telcodatagen.exe 1000 0.2 2
   ```

   Voor deze opdracht worden de volgende parameters gebruikt:
   * Aantal records met gespreksgegevens per uur.
   * Percentage van fraudekans: hoe vaak de app een frauduleus gesprek moet simuleren. Een waarde van 0.2 betekent dat ongeveer 20% van de gespreksrecords er frauduleus uitziet.
   * Tijd in uren: het aantal uren dat de app moet worden uitgevoerd. U kunt de app ook op elk gewenst moment stoppen door het proces op de opdrachtregel te beëindigen (met **Ctrl+C**).

   Na enkele seconden begint de app met het weergeven van telefoongesprekrecords op het scherm wanneer deze naar de event hub worden gestuurd. De telefoongesprekgegevens bevatten de volgende velden:

   |**Record**  |**Definitie**  |
   |---------|---------|
   |CallrecTime    |  Het tijdstempel voor de begintijd van de oproep.       |
   |SwitchNum     |  Het schakelnummer van de oproep. In dit voorbeeld zijn de schakelnummers tekenreeksen die staan voor het land/de regio van herkomst (VS, China, UK, Duitsland of Australië).       |
   |CallingNum     |  Het telefoonnummer van de beller.       |
   |CallingIMSI     |  De International Mobile Subscriber Identity (IMSI). Dit is een unieke id van de beller.       |
   |CalledNum     |   Het telefoonnummer van de ontvanger.      |
   |CalledIMSI     |  De International Mobile Subscriber Identity (IMSI). Dit is een unieke id van de ontvanger.       |

## <a name="create-a-stream-analytics-job"></a>Een Stream Analytics-taak maken

Nu u een stream van gesprekgebeurtenissen hebt, kunt u een Stream Analytics-taak maken die gegevens uit de event hub leest.

1. Als u een Stream Analytics-taak wilt maken, gaat u naar de [Azure-portal](https://portal.azure.com/).

2. Selecteer **Een resource maken** en zoek naar **Stream Analytics-taak**. Selecteer de tegel **Stream Analytics-taak** en selecteer *Maken**.

3. Voer in het formulier **Nieuwe Stream Analytics-taak** de volgende waarden in:

   |**Instelling**  |**Voorgestelde waarde**  |**Beschrijving**  |
   |---------|---------|---------|
   |Taaknaam     |  ASATutorial       |   Een unieke naam voor het identificeren van de event hub-naamruimte.      |
   |Abonnement    |  \<Your subscription\>   |   Selecteer een Azure-abonnement waarvoor u de taak wilt maken.       |
   |Resourcegroep   |   MyASADemoRG      |   Selecteer **Bestaande gebruiken** en voer een naam voor de nieuwe resourcegroep voor uw account in.      |
   |Locatie   |    VS - west 2     |      De locatie waar de taak kan worden geïmplementeerd. Het is raadzaam de taak en de event hub in dezelfde regio te plaatsen om de best mogelijke prestaties te realiseren en u niet hoeft te betalen voor het overbrengen van gegevens tussen regio's.      |
   |Hostingomgeving    | Cloud        |     Stream Analytics-taken kunnen worden geïmplementeerd in Cloud of in Edge. Met Cloud kunt u taken implementeren naar Azure Cloud en met Edge kunt u taken implementeren naar een IoT Edge-apparaat.    |
   |Streaming-eenheden     |    1       |      Streaming-eenheden vertegenwoordigen de computerresources die nodig zijn om een taak uit te voeren. Deze waarde is standaard ingesteld op 1. Zie het artikel [Streaming-eenheden begrijpen en aanpassen](stream-analytics-streaming-unit-consumption.md) voor meer informatie over het schalen van streaming-eenheden.      |

4. Gebruik standaardopties voor de resterende instellingen, selecteer **Maken** en wacht tot de implementatie is voltooid.

   ![Een Azure Stream Analytics-taak maken](media/stream-analytics-real-time-fraud-detection/create-stream-analytics-job.png)

## <a name="configure-job-input"></a>Taakinvoer configureren

In de volgende stap definieert u een invoerbron voor de taak om gegevens te kunnen lezen met de Event Hub die u in de vorige sectie hebt gemaakt.

1. Open de pagina **Alle resources** vanuit Azure Portal en zoek de Stream Analytics-taak *ASATutorial*.

2. Selecteer in de sectie **Taaktopologie** van de Stream Analytics-taak de optie **Invoer**.

3. Selecteer **+ Stroominvoer toevoegen** en **Event Hub**. Voer in het invoerformulier de volgende waarden in:

   |**Instelling**  |**Voorgestelde waarde**  |**Beschrijving**  |
   |---------|---------|---------|
   |Invoeralias     |  CallStream       |  Geef een beschrijvende naam op om uw invoer te identificeren. De invoeralias mag alleen alfanumerieke tekens, afbreekstreepjes en onderstrepingstekens bevatten en moet 3 tot 63 tekens lang zijn.       |
   |Abonnement    |   \<Your subscription\>      |   Selecteer het Azure-abonnement waarvoor u de event hub hebt gemaakt. De event hub kan zich in dezelfde of een ander abonnement als de Stream Analytics-taak bevinden.       |
   |Event hub-naamruimte    |  asaTutorialEventHub       |  Selecteer de event hub-naamruimte die u in de vorige sectie hebt gemaakt. Alle in uw huidige abonnement beschikbare event hub-naamruimten worden weergegeven in de vervolgkeuzelijst.       |
   |Event Hub-naam    |   MyEventHub      |  Selecteer de event hub die u in de vorige sectie hebt gemaakt. Alle in uw huidige abonnement beschikbare event hubs worden weergegeven in de vervolgkeuzelijst.       |
   |Naam van het Event Hub-beleid   |  Mypolicy       |  Selecteer het door de event hub gedeelde toegangsbeleid dat u in de vorige sectie hebt gemaakt. Alle in uw huidige abonnement beschikbare beleidsregels voor event hubs worden weergegeven in de vervolgkeuzelijst.       |

4. Gebruik standaardopties voor de resterende instellingen en selecteer **Opslaan**.

   ![Azure Stream Analytics-invoer configureren](media/stream-analytics-real-time-fraud-detection/configure-stream-analytics-input.png)

## <a name="configure-job-output"></a>Taakuitvoer configureren

De laatste stap is bedoeld om een uitvoerlocatie te definiëren waar de taak de getransformeerde gegevens naartoe kan schrijven. In deze zelfstudie voert u met behulp van Power BI gegevens uit en maakt u deze zichtbaar.

1. Open **Alle resources** vanuit Azure Portal en selecteer de Stream Analytics-taak *ASATutorial*.

2. Selecteer in de sectie **Taaktopologie** van de Stream Analytics-taak de optie **Uitvoer**.

3. Selecteer **+ Toevoegen** > **Power BI**. Selecteer vervolgens **Autoriseren** en volg de prompts om Power BI te verifiëren.

:::image type="content" source="media/stream-analytics-real-time-fraud-detection/authorize-power-bi.png" alt-text="de knop autoriseren voor Power BI":::

4. Vul de volgende gegevens in het uitvoerformulier in en selecteer **Opslaan**:

   |**Instelling**  |**Voorgestelde waarde**  |
   |---------|---------|
   |Uitvoeralias  |  MyPBIoutput  |
   |Werkruimte Groep| Mijn werkruimte |
   |Naam van de gegevensset  |   ASAdataset  |
   |Tabelnaam |  ASATable  |
   | Verificatiemodus | Gebruikerstoken |

   ![Azure Stream Analytics-uitvoer configureren](media/stream-analytics-real-time-fraud-detection/configure-stream-analytics-output.png)

   In deze zelfstudie wordt de verificatiemodus *Gebruikerstoken* gebruikt. Zie [Beheerde identiteit gebruiken om uw Azure Stream Analytics-taak te verifiëren voor Power BI](powerbi-output-managed-identity.md) als u Beheerde identiteit wilt gebruiken.

## <a name="create-queries-to-transform-real-time-data"></a>Query's maken om realtime gegevens te transformeren

Op dit moment hebt u een Stream Analytics-taak ingesteld om een binnenkomende gegevensstroom te lezen. De volgende stap is om een query te maken die gegevens in realtime analyseert. De query's maken gebruik van een SQL-achtige taal die sommige extensies bevat die specifiek zijn voor Stream Analytics.

In dit gedeelte van de zelfstudie maakt en test u verschillende query's om een paar manieren te leren waarop u een invoerstroom voor analyse kunt maken. 

De query's die u hier maakt, geven alleen de getransformeerde gegevens weer op het scherm. In een later gedeelte schrijft u de getransformeerde gegevens naar Power BI.

Raadpleeg [Verwijzing voor Azure Stream Analytics-querytaal](/stream-analytics-query/stream-analytics-query-language-reference) voor meer informatie over de taal.

### <a name="test-using-a-pass-through-query"></a>Testen met behulp van een Pass Through-query

Als u elke gebeurtenis wilt archiveren, kunt u een Pass Through-query gebruiken om alle velden in de payload van een gebeurtenis te lezen.

1. Navigeer naar uw Stream Analytics-taak in de Azure-portal en selecteer **Query** in *Taaktopologie*. 

2. Voer deze query in het queryvenster in:

   ```SQL
   SELECT 
       *
   FROM 
       CallStream
   ```

    >[!NOTE]
    >Net als bij SQL zijn trefwoorden niet hoofdlettergevoelig en zijn spaties niet van belang.

    In deze query is `CallStream` de alias die u opgeeft wanneer u de invoer maakt. Als u een andere alias hebt gebruikt, gebruikt u die naam.

3. Selecteer **Query testen**.

    De Stream Analytics-taal voert de query uit op de voorbeeldgegevens van de invoer en geeft de uitvoer onderaan het scherm weer. De resultaten geven aan dat de Event Hub en de Stream Analytics-taak correct zijn geconfigureerd.

    :::image type="content" source="media/stream-analytics-real-time-fraud-detection/sample-output-passthrough.png" alt-text="Voorbeelduitvoer van testquery":::

    Het exacte aantal records dat u ziet, is afhankelijk van hoeveel records in het voorbeeld zijn vastgelegd.

### <a name="reduce-the-number-of-fields-using-a-column-projection"></a>Het aantal velden verminderen met een kolomprojectie

In veel gevallen heeft uw analyse niet alle kolommen van de invoerstroom nodig. U kunt een query gebruiken om een kleinere set geretourneerde velden weer te geven dan in de Pass Through-query.

Voer de volgende query uit en bekijk de uitvoer.

```SQL
SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNumCalledNum 
FROM 
    CallStream
```

### <a name="count-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Aantal binnenkomende oproepen per regio: Tumblingvenster met aggregatie

Stel dat u het aantal binnenkomende aanroepen per regio wilt tellen. Wanneer u gegevens streamt en u setfuncties wilt uitvoeren zoals tellen, moet u de stream segmenteren in tijdelijke eenheden, omdat de gegevensstroom effectief oneindig is. U doet dit door een Stream Analytics-[vensterfunctie](stream-analytics-window-functions.md) te gebruiken. U kunt vervolgens met de gegevens als eenheid werken in dat venster.

Voor deze transformatie hebt u een reeks tijdelijke vensters nodig die niet overlappen. Elk venster heeft een discrete set gegevens die u kunt groeperen en samenvoegen. Dit type venster wordt *Tumblingvenster* genoemd. In het Tumblingvenster kunt u een telling krijgen van de binnenkomende aanroepen die gegroepeerd zijn op `SwitchNum`, wat het land/de regio vertegenwoordigt waar de oproep vandaan komt.

1. Plak de volgende query in de query-editor:

    ```SQL
    SELECT 
        System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount 
    FROM
        CallStream TIMESTAMP BY CallRecTime 
    GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum
    ```

    Deze query gebruikt het sleutelwoord `Timestamp By` in de clausule `FROM` om op te geven welk tijdsstempelveld in de invoerstroom moet worden gebruikt om het Tumblingvenster te definiëren. In dit gedeelte verdeelt het venster de gegevens in segment via het veld `CallRecTime` in elk record. Als er geen veld is opgegeven, gebruikt de vensterbewerking de tijd dat elke gebeurtenis bij de Event Hub arriveert. Raadpleeg 'Aankomsttijd vs toepassingstijd' in [Verwijzing voor Stream Analytics-querytaal](/stream-analytics-query/stream-analytics-query-language-reference). 

    De projectie omvat `System.Timestamp`, dat een tijdsstempel voor het eind van elk venster retourneert. 

    Als u wilt opgeven dat u een Tumblingvenster wilt gebruiken, gebruikt u de functie [TUMBLINGWINDOW](/stream-analytics-query/tumbling-window-azure-stream-analytics) in het component `GROUP BY`. In de functie geeft u een tijdseenheid op (van een microseconde tot een dag) en een venstergrootte (hoeveel eenheden). In dit voorbeeld bestaat het Tumblingvenster uit intervallen van vijf seconden, dus u krijgt een telling per land/regio voor elke 5 seconden aan aanroepen.

2. Selecteer **Query testen**. In de resultaten ziet u dat de tijdstempels onder **WindowEnd** in stappen van 5 seconden zijn.

### <a name="detect-sim-fraud-using-a-self-join"></a>SIM-kaartfraude detecteren met een self-join

Voor dit voorbeeld moet u frauduleus gebruik beschouwen als aanroepen die binnen vijf seconden van elkaar van dezelfde gebruiker komen, maar in verschillende locaties. Zo kan dezelfde gebruiker niet op rechtmatige wijze op hetzelfde moment een telefoongesprek vanuit de Verenigde Staten en Australië initiëren.

Als u deze gevallen wilt controleren, kunt u een self-join gebruiken van de streaminggegevens om de stream samen te voegen met zichzelf op basis van de waarde `CallRecTime`. U kunt vervolgens zoeken naar gespreksrecords waarvan de `CallingIMSI`-waarde (het nummer waarmee de oproep is uitgevoerd) hetzelfde is, maar waarvan de `SwitchNum`-waarde (land/regio van herkomst) niet dezelfde is.

Wanneer u een join-bewerking voor streaminggegevens gebruikt, moet de join enkele beperkingen bevatten met betrekking tot hoe ver de overeenkomende rijen in tijd kunnen worden gescheiden. Zoals eerder is vermeld, zijn de streaminggegevens in feite oneindig. De tijdslimieten voor de relatie worden opgegeven binnen het component `ON` van de join met behulp van de functie `DATEDIFF`. In dit geval is de join gebaseerd op een interval van vijf seconden van aanroepgegevens.

1. Plak de volgende query in de query-editor:

    ```SQL
    SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
    INTO "MyPBIoutput"
    FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
    JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime
    ON CS1.CallingIMSI = CS2.CallingIMSI
    AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
    WHERE CS1.SwitchNum != CS2.SwitchNum
    GROUP BY TumblingWindow(Duration(second, 1))
    ```

    Deze query is dezelfde als elke SQL-join, behalve voor de functie `DATEDIFF` in de join. Deze versie van `DATEDIFF` is specifiek voor Stream Analytics en moet verschijnen in de component `ON...BETWEEN`. De parameters zijn een tijdseenheid (seconden in dit voorbeeld) en de aliassen van de twee bronnen in de join. Dit is anders dan de standaard SQL-functie `DATEDIFF`.

    De component `WHERE` omvat de omstandigheden die de frauduleuze aanroep markeert: de oorspronkelijke switches zijn niet dezelfde.

2. Selecteer **Query testen**. Controleer de uitvoer en selecteer **Query opslaan**.

## <a name="start-the-job-and-visualize-output"></a>De taak starten en uitvoer visualiseren

1. Als u de taak wilt starten, gaat u naar **Overzicht** van de taak en selecteert u **Starten**.

2. Selecteer **Nu** voor de starttijd van de taakuitvoer en selecteer **Starten**. U kunt de taakstatus bekijken in de meldingsbalk.

3. Nadat de taak is voltooid, gaat u naar [Power BI](https://powerbi.com/) en meldt u zich aan met uw werk- of schoolaccount. Als de Stream Analytics-query resultaten uitvoert, wordt de gegevensset *ASAdataset* die u hebt gemaakt, weergegeven onder het tabblad **Gegevenssets**.

4. Selecteer in de Power BI-werkruimte **+ Maken** om een nieuw dashboard te maken met de naam *Frauduleuze gesprekken*.

5. Selecteer boven in het venster de optie **Bewerken** en **Tegel toevoegen**. Selecteer **Aangepaste streaminggegevens** en **Volgende**. Kies **ASAdataset** onder **Uw gegevenssets**. Selecteer **Kaart** in de vervolgkeuzelijst **Visualisatietype** en voeg **frauduleuze gesprekken** toe aan **Velden**. Selecteer **Volgende** om een naam voor de tegel in te voeren en selecteer vervolgens **Toepassen** om het bestand te maken.

   ![Tegels in Power BI-dashboard maken](media/stream-analytics-real-time-fraud-detection/create-power-bi-dashboard-tiles.png)

6. Voer nogmaals stap 5 uit met de volgende opties:
   * Selecteer in Type visualisatie de optie Lijndiagram.
   * Voeg een as toe en selecteer **windowend**.
   * Voeg een waarde toe en selecteer **fraudulentcalls**.
   * Selecteer bij **Tijdvenster voor weergave** de laatste 10 minuten.

7. Als beide tegels zijn toegevoegd, moet uw dashboard eruitzien zoals in het onderstaande voorbeeld. Als uw Event Hub-zendtoepassing en de Streaming Analytics-toepassing worden uitgevoerd, wordt uw Power BI-dashboard regelmatig bijgewerkt met nieuw ontvangen gegevens.

   ![Resultaten weergeven in Power BI-dashboard](media/stream-analytics-real-time-fraud-detection/power-bi-results-dashboard.png)

## <a name="embedding-your-power-bi-dashboard-in-a-web-application"></a>Uw Power BI-dashboard insluiten in een webtoepassing

Voor dit gedeelte van de zelfstudie maakt u gebruik van een voorbeeld van een [ASP.NET](https://asp.net/)-webtoepassing die gemaakt is door het Power BI-team en waarmee u uw dashboard kunt insluiten. Zie het artikel [Insluiten met Power BI](/power-bi/developer/embedding) voor meer informatie over het insluiten van dashboards.

Stel de toepassing in door naar de GitHub-opslagplaats [Power BI-Developer-Samples](https://github.com/Microsoft/PowerBI-Developer-Samples) te gaan en volg de instructies in de sectie **Gebruiker is eigenaar van gegevens** (gebruik de omleidings- en startpagina-URL in de subsectie **integrate-web-app**). Omdat we werken met het Dashboard-voorbeeld, gebruiken we de voorbeeldcode **integrate-web-app** in de [GitHub-opslagplaats](https://github.com/microsoft/PowerBI-Developer-Samples/tree/master/.NET%20Framework/Embed%20for%20your%20organization/).
Zodra de toepassing in uw browser wordt uitgevoerd, volgt u deze stappen voor het insluiten van het dashboard dat u eerder in de webpagina hebt gemaakt:

1. Selecteer **Aanmelden bij Power BI**, waarmee de toepassing toegang wordt verleend tot de dashboards in uw Power BI-account.

2. Selecteer de knop **Dashboards ophalen**, waardoor de dashboards van uw account in een tabel worden weergegeven. Zoek de naam van het dashboard dat u eerder hebt gemaakt, **powerbi-embedded-dashboard** en kopieer de bijbehorende **EmbedUrl**.

3. Plak tot slot de **EmbedUrl** in het bijbehorende tekstveld en selecteer **Dashboard insluiten**. U kunt nu hetzelfde dashboard zien dat is ingesloten in een webtoepassing.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een eenvoudige Stream Analytics-taak gemaakt, de binnenkomende gegevens geanalyseerd en de resultaten gepresenteerd in een Power BI-dashboard. Ga voor meer informatie over Stream Analytics-taken verder met de volgende zelfstudie:

> [!div class="nextstepaction"]
> [Azure Functions uitvoeren binnen Stream Analytics-taken](stream-analytics-with-azure-functions.md)
