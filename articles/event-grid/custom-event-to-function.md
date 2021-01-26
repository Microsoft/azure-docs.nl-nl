---
title: 'Quickstart: Aangepaste gebeurtenissen verzenden naar een Azure-functie - Event Grid'
description: 'Quickstart: Gebruik Azure Event Grid en Azure CLI of de portal om een onderwerp te publiceren en u te abonneren op deze gebeurtenis. Er wordt een Azure-functie gebruikt voor het eindpunt.'
ms.date: 07/07/2020
ms.topic: quickstart
ms.openlocfilehash: 4fe4753de41443a0537636933364c7b69b25cb27
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791724"
---
# <a name="quickstart-route-custom-events-to-an-azure-function-with-event-grid"></a>Quickstart: Aangepaste gebeurtenissen routeren naar een Azure-functie met Event Grid

Azure Event Grid is een gebeurtenisservice voor de cloud. Azure Functions is een van de ondersteunde gebeurtenishandlers. In dit artikel gebruikt u Azure Portal om een aangepast onderwerp te maken, u op het aangepaste onderwerp te abonneren, en de gebeurtenis te activeren om het resultaat weer te geven. U verzendt de gebeurtenissen naar een Azure-functie.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-azure-function"></a>Een Azure-functie maken
Voordat u zich abonneert op het aangepaste onderwerp, gaan we een functie maken om de gebeurtenissen te verwerken. 

1. Maak een functie-app maken met behulp van instructies in [Een functie-app maken](../azure-functions/functions-get-started.md).
2. Maak een functie met behulp van de **Event Grid Trigger**. Selecteer Als dit de eerste keer is dat u deze trigger gebruikt, moet u mogelijk op Installeren klikken om de extensie te installeren.
    1. Selecteer op de pagina **Functie-app** **Functies** in het linkermenu, zoek **Event Grid** bij sjablonen en selecteer vervolgens **Azure Event Grid-trigger**. 

        :::image type="content" source="./media/custom-event-to-function/function-event-grid-trigger.png" alt-text="Event Grid-trigger selecteren":::
3. Voer op de pagina **Nieuwe functie** een naam in voor de functie en selecteer **Functie maken**.

    :::image type="content" source="./media/custom-event-to-function/new-function-page.png" alt-text="Pagina Nieuwe functie":::
4. Gebruik de pagina **Code + Test** om de bestaande code voor de functie te bekijken en bij te werken. 

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="create-a-custom-topic"></a>Een aangepast onderwerp maken

Een Event Grid-onderwerp biedt een door de gebruiker gedefinieerd eindpunt waarop u de gebeurtenissen kunt posten. 

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Selecteer **Alle services** in het navigatiemenu links, zoek naar **Event Grid** en selecteer **Event Grid-onderwerpen**. 

    ![Event Grid-onderwerpen selecteren](./media/custom-event-to-function/select-event-grid-topics.png)
3. Selecteer op de pagina **Event Grid-onderwerpen** de optie **+ Toevoegen** op de werkbalk. 

    ![De knop Event Grid-onderwerp toevoegen](./media/custom-event-to-function/add-event-grid-topic-button.png)

4. Voer de volgende stappen uit op de pagina **Onderwerp maken**:

    1. Geef een unieke **naam** op voor het aangepaste onderwerp. De onderwerpnaam moet uniek zijn omdat deze wordt vertegenwoordigd door een DNS-vermelding. Gebruik niet de naam die in de afbeelding wordt weergegeven. Maak in plaats daarvan uw eigen naam - deze moet tussen 3 en 50 tekens lang zijn en mag alleen de waarden a-z, A-Z, 0-9 en '-' bevatten.
    2. Selecteer uw Azure-**abonnement**.
    3. Selecteer de resourcegroep uit de vorige stappen.
    4. Selecteer een **locatie** voor het Event Grid-onderwerp.
    5. Behoud de standaardwaarde **Event Grid-schema** voor het veld **Gebeurtenisschema**. 

       ![De pagina Onderwerp maken](./media/custom-event-to-function/create-custom-topic.png)
    6. Selecteer **Maken**. 

5. Nadat het aangepaste onderwerp is gemaakt, ziet u een melding dat de implementatie gelukt is. Selecteer **Naar de resourcegroep gaan**. 

   ![Zie melding implementatie gelukt](./media/custom-event-to-function/success-notification.png)

6. Selecteer op de pagina **Resourcegroep** het Event Grid-onderwerp. 

   ![De Event Grid-onderwerpresource selecteren](./media/custom-event-to-function/select-event-grid-topic.png)

7. U ziet de pagina **Event Grid-onderwerp** voor uw Event Grid. Laat deze pagina geopend. U gebruikt deze later in de quickstart. 

    ![Startpagina van Event Grid-onderwerp](./media/custom-event-to-function/event-grid-topic-home-page.png)

## <a name="subscribe-to-custom-topic"></a>Abonneren op aangepast onderwerp

U abonneert u op een Event Grid-onderwerp om Event Grid te laten weten welke gebeurtenissen u wilt traceren en waar de gebeurtenissen naartoe moeten worden gestuurd.

1. Selecteer op de pagina **Event Grid-onderwerp** voor uw aangepaste onderwerp **+ Gebeurtenisabonnement** op de werkbalk.

   ![Gebeurtenisabonnement toevoegen](./media/custom-event-to-function/new-event-subscription.png)

2. Voer op de pagina **Gebeurtenisabonnement maken** de volgende stappen uit:
    1. Voer een **naam** in voor het gebeurtenisabonnement.
    3. Selecteer **Azure-functie** voor het **Eindpunttype**. 
    4. Kies **Een eindpunt selecteren**. 

       ![Waarden opgeven voor gebeurtenisabonnement](./media/custom-event-to-function/provide-subscription-values.png)

    5. Selecteer voor het eindpunt het Azure-abonnement en de resourcegroep waarin uw functie-app zich bevindt en selecteer vervolgens de functie-app en de functie die u eerder hebt gemaakt. Selecteer **Confirm Selection** (Selectie bevestigen).

       ![Eindpunt-URL opgeven](./media/custom-event-to-function/provide-endpoint.png)
    6. Deze stap is optioneel, maar wordt aanbevolen voor productiescenario's. Ga op de pagina **Gebeurtenisabonnement maken** naar het tabblad **Geavanceerde functies** en stel waarden in voor **Maximum aantal gebeurtenissen per batch** en **Gewenste batchgrootte in kilobytes**. 
    
        Batchverwerking biedt u een hoge doorvoer. Voor **Maximum aantal gebeurtenissen per batch** stelt u het maximum aantal gebeurtenissen in dat een abonnement aan een batch toevoegt. Gewenste batchgrootte wordt de bovengrens van de batchgrootte in kilobytes, maar kan worden overschreden als een enkele gebeurtenis groter is dan deze drempelwaarde.
    
        :::image type="content" source="./media/custom-event-to-function/enable-batching.png" alt-text="Batchverwerking inschakelen":::
    6. Selecteer **Maken** op de pagina **Gebeurtenisabonnement maken**.

## <a name="send-an-event-to-your-topic"></a>Een gebeurtenis verzenden naar het onderwerp

Nu gaan we een gebeurtenis activeren om te zien hoe het bericht via Event Grid naar het eindpunt wordt gedistribueerd. Gebruik Azure CLI of PowerShell om een testgebeurtenis te verzenden naar uw aangepaste onderwerp. Meestal worden de gebeurtenisgegevens verzonden via een toepassing of Azure-service.

Het eerste voorbeeld maakt gebruik van Azure CLI. In dit voorbeeld worden de URL en de sleutel voor het aangepaste onderwerp, plus de voorbeeldgegevens van de gebeurtenis opgehaald. Gebruik de naam van het aangepaste onderwerp voor `<topic name>`. Hiermee worden voorbeeldgebeurtenisgegevens gemaakt. Het element `data` van de JSON is de nettolading van de gebeurtenis. Elke juist opgemaakte JSON kan in dit veld worden ingevoerd. U kunt het onderwerpveld ook gebruiken voor geavanceerd routeren en filteren. CURL is een hulpprogramma waarmee HTTP-aanvragen worden verzonden.


### <a name="azure-cli"></a>Azure CLI
1. Selecteer **Cloud Shell** in Azure Portal. Selecteer **Bash** in de linkerbovenhoek van het Cloud Shell-venster. 

    ![Cloud Shell - Bash](./media/custom-event-quickstart-portal/cloud-shell-bash.png)
1. Voer de volgende opdracht uit om het **eindpunt** voor het onderwerp op te halen: Nadat u de opdracht hebt gekopieerd en geplakt, werkt u de **onderwerpnaam** en **naam van de resourcegroep** bij voordat u de opdracht uitvoert. 

    ```azurecli
    endpoint=$(az eventgrid topic show --name <topic name> -g <resource group name> --query "endpoint" --output tsv)
    ```
2. Voer de volgende opdracht uit om de **sleutel** voor het aangepaste onderwerp op te halen: Nadat u de opdracht hebt gekopieerd en geplakt, werkt u de **onderwerpnaam** en **naam van de resourcegroep** bij voordat u de opdracht uitvoert. 

    ```azurecli
    key=$(az eventgrid topic key list --name <topic name> -g <resource group name> --query "key1" --output tsv)
    ```
3. Kopieer de volgende instructie met de gebeurtenisdefinitie en druk op **ENTER**. 

    ```json
    event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
    ```
4. Voer de volgende **curl**-opdracht uit om de gebeurtenis te plaatsen:

    ```
    curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
    ```

### <a name="azure-powershell"></a>Azure PowerShell
In het tweede voorbeeld wordt PowerShell gebruikt om gelijksoortige stappen uit te voeren.

1. Selecteer **Cloud Shell** in Azure Portal (of ga naar `https://shell.azure.com/`). Selecteer **PowerShell** in de linkerbovenhoek van het Cloud Shell-venster. Zie de voorbeeldafbeelding van een **Cloud Shell**-venster in de sectie Azure CLI.
2. Stel de volgende variabelen in. Nadat u elke opdracht hebt gekopieerd en geplakt, werkt u de **onderwerpnaam** en **naam van de resourcegroep** bij voordat u de opdracht uitvoert:

    ```powershell
    $resourceGroupName = <resource group name>
    $topicName = <topic name>
    ```
3. Voer de volgende opdrachten uit om het **eindpunt** en de **sleutels** voor het onderwerp op te halen:

    ```powershell
    $endpoint = (Get-AzEventGridTopic -ResourceGroupName $resourceGroupName -Name $topicName).Endpoint
    $keys = Get-AzEventGridTopicKey -ResourceGroupName $resourceGroupName -Name $topicName
    ```
4. Bereid de gebeurtenis voor. Kopieer de instructies en voer deze uit in het Cloud Shell-venster. 

    ```powershell
    $eventID = Get-Random 99999

    #Date format should be SortableDateTimePattern (ISO 8601)
    $eventDate = Get-Date -Format s

    #Construct body using Hashtable
    $htbody = @{
        id= $eventID
        eventType="recordInserted"
        subject="myapp/vehicles/motorcycles"
        eventTime= $eventDate   
        data= @{
            make="Ducati"
            model="Monster"
        }
        dataVersion="1.0"
    }
    
    #Use ConvertTo-Json to convert event body from Hashtable to JSON Object
    #Append square brackets to the converted JSON payload since they are expected in the event's JSON payload syntax
    $body = "["+(ConvertTo-Json $htbody)+"]"
    ```
5. Gebruik de cmdlet **Invoke-WebRequest** om de gebeurtenis te verzenden. 

    ```powershell
    Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
    ```

### <a name="verify-in-the-event-grid-viewer"></a>Controleren in de Event Grid-viewer
U hebt de gebeurtenis geactiveerd en Event Grid heeft het bericht verzonden naar het eindpunt dat u hebt geconfigureerd toen u zich abonneerde. Navigeer naar de door Event Grid geactiveerde functie en open de logboeken. U ziet een kopie van de gegevenspayload van de gebeurtenis in de logboeken. Als u er niet voor zorgt dat u het logboekvenster het eerst opent, of als u op Opnieuw verbinding maken klikt, verzendt u opnieuw een testgebeurtenis.

![Geslaagd functietriggerlogboek](./media/custom-event-to-function/successful-function.png)

## <a name="clean-up-resources"></a>Resources opschonen
Als u verder wilt werken met deze gebeurtenis, schoon dan de resources die u in dit artikel hebt gemaakt, niet op. Verwijder anders de resources die u in dit artikel hebt gemaakt.

1. Selecteer **Resourcegroepen** in het linkermenu. Als u deze optie niet ziet in het linkermenu, selecteert u **Alle services** in het linkermenu en selecteert u **Resourcegroepen**. 
2. Selecteer de resourcegroep om de pagina **Resourcegroep** te openen. 
3. Selecteer **Resourcegroep verwijderen** op de taakbalk. 
4. Controleer de verwijdering door de naam van de resourcegroep in te voeren en **Verwijderen** te selecteren. 

    ![Resourcegroepen](./media/custom-event-to-function/delete-resource-groups.png)

    De andere resourcegroep die u in de afbeelding ziet, is gemaakt en gebruikt door het Cloud Shell-venster. Verwijder deze als u het Cloud Shell-venster later niet meer gaat gebruiken. 

## <a name="next-steps"></a>Volgende stappen

U weet nu hoe u onderwerpen maakt en hoe u zich abonneert op een gebeurtenis. Kijk waar Event Grid u nog meer bij kan helpen:

- [Over Event Grid](overview.md)
- [Blob Storage-gebeurtenissen naar een aangepast eindpunt op het web routeren](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Monitor virtual machine changes with Azure Event Grid and Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) (Wijzigingen in virtuele machines bewaken met Azure Event Grid en Logic Apps)
- [Big data streamen naar een datawarehouse](event-grid-event-hubs-integration.md)
