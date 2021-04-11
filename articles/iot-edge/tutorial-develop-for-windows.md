---
title: 'Zelfstudie: een module ontwikkelen voor Windows-apparaten met behulp van Azure IoT Edge'
description: In deze zelfstudie wordt uitgelegd hoe u uw ontwikkelcomputer en cloudresources instelt om IoT Edge modules te ontwikkelen met Windows-containers voor Windows-apparaten
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
monikerRange: =iotedge-2018-06
ms.openlocfilehash: a2d8a215dfc34073783a5d8a6233ec8dbae4ec17
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106218869"
---
# <a name="tutorial-develop-iot-edge-modules-using-windows-containers"></a>Zelf studie: IoT Edge-modules ontwikkelen met behulp van Windows-containers

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Visual Studio gebruiken voor het ontwikkelen en implementeren van code voor Windows-apparaten met IoT Edge.

>[!NOTE]
>IoT Edge 1,1 LTS is het laatste release kanaal dat ondersteuning biedt voor Windows-containers. Vanaf versie 1,2 worden Windows-containers niet ondersteund. Overweeg het gebruik of verplaatsen van [IOT Edge voor Linux in Windows](iot-edge-for-linux-on-windows.md) om IOT Edge op Windows-apparaten uit te voeren.

In deze zelfstudie wordt uitgelegd wat er nodig is om uw eigen code te ontwikkelen en te implementeren op een IoT Edge-apparaat. Het is handig deze zelfstudie door te nemen voor de andere zelfstudies, die dieper ingaan op specifieke programmeertalen of Azure-services.

In deze zelfstudie wordt gebruikgemaakt van het voorbeeld van de implementatie van een **C#-module op een Windows-apparaat**. Dit voorbeeld is gekozen omdat dit het meest voorkomende ontwikkelscenario is. Als u in een andere taal wilt ontwikkelen of als u Azure-services wilt implementeren als modules, is deze zelfstudie eveneens nuttig voor meer informatie over de hulpprogramma's voor ontwikkeling. Wanneer u de ontwikkelconcepten begrijpt, kunt u de gewenste taal of Azure-service kiezen voor meer informatie.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Uw ontwikkelcomputer instellen.
> * De IoT Edge Tools voor Visual Studio gebruiken om een nieuw project te maken.
> * Uw project bouwen als een container en het opslaan in een Azure-containerregister.
> * Uw code implementeren op een IoT Edge-apparaat.

## <a name="prerequisites"></a>Vereisten

Een ontwikkelcomputer:

* Windows 10 met update 1809 of hoger.
* U kunt uw eigen computer of een virtuele machine gebruiken, afhankelijk van uw ontwikkelvoorkeuren.
  * Zorg ervoor dat uw ontwikkelcomputer ondersteuning biedt voor geneste virtualisatie. Deze mogelijkheid is nodig voor het uitvoeren van een container-engine, die u in de volgende sectie installeert.
