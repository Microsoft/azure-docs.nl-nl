---
title: VM-extensiebeheer met Azure Arc ingeschakelde servers
description: Azure Arc-servers kunnen de implementatie van extensies van virtuele machines beheren die na de implementatie configuratie- en automatiseringstaken bieden met niet-Azure-VM's.
ms.date: 04/13/2021
ms.topic: conceptual
ms.openlocfilehash: 67f1b5b3db6ef446342e8381d54d487af1f3426a
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389788"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers"></a>Extensiebeheer voor virtuele machines met servers met Azure Arc

Extensies voor virtuele machines (VM's) zijn kleine toepassingen die configuratie- en automatiseringstaken na de implementatie op Virtuele Azure-machines bieden. Als voor een virtuele machine bijvoorbeeld software-installatie, antivirusbeveiliging of een script moet worden uitgevoerd, kan een VM-extensie worden gebruikt.

Azure Arc ingeschakelde servers kunt u Azure VM-extensies implementeren op niet-Azure Windows- en Linux-VM's, waardoor het beheer van uw hybride machine gedurende de levensduur wordt vereenvoudigd. VM-extensies kunnen worden beheerd met de volgende methoden op uw hybride machines of servers die worden beheerd door Arc-servers:

- De [Azure Portal](manage-vm-extensions-portal.md)
- De [Azure CLI](manage-vm-extensions-cli.md)
- [Azure PowerShell](manage-vm-extensions-powershell.md)
- Azure [Resource Manager sjablonen](manage-vm-extensions-template.md)

> [!NOTE]
> Azure Arc-servers biedt geen ondersteuning voor het implementeren en beheren van VM-extensies op virtuele Azure-machines. Zie het volgende artikel overzicht van VM-extensies voor [Azure-VM's.](../../virtual-machines/extensions/overview.md)

## <a name="key-benefits"></a>Belangrijkste voordelen

Azure Arc VM-extensieondersteuning voor servers biedt de volgende belangrijke voordelen:

- Verzamel logboekgegevens voor analyse met [logboeken in Azure Monitor](../../azure-monitor/logs/data-platform-logs.md) door de VM-extensie van de Log Analytics-agent in te stellen. Dit is handig voor het uitvoeren van complexe analyses voor gegevens uit verschillende soorten bronnen.

- Met [Azure Monitor voor VM's](../../azure-monitor/vm/vminsights-overview.md)analyseert de prestaties van uw Windows- en Linux-VM's en bewaakt u hun processen en afhankelijkheden van andere resources en externe processen. Dit wordt bereikt door zowel de Log Analytics-agent als de VM-extensies van de afhankelijkheidsagent in te stellen.

- Download en voer scripts uit op hybride verbonden machines met behulp van de aangepaste scriptextensie. Deze extensie is handig voor configuratie na de implementatie, software-installatie of andere configuratie- of beheertaken.

- Automatisch vernieuwen van certificaten die zijn opgeslagen in [een Azure Key Vault.](../../key-vault/general/overview.md)

## <a name="availability"></a>Beschikbaarheid

De functionaliteit van de VM-extensie is alleen beschikbaar in de lijst met [ondersteunde regio's.](overview.md#supported-regions) Zorg ervoor dat u uw computer onboardt in een van deze regio's.

## <a name="extensions"></a>Uitbreidingen

In deze release ondersteunen we de volgende VM-extensies op Windows- en Linux-machines.

Zie Agent overview (Agentoverzicht) voor meer informatie over het Azure Connected Machine agentpakket en details over het onderdeel [Extensieagent.](agent-overview.md#agent-component-details)

> [!NOTE]
> Onlangs is ondersteuning voor de DSC VM-extensie voor Arc-servers verwijderd. U kunt ook de aangepaste scriptextensie gebruiken om de configuratie na de implementatie van uw server of computer te beheren.

### <a name="windows-extensions"></a>Windows-extensies

|Toestelnummer |Publisher |Type |Aanvullende informatie |
|----------|----------|-----|-----------------------|
|Azure Defender scanner voor beveiligingsle beveiligingsleeds |Qualys |WindowsAgent.AzureSecurityCenter |[Azure Defender geïntegreerde oplossing voor evaluatie van beveiligingsleedsleed voor Azure- en hybride machines](../../security-center/deploy-vulnerability-assessment-vm.md)|
|Aangepaste scriptextensie |Microsoft.Compute | CustomScriptExtension |[Aangepaste scriptextensie voor Windows](../../virtual-machines/extensions/custom-script-windows.md)|
|Log Analytics-agent |Microsoft.EnterpriseCloud.Monitoring |MicrosoftMonitoringAgent |[Log Analytics VM-extensie voor Windows](../../virtual-machines/extensions/oms-windows.md)|
|Azure Monitor voor VM's (inzichten) |Microsoft.Azure.Monitoring.DependencyAgent |DependencyAgentWindows | [Extensie van afhankelijkheidsagent voor virtuele machines voor Windows](../../virtual-machines/extensions/agent-dependency-windows.md)|
|Azure Key Vault Certificate Sync | Microsoft.Azure.Key.Vault |KeyVaultForWindows | [Key Vault-extensie voor virtuele machines voor Windows](../../virtual-machines/extensions/key-vault-windows.md) |
|Azure Monitor-agent |Microsoft.Azure.Monitor |AzureMonitorWindowsAgent |[De Azure Monitor installeren (preview)](../../azure-monitor/agents/azure-monitor-agent-install.md) |

### <a name="linux-extensions"></a>Linux-extensies

|Toestelnummer |Publisher |Type |Aanvullende informatie |
|----------|----------|-----|-----------------------|
|Azure Defender scanner voor beveiligingsprobleem |Qualys |LinuxAgent.AzureSecurityCenter |[Azure Defender geïntegreerde oplossing voor de evaluatie van beveiligingsleed van Azure en hybride machines](../../security-center/deploy-vulnerability-assessment-vm.md)|
|Aangepaste scriptextensie |Microsoft.Azure.Extensions |CustomScript |[Aangepaste scriptextensie voor Linux versie 2](../../virtual-machines/extensions/custom-script-linux.md) |
|Log Analytics-agent |Microsoft.EnterpriseCloud.Monitoring |OmsAgentForLinux |[Log Analytics VM-extensie voor Linux](../../virtual-machines/extensions/oms-linux.md) |
|Azure Monitor voor VM's (inzichten) |Microsoft.Azure.Monitoring.DependencyAgent |DependencyAgentLinux |[Extensie van afhankelijkheidsagent voor virtuele machines voor Linux](../../virtual-machines/extensions/agent-dependency-linux.md) |
|Azure Key Vault Certificate Sync | Microsoft.Azure.Key.Vault |KeyVaultForLinux | [Key Vault-extensie voor virtuele machines voor Linux](../../virtual-machines/extensions/key-vault-linux.md) |
|Azure Monitor-agent |Microsoft.Azure.Monitor |AzureMonitorLinuxAgent |[De agent Azure Monitor installeren (preview)](../../azure-monitor/agents/azure-monitor-agent-install.md) |

## <a name="prerequisites"></a>Vereisten

Deze functie is afhankelijk van de volgende Azure-resourceproviders in uw abonnement:

- **Microsoft.HybridCompute**
- **Microsoft.GuestConfiguration**

Als ze nog niet zijn geregistreerd, volgt u de stappen onder [Azure-resourceproviders registreren.](agent-overview.md#register-azure-resource-providers)

Lees de documentatie voor elke VM-extensie waarnaar in de vorige tabel wordt verwezen om te begrijpen of deze netwerk- of systeemvereisten heeft. Dit kan u helpen te voorkomen dat u verbindingsproblemen ondervindt met een Azure-service of -functie die afhankelijk is van die VM-extensie.

### <a name="log-analytics-vm-extension"></a>Log Analytics VM-extensie

Voor de VM-extensie van de Log Analytics-agent voor Linux moet Python 2.x zijn geïnstalleerd op de doelmachine.

### <a name="azure-key-vault-vm-extension-preview"></a>Azure Key Vault VM-extensie (preview)

De Key Vault VM-extensie (preview) biedt geen ondersteuning voor de volgende Linux-besturingssystemen:

- CentOS Linux 7 (x64)
- Red Hat Enterprise Linux (RHEL) 7 (x64)
- Amazon Linux 2 (x64)

Het implementeren van Key Vault VM-extensie (preview) wordt alleen ondersteund met behulp van:

- De Azure CLI
- De Azure PowerShell
- Azure Resource Manager-sjabloon

Voordat u de extensie implementeert, moet u het volgende voltooien:

1. [Maak een kluis en certificaat](../../key-vault/certificates/quick-create-portal.md) (zelf-ondertekend of importeren).

2. Verleen de Azure Arc ingeschakelde server toegang tot het certificaatgeheim. Als u de [RBAC-preview](../../key-vault/general/rbac-guide.md)gebruikt, zoekt u de naam van de Azure Arc-resource en wijst u de **rol Key Vault Secrets User (preview)** toe. Als u een toegangsbeleid [Key Vault,](../../key-vault/general/assign-access-policy-portal.md)wijst u secret **get-machtigingen** toe aan de door Azure Arc van de resource toegewezen systeemidentiteit.

### <a name="connected-machine-agent"></a>Connected Machine-agent

Controleer of uw computer overeenkomt met [de ondersteunde versies](agent-overview.md#supported-operating-systems) van het Windows- en Linux-besturingssysteem voor de Azure Connected Machine agent.

De minimale versie van de Connected Machine-agent die wordt ondersteund met deze functie in Windows en Linux is de versie 1.0.

Zie Upgrade agent (Agent bijwerken) als u uw computer wilt upgraden naar de versie van de vereiste [agent.](manage-agent.md#upgrading-agent)

## <a name="next-steps"></a>Volgende stappen

U kunt VM-extensies implementeren, beheren en verwijderen met behulp van [de Azure CLI](manage-vm-extensions-cli.md), [Azure PowerShell](manage-vm-extensions-powershell.md), vanuit de [Azure Portal](manage-vm-extensions-portal.md)of Azure Resource Manager [sjablonen.](manage-vm-extensions-template.md)
