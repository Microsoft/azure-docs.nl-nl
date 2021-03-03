---
title: Azure-beveiligings basislijn voor App Service
description: De App Service Security Baseline voorziet in procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-Bench Mark.
author: msmbaldwin
ms.service: app-service
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: dd612e7e3c54a000d989c5a2f3a633d06d6d11cb
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101718342"
---
# <a name="azure-security-baseline-for-app-service"></a>Azure-beveiligings basislijn voor App Service

De Azure-beveiligings basislijn voor App Service bevat aanbevelingen waarmee u de beveiligings postuur van uw implementatie kunt verbeteren. De basis lijn voor deze service wordt opgehaald uit de [Azure Security Bench Mark-versie 1,0](../security/benchmarks/overview-v1.md), die aanbevelingen biedt over hoe u uw cloud oplossingen kunt beveiligen in azure met onze richt lijnen voor best practices. De inhoud wordt gegroepeerd op de **beveiligings controles** die zijn gedefinieerd door de Azure Security-benchmark en de bijbehorende richt lijnen die van toepassing zijn op app service. **Besturings elementen** die niet van toepassing zijn op app service, zijn uitgesloten.

Als u wilt zien hoe App Service volledig is toegewezen aan de beveiligings benchmark van Azure, raadpleegt u het [volledige app service beveiligings basislijn toewijzings bestand](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Benchmark: netwerkbeveiliging](../security/benchmarks/security-control-network-security.md) voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-resources in virtuele netwerken beveiligen

**Hulp bij** het gebruik van app service in de categorie geïsoleerde prijzen, ook wel een app service Environment (ASE) genoemd, kunt u rechtstreeks in een subnet binnen uw Azure-Virtual Network implementeren. Gebruik netwerk beveiligings groepen om uw Azure App Service Environment te beveiligen door inkomend en uitgaand verkeer naar resources in uw virtuele netwerk te blok keren, of om de toegang tot apps in een App Service Environment te beperken.

Netwerk beveiligings groepen bevatten standaard een impliciete regel voor weigeren met de laagste prioriteit en hiervoor moet u expliciet regels voor toestaan toevoegen. Voeg regels voor toestaan toe voor uw netwerk beveiligings groep op basis van een minimale geprivilegieerde netwerk benadering. De onderliggende virtuele machines die worden gebruikt voor het hosten van de App Service Environment zijn niet rechtstreeks toegankelijk omdat ze zich in een door micro soft beheerd abonnement bevinden.

Beveilig een App Service Environment door verkeer te routeren via een WAF (Web Application firewall) ingeschakeld Azure-toepassing gateway. Gebruik service-eind punten in combi natie met de Application Gateway voor het beveiligen van inkomend publicatie verkeer naar uw app.  

In de multi tenant-App Service (een app die zich niet in een geïsoleerde tier bewaart), gebruikt u netwerk beveiligings groepen om uitgaand verkeer van uw app te blok keren. Stel uw apps in staat om toegang te krijgen tot resources in of via een Virtual Network, met de functie voor integratie van Virtual Network. Deze functie kan ook worden gebruikt om uitgaand verkeer naar open bare adressen van de app te blok keren. Virtual Network-integratie kan niet worden gebruikt om binnenkomende toegang tot een app te bieden.  

Beveilig inkomend verkeer naar uw app met:
- Toegangs beperkingen: een reeks regels voor toestaan of weigeren waarmee de inkomende toegang wordt beheerd
- Service-eind punten: kan binnenkomend verkeer van buiten opgegeven virtuele netwerken of subnetten weigeren
- Persoonlijke eind punten: uw app beschikbaar maken voor uw Virtual Network met een privé-IP-adres. Met de persoonlijke eind punten die zijn ingeschakeld voor uw app, is deze niet langer toegankelijk via Internet

Wanneer u Virtual Network integratie functie met virtuele netwerken in dezelfde regio gebruikt, gebruikt u netwerk beveiligings groepen en route tabellen met door de gebruiker gedefinieerde routes. Door de gebruiker gedefinieerde routes kunnen worden geplaatst op het subnet met integratie om uitgaand verkeer als bedoeld te verzenden.  

Overweeg het implementeren van een Azure Firewall voor het centraal maken, afdwingen en vastleggen van toepassingen en beleids regels voor netwerk connectiviteit in uw abonnementen en virtuele netwerken. Azure Firewall gebruikt een statisch openbaar IP-adres voor virtuele netwerk bronnen, waarmee externe firewalls verkeer kunnen identificeren dat afkomstig is van het virtuele netwerk. 

- [Een App Service Environment vergren delen](environment/firewall-integration.md)

- [Open Web Application Security project (OWASP) Top 10 beveiligings problemen beveiliging](https://owasp.org/www-project-top-ten/)

- [Netwerkbeveiligingsgroepen](../virtual-network/network-security-groups-overview.md)

- [Een app integreren met een virtueel Azure-netwerk](web-sites-integrate-with-vnet.md)

- [Netwerkoverwegingen voor een App Service-omgeving](environment/network-info.md)

- [Een externe ASE maken](environment/create-external-ase.md)

- [Een interne ASE maken](environment/create-ilb-ase.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: de [Security Bench Mark van Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) is het standaard beleids initiatief voor Security Center en is de basis voor de [aanbevelingen van Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). De Azure Policy definities die aan dit besturings element zijn gerelateerd, worden automatisch door Security Center ingeschakeld. Voor waarschuwingen met betrekking tot dit besturings element is mogelijk een [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -plan vereist voor de gerelateerde services.

**Ingebouwde definities Azure Policy-micro soft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-1.md)]

**Ingebouwde definities Azure Policy-micro soft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.web-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: de configuratie en het verkeer van virtuele netwerken, subnetten en netwerk interfaces bewaken en vastleggen

**Hulp** bij het implementeren van aanbevelingen voor netwerk beveiliging van Azure Security Center voor het beveiligen van netwerk bronnen en configuraties die betrekking hebben op uw app service apps en api's.

Gebruik Azure Firewall om verkeer te verzenden en de toepassing en het beleid voor netwerk verbindingen centraal te maken, af te dwingen en te registreren in abonnementen en virtuele netwerken. Azure Firewall gebruikt een statisch openbaar IP-adres voor uw virtuele netwerk bronnen, waarmee externe firewalls verkeer kunnen identificeren dat afkomstig is van uw Virtual Network. De Azure Firewall-service is ook volledig geïntegreerd met Azure Monitor voor logboek registratie en analyse.

- [Overzicht van Azure Firewall](../firewall/overview.md)

- [Informatie over de netwerk beveiliging die wordt verschaft door Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Bewaking en beveiliging van App Service inschakelen](../security-center/defender-for-app-service-introduction.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: de [Security Bench Mark van Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) is het standaard beleids initiatief voor Security Center en is de basis voor de [aanbevelingen van Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). De Azure Policy definities die aan dit besturings element zijn gerelateerd, worden automatisch door Security Center ingeschakeld. Voor waarschuwingen met betrekking tot dit besturings element is mogelijk een [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -plan vereist voor de gerelateerde services.

**Ingebouwde definities Azure Policy-micro soft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.2](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-2.md)]

### <a name="13-protect-critical-web-applications"></a>1,3: essentiële webtoepassingen beveiligen

**Hulp**: een op internet toegankelijke app beveiligen in een app service Environment (ASE) door:
- Een Web Application firewall (WAF) met Azure-toepassing gateway voor een Internet gerichte app implementeren
- Toegangs beperkingen gebruiken voor het beveiligen van inkomend verkeer naar de Application Gateway
- De app beveiligen met Azure Active Directory (Azure AD) om verificatie te garanderen
- De minimum versie van TLS instellen op 1,2
- Stel de app alleen in op HTTPS

Alle toepassings verkeer verloopt via een Azure Firewall apparaat en de logboeken controleren. 

Een op internet toegankelijke app beveiligen in de multi tenant-App Service, (zoals, niet in de geïsoleerde laag)
- Een apparaat voor firewall-ondersteuning voor webtoepassingen voor een app implementeren
- Toegangs beperkingen of service-eind punten gebruiken voor het beveiligen van inkomend verkeer naar het WAF-apparaat (Web Application firewall)
- De app beveiligen met Azure AD om verificatie te garanderen
- De minimum versie van TLS instellen op 1,2
- Stel de app alleen in op HTTPS
- Gebruik de integratie van virtueel netwerk en de app-instelling WEBSITE_VIRTUAL NETWORK_ROUTE_ALL om al het uitgaande verkeer afhankelijk te maken van netwerk beveiligings groepen en door de gebruiker gedefinieerde routes op het integratie subnet.

Net als bij de toepassings service omgevings-app, moet u alle toepassings verkeer verloopt via een Azure Firewall apparaat en de logboeken in de app controleren.

Daarnaast kunt u aanbevelingen bekijken en volgen in het document vergren delen van een App Service Environment.

- [Een App Service Environment vergren delen](environment/firewall-integration.md)

- [Azure Web Application firewall op Azure-toepassing gateway](../web-application-firewall/ag/ag-overview.md)

- [Toegangs beperkingen Azure App Service](app-service-ip-restrictions.md)

- [Spoor WAF-waarschuwingen op en bewaak trends eenvoudig met Azure Monitor ](../azure-monitor/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: de [Security Bench Mark van Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) is het standaard beleids initiatief voor Security Center en is de basis voor de [aanbevelingen van Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). De Azure Policy definities die aan dit besturings element zijn gerelateerd, worden automatisch door Security Center ingeschakeld. Voor waarschuwingen met betrekking tot dit besturings element is mogelijk een [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -plan vereist voor de gerelateerde services.

**Ingebouwde definities Azure Policy-micro soft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 1.3](../../includes/policy/standards/asb/rp-controls/microsoft.web-1-3.md)]

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: communicatie met bekende schadelijke IP-adressen weigeren

**Hulp**: beveilig de app service Environment zoals beschreven in de documentatie over het vergren delen van een app service environment. Pas de functie geïntegreerde bedreigings informatie in Azure Security Center toe om communicatie met bekende of ongebruikte open bare IP-adressen te weigeren. Gebruik toegangs beperkingen voor het beveiligen van inkomend verkeer naar de Application Gateway. 

Beveilig de multi tenant-App Service (een app die zich niet in een geïsoleerde laag bevindt), met een openbaar Internet gericht eind punt. Hiermee wordt alleen verkeer van een specifiek subnet binnen uw Virtual Network toegestaan en worden alle overige blokken geblokkeerd. Gebruik toegangs beperkingen voor het configureren van netwerk Access Control lijsten (IP-beperkingen) voor het vergren delen van toegestaan binnenkomend verkeer.

Definieer de prioriteit van de lijst besteld toestaan of weigeren om netwerk toegang tot uw app te beheren. Deze lijst kan IP-adressen of Virtual Network subnetten bevatten. Er bestaat een impliciete regel voor ' weigeren ' aan het einde van de lijst wanneer deze een of meer vermeldingen bevat. Deze mogelijkheid werkt met alle App Service gehoste werk belastingen, waaronder, Web Apps, API Apps, Linux-apps, Linux-container-apps en-functies. 

Gebruik service-eind punten om de toegang tot uw web-app te beperken vanuit een Azure-Virtual Network. Beperk de toegang tot een multi tenant-App Service (een app die zich niet in een geïsoleerde laag bevindt), van geselecteerde subnetten met Service-eind punten. 

- [Beperkingen van statische IP-adressen voor Azure App Service](app-service-ip-restrictions.md)

- [Azure Web Application firewall op Azure-toepassing gateway](../web-application-firewall/ag/ag-overview.md)

- [Een Web Application firewall (WAF) voor App Service Environment configureren](environment/app-service-app-service-environment-web-application-firewall.md)

- [De ASE beveiligen zoals beschreven in een App Service Environment vergren delen](environment/firewall-integration.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: de [Security Bench Mark van Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) is het standaard beleids initiatief voor Security Center en is de basis voor de [aanbevelingen van Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). De Azure Policy definities die aan dit besturings element zijn gerelateerd, worden automatisch door Security Center ingeschakeld. Voor waarschuwingen met betrekking tot dit besturings element is mogelijk een [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -plan vereist voor de gerelateerde services.

**Ingebouwde definities Azure Policy-micro soft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-4.md)]

### <a name="15-record-network-packets"></a>1,5: netwerk pakketten opnemen

**Hulp**: controleert aanvragen en antwoorden die worden verzonden naar en van app service apps met Security Center. Aanvallen tegen een webtoepassing kunnen worden bewaakt met behulp van een real-time Application Gateway met Web Application firewall, ingeschakeld met geïntegreerde logboek registratie van Azure Monitor om waarschuwingen voor Web Application firewalls bij te houden en trends eenvoudig te bewaken.

- [Azure Web Application Firewall voor Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: de [Security Bench Mark van Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) is het standaard beleids initiatief voor Security Center en is de basis voor de [aanbevelingen van Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). De Azure Policy definities die aan dit besturings element zijn gerelateerd, worden automatisch door Security Center ingeschakeld. Voor waarschuwingen met betrekking tot dit besturings element is mogelijk een [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -plan vereist voor de gerelateerde services.

**Ingebouwde definities Azure Policy-micro soft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.5](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-5.md)]

### <a name="17-manage-traffic-to-web-applications"></a>1,7: verkeer naar webtoepassingen beheren

**Richt lijnen**: verkeer beheren voor een app in een app service Environment:

- Beveilig de App Service Environment zoals beschreven in een App Service Environment vergren delen
- Een Application Gateway implementeren met een Azure Web Application Firewall voor uw Internet gerichte apps
- Stel de app zo in dat deze alleen toegankelijk is via HTTPS

Verkeer beheren voor een app die toegankelijk is via internet in de multi tenant-App Service (niet in de geïsoleerde laag): 

- Een Application Gateway implementeren waarop Azure Web Application firewall is ingeschakeld voor uw Internet gerichte apps
- Toegangs beperkingen of service-eind punten gebruiken voor het beveiligen van inkomend verkeer naar de Web Application firewall. De mogelijkheden voor toegangs beperkingen werken met alle App Service gehoste werk belastingen, waaronder Web Apps, API Apps, Linux-apps, Linux-container-apps en-functies.

- De app zo instellen dat deze alleen via HTTPS toegankelijk is
- Beperk de toegang tot uw App Service-app met statische IP-beperkingen, zodat deze alleen verkeer van de VIP op een toepassings Gateway ontvangt als het enige adres met Access.

Raadpleeg de koppelingen waarnaar wordt verwezen voor aanvullende informatie.

- [Beperkingen van statische IP-adressen voor Azure App Service](app-service-ip-restrictions.md)

- [Azure Web Application firewall op Azure-toepassing gateway](../web-application-firewall/ag/ag-overview.md)

- [End-to-end TLS configureren met behulp van Application Gateway met de portal](../application-gateway/end-to-end-ssl-portal.md)

- [De ASE beveiligen zoals beschreven in een App Service vergren delen](/azure/app-service/environment/firewall-integrationEnvironment:)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: de complexiteit en administratieve overhead van netwerk beveiligings regels minimaliseren

**Hulp**: app service heeft een aantal eind punten die worden gebruikt om de service te beheren. Deze eindpunt adressen zijn ook opgenomen in het AppServiceManagement IP-service label. De label AppServiceManagement wordt alleen gebruikt met een App Service Environment om dergelijk verkeer toe te staan. 

U kunt het verkeer voor de bijbehorende service toestaan of weigeren door de servicetag naam op te geven in het juiste bron-of doel veld van een regel. App Service inkomende adressen worden gevolgd in de AppService IP-service-tag. Er is geen IP-servicetag die de uitgaande adressen bevat die door App Service worden gebruikt.

Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen.

- [Service tags van virtueel netwerk](../virtual-network/service-tags-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: standaard beveiligings configuraties voor netwerk apparaten onderhouden

**Richt lijnen**: standaard beveiligings configuraties definiëren en implementeren voor netwerk instellingen die betrekking hebben op uw app service-apps. 

Beveiligings configuraties onderhouden met Azure Policy aliassen in de naam ruimten ' micro soft. Web ' en ' micro soft. Network '. Aangepaste beleids regels maken om de netwerk configuratie van uw App Service-apps te controleren of af te dwingen. 

Gebruik ingebouwde beleids definities voor App Service, zoals:
- De app moet een service-eind punt voor een virtueel netwerk gebruiken
- De app mag alleen toegankelijk zijn via HTTPS
- De minimum versie van TLS instellen op de huidige versie

Raadpleeg de koppelingen waarnaar wordt verwezen voor aanvullende informatie.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [End-to-end TLS configureren met behulp van Application Gateway met de portal](../application-gateway/end-to-end-ssl-portal.md)

- [De ASE beveiligen zoals beschreven in een App Service vergren delen](/azure/app-service/environment/firewall-integrationEnvironment:)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="110-document-traffic-configuration-rules"></a>1,10: configuratie regels voor het document verkeer

**Hulp**: Labels gebruiken voor netwerk beveiligings groepen en andere gerelateerde resources, waaronder verkeers stroom in app service.

Geef de bedrijfs behoefte, duur, enzovoort op, met het veld Beschrijving voor alle regels, waarmee verkeer van of naar een netwerk wordt toegestaan voor afzonderlijke regels voor netwerk beveiligings groepen.

Pas een van de ingebouwde Azure Policy definities toe die betrekking hebben op label effecten, zoals ' vereist label en de waarde ', om ervoor te zorgen dat alle resources worden gemaakt met tags en u op de hoogte moet worden gesteld van alle bestaande niet-gelabelde resources. Gebruik Azure PowerShell of Azure CLI om op basis van hun labels acties op te zoeken of uit te voeren op resources.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

- [Toegangs beperkingen Azure App Service](/azure/app-service/app-service-ip-restriction)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische hulpprogram ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

**Hulp**: Azure-activiteiten logboek gebruiken om configuraties van netwerk bronnen te bewaken en wijzigingen aan te brengen in de netwerk instellingen en op alle bronnen met betrekking tot app service. 

Pas een van de verschillende Azure Policy ingebouwde definities voor App Service toe, zoals een beleid waarmee apps worden gecontroleerd op het gebruik van de service voor het virtuele netwerk eindpunt. Maak waarschuwingen in Azure Monitor om te activeren wanneer wijzigingen in essentiële netwerk instellingen of-bronnen plaatsvinden. 

Bekijk gedetailleerde beveiligings waarschuwingen en aanbevelingen in Security Center, op de portal of via programmatische hulpprogram ma's. Exporteer deze gegevens of stuur deze naar andere controle hulpprogramma's in uw omgeving. Er zijn hulpprogram ma's beschikbaar om waarschuwingen en aanbevelingen hand matig of op doorlopende wijze te exporteren. Met deze hulpprogram ma's kunt u het volgende doen:
 
- Doorlopend exporteren naar een Log Analytics-werk ruimte
- Doorlopend exporteren naar Azure Event Hubs (voor integratie met Siem's van derden)
- Exporteren naar een CSV-bestand (één keer)

Het is raadzaam om een proces met automatische hulpprogram ma's te maken voor het bewaken van netwerk bron configuraties en om snel wijzigingen te detecteren.

- [Activiteiten logboek gebeurtenissen van Azure weer geven en ophalen](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Waarschuwingen maken in Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

- [Beveiligingswaarschuwingen en aanbevelingen exporteren](../security-center/continuous-export.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie [Azure Security Bench Mark: Logging and monitoring](../security/benchmarks/security-control-logging-monitoring.md)(Engelstalig) voor meer informatie.*

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

**Hulp**: integreer uw app service Environment (ASE) met Azure monitor voor het verzenden van logboeken naar Azure Storage, Azure Event Hubs of log Analytics. Diagnostische instellingen van Azure-activiteiten logboek inschakelen voor logboek registratie van controle vlak audit. Beveiligings waarschuwingen van Security Center worden gepubliceerd in het Azure-activiteiten logboek. Controleer Azure-activiteiten logboek gegevens, waarmee u kunt bepalen wat voor schrijf bewerkingen (PUT, POST, DELETE) die zijn uitgevoerd op het niveau van het besturings element van de Control voor Azure App Service en andere Azure-resources, de controle functies ' wat, wie en wanneer '. Sla uw query's op voor toekomstig gebruik, stel query resultaten vast aan Azure-Dash boards en maak logboek waarschuwingen. Gebruik ook de REST API voor gegevens toegang in Application Insights om via een programma toegang te krijgen tot uw telemetrie.

Gebruik Microsoft Azure Sentinel, een schaalbaar, SIEM (Security Information Event Management) dat beschikbaar is om verbinding te maken met verschillende gegevens bronnen en connectors op basis van uw bedrijfs vereisten. U kunt ook gegevens in-of uitschakelen voor een SIEM-systeem (Security Information Event Management) van derden, zoals Barracuda in azure Marketplace.

- [Logboek registratie ASE-activiteit](https://docs.microsoft.com/azure/app-service/environment/using-an-ase#logging)

- [Diagnostische instellingen inschakelen voor Azure App Service](troubleshoot-diagnostic-logs.md)

- [Application Insights inschakelen](../azure-monitor/app/app-insights-overview.md)

- [Telemetrie exporteren vanuit Application Insights](../azure-monitor/app/export-telemetry.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources

**Hulp** bij het inschakelen van diagnostische instellingen voor Azure-activiteiten logboek voor het controleren van de controle van app service. De logboeken verzenden naar een Log Analytics-werk ruimte, een Azure Event hub of een Azure Storage-account.

De ' What, wie en wanneer ' voor schrijf bewerkingen (PUT, POST, DELETE) die zijn uitgevoerd op het niveau van het besturings vlak kunnen worden bepaald met Azure-activiteiten logboek gegevens voor App Service en andere Azure-resources.

Daarnaast biedt Azure Key Vault gecentraliseerd geheim beheer met toegangs beleid en controle geschiedenis. 

- [Diagnostische instellingen voor Azure-activiteiten logboek inschakelen](/azure/azure-monitor/platform/activity-log)

- [Diagnostische instellingen inschakelen voor Azure App Service](troubleshoot-diagnostic-logs.md)

- [Resource Manager-bewerkingen](../role-based-access-control/resource-provider-operations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: de [Security Bench Mark van Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) is het standaard beleids initiatief voor Security Center en is de basis voor de [aanbevelingen van Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). De Azure Policy definities die aan dit besturings element zijn gerelateerd, worden automatisch door Security Center ingeschakeld. Voor waarschuwingen met betrekking tot dit besturings element is mogelijk een [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -plan vereist voor de gerelateerde services.

**Ingebouwde definities Azure Policy-micro soft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.web-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren

**Richt lijnen**: stel in azure monitor de Bewaar periode voor het logboek in voor de log Analytics-werk ruimten die zijn gekoppeld aan uw app service bronnen volgens de nalevings voorschriften van uw organisatie.
- [Para meters voor het bewaren van Logboeken instellen](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren

**Richt lijnen**: Bekijk de diagnostische instellingen voor Azure-activiteiten logboek in uw app service-resources met de logboeken die worden verzonden naar een log Analytics-werk ruimte. Query's uitvoeren in Log Analytics om termen te zoeken, trends te identificeren, patronen te analyseren en veel andere inzichten te bieden op basis van de verzamelde gegevens.

Gebruik Application Insights voor uw App Service-apps en om logboek-, prestatie-en fout gegevens te verzamelen. De telemetriegegevens weer geven die zijn verzameld door Application Insights binnen het Azure Portal.

Als u een Web Application firewall (WAF) hebt geïmplementeerd, kunt u aanvallen op uw App Service-apps bewaken met behulp van een real-time firewall logboek voor webtoepassingen. Het logboek is geïntegreerd met Azure Monitor voor het bijhouden van Firewall waarschuwingen voor webtoepassingen en het eenvoudig bewaken van trends.

Gebruik Azure Sentinel, een schaalbaar en SIEM (native Security Information Event Management) van de cloud om te integreren met verschillende gegevens bronnen en connectors, conform de vereisten. U kunt gegevens in azure Marketplace eventueel inschakelen voor een oplossing voor het beheer van beveiligings gegevens van derden.

- [Diagnostische instellingen voor Azure-activiteiten logboek inschakelen](/azure/azure-monitor/platform/activity-log)

- [Application Insights inschakelen](../azure-monitor/app/app-insights-overview.md)

- [Uw App Service Environment integreren met de Azure-toepassing gateway (WAF)](environment/integrate-with-application-gateway.md)

- [Azure-Sentinel aan de trein](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteiten

**Hulp**: Configureer Security Center in uw Azure-abonnement en controleer de gegenereerde waarschuwingen. Gebruik Azure Monitor om uw activiteiten logboek gegevens op te halen in een event hub, waar deze kan worden gelezen door een SIEM-oplossing (Security Information Event Management), zoals Azure Sentinel. 

Bewaak aanvallen op uw App Service-apps met behulp van een realtime web application firewall-logboek met een geïmplementeerde Azure Web Application firewall (WAF). Het logboek is geïntegreerd met Azure Monitor om WAF-waarschuwingen (Web Application firewall) bij te houden en om snel trends te bewaken.

- [Uw App Service Environment integreren met de Azure-toepassing gateway (WAF)](environment/integrate-with-application-gateway.md)

- [Beveiligingswaarschuwingen en aanbevelingen exporteren](../security-center/continuous-export.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [Azure Security Bench Mark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: een inventaris van beheerders accounts onderhouden

**Hulp**: Azure Active Directory (Azure AD) heeft ingebouwde rollen die expliciet moeten worden toegewezen en query's kunnen uitvoeren. Gebruik de Azure AD Power shell-module om ad hoc-query's uit te voeren om accounts te detecteren die lid zijn van beheer groepen.

- [Leden van een directory-rol in azure AD ophalen met Power shell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

- [Beheerde identiteiten gebruiken voor App Service en Azure Functions](https://docs.microsoft.com/azure/app-service/overview-managed-identity?context=azure%2Factive-directory%2Fmanaged-identities-azure-resources%2Fcontext%2Fmsi-context&amp;tabs=dotnet)

- [Azure-rollen toewijzen met behulp van de Azure Portal](../role-based-access-control/role-assignments-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="32-change-default-passwords-where-applicable"></a>3,2: standaard wachtwoorden wijzigen indien van toepassing

**Hulp**: Azure Active Directory (Azure AD) beschikt niet over het concept van standaard wachtwoorden. Het biedt beheer vlak toegang tot App Service.

Vermijd het implementeren van standaard wachtwoorden voor gebruikers toegang bij het maken van uw eigen apps. Gebruik een van de id-providers die standaard beschikbaar zijn voor App Service, zoals Azure AD, micro soft-account, Facebook, Google of Twitter.

Schakel anonieme toegang uit, tenzij u deze moet ondersteunen. 

- [Id-providers die standaard beschikbaar zijn in Azure App Service](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization#identity-providers)

- [Verificatie en autorisatie in Azure App Service en Azure Functions](overview-authentication-authorization.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: speciale beheerders accounts gebruiken

**Richt lijnen**: Maak standaard procedures voor het gebruik van specifieke beheerders accounts. Gebruik de functies voor identiteits-en toegangs beheer in Security Center om het aantal beheerders accounts te controleren en bij te houden. 

Gebruik aanbevelingen van Security Center of het ingebouwde Azure-beleid, zoals:

- Er moeten meer dan één eigenaar aan uw abonnement zijn toegewezen. 
- Afgeschafte accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement

Maak een proces voor het bewaken van netwerk bron configuraties en Detecteer wijzigingen in beheerders accounts.

- [Azure Security Center gebruiken om identiteit en toegang te bewaken](../security-center/security-center-identity-access.md)

- [Azure Policy gebruiken](../governance/policy/tutorials/create-and-manage.md)

- [Meer informatie over het verlenen van gebruikers toegang tot toepassingen](../role-based-access-control/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: gebruik Azure Active Directory eenmalige aanmelding (SSO)

**Hulp**: Verifieer App Service via Azure Active Directory (Azure AD). Het biedt een OAuth 2,0-Service voor uw ID-provider en biedt geautoriseerde toegang tot mobiele en webtoepassingen. 

App Service-apps gebruiken federatieve identiteit, waarin een id-provider van derden de gebruikers identiteiten en verificatie stroom voor u beheert. Deze id-providers zijn standaard beschikbaar:

- Azure AD
- Microsoft-account

- Facebook

- Google

- Twitter

Wanneer u verificatie en autorisatie met een van deze providers inschakelt, is het aanmeldings eindpunt beschikbaar voor gebruikers verificatie en voor validatie van verificatie tokens van de provider.

- [Meer informatie over verificatie en autorisatie in Azure App Service](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization#identity-providers)

- [Meer informatie over verificatie en autorisatie in Azure App Service](overview-authentication-authorization.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: multi-factor Authentication gebruiken voor alle op Azure Active Directory gebaseerde toegang

**Hulp**: Schakel de functie multi-factor authentication in azure Active Directory (Azure AD) in en volg de aanbevelingen voor identiteits-en toegangs beheer in Security Center.

Multi-factor Authentication implementeren voor Azure AD. Beheerders moeten ervoor zorgen dat de abonnements accounts in de portal zijn beveiligd. Het abonnement is kwetsbaar voor aanvallen omdat het de resources beheert die u hebt gemaakt. 

- [Multi-factor Authentication voor Azure-beveiliging](/azure/security/develop/secure-aad-app)

- [Multi-factor Authentication inschakelen in azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identiteit en toegang bewaken in Azure Security Center](../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: beveiligde, door Azure beheerde werk stations gebruiken voor beheer taken

**Richt lijnen**: gebruik privileged Access workstations (Paw) met multi-factor Authentication die is geconfigureerd om Azure-resources aan te melden en te configureren. 

- [Meer informatie over privileged Access workstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Multi-factor Authentication inschakelen in azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logboeken en waarschuwingen voor verdachte activiteiten van beheerders accounts

**Hulp**: gebruik PRIVILEGED Identity Management (PIM) in azure Active Directory (Azure AD) voor het genereren van Logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten in de omgeving worden uitgevoerd.

Daarnaast kunt u met Azure AD-risico detectie waarschuwingen en rapporten bekijken over Risk ante gebruikers gedrag.

Beveiliging tegen bedreigingen in Security Center biedt uitgebreide beveiligingen voor uw omgeving, waaronder bedreigingen voor Azure Compute-resources zoals Windows-computers, Linux-machines, App Service en Azure-containers.

- [Privileged Identity Management implementeren (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Meer informatie over Azure AD-risico detectie](../active-directory/identity-protection/overview-identity-protection.md)

- [Bedreigings beveiliging voor Azure Compute-resources](../security-center/azure-defender.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Hulp**: gebruik benoemde locaties voor voorwaardelijke toegang om alleen toegang toe te staan tot de Azure Portal vanuit specifieke logische groepen met IP-adresbereiken, landen of regio's.

- [Benoemde locaties configureren in azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory gebruiken

**Hulp**: gebruik Azure Active Directory (Azure AD) als centraal verificatie-en autorisatie systeem voor uw app service-apps. Azure AD beveiligt gegevens door gebruik te maken van sterke code ring voor gegevens in rust en onderweg, met zouten, hashes en het veilig opslaan van gebruikers referenties.

- [Uw Azure App Service-apps configureren voor het gebruik van Azure AD-aanmelding](configure-authentication-provider-aad.md)

- [Een Azure AD-instantie maken en configureren](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regel matig gebruikers toegang controleren en afstemmen

**Hulp**: Detecteer verlopen accounts met de logboeken van Azure Active Directory (Azure AD). Gebruik Azure Identity Access revisies om groepslid maatschappen en de toegang tot bedrijfs toepassingen, evenals roltoewijzingen, op efficiënte wijze te beheren. Controleer regel matig de gebruikers toegang om ervoor te zorgen dat alleen de beoogde gebruikers de toegang blijven hebben. 

- [Meer informatie over Azure AD-rapportage](/azure/active-directory/reports-monitoring/)

- [Beoordelingen over Azure Identity Access gebruiken](../active-directory/governance/access-reviews-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: controle pogingen om toegang te krijgen tot gedeactiveerde referenties

**Hulp**: gebruik Azure Active Directory (Azure AD) als centraal verificatie-en autorisatie systeem voor uw app service-apps. Azure AD beveiligt gegevens door gebruik te maken van sterke code ring voor gegevens in rest-en transitieve, zouten, hashes en veilig opgeslagen gebruikers referenties.

Met toegang tot de Azure AD-aanmeldings activiteit, audit en risico logboek bronnen kunt u integreren met Azure Sentinel of een SIEM-oplossing (Security Information Event Management) van derden. Stroom lijn het proces door Diagnostische instellingen te maken voor Azure AD-gebruikers accounts en de controle-en aanmeldings logboeken te verzenden naar een Log Analytics-werk ruimte. Gewenste logboek waarschuwingen kunnen worden geconfigureerd in Log Analytics.

- [Uw Azure App Service-apps configureren voor het gebruik van Azure AD-aanmelding](configure-authentication-provider-aad.md)

- [Azure-activiteitenlogboeken integreren in Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Azure-Sentinel aan de trein](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: waarschuwing voor de afwijking van het aanmeldings gedrag van het account

**Hulp**: gebruik Azure Active Directory (Azure AD) als centraal verificatie-en autorisatie systeem voor uw app service-apps. 

Gebruik Azure AD Identity Protection om automatische antwoorden te configureren op gedetecteerde verdachte acties die betrekking hebben op gebruikers identiteiten, zoals de afwijking van het gedrag van de account aanmelding op het besturings vlak met de Azure Portal. U kunt ook gegevens opnemen in azure Sentinel voor verder onderzoek. 

- [Uw Azure App Service-app configureren voor het gebruik van Azure AD-aanmelding](configure-authentication-provider-aad.md)

- [Riskante Azure AD-aanmeldingen weergeven](../active-directory/identity-protection/overview-identity-protection.md)

- [Risico beleid voor identiteits beveiliging configureren en inschakelen](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: micro soft biedt toegang tot relevante klant gegevens tijdens ondersteunings scenario's

**Richt lijnen**: niet beschikbaar; Klanten-lockbox wordt niet ondersteund voor Azure App Service.

- [Lijst met door Klanten-lockbox ondersteunde services](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Benchmark: gegevensbescherming](../security/benchmarks/security-control-data-protection.md) voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: een inventaris van gevoelige informatie onderhouden

**Hulp**: Tags gebruiken bij het volgen van app service resources die gevoelige informatie opslaan of verwerken.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: systemen isoleren die gevoelige informatie opslaan of verwerken

**Richt lijnen**: voor een app service Environment, implementeert u afzonderlijke abonnementen, beheer groepen of beide, voor ontwikkel-, test-en productie omgevingen. U kunt apps isoleren die gevoelige informatie van andere apps op dezelfde manier verwerken. Implementeer uw App Service-app in een Virtual Network. Gebruik netwerk beveiligings groepen en-subnetten voor verdere isolatie van toepassingen. 

Er zijn twee implementatie typen voor een App Service omgeving (ASE). Beide kunt u het verkeer isoleren op basis van uw bedrijfs vereisten.

- Externe Application Service-omgeving: geeft de App Service Environment gehoste apps op een IP-adres dat toegankelijk is via internet.

-  interne load balancer (ILB) Application Service Environment: geeft de App Service Environment gehoste apps op een IP-adres in uw Virtual Network. Het interne eind punt is een interne load balancer (ILB). Dit is de reden waarom het een ILB ASE wordt genoemd. 

Voor de multi tenant-App Service (een app die geen deel uitmaakt van de geïsoleerde laag), gebruikt u Virtual Network-integratie voor de toegang van uw app tot resources in uw virtuele netwerk.  Gebruik persoonlijke site toegang om een app alleen toegankelijk te maken vanuit een particulier netwerk, bijvoorbeeld een van een virtueel Azure-netwerk. Virtual Network integratie wordt alleen gebruikt om uitgaande oproepen van uw app naar uw Virtual Network te maken. De functie voor integratie van Virtual Network werkt anders als deze wordt gebruikt met een virtueel netwerk in dezelfde regio en met virtuele netwerken in andere regio's. 
 
- [Netwerkoverwegingen voor een App Service-omgeving](environment/network-info.md)

- [Een externe ASE maken](environment/create-external-ase.md)

- [Een interne ASE maken](environment/create-ilb-ase.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: niet-geautoriseerde overdracht van gevoelige gegevens controleren en blok keren

**Richt lijnen**: Hoewel functies voor gegevens identificatie, classificatie en verlies preventie nog niet beschikbaar zijn voor app service, kunt u het risico van gegevens exfiltration van het virtuele netwerk verminderen door alle regels te verwijderen waarin de bestemming een ' tag ' gebruikt voor Internet-of Azure-Services. 

Micro soft beheert de onderliggende infra structuur voor App Service en heeft strikte controles geïmplementeerd om verlies of bloot stelling van uw gegevens te voor komen.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: alle gevoelige gegevens in de overdracht versleutelen

**Richt lijnen**: gebruik de standaard minimale versie van TLS 1,2, geconfigureerd in TLS/SSL-instellingen, voor het versleutelen van alle gegevens die onderweg zijn. Zorg er ook voor dat alle HTTP-verbindings aanvragen worden omgeleid naar HTTPS.

- [Meer informatie over versleuteling in transit voor Azure App Service-web-apps](security-recommendations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: de [Security Bench Mark van Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) is het standaard beleids initiatief voor Security Center en is de basis voor de [aanbevelingen van Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). De Azure Policy definities die aan dit besturings element zijn gerelateerd, worden automatisch door Security Center ingeschakeld. Voor waarschuwingen met betrekking tot dit besturings element is mogelijk een [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -plan vereist voor de gerelateerde services.

**Ingebouwde definities Azure Policy-micro soft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 4.4](../../includes/policy/standards/asb/rp-controls/microsoft.web-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: een actief detectie hulpprogramma gebruiken om gevoelige gegevens te identificeren

**Hulp**: momenteel niet beschikbaar. De functies voor gegevens identificatie, classificatie en verlies preventie zijn nog niet beschikbaar voor App Service. 

Tags App Service-apps die gevoelige informatie kunnen verwerken. Implementeer, indien nodig, een oplossing van derden voor naleving.

Micro soft beheert het onderliggende platform en behandelt alle klant gegevens als vertrouwelijk en gaat naar een fantastische lengte om te beschermen tegen verlies en bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Azure RBAC gebruiken om de toegang tot resources te beheren 

**Richt lijnen**: gebruik Azure op rollen gebaseerd toegangs beheer (Azure RBAC) in azure Active Directory (Azure AD) om de toegang tot het app service besturings vlak op het Azure portal te beheren.

- [Azure RBAC configureren](../role-based-access-control/role-assignments-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: gevoelige informatie op rest versleutelen

**Richt lijnen**: website-inhoud in een app service-app, zoals bestanden, worden opgeslagen in azure Storage, waarmee de inhoud automatisch wordt versleuteld in rust. U kunt ervoor kiezen om toepassings geheimen op te slaan in Key Vault en deze op te halen tijdens runtime.

Door de klant verstrekte geheimen worden op rest versleuteld terwijl ze zijn opgeslagen in App Service configuratie databases.

Houd er rekening mee dat lokale gekoppelde schijven optioneel kunnen worden gebruikt door websites als tijdelijke opslag (bijvoorbeeld D:\Local en% TMP%), maar niet op rest versleuteld.

- [Besturings elementen voor gegevens beveiliging begrijpen voor Azure App Service](https://docs.microsoft.com/azure/app-service/security-recommendations#data-protection)

- [Azure Storage versleuteling in rust begrijpen](../storage/common/storage-service-encryption.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

**Hulp**: gebruik Azure monitor met Azure-activiteiten logboek om waarschuwingen te maken op basis van wijzigingen in productie app service apps en andere essentiële of verwante resources.

- [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteiten logboek](/azure/azure-monitor/platform/alerts-activity-log)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie voor meer informatie de [Azure Security Bench Mark: beveiligingslek beheer](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatische hulpprogram ma's voor het scannen van beveiligings problemen uitvoeren

**Richt lijnen**: Stel een DevSecOps-praktijk in om ervoor te zorgen dat uw app service-apps veilig zijn en beveiligd blijven gedurende de levens duur. DevSecOps maakt gebruik van het beveiligings team van uw organisatie en hun mogelijkheden in uw DevOps-procedures om de verantwoordelijkheid van iedereen in het team te beveiligen.

Bekijk en volg de aanbevelingen van Security Center voor het beveiligen van uw App Service-apps.

- [Continue beveiligings validatie toevoegen aan uw CI/CD-pijp lijn](https://docs.microsoft.com/azure/devops/migrate/security-validation-cicd-pipeline?preserve-view=true&amp;view=azure-devops)

- [Aanbevelingen voor de evaluatie van Azure Security Center-beveiligings problemen implementeren](../security-center/deploy-vulnerability-assessment-vm.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: een risico classificatie proces gebruiken om prioriteit te geven aan het herstel van ontdekte beveiligings problemen

**Hulp**: micro soft voert beveiligings beheer uit op de onderliggende systemen die ondersteuning bieden voor app service. U kunt echter de ernst van de aanbevelingen binnen Security Center gebruiken en de beveiligde Score om het risico binnen uw omgeving te meten. Uw beveiligde Score is gebaseerd op het aantal Security Center aanbevelingen dat u hebt verminderd. Denk na over de ernst van de aanbevelingen om eerst de prioriteit te bepalen.

- [Naslag Gids voor beveiligings aanbevelingen](../security-center/recommendations-reference.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie [Azure Security Bench Mark: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md)voor meer informatie.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatische Asset-detectie oplossing gebruiken

**Richt lijnen**: gebruik Azure resource Graph om alle resources (zoals compute, opslag, netwerk, poorten, protocollen enzovoort) in uw abonnementen te doorzoeken of te detecteren. Zorg ervoor dat de juiste machtigingen worden toegepast op uw Tenant en dat u alle Azure-abonnementen en resources in uw abonnementen kunt inventariseren.

Hoewel klassieke Azure-resources kunnen worden gedetecteerd via resource grafiek, is het raadzaam om Azure Resource Manager resources te maken en te gebruiken.

- [Query's maken met Azure resource Graph](../governance/resource-graph/first-query-portal.md)

- [Uw Azure-abonnementen weer geven](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?preserve-view=true&amp;view=azps-4.8.0)

- [Meer informatie over Azure RBAC](../role-based-access-control/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="62-maintain-asset-metadata"></a>6,2: meta gegevens van activa onderhouden

**Richt lijnen**: Tags Toep assen op Azure-resources met behulp van meta gegevens om ze logisch in een taxonomie te organiseren.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: niet-geautoriseerde Azure-resources verwijderen

**Richt lijnen**: Gebruik labels, beheer groepen en afzonderlijke abonnementen, waar nodig, om Azure-resources te organiseren en bij te houden. Sluit de inventarisatie regel matig af en zorg ervoor dat niet-geautoriseerde resources worden verwijderd uit uw abonnementen als onderdeel van dit proces.

Kies Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in klant abonnementen, met behulp van de volgende ingebouwde beleids definities:

- Niet toegestane resourcetypen
- Toegestane brontypen

Raadpleeg de koppelingen waarnaar wordt verwezen voor aanvullende informatie.

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Beheergroepen maken](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: de inventaris van goedgekeurde Azure-resources definiëren en onderhouden

**Richt lijnen**: een inventaris van goedgekeurde Azure-resources en goedgekeurde software voor reken resources maken op basis van de behoeften van uw organisatie.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

**Hulp**: gebruik Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in uw abonnementen.

Gebruik Azure resource Graph voor het opvragen of detecteren van resources binnen hun abonnementen.  Zorg ervoor dat alle Azure-resources die aanwezig zijn in de omgeving, zijn goedgekeurd. 

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md) 

- [Query's maken met Azure Graph](../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitor voor niet-goedgekeurde software toepassingen binnen reken resources

**Hulp**: Azure resource Graph gebruiken om resources in uw abonnementen op te vragen of te detecteren en ervoor te zorgen dat de gedetecteerde Azure-resources worden goedgekeurd op basis van uw organisatie beleid.

Gebruik webjobs in App Service voor het bewaken van niet-goedgekeurde software toepassingen die zijn geïmplementeerd in reken resources. Gebruik webjobs om een programma of script uit te voeren in hetzelfde exemplaar als een web-app, API-app of mobiele app. Definieer webconfiguraties en bewaking met Logboeken. Selecteer in de detail pagina voor het uitvoeren van webtaaks de optie uitvoer in-/uitschakelen om de tekst van de logboek inhoud te bekijken. Houd er rekening mee dat webjobs nog niet worden ondersteund voor App Service in Linux.

- [Achtergrond taken uitvoeren met webjobs in Azure App Service](webjobs-create.md)

- [Zelf studie-beleids regels maken en beheren om naleving af te dwingen](../governance/policy/tutorials/create-and-manage.md)

- [Quick Start: uw eerste resource grafiek query uitvoeren met Azure resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: niet-goedgekeurde Azure-resources en software toepassingen verwijderen

**Richt lijnen**: Zorg ervoor dat alle Azure-resources die in de omgeving aanwezig zijn, zijn goedgekeurd. Gebruik Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in uw abonnementen. Alle geïmplementeerde software toepassingen verwijderen die niet zijn goedgekeurd volgens uw organisatie beleid.

- [Achtergrond taken uitvoeren met webjobs in Azure App Service](webjobs-create.md)

- [Zelf studie-beleids regels maken en beheren om naleving af te dwingen](../governance/policy/tutorials/create-and-manage.md)

- [Quick Start: uw eerste resource grafiek query uitvoeren met Azure resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="68-use-only-approved-applications"></a>6,8: alleen goedgekeurde toepassingen gebruiken

**Richt lijnen**: Zorg ervoor dat alle Azure-resources die in de omgeving aanwezig zijn, zijn goedgekeurd. Gebruik Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in uw abonnementen. Alle geïmplementeerde software toepassingen verwijderen die niet zijn goedgekeurd volgens uw organisatie beleid. 

- [Achtergrond taken uitvoeren met webjobs in Azure App Service](webjobs-create.md)

- [Zelf studie-beleids regels maken en beheren om naleving af te dwingen](../governance/policy/tutorials/create-and-manage.md)

- [Quick Start: uw eerste resource grafiek query uitvoeren met Azure resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="69-use-only-approved-azure-services"></a>6,9: alleen goedgekeurde Azure-Services gebruiken

**Richt lijnen**: Maak een proces om de niet-geautoriseerde Azure-Services periodiek te controleren om ervoor te zorgen dat alleen geautoriseerde Azure-Services in uw abonnementen worden gebruikt.

Gebruik Azure resource Graph binnen dit proces om resources te zoeken of te detecteren binnen hun abonnementen. Zorg ervoor dat alle Azure-resources die aanwezig zijn in de omgeving, zijn goedgekeurd.

Configureer Azure Policy om beperkingen toe te voegen aan het type resources dat in uw abonnementen kan worden gemaakt met behulp van de volgende ingebouwde beleids definities:

- Niet toegestane resourcetypen

- Toegestane brontypen

Gebruik webjobs in App Service voor het bewaken van niet-goedgekeurde software toepassingen die zijn geïmplementeerd in computer bronnen. Gebruik webjobs om een programma of script uit te voeren in hetzelfde exemplaar als een web-app, API-app of mobiele app. Definieer webconfiguraties en bewaking met Logboeken. Selecteer in de detail pagina voor het uitvoeren van webtaaks de optie uitvoer in-/uitschakelen om de tekst van de logboek inhoud te bekijken. Houd er rekening mee dat webjobs nog niet worden ondersteund voor App Service in Linux.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een specifiek resource type weigeren met Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

- [Achtergrond taken uitvoeren met webjobs in Azure App Service](webjobs-create.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: een inventaris van goedgekeurde software titels onderhouden

**Richt lijnen**: Implementeer een proces voor inventarisatie en controleer de software titels in uw abonnementen op periodieke basis om ervoor te zorgen dat alleen geautoriseerde Azure-Services in uw abonnementen worden gebruikt.

Gebruik Azure resource Graph om binnen dit proces een query uit te zoeken of bronnen te zoeken in uw abonnementen. Zorg ervoor dat alle Azure-bronnen die in de omgeving zijn gedetecteerd, zijn goedgekeurd.

Configureer Azure Policy om beperkingen toe te voegen aan het type resources dat kan worden gemaakt in klant abonnementen met behulp van de volgende ingebouwde beleids definities:

- Niet toegestane resourcetypen

- Toegestane brontypen

Gebruik ook webjobs in App Service voor het inventariseren van niet-goedgekeurde software toepassingen die zijn geïmplementeerd in computer bronnen. Definieer de configuratie en bewaking met Logboeken. Selecteer in de detail pagina voor het uitvoeren van webtaaks de optie uitvoer in-/uitschakelen om de tekst van de logboek inhoud te bekijken. Houd er rekening mee dat webjobs nog niet worden ondersteund voor App Service in Linux.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een specifiek resource type weigeren met Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

**Hulp** bij het configureren van voorwaardelijke toegang van Azure voor het beperken van de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager, door ' blok toegang ' te configureren voor de app Microsoft Azure management.

- [Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager te blok keren](../role-based-access-control/conditional-access-azure-management.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: de mogelijkheid van gebruikers om scripts uit te voeren binnen reken bronnen beperken

**Hulp**: webjobs in app service klanten in staat stellen om een programma of script uit te voeren in hetzelfde exemplaar als een web-app, API-app of mobiele app. U bent verantwoordelijk voor het definiëren van uw configuratie om scripts te beperken of te beperken, die niet zijn toegestaan door de organisatie. App Service biedt geen mechanisme om de uitvoering van scripts systeem eigen te beperken. Houd er rekening mee dat webjobs nog niet worden ondersteund voor App Service in Linux.

- [Achtergrond taken uitvoeren met webjobs in Azure App Service](webjobs-create.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: toepassingen met een hoog risico fysiek of logisch scheiden

**Hulp**: implementeer afzonderlijke abonnementen of beheer groepen om isolatie te bieden voor app service apps met een hoog risico. Implementeer een app met een hoger risico in een eigen Virtual Network, omdat de perimeter beveiliging in App Service wordt bereikt door het gebruik van virtuele netwerken. De App Service Environment is een implementatie van App Service in een subnet in uw Azure Virtual Network. 

Er zijn twee soorten Application Service-omgeving, externe Application Service Environment en ILB (interne Load Balancer) toepassings service omgeving. Kies de beste architectuur op basis van uw vereisten.

- [Netwerkoverwegingen voor een App Service-omgeving](environment/network-info.md)

- [Een externe App Service omgeving maken](environment/create-external-ase.md)

- [Een App Service-omgeving voor een interne load balancer maken en gebruiken](environment/create-ilb-ase.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [Azure Security Bench Mark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md)(Engelstalig) voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources

**Hulp**: Definieer en implementeer standaard beveiligings configuraties voor uw app Service geïmplementeerde apps met Azure Policy.

Gebruik Azure Policy aliassen in de naam ruimte ' micro soft. Web ' om aangepaste beleids regels te maken om de configuratie van uw App Service Web Apps te controleren of af te dwingen.

Pas ingebouwde beleids definities toe, zoals:

- App Service moet gebruikmaken van een service-eindpunt voor een virtueel netwerk

- Webtoepassingen moeten alleen toegankelijk zijn via HTTPS

- De meest recente TLS-versie in uw apps gebruiken

Het is raadzaam om het proces te documenteren om de ingebouwde beleids definities toe te passen op gestandaardiseerd gebruik.   

- [Beschik bare Azure Policy aliassen weer geven](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&amp;view=azps-4.8.0)

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: Beveilig Azure-resource configuraties onderhouden

**Hulp**: gebruik Azure Policy [deny] en [indien niet aanwezig] effecten om beveiligde instellingen af te dwingen voor uw Azure app service apps.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy effecten begrijpen](../governance/policy/concepts/effects.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan

**Hulp**: Kies Azure DevOps of Azure opslag plaatsen om uw code veilig op te slaan en te beheren wanneer u aangepaste Azure Policy definities gebruikt.

Gebruik uw bestaande, doorlopende integratie (CI) en continue levering (CD)-pijp lijn voor het implementeren van een bekende, beveiligde configuratie.

- [Code opslaan in azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?preserve-view=true&amp;view=azure-devops)

- [Documentatie voor Azure opslag plaatsen](https://docs.microsoft.com/azure/devops/repos/?preserve-view=true&amp;view=azure-devops)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: hulpprogram ma's voor configuratie beheer voor Azure-resources implementeren

**Hulp**: gebruik ingebouwde Azure Policy definities en Azure Policy aliassen in de naam ruimte ' micro soft. Web ' om aangepaste beleids regels te maken om systeem configuraties te Signa lering, te controleren en af te dwingen. Ontwikkel een proces en pijp lijn voor het beheren van beleids uitzonderingen.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-resources implementeren

**Hulp**: gebruik ingebouwde Azure Policy definities en Azure Policy aliassen in de naam ruimte ' micro soft. Web ' om aangepaste beleids regels te maken om systeem configuraties te Signa lering, te controleren en af te dwingen. 

Toep assen Azure Policy [audit], [deny] en [Deploy if exists], effect om automatisch configuraties af te dwingen voor uw Azure-resources.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure-geheimen veilig beheren

**Hulp**: gebruik beheerde identiteiten om uw app service-apps te voorzien van een automatisch beheerde identiteit in azure Active Directory (Azure AD). Met beheerde identiteiten kunnen uw apps worden geverifieerd bij elke service die ondersteuning biedt voor Azure AD-verificatie, met inbegrip van Key Vault, zonder enige referenties in uw code. Zorg ervoor dat zacht verwijderen is ingeschakeld in Azure Key Vault.

- [Zacht verwijderen inschakelen in Azure Key Vault](../key-vault/general/key-vault-recovery.md)

- [Beheerde identiteiten voor App Service gebruiken](overview-managed-identity.md)

- [Key Vault verificatie bieden met een beheerde identiteit](../key-vault/general/assign-access-policy-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: identiteiten veilig en automatisch beheren

**Richt lijnen**: gebruik beheerde identiteiten om uw door app Service geïmplementeerde apps te voorzien van een automatisch beheerde identiteit in azure Active Directory (Azure AD). Met beheerde identiteiten kunnen uw apps worden geverifieerd bij elke service die ondersteuning biedt voor Azure AD-verificatie, met inbegrip van Key Vault, zonder enige referenties in uw code.

- [Beheerde identiteiten voor App Service gebruiken](overview-managed-identity.md)

- [Key Vault verificatie bieden met een beheerde identiteit](../key-vault/general/assign-access-policy-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: de [Security Bench Mark van Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) is het standaard beleids initiatief voor Security Center en is de basis voor de [aanbevelingen van Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). De Azure Policy definities die aan dit besturings element zijn gerelateerd, worden automatisch door Security Center ingeschakeld. Voor waarschuwingen met betrekking tot dit besturings element is mogelijk een [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -plan vereist voor de gerelateerde services.

**Ingebouwde definities Azure Policy-micro soft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 7.12](../../includes/policy/standards/asb/rp-controls/microsoft.web-7-12.md)]

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: onbedoelde referentie blootstelling elimineren

**Richt lijnen**: referentie scanner implementeren om referenties in code te identificeren. Door het gebruik van Credential Scanner worden gebruikers ook aangemoedigd om gedetecteerde referenties naar veiligere locaties, zoals Azure Key Vault, te verplaatsen.

- [Referentie scanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="data-recovery"></a>Gegevensherstel

*Zie [Azure Security Bench Mark: Data Recovery](../security/benchmarks/security-control-data-recovery.md)(Engelstalig) voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zorg voor regel matige automatische back-ups

**Hulp**: met de functie voor back-up en terugzetten in app service kunt u eenvoudig hand matig app-back-ups maken of volgens een planning. U kunt instellen dat de back-ups tot een onbeperkte tijd worden bewaard. U kunt de app herstellen naar een moment opname van een vorige status door de bestaande app te overschrijven of te herstellen naar een andere app.

App Service kunt een back-up maken van de volgende gegevens naar een Azure-opslag account en-container, die u hebt geconfigureerd voor het gebruik van uw app:
- App-configuratie
- Bestandsinhoud
- Data base verbonden met uw app

Zorg ervoor dat regel matige en automatische back-ups worden uitgevoerd met een frequentie zoals gedefinieerd door uw organisatie beleid.

- [Meer informatie over Azure App Service back-upfunctie](manage-backup.md)

- [Door de klant beheerde sleutels voor Azure Storage versleuteling](../storage/common/customer-managed-keys-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: volledige back-ups van het systeem uitvoeren en een back-up maken van door de klant beheerde sleutels

**Hulp**: gebruik de functie back-up en herstellen van app service om een back-up te maken van uw toepassingen. Voor de back-upfuncties is een Azure Storage account vereist voor het opslaan van de back-upgegevens van uw toepassing.

- Azure Storage biedt versleuteling op rest-gebruik door het systeem geleverde sleutels of uw eigen door de klant beheerde sleutels. Hier worden uw toepassings gegevens opgeslagen wanneer deze niet worden uitgevoerd in een web-app in Azure.
- Uitvoeren vanuit een implementatie pakket is een implementatie functie van App Service. Hiermee kunt u uw site-inhoud vanuit een Azure Storage-account implementeren met behulp van een URL voor Shared Access Signature (SAS).

- Key Vault verwijzingen zijn een beveiligings functie van App Service. Hiermee kunt u geheimen in runtime importeren als toepassings instellingen. Gebruik deze om de SAS-URL van uw Azure Storage-account te versleutelen.

Meer informatie vindt u op de koppelingen waarnaar wordt verwezen.

- [Back-up maken van uw app in Azure](manage-backup.md)

- [Een app die wordt uitgevoerd in Azure App Service herstellen](web-sites-restore.md)

- [Meer informatie over versleuteling van data-at-rest in Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest#encryption-at-rest-in-microsoft-cloud-services) 

- [Versleutelings model en sleutel beheer tabel](../security/fundamentals/encryption-atrest.md)

- [Versleuteling in rust met door de klant beheerde sleutels](configure-encrypt-at-rest-using-cmk.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: alle back-ups valideren, inclusief door de klant beheerde sleutels

**Hulp**: test het herstel proces periodiek op back-ups van uw app service-toepassingen.

- [Back-up maken van uw app in Azure](manage-backup.md)

- [Een Azure App Service web-app herstellen](web-sites-restore.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zorg voor de bescherming van back-ups en door de klant beheerde sleutels

**Hulp**: app service back-ups worden opgeslagen in een Azure Storage-account. Gegevens in Azure Storage worden transparant versleuteld en ontsleuteld met 256-bits AES-versleuteling, een van de krach tigste blok cijfers die beschikbaar zijn en is compatibel met FIPS 140-2. Azure Storage versleuteling lijkt op BitLocker-versleuteling in Windows.

Azure Storage versleuteling is ingeschakeld voor alle opslag accounts, met inbegrip van Resource Manager en klassieke opslag accounts. Azure Storage versleuteling kan niet worden uitgeschakeld. Omdat uw gegevens standaard worden beveiligd, hoeft u uw code of toepassingen niet te wijzigen om Azure Storage versleuteling te benutten.

Gegevens in een opslag account worden standaard versleuteld met door micro soft beheerde sleutels. U kunt gebruikmaken van door micro soft beheerde sleutels voor het versleutelen van uw gegevens, maar u kunt ook versleuteling beheren met uw eigen sleutels. Zorg ervoor dat zacht verwijderen is ingeschakeld in Azure Key Vault.

- [Azure Storage versleuteling voor Data-at-rest begrijpen](../storage/common/storage-service-encryption.md)

- [Zacht verwijderen inschakelen in Azure Key Vault](../key-vault/general/key-vault-recovery.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Benchmark: respons op incidenten](../security/benchmarks/security-control-incident-response.md) voor meer informatie.*

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

Daarnaast kunt u ook eenvoudig abonnementen markeren (bijvoorbeeld productie, niet-productie) en een naamgevings systeem maken om Azure-resources duidelijk te identificeren en te categoriseren.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="103-test-security-response-procedures"></a>10,3: procedures voor beveiligings antwoorden testen

**Richt lijnen**: oefeningen uitvoeren om de respons mogelijkheden van uw systeem op een gewone uitgebracht te testen. Stel vast waar zich zwakke plekken en hiaten bevinden, en wijzig zo nodig het plan.

- [Raadpleeg de publicatie handleiding van het NIST voor het testen, trainen en uitoefenen van Program Ma's voor IT-plannen en-mogelijkheden](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen configureren voor beveiligings incidenten

**Hulp**: contact gegevens van beveiligings incidenten worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat de gegevens van de klant zijn geopend door een onrecht matige of niet-gemachtigde partij.  Bekijk incidenten na het feit om te controleren of de problemen zijn opgelost.

- [De Azure Security Center Security-contact persoon instellen](../security-center/security-center-provide-security-contact-details.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: beveiligings waarschuwingen opnemen in uw reactie systeem van uw incident

**Richt lijnen**: uw Security Center waarschuwingen en aanbevelingen exporteren met de functie continue export. Met doorlopend exporteren kunt u waarschuwingen en aanbevelingen hand matig of op een doorlopende manier exporteren. Gebruik de Security Center Data Connector om de waarschuwingen te streamen naar Azure Sentinel volgens de behoeften van het bedrijf.

- [Continue export configureren](../security-center/continuous-export.md)

- [Waarschuwingen streamen naar Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: de reactie op beveiligings waarschuwingen automatiseren

**Hulp**: gebruik de functie werk stroom automatisering in Security Center om automatisch reacties te activeren via ' Logic apps ' in beveiligings waarschuwingen en aanbevelingen.

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

- Zie [Overzicht Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Meer informatie over [Azure-beveiligingsbasislijnen](/azure/security/benchmarks/security-baselines-overview)
