---
title: Problemen met de implementatie van Azure Automation-onderdelen oplossen
description: In dit artikel leest u hoe u problemen kunt oplossen en oplossen die zich voordoen bij het implementeren van Azure Automation-functies.
services: automation
ms.date: 02/11/2021
ms.topic: troubleshooting
ms.openlocfilehash: 43bc367be9ad9bb32f33f94df774acb3e808182a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100651116"
---
# <a name="troubleshoot-feature-deployment-issues"></a>Problemen met implementatie van functies oplossen

Er kunnen fout berichten worden weer gegeven wanneer u de functie Azure Automation Updatebeheer of de functie Wijzigingen bijhouden en inventarisatie op uw Vm's implementeert. In dit artikel worden de fouten beschreven die zich kunnen voordoen en hoe u deze kunt oplossen.

## <a name="known-issues"></a>Bekende problemen

### <a name="scenario-renaming-a-registered-node-requires-unregister-or-register-again"></a><a name="node-rename"></a>Scenario: voor het wijzigen van de naam van een geregistreerd knoop punt moet u registratie ongedaan maken of registreren.

#### <a name="issue"></a>Probleem

Een knoop punt is geregistreerd voor Azure Automation en vervolgens wordt de computer naam van het besturings systeem gewijzigd. Rapporten van het knoop punt worden nog steeds weer gegeven met de oorspronkelijke naam.

#### <a name="cause"></a>Oorzaak

Als u de naam van geregistreerde knoop punten wijzigt, wordt de knooppunt naam in Azure Automation niet bijgewerkt.

#### <a name="resolution"></a>Oplossing

Hef de registratie van het knoop punt uit de configuratie van Azure Automation status op en registreer het vervolgens opnieuw. Rapporten die worden gepubliceerd naar de service vóór dat moment, zijn niet meer beschikbaar.

### <a name="scenario-re-signing-certificates-via-https-proxy-isnt-supported"></a><a name="resigning-cert"></a>Scenario: het opnieuw ondertekenen van certificaten via een HTTPS-proxy wordt niet ondersteund

#### <a name="issue"></a>Probleem

Wanneer u verbinding maakt via een proxy die HTTPS-verkeer beëindigt en vervolgens het verkeer opnieuw versleutelt met een nieuw certificaat, wordt de verbinding niet toegestaan door de service.

#### <a name="cause"></a>Oorzaak

Azure Automation biedt geen ondersteuning voor het opnieuw ondertekenen van certificaten die worden gebruikt voor het versleutelen van verkeer.

#### <a name="resolution"></a>Oplossing

Er is momenteel geen oplossing voor dit probleem.

## <a name="general-errors"></a>Algemene fouten

### <a name="scenario-feature-deployment-fails-with-the-message-the-solution-cannot-be-enabled"></a><a name="missing-write-permissions"></a>Scenario: de implementatie van de functie mislukt met het bericht ' de oplossing kan niet worden ingeschakeld '

#### <a name="issue"></a>Probleem

U ontvangt een van de volgende berichten wanneer u een functie op een virtuele machine probeert in te scha kelen:

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>Oorzaak

Deze fout wordt veroorzaakt door onjuiste of ontbrekende machtigingen voor de virtuele machine of de werk ruimte of voor de gebruiker.

#### <a name="resolution"></a>Oplossing

