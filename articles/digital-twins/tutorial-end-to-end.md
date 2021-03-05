---
title: 'Zelfstudie: Een end-to-end-oplossing verbinden'
titleSuffix: Azure Digital Twins
description: Zelf studie voor het uitbouwen van een end-to-end Azure Digital Twins-oplossing op basis van apparaatgegevens.
author: baanders
ms.author: baanders
ms.date: 4/15/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: 3395dc3010f7ae3aabadda8105c1765a9c300988
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102201257"
---
# <a name="tutorial-build-out-an-end-to-end-solution"></a>Zelfstudie: Een end-to-end-oplossing bouwen

Om een volledige end-to-end oplossing op basis van live data uit uw omgeving op te zetten, kunt u uw Azure Digital Twins-instantie verbinden met andere Azure-services voor het beheer van apparaten en data.

In deze zelfstudie gaat u...
> [!div class="checklist"]
> * Een Azure Digital Twins-instantie instellen
> * Het voorbeeldgebouwscenario leren kennen en de vooraf geschreven componenten instantiëren
> * Een [Azure Functions](../azure-functions/functions-overview.md)-app gebruiken om gesimuleerde telemetrie te routeren van een [IoT Hub](../iot-hub/about-iot-hub.md)-apparaat naar digitale-tweelingeigenschappen
> * Wijzigingen doorvoeren in de **tweelinggrafiek** door digitale-tweelingmeldingen te verwerken met Azure Functions, eindpunten en routes

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="set-up-cloud-shell-session"></a>Een Cloud Shell-sessie instellen
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

## <a name="get-started-with-the-building-scenario"></a>Aan de slag met het gebouwscenario

Het voorbeeldproject dat in deze zelfstudie wordt gebruikt, is een realistisch **gebouwscenario** dat een verdieping, een ruimte en een thermostaatapparaat bevat. Deze onderdelen worden digitaal weergegeven in een Azure Digital Twins-instance, die vervolgens wordt verbonden met [IoT Hub](../iot-hub/about-iot-hub.md), [Event Grid](../event-grid/overview.md)en twee [Azure-functies](../azure-functions/functions-overview.md) om gegevensverplaatsing te vergemakkelijken.

Hieronder ziet u een diagram dat het volledige scenario weergeeft. 

Eerst maakt u de Azure Digital Twins-instantie (**sectie A** in het diagram) en vervolgens stelt u de gegevensstroom van de telemetrie naar de digitale tweelingen (**pijl B**) in, waarna u de gegevensdoorgifte instelt via de tweelinggrafiek (**pijl C**).

:::image type="content" source="media/tutorial-end-to-end/building-scenario.png" alt-text="Afbeelding van het volledige gebouwscenario. Illustreert gegevens die van een apparaat naar IoT Hub stromen, via een Azure-functie (pijl B) naar een Azure Digital Twins-instantie (sectie A), en vervolgens via Event Grid naar een andere Azure-functie voor verwerking (pijl C)":::

Bij het doorwerken van het scenario interageert u met onderdelen van de vooraf geschreven voorbeeld-app die u eerder hebt gedownload.

Hier volgen de onderdelen die worden geïmplementeerd door de voorbeeld-app *AdtSampleApp* van het gebouwscenario:
* Apparaatverificatie 
* [.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)-gebruiksvoorbeelden (te vinden in *CommandLoop.cs*)
* Console-interface voor het aanroepen van de Azure Digital Twins-API
* *SampleClientApp*: een voorbeeld van een Azure Digital Twins-oplossing
* *SampleFunctionsApp*: een Azure Functions-app die uw Azure Digital Twins-grafiek bijgewerkt als resultaat van telemetrie van IoT Hub en Azure Digital Twins-gebeurtenissen

### <a name="instantiate-the-pre-created-twin-graph"></a>De vooraf gemaakte tweelinggrafiek instantiëren

Eerst gebruikt u de *AdtSampleApp*-oplossing uit het voorbeeldproject om het Azure Digital Twins-deel van het end-to-end scenario (**sectie A**) te bouwen:

:::image type="content" source="media/tutorial-end-to-end/building-scenario-a.png" alt-text="Een fragment van de volledige afbeelding van het gebouwscenario, waar sectie A, de Azure Digital Twins-instantie, eruit wordt gelicht":::

Voer in uw Visual Studio-venster met het voorbeeldproject _**AdtE2ESample**_ open het project uit met deze knop op de werkbalk:

