---
title: Azure-beveiligings basislijn voor Azure Data Factory
description: De Azure Data Factory Security Baseline voorziet in procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-Bench Mark.
author: msmbaldwin
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: a21ae2ce79c500455c5735f4d82e7852e8474ad1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105559142"
---
# <a name="azure-security-baseline-for-azure-data-factory"></a>Azure-beveiligings basislijn voor Azure Data Factory

In deze beveiligings basislijn worden richt lijnen van de [Azure Security Bench Mark-versie 1,0](../security/benchmarks/overview-v1.md) ingesteld op Azure Data Factory. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen.
De inhoud wordt gegroepeerd op de **beveiligings controles** die zijn gedefinieerd door de Azure Security-benchmark en de bijbehorende richt lijnen die van toepassing zijn op Azure Data Factory. **Besturings elementen** die niet van toepassing zijn op Azure Data Factory, zijn uitgesloten.

Zie [volledige Azure Data Factory beveiliging Baseline-toewijzings bestand](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)voor meer informatie over hoe Azure Data Factory volledig wordt toegewezen aan de beveiligings benchmark van Azure.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Bench Mark: Network Security](../security/benchmarks/security-control-network-security.md)(Engelstalig) voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-resources in virtuele netwerken beveiligen

**Richt lijnen**: wanneer u een Azure-SSIS Integration runtime (IR) maakt, hebt u de mogelijkheid om deze te koppelen aan een virtueel netwerk. Hiermee kunnen Azure Data Factory bepaalde netwerk bronnen maken, zoals een netwerk beveiligings groep (NSG) en een load balancer. U hebt ook de mogelijkheid om uw eigen statische open bare IP-adres op te geven of Azure Data Factory een voor u te maken.  Op het NSG dat automatisch wordt gemaakt door Azure Data Factory, is poort 3389 standaard geopend voor al het verkeer. Vergrendel dit om ervoor te zorgen dat alleen uw beheerders toegang hebben.

Self-Hosted IRs kan worden geïmplementeerd op een on-premises machine of virtuele Azure-machine in een virtueel netwerk. Zorg ervoor dat in de implementatie van het subnet van het virtuele netwerk een NSG is geconfigureerd om alleen beheerders toegang toe te staan. Azure-SSIS IR heeft poort 3389 standaard niet toegestaan op de Windows Firewall-regel op elk IR-knoop punt voor beveiliging. U kunt uw door het virtuele netwerk geconfigureerde bronnen beveiligen door een NSG aan het subnet te koppelen en strikte regels in te stellen.

Als privé-koppeling beschikbaar is, gebruikt u privé-eind punten om alle resources te beveiligen die worden gekoppeld aan uw Azure Data Factory pijp lijn, zoals Azure SQL Server. Met een persoonlijke koppeling wordt verkeer tussen uw virtuele netwerk en de service over het micro soft-backbone-netwerk gepasseerd, waardoor de bloot stelling van het open bare Internet wordt geëlimineerd.

- [Een Azure-SSIS IR maken](create-azure-ssis-integration-runtime.md)

- [Een zelf-hostende IR maken en configureren](create-self-hosted-integration-runtime.md)

- [Een Virtual Network maken](../virtual-network/quick-create-portal.md)

- [Een NSG maken met een beveiligings configuratie](../virtual-network/tutorial-filter-network-traffic.md)

- [Een Azure-SSIS IR toevoegen aan een virtueel netwerk](join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration)