Zorg ervoor dat u de juiste machtigingen hebt voor de [implementatie van functies](../automation-role-based-access-control.md#feature-setup-permissions)en probeer het onderdeel opnieuw te implementeren. Als het fout bericht wordt weer gegeven `The solution cannot be enabled on this VM because the permission to read the workspace is missing` , raadpleegt u de volgende [informatie over het oplossen van problemen](update-management.md#failed-to-enable-error).

### <a name="scenario-feature-deployment-fails-with-the-message-failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>Scenario: de implementatie van de functie mislukt met het bericht ' kan Automation-account niet configureren voor diagnostische logboek registratie '

#### <a name="issue"></a>Probleem

Het volgende bericht wordt weer gegeven wanneer u probeert een functie in te scha kelen op een virtuele machine:

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>Oorzaak

Deze fout kan optreden als de prijs categorie niet overeenkomt met het facturerings model van het abonnement. Zie [verbruik en geschatte kosten in azure monitor bewaken](../../azure-monitor//usage-estimated-costs.md)voor meer informatie.

#### <a name="resolution"></a>Oplossing

Maak uw Log Analytics-werk ruimte hand matig en herhaal het implementatie proces van de functie om de werk ruimte te selecteren die u hebt gemaakt.

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>Scenario: ComputerGroupQueryFormatError

#### <a name="issue"></a>Probleem

Deze fout code geeft aan dat de query opgeslagen Zoek computer groep die wordt gebruikt om de functie te richten, niet correct is ingedeeld. 

#### <a name="cause"></a>Oorzaak

Mogelijk hebt u de query gewijzigd, of is het systeem mogelijk gewijzigd.

#### <a name="resolution"></a>Oplossing

U kunt de query voor de functie verwijderen en vervolgens de functie opnieuw inschakelen, waardoor de query opnieuw wordt gemaakt. U vindt de query in uw werk ruimte onder **opgeslagen Zoek opdrachten**. De naam van de query is **MicrosoftDefaultComputerGroup** en de categorie van de query is de naam van de bijbehorende functie. Als er meerdere functies zijn ingeschakeld, wordt in de **MicrosoftDefaultComputerGroup** -query meerdere keren weer gegeven onder **opgeslagen Zoek opdrachten**.

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>Scenario: PolicyViolation

#### <a name="issue"></a>Probleem

Deze fout code geeft aan dat de implementatie is mislukt vanwege een schending van een of meer beleids regels.

#### <a name="cause"></a>Oorzaak 

Een beleid blokkeert de bewerking niet volt ooien.

#### <a name="resolution"></a>Oplossing

Als u de functie wilt implementeren, moet u overwegen om het aangegeven beleid te wijzigen. Omdat er veel verschillende soorten beleids regels kunnen worden gedefinieerd, zijn de vereiste wijzigingen afhankelijk van het beleid dat wordt geschonden. Als er bijvoorbeeld een beleid is gedefinieerd voor een resource groep die toestemming weigert om de inhoud van een aantal resources te wijzigen, kunt u een van de volgende oplossingen kiezen:

* Verwijder het beleid samen.
* Probeer de functie in te scha kelen voor een andere resource groep.
* Het beleid opnieuw instellen voor een specifieke resource, bijvoorbeeld een Automation-account.
* Wijzig de set resources die het beleid heeft geconfigureerd om te weigeren.

Controleer de meldingen in de rechter bovenhoek van de Azure Portal of ga naar de resource groep die uw Automation-account bevat en selecteer **implementaties** onder **instellingen** om de mislukte implementatie te bekijken. Zie [overzicht van Azure Policy](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json)voor meer informatie over Azure Policy.

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>Scenario: fouten bij het ontkoppelen van een werk ruimte

#### <a name="issue"></a>Probleem

Het volgende fout bericht wordt weer gegeven wanneer u een werk ruimte probeert te ontkoppelen:

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>Oorzaak

Deze fout treedt op wanneer er nog functies actief zijn in uw Log Analytics-werk ruimte, afhankelijk van uw Automation-account en Log Analytics werk ruimte die wordt gekoppeld.

### <a name="resolution"></a>Oplossing

Verwijder de resources voor de volgende functies uit uw werk ruimte als u ze gebruikt:

* Updatebeheer
* Wijzigingen bijhouden en Inventaris
* VM's starten/stoppen buiten kantooruren

Nadat u de functie resources hebt verwijderd, kunt u uw werk ruimte ontkoppelen. Het is belang rijk om bestaande artefacten uit deze functies op te schonen vanuit uw werk ruimte en uw Automation-account:

* Verwijder voor Updatebeheer **Update-implementaties (schema's)** uit uw Automation-account.
* Verwijder voor VM's buiten bedrijfsuren starten/stoppen alle vergren delingen voor onderdeel onderdelen in uw Automation-account onder **instellingen**  >  **vergrendelingen**. Zie [de functie verwijderen](../automation-solution-vm-management-remove.md)voor meer informatie.

## <a name="log-analytics-for-windows-extension-failures"></a><a name="mma-extension-failures"></a>Log Analytics voor Windows-extensie fouten

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Een installatie van de Log Analytics-agent voor Windows-extensies kan om verschillende redenen mislukken. In de volgende sectie worden problemen met de implementatie van functies beschreven die kunnen leiden tot fouten tijdens de implementatie van de uitbrei ding van de Log Analytics-agent voor Windows.

>[!NOTE]
>Log Analytics-agent voor Windows is de naam die momenteel wordt gebruikt in Azure Automation voor micro soft Monitoring Agent (MMA).

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>Scenario: er is een uitzonde ring opgetreden tijdens een webclient-aanvraag

De Log Analytics voor Windows-extensie op de VM kan niet communiceren met externe resources en de implementatie mislukt.

#### <a name="issue"></a>Probleem

Hier volgen enkele voor beelden van fout berichten die worden geretourneerd:

```error
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```error
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>Oorzaak

Enkele mogelijke oorzaken van deze fout zijn:

* Een proxy die in de virtuele machine is geconfigureerd, staat alleen specifieke poorten toe.
* Een firewall instelling heeft de toegang tot de vereiste poorten en adressen geblokkeerd.

#### <a name="resolution"></a>Oplossing

Zorg ervoor dat u de juiste poorten en adressen voor communicatie hebt geopend. Zie [uw netwerk plannen](../automation-hybrid-runbook-worker.md#network-planning)voor een lijst met poorten en adressen.

### <a name="scenario-install-failed-because-of-transient-environment-issues"></a><a name="transient-environment-issue"></a>Scenario: de installatie is mislukt vanwege problemen met de tijdelijke omgeving

De installatie van de Log Analytics voor Windows-uitbrei ding is mislukt tijdens de implementatie vanwege een andere installatie of actie die de installatie blokkeert.

#### <a name="issue"></a>Probleem

Hier volgen enkele voor beelden van fout berichten die kunnen worden geretourneerd:

```error
The Microsoft Monitoring Agent failed to install on this machine. Please try to uninstall and reinstall the extension. If the issue persists, please contact support.
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1618'
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.2) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.2\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1601'
```

#### <a name="cause"></a>Oorzaak

Enkele mogelijke oorzaken van deze fout zijn:

* Er wordt een andere installatie uitgevoerd.
* Het systeem wordt geactiveerd om opnieuw op te starten tijdens het implementeren van de sjabloon.

#### <a name="resolution"></a>Oplossing

Deze fout is van tijdelijke aard. Voer de implementatie opnieuw uit om de extensie te installeren.

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a>Scenario: time-out voor installatie

De installatie van de Log Analytics voor Windows-extensie is niet voltooid vanwege een time-out.

#### <a name="issue"></a>Probleem

Hier volgt een voor beeld van een fout bericht dat kan worden geretourneerd:

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Oorzaak

Dit type fout treedt op omdat de VM tijdens de installatie intensief wordt belast.

### <a name="resolution"></a>Oplossing

Installeer de Log Analytics agent voor Windows-extensie wanneer de virtuele machine minder wordt geladen.

## <a name="next-steps"></a>Volgende stappen

Als uw probleem hier niet wordt weer gegeven of u het probleem niet kunt oplossen, kunt u een van de volgende kanalen proberen voor aanvullende ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure-forums](https://azure.microsoft.com/support/forums/).
* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) , het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring. Azure-ondersteuning verbindt de Azure-community met antwoorden, ondersteuning en experts.
* Een ondersteunings incident voor Azure. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/)en selecteer **ondersteuning verkrijgen**.