---
title: Azure-beveiligings basislijn voor Azure Kubernetes-service
description: De beveiligings basislijn van de Azure Kubernetes-service biedt procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-Bench Mark.
author: msmbaldwin
ms.service: container-service
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 8e86679e7531bb70d6d4f1a27718c2fc8f0e8a9c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100578548"
---
# <a name="azure-security-baseline-for-azure-kubernetes-service"></a>Azure-beveiligings basislijn voor Azure Kubernetes-service

De Azure Security Baseline voor Azure Kubernetes-service bevat aanbevelingen waarmee u de beveiligings-postuur van uw implementatie kunt verbeteren.

De basis lijn voor deze service wordt opgehaald uit de [Azure Security Bench Mark-versie 1,0](../security/benchmarks/overview.md), die aanbevelingen biedt over hoe u uw cloud oplossingen kunt beveiligen in azure met onze richt lijnen voor best practices.

Zie [overzicht van Azure Security-basis lijnen](../security/benchmarks/security-baselines-overview.md)voor meer informatie.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Bench Mark: Network Security](../security/benchmarks/security-control-network-security.md)(Engelstalig) voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-resources in virtuele netwerken beveiligen

**Richt lijnen**: standaard worden er automatisch een netwerk beveiligings groep en route tabel gemaakt met het maken van een AKS-cluster (Microsoft Azure Kubernetes-service). AKS wijzigt automatisch netwerk beveiligings groepen voor de juiste verkeers stroom wanneer services worden gemaakt met load balancers, poort toewijzingen of ingangs routes. De netwerk beveiligings groep wordt automatisch gekoppeld aan de virtuele Nic's op klant knooppunten en de route tabel met het subnet in het virtuele netwerk. 

Gebruik AKS-netwerk beleid om netwerk verkeer te beperken door regels te definiëren voor binnenkomend en uitgaand verkeer tussen Linux-peul in een cluster op basis van de keuze van naam ruimten en label selectie. Voor het gebruik van netwerk beleid is de Azure CNI-invoeg toepassing met gedefinieerde virtuele netwerken en subnetten vereist en kan alleen worden ingeschakeld wanneer het cluster wordt gemaakt. Ze kunnen niet worden geïmplementeerd op een bestaand AKS-cluster.

U kunt een persoonlijk AKS-cluster implementeren om ervoor te zorgen dat het netwerk verkeer tussen uw AKS-API-server en knooppunt groepen alleen in het particuliere netwerk blijft. Het besturings vlak of de API-server bevindt zich in een door AKS beheerd Azure-abonnement en maakt gebruik van interne (RFC1918) IP-adressen, terwijl het cluster of de knooppunt groep van de klant zich in hun eigen abonnement bevindt. De server en het cluster of de knooppunt groep communiceren met elkaar met behulp van de Azure Private Link-service in het virtuele netwerk van de API-server en een persoonlijk eind punt dat wordt weer gegeven in het subnet van het AKS-cluster van de klant.  U kunt ook een openbaar eind punt gebruiken voor de AKS-API-server, maar de toegang beperken met de toegestane IP-bereiken van de AKS API-server. 

- [Beveiligingsconcepten voor toepassingen en clusters in Azure Kubernetes Service (AKS)](concepts-security.md)

- [Verkeer beveiligen tussen een Peul netwerk beleid dat wordt gebruikt in azure Kubernetes service (AKS)](use-network-policies.md)

