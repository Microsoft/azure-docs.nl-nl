---
title: Overzicht van Azure Automation Hybrid Runbook Worker
description: Dit artikel bevat een overzicht van de Hybrid Runbook Worker, die u kunt gebruiken om runbooks uit te voeren op computers in uw lokale Data Center of Cloud provider.
services: automation
ms.subservice: process-automation
ms.date: 01/22/2021
ms.topic: conceptual
ms.openlocfilehash: c95ccb5ea1a23e8173d58bd3a18490e9b8e630e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100581260"
---
# <a name="hybrid-runbook-worker-overview"></a>Overzicht van Hybrid Runbook Worker

Runbooks in Azure Automation hebben mogelijk geen toegang tot resources in andere Clouds of in uw on-premises omgeving omdat ze worden uitgevoerd op het Azure-Cloud platform. U kunt de functie Hybrid Runbook Worker van Azure Automation gebruiken om runbooks rechtstreeks uit te voeren op de computer waarop de rol wordt gehost en aan resources in de omgeving om deze lokale resources te beheren. Runbooks worden opgeslagen en beheerd in Azure Automation en vervolgens aan een of meer toegewezen computers geleverd.

## <a name="runbook-worker-types"></a>Typen Runbook worker

Er zijn twee soorten Runbook-werk nemers: systeem en gebruiker. In de volgende tabel wordt het verschil tussen de tabellen beschreven.

|Type | Description |
|-----|-------------|
|**Systeem** |Ondersteunt een set verborgen runbooks die worden gebruikt door de Updatebeheer-functie die is ontworpen om door de gebruiker opgegeven updates te installeren op Windows-en Linux-computers.<br> Dit type Hybrid Runbook Worker is geen lid van een Hybrid Runbook Worker groep en voert daarom geen runbooks uit die zijn gericht op een Runbook worker-groep. |
|**Gebruiker** |Ondersteunt door de gebruiker gedefinieerde runbooks die rechtstreeks worden uitgevoerd op de Windows-en Linux-computer die lid zijn van een of meer Runbook worker-groepen. |

Een Hybrid Runbook Worker kan worden uitgevoerd op het Windows-of Linux-besturings systeem en deze rol is afhankelijk van de [log Analytics agent](../azure-monitor/agents/log-analytics-agent.md) rapportage aan een Azure monitor [log Analytics-werk ruimte](../azure-monitor/logs/design-logs-deployment.md). De werk ruimte is niet alleen om de computer te bewaken voor het ondersteunde besturings systeem, maar ook om de onderdelen te downloaden die nodig zijn om de Hybrid Runbook Worker te installeren.

Als Azure Automation [updatebeheer](./update-management/overview.md) is ingeschakeld, wordt een computer die is verbonden met uw log Analytics-werk ruimte automatisch geconfigureerd als een systeem Hybrid Runbook Worker. [Hybrid Runbook worker](automation-windows-hrw-install.md) Zie een [Linux-Hybrid Runbook worker implementeren](automation-linux-hrw-install.md)voor meer informatie over het configureren van een Windows-Hybrid Runbook worker als gebruiker.

## <a name="how-does-it-work"></a>Hoe werkt het?

![Overzicht van Hybrid Runbook Worker](media/automation-hybrid-runbook-worker/automation.png)

Elke gebruiker Hybrid Runbook Worker is lid van een Hybrid Runbook Worker groep die u opgeeft wanneer u de werk nemer installeert. Een groep kan één werk nemer bevatten, maar u kunt meerdere werk nemers in een groep toevoegen voor maximale Beschik baarheid. Elke machine kan één Hybrid Runbook Worker melden aan één Automation-account. u kunt de Hybrid worker niet registreren in meerdere Automation-accounts. Een Hybrid Worker kan alleen Luis teren naar taken vanuit één Automation-account. Voor computers die als host fungeren voor het systeem Hybrid Runbook worker die wordt beheerd door Updatebeheer, kunnen ze worden toegevoegd aan een Hybrid Runbook Worker groep. Maar u moet hetzelfde Automation-account gebruiken voor zowel Updatebeheer als het lidmaatschap van de Hybrid Runbook Worker groep.

