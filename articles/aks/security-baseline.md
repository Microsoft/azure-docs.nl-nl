---
title: Azure-beveiligingsbasislijn voor Azure Kubernetes Service
description: De Azure Kubernetes Service beveiligingsbasislijn biedt procedurele richtlijnen en resources voor het implementeren van de beveiligingsaanbevelingen die zijn opgegeven in de Azure Security Benchmark.
author: msmbaldwin
ms.service: container-service
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark, devx-track-azurepowershell
ms.openlocfilehash: 0564f1f39ac9d492dfffdf0e7adacdde08db0874
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769585"
---
# <a name="azure-security-baseline-for-azure-kubernetes-service"></a>Azure-beveiligingsbasislijn voor Azure Kubernetes Service

Deze beveiligingsbasislijn past richtlijnen van [azure Security Benchmark versie 1.0](../security/benchmarks/overview-v1.md) toe op Azure Kubernetes. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen. De inhoud wordt gegroepeerd op basis van de beveiligingscontroles die **zijn** gedefinieerd door de Azure Security-benchmark en de gerelateerde richtlijnen die van toepassing zijn op Azure Kubernetes. **Besturingselementen** die niet van toepassing zijn op Azure Kubernetes of waarvoor de verantwoordelijkheid van Microsoft is, zijn uitgesloten.

Zie het volledige toewijzingsbestand voor azure [Kubernetes-beveiligingsbasislijnen](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)om te zien hoe Azure Kubernetes volledig is toegewezen aan de Azure Security Benchmark.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Benchmark: netwerkbeveiliging](../security/benchmarks/security-control-network-security.md) voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Azure-resources binnen virtuele netwerken beveiligen

**Richtlijnen:** standaard worden er automatisch een netwerkbeveiligingsgroep en routetabel gemaakt met het maken van een Microsoft Azure Kubernetes Service-cluster (AKS). AKS wijzigt netwerkbeveiligingsgroepen automatisch voor de juiste verkeersstroom wanneer services worden gemaakt met load balancers, poorttoewijzingen of toegangsroute. De netwerkbeveiligingsgroep wordt automatisch gekoppeld aan de virtuele NIC's op klantknooppunten en de routetabel met het subnet in het virtuele netwerk. 

Gebruik AKS-netwerkbeleid om netwerkverkeer te beperken door regels te definiëren voor in- en uitgaande verkeer tussen Linux-pods in een cluster op basis van de keuze van naamruimten en label selectors. Gebruik van netwerkbeleid vereist de Azure CNI-inzender met gedefinieerd virtueel netwerk en subnetten en kan alleen worden ingeschakeld bij het maken van het cluster. Ze kunnen niet worden geïmplementeerd op een bestaand AKS-cluster.

U kunt een privé-AKS-cluster implementeren om ervoor te zorgen dat netwerkverkeer tussen uw AKS API-server en knooppuntgroepen alleen in het particuliere netwerk blijft. Het besturingsvlak of de API-server bevindt zich in een door AKS beheerd Azure-abonnement en maakt gebruik van interne IP-adressen (RFC1918), terwijl het cluster of de knooppuntgroep van de klant zich in hun eigen abonnement bevindt. De server en de cluster- of knooppuntgroep communiceren met elkaar met behulp van de Azure Private Link-service in het virtuele netwerk van de API-server en een privé-eindpunt dat beschikbaar is in het subnet van het AKS-cluster van de klant.  U kunt ook een openbaar eindpunt gebruiken voor de AKS API-server, maar de toegang beperken met de functie Geautoriseerde IP-bereiken van de AKS API-server. 

- [Beveiligingsconcepten voor toepassingen en clusters in Azure Kubernetes Service (AKS)](concepts-security.md)

- [Verkeer tussen pods beveiligen met behulp van netwerkbeleid in Azure Kubernetes Service (AKS)](use-network-policies.md)

