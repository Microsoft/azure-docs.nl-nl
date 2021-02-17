---
title: Azure-beveiligings basislijn voor Azure Cloud Services
description: De beveiligings basislijn van Azure Cloud Services biedt procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-Bench Mark.
author: msmbaldwin
ms.service: cloud-services
ms.topic: conceptual
ms.date: 11/02/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 28be34e9323697759b2492d86b968f8e3d47a28e
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100572045"
---
# <a name="azure-security-baseline-for-azure-cloud-services"></a>Azure-beveiligings basislijn voor Azure Cloud Services

In deze beveiligings basislijn worden richt lijnen van de [Azure Security Bench Mark-versie 1,0](../security/benchmarks/overview-v1.md) ingesteld op Microsoft Azure Cloud Services. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen.
De inhoud wordt gegroepeerd op de **beveiligings controles** die zijn gedefinieerd door de Azure Security-benchmark en de bijbehorende richt lijnen die van toepassing zijn op Cloud Services. **Besturings elementen** die niet van toepassing zijn op Cloud Services, zijn uitgesloten.

 
Als u wilt zien hoe Cloud Services volledig is toegewezen aan de beveiligings benchmark van Azure, raadpleegt u het [volledige Cloud Services beveiligings basislijn toewijzings bestand](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Bench Mark: Network Security](../security/benchmarks/security-control-network-security.md)(Engelstalig) voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-resources in virtuele netwerken beveiligen

**Hulp**: Maak een klassieke Azure-Virtual Network met afzonderlijke open bare en privé-subnetten om isolatie af te dwingen op basis van vertrouwde poorten en IP-bereiken. Deze virtuele netwerken en subnetten moeten de klassieke Virtual Network (klassieke implementatie) resources zijn en niet van de huidige Azure Resource Manager resources.  

Verkeer toestaan of weigeren met behulp van een netwerk beveiligings groep, die regels voor toegangs beheer bevat op basis van de richting van het verkeer, het Protocol, het bron adres en de poort en het doel adres en-poort. De regels van een netwerk beveiligings groep kunnen op elk gewenst moment worden gewijzigd en wijzigingen worden toegepast op alle gekoppelde exemplaren.

Microsoft Azure Cloud Services (klassiek) kan niet in Azure Resource Manager virtuele netwerken worden geplaatst. Op Resource Manager gebaseerde virtuele netwerken en op klassieke implementaties gebaseerde virtuele netwerken kunnen echter worden verbonden via peering. 

- [Overzicht van de netwerk beveiligings groep](../virtual-network/network-security-groups-overview.md)

