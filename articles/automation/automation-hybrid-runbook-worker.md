---
title: Azure Automation Hybrid Runbook Worker overzicht
description: Dit artikel biedt een overzicht van de Hybrid Runbook Worker, die u kunt gebruiken om runbooks uit te voeren op computers in uw lokale datacenter of cloudprovider.
services: automation
ms.subservice: process-automation
ms.date: 01/22/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2bb178302d399805eb84b233060d5717e2dba8b3
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830552"
---
# <a name="hybrid-runbook-worker-overview"></a>Overzicht van Hybrid Runbook Worker

Runbooks in Azure Automation mogelijk geen toegang tot resources in andere clouds of in uw on-premises omgeving omdat ze worden uitgevoerd op het Azure-cloudplatform. U kunt de Hybrid Runbook Worker-functie van Azure Automation gebruiken om runbooks rechtstreeks uit te voeren op de computer die als host voor de rol en voor resources in de omgeving wordt gebruikt om deze lokale resources te beheren. Runbooks worden opgeslagen en beheerd in Azure Automation en vervolgens aan een of meer toegewezen machines geleverd.

## <a name="runbook-worker-types"></a>Runbook Worker-typen

Er zijn twee typen Runbook Workers: systeem en gebruiker. In de volgende tabel wordt het verschil tussen deze twee beschreven.

|Type | Description |
|-----|-------------|
|**Systeem** |Ondersteunt een set verborgen runbooks die worden gebruikt door de Updatebeheer-functie die is ontworpen voor het installeren van door de gebruiker opgegeven updates op Windows- en Linux-computers.<br> Dit type Hybrid Runbook Worker is geen lid van een Hybrid Runbook Worker-groep en daarom worden er geen runbooks uitgevoerd die zijn gericht op een Runbook Worker-groep. |
|**Gebruiker** |Ondersteunt door de gebruiker gedefinieerde runbooks die zijn bedoeld om rechtstreeks te worden uitgevoerd op de Windows- en Linux-computer die lid zijn van een of meer Runbook Worker-groepen. |

Een Hybrid Runbook Worker kan worden uitgevoerd op het Windows- of Linux-besturingssysteem en deze rol is afhankelijk van de [Log Analytics-agent](../azure-monitor/agents/log-analytics-agent.md) die rapporteert aan een Azure Monitor [Log Analytics-werkruimte](../azure-monitor/logs/design-logs-deployment.md). De werkruimte is niet alleen om de machine te controleren op het ondersteunde besturingssysteem, maar ook om de onderdelen te downloaden die nodig zijn om de Hybrid Runbook Worker.

Wanneer Azure Automation [Updatebeheer](./update-management/overview.md) is ingeschakeld, wordt elke machine die is verbonden met uw Log Analytics-werkruimte automatisch geconfigureerd als een Hybrid Runbook Worker. Zie Deploy a Windows Hybrid Runbook Worker (Een [Windows-Hybrid Runbook Worker Hybrid Runbook Worker](automation-windows-hrw-install.md) implementeren) voor Linux en Zie Deploy a Linux Hybrid Runbook Worker (Een Linux-Hybrid Runbook Worker) als u dit wilt configureren als een [windows-Hybrid Runbook Worker.](automation-linux-hrw-install.md)

## <a name="how-does-it-work"></a>Hoe werkt het?

![Overzicht van Hybrid Runbook Worker](media/automation-hybrid-runbook-worker/automation.png)

Elke gebruiker Hybrid Runbook Worker lid is van een Hybrid Runbook Worker die u opgeeft wanneer u de werkmedewerker installeert. Een groep kan één werker bevatten, maar u kunt meerdere werknemers in een groep opnemen voor hoge beschikbaarheid. Elke computer kan één computer hosten Hybrid Runbook Worker aan één Automation-account te rapporteren; u kunt de Hybrid Worker niet registreren voor meerdere Automation-accounts. Een hybrid worker kan alleen luisteren naar taken vanuit één Automation-account. Voor machines die als host voor de Hybrid Runbook Worker van het systeem worden Updatebeheer, kunnen ze worden toegevoegd aan een Hybrid Runbook Worker groep. U moet echter hetzelfde Automation-account gebruiken voor zowel Updatebeheer als het Hybrid Runbook Worker groepslidmaatschap.