- [Een privé-Azure Kubernetes Service maken](private-clusters.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Waarschuwingen met betrekking tot dit besturingselement vereisen mogelijk een [Azure Defender](/azure/security-center/azure-defender) voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.ContainerService**:

[!INCLUDE [Resource Policy for Microsoft.ContainerService 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.containerservice-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: De configuratie en het verkeer van virtuele netwerken, subnetten en NIC's bewaken en in een logboek plaatsen

**Richtlijnen:** gebruik Security Center en volg de aanbevelingen voor netwerkbeveiliging om de netwerkbronnen te beveiligen die worden gebruikt door uw Azure Kubernetes Service (AKS)-clusters. 

Schakel stroomlogboeken voor netwerkbeveiligingsgroep in en verzend de logboeken naar een Azure Storage voor controle. U kunt de stroomlogboeken ook verzenden naar een Log Analytics-werkruimte en vervolgens Traffic Analytics gebruiken om inzicht te krijgen in verkeerspatronen in uw Azure-cloud om netwerkactiviteiten te visualiseren, hotspots en beveiligingsrisico's te identificeren, verkeersstroompatronen te begrijpen en onjuiste netwerkconfiguraties aan te wijzen.

- [Informatie over netwerkbeveiliging die wordt geleverd door Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Logboeken voor netwerkbeveiligingsstromen inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Informatie over het inschakelen en gebruiken van Traffic Analytics](../network-watcher/traffic-analytics.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="13-protect-critical-web-applications"></a>1.3: Kritieke webtoepassingen beveiligen

**Richtlijnen:** gebruik een Azure Application Gateway ingeschakelde Web Application Firewall (WAF) vóór een AKS-cluster om een extra beveiligingslaag te bieden door het binnenkomende verkeer naar uw webtoepassingen te filteren. Azure WAF maakt gebruik van een set regels, geleverd door The Open Web Application Security Project (OWASP), voor aanvallen, zoals cross-site scripting of cookie-versonsoning tegen dit verkeer. 

Gebruik een API-gateway voor verificatie, autorisatie, beperking, caching, transformatie en bewaking voor API's die worden gebruikt in uw AKS-omgeving. Een API-gateway fungeert als een front door aan de microservices, ontkoppelt clients van uw microservices en vermindert de complexiteit van uw microservices door de last van het afhandelen van cross-cutting problemen weg te nemen.

- [Best practices voor netwerkconnectiviteit en -beveiliging in AKS begrijpen](operator-best-practices-network.md)

- [Application Gateway Ingress Controller ](../application-gateway/ingress-controller-overview.md)

- [Azure-API Management gebruiken met microservices die zijn geïmplementeerd in Azure Kubernetes Service](../api-management/api-management-kubernetes.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Communicatie met bekende schadelijke IP-adressen weigeren

**Richtlijnen:** DDoS (Distributed Denial of Service) Standard-beveiliging van Microsoft inschakelen op de virtuele netwerken waar Azure Kubernetes Service-onderdelen (AKS) worden geïmplementeerd voor beveiliging tegen DDoS-aanvallen.

Installeer de engine voor netwerkbeleid en maak Kubernetes-netwerkbeleid om de verkeersstroom tussen pods in AKS te controleren, omdat standaard al het verkeer tussen deze pods is toegestaan. Netwerkbeleid mag alleen worden gebruikt voor Linux-knooppunten en -pods in AKS. Regels definiëren die podcommunicatie beperken voor verbeterde beveiliging. 

Kies ervoor om verkeer toe te staan of te weigeren op basis van instellingen zoals toegewezen labels, naamruimte of verkeerspoort. Het vereiste netwerkbeleid kan automatisch worden toegepast wanneer pods dynamisch worden gemaakt in een AKS-cluster. 

- [Verkeer tussen pods beveiligen met behulp van netwerkbeleid in Azure Kubernetes Service (AKS)](use-network-policies.md)

- [DDoS-beveiliging configureren](../ddos-protection/manage-ddos-protection.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="15-record-network-packets"></a>1.5: Netwerkpakketten registreren

**Richtlijnen:** gebruik Network Watcher pakketopname zoals vereist voor het onderzoeken van afwijkende activiteiten. 

Network Watcher wordt automatisch ingeschakeld in de regio van uw virtuele netwerk wanneer u een virtueel netwerk in uw abonnement maakt of bij werkt. U kunt ook nieuwe exemplaren van Network Watcher maken met behulp van PowerShell, de Azure CLI, de REST API of de Azure Resource Manager Client-methode

- [Het inschakelen van Network Watcher](../network-watcher/network-watcher-create.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Op het netwerk gebaseerde inbraakdetectie-/inbraakpreventiesystemen (IDS/IPS) implementeren

**Richtlijnen:** beveilig Azure Kubernetes Service AKS-cluster (AKS) met een Azure Application Gateway ingeschakeld met een Web Application Firewall (WAF). 

Als inbraakdetectie en/of -preventie op basis van payloadinspectie of gedragsanalyse geen vereiste is, kan een Azure Application Gateway met WAF worden gebruikt en geconfigureerd in de 'detectiemodus' voor het vastleggen van waarschuwingen en bedreigingen, of 'preventiemodus' om gedetecteerde indringers en aanvallen actief te blokkeren.

- [Informatie over best practices voor het beveiligen van uw AKS-cluster met een WAF](https://docs.microsoft.com/azure/aks/operator-best-practices-network#secure-traffic-with-a-web-application-firewall-waf)

- [Een Azure Application Gateway (Azure WAF)](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Complexiteit en administratieve overhead van netwerkbeveiligingsregels minimaliseren

**Richtlijnen:** Gebruik servicetags voor virtuele netwerken om netwerktoegangsbesturingselementen te definiëren voor netwerkbeveiligingsgroepen die zijn gekoppeld Azure Kubernetes Service AKS-exemplaren. Servicetags kunnen worden gebruikt in plaats van specifieke IP-adressen bij het maken van beveiligingsregels om het verkeer voor de bijbehorende service toe te staan of te weigeren door de naam van de servicetag op te geven. 

Microsoft beheert de adres-voorvoegsels die door de servicetag worden omvat en werkt de servicetag automatisch bij wanneer adressen worden gewijzigd.

Pas een Azure-tag toe op knooppuntgroepen in uw AKS-cluster. Ze verschillen van de servicetags van het virtuele netwerk en worden toegepast op elk knooppunt in de knooppuntgroep en blijven bestaan via upgrades. 

- [Servicetags begrijpen en gebruiken](../virtual-network/service-tags-overview.md)

- [NSG's voor AKS begrijpen](support-policies.md)

- [Beheer het verkeer van het verkeer voor clusterknooppunten in Azure Kubernetes Service (AKS)](limit-egress-traffic.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Standaardbeveiligingsconfiguraties voor netwerkapparaten onderhouden

**Richtlijnen:** standaardbeveiligingsconfiguraties definiëren en implementeren met Azure Policy voor netwerkbronnen die zijn gekoppeld aan uw Azure Kubernetes Service(AKS)-clusters. 

Gebruik Azure Policy aliassen in de naamruimten Microsoft.ContainerService en Microsoft.Network om aangepaste beleidsregels te maken om de netwerkconfiguratie van uw AKS-clusters te controleren of af te dwingen. 

Gebruik ook ingebouwde beleidsdefinities die betrekking hebben op AKS, zoals:

- Geautoriseerde IP-bereiken moeten worden gedefinieerd voor Kubernetes Services

- Inkomend HTTPS-verkeer afdwingen in Kubernetes-cluster

- Garanderen dat services alleen toegestane poorten kunnen controleren in een Kubernetes-cluster

Aanvullende informatie is beschikbaar via de koppelingen waarnaar wordt verwezen.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy voorbeelden voor netwerken](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="110-document-traffic-configuration-rules"></a>1.10: Configuratieregels voor verkeer documenteren

**Richtlijnen:** Tags gebruiken voor netwerkbeveiligingsgroepen en andere resources voor het verkeer van en naar Azure Kubernetes Service (AKS)-clusters. Gebruik het veld Beschrijving voor afzonderlijke regels voor netwerkbeveiligingsgroep om bedrijfseen/of duur, en meer, op te geven voor regels die verkeer van/naar een netwerk toestaan.

Gebruik een van de ingebouwde Azure Policy-gerelateerde definities voor taggen, bijvoorbeeld Tag en waarde vereisen, die ervoor zorgen dat alle resources worden gemaakt met tags en meldingen ontvangen voor bestaande resources zonder tag.

Kies ervoor om specifieke netwerkpaden binnen het cluster toe te staan of te weigeren op basis van naamruimten en label selectors met netwerkbeleid. Gebruik deze naamruimten en labels als descriptors voor configuratieregels voor verkeer. Gebruik Azure PowerShell azure-opdrachtregelinterface (CLI) om resources op te zoeken of acties uit te voeren op basis van hun tags.

- [Azure Policy with CLI](/cli/azure/policy)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

- [Een NSG maken met een beveiligings config](../virtual-network/tutorial-filter-network-traffic.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Geautomatiseerde hulpprogramma's gebruiken om netwerkresourceconfiguraties te bewaken en wijzigingen te detecteren

**Richtlijnen:** Azure-activiteitenlogboek gebruiken om configuraties van netwerkresources te bewaken en wijzigingen te detecteren voor netwerkresources met betrekking tot Azure Kubernetes Service (AKS)-clusters. 

Maak waarschuwingen binnen Azure Monitor die worden getriggerd wanneer er wijzigingen in kritieke netwerkbronnen plaatsvinden. Alle vermeldingen van de AzureContainerService-gebruiker in de activiteitenlogboeken worden geregistreerd als platformacties. 

Gebruik Azure Monitor logboeken om de logboeken van AKS, de hoofdonderdelen kube-apiserver en kube-controller-manager in teschakelen en er query's op uit te voeren. Maak en beheer de knooppunten die de kubelet uitvoeren met containerruntime en implementeer hun toepassingen via de beheerde Kubernetes API-server. 

- [Azure-activiteitenlogboekgebeurtenissen weergeven en ophalen](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Waarschuwingen maken in Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

- [Logboeken van Kubernetes-hoofdknooppunten inschakelen en controleren in AKS (Azure Kubernetes Service)](/azure/aks/view-master-logs)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie de Azure [Security Benchmark: Logging and Monitoring (Azure Security Benchmark: logboekregistratie en bewaking) voor meer informatie.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Goedgekeurde tijdsynchronisatiebronnen gebruiken

**Richtlijnen:** Azure Kubernetes Service knooppunten (AKS) gebruiken ntp.ubuntu.com voor tijdsynchronisatie, samen met UDP-poort 123 en Network Time Protocol (NTP). 

Zorg ervoor dat NTP-servers toegankelijk zijn voor de clusterknooppunten als u aangepaste DNS-servers gebruikt. 

- [Inzicht in NTP-domein- en poortvereisten voor AKS-clusterknooppunten](limit-egress-traffic.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

### <a name="22-configure-central-security-log-management"></a>2.2: Centraal beheer van beveiligingslogboek configureren

**Richtlijnen:** Schakel auditlogboeken in van hoofdonderdelen van Azure Kubernetes Services (AKS), kube-apiserver en kube-controller-manager, die worden geleverd als een beheerde service. 

- kube-auditaksService: De weergavenaam in het auditlogboek voor de besturingsvlakbewerking (van de hcpService) 

- masterclient: De weergavenaam in het auditlogboek voor MasterClientCertificate, het certificaat dat u krijgt van az aks get-credentials 

- nodeclient: de weergavenaam voor ClientCertificate, die wordt gebruikt door agentknooppunten

Schakel ook andere auditlogboeken in, zoals kube-audit. 

Exporteert deze logboeken naar Log Analytics of een ander opslagplatform. Gebruik Azure Monitor Log Analytics-werkruimten om query's uit te voeren en analyses uit te voeren en gebruik Azure Storage accounts voor langetermijn- en archiveringsopslag.

Schakel deze gegevens in en gebruik deze om Azure Sentinel siem van derden te maken op basis van de zakelijke vereisten van uw organisatie.

- [Bekijk hier het logboekschema met logboekrollen](/azure/aks/view-master-logs)

- [Meer Azure Monitor voor containers](/azure/azure-monitor/insights/container-insights-overview)

- [Het inschakelen Azure Monitor voor containers](/azure/azure-monitor/insights/container-insights-onboard)

- [Logboeken van Kubernetes-hoofdknooppunten inschakelen en controleren in AKS (Azure Kubernetes Service)](/azure/aks/view-master-logs)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Auditlogregistratie voor Azure-resources inschakelen

**Richtlijnen:** Gebruik activiteitenlogboeken om acties op Azure Kubernetes Service resources (AKS) te controleren om alle activiteiten en hun status weer te geven. Bepaal met activiteitenlogboeken welke bewerkingen zijn uitgevoerd op de resources in uw abonnement: 

- wie de bewerking heeft gestart
- wanneer de bewerking heeft plaatsgevonden
- de status van de bewerking
- de waarden van andere eigenschappen die u kunnen helpen bij het onderzoeken van de bewerking

Haal informatie op uit het activiteitenlogboek via Azure PowerShell, de Azure-opdrachtregelinterface (CLI), de Azure REST API of de Azure Portal. 

Schakel auditlogboeken in op AKS-hoofdonderdelen, zoals: 

- kube-auditaksService: de weergavenaam in het auditlogboek voor de besturingsvlakbewerking (van de hcpService) 

- masterclient: De weergavenaam in het auditlogboek voor MasterClientCertificate, het certificaat dat u krijgt van az aks get-credentials 

- nodeclient: de weergavenaam voor ClientCertificate, die wordt gebruikt door agentknooppunten

Schakel ook andere auditlogboeken in, zoals kube-audit. 

- [Logboeken van Kubernetes-hoofd-knooppunt inschakelen en controleren in AKS](/azure/aks/view-master-logs)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Beveiligingslogboeken verzamelen van besturingssystemen

**Richtlijnen:** Schakel automatische installatie van Log Analytics-agents in voor het verzamelen van gegevens van de AKS-clusterknooppunten. Automatische inrichting van de Azure Log Analytics Monitoring Agent vanuit de Azure Security Center is standaard uitgeschakeld. De agent kan ook handmatig worden geïnstalleerd. Als automatische inrichting is Security Center implementeert u de Log Analytics-agent op alle ondersteunde Virtuele Azure-VM's en op nieuwe VM's die worden gemaakt. Security Center verzamelt gegevens van Azure Virtual Machines (VM), virtuele-machineschaalsets en IaaS-containers, zoals Kubernetes-clusterknooppunten, om te controleren op beveiligingsproblemen en bedreigingen. Gegevens worden verzameld met behulp van de Azure Log Analytics-agent, die verschillende configuraties en gebeurtenislogboeken met betrekking tot beveiliging van de computer leest en de gegevens kopieert naar uw werkruimte voor analyse. 

Gegevensverzameling is vereist om inzicht te bieden in ontbrekende updates, onjuist geconfigureerde beveiligingsinstellingen van het besturingssysteem, endpoint protection-status en status- en bedreigingsdetecties.

- [Automatische inrichting van de Log Analytics-agent inschakelen](../security-center/security-center-enable-data-collection.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

### <a name="25-configure-security-log-storage-retention"></a>2.5: Opslagretentie van beveiligingslogboek configureren

**Richtlijnen:** Onboard uw Azure Kubernetes Service-exemplaren (AKS) naar Azure Monitor en stel de bijbehorende azure Log Analytics-werkruimteretentieperiode in op basis van de nalevingsvereisten van uw organisatie. 

- [Retentieparameters voor logboeken instellen voor Log Analytics-werkruimten](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="26-monitor-and-review-logs"></a>2.6: Logboeken bewaken en controleren

**Richtlijnen:** onboard uw AKS Azure Kubernetes Service s (AKS)-exemplaren voor het Azure Monitor configureren van diagnostische instellingen voor uw cluster. 

Gebruik Azure Monitor Log Analytics-werkruimte om logboeken te controleren en query's uit te voeren op logboekgegevens. Azure Monitor-logboeken worden ingeschakeld en beheerd in de Azure Portal of via CLI en werken met kubernetes op rollen gebaseerd toegangsbeheer (Kubernetes RBAC), Azure RBAC- en niet-RBAC-clusters.

Bekijk de logboeken die zijn gegenereerd door AKS-hoofdonderdelen (kube-apiserver en kube-controllermanager) voor het oplossen van problemen met uw toepassing en services. Gegevens inschakelen en in gebruik nemen Azure Sentinel of een SIEM van derden voor gecentraliseerd logboekbeheer en -bewaking.

- [Logboeken van Kubernetes-hoofd-knooppunt inschakelen en controleren in AKS](/azure/aks/view-master-logs)

- [Onboarding van Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Aangepaste query's uitvoeren in Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Waarschuwingen inschakelen voor afwijkende activiteiten

**Richtlijnen:** gebruik Azure Kubernetes Service (AKS) samen met Security Center om meer inzicht te krijgen in AKS-knooppunten. 

Bekijk Security Center waarschuwingen over bedreigingen en schadelijke activiteiten die zijn gedetecteerd op host- en clusterniveau. Security Center implementeert continue analyse van onbewerkte beveiligingsgebeurtenissen die plaatsvinden in een AKS-cluster, zoals netwerkgegevens, het maken van processen en het Kubernetes-auditlogboek. Bepaal of deze activiteit verwacht gedrag vertoont of dat de app niet goed werkt. Gebruik metrische gegevens en logboeken in Azure Monitor om uw bevindingen te onderbouwen. 

- [Azure Kubernetes Services-integratie met Security Center](../security-center/defender-for-kubernetes-introduction.md)

- [Een Standard Azure Security Center laag inschakelen](../security-center/security-center-get-started.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="28-centralize-anti-malware-logging"></a>2.8: Antimalwarelogregistratie centraliseren

**Richtlijnen:** Microsoft Antimalware voor Azure Kubernetes Service Azure installeren en inschakelen om virtuele AKS-machines (AKS) en virtuele-machineschaalsetknooppunten te installeren en in te stellen. Bekijk waarschuwingen in Security Center voor herstel.

- [Microsoft Antimalware voor Azure Cloud Services en Virtual Machines](../security/fundamentals/antimalware.md)

- [Referentiehandleiding voor beveiligingswaarschuwingen](../security-center/alerts-reference.md)

- [Waarschuwingen voor containers - Azure Kubernetes Service clusters](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="29-enable-dns-query-logging"></a>2.9: Logboekregistratie van DNS-query's inschakelen

**Richtlijnen:** Azure Kubernetes Service (AKS) maakt gebruik van het CoreDNS-project voor cluster-DNS-beheer en -resolutie.

Schakel logboekregistratie van DNS-query's in door gedocumenteerde configuratie toe te passen in uw coredns-custom ConfigMap. 

- [CoreDNS aanpassen met Azure Kubernetes Service](coredns-custom.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="210-enable-command-line-audit-logging"></a>2.10: Controlelogregistratie via opdrachtregel inschakelen

**Richtlijnen:** gebruik kubectl, een opdrachtregelclient, in Azure Kubernetes Service (AKS) om een Kubernetes-cluster te beheren en de logboeken van het AKS-knooppunt op te halen voor het oplossen van problemen. Kubectl is al geïnstalleerd als u Azure Cloud Shell. Als u kubectl lokaal wilt installeren, gebruikt u de cmdlet Install-AzAksKubectl.

- [Quickstart: een cluster Azure Kubernetes Service implementeren met behulp van PowerShell](kubernetes-walkthrough-powershell.md)

- [Kubelet-logboeken ophalen van AKS-clusterknooppunten (Azure Kubernetes Service)](kubelet-logs.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie de Azure [Security Benchmark: Identity and Access Control (Azure Security-benchmark: identiteit en Access Control).](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Een inventaris van beheerdersaccounts onderhouden

**Richtlijnen:** Azure Kubernetes Service (AKS) zelf biedt geen oplossing voor identiteitsbeheer waarin gewone gebruikersaccounts en wachtwoorden worden opgeslagen. Met Azure Active Directory (Azure AD)-integratie kunt u gebruikers of groepen toegang verlenen tot Kubernetes-resources binnen een naamruimte of in het cluster.

Ad-hocquery's uitvoeren om accounts te ontdekken die lid zijn van AKS-beheergroepen met de Azure AD PowerShell-module

Gebruik Azure CLI voor bewerkingen zoals 'Toegangsreferenties voor een beheerd Kubernetes-cluster krijgen' om de toegang regelmatig af te ronden. Implementeert dit proces om een bijgewerkte inventaris van de serviceaccounts bij te houden. Dit is een ander primair gebruikerstype in AKS. Dwing Security Center van identiteits- en toegangsbeheer af.

- [AKS integreren met Azure AD](azure-ad-integration-cli.md)

- [Leden van een directoryrol in Azure AD krijgen met PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Identiteit en toegang bewaken met Azure Security Center](../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Standaardwachtwoorden wijzigen, indien van toepassing

**Richtlijnen:** Azure Kubernetes Service (AKS) heeft niet het concept van algemene standaardwachtwoorden en biedt geen oplossing voor identiteitsbeheer waarbij gewone gebruikersaccounts en wachtwoorden kunnen worden opgeslagen. Met Azure Active Directory (Azure AD)-integratie kunt u op rollen gebaseerde toegang verlenen tot AKS-resources binnen een naamruimte of in het cluster. 

Ad-hocquery's uitvoeren om accounts te ontdekken die lid zijn van AKS-beheergroepen met de Azure AD PowerShell-module

- [Toegangs- en identiteitsopties voor AKS begrijpen](concepts-identity.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Toegewezen beheerdersaccounts gebruiken

**Richtlijnen:** Integreer gebruikersverificatie voor uw AKS Azure Kubernetes Service clusters (AKS) met Azure Active Directory (Azure AD). Meld u aan bij een AKS-cluster met behulp van een Azure AD-verificatie token. Kubernetes op rollen gebaseerd toegangsbeheer (Kubernetes RBAC) configureren voor beheerderstoegang tot Kubernetes-configuratiegegevens en -machtigingen, naamruimten en clusterbronnen. 

Maak beleidsregels en procedures voor het gebruik van toegewezen beheerdersaccounts. Aanbevelingen Security Center identiteits- en toegangsbeheer implementeren.

- [Identiteit en toegang bewaken met Azure Security Center](../security-center/security-center-identity-access.md)

- [Toegang tot clusterbronnen beheren](azure-ad-rbac.md)

- [Op rollen gebaseerd toegangsbesturingselementen van Azure gebruiken](control-kubeconfig-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Eenmalige aanmelding (SSO) gebruiken met Azure Active Directory

**Richtlijnen:** Gebruik een aanmelding voor Azure Kubernetes Service (AKS) met Azure Active Directory (Azure AD) geïntegreerde verificatie voor een AKS-cluster.

- [Kubernetes-logboeken, gebeurtenissen en metrische podgegevens in realtime weergeven](/azure/azure-monitor/insights/container-insights-livedata-overview)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Meervoudige verificatie gebruiken voor alle Azure Active Directory op basis van toegang

**Richtlijnen:** Verificatie voor Azure Kubernetes Service (AKS) integreren met Azure Active Directory (Azure AD). 

Schakel meervoudige verificatie van Azure AD in en volg Security Center aanbevelingen voor identiteits- en toegangsbeheer van azure ad.

- [Meervoudige verificatie inschakelen in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identiteit en toegang binnen uw Azure Security Center](../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Toegewezen machines (Privileged Access Workstations) gebruiken voor alle beheertaken

**Richtlijnen:** Gebruik een PAW (Privileged Access Workstation), met Multi-Factor Authentication (MFA), geconfigureerd om u aan te melden bij uw opgegeven Azure Kubernetes Service(AKS)-clusters en verwante resources.

- [Meer informatie over Privileged Access Workstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Multi-Factor Authentication (MFA) inschakelen in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Logboeken en waarschuwingen voor verdachte activiteiten van beheerdersaccounts

**Richtlijnen:** Gebruik Azure Active Directory (Azure AD)-beveiligingsrapporten met in Azure AD geïntegreerde verificatie voor Azure Kubernetes Service (AKS). Waarschuwingen kunnen worden gegenereerd wanneer er verdachte of onveilige activiteiten plaatsvinden in de omgeving. Gebruik Security Center om identiteits- en toegangsactiviteiten te bewaken.

- [Azure AD-gebruikers identificeren die zijn gemarkeerd voor riskante activiteiten](../active-directory/identity-protection/overview-identity-protection.md)

- [Identiteits- en toegangsactiviteit van gebruikers bewaken in Azure Security Center](../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8: Alleen Azure-resources beheren vanaf goedgekeurde locaties

**Richtlijnen:** Gebruik benoemde locaties voor voorwaardelijke toegang om toegang tot Azure Kubernetes Service clusters (AKS) toe te staan vanuit alleen specifieke logische groeperingen van IP-adresbereiken of landen/regio's. Hiervoor is geïntegreerde verificatie vereist voor AKS met Azure Active Directory (Azure AD).

Beperk de toegang tot de AKS API-server vanuit een beperkte set IP-adresbereiken, omdat deze aanvragen ontvangt om acties uit te voeren in het cluster om resources te maken of het aantal knooppunten te schalen. 

- [Beveiligde toegang tot de API-server met behulp van geautoriseerde IP-adresbereiken in Azure Kubernetes Service (AKS)](api-server-authorized-ip-ranges.md)

- [Benoemde locaties configureren in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="39-use-azure-active-directory"></a>3.9: Gebruik Azure Active Directory

**Richtlijnen:** Gebruik Azure Active Directory (Azure AD) als het centrale verificatie- en autorisatiesysteem voor Azure Kubernetes Service (AKS). Azure AD beveiligt gegevens met behulp van sterke versleuteling voor data-at-rest en in transit en salts, hashes en slaat gebruikersreferenties veilig op.

Gebruik de ingebouwde AKS-rollen met op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) - Inzender en eigenaar van resourcebeleid voor beleidstoewijzingsbewerkingen aan uw Kubernetes-cluster

- [Overzicht van Azure-beleid](../governance/policy/overview.md)

- [Azure AD integreren met AKS](azure-ad-integration-cli.md)

- [Door AKS beheerde Azure AD integreren](managed-aad.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmatig gebruikerstoegang controleren en afstemmen

**Richtlijnen:** gebruik Azure Active Directory (Azure AD)-beveiligingsrapporten met geïntegreerde Azure AD-verificatie voor Azure Kubernetes Service (AKS). Doorzoek Azure AD-logboeken om verouderde accounts te ontdekken. 

Voer Azure Identity Access Reviews uit om groepslidmaatschap, toegang tot bedrijfstoepassingen en roltoewijzingen efficiënt te beheren. Aanbevelingen voor identiteit en toegang herstellen vanuit Security Center.

Let op rollen die worden gebruikt voor ondersteunings- of probleemoplossingsdoeleinden. Alle clusteracties die door Microsoft ondersteuning worden ondernomen (met toestemming van de gebruiker) worden bijvoorbeeld gemaakt onder een ingebouwde Kubernetes-rol 'bewerken' van de naam aks-support-rolebinding. AKS-ondersteuning is ingeschakeld met deze rol om clusterconfiguratie en -resources te bewerken om clusterproblemen op te lossen en te diagnosticeren. Deze rol kan echter geen machtigingen wijzigen en geen rollen of rolbindingen maken. Deze roltoegang is alleen ingeschakeld onder actieve ondersteuningstickets met JIT-toegang (Just-In-Time).
 
- [Toegangs- en identiteitsopties voor Azure Kubernetes Service (AKS)](concepts-identity.md)

- [Toegangsbeoordelingen voor Azure Identity gebruiken](../active-directory/governance/access-reviews-overview.md)

- [De identiteits- en toegangsactiviteit van de gebruiker in de Azure Security Center](../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Pogingen tot toegang tot gedeactiveerde referenties controleren

**Richtlijnen:** Gebruikersverificatie voor Azure Kubernetes Service (AKS) integreren met Azure Active Directory (Azure AD). Diagnostische instellingen maken voor Azure AD en de audit- en aanmeldingslogboeken verzenden naar een Azure Log Analytics-werkruimte. Configureer de gewenste waarschuwingen (bijvoorbeeld wanneer een gedeactiveerd account zich probeert aan te melden) binnen een Azure Log Analytics-werkruimte.
- [Azure-activiteitenlogboeken integreren in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Logboekwaarschuwingen maken, weergeven en beheren met behulp van Azure Monitor](/azure/azure-monitor/platform/alerts-log)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Waarschuwing bij afwijking van aanmeldingsgedrag van account

**Richtlijnen:** Gebruikersverificatie voor Azure Kubernetes Service (AKS) integreren met Azure Active Directory (Azure AD). Gebruik de functie Risicodetecties en identiteitsbeveiliging van Azure AD om geautomatiseerde reacties te configureren op gedetecteerde verdachte acties met betrekking tot gebruikersidentiteiten. Gegevens opnemen in Azure Sentinel voor verder onderzoek op basis van bedrijfsbehoeften.

- [Riskante Azure AD-aanmeldingen weergeven](../active-directory/identity-protection/overview-identity-protection.md)

- [Identiteitsbeveiligingsrisicobeleid configureren en inschakelen](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Onboarding van Azure Sentinel](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Benchmark: gegevensbescherming](../security/benchmarks/security-control-data-protection.md) voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Een inventaris van gevoelige informatie onderhouden

**Richtlijnen:** Gebruik tags voor resources die betrekking hebben op Azure Kubernetes Service (AKS)-implementaties om Te helpen bij het bijhouden van Azure-resources die gevoelige informatie opslaan of verwerken.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

- [Tags bijwerken voor beheerde clusters](/rest/api/aks/managedclusters/updatetags)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Systemen isoleren die gevoelige informatie opslaan of verwerken

**Richtlijnen:** Teams en workloads in hetzelfde cluster logisch isoleren met Azure Kubernetes Service (AKS) om het minste aantal bevoegdheden te bieden, met een bereik dat is beperkt tot de resources die elk team nodig heeft. 

Gebruik de naamruimte in Kubernetes om een logische isolatiegrens te maken. Overweeg aanvullende Kubernetes-functies te implementeren voor isolatie en multitenancy, zoals planning, netwerken, verificatie/autorisatie en containers.

Implementeert afzonderlijke abonnementen en/of beheergroepen voor ontwikkel-, test- en productieomgevingen. Scheid AKS-clusters met netwerken door ze te implementeren in afzonderlijke virtuele netwerken, die op de juiste wijze worden gelabeld.

- [Meer informatie over best practices voor clusterisolatie in AKS](operator-best-practices-cluster-isolation.md)

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Best practices voor netwerkconnectiviteit en -beveiliging in AKS begrijpen](operator-best-practices-network.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Onbevoegde overdracht van gevoelige informatie controleren en blokkeren

**Richtlijnen:** Gebruik een oplossing van derden van Azure Marketplace op netwerkperimeters die controleert op onbevoegde overdracht van gevoelige informatie en dergelijke overdrachten blokkeert tijdens het waarschuwen van informatiebeveiligingsprofessionals.

Microsoft beheert het onderliggende platform en behandelt alle klantinhoud als gevoelig en doet er alles aan om zich te beschermen tegen gegevensverlies en -blootstelling van klanten. Om ervoor te zorgen dat klantgegevens in Azure veilig blijven, heeft Microsoft een suite met robuuste besturingselementen en mogelijkheden voor gegevensbeveiliging geïmplementeerd en onderhouden.

- [Lijst met vereiste poorten, adressen en domeinnamen voor AKS-functionaliteit](limit-egress-traffic.md)

- [Diagnostische instellingen configureren voor Azure Firewall](../firewall/firewall-diagnostics.md)

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Alle gevoelige gegevens tijdens de overdracht versleutelen

**Richtlijnen:** Maak een HTTPS-controller voor verkeer en gebruik uw eigen TLS-certificaten (of optioneel Let's Encrypt) voor uw AKS-implementaties (Azure Kubernetes Service). 

Kubernetes-verkeer voorgressie wordt standaard versleuteld via HTTPS/TLS. Controleer eventueel niet-versleuteld egress-verkeer van uw AKS-instanties voor aanvullende bewaking. Dit kan NTP-verkeer, DNS-verkeer, HTTP-verkeer voor het ophalen van updates in sommige gevallen. 

- [Een HTTPS-controller voor verkeer maken in AKS en uw eigen TLS-certificaten gebruiken](ingress-own-tls.md)

- [Een HTTPS-controller voor verkeer maken in AKS met Let's Encrypt](ingress-tls.md)

- [Lijst met potentiële out-going poorten en protocollen die door AKS worden gebruikt](limit-egress-traffic.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Een actief detectieprogramma gebruiken om gevoelige gegevens te identificeren

**Richtlijnen:** Functies voor gegevensidentificatie, -classificatie en -preventie zijn nog niet beschikbaar voor Azure Storage of rekenbronnen. Implementeert indien nodig een oplossing van derden voor nalevingsdoeleinden.
Microsoft beheert het onderliggende platform en behandelt alle klantinhoud als gevoelig en doet er alles aan om bescherming te bieden tegen gegevensverlies en -blootstelling van klanten. 

Om ervoor te zorgen dat klantgegevens in Azure veilig blijven, heeft Microsoft een suite met robuuste besturingselementen en mogelijkheden voor gegevensbeveiliging geïmplementeerd en onderhouden.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Azure RBAC gebruiken om toegang tot resources te beheren

**Richtlijnen:** Gebruik het azure RBAC-autorisatiesysteem (op rollen gebaseerd toegangsbeheer) dat is gebouwd op Azure Resource Manager voor een fijner toegangsbeheer van Azure-resources.

Configureer Azure Kubernetes Service (AKS) voor het gebruik van Azure Active Directory (Azure AD) voor gebruikersverificatie. Meld u aan bij een AKS-cluster met behulp van een Azure AD-verificatie token met behulp van deze configuratie. 

Gebruik de ingebouwde AKS-rollen met Azure RBAC- Inzender voor resourcebeleid en Eigenaar voor beleidstoewijzingsbewerkingen voor uw AKS-cluster

- [Toegang tot clusterbronnen beheren met Azure RBAC en Azure AD-identiteiten in AKS](azure-ad-rbac.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor de aanbevelingen Security Center van [Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Voor waarschuwingen met betrekking tot dit besturingselement is mogelijk een [Azure Defender](/azure/security-center/azure-defender) nodig voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.ContainerService**:

[!INCLUDE [Resource Policy for Microsoft.ContainerService 4.6](../../includes/policy/standards/asb/rp-controls/microsoft.containerservice-4-6.md)]

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Preventie van gegevensverlies op basis van een host gebruiken om toegangsbeheer af te dwingen

**Richtlijnen:** Functies voor gegevensidentificatie, classificatie en preventie van verlies zijn nog niet beschikbaar voor Azure Storage of rekenbronnen. Implementeert, indien nodig, een oplossing van derden voor nalevingsdoeleinden.
Microsoft beheert het onderliggende platform en behandelt alle klantinhoud als gevoelig en doet er alles aan om bescherming te bieden tegen gegevensverlies en -blootstelling van klanten. Om ervoor te zorgen dat klantgegevens in Azure veilig blijven, heeft Microsoft een suite met robuuste besturingselementen en mogelijkheden voor gegevensbeveiliging geïmplementeerd en onderhouden.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Gevoelige informatie at-rest versleutelen

**Richtlijnen:** de twee primaire typen opslag die worden geleverd voor volumes in Azure Kubernetes Service (AKS) worden mogelijk gemaakt door Azure Disks of Azure Files. Beide typen opslag maken gebruik van Azure Storage Service Encryption (SSE), waarmee data-at-rest worden versleuteld om de beveiliging te verbeteren. Gegevens worden standaard versleuteld met door Microsoft beheerde sleutels.

Versleuteling in rust met behulp van door de klant beheerde sleutels is beschikbaar voor het versleutelen van zowel het besturingssysteem als de gegevensschijven op AKS-clusters voor extra controle over versleutelingssleutels. Klanten zijn eigenaar van de verantwoordelijkheid voor activiteiten voor sleutelbeheer, zoals het maken van back-ups en roulatie van sleutels. Schijven kunnen momenteel niet worden versleuteld met behulp van Azure Disk Encryption op het niveau van het AKS-knooppunt.

- [Best practices voor opslag en back-ups in Azure Kubernetes Service (AKS)](operator-best-practices-storage.md)

- [Bring Your Own Keys (BYOK) met Azure-schijven in Azure Kubernetes Service (AKS)](azure-disk-customer-managed-keys.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logboeken en waarschuwingen voor wijzigingen in kritieke Azure-resources

**Richtlijnen:** Gebruik Azure Monitor voor containers om de prestaties te bewaken van containerworkloads die zijn geïmplementeerd op beheerde Kubernetes-clusters die worden gehost op Azure Kubernetes Service (AKS). 

Configureer waarschuwingen voor proactieve meldingen of het maken van logboeken wanneer het CPU- en geheugengebruik op knooppunten of containers gedefinieerde drempelwaarden overschrijdt, of wanneer er een statuswijziging optreedt in het cluster bij het status rollup van de infrastructuur of knooppunten. 

Gebruik azure-activiteitenlogboek om uw AKS-clusters en gerelateerde resources op hoog niveau te bewaken. Integreer met Prometheus om metrische gegevens van toepassingen en workloads weer te geven die worden verzameld van knooppunten en Kubernetes met behulp van query's om aangepaste waarschuwingen, dashboards en gedetailleerde gedetailleerde analyse uit te voeren.

- [Inzicht Azure Monitor voor containers](/azure/azure-monitor/insights/container-insights-overview)

- [Het inschakelen Azure Monitor voor containers](/azure/azure-monitor/insights/container-insights-onboard)

- [Azure-activiteitenlogboekgebeurtenissen weergeven en ophalen](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie de Azure [Security Benchmark: Vulnerability Management](../security/benchmarks/security-control-vulnerability-management.md)voor meer informatie.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Geautomatiseerde hulpprogramma's voor het scannen op beveiligingsleed uitvoeren

**Richtlijnen:** gebruik Security Center om uw Azure Container Registry AKS-exemplaren (Azure Kubernetes Service) te controleren op beveiligingsproblemen. Schakel de bundel Containerregisters in Security Center om ervoor te zorgen dat Security Center klaar is om afbeeldingen te scannen die naar het register worden pushen.

Ontvang een melding in Security Center dashboard wanneer er problemen worden gevonden nadat Security Center afbeelding hebt gescand met Qualys. De bundelfunctie Containerregisters biedt meer inzicht in beveiligingsproblemen van de afbeeldingen die worden gebruikt in Azure Resource Manager op basis van registers. 

Gebruik Security Center voor aanbevelingen die kunnen worden uitgevoerd voor elk beveiligingsprobleem. Deze aanbevelingen omvatten een classificatie van ernst en richtlijnen voor herstel. 

- [Best practices voor het beheer en de beveiliging van containerafbeeldingen in Azure Kubernetes Service (AKS)](../security-center/defender-for-container-registries-introduction.md)

- [Best practices voor het beheer en de beveiliging van containerafbeeldingen in AKS begrijpen](operator-best-practices-container-image-management.md)

- [Inzicht in de integratie van containerregisters met Azure Security Center](../security-center/defender-for-container-registries-introduction.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Een geautomatiseerde beheeroplossing voor besturingssysteempatchs implementeren

**Richtlijnen:** beveiligingsupdates worden automatisch toegepast op Linux-knooppunten om de AKS-clusters (Azure Kubernetes Service) van klanten te beveiligen. Deze updates omvatten beveiligingsfixes voor het besturingssysteem of kernelupdates. 

Houd er rekening mee dat het proces om Windows Server-knooppunten up-to-date te houden verschilt van knooppunten met Linux, omdat Windows Server-knooppunten geen dagelijkse updates ontvangen. In plaats daarvan moeten klanten een upgrade uitvoeren op de Windows Server-knooppuntgroepen in hun AKS-clusters, waarmee nieuwe knooppunten met de meest recente basis-Windows Server-afbeelding en patches worden geïmplementeerd met behulp van het Azure-configuratiescherm of de Azure CLI. Deze updates bevatten verbeteringen in de beveiliging of functionaliteit van AKS.

- [Begrijpen hoe updates worden toegepast op AKS-clusterknooppunten waarop Linux wordt uitgevoerd](node-updates-kured.md)

- [Een AKS-knooppuntgroep bijwerken voor AKS-clusters die gebruikmaken van Windows Server-knooppunten](https://docs.microsoft.com/azure/aks/use-multiple-node-pools#upgrade-a-node-pool)

- [Azure Kubernetes Service (AKS) voor afbeeldingsupgrades van knooppunt](node-image-upgrade.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Een automatische oplossing voor patchbeheer implementeren voor softwaretitels van derden

**Richtlijnen:** Implementeert een handmatig proces om ervoor te zorgen Azure Kubernetes Service toepassingen van derden van het AKS-clusterknooppunt gedurende de levensduur van het cluster worden gepatcht. Hiervoor moet u mogelijk automatische updates inschakelen, de knooppunten controleren of periodiek opnieuw opstarten.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Waarschuwingen met betrekking tot dit besturingselement vereisen mogelijk een [Azure Defender](/azure/security-center/azure-defender) voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.ContainerService**:

[!INCLUDE [Resource Policy for Microsoft.ContainerService 5.3](../../includes/policy/standards/asb/rp-controls/microsoft.containerservice-5-3.md)]

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Scans van back-to-back-beveiligingsprobleem vergelijken

**Richtlijnen:** exporteer Security Center scanresultaten met consistente intervallen en vergelijk de resultaten om te controleren of beveiligingsproblemen zijn verteerd.

Gebruik de PowerShell-cmdlet 'Get-AzSecurityTask' om het ophalen van beveiligingstaken te automatiseren die Security Center u aanbeveelt uit te voeren om uw beveiligingsstatus te versterken en de resultaten van de scan op beveiligingsprobleem te verbeteren.

- [PowerShell gebruiken om beveiligingsproblemen weer te geven die zijn ontdekt door Azure Security Center](/powershell/module/az.security/get-azsecuritytask)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Gebruik een proces voor risicoclassificatie om prioriteit te geven aan het herstellen van gevonden beveiligingsproblemen

**Richtlijnen:** gebruik de ernstclassificatie die door de Security Center om prioriteit te geven aan het herstellen van beveiligingsproblemen. 

Gebruik Common Vulnerability Scoring System (CVSS) (of een ander scoresysteem zoals geleverd door uw scanprogramma) als u een ingebouwd hulpprogramma voor de evaluatie van beveiligingsprobleem (zoals Qualys of Rapid7, aangeboden door Azure) gebruikt.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie Azure Security [Benchmark: Inventory and Asset Management (Azure Security Benchmark: Inventaris en Asset Management) voor meer informatie.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Een geautomatiseerde oplossing voor assetdetectie gebruiken

**Richtlijnen:** gebruik Azure Resource Graph voor het opvragen/ontdekken van alle resources (zoals rekenkracht, opslag, netwerk, en dergelijke) binnen uw abonnementen. Zorg ervoor dat u over de juiste (lees)machtigingen in uw tenant hebt en alle Azure-abonnementen en resources binnen uw abonnementen kunt opsnoemen.

Hoewel klassieke Azure-resources kunnen worden ontdekt via Resource Graph, wordt het ten zeerste aanbevolen om in de toekomst Azure Resource Manager te maken en te gebruiken.

- [Query's maken met Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Uw Azure-abonnementen weergeven](/powershell/module/az.accounts/get-azsubscription)

- [Inzicht krijgen in Azure RBAC](../role-based-access-control/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="62-maintain-asset-metadata"></a>6.2: Metagegevens van activa onderhouden

**Richtlijnen:** Tags toepassen op Azure-resources met metagegevens om ze logisch te ordenen in een taxonomie.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Niet-geautoriseerde Azure-resources verwijderen

**Richtlijnen:** Gebruik waar van toepassing tags, beheergroepen en afzonderlijke abonnementen om assets te organiseren en bij te houden. 

Taints, labels of tags toepassen bij het maken van een Azure Kubernetes Service (AKS)-knooppuntgroep. Alle knooppunten in die knooppuntgroep nemen ook die taint, het label of de tag over.

Taints, labels of tags kunnen worden gebruikt om de inventaris regelmatig af te stemmen en ervoor te zorgen dat niet-geautoriseerde resources tijdig worden verwijderd uit abonnementen.

- [Extra Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Een Beheergroepen](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

- [Beheerde clusters - Tags bijwerken](/rest/api/aks/managedclusters/updatetags)

- [Een taint, label of tag opgeven voor een knooppuntgroep](https://docs.microsoft.com/azure/aks/use-multiple-node-pools#specify-a-taint-label-or-tag-for-a-node-pool)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Een inventaris van goedgekeurde Azure-resources definiëren en onderhouden

**Richtlijnen:** Definieer een lijst met goedgekeurde Azure-resources en goedgekeurde software voor rekenbronnen op basis van zakelijke behoeften van de organisatie.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Controleren op niet-goedgekeurde Azure-resources

**Richtlijnen:** gebruik Azure Policy beperkingen in te stellen voor het type resources dat kan worden gemaakt in klantabonnementen met behulp van de volgende ingebouwde beleidsdefinities:
-   Niet toegestane resourcetypen 

-   Toegestane brontypen

Gebruik Azure Resource Graph om resources in uw abonnementen op te vragen/te ontdekken. Zorg ervoor dat alle Azure-resources in de omgeving worden goedgekeurd op basis van zakelijke vereisten van de organisatie.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Query's maken met Azure Graph](../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Controleren op niet-goedgekeurde softwaretoepassingen binnen rekenbronnen

**Richtlijnen:** gebruik Azure Automation Wijzigingen bijhouden en inventaris om te zien of er software in uw omgeving is geïnstalleerd. 

Inventaris verzamelen en weergeven voor software, bestanden, Linux-daemons, Windows-services en Windows-registersleutels op uw computers en controleren op niet-goedgekeurde softwaretoepassingen. 

Houd de configuraties van uw machines bij om operationele problemen in uw omgeving vast te stellen en meer inzicht te krijgen in de status van uw machines.

- [Inventaris van virtuele Azure-machines inschakelen](../automation/automation-tutorial-installed-software.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Niet-goedgekeurde Azure-resources en -softwaretoepassingen verwijderen

**Richtlijnen:** gebruik Azure Automation Wijzigingen bijhouden en inventaris om erachter te komen wat software is geïnstalleerd in uw omgeving. 

Inventaris verzamelen en weergeven voor software, bestanden, Linux-daemons, Windows-services en Windows-registersleutels op uw computers en controleren op niet-goedgekeurde softwaretoepassingen. 

Houd de configuraties van uw machines bij om operationele problemen in uw omgeving vast te stellen en meer inzicht te krijgen in de status van uw machines.

- [Inventaris van virtuele Azure-machines inschakelen](../automation/automation-tutorial-installed-software.md)

- [Bewaking van bestandsintegriteit gebruiken](../security-center/security-center-file-integrity-monitoring.md)

- [Inzicht in Azure Wijzigingen bijhouden](../automation/change-tracking/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="68-use-only-approved-applications"></a>6.8: Alleen goedgekeurde toepassingen gebruiken

**Richtlijnen:** gebruik Azure Automation Wijzigingen bijhouden en inventaris om erachter te komen wat software is geïnstalleerd in uw omgeving. 

Inventaris verzamelen en weergeven voor software, bestanden, Linux-daemons, Windows-services en Windows-registersleutels op uw computers en controleren op niet-goedgekeurde softwaretoepassingen. 

Houd de configuraties van uw machines bij om operationele problemen in uw omgeving vast te stellen en meer inzicht te krijgen in de status van uw machines.

Adaptieve toepassingsanalyse inschakelen in Security Center toepassingen die in uw omgeving bestaan.

- [Inventaris van virtuele Azure-machines inschakelen](../automation/automation-tutorial-installed-software.md)

- [Adaptieve toepassingsbesturingselementen Azure Security Center gebruiken](../security-center/security-center-adaptive-application.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="69-use-only-approved-azure-services"></a>6.9: Alleen goedgekeurde Azure-services gebruiken

**Richtlijnen:** gebruik Azure Policy beperkingen in te stellen voor het type resources dat kan worden gemaakt in klantabonnementen met behulp van de volgende ingebouwde beleidsdefinities:

- Niet toegestane resourcetypen

- Toegestane brontypen

Gebruik Azure Resource Graph voor het opvragen/ontdekken van resources binnen uw abonnementen. Zorg ervoor dat alle Azure-resources in de omgeving zijn goedgekeurd.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een specifiek resourcetype weigeren met Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Een inventaris van goedgekeurde softwaretitels onderhouden

**Richtlijnen:** Azure Policy beperkingen in te stellen voor het type resources dat in uw abonnementen kan worden gemaakt met behulp van ingebouwde beleidsdefinities.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: De mogelijkheid van gebruikers om te communiceren met Azure Resource Manager

**Richtlijnen:** Gebruik voorwaardelijke toegang van Azure om de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager te beperken door Toegang blokkeren te configureren voor de app Microsoft Azure Management.
- [Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12: De mogelijkheid van gebruikers om scripts uit te voeren in rekenbronnen beperken

**Richtlijnen:** Azure Kubernetes Service (AKS) zelf biedt geen oplossing voor identiteitsbeheer waarbij gewone gebruikersaccounts en wachtwoorden worden opgeslagen. Gebruik in plaats daarvan Azure Active Directory (Azure AD) als de geïntegreerde identiteitsoplossing voor uw AKS-clusters.

Verleen gebruikers of groepen toegang tot Kubernetes-resources binnen een naamruimte of in het cluster met behulp van Azure AD-integratie.

Gebruik de Azure AD PowerShell-module om ad-hocquery's uit te voeren om accounts te vinden die lid zijn van uw AKS-beheergroepen en deze te gebruiken om de toegang regelmatig af te stemmen. Gebruik Azure CLI voor bewerkingen zoals 'Toegangsreferenties voor een beheerd Kubernetes-cluster krijgen. Aanbevelingen Security Center identiteits- en toegangsbeheer implementeren.

- [AKS beheren met Azure CLI](/cli/azure/aks)

- [Inzicht in AKS- en Azure AD-integratie](concepts-identity.md)

- [AKS integreren met Azure AD](azure-ad-integration-cli.md)

- [Een directoryrol in Azure AD krijgen met PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Leden van een directoryrol in Azure AD krijgen met PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Identiteit en toegang bewaken met Azure Security Center](../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Toepassingen met een hoog risico fysiek of logisch scheiden

**Richtlijnen:** gebruik Azure Kubernetes Service(AKS)-functies om teams en workloads in hetzelfde cluster logisch te isoleren voor het minste aantal bevoegdheden, dat is beperkt tot de resources die elk team nodig heeft. 

Implementeert naamruimte in Kubernetes om een logische isolatiegrens te maken. Gebruik Azure Policy aliassen in de naamruimte Microsoft.ContainerService om aangepaste beleidsregels te maken om de configuratie van uw AKS-exemplaren (Azure Kubernetes Service) te controleren of af te dwingen. 

Bekijk en implementeert aanvullende Kubernetes-functies en -overwegingen voor isolatie en multitenancy met het volgende: planning, netwerken, verificatie/autorisatie en containers. Gebruik ook afzonderlijke abonnementen en beheergroepen voor ontwikkeling, testen en productie. Scheid AKS-clusters met virtuele netwerken, subnetten die op de juiste wijze zijn gelabeld en beveiligd met een Web Application Firewall (WAF).

- [Meer informatie over best practices voor clusterisolatie in AKS](operator-best-practices-cluster-isolation.md)

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Een Beheergroepen](../governance/management-groups/create-management-group-portal.md)

- [Best practices voor netwerkconnectiviteit en -beveiliging in AKS begrijpen](operator-best-practices-network.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="secure-configuration"></a>Veilige configuratie

*Zie de Azure [Security Benchmark: Secure Configuration voor meer informatie.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Veilige configuraties voor alle Azure-resources tot stand brengen

**Richtlijnen:** gebruik Azure Policy aliassen in de naamruimte Microsoft.ContainerService om aangepaste beleidsregels te maken om de configuratie van uw AKS-exemplaren (Azure Kubernetes Service) te controleren of af te dwingen. Gebruik ingebouwde Azure Policy definities.

Voorbeelden van ingebouwde beleidsdefinities voor AKS zijn:

- Inkomend HTTPS-verkeer afdwingen in Kubernetes-cluster

- Geautoriseerde IP-bereiken moeten worden gedefinieerd voor Kubernetes Services

- Op rollen gebaseerde Access Control (RBAC) moeten worden gebruikt in Kubernetes Services

- Alleen toegestane containerinstallatiekopieën garanderen in een Kubernetes-cluster

Exporteert een sjabloon van uw AKS-configuratie in JavaScript Object Notation (JSON) met Azure Resource Manager. Controleer deze regelmatig om ervoor te zorgen dat deze configuraties voldoen aan de beveiligingsvereisten voor uw organisatie. Gebruik de aanbevelingen van Azure Security Center als een veilige configuratiebasislijn voor uw Azure-resources. 

- [Beveiligingsbeleid voor AKS-pods configureren en beheren](use-pod-security-policies.md)

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Veilige besturingssysteemconfiguraties maken

**Richtlijnen:** AKS-clusters (Azure Kubernetes Clusters) worden geïmplementeerd op virtuele hostmachines met een besturingssysteem dat is geoptimaliseerd voor beveiliging. Het hostbesturingssysteem bevat aanvullende beveiligingsbeveiligingsstappen om de kans op aanvallen surface area te verminderen en om de implementatie van containers op een veilige manier toe te staan. 

Azure past dagelijkse patches (inclusief beveiligingspatches) toe op hosts van virtuele AKS-machines, waarbij sommige patches opnieuw moeten worden opgestart. Klanten zijn verantwoordelijk voor het plannen van het opnieuw opstarten van de AKS Virtual Machine-host naar behoefte. 

- [Beveiligingsbeveiliging voor het hostbesturingssysteem van het AKS-agent-knooppunt](security-hardened-vm-host-image.md)

- [Beveiligingsbeveiliging in hosts voor virtuele AKS-machines begrijpen](security-hardened-vm-host-image.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Veilige Azure-resourceconfiguraties onderhouden

**Richtlijnen:** Uw AKS-cluster (Azure Kubernetes Service) beveiligen met podbeveiligingsbeleid.  Beperk welke pods kunnen worden gepland om de beveiliging van uw cluster te verbeteren. 

Pods die resources aanvragen die niet zijn toegestaan, kunnen niet worden uitgevoerd in het AKS-cluster. 

Gebruik ook de effecten Azure Policy [weigeren] en [deploy if not exist] om beveiligde instellingen af te dwingen voor de Azure-resources die zijn gerelateerd aan uw AKS-implementaties (zoals virtuele netwerken, subnetten, Azure Firewalls, opslagaccounts, etc.). 

Maak aangepaste Azure Policy met behulp van aliassen uit de volgende naamruimten: 

- Microsoft.ContainerService

- Microsoft.Network

Aanvullende informatie is beschikbaar via de koppelingen waarnaar wordt verwezen.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Inzicht Azure Policy effecten](../governance/policy/concepts/effects.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Veilige besturingssysteemconfiguraties onderhouden

**Richtlijnen:** Azure Kubernetes Service clusters (AKS) worden geïmplementeerd op virtuele hostmachines met een besturingssysteem dat is geoptimaliseerd voor beveiliging. Het hostbesturingssysteem bevat aanvullende beveiligingsbeveiligingsstappen om de kans op aanvallen surface area te verminderen en de implementatie van containers op een veilige manier toe te staan. 

Raadpleeg de lijst met CIS-besturingselementen (Center for Internet Security) die zijn ingebouwd in het hostbesturingssysteem.  

- [Beveiliging verbeteren voor het hostbesturingssysteem van het AKS-agent-knooppunt](security-hardened-vm-host-image.md)

- [Statusconfiguratie van AKS-clusters begrijpen](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#control-plane)

- [Beveiligingsbeveiliging begrijpen in hosts voor virtuele AKS-machines](security-hardened-vm-host-image.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuratie van Azure-resources veilig opslaan

**Richtlijnen:** Gebruik Azure-opslag om uw configuraties veilig op te slaan en te beheren als u aangepaste Azure Policy gebruikt. Exporteert u een sjabloon van Azure Kubernetes Service AKS-configuratie in JavaScript Object Notation (JSON) met Azure Resource Manager. Controleer deze regelmatig om ervoor te zorgen dat de configuraties voldoen aan de beveiligingsvereisten voor uw organisatie.

Implementeert oplossingen van derden, zoals Terraform, om een configuratiebestand te maken dat de resources voor het Kubernetes-cluster declareert. U kunt uw AKS-implementatie beveiligen door best practices voor beveiliging te implementeren en uw configuratie als code op een beveiligde locatie op te slaan.

- [Een Kubernetes-cluster definiëren](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks#define-a-kubernetes-cluster)

- [Beveiligingsbeveiliging voor het hostbesturingssysteem van het AKS-agent-knooppunt](security-hardened-vm-host-image.md)

- [Code opslaan in Azure DevOps](/azure/devops/repos/git/gitworkflow)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Veilige opslag van aangepaste installatiesturingssysteeminstallatie

**Richtlijnen:** niet van toepassing op Azure Kubernetes Service (AKS). AKS biedt standaard een hostbesturingssysteem (OS) dat is geoptimaliseerd voor beveiliging. Er is momenteel geen optie om een alternatief of aangepast besturingssysteem te selecteren.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Hulpprogramma's voor configuratiebeheer implementeren voor Azure-resources

**Richtlijnen:** gebruik Azure Policy om beperkingen in te stellen voor het type resources dat in abonnementen kan worden gemaakt met behulp van ingebouwde beleidsdefinities en Azure Policy-aliassen in de naamruimte Microsoft.ContainerService. 

Maak aangepaste beleidsregels om systeemconfiguraties te controleren en af te dwingen. Ontwikkel een proces en pijplijn voor het beheren van beleids uitzonderingen.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Aliassen gebruiken](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Hulpprogramma's voor configuratiebeheer implementeren voor besturingssystemen

**Richtlijnen:** Azure Kubernetes Service clusters (AKS) worden geïmplementeerd op virtuele hostmachines met een besturingssysteem dat is geoptimaliseerd voor beveiliging. Het hostbesturingssysteem bevat aanvullende beveiligingsbeveiligingsstappen om de kans op aanvallen surface area te verminderen en om de implementatie van containers op een veilige manier toe te staan. 

Raadpleeg de lijst met CIS-besturingselementen (Center for Internet Security) die zijn ingebouwd in AKS-hosts.  

- [Beveiligingsbeveiliging voor het hostbesturingssysteem van het AKS-agent-knooppunt](security-hardened-vm-host-image.md)

- [Beveiligingsbeveiliging in hosts voor virtuele AKS-machines begrijpen](security-hardened-vm-host-image.md)

- [Statusconfiguratie van AKS-clusters begrijpen](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#control-plane)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Geautomatiseerde configuratiebewaking implementeren voor Azure-resources

**Richtlijnen:** gebruik Security Center om basislijnscans uit te voeren voor resources die betrekking hebben op uw Azure Kubernetes Service (AKS)-implementaties. Voorbeelden van resources zijn, maar niet beperkt tot het AKS-cluster zelf, het virtuele netwerk waar het AKS-cluster is geïmplementeerd, het Azure Storage-account dat wordt gebruikt om de Terraform-status bij te houden of Azure Key Vault-exemplaren die worden gebruikt voor de versleutelingssleutels voor het besturingssysteem en de gegevensschijven van uw AKS-cluster.

- [Aanbevelingen in een Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Geautomatiseerde configuratiebewaking implementeren voor besturingssystemen

**Richtlijnen:** gebruik Security Center containeraanbevelingen in de sectie Compute-apps om basislijnscans uit te voeren voor &amp; uw Azure Kubernetes Service (AKS)-clusters. 

Ontvang een melding in Security Center dashboard wanneer er configuratieproblemen of beveiligingsproblemen worden gevonden. Hiervoor moet de optionele containerregisterbundel worden inschakelen, zodat Security Center de afbeelding kan scannen.  

- [Aanbevelingen van Azure Security Center voor containers](../security-center/container-security.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure-geheimen veilig beheren

**Richtlijnen:** integreer Azure Key Vault met een AKS Azure Kubernetes Service cluster (AKS) met behulp van een FlexVolume-station. Gebruik Azure Key Vault om geheimen zoals referenties, opslagaccountsleutels of certificaten op te slaan en regelmatig te roteren. Met het FlexVolume-stuurprogramma kan het AKS-cluster standaard referenties ophalen van Key Vault en veilig alleen aan de aanvragende pod leveren. Gebruik een door pods beheerde identiteit om toegang aan te vragen tot Key Vault en de vereiste referenties op te halen via het FlexVolume-stuurprogramma. Zorg Key Vault de functie Voor verwijderen is ingeschakeld. 

Beperk de blootstelling van referenties door referenties in uw toepassingscode niet te definiëren. 

Vermijd het gebruik van vaste of gedeelde referenties. 

- [Beveiligingsconcepten voor toepassingen en clusters in Azure Kubernetes Service (AKS)](concepts-security.md)

- [Hoe u een Key Vault met uw AKS-cluster](https://docs.microsoft.com/azure/aks/developer-best-practices-pod-security#limit-credential-exposure)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Identiteiten veilig en automatisch beheren

**Richtlijnen:** definieer referenties in uw toepassingscode niet als een best practice. Gebruik beheerde identiteiten voor Azure-resources om een pod zichzelf te laten verifiëren bij elke service in Azure die dit ondersteunt, waaronder Azure Key Vault. Aan de pod wordt een Azure-identiteit toegewezen om te verifiëren bij Azure Active Directory (Azure AD) en een digitaal token te ontvangen dat kan worden gepresenteerd aan andere Azure-services die controleren of de pod gemachtigd is om toegang te krijgen tot de service en de vereiste acties uit te voeren.

Houd er rekening mee dat door pods beheerde identiteiten alleen zijn bedoeld voor gebruik met Linux-pods en containerafbeeldingen. Inrichting Azure Key Vault voor het opslaan en ophalen van digitale sleutels en referenties. Sleutels zoals de sleutels die worden gebruikt voor het versleutelen van besturingssysteemschijven, kunnen AKS-clustergegevens worden opgeslagen in Azure Key Vault.

Service-principals kunnen ook worden gebruikt in AKS-clusters. Clusters die service-principals gebruiken, kunnen echter uiteindelijk een status bereiken waarin de service-principal moet worden vernieuwd om het cluster te laten werken. Het beheren van service-principals voegt complexiteit toe. Daarom is het eenvoudiger om beheerde identiteiten te gebruiken. Dezelfde machtigingsvereisten gelden voor zowel service-principals als beheerde identiteiten.

- [Beheerde identiteiten en Key Vault met Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/developer-best-practices-pod-security#limit-credential-exposure)

- [Azure AD Pod Identity](https://github.com/Azure/aad-pod-identity)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Onbedoelde blootstelling van referenties voorkomen

**Richtlijnen:** Referentiescanner implementeren om referenties in code te identificeren. Referentiescanner bevordert ook het verplaatsen van gevonden referenties naar veiligere locaties, Azure Key Vault met aanbevelingen.

Beperk de blootstelling van referenties door referenties in uw toepassingscode niet te definiëren. en vermijd het gebruik van gedeelde referenties. Azure Key Vault moet worden gebruikt om digitale sleutels en referenties op te slaan en op te halen. Gebruik beheerde identiteiten voor Azure-resources om uw pod toegang te geven tot andere resources. 

- [Referentiescanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Best practices voor ontwikkelaars voor podbeveiliging](developer-best-practices-pod-security.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie de Azure [Security Benchmark: Malware Defense voor meer informatie.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1: Centraal beheerde antimalwaresoftware gebruiken

**Richtlijnen:** AKS beheert de levenscyclus en bewerkingen van agentknooppunten namens u. Het wijzigen van de IaaS-resources die zijn gekoppeld aan de agentknooppunten wordt niet ondersteund. Voor Linux-knooppunten kunt u echter daemonsets gebruiken om aangepaste software te installeren, zoals een antimalwareoplossing.

- [Referentiehandleiding voor beveiligingswaarschuwingen](../security-center/alerts-reference.md)

- [Waarschuwingen voor containers - Azure Kubernetes Service clusters](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)

- [Gedeelde AKS-verantwoordelijkheid en Daemon Sets](https://docs.microsoft.com/azure/aks/support-policies#shared-responsibility)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Bestanden vooraf scannen die moeten worden geüpload naar niet-compute-Azure-resources

**Richtlijnen:** scan alle bestanden die worden geüpload naar uw AKS-resources vooraf. Gebruik Security Center detectie van bedreigingen voor gegevensservices om malware te detecteren die is geüpload naar opslagaccounts als u een Azure Storage-account als gegevensopslag gebruikt of om de Terraform-status voor uw AKS-cluster bij te houden. 

- [Informatie Azure Security Center detectie van bedreigingen voor gegevensservices](../security-center/azure-defender.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8.3: Ervoor zorgen dat antimalwaresoftware en handtekeningen worden bijgewerkt

**Richtlijnen:** AKS beheert de levenscyclus en bewerkingen van agentknooppunten namens u. Het wijzigen van de IaaS-resources die zijn gekoppeld aan de agentknooppunten wordt niet ondersteund. Voor Linux-knooppunten kunt u echter daemonsets gebruiken om aangepaste software te installeren, zoals een antimalwareoplossing.

- [Referentiehandleiding voor beveiligingswaarschuwingen](../security-center/alerts-reference.md)

- [Waarschuwingen voor containers - Azure Kubernetes Service clusters](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)

- [Gedeelde AKS-verantwoordelijkheid en daemonsets](https://docs.microsoft.com/azure/aks/support-policies#shared-responsibility)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

## <a name="data-recovery"></a>Gegevensherstel

*Zie de Azure [Security Benchmark: Data Recovery voor meer informatie.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Regelmatige automatische back-ups garanderen

**Richtlijnen:** U maakt een back-up van uw gegevens met behulp van een geschikt hulpprogramma voor uw opslagtype, zoals Velero, waarmee u een back-up kunt maken van permanente volumes, samen met aanvullende clusterbronnen en configuraties. Controleer regelmatig de integriteit en beveiliging van deze back-ups. 

Verwijder de status van uw toepassingen vóór de back-up. In gevallen waarin dit niet kan worden gedaan, moet u een back-up maken van de gegevens van permanente volumes en regelmatig de herstelbewerkingen testen om de gegevensintegriteit en de vereiste processen te controleren.

- [Best practices voor opslag en back-ups in AKS](operator-best-practices-storage.md)

- [Best practices voor bedrijfscontinuïteit en herstel na noodherstel in AKS](operator-best-practices-multi-region.md)

- [Meer Azure Site Recovery](../site-recovery/site-recovery-overview.md)

- [Velero instellen in Azure](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Volledige systeemback-ups uitvoeren en back-ups maken van door de klant beheerde sleutels

**Richtlijnen:** U maakt een back-up van uw gegevens met behulp van een geschikt hulpprogramma voor uw opslagtype, zoals Velero, waarmee u een back-up kunt maken van permanente volumes, samen met aanvullende clusterbronnen en configuraties. 

Voer regelmatig automatische back-ups van Key Vault certificaten, sleutels, beheerde opslagaccounts en geheimen uit met PowerShell-opdrachten. 

- [Back-ups maken Key Vault certificaten](/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate)

- [Back-ups maken van Key Vault sleutels](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Back-ups maken Key Vault beheerde opslagaccounts](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [Back-ups maken van Key Vault Geheimen](/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret)

- [Het inschakelen van Azure Backup](/azure/backup/)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richtlijnen:** periodiek gegevensherstel uitvoeren van inhoud binnen Velero Backup. Test indien nodig het herstellen naar een geïsoleerd virtueel netwerk.

Voer periodiek gegevensherstel uit Key Vault certificaten, sleutels, beheerde opslagaccounts en geheimen, met PowerShell-opdrachten.

- [De certificaten Key Vault herstellen](/powershell/module/az.keyvault/restore-azkeyvaultcertificate)

- [De sleutels Key Vault herstellen](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Beheerde Key Vault herstellen](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Geheimen herstellen Key Vault geheimen](/powershell/module/az.keyvault/restore-azkeyvaultsecret)

- [Bestanden herstellen vanuit een back-up van virtuele Azure-machines](/azure/backup/backup-azure-restore-files-from-vm)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Beveiliging van back-ups en door de klant beheerde sleutels garanderen

**Richtlijnen:** Een back-up maken van uw gegevens met behulp van een geschikt hulpprogramma voor uw opslagtype, zoals Velero, waarmee u een back-up kunt maken van permanente volumes, samen met aanvullende clusterbronnen en configuraties. 

Schakel Soft-Delete in Key Vault om sleutels te beveiligen tegen onbedoelde of schadelijke verwijdering als Azure Key Vault wordt gebruikt met voor Azure Kubernetes Service-implementaties (AKS).

- [Meer Azure Storage Service Encryption](../storage/common/storage-service-encryption.md)

- [Een Soft-Delete inschakelen in Key Vault](https://docs.microsoft.com/azure/storage/blobs/soft-delete-blob-overview?tabs=azure-portal)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Benchmark: respons op incidenten](../security/benchmarks/security-control-incident-response.md) voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10.1: Een handleiding voor het reageren op incidenten maken

**Richtlijnen**: Stel voor uw organisatie een responshandleiding op voor gebruik bij incidenten. Zorg ervoor dat er schriftelijke responsplannen zijn waarin alle rollen van het personeel worden gedefinieerd, evenals alle fasen in het afhandelen/managen van incidenten, vanaf de detectie van het incident tot een evaluatie ervan achteraf.

- [Werkstroomautomatiseringen configureren binnen Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Richtlijnen voor het bouwen van uw eigen proces voor het reageren op beveiligingsincidents](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center van een incident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [NIST's Computer Security Incident Handling Guide](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Een procedure voor het scoren en prioriteren van incidenten maken

**Richtlijnen:** geef prioriteit aan welke waarschuwingen eerst moeten worden onderzocht Security Center ernst aan waarschuwingen moet worden toegewezen. De ernst is gebaseerd op hoe zeker Security Center is bij het vinden of de analyse die wordt gebruikt om de waarschuwing uit te geven, evenals het betrouwbaarheidsniveau dat er een schadelijke intentie is achter de activiteit die heeft geleid tot de waarschuwing.
Markeer abonnementen duidelijk (bijvoorbeeld productie, niet-productie) en maak een naamgevingssysteem om Azure-resources duidelijk te identificeren en categoriseren.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="103-test-security-response-procedures"></a>10.3: Procedures voor het reageren op beveiliging testen

**Richtlijnen:** oefeningen uitvoeren om de mogelijkheden voor incidentrespons van uw systemen regelmatig te testen. Identificeer zwakke punten en hiaten en wijzig zo nodig de reactieplannen voor incidenten.

- [Handleiding voor test-, trainings- en oefeningsprogramma's voor IT-plannen en -mogelijkheden](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Contactgegevens voor beveiligingsincidenten verstrekken en waarschuwingsmeldingen voor beveiligingsincidenten configureren

**Richtlijnen:** Contactgegevens voor beveiligingsincidenten worden door Microsoft gebruikt om contact met u op te nemen als de Microsoft Security Response Center (MSRC) detecteert dat de gegevens van de klant zijn gebruikt door een onrechtmatige of niet-geautoriseerde partij. 

Bekijk incidenten, na het feit, om ervoor te zorgen dat problemen worden opgelost.

- [De beveiligingscontactcontacte Azure Security Center instellen](../security-center/security-center-provide-security-contact-details.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Beveiligingswaarschuwingen opnemen in uw systeem voor het reageren op incidenten

**Richtlijnen:** exporteert Security Center en aanbevelingen met behulp van de functie Continue export. Met Continue export kunt u waarschuwingen en aanbevelingen handmatig of op een continue, continue manier exporteren. 

Kies de Security Center gegevensconnector om de waarschuwingen te streamen naar Azure Sentinel, afhankelijk van de behoeften en op basis van zakelijke vereisten van de organisatie.

- [Continue export configureren](../security-center/continuous-export.md)

- [Waarschuwingen streamen naar Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Het antwoord op beveiligingswaarschuwingen automatiseren

**Richtlijnen:** gebruik de functie Werkstroomautomatisering in Security Center automatisch reacties te activeren via 'Logic Apps' voor beveiligingswaarschuwingen en -aanbevelingen.

- [Werkstroomautomatisering en -Logic Apps](../security-center/workflow-automation.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie de Azure [Security Benchmark: Penetratietests en Red Team-oefeningen voor meer informatie.](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Regelmatige penetratietests uitvoeren van uw Azure-resources en zorgen voor herstel van alle kritieke beveiligingsonderzoeken

**Richtlijnen:** volg de Microsoft-regels voor betrokkenheid om ervoor te zorgen dat uw penetratietests niet in strijd zijn met het Microsoft-beleid. Aanvullende informatie over de strategie en uitvoering van Microsoft voor red teaming en penetratietests van live-site tegen door Microsoft beheerde cloudinfrastructuur, -services en -toepassingen vindt u op de koppelingen waarnaar wordt verwezen.

- [Regels voor het inzetten van penetratietests](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

## <a name="next-steps"></a>Volgende stappen

- Zie [Overzicht Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Meer informatie over [Azure-beveiligingsbasislijnen](/azure/security/benchmarks/security-baselines-overview)
