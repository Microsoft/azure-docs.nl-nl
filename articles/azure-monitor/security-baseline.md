---
title: Azure-beveiligings basislijn voor Azure Monitor
description: De Azure Monitor Security Baseline voorziet in procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-Bench Mark.
author: msmbaldwin
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 39dff9b48fb292d8a1c145ae3e592786ddb027a4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102198826"
---
# <a name="azure-security-baseline-for-azure-monitor"></a>Azure-beveiligings basislijn voor Azure Monitor

In deze beveiligings basislijn worden richt lijnen van de [Azure Security Bench Mark-versie 1,0](../security/benchmarks/overview-v1.md) ingesteld op Azure monitor. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen.
De inhoud wordt gegroepeerd op de **beveiligings controles** die zijn gedefinieerd door de Azure Security-benchmark en de bijbehorende richt lijnen die van toepassing zijn op Azure monitor. **Besturings elementen** die niet van toepassing zijn op Azure monitor, zijn uitgesloten. 

 
Als u wilt zien hoe Azure Monitor volledig is toegewezen aan de beveiligings benchmark van Azure, raadpleegt u het [volledige Azure monitor beveiligings basislijn toewijzings bestand](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Benchmark: netwerkbeveiliging](../security/benchmarks/security-control-network-security.md) voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-resources in virtuele netwerken beveiligen

**Hulp** bij het inschakelen van een persoonlijke Azure-koppeling om toegang te verlenen tot Azure SaaS-Services (bijvoorbeeld Azure monitor) en Azure gehoste klanten/partner services via een persoonlijk eind punt in uw virtuele netwerk. Verkeer tussen uw virtuele netwerk en de services wordt via het backbonenetwerk van Microsoft geleid, waarmee de risico's van het openbare internet worden vermeden.

Als u wilt toestaan dat verkeer wordt bereikt Azure Monitor, gebruikt u de AzureMonitor-service tags om inkomend en uitgaand verkeer via netwerk beveiligings groepen toe te staan. Als u wilt toestaan dat test verkeer voor beschikbaarheids bewaking Azure Monitor bereikt, gebruikt u de ApplicationInsightsAvailability-service code voor al het binnenkomende verkeer via netwerk beveiligings groepen. Als u waarschuwings meldingen wilt toestaan om klant eindpunten te bereiken, gebruikt u de servicetag ' ActionGroup ' om binnenkomend verkeer via netwerk beveiligings groepen toe te staan.

Met regels voor virtuele netwerken kunnen Azure Monitor alleen communicatie accepteren die worden verzonden vanuit geselecteerde subnetten binnen een virtueel netwerk.

Gebruik Log Analytics gateway om gegevens te verzenden naar een Log Analytics-werk ruimte in Azure Monitor namens de computers die niet rechtstreeks verbinding kunnen maken met internet om te voor komen dat computers verbinding met internet hebben. 

- [Privé-koppeling instellen voor Azure Monitor](/azure/azure-monitor/platform/private-link-security)

- [Computers zonder Internet toegang verbinden met behulp van de Log Analytics-gateway in Azure Monitor](/azure/azure-monitor/platform/gateway)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: de configuratie en het verkeer van virtuele netwerken, subnetten en netwerk interfaces bewaken en vastleggen

**Richt lijnen**: Azure monitor is een kern service en biedt geen ondersteuning voor het rechtstreeks in een virtueel netwerk implementeren van de onderliggende infra structuur van micro soft. Voor bronnen die netwerk verbindingen met het Azure Monitor-aanbod doen, moet u echter hun netwerk beveiligen met een netwerk beveiligings groep. Schakel logboeken stroom voor netwerk beveiligings groepen in en verzend logboeken naar een opslag account voor verkeers controle. U kunt ook stroom logboeken naar een Log Analytics-werk ruimte verzenden en Traffic Analytics gebruiken om inzicht te krijgen in de verkeers stroom in uw Azure-Cloud. Enkele voor delen van Traffic Analytics zijn de mogelijkheid om netwerk activiteiten te visualiseren en HOTS pots te identificeren, beveiligings dreigingen te identificeren, verkeers patronen te begrijpen en netwerk configuraties te lokaliseren.

Wanneer u Azure Monitor met een persoonlijke koppeling gebruikt, krijgt u toegang tot netwerk logboek registratie, zoals ' gegevens verwerkt door het persoonlijke eind punt (IN/uit) '.

