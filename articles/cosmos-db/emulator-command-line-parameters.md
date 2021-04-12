---
title: Naslag informatie over de opdracht regel en Power shell voor Azure Cosmos DB-emulator
description: Meer informatie over de opdracht regel parameters voor Azure Cosmos DB emulator, het beheren van de emulator met Power shell en het wijzigen van het aantal containers dat u kunt maken binnen de emulator.
ms.service: cosmos-db
ms.topic: how-to
author: markjbrown
ms.author: mjbrown
ms.date: 09/17/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: e0c795484bf860402d05c1dc5779633962a44ec2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99979501"
---
# <a name="command-line-and-powershell-reference-for-azure-cosmos-db-emulator"></a>Naslag informatie over de opdracht regel en Power shell voor Azure Cosmos DB-emulator
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

De Azure Cosmos DB-emulator biedt een lokale omgeving die de Azure Cosmos DB-Service voor lokale ontwikkelings doeleinden emuleert. Nadat [de emulator is geïnstalleerd](local-emulator.md), kunt u de emulator beheren met opdracht regel-en Power shell-opdrachten. In dit artikel wordt beschreven hoe u de opdracht regel-en Power shell-opdrachten gebruikt om de emulator te starten en te stoppen, opties te configureren en andere bewerkingen uit te voeren. U moet de opdrachten uitvoeren vanaf de installatie locatie.

##  <a name="manage-the-emulator-with-command-line-syntax"></a><a id="command-line"></a>De emulator met opdracht regel syntaxis beheren

```cmd
Microsoft.Azure.Cosmos.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/EnableMongoDbEndpoint] [/?]
```

Typ `Microsoft.Azure.Cosmos.Emulator.exe /?` bij de opdrachtprompt om een lijst met opties te zien.

