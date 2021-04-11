---
title: Azure-beveiligings basislijn voor Azure-Sentinel
description: De Azure Sentinel Security-basis lijn biedt procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-Bench Mark.
author: msmbaldwin
ms.service: azure-sentinel
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: a827268444fdbc2cdbbe5ac1220ad7d49a2a254f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104604233"
---
# <a name="azure-security-baseline-for-azure-sentinel"></a>Azure-beveiligings basislijn voor Azure-Sentinel

In deze beveiligings basislijn worden richt lijnen van de [Azure Security Bench Mark-versie 1,0](../security/benchmarks/overview-v1.md) naar Azure Sentinel toegepast. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen.
De inhoud wordt gegroepeerd op de **beveiligings controles** die zijn gedefinieerd door de Azure Security-Bench Mark en de bijbehorende richt lijnen die van toepassing zijn op Azure Sentinel. **Besturings elementen** die niet van toepassing zijn op de Azure-Sentinel, zijn uitgesloten.

 
Zie het [volledige Azure Sentinel Security Baseline-toewijzings bestand](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)voor meer informatie over hoe Azure Sentinel volledig is toegewezen aan de Azure Security-Bench Mark.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Benchmark: netwerkbeveiliging](../security/benchmarks/security-control-network-security.md) voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-resources in virtuele netwerken beveiligen

**Richt lijnen**: u kunt een virtueel netwerk, subnet of netwerk beveiligings groep niet rechtstreeks aan Azure Sentinel koppelen. U kunt echter een persoonlijk Azure-eind punt inschakelen voor de Log Analytics-werk ruimte die is gekoppeld aan Azure Sentinel om de communicatie van en naar uw particuliere netwerken te beperken.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische hulpprogram ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

**Hulp**: Azure-activiteiten logboek gebruiken om netwerk resource configuraties te bewaken en wijzigingen te detecteren voor netwerk bronnen die betrekking hebben op uw Azure Sentinel-werk ruimte. Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer er wijzigingen in kritieke resources plaatsvinden.

- [Controle inschakelen in azure Sentinel](resources.md)

- [Activiteiten logboek gebeurtenissen van Azure weer geven en ophalen](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)
 

