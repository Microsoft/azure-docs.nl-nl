---
title: Azure-beveiligingsbasislijn voor App Service
description: De App Service beveiligingsbasislijn biedt procedurele richtlijnen en resources voor het implementeren van de beveiligingsaanbevelingen die zijn opgegeven in de Azure Security Benchmark.
author: msmbaldwin
ms.service: app-service
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark, devx-track-azurepowershell
ms.openlocfilehash: d3e9d7d8cd8e5ffad74b02179994e299a59c8894
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833936"
---
# <a name="azure-security-baseline-for-app-service"></a>Azure-beveiligingsbasislijn voor App Service

De Azure-beveiligingsbasislijn voor App Service bevat aanbevelingen die u helpen de beveiligingsstatus van uw implementatie te verbeteren. De basislijn voor deze service is gebaseerd op versie [1.0](../security/benchmarks/overview-v1.md)van de Azure Security Benchmark. Deze biedt aanbevelingen voor het beveiligen van uw cloudoplossingen in Azure aan de hand van onze richtlijnen voor best practices. De inhoud wordt gegroepeerd op basis van de **beveiligingscontroles** die zijn gedefinieerd door de Azure Security Benchmark en de gerelateerde richtlijnen die van toepassing zijn op App Service. **Besturingselementen** die niet van App Service zijn uitgesloten.

Zie het volledige App Service toewijzingsbestand voor beveiligingsbasislijnen om te zien hoe App Service volledig is toegewezen aan de Azure Security [App Service-benchmark.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Benchmark: netwerkbeveiliging](../security/benchmarks/security-control-network-security.md) voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Azure-resources binnen virtuele netwerken beveiligen

**Richtlijnen:** wanneer u App Service gebruikt in de prijscategorie Isolated, ook wel een App Service Environment (ASE) genoemd, kunt u rechtstreeks implementeren in een subnet in uw Azure-Virtual Network. Gebruik netwerkbeveiligingsgroepen om uw Azure App Service Environment te beveiligen door inkomende en uitgaande verkeer naar resources in uw virtuele netwerk te blokkeren of om de toegang tot apps in een App Service Environment.

Netwerkbeveiligingsgroepen bevatten standaard een impliciete regel voor weigeren met de laagste prioriteit en vereisen dat u expliciete regels voor toestaan toevoegt. Voeg regels voor toestaan toe voor uw netwerkbeveiligingsgroep op basis van een netwerkbenadering met de minste bevoegdheden. De onderliggende virtuele machines die worden gebruikt voor het hosten van App Service Environment zijn niet rechtstreeks toegankelijk omdat ze zich in een door Microsoft beheerd abonnement.

Bebeveiligen van App Service Environment door verkeer te routeren via een Web Application Firewall (WAF) ingeschakeld Azure Application Gateway. Gebruik service-eindpunten in combinatie met de Application Gateway om het inkomende publicatieverkeer naar uw app te beveiligen.  

Gebruik in de multi-tenant App Service (een app die niet in de geïsoleerde laag staat) netwerkbeveiligingsgroepen om uitgaand verkeer van uw app te blokkeren. Ervoor zorgen dat uw apps toegang hebben tot resources in of via Virtual Network, met de functie Virtual Network Integration. Deze functie kan ook worden gebruikt om uitgaand verkeer naar openbare adressen van de app te blokkeren. Virtual Network-integratie kan niet worden gebruikt om inkomende toegang tot een app te bieden.  

Beveilig het inkomende verkeer naar uw app met:
- Toegangsbeperkingen: een reeks regels voor toestaan of weigeren waarmee binnenkomende toegang wordt bestuurd
- Service-eindpunten: hiermee kunt u inkomende verkeer van buiten opgegeven virtuele netwerken of subnetten weigeren
- Privé-eindpunten: stel uw app beschikbaar voor uw Virtual Network met een privé-IP-adres. Als de privé-eindpunten zijn ingeschakeld voor uw app, is deze niet meer toegankelijk via internet

Wanneer u Virtual Network-integratiefunctie met virtuele netwerken in dezelfde regio gebruikt, gebruikt u netwerkbeveiligingsgroepen en routetabellen met door de gebruiker gedefinieerde routes. Door de gebruiker gedefinieerde routes kunnen op het integratiesubnet worden geplaatst om uitgaand verkeer naar gebruik te verzenden.  

Overweeg het implementeren van een Azure Firewall om toepassings- en netwerkconnectiviteitsbeleid voor uw abonnementen en virtuele netwerken centraal te maken, af te dwingen en te loggen. Azure Firewall maakt gebruik van een statisch openbaar IP-adres voor virtuele netwerkbronnen, waarmee externe firewalls verkeer kunnen identificeren dat afkomstig is van uw virtuele netwerk. 

- [Een App Service Environment](environment/firewall-integration.md)

- [Open Web Application Security Project (OWASP) Top 10 beveiligingsproblemen](https://owasp.org/www-project-top-ten/)

- [Netwerkbeveiligingsgroepen](../virtual-network/network-security-groups-overview.md)

- [Een app integreren met een virtueel Azure-netwerk](web-sites-integrate-with-vnet.md)

- [Netwerkoverwegingen voor een App Service-omgeving](environment/network-info.md)

- [Een externe ASE maken](environment/create-external-ase.md)

- [Een interne ASE maken](environment/create-ilb-ase.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Voor waarschuwingen met betrekking tot dit besturingselement is mogelijk een [Azure Defender](/azure/security-center/azure-defender) nodig voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-1.md)]

**Azure Policy ingebouwde definities - Microsoft.Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.web-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: De configuratie en het verkeer van virtuele netwerken, subnetten en netwerkinterfaces bewaken en in een logboek plaatsen

**Richtlijnen:** aanbevelingen voor netwerkbeveiliging van Azure Security Center om netwerkresources en configuraties te beveiligen die betrekking hebben op uw App Service apps en API's.

Gebruik Azure Firewall om verkeer te verzenden en toepassings- en netwerkverbindingsbeleid voor abonnementen en virtuele netwerken centraal te maken, af te dwingen en te loggen. Azure Firewall maakt gebruik van een statisch openbaar IP-adres voor uw virtuele netwerkbronnen, waarmee externe firewalls verkeer kunnen identificeren dat afkomstig is van uw Virtual Network. De Azure Firewall service is ook volledig geïntegreerd met Azure Monitor voor logboekregistratie en analyse.

- [Azure Firewall Overzicht](../firewall/overview.md)

- [Informatie over netwerkbeveiliging die wordt geleverd door Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Bewaking en beveiliging van App Service](../security-center/defender-for-app-service-introduction.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor de aanbevelingen Security Center van [Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Voor waarschuwingen met betrekking tot dit besturingselement is mogelijk een [Azure Defender](/azure/security-center/azure-defender) nodig voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.2](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-2.md)]

### <a name="13-protect-critical-web-applications"></a>1.3: Kritieke webtoepassingen beveiligen

**Richtlijnen:** Een via internet toegankelijke app in een App Service Environment (ASE) beveiligen door:
- Een Web Application Firewall (WAF) implementeren met Azure Application Gateway vóór een app die is gericht op internet
- Toegangsbeperkingen gebruiken om het inkomende verkeer naar de Application Gateway
- De app beveiligen met Azure Active Directory (Azure AD) om verificatie te garanderen
- Stel de minimale TLS-versie in op 1.2
- De app instellen op alleen HTTPS

Al het verkeer van de toepassing uitgaand via een Azure Firewall en de logboeken bewaken. 

Een via internet toegankelijke app beveiligen in de multiten tenant App Service,(zoals, niet in de geïsoleerde laag)
- Een Web Application Firewall voor een app implementeren
- Toegangsbeperkingen of service-eindpunten gebruiken om het inkomende verkeer naar het Web Application Firewall (WAF)-apparaat te beveiligen
- De app beveiligen met Azure AD om verificatie te garanderen
- Stel de minimale TLS-versie in op 1.2
- De app instellen op alleen HTTPS
- Gebruik Integratie van virtueel netwerk en de app-instelling WEBSITE_VIRTUAL NETWORK_ROUTE_ALL al het uitgaande verkeer onderhevig te maken aan netwerkbeveiligingsgroepen en door de gebruiker gedefinieerde routes op het integratiesubnet.