- [Netwerk vereisten voor Azure Monitor agents](/azure/azure-monitor/platform/log-analytics-agent#network-requirements)

- [Computers zonder Internet toegang verbinden met behulp van de Log Analytics-gateway in Azure Monitor](/azure/azure-monitor/platform/gateway)

- [Stroom logboeken van netwerk beveiligings groepen inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Traffic Analytics inschakelen en gebruiken](../network-watcher/traffic-analytics.md)

- [Informatie over de netwerk beveiliging die wordt verschaft door Azure Security Center](../security-center/security-center-network-recommendations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: de complexiteit en administratieve overhead van netwerk beveiligings regels minimaliseren

**Hulp**: als u wilt toestaan dat verkeer wordt bereikt Azure monitor, gebruikt u de AzureMonitor-service tags om inkomend en uitgaand verkeer via netwerk beveiligings groepen toe te staan. Als u wilt toestaan dat test verkeer voor beschikbaarheids bewaking Azure Monitor bereikt, gebruikt u de ApplicationInsightsAvailability-service code voor al het binnenkomende verkeer via netwerk beveiligings groepen. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen.

- [Service Tags begrijpen en gebruiken](../virtual-network/service-tags-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="110-document-traffic-configuration-rules"></a>1,10: configuratie regels voor het document verkeer

**Hulp**: Azure monitor maakt deel uit van de Azure Core-Services en kan niet afzonderlijk als een service worden geïmplementeerd. Azure Monitor onderdelen, met inbegrip van de Azure Monitor-agent, en Application Insights SDK kunnen worden geïmplementeerd met uw resources, en dit kan van invloed zijn op de beveiligings postuur van die bronnen.

- [Netwerk vereisten voor Azure Monitor agents](/azure/azure-monitor/platform/log-analytics-agent#network-requirements)

- [Computers zonder Internet toegang verbinden met behulp van de Log Analytics-gateway in Azure Monitor](/azure/azure-monitor/platform/gateway) 

- [Zie aan de slag met Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview#get-started)

- [Beschik baarheid webtests instellen](app/monitor-web-app-availability.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische hulpprogram ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

**Hulp**: gebruik het Azure-activiteiten logboek om resource configuraties te bewaken en wijzigingen in uw netwerk bronnen te detecteren die zijn gerelateerd aan Azure monitor. Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer wijzigingen aan die kritieke netwerk bronnen plaatsvinden.

- [Activiteiten logboek gebeurtenissen van Azure weer geven en ophalen](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Waarschuwingen maken in Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie [Azure Security Bench Mark: Logging and monitoring](../security/benchmarks/security-control-logging-monitoring.md)(Engelstalig) voor meer informatie.*

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

**Hulp**: Azure monitor activiteiten logboeken gebruikt om wijzigingen in de resources te registreren. U kunt deze logboeken exporteren naar Azure Storage, Event hub of een Log Analytics-werk ruimte. Opname logboeken via Azure Monitor voor het verzamelen van beveiligings gegevens die zijn gegenereerd door eindpunt apparaten, netwerk bronnen en andere beveiligings systemen. In Azure Monitor kunt u met behulp van de gegevens query's en analyses uitvoeren, Azure Storage accounts gebruiken voor een lange termijn/archief opslag van Logboeken.

U kunt ook gegevens in-of uitschakelen voor Azure Sentinel of een SIEM van derden.

- [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](/azure/azure-monitor/platform/diagnostic-settings)

- [Interne host-logboeken van de virtuele machine van Azure verzamelen met Azure Monitor](/azure/azure-monitor/learn/quick-collect-azurevm)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

- [Aan de slag met Azure Monitor en integratie van SIEM van derden](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: de [Security Bench Mark van Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) is het standaard beleids initiatief voor Security Center en is de basis voor de [aanbevelingen van Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). De Azure Policy definities die aan dit besturings element zijn gerelateerd, worden automatisch door Security Center ingeschakeld. Voor waarschuwingen met betrekking tot dit besturings element is mogelijk een [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -plan vereist voor de gerelateerde services.

**Ingebouwde definities Azure Policy-micro soft. Insights**:

[!INCLUDE [Resource Policy for microsoft.insights 2.2](../../includes/policy/standards/asb/rp-controls/microsoft.insights-2-2.md)]

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources

**Hulp**: Azure monitor activiteiten logboeken gebruikt, wordt het activiteiten logboek automatisch ingeschakeld en worden de bewerkingen geregistreerd die op Azure monitor resources zijn uitgevoerd, zoals: wie de bewerking heeft gestart, wanneer de bewerking is uitgevoerd, de status van de bewerking en andere nuttige controle-informatie. 

- [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](/azure/azure-monitor/platform/diagnostic-settings)

- [Logboek registratie en verschillende logboek typen in azure](/azure/azure-monitor/platform/platform-logs-overview)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: de [Security Bench Mark van Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) is het standaard beleids initiatief voor Security Center en is de basis voor de [aanbevelingen van Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). De Azure Policy definities die aan dit besturings element zijn gerelateerd, worden automatisch door Security Center ingeschakeld. Voor waarschuwingen met betrekking tot dit besturings element is mogelijk een [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -plan vereist voor de gerelateerde services.

**Ingebouwde definities Azure Policy-micro soft. Insights**:

[!INCLUDE [Resource Policy for microsoft.insights 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.insights-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren

**Richt lijnen**: stel in azure monitor uw Bewaar periode voor log Analytics werkruimte in volgens de nalevings voorschriften van uw organisatie. Gebruik Azure Storage accounts voor een lange termijn/archief opslag van uw logboeken.

- [De Bewaar periode voor gegevens wijzigen in Log Analytics](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

- [Bewaar beleid configureren voor logboeken van Azure Storage-account](/azure/storage/common/storage-monitor-storage-account#configure-logging)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren

**Richt lijnen**: Logboeken analyseren en controleren op afwijkend gedrag en regel matig de resultaten bekijken. Gebruik Azure Monitor en een Log Analytics werk ruimte om logboeken te controleren en query's uit te voeren op logboek gegevens.

U kunt ook gegevens in-en inschakelen voor Azure Sentinel of een SIEM van derden.

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

- [Aan de slag met Log Analytics query's](/azure/azure-monitor/log-query/log-analytics-tutorial)

- [Aangepaste query's uitvoeren in Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteiten

**Hulp**: gebruik Azure Security Center met log Analytics werk ruimte voor bewaking en waarschuwingen over afwijkende activiteiten die in beveiligings logboeken en gebeurtenissen zijn gevonden. U kunt ook gegevens naar Azure-Sentinel inschakelen en op het bord zetten.

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

- [Waarschuwingen beheren in Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Een waarschuwing over logboek gegevens van log Analytics](/azure/azure-monitor/learn/tutorial-response)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [Azure Security Bench Mark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: een inventaris van beheerders accounts onderhouden

**Richt lijnen**: met Azure op rollen gebaseerd toegangs beheer (Azure RBAC) kunt u de toegang tot Azure-resources beheren via roltoewijzingen. U kunt deze rollen toewijzen aan gebruikers, groeperingen van service-principals en beheerde identiteiten. Er bestaan vooraf gedefinieerde, ingebouwde rollen voor bepaalde resources, en deze rollen kunnen worden geïnventariseerd of opgevraagd via tools zoals Azure CLI, Azure PowerShell en Azure Portal.

- [Een directory-rol verkrijgen in Azure Active Directory (Azure AD) met Power shell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Leden van een directory-rol in azure AD ophalen met Power shell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: speciale beheerders accounts gebruiken

**Richt lijnen**: Maak standaard procedures voor het gebruik van specifieke beheerders accounts. Gebruik Azure Security Center identiteits-en toegangs beheer om het aantal beheerders accounts te bewaken.

U kunt ook Just-in-time/alleen-voldoende toegang inschakelen met behulp van Azure Active Directory (Azure AD) Privileged Identity Management geprivilegieerde rollen voor micro soft-Services en Azure Resource Manager.

- [Overzicht van Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: gebruik Azure Active Directory eenmalige aanmelding (SSO)

**Richt lijnen**: gebruik waar mogelijk Azure Active Directory (Azure AD) SSO in plaats van afzonderlijke zelfstandige referenties per service te configureren. Gebruik Azure Security Center aanbevelingen voor identiteits-en toegangs beheer.

- [Informatie over eenmalige aanmelding met Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: multi-factor Authentication gebruiken voor alle op Azure Active Directory gebaseerde toegang

**Hulp** bij het inschakelen van Azure Active Directory (Azure AD) multi-factor Authentication en het volgen van Azure Security Center identiteits-en toegangs aanbevelingen.

- [Multi-factor Authentication inschakelen in azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identiteit en toegang bewaken in Azure Security Center](../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: gebruik speciale machines (privileged Access workstations) voor alle beheer taken

**Hulp**: gebruik een beveiligd, door Azure beheerd werk station (ook wel een privileged Access Workstation of paw) voor beheer taken waarvoor verhoogde bevoegdheden zijn vereist.

- [Meer informatie over veilige, door Azure beheerde werk stations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure Active Directory (Azure AD) multi-factor Authentication inschakelen](../active-directory/authentication/howto-mfa-getstarted.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logboeken en waarschuwingen voor verdachte activiteiten van beheerders accounts

**Hulp**: gebruik Azure Active Directory (Azure AD) beveiligings rapporten en-bewaking om te detecteren wanneer verdachte of onveilige activiteiten in de omgeving worden uitgevoerd. Gebruik Azure Security Center om identiteits-en toegangs activiteiten te bewaken.

- [Azure AD-gebruikers identificeren die zijn gemarkeerd voor riskante activiteiten](../active-directory/identity-protection/overview-identity-protection.md)

- [Identiteits- en toegangsactiviteiten van gebruikers controleren in Azure Security Center](../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Hulp**: gebruik benoemde locaties voor voorwaardelijke toegang om alleen toegang toe te staan vanaf specifieke logische groepen met IP-adresbereiken of landen/regio's.

- [Benoemde locaties configureren in azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory gebruiken

**Hulp**: gebruik Azure Active Directory (Azure AD) als centrale verificatie-en autorisatie systeem. Azure AD beveiligt gegevens door gebruik te maken van sterke versleuteling voor gegevens in rust en onderweg. Azure AD bevat ook zouten, hashes en veilige gebruikers referenties.

- [Een Azure AD-instantie maken en configureren](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regel matig gebruikers toegang controleren en afstemmen

**Hulp**: Azure Active Directory (Azure AD) biedt logboeken waarmee u verlopen accounts kunt detecteren. Daarnaast kunt u Azure Identity Access revisies gebruiken om groepslid maatschappen en de toegang tot bedrijfs toepassingen en roltoewijzingen op efficiënte wijze te beheren. Gebruikers toegang kan regel matig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben.

- [Meer informatie over Azure AD-rapportage](/azure/active-directory/reports-monitoring/)

- [Beoordelingen over Azure Identity Access gebruiken](../active-directory/governance/access-reviews-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: controle pogingen om toegang te krijgen tot gedeactiveerde referenties

**Hulp**: u hebt toegang tot Azure Active Directory (Azure AD) aanmeldings activiteit, controle en risico gebeurtenis logboek bronnen, waarmee u kunt integreren met elk Siem/bewakings programma. U kunt dit proces stroom lijnen door Diagnostische instellingen voor Azure AD-gebruikers accounts te maken en de audit logboeken en aanmeldings logboeken te verzenden naar een Log Analytics-werk ruimte. U kunt de gewenste waarschuwingen configureren in Log Analytics werk ruimte.

- [Azure-activiteitenlogboeken integreren in Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: waarschuwing voor de afwijking van het aanmeldings gedrag van het account

**Hulp**: gebruik Azure Active Directory (Azure AD) risico-en identiteits beveiligings functies voor het configureren van automatische antwoorden op gedetecteerde verdachte acties die betrekking hebben op gebruikers identiteiten. U kunt ook gegevens opnemen in azure Sentinel voor verder onderzoek.

- [Riskante Azure AD-aanmeldingen weergeven](../active-directory/identity-protection/overview-identity-protection.md)

- [Risico beleid voor identiteits beveiliging configureren en inschakelen](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Benchmark: gegevensbescherming](../security/benchmarks/security-control-data-protection.md) voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: een inventaris van gevoelige informatie onderhouden

**Richt lijnen**: gebruik Tags waar mogelijk om te helpen bij het volgen van Azure monitor resources die gevoelige informatie opslaan of verwerken, zoals u log Analytics werk ruimten.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

- [Toegang tot logboekgegevens en werkruimten beheren in Azure Monitor](/azure/azure-monitor/platform/manage-access)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: systemen isoleren die gevoelige informatie opslaan of verwerken

**Richt lijnen**: isolatie implementeren met afzonderlijke abonnementen en beheer groepen voor afzonderlijke beveiligings domeinen, zoals omgevings type en gegevens gevoeligheids niveau. U kunt het toegangs niveau voor uw Azure Monitor en gerelateerde resources beperken die worden vereist door uw toepassingen en bedrijfs omgevingen. U kunt de toegang tot Azure Monitor beheren via toegangs beheer op basis van rollen (Azure RBAC).

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Beheer groepen maken](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: alle gevoelige gegevens in de overdracht versleutelen

**Hulp**: Azure monitor onderhandelt standaard TLS 1,2. Zorg ervoor dat alle clients die verbinding maken met uw Azure-resources, TLS 1,2 of hoger kunnen onderhandelen. 

Application Insights en Log Analytics beide blijven toestaan dat TLS 1,1-en TLS 1,0-gegevens worden opgenomen. Gegevens kunnen worden beperkt tot TLS 1,2 door te configureren aan de client zijde.

- [Veilig verzenden van gegevens met behulp van TLS 1,2](/azure/azure-monitor/platform/data-security#sending-data-securely-using-tls-12)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: een actief detectie hulpprogramma gebruiken om gevoelige gegevens te identificeren

**Hulp**: de functies gegevens identificatie, classificatie en verlies preventie zijn nog niet beschikbaar voor Azure monitor. Implementeer oplossingen van derden, indien nodig voor nalevings doeleinden.
Voor het onderliggende platform dat door micro soft wordt beheerd, behandelt micro soft alle inhoud van de klant als gevoelig en gaat u naar een fantastische lengte om te beschermen tegen verlies en bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: op rollen gebaseerd toegangs beheer gebruiken voor het beheren van de toegang tot bronnen

**Hulp**: gebruik Azure op rollen gebaseerd toegangs beheer (RBAC) voor het beheren van de toegang tot Azure monitor.

- [Rollen, machtigingen en beveiliging in Azure Monitor](/azure/azure-monitor/platform/roles-permissions-security)

- [Azure RBAC configureren](../role-based-access-control/role-assignments-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: gevoelige informatie op rest versleutelen

**Hulp**: Azure monitor zorgt ervoor dat alle gegevens en opgeslagen query's op rest worden versleuteld met behulp van door micro soft beheerde sleutels (MMK). Azure Monitor biedt ook een optie voor versleuteling met behulp van uw eigen sleutel die is opgeslagen in uw Azure Key Vault en die toegankelijk is voor opslag met door het systeem toegewezen beheerde identiteits verificatie. Deze door de klant beheerde sleutel (CMK) kan software of hardware-HSM zijn beveiligd.

- [Azure Monitor door de klant beheerde sleutels](/azure/azure-monitor/platform/customer-managed-keys)

- [Log Analytics gegevens beveiliging](/azure/azure-monitor/platform/data-security)

- [Verzameling, retentie en opslag van gegevens in Application Insights](app/data-retention-privacy.md)

- [Meer informatie over versleuteling van data-at-rest in Azure](../security/fundamentals/encryption-atrest.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

**Hulp**: gebruik Azure monitor met het Azure-activiteiten logboek om waarschuwingen te maken voor wanneer wijzigingen in azure monitor en gerelateerde resources plaatsvinden.

- [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteiten logboek](/azure/azure-monitor/platform/alerts-activity-log)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: de [Security Bench Mark van Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) is het standaard beleids initiatief voor Security Center en is de basis voor de [aanbevelingen van Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). De Azure Policy definities die aan dit besturings element zijn gerelateerd, worden automatisch door Security Center ingeschakeld. Voor waarschuwingen met betrekking tot dit besturings element is mogelijk een [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -plan vereist voor de gerelateerde services.

**Ingebouwde definities Azure Policy-micro soft. Insights**:

[!INCLUDE [Resource Policy for microsoft.insights 4.9](../../includes/policy/standards/asb/rp-controls/microsoft.insights-4-9.md)]

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie voor meer informatie de [Azure Security Bench Mark: beveiligingslek beheer](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: een risico classificatie proces gebruiken om prioriteit te geven aan het herstel van ontdekte beveiligings problemen

**Richt lijnen**: gebruik een gemeen schappelijke risico Score programma (bijvoorbeeld gemeen schappelijke restrictie systeem voor beveiligings problemen) of de standaard risico classificaties die worden meegeleverd met het hulp programma van derden.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie [Azure Security Bench Mark: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md)voor meer informatie.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatische Asset-detectie oplossing gebruiken

**Hulp**: gebruik Azure CLI om Azure monitor-resources in uw abonnementen te doorzoeken en te detecteren. Zorg ervoor dat de juiste (Lees-) machtigingen voor uw Tenant en alle Azure-abonnementen en resources in uw abonnementen inventariseren.

- [Azure Monitor CLI](https://docs.microsoft.com/cli/azure/monitor)

- [Uw Azure-abonnementen weer geven](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&preserve-view=true)

- [Meer informatie over Azure RBAC](../role-based-access-control/overview.md)

- [Rollen, machtigingen en beveiliging in Azure Monitor](/azure/azure-monitor/platform/roles-permissions-security)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="62-maintain-asset-metadata"></a>6,2: meta gegevens van activa onderhouden

**Hulp**: Labels Toep assen op Azure monitor resources die meta gegevens geven om ze logisch in een taxonomie te organiseren.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: niet-geautoriseerde Azure-resources verwijderen

**Richt lijnen**: Gebruik labels, beheer groepen en afzonderlijke abonnementen, waar nodig, om Azure monitor gerelateerde resources te organiseren en bij te houden. Sluit de inventaris regel matig af en zorg ervoor dat niet-geautoriseerde resources tijdig worden verwijderd uit het abonnement.

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Beheer groepen maken](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: de inventaris van goedgekeurde Azure-resources definiëren en onderhouden

**Richt lijnen**: Maak een inventaris van goedgekeurde Azure-resources en goedgekeurde software voor reken resources conform uw organisatie behoeften.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

**Hulp**: gebruik Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in uw abonnementen.

Gebruik Azure Resource Graph om resources binnen hun abonnementen op te vragen en te detecteren.  Zorg ervoor dat alle Azure-resources die aanwezig zijn in de omgeving, zijn goedgekeurd.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Query's maken met Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: niet-goedgekeurde Azure-resources en software toepassingen verwijderen

**Hulp**: regel matig een afstemming van de inventaris en zorg ervoor dat niet-geautoriseerde Azure monitor gerelateerde resources op tijd worden verwijderd uit het abonnement.  

- [Azure Log Analytics-werk ruimte verwijderen](/azure/azure-monitor/platform/delete-workspace)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="69-use-only-approved-azure-services"></a>6,9: alleen goedgekeurde Azure-Services gebruiken

**Hulp**: gebruik Azure Policy om te beperken welke Azure monitor gerelateerde resources u in uw omgeving kunt inrichten.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een specifiek resource type weigeren met Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

**Hulp**: gebruik de voorwaardelijke toegang van Azure om de mogelijkheden van gebruikers om te communiceren met Azure Resources Manager te beperken door ' toegang blok keren ' te configureren voor de app Microsoft Azure management.

- [Voorwaardelijke toegang configureren om de toegang tot Azure-bronnen beheer te blok keren](../role-based-access-control/conditional-access-azure-management.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [Azure Security Bench Mark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md)(Engelstalig) voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources

**Hulp**: aangepaste Azure Policy definities gebruiken om de configuratie van uw Azure monitor gerelateerde resources te controleren of af te dwingen. U kunt ook ingebouwde Azure Policy definities gebruiken als beschikbaar.

Azure Resource Manager heeft ook de mogelijkheid om de sjabloon in JavaScript Object Notation (JSON) te exporteren, die moet worden gecontroleerd om ervoor te zorgen dat de configuraties voldoen aan de beveiligings vereisten voor uw organisatie of deze overschrijden.

U kunt ook aanbevelingen van Azure Security Center gebruiken als een veilige configuratie basislijn voor uw Azure-resources.

Als u gebruikmaakt van de APM-mogelijkheden van live streamen, moet u naast de instrumentatie sleutel het kanaal beveiligen met een geheime API-sleutel.

- [Beveiliging APM Live Metrics Stream](https://docs.microsoft.com/azure/azure-monitor/app/live-stream#secure-the-control-channel)

- [Beschik bare Azure Policy aliassen weer geven](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Zelfstudie: Beleidsregels voor het afdwingen van naleving maken en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Eén en meerdere resources exporteren naar een sjabloon in Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Aanbevelingen voor beveiliging: een naslaggids](../security-center/recommendations-reference.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: Beveilig Azure-resource configuraties onderhouden

**Richt lijnen**: gebruik Azure Policy [deny] en [implementeren indien niet aanwezig] voor het afdwingen van beveiligde instellingen voor uw Azure monitor gerelateerde resources.  Daarnaast kunt u Azure Resource Manager sjablonen gebruiken om de beveiligings configuratie van uw Azure Monitor gerelateerde resources te onderhouden die uw organisatie nodig heeft.

- [Azure Policy effecten begrijpen](../governance/policy/concepts/effects.md)

- [Beleidsregels voor het afdwingen van naleving maken en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Overzicht van Azure Resource Manager sjablonen](../azure-resource-manager/templates/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan

**Hulp**: Azure DevOps gebruiken om uw code veilig op te slaan en te beheren, zoals aangepaste Azure-beleids regels en Azure Resource Manager sjablonen. Als u toegang wilt krijgen tot de resources die u beheert in azure DevOps, kunt u machtigingen verlenen of weigeren aan specifieke gebruikers, ingebouwde beveiligings groepen of groepen die zijn gedefinieerd in Azure Active Directory (Azure AD) als deze zijn geïntegreerd met Azure DevOps, of Active Directory als dit is geïntegreerd met TFS.

- [Code opslaan in azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Over machtigingen en groepen in azure DevOps](/azure/devops/organizations/security/about-permissions)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: hulpprogram ma's voor configuratie beheer voor Azure-resources implementeren

**Richt lijnen**: standaard beveiligings configuraties voor Azure monitor gerelateerde resources definiëren en implementeren met behulp van Azure Policy. Gebruik aangepaste Azure Policy definities om de beveiligings configuratie van uw Azure Monitor gerelateerde resources te controleren of af te dwingen. U kunt ook gebruikmaken van ingebouwde beleids definities die betrekking hebben op uw specifieke resources.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy aliassen](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-resources implementeren

**Hulp**: gebruik Azure Security Center om basislijn scans uit te voeren voor uw Azure monitor gerelateerde resources.  Gebruik Azure Policy bovendien om een waarschuwing te krijgen en Azure-resource configuraties te controleren.

- [Aanbevelingen herstellen in Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure-geheimen veilig beheren

**Hulp**: gebruik Managed Service Identity in combi natie met Azure Key Vault om geheim beheer te vereenvoudigen en beveiligen voor ondersteunde Azure monitor-gerelateerde bronnen.

- [Integratie met door Azure beheerde identiteiten](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Services die beheerde identiteiten voor Azure-resources ondersteunen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Een Key Vault maken](../key-vault/secrets/quick-create-portal.md)

- [Key Vault verificatie bieden met een beheerde identiteit](/azure/key-vault/general/assign-access=policy-portal)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: identiteiten veilig en automatisch beheren

**Richt lijnen**: gebruik beheerde identiteiten om Azure-Services te voorzien van een automatisch beheerde identiteit in azure Active Directory (Azure AD). Met beheerde identiteiten kunt u zich verifiëren bij elke service die ondersteuning biedt voor Azure AD-verificatie, met inbegrip van Azure Monitor resources, zonder enige referenties in uw code.

- [Beheerde identiteiten voor Azure-resources configureren](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

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

**Hulp**: micro soft anti-malware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services (bijvoorbeeld Azure monitor gerelateerde resources), maar wordt niet uitgevoerd voor uw inhoud. 

Scan vooraf bestanden die worden geüpload naar toepasselijke Azure Monitor gerelateerde resources, zoals Log Analytics werk ruimte.

Gebruik Azure Security Center bedreigings detectie voor gegevens Services om malware te detecteren die is geüpload naar opslag accounts. 

- [Meer informatie over micro soft anti-malware voor Azure Cloud Services en Virtual Machines](../security/fundamentals/antimalware.md)

- [Meer informatie over de detectie van bedreigingen van Azure Security Center voor gegevens Services](../security-center/azure-defender.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="data-recovery"></a>Gegevensherstel

*Zie [Azure Security Bench Mark: Data Recovery](../security/benchmarks/security-control-data-recovery.md)(Engelstalig) voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zorg voor regel matige automatische back-ups

**Hulp**: gebruik Azure Resource Manager om de Azure monitor en gerelateerde resources te exporteren in een JavaScript object NOTATION (JSON)-sjabloon die kan worden gebruikt als back-up voor Azure monitor en gerelateerde configuraties.  Gebruik Azure Automation om de back-upscripts automatisch uit te voeren. 

- [Log Analytics-werk ruimte beheren met Azure Resource Manager sjablonen](/azure/azure-monitor/samples/resource-manager-workspace)

- [Eén en meerdere resources exporteren naar een sjabloon in Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Over Azure Automation](../automation/automation-intro.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: volledige back-ups van het systeem uitvoeren en een back-up maken van door de klant beheerde sleutels

**Hulp**: gebruik Azure Resource Manager om de Azure monitor en gerelateerde resources te exporteren in een JavaScript object NOTATION (JSON)-sjabloon die kan worden gebruikt als back-up voor Azure monitor en gerelateerde configuraties. Back-ups van door de klant beheerde sleutels binnen Azure Key Vault als Azure Monitor gerelateerde bronnen gebruikmaken van door de klant beheerde sleutels,

- [Log Analytics-werk ruimte beheren met Azure Resource Manager sjablonen](/azure/azure-monitor/platform/template-workspace-configuration)

- [Eén en meerdere resources exporteren naar een sjabloon in Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Hoe kan ik een back-up maken van sleutel kluis sleutels in azure?](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richt lijnen**: Zorg ervoor dat u regel matig herstel kunt uitvoeren met Azure Resource Manager back-upsjabloon bestanden. Het herstellen van een back-up van door de klant beheerde sleutels testen.

- [Log Analytics-werk ruimte beheren met Azure Resource Manager sjablonen](/azure/azure-monitor/samples/resource-manager-workspace)

- [Sleutel kluis sleutels herstellen in azure](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zorg voor de bescherming van back-ups en door de klant beheerde sleutels

**Hulp**: Azure DevOps gebruiken om uw code veilig op te slaan en te beheren, zoals aangepaste Azure-beleids regels, Azure Resource Manager sjablonen. Als u resources wilt beveiligen die u beheert in azure DevOps, kunt u machtigingen verlenen of weigeren aan specifieke gebruikers, ingebouwde beveiligings groepen of groepen die zijn gedefinieerd in Azure Active Directory (Azure AD) als deze zijn geïntegreerd met Azure DevOps, of Active Directory als dit is geïntegreerd met TFS.  Gebruik Azure RBAC (op rollen gebaseerd toegangs beheer) voor de beveiliging van door de klant beheerde sleutels.

Daarnaast schakelt u Soft-Delete in Key Vault in om de beveiliging van sleutels te beschermen tegen onbedoelde of schadelijke verwijdering. Als Azure Storage wordt gebruikt voor het opslaan van back-ups van Azure Resource Manager-sjablonen, schakelt u de optie zacht verwijderen in om uw gegevens op te slaan en te herstellen wanneer blobs of BLOB-moment opnamen worden verwijderd.

- [Code opslaan in azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Over machtigingen en groepen in azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Soft-Delete inschakelen en beveiliging opschonen in Key Vault](../storage/blobs/soft-delete-blob-overview.md)

- [Voorlopig verwijderen voor Azure Storage-blobs](../storage/blobs/soft-delete-blob-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Benchmark: respons op incidenten](../security/benchmarks/security-control-incident-response.md) voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10,1: een hand leiding voor reactie op incidenten maken

**Richtlijnen**: Stel voor uw organisatie een responshandleiding op voor gebruik bij incidenten. Zorg ervoor dat er schriftelijke responsplannen zijn waarin alle rollen van het personeel worden gedefinieerd, evenals alle fasen in het afhandelen/managen van incidenten, vanaf de detectie van het incident tot een evaluatie ervan achteraf.

- [Richt lijnen voor het bouwen van uw eigen beveiligings incident antwoord proces](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Micro soft Security Response Center anatomie van een incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [De verwerkings handleiding voor het computer beveiligings incident van het NIST gebruiken om u te helpen bij het maken van uw eigen reactie plan voor incidenten](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: een beoordelings procedure voor incidenten en prioriteits procedures maken

**Hulp**: Security Center wijst aan elke waarschuwing een Ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of de analyse die wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau waarvan er sprake is van schadelijke opzet achter de activiteit die tot de waarschuwing heeft geleid.

Daarnaast kunt u ook duidelijk abonnementen markeren (voor bijvoorbeeld productie, niet-productie) met behulp van tags en maak een naamgevings systeem om Azure-resources duidelijk te identificeren en te categoriseren, met name voor de verwerking van gevoelige gegevens.  Het is uw verantwoordelijkheid om prioriteit te geven aan het oplossen van waarschuwingen op basis van de ernst van de Azure-resources en -omgeving waarin het incident heeft plaatsgevonden.

- [Beveiligingswaarschuwingen in Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="103-test-security-response-procedures"></a>10,3: procedures voor beveiligings antwoorden testen

**Richt lijnen**: oefent oefeningen uit om de respons mogelijkheden van uw systeem te testen op een reguliere uitgebracht om uw Azure-resources te beschermen. Stel vast waar zich zwakke plekken en hiaten bevinden, en wijzig zo nodig het plan.

- [Publicatie van het NIST-hand leiding voor test-, trainings-en oefen Programma's voor IT-plannen en-mogelijkheden](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen configureren voor beveiligings incidenten

**Hulp**: contact gegevens van beveiligings incidenten worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat uw gegevens zijn geopend door een onrecht matige of niet-gemachtigde partij. Bekijk incidenten na het feit om te controleren of de problemen zijn opgelost.

- [De Azure Security Center Security-contact persoon instellen](../security-center/security-center-provide-security-contact-details.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: beveiligings waarschuwingen opnemen in uw reactie systeem van uw incident

**Hulp**: exporteer uw Azure Security Center waarschuwingen en aanbevelingen met behulp van de functie continue export om Risico's voor Azure-resources te identificeren. Met doorlopend exporteren kunt u waarschuwingen en aanbevelingen hand matig of op een doorlopende manier exporteren. U kunt de Azure Security Center Data Connector gebruiken om de waarschuwingen naar Azure Sentinel te streamen. 

- [Continue export configureren](../security-center/continuous-export.md) 

- [Waarschuwingen streamen naar Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: de reactie op beveiligings waarschuwingen automatiseren

**Hulp**: gebruik de functie werk stroom automatisering in azure Security Center om automatisch reacties te activeren via ' Logic apps ' in beveiligings waarschuwingen en aanbevelingen voor het beveiligen van uw Azure-resources.

- [Werk stroom automatisering en Logic Apps configureren](../security-center/workflow-automation.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie [Azure Security Bench Mark: Indringings tests en rode team oefeningen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)voor meer informatie.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: voert regel matig indringings tests van uw Azure-resources uit en zorgt voor herbemiddeling van alle essentiële beveiligings resultaten

**Richt lijnen**: Volg de micro soft-regels om ervoor te zorgen dat de indringings tests niet worden geschonden door het micro soft-beleid. Gebruik de strategie van micro soft en de uitvoering van de implementatie van de indringing van een live site in de Cloud, services en toepassingen die door micro soft worden beheerd.

- [Regels voor het inzetten van penetratietests](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

## <a name="next-steps"></a>Volgende stappen

- Zie [Overzicht Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Meer informatie over [Azure-beveiligingsbasislijnen](/azure/security/benchmarks/security-baselines-overview)