|**Optie** | **Beschrijving** | **Opdracht**| **Argumenten**|
|---|---|---|---|
|[Geen argumenten] | Start de Azure Cosmos DB Emulator met standaardinstellingen. |Microsoft.Azure.Cosmos.Emulator.exe| |
|[Help] |Toont de lijst met ondersteunde opdrachtregelargumenten.|Microsoft.Azure.Cosmos.Emulator.exe /? | |
| GetStatus |Downloadt de status van de Azure Cosmos DB Emulator. De status wordt aangegeven door de afsluitcode: 1 = starten, 2 = wordt uitgevoerd, 3 = gestopt. Een negatieve afsluitcode geeft aan dat er een fout is opgetreden. Er wordt geen andere uitvoer geproduceerd. | Microsoft.Azure.Cosmos.Emulator.exe /GetStatus| |
| Afsluiten| Sluit de Azure Cosmos DB Emulator af.| Microsoft.Azure.Cosmos.Emulator.exe /Shutdown | |
|DataPath | Specificeert het pad waarin de gegevensbestanden worden opgeslagen. De standaardwaarde is %LocalAppdata%\CosmosDBEmulator. | Microsoft.Azure.Cosmos.Emulator.exe/DataPath =\<datapath\> | \<datapath\>: Een toegankelijk pad |
|Poort | Specificeert het poortnummer dat moet worden gebruikt voor de emulator. De standaardwaarde is 8081. |Microsoft.Azure.Cosmos.Emulator.exe/Port =\<port\> | \<port\>: Enkel poort nummer |
| ComputePort | Specificeert het poortnummer dat moet worden gebruikt voor de Compute Interop Gateway-service. De testpoort voor het HTTP-eindpunt de gateway wordt berekend als ComputePort + 79. Daarom moeten ComputePort en ComputePort + 79 open en beschikbaar zijn. De standaardwaarde is 8900. | Microsoft.Azure.Cosmos.Emulator.exe/ComputePort =\<computeport\> | \<computeport\>: Enkel poort nummer |
| EnableMongoDbEndpoint=3.2 | Schakelt MongoDB API 3.2 in | Microsoft.Azure.Cosmos.Emulator.exe /EnableMongoDbEndpoint=3.2 | |
| EnableMongoDbEndpoint=3.6 | Schakelt MongoDB API 3.6 in | Microsoft.Azure.Cosmos.Emulator.exe /EnableMongoDbEndpoint=3.6 | |
| MongoPort | Specificeert het poortnummer dat moet worden gebruikt MongoDB compatibiliteit-API. De standaardwaarde is 10255. |Microsoft.Azure.Cosmos.Emulator.exe/MongoPort =\<mongoport\>|\<mongoport\>: Enkel poort nummer|
| EnableCassandraEndpoint | Schakelt Cassandra-API in | Microsoft.Azure.Cosmos.Emulator.exe /EnableCassandraEndpoint | |
| CassandraPort | Specificeert het poortnummer dat moet worden gebruikt voor het Cassandra-eindpunt. De standaardwaarde is 10350. | Microsoft.Azure.Cosmos.Emulator.exe/CassandraPort =\<cassandraport\> | \<cassandraport\>: Enkel poort nummer |
| EnableGremlinEndpoint | Schakelt Gremlin-API in | Microsoft.Azure.Cosmos.Emulator.exe /EnableGremlinEndpoint | |
| GremlinPort | Het poortnummer dat moet worden gebruikt voor het Gremlin-eindpunt. De standaardwaarde is 8901. | Microsoft.Azure.Cosmos.Emulator.exe/GremlinPort =\<port\> | \<port\>: Enkel poort nummer |
|EnableTableEndpoint | Schakelt Azure Table-API in | Microsoft.Azure.Cosmos.Emulator.exe /EnableTableEndpoint | |
|TablePort | Het poortnummer dat moet worden gebruikt voor het Azure Table-eindpunt. De standaardwaarde is 8902. | Microsoft.Azure.Cosmos.Emulator.exe/TablePort =\<port\> | \<port\>: Enkel poort nummer|
| KeyFile | Leest de autorisatiesleutel uit het opgegeven bestand. Gebruik de optie /GenKeyFile om een keyfile te genereren | Microsoft.Azure.Cosmos.Emulator.exe/KeyFile =\<file_name\> | \<file_name\>: Pad naar het bestand |
| ResetDataPath | Verwijdert recursief alle bestanden in het opgegeven pad. Als u geen pad opgeeft, wordt standaard %LOCALAPPDATA%\CosmosDbEmulator gebruikt. | Microsoft.Azure.Cosmos.Emulator.exe/ResetDataPath =\<path> | \<path\>: Bestandspad  |
| StartTraces  |  Start het verzamelen van logboeken voor foutopsporing met behulp van LOGMAN. | Microsoft.Azure.Cosmos.Emulator.exe /StartTraces | |
| StopTraces     | Stopt het verzamelen van logboeken voor foutopsporing met behulp van LOGMAN. | Microsoft.Azure.Cosmos.Emulator.exe /StopTraces  | |
| StartWprTraces  |  Start het verzamelen van logboeken voor foutopsporing met behulp van het hulpprogramma Windows Performance Recorder. | Microsoft.Azure.Cosmos.Emulator.exe /StartWprTraces | |
| StopWprTraces     | Stopt het verzamelen van logboeken voor foutopsporing met behulp van het hulpprogramma Windows Performance Recorder. | Microsoft.Azure.Cosmos.Emulator.exe /StopWprTraces  | |
|FailOnSslCertificateNameMismatch | Standaard genereert de emulator het zelfondertekend TLS/SSL-certificaat opnieuw als het SAN van het certificaat de domein naam van de emulator-host, het lokale IPv4-adres, ' localhost ' en ' 127.0.0.1 ' niet bevat. Met deze optie kan de emulator dan niet worden gestart. Vervolgens moet u de optie /GenCert gebruiken om een nieuw zelfondertekend TLS/SSL-certificaat te maken en te installeren. | Microsoft.Azure.Cosmos.Emulator.exe /FailOnSslCertificateNameMismatch  | |
| GenCert | Genereert en installeert een nieuw zelfondertekend TLS/SSL-certificaat. u kunt eventueel een door komma's gescheiden lijst met extra DNS-namen gebruiken om toegang te krijgen tot de emulator via het netwerk. | Microsoft.Azure.Cosmos.Emulator.exe/GenCert =\<dns-names\> |\<dns-names\>: Optionele, door komma's gescheiden lijst met extra DNS-namen  |
| DirectPorts |Specificeert de poorten die worden gebruikt voor rechtstreekse connectiviteit. Standaardwaarden zijn 10251,10252,10253,10254. | Microsoft.Azure.Cosmos.Emulator.exe/DirectPorts:\<directports\> | \<directports\>: Door komma's gescheiden lijst met 4 poorten |
| Sleutel |De autorisatiesleutel voor de emulator. De sleutel moet de base 64-codering zijn van een 64-byte-vector. | Microsoft.Azure.Cosmos.Emulator.exe/Key:\<key\> | \<key\>: Sleutel moet de base-64-code ring van een 64-byte-vector zijn|
| EnableRateLimiting | Geeft aan dat het beperkingsgedrag van de aanvraagsnelheid is ingeschakeld. |Microsoft.Azure.Cosmos.Emulator.exe /EnableRateLimiting | |
| DisableRateLimiting |Geeft aan dat het beperkingsgedrag van de aanvraagsnelheid is uitgeschakeld. |Microsoft.Azure.Cosmos.Emulator.exe /DisableRateLimiting | |
| NoUI | De gebruikersinterface van de emulator niet weergeven. | Microsoft.Azure.Cosmos.Emulator.exe /NoUI | |
| NoExplorer | Geen Data Explorer weergeven bij het opstarten. |Microsoft.Azure.Cosmos.Emulator.exe /NoExplorer | | 
| PartitionCount | Specificeert het maximum aantal gepartitioneerde containers. Zie [Het aantal containers wijzigen](#set-partitioncount) voor meer informatie. | Microsoft.Azure.Cosmos.Emulator.exe/PartitionCount =\<partitioncount\> | \<partitioncount\>: Maximum aantal toegestane containers met één partitie. De standaardwaarde is 25. Maximaal toegestaan is 250.|
| DefaultPartitionCount| Specificeert het standaardaantal partities voor een gepartitioneerde container. | Microsoft.Azure.Cosmos.Emulator.exe/DefaultPartitionCount =\<defaultpartitioncount\> | \<defaultpartitioncount\> De standaard waarde is 25.|
| AllowNetworkAccess | Geeft toegang tot de emulator via een netwerk. U moet ook/Key = \<key_string\> of/keyfile = door geven \<file_name\> om netwerk toegang in te scha kelen. | Microsoft.Azure.Cosmos.Emulator.exe/AllowNetworkAccess/Key = \<key_string\> of Microsoft.Azure.Cosmos.Emulator.exe/AllowNetworkAccess/keyfile =\<file_name\>| |
| NoFirewall | Firewallregels niet aanpassen wanneer de optie /AllowNetworkAccess wordt gebruikt. |Microsoft.Azure.Cosmos.Emulator.exe /NoFirewall | |
| GenKeyFile | Een nieuwe autorisatiesleutel genereren en opslaan in het opgegeven bestand. De gegenereerde sleutel kan worden gebruikt met de opties/Key of/KeyFile. | Microsoft.Azure.Cosmos.Emulator.exe/GenKeyFile =\<path to key file\> | |
| Consistentie | Het standaard consistentieniveau voor het account instellen. | Microsoft.Azure.Cosmos.Emulator.exe/Consistency =\<consistency\> | \<consistency\>: De waarde moet een van de volgende [consistentie niveaus](consistency-levels.md)zijn: sessie, sterk, uiteindelijk of BoundedStaleness. De standaardwaarde is Sessie. |
| ? | Het helpbericht weergeven.| | |

## <a name="manage-the-emulator-with-powershell"></a>De emulator beheren met Power shell

De emulator wordt geleverd met een PowerShell-module voor het starten, stoppen, en verwijderen van de emulator, en voor het ophalen van de status van de service. Voer de volgende cmdlet uit om de PowerShell-module te gebruiken:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

of plaats de map `PSModules` in uw `PSModulePath` en importeer deze zoals wordt weergegeven in de volgende opdracht:

```powershell
$env:PSModulePath += "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules"
Import-Module Microsoft.Azure.CosmosDB.Emulator
```

Hier volgt een samenvatting van de opdrachten voor het beheren van de emulator vanuit PowerShell:

### `Get-CosmosDbEmulatorStatus`

**Syntaxis**

`Get-CosmosDbEmulatorStatus`

**Opmerkingen**

Retourneert een van deze ServiceControllerStatus-waarden: ServiceControllerStatus.StartPending, ServiceControllerStatus.Running of ServiceControllerStatus.Stopped.

### `Start-CosmosDbEmulator`

**Syntaxis**

`Start-CosmosDbEmulator [-DataPath <string>] [-DefaultPartitionCount <uint16>] [-DirectPort <uint16[]>] [-MongoPort <uint16>] [-NoUI] [-NoWait] [-PartitionCount <uint16>] [-Port <uint16>] [<CommonParameters>]`

**Opmerkingen**

Start de emulator. Standaard wacht de opdracht totdat de emulator klaar is om aanvragen te accepteren. Gebruik de optie -NoWait als u wilt dat de cmdlet wordt geretourneerd zodra deze de emulator heeft gestart.

### `Stop-CosmosDbEmulator`

**Syntaxis**

 `Stop-CosmosDbEmulator [-NoWait]`

**Opmerkingen**

Stopt de emulator. Standaard wacht deze opdracht totdat de emulator volledig is afgesloten. Gebruik de optie -NoWait als u wilt dat de cmdlet wordt geretourneerd zodra het afsluiten van de emulator wordt gestart.

### `Uninstall-CosmosDbEmulator`

**Syntaxis**

`Uninstall-CosmosDbEmulator [-RemoveData]`

**Opmerkingen**

Verwijdert de emulator en optioneel ook de inhoud van $env:LOCALAPPDATA\CosmosDbEmulator.
De cmdlet zorgt ervoor dat de emulator is gestopt deze wordt verwijderd.

## <a name="change-the-number-of-default-containers"></a><a id="set-partitioncount"></a>Het aantal standaard containers wijzigen

Standaard kunt u Maxi maal 25 containers met een vaste grootte maken (alleen ondersteund met behulp van Azure Cosmos DB Sdk's) of vijf onbeperkte containers met behulp van de Azure Cosmos DB emulator. Door de waarde voor **PartitionCount** te wijzigen, kunt u maximaal 250 containers met een vaste grootte maken of vijftig containers van onbeperkte grootte, of een combinatie van de twee die niet groter is dan 250 containers met een vaste grootte (waarbij één container van onbeperkte grootte = vijf containers met een vaste grootte). Het wordt echter afgeraden om de emulator uit te voeren met meer dan 200 containers met een vaste grootte. Vanwege de overhead die hierdoor wordt toegevoegd aan de I/O-bewerkingen van de schijf, ontstaan er dan onvoorspelbare time-outs bij het gebruik van de eindpunt-API's.

Als u probeert een container te maken nadat het huidige aantal partities is overschreden, genereert de emulator een ServiceUnavailable-uitzondering, met het volgende bericht.

> We ondervinden momenteel hoge vraag in deze regio en kunnen op dit moment niet aan uw aanvraag voldoen. We work continuously to bring more and more capacity online, and encourage you to try again. (Er is momenteel sprake van grote vraag in deze regio en we kunnen uw aanvraag op dit moment niet verwerken. We doen ons best om meer en meer capaciteit online te brengen en adviseren om het nogmaals te proberen.)
> ActivityId: 12345678-1234-1234-1234-123456789abc

Voer de volgende stappen uit om het aantal containers te wijzigen dat beschikbaar is in de Azure Cosmos DB emulator:

1. Verwijder alle lokale Azure Cosmos DB Emulator-gegevens door met de rechtermuisknop te klikken op het pictogram van de **Azure Cosmos DB Emulator** pictogram in het systeemvak en vervolgens te klikken op **Gegevens opnieuw instellen...**.

1. Verwijder alle emulator-gegevens in de map `%LOCALAPPDATA%\CosmosDBEmulator`.

1. Sluit alle geopende exemplaren door met de rechtermuisknop te klikken op het pictogram van de **Azure Cosmos DB Emulator** in het systeemvak en vervolgens te klikken op **Afsluiten**. Het afsluiten van alle exemplaren kan een paar minuten duren.

1. Installeer de nieuwste versie van de [Azure Cosmos DB Emulator](https://aka.ms/cosmosdb-emulator).

1. Start de emulator met de vlag PartitionCount door een waarde < = 250 in te stellen. Bijvoorbeeld: `C:\Program Files\Azure Cosmos DB Emulator> Microsoft.Azure.Cosmos.Emulator.exe /PartitionCount=100`.
 
## <a name="next-steps"></a>Volgende stappen

* [De Azure Cosmos DB-emulator certificaten exporteren voor gebruik met Java-, python-en Node.js-apps](local-emulator-export-ssl-certificates.md)
* [Problemen met de emulator oplossen](troubleshoot-local-emulator.md)
