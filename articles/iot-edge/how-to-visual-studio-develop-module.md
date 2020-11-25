---
title: Modules ontwikkelen en fouten opsporen in Visual Studio-Azure IoT Edge
description: Gebruik Visual Studio met Azure IoT-Hulpprogram Ma's om een C-of C# IoT Edge-module te ontwikkelen en deze vanuit uw IoT Hub naar een IoT-apparaat te pushen, zoals geconfigureerd door een implementatie manifest.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/27/2020
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 1883ffdff20bbbef8efec1440854f01a21a281dc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994516"
---
# <a name="use-visual-studio-2019-to-develop-and-debug-modules-for-azure-iot-edge"></a>Visual Studio 2019 gebruiken voor het ontwikkelen en opsporen van fouten in modules voor Azure IoT Edge

U kunt uw bedrijfs logica omzetten in modules voor Azure IoT Edge. Dit artikel laat u zien hoe u Visual Studio 2019 kunt gebruiken als het belangrijkste hulp programma voor het ontwikkelen en opsporen van fouten in modules.

De Azure IoT Edge-Hulpprogram Ma's voor Visual Studio bieden de volgende voor delen:

- Maken, bewerken, bouwen, uitvoeren en fouten opsporen Azure IoT Edge oplossingen en modules op uw lokale ontwikkel computer.
- Implementeer uw Azure IoT Edge-oplossing op Azure IoT Edge apparaat via Azure IoT Hub.
- Codeer uw Azure IoT-modules in C of C# en profiteer van de voor delen van de ontwikkeling van Visual Studio.
- Azure IoT Edge apparaten en modules beheren met de gebruikers interface.

Dit artikel laat u zien hoe u met de Azure IoT Edge-Hulpprogram Ma's voor Visual Studio 2019 uw IoT Edge modules kunt ontwikkelen. U leert ook hoe u uw project implementeert op uw Azure IoT Edge-apparaat. Visual Studio 2019 biedt momenteel ondersteuning voor modules die zijn geschreven in C en C#. De ondersteunde architecturen zijn Windows x64 en Linux x64 of ARM32. Zie [ondersteuning voor taal en architectuur](module-development.md#language-and-architecture-support)voor meer informatie over ondersteunde besturings systemen, talen en architecturen.
  
## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u een computer of virtuele machine met Windows gebruikt als uw ontwikkel computer. Op Windows-computers kunt u Windows-of linux-modules ontwikkelen. Voor het ontwikkelen van Windows-modules gebruikt u een Windows-computer met versie 1809/build 17763 of hoger. Voor het ontwikkelen van linux-modules gebruikt u een Windows-computer die voldoet aan de [vereisten voor docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).

Omdat in dit artikel Visual Studio 2019 als belangrijkste ontwikkel programma wordt gebruikt, installeert u Visual Studio. Zorg ervoor dat u de **Azure-ontwikkel** -en bureaublad ontwikkeling opneemt **met C++** -workloads in uw Visual Studio 2019-installatie. U kunt [Visual Studio 2019 wijzigen](/visualstudio/install/modify-visual-studio?view=vs-2019) om de vereiste werk belastingen toe te voegen.

Nadat uw Visual Studio 2019 gereed is, hebt u ook de volgende hulpprogram ma's en onderdelen nodig:

- Down load en Installeer [Azure IOT Edge-Hulpprogram ma's](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) van Visual Studio Marketplace om een IOT Edge-project te maken in visual studio 2019.

> [!TIP]
> Als u Visual Studio 2017 gebruikt, moet u [Azure IOT Edge-Hulpprogram ma's](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) voor VS 2017 downloaden en installeren via de Visual Studio Marketplace

- Down load en Installeer [docker Community Edition](https://docs.docker.com/install/) op uw ontwikkel computer om uw module-installatie kopieën te bouwen en uit te voeren. U moet docker CE instellen om te worden uitgevoerd in de Linux-container modus of de Windows-container modus.

- Stel uw lokale ontwikkel omgeving in op fout opsporing, uitvoering en test uw IoT Edge-oplossing door het [Azure IOT EdgeHub dev tool](https://pypi.org/project/iotedgehubdev/)te installeren. Installeer [python (2.7/3.6 +) en PIP](https://www.python.org/) en installeer het **iotedgehubdev** -pakket door de volgende opdracht uit te voeren in uw Terminal. Zorg ervoor dat de versie van het hulp programma Azure IoT EdgeHub dev groter is dan 0.3.0.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- Kloon de opslag plaats en installeer de Vcpkg-bibliotheek beheerder en installeer vervolgens het **Azure-IOT-SDK-c-pakket** voor Windows.

  ```cmd
  git clone https://github.com/Microsoft/vcpkg
  cd vcpkg
  bootstrap-vcpkg.bat
  ```

  ```cmd
  vcpkg.exe install azure-iot-sdk-c:x64-windows
  vcpkg.exe --triplet x64-windows integrate install
  ```

- [Azure container Registry](../container-registry/index.yml) of [docker hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags).

  > [!TIP]
  > U kunt een lokaal docker-REGI ster gebruiken voor prototype-en test doeleinden in plaats van een Cloud register.

- Als u uw module op een apparaat wilt testen, hebt u een actieve IoT-hub met ten minste één IoT Edge apparaat nodig. Volg de stappen in de Quick start voor [Linux](quickstart-linux.md) of [Windows](quickstart.md)om uw computer als IOT edge apparaat te gebruiken. Als IoT Edge daemon wordt uitgevoerd op uw ontwikkel computer, moet u mogelijk EdgeHub en EdgeAgent stoppen voordat u begint met de ontwikkeling van Visual Studio.

### <a name="check-your-tools-version"></a>De versie van uw hulpprogram ma's controleren

1. Selecteer in het menu **extensies** de optie **uitbrei dingen beheren**. Vouw **geïnstalleerde >-Hulpprogram ma's** uit en u vindt **Azure IOT Edge-Hulpprogram Ma's voor Visual Studio** en **Cloud Explorer voor Visual Studio**.

1. Noteer de geïnstalleerde versie. U kunt deze versie vergelijken met de nieuwste versie van Visual Studio Marketplace ([Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS2019), [Azure IOT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools))

1. Als uw versie ouder is dan beschikbaar is op Visual Studio Marketplace, werkt u uw hulp middelen bij in Visual Studio, zoals wordt weer gegeven in de volgende sectie.

### <a name="update-your-tools"></a>Uw hulpprogram ma's bijwerken

1. Vouw in het venster **extensies beheren** de optie **updates > Visual Studio Marketplace** uit, selecteer **Azure IOT Edge tools** of **Cloud Explorer voor Visual Studio** en selecteer **Update**.

1. Nadat de update van de hulpprogram ma's is gedownload, sluit u Visual Studio voor het activeren van de hulpprogram ma's bijwerken met behulp van het VSIX-installatie programma.

1. Selecteer in het installatie programma **OK** om te starten en **Wijzig** vervolgens de hulpprogram ma's.

1. Nadat de update is voltooid, selecteert u **sluiten** en Start Visual Studio opnieuw.

### <a name="create-an-azure-iot-edge-project"></a>Een Azure IoT Edge-project maken

Met de sjabloon Azure IoT Edge project in Visual Studio maakt u een project dat kan worden geïmplementeerd op Azure IoT Edge apparaten in azure IoT Hub. Eerst maakt u een Azure IoT Edge oplossing en vervolgens genereert u de eerste module in die oplossing. Elke IoT Edge-oplossing kan meer dan één module bevatten.

> [!TIP]
> De IoT Edge project structuur die door Visual Studio is gemaakt, is niet hetzelfde als in Visual Studio code.

1. Zoek in het dialoog venster Nieuw project in Visual Studio **Azure IOT Edge** project op en klik op **volgende**. Voer in het venster project configuratie een naam in voor het project en geef de locatie op en selecteer vervolgens **maken**. De standaard naam van het project is **AzureIoTEdgeApp1**.

   ![Nieuw project maken](./media/how-to-visual-studio-develop-csharp-module/create-new.png)

1. Selecteer in het venster **IOT Edge toepassing en module toevoegen** de optie **C#-module** of **C-module** en geef vervolgens de module naam en module-afbeeldings opslagplaats op. Visual Studio vult de module naam automatisch in met **localhost: 5000/<uw module naam \>**. Vervang deze door uw eigen register gegevens. Als u een lokaal docker-REGI ster gebruikt voor het testen, is **localhost** prima. Als u Azure Container Registry gebruikt, gebruikt u de aanmeldings server uit de instellingen van het REGI ster. De aanmeldings server ziet eruit als **_\<registry name\>_ . azurecr.io**. Vervang alleen het gedeelte **localhost: 5000** van de teken reeks, zodat het uiteindelijke resultaat eruitziet als **\<*registry name*\> . azurecr.io/ _\<your module name\>_**. De standaard module naam is **IotEdgeModule1**

   ![Toepassing en module toevoegen](./media/how-to-visual-studio-develop-csharp-module/add-application-and-module.png)

1. Selecteer **OK** om de Azure IOT EDGE-oplossing te maken met een module die gebruikmaakt van C# of C.

U hebt nu het project **AzureIoTEdgeApp1. Linux. amd64** of het project **AzureIoTEdgeApp1. Windows. amd64** en ook een **IotEdgeModule1** -project in uw oplossing. Elk **AzureIoTEdgeApp1** -project bevat een `deployment.template.json` bestand waarin de modules worden gedefinieerd die u wilt bouwen en implementeren voor uw IOT EDGE-oplossing. ook worden de routes tussen modules gedefinieerd. De standaard oplossing heeft een **SimulatedTemperatureSensor** -module en een **IotEdgeModule1** -module. De **SimulatedTemperatureSensor** -module genereert gesimuleerde gegevens in de **IotEdgeModule1** -module, terwijl de standaard code in de module **IotEdgeModule1** berichten direct pipet ontvangen op Azure IOT hub.

Als u wilt zien hoe de gesimuleerde temperatuur sensor werkt, bekijkt u de [bron code SimulatedTemperatureSensor. csproj](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor).

Het **IotEdgeModule1** -project is een .net Core 2,1-console toepassing als dit een C#-module is. Het bevat vereiste docker-bestanden die u nodig hebt voor uw IoT Edge-apparaat dat wordt uitgevoerd met een Windows-container of een Linux-container. `module.json`In het bestand worden de meta gegevens van een module beschreven. De daad werkelijke module code, waarmee de Azure IoT Device SDK als afhankelijkheid wordt gebruikt, is te vinden in het `Program.cs` `main.c` bestand of.

## <a name="develop-your-module"></a>Uw module ontwikkelen

De standaard module code die wordt geleverd bij de oplossing bevindt zich op **IotEdgeModule1**  >  **Program.cs** (voor C#) of **Main. c** (c). De module en het `deployment.template.json` bestand zijn zo ingesteld dat u de oplossing kunt bouwen, deze naar het container register pusht en deze implementeert op een apparaat om te testen zonder dat u code hoeft te gebruiken. De module is gebouwd om invoer van een bron (in dit geval de **SimulatedTemperatureSensor** -module die gegevens simuleert) te maken en deze naar Azure IOT hub te door sluizen.

Wanneer u klaar bent om de module sjabloon aan te passen met uw eigen code, gebruikt u de [Azure IOT hub sdk's](../iot-hub/iot-hub-devguide-sdks.md) om modules te bouwen die voldoen aan de belangrijkste behoeften van IOT-oplossingen, zoals beveiliging, Apparaatbeheer en betrouw baarheid.

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>Iotedgehubdev initialiseren met IoT Edge apparaat connection string

1. Kopieer de connection string van een IoT Edge apparaat vanuit de **primaire verbindings reeks** in de Visual Studio-Cloud Verkenner. Zorg ervoor dat u de connection string van een niet-edge-apparaat niet kopieert, omdat het pictogram van een IoT Edge apparaat verschilt van het pictogram van een niet-edge-apparaat.

   ![Verbindings reeks voor apparaat voor rand kopiëren](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. Selecteer in het menu **extra** het **Azure IOT Edge tools**  >  **Setup IOT Edge Simulator**, plak de Connection String en klik op **OK**.

   ![Venster met set-rand verbindings reeks openen](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

1. Voer de connection string uit in de eerste stap en selecteer **OK**.

> [!NOTE]
> U hoeft deze stappen slechts eenmaal op uw ontwikkel computer uit te voeren, aangezien de resultaten automatisch worden toegepast op alle volgende Azure IoT Edge oplossingen. Deze procedure kan opnieuw worden gevolgd als u naar een andere connection string moet overschakelen.

## <a name="build-and-debug-single-module"></a>Eén module samen stellen en debuggen

Normaal gesp roken wilt u elke module testen en fouten opsporen voordat u deze uitvoert binnen een volledige oplossing met meerdere modules.

1. Klik in **Solution Explorer** met de rechter muisknop op **IotEdgeModule1** en selecteer **instellen als opstart project** in het context menu.

   ![Opstart project instellen](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. Druk op **F5** of klik op de onderstaande knop om de module uit te voeren. &ndash;de eerste keer dat u dit doet, kan het 10 20 seconden duren voordat u dit doet.

   ![Module uitvoeren](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. Als de module is geïnitialiseerd, ziet u een .NET Core-Console-app.

   ![Module wordt uitgevoerd](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

1. Stel bij het ontwikkelen in C# een onderbrekings punt in voor de `PipeMessage()` functie in **Program.cs**; als u C gebruikt, stelt u een onderbrekings punt in voor de `InputQueue1Callback()` functie in **Main. C**. U kunt dit testen door een bericht te verzenden door de volgende opdracht uit te voeren in een **Git-Bash** of **WSL bash** -shell. (U kunt de opdracht niet uitvoeren `curl` vanuit een Power shell of opdracht prompt.)

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![Fout opsporing voor één module](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    Het onderbrekings punt moet worden geactiveerd. U kunt variabelen bekijken in het Visual Studio **lokalen** -venster.

   > [!TIP]
   > U kunt ook [postman](https://www.getpostman.com/) of andere API-hulpprogram ma's gebruiken om berichten te verzenden in plaats van `curl` .

1. Druk op **CTRL + F5** of klik op de knop stoppen om de fout opsporing te stoppen.

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>Bouw en debug IoT Edge-oplossing met meerdere modules

Wanneer u klaar bent met het ontwikkelen van een enkele module, wilt u mogelijk een volledige oplossing met meerdere modules uitvoeren en fouten opsporen.

1. Voeg in **Solution Explorer** een tweede module aan de oplossing toe door met de rechter muisknop op **AzureIoTEdgeApp1** te klikken en **Add**  >  **nieuwe IOT Edge module** toevoegen te selecteren. De standaard naam van de tweede module is **IotEdgeModule2** en zal fungeren als een andere pipe-module.

1. Open het bestand `deployment.template.json` en u ziet dat **IotEdgeModule2** is toegevoegd in de sectie **modules** . Vervang de sectie **routes** door het volgende. Als u de module namen hebt aangepast, moet u deze namen bijwerken zodat deze overeenkomen.

    ```json
        "routes": {
          "IotEdgeModule1ToIoTHub": "FROM /messages/modules/IotEdgeModule1/outputs/* INTO $upstream",
          "sensorToIotEdgeModule1": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule1/inputs/input1\")",
          "IotEdgeModule2ToIoTHub": "FROM /messages/modules/IotEdgeModule2/outputs/* INTO $upstream",
          "sensorToIotEdgeModule2": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule2/inputs/input1\")"
        },
    ```

1. Klik met de rechter muisknop op **AzureIoTEdgeApp1** en selecteer **instellen als opstart project** in het context menu.

1. Maak uw onderbrekings punten en druk vervolgens op **F5** om meerdere modules tegelijk uit te voeren en fouten op te sporen. U ziet nu meerdere .NET Core Console-app-Vensters, waarbij elk venster een andere module vertegenwoordigt.

   ![Fouten opsporen in meerdere modules](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. Druk op **CTRL + F5** of selecteer de knop stoppen om de fout opsporing te stoppen.

## <a name="build-and-push-images"></a>Installatie kopieën bouwen en pushen

1. Zorg ervoor dat **AzureIoTEdgeApp1** het start project is. Selecteer **fout opsporing** of **versie** als de configuratie die u wilt bouwen voor uw module installatie kopieën.

    > [!NOTE]
    > Bij het kiezen van **debug**, Visual Studio wordt gebruikt `Dockerfile.(amd64|windows-amd64).debug` voor het bouwen van docker-installatie kopieën. Dit omvat het .NET core-opdracht regel programma voor fout opsporing VSDBG in de container installatie kopie tijdens het maken van het bestand. Voor productie klare IoT Edge modules wordt u aangeraden de **release** configuratie te gebruiken, die `Dockerfile.(amd64|windows-amd64)` zonder VSDBG wordt gebruikt.

1. Als u een persoonlijk REGI ster gebruikt, zoals Azure Container Registry (ACR), gebruikt u de volgende docker-opdracht om u aan te melden.  U kunt de gebruikers naam en het wacht woord ophalen van de pagina **toegangs sleutels** van het REGI ster in de Azure Portal. Als u lokaal REGI ster gebruikt, kunt u [een lokaal REGI ster uitvoeren](https://docs.docker.com/registry/deploying/#run-a-local-registry).

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

1. Als u een persoonlijk REGI ster gebruikt, zoals Azure Container Registry, moet u de aanmeldings gegevens van uw REGI ster toevoegen aan de runtime-instellingen die in het bestand zijn gevonden `deployment.template.json` . Vervang de tijdelijke aanduidingen door de werkelijke ACR-gebruikers naam, het wacht woord en de register naam.

    ```json
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "registry1": {
                "username": "<username>",
                "password": "<password>",
                "address": "<registry name>.azurecr.io"
              }
            }
          }
    ```

1. Klik in **Solution Explorer** met de rechter muisknop op **AzureIoTEdgeApp1** en selecteer **Build-en push IOT Edge modules** om de docker-installatie kopie voor elke module te bouwen en te pushen.

## <a name="deploy-the-solution"></a>De oplossing implementeren

In dit snelstartartikel voor het instellen van uw IoT Edge-apparaat hebt u een module geïmplementeerd met behulp van de Azure-portal. U kunt modules ook implementeren met behulp van de Cloud Explorer voor Visual Studio. U hebt al een implementatie manifest voor bereid voor uw scenario, het `deployment.json` bestand en alles wat u hoeft te doen, selecteert u een apparaat om de implementatie te ontvangen.

1. Open **Cloud Explorer** door te klikken op **View**  >  **Cloud Explorer** weer geven. Zorg ervoor dat u bent aangemeld bij Visual Studio 2019.

1. Vouw in **Cloud Explorer** uw abonnement uit, Zoek uw Azure-IOT hub en het Azure IOT edge-apparaat dat u wilt implementeren.

1. Klik met de rechter muisknop op het IoT Edge apparaat om een implementatie voor te maken. Ga naar het implementatie manifest dat is geconfigureerd voor uw platform in de map **config** in uw Visual Studio-oplossing, zoals `deployment.arm32v7.json` .

1. Klik op de knop Vernieuwen om de nieuwe modules samen met de **SimulatedTemperatureSensor** -module en **$edgeAgent** en **$edgeHub** weer te geven.

## <a name="view-generated-data"></a>Gegenereerde gegevens weergeven

1. Als u het D2C-bericht voor een specifiek IoT-Edge apparaat wilt bewaken, selecteert u het in uw IoT-hub in **Cloud Explorer** en klikt u vervolgens op **bewaking van ingebouwd gebeurtenis-eind punt starten** in het **actie** venster.

1. Als u de bewaking van gegevens wilt stoppen, selecteert u het **ingebouwde gebeurtenis eindpunt controle stoppen** in het venster **actie** .

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het ontwikkelen van aangepaste modules voor uw IoT Edge-apparaten [en het gebruik van Azure IOT hub sdk's](../iot-hub/iot-hub-devguide-sdks.md).