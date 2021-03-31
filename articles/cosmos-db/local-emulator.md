---
title: Lokaal installeren en ontwikkelen met Azure Cosmos DB-emulator
description: Meer informatie over het installeren en gebruiken van de Azure Cosmos DB-emulator in Windows-, Linux-, macOS-en Windows docker-omgevingen. Met behulp van de emulator kunt u uw toepassing lokaal gratis ontwikkelen en testen zonder dat u een Azure-abonnement hoeft te maken.
ms.service: cosmos-db
ms.topic: how-to
author: markjbrown
ms.author: mjbrown
ms.date: 09/22/2020
ms.custom: devx-track-csharp, contperf-fy21q1
ms.openlocfilehash: 89e82e210dbf7c404ebd5978d87bf391d8565ee2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97033949"
---
# <a name="install-and-use-the-azure-cosmos-db-emulator-for-local-development-and-testing"></a>De Azure Cosmos DB-emulator installeren en gebruiken voor lokale ontwikkeling en tests
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

De Azure Cosmos DB Emulator is een lokale omgeving waarin de Azure Cosmos DB-service wordt geëmuleerd voor ontwikkelingsdoeleinden. Met behulp van de Azure Cosmos DB Emulator kunt u uw toepassing lokaal ontwikkelen en testen, kosteloos en zonder een Azure-abonnement te maken. Wanneer u tevreden bent over hoe uw toepassing werkt in de Azure Cosmos DB-emulator, kunt u overschakelen naar het gebruik van een Azure Cosmos-account in de Cloud. In dit artikel wordt beschreven hoe u de emulator installeert en gebruikt in Windows-, Linux-, macOS-en Windows docker-omgevingen.

## <a name="download-the-emulator"></a>De emulator downloaden

Down load en installeer de nieuwste versie van Azure Cosmos DB emulator op uw lokale computer om aan de slag te gaan. In het artikel over de release van de [emulator](local-emulator-release-notes.md) wordt een lijst weer gegeven met alle beschik bare versies en de functie-updates die in elke release zijn aangebracht.

