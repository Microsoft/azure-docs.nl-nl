---
title: Modules voor het ontwikkelen en opsporen van fouten voor Azure IoT Edge | Microsoft Docs
description: Visual Studio code gebruiken voor het ontwikkelen, bouwen en opsporen van fouten in een module voor Azure IoT Edge met C#, Python, Node.js, Java of C
services: iot-edge
keywords: ''
author: kgremban
ms.author: kgremban
ms.date: 08/07/2019
ms.topic: conceptual
ms.service: iot-edge
ms.custom: devx-track-js
ms.openlocfilehash: ebc12e6d64d015267497497bebc22c8586adf999
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995910"
---
# <a name="use-visual-studio-code-to-develop-and-debug-modules-for-azure-iot-edge"></a>Visual Studio code gebruiken voor het ontwikkelen en opsporen van fouten in modules voor Azure IoT Edge

U kunt uw bedrijfs logica omzetten in modules voor Azure IoT Edge. Dit artikel laat u zien hoe u Visual Studio code kunt gebruiken als het belangrijkste hulp programma voor het ontwikkelen en opsporen van fouten in modules.

Er zijn twee manieren om fouten op te sporen in C#-, Node.js-of Java-modules in Visual Studio code: u kunt een proces in een module container koppelen of de module code in de foutopsporingsmodus starten. Als u fouten wilt opsporen in modules die in Python of C zijn geschreven, kunt u alleen aan een proces in linux amd64-containers koppelen.