Wanneer u een runbook start voor een gebruiker Hybrid Runbook Worker, geeft u de groep op waarop deze wordt uitgevoerd. Elke werk nemer in de groep pollt Azure Automation om te zien of er taken beschikbaar zijn. Als een taak beschikbaar is, haalt de eerste werk nemer de taak op. De verwerkings tijd van de taken wachtrij is afhankelijk van het hardwareprofiel en de belasting van de Hybrid Worker. U kunt een bepaalde werk nemer niet opgeven. Hybrid worker werkt op een Polling-mechanisme (elke 30 seconden) en volgt een bestelling van de eerste aangeleverde. Afhankelijk van wanneer een taak is gepusht, waarbij de Hybrid worker pingt, wordt de taak door de Automation-Service uitgesteld. Eén Hybrid Worker kan in het algemeen vier taken per ping ophalen (dat wil zeggen elke 30 seconden). Als uw frequentie van het pushen van taken hoger is dan vier per 30 seconden, is er een hoge mogelijkheid om een andere Hybrid worker in de Hybrid Runbook Worker de taak op te halen.

Een Hybrid Runbook Worker heeft niet veel van de resource [limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) voor [Azure sandbox](automation-runbook-execution.md#runbook-execution-environment) op schijf ruimte, geheugen of netwerk sockets. De limieten voor een Hybrid worker zijn alleen gerelateerd aan de eigen resources van de werk nemer, en ze zijn niet beperkt door de [maximale tijd limiet](automation-runbook-execution.md#fair-share) die Azure sandboxes hebben.

Als u de distributie van runbooks op Hybrid Runbook Workers wilt beheren en wanneer of hoe de taken worden geactiveerd, kunt u de Hybrid worker registreren voor verschillende Hybrid Runbook Worker groepen binnen uw Automation-account. Richt de taken voor de specifieke groep of groepen in om de uitvoerings volgorde te ondersteunen.

## <a name="hybrid-runbook-worker-installation"></a>Installatie van Hybrid Runbook Worker

Het proces voor het installeren van een gebruiker Hybrid Runbook Worker is afhankelijk van het besturings systeem. In de volgende tabel worden de implementatie typen gedefinieerd.

|Besturingssysteem  |Implementatie typen  |
|---------|---------|
|Windows     | [Geautomatiseerd](automation-windows-hrw-install.md#automated-deployment)<br>[Handmatig](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Handmatig](automation-linux-hrw-install.md#install-a-linux-hybrid-runbook-worker)        |

De aanbevolen installatie methode voor een Windows-machine is het gebruik van een Azure Automation runbook om het proces van het configureren volledig te automatiseren. Als dat niet het geval is, kunt u een stapsgewijze procedure volgen om de rol hand matig te installeren en te configureren. Voor Linux-machines voert u een python-script uit om de agent op de computer te installeren.

## <a name="network-planning"></a><a name="network-planning"></a>Netwerkplanning

Controleer [Azure Automation netwerk configuratie](automation-network-configuration.md#network-planning-for-hybrid-runbook-worker) voor gedetailleerde informatie over de poorten, url's en andere netwerk gegevens die nodig zijn voor de Hybrid Runbook Worker.

### <a name="proxy-server-use"></a>Gebruik van proxy server

Als u een proxy server gebruikt voor communicatie tussen Azure Automation en computers waarop de Log Analytics agent wordt uitgevoerd, moet u ervoor zorgen dat de juiste resources toegankelijk zijn. De time-out voor aanvragen van de Hybrid Runbook Worker-en Automation-Services is 30 seconden. Na drie pogingen mislukt een aanvraag.

### <a name="firewall-use"></a>Gebruik van Firewall

Als u een firewall gebruikt om de toegang tot internet te beperken, moet u de firewall configureren om toegang toe te staan. Als u de Log Analytics-gateway als een proxy gebruikt, moet u ervoor zorgen dat deze is geconfigureerd voor Hybrid Runbook Workers. Zie [Configure the log Analytics Gateway for Automation Hybrid Runbook Workers](../azure-monitor/agents/gateway.md)(Engelstalig).

### <a name="service-tags"></a>Servicetags

Azure Automation ondersteunt de service tags voor het virtuele Azure-netwerk, te beginnen met de service label [GuestAndHybridManagement](../virtual-network/service-tags-overview.md). U kunt service tags gebruiken voor het definiëren van netwerk toegangs beheer voor [netwerk beveiligings groepen](../virtual-network/network-security-groups-overview.md#security-rules) of [Azure firewall](../firewall/service-tags.md). Service tags kunnen worden gebruikt in plaats van specifieke IP-adressen wanneer u beveiligings regels maakt. Door het opgeven van de servicetag naam **GuestAndHybridManagement**  in het juiste bron-of doel veld van een regel, kunt u het verkeer voor de Automation-Service toestaan of weigeren. Deze servicetag biedt geen ondersteuning voor het toestaan van nauw keurigere controle door IP-adresbereiken te beperken tot een bepaalde regio.

Het servicetag voor de Azure Automation-Service biedt alleen IP-adressen die worden gebruikt voor de volgende scenario's:

* Webhooks activeren vanuit uw virtuele netwerk
* Hybrid Runbook Workers of status configuratie agenten op uw VNet toestaan om te communiceren met de Automation-Service

>[!NOTE]
>De **GuestAndHybridManagement** van de service tags biedt momenteel geen ondersteuning voor het uitvoeren van een runbook-taak in een Azure-sandbox, alleen rechtstreeks op een Hybrid Runbook Worker.

## <a name="support-for-impact-level-5-il5"></a>Ondersteuning voor impact niveau 5 (IL5)

Azure Automation Hybrid Runbook Worker kan worden gebruikt in Azure Government voor het ondersteunen van impact niveau 5-workloads in een van de volgende twee configuraties:

* [Geïsoleerde virtuele machine](../azure-government/documentation-government-impact-level-5.md#isolated-virtual-machines). Wanneer ze worden geïmplementeerd, gebruiken ze de gehele fysieke host voor die computer, die het vereiste isolatie niveau biedt om IL5-workloads te ondersteunen.

* [Exclusieve Azure-hosts](../azure-government/documentation-government-impact-level-5.md#azure-dedicated-host), die fysieke servers bieden die kunnen fungeren als host voor een of meer virtuele machines, toegewezen aan één Azure-abonnement.

>[!NOTE]
>Reken isolatie via de Hybrid Runbook Worker rol is beschikbaar voor Clouds van Azure en Amerikaanse overheids instanties.

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>Updatebeheer adressen voor Hybrid Runbook Worker

Naast de standaard adressen en poorten die vereist zijn voor de Hybrid Runbook Worker, heeft Updatebeheer andere vereisten voor netwerk configuratie beschreven onder het gedeelte [netwerk planning](./update-management/overview.md#ports) .

## <a name="azure-automation-state-configuration-on-a-hybrid-runbook-worker"></a>Azure Automation status configuratie op een Hybrid Runbook Worker

U kunt [Azure Automation status configuratie](automation-dsc-overview.md) uitvoeren op een Hybrid Runbook Worker. Als u de configuratie wilt beheren van servers die ondersteuning bieden voor de Hybrid Runbook Worker, moet u de servers als DSC-knoop punten toevoegen. Zie [machines inschakelen voor beheer door Azure Automation status configuratie](automation-dsc-onboarding.md).

## <a name="runbook-worker-limits"></a>Limieten voor Runbook worker

Het maximum aantal Hybrid Worker groepen per Automation-account is 4000 en is van toepassing op gebruikers Hybrid Workers van zowel het systeem & gebruiker. Als u meer dan 4.000 computers wilt beheren, raden we u aan een ander Automation-account te maken.

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>Runbooks op een Hybrid Runbook Worker

Mogelijk beschikt u over runbooks die resources op de lokale computer beheren of worden uitgevoerd op resources in de lokale omgeving waar een gebruiker Hybrid Runbook Worker wordt geïmplementeerd. In dit geval kunt u ervoor kiezen om uw runbooks uit te voeren op de Hybrid worker in plaats van een Automation-account. Runbooks die worden uitgevoerd op een Hybrid Runbook Worker, zijn identiek in de structuur van de mappen die u uitvoert in het Automation-account. Zie [Runbooks uitvoeren op een Hybrid Runbook worker](automation-hrw-run-runbooks.md).

### <a name="hybrid-runbook-worker-jobs"></a>Hybrid Runbook Worker taken

Hybrid Runbook Worker taken worden uitgevoerd onder het lokale **systeem** account op Windows of het [nxautomation-account](automation-runbook-execution.md#log-analytics-agent-for-linux) in Linux. Azure Automation verwerkt taken op Hybrid Runbook Workers anders dan taken die worden uitgevoerd in azure-sandboxes. Zie [Runbook Execution Environment](automation-runbook-execution.md#runbook-execution-environment).

Als de Hybrid Runbook Worker-hostcomputer opnieuw wordt opgestart, wordt elke actieve Runbook-taak opnieuw gestart vanaf het begin of van het laatste controle punt voor Power shell-werk stroom runbooks. Nadat een runbook-taak meer dan drie keer opnieuw is opgestart, wordt deze onderbroken.

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Runbook-machtigingen voor een Hybrid Runbook Worker

Omdat ze toegang hebben tot niet-Azure-resources, kunnen runbooks die worden uitgevoerd op een gebruiker Hybrid Runbook Worker niet gebruikmaken van het verificatie mechanisme dat doorgaans wordt gebruikt door runbooks die verifiëren naar Azure-resources. Een runbook biedt een eigen verificatie voor lokale bronnen of configureert verificatie met behulp [van beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager). U kunt ook een uitvoeren als-account opgeven om een gebruikers context te bieden voor alle runbooks.

## <a name="view-system-hybrid-runbook-workers"></a>Hybrid Runbook Workers van het systeem weer geven

Nadat de Updatebeheer-functie is ingeschakeld op Windows-of Linux-computers, kunt u de lijst met Hybrid Runbook worker-groepen in de Azure Portal inventariseren. U kunt Maxi maal 2.000 werk nemers in de portal weer geven door in het linkerdeel venster van het geselecteerde Automation-account de **groep hybride werk nemers** van het tabblad systeem te selecteren in de **groep Hybrid worker** -werk rollen.

:::image type="content" source="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png" alt-text="Pagina hybride werk groepen voor Automation-account systeem" border="false" lightbox="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png":::

Als u meer dan 2.000 Hybrid Workers hebt, kunt u het volgende Power shell-script uitvoeren om een lijst met al deze werk nemers te verkrijgen:

```powershell
"Get-AzSubscription -SubscriptionName "<subscriptionName>" | Set-AzContext
$workersList = (Get-AzAutomationHybridWorkerGroup -ResourceGroupName "<resourceGroupName>" -AutomationAccountName "<automationAccountName>").Runbookworker
$workersList | export-csv -Path "<Path>\output.csv" -NoClobber -NoTypeInformation"
```

## <a name="next-steps"></a>Volgende stappen

* Zie [Runbooks uitvoeren op een Hybrid Runbook worker](automation-hrw-run-runbooks.md)voor meer informatie over het configureren van uw runbooks om processen te automatiseren in uw on-premises Data Center of andere cloud omgeving.

* Zie [problemen met Hybrid Runbook worker oplossen](troubleshoot/hybrid-runbook-worker.md#general)voor meer informatie over het oplossen van problemen met uw Hybrid Runbook Workers.
