---
title: Integreren met Azure SignalR Service
titleSuffix: Azure Digital Twins
description: Zie telemetrie van Azure Digital Apparaatdubbels streamen naar clients met behulp van Azure-Signa lering
author: dejimarquis
ms.author: aymarqui
ms.date: 02/12/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 89bd77c30ec52a72087598b86f22e85659fa1b0e
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102203892"
---
# <a name="integrate-azure-digital-twins-with-azure-signalr-service"></a>Azure Digital Apparaatdubbels integreren met de Azure signalerings service

In dit artikel leert u hoe u Azure Digital Apparaatdubbels integreert met de [Azure signalerings service](../azure-signalr/signalr-overview.md).

Met de oplossing die in dit artikel wordt beschreven, kunt u digitale dubbele telemetriegegevens naar verbonden clients pushen, zoals een enkele webpagina of een mobiele toepassing. Als gevolg hiervan worden clients bijgewerkt met realtime metrische gegevens en status van IoT-apparaten, zonder de nood zaak om de server te pollen of nieuwe HTTP-aanvragen voor updates te verzenden.

## <a name="prerequisites"></a>Vereisten

Hier volgen de vereisten die u moet volt ooien voordat u doorgaat:

* Voordat u uw oplossing integreert met de Azure signalerings service in dit artikel, moet u de zelf studie over Azure Digital Apparaatdubbels [_**: verbinding maken met een end-to-end-oplossing**_](tutorial-end-to-end.md), omdat dit artikel op basis van de hand leiding wordt gebouwd. In deze zelf studie wordt u begeleid bij het instellen van een Azure Digital Apparaatdubbels-exemplaar dat werkt met een virtueel IoT-apparaat om digitale dubbele updates te activeren. In dit artikel wordt beschreven hoe u deze updates verbindt met een voor beeld-web-app met behulp van de Azure signalerings service.

* U hebt de volgende waarden nodig in de zelf studie:
  - Event grid-onderwerp
  - Resourcegroep
  - Naam app service/function-app
    