:::image type="icon" source="media/local-emulator/download-icon.png" border="false":::**[De Azure Cosmos DB-emulator downloaden](https://aka.ms/cosmosdb-emulator)**

U kunt toepassingen ontwikkelen met behulp van Azure Cosmos DB emulator met de [SQL](local-emulator.md#sql-api)-, [Cassandra](local-emulator.md#cassandra-api)-, [MongoDb](local-emulator.md#azure-cosmos-dbs-api-for-mongodb)-, [Gremlin](local-emulator.md#gremlin-api)-en [Table](local-emulator.md#table-api) -API-accounts. De Data Explorer in de emulator biedt momenteel alleen ondersteuning voor het weer geven van SQL-gegevens. de gegevens die zijn gemaakt met behulp van MongoDB, Gremlin/Graph en Cassandra-client toepassingen, worden op dit moment niet weer gegeven. Zie [verbinding maken met het Emulator-eind punt](#connect-with-emulator-apis) vanaf verschillende api's voor meer informatie.

## <a name="how-does-the-emulator-work"></a>Hoe werkt de emulator?

De Azure Cosmos DB Emulator biedt een hoogwaardige emulatie van de Azure Cosmos DB-service. Het ondersteunt gelijkwaardige functionaliteit als de Azure Cosmos DB, waaronder het maken van gegevens, het opvragen van gegevens, het inrichten en schalen van containers en het uitvoeren van opgeslagen procedures en triggers. U kunt toepassingen ontwikkelen en testen met behulp van de Azure Cosmos DB emulator en deze implementeren in azure op wereld wijde schaal door het Azure Cosmos DB verbindings eindpunt bij te werken.

Emulatie van de Azure Cosmos DB-service is zeer betrouwbaar, maar de implementatie van de emulator is anders dan die van de service. De emulator gebruikt bijvoorbeeld standaardbesturingssysteemonderdelen, zoals het lokale bestandssysteem voor persistentie en de HTTPS-protocolstack voor connectiviteit. Functionaliteit die afhankelijk is van de Azure-infra structuur, zoals globale replicatie, latentie in milliseconden voor lees-en schrijf bewerkingen en instel bare consistentie niveaus zijn niet van toepassing wanneer u de emulator gebruikt.

U kunt gegevens migreren tussen de Azure Cosmos DB emulator en de Azure Cosmos DB-service met behulp van het [hulp programma voor gegevens migratie Azure Cosmos DB](https://github.com/azure/azure-documentdb-datamigrationtool).

## <a name="differences-between-the-emulator-and-the-cloud-service"></a>Verschillen tussen de emulator en de Cloud service

Omdat de Azure Cosmos DB-emulator een geëmuleerde omgeving biedt die wordt uitgevoerd op het lokale werk station van de ontwikkel aars, zijn er enkele verschillen in de functionaliteit van de emulator en een Azure Cosmos-account in de Cloud:

* Momenteel wordt in het deel venster **Data Explorer** van de emulator volledig alleen SQL API-clients ondersteund. De **Data Explorer** weer gave en bewerkingen voor Azure Cosmos DB-api's, zoals MongoDb-, Table-, Graph-en Cassandra-api's, worden niet volledig ondersteund.

* De emulator ondersteunt slechts één vast account en een bekende primaire sleutel. U kunt de sleutel niet opnieuw genereren wanneer u de Azure Cosmos DB-emulator gebruikt, maar de standaard sleutel kan echter worden gewijzigd met behulp van de [opdracht regel](emulator-command-line-parameters.md) optie.

* Met de emulator kunt u alleen een Azure Cosmos-account maken in de [ingerichte doorvoer](set-throughput.md) modus. Er wordt momenteel geen [serverloze](serverless.md) modus ondersteund.

* De emulator is geen schaal bare service en biedt geen ondersteuning voor een groot aantal containers. Wanneer u de Azure Cosmos DB-emulator gebruikt, kunt u standaard Maxi maal 25 containers met een vaste grootte maken op 400 RU/s (alleen ondersteund met behulp van Azure Cosmos DB Sdk's) of 5 onbeperkte containers. Zie [het artikel PartitionCount value instellen](emulator-command-line-parameters.md#set-partitioncount) voor meer informatie over het wijzigen van deze waarde.

* De emulator biedt geen verschillende [Azure Cosmos DB consistentie niveaus](consistency-levels.md) , zoals de Cloud service.

* De emulator biedt geen [replicatie voor meerdere regio's](distribute-data-globally.md).

* Omdat de kopie van uw Azure Cosmos DB-emulator mogelijk niet altijd up-to-date is met de meest recente wijzigingen in de Azure Cosmos DB-Service, moet u altijd verwijzen naar de [Azure Cosmos DB capacity planner](estimate-ru-with-capacity-planner.md) om nauw keurige schatting te maken van de door Voer (RUs)-vereisten van uw toepassing.

* De emulator ondersteunt een maximale ID-eigenschap van 254 tekens.

## <a name="install-the-emulator"></a>De emulator installeren

Controleer voordat u de emulator installeert of u over de volgende hardware-en software vereisten beschikt:

* Software vereisten:
  * Momenteel wordt het besturings systeem Windows Server 2016, 2019 of Windows 10 host ondersteund. Het besturings systeem voor de host met Active Directory ingeschakeld wordt momenteel niet ondersteund.
  * 64-bits besturingssysteem

* Minimale hardwarevereisten:
  * 2 GB RAM
  * 10 GB beschikbare schijfruimte

* Als u de Azure Cosmos DB Emulator wilt installeren, configureren en uitvoeren, moet u beheerdersbevoegdheden hebben op de computer. De emulator voegt een certificaat toe en stelt ook de firewall regels in om de services uit te voeren. Daarom zijn beheerders rechten nodig voor de emulator om dergelijke bewerkingen uit te kunnen voeren.

Down load en installeer de nieuwste versie van [Azure Cosmos DB emulator](https://aka.ms/cosmosdb-emulator) op uw lokale computer om aan de slag te gaan. Als u problemen ondervindt bij het installeren van de emulator, raadpleegt u het artikel [over het oplossen van problemen](troubleshoot-local-emulator.md) met de emulator voor fout opsporing.

Afhankelijk van uw systeem vereisten kunt u de emulator uitvoeren op [Windows](#run-on-windows), [docker voor Windows](#run-on-windows-docker), [Linux of macOS,](#run-on-linux-macos) zoals beschreven in de volgende secties van dit artikel.

## <a name="check-for-emulator-updates"></a>Controleren op emulator-updates

Elke versie van de emulator wordt geleverd met een set functie-updates of oplossingen voor fouten. Lees het artikel over de opmerkingen met de [emulator-versie](local-emulator-release-notes.md) voor meer informatie over de beschik bare versies.

Als u na de installatie de standaard instellingen hebt gebruikt, worden de gegevens die overeenkomen met de emulator opgeslagen op de locatie%LOCALAPPDATA%\CosmosDBEmulator. U kunt een andere locatie configureren met behulp van de optionele instellingen voor het gegevenspad; Dit is de `/DataPath=PREFERRED_LOCATION` [opdracht regel parameter](emulator-command-line-parameters.md). De gegevens die in één versie van de Azure Cosmos DB-emulator zijn gemaakt, zijn niet gegarandeerd toegankelijk als u een andere versie gebruikt. Als u uw gegevens voor de lange termijn wilt behouden, is het raadzaam dat u die gegevens opslaat in een Azure Cosmos-account in plaats van de Azure Cosmos DB-emulator.

## <a name="use-the-emulator-on-windows"></a><a id="run-on-windows"></a>De emulator gebruiken in Windows

De Azure Cosmos DB-emulator is standaard geïnstalleerd op `C:\Program Files\Azure Cosmos DB Emulator` locatie. Als u de Azure Cosmos DB-emulator in Windows wilt starten, selecteert u de knop **Start** of drukt u op de Windows-toets. Begin met het typen van **Azure Cosmos DB Emulator** en selecteer de emulator in de lijst met toepassingen.

:::image type="content" source="./media/local-emulator/database-local-emulator-start.png" alt-text="Selecteer de knop Start of druk op de Windows-toets, begin met het typen van Azure Cosmos DB emulator en selecteer de emulator in de lijst met toepassingen":::

Wanneer de emulator is gestart, ziet u een pictogram in het systeemvak van de Windows-taak balk. De Azure Cosmos Data Explorer wordt automatisch geopend in uw browser op deze URL- `https://localhost:8081/_explorer/index.html` URL.

:::image type="content" source="./media/local-emulator/database-local-emulator-taskbar.png" alt-text="Melding in systeemvak van lokale Azure Cosmos Emulator":::

U kunt ook de emulator starten en stoppen vanuit de opdracht regel-of Power shell-opdrachten. Zie het naslag artikel over het [opdracht regel programma](emulator-command-line-parameters.md) voor meer informatie.

De Azure Cosmos DB Emulator wordt standaard uitgevoerd op de lokale computer ('localhost'), en luistert op poort 8081. Het adres wordt weergegeven als `https://localhost:8081/_explorer/index.html`. Als u de explorer sluit en later opnieuw wilt openen, kunt u de URL in uw browser openen of deze starten vanuit de Azure Cosmos DB Emulator in het Windows-systeemvak, zoals hieronder weergegeven.

:::image type="content" source="./media/local-emulator/database-local-emulator-data-explorer-launcher.png" alt-text="Startprogramma voor Data Explorer van lokale Azure Cosmos Emulator":::

## <a name="use-the-emulator-on-docker-for-windows"></a><a id="run-on-windows-docker"></a>De emulator gebruiken in docker voor Windows

U kunt de Azure Cosmos DB-emulator uitvoeren op de Windows docker-container. Zie de [docker-hub](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/) voor de docker pull-opdracht en [github](https://github.com/Azure/azure-cosmos-db-emulator-docker) voor `Dockerfile` meer informatie. De emulator werkt momenteel niet op docker voor Oracle Linux. Gebruik de volgende instructies om de emulator uit te voeren op docker voor Windows:

1. Nadat u [docker voor Windows](https://www.docker.com/docker-windows) hebt geïnstalleerd, gaat u naar Windows-containers door met de rechter muisknop op het docker-pictogram op de werk balk te klikken en **Switch naar Windows-containers** te selecteren.

1. Haal vervolgens de installatiekopie van de emulator op van Docker Hub met de volgende opdracht in uw favoriete shell.

   ```bash
   docker pull mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
   ```

1. Als u de installatie kopie wilt starten, voert u de volgende opdrachten uit, afhankelijk van de opdracht regel of de Power shell-omgeving:

   # <a name="command-line"></a>[Opdrachtregel](#tab/cli)

   ```bash

   md %LOCALAPPDATA%\CosmosDBEmulator\bind-mount

   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%LOCALAPPDATA%\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
   ```
   Windows-docker-installatie kopieën zijn mogelijk niet algemeen compatibel met elk besturings systeem van Windows host. De standaard installatie kopie van Azure Cosmos DB-emulator is bijvoorbeeld alleen compatibel met Windows 10 en Windows Server 2016. Als u een installatie kopie nodig hebt die compatibel is met Windows Server 2019, voert u de volgende opdracht uit:

   ```bash
   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%hostDirectory%,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/winsrv2019/azure-cosmos-emulator:latest
   ```

   # <a name="powershell"></a>[PowerShell](#tab/powershell)

   ```powershell

   md $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount 2>null

   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=$env:LOCALAPPDATA\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator

   ```

   De reactie ziet er ongeveer als volgt uit:

   ```bash
   Starting emulator
   Emulator Endpoint: https://172.20.229.193:8081/
   Primary Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
   Exporting SSL Certificate
   You can import the SSL certificate from an administrator command prompt on the host by running:
   cd /d %LOCALAPPDATA%\CosmosDBEmulatorCert
   powershell .\importcert.ps1
   --------------------------------------------------------------------------------------------------
   Starting interactive shell
   ```
   ---

   > [!NOTE]
   > Wanneer `docker run` u de opdracht uitvoert en er een fout in de poort conflict wordt weer gegeven (dat wil zeggen als de opgegeven poort al in gebruik is), geeft u een aangepaste poort door de poort nummers te wijzigen. U kunt bijvoorbeeld de para meter "-p 8081:8081" wijzigen in "-p 443:8081"

1. Gebruik nu het Emulator-eind punt en de primaire sleutel van het antwoord en importeer het TLS/SSL-certificaat op uw host. Als u het TLS/SSL-certificaat wilt importeren, voert u de volgende stappen uit vanaf een opdracht prompt van de beheerder:

   # <a name="command-line"></a>[Opdrachtregel](#tab/cli)

   ```bash
   cd  %LOCALAPPDATA%\CosmosDBEmulator\bind-mount
   powershell .\importcert.ps1
   ```

   # <a name="powershell"></a>[PowerShell](#tab/powershell)

   ```powershell
   cd $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount
   .\importcert.ps1
   ```
   ---

1. Als u de interactieve shell sluit nadat de emulator is gestart, wordt de container van de emulator afgesloten. Als u de Data Explorer opnieuw wilt openen, gaat u naar de volgende URL in uw browser. Het eindpunt van de emulator wordt vermeld in het reactiebericht dat hierboven wordt getoond.

   `https://<emulator endpoint provided in response>/_explorer/index.html`

Als u een .NET-client toepassing uitvoert op een Linux docker-container en als u Azure Cosmos DB-emulator uitvoert op een hostcomputer, gebruikt u de instructies in de volgende sectie om het certificaat te importeren in de Linux docker-container.

### <a name="regenerate-the-emulator-certificates-when-running-on-a-docker-container"></a>De emulator-certificaten opnieuw genereren wanneer deze worden uitgevoerd op een docker-container

Wanneer de emulator wordt uitgevoerd in een docker-container, worden de certificaten die zijn gekoppeld aan de emulator telkens opnieuw gegenereerd wanneer u de desbetreffende container stopt en opnieuw opstart. U moet de certificaten na het starten van elke container opnieuw importeren. Als u deze beperking wilt omzeilen, kunt u een docker-bestand gebruiken om de docker-container te binden aan een bepaald IP-adres en een container installatie kopie.

U kunt bijvoorbeeld de volgende configuratie in het docker-bestand gebruiken. Zorg ervoor dat u deze formatteert volgens uw vereiste: 

```yml
version: '2.4' # Do not upgrade to 3.x yet, unless you plan to use swarm/docker stack: https://github.com/docker/compose/issues/4513

networks:
  default:
    external: false
    ipam:
      driver: default
      config:
        - subnet: "172.16.238.0/24"

services:

  # First create a directory that will hold the emulator traces and certificate to be imported
  # set hostDirectory=C:\emulator\bind-mount
  # mkdir %hostDirectory%

  cosmosdb:
    container_name: "azurecosmosemulator"
    hostname: "azurecosmosemulator"
    image: 'mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator'
    platform: windows
    tty: true
    mem_limit: 3GB
    ports:
        - '8081:8081'
        - '8900:8900'
        - '8901:8901'
        - '8902:8902'
        - '10250:10250'
        - '10251:10251'
        - '10252:10252'
        - '10253:10253'
        - '10254:10254'
        - '10255:10255'
        - '10256:10256'
        - '10350:10350'
    networks:
      default:
        ipv4_address: 172.16.238.246
    volumes:
        - '${hostDirectory}:C:\CosmosDB.Emulator\bind-mount'
```

## <a name="use-the-emulator-on-linux-or-macos"></a><a id="run-on-linux-macos"></a>De emulator gebruiken in Linux of macOS

De Azure Cosmos DB-emulator kan momenteel alleen worden uitgevoerd in Windows. Als u Linux of macOS gebruikt, kunt u de emulator uitvoeren in een virtuele Windows-machine die wordt gehost in een Hyper Visor zoals Parallels of VirtualBox.

> [!NOTE]
> Telkens wanneer u de virtuele Windows-machine opnieuw opstart die wordt gehost in een Hyper Visor, moet u het certificaat opnieuw importeren omdat het IP-adres van de virtuele machine verandert. Het importeren van het certificaat is niet vereist voor het geval u de virtuele machine hebt geconfigureerd om het IP-adres te behouden.

Gebruik de volgende stappen om de emulator te gebruiken in Linux-of macOS-omgevingen:

1. Voer de volgende opdracht uit vanaf de virtuele Windows-machine en noteer het IPv4-adres:

   ```bash
   ipconfig.exe
   ```

1. Wijzig de eind punt-URL in uw toepassing om het IPv4-adres te gebruiken dat wordt geretourneerd door `ipconfig.exe` in plaats van `localhost` .

1. Start vanaf de Windows-VM de Azure Cosmos DB-emulator vanaf de opdracht regel met behulp van de volgende opties. Voor meer informatie over de para meters die worden ondersteund door de opdracht regel, raadpleegt u de [emulator opdracht regel hulpprogramma Naslag informatie](emulator-command-line-parameters.md):

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
   ```

1. Ten slotte moet u het certificaat vertrouwens proces oplossen tussen de toepassing die wordt uitgevoerd op de Linux-of Mac-omgeving en de emulator. U kunt een van de volgende twee opties gebruiken om het certificaat op te lossen:

   1. [Importeer het EMULATOR TLS/SSL-certificaat in de Linux-of Mac-omgeving](#import-certificate) of
   2. [De TLS/SSL-validatie in de toepassing uitschakelen](#disable-ssl-validation)

### <a name="option-1-import-the-emulator-tlsssl-certificate"></a><a id="import-certificate"></a>Optie 1: de emulator TLS/SSL-certificaat importeren

In de volgende secties ziet u hoe u het Emulator TLS/SSL-certificaat in Linux-en macOS-omgevingen kunt importeren.

#### <a name="linux-environment"></a>Linux-omgeving

Als u werkt met Linux, vertrouwt .NET op OpenSSL voor de validatie:

1. [Exporteer het certificaat in PFX-indeling](local-emulator-export-ssl-certificates.md#export-emulator-certificate). De optie PFX is beschikbaar wanneer u de persoonlijke sleutel exporteert.

1. Kopieer het PFX-bestand naar uw Linux-omgeving.

1. Converteer het PFX-bestand naar een CRT-bestand.

   ```bash
   openssl pkcs12 -in YourPFX.pfx -clcerts -nokeys -out YourCTR.crt
   ```

1. Kopieer het CRT-bestand naar de map met aangepaste certificaten in uw Linux-distributie. In het geval van Debian-distributies is dat meestal `/usr/local/share/ca-certificates/`.

   ```bash
   cp YourCTR.crt /usr/local/share/ca-certificates/
   ```

1. Werk de TLS/SSL-certificaten bij, waarmee de `/etc/ssl/certs/` map wordt bijgewerkt.

   ```bash
   update-ca-certificates
   ```

#### <a name="macos-environment"></a>macOS-omgeving

Voer de volgende stappen uit als u werkt met een Mac:

1. [Exporteer het certificaat in PFX-indeling](local-emulator-export-ssl-certificates.md#export-emulator-certificate). De optie PFX is beschikbaar wanneer u de persoonlijke sleutel exporteert.

1. Kopieer het PFX-bestand naar uw Mac-omgeving.

1. Open de toepassing *Sleutelhangertoegang* en importeer het PFX-bestand.

1. Open de lijst met certificaten en zoek het certificaat met de naam `localhost`.

1. Open het contextmenu voor dat specifieke item, selecteer *Toon info* en selecteer bij *Vertrouw* > *Gebruik bij dit certificaat* de optie *Vertrouw altijd*. 

   :::image type="content" source="./media/local-emulator/mac-trust-certificate.png" alt-text="Open het contextmenu voor dat specifieke item, selecteer Toon info en selecteer bij Vertrouw &gt; Gebruik bij dit certificaat de optie Vertrouw altijd":::
  
### <a name="option-2-disable-the-ssl-validation-in-the-application"></a><a id="disable-ssl-validation"></a>Optie 2: de SSL-validatie in de toepassing uitschakelen

Het uitschakelen van SSL-validatie wordt alleen aanbevolen voor ontwikkelings doeleinden en moet niet worden uitgevoerd in een productie omgeving. De volgende voor beelden laten zien hoe u SSL-validatie voor .NET-en Node.js-toepassingen kunt uitschakelen.

# <a name="net-standard-21"></a>[.NET Standard 2.1 +](#tab/ssl-netstd21)

Voor elke toepassing die wordt uitgevoerd in een framework dat compatibel is met .NET Standard 2,1 of hoger, kunnen we gebruikmaken van het `CosmosClientOptions.HttpClientFactory` volgende:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/HttpClientFactory/Program.cs?name=DisableSSLNETStandard21)]

# <a name="net-standard-20"></a>[.NET Standard 2,0](#tab/ssl-netstd20)

Voor elke toepassing die wordt uitgevoerd in een framework dat compatibel is met .NET Standard 2,0, kunnen we gebruikmaken van het `CosmosClientOptions.HttpClientFactory` volgende:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/HttpClientFactory/Program.cs?name=DisableSSLNETStandard20)]

# <a name="nodejs"></a>[Node.js](#tab/ssl-nodejs)

Voor Node.js toepassingen kunt u het bestand wijzigen `package.json` om het in te stellen `NODE_TLS_REJECT_UNAUTHORIZED` tijdens het starten van de toepassing:

```json
"start": NODE_TLS_REJECT_UNAUTHORIZED=0 node app.js
```
--- 

## <a name="enable-access-to-emulator-on-a-local-network"></a>Toegang tot de emulator inschakelen op een lokaal netwerk

Als u meerdere computers hebt met één netwerk en u de emulator op één computer instelt en u deze wilt openen vanaf een andere computer. In dat geval moet u de toegang tot de emulator inschakelen op een lokaal netwerk.

U kunt de emulator uitvoeren op een lokaal netwerk. U maakt netwerktoegang mogelijk door de optie `/AllowNetworkAccess` op te geven op de [opdrachtregel](emulator-command-line-parameters.md). U moet hiervoor ook `/Key=key_string` of `/KeyFile=file_name` opgeven. U kunt `/GenKeyFile=file_name` gebruiken om vooraf een bestand met een willekeurige sleutel te genereren. Vervolgens kunt u die doorgeven aan `/KeyFile=file_name` of `/Key=contents_of_file`.

Om netwerk toegang voor de eerste keer in te scha kelen, moet de gebruiker de emulator afsluiten en de gegevensdirectory *%LocalAppData%\CosmosDBEmulator* van de emulator verwijderen.

## <a name="authenticate-connections-when-using-emulator"></a><a id="authenticate-requests"></a>Verbindingen verifiëren bij het gebruik van een emulator

Net als bij Azure Cosmos DB in de cloud, moet elke aanvraag die u op basis van de Azure Cosmos DB Emulator maakt, worden geverifieerd. De Azure Cosmos DB-emulator ondersteunt alleen beveiligde communicatie via TLS. De Azure Cosmos DB-emulator ondersteunt één vast account en een bekende verificatie sleutel voor primaire-sleutel verificatie. Dit account en deze sleutel zijn de enige referenties die zijn toegestaan voor gebruik met de Azure Cosmos DB Emulator. Dit zijn:

```bash
Account name: localhost:<port>
Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

> [!NOTE]
> De primaire sleutel die wordt ondersteund door de Azure Cosmos DB-emulator is uitsluitend bedoeld voor gebruik met de emulator. U kunt niet uw Azure DB Cosmos-account en -sleutel voor productie gebruiken met de Azure Cosmos DB Emulator.

> [!NOTE]
> Als u de emulator hebt gestart met de optie/Key, gebruikt u de gegenereerde sleutel in plaats van de standaard sleutel `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` . Zie de [informatie over het opdrachtregelprogramma](emulator-command-line-parameters.md) voor meer informatie.

## <a name="connect-to-different-apis-with-the-emulator"></a><a id="connect-with-emulator-apis"></a>Verbinding maken met verschillende Api's met de emulator

### <a name="sql-api"></a>SQL-API

Wanneer de Azure Cosmos DB-emulator op uw computer wordt uitgevoerd, kunt u elke ondersteunde [Azure Cosmos DB SDK](sql-api-sdk-dotnet-standard.md) of de [Azure Cosmos DB REST API](/rest/api/cosmos-db/) gebruiken om met de emulator te communiceren. De Azure Cosmos DB-emulator bevat ook een ingebouwde Data Explorer waarmee u containers kunt maken voor SQL API of Azure Cosmos DB voor Mongo DB API. Met behulp van Data Explorer kunt u items weer geven en bewerken zonder dat u code hoeft te schrijven.

```csharp
// Connect to the Azure Cosmos DB Emulator running locally
CosmosClient client = new CosmosClient(
   "https://localhost:8081", 
    "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

```

### <a name="azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB-API voor MongoDB

Zodra u de Azure Cosmos DB-emulator hebt die op uw bureau blad wordt uitgevoerd, kunt u de API van de [Azure Cosmos DB voor MongoDb](mongodb-introduction.md) gebruiken om met de emulator te communiceren. Start de emulator vanaf de [opdracht prompt](emulator-command-line-parameters.md) als beheerder met '/EnableMongoDbEndpoint '. Gebruik vervolgens de volgende verbindingsreeks om verbinding te maken met het MongoDB API-account:

```bash
mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true
```

### <a name="table-api"></a>Tabel-API

Zodra u de Azure Cosmos DB-emulator op uw bureau blad hebt uitgevoerd, kunt u de [Azure Cosmos DB Table-API SDK](./tutorial-develop-table-dotnet.md) gebruiken om met de emulator te communiceren. Start de emulator vanaf de [opdracht prompt](emulator-command-line-parameters.md) als beheerder met '/EnableTableEndpoint '. Voer vervolgens de volgende code uit om verbinding te maken met het Table-API-account:

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Table;
using CloudTable = Microsoft.WindowsAzure.Storage.Table.CloudTable;
using CloudTableClient = Microsoft.WindowsAzure.Storage.Table.CloudTableClient;

string connectionString = "DefaultEndpointsProtocol=http;AccountName=localhost;AccountKey=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==;TableEndpoint=http://localhost:8902/;";

CloudStorageAccount account = CloudStorageAccount.Parse(connectionString);
CloudTableClient tableClient = account.CreateCloudTableClient();
CloudTable table = tableClient.GetTableReference("testtable");
table.CreateIfNotExists();
table.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
```

### <a name="cassandra-api"></a>Cassandra-API

Start Emulator vanaf een beheerders [opdracht prompt](emulator-command-line-parameters.md) met '/EnableCassandraEndpoint '. U kunt ook de omgevingsvariabele `AZURE_COSMOS_EMULATOR_CASSANDRA_ENDPOINT=true`instellen.

1. [Installeer Python 2.7](https://www.python.org/downloads/release/python-2716/)

1. [Installeer Cassandra CLI/CQLSH](https://cassandra.apache.org/download/)

1. Voer de volgende opdrachten uit in een opdrachtpromptvenster met normale rechten:

   ```bash
   set Path=c:\Python27;%Path%
   cd /d C:\sdk\apache-cassandra-3.11.3\bin
   set SSL_VERSION=TLSv1_2
   set SSL_VALIDATE=false
   cqlsh localhost 10350 -u localhost -p C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw== --ssl
   ```

1. Voer in de CQLSH-shell de volgende opdrachten uit om verbinding te maken met het Cassandra-eindpunt:

   ```bash
   CREATE KEYSPACE MyKeySpace WITH replication = {'class':'MyClass', 'replication_factor': 1};
   DESCRIBE keyspaces;
   USE mykeyspace;
   CREATE table table1(my_id int PRIMARY KEY, my_name text, my_desc text);
   INSERT into table1 (my_id, my_name, my_desc) values( 1, 'name1', 'description 1');
   SELECT * from table1;
   EXIT
   ```

### <a name="gremlin-api"></a>Gremlin-API

Start Emulator vanaf een beheerders [opdracht prompt](emulator-command-line-parameters.md)met '/EnableGremlinEndpoint '. U kunt ook de omgevingsvariabele `AZURE_COSMOS_EMULATOR_GREMLIN_ENDPOINT=true`instellen.

1. [Installeer apache-tinkerpop-gremlin-console-3.3.4](https://archive.apache.org/dist/tinkerpop/3.3.4).

1. Maak in Data Explorer van de emulator een data base ' db1 ' en een verzameling ' coll1 '. Kies voor de partitie sleutel "/name"

1. Voer de volgende opdrachten uit in een opdrachtpromptvenster met normale rechten:

   ```bash
   cd /d C:\sdk\apache-tinkerpop-gremlin-console-3.3.4-bin\apache-tinkerpop-gremlin-console-3.3.4
  
   copy /y conf\remote.yaml conf\remote-localcompute.yaml
   notepad.exe conf\remote-localcompute.yaml
     hosts: [localhost]
     port: 8901
     username: /dbs/db1/colls/coll1
     password: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
     connectionPool: {
     enableSsl: false}
     serializer: { className: org.apache.tinkerpop.gremlin.driver.ser.GraphSONMessageSerializerV1d0,
     config: { serializeResultToString: true  }}

   bin\gremlin.bat
   ```

1. Voer in de Gremlin-shell de volgende opdrachten uit om verbinding te maken met het Gremlin-eind punt:

   ```bash
   :remote connect tinkerpop.server conf/remote-localcompute.yaml
   :remote console
   :> g.V()
   :> g.addV('person1').property(id, '1').property('name', 'somename1')
   :> g.addV('person2').property(id, '2').property('name', 'somename2')
   :> g.V()
   ```

## <a name="uninstall-the-local-emulator"></a><a id="uninstall"></a>De lokale emulator verwijderen

Gebruik de volgende stappen om de emulator te verwijderen:

1. Sluit alle geopende exemplaren van de lokale emulator af door met de rechter muisknop op het pictogram van de **Azure Cosmos DB emulator** op het systeemvak te klikken en vervolgens **Afsluiten** te selecteren. Het afsluiten van alle exemplaren kan een paar minuten duren.

1. Typ in het zoekvak van Windows **apps & functies** en selecteer **apps & onderdelen (systeem instellingen)** .

1. Ga in de lijst met apps naar de **Azure Cosmos DB emulator**, Selecteer deze, klik op **verwijderen** en vervolgens op bevestigen en selecteer vervolgens **verwijderen** .

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u de lokale emulator gebruikt voor gratis lokale ontwikkeling. U kunt nu door gaan met de volgende artikelen:

* [De Azure Cosmos DB-emulator certificaten exporteren voor gebruik met Java-, python-en Node.js-apps](local-emulator-export-ssl-certificates.md)
* [Opdracht regel parameters en Power shell-opdrachten gebruiken om de emulator te beheren](emulator-command-line-parameters.md)
* [Problemen met de emulator oplossen](troubleshoot-local-emulator.md)
