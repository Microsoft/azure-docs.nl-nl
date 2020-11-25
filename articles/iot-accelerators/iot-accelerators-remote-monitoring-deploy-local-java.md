---
title: Externe bewakings oplossing implementeren lokaal-IntelliJ IDE-Azure | Microsoft Docs
description: In deze hand leiding wordt uitgelegd hoe u de oplossing voor externe controle kunt implementeren op uw lokale computer met behulp van IntelliJ voor testen en ontwikkeling.
author: dominicbetts
ms.custom: devx-track-java
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/24/2019
ms.topic: conceptual
ms.openlocfilehash: 78573cfe00d8e2e7ddcbf705dffdd5530f82c4e0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96024194"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---intellij"></a>De externe controle oplossings versneller implementeren lokaal-IntelliJ

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

In dit artikel wordt beschreven hoe u de oplossing voor externe controle op uw lokale computer implementeert voor testen en ontwikkeling. U leert hoe u de micro Services in IntelliJ uitvoert. Een lokale implementatie van micro Services maakt gebruik van de volgende Cloud Services: IoT Hub, Azure Cosmos DB, Azure streaming Analytics en Azure Time Series Insights.

Als u de oplossings versneller voor externe controle in docker op uw lokale computer wilt uitvoeren, raadpleegt u [de externe controle oplossings versneller lokaal-docker implementeren](iot-accelerators-remote-monitoring-deploy-local-docker.md).

## <a name="prerequisites"></a>Vereisten

Als u de Azure-Services wilt implementeren die worden gebruikt door de oplossings versneller voor externe bewaking, hebt u een actief Azure-abonnement nodig.

Als u geen account hebt, kunt u binnen een paar minuten een account voor de gratis proefversie maken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie.

### <a name="machine-setup"></a>Machine instellen

