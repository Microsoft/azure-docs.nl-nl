---
title: Problemen oplossen bij het gebruik van de Azure Cosmos DB-emulator
description: Meer informatie over hoe u troubleshot service niet beschikbaar, certificaat, versleuteling en versie beheer problemen ondervindt bij het gebruik van de Azure Cosmos DB-emulator.
ms.service: cosmos-db
ms.topic: troubleshooting
author: markjbrown
ms.author: mjbrown
ms.date: 09/17/2020
ms.custom: contperfq1
ms.openlocfilehash: 83559cc2ab1ca9597cca405333061e53b6f56aa9
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96030744"
---
# <a name="troubleshoot-issues-when-using-the-azure-cosmos-db-emulator"></a>Problemen oplossen bij het gebruik van de Azure Cosmos DB-emulator
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

De Azure Cosmos DB Emulator is een lokale omgeving waarin de Azure Cosmos DB-service wordt geëmuleerd voor ontwikkelingsdoeleinden. Gebruik de tips in dit artikel voor hulp bij het oplossen van problemen die optreden bij het installeren of gebruiken van de Azure Cosmos DB-emulator. 

Als u een nieuwe versie van de emulator hebt geïnstalleerd en er fouten optreden, zorg dan dat u uw gegevens opnieuw instelt. U kunt uw gegevens opnieuw instellen door met de rechter muisknop te klikken op het pictogram Azure Cosmos DB emulator op het systeemvak en vervolgens op gegevens opnieuw instellen te klikken.... Als dat niet het geval is, kunt u de emulator en eventuele oudere versies van de emulator verwijderen, indien gevonden, verwijdert u de map *C:\Program files\Azure Cosmos DB emulator* en installeert u de emulator opnieuw. Zie [De lokale emulator verwijderen](local-emulator.md#uninstall) voor instructies. Als u de gegevens opnieuw instelt, gaat u naar `%LOCALAPPDATA%\CosmosDBEmulator` locatie en verwijdert u de map.

## <a name="troubleshoot-corrupted-windows-performance-counters"></a>Problemen met beschadigde Windows-prestatie meter items oplossen

* Als de Azure Cosmos DB-emulator vastloopt, verzamelt u de dump bestanden vanuit `%LOCALAPPDATA%\CrashDumps` de map, comprimeert u deze en opent u een ondersteunings ticket vanuit de [Azure Portal](https://portal.azure.com).

* Als `Microsoft.Azure.Cosmos.ComputeServiceStartupEntryPoint.exe` vastloopt, kan dit een indicatie zijn dat de prestatiemeteritems beschadigd zijn. Dit probleem kunt u meestal oplossen door de volgende opdracht uit te voeren vanaf een opdrachtprompt met beheerdersrechten:

  ```cmd
  lodctr /R
   ```

## <a name="troubleshoot-connectivity-issues"></a>Verbindingsproblemen oplossen

* Als er sprake is van een verbindingsprobleem, [verzamelt u traceringsbestanden](#trace-files), comprimeert u de bestanden en opent u een ondersteuningsticket via de [Azure-portal](https://portal.azure.com).

* Als u het bericht **Service niet beschikbaar** krijgt, is het mogelijk dat de emulator de netwerkstack niet kan initialiseren. Controleer of de veilige Pulse-client of Juniper-netwerkclient is geïnstalleerd. De netwerkfilterstuurprogramma's van deze clients kunnen mogelijk de oorzaak zijn van het probleem. Doorgaans kan het probleem worden opgelost door stuurprogramma's voor netwerkfilters van derden te verwijderen. U kunt de emulator ook starten met/DisableRIO, waarmee de netwerkcommunicatie van de emulator wordt overgezet naar normale Winsock. 

* Als u **de aanvraag ' verboden ' ontvangt, wordt het bericht ': ' verzonden met een verboden versleuteling in het Transit Protocol of de versleuteling. Controleer de mini maal toegestane protocol instelling voor SSL/TLS...** verbindings problemen. Dit kan worden veroorzaakt door algemene wijzigingen in het besturings systeem (bijvoorbeeld insider preview Build 20170) of de instellingen van de browser die TLS 1,3 als standaard inschakelen. Er kan een soort gelijke fout optreden wanneer u de SDK gebruikt om een aanvraag uit te voeren op de Cosmos-emulator, zoals **Microsoft.Azure.Documents.DocumentClientException: er wordt een aanvraag gemaakt met een verboden versleuteling in het Transit Protocol of de versleuteling. Controleer de mini maal toegestane protocol instelling voor SSL/TLS-accounts**. Dit werkt op dit moment zoals verwacht, omdat de Cosmos Emulator alleen het TLS 1.2-protocol accepteert en gebruikt. De aanbevolen oplossing is om de instellingen te wijzigen en standaard te bewerken in TLS 1,2. bijvoorbeeld: in IIS-beheer navigeert u naar "sites"-> "standaard websites" en zoekt u de site bindingen voor poort 8081 en bewerkt u deze om TLS 1,3 uit te scha kelen. Een vergelijkbare bewerking kan worden uitgevoerd voor de webbrowser via de opties in Instellingen.

* Als de emulator wordt uitgevoerd en uw computer naar de slaapstand gaat of er besturingssysteemupdates worden uitgevoerd, wordt mogelijk het bericht **Service is momenteel niet beschikbaar** weergegeven. Stel de gegevens van de emulator opnieuw in door met de rechtermuisknop op het pictogram te klikken dat wordt weergegeven in het Windows-systeemvak. Selecteer vervolgens **Reset Data** (Gegevens opnieuw instellen).

## <a name="collect-trace-files"></a><a id="trace-files"></a>Traceringsbestanden verzamelen

Voor het verzamelen van foutopsporingsgegevens, voert u de volgende opdrachten uit vanaf een opdrachtprompt voor een beheerder:

1. Navigeer naar het pad waar de emulator is geïnstalleerd:

   ```bash
   cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"
   ```

1. Sluit de emulator af en Bekijk de systeem balk om te controleren of het programma is afgesloten. Het kan een minuut duren voordat de bewerking is voltooid. U kunt ook **Afsluiten** selecteren in de gebruikers interface van de Azure Cosmos DB emulator.

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /shutdown
   ```

1. Start de logboek registratie met de volgende opdracht:

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /startwprtraces
   ```

1. De emulator starten

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe
   ```

1. Reproduceer het probleem. Als de Data Explorer niet werkt, hoeft u alleen te wachten totdat de browser een paar seconden wordt geopend om de fout te ondervangen.

1. Stop de registratie met de volgende opdracht:

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /stopwprtraces
   ```
   
1. Navigeer naar `%ProgramFiles%\Azure Cosmos DB Emulator` pad en zoek het bestand *docdbemulator_000001. etl* .

1. Open een ondersteuningsticket via de [Azure-portal](https://portal.azure.com) en stuur het ETL-bestand mee, evenals de stappen om het probleem te reproduceren.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u problemen met de lokale emulator kunt oplossen. U kunt nu door gaan met de volgende artikelen:

* [De Azure Cosmos DB-emulator certificaten exporteren voor gebruik met Java-, python-en Node.js-apps](local-emulator-export-ssl-certificates.md)
* [Opdracht regel parameters en Power shell-opdrachten gebruiken om de emulator te beheren](emulator-command-line-parameters.md)
