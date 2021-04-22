---
title: Modules ontwikkelen en fouten opsporen voor Azure IoT Edge | Microsoft Docs
description: Gebruik Visual Studio Code voor het ontwikkelen, bouwen en opsporen van fouten in een module voor Azure IoT Edge met behulp van C#, Python, Node.js, Java of C
services: iot-edge
keywords: ''
author: kgremban
ms.author: kgremban
ms.date: 08/07/2019
ms.topic: conceptual
ms.service: iot-edge
ms.custom: devx-track-js
ms.openlocfilehash: 7cc5061911ffc2cbc91dfa8c0d2fd1a6e59ff1a1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875661"
---
# <a name="use-visual-studio-code-to-develop-and-debug-modules-for-azure-iot-edge"></a>Gebruik Visual Studio Code voor het ontwikkelen en opsporen van fouten in modules voor Azure IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

U kunt uw bedrijfslogica veranderen in modules voor Azure IoT Edge. In dit artikel wordt beschreven hoe u Visual Studio Code gebruikt als het belangrijkste hulpprogramma voor het ontwikkelen en opsporen van fouten in modules.

Er zijn twee manieren om fouten op te sporen in modules die zijn geschreven in C#, Node.js of Java in Visual Studio Code: u kunt een proces koppelen in een modulecontainer of de modulecode starten in de foutopsporingsmodus. Als u fouten wilt opsporen in modules die zijn geschreven in Python of C, kunt u alleen koppelen aan een proces in Linux amd64-containers.