- [Een persoonlijk Azure Kubernetes service-cluster maken](private-clusters.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: de configuratie en het verkeer van virtuele netwerken, subnetten en Nic's bewaken en vastleggen

**Hulp**: gebruik Security Center en volg de aanbevelingen voor netwerk beveiliging om de netwerk bronnen te beveiligen die worden gebruikt door uw AKS-clusters (Azure Kubernetes service). 

Schakel logboeken stroom van de netwerk beveiligings groep in en verzend de logboeken naar een Azure Storage account voor controle. U kunt de stroom logboeken ook naar een Log Analytics-werk ruimte verzenden en vervolgens Traffic Analytics gebruiken om inzicht te krijgen in verkeers patronen in uw Azure-Cloud om netwerk activiteit te visualiseren, HOTS pots en beveiligings Risico's te identificeren, verkeer stroom patronen te ontdekken en onjuiste netwerk configuraties te lokaliseren.

- [Informatie over de netwerk beveiliging die wordt verschaft door Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Stroom logboeken voor netwerk beveiliging inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Traffic Analytics inschakelen en gebruiken](../network-watcher/traffic-analytics.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="13-protect-critical-web-applications"></a>1,3: essentiële webtoepassingen beveiligen

**Hulp**: gebruik een Azure-toepassing gateway ingeschakelde Web Application firewall (WAF) voor een AKS-cluster om een extra beveiligingslaag te bieden door het binnenkomende verkeer te filteren op uw webtoepassingen. Azure WAF maakt gebruik van een set regels, die wordt geboden door het open Web Application Security-project (OWASP), voor aanvallen, zoals cross-site scripting of cookie-poisoning voor dit verkeer. 

Gebruik een API-gateway voor verificatie, autorisatie, beperking, caching, trans formatie en bewaking voor Api's die in uw AKS-omgeving worden gebruikt. Een API-gateway fungeert als front-deur voor de micro Services, ontkoppelt clients van uw micro Services en verlaagt de complexiteit van uw micro Services door het verwijderen van de last van het afhandelen van cross-zaken.

- [Aanbevolen procedures voor netwerk connectiviteit en beveiliging in AKS](operator-best-practices-network.md)

- [Application Gateway ingangs controller ](../application-gateway/ingress-controller-overview.md)

- [Azure API Management gebruiken met micro services die zijn geïmplementeerd in azure Kubernetes service](../api-management/api-management-kubernetes.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: communicatie met bekende schadelijke IP-adressen weigeren

**Hulp**: Schakel micro soft Distributed Denial-of-service (DDoS) Standard-beveiliging in op de virtuele netwerken waarin de Azure Kubernetes service (AKS)-onderdelen worden geïmplementeerd voor beveiliging tegen DDoS aanvallen.
Installeer de Network Policy engine en maak Kubernetes-netwerk beleid om de stroom van verkeer tussen peul in AKS te beheren als standaard al het verkeer is toegestaan tussen deze peulen. Netwerk beleid mag alleen worden gebruikt voor Linux-knoop punten en een Peul in AKS. Regels definiëren die de pod-communicatie beperken voor een betere beveiliging. 

Kies deze optie om verkeer toe te staan of te weigeren op basis van instellingen, zoals toegewezen labels, de naam ruimte of de netwerk poort. Het vereiste netwerk beleid kan automatisch worden toegepast wanneer het Peul dynamisch wordt gemaakt in een AKS-cluster. 

- [Verkeer beveiligen tussen een Peul netwerk beleid dat wordt gebruikt in azure Kubernetes service (AKS)](use-network-policies.md)

- [DDoS-beveiliging configureren](../ddos-protection/manage-ddos-protection.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="15-record-network-packets"></a>1,5: netwerk pakketten opnemen

**Hulp**: gebruik Network Watcher pakket opname zoals vereist voor het onderzoeken van afwijkende activiteiten. 

Network Watcher wordt automatisch ingeschakeld in de regio van uw virtuele netwerk wanneer u een virtueel netwerk in uw abonnement maakt of bijwerkt. U kunt ook nieuwe exemplaren van Network Watcher maken met behulp van Power shell, de Azure CLI, de REST API of de methode ARMClient

- [Network Watcher inschakelen](../network-watcher/network-watcher-create.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: op netwerk gebaseerde inbreuk detectie/indringings systemen (ID'S/IP-adressen) implementeren

**Hulp**: Beveilig uw Azure Kubernetes service-cluster (AKS) met een Azure-toepassing gateway ingeschakeld met een Web Application firewall (WAF). 

Als inbraak detectie en/of preventie op basis van Payload-inspectie of gedrags analyse geen vereiste is, kan een Azure-toepassing gateway met WAF worden gebruikt en geconfigureerd in detectie modus om waarschuwingen en bedreigingen in een logboek te registreren, of "preventie modus" om gedetecteerde indringingen en aanvallen actief te blok keren.

- [Aanbevolen procedures voor het beveiligen van uw AKS-cluster met een WAF](operator-best-practices-network.md#secure-traffic-with-a-web-application-firewall-waf)

- [Azure-toepassing Gateway implementeren (Azure WAF)](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="17-manage-traffic-to-web-applications"></a>1,7: verkeer naar webtoepassingen beheren

**Hulp**: gebruik een Azure-toepassing met Gateway ingeschakelde Web Application firewall (WAF) voor een AKS-cluster om het binnenkomende verkeer te filteren. Het open Web Application Security-project (OWASP) biedt een set regels die worden gebruikt in de Azure-WAF om aanvallen zoals cross-site scripting of cookie-vergiftiging te bekijken.

Pas FQDN-labels (Fully Qualified Domain Name) toe op toepassingen voor gebruiks gemak bij het instellen van toepassings regels binnen een netwerk beveiligings groep. Nadat u de netwerk regels hebt ingesteld. Voeg een toepassings regel toe met behulp van een FQDN-tag, bijvoorbeeld AzureKubernetesService, die alle vereiste FQDN-codes bevat die toegankelijk zijn via TCP-poort 443 en poort 80. 

- [Aanbevolen procedures voor netwerk connectiviteit en beveiliging in AKS](operator-best-practices-network.md)

- [Verkeer beveiligen tussen een Peul netwerk beleid dat wordt gebruikt in azure Kubernetes service (AKS)](use-network-policies.md)

- [Azure-toepassing Gateway implementeren (Azure WAF)](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: de complexiteit en administratieve overhead van netwerk beveiligings regels minimaliseren

**Richt lijnen**: gebruik Tags voor het virtuele netwerk om netwerk toegangs beheer te definiëren voor netwerk beveiligings groepen die zijn gekoppeld aan Azure Kubernetes service-exemplaren (AKS). Service tags kunnen worden gebruikt in plaats van specifieke IP-adressen bij het maken van beveiligings regels om het verkeer voor de bijbehorende service toe te staan of te weigeren door de servicetag naam op te geven. 

Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen.

Pas een Azure-tag toe op knooppunt groepen in uw AKS-cluster. Ze verschillen van de service tags van het virtuele netwerk en worden toegepast op elk knoop punt in de knooppunt groep en blijven bestaan door upgrades. 

- [Service Tags begrijpen en gebruiken](../virtual-network/service-tags-overview.md)

- [Meer informatie over Nsg's voor AKS](support-policies.md)

- [Uitgaand verkeer beheren voor cluster knooppunten in azure Kubernetes service (AKS)](limit-egress-traffic.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: standaard beveiligings configuraties voor netwerk apparaten onderhouden

**Richt lijnen**: standaard beveiligings configuraties definiëren en implementeren met Azure Policy voor netwerk bronnen die zijn gekoppeld aan uw AKS-clusters (Azure Kubernetes service). Gebruik Azure Policy aliassen in de naam ruimten ' micro soft. container service ' en ' micro soft. Network ' om aangepaste beleids regels te maken om de netwerk configuratie van uw AKS-clusters te controleren of af te dwingen. 

Gebruik ook ingebouwde beleids definities met betrekking tot AKS, zoals:

• Geautoriseerde IP-bereiken moeten worden gedefinieerd op Kubernetes Services

• HTTPS-ingang afdwingen in Kubernetes-cluster

• Zorg ervoor dat services alleen Luis teren op toegestane poorten in het Kubernetes-cluster

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Voor beelden Azure Policy voor netwerken](../governance/policy/samples/built-in-policies.md#network)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="110-document-traffic-configuration-rules"></a>1,10: configuratie regels voor het document verkeer

**Hulp**: Labels gebruiken voor netwerk beveiligings groepen en andere bronnen voor verkeer stroom naar en van Azure Kubernetes Service-clusters (AKS). Gebruik het veld Beschrijving voor afzonderlijke regels voor netwerk beveiligings groepen om de bedrijfs behoeften en/of de duur op te geven, enzovoort voor regels die verkeer van/naar een netwerk toestaan.
Gebruik een van de ingebouwde Azure Policy definities met tags, bijvoorbeeld ' tag en de waarde ', zodat alle resources met tags worden gemaakt en meldingen voor bestaande niet-gelabelde resources kunnen ontvangen.

Kies ervoor om specifieke netwerk paden binnen het cluster toe te staan of te weigeren op basis van naam ruimten en label selectie met netwerk beleid. Gebruik deze naam ruimten en labels als descriptor voor de regels voor het configureren van verkeer. Gebruik Azure PowerShell of de Azure-opdracht regel interface (CLI) om op basis van hun labels acties op te zoeken of uit te voeren op resources.

- [Azure Policy met CLI](/cli/azure/policy)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

- [Een NSG maken met een beveiligings configuratie](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische hulpprogram ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

**Hulp**: Azure-activiteiten logboek gebruiken om netwerk bron configuraties te bewaken en wijzigingen te detecteren voor netwerk bronnen die betrekking hebben op Azure Kubernetes Service-clusters (AKS). 

Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer er wijzigingen in kritieke netwerk bronnen plaatsvinden. Alle vermeldingen van de AzureContainerService-gebruiker in de activiteiten logboeken worden geregistreerd als platform acties. 

Gebruik Azure Monitor Logboeken om de logboeken in te scha kelen en query's uit te voeren op de AKS van de hoofd onderdelen, uitvoeren-apiserver en uitvoeren-Controller-Manager. De knoop punten waarop de kubelet wordt uitgevoerd, maken en beheren met container runtime en hun toepassingen implementeren via de Managed Kubernetes API-server. 

- [Activiteiten logboek gebeurtenissen van Azure weer geven en ophalen](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Waarschuwingen maken in Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

- [Logboeken van Kubernetes-hoofdknooppunten inschakelen en controleren in AKS (Azure Kubernetes Service)](view-master-logs.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie [Azure Security Bench Mark: Logging and monitoring](../security/benchmarks/security-control-logging-monitoring.md)(Engelstalig) voor meer informatie.*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: goedgekeurde tijd synchronisatie bronnen gebruiken

**Hulp**: aks-knoop punten (Azure Kubernetes service) gebruiken ntp.Ubuntu.com voor tijd synchronisatie, samen met UDP-poort 123 en Network Time Protocol (NTP). 

Zorg ervoor dat de NTP-servers toegankelijk zijn voor de cluster knooppunten als u aangepaste DNS-servers gebruikt. 

- [Informatie over NTP-domein en-poort vereisten voor AKS-cluster knooppunten](limit-egress-traffic.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

**Hulp**: Schakel Logboeken in van de hoofd onderdelen van Azure Kubernetes Services (AKS), uitvoeren-apiserver en uitvoeren-Controller-Manager, die als beheerde service worden meegeleverd. 

• uitvoeren-auditaksService: de weergave naam in het controle logboek voor de besturings vlak bewerking (van de hcpService) 

• masterclient: de weergave naam in het controle logboek voor MasterClientCertificate, het certificaat dat u ontvangt van AZ AKS Get-credentials 

• nodeclient: de weergave naam voor ClientCertificate, die wordt gebruikt door agent knooppunten

Schakel ook andere audit Logboeken in, zoals uitvoeren-audit. 

Deze logboeken exporteren naar Log Analytics of een ander opslag platform. Gebruik in Azure Monitor Log Analytics-werk ruimten om analyses uit te voeren en te uitvoeren en gebruik Azure Storage accounts voor lange termijn-en archiverings opslag.

Deze gegevens in-of uitschakelen voor Azure Sentinel of een SIEM van derden op basis van uw zakelijke vereisten voor uw organisatie.

- [Controleer het logboek schema inclusief de logboek functies.](view-master-logs.md)

- [Azure Monitor voor containers begrijpen](../azure-monitor/containers/container-insights-overview.md)

- [Azure Monitor inschakelen voor containers](../azure-monitor/containers/container-insights-onboard.md)

- [Logboeken van Kubernetes-hoofdknooppunten inschakelen en controleren in AKS (Azure Kubernetes Service)](view-master-logs.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources

**Hulp**: activiteiten Logboeken gebruiken om acties op Azure Kubernetes service-resources (AKS) te controleren om alle activiteiten en hun status weer te geven. Bepalen welke bewerkingen zijn uitgevoerd op de resources in uw abonnement met activiteiten logboeken: wie de bewerking heeft gestart

Wanneer de bewerking is uitgevoerd

de status van de bewerking

de waarden van andere eigenschappen die u kunnen helpen bij het onderzoeken van de bewerking

Gegevens ophalen uit het activiteiten logboek via Azure PowerShell, de Azure-opdracht regel interface (CLI), de Azure-REST API of de Azure Portal. 

Schakel audit Logboeken in op AKS-hoofd onderdelen, zoals: 

• uitvoeren-auditaksService: de weergave naam in het controle logboek voor de besturings vlak bewerking (van de hcpService) 

• masterclient: de weergave naam in het controle logboek voor MasterClientCertificate, het certificaat dat u ontvangt van AZ AKS Get-credentials 

• nodeclient: de weergave naam voor ClientCertificate, die wordt gebruikt door agent knooppunten

Schakel ook andere audit Logboeken in, zoals uitvoeren-audit. 

- [Logboeken van Kubernetes-hoofd knooppunten inschakelen en controleren in AKS](view-master-logs.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: beveiligings logboeken verzamelen van besturings systemen

**Hulp**: Schakel automatische installatie van log Analytics agents in voor het verzamelen van gegevens uit de AKS-cluster knooppunten. U kunt ook automatisch inrichten van de Azure Log Analytics monitoring-agent inschakelen vanuit Azure Security Center, zoals standaard is ingeschakeld. De agent kan ook hand matig worden geïnstalleerd. Bij automatische inrichting op heeft Security Center de Log Analytics agent geïmplementeerd op alle ondersteunde virtuele machines van Azure en nieuwe die worden gemaakt. Security Center verzamelt gegevens van Azure Virtual Machines (VM), schaal sets voor virtuele machines en IaaS containers, zoals Kubernetes-cluster knooppunten, om te controleren op beveiligings problemen en bedreigingen. Gegevens worden verzameld met behulp van de Azure Log Analytics-agent, die verschillende aan beveiliging gerelateerde configuraties en gebeurtenis logboeken van de machine leest en de gegevens naar uw werk ruimte kopieert voor analyse. 

Gegevens verzameling is vereist om inzicht te krijgen in ontbrekende updates, onjuiste geconfigureerde beveiligings instellingen voor het besturings systeem, status van Endpoint Protection en detectie van status en bedreigingen.

- [Automatische inrichting van de Log Analytics-agent inschakelen](../security-center/security-center-enable-data-collection.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Gedeeld

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren

**Hulp**: de AKS-exemplaren (Azure Kubernetes service) voorbereiden op Azure monitor en de bijbehorende Bewaar periode voor de Azure log Analytics-werk ruimte instellen volgens de nalevings vereisten van uw organisatie. 

- [Para meters voor het bewaren van Logboeken instellen voor Log Analytics-werk ruimten](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren

**Hulp**: de AKS-exemplaren (Azure Kubernetes service) voor het Azure monitor en configureren van diagnostische instellingen voor uw cluster. 

Gebruik de Log Analytics werk ruimte van Azure Monitor om logboeken te controleren en query's uit te voeren op logboek gegevens. Azure Monitor-logboeken worden ingeschakeld en beheerd in de Azure Portal, of via CLI, en werken met zowel Kubernetes op rollen gebaseerd toegangs beheer (Kubernetes RBAC), Azure RBAC en niet-RBAC ingeschakelde AKS-clusters.

Bekijk de logboeken die zijn gegenereerd door de AKS-Master onderdelen (uitvoeren-apiserver en uitvoeren-controllermanager) voor het oplossen van problemen met uw toepassing en services. Gegevens in-en uitschakelen voor Azure Sentinel of een SIEM van derden voor gecentraliseerd logboek beheer en controle.

- [Logboeken van Kubernetes-hoofd knooppunten inschakelen en controleren in AKS](view-master-logs.md)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

- [Aangepaste query's uitvoeren in Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteiten

**Hulp**: gebruik Azure Kubernetes service (AKS) samen met Security Center om dieper inzicht te krijgen in AKS-knoop punten. Bekijk Security Center waarschuwingen over bedreigingen en schadelijke activiteiten die zijn gedetecteerd op de host en op het cluster niveau. Security Center implementeert doorlopende analyse van onbewerkte beveiligings gebeurtenissen die optreden in een AKS-cluster, zoals netwerk gegevens, het maken van processen en het Kubernetes-controle logboek. Bepaal of deze activiteit verwacht gedrag vertoont of dat de app niet goed werkt. Gebruik metrische gegevens en Logboeken in Azure Monitor om uw bevindingen te bewijzen. 

- [Meer informatie over Azure Kubernetes Services-integratie met Security Center](../security-center/defender-for-kubernetes-introduction.md)

- [Azure Security Center Standard-laag inschakelen](../security-center/security-center-get-started.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="28-centralize-anti-malware-logging"></a>2,8: registratie van anti-malware centraliseren

**Richt lijnen**: Installeer en schakel micro soft anti-malware voor Azure in op virtuele machines van Azure Kubernetes service (AKS) en virtuele-machine schaal sets. Bekijk waarschuwingen in Security Center voor herstel.

- [Micro soft antimalware voor Azure Cloud Services en Virtual Machines](../security/fundamentals/antimalware.md)

- [Naslag Gids voor beveiligings waarschuwingen](../security-center/alerts-reference.md)

- [Waarschuwingen voor containers-Azure Kubernetes Service-clusters](../security-center/alerts-reference.md#alerts-akscluster)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-query logboek registratie inschakelen

**Hulp**: Azure Kubernetes service (AKS) maakt gebruik van het CoreDNS-project voor cluster-DNS-beheer en-omzetting.

Schakel logboek registratie voor DNS-query's in door gedocumenteerde configuratie in uw coredns-aangepaste ConfigMap toe te passen. 

- [CoreDNS aanpassen met Azure Kubernetes Service](coredns-custom.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="210-enable-command-line-audit-logging"></a>2,10: controle logboek registratie op opdracht regel inschakelen

**Richt lijnen**: gebruik kubectl, een opdracht regel-client, in azure Kubernetes service (AKS) voor het beheren van een Kubernetes-cluster en het ophalen van de logboeken van het AKS-knoop punt voor het oplossen van problemen. Kubectl is al geïnstalleerd als u Azure Cloud Shell gebruikt. Als u kubectl lokaal wilt installeren, gebruikt u de cmdlet install-AzAksKubectl.

- [Snelstartgids-een Azure Kubernetes service-cluster implementeren met behulp van Power shell](kubernetes-walkthrough-powershell.md)

- [Kubelet-logboeken ophalen van AKS-clusterknooppunten (Azure Kubernetes Service)](kubelet-logs.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [Azure Security Bench Mark: identiteits-en toegangs beheer](../security/benchmarks/security-control-identity-access-control.md)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: een inventaris van beheerders accounts onderhouden

**Hulp**: Azure Kubernetes service (AKS) zelf biedt geen oplossing voor identiteits beheer, waarmee reguliere gebruikers accounts en wacht woorden worden opgeslagen. Met de integratie van Azure Active Directory (Azure AD) kunt u gebruikers of groepen toegang verlenen tot Kubernetes bronnen binnen een naam ruimte of in het cluster. 

Ad hoc-query's uitvoeren om accounts te detecteren die lid zijn van AKS-beheer groepen met de Azure AD Power shell-module

Gebruik Azure CLI voor bewerkingen als ' Get Access credentials for a Managed Kubernetes cluster ' om te helpen bij het regel matig afstemmen van de toegang. Implementeer dit proces om een bijgewerkte inventaris van de service accounts te maken, die een ander primair gebruikers type in AKS zijn. De aanbevelingen voor identiteits-en toegangs beheer van Security Center afdwingen.

- [AKS integreren met Azure AD](./azure-ad-integration-cli.md)

- [Leden van een directory-rol in azure AD ophalen met Power shell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Identiteit en toegang controleren met Azure Security Center](../security-center/security-center-identity-access.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="32-change-default-passwords-where-applicable"></a>3,2: standaard wachtwoorden wijzigen indien van toepassing

**Hulp**: Azure Kubernetes service (AKS) heeft niet het concept van algemene standaard wachtwoorden en biedt geen oplossing voor identiteits beheer waar reguliere gebruikers accounts en wacht woorden kunnen worden opgeslagen. Met Azure Active Directory-integratie (Azure AD) kunt u op rollen gebaseerde toegang tot AKS-resources binnen een naam ruimte of in het cluster verlenen. 

Ad hoc-query's uitvoeren om accounts te detecteren die lid zijn van AKS-beheer groepen met de Azure AD Power shell-module

- [Toegang en identiteits opties voor AKS](concepts-identity.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: speciale beheerders accounts gebruiken

**Hulp**: Integreer gebruikers verificatie voor uw Azure Kubernetes Service-clusters (AKS) met Azure Active Directory (Azure AD). Meld u aan bij een AKS-cluster met behulp van een Azure AD-verificatie token. Kubernetes op rollen gebaseerd toegangs beheer (Kubernetes RBAC) configureren voor beheerders toegang tot Kubernetes Configuration (kubeconfig)-gegevens en-machtigingen, naam ruimten en cluster resources. 

Beleids regels en procedures voor het gebruik van specifieke beheerders accounts maken. Implementeer Security Center aanbevelingen voor identiteits-en toegangs beheer.

- [Identiteit en toegang controleren met Azure Security Center](../security-center/security-center-identity-access.md)

- [Toegang tot cluster bronnen beheren](azure-ad-rbac.md)

- [Op rollen gebaseerde toegangs beheer functies van Azure gebruiken](control-kubeconfig-access.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: eenmalige aanmelding (SSO) met Azure Active Directory gebruiken

**Hulp**: eenmalige aanmelding gebruiken voor Azure Kubernetes service (AKS) met Azure Active Directory (Azure AD) geïntegreerde verificatie voor een AKS-cluster.

- [Kubernetes-logboeken, gebeurtenissen en metrische gegevens over pod in realtime weer geven](../azure-monitor/containers/container-insights-livedata-overview.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: multi-factor Authentication gebruiken voor alle op Azure Active Directory gebaseerde toegang

**Hulp**: Integreer verificatie voor Azure Kubernetes service (AKS) met Azure Active Directory (Azure AD). 

Schakel Azure AD Multi-Factor Authentication (MFA) in en volg de aanbevelingen voor identiteits-en toegangs beheer van Security Center.

- [MFA inschakelen in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identiteit en toegang bewaken in Azure Security Center](../security-center/security-center-identity-access.md) 

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: gebruik speciale machines (privileged Access workstations) voor alle beheer taken

**Richt lijnen**: gebruik een privileged Access Workstation (Paw), met multi-factor Authentication (MFA), dat is geconfigureerd om u aan te melden bij uw opgegeven Azure Kubernetes service (AKS)-clusters en gerelateerde bronnen.
- [Meer informatie over privileged Access workstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [MFA inschakelen in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logboeken en waarschuwingen voor verdachte activiteiten van beheerders accounts

**Hulp**: gebruik Azure Active Directory-beveiligings rapporten (Azure AD) met Azure AD geïntegreerde verificatie voor Azure Kubernetes service (AKS). Waarschuwingen kunnen worden gegenereerd wanneer verdachte of onveilige activiteiten in de omgeving worden uitgevoerd. Gebruik Security Center om identiteits-en toegangs activiteiten te bewaken.

- [Azure AD-gebruikers identificeren die zijn gemarkeerd voor riskante activiteiten](../active-directory/identity-protection/overview-identity-protection.md)

- [Identiteits-en toegangs activiteiten van gebruikers controleren in Azure Security Center](../security-center/security-center-identity-access.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: beheer Azure-resources alleen op goedgekeurde locaties

**Hulp**: gebruik benoemde locaties voor voorwaardelijke toegang om alleen toegang te verlenen tot AKS-clusters (Azure Kubernetes service) vanuit specifieke logische groepen met IP-adresbereiken of landen/regio's. Hiervoor is geïntegreerde verificatie vereist voor AKS met Azure Active Directory (Azure AD).

Beperk de toegang tot de AKS API-server vanuit een beperkt aantal IP-adresbereiken, omdat deze aanvragen voor het uitvoeren van acties in het cluster worden ontvangen om resources te maken of het aantal knoop punten te schalen. 

- [Veilige toegang tot de API-server met behulp van geautoriseerde IP-adresbereiken in azure Kubernetes service (AKS)](api-server-authorized-ip-ranges.md)

- [Benoemde locaties configureren in azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory gebruiken

**Hulp**: gebruik Azure Active Directory (Azure AD) als het centrale verificatie-en autorisatie systeem voor Azure Kubernetes service (AKS). Azure AD beveiligt gegevens door gebruik te maken van sterke code ring voor gegevens in rust-en transit-en zouten, hashes en veilige gebruikers referenties.

Gebruik de ingebouwde functies van AKS met op rollen gebaseerd toegangs beheer (Azure RBAC) van het resource beleid, voor beleids toewijzings bewerkingen aan uw Kubernetes-cluster

- [Overzicht van Azure-beleid](../governance/policy/overview.md)

- [Azure AD integreren met AKS](./azure-ad-integration-cli.md) 

- [Door AKS beheerde Azure AD integreren](managed-aad.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regel matig gebruikers toegang controleren en afstemmen

**Hulp**: gebruik Azure Active Directory-beveiligings rapporten (Azure AD) met Azure AD geïntegreerde verificatie voor Azure Kubernetes service (AKS). Zoek in azure AD-logboeken hoe u verlopen accounts kunt detecteren. 

Voer beoordelingen van Azure Identity Access uit om groepslid maatschappen, toegang tot bedrijfs toepassingen en roltoewijzingen op efficiënte wijze te beheren. Herstel de identiteits-en toegangs aanbevelingen van Security Center.

Houd rekening met de rollen die worden gebruikt voor ondersteunings-of probleemoplossings doeleinden. Zo worden alle cluster acties die door micro soft-ondersteuning worden uitgevoerd (met toestemming van de gebruiker), gemaakt onder een ingebouwde Kubernetes ' bewerken ' van de naam AKS-support-rolebinding. AKS-ondersteuning is ingeschakeld met deze rol om cluster configuratie en-bronnen te bewerken om cluster problemen op te lossen en te diagnosticeren. Deze rol kan echter geen machtigingen wijzigen en er kunnen geen rollen of functie bindingen worden gemaakt. Deze rol toegang is alleen ingeschakeld onder actieve ondersteunings tickets met Just-in-time-toegang.
 
- [Toegangs- en identiteitsopties voor Azure Kubernetes Service (AKS)](concepts-identity.md)

- [Beoordelingen over Azure Identity Access gebruiken](../active-directory/governance/access-reviews-overview.md)

- [De identiteits-en toegangs activiteit van de gebruiker in Azure Security Center controleren](../security-center/security-center-identity-access.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: controle pogingen om toegang te krijgen tot gedeactiveerde referenties

**Hulp**: Integreer gebruikers verificatie voor Azure Kubernetes service (AKS) met Azure Active Directory (Azure AD). Diagnostische instellingen voor Azure AD maken, de controle-en aanmeldings logboeken verzenden naar een Azure Log Analytics-werk ruimte. Gewenste waarschuwingen configureren (zoals wanneer een gedeactiveerd account zich probeert aan te melden) binnen een Azure Log Analytics-werk ruimte.
- [Azure-activiteitenlogboeken integreren in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Logboek waarschuwingen maken, weer geven en beheren met behulp van Azure Monitor](../azure-monitor/alerts/alerts-log.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: waarschuwing voor de afwijking van het aanmeldings gedrag van accounts

**Hulp**: Integreer gebruikers verificatie voor Azure Kubernetes service (AKS) met Azure Active Directory (Azure AD). Gebruik de functie risico detecties en identiteits beveiliging van Azure AD om automatische antwoorden te configureren op gedetecteerde verdachte acties met betrekking tot gebruikers identiteiten. Gegevens opnemen in azure Sentinel voor verdere tests op basis van bedrijfs behoeften.

- [Riskante Azure AD-aanmeldingen weergeven](../active-directory/identity-protection/overview-identity-protection.md)

- [Risico beleid voor identiteits beveiliging configureren en inschakelen](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: micro soft biedt toegang tot relevante klant gegevens tijdens ondersteunings scenario's

**Richt lijnen**: niet van toepassing op de Azure Kubernetes-service (AKS) omdat deze niet wordt ondersteund door klanten-lockbox.
- [Lijst met door Klanten-lockbox ondersteunde services](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Bench Mark: Data Protection](../security/benchmarks/security-control-data-protection.md)voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: een inventaris van gevoelige informatie onderhouden

**Richt lijnen**: Gebruik labels voor bronnen die betrekking hebben op Azure Kubernetes service (AKS)-implementaties om te helpen bij het bijhouden van Azure-resources die gevoelige informatie opslaan of verwerken.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

- [Labels voor beheerde clusters bijwerken](/rest/api/aks/managedclusters/updatetags)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: systemen isoleren die gevoelige informatie opslaan of verwerken

**Richt lijnen**: teams en werk belastingen logisch isoleren in hetzelfde cluster met Azure Kubernetes service (AKS) om het minste aantal bevoegdheden te bieden, het bereik van de resources die zijn vereist door elk team. 

Gebruik de naam ruimte in Kubernetes om een logische isolatie grens te maken. Overweeg het implementeren van aanvullende Kubernetes-functies voor isolatie en multitenancy, zoals planning, netwerken, verificatie/autorisatie en containers.

Implementeer afzonderlijke abonnementen en/of beheer groepen voor ontwikkelings-, test-en productie omgevingen. Scheid AKS-clusters met netwerken door ze te implementeren op verschillende virtuele netwerken, die op de juiste wijze worden gelabeld.

- [Meer informatie over Best Practices for cluster-isolatie in AKS](operator-best-practices-cluster-isolation.md)

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Aanbevolen procedures voor netwerk connectiviteit en beveiliging in AKS](operator-best-practices-network.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: niet-geautoriseerde overdracht van gevoelige gegevens controleren en blok keren

**Richt lijnen**: gebruik een oplossing van derden van Azure Marketplace op netwerk verbindingen die controleren op niet-geautoriseerde overdracht van gevoelige informatie en die overdrachten blokkeert tijdens het melden van informatie over de beveiliging van uw mede werkers.

Micro soft beheert het onderliggende platform en behandelt alle inhoud van de klant als vertrouwelijk en gaat naar uitstekende lengtes om het verlies en de bloot stelling van klant gegevens te beschermen. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

- [Lijst met vereiste poorten, adressen en domein namen voor de functionaliteit van AKS](limit-egress-traffic.md)

- [Diagnostische instellingen voor Azure Firewall configureren](../firewall/firewall-diagnostics.md)

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: alle gevoelige gegevens in de overdracht versleutelen

**Richt lijnen**: Maak een HTTPS ingress-controller en gebruik uw eigen TLS-certificaten (of u kunt ze ook versleutelen) voor de implementaties van uw Azure Kubernetes-service (AKS). 

Kubernetes uitgangs verkeer wordt standaard versleuteld via HTTPS/TLS. Bekijk eventueel niet-versleuteld uitgaand verkeer van uw AKS-instanties voor extra bewaking. Dit kan NTP-verkeer, DNS-verkeer, HTTP-verkeer zijn voor het ophalen van updates in sommige gevallen. 

- [Een HTTPS ingress-controller maken op AKS en uw eigen TLS-certificaten gebruiken](ingress-own-tls.md)

- [Een HTTPS ingress-controller maken op AKS met versleutelen](ingress-tls.md)

- [Lijst met mogelijke poorten en protocollen die worden gebruikt door AKS](limit-egress-traffic.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: een actief detectie hulpprogramma gebruiken om gevoelige gegevens te identificeren

**Hulp**: de functies gegevens identificatie, classificatie en verlies preventie zijn nog niet beschikbaar voor Azure Storage of reken resources. Implementeer oplossingen van derden, indien nodig voor nalevings doeleinden.
Micro soft beheert het onderliggende platform en behandelt alle inhoud van de klant als vertrouwelijk en gaat naar uitstekende lengtes om het verlies en de bloot stelling van klant gegevens te beschermen. 

Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: Azure RBAC gebruiken om de toegang tot resources te beheren

**Richt lijnen**: gebruik het Azure RBAC-autorisatie systeem (op rollen gebaseerd toegangs beheer) dat is gebouwd op Azure Resource Manager om een nauw keurig toegangs beheer van Azure-resources te bieden.

Configureer Azure Kubernetes service (AKS) voor het gebruik van Azure Active Directory (Azure AD) voor gebruikers verificatie. Meld u aan bij een AKS-cluster met behulp van Azure AD-verificatie token met deze configuratie. 

Gebruik de ingebouwde functies van AKS met Azure RBAC-resource Policy contributor en owner voor beleids toewijzings bewerkingen aan uw AKS-cluster

- [Toegang tot cluster bronnen beheren met Azure RBAC en Azure AD-identiteiten in AKS](azure-ad-rbac.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: voor komen dat gegevens verlies op basis van host wordt gebruikt voor het afdwingen van toegangs beheer

**Hulp**: de functies gegevens identificatie, classificatie en verlies preventie zijn nog niet beschikbaar voor Azure Storage of reken resources. Implementeer oplossingen van derden, indien nodig voor nalevings doeleinden.
Micro soft beheert het onderliggende platform en behandelt alle inhoud van de klant als vertrouwelijk en gaat naar uitstekende lengtes om het verlies en de bloot stelling van klant gegevens te beschermen. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: gevoelige informatie op rest versleutelen

**Richt lijnen**: de twee primaire typen opslag ruimte die worden gegeven voor volumes in azure Kubernetes service (AKS) worden ondersteund door Azure disks of Azure files. Beide typen opslag gebruiken Azure Storage-service versleuteling (SSE), waarmee gegevens in rust worden versleuteld om de beveiliging te verbeteren. Standaard worden gegevens versleuteld met door micro soft beheerde sleutels.

Versleuteling-op-rust met door de klant beheerde sleutels is beschikbaar voor het versleutelen van het besturings systeem en de gegevens schijven op AKS-clusters voor extra controle over versleutelings sleutels. Klanten zijn verantwoordelijk voor activiteiten voor sleutel beheer, zoals sleutel back-up en rotatie. Schijven kunnen momenteel niet worden versleuteld met Azure Disk Encryption op knooppunt niveau AKS.

- [Aanbevolen procedures voor opslag en back-ups in azure Kubernetes service (AKS)](operator-best-practices-storage.md)

- [Breng uw eigen sleutels (BYOK) met Azure-schijven in azure Kubernetes service (AKS)](azure-disk-customer-managed-keys.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

**Hulp**: gebruik Azure monitor voor containers voor het bewaken van de prestaties van container werkbelastingen die zijn geïmplementeerd op beheerde Kubernetes-clusters die worden gehost op de Azure Kubernetes-service (AKS). 

Waarschuwingen configureren voor proactieve meldingen of het maken van Logboeken wanneer het CPU-en geheugen gebruik op knoop punten of containers de gedefinieerde drempel waarden overschrijdt, of wanneer er een status wijziging optreedt in het cluster bij het samen vouwen van de infra structuur of de knooppunt status. 

Gebruik Azure-activiteiten logboek om uw AKS-clusters en gerelateerde resources op hoog niveau te bewaken. Integreer met Prometheus om de metrische gegevens van de toepassing en werk belasting weer te geven die worden verzameld van knoop punten en Kubernetes met behulp van query's om aangepaste waarschuwingen, Dash boards en gedetailleerde gedetailleerde analyses te maken.

- [Azure Monitor voor containers begrijpen](../azure-monitor/containers/container-insights-overview.md)

- [Azure Monitor inschakelen voor containers](../azure-monitor/containers/container-insights-onboard.md)

- [Activiteiten logboek gebeurtenissen van Azure weer geven en ophalen](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie voor meer informatie de [Azure Security Bench Mark: beveiligingslek beheer](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatische hulpprogram ma's voor het scannen van beveiligings problemen uitvoeren

**Hulp**: gebruik Security Center om uw Azure container Registry te controleren, waaronder Azure Kubernetes service-exemplaren (AKS) voor beveiligings problemen. Schakel de bundel van container registers in Security Center in om ervoor te zorgen dat Security Center gereed is voor het scannen van installatie kopieën die naar het REGI ster worden gepusht.

Ontvang een melding in het Security Center-dash board wanneer er problemen zijn gevonden nadat Security Center de installatie kopie scant met behulp van Qualys. De functie bundel van container registers biedt een diepere zicht baarheid in de beveiligings lekken van de installatie kopieën die worden gebruikt in op Azure Resource Manager gebaseerde registers. 

Gebruik Security Center voor aanbevelingen waarvoor een actie kan worden uitgevoerd voor elk beveiligingslek. Deze aanbevelingen omvatten een Ernst classificatie en richt lijnen voor herstel. 

- [Aanbevolen procedures voor het beheer van container installatie kopieën en beveiliging in azure Kubernetes service (AKS)](../security-center/defender-for-container-registries-introduction.md)

- [Aanbevolen procedures voor het beheer van container installatie kopieën en beveiliging in AKS](operator-best-practices-container-image-management.md)

- [De integratie van container registers begrijpen met Azure Security Center](../security-center/defender-for-container-registries-introduction.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: geautomatiseerde oplossing voor patch beheer voor besturings systemen implementeren

**Hulp**: beveiligings updates worden automatisch toegepast op Linux-knoop punten om de Azure Kubernetes Service-clusters (AKS) van de klant te beveiligen. Deze updates omvatten beveiligings correcties voor het besturings systeem of kernel-updates. 

Houd er rekening mee dat het proces om Windows Server-knoop punten up-to-date te houden verschilt van knoop punten waarop Linux wordt uitgevoerd, omdat Windows Server-knoop punten geen dagelijkse updates ontvangen. In plaats daarvan moeten klanten een upgrade uitvoeren op de Windows Server-knooppunt groepen in hun AKS-clusters, die nieuwe knoop punten implementeert met de nieuwste basis venster Server installatie kopie en patches met behulp van het configuratie scherm of de Azure CLI van Azure. Deze updates bevatten verbeteringen in de beveiliging of functionaliteit van AKS.

- [Begrijpen hoe updates worden toegepast op AKS-cluster knooppunten waarop Linux wordt uitgevoerd](node-updates-kured.md)

- [Een upgrade uitvoeren voor een AKS-knooppunt groep voor AKS-clusters die gebruikmaken van Windows Server-knoop punten](use-multiple-node-pools.md#upgrade-a-node-pool)

- [Upgrade van installatie kopie van knoop punt van Azure Kubernetes service (AKS)](node-image-upgrade.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: een geautomatiseerde oplossing voor patch beheer implementeren voor titels van software van derden

**Richt lijnen**: Implementeer een hand matig proces om ervoor te zorgen dat de toepassingen van derden van de Azure Kubernetes service (AKS) voor de duur van de cluster levensduur worden bijgewerkt. Hiervoor moet u mogelijk automatische updates inschakelen, de knoop punten bewaken of periodieke opnieuw opstarten uitvoeren.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: vergelijken van back-to-back-problemen

**Hulp**: de resultaten van Security Center scan worden met consistente intervallen geëxporteerd en de resultaten worden vergeleken om te controleren of de beveiligings problemen zijn opgelost. 

Gebruik de Power shell-cmdlet ' Get-AzSecurityTask ' om het ophalen van beveiligings taken waarmee Security Center wordt aanbevolen, te automatiseren om de resultaten van de scan van beveiligings problemen postuur en herstel te verbeteren.

- [Power shell gebruiken om beveiligings problemen weer te geven die zijn gedetecteerd door Azure Security Center](/powershell/module/az.security/get-azsecuritytask)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: een risico classificatie proces gebruiken om prioriteit te geven aan het herstel van ontdekte beveiligings problemen

**Richt lijnen**: gebruik de ernst classificatie van Security Center om de herbemiddeling van beveiligings problemen te bepalen. 

Gebruik een gemeen schappelijke beveiligings beoordelings systeem (CVSS) (of een andere punten die door het Scan hulpprogramma worden geleverd) als u een ingebouwd hulp programma voor evaluatie van beveiligings problemen (zoals Qualys of Rapid7, aangeboden door Azure) gebruikt.

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

## <a name="inventory-and-asset-management"></a>Inventarisatie en asset-management

*Zie [Azure Security Bench Mark: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md)voor meer informatie.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatische Asset-detectie oplossing gebruiken

**Hulp**: Azure resource Graph gebruiken voor het opvragen/detecteren van alle resources (zoals compute, opslag, netwerk, enzovoort) binnen uw abonnementen. Zorg ervoor dat u de juiste machtigingen (lezen) hebt in uw Tenant en dat u alle Azure-abonnementen kunt inventariseren, evenals de resources in uw abonnementen.

Hoewel klassieke Azure-resources kunnen worden gedetecteerd via resource grafiek, is het raadzaam om op Azure Resource Manager gebaseerde resources te maken en te gebruiken.

- [Query's maken met Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Uw Azure-abonnementen weer geven](/powershell/module/az.accounts/get-azsubscription)

- [Meer informatie over Azure RBAC](../role-based-access-control/overview.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="62-maintain-asset-metadata"></a>6,2: meta gegevens van activa onderhouden

**Richt lijnen**: Tags Toep assen op Azure-resources met meta gegevens om ze logisch in een taxonomie te organiseren.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: niet-geautoriseerde Azure-resources verwijderen

**Richt lijnen**: Gebruik labels, beheer groepen en afzonderlijke abonnementen, waar nodig, om assets te organiseren en bij te houden. 

Taints, labels of Tags Toep assen bij het maken van een Azure Kubernetes service (AKS)-knooppunt groep. Alle knoop punten in die knooppunt groep nemen ook de Taint, label of tag over.

Taints, labels of tags kunnen worden gebruikt om de inventaris regel matig af te stemmen en ervoor te zorgen dat niet-geautoriseerde resources tijdig worden verwijderd uit abonnementen.

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Beheergroepen maken](../governance/management-groups/create-management-group-portal.md)

- [Labels maken en gebruikers Tags](../azure-resource-manager/management/tag-resources.md)

- [Beheerde clusters-Tags bijwerken](/rest/api/aks/managedclusters/updatetags)

- [Een Taint, label of tag voor een knooppunt groep opgeven](use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: een inventaris van goedgekeurde Azure-resources definiëren en onderhouden

**Richt lijnen**: een lijst met goedgekeurde Azure-resources en goedgekeurde software voor reken resources definiëren op basis van de bedrijfs behoeften van de organisatie.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

**Hulp: gebruik** Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in klant abonnementen met behulp van de volgende ingebouwde beleids definities:
-   Niet toegestane resourcetypen 

-   Toegestane brontypen

Gebruik Azure resource Graph voor het opvragen/detecteren van resources in uw abonnementen. Zorg ervoor dat alle Azure-resources die aanwezig zijn in de omgeving, zijn goedgekeurd op basis van zakelijke vereisten van de organisatie.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Query's maken met Azure Graph](../governance/resource-graph/first-query-portal.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitor voor niet-goedgekeurde software toepassingen binnen reken resources

**Hulp**: gebruik Azure Automation functies wijzigingen bijhouden en inventaris om software te vinden die in uw omgeving is geïnstalleerd. 

Verzamelen en weer geven van inventaris voor software, bestanden, Linux-daemons, Windows-Services en Windows-register sleutels op uw computers en monitor voor niet-goedgekeurde software toepassingen. 

Houd de configuraties van uw machines bij om u te helpen bij het zoeken van operationele problemen in uw omgeving en om de status van uw computers beter te begrijpen.

- [Inventarisatie van virtuele Azure-machines inschakelen](../automation/automation-tutorial-installed-software.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: niet-goedgekeurde Azure-resources en software toepassingen verwijderen

**Hulp**: gebruik Azure Automation functies wijzigingen bijhouden en inventaris om software te vinden die in uw omgeving is geïnstalleerd. 

Verzamelen en weer geven van inventaris voor software, bestanden, Linux-daemons, Windows-Services en Windows-register sleutels op uw computers en monitor voor niet-goedgekeurde software toepassingen. 

Houd de configuraties van uw machines bij om u te helpen bij het zoeken van operationele problemen in uw omgeving en om de status van uw computers beter te begrijpen.

- [Inventarisatie van virtuele Azure-machines inschakelen](../automation/automation-tutorial-installed-software.md)

- [Bestands integriteit controleren gebruiken](../security-center/security-center-file-integrity-monitoring.md)

- [Meer informatie over Azure Wijzigingen bijhouden](../automation/change-tracking/overview.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="68-use-only-approved-applications"></a>6,8: alleen goedgekeurde toepassingen gebruiken

**Hulp**: gebruik Azure Automation functies wijzigingen bijhouden en inventaris om software te vinden die in uw omgeving is geïnstalleerd. 

Verzamelen en weer geven van inventaris voor software, bestanden, Linux-daemons, Windows-Services en Windows-register sleutels op uw computers en monitor voor niet-goedgekeurde software toepassingen. 

Houd de configuraties van uw machines bij om u te helpen bij het zoeken van operationele problemen in uw omgeving en om de status van uw computers beter te begrijpen.

Schakel adaptieve toepassings analyse in Security Center in voor toepassingen die in uw omgeving aanwezig zijn.

- [Inventarisatie van virtuele Azure-machines inschakelen](../automation/automation-tutorial-installed-software.md)

 
Azure Security Center Adaptive toepassing gebruiken
- [Besturingselementen](../security-center/security-center-adaptive-application.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="69-use-only-approved-azure-services"></a>6,9: alleen goedgekeurde Azure-Services gebruiken

**Hulp: gebruik** Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in klant abonnementen met behulp van de volgende ingebouwde beleids definities:

- Niet toegestane resourcetypen

- Toegestane brontypen

Gebruik Azure resource Graph voor het opvragen/detecteren van resources in uw abonnementen. Zorg ervoor dat alle Azure-resources die aanwezig zijn in de omgeving, zijn goedgekeurd.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een specifiek resource type weigeren met Azure Policy](../governance/policy/samples/index.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: een inventaris van goedgekeurde software titels onderhouden

**Hulp**: gebruik Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in uw abonnementen met ingebouwde beleids definities.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

**Richt lijnen**: gebruik de voorwaardelijke toegang van Azure om gebruikers de mogelijkheid te bieden om te communiceren met Azure Resource Manager door ' blok toegang ' te configureren voor de app Microsoft Azure management.
- [Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager te blok keren](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: de mogelijkheid van gebruikers om scripts in reken bronnen uit te voeren, beperken

**Hulp**: Azure Kubernetes service (AKS) zelf biedt geen oplossing voor identiteits beheer waar reguliere gebruikers accounts en wacht woorden worden opgeslagen. Gebruik in plaats daarvan Azure Active Directory (Azure AD) als geïntegreerde identiteits oplossing voor uw AKS-clusters. 

Verleen gebruikers of groepen toegang tot Kubernetes-resources binnen een naam ruimte of in het cluster met behulp van Azure AD-integratie. 

Gebruik de Azure AD Power shell-module om ad hoc-query's uit te voeren om accounts te detecteren die lid zijn van uw AKS-beheer groepen en gebruik deze om de toegang regel matig af te stemmen. Gebruik Azure CLI voor bewerkingen zoals ' toegangs referenties ophalen voor een beheerd Kubernetes-cluster. Implementeer Security Center aanbevelingen voor identiteits-en toegangs beheer.

- [AKS beheren met Azure CLI](/cli/azure/aks)

- [Meer informatie over AKS en Azure AD-integratie](concepts-identity.md)

- [AKS integreren met Azure AD](./azure-ad-integration-cli.md)

- [Een directory-rol verkrijgen in azure AD met Power shell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Leden van een directory-rol in azure AD ophalen met Power shell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Identiteit en toegang controleren met Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: toepassingen met een hoog risico fysiek of logisch scheiden

**Hulp**: gebruik de functies van de Azure Kubernetes service (AKS) om teams en werk belastingen logisch te isoleren in hetzelfde cluster voor het minste aantal bevoegdheden, tot de resources die zijn vereist door elk team. 

De naam ruimte in Kubernetes implementeren om een logische isolatie grens te maken. Gebruik Azure Policy aliassen in de naam ruimte ' micro soft. container service ' om aangepaste beleids regels te maken om de configuratie van uw Azure Kubernetes service (AKS)-instanties te controleren of af te dwingen. 

Bekijk en implementeer aanvullende Kubernetes-functies en overwegingen voor isolatie en multitenancy om het volgende toe te voegen: planning, netwerken, verificatie/autorisatie en containers. Gebruik ook afzonderlijke abonnementen en/of beheer groepen voor ontwikkeling, testen en productie. Scheid AKS-clusters met virtuele netwerken, subnetten die op de juiste wijze zijn gelabeld en beveiligd met een Web Application firewall (WAF).

- [Meer informatie over Best Practices for cluster-isolatie in AKS](operator-best-practices-cluster-isolation.md)

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Beheergroepen maken](../governance/management-groups/create-management-group-portal.md)

- [Aanbevolen procedures voor netwerk connectiviteit en beveiliging in AKS](operator-best-practices-network.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [Azure Security Bench Mark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md)(Engelstalig) voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources

**Hulp**: gebruik Azure Policy aliassen in de naam ruimte ' micro soft. container service ' om aangepaste beleids regels te maken voor het controleren of afdwingen van de configuratie van uw AKS-exemplaren (Azure Kubernetes service). Gebruik ingebouwde Azure Policy definities.

Voor beelden van ingebouwde beleids definities voor AKS zijn:

• HTTPS-ingang afdwingen in Kubernetes-cluster

• Geautoriseerde IP-bereiken moeten worden gedefinieerd op Kubernetes Services

• Op rollen gebaseerd toegangs beheer (RBAC) moet worden gebruikt voor Kubernetes-Services

• Zorg ervoor dat er alleen toegestane container installatie kopieën in het Kubernetes-cluster zijn

Exporteer een sjabloon van uw AKS-configuratie in JavaScript Object Notation (JSON) met Azure Resource Manager. Controleer het regel matig om er zeker van te zijn dat deze configuraties voldoen aan de beveiligings vereisten voor uw organisatie. Gebruik de aanbevelingen van Azure Security Center als een veilige configuratie basislijn voor uw Azure-resources. 

- [AKS pod-beveiligings beleid configureren en beheren](use-pod-security-policies.md)

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: veilige configuraties van besturings systemen instellen

**Richt lijnen**: Azure Kubernetes clusters (AKS)-clusters worden geïmplementeerd op virtuele machines met een geoptimaliseerde beveiliging. Het hostbesturingssysteem bevat extra stappen voor beveiligings beveiliging die erin zijn opgenomen om de surface area van aanvallen te verminderen en de implementatie van containers op een veilige manier mogelijk te maken. 

Azure past dagelijkse patches (inclusief beveiligings patches) toe aan AKS van virtuele machines met enkele patches die opnieuw moeten worden opgestart. Klanten zijn verantwoordelijk voor het plannen van het opnieuw opstarten van AKS virtual machine host op basis van behoefte. 

- [Beveiligings beveiliging voor AKS-agent knooppunt host-besturings systeem](security-hardened-vm-host-image.md)

- [Meer informatie over beveiligings beveiliging in hosts van virtuele AKS-machines](security-hardened-vm-host-image.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: Beveilig Azure-resource configuraties onderhouden

**Hulp**: Beveilig uw Azure Kubernetes service (AKS)-cluster met behulp van pod-beveiligings beleid.  Beperk wat er kan worden gepland om de beveiliging van uw cluster te verbeteren. 

Een Peul waarvoor niet-toegestane resources niet mogen worden uitgevoerd in het AKS-cluster. 

Gebruik Azure Policy [deny] en [implementeren indien niet aanwezig] effecten om beveiligde instellingen af te dwingen voor de Azure-resources die zijn gerelateerd aan uw AKS-implementaties (zoals virtuele netwerken, subnetten, Azure-firewalls, opslag accounts, enzovoort). 

Aangepaste Azure Policy definities maken met behulp van aliassen van de volgende naam ruimten: 

• Micro soft. container service

• Micro soft. Network

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy effecten begrijpen](../governance/policy/concepts/effects.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: veilige configuraties van besturings systemen onderhouden

**Richt lijnen**: Azure Kubernetes service (AKS)-clusters worden geïmplementeerd op virtuele machines met een geoptimaliseerde beveiliging. Het hostbesturingssysteem bevat extra stappen voor beveiligings beveiliging die erin zijn opgenomen om de surface area van aanvallen te verminderen en de implementatie van containers op een veilige manier mogelijk te maken. 

Raadpleeg de lijst met de besturings elementen Center for Internet Security (CIS) die zijn ingebouwd in het besturings systeem van de host.  

- [Beveiligings beveiliging voor AKS-agent knooppunt host-besturings systeem](security-hardened-vm-host-image.md)

- [Informatie over de status configuratie van AKS-clusters](concepts-clusters-workloads.md#control-plane)

- [Meer informatie over beveiligings beveiliging in hosts van virtuele AKS-machines](security-hardened-vm-host-image.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan

**Hulp**: Azure opslag plaatsen gebruiken om uw configuraties veilig op te slaan en te beheren als u aangepaste Azure Policy definities gebruikt. Exporteer een sjabloon van de configuratie van de Azure Kubernetes-service (AKS) in JavaScript Object Notation (JSON) met Azure Resource Manager. Controleer deze regel matig om te controleren of de configuraties voldoen aan de beveiligings vereisten voor uw organisatie. 

Implementeer oplossingen van derden, zoals terraform, om een configuratie bestand te maken dat de bronnen voor het Kubernetes-cluster declareert. U kunt uw AKS-implementatie beveiligen door aanbevolen procedures voor beveiliging te implementeren en uw configuratie op te slaan als code op een beveiligde locatie.

- [Een Kubernetes-cluster definiëren](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks#define-a-kubernetes-cluster)

Beveiligings beveiliging voor AKS-agent knooppunt host-besturings systeem

security-hardened-vm-host-image.md

- [Code opslaan in azure DevOps](/azure/devops/repos/git/gitworkflow)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: aangepaste installatie kopieën van een besturings systeem veilig opslaan

**Richt lijnen**: niet van toepassing op Azure Kubernetes service (AKS). AKS biedt standaard een geoptimaliseerd host-besturings systeem (OS). Er is geen huidige optie om een alternatief of aangepast besturings systeem te selecteren.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: hulpprogram ma's voor configuratie beheer voor Azure-resources implementeren

**Hulp**: gebruik Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in abonnementen met ingebouwde beleids definities en Azure Policy aliassen in de naam ruimte ' micro soft. container service '. 

Aangepaste beleids regels maken om systeem configuraties te controleren en af te dwingen. Ontwikkel een proces en pijp lijn voor het beheren van beleids uitzonderingen.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Aliassen gebruiken](../governance/policy/concepts/definition-structure.md#aliases)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: hulpprogram ma's voor configuratie beheer voor besturings systemen implementeren

**Richt lijnen**: Azure Kubernetes service (AKS)-clusters worden geïmplementeerd op virtuele machines met een geoptimaliseerde beveiliging. Het hostbesturingssysteem bevat extra stappen voor beveiligings beveiliging die erin zijn opgenomen om de surface area van aanvallen te verminderen en de implementatie van containers op een veilige manier mogelijk te maken. 

Raadpleeg de lijst met besturings elementen van het Center voor Internet Security (CIS) die zijn ingebouwd in AKS-hosts.  

- [Beveiligings beveiliging voor AKS-agent knooppunt host-besturings systeem](security-hardened-vm-host-image.md)

- [Meer informatie over beveiligings beveiliging in hosts van virtuele AKS-machines](security-hardened-vm-host-image.md)

- [Informatie over de status configuratie van AKS-clusters](concepts-clusters-workloads.md#control-plane)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-resources implementeren

**Hulp**: gebruik Security Center om basislijn scans uit te voeren voor bronnen die betrekking hebben op de implementaties van Azure Kubernetes service (AKS). Voor beelden van resources zijn niet beperkt tot het AKS-cluster zelf, het virtuele netwerk waarin het AKS-cluster is geïmplementeerd, het Azure Storage account dat wordt gebruikt voor het bijhouden van terraform-status of Azure Key Vault exemplaren die worden gebruikt voor de versleutelings sleutels voor het besturings systeem en de gegevens schijven van uw AKS-cluster.

- [Aanbevelingen herstellen in Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: geautomatiseerde configuratie bewaking voor besturings systemen implementeren

**Hulp**: gebruik Security Center container aanbevelingen in het gedeelte COMPUTE- &amp; apps om basislijn scans uit te voeren voor uw AKS-clusters (Azure Kubernetes service). Ontvang een melding in het Security Center-dash board wanneer er configuratie problemen of beveiligings problemen worden gevonden. Hiervoor moet de optionele container-register bundel worden ingeschakeld, waarmee Security Center de installatie kopie kunt scannen.  

- [Aanbevelingen van Azure Security Center voor containers](../security-center/container-security.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure-geheimen veilig beheren

**Hulp**: Integreer Azure Key Vault met een AKS-cluster (Azure Kubernetes service) met behulp van een FlexVolume-station. Gebruik Azure Key Vault om geheimen op te slaan en regel matig te draaien, zoals referenties, sleutels voor opslag accounts of certificaten. Met het FlexVolume-stuur programma kan het AKS-cluster systeem eigen referenties ophalen van Key Vault en deze alleen veilig aanbieden aan de aanvragende pod. Gebruik een beheerde pod-identiteit om toegang aan te vragen voor Key Vault en de vereiste referenties op te halen via het FlexVolume-stuur programma. Zorg ervoor Key Vault zacht verwijderen is ingeschakeld. 

Beperk de bloot stelling van referenties door geen referenties te definiëren in de code van uw toepassing. 

Vermijd het gebruik van vaste of gedeelde referenties. 

- [Beveiligingsconcepten voor toepassingen en clusters in Azure Kubernetes Service (AKS)](concepts-security.md)

- [Key Vault gebruiken met uw AKS-cluster](developer-best-practices-pod-security.md#limit-credential-exposure)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: identiteiten veilig en automatisch beheren

**Hulp**: Geef geen referenties in de toepassings code op als een beveiligings best practice. Gebruik beheerde identiteiten voor Azure-resources om een pod te laten verifiëren voor elke service in azure die dit ondersteunt, met inbegrip van Azure Key Vault. Aan de Pod is een Azure-identiteit toegewezen om te verifiëren bij Azure Active Directory (Azure AD) en een digitaal token te ontvangen dat kan worden gepresenteerd aan andere Azure-Services die controleren of de Pod is gemachtigd om toegang te krijgen tot de service en de vereiste acties uit te voeren. 

Houd er rekening mee dat beheerde identiteiten van pod alleen bedoeld zijn voor gebruik met Linux peul en container installatie kopieën. Azure Key Vault inrichten om digitale sleutels en referenties op te slaan en op te halen. Sleutels, zoals degene die worden gebruikt voor het versleutelen van besturingssysteem schijven, kunnen AKS-cluster gegevens worden opgeslagen in Azure Key Vault.

Service-principals kunnen ook worden gebruikt in AKS-clusters. Clusters die service-principals gebruiken, kunnen echter uiteindelijk een status krijgen waarin de Service-Principal moet worden vernieuwd om het cluster te laten functioneren. Het beheren van service-principals voegt complexiteit toe. Daarom is het eenvoudiger om beheerde identiteiten te gebruiken. Dezelfde machtigings vereisten gelden voor service-principals en beheerde identiteiten.

- [Beheerde identiteiten en Key Vault begrijpen met Azure Kubernetes service (AKS)](developer-best-practices-pod-security.md#limit-credential-exposure)

- [Pod-identiteit van Azure Active Directory](https://github.com/Azure/aad-pod-identity)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: onbedoelde referentie blootstelling elimineren

**Richt lijnen**: referentie scanner implementeren om referenties in code te identificeren. Credential scanner stimuleert ook het verplaatsen van gedetecteerde referenties naar veiliger locaties, zoals Azure Key Vault met aanbevelingen.

Beperk de bloot stelling van referenties door geen referenties te definiëren in de code van uw toepassing. en Vermijd het gebruik van gedeelde referenties. Azure Key Vault moet worden gebruikt om digitale sleutels en referenties op te slaan en op te halen. Gebruik beheerde identiteiten voor Azure-resources om ervoor te zorgen dat uw Pod toegang tot andere bronnen kan aanvragen. 

- [Referentie scanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Aanbevolen procedures voor ontwikkel aars voor pod-beveiliging](developer-best-practices-pod-security.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie voor meer informatie de [Azure Security Bench Mark: beveiliging tegen schadelijke software](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: centraal beheerde antimalware-software gebruiken

**Richt lijnen**: aks beheert de levens cyclus en bewerkingen van agent knooppunten namens u-wijzigingen in de IaaS-resources die zijn gekoppeld aan de agent knooppunten, worden niet ondersteund. Voor Linux-knoop punten kunt u echter daemon sets gebruiken om aangepaste software te installeren, zoals een anti-malware-oplossing.

- [Naslag Gids voor beveiligings waarschuwingen](../security-center/alerts-reference.md)

- [Waarschuwingen voor containers-Azure Kubernetes Service-clusters](../security-center/alerts-reference.md#alerts-akscluster)

- [Gedeelde verantwoordelijkheids-en daemon-sets AKS](support-policies.md#shared-responsibility)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: scan bestanden die moeten worden geüpload naar niet-reken resources van Azure

**Hulp**: voor het vooraf scannen van bestanden die worden geüpload naar uw AKS-resources. Gebruik Security Center bedreigings detectie voor gegevens Services om malware te detecteren die is geüpload naar opslag accounts als u een Azure Storage-account gebruikt als gegevens archief of als u de terraform-status voor uw AKS-cluster wilt bijhouden. 

- [Meer informatie over de detectie van bedreigingen van Azure Security Center voor gegevens Services](../security-center/azure-defender.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8,3: controleren of antimalware-software en hand tekeningen zijn bijgewerkt

**Richt lijnen**: aks beheert de levens cyclus en bewerkingen van agent knooppunten namens u-wijzigingen in de IaaS-resources die zijn gekoppeld aan de agent knooppunten, worden niet ondersteund. Voor Linux-knoop punten kunt u echter daemon sets gebruiken om aangepaste software te installeren, zoals een anti-malware-oplossing.

- [Naslag Gids voor beveiligings waarschuwingen](../security-center/alerts-reference.md)

- [Waarschuwingen voor containers-Azure Kubernetes Service-clusters](../security-center/alerts-reference.md#alerts-akscluster)

- [Gedeelde verantwoordelijkheids-en daemon-sets AKS](support-policies.md#shared-responsibility)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

## <a name="data-recovery"></a>Gegevensherstel

*Zie [Azure Security Bench Mark: Data Recovery](../security/benchmarks/security-control-data-recovery.md)(Engelstalig) voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: controleren op regel matige automatische back-ups

**Richt lijnen**: Maak een back-up van uw gegevens met behulp van een geschikt hulp programma voor uw opslag type, zoals velero, waarmee back-ups van permanente volumes kunnen worden gemaakt, samen met extra cluster bronnen en-configuraties. Controleer regel matig de integriteit en beveiliging van deze back-ups. 

Verwijder de status van uw toepassingen voordat u een back-up maakt. Als dit niet mogelijk is, maakt u een back-up van de gegevens van permanente volumes en test u de herstel bewerkingen regel matig om de integriteit van de gegevens en de vereiste processen te controleren.

- [Aanbevolen procedures voor opslag en back-ups in AKS](operator-best-practices-storage.md)

- [Aanbevolen procedures voor bedrijfs continuïteit en herstel na nood gevallen in AKS](operator-best-practices-multi-region.md)

- [Azure Site Recovery begrijpen](../site-recovery/site-recovery-overview.md)

- [Velero in azure instellen](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: volledige back-ups van het systeem uitvoeren en een back-up maken van door de klant beheerde sleutels

**Richt lijnen**: Maak een back-up van uw gegevens met behulp van een geschikt hulp programma voor uw opslag type, zoals velero, waarmee back-ups van permanente volumes kunnen worden gemaakt, samen met extra cluster bronnen en-configuraties. 

Maak regel matig automatische back-ups van Key Vault certificaten, sleutels, beheerde opslag accounts en geheimen, met Power shell-opdrachten. 

Bijvoorbeeld:

Backup-AzKeyVaultCertificate Backup-AzKeyVaultKey Backup-AzKeyVaultManagedStorageAccount Backup-AzKeyVaultSecret

- [Back-up maken van Key Vault certificaten](/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate)

- [Back-ups maken van Key Vault sleutels](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Back-ups maken van Key Vault beheerde opslag accounts](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [Back-ups maken van Key Vault geheimen](/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret)

- [Azure Backup inschakelen](../backup/index.yml)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richt lijnen**: regel matig gegevens herstel van inhoud in Velero-back-up uitvoeren. Test zo nodig het herstellen naar een geïsoleerd virtueel netwerk.

Voer regel matig gegevens herstel uit van Key Vault certificaten, sleutels, beheerde opslag accounts en geheimen, met Power shell-opdrachten. 

Bijvoorbeeld:

Restore-AzKeyVaultCertificate Restore-AzKeyVaultKey Restore-AzKeyVaultManagedStorageAccount Restore-AzKeyVaultSecret

- [Key Vault certificaten herstellen](/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0)

- [Key Vault sleutels herstellen](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

- [Key Vault beheerde opslag accounts herstellen](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Key Vault geheimen herstellen](/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0)

- [Bestanden herstellen vanuit back-up van Azure virtual machine](../backup/backup-azure-restore-files-from-vm.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zorg voor de bescherming van back-ups en door de klant beheerde sleutels

**Richt lijnen**: Maak een back-up van uw gegevens met behulp van een geschikt hulp programma voor uw opslag type, zoals velero, waarmee back-ups van permanente volumes kunnen worden gemaakt, samen met extra cluster bronnen en-configuraties. 

Schakel Soft-Delete in Key Vault in om sleutels te beschermen tegen onbedoelde of schadelijke verwijdering als Azure Key Vault wordt gebruikt met voor Azure Kubernetes service (AKS)-implementaties.

- [Azure Storage service-versleuteling begrijpen](../storage/common/storage-service-encryption.md)

- [Soft-Delete in Key Vault inschakelen](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Bench Mark: Incident Response](../security/benchmarks/security-control-incident-response.md)(Engelstalig) voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10,1: een hand leiding voor reactie op incidenten maken

**Richtlijnen**: Stel voor uw organisatie een responshandleiding op voor gebruik bij incidenten. Zorg ervoor dat er schriftelijke responsplannen zijn waarin alle rollen van het personeel worden gedefinieerd, evenals alle fasen in het afhandelen/managen van incidenten, vanaf de detectie van het incident tot een evaluatie ervan achteraf.

- [Werk stroom automatisering configureren in Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Richt lijnen voor het bouwen van uw eigen beveiligings incident antwoord proces](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Micro soft Security Response Center anatomie van een incident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Klant kan ook gebruikmaken van de hand leiding voor de verwerking van het computer beveiligings incident van het NIST om hulp te bieden bij het maken van een eigen incident respons plan](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: een beoordelings procedure voor incidenten en prioriteits procedures maken

**Richt lijnen**: rang geven aan welke waarschuwingen eerst moeten worden onderzocht met Security Center ernst van de waarschuwing. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of het analyse programma dat wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau dat er schadelijke bedoelingen zijn achter de activiteit die tot de waarschuwing heeft geleid.
Markeer abonnementen duidelijk (bijvoorbeeld productie, niet-productie) en maak een naamgevings systeem om Azure-resources duidelijk te identificeren en te categoriseren.

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="103-test-security-response-procedures"></a>10,3: procedures voor beveiligings antwoorden testen

**Richt lijnen**: oefent oefeningen uit om de respons mogelijkheden van uw systeem op een gewone uitgebracht te testen. Raadpleeg de publicatie van het NIST om zwakke punten en hiaten te identificeren en zo nodig de planning te herzien.

- [Hand leiding voor het testen, trainen en uitoefenen van Program Ma's voor IT-plannen en-mogelijkheden](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen configureren voor beveiligings incidenten

**Hulp**: contact gegevens van beveiligings incidenten worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat de gegevens van de klant zijn geopend door een onrecht matige of niet-gemachtigde partij. Bekijk incidenten na het feit om te controleren of de problemen zijn opgelost.

- [De Azure Security Center Security-contact persoon instellen](../security-center/security-center-provide-security-contact-details.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: beveiligings waarschuwingen opnemen in uw reactie systeem van uw incident

**Hulp**: Security Center waarschuwingen en aanbevelingen exporteren met de functie continue export. Met doorlopend exporteren kunt u waarschuwingen en aanbevelingen hand matig of op een doorlopende manier exporteren. U kunt ook de Security Center Data Connector kiezen om de waarschuwingen naar Azure Sentinel te streamen op basis van de zakelijke vereisten van de organisatie.

- [Continue export configureren](../security-center/continuous-export.md)

- [Waarschuwingen streamen naar Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: de reactie op beveiligings waarschuwingen automatiseren

**Hulp**: gebruik de functie werk stroom automatisering in azure Security Center om automatisch reacties te activeren via ' Logic apps ' in beveiligings waarschuwingen en aanbevelingen.

- [Werk stroom automatisering en Logic Apps configureren](../security-center/workflow-automation.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie [Azure Security Bench Mark: Indringings tests en rode team oefeningen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)voor meer informatie.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: voert regel matig indringings tests van uw Azure-resources uit en zorgt voor herbemiddeling van alle essentiële beveiligings resultaten

**Richt lijnen**: Volg de micro soft-regels om ervoor te zorgen dat de indringings tests niet worden geschonden door het micro soft-beleid: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

- [U vindt hier meer informatie over de strategie van micro soft en de uitvoering van Red Teaming en live site indringings tests met door micro soft beheerde Cloud infrastructuur,-services en-toepassingen.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

## <a name="next-steps"></a>Volgende stappen

- Zie de [Azure Security-Bench Mark](../security/benchmarks/overview.md)
- Meer informatie over [Azure-beveiligingsbasislijnen](../security/benchmarks/security-baselines-overview.md)