Net als bij de App Service Environment-app, kunt u al het verkeer van de toepassing uitgaand laten Azure Firewall een apparaat en de logboeken in de app controleren.

Bekijk en volg bovendien de aanbevelingen in het document Een App Service Environment vergrendelen.

- [Een App Service Environment](environment/firewall-integration.md)

- [Azure Web Application Firewall op Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

- [Azure App Service toegangsbeperkingen](app-service-ip-restrictions.md)

- [WAF-waarschuwingen bijhouden en eenvoudig trends bewaken met Azure Monitor ](../azure-monitor/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Waarschuwingen met betrekking tot dit besturingselement vereisen mogelijk een [Azure Defender](/azure/security-center/azure-defender) voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 1.3](../../includes/policy/standards/asb/rp-controls/microsoft.web-1-3.md)]

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Communicatie met bekende schadelijke IP-adressen weigeren

**Richtlijnen:** beveilig de App Service Environment zoals beschreven in de documentatie Een App Service Environment vergrendelen. Pas de integrated Threat Intelligence-functionaliteit in Azure Security Center om communicatie met bekende schadelijke of ongebruikte openbare IP-adressen te weigeren. Gebruik Toegangsbeperkingen om het inkomende verkeer naar de Application Gateway. 

Beveilig de multiten tenant-App Service (een app die zich niet in een Geïsoleerde laag) met een openbaar internet-gericht eindpunt. Het staat alleen verkeer van een specifiek subnet binnen uw Virtual Network en blokkeert de rest. Gebruik Toegangsbeperkingen om netwerktoegangslijsten Access Control (IP-beperkingen) te configureren om toegestaan inkomende verkeer te vergrendelen.

Definieer prioriteit in de lijst met geordende toestaan of weigeren om netwerktoegang tot uw app te beheren. Deze lijst kan IP-adressen of Virtual Network s bevatten. Er bestaat een impliciete regel voor alles weigeren aan het einde van de lijst wanneer deze een of meer vermeldingen bevat. Deze mogelijkheid werkt met alle App Service gehoste werkbelastingen, waaronder Web Apps, API Apps, Linux-apps, Linux-container-apps en Functions. 

Gebruik service-eindpunten om de toegang tot uw web-app vanuit een Azure-Virtual Network. Beperk de toegang tot een multiten tenant App Service (een app die zich niet in een Geïsoleerde laag) van geselecteerde subnetten met service-eindpunten. 

- [Beperkingen van statische IP-adressen voor Azure App Service](app-service-ip-restrictions.md)

- [Azure Web Application Firewall op Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

- [Een Web Application Firewall (WAF) configureren voor App Service Environment](environment/app-service-app-service-environment-web-application-firewall.md)

- [Beveilig de AS-omgeving zoals beschreven in Vergrendelen van een App Service Environment](environment/firewall-integration.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor de aanbevelingen Security Center van [Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Waarschuwingen met betrekking tot dit besturingselement vereisen mogelijk een [Azure Defender](/azure/security-center/azure-defender) voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-4.md)]

### <a name="15-record-network-packets"></a>1.5: Netwerkpakketten registreren

**Richtlijnen:** bewaakt aanvragen en antwoorden die worden verzonden naar en van App Service apps met Security Center. Aanvallen tegen een webtoepassing kunnen worden bewaakt met behulp van een realtime Application Gateway met Web Application Firewall, ingeschakeld met geïntegreerde logboekregistratie van Azure Monitor om Web Application Firewall-waarschuwingen bij te houden en eenvoudig trends te bewaken.

- [Azure Web Application Firewall voor Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Waarschuwingen met betrekking tot dit besturingselement vereisen mogelijk een [Azure Defender](/azure/security-center/azure-defender) voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.5](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-5.md)]

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verkeer naar webtoepassingen beheren

**Richtlijnen:** Verkeer voor een app in een App Service Environment:

- Beveilig de App Service Environment zoals beschreven in Een App Service Environment
- Een Application Gateway met een Azure Web Application Firewall voor uw internet-apps
- Instellen dat de app alleen toegankelijk is via HTTPS

Verkeer beheren voor een via internet toegankelijke app in de multiten tenant App Service (niet in de geïsoleerde laag): 

- Een Application Gateway implementeren die Azure Web Application Firewall ingeschakeld voor uw internet-apps
- Gebruik toegangsbeperkingen of service-eindpunten om het inkomende verkeer naar de Web Application Firewall. De toegangsbeperkingen werken met alle App Service gehoste werkbelastingen, waaronder Web Apps, API Apps, Linux-apps, Linux-container-apps en Functions.

- Instellen dat de app alleen toegankelijk is via HTTPS
- Beperk de toegang tot App Service app met statische IP-beperkingen, zodat deze alleen verkeer van het VIP op een toepassingsgateway ontvangt als het enige adres met toegang.

Bekijk de koppelingen waarnaar wordt verwezen voor meer informatie.

- [Beperkingen van statische IP-adressen voor Azure App Service](app-service-ip-restrictions.md)

- [Azure Web Application Firewall op Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

- [End-to-end-TLS configureren met behulp van Application Gateway met de portal](../application-gateway/end-to-end-ssl-portal.md)

- [Beveilig de AS-omgeving zoals beschreven in Vergrendelen van een App Service](/azure/app-service/environment/firewall-integration)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: De complexiteit en administratieve overhead van netwerkbeveiligingsregels minimaliseren

**Richtlijnen:** App Service een aantal eindpunten die worden gebruikt voor het beheren van de service. Deze eindpuntadressen zijn ook opgenomen in de IP-servicetag AppServiceManagement. De tag AppServiceManagement wordt alleen gebruikt met een App Service Environment om dergelijk verkeer toe te staan. 

U kunt het verkeer voor de bijbehorende service toestaan of weigeren door de naam van de servicetag op te geven in het juiste bron- of doelveld van een regel. App Service binnenkomende adressen worden bijgespoord in de IP-servicetag AppService. Er is geen IP-servicetag die de uitgaande adressen bevat die door de App Service.

Microsoft beheert de adres voorvoegsels die worden omvat door de servicetag en werkt de servicetag automatisch bij wanneer adressen veranderen.

- [Servicetags voor virtuele netwerken](../virtual-network/service-tags-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Standaardbeveiligingsconfiguraties voor netwerkapparaten onderhouden

**Richtlijnen:** standaardbeveiligingsconfiguraties definiëren en implementeren voor netwerkinstellingen die betrekking hebben op uw App Service apps. 

Behoudt beveiligingsconfiguraties Azure Policy aliassen in de naamruimten Microsoft.Web en Microsoft.Network. Maak aangepaste beleidsregels voor het controleren of afdwingen van de netwerkconfiguratie van uw App Service apps. 

Gebruik ingebouwde beleidsdefinities voor App Service, zoals:
- De app moet gebruikmaken van een service-eindpunt voor een virtueel netwerk
- De app mag alleen toegankelijk zijn via HTTPS
- De minimale TLS-versie instellen op de huidige versie

Bekijk de koppelingen waarnaar wordt verwezen voor meer informatie.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [End-to-end-TLS configureren met behulp van Application Gateway met de portal](../application-gateway/end-to-end-ssl-portal.md)

- [Beveilig de AS-omgeving zoals beschreven in Vergrendelen van een App Service](/azure/app-service/environment/firewall-integration)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="110-document-traffic-configuration-rules"></a>1.10: Configuratieregels voor verkeer documenteren

**Richtlijnen:** Tags gebruiken voor netwerkbeveiligingsgroepen en andere gerelateerde resources, waaronder verkeersstromen in App Service.

Geef de bedrijfs behoeften, duur, en meer op, met het veld Beschrijving voor regels die verkeer van of naar een netwerk toestaan voor afzonderlijke regels voor netwerkbeveiligingsgroepen.

Pas een van de ingebouwde Azure Policy-definities toe die betrekking hebben op tageffecten, zoals 'Tag en waarde vereisen', om ervoor te zorgen dat alle resources worden gemaakt met tags en u op de hoogte te stellen van bestaande resources zonder tag. Gebruik Azure PowerShell of Azure CLI om resources op te zoeken of acties uit te voeren op basis van hun tags.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

- [Azure App Service toegangsbeperkingen](/azure/app-service/app-service-ip-restrictions)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Geautomatiseerde hulpprogramma's gebruiken om netwerkresourceconfiguraties te bewaken en wijzigingen te detecteren

**Richtlijnen:** Gebruik het Activiteitenlogboek van Azure om configuraties van netwerkresources te bewaken en wijzigingen in netwerkinstellingen en alle resources met betrekking tot App Service. 

Pas een van de verschillende Azure Policy ingebouwde definities toe voor App Service, zoals een beleid dat apps controleert op het gebruik van de eindpuntservice voor virtuele netwerken. Maak waarschuwingen binnen Azure Monitor te activeren wanneer er wijzigingen in kritieke netwerkinstellingen of -resources plaatsvinden. 

Bekijk gedetailleerde beveiligingswaarschuwingen en aanbevelingen in Security Center, in de portal of via programmatische hulpprogramma's. Exporteert deze informatie of verzendt deze naar andere bewakingshulpprogramma's in uw omgeving. Hulpprogramma's zijn beschikbaar om waarschuwingen en aanbevelingen handmatig of op een continue en continue manier te exporteren. Met deze hulpprogramma's kunt u het volgende doen:
 
- Continu exporteren naar een Log Analytics-werkruimte
- Continu exporteren naar Azure Event Hubs (voor integratie met SIEM's van derden)
- Exporteren naar een CSV-bestand (één keer)

Het is raadzaam om een proces te maken met geautomatiseerde hulpprogramma's voor het bewaken van netwerkresourceconfiguraties en het snel detecteren van wijzigingen.

- [Gebeurtenissen in het Azure-activiteitenlogboek weergeven en ophalen](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Waarschuwingen maken in Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

- [Beveiligingswaarschuwingen en aanbevelingen exporteren](../security-center/continuous-export.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie de Azure [Security Benchmark: Logging and Monitoring (Azure Security Benchmark: logboekregistratie en bewaking) voor meer informatie.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Centraal beheer van beveiligingslogboek configureren

**Richtlijnen:** integreer uw App Service Environment (ASE) met Azure Monitor logboeken te verzenden naar Azure Storage, Azure Event Hubs of Log Analytics. Schakel diagnostische instellingen voor Azure-activiteitenlogboek in voor controlelogboekregistratie van besturingsvlak. Beveiligingswaarschuwingen van Security Center worden gepubliceerd naar het Azure-activiteitenlogboek. Controleer azure-activiteitenlogboekgegevens, waarmee u kunt bepalen wat, wie en wanneer voor schrijfbewerkingen (PUT, POST, DELETE) die worden uitgevoerd op het niveau van het besturingsvlak voor Azure App Service en andere Azure-resources. Sla uw query's op voor toekomstig gebruik, maak queryresultaten vast aan Azure Dashboards en maak logboekwaarschuwingen. Gebruik ook de data access-REST API in Application Insights om programmatisch toegang te krijgen tot uw telemetrie.

Gebruik Microsoft Azure Sentinel, een schaalbaar, cloudeigen SIEM (Security Information Event Management) dat beschikbaar is om verbinding te maken met verschillende gegevensbronnen en connectors, op basis van uw bedrijfsvereisten. U kunt ook gegevens inschakelen en inschakelen voor een SIEM-systeem (Security Information Event Management) van derden, zoals Barracuda in Azure Marketplace.

- [Activiteiten van ASE voor logboekregistratie](./environment/using-an-ase.md#logging)

- [Diagnostische instellingen inschakelen voor Azure App Service](troubleshoot-diagnostic-logs.md)

- [Het inschakelen van Application Insights](../azure-monitor/app/app-insights-overview.md)

- [Telemetrie exporteren vanuit Application Insights](../azure-monitor/app/export-telemetry.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Auditlogregistratie voor Azure-resources inschakelen

**Richtlijnen:** Diagnostische instellingen voor Azure-activiteitenlogboek inschakelen voor controlelogboekregistratie van controlevlak van App Service. Verzend de logboeken naar een Log Analytics-werkruimte, Azure Event Hub of een Azure Storage account.

Het 'wat, wie en wanneer' voor schrijfbewerkingen (PUT, POST, DELETE) die op het niveau van het besturingsvlak worden uitgevoerd, kan worden bepaald met behulp van Azure-activiteitenlogboekgegevens voor App Service en andere Azure-resources.

Daarnaast biedt Azure Key Vault gecentraliseerd geheimbeheer met toegangsbeleid en controlegeschiedenis. 

- [Diagnostische instellingen inschakelen voor Azure-activiteitenlogboek](../azure-monitor/essentials/activity-log.md)

- [Diagnostische instellingen inschakelen voor Azure App Service](troubleshoot-diagnostic-logs.md)

- [Resource Manager-bewerkingen](../role-based-access-control/resource-provider-operations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Waarschuwingen met betrekking tot dit besturingselement vereisen mogelijk een [Azure Defender](/azure/security-center/azure-defender) voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Web:**

[!INCLUDE [Resource Policy for Microsoft.Web 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.web-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: Opslagretentie van beveiligingslogboek configureren

**Richtlijnen:** stel Azure Monitor logboekretentieperiode in voor de Log Analytics-werkruimten die zijn gekoppeld aan uw App Service-resources overeenkomstig de nalevingsvoorschriften van uw organisatie.
- [Retentieparameters voor logboeken instellen](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="26-monitor-and-review-logs"></a>2.6: Logboeken bewaken en controleren

**Richtlijnen:** Controleer de diagnostische instellingen voor het Azure-activiteitenlogboek in uw App Service resources met de logboeken die worden verzonden naar een Log Analytics-werkruimte. Voer query's uit in Log Analytics om termen te zoeken, trends te identificeren, patronen te analyseren en veel andere inzichten te bieden op basis van de verzamelde gegevens.

Gebruik Application Insights voor uw App Service-apps en om logboek-, prestatie- en foutgegevens te verzamelen. Bekijk de telemetriegegevens die door de Application Insights in de Azure Portal.

Als u een Web Application Firewall (WAF) hebt geïmplementeerd, kunt u aanvallen op uw App Service-apps bewaken met behulp van een realtime Web Application Firewall logboek. Het logboek is geïntegreerd met Azure Monitor voor het bijhouden van Web Application Firewall en het eenvoudig bewaken van trends.

Gebruik Azure Sentinel, een schaalbaar en cloudeigen SIEM (Security Information Event Management), om te integreren met verschillende gegevensbronnen en connectors, op basis van de vereisten. U kunt eventueel gegevens inschakelen en inschakelen in een externe oplossing voor het beheren van beveiligingsgegevens in de Azure Marketplace.

- [Diagnostische instellingen inschakelen voor Azure-activiteitenlogboek](../azure-monitor/essentials/activity-log.md)

- [Het inschakelen van Application Insights](../azure-monitor/app/app-insights-overview.md)

- [Uw App Service Environment integreren met de Azure Application Gateway (WAF)](environment/integrate-with-application-gateway.md)

- [Hoe kunt u de Azure Sentinel](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Waarschuwingen inschakelen voor afwijkende activiteiten

**Richtlijnen:** Configureer Security Center in uw Azure-abonnement en bekijk de gegenereerde waarschuwingen. Gebruik Azure Monitor om uw activiteitenlogboekgegevens op te halen naar een Event Hub, waar deze kunnen worden gelezen door een SIEM-oplossing (Security Information Event Management), zoals Azure Sentinel. 

Bemonitor aanvallen op uw App Service apps met behulp van een realtime Web Application Firewall logboek met een geïmplementeerde Azure Web Application Firewall (WAF). Het logboek is geïntegreerd met Azure Monitor om Web Application Firewall (WAF)-waarschuwingen bij te houden en eenvoudig trends te bewaken.

- [Uw App Service Environment integreren met de Azure Application Gateway (WAF)](environment/integrate-with-application-gateway.md)

- [Beveiligingswaarschuwingen en aanbevelingen exporteren](../security-center/continuous-export.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie de Azure [Security Benchmark: Identity and Access Control (Azure Security-benchmark: identiteit en Access Control).](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Een inventaris van beheerdersaccounts onderhouden

**Richtlijnen:** Azure Active Directory (Azure AD) heeft ingebouwde rollen die expliciet moeten worden toegewezen en query's kunnen uitvoeren. Gebruik de Azure AD PowerShell-module om ad-hocquery's uit te voeren om accounts te ontdekken die lid zijn van beheergroepen.

- [Leden van een directoryrol in Azure AD krijgen met PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

- [Beheerde identiteiten gebruiken voor App Service en Azure Functions](./overview-managed-identity.md?tabs=dotnet&context=azure%2factive-directory%2fmanaged-identities-azure-resources%2fcontext%2fmsi-context)

- [Azure-rollen toewijzen met behulp van de Azure Portal](../role-based-access-control/role-assignments-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Standaardwachtwoorden wijzigen, indien van toepassing

**Richtlijnen:** Azure Active Directory (Azure AD) heeft niet het concept van standaardwachtwoorden. Het biedt toegang tot de besturingsvlak App Service.

Over het algemeen moet u voorkomen dat u standaardwachtwoorden voor gebruikerstoegang implementeert bij het bouwen van uw eigen apps. Gebruik een van de id-providers die standaard beschikbaar zijn voor App Service, zoals Azure AD, Microsoft-account, Facebook, Google of Twitter.

Anonieme toegang uitschakelen, tenzij u deze moet ondersteunen. 

- [Id-providers zijn standaard beschikbaar in Azure App Service](./overview-authentication-authorization.md#identity-providers)

- [Verificatie en autorisatie in Azure App Service en Azure Functions](overview-authentication-authorization.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Toegewezen beheerdersaccounts gebruiken

**Richtlijnen:** Standaardprocedures voor het gebruik van toegewezen beheerdersaccounts maken. Gebruik de functies identiteits- en toegangsbeheer in Security Center het aantal beheerdersaccounts te bewaken en bij te houden. 

Gebruik aanbevelingen van Security Center of ingebouwde Azure-beleidsregels, zoals:

- Er moet meer dan één eigenaar zijn toegewezen aan uw abonnement. 
- Afgeschafte accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement

Maak een proces voor het bewaken van netwerkresourceconfiguraties en het detecteren van wijzigingen in beheerdersaccounts.

- [Identiteit en toegang Azure Security Center gebruiken](../security-center/security-center-identity-access.md)

- [Hoe u Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Meer informatie over het verlenen van gebruikerstoegang tot toepassingen](../role-based-access-control/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Eenmalige Azure Active Directory gebruiken

**Richtlijnen:** Verificatie App Service via Azure Active Directory (Azure AD). Het biedt een OAuth 2.0-service voor uw id-provider en biedt geautoriseerde toegang tot mobiele en webtoepassingen. 

App Service apps federatief identiteit gebruiken, waarbij een id-provider van derden de gebruikersidentiteiten en verificatiestroom voor u beheert. Deze id-providers zijn standaard beschikbaar:

- Azure AD
- Microsoft-account

- Facebook

- Google

- Twitter

Wanneer u verificatie en autorisatie inschakelen met een van deze providers, is het aanmeldings-eindpunt beschikbaar voor gebruikersverificatie en voor validatie van verificatietokens van de provider.

- [Verificatie en autorisatie in Azure App Service](./overview-authentication-authorization.md#identity-providers)

- [Meer informatie over verificatie en autorisatie in Azure App Service](overview-authentication-authorization.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Meervoudige verificatie gebruiken voor alle Azure Active Directory op basis van toegang

**Richtlijnen:** Schakel de functie voor meervoudige verificatie in Azure Active Directory (Azure AD) in en volg de aanbevelingen voor identiteits- en toegangsbeheer in Security Center.

Meervoudige verificatie implementeren voor Azure AD. Beheerders moeten ervoor zorgen dat de abonnementsaccounts in de portal zijn beveiligd. Het abonnement is kwetsbaar voor aanvallen omdat het de resources beheert die u hebt gemaakt. 

- [Meervoudige verificatie in Azure Security](/previous-versions/azure/security/develop/secure-aad-app)

- [Meervoudige verificatie inschakelen in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identiteit en toegang binnen uw Azure Security Center](../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: Beveiligde, door Azure beheerde werkstations gebruiken voor beheertaken

**Richtlijnen:** Paw (Privileged Access Workstations) gebruiken met meervoudige verificatie die is geconfigureerd voor aanmelden bij en configureren van Azure-resources. 

- [Meer informatie over Privileged Access Workstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Meervoudige verificatie inschakelen in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Logboeken en waarschuwingen voor verdachte activiteiten van beheerdersaccounts

**Richtlijnen:** gebruik Privileged Identity Management (PIM) in Azure Active Directory (Azure AD) voor het genereren van logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten in de omgeving plaatsvinden.

Gebruik bovendien Azure AD-risicodetecties om waarschuwingen en rapporten over riskant gebruikersgedrag weer te geven.

Bedreigingsbeveiliging in Security Center biedt uitgebreide beveiliging voor uw omgeving, waaronder beveiliging tegen bedreigingen voor Azure-rekenresources, zoals Windows-machines, Linux-machines, App Service en Azure-containers.

- [Een Privileged Identity Management (PIM) implementeren](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Inzicht in Azure AD-risicodetecties](../active-directory/identity-protection/overview-identity-protection.md)

- [Bedreigingsbeveiliging voor Azure-rekenresources](../security-center/azure-defender.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Richtlijnen:** gebruik benoemde locaties voor voorwaardelijke toegang om toegang tot de Azure Portal alleen toe te staan vanuit specifieke logische groeperingen van IP-adresbereiken, landen of regio's.

- [Benoemde locaties configureren in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="39-use-azure-active-directory"></a>3.9: Gebruik Azure Active Directory

**Richtlijnen:** gebruik Azure Active Directory (Azure AD) als het centrale verificatie- en autorisatiesysteem voor uw App Service apps. Azure AD beveiligt gegevens met behulp van sterke versleuteling voor data-at-rest en in transit en ook salts, hashes en veilig opgeslagen gebruikersreferenties.

- [Uw apps voor Azure App Service configureren voor het gebruik van Azure AD-aanmelding](configure-authentication-provider-aad.md)

- [Een Azure AD-instantie maken en configureren](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmatig gebruikerstoegang controleren en afstemmen

**Richtlijnen:** Verouderde accounts met de logboeken van Azure Active Directory (Azure AD) ontdekken. Gebruik Azure Identity Access Reviews om groepslidmaatschap en toegang tot bedrijfstoepassingen, evenals roltoewijzingen, efficiënt te beheren. Controleer regelmatig de gebruikerstoegang om ervoor te zorgen dat alleen de beoogde gebruikers toegang hebben. 

- [Inzicht in Azure AD-rapportage](../active-directory/reports-monitoring/index.yml)

- [Toegangsbeoordelingen voor Azure-identiteiten gebruiken](../active-directory/governance/access-reviews-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Pogingen tot toegang tot gedeactiveerde referenties controleren

**Richtlijnen:** gebruik Azure Active Directory (Azure AD) als het centrale verificatie- en autorisatiesysteem voor uw App Service apps. Azure AD beveiligt gegevens met behulp van sterke versleuteling voor data-at-rest en in-transit, salts, hashes en slaat gebruikersreferenties veilig op.

Met toegang tot bronnen van aanmeldingsactiviteiten, controles en risicogebeurtenissen van Azure AD kunt u integreren met Azure Sentinel of een SIEM-oplossing (Security Information Event Management) van derden. Stroomlijn het proces door diagnostische instellingen te maken voor Azure AD-gebruikersaccounts en de audit- en aanmeldingslogboeken te verzenden naar een Log Analytics-werkruimte. Gewenste logboekwaarschuwingen kunnen worden geconfigureerd in Log Analytics.

- [Uw apps configureren Azure App Service azure AD-aanmelding te gebruiken](configure-authentication-provider-aad.md)

- [Azure-activiteitenlogboeken integreren in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Het in-boarden Azure Sentinel](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Afwijking van aanmeldingsgedrag van account

**Richtlijnen:** gebruik Azure Active Directory (Azure AD) als het centrale verificatie- en autorisatiesysteem voor uw App Service apps. 

Gebruik Azure AD Identity Protection om geautomatiseerde reacties te configureren op gedetecteerde verdachte acties met betrekking tot gebruikersidentiteiten, zoals de afwijking van het aanmeldingsgedrag van het account op het besturingsvlak met de Azure Portal. U kunt ook gegevens opnemen in Azure Sentinel voor verder onderzoek. 

- [Uw app configureren Azure App Service azure ad-aanmelding te gebruiken](configure-authentication-provider-aad.md)

- [Riskante Azure AD-aanmeldingen weergeven](../active-directory/identity-protection/overview-identity-protection.md)

- [Risicobeleid voor Identiteitsbeveiliging configureren en inschakelen](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Microsoft toegang geven tot relevante klantgegevens tijdens ondersteuningsscenario's

**Richtlijnen:** Niet beschikbaar; Klanten-lockbox wordt niet ondersteund voor Azure App Service.

- [Lijst met Klanten-lockbox ondersteunde services](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Benchmark: gegevensbescherming](../security/benchmarks/security-control-data-protection.md) voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Een inventaris van gevoelige informatie onderhouden

**Richtlijnen:** tags gebruiken om te helpen bij het bijhouden App Service resources die gevoelige informatie opslaan of verwerken.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Systemen isoleren die gevoelige informatie opslaan of verwerken

**Richtlijnen:** implementeert voor App Service Environment afzonderlijke abonnementen, beheergroepen of beide, voor ontwikkel-, test- en productieomgevingen. U kunt apps die gevoelige informatie verwerken op dezelfde manier isoleren van andere apps. Implementeer App Service app in een Virtual Network. Gebruik netwerkbeveiligingsgroepen en subnetten voor verdere toepassingsisolatie. 

Er zijn twee implementatietypen voor een App Service-omgeving (ASE). Met beide kunt u het verkeer isoleren op basis van uw bedrijfsvereisten.

- Externe toepassingsserviceomgeving: maakt de App Service Environment gehoste apps beschikbaar op een IP-adres dat toegankelijk is via internet.

-  internal load balancer (ILB) Application Service Environment: maakt de App Service Environment gehoste apps beschikbaar op een IP-adres in uw Virtual Network. Het interne eindpunt is een interne load balancer (ILB), daarom wordt het een ILB AS-account genoemd. 

Gebruik voor de multi-tenant App Service (een app die niet in de isolated-laag staat) Virtual Network Integration voor de toegang van uw app tot resources in uw virtuele netwerk.  Gebruik privésitetoegang om een app alleen toegankelijk te maken vanuit een particulier netwerk, zoals een app vanuit een virtueel Azure-netwerk. Virtual Network-integratie wordt alleen gebruikt om uitgaande aanroepen vanuit uw app naar uw Virtual Network. De functie Virtual Network-integratie werkt anders wanneer deze wordt gebruikt met een virtueel netwerk in dezelfde regio en met virtuele netwerken in andere regio's. 
 
- [Netwerkoverwegingen voor een App Service-omgeving](environment/network-info.md)

- [Een externe ASE maken](environment/create-external-ase.md)

- [Een interne ASE maken](environment/create-ilb-ase.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Onbevoegde overdracht van gevoelige informatie bewaken en blokkeren

**Richtlijnen:** hoewel functies voor gegevensidentificatie, classificatie en preventie van verlies nog niet beschikbaar zijn voor App Service, kunt u het risico op gegevens exfiltratie van het virtuele netwerk verminderen door alle regels te verwijderen waarbij de bestemming een 'tag' gebruikt voor internet- of Azure-services. 

Microsoft beheert de onderliggende infrastructuur voor App Service en heeft strenge controles geïmplementeerd om verlies of blootstelling van uw gegevens te voorkomen.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Alle gevoelige gegevens tijdens de overdracht versleutelen

**Richtlijnen:** gebruik de standaard minimumversie van TLS 1.2, geconfigureerd in TLS/SSL-instellingen, voor het versleutelen van alle gegevens die onderweg zijn. Zorg er ook voor dat alle HTTP-verbindingsaanvragen worden omgeleid naar HTTPS.

- [Inzicht in versleuteling in transit voor Azure App Service web-apps](security-recommendations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Waarschuwingen met betrekking tot dit besturingselement vereisen mogelijk een [Azure Defender](/azure/security-center/azure-defender) voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Web:**

[!INCLUDE [Resource Policy for Microsoft.Web 4.4](../../includes/policy/standards/asb/rp-controls/microsoft.web-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Een actief detectieprogramma gebruiken om gevoelige gegevens te identificeren

**Richtlijnen:** momenteel niet beschikbaar. Functies voor gegevensidentificatie, -classificatie en -preventie zijn nog niet beschikbaar voor App Service. 

Tag App Service apps die gevoelige informatie kunnen verwerken. Implementeert een oplossing van derden, indien nodig voor nalevingsdoeleinden.

Microsoft beheert het onderliggende platform en behandelt alle klantgegevens als gevoelig en doet er alles aan om zich te beschermen tegen gegevensverlies en -blootstelling van klanten. Om ervoor te zorgen dat klantgegevens in Azure veilig blijven, heeft Microsoft een suite met robuuste besturingselementen en mogelijkheden voor gegevensbeveiliging geïmplementeerd en onderhouden.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC gebruiken om de toegang tot resources te beheren 

**Richtlijnen:** Gebruik op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) in Azure Active Directory (Azure AD) om de toegang tot het App Service-beheervlak op de Azure Portal.

- [Azure RBAC configureren](../role-based-access-control/role-assignments-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Gevoelige informatie at-rest versleutelen

**Richtlijnen:** website-inhoud in een App Service-app, zoals bestanden, worden opgeslagen in Azure Storage, waardoor de inhoud in rust automatisch wordt versleuteld. Kies ervoor om toepassingsgeheimen in Key Vault op te slaan en op te halen tijdens runtime.

Door de klant verstrekte geheimen worden 'at rest' versleuteld terwijl ze worden opgeslagen in App Service configuratiedatabases.

Houd er rekening mee dat lokaal gekoppelde schijven optioneel door websites kunnen worden gebruikt als tijdelijke opslag (bijvoorbeeld D:\local en %TMP), maar dat ze niet at-rest worden versleuteld.

- [Informatie over besturingselementen voor gegevensbeveiliging voor Azure App Service](./security-recommendations.md#data-protection)

- [Meer Azure Storage versleuteling in rust](../storage/common/storage-service-encryption.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logboeken en waarschuwingen voor wijzigingen in kritieke Azure-resources

**Richtlijnen:** gebruik Azure Monitor azure-activiteitenlogboek om waarschuwingen te maken bij wijzigingen in productie-App Service apps en andere kritieke of gerelateerde resources.

- [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteitenlogboek](../azure-monitor/alerts/alerts-activity-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie de Azure [Security Benchmark: Vulnerability Management](../security/benchmarks/security-control-vulnerability-management.md)voor meer informatie.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Geautomatiseerde hulpprogramma's voor het scannen op beveiligingsleed uitvoeren

**Richtlijnen:** Een DevSecOps-praktijk gebruiken om ervoor te zorgen dat uw App Service-apps beveiligd blijven gedurende de hele levenscyclus. DevSecOps neemt het beveiligingsteam van uw organisatie en hun mogelijkheden op in uw DevOps-procedures, waardoor beveiliging de verantwoordelijkheid is van iedereen in het team.

Bekijk en volg de aanbevelingen van Security Center voor het beveiligen van uw App Service apps.

- [Continue beveiligingsvalidatie toevoegen aan uw CI/CD-pijplijn](/azure/devops/migrate/security-validation-cicd-pipeline?view=azure-devops&preserve-view=true)

- [Aanbevelingen voor de evaluatie Azure Security Center beveiligingsprobleem implementeren](../security-center/deploy-vulnerability-assessment-vm.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Gebruik een proces voor risicoclassificatie om prioriteit te geven aan het herstellen van gevonden beveiligingsproblemen

**Richtlijnen:** Microsoft voert vulnerability management uit op de onderliggende systemen die ondersteuning bieden App Service. U kunt echter de ernst van de aanbevelingen binnen uw Security Center en de secure score gebruiken om risico's binnen uw omgeving te meten. Uw beveiligingsscore is gebaseerd op het aantal Security Center dat u hebt beperkt. Als u de aanbevelingen die u het eerst wilt oplossen, wilt prioriteren, moet u rekening houden met de ernst van elke aanbeveling.

- [Naslaghandleiding voor beveiligingsaanbevelingen](../security-center/recommendations-reference.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie de Azure [Security Benchmark: Inventory and Asset Management (Azure Security-benchmark: inventaris en assetbeheer) voor meer informatie.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Geautomatiseerde oplossing voor assetdetectie gebruiken

**Richtlijnen:** gebruik Azure Resource Graph om alle resources in uw abonnementen op te vragen of te ontdekken (zoals rekenkracht, opslag, netwerk, poorten, protocollen, bijvoorbeeld ). Zorg ervoor dat de juiste machtigingen worden toegepast op uw tenant en dat u alle Azure-abonnementen en resources binnen uw abonnementen kunt opsnoemen.

Hoewel klassieke Azure-resources kunnen worden ontdekt via Resource Graph, wordt het ten zeerste aanbevolen om in de toekomst Azure Resource Manager te maken en te gebruiken.

- [Query's maken met Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Uw Azure-abonnementen weergeven](/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&preserve-view=true)

- [Inzicht krijgen in Azure RBAC](../role-based-access-control/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="62-maintain-asset-metadata"></a>6.2: Metagegevens van activa onderhouden

**Richtlijnen:** Tags toepassen op Azure-resources met behulp van metagegevens om ze logisch te ordenen in een taxonomie.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Niet-geautoriseerde Azure-resources verwijderen

**Richtlijnen:** gebruik waar nodig tags, beheergroepen en afzonderlijke abonnementen om Azure-resources te organiseren en bij te houden. Afstemmen van inventaris op regelmatige basis en ervoor zorgen dat niet-geautoriseerde resources worden verwijderd uit uw abonnementen als onderdeel van dit proces.

Kies Azure Policy u beperkingen wilt stellen voor het type resources dat in klantabonnementen kan worden gemaakt met behulp van de volgende ingebouwde beleidsdefinities:

- Niet toegestane resourcetypen
- Toegestane brontypen

Bekijk de koppelingen waarnaar wordt verwezen voor meer informatie.

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Een Beheergroepen](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Inventaris van goedgekeurde Azure-resources definiëren en onderhouden

**Richtlijnen:** Maak een inventaris van goedgekeurde Azure-resources en goedgekeurde software voor rekenbronnen op basis van de behoeften van uw organisatie.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Controleren op niet-goedgekeurde Azure-resources

**Richtlijnen:** Azure Policy beperkingen in te stellen voor het type resources dat in uw abonnementen kan worden gemaakt.

Gebruik Azure Resource Graph om resources in hun abonnementen op te vragen of te ontdekken.  Zorg ervoor dat alle Azure-resources in de omgeving zijn goedgekeurd. 

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md) 

- [Query's maken met Azure Graph](../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Controleren op niet-goedgekeurde softwaretoepassingen binnen rekenbronnen

**Richtlijnen:** gebruik Azure Resource Graph om resources in uw abonnementen op te vragen of te ontdekken en ervoor te zorgen dat de gevonden Azure-resources worden goedgekeurd op basis van het beleid van uw organisatie.

Gebruik WebJobs in App Service om te controleren op niet-goedgekeurde softwaretoepassingen die zijn geïmplementeerd binnen rekenbronnen. Gebruik WebJobs om een programma of script uit te voeren in hetzelfde exemplaar als een web-app, API-app of mobiele app. Definieer webjobconfiguraties en bewaking met logboeken. Selecteer op de pagina Details van webjobuitvoer de optie Uitvoer in-/uitschakelen om de tekst van de logboekinhoud weer te geven. Houd er rekening mee dat webjobs nog niet worden ondersteund voor App Service op Linux.

- [Achtergrondtaken uitvoeren met WebJobs in Azure App Service](webjobs-create.md)

- [Zelfstudie: Beleidsregels maken en beheren om naleving af te dwingen](../governance/policy/tutorials/create-and-manage.md)

- [Quickstart: uw eerste query Resource Graph uitvoeren met Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Niet-goedgekeurde Azure-resources en -softwaretoepassingen verwijderen

**Richtlijnen:** Zorg ervoor dat alle Azure-resources die aanwezig zijn in de omgeving, worden goedgekeurd. Gebruik Azure Policy beperkingen in te stellen voor het type resources dat in uw abonnementen kan worden gemaakt. Verwijder alle geïmplementeerde softwaretoepassingen die niet zijn goedgekeurd volgens het beleid van uw organisatie.

- [Achtergrondtaken uitvoeren met WebJobs in Azure App Service](webjobs-create.md)

- [Zelfstudie: Beleidsregels maken en beheren om naleving af te dwingen](../governance/policy/tutorials/create-and-manage.md)

- [Quickstart: uw eerste query Resource Graph uitvoeren met Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="68-use-only-approved-applications"></a>6.8: Alleen goedgekeurde toepassingen gebruiken

**Richtlijnen:** Zorg ervoor dat alle Azure-resources in de omgeving worden goedgekeurd. Gebruik Azure Policy beperkingen in te stellen voor het type resources dat in uw abonnementen kan worden gemaakt. Verwijder alle geïmplementeerde softwaretoepassingen die niet zijn goedgekeurd volgens het beleid van uw organisatie. 

- [Achtergrondtaken uitvoeren met WebJobs in Azure App Service](webjobs-create.md)

- [Zelfstudie: Beleidsregels maken en beheren om naleving af te dwingen](../governance/policy/tutorials/create-and-manage.md)

- [Quickstart: uw eerste query Resource Graph uitvoeren met Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="69-use-only-approved-azure-services"></a>6.9: Alleen goedgekeurde Azure-services gebruiken

**Richtlijnen:** maak een proces om niet-geautoriseerde Azure-services periodiek te controleren om ervoor te zorgen dat alleen geautoriseerde Azure-services worden gebruikt in uw abonnementen.

Gebruik Azure Resource Graph, binnen dit proces, om resources in hun abonnementen op te vragen of te ontdekken. Zorg ervoor dat alle Azure-resources in de omgeving zijn goedgekeurd.

Configureer Azure Policy beperkingen in te stellen voor het type resources dat in uw abonnementen kan worden gemaakt met behulp van de volgende ingebouwde beleidsdefinities:

- Niet toegestane resourcetypen

- Toegestane brontypen

Gebruik WebJobs in App Service om te controleren op niet-goedgekeurde softwaretoepassingen die zijn geïmplementeerd in computerbronnen. Gebruik WebJobs om een programma of script uit te voeren in hetzelfde exemplaar als een web-app, API-app of mobiele app. Definieer webjobconfiguraties en bewaking met logboeken. Selecteer op de pagina Details van webjobuitvoer de optie Uitvoer in-/uitschakelen om de tekst van de logboekinhoud weer te geven. Houd er rekening mee dat webjobs nog niet worden ondersteund voor App Service op Linux.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een specifiek resourcetype weigeren met Azure Policy](../governance/policy/samples/built-in-policies.md#general)

- [Achtergrondtaken uitvoeren met WebJobs in Azure App Service](webjobs-create.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Een inventaris van goedgekeurde softwaretitels onderhouden

**Richtlijnen:** Implementeert periodiek een proces voor het inventariseren en controleren van softwaretitels in uw abonnementen om ervoor te zorgen dat alleen geautoriseerde Azure-services worden gebruikt in uw abonnementen.

Gebruik Azure Resource Graph, binnen dit proces, om resources in uw abonnementen op te vragen of te ontdekken. Zorg ervoor dat alle Azure-resources die in de omgeving worden ontdekt, zijn goedgekeurd.

Configureer Azure Policy beperkingen in te stellen voor het type resources dat kan worden gemaakt in klantabonnementen met behulp van de volgende ingebouwde beleidsdefinities:

- Niet toegestane resourcetypen

- Toegestane brontypen

Op dezelfde manier kunt u WebJobs in App Service om niet-goedgekeurde softwaretoepassingen te inventariseren die zijn geïmplementeerd in computerbronnen. Definieer de configuratie en bewaking met logboeken. Selecteer op de pagina Details van webjobuitvoer de optie Uitvoer in-/uitschakelen om de tekst van de logboekinhoud weer te geven. Houd er rekening mee dat webjobs nog niet worden ondersteund voor App Service op Linux.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een specifiek resourcetype weigeren met Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: De mogelijkheid van gebruikers om te communiceren met Azure Resource Manager

**Richtlijnen:** Configureer voorwaardelijke toegang van Azure om de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager te beperken door Toegang blokkeren te configureren voor de app Microsoft Azure Management.

- [Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: De mogelijkheid van gebruikers om scripts uit te voeren binnen rekenbronnen beperken

**Richtlijnen:** Met webjobs in App Service kunnen klanten een programma of script uitvoeren in hetzelfde exemplaar als een web-app, API-app of mobiele app. U bent verantwoordelijk voor het definiëren van uw configuratie om scripts te beperken of te beperken die niet zijn toegestaan door de organisatie. App Service biedt geen mechanisme om de uitvoering van scripts systeemeigen te beperken. Houd er rekening mee dat webjobs nog niet worden ondersteund voor App Service op Linux.

- [Achtergrondtaken uitvoeren met WebJobs in Azure App Service](webjobs-create.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Toepassingen met een hoog risico fysiek of logisch scheiden

**Richtlijnen:** Implementeert afzonderlijke abonnementen of beheergroepen om isolatie te bieden voor App Service apps. Implementeer een app met een hoger risico in Virtual Network eigen netwerk, omdat perimeterbeveiliging in App Service wordt bereikt via het gebruik van virtuele netwerken. De App Service Environment is een implementatie van App Service in een subnet in uw Azure-Virtual Network. 

Er zijn twee typen Application Service Environment: External Application Service Environment en ILB (Internal Load Balancer) Application Service Environment. Kies de beste architectuur op basis van uw vereisten.

- [Netwerkoverwegingen voor een App Service-omgeving](environment/network-info.md)

- [Een externe App Service maken](environment/create-external-ase.md)

- [Een App Service-omgeving voor een interne load balancer maken en gebruiken](environment/create-ilb-ase.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="secure-configuration"></a>Veilige configuratie

*Zie de Azure [Security Benchmark: Secure Configuration voor meer informatie.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Veilige configuraties voor alle Azure-resources tot stand brengen

**Richtlijnen:** standaardbeveiligingsconfiguraties definiëren en implementeren voor uw App Service apps met Azure Policy.

Gebruik Azure Policy aliassen in de naamruimte Microsoft.Web om aangepaste beleidsregels te maken voor het controleren of afdwingen van de configuratie van uw App Service Web Apps.

Pas ingebouwde beleidsdefinities toe, zoals:

- App Service moet gebruikmaken van een service-eindpunt voor een virtueel netwerk

- Webtoepassingen mogen alleen toegankelijk zijn via HTTPS

- De nieuwste TLS-versie in uw apps gebruiken

Het is raadzaam om het proces voor het toepassen van de ingebouwde beleidsdefinities voor gestandaardiseerd gebruik te documenteren.   

- [Beschikbare aliassen Azure Policy weergeven](/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&preserve-view=true)

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Veilige Azure-resourceconfiguraties onderhouden

**Richtlijnen:** gebruik Azure Policy [weigeren] en [implementeren indien niet aanwezig] om beveiligde instellingen af te dwingen voor uw Azure App Service apps.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Inzicht in Azure Policy effecten](../governance/policy/concepts/effects.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuratie van Azure-resources veilig opslaan

**Richtlijnen:** kies Azure DevOps of Azure Repos om uw code veilig op te slaan en te beheren wanneer u aangepaste Azure Policy gebruikt.

Gebruik uw bestaande PIJPLIJN voor continue integratie (CI) en Continue levering (CD) om een bekende veilige configuratie te implementeren.

- [Code opslaan in Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops&preserve-view=true)

- [Documentatie voor Azure-repos](/azure/devops/repos/?view=azure-devops&preserve-view=true)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Hulpprogramma's voor configuratiebeheer implementeren voor Azure-resources

**Richtlijnen:** gebruik ingebouwde Azure Policy-definities en Azure Policy-aliassen in de naamruimte Microsoft.Web om aangepaste beleidsregels te maken om systeemconfiguraties te waarschuwen, te controleren en af te dwingen. Ontwikkel een proces en pijplijn voor het beheren van beleids uitzonderingen.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Geautomatiseerde configuratiebewaking implementeren voor Azure-resources

**Richtlijnen:** gebruik ingebouwde Azure Policy-definities en Azure Policy-aliassen in de naamruimte Microsoft.Web om aangepaste beleidsregels te maken om systeemconfiguraties te waarschuwen, te controleren en af te dwingen. 

Pas Azure Policy [audit], [deny] en [deploy if not exist] toe om configuraties automatisch af te dwingen voor uw Azure-resources.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure-geheimen veilig beheren

**Richtlijnen:** Beheerde identiteiten gebruiken om uw App Service-apps een automatisch beheerde identiteit te bieden in Azure Active Directory (Azure AD). Met beheerde identiteiten kunnen uw apps zich verifiëren bij elke service die ondersteuning biedt voor Azure AD-verificatie, Key Vault, zonder referenties in uw code. Zorg ervoor dat de functie voor zacht verwijderen is ingeschakeld in Azure Key Vault.

- [Zacht verwijderen inschakelen in Azure Key Vault](../key-vault/general/key-vault-recovery.md)

- [Beheerde identiteiten gebruiken voor App Service](overview-managed-identity.md)

- [Verificatie met Key Vault met een beheerde identiteit](../key-vault/general/assign-access-policy-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Identiteiten veilig en automatisch beheren

**Richtlijnen:** Gebruik beheerde identiteiten om uw door App Service geïmplementeerde apps een automatisch beheerde identiteit te bieden in Azure Active Directory (Azure AD). Met beheerde identiteiten kunnen uw apps zich verifiëren bij elke service die ondersteuning biedt voor Azure AD-verificatie, Key Vault, zonder referenties in uw code.

- [Beheerde identiteiten gebruiken voor App Service](overview-managed-identity.md)

- [Verificatie met Key Vault met een beheerde identiteit](../key-vault/general/assign-access-policy-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** De [Azure Security-benchmark](/azure/governance/policy/samples/azure-security-benchmark) is het standaardbeleidsinitiatief voor Security Center en is de basis voor [Security Center aanbevelingen van Security Center.](/azure/security-center/security-center-recommendations) De Azure Policy met betrekking tot dit besturingselement worden automatisch ingeschakeld door Security Center. Waarschuwingen met betrekking tot dit besturingselement vereisen mogelijk een [Azure Defender](/azure/security-center/azure-defender) voor de gerelateerde services.

**Azure Policy ingebouwde definities - Microsoft.Web:**

[!INCLUDE [Resource Policy for Microsoft.Web 7.12](../../includes/policy/standards/asb/rp-controls/microsoft.web-7-12.md)]

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Onbedoelde blootstelling van referenties voorkomen

**Richtlijnen:** Referentiescanner implementeren om referenties in code te identificeren. Door het gebruik van Credential Scanner worden gebruikers ook aangemoedigd om gedetecteerde referenties naar veiligere locaties, zoals Azure Key Vault, te verplaatsen.

- [Referentiescanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="data-recovery"></a>Gegevensherstel

*Zie de Azure [Security Benchmark: Data Recovery voor meer informatie.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Regelmatige automatische back-ups garanderen

**Richtlijnen:** met de functie Back-up en App Service kunt u eenvoudig handmatig of volgens een planning app-back-ups maken. U kunt de back-ups zo configureren dat ze voor onbepaalde tijd worden bewaard. U kunt de app herstellen naar een momentopname van een eerdere status door de bestaande app te overschrijven of te herstellen naar een andere app.

App Service kunt een back-up maken van de volgende gegevens naar een Azure-opslagaccount en -container, die u hebt geconfigureerd voor het gebruik van uw app:
- App-configuratie
- Bestandsinhoud
- Database die is verbonden met uw app

Zorg ervoor dat regelmatige en geautomatiseerde back-ups plaatsvinden met een frequentie zoals gedefinieerd door het beleid van uw organisatie.

- [Inzicht in Azure App Service back-upmogelijkheden](manage-backup.md)

- [Door de klant beheerde sleutels voor Azure Storage versleuteling](../storage/common/customer-managed-keys-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Volledige systeemback-ups uitvoeren en back-ups maken van door de klant beheerde sleutels

**Richtlijnen:** Gebruik de functie voor back-up en herstel van App Service back-up van uw toepassingen. De back-upfuncties vereisen een Azure Storage account voor het opslaan van de back-upgegevens van uw toepassing.

- Azure Storage biedt versleuteling in rust: gebruik door het systeem geleverde sleutels of uw eigen, door de klant beheerde sleutels. Hier worden uw toepassingsgegevens opgeslagen wanneer deze niet worden uitgevoerd in een web-app in Azure.
- Uitvoeren vanuit een implementatiepakket is een implementatiefunctie van App Service. Hiermee kunt u uw site-inhoud implementeren vanuit een Azure Storage-account met behulp van Shared Access Signature URL (SAS).

- Key Vault zijn een beveiligingsfunctie van App Service. Hiermee kunt u geheimen tijdens runtime importeren als toepassingsinstellingen. Gebruik dit om de SAS-URL van uw Azure Storage versleutelen.

Meer informatie is beschikbaar via de koppelingen waarnaar wordt verwezen.

- [Back-up maken van uw app in Azure](manage-backup.md)

- [Een app herstellen die wordt uitgevoerd in Azure App Service](web-sites-restore.md)

- [Meer informatie over versleuteling van data-at-rest in Azure](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [Versleutelingsmodel en sleutelbeheertabel](../security/fundamentals/encryption-atrest.md)

- [Versleuteling in rust met door de klant beheerde sleutels](configure-encrypt-at-rest-using-cmk.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richtlijnen:** test het herstelproces periodiek voor back-ups van uw App Service toepassingen.

- [Back-up maken van uw app in Azure](manage-backup.md)

- [Een web-app Azure App Service herstellen](web-sites-restore.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Beveiliging van back-ups en door de klant beheerde sleutels garanderen

**Richtlijnen:** App Service back-ups worden opgeslagen in een Azure Storage account. Gegevens in Azure Storage worden transparant versleuteld en ontsleuteld met 256-bits AES-versleuteling, een van de sterkste blokversleutelingen die beschikbaar zijn en compatibel zijn met FIPS 140-2. Azure Storage is vergelijkbaar met BitLocker-versleuteling in Windows.

Azure Storage is ingeschakeld voor alle opslagaccounts, met inbegrip van zowel Resource Manager als klassieke opslagaccounts. Azure Storage-versleuteling kan niet worden uitgeschakeld. Omdat uw gegevens standaard zijn beveiligd, hoeft u uw code of toepassingen niet te wijzigen om te profiteren van Azure Storage versleuteling.

Gegevens in een opslagaccount worden standaard versleuteld met door Microsoft beheerde sleutels. U kunt vertrouwen op door Microsoft beheerde sleutels voor de versleuteling van uw gegevens of u kunt versleuteling beheren met uw eigen sleutels. Zorg ervoor dat de functie voor zacht verwijderen is ingeschakeld in Azure Key Vault.

- [Meer informatie Azure Storage versleuteling van data-at-rest](../storage/common/storage-service-encryption.md)

- [Soft Delete inschakelen in Azure Key Vault](../key-vault/general/key-vault-recovery.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Benchmark: respons op incidenten](../security/benchmarks/security-control-incident-response.md) voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10.1: Een handleiding voor het reageren op incidenten maken

**Richtlijnen**: Stel voor uw organisatie een responshandleiding op voor gebruik bij incidenten. Zorg ervoor dat er schriftelijke responsplannen zijn waarin alle rollen van het personeel worden gedefinieerd, evenals alle fasen in het afhandelen/managen van incidenten, vanaf de detectie van het incident tot een evaluatie ervan achteraf.

- [Werkstroomautomatiseringen configureren binnen Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Richtlijnen voor het bouwen van uw eigen reactieproces voor beveiligingsincident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center van een incident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [De klant kan ook gebruikmaken van de computerbeveiligingsincidentafhandelingshandleiding van NIST om te helpen bij het maken van een eigen plan voor het reageren op incidenten](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Een procedure voor het scoren en prioriteren van incidenten maken

**Richtlijnen:** Security Center aan elke waarschuwing een ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op hoe zeker Security Center is bij het vinden of de analyse die wordt gebruikt om de waarschuwing uit te geven, evenals het betrouwbaarheidsniveau dat er een schadelijke intentie achter de activiteit zit die tot de waarschuwing heeft geleid.

Markeer daarnaast abonnementen duidelijk (bijvoorbeeld productie, niet-productie) en maak een naamgevingssysteem om Azure-resources duidelijk te identificeren en te categoriseren.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="103-test-security-response-procedures"></a>10.3: Procedures voor het reageren op beveiliging testen

**Richtlijnen:** oefen regelmatig om de mogelijkheden voor het reageren op incidenten van uw systeem te testen. Stel vast waar zich zwakke plekken en hiaten bevinden, en wijzig zo nodig het plan.

- [Raadpleeg de publicatie van NIST- Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities (Handleiding voor test-, trainings- en oefeningsprogramma's voor IT-plannen en -mogelijkheden)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Contactgegevens voor beveiligingsincidenten verstrekken en waarschuwingsmeldingen voor beveiligingsincidenten configureren

**Richtlijnen:** Contactgegevens voor beveiligingsincidenten worden door Microsoft gebruikt om contact met u op te nemen als de Microsoft Security Response Center (MSRC) detecteert dat de gegevens van de klant zijn gebruikt door een onrechtmatige of niet-geautoriseerde partij.  Bekijk incidenten na het feit om ervoor te zorgen dat problemen worden opgelost.

- [De beveiligingscontactcontacte Azure Security Center instellen](../security-center/security-center-provide-security-contact-details.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Beveiligingswaarschuwingen opnemen in uw systeem voor het reageren op incidenten

**Richtlijnen:** exporteert Security Center waarschuwingen en aanbevelingen met behulp van de functie Continue export. Met continue export kunt u waarschuwingen en aanbevelingen handmatig of doorlopend exporteren. Gebruik de Security Center gegevensconnector om de waarschuwingen te streamen Azure Sentinel bedrijfsbehoeften.

- [Continue export configureren](../security-center/continuous-export.md)

- [Waarschuwingen streamen naar Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: De reactie op beveiligingswaarschuwingen automatiseren

**Richtlijnen:** gebruik de functie Werkstroomautomatisering in Security Center automatisch reacties te activeren via 'Logic Apps' voor beveiligingswaarschuwingen en aanbevelingen.

- [Werkstroomautomatisering en -Logic Apps](../security-center/workflow-automation.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking:** geen

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie de Azure [Security Benchmark: Penetration Tests en Red Team Exercises (Penetratietests en Red Team-oefeningen) voor meer informatie.](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Regelmatige penetratietests uitvoeren van uw Azure-resources en zorgen voor herstel van alle kritieke beveiligings bevindingen

**Richtlijnen:** volg de Microsoft Cloud Penetration Testing Rules of Engagement om ervoor te zorgen dat uw penetratietests niet in strijd zijn met het Microsoft-beleid. Gebruik de strategie van Microsoft en de uitvoering van Red Teaming-activiteiten, en voer een penetratietest van de live site uit op basis van een infrastructuur, services en toepassingen die door Microsoft worden beheerd. 

- [Regels voor het inzetten van penetratietests](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking:** geen

## <a name="next-steps"></a>Volgende stappen

- Zie [Overzicht Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Meer informatie over [Azure-beveiligingsbasislijnen](../security/benchmarks/security-baselines-overview.md)
