---
title: Details van de netwerk configuratie Azure Automation
description: Dit artikel bevat informatie over de netwerk gegevens die vereist zijn voor Azure Automation status configuratie, Azure Automation Hybrid Runbook Worker, Updatebeheer en Wijzigingen bijhouden en inventaris
ms.author: magoedte
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: 0add7eed6abbe6c137d423ee4a7ef5f0f60072e3
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98900209"
---
# <a name="azure-automation-network-configuration-details"></a>Details van de netwerk configuratie Azure Automation

Deze pagina bevat netwerk gegevens die nodig zijn voor de [configuratie van Hybrid Runbook Worker en status](#hybrid-runbook-worker-and-state-configuration), en voor [updatebeheer en wijzigingen bijhouden en inventaris](#update-management-and-change-tracking-and-inventory).

## <a name="hybrid-runbook-worker-and-state-configuration"></a>Configuratie van Hybrid Runbook Worker en status

De volgende poort en Url's zijn vereist voor de Hybrid Runbook Worker en voor de [configuratie van de automatiserings status](automation-dsc-overview.md) om te communiceren met Azure Automation.

* Poort: alleen 443 vereist voor uitgaande internet toegang
* Globale URL: `*.azure-automation.net`
* Globale URL van US Gov-Virginia: `*.azure-automation.us`
* Agent service: `https://<workspaceId>.agentsvc.azure-automation.net`

### <a name="network-planning-for-hybrid-runbook-worker"></a>Hybrid Runbook Worker van het netwerk plannen

Voor een systeem-of gebruikers Hybrid Runbook Worker verbinding maken met en registreren bij Azure Automation, moet deze toegang hebben tot het poort nummer en de Url's die in deze sectie worden beschreven. De werk nemer moet ook toegang hebben tot de [poorten en url's die vereist zijn voor de log Analytics-agent](../azure-monitor/platform/agent-windows.md) om verbinding te maken met de Azure monitor log Analytics-werk ruimte.

Als u een Automation-account hebt dat is gedefinieerd voor een specifieke regio, kunt u Hybrid Runbook Worker communicatie beperken tot dat regionale Data Center. Controleer de [DNS-records die door Azure Automation worden gebruikt](how-to/automation-region-dns-records.md) voor de vereiste DNS-records.

### <a name="configuration-of-private-networks-for-state-configuration"></a>Configuratie van particuliere netwerken voor status configuratie

Als uw knoop punten zich in een particulier netwerk bevinden, zijn de hierboven gedefinieerde poort en Url's vereist. Deze resources bieden netwerk connectiviteit voor het beheerde knoop punt en toestaan dat DSC communiceert met Azure Automation.

Als u gebruikmaakt van DSC-resources die communiceren tussen knoop punten, zoals de [WaitFor *-resources](/powershell/scripting/dsc/reference/resources/windows/waitForAllResource), moet u ook verkeer tussen knoop punten toestaan. Raadpleeg de documentatie voor elke DSC-resource voor meer informatie over deze netwerk vereisten.

Zie [TLS 1,2 Enforcement voor Azure Automation](automation-managing-data.md#tls-12-enforcement-for-azure-automation)voor meer informatie over de client vereisten voor TLS 1,2.

## <a name="update-management-and-change-tracking-and-inventory"></a>Updatebeheer en Wijzigingen bijhouden en inventaris

De adressen in deze tabel zijn vereist voor zowel Updatebeheer als voor Wijzigingen bijhouden en inventaris. De alinea die volgt op de tabel is ook van toepassing op beide.

Communicatie met deze adressen maakt gebruik van **poort 443**.

|Openbare Azure-peering  |Azure Government  |
|---------|---------|
|\*.ods.opinsights.azure.com    | \*. ods.opinsights.azure.us         |
|\*.oms.opinsights.azure.com     | \*. oms.opinsights.azure.us        |
|\*.blob.core.windows.net | \*. blob.core.usgovcloudapi.net|
|\*.azure-automation.net | \*. azure-automation.us|

Wanneer u beveiligings regels voor netwerk groepen maakt of Azure Firewall configureert om verkeer toe te staan voor de Automation-Service en de Log Analytics-werk ruimte, gebruikt u de [service Tags](../virtual-network/service-tags-overview.md#available-service-tags) **GuestAndHybridManagement** en **AzureMonitor**. Dit vereenvoudigt het voortdurend beheer van uw netwerk beveiligings regels. Als u verbinding wilt maken met de Automation-Service van uw Azure-Vm's veilig en privé, raadpleegt u [Azure private link gebruiken](./how-to/private-link-security.md). Zie [Download bare json-bestanden](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)voor informatie over het verkrijgen van de huidige servicetag en bereik gegevens die u wilt opnemen als onderdeel van uw on-premises firewall configuraties.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [automation updatebeheer Overview](update-management\overview.md).
* Meer informatie over [Hybrid Runbook worker](automation-hybrid-runbook-worker.md).
* Meer informatie over [Wijzigingen bijhouden en inventarisatie](change-tracking\overview.md).
* Meer informatie over de configuratie van de [automatiserings status](automation-dsc-overview.md).
