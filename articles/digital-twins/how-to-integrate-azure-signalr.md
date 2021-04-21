---
title: Integreren met Azure SignalR Service
titleSuffix: Azure Digital Twins
description: Bekijk hoe u Azure Digital Twins streamt naar clients met behulp van Azure SignalR
author: dejimarquis
ms.author: aymarqui
ms.date: 02/12/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: e8bdb843ab6304f2f38228f37d8709e4084ee52e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775327"
---
# <a name="integrate-azure-digital-twins-with-azure-signalr-service"></a>Integratie Azure Digital Twins met Azure SignalR Service

In dit artikel leert u hoe u uw Azure Digital Twins integreert [met Azure SignalR Service.](../azure-signalr/signalr-overview.md)

Met de oplossing die in dit artikel wordt beschreven, kunt u telemetriegegevens van digitale tweelingen pushen naar verbonden clients, zoals één webpagina of een mobiele toepassing. Als gevolg hiervan worden clients bijgewerkt met realtime metrische gegevens en status van IoT-apparaten, zonder dat ze de server hoeven te peilen of nieuwe HTTP-aanvragen hoeven in te dienen voor updates.

## <a name="prerequisites"></a>Vereisten

Dit zijn de vereisten die u moet voltooien voordat u doorgaat:

* Voordat u uw oplossing integreert met Azure SignalR Service in dit artikel, moet u de Azure Digital Twins [_**Tutorial: Connect an end-to-end solution (Zelfstudie: een end-to-end-oplossing**_](tutorial-end-to-end.md)verbinden) voltooien, omdat dit instructieartikel daar bovenop voortbouwt. De zelfstudie leidt u door het instellen van een Azure Digital Twins die werkt met een virtueel IoT-apparaat om digital twin-updates te activeren. In dit artikel worden deze updates verbonden met een voorbeeld-web-app met behulp van Azure SignalR Service.

* U hebt de volgende waarden uit de zelfstudie nodig:
  - Event Grid-onderwerp
  - Resourcegroep
  - Naam van app-service/functie-app
    
