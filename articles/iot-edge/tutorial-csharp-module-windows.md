---
title: 'Zelfstudie: een C#-module voor Windows ontwikkelen met behulp van Azure IoT Edge'
description: In deze zelfstudie ziet u hoe u een IoT Edge-module met C#-code maakt en deze implementeert op een Windows IoT Edge-apparaat.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/03/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, amqp, devx-track-csharp
ms.openlocfilehash: 46d3a9e961be0717aba75c1f5845b97b52092510
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96931959"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>Zelfstudie: Een C#-module ontwikkelen voor Windows-apparaten met IoT Edge

Visual Studio gebruiken om C#-code te ontwikkelen en te implementeren op een Windows-apparaat met Azure IoT Edge.

U kunt Azure IoT Edge-modules gebruiken voor het implementeren van code die uw bedrijfslogica rechtstreeks op uw IoT Edge-apparaten implementeert. In deze zelfstudie leert u een IoT Edge-module te maken die sensorgegevens filtert. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Visual Studio gebruiken om een IoT Edge-module te maken op basis van de C# SDK.
> * Visual Studio en Docker gebruiken om een Docker-installatiekopie te maken en deze te publiceren naar het register.
> * De module implementeren op uw IoT Edge-apparaat.
> * Gegenereerde gegevens weergeven.

De IoT Edge-module die u maakt in deze zelfstudie filtert de temperatuurgegevens die door uw apparaat worden gegenereerd. Er worden alleen gegevens upstream gezonden als de temperatuur boven een opgegeven drempelwaarde komt. Dit soort analyse is nuttig om de hoeveelheid gegevens te reduceren die worden gecommuniceerd naar en worden opgeslagen in de cloud.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Deze zelfstudie laat zien hoe u een module kunt ontwikkelen in **C#** met behulp van **Visual Studio 2019** en hoe u deze kunt implementeren op een **Windows-apparaat**. Als u modules voor Linux-apparaten ontwikkelt, gaat u naar [Een C#-module ontwikkelen voor Linux-apparaten met IoT Edge](tutorial-csharp-module.md).

Gebruik de volgende tabel om inzicht te krijgen in de opties voor het ontwikkelen en implementeren van C#-modules op Windows-apparaten:

| C# | Visual Studio Code | Visual Studio 2017/2019 |
| -- | ------------------ | ------------------ |
| **Windows AMD64 ontwikkelen** | ![C#-modules ontwikkelen voor WinAMD64 in VS Code](./media/tutorial-c-module/green-check.png) | ![C#-modules ontwikkelen voor WinAMD64 in Visual Studio](./media/tutorial-c-module/green-check.png) |
| **Windows AMD64 fouten opsporen** |   | ![Fouten opsporen in C#-modules voor WinAMD64 in Visual Studio](./media/tutorial-c-module/green-check.png) |

Voordat u met deze zelfstudie begint, moet u de vorige zelfstudie hebben doorlopen om uw ontwikkelomgeving in te stellen: [Een IoT Edge-module ontwikkelen voor een Windows-apparaat](tutorial-develop-for-windows.md). Na voltooiing van die zelfstudie moet u de volgende vereisten al hebben:

* Een gratis of standaard [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)-laag in Azure.
* Een [Windows-apparaat met Azure IoT Edge](quickstart.md).
* Een containerregister, zoals [Azure Container Registry](../container-registry/index.yml).
* [Visual Studio 2019](/visualstudio/install/install-visual-studio) geconfigureerd met de extensie [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools).
* [Docker Desktop](https://docs.docker.com/docker-for-windows/install/) geconfigureerd voor het uitvoeren van Windows-containers.

> [!TIP]
> Als u Visual Studio 2017 (versie 15.7 of hoger) gebruikt, downloadt en installeert u [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) voor Visual Studio 2017 vanuit de Visual Studio marketplace

## <a name="create-a-module-project"></a>Een moduleproject maken

Met de volgende stappen maakt u een IoT Edge-moduleproject met behulp van Visual Studio en de extensie Azure IoT Edge Tools. Zodra u een projectsjabloon hebt gemaakt, voegt u nieuwe code toe zodat de module berichten filtert op basis van de gerapporteerde eigenschappen.

### <a name="create-a-new-project"></a>Een nieuw project maken

De extensie Azure IoT Edge Tools biedt projectsjablonen voor alle ondersteunde IoT Edge-moduletalen in Visual Studio. Deze sjablonen bevatten alle bestanden en code die u nodig hebt om een werkende module te implementeren om IoT Edge te testen of bieden u een uitgangspunt om de sjabloon aan te passen met uw eigen bedrijfslogica.

1. Start Visual Studio 2019 en selecteer **Nieuw project maken**.

2. Zoek naar **IoT Edge** en kies het project **Azure IoT Edge (Windows amd64)** . Klik op **Volgende**.

   ![Een nieuw Azure IoT Edge-project maken](./media/tutorial-csharp-module-windows/new-project.png)

3. Wijzig de naam van het project en de oplossing in iets beschrijvends, zoals **CSharpTutorialApp**. Klik op **Maken** om het project te maken.

   ![Een nieuw Azure IoT Edge-project configureren](./media/tutorial-csharp-module-windows/configure-project.png)

4. Configureer uw project met de volgende waarden:

   | Veld | Waarde |
   | ----- | ----- |
   | Een sjabloon selecteren | Selecteer **C#-module**. |
   | Moduleprojectnaam | Geef de module de naam **CSharpModule**. |
   | Opslagplaats voor Docker-installatiekopieën | Een opslagplaats voor afbeeldingen bevat de naam van het containerregister en de naam van uw containerafbeelding. De containerafbeelding wordt vooraf ingevuld met de naam van het moduleproject. Vervang **localhost:5000** door de waarde van de **aanmeldingsserver** uit uw Azure-containerregister. U vindt de aanmeldingsserver op de overzichtspagina van het containerregister in de Azure-portal. <br><br> De uiteindelijk opslagplaats voor de installatiekopie ziet er ongeveer als volgt uit: \<registry name\>.azurecr.io/csharpmodule. |

   ![Uw project configureren voor doelapparaat, moduletype en containerregister](./media/tutorial-csharp-module-windows/add-application-and-module.png)

5. Selecteer **Toevoegen** om het project te maken.

### <a name="add-your-registry-credentials"></a>Uw registerreferenties toevoegen

In het distributiemanifest worden de referenties voor het containerregister gedeeld met de IoT Edge-runtime. De runtime heeft deze referenties nodig om uw persoonlijke installatiekopieën naar het IoT Edge-apparaat te halen. Gebruik de referenties uit de sectie **Toegangssleutels** van uw Azure-containerregister.

1. Ga in Visual Studio naar de Solution Explorer en open het bestand **deployment.template.json**.

2. Zoek de eigenschap **registryCredentials** in de gewenste $edgeAgent-eigenschappen. Het adres van het register moet automatisch worden ingevuld op basis van de informatie die u hebt opgegeven bij het maken van het project. De velden voor de gebruikersnaam en het wachtwoord moeten namen van variabelen bevatten. Bijvoorbeeld:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

3. Open het **.env**-bestand in uw moduleoplossing. (Het is standaard verborgen in de Solution Explorer, dus u moet mogelijk de knop **Alle bestanden weergeven** selecteren om het weer te geven.) Het ENV-bestand moet dezelfde variabelen voor de gebruikersnaam en het wachtwoord bevatten als het bestand deployment.template.json.

4. Voeg de waarden voor de **gebruikersnaam** en het **wachtwoord** toe uit het Azure-containerregister.

5. Sla de wijzigingen in het .env-bestand op.

### <a name="update-the-module-with-custom-code"></a>De module bijwerken met aangepaste code

De standaardmodulecode ontvangt berichten in een invoerwachtrij en geeft deze door aan een uitvoerwachtrij. We gaan extra code toevoegen, zodat de module de berichten aan de rand verwerkt voordat deze naar IoT Hub worden doorgestuurd. Werk de module bij zodat deze de temperatuurgegevens in elk bericht analyseert en het bericht alleen naar IoT Hub verzendt als de temperatuur een bepaalde drempelwaarde overschrijdt.

1. Open **CSharpModule** > **Program.cs** in Visual Studio.

2. Voeg bovenaan de naamruimte **CSharpModule** drie **using**-instructies toe voor typen die later worden gebruikt:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. Voeg de variabele **temperatureThreshold** toe aan de klasse **Program** na de tellervariabele. De variabele temperatureThreshold bepaalt de waarde die de gemeten temperatuur moet overschrijden voordat de gegevens naar de IoT-hub worden verzonden.

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. Voeg de klassen **MessageBody**, **Machine** en **Ambient** toe aan de klasse **Program** class na de declaraties van variabelen. Deze klassen bepalen het verwachte schema voor de hoofdtekst van inkomende berichten.

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

5. Zoek de methode **Init**. Met deze methode wordt een **ModuleClient**-object gemaakt. Met dit object kan de module verbinding maken met de lokale Azure IoT Edge-runtime om berichten te verzenden en te ontvangen. De code registreert ook een aanroep om berichten van een IoT Edge-hub te ontvangen via het eindpunt **input1**.

   Vervang de volledige Init-methode door de volgende code:

   ```csharp
   static async Task Init()
   {
       AmqpTransportSettings amqpSetting = new AmqpTransportSettings(TransportType.Amqp_Tcp_Only);
       ITransportSettings[] settings = { amqpSetting };

       // Open a connection to the Edge runtime
       ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
       await ioTHubModuleClient.OpenAsync();
       Console.WriteLine("IoT Hub module client initialized.");

       // Read the TemperatureThreshold value from the module twin's desired properties
       var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
       await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);

       // Attach a callback for updates to the module twin's desired properties.
       await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

       // Register a callback for messages that are received by the module.
       await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
   }
   ```

   Deze bijgewerkte Init-methode stelt nog steeds de verbinding met de IoT Edge runtime in met de ModuleClient, maar voegt ook nieuwe functionaliteit toe. Hiermee worden de gewenste eigenschappen van de moduledubbel gelezen om de waarde van **temperatureThreshold** op te halen. Vervolgens wordt er een callback gemaakt die luistert naar toekomstige updates voor de gewenste eigenschappen van de moduledubbel. Met deze callback kunt u de drempelwaarde voor de temperatuur in de moduledubbel op afstand bijwerken, waarna de wijzigingen worden opgenomen in de module.

   De bijgewerkte Init-methode wijzigt ook de bestaande **SetInputMessageHandlerAsync**-methode. In de voorbeeldcode worden inkomende berichten op *input1* verwerkt met de functie *PipeMessage*, maar we willen dat wijzigen om de functie *FilterMessages* te gebruiken die we in de volgende stappen gaan maken.

6. Voeg een nieuwe methode **onDesiredPropertiesUpdate** toe aan de klasse **Program**. Deze methode ontvangt updates van de gewenste eigenschappen van de moduledubbel en updatet de variabele **temperatureThreshold** naar dezelfde waarde. Alle modules hebben hun eigen moduledubbel, waardoor u rechtstreeks in de cloud de code kunt configureren die in een module wordt uitgevoerd.

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

7. Verwijder de voorbeeldmethode **PipeMessage** en vervang deze door een nieuwe **FilterMessages**-methode. Deze methode wordt aangeroepen wanneer de module een bericht ontvangt van de IoT Edge-hub. Berichten die temperaturen onder de drempelwaarde die via de moduledubbel is ingesteld, worden hierdoor gefilterd. Ook wordt de eigenschap **BerichtType** toegevoegd aan het bericht met de waarde ingesteld op **Alarm**.

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
                using(var filteredMessage = new Message(messageBytes))
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

9. Open het bestand **deployment.template.json** in uw IoT Edge-oplossing. Dit bestand laat aan de IoT Edge-agent weten welke modules moeten worden geïmplementeerd. In dit geval gaat het om **SimulatedTemperatureSensor** en **CSharpModule**. Het bestand laat de IoT Edge-hub ook weten hoe berichten tussen de modules moeten worden gerouteerd.

10. Voeg de moduledubbel **CSharpModule** toe aan het distributiemanifest. Voeg de volgende JSON-inhoud onder aan de sectie **modulesContent** in, na de moduledubbel **$edgeHub**:

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Moduledubbel toevoegen aan implementatiesjabloon](./media/tutorial-csharp-module-windows/module-twin.png)

11. Sla het bestand deployment.template.json op.

## <a name="build-and-push-your-module"></a>De module bouwen en pushen

In de vorige sectie hebt u een IoT Edge-oplossing gemaakt en code toegevoegd aan de **CSharpModule** om berichten te filteren waarin de gemelde temperatuur van de machine onder de aanvaardbare drempelwaarde is. Nu moet u de oplossing bouwen als een containerinstallatiekopie en deze naar het containerregister pushen.

1. Gebruik de volgende opdracht om u aan te melden bij Docker op uw ontwikkelcomputer. Gebruik de gebruikersnaam, het wachtwoord en de aanmeldingsserver uit het Azure-containerregister. U kunt deze waarden ophalen in de sectie **Toegangssleutels** van het register in Azure Portal.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Mogelijk wordt een beveiligingswaarschuwing weergegeven waarin het gebruik van `--password-stdin` wordt aanbevolen. Hoewel dit wordt aanbevolen voor productiescenario's, valt het buiten het bereik van deze zelfstudie. Zie de documentatie voor [aanmelding bij Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) voor meer informatie.

2. Klik in de Solution Explorer van Visual Studio met de rechtermuisknop op de naam van het project dat u wilt bouwen. De standaardnaam is **AzureIotEdgeApp1** en omdat u een Windows-module bouwt, moet de extensie **Windows.Amd64** zijn.

3. Selecteer **IoT Edge-modules bouwen en pushen**.

   Met de opdracht voor bouwen en pushen worden drie bewerkingen gestart. Eerst wordt er een nieuwe map met de naam **config** in de oplossing gemaakt die het volledige implementatiemanifest bevat, op basis van de informatie in de implementatiesjabloon en andere oplossingsbestanden. Daarna wordt `docker build` uitgevoerd om de containerinstallatiekopie te bouwen op basis van de juiste dockerfile voor uw doelarchitectuur. Vervolgens wordt `docker push` uitgevoerd om de opslagplaats van de installatiekopie naar het containerregister te pushen.

   Dit proces kan de eerste keer enkele minuten duren, maar de volgende keer dat u de opdrachten uitvoert, wordt het sneller uitgevoerd.

## <a name="deploy-modules-to-device"></a>Modules op het apparaat implementeren

Gebruik Cloud Explorer van Visual Studio en de Azure IoT Tools-extensie om het moduleproject op uw IoT Edge-apparaat te implementeren. U hebt al een implementatiemanifest voorbereid voor uw scenario, namelijk het bestand **deployment.windows-amd64.json** in de map config. U hoeft nu alleen nog maar een apparaat te selecteren dat de implementatie moet ontvangen.

Zorg ervoor dat uw IoT Edge-apparaat actief is.

1. Vouw in Cloud Explorer van Visual Studio de resources uit om de lijst met IoT-apparaten weer te geven.

2. Klik met de rechtermuisknop op de naam van het IoT Edge-apparaat waarvan u de implementatie wilt ontvangen.

3. Selecteer **Implementatie maken**.

4. Ga in de bestandsverkenner naar de configuratiemap van uw oplossing en selecteer vervolgens het bestand **deployment.windows-amd64.json**.

5. Vernieuw Cloud Explorer om de geïmplementeerde modules onder uw apparaat weer te geven.

## <a name="view-generated-data"></a>Gegenereerde gegevens weergeven

Als u het implementatiemanifest op uw IoT Edge-apparaat toepast, verzamelt de IoT Edge-runtime op het apparaat de informatie over de nieuwe implementatie en wordt deze uitgevoerd. Modules die worden uitgevoerd op het apparaat en die niet zijn opgenomen in het implementatiemanifest, worden gestopt. Alle modules die ontbreken op het apparaat worden gestart.

U kunt de IoT Edge Tools-extensie gebruiken om berichten weer te geven als ze arriveren in uw IoT Hub.

1. Selecteer in Cloud Explorer van Visual Studio de naam van het IoT Edge-apparaat.

2. Selecteer in de lijst **Actions** de optie **Start Monitoring Built-in Event Endpoint**.

3. Bekijk de berichten die in uw IoT Hub arriveren. Het kan even duren voordat er berichten arriveren, omdat de wijzigingen die we hebben aangebracht in de CSharpModule-code wachten totdat de computertemperatuur 25 graden bereikt voordat berichten worden verzonden. Daarnaast wordt het berichttype **Waarschuwing** toegevoegd aan berichten die de drempelwaarde voor de temperatuur bereiken.

   ![Berichten weergeven die binnenkomen op IoT Hub](./media/tutorial-csharp-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>De moduledubbel bewerken

We hebben de CSharpModule-moduledubbel gebruikt om de drempelwaarde voor de temperatuur op 25 graden in te stellen. U kunt de moduledubbel gebruiken om de functionaliteit te wijzigen zonder dat u de modulecode hoeft bij te werken.

1. Open het bestand **deployment.windows-amd64.json** in Visual Studio. (Open niet het bestand deployment.template. Als u het distributiemanifest niet ziet in het configuratiebestand in de Solution Explorer, selecteert u het pictogram **Alle bestanden weergeven** in de werkbalk.)

2. Zoek de CSharpModule-dubbel en wijzig de waarde van de parameter **temperatureThreshold** in een nieuwe temperatuur die 5 tot 10 graden hoger is dan de laatste gerapporteerde temperatuur.

3. Sla het bestand **deployment.windows-amd64.json** op.

4. Volg de implementatiestappen opnieuw om het bijgewerkte distributiemanifest op uw apparaat toe te passen.

5. Bewaak de binnenkomende apparaat-naar-cloud-berichten. U ziet dat de berichten stoppen totdat de nieuwe temperatuurdrempel is bereikt.

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent door te gaan met het volgende aanbevolen artikel, kunt u de resources en configuraties die u hebt gemaakt behouden en opnieuw gebruiken. U kunt ook hetzelfde IoT Edge-apparaat blijven gebruiken als een testapparaat.

Anders kunt u de lokale configuraties en Azure-resources die u in dit artikel hebt gemaakt, verwijderen om kosten te voorkomen.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een IoT Edge-module gemaakt met code voor het filteren van onbewerkte gegevens die worden gegenereerd door uw IoT Edge-apparaat.

U kunt doorgaan met de volgende zelfstudies om te leren hoe Azure IoT Edge u kan helpen bij de implementatie van Azure Cloud Services voor het verwerken en analyseren van gegevens aan de rand.

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)
