---
title: De Azure File Sync | Microsoft Docs
description: Lees hoe u uw implementatie Azure File Sync bewaken met behulp van Azure Monitor, opslagsynchronisatieservice en Windows Server.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 8e9fbd5fd8fc90681432ee8403b6dd139d02a5a5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796999"
---
# <a name="monitor-azure-file-sync"></a>Azure File Sync bewaken

Gebruik Azure File Sync om de bestands shares van uw organisatie te centraliseren in Azure Files, met behoud van de flexibiliteit, prestaties en compatibiliteit van een on-premises bestandsserver. Door Azure File Sync wordt Windows Server getransformeerd in een snelle cache van uw Azure-bestandsshare. U kunt elk protocol dat beschikbaar is in Windows Server, inclusief SMB, NFS en FTPS, gebruiken voor lokale toegang tot uw gegevens. U kunt zoveel caches hebben als u nodig hebt over de hele wereld.

In dit artikel wordt beschreven hoe u uw Azure File Sync bewaakt met behulp van Azure Monitor, opslagsynchronisatieservice en Windows Server.

De volgende scenario's worden in deze handleiding behandeld: 
- Bekijk Azure File Sync metrische gegevens in Azure Monitor.
- Maak waarschuwingen in Azure Monitor u proactief op de hoogte te stellen van kritieke omstandigheden.
- Bekijk de status van uw Azure File Sync implementatie met behulp van de Azure Portal.
- De gebeurtenislogboeken en prestatiemeters op uw Windows-servers gebruiken om de status van uw Azure File Sync bewaken. 

## <a name="azure-monitor"></a>Azure Monitor

Gebruik [Azure Monitor](../../azure-monitor/overview.md) om metrische gegevens weer te geven en om waarschuwingen te configureren voor synchronisatie, opslag in cloudlagen en serverconnectiviteit.  

### <a name="metrics"></a>Metrische gegevens

Metrische Azure File Sync zijn standaard ingeschakeld en worden elke 15 minuten Azure Monitor gegevens verzonden.

**Metrische gegevens Azure File Sync weergeven in Azure Monitor**
1. Ga naar uw **opslagsynchronisatieservice** in **de Azure Portal** klik op **Metrische gegevens.**
2. Klik op **de vervolgkeuzepagina** Metrische gegevens en selecteer de metrische gegevens die u wilt weergeven.

![Schermopname van Azure File Sync metrische gegevens](media/storage-sync-files-troubleshoot/file-sync-metrics.png)

De volgende metrische gegevens voor Azure File Sync zijn beschikbaar in Azure Monitor:

| Naam van metrische gegevens | Description |
|-|-|
| Gesynchroniseerde bytes | Grootte van overgedragen gegevens (uploaden en downloaden).<br><br>Eenheid: Bytes<br>Aggregatietype: Som<br>Toepasselijke dimensies: Naam van server-eindpunt, Synchronisatierichting, Naam van synchronisatiegroep |
| Terughalen van cloudopslaglagen | Grootte van de gegevens die zijn ingeroepen.<br><br>**Opmerking:** deze metrische gegevens worden in de toekomst verwijderd. Gebruik de metrische gegevens voor het inroepen van cloudlagen om de grootte van de ingeroepen gegevens te bewaken.<br><br>Eenheid: Bytes<br>Aggregatietype: Som<br>Toepasselijke dimensie: Servernaam |
| Terugroepgrootte van cloudopslaglagen | Grootte van de gegevens die zijn ingeroepen.<br><br>Eenheid: Bytes<br>Aggregatietype: Som<br>Toepasselijke dimensies: Servernaam, Naam van synchronisatiegroep |
| Terugroepgrootte van cloudopslaglagen per toepassing | Grootte van gegevens die door de toepassing zijn ingeroepen.<br><br>Eenheid: Bytes<br>Aggregatietype: Som<br>Toepasselijke dimensies: Toepassingsnaam, Servernaam, Naam van synchronisatiegroep |
| Terugroepdoorvoer van cloudopslaglagen | Grootte van de doorvoer voor het inroepen van gegevens.<br><br>Eenheid: Bytes<br>Aggregatietype: Som<br>Toepasselijke dimensies: Servernaam, Naam van synchronisatiegroep |
| Bestanden worden niet gesynchroniseerd | Aantal bestanden dat niet kan worden gesynchroniseerd.<br><br>Eenheid: Aantal<br>Aggregatietypen: Gemiddelde, Som<br>Toepasselijke dimensies: Naam van server-eindpunt, Synchronisatierichting, Naam van synchronisatiegroep |
| Gesynchroniseerde bestanden | Aantal overgedragen bestanden (uploaden en downloaden).<br><br>Eenheid: Aantal<br>Aggregatietype: Som<br>Toepasselijke dimensies: Naam van server-eindpunt, Synchronisatierichting, Naam van synchronisatiegroep |
| Onlinestatus van server | Aantal heartbeats dat van de server is ontvangen.<br><br>Eenheid: Aantal<br>Aggregatietype: Maximum<br>Toepasselijke dimensie: Servernaam |
| Resultaat synchronisatiesessie | Resultaat van synchronisatiesessie (1=geslaagde synchronisatiesessie; 0=mislukte synchronisatiesessie)<br><br>Eenheid: Aantal<br>Aggregatietypen: Maximum<br>Toepasselijke dimensies: Naam van server-eindpunt, Synchronisatierichting, Naam van synchronisatiegroep |

