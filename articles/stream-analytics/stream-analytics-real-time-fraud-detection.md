---
title: Fraudedetectie in realtime met Azure Stream Analytics
description: Meer informatie over het maken van een real-time fraude detectie oplossing met Stream Analytics. Gebruik een Event Hub voor de verwerking van real-time gebeurtenis.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/24/2020
ms.custom: seodec18
ms.openlocfilehash: ba216e41672e1d19e552b3f82a2ea65da7d3a435
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96007083"
---
# <a name="get-started-using-azure-stream-analytics-real-time-fraud-detection"></a>Aan de slag met Azure Stream Analytics: fraude detectie in realtime

Deze zelf studie biedt een end-to-end illustratie van het gebruik van Azure Stream Analytics. In deze zelfstudie leert u procedures om het volgende te doen: 

* Streaming-gebeurtenissen naar een exemplaar van Azure Event Hubs brengen. In deze zelf studie gebruikt u een app die een stroom van de meta gegevens van mobiele telefoon records simuleert.

* Schrijf SQL-achtige Stream Analytics query's voor het transformeren van gegevens, het samen voegen van informatie of het zoeken naar patronen. U ziet hoe u een query kunt gebruiken om de inkomende stroom te controleren en te zoeken naar aanroepen die mogelijk frauduleus zijn.

* De resultaten verzenden naar een uitvoer Sink (opslag) die u kunt analyseren voor extra inzichten. In dit geval verzendt u de verdachte oproep gegevens naar Azure Blob-opslag.

In deze zelf studie wordt het voor beeld van fraude detectie in realtime gebruikt op basis van gegevens van telefoon gesprekken. De techniek die wordt geïllustreerd is ook geschikt voor andere typen fraude detectie, zoals fraude via credit card of identiteits diefstal. 

## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>Scenario: fraude detectie voor telecom municatie en SIM in realtime

Een telecommunicatie bedrijf heeft een grote hoeveelheid gegevens voor inkomende oproepen. Het bedrijf wil frauduleuze aanroepen in real-time detecteren zodat ze klanten kunnen informeren of service afsluiten voor een specifiek nummer. Eén type SIM-fraude omvat meerdere aanroepen van dezelfde identiteit op hetzelfde moment, maar op geografisch verschillende locaties. Om dit soort fraude te detecteren, moet het bedrijf binnenkomende telefoon records onderzoeken en zoeken naar specifieke patronen, in dit geval, voor aanroepen op hetzelfde tijdstip in verschillende landen/regio's. Alle telefoon records die in deze categorie vallen, worden naar de opslag locatie geschreven voor verdere analyse.

## <a name="prerequisites"></a>Vereisten

In deze zelf studie simuleert u gegevens van telefoon gesprekken met behulp van een client-app die de meta gegevens van een voor beeld telefoon oproep genereert. Sommige van de records die de app produceert, lijken op frauduleuze aanroepen. 

Zorg ervoor dat u het volgende hebt voordat u begint:

* Een Azure-account.
* De oproep-event generator-app, [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip), die kan worden gedownload van het micro soft Download centrum. Pak dit pakket uit in een map op uw computer. Als u de bron code wilt zien en de app in een fout opsporingsprogramma wilt uitvoeren, kunt u de app-bron code ophalen van [github](https://aka.ms/azure-stream-analytics-telcogenerator). 

    >[!NOTE]
    >Het gedownloade zip-bestand kan door Windows worden geblokkeerd. Als u deze niet kunt uitpakken, klikt u met de rechter muisknop op het bestand en selecteert u **Eigenschappen**. Als het bericht ' dit bestand is afkomstig van een andere computer en kan worden geblokkeerd om deze computer te beveiligen ' wordt weer gegeven, selecteert u de optie **deblokkeren** en klikt u vervolgens op **Toep assen**.

Als u de resultaten van de streaming Analytics-taak wilt bekijken, hebt u ook een hulp programma nodig om de inhoud van een Azure Blob Storage-container weer te geven. Als u Visual Studio gebruikt, kunt u [Azure-Hulpprogram ma's voor Visual Studio](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) of [Visual Studio Cloud Explorer](/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer)gebruiken. U kunt ook zelfstandige hulpprogram ma's installeren, zoals [Azure Storage Explorer](https://storageexplorer.com/) of [Cerulean](https://www.cerebrata.com/products/cerulean/features/azure-storage). 

## <a name="create-an-azure-event-hubs-to-ingest-events"></a>Een Azure-Event Hubs maken voor het opnemen van gebeurtenissen

Als u een gegevens stroom wilt analyseren, neemt *u deze op* in Azure. Een typische manier om gegevens op te nemen, is door [Azure Event hubs](../event-hubs/event-hubs-about.md)te gebruiken. Hiermee kunt u miljoenen gebeurtenissen per seconde opnemen en vervolgens de gebeurtenis gegevens verwerken en opslaan. Voor deze zelf studie maakt u een Event Hub en hebt u vervolgens de oproep-event generator-app oproep gegevens verzenden naar die Event Hub.

>[!NOTE]
>Zie [een event hubs naam ruimte maken en een event hub met behulp van de Azure Portal](../event-hubs/event-hubs-create.md)voor een gedetailleerde versie van deze procedure. 

### <a name="create-a-namespace-and-event-hub"></a>Een naam ruimte en Event Hub maken
In deze procedure maakt u eerst een Event Hub naam ruimte en voegt u vervolgens een Event Hub aan die naam ruimte toe. Event hub-naam ruimten worden gebruikt om gerelateerde exemplaren van de gebeurtenis-bus logisch te groeperen. 

1. Meld u aan bij de Azure Portal en klik op **een resource maken** in de linkerbovenhoek van het scherm.

2. Selecteer **Alle services** in het menu aan de linkerkant en selecteer de **ster (`*`)** naast **Event Hubs** in de categorie **Analyse**. Controleer of **Event Hubs** is toegevoegd aan **FAVORIETEN** in het navigatiemenu aan de linkerkant. 

   ![Zoeken naar Event Hubs](./media/stream-analytics-real-time-fraud-detection/select-event-hubs-menu.png)

3. Selecteer **Event Hubs** onder **FAVORIETEN** in het navigatiemenu links en selecteer **Toevoegen** op de werkbalk.

   ![Knop Toevoegen](./media/stream-analytics-real-time-fraud-detection/event-hubs-add-toolbar.png)

4. Voer in het deel venster **naam ruimte maken** een naam in voor de naam ruimte, zoals `<yourname>-eh-ns-demo` . U kunt een wille keurige naam voor de naam ruimte gebruiken, maar de naam moet geldig zijn voor een URL en moet uniek zijn binnen Azure. 
    
5. Selecteer een abonnement en maak of kies een resource groep en klik vervolgens op **maken**.

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-namespace-new-portal.png" alt="Create event hub namespace in Azure portal" width="300px"/>

6. Wanneer de implementatie van de naam ruimte is voltooid, zoekt u de Event Hub naam ruimte in de lijst met Azure-resources. 

7. Klik op de nieuwe naam ruimte en klik in het deel venster naam ruimte op **Event hub**.

   ![De knop Event hub toevoegen voor het maken van een nieuwe Event Hub](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-button-new-portal.png)    
 
8. Geef de nieuwe Event Hub een naam `asa-eh-frauddetection-demo` . U kunt ook een andere naam gebruiken. Als u dat wel doet, noteert u deze, omdat u de naam later nodig hebt. U hoeft op dit moment geen andere opties voor de Event Hub in te stellen.

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-new-portal.png" alt="Name event hub in Azure portal" width="400px"/>
    
9. Klik op **Create**.

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Toegang verlenen tot de event hub en een verbindingsreeks ophalen

Voordat een proces gegevens naar een Event Hub kan verzenden, moet de Event Hub een beleid hebben waarmee de juiste toegang is toegestaan. Het toegangsbeleid genereert een verbindingsreeks die autorisatiegegevens bevat.

1. Klik in het deel venster gebeurtenis naam ruimte op **Event hubs** en klik vervolgens op de naam van uw nieuwe event hub.

2. Klik in het deel venster Event hub op **beleid voor gedeelde toegang** en klik vervolgens op **+ &nbsp; toevoegen**.

    > [!NOTE]
    > Zorg ervoor dat u werkt met de Event Hub, niet de Event Hub naam ruimte.

3. Voeg een beleid toe `asa-policy-manage-demo` met de naam en voor **claim**, selecteer **beheren**.

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-shared-access-policy-manage-new-portal.png" alt="Create shared access policy for Stream Analytics" width="300px"/>
 
4. Klik op **Create**.

5. Nadat het beleid is geïmplementeerd, klikt u erop in de lijst met beleid voor gedeelde toegang.

6. Zoek het vak **VERBINDINGS reeks met de naam primaire sleutel** en klik op de knop kopiëren naast het Connection String. 

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-shared-access-policy-copy-connection-string-new-portal.png" alt="Stream Analytics shared access policy" width="300px"/>
 
7. Plak de verbindingsreeks in een teksteditor. U hebt deze connection string nodig voor de volgende sectie, nadat u enkele kleine wijzigingen hebt aangebracht.

    De connection string ziet er als volgt uit:

    `Endpoint=sb://YOURNAME-eh-ns-demo.servicebus.windows.net/;SharedAccessKeyName=asa-policy-manage-demo;SharedAccessKey=Gw2NFZwU1Di+rxA2T+6hJYAtFExKRXaC2oSQa0ZsPkI=;EntityPath=asa-eh-frauddetection-demo`

    U ziet dat de Connection String meerdere sleutel-waardeparen bevat, gescheiden door punt komma's: `Endpoint` ,, en `SharedAccessKeyName` `SharedAccessKey` `EntityPath` .  


## <a name="configure-and-start-the-event-generator-application"></a>De toepassing voor gebeurtenis Generator configureren en starten

Voordat u de TelcoGenerator-app start, moet u deze zo configureren dat de oproep records worden verzonden naar de Event Hub die u hebt gemaakt.

### <a name="configure-the-telcogenerator-app"></a>De TelcoGenerator-app configureren

1. In de editor waar u de connection string hebt gekopieerd, noteert u de `EntityPath` waarde en verwijdert u vervolgens het `EntityPath` paar (Vergeet niet om de punt komma die eraan voorafgaat te verwijderen). 

2. Open in de map waar u het TelcoGenerator.zip bestand uitpakt het telcodatagen.exe.config-bestand in een editor. (Er is meer dan één. config-bestand, dus zorg ervoor dat u de juiste versie opent.)

3. In het `<appSettings>` element:

   * Stel de waarde van de `EventHubName` sleutel in op de naam van de Event hub (dat wil zeggen, de waarde van het pad van de entiteit).
   * Stel de waarde van de `Microsoft.ServiceBus.ConnectionString` sleutel in op de Connection String. 

   De `<appSettings>` sectie ziet eruit als in het volgende voor beeld:

    ```xml
    <appSettings>
     <!-- Service Bus specific app setings for messaging connections -->
     <add key="EventHubName" value="asa-eh-ns-demo"/>
     <add key="Microsoft.ServiceBus.ConnectionString" value="Endpoint=sb://asa-eh-ns-demo.servicebus.windows.net/;SharedAccessKeyName=asa-policy-manage-demo;SharedAccessKey=GEcnTKf2//1MRn6SN1A2u0O76MP9pj3v0Ccyf1su4Zo="/>
   </appSettings>
    ```
 
4. Sla het bestand op. 

### <a name="start-the-app"></a>De app starten

1. Open een opdracht venster en ga naar de map waarin de TelcoGenerator-app is uitgepakt.

2. Voer de volgende opdracht in:

    ```console
    telcodatagen.exe 1000 0.2 2
    ```

   De parameters zijn: 

   * Aantal CDRs per uur. 
   * Fraude kans op SIM-kaart: hoe vaak, als percentage van alle aanroepen, de app een frauduleuze oproep moet simuleren. Een waarde van 0.2 betekent dat ongeveer 20% van de gespreksrecords er frauduleus uitziet.
   * Duur in uren. Het aantal uren dat de app moet worden uitgevoerd. U kunt de app ook op elk gewenst moment stoppen door op CTRL + C te drukken op de opdracht regel.

   Na enkele seconden begint de app met het weergeven van telefoongesprekrecords op het scherm wanneer deze naar de event hub worden gestuurd.

Enkele van de belangrijkste velden die u in deze realtime-detectie toepassing gebruikt, zijn de volgende:

|**Record**|**Definitie**|
|----------|--------------|
|`CallrecTime`|Het tijdstempel voor de begintijd van de oproep. |
|`SwitchNum`|Het schakelnummer van de oproep. In dit voorbeeld zijn de schakelnummers tekenreeksen die staan voor het land/de regio van herkomst (VS, China, UK, Duitsland of Australië). |
|`CallingNum`|Het telefoonnummer van de beller. |
|`CallingIMSI`|De International Mobile Subscriber Identity (IMSI). Dit is de unieke id van de aanroeper. |
|`CalledNum`|Het telefoonnummer van de ontvanger. |
|`CalledIMSI`|De International Mobile Subscriber Identity (IMSI). Dit is de unieke id van de ontvanger van het gesprek. |


## <a name="create-a-stream-analytics-job-to-manage-streaming-data"></a>Een Stream Analytics taak maken voor het beheren van streaminggegevens

Nu u een stroom van oproep gebeurtenissen hebt, kunt u een Stream Analytics taak instellen. Met de taak worden gegevens uit de Event Hub die u hebt ingesteld, gelezen. 

### <a name="create-the-job"></a>De taak maken 

1. Klik in de Azure Portal op **een resource maken**  >  **Internet of Things**  >  **Stream Analytics taak**.

2. Noem de taak `asa_frauddetection_job_demo` , geef een abonnement, resource groep en locatie op.

    Het is een goed idee om de taak en de Event Hub in dezelfde regio te plaatsen voor de beste prestaties en om ervoor te zorgen dat u niet betaalt voor het overdragen van gegevens tussen regio's.

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-job-new-portal.png" alt="Create Stream Analytics job in portal" width="300px"/>

3. Klik op **Create**.

    De taak wordt gemaakt en er worden taak details weer gegeven in de portal. Er wordt nog niets uitgevoerd, maar u moet de taak configureren voordat deze kan worden gestart.

### <a name="configure-job-input"></a>Taakinvoer configureren

1. Zoek in het deel venster dash board of **alle resources** naar de stream Analytics taak en selecteer deze `asa_frauddetection_job_demo` . 
2. Klik in de sectie **overzicht** van het deel venster stream Analytics taak op **het invoervak** .

   ![Invoervak onder topologie in het deel venster streaming Analytics-taak](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-input-box-new-portal.png)
 
3. Klik op **stroom invoer toevoegen** en selecteer **Event hub**. Vul vervolgens de nieuwe invoer pagina in met de volgende gegevens:

   |**Instelling**  |**Voorgestelde waarde**  |**Beschrijving**  |
   |---------|---------|---------|
   |Invoeralias  |  CallStream   |  Voer een naam in voor de invoer van de taak.   |
   |Abonnement   |  \<Your subscription\> |  Selecteer het Azure-abonnement met de Event hub die u hebt gemaakt.   |
   |Event hub-naamruimte  |  ASA-eh-NS-demo |  Voer de naam van de Event hub-naam ruimte in.   |
   |Event Hub-naam  | ASA-eh-frauddetection-demo | Selecteer de naam van uw event hub.   |
   |Naam van het Event Hub-beleid  | ASA-beleid-beheren-demo | Selecteer het toegangs beleid dat u eerder hebt gemaakt.   |

    </br>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-input-new-portal.png" alt="Create Stream Analytics input in portal" width="300px"/>


4. Klik op **Create**.

## <a name="create-queries-to-transform-real-time-data"></a>Query's maken om real-time gegevens te transformeren

Op dit moment hebt u een Stream Analytics-taak ingesteld om een binnenkomende gegevens stroom te lezen. De volgende stap is het maken van een query die de gegevens in real-time analyseert. Stream Analytics ondersteunt een eenvoudig, declaratief query model dat trans formaties beschrijft voor realtime verwerking. De query's gebruiken een SQL-achtige taal met bepaalde extensies die specifiek zijn voor Stream Analytics. 

Een eenvoudige query kan alleen alle inkomende gegevens lezen. U kunt echter vaak query's maken die naar specifieke gegevens of naar relaties in de gegevens zoeken. In deze sectie van de zelf studie maakt en test u verschillende query's om een aantal manieren te ontdekken waarop u een invoer stroom kunt transformeren voor analyse. 

Met de query's die u hier maakt, worden alleen de getransformeerde gegevens naar het scherm weer gegeven. In een latere sectie configureert u een uitvoer Sink en een query die de getransformeerde gegevens naar die Sink schrijft.

Meer informatie over de taal vindt u in de [Azure stream Analytics query language-referentie](/stream-analytics-query/stream-analytics-query-language-reference).

### <a name="get-sample-data-for-testing-queries"></a>Voorbeeld gegevens ophalen voor het testen van query's

De TelcoGenerator-app stuurt gespreks records naar het Event Hub en uw Stream Analytics taak is geconfigureerd om te lezen van de Event Hub. U kunt een query gebruiken om de taak te testen om er zeker van te zijn dat deze correct wordt gelezen. Als u een query wilt testen in de Azure-console, hebt u voorbeeld gegevens nodig. Voor dit scenario haalt u voorbeeld gegevens op uit de stroom die wordt weer gegeven in de Event Hub.

1. Zorg ervoor dat de TelcoGenerator-app wordt uitgevoerd en dat er oproep records worden geproduceerd.
2. Ga in de portal terug naar het deel venster streaming Analytics-taak. (Als u het deel venster hebt gesloten, zoekt u `asa_frauddetection_job_demo` in het deel venster **alle resources** .)
3. Klik op het **query** -vak. In Azure worden de invoer en uitvoer weer gegeven die zijn geconfigureerd voor de taak en kunt u een query maken waarmee u de invoer stroom kunt transformeren wanneer deze naar de uitvoer wordt verzonden.
4. Klik in het **query** deel venster op de punten naast de `CallStream` invoer en selecteer vervolgens **voorbeeld gegevens uit invoer**.

   ![Menu opties voor het gebruik van voorbeeld gegevens voor de stream Analytics-taak vermelding, waarbij ' voorbeeld gegevens van invoer ' is geselecteerd](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sample-data-from-input.png)


5. Stel **minuten** in op 3 en klik vervolgens op **OK**. 
    
   ![Opties voor het bemonsteren van de invoer stroom met drie minuten geselecteerd](./media/stream-analytics-real-time-fraud-detection/stream-analytics-input-create-sample-data.png)

    Azure samples 3 minuten van gegevens uit de invoer stroom en brengt u op de hoogte wanneer de voorbeeld gegevens gereed zijn. (Dit duurt even.) 

De voorbeeldgegevens worden tijdelijk opgeslagen en zijn beschikbaar zolang u het queryvenster geopend houdt. Als u het queryvenster sluit, worden de voorbeeldgegevens verwijderd en zult u een nieuwe set voorbeeldgegevens moeten maken. 

Als alternatief kunt u een. JSON-bestand met voorbeeld gegevens [uit github](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json)ophalen en dat JSON-bestand uploaden om als voorbeeld gegevens voor de invoer te gebruiken `CallStream` . 

### <a name="test-using-a-pass-through-query"></a>Testen met behulp van een Pass Through-query

Als u elke gebeurtenis wilt archiveren, kunt u een Pass-Through-query gebruiken om alle velden in de payload van de gebeurtenis te lezen.

1. Voer in het query venster de volgende query in:
        
   ```SQL
   SELECT 
       *
   FROM 
       CallStream
   ```

    >[!NOTE]
    >Net als bij SQL zijn tref woorden niet hoofdletter gevoelig en is spaties niet significant.

    In deze query `CallStream` is de alias die u hebt opgegeven tijdens het maken van de invoer. Als u een andere alias hebt gebruikt, gebruikt u die naam in plaats daarvan.

2. Klik op **testen**.

    Met de Stream Analytics taak wordt de query uitgevoerd op basis van de voorbeeld gegevens en wordt de uitvoer onder aan het venster weer gegeven. De resultaten geven aan dat de Event hub en de streaming Analytics-taak correct zijn geconfigureerd. (Zoals vermeld, maakt u later een uitvoer Sink waarmee de query gegevens kan schrijven.)

   ![Taak uitvoer Stream Analytics, met 73 records gegenereerd](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output.png)

    Het exacte aantal records dat u ziet, is afhankelijk van het aantal records dat is vastgelegd in het voor beeld van 3 minuten.
 
### <a name="reduce-the-number-of-fields-using-a-column-projection"></a>Verminder het aantal velden met behulp van een kolom projectie

In veel gevallen heeft uw analyse niet alle kolommen nodig van de invoer stroom. U kunt een query gebruiken om een kleinere set geretourneerde velden te projecteren dan in de Pass-Through-query.

1. Wijzig de query in de code-editor naar het volgende:

    ```SQL
    SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum 
    FROM 
        CallStream
    ```

2. Klik nogmaals op **testen** . 

   ![Stream Analytics taak uitvoer voor projectie bevat 25 records](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-projection.png)
 
### <a name="count-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Aantal binnenkomende oproepen per regio: Tumblingvenstertriggers-venster met aggregatie

Stel dat u het aantal inkomende oproepen per regio wilt tellen. Bij het streamen van gegevens moet u, wanneer u statistische functies wilt uitvoeren, zoals tellen, de stroom in tijdelijke eenheden verdelen (omdat de gegevens stroom zelf oneindig) is. U kunt dit doen met behulp van een streaming Analytics- [venster functie](stream-analytics-window-functions.md). U kunt vervolgens met de gegevens in dat venster werken als een eenheid.

Voor deze trans formatie wilt u een reeks tijdelijke Vensters die geen overlap hebben: elk venster heeft een afzonderlijke set gegevens die u kunt groeperen en samen voegen. Dit type venster wordt een *tumblingvenstertriggers-venster* genoemd. In het venster Tumblingvenstertriggers kunt u het aantal inkomende oproepen groeperen op `SwitchNum` , dat staat voor het land of de regio waar de oproep afkomstig is. 

1. Wijzig de query in de code-editor naar het volgende:

    ```SQL
    SELECT 
        System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount 
    FROM
        CallStream TIMESTAMP BY CallRecTime 
    GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum
    ```

    Deze query gebruikt het `Timestamp By` sleutel woord in de- `FROM` component om op te geven welk tijds tempel veld in de invoer stroom moet worden gebruikt voor het definiëren van het tumblingvenstertriggers-venster. In dit geval deelt het venster de gegevens in segmenten op basis van het `CallRecTime` veld in elke record. (Als er geen veld is opgegeven, gebruikt de venster bewerking de tijd dat elke gebeurtenis bij de Event Hub arriveert. Zie ' aankomst tijd versus toepassings tijd ' in de [Naslag informatie over de query taal van stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference). 

    De projectie bevat `System.Timestamp` , die een tijds tempel retourneert voor het einde van elk venster. 

    Als u wilt opgeven dat u een Tumblingvenstertriggers-venster wilt gebruiken, gebruikt u de functie [TUMBLINGWINDOW](/stream-analytics-query/tumbling-window-azure-stream-analytics) in de `GROUP BY` component. In de functie geeft u een tijds eenheid op (ergens van een micro seconde naar een dag) en een venster grootte (hoeveel eenheden). In dit voor beeld bestaat het Tumblingvenstertriggers-venster uit vijf seconden, zodat u per vijf seconden het aantal aanroepen kunt berekenen op basis van het land/de regio.

2. Klik nogmaals op **testen** . In de resultaten ziet u dat de tijds tempels onder **WindowEnd** zich in stappen van vijf seconden bevinden.

   ![Stream Analytics taak uitvoer voor aggregatie met 13 records](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-aggregation.png)
 
### <a name="detect-sim-fraud-using-a-self-join"></a>SIMKAART fraude detecteren met een self-deelname

Voor dit voor beeld moet u frauduleus gebruik gebruiken om aanroepen te doen die afkomstig zijn van dezelfde gebruiker, maar op verschillende locaties binnen vijf seconden van elkaar. Zo kan dezelfde gebruiker niet op rechtmatige wijze op hetzelfde moment een telefoongesprek vanuit de Verenigde Staten en Australië initiëren. 

Als u wilt controleren of dit het geval is, kunt u een self-koppeling van de streaminggegevens gebruiken om de stroom toe te voegen aan zichzelf op basis van de `CallRecTime` waarde. U kunt vervolgens zoeken naar oproep records waarbij de `CallingIMSI` waarde (het oorspronkelijke nummer) hetzelfde is, maar de `SwitchNum` waarde (land/regio van oorsprong) is niet hetzelfde.

Wanneer u een koppeling met streaminggegevens gebruikt, moet de samen voegen enkele limieten bieden voor de mate waarin de overeenkomende rijen in de tijd kunnen worden gescheiden. (Zoals eerder vermeld, zijn de streaminggegevens in feite oneindig.) De tijds limieten voor de relatie worden opgegeven in de `ON` component van de samen voeging met behulp van de `DATEDIFF` functie. In dit geval is de samen voeging gebaseerd op een interval van 5 seconden voor het aanroepen van gegevens.

1. Wijzig de query in de code-editor naar het volgende: 

    ```SQL
    SELECT  System.Timestamp as Time, 
        CS1.CallingIMSI, 
        CS1.CallingNum as CallingNum1, 
        CS2.CallingNum as CallingNum2, 
        CS1.SwitchNum as Switch1, 
        CS2.SwitchNum as Switch2 
    FROM CallStream CS1 TIMESTAMP BY CallRecTime 
        JOIN CallStream CS2 TIMESTAMP BY CallRecTime 
        ON CS1.CallingIMSI = CS2.CallingIMSI 
        AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5 
    WHERE CS1.SwitchNum != CS2.SwitchNum
    ```

    Deze query is net als elke SQL-samen voeging, behalve voor de `DATEDIFF` functie in de samen voeging. Deze versie van `DATEDIFF` is specifiek voor streaming Analytics en moet worden weer gegeven in de `ON...BETWEEN` component. De para meters zijn een tijds eenheid (seconden in dit voor beeld) en de aliassen van de twee bronnen voor de koppeling. Dit wijkt af van de standaard functie van SQL `DATEDIFF` .

    De- `WHERE` component bevat de voor waarde die de frauduleuze oproep markeert: de oorspronkelijke switches zijn niet hetzelfde. 

2. Klik nogmaals op **testen** . 

   ![Stream Analytics taak uitvoer voor Self-deelname, met 6 gegenereerde records](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-self-join.png)

3. Klik op **Opslaan** om de Self-samenvoegings query op te slaan als onderdeel van de streaming Analytics-taak. (De voorbeeld gegevens worden niet opgeslagen.)

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-query-editor-save-button-new-portal.png" alt="Save Stream Analytics query in portal" width="300px"/>

## <a name="create-an-output-sink-to-store-transformed-data"></a>Een uitvoer Sink maken om getransformeerde gegevens op te slaan

U hebt een gebeurtenis stroom, een Event Hub invoer voor opname gebeurtenissen en een query gedefinieerd voor het uitvoeren van een trans formatie over de stroom. De laatste stap bestaat uit het definiëren van een uitvoer filter voor de taak, dat wil zeggen, een plaats om de getransformeerde stroom naar te schrijven. 

U kunt veel resources gebruiken als uitvoer filters, een SQL Server-Data Base, tabel opslag, Data Lake opslag, Power BI en zelfs een andere Event Hub. Voor deze zelf studie schrijft u de stroom naar Azure Blob Storage. Dit is een typische keuze voor het verzamelen van gebeurtenis gegevens voor latere analyse, omdat deze niet-gestructureerde gegevens bevat.

Als u een bestaand Blob Storage-account hebt, kunt u dat gebruiken. Voor deze zelf studie leert u hoe u een nieuw opslag account maakt.

### <a name="create-an-azure-blob-storage-account"></a>Een Azure Blob Storage-account maken

1. Selecteer in de linkerbovenhoek in Azure Portal **Een resource maken** > **Storage** > **Storage-account**. Vul de taak pagina voor het opslag account in met de **naam** die is ingesteld op ' asaehstorage ', de **locatie** die is ingesteld op ' vs-Oost ', de **resource groep** die is ingesteld op ' ASA-eh-NS-RG ' (host het opslag account in dezelfde resource groep als de streaming-taak voor betere prestaties). De overige instellingen kunnen op de standaardwaarden blijven staan.  

   ![Opslag account maken in Azure Portal](./media/stream-analytics-real-time-fraud-detection/stream-analytics-storage-account-create.png)

2. Ga in het Azure Portal naar het deel venster streaming Analytics-taak. (Als u het deel venster hebt gesloten, zoekt u `asa_frauddetection_job_demo` in het deel venster **alle resources** .)

3. Klik in de sectie **taak topologie** op het vak **uitvoer** .

4. Klik in het deel venster **uitvoer** op **toevoegen** en selecteer **Blob Storage**. Vul vervolgens de pagina nieuwe uitvoer in met de volgende informatie:

   |**Instelling**  |**Voorgestelde waarde**  |**Beschrijving**  |
   |---------|---------|---------|
   |Uitvoeralias  |  CallStream-FraudulentCalls   |  Voer een naam in voor de uitvoer van de taak.   |
   |Abonnement   |  \<Your subscription\> |  Selecteer het Azure-abonnement met het opslagaccount dat u hebt gemaakt. Het opslagaccount kan voor hetzelfde of een ander abonnement gelden. Voor dit voorbeeld wordt aangenomen dat u een opslagaccount voor hetzelfde abonnement hebt gemaakt. |
   |Storage-account  |  asaehstorage |  Voer de naam in van het opslagaccount dat u hebt gemaakt. |
   |Container  | ASA-fraudulentcalls-demo | Kies Nieuw maken en voer een container naam in. |

    <br/>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-output-blob-storage-new-console.png" alt="Create blob output for Stream Analytics job" width="300px"/>
    
5. Klik op **Opslaan**. 


## <a name="start-the-streaming-analytics-job"></a>De streaming Analytics-taak starten

De taak is nu geconfigureerd. U hebt een invoer (de Event Hub) opgegeven, een trans formatie (de query voor het zoeken naar frauduleuze aanroepen) en een uitvoer (Blob Storage). U kunt nu de taak starten. 

1. Zorg ervoor dat de TelcoGenerator-app wordt uitgevoerd.

2. Klik in het deel venster taak op **starten**. Selecteer **nu** bij begin tijd van taak uitvoer in het deel venster **taak starten** . 

   ![De Stream Analytics-taak starten](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-start.png)



## <a name="examine-the-transformed-data"></a>De getransformeerde gegevens onderzoeken

U hebt nu een volledige streaming Analytics-taak. De taak onderzoekt een stroom van de meta gegevens van telefoon gesprekken, zoekt naar frauduleuze telefoon gesprekken in realtime en schrijft informatie over deze frauduleuze aanroepen naar opslag. 

Als u deze zelf studie wilt volt ooien, kunt u de gegevens die worden vastgelegd door de streaming Analytics-taak bekijken. De gegevens worden naar Azure-blog opslag geschreven in segmenten (bestanden). U kunt elk hulp programma gebruiken waarmee Azure-Blob Storage worden gelezen. Zoals vermeld in de sectie vereisten, kunt u Azure-extensies gebruiken in Visual Studio of u kunt een hulp programma gebruiken zoals [Azure Storage Explorer](https://storageexplorer.com/) of [Cerulean](https://www.cerebrata.com/products/cerulean/features/azure-storage). 

Wanneer u de inhoud van een bestand in Blob Storage bekijkt, ziet u ongeveer het volgende:

   ![Azure Blob-opslag met streaming Analytics-uitvoer](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-blob-storage-view.png)
 

## <a name="clean-up-resources"></a>Resources opschonen

Er zijn aanvullende artikelen die door gaan met het scenario voor fraude detectie en de resources gebruiken die u in deze zelf studie hebt gemaakt. Als u wilt door gaan, raadpleegt u de suggesties in de **volgende stappen**.

Als u echter klaar bent en u de resources die u hebt gemaakt, niet nodig hebt, kunt u ze verwijderen zodat u geen onnodige Azure-kosten in rekening brengt. In dat geval wordt u aangeraden om het volgende te doen:

1. Stop de streaming Analytics-taak. Klik in het deel venster **taken** op **stoppen** aan de bovenkant.
2. Stop de telecommunicatie Generator-app. In het opdracht venster waar u de app hebt gestart, drukt u op CTRL + C.
3. Als u voor deze zelf studie een nieuw Blob Storage-account hebt gemaakt, verwijdert u het. 
4. Verwijder de streaming Analytics-taak.
5. Verwijder de Event Hub.
6. Verwijder de Event Hub naam ruimte.

## <a name="get-support"></a>Ondersteuning krijgen

Ga voor meer hulp naar de [pagina micro soft Q&een vraag voor Azure stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Volgende stappen

U kunt door gaan met deze zelf studie met het volgende artikel:

* [Stream Analytics en Power BI: een real-time analyse dashboard voor het streamen van gegevens](stream-analytics-power-bi-dashboard.md). In dit artikel leest u hoe u de telecommunicatie-uitvoer van de Stream Analytics-taak kunt verzenden naar Power BI voor visualisaties en analyses in realtime.

Zie de volgende artikelen voor meer informatie over Stream Analytics in het algemeen:

* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaggids voor Azure Stream Analytics Query](/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](/rest/api/streamanalytics/)