Als u niet bekend bent met de mogelijkheden voor het debuggen van Visual Studio Code, leest u [over Debugging](https://code.visualstudio.com/Docs/editor/debugging).

Dit artikel bevat instructies voor het ontwikkelen en debuggen van modules in meerdere talen voor meerdere architecturen. Momenteel biedt Visual Studio Code ondersteuning voor modules die zijn geschreven in C#, C, Python, Node.js en Java. De ondersteunde apparaatarchitecten zijn X64 en ARM32. Zie Taal- en architectuurondersteuning voor meer informatie over ondersteunde besturingssystemen, [talen en architecturen.](module-development.md#language-and-architecture-support)

>[!NOTE]
>Ondersteuning voor ontwikkelen en debuggen voor Linux ARM64-apparaten is in [openbare preview.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Zie [ARM64 IoT Edge-modules ontwikkelen in Visual Studio Code en er fouten in opsporen (preview)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

U kunt een computer of een virtuele machine met Windows, macOS of Linux gebruiken als uw ontwikkelcomputer. Op Windows-computers kunt u Windows- of Linux-modules ontwikkelen. Als u Windows-modules wilt ontwikkelen, gebruikt u een Windows-computer met versie 1809/build 17763 of hoger. Als u Linux-modules wilt ontwikkelen, gebruikt u een Windows-computer die voldoet aan [de vereisten voor Docker Desktop.](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)

Installeer [eerst Visual Studio Code](https://code.visualstudio.com/) en voeg vervolgens de volgende extensies toe:

- [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
- [Docker-extensie](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
- Visual Studio(s) die specifiek zijn voor de taal waarin u ontwikkelt:
  - C#, inclusief Azure Functions: [C#-extensie](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
  - Python: [Python-extensie](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  - Java: [Java Extension Pack voor Visual Studio code](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
  - C: [C/C++-extensie](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)

U moet ook enkele aanvullende taalspecifieke hulpprogramma's installeren om uw module te ontwikkelen:

- C#, inclusief Azure Functions: [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet/2.1)

- Python: [Python](https://www.python.org/downloads/) en [Pip voor](https://pip.pypa.io/en/stable/installing/#installation) het installeren van Python-pakketten (meestal opgenomen in uw Python-installatie).

- Node.js: [Node.js](https://nodejs.org). U kunt ook [Yeoman](https://www.npmjs.com/package/yo) en de modulegenerator [Azure IoT Edge Node.js installeren.](https://www.npmjs.com/package/generator-azure-iot-edge-module)

- Java: [Java SE Development Kit 10](/azure/developer/java/fundamentals/java-jdk-long-term-support) en [Maven](https://maven.apache.org/). U moet de [omgevingsvariabele `JAVA_HOME` instellen om naar](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) uw JDK-installatie te wijzen.

Als u de module-afbeelding wilt bouwen en implementeren, hebt u Docker nodig om de module-afbeelding en een containerregister te bouwen voor het maken van de module-afbeelding:

- [Docker Community Edition](https://docs.docker.com/install/) op uw ontwikkelmachine.

- [Azure Container Registry](../container-registry/index.yml) of [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

    > [!TIP]
    > U kunt een lokaal Docker-register gebruiken voor prototype- en testdoeleinden in plaats van een cloudregister.

Tenzij u uw module in C ontwikkelt, hebt u ook het op Python gebaseerde [Azure IoT EdgeHub Dev Tool](https://pypi.org/project/iotedgehubdev/) nodig om uw lokale ontwikkelomgeving in te stellen voor het opsporen, uitvoeren en testen van uw IoT Edge oplossing. Als u dit nog niet hebt gedaan, installeert u [Python (2.7/3.6/3.7)](https://www.python.org/) en Pip en installeert u **vervolgens iotedgehubdev** door deze opdracht uit te voeren in uw terminal.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```
   
> [!NOTE]
> Momenteel maakt iotedgehubdev gebruik van een docker-py-bibliotheek die niet compatibel is met Python 3.8.
>
> Als u meerdere Python hebt, inclusief vooraf geïnstalleerde python 2.7 (bijvoorbeeld in Ubuntu of macOS), moet u ervoor zorgen dat u de juiste gebruikt of `pip` `pip3` **iotedgehubdev** installeert

Als u de module op een apparaat wilt testen, hebt u een actieve IoT-hub nodig met ten minste één IoT Edge apparaat. Als u uw computer wilt gebruiken als IoT Edge apparaat, volgt u de stappen in de snelstart voor [Linux](quickstart-linux.md) of [Windows.](quickstart.md) Als u een IoT Edge op uw ontwikkelmachine, moet u EdgeHub en EdgeAgent mogelijk stoppen voordat u verder gaat met de volgende stap.

## <a name="create-a-new-solution-template"></a>Een nieuwe oplossingssjabloon maken

De volgende stappen laten zien hoe u een IoT Edge-module maakt in uw favoriete ontwikkelingstaal (inclusief Azure Functions, geschreven in C#) met behulp van Visual Studio Code en de Azure IoT Tools. U begint met het maken van een oplossing en vervolgens het genereren van de eerste module in die oplossing. Elke oplossing kan meerdere modules bevatten.

1. Selecteer   >  **Opdrachtpalet weergeven.**

1. Voer in het opdrachtpalet de opdracht in en **voer deze uit Azure IoT Edge: New IoT Edge Solution**.

   ![Nieuwe IoT Edge uitvoeren](./media/how-to-develop-csharp-module/new-solution.png)

1. Blader naar de map waar u de nieuwe oplossing wilt maken en selecteer **vervolgens Map selecteren.**

1. Voer een naam in voor uw oplossing.

1. Selecteer een modulesjabloon voor de ontwikkelingstaal van uw voorkeur als de eerste module in de oplossing.

1. Voer een naam in voor de module. Kies een naam die uniek is binnen uw containerregister.

1. Geef de naam op van de opslagplaats voor de afbeelding van de module. Visual Studio Code wordt de modulenaam automatisch met **localhost:5000/<modulenaam. \>** Vervang deze door uw eigen registergegevens. Als u een lokaal Docker-register gebruikt voor het testen, is **localhost** prima. Als u Azure Container Registry gebruikt, gebruikt u de aanmeldingsserver uit de instellingen van uw register. De aanmeldingsserver ziet er als **_\<registry name\>_ azurecr.io.** Vervang alleen het **gedeelte localhost:5000** van de tekenreeks zodat het uiteindelijke resultaat eruitziet als **\<*registry name*\> .azurecr.io/ _\<your module name\>_**.

   ![Opslagplaats voor Docker-installatiekopieën opgeven](./media/how-to-develop-csharp-module/repository.png)

Visual Studio Code gebruikt de informatie die u hebt opgegeven, maakt een IoT Edge oplossing en laadt deze in een nieuw venster.

De oplossing heeft vier items:

- Een **vscode-map** bevat foutopsporingsconfiguraties.

- Een **map modules** bevat submappen voor elke module.  In de map voor elke module staat een bestand, **module.jsop**, dat bepaalt hoe modules worden gebouwd en geïmplementeerd.  Dit bestand moet worden gewijzigd om het containerregister van de module-implementatie te wijzigen van localhost in een extern register. Op dit moment hebt u slechts één module.  Maar u kunt meer toevoegen aan het opdrachtenpalet met de opdracht **Azure IoT Edge: Add IoT Edge Module**.

- Een **.env-bestand** bevat uw omgevingsvariabelen. Als Azure Container Registry uw register is, hebt u een Azure Container Registry en wachtwoord in het register.

  > [!NOTE]
  > Het omgevingsbestand wordt alleen gemaakt als u een opslagplaats voor de afbeelding voor de module op geeft. Als u de localhost-standaardwaarden hebt geaccepteerd om lokaal te testen en fouten op te sporen, hoeft u geen omgevingsvariabelen te declaren.

- In **deployment.template.jsbestand** wordt de nieuwe module vermeld, samen met een **simulatedTemperatureSensor-voorbeeldmodule** die gegevens simuleert die u voor het testen kunt gebruiken. Zie Voor meer informatie over hoe implementatiemanifests werken, zie [Implementatiemanifests](module-composition.md)gebruiken om modules te implementeren en routes vast te stellen.

Bekijk de broncode [SimulatedTemperatureSensor.csproj](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor)om te zien hoe de gesimuleerde temperatuurmodule werkt.

## <a name="add-additional-modules"></a>Aanvullende modules toevoegen

Als u extra modules wilt toevoegen aan uw oplossing, moet u de opdracht **Azure IoT Edge: Add IoT Edge Module** from the command palette (Module toevoegen vanuit het opdrachtpalet). U kunt ook met de rechtermuisknop op **de map modules** of het bestand in de Visual Studio codeverkennerweergave klikken en vervolgens `deployment.template.json` IoT Edge Module **toevoegen** selecteren.

## <a name="develop-your-module"></a>Uw module ontwikkelen

De standaardmodulecode die bij de oplossing wordt geleverd, bevindt zich op de volgende locatie:

- Azure Function (C#): **modules > de module de naam *&lt; van &gt;*  >  *&lt; uw module &gt; .cs***
- C#: **modules > de naam van *&lt; uw &gt; module* > Program.cs**
- Python: **modules > *&lt; de naam &gt; van uw module* > main.py**
- Node.js: modules **> *&lt; de naam van &gt; uw module* > app.js**
- Java: **modules > de *&lt; &gt; naam* van uw module > src > main > java > com > edgemodulemodules > App.java**
- C: **modules > uw *&lt; modulenaam > &gt;* main.c**

De module en de deployment.template.json-file zijn zo ingesteld dat u de oplossing kunt bouwen, deze naar het containerregister kunt pushen en op een apparaat kunt implementeren om te testen zonder code aan te raken. De module is gebouwd om invoer van een bron op te halen (in dit geval de module SimulatedTemperatureSensor die gegevens simuleert) en deze door te se pijpen naar IoT Hub.

Wanneer u klaar bent om de sjabloon aan te passen met uw eigen code, gebruikt u de [Azure IoT Hub SDK's](../iot-hub/iot-hub-devguide-sdks.md) om modules te bouwen die voldoen aan de belangrijkste behoeften voor IoT-oplossingen, zoals beveiliging, apparaatbeheer en betrouwbaarheid.

## <a name="debug-a-module-without-a-container-c-nodejs-java"></a>Fouten opsporen in een module zonder container (C#, Node.js, Java)

Als u ontwikkelt in C#, Node.js of Java, vereist uw module het gebruik van een **ModuleClient-object** in de standaardmodulecode, zodat deze berichten kan starten, uitvoeren en door sturen. U gebruikt ook de **standaardinvoerkanaalinvoer1 om** actie te ondernemen wanneer de module berichten ontvangt.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>Een IoT Edge-simulator instellen voor IoT Edge oplossing

Op uw ontwikkelmachine kunt u een IoT Edge-simulator starten in plaats van de IoT Edge-beveiligingsdeemon te installeren, zodat u uw IoT Edge uitvoeren.

1. Klik in Device Explorer aan de linkerkant met de rechtermuisknop op de apparaat-id van uw IoT Edge en selecteer vervolgens **Setup IoT Edge Simulator** om de simulator te starten met het apparaat connection string.
1. U kunt zien IoT Edge simulator is ingesteld door de voortgangsdetails te lezen in de geïntegreerde terminal.

### <a name="set-up-iot-edge-simulator-for-single-module-app"></a>Een simulator IoT Edge één module-app instellen

Voer de opdracht **Azure IoT Edge: Start IoT Edge Hub Simulator for Single Module** vanuit het opdrachtpalet van Visual Studio Code om de simulator in te stellen en te starten. Wanneer u hier om wordt gevraagd, gebruikt u de waarde **input1** uit de standaardmodulecode (of de equivalente waarde van uw code) als invoernaam voor uw toepassing. De opdracht activeert **de Iotedgehubdev** CLI en start vervolgens de IoT Edge simulator en een modulecontainer voor het testprogramma. U kunt de onderstaande uitvoer bekijken in de geïntegreerde terminal als de simulator is gestart in de modus voor één module. U kunt ook een opdracht `curl` zien om u te helpen bij het verzenden van berichten. U gebruikt dit later.

   ![Een simulator IoT Edge één module-app instellen](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   U kunt de weergave Docker Explorer in Visual Studio Code gebruiken om de status van de module te zien.

   ![Status van simulatormodule](media/how-to-develop-csharp-module/simulator-status.png)

   De **edgeHubDev-container** is de kern van de lokale IoT Edge simulator. Deze kan worden uitgevoerd op uw ontwikkelmachine zonder de IoT Edge beveiligingsdemon en biedt omgevingsinstellingen voor uw systeemeigen module-app of modulecontainers. De **invoercontainer** bevat REST API's om berichten te helpen overbruggen naar het doelinvoerkanaal in uw module.

### <a name="debug-module-in-launch-mode"></a>Foutopsporingsmodule in de startmodus

1. Bereid uw omgeving voor op foutopsporing overeenkomstig de vereisten van uw ontwikkelingstaal, stel een onderbrekingspunt in uw module in en selecteer de foutopsporingsconfiguratie die u wilt gebruiken:
   - **C#**
     - In de Visual Studio Code geïntegreerde terminal wijzigt u de map in de ***&lt; naammap &gt;*** van de module en voer vervolgens de volgende opdracht uit om de .NET Core-toepassing te bouwen.

       ```cmd
       dotnet build
       ```

     - Open het bestand `Program.cs` en voeg een onderbrekingspunt toe.

     - Navigeer naar de Visual Studio code voor foutopsporing door **Weergave > foutopsporing te selecteren.** Selecteer de foutopsporingsconfiguratie **_&lt; van &gt; de modulenaam_ Lokale foutopsporing (.NET Core)** in de vervolgkeuzekeuze.

        > [!NOTE]
        > Als uw .NET Core niet consistent is met het programmapad in , moet u het programmapad in handmatig bijwerken zodat het overeenkomt met het in uw `TargetFramework` `launch.json` `launch.json` .csproj-bestand, zodat Visual Studio Code dit programma kan `TargetFramework` starten.

   - **Node.js**
     - In de Visual Studio Code geïntegreerde terminal wijzigt u de map in de naammap van de ***&lt; module &gt;*** en voer vervolgens de volgende opdracht uit om Node-pakketten te installeren

       ```cmd
       npm install
       ```

     - Open het bestand `app.js` en voeg een onderbrekingspunt toe.

     - Navigeer naar Visual Studio code-foutopsporingsweergave door **Weergave > foutopsporing te selecteren.** Selecteer de foutopsporingsconfiguratie **_&lt; van &gt; uw modulenaam_ Lokale foutopsporing (Node.js) in** de vervolgkeuzekeuze.
   - **Java**
     - Open het bestand `App.java` en voeg een onderbrekingspunt toe.

     - Navigeer naar Visual Studio code-foutopsporingsweergave door **Weergave > foutopsporing te selecteren.** Selecteer de foutopsporingsconfiguratie **_&lt; van &gt; uw modulenaam_ Lokale foutopsporing (Java)** in de vervolgkeuzekeuze.

1. Klik **op Foutopsporing starten** of druk op **F5** om de foutopsporingssessie te starten.

1. Voer in Visual Studio met code geïntegreerde terminal de volgende opdracht uit om een bericht Hallo wereld **verzenden** naar uw module. Dit is de opdracht die in de vorige stappen is weergegeven bij het instellen van IoT Edge simulator.

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > Als u Windows gebruikt, moet u ervoor zorgen dat de shell van uw geïntegreerde Visual Studio Code-terminal **Git Bash** of **WSL Bash is.** U kunt de opdracht `curl` niet uitvoeren vanaf een PowerShell of opdrachtprompt.
   > [!TIP]
   > U kunt ook [PostMan of andere](https://www.getpostman.com/) API-hulpprogramma's gebruiken om berichten via te verzenden in plaats van `curl` .

1. In de Visual Studio Code Debug ziet u de variabelen in het linkerpaneel.

1. Als u de debugging-sessie wilt stoppen, selecteert u de knop Stoppen of drukt u op **Shift + F5** en vervolgens **Azure IoT Edge: Stop IoT Edge Simulator** in het opdrachtenpalet om de simulator te stoppen en op te schonen.

## <a name="debug-in-attach-mode-with-iot-edge-simulator-c-nodejs-java-azure-functions"></a>Fouten opsporen in de modus koppelen met IoT Edge Simulator (C#, Node.js, Java, Azure Functions)

Uw standaardoplossing bevat twee modules, een is een gesimuleerde temperatuursensormodule en de andere is de pijpmodule. De gesimuleerde temperatuursensor verzendt berichten naar de pijpmodule en vervolgens worden de berichten doorspijpen naar de IoT Hub. In de modulemap die u hebt gemaakt, staan verschillende Docker-bestanden voor verschillende containertypen. Gebruik een van de bestanden die eindigen op de extensie **.debug** om uw module te bouwen voor testen.

Op dit moment wordt debugging in de attach-modus alleen als volgt ondersteund:

- C#-modules, inclusief modules voor Azure Functions, ondersteunen foutopsporing in Linux amd64-containers
- Node.js-modules bieden ondersteuning voor debuggen in Linux amd64- en arm32v7-containers en Windows amd64-containers
- Java-modules ondersteunen debuggen in Linux amd64- en arm32v7-containers

> [!TIP]
> U kunt schakelen tussen opties voor het standaardplatform voor uw IoT Edge oplossing door op het item in de Visual Studio Codestatusbalk te klikken.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>Een IoT Edge voor een IoT Edge oplossing

Op uw ontwikkelmachine kunt u een IoT Edge-simulator starten in plaats van de IoT Edge-beveiligingsdeemon te installeren, zodat u uw IoT Edge-oplossing kunt uitvoeren.

1. Klik in Device Explorer aan de linkerkant met de rechtermuisknop op uw IoT Edge-apparaat-id en selecteer vervolgens **Setup IoT Edge Simulator** om de simulator te starten met de connection string.

1. U kunt zien IoT Edge simulator is ingesteld door de voortgangsdetails te lezen in de geïntegreerde terminal.

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>Container bouwen en uitvoeren voor foutopsporing en foutopsporing in de attach-modus

1. Open het modulebestand ( `Program.cs` , , of ) en voeg een `app.js` `App.java` `<your module name>.cs` onderbrekingspunt toe.

1. Klik in Visual Studio Code Explorer-weergave met de rechtermuisknop op het bestand voor uw oplossing en selecteer vervolgens `deployment.debug.template.json` Build and Run IoT Edge solution in **Simulator**. U kunt alle modulecontainerlogboeken in hetzelfde venster bekijken. U kunt ook naar de Docker-weergave navigeren om de containerstatus te bekijken.

   ![Variabelen bekijken](media/how-to-vs-code-develop-module/view-log.png)

1. Navigeer naar Visual Studio Code debug-weergave en selecteer het configuratiebestand voor foutopsporing voor uw module. De naam van de foutopsporingsoptie moet vergelijkbaar zijn met ***&lt; de naam van uw &gt; module* Remote Debug**

1. Selecteer **Start Debugging of** druk op **F5.** Selecteer het proces waar u aan wilt koppelen.

1. In Visual Studio code foutopsporingsweergave ziet u de variabelen in het linkerpaneel.

1. Als u de debugging-sessie wilt stoppen, selecteert u eerst de knop Stoppen of drukt u op **Shift + F5** en selecteert u **Azure IoT Edge: Stop IoT Edge Simulator** in het opdrachtpalet.

> [!NOTE]
> In het voorgaande voorbeeld ziet u hoe u fouten kunt opsporen IoT Edge modules in containers. Er zijn poorten aan de containerinstellingen van uw module `createOptions` toegevoegd. Nadat u klaar bent met het debuggen van uw modules, raden we u aan deze beschikbaar gemaakt poorten te verwijderen voor IoT Edge modules.
>
> Voor modules die zijn geschreven in C#, waaronder Azure Functions, is dit voorbeeld gebaseerd op de foutopsporingsversie van , die tijdens het bouwen het `Dockerfile.amd64.debug` .NET Core-opdrachtregeldebugger (VSDBG) in uw containerafbeelding bevat. Nadat u fouten in uw C#-modules hebt opsporen, raden we u aan om de Dockerfile zonder VSDBG rechtstreeks te gebruiken voor productie-IoT Edge modules.

## <a name="debug-a-module-with-the-iot-edge-runtime"></a>Fouten opsporen in een module met de IoT Edge runtime

In elke modulemap zijn er verschillende Docker-bestanden voor verschillende containertypen. Gebruik een van de bestanden die eindigen met de extensie **.debug om** uw module te bouwen voor testen.

Bij het debuggen van modules met behulp van deze methode, worden uw modules uitgevoerd boven op de IoT Edge runtime. Het IoT Edge-apparaat en uw Visual Studio-code kunnen zich op dezelfde computer of meer normaal gesproken Visual Studio Code op de ontwikkelmachine en de IoT Edge-runtime en -modules worden uitgevoerd op een andere fysieke computer. Als u fouten wilt opsporen in Visual Studio Code, moet u het volgende doen:

- Stel uw IoT Edge-apparaat in, bouw uw IoT Edge-module(s) met de Dockerfile **.debug** en implementeer deze vervolgens op IoT Edge apparaat.
- Maak het IP-adres en de poort van de module weer zodat het debugger kan worden gekoppeld.
- Werk de `launch.json` bij zodat Visual Studio Code kan worden gekoppeld aan het proces in de container op de externe computer. Dit bestand bevindt zich in de map in uw werkruimte en wordt telkens bijgewerkt wanneer u een nieuwe module toevoegt die ondersteuning biedt `.vscode` voor debuggen.

### <a name="build-and-deploy-your-module-to-the-iot-edge-device"></a>Uw module bouwen en implementeren op IoT Edge apparaat

1. Open Visual Studio Code het bestand dat de foutopsporingsversie van uw module-afbeeldingen bevat `deployment.debug.template.json` met de `createOptions` juiste waarden.

1. Als u uw module in Python ontwikkelt, volgt u deze stappen voordat u doorgaat:
   - Open het bestand `main.py` en voeg deze code toe na de importsectie:

      ```python
      import ptvsd
      ptvsd.enable_attach(('0.0.0.0',  5678))
      ```

   - Voeg de volgende coderegel toe aan de callback die u wilt debuggen:

      ```python
      ptvsd.break_into_debugger()
      ```

     Als u bijvoorbeeld fouten in de functie wilt opsporen, voegt u die `receive_message_listener` regel code in zoals hieronder wordt weergegeven:

      ```python
      def receive_message_listener(client):
          ptvsd.break_into_debugger()
          global RECEIVED_MESSAGES
          while True:
              message = client.receive_message_on_input("input1")   # blocking call
              RECEIVED_MESSAGES += 1
              print("Message received on input1")
              print( "    Data: <<{}>>".format(message.data) )
              print( "    Properties: {}".format(message.custom_properties))
              print( "    Total calls received: {}".format(RECEIVED_MESSAGES))
              print("Forwarding message to output1")
              client.send_message_to_output(message, "output1")
              print("Message successfully forwarded")
      ```

1. In het opdrachtpalet Visual Studio Code:
   1. Voer de opdracht **Azure IoT Edge: Build and Push IoT Edge solution**.

   1. Selecteer het `deployment.debug.template.json` bestand voor uw oplossing.

1. In het **Azure IoT Hub Apparaten van** de Visual Studio codeverkennerweergave:
   1. Klik met de rechtermuisknop op IoT Edge apparaat-id en selecteer **implementatie voor één apparaat maken.**

      > [!TIP]
      > Als u wilt controleren **of** het apparaat dat u hebt gekozen een IoT Edge-apparaat is, selecteert u het om de lijst met modules uit te vouwen en de aanwezigheid van $edgeHub en **$edgeAgent**. Elk IoT Edge bevat deze twee modules.

   1. Navigeer naar de **configuratiemap van uw** oplossing, selecteer het `deployment.debug.amd64.json` bestand en selecteer vervolgens **Edge-distributiemanifest selecteren.**

U ziet dat de implementatie is gemaakt met een implementatie-id in de geïntegreerde terminal.

U kunt de containerstatus controleren door de opdracht `docker ps` uit te voeren in de terminal. Als uw Visual Studio code en IoT Edge runtime op dezelfde computer worden uitgevoerd, kunt u ook de status controleren in Visual Studio Code Docker-weergave.

### <a name="expose-the-ip-and-port-of-the-module-for-the-debugger"></a>Het IP-adres en de poort van de module voor het debugger blootstellen

U kunt deze sectie overslaan als uw modules worden uitgevoerd op dezelfde computer als Visual Studio Code, omdat u localhost gebruikt om aan de container te koppelen en al de juiste poortinstellingen hebt in het **.debug** Dockerfile, de containerinstellingen en het bestand van de `createOptions` `launch.json` module. Als uw modules en Visual Studio code op afzonderlijke computers worden uitgevoerd, volgt u de stappen voor uw ontwikkelingstaal.

- **C#, inclusief Azure Functions**

  [Configureer het SSH-kanaal op uw ontwikkelmachine en](https://github.com/OmniSharp/omnisharp-vscode/wiki/Attaching-to-remote-processes) IoT Edge apparaat en bewerk `launch.json` vervolgens het bestand dat u wilt koppelen.

- **Node.js**

  - Zorg ervoor dat de module op de computer waar de foutopsporing moet worden uitgevoerd en gereed is voor het koppelen van foutopsporings opsporen, en dat poort 9229 extern toegankelijk is. U kunt dit controleren door te openen `http://<target-machine-IP>:9229/json` op de machine met het debugger. In deze URL wordt informatie weergegeven over de Node.js module die moet worden bespord.
  
  - Open Visual Studio Code op uw ontwikkelmachine en bewerk deze zodat de adreswaarde van het profiel Remote Debug (Node.js) van de `launch.json` ***&lt; &gt; modulenaam* (remote** **_&lt; &gt;_ debugNode.js in Windows Container)** als de module wordt uitgevoerd als een Windows-container, het IP-adres is van de machine die wordt foutopsporing uitgevoerd.

- **Java**

  - Bouw een SSH-tunnel naar de computer om debuggen te worden door uit te laten. `ssh -f <username>@<target-machine> -L 5005:127.0.0.1:5005 -N`
  
  - Open op uw ontwikkelmachine Visual Studio Code en bewerk het ***&lt; &gt; profiel* voor de modulenaam Remote Debug (Java)** in, zodat u deze kunt koppelen `launch.json` aan de doelmachine. Zie de sectie over het configureren van `launch.json` het [debugger](https://code.visualstudio.com/docs/java/java-debugging#_configuration)voor meer informatie over het bewerken en debuggen van Java met Visual Studio Code.

- **Python**

  - Zorg ervoor dat poort 5678 op de computer die moet worden ontspord, is geopend en toegankelijk is.

  - In de code die u eerder hebt ingevoegd in , wijzigt u `ptvsd.enable_attach(('0.0.0.0', 5678))` `main.py` **0.0.0.0** in het IP-adres van de computer waar debug wordt uitgevoerd. Bouw, push en implementeer uw IoT Edge module opnieuw.

  - Open op uw ontwikkelmachine Visual Studio Code en bewerk deze zodat de waarde van het profiel Remote `launch.json` `host` **Debug (Python) voor de *&lt; modulenaam &gt;*** het IP-adres van de doelmachine gebruikt in plaats van `localhost` .

### <a name="debug-your-module"></a>Fouten opsporen in uw module

1. Selecteer in Visual Studio Code Debug-weergave het configuratiebestand voor foutopsporing voor uw module. De naam van de foutopsporingsoptie moet vergelijkbaar zijn met de ***&lt; naam van uw module Remote &gt;* Debug**

1. Open het modulebestand voor uw ontwikkelingstaal en voeg een onderbrekingspunt toe:

   - **Azure Function (C#)**: Voeg uw onderbrekingspunt toe aan het bestand `<your module name>.cs` .
   - **C#**: Voeg uw onderbrekingspunt toe aan het bestand `Program.cs` .
   - **Node.js:** voeg uw onderbrekingspunt toe aan het bestand `app.js` .
   - **Java:** Voeg uw onderbrekingspunt toe aan het bestand `App.java` .
   - **Python:** voeg uw onderbrekingspunt toe aan het bestand `main.py` in de callback-methode waar u de regel hebt `ptvsd.break_into_debugger()` toegevoegd.
   - **C:** Voeg uw onderbrekingspunt toe aan het bestand `main.c` .

1. Selecteer **Start Debugging** of selecteer **F5.** Selecteer het proces waar u aan wilt koppelen.

1. In de Visual Studio code voor foutopsporing ziet u de variabelen in het linkerpaneel.

> [!NOTE]
> In het voorgaande voorbeeld ziet u hoe u fouten kunt opsporen IoT Edge modules op containers. Er zijn poorten aan de containerinstellingen van uw module `createOptions` toegevoegd. Nadat u klaar bent met het debuggen van uw modules, raden we u aan deze beschikbaar gemaakt poorten te verwijderen voor productie-IoT Edge modules.

## <a name="build-and-debug-a-module-remotely"></a>Een module op afstand bouwen en fouten opsporen

Met recente wijzigingen in zowel de Docker- als Moby-engines ter ondersteuning van SSH-verbindingen en een nieuwe instelling in Azure IoT Tools waarmee omgevingsinstellingen kunnen worden injectie in het opdrachtpalet van Visual Studio Code en Azure IoT Edge terminals, kunt u nu modules bouwen en fouten opsporen op externe apparaten.

Zie dit [IoT Developer-blogbericht](https://devblogs.microsoft.com/iotdev/easily-build-and-debug-iot-edge-modules-on-your-remote-device-with-azure-iot-edge-for-vs-code-1-9-0/) voor meer informatie en stapsgewijse instructies.

## <a name="next-steps"></a>Volgende stappen

Nadat u uw module hebt gebouwd, leert u hoe u de modules [Azure IoT Edge implementeren vanuit Visual Studio Code](how-to-deploy-modules-vscode.md).

Als u modules wilt ontwikkelen voor uw IoT Edge apparaten, [moet u de SDK'Azure IoT Hub begrijpen en gebruiken.](../iot-hub/iot-hub-devguide-sdks.md)