- [Persoonlijke Azure-koppeling](../private-link/private-link-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: de configuratie en het verkeer van virtuele netwerken, subnetten en netwerk interfaces bewaken en vastleggen

**Hulp**: gebruik Azure Security Center en herstel aanbevelingen voor netwerk beveiliging voor het virtuele netwerk en de netwerk beveiligings groep die zijn gekoppeld aan uw Integration runtime-implementatie.

Daarnaast kunt u NSG-stroom Logboeken (netwerk beveiligings groep) inschakelen voor de NSG uw Integration Runtime-implementatie beveiligen en logboeken naar een Azure Storage account verzenden voor het controleren van het verkeer.

U kunt ook NSG-stroom logboeken naar een Log Analytics-werk ruimte verzenden en Traffic Analytics gebruiken om inzicht te krijgen in de verkeers stroom in uw Azure-Cloud. Enkele voor delen van Traffic Analytics zijn de mogelijkheid om netwerk activiteiten te visualiseren en HOTS pots te identificeren, beveiligings dreigingen te identificeren, verkeers patronen te begrijpen en netwerk configuraties te lokaliseren.

- [Informatie over de netwerk beveiliging die wordt verschaft door Azure Security Center](../security-center/security-center-network-recommendations.md)

- [NSG-stroom logboeken inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Informatie over de netwerk beveiliging die wordt verschaft door Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Traffic Analytics inschakelen en gebruiken](../network-watcher/traffic-analytics.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: communicatie met bekende schadelijke IP-adressen weigeren

**Hulp**: Schakel DDoS Protection standaard in op de virtuele netwerken die zijn gekoppeld aan uw Integration runtime-implementatie voor beveiliging van gedistribueerde Denial-of-service-aanvallen. Gebruik Azure Security Center geïntegreerde bedreigings informatie om communicatie met bekende of ongebruikte Internet-IP-adressen te weigeren.

- [DDoS-beveiliging configureren](../ddos-protection/manage-ddos-protection.md)

- [Meer informatie over Azure Security Center geïntegreerde bedreigings informatie](../security-center/azure-defender.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="15-record-network-packets"></a>1,5: netwerk pakketten opnemen

**Hulp**: Schakel logboeken stroom voor netwerk beveiligings groepen (NSG) in voor de NSG die uw Integration runtime-implementatie beveiligen en logboeken naar een Azure Storage account verzenden voor het controleren van het verkeer.

U kunt ook NSG-stroom logboeken naar een Log Analytics-werk ruimte verzenden en Traffic Analytics gebruiken om inzicht te krijgen in de verkeers stroom in uw Azure-Cloud. Enkele voor delen van Traffic Analytics zijn de mogelijkheid om netwerk activiteiten te visualiseren en HOTS pots te identificeren, beveiligings dreigingen te identificeren, verkeers patronen te begrijpen en netwerk configuraties te lokaliseren.

- [NSG-stroom logboeken inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Informatie over de netwerk beveiliging die wordt verschaft door Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Traffic Analytics inschakelen en gebruiken](../network-watcher/traffic-analytics.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: op netwerk gebaseerde inbreuk detectie/indringings systemen (ID'S/IP-adressen) implementeren

**Richt lijnen**: als u uitgaand verkeer van Azure-SSIS IR wilt controleren, kunt u verkeer dat is geïnitieerd door Azure-SSIS IR naar een on-premises firewall apparaat door sturen via Azure ExpressRoute Force tunneling of naar een virtueel netwerk apparaat (NVA) van Azure Marketplace dat ondersteuning biedt voor ID'S/IP-adressen. Als inbraak detectie en/of preventie op basis van Payload-inspectie geen vereiste is, kan Azure Firewall met bedreigings informatie worden gebruikt.

- [Een Azure-SSIS Integration Runtime toevoegen aan een virtueel netwerk](join-azure-ssis-integration-runtime-virtual-network.md)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Azure Firewall implementeren](../firewall/tutorial-firewall-deploy-portal.md)

- [Waarschuwingen configureren met Azure Firewall](../firewall/threat-intel.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: de complexiteit en administratieve overhead van netwerk beveiligings regels minimaliseren

**Richt lijnen**: gebruik Tags voor het virtuele netwerk om netwerk toegangs beheer in te stellen voor de netwerk beveiligings groep (NSG) of Azure firewall. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de service label (bijvoorbeeld DataFactoryManagement) op te geven in het juiste bron-of doel veld van een regel, kunt u inkomend of geblokkeerd verkeer voor de bijbehorende service toestaan of weigeren. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen.

- [Service Tags begrijpen en gebruiken](../virtual-network/service-tags-overview.md)

- [Meer informatie over Azure Data Factory specifieke service Tags](join-azure-ssis-integration-runtime-virtual-network.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: standaard beveiligings configuraties voor netwerk apparaten onderhouden

**Hulp**: Definieer en implementeer standaard beveiligings configuraties voor netwerk instellingen en netwerk resources die zijn gekoppeld aan uw Azure Data Factory-exemplaren met Azure Policy. Gebruik Azure Policy aliassen in de naam ruimten ' micro soft. DataFactory ' en ' micro soft. Network ' om aangepaste beleids regels te maken om de netwerk configuratie van uw Azure Data Factory instanties te controleren of af te dwingen. U kunt ook gebruikmaken van ingebouwde beleids definities met betrekking tot netwerken of uw Azure Data Factory-exemplaren, zoals: ' DDoS Protection Standard moet worden ingeschakeld '.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Voor beelden Azure Policy voor netwerken](../governance/policy/samples/index.md)

- [Een Azure Blueprint maken](../governance/blueprints/create-blueprint-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="110-document-traffic-configuration-rules"></a>1,10: configuratie regels voor het document verkeer

**Richt lijnen**: Gebruik labels voor bronnen die betrekking hebben op netwerk beveiliging en verkeers stroom voor uw Azure Data Factory-instanties voor het leveren van meta gegevens en logische organisaties.

Gebruik een van de ingebouwde Azure Policy definities met betrekking tot het labelen, zoals ' vereist label en de bijbehorende waarde ', om ervoor te zorgen dat alle resources met tags worden gemaakt en u op de hoogte moet zijn van bestaande niet-gelabelde resources.

U kunt Azure PowerShell of Azure CLI gebruiken om op basis van hun labels acties op te zoeken of uit te voeren op resources.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische hulpprogram ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

**Hulp**: Azure-activiteiten logboek gebruiken om netwerk resource configuraties te bewaken en wijzigingen te detecteren voor netwerk bronnen die betrekking hebben op uw Azure Data Factory exemplaren. Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer er wijzigingen in kritieke netwerk bronnen plaatsvinden.

- [Activiteiten logboek gebeurtenissen van Azure weer geven en ophalen](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Waarschuwingen maken in Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie [Azure Security Bench Mark: Logging and monitoring](../security/benchmarks/security-control-logging-monitoring.md)(Engelstalig) voor meer informatie.*

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

**Hulp**: opname logboeken via Azure monitor voor het verzamelen van beveiligings gegevens die zijn gegenereerd door Azure Data Factory. In Azure Monitor kunt u een query uitvoeren op de Log Analytics-werk ruimte die is geconfigureerd om uw Azure Data Factory activiteiten logboeken te ontvangen. Gebruik Azure Storage accounts voor langdurige logboek opslag of event hubs voor het exporteren van gegevens naar andere systemen.

U kunt ook gegevens in-of uitschakelen voor Azure Sentinel of een beveiligings incident en gebeurtenis beheer van derden (SIEM). U kunt Azure Data Factory ook integreren met Git om diverse voor delen van broncode beheer te benutten, zoals de mogelijkheid om wijzigingen op te sporen/te controleren en de mogelijkheid om wijzigingen te herstellen die fouten veroorzaken.

- [Diagnostische instellingen configureren](../azure-monitor/essentials/diagnostic-settings.md#create-in-azure-portal)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

- [Aan de slag met Azure Monitor en integratie van SIEM van derden](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Broncode beheer in Azure Data Factory](source-control.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources

**Richt lijnen**: voor het bijhouden van de controle van de controles van het Azure-activiteiten logboek kunt u de logboeken voor diagnostische gegevens over het besturings systeem inschakelen en de logbestanden verzenden naar een log Analytics-werk ruimte, Azure Event Hubs of Azure Storage account Met Azure-activiteiten logboek gegevens kunt u de ' What, wie en wanneer ' bepalen voor schrijf bewerkingen (PUT, POST, DELETE) die zijn uitgevoerd op het niveau van het controle vlak voor uw Azure-resources.

Diagnostische instellingen gebruiken voor het configureren van Diagnostische logboeken voor niet-reken resources in Azure Data Factory, zoals metrische gegevens en informatie over het uitvoeren van de pijp lijn. Azure Data Factory Stores-pijp lijn-gegevens gedurende 45 dagen worden uitgevoerd. Als u deze gegevens langer wilt bewaren, slaat u de diagnostische logboeken op in een opslag account voor controle of hand matige inspectie en geeft u de retentie tijd op in dagen.  U kunt de logboeken ook streamen naar Azure Event Hubs of de logboeken naar een Log Analytics-werk ruimte verzenden voor analyse.

- [Diagnostische instellingen voor Azure-activiteiten logboek inschakelen](../azure-monitor/essentials/activity-log.md)

- [Informatie over Azure Data Factory Diagnostische logboeken](monitor-using-azure-monitor.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: beveiligings logboeken verzamelen van besturings systemen

**Richt lijnen**: als u uw Integration runtime uitvoert op een virtuele machine (VM) van Azure, kunt u Azure monitor gebruiken om gegevens te verzamelen van de virtuele machine. Als u de extensie Log Analytics VM installeert, kan Azure Monitor gegevens verzamelen van uw Azure-Vm's. Azure Security Center kunt vervolgens bewaking van beveiligings logboeken voor Virtual Machines bieden. Gezien het volume van de gegevens dat het beveiligings logboek genereert, wordt het niet standaard opgeslagen. 

Als uw organisatie de gegevens van het beveiligings gebeurtenis logboek wil behouden, kan deze worden opgeslagen in een gegevensverzamelings tier, op welk moment een query kan worden uitgevoerd in Log Analytics.

- [Gegevens verzamelen van Azure-Virtual Machines in Azure Monitor](../azure-monitor/vm/quick-collect-azurevm.md)

- [Gegevens verzameling inschakelen in Azure Security Center](../security-center/security-center-enable-data-collection.md#data-collection-tier)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren

**Hulp**: Diagnostische instellingen inschakelen voor Azure Data Factory. Als u ervoor kiest om logboeken op te slaan in een Log Analytics-werk ruimte, moet u uw Bewaar periode voor Log Analytics werkruimte instellen volgens de nalevings voorschriften van uw organisatie. Gebruik Azure Storage-accounts voor lange termijn/archiverings opslag.

- [Diagnostische logboeken inschakelen in Azure Data Factory](monitor-using-azure-monitor.md)

- [Para meters voor het bewaren van Logboeken instellen voor Log Analytics-werk ruimten](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren

**Hulp**: Diagnostische instellingen voor Azure Data Factory inschakelen en logboeken naar een log Analytics werkruimte verzenden. Gebruik Log Analytics om uw logboeken te analyseren en te controleren op afwijkend gedrag en regel matig de resultaten te controleren. Zorg ervoor dat u ook diagnostische instellingen inschakelt voor gegevens archieven die betrekking hebben op uw Azure Data Factory-implementaties. Raadpleeg de beveiligings basislijn van elke service voor meer informatie over het inschakelen van diagnostische instellingen.

Als u uw Integration Runtime uitvoert in een virtuele Azure-machine (VM), schakelt u ook de diagnostische instellingen voor de virtuele machine in.

U kunt ook gegevens in-of uitschakelen voor Azure Sentinel of een SIEM van derden.

- [Log Analytics schema](./monitor-using-azure-monitor.md#schema-of-logs-and-events)

- [Gegevens verzamelen van een virtuele machine van Azure met Azure Monitor](../azure-monitor/vm/quick-collect-azurevm.md)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteiten

**Richt lijnen**: u kunt waarschuwingen over ondersteunde metrische gegevens in Data Factory genereren door te gaan naar de &amp; sectie metrische gegevens van waarschuwingen in azure monitor.

Diagnostische instellingen configureren voor Azure Data Factory en logboeken naar een Log Analytics werkruimte verzenden. Configureer in uw Log Analytics-werk ruimte de waarschuwingen die moeten worden uitgevoerd wanneer een vooraf gedefinieerde set voor waarden plaatsvindt. U kunt ook gegevens in-of uitschakelen voor Azure Sentinel of een SIEM van derden.

Zorg er bovendien voor dat u Diagnostische instellingen inschakelt voor services die betrekking hebben op uw gegevens archieven. U kunt de beveiligings basislijn van elke service raadplegen voor hulp.

- [Waarschuwingen in Azure Data Factory](./monitor-visually.md#alerts)

- [Pagina alle ondersteunde metrische gegevens](../azure-monitor/essentials/metrics-supported.md)

- [Waarschuwingen configureren in Log Analytics werk ruimte](../azure-monitor/alerts/alerts-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="28-centralize-anti-malware-logging"></a>2,8: registratie van anti-malware centraliseren

**Richt lijnen**: als u uw Integration runtime uitvoert op een virtuele machine van Azure, kunt u micro soft antimalware voor Azure Cloud Services gebruiken en virtual machines en uw virtuele machines zo configureren dat gebeurtenissen worden geregistreerd in een Azure Storage-account. Configureer een Log Analytics-werk ruimte om de gebeurtenissen van de opslag accounts op te nemen en maak waar nodig waarschuwingen. Volg de aanbevelingen in Azure Security Center: ' COMPUTE &amp; apps '. 

- [Micro soft antimalware configureren voor Cloud Services en Virtual Machines](../security/fundamentals/antimalware.md)

- [Bewaking op gast niveau inschakelen voor Virtual Machines](../cost-management-billing/cloudyn/azure-vm-extended-metrics.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="210-enable-command-line-audit-logging"></a>2,10: controle logboek registratie op opdracht regel inschakelen

**Richt lijnen**: als u uw Integration runtime uitvoert op een virtuele machine (VM) van Azure, kunt u de opdracht regel controle logboek registratie inschakelen. De Azure Security Center biedt bewaking van beveiligings logboeken voor Azure-Vm's.  Security Center richt micro soft monitoring agent in op alle ondersteunde virtuele machines van Azure en nieuwe die worden gemaakt als automatische inrichting is ingeschakeld, of u kunt de agent hand matig installeren.  De agent maakt de gebeurtenis 4688 voor het maken van processen en het veld CommandLine in gebeurtenis 4688 mogelijk. Nieuwe processen die op de virtuele machine worden gemaakt, worden vastgelegd door EventLog en gecontroleerd door de detectie services van Security Center.

- [Gegevensverzameling in Azure Security Center](../security-center/security-center-enable-data-collection.md#data-collection-tier)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [Azure Security Bench Mark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: een inventaris van beheerders accounts onderhouden

**Richt lijnen**: zorg er binnen Azure Data Factory voor dat u de gebruikers toegang regel matig bijhoudt en afstemt. Als u Data Factory-exemplaren wilt maken, moet het gebruikersaccount waarmee u zich bij Azure aanmeldt, lid zijn van de rol Inzender of Eigenaar, of moet dit een beheerder van het Azure-abonnement zijn.

Daarnaast heeft Azure Active Directory (Azure AD) op Tenant niveau ingebouwde rollen die expliciet moeten worden toegewezen en query's kunnen worden doorzocht. Gebruik de Azure AD Power shell-module om ad hoc-query's uit te voeren om accounts te detecteren die lid zijn van beheer groepen met beheerders toegang tot het beheer vlak van uw Azure Data Factory-exemplaren.

Azure AD is de aanbevolen methode voor het beheren van gebruikers toegang. Houd er rekening mee dat als u Integration Runtime uitvoert op een virtuele machine (VM) van Azure, uw VM ook lokale accounts kan hebben. Zowel lokale als domein accounts moeten worden gecontroleerd en beheerd, normaal gesp roken met een minimale footprint. Daarnaast adviseren we dat de privileged Identity Manager wordt gecontroleerd op de just-in-time-functie om de beschik baarheid van beheerders machtigingen te verminderen.

- [Rollen en machtigingen voor Azure Data Factory](concepts-roles-permissions.md)

- [Informatie over privileged Identity Manager](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Een directory-rol verkrijgen in azure AD met Power shell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Leden van een directory-rol in azure AD ophalen met Power shell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Informatie voor lokale accounts](../active-directory/devices/assign-local-admin.md#manage-the-device-administrator-role)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="32-change-default-passwords-where-applicable"></a>3,2: standaard wachtwoorden wijzigen indien van toepassing

**Hulp**: Azure Data Factory gebruikt Azure Active Directory (Azure AD) om toegang te bieden tot de Azure Portal en de Azure Data Factory-console. Azure AD heeft niet het concept van standaard wachtwoorden, maar u bent zelf verantwoordelijk voor het wijzigen of niet toestaan van standaard wachtwoorden voor aangepaste of externe toepassingen.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: speciale beheerders accounts gebruiken

**Richt lijnen**: Maak standaard procedures voor het gebruik van speciale beheerders accounts voor toegang tot het Azure Control-vlak (Azure Portal) en de Azure Data Factory-console. Gebruik Azure Security Center identiteits-en toegangs beheer om het aantal beheerders accounts in Azure Active Directory (Azure AD) te bewaken.

Daarnaast kunt u aanbevelingen van Azure Security Center of ingebouwde Azure-beleids regels gebruiken om u te helpen bij het bijhouden van specifieke beheerders accounts, zoals:
- Er moet meer dan één eigenaar zijn toegewezen aan uw abonnement
- Afgeschafte accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement

Als u uw Integration Runtime uitvoert op een virtuele machine van Azure, kunnen de beheerders accounts op Azure Virtual Machines ook worden geconfigureerd met Azure privileged Identity Manager (PIM). Azure privileged Identity Manager biedt verschillende opties, zoals just-in-time-uitbrei ding, multi-factor Authentication en delegerings opties, zodat machtigingen alleen beschikbaar zijn voor specifieke tijds frames en een tweede persoon moet goed keuren.

- [Inzicht in Azure Security Center identiteit en toegang](../security-center/security-center-identity-access.md)

- [Azure Policy gebruiken](../governance/policy/tutorials/create-and-manage.md)

- [Informatie over privileged Identity Manager](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Rollen en machtigingen voor Azure Data Factory](concepts-roles-permissions.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: gebruik Azure Active Directory eenmalige aanmelding (SSO)

**Richt lijnen**: gebruik een Azure app-registratie (Service-Principal) om een token op te halen dat door uw toepassing of functie kan worden gebruikt om uw Recovery Services kluizen te openen en ermee te werken.

- [Azure REST-Api's aanroepen](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Uw client toepassing (Service-Principal) registreren met Azure Active Directory (Azure AD)](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Informatie over Azure Recovery Services-API](/rest/api/recoveryservices/)

- [Informatie over REST API voor Azure Data Factory](/rest/api/datafactory/)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: multi-factor Authentication gebruiken voor alle op Azure Active Directory gebaseerde toegang

**Hulp**: Schakel Azure Active Directory (Azure AD) multi-factor Authentication in en volg Azure Security Center aanbevelingen voor identiteits-en toegangs beheer.

- [Multi-factor Authentication inschakelen in azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identiteit en toegang bewaken in Azure Security Center](../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: gebruik speciale machines (privileged Access workstations) voor alle beheer taken

**Richt lijnen**: gebruik privileged Access workstations (Paw) met multi-factor Authentication die is geconfigureerd om Azure-resources aan te melden en te configureren. 

- [Meer informatie over privileged Access workstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Multi-factor Authentication inschakelen in azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logboeken en waarschuwingen voor verdachte activiteiten van beheerders accounts

**Hulp**: gebruik Azure Active Directory (Azure AD)-beveiligings rapporten voor het genereren van Logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten in de omgeving worden uitgevoerd. Gebruik Azure Security Center om identiteits-en toegangs activiteiten te bewaken.

Als u uw Integration Runtime uitvoert op een virtuele machine (VM) van Azure, kunt u ook on-Board van uw VM naar Azure Sentinel. Microsoft Azure Sentinel is een schaalbaar, cloudoplossing voor Security Information Event Management (SIEM) en Security Orchestration Automated Response (SOAR). Azure Sentinel levert intelligente beveiligingsanalyses en bedreigingsinformatie voor de hele onderneming en is daardoor één oplossing voor waarschuwingsdetectie, zichtbaarheid van bedreigingen, proactieve opsporing en reactie op bedreigingen.

- [Azure AD-gebruikers identificeren die zijn gemarkeerd voor riskante activiteiten](../active-directory/identity-protection/overview-identity-protection.md)

- [Identiteits- en toegangsactiviteiten van gebruikers controleren in Azure Security Center](../security-center/security-center-identity-access.md)

- [Azure-Sentinel aan de trein](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Hulp**: gebruik benoemde locaties voor voorwaardelijke toegang om alleen toegang toe te staan vanaf specifieke logische groepen met IP-adresbereiken of landen/regio's.

- [Benoemde locaties configureren in azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory gebruiken

**Richt lijnen**: een Data Factory kan worden gekoppeld aan een beheerde identiteit voor Azure-resources die de specifieke Data Factory vertegenwoordigt. U kunt deze beheerde identiteit voor Azure SQL Database authenticatie gebruiken. De aangewezen Factory heeft toegang tot en kopiëren van gegevens van of naar uw data base met behulp van deze identiteit.

Als u uw Integration Runtime (IR) uitvoert op een virtuele machine van Azure, kunt u beheerde identiteiten gebruiken om te verifiëren bij elke service die ondersteuning biedt voor Azure Active Directory (Azure AD)-verificatie, inclusief Key Vault, zonder enige referenties in uw code. Uw code die wordt uitgevoerd op een virtuele machine, kan beheerde identiteit gebruiken om toegangs tokens aan te vragen voor services die ondersteuning bieden voor Azure AD-verificatie.

- [Een Azure AD-instantie maken en configureren](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Wat zijn beheerde identiteiten voor Azure-resources?](../active-directory/managed-identities-azure-resources/overview.md)

- [Gegevens in Azure SQL Database kopiëren en transformeren met behulp van Azure Data Factory](connector-azure-sql-database.md)

- [Azure AD-verificatie configureren en beheren met Azure SQL Database](../azure-sql/database/authentication-aad-configure.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regel matig gebruikers toegang controleren en afstemmen

**Hulp**: Azure Active Directory (Azure AD) bevat logboeken waarmee u verouderde accounts kunt detecteren. Daarnaast kunt u Azure Identity Access revisies gebruiken om groepslid maatschappen en de toegang tot bedrijfs toepassingen en roltoewijzingen op efficiënte wijze te beheren. Gebruikers toegang kan regel matig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben.

Als u de runtime-integratie uitvoert op een virtuele machine van Azure, moet u de lokale beveiligings groepen en gebruikers controleren om ervoor te zorgen dat er geen onverwachte accounts zijn die inbreuk kunnen maken op het systeem.

- [Beoordelingen over Azure Identity Access gebruiken](../active-directory/governance/access-reviews-overview.md)

- [Meer informatie over Azure AD-rapportage](../active-directory/reports-monitoring/index.yml)

- [Beoordelingen over Azure Identity Access gebruiken](../active-directory/governance/access-reviews-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: controle pogingen om toegang te krijgen tot gedeactiveerde referenties

**Hulp**: u hebt toegang tot Azure Active Directory (Azure AD) aanmeldings activiteit, controle en risico gebeurtenis logboek bronnen, waarmee u kunt integreren met elk Siem/bewakings programma. U kunt dit proces stroom lijnen door Diagnostische instellingen voor Azure AD-gebruikers accounts te maken en de audit logboeken en aanmeldings logboeken te verzenden naar een Log Analytics-werk ruimte. U kunt de gewenste logboek waarschuwingen configureren in Log Analytics.

Als u uw Integration Runtime uitvoert in een virtuele Azure-machine (VM), wordt deze VM naar Azure Sentinel. Microsoft Azure Sentinel is een schaalbaar, cloudoplossing voor Security Information Event Management (SIEM) en Security Orchestration Automated Response (SOAR). Azure Sentinel levert intelligente beveiligingsanalyses en bedreigingsinformatie voor de hele onderneming en is daardoor één oplossing voor waarschuwingsdetectie, zichtbaarheid van bedreigingen, proactieve opsporing en reactie op bedreigingen.

- [Azure-activiteitenlogboeken integreren in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Toegang tot Event Hubs resources autoriseren met behulp van Azure AD](../event-hubs/authorize-access-azure-active-directory.md)

- [Azure-Sentinel aan de trein](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: waarschuwing voor de afwijking van het aanmeldings gedrag van het account

**Hulp**: gebruik Azure Active Directory (Azure AD) als centraal verificatie-en autorisatie systeem voor uw Azure Data Factory resources, zoals Azure SQL database of Azure virtual machines. Voor de afwijking van het gedrag van de account aanmelding op het besturings vlak (de Azure Portal), gebruikt u de functies Azure AD Identity Protection en risico detectie om automatische antwoorden te configureren op gedetecteerde verdachte acties met betrekking tot gebruikers identiteiten. U kunt ook gegevens opnemen in azure Sentinel voor verder onderzoek.

- [Riskante Azure AD-aanmeldingen weergeven](../active-directory/identity-protection/overview-identity-protection.md)

- [Risico beleid voor identiteits beveiliging configureren en inschakelen](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

- [Azure AD-verificatie configureren en beheren met SQL](../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell)

- [Azure AD-verificatie voor Azure-SSIS Integration Runtime inschakelen](enable-aad-authentication-azure-ssis-ir.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: micro soft biedt toegang tot relevante klant gegevens tijdens ondersteunings scenario's

**Richt lijnen**: in ondersteunings Scenario's waarin micro soft toegang heeft tot klant gegevens, biedt Azure klanten-lockbox een interface waarmee klanten aanvragen voor gegevens toegang van klanten kunnen controleren en goed keuren of afwijzen. Hoewel Azure lockbox niet beschikbaar is voor Azure Data Factory, ondersteunt Azure lockbox Azure SQL Database en Azure Virtual Machines.

 

- [Klanten-lockbox begrijpen](../security/fundamentals/customer-lockbox-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Bench Mark: Data Protection](../security/benchmarks/security-control-data-protection.md)(Engelstalig) voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: een inventaris van gevoelige informatie onderhouden

**Hulp**: Tags gebruiken om Azure-resources te helpen bij het bijhouden of verwerken van gevoelige informatie.

Gebruik de functie voor gegevens detectie en-classificatie van Azure SQL Database. Gegevens detectie en-classificatie bieden geavanceerde mogelijkheden die zijn ingebouwd in Azure SQL Database voor het detecteren, classificeren, labelen van &amp; het beveiligen van gevoelige gegevens in uw data bases.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

- [Gegevens detectie en-classificatie gebruiken voor Azure SQL Server](../azure-sql/database/data-discovery-and-classification-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: systemen isoleren die gevoelige informatie opslaan of verwerken

**Richt lijnen**: afzonderlijke abonnementen en/of beheer groepen implementeren voor ontwikkeling, testen en productie. Integration Runtimes moeten worden gescheiden door de/subnet van het virtuele netwerk (VNet) en worden gelabeld.

U kunt ook privé-eind punten gebruiken om netwerk isolatie uit te voeren. Een privé-eindpunt in Azure is een netwerkinterface waarmee u privé en veilig verbinding maakt met een service die door Azure Private Link mogelijk wordt gemaakt. Private Endpoint maakt gebruik van een privé-IP-adres van uw VNet, waarbij de service effectief in uw VNet wordt geplaatst.

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Beheergroepen maken](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

- [Persoonlijke koppeling begrijpen](../private-link/private-endpoint-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: niet-geautoriseerde overdracht van gevoelige gegevens controleren en blok keren

**Richt lijnen**: voor gegevens bronnen (zoals Azure SQL database) het opslaan of verwerken van gevoelige informatie voor uw Azure Data Factory-implementatie, markeert u de gerelateerde resources als gevoelig voor het gebruik van tags.

Als privé-koppeling beschikbaar is, gebruikt u privé-eind punten om alle resources te beveiligen die aan uw Azure Data Factory-pijp lijn zijn gekoppeld. Verkeer tussen uw virtuele netwerk en de services wordt via het backbonenetwerk van Microsoft geleid, waarmee de risico's van het openbare internet worden vermeden. U kunt ook het risico van gegevens exfiltration verminderen door een strikte set uitgaande regels te configureren in een netwerk beveiligings groep (NSG) en die NSG te koppelen aan uw subnet.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

- [Een NSG maken met een beveiligings configuratie](../virtual-network/tutorial-filter-network-traffic.md) 

- [Persoonlijke Azure-koppeling](../private-link/private-link-overview.md)

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: alle gevoelige gegevens in de overdracht versleutelen

**Richt lijnen**: als de gegevens opslag in de Cloud https of TLS ondersteunt, worden alle gegevens overdrachten tussen services voor gegevens verplaatsing in Data Factory en een gegevens archief in de Cloud via Secure Channel https of TLS. Gebruikte TLS-versie is 1,2. 

Voor alle verbindingen met Azure SQL Database en Azure Synapse Analytics (voorheen SQL Data Warehouse) is versleuteling (SSL/TLS) vereist terwijl gegevens onderweg naar en van de Data Base worden verzonden. Wanneer u een pijp lijn ontwerpt met behulp van JSON, voegt u de eigenschap Encryption toe en stelt u deze in op True in de connection string. Voor Azure Storage kunt u HTTPS gebruiken in de connection string.

- [Informatie over versleuteling in transit in Azure Data Factory](data-movement-security-considerations.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: een actief detectie hulpprogramma gebruiken om gevoelige gegevens te identificeren

**Richt lijnen**: als u Azure Data Factory gebruikt voor het kopiëren en transformeren van uw Azure SQL database-exemplaren, gebruikt u de functie Azure SQL database gegevens detectie en-classificatie. Gegevens detectie en-classificatie bieden geavanceerde mogelijkheden die zijn ingebouwd in Azure SQL Database voor het detecteren, classificeren, labelen van &amp; het beveiligen van gevoelige gegevens in uw data bases.

De functies voor gegevens detectie en-classificatie zijn nog niet beschikbaar voor andere Azure-Services.

- [Gegevens detectie en-classificatie gebruiken voor Azure SQL Server](../azure-sql/database/data-discovery-and-classification-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: op rollen gebaseerd toegangs beheer gebruiken voor het beheren van de toegang tot bronnen

**Richt lijnen**: gebruik Azure RBAC (op rollen gebaseerd toegangs beheer) om de toegang tot het Azure Data Factory besturings vlak (de Azure Portal) te beheren.

Als u Data Factory-exemplaren wilt maken, moet het gebruikersaccount waarmee u zich bij Azure aanmeldt, lid zijn van de rol Inzender of Eigenaar, of moet dit een beheerder van het Azure-abonnement zijn.

Raadpleeg voor uw Data Factory gegevens bronnen, zoals Azure SQL Database, de beveiligings basislijn voor die service voor meer informatie over Azure RBAC.

- [Azure RBAC configureren](../role-based-access-control/role-assignments-portal.md)

- [Rollen en machtigingen voor Azure Data Factory](concepts-roles-permissions.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: voor komen dat gegevens verlies op basis van host wordt gebruikt voor het afdwingen van toegangs beheer

**Hulp**: de functies gegevens identificatie, classificatie en verlies preventie zijn nog niet beschikbaar voor Azure Storage of reken resources. Implementeer oplossingen van derden, indien nodig voor nalevings doeleinden.

Voor het onderliggende platform dat door micro soft wordt beheerd, behandelt micro soft alle inhoud van de klant als gevoelig en gaat u naar een fantastische lengte om te beschermen tegen verlies en bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: gevoelige informatie op rest versleutelen

**Richt lijnen**: we raden u aan het mechanisme voor gegevens versleuteling in te scha kelen voor gegevens archieven die betrekking hebben op uw Azure Data Factory-implementaties. U kunt de beveiligings basislijn voor die service raadplegen voor meer informatie over de versleuteling van gegevens in rust.

Als u uw Integration Runtime uitvoert op een virtuele machine van Azure, worden virtuele schijven op Windows Virtual Machines (VM) versleuteld met behulp van server versleuteling of Azure Disk Encryption (ADE). Azure Disk Encryption maakt gebruik van de BitLocker-functie van Windows voor het versleutelen van beheerde schijven met door de klant beheerde sleutels in de gast-VM. Versleuteling aan de server zijde met door de klant beheerde sleutels wordt verbeterd op ADE door u in staat te stellen alle typen besturings systemen en installatie kopieën voor uw virtuele machines te gebruiken door gegevens in de opslag service te versleutelen.

U kunt referenties of geheime waarden opslaan in een Azure Key Vault en deze gebruiken tijdens de uitvoering van de pijp lijn om uw activiteiten door te geven. U kunt ook referenties voor gegevens archieven en berekeningen in een Azure Key Vault opslaan. Azure Data Factory haalt de referenties op bij het uitvoeren van een activiteit die gebruikmaakt van de gegevens opslag/compute.

- [Meer informatie over versleuteling in de rest van Azure Data Factory](data-movement-security-considerations.md)

- [Versleuteling aan server zijde van Azure Managed disks](../virtual-machines/disk-encryption.md)

- [Azure Disk Encryption voor Windows-Vm's](../virtual-machines/windows/disk-encryption-overview.md)

- [Azure Key Vault geheimen gebruiken in pijplijn activiteiten](how-to-use-azure-key-vault-secrets-pipeline-activities.md) 

- [Referenties in Azure Key Vault](store-credentials-in-key-vault.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

**Hulp**: gebruik Azure monitor met het Azure-activiteiten logboek om waarschuwingen te maken wanneer wijzigingen worden doorgevoerd in azure Data Factory en gerelateerde resources.

- [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteiten logboek](../azure-monitor/alerts/alerts-activity-log.md)

- [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteiten logboek](../azure-monitor/alerts/alerts-activity-log.md)

- [Azure Storage-analyselogboeken](../storage/common/storage-analytics-logging.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie [Azure Security Bench Mark: beveiligingslek beheer](../security/benchmarks/security-control-vulnerability-management.md)voor meer informatie.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatische hulpprogram ma's voor het scannen van beveiligings problemen uitvoeren

**Richt lijnen**: als u Azure SQL database als een gegevens archief gebruikt, schakelt u geavanceerde gegevens beveiliging in voor Azure SQL database en volgt u de aanbevelingen van Azure Security Center over het uitvoeren van evaluatie van beveiligings problemen op uw Azure SQL-servers.

Als u uw Integration Runtime uitvoert op een virtuele machine (VM) van Azure, volgt u de aanbevelingen van Azure Security Center over het uitvoeren van de beveiligings evaluaties op uw Vm's.  Gebruik Azure-beveiliging aanbevolen of een oplossing van derden voor het uitvoeren van evaluatie van beveiligings problemen voor uw virtuele machines. 

- [Beveiligings evaluaties uitvoeren op Azure SQL Database](../azure-sql/database/sql-vulnerability-assessment.md)

- [Geavanceerde gegevens beveiliging inschakelen](../azure-sql/database/azure-defender-for-sql.md)

- [Aanbevelingen voor de evaluatie van Azure Security Center-beveiligings problemen implementeren](../security-center/deploy-vulnerability-assessment-vm.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: geautomatiseerde oplossing voor patch beheer voor besturings systemen implementeren

**Richt lijnen**: als u uw Integration runtime uitvoert op een virtuele Azure-machine (VM), gebruikt u de Azure updatebeheer-oplossing om updates en patches voor uw virtuele machines te beheren.  Updatebeheer is afhankelijk van de lokaal geconfigureerde update opslagplaats voor patches die worden ondersteund door Windows-systemen. Met hulpprogram ma's als System Center Updates Publisher (updates Publisher) kunt u aangepaste updates publiceren in Windows Server Update Services (WSUS). Met dit scenario kunnen Updatebeheer patches voor machines die gebruikmaken van Configuration Manager als update opslagplaats met software van derden.

Voor het onderliggende platform dat door micro soft wordt beheerd, behandelt micro soft alle inhoud van de klant als gevoelig en gaat u naar een fantastische lengte om te beschermen tegen verlies en bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

- [Updatebeheer oplossing in azure](../automation/update-management/overview.md)

- [Updates en patches voor uw Azure-VM's beheren](../automation/update-management/manage-updates-for-vm.md)

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Implementeer een oplossing voor geautomatiseerd patch beheer voor software titels van derden

**Richt lijnen**: als u uw Integration runtime uitvoert op een virtuele machine (VM) van Azure, kunt u een oplossing voor patch beheer van derden gebruiken.  U kunt de Azure Updatebeheer-oplossing gebruiken om updates en patches voor uw virtuele machines te beheren.  Updatebeheer is afhankelijk van de lokaal geconfigureerde update opslagplaats voor patches die worden ondersteund door Windows-systemen. Met hulpprogram ma's als System Center Updates Publisher (updates Publisher) kunt u aangepaste updates publiceren in Windows Server Update Services (WSUS). Met dit scenario kunnen Updatebeheer patches voor machines die gebruikmaken van Configuration Manager als update opslagplaats met software van derden. 

- [Updatebeheer oplossing in azure](../automation/update-management/overview.md)

- [Updates en patches voor uw Azure-VM's beheren](../automation/update-management/manage-updates-for-vm.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: vergelijken van back-to-back-problemen

**Richt lijnen**: als u uw Integration runtime uitvoert op een virtuele machine van Azure, exporteert scan resultaten met consistente intervallen en vergelijkt u de resultaten om te controleren of de beveiligings problemen zijn opgelost. Wanneer u aanbevelingen voor beveiligings beheer gebruikt die worden voorgesteld door Azure Security Center, kunt u in de portal van de geselecteerde oplossing draaien om historische scan gegevens weer te geven.

- [Meer informatie over geïntegreerde scanner voor beveiligings problemen voor virtuele machines](../security-center/deploy-vulnerability-assessment-vm.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: een risico classificatie proces gebruiken om prioriteit te geven aan het herstel van ontdekte beveiligings problemen

**Hulp**: als u uw Integration runtime uitvoert op een virtuele Azure-machine, kunt u de systeem eigen beveiligings probleem scanner gebruiken. De beveiligings scanner die in Azure Security Center is opgenomen, wordt aangedreven door Qualys. De scanner van Qualys is het toonaangevende hulp programma voor het real-time identificeren van beveiligings problemen in uw Azure Virtual Machines.

Wanneer Security Center beveiligings problemen identificeert, worden de bevindingen en gerelateerde informatie als aanbevelingen weer gegeven. De verwante informatie bevat herstels tappen, gerelateerde CVEs, CVSS-scores en meer. U kunt de geïdentificeerde beveiligings problemen voor een of meer abonnementen of voor een specifieke virtuele machine weer geven.

- [Geïntegreerde scanner voor beveiligings problemen voor virtuele machines](../security-center/deploy-vulnerability-assessment-vm.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie [Azure Security Bench Mark: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md)(Engelstalig) voor meer informatie.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatische Asset-detectie oplossing gebruiken

**Hulp**: Azure resource Graph gebruiken voor het opvragen/detecteren van alle resources (zoals compute, opslag, netwerk, poorten en protocollen enz.) binnen uw abonnement (en). Zorg ervoor dat de juiste (Lees-) machtigingen voor uw Tenant en alle Azure-abonnementen en resources in uw abonnementen inventariseren.

Hoewel klassieke Azure-resources kunnen worden gedetecteerd via resource grafiek, is het raadzaam om Azure Resource Manager resources te maken en te gebruiken.

- [Query's maken met Azure resource Graph](../governance/resource-graph/first-query-portal.md)

- [Uw Azure-abonnementen weer geven](/powershell/module/az.accounts/get-azsubscription)

- [Meer informatie over Azure RBAC](../role-based-access-control/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="62-maintain-asset-metadata"></a>6,2: meta gegevens van activa onderhouden

**Richt lijnen**: Tags Toep assen op Azure-resources die meta gegevens geven om ze logisch in een taxonomie te organiseren.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: niet-geautoriseerde Azure-resources verwijderen

**Richt lijnen**: Gebruik labels, beheer groepen en afzonderlijke abonnementen, waar nodig, om Azure-resources te organiseren en bij te houden. Sluit de inventaris regel matig af en zorg ervoor dat niet-geautoriseerde resources tijdig worden verwijderd uit het abonnement.

Gebruik Azure Policy bovendien om beperkingen te leggen voor het type resources dat kan worden gemaakt in klant abonnementen met behulp van de volgende ingebouwde beleids definities:

- Niet toegestane resourcetypen

- Toegestane brontypen

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Beheergroepen maken](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: de inventaris van goedgekeurde Azure-resources definiëren en onderhouden

**Hulp**: Definieer goedgekeurde Azure-resources en goedgekeurde software voor reken resources.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

**Hulp**: gebruik Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in uw abonnement (en). 

Gebruik Azure resource Graph voor het opvragen/detecteren van resources binnen hun abonnement (en).  Zorg ervoor dat alle Azure-resources die aanwezig zijn in de omgeving, zijn goedgekeurd.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Query's maken met Azure Graph](../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitor voor niet-goedgekeurde software toepassingen binnen reken resources

**Richt lijnen**: als u uw Integration runtime uitvoert in een virtuele Azure-machine (VM), maakt u gebruik van Azure virtual machine Inventory om het verzamelen van informatie over alle software op virtual machines te automatiseren. Azure Automation biedt volledige controle tijdens de implementatie, bewerkingen en het buiten gebruik stellen van werkbelastingen en bronnen.

Opmerking: de software naam, versie, uitgever en vernieuwings tijd zijn beschikbaar via de Azure Portal. Om toegang te krijgen tot de installatie datum en andere informatie, is de klant verplicht om diagnostische gegevens op gast niveau in te scha kelen en de Windows-gebeurtenis logboeken naar een Log Analytics-werk ruimte te brengen.

- [Een inleiding tot Azure Automation](../automation/automation-intro.md)

- [Azure VM-inventaris inschakelen](../automation/automation-tutorial-installed-software.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: niet-goedgekeurde Azure-resources en software toepassingen verwijderen

**Richt lijnen**: als u uw Integration runtime uitvoert op een virtuele machine van Azure, biedt Azure Automation volledige controle over de implementatie, het uitvoeren van bewerkingen en het buiten gebruik stellen van werk belastingen en resources.  U kunt Wijzigingen bijhouden gebruiken om alle software te identificeren die op Virtual Machines is geïnstalleerd. U kunt uw eigen proces implementeren of de configuratie van Azure Automation status gebruiken voor het verwijderen van onbevoegde software.

- [Een inleiding tot Azure Automation](../automation/automation-intro.md)

- [Wijzigingen in uw omgeving bijhouden met de Wijzigingen bijhouden oplossing](../automation/change-tracking/overview.md)

- [Overzicht van Azure Automation status configuratie](../automation/automation-dsc-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="68-use-only-approved-applications"></a>6,8: alleen goedgekeurde toepassingen gebruiken

**Richt lijnen**: als u uw Integration runtime uitvoert op een virtuele machine (VM) van Azure, moet u Azure Security Center adaptieve toepassings besturings elementen gebruiken om ervoor te zorgen dat alleen geautoriseerde software wordt uitgevoerd en alle niet-geautoriseerde software wordt geblokkeerd voor het uitvoeren van virtuele machines.

- [Azure Security Center adaptieve toepassings besturings elementen gebruiken](../security-center/security-center-adaptive-application.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="69-use-only-approved-azure-services"></a>6,9: alleen goedgekeurde Azure-Services gebruiken

**Hulp: gebruik** Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in klant abonnement (en) met de volgende ingebouwde beleids definities:
- Niet toegestane resourcetypen
- Toegestane brontypen

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een specifiek resource type weigeren met Azure Policy](../governance/policy/samples/index.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: een inventaris van goedgekeurde software titels onderhouden

**Richt lijnen**: adaptief toepassings beheer is een intelligente, geautomatiseerde en end-to-end oplossing van Azure Security Center waarmee u kunt bepalen welke toepassingen kunnen worden uitgevoerd op uw Azure-en niet-Azure-machines (Windows en Linux).  Implementeer een oplossing van derden als deze niet voldoet aan de vereisten van uw organisatie.

Houd er rekening mee dat dit alleen geldt als uw Integration Runtime wordt uitgevoerd op een virtuele Azure-machine.

- [Azure Security Center adaptieve toepassings besturings elementen gebruiken](../security-center/security-center-adaptive-application.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

**Hulp** bij het configureren van voorwaardelijke toegang van Azure om gebruikers de mogelijkheid te bieden om te communiceren met Azure Resource Manager door ' blok toegang ' te configureren voor de app Microsoft Azure management.

- [Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager te blok keren](../role-based-access-control/conditional-access-azure-management.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: de mogelijkheid van gebruikers om scripts uit te voeren binnen reken bronnen beperken

**Richt lijnen**: als u de runtime-integratie uitvoert op een virtuele machine van Azure, kunt u, afhankelijk van het type scripts, gebruikmaken van specifieke configuraties van het besturings systeem of bronnen van derden om de mogelijkheid van gebruikers om scripts in azure Compute-resources uit te voeren, te beperken. U kunt ook gebruikmaken van Azure Security Center adaptieve toepassings controles om ervoor te zorgen dat alleen geautoriseerde software wordt uitgevoerd en alle niet-geautoriseerde software wordt geblokkeerd voor uitvoering op Azure Virtual Machines.

- [De uitvoering van Power shell-scripts beheren in Windows-omgevingen](/powershell/module/microsoft.powershell.security/set-executionpolicy)

- [Azure Security Center adaptieve toepassings besturings elementen gebruiken](../security-center/security-center-adaptive-application.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: toepassingen met een hoog risico fysiek of logisch scheiden

**Richt lijnen**: toepassingen met een hoog risico die zijn geïmplementeerd in uw Azure-omgeving, kunnen worden geïsoleerd met virtuele netwerken, subnetten, abonnementen, beheer groepen, enzovoort en voldoende beveiligd met een Azure firewall, Web Application firewall (WAF) of netwerk beveiligings groep (NSG). 

- [Virtuele netwerken en virtuele machines in azure](../virtual-machines/network-overview.md)

- [Wat is Azure Firewall?](../firewall/overview.md)

- [Wat is Azure Web Application Firewall?](../web-application-firewall/overview.md)

- [Netwerkbeveiligingsgroepen](../virtual-network/network-security-groups-overview.md)

- [Wat is Azure Virtual Network?](../virtual-network/virtual-networks-overview.md)

- [Uw resources organiseren met Azure-beheergroepen](../governance/management-groups/overview.md)

- [Handleiding voor beslissingen over abonnementen](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [Azure Security Bench Mark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md)(Engelstalig) voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources

**Richt lijnen**: standaard beveiligings configuraties voor Azure Data Factory definiëren en implementeren met Azure Policy. Gebruik Azure Policy aliassen in de naam ruimte ' micro soft. DataFactory ' om aangepaste beleids regels te maken om de configuratie van uw Azure Data Factory instanties te controleren of af te dwingen.

- [Beschik bare Azure Policy aliassen weer geven](/powershell/module/az.resources/get-azpolicyalias)

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: veilige configuraties van besturings systemen instellen

**Richt lijnen**: als u de runtime-integratie uitvoert op een virtuele Azure-machine, gebruikt u Azure Security Center aanbeveling [beveiligings configuraties herstellen op uw virtual machines] om beveiligings configuraties voor alle reken resources te onderhouden.

- [Azure Security Center aanbevelingen bewaken](../security-center/security-center-recommendations.md)

- [Azure Security Center aanbevelingen herstellen](../security-center/security-center-remediate-recommendations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: Beveilig Azure-resource configuraties onderhouden

**Hulp**: gebruik Azure Policy [deny] en [implementeren indien niet aanwezig] voor het afdwingen van beveiligde instellingen voor uw Azure-resources.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy effecten begrijpen](../governance/policy/concepts/effects.md)

- [Informatie over het maken van Azure Resource Manager sjablonen](../virtual-machines/windows/ps-template.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: veilige configuraties van besturings systemen onderhouden

**Richt lijnen**: als u uw Integration runtime uitvoert op een virtuele machine (VM) van Azure, moet u er rekening mee houden dat er verschillende opties zijn voor het onderhouden van een veilige configuratie voor vm's voor implementatie:
- Azure Resource Manager sjablonen: Dit zijn JSON-bestanden die worden gebruikt voor het implementeren van een virtuele machine vanuit de Azure Portal, en aangepaste sjablonen moeten worden behouden. Micro soft voert het onderhoud uit op basis van deze sjablonen.
- Aangepaste virtuele harde schijf (VHD): in sommige gevallen is het vereist dat er aangepaste VHD-bestanden worden gebruikt, zoals bij het verwerken van complexe omgevingen die niet op een andere manier kunnen worden beheerd. 
- Azure Automation status configuratie: zodra het basis besturingssysteem is geïmplementeerd, kan dit worden gebruikt voor een gedetailleerdere controle van de instellingen en afgedwongen via het Automation-Framework.

In de meeste gevallen kunnen de micro soft-sjablonen voor virtuele machines in combi natie met de Azure Automation desired state Configuration worden geholpen bij het verg Roten en onderhouden van de beveiligings vereisten.

- [Informatie over het downloaden van de VM-sjabloon](/previous-versions/azure/virtual-machines/windows/download-template)

- [Informatie over het maken van Azure Resource Manager sjablonen](../virtual-machines/windows/ps-template.md)

- [Een aangepaste VM-VHD uploaden naar Azure](../virtual-machines/windows/upload-generalized-managed.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan

**Richt lijnen**: als u aangepaste Azure Policy definities gebruikt, kunt u Azure DevOps of Azure opslag plaatsen gebruiken om uw code veilig op te slaan en te beheren.

- [Code opslaan in azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Documentatie voor Azure opslag plaatsen](/azure/devops/repos/)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: aangepaste installatie kopieën van een besturings systeem veilig opslaan

**Richt lijnen**: als u aangepaste installatie kopieën gebruikt, moet u Azure RBAC (op rollen gebaseerd toegangs beheer) gebruiken om ervoor te zorgen dat alleen gemachtigde gebruikers toegang kunnen krijgen tot de installatie kopieën. Voor container installatie kopieën slaat u ze op in Azure Container Registry en maakt u gebruik van Azure RBAC om ervoor te zorgen dat alleen geautoriseerde gebruikers toegang hebben tot de installatie kopieën.

De rol Data Factory Inzender kan worden gebruikt om gegevens fabrieken te maken en te beheren, evenals onderliggende resources.

- [Meer informatie over Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md) 

- [Meer informatie over Azure RBAC voor Container Registry](../container-registry/container-registry-roles.md) 

- [Azure RBAC configureren](../role-based-access-control/quickstart-assign-role-user-portal.md)

- [Rollen en machtigingen voor Azure Data Factory](concepts-roles-permissions.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: hulpprogram ma's voor configuratie beheer voor Azure-resources implementeren

**Hulp**: gebruik ingebouwde Azure Policy definities en Azure Policy aliassen in de naam ruimte ' micro soft. DataFactory ' om aangepaste beleids regels te maken om systeem configuraties te Signa lering, te controleren en af te dwingen. Ontwikkel bovendien een proces en pijp lijn voor het beheren van beleids uitzonderingen.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: hulpprogram ma's voor configuratie beheer voor besturings systemen implementeren

**Hulp**: deze aanbeveling kan worden toegepast als uw Integration runtime wordt uitgevoerd op een virtuele Azure-machine. Azure Automation status configuratie is een configuratie beheer service voor DSC-knoop punten (desired state Configuration) in elke Cloud of on-premises Data Center. Hiermee kan de schaal baarheid van duizenden computers snel en eenvoudig worden uitgebreid vanaf een centrale, veilige locatie. U kunt eenvoudig computers onboarden, de declaratieve configuraties toewijzen en rapporten weer geven met de naleving van elke computer die u hebt opgegeven. 

- [Onboarding van machines voor beheer door Azure Automation status configuratie](../automation/automation-dsc-onboarding.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-resources implementeren

**Hulp**: gebruik ingebouwde Azure Policy definities en Azure Policy aliassen in de naam ruimte ' micro soft. DataFactory ' om aangepaste beleids regels te maken om systeem configuraties te Signa lering, te controleren en af te dwingen. Gebruik Azure Policy [audit], [deny] en [implementeren indien niet aanwezig] om automatisch configuraties af te dwingen voor uw Azure-resources.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: geautomatiseerde configuratie bewaking voor besturings systemen implementeren

**Hulp**: deze aanbeveling kan worden toegepast als uw Integration runtime wordt uitgevoerd op een virtuele Azure-machine. Azure Automation status configuratie is een configuratie beheer service voor DSC-knoop punten (desired state Configuration) in elke Cloud of on-premises Data Center. Hiermee kan de schaal baarheid van duizenden computers snel en eenvoudig worden uitgebreid vanaf een centrale, veilige locatie. U kunt eenvoudig computers onboarden, de declaratieve configuraties toewijzen en rapporten weer geven met de naleving van elke computer die u hebt opgegeven. 

- [Onboarding van machines voor beheer door Azure Automation status configuratie](../automation/automation-dsc-onboarding.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure-geheimen veilig beheren

**Hulp**: gebruik Managed Service Identity in combi natie met Azure Key Vault om het geheim beheer voor uw Cloud toepassingen te vereenvoudigen en te beveiligen.

U kunt ook referenties of geheime waarden opslaan in een Azure Key Vault en deze gebruiken tijdens de uitvoering van de pijp lijn om uw activiteiten door te geven. Zorg ervoor dat de functie voor voorlopig verwijderen is ingeschakeld.

- [Integratie met door Azure beheerde identiteiten](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Een Key Vault maken](../key-vault/secrets/quick-create-portal.md)

- [Verifiëren bij Key Vault](../key-vault/general/authentication.md)

- [Toegangs beleid voor Key Vault toewijzen](../key-vault/general/assign-access-policy-portal.md)

- [Azure Key Vault-geheimen gebruiken in pijplijnactiviteiten](how-to-use-azure-key-vault-secrets-pipeline-activities.md)

- [Voorlopig verwijderen in Azure Key Vault](../key-vault/general/soft-delete-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: identiteiten veilig en automatisch beheren

**Richt lijnen**: wanneer u een Data Factory maakt, kan er een beheerde identiteit worden gemaakt, samen met het maken van de fabriek. De beheerde identiteit is een beheerde toepassing die is geregistreerd voor Azure Active Directory (Azure AD) en vertegenwoordigt deze specifieke data factory.

- [Beheerde identiteit voor Azure Data Factory](data-factory-service-identity.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: onbedoelde referentie blootstelling elimineren

**Richt lijnen**: referentie scanner implementeren om referenties in code te identificeren. Door het gebruik van Credential Scanner worden gebruikers ook aangemoedigd om gedetecteerde referenties naar veiligere locaties, zoals Azure Key Vault, te verplaatsen.

- [Referentie scanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie [Azure Security Bench Mark: verdediging tegen schadelijke software](../security/benchmarks/security-control-malware-defense.md)voor meer informatie.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: centraal beheerde anti-malware-software gebruiken

**Richt lijnen**: als u uw Integration runtime uitvoert op een virtuele Azure-machine, kunt u micro soft antimalware voor Azure Windows virtual machines gebruiken om uw resources voortdurend te controleren en te beschermen. 

- [Micro soft antimalware configureren voor Cloud Services en Virtual Machines](../security/fundamentals/antimalware.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: scan bestanden die moeten worden geüpload naar niet-reken resources van Azure

**Hulp**: micro soft antimalware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services (bijvoorbeeld Azure app service), maar wordt niet uitgevoerd voor uw inhoud.

Scan vooraf bestanden die worden geüpload naar niet-reken resources van Azure, zoals App Service, Data Lake Storage, Blob Storage, enzovoort. 

Gebruik Azure Security Center bedreigings detectie voor gegevens Services om malware te detecteren die is geüpload naar opslag accounts. 

- [Micro soft antimalware voor Azure Cloud Services en Virtual Machines begrijpen](../security/fundamentals/antimalware.md)

- [Meer informatie over de detectie van bedreigingen van Azure Security Center voor gegevens Services](../security-center/azure-defender.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: controleren of anti-malware-software en hand tekeningen zijn bijgewerkt

**Richt lijnen**: wanneer micro soft antimalware voor Azure wordt geïmplementeerd, worden standaard automatisch de nieuwste hand tekening, platform en engine-updates geïnstalleerd. Volg de aanbevelingen in Azure Security Center: "COMPUTE &amp; apps" om ervoor te zorgen dat alle eind punten up-to-date zijn met de nieuwste hand tekeningen. Het Windows-besturings systeem kan verder worden beveiligd met extra beveiliging om het risico van aanvallen op virussen of malware te beperken met behulp van de micro soft Defender Advanced Threat Protection-Service die kan worden geïntegreerd met Azure Security Center.

- [Micro soft antimalware implementeren voor Azure Cloud Services en Virtual Machines](../security/fundamentals/antimalware.md)

- [Microsoft Defender Advanced Threat Protection](/windows/security/threat-protection/microsoft-defender-atp/onboard-configure)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="data-recovery"></a>Gegevensherstel

*Zie [Azure Security Bench Mark: Data Recovery](../security/benchmarks/security-control-data-recovery.md)(Engelstalig) voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zorg voor regel matige automatische back-ups

**Richt lijnen**: als u uw Integration runtime uitvoert op een virtuele machine (VM) van Azure, schakelt u Azure backup in en configureert u de VM, evenals de gewenste frequentie en bewaar periode voor automatische back-ups.

Voor uw gegevens archieven raadpleegt u de beveiligings basislijn van die service voor aanbevelingen voor het uitvoeren van regel matige, geautomatiseerde back-ups.

- [Een overzicht van Azure VM backup](../backup/backup-azure-vms-introduction.md)

- [Een back-up van een Azure VM maken op basis van de VM-instellingen](../backup/backup-azure-vms-first-look-arm.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: volledige back-ups van het systeem uitvoeren en een back-up maken van door de klant beheerde sleutels

**Hulp**: als u uw Integration runtime uitvoert in een virtuele Azure-machine (VM), schakelt u Azure backup en doel-Azure-vm's in, evenals de gewenste frequentie-en bewaar perioden. Maak een back-up van door de klant beheerde sleutels binnen Azure Key Vault.

Voor uw gegevens archieven raadpleegt u de beveiligings basislijn van die service voor aanbevelingen voor het uitvoeren van regel matige, geautomatiseerde back-ups.

- [Een overzicht van Azure VM backup](../backup/backup-azure-vms-introduction.md)

- [Hoe kan ik een back-up maken van sleutel kluis sleutels in azure?](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richt lijnen**: als u uw Integration runtime uitvoert op een virtuele machine van Azure, moet u ervoor zorgen dat er regel matig gegevens worden teruggezet in azure backup. Test zo nodig inhoud terug naar een geïsoleerd VLAN. Periodiek het herstellen testen van back-ups van door de klant beheerde sleutels.

Raadpleeg de beveiligings basislijn van de service voor informatie over het valideren van back-ups voor uw gegevens archieven.

- [Bestanden herstellen vanuit back-up van Azure virtual machine](../backup/backup-azure-restore-files-from-vm.md)

- [Sleutel kluis sleutels herstellen in azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zorg voor de bescherming van back-ups en door de klant beheerde sleutels

**Richt lijnen**: als u uw Integration runtime uitvoert op een virtuele Azure-machine (VM) en u een back-up hebt gemaakt van de vm met Azure backup, wordt uw virtuele machine in rust versleuteld met Storage service Encryption (SSE). Azure Backup kunt ook een back-up maken van virtuele Azure-machines die zijn versleuteld met behulp van Azure Disk Encryption. Azure Disk Encryption kan worden geïntegreerd met BitLocker-versleutelings sleutels (BEKs), die worden beveiligd in een sleutel kluis als geheimen. Azure Disk Encryption is ook geïntegreerd met Azure Key Vault Key Encryption Keys (KEKs). Schakel Soft-Delete in Key Vault in om sleutels te beschermen tegen onbedoelde of schadelijke verwijdering.

- [Voorlopig verwijderen voor Vm's](../backup/backup-azure-security-feature-cloud.md)

- [Azure Key Vault: overzicht van voorlopig verwijderen](../key-vault/general/soft-delete-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Bench Mark: Incident Response](../security/benchmarks/security-control-incident-response.md)(Engelstalig) voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10,1: een hand leiding voor reactie op incidenten maken

**Richtlijnen**: Stel voor uw organisatie een responshandleiding op voor gebruik bij incidenten. Zorg ervoor dat er schriftelijke responsplannen zijn waarin alle rollen van het personeel worden gedefinieerd, evenals alle fasen in het afhandelen/managen van incidenten, vanaf de detectie van het incident tot een evaluatie ervan achteraf.

- [Werk stroom automatisering configureren in Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Richt lijnen voor het bouwen van uw eigen beveiligings incident antwoord proces](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Micro soft Security Response Center anatomie van een incident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Klant kan ook gebruikmaken van de hand leiding voor de verwerking van het computer beveiligings incident van het NIST om hulp te bieden bij het maken van een eigen incident respons plan](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: een beoordelings procedure voor incidenten en prioriteits procedures maken

**Hulp**: Security Center wijst aan elke waarschuwing een Ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of de analyse die wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau waarvan er sprake is van schadelijke opzet achter de activiteit die tot de waarschuwing heeft geleid.

Daarnaast kunt u ook duidelijk abonnementen markeren (voor bijvoorbeeld productie, niet-productie) en maak een naamgevings systeem om Azure-resources duidelijk te identificeren en te categoriseren.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="103-test-security-response-procedures"></a>10,3: procedures voor beveiligings antwoorden testen

**Richt lijnen**: oefent oefeningen uit om de respons mogelijkheden van uw systeem op een gewone uitgebracht te testen. Stel vast waar zich zwakke plekken en hiaten bevinden, en wijzig zo nodig het plan.

- [Raadpleeg de publicatie van het NIST: hand leiding voor het testen, trainen en uitoefenen van Program Ma's voor IT-plannen en-mogelijkheden](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen configureren voor beveiligings incidenten

**Hulp**: contact gegevens van beveiligings incidenten worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat de gegevens van de klant zijn geopend door een onrecht matige of niet-gemachtigde partij.  Bekijk incidenten na het feit om te controleren of de problemen zijn opgelost.

- [De Azure Security Center Security-contact persoon instellen](../security-center/security-center-provide-security-contact-details.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: beveiligings waarschuwingen opnemen in uw reactie systeem van uw incident

**Richt lijnen**: uw Azure Security Center waarschuwingen en aanbevelingen exporteren met de functie continue export. Met doorlopend exporteren kunt u waarschuwingen en aanbevelingen hand matig of op een doorlopende manier exporteren. U kunt de Azure Security Center Data Connector gebruiken om de waarschuwingen naar Azure Sentinel te streamen.

- [Continue export configureren](../security-center/continuous-export.md)

- [Waarschuwingen streamen naar Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: de reactie op beveiligings waarschuwingen automatiseren

**Hulp**: gebruik de functie werk stroom automatisering in azure Security Center om automatisch reacties te activeren via ' Logic apps ' in beveiligings waarschuwingen en aanbevelingen.

- [Werk stroom automatisering en Logic Apps configureren](../security-center/workflow-automation.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie [Azure Security Bench Mark: Indringings tests en rode team oefeningen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)voor meer informatie.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: voert regel matig indringings tests van uw Azure-resources uit en zorgt voor herbemiddeling van alle essentiële beveiligings resultaten

**Richt lijnen**: Volg de stappen voor het testen van de Microsoft Cloud indringings regels om ervoor te zorgen dat uw indringings tests niet worden geschonden door het micro soft-beleid. Gebruik de strategie van Microsoft en de uitvoering van Red Teaming-activiteiten, en voer een penetratietest van de live site uit op basis van een infrastructuur, services en toepassingen die door Microsoft worden beheerd. 

- [Regels voor het inzetten van penetratietests](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

## <a name="next-steps"></a>Volgende stappen

- Zie [overzicht van Azure Security Bench Mark v2](../security/benchmarks/overview.md)
- Meer informatie over [Azure-beveiligingsbasislijnen](../security/benchmarks/security-baselines-overview.md)