:::image type="content" source="media/tutorial-end-to-end/start-button-sample.png" alt-text="De startknop van Visual Studio (project SampleClientApp)":::

Er wordt een consolevenster geopend, de verificatie wordt uitgevoerd en er wordt gewacht op een opdracht. Voer in deze console de volgende opdracht uit om de Azure Digital Twins-voorbeeldoplossing te instantiëren.

> [!IMPORTANT]
> Als u al digitale tweelingen en relaties in uw Azure Digital Twins-instantie hebt, worden die door deze opdracht verwijderd en vervangen door de tweelingen en relaties voor het voorbeeldscenario.

```cmd/sh
SetupBuildingScenario
```

De uitvoer van deze opdracht is een reeks bevestigingsberichten terwijl er [**digitale tweelingen**](concepts-twins-graph.md) worden gemaakt en verbonden in uw Azure Digital Twins-instantie: een verdieping met de naam *floor1*, een ruimte met de naam *room21* en een temperatuursensor met de naam *thermostat67*. Deze digitale tweelingen vertegenwoordigen de entiteiten die zouden bestaan in een werkelijke omgeving.

Ze worden via relaties verbonden tot de volgende [**tweelinggrafiek**](concepts-twins-graph.md). De tweelinggrafiek vertegenwoordigt de omgeving als geheel, met inbegrip van de relaties tussen de entiteiten en de manier waarop ze met elkaar interageren.

:::image type="content" source="media/tutorial-end-to-end/building-scenario-graph.png" alt-text="Een grafiek die laat zien dat floor1 room21 bevat en room21 thermostat67 bevat" border="false":::

U kunt de gemaakte tweelingen verifiëren door de volgende opdracht uit te voeren, waarmee alle digitale tweelingen uit de verbonden Azure Digital Twins-instantie worden opgevraagd:

```cmd/sh
Query
```

