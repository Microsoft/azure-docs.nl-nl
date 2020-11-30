---
title: Levenscyclus beheer van virtuele machines met Azure VMware-oplossingen
description: Meer informatie over het beheren van alle aspecten van de levens cyclus van uw Azure VMware-oplossing-Vm's met Microsoft Azure systeem eigen hulpprogram ma's.
ms.topic: conceptual
ms.date: 09/11/2020
ms.openlocfilehash: ff1032900fa936895f3adfcb0d8a872f24948aca
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96326808"
---
# <a name="lifecycle-management-of-azure-vmware-solution-vms"></a>Levenscyclus beheer van virtuele machines met Azure VMware-oplossingen

Met Microsoft Azure systeem eigen hulp middelen kunt u uw virtuele machines (Vm's) bewaken en beheren in de Azure-omgeving. Daarnaast kunt u hiermee uw Vm's bewaken en beheren op Azure VMware-oplossing en uw on-premises Vm's. In dit overzicht bekijken we de geïntegreerde Azure-bewakings architectuur en hoe u de systeem eigen hulpprogram ma's kunt gebruiken voor het beheren van uw Azure VMware-oplossings-Vm's in de levens cyclus.

## <a name="benefits"></a>Voordelen

- Systeem eigen services van Azure kunnen worden gebruikt voor het beheren van uw virtuele machines in een hybride omgeving (Azure, Azure VMware-oplossing en on-premises).
- Geïntegreerde bewaking en zicht baarheid van uw Azure, Azure VMware-oplossing en on-premises Vm's.
- Met Azure Updatebeheer in Azure Automation kunt u updates van het besturings systeem voor zowel uw Windows-als Linux-machines beheren. 
- Azure Security Center biedt geavanceerde beveiliging tegen bedreigingen, waaronder:
    - Bestandsintegriteit controleren
    - Bestandsloze beveiligingswaarschuwingen
    - Evaluatie van patch voor besturings systeem
    - Evaluatie van onjuiste beveiligingsconfiguratie
    - Evaluatie van eindpuntbeveiliging 
- Implementeer eenvoudig de Log Analytics-agent met behulp van Azure Arc enabled-servers ondersteuning voor VM-extensies voor nieuwe en bestaande Vm's. 
- Met uw Log Analytics-werk ruimte in Azure Monitor kan de verzameling van Logboeken en prestatie meter items worden verzameld met de Log Analytics agent of uitbrei dingen. Verzamel gegevens en logboeken op één punt en presenteer die gegevens aan verschillende Azure systeem eigen services. 
- Extra voor delen van Azure Monitor zijn: 
    - Naadloze bewaking 
    - Beter inzicht in de infra structuur 
    - Chat meldingen 
    - Automatische resolutie 
    - Kosten efficiëntie 

## <a name="integrated-azure-monitoring-architecture"></a>Geïntegreerde Azure-bewakings architectuur

In het volgende diagram ziet u de geïntegreerde bewakings architectuur voor virtuele machines van Azure VMware-oplossingen.

![Geïntegreerde Azure-bewakings architectuur](media/lifecycle-management-azure-vmware-solutions-virtual-machines/integrated-azure-monitoring-architecture.png)

## <a name="before-you-start"></a>Voordat u begint

Als u geen ervaring hebt met Azure of als u niet bekend bent met een van de eerder genoemde services, raadpleegt u de volgende artikelen:

- [Overzicht van Automation-accountverificatie](../automation/automation-security-overview.md)
- [De implementatie van uw Azure monitor-logboeken ontwerpen](../azure-monitor/platform/design-logs-deployment.md) en [Azure monitor](../azure-monitor/overview.md)
- [Plannen](../security-center/security-center-planning-and-operations-guide.md) en [ondersteunde platforms](../security-center/security-center-os-coverage.md) voor Azure Security Center
- [Overzicht Azure Monitor voor VM's inschakelen](../azure-monitor/insights/vminsights-enable-overview.md)
- [Wat is servers voor Azure-Arc ingeschakeld?](../azure-arc/servers/overview.md) en [Wat is Azure Arc enabled Kubernetes?](../azure-arc/kubernetes/overview.md)
- [Overzicht van Updatebeheer](../automation/update-management/overview.md)

## <a name="integrating-and-deploying-azure-native-services"></a>Azure native Services integreren en implementeren

### <a name="enable-azure-update-management"></a>Azure Updatebeheer inschakelen

Azure Updatebeheer in Azure Automation beheert updates van het besturings systeem voor uw Windows-en Linux-computers in een hybride omgeving. Hiermee wordt de compatibiliteit van patches bewaakt en worden waarschuwingen over de afwijking van patches doorgestuurd naar Azure Monitor voor herstel. Azure Updatebeheer moet verbinding maken met uw Log Analytics-werk ruimte om opgeslagen gegevens te kunnen gebruiken om de status van updates op uw Vm's te beoordelen.

1.  Voordat u Log Analytics kunt toevoegen aan Azure Updatebeheer, moet u eerst [een Azure Automation-account maken](../automation/automation-create-standalone-account.md). Als u uw account liever met behulp van een sjabloon wilt maken, raadpleegt u [een Automation-account maken met behulp van een Azure Resource Manager sjabloon](../automation/quickstart-create-automation-account-template.md).

2. Met **log Analytics werk ruimte** kan de verzameling van Logboeken en prestatie meter items worden verzameld met de log Analytics agent of uitbrei dingen. Zie [een log Analytics-werk ruimte maken in de Azure Portal](../azure-monitor/learn/quick-create-workspace.md)om uw log Analytics-werk ruimte te maken. Als u wilt, kunt u ook een werk ruimte maken via [cli](../azure-monitor/learn/quick-create-workspace-cli.md), [power shell](../azure-monitor/platform/powershell-workspace-configuration.md)of [Azure Resource Manager sjabloon](../azure-monitor/samples/resource-manager-workspace.md).

3. Als u Azure Updatebeheer wilt inschakelen voor uw virtuele machines, raadpleegt u [updatebeheer inschakelen vanuit een Automation-account](../automation/update-management/enable-from-automation-account.md). In het proces koppelt u uw Log Analytics-werk ruimte aan uw Automation-account. 
 
4. Zodra u Vm's hebt toegevoegd aan Azure Updatebeheer, kunt u [updates op Vm's implementeren en resultaten bekijken](../automation/update-management/deploy-updates.md). 

### <a name="enable-azure-security-center"></a>Azure Security Center inschakelen

Azure Security Center biedt geavanceerde beveiliging tegen bedreigingen in uw hybride werk belastingen in de Cloud en on-premises. Hiermee wordt het beveiligings probleem van Vm's van Azure VMware-oplossingen beoordeeld en worden indien nodig waarschuwingen gegenereerd. Deze beveiligings waarschuwingen kunnen worden doorgestuurd naar Azure Monitor voor oplossing.

Voor Azure Security Center is geen implementatie vereist. Zie een lijst met [ondersteunde functies voor virtuele machines](../security-center/security-center-services.md)voor meer informatie.

1. Als u virtuele machines met Azure VMware-oplossingen en niet-Azure-Vm's wilt toevoegen aan Security Center, raadpleegt u [Quick Start: instellen van Azure Security Center](../security-center/security-center-get-started.md). 

2. Nadat u Vm's of Vm's van Azure VMware-oplossingen hebt toegevoegd vanuit een niet-Azure-omgeving, schakelt u Azure Defender in Security Center. Security Center worden de Vm's geëvalueerd voor mogelijke beveiligings problemen. Het bevat ook aanbevelingen op het tabblad Overzicht. Zie [beveiligings aanbevelingen in azure Security Center](../security-center/security-center-recommendations.md)voor meer informatie.

3. U kunt beveiligings beleid definiëren in Azure Security Center. Zie [werken met beveiligings beleid](../security-center/tutorial-security-policy.md)voor meer informatie over het configureren van uw beveiligings beleid.

### <a name="onboard-vms-to-azure-arc-enabled-servers"></a>Virtuele machines onboarden naar servers met Azure Arc ingeschakeld

Azure Arc breidt Azure-beheer uit naar elke infra structuur, waaronder Azure VMware-oplossing, on-premises of andere Cloud platforms.

- Zie [hybride computers verbinden met Azure op schaal](../azure-arc/servers/onboard-service-principal.md) om servers met Azure-arc in te scha kelen voor meerdere Windows-of Linux-vm's.

### <a name="onboard-hybrid-kubernetes-clusters-with-arc-enabled-kubernetes"></a>On-board hybride Kubernetes-clusters met Arc ingeschakelde Kubernetes

U kunt een Kubernetes-cluster dat wordt gehost in uw Azure VMware-oplossings omgeving koppelen met behulp van Azure Arc enabled Kubernetes. 

- Zie [een onboarding-service-principal voor Azure-Arc maken](../azure-arc/kubernetes/create-onboarding-service-principal.md).

### <a name="deploy-the-log-analytics-agent"></a>De Log Analytics-agent implementeren

Virtuele machines van Azure VMware-oplossingen kunnen worden bewaakt via de Log Analytics-agent (ook wel bekend als micro soft Monitoring Agent (MMA) of OMS Linux agent). U hebt al een Log Analytics werk ruimte gemaakt terwijl u Azure Automation Updatebeheer inschakelt.

- Implementeer de Log Analytics-agent met behulp van de [Azure Arc enabled-servers ondersteuning voor VM-extensies](../azure-arc/servers/manage-vm-extensions.md).

### <a name="enable-azure-monitor"></a>Azure Monitor inschakelen

Azure Monitor is een uitgebreide oplossing voor het verzamelen, analyseren en werken op telemetrie in uw Cloud-en on-premises omgevingen. Hiervoor is geen implementatie vereist. Met Azure Monitor kunt u de prestaties van een gast besturingssysteem controleren en toepassings afhankelijkheden detecteren en toewijzen voor Azure VMware-oplossingen of on-premises Vm's.

- Met Azure Monitor kunt u gegevens verzamelen uit verschillende bronnen om te controleren en analyseren. Zie [bronnen van bewakings gegevens voor Azure monitor](../azure-monitor/platform/data-sources.md)voor meer informatie.

- Verschillende typen gegevens verzamelen voor analyse, visualisatie en waarschuwingen. Zie [Azure monitor data platform](../azure-monitor/platform/data-platform.md)voor meer informatie.

- Zie [log Analytics-werk ruimte configureren voor Azure monitor voor VM's](../azure-monitor/insights/vminsights-configure-workspace.md)om Azure monitor te configureren met uw log Analytics-werk ruimte.

- U kunt waarschuwings regels maken om problemen in uw omgeving te identificeren, zoals een hoog gebruik van resources, ontbrekende patches, weinig schijf ruimte en heartbeat van uw Vm's. U kunt ook een geautomatiseerd antwoord op gedetecteerde gebeurtenissen instellen door een waarschuwing te verzenden naar de ITSM-hulpprogram ma's (IT Service Management). Melding over waarschuwings detectie kan ook via e-mail worden verzonden. Als u dergelijke regels wilt maken, raadpleegt u:
    - [Metrische waarschuwingen maken, weer geven en beheren met behulp van Azure monitor](../azure-monitor/platform/alerts-metric.md).
    - [Logboek waarschuwingen maken, weer geven en beheren met behulp van Azure monitor](../azure-monitor/platform/alerts-log.md).
    - [Actie regels](../azure-monitor/platform/alerts-action-rules.md) om geautomatiseerde acties en meldingen in te stellen.
    - [Verbind Azure met ITSM-hulpprogram ma's met behulp van IT Service Management-connector](../azure-monitor/platform/itsmc-overview.md).