---
title: 'Zelfstudie: C-module maken voor Linux - Azure IoT Edge | Microsoft Docs'
description: In deze zelfstudie ziet u hoe u een IoT Edge-module met C-code maakt en deze implementeert op een Linux-apparaat met IoT Edge
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 3778361d066543325672f37cf163e58329f04abe
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106221565"
---
# <a name="tutorial-develop-a-c-iot-edge-module-using-linux-containers"></a>Zelf studie: een C IoT Edge-module ontwikkelen met Linux-containers

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Gebruik Visual Studio code om C-code te ontwikkelen en te implementeren op een apparaat met Azure IoT Edge.

U kunt IoT Edge-modules gebruiken voor het implementeren van code die uw bedrijfslogica rechtstreeks op uw IoT Edge-apparaten implementeert. In deze zelfstudie leert u een IoT Edge-module te maken die sensorgegevens filtert. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Visual Studio Code gebruiken om een IoT Edge-module in C te maken
> * Visual Studio Code en Docker gebruiken om een Docker-installatiekopie te maken en deze te publiceren in een containerregister
> * De module implementeren op uw IoT Edge-apparaat
> * Gegenereerde gegevens weergeven

De IoT Edge-module die u maakt in deze zelfstudie filtert de temperatuurgegevens die door uw apparaat worden gegenereerd. Er worden alleen gegevens upstream gezonden als de temperatuur boven een opgegeven drempelwaarde komt. Dit soort analyse is nuttig om de hoeveelheidgegevens die worden gecommuniceerd naar en opgeslagen in de cloud te reduceren.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

In deze zelf studie wordt gedemonstreerd hoe u een module ontwikkelt in **C** met **Visual Studio code** en hoe u deze implementeert op een IOT edge apparaat. Als u modules wilt ontwikkelen met behulp van Windows-containers, gaat u naar [een C IOT Edge-module ontwikkelen met behulp van Windows-containers](tutorial-c-module-windows.md) .

Gebruik de volgende tabel om inzicht te krijgen in de opties voor het ontwikkelen en implementeren van C-modules met Linux-containers:

| C | Visual Studio Code | Visual Studio |
| - | ------------------ | ------------- |
| **Linux AMD64** | ![VS Code gebruiken voor C-modules op Linux AMD64](./media/tutorial-c-module/green-check.png) | ![VS gebruiken voor C-modules op Linux AMD64](./media/tutorial-c-module/green-check.png) |
| **Linux ARM32** | ![VS Code gebruiken voor C-modules op Linux ARM32](./media/tutorial-c-module/green-check.png) | ![VS gebruiken voor C-modules op Linux ARM32](./media/tutorial-c-module/green-check.png) |

Voordat u met deze zelf studie begint, moet u de vorige zelf studie hebben door lopen om uw ontwikkel omgeving in te stellen voor Linux-container ontwikkeling: [ontwikkel IOT Edge-modules met behulp van Linux-containers](tutorial-develop-for-linux.md). Als u die zelfstudie hebt voltooid, hebt u de volgende vereisten klaarstaan:

* Een gratis of standaard [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)-laag in Azure.
* Een apparaat met Azure IoT Edge Linux-containers. U kunt de Quick starts gebruiken om een [Linux-apparaat](quickstart-linux.md) of [Windows-apparaat](quickstart.md)in te stellen.
* Een containerregister, zoals [Azure Container Registry](../container-registry/index.yml).
* [Visual Studio Code](https://code.visualstudio.com/) geconfigureerd met de [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) geconfigureerd voor het uitvoeren van Linux-containers.

Om een IoT Edge-module te ontwikkelen in C, installeert u de volgende aanvullende vereisten op uw ontwikkelcomputer:

* [C/C++-extensie](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools) voor Visual Studio Code (Engelstalig).

U hoeft de Azure IoT C SDK niet te installeren voor deze zelfstudie, maar deze kan wel handige functionaliteit bieden, zoals IntelliSense en het lezen van programmadefinities. Zie [Azure IoT C-SDK's en -bibliotheken](https://github.com/Azure/azure-iot-sdk-c) voor informatie over de installatie.

## <a name="create-a-module-project"></a>Een moduleproject maken

Met de volgende stappen maakt u een IoT Edge-moduleproject voor C met behulp van Visual Studio Code en de extensie Azure IoT-hulpprogramma's. Zodra u een projectsjabloon hebt gemaakt, voegt u nieuwe code toe zodat de module berichten filtert op basis van de gerapporteerde eigenschappen.

### <a name="create-a-new-project"></a>Een nieuw project maken

Maak een C-oplossingssjabloon die u met uw eigen code kunt aanpassen.

1. Selecteer **View** > **Command Palette** om het VS Code-opdrachtpalet te openen.

2. Typ in het opdrachtenpalet de opdracht **Azure: Aanmelden** in, voer deze uit en volg de instructies om u aan te melden bij uw Azure-account. Als u zich al hebt aangemeld, kunt u deze stap overslaan.

3. Typ in het opdrachtpalet de opdracht **Azure IoT Edge: New IoT Edge solution** in en voer deze uit. Volg de aanwijzingen in het opdrachtpalet om uw oplossing te maken.

   | Veld | Waarde |
   | ----- | ----- |
   | Map selecteren | Kies de locatie op uw ontwikkelcomputer waar VS Code de oplossingsbestanden moet maken. |
   | Een naam opgeven voor de oplossing | Voer een beschrijvende naam voor de oplossing in of accepteer de standaardnaam **EdgeSolution**. |
   | Modulesjabloon selecteren | Kies **C Module**. |
   | Een modulenaam opgeven | Geef de module de naam **CModule**. |
   | Opslagplaats voor Docker-afbeeldingen voor de module opgeven | Een opslagplaats voor afbeeldingen bevat de naam van het containerregister en de naam van uw containerafbeelding. De containerafbeelding wordt vooraf gevuld vanuit de naam die u in de laatste stap hebt opgegeven. Vervang **localhost:5000** door de waarde van de **aanmeldingsserver** uit uw Azure-containerregister. U vindt de aanmeldingsserver op de overzichtspagina van het containerregister in de Azure-portal. <br><br> De uiteindelijke opslagplaats voor de installatiekopie ziet er ongeveer als volgt uit: \<registry name\>.azurecr.io/cmodule. |

   ![Opslagplaats voor Docker-installatiekopieën opgeven](./media/tutorial-c-module/repository.png)

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

De standaardmodulecode ontvangt berichten in een invoerwachtrij en geeft deze door aan een uitvoerwachtrij. We gaan extra code toevoegen, zodat de module de berichten aan de rand verwerkt voordat deze naar IoT Hub worden doorgestuurd. Werk de module bij zodat deze de temperatuurgegevens in elk bericht analyseert en het bericht alleen naar IoT Hub verzendt als de temperatuur een bepaalde drempelwaarde overschrijdt.

1. De gegevens van de sensor in dit scenario worden in JSON-indeling aangeleverd. Als u berichten wilt filteren in een JSON-indeling, moet u een JSON-bibliotheek voor C importeren. In deze zelfstudie wordt Parson gebruikt.

   1. Download de [Parson GitHub-opslagplaats](https://github.com/kgabis/parson). Kopieer de bestanden **parson.c** en **parson.h** naar de map **CModule**.

   2. Open **modules** > **CModule** > **CMakeLists.txt**. Importeer boven in het bestand de Parson-bestanden als een bibliotheek met de naam **my_parson**.

      ```txt
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. Voeg `my_parson` toe aan de lijst met bibliotheken in de functie **target_link_libraries** van CMakeLists.txt.

   4. Sla het bestand **CMakeLists.txt** op.

   5. Open **modules** > **CModule** > **main.c**. Voeg onder aan de lijst met insluitinstructies een nieuwe toe om `parson.h` voor JSON-ondersteuning op te nemen:

      ```c
      #include "parson.h"
      ```

1. Voeg in het bestand **main.c** na de sectie include een globale variabele toe met de naam `temperatureThreshold`. Deze variabele bepaalt de waarde die de gemeten temperatuur moet overschrijden voordat de gegevens naar IoT Hub worden verzonden.

    ```c
    static double temperatureThreshold = 25;
    ```

1. Zoek de functie `CreateMessageInstance` in main.c. Vervang de interne if-else-instructie door de volgende code waarmee een aantal regels functionaliteit wordt toegevoegd:

   ```c
       if ((messageInstance->messageHandle = IoTHubMessage_Clone(message)) == NULL)
       {
           free(messageInstance);
           messageInstance = NULL;
       }
       else
       {
           messageInstance->messageTrackingId = messagesReceivedByInput1Queue;
           MAP_HANDLE propMap = IoTHubMessage_Properties(messageInstance->messageHandle);
           if (Map_AddOrUpdate(propMap, "MessageType", "Alert") != MAP_OK)
           {
              printf("ERROR: Map_AddOrUpdate Failed!\r\n");
           }
       }
   ```

   De nieuwe regels code in de else-instructie voegen een nieuwe eigenschap toe aan het bericht, waarmee het bericht wordt gelabeld als een waarschuwing. Met deze code worden alle berichten als waarschuwingen gelabeld, omdat we functionaliteit toevoegen waarmee alleen berichten worden verzonden naar IoT Hub als er hoge temperaturen worden gerapporteerd.

1. Vervang de volledige functie `InputQueue1Callback` door de volgende code. Deze functie implementeert het feitelijke berichtenfilter. Wanneer een bericht wordt ontvangen, wordt gecontroleerd of de gerapporteerde temperatuur de drempelwaarde overschrijdt. Zo ja, dan wordt het bericht doorgestuurd via de uitvoerwachtrij. Zo nee, dan wordt het bericht genegeerd.

    ```c
    static unsigned char *bytearray_to_str(const unsigned char *buffer, size_t len)
    {
        unsigned char *ret = (unsigned char *)malloc(len + 1);
        memcpy(ret, buffer, len);
        ret[len] = '\0';
        return ret;
    }

    static IOTHUBMESSAGE_DISPOSITION_RESULT InputQueue1Callback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
        IOTHUBMESSAGE_DISPOSITION_RESULT result;
        IOTHUB_CLIENT_RESULT clientResult;
        IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle = (IOTHUB_MODULE_CLIENT_LL_HANDLE)userContextCallback;

        unsigned const char* messageBody;
        size_t contentSize;

        if (IoTHubMessage_GetByteArray(message, &messageBody, &contentSize) == IOTHUB_MESSAGE_OK)
        {
            messageBody = bytearray_to_str(messageBody, contentSize);
        } else
        {
            messageBody = "<null>";
        }

        printf("Received Message [%zu]\r\n Data: [%s]\r\n",
                messagesReceivedByInput1Queue, messageBody);

        // Check if the message reports temperatures higher than the threshold
        JSON_Value *root_value = json_parse_string(messageBody);
        JSON_Object *root_object = json_value_get_object(root_value);
        double temperature;
        if (json_object_dotget_value(root_object, "machine.temperature") != NULL && (temperature = json_object_dotget_number(root_object, "machine.temperature")) > temperatureThreshold)
        {
            printf("Machine temperature %f exceeds threshold %f\r\n", temperature, temperatureThreshold);
            // This message should be sent to next stop in the pipeline, namely "output1".  What happens at "outpu1" is determined
            // by the configuration of the Edge routing table setup.
            MESSAGE_INSTANCE *messageInstance = CreateMessageInstance(message);
            if (NULL == messageInstance)
            {
                result = IOTHUBMESSAGE_ABANDONED;
            }
            else
            {
                printf("Sending message (%zu) to the next stage in pipeline\n", messagesReceivedByInput1Queue);

                clientResult = IoTHubModuleClient_LL_SendEventToOutputAsync(iotHubModuleClientHandle, messageInstance->messageHandle, "output1", SendConfirmationCallback, (void *)messageInstance);
                if (clientResult != IOTHUB_CLIENT_OK)
                {
                    IoTHubMessage_Destroy(messageInstance->messageHandle);
                    free(messageInstance);
                    printf("IoTHubModuleClient_LL_SendEventToOutputAsync failed on sending msg#=%zu, err=%d\n", messagesReceivedByInput1Queue, clientResult);
                    result = IOTHUBMESSAGE_ABANDONED;
                }
                else
                {
                    result = IOTHUBMESSAGE_ACCEPTED;
                }
            }
        }
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

1. Voeg een `moduleTwinCallback`-functie toe. Deze methode ontvangt updates van de gewenste eigenschappen van de moduledubbel en updatet de variabele **temperatureThreshold** naar dezelfde waarde. Alle modules hebben hun eigen moduledubbel, waardoor u rechtstreeks in de cloud de code kunt configureren die in een module wordt uitgevoerd.

    ```c
    static void moduleTwinCallback(DEVICE_TWIN_UPDATE_STATE update_state, const unsigned char* payLoad, size_t size, void* userContextCallback)
    {
        printf("\r\nTwin callback called with (state=%s, size=%zu):\r\n%s\r\n",
            MU_ENUM_TO_STRING(DEVICE_TWIN_UPDATE_STATE, update_state), size, payLoad);
        JSON_Value *root_value = json_parse_string(payLoad);
        JSON_Object *root_object = json_value_get_object(root_value);
        if (json_object_dotget_value(root_object, "desired.TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_dotget_number(root_object, "desired.TemperatureThreshold");
        }
        if (json_object_get_value(root_object, "TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_get_number(root_object, "TemperatureThreshold");
        }
    }
    ```

1. Zoek de functie `SetupCallbacksForModule`. Vervang de functie door de volgende code waarmee een **else if**-instructie wordt gebruikt om te controleren of de moduledubbel is bijgewerkt.

   ```c
   static int SetupCallbacksForModule(IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle)
   {
       int ret;

       if (IoTHubModuleClient_LL_SetInputMessageCallback(iotHubModuleClientHandle, "input1", InputQueue1Callback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetInputMessageCallback(\"input1\")..........FAILED!\r\n");
           ret = MU_FAILURE;
       }
       else if (IoTHubModuleClient_LL_SetModuleTwinCallback(iotHubModuleClientHandle, moduleTwinCallback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetModuleTwinCallback(default)..........FAILED!\r\n");
           ret = MU_FAILURE;
       }
       else
       {
           ret = 0;
       }

       return ret;
   }
   ```

1. Sla het bestand main.c op.

1. Open in VS Code Explorer het bestand **deployment.template.json** in de werkruimte van de IoT Edge-oplossing.

1. Voeg de moduledubbel CModule toe aan het distributiemanifest. Voeg de volgende JSON-inhoud onder aan de sectie `moduleContent` in, na de moduledubbel `$edgeHub`:

   ```json
   "CModule": {
       "properties.desired":{
           "TemperatureThreshold":25
       }
   }
   ```

   ![Dubbele CModule toevoegen aan implementatiesjabloon](./media/tutorial-c-module/module-twin.png)

1. Sla het bestand **deployment.template.json** op.

## <a name="build-and-push-your-module"></a>De module bouwen en pushen

In de vorige sectie hebt u een IoT Edge-oplossing gemaakt en code toegevoegd aan de CModule waarmee berichten worden gefilterd waarin de gemelde temperatuur van de computer binnen de aanvaardbare grenzen is. Nu moet u de oplossing bouwen als een containerinstallatiekopie en deze naar het containerregister pushen.

1. Open de VS Code-terminal door **View** > **Terminal** te selecteren.

2. Meld u aan bij Docker door de volgende opdracht in de terminal in te voeren. Meld u aan met de gebruikersnaam, het wachtwoord en de aanmeldingsserver uit het Azure-containerregister. U kunt deze waarden ophalen in de sectie **Toegangssleutels** van het register in Azure Portal.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Mogelijk wordt een beveiligingswaarschuwing weergegeven waarin het gebruik van `--password-stdin` wordt aanbevolen. Hoewel dit wordt aanbevolen voor productiescenario's, valt het buiten het bereik van deze zelfstudie. Zie de documentatie voor [aanmelding bij Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) voor meer informatie.

3. Klik in VS Code Explorer met de rechtermuisknop op het bestand **deployment.template.json** en selecteer **Build and Push IoT Edge solution**.

   Met de opdracht voor bouwen en pushen worden drie bewerkingen gestart. Eerst wordt er een nieuwe map met de naam **config** in de oplossing gemaakt die het volledige implementatiemanifest bevat, op basis van de informatie in de implementatiesjabloon en andere oplossingsbestanden. Daarna wordt `docker build` uitgevoerd om de containerinstallatiekopie te bouwen op basis van de juiste dockerfile voor uw doelarchitectuur. Vervolgens wordt `docker push` uitgevoerd om de opslagplaats van de installatiekopie naar het containerregister te pushen.

   Dit proces kan de eerste keer enkele minuten duren, maar de volgende keer dat u de opdrachten uitvoert, wordt het sneller uitgevoerd.

## <a name="deploy-modules-to-device"></a>Modules op het apparaat implementeren

Gebruik Visual Studio Code Explorer en de Azure IoT Tools-extensie om het moduleproject op uw IoT Edge-apparaat te implementeren. U hebt al een implementatiemanifest voorbereid voor uw scenario, namelijk het bestand **deployment.amd64.json** in de map config. U hoeft nu alleen nog maar een apparaat te selecteren dat de implementatie moet ontvangen.

Zorg ervoor dat uw IoT Edge-apparaat actief is.

1. Vouw in de Visual Studio Code Explorer, onder de sectie **Azure IoT Hub**, de optie **Apparaten** uit om de lijst met IoT-apparaten weer te geven.

2. Klik met de rechtermuisknop op de naam van het IoT Edge-apparaat en selecteer **Implementatie voor één apparaat maken**.

3. Selecteer het bestand **deployment.amd64.json** in de map **config** en klik vervolgens op **Edge-distributiemanifest selecteren**. Gebruik niet het bestand deployment.template.json.

4. Vouw onder uw apparaat **Modules** uit voor een lijst met de geïmplementeerde en actieve modules. Klik op de knop Vernieuwen. U ziet nu dat de nieuwe **CModule** wordt uitgevoerd, samen met de module **SimulatedTemperatureSensor** en de **$edgeAgent** en **$edgeHub**.

    Het kan enkele minuten duren voordat de modules zijn gestart. De IoT Edge-runtime moet het nieuwe implementatiemanifest ontvangen, de module-installatiekopieën uit de containerruntime ophalen en vervolgens elke nieuwe module starten.

## <a name="view-generated-data"></a>Gegenereerde gegevens weergeven

Als u het implementatiemanifest op uw IoT Edge-apparaat toepast, verzamelt de IoT Edge-runtime op het apparaat de informatie over de nieuwe implementatie en wordt deze uitgevoerd. Modules die worden uitgevoerd op het apparaat en die niet zijn opgenomen in het implementatiemanifest, worden gestopt. Alle modules die ontbreken op het apparaat worden gestart.

1. Klik in de Visual Studio Code Explorer met de rechtermuisknop op de naam van uw IoT Edge-apparaat en selecteer **Bewaking van ingebouwd gebeurteniseindpunt starten**.

2. Bekijk de berichten die in uw IoT Hub arriveren. Het kan enige tijd duren voordat de berichten worden ontvangen, omdat het IoT Edge-apparaat eerst de eigen nieuwe implementatie moet ontvangen en alle modules moet starten. Vervolgens wachten de wijzigingen die we hebben aangebracht in de CModule-code totdat de computertemperatuur 25 graden bereikt voordat berichten worden verzonden. Daarnaast wordt het berichttype **Waarschuwing** toegevoegd aan berichten die de drempelwaarde voor de temperatuur bereiken.

   ![Berichten weergeven die binnenkomen op IoT Hub](./media/tutorial-c-module/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>De moduledubbel bewerken

We hebben de CModule-moduledubbel in het implementatiemanifest gebruikt om de drempelwaarde voor de temperatuur op 25 graden in te stellen. U kunt de moduledubbel gebruiken om de functionaliteit te wijzigen zonder dat u de modulecode hoeft bij te werken.

1. Vouw in Visual Studio Code de details onder uw IoT Edge-apparaat uit om de actieve modules weer te geven.

2. Klik met de rechtermuisknop op **CModule** en selecteer **Moduledubbel bewerken**.

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