Als u de lokale implementatie wilt volt ooien, moet u de volgende hulpprogram ma's installeren op uw lokale ontwikkel computer:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [IntelliJ Community-editie](https://www.jetbrains.com/idea/download/)
* [IntelliJ scala-invoeg toepassing](https://plugins.jetbrains.com/plugin/1347-scala)
* [IntelliJ SBT-invoeg toepassing](https://plugins.jetbrains.com/plugin/5007-sbt)
* [IntelliJ SBT uitvoerder-invoeg toepassing](https://plugins.jetbrains.com/plugin/7247-sbt-executor)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js V8](https://nodejs.org/)

Node.js V8 is een vereiste voor de PC'S CLI die de scripts gebruiken om Azure-resources te maken. Gebruik Node.js V10 toevoegen niet.

> [!NOTE]
> IntelliJ IDE is beschikbaar voor Windows en Mac.

## <a name="download-the-source-code"></a>De bron code downloaden

De opslag plaatsen voor de bron code voor externe controle zijn de bron code en de docker-configuratie bestanden die u nodig hebt om de micro Services docker-installatie kopieën uit te voeren.

Als u een lokale versie van de opslag plaats wilt klonen en maken, gebruikt u de opdracht regel omgeving om naar een geschikte map op uw lokale computer te gaan. Voer vervolgens een van de volgende sets opdrachten uit om de Java-opslag plaats te klonen:

* Voer de volgende opdracht uit om de nieuwste versie van de Java micro service-implementaties te downloaden:

  ```cmd/sh
  git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-java.git
  ```

* Voer de volgende opdrachten uit om de meest recente submodules op te halen:

   ```cmd/sh
   cd azure-iot-pcs-remote-monitoring-java
   git submodule foreach git pull origin master
    ```

> [!NOTE]
> Met deze opdrachten wordt de bron code voor alle micro Services gedownload naast de scripts die u gebruikt om de micro services lokaal uit te voeren. U hebt de bron code niet nodig om de micro services uit te voeren in docker. Maar de bron code is handig als u later van plan bent om de oplossings versneller te wijzigen en uw wijzigingen lokaal te testen.

## <a name="deploy-the-azure-services"></a>De Azure-Services implementeren

Hoewel in dit artikel wordt uitgelegd hoe u de micro services lokaal kunt uitvoeren, zijn ze afhankelijk van Azure-Services die in de cloud worden uitgevoerd. Gebruik het volgende script om de Azure-Services te implementeren. In de script voorbeelden wordt ervan uitgegaan dat u de Java-opslag plaats op een Windows-computer gebruikt. Als u in een andere omgeving werkt, past u de paden, bestands extensies en pad scheidings tekens op de juiste wijze aan.

### <a name="create-new-azure-resources"></a>Nieuwe Azure-resources maken

Als u de vereiste Azure-resources nog niet hebt gemaakt, voert u de volgende stappen uit:

1. In uw opdracht regel omgeving gaat u naar de map **\services\scripts\local\launch** in de gekloonde kopie van de opslag plaats.

1. Voer de volgende opdrachten uit om het hulp programma **pc's** CLI te installeren en u aan te melden bij uw Azure-account:

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Voer het script **Start. cmd** uit. Het script vraagt u om de volgende informatie:

   * De naam van een oplossing.
   * Het te gebruiken Azure-abonnement.
   * De locatie van het Azure-Data Center dat moet worden gebruikt.

   Met het script maakt u een resource groep in azure met de naam van de oplossing. Deze resource groep bevat de Azure-resources die door de oplossings versneller worden gebruikt. U kunt deze resource groep verwijderen nadat u de bijbehorende resources niet meer nodig hebt.

   Het script voegt ook een set omgevings variabelen toe aan uw lokale machine. De naam van elke variabele heeft de voor voegsel- **pc's**. Deze omgevings variabelen bieden Details waarmee externe controle de configuratie waarden van een Azure Key Vault bron kan lezen.

   > [!TIP]
   > Wanneer het script is voltooid, worden de omgevings variabelen opgeslagen in een bestand met de naam **\<your home folder\> \\ . PCs \\ \<solution name\> . env**. U kunt deze gebruiken voor toekomstige implementaties van oplossings versnelling. Houd er rekening mee dat alle omgevings variabelen die op uw lokale computer zijn ingesteld, de waarden in het **\\ \\ lokale \\ . env** -bestand van Services-scripts overschrijven wanneer u **docker-opstellen** uitvoert.

1. Sluit uw opdracht regel omgeving.

### <a name="use-existing-azure-resources"></a>Bestaande Azure-resources gebruiken

Als u de vereiste Azure-resources al hebt gemaakt, stelt u de bijbehorende omgevings variabelen in op uw lokale computer:
* **PCS_KEYVAULT_NAME**: de naam van de Key Vault resource.
* **PCS_AAD_APPID**: de Azure Active Directory-toepassings-id (Azure AD).
* **PCS_AAD_APPSECRET**: het Azure AD-toepassings geheim.

Configuratie waarden worden gelezen uit deze Key Vault bron. Deze omgevings variabelen kunnen worden opgeslagen in het bestand **\<your home folder\> \\ . pc's \\ \<solution name\> . env** van de implementatie. Houd er rekening mee dat omgevings variabelen die op uw lokale computer zijn ingesteld, waarden in het bestand **\\ \\ Local \\ . env van Services-scripts** worden vervangen wanneer u **docker-opstellen** uitvoert.

Een deel van de configuratie die nodig is voor de micro service, wordt opgeslagen in een exemplaar van Key Vault dat is gemaakt tijdens de eerste implementatie. De bijbehorende variabelen in de sleutel kluis moeten naar behoefte worden gewijzigd.

## <a name="run-the-microservices"></a>De micro Services uitvoeren

In deze sectie voert u de externe bewakings service uit. U voert de volgende handelingen uit:

* De webgebruikersinterface zelf.
* De Azure IoT Device simulatie-, auth-en Azure Stream Analytics Manager-services in docker.
* De micro Services in IntelliJ.

### <a name="run-the-device-simulation-service"></a>De Device simulatie service uitvoeren

Open een nieuw opdracht prompt venster. Controleer of u toegang hebt tot de omgevings variabelen die zijn ingesteld door het script **Start. cmd** in de vorige sectie.

Voer de volgende opdracht uit om de docker-container voor de Device simulatie service te openen. De service simuleert apparaten voor de oplossing voor externe controle.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="run-the-auth-service"></a>De auth-service uitvoeren

Open een nieuw opdracht prompt venster en voer de volgende opdracht uit om de docker-container voor de auth-service te openen. Met deze service kunt u de gebruikers beheren die zijn gemachtigd om toegang te krijgen tot Azure IoT-oplossingen.

```cmd
<path_to_cloned_repository>\services\auth\scripts\docker\run.cmd
```

### <a name="run-the-stream-analytics-manager-service"></a>De Stream Analytics Manager-service uitvoeren

Open een nieuw opdracht prompt venster en voer de volgende opdracht uit om de docker-container voor de Stream Analytics Manager-service te openen. Met deze service kunt u Stream Analytics taken beheren. Dit beheer omvat het instellen van de taak configuratie en het starten, stoppen en bewaken van de taak status.

```cmd
<path_to_cloned_repository>\services\asa-manager\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-your-local-machine"></a>Alle andere micro Services op uw lokale computer implementeren

De volgende stappen laten zien hoe u micro Services voor externe controle uitvoert in IntelliJ.

#### <a name="import-a-project"></a>Een project importeren

1. Open de IntelliJ IDE.
1. Selecteer **project importeren**.
1. Kies **Azure-IOT-PCs-Remote-Monitoring-java\services\build.SBT**.

#### <a name="create-run-configurations"></a>Uitvoerings configuraties maken

1. Selecteer **Run**  >  **bewerkings configuraties** uitvoeren.
1. Selecteer **nieuwe configuratie**  >  **SBT taak** toevoegen.
1. Voer een **naam** in en voer vervolgens **taken** in als **Run**.
1. Selecteer de **werkmap** op basis van de service die u wilt uitvoeren.
1. Selecteer **OK Toep assen**  >  **OK** om uw keuzes op te slaan.
1. Uitvoerings configuraties maken voor de volgende webservices:
    * WebService (services\config)
    * WebService (services\device-Telemetry)
    * WebService (services\iothub-Manager)
    * WebService (services\storage-adapter)

In de volgende afbeelding ziet u een voor beeld van het toevoegen van een configuratie voor een service:

[![Scherm opname van het venster IntelliJ IDE run/debug-configuratie, met de optie storageAdapter gemarkeerd in de taken lijst SBT in het linkerdeel venster en de vermeldingen in de vakken naam, taken, werkmap en VM-para meters in het rechterdeel venster.](./media/deploy-locally-intellij/run-configurations.png)](./media/deploy-locally-intellij/run-configurations.png#lightbox)

#### <a name="create-a-compound-configuration"></a>Een samengestelde configuratie maken

1. Als u alle services tegelijk wilt uitvoeren, selecteert u **nieuwe configuratie**  >  **verbinding** toevoegen.
1. Voer een **naam** in en selecteer vervolgens **SBT-taken toevoegen**.
1. Selecteer **OK Toep assen**  >  **OK** om uw keuzes op te slaan.

De volgende afbeelding laat bijvoorbeeld zien hoe u alle SBT-taken aan één configuratie toevoegt:

[![Scherm opname van het venster IntelliJ IDE run/debug-configuraties, met de optie AllServices gemarkeerd in de samengestelde lijst in het linkerdeel venster en de optie voor de SBT-taak ' deviceTelemetry ' gemarkeerd in het rechterdeel venster.](./media/deploy-locally-intellij/all-services.png)](./media/deploy-locally-intellij/all-services.png#lightbox)

Selecteer **uitvoeren** om de webservices op de lokale computer te bouwen en uit te voeren.

Elke webservice opent een opdracht prompt venster en webbrowser venster. Bij de opdracht prompt wordt de uitvoer van de actieve service weer gegeven. In het browser venster kunt u de status controleren. Sluit de opdracht prompt Vensters of webpagina's niet, omdat deze acties de webservice stoppen.

Als u toegang wilt krijgen tot de status van de services, gaat u naar de volgende Url's:

* IoT-Hub Manager: `http://localhost:9002/v1/status`
* Telemetrie van apparaat: `http://localhost:9004/v1/status`
* configuraties `http://localhost:9005/v1/status`
* opslag adapter: `http://localhost:9022/v1/status`

### <a name="start-the-stream-analytics-job"></a>De Stream Analytics-taak starten

Volg deze stappen om de Stream Analytics taak te starten:

1. Ga naar de [Azure Portal](https://portal.azure.com).
1. Ga naar de **resource groep** die voor uw oplossing is gemaakt. De naam van de resource groep is de naam die u voor uw oplossing hebt gekozen toen u het script **Start. cmd** hebt uitgevoerd.
1. Selecteer de **Stream Analytics taak** in de lijst met resources.
1. Selecteer op de pagina **overzicht** van stream Analytics-taak de knop **Start** en selecteer vervolgens **Start** om de taak te starten.

### <a name="run-the-web-ui"></a>De Web-UI uitvoeren

In deze stap start u de Web-UI. Open een nieuw opdracht prompt venster. Controleer of u toegang hebt tot de omgevings variabelen die zijn ingesteld door het script **Start. cmd** . Ga naar de map **webui** in uw lokale kopie van de opslag plaats en voer de volgende opdrachten uit:

```cmd
npm install
npm start
```

Wanneer de **Start** opdracht is voltooid, wordt de pagina op het adres weer gegeven in de browser `http://localhost:3000/dashboard` . De fouten op deze pagina worden verwacht. Voer de volgende stappen uit om de toepassing zonder fouten weer te geven.

### <a name="configure-and-run-nginx"></a>Nginx configureren en uitvoeren

Stel een omgekeerde proxy server in die de webtoepassing koppelt aan de micro services die worden uitgevoerd op uw lokale computer:

1. Kopieer het bestand **nginx. conf** vanuit de map **webui\scripts\localhost** in uw lokale kopie van de opslag plaats naar de map **nginx\conf** installatiemap.
1. Voer nginx uit.

Zie [nginx voor Windows](https://nginx.org/en/docs/windows.html)voor meer informatie over het uitvoeren van nginx.

### <a name="connect-to-the-dashboard"></a>Verbinding maken met het dash board

Ga naar in uw browser om toegang te krijgen tot het dash board van de oplossing voor externe controle `http://localhost:9000` .

## <a name="clean-up"></a>Opschonen

Verwijder de Cloud Services uit uw Azure-abonnement nadat u klaar bent met testen om onnodige kosten te voor komen. Als u de services wilt verwijderen, gaat u naar de [Azure Portal](https://ms.portal.azure.com)en verwijdert u de resource groep waarin het script **Start. cmd** is gemaakt.

U kunt ook de lokale kopie van de externe bewakings opslagplaats verwijderen die is gemaakt bij het klonen van de bron code van GitHub.

## <a name="next-steps"></a>Volgende stappen

Nu u de oplossing voor controle op afstand hebt geïmplementeerd, is de volgende stap het [verkennen van de mogelijkheden van het dash board van de oplossing](quickstart-remote-monitoring-deploy.md).
