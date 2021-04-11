---
title: Azure-beveiligings basislijn voor Cognitive Services
description: De Cognitive Services Security Baseline voorziet in procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-Bench Mark.
author: msmbaldwin
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 218810183f547d4e90043364a318615a204df9d8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105044852"
---
# <a name="azure-security-baseline-for-cognitive-services"></a>Azure-beveiligings basislijn voor Cognitive Services

In deze beveiligings basislijn worden richt lijnen van de [Azure Security Bench Mark-versie 1,0](../security/benchmarks/overview-v1.md) ingesteld op Cognitive Services. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen.
De inhoud wordt gegroepeerd op de **beveiligings controles** die zijn gedefinieerd door de Azure Security-benchmark en de bijbehorende richt lijnen die van toepassing zijn op Cognitive Services. **Besturings elementen** die niet van toepassing zijn op Cognitive Services, zijn uitgesloten.

 
Als u wilt zien hoe Cognitive Services volledig is toegewezen aan de beveiligings benchmark van Azure, raadpleegt u het [volledige Cognitive Services beveiligings basislijn toewijzings bestand](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Benchmark: netwerkbeveiliging](../security/benchmarks/security-control-network-security.md) voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-resources in virtuele netwerken beveiligen

**Hulp**: Microsoft Azure Cognitive Services biedt een gelaagd beveiligings model. Met dit model kunt u uw Cognitive Services-accounts beveiligen met een specifieke subset van netwerken. Als er netwerkregels zijn geconfigureerd, hebben alleen toepassingen die gegevens aanvragen via de opgegeven set netwerken toegang tot het account. U kunt de toegang tot uw resources beperken met aanvraag filtering, zodat alleen aanvragen worden toegestaan die afkomstig zijn van opgegeven IP-adressen, IP-adresbereiken of uit een lijst met subnetten in azure Virtual Networks.

Ondersteuning voor het virtuele netwerk en het service-eind punt voor Cognitive Services is beperkt tot een specifieke set regio's.

- [Azure Cognitive Services virtuele netwerken configureren](./cognitive-services-virtual-networks.md?tabs=portal)