Wanneer u een runbook start op een Hybrid Runbook Worker, geeft u de groep op waarop het wordt uitgevoerd. Elke werker in de groep peilt Azure Automation om te zien of er taken beschikbaar zijn. Als er een taak beschikbaar is, neemt de eerste werker die de taak krijgt deze. De verwerkingstijd van de takenwachtrij is afhankelijk van het hardwareprofiel en de belasting van de hybrid worker. U kunt een bepaalde werker niet opgeven. Hybrid Worker werkt op een polling-mechanisme (elke 30 seconden) en volgt een volgorde van 'first come, first-serve'. Afhankelijk van wanneer een taak is gep pusht, wordt de taak door de Hybrid Worker door de Automation-service opgehaald. Eén hybrid worker kan doorgaans vier taken per ping ophalen (dat wil zeggen, elke 30 seconden). Als het aantal pushtaken hoger is dan vier per 30 seconden, bestaat de kans dat een andere hybrid worker in de Hybrid Runbook Worker de taak heeft opgehaald.

Een Hybrid Runbook Worker heeft niet veel van de resourcelimieten van [de Azure-sandbox](automation-runbook-execution.md#runbook-execution-environment) voor schijfruimte, geheugen of netwerksockers. [](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) De limieten voor een hybrid worker zijn alleen gerelateerd aan de eigen resources van de werkmedewerker en worden niet beperkt door de tijdslimiet voor fair [share](automation-runbook-execution.md#fair-share) die Azure-sandboxes hebben.

Als u de distributie van runbooks in Hybrid Runbook Workers wilt bepalen en wanneer of hoe de taken worden geactiveerd, kunt u de Hybrid Worker registreren voor verschillende Hybrid Runbook Worker-groepen binnen uw Automation-account. Richt de taken op de specifieke groep of groepen om uw uitvoeringsindeling te ondersteunen.

## <a name="hybrid-runbook-worker-installation"></a>Hybrid Runbook Worker installeren

Het proces voor het installeren van een Hybrid Runbook Worker is afhankelijk van het besturingssysteem. De volgende tabel definieert de implementatietypen.

|Besturingssysteem  |Implementatietypen  |
|---------|---------|
|Windows     | [Geautomatiseerd](automation-windows-hrw-install.md#automated-deployment)<br>[Handmatig](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Handmatig](automation-linux-hrw-install.md#install-a-linux-hybrid-runbook-worker)        |

De aanbevolen installatiemethode voor een Windows-computer is het gebruik van een Azure Automation runbook om het configuratieproces volledig te automatiseren. Als dat niet haalbaar is, kunt u een stapsgewijs procedure volgen om de rol handmatig te installeren en te configureren. Voor Linux-machines moet u een Python-script uitvoeren om de agent op de computer te installeren.

## <a name="network-planning"></a><a name="network-planning"></a>Netwerkplanning

Controleer [Azure Automation netwerkconfiguratie](automation-network-configuration.md#network-planning-for-hybrid-runbook-worker) voor gedetailleerde informatie over de poorten, URL's en andere netwerkgegevens die vereist zijn voor de Hybrid Runbook Worker.

### <a name="proxy-server-use"></a>Gebruik van proxyserver

Als u een proxyserver gebruikt voor communicatie tussen Azure Automation machines met de Log Analytics-agent, moet u ervoor zorgen dat de juiste resources toegankelijk zijn. De time-out voor aanvragen van de Hybrid Runbook Worker- en Automation-services is 30 seconden. Na drie pogingen mislukt een aanvraag.

### <a name="firewall-use"></a>Gebruik van firewall

Als u een firewall gebruikt om de toegang tot internet te beperken, moet u de firewall configureren om toegang toe te staan. Als u de Log Analytics-gateway als proxy gebruikt, moet u ervoor zorgen dat deze is geconfigureerd voor Hybrid Runbook Workers. Zie [Configure the Log Analytics gateway for Automation Hybrid Runbook Workers (De Log Analytics-gateway configureren voor Automation Hybrid Runbook Workers).](../azure-monitor/agents/gateway.md)

### <a name="service-tags"></a>Servicetags

Azure Automation biedt ondersteuning voor servicetags voor virtuele Azure-netwerken, te beginnen met de servicetag [GuestAndHybridManagement](../virtual-network/service-tags-overview.md). U kunt servicetags gebruiken voor het definiëren van besturingselementen voor netwerktoegang in [netwerkbeveiligingsgroepen](../virtual-network/network-security-groups-overview.md#security-rules) [of Azure Firewall.](../firewall/service-tags.md) Servicetags kunnen worden gebruikt in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de servicetagnaam **GuestAndHybridManagement**  op te geven in het juiste bron- of doelveld van een regel, kunt u het verkeer voor de Automation-service toestaan of weigeren. Deze servicetag biedt geen ondersteuning voor gedetailleerdere controle door IP-adresbereiken te beperken tot een specifieke regio.

De servicetag voor de Azure Automation service biedt alleen DEP's die worden gebruikt voor de volgende scenario's:

* Webhooks activeren vanuit uw virtuele netwerk
* Toestaan dat Hybrid Runbook Workers of State Configuration agents op uw VNet kunnen communiceren met de Automation-service

>[!NOTE]
>De servicetag **GuestAndHybridManagement** biedt momenteel geen ondersteuning voor het uitvoeren van runbook-taak in een Azure-sandbox, alleen rechtstreeks op een Hybrid Runbook Worker.

## <a name="support-for-impact-level-5-il5"></a>Ondersteuning voor Impact Level 5 (IL5)

Azure Automation Hybrid Runbook Worker kunnen worden gebruikt in Azure Government ondersteuning voor Impact Level 5-workloads in een van de volgende twee configuraties:

* [Geïsoleerde virtuele machine](../azure-government/documentation-government-impact-level-5.md#isolated-virtual-machines). Wanneer ze worden geïmplementeerd, gebruiken ze de volledige fysieke host voor die machine, waardoor het benodigde isolatieniveau wordt geboden dat is vereist voor de ondersteuning van IL5-workloads.

* [Azure Dedicated Hosts,](../azure-government/documentation-government-impact-level-5.md#azure-dedicated-host)dat fysieke servers biedt die een of meer virtuele machines kunnen hosten, toegewezen aan één Azure-abonnement.

>[!NOTE]
>Rekenisolatie via de Hybrid Runbook Worker-rol is beschikbaar voor commerciële clouds van Azure en clouds van de Amerikaanse overheid.

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>Updatebeheer adressen voor Hybrid Runbook Worker

Naast de standaardadressen en poorten die vereist zijn voor de Hybrid Runbook Worker, heeft Updatebeheer andere netwerkconfiguratievereisten die worden beschreven in de [sectie Netwerkplanning.](./update-management/overview.md#ports)

## <a name="azure-automation-state-configuration-on-a-hybrid-runbook-worker"></a>Azure Automation State Configuration op een Hybrid Runbook Worker

U kunt de [Azure Automation State Configuration](automation-dsc-overview.md) uitvoeren op een Hybrid Runbook Worker. Voor het beheren van de configuratie van servers die de Hybrid Runbook Worker, moet u de servers toevoegen als DSC-knooppunten. Zie [Machines inschakelen voor beheer door Azure Automation State Configuration.](automation-dsc-onboarding.md)

## <a name="runbook-worker-limits"></a>Limieten voor Runbook Worker

Het maximum aantal Hybrid Worker per Automation-account is 4000 en is van toepassing op hybrid workers & systeemgebruikers. Als u meer dan 4000 machines moet beheren, raden we u aan nog een Automation-account te maken.

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>Runbooks op een Hybrid Runbook Worker

Mogelijk hebt u runbooks die resources op de lokale computer beheren of uitvoeren op resources in de lokale omgeving waar een Hybrid Runbook Worker wordt geïmplementeerd. In dit geval kunt u ervoor kiezen om uw runbooks uit te voeren op de Hybrid Worker in plaats van in een Automation-account. Runbooks die worden uitgevoerd op Hybrid Runbook Worker zijn qua structuur identiek aan de runbooks die u in het Automation-account hebt uitgevoerd. Zie [Runbooks uitvoeren op een Hybrid Runbook Worker.](automation-hrw-run-runbooks.md)

### <a name="hybrid-runbook-worker-jobs"></a>Hybrid Runbook Worker taken

Hybrid Runbook Worker taken worden uitgevoerd onder het lokale **systeemaccount** in Windows of [het nxautomation-account](automation-runbook-execution.md#log-analytics-agent-for-linux) in Linux. Azure Automation verwerkt taken in Hybrid Runbook Workers anders dan taken die worden uitgevoerd in Azure-sandboxes. Zie [Runbook execution environment (Runbookuitvoeringsomgeving).](automation-runbook-execution.md#runbook-execution-environment)

Als de Hybrid Runbook Worker-hostmachine opnieuw wordt opgestart, wordt elke runbooktaak opnieuw gestart vanaf het begin of vanaf het laatste controlepunt voor PowerShell Workflow-runbooks. Nadat een runbook-taak meer dan drie keer opnieuw is gestart, wordt deze tijdelijk opgeschort.

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Runbookmachtigingen voor een Hybrid Runbook Worker

Omdat ze toegang hebben tot niet-Azure-resources, kunnen runbooks die worden uitgevoerd op een Hybrid Runbook Worker niet gebruikmaken van het verificatiemechanisme dat doorgaans wordt gebruikt door runbooks die worden uitgevoerd bij Azure-resources. Een runbook biedt een eigen verificatie voor lokale resources of configureert verificatie met behulp van [beheerde identiteiten voor Azure-resources.](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager) U kunt ook een Uitvoeren als-account opgeven om een gebruikerscontext te bieden voor alle runbooks.

## <a name="view-system-hybrid-runbook-workers"></a>Hybrid Runbook Workers van het systeem weergeven

Nadat de Updatebeheer functie is ingeschakeld op Windows- of Linux-computers, kunt u de lijst met hybrid runbook workers van het systeem in de Azure Portal. U kunt maximaal 2000 werknemers weergeven in de portal door in het linkerdeelvenster voor het geselecteerde Automation-account het tabblad Systeemgroep hybrid workers te selecteren in de optie **Hybrid** **Workers-groep.**

:::image type="content" source="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png" alt-text="Pagina Hybride werkgroep voor Automation-accountsysteem" border="false" lightbox="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png":::

Als u meer dan 2000 hybrid workers hebt, kunt u het volgende PowerShell-script uitvoeren om een lijst met al deze werksters op te halen:

```powershell
"Get-AzSubscription -SubscriptionName "<subscriptionName>" | Set-AzContext
$workersList = (Get-AzAutomationHybridWorkerGroup -ResourceGroupName "<resourceGroupName>" -AutomationAccountName "<automationAccountName>").Runbookworker
$workersList | export-csv -Path "<Path>\output.csv" -NoClobber -NoTypeInformation"
```

## <a name="next-steps"></a>Volgende stappen

* Zie Runbooks uitvoeren op een Hybrid Runbook Worker voor meer informatie over het configureren van uw runbooks voor het automatiseren van processen in uw on-premises datacenter of andere [cloudomgeving.](automation-hrw-run-runbooks.md)

* Zie Problemen met Hybrid Runbook Workers oplossen voor meer informatie over het [oplossen Hybrid Runbook Worker problemen.](troubleshoot/hybrid-runbook-worker.md#general)
