---
title: 'Zelfstudie: C-modules voor Windows ontwikkelen met behulp van Azure IoT Edge'
description: In deze zelfstudie ziet u hoe u IoT Edge-modules in C maakt en deze implementeert op Windows-apparaten waarop IoT Edge wordt uitgevoerd.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 8f019c8f3c560fdfdc0c8e5992389c253c9b0d74
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/14/2021
ms.locfileid: "103463371"
---
# <a name="tutorial-develop-c-iot-edge-modules-using-windows-containers"></a>Zelf studie: C IoT Edge-modules ontwikkelen met behulp van Windows-containers

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

In dit artikel wordt beschreven hoe u Visual Studio kunt gebruiken om C-code te ontwikkelen en te implementeren op een Windows-apparaat waarop Azure IoT Edge wordt uitgevoerd.

>[!NOTE]
>IoT Edge 1,1 LTS is het laatste release kanaal dat ondersteuning biedt voor Windows-containers. Vanaf versie 1,2 worden Windows-containers niet ondersteund. Overweeg het gebruik of verplaatsen van [IOT Edge voor Linux in Windows](iot-edge-for-linux-on-windows.md) om IOT Edge op Windows-apparaten uit te voeren.

U kunt Azure IoT Edge-modules gebruiken voor het implementeren van code waarmee uw bedrijfslogica rechtstreeks op uw IoT Edge-apparaten wordt geïmplementeerd. In deze zelfstudie leert u een IoT Edge-module te maken die sensorgegevens filtert.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Visual Studio gebruiken om een IoT Edge-module te maken op basis van de C SDK.
> * Visual Studio en Docker gebruiken om een Docker-installatiekopie te maken en deze te publiceren naar het register.
> * De module implementeren op uw IoT Edge-apparaat.
> * Gegenereerde gegevens weergeven.

De IoT Edge-module die u maakt in deze zelfstudie filtert de temperatuurgegevens die door uw apparaat worden gegenereerd. Deze module verzendt alleen gegevens upstream als de temperatuur boven een bepaalde drempelwaarde komt. Dit soort analyse is nuttig om de hoeveelheid gegevens te reduceren die worden gecommuniceerd naar en worden opgeslagen in de cloud.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Deze zelfstudie laat zien hoe u een module kunt ontwikkelen in C met behulp van Visual Studio 2019 en hoe u deze kunt implementeren op een Windows-apparaat. Als u modules ontwikkelt met behulp van Linux-containers, gaat u in plaats daarvan naar [ontwikkel C IOT Edge-modules met Linux-containers](tutorial-csharp-module.md) .

Raadpleeg de volgende tabel voor meer informatie over de mogelijkheden voor het ontwikkelen en implementeren van C-modules met behulp van Windows-containers:

| C | Visual&nbsp;Studio&nbsp;Code | Visual Studio 2017&nbsp;en&nbsp;2019 |
| -- | ------------------ | :------------------: |
| Windows AMD64 |  | ![C-modules ontwikkelen voor WinAMD64 in Visual Studio](./media/tutorial-c-module/green-check.png) |

Voordat u met deze zelf studie begint, moet u uw ontwikkel omgeving instellen door de instructies in de zelf studie [IOT Edge-modules ontwikkelen met behulp van Windows-containers](tutorial-develop-for-windows.md) te volgen. Nadat u deze hebt voltooid, bevat uw omgeving de volgende vereisten:

* Een gratis of standaard [IoT-hub](../iot-hub/iot-hub-create-through-portal.md)-laag in Azure.
* Een [Windows-apparaat met Azure IoT Edge](how-to-install-iot-edge-windows-on-windows.md).
* Een containerregister, zoals [Azure Container Registry](../container-registry/index.yml).
* [Visual Studio 2019](/visualstudio/install/install-visual-studio), geconfigureerd met de extensie [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools).
* [Docker Desktop](https://docs.docker.com/docker-for-windows/install/), geconfigureerd voor het uitvoeren van Windows-containers.

Installeer de Azure IoT C SDK voor Windows x64 via vcpkg door de volgende opdrachten uit te voeren:

   ```powershell
   git clone https://github.com/Microsoft/vcpkg
   cd vcpkg
   .\bootstrap-vcpkg.bat
   .\vcpkg install azure-iot-sdk-c:x64-windows
   .\vcpkg --triplet x64-windows integrate install
   ```

> [!TIP]
> Als u Visual Studio 2017 (versie 15.7 of nieuwer) gebruikt, downloadt u Azure IoT Edge Tools voor Visual Studio 2017 van [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) en installeert u het.

## <a name="create-a-module-project"></a>Een moduleproject maken

In deze sectie maakt u een IoT Edge-moduleproject op basis van de C SDK met behulp van Visual Studio en de Azure IoT Edge Tools-extensie. Nadat u een projectsjabloon hebt gemaakt, voegt u nieuwe code toe zodat de module berichten filtert op basis van de gerapporteerde eigenschappen.

### <a name="create-a-new-project"></a>Een nieuw project maken

Maak een C-oplossingssjabloon die u met uw eigen code kunt aanpassen.

1. Open Visual Studio 2019 en selecteer **Nieuw project maken**.

1. Zoek in het deelvenster **Nieuw project maken** naar **IoT Edge**. Selecteer vervolgens in de resultatenlijst het project **Azure IOT Edge (Windows amd64)** .

   ![Schermopname van het IoT Edge-deelvenster Nieuw project maken.](./media/tutorial-c-module-windows/new-project.png)

1. Selecteer **Next**.

    Het deelvenster **Uw nieuwe project configureren** wordt geopend.

   ![Schermopname van het deelvenster Uw nieuwe project configureren.](./media/tutorial-c-module-windows/configure-project.png)

1. In het deelvenster **Uw nieuwe project configureren** wijzigt u de naam van het project en de oplossing in iets beschrijvends, bijvoorbeeld **CTutorialApp**. 

1. Selecteer **Maken** om het project te maken.

   Het deelvenster **Module toevoegen** wordt geopend.

   ![Schermopname van het deelvenster Module toevoegen voor het configureren van uw project.](./media/tutorial-c-module-windows/add-application-and-module.png)

1. Op de pagina **Uw nieuwe project configureren** gaat u als volgt te werk:

   a. Selecteer in het linkerdeelvenster de **C Module**-sjabloon.  
   b. Voer in het vak **Naam van module** **CModule** in.  
   c. Ga naar het vak **URL van opslagplaats**. Vervang **localhost:5000** door de waarde van de **aanmeldingsserver** uit uw Azure-containerregister. Volg hierbij het volgende indeling: `<registry name>.azurecr.io/cmodule`

    > [!NOTE]
    > Een opslagplaats voor installatiekopieën bevat de naam van het containerregister en de naam van uw containerinstallatiekopie. De containerinstallatiekopie wordt vooraf ingevuld met de waarde van de naam van het moduleproject.  U vindt de aanmeldingsserver op de overzichtspagina van het containerregister in Azure Portal.

1. Selecteer **Toevoegen** om het project te maken.

### <a name="add-your-registry-credentials"></a>Uw registerreferenties toevoegen

In het distributiemanifest worden de referenties voor het containerregister gedeeld met de IoT Edge-runtime. De runtime heeft deze referenties nodig om uw persoonlijke installatiekopieën naar het IoT Edge-apparaat te halen. Gebruik de referenties uit de sectie **Toegangssleutels** van uw Azure-containerregister.

1. Open het bestand *deployment.template.json* in Visual Studio Solution Explorer.

1. Zoek de eigenschap **registryCredentials** in de gewenste $edgeAgent-eigenschappen. Het registeradres van de eigenschap moet automatisch worden gevuld met de informatie die u hebt ingevoerd tijdens het maken van het project. De velden voor gebruikersnaam en wachtwoord moeten namen van variabelen bevatten. Bijvoorbeeld:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

1. Open het omgevingsbestand (ENV) in uw moduleoplossing. Het bestand is standaard verborgen in Solution Explorer, dus u moet mogelijk de knop **Alle bestanden weergeven** selecteren om het bestand weer te geven. Het ENV-bestand moet dezelfde variabelen voor de gebruikersnaam en het wachtwoord bevatten als het bestand *deployment.template.json*.

1. Voeg de waarden voor de **gebruikersnaam** en het **wachtwoord** toe uit het Azure-containerregister.

1. Sla uw wijzigingen in het ENV-bestand op.

### <a name="update-the-module-with-custom-code"></a>De module bijwerken met aangepaste code

De standaardmodulecode ontvangt berichten in een invoerwachtrij en geeft deze door aan een uitvoerwachtrij. Er wordt nog meer code toegevoegd, zodat de module de berichten aan de rand verwerkt voordat deze naar uw IoT-hub worden doorgestuurd. Werk de module bij, zodat deze de temperatuurgegevens in elk bericht analyseert en het bericht naar de IoT-hub verzendt als de temperatuur een bepaalde drempelwaarde overschrijdt.

1. De gegevens van de sensor in dit scenario worden in JSON-indeling aangeleverd. Als u berichten wilt filteren in een JSON-indeling, moet u een JSON-bibliotheek voor C importeren. In deze zelfstudie wordt Parson gebruikt.

   a. Download de [Parson GitHub-opslagplaats](https://github.com/kgabis/parson).  
   b. Kopieer de bestanden *parson.c* en *parson.h* naar het CModule-project.  
   c. Open in Visual Studio het bestand *CMakeLists.txt* uit de map van het CModule-project.  
   d. Importeer boven in het bestand de Parson-bestanden als een bibliotheek met de naam **my_parson**.

      ```txt
      add_library(my_parson
          parson.c
          parson.h
      )
      ```  
   e. Voeg `my_parson` toe aan de lijst met bibliotheken in de sectie target_link_libraries van het bestand *CMakeLists.txt*.  
   f. Sla het bestand *CMakeLists.txt* op.  
   g. Selecteer **CModule** > **main.c**. Voeg onder aan de lijst met insluitinstructies een nieuwe instructie toe om `parson.h` voor JSON-ondersteuning op te nemen:

      ```c
      #include "parson.h"
      ```

1. Voeg in het bestand *main.c*, naast de variabele `messagesReceivedByInput1Queue`, een globale variabele toe met de naam `temperatureThreshold`. Deze variabele bepaalt de waarde die de gemeten temperatuur moet overschrijden voordat de gegevens naar uw IoT-hub worden verzonden.

    ```c
    static double temperatureThreshold = 25;
    ```

1. Zoek de functie `CreateMessageInstance` in *main.c*. Vervang de interne *if-else*-instructie door de volgende code, waardoor een aantal regels functionaliteit wordt toegevoegd:

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

   Met de nieuwe regels code in de *else*-instructie wordt een nieuwe eigenschap toe aan het bericht toegevoegd, waardoor het bericht als een waarschuwing wordt gelabeld. Met deze code worden alle berichten als waarschuwingen gelabeld, omdat er functionaliteit wordt toegevoegd waarmee berichten alleen naar de IoT-hub worden verzonden als er hoge temperaturen worden gerapporteerd.

1. Zoek de functie `InputQueue1Callback` en vervang de volledige functie door de volgende code. Deze functie implementeert het feitelijke berichtenfilter. Wanneer een bericht wordt ontvangen, wordt gecontroleerd of de gerapporteerde temperatuur de drempelwaarde overschrijdt. Als de temperatuur de drempelwaarde overschrijdt, wordt het bericht via de uitvoerwachtrij doorgestuurd. Als de drempelwaarde niet wordt overschreden, wordt het bericht genegeerd.

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

        // Check whether the message reports temperatures that exceed the threshold
        JSON_Value *root_value = json_parse_string(messageBody);
        JSON_Object *root_object = json_value_get_object(root_value);
        double temperature;

        // If temperature exceeds the threshold, send to output1
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
        // If message does not exceed the threshold, do not forward
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

1. Voeg een `moduleTwinCallback`-functie toe. Deze methode ontvangt updates van de gewenste eigenschappen van de moduledubbel en updatet de variabele **temperatureThreshold** naar dezelfde waarde. Alle modules hebben hun eigen moduledubbel, waardoor u rechtstreeks in de cloud de code kunt configureren die in de module wordt uitgevoerd.

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

1. Zoek de functie `SetupCallbacksForModule`. Vervang de functie door de volgende code, waarin een *else-if*-instructie wordt gebruikt om te controleren of de moduledubbel is bijgewerkt.

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

1. Sla het bestand *main.c* op.

1. Open het bestand *deployment.template.json*.

1. Voeg de moduledubbel **CModule** toe aan het distributiemanifest. Voeg de volgende JSON-inhoud onder aan de sectie `moduleContent` in, na de moduledubbel `$edgeHub`:

   ```json
   "CModule": {
       "properties.desired":{
           "TemperatureThreshold":25
       }
   }
   ```

   ![Schermopname van de moduledubbel die wordt toegevoegd aan de implementatiesjabloon.](./media/tutorial-c-module-windows/module-twin.png)

1. Sla het bestand *deployment.template.json* op.

## <a name="build-and-push-your-module"></a>De module bouwen en pushen

In de vorige sectie hebt u een IoT Edge-oplossing gemaakt en code toegevoegd aan **CModule** om berichten te filteren waarin de gemelde temperatuur van de machine onder de aanvaardbare drempelwaarde is. Nu moet u de oplossing bouwen als een containerinstallatiekopie en deze naar het containerregister pushen.

### <a name="sign-in-to-docker"></a>Aanmelden bij Docker

Geef de containerregisterreferenties op voor Docker op uw ontwikkelcomputer, zodat de containerinstallatiekopie voor opslag in het register kan worden gepusht.

1. Open PowerShell of een opdrachtpromptvenster.

1. Meld u aan bij Docker met de referenties voor het Azure-containerregister die u hebt opgeslagen nadat u het register hebt gemaakt.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Mogelijk wordt een beveiligingswaarschuwing weergegeven waarin het gebruik van `--password-stdin` wordt aanbevolen. Hoewel dit voor productiescenario's als best practice wordt aanbevolen, valt het buiten het bereik van deze zelfstudie. Zie de documentatie voor [aanmelding bij Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) voor meer informatie.

### <a name="build-and-push"></a>Bouwen en pushen

Uw ontwikkelcomputer heeft nu toegang tot uw containerregister en uw IoT Edge-apparaten. Het is tijd om de projectcode in te schakelen in een containerinstallatiekopie.

1. Klik in Visual Studio Solution Explorer met de rechtermuisknop op de naam van het project dat u wilt bouwen. De standaardnaam is **AzureIotEdgeApp1**. In deze zelfstudie wordt de naam **CTutorialApp** gekozen. En omdat u een Windows-module bouwt, moet de extensie **Windows.Amd64** zijn.

1. Selecteer **IoT Edge-modules bouwen en pushen**.

   Met de opdracht voor bouwen en pushen worden drie bewerkingen gestart:
   * Eerst wordt een nieuwe map in de oplossing gemaakt met de naam *config*. Deze map bevat het volledige distributiemanifest. Het is gebaseerd op informatie in de implementatiesjabloon en andere oplossingsbestanden. 
   * Daarna wordt `docker build` uitgevoerd om de containerinstallatiekopie te bouwen. Dit gebeurt op basis van de juiste Dockerfile voor uw doelarchitectuur. 
   * Vervolgens wordt `docker push` uitgevoerd om de opslagplaats van de installatiekopie naar het containerregister te pushen.

   Dit proces kan de eerste keer enkele minuten duren, maar zal sneller gaan wanneer u de opdrachten de volgende keer uitvoert.

## <a name="deploy-modules-to-the-device"></a>Modules op het apparaat implementeren

Gebruik Cloud Explorer van Visual Studio en de Azure IoT Tools-extensie om het moduleproject op uw IoT Edge-apparaat te implementeren. U hebt al een implementatiemanifest voorbereid voor uw scenario, namelijk het bestand *deployment.windows-amd64.json* in de map *config*. U hoeft nu alleen nog maar een apparaat te selecteren dat de implementatie moet ontvangen.

Zorg ervoor dat uw IoT Edge-apparaat actief is.

1. Vouw in Visual Studio Cloud Explorer de resources uit om de lijst met IoT-apparaten te bekijken.

1. Klik met de rechtermuisknop op de naam van het IoT Edge-apparaat waarvan u de implementatie wilt ontvangen.

1. Selecteer **Implementatie maken**.

1. Ga in Visual Studio File Explorer naar de map *config* van uw oplossing. Selecteer hierin het bestand *deployment.windows-amd64.json*.

1. Vernieuw Cloud Explorer om de geïmplementeerde modules weer te geven die onder uw apparaat vermeld staan.

## <a name="view-generated-data"></a>Gegenereerde gegevens weergeven

Nadat u het distributiemanifest op uw IoT Edge-apparaat hebt toegepast, verzamelt de IoT Edge-runtime op het apparaat de informatie over de nieuwe implementatie en wordt deze uitgevoerd. Modules die worden uitgevoerd op het apparaat, maar die niet zijn opgenomen in het distributiemanifest, worden gestopt. Modules die op het apparaat ontbreken, worden gestart.

U kunt de IoT Edge Tools-extensie gebruiken om berichten weer te geven terwijl ze arriveren in uw IoT-hub.

1. Selecteer in Cloud Explorer van Visual Studio de naam van het IoT Edge-apparaat.

1. Selecteer in de lijst **Actions** de optie **Start Monitoring Built-in Event Endpoint**.

1. Bekijk de berichten die in uw IoT-hub arriveren. Het kan enige tijd duren voordat de berichten worden ontvangen, omdat het IoT Edge-apparaat eerst de eigen nieuwe implementatie moet ontvangen en alle modules moet starten. De wijzigingen die zijn aangebracht in de CModule-code moet wachten totdat de machinetemperatuur de 25 graden bereikt. Pas daarna kunnen de berichten worden verzonden. Daarnaast wordt met de code het berichttype **Waarschuwing** aan berichten toegevoegd die de drempelwaarde voor de temperatuur bereiken.

   ![Schermopname van het uitvoervenster waarin berichten worden weergegeven die arriveren bij de IoT-hub.](./media/tutorial-c-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>De moduledubbel bewerken

U hebt de CModule-moduledubbel gebruikt om de drempelwaarde voor de temperatuur op 25 graden in te stellen. U kunt de moduledubbel gebruiken om de functionaliteit te wijzigen zonder dat u de modulecode hoeft bij te werken.

1. Open het bestand *deployment.windows-amd64.json* in Visual Studio. 

   Open *niet* het bestand *deployment.template*. Als u in Solution Explorer het distributiemanifest niet ziet in het bestand *config*, selecteert u het pictogram **Alle bestanden weergeven** in de Solution Explorer-werkbalk.

1. Zoek de CModule-dubbel en wijzig de waarde van de parameter **temperatureThreshold** in een nieuwe temperatuur die 5 tot 10 graden hoger is dan de laatste gerapporteerde temperatuur.

1. Sla het bestand *deployment.windows-amd64.json* op.

1. Volg de implementatie-instructies opnieuw om het bijgewerkte distributiemanifest op uw apparaat toe te passen.

1. Bewaak de binnenkomende apparaat-naar-cloud-berichten. De berichten zouden moeten stoppen totdat de nieuwe temperatuurdrempel is bereikt.

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent door te gaan met het volgende aanbevolen artikel, kunt u de resources en configuraties die u in deze zelfstudie hebt gemaakt, bewaren en opnieuw gebruiken. U kunt ook hetzelfde IoT Edge-apparaat blijven gebruiken als een testapparaat.

Anders kunt u, om kosten te vermijden, de lokale configuraties en Azure-resources verwijderen die u hier hebt gebruikt.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een IoT Edge-module gemaakt met code voor het filteren van onbewerkte gegevens die worden gegenereerd door uw IoT Edge-apparaat.

U kunt doorgaan met de volgende zelfstudies om te leren hoe Azure IoT Edge u kan helpen bij de implementatie van Azure-cloudservices voor het verwerken en analyseren van gegevens aan de rand.

> [!div class="nextstepaction"]
> [Azure Functions](tutorial-deploy-function.md)
> [Azure Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Azure Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision-service](tutorial-deploy-custom-vision.md)
