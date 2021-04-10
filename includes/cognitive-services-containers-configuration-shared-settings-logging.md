---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: a2a5935079a339e85713e9cbcd0f32c211cabbb5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729815"
---
Met de instellingen wordt de `Logging` ondersteuning voor ASP.net core logboek registratie voor uw container beheerd. U kunt dezelfde configuratie-instellingen en-waarden gebruiken voor uw container die u gebruikt voor een ASP.NET Core-toepassing. 

De volgende logboek registratie providers worden ondersteund door de container:

|Provider|Doel|
|--|--|
|[Console](/aspnet/core/fundamentals/logging/#console-provider)|De provider van ASP.NET Core `Console` logboek registratie. Alle ASP.NET Core configuratie-instellingen en standaard waarden voor deze logboek registratie provider worden ondersteund.|
|[Fouten opsporen](/aspnet/core/fundamentals/logging/#debug-provider)|De provider van ASP.NET Core `Debug` logboek registratie. Alle ASP.NET Core configuratie-instellingen en standaard waarden voor deze logboek registratie provider worden ondersteund.|
|[Schijf](#disk-logging)|De JSON-logboek registratie provider. Deze logboek registratie provider schrijft logboek gegevens naar de uitvoer koppeling.|

Met deze container opdracht worden logboek gegevens in de JSON-indeling opgeslagen in de uitvoer koppeling:

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
Logging:Disk:Format=json
```

Deze container opdracht bevat informatie over fout opsporing, voorafgegaan door `dbug` , terwijl de container wordt uitgevoerd:

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
Logging:Console:LogLevel:Default=Debug
```

### <a name="disk-logging"></a>Schijf registratie

De `Disk` logboek registratie provider ondersteunt de volgende configuratie-instellingen:

| Name | Gegevenstype | Beschrijving |
|------|-----------|-------------|
| `Format` | Tekenreeks | De uitvoer indeling voor logboek bestanden.<br/> **Opmerking:** Deze waarde moet worden ingesteld op `json` om de logboek registratie provider in te scha kelen. Als deze waarde is opgegeven zonder ook een uitvoer koppeling op te geven tijdens het instantiëren van een container, treedt er een fout op. |
| `MaxFileSize` | Geheel getal | De maximale grootte in mega bytes (MB) van een logboek bestand. Wanneer de grootte van het huidige logboek bestand voldoet aan of groter is dan deze waarde, wordt er een nieuw logboek bestand gestart door de logboek registratie provider. Als-1 is opgegeven, wordt de grootte van het logboek bestand alleen beperkt door de maximale bestands grootte, indien aanwezig, voor de uitvoer koppeling. De standaardwaarde is 1. |

Zie [instellingen bestands configuratie](/aspnet/core/fundamentals/logging/)voor meer informatie over het configureren van ondersteuning voor logboek registratie in ASP.net core.