### <a name="alerts"></a>Waarschuwingen

Met waarschuwingen wordt u proactief gewaarschuwd wanneer er belangrijke voorwaarden in uw bewakingsgegevens worden gevonden. Zie Overzicht van waarschuwingen in Azure Monitor voor meer informatie over het configureren van [Microsoft Azure.](../../azure-monitor/alerts/alerts-overview.md)

**Waarschuwingen maken voor Azure File Sync**

1. Ga naar uw **opslagsynchronisatieservice** in **Azure Portal**. 
2. Klik **op Waarschuwingen** in de sectie Bewaking en klik vervolgens op + Nieuwe **waarschuwingsregel.**
3. Klik **op Voorwaarde selecteren** en geef de volgende informatie op voor de waarschuwing: 
    - **Meting**
    - **Dimensienaam**
    - **Waarschuwingslogica**
4. Klik **op Actiegroep selecteren** en voeg een actiegroep (e-mail, sms, enzovoort) toe aan de waarschuwing door een bestaande actiegroep te selecteren of een nieuwe actiegroep te maken.
5. Vul de **waarschuwingsdetails** in, zoals de naam van **de waarschuwingsregel,** **Beschrijving** **en Ernst.**
6. Klik **op Waarschuwingsregel maken** om de waarschuwing te maken.  

De volgende tabel bevat enkele voorbeeldscenario's die moeten worden bewaakt en de juiste metrische gegevens die voor de waarschuwing moeten worden gebruikt:

| Scenario | Te gebruiken metrische gegevens voor waarschuwing |
|-|-|
| Status van server-eindpunt toont een fout in de portal | Resultaat van synchronisatiesessie |
| Bestanden worden niet gesynchroniseerd met een server of cloud-eindpunt | Bestanden worden niet gesynchroniseerd |
| De geregistreerde server kan niet communiceren met de opslagsynchronisatieservice | Onlinestatus van server |
| In cloudlagen terughalen is de grootte van 500GiB in een dag overschreden  | In cloudlagen terughalen van grootte |