* Installeer [Git](https://git-scm.com/).

Een Azure IoT Edge-apparaat op Windows:

* [Installeer en beheer Azure IOT Edge met Windows-containers](how-to-install-iot-edge-windows-on-windows.md).
* Het is raadzaam om IoT Edge niet uit te voeren op uw ontwikkel computer, maar als dat mogelijk is, gebruikt u een afzonderlijk apparaat. Dit onderscheid tussen ontwikkelcomputer en IoT Edge apparaat sluit nauwer aan op een echt implementatiescenario en helpt om de verschillende concepten uit elkaar te houden.

Cloudresources:

* Een gratis of standaard [IoT-hub](../iot-hub/iot-hub-create-through-portal.md)-laag in Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="key-concepts"></a>Belangrijkste concepten

In deze zelfstudie doorloopt u de ontwikkeling van een IoT Edge-module. Een *IoT Edge-module*, of soms kortweg *module* genoemd, is een container die uitvoerbare code bevat. U kunt een of meer modules implementeren op een IoT Edge-apparaat. Modules voeren specifieke taken uit, zoals het opnemen van gegevens van sensoren, het uitvoeren van gegevensanalyses of gegevensopschoning, of het verzenden van berichten naar een IoT-hub. Zie [Informatie over Azure IoT Edge-modules](iot-edge-modules.md) voor meer informatie.

Bij het ontwikkelen van IoT Edge-modules is het belangrijk dat u begrijpt wat het verschil is tussen de ontwikkelcomputer en het IoT Edge doelapparaat waarop de module uiteindelijk zal worden geïmplementeerd. De container die u voor de modulecode bouwt, moet overeenkomen met het besturingssysteem van het *doelapparaat*. Voor de ontwikkeling van Windows-containers is dit concept eenvoudiger omdat Windows-containers alleen worden uitgevoerd op Windows-besturingssystemen. Maar u kunt bijvoorbeeld uw Windows-ontwikkelcomputer gebruiken om modules voor Linux IoT Edge-apparaten te bouwen. In dat scenario moet u ervoor zorgen dat er Linux-containers op uw ontwikkelcomputer werden uitgevoerd. Als u deze zelfstudie doorloopt, moet u rekening houden met het verschil tussen het *besturingssysteem van de ontwikkelcomputer* en het *besturingssysteem van de container*.

Deze zelfstudie richt zich op Windows-apparaten met IoT Edge. Windows-IoT Edge-apparaten gebruiken Windows-containers. We raden u aan om Visual Studio te gebruiken voor het ontwikkelen voor Windows-apparaten, en dat wordt dan ook in deze zelfstudie gebruikt. U kunt ook Visual Studio Code gebruiken, maar er zijn wel verschillen in ondersteuning tussen de twee hulpprogramma's.

De volgende tabel bevat een overzicht van de ondersteunde ontwikkelscenario's voor **Windows-containers** in Visual Studio Code en Visual Studio.

|   | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Azure-services** | Azure Functions <br> Azure Stream Analytics |   |
| **Talen** | C# (foutopsporing wordt niet ondersteund) | C <br> C# |
| **Meer informatie** | [Azure IoT Edge voor Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Azure IoT Edge Tools voor Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)<br>[Azure IoT Edge Tools voor Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

## <a name="install-container-engine"></a>Container-engine installeren

IoT Edge-modules worden verpakt als container. U hebt dus een container-engine op uw ontwikkelcomputer nodig om de containers te bouwen en te beheren. Het is raadzaam Docker Desktop te gebruiken voor ontwikkeling vanwege de vele functies en populariteit als een container-engine. Met Docker Desktop op een Windows-computer kunt u schakelen tussen Linux-containers en Windows-containers, en kunt u dus eenvoudig modules ontwikkelen voor verschillende soorten IoT Edge apparaten.

Gebruik de Docker-documentatie om Docker Desktop te installeren op uw ontwikkelcomputer:

* [Docker Desktop voor Windows installeren](https://docs.docker.com/docker-for-windows/install/)

  * Wanneer u Docker Desktop voor Windows installeert, wordt u gevraagd of u Linux- of Windows-containers wilt gebruiken. Voor deze zelfstudie gebruikt u **Windows-containers**. Zie [Schakelen tussen Windows- en Linux-containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) voor meer informatie.

## <a name="set-up-visual-studio-and-tools"></a>Visual Studio en hulpprogramma's instellen

De IoT-extensies voor Visual Studio helpen u bij het ontwikkelen van IoT Edge-modules. Deze extensies bieden projectsjablonen, automatiseren het maken van het implementatiemanifest en bieden u de mogelijkheid om IoT Edge apparaten te bewaken en te beheren. In deze sectie installeert u Visual Studio en de IoT-extensie. Vervolgens stelt u uw Azure-account in voor het beheren van IoT Hub-resources vanuit Visual Studio.

In deze zelfstudie leert u de ontwikkelstappen voor Visual Studio 2019. Als u Visual Studio 2017 (versie 15.7 of hoger) gebruikt, zijn de stappen vergelijkbaar. Als u liever Visual Studio Code gebruikt, raadpleegt u de instructies in [Visual Studio Code gebruiken voor het ontwikkelen van en opsporen van fouten in modules voor Azure IoT Edge](how-to-vs-code-develop-module.md).

1. Bereid Visual Studio 2019 voor op uw ontwikkelcomputer.

   * Als u Visual Studio nog niet op uw ontwikkelcomputer hebt, [installeert u Visual Studio 2019](/visualstudio/install/install-visual-studio) met de volgende workloads:

      * Azure-ontwikkeling
      * Desktopontwikkeling met C++
      * Platformoverschrijdende ontwikkelmogelijkheden van .NET Core

   * Als u Visual Studio 2019 al hebt geïnstalleerd op uw ontwikkelcomputer, volgt u de stappen in [Visual Studio aanpassen](/visualstudio/install/modify-visual-studio) om de vereiste workloads toe te voegen.

2. Download en installeer de [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools)-extensie voor Visual Studio 2019.

   Als u Visual Studio 2017 (versie 15.7 of hoger) gebruikt, downloadt en installeert u de [Azure IoT Edge Tools voor Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools).

3. Wanneer de installatie is voltooid, opent u Visual Studio 2019 en selecteert u **Continue without code**.

4. Selecteer **View** > **Cloud Explorer**.

5. Selecteer het profielpictogram in de cloudverkenner en meld u aan bij uw Azure-account als u zich nog niet hebt aangemeld.

6. Als u zich hebt aangemeld, worden uw Azure-abonnementen weergegeven. Vouw het abonnement uit dat uw IoT-hub bevat.

7. Vouw onder uw abonnement **IoT-hubs** en vervolgens uw IoT-hub uit. U ziet een lijst met uw IoT-apparaten en kunt deze verkenner gebruiken om ze te beheren.

   ![Toegang tot IoT Hub-resources in Cloud Explorer](./media/tutorial-develop-for-windows/cloud-explorer-view-hub.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Een nieuw moduleproject maken

De Azure IoT Edge Tools-extensie biedt projectsjablonen voor alle ondersteunde IoT Edge-moduletalen in Visual Studio. Deze sjablonen bevatten alle bestanden en code die u nodig hebt om een werkende module te implementeren om IoT Edge te testen of bieden u een uitgangspunt om de sjabloon aan te passen met uw eigen bedrijfslogica.

1. Selecteer **Bestand** > **Nieuw** > **Project...**

2. Zoek in het venster voor het nieuwe project naar **IoT Edge** en kies het project **Azure IoT Edge (Windows amd64)** . Klik op **Volgende**.

   ![Een nieuw Azure IoT Edge-project maken](./media/tutorial-develop-for-windows/new-project.png)

3. Wijzig in het venster voor het configureren van uw nieuwe project de naam van het project en de oplossing in iets beschrijvends, zoals **CSharpTutorialApp**. Klik op **Maken** om het project te maken.

   ![Een nieuw Azure IoT Edge-project configureren](./media/tutorial-develop-for-windows/configure-project.png)

4. Configureer in het venster Module toevoegen het project met de volgende waarden:

   | Veld | Waarde |
   | ----- | ----- |
   | Visual Studio-sjabloon | Selecteer **C#-module**. |
   | Modulenaam | Accepteer de standaardwaarde **IotEdgeModule1**. |
   | URL van opslagplaats | Een opslagplaats voor afbeeldingen bevat de naam van het containerregister en de naam van uw containerafbeelding. De containerafbeelding wordt vooraf ingevuld met de naam van het moduleproject. Vervang **localhost:5000** door de waarde van de **aanmeldingsserver** uit uw Azure-containerregister. U vindt de waarde van de aanmeldingsserver op de overzichtspagina van het containerregister in de Azure-portal. <br><br> De uiteindelijke opslagplaats voor de installatiekopie ziet er ongeveer als volgt uit: \<registry name\>.azurecr.io/iotedgemodule1. |

      ![Uw project configureren voor doelapparaat, moduletype en containerregister](./media/tutorial-develop-for-windows/add-module-to-solution.png)

5. Selecteer **Toevoegen** om de module te maken.

Zodra uw nieuwe project in het Visual Studio-venster is geladen, neemt u even de tijd om vertrouwd te raken met de bestanden die zijn gemaakt:

* Een IoT Edge-project met de naam **CSharpTutorialApp**.
  * De map **Modules** bevat aanwijzers naar de modules die in het project zijn opgenomen. In dit geval moet dit alleen IotEdgeModule1 zijn.
  * Het verborgen bestand **.env** bevat de referenties voor uw containerregister. Deze referenties worden gedeeld met uw IoT Edge-apparaat, zodat het de containerinstallatiekopieën kan ophalen.
  * Het bestand **deployment.template.json** is een sjabloon waarmee u een implementatiemanifest kunt maken. Een *implementatiemanifest* is een bestand dat precies aangeeft welke modules u wilt implementeren op een apparaat, hoe ze moeten worden geconfigureerd en hoe ze met elkaar en de cloud kunnen communiceren.
    > [!TIP]
    > In de sectie met registerreferenties wordt het adres automatisch ingevuld op basis van de informatie die u hebt ingevoerd tijdens het maken van de oplossing. De gebruikersnaam en het wachtwoord verwijzen echter naar variabelen die zijn opgeslagen in het .env-bestand. Dit is om beveiligingsredenen, omdat het .env-bestand door Git wordt genegeerd, maar de implementatiesjabloon niet.
* Een IoT Edge-moduleproject met de naam **IotEdgeModule1**.
  * Het bestand **program.cs** bevat de C#-standaardmodulecode die wordt geleverd bij de projectsjabloon. De standaardmodule haalt invoer uit een bron en geeft deze door aan IoT Hub.
  * De module **.json** bevat details over de module, met inbegrip van de volledige opslagplaats voor de installatiekopie, de installatiekopieversie en de Dockerfile die u voor elk ondersteund platform wilt gebruiken.

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Uw registerreferenties voor de IoT Edge-agent opgeven

De IoT Edge-runtime heeft uw registerreferenties nodig om uw containerinstallatiekopieën naar het IoT Edge-apparaat te halen. De IoT Edge-extensie probeert uw containerregisterinformatie van Azure op te halen en deze in de implementatiesjabloon in te vullen.

1. Open het bestand **deployment.template.json** in uw moduleoplossing.

1. Zoek de eigenschap **registryCredentials** in de gewenste $edgeAgent-eigenschappen. Het adres van het register moet automatisch worden ingevuld op basis van de informatie die u hebt opgegeven bij het maken van het project. De velden voor de gebruikersnaam en het wachtwoord moeten namen van variabelen bevatten. Bijvoorbeeld:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

1. Open het **.env**-bestand in uw moduleoplossing. (Het is standaard verborgen in de Solution Explorer, dus u moet mogelijk de knop **Alle bestanden weergeven** selecteren om het weer te geven.)

1. Voeg de waarden voor **gebruikersnaam** en **wachtwoord** toe die u hebt gekopieerd uit het Azure-containerregister.

1. Sla de wijzigingen in het .env-bestand op.

### <a name="review-the-sample-code"></a>De voorbeeldcode bekijken

De oplossingssjabloon die u hebt gemaakt, bevat voorbeeldcode voor een IoT Edge-module. In deze voorbeeldmodule worden berichten eenvoudigweg ontvangen en vervolgens doorgegeven. Met de pijplijnfunctionaliteit wordt een belangrijk concept in IoT Edge gedemonstreerd, waarmee modules met elkaar communiceren.

Elke module kan meerdere *invoer*- en *uitvoer* wachtrijen hebben die in hun code zijn gedeclareerd. Met de IoT Edge-hub die op het apparaat wordt uitgevoerd, worden berichten van de uitvoer van een module naar de invoer van een of meer modules gerouteerd. De specifieke code voor het declareren van invoer en uitvoer verschilt per taal, maar het concept is voor alle modules hetzelfde. Zie [Routes declareren](module-composition.md#declare-routes) voor meer informatie over routering tussen modules.

De C#-voorbeeldcode die bij de projectsjabloon hoort, maakt gebruik van de [ModuleClient-klasse](/dotnet/api/microsoft.azure.devices.client.moduleclient) van de IoT Hub SDK voor .NET.

1. Zoek in het bestand **program.cs** de methode **SetInputMessageHandlerAsync** op.

2. Met de methode [SetInputMessageHandlerAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync) wordt een invoerwachtrij ingesteld voor het ontvangen van inkomende berichten. Bekijk deze methode en zie hoe hiermee een invoerwachtrij met de naam **input1** wordt geïnitialiseerd.

   ![De invoernaam zoeken in de SetInputMessageHandlserAsync-constructor](./media/tutorial-develop-for-windows/declare-input-queue.png)

3. Zoek vervolgens de methode **SendEventAsync**.

4. Met de methode [SendEventAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync) worden ontvangen berichten verwerkt en wordt een uitvoerwachtrij ingesteld om de berichten door te geven. Bekijk deze methode en controleer of er een uitvoerwachtrij met de naam **output1** wordt geïnitialiseerd.

   ![De uitvoernaam zoeken in de SendEventAsync-constructor](./media/tutorial-develop-for-windows/declare-output-queue.png)

5. Open het bestand **deployment.template.json**.

6. Zoek de eigenschap **modules** van de gewenste eigenschappen van $edgeAgent.

   Er moeten hier twee modules worden weergegeven. Een is de **SimulatedTemperatureSensor**-module, die standaard is opgenomen in alle sjablonen om gesimuleerde temperatuurgegevens te bieden die u kunt gebruiken om uw modules te testen. De ander is de **IotEdgeModule1-** module die u hebt gemaakt als onderdeel van dit project.

   Deze module-eigenschap geeft aan welke modules moeten worden opgenomen in de implementatie op uw apparaat of apparaten.

7. Zoek de eigenschap **routes** van de gewenste eigenschappen van $edgeHub.

   Een van de functies van de IoT Edge hub-module bestaat uit het routeren van berichten tussen alle modules in een implementatie. Bekijk de waarden in de eigenschap routes. Eén route, **IotEdgeModule1ToIoTHub**, gebruikt een jokerteken (* *\** _) om elk bericht op te nemen dat afkomstig is van een uitvoerwachtrij in de IotEdgeModule1-module. Deze berichten gaan naar _$upstream*, een gereserveerde naam die IoT Hub aangeeft. De tweede route, **sensorToIotEdgeModule1**, neemt berichten over van de SimulatedTemperatureSensor-module en stuurt deze door naar de invoerwachtrij *input1* van de IotEdgeModule1-module.

   ![Routes bekijken in deployment.template.json](./media/tutorial-develop-for-windows/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>De oplossing bouwen en pushen

U hebt de modulecode en de implementatiesjabloon gecontroleerd om inzicht te krijgen in enkele belangrijke concepten van de implementatie. Nu moet u de IotEdgeModule1-containerinstallatiekopie bouwen en naar het containerregister pushen. Met de extensie voor IoT-hulpprogramma's voor Visual Studio worden in deze stap ook het implementatiemanifest op basis van de informatie in het sjabloonbestand en de modulegegevens van de oplossingsbestanden gegenereerd.

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

1. Klik met de rechtermuisknop op de projectmap **CSharpTutorialApp** en selecteer **IoT Edge-modules bouwen en pushen**.

   ![IoT Edge-modules bouwen en pushen](./media/tutorial-develop-for-windows/build-and-push-modules.png)

   Met de opdracht voor bouwen en pushen worden drie bewerkingen gestart. Eerst wordt er een nieuwe map met de naam **config** in de oplossing gemaakt die het volledige implementatiemanifest bevat, op basis van de informatie in de implementatiesjabloon en andere oplossingsbestanden. Daarna wordt `docker build` uitgevoerd om de containerinstallatiekopie te bouwen op basis van de juiste dockerfile voor uw doelarchitectuur. Vervolgens wordt `docker push` uitgevoerd om de opslagplaats van de installatiekopie naar het containerregister te pushen.

   Dit proces kan de eerste keer enkele minuten duren, maar de volgende keer dat u de opdrachten uitvoert, wordt het sneller uitgevoerd.

2. Open het bestand **deployment.windows-amd64.json** in de zojuist gemaakte configuratiemap. (De configuratiemap wordt mogelijk niet weergegeven in de Solution Explorer in Visual Studio. Als dat het geval is, selecteert u het pictogram **Alle bestanden weergeven** op de taakbalk van Solution Explorer.)

3. Zoek de parameter **image** van de IotEdgeModule1-sectie. De installatiekopie bevat de volledige installatiekopieopslagplaats met de naam, versie en architectuurtag van het module.json-bestand.

4. Open het bestand **module.json** in de map IotEdgeModule1.

5. Wijzig het versienummer voor de installatiekopie van de module. (De versie, niet de $schema-version.) Verhoog bijvoorbeeld het versienummer van de patch naar **0.0.2**, alsof er een kleine correctie in de modulecode is aangebracht.

   >[!TIP]
   >Moduleversies bieden ondersteuning voor versiebeheer en bieden u de mogelijkheid wijzigingen te testen op een klein aantal apparaten voordat u updates in een productieomgeving implementeert. Als u de moduleversie niet verhoogt voordat u bouwt en pusht, overschrijft u de opslagplaats in uw containerregister.

6. Sla de wijzigingen op in het bestand module.json.

7. Klik opnieuw met de rechtermuisknop op de projectmap **CSharpTutorialApp** en selecteer **IoT Edge-modules bouwen en pushen** opnieuw.

8. Open het bestand **deployment.windows-amd64.json** opnieuw. U ziet dat er geen nieuw bestand is gemaakt tijdens het uitvoeren van de opdracht voor het bouwen en pushen. Hetzelfde bestand is bijgewerkt met de wijzigingen. De IotEdgeModule1-installatiekopie verwijst nu naar de versie 0.0.2 van de container. Deze wijziging in het implementatiemanifest is de manier waarop u het IoT Edge-apparaat laat weten dat er een nieuwe versie van een module moet worden opgehaald.

9. Ga naar [Azure Portal](https://portal.azure.com) en navigeer naar het containerregister om verder te controleren wat de opdracht voor het bouwen en pushen heeft gedaan.

10. Selecteer in het containerregister **Opslagplaatsen** en vervolgens **iotedgemodule1**. Controleer of beide versies van de installatiekopie naar het register zijn gepusht.

    ![Beide installatiekopieversies in containerregister weergeven](./media/tutorial-develop-for-windows/view-repository-versions.png)

### <a name="troubleshoot"></a>Problemen oplossen

Als er fouten optreden bij het bouwen en pushen van de installatiekopie van de module, heeft dit vaak te maken met Docker-configuratie op uw ontwikkelcomputer. Gebruik de volgende controles om uw configuratie te controleren:

* Hebt u de opdracht `docker login` uitgevoerd met behulp van de referenties die u uit het containerregister hebt gekopieerd? Deze referenties zijn anders dan de referenties die u gebruikt om u aan te melden bij Azure.
* Hebt u de juiste containeropslagplaats? Heeft deze de juiste containerregisternaam en de juiste modulenaam? Open het bestand **module.json** in de map IotEdgeModule1 om dit te controleren. De waarde van de opslagplaats moet eruitzien als **\<registry name\>.azurecr.io/iotedgemodule1**.
* Als u een andere naam dan **IotEdgeModule1** voor uw module hebt gebruikt, is die naam dan wel consistent in de hele oplossing?
* Worden op de computer hetzelfde type containers uitgevoerd als die u bouwt? Deze zelfstudie is voor Windows IoT Edge-apparaten, dus uw Visual Studio-bestanden moeten de **windows-amd64**-extensie hebben en Docker Desktop moet Windows-containers uitvoeren.

## <a name="deploy-modules-to-device"></a>Modules op het apparaat implementeren

U hebt gecontroleerd of de gemaakte containerinstallatiekopieën zijn opgeslagen in het containerregister, dus het is tijd om ze te implementeren op een apparaat. Zorg ervoor dat uw IoT Edge-apparaat actief is.

1. Open Cloud Explorer in Visual Studio en vouw de details voor uw IoT-hub uit.

2. Selecteer de naam van het apparaat waarop u wilt implementeren. Selecteer in de lijst **Actions** de optie **Create Deployment**.

   ![Implementatie voor één apparaat maken](./media/tutorial-develop-for-windows/create-deployment.png)

3. Ga in de bestandsverkenner naar de configuratiemap van uw project en selecteer vervolgens het bestand **deployment.windows-amd64.json**. Dit bestand bevindt zich vaak in `C:\Users\<username>\source\repos\CSharpTutorialApp\CSharpTutorialApp\config\deployment.windows-amd64.json`

   Gebruik niet het bestand deployment.template.json, dat niet de volledige module-installatiekopiewaarden bevat.

4. Vouw de details van uw IoT Edge-apparaat uit in Cloud Explorer om de modules op uw apparaat te bekijken.

5. Gebruik de knop **Vernieuwen** om de status van het apparaat bij te werken om te zien of de modules SimulatedTemperatureSensor en IotEdgeModule1 op uw apparaat zijn geïmplementeerd.

   ![Modules weergeven die op uw IoT Edge-apparaat worden uitgevoerd](./media/tutorial-develop-for-windows/view-running-modules.png)

## <a name="view-messages-from-device"></a>Berichten van het apparaat weergeven

De IotEdgeModule1-code ontvangt berichten via de invoerwachtrij en geeft deze door aan de uitvoerwachtrij. Het implementatiemanifest heeft routes gedeclareerd die berichten hebben doorgegeven van SimulatedTemperatureSensor aan IotEdgeModule1 en berichten vervolgens hebben doorgestuurd van IotEdgeModule1 naar IoT Hub. Met de Azure IoT Edge Tools voor Visual Studio kunt u berichten zien wanneer ze vanaf uw afzonderlijke apparaten op IoT Hub arriveren.

1. Selecteer in Cloud Explorer van Visual Studio de naam van het IoT Edge-apparaat waarop u hebt geïmplementeerd.

2. Selecteer in het menu **Actions** de optie **Start Monitoring Built-in Event Endpoint**.

3. Bekijk de sectie **Output** in Visual Studio om berichten te zien die binnenkomen op uw IoT-hub.

   Het kan enkele minuten duren voordat beide modules worden gestart. De IoT Edge-runtime moet het nieuwe implementatiemanifest ontvangen, de module-installatiekopieën uit de containerruntime ophalen en vervolgens elke nieuwe module starten.

   ![Inkomende apparaat-naar-cloudberichten weergeven](./media/tutorial-develop-for-windows/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Wijzigingen op apparaat weergeven

Als u wilt zien wat er gebeurt op uw apparaat zelf, gebruikt u de opdrachten in deze sectie om de IoT Edge-runtime en -modules op uw apparaat te controleren.

De opdrachten in deze sectie zijn voor uw IoT Edge apparaat, niet voor uw ontwikkelcomputer. Als u een virtuele machine voor uw IoT Edge-apparaat gebruikt, kunt u er nu verbinding mee maken. Ga in Azure naar de overzichtspagina van de virtuele machine en selecteer **Verbinding maken** voor toegang tot de verbinding met extern bureaublad. Open op het apparaat een opdracht- of PowerShell-venster om de `iotedge`-opdrachten uit te voeren.

* Bekijk alle modules die op het apparaat zijn geïmplementeerd en controleer de status ervan:

   ```cmd
   iotedge list
   ```

   U moet vier modules zien: de twee IoT Edge-runtime-modules, SimulatedTemperatureSensor en IotEdgeModule1. Alle vier moeten worden weergegeven als actief.

* Controleer de logboeken voor een specifieke module:

   ```cmd
   iotedge logs <module name>
   ```

   IoT Edge-modules zijn hoofdlettergevoelig.

   De SimulatedTemperatureSensor- en IotEdgeModule1-logboeken moeten de berichten weergeven die ze verwerken. De edgeAgent-module is verantwoordelijk voor het starten van de andere modules, dus de logboeken hiervan bevatten informatie over het implementeren van het implementatiemanifest. Als een module niet wordt vermeld of niet wordt uitgevoerd, bevatten de edgeAgent-logboeken waarschijnlijk de fouten. De edgeHub-module is verantwoordelijk voor de communicatie tussen de modules en IoT Hub. Als de modules actief zijn, maar de berichten niet op uw IoT-hub arriveren, bevatten de edgeHub-logboeken waarschijnlijk de fouten.

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent door te gaan met het volgende aanbevolen artikel, kunt u de resources en configuraties die u hebt gemaakt behouden en opnieuw gebruiken. U kunt ook hetzelfde IoT Edge-apparaat blijven gebruiken als een testapparaat.

Anders kunt u de lokale configuraties en Azure-resources die u in dit artikel hebt gemaakt, verwijderen om kosten te voorkomen.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u Visual Studio 2019 op uw ontwikkelcomputer ingesteld en uw eerste IoT Edge-module geïmplementeerd. Nu u de basisconcepten kent, kunt u functionaliteit toevoegen aan een module, zodat de gegevens die worden doorgegeven, kunnen worden geanalyseerd. Kies uw voorkeurstaal:

> [!div class="nextstepaction"]
> [C](tutorial-c-module-windows.md)
> [C#](tutorial-csharp-module-windows.md)