---
title: Zelf studie-Azure IoT Hub-bericht routering resultaten weer geven (.NET) | Microsoft Docs
description: Zelf studie-nadat u alle resources hebt ingesteld met behulp van deel 1 van de zelf studie, voegt u de mogelijkheid toe om berichten te routeren naar Azure Stream Analytics en de resultaten in Power BI weer te geven.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2018
ms.author: robinsh
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: f441a1cf97c069c4755b436bbb8cb9268b469eb3
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102199744"
---
# <a name="tutorial-part-2---view-the-routed-messages"></a>Zelfstudie: Deel 2: de gerouteerde berichten weergeven

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="rules-for-routing-the-messages"></a>Regels voor het routeren van de berichten

Dit zijn de regels voor de routering van berichten. Deze zijn ingesteld in deel 1 van deze zelfstudie. In dit tweede deel ziet u de werking ervan.

|Waarde |Resultaat|
|------|------|
|niveau = 'opslag' |Schrijf naar Azure Storage.|
|niveau = 'kritiek' |Schrijf naar een Service Bus-wachtrij. Een logische app haalt het bericht op uit de wachtrij en gebruikt Office 365 om het bericht via e-mail te versturen.|
|standaardinstelling |Geef deze gegevens weer met Power BI.|

Maak nu de resources waarnaar de berichten worden gerouteerd, voer een app uit om berichten naar de hub te verzenden en zie de routering in actie.

## <a name="create-a-logic-app"></a>Een logische app maken  

De Service Bus-wachtrij moet worden gebruikt voor het ontvangen van berichten die zijn aangeduid als kritiek. Installeer een logische app die wordt geactiveerd om de Service Bus-wachtrij te bewaken en een e-mailbericht te verzenden wanneer een bericht wordt toegevoegd aan de wachtrij.

