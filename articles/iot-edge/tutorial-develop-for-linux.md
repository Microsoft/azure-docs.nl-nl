---
title: 'Zelfstudie: een module ontwikkelen voor Linux-apparaten met behulp van Azure IoT Edge'
description: In deze zelfstudie wordt uitgelegd hoe u uw ontwikkelcomputer en cloudresources instelt om IoT Edge modules te ontwikkelen met Linux-containers voor Linux-apparaten
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 10742ec277f5742067c432a2823cbb7592e6a752
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874689"
---
# <a name="tutorial-develop-iot-edge-modules-with-linux-containers"></a>Zelfstudie: Ontwikkel IoT Edge modules met Linux-containers

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Gebruik Visual Studio Code om code te ontwikkelen en te implementeren op apparaten met IoT Edge.

In de quickstart hebt u een IoT Edge gemaakt en een module geïmplementeerd vanuit de Azure Marketplace. In deze zelfstudie wordt uitgelegd hoe u uw eigen code ontwikkelt en implementeert op een IoT Edge-apparaat. Het is handig dit artikel door te nemen voor de andere zelfstudies, die dieper ingaan op specifieke programmeertalen of Azure-services.

In deze zelfstudie wordt gebruikgemaakt van het voorbeeld van de implementatie van een **C#-module op een Linux-apparaat**. Dit voorbeeld is gekozen omdat dit het meest voorkomende ontwikkelaarsscenario voor IoT Edge oplossingen is. Ook als u van plan bent een andere taal te gebruiken of een Azure-service te implementeren, is deze zelfstudie nog steeds nuttig om meer te leren over de ontwikkelhulpprogramma's en -concepten. Voltooi deze inleiding tot het ontwikkelproces en kies vervolgens de gewenste taal of Azure-service om de details te bekijken.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Uw ontwikkelcomputer instellen.
> * De IoT Edge Tools voor Visual Studio Code gebruiken om een nieuw project te maken.
> * Uw project bouwen als een container en het opslaan in een Azure-containerregister.
> * Uw code implementeren op een IoT Edge-apparaat.

## <a name="prerequisites"></a>Vereisten

Een ontwikkelcomputer:

* U kunt uw eigen computer of een virtuele machine gebruiken, afhankelijk van uw ontwikkelvoorkeuren.
  * Zorg ervoor dat uw ontwikkelcomputer ondersteuning biedt voor geneste virtualisatie. Deze mogelijkheid is nodig voor het uitvoeren van een container-engine, die u in de volgende sectie installeert.
