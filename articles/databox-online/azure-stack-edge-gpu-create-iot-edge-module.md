---
title: C#-IoT Edge module voor Azure Stack Edge Pro met GPU-| Microsoft Docs
description: Meer informatie over het ontwikkelen van een C#-IoT Edge module die kan worden geïmplementeerd op uw Azure Stack Edge Pro GPU-apparaat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: 1aab6fa7a2ea659b489a2e65e2a6a79070edc6b3
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873760"
---
# <a name="develop-a-c-iot-edge-module-to-move-files-on-azure-stack-edge-pro"></a>Een C#-module IoT Edge om bestanden op een Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In dit artikel wordt beschreven hoe u een module IoT Edge voor implementatie met uw Azure Stack Edge Pro apparaat. Azure Stack Edge Pro is een opslagoplossing waarmee u gegevens kunt verwerken en via het netwerk naar Azure kunt verzenden.

U kunt de Azure IoT Edge modules gebruiken met uw Azure Stack Edge Pro om de gegevens te transformeren terwijl ze naar Azure worden verplaatst. De module die in dit artikel wordt gebruikt, implementeert de logica voor het kopiëren van een bestand van een lokale share naar een cloud-share op Azure Stack Edge Pro apparaat.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Maak een containerregister voor het opslaan en beheren van uw modules (Docker-afbeeldingen).
> * Maak een IoT Edge module die u op uw apparaat Azure Stack Edge Pro implementeren.


## <a name="about-the-iot-edge-module"></a>Over de IoT Edge module

Uw Azure Stack Edge Pro kan uw apparaat implementeren en uitvoeren IoT Edge modules. Edge-modules zijn in feite Docker-containers die een specifieke taak uitvoeren, zoals het opnemen van een bericht van een apparaat, het transformeren van een bericht of het verzenden van een bericht naar een IoT Hub. In dit artikel maakt u een module die bestanden kopieert van een lokale share naar een cloud-share op Azure Stack Edge Pro apparaat.

1. Bestanden worden naar de lokale share op uw Azure Stack Edge Pro geschreven.
2. De generator voor bestandsgebeurtenissen maakt een bestandsgebeurtenis voor elk bestand dat naar de lokale share wordt geschreven. De bestandsgebeurtenissen worden ook gegenereerd wanneer een bestand wordt gewijzigd. De bestandsgebeurtenissen worden vervolgens verzonden naar IoT Edge Hub (in IoT Edge runtime).
3. De IoT Edge-module verwerkt de bestandsgebeurtenis om een bestandsgebeurtenisobject te maken dat ook een relatief pad voor het bestand bevat. De module genereert een absoluut pad met behulp van het relatieve bestandspad en kopieert het bestand van de lokale share naar de cloud-share. De module verwijdert vervolgens het bestand uit de lokale share.

![Hoe Azure IoT Edge module werkt op Azure Stack Edge Pro](./media/azure-stack-edge-gpu-create-iot-edge-module/how-module-works-1.png)

Zodra het bestand zich in de cloud share, wordt het automatisch geüpload naar uw Azure Storage account.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, controleert u of u over het volgende beschikt:

- Een Azure Stack Edge Pro apparaat dat wordt uitgevoerd.

    - Het apparaat heeft ook een gekoppelde IoT Hub resource.
    - Op het apparaat is de Edge-rekenrol geconfigureerd.
    Ga voor meer informatie naar [Compute configureren](azure-stack-edge-j-series-deploy-configure-compute.md#configure-compute) voor uw Azure Stack Edge Pro.<!--Update link?-->

- De volgende ontwikkelingsbronnen:

    - [Visual Studio Code](https://code.visualstudio.com/).
    - [De extensie C# voor Visual Studio Code (van OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
    - [Azure IoT Edge-extensie voor Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).
    - [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet/2.1).
    - [Docker CE](https://store.docker.com/editions/community/docker-ce-desktop-windows). Mogelijk moet u een account maken om de software te downloaden en te installeren.

## <a name="create-a-container-registry"></a>Een containerregister maken

Een Azure-containerregister is een persoonlijk Docker-register in Azure waar u uw persoonlijke installatiekopieën van de Docker-container kunt opslaan en beheren. De twee populaire Docker-registerservices die beschikbaar zijn in de cloud zijn Azure Container Registry en Docker Hub. In dit artikel wordt de Container Registry.

1. Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).
2. Selecteer **Een resource maken > Containers > Container Registry**. Klik op **Create**.
3. Geef het volgende op:

   1. Een unieke **registernaam** in Azure die 5 tot 50 alfanumerieke tekens bevat.
   2. Kies een **abonnement**.
   3. Maak een nieuwe resourcegroep of kies een **bestaande resourcegroep.**
   4. Selecteer een **Locatie.** We raden u aan dat deze locatie hetzelfde is als die is gekoppeld aan de Azure Stack Edge resource.
   5. Stel de **Gebruiker met beheerdersrechten** in op **Inschakelen**.
   6. Stel de SKU in op **Basic**.

      ![Containerregister maken](./media/azure-stack-edge-gpu-create-iot-edge-module/create-container-registry-1.png)
 
4. Selecteer **Maken**.
5. Nadat het containerregister is gemaakt, bladert u ernaartoe en selecteert u **Toegangssleutels**.

    ![Toegangssleutels ophalen](./media/azure-stack-edge-gpu-create-iot-edge-module/get-access-keys-1.png)
 
6. Kopieer de waarden voor **Aanmeldingsserver**, **Gebruikersnaam** en **wachtwoord**. U gebruikt deze waarden later om de Docker-afbeelding naar uw register te publiceren en om de registerreferenties toe te voegen aan Azure IoT Edge runtime.


## <a name="create-an-iot-edge-module-project"></a>Een IoT Edge-moduleproject creëren

Met de volgende stappen maakt u IoT Edge moduleproject op basis van de .NET Core 2.1 SDK. Het project gebruikt Visual Studio Code en de Azure IoT Edge extensie.

### <a name="create-a-new-solution"></a>Een nieuwe oplossing maken

Maak een C#-oplossingssjabloon die u met uw eigen code kunt aanpassen.

1. In Visual Studio Code selecteert u **Weergave > opdrachtpalet om** het OPDRACHTENpalet van VS Code te openen.
2. Voer in het opdrachtpalet de opdracht **Azure: Aanmelden** in, voer deze uit en volg de instructies om u aan te melden bij uw Azure-account. Als u al bent aangemeld, kunt u deze stap overslaan.
3. Voer in het opdrachtpalet de opdracht **Azure IoT Edge: New IoT Edge solution** in en voer deze uit. Geef in het opdrachtpalet de volgende informatie op om de oplossing te maken:

    1. Selecteer de map waarin u de oplossing wilt maken.
    2. Geef een naam op voor de oplossing of houd de standaardnaam **EdgeSolution** aan.
    
        ![Nieuwe oplossing maken 1](./media/azure-stack-edge-gpu-create-iot-edge-module/create-new-solution-1.png)

    3. Kies **C#-module** als de modulesjabloon.
    4. Vervang de standaardnaam van de module door de naam die u wilt toewijzen. In dit geval is dit **FileCopyModule.**
    
        ![Nieuwe oplossing maken 2](./media/azure-stack-edge-gpu-create-iot-edge-module/create-new-solution-2.png)

    5. Geef het containerregister dat u in de vorige sectie hebt gemaakt op als de opslagplaats voor de afbeelding voor uw eerste module. Vervang **localhost:5000** door de gekopieerde waarde voor de aanmeldingsserver.

        De uiteindelijke tekenreeks ziet er als `<Login server name>/<Module name>` uit. In dit voorbeeld is de tekenreeks: `mycontreg2.azurecr.io/filecopymodule` .

        ![Nieuwe oplossing maken 3](./media/azure-stack-edge-gpu-create-iot-edge-module/create-new-solution-3.png)

4. Ga naar **Bestand > Map openen.**

    ![Nieuwe oplossing maken 4](./media/azure-stack-edge-gpu-create-iot-edge-module/create-new-solution-4.png)

5. Blader en wijs naar de **map EdgeSolution** die u eerder hebt gemaakt. In het VS Code-venster wordt IoT Edge oplossingswerkruimte geladen met de vijf onderdelen op het hoogste niveau. U gaat de `.vscode` map, het **.gitignore-bestand,** **het .env-bestand** en de deployment.template.jsop** in dit artikel niet bewerken.
    
    Het enige onderdeel dat u wijzigt, is de map modules. Deze map bevat de C#-code voor uw module en Docker-bestanden om uw module te bouwen als een containerafbeelding.

    ![Nieuwe oplossing maken 5](./media/azure-stack-edge-gpu-create-iot-edge-module/create-new-solution-5.png)

### <a name="update-the-module-with-custom-code"></a>De module bijwerken met aangepaste code

1. Open in VS Code Explorer **modules > FileCopyModule > Program.cs.**
2. Voeg bovenaan de **fileCopyModule-naamruimte** de volgende using-instructies toe voor typen die later worden gebruikt. **Microsoft.Azure.Devices.Client.Transport.Mqtt** is een protocol voor het verzenden van berichten naar IoT Edge Hub.

    ```
    namespace FileCopyModule
    {
        using Microsoft.Azure.Devices.Client.Transport.Mqtt;
        using Newtonsoft.Json;
    ```
3. Voeg de **variabele InputFolderPath** en **OutputFolderPath toe** aan de klasse Program.

    ```
    class Program
        {
            static int counter;
            private const string InputFolderPath = "/home/input";
            private const string OutputFolderPath = "/home/output";
    ```

4. Voeg direct na de vorige stap de **klasse FileEvent** toe om de bericht-body te definiëren.

    ```
    /// <summary>
    /// The FileEvent class defines the body of incoming messages. 
    /// </summary>
    private class FileEvent
    {
        public string ChangeType { get; set; }

        public string ShareRelativeFilePath { get; set; }

        public string ShareName { get; set; }
    }
    ```

5. In de **Methode Init maakt** en configureert de code een **ModuleClient-object.** Met dit object kan de module verbinding maken met de lokale Azure IoT Edge runtime met behulp van het MQTT-protocol om berichten te verzenden en te ontvangen. De verbindingsreeks die in de methode Init wordt gebruikt, wordt door de IoT Edge-runtime aan de module geleverd. De code registreert een FileCopy-callback om berichten te ontvangen van een IoT Edge hub via het **eindpunt input1.** Vervang de **Methode Init** door de volgende code.

    ```
    /// <summary>
    /// Initializes the ModuleClient and sets up the callback to receive
    /// messages containing file event information
    /// </summary>
    static async Task Init()
    {
        MqttTransportSettings mqttSetting = new MqttTransportSettings(TransportType.Mqtt_Tcp_Only);
        ITransportSettings[] settings = { mqttSetting };

        // Open a connection to the IoT Edge runtime
        ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
        await ioTHubModuleClient.OpenAsync();
        Console.WriteLine("IoT Hub module client initialized.");

        // Register callback to be called when a message is received by the module
        await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FileCopy, ioTHubModuleClient);
    }
    ```

6. Verwijder de code voor **de pipemessage-methode** en voeg in plaats ervan de code voor **FileCopy in.**

    ```
        /// <summary>
        /// This method is called whenever the module is sent a message from the IoT Edge Hub.
        /// This method deserializes the file event, extracts the corresponding relative file path, and creates the absolute input file path using the relative file path and the InputFolderPath.
        /// This method also forms the absolute output file path using the relative file path and the OutputFolderPath. It then copies the input file to output file and deletes the input file after the copy is complete.
        /// </summary>
        static async Task<MessageResponse> FileCopy(Message message, object userContext)
        {
            int counterValue = Interlocked.Increment(ref counter);

            try
            {
                byte[] messageBytes = message.GetBytes();
                string messageString = Encoding.UTF8.GetString(messageBytes);
                Console.WriteLine($"Received message: {counterValue}, Body: [{messageString}]");

                if (!string.IsNullOrEmpty(messageString))
                {
                    var fileEvent = JsonConvert.DeserializeObject<FileEvent>(messageString);

                    string relativeFileName = fileEvent.ShareRelativeFilePath.Replace("\\", "/");
                    string inputFilePath = InputFolderPath + relativeFileName;
                    string outputFilePath = OutputFolderPath + relativeFileName;

                    if (File.Exists(inputFilePath))                
                    {
                        Console.WriteLine($"Moving input file: {inputFilePath} to output file: {outputFilePath}");
                        var outputDir = Path.GetDirectoryName(outputFilePath);
                        if (!Directory.Exists(outputDir))
                        {
                            Directory.CreateDirectory(outputDir);
                        }

                        File.Copy(inputFilePath, outputFilePath, true);
                        Console.WriteLine($"Copied input file: {inputFilePath} to output file: {outputFilePath}");
                        File.Delete(inputFilePath);
                        Console.WriteLine($"Deleted input file: {inputFilePath}");
                    } 
                    else
                    {
                        Console.WriteLine($"Skipping this event as input file doesn't exist: {inputFilePath}");   
                    }
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Caught exception: {0}", ex.Message);
                Console.WriteLine(ex.StackTrace);
            }

            Console.WriteLine($"Processed event.");
            return MessageResponse.Completed;
        }
    ```

7. Sla dit bestand op.
8. U kunt ook [een bestaand codevoorbeeld voor](https://azure.microsoft.com/resources/samples/data-box-edge-csharp-modules/?cdn=disable) dit project downloaden. Vervolgens kunt u het bestand valideren dat u hebt opgeslagen op het **bestand program.cs** in dit voorbeeld.

## <a name="build-your-iot-edge-solution"></a>Uw eigen IoT Edge-oplossing bouwen

In de vorige sectie hebt u een IoT Edge oplossing gemaakt en code toegevoegd aan fileCopyModule om bestanden van de lokale share naar de cloud share te kopiëren. Nu moet u de oplossing bouwen als een containerinstallatiekopie en deze naar het containerregister pushen.

1. Ga in VSCode naar Terminal > New Terminal om een nieuwe geïntegreerde Visual Studio Code te openen.
2. Meld u aan bij Docker door de volgende opdracht in te voeren in de geïntegreerde terminal.

    `docker login <ACR login server> -u <ACR username>`

    Gebruik de aanmeldingsserver en gebruikersnaam die u hebt gekopieerd uit het containerregister.

    ![IoT Edge-oplossingen bouwen en pushen](./media/azure-stack-edge-gpu-create-iot-edge-module/build-iot-edge-solution-1.png)

2. Wanneer u wordt gevraagd om een wachtwoord, moet u het wachtwoord opgeven. U kunt ook de waarden voor aanmeldingsserver,  gebruikersnaam en wachtwoord ophalen uit de toegangssleutels in het containerregister in de Azure Portal.
 
3. Zodra de referenties zijn opgegeven, kunt u de module-afbeelding naar uw Azure-containerregister pushen. Klik in VS Code Explorer met de rechtermuisknop op **module.jsbestand** en selecteer Build and Push **IoT Edge solution**.

    ![Oplossing voor IoT Edge bouwen en pushen 2](./media/azure-stack-edge-gpu-create-iot-edge-module/build-iot-edge-solution-2.png)
 
    Wanneer u aan Visual Studio Code opdracht geeft om uw oplossing te bouwen, worden er twee opdrachten uitgevoerd in de geïntegreerde terminal: docker build en docker push. Met deze twee opdrachten wordt uw code gebouwd, wordt de CSharpModule.dll in een container opgeslagen en wordt de code vervolgens naar het containerregister gepusht dat u hebt opgegeven toen u de oplossing initialiseerde.

    U wordt gevraagd om het moduleplatform te kiezen. Selecteer *amd64 die* overeenkomt met Linux.

    ![Platform selecteren](./media/azure-stack-edge-gpu-create-iot-edge-module/select-platform.png)

    > [!IMPORTANT] 
    > Alleen de Linux-modules worden ondersteund.

    Mogelijk ziet u de volgende waarschuwing die u kunt negeren:

    *Program.cs(77,44): waarschuwing CS1998: Deze asynchrone methode heeft geen await-operators en wordt synchroon uitgevoerd. Overweeg het gebruik van de operator 'await' om te wachten op niet-blokkerende API-aanroepen of 'await Task.Run(...)' om CPU-gebonden werk uit te voeren op een achtergrondthread.*

4. U kunt het volledige adres van de containerinstallatiekopie, inclusief de tag, zien in de geïntegreerde terminal van VS Code. Het adres van de afbeelding is gebouwd op basis van informatie die zich in de module.jsbestand met de indeling `<repository>:<version>-<platform>` . Voor dit artikel moet het er als `mycontreg2.azurecr.io/filecopymodule:0.0.1-amd64` uitzien.

## <a name="next-steps"></a>Volgende stappen

Als u deze module wilt implementeren en uitvoeren op Azure Stack Edge Pro, bekijkt u de stappen in [Een module toevoegen.](azure-stack-edge-j-series-deploy-configure-compute.md#add-a-module)<!--Update link?-->
