---
title: Omgevingsvariabelen voor de runtime van taken
description: Richt lijnen voor de omgevings variabele van de taak runtime en naslag informatie voor Azure Batch Analytics.
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: a2cab5011eb04586dc361bf1cec9c1f162d70117
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95538526"
---
# <a name="azure-batch-runtime-environment-variables"></a>Omgevings variabelen Azure Batch-runtime

De [Azure batch-service](https://azure.microsoft.com/services/batch/) stelt de volgende omgevings variabelen in op reken knooppunten. U kunt naar deze omgevings variabelen verwijzen in opdracht regels voor taken, en in de Program ma's en scripts die worden uitgevoerd door de opdracht regels.

Zie [omgevings instellingen voor taken](./jobs-and-tasks.md#environment-settings-for-tasks)voor meer informatie over het gebruik van omgevings variabelen met batch.

## <a name="environment-variable-visibility"></a>Zicht baarheid van omgevings variabele

Deze omgevings variabelen zijn alleen zichtbaar in de context van de **taak gebruiker**, het gebruikers account op het knoop punt waaronder een taak wordt uitgevoerd. U ziet deze *niet* als u [extern verbinding maakt](./error-handling.md#connect-to-compute-nodes) met een rekenknooppunt via Remote Desktop Protocol (RDP) of Secure Shell (SSH) en de omgevingsvariabelen weergeeft. Dit komt doordat het gebruikersaccount dat voor de externe verbinding wordt gebruikt, niet hetzelfde is als het account dat door de taak wordt gebruikt.

Als u de huidige waarde van een omgevings variabele wilt ophalen, start u `cmd.exe` op een Windows-reken knooppunt of `/bin/sh` op een Linux-knoop punt:

`cmd /c set <ENV_VARIABLE_NAME>`

`/bin/sh -c "printenv <ENV_VARIABLE_NAME>"`

## <a name="command-line-expansion-of-environment-variables"></a>Opdracht regel expansie van omgevings variabelen

De opdracht regels die door taken op reken knooppunten worden uitgevoerd, worden niet uitgevoerd onder een shell. Daarom kunnen deze opdracht regels niet standaard profiteren van shell-functies zoals omgevings variabele-uitbrei ding (dit omvat de `PATH` ). Als u gebruik wilt maken van deze functies, moet u **de shell aanroepen** in de opdracht regel. U kunt bijvoorbeeld starten `cmd.exe` op Windows-reken knooppunten of `/bin/sh` op Linux-knoop punten:

`cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

`/bin/sh -c "MyTaskApplication $MY_ENV_VAR"`

## <a name="environment-variables"></a>Omgevingsvariabelen

| Naam van de variabele                     | Beschrijving                                                              | Beschikbaarheid | Voorbeeld |
|-----------------------------------|--------------------------------------------------------------------------|--------------|---------|
| AZ_BATCH_ACCOUNT_NAME           | De naam van het batch-account waartoe de taak behoort.                  | Alle taken.   | mybatchaccount gemaakt |
| AZ_BATCH_ACCOUNT_URL            | De URL van het batch-account. | Alle taken. | `https://myaccount.westus.batch.azure.com` |
| AZ_BATCH_APP_PACKAGE            | Een voor voegsel van alle omgevings variabelen voor het app-pakket. Als bijvoorbeeld toepassing "FOO" versie "1" is geïnstalleerd op een groep, wordt de omgevings variabele AZ_BATCH_APP_PACKAGE_FOO_1 (op Linux) of AZ_BATCH_APP_PACKAGE_FOO # 1 (in Windows). AZ_BATCH_APP_PACKAGE_FOO_1 verwijst naar de locatie waar het pakket is gedownload (een map). Wanneer u de standaard versie van het app-pakket gebruikt, gebruikt u de omgevings variabele AZ_BATCH_APP_PACKAGE zonder de versie nummers. Als in Linux en de naam van het toepassings pakket ' agent-Linux-x64 ' is en de versie ' 1.1.46.0 ' is, is de omgevings naam in feite: AZ_BATCH_APP_PACKAGE_agent_linux_x64_1_1_46_0, met behulp van onderstrepings tekens en kleine letters. Klik [hier](./batch-application-packages.md#execute-the-installed-applications) voor meer informatie. | Elke taak met een gekoppeld app-pakket. Ook beschikbaar voor alle taken als het knoop punt zelf toepassings pakketten heeft. | AZ_BATCH_APP_PACKAGE_FOO_1 (Linux) of AZ_BATCH_APP_PACKAGE_FOO # 1 (Windows) |
| AZ_BATCH_AUTHENTICATION_TOKEN   | Een verificatie token dat toegang verleent tot een beperkt aantal batch-service bewerkingen. Deze omgevings variabele is alleen aanwezig als de [authenticationTokenSettings](/rest/api/batchservice/task/add#authenticationtokensettings) worden ingesteld wanneer de [taak wordt toegevoegd](/rest/api/batchservice/task/add#request-body). De token waarde wordt in de batch-Api's gebruikt als referenties voor het maken van een batch-client, zoals in de [.net API BatchClient. Open ()](/dotnet/api/microsoft.azure.batch.batchclient.open#Microsoft_Azure_Batch_BatchClient_Open_Microsoft_Azure_Batch_Auth_BatchTokenCredentials_). | Alle taken. | OAuth2-toegangs token |
| AZ_BATCH_CERTIFICATES_DIR       | Een map in de [werkmap][files_dirs] van de taak waarin certificaten worden opgeslagen voor Linux-reken knooppunten. Deze omgevings variabele is niet van toepassing op Windows-reken knooppunten.                                                  | Alle taken.   |  /mnt/batch/tasks/workitems/batchjob001/job-1/task001/certs |
| AZ_BATCH_HOST_LIST              | De lijst met knoop punten die worden toegewezen aan een [taak met meerdere instanties][multi_instance] in de indeling `nodeIP,nodeIP` . | Primaire en subtaken voor meerdere instanties. | `10.0.0.4,10.0.0.5` |
| AZ_BATCH_IS_CURRENT_NODE_MASTER | Hiermee geeft u op of het huidige knoop punt het hoofd knooppunt is voor een [taak met meerdere exemplaren][multi_instance]. Mogelijke waarden zijn `true` en `false` .| Primaire en subtaken voor meerdere instanties. | `true` |
| AZ_BATCH_JOB_ID                 | De ID van de job waartoe de taak behoort. | Alle taken behalve taak starten. | batchjob001 |
| AZ_BATCH_JOB_PREP_DIR           | Het volledige pad van de map voor taak voorbereidings [taak][files_dirs] op het knoop punt. | Alle taken behalve taak begin taak en taak voorbereiden. Alleen beschikbaar als de taak is geconfigureerd met een taak voorbereidings taak. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation |
| AZ_BATCH_JOB_PREP_WORKING_DIR   | Het volledige pad naar de werkmap van de taak voorbereidings [taak][files_dirs] op het knoop punt. | Alle taken behalve taak begin taak en taak voorbereiden. Alleen beschikbaar als de taak is geconfigureerd met een taak voorbereidings taak. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation\wd |
| AZ_BATCH_MASTER_NODE            | Het IP-adres en de poort van het reken knooppunt waarop de primaire taak van een [taak met meerdere exemplaren][multi_instance] wordt uitgevoerd. Gebruik niet de poort die u hier hebt opgegeven voor de MPI-of NCCL-communicatie. deze is gereserveerd voor de Azure Batch-service. Gebruik in plaats daarvan de variabele MASTER_PORT, door deze in te stellen met een waarde die wordt door gegeven via de opdracht regel argument (poort 6105 is een goede standaard keuze) of gebruik de waarde AML-sets als dat wel het geval is. | Primaire en subtaken voor meerdere instanties. | `10.0.0.4:6000` |
| AZ_BATCH_NODE_ID                | De ID van het knoop punt waaraan de taak is toegewezen. | Alle taken. | TVM-1219235766_3-20160919t172711z |
| AZ_BATCH_NODE_IS_DEDICATED      | Als `true` het huidige knoop punt een toegewezen knoop punt is. Als `false` is het een [knoop punt met een lage prioriteit](batch-low-pri-vms.md). | Alle taken. | `true` |
| AZ_BATCH_NODE_LIST              | De lijst met knoop punten die worden toegewezen aan een [taak met meerdere instanties][multi_instance] in de indeling `nodeIP;nodeIP` . | Primaire en subtaken voor meerdere instanties. | `10.0.0.4;10.0.0.5` |
| AZ_BATCH_NODE_MOUNTS_DIR        | Het volledige pad van de [bestandssysteem koppelings](virtual-file-mount.md) locatie op knooppunt niveau waar alle koppelings directory's zich bevinden. Windows-bestands shares maken gebruik van een stationsletter, dus voor Windows is het koppel station onderdeel van apparaten en stations.  |  Alle taken met inbegrip van een begin taak hebben toegang tot de gebruiker, op voor hand dat de gebruiker op de hoogte is van de koppelings machtigingen voor de gekoppelde map. | In Ubuntu is de locatie bijvoorbeeld: `/mnt/batch/tasks/fsmounts` |
| AZ_BATCH_NODE_ROOT_DIR          | Het volledige pad naar de hoofdmap van alle [batch-mappen][files_dirs] op het knoop punt. | Alle taken. | C:\user\tasks |
| AZ_BATCH_NODE_SHARED_DIR        | Het volledige pad naar de [gedeelde map][files_dirs] in het knoop punt. Alle taken die worden uitgevoerd op een knoop punt hebben lees-en schrijf toegang tot deze map. Taken die op andere knoop punten worden uitgevoerd, hebben geen externe toegang tot deze map (dit is geen gedeelde netwerkmap). | Alle taken. | C:\user\tasks\shared |
| AZ_BATCH_NODE_STARTUP_DIR       | Het volledige pad van de [map][files_dirs] voor het starten van de taak in het knoop punt. | Alle taken. | C:\user\tasks\startup |
| AZ_BATCH_POOL_ID                | De ID van de pool waarin de taak wordt uitgevoerd. | Alle taken. | batchpool001 |
| AZ_BATCH_TASK_DIR               | Het volledige pad naar de [map met taken][files_dirs] op het knoop punt. Deze map bevat de `stdout.txt` en `stderr.txt` voor de taak en de AZ_BATCH_TASK_WORKING_DIR. | Alle taken. | C:\user\tasks\workitems\batchjob001\job-1\task001 |
| AZ_BATCH_TASK_ID                | De id van de huidige taak. | Alle taken behalve taak starten. | task001 |
| AZ_BATCH_TASK_SHARED_DIR | Een mappad dat identiek is voor de primaire taak en elke subtaak van een [taak met meerdere exemplaren][multi_instance]. Het pad bestaat op elk knoop punt waarop de taak met meerdere exemplaren wordt uitgevoerd en is lezen/schrijven toegankelijk voor de taak opdrachten die worden uitgevoerd op dat knoop punt (zowel de [coördinatie opdracht][coord_cmd] als de [toepassing opdracht][app_cmd]). Subtaken of een primaire taak die op andere knoop punten wordt uitgevoerd, hebben geen externe toegang tot deze map (dit is geen gedeelde netwerkmap). | Primaire en subtaken voor meerdere instanties. | C:\user\tasks\workitems\multiinstancesamplejob\job-1\multiinstancesampletask |
| AZ_BATCH_TASK_WORKING_DIR       | Het volledige pad naar de [werkmap][files_dirs] van de taak op het knoop punt. De taak die momenteel wordt uitgevoerd, heeft lees-en schrijf toegang tot deze map. | Alle taken. | C:\user\tasks\workitems\batchjob001\job-1\task001\wd |
| CCP_NODES                       | De lijst met knoop punten en het aantal kernen per knoop punt dat is toegewezen aan een [taak met meerdere exemplaren][multi_instance]. Knoop punten en kernen worden weer gegeven in de indeling `numNodes<space>node1IP<space>node1Cores<space>`<br/>`node2IP<space>node2Cores<space> ...`, waarbij het aantal knoop punten wordt gevolgd door een of meer IP-adressen van knoop punten en het aantal kern geheugens voor beide. |  Primaire en subtaken voor meerdere instanties. |`2 10.0.0.4 1 10.0.0.5 1` |

[files_dirs]: ./files-and-directories.md
[multi_instance]: ./batch-mpi.md
[coord_cmd]: ./batch-mpi.md#coordination-command
[app_cmd]: ./batch-mpi.md#application-command