>[!TIP]
> Deze vereenvoudigde methode wordt verstrekt als onderdeel van het project _**AdtE2ESample**_. Buiten de context van deze voorbeeldcode kunt u met behulp van de [query-API's](/rest/api/digital-twins/dataplane/query) of de [CLI-opdrachten](how-to-use-cli.md) op elk gewenst moment een query uitvoeren op alle dubbels in uw exemplaar.
>
> Hier ziet u de volledige hoofdtekst van de query om alle digitale dubbels in uw exemplaar op te halen:
> 
> :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="GetAllTwins":::

Hierna kunt u stoppen met het uitvoeren van het project. Maar houd de oplossing open in Visual Studio, want u blijft deze gebruiken tijdens de zelfstudie.

## <a name="set-up-the-sample-function-app"></a>De voorbeeldfunctie-app instellen

De volgende stap is het instellen van een [Azure Functions-app-](../azure-functions/functions-overview.md) die tijdens deze zelfstudie wordt gebruikt om gegevens te verwerken. De functie-app, *SampleFunctionsApp*, bevat twee functies:
* *ProcessHubToDTEvents*: verwerkt inkomende IoT Hub-gegevens en werkt Azure Digital Twins dienovereenkomstig bij
* *ProcessDTRoutedData*: verwerkt gegevens van digitale tweelingen en werkt de bovenliggende tweelingen in Azure Digital Twins dienovereenkomstig bij

In deze sectie publiceert u de vooraf geschreven functie-app en zorgt u ervoor dat de functie-app toegang kan krijgen tot Azure Digital Twins door hieraan een AAD-identiteit (Azure Active Directory) toe te wijzen. Door deze stappen uit te voeren, kan de rest van de zelfstudie gebruikmaken van de functies in de functie-app. 

Terug in het Visual Studio-venster waarin het _**AdtE2ESample**_-project is geopend, staat de functie-app in het projectbestand _**SampleFunctionsApp**_. U kunt deze weergeven in het deelvenster *Solution Explorer*.

### <a name="update-dependencies"></a>Afhankelijkheden bijwerken

Voordat u de app publiceert, is het handig om ervoor te zorgen dat uw afhankelijkheden zijn bijgewerkt en dat u over de nieuwste versie van alle meegeleverde pakketten beschikt.

Vouw in  het deel venster Solution Explorer _**SampleFunctionsApp** > afhankelijkheden_ uit. Klik met de rechter muisknop op *Pakketten* en kies *NuGet-pakketten beheren...* .

:::image type="content" source="media/tutorial-end-to-end/update-dependencies-1.png" alt-text="Visual Studio: NuGet Packages beheren voor het SampleFunctionsApp-project" border="false":::

Hiermee opent u de NuGet Package Manager. Selecteer het tabblad *Updates* en als er pakketten moeten worden bijgewerkt, selecteert u het vakje *Alle pakketten selecteren*. Druk vervolgens op *Bijwerken*.

:::image type="content" source="media/tutorial-end-to-end/update-dependencies-2.png" alt-text="Visual Studio: Selecteren om alle pakketten in de NuGet Package Manager bij te werken":::

### <a name="publish-the-app"></a>De app publiceren

Ga terug naar het venster van Visual Studio waarin het _**AdtE2ESample**_ -project is geopend en zoek het _**SampleFunctionsApp**_ -project in het deel venster *Solution Explorer* .

[!INCLUDE [digital-twins-publish-azure-function.md](../../includes/digital-twins-publish-azure-function.md)]

### <a name="assign-permissions-to-the-function-app"></a>Machtigingen toewijzen aan de functie-app

De volgende stap is de functie-app toegang te geven tot Azure Digital Twins door een app-instelling te configureren, de app een door het systeem beheerde Microsoft Azure AD-identiteit toe te wijzen, en deze identiteit de rol *Azure Digital Twins-gegevenseigenaar* te geven in het Azure Digital Twins-exemplaar. Deze rol is vereist voor alle gebruikers of functies die veel gegevensvlakactiviteiten op het exemplaar willen uitvoeren. Meer informatie over beveiliging en roltoewijzingen vindt u in [*Concepten: Beveiliging voor Azure Digital Twins-oplossingen*](concepts-security.md).

Gebruik in Azure Cloud Shell de volgende opdracht om een toepassingsinstelling in te stellen die door uw functie-app wordt gebruikt om te verwijzen naar uw exemplaar van Azure Digital Twins. Vul de tijdelijke aanduidingen in met de details van uw resources (Houd er rekening mee dat uw URL van het Azure Digital Apparaatdubbels-exemplaar de hostnaam is voorafgegaan door *https://*).

```azurecli-interactive
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=<your-Azure-Digital-Twins-instance-URL>"
```

De uitvoer is de lijst met instellingen voor de Azure-functie, die nu een vermelding met de naam **ADT_SERVICE_URL** moet bevatten.

Gebruik de volgende opdracht om de door het systeem beheerde identiteit te maken. Zoek naar het veld **principalId** in de uitvoer.

```azurecli-interactive
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

Gebruik de waarde **principalId** uit de uitvoer in de volgende opdracht om de identiteit van de functie-app toe te wijzen aan de rol van *Azure Digital Apparaatdubbels-gegevens eigenaar* voor uw Azure Digital apparaatdubbels-exemplaar.

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
```

Het resultaat van deze opdracht is uitgevoerde informatie over de roltoewijzing die u hebt gemaakt. De functie-app heeft nu machtigingen voor toegang tot uw Azure Digital Twins-instantie.

## <a name="process-simulated-telemetry-from-an-iot-hub-device"></a>Gesimuleerde telemetrie van een IoT Hub-apparaat verwerken

Een Azure Digital Twins-grafiek is bedoeld om te worden aangedreven door telemetrie van echte apparaten. 

In deze stap verbindt u een gesimuleerd thermostaatapparaat dat in [IoT Hub](../iot-hub/about-iot-hub.md) is geregistreerd met de digitale tweeling die het vertegenwoordigt in Azure Digital Twins. Wanneer het gesimuleerde apparaat telemetrie verzendt, worden de gegevens door de Azure-functie *ProcessHubToDTEvents* geleid, die een overeenkomstige update activeert in de digitale tweeling. Op deze manier blijft de digitale tweeling up-to-date met de gegevens van het echte apparaat. In Azure Digital Twins wordt het proces waar mee gebeurtenisgegevens van de ene plaats naar de andere worden geleid [**gebeurtenisroutering**](concepts-route-events.md) genoemd.

Dat gebeurt in dit deel van het end-to-end-scenario (**pijl B**):

:::image type="content" source="media/tutorial-end-to-end/building-scenario-b.png" alt-text="Een fragment van de volledige afbeelding van het gebouwscenario, waar pijl B, de elementen voor Azure Digital Twins: het apparaat, IoT Hub en de eerste Azure-functie eruit worden gelicht":::

Dit zijn de acties die u moet voltooien om de verbinding met dit apparaat in te stellen:
1. Een IoT-hub maken waarmee het gesimuleerde apparaat wordt beheerd
2. De IoT-hub verbinden met de juiste Azure-functie door een gebeurtenisabonnement in te stellen
3. Het gesimuleerde apparaat in IoT Hub registreren
4. Het gesimuleerde apparaat uitvoeren en telemetrie genereren
5. Query's uitvoeren op Azure Digital Twins om de live resultaten te bekijken

### <a name="create-an-iot-hub-instance"></a>Een IoT Hub-instantie maken

Azure Digital Twins is bedoeld om te werken naast [IoT Hub](../iot-hub/about-iot-hub.md), een Azure-service voor het beheren van apparaten en hun gegevens. In deze stap gaat u een IoT-hub instellen waarmee het voorbeeldapparaat in deze zelfstudie wordt beheerd.

Gebruik in Azure Cloud Shell deze opdracht om een nieuwe IoT-hub te maken:

```azurecli-interactive
az iot hub create --name <name-for-your-IoT-hub> -g <your-resource-group> --sku S1
```

De uitvoer van deze opdracht is informatie over de IoT-hub die is gemaakt.

Sla de **naam** op die u hebt gegeven aan uw IOT-hub. U gebruikt dit later.

### <a name="connect-the-iot-hub-to-the-azure-function"></a>De IoT-hub verbinden met de Azure-functie

Koppel vervolgens uw IoT-hub aan de Azure-functie *ProcessHubToDTEvents* in de functie-app die u eerder hebt gepubliceerd, zodat gegevens van het apparaat in IoT Hub door de functie kunnen stromen, waardoor Azure Digital Twins wordt bijgewerkt.

Hiervoor maakt u een **gebeurtenisabonnement** in uw IoT Hub, met de Azure-functie als eindpunt. Hiermee wordt de functie 'geabonneerd' op gebeurtenissen die zich in IoT Hub voordoen.

Ga in de [Azure-portal](https://portal.azure.com/)naar uw zojuist gemaakte IoT-hub door de naam ervan te zoeken in de bovenste zoekbalk. Selecteer *Gebeurtenissen* in het hubmenu en selecteer *+ Gebeurtenisabonnement*.

:::image type="content" source="media/tutorial-end-to-end/event-subscription-1.png" alt-text="Azure Portal: IoT Hub-gebeurtenisabonnement":::

Hiermee wordt de pagina *Gebeurtenisabonnement maken* weergegeven.

:::image type="content" source="media/tutorial-end-to-end/event-subscription-2.png" alt-text="Azure-portal: gebeurtenisabonnement maken":::

Vul de velden als volgt in (velden die standaard worden ingevuld, worden niet vermeld):
* *GEBEURTENISABONNEMENTDETAILS* > **Naam**: Geef een naam op voor uw gebeurtenisabonnement.
* *DETAILS VAN HET ONDERWERP* > **Naam van systeemonderwerp**: Geef een naam op voor het systeemonderwerp. 
* *GEBEURTENISTYPEN* > **Filteren op gebeurtenistypen**: Selecteer *Apparaattelemetrie* uit de menuopties.
* *DETAILS VAN EINDPUNT* > **Type eindpunt**: Selecteer *Azure-functie* in de menuopties.
* *DETAILS VAN EINDPUNT* > **Eindpunt**: Klik op de koppeling *Selecteer een eindpunt*. Hierdoor wordt het venster *Azure-functie selecteren* geopend: :::image type="content" source="media/tutorial-end-to-end/event-subscription-3.png" alt-text="Azure-portal-gebeurtenisabonnement: Azure-functie selecteren" border="false":::
    - Vul uw **Abonnement**, **Resourcegroep**, **Functie-app** en **Functie** in (*ProcessHubToDTEvents*). Sommige van deze waarden worden mogelijk automatisch ingevuld nadat u het abonnement hebt geselecteerd.
    - Klik op **Selectie bevestigen**.

Klik op **Maken** op de pagina *Gebeurtenisabonnement maken*.

### <a name="register-the-simulated-device-with-iot-hub"></a>Het gesimuleerde apparaat bij IoT Hub registreren 

In deze sectie wordt in IoT Hub een apparaatweergave met de id *thermostat67* gemaakt. Het gesimuleerde apparaat maakt hier verbinding mee, en dit is de manier waarop telemetriegegevens van het apparaat naar IoT Hub worden gestuurd, waar de geabonneerde Azure-functie uit de vorige stap luistert, klaar om de gebeurtenissen te detecteren en door te gaan met de verwerking.

Maak in Azure Cloud Shell een apparaat in IoT Hub met de volgende opdracht:

```azurecli-interactive
az iot hub device-identity create --device-id thermostat67 --hub-name <your-IoT-hub-name> -g <your-resource-group>
```

De uitvoer is informatie over het gemaakte apparaat.

### <a name="configure-and-run-the-simulation"></a>De simulatie configureren en uitvoeren

Configureer vervolgens de apparaatsimulator om gegevens te verzenden naar uw IoT Hub-instance.

Begin door de *IoT-hubverbindingsreeks* op te halen met deze opdracht:

```azurecli-interactive
az iot hub connection-string show -n <your-IoT-hub-name>
```

Haal vervolgens de *apparaatverbindingsreeks* op met deze opdracht:

```azurecli-interactive
az iot hub device-identity connection-string show --device-id thermostat67 --hub-name <your-IoT-hub-name>
```

U neemt deze waarden op in de apparaatsimulatorcode in uw lokale project om de simulator aan te sluiten op deze IoT-hub en dit IoT-hub-apparaat.

Open in een nieuw Visual Studio-venster (vanuit de map met de gedownloade oplossing) _Apparaatsimulator > **DeviceSimulator.sln**_.

>[!NOTE]
> Als het goed is, hebt u nu twee Visual Studio-vensters: een met _**DeviceSimulator.sln**_ en een van eerder met _**AdtE2ESample.sln**_.

Selecteer in het deelvenster *Solution Explorer* in dit nieuwe Visual Studio-venster _DeviceSimulator/**AzureIoTHub.cs**_ om het te openen in het bewerkingsvenster. Wijzig de volgende verbindingsreekswaarden in de waarden die u hierboven hebt verzameld:

```csharp
iotHubConnectionString = <your-hub-connection-string>
deviceConnectionString = <your-device-connection-string>
```

Sla het bestand op.

Voer nu met deze knop op de werkbalk het **DeviceSimulator**-project uit om de resultaten te zien van de gegevenssimulatie die u hebt ingesteld:

:::image type="content" source="media/tutorial-end-to-end/start-button-simulator.png" alt-text="De startknop van Visual Studio (project DeviceSimulator)":::

Er wordt een consolevenster geopend waar gesimuleerde telemetrieberichten met temperaturen worden weergegeven. Deze worden verzonden naar IoT Hub, waar de vervolgens worden opgepakt en verwerkt door de Azure-functie.

:::image type="content" source="media/tutorial-end-to-end/console-simulator-telemetry.png" alt-text="Console-uitvoer van de apparaatsimulator die laat zien dat er temperatuurtelemetrie wordt verzonden":::

U hoeft niets anders te doen in deze console, maar laat deze draaien terwijl u de volgende stappen voltooit.

### <a name="see-the-results-in-azure-digital-twins"></a>De resultaten bekijken in Azure Digital Twins

De functie *ProcessHubToDTEvents* die u eerder hebt gepubliceerd luistert naar de gegevens van IoT Hub en roept een Azure Digital Twins-API aan om de eigenschap *Temperature* van de tweeling *thermostat67* bij te werken.

Ga naar uw Visual Studio-venster waar het project _**AdtE2ESample**_ is geopend en voer het project uit om de gegevens van de kant van Azure Digital Twins te zien.

Voer in het projectconsolevenster dat wordt geopend de volgende opdracht uit om de temperaturen op te halen die door de digitale tweeling *thermostat67* worden gerapporteerd:

```cmd
ObserveProperties thermostat67 Temperature
```

U moet de Live bijgewerkte Tempe raturen *van uw Azure Digital apparaatdubbels-exemplaar* elke twee seconden registreren bij de console.

>[!NOTE]
> Het kan een paar seconden duren voordat de gegevens van het apparaat worden door gegeven aan de dubbele. De eerste paar temperatuur metingen kunnen worden weer gegeven als 0 voordat de gegevens worden aangekomen.

:::image type="content" source="media/tutorial-end-to-end/console-digital-twins-telemetry.png" alt-text="Console-uitvoer met logboek van temperatuurberichten van digitale tweeling thermostat67":::

Nadat u hebt geverifieerd dat dit goed werkt, kunt u stoppen met het uitvoeren van beide projecten. Houd de Visual Studio-vensters open, want u gaat deze in de rest van de zelfstudie gebruiken.

## <a name="propagate-azure-digital-twins-events-through-the-graph"></a>Azure Digital Twins-gebeurtenissen doorvoeren in de grafiek

Tot zover hebt u in deze zelfstudie gezien hoe Azure Digital Twins kan worden bijgewerkt met externe apparaatgegevens. Nu gaat u zien hoe wijzigingen in één digitale tweeling kunnen worden doorgevoerd in de Azure Digital Twins-grafiek; met andere woorden, hoe tweelingen kunnen worden bijgewerkt met interne gegevens van de service.

Om dit te doen gebruikt u de Azure-functie *ProcessDTRoutedData* om een *Room*-tweeling bij te werken wanneer de verbonden *Thermostat*-tweeling wordt bijgewerkt. Dat gebeurt in dit deel van het end-to-end-scenario (**pijl C**):

:::image type="content" source="media/tutorial-end-to-end/building-scenario-c.png" alt-text="Een fragment van de volledige afbeelding van het gebouwscenario, waar pijl C, de elementen na Azure Digital Twins: het Event Grid en de tweede Azure-functie eruit worden gelicht":::

Dit zijn de acties die u moet voltooien om deze gegevensstroom in te stellen:
1. Een Event Grid-eindpunt in Azure Digital Twins maken dat de instantie verbindt met Event Grid
2. Een route binnen Azure Digital Twins instellen om wijzigingsgebeurtenissen van tweelingeigenschappen naar het eindpunt te verzenden
3. Een Azure Functions-app implementeren die (via [Event Grid](../event-grid/overview.md)) naar het eindpunt luistert, en andere tweelingen dienovereenkomstig bijwerkt
4. Het gesimuleerde apparaat uitvoeren en query's uitvoeren op Azure Digital Twins om de live resultaten te zien

### <a name="set-up-endpoint"></a>Eindpunt instellen

[Event Grid](../event-grid/overview.md) is een Azure-service die u helpt gebeurtenissen die afkomstig zijn van Azure Services te routeren en te bezorgen op andere plaatsen binnen Azure. U kunt een [gebeurtenisrasteronderwerp](../event-grid/concepts.md) maken om bepaalde gebeurtenissen van een bron te verzamelen, waarna abonnees naar het onderwerp kunnen luisteren om de gegevens te ontvangen wanneer deze binnenkomen.

In deze sectie maakt u een gebeurtenisrasteronderwerp en maakt u vervolgens een eindpunt in Azure Digital Twins dat naar dat onderwerp wijst (d.w.z. er gebeurtenissen naartoe stuurt). 

Voer de volgende opdrachten uit in Azure Cloud Shell om een gebeurtenisrasteronderwerp te maken:

```azurecli-interactive
az eventgrid topic create -g <your-resource-group> --name <name-for-your-event-grid-topic> -l <region>
```

> [!TIP]
> Voer deze opdracht uit om een lijst uit te voeren van Azure-regionamen die kunnen worden doorgegeven aan opdrachten in de Azure CLI:
> ```azurecli-interactive
> az account list-locations -o table
> ```

De uitvoer van deze opdracht is informatie over het gebeurtenisrasteronderwerp dat u hebt gemaakt.

Maak vervolgens een Event Grid-eindpunt in Azure Digital Twins, waarmee uw instantie wordt verbonden met uw Event Grid-onderwerp. Gebruik de onderstaande opdracht, waarbij u de tijdelijke aanduidingen in de velden naar behoefte invult:

```azurecli-interactive
az dt endpoint create eventgrid --dt-name <your-Azure-Digital-Twins-instance> --eventgrid-resource-group <your-resource-group> --eventgrid-topic <your-event-grid-topic> --endpoint-name <name-for-your-Azure-Digital-Twins-endpoint>
```

De uitvoer van deze opdracht is informatie over het eindpunt dat u hebt gemaakt.

U kunt ook verifiëren dat het eindpunt met succes is gemaakt, door de volgende opdracht uit te voeren om uw Azure Digital Twins-instantie voor dit eindpunt op te vragen:

```azurecli-interactive
az dt endpoint show --dt-name <your-Azure-Digital-Twins-instance> --endpoint-name <your-Azure-Digital-Twins-endpoint> 
```

Zoek naar het veld `provisioningState` in de uitvoer en controleer of de waarde 'Geslaagd' is. Het kan ook zijn dat er 'Inrichten' staat, wat betekent dat het eindpunt nog wordt gemaakt. In dat geval wacht u een paar seconden en voert u de opdracht opnieuw uit om te controleren of de bewerking is voltooid.

:::image type="content" source="media/tutorial-end-to-end/output-endpoints.png" alt-text="Resultaat van de eindpuntquery, dat laat zien dat het eindpunt de provisioningState Geslaagd heeft":::

Sla de namen die u hebt opgegeven in uw **Event grid-onderwerp** en uw event grid- **eind punt** op in azure Digital apparaatdubbels. U gebruikt deze later.

### <a name="set-up-route"></a>Route instellen

Maak vervolgens een Azure Digital Twins-route die gebeurtenissen verzendt naar het Event Grid-eindpunt dat u zojuist hebt gemaakt.

```azurecli-interactive
az dt route create --dt-name <your-Azure-Digital-Twins-instance> --endpoint-name <your-Azure-Digital-Twins-endpoint> --route-name <name-for-your-Azure-Digital-Twins-route>
```

De uitvoer van deze opdracht is informatie over de route die u hebt gemaakt.

>[!NOTE]
>Eindpunten (uit de vorige stap) moeten klaar zijn met inrichten voordat u een gebeurtenisroute kunt instellen die deze gebruikt. Als de route niet kan worden gemaakt omdat de eindpunten niet klaar zijn, dan wacht u een paar minuten en probeert u het opnieuw.

#### <a name="connect-the-function-to-event-grid"></a>De functie verbinden met Event Grid

Abonneer vervolgens de Azure-functie *ProcessDTRoutedData* op het gebeurtenisrasteronderwerp dat u eerder hebt gemaakt, zodat telemetriegegevens van de tweeling *thermostat67* door het gebeurtenisrasteronderwerp naar de functie kan stromen, die teruggaat naar Azure Digital Twins en de tweeling *room21* dienovereenkomstig bijwerkt.

Hiervoor maakt u een **Event grid-abonnement** waarmee gegevens worden verzonden vanuit het **Event grid-onderwerp** dat u eerder hebt gemaakt voor de Azure-functie van *ProcessDTRoutedData* .

Ga in de [Azure-portal](https://portal.azure.com/)naar uw gebeurtenisrasteronderwerp door de naam ervan te zoeken in de bovenste zoekbalk. Selecteer *+ Gebeurtenisabonnement*.

:::image type="content" source="media/tutorial-end-to-end/event-subscription-1b.png" alt-text="Azure Portal: Event Grid-abonnement":::

De stappen voor het maken van dit gebeurtenisabonnement lijken op degene voor het abonneren van de eerste Azure-functie op IoT Hub eerder in deze zelfstudie. Deze keer hoeft u niet *Apparaattelemetrie* op te geven als het gebeurtenistype waarnaar moet worden geluisterd, en dat u verbinding maakt met een andere Azure-functie.

Vul de velden op de pagina *Gebeurtenisabonnement maken* als volgt in (velden die automatisch worden ingevuld, worden niet vermeld):
* *GEBEURTENISABONNEMENTDETAILS* > **Naam**: Geef een naam op voor uw gebeurtenisabonnement.
* *DETAILS VAN EINDPUNT* > **Type eindpunt**: Selecteer *Azure-functie* in de menuopties.
* *DETAILS VAN EINDPUNT* > **Eindpunt**: Klik op de koppeling *Selecteer een eindpunt*. Hierdoor wordt het venster *Azure-functie selecteren* geopend:
    - Vul uw **Abonnement**, **Resourcegroep**, **Functie-app** en **Functie** in (*ProcessDTRoutedData*). Sommige van deze waarden worden mogelijk automatisch ingevuld nadat u het abonnement hebt geselecteerd.
    - Klik op **Selectie bevestigen**.

Klik op **Maken** op de pagina *Gebeurtenisabonnement maken*.

### <a name="run-the-simulation-and-see-the-results"></a>De simulatie uitvoeren en de resultaten bekijken

Nu kunt u de apparaatsimulator uitvoeren en de nieuwe gebeurtenisstroom die u het ingesteld in gang zetten. Ga naar uw Visual Studio-venster waar het project _**DeviceSimulator**_ open is, en voer het project uit.

Net als toen u eerder de apparaatsimulator hebt uitgevoerd, wordt er een consolevenster geopend waar gesimuleerde telemetrieberichten met temperaturen worden weergegeven. Deze gebeurtenissen volgen de stroom die u eerder hebt ingesteld om de *thermostat67*-tweeling bij te werken, en vervolgens de stroom die u zojuist hebt ingesteld om de *room21*-tweeling dienovereenkomstig bij te werken.

:::image type="content" source="media/tutorial-end-to-end/console-simulator-telemetry.png" alt-text="Console-uitvoer van de apparaatsimulator die laat zien dat er temperatuurtelemetrie wordt verzonden":::

U hoeft niets anders te doen in deze console, maar laat deze draaien terwijl u de volgende stappen voltooit.

Ga naar uw Visual Studio-venster waar het project _**AdtE2ESample**_ is geopend en voer het project uit om de gegevens van de kant van Azure Digital Twins te zien.

Voer in het projectconsolevenster dat wordt geopend de volgende opdracht uit om de temperaturen op te halen die **zowel** door de digitale tweeling *thermostat67* als de digitale tweeling *room21* worden gerapporteerd.

```cmd
ObserveProperties thermostat67 Temperature room21 Temperature
```

U moet de Live bijgewerkte Tempe raturen *van uw Azure Digital apparaatdubbels-exemplaar* elke twee seconden registreren bij de console. U ziet dat de temperatuur voor *room21* wordt bijgewerkt overeenkomstig de updates van *thermostat67*.

:::image type="content" source="media/tutorial-end-to-end/console-digital-twins-telemetry-b.png" alt-text="Console-uitvoer met logboek van temperatuurberichten van een thermostaat en een ruimte":::

Nadat u hebt geverifieerd dat dit goed werkt, kunt u stoppen met het uitvoeren van beide projecten. U kunt de vensters van Visual Studio nu ook sluiten, want de zelfstudie is voltooid.

## <a name="review"></a>Beoordelen

Hier volgt een overzicht van het scenario dat u in deze zelfstudie hebt uitgebouwd.

1. Een Azure Digital Twins-instance is een digitale weergave van een verdieping, een ruimte en een thermostaat (vertegenwoordigd door **sectie A** in het onderstaande diagram)
2. Er wordt gesimuleerde apparaattelemetrie verzonden naar IoT Hub, waar de Azure-functie *ProcessHubToDTEvents* luistert naar telemetriegebeurtenissen. De Azure-functie *ProcessHubToDTEvents* gebruikt de informatie in deze gebeurtenissen om de eigenschap *Temperature* van *thermostat67* (**pijl B** in het diagram) in te stellen.
3. Eigenschapswijzigingsgebeurtenissen in Azure Digital Twins worden doorgestuurd naar een gebeurtenisrasteronderwerp, waar de Azure-functie *ProcessDTRoutedData* luistert naar gebeurtenissen. De Azure-functie *ProcessDTRoutedData* gebruikt de informatie in deze gebeurtenissen om de eigenschap *Temperature* van *room21* (**pijl C** in het diagram) in te stellen.

:::image type="content" source="media/tutorial-end-to-end/building-scenario.png" alt-text="Afbeelding van het volledige gebouwscenario. Illustreert gegevens die van een apparaat naar IoT Hub stromen, via een Azure-functie (pijl B) naar een Azure Digital Twins-instantie (sectie A), en vervolgens via Event Grid naar een andere Azure-functie voor verwerking (pijl C)":::

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u deze zelf studie hebt voltooid, kunt u kiezen welke resources u wilt verwijderen, afhankelijk van wat u nu wilt doen.

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

* **Als u wilt door gaan met het Azure Digital apparaatdubbels-exemplaar dat u in dit artikel hebt ingesteld, maar een aantal of alle modellen, apparaatdubbels en relaties hebt gewist**, kunt u de [AZ DT](/cli/azure/ext/azure-iot/dt) cli-opdrachten in een [Azure Cloud shell](https://shell.azure.com) -venster gebruiken om de elementen te verwijderen die u wilt verwijderen.

    Met deze optie worden geen van de andere Azure-resources verwijderd die in deze zelf studie zijn gemaakt (IoT Hub, Azure Functions app, enzovoort). U kunt deze afzonderlijk verwijderen met behulp van de benodigde [DT-opdrachten](/cli/azure/reference-index) voor elk resource type.

Misschien wilt u ook de projectmap van uw lokale computer verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een end-to-end scenario gemaakt dat laat zien hoe Azure Digital Twins wordt aangedreven door live apparaatgegevens.

Ga vervolgens naar de conceptdocumentatie voor meer informatie over de elementen waarmee u in de zelfstudie hebt gewerkt:

> [!div class="nextstepaction"]
> [*Concepten: Aangepaste modellen*](concepts-models.md)