Lees over [fout opsporing](https://code.visualstudio.com/Docs/editor/debugging)als u niet bekend bent met de mogelijkheden voor fout opsporing van Visual Studio code.

Dit artikel bevat instructies voor het ontwikkelen en debuggen van modules in meerdere talen voor meerdere architecturen. Visual Studio code biedt momenteel ondersteuning voor modules die zijn geschreven in C#, C, Python, Node.js en Java. De ondersteunde architecturen voor apparaten zijn x64 en ARM32. Zie [ondersteuning voor taal en architectuur](module-development.md#language-and-architecture-support)voor meer informatie over ondersteunde besturings systemen, talen en architecturen.

>[!NOTE]
>Ondersteuning voor het ontwikkelen en opsporen van fouten voor Linux ARM64-apparaten is beschikbaar in de [open bare preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Zie [ARM64 IoT Edge-modules ontwikkelen in Visual Studio Code en er fouten in opsporen (preview)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

U kunt een computer of een virtuele machine met Windows, macOS of Linux gebruiken als uw ontwikkel computer. Op Windows-computers kunt u Windows-of linux-modules ontwikkelen. Voor het ontwikkelen van Windows-modules gebruikt u een Windows-computer met versie 1809/build 17763 of hoger. Voor het ontwikkelen van linux-modules gebruikt u een Windows-computer die voldoet aan de [vereisten voor docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).

Installeer eerst [Visual Studio code](https://code.visualstudio.com/) en voeg vervolgens de volgende uitbrei dingen toe:

- [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
- [Docker-extensie](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
- Visual Studio-extensie (s) die specifiek zijn voor de taal die u wilt ontwikkelen in:
  - C#, waaronder Azure Functions: [C#-uitbrei ding](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
  - Python: [python-extensie](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  - Java: [Java Extension Pack voor Visual Studio code](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
  - C: [c/C++-extensie](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)

U moet ook extra, taalspecifieke hulpprogram ma's installeren om uw module te ontwikkelen:

- C#, inclusief Azure Functions: [.net Core 2,1 SDK](https://www.microsoft.com/net/download)

- Python: [python](https://www.python.org/downloads/) en [PIP](https://pip.pypa.io/en/stable/installing/#installation) voor het installeren van Python-pakketten (doorgaans opgenomen in uw python-installatie).

- Node.js: [Node.js](https://nodejs.org). U wilt ook [Yeoman](https://www.npmjs.com/package/yo) en de [Module Generator van Azure IOT Edge Node.js](https://www.npmjs.com/package/generator-azure-iot-edge-module)installeren.

- Java: [Java SE Development Kit 10](/azure/developer/java/fundamentals/java-jdk-long-term-support) en [maven](https://maven.apache.org/). U moet [de `JAVA_HOME` omgevings variabele instellen](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) om naar uw jdk-installatie te verwijzen.

Als u uw module-installatie kopie wilt bouwen en implementeren, moet u docker gebruiken om de module installatie kopie te bouwen en een container register voor de module installatie kopie:

- [Docker Community Edition](https://docs.docker.com/install/) op uw ontwikkel computer.

- [Azure container Registry](../container-registry/index.yml) of [docker hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

    > [!TIP]
    > U kunt een lokaal docker-REGI ster gebruiken voor prototype-en test doeleinden in plaats van een Cloud register.

Tenzij u uw module in C ontwikkelt, hebt u ook het op python gebaseerde [Azure IOT EdgeHub dev tool](https://pypi.org/project/iotedgehubdev/) nodig om uw lokale ontwikkel omgeving in te stellen voor het opsporen, uitvoeren en testen van uw IOT EDGE-oplossing. Als u dit nog niet hebt gedaan, installeert u [python (2.7/3.6/3.7) en PIP](https://www.python.org/) en installeert u **iotedgehubdev** door deze opdracht uit te voeren in uw Terminal.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```
   
> [!NOTE]
> Op dit moment gebruikt iotedgehubdev een docker-py-bibliotheek die niet compatibel is met python 3,8.
>
> Als u meerdere python hebt, waaronder vooraf geïnstalleerde Python 2,7 (bijvoorbeeld op Ubuntu of macOS), moet u ervoor zorgen dat u de juiste `pip` of `pip3` installeert **iotedgehubdev** gebruikt

Als u uw module op een apparaat wilt testen, hebt u een actieve IoT-hub met ten minste één IoT Edge apparaat nodig. Volg de stappen in de Quick start voor [Linux](quickstart-linux.md) of [Windows](quickstart.md)om uw computer als IOT edge apparaat te gebruiken. Als IoT Edge daemon wordt uitgevoerd op uw ontwikkel computer, moet u mogelijk EdgeHub en EdgeAgent stoppen voordat u verdergaat met de volgende stap.

## <a name="create-a-new-solution-template"></a>Een nieuwe oplossings sjabloon maken

In de volgende stappen ziet u hoe u een IoT Edge module maakt in uw voorkeurs taal (waaronder Azure Functions, geschreven in C#) met behulp van Visual Studio code en de Azure IoT-Hulpprogram Ma's. U begint met het maken van een oplossing en het genereren van de eerste module in die oplossing. Elke oplossing kan meerdere modules bevatten.

1. Selecteer **View**  >  **opdracht palet** weer geven.

1. Voer in het opdracht palet de opdracht **Azure IOT Edge: nieuwe IOT EDGE-oplossing** in en voer deze uit.

   ![Nieuwe IoT Edge oplossing uitvoeren](./media/how-to-develop-csharp-module/new-solution.png)

1. Blader naar de map waar u de nieuwe oplossing wilt maken en selecteer vervolgens **map selecteren**.

1. Voer een naam in voor uw oplossing.

1. Selecteer een module sjabloon voor uw voorkeurs taal voor ontwikkeling als de eerste module in de oplossing.

1. Voer een naam in voor uw module. Kies een unieke naam in het container register.

1. Geef de naam op van de installatie kopie opslagplaats van de module. Visual Studio code vult de module naam automatisch in met **localhost: 5000/<uw module naam \>**. Vervang deze door uw eigen register gegevens. Als u een lokaal docker-REGI ster gebruikt voor het testen, is **localhost** prima. Als u Azure Container Registry gebruikt, gebruikt u de aanmeldings server uit de instellingen van het REGI ster. De aanmeldings server ziet eruit als **_\<registry name\>_ . azurecr.io**. Vervang alleen het gedeelte **localhost: 5000** van de teken reeks, zodat het uiteindelijke resultaat eruitziet als **\<*registry name*\> . azurecr.io/ _\<your module name\>_**.

   ![Opslagplaats voor Docker-installatiekopieën opgeven](./media/how-to-develop-csharp-module/repository.png)

Visual Studio code voert de door u verstrekte informatie, maakt een IoT Edge oplossing en laadt deze vervolgens in een nieuw venster.

Er zijn vier items in de oplossing:

- Een **. vscode** -map bevat configuraties voor fout opsporing.

- Een map met **modules** bevat submappen voor elke module.  In de map voor elke module bevindt zich een bestand **module.js**, dat bepaalt hoe modules worden gebouwd en geïmplementeerd.  Dit bestand moet worden gewijzigd om het module-implementatie container register van localhost te wijzigen in een extern REGI ster. Op dit moment hebt u slechts één module.  U kunt echter meer in het opdracht palet toevoegen met de opdracht **Azure IOT Edge: IOT Edge module toevoegen**.

- Een **. env** -bestand bevat uw omgevings variabelen. Als Azure Container Registry uw REGI ster is, hebt u een Azure Container Registry gebruikers naam en wacht woord.

  > [!NOTE]
  > Het omgevings bestand wordt alleen gemaakt als u een opslag plaats voor installatie kopieën voor de module opgeeft. Als u de standaard waarden voor localhost hebt geaccepteerd om lokaal te testen en fouten op te sporen, hoeft u geen omgevings variabelen te declareren.

- Een **deployment.template.jsin** het bestand bevat een lijst met uw nieuwe module, samen met een **SimulatedTemperatureSensor** -module die gegevens simuleert die u voor het testen kunt gebruiken. Zie informatie over het [gebruik van implementatie manifesten voor het implementeren van modules en het tot stand brengen van routes](module-composition.md)voor meer informatie over de werking van implementatie manifesten.

Als u wilt zien hoe de gesimuleerde temperatuur module werkt, bekijkt u de [bron code SimulatedTemperatureSensor. csproj](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor).

## <a name="add-additional-modules"></a>Aanvullende modules toevoegen

Als u extra modules wilt toevoegen aan uw oplossing, voert u de opdracht uit **Azure IOT Edge: IOT Edge module toevoegen** vanuit het opdracht palet. U kunt ook met de rechter muisknop op de map **modules** of het `deployment.template.json` bestand in de Visual Studio code Explorer-weer gave klikken en vervolgens **IOT Edge module toevoegen** selecteren.

## <a name="develop-your-module"></a>Uw module ontwikkelen

De standaard module code die bij de oplossing hoort, bevindt zich op de volgende locatie:

- Azure function (C#): **modules > *&lt; de &gt;* module naam van  >  *&lt; de module &gt;* naam. cs**
- C#: **modules > *&lt; uw module naam &gt;* > Program.cs**
- Python: **modules > *&lt; uw module naam &gt;* > Main.py**
- Node.js: **modules > *&lt; de naam &gt; van de module* > app.js**
- Java: **modules > *&lt; de module naam &gt;* > src > main > java > com > edgemodulemodules > app. java**
- C: **modules > *&lt; uw module naam &gt;* > Main. c**

De module en de deployment.template.jsvoor het bestand worden zo ingesteld dat u de oplossing kunt bouwen, deze naar het container register pusht en deze implementeert op een apparaat om te testen zonder dat u code hoeft te gebruiken. De module is gebouwd om simpelweg invoer van een bron (in dit geval de SimulatedTemperatureSensor-module die gegevens simuleert) te maken en deze te IoT Hub.

Wanneer u klaar bent om de sjabloon aan te passen met uw eigen code, gebruikt u de [Azure IOT hub sdk's](../iot-hub/iot-hub-devguide-sdks.md) om modules te bouwen die voldoen aan de belangrijkste behoeften voor IOT-oplossingen zoals beveiliging, Apparaatbeheer en betrouw baarheid.

## <a name="debug-a-module-without-a-container-c-nodejs-java"></a>Fouten opsporen in een module zonder container (C#, Node.js, Java)

Als u in C#, Node.js of Java ontwikkelt, vereist uw module het gebruik van een **ModuleClient** -object in de standaard module code zodat het kan starten, uitvoeren en routeren van berichten. U gebruikt ook het standaard invoer kanaal **input1** om actie te ondernemen wanneer de module berichten ontvangt.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>IoT Edge Simulator voor IoT Edge oplossing instellen

U kunt op uw ontwikkel computer een IoT Edge Simulator starten in plaats van de IoT Edge-beveiligings-daemon te installeren, zodat u uw IoT Edge-oplossing kunt uitvoeren.

1. Klik in Device Explorer aan de linkerkant met de rechter muisknop op uw IoT Edge apparaat-ID en selecteer vervolgens **Setup IOT Edge Simulator** om de Simulator te starten met de Connection String van het apparaat.
1. U kunt zien dat de IoT Edge Simulator is ingesteld door de details van de voortgang in de geïntegreerde Terminal te lezen.

### <a name="set-up-iot-edge-simulator-for-single-module-app"></a>IoT Edge Simulator instellen voor een app met één module

Als u de Simulator wilt instellen en starten, voert u de opdracht uit **Azure IOT Edge: start IOT Edge hub Simulator voor een enkele module** vanuit het Visual Studio code-opdracht palet. Als u hierom wordt gevraagd, gebruikt u de waarde **input1** uit de standaard module code (of de equivalente waarde uit uw code) als de invoer naam voor uw toepassing. Met de opdracht wordt de **iotedgehubdev** cli geactiveerd en wordt vervolgens de container van de module IOT Edge Simulator en een test hulpprogramma gestart. U kunt de onderstaande uitvoer in de geïntegreerde terminal bekijken als de Simulator is gestart in de modus voor één module. U kunt ook een opdracht bekijken voor het verzenden van een `curl` bericht via. U gebruikt dit later.

   ![IoT Edge Simulator instellen voor een app met één module](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   U kunt de weer gave docker-Verkenner in Visual Studio code gebruiken om de uitvoerings status van de module te bekijken.

   ![Status van Simulator-module](media/how-to-develop-csharp-module/simulator-status.png)

   De **edgeHubDev** -container is de kern van de lokale IOT Edge Simulator. Het kan worden uitgevoerd op uw ontwikkel computer zonder de IoT Edge Security daemon en biedt omgevings instellingen voor de app of module containers van uw systeem eigen module. De **invoer** container geeft rest-api's weer om berichten over te dragen aan het doel invoer kanaal in uw module.

### <a name="debug-module-in-launch-mode"></a>Fouten opsporen in module in de start modus

1. Uw omgeving voorbereiden op fout opsporing volgens de vereisten van uw ontwikkel taal, een onderbrekings punt in uw module instellen en de configuratie voor fout opsporing selecteren die moet worden gebruikt:
   - **C#**
     - Wijzig in de Visual Studio code Integrated-Terminal de map in de map **_&lt; your module &gt; name_* _ en voer vervolgens de volgende opdracht uit om de .net core-toepassing te bouwen.

       ```cmd
       dotnet build
       ```

     - Open het bestand `Program.cs` en voeg een onderbrekings punt toe.

     - Ga naar de weer gave fout opsporing van Visual Studio-code door _* weer gave > debug * * te selecteren. Selecteer de configuratie voor fout opsporing * *_ &lt; uw module naam &gt; _ Local debug (.net core) * * in de vervolg keuzelijst.

        > [!NOTE]
        > Als uw .NET core `TargetFramework` niet consistent is met uw programmapad in `launch.json` , moet u het programmapad hand matig bijwerken zodat `launch.json` deze overeenkomt met de `TargetFramework` in uw. csproj-bestand zodat Visual Studio code dit programma kan starten.

   - **Node.js**
     - Wijzig in de Visual Studio code Integrated-Terminal de map in de map **_&lt; your module &gt; name_* _ en voer vervolgens de volgende opdracht uit om knooppunt pakketten te installeren

       ```cmd
       npm install
       ```

     - Open het bestand `app.js` en voeg een onderbrekings punt toe.

     - Ga naar de weer gave fout opsporing van Visual Studio-code door _* weer gave > debug * * te selecteren. Selecteer de configuratie voor fout opsporing * *_ &lt; uw module naam &gt; _ Local debug (Node.js) * * in de vervolg keuzelijst.
   - **Java**
     - Open het bestand `App.java` en voeg een onderbrekings punt toe.

     - Ga naar de weer gave fout opsporing Visual Studio-code door **weer gave > debug** te selecteren. Selecteer in de vervolg keuzelijst de **_&lt; naam &gt;_** van de module voor fout opsporing in het configuratie programma.

1. Klik op **fout opsporing starten** of druk op **F5** om de foutopsporingssessie te starten.

1. Voer in de Visual Studio code geïntegreerde Terminal de volgende opdracht uit om een **Hallo wereld** -bericht naar uw module te verzenden. Dit is de opdracht die wordt weer gegeven in de vorige stappen bij het instellen van IoT Edge Simulator.

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > Als u Windows gebruikt, zorgt u ervoor dat de shell van uw Visual Studio code-geïntegreerde Terminal **Git Bash** of **WSL bash** is. U kunt de opdracht niet uitvoeren `curl` vanuit een Power shell-of opdracht prompt.
   > [!TIP]
   > U kunt ook [postman](https://www.getpostman.com/) of andere API-hulpprogram ma's gebruiken om berichten te verzenden in plaats van `curl` .

1. In de weer gave Visual Studio code debug ziet u de variabelen in het linkerdeel venster.

1. Als u de foutopsporingssessie wilt stoppen, selecteert u de knop stoppen of drukt u op **SHIFT + F5** en voert u vervolgens **Azure IoT Edge: stop IOT Edge Simulator** in het opdracht palet om de Simulator te stoppen en op te schonen.

## <a name="debug-in-attach-mode-with-iot-edge-simulator-c-nodejs-java-azure-functions"></a>Fout opsporing in de modus attach with IoT Edge Simulator (C#, Node.js, Java Azure Functions)

Uw standaard oplossing bevat twee modules, een is een gesimuleerde temperatuur sensor module en de andere is de pipe-module. De gesimuleerde temperatuur sensor verzendt berichten naar de module pipe en vervolgens worden de berichten naar de IoT Hub verzonden. In de map module die u hebt gemaakt, zijn er verschillende docker-bestanden voor verschillende container typen. Gebruik een van de bestanden die met de extensie eindigen **. debug** voor het maken van de module voor testen.

Op dit moment wordt fout opsporing in de modus koppelen alleen als volgt ondersteund:

- C#-modules, met inbegrip van die voor Azure Functions, ondersteuning voor fout opsporing in linux amd64-containers
- Node.js-modules bieden ondersteuning voor fout opsporing in linux amd64-en arm32v7-containers en Windows amd64-containers
- Java-modules bieden ondersteuning voor fout opsporing in linux amd64-en arm32v7-containers

> [!TIP]
> U kunt scha kelen tussen de opties voor het standaard platform voor uw IoT Edge oplossing door te klikken op het item in de status balk van Visual Studio code.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>IoT Edge Simulator voor IoT Edge oplossing instellen

U kunt in uw ontwikkel computer een IoT Edge Simulator starten in plaats van de IoT Edge-beveiligings-daemon te installeren, zodat u uw IoT Edge-oplossing kunt uitvoeren.

1. Klik in Device Explorer aan de linkerkant met de rechter muisknop op uw IoT Edge apparaat-ID en selecteer vervolgens **Setup IOT Edge Simulator** om de Simulator te starten met de Connection String van het apparaat.

1. U kunt zien dat de IoT Edge Simulator is ingesteld door de details van de voortgang in de geïntegreerde Terminal te lezen.

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>Container bouwen en uitvoeren voor fout opsporing en fout opsporing in de modus koppelen

1. Open het module bestand ( `Program.cs` , `app.js` , `App.java` of `<your module name>.cs` ) en voeg een onderbrekings punt toe.

1. Klik in de weer gave Visual Studio code Explorer met de rechter muisknop op het `deployment.debug.template.json` bestand voor uw oplossing en selecteer vervolgens **bouwen en uitvoeren IOT EDGE oplossing in Simulator**. U kunt alle module container logboeken bekijken in hetzelfde venster. U kunt ook naar de docker-weer gave navigeren om de container status te bekijken.

   ![Variabelen bekijken](media/how-to-vs-code-develop-module/view-log.png)

1. Ga naar de weer gave fout opsporing Visual Studio-code en selecteer het configuratie bestand voor fout opsporing voor uw module. De naam van de optie voor fout opsporing moet overeenkomen met de naam van de **_&lt; &gt; module_ externe fout opsporing**

1. Selecteer **Start Debugging** of druk op **F5**. Selecteer het proces dat u wilt koppelen.

1. In Visual Studio code debug weer gave ziet u de variabelen in het linkerdeel venster.

1. Als u de foutopsporingssessie wilt stoppen, selecteert u eerst de knop stoppen of drukt u op **SHIFT + F5** en selecteert u **Azure IoT Edge: stop IOT Edge Simulator** vanuit het opdracht palet.

> [!NOTE]
> In het vorige voor beeld ziet u hoe u fouten opspoort IoT Edge modules op containers. Er zijn weer gegeven poorten toegevoegd aan de container instellingen van uw module `createOptions` . Wanneer u klaar bent met het opsporen van fouten in uw modules, raden we u aan deze beschik bare poorten te verwijderen voor productie klare IoT Edge modules.
>
> In het geval van modules die zijn geschreven in C#, met inbegrip van Azure Functions, is dit voor beeld gebaseerd op de versie van de fout opsporing `Dockerfile.amd64.debug` , waaronder de .net core-opdracht regel Debugger (VSDBG) in uw container installatie kopie tijdens het bouwen ervan. Wanneer u fouten opspoort in uw C#-modules, raden we u aan om de Dockerfile direct te gebruiken zonder VSDBG voor productie klare IoT Edge modules.

## <a name="debug-a-module-with-the-iot-edge-runtime"></a>Fouten opsporen in een module met de IoT Edge runtime

In elke module map bevinden zich verschillende docker-bestanden voor verschillende container typen. Gebruik een van de bestanden die met de extensie eindigen **. debug** voor het maken van de module voor testen.

Bij het opsporen van fouten in modules met deze methode worden uw modules boven op de IoT Edge-runtime uitgevoerd. Het IoT Edge-apparaat en de Visual Studio-code kunnen zich op dezelfde computer bevinden, of meer meestal Visual Studio code bevindt zich op de ontwikkel computer en de IoT Edge runtime en modules worden uitgevoerd op een andere fysieke computer. Als u fouten wilt opsporen in Visual Studio code, moet u het volgende doen:

- Stel uw IoT Edge-apparaat in, bouw uw IoT Edge module (s) met de **. debug** Dockerfile en implementeer deze op het IOT edge apparaat.
- Het IP-adres en de poort van de module beschikbaar stellen zodat het fout opsporingsprogramma kan worden bijgevoegd.
- Werk de `launch.json` zodat Visual Studio code kan koppelen aan het proces in de container op de externe computer. Dit bestand bevindt zich in de `.vscode` map in uw werk ruimte en updates telkens wanneer u een nieuwe module toevoegt die fout opsporing ondersteunt.

### <a name="build-and-deploy-your-module-to-the-iot-edge-device"></a>Uw module bouwen en implementeren op het IoT Edge apparaat

1. Open in Visual Studio code het `deployment.debug.template.json` bestand, dat de foutopsporingsversie van uw module installatie kopieën bevat, waarbij de juiste `createOptions` waarden zijn ingesteld.

1. Als u uw module in python ontwikkelt, voert u de volgende stappen uit voordat u doorgaat:
   - Open het bestand `main.py` en voeg deze code toe na de sectie importeren:

      ```python
      import ptvsd
      ptvsd.enable_attach(('0.0.0.0',  5678))
      ```

   - Voeg de volgende regel code toe aan de retour aanroep die u wilt debuggen:

      ```python
      ptvsd.break_into_debugger()
      ```

     Als u bijvoorbeeld fouten wilt opsporen in de `receive_message_listener` functie, voegt u de volgende regel code toe, zoals hieronder wordt weer gegeven:

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

1. In het Visual Studio code-opdracht palet:
   1. Voer de opdracht uit **Azure IOT Edge: Build en Push IOT Edge Solution**.

   1. Selecteer het `deployment.debug.template.json` bestand voor uw oplossing.

1. In het gedeelte **apparaten van Azure IOT hub** van de weer gave Visual Studio code Explorer:
   1. Klik met de rechter muisknop op een IoT Edge apparaat-ID en selecteer **implementatie maken voor één apparaat**.

      > [!TIP]
      > Als u wilt controleren of het apparaat dat u hebt gekozen een IoT Edge apparaat is, selecteert u dit om de lijst met modules uit te vouwen en de aanwezigheid van **$edgeHub** en **$edgeAgent** te controleren. Elk IoT Edge apparaat bevat deze twee modules.

   1. Ga naar de map **config** van uw oplossing, selecteer het `deployment.debug.amd64.json` bestand en selecteer vervolgens **Edge-implementatie manifest selecteren**.

U ziet dat de implementatie is gemaakt met een implementatie-ID in de geïntegreerde Terminal.

U kunt de status van de container controleren door de `docker ps` opdracht uit te voeren in de Terminal. Als uw Visual Studio code en IoT Edge runtime op dezelfde computer worden uitgevoerd, kunt u de status ook controleren in de weer gave van Visual Studio code docker.

### <a name="expose-the-ip-and-port-of-the-module-for-the-debugger"></a>Het IP-adres en de poort van de module voor het fout opsporingsprogramma beschikbaar maken

U kunt deze sectie overs Laan als uw modules worden uitgevoerd op dezelfde computer als Visual Studio code, omdat u localhost gebruikt om aan de container te koppelen en al de juiste poort instellingen hebt in de **. debug** Dockerfile, de container instellingen van de module `createOptions` en het `launch.json` bestand. Als uw modules en Visual Studio code op afzonderlijke computers worden uitgevoerd, volgt u de stappen voor uw ontwikkelings taal.

- **C#, met inbegrip van Azure Functions**

  [Configureer het SSH-kanaal op uw ontwikkel computer en IOT edge apparaat](https://github.com/OmniSharp/omnisharp-vscode/wiki/Attaching-to-remote-processes) en bewerk vervolgens `launch.json` het bestand dat u wilt bijvoegen.

- **Node.js**

  - Zorg ervoor dat de module op de computer waarvoor fouten worden opgespoord, wordt uitgevoerd en gereed is voor het toevoegen van fout opsporing en dat poort 9229 extern toegankelijk is. U kunt dit controleren door te openen `http://<target-machine-IP>:9229/json` op de computer voor fout opsporing. Deze URL moet informatie weer geven over de Node.js-module waarvoor fouten worden opgespoord.
  
  - Open Visual Studio code op uw ontwikkel computer en bewerk dit `launch.json` zodat de adres waarde van het profiel van de **_&lt; module naam &gt;_ Remote Debug (Node.js)** (of **_&lt; uw module naam &gt;_ externe fout opsporing (Node.js in het Windows-container profiel)** is als de module wordt uitgevoerd als een Windows-container.

- **Java**

  - Bouw een SSH-tunnel naar de computer waarvoor u fouten wilt opsporen door uit te voeren `ssh -f <username>@<target-machine> -L 5005:127.0.0.1:5005 -N` .
  
  - Open Visual Studio code op uw ontwikkel computer en bewerk het profiel **_&lt; module naam &gt;_ Remote Debug (Java)** in `launch.json` , zodat u deze kunt koppelen aan de doel computer. `launch.json`Zie de sectie over [het configureren van het fout opsporingsprogramma](https://code.visualstudio.com/docs/java/java-debugging#_configuration)voor meer informatie over het bewerken en opsporen van fouten in Java met Visual Studio code.

- **Python**

  - Zorg ervoor dat poort 5678 op de computer waarvoor fouten worden opgespoord, open en toegankelijk is.

  - In de code `ptvsd.enable_attach(('0.0.0.0', 5678))` die u eerder hebt ingevoegd `main.py` , wijzigt u **0.0.0.0** in het IP-adres van de computer waarvoor u fouten wilt opsporen. Bouw, push en implementeer uw IoT Edge module opnieuw.

  - Open Visual Studio code op uw ontwikkel computer en bewerk dit `launch.json` zodat de `host` waarde van het profiel van de **_&lt; module naam &gt;_ Remote Debug (python)** het IP-adres van de doel computer gebruikt in plaats van `localhost` .

### <a name="debug-your-module"></a>Fouten opsporen in uw module

1. Selecteer in de weer gave fout opsporing Visual Studio-code het configuratie bestand voor fout opsporing voor uw module. De naam van de optie voor fout opsporing moet overeenkomen met de naam van de **_&lt; &gt; module_ externe fout opsporing**

1. Open het module bestand voor uw ontwikkel taal en voeg een onderbrekings punt toe:

   - **Azure function (C#)**: Voeg het onderbrekings punt toe aan het bestand `<your module name>.cs` .
   - **C#**: Voeg het onderbrekings punt toe aan het bestand `Program.cs` .
   - **Node.js**: Voeg het onderbrekings punt toe aan het bestand `app.js` .
   - **Java**: Voeg het onderbrekings punt toe aan het bestand `App.java` .
   - **Python**: Voeg het onderbrekings punt toe aan het bestand `main.py` in de call back methode waar u de regel hebt toegevoegd `ptvsd.break_into_debugger()` .
   - **C**: Voeg het onderbrekings punt toe aan het bestand `main.c` .

1. Selecteer **Start Debugging** of selecteer **F5**. Selecteer het proces dat u wilt koppelen.

1. In de weer gave Visual Studio code debug ziet u de variabelen in het linkerdeel venster.

> [!NOTE]
> In het vorige voor beeld ziet u hoe u fouten opspoort IoT Edge modules op containers. Er zijn weer gegeven poorten toegevoegd aan de container instellingen van uw module `createOptions` . Wanneer u klaar bent met het opsporen van fouten in uw modules, raden we u aan deze beschik bare poorten te verwijderen voor productie klare IoT Edge modules.

## <a name="build-and-debug-a-module-remotely"></a>Een module op afstand bouwen en fouten opsporen

Met recente wijzigingen in de docker-en Moby-engines ter ondersteuning van SSH-verbindingen en een nieuwe instelling in azure IoT-Hulpprogram Ma's die het invoegen van omgevings instellingen in het Visual Studio code-opdracht palet en Azure IoT Edge terminals mogelijk maakt, kunt u nu modules bouwen en fouten opsporen op externe apparaten.

Raadpleeg dit [IOT-ontwikkelaars blog item](https://devblogs.microsoft.com/iotdev/easily-build-and-debug-iot-edge-modules-on-your-remote-device-with-azure-iot-edge-for-vs-code-1-9-0/) voor meer informatie en stapsgewijze instructies.

## <a name="next-steps"></a>Volgende stappen

Nadat u uw module hebt gemaakt, leert u hoe u [Azure IOT Edge-modules kunt implementeren vanuit Visual Studio code](how-to-deploy-modules-vscode.md).

Voor het ontwikkelen van modules voor uw IoT Edge apparaten moet u [Azure IOT hub Sdk's begrijpen en gebruiken](../iot-hub/iot-hub-devguide-sdks.md).