---
title: Azure-beveiligingsbasislijn voor Virtual Machine Scale Sets
description: De Virtual Machine Scale Sets beveiligingsbasislijn biedt procedurele richtlijnen en resources voor het implementeren van de beveiligingsaanbevelingen die zijn opgegeven in de Azure Security Benchmark.
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 60b1de12f031a55388960a6e3c4e7df00c43e3c8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867707"
---
# <a name="azure-security-baseline-for-virtual-machine-scale-sets"></a>Azure-beveiligingsbasislijn voor Virtual Machine Scale Sets

Met deze beveiligingsbasislijn worden richtlijnen van [de Azure Security Benchmark versie 1.0](../security/benchmarks/overview-v1.md) toegepast op Virtual Machine Scale Sets. De Azure Security-benchmark biedt aanbevelingen voor het beveiligen van uw cloudoplossingen  in Azure. De inhoud wordt gegroepeerd op basis van de beveiligingscontroles die zijn gedefinieerd door de Azure Security-benchmark en de gerelateerde richtlijnen die van toepassing zijn op Virtual Machine Scale Sets.

> [!NOTE]
> **Besturingselementen** die niet van toepassing zijn op Virtual Machine Scale Sets of waarvoor de verantwoordelijkheid van Microsoft is, zijn uitgesloten. Zie het volledige Virtual Machine Scale Sets toewijzingsbestand voor de beveiligingsbasislijn om te zien hoe Virtual Machine Scale Sets volledig is toegewezen aan de Azure **[Virtual Machine Scale Sets Security-benchmark.](https://github.com/MicrosoftDocs/SecurityBenchmarks/raw/master/Azure%20Offer%20Security%20Baselines/1.1/virtual-machine-scale-sets-security-baseline-v1.1.xlsx)**

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Benchmark: netwerkbeveiliging](../security/benchmarks/security-control-network-security.md) voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Azure-resources binnen virtuele netwerken beveiligen

**Richtlijnen:** wanneer u een virtuele Azure-machine (VM) maakt, moet u een virtueel netwerk maken of een bestaand virtueel netwerk gebruiken en de virtuele machine configureren met een subnet. Zorg ervoor dat voor alle geïmplementeerde subnetten een netwerkbeveiligingsgroep is toegepast met netwerktoegangsbesturingselementen die specifiek zijn voor vertrouwde poorten en bronnen van uw toepassingen.

Als u een specifieke use-case voor een gecentraliseerde firewall hebt, kunt Azure Firewall ook worden gebruikt om aan deze vereisten te voldoen.

- [Netwerken voor virtuele-machineschaalsets in Azure](virtual-machine-scale-sets-networking.md)

- [Een Virtual Network](../virtual-network/quick-create-portal.md)

- [Een NSG maken met een beveiligings config](../virtual-network/tutorial-filter-network-traffic.md)