Zie de sectie Waarschuwingsvoorbeelden voor instructies over het maken van waarschuwingen [voor deze scenario's.](#alert-examples)

## <a name="storage-sync-service"></a>Opslagsynchronisatieservice

Als u de status van uw implementatie Azure File Sync in de  **Azure Portal,** gaat u naar de opslagsynchronisatieservice en is de volgende informatie beschikbaar:

- Status van geregistreerde server
- Status van server-eindpunt
    - Bestanden worden niet gesynchroniseerd
    - Synchronisatieactiviteit
    - Efficiëntie van opslaglagen in de cloud
    - Bestanden worden niet in lagen opgeslagen
    - Fouten in het terughalen
- Metrische gegevens

### <a name="registered-server-health"></a>Status van geregistreerde server

Als u de status **van de geregistreerde server** in de portal wilt weergeven, gaat u naar de sectie Geregistreerde **servers** van de **opslagsynchronisatieservice**.

![Schermopname van de status van geregistreerde servers](media/storage-sync-files-troubleshoot/file-sync-registered-servers.png)

- Als de **status van de** geregistreerde server **Online** is, communiceert de server met de service.
- Als de **status Geregistreerde server** offline wordt **weergegeven,** wordt het storage sync monitor-proces (AzureStorageSyncMonitor.exe) niet uitgevoerd of heeft de server geen toegang tot de Azure File Sync service. Zie de [documentatie voor probleemoplossing](file-sync-troubleshoot.md?tabs=portal1%252cazure-portal#server-endpoint-noactivity) voor hulp.

### <a name="server-endpoint-health"></a>Status van server-eindpunt

Als u de status van een **server-eindpunt**  in de portal wilt weergeven, gaat u naar de sectie Synchronisatiegroepen van de opslagsynchronisatieservice **en** selecteert u **een synchronisatiegroep.**

![Schermopname van de status van het server-eindpunt](media/storage-sync-files-troubleshoot/file-sync-server-endpoint-health.png)

- De status en synchronisatieactiviteit van het **server-eindpunt** **in** de portal is gebaseerd op de synchronisatiegebeurtenissen die zijn vastgelegd in het telemetriegebeurtenislogboek op de server (id 9102 en 9302). Als een synchronisatiesessie mislukt vanwege een tijdelijke fout, zoals een geannuleerde fout, wordt het server-eindpunt **nog** steeds in orde weergegeven in de portal zolang de huidige synchronisatiesessie voortgang maakt (bestanden worden toegepast). Gebeurtenis-id 9302 is de voortgangsgebeurtenis van de synchronisatie en gebeurtenis-id 9102 wordt geregistreerd zodra een synchronisatiesessie is voltooid.  Zie Synchronisatie van status [en synchronisatie](file-sync-troubleshoot.md?tabs=server%252cazure-portal#broken-sync) [voor meer informatie.](file-sync-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session) Als in de status van het server-eindpunt een **fout of** geen activiteit wordt **weergegeven,** bekijkt u [de](file-sync-troubleshoot.md?tabs=portal1%252cazure-portal#common-sync-errors) documentatie voor probleemoplossing voor hulp.
- Het **aantal bestanden dat niet wordt** gesynchroniseerd in de portal is gebaseerd op de gebeurtenis-id 9121 die is geregistreerd in het telemetriegebeurtenislogboek op de server. Deze gebeurtenis wordt geregistreerd voor elke fout per item zodra de synchronisatiesessie is voltooid. Als u fouten per item wilt oplossen, Hoe kan ik of er specifieke bestanden of mappen zijn die [niet worden gesynchroniseerd?](file-sync-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).
- Als u de **efficiëntie van cloudopslaglagen** in de portal wilt weergeven, gaat u naar De eigenschappen van **het server-eindpunt** en navigeert u naar de sectie **Cloudlagen.** De gegevens die worden verstrekt voor de efficiëntie van opslag in cloudlagen, zijn gebaseerd op gebeurtenis-id 9071 die is vastgelegd in het gebeurtenislogboek telemetrie op de server. Zie Cloudlagen bewaken [voor meer informatie.](file-sync-monitor-cloud-tiering.md)
- Als u bestanden wilt weergeven die geen **opslaglagen bevatten** en fouten **in** de portal wilt inroepen, gaat u naar De eigenschappen van het **server-eindpunt** en navigeert u naar de sectie **Cloudlagen.** **Bestanden die** niet in lagen worden opgeslagen, zijn gebaseerd op gebeurtenis-id 9003 die is geregistreerd in het telemetriegebeurtenislogboek op de server en het inroepen van fouten **is** gebaseerd op gebeurtenis-id 9006. Zie How to troubleshoot files that [fail to tier](file-sync-troubleshoot.md?tabs=portal1%252cazure-portal#how-to-troubleshoot-files-that-fail-to-tier) (Problemen oplossen met bestanden die niet in een laag kunnen worden opgeslagen) en How to troubleshoot files that fail to be recalled (Problemen oplossen met bestanden die niet kunnen worden ingeroepen) voor informatie over het onderzoeken van bestanden die niet kunnen [worden ingeroepen.](file-sync-troubleshoot.md?tabs=portal1%252cazure-portal#how-to-troubleshoot-files-that-fail-to-be-recalled)

### <a name="metric-charts"></a>Grafieken met metrische gegevens

- De volgende grafieken met metrische gegevens zijn te zien in de portal van de opslagsynchronisatieservice:

  | Naam van metrische gegevens | Description | Bladenaam |
  |-|-|-|
  | Gesynchroniseerde bytes | Grootte van overgedragen gegevens (uploaden en downloaden) | Synchronisatiegroep, server-eindpunt |
  | Terughalen van cloudopslaglagen | Grootte van de gegevens die zijn ingeroepen | Geregistreerde servers |
  | Bestanden worden niet gesynchroniseerd | Aantal bestanden dat niet kan worden gesynchroniseerd | Server-eindpunt |
  | Gesynchroniseerde bestanden | Aantal overgedragen bestanden (uploaden en downloaden) | Synchronisatiegroep, server-eindpunt |
  | Onlinestatus van server | Aantal heartbeats ontvangen van de server | Geregistreerde servers |

- Zie voor meer informatie [Azure Monitor.](#azure-monitor)

  > [!Note]  
  > De grafieken in de portal van de opslagsynchronisatieservice hebben een tijdsbereik van 24 uur. Als u verschillende tijdsbereiken of dimensies wilt weergeven, gebruikt u Azure Monitor.

## <a name="windows-server"></a>Windows Server

Op de **Windows-server** waar de Azure File Sync-agent is geïnstalleerd, kunt u de status  van de server-eindpunten op die server weergeven met behulp van de gebeurtenislogboeken en **prestatiemeters.**

### <a name="event-logs"></a>Gebeurtenislogboeken

Gebruik het gebeurtenislogboek Telemetrie op de server om de status van geregistreerde servers, synchronisatie en cloudopslaglagen te bewaken. Het gebeurtenislogboek telemetrie bevindt zich in Logboeken *onder Toepassingen en services\Microsoft\FileSync\Agent.*

Synchronisatie health

- Gebeurtenis-id 9102 wordt geregistreerd zodra een synchronisatiesessie is voltooid. Gebruik deze gebeurtenis om te bepalen of synchronisatiesessies zijn geslaagd (**HResult = 0**) en of er synchronisatiefouten per item zijn (**PerItemErrorCount).** Zie de documentatie synchronisatie [status en](file-sync-troubleshoot.md?tabs=server%252cazure-portal#broken-sync) fouten per item  [voor meer](file-sync-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) informatie.

  > [!Note]  
  > Soms mislukken synchronisatiesessies in het algemeen of hebben ze een Niet-nul PerItemErrorCount. Ze maken echter nog steeds voortgang en sommige bestanden worden gesynchroniseerd. U kunt dit zien in de toegepaste velden, zoals AppliedFileCount, AppliedDirCount, AppliedTombstoneCount en AppliedSizeBytes. Deze velden laten zien hoeveel van de sessie is geslaagd. Als er meerdere synchronisatiesessies in een rij mislukken en het aantal toegepaste sessies toeneemt, geeft u synchronisatietijd om het opnieuw te proberen voordat u een ondersteuningsticket opent.

- Gebeurtenis-id 9121 wordt geregistreerd voor elke fout per item zodra de synchronisatiesessie is voltooid. Gebruik deze gebeurtenis om het aantal bestanden te bepalen dat niet kan worden gesynchroniseerd met deze fout (**PersistentCount** en **TransientCount).** Permanente fouten per item moeten worden onderzocht. Zie Hoe kan ik of er specifieke bestanden of mappen zijn die [niet worden gesynchroniseerd?](file-sync-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).

- Gebeurtenis-id 9302 wordt elke 5 tot 10 minuten geregistreerd als er een actieve synchronisatiesessie is. Gebruik deze gebeurtenis om te bepalen hoeveel items moeten worden gesynchroniseerd (**TotalItemCount),** het aantal items dat tot nu toe is gesynchroniseerd **(AppliedItemCount)** en het aantal items dat niet kan worden gesynchroniseerd vanwege een fout per item (**PerItemErrorCount).** Als de synchronisatie niet wordt uitgevoerd **(AppliedItemCount=0),** mislukt de synchronisatiesessie uiteindelijk en wordt gebeurtenis-id 9102 geregistreerd met de fout. Zie de documentatie over synchronisatie-voortgang [voor meer informatie.](file-sync-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)

Status van geregistreerde server

- Gebeurtenis-id 9301 wordt elke 30 seconden geregistreerd wanneer een server taken opvraagt bij de service. Als GetNextJob eindigt met **status = 0,** kan de server communiceren met de service. Als GetNextJob eindigt met een fout, raadpleegt u de documentatie voor [probleemoplossing](file-sync-troubleshoot.md?tabs=portal1%252cazure-portal#server-endpoint-noactivity) voor hulp.

Status van cloudopslaglagen

- Als u laagactiviteit op een server wilt bewaken, gebruikt u gebeurtenis-id 9003, 9016 en 9029 in het gebeurtenislogboek Telemetrie, dat zich bevindt in Logboeken onder Toepassingen en *services\Microsoft\FileSync\Agent.*

  - Gebeurtenis-id 9003 biedt foutdistributie voor een server-eindpunt. Bijvoorbeeld: Totaal aantal fouten en ErrorCode. Er wordt één gebeurtenis geregistreerd per foutcode.
  - Gebeurtenis-id 9016 biedt ghostingresultaten voor een volume. Bijvoorbeeld: Percentage vrije ruimte is, Aantal gedrenkte bestanden in sessie en Aantal bestanden dat niet is gedeerd.
  - Gebeurtenis-id 9029 biedt informatie over een ghosting-sessie voor een server-eindpunt. Bijvoorbeeld: aantal bestanden dat is geprobeerd in de sessie, Aantal bestanden in een laag in de sessie en Aantal bestanden dat al in een laag is opgeslagen.

- Als u de terugroepactiviteit op een server wilt bewaken, gebruikt u gebeurtenis-id 9005, 9006, 9009, 9059 en 9071 in het gebeurtenislogboek Telemetrie, dat zich bevindt in Logboeken *onder Applications and Services\Microsoft\FileSync\Agent.*

  - Gebeurtenis-id 9005 biedt betrouwbaarheid van terughalen voor een server-eindpunt. Bijvoorbeeld: Totaal aantal unieke bestanden dat is gebruikt en Totaal aantal unieke bestanden met mislukte toegang.
  - Gebeurtenis-id 9006 biedt foutdistributie voor terughalen voor een server-eindpunt. Bijvoorbeeld: Totaal aantal mislukte aanvragen en ErrorCode. Er wordt één gebeurtenis geregistreerd per foutcode.
  - Gebeurtenis-id 9009 bevat informatie over terugroepsessies voor een server-eindpunt. Bijvoorbeeld: DurationSeconds, CountFilesRecallSucceeded en CountFilesRecallFailed.
  - Gebeurtenis-id 9059 biedt distributie van terugroepen van toepassingen voor een server-eindpunt. Bijvoorbeeld: ShareId, Application Name en TotalEgressNetworkBytes.
  - Gebeurtenis-id 9071 biedt efficiëntie van opslag in cloudlagen voor een server-eindpunt. Bijvoorbeeld: TotalDistinctFileCountCacheHit, TotalDistinctFileCountCacheMiss, TotalCacheHitBytes en TotalCacheMissBytes.

### <a name="performance-counters"></a>Prestatiemeteritems

Gebruik de Azure File Sync prestatiemeters op de server om synchronisatieactiviteiten te bewaken.

Als u Azure File Sync prestatiemeters op de server wilt weergeven, opent u Prestatiemeter (Perfmon.exe). U vindt de tellers onder de **objecten OVERGEDRAGEN AFS-bytes** en **AFS-synchronisatiebewerkingen.**

De volgende prestatiemeters voor Azure File Sync zijn beschikbaar in Prestatiemeter:

| Prestatieobject\Naam teller | Description |
|-|-|
| OVERGEDRAGEN AFS-bytes\Bytes per seconde gedownload | Het aantal bytes dat per seconde is gedownload. |
| OVERGEDRAGEN AFS-bytes\Geüploade bytes per seconde | Het aantal bytes dat per seconde is geüpload. |
| OVERGEDRAGEN AFS-bytes\Totaal aantal bytes per seconde | Totaal aantal bytes per seconde (uploaden en downloaden). |
| AFS-synchronisatiebewerkingen\Gedownloade synchronisatiebestanden per seconde | Het aantal bestanden dat per seconde is gedownload. |
| AFS-synchronisatiebewerkingen\Geüploade synchronisatiebestanden per seconde | Het aantal bestanden dat per seconde is geüpload. |
| AFS-synchronisatiebewerkingen\Totaal aantal synchronisatiebestandsbewerkingen per seconde | Totaal aantal gesynchroniseerde bestanden (uploaden en downloaden). |

## <a name="alert-examples"></a>Waarschuwingsvoorbeelden
Deze sectie bevat enkele voorbeeldwaarschuwingen voor Azure File Sync.

  > [!Note]  
  > Als u een waarschuwing maakt en deze te veel ruis maakt, past u de drempelwaarde en waarschuwingslogica aan.

### <a name="how-to-create-an-alert-if-the-server-endpoint-health-shows-an-error-in-the-portal"></a>Een waarschuwing maken als in de status van het server-eindpunt een fout wordt weergegeven in de portal

1. **Navigeer in Azure Portal** naar de respectieve **opslagsynchronisatieservice**. 
2. Ga naar de **sectie Bewaking** en klik op **Waarschuwingen.** 
3. Klik op **+ Nieuwe waarschuwingsregel om** een nieuwe waarschuwingsregel te maken. 
4. Configureer de voorwaarde door op **Voorwaarde selecteren te klikken.**
5. Klik in de blade **Signaallogica** configureren op **Resultaat van synchronisatiesessie** onder signaalnaam.  
6. Selecteer de volgende dimensieconfiguratie: 
     - Dimensienaam: **Naam van server-eindpunt**  
     - Operator: **=** 
     - Dimensiewaarden: **Alle huidige en toekomstige waarden**  
7. Navigeer **naar Waarschuwingslogica** en voltooi het volgende: 
     - Drempelwaarde ingesteld op **Statisch** 
     - Operator: **Kleiner dan** 
     - Aggregatietype: **Maximum**  
     - Drempelwaarde: **1** 
     - Geëvalueerd op basis van: Aggregatiegranulariteit = **24 uur** | Frequentie van evaluatie = **elk uur** 
     - Klik **op Done.** 
8. Klik **op Actiegroep selecteren** om een actiegroep (e-mail, sms, enzovoort) aan de waarschuwing toe te voegen door een bestaande actiegroep te selecteren of een nieuwe actiegroep te maken.
9. Vul de **waarschuwingsdetails** in, zoals de naam van **de waarschuwingsregel,** **Beschrijving** **en Ernst.**
10. Klik op **Waarschuwingsregel maken**. 

### <a name="how-to-create-an-alert-if-files-are-failing-to-sync-to-a-server-or-cloud-endpoint"></a>Een waarschuwing maken als bestanden niet kunnen worden gesynchroniseerd met een server of cloud-eindpunt

1. **Navigeer in Azure Portal** naar de respectieve **opslagsynchronisatieservice.** 
2. Ga naar de **sectie Bewaking** en klik op **Waarschuwingen.** 
3. Klik op **+ Nieuwe waarschuwingsregel om** een nieuwe waarschuwingsregel te maken. 
4. Configureer de voorwaarde door op **Voorwaarde selecteren te klikken.**
5. Klik in de blade **Signaallogica** configureren **op Bestanden die niet worden gesynchroniseerd** onder signaalnaam.  
6. Selecteer de volgende dimensieconfiguratie: 
     - Dimensienaam: **Naam van server-eindpunt**  
     - Operator: **=** 
     - Dimensiewaarden: **Alle huidige en toekomstige waarden**  
7. Navigeer **naar Waarschuwingslogica** en voltooi het volgende: 
     - Drempelwaarde ingesteld op **Statisch** 
     - Operator: **Groter dan** 
     - Aggregatietype: **Gemiddeld**  
     - Drempelwaarde: **100** 
     - Geëvalueerd op basis van: Aggregatiegranulatie = **5 minuten** | Evaluatiefrequentie = **om de 5 minuten** 
     - Klik **op Done.** 
8. Klik **op Actiegroep selecteren** om een actiegroep (e-mail, sms, enzovoort) aan de waarschuwing toe te voegen door een bestaande actiegroep te selecteren of een nieuwe actiegroep te maken.
9. Vul de details **van de waarschuwing in,** **zoals de naam van de waarschuwingsregel,** **beschrijving** **en ernst.**
10. Klik op **Waarschuwingsregel maken**. 

### <a name="how-to-create-an-alert-if-a-registered-server-is-failing-to-communicate-with-the-storage-sync-service"></a>Een waarschuwing maken als een geregistreerde server niet kan communiceren met de opslagsynchronisatieservice

1. **Navigeer in Azure Portal** naar de respectieve **opslagsynchronisatieservice**. 
2. Ga naar de **sectie Bewaking** en klik op **Waarschuwingen.** 
3. Klik op **+ Nieuwe waarschuwingsregel om** een nieuwe waarschuwingsregel te maken. 
4. Configureer de voorwaarde door op **Voorwaarde selecteren te klikken.**
5. Klik in de blade **Signaallogica** configureren op **Onlinestatus van server** onder signaalnaam.  
6. Selecteer de volgende dimensieconfiguratie: 
     - Dimensienaam: **Servernaam**  
     - Operator: **=** 
     - Dimensiewaarden: **Alle huidige en toekomstige waarden**  
7. Navigeer **naar Waarschuwingslogica** en voltooi het volgende: 
     - Drempelwaarde ingesteld op **Statisch** 
     - Operator: **Kleiner dan** 
     - Aggregatietype: **Maximum**  
     - Drempelwaarde (in bytes): **1** 
     - Geëvalueerd op basis van: Aggregatiegranulatie = **1 uur** | Evaluatiefrequentie = **om de 30 minuten** 
         - Houd er rekening mee dat de metrische gegevens elke Azure Monitor 15 tot 20 minuten worden verzonden. Stel de frequentie **van de evaluatie niet in** op minder dan 30 minuten (genereert valse waarschuwingen).
     - Klik **op Done.** 
8. Klik **op Actiegroep selecteren** om een actiegroep (e-mail, sms, enzovoort) aan de waarschuwing toe te voegen door een bestaande actiegroep te selecteren of een nieuwe actiegroep te maken.
9. Vul de **waarschuwingsdetails** in, zoals de naam van **de waarschuwingsregel,** **Beschrijving** **en Ernst.**
10. Klik op **Waarschuwingsregel maken**. 

### <a name="how-to-create-an-alert-if-the-cloud-tiering-recall-size-has-exceeded-500gib-in-a-day"></a>Een waarschuwing maken als de terugroepgrootte van cloudlagen 500GiB per dag heeft overschreden

1. **Navigeer in Azure Portal** naar de respectieve **opslagsynchronisatieservice**. 
2. Ga naar de **sectie Bewaking** en klik op **Waarschuwingen.** 
3. Klik op **+ Nieuwe waarschuwingsregel om** een nieuwe waarschuwingsregel te maken. 
4. Configureer de voorwaarde door op **Voorwaarde selecteren te klikken.**
5. Klik in de blade **Signaallogica** configureren op Grootte van in cloudopslaglagen **terughalen** onder signaalnaam.  
6. Selecteer de volgende dimensieconfiguratie: 
     - Dimensienaam: **Servernaam**  
     - Operator: **=** 
     - Dimensiewaarden: **Alle huidige en toekomstige waarden**  
7. Navigeer **naar Waarschuwingslogica** en voltooi het volgende: 
     - Drempelwaarde ingesteld op **Statisch** 
     - Operator: **Groter dan** 
     - Aggregatietype: **Totaal**  
     - Drempelwaarde (in bytes): **67108864000** 
     - Geëvalueerd op basis van: Aggregatiegranulatie = **24 uur** | Frequentie van evaluatie = **elk uur** 
     - Klik **op Done.** 
8. Klik **op Actiegroep selecteren** om een actiegroep (e-mail, sms, enzovoort) aan de waarschuwing toe te voegen door een bestaande actiegroep te selecteren of een nieuwe actiegroep te maken.
9. Vul de details **van de waarschuwing in,** **zoals de naam van de waarschuwingsregel,** **beschrijving** **en ernst.**
10. Klik op **Waarschuwingsregel maken**. 

## <a name="next-steps"></a>Volgende stappen
- [Planning voor een Azure Files Sync-implementatie](file-sync-planning.md)
- [Overweeg firewall- en proxyinstellingen](file-sync-firewall-and-proxy.md)
- [Azure File Sync implementeren](file-sync-deployment-guide.md)
- [Problemen oplossen met Azure File Sync](file-sync-troubleshoot.md)