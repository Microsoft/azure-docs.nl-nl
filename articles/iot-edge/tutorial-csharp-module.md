---
title: 'Zelfstudie: een C#-module voor Linux ontwikkelen met behulp van Azure IoT Edge'
description: In deze zelfstudie ziet u hoe u een IoT Edge-module met C#-code maakt en deze implementeert op een Linux IoT Edge-apparaat
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: e24c04152911f976907e6bdc433c33e035ade639
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874702"
---
# <a name="tutorial-develop-a-c-iot-edge-module-using-linux-containers"></a>Zelfstudie: Een C#-IoT Edge ontwikkelen met Linux-containers

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Gebruik Visual Studio Code om C#-code te ontwikkelen en deze te implementeren op een apparaat met Azure IoT Edge.

U kunt Azure IoT Edge-modules gebruiken voor het implementeren van code die uw bedrijfslogica rechtstreeks op uw IoT Edge-apparaten implementeert. In deze zelfstudie leert u een IoT Edge-module te maken die sensorgegevens filtert. U gebruikt het gesimuleerde IoT Edge-apparaat dat u hebt gemaakt in de snelstarts. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Visual Studio Code gebruiken om een IoT Edge-module te maken op basis van de .NET Core 2.1 SDK.
> * Visual Studio Code en Docker gebruiken om een Docker-installatiekopie te maken en deze te publiceren naar het register.
> * De module implementeren op uw IoT Edge-apparaat.
> * Gegenereerde gegevens weergeven.

De IoT Edge-module die u maakt in deze zelfstudie filtert de temperatuurgegevens die door uw apparaat worden gegenereerd. Er worden alleen gegevens upstream gezonden als de temperatuur boven een opgegeven drempelwaarde komt. Dit soort analyse is nuttig om de hoeveelheid gegevens te reduceren die worden gecommuniceerd naar en worden opgeslagen in de cloud.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

In deze zelfstudie wordt gedemonstreerd hoe u een module in **C#** ontwikkelt met **behulp van Visual Studio Code** en deze implementeert op een IoT Edge apparaat. Als u modules ontwikkelt met Windows-containers, gaat u naar Een C# IoT Edge ontwikkelen met [Windows-containers.](tutorial-csharp-module-windows.md)

Gebruik de volgende tabel om inzicht te krijgen in uw opties voor het ontwikkelen en implementeren van C#-modules met Behulp van Linux-containers:

| C# | Visual Studio Code | Visual Studio |
| -- | ------------------ | ------------- |
| **Linux AMD64** | ![C#-modules voor LinuxAMD64 in VS Code](./media/tutorial-c-module/green-check.png) | ![C#-modules voor LinuxAMD64 in Visual Studio](./media/tutorial-c-module/green-check.png) |
| **Linux ARM32** | ![C#-modules voor LinuxARM32 in VS Code](./media/tutorial-c-module/green-check.png) | ![C#-modules voor LinuxARM64 in Visual Studio](./media/tutorial-c-module/green-check.png) |

>[!NOTE]
>Ondersteuning voor Linux ARM64-apparaten is beschikbaar in [openbare preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Zie [ARM64 IoT Edge-modules ontwikkelen in Visual Studio Code en er fouten in opsporen (preview)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview) voor meer informatie.

Voordat u met deze zelfstudie begint, moet u de vorige zelfstudie hebben doorgegaan om uw ontwikkelomgeving in te stellen: Een [IoT Edge-module ontwikkelen met Linux-containers.](tutorial-develop-for-linux.md) Na voltooiing van die zelfstudie moet u de volgende vereisten al hebben:

* Een gratis of standaard [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)-laag in Azure.
* Een apparaat met Azure IoT Edge linux-containers. U kunt de quickstarts gebruiken om een [Linux-apparaat](quickstart-linux.md) of [Windows-apparaat in te stellen.](quickstart.md)
* Een containerregister, zoals [Azure Container Registry](../container-registry/index.yml).
* [Visual Studio Code](https://code.visualstudio.com/) geconfigureerd met de [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) geconfigureerd voor het uitvoeren van Linux-containers.

Om deze zelfstudies te voltooien, bereidt u de volgende aanvullende vereisten voor op uw ontwikkelcomputer:

* [De extensie C# voor Visual Studio Code (van OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet/2.1).

## <a name="create-a-module-project"></a>Een moduleproject maken

Met de volgende stappen maakt u een IoT Edge-moduleproject voor C++ met behulp van Visual Studio Code en de extensie Azure IoT-hulpprogramma's. Zodra u een projectsjabloon hebt gemaakt, voegt u nieuwe code toe zodat de module berichten filtert op basis van de gerapporteerde eigenschappen.

### <a name="create-a-new-project"></a>Een nieuw project maken

Maak een C#-oplossingssjabloon die u met uw eigen code kunt aanpassen.

1. Selecteer in Visual Studio Code **View** > **Command Palette** om het VS Code-opdrachtpalet te openen.

2. Voer in het opdrachtpalet de opdracht **Azure: Aanmelden** in, voer deze uit en volg de instructies om u aan te melden bij uw Azure-account. Als u al bent aangemeld, kunt u deze stap overslaan.

3. Voer in het opdrachtpalet de opdracht **Azure IoT Edge: New IoT Edge solution** in en voer deze uit. Volg de aanwijzingen in het opdrachtpalet om uw oplossing te maken.

   | Veld | Waarde |
   | ----- | ----- |
   | Map selecteren | Kies de locatie op uw ontwikkelcomputer waar VS Code de oplossingsbestanden moet maken. |
   | Een naam opgeven voor de oplossing | Voer een beschrijvende naam voor de oplossing in of accepteer de standaardnaam **EdgeSolution**. |
   | Modulesjabloon selecteren | Kies **C# Module**. |
   | Een modulenaam opgeven | Geef de module de naam **CSharpModule**. |
   | Opslagplaats voor Docker-afbeeldingen voor de module opgeven | Een opslagplaats voor afbeeldingen bevat de naam van het containerregister en de naam van uw containerafbeelding. De containerafbeelding wordt vooraf gevuld vanuit de naam die u in de laatste stap hebt opgegeven. Vervang **localhost:5000** door de waarde van de **aanmeldingsserver** uit uw Azure-containerregister. U vindt de aanmeldingsserver op de overzichtspagina van het containerregister in de Azure-portal. <br><br>De uiteindelijk opslagplaats voor de installatiekopie ziet er ongeveer als volgt uit: \<registry name\>.azurecr.io/csharpmodule. |

   ![Opslagplaats voor Docker-installatiekopieën opgeven](./media/tutorial-csharp-module/repository.png)

### <a name="add-your-registry-credentials"></a>Uw registerreferenties toevoegen

In het omgevingsbestand worden de referenties voor het containerregister opgeslagen. Deze referenties worden gedeeld met de IoT Edge-runtime. De runtime heeft deze referenties nodig om uw persoonlijke installatiekopieën naar het IoT Edge-apparaat te halen. Gebruik de referenties uit de sectie **Toegangssleutels** van uw Azure-containerregister.

De IoT Edge-extensie probeert uw containerregisterreferenties van Azure op te halen en deze in het omgevingsbestand in te vullen. Controleer of uw referenties al zijn toegevoegd. Als dat niet het geval is, voegt u ze nu toe:

1. Open in VS Code Explorer het bestand **.env**.
2. Werk de velden **username** en **password** bij met de waarden die u hebt gekopieerd uit het Azure-containerregister.
3. Sla dit bestand op.

### <a name="select-your-target-architecture"></a>Uw doelarchitectuur selecteren

Momenteel kunt u met Visual Studio Code C++-modules ontwikkelen voor Linux AMD64- en Linux ARM32v7-apparaten. U moet voor elke oplossing selecteren op welke architectuur u zich richt, omdat de container voor elk type architectuur anders wordt opgebouwd en anders wordt uitgevoerd. De standaardinstelling is Linux AMD64.

1. Open het opdrachtpalet en zoek **Azure IoT Edge: standaarddoelplatform voor Edge-oplossing instellen** of selecteer het snelkoppelingspictogram onderaan het venster.

2. Selecteer in het opdrachtpalet de doelarchitectuur in de lijst met opties. In deze zelfstudie gebruiken we een virtuele Ubuntu-machine als het IoT Edge-apparaat, dus laten we de standaardinstelling **amd64** staan.

### <a name="update-the-module-with-custom-code"></a>De module bijwerken met aangepaste code

1. Open in VS Code Explorer **modules** > **CSharpModule** > **Program.cs**.

2. Voeg bovenaan de naamruimte **CSharpModule** drie **using**-instructies toe voor typen die later worden gebruikt:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. Voeg de variabele **temperatureThreshold** toe aan de klasse **Program**. Deze variabele bepaalt de waarde die de gemeten temperatuur moet overschrijden voordat de gegevens naar de IoT-hub worden verzonden.

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. Voeg de klassen **MessageBody**, **Machine** en **Ambient** toe aan de klasse **Program**. Deze klassen bepalen het verwachte schema voor de hoofdtekst van inkomende berichten.

    ```csharp
    class MessageBody
    {
        public Machine machine {get;set;}
        public Ambient ambient {get; set;}
        public string timeCreated {get; set;}
    }
    class Machine
    {
        public double temperature {get; set;}
        public double pressure {get; set;}
    }
    class Ambient
    {
        public double temperature {get; set;}
        public int humidity {get; set;}
    }
    ```

5. Zoek de functie **Init**. Met deze functie wordt een **ModuleClient**-object gemaakt. Met dit object kan de module verbinding maken met de lokale Azure IoT Edge-runtime om berichten te verzenden en te ontvangen. Nadat de **ModuleClient** is gemaakt, leest de code de waarde van **temperatureThreshold** uit de gewenste eigenschappen van de moduledubbel. De code registreert een aanroep om berichten van een IoT Edge-hub te ontvangen via het eindpunt **input1**. Vervang de methode **SetInputMessageHandlerAsync** door een nieuwe en voeg een methode **SetDesiredPropertyUpdateCallbackAsync** toe voor updates van de gewenste eigenschappen. Om deze wijziging aan te brengen, moet u de laatste regel van de methode **Init** vervangen door de volgende code:

    ```csharp
    // Register a callback for messages that are received by the module.
    // await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Read the TemperatureThreshold value from the module twin's desired properties
    var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
    await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);

    // Attach a callback for updates to the module twin's desired properties.
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

    // Register a callback for messages that are received by the module.
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

6. Voeg de methode **onDesiredPropertiesUpdate** toe aan de klasse **Program**. Deze methode ontvangt updates van de gewenste eigenschappen van de moduledubbel en updatet de variabele **temperatureThreshold** naar dezelfde waarde. Alle modules hebben hun eigen moduledubbel, waardoor u rechtstreeks in de cloud de code kunt configureren die in een module wordt uitgevoerd.

    ```csharp
    static Task OnDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"]!=null)
                temperatureThreshold = desiredProperties["TemperatureThreshold"];

        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error when receiving desired property: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error when receiving desired property: {0}", ex.Message);
        }
        return Task.CompletedTask;
    }
    ```

7. Vervang de methode **PipeMessage** door de methode **FilterMessages**. Deze methode wordt aangeroepen wanneer de module een bericht ontvangt van de IoT Edge-hub. Berichten die temperaturen onder de drempelwaarde die via de moduledubbel is ingesteld, worden hierdoor gefilterd. Ook wordt de eigenschap **BerichtType** toegevoegd aan het bericht met de waarde ingesteld op **Alarm**.

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        var counterValue = Interlocked.Increment(ref counter);
        try
        {
            ModuleClient moduleClient = (ModuleClient)userContext;
            var messageBytes = message.GetBytes();
            var messageString = Encoding.UTF8.GetString(messageBytes);
            Console.WriteLine($"Received message {counterValue}: [{messageString}]");

            // Get the message body.
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                Console.WriteLine($"Machine temperature {messageBody.machine.temperature} " +
                    $"exceeds threshold {temperatureThreshold}");
                using (var filteredMessage = new Message(messageBytes))
                {
                    foreach (KeyValuePair<string, string> prop in message.Properties)
                    {
                        filteredMessage.Properties.Add(prop.Key, prop.Value);
                    }

                    filteredMessage.Properties.Add("MessageType", "Alert");
                    await moduleClient.SendEventAsync("output1", filteredMessage);
                }
            }

            // Indicate that the message treatment is completed.
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed.
            var moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed.
            ModuleClient moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

8. Sla het bestand Program.cs op.

9. Open in VS Code Explorer het bestand **deployment.template.json** in de werkruimte van de IoT Edge-oplossing.

10. Voeg de moduledubbel **CSharpModule** toe aan het distributiemanifest. Voeg de volgende JSON-inhoud onder aan de sectie **modulesContent** in, na de moduledubbel **$edgeHub**:

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Moduledubbel toevoegen aan implementatiesjabloon](./media/tutorial-csharp-module/module-twin.png)

11. Sla het bestand deployment.template.json op.

## <a name="build-and-push-your-module"></a>De module bouwen en pushen

In de vorige sectie hebt u een IoT Edge-oplossing gemaakt en code toegevoegd aan de CSharpModule. Met de nieuwe code worden berichten uitgefilterd waarbij de gerapporteerde temperatuur van de computer binnen de toegestane limieten valt. Nu moet u de oplossing bouwen als een containerinstallatiekopie en deze naar het containerregister pushen.

1. Open de met VS Code geïntegreerde terminal door **View** > **Terminal** te selecteren.

1. Meld u aan bij Docker door de volgende opdracht in de terminal in te voeren. Meld u aan met de gebruikersnaam, het wachtwoord en de aanmeldingsserver uit het Azure-containerregister. U kunt deze waarden ophalen in de sectie **Toegangssleutels** van het register in Azure Portal.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Mogelijk wordt een beveiligingswaarschuwing weergegeven waarin het gebruik van `--password-stdin` wordt aanbevolen. Hoewel dit wordt aanbevolen voor productiescenario's, valt het buiten het bereik van deze zelfstudie. Zie de documentatie voor [aanmelding bij Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) voor meer informatie.

1. Klik in VS Code Explorer met de rechtermuisknop op het bestand **deployment.template.json** en selecteer **Build and Push IoT Edge solution**.

   Met de opdracht voor bouwen en pushen worden drie bewerkingen gestart. Eerst wordt er een nieuwe map met de naam **config** in de oplossing gemaakt die het volledige implementatiemanifest bevat, op basis van de informatie in de implementatiesjabloon en andere oplossingsbestanden. Daarna wordt `docker build` uitgevoerd om de containerinstallatiekopie te bouwen op basis van de juiste dockerfile voor uw doelarchitectuur. Vervolgens wordt `docker push` uitgevoerd om de opslagplaats van de installatiekopie naar het containerregister te pushen.

   Dit proces kan de eerste keer enkele minuten duren, maar de volgende keer dat u de opdrachten uitvoert, wordt het sneller uitgevoerd.

## <a name="deploy-and-run-the-solution"></a>De oplossing implementeren en uitvoeren

Gebruik Visual Studio Code Explorer en de Azure IoT Tools-extensie om het moduleproject op uw IoT Edge-apparaat te implementeren. U hebt al een implementatiemanifest voorbereid voor uw scenario, namelijk het bestand **deployment.amd64.json** in de map config. U hoeft nu alleen nog maar een apparaat te selecteren dat de implementatie moet ontvangen.

Zorg ervoor dat uw IoT Edge-apparaat actief is.

1. Vouw in de Visual Studio Code Explorer, onder de sectie **Azure IoT Hub**, de optie **Apparaten** uit om de lijst met IoT-apparaten weer te geven.

2. Klik met de rechtermuisknop op de naam van het IoT Edge-apparaat en selecteer **Implementatie voor één apparaat maken**.

3. Selecteer het bestand **deployment.amd64.json** in de map **config** en klik vervolgens op **Edge-distributiemanifest selecteren**. Gebruik niet het bestand deployment.template.json.

4. Vouw onder uw apparaat **Modules** uit voor een lijst met de geïmplementeerde en actieve modules. Klik op de knop Vernieuwen. U ziet nu dat de nieuwe **CSharpModule** wordt uitgevoerd, samen met de module **SimulatedTemperatureSensor** en de **$edgeAgent** en **$edgeHub**.

    Het kan enkele minuten duren voordat de modules zijn gestart. De IoT Edge-runtime moet het nieuwe implementatiemanifest ontvangen, de module-installatiekopieën uit de containerruntime ophalen en vervolgens elke nieuwe module starten.

## <a name="view-generated-data"></a>Gegenereerde gegevens weergeven

Als u het implementatiemanifest op uw IoT Edge-apparaat toepast, verzamelt de IoT Edge-runtime op het apparaat de informatie over de nieuwe implementatie en wordt deze uitgevoerd. Modules die worden uitgevoerd op het apparaat en die niet zijn opgenomen in het implementatiemanifest, worden gestopt. Alle modules die ontbreken op het apparaat worden gestart.

1. Klik in de Visual Studio Code Explorer met de rechtermuisknop op de naam van uw IoT Edge-apparaat en selecteer **Bewaking van ingebouwd gebeurteniseindpunt starten**.

2. Bekijk de berichten die in uw IoT Hub arriveren. Het kan enige tijd duren voordat de berichten worden ontvangen, omdat het IoT Edge-apparaat eerst de eigen nieuwe implementatie moet ontvangen en alle modules moet starten. Vervolgens wachten de wijzigingen die we hebben aangebracht in de CModule-code totdat de computertemperatuur 25 graden bereikt voordat berichten worden verzonden. Daarnaast wordt het berichttype **Waarschuwing** toegevoegd aan berichten die de drempelwaarde voor de temperatuur bereiken.

   ![Berichten weergeven die binnenkomen op IoT Hub](./media/tutorial-csharp-module/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>De moduledubbel bewerken

We hebben de CSharpModule-moduledubbel in het implementatiemanifest gebruikt om de drempelwaarde voor de temperatuur op 25 graden in te stellen. U kunt de moduledubbel gebruiken om de functionaliteit te wijzigen zonder dat u de modulecode hoeft bij te werken.

1. Vouw in Visual Studio Code de details onder uw IoT Edge-apparaat uit om de actieve modules weer te geven.

2. Klik met de rechtermuisknop op **CSharpModule** en selecteer **Moduledubbel bewerken**.

3. Zoek **TemperatureThreshold** op in de gewenste eigenschappen. Wijzig de waarde in een nieuwe temperatuur van vijf graden tot 10 graden hoger dan de laatst gerapporteerde temperatuur.

4. Sla het moduledubbelbestand op.

5. Klik met de rechtermuisknop op een willekeurige plaats in het deelvenster Moduledubbel bewerken en selecteer **Moduledubbel bijwerken**.

6. Bewaak de binnenkomende apparaat-naar-cloud-berichten. U ziet dat de berichten stoppen totdat de nieuwe temperatuurdrempel is bereikt.

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent door te gaan met het volgende aanbevolen artikel, kunt u de resources en configuraties die u hebt gemaakt behouden en opnieuw gebruiken. U kunt ook hetzelfde IoT Edge-apparaat blijven gebruiken als een testapparaat.

Anders kunt u de lokale configuraties en Azure-resources die u in dit artikel hebt gemaakt, verwijderen om kosten te voorkomen.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een IoT Edge-module gemaakt die code bevat voor het filteren van onbewerkte gegevens die worden gegenereerd door uw IoT Edge-apparaat.

U kunt doorgaan met de volgende zelfstudies om te leren hoe Azure IoT Edge u kan helpen bij de implementatie van Azure Cloud Services voor het verwerken en analyseren van gegevens aan de rand.

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)