- [Peering van virtuele netwerken](./cloud-services-connectivity-and-networking-faq.md?preserve-view=true#how-can-i-use-azure-resource-manager-virtual-networks-with-cloud-services)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: de configuratie en het verkeer van virtuele netwerken, subnetten en Nic's bewaken en vastleggen

**Hulp**: documenteer uw Azure Cloud Services-configuratie en controleer deze op wijzigingen. Gebruik het configuratie bestand van de service om het aantal rolinstanties te bepalen dat moet worden geïmplementeerd voor elke rol in de service, de waarden van configuratie-instellingen en de vinger afdrukken voor alle certificaten die aan een rol zijn gekoppeld. 

Als de service deel uitmaakt van een virtueel netwerk, moeten configuratie-informatie voor het netwerk worden verstrekt in het service configuratie bestand, evenals in het configuratie bestand voor virtuele netwerken. De standaard extensie voor het service configuratie bestand is. cscfg. Houd er rekening mee dat Azure Policy wordt niet ondersteund voor klassieke implementaties voor het afdwingen van configuraties.

Stel de configuratie waarden van een Cloud service in het service configuratie bestand (. cscfg) en de definitie in een service definitie bestand (. csdef) in. Gebruik het service definitie bestand om het service model voor een toepassing te definiëren. Definieer de functies die beschikbaar zijn voor een Cloud service en geef ook de service-eind punten op. Registreer de configuratie voor Azure Cloud Services met het service configuratie bestand. Elke herconfiguratie kan worden uitgevoerd via het ServiceConfig. cscfg-bestand. 

De optionele NetworkTrafficRules element service-definitie bewaken, waarmee wordt beperkt welke rollen kunnen communiceren met opgegeven interne eind punten. Configureer het NetworkTrafficRules-knoop punt, een optioneel element in het service definitie bestand, om op te geven hoe rollen met elkaar moeten communiceren. Plaats limieten op de rollen die toegang hebben tot de interne eind punten van de specifieke rol.  Houd er rekening mee dat de service definitie niet kan worden gewijzigd. 

Schakel logboeken stroom van de netwerk beveiligings groep in en verzend de logboeken naar een Azure Storage account voor controle. Verzend de stroom logboeken naar een Log Analytics-werk ruimte en gebruik Traffic Analytics om inzicht te krijgen in verkeers patronen in uw Azure-Tenant. Enkele voor delen van Traffic Analytics zijn de mogelijkheid om netwerk activiteiten te visualiseren, HOTS pots en beveiligings Risico's te identificeren, verkeers patronen te begrijpen en netwerk configuraties te lokaliseren.

- [Azure Resource Manager vs. klassieke implementatie-inzicht in implementatie modellen en de status van uw resources](../azure-resource-manager/management/deployment-models.md)

- [Cloud Services configuratie bestand](schema-cscfg-file.md)

- [Lijst met services die door Azure Policy worden ondersteund](/cli/azure/azure-services-the-azure-cli-can-manage?preserve-view=)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="13-protect-critical-web-applications"></a>1,3: essentiële webtoepassingen beveiligen

**Richt lijnen**: micro soft maakt gebruik van de Transport Layer Security (TLS) protocol v 1.2 om gegevens te beveiligen wanneer het onderweg is tussen Azure Cloud Services en klanten. Micro soft-data centers onderhandelen over een TLS-verbinding met client systemen die verbinding maken met Azure-Services. TLS biedt sterke verificatie, privacy van berichten en integriteit (inschakelen van detectie van bericht manipulatie, onderscheping en vervalsing), interoperabiliteit, algoritme flexibiliteit en gemakkelijke implementatie en gebruik.

- [Grond beginselen van versleuteling](../security/fundamentals/encryption-overview.md)

- [TLS/SSL-certificaten configureren](cloud-services-configure-ssl-certificate-portal.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: communicatie met bekende schadelijke IP-adressen weigeren

**Hulp**: Azure-Cloud implementeert een multilaag netwerk beveiliging om de platform services te beveiligen tegen gedistribueerde Denial-of-service-aanvallen (DDoS). De Azure DDoS Protection maakt deel uit van het doorlopende bewakings proces van de Azure-Cloud, dat voortdurend wordt verbeterd door indringings tests. Deze DDoS Protection is ontworpen om niet alleen aanvallen van de buiten kant, maar ook van andere Azure-tenants te voor komen. 

Er zijn een aantal verschillende manieren om communicatie te blok keren of te weigeren naast beveiliging op platform niveau binnen Azure Cloud Services. Deze zijn: 

-  Een opstart taak maken om specifieke IP-adressen selectief te blok keren
-  Toegang tot een set opgegeven IP-adressen door een Azure-webfunctie beperken door uw IIS web.config-bestand te wijzigen

Voorkom binnenkomend verkeer naar de standaard-URL of de naam van uw Cloud Services, bijvoorbeeld *. cloudapp.net. Stel de host-header in op een aangepaste DNS-naam onder configuratie van site binding in het bestand met de Cloud Services definitie (*. csdef).

Configureer een regel voor het weigeren van regels voor klassieke abonnements beheerders toewijzingen. Nadat een intern eind punt is gedefinieerd, kan de communicatie standaard stromen van elke rol naar het interne eind punt van een rol zonder beperkingen. Als u de communicatie wilt beperken, moet u een NetworkTrafficRules-element toevoegen aan het element ServiceDefinition in het service definitie bestand.

- [Hoe kan ik binnenkomend verkeer naar de standaard-URL van mijn Cloud service blok keren/uitschakelen](./cloud-services-connectivity-and-networking-faq.md?preserve-view=true#how-can-i-blockdisable-incoming-traffic-to-the-default-url-of-my-cloud-service)

- [Azure DDOS-beveiliging](./cloud-services-connectivity-and-networking-faq.md?preserve-view=true#how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-might-indicate-a-malicious-attack-to-the-cloud-service)

- [Een specifiek IP-adres blok keren](cloud-services-startup-tasks-common.md#block-a-specific-ip-address)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="15-record-network-packets"></a>1,5: netwerk pakketten opnemen

**Hulp**: Azure Network Watcher, netwerk prestatie bewaking, diagnose en analyse service gebruiken waarmee Azure-netwerken kunnen worden bewaakt. De extensie van de virtuele machine van Network Watcher agent is een vereiste voor het vastleggen van netwerk verkeer op aanvraag en andere geavanceerde functionaliteit van Azure Virtual Machines. Installeer de extensie van de virtuele machine van de Network Watcher agent en schakel logboeken stroom voor de netwerk beveiligings groep in.

Logboek registratie van de stroom configureren in een netwerk beveiligings groep. Raadpleeg de informatie over het implementeren van de Network Watcher virtuele-machine-extensie op een bestaande virtuele machine die is geïmplementeerd via het klassieke implementatie model.

- [Logboek registratie van stromen configureren op een netwerk beveiligings groep](../virtual-machines/extensions/network-watcher-linux.md)

- [Ga voor meer informatie over het configureren van stroom logboeken naar](/cli/azure/azure-services-the-azure-cli-can-manage?preserve-view=)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: op netwerk gebaseerde inbreuk detectie/indringings systemen (ID'S/IP-adressen) implementeren

**Hulp**: Azure Cloud Services heeft geen ingebouwde ID'S of ip's-mogelijkheid. Klanten kunnen op basis van hun organisatorische vereisten een extra op het netwerk gebaseerde ID'S of IP'S van Azure Marketplace selecteren en implementeren. Wanneer u oplossingen van derden gebruikt, moet u ervoor zorgen dat u uw geselecteerde ID'S of IP'S-oplossing grondig test met Azure Cloud Services om de juiste werking en functionaliteit te garanderen.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall) 

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="17-manage-traffic-to-web-applications"></a>1,7: verkeer naar webtoepassingen beheren

**Richt lijnen**: Service certificaten die zijn gekoppeld aan Azure Cloud Services, zorgen voor veilige communicatie van en naar de service. Deze certificaten worden gedefinieerd in de definitie van de service en worden automatisch geïmplementeerd naar de virtuele machine waarop een exemplaar van een webfunctie wordt uitgevoerd. Een voor beeld: voor een webrole kunt u een service certificaat gebruiken dat een weer gegeven HTTPS-eind punt kan verifiëren. 

Als u het certificaat wilt bijwerken, hoeft u alleen een nieuw certificaat te uploaden en de vingerafdruk waarde in het service configuratie bestand te wijzigen.

Gebruik het TLS 1,2-protocol, de meest gebruikte methode voor het beveiligen van gegevens om vertrouwelijkheid en integriteits beveiliging te bieden. 

Voor het beveiligen van webtoepassingen en om deze te beveiligen tegen aanvallen zoals OWASP Top 10 kunt u een Azure Web Application firewall-Azure-toepassing Gateway implementeren voor het beveiligen van webtoepassingen. 

- [Service certificaten](cloud-services-certs-create.md)

- [TLS configureren voor een toepassing in azure](cloud-services-configure-ssl-certificate-portal.md)

- [Application Gateway implementeren](../application-gateway/quick-create-portal.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: standaard beveiligings configuraties voor netwerk apparaten onderhouden

**Hulp**: de beveiliging van uw Azure-Cloud Services configureren en controleren op wijzigingen. Het configuratie bestand van de service specificeert het aantal rolinstanties dat moet worden geïmplementeerd voor elke rol in de service, de waarden van de configuratie-instellingen en de vinger afdrukken voor alle certificaten die aan een rol zijn gekoppeld. 

Als uw service deel uitmaakt van een virtueel netwerk, moeten de configuratie gegevens voor het netwerk worden verstrekt in het service configuratie bestand en in het configuratie bestand voor virtuele netwerken. De standaard extensie voor het service configuratie bestand is. cscfg.

Houd er rekening mee dat Azure Policy wordt niet ondersteund met Azure Cloud Services voor het afdwingen van de configuratie.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="110-document-traffic-configuration-rules"></a>1,10: configuratie regels voor het document verkeer

**Hulp**: Azure-netwerk beveiligings groepen kunnen worden gebruikt voor het filteren van netwerk verkeer naar en van Azure-resources in een azure-Virtual Network. Een netwerk beveiligings groep bevat beveiligings regels voor het toestaan of weigeren van binnenkomend netwerk verkeer naar of het uitgaande netwerk verkeer van verschillende typen Azure-resources. Voor elke regel kunt u de bron en het doel, de poort en het protocol opgeven.

Gebruik het veld Beschrijving voor afzonderlijke regels voor netwerk beveiligings groepen in azure Cloud Services om de regels te documenteren die verkeer naar of van een netwerk toestaan.

- [Netwerk verkeer filteren met regels voor netwerk beveiligings groepen](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische hulpprogram ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

**Richt lijnen**: gebruik de ingebouwde eindpunt controle van Azure Traffic Manager en automatische functies voor endpoint failover. Ze helpen u bij het leveren van toepassingen met een hoge Beschik baarheid, die robuust zijn voor het aantal storingen van eind punten en Azure-regio's. Als u eindpunt bewaking wilt configureren, moet u bepaalde instellingen opgeven voor uw Traffic Manager profiel.

Verzamel inzicht van het activiteiten logboek, een platform logboek in azure, in gebeurtenissen op abonnements niveau. Het bevat informatie zoals wanneer een resource wordt gewijzigd of wanneer een virtuele machine wordt gestart. Bekijk het activiteiten logboek in het Azure Portal of haal vermeldingen op met Power shell en CLI. 

Een diagnostische instelling maken om het activiteiten logboek te verzenden naar Azure Monitor, Azure Event Hubs om buiten Azure te worden doorgestuurd of om te Azure Storage voor archivering. Configureer Azure Monitor voor meldings waarschuwingen wanneer kritieke resources in uw Azure-Cloud Services worden gewijzigd. 

- [Azure-activiteitenlogboek](../azure-monitor/essentials/activity-log.md)

- [Waarschuwingen voor activiteiten logboek maken, weer geven en beheren met behulp van Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

- [Traffic Manager bewaking](../traffic-manager/traffic-manager-monitoring.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie [Azure Security Bench Mark: Logging and monitoring](../security/benchmarks/security-control-logging-monitoring.md)(Engelstalig) voor meer informatie.*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: goedgekeurde tijd synchronisatie bronnen gebruiken

**Richt lijnen**: micro soft onderhoudt tijd bronnen voor Azure-resources voor Azure Cloud Services. Klanten moeten mogelijk een netwerk regel maken om toegang te bieden tot een tijd server die in hun omgeving wordt gebruikt, via poort 123 met het UDP-protocol.

- [NTP-server toegang](../firewall/protect-windows-virtual-desktop.md#additional-considerations)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Gedeeld

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

**Richt lijnen**: de gegevens van uw Cloud service streaming gebruiken via een programma met Azure Event hubs. Integreer en verzend al deze gegevens naar Azure Sentinel om uw logboeken te controleren en te controleren, of gebruik een SIEM van derden. Configureer voor het beheer van het centrale beveiligings logboek continue export van uw gekozen Azure Security Center gegevens naar Azure Event Hubs en stel de juiste connector in voor uw SIEM. Hier volgen enkele opties voor Azure Sentinel, waaronder hulpprogram ma's van derden:

- Azure-Sentinel-de systeem eigen Security Center-gegevens connector gebruiken
- Splunk: gebruik de Azure Monitor-invoeg toepassing voor Splunk
- IBM QRadar: een hand matig geconfigureerde logboek bron gebruiken
- ArcSight: gebruik SmartConnector

Raadpleeg de Azure Sentinel-documentatie voor meer informatie over beschik bare connectors met Azure Sentinel. 

- [Verbinding maken met gegevensbronnen](../sentinel/connect-data-sources.md)

- [Integreren met een SIEM](../security-center/continuous-export.md)

- [Diagnostische gegevens opslaan](diagnostics-extension-to-storage.md)

- [SIEM-integratie configureren via Azure Event Hubs](../security-center/continuous-export.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources

**Hulp**: Visual Studio configureren om Azure Diagnostics in te stellen voor het oplossen van problemen met Azure Cloud Services die systeem-en logboek gegevens op virtuele machines vastleggen, inclusief exemplaren van virtuele machines waarop uw Azure-Cloud Services wordt uitgevoerd. De diagnostische gegevens worden overgebracht naar een opslag account van uw keuze. Schakel diagnostische gegevens in azure Cloud Services-projecten in vóór de implementatie.

 
Bekijk de wijzigings geschiedenis voor sommige gebeurtenissen in het activiteiten logboek in Azure Monitor. Controleren welke wijzigingen zijn aangebracht tijdens een tijds periode van de gebeurtenis. Kies een gebeurtenis in het activiteiten logboek voor uitgebreidere inspectie met het tabblad wijzigings overzicht (preview). De diagnostische gegevens naar Application Insights verzenden wanneer u een Azure Cloud Services vanuit Visual Studio publiceert. Maak de Application Insights Azure-resource op dat moment of verzend de gegevens naar een bestaande Azure-resource. 

Azure Cloud Services kan worden bewaakt door Application Insights voor Beschik baarheid, prestaties, fouten en gebruik. Aangepaste grafieken kunnen worden toegevoegd aan Application Insights zodat u de gegevens kunt zien die het meest van belang zijn. Gegevens van rolinstantie kunnen worden verzameld met behulp van de Application Insights SDK in uw Azure Cloud Services-project. 

- [Diagnostische gegevens inschakelen in Visual Studio vóór de implementatie](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines?preserve-view=true#to-turn-on-diagnostics-in-visual-studio-before-deployment)

- [Wijzigings overzicht weer geven](../azure-monitor/essentials/activity-log.md#view-change-history)

- [Application Insights voor Azure-Cloud service (klassiek)](../azure-monitor/app/cloudservices.md)

- [Diagnostische gegevens instellen voor Azure-Cloud service (klassiek) en virtuele machines](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines?preserve-view=true&toc=%2fazure%2fcloud-services%2ftoc.json)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren

**Richt lijnen**: u kunt geavanceerde bewaking met Azure Cloud Services gebruiken, waarmee u extra metrische gegevens kunt bemonsteren en verzamelen met intervallen van vijf minuten, 1 uur en 12 uur. De geaggregeerde gegevens worden opgeslagen in het opslag account, in tabellen, en worden na 10 dagen verwijderd. Het gebruikte opslag account wordt echter geconfigureerd door de rol en u kunt verschillende opslag accounts voor verschillende rollen gebruiken. Dit is geconfigureerd met een connection string in de. csdef-en. cscfg-bestanden.

Houd er rekening mee dat geavanceerde bewaking gebruikmaakt van de Azure Diagnostics extensie (Application Insights SDK is optioneel) voor de rol die u wilt bewaken. De diagnostische uitbrei ding maakt gebruik van een configuratie bestand (per rol) met de naam diagnostische gegevens. wadcfgx om de bewaakte metrische gegevens van de diagnostische functies te configureren. Met de diagnostische extensie van Azure worden gegevens verzameld en opgeslagen in een Azure Storage-account. Deze instellingen worden geconfigureerd in de. wadcfgx-,. csdef-en. cscfg-bestanden.

- [Inleiding tot Cloud service monitoring](cloud-services-how-to-monitor.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren

**Hulp**: er zijn basis-of geavanceerde bewakings modi beschikbaar voor Azure Cloud Services. Azure Cloud Services verzamelt automatisch basis bewakings gegevens (CPU-percentage, netwerk in/uit en lees-en schrijf bewerkingen van de schijf) van een virtuele machine in de host. Bekijk de verzamelde bewakings gegevens op de pagina's overzicht en metrieken van een Cloud service in de Azure Portal. 

Schakel diagnostische gegevens in azure Cloud Services in voor het verzamelen van diagnostische informatie, zoals toepassings logboeken, prestatie meter items en meer, terwijl u de Azure Diagnostics extensie gebruikt. Schakel de diagnostische configuratie in of werk deze bij in een Cloud service die al wordt uitgevoerd met Set-AzureServiceDiagnosticsExtension cmdlet of implementeer automatisch een Cloud service met diagnostische extensie. Installeer desgewenst de Application Insights SDK. Verzend prestatie meter items naar Azure Monitor.

Met de diagnostische extensie van Azure worden gegevens verzameld en opgeslagen in een Azure Storage-account. Diagnostische gegevens overzetten naar de Microsoft Azure-opslagemulator of Azure Storage omdat deze niet permanent is opgeslagen. Eenmaal in de opslag kan het worden weer gegeven met een van de beschik bare hulpprogram ma's, zoals Server Explorer in Visual Studio, Microsoft Azure Storage Explorer Azure Management Studio. Configureer de metrische gegevens van de diagnose die moeten worden bewaakt met een configuratie bestand (per rol) met de naam Diagnostics. wadcfgx in de uitbrei ding voor diagnostische gegevens. 

- [Inleiding tot Cloud service monitoring](cloud-services-how-to-monitor.md)

- [Diagnostische gegevens inschakelen in een Werknemersrol-integreren met een SIEM](../security-center/continuous-export.md)

- [Diagnostische gegevens inschakelen in azure Cloud Services met behulp van Power shell](cloud-services-diagnostics-powershell.md)

- [Diagnostische gegevens opslaan en weergeven in Azure Storage](diagnostics-extension-to-storage.md?&amp;preserve-view=true)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteiten

**Richt lijnen**: u kunt Azure Cloud Services-logboek gegevens bewaken door te integreren met Azure Sentinel of met een Siem van derden door waarschuwingen in te scha kelen voor afwijkende activiteiten.

- [Integreren met een SIEM](../security-center/continuous-export.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="28-centralize-anti-malware-logging"></a>2,8: registratie van anti-malware centraliseren

**Richt lijnen**: micro soft antimalware voor Azure, beschermt Azure Cloud Services en virtuele machines. U hebt de mogelijkheid om ook beveiligings oplossingen van derden te implementeren, zoals fire walls van webtoepassingen, netwerk firewalls, antimalware, inbraak detectie en preventie systemen (ID'S of IP-adressen).

- [Wat zijn de functies en mogelijkheden die Azure Basic IP'S/ID'S en DDOS biedt](./cloud-services-configuration-and-management-faq.md?preserve-view=true#what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [Azure Security Bench Mark: identiteits-en toegangs beheer](../security/benchmarks/security-control-identity-access-control.md)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: een inventaris van beheerders accounts onderhouden

**Richt lijnen**: micro soft raadt u aan om de toegang tot Azure-resources te beheren met behulp van Azure op rollen gebaseerd toegangs beheer (Azure RBAC). Azure Cloud Services biedt echter geen ondersteuning voor het Azure RBAC-model, omdat het geen Azure Resource Manager-gebaseerde service is en u een klassiek abonnement moet gebruiken

Standaard zijn de accounts beheerder, service beheerder en Co-Administrator de drie klassieke abonnements beheerders rollen in Azure. 

Klassieke abonnementsbeheerders hebben volledige toegang tot het Azure-abonnement. Ze kunnen resources beheren met behulp van Azure Portal, API's van Azure Resource Manager en API's van het klassieke implementatiemodel. Het account dat wordt gebruikt voor registratie bij Azure wordt automatisch ingesteld als de accountbeheerder en de servicebeheerder. Aanvullende Co-Administrators kunnen later worden toegevoegd. 

De service beheerder en het Co-Administrators hebben gelijkwaardige toegang tot gebruikers die de rol van eigenaar (een Azure-rol) aan het abonnements bereik hebben toegewezen. Beheer Co-Administrators of Bekijk de service beheerder met behulp van het tabblad klassieke beheerders op het Azure Portal. 

Roltoewijzingen voor klassieke service beheerder en beheerders met Power shell weer geven met de opdracht:

Get-AzRoleAssignment-IncludeClassicAdministrators

Bekijk de verschillen tussen de beheer rollen van klassieke abonnementen. 

- [Verschillen tussen drie klassieke abonnements rollen voor abonnementen](../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: speciale beheerders accounts gebruiken

**Richt lijnen**: het wordt aanbevolen om standaard procedures voor het gebruik van specifieke beheerders accounts te maken op basis van de beschik bare rollen en de vereiste machtigingen voor het uitvoeren en beheren van de Azure Cloud Services-resources.

- [Verschillen tussen de beheerders rollen van het klassieke abonnement](../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: eenmalige aanmelding (SSO) met Azure Active Directory gebruiken

**Hulp**: Vermijd het beheren van afzonderlijke identiteiten voor toepassingen die worden uitgevoerd op Azure Cloud Services. Implementeer eenmalige aanmelding om te voor komen dat gebruikers meerdere identiteiten en referenties beheren.

- [Wat is eenmalige aanmelding (SSO)?](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: gebruik speciale machines (privileged Access workstations) voor alle beheer taken

**Richt lijnen**: het wordt aanbevolen een beveiligd, door Azure beheerd werk station (ook wel een privileged Access Workstation genoemd) te gebruiken voor beheer taken, waarvoor verhoogde bevoegdheden zijn vereist.

- [Meer informatie over veilige, door Azure beheerde werk stations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure AD MFA inschakelen](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Bench Mark: Data Protection](../security/benchmarks/security-control-data-protection.md)voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: een inventaris van gevoelige informatie onderhouden

**Richt lijnen**: gebruik de rest api's van de Azure Cloud service om uw Azure Cloud service-resources te inventariseren voor gevoelige informatie. Vraag de geïmplementeerde Cloud service bronnen uit om de configuratie-en pakket bronnen op te halen.

 Enkele Api's worden hieronder weer gegeven:

- Implementatie ophalen: met de bewerking voor het ophalen van de implementatie worden configuratie-informatie, status en systeem eigenschappen voor een implementatie geretourneerd.
- Pakket ophalen: met de bewerking pakket ophalen wordt een Cloud service pakket voor een implementatie opgehaald en worden de pakket bestanden opgeslagen in Microsoft Azure Blob-opslag
- Eigenschappen van Cloud service ophalen: met de bewerking eigenschappen van Cloud service ophalen worden eigenschappen opgehaald voor de opgegeven Cloud service

Raadpleeg de documentatie van de REST Api's van Azure Cloud service en maak een proces voor gegevens beveiliging van gevoelige informatie op basis van de vereisten van uw organisatie.

- [Implementatie ophalen](/rest/api/compute/cloudservices/rest-get-deployment)

- [Eigenschappen van Cloud service ophalen](/rest/api/compute/cloudservices/rest-get-cloud-service-properties)

- [Pakket ophalen](/rest/api/compute/cloudservices/rest-get-package)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: systemen isoleren die gevoelige informatie opslaan of verwerken

**Richt lijnen**: isolatie implementeren met afzonderlijke abonnementen en beheer groepen voor afzonderlijke beveiligings domeinen, zoals omgevings type en gegevens gevoeligheids niveau voor Azure Cloud Services.

U kunt ook de ' permissionLevel ' in het certificaat element van de Azure Cloud service bewerken om de toegangs machtigingen op te geven die aan de rollen processen worden gegeven. Als u wilt dat alleen verhoogde processen toegang hebben tot de persoonlijke sleutel, geeft u verhoogde machtigingen op. met de machtiging limitedOrElevated kunnen alle rollen processen toegang krijgen tot de persoonlijke sleutel. Mogelijke waarden zijn limitedOrElevated of verhoogd. De standaard waarde is limitedOrElevated.

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Beheer groepen maken](../governance/management-groups/create-management-group-portal.md)

- [WebRole-schema](schema-csdef-webrole.md#Certificate)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: niet-geautoriseerde overdracht van gevoelige gegevens controleren en blok keren

**Richt lijnen**: het is raadzaam om een oplossing van een derde partij te gebruiken uit Azure Marketplace in netwerk verbindingen om te controleren op niet-geautoriseerde overdracht van gevoelige informatie en om dergelijke overdrachten te blok keren terwijl u waarschuwt voor informatie beveiliging.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: alle gevoelige gegevens in de overdracht versleutelen

**Hulp**: TLS v2 configureren voor Azure Cloud Services. Gebruik de Azure Portal om het certificaat toe te voegen aan uw gefaseerde Azure Cloud Services-implementatie en voeg de certificaat gegevens toe aan de bestanden van de services van de service CSDEF en CSCFG. Verpakt uw toepassing opnieuw en werk uw gefaseerde implementatie bij om het nieuwe pakket te gebruiken. 

Gebruik service certificaten in azure die zijn gekoppeld aan Azure Cloud Services om beveiligde communicatie met en van de service mogelijk te maken. Geef een certificaat op waarmee een weer gegeven HTTPS-eind punt kan worden geverifieerd. Definieer service certificaten in de service definitie van de Cloud service en implementeer deze automatisch naar de virtuele machine, waarop een exemplaar van uw rol wordt uitgevoerd.

Verificatie met behulp van de beheer-API met beheer certificaten) Hiermee kunt u verifiëren met het klassieke implementatie model. Voor veel programma's en hulpprogramma's (zoals Visual Studio of de Azure SDK) worden deze certificaten gebruikt om de configuratie en implementatie van verschillende Azure-services te automatiseren. 

Voor aanvullende informatie biedt de Azure Service Management-API programmatische toegang tot de service management-functionaliteit die beschikbaar is via de Azure Portal. Azure SDK voor python kan worden gebruikt voor het beheren van Azure Cloud Services-en Azure Storage-accounts. De Azure SDK voor python verloopt de Service Management-API, een REST API. Alle API-bewerkingen worden uitgevoerd via TLS en wederzijds geverifieerd met behulp van X. 509 v3-certificaten. De beheer service kan worden geopend vanuit een service die in azure wordt uitgevoerd. Het kan ook rechtstreeks via internet worden geopend vanuit elke toepassing die een HTTPS-aanvraag kan verzenden en een HTTPS-antwoord kan ontvangen.

- [TLS configureren voor een toepassing in azure](cloud-services-configure-ssl-certificate-portal.md)

- [Service beheer van python gebruiken](cloud-services-python-how-to-use-service-management.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: een actief detectie hulpprogramma gebruiken om gevoelige gegevens te identificeren

**Richt lijn**: het wordt aanbevolen een actief detectie hulpprogramma van derden te gebruiken om alle gevoelige informatie te identificeren die is opgeslagen, verwerkt of verzonden door de technologie systemen van de organisatie, waaronder de locaties op locatie of bij een externe service provider, en vervolgens de gevoelige informatie-inventaris van de organisatie bij te werken.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: voor komen dat gegevens verlies op basis van host wordt gebruikt voor het afdwingen van toegangs beheer

**Richt lijnen**: niet van toepassing op de Cloud service (klassiek). Er wordt geen preventie van gegevens verlies afgedwongen.

Het is raadzaam om een hulp programma van derden te implementeren, zoals een geautomatiseerde oplossing voor het voor komen van gegevens verlies op basis van een host voor het afdwingen van toegangs beheer voor gegevens, zelfs wanneer gegevens worden gekopieerd uit een systeem.

Voor het onderliggende platform dat door micro soft wordt beheerd, behandelt micro soft alle inhoud van de klant als gevoelig en gaat u naar een fantastische lengte om te beschermen tegen verlies en bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens in azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: gevoelige informatie op rest versleutelen

**Hulp**: Azure Cloud Services biedt geen ondersteuning voor versleuteling-at-rest. Dit komt doordat Azure Cloud Services zijn ontworpen om stateless te zijn. Azure Cloud Services ondersteunen externe opslag, bijvoorbeeld Azure Storage, standaard, versleuteld op rest.  

De toepassings gegevens die zijn opgeslagen op de tijdelijke schijven, worden niet versleuteld. De klant is verantwoordelijk voor het beheren en versleutelen van deze gegevens, indien nodig.  

- [Meer informatie over versleuteling van data-at-rest in Azure](../security/fundamentals/encryption-atrest.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

**Richt lijnen**: u kunt klassieke metrische waarschuwingen in azure monitor gebruiken om een melding te ontvangen wanneer een van uw metrische gegevens een drempel waarde overschrijdt. Klassieke metrische waarschuwingen zijn een verouderde functie waarmee waarschuwingen alleen voor niet-dimensionale metrische gegevens kunnen worden gewaarschuwd. Er is een bestaande nieuwere functie met de naam metrische waarschuwingen, waarmee de functionaliteit van klassieke metrische waarschuwingen wordt verbeterd. 

Daarnaast kunt Application Insights Azure Cloud Services-apps bewaken voor Beschik baarheid, prestaties, fouten en gebruik. Dit maakt gebruik van gecombineerde gegevens van Application Insights Sdk's met Azure Diagnostics gegevens uit uw Azure-Cloud Services.

- [Klassieke metrische waarschuwingen maken, weer geven en beheren met behulp van Azure Monitor](../azure-monitor/alerts/alerts-classic-portal.md)

- [Overzicht van metrische waarschuwingen](../azure-monitor/alerts/alerts-metric-overview.md) 

- [Application Insights voor Azure-Cloud service (klassiek)](../azure-monitor/app/cloudservices.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie voor meer informatie de [Azure Security Bench Mark: beveiligingslek beheer](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: geautomatiseerde oplossing voor patch beheer voor besturings systemen implementeren

**Richt lijnen**: Houd er rekening mee dat deze informatie betrekking heeft op het Azure-gast besturingssysteem voor Azure Cloud Services Worker en webrollen met platform as a Service (PaaS). Het is echter niet van toepassing op Virtual Machines met Infrastructure as a Service (IaaS).

Standaard werkt Azure het gast besturingssysteem van de klant periodiek bij naar de meest recente ondersteunde installatie kopie in de besturingssysteem familie die ze hebben opgegeven in hun service configuratie (. cscfg), zoals Windows Server 2016.

Wanneer een klant een specifieke versie van het besturings systeem voor de Azure Cloud Services-implementatie kiest, worden automatisch updates van het besturings systeem uitgeschakeld en wordt de verantwoordelijkheid voor patches hersteld. De klant moet er zeker van zijn dat hun rolinstanties updates ontvangen, of ze kunnen hun toepassing bloot stellen aan beveiligings problemen.

- [Azure-gast besturingssysteem](cloud-services-guestos-msrc-releases.md)

- [Ondersteunings beleid voor Azure-gast besturingssystemen](cloud-services-guestos-retirement-policy.md)

- [Cloud service (klassiek) configureren](cloud-services-how-to-configure-portal.md)

- [De versie van het gast besturingssysteem beheren](cloud-services-how-to-configure-portal.md#manage-guest-os-version)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: een geautomatiseerde oplossing voor patch beheer implementeren voor titels van software van derden

**Hulp**: gebruik een oplossing voor patch beheer van derden. Klanten die al gebruikmaken van Configuration Manager in hun omgeving kunnen System Center Updates Publisher ook gebruiken om aangepaste updates te publiceren in Windows Server Update service. 

Op deze manier kunnen Updatebeheer patches voor machines die gebruikmaken van Configuration Manager als update opslagplaats met software van derden.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: een risico classificatie proces gebruiken om prioriteit te geven aan het herstel van ontdekte beveiligings problemen

**Richt lijnen**: het wordt aanbevolen voor een klant om het bereik van hun risico van een DDoS-aanval op gang te begrijpen. 

We suggereren deze scenario's:

- Welke nieuwe open bare Azure-resources moeten worden beschermd?
- Is er een Single Point of Failure in de service?
- Hoe kunnen services worden geïsoleerd om de impact van een aanval te beperken terwijl services beschikbaar blijven voor geldige klanten?
- Zijn er virtuele netwerken waar DDoS Protection Standard moet worden ingeschakeld, maar niet?
- Zijn mijn Services actief/actief met failover over meerdere regio's?

Ondersteunende documentatie:

- [Risico-evaluatie van uw Azure-resources](../security/fundamentals/ddos-best-practices.md#risk-evaluation-of-your-azure-resources)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="inventory-and-asset-management"></a>Inventarisatie en asset-management

*Zie [Azure Security Bench Mark: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md)voor meer informatie.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatische Asset-detectie oplossing gebruiken

**Richt lijnen**: niet van toepassing op Azure Cloud Services. Deze aanbeveling is van toepassing op IaaS Compute-resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: niet-geautoriseerde Azure-resources verwijderen

**Richt lijnen**: het wordt aanbevolen om de inventaris regel matig af te stemmen en ervoor te zorgen dat niet-geautoriseerde resources tijdig worden verwijderd uit het abonnement.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: een inventaris van goedgekeurde Azure-resources definiëren en onderhouden

**Hulp**: de klant moet goedgekeurde Azure-resources en goedgekeurde software definiëren voor reken resources.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

**Richt lijnen**: gebruik de functie voor adaptieve toepassings beheer, die beschikbaar is in azure Security Center. Het is een intelligente, geautomatiseerde end-to-end oplossing van Security Center waarmee u kunt bepalen welke toepassingen kunnen worden uitgevoerd op uw Windows-en Linux-, Azure-en niet-Azure-machines. Het helpt u ook bij het beschermen van uw computers tegen schadelijke software. 

Deze functie is beschikbaar voor zowel Azure-als niet-Azure-Windows (alle versies, klassieke of Azure Resource Manager) en Linux-machines.

Security Center gebruikt machine learning voor het analyseren van de toepassingen die op uw computers worden uitgevoerd en maakt een lijst met toegestane gegevens van deze intelligentie. Deze mogelijkheid vereenvoudigt het proces van het configureren en onderhouden van beleids regels voor het toestaan van toepassingen, zodat u het volgende kunt doen:
- Blok keer of Meld u aan bij pogingen om schadelijke toepassingen uit te voeren, met inbegrip van degenen die anders kunnen worden gemist door antimalware-oplossingen.

- Voldoen aan het beveiligingsbeleid van uw organisatie waarin alleen het gebruik van gelicentieerde software is toegestaan.
- Vermijden dat in uw omgeving ongewenste software wordt gebruikt.
- Vermijden dat oude en niet-ondersteunde apps worden uitgevoerd.
- Voorkomen dat bepaalde softwareprogramma's worden uitgevoerd die in uw organisatie niet zijn toegestaan.
- De IT-afdeling in staat stellen de toegang tot gevoelige gegevens te beheren via het gebruik van apps.

Meer informatie vindt u op de koppelingen waarnaar wordt verwezen.

- [Adaptieve toepassingsbesturingselementen](../security-center/security-center-adaptive-application.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitor voor niet-goedgekeurde software toepassingen binnen reken resources

**Richt lijnen**: gebruik de functie voor adaptieve toepassings beheer, die beschikbaar is in azure Security Center. Het is een intelligente, geautomatiseerde end-to-end oplossing van Security Center waarmee u kunt bepalen welke toepassingen kunnen worden uitgevoerd op uw Windows-en Linux-, Azure-en niet-Azure-machines. Het helpt u ook bij het beschermen van uw computers tegen schadelijke software. 

Deze functie is beschikbaar voor zowel Azure-als niet-Azure-Windows (alle versies, klassieke of Azure Resource Manager) en Linux-machines.

Security Center gebruikt machine learning voor het analyseren van de toepassingen die op uw computers worden uitgevoerd en maakt een lijst met toegestane gegevens van deze intelligentie. Deze mogelijkheid vereenvoudigt het proces van het configureren en onderhouden van beleids regels voor het toestaan van toepassingen, zodat u het volgende kunt doen:

- Blok keer of Meld u aan bij pogingen om schadelijke toepassingen uit te voeren, met inbegrip van degenen die anders kunnen worden gemist door antimalware-oplossingen.

- Voldoen aan het beveiligingsbeleid van uw organisatie waarin alleen het gebruik van gelicentieerde software is toegestaan.

- Vermijden dat in uw omgeving ongewenste software wordt gebruikt.

- Vermijden dat oude en niet-ondersteunde apps worden uitgevoerd.

- Voorkomen dat bepaalde softwareprogramma's worden uitgevoerd die in uw organisatie niet zijn toegestaan.

- De IT-afdeling in staat stellen de toegang tot gevoelige gegevens te beheren via het gebruik van apps.

Meer informatie vindt u op de koppelingen waarnaar wordt verwezen.

- [Adaptieve toepassingsbesturingselementen](../security-center/security-center-adaptive-application.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: niet-goedgekeurde Azure-resources en software toepassingen verwijderen

**Richt lijnen**: gebruik de functie voor adaptieve toepassings beheer, die beschikbaar is in azure Security Center. Het is een intelligente, geautomatiseerde end-to-end oplossing van Security Center waarmee u kunt bepalen welke toepassingen kunnen worden uitgevoerd op uw Windows-en Linux-, Azure-en niet-Azure-machines. Het helpt u ook bij het beschermen van uw computers tegen schadelijke software. 

Deze functie is beschikbaar voor zowel Azure-als niet-Azure-Windows (alle versies, klassieke of Azure Resource Manager) en Linux-machines.

Security Center gebruikt machine learning voor het analyseren van de toepassingen die op uw computers worden uitgevoerd en maakt een lijst met toegestane gegevens van deze intelligentie. Deze mogelijkheid vereenvoudigt het proces van het configureren en onderhouden van beleids regels voor het toestaan van toepassingen, zodat u het volgende kunt doen:

- Blok keer of Meld u aan bij pogingen om schadelijke toepassingen uit te voeren, met inbegrip van degenen die anders kunnen worden gemist door antimalware-oplossingen.

- Voldoen aan het beveiligingsbeleid van uw organisatie waarin alleen het gebruik van gelicentieerde software is toegestaan.

- Vermijden dat in uw omgeving ongewenste software wordt gebruikt.

- Vermijden dat oude en niet-ondersteunde apps worden uitgevoerd.

- Voorkomen dat bepaalde softwareprogramma's worden uitgevoerd die in uw organisatie niet zijn toegestaan.

- De IT-afdeling in staat stellen de toegang tot gevoelige gegevens te beheren via het gebruik van apps.

Meer informatie vindt u op de koppelingen waarnaar wordt verwezen.

- [Adaptieve toepassingsbesturingselementen](../security-center/security-center-adaptive-application.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="68-use-only-approved-applications"></a>6,8: alleen goedgekeurde toepassingen gebruiken

**Richt lijnen**: gebruik de functie voor adaptieve toepassings beheer, die beschikbaar is in azure Security Center. Het is een intelligente, geautomatiseerde end-to-end oplossing van Security Center waarmee u kunt bepalen welke toepassingen kunnen worden uitgevoerd op uw Windows-en Linux-, Azure-en niet-Azure-machines. Het helpt u ook bij het beschermen van uw computers tegen schadelijke software. 

Deze functie is beschikbaar voor zowel Azure-als niet-Azure-Windows (alle versies, klassieke of Azure Resource Manager) en Linux-machines.

Security Center gebruikt machine learning voor het analyseren van de toepassingen die op uw computers worden uitgevoerd en maakt een lijst met toegestane gegevens van deze intelligentie. Deze mogelijkheid vereenvoudigt het proces van het configureren en onderhouden van beleids regels voor het toestaan van toepassingen, zodat u het volgende kunt doen:

- Blok keer of Meld u aan bij pogingen om schadelijke toepassingen uit te voeren, met inbegrip van degenen die anders kunnen worden gemist door antimalware-oplossingen.

- Voldoen aan het beveiligingsbeleid van uw organisatie waarin alleen het gebruik van gelicentieerde software is toegestaan.

- Vermijden dat in uw omgeving ongewenste software wordt gebruikt.

- Vermijden dat oude en niet-ondersteunde apps worden uitgevoerd.

- Voorkomen dat bepaalde softwareprogramma's worden uitgevoerd die in uw organisatie niet zijn toegestaan.

- De IT-afdeling in staat stellen de toegang tot gevoelige gegevens te beheren via het gebruik van apps.

Meer informatie vindt u op de koppelingen waarnaar wordt verwezen.

- [Adaptieve toepassingsbesturingselementen](../security-center/security-center-adaptive-application.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: een inventaris van goedgekeurde software titels onderhouden

**Richt lijnen**: gebruik de functie voor adaptieve toepassings beheer, die beschikbaar is in azure Security Center. Het is een intelligente, geautomatiseerde end-to-end oplossing van Security Center waarmee u kunt bepalen welke toepassingen kunnen worden uitgevoerd op uw Windows-en Linux-, Azure-en niet-Azure-machines. Het helpt u ook bij het beschermen van uw computers tegen schadelijke software. 

Deze functie is beschikbaar voor zowel Azure-als niet-Azure-Windows (alle versies, klassieke of Azure Resource Manager) en Linux-machines.

Security Center gebruikt machine learning voor het analyseren van de toepassingen die op uw computers worden uitgevoerd en maakt een lijst met toegestane gegevens van deze intelligentie. Deze mogelijkheid vereenvoudigt het proces van het configureren en onderhouden van beleids regels voor het toestaan van toepassingen, zodat u het volgende kunt doen:
- Blok keer of Meld u aan bij pogingen om schadelijke toepassingen uit te voeren, met inbegrip van degenen die anders kunnen worden gemist door antimalware-oplossingen.

- Voldoen aan het beveiligingsbeleid van uw organisatie waarin alleen het gebruik van gelicentieerde software is toegestaan.
- Vermijden dat in uw omgeving ongewenste software wordt gebruikt.
- Vermijden dat oude en niet-ondersteunde apps worden uitgevoerd.
- Voorkomen dat bepaalde softwareprogramma's worden uitgevoerd die in uw organisatie niet zijn toegestaan.
- De IT-afdeling in staat stellen de toegang tot gevoelige gegevens te beheren via het gebruik van apps.

Meer informatie vindt u op de koppelingen waarnaar wordt verwezen.

- [Adaptieve toepassingsbesturingselementen](../security-center/security-center-adaptive-application.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: de mogelijkheid van gebruikers om scripts in reken bronnen uit te voeren, beperken

**Richt lijnen**: gebruik de functie voor adaptieve toepassings beheer, die beschikbaar is in azure Security Center. Het is een intelligente, geautomatiseerde end-to-end oplossing van Security Center waarmee u kunt bepalen welke toepassingen kunnen worden uitgevoerd op uw Windows-en Linux-, Azure-en niet-Azure-machines. Het helpt u ook bij het beschermen van uw computers tegen schadelijke software. 

Deze functie is beschikbaar voor zowel Azure-als niet-Azure-Windows (alle versies, klassieke of Azure Resource Manager) en Linux-machines.

Security Center gebruikt machine learning voor het analyseren van de toepassingen die op uw computers worden uitgevoerd en maakt een lijst met toegestane gegevens van deze intelligentie. Deze mogelijkheid vereenvoudigt het proces van het configureren en onderhouden van beleids regels voor het toestaan van toepassingen, zodat u het volgende kunt doen:

- Blok keer of Meld u aan bij pogingen om schadelijke toepassingen uit te voeren, met inbegrip van degenen die anders kunnen worden gemist door antimalware-oplossingen.

- Voldoen aan het beveiligingsbeleid van uw organisatie waarin alleen het gebruik van gelicentieerde software is toegestaan.

- Vermijden dat in uw omgeving ongewenste software wordt gebruikt.

- Vermijden dat oude en niet-ondersteunde apps worden uitgevoerd.

- Voorkomen dat bepaalde softwareprogramma's worden uitgevoerd die in uw organisatie niet zijn toegestaan.

- De IT-afdeling in staat stellen de toegang tot gevoelige gegevens te beheren via het gebruik van apps.

Meer informatie vindt u op de koppelingen waarnaar wordt verwezen.

- [Adaptieve toepassingsbesturingselementen](../security-center/security-center-adaptive-application.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: toepassingen met een hoog risico fysiek of logisch scheiden

**Richt lijnen**: voor gevoelige of hoogwaardige toepassingen met Azure Cloud Services, het implementeren van afzonderlijke abonnementen of beheer groepen om isolatie te bieden.

Een netwerk beveiligings groep gebruiken, een regel voor binnenkomende beveiliging maken, een service zoals http kiezen, een aangepaste poort kiezen, een prioriteit en naam geven. De prioriteit is van invloed op de volg orde waarin de regels worden toegepast, hoe lager de numerieke waarde is, hoe eerder de regel wordt toegepast. U moet uw netwerk beveiligings groep koppelen aan een subnet of een specifieke netwerk interface om het netwerk verkeer te isoleren of te segmenteren op basis van de behoeften van uw bedrijf.

Meer informatie vindt u op de koppelingen waarnaar wordt verwezen.

- [Zelf studie: netwerk verkeer filteren met een netwerk beveiligings groep met behulp van de Azure Portal](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [Azure Security Bench Mark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md)(Engelstalig) voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources

**Richt lijnen**: gebruik de aanbevelingen van Azure Security Center als een veilige configuratie basislijn voor uw Azure Cloud Services-resources. 

Kies op de Azure Portal Security Center, reken &amp; apps en Azure Cloud Services om de aanbevelingen te zien die van toepassing zijn op uw service bronnen.

- [Aanbevelingen voor beveiliging: een naslaggids](../security-center/recommendations-reference.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: Beveilig Azure-resource configuraties onderhouden

**Richt lijnen**: niet van toepassing op Azure Cloud Services. Het is gebaseerd op het klassieke implementatie model. Het is raadzaam om een oplossing van derden te gebruiken voor het onderhouden van beveiligde Azure-resource configuraties

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan

**Hulp**: in het configuratie bestand van de Azure-Cloud service worden de operationele kenmerken van een resource opgeslagen. U kunt een kopie van de configuratie bestanden opslaan in een beveiligd opslag account.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: hulpprogram ma's voor configuratie beheer voor Azure-resources implementeren

**Richt lijnen**: niet van toepassing op Azure Cloud Services. Het is gebaseerd op het klassieke implementatie model en kan niet worden beheerd door Azure Resource Manager configuratie hulpprogramma's op basis van implementatie.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: hulpprogram ma's voor configuratie beheer voor besturings systemen implementeren

**Richt lijnen**: niet van toepassing op Azure Cloud Services. Deze aanbeveling is van toepassing op IaaS-gebaseerde Compute-resources (Infrastructure as a Service).

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-resources implementeren

**Hulp**: gebruik Azure Security Center om basislijn scans voor uw Azure-resources uit te voeren.  

- [Aanbevelingen herstellen in Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: geautomatiseerde configuratie bewaking voor besturings systemen implementeren

**Richt lijnen**: kies in azure Security Center &amp; de functie Compute apps en volg de aanbevelingen voor virtuele machines, servers en containers.

- [Aanbevelingen van Azure Security Center voor containers](../security-center/container-security.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure-geheimen veilig beheren

**Hulp**: Azure Cloud Services is gebaseerd op een klassiek implementatie model en kan niet worden geïntegreerd met Azure Key Vault.

U kunt geheimen beveiligen, zoals referenties die worden gebruikt in azure Cloud Services, zodat u niet elke keer een wacht woord hoeft in te voeren. Geef een wacht woord voor onbewerkte tekst op om te beginnen met een beveiligde teken reeks met behulp van de Power shell-opdracht ConvertTo-SecureString. Converteer vervolgens deze beveiligde teken reeks naar een versleutelde standaard teken reeks met behulp van ConvertFrom-SecureString.  U kunt deze versleutelde standaard teken reeks nu opslaan in een bestand met behulp van set-content.

Daarnaast is het raadzaam om de persoonlijke sleutels op te slaan voor certificaten die worden gebruikt in azure Cloud Services naar een beveiligde opslag.

- [Extern bureaublad configureren vanuit Power shell](cloud-services-role-enable-remote-desktop-powershell.md#configure-remote-desktop-from-powershell)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: onbedoelde referentie blootstelling elimineren

**Hulp**: veilige geheimen zoals referenties die worden gebruikt in azure Cloud Services, zodat u niet elke keer een wacht woord hoeft in te voeren. 
 

Als u wilt beginnen, geeft u een wacht woord voor onbewerkte tekst op, wijzigt u dit in een beveiligde teken reeks met behulp van de opdracht ConvertTo-SecureString. Converteer vervolgens deze beveiligde teken reeks naar een versleutelde standaard teken reeks met behulp van ConvertFrom-SecureString. Sla deze versleutelde standaard teken reeks nu op in een bestand met behulp van Set-Content opdracht.

Sla de persoonlijke sleutels op voor certificaten die worden gebruikt in azure Cloud Services naar een beveiligde opslag locatie.

- [Extern bureaublad configureren vanuit Power shell](cloud-services-role-enable-remote-desktop-powershell.md#configure-remote-desktop-from-powershell)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie voor meer informatie de [Azure Security Bench Mark: beveiliging tegen schadelijke software](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: centraal beheerde antimalware-software gebruiken

**Hulp**: micro soft antimalware voor Azure is beschikbaar voor Azure Cloud Services en virtual machines. Het is een gratis real-time beveiliging waarmee u virussen, spyware en andere schadelijke software kunt herkennen en verwijderen. Er worden waarschuwingen gegenereerd wanneer bekende schadelijke of ongewenste software probeert zichzelf te installeren of uit te voeren op uw Azure-systemen. 

Gebruik de op Power shell gebaseerde antimalware-cmdlet om de antimalware-configuratie op te halen, met ' Get-AzureServiceAntimalwareConfig '.

Schakel de antimalware-uitbrei ding in met een Power shell-script in de opstart taak in azure Cloud Services.

Kies de functie adaptieve toepassings beheer in Azure Security Center, een intelligente, geautomatiseerde, end-to-end oplossing. Het helpt uw computers te beschermen tegen malware en kunt u de pogingen om schadelijke toepassingen uit te voeren, blok keren of waarschuwen, met inbegrip van de apparaten die anders kunnen worden gemist door antimalware-oplossingen.

- [Hoe kan ik een anti-malware-extensie voor mijn Azure Cloud Services op een geautomatiseerde manier toevoegen](./cloud-services-configuration-and-management-faq.md?preserve-view=true#how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way)

- [Implementatie Scenario's voor antimalware](../security/fundamentals/antimalware.md#antimalware-deployment-scenarios)

- [Adaptieve toepassingsbesturingselementen](../security-center/security-center-adaptive-application.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Bench Mark: Incident Response](../security/benchmarks/security-control-incident-response.md)(Engelstalig) voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10,1: een hand leiding voor reactie op incidenten maken

**Richtlijnen**: Stel voor uw organisatie een responshandleiding op voor gebruik bij incidenten. Zorg ervoor dat er schriftelijke responsplannen zijn waarin alle rollen van het personeel worden gedefinieerd, evenals alle fasen in het afhandelen/managen van incidenten, vanaf de detectie van het incident tot een evaluatie ervan achteraf.

- [Werk stroom automatisering configureren in Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Richt lijnen voor het bouwen van uw eigen beveiligings incident antwoord proces](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Micro soft Security Response Center anatomie van een incident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Klant kan ook gebruikmaken van de hand leiding voor de verwerking van het computer beveiligings incident van het NIST om hulp te bieden bij het maken van een eigen incident respons plan](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: een beoordelings procedure voor incidenten en prioriteits procedures maken

**Hulp**: Azure Security Center wijst aan elke waarschuwing een Ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of het analyse programma dat wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau dat er schadelijke bedoelingen zijn achter de activiteit die tot de waarschuwing heeft geleid. 

Markeer abonnementen duidelijk (bijvoorbeeld productie, niet-productie) en maak een naamgevings systeem om Azure-resources duidelijk te identificeren en te categoriseren.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="103-test-security-response-procedures"></a>10,3: procedures voor beveiligings antwoorden testen

**Richt lijnen**: oefent oefeningen uit om de respons mogelijkheden van uw systeem op een gewone uitgebracht te testen. Stel vast waar zich zwakke plekken en hiaten bevinden, en wijzig zo nodig het plan. 

- [Raadpleeg de publicatie van het NIST: hand leiding voor het testen, trainen en uitoefenen van Program Ma's voor IT-plannen en-mogelijkheden](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen configureren voor beveiligings incidenten

**Hulp**: contact gegevens van beveiligings incidenten worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat de gegevens van de klant zijn geopend door een onrecht matige of niet-gemachtigde partij. Bekijk incidenten na het feit om te controleren of de problemen zijn opgelost. 

- [De Azure Security Center Security-contact persoon instellen](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: beveiligings waarschuwingen opnemen in uw reactie systeem van uw incident

**Richt lijnen**: uw Azure Security Center waarschuwingen en aanbevelingen exporteren met de functie continue export. Met doorlopend exporteren kunt u waarschuwingen en aanbevelingen hand matig of op een doorlopende manier exporteren. U kunt de Security Center Data Connector gebruiken om de waarschuwingen naar Azure Sentinel te streamen. 

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