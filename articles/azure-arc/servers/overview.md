---
title: Overzicht van servers met Azure Arc
description: Lees meer informatie over het gebruik van Azure Arc voor servers om servers te beheren die buiten Azure worden gehost alsof het Azure-resources zijn.
keywords: azure automation, DSC, powershell, configuratie van gewenste status, updatebeheer, bijhouden van wijzigingen, inventaris, runbooks, python, grafisch, hybride
ms.date: 11/12/2020
ms.topic: overview
ms.openlocfilehash: 8368f89b8e471698ede3e9e8eb691e69f494b6e2
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183393"
---
# <a name="what-is-azure-arc-enabled-servers"></a>Wat zijn servers met Azure Arc?

Met servers met Azure Arc kunt u uw Windows- en Linux-machines die buiten Azure worden gehost, in uw bedrijfsnetwerk of via een andere cloudprovider, beheren op eenzelfde manier als systeemeigen Azure-VM's. Wanneer een hybride machine aan Azure wordt gekoppeld, wordt deze een gekoppelde machine en wordt deze behandeld als een resource in Azure. Elke gekoppelde machine heeft een resource-id, is opgenomen in een resourcegroep en profiteert van standaard Azure-constructies, zoals Azure Policy en het toepassen van tags. Serviceproviders die de on-premises infrastructuur van een klant beheren, kunnen met behulp van [Azure Lighthouse](../../lighthouse/how-to/manage-hybrid-infrastructure-arc.md) met Azure Arc hun hybride machines beheren in meerdere klantomgevingen, net als ze vandaag de dag met systeemeigen Azure-resources doen.

Om deze ervaring te bieden voor hybride machines die buiten Azure worden gehost, moet de Azure Connected Machine-agent worden geïnstalleerd op elke machine die u aan Azure wilt koppelen. Deze agent levert geen andere functionaliteit en vervangt de [Log Analytics-agent](../../azure-monitor/platform/log-analytics-agent.md) van Azure niet. De Log Analytics-agent voor Windows en Linux is vereist wanneer u het besturingssysteem en workloads op de machine proactief wilt monitoren, deze wilt beheren met Automation-runbooks of oplossingen zoals Updatebeheer, of andere Azure-services zoals [Azure Security Center](../../security-center/security-center-introduction.md) wilt gebruiken.

## <a name="supported-scenarios"></a>Ondersteunde scenario's

Wanneer u uw machine verbindt met servers met Azure Arc, kunt u de volgende configuratiebeheer- en bewakingstaken uitvoeren:

- [Azure Policy-gastconfiguraties](../../governance/policy/concepts/guest-configuration.md) toewijzen met dezelfde ervaring als beleidstoewijzing voor virtuele Azure-machines. Tegenwoordig worden door het gastconfiguratiebeleid geen configuraties toegepast, maar worden alleen instellingen binnen in de machine gecontroleerd. Zie [Prijzen voor Azure Policy](https://azure.microsoft.com/pricing/details/azure-policy/) voor informatie over de kosten van het gebruik van de richtlijnen voor de Azure Policy-gastconfiguratie met servers met Azure Arc.

- Rapporteren over configuratiewijzigingen van geïnstalleerde software, Microsoft-services, Windows-register en -bestanden, en Linux-daemons op bewaakte servers met behulp van Azure Automation [Change Tracking and Inventory](../../automation/change-tracking/overview.md).

- Controleer de prestaties van het gastbesturingssysteem van uw verbonden computer en ontdek toepassingscomponenten om hun processen en afhankelijkheden te controleren met andere resources die de door de toepassing worden gecommuniceerd met behulp van [Azure Monitor voor VM's](../../azure-monitor/insights/vminsights-overview.md).

- Vereenvoudig de implementatie met andere Azure-services zoals Azure Automation [State Configuration](../../automation/automation-dsc-overview.md) en Azure Monitor Log Analytics-werkruimte door gebruik te maken van de ondersteunde [Azure VM-extensies](manage-vm-extensions.md) voor uw niet-Azure Windows- of Linux-machine. Dit omvat het uitvoeren van configuratie na de implementatie of software-installatie met behulp van de aangepaste scriptextensie.

- Gebruik [Updatebeheer](../../automation/update-management/overview.md) in Azure Automation om updates van het besturingssysteem voor uw Windows- en Linux-servers te beheren

    > [!NOTE]
    > Op dit moment wordt het rechtstreeks inschakelen van Updatebeheer vanaf een server met Azure Arc niet ondersteund. Raadpleeg [Updatebeheer inschakelen vanuit uw Automation-account](../../automation/update-management/enable-from-automation-account.md) om te zien wat de vereisten zijn en hoe u updatebeheer voor uw server inschakelt.

- Neem uw niet-Azure-servers op voor het detecteren van bedreigingen en proactief toezicht op potentiële beveiligingsrisico's met behulp van [Azure Security Center](../../security-center/security-center-introduction.md).

Aanmeldingsgegevens die zijn verzameld en opgeslagen in een Log Analytics-werkruimte van de hybride machine bevatten nu eigenschappen die specifiek zijn voor de machine, zoals een resource-id. Dit kan worden gebruikt ter ondersteuning van toegang tot het logboek met [resource-context](../../azure-monitor/platform/design-logs-deployment.md#access-mode).

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>Ondersteunde regio’s

Zie de pagina met [Azure-producten per regio](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc) voor een definitieve lijst met ondersteunde regio's voor servers met Azure Arc.

In de meeste gevallen moet de locatie die u selecteert bij het maken van het installatiescript de Azure-regio zijn die geografisch het dichtst bij de locatie van uw machine ligt. Data-at-rest worden opgeslagen in de Azure-geografie die de door u opgegeven regio bevat. Dit kan ook van invloed zijn op welke regio u kiest als u gegevenslocatievereisten hebt. Als de Azure-regio waaraan uw machine is gekoppeld, wordt getroffen door een storing, heeft dit geen invloed op de gekoppelde machine, maar beheerbewerkingen die gebruikmaken van Azure kunnen mogelijk niet worden voltooid. Als u meerdere locaties hebt die een geografisch redundante service ondersteunen, kunt u bij een regionale storing het beste de machines in elke locatie aan een andere Azure-regio koppelen.

De volgende metagegevens over de verbonden machine worden verzameld en opgeslagen in de regio waarin de Azure Arc-machine is geconfigureerd:

- Naam en versie van besturingssysteem
- Computernaam
- Volledig gekwalificeerde domeinnaam (FQDN) van de computer
- Versie van Connected Machine-agent

Als de machine bijvoorbeeld is geregistreerd bij Azure Arc in de regio VS - oost, worden deze gegevens opgeslagen in deze VS-regio.

### <a name="agent-status"></a>Agenstatus

De Connected Machine-agent stuurt elke 5 minuten een standaard heartbeat-bericht naar de service. Als de service de heartbeat-berichten van een machine niet meer ontvangt, wordt de machine als offline beschouwd en wordt de status in de portal na 15 tot 30 minuten automatisch gewijzigd in **Verbinding verbroken**. Wanneer er weer een heartbeat-bericht van de Connected Machine-agent wordt ontvangen, wordt de status van de machine automatisch gewijzigd in **Verbonden**.

## <a name="next-steps"></a>Volgende stappen

Lees voordat u Arc-servers evalueert of inschakelt op meerdere hybride machines het artikel [Overzicht van Connected Machine Agent](agent-overview.md) om inzicht te krijgen in de vereisten, de technische details van de agent en de implementatiemethoden.