---
title: 'Zelfstudie: C-module maken voor Windows - Azure IoT Edge | Microsoft Docs'
description: In deze zelfstudie ziet u hoe u een IoT Edge-module met C-code maakt en deze implementeert op een Windows-apparaat met IoT Edge
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: d9cffcadcb95b6c8c61205d458610f402fa7286d
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94964589"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>Zelfstudie: Een C-module ontwikkelen voor Windows-apparaten met IoT Edge

Visual Studio gebruiken om C-code te ontwikkelen en te implementeren op een Windows-apparaat met Azure IoT Edge.

U kunt Azure IoT Edge-modules gebruiken voor het implementeren van code die uw bedrijfslogica rechtstreeks op uw IoT Edge-apparaten implementeert. In deze zelfstudie leert u een IoT Edge-module te maken die sensorgegevens filtert. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Visual Studio gebruiken om een IoT Edge-module te maken op basis van de C SDK.
> * Visual Studio en Docker gebruiken om een Docker-installatiekopie te maken en deze te publiceren naar het register.
> * De module implementeren op uw IoT Edge-apparaat.
> * Gegenereerde gegevens weergeven.

De IoT Edge-module die u maakt in deze zelfstudie filtert de temperatuurgegevens die door uw apparaat worden gegenereerd. Er worden alleen gegevens upstream gezonden als de temperatuur boven een opgegeven drempelwaarde komt. Dit soort analyse is nuttig om de hoeveelheid gegevens te reduceren die worden gecommuniceerd naar en worden opgeslagen in de cloud.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Deze zelfstudie laat zien hoe u een module kunt ontwikkelen in **C** met behulp van **Visual Studio 2019** en hoe u deze kunt implementeren op een **Windows-apparaat**. Als u modules voor Linux-apparaten ontwikkelt, gaat u naar [C-module ontwikkelen voor Linux-apparaten met IoT Edge](tutorial-c-module.md).

Gebruik de volgende tabel om inzicht te krijgen in de opties voor het ontwikkelen en implementeren van C-modules op Windows-apparaten:

| C | Visual Studio Code | Visual Studio 2017/2019 |
| -- | ------------------ | ------------------ |
| **Windows AMD64** |  | ![C-modules ontwikkelen voor WinAMD64 in Visual Studio](./media/tutorial-c-module/green-check.png) |

Voordat u met deze zelfstudie begint, moet u de vorige zelfstudie hebben doorlopen om uw ontwikkelomgeving voor Windows-containerontwikkeling in te stellen: [IoT Edge-modules ontwikkelen voor Windows-apparaten](tutorial-develop-for-windows.md). Als u die zelfstudie hebt voltooid, hebt u de volgende vereisten klaarstaan:

