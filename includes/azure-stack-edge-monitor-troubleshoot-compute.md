---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/30/2020
ms.author: alkohli
ms.openlocfilehash: 92ccb6127e624ace9e719ffd23324b3a1b971f72
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89272162"
---
Op een Azure Stack edge-apparaat waarop de compute-rol is geconfigureerd, kunt u problemen oplossen of het apparaat bewaken met behulp van twee verschillende sets opdrachten.

- `iotedge`Opdrachten gebruiken. Deze opdrachten zijn beschikbaar voor basis bewerkingen voor uw apparaat.
- `dkrdbe`Opdrachten gebruiken. Deze opdrachten zijn beschikbaar voor een uitgebreide set bewerkingen voor uw apparaat.

Als u een van de bovenstaande opdrachten wilt uitvoeren, moet u [verbinding maken met de Power shell-interface](#connect-to-the-powershell-interface).

### <a name="use-iotedge-commands"></a>`iotedge`Opdrachten gebruiken

[Maak verbinding met de Power shell-interface](#connect-to-the-powershell-interface) en gebruik de functie om een lijst met beschik bare opdrachten weer te geven `iotedge` .

```powershell
[10.100.10.10]: PS>iotedge -?                                                                                                                                                                                                 Usage: iotedge COMMAND

Commands:
   check
   list
   logs
   restart

[10.100.10.10]: PS>
```

De volgende tabel bevat een korte beschrijving van de opdrachten die beschikbaar zijn voor `iotedge` :

|command  |Beschrijving |
|---------|---------|
|`check`     | Geautomatiseerde controles uitvoeren op veelvoorkomende configuratie-en verbindings problemen       |
|`list`     | Modules in lijst weergeven         |
|`logs`     | De logboeken van een module ophalen        |
|`restart`     | Een module stoppen en opnieuw starten         |


### <a name="use-dkrdbe-commands"></a>`dkrdbe`Opdrachten gebruiken

[Maak verbinding met de Power shell-interface](#connect-to-the-powershell-interface) en gebruik de functie om een lijst met beschik bare opdrachten weer te geven `dkrdbe` .

```powershell
[10.100.10.10]: PS>dkrdbe -?
Usage: dkrdbe COMMAND

Commands:
   image [prune]
   images
   inspect
   login
   logout
   logs
   port
   ps
   pull
   start
   stats
   stop
   system [df]
   top

[10.100.10.10]: PS>
```
De volgende tabel bevat een korte beschrijving van de opdrachten die beschikbaar zijn voor `dkrdbe` :

|command  |Beschrijving |
|---------|---------|
|`image`     | Installatie kopieën beheren. Als u ongebruikte installatie kopieën wilt verwijderen, gebruikt u: `dkrdbe image prune -a -f`       |
|`images`     | Afbeeldingen weer geven         |
|`inspect`     | Gegevens op laag niveau op docker-objecten retour neren         |
|`login`     | Aanmelden bij een docker-REGI ster         |
|`logout`     | Afmelden bij een docker-REGI ster         |
|`logs`     | De logboeken van een container ophalen        |
|`port`     | Poort toewijzingen of een specifieke toewijzing voor de container weer geven        |
|`ps`     | Containers weergeven        |
|`pull`     | Een installatie kopie of een opslag plaats vanuit een REGI ster ophalen         |
|`start`     | Een of meer gestopt containers starten         |
|`stats`     | Een live stream van de container (s) weer geven statistieken over het resource gebruik         |
|`stop`     | Een of meer actieve containers stoppen        |
|`system`     | Docker beheren         |
|`top`     | De actieve processen van een container weer geven         |

Gebruik om hulp te krijgen voor elke beschik bare opdracht `dkrdbe <command-name> --help` .

Als u bijvoorbeeld inzicht wilt krijgen in het gebruik van de `port` opdracht, typt u:

```powershell
[10.100.10.10]: P> dkrdbe port --help

Usage:  dkr port CONTAINER [PRIVATE_PORT[/PROTO]]

List port mappings or a specific mapping for the container
[10.100.10.10]: P> dkrdbe login --help

Usage:  docker login [OPTIONS] [SERVER]

Log in to a Docker registry.
If no server is specified, the default is defined by the daemon.

Options:
  -p, --password string   Password
      --password-stdin    Take the password from stdin
  -u, --username string   Username
[10.100.10.10]: PS>
```

De beschik bare opdrachten voor de `dkrdbe` functie gebruiken dezelfde para meters als degene die worden gebruikt voor de normale docker-opdrachten. Voor de opties en para meters die worden gebruikt met de opdracht docker, gaat u naar [het hulp programma docker commandline gebruiken](https://docs.docker.com/engine/reference/commandline/docker/).

### <a name="to-check-if-the-module-deployed-successfully"></a>Controleren of de module is geïmplementeerd

Compute-modules zijn containers waarvoor een bedrijfs logica is geïmplementeerd. Als u wilt controleren of een compute-module is geïmplementeerd, voert u de `ps` opdracht uit en controleert u of de container (overeenkomend met de module Compute) wordt uitgevoerd.

Voer de opdracht uit om de lijst met alle containers (met inbegrip van de onderbroken items) op te halen `ps -a` .

```powershell
[10.100.10.10]: P> dkrdbe ps -a
CONTAINER ID        IMAGE                                                COMMAND                   CREATED             STATUS              PORTS                                                                  NAMES
d99e2f91d9a8        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  movefile
0a06f6d605e9        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  filemove
2f8a36e629db        mcr.microsoft.com/azureiotedge-hub:1.0               "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days           0.0.0.0:443->443/tcp, 0.0.0.0:5671->5671/tcp, 0.0.0.0:8883->8883/tcp   edgeHub
acce59f70d60        mcr.microsoft.com/azureiotedge-agent:1.0             "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days                                                                                  edgeAgent
[10.100.10.10]: PS>
```

Als er een fout is opgetreden bij het maken van de container installatie kopie of tijdens het ophalen van de installatie kopie, voert u uit `logs edgeAgent` .  `EdgeAgent` is de IoT Edge runtime-container die verantwoordelijk is voor het inrichten van andere containers.

Omdat `logs edgeAgent` alle logboeken worden gedumpt, is het gebruik van de optie een goede manier om de recente fouten te bekijken `--tail 20` .


```powershell
[10.100.10.10]: PS>dkrdbe logs edgeAgent --tail 20
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Util.Uds.HttpUdsMessageHandler] - Connected socket /var/run/iotedge/mgmt.sock
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Util.Uds.HttpUdsMessageHandler] - Sending request http://mgmt.sock/modules?api-version=2018-06-28
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Agent] - Getting edge agent config...
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Agent] - Obtained edge agent config
2019-02-28 23:38:23.469 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Edgelet.ModuleManagementHttpClient] - Received a valid Http response from unix:///var/run/iotedge/mgmt.soc
k for List modules
--------------------CUT---------------------
--------------------CUT---------------------
08:28.1007774+00:00","restartCount":0,"lastRestartTimeUtc":"2019-02-26T20:08:28.1007774+00:00","runtimeStatus":"running","version":"1.0","status":"running","restartPolicy":"always
","type":"docker","settings":{"image":"edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64","imageHash":"sha256:47778be0602fb077d7bc2aaae9b0760fbfc7c058bf4df192f207ad6cbb96f7cc","c
reateOptions":"{\"HostConfig\":{\"Binds\":[\"/home/hcsshares/share4-dl460:/home/input\",\"/home/hcsshares/share4-iot:/home/output\"]}}"},"env":{}}
2019-02-28 23:38:28.480 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Planners.HealthRestartPlanner] - HealthRestartPlanner created Plan, with 0 command(s).
```

### <a name="to-get-container-logs"></a>Container logboeken ophalen

Als u Logboeken voor een specifieke container wilt ophalen, moet u eerst de container weer geven en vervolgens de logboeken ophalen voor de container waarin u bent geïnteresseerd.

1. [Verbinding maken met de Power shell-interface](#connect-to-the-powershell-interface).
2. Voer de opdracht uit om de lijst met actieve containers op te halen `ps` .

    ```powershell
    [10.100.10.10]: P> dkrdbe ps
    CONTAINER ID        IMAGE                                                COMMAND                   CREATED             STATUS              PORTS                                                                  NAMES
    d99e2f91d9a8        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  movefile
    0a06f6d605e9        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  filemove
    2f8a36e629db        mcr.microsoft.com/azureiotedge-hub:1.0               "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days           0.0.0.0:443->443/tcp, 0.0.0.0:5671->5671/tcp, 0.0.0.0:8883->8883/tcp   edgeHub
    acce59f70d60        mcr.microsoft.com/azureiotedge-agent:1.0             "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days                                                                                  edgeAgent
    ```

3. Noteer de container-ID voor de container waarvoor u de logboeken nodig hebt.

4. Als u de logboeken voor een specifieke container wilt ophalen, voert u de opdracht uit die `logs` de container-ID levert.

    ```powershell
    [10.100.10.10]: PS>dkrdbe logs d99e2f91d9a8
    02/26/2019 18:21:45: Info: Opening module client connection.
    02/26/2019 18:21:46: Info: Initializing with input: /home/input, output: /home/output.
    02/26/2019 18:21:46: Info: IoT Hub module client initialized.
    02/26/2019 18:22:24: Info: Received message: 1, SequenceNumber: 0 CorrelationId: , MessageId: 081886a07e694c4c8f245a80b96a252a Body: [{"ChangeType":"Created","ShareRelativeFilePath":"\\__Microsoft Data Box Edge__\\Upload\\Errors.xml","ShareName":"share4-dl460"}]
    02/26/2019 18:22:24: Info: Moving input file: /home/input/__Microsoft Data Box Edge__/Upload/Errors.xml to /home/output/__Microsoft Data Box Edge__/Upload/Errors.xml
    02/26/2019 18:22:24: Info: Processed event.
    02/26/2019 18:23:38: Info: Received message: 2, SequenceNumber: 0 CorrelationId: , MessageId: 30714d005eb048e7a4e7e3c22048cf20 Body: [{"ChangeType":"Created","ShareRelativeFilePath":"\\f [10]","ShareName":"share4-dl460"}]
    02/26/2019 18:23:38: Info: Moving input file: /home/input/f [10] to /home/output/f [10]
    02/26/2019 18:23:38: Info: Processed event.
    ```

### <a name="to-monitor-the-usage-statistics-of-the-device"></a>De gebruiks statistieken van het apparaat controleren

Gebruik de opdracht om het geheugen, het CPU-gebruik en de i/o-bewerkingen op het apparaat te controleren `stats` .

1. [Verbinding maken met de Power shell-interface](#connect-to-the-powershell-interface).
2. Voer de `stats` opdracht uit om de Live Stream uit te scha kelen en alleen het eerste resultaat op te halen.

   ```powershell
   dkrdbe stats --no-stream
   ```

   In het volgende voor beeld ziet u het gebruik van deze cmdlet:

    ```
    [10.100.10.10]: P> dkrdbe stats --no-stream
    CONTAINER ID        NAME          CPU %         MEM USAGE / LIMIT     MEM %         NET I/O             BLOCK I/O           PIDS
    d99e2f91d9a8        movefile      0.0           24.4MiB / 62.89GiB    0.04%         751kB / 497kB       299kB / 0B          14
    0a06f6d605e9        filemove      0.00%         24.11MiB / 62.89GiB   0.04%         679kB / 481kB       49.5MB / 0B         14
    2f8a36e629db        edgeHub       0.18%         173.8MiB / 62.89GiB   0.27%         4.58MB / 5.49MB     25.7MB / 2.19MB     241
    acce59f70d60        edgeAgent     0.00%         35.55MiB / 62.89GiB   0.06%         2.23MB / 2.31MB     55.7MB / 332kB      14
    [10.100.10.10]: PS>
    ```