- [Waarschuwingen maken in Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie [Azure Security Bench Mark: Logging and monitoring](../security/benchmarks/security-control-logging-monitoring.md)(Engelstalig) voor meer informatie.*

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

**Hulp**: audit logboeken van Azure Sentinel worden onderhouden in azure-activiteiten Logboeken. U kunt deze gegevens weer geven door deze vanuit het Azure-activiteiten logboek te streamen naar Azure Sentinel, waar u vervolgens onderzoek en analyses kunt uitvoeren.

- [Controle inschakelen in azure Sentinel](resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources

**Hulp**: audit logboeken voor Azure Sentinel worden bijgehouden in activiteiten logboeken van Azure. U kunt deze gegevens weer geven door deze vanuit het Azure-activiteiten logboek te streamen naar Azure Sentinel, waar u vervolgens onderzoek en analyses kunt uitvoeren.

- [Controle inschakelen in azure Sentinel](resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren

**Richt lijnen**: in azure monitor stelt u de Bewaar periode voor het logboek In voor log Analytics werk ruimten die zijn gekoppeld aan uw Azure Sentinel-werk ruimten volgens de nalevings voorschriften van uw organisatie.

- [Para meters voor het bewaren van Logboeken instellen](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren

**Hulp**: audit logboeken van Azure Sentinel worden onderhouden in azure-activiteiten Logboeken. U kunt deze gegevens weer geven door deze vanuit het Azure-activiteiten logboek te streamen naar Azure Sentinel, waar u vervolgens zoek acties en analyses kunt uitvoeren. Analyseer en Controleer Logboeken vanuit uw activiteiten logboeken van Azure Sentinel op afwijkend gedrag. Gebruik de sectie ' logs ' in de Azure Sentinel-werk ruimte om query's uit te voeren of waarschuwingen te maken op basis van uw Sentinel-Logboeken.

- [Controle inschakelen in azure Sentinel](resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteiten

**Hulp**: audit logboeken van Azure Sentinel worden onderhouden in azure-activiteiten Logboeken. U kunt deze gegevens weer geven door deze vanuit het Azure-activiteiten logboek te streamen naar Azure Sentinel, waar u vervolgens zoek acties en analyses kunt uitvoeren. Analyseer en Controleer Logboeken vanuit uw activiteiten logboeken van Azure Sentinel op afwijkend gedrag. Gebruik de sectie ' logs ' in de Azure Sentinel-werk ruimte om query's uit te voeren of waarschuwingen te maken op basis van uw Sentinel-Logboeken.

- [Controle inschakelen in azure Sentinel](resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [Azure Security Bench Mark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: een inventaris van beheerders accounts onderhouden

**Richt lijnen**: behoud een inventaris van de gebruikers accounts met beheerders toegang tot het besturings vlak (zoals Azure Portal) van uw Azure Sentinel-werk ruimte. 

U kunt het deel venster identiteits-en toegangs beheer in de Azure Portal voor uw abonnement gebruiken om op rollen gebaseerd toegangs beheer op basis van Azure (Azure RBAC) te configureren. De rollen worden toegepast op gebruikers, groepen, service-principals en beheerde identiteiten in Azure Active Directory (Azure AD). Azure Sentinel maakt ook gebruik van Azure RBAC om ingebouwde beheerders rollen te bieden, zoals Azure Sentinel contributor, die kunnen worden toegewezen aan gebruikers, groepen en services in Azure. 

- [Informatie over aangepaste rollen](../role-based-access-control/custom-roles.md)

- [Meer informatie over Azure RBAC in azure Sentinel](roles.md)

- [Azure RBAC voor werkmappen configureren](quickstart-get-visibility.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: speciale beheerders accounts gebruiken

**Richt lijnen**: Maak standaard procedures voor het gebruik van specifieke beheerders accounts. Gebruik Azure Security Center identiteits-en toegangs beheer om het aantal beheerders accounts te bewaken.

Daarnaast kunt u aanbevelingen van Azure Security Center of ingebouwde Azure-beleids regels gebruiken om u te helpen bij het bijhouden van specifieke beheerders accounts, zoals:

- Er moet meer dan één eigenaar zijn toegewezen aan uw abonnement
- Afgeschafte accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement

Meer informatie vindt u op de koppelingen waarnaar wordt verwezen.

- [Azure Security Center gebruiken om identiteit en toegang te bewaken (preview)](../security-center/security-center-identity-access.md)

- [Azure Policy gebruiken](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: gebruik Azure Active Directory eenmalige aanmelding (SSO)

**Hulp**: de toegang tot het vlak beheren van uw Azure Sentinel-werk ruimte (bijvoorbeeld Azure Portal) is beschikbaar via rest API en ondersteunt SSO. Als u zich wilt verifiëren, stelt u de autorisatie-header voor uw aanvragen in op een JSON Web Token die u hebt verkregen van Azure Active Directory (Azure AD).

- [Meer informatie over Azure Log Analytics REST API](/rest/api/loganalytics/)

- [Informatie over eenmalige aanmelding met Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: multi-factor Authentication gebruiken voor alle op Azure Active Directory gebaseerde toegang

**Hulp**: Schakel Azure Active Directory (Azure AD) multi-factor Authentication in en volg Azure Security Center aanbevelingen voor identiteits-en toegangs beheer.

- [Multi-factor Authentication inschakelen in azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identiteit en toegang bewaken in Azure Security Center](../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: beveiligde, door Azure beheerde werk stations gebruiken voor beheer taken

**Richt lijnen**: gebruik een privileged Access Workstation (Paw) met Azure AD multi-factor Authentication ingeschakeld om u aan te melden en uw Azure-Sentinel-gerelateerde resources te configureren. 
 

 
- [Privileged Access Workstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)
 

 
- [Een Azure AD-implementatie op basis van een Cloud met meerdere factoren plannen](../active-directory/authentication/howto-mfa-getstarted.md)

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

**Hulp**: gebruik benoemde locaties voor voorwaardelijke toegang om alleen toegang toe te staan tot de Azure Portal vanuit specifieke logische groepen met IP-adresbereiken of landen/regio's.

- [Benoemde locaties configureren in azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory gebruiken

**Hulp**: gebruik Azure Active Directory (Azure AD) als centraal verificatie-en autorisatie systeem voor uw onderverklikker instanties van Azure. Azure AD beveiligt gegevens door gebruik te maken van sterke versleuteling voor gegevens in rust en onderweg. Azure AD bevat ook zouten, hashes en veilige gebruikers referenties.

- [Een nieuwe Azure AD-Tenant maken en configureren](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regel matig gebruikers toegang controleren en afstemmen

**Hulp**: Azure Active Directory (Azure AD) bevat logboeken waarmee u verouderde accounts kunt detecteren. Daarnaast kunt u Azure Identity Access revisies gebruiken om groepslid maatschappen en de toegang tot bedrijfs toepassingen en roltoewijzingen op efficiënte wijze te beheren. Gebruikers toegang kan regel matig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben.

- [Meer informatie over Azure AD-rapportage](/azure/active-directory/reports-monitoring)

- [Beoordelingen over Azure Identity Access gebruiken](../active-directory/governance/access-reviews-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: controle pogingen om toegang te krijgen tot gedeactiveerde referenties

**Hulp**: gebruik Azure Active Directory (Azure AD) als centraal verificatie-en autorisatie systeem voor uw Azure-Sentinel-werk ruimten. Azure AD beveiligt gegevens door gebruik te maken van sterke versleuteling voor gegevens in rust en onderweg. Azure AD bevat ook zouten, hashes en veilige gebruikers referenties.

U hebt toegang tot de Azure AD-aanmeldings activiteit, de controle-en risico gebeurtenis logboek bronnen, waarmee u kunt integreren met Azure Sentinel of een systeem informatie en gebeurtenis beheer oplossing van derden.

U kunt dit proces stroom lijnen door Diagnostische instellingen voor Azure AD-gebruikers accounts te maken en de audit logboeken en aanmeldings logboeken te verzenden naar een Log Analytics-werk ruimte. U kunt de gewenste logboek waarschuwingen configureren in Log Analytics.

- [Azure-activiteitenlogboeken integreren in Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Azure-Sentinel aan de trein](quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: waarschuwing voor de afwijking van het aanmeldings gedrag van het account

**Richt lijnen**: voor de afwijking van het aanmeldings gedrag van accounts op het besturings vlak (bijvoorbeeld Azure Portal), gebruikt u de functies voor identiteits beveiliging en risico detectie van Azure Active Directory (Azure AD) voor het configureren van automatische antwoorden op gedetecteerde verdachte acties met betrekking tot gebruikers identiteiten. U kunt ook gegevens opnemen in azure Sentinel voor verder onderzoek.

- [Hoe kan ik een Risk ante aanmelding van Azure AD weer geven?](../active-directory/identity-protection/overview-identity-protection.md)

- [Risico beleid voor identiteits beveiliging configureren en inschakelen](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure-Sentinel onboarden](quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: micro soft biedt toegang tot relevante klant gegevens tijdens ondersteunings scenario's

**Hulp**: momenteel niet beschikbaar; Klanten-lockbox wordt nog niet ondersteund voor de Azure Sentinel-of Log Analytics-werk ruimte.

- [Lijst met door Klanten-lockbox ondersteunde services](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

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

**Hulp** bij het implementeren van afzonderlijke abonnementen, zoals beheer groepen voor de ontwikkelings-, test-en productie-Sentinel-werk ruimten.

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Beheergroepen maken](../governance/management-groups/create-management-group-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: alle gevoelige gegevens in de overdracht versleutelen

**Richt lijnen**: als u een Azure-of on-premises virtuele machine als syslog-doorstuur server gebruikt, moet u de syslog-daemon (rsyslog of syslog-ng) configureren om te communiceren in TLS.

- [Verbind uw externe oplossing met de algemene gebeurtenis indeling](connect-common-event-format.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: een actief detectie hulpprogramma gebruiken om gevoelige gegevens te identificeren

**Hulp**: de functies gegevens identificatie, classificatie en verlies preventie zijn nog niet beschikbaar in Azure. Implementeer oplossingen van derden, indien nodig voor nalevings doeleinden.

Voor het onderliggende platform dat door micro soft wordt beheerd, behandelt micro soft alle inhoud van de klant als gevoelig en gaat u naar een fantastische lengte om te beschermen tegen verlies en bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: op rollen gebaseerd toegangs beheer gebruiken voor het beheren van de toegang tot bronnen

**Richt lijnen**: u kunt het deel venster identiteits-en toegangs beheer (IAM) in de Azure Portal gebruiken om op rollen gebaseerd toegangs beheer op basis van Azure (Azure RBAC) te configureren. De rollen worden toegepast op gebruikers, groepen, service-principals en beheerde identiteiten in Active Directory. U kunt ingebouwde Azure-rollen of aangepaste rollen gebruiken voor individuen en groepen. 

Azure-Sentinel maakt gebruik van Azure RBAC om ingebouwde rollen te bieden die kunnen worden toegewezen aan gebruikers, groepen en services in Azure. Met behulp van Azure RBAC kunt u rollen binnen uw beveiligings team gebruiken en maken om de juiste toegang tot Azure Sentinel te verlenen. Op basis van de rollen hebt u nauw keurige controle over wat gebruikers met toegang tot Azure Sentinel kunnen zien. U kunt Azure-rollen rechtstreeks toewijzen in de Sentinel-werk ruimte van Azure of op een abonnement of resource groep waartoe de werk ruimte behoort. Er zijn drie specifieke, ingebouwde Azure Sentinel-rollen:

- Azure Sentinel Reader
- Azure Sentinel responder
- Azure Sentinel-bijdrager

Naast Azure Sentinel exclusieve Azure-rollen zijn er Azure en Log Analytics ingebouwde Azure-rollen die een grotere set machtigingen kunnen verlenen die toegang bieden tot uw Azure Sentinel-werk ruimte en andere resources:

Azure-rollen zijn onder andere eigenaar, bijdrager en lezer. Azure-rollen verlenen toegang tot alle Azure-resources, waaronder Log Analytics-werk ruimten en Azure-Sentinel-resources.

Log Analytics functies zijn Log Analytics Inzender en Log Analytics lezer. Log Analytics rollen verlenen toegang tot alle Log Analytics-werk ruimten.

Elke Sentinel-werkmap is bovendien een Azure-resource en u kunt rollen toewijzen aan gebruikers voor het beheren van de toegang.

- [Azure RBAC configureren](../role-based-access-control/role-assignments-portal.md)

- [Informatie over aangepaste rollen](../role-based-access-control/custom-roles.md)

- [Rollen in Sentinel en Log Analytics begrijpen](roles.md)

- [Azure RBAC voor werkmappen configureren](quickstart-get-visibility.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: gevoelige informatie op rest versleutelen

**Hulp**: Azure Sentinel en Azure monitor log Analytics-werk ruimten maken momenteel gebruik van door micro soft beheerde sleutels voor het versleutelen van opgenomen gegevens in rust. De mogelijkheid om uw eigen sleutel te halen, is nog niet volledig ondersteund voor Sentinel, maar zal zich in de nabije toekomst bevinden. 

 
- [Azure Monitor door de klant beheerde sleutel overzicht](https://docs.microsoft.com/azure/azure-monitor/logs/customer-managed-keys#customer-managed-key-overview) 

 
- [Informatie over door de klant beheerde sleutels in azure Sentinel (preview-versie)](customer-managed-keys.md)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

**Hulp**: audit logboeken van Azure Sentinel worden onderhouden in azure-activiteiten Logboeken. U kunt deze gegevens weer geven door deze vanuit het Azure-activiteiten logboek te streamen naar Azure Sentinel, waar u vervolgens onderzoek en analyses kunt uitvoeren. In de sectie ' logs ' in uw Azure Sentinel-werk ruimte kunt u waarschuwingen maken voor wanneer er wijzigingen worden aangebracht in productie-en test-werk ruimten van Azure, evenals andere kritieke of gerelateerde resources.

- [Controle inschakelen in azure Sentinel](resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie [Azure Security Bench Mark: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md)voor meer informatie.*

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

Meer informatie vindt u op de koppelingen waarnaar wordt verwezen.

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Beheergroepen maken](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

**Hulp**: gebruik Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in uw abonnementen.

Gebruik Azure resource Graph voor het opvragen/detecteren van resources binnen hun abonnement (en).  Zorg ervoor dat alle Azure-resources die aanwezig zijn in de omgeving, zijn goedgekeurd.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Query's maken met Azure Graph](../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="69-use-only-approved-azure-services"></a>6,9: alleen goedgekeurde Azure-Services gebruiken

**Hulp: gebruik** Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in klant abonnement (en) met de volgende ingebouwde beleids definities:

- Niet toegestane resourcetypen
- Toegestane brontypen

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een specifiek resource type weigeren met Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

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

**Hulp**: Definieer en implementeer standaard beveiligings configuraties voor uw log Analytics-werk ruimte die is gekoppeld aan uw Sentinel-werk ruimte met Azure Policy. Gebruik Azure Policy aliassen in de naam ruimte ' micro soft. OperationalInsights ' om aangepaste beleids regels te maken om de configuratie van uw Log Analytics-werk ruimten te controleren of af te dwingen.
 

- [Beschik bare Azure Policy aliassen weer geven](/powershell/module/az.resources/get-azpolicyalias)

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: Beveilig Azure-resource configuraties onderhouden

**Richt lijnen**: gebruik Azure Policy [deny] en [implementeren indien niet aanwezig] effecten om beveiligde instellingen af te dwingen voor uw Azure-resources.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy effecten begrijpen](../governance/policy/concepts/effects.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan

**Richt lijnen**: als u aangepaste definities van Azure-beleid gebruikt, kunt u Azure DevOps of Azure opslag plaatsen gebruiken om uw code veilig op te slaan en te beheren.
 

- [Code opslaan in azure DevOps](/azure/devops/repos/git/gitworkflow)

 
- [Documentatie voor Azure opslag plaatsen](/azure/devops/repos/)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: hulpprogram ma's voor configuratie beheer voor Azure-resources implementeren

**Hulp**: gebruik ingebouwde Azure Policy definities en Azure Policy aliassen in de naam ruimte ' micro soft. OperationalInsights ' om aangepaste beleids regels te maken om systeem configuraties te Signa lering, te controleren en af te dwingen. Ontwikkel bovendien een proces en pijp lijn voor het beheren van beleids uitzonderingen.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-resources implementeren

**Hulp**: gebruik ingebouwde Azure Policy definities en Azure Policy aliassen in de naam ruimte ' micro soft. OperationalInsights ' om aangepaste beleids regels te maken om systeem configuraties te Signa lering, te controleren en af te dwingen. Gebruik het Azure-beleid [audit], [deny] en [implementeren indien niet aanwezig] effecten om automatisch configuraties voor uw Azure-resources af te dwingen.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure-geheimen veilig beheren

**Hulp**: Azure Sentinel ondersteunt het verzamelen van logboeken van veel bronnen met behulp van verschillende connectors. Voor sommige van deze connectors is Setup vereist met een Log Analytics werkruimte sleutel. Bij het instellen van deze connectors gebruikt u Azure Key Vault om uw sleutels op te slaan om het geheim beheer te vereenvoudigen en onbedoelde referentie blootstelling te voor komen.

- [Sentinel verbinden met gegevens bronnen](connect-data-sources.md)

- [Een Key Vault maken](../key-vault/secrets/quick-create-portal.md)

- [Key Vault verificatie bieden met een beheerde identiteit](../key-vault/general/assign-access-policy-portal.md)

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

**Hulp**: micro soft antimalware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services (bijvoorbeeld Azure Sentinel en log Analytics), maar wordt niet uitgevoerd op de inhoud van de klant. 

Het is uw verantwoordelijkheid om vooraf te scannen op inhoud die wordt geüpload naar niet-reken resources van Azure, waaronder Log Analytics-werk ruimte. Micro soft heeft geen toegang tot klant gegevens en kan daarom geen anti-malware scans uitvoeren van klant inhoud namens u.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="data-recovery"></a>Gegevensherstel

*Zie [Azure Security Bench Mark: Data Recovery](../security/benchmarks/security-control-data-recovery.md)(Engelstalig) voor meer informatie.*

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zorg voor de bescherming van back-ups en door de klant beheerde sleutels

**Hulp**: Schakel Soft-Delete in Key Vault in om sleutels te beschermen tegen onbedoelde of schadelijke verwijdering. In het geval dat de sleutel versleutelings sleutel door een gebruiker wordt ingetrokken door deze te verwijderen of door de toegang tot Azure-Sentinel te verwijderen binnen één uur, zal Azure Sentinel de wijziging naleven en zich gedragen alsof de gegevens niet meer beschikbaar zijn. Op dit moment wordt een bewerking uitgevoerd die gebruikmaakt van permanente opslag resources, zoals gegevens opname, blijvende configuratie wijzigingen en het maken van incidenten, voor komen. Eerder opgeslagen gegevens worden niet verwijderd, maar blijven ontoegankelijk. Ontoegankelijke gegevens vallen onder het beleid voor gegevens behoud en worden verwijderd overeenkomstig dat beleid.

De enige bewerking die mogelijk is nadat de versleutelings sleutel is ingetrokken of verwijderd, is account verwijdering.

Als de toegang na het intrekken wordt hersteld, wordt de toegang tot de gegevens binnen een uur hersteld door Azure Sentinel.

- [Soft-Delete in Key Vault inschakelen](../storage/blobs/soft-delete-blob-overview.md)

- [Informatie over door de klant beheerde sleutels in azure Sentinel](customer-managed-keys.md)

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

- [Waarschuwingen streamen naar Azure Sentinel](connect-azure-security-center.md)

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

- Zie [Overzicht Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Meer informatie over [Azure-beveiligingsbasislijnen](/azure/security/benchmarks/security-baselines-overview)