* De meeste besturingssystemen waarop een container-engine kan worden uitgevoerd, kunnen worden gebruikt voor het ontwikkelen van IoT Edge-modules voor Linux-apparaten. In deze zelfstudie wordt gebruikgemaakt van een Windows-computer. Er wordt echter gewezen op bekende verschillen in macOS of Linux.
* Installeer [Git](https://git-scm.com/) om verderop in deze zelfstudie modulesjabloonpakketten op te halen.  
* [De extensie C# voor Visual Studio Code (van OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet/2.1).

Een Azure IoT Edge-apparaat:

* We raden u aan om IoT Edge niet uit te voeren op uw ontwikkelcomputer, maar een afzonderlijk apparaat te gebruiken. Dit onderscheid tussen ontwikkelcomputer en IoT Edge apparaat sluit nauwer aan op een echt implementatiescenario en helpt om de verschillende concepten uit elkaar te houden.
* Als u geen tweede apparaat beschikbaar hebt, gebruikt u het quickstart-artikel om een IoT Edge-apparaat in Azure te maken met een [virtuele Linux-machine](quickstart-linux.md).

Cloudresources:

* Een gratis of standaard [IoT-hub](../iot-hub/iot-hub-create-through-portal.md)-laag in Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="key-concepts"></a>Belangrijkste concepten

In deze zelfstudie doorloopt u de ontwikkeling van een IoT Edge-module. Een *IoT Edge-module*, of soms kortweg *module* genoemd, is een container met uitvoerbare code. U kunt een of meer modules implementeren op een IoT Edge-apparaat. Modules voeren specifieke taken uit, zoals het opnemen van gegevens van sensoren, het opschonen en analyseren van gegevens of het verzenden van berichten naar een IoT-hub. Zie [Informatie over Azure IoT Edge-modules](iot-edge-modules.md) voor meer informatie.

Bij het ontwikkelen van IoT Edge-modules is het belangrijk dat u begrijpt wat het verschil is tussen de ontwikkelcomputer en het IoT Edge doelapparaat waarop de module uiteindelijk zal worden geïmplementeerd. De container die u voor de modulecode bouwt, moet overeenkomen met het besturingssysteem van het *doelapparaat*. Zo is het meest voorkomende scenario dat iemand een module ontwikkelt op een Windows-computer met het doel deze op een Linux-apparaat met IoT Edge te implementeren. In dat geval zou het besturingssysteem van de container Linux zijn. Als u deze zelfstudie doorloopt, moet u rekening houden met het verschil tussen het *besturingssysteem van de ontwikkelcomputer* en het *besturingssysteem van de container*.

>[!TIP]
>Als u een linux IoT Edge gebruikt in [Windows,](iot-edge-for-linux-on-windows.md)is het doelapparaat *in* uw scenario de virtuele Linux-machine, niet de Windows-host.

Deze zelfstudie is gericht op apparaten met IoT Edge linux-containers. Als de ontwikkelcomputer Linux-containers uitvoert, kunt u uw favoriete besturingssysteem gebruiken. We raden u aan Visual Studio code te gebruiken om te ontwikkelen met Linux-containers, dus dat wordt in deze zelfstudie gebruikt. U kunt ook Visual Studio gebruiken, maar er zijn wel verschillen in ondersteuning tussen de twee hulpprogramma's.

De volgende tabel bevat een overzicht van de ondersteunde ontwikkelscenario's voor **Linux-containers** in Visual Studio Code en Visual Studio.

|   | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Architectuur van Linux-apparaat** | Linux AMD64 <br> Linux ARM32 | Linux AMD64 <br> Linux ARM32 |
| **Azure-services** | Azure Functions <br> Azure Stream Analytics <br> Azure Machine Learning |   |
| **Talen** | C <br> C# <br> Java <br> Node.js <br> Python | C <br> C# |
| **Meer informatie** | [Azure IoT Edge voor Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Azure IoT Edge Tools voor Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) <br> [Azure IoT Edge Tools voor Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

>[!NOTE]
>Ondersteuning voor Linux ARM64-apparaten is beschikbaar in [openbare preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Zie [ARM64 IoT Edge-modules ontwikkelen in Visual Studio Code en er fouten in opsporen (preview)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview) voor meer informatie.

In deze zelfstudie leert u de ontwikkelstappen voor Visual Studio Code. Als u liever Visual Studio gebruikt, raadpleegt u de instructies in [Visual Studio 2019 gebruiken voor het ontwikkelen van en opsporen van fouten in modules voor Azure IoT Edge](how-to-visual-studio-develop-module.md).
## <a name="install-container-engine"></a>Container-engine installeren

IoT Edge-modules worden verpakt als container. U hebt dus een container-engine op uw ontwikkelcomputer nodig om modules te bouwen en te beheren. We raden aan Docker Desktop te gebruiken voor ontwikkeling vanwege de ondersteuning en populariteit van de functies. Met Docker Desktop in Windows kunt u schakelen tussen Linux-containers en Windows-containers, en kunt u dus eenvoudig modules ontwikkelen voor verschillende soorten IoT Edge apparaten.

Gebruik de Docker-documentatie om Docker Desktop te installeren op uw ontwikkelcomputer:

* [Docker Desktop voor Windows installeren](https://docs.docker.com/docker-for-windows/install/)

  * Wanneer u Docker Desktop voor Windows installeert, wordt u gevraagd of u Linux- of Windows-containers wilt gebruiken. Deze beslissing kan op elk gewenst moment worden gewijzigd met een eenvoudige schakeloptie. Voor deze zelfstudie gebruiken we Linux-containers omdat onze modules gericht zijn op Linux-apparaten. Zie [Schakelen tussen Windows- en Linux-containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) voor meer informatie.

* [Docker Desktop voor Mac installeren](https://docs.docker.com/docker-for-mac/install/)

* Lees [About Docker CE](https://docs.docker.com/install/) voor informatie over de installatie op verschillende Linux-platforms.
  * Installeer Docker Desktop voor Windows voor het Windows-subsysteem voor Linux (WSL).

## <a name="set-up-vs-code-and-tools"></a>VS Code en hulpprogramma's instellen

Gebruik de IoT-extensies voor Visual Studio Code voor het ontwikkelen van IoT Edge-modules. Deze extensies bieden projectsjablonen, automatiseren het maken van het implementatiemanifest en bieden u de mogelijkheid om IoT Edge apparaten te bewaken en te beheren. In deze sectie installeert u Visual Studio Code en de IoT-extensie. Vervolgens stelt u uw Azure-account in voor het beheren van IoT Hub-resources vanuit Visual Studio Code.

1. Installeer [Visual Studio Code](https://code.visualstudio.com/) op uw ontwikkelcomputer.

2. Wanneer de installatie is voltooid, selecteert u **View** > **Extensions**.

3. Zoek naar **Azure IoT Tools**. Dit is feitelijk een verzameling extensies waarmee u kunt communiceren met IoT-hub- en IoT-apparaten, en IoT Edge-modules kunt ontwikkelen.

4. Selecteer **Installeren**. Elke extensie die wordt opgenomen, wordt afzonderlijk geïnstalleerd.

5. Wanneer de extensies zijn geïnstalleerd, opent u het opdrachtpalet door **View** > **Command Palette** te selecteren.

6. In het opdrachtenpalet zoekt en selecteert u **Azure: Aanmelden** en voer deze uit. Volg de aanwijzingen om u aan te melden bij uw Azure-account.

7. Weer in het opdrachtenpalet zoekt en selecteert u **Azure IoT Hub: Select IoT Hub**. Volg de aanwijzingen om uw Azure-abonnement en IoT-hub te selecteren.

8. Open de sectie Explorer van Visual Studio Code door het pictogram te selecteren in de activiteitenbalk aan de linkerkant of door **View** > **Explorer** te selecteren.

9. Vouw onderaan de sectie Explorer het samengevouwen menu **Azure IoT Hub / Devices** uit. Als het goed is, ziet u nu de apparaten en IoT Edge-apparaten die zijn gekoppeld aan de IoT-hub die u hebt geselecteerd in het opdrachtenpalet.

   ![Apparaten weergeven in uw IoT-hub](./media/tutorial-develop-for-linux/view-iot-hub-devices.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Een nieuw moduleproject maken

De extensie voor Azure IoT-hulpprogramma's biedt projectsjablonen voor alle ondersteunde IoT Edge-moduletalen in Visual Studio Code. Deze sjablonen bevatten alle bestanden en code die u nodig hebt om een werkende module te implementeren om IoT Edge te testen of bieden u een uitgangspunt om de sjabloon aan te passen met uw eigen bedrijfslogica.

Voor deze zelfstudie gebruiken we de C#-modulesjabloon omdat deze de meestgebruikte sjabloon is.

### <a name="create-a-project-template"></a>Een projectsjabloon maken

Zoek en selecteer in het Visual Studio Code-opdrachtenpalet de optie **Azure IoT Edge: New IoT Edge Solution**. Volg de aanwijzingen en gebruik de volgende waarden om uw oplossing te maken:

   | Veld | Waarde |
   | ----- | ----- |
   | Map selecteren | Kies de locatie op uw ontwikkelcomputer waar VS Code de oplossingsbestanden moet maken. |
   | Een naam opgeven voor de oplossing | Voer een beschrijvende naam voor de oplossing in of accepteer de standaardnaam **EdgeSolution**. |
   | Modulesjabloon selecteren | Kies **C# Module**. |
   | Een modulenaam opgeven | Accepteer de standaardwaarde **SampleModule**. |
   | Opslagplaats voor Docker-afbeeldingen voor de module opgeven | Een opslagplaats voor afbeeldingen bevat de naam van het containerregister en de naam van uw containerafbeelding. De containerafbeelding wordt vooraf gevuld vanuit de naam die u in de laatste stap hebt opgegeven. Vervang **localhost:5000** door de waarde van de **aanmeldingsserver** uit uw Azure-containerregister. U vindt de waarde van de aanmeldingsserver op de overzichtspagina van het containerregister in de Azure-portal. <br><br> De uiteindelijke opslagplaats voor de installatiekopie ziet er als volgt uit: \<registry name\>.azurecr.io/nodemodule. |

   ![Opslagplaats voor Docker-installatiekopieën opgeven](./media/tutorial-develop-for-linux/image-repository.png)

Zodra de nieuwe oplossing in het Visual Studio Code-venster is geladen, neemt u even de tijd om vertrouwd te raken met de bestanden die zijn gemaakt:

* De map **.vscode** bevat een bestand met de naam **launch.json**, dat wordt gebruikt voor het opsporen van fouten in modules.
* De map **modules** bevat een map voor elke module in uw oplossing. Op dit moment moet dit alleen **SampleModule** zijn, of een andere naam die u aan de module hebt gegeven. De map SampleModule bevat de belangrijkste programmacode, de metagegevens van de module en verschillende Docker-bestanden.
* Het bestand **.env** bevat de referenties voor uw containerregister. Deze referenties worden gedeeld met uw IoT Edge-apparaat, zodat het de containerinstallatiekopieën kan ophalen.
* Het bestand **deployment.debug.template.json** en het bestand **deployment.template.json** zijn sjablonen die u helpen bij het maken van een implementatiemanifest. Een *implementatiemanifest* is een bestand dat precies aangeeft welke modules u wilt implementeren op een apparaat, hoe ze moeten worden geconfigureerd en hoe ze met elkaar en de cloud kunnen communiceren. De sjabloonbestanden gebruiken aanwijzers voor bepaalde waarden. Wanneer u de sjabloon omzet in een echt implementatiemanifest, worden de aanwijzers vervangen door waarden die zijn overgenomen uit andere oplossingsbestanden. Zoek de twee gebruikelijke tijdelijke aanduidingen in uw implementatiesjabloon:

  * In de sectie met registerreferenties wordt het adres automatisch ingevuld op basis van de informatie die u hebt ingevoerd tijdens het maken van de oplossing. De gebruikersnaam en het wachtwoord verwijzen echter naar de variabelen die zijn opgeslagen in het .env-bestand. Deze configuratie is om beveiligingsredenen, omdat het .env-bestand door Git wordt genegeerd, maar de implementatiesjabloon niet.
  * In de sectie SampleModule is de containerinstallatiekopie niet ingevuld, zelfs als u de opslagplaats voor de installatiekopie tijdens het maken van de oplossing hebt opgegeven. Deze tijdelijke aanduiding verwijst naar het bestand **module.json** in de map SampleModule. Als u naar dat bestand gaat, ziet u dat het installatiekopieveld de opslagplaats bevat, maar ook een tagwaarde die uit de versie en het platform van de container bestaat. U kunt de versie handmatig implementeren als onderdeel van de ontwikkelcyclus en u selecteert het containerplatform met behulp van een schakelaar die verderop in deze sectie wordt geïntroduceerd.

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Uw registerreferenties voor de IoT Edge-agent opgeven

In het omgevingsbestand worden de referenties voor het containerregister opgeslagen. Deze referenties worden gedeeld met de IoT Edge-runtime. De runtime heeft deze referenties nodig om uw containerinstallatiekopieën naar het IoT Edge-apparaat te halen.

>[!NOTE]
>Als u de waarde **localhost:5000** niet hebt vervangen door de waarde voor de aanmeldingsserver van uw Azure-containerregister, ontbreken in de stap [**Een projectsjabloon maken**](#create-a-project-template) het **. env**-bestand en de sectie registryCredentials van het implementatiemanifest. 

De IoT Edge-extensie probeert uw containerregisterreferenties van Azure op te halen en deze in het omgevingsbestand in te vullen. Controleer of uw referenties al zijn toegevoegd. Als dat niet het geval is, voegt u ze nu toe:

1. Open het **.env**-bestand in uw moduleoplossing.
2. Voeg de waarden voor **gebruikersnaam** en **wachtwoord** toe die u hebt gekopieerd uit het Azure-containerregister.
3. Sla de wijzigingen in het .env-bestand op.

### <a name="select-your-target-architecture"></a>Uw doelarchitectuur selecteren

Momenteel kunt u met Visual Studio Code C#-modules ontwikkelen voor Linux AMD64- en ARM32v7-apparaten. U moet voor elke oplossing selecteren op welke architectuur u zich richt, omdat dit beïnvloedt hoe de container wordt opgebouwd en uitgevoerd. De standaardinstelling is Linux AMD64.

1. Open het opdrachtpalet en zoek **Azure IoT Edge: standaarddoelplatform voor Edge-oplossing instellen** of selecteer het snelkoppelingspictogram onderaan het venster.

   ![Pictogram Architectuur selecteren in zijbalk](./media/tutorial-develop-for-linux/select-architecture.png)

2. Selecteer in het opdrachtpalet de doelarchitectuur in de lijst met opties. In deze zelfstudie gebruiken we een virtuele Ubuntu-machine als het IoT Edge-apparaat, dus laten we de standaardinstelling **amd64** staan.

### <a name="review-the-sample-code"></a>De voorbeeldcode bekijken

De oplossingssjabloon die u hebt gemaakt, bevat voorbeeldcode voor een IoT Edge-module. In deze voorbeeldmodule worden berichten eenvoudigweg ontvangen en vervolgens doorgegeven. Met de pijplijnfunctionaliteit wordt een belangrijk concept in IoT Edge gedemonstreerd, waarmee modules met elkaar communiceren.

Elke module kan meerdere *invoer*- en *uitvoer* wachtrijen hebben die in hun code zijn gedeclareerd. Met de IoT Edge-hub die op het apparaat wordt uitgevoerd, worden berichten van de uitvoer van een module naar de invoer van een of meer modules gerouteerd. De specifieke code voor het declareren van invoer en uitvoer verschilt per taal, maar het concept is voor alle modules hetzelfde. Zie [Routes declareren](module-composition.md#declare-routes) voor meer informatie over routering tussen modules.

De C#-voorbeeldcode die bij de projectsjabloon hoort, maakt gebruik van de [ModuleClient-klasse](/dotnet/api/microsoft.azure.devices.client.moduleclient) van de IoT Hub SDK voor .NET.

1. Open het bestand **Program.cs**, dat zich in de map **modules/SampleModule/** bevindt.

2. Zoek in program.cs de methode **SetInputMessageHandlerAsync**.

3. Met de methode [SetInputMessageHandlerAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync) wordt een invoerwachtrij ingesteld voor het ontvangen van inkomende berichten. Bekijk deze methode en zie hoe hiermee een invoerwachtrij met de naam **input1** wordt geïnitialiseerd.

   ![De invoernaam zoeken in de SetInputMessageCallback-constructor](./media/tutorial-develop-for-linux/declare-input-queue.png)

4. Zoek vervolgens de methode **SendEventAsync**.

5. Met de methode [SendEventAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync) worden ontvangen berichten verwerkt en wordt een uitvoerwachtrij ingesteld om de berichten door te geven. Bekijk deze methode en controleer of er een uitvoerwachtrij met de naam **output1** wordt geïnitialiseerd.

   ![De uitvoernaam zoeken in SendEventToOutputAsync](./media/tutorial-develop-for-linux/declare-output-queue.png)

6. Open het bestand **deployment.template.json**.

7. Zoek de eigenschap **modules** van de gewenste eigenschappen van $edgeAgent.

   Er moeten hier twee modules worden weergegeven. Een is de **SimulatedTemperatureSensor**-module, die standaard is opgenomen in alle sjablonen om gesimuleerde temperatuurgegevens te bieden die u kunt gebruiken om uw modules te testen. De andere is de module **SampleModule** die u als onderdeel van deze oplossing hebt gemaakt.

8. Zoek onder aan het bestand de gewenste eigenschappen voor de module **$edgeHub**.

   Een van de functies van de IoT Edge hub-module bestaat uit het routeren van berichten tussen alle modules in een implementatie. Bekijk de waarden in de eigenschap **routes**. De ene route, **SampleModuleToIoTHub**, gebruikt een jokerteken (* *\** _) om berichten aan te geven die afkomstig zijn uit uitvoerwachtrijen in de SampleModule-module. Deze berichten gaan naar _$upstream*, een gereserveerde naam die IoT Hub aangeeft. De andere route, **sensorToSampleModule**, neemt berichten over van de SimulatedTemperatureSensor-module en stuurt deze door naar de invoerwachtrij *input1* die in de SampleModule-code is geïnitialiseerd.

   ![Routes bekijken in deployment.template.json](./media/tutorial-develop-for-linux/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>De oplossing bouwen en pushen

U hebt de modulecode en de implementatiesjabloon gecontroleerd om inzicht te krijgen in enkele belangrijke concepten van de implementatie. Nu moet u de SampleModule-containerinstallatiekopie bouwen en naar het containerregister pushen. Met de extensie voor IoT-hulpprogramma's voor Visual Studio Code worden in deze stap ook het implementatiemanifest op basis van de informatie in het sjabloonbestand en de modulegegevens van de oplossingsbestanden gegenereerd.

### <a name="sign-in-to-docker"></a>Aanmelden bij Docker

Geef de containerregisterreferenties op voor Docker, zodat de containerinstallatiekopie voor opslag in het register kan worden gepusht.

1. Open de in Visual Studio Code geïntegreerde terminal door **View** > **Terminal** te selecteren.

2. Meld u aan bij Docker met de Azure-containerregisterreferenties die u hebt opgeslagen nadat u het register hebt gemaakt.

   ```cmd/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Mogelijk wordt een beveiligingswaarschuwing weergegeven waarin het gebruik van `--password-stdin` wordt aanbevolen. Hoewel dit wordt aanbevolen voor productiescenario's, valt het buiten het bereik van deze zelfstudie. Zie de documentatie voor [aanmelding bij Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) voor meer informatie.
   
3. Inloggen in Azure Container Registry

   ```cmd/sh
   az acr login -n <ACR registry name>
   ```

### <a name="build-and-push"></a>Bouwen en pushen

Visual Studio Code heeft nu toegang tot uw containerregister, dus is het tijd om de oplossingscode in te schakelen in een containerinstallatiekopie.

1. Klik in de Visual Studio Code Explorer met de rechtermuisknop op het bestand **deployment.template.json** en selecteer **Build and Push IoT Edge Solution**.

   ![IoT Edge-modules bouwen en pushen](./media/tutorial-develop-for-linux/build-and-push-modules.png)

   Met de opdracht voor bouwen en pushen worden drie bewerkingen gestart. Eerst wordt er een nieuwe map met de naam **config** in de oplossing gemaakt die het volledige implementatiemanifest bevat, op basis van de informatie in de implementatiesjabloon en andere oplossingsbestanden. Daarna wordt `docker build` uitgevoerd om de containerinstallatiekopie te bouwen op basis van de juiste dockerfile voor uw doelarchitectuur. Vervolgens wordt `docker push` uitgevoerd om de opslagplaats van de installatiekopie naar het containerregister te pushen.

   Dit proces kan de eerste keer enkele minuten duren, maar de volgende keer dat u de opdrachten uitvoert, wordt het sneller uitgevoerd.

2. Open het bestand **deployment.amd64.json** in de zojuist gemaakte configuratiemap. De bestandsnaam geeft de doelarchitectuur aan. Deze is dus anders als u een andere architectuur hebt gekozen.

3. U ziet dat de twee parameters met een tijdelijke aanduiding nu de juiste waarden bevatten. In het gedeelte **registryCredentials** zijn de gebruikersnaam en wachtwoord voor het register opgehaald uit het .env-bestand. De **SampleModule** bevat de volledige installatiekopieopslagplaats met de naam, versie en architectuurtag van het module.json-bestand.

4. Open het bestand **module.json** in de map SampleModule.

5. Wijzig het versienummer voor de installatiekopie van de module. (De versie, niet de $schema-version.) Verhoog bijvoorbeeld het versienummer van de patch naar **0.0.2**, alsof er een kleine correctie in de modulecode is aangebracht.

   >[!TIP]
   >Moduleversies bieden ondersteuning voor versiebeheer en bieden u de mogelijkheid wijzigingen te testen op een klein aantal apparaten voordat u updates in een productieomgeving implementeert. Als u de moduleversie niet verhoogt voordat u bouwt en pusht, overschrijft u de opslagplaats in uw containerregister.

6. Sla de wijzigingen op in het bestand module.json.

7. Klik opnieuw met de rechtermuisknop op het bestand **deployment.template.json** en selecteer opnieuw **Build and Push IoT Edge solution**.

8. Open het bestand **deployment.amd64.json** opnieuw. U ziet dat er geen nieuw bestand is gemaakt tijdens het uitvoeren van de opdracht voor het bouwen en pushen. Hetzelfde bestand is bijgewerkt met de wijzigingen. De SampleModule-installatiekopie verwijst nu naar de versie 0.0.2 van de container.

9. Ga naar [Azure Portal](https://portal.azure.com) en navigeer naar het containerregister om verder te controleren wat de opdracht voor het bouwen en pushen heeft gedaan.

10. Selecteer in het containerregister **Opslagplaatsen** en vervolgens **samplemodule**. Controleer of beide versies van de installatiekopie naar het register zijn gepusht.

    ![Beide installatiekopieversies in containerregister weergeven](./media/tutorial-develop-for-linux/view-repository-versions.png)

<!--Alternative steps: Use VS Code Docker tools to view ACR images with tags-->

### <a name="troubleshoot"></a>Problemen oplossen

Als er fouten optreden bij het bouwen en pushen van de installatiekopie van de module, heeft dit vaak te maken met Docker-configuratie op uw ontwikkelcomputer. Gebruik de volgende controles om uw configuratie te controleren:

* Hebt u de opdracht `docker login` uitgevoerd met behulp van de referenties die u uit het containerregister hebt gekopieerd? Deze referenties zijn anders dan de referenties die u gebruikt om u aan te melden bij Azure.
* Hebt u de juiste containeropslagplaats? Heeft deze de juiste containerregisternaam en de juiste modulenaam? Open het bestand **module.json** in de map SampleModule om dit te controleren. De waarde van de opslagplaats moet eruitzien als **\<registry name\>.azurecr.io/samplemodule**.
* Als u een andere naam dan **SampleModule** voor uw module hebt gebruikt, is die naam dan wel consistent in de hele oplossing?
* Worden op de computer hetzelfde type containers uitgevoerd als die u bouwt? Deze zelfstudie is voor Linux IoT Edge-apparaten en daarom moet in de zijbalk van Visual Studio Code **amd64** of **arm32v7** worden vermeld, en moeten op Docker Desktop Linux-containers worden uitgevoerd.  

## <a name="deploy-modules-to-device"></a>Modules op het apparaat implementeren

U hebt gecontroleerd of de gemaakte containerinstallatiekopieën zijn opgeslagen in het containerregister, dus het is tijd om ze te implementeren op een apparaat. Zorg ervoor dat uw IoT Edge-apparaat actief is.

1. Vouw in de Visual Studio Code Explorer, onder de sectie **Azure IoT Hub**, de optie **Apparaten** uit om de lijst met IoT-apparaten weer te geven.

2. Klik met de rechtermuisknop op de naam van het IoT Edge-apparaat dat u wilt implementeren en selecteer **Create Deployment for Single Device**.

   ![Implementatie voor één apparaat maken](./media/tutorial-develop-for-linux/create-deployment.png)

3. Ga in de bestandsverkenner naar de map **config** en selecteer vervolgens het bestand **deployment.amd64.json**.

   Gebruik niet het bestand deployment.template.json, dat geen containerregisterreferenties of module-installatiekopiewaarden bevat. Als u ontwikkelt voor een Linux ARM32-apparaat, wordt het implementatiemanifest deployment.arm32v7.json genoemd.

4. Vouw onder uw apparaat **Modules** uit voor een lijst met de geïmplementeerde en actieve modules. Klik op de knop Vernieuwen. U ziet nu de nieuwe SimulatedTemperatureSensor- en SampleModule-modules die op uw apparaat worden uitgevoerd.

   Het kan enkele minuten duren voordat de modules zijn gestart. De IoT Edge-runtime moet het nieuwe implementatiemanifest ontvangen, de module-installatiekopieën uit de containerruntime ophalen en vervolgens elke nieuwe module starten.

   ![Modules weergeven die op uw IoT Edge-apparaat worden uitgevoerd](./media/tutorial-develop-for-linux/view-running-modules.png)

## <a name="view-messages-from-device"></a>Berichten van het apparaat weergeven

De SampleModule-code ontvangt berichten via de invoerwachtrij en geeft deze door aan de uitvoerwachtrij. Het implementatiemanifest heeft routes gedeclareerd die berichten hebben doorgegeven aan SampleModule van SimulatedTemperatureSensor en berichten vervolgens hebben doorgestuurd van SampleModule naar IoT Hub. Met de Azure IoT Tools voor Visual Studio Code kunt u berichten zien wanneer ze vanaf uw afzonderlijke apparaten op IoT Hub arriveren.

1. Klik in de Visual Studio Code Explorer met de rechtermuisknop op de naam van het IoT Edge-apparaat dat u wilt controleren en selecteer **Start Monitoring Built-in Event Endpoint**.

2. Bekijk het uitvoervenster in Visual Studio Code om berichten te zien die binnenkomen op uw IoT-hub.

   ![Inkomende apparaat-naar-cloudberichten weergeven](./media/tutorial-develop-for-linux/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Wijzigingen op apparaat weergeven

Als u wilt zien wat er gebeurt op uw apparaat zelf, gebruikt u de opdrachten in deze sectie om de IoT Edge-runtime en -modules op uw apparaat te controleren.

De opdrachten in deze sectie zijn voor uw IoT Edge apparaat, niet voor uw ontwikkelcomputer. Als u een virtuele machine voor uw IoT Edge-apparaat gebruikt, kunt u er nu verbinding mee maken. Ga in Azure naar de overzichtspagina van de virtuele machine en selecteer **Verbinding maken** voor toegang tot de beveiligde shell-verbinding.

* Bekijk alle modules die op het apparaat zijn geïmplementeerd en controleer de status ervan:

   ```bash
   iotedge list
   ```

   U moet vier modules zien: de twee IoT Edge-runtime-modules, SimulatedTemperatureSensor en SampleModule. Alle vier moeten worden weergegeven als actief.

* Controleer de logboeken voor een specifieke module:

   ```bash
   iotedge logs <module name>
   ```

   IoT Edge-modules zijn hoofdlettergevoelig.

   De SimulatedTemperatureSensor- en SampleModule-logboeken moeten de berichten weergeven die ze verwerken. De edgeAgent-module is verantwoordelijk voor het starten van de andere modules, dus de logboeken hiervan bevatten informatie over het implementeren van het implementatiemanifest. Als een module niet wordt vermeld of niet wordt uitgevoerd, bevatten de edgeAgent-logboeken waarschijnlijk de fouten. De edgeHub-module is verantwoordelijk voor de communicatie tussen de modules en IoT Hub. Als de modules actief zijn, maar de berichten niet op uw IoT-hub arriveren, bevatten de edgeHub-logboeken waarschijnlijk de fouten.

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent door te gaan met het volgende aanbevolen artikel, kunt u de resources en configuraties die u hebt gemaakt behouden en opnieuw gebruiken. U kunt ook hetzelfde IoT Edge-apparaat blijven gebruiken als een testapparaat.

Anders kunt u de lokale configuraties en Azure-resources die u in dit artikel hebt gemaakt, verwijderen om kosten te voorkomen.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u Visual Studio Code op uw ontwikkelcomputer ingesteld en uw eerste IoT Edge-module geïmplementeerd. Nu u de basisconcepten kent, kunt u functionaliteit toevoegen aan een module, zodat de gegevens die worden doorgegeven, kunnen worden geanalyseerd. Kies uw voorkeurstaal:

> [!div class="nextstepaction"]
> [C](tutorial-c-module.md)
> [C#](tutorial-csharp-module.md)
> [Java](tutorial-java-module.md)
> [Node.js](tutorial-node-module.md)
> [Python](tutorial-python-module.md)