- [Het implementeren en configureren van Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Voor waarschuwingen met betrekking tot dit besturingselement is mogelijk een [Azure Defender](/azure/security-center/azure-defender) nodig voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.compute-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: De configuratie en het verkeer van virtuele netwerken, subnetten en netwerkinterfaces bewaken en in een logboek plaatsen

**Richtlijnen:** gebruik de Azure Security Center om aanbevelingen voor netwerkbeveiliging te identificeren en te volgen om uw azure-VM-resources (virtuele machines) in Azure te beveiligen. Schakel NSG-stroomlogboeken in en verzend logboeken naar een opslagaccount voor verkeerscontrole voor de VM's voor ongebruikelijke activiteiten.

- [NSG-stroomlogboeken inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Informatie over netwerkbeveiliging die wordt geleverd door Azure Security Center](../security-center/security-center-network-recommendations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="13-protect-critical-web-applications"></a>1.3: Kritieke webtoepassingen beveiligen

**Richtlijnen:** als u uw virtuele-machineschaalset gebruikt om webtoepassingen te hosten, gebruikt u een netwerkbeveiligingsgroep (NSG) op het subnet van de virtuele-machineschaalset om te beperken welk netwerkverkeer, poorten en protocollen mogen communiceren. Volg een netwerkbenadering met de minste bevoegdheden bij het configureren van uw NSG's om alleen vereist verkeer naar uw toepassing toe te staan.

U kunt ook Azure Web Application Firewall (WAF) implementeren vóór kritieke webtoepassingen voor aanvullende inspectie van binnenkomend verkeer. Schakel diagnostische instelling voor WAF in en opname van logboeken in een opslagaccount, Event Hub of Log Analytics-werkruimte.

- [Netwerken voor virtuele-machineschaalsets in Azure](virtual-machine-scale-sets-networking.md)

- [Een toepassingsgateway met een Web Application Firewall maken met behulp van Azure Portal](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Communicatie met bekende schadelijke IP-adressen weigeren

**Richtlijnen:** DDoS (Distributed Denial of Service) Standard-beveiliging op de virtuele netwerken inschakelen om bescherming te bieden tegen DDoS-aanvallen. Met Azure Security Center Integrated Threat Intelligence kunt u de communicatie met bekende schadelijke IP-adressen bewaken.  Configureer Azure Firewall in elk van uw Virtual Network segmenten, met Bedreigingsinformatie ingeschakeld en geconfigureerd voor 'Waarschuwen en weigeren' voor schadelijk netwerkverkeer.

U kunt Azure Security Center Just-In-Time-netwerktoegang van de Windows Virtual Machines de goedgekeurde IP-adressen voor een beperkte periode beperken.  Gebruik ook Azure Security Center adaptieve netwerkharding om NSG-configuraties aan te bevelen die poorten en bron-IP's beperken op basis van werkelijk verkeer en bedreigingsinformatie.

- [DDoS-beveiliging configureren](../ddos-protection/manage-ddos-protection.md)

- [Een Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Inzicht Azure Security Center geïntegreerde bedreigingsinformatie](../security-center/azure-defender.md)

- [Inzicht Azure Security Center adaptieve netwerkharding](../security-center/security-center-adaptive-network-hardening.md)

- [Meer Azure Security Center Just-In-Time-Access Control](../security-center/security-center-just-in-time.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Waarschuwingen met betrekking tot dit besturingselement vereisen mogelijk een [Azure Defender](/azure/security-center/azure-defender) voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.compute-1-4.md)]

### <a name="15-record-network-packets"></a>1.5: Netwerkpakketten registreren

**Richtlijnen:** U kunt NSG-stroomlogboeken opnemen in een opslagaccount om stroomrecords te genereren voor uw Azure-Virtual Machines. Bij het onderzoeken van afwijkende activiteiten kunt u Network Watcher inschakelen, zodat netwerkverkeer kan worden gecontroleerd op ongebruikelijke en onverwachte activiteit.

- [NSG-stroomlogboeken inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Het inschakelen van Network Watcher](../network-watcher/network-watcher-create.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Op het netwerk gebaseerde inbraakdetectie-/inbraakpreventiesystemen (IDS/IPS) implementeren

**Richtlijnen:** door pakketopnamen van Network Watcher en een opensource-IDS-hulpprogramma te combineren, kunt u netwerkindringingsdetectie uitvoeren voor een breed scala aan bedreigingen. U kunt ook Azure Firewall implementeren in de Virtual Network-segmenten, met Bedreigingsinformatie ingeschakeld en geconfigureerd voor 'Waarschuwen en weigeren' voor schadelijk netwerkverkeer.

- [Netwerkindringingsdetectie Network Watcher en opensource-hulpprogramma's](../network-watcher/network-watcher-intrusion-detection-open-source-tools.md)

- [Een Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Waarschuwingen configureren met Azure Firewall](../firewall/threat-intel.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verkeer naar webtoepassingen beheren

**Richtlijnen:** als u virtuele-machineschaalset gebruikt om webtoepassingen te hosten, kunt u Azure Application Gateway implementeren voor webtoepassingen met HTTPS/SSL ingeschakeld voor vertrouwde certificaten. Met Azure Application Gateway kunt u het webverkeer van uw toepassing om leiden naar specifieke resources door listeners toe te wijzen aan poorten, regels te maken en resources toe te voegen aan een back-endpool, zoals een virtuele-machineschaalset, enzovoort.

- [Een Application Gateway](../application-gateway/quick-create-portal.md)

- [Https configureren Application Gateway te gebruiken](../application-gateway/create-ssl-portal.md)

- [Een schaalset maken die verwijst naar een toepassingsgateway](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-networking#create-a-scale-set-that-references-an-application-gateway)

- [Meer inzicht in laag 7-taakverdeling met Azure-webtoepassingsgateways](../application-gateway/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: De complexiteit en administratieve overhead van netwerkbeveiligingsregels minimaliseren

**Richtlijnen:** gebruik Virtual Network servicetags om netwerktoegangsbesturingselementen te definiëren in netwerkbeveiligingsgroepen of Azure Firewall geconfigureerd voor uw virtuele Azure-machines. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de servicetag (bijvoorbeeld ApiManagement) op te geven in het juiste bron- of doelveld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Microsoft beheert de adres voorvoegsels die worden omvat door de servicetag en werkt de servicetag automatisch bij wanneer adressen veranderen.

- [Servicetags begrijpen en gebruiken](../virtual-network/service-tags-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Standaardbeveiligingsconfiguraties voor netwerkapparaten onderhouden

**Richtlijnen:** standaardbeveiligingsconfiguraties definiëren en implementeren voor Azure-Virtual Machine Scale Sets met Azure Policy. U kunt Azure Blueprints ook gebruiken om grootschalige implementaties van Azure-VM's te vereenvoudigen door belangrijke omgevingsartefacten, zoals Azure Resource Manager-sjablonen, roltoewijzingen en Azure Policy-toewijzingen, in één blauwdrukdefinitie te verpakken. U kunt de blauwdruk toepassen op abonnementen en resourcebeheer inschakelen via blauwdrukversiebeheer.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Meer informatie over sjablonen voor virtuele-machineschaalsets](virtual-machine-scale-sets-mvss-start.md) 

- [Azure Policy voorbeelden voor netwerken](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [Een Azure Blueprint maken](../governance/blueprints/create-blueprint-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="110-document-traffic-configuration-rules"></a>1.10: Configuratieregels voor verkeer documenteren

**Richtlijnen:** u kunt tags gebruiken voor netwerkbeveiligingsgroepen (NSG's) en andere bronnen met betrekking tot netwerkbeveiliging en verkeersstromen die zijn geconfigureerd voor uw virtuele Windows-machines. Gebruik voor afzonderlijke NSG-regels het veld Beschrijving om de bedrijfs behoeften en/of duur op te geven voor regels die verkeer van/naar een netwerk toestaan.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

- [Een Virtual Network](../virtual-network/quick-create-portal.md)

- [Een NSG maken met een beveiligings config](../virtual-network/tutorial-filter-network-traffic.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Geautomatiseerde hulpprogramma's gebruiken om netwerkresourceconfiguraties te bewaken en wijzigingen te detecteren

**Richtlijnen:** Gebruik het Azure-activiteitenlogboek om wijzigingen in netwerkresourceconfiguraties met betrekking tot uw virtuele-machineschaalset van Azure te bewaken. Maak waarschuwingen binnen Azure Monitor die worden getriggerd wanneer er wijzigingen in kritieke netwerkinstellingen of resources plaatsvinden.

Gebruik Azure Policy om configuraties te valideren (en/of te herstellen) voor netwerkresources die betrekking hebben op de virtuele-machineschaalset.

- [Azure-activiteitenlogboekgebeurtenissen weergeven en ophalen](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Waarschuwingen maken in Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy voorbeelden voor netwerken](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Voor waarschuwingen met betrekking tot dit besturingselement is mogelijk een [Azure Defender](/azure/security-center/azure-defender) nodig voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 1.11](../../includes/policy/standards/asb/rp-controls/microsoft.compute-1-11.md)]

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie de Azure [Security Benchmark: Logging and Monitoring (Azure Security Benchmark: logboekregistratie en bewaking) voor meer informatie.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Goedgekeurde tijdsynchronisatiebronnen gebruiken

**Richtlijnen:** Microsoft onderhoudt tijdbronnen voor Azure-resources, maar u hebt de mogelijkheid om de tijdsynchronisatie-instellingen voor uw Virtual Machines.

- [Tijdsynchronisatie configureren voor Azure Windows-rekenbronnen](../virtual-machines/windows/time-sync.md)

- [Tijdsynchronisatie configureren voor Azure Linux-rekenbronnen](../virtual-machines/linux/time-sync.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

### <a name="22-configure-central-security-log-management"></a>2.2: Centraal beheer van beveiligingslogboek configureren

**Richtlijnen:** Activiteitenlogboeken kunnen worden gebruikt voor het controleren van bewerkingen en acties die worden uitgevoerd op resources in de virtuele-machineschaalset. Het activiteitenlogboek bevat alle schrijfbewerkingen (PUT, POST, DELETE) voor uw resources, met uitzondering van leesbewerkingen (GET). Activiteitenlogboeken kunnen worden gebruikt om een fout te vinden bij het oplossen van problemen of om te controleren hoe een gebruiker in uw organisatie een resource heeft gewijzigd.

U kunt logboekgegevens die zijn geproduceerd uit Azure-activiteitenlogboeken of virtual machine-resources inschakelen en inschakelen voor Azure Sentinel of een SIEM van derden voor centraal beheer van beveiligingslogboeken.

Gebruik Azure Security Center om bewaking van beveiligingsgebeurtenislogboek voor Azure-Virtual Machines. Gezien de hoeveelheid gegevens die het beveiligingsgebeurtenislogboek genereert, wordt het niet standaard opgeslagen. 

Als uw organisatie de gegevens van het beveiligingsgebeurtenislogboek van de virtuele machine wil behouden, kunnen deze worden opgeslagen in een Log Analytics-werkruimte op de gewenste gegevensverzamelingslaag die is geconfigureerd in Azure Security Center.

- [Platformlogboeken en metrische gegevens verzamelen met Azure Monitor ](../azure-monitor/essentials/diagnostic-settings.md)

- [Onboarding van Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Aan de slag met Azure Monitor siem-integratie van derden](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

- [Gegevensverzameling in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier) 

- [Virtuele machines bewaken in Azure](../azure-monitor/vm/monitor-vm-azure.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Waarschuwingen met betrekking tot dit besturingselement vereisen mogelijk een [Azure Defender](/azure/security-center/azure-defender) voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 2.2](../../includes/policy/standards/asb/rp-controls/microsoft.compute-2-2.md)]

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Auditlogregistratie voor Azure-resources inschakelen

**Richtlijnen:** Activiteitenlogboeken kunnen worden gebruikt voor het controleren van bewerkingen en acties die worden uitgevoerd op resources van de virtuele-machineschaalset. Het activiteitenlogboek bevat alle schrijfbewerkingen (PUT, POST, DELETE) voor uw resources, met uitzondering van leesbewerkingen (GET). Activiteitenlogboeken kunnen worden gebruikt om een fout te vinden bij het oplossen van problemen of om te controleren hoe een gebruiker in uw organisatie een resource heeft gewijzigd.

Schakel het verzamelen van diagnostische gegevens van het gast-besturingssysteem in door de diagnostische extensie te implementeren op Virtual Machines (VM). U kunt de extensie voor diagnostische gegevens gebruiken voor het verzamelen van diagnostische gegevens, zoals toepassingslogboeken of prestatiemeters van een virtuele Azure-machine.

Voor geavanceerde zichtbaarheid van de toepassingen en services die worden ondersteund door de Virtuele-machineschaalset van Azure kunt u zowel Azure Monitor voor VM's als Application Insights inschakelen. Met Application Insights kunt u uw toepassing bewaken en telemetrie vastleggen, zoals HTTP-aanvragen, uitzonderingen, enzovoort, zodat u problemen tussen de VM's en uw toepassing kunt correleren.

- [Platformlogboeken en metrische gegevens verzamelen met Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Gebeurtenissen in het Azure-activiteitenlogboek weergeven en ophalen](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Virtuele machines bewaken in Azure](../azure-monitor/vm/monitor-vm-azure.md)

- [Overzicht van Application Insights](../azure-monitor/app/app-insights-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Voor waarschuwingen met betrekking tot dit besturingselement [is mogelijk](/azure/security-center/azure-defender) een Azure Defender nodig voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.compute-2-3.md)]

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Beveiligingslogboeken van besturingssystemen verzamelen

**Richtlijnen:** Gebruik Azure Security Center om bewaking van beveiligingsgebeurtenislogboek te bieden voor Azure Virtual Machines. Gezien de hoeveelheid gegevens die het beveiligingsgebeurtenislogboek genereert, wordt het niet standaard opgeslagen. 

Als uw organisatie de gegevens van het beveiligingsgebeurtenislogboek van de virtuele machine wil behouden, kunnen deze worden opgeslagen in een Log Analytics-werkruimte op de gewenste gegevensverzamelingslaag die is geconfigureerd in Azure Security Center.

- [Gegevensverzameling in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier) 

- [Virtuele machines bewaken in Azure](../azure-monitor/vm/monitor-vm-azure.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Voor waarschuwingen met betrekking tot dit besturingselement [is mogelijk](/azure/security-center/azure-defender) een Azure Defender nodig voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 2.4](../../includes/policy/standards/asb/rp-controls/microsoft.compute-2-4.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: Opslagretentie van beveiligingslogboek configureren

**Richtlijnen:** Zorg ervoor dat voor opslagaccounts of Log Analytics-werkruimten die worden gebruikt voor het opslaan van logboeken van virtuele machines, de bewaarperiode voor logboeken is ingesteld volgens de nalevingsvoorschriften van uw organisatie.

- [Virtuele machines bewaken in Azure](../azure-monitor/vm/monitor-vm-azure.md)

- [Retentieperiode voor Log Analytics-werkruimte configureren](../azure-monitor/logs/manage-cost-storage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="26-monitor-and-review-logs"></a>2.6: Logboeken bewaken en controleren

**Richtlijnen:** Logboeken analyseren en controleren op afwijkende gedrag en regelmatig resultaten bekijken. Gebruik Azure Monitor logboeken te controleren en query's uit te voeren op logboekgegevens.

U kunt ook gegevens inschakelen en in gebruik nemen voor Azure Sentinel of een SIEM van derden om uw logboeken te controleren en te controleren.

- [Onboarding van Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Inzicht in Log Analytics-werkruimte](/azure/azure-monitor/logs/get-started-queries)

- [Aangepaste query's uitvoeren in Azure Monitor](../azure-monitor/logs/log-analytics-tutorial.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Waarschuwingen inschakelen voor afwijkende activiteiten

**Richtlijnen:** gebruik Azure Security Center geconfigureerd met een Log Analytics-werkruimte voor het bewaken en waarschuwen van afwijkende activiteiten in beveiligingslogboeken en gebeurtenissen voor uw Azure-Virtual Machines.  

U kunt ook gegevens inschakelen en in gebruik nemen voor Azure Sentinel of een SIEM van derden om waarschuwingen in te stellen voor afwijkende activiteiten.

- [Onboarding van Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Waarschuwingen beheren in Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Waarschuwingen voor logboekgegevens van Log Analytics](../azure-monitor/alerts/tutorial-response.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="28-centralize-anti-malware-logging"></a>2.8: Antimalwarelogregistratie centraliseren

**Richtlijnen:** u kunt Microsoft Antimalware gebruiken voor Azure Cloud Services en Virtual Machines en uw virtuele Windows-machines configureren om gebeurtenissen te melden bij een Azure Storage-account. Configureer een Log Analytics-werkruimte om de gebeurtenissen uit de opslagaccounts op te nemen en waar nodig waarschuwingen te maken. Volg de aanbevelingen in Azure Security Center: &amp; 'Compute-apps'.  Voor virtuele Linux-machines hebt u een hulpprogramma van derden nodig voor de detectie van beveiligingsleed tegen malware. 

- [Microsoft Antimalware configureren voor Cloud Services en Virtual Machines](../security/fundamentals/antimalware.md)

- [Bewaking op gastniveau inschakelen voor Virtual Machines](../cost-management-billing/cloudyn/azure-vm-extended-metrics.md)

- [Instructies voor onboarding van Linux-servers naar Azure Security Center](../security-center/quickstart-onboard-machines.md) 

- [De volgende koppeling bevat de door Microsoft aanbevolen beveiligingsrichtlijnen, die kunnen fungeren als een criterialijst voor de geselecteerde software voor beveiligingsprobleem](../virtual-machines/security-recommendations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor de aanbevelingen Security Center van [Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Voor waarschuwingen met betrekking tot dit besturingselement [is mogelijk](/azure/security-center/azure-defender) een Azure Defender nodig voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 2.8](../../includes/policy/standards/asb/rp-controls/microsoft.compute-2-8.md)]

### <a name="29-enable-dns-query-logging"></a>2.9: Logboekregistratie van DNS-query's inschakelen

**Richtlijnen:** Implementeert een oplossing van derden Azure Marketplace oplossing voor DNS-logboekregistratie op basis van de behoefte van uw organisatie.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="210-enable-command-line-audit-logging"></a>2.10: Controlelogregistratie vanaf de opdrachtregel inschakelen

**Richtlijnen:** de Azure Security Center biedt bewaking van beveiligingsgebeurtenislogboek voor Azure Virtual Machines (VM). Security Center de Microsoft Monitoring Agent in voor alle ondersteunde Azure-VM's en nieuwe virtuele azure-VM's die worden gemaakt als automatische inrichting is ingeschakeld of u kunt de agent handmatig installeren.  De agent schakelt gebeurtenis 4688 voor het maken van het proces en het veld CommandLine in gebeurtenis 4688 in. Nieuwe processen die op de VM zijn gemaakt, worden vastgelegd door EventLog en bewaakt door Security Center detectieservices van de VM.

Voor virtuele Linux-machines kunt u consolelogboekregistratie handmatig per knooppunt configureren en syslogs gebruiken om de gegevens op te slaan.  Gebruik ook Azure Monitor Log Analytics-werkruimte van azure om logboeken te controleren en query's uit te voeren op syslog-gegevens van virtuele Azure-machines.

- [Gegevensverzameling in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

- [Aangepaste query's uitvoeren in Azure Monitor](../azure-monitor/logs/get-started-queries.md)

- [Syslog-gegevensbronnen in Azure Monitor](../azure-monitor/agents/data-sources-syslog.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie Azure Security [Benchmark: Identity and Access Control (Azure Security Benchmark: identiteit en Access Control).](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Een inventaris van beheerdersaccounts onderhouden

**Richtlijnen:** hoewel Azure Active Directory (Azure AD) de aanbevolen methode is om gebruikerstoegang te beheren, kan Azure Virtual Machines lokale accounts hebben. Zowel lokale accounts als domeinaccounts moeten worden gecontroleerd en beheerd, normaal met een minimale footprint. Maak daarnaast gebruik van Azure Privileged Identity Management voor beheerdersaccounts die worden gebruikt voor toegang tot de resources van de virtuele machines.

- [Informatie voor lokale accounts](https://docs.microsoft.com/azure/active-directory/devices/assign-local-admin#manage-the-device-administrator-role)

- [Informatie over Privileged Identity Manager](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Standaardwachtwoorden wijzigen, indien van toepassing

**Richtlijnen:** Azure Virtual Machine Scale Set en Azure Active Directory (Azure AD) hebben niet het concept van standaardwachtwoorden. De klant is verantwoordelijk voor toepassingen van derden en Marketplace-services die standaardwachtwoorden kunnen gebruiken.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Toegewezen beheerdersaccounts gebruiken

**Richtlijnen:** maak standaardprocedures voor het gebruik van toegewezen beheerdersaccounts die toegang hebben tot uw virtuele machines. Gebruik Azure Security Center identiteits- en toegangsbeheer om het aantal beheerdersaccounts te bewaken. Alle beheerdersaccounts die worden gebruikt voor toegang tot azure virtual machine-resources, kunnen ook worden beheerd door Azure Privileged Identity Management (PIM). Azure Privileged Identity Management biedt verschillende opties, zoals Just-In-Time-verhoging, meervoudige verificatie vereisen voordat een rol wordt aangenomen, en overdrachtsopties, zodat machtigingen alleen beschikbaar zijn voor specifieke tijdsframes en een goedkeurder nodig zijn.

- [Inzicht Azure Security Center identiteit en toegang](../security-center/security-center-identity-access.md)

- [Informatie over Privileged Identity Manager](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Voor waarschuwingen met betrekking tot dit besturingselement [is mogelijk](/azure/security-center/azure-defender) een Azure Defender nodig voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 3.3](../../includes/policy/standards/asb/rp-controls/microsoft.compute-3-3.md)]

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Eenmalige Azure Active Directory gebruiken

**Richtlijnen:** gebruik waar mogelijk eenmalige aanmelding met Azure Active Directory (Azure AD) in plaats van afzonderlijke, individuele referenties per service te configureren. Gebruik Azure Security Center aanbevelingen voor identiteits- en toegangsbeheer.

- [Een aanmelding voor toepassingen in Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Identiteit en toegang bewaken binnen Azure Security Center](../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Meervoudige verificatie gebruiken voor alle Azure Active Directory op basis van toegang

**Richtlijnen:** meervoudige Azure Active Directory (Azure AD) inschakelen en Azure Security Center aanbevelingen voor identiteits- en toegangsbeheer volgen.

- [Meervoudige verificatie inschakelen in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identiteit en toegang bewaken binnen Azure Security Center](../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: Beveiligde, door Azure beheerde werkstations gebruiken voor beheertaken

**Richtlijnen:** GebruikWS (privileged access-werkstations) met meervoudige verificatie die is geconfigureerd om u aan te melden bij en Azure-resources te configureren.

- [Meer informatie over Privileged Access-werkstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Meervoudige verificatie inschakelen in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Logboeken en waarschuwingen voor verdachte activiteiten van beheerdersaccounts

**Richtlijnen:** gebruik Azure Active Directory (Azure AD) Privileged Identity Management (PIM) voor het genereren van logboeken en waarschuwingen wanneer er verdachte of onveilige activiteiten plaatsvinden in de omgeving. Gebruik Azure AD-risicodetecties om waarschuwingen en rapporten over riskant gebruikersgedrag weer te geven. Optioneel kan de klant waarschuwingen Azure Security Center risicodetectie opnemen in Azure Monitor configureren en aangepaste waarschuwingen/meldingen configureren met behulp van actiegroepen.

- [Een Privileged Identity Management (PIM) implementeren](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Inzicht Azure Security Center risicodetecties (verdachte activiteit)](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure-activiteitenlogboeken integreren in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Actiegroepen configureren voor aangepaste waarschuwingen en meldingen](../azure-monitor/alerts/action-groups.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Richtlijnen:** gebruik Azure Active Directory (Azure AD) beleid voor voorwaardelijke toegang en benoemde locaties om alleen toegang toe te staan vanuit specifieke logische groeperingen van IP-adresbereiken of landen/regio's.

- [Benoemde locaties configureren in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="39-use-azure-active-directory"></a>3.9: Gebruik Azure Active Directory

**Richtlijnen:** gebruik Azure Active Directory (Azure AD) als het centrale verificatie- en autorisatiesysteem. Azure AD beschermt gegevens met behulp van sterke versleuteling voor data-at-rest en in transit. Azure AD biedt ook salts, hashes en slaat veilig gebruikersreferenties op.  U kunt beheerde identiteiten gebruiken voor verificatie bij elke service die ondersteuning biedt voor Azure AD-verificatie, inclusief Key Vault, zonder referenties in uw code. Uw code die wordt uitgevoerd op een virtuele machine, kan de beheerde identiteit gebruiken om toegangstokens aan te vragen voor services die ondersteuning bieden voor Azure AD-verificatie.

- [Een Azure AD-instantie maken en configureren](../active-directory-domain-services/tutorial-create-instance.md)

- [Overzicht van beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmatig gebruikerstoegang controleren en afstemmen

**Richtlijnen:** Azure Active Directory (Azure AD) biedt logboeken voor het ontdekken van verouderde accounts. Gebruik daarnaast Azure AD-identiteitstoegangsbeoordelingen om groepslidmaatschap, toegang tot bedrijfstoepassingen en roltoewijzingen efficiënt te beheren. De toegang van de gebruiker kan regelmatig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers nog toegang hebben. Wanneer u virtuele Azure-machines gebruikt, moet u de lokale beveiligingsgroepen en gebruikers controleren om ervoor te zorgen dat er geen onverwachte accounts zijn die het systeem in gevaar kunnen brengen.

- [Toegangsbeoordelingen voor Azure Identity gebruiken](../active-directory/governance/access-reviews-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Pogingen tot toegang tot gedeactiveerde referenties controleren

**Richtlijnen:** Configureer diagnostische instellingen voor Azure Active Directory (Azure AD) om de auditlogboeken en aanmeldingslogboeken te verzenden naar een Log Analytics-werkruimte. Gebruik ook Azure Monitor logboeken te controleren en query's uit te voeren op logboekgegevens van virtuele Azure-machines.

- [Inzicht in Log Analytics-werkruimte](../azure-monitor/logs/log-analytics-tutorial.md)

- [Azure-activiteitenlogboeken integreren in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Aangepaste query's uitvoeren in Azure Monitor](../azure-monitor/logs/get-started-queries.md)

- [Virtuele machines bewaken in Azure](/azure/azure-monitor/vm/monitor-vm-azure)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Afwijking van aanmeldingsgedrag van account

**Richtlijnen:** gebruik Azure Active Directory (Azure AD)-functies voor risico- en identiteitsbeveiliging om geautomatiseerde reacties te configureren op gedetecteerde verdachte acties met betrekking tot de resources van uw opslagaccount. U moet geautomatiseerde reacties via Azure Sentinel om de beveiligingsreacties van uw organisatie te implementeren.

- [Riskante Azure AD-aanmeldingen weergeven](../active-directory/identity-protection/overview-identity-protection.md)

- [Risicobeleid voor Identiteitsbeveiliging configureren en inschakelen](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Onboarding van Azure Sentinel](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Microsoft toegang geven tot relevante klantgegevens tijdens ondersteuningsscenario's

**Richtlijnen:** In ondersteuningsscenario's waarin Microsoft toegang nodig heeft tot klantgegevens (zoals tijdens een ondersteuningsaanvraag), gebruikt u Klanten-lockbox voor virtuele Azure-machines om aanvragen voor toegang tot klantgegevens te controleren en goed te keuren of af te wijzen.

- [Informatie over Klanten-lockbox](../security/fundamentals/customer-lockbox-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Benchmark: gegevensbescherming](../security/benchmarks/security-control-data-protection.md) voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Een inventaris van gevoelige informatie onderhouden

**Richtlijnen:** Tags gebruiken om virtuele Azure-machines bij te houden die gevoelige informatie opslaan of verwerken.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Systemen isoleren die gevoelige informatie opslaan of verwerken

**Richtlijnen:** Implementeert afzonderlijke abonnementen en/of beheergroepen voor ontwikkeling, test en productie. Resources moeten worden gescheiden door een virtueel netwerk/subnet, op de juiste wijze gelabeld en beveiligd binnen een netwerkbeveiligingsgroep (NSG) of door een Azure Firewall. Voor Virtual Machines opslaan of verwerken van gevoelige gegevens implementeert u beleid en procedure(en) om deze uit te schakelen wanneer deze niet in gebruik zijn.

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Een Beheergroepen](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

- [Een Virtual Network](../virtual-network/quick-create-portal.md)

- [Een NSG maken met een beveiligings config](../virtual-network/tutorial-filter-network-traffic.md)

- [Een Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Waarschuwingen of waarschuwingen configureren en weigeren met Azure Firewall](../firewall/threat-intel.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Onbevoegde overdracht van gevoelige informatie bewaken en blokkeren

**Richtlijnen:** Implementeert een oplossing van derden op netwerkperimeters die de overdracht van gevoelige gegevens door onbevoegden controleert en dergelijke overdrachten blokkeert tijdens het waarschuwen van informatiebeveiligingsprofessionals.

Voor het onderliggende platform dat wordt beheerd door Microsoft, behandelt Microsoft alle klantinhoud als gevoelig om bescherming te bieden tegen verlies en blootstelling van klantgegevens. Om ervoor te zorgen dat klantgegevens in Azure veilig blijven, heeft Microsoft een suite met robuuste besturingselementen en mogelijkheden voor gegevensbeveiliging geïmplementeerd en onderhouden.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Alle gevoelige gegevens tijdens de overdracht versleutelen

**Richtlijnen:** Gegevens die worden verzonden naar, van en tussen Virtual Machines (VM's) waarop Windows wordt uitgevoerd, worden op verschillende manieren versleuteld, afhankelijk van de aard van de verbinding, zoals bij het maken van verbinding met een VM in een RDP- of SSH-sessie.

Microsoft gebruikt het Transport Layer Security (TLS)-protocol om gegevens te beveiligen wanneer deze worden gebruikt tussen de cloudservices en klanten.

- [In-transit-versleuteling in VM's](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#in-transit-encryption-in-vms)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Een actief detectieprogramma gebruiken om gevoelige gegevens te identificeren

**Richtlijnen:** Gebruik een actief detectieprogramma van derden om alle gevoelige informatie te identificeren die is opgeslagen, verwerkt of verzonden door de technologiesystemen van de organisatie, met inbegrip van de informatie die zich op locatie of bij een externe serviceprovider bevindt en werk de inventaris van gevoelige informatie van de organisatie bij.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC gebruiken om de toegang tot resources te beheren 

**Richtlijnen:** Met op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) kunt u taken scheiden binnen uw team en alleen de hoeveelheid toegang verlenen aan gebruikers op uw virtuele machine (VM) die ze nodig hebben om hun taken uit te voeren. In plaats van iedereen onbeperkte machtigingen te verlenen op de VM, kunt u alleen bepaalde acties toestaan. U kunt toegangsbeheer voor de VM in de Azure Portal configureren met behulp van de Azure CLI of Azure PowerShell.

- [Azure RBAC](../role-based-access-control/overview.md)

- [Ingebouwde Azure-rollen](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Preventie van gegevensverlies op basis van een host gebruiken om toegangsbeheer af te dwingen

**Richtlijnen:** Implementeert een hulpprogramma van derden, zoals een geautomatiseerde oplossing voor preventie van gegevensverlies op basis van een host, om toegangscontroles af te dwingen om het risico op gegevensschending te beperken.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Gevoelige informatie at-rest versleutelen

**Richtlijnen:** Virtuele schijven op Virtual Machines (VM) worden in rust versleuteld met versleuteling aan de serverzijde of Azure Disk Encryption (ADE). Azure Disk Encryption maakt gebruik van DM-Crypt functie van Linux om beheerde schijven te versleutelen met door de klant beheerde sleutels binnen de gast-VM. Versleuteling aan de serverzijde met door de klant beheerde sleutels verbetert ADE doordat u alle typen besturingssystemen en afbeeldingen voor uw VM's kunt gebruiken door gegevens in de Storage-service te versleutelen.

- [Azure Disk Encryption voor Virtual Machine Scale Sets](disk-encryption-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Voor waarschuwingen met betrekking tot dit besturingselement [is mogelijk](/azure/security-center/azure-defender) een Azure Defender nodig voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 4.8](../../includes/policy/standards/asb/rp-controls/microsoft.compute-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Wijzigingen in kritieke Azure-resources aanmelden en er waarschuwingen voor ontvangen

**Richtlijnen:** gebruik Azure Monitor met het Azure-activiteitenlogboek om waarschuwingen te maken voor wanneer er wijzigingen worden aangebracht in virtuele-machineschaalsets en gerelateerde resources.  

- [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteitenlogboek](../azure-monitor/alerts/alerts-activity-log.md)

- [Azure Storage-analyselogboeken](../storage/common/storage-analytics-logging.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie de Azure [Security Benchmark: Vulnerability Management](../security/benchmarks/security-control-vulnerability-management.md)voor meer informatie.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Geautomatiseerde hulpprogramma's voor het scannen op beveiligingsleed uitvoeren

**Richtlijnen:** volg aanbevelingen van Azure Security Center over het uitvoeren van evaluaties van beveiligingsleeds op uw Azure-Virtual Machines.  Gebruik de door Azure Security aanbevolen oplossing of een oplossing van derden voor het uitvoeren van evaluaties van beveiligingsleeds voor uw virtuele machines.

- [Aanbevelingen voor het Azure Security Center van beveiligingsleeds implementeren](../security-center/deploy-vulnerability-assessment-vm.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Waarschuwingen met betrekking tot dit besturingselement vereisen mogelijk een [Azure Defender](/azure/security-center/azure-defender) voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 5.1](../../includes/policy/standards/asb/rp-controls/microsoft.compute-5-1.md)]

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Geautomatiseerde beheeroplossing voor besturingssysteempatchs implementeren

**Richtlijnen:** Schakel Automatische upgrades van besturingssystemen in voor ondersteunde besturingssysteemversies of voor aangepaste installatie installatie afbeeldingen die zijn opgeslagen in een Shared Image Gallery.

- [Automatische upgrades van het besturingssysteem voor virtuele-machineschaalsets in Azure](virtual-machine-scale-sets-automatic-upgrade.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Waarschuwingen met betrekking tot dit besturingselement vereisen mogelijk een [Azure Defender](/azure/security-center/azure-defender) voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 5.2](../../includes/policy/standards/asb/rp-controls/microsoft.compute-5-2.md)]

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Een automatische oplossing voor patchbeheer implementeren voor softwaretitels van derden

**Richtlijnen:** Azure Virtual Machine Scale Sets automatische upgrades van besturingssysteemafbeeldingen gebruiken. U kunt de Azure Desired State Configuration (DSC) gebruiken voor onderliggende virtuele machines in de virtuele-machineschaalset. DSC wordt gebruikt om de VM's te configureren wanneer ze online komen, zodat ze de gewenste software uitvoeren.

- [Gebruik Virtual Machine Scale Sets met de Azure DSC extensie](virtual-machine-scale-sets-dsc.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Scans van back-to-back-beveiligingsprobleem vergelijken

**Richtlijnen:** Scanresultaten met consistente intervallen exporteren en de resultaten vergelijken om te controleren of beveiligingsproblemen zijn verteerd. Wanneer u vulnerability management aanbevolen door Azure Security Center, kan de klant naar de portal van de geselecteerde oplossing draaien om historische scangegevens weer te geven.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Een risicobeoordelingsproces gebruiken om prioriteit te geven aan het herstellen van gevonden beveiligingsproblemen

**Richtlijnen:** gebruik de standaardrisicoclassificaties (secure score) die door de Azure Security Center.

- [Inzicht Azure Security Center de secure score](../security-center/secure-score-security-controls.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Voor waarschuwingen met betrekking tot dit besturingselement [is mogelijk](/azure/security-center/azure-defender) een Azure Defender nodig voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 5.5](../../includes/policy/standards/asb/rp-controls/microsoft.compute-5-5.md)]

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie Azure Security [Benchmark: Inventory and Asset Management (Azure Security Benchmark: Inventaris en Asset Management) voor meer informatie.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Een geautomatiseerde oplossing voor assetdetectie gebruiken

**Richtlijnen:** gebruik Azure Resource Graph om alle resources (inclusief virtuele machines) binnen uw abonnementen op te vragen en te ontdekken. Zorg ervoor dat u over de juiste (lees)machtigingen in uw tenant hebt en alle Azure-abonnementen en resources binnen uw abonnementen kunt opsnoemen.

- [Query's maken met Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Uw Azure-abonnementen weergeven](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Inzicht krijgen in Azure RBAC](../role-based-access-control/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="62-maintain-asset-metadata"></a>6.2: Metagegevens van activa onderhouden

**Richtlijnen:** Tags toepassen op Azure-resources die metagegevens geven om ze logisch te organiseren op basis van een taxonomie.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Niet-geautoriseerde Azure-resources verwijderen

**Richtlijnen:** gebruik waar nodig tags, beheergroepen en afzonderlijke abonnementen om uw schaalsets en gerelateerde resources Virtual Machines te organiseren en bij te houden. Afstemmen van inventaris op regelmatige basis en ervoor zorgen dat niet-geautoriseerde resources worden verwijderd uit het abonnement op tijd.

- [Extra Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Een Beheergroepen](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Inventaris van goedgekeurde Azure-resources definiëren en onderhouden

**Richtlijnen:** Maak een inventaris van goedgekeurde Azure-resources en goedgekeurde software voor rekenbronnen op de behoeften van onze organisatie.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Controleren op niet-goedgekeurde Azure-resources

**Richtlijnen:** Gebruik Azure-beleid om beperkingen in te stellen voor het type resources dat kan worden gemaakt in een of meer klantabonnementen met behulp van de volgende ingebouwde beleidsdefinities:

- Niet toegestane resourcetypen

- Toegestane brontypen

Gebruik daarnaast de Azure Resource Graph om query's uit te voeren op resources binnen de abonnement(en). Dit kan helpen in omgevingen met een hoge beveiliging, zoals omgevingen met opslagaccounts.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Query's maken met Azure Graph](../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Controleren op niet-goedgekeurde softwaretoepassingen binnen rekenbronnen

**Richtlijnen:** Azure Automation biedt volledige controle tijdens de implementatie, bewerkingen en het buiten gebruik stellen van workloads en resources.  Maak gebruik van Azure Virtual Machine Inventory om het verzamelen van informatie over alle software op Virtual Machines. Opmerking: De softwarenaam, versie, uitgever en vernieuwingstijd zijn beschikbaar in de Azure Portal. Om toegang te krijgen tot de installatiedatum en andere informatie, moet de klant diagnostische gegevens op gastniveau inschakelen en de Windows-gebeurtenislogboeken naar een Log Analytics-werkruimte brengen.

Adaptieve toepassingsbesturingselementen zijn momenteel niet beschikbaar voor Virtual Machine Scale Sets.

- [Een inleiding tot Azure Automation](../automation/automation-intro.md)

- [Azure VM-inventaris inschakelen](../automation/automation-tutorial-installed-software.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Niet-goedgekeurde Azure-resources en -softwaretoepassingen verwijderen

**Richtlijnen:** Azure Automation volledige controle tijdens de implementatie, bewerkingen en buiten gebruik stellen van workloads en resources.  U kunt de Wijzigingen bijhouden gebruiken om alle software te identificeren die op de Virtual Machines. U kunt uw eigen proces implementeren of Azure Automation State Configuration voor het verwijderen van niet-geautoriseerde software.

- [Een inleiding tot Azure Automation](../automation/automation-intro.md)

- [Wijzigingen in uw omgeving bijhouden met de Wijzigingen bijhouden oplossing](../automation/change-tracking/overview.md)

- [Azure Automation State Configuration Overzicht](../automation/automation-dsc-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="68-use-only-approved-applications"></a>6.8: Alleen goedgekeurde toepassingen gebruiken

**Richtlijnen:** Momenteel zijn adaptieve toepassingsbesturingselementen niet beschikbaar voor Virtual Machine Scale Sets. Gebruik software van derden om het gebruik van alleen goedgekeurde toepassingen te controleren.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Voor waarschuwingen met betrekking tot dit besturingselement [is mogelijk](/azure/security-center/azure-defender) een Azure Defender nodig voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 6.8](../../includes/policy/standards/asb/rp-controls/microsoft.compute-6-8.md)]

### <a name="69-use-only-approved-azure-services"></a>6.9: Alleen goedgekeurde Azure-services gebruiken

**Richtlijnen:** Gebruik Azure Policy om beperkingen in te stellen voor het type resources dat kan worden gemaakt in een of meer klantabonnementen met behulp van de volgende ingebouwde beleidsdefinities:
- Niet toegestane resourcetypen
- Toegestane brontypen

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een specifiek resourcetype weigeren met Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Waarschuwingen met betrekking tot dit besturingselement vereisen mogelijk een [Azure Defender](/azure/security-center/azure-defender) voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 6.9](../../includes/policy/standards/asb/rp-controls/microsoft.compute-6-9.md)]

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Een inventaris van goedgekeurde softwaretitels onderhouden

**Richtlijnen:** momenteel zijn adaptieve toepassingsregelaars niet beschikbaar voor Virtual Machine Scale Sets. Implementeert een oplossing van derden als dit niet voldoet aan de vereisten van uw organisatie.

- [Adaptieve toepassingsbesturingselementen Azure Security Center gebruiken](../security-center/security-center-adaptive-application.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Waarschuwingen met betrekking tot dit besturingselement vereisen mogelijk een [Azure Defender](/azure/security-center/azure-defender) voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 6.10](../../includes/policy/standards/asb/rp-controls/microsoft.compute-6-10.md)]

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: De mogelijkheid van gebruikers om te communiceren met Azure Resource Manager

**Richtlijnen:** Gebruik voorwaardelijke toegang van Azure om de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager te beperken door Toegang blokkeren te configureren voor de app Microsoft Azure Management.

- [Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: De mogelijkheid van gebruikers om scripts uit te voeren binnen rekenbronnen beperken

**Richtlijnen:** Afhankelijk van het type scripts kunt u besturingssysteemspecifieke configuraties of resources van derden gebruiken om de mogelijkheid van gebruikers om scripts uit te voeren binnen Azure-rekenresources te beperken.

- [De uitvoering van PowerShell-scripts beheren in Windows-omgevingen](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-7&amp;preserve-view=true)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Toepassingen met een hoog risico fysiek of logisch scheiden

**Richtlijnen:** Toepassingen met een hoog risico die in uw Azure-omgeving zijn geïmplementeerd, kunnen worden geïsoleerd met behulp van een virtueel netwerk, subnet, abonnementen, beheergroepen enzovoort en voldoende worden beveiligd met een Azure Firewall, Web Application Firewall (WAF) of netwerkbeveiligingsgroep (NSG). 

- [Virtuele netwerken en virtuele machines in Azure](/azure/virtual-machines/windows/network-overview)

- [Azure Firewall overzicht](../firewall/overview.md)

- [Web Application Firewall overzicht](../web-application-firewall/overview.md)

- [Overzicht van netwerkbeveiliging](../virtual-network/network-security-groups-overview.md)

- [Overzicht van Azure Virtual Network](../virtual-network/virtual-networks-overview.md)

- [Uw resources organiseren met Azure-beheergroepen](../governance/management-groups/overview.md)

- [Handleiding voor beslissingen over abonnementen](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="secure-configuration"></a>Veilige configuratie

*Zie de Azure [Security Benchmark: Secure Configuration voor meer informatie.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Veilige configuraties voor alle Azure-resources tot stand brengen

**Richtlijnen:** gebruik Azure Policy of Azure Security Center om beveiligingsconfiguraties voor alle Azure-resources te onderhouden. Daarnaast biedt Azure Resource Manager de mogelijkheid om de sjabloon te exporteren in JavaScript Object Notation (JSON), die moet worden gecontroleerd om ervoor te zorgen dat de configuraties voldoen aan de beveiligingsvereisten voor uw bedrijf of deze overschrijden.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Informatie over het downloaden van de VM-sjabloon](/previous-versions/azure/virtual-machines/windows/download-template)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Veilige besturingssysteemconfiguraties maken

**Richtlijnen:** gebruik Azure Security Center beveiligingsproblemen in beveiligingsconfiguraties op uw Virtual Machines om beveiligingsconfiguraties op alle rekenbronnen te onderhouden.

- [Aanbevelingen voor Azure Security Center bewaken](../security-center/security-center-recommendations.md)

- [Aanbevelingen Azure Security Center herstellen](../security-center/security-center-remediate-recommendations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Veilige Azure-resourceconfiguraties onderhouden

**Richtlijnen:** gebruik Azure Resource Manager azure-sjablonen en Azure-beleid om Veilig Azure-resources te configureren die zijn gekoppeld aan Virtual Machines schaalsets.  Azure Resource Manager sjablonen zijn JSON-bestanden die worden gebruikt voor het implementeren van virtuele machines, samen met Azure-resources, en aangepaste sjablonen moeten worden onderhouden.  Microsoft voert het onderhoud uit op basissjablonen.  Gebruik Azure Policy [deny] en [deploy if not exist] om veilige instellingen af te dwingen voor uw Azure-resources.

- [Informatie over het maken Azure Resource Manager sjablonen](../virtual-machines/windows/ps-template.md)

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Inzicht Azure Policy effecten](../governance/policy/concepts/effects.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Veilige besturingssysteemconfiguraties onderhouden

**Richtlijnen:** Er zijn verschillende opties voor het onderhouden van een beveiligde configuratie voor Azure Virtual Machines (VM) voor implementatie:

1. Azure Resource Manager sjablonen: dit zijn JSON-bestanden die worden gebruikt voor het implementeren van een virtuele Azure Portal en aangepaste sjablonen moeten worden onderhouden. Microsoft voert het onderhoud uit op basissjablonen.

2. Aangepaste virtuele harde schijf (VHD): in sommige gevallen kan het nodig zijn om aangepaste VHD-bestanden te gebruiken, zoals bij het omgaan met complexe omgevingen die niet op een andere manier kunnen worden beheerd.

3. Azure Automation State Configuration: Zodra het basisbesturingssysteem is geïmplementeerd, kan dit worden gebruikt voor een gedetailleerdere controle van de instellingen en worden afgedwongen via het automatiseringsraamwerk.

In de meeste scenario's kunnen de Microsoft-basis-VM-sjablonen in combinatie Azure Automation Desired State Configuration helpen bij het voldoen aan en onderhouden van de beveiligingsvereisten.

- [Informatie over het downloaden van de VM-sjabloon](/previous-versions/azure/virtual-machines/windows/download-template)

- [Informatie over het maken van ARM-sjablonen](../virtual-machines/windows/ps-template.md)

- [Een aangepaste VM-VHD uploaden naar Azure](../virtual-machines/windows/upload-generalized-managed.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Voor waarschuwingen met betrekking tot dit besturingselement is mogelijk een [Azure Defender](/azure/security-center/azure-defender) nodig voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 7.4](../../includes/policy/standards/asb/rp-controls/microsoft.compute-7-4.md)]

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuratie van Azure-resources veilig opslaan

**Richtlijnen:** Gebruik Azure DevOps om uw code veilig op te slaan en te beheren, zoals aangepaste Azure-beleidsregels, Azure Resource Manager-sjablonen, Desired State Configuration scripts, enzovoort.  Voor toegang tot de resources die u beheert in Azure DevOps, zoals uw code, builds en het bijhouden van werk, moet u machtigingen hebben voor deze specifieke resources. De meeste machtigingen worden verleend via ingebouwde beveiligingsgroepen, zoals beschreven in Machtigingen en toegang. U kunt machtigingen verlenen of weigeren aan specifieke gebruikers, ingebouwde beveiligingsgroepen of groepen die zijn gedefinieerd in Azure Active Directory (Azure AD) als deze zijn geïntegreerd met Azure DevOps of Active Directory als deze zijn geïntegreerd met TFS.

- [Code opslaan in Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Over machtigingen en groepen in Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Veilige opslag van aangepaste besturingssysteeminstallatie

**Richtlijnen:** als u aangepaste afbeeldingen (bijvoorbeeld virtuele harde schijf) gebruikt, gebruikt u op rollen gebaseerd toegangsbesturingselementen van Azure om ervoor te zorgen dat alleen gemachtigde gebruikers toegang hebben tot de afbeeldingen.  

- [RBAC in Azure begrijpen](../role-based-access-control/rbac-and-directory-admin-roles.md)

- [RBAC configureren in Azure](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Hulpprogramma's voor configuratiebeheer implementeren voor Azure-resources

**Richtlijnen:** gebruik Azure Policy om systeemconfiguraties voor uw virtuele machines te waarschuwen, controleren en afdwingen. Daarnaast ontwikkelt u een proces en pijplijn voor het beheren van beleids uitzonderingen.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Hulpprogramma's voor configuratiebeheer implementeren voor besturingssystemen

**Richtlijnen:** Azure Automation State Configuration is een configuratiebeheerservice voor Desired State Configuration knooppunten (DSC) in een cloud of on-premises datacenter. Het maakt schaalbaarheid op duizenden computers snel en eenvoudig mogelijk vanaf een centrale, veilige locatie. U kunt eenvoudig machines onboarden, declaratieve configuraties toewijzen en rapporten weergeven waarin wordt weergegeven dat elke machine voldoet aan de gewenste status die u hebt opgegeven. 

- [Machines onboarden voor beheer door Azure Automation State Configuration](../automation/automation-dsc-onboarding.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Geautomatiseerde configuratiebewaking implementeren voor Azure-resources

**Richtlijnen:** Maak gebruik Azure Security Center basislijnscans uit te voeren voor uw virtuele Azure-machines.  Aanvullende methoden voor geautomatiseerde configuratie zijn het gebruik Azure Automation State Configuration.

- [Aanbevelingen herstellen in Azure Security Center](../security-center/security-center-remediate-recommendations.md)

- [Aan de slag met Azure Automation State Configuration](../automation/automation-dsc-getting-started.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Geautomatiseerde configuratiebewaking implementeren voor besturingssystemen

**Richtlijnen:** Azure Automation State Configuration is een configuratiebeheerservice voor Desired State Configuration (DSC)-knooppunten in een cloud- of on-premises datacenter. Het maakt schaalbaarheid op duizenden computers snel en eenvoudig mogelijk vanaf een centrale, veilige locatie. U kunt eenvoudig machines onboarden, declaratieve configuraties toewijzen en rapporten weergeven waarin wordt weergegeven dat elke machine voldoet aan de gewenste status die u hebt opgegeven.

- [Machines onboarden voor beheer door Azure Automation State Configuration](../automation/automation-dsc-onboarding.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Waarschuwingen met betrekking tot dit besturingselement vereisen mogelijk een [Azure Defender](/azure/security-center/azure-defender) voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 7.10](../../includes/policy/standards/asb/rp-controls/microsoft.compute-7-10.md)]

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure-geheimen veilig beheren

**Richtlijnen:** Gebruik Managed Service Identity in combinatie met Azure Key Vault om het beheer van geheimen voor uw cloudtoepassingen te vereenvoudigen en te beveiligen.

- [Integreren met beheerde Azure-identiteiten](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Een Key Vault](../key-vault/general/quick-create-portal.md)

- [Verificatie bij Key Vault](../key-vault/general/authentication.md)

- [Een toegangsbeleid voor Key Vault toewijzen](../key-vault/general/assign-access-policy-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Identiteiten veilig en automatisch beheren

**Richtlijnen:** Beheerde identiteiten gebruiken om Azure-services te voorzien van een automatisch beheerde identiteit in Azure Active Directory (Azure AD). Met beheerde identiteiten kunt u zich verifiëren bij elke service die ondersteuning biedt voor Azure AD-verificatie, Key Vault, zonder referenties in uw code.

- [Beheerde identiteiten configureren](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Onbedoelde referentieblootstelling voorkomen

**Richtlijnen:** Referentiescanner implementeren om referenties in code te identificeren. Door het gebruik van Credential Scanner worden gebruikers ook aangemoedigd om gedetecteerde referenties naar veiligere locaties, zoals Azure Key Vault, te verplaatsen.

- [Referentiescanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie de Azure [Security Benchmark: Malware Defense voor meer informatie.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Centraal beheerde antimalwaresoftware gebruiken

**Richtlijnen:** Gebruik Microsoft Antimalware virtuele Azure Windows-machines om uw resources continu te bewaken en te beschermen.  U hebt een hulpprogramma van derden nodig voor antimalwarebeveiliging in de virtuele Linux-machine van Azure. 

- [Een Microsoft Antimalware configureren voor Cloud Services en Virtual Machines](../security/fundamentals/antimalware.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Waarschuwingen met betrekking tot dit besturingselement vereisen mogelijk een [Azure Defender](/azure/security-center/azure-defender) voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 8.1](../../includes/policy/standards/asb/rp-controls/microsoft.compute-8-1.md)]

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Ervoor zorgen dat antimalwaresoftware en handtekeningen worden bijgewerkt

**Richtlijnen:** wanneer deze worden geïmplementeerd voor virtuele Windows-machines, Microsoft Antimalware voor Azure automatisch de meest recente handtekening-, platform- en engine-updates standaard geïnstalleerd. Volg de aanbevelingen in Azure Security Center: 'Compute-apps' om ervoor te zorgen dat alle eindpunten zijn bijgewerkt met &amp; de nieuwste handtekeningen. Het Windows-besturingssysteem kan verder worden beveiligd met extra beveiliging om het risico op virus- of malwareaanvallen te beperken met de Microsoft Defender Advanced Threat Protection-service die is geïntegreerd met Azure Security Center.

U hebt een hulpprogramma van derden nodig voor antimalwarebeveiliging in azure Linux Virtual Machine. 

- [Een Microsoft Antimalware implementeren voor Azure Cloud Services en Virtual Machines](../security/fundamentals/antimalware.md)

- [Microsoft Defender Advanced Threat Protection](/windows/security/threat-protection/microsoft-defender-atp/onboard-configure) 

- [Een Microsoft Antimalware configureren voor Cloud Services en Virtual Machines](../virtual-machines/security-recommendations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Waarschuwingen met betrekking tot dit besturingselement vereisen mogelijk een [Azure Defender](/azure/security-center/azure-defender) voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 8.3](../../includes/policy/standards/asb/rp-controls/microsoft.compute-8-3.md)]

## <a name="data-recovery"></a>Gegevensherstel

*Zie de Azure [Security Benchmark: Data Recovery voor meer informatie.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Regelmatige automatische back-ups garanderen

**Richtlijnen:** Maak een momentopname van het exemplaar van de virtuele-machineschaalset van Azure of de beheerde schijf die aan het exemplaar is gekoppeld met behulp van PowerShell- of REST API's.  U kunt ook Azure Automation back-upscripts met regelmatige tussenpozen uit te voeren.

- [Een momentopname maken van een exemplaar van een virtuele-machineschaalset en beheerde schijf](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-faq#how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance) 

- [Inleiding tot Azure Automation](../automation/automation-intro.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor de aanbevelingen Security Center van [Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Voor waarschuwingen met betrekking tot dit besturingselement [is mogelijk](/azure/security-center/azure-defender) een Azure Defender nodig voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 9.1](../../includes/policy/standards/asb/rp-controls/microsoft.compute-9-1.md)]

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Volledige systeemback-ups uitvoeren en back-ups maken van door de klant beheerde sleutels

**Richtlijnen:** maak momentopnamen van uw virtuele Azure-machines of de beheerde schijven die zijn gekoppeld aan deze exemplaren met behulp van PowerShell of REST API's. Back-up maken van door de klant beheerde sleutels binnen Azure Key Vault.

Schakel Azure Backup azure-Virtual Machines (VM) in, evenals de gewenste frequentie en bewaarperioden. Dit omvat een volledige back-up van de systeemtoestand. Als u Azure-schijfversleuteling gebruikt, wordt de back-up van door de klant beheerde sleutels automatisch door Azure VM backup verwerkt.

- [Back-ups maken op Azure-VM's die gebruikmaken van versleuteling](../backup/backup-azure-vms-encryption.md)

- [Overzicht van back-ups van azure-VM's](../backup/backup-azure-vms-introduction.md)

- [Een momentopname maken van een exemplaar van een virtuele-machineschaalset en beheerde schijf](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-faq#how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance)

- [Een back-up maken van sleutelkluissleutels in Azure](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Waarschuwingen met betrekking tot dit besturingselement vereisen mogelijk een [Azure Defender](/azure/security-center/azure-defender) voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Compute:**

[!INCLUDE [Resource Policy for Microsoft.Compute 9.2](../../includes/policy/standards/asb/rp-controls/microsoft.compute-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richtlijnen:** zorg ervoor dat u periodiek gegevensherstel kunt uitvoeren op een beheerde schijf binnen Azure Backup. Test indien nodig inhoud herstellen naar een geïsoleerd virtueel netwerk of abonnement. De klant moet het herstel testen van door de klant beheerde back-upsleutels.

Als u Azure-schijfversleuteling gebruikt, kunt u uw virtuele-machineschaalsets herstellen met de schijfversleutelingssleutels. Wanneer u schijfversleuteling gebruikt, kunt u de Azure-VM herstellen met de schijfversleutelingssleutels.

- [Back-ups maken op Azure-VM's die gebruikmaken van versleuteling](../backup/backup-azure-vms-encryption.md)

- [Een schijf herstellen en een herstelde VM maken in Azure](../backup/tutorial-restore-disk.md)

- [Sleutelkluissleutels herstellen in Azure](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

- [Schijfversleuteling inschakelen voor Azure Virtual Machine Scale Sets](disk-encryption-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Beveiliging van back-ups en door de klant beheerde sleutels garanderen

**Richtlijnen:** beveiliging tegen verwijderen inschakelen voor beheerde schijven met behulp van vergrendelingen. Schakel beveiliging Soft-Delete en ops manier in Key Vault om sleutels te beveiligen tegen onbedoelde of schadelijke verwijdering.  

- [Resources vergrendelen om onverwachte wijzigingen te voorkomen](../azure-resource-manager/management/lock-resources.md)

- [Azure Key Vault overzicht van de beveiliging tegen soft-delete en opsting](../key-vault/general/soft-delete-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Benchmark: respons op incidenten](../security/benchmarks/security-control-incident-response.md) voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10.1: Een handleiding voor het reageren op incidenten maken

**Richtlijnen**: Stel voor uw organisatie een responshandleiding op voor gebruik bij incidenten. Zorg ervoor dat er schriftelijke responsplannen zijn waarin alle rollen van het personeel worden gedefinieerd, evenals alle fasen in het afhandelen/managen van incidenten, vanaf de detectie van het incident tot een evaluatie ervan achteraf.  

- [Richtlijnen voor het bouwen van uw eigen proces voor het reageren op beveiligingsincidents](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center van een incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Gebruik de computerbeveiligingsincidentafhandelingshandleiding van NIST om u te helpen bij het maken van uw eigen plan voor het reageren op incidenten](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Een procedure voor het scoren en prioriteren van incidenten maken

**Richtlijnen:** Security Center aan elke waarschuwing een ernst toe te wijzen om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op hoe zeker Security Center is in het zoeken of de metrische gegevens die worden gebruikt om de waarschuwing uit te geven, evenals het betrouwbaarheidsniveau dat er een kwaadwillende intentie is achter de activiteit die heeft geleid tot de waarschuwing. 

Daarnaast moet u abonnementen duidelijk markeren (bijvoorbeeld productie, niet-prod) met behulp van tags en maak een naamgevingssysteem om Azure-resources duidelijk te identificeren en te categoriseren, met name de resources die gevoelige gegevens verwerken.  Het is uw verantwoordelijkheid om prioriteit te geven aan het oplossen van waarschuwingen op basis van de ernst van de Azure-resources en -omgeving waarin het incident heeft plaatsgevonden.

- [Beveiligingswaarschuwingen in Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Tags gebruiken om Azure-resources te organiseren](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="103-test-security-response-procedures"></a>10.3: Procedures voor het reageren op beveiliging testen

**Richtlijnen:** oefeningen uitvoeren om de mogelijkheden voor het reageren op incidenten van uw systemen regelmatig te testen om uw Azure-resources te beschermen. Stel vast waar zich zwakke plekken en hiaten bevinden, en wijzig zo nodig het plan.

- [Publicatie van NIST - Handleiding voor programma's voor testen, training en oefeningen voor IT-plannen en -mogelijkheden](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Contactgegevens voor beveiligingsincidenten verstrekken en waarschuwingsmeldingen voor beveiligingsincidenten configureren

**Richtlijnen:** Contactgegevens voor beveiligingsincidenten worden door Microsoft gebruikt om contact met u op te nemen als de Microsoft Security Response Center (MSRC) detecteert dat uw gegevens zijn gebruikt door een onrechtmatige of niet-geautoriseerde partij. Bekijk incidenten na het feit om ervoor te zorgen dat problemen worden opgelost.

- [De beveiligingscontactcontacte Azure Security Center instellen](../security-center/security-center-provide-security-contact-details.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Beveiligingswaarschuwingen opnemen in uw systeem voor het reageren op incidenten

**Richtlijnen:** exporteert uw Azure Security Center en aanbevelingen met behulp van de functie Continue export om risico's voor Azure-resources te identificeren. Met continue export kunt u waarschuwingen en aanbevelingen handmatig of doorlopend exporteren. U kunt de connector Azure Security Center gebruiken om de waarschuwingen te streamen naar Azure Sentinel.

- [Continue export configureren](../security-center/continuous-export.md)

- [Waarschuwingen streamen naar Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: De reactie op beveiligingswaarschuwingen automatiseren

**Richtlijnen:** gebruik de functie Werkstroomautomatisering in Azure Security Center automatisch reacties te activeren via 'Logic Apps' voor beveiligingswaarschuwingen en aanbevelingen voor het beveiligen van uw Azure-resources. 

- [Werkstroomautomatisering en -Logic Apps](../security-center/workflow-automation.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie de Azure [Security Benchmark: Penetration Tests en Red Team Exercises (Penetratietests en Red Team-oefeningen) voor meer informatie.](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Regelmatige penetratietests uitvoeren van uw Azure-resources en zorgen voor herstel van alle kritieke beveiligings bevindingen

**Richtlijnen:** volg de Microsoft-regels voor betrokkenheid om ervoor te zorgen dat uw penetratietests niet in strijd zijn met het Microsoft-beleid. Gebruik de strategie en uitvoering van Microsoft voor red teaming en penetratietests van live-site tegen door Microsoft beheerde cloudinfrastructuur, -services en -toepassingen.

- [Regels voor het inzetten van penetratietests](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

## <a name="next-steps"></a>Volgende stappen

- Zie [Overzicht Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Meer informatie over [Azure-beveiligingsbasislijnen](/azure/security/benchmarks/security-baselines-overview)
