---
title: Azure Event Hubs - Gegevensanomalieën binnen in realtime verzonden gebeurtenissen visualiseren
description: 'Zelfstudie: Gegevensanomalieën binnen in realtime naar Microsoft Azure Event Hubs verzonden gebeurtenissen visualiseren'
ms.topic: tutorial
ms.date: 06/23/2020
ms.openlocfilehash: bd08d611761e125fdd03173ce6027c60e82baed6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98623456"
---
# <a name="tutorial-visualize-data-anomalies-in-real-time-events-sent-to-azure-event-hubs"></a>Zelfstudie: gegevensanomalieën binnen in realtime naar Azure Event Hubs verzonden gebeurtenissen visualiseren

Met Azure Event Hubs kunt u Azure Stream Analytics gebruiken om de binnenkomende gegevens te controleren en de anomalieën te vinden, die u vervolgens kunt visualiseren in Power BI. Stel dat u duizenden apparaten hebt die constant realtimegegevens naar een Event Hub versturen, wat neerkomt op miljoenen gebeurtenissen per seconde. Hoe controleert u zoveel gegevens op afwijkingen, of fouten, in de gegevens? Wat bijvoorbeeld als de apparaten creditcardtransacties verzenden en u wilt registreren waar u meerdere transacties in meerdere landen/regio's hebt in een interval van 5 seconden? Dit kan gebeuren als iemand creditcards steelt en deze dan gebruikt om tegelijkertijd op verschillende plaatsen ter wereld artikelen te kopen. 

In deze zelfstudie simuleert u dit voorbeeld. U voert een toepassing uit die creditcardtransacties maakt en verzendt naar een Event Hub. Vervolgens leest u de gegevensstroom in realtime met Azure Stream Analytics, dat de geldige transacties van de ongeldige transacties scheidt, en vervolgens gebruikt u Power BI om de transacties die als ongeldig zijn gelabeld, visueel te identificeren.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Een Event Hubs-naamruimte maken
> * Een Event Hub maken
> * De app uitvoeren die creditcardtransacties verzendt
> * Een Stream Analytics-taak configureren om die transacties te verwerken
> * Een Power BI-visualisatie configureren om de resultaten te tonen

U hebt een Azure-abonnement nodig om deze zelfstudie te voltooien. Als u nog geen abonnement hebt, maakt u een [gratis account][] voordat u begint.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Installeer [Visual Studio](https://www.visualstudio.com/). 
- U hebt een Power BI-account nodig om de uitvoer van een Stream Analytics-taak te analyseren. U kunt [Power BI gratis uitproberen](https://app.powerbi.com/signupredirect?pbi_source=web).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-up-resources"></a>Resources instellen

Voor deze zelfstudie hebt u een Event Hubs-naamruimte en een Event Hub nodig. U kunt deze resources maken met Azure CLI of Azure PowerShell. Gebruik dezelfde resourcegroep en -locatie voor alle resources. Vervolgens kunt u alles aan het einde in één stap verwijderen door de resourcegroep te verwijderen.

In de volgende secties wordt beschreven hoe u deze vereiste stappen kunt uitvoeren. Volg de CLI- *of* PowerShell-instructies om de volgende stappen uit te voeren:

1. Maak een [resourcegroep](../azure-resource-manager/management/overview.md). 

2. Maak een Event Hubs-naamruimte. 

3. Maak een Event Hub.

> [!NOTE]
> Er worden in elk script variabelen ingesteld die u later in de zelfstudie nodig hebt. Dit zijn de naam van de resourcegroep ($resourceGroup), de naamruimte van de Event Hub ( **$eventHubNamespace**) en de naam van de Event Hub ( **$eventHubName** ). Hier wordt later in dit artikel naar verwezen met hun dollartekenvoorvoegsel ($), zodat u weet dat ze zijn ingesteld in het script.

<!-- some day they will approve the tab control; 
  When that happens, put CLI and PSH in tabs. -->

### <a name="set-up-your-resources-using-azure-cli"></a>Resources instellen met Azure CLI

Kopieer en plak dit script in Cloud Shell. Ervan uitgaande dat u al bent aangemeld, wordt het script met één regel tegelijk uitgevoerd.

Aan de variabelen die globaal uniek moeten zijn, wordt `$RANDOM` toegevoegd. Wanneer het script wordt uitgevoerd en de variabelen worden ingesteld, wordt een willekeurige numerieke reeks gegenereerd en samengevoegd aan het einde van de vaste reeks om deze uniek te maken.

```azurecli-interactive
# Set the values for location and resource group name.
location=westus
resourceGroup=ContosoResourcesEH

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The Event Hubs namespace name must be globally unique, so add a random number to the end.
eventHubNamespace=ContosoEHNamespace$RANDOM
echo "Event Hub Namespace = " $eventHubNamespace

# Create the Event Hubs namespace.
az eventhubs namespace create --resource-group $resourceGroup \
   --name $eventHubNamespace \
   --location $location \
   --sku Standard

# The event hub name must be globally unique, so add a random number to the end.
eventHubName=ContosoEHhub$RANDOM
echo "event hub name = " $eventHubName

# Create the event hub.
az eventhubs eventhub create --resource-group $resourceGroup \
    --namespace-name $eventHubNamespace \
    --name $eventHubName \
    --message-retention 3 \
    --partition-count 2

# Get the connection string that authenticates the app with the Event Hubs service.
connectionString=$(az eventhubs namespace authorization-rule keys list \
   --resource-group $resourceGroup \
   --namespace-name $eventHubNamespace \
   --name RootManageSharedAccessKey \
   --query primaryConnectionString \
   --output tsv)
echo "Connection string = " $connectionString 
```

### <a name="set-up-your-resources-using-azure-powershell"></a>Resources instellen met Azure PowerShell

Kopieer en plak dit script in Cloud Shell. Ervan uitgaande dat u al bent aangemeld, wordt het script met één regel tegelijk uitgevoerd.

Aan de variabelen die globaal uniek moeten zijn, wordt `$(Get-Random)` toegevoegd. Wanneer het script wordt uitgevoerd en de variabelen worden ingesteld, wordt een willekeurige numerieke reeks gegenereerd en samengevoegd aan het einde van de vaste reeks om deze uniek te maken.

```azurepowershell-interactive
# Log in to Azure account.
Login-AzAccount

# Set the values for the location and resource group.
$location = "West US"
$resourceGroup = "ContosoResourcesEH"

# Create the resource group to be used  
#   for all resources for this tutorial.
New-AzResourceGroup -Name $resourceGroup -Location $location

# The Event Hubs namespace name must be globally unique, so add a random number to the end.
$eventHubNamespace = "contosoEHNamespace$(Get-Random)"
Write-Host "Event Hub Namespace is " $eventHubNamespace

# The event hub name must be globally unique, so add a random number to the end.
$eventHubName = "contosoEHhub$(Get-Random)"
Write-Host "Event hub Name is " $eventHubName

# Create the Event Hubs namespace.
New-AzEventHubNamespace -ResourceGroupName $resourceGroup `
     -NamespaceName $eventHubNamespace `
     -Location $location

# Create the event hub.
$yourEventHub = New-AzEventHub -ResourceGroupName $resourceGroup `
    -NamespaceName $eventHubNamespace `
    -Name $eventHubName `
    -MessageRetentionInDays 3 `
    -PartitionCount 2

# Get the event hub key, and retrieve the connection string from that object.
# You need this to run the app that sends test messages to the event hub.
$eventHubKey = Get-AzEventHubKey -ResourceGroupName $resourceGroup `
    -Namespace $eventHubNamespace `
    -AuthorizationRuleName RootManageSharedAccessKey

# Save this value somewhere local for later use.
Write-Host "Connection string is " $eventHubKey.PrimaryConnectionString
```

## <a name="run-app-to-produce-test-event-data"></a>App voor het produceren van testgebeurtenisgegevens uitvoeren

De Event Hubs-[voorbeelden op GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet) bevatten een Anomaliedetectie-app die testgegevens voor u produceert. Deze app simuleert het gebruik van creditcards door creditcardtransacties naar de Event Hub te schrijven, inclusief het af en toe schrijven van meerdere transacties voor dezelfde creditcard op meerdere locaties, zodat ze als anomalieën worden getagd. Volg deze stappen voor het uitvoeren van deze app: 

1. Download de [Azure Event Hubs-voorbeelden](https://github.com/Azure/azure-event-hubs/archive/master.zip) van GitHub en pak het bestand lokaal uit.
2. Ga naar de map **\azure-event-hubs-master\samples\DotNet\\** . 
3. Schakel over naar de map **Azure.Messaging.EventHubs\AnomalyDetector\\** en dubbelklik op **AnomalyDetector.sln** om de oplossing in Visual Studio te openen. 

    Als u de oude versie van het voorbeeld wilt gebruiken, dat gebruikmaakt van het oude pakket Microsoft.Azure.EventHubs, open dan de oplossing uit de map **Microsoft.Azure.EventHubs\AnomalyDetector**. 
3. Open Program.cs en vervang **Event Hubs connection string** door de verbindingsreeks die u bij het uitvoeren van het script hebt opgeslagen. 
4. Vervang **Event Hub name** door de naam van uw Event Hub. Klik op F5 om de toepassing uit te voeren. Deze begint met het verzenden van gebeurtenissen naar uw Event Hub en gaat door totdat er 1000 gebeurtenissen zijn verzonden. Er zijn een paar gevallen waarin u alleen gegevens kunt ophalen als de app actief is. Deze gevallen worden waar nodig aangegeven in de volgende instructies.

## <a name="set-up-azure-stream-analytics"></a>Azure Stream Analytics instellen

U kunt nu gegevens streamen naar uw Event Hub. Als u die gegevens in een Power BI-visualisatie wilt gebruiken, begint u met het instellen van een Stream Analytics-taak om de gegevens op te halen, die vervolgens worden ingevoerd in de Power BI-visualisatie.

### <a name="create-the-stream-analytics-job"></a>De Stream Analytics-taak maken

1. Klik in Azure Portal op **Een resource maken**. Typ **stream analytics** in het zoekvak en druk op **Enter**. Selecteer **Stream Analytics Job**. Klik op **Maken** in het taakvenster van Stream Analytics. 

2. Voer de volgende informatie in voor de taak:

   **Taaknaam**: gebruik **contosoEHjob**. Dit veld is de naam van de taak en moet globaal uniek zijn.

   **Abonnement**: Selecteer uw abonnement.

   **Resourcegroep**: gebruik dezelfde resourcegroep die wordt gebruikt door uw Event Hub (**ContosoResourcesEH**).

   **Locatie**: gebruik dezelfde locatie die u in het installatiescript hebt gebruikt (**VS - west**).

   ![Schermopname waarin wordt weergegeven hoe u een nieuwe Azure Stream Analytics-taak maakt.](./media/event-hubs-tutorial-visualize-anomalies/stream-analytics-add-job.png)

    Accepteer de standaardwaarden voor de rest van de velden. Klik op **Create**. 

### <a name="add-an-input-to-the-stream-analytics-job"></a>Een invoer aan de Stream Analytics-taak toevoegen

Als u zich niet in het deelvenster **Stream Analytics-taak** in de portal bevindt, kunt u teruggaan naar uw Stream Analytics-taak door op **Resourcegroepen** in de portal te klikken en vervolgens uw resourcegroep te selecteren (**ContosoResourcesEH**). Deze actie toont alle resources in de groep, waarna u uw stroomanalysetaak kunt selecteren. 

De invoer voor de Stream Analytics-taak zijn de creditcardtransacties van de Event Hub.

> [!NOTE]
> De waarden van variabelen die beginnen met het dollarteken ($) worden ingesteld met de opstartscripts in de voorgaande secties. U moet hier dezelfde waarden gebruiken bij het opgeven van die velden. Dit zijn de Event Hubs-naamruimte en de naam van de Event Hub.

1. Klik onder **Taaktopologie** op **Invoer**.

2. Klik in het deelvenster **Invoer** op **Stroominvoer toevoegen** en selecteer Event Hubs. Vul de volgende velden in op het scherm dat wordt weergegeven:

   **Invoeralias**: Gebruik **contosoinputs**. Dit veld is de naam van de invoerstroom die wordt gebruikt bij het definiëren van de query voor de gegevens.

   **Abonnement**: Selecteer uw abonnement.

   **Event Hubs-naamruimte**: selecteer uw Event Hub-naamruimte ($**eventHubNamespace**). 

   **Event Hub-naam**: klik op **Bestaande gebruiken** en selecteer uw Event Hub ($**eventHubName**).

   **Naam van het Event Hubs-beleid**: selecteer **RootManageSharedAccessKey**.

   **Event Hubs-consumentengroep**: laat dit veld leeg als u de standaardconsumentengroep wilt gebruiken.

   Accepteer de standaardwaarden voor de rest van de velden.

   ![Schermopname waarin wordt weergegeven hoe u een invoerstroom kunt toevoegen aan de Stream Analytics-taak.](./media/event-hubs-tutorial-visualize-anomalies/stream-analytics-inputs.png)

5. Klik op **Opslaan**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Een uitvoer aan de Stream Analytics-taak toevoegen

1. Klik onder **Taaktopologie** op **Uitvoer**. Dit veld is de naam van de uitvoerstroom die wordt gebruikt bij het definiëren van de query voor de gegevens.

2. Klik in het deelvenster **Uitvoer** op **Toevoegen** en selecteer **Power BI**. Vul de volgende velden in op het scherm dat wordt weergegeven:

   **Uitvoeralias**: gebruik **contosooutputs**. Dit veld is de unieke alias voor de uitvoer. 

   **Naam van gegevensset**: gebruik **contosoehdataset**. Dit veld is de naam van de gegevensset die moet worden gebruikt in Power BI. 

   **Tabelnaam**: gebruik **contosoehtable**. Dit veld is de naam van de gegevensset die moet worden gebruikt in Power BI. 

   Accepteer de standaardwaarden voor de rest van de velden.

   ![Schermopname waarin wordt weergegeven hoe u de uitvoerwaarden voor een Stream Analytics-taak kunt instellen.](./media/event-hubs-tutorial-visualize-anomalies/stream-analytics-outputs.png)

3. Klik op **Autoriseren** en meld u aan bij uw Power BI-account.

4. Accepteer de standaardwaarden voor de rest van de velden.

5. Klik op **Opslaan**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>De query van de Stream Analytics-taak configureren

Deze query wordt gebruikt om de gegevens op te halen die uiteindelijk naar de Power BI-visualisatie worden verzonden. In de query wordt gebruikgemaakt van **contosoinputs** en **contosooutputs**, die u eerder hebt gedefinieerd bij het instellen van de taak. Met deze query worden de creditcardtransacties opgehaald die frauduleus lijken te zijn; dit zijn transacties waarbij hetzelfde creditcardnummer meerdere transacties heeft op verschillende locaties binnen hetzelfde interval van vijf seconden.

1. Klik onder **Taaktopologie** op **Query**.

2. Vervang de query door de volgende: 

   ```SQL
   /* criteria for fraud:
      credit card purchases with the same card
      in different locations within 5 seconds
   */
   SELECT System.Timestamp AS WindowEnd, 
     COUNT(*) as FraudulentUses      
   INTO contosooutputs
   FROM contosoinputs CS1 TIMESTAMP BY [Timestamp]
       JOIN contosoinputs CS2 TIMESTAMP BY [Timestamp]
       /* where the credit card # is the same */
       ON CS1.CreditCardId = CS2.CreditCardId
       /* and time between the two is between 0 and 5 seconds */
       AND DATEDIFF(second, CS1, CS2) BETWEEN 0 AND 5
       /* where the location is different */
   WHERE CS1.Location != CS2.Location
   GROUP BY TumblingWindow(Duration(second, 1))
   ```

4. Klik op **Opslaan**.

### <a name="test-the-query-for-the-stream-analytics-job"></a>De query voor de Stream Analytics-taak testen 

1. Voer de app Anomaly Detector uit om gegevens naar de Event Hub te verzenden terwijl u de test instelt en uitvoert. 

2. Klik in het deelvenster Query op de puntjes naast de invoer **contosoinputs** en selecteer vervolgens **Steekproef nemen van gegevens uit de invoer**.

3. Geef aan dat u drie minuten aan gegevens wilt en klik vervolgens op **OK**. Wacht tot u een melding krijgt dat de gegevens zijn verzameld.

4. Klik op **Test** en controleer of u resultaten krijgt. De resultaten worden weergegeven in het gedeelte **Resultaten** van het onderste deelvenster rechts onder de query.

5. Sluit het deelvenster Query.

### <a name="run-the-stream-analytics-job"></a>De Stream Analytics-taak uitvoeren

Klik in de Stream Analytics-taak op **Start**, vervolgens op **Nu**  en daarna op **Start**. Zodra de taak kan worden gestart, wordt de taakstatus veranderd van **Gestopt** naar **In uitvoering**.

## <a name="set-up-the-power-bi-visualizations"></a>De Power BI-visualisaties instellen

1. Voer de app Anomaly Detector uit om gegevens naar de Event Hub te verzenden terwijl u de Power BI-visualisatie instelt. U moet de app mogelijk meerdere keren uitvoeren, omdat er slechts 1000 transacties worden gegenereerd wanneer deze wordt uitgevoerd.

2. Meld u aan bij uw [Power BI](https://powerbi.microsoft.com/)-account.

3. Ga naar **Mijn werkruimte**.

4. Klik op **Gegevenssets**.

   U zou de gegevensset moeten zien die u hebt opgegeven toen u de uitvoer voor de Stream Analytics-taak hebt gemaakt (**contosoehdataset**). Het kan de eerste keer 5–10 minuten duren voordat de gegevensset wordt weergegeven.

5. Klik op **Dashboards**, klik vervolgens op **Maken** en selecteer **Dashboard**.

   ![Schermafbeelding van de knoppen Dashboards en Maken.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-add-dashboard.png)

6. Geef de naam van het dashboard op en klik op **Maken**. Gebruik **Credit Card Anomalies**.

   ![Schermopname van het opgeven van de dashboardnaam.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-dashboard-name.png)

7. Klik op de dashboardpagina op **Tegel toevoegen**, selecteer **Aangepaste streaminggegevens**  in het gedeelte **REALTIMEGEGEVENS** en klik op **Volgende**.

   ![Schermafbeelding van het opgeven van de bron voor een tegel.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-add-card-real-time-data.png)

8. Selecteer uw gegevensset (**contosoehdataset**) en klik op **Volgende**.

   ![Schermafbeelding van het opgeven van een gegevensset.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-dashboard-select-dataset.png)

9. Selecteer **Kaart** als visualisatietype. Klik onder **velden** op **waarde toevoegen** en selecteer `fraudulentuses` .

   ![Schermafbeelding van het opgeven van visualisatietype en velden.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-add-card-tile.png)

   Klik op **Volgende**.

10. Stel de titel in op **Fraudulent uses** (frauduleus gebruik) en de subtitel op **Sum in last few minutes** (totaal in de afgelopen paar minuten). Klik op **Toepassen**. De tegel wordt opgeslagen op uw dashboard.

    ![Schermafbeelding van het opgeven van de titel en subtitel voor een dashboardtegel.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-tile-details.png)

    > [!IMPORTANT]
    > Wanneer u de voorbeeldtoepassing uitvoert en gegevens naar de hub van het evenement streamt, verandert het nummer op deze tegel snel (elke seconde). Dat komt doordat de Stream Analytics-query de waarde **elke seconde** bijwerkt. Update de query naar een tumblingvenster van 3 minuten om het totaal van de laatste minuten te zien. 
11. Voeg nog een visualisatie toe. Herhaal de eerste paar stappen:

    * Klik op **Tegel toevoegen**.
    * Selecteer **Aangepaste streaminggegevens**. 
    * Klik op **Volgende**.
    * Selecteer uw gegevensset en klik op **Volgende**. 

12. Selecteer onder **Type visualisatie** de optie **Lijndiagram**.

13. Klik onder **as** op **waarde toevoegen** en selecteer `windowend` . 

14. Klik onder **waarden** op **waarde toevoegen** en selecteer `fraudulentuses` .

15. Selecteer onder **Tijdvenster voor weergave** de laatste vijf minuten. Klik op **Volgende**.

16. Geef **Show fraudulent uses over time** (frauduleus gebruik in de loop der tijd weergeven) op als de titel en laat de subtitel van de tegel leeg; klik op **Toepassen**. U keert terug naar het dashboard.

17. Voer de app Anomaly Detector nogmaals uit om gegevens naar de Event Hub te verzenden. U ziet dat de tegel **Fraudulent uses** verandert terwijl de gegevens worden geanalyseerd, en het lijndiagram toont gegevens. 

    ![Schermopname van Power BI-resultaten](./media/event-hubs-tutorial-visualize-anomalies/power-bi-results.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u alle resources die u hebt gemaakt, wilt verwijderen, verwijdert u de visualisatiegegevens van Power BI, en vervolgens de resourcegroep. Als u de resourcegroep verwijdert, worden alle resources verwijderd die deze groep bevat. In dit geval worden de Event Hub, de Event Hub-naamruimte, de stroommanalysetaak en de resourcegroep zelf verwijderd. 

### <a name="clean-up-resources-in-the-power-bi-visualization"></a>Resources in de Power BI-visualisatie opschonen

Meld u aan bij uw Power BI-account. Ga naar **Mijn werkruimte**. Klik op de regel met uw dashboardnaam op het prullenbakpictogram. Ga vervolgens naar **Gegevenssets** en klik op het prullenbakpictogram om de gegevensset (**contosoehdataset**) te verwijderen.

### <a name="clean-up-resources-using-azure-cli"></a>Resources opschonen met Azure CLI

U kunt de resourcegroep verwijderen met de opdracht [az group delete](/cli/azure/group#az-group-delete).

```azurecli-interactive
az group delete --name $resourceGroup
```

### <a name="clean-up-resources-using-powershell"></a>Resources opschonen met PowerShell

U kunt de resourcegroep verwijderen met de opdracht [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:
> [!div class="checklist"]
> * Een Event Hubs-naamruimte maken
> * Een Event Hub maken
> * De app uitvoeren die gebeurtenissen simuleert en naar de Event Hub verzendt
> * Een Stream Analytics-taak configureren om naar de hub verzonden gebeurtenissen te verwerken
> * Een Power BI-visualisatie configureren om de resultaten te tonen

Ga naar het volgende artikel voor meer informatie over Azure Event Hubs.

> [!div class="nextstepaction"]
> [Aan de slag met het verzenden van berichten naar Azure Event Hubs in .NET Standard](event-hubs-dotnet-standard-getstarted-send.md)

[gratis account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