- [Overzicht van Azure Virtual Networks](../virtual-network/virtual-networks-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: de configuratie en het verkeer van virtuele netwerken, subnetten en netwerk interfaces bewaken en vastleggen

**Richt lijnen**: wanneer virtual machines worden geïmplementeerd in hetzelfde virtuele netwerk als uw Cognitive Services-container, kunt u netwerk beveiligings groepen gebruiken om het risico van gegevens exfiltration te verminderen. Schakel logboeken stroom voor netwerk beveiligings groepen in en verzend logboeken naar een Azure Storage account voor verkeers controle. U kunt ook stroom logboeken voor netwerk beveiligings groepen naar een Log Analytics-werk ruimte verzenden en Traffic Analytics gebruiken om inzicht te krijgen in de verkeers stroom in uw Azure-Cloud. Enkele voor delen van Traffic Analytics zijn de mogelijkheid om netwerk activiteiten te visualiseren en HOTS pots te identificeren, beveiligings dreigingen te identificeren, verkeers patronen te begrijpen en netwerk configuraties te lokaliseren.

- [NSG-stroom logboeken inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Traffic Analytics inschakelen en gebruiken](../network-watcher/traffic-analytics.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="13-protect-critical-web-applications"></a>1,3: essentiële webtoepassingen beveiligen

**Richt lijnen**: als u Cognitive services binnen een container gebruikt, kunt u de implementatie van de container uitbreiden met een web-to-end firewall oplossing voor webtoepassingen die kwaad aardig verkeer filtert en end-to-TLS-versleuteling ondersteunt, waardoor het container eindpunt persoonlijk en beveiligd blijft. 

Houd er rekening mee dat Cognitive Services containers vereist zijn voor het indienen van meet gegevens voor facturerings doeleinden. De enige uitzonde ring is offline containers, aangezien ze een andere facturerings methodologie volgen. Als u geen toestemming geeft voor de lijst met verschillende netwerk kanalen waarvan de Cognitive Services containers afhankelijk zijn, wordt voor komen dat de container werkt. De host moet lijst poort 443 en de volgende domeinen toestaan:

- *. cognitive.microsoft.com
- *. cognitiveservices.azure.com

Houd er ook rekening mee dat u grondige pakket inspectie voor uw firewall oplossing moet uitschakelen op de beveiligde kanalen die de Cognitive Services-containers maken voor micro soft-servers. Als u dit niet doet, kan de container niet goed functioneren.

- [Azure Cognitive Services-container beveiliging begrijpen](./cognitive-services-container-support.md#azure-cognitive-services-container-security)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: communicatie met bekende schadelijke IP-adressen weigeren

**Richt lijnen**: wanneer virtuele machines worden geïmplementeerd in hetzelfde virtuele netwerk als uw Cognitive Services-container, definieert en implementeert u standaard beveiligings configuraties voor gerelateerde netwerk bronnen met Azure Policy. Gebruik Azure Policy aliassen in de naam ruimten ' micro soft. CognitiveServices ' en ' micro soft. Network ' om aangepaste beleids regels te maken om de netwerk configuratie van uw Azure-cache voor redis-exemplaren te controleren of af te dwingen. U kunt ook gebruik maken van ingebouwde beleids definities zoals:

- De DDoS Protection-standaard moet zijn ingeschakeld

Gebruik Azure-blauw drukken om grootschalige Azure-implementaties te vereenvoudigen door sleutel omgevings artefacten, zoals Azure Resource Manager sjablonen, Toegangs beheer op basis van rollen (Azure RBAC) en-beleid, in één blauw definitie te verpakken. Pas de blauw druk toe op nieuwe abonnementen en omgevingen en Verfijn de controle en het beheer via versies.

Als u Cognitive Services binnen een container gebruikt, kunt u uw container implementatie uitbreiden met een webtoepassing voor front-to-application firewall die schadelijk verkeer filtert en end-to-end TLS-code ring ondersteunt, waardoor het container eindpunt persoonlijk en beveiligd blijft. 

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een Azure Blueprint maken](../governance/blueprints/create-blueprint-portal.md)

- [Azure Cognitive Services-container beveiliging begrijpen](./cognitive-services-container-support.md#azure-cognitive-services-container-security)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="15-record-network-packets"></a>1,5: netwerk pakketten opnemen

**Richt lijnen**: wanneer virtuele machines worden geïmplementeerd in hetzelfde virtuele netwerk als uw Cognitive Services-container, kunt u netwerk beveiligings groepen gebruiken om het risico van gegevens exfiltration te verminderen. Schakel logboeken stroom voor netwerk beveiligings groepen in en verzend logboeken naar een Azure Storage account voor verkeers controle. U kunt ook stroom logboeken voor netwerk beveiligings groepen naar een Log Analytics-werk ruimte verzenden en Traffic Analytics gebruiken om inzicht te krijgen in de verkeers stroom in uw Azure-Cloud. Enkele voor delen van Traffic Analytics zijn de mogelijkheid om netwerk activiteiten te visualiseren en HOTS pots te identificeren, beveiligings dreigingen te identificeren, verkeers patronen te begrijpen en netwerk configuraties te lokaliseren.

- [NSG-stroom logboeken inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Traffic Analytics inschakelen en gebruiken](../network-watcher/traffic-analytics.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: op netwerk gebaseerde inbreuk detectie/indringings systemen (ID'S/IP-adressen) implementeren

**Richt lijnen**: als u Cognitive services binnen een container gebruikt, kunt u de implementatie van de container uitbreiden met een web-to-end firewall oplossing voor webtoepassingen die kwaad aardig verkeer filtert en end-to-TLS-versleuteling ondersteunt, waardoor het container eindpunt persoonlijk en beveiligd blijft.  U kunt een aanbieding selecteren in de Azure Marketplace die ondersteuning biedt voor ID'S/IP-adressen, met de mogelijkheid om Payload-inspectie uit te scha kelen.

Houd er rekening mee dat Cognitive Services containers vereist zijn voor het indienen van meet gegevens voor facturerings doeleinden. De enige uitzonde ring hierop is offline containers, aangezien ze een andere facturerings methodologie volgen. Als u geen toestemming geeft voor de lijst met verschillende netwerk kanalen waarvan de Cognitive Services containers afhankelijk zijn, wordt voor komen dat de container werkt. De host moet lijst poort 443 en de volgende domeinen toestaan:

- *. cognitive.microsoft.com
- *. cognitiveservices.azure.com

Houd er ook rekening mee dat u grondige pakket inspectie voor uw firewall oplossing moet uitschakelen op de beveiligde kanalen die de Cognitive Services-containers maken voor micro soft-servers. Als u dit niet doet, kan de container niet goed functioneren.

- [Azure Cognitive Services-container beveiliging begrijpen](./cognitive-services-container-support.md#azure-cognitive-services-container-security)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="17-manage-traffic-to-web-applications"></a>1,7: verkeer naar webtoepassingen beheren

**Richt lijnen**: als u Cognitive services binnen een container gebruikt, kunt u de implementatie van de container uitbreiden met een web-to-end firewall oplossing voor webtoepassingen die kwaad aardig verkeer filtert en end-to-TLS-versleuteling ondersteunt, waardoor het container eindpunt persoonlijk en beveiligd blijft. 

Houd er rekening mee dat Cognitive Services containers vereist zijn voor het indienen van meet gegevens voor facturerings doeleinden. De enige uitzonde ring is offline containers, aangezien ze een andere facturerings methodologie volgen. Als u geen toestemming geeft voor de lijst met verschillende netwerk kanalen waarvan de Cognitive Services containers afhankelijk zijn, wordt voor komen dat de container werkt. De host moet lijst poort 443 en de volgende domeinen toestaan:

- *. cognitive.microsoft.com
- *. cognitiveservices.azure.com

Houd er ook rekening mee dat u grondige pakket inspectie voor uw firewall oplossing moet uitschakelen op de beveiligde kanalen die de Cognitive Services-containers maken voor micro soft-servers. Als u dit niet doet, kan de container niet goed functioneren.

- [Azure Cognitive Services-container beveiliging begrijpen](./cognitive-services-container-support.md#azure-cognitive-services-container-security)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: de complexiteit en administratieve overhead van netwerk beveiligings regels minimaliseren

**Richt lijnen**: gebruik Tags voor het virtuele netwerk om netwerk toegangs beheer te definiëren voor netwerk beveiligings groepen of Azure firewall. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de service label (bijvoorbeeld ApiManagement) op te geven in het juiste bron-of doel veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen.

U kunt ook toepassings beveiligings groepen gebruiken om complexe beveiligings configuratie te vereenvoudigen. Met behulp van toepassingsbeveiligingsgroepen kunt u netwerkbeveiliging configureren als een natuurlijk verlengstuk van de structuur van een toepassing, waarbij u virtuele machines kunt groeperen en netwerkbeveiligingsbeleid kunt definiëren op basis van die groepen.

- [Service tags van virtueel netwerk](../virtual-network/service-tags-overview.md)

- [Toepassings beveiligings groepen](../virtual-network/network-security-groups-overview.md#application-security-groups)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: standaard beveiligings configuraties voor netwerk apparaten onderhouden

**Hulp**: Definieer en implementeer standaard beveiligings configuraties voor netwerk bronnen die betrekking hebben op uw Cognitive Services-container met Azure Policy. Gebruik Azure Policy aliassen in de naam ruimten ' micro soft. CognitiveServices ' en ' micro soft. Network ' om aangepaste beleids regels te maken om de netwerk configuratie van uw Azure-cache voor redis-exemplaren te controleren of af te dwingen.

U kunt ook Azure-blauw drukken gebruiken om grootschalige Azure-implementaties te vereenvoudigen door sleutel omgevings artefacten, zoals Azure Resource Manager sjablonen, op rollen gebaseerd toegangs beheer (Azure RBAC) en beleids regels, op te lossen in één definitie van een blauw druk. Pas de blauw druk toe op nieuwe abonnementen en omgevingen en Verfijn de controle en het beheer via versies.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een Azure Blueprint maken](../governance/blueprints/create-blueprint-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="110-document-traffic-configuration-rules"></a>1,10: configuratie regels voor het document verkeer

**Richt lijnen**: Gebruik labels voor netwerk bronnen die zijn gekoppeld aan uw Cognitive Services-container om ze logisch in een taxonomie te organiseren.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische hulpprogram ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

**Hulp**: gebruik het Azure-activiteiten logboek om netwerk resource configuraties te bewaken en wijzigingen te detecteren voor netwerk bronnen die betrekking hebben op uw Cognitive Services-container. Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer er wijzigingen in kritieke netwerk bronnen plaatsvinden.

- [Activiteiten logboek gebeurtenissen van Azure weer geven en ophalen](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Waarschuwingen maken in Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie [Azure Security Bench Mark: Logging and monitoring](../security/benchmarks/security-control-logging-monitoring.md)(Engelstalig) voor meer informatie.*

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

**Hulp**: Schakel Diagnostische instellingen voor Azure-activiteiten logboek in en verzend de logboeken naar een log Analytics-werk ruimte, een Azure Event hub of een Azure-opslag account voor archivering. Activiteiten logboeken bieden inzicht in de bewerkingen die zijn uitgevoerd op uw Cognitive Services-container op het niveau van het besturings element. Met Azure-activiteiten logboek gegevens kunt u de ' What, wie en wanneer ' bepalen voor schrijf bewerkingen (PUT, POST, DELETE) die zijn uitgevoerd op het niveau van het besturings vlak voor uw Azure-cache voor redis-exemplaren.

- [Diagnostische instellingen voor Azure-activiteiten logboek inschakelen](../azure-monitor/essentials/activity-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources

**Richt lijnen**: voor het bijhouden van de controle op vliegtuig controles, schakelt u Diagnostische instellingen voor Azure-activiteiten logboek in en verzendt u de logboeken naar een log Analytics-werk ruimte, Azure Event hub of Azure Storage-account voor archivering. Met Azure-activiteiten logboek gegevens kunt u de ' What, wie en wanneer ' bepalen voor schrijf bewerkingen (PUT, POST, DELETE) die zijn uitgevoerd op het niveau van het controle vlak voor uw Azure-resources.

Daarnaast verzendt Cognitive Services diagnostische gebeurtenissen die kunnen worden verzameld en gebruikt voor analyse, waarschuwingen en rapportage. U kunt de diagnostische instellingen voor een Cognitive Services container configureren via de Azure Portal. U kunt een of meer diagnostische gebeurtenissen verzenden naar een opslag account, Event hub of een Log Analytics-werk ruimte.

- [Diagnostische instellingen voor Azure-activiteiten logboek inschakelen](../azure-monitor/essentials/activity-log.md)

- [Diagnostische instellingen gebruiken voor Azure Cognitive Services](diagnostic-logging.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren

**Hulp**: stel binnen Azure monitor uw Bewaar periode voor log Analytics werk ruimte in volgens de nalevings voorschriften van uw organisatie. Gebruik Azure Storage-accounts voor lange termijn/archiverings opslag.

- [Para meters voor het bewaren van Logboeken instellen voor Log Analytics-werk ruimten](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren

**Hulp**: Schakel Diagnostische instellingen voor Azure-activiteiten logboek in en verzend de logboeken naar een log Analytics-werk ruimte. Deze logboeken bevatten uitgebreide, frequente gegevens over de werking van een resource die worden gebruikt voor het identificeren van problemen en fout opsporing. Voer query's uit in Log Analytics om zoek termen te zoeken, trends te identificeren, patronen te analyseren en veel andere inzichten te bieden op basis van de activiteiten logboek gegevens die mogelijk zijn verzameld voor Azure Cognitive Services.

- [Diagnostische instellingen voor Azure-activiteiten logboek inschakelen](../azure-monitor/essentials/activity-log.md)

- [Azure-activiteiten logboeken verzamelen en analyseren in Log Analytics werk ruimte in Azure Monitor](../azure-monitor/essentials/activity-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteiten

**Richt lijnen**: u kunt waarschuwingen over ondersteunde metrische gegevens in cognitive Services genereren door te gaan naar de sectie waarschuwingen en metrische gegevens in azure monitor.

Configureer Diagnostische instellingen voor uw Cognitive Services-container en verzend logboeken naar een Log Analytics-werk ruimte. Configureer in uw Log Analytics-werk ruimte de waarschuwingen die moeten worden uitgevoerd wanneer een vooraf gedefinieerde set voor waarden plaatsvindt. U kunt ook gegevens in-of uitschakelen voor Azure Sentinel of een SIEM van derden.

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

- [Logboek waarschuwingen maken, weer geven en beheren met behulp van Azure Monitor](../azure-monitor/alerts/alerts-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [Azure Security Bench Mark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: een inventaris van beheerders accounts onderhouden

**Hulp**: Azure Active Directory (Azure AD) heeft ingebouwde rollen die expliciet moeten worden toegewezen en waarop query's kunnen worden doorzocht. Gebruik de Azure AD Power shell-module om ad hoc-query's uit te voeren om accounts te detecteren die lid zijn van beheer groepen.

- [Een directory-rol verkrijgen in azure AD met Power shell](/powershell/module/azuread/get-azureaddirectoryrole?amp;preserve-view=true&view=azureadps-2.0)

- [Leden van een directory-rol in azure AD ophalen met Power shell](/powershell/module/azuread/get-azureaddirectoryrolemember?amp;preserve-view=true&view=azureadps-2.0)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="32-change-default-passwords-where-applicable"></a>3,2: standaard wachtwoorden wijzigen indien van toepassing

**Hulp**: beheer van toegang tot Cognitive Services wordt beheerd via Azure Active Directory (Azure AD). Azure AD heeft niet het concept van standaard wachtwoorden.

De toegang tot het gegevens vlak tot Cognitive Services wordt beheerd via toegangs sleutels. Deze sleutels worden gebruikt door de clients die verbinding maken met uw cache en kunnen op elk gewenst moment opnieuw worden gegenereerd.

Het is niet raadzaam om standaard wachtwoorden te bouwen in uw toepassing. In plaats daarvan kunt u uw wacht woorden opslaan in Azure Key Vault en vervolgens Azure AD gebruiken om deze op te halen.

- [Azure-cache opnieuw genereren voor redis-toegangs sleutels](../azure-cache-for-redis/cache-configure.md#settings)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: speciale beheerders accounts gebruiken

**Richt lijnen**: Maak standaard procedures voor het gebruik van specifieke beheerders accounts. Gebruik Security Center identiteits-en toegangs beheer om het aantal beheerders accounts te bewaken.

Daarnaast kunt u aanbevelingen van Security Center of ingebouwde Azure-beleids regels gebruiken om u te helpen bij het bijhouden van specifieke beheerders accounts, zoals:

- Er moet meer dan één eigenaar zijn toegewezen aan uw abonnement
- Afgeschafte accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement

- [Azure Security Center gebruiken om identiteit en toegang te bewaken (preview)](../security-center/security-center-identity-access.md)

- [Azure Policy gebruiken](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: gebruik Azure Active Directory eenmalige aanmelding (SSO)

**Hulp**: Cognitive Services maakt gebruik van toegangs sleutels om gebruikers te verifiëren en biedt geen ondersteuning voor eenmalige aanmelding (SSO) op het niveau van het gegevens vlak. Toegang tot het besturings vlak voor Cognitive Services is beschikbaar via REST API en ondersteunt SSO. Als u zich wilt verifiëren, stelt u de autorisatie-header voor uw aanvragen in op een JSON-webtoken (JavaScript Object Notation) dat u hebt verkregen via Azure Active Directory (Azure AD).

- [Meer informatie over Azure Cognitive Services REST API](/rest/api/cognitiveservices/)

- [Informatie over eenmalige aanmelding met Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: multi-factor Authentication gebruiken voor alle op Azure Active Directory gebaseerde toegang

**Hulp**: Schakel Azure Active Directory (Azure AD) multi-factor Authentication in en volg Security Center aanbevelingen voor identiteits-en toegangs beheer.

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

**Hulp**: gebruik Azure Active Directory (Azure AD) PRIVILEGED Identity Management (PIM) voor het genereren van Logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten in de omgeving worden uitgevoerd.

Daarnaast kunt u met Azure AD-risico detectie waarschuwingen en rapporten bekijken over Risk ante gebruikers gedrag.

- [Privileged Identity Management implementeren (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Meer informatie over Azure AD-risico detectie](../active-directory/identity-protection/overview-identity-protection.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Hulp** bij het configureren van benoemde locaties in azure Active Directory (Azure AD) voorwaardelijke toegang om alleen toegang toe te staan vanaf specifieke logische groepen met IP-adresbereiken of landen en regio's.

- [Benoemde locaties configureren in azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory gebruiken

**Hulp**: gebruik Azure Active Directory (Azure AD) als centrale verificatie-en autorisatie systeem. Azure AD beveiligt gegevens door gebruik te maken van sterke code ring voor gegevens in rust en onderweg, met zouten, hashes en het veilig opslaan van gebruikers referenties. Als uw use-case AD-verificatie ondersteunt, gebruikt u Azure AD om aanvragen voor uw Cognitive Services-API te verifiëren. 

Momenteel worden alleen de Computer Vision-API, Face-API, Text Analytics-API, insluitende lezer, formulier herkenning, afwijkings detectie en alle Bing-services ondersteund, met uitzonde ring van Bing Aangepaste zoekopdrachten ondersteuning voor verificatie met behulp van Azure AD.

- [Aanvragen voor Cognitive Services verifiëren](./authentication.md#authenticate-with-azure-active-directory)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regel matig gebruikers toegang controleren en afstemmen

**Hulp**: Azure Active Directory (Azure AD) biedt logboeken waarmee u verlopen accounts kunt detecteren. Daarnaast kunnen klanten Azure Identity Access revisies gebruiken om groepslid maatschappen, toegang tot bedrijfs toepassingen en roltoewijzingen op efficiënte wijze te beheren. De toegang van de gebruiker kan regel matig worden gecontroleerd om ervoor te zorgen dat alleen actieve gebruikers de toegang blijven houden.

Klant voor het onderhouden van de inventaris van API Management gebruikers accounts, waarbij u indien nodig de toegang afstemt. In API Management zijn ontwikkel aars de gebruikers van de Api's die u beschikbaar maakt met behulp van API Management. Nieuw gemaakte ontwikkelaars accounts zijn standaard actief en zijn gekoppeld aan de groep ontwikkel aars. Ontwikkelaars accounts die zich in een actieve status bevinden, kunnen worden gebruikt voor toegang tot alle Api's waarvoor ze abonnementen hebben.

- [Gebruikersaccounts in Azure API Management beheren](../api-management/api-management-howto-create-or-invite-developers.md)

- [Een lijst met API Management gebruikers ophalen](/powershell/module/az.apimanagement/get-azapimanagementuser?amp;preserve-view=true&view=azps-4.8.0)

- [Beoordelingen over Azure Identity Access gebruiken](../active-directory/governance/access-reviews-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: controle pogingen om toegang te krijgen tot gedeactiveerde referenties

**Richt lijnen**: u hebt toegang tot Azure Active Directory (Azure AD) aanmeldings activiteit, controle en risico logboek bronnen, waarmee u kunt integreren met Azure Sentinel of een Siem van derden.

U kunt dit proces stroom lijnen door Diagnostische instellingen voor Azure AD-gebruikers accounts te maken en de audit logboeken en aanmeldings logboeken te verzenden naar een Log Analytics-werk ruimte. U kunt de gewenste logboek waarschuwingen configureren in Log Analytics.

- [Azure-activiteitenlogboeken integreren in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Azure-Sentinel aan de trein](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: waarschuwing voor de afwijking van het aanmeldings gedrag van het account

**Richt lijnen**: voor de afwijking van het aanmeldings gedrag van accounts op het besturings vlak, gebruikt u de functies voor identiteits beveiliging en risico detectie van Azure Active Directory (Azure AD) voor het configureren van automatische antwoorden op gedetecteerde verdachte acties die betrekking hebben op gebruikers identiteiten. U kunt ook gegevens opnemen in azure Sentinel voor verder onderzoek.

- [Riskante Azure AD-aanmeldingen weergeven](../active-directory/identity-protection/overview-identity-protection.md)

- [Risico beleid voor identiteits beveiliging configureren en inschakelen](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: micro soft biedt toegang tot relevante klant gegevens tijdens ondersteunings scenario's

**Richt lijnen**: niet beschikbaar voor Cognitive Services. Klanten-lockbox wordt nog niet ondersteund voor Cognitive Services.

- [Lijst met door Klanten-lockbox ondersteunde services](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Benchmark: gegevensbescherming](../security/benchmarks/security-control-data-protection.md) voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: een inventaris van gevoelige informatie onderhouden

**Hulp**: Tags gebruiken om Azure-resources te helpen bij het bijhouden of verwerken van gevoelige informatie.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: systemen isoleren die gevoelige informatie opslaan of verwerken

**Richt lijnen**: afzonderlijke abonnementen of beheer groepen implementeren voor ontwikkeling, testen en productie. Resources moeten worden gescheiden door virtuele netwerken of subnetten, op de juiste wijze worden gelabeld en beveiligd door een netwerk beveiligings groep of Azure Firewall. Resources die gevoelige gegevens opslaan of verwerken, moeten voldoende geïsoleerd zijn. Voor Virtual Machines het opslaan of verwerken van gevoelige gegevens, implementeert u beleid en procedures om ze uit te scha kelen wanneer ze niet worden gebruikt.

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Beheer groepen maken](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

- [Een Virtual Network maken](../virtual-network/quick-create-portal.md)

- [Een NSG maken met een beveiligings configuratie](../virtual-network/tutorial-filter-network-traffic.md)

- [Azure Firewall implementeren](../firewall/tutorial-firewall-deploy-portal.md)

- [Waarschuwing of waarschuwing configureren en weigeren met Azure Firewall](../firewall/threat-intel.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: niet-geautoriseerde overdracht van gevoelige gegevens controleren en blok keren

**Richt lijnen**: nog niet beschikbaar. De functies voor gegevens identificatie, classificatie en verlies preventie zijn nog niet beschikbaar voor Cognitive Services.

Micro soft beheert de onderliggende infra structuur voor Cognitive Services en heeft strikte controles geïmplementeerd om verlies of bloot stelling van klant gegevens te voor komen.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: alle gevoelige gegevens in de overdracht versleutelen

**Hulp**: alle Cognitive Services-eind punten worden weer gegeven via http afdwingen TLS 1,2. Met een afgedwongen beveiligings protocol moeten gebruikers die proberen een Cognitive Services eind punt aan te roepen, voldoen aan de volgende richt lijnen:

- Het besturings systeem van de client moet TLS 1,2 ondersteunen.
- De taal (en het platform) die wordt gebruikt voor het maken van de HTTP-aanroep moet TLS 1,2 opgeven als onderdeel van de aanvraag. Afhankelijk van de taal en het platform wordt het opgeven van TLS impliciet of expliciet uitgevoerd.

- [Transport Layer Security voor Azure Cognitive Services begrijpen](cognitive-services-security.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: een actief detectie hulpprogramma gebruiken om gevoelige gegevens te identificeren

**Hulp**: de functies gegevens identificatie, classificatie en verlies preventie zijn nog niet beschikbaar voor Cognitive Services. Label instanties die gevoelige informatie bevatten als zodanig en implementeren van een oplossing van derden, indien nodig voor nalevings doeleinden.

Micro soft beheert het onderliggende platform en behandelt alle inhoud van de klant als vertrouwelijk en gaat naar uitstekende lengtes om het verlies en de bloot stelling van klant gegevens te beschermen. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Azure RBAC gebruiken om de toegang tot resources te beheren 

**Richt lijnen**: gebruik Azure op rollen gebaseerd toegangs beheer (Azure RBAC) voor het beheren van de toegang tot het Cognitive Services besturings vlak (dat wil zeggen Azure Portal). 

- [Azure RBAC configureren](../role-based-access-control/role-assignments-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: gevoelige informatie op rest versleutelen

**Hulp**: versleuteling in rust voor Cognitive Services is afhankelijk van de specifieke service die wordt gebruikt. In de meeste gevallen worden gegevens versleuteld en ontsleuteld met behulp van FIPS 140-2-compatibele 256-bits AES-versleuteling. Versleuteling en ontsleuteling zijn transparant, wat betekent dat versleuteling en toegang wordt beheerd voor de klanten van micro soft. Klant gegevens zijn standaard beveiligd en ze hoeven hun code of toepassingen niet te wijzigen om te kunnen profiteren van versleuteling.

U kunt ook Azure Key Vault gebruiken om uw door de klant beheerde sleutels op te slaan. U kunt uw eigen sleutels maken en deze opslaan in een sleutelkluis of u kunt de Azure Key Vault API's gebruiken om sleutels te genereren.

- [Lijst met services die de informatie in rust versleutelen](./encryption/cognitive-services-encryption-keys-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

**Hulp**: gebruik Azure monitor met het Azure-activiteiten logboek om waarschuwingen te maken wanneer wijzigingen worden aangebracht in productie-exemplaren van Cognitive Services en andere essentiële of gerelateerde resources.

- [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteiten logboek](../azure-monitor/alerts/alerts-activity-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie [Azure Security Bench Mark: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md)voor meer informatie.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatische Asset-detectie oplossing gebruiken

**Hulp**: Azure resource Graph gebruiken om in uw abonnementen alle bronnen (zoals compute, opslag, netwerk, poorten, protocollen enzovoort) op te vragen of te detecteren. Zorg ervoor dat de juiste (Lees-) machtigingen voor uw Tenant en alle Azure-abonnementen en resources in uw abonnementen inventariseren.

Hoewel klassieke Azure-resources kunnen worden gedetecteerd via resource grafiek, is het raadzaam om Azure Resource Manager resources te maken en te gebruiken.

- [Query's maken met Azure resource Graph](../governance/resource-graph/first-query-portal.md)

- [Uw Azure-abonnementen weer geven](/powershell/module/az.accounts/get-azsubscription?amp;preserve-view=true&view=azps-4.8.0)

- [Meer informatie over Azure RBAC](../role-based-access-control/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="62-maintain-asset-metadata"></a>6,2: meta gegevens van activa onderhouden

**Richt lijnen**: Tags Toep assen op Azure-resources die meta gegevens geven om ze logisch in een taxonomie te organiseren.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: niet-geautoriseerde Azure-resources verwijderen

**Richt lijnen**: Gebruik labels, beheer groepen en afzonderlijke abonnementen, waar nodig, om Azure-cache in te delen en bij te houden voor redis-exemplaren en gerelateerde bronnen. Sluit de inventaris regel matig af en zorg ervoor dat niet-geautoriseerde resources tijdig worden verwijderd uit het abonnement.

Gebruik Azure Policy bovendien om beperkingen te leggen voor het type resources dat kan worden gemaakt in klant abonnementen met behulp van de volgende ingebouwde beleids definities:

- Niet toegestane resourcetypen
- Toegestane brontypen

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Beheer groepen maken](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

**Hulp: gebruik** Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in klant abonnementen met behulp van de volgende ingebouwde beleids definities:

- Niet toegestane resourcetypen
- Toegestane brontypen

Daarnaast kunt u met Azure resource Graph bronnen in de abonnementen opvragen of detecteren.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Query's maken met Azure resource Graph](../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="69-use-only-approved-azure-services"></a>6,9: alleen goedgekeurde Azure-Services gebruiken

**Hulp: gebruik** Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in klant abonnementen met behulp van de volgende ingebouwde beleids definities:
- Niet toegestane resourcetypen
- Toegestane brontypen

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een specifiek resource type weigeren met Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

**Hulp** bij het configureren van voorwaardelijke toegang van Azure om gebruikers de mogelijkheid te bieden om te communiceren met Azure Resource Manager door ' blok toegang ' te configureren voor de app Microsoft Azure management.

- [Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager te blok keren](../role-based-access-control/conditional-access-azure-management.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [Azure Security Bench Mark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md)(Engelstalig) voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources

**Richt lijnen**: standaard beveiligings configuraties voor uw Cognitive Services-container definiëren en implementeren met Azure Policy. Gebruik Azure Policy aliassen in de naam ruimte ' micro soft. CognitiveServices ' om aangepaste beleids regels te maken om de configuratie van uw Azure-cache voor redis-exemplaren te controleren of af te dwingen.

- [Beschik bare Azure Policy aliassen weer geven](/powershell/module/az.resources/get-azpolicyalias?amp;preserve-view=true&view=azps-4.8.0)

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: Beveilig Azure-resource configuraties onderhouden

**Hulp**: gebruik Azure Policy [deny] en [indien niet aanwezig] effecten om beveiligde instellingen af te dwingen voor uw Azure-resources.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy effecten begrijpen](../governance/policy/concepts/effects.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan

**Richt lijnen**: als u aangepaste Azure Policy definities of Azure Resource Manager sjablonen gebruikt voor uw Cognitive Services containers en gerelateerde resources, gebruikt u Azure opslag plaatsen om uw code veilig op te slaan en te beheren.

- [Code opslaan in azure DevOps](/azure/devops/repos/git/gitworkflow?amp;preserve-view=true&view=azure-devops)

- [Documentatie voor Azure opslag plaatsen](/azure/devops/repos/?amp;preserve-view=true&view=azure-devops)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: hulpprogram ma's voor configuratie beheer voor Azure-resources implementeren

**Hulp**: gebruik Azure Policy aliassen in de naam ruimte ' micro soft. cache ' om aangepaste beleids regels te maken om systeem configuraties te Signa lering, te controleren en af te dwingen. Ontwikkel bovendien een proces en pijp lijn voor het beheren van beleids uitzonderingen.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-resources implementeren

**Hulp**: gebruik Azure Policy aliassen in de naam ruimte ' micro soft. CognitiveServices ' om aangepaste Azure Policy definities te maken om systeem configuraties te Signa lering, te controleren en af te dwingen. Gebruik Azure Policy [audit], [deny] en [implementeren indien niet aanwezig] effecten om automatisch configuraties voor uw Azure-cache af te dwingen voor redis-exemplaren en gerelateerde resources.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure-geheimen veilig beheren

**Richt lijnen**: voor virtuele Azure-machines of webtoepassingen die worden uitgevoerd op Azure app service wordt gebruikt om toegang te krijgen tot uw COGNITIVE Services-API, gebruikt u Managed Service Identity in combi natie met Azure Key Vault om Cognitive Services sleutel beheer te vereenvoudigen en te beveiligen. Zorg ervoor Key Vault zacht verwijderen is ingeschakeld.

- [Integratie met door Azure beheerde identiteiten](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Een Key Vault maken](../key-vault/secrets/quick-create-portal.md)

- [Verifiëren bij Key Vault](../key-vault/general/authentication.md)

- [Toegangs beleid voor Key Vault toewijzen](../key-vault/general/assign-access-policy-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: identiteiten veilig en automatisch beheren

**Richt lijnen**: voor virtuele Azure-machines of webtoepassingen die worden uitgevoerd op Azure app service wordt gebruikt om toegang te krijgen tot uw COGNITIVE Services-API, gebruikt u Managed Service Identity in combi natie met Azure Key Vault om Cognitive Services sleutel beheer te vereenvoudigen en te beveiligen. Zorg ervoor Key Vault zacht verwijderen is ingeschakeld.

Gebruik beheerde identiteiten om Azure-Services te voorzien van een automatisch beheerde identiteit in Azure Active Directory (Azure AD). Met beheerde identiteiten kunt u zich verifiëren bij elke service die ondersteuning biedt voor Azure AD-verificatie, met inbegrip van Azure Key Vault, zonder enige referenties in uw code.

- [Beheerde identiteiten configureren](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Integratie met door Azure beheerde identiteiten](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: onbedoelde referentie blootstelling elimineren

**Richt lijnen**: referentie scanner implementeren om referenties in code te identificeren. Door het gebruik van Credential Scanner worden gebruikers ook aangemoedigd om gedetecteerde referenties naar veiligere locaties, zoals Azure Key Vault, te verplaatsen.

- [Referentie scanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie voor meer informatie de [Azure Security Bench Mark: beveiliging tegen schadelijke software](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: scan bestanden die moeten worden geüpload naar niet-reken resources van Azure

**Hulp**: micro soft anti-malware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services (bijvoorbeeld Azure cache voor redis), maar wordt niet uitgevoerd op de inhoud van de klant.

Scan vooraf op inhoud die wordt geüpload naar niet-reken resources van Azure, zoals App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL, enzovoort. Micro soft heeft geen toegang tot uw gegevens in deze instanties.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="data-recovery"></a>Gegevensherstel

*Zie [Azure Security Bench Mark: Data Recovery](../security/benchmarks/security-control-data-recovery.md)(Engelstalig) voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zorg voor regel matige automatische back-ups

**Hulp**: de gegevens in uw Microsoft Azure Storage-account worden altijd automatisch gerepliceerd om duurzaamheid en hoge Beschik baarheid te garanderen. Azure Storage kopieert uw gegevens, zodat deze worden beveiligd tegen geplande en niet-geplande gebeurtenissen, waaronder tijdelijke hardwarestoringen, netwerk-of energie storingen en enorme natuur rampen. U kunt ervoor kiezen om uw gegevens te repliceren binnen hetzelfde Data Center, op zonegebonden-data centrums binnen dezelfde regio of in geografisch gescheiden regio's. 

U kunt ook de functie levenscyclus beheer gebruiken om een back-up te maken van gegevens naar de laag van het archief. Daarnaast schakelt u de optie voorlopig verwijderen in voor uw back-ups die zijn opgeslagen in het opslag account.

- [Informatie over Azure Storage redundantie en Service-Level-overeenkomsten](../storage/common/storage-redundancy.md)

- [De levenscyclus van Azure Blob-opslag beheren](../storage/blobs/storage-lifecycle-management-concepts.md)

- [Voorlopig verwijderen voor Azure Storage-blobs](../storage/blobs/soft-delete-blob-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: volledige back-ups van het systeem uitvoeren en een back-up maken van door de klant beheerde sleutels

**Hulp**: gebruik Azure Resource Manager om Cognitive Services en gerelateerde resources te implementeren. Azure Resource Manager biedt de mogelijkheid om sjablonen te exporteren, waarmee u uw oplossing in de levens cyclus van de ontwikkeling kunt implementeren en de betrouw baarheid van uw resources in een consistente staat hebt geïmplementeerd. Gebruik Azure Automation om de API van de Azure Resource Manager-sjabloon regel matig te kunnen aanroepen. Maak een back-up van vooraf gedeelde sleutels in Azure Key Vault.

- [Overzicht van Azure Resource Manager](../azure-resource-manager/management/overview.md)

- [Een Cognitive Services resource maken met behulp van een Azure Resource Manager sjabloon](./create-account-resource-manager-template.md?tabs=portal)

- [Eén en meerdere resources exporteren naar een sjabloon in Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Resource groepen-sjabloon exporteren](/rest/api/resources/resourcegroups/exporttemplate)

- [Inleiding tot Azure Automation](../automation/automation-intro.md)

- [Hoe kan ik een back-up maken van sleutel kluis sleutels in azure?](/powershell/module/az.keyvault/backup-azkeyvaultkey?amp;preserve-view=true&view=azps-4.8.0)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richt lijnen**: Zorg ervoor dat u regel matig de implementatie van Azure Resource Manager sjablonen regel matig uitvoert op een geïsoleerd abonnement, indien nodig. Herstel van back-ups van vooraf gedeelde sleutels testen.

- [Resources implementeren met ARM-sjablonen en Azure Portal](../azure-resource-manager/templates/deploy-portal.md)

- [Sleutel kluis sleutels herstellen in azure](/powershell/module/az.keyvault/restore-azkeyvaultkey?amp;preserve-view=true&view=azps-4.8.0)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zorg voor de bescherming van back-ups en door de klant beheerde sleutels

**Hulp**: Azure DevOps gebruiken om uw Azure Resource Manager sjablonen veilig op te slaan en te beheren. Als u resources wilt beveiligen die u beheert in azure DevOps, kunt u machtigingen verlenen of weigeren aan specifieke gebruikers, ingebouwde beveiligings groepen of groepen die zijn gedefinieerd in Azure Active Directory (Azure AD) als deze zijn geïntegreerd met Azure DevOps, of Active Directory als deze zijn geïntegreerd met Team Foundation Server.

Gebruik Azure op rollen gebaseerd toegangs beheer voor het beveiligen van sleutels die door de klant worden beheerd. Schakel Soft-Delete in en verwijder de beveiliging in Key Vault om sleutels te beschermen tegen onbedoelde of schadelijke verwijdering. 

- [Code opslaan in azure DevOps](/azure/devops/repos/git/gitworkflow?amp;preserve-view=true&view=azure-devops)

- [Over machtigingen en groepen in azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Voorlopig verwijderen voor Azure Storage-blobs](../storage/blobs/soft-delete-blob-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Benchmark: respons op incidenten](../security/benchmarks/security-control-incident-response.md) voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10,1: een hand leiding voor reactie op incidenten maken

**Richtlijnen**: Stel voor uw organisatie een responshandleiding op voor gebruik bij incidenten. Zorg ervoor dat er schriftelijke responsplannen zijn waarin alle rollen van het personeel worden gedefinieerd, evenals alle fasen in het afhandelen/managen van incidenten, vanaf de detectie van het incident tot een evaluatie ervan achteraf.

- [Werk stroom automatisering configureren in Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Richt lijnen voor het bouwen van uw eigen beveiligings incident antwoord proces](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [U kunt ook gebruikmaken van de hand leiding voor de verwerking van het computer beveiligings incident van het NIST om u te helpen bij het maken van uw eigen reactie plan voor incidenten](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

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

**Richt lijnen**: uw Security Center waarschuwingen en aanbevelingen exporteren met de functie continue export. Met doorlopend exporteren kunt u waarschuwingen en aanbevelingen hand matig of op een doorlopende manier exporteren. U kunt de Security Center Data Connector gebruiken om de waarschuwingen naar Azure Sentinel te streamen.

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

- Zie [Overzicht Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Meer informatie over [Azure-beveiligingsbasislijnen](../security/benchmarks/security-baselines-overview.md)