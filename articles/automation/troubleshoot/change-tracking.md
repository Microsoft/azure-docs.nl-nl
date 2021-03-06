---
title: Problemen met de Azure Automation-Wijzigingen bijhouden en-inventaris oplossen
description: In dit artikel leest u hoe u problemen oplost en oplost met de functie voor Wijzigingen bijhouden en inventaris van Azure Automation.
services: automation
ms.subservice: change-inventory-management
ms.date: 02/15/2021
ms.topic: troubleshooting
ms.openlocfilehash: dd027f94edad580836f0afb8c7293c81ca77605a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101723823"
---
# <a name="troubleshoot-change-tracking-and-inventory-issues"></a>Problemen met Wijzigingen bijhouden en inventaris oplossen

In dit artikel wordt beschreven hoe u problemen met Azure Automation Wijzigingen bijhouden en voorraad problemen oplost en oplost. Zie [overzicht van wijzigingen bijhouden en inventaris](../change-tracking/overview.md)voor algemene informatie over wijzigingen bijhouden en inventarisatie.

## <a name="general-errors"></a>Algemene fouten

### <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>Scenario: de computer is al geregistreerd voor een ander account

### <a name="issue"></a>Probleem

Het volgende fout bericht wordt weer gegeven:

```error
Unable to Register Machine for Change Tracking, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Oorzaak

De machine is al geïmplementeerd in een andere werk ruimte voor Wijzigingen bijhouden.

### <a name="resolution"></a>Oplossing

1. Zorg ervoor dat uw computer rapporteert aan de juiste werk ruimte. Zie [agent connectiviteit controleren op Azure monitor](../../azure-monitor/agents/agent-windows.md#verify-agent-connectivity-to-azure-monitor)voor meer informatie over hoe u dit kunt controleren. Zorg er ook voor dat deze werk ruimte is gekoppeld aan uw Azure Automation-account. Als u wilt bevestigen, gaat u naar uw Automation-account en selecteert u **gekoppelde werk ruimte** onder **gerelateerde resources**.

1. Zorg ervoor dat de computers worden weer gegeven in de Log Analytics werkruimte die aan uw Automation-account is gekoppeld. Voer de volgende query uit in de werk ruimte Log Analytics.

   ```kusto
   Heartbeat
   | summarize by Computer, Solutions
   ```

   Als uw computer niet in de query resultaten wordt weer geven, is deze niet recent ingecheckt. Er is waarschijnlijk een probleem met de lokale configuratie. U moet de Log Analytics-agent opnieuw installeren.

   Als uw computer wordt weer gegeven in de query resultaten, controleert u onder de eigenschap Solutions die **change tracking** wordt weer gegeven. Hiermee wordt gecontroleerd of het is geregistreerd bij Wijzigingen bijhouden en inventarisatie. Als dat niet het geval is, controleert u of er problemen zijn met de scope configuratie. De scope configuratie bepaalt welke machines zijn geconfigureerd voor Wijzigingen bijhouden en inventarisatie. Als u de scope configuratie voor de doel computer wilt configureren, raadpleegt u [Wijzigingen bijhouden en inventaris inschakelen vanuit een Automation-account](../change-tracking/enable-from-automation-account.md).

   Voer deze query uit in uw werk ruimte.

   ```kusto
   Operation
   | where OperationCategory == 'Data Collection Status'
   | sort by TimeGenerated desc
   ```

1. Als er een ```Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota``` resultaat wordt weer gegeven, is de in uw werk ruimte gedefinieerde quota bereikt, waardoor er geen gegevens meer kunnen worden opgeslagen. Ga in uw werk ruimte naar **verbruik en geschatte kosten**. Selecteer een nieuwe **prijs categorie** waarmee u meer gegevens kunt gebruiken, of klik op **dagelijks Cap** en verwijder het kapje.

:::image type="content" source="./media/change-tracking/change-tracking-usage.png" alt-text="Gebruik en geschatte kosten." lightbox="./media/change-tracking/change-tracking-usage.png":::

Als uw probleem nog steeds niet is opgelost, volgt u de stappen in [een Windows-Hybrid Runbook worker implementeren](../automation-windows-hrw-install.md) om de Hybrid worker voor Windows opnieuw te installeren. Volg voor Linux de stappen in  [een Linux-Hybrid Runbook worker implementeren](../automation-linux-hrw-install.md).

## <a name="windows"></a>Windows

### <a name="scenario-change-tracking-and-inventory-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>Scenario: Wijzigingen bijhouden-en inventaris records worden niet weer gegeven voor Windows-computers

#### <a name="issue"></a>Probleem

U ziet geen Wijzigingen bijhouden-en inventaris resultaten voor Windows-machines die zijn ingeschakeld voor de functie.

#### <a name="cause"></a>Oorzaak

Deze fout kan de volgende oorzaken hebben:

* De Azure Log Analytics-agent voor Windows wordt niet uitgevoerd.
* De communicatie met het Automation-account wordt geblokkeerd.
* De Management Packs voor Wijzigingen bijhouden en inventarisatie zijn niet gedownload.
* De virtuele machine die wordt ingeschakeld, kan afkomstig zijn van een gekloonde computer die niet is voor bereid met het uitvoeren van een systeem voorbereiding (Sysprep) met de Log Analytics-agent voor Windows geïnstalleerd.

#### <a name="resolution"></a>Oplossing

Ga op de Log Analytics agent machine naar **C:\Program Files\Microsoft monitoring Agent\Agent\Tools** en voer de volgende opdrachten uit:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

Als u nog steeds hulp nodig hebt, kunt u Diagnostische gegevens verzamelen en contact opnemen met ondersteuning.

> [!NOTE]
> De Log Analytics-agent schakelt standaard fout tracering in. Als u uitgebreide fout berichten wilt inschakelen, zoals in het voor gaande voor beeld, gebruikt u de `VER` para meter. Gebruik voor informatie traceringen `INF` Wanneer u aanroept `StartTracing.cmd` .

##### <a name="log-analytics-agent-for-windows-not-running"></a>Log Analytics-agent voor Windows niet actief

Controleer of de Log Analytics agent voor Windows (**HealthService.exe**) wordt uitgevoerd op de computer.

##### <a name="communication-to-automation-account-blocked"></a>Communicatie met Automation-account geblokkeerd

Controleer Logboeken op de computer en zoek naar gebeurtenissen die het woord `changetracking` erin bevatten.

Zie [netwerk planning](../automation-hybrid-runbook-worker.md#network-planning)voor meer informatie over de adressen en poorten die moeten worden toegestaan voor het werken met wijzigingen bijhouden en de inventarisatie.

##### <a name="management-packs-not-downloaded"></a>Management Packs niet gedownload

Controleer of de volgende Wijzigingen bijhouden-en Inventory Management Packs lokaal zijn geïnstalleerd:

* `Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*`
* `Microsoft.IntelligencePacks.InventoryChangeTracking.*`
* `Microsoft.IntelligencePacks.SingletonInventoryCollection.*`

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>VM van een gekloonde computer die niet is Sysprep

Als u een gekloonde installatie kopie gebruikt, Sysprep de installatie kopie eerst en installeert u vervolgens de Log Analytics-agent voor Windows.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-and-inventory-results-on-linux-machines"></a>Scenario: geen Wijzigingen bijhouden-en inventaris resultaten op Linux-machines

#### <a name="issue"></a>Probleem

U ziet geen Wijzigingen bijhouden-en inventaris resultaten voor Linux-machines die zijn ingeschakeld voor de functie. 

#### <a name="cause"></a>Oorzaak
Hier vindt u mogelijke oorzaken die specifiek zijn voor dit probleem:
* De Log Analytics-agent voor Linux wordt niet uitgevoerd.
* De Log Analytics-agent voor Linux is niet juist geconfigureerd.
* Er zijn FIM-conflicten (File Integrity Monitoring).

#### <a name="resolution"></a>Oplossing 

##### <a name="log-analytics-agent-for-linux-not-running"></a>Log Analytics-agent voor Linux niet actief

Controleer of de daemon voor de Log Analytics-agent voor Linux (**omsagent**) wordt uitgevoerd op de computer. Voer de volgende query uit in de Log Analytics-werk ruimte die is gekoppeld aan uw Automation-account.

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

Als uw machine niet in query resultaten wordt weer geven, is deze niet recent ingecheckt. Er is waarschijnlijk een probleem met de lokale configuratie en u moet de agent opnieuw installeren. Zie [logboek gegevens verzamelen met de log Analytics-agent](../../azure-monitor/agents/log-analytics-agent.md)voor meer informatie over de installatie en configuratie.

Als uw computer in de query resultaten wordt weer gegeven, controleert u de scope configuratie. Zie [oplossingen voor doel bewaking in azure monitor](../../azure-monitor/insights/solution-targeting.md).

Zie [probleem: u ziet geen Linux-gegevens](../../azure-monitor/agents/agent-linux-troubleshoot.md#issue-you-are-not-seeing-any-linux-data)voor meer probleem oplossing van dit probleem.

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>Log Analytics-agent voor Linux is niet juist geconfigureerd

De Log Analytics-agent voor Linux is mogelijk niet juist geconfigureerd voor de logboek-en opdracht regel-uitvoer verzameling met behulp van het hulp programma OMS-logboek verzamelaar. Zie [overzicht van wijzigingen bijhouden en inventarisatie](../change-tracking/overview.md).

##### <a name="fim-conflicts"></a>FIM-conflicten

De FIM-functie van Azure Security Center kan de integriteit van uw Linux-bestanden onjuist valideren. Controleer of FIM operationeel is en correct is geconfigureerd voor Linux-bestands bewaking. Zie [overzicht van wijzigingen bijhouden en inventarisatie](../change-tracking/overview.md).

## <a name="next-steps"></a>Volgende stappen

Als uw probleem hier niet wordt weer gegeven of u het probleem niet kunt oplossen, kunt u een van de volgende kanalen proberen voor aanvullende ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure-forums](https://azure.microsoft.com/support/forums/).
* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) , het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring. Azure-ondersteuning verbindt de Azure-community met antwoorden, ondersteuning en experts.
* Een ondersteunings incident voor Azure. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/)en selecteer **ondersteuning verkrijgen**.