---
title: 'Zelfstudie: Azure-functies implementeren als modules - Azure IoT Edge'
description: In deze zelfstudie ontwikkelt u een Azure-functie als een IoT Edge-module, en implementeert u deze functie op een Edge-apparaat.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/29/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 85bc54402237ff8e9c82c43ab9cdbe2dc6658af1
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/14/2021
ms.locfileid: "103464102"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules"></a>Zelfstudie: Azure-functies implementeren als IoT Edge-modules

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

U kunt Azure-functies gebruiken voor het implementeren van code die uw bedrijfslogica rechtstreeks op uw Azure IoT Edge-apparaten implementeert. In deze zelfstudie leert u hoe u een Azure-functie maakt en implementeert die sensorgegevens op het gesimuleerde IoT Edge-apparaat filtert. U gebruikt het gesimuleerde IoT Edge-apparaat dat u hebt gemaakt in de Quick starts. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Visual Studio Code gebruiken om een Azure-functie te maken.
> * VS Code en Docker gebruiken om een Docker-installatiekopie te maken en deze te publiceren naar een containerregister.
> * De module vanuit het containerregister implementeren op uw IoT Edge-apparaat.
> * Gefilterde gegevens weergeven.

<center>

![Diagram - zelfstudie-architectuur, functiemodule faseren en implementeren](./media/tutorial-deploy-function/functions-architecture.png)
</center>

De Azure-functie die u maakt in deze zelfstudie filtert de temperatuurgegevens die door uw apparaat worden gegenereerd. De functie verzendt gegevens alleen upstream naar Azure IoT Hub wanneer de temperatuur boven een opgegeven drempelwaarde komt.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Voordat u met deze zelf studie begint, moet u de vorige zelf studie hebben door lopen om uw ontwikkel omgeving in te stellen voor Linux-container ontwikkeling: [ontwikkel IOT Edge-modules met behulp van Linux-containers](tutorial-develop-for-linux.md). Als u die zelfstudie hebt voltooid, hebt u de volgende vereisten klaarstaan:

* Een gratis of standaard [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)-laag in Azure.
* Een apparaat met Azure IoT Edge. U kunt de Quick starts gebruiken om een [Linux-apparaat](quickstart-linux.md) of [Windows-apparaat](quickstart.md)in te stellen.
* Een containerregister, zoals [Azure Container Registry](../container-registry/index.yml).
* [Visual Studio Code](https://code.visualstudio.com/) geconfigureerd met de [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) geconfigureerd voor het uitvoeren van Linux-containers.

Om een IoT Edge-module te ontwikkelen met Azure Functions, installeert u de volgende aanvullende vereisten op uw ontwikkelcomputer:

* [De extensie C# voor Visual Studio Code (van OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [De .NET Core 2.1 SDK](https://www.microsoft.com/net/download).

## <a name="create-a-function-project"></a>Een functieproject maken

De Azure IoT-hulpprogramma's voor Visual Studio Code die u hebt geïnstalleerd als een van de vereiste onderdelen bieden niet alleen beheermogelijkheden, maar ook enkele codesjablonen. In deze sectie gebruikt u Visual Studio Code om een IoT Edge-oplossing te maken waarin een Azure-functie wordt gebruikt.

### <a name="create-a-new-project"></a>Een nieuw project maken

Maak een C#-oplossingssjabloon voor een C#-functie die u met uw eigen code kunt aanpassen.

1. Open Visual Studio Code op uw ontwikkelcomputer.

2. Selecteer **View** > **Command Palette** en open het VS Code-opdrachtpalet.

3. Voer in het opdrachtpalet de opdracht **Azure IoT Edge: New IoT Edge solution** in en voer deze uit. Volg de aanwijzingen in het opdrachtpalet om uw oplossing te maken.

   | Veld | Waarde |
   | ----- | ----- |
   | Map selecteren | Kies de locatie op uw ontwikkelcomputer waar VS Code de oplossingsbestanden moet maken. |
   | Een naam opgeven voor de oplossing | Voer een beschrijvende naam voor uw oplossing in, bijvoorbeeld **FunctionSolution**, of accepteer de standaardinstelling. |
   | Modulesjabloon selecteren | Kies **Azure Functions - C#** . |
   | Een modulenaam opgeven | Geef de module de naam **CSharpFunction**. |
   | Opslagplaats voor Docker-afbeeldingen voor de module opgeven | Een opslagplaats voor afbeeldingen bevat de naam van het containerregister en de naam van uw containerafbeelding. De containerafbeelding wordt vooraf gevuld vanuit de laatste stap. Vervang **localhost:5000** door de waarde van de **aanmeldingsserver** uit uw Azure-containerregister. U vindt de aanmeldingsserver op de overzichtspagina van het containerregister in de Azure-portal. De uiteindelijke tekenreeks ziet er ongeveer als volgt uit \<registry name\>.azurecr.io/CSharpFunction. |

   ![Opslagplaats voor Docker-installatiekopieën opgeven](./media/tutorial-deploy-function/repository.png)

### <a name="add-your-registry-credentials"></a>Uw registerreferenties toevoegen

In het omgevingsbestand worden de referenties voor het containerregister opgeslagen. Deze referenties worden gedeeld met de IoT Edge-runtime. De runtime heeft deze referenties nodig om uw persoonlijke installatiekopieën naar het IoT Edge-apparaat te halen.

De IoT Edge-extensie probeert uw containerregisterreferenties van Azure op te halen en deze in het omgevingsbestand in te vullen. Controleer of uw referenties al zijn toegevoegd. Als dat niet het geval is, voegt u ze nu toe:

1. Open in VS Code Explorer het .env-bestand.
2. Werk de velden **gebruikersnaam** en **wachtwoord** bij met de waarden die u hebt gekopieerd uit het Azure-containerregister.
3. Sla dit bestand op.

### <a name="select-your-target-architecture"></a>Uw doelarchitectuur selecteren

Momenteel kunt u met Visual Studio Code C-modules ontwikkelen voor Linux AMD64- en Linux ARM32v7-apparaten. U moet voor elke oplossing selecteren op welke architectuur u zich richt, omdat de container voor elk type architectuur anders wordt opgebouwd en anders wordt uitgevoerd. De standaardinstelling is Linux AMD64.

1. Open het opdrachtpalet en zoek **Azure IoT Edge: standaarddoelplatform voor Edge-oplossing instellen** of selecteer het snelkoppelingspictogram onderaan het venster.

2. Selecteer in het opdrachtpalet de doelarchitectuur in de lijst met opties. In deze zelfstudie gebruiken we een virtuele Ubuntu-machine als het IoT Edge-apparaat, dus laten we de standaardinstelling **amd64** staan.

### <a name="update-the-module-with-custom-code"></a>De module bijwerken met aangepaste code

We gaan extra code toevoegen, zodat de module de berichten aan de rand verwerkt voordat deze naar IoT Hub worden doorgestuurd.

1. Open in Visual Studio Code **modules** > **CSharpFunction** > **CSharpFunction.cs**.

1. Vervang de inhoud van het bestand **CSharpFunction.cs** door de volgende code. Deze code ontvangt telemetrie over omgevings- en machinetemperatuur en stuurt het bericht alleen door naar IoT Hub als de machinetemperatuur hoger is dan een opgegeven drempelwaarde.

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.IO;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Client;
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Extensions.EdgeHub;
   using Microsoft.Azure.WebJobs.Host;
   using Microsoft.Extensions.Logging;
   using Newtonsoft.Json;

   namespace Functions.Samples
   {
       public static class CSharpFunction
       {
           [FunctionName("CSharpFunction")]
           public static async Task FilterMessageAndSendMessage(
               [EdgeHubTrigger("input1")] Message messageReceived,
               [EdgeHub(OutputName = "output1")] IAsyncCollector<Message> output,
               ILogger logger)
           {
               const int temperatureThreshold = 20;
               byte[] messageBytes = messageReceived.GetBytes();
               var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

               if (!string.IsNullOrEmpty(messageString))
               {
                   logger.LogInformation("Info: Received one non-empty message");
                   // Get the body of the message and deserialize it.
                   var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

                   if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                   {
                       // Send the message to the output as the temperature value is greater than the threshold.
                       using (var filteredMessage = new Message(messageBytes))
                       {
                            // Copy the properties of the original message into the new Message object.
                            foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                            {filteredMessage.Properties.Add(prop.Key, prop.Value);}
                            // Add a new property to the message to indicate it is an alert.
                            filteredMessage.Properties.Add("MessageType", "Alert");
                            // Send the message.
                            await output.AddAsync(filteredMessage);
                            logger.LogInformation("Info: Received and transferred a message with temperature above the threshold");
                       }
                   }
               }
           }
       }
       //Define the expected schema for the body of incoming messages.
       class MessageBody
       {
           public Machine machine {get; set;}
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
   }
   ```

1. Sla het bestand op.

## <a name="build-and-push-your-iot-edge-solution"></a>Uw IoT Edge-oplossing bouwen en pushen

In de vorige sectie hebt u een IoT Edge-oplossing gemaakt en de **CSharpFunction** aangepast om berichten uit te filteren waarin de gemelde temperatuur van de machine onder de aanvaardbare drempelwaarde ligt. Nu moet u de oplossing bouwen als een containerinstallatiekopie en deze naar het containerregister pushen.

1. Open de met VS Code geïntegreerde terminal door **View** > **Terminal** te selecteren.

2. Meld u aan bij Docker door de volgende opdracht in de terminal in te voeren. Meld u aan met de gebruikersnaam, het wachtwoord en de aanmeldingsserver uit het Azure-containerregister. U kunt deze waarden ophalen in de sectie **Toegangssleutels** van het register in Azure Portal.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Mogelijk wordt een beveiligingswaarschuwing weergegeven waarin het gebruik van `--password-stdin` wordt aanbevolen. Hoewel dit wordt aanbevolen voor productiescenario's, valt het buiten het bereik van deze zelfstudie. Zie de documentatie voor [aanmelding bij Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) voor meer informatie.

3. Klik in VS Code Explorer met de rechtermuisknop op het bestand **deployment.template.json** en selecteer **Build and Push IoT Edge solution**.

   Met de opdracht voor bouwen en pushen worden drie bewerkingen gestart. Eerst wordt er een nieuwe map met de naam **config** in de oplossing gemaakt die het volledige implementatiemanifest bevat, die is gebouwd op basis van de informatie in de implementatiesjabloon en andere oplossingsbestanden. Daarna wordt `docker build` uitgevoerd om de containerinstallatiekopie te bouwen op basis van de juiste dockerfile voor uw doelarchitectuur. Vervolgens wordt `docker push` uitgevoerd om de opslagplaats van de installatiekopie naar het containerregister te pushen.

   Dit proces kan de eerste keer enkele minuten duren, maar de volgende keer dat u de opdrachten uitvoert, wordt het sneller uitgevoerd.

## <a name="view-your-container-image"></a>De containerinstallatiekopie weergeven

In Visual Studio Code wordt een succesbericht weergegeven wanneer de containerinstallatiekopie naar het containerregister is doorgezet. Als u zelf wilt bevestigen dat de bewerking is geslaagd, kunt u de installatiekopie in het register bekijken.

1. Blader in de Azure-portal naar het Azure-containerregister.
2. Selecteer **Opslagplaatsen**.
3. U zou nu de opslagplaats **csharpfunction** in de lijst moeten zien. Selecteer deze opslagplaats om meer details te bekijken.
4. In de sectie **Tags** zou u de tag **0.0.1-amd64** moeten zien. Deze tag bevat de versie en het platform van de installatiekopie die u hebt gemaakt. Deze waarden zijn ingesteld in het bestand module.json in de map CSharpFunction.

## <a name="deploy-and-run-the-solution"></a>De oplossing implementeren en uitvoeren

U kunt de Azure-portal gebruiken om uw functiemodule op een IoT Edge-apparaat te implementeren, zoals u hebt gedaan in de quickstarts. U kunt modules ook vanuit Visual Studio Code implementeren en bewaken. In de volgende secties worden de Azure IoT-hulpprogramma's voor VS Code gebruikt die wordt vermeld in de vereisten. Installeer de extensie nu, als u dat nog niet hebt gedaan.

1. Vouw in de Visual Studio Code Explorer, onder de sectie **Azure IoT Hub**, de optie **Apparaten** uit om de lijst met IoT-apparaten weer te geven.

2. Klik met de rechtermuisknop op de naam van het IoT Edge-apparaat en selecteer **Implementatie voor één apparaat maken**.

3. Blader naar de oplossingsmap die de **CSharpFunction** bevat. Open de map config, selecteer het bestand **deployment.amd64.json** en kies **Edge-distributiemanifest selecteren**.

4. Vouw onder uw apparaat **Modules** uit voor een lijst met de geïmplementeerde en actieve modules. Klik op de knop Vernieuwen. U ziet nu dat de nieuwe **CSharpFunction** wordt uitgevoerd, samen met de module **SimulatedTemperatureSensor** en de **$edgeAgent** en **$edgeHub**.

    Het kan even duren voordat de nieuwe modules worden weergegeven. Het IoT Edge-apparaat moet de nieuwe implementatiegegevens ophalen van IoT Hub, de nieuwe containers starten en de status weer melden bij IoT Hub.

   ![Geïmplementeerde modules in VS Code weergeven](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-the-generated-data"></a>De gegenereerde gegevens weergeven

U kunt alle berichten zien die bij de IoT-hub binnenkomen door in het opdrachtpalet de opdracht **Azure IoT Hub: Start Monitoring Built-in Event Endpoint** in het opdrachtenpalet uit te voeren.

U kunt de weergave ook filteren om alle berichten te zien die bij de IoT-hub binnenkomen vanuit een specifiek apparaat. Klik met de rechtermuisknop op het apparaat in de sectie **Azure IoT Hub Devices** en selecteer **Start Monitoring Built-in Event Endpoint**.

Als u wilt stoppen met het bewaken van berichten, voert u de opdracht **Azure IoT Hub: Stop Monitoring Built-in Event Endpoint** in het opdrachtenpalet uit.

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent door te gaan met het volgende aanbevolen artikel, kunt u de resources en configuraties die u hebt gemaakt behouden en opnieuw gebruiken. U kunt ook hetzelfde IoT Edge-apparaat blijven gebruiken als een testapparaat.

Anders kunt u de lokale configuraties en Azure-resources die u in dit artikel hebt gemaakt, verwijderen om kosten te voorkomen.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een Azure-functiemodule gemaakt met code voor het filteren van onbewerkte gegevens die worden gegenereerd door uw IoT Edge-apparaat.

Ga verder met de volgende zelfstudies om te leren hoe Azure IoT Edge u verder kan helpen bij het omzetten van uw gegevens in bedrijfsinzichten.

> [!div class="nextstepaction"]
> [Gemiddelden zoeken met een zwevend venster in Azure Stream Analytics](tutorial-deploy-stream-analytics.md)
