---
title: 'Quickstart: Aangepaste gebeurtenissen verzenden naar het webeindpunt - Event Grid, Azure Portal'
description: 'Quickstart: Gebruik Azure Event Grid en Azure Portal om een aangepast onderwerp te publiceren en u op gebeurtenissen voor dat onderwerp te abonneren. De gebeurtenissen worden verwerkt door een web-app.'
ms.date: 07/07/2020
ms.topic: quickstart
ms.openlocfilehash: 592e2d6b7393da8cb55a457b022d6c2358048cfe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96013663"
---
# <a name="quickstart-route-custom-events-to-web-endpoint-with-the-azure-portal-and-event-grid"></a>Quickstart: Aangepaste gebeurtenissen routeren naar het webeindpunt met behulp van Azure Portal en Event Grid

Azure Event Grid is een gebeurtenisservice voor de cloud. In dit artikel gebruikt u Azure Portal om een aangepast onderwerp te maken, u op het aangepaste onderwerp te abonneren, en de gebeurtenis te activeren om het resultaat weer te geven. Normaal gesproken verzendt u gebeurtenissen naar een eindpunt dat de gebeurtenisgegevens verwerkt en vervolgens in actie komt. Ter vereenvoudiging van dit artikel stuurt u hier de gebeurtenissen echter naar een web-app die de berichten verzamelt en weergeeft.