* Een gratis of standaard [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)-laag in Azure.
* Een [Windows-apparaat met Azure IoT Edge](quickstart.md).
* Een containerregister, zoals [Azure Container Registry](../container-registry/index.yml).
* [Visual Studio 2019](/visualstudio/install/install-visual-studio) geconfigureerd met de extensie [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools).
* [Docker Desktop](https://docs.docker.com/docker-for-windows/install/) geconfigureerd voor het uitvoeren van Windows-containers.
* De Azure IoT C SDK voor Windows x64 installeren via vcpkg:

   ```powershell
   git clone https://github.com/Microsoft/vcpkg
   cd vcpkg
   .\bootstrap-vcpkg.bat
   .\vcpkg install azure-iot-sdk-c:x64-windows
   .\vcpkg --triplet x64-windows integrate install
   ```

> [!TIP]
> Als u Visual Studio 2017 (versie 15.7 of hoger) gebruikt, downloadt en installeert u [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) voor Visual Studio 2017 vanuit de Visual Studio marketplace

## <a name="create-a-module-project"></a>Een moduleproject maken

Met de volgende stappen maakt u een IoT Edge-moduleproject op basis van de C SDK met behulp van Visual Studio en de Azure IoT Edge Tools-extensie. Zodra u een projectsjabloon hebt gemaakt, voegt u nieuwe code toe zodat de module berichten filtert op basis van de gerapporteerde eigenschappen.

### <a name="create-a-new-project"></a>Een nieuw project maken

Maak een C-oplossingssjabloon die u met uw eigen code kunt aanpassen.

1. Start Visual Studio 2019 en selecteer **Nieuw project maken**.

2. Zoek naar **IoT Edge** en kies het project **Azure IoT Edge (Windows amd64)** . Klik op **Volgende**.

   ![Een nieuw Azure IoT Edge-project maken](./media/tutorial-c-module-windows/new-project.png)

3. Wijzig de naam van het project en de oplossing in iets beschrijvends, zoals **CTutorialApp**. Klik op **Maken** om het project te maken.

   ![Een nieuw Azure IoT Edge-project configureren](./media/tutorial-c-module-windows/configure-project.png)

4. Configureer uw project met de volgende waarden:

   | Veld | Waarde |
   | ----- | ----- |
   | Een sjabloon selecteren | Selecteer **C-module**. |
   | Moduleprojectnaam | Geef de module de naam **CModule**. |
   | Opslagplaats voor Docker-installatiekopieën | Een opslagplaats voor afbeeldingen bevat de naam van het containerregister en de naam van uw containerafbeelding. De containerafbeelding wordt vooraf ingevuld met de naam van het moduleproject. Vervang **localhost:5000** door de waarde van de **aanmeldingsserver** uit uw Azure-containerregister. U vindt de aanmeldingsserver op de overzichtspagina van het containerregister in de Azure-portal. <br><br> De uiteindelijke opslagplaats voor de installatiekopie ziet er ongeveer als volgt uit: \<registry name\>.azurecr.io/cmodule. |

   ![Uw project configureren voor doelapparaat, moduletype en containerregister](./media/tutorial-c-module-windows/add-application-and-module.png)

5. Selecteer **Toevoegen** om het project te maken.

### <a name="add-your-registry-credentials"></a>Uw registerreferenties toevoegen

In het distributiemanifest worden de referenties voor het containerregister gedeeld met de IoT Edge-runtime. De runtime heeft deze referenties nodig om uw persoonlijke installatiekopieën naar het IoT Edge-apparaat te halen. Gebruik de referenties uit de sectie **Toegangssleutels** van uw Azure-containerregister.

1. Ga in Visual Studio naar de Solution Explorer en open het bestand **deployment.template.json**.

2. Zoek de eigenschap **registryCredentials** in de gewenste $edgeAgent-eigenschappen. Het adres van het register moet automatisch worden ingevuld op basis van de informatie die u hebt opgegeven bij het maken van het project. De velden voor gebruikersnaam en wachtwoord moeten namen van variabelen bevatten. Bijvoorbeeld:

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

1. De gegevens van de sensor in dit scenario worden in JSON-indeling aangeleverd. Als u berichten wilt filteren in een JSON-indeling, moet u een JSON-bibliotheek voor C importeren. In deze zelfstudie wordt Parson gebruikt.

   1. Download de [Parson GitHub-opslagplaats](https://github.com/kgabis/parson). Kopieer de bestanden **parson.c** en **parson.h** naar het project **CModule**.

   2. Open in Visual Studio het bestand **CMakeLists.txt** uit de map van het CModule-project. Importeer boven in het bestand de Parson-bestanden als een bibliotheek met de naam **my_parson**.

      ```txt
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. Voeg `my_parson` toe aan de lijst met bibliotheken in de sectie **target_link_libraries** van het bestand CMakeLists.txt.

   4. Sla het bestand **CMakeLists.txt** op.

   5. Open **CModule** > **main.c**. Voeg onder aan de lijst met insluitinstructies een nieuwe toe om `parson.h` voor JSON-ondersteuning op te nemen:

      ```c
      #include "parson.h"
      ```

2. Voeg in het bestand **main.c** een globale variabele toe met de naam `temperatureThreshold` naast de variabele messagesReceivedByInput1Queue. Deze variabele bepaalt de waarde die de gemeten temperatuur moet overschrijden voordat de gegevens naar IoT Hub worden verzonden.

    ```c
    static double temperatureThreshold = 25;
    ```

3. Zoek de functie `CreateMessageInstance` in main.c. Vervang de interne if-else-instructie door de volgende code waarmee een aantal regels functionaliteit wordt toegevoegd:

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

4. Zoek de functie `InputQueue1Callback` en vervang de volledige functie door de volgende code. Deze functie implementeert het feitelijke berichtenfilter. Wanneer een bericht wordt ontvangen, wordt gecontroleerd of de gerapporteerde temperatuur de drempelwaarde overschrijdt. Zo ja, dan wordt het bericht doorgestuurd via de uitvoerwachtrij. Zo nee, dan wordt het bericht genegeerd.

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

        // If temperature exceeds threshold, send to output1
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
        // If message does not exceed threshold, do not forward
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

5. Voeg een `moduleTwinCallback`-functie toe. Deze methode ontvangt updates van de gewenste eigenschappen van de moduledubbel en updatet de variabele **temperatureThreshold** naar dezelfde waarde. Alle modules hebben hun eigen moduledubbel, waardoor u rechtstreeks in de cloud de code kunt configureren die in een module wordt uitgevoerd.

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

6. Zoek de functie `SetupCallbacksForModule`. Vervang de functie door de volgende code waarmee een **else if**-instructie wordt gebruikt om te controleren of de moduledubbel is bijgewerkt.

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

7. Sla het bestand main.c op.

8. Open het bestand **deployment.template.json**.

9. Voeg de moduledubbel CModule toe aan het distributiemanifest. Voeg de volgende JSON-inhoud onder aan de sectie `moduleContent` in, na de moduledubbel `$edgeHub`:

   ```json
   "CModule": {
       "properties.desired":{
           "TemperatureThreshold":25
       }
   }
   ```

   ![Dubbele CModule toevoegen aan implementatiesjabloon](./media/tutorial-c-module-windows/module-twin.png)

10. Sla het bestand **deployment.template.json** op.

## <a name="build-and-push-your-module"></a>De module bouwen en pushen

In de vorige sectie hebt u een IoT Edge-oplossing gemaakt en code toegevoegd aan de **CModule** om berichten te filteren waarin de gemelde temperatuur van de machine onder de aanvaardbare drempelwaarde is. Nu moet u de oplossing bouwen als een containerinstallatiekopie en deze naar het containerregister pushen.

### <a name="sign-in-to-docker"></a>Aanmelden bij Docker

Geef de containerregisterreferenties op voor Docker op uw ontwikkelcomputer, zodat de containerinstallatiekopie voor opslag in het register kan worden gepusht.

1. Open PowerShell of een opdrachtprompt.

2. Meld u aan bij Docker met de Azure-containerregisterreferenties die u hebt opgeslagen nadat u het register hebt gemaakt.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Mogelijk wordt een beveiligingswaarschuwing weergegeven waarin het gebruik van `--password-stdin` wordt aanbevolen. Hoewel dit wordt aanbevolen voor productiescenario's, valt het buiten het bereik van deze zelfstudie. Zie de documentatie voor [aanmelding bij Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) voor meer informatie.

### <a name="build-and-push"></a>Bouwen en pushen

Uw ontwikkelcomputer heeft nu toegang tot uw containerregister en uw IoT Edge-apparaten. Het is tijd om de projectcode in te schakelen in een containerinstallatiekopie.

1. Klik in de Solution Explorer van Visual Studio met de rechtermuisknop op de naam van het project dat u wilt bouwen. De standaardnaam is **AzureIotEdgeApp1**. Voor deze zelfstudie is de naam **CTutorialApp** gekozen. Omdat u een Windows-module bouwt, moet de extensie **Windows.Amd64** zijn.

2. Selecteer **IoT Edge-modules bouwen en pushen**.

   Met de opdracht voor bouwen en pushen worden drie bewerkingen gestart. Eerst wordt er een nieuwe map met de naam **config** in de oplossing gemaakt die het volledige implementatiemanifest bevat, op basis van de informatie in de implementatiesjabloon en andere oplossingsbestanden. Daarna wordt `docker build` uitgevoerd om de containerinstallatiekopie te bouwen op basis van de juiste dockerfile voor uw doelarchitectuur. Vervolgens wordt `docker push` uitgevoerd om de opslagplaats van de installatiekopie naar het containerregister te pushen.

   Dit proces kan de eerste keer enkele minuten duren, maar de volgende keer dat u de opdrachten uitvoert, wordt het sneller uitgevoerd.

## <a name="deploy-modules-to-device"></a>Modules op het apparaat implementeren

Gebruik Cloud Explorer van Visual Studio en de Azure IoT Tools-extensie om het moduleproject op uw IoT Edge-apparaat te implementeren. U hebt al een implementatiemanifest voorbereid voor uw scenario, namelijk het bestand **deployment.windows-amd64.json** in de map config. U hoeft nu alleen nog maar een apparaat te selecteren dat de implementatie moet ontvangen.


Zorg ervoor dat uw IoT Edge-apparaat actief is.

1. Vouw in Cloud Explorer van Visual Studio de resources uit om de lijst met IoT-apparaten weer te geven.

2. Klik met de rechtermuisknop op de naam van het IoT Edge-apparaat waarvan u de implementatie wilt ontvangen.

3. Selecteer **Implementatie maken**.

4. Ga in de bestandsverkenner naar de configuratiemap van uw oplossing en selecteer vervolgens het bestand **deployment.windows-amd64**.

5. Vernieuw Cloud Explorer om de geïmplementeerde modules onder uw apparaat weer te geven.

## <a name="view-generated-data"></a>Gegenereerde gegevens weergeven

Als u het implementatiemanifest op uw IoT Edge-apparaat toepast, verzamelt de IoT Edge-runtime op het apparaat de informatie over de nieuwe implementatie en wordt deze uitgevoerd. Modules die worden uitgevoerd op het apparaat en die niet zijn opgenomen in het implementatiemanifest, worden gestopt. Alle modules die ontbreken op het apparaat worden gestart.

U kunt de IoT Edge Tools-extensie gebruiken om berichten weer te geven als ze arriveren in uw IoT Hub.

1. Selecteer in de Cloud Explorer van Visual Studio de naam van het IoT Edge-apparaat.

2. Selecteer in de lijst **Actions** de optie **Start Monitoring Built-in Event Endpoint**.

3. Bekijk de berichten die in uw IoT Hub arriveren. Het kan enige tijd duren voordat de berichten worden ontvangen, omdat het IoT Edge-apparaat eerst de eigen nieuwe implementatie moet ontvangen en alle modules moet starten. Vervolgens wachten de wijzigingen die we hebben aangebracht in de CModule-code totdat de computertemperatuur 25 graden bereikt voordat berichten worden verzonden. Daarnaast wordt het berichttype **Waarschuwing** toegevoegd aan berichten die de drempelwaarde voor de temperatuur bereiken.

   ![Berichten weergeven die binnenkomen op IoT Hub](./media/tutorial-c-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>De moduledubbel bewerken

We hebben de CModule-moduledubbel gebruikt om de drempelwaarde voor de temperatuur op 25 graden in te stellen. U kunt de moduledubbel gebruiken om de functionaliteit te wijzigen zonder dat u de modulecode hoeft bij te werken.

1. Open het bestand **deployment.windows-amd64.json** in Visual Studio. (Open niet het bestand deployment.template. Als u het distributiemanifest niet ziet in het configuratiebestand in de Solution Explorer, selecteert u het pictogram **Alle bestanden weergeven** in de werkbalk.)

2. Zoek de CModule-dubbel en wijzig de waarde van de parameter **temperatureThreshold** in een nieuwe temperatuur die 5 tot 10 graden hoger is dan de laatste gerapporteerde temperatuur.

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