* U moet [**Node.js**](https://nodejs.org/) op uw computer zijn geïnstalleerd.

U moet zich ook verder aanmelden bij de [Azure Portal](https://portal.azure.com/) met uw Azure-account.

## <a name="solution-architecture"></a>Architectuur voor de oplossing

U voegt de Azure signalerings service aan Azure Digital Apparaatdubbels toe via het onderstaande pad. De secties A, B en C in het diagram worden opgehaald uit het architectuur diagram van de [end-to-end zelf studie](tutorial-end-to-end.md). In dit procedure-artikel maakt u sectie D op basis van de bestaande architectuur.

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-integration-topology.png" alt-text="Een weer gave van Azure-Services in een end-to-end-scenario. Toont gegevens die vanuit een apparaat naar IoT Hub worden gestroomd, via een Azure-functie (pijl B) naar een Azure Digital Apparaatdubbels-exemplaar (sectie A) en vervolgens via Event Grid naar een andere Azure-functie voor verwerking (pijl C). Sectie D toont gegevens stromen van hetzelfde Event Grid in pijl C naar een Azure-functie met het label ' Broadcast '. broadcast communiceert met een andere Azure-functie met het label Negotiate en zowel broadcast als Negotiate communiceren met computer apparaten." lightbox="media/how-to-integrate-azure-signalr/signalr-integration-topology.png":::

## <a name="download-the-sample-applications"></a>De voorbeeld toepassingen downloaden

Down load eerst de vereiste voor beeld-apps. U hebt het volgende nodig:
* [**Azure Digital apparaatdubbels end-to-end-voor beelden**](/samples/azure-samples/digital-twins-samples/digital-twins-samples/): dit voor beeld bevat een *AdtSampleApp* met twee Azure-functies voor het verplaatsen van gegevens van een Azure Digital apparaatdubbels-exemplaar (u vindt hier meer informatie over dit scenario [*: Verbind een end-to-end oplossing*](tutorial-end-to-end.md)). Het bevat ook een *DeviceSimulator* -voorbeeld toepassing voor het simuleren van een IOT-apparaat, waarbij elke seconde een nieuwe temperatuur waarde wordt gegenereerd.
    - Als u het voor beeld nog niet hebt gedownload als onderdeel van de zelf studie in [*vereisten*](#prerequisites), navigeert u naar de voorbeeld [koppeling](/samples/azure-samples/digital-twins-samples/digital-twins-samples/) en selecteert u de knop *door de code bladeren* onder de titel. Hiermee gaat u naar de GitHub-opslag plaats voor de voor beelden, die u als een kunt downloaden *. ZIP* door de *code* knop te selecteren en de *zip te downloaden*.

        :::image type="content" source="media/includes/download-repo-zip.png" alt-text="Weer gave van de opslag plaats van Digital-apparaatdubbels-samples op GitHub. De knop code is geselecteerd en er wordt een klein dialoog venster geproduceerd waarin de knop voor het downloaden van een ZIP is gemarkeerd." lightbox="media/includes/download-repo-zip.png":::

    Hiermee wordt een kopie van de voor beeld-opslag plaats gedownload naar uw computer, zoals **digital-twins-samples-master.zip**. Pak de map uit.
* Voor beeld van een web-app voor de [**Signa lering-integratie**](/samples/azure-samples/digitaltwins-signalr-webapp-sample/digital-twins-samples/): dit is een voor beeld van een reactie op een web-app die gebruikmaakt van Azure Digital apparaatdubbels-telemetriegegevens van een Azure signalerings service.
    -  Ga naar de voorbeeld koppeling en gebruik hetzelfde download proces om een kopie van het voor beeld te downloaden naar uw computer, zoals _**digitaltwins-signalr-webapp-sample-main.zip**_. Pak de map uit.

[!INCLUDE [Create instance](../azure-signalr/includes/signalr-quickstart-create-instance.md)]

Sluit het browser venster geopend voor de Azure Portal, omdat u het opnieuw gebruikt in de volgende sectie.

## <a name="publish-and-configure-the-azure-functions-app"></a>De Azure Functions-app publiceren en configureren

In deze sectie gaat u twee Azure-functies instellen:
* **onderhandelen** -een http-activerings functie. Er wordt gebruikgemaakt van de *SignalRConnectionInfo* -invoer binding voor het genereren en retour neren van geldige verbindings gegevens.
* **broadcast** : een [Event grid](../event-grid/overview.md) -activerings functie. Het ontvangt een Azure Digital Apparaatdubbels-telemetrie-gegevens via het event grid en gebruikt de uitvoer binding van het *seingevings* exemplaar dat u in de vorige stap hebt gemaakt om het bericht naar alle verbonden client toepassingen te verzenden.

Start Visual Studio (of een andere code-editor van uw keuze) en open de code oplossing in de map *Digital-apparaatdubbels-samples-master > ADTSampleApp* . Voer vervolgens de volgende stappen uit om de functies te maken:

1. Maak in het project *SampleFunctionsApp* een nieuwe C#-klasse met de naam **SignalRFunctions.cs**.

1. Vervang de inhoud van het klassen bestand door de volgende code:
    
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/signalRFunction.cs":::

1. Navigeer in het *console venster package manager* van Visual Studio, of een opdracht venster op uw computer, naar de map *Digital-Twins-samples-master\AdtSampleApp\SampleFunctionsApp* en voer de volgende opdracht uit om het `SignalRService` NuGet-pakket te installeren op het project:
    ```cmd
    dotnet add package Microsoft.Azure.WebJobs.Extensions.SignalRService --version 1.2.0
    ```

    Hiermee worden eventuele afhankelijkheids problemen in de klasse opgelost.

1. Publiceer uw functie in azure met behulp van de stappen die worden beschreven in het [gedeelte *de app publiceren*](tutorial-end-to-end.md#publish-the-app) van de zelf studie *een end-to-end oplossing verbinden* . U kunt het publiceren naar dezelfde app service/function-app die u hebt gebruikt in de end-to-end [-zelf studie](#prerequisites), of een nieuwe maken, maar u kunt ook dezelfde versie gebruiken om de duplicatie te minimaliseren. 

Configureer vervolgens de functies om te communiceren met uw Azure signalerings instantie. U begint met het verzamelen van de **Connection String** van de signaale-instantie en deze vervolgens toe te voegen aan de instellingen van de app met functies.

1. Ga naar de [Azure Portal](https://portal.azure.com/) en zoek de naam van uw seingevings instantie in de zoek balk boven aan de portal. Selecteer het exemplaar om het te openen.
1. Selecteer **sleutels** in het menu van de instantie om de verbindings reeksen voor het exemplaar van de seingevings service weer te geven.
1. Selecteer het *Kopieer* pictogram om de primaire Connection String te kopiëren.

    :::image type="content" source="media/how-to-integrate-azure-signalr/signalr-keys.png" alt-text="Scherm afbeelding van de Azure Portal die de pagina sleutels voor de seingevings instantie weergeeft. Het pictogram kopiëren naar klem bord naast de primaire VERBINDINGS reeks is gemarkeerd." lightbox="media/how-to-integrate-azure-signalr/signalr-keys.png":::

1. Voeg ten slotte uw Azure-seingevings **Connection String** toe aan de app-instellingen van de functie, met behulp van de volgende Azure cli-opdracht. Vervang ook de tijdelijke aanduidingen door de naam van uw resource groep en app service/functie-app uit de [vereiste zelf studie](how-to-integrate-azure-signalr.md#prerequisites). De opdracht kan worden uitgevoerd in [Azure Cloud shell](https://shell.azure.com)of lokaal als u de Azure cli [op uw computer hebt geïnstalleerd](/cli/azure/install-azure-cli):
 
    ```azurecli-interactive
    az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "AzureSignalRConnectionString=<your-Azure-SignalR-ConnectionString>"
    ```

    De uitvoer van deze opdracht drukt alle app-instellingen af die zijn ingesteld voor uw Azure-functie. Ga naar `AzureSignalRConnectionString` de onderkant van de lijst om te controleren of deze is toegevoegd.

    :::image type="content" source="media/how-to-integrate-azure-signalr/output-app-setting.png" alt-text="Fragment van uitvoer in een opdracht venster waarin een lijst item wordt weer gegeven met de naam ' AzureSignalRConnectionString '":::

#### <a name="connect-the-function-to-event-grid"></a>De functie verbinden met Event Grid

Abonneer u vervolgens op de functie *broadcast* Azure in het **onderwerp Event grid** dat u tijdens de [vereiste zelf studie](how-to-integrate-azure-signalr.md#prerequisites)hebt gemaakt. Dit zorgt ervoor dat telemetriegegevens van de thermostat67 tussen het gebeurtenis grid-onderwerp en de functie kunnen stromen. Hier kan de functie de gegevens naar alle clients verzenden.

Hiervoor maakt u een **gebeurtenis abonnement** vanuit uw event grid-onderwerp naar uw *broadcast* Azure-functie als een eind punt.

Ga in de [Azure-portal](https://portal.azure.com/)naar uw gebeurtenisrasteronderwerp door de naam ervan te zoeken in de bovenste zoekbalk. Selecteer *+ Gebeurtenisabonnement*.

:::image type="content" source="media/how-to-integrate-azure-signalr/event-subscription-1b.png" alt-text="Azure Portal: Event Grid-abonnement":::

Vul de velden op de pagina *Gebeurtenisabonnement maken* als volgt in (velden die automatisch worden ingevuld, worden niet vermeld):
* *GEBEURTENISABONNEMENTDETAILS* > **Naam**: Geef een naam op voor uw gebeurtenisabonnement.
* *DETAILS VAN EINDPUNT* > **Type eindpunt**: Selecteer *Azure-functie* in de menuopties.
* *DETAILS VAN EINDPUNT* > **Eindpunt**: Klik op de koppeling *Selecteer een eindpunt*. Hierdoor wordt het venster *Azure-functie selecteren* geopend:
    - Vul uw **abonnement**, de **resource groep**, de **functie-app** en de **functie** (*broadcast*) in. Sommige van deze waarden worden mogelijk automatisch ingevuld nadat u het abonnement hebt geselecteerd.
    - Klik op **Selectie bevestigen**.

:::image type="content" source="media/how-to-integrate-azure-signalr/create-event-subscription.png" alt-text="Azure Portal weer gave van het maken van een gebeurtenis abonnement. De velden hierboven zijn ingevuld en de knoppen selectie bevestigen en maken zijn gemarkeerd.":::

Klik op **Maken** op de pagina *Gebeurtenisabonnement maken*.

Op dit moment ziet u twee gebeurtenis abonnementen op de pagina met *Event grid onderwerp* .

:::image type="content" source="media/how-to-integrate-azure-signalr/view-event-subscriptions.png" alt-text="Azure Portal weer gave van twee gebeurtenis abonnementen op de pagina Event grid-onderwerp." lightbox="media/how-to-integrate-azure-signalr/view-event-subscriptions.png":::

## <a name="configure-and-run-the-web-app"></a>De web-app configureren en uitvoeren

In deze sectie ziet u het resultaat in actie. Configureer eerst de voor **beeld-client-web-app** om verbinding te maken met de Azure seingevings stroom die u hebt ingesteld. Vervolgens start u de voor beeld- **app voor gesimuleerde apparaten** die telemetriegegevens verzendt via uw Azure Digital apparaatdubbels-exemplaar. Daarna bekijkt u de voor beeld-web-app om de gesimuleerde apparaatgegevens te zien die de voor beeld-web-app in realtime bijwerken.

### <a name="configure-the-sample-client-web-app"></a>De voor beeld-client-web-app configureren

Vervolgens configureert u de voor beeld-Web-App van de client. Begin met het verzamelen van de **http-eind punt-URL** van de functie *Negotiate* en gebruik deze om de app-code op uw computer te configureren.

1. Ga naar de pagina [functie-apps](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) van Azure Portal en selecteer de functie-app in de lijst. Selecteer in het menu app *functies* en kies de functie *onderhandelen* .

    :::image type="content" source="media/how-to-integrate-azure-signalr/functions-negotiate.png" alt-text="Azure Portal weer gave van de functie-app, waarbij ' functies ' in het menu is gemarkeerd. De lijst met functies wordt op de pagina weer gegeven en de functie Negotiate is ook gemarkeerd.":::

1. Klik op *URL ophalen* en kopieer de waarde naar **boven op _/API_ (Neem de laatste _/Negotiate_ niet op?)**. U gebruikt deze in de volgende stap.

    :::image type="content" source="media/how-to-integrate-azure-signalr/get-function-url.png" alt-text="Azure Portal weer gave van de functie Negotiate. De knop functie-URL ophalen is gemarkeerd en het gedeelte van de URL vanaf het begin tot en met '/API '":::

1. Open Visual Studio of een wille keurige code-editor van uw keuze door de map ungezipted _**digitaltwins-signalering-webapp-sample-main**_ te openen die u hebt gedownload in het gedeelte [*de voorbeeld toepassingen downloaden*](#download-the-sample-applications) .

1. Open het bestand *src/App.js* en vervang de functie-URL in `HubConnectionBuilder` door de http-eind punt-URL van de **onderhandelings** functie die u in de vorige stap hebt opgeslagen:

    ```javascript
        const hubConnection = new HubConnectionBuilder()
            .withUrl('<Function URL>')
            .build();
    ```
1. Navigeer in de *ontwikkelaars opdracht prompt* van Visual Studio of een opdracht venster op de computer naar de map *digitaltwins-signalr-webapp-sample-main\src* . Voer de volgende opdracht uit om de afhankelijke knooppunt pakketten te installeren:

    ```cmd
    npm install
    ```

Stel vervolgens in het Azure Portal machtigingen in uw functie-app in:
1. Selecteer op de pagina [functie-apps](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) van de Azure Portal het exemplaar van de functie-app.

1. Schuif omlaag in het menu exemplaar en selecteer *CORS*. Voeg op de CORS-pagina `http://localhost:3000` als een toegestane oorsprong toe door deze in het lege vak in te voeren. Schakel het selectie vakje *Access-Control-Allow-credentials* in en klik op *Opslaan*.

    :::image type="content" source="media/how-to-integrate-azure-signalr/cors-setting-azure-function.png" alt-text="CORS-instelling in azure function":::

### <a name="run-the-device-simulator"></a>De Device Simulator uitvoeren

Tijdens de end-to-end-zelf studie hebt u [de Device Simulator geconfigureerd voor het](tutorial-end-to-end.md#configure-and-run-the-simulation) verzenden van gegevens via een IOT hub en naar uw Azure Digital apparaatdubbels-exemplaar.

Nu hoeft u alleen maar het simulator-project te starten, dat zich bevindt in *Digital-apparaatdubbels-samples-master > DeviceSimulator > DeviceSimulator. SLN*. Als u Visual Studio gebruikt, kunt u het project openen en dit vervolgens uitvoeren met deze knop op de werk balk:

:::image type="content" source="media/how-to-integrate-azure-signalr/start-button-simulator.png" alt-text="De startknop van Visual Studio (project DeviceSimulator)":::

Er wordt een consolevenster geopend waar gesimuleerde telemetrieberichten met temperaturen worden weergegeven. Deze worden via uw Azure Digital Apparaatdubbels-exemplaar verzonden, waar ze vervolgens worden opgehaald door de Azure-functies en-Signa lering.

U hoeft niets anders in deze console te doen, maar blijft actief terwijl u de volgende stap voltooit.

### <a name="see-the-results"></a>De resultaten weergeven

Als u de resultaten in actie wilt zien, start u de voor beeld-web-app voor de **Signa lering-integratie**. U kunt dit doen vanuit elk console venster op de locatie van de *digitaltwins-signalr-webapp-sample-main\src* door deze opdracht uit te voeren:

```cmd
npm start
```

Hiermee opent u een browser venster waarin de voor beeld-app wordt uitgevoerd. Hiermee wordt een visuele temperatuur meter weer gegeven. Zodra de app is uitgevoerd, moet u de waarden voor de Tempe ratuur van de telemetrie in de Device Simulator bekijken die door Azure Digital Apparaatdubbels worden door gegeven aan de hand van de web-app in realtime.

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-webapp-output.png" alt-text="Fragment van de voor beeld-client-web-app, met een visuele temperatuur meter. De weer gegeven temperatuur is 67,52":::

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources die in dit artikel zijn gemaakt niet meer nodig hebt, volgt u deze stappen om ze te verwijderen. 

Met de Azure Cloud Shell of lokale Azure CLI kunt u alle Azure-resources in een resource groep verwijderen met de opdracht [AZ Group delete](/cli/azure/group#az-group-delete) . Als u de resource groep verwijdert, wordt ook verwijderd...
* het Azure Digital Apparaatdubbels-exemplaar (van de end-to-end-zelf studie)
* de IoT hub en de registratie van het hub-apparaat (van de end-to-end-zelf studie)
* het event grid-onderwerp en de bijbehorende abonnementen
* de Azure Functions-app, inclusief alle drie de functies en gekoppelde resources zoals opslag
* het exemplaar van Azure signalering

> [!IMPORTANT]
> Het verwijderen van een resourcegroep kan niet ongedaan worden gemaakt. De resourcegroep en alle resources daarin worden permanent verwijderd. Zorg ervoor dat u niet per ongeluk de verkeerde resourcegroep of resources verwijdert. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

Verwijder tot slot de voorbeeld mappen van het project die u hebt gedownload naar uw lokale machine (*digital-twins-samples-master.zip*, *digitaltwins-signalr-webapp-sample-main.zip* en de ongecomprimeerde tegen Hangers).

## <a name="next-steps"></a>Volgende stappen

In dit artikel stelt u Azure functions in met de Signa lering voor het uitzenden van Azure Digital Apparaatdubbels-telemetrie-gebeurtenissen naar een voor beeld-client toepassing.

Meer informatie over de Azure signalerings service:
* [*Wat is Azure SignalR Service?*](../azure-signalr/signalr-overview.md)

Of lees meer over de verificatie van de Azure signalerings service met Azure Functions:
* [*Verificatie van Azure SignalR-service*](../azure-signalr/signalr-tutorial-authenticate-azure-functions.md)