## <a name="prerequisites"></a>Vereisten
[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="create-a-custom-topic"></a>Een aangepast onderwerp maken

Een Event Grid-onderwerp biedt een door de gebruiker gedefinieerd eindpunt waarop u de gebeurtenissen kunt posten. 

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Typ in de zoekbalk van het onderwerp **Event Grid-onderwerpen** en selecteer vervolgens **Event Grid-onderwerpen** in de vervolgkeuzelijst. 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-event-grid-topics.png" alt-text="Event Grid-onderwerpen zoeken en selecteren":::
3. Selecteer op de pagina **Event Grid-onderwerpen** de optie **+ Toevoegen** op de werkbalk. 

    :::image type="content" source="./media/custom-event-quickstart-portal/add-event-grid-topic-button.png" alt-text="De knop Event Grid-onderwerp toevoegen":::
4. Voer de volgende stappen uit op de pagina **Onderwerp maken**:
    1. Selecteer uw Azure-**abonnement**.
    2. Selecteer een bestaande resourcegroep of selecteer **Nieuwe maken** en geef een **naam** op voor de **resourcegroep**.
    3. Geef een unieke **naam** op voor het aangepaste onderwerp. De onderwerpnaam moet uniek zijn omdat deze wordt vertegenwoordigd door een DNS-vermelding. Gebruik niet de naam die in de afbeelding wordt weergegeven. Maak in plaats daarvan uw eigen naam - deze moet tussen 3 en 50 tekens lang zijn en mag alleen de waarden a-z, A-Z, 0-9 en '-' bevatten.
    4. Selecteer een **locatie** voor het Event Grid-onderwerp.
    5. Selecteer **Controleren en maken** onderaan de pagina. 

        :::image type="content" source="./media/custom-event-quickstart-portal/create-custom-topic.png" alt-text="De pagina Onderwerp maken":::
    6. Selecteer de optie **Maken** op het tabblad **Beoordelen en maken** van de pagina **Onderwerp maken**. 
    
        :::image type="content" source="./media/custom-event-quickstart-portal/review-create-page.png" alt-text="Instellingen controleren en maken":::
5. Typ na het voltooien van de implementatie **Event Grid-onderwerpen** in de zoekbalk en selecteer **Event Grid-onderwerpen** in de vervolgkeuzelijst zoals u dat eerder hebt gedaan. 
6. Selecteer in de lijst het onderwerp dat u hebt gemaakt. 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-event-grid-topic.png" alt-text="Selecteer uw onderwerp in de lijst":::

7. U ziet de pagina **Event Grid-onderwerp** voor uw onderwerp. Laat deze pagina geopend. U gebruikt deze later in de quickstart. 

    :::image type="content" source="./media/custom-event-quickstart-portal/event-grid-topic-home-page.png" alt-text="Startpagina van Event Grid-onderwerp":::

## <a name="create-a-message-endpoint"></a>Het eindpunt van een bericht maken
Voordat u een abonnement voor het aangepaste onderwerp maakt, moet u een eindpunt voor het gebeurtenisbericht maken. Het eindpunt onderneemt normaal gesproken actie op basis van de gebeurtenisgegevens. Ter vereenvoudiging van deze snelstart gaat u een [vooraf gebouwde web-app](https://github.com/Azure-Samples/azure-event-grid-viewer) implementeren waarmee de gebeurtenisberichten worden weergegeven. De geïmplementeerde oplossing omvat een App Service-plan, een App Service-web-app en broncode van GitHub.

1. Selecteer **Implementeren in Azure** op de artikelpagina om de oplossing voor uw abonnement te implementeren. Geef in Azure Portal waarden op voor de parameters.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"  alt="Button to Deploy to Aquent." /></a>
1. De implementatie kan enkele minuten duren. Controleer of uw web-app wordt uitgevoerd nadat de implementatie is voltooid. Navigeer in een webbrowser naar: `https://<your-site-name>.azurewebsites.net`

    Controleer het foutbericht als de implementatie mislukt. Mogelijk wordt dit veroorzaakt doordat de naam van de website al wordt gebruikt. Implementeer de sjabloon opnieuw en kies een andere naam voor de site. 
1. De site wordt weergegeven, maar er zijn nog geen gebeurtenissen op gepubliceerd.

   ![Nieuwe site weergeven](./media/custom-event-quickstart-portal/view-site.png)

## <a name="subscribe-to-custom-topic"></a>Abonneren op aangepast onderwerp

U abonneert u op een Event Grid-onderwerp om Event Grid te laten weten welke gebeurtenissen u wilt traceren en waar de gebeurtenissen naartoe moeten worden gestuurd.

1. Selecteer op de pagina **Event Grid-onderwerp** voor uw aangepaste onderwerp **+ Gebeurtenisabonnement** op de werkbalk.

    :::image type="content" source="./media/custom-event-quickstart-portal/new-event-subscription.png" alt-text="Knop voor het toevoegen van een gebeurtenisabonnement":::
2. Voer op de pagina **Gebeurtenisabonnement maken** de volgende stappen uit:
    1. Voer een **naam** in voor het gebeurtenisabonnement.
    3. Selecteer **Webhook** voor het **eindpunttype**. 
    4. Kies **Een eindpunt selecteren**. 

        :::image type="content" source="./media/custom-event-quickstart-portal/provide-subscription-values.png" alt-text="Waarden opgeven voor gebeurtenisabonnement":::
    5. Voor het webhookeindpunt geeft u de URL van uw web-app op en voegt u `api/updates` toe aan de URL van de startpagina. Selecteer **Confirm Selection** (Selectie bevestigen).

        :::image type="content" source="./media/custom-event-quickstart-portal/provide-endpoint.png" alt-text="Eindpunt-URL opgeven":::
    6. Selecteer **Maken** als u terug bent op de pagina **Gebeurtenisabonnement maken**.

3. Bekijk opnieuw uw web-app en u zult zien dat er een validatiegebeurtenis voor een abonnement naartoe is verzonden. Selecteer het oogpictogram om de gebeurtenisgegevens uit te breiden. Via Event Grid wordt de validatiegebeurtenis verzonden zodat het eindpunt kan controleren of de gebeurtenisgegevens in aanmerking komen om ontvangen te worden. De web-app bevat code waarmee het abonnement kan worden gevalideerd.

    ![Een abonnementgebeurtenis weergeven](./media/custom-event-quickstart-portal/view-subscription-event.png)

## <a name="send-an-event-to-your-topic"></a>Een gebeurtenis verzenden naar het onderwerp

Nu gaan we een gebeurtenis activeren om te zien hoe het bericht via Event Grid naar het eindpunt wordt gedistribueerd. Gebruik Azure CLI of PowerShell om een testgebeurtenis te verzenden naar uw aangepaste onderwerp. Meestal worden de gebeurtenisgegevens verzonden via een toepassing of Azure-service.

Het eerste voorbeeld maakt gebruik van Azure CLI. In dit voorbeeld worden de URL en de sleutel voor het aangepaste onderwerp, plus de voorbeeldgegevens van de gebeurtenis opgehaald. Gebruik de naam van het aangepaste onderwerp voor `<topic name>`. Hiermee worden voorbeeldgebeurtenisgegevens gemaakt. Het element `data` van de JSON is de nettolading van de gebeurtenis. Elke juist opgemaakte JSON kan in dit veld worden ingevoerd. U kunt het onderwerpveld ook gebruiken voor geavanceerd routeren en filteren. CURL is een hulpprogramma waarmee HTTP-aanvragen worden verzonden.


### <a name="azure-cli"></a>Azure CLI
1. Selecteer **Cloud Shell** in Azure Portal. De Cloud Shell wordt geopend in het onderste deelvenster van de webbrowser. 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-cloud-shell.png" alt-text="Het pictogram voor Cloud Shell selecteren":::
1. Selecteer **Bash** in de linkerbovenhoek van het Cloud Shell-venster. 

    ![Cloud Shell - Bash](./media/custom-event-quickstart-portal/cloud-shell-bash.png)
1. Voer de volgende opdracht uit om het **eindpunt** voor het onderwerp op te halen: Nadat u de opdracht hebt gekopieerd en geplakt, werkt u de **onderwerpnaam** en **naam van de resourcegroep** bij voordat u de opdracht uitvoert. U gaat voorbeeldgebeurtenissen publiceren naar dit eindpunt voor het onderwerp. 

    ```azurecli
    endpoint=$(az eventgrid topic show --name <topic name> -g <resource group name> --query "endpoint" --output tsv)
    ```
2. Voer de volgende opdracht uit om de **sleutel** voor het aangepaste onderwerp op te halen: Nadat u de opdracht hebt gekopieerd en geplakt, werkt u de **onderwerpnaam** en **naam van de resourcegroep** bij voordat u de opdracht uitvoert. Dit is de primaire sleutel van het Event Grid-onderwerp. Ga naar het tabblad **Toegangssleutels** van de pagina **Event Grid-onderwerp** om deze sleutel op te halen in Azure Portal. U hebt de toegangssleutel nodig als u een gebeurtenis voor een aangepast onderwerp wilt plaatsen. 

    ```azurecli
    key=$(az eventgrid topic key list --name <topic name> -g <resource group name> --query "key1" --output tsv)
    ```
3. Kopieer de volgende instructie met de gebeurtenisdefinitie en druk op **ENTER**. 

    ```json
    event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
    ```
4. Voer de volgende **curl**-opdracht uit om de gebeurtenis te plaatsen: In de opdracht is de `aeg-sas-key`-header ingesteld op de toegangssleutel die u eerder hebt gekregen. 

    ```
    curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
    ```

### <a name="azure-powershell"></a>Azure PowerShell
In het tweede voorbeeld wordt PowerShell gebruikt om gelijksoortige stappen uit te voeren.

1. Selecteer **Cloud Shell** in Azure Portal (of ga naar `https://shell.azure.com/`). De Cloud Shell wordt geopend in het onderste deelvenster van de webbrowser. 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-cloud-shell.png" alt-text="Het pictogram voor Cloud Shell selecteren":::
1. Selecteer in de **Cloud Shell** de optie **PowerShell** in de linkerbovenhoek van het Cloud Shell-venster. Zie de voorbeeldafbeelding van een **Cloud Shell**-venster in de sectie Azure CLI.
2. Stel de volgende variabelen in. Nadat u elke opdracht hebt gekopieerd en geplakt, werkt u de **onderwerpnaam** en **naam van de resourcegroep** bij voordat u de opdracht uitvoert:

    **Resourcegroep**:
    ```powershell
    $resourceGroupName = "<resource group name>"
    ```

    **Naam van Event Grid-onderwerp**:    
    ```powershell
    $topicName = "<topic name>"
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
U hebt de gebeurtenis geactiveerd en Event Grid heeft het bericht verzonden naar het eindpunt dat u hebt geconfigureerd toen u zich abonneerde. Bekijk uw web-app om de gebeurtenis te zien die u zojuist hebt verzonden.

:::image type="content" source="./media/custom-event-quickstart-portal/event-grid-viewer-end.png" alt-text="Event Grid-viewer":::

## <a name="clean-up-resources"></a>Resources opschonen
Als u verder wilt werken met deze gebeurtenis, schoon dan de resources die u in dit artikel hebt gemaakt, niet op. Verwijder anders de resources die u in dit artikel hebt gemaakt.

1. Selecteer **Resourcegroepen** in het linkermenu. Als u deze optie niet ziet in het linkermenu, selecteert u **Alle services** in het linkermenu en selecteert u **Resourcegroepen**. 

    ![Resourcegroepen](./media/custom-event-quickstart-portal/delete-resource-groups.png)
1. Selecteer de resourcegroep om de pagina **Resourcegroep** te openen. 
1. Selecteer **Resourcegroep verwijderen** op de taakbalk. 
1. Controleer de verwijdering door de naam van de resourcegroep in te voeren en **Verwijderen** te selecteren. 

    De andere resourcegroep die u in de afbeelding ziet, is gemaakt en gebruikt door het Cloud Shell-venster. Verwijder deze als u het Cloud Shell-venster later niet meer gaat gebruiken. 

## <a name="next-steps"></a>Volgende stappen

U weet nu hoe u aangepaste onderwerpen maakt en hoe u zich abonneert op een gebeurtenis. Kijk waar Event Grid u nog meer bij kan helpen:

- [Over Event Grid](overview.md)
- [Blob Storage-gebeurtenissen naar een aangepast eindpunt op het web routeren](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Monitor virtual machine changes with Azure Event Grid and Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) (Wijzigingen in virtuele machines bewaken met Azure Event Grid en Logic Apps)
- [Big data streamen naar een datawarehouse](event-grid-event-hubs-integration.md)