1. Selecteer in [Azure Portal](https://portal.azure.com) de optie **+ Een resource maken**. Typ **logische app** in het zoekvak en klik op Enter. Selecteer Logische app in de weergegeven zoekresultaten en vervolgens **Maken** om door te gaan naar het deelvenster **Logische app maken**. Vul de velden in.

   **Naam**: dit veld is de naam van de logische app. In deze zelfstudie wordt gebruikgemaakt van **ContosoLogicApp**.

   **Abonnement**: Selecteer uw Azure-abonnement.

   **Resourcegroep**: Selecteer **Bestaande gebruiken** en selecteer uw resourcegroep. In deze zelfstudie wordt gebruikgemaakt van **ContosoResources**.

   **Locatie**: gebruik uw locatie. In deze zelfstudie wordt gebruikgemaakt van **VS - west**.

   **Log Analytics inschakelen**: deze wissel knop moet worden uitgeschakeld.

   ![Het scherm Logische app maken](./media/tutorial-routing-view-message-routing-results/create-logic-app.png)

   Selecteer **Maken**. Het kan enkele minuten duren voordat de app is geïmplementeerd.

2. Ga nu naar de logische app. De eenvoudigste manier om naar de logische app te gaan is door **Resourcegroepen** te selecteren, uw resourcegroep te selecteren (in deze zelfstudie wordt **ContosoResources** gebruikt) en vervolgens de logische app in de lijst met resources te selecteren. 

    De pagina Ontwerpfunctie voor logic apps wordt weergegeven (misschien moet u naar rechts schuiven om de volledige pagina te zien). Schuif op de pagina Ontwerpfunctie voor logic apps omlaag totdat u de tegel waarin **Lege logische app +** ziet, en selecteer deze. Het standaard tabblad is 'Voor u'. Als dit deelvenster leeg is, selecteert u **Alle** om alle beschikbare connectors en triggers weer te geven.

3. Selecteer **Service Bus** in de lijst met connectors.

   ![De lijst met connectors](./media/tutorial-routing-view-message-routing-results/logic-app-connectors.png)

4. Er wordt een lijst met triggers weergegeven. Selecteer **Wanneer een bericht wordt ontvangen in een wachtrij (automatisch voltooien) / Service Bus**.

   ![De lijst met triggers voor de Service Bus](./media/tutorial-routing-view-message-routing-results/logic-app-triggers.png)

5. Vul op het volgende scherm de naam van de verbinding in. In deze zelfstudie wordt gebruikgemaakt van **ContosoConnection**.

   ![De verbinding voor de Service Bus-wachtrij instellen](./media/tutorial-routing-view-message-routing-results/logic-app-define-connection.png)

   Selecteer de Service Bus-naamruimte. In deze zelfstudie wordt gebruikgemaakt van **ContosoSBNamespace**. Wanneer u de naamruimte selecteert, vraagt de portal de Service Bus-naamruimte op voor het ophalen van de sleutels. Selecteer achtereenvolgens **RootManageSharedAccessKey** en **Maken**.

   ![Het instellen van de verbinding voltooien](./media/tutorial-routing-view-message-routing-results/logic-app-finish-connection.png)

6. Selecteer de naam van de wachtrij op het volgende scherm (in deze zelfstudie wordt gebruikgemaakt van **contososbqueue**) in de vervolgkeuzelijst. U kunt de standaardwaarden gebruiken voor de rest van de velden.

   ![De wachtrijopties](./media/tutorial-routing-view-message-routing-results/logic-app-queue-options.png)

7. Stel nu de actie in voor het verzenden van het e-mailbericht wanneer een bericht wordt ontvangen in de wachtrij. Selecteer in de ontwerpfunctie voor logische apps **+ Nieuwe stap** om een stap toe te voegen en selecteer vervolgens **Alle** om alle beschikbare opties weer te geven. Zoek en selecteer in het deelvenster **Een actie kiezen** de optie **Office 365 Outlook**. Selecteer **E-mail verzenden / Office 365 Outlook** in het scherm Acties.  

   ![De Office365-opties](./media/tutorial-routing-view-message-routing-results/logic-app-select-outlook.png)

8. Meld u aan bij uw werk- of schoolaccount om de verbinding in te stellen. Als er een time-out optreedt, probeert u het gewoon opnieuw. Geef de e-mailadressen op voor de ontvanger(s) van de e-mailberichten. Geef tevens het onderwerp op en het type berichten dat u wilt dat de ontvanger in de hoofdtekst zal zien. Vul voor het testen uw eigen e-mailadres in als ontvanger.

   Selecteer **Dynamische inhoud toevoegen** om de inhoud weer te geven van het bericht dat u kunt opnemen. Selecteer **Inhoud**: hiermee wordt het bericht in het e-mailbericht opgenomen.

   ![De e-mailopties voor de logische app](./media/tutorial-routing-view-message-routing-results/logic-app-send-email.png)

9. Selecteer **Opslaan**. Sluit daarna de ontwerpfunctie voor de logische app af.

## <a name="set-up-azure-stream-analytics"></a>Azure Stream Analytics instellen

Als u de gegevens in een Power BI-visualisatie wilt zien, stelt u eerst een Stream Analytics-taak in om de gegevens op te halen. Houd er rekening mee dat alleen de berichten waarin het **niveau****normaal** is, worden verzonden naar het standaardeindpunt en door de Stream Analytics-taak voor de Power BI-visualisatie worden opgehaald.

### <a name="create-the-stream-analytics-job"></a>De Stream Analytics-taak maken

1. Selecteer in [Azure Portal](https://portal.azure.com) de optie **Een resource maken** > **Internet of Things** > **Stream Analytics-taak**.

2. Voer de volgende informatie in voor de taak.

   **Taaknaam**: de naam van de taak. De naam moet wereldwijd uniek zijn. In deze zelfstudie wordt gebruikgemaakt van **contosoJob**.

   **Abonnement**: Het Azure-abonnement dat u gebruikt voor de zelfstudie.

   **Resourcegroep**: gebruik dezelfde resourcegroep die wordt gebruikt door uw IoT Hub. In deze zelfstudie wordt gebruikgemaakt van **ContosoResources**.

   **Locatie**: gebruik dezelfde locatie die u in het instellingsscript hebt gebruikt. In deze zelfstudie wordt gebruikgemaakt van **VS - west**.

   ![De Stream Analytics-taak maken](./media/tutorial-routing-view-message-routing-results/stream-analytics-create-job.png)

3. Selecteer **Maken** om de taak te maken. Het implementeren van de taak kan een paar minuten duren.

    Als u wilt teruggaan naar de taak, selecteert u **Resourcegroepen**. In deze zelfstudie wordt gebruikgemaakt van **ContosoResources**. Selecteer de resourcegroep en selecteer vervolgens de Stream Analytics-taak in de lijst met resources.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Een invoer aan de Stream Analytics-taak toevoegen

1. Selecteer **Invoer** onder **Taaktopologie**.

2. Selecteer **Stroominvoer toevoegen** in het deelvenster **Invoer** en selecteer vervolgens IoT Hub. Vul de volgende velden in op het scherm dat wordt weergegeven:

   **Invoeralias**: in deze zelfstudie wordt gebruikgemaakt **contosoinputs**.

   **IoT Hub selecteren bij uw abonnementen**: Selecteer dit keuzerondje.

   **Abonnement**: Selecteer het Azure-abonnement dat u gebruikt voor deze zelfstudie.

   **IoT Hub**: Selecteer de IoT-hub. In deze zelfstudie wordt gebruikgemaakt van **ContosoTestHub**.

   **Eindpunt**: selecteer **Berichten**. (Als u Operations Monitoring selecteert, krijgt u de telemetriegegevens over de IoT Hub in plaats van de gegevens die u doorstuurt.) 

   **Naam van het gedeelde toegangsbeleid**: Selecteer **service**. In de portal wordt de sleutel van het beleid voor gedeelde toegang voor u ingevuld.

   **Consumentengroep**: Selecteer de consumentengroep die is ingesteld in deel 1 van deze zelfstudie. In deze zelfstudie wordt gebruikgemaakt van **contosoconsumers**.
   
   Accepteer de standaardwaarden voor de rest van de velden. 

   ![De invoerwaarden voor de Stream Analytics-taak instellen](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-inputs.png)

3. Selecteer **Opslaan**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Een uitvoer aan de Stream Analytics-taak toevoegen

1. Selecteer **Uitvoer** onder **Taaktopologie**.

2. Selecteer in het deelvenster **Uitvoer** de optie **Toevoegen** en selecteer vervolgens **Power BI**. Vul de volgende velden in op het scherm dat wordt weergegeven:

   **Uitvoeralias**: de alias die uniek is voor de uitvoer. In deze zelfstudie wordt gebruikgemaakt van **contosooutputs**. 

   **Naam van de gegevensset**: de naam van de gegevensset die moet worden gebruikt in Power BI. In deze zelfstudie wordt gebruikgemaakt van **contosodataset**. 

   **Tabelnaam**: de naam van de tabel die moet worden gebruikt in Power BI. In deze zelfstudie wordt gebruikgemaakt van **contosotable**.

  **Verificatie modus**: Selecteer de modus die u wilt gebruiken.

   Accepteer de standaardwaarden voor de rest van de velden.

3. Selecteer **Autoriseren** en meld u aan bij uw Power BI-account. (Probeer het zo nodig nogmaals.)

   ![De uitvoerwaarden voor de Stream Analytics-taak instellen](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-outputs.png)

4. Selecteer **Opslaan**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>De query van de Stream Analytics-taak configureren

1. Selecteer **Query** onder **Taaktopologie**.

2. Vervang `[YourInputAlias]` door de invoeralias van de taak. In deze zelfstudie wordt gebruikgemaakt van **contosoinputs**.

3. Vervang `[YourOutputAlias]` door de uitvoeralias van de taak. In deze zelfstudie wordt gebruikgemaakt van **contosooutputs**.

   ![De query voor de Stream Analytics-taak instellen](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-query.png)

4. Selecteer **Opslaan**.

5. Sluit het deelvenster Query. U keert terug naar de weergave van de resources in de resourcegroep. Selecteer de Stream Analytics-taak. In deze zelfstudie heet deze taak **contosoJob**.

### <a name="run-the-stream-analytics-job"></a>De Stream Analytics-taak uitvoeren

Selecteer in de Stream Analytics-taak achtereenvolgens **Start** > **Nu** > **Start**. Zodra de taak kan worden gestart, wordt de taakstatus veranderd van **Gestopt** naar **In uitvoering**.

Als u het Power BI-rapport instelt, hebt u gegevens nodig, dus stelt u Power BI in nadat u het apparaat hebt gemaakt en de simulatietoepassing van het apparaat hebt ingesteld.

## <a name="run-simulated-device-app"></a>De app voor een gesimuleerd apparaat uitvoeren

In deel 1 van deze zelfstudie hebt u een apparaat ingesteld om het gebruik van een IoT-apparaat te simuleren. In deze sectie downloadt u de .NET-console-app die het apparaat simuleert dat apparaat-naar-cloud-berichten verzendt naar een IoT-hub (als u de app en resources nog niet hebt gedownload in deel 1).

Deze toepassing verzendt berichten voor elk van de verschillende berichtrouteringsmethoden. Een map in de download bevat ook de ingevulde Azure Resource Manager-sjabloon en het parameterbestand, evenals de Azure CLI- en PowerShell-scripts.

Als u de bestanden nog niet uit de opslagplaats hebt gedownload in deel 1 van deze zelfstudie, kunt u deze nu downloaden van [IoT Device Simulation](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Met deze koppeling downloadt u een opslagplaats die meerdere toepassingen bevat. De oplossing die u zoekt, is iot-hub/Tutorials/Routing/IoT_SimulatedDevice.sln. 

Dubbelklik op het oplossingsbestand (IoT_SimulatedDevice.sln) om de code in Visual Studio te openen en open vervolgens Program.cs. Vervang `{your hub name}` door de hostnaam van de IoT Hub. De indeling van de hostnaam van de IoT Hub is **{iot-hub-name} .azure-devices.net**. Voor deze zelfstudie is de naam van de hubhost **ContosoTestHub.azure devices.net**. Vervang vervolgens `{your device key}` door de apparaatsleutel die u eerder hebt opgeslagen bij het instellen van het gesimuleerde apparaat. 

   ```csharp
        static string s_myDeviceId = "Contoso-Test-Device";
        static string s_iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string s_deviceKey = "{your device key}";
   ```

## <a name="run-and-test"></a>Uitvoeren en testen

Voer de consoletoepassing uit. Wacht enkele minuten. U kunt de berichten zien die op het scherm van de console van de toepassing worden verzonden.

Deze app verzendt elke seconde een nieuw apparaat-naar-cloud-bericht naar de IoT Hub. Het bericht bevat een JSON-geserialiseerd object met een apparaat-id, temperatuur, vochtigheid en berichtniveau, die als standaardwaarde `normal` heeft. Hiermee wordt een willekeurig niveau `critical` of `storage` toegewezen, zodat het bericht wordt doorgestuurd naar het opslagaccount of naar de Service Bus-wachtrij (die uw logische app aanzet tot het verzenden van een e-mailbericht). De standaardmeetwaarden (`normal`) worden weergegeven in het BI-rapport dat u daarna instelt.

Als alles juist is ingesteld, zou u op dit moment de volgende resultaten moeten zien:

1. U gaat e-mailberichten ontvangen over kritieke berichten.

   ![De resulterende e-mailberichten](./media/tutorial-routing-view-message-routing-results/results-in-email.png)

   Dit resultaat betekent het volgende. 

   * De routering naar de Service Bus-wachtrij werkt correct.
   * De logische app die het bericht uit de Service Bus-wachtrij ophaalt, werkt correct.
   * De connector van de logische app voor Outlook werkt goed. 

2. Selecteer in [Azure Portal](https://portal.azure.com) de optie **Resourcegroepen** en selecteer uw resourcegroep. In deze zelfstudie wordt gebruikgemaakt van **ContosoResources**. 

    Selecteer het opslagaccount, selecteer **Containers** en selecteer vervolgens de container. In deze zelfstudie wordt gebruikgemaakt van **contosoresults**. U zou een map moeten zien, en u kunt inzoomen via de mappen totdat u een of meer bestanden ziet. Open een van deze bestanden; ze bevatten de vermeldingen die zijn doorgestuurd naar het opslagaccount. 

   ![De resulterende bestanden in de opslag](./media/tutorial-routing-view-message-routing-results/results-in-storage.png)

Dit resultaat betekent het volgende.

   * De routering naar de Service Bus-wachtrij werkt correct.

Stel nu de Power BI-visualisatie in terwijl de toepassing nog steeds wordt uitgevoerd om de berichten te bekijken die afkomstig zijn van de standaardroutering.

## <a name="set-up-the-power-bi-visualizations"></a>De Power BI-visualisaties instellen

1. Meld u aan bij uw [Power BI](https://powerbi.microsoft.com/)-account.

2. Ga naar **Werkruimten** en selecteer de werkruimte die u hebt ingesteld toen u de uitvoer voor de Stream Analytics-taak hebt gemaakt. In deze zelfstudie wordt gebruikgemaakt van **Mijn werkruimte**. 

3. Selecteer **Gegevenssets**. Als u geen gegevens sets ziet, wacht u een paar minuten en probeert u het opnieuw.

   U zou de vermelde gegevensset moeten zien die u hebt opgegeven toen u de uitvoer voor de Stream Analytics-taak hebt gemaakt. In deze zelfstudie wordt gebruikgemaakt van **contosodataset**. (Het kan 5-10 minuten duren voordat de gegevensset voor de eerste keer wordt weergegeven.)

4. Selecteer onder **ACTIONS** het eerste pictogram om een rapport te maken.

   ![Power BI-werkruimte waarbij het pictogram Acties en Rapport zijn gemarkeerd](./media/tutorial-routing-view-message-routing-results/power-bi-actions.png)

5. Maak een lijndiagram om in realtime de temperatuur gedurende een bepaalde periode weer te geven.

   * Voeg op de pagina voor het maken van rapporten een lijndiagram toe door het lijndiagrampictogram te selecteren.

     ![De visualisaties en velden](./media/tutorial-routing-view-message-routing-results/power-bi-visualizations-and-fields.png)

   * Klap in het deelvenster **Velden** de tabel uit die u hebt opgegeven toen u de uitvoer voor de Stream Analytics-taak hebt gemaakt. In deze zelfstudie wordt gebruikgemaakt van **contosotable**.

   * Sleep **EventEnqueuedUtcTime** naar **As** in het deelvenster **Visualisaties**.

   * Sleep **Temperatuur** naar **Waarden**.

   Er wordt een lijndiagram gemaakt. De x-as geeft de datum en tijd in UTC-tijdzone aan. De y-as geeft de temperatuur van de sensor aan.

6. Maak een ander lijndiagram om in realtime de vochtigheid gedurende een bepaalde periode weer te geven. Volg, als u het tweede diagram wilt instellen, hetzelfde proces als voor het eerste diagram en plaats **EventEnqueuedUtcTime** op de x-as (**As**) en **Vochtigheid** op de y-as (**Waarden**).

   ![Het laatste Power BI-rapport met de twee diagrammen](./media/tutorial-routing-view-message-routing-results/power-bi-report.png)

7. Selecteer **Opslaan** om het rapport op te slaan. Als u daarom wordt gevraagd, voert u een naam in voor het rapport.

U zou gegevens moeten kunnen zien in beide diagrammen. Dit resultaat betekent het volgende:

   * De routering naar het standaardeindpunt werkt correct.
   * De Azure Stream Analytics-taak stroomt correct.
   * De Power BI-visualisatie is juist ingesteld.

U kunt de diagrammen vernieuwen om de meest recente gegevens te bekijken door de knop Vernieuwen bovenaan het Power BI-venster te selecteren. 

## <a name="clean-up-resources"></a>Resources opschonen 

Als u alle Azure-resources die u in de twee delen van deze zelfstudie hebt gemaakt, wilt verwijderen, verwijdert u de resourcegroep. Hiermee verwijdert u ook alle resources in de groep. In dit geval worden de IoT Hub, de Service Bus-naamruimte en -wachtrij, de logische app, het opslagaccount en de resourcegroep zelf verwijderd. U kunt ook de Power BI-resources verwijderen en de e-mailberichten wissen die tijdens de zelfstudie zijn verzonden.

### <a name="clean-up-resources-in-the-power-bi-visualization"></a>Resources in de Power BI-visualisatie opschonen

Meld u aan bij uw [Power BI](https://powerbi.microsoft.com/)-account. Ga naar uw werkruimte. In deze zelfstudie wordt gebruikgemaakt van **Mijn werkruimte**. Als u de Power BI-visualisatie wilt verwijderen, gaat u naar Gegevenssets en selecteert u het prullenbakpictogram om de gegevensset te verwijderen. In deze zelfstudie wordt gebruikgemaakt van **contosodataset**. Wanneer u de gegevensset verwijdert, wordt het rapport ook verwijderd.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Resources opschonen met de Azure-CLI

U kunt de resourcegroep verwijderen met de opdracht [az group delete](/cli/azure/group#az-group-delete). `$resourceGroup` is aan het begin van deze zelfstudie ingesteld op **ContosoResources**.

```azurecli-interactive
az group delete --name $resourceGroup
```

### <a name="use-powershell-to-clean-up-resources"></a>Resources opschonen met PowerShell

U kunt de resourcegroep verwijderen met de opdracht [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). `$resourceGroup` is aan het begin van deze zelfstudie ingesteld op **ContosoResources**.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroup
```

### <a name="clean-up-test-emails"></a>Test-e-mailberichten opschonen

Wellicht wilt u de e-mailberichten die via de logische app in uw postvak in zijn gegenereerd terwijl de apparaattoepassing werd uitgevoerd, verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze tweedelige zelfstudie hebt u geleerd hoe u met berichtroutering IoT Hub-berichten naar verschillende bestemmingen doorstuurt door de volgende taken uit te voeren.  

**Deel I: Resources maken, berichtroutering instellen**
> [!div class="checklist"]
> * Maak de resources: een IoT hub, een opslagaccount, een Service Bus-wachtrij en een gesimuleerd apparaat.
> * Configureer de eindpunten en berichtroutes in IoT Hub voor het opslagaccount en de Service Bus-wachtrij.

**Deel II: Berichten verzenden naar de hub, gerouteerde resultaten weergeven**
> [!div class="checklist"]
> * Maak een logische app die wordt geactiveerd en een e-mailbericht verzendt wanneer een bericht wordt toegevoegd aan de Service Bus-wachtrij.
> * Download een app die een IoT-apparaat aanzet tot het verzenden van berichten naar de hub voor de verschillende routeringsopties en voer deze uit.
> * Maak een Power BI-visualisatie voor gegevens die naar het standaardeindpunt worden verzonden.
> * Bekijk de resultaten...
> * .. .in de Service Bus-wachtrij en e-mailberichten.
> * ...in het opslagaccount.
> * ...in de Power BI-visualisatie.

Ga door naar de volgende zelfstudie voor informatie over het beheren van de toestand van een IoT-apparaat. 

> [!div class="nextstepaction"]
> [Metrische gegevens en diagnostische gegevens instellen en gebruiken met een IoT Hub](tutorial-use-metrics-and-diags.md)