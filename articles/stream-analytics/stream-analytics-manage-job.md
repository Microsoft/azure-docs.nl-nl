---
title: 'Zelfstudie: een Stream Analytics-taak maken en beheren met behulp van de Azure-portal'
description: Deze zelfstudie biedt een end-to-end-demonstratie van het gebruik van Azure Stream Analytics voor het analyseren van frauduleuze gesprekken in een reeks telefoongesprekken.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/30/2020
ms.openlocfilehash: 70acc696f1cb366d25299f616744e52491a54471
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024174"
---
# <a name="tutorial-analyze-phone-call-data-with-stream-analytics-and-visualize-results-in-power-bi-dashboard"></a>Zelfstudie: Gegevens van telefoongesprekken met Stream Analytics analyseren en de resultaten visualiseren in een Power BI-dashboard

In deze zelfstudie leert u hoe u telefoongesprekken kunt analyseren met Azure Stream Analytics. De gegevens van telefoongesprekken die door de clienttoepassing worden gegenereerd, bevatten enkele frauduleuze gesprekken die worden uitgefilterd door de Stream Analytics-taak.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Gegevens van een voorbeeld van een telefoongesprek genereren en deze verzenden naar Azure Event Hubs
> * Een Stream Analytics-taak maken
> * Taakinvoer en -uitvoer configureren
> * Een query definiëren om frauduleuze gesprekken eruit te filteren
> * De taak testen en starten
> * Resultaten visualiseren in Power BI

## <a name="prerequisites"></a>Vereisten

Doe voordat u begint het volgende:

* Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) aan.
* Meld u aan bij de [Azure-portal](https://portal.azure.com/).
* Download de app voor het genereren van telefoongesprekken [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) in het Microsoft Downloadcentrum of haal de broncode op van [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator).
* U hebt een Power BI-account nodig.

## <a name="create-an-azure-event-hub"></a>Een Azure Event Hub maken

Voordat Stream Analytics de gegevensstroom van frauduleuze gesprekken kan analyseren, moeten de gegevens naar Azure worden verzonden. In deze zelfstudie verzendt u gegevens naar Azure met behulp van [Azure Event Hubs](../event-hubs/event-hubs-about.md).

Gebruik de volgende stappen voor het maken van een Event Hub en verzenden van gespreksgegevens naar die Event Hub:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Selecteer **Een resource maken** > **Internet of Things** > **Event Hubs**.

   ![Azure Event Hub in de portal maken](media/stream-analytics-manage-job/find-event-hub-resource.png)
3. Voer in het deelvenster **Naamruimte maken** de volgende waarden in:

   |**Instelling**  |**Voorgestelde waarde** |**Beschrijving**  |
   |---------|---------|---------|
   |Naam     | asaTutorialEventHub        |  Een unieke naam voor het identificeren van de event hub-naamruimte.       |
   |Abonnement     |   \<Your subscription\>      |   Selecteer een Azure-abonnement waarvoor u de event hub wilt maken.      |
   |Resourcegroep     |   MyASADemoRG      |  Selecteer **Nieuwe maken** en voer een naam voor de nieuwe resourcegroep voor uw account in.       |
   |Locatie     |   VS - west 2      |    De locatie waar de event hub-naamruimte kan worden geïmplementeerd.     |

4. Gebruik standaardopties voor de resterende instellingen en selecteer **Beoordelen en maken**. Selecteer vervolgens **Maken** om het implementatieproces te starten.

   ![Event hub-naamruimte in de Azure-portal maken](media/stream-analytics-manage-job/create-event-hub-namespace.png)

5. Nadat de naamruimte is geïmplementeerd, gaat u naar **Alle resources** en zoekt u *asaTutorialEventHub* in de lijst met Azure-resources. Selecteer *asaTutorialEventHub* om deze te openen.

6. Selecteer vervolgens **+ Event Hub** en voer een **Naam** in voor de Event hub. Stel **Aantal partities** in op *2*.  Gebruik standaardopties voor de resterende instellingen en selecteer **Maken**. Wacht tot de implementatie is voltooid.

   ![Event Hub-configuratie in de Azure-portal](media/stream-analytics-manage-job/create-event-hub-portal.png)

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Toegang verlenen tot de event hub en een verbindingsreeks ophalen

Voordat een toepassing gegevens naar Azure Event Hubs kan verzenden, moet de event hub een beleid hebben waarmee de juiste toegang wordt verleend. Het toegangsbeleid genereert een verbindingsreeks die autorisatiegegevens bevat.

1. Ga naar de Event Hub die u in de vorige stap hebt gemaakt, *MyEventHub*. Selecteer onder **Instellingen** **Beleid voor gedeelde toegang** en selecteer vervolgens **+ Toevoegen**.

2. Geef het beleid de naam **MyPolicy** en controleer of het selectievakje **Beheren** is ingeschakeld. Selecteer vervolgens **Maken**.

   ![Gedeeld toegangsbeleid voor event hub maken](media/stream-analytics-manage-job/create-event-hub-access-policy.png)

3. Zodra het beleid is gemaakt, klikt u op de naam van het beleid om het beleid te openen. Zoek **Verbindingsreeks - primaire sleutel**. Selecteer de knop **Kopiëren** naast de verbindingsreeks.

   ![De verbindingsreeks voor het beleid voor gedeelde toegang opslaan](media/stream-analytics-manage-job/save-connection-string.png)

4. Plak de verbindingsreeks in een teksteditor. U hebt deze verbindingsreeks nodig in de volgende sectie.

   De verbindingsreeks ziet er als volgt uit:

   `Endpoint=sb://<Your event hub namespace>.servicebus.windows.net/;SharedAccessKeyName=<Your shared access policy name>;SharedAccessKey=<generated key>;EntityPath=<Your event hub name>`

   U ziet dat de verbindingsreeks meerdere sleutel-/waardeparen bevat, gescheiden door puntkomma's: **Endpoint**, **SharedAccessKeyName**, **SharedAccessKey** en **EntityPath**.

## <a name="start-the-event-generator-application"></a>De app voor het genereren van gebeurtenissen starten

Voordat u de app TelcoGenerator start, moet u deze configureren voor het verzenden van gegevens naar de Azure Event Hubs die u eerder hebt gemaakt.

1. Pak de inhoud van het bestand [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) uit.
2. Open het bestand `TelcoGenerator\TelcoGenerator\telcodatagen.exe.config` in een teksteditor van uw keuze (Er is meer dan één .config-bestand, let er dus op dat u het juiste bestand opent.)

3. Update het `<appSettings>`-element in het configuratiebestand met de volgende details:

   * Stel de waarde van de sleutel *EventHubName* in op de waarde van EntityPath in de verbindingsreeks.
   * Stel de waarde van de sleutel *Microsoft.ServiceBus.ConnectionString* in op de verbindingsreeks zonder de EntityPath-waarde (vergeet niet de voorafgaande puntkomma te verwijderen).

4. Sla het bestand op.
5. Open vervolgens een opdrachtvenster en ga naar de map waar u de toepassing TelcoGenerator hebt uitgepakt. Voer vervolgens de volgende opdracht in:

   ```cmd
   telcodatagen.exe 1000 0.2 2
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

2. Selecteer **Een resource maken** en zoek naar **Stream Analytics-taak**. Selecteer de tegel **Stream Analytics-taak** en selecteer **Maken**.

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

   ![Een Azure Stream Analytics-taak maken](media/stream-analytics-manage-job/create-stream-analytics-job.png)

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

   ![Azure Stream Analytics-invoer configureren](media/stream-analytics-manage-job/configure-stream-analytics-input.png)

## <a name="configure-job-output"></a>Taakuitvoer configureren

De laatste stap is bedoeld om een uitvoerlocatie te definiëren waar de taak de getransformeerde gegevens naartoe kan schrijven. In deze zelfstudie voert u met behulp van Power BI gegevens uit en maakt u deze zichtbaar.

1. Open **Alle resources** vanuit Azure Portal en selecteer de Stream Analytics-taak *ASATutorial*.

2. Selecteer in de sectie **Taaktopologie** van de Stream Analytics-taak de optie **Uitvoer**.

3. Selecteer **+ Toevoegen** > **Power BI**. Selecteer vervolgens **Autoriseren** en volg de prompts om Power BI te verifiëren.

:::image type="content" source="media/stream-analytics-manage-job/authorize-power-bi.png" alt-text="de knop autoriseren voor Power BI":::

4. Vul de volgende gegevens in het uitvoerformulier in en selecteer **Opslaan**:

   |**Instelling**  |**Voorgestelde waarde**  |
   |---------|---------|
   |Uitvoeralias  |  MyPBIoutput  |
   |Werkruimte Groep| Mijn werkruimte |
   |Naam van de gegevensset  |   ASAdataset  |
   |Tabelnaam |  ASATable  |
   | Verificatiemodus | Gebruikerstoken |

   ![Azure Stream Analytics-uitvoer configureren](media/stream-analytics-manage-job/configure-stream-analytics-output.png)

   In deze zelfstudie wordt de verificatiemodus *Gebruikerstoken* gebruikt. Zie [Beheerde identiteit gebruiken om uw Azure Stream Analytics-taak te verifiëren voor Power BI](powerbi-output-managed-identity.md) als u Beheerde identiteit wilt gebruiken.

## <a name="define-a-query-to-analyze-input-data"></a>Een query voor het analyseren van invoergegevens definiëren

In de volgende stap maakt u een transformatie waarmee gegevens in realtime worden geanalyseerd. U definieert de transformatie-query met [Stream Analytics Query Language](/stream-analytics-query/stream-analytics-query-language-reference). De query die in deze zelfstudie wordt gebruikt, detecteert frauduleuze gesprekken in de telefoongegevens.

In dit voorbeeld worden binnen vijf seconden door dezelfde gebruiker frauduleuze gesprekken gevoerd, maar vanaf afzonderlijke locaties. Zo kan dezelfde gebruiker niet op rechtmatige wijze op hetzelfde moment een telefoongesprek vanuit de Verenigde Staten en Australië initiëren. Ga als volgt te werk als u de transformatie-query voor uw Stream Analytics-taak wilt definiëren:

1. Open het deelvenster **Alle resources** vanuit de Azure-portal en open de Stream Analytics-taak **ASATutorial** die u eerder hebt gemaakt.

2. Selecteer in de sectie **Taaktopologie** van de Stream Analytics-taak de optie **Query**. Het queryvenster vermeldt de invoer en uitvoer die voor de taak zijn geconfigureerd en u kunt een query maken voor het transformeren van de invoerstroom.

3. Vervang de bestaande query in de editor door de volgende query, die een self-join uitvoert met een interval van 5 seconden aan gespreksgegevens:

   ```sql
   SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
   INTO "MyPBIoutput"
   FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
   JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime
   ON CS1.CallingIMSI = CS2.CallingIMSI
   AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
   WHERE CS1.SwitchNum != CS2.SwitchNum
   GROUP BY TumblingWindow(Duration(second, 1))
   ```

   Als u wilt controleren op frauduleuze gesprekken, voert u een self-join uit voor de streaminggegevens op basis van de waarde `CallRecTime`. U kunt vervolgens zoeken naar gespreksrecords waarvan de `CallingIMSI`-waarde (het nummer waarmee de oproep is uitgevoerd) hetzelfde is, maar waarvan de `SwitchNum`-waarde (land/regio van herkomst) verschilt. Wanneer u een JOIN-bewerking voor streaminggegevens gebruikt, moet de join enkele beperkingen bevatten met betrekking tot hoe ver de overeenkomende rijen in tijd kunnen worden gescheiden. Omdat er sprake is van een eindeloze gegevensstroom, worden de tijdsgrenzen voor de relatie opgegeven binnen de **ON**-component van de join met behulp van de functie [DATEDIFF](/stream-analytics-query/datediff-azure-stream-analytics).

   Deze query is net als een normale SQL-join, met als enig verschil de functie **DATEDIFF**. De in deze query gebruikte functie **DATEDIFF** is specifiek voor Streaming Analytics en moet worden opgenomen in de `ON...BETWEEN`-component.

4. U moet de query **Opslaan**.

   ![Stream Analytics-query in de portal definiëren](media/stream-analytics-manage-job/define-stream-analytics-query.png)

## <a name="test-your-query"></a>De query testen

U kunt een query testen vanuit de query-editor. Voer de volgende stappen uit om de query te testen:

1. Zorg ervoor dat de app TelcoGenerator wordt uitgevoerd en records van telefoongesprekken produceert.

2. Selecteer **Testen** om de query te testen. U ziet de volgende resultaten:

   ![Uitvoer van test van Stream Analytics-query](media/stream-analytics-manage-job/sample-test-output-restuls.png)

## <a name="start-the-job-and-visualize-output"></a>De taak starten en uitvoer visualiseren

1. Als u de taak wilt starten, gaat u naar **Overzicht** van de taak en selecteert u **Starten**.

2. Selecteer **Nu** voor de starttijd van de taakuitvoer en selecteer **Starten**. U kunt de taakstatus bekijken in de meldingsbalk.

3. Nadat de taak is voltooid, gaat u naar [Power BI](https://powerbi.com/) en meldt u zich aan met uw werk- of schoolaccount. Als de Stream Analytics-query resultaten uitvoert, wordt de gegevensset *ASAdataset* die u hebt gemaakt, weergegeven onder het tabblad **Gegevenssets**.

4. Selecteer in de Power BI-werkruimte **+ Maken** om een nieuw dashboard te maken met de naam *Frauduleuze gesprekken*.

5. Selecteer boven in het venster de optie **Bewerken** en **Tegel toevoegen**. Selecteer **Aangepaste streaminggegevens** en **Volgende**. Kies **ASAdataset** onder **Uw gegevenssets**. Selecteer **Kaart** in de vervolgkeuzelijst **Visualisatietype** en voeg **frauduleuze gesprekken** toe aan **Velden**. Selecteer **Volgende** om een naam voor de tegel in te voeren en selecteer vervolgens **Toepassen** om het bestand te maken.

   ![Tegels in Power BI-dashboard maken](media/stream-analytics-manage-job/create-power-bi-dashboard-tiles.png)

6. Voer nogmaals stap 5 uit met de volgende opties:
   * Selecteer in Type visualisatie de optie Lijndiagram.
   * Voeg een as toe en selecteer **windowend**.
   * Voeg een waarde toe en selecteer **fraudulentcalls**.
   * Selecteer bij **Tijdvenster voor weergave** de laatste 10 minuten.

7. Als beide tegels zijn toegevoegd, moet uw dashboard eruitzien zoals in het onderstaande voorbeeld. Als uw Event Hub-zendtoepassing en de Streaming Analytics-toepassing worden uitgevoerd, wordt uw Power BI-dashboard regelmatig bijgewerkt met nieuw ontvangen gegevens.

   ![Resultaten weergeven in Power BI-dashboard](media/stream-analytics-manage-job/power-bi-results-dashboard.png)

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