* U moet deze [**Node.js**](https://nodejs.org/) op uw computer.

U moet zich ook aanmelden bij de Azure Portal [met](https://portal.azure.com/) uw Azure-account.

## <a name="solution-architecture"></a>Architectuur voor de oplossing

U koppelt een Azure SignalR Service aan Azure Digital Twins via het onderstaande pad. Secties A, B en C in het diagram zijn afkomstig uit het architectuurdiagram van de [end-to-end-zelfstudie die vereist is.](tutorial-end-to-end.md) In dit artikel bouwt u sectie D op de bestaande architectuur.

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-integration-topology.png" alt-text="Een weergave van Azure-services in een end-to-end scenario. Geeft gegevens weer die van een apparaat naar IoT Hub stromen, via een Azure-functie (pijl B) naar een Azure Digital Twins-exemplaar (sectie A), en vervolgens via Event Grid naar een andere Azure-functie voor verwerking (pijl C). Sectie D toont gegevens die vanaf dezelfde Event Grid pijl C naar een Azure-functie met het label Broadcast. 'broadcast' communiceert met een andere Azure-functie met het label 'negotiate', en zowel 'broadcast' als 'negotiate' communiceren met computerapparaten." lightbox="media/how-to-integrate-azure-signalr/signalr-integration-topology.png":::

## <a name="download-the-sample-applications"></a>De voorbeeldtoepassingen downloaden

Download eerst de vereiste voorbeeld-apps. U hebt beide van de volgende opties nodig:
* [**Azure Digital Twins end-to-end-voorbeelden:**](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)dit voorbeeld bevat een *AdtSampleApp* die twee Azure-functies bevat voor het verplaatsen van gegevens rond een Azure Digital Twins-exemplaar (meer informatie over dit scenario vindt u in [*Zelfstudie: Een end-to-end-oplossing verbinden).*](tutorial-end-to-end.md) Het bevat ook een *DeviceSimulator-voorbeeldtoepassing* die een IoT-apparaat simuleert en elke seconde een nieuwe temperatuurwaarde genereert.
    - Als u het voorbeeld nog niet hebt gedownload als onderdeel van de [](/samples/azure-samples/digital-twins-samples/digital-twins-samples/) zelfstudie [*in*](#prerequisites)Vereisten, gaat u naar de voorbeeldkoppeling en selecteert u de knop Bladeren in *code* onder de titel. Hiermee gaat u naar de GitHub-opslagplaats voor de voorbeelden, die u als een kunt *downloaden. Zip door* de knop *Code en* ZIP downloaden *te selecteren.*

        :::image type="content" source="media/includes/download-repo-zip.png" alt-text="Bekijk de opslagplaats digital-twins-samples op GitHub. De knop Code is geselecteerd. Er wordt een klein dialoogvenster weergegeven waarin de knop ZIP downloaden is gemarkeerd." lightbox="media/includes/download-repo-zip.png":::

    Hiermee downloadt u een kopie van de voorbeeld-repo naar uw computer, **zoalsdigital-twins-samples-master.zip**. Pak de map uit.
* [**Voorbeeld van signalR-integratieweb-app:**](/samples/azure-samples/digitaltwins-signalr-webapp-sample/digital-twins-samples/)dit is een React-voorbeeldweb-app die Azure Digital Twins telemetriegegevens van een Azure SignalR Service.
    -  Navigeer naar de voorbeeldkoppeling en gebruik hetzelfde downloadproces om een kopie van het voorbeeld naar uw computer te downloaden, _**zoalsdigitaltwins-signalr-webapp-sample-main.zip**_. Pak de map uit.

[!INCLUDE [Create instance](../azure-signalr/includes/signalr-quickstart-create-instance.md)]

Laat het browservenster geopend voor Azure Portal, want u gebruikt het opnieuw in de volgende sectie.

## <a name="publish-and-configure-the-azure-functions-app"></a>De app Azure Functions publiceren en configureren

In deze sectie stelt u twee Azure-functies in:
* **negotiate:** een HTTP-triggerfunctie. De invoerbinding *SignalRConnectionInfo* wordt gebruikt om geldige verbindingsgegevens te genereren en te retourneren.
* **broadcast:** een [Event Grid](../event-grid/overview.md) triggerfunctie. Het ontvangt Azure Digital Twins telemetriegegevens via het gebeurtenisraster en gebruikt de uitvoerbinding van het *SignalR-exemplaar* dat u in de vorige stap hebt gemaakt om het bericht uit te zenden naar alle verbonden clienttoepassingen.

Start Visual Studio (of een andere code-editor naar keuze) en open de codeoplossing in de map *digital-twins-samples-master > ADTSampleApp.* Ga vervolgens als volgt te werk om de functies te maken:

1. Maak in het project *SampleFunctionsApp* een nieuwe C#-klasse met de **naam SignalRFunctions.cs.**

1. Vervang de inhoud van het klassebestand door de volgende code:
    
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/signalRFunction.cs":::

1. Navigeer in het Pakketbeheer-consolevenster van *Visual Studio of* een opdrachtvenster op uw computer naar de map *digital-twins-samples-master\AdtSampleApp\SampleFunctionsApp* en voer de volgende opdracht uit om het NuGet-pakket te installeren in `SignalRService` het project:
    ```cmd
    dotnet add package Microsoft.Azure.WebJobs.Extensions.SignalRService --version 1.2.0
    ```

    Hiermee worden eventuele afhankelijkheidsproblemen in de klasse opgelost.

1. Publiceer uw functie naar Azure met behulp van de stappen die worden beschreven in de sectie De [ *app publiceren*](tutorial-end-to-end.md#publish-the-app) van de zelfstudie *Een end-to-end-oplossing verbinden.* U kunt deze publiceren naar dezelfde app-service/functie-app die u [](#prerequisites)hebt gebruikt in de end-to-end-zelfstudie die u hebt gebruikt, of een nieuwe maken, maar misschien wilt u dezelfde gebruiken om duplicatie te minimaliseren. 

Configureer vervolgens de functies om te communiceren met uw Azure SignalR-exemplaar. U begint met het verzamelen van de connection string van het SignalR-exemplaar en voegt deze vervolgens toe aan de instellingen van de functie-app.

1. Ga naar [de Azure Portal](https://portal.azure.com/) zoek naar de naam van uw SignalR-exemplaar in de zoekbalk boven aan de portal. Selecteer het exemplaar om het te openen.
1. Selecteer **Sleutels in** het exemplaarmenu om de verbindingsreeksen voor het SignalR-service-exemplaar weer te geven.
1. Selecteer het *pictogram Kopiëren* om de primaire connection string.

    :::image type="content" source="media/how-to-integrate-azure-signalr/signalr-keys.png" alt-text="Schermopname van de Azure Portal met de pagina Sleutels voor het SignalR-exemplaar. Het pictogram Kopiëren naar klembord naast de Primaire VERBINDINGSREEKS is gemarkeerd." lightbox="media/how-to-integrate-azure-signalr/signalr-keys.png":::

1. Voeg ten slotte uw Azure **SignalR-connection string** toe aan de app-instellingen van de functie met behulp van de volgende Azure CLI-opdracht. Vervang ook de tijdelijke aanduidingen door de naam van uw resourcegroep en app-service/functie-app uit de vereiste [voor de zelfstudie.](how-to-integrate-azure-signalr.md#prerequisites) De opdracht kan worden uitgevoerd in [Azure Cloud Shell](https://shell.azure.com)of lokaal als u de Azure CLI op [uw computer hebt geïnstalleerd:](/cli/azure/install-azure-cli)
 
    ```azurecli-interactive
    az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "AzureSignalRConnectionString=<your-Azure-SignalR-ConnectionString>"
    ```

    Met de uitvoer van deze opdracht worden alle app-instellingen afgedrukt die zijn ingesteld voor uw Azure-functie. Zoek naar `AzureSignalRConnectionString` onderaan de lijst om te controleren of deze is toegevoegd.

    :::image type="content" source="media/how-to-integrate-azure-signalr/output-app-setting.png" alt-text="Fragment van uitvoer in een opdrachtvenster, met een lijstitem met de naam 'AzureSignalRConnectionString'":::

#### <a name="connect-the-function-to-event-grid"></a>De functie verbinden met Event Grid

Abonneer vervolgens de Broadcast *Azure-functie* op het **Event Grid-onderwerp dat** u hebt gemaakt tijdens de [vereiste voor de zelfstudie](how-to-integrate-azure-signalr.md#prerequisites). Hierdoor kunnen telemetriegegevens van de tweeling thermostat67 door het event grid-onderwerp en naar de functie stromen. Hier kan de functie de gegevens uitzenden naar alle clients.

Hiervoor maakt u een gebeurtenisabonnement van uw Event Grid-onderwerp naar uw *Broadcast* Azure-functie als eindpunt. 

Ga in de [Azure-portal](https://portal.azure.com/)naar uw gebeurtenisrasteronderwerp door de naam ervan te zoeken in de bovenste zoekbalk. Selecteer *+ Gebeurtenisabonnement*.

:::image type="content" source="media/how-to-integrate-azure-signalr/event-subscription-1b.png" alt-text="Azure Portal: Event Grid-abonnement":::

Vul de velden op de pagina *Gebeurtenisabonnement maken* als volgt in (velden die automatisch worden ingevuld, worden niet vermeld):
* *GEBEURTENISABONNEMENTDETAILS* > **Naam**: Geef een naam op voor uw gebeurtenisabonnement.
* *DETAILS VAN EINDPUNT* > **Type eindpunt**: Selecteer *Azure-functie* in de menuopties.
* *DETAILS VAN EINDPUNT* > **Eindpunt**: Klik op de koppeling *Selecteer een eindpunt*. Hierdoor wordt het venster *Azure-functie selecteren* geopend:
    - Vul uw **abonnement,** **resourcegroep,** **functie-app en** **functie** (*broadcast) in.* Sommige van deze waarden worden mogelijk automatisch ingevuld nadat u het abonnement hebt geselecteerd.
    - Klik op **Selectie bevestigen**.

:::image type="content" source="media/how-to-integrate-azure-signalr/create-event-subscription.png" alt-text="Azure Portal weergave van het maken van een gebeurtenisabonnement. De bovenstaande velden zijn ingevuld en de knoppen 'Selectie bevestigen' en 'Maken' zijn gemarkeerd.":::

Klik op **Maken** op de pagina *Gebeurtenisabonnement maken*.

Op dit moment ziet u twee gebeurtenisabonnementen op de *pagina Event Grid onderwerp.*

:::image type="content" source="media/how-to-integrate-azure-signalr/view-event-subscriptions.png" alt-text="Azure Portal weergave van twee gebeurtenisabonnementen op de pagina Event Grid-onderwerp." lightbox="media/how-to-integrate-azure-signalr/view-event-subscriptions.png":::

## <a name="configure-and-run-the-web-app"></a>De web-app configureren en uitvoeren

In deze sectie ziet u het resultaat in actie. Configureer eerst de **voorbeeld-clientweb-app** om verbinding te maken met de Azure SignalR-stroom die u hebt ingesteld. Vervolgens start u de **voorbeeld-app** voor het gesimuleerde apparaat die telemetriegegevens via uw Azure Digital Twins verzendt. Daarna bekijkt u de voorbeeld-web-app om te zien hoe de voorbeeld-web-app in realtime wordt bijgewerkt met de gesimuleerde apparaatgegevens.

### <a name="configure-the-sample-client-web-app"></a>De voorbeeldclient-web-app configureren

Vervolgens configureert u de voorbeeldclient-web-app. Begin met het verzamelen van **de HTTP-eindpunt-URL** van de *functie negotiate* en gebruik deze vervolgens om de app-code op uw computer te configureren.

1. Ga naar de Azure Portal [functie-apps van het](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) bedrijf en selecteer uw functie-app in de lijst. Selecteer functies in het *app-menu* en kies de functie *negotiate.*

    :::image type="content" source="media/how-to-integrate-azure-signalr/functions-negotiate.png" alt-text="Azure Portal weergave van de functie-app, met 'Functies' gemarkeerd in het menu. De lijst met functies wordt weergegeven op de pagina en de functie 'negotiate' is ook gemarkeerd.":::

1. Druk *op Functie-URL downloaden* en kopieer de waarde omhoog via **_/api_ (neem niet de laatste _/negotiate?_) op.** U gebruikt deze in de volgende stap.

    :::image type="content" source="media/how-to-integrate-azure-signalr/get-function-url.png" alt-text="Azure Portal weergave van de functie 'negotiate'. De knop Functie-URL krijgen is gemarkeerd en het gedeelte van de URL vanaf het begin tot en met '/api'":::

1. Gebruik Visual Studio of een code-editor naar keuze om de uitgepakte map _**digitaltwins-signalr-webapp-sample-main**_ te openen die u hebt gedownload in de sectie [*De*](#download-the-sample-applications) voorbeeldtoepassingen downloaden.

1. Open het *bestand src/App.js* en vervang de functie-URL in door de HTTP-eindpunt-URL van de functie negotiate die u in de vorige stap `HubConnectionBuilder` hebt opgeslagen: 

    ```javascript
        const hubConnection = new HubConnectionBuilder()
            .withUrl('<Function URL>')
            .build();
    ```
1. Navigeer Visual Studio  opdrachtprompt voor ontwikkelaars of een opdrachtvenster op uw computer naar de map *digitaltwins-signalr-webapp-sample-main\src.* Voer de volgende opdracht uit om de afhankelijke knooppuntpakketten te installeren:

    ```cmd
    npm install
    ```

Stel vervolgens machtigingen in uw functie-app in de Azure Portal:
1. Selecteer op Azure Portal pagina [Functie-apps](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) uw functie-app-exemplaar.

1. Schuif omlaag in het exemplaarmenu en selecteer *CORS.* Voeg op de cors-pagina toe `http://localhost:3000` als toegestane oorsprong door deze in het lege vak in te voegen. Schakel het selectievakje *Access-Control-Allow-Credentials inschakelen in* en druk op *Opslaan.*

    :::image type="content" source="media/how-to-integrate-azure-signalr/cors-setting-azure-function.png" alt-text="CORS-instelling in Azure Function":::

### <a name="run-the-device-simulator"></a>De apparaatsimulator uitvoeren

Tijdens de end-to-end-zelfstudie hebt u de apparaatsimulator geconfigureerd voor het verzenden van gegevens via een IoT Hub en naar uw Azure Digital Twins exemplaar. [](tutorial-end-to-end.md#configure-and-run-the-simulation)

Nu hoeft u alleen nog maar het simulatorproject te starten, dat zich bevindt in *digital-twins-samples-master > DeviceSimulator > DeviceSimulator.sln.* Als u een Visual Studio, kunt u het project openen en vervolgens uitvoeren met deze knop op de werkbalk:

:::image type="content" source="media/how-to-integrate-azure-signalr/start-button-simulator.png" alt-text="De startknop van Visual Studio (project DeviceSimulator)":::

Er wordt een consolevenster geopend waar gesimuleerde telemetrieberichten met temperaturen worden weergegeven. Deze worden verzonden via Azure Digital Twins-exemplaar, waar ze vervolgens worden opgehaald door de Azure-functies en SignalR.

U hoeft niets anders te doen in deze console, maar laat deze actief terwijl u de volgende stap voltooit.

### <a name="see-the-results"></a>De resultaten weergeven

Als u de resultaten in actie wilt zien, start u het voorbeeld van **de SignalR-integratieweb-app**. U kunt dit doen vanuit elk consolevenster op de locatie *digitaltwins-signalr-webapp-sample-main\src* door deze opdracht uit te voeren:

```cmd
npm start
```

Hiermee opent u een browservenster waarin de voorbeeld-app wordt uitgevoerd, waarin een visuele temperatuurmeter wordt weergegeven. Zodra de app wordt uitgevoerd, ziet u dat de temperatuur-telemetriewaarden van de apparaatsimulator die worden doorgegeven via Azure Digital Twins in realtime worden weergegeven door de web-app.

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-webapp-output.png" alt-text="Fragment van de voorbeeldweb-app van de client, met een visuele temperatuurmeter. De weergegeven temperatuur is 67,52":::

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources die in dit artikel zijn gemaakt niet meer nodig hebt, volgt u deze stappen om ze te verwijderen. 

Met de Azure Cloud Shell of lokale Azure CLI kunt u alle Azure-resources in een resourcegroep verwijderen met de [opdracht az group](/cli/azure/group#az_group_delete) delete. Als u de resourcegroep verwijdert, wordt ook...
* het Azure Digital Twins (uit de end-to-end zelfstudie)
* de IoT-hub en de hubapparaatregistratie (uit de end-to-end zelfstudie)
* het Event Grid-onderwerp en de bijbehorende abonnementen
* de Azure Functions app, inclusief alle drie de functies en bijbehorende resources, zoals opslag
* het Azure SignalR-exemplaar

> [!IMPORTANT]
> Het verwijderen van een resourcegroep kan niet ongedaan worden gemaakt. De resourcegroep en alle resources daarin worden permanent verwijderd. Zorg ervoor dat u niet per ongeluk de verkeerde resourcegroep of resources verwijdert. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

Verwijder ten slotte de projectvoorbeeldmappen die u hebt gedownload naar uw lokale computer (*digital-twins-samples-master.zip*, *digitaltwins-signalr-webapp-sample-main.zip* en hun uitgepakte tegenhangers).

## <a name="next-steps"></a>Volgende stappen

In dit artikel stelt u Azure-functies in met SignalR om Azure Digital Twins telemetriegebeurtenissen uit te zenden naar een voorbeeldclienttoepassing.

Hierna leert u meer over Azure SignalR Service:
* [*Wat is Azure SignalR Service?*](../azure-signalr/signalr-overview.md)

Of lees meer over Azure SignalR Service verificatie met Azure Functions:
* [*Verificatie van Azure SignalR-service*](../azure-signalr/signalr-tutorial-authenticate-azure-functions.md)