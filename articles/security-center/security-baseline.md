---
title: Azure-beveiligings basislijn voor Security Center
description: De Security Center Security Baseline voorziet in procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-Bench Mark.
author: msmbaldwin
ms.service: security-center
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: e9e7586c29f3b75512a57b0f89885f13e58e2ba1
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94949255"
---
# <a name="azure-security-baseline-for-security-center"></a>Azure-beveiligings basislijn voor Security Center

Deze beveiligings basislijn is van toepassing op de richt lijnen van [Azure Security](../security/benchmarks/overview.md) voor de Azure Security Center. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen. De inhoud wordt gegroepeerd op de **beveiligings controles** die zijn gedefinieerd door de Azure Security-benchmark en de bijbehorende richt lijnen die van toepassing zijn op Azure Security Center. **Besturings elementen** die niet van toepassing zijn op Azure Security Center, zijn uitgesloten. Als u wilt zien hoe Azure Security Center volledig is toegewezen aan de beveiligings benchmark van Azure, raadpleegt u het [volledige Azure Security Center beveiligings basislijn toewijzings bestand](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Bench Mark: Network Security](../security/benchmarks/security-control-network-security.md)(Engelstalig) voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-resources in virtuele netwerken beveiligen

**Hulp**: Azure Security Center is een basis-Azure-aanbieding. U kunt een virtueel netwerk, subnet of netwerk beveiligings groep niet rechtstreeks aan Security Center koppelen. Als u het verzamelen van gegevens voor uw reken resources inschakelt, kunt Security Center de gegevens die worden verzameld via een Log Analytics-werk ruimte, de werk ruimte zodanig configureren dat deze gebruikmaakt van een persoonlijke koppeling om toegang te krijgen tot uw werkruimte gegevens via een persoonlijk eind punt in uw virtuele netwerk. Als het gebruik van gegevens verzameling Security Center afhankelijk is van de Log Analytics agent die wordt geïmplementeerd op uw servers voor het verzamelen van beveiligings gegevens en het bieden van beveiliging voor deze reken resources. De Log Analytics-agent vereist dat specifieke poorten en protocollen worden geopend voor een juiste werking met Security Center. Vergrendel uw netwerken zodat alleen deze vereiste poorten en protocollen zijn toegestaan en voeg alleen aanvullende regels toe die uw toepassing nodig heeft om via netwerk beveiligings groepen te werken.

- [Gegevensverzameling in Azure Security Center](security-center-enable-data-collection.md)

- [Bestands netwerk verkeer met een netwerk beveiligings groep](../virtual-network/tutorial-filter-network-traffic.md)

- [Firewall vereisten voor het gebruik van de Log Analytics-agent](../azure-monitor/platform/log-analytics-agent.md#firewall-requirements)

- [Persoonlijke Azure-koppeling](../private-link/private-link-overview.md) 

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: standaard beveiligings configuraties voor netwerk apparaten onderhouden

**Hulp**: de Azure Security Center-aanbieding wordt niet rechtstreeks geïntegreerd met een virtueel netwerk, maar kan gegevens verzamelen van servers die zijn geconfigureerd met de log Analytics-agent die op uw netwerken wordt geïmplementeerd. Uw servers die zijn geconfigureerd voor het verzenden van gegevens naar Security Center vereisen dat bepaalde poorten en protocollen goed kunnen communiceren. Standaard beveiligings configuraties voor deze netwerk resources definiëren en afdwingen met Azure Policy.

U kunt ook Azure-blauw drukken gebruiken om grootschalige Azure-implementaties te vereenvoudigen door het verpakken van sleutel omgevings artefacten, zoals Azure Resource Manager sjablonen, roltoewijzingen en Azure Policy toewijzingen, in één definitie van de blauw druk. U kunt de blauw druk Toep assen op nieuwe abonnementen om consistente en veilige Security Center configuraties en gerelateerde netwerk bronnen te implementeren.

- [Gegevensverzameling in Azure Security Center](security-center-enable-data-collection.md)

- [Firewall vereisten voor het gebruik van de Log Analytics-agent](../azure-monitor/platform/log-analytics-agent.md#firewall-requirements)

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md) 

- [Voor beelden Azure Policy voor netwerken](../governance/policy/samples/built-in-policies.md#network)

- [Een Azure Blueprint maken](../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="110-document-traffic-configuration-rules"></a>1,10: configuratie regels voor het document verkeer

**Hulp**: de Azure Security Center-aanbieding wordt niet rechtstreeks geïntegreerd met een virtueel netwerk, maar kan gegevens verzamelen van servers die zijn geconfigureerd met de log Analytics-agent die op uw netwerken wordt geïmplementeerd. Uw servers die zijn geconfigureerd voor het verzenden van gegevens naar Security Center vereisen dat bepaalde poorten en protocollen goed kunnen communiceren. Standaard beveiligings configuraties voor deze netwerk resources definiëren en afdwingen met Azure Policy.

Gebruik resource Tags voor netwerk beveiligings groepen en andere bronnen zoals servers in uw netwerken die zijn geconfigureerd voor het verzenden van beveiligings logboeken naar Azure Security Center. Gebruik voor afzonderlijke regels voor de netwerk beveiligings groep het veld Beschrijving om de regels te documenteren die verkeer van of naar een netwerk toestaan. 

Gebruik een van de ingebouwde Azure Policy definities die betrekking hebben op labelen, zoals ' tag vereisen en de waarde ', om ervoor te zorgen dat alle resources met tags worden gemaakt en u op de hoogte moet zijn van bestaande niet-gelabelde resources.

U kunt Azure PowerShell of Azure CLI gebruiken om op basis van hun labels acties op te zoeken of uit te voeren op resources. 

- [Gegevensverzameling in Azure Security Center](security-center-enable-data-collection.md)

- [Firewall vereisten voor het gebruik van de Log Analytics-agent](../azure-monitor/platform/log-analytics-agent.md#firewall-requirements)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md) 

- [Een Azure-Virtual Network maken](../virtual-network/quick-create-portal.md) 

- [Netwerk verkeer filteren met regels voor netwerk beveiligings groepen](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische hulpprogram ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

**Hulp**: Azure-activiteiten logboek gebruiken om resource configuraties te bewaken en wijzigingen te detecteren voor netwerk bronnen die betrekking hebben op Azure Security Center. Maak waarschuwingen in Azure Monitor om u te waarschuwen wanneer wijzigingen aan kritieke resources plaatsvinden.

- [Activiteiten logboek gebeurtenissen van Azure weer geven en ophalen](../azure-monitor/platform/activity-log.md#view-the-activity-log) 

- [Waarschuwingen maken in Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie [Azure Security Bench Mark: Logging and monitoring](../security/benchmarks/security-control-logging-monitoring.md)(Engelstalig) voor meer informatie.*

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

**Richt lijnen**: statistische beveiligings gegevens gegenereerd door Azure Security Center en de verbonden bronnen met behulp van een centrale log Analytics-werk ruimte. 

Configureer gegevens verzameling van Security Center om beveiligings gegevens en gebeurtenissen van uw verbonden Azure-reken resources te verzenden naar een centrale Log Analytics-werk ruimte. Naast het verzamelen van gegevens, gebruikt u de functie continue export om beveiligings waarschuwingen en aanbevelingen die zijn gegenereerd door Security Center, te streamen naar uw centrale Log Analytics-werk ruimte. In Azure Monitor kunt u de beveiligings gegevens die zijn gegenereerd op basis van Security Center en uw verbonden Azure-resources, opvragen en er analyses op uitvoeren. 

U kunt ook gegevens verzenden die zijn geproduceerd door Security Center naar Azure Sentinel of een SIEM van derden.

- [Security Center-gegevens continu exporteren](continuous-export.md)

- [Gegevensverzameling in Azure Security Center](security-center-enable-data-collection.md)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md) 

- [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Interne host-logboeken van de virtuele machine van Azure verzamelen met Azure Monitor](../azure-monitor/learn/quick-collect-azurevm.md)

- [Aan de slag met Azure Monitor en integratie van SIEM van derden](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources

**Hulp**: Azure monitor activiteiten logboeken zijn automatisch beschikbaar. deze logboeken bevatten alle schrijf bewerkingen voor uw resource, zoals Azure Security Center, inclusief welke bewerkingen zijn uitgevoerd, wie de bewerking heeft gestart en wanneer deze zich hebben voorgedaan. Verzend uw Azure-activiteiten logboeken naar een Log Analytics-werk ruimte voor consolidatie van het logboek en een verhoogde Bewaar periode.

- [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Logboek registratie en verschillende logboek typen in azure](../azure-monitor/platform/platform-logs-overview.md)

- [Activiteiten logboeken verzenden naar een Log Analytics-werk ruimte](../azure-monitor/platform/activity-log.md#send-to-log-analytics-workspace)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren

**Richt lijnen**: stel in azure monitor uw Bewaar periode voor log Analytics werkruimte in volgens de nalevings voorschriften van uw organisatie. Gebruik Azure Storage accounts voor lange termijn-en archiverings opslag. 

- [De Bewaar periode voor gegevens wijzigen in Log Analytics](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period) 

- [Bewaar beleid configureren voor logboeken van Azure Storage-account](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren

**Hulp**: Analyseer en bewaak logboeken die zijn geproduceerd door Azure Security Center en de verbonden bronnen voor afwijkend gedrag en Bekijk regel matig de resultaten. Gebruik Azure Monitor en een Log Analytics werk ruimte om logboeken te controleren en query's uit te voeren op logboek gegevens.

U kunt ook gegevens in-en inschakelen voor Azure Sentinel of een SIEM van derden. 

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md) 

- [Aan de slag met Log Analytics query's](../azure-monitor/log-query/get-started-portal.md) 

- [Aangepaste query's uitvoeren in Azure Monitor](../azure-monitor/log-query/get-started-queries.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteiten

**Hulp** bij het configureren van waarschuwingen voor Azure monitor Logboeken om te zoeken naar ongewenste of afwijkende activiteiten die worden vastgelegd door het activiteiten logboek of de gegevens die door Azure Security Center worden geproduceerd. Stel actie groepen zo in dat uw organisatie wordt gewaarschuwd en actie kan ondernemen als een logboek waarschuwing wordt gestart voor afwijkende activiteiten. Gebruik Security Center functie werk stroom automatisering om logische apps te activeren voor beveiligings waarschuwingen en aanbevelingen. Security Center werk stromen kunnen worden gebruikt om gebruikers op de hoogte te stellen van incident reacties of acties ondernemen om resources te herstellen op basis van de informatie over de waarschuwing.

U kunt ook gegevens die zijn gerelateerd aan en worden geproduceerd door Azure Security Center naar Azure Sentinel inschakelen. Azure Sentinel ondersteunt playbooks die automatische bedreigings reacties op beveiligings problemen toestaan.

- [Automatisering van werk stromen Azure Security Center](workflow-automation.md)

- [Waarschuwingen beheren in Azure Security Center](security-center-managing-and-responding-alerts.md) 

- [Een waarschuwing over logboek gegevens van log Analytics](../azure-monitor/learn/tutorial-response.md)

- [Automatische bedreigings reacties instellen in azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

- [Waarschuwingen registreren in Azure Monitor](../azure-monitor/platform/alerts-unified-log.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [Azure Security Bench Mark: identiteits-en toegangs beheer](../security/benchmarks/security-control-identity-access-control.md)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: een inventaris van beheerders accounts onderhouden

**Richt lijnen**: met Azure op rollen gebaseerd toegangs beheer (Azure RBAC) kunt u de toegang tot Azure-resources beheren via roltoewijzingen. U kunt deze rollen toewijzen aan gebruikers, groeperingen van service-principals en beheerde identiteiten. Er zijn vooraf gedefinieerde ingebouwde rollen voor bepaalde resources, en deze rollen kunnen worden geïnventariseerd of opgevraagd via hulpprogram ma's als Azure CLI, Azure PowerShell of de Azure Portal. Azure Security Center heeft ingebouwde rollen voor ' beveiligings lezer ' of ' beveiliging admin, waarmee gebruikers beveiligings beleid kunnen lezen of bijwerken en waarschuwingen en aanbevelingen kan negeren.

- [Machtigingen in Azure Security Center](security-center-permissions.md)

- [Een directory-rol verkrijgen in azure AD met Power shell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0) 

- [Leden van een directory-rol in azure AD ophalen met Power shell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: speciale beheerders accounts gebruiken

**Richt lijnen**: Maak standaard procedures voor het gebruik van specifieke beheerders accounts voor het Azure-platform of specifieke voor de Azure Security Center aanbieding. Gebruik Azure Security Center identiteits-en toegangs beheer om het aantal beheerders accounts in Azure Active Directory te bewaken. Security Center heeft ook ingebouwde rollen voor ' beveiliging admin, waarmee gebruikers beveiligings beleid kunnen bijwerken en waarschuwingen en aanbevelingen kan negeren. Zorg ervoor dat u alle gebruikers met deze roltoewijzing regel matig kunt controleren en reconciliëren.

Daarnaast kunt u aanbevelingen van Azure Security Center of ingebouwde Azure-beleids regels gebruiken om u te helpen bij het bijhouden van specifieke beheerders accounts, zoals:

- Er moet meer dan één eigenaar zijn toegewezen aan uw abonnement
- Afgeschafte accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement

- [Machtigingen in Azure Security Center](security-center-permissions.md)

- [Azure Security Center gebruiken om identiteit en toegang te bewaken (preview)](security-center-identity-access.md)

- [Azure Policy gebruiken](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: eenmalige aanmelding (SSO) met Azure Active Directory gebruiken

**Richt lijnen**: gebruik waar mogelijk Azure Active Directory-SSO in plaats van afzonderlijke zelfstandige referenties per service te configureren. Gebruik Azure Security Center-aanbevelingen voor identiteit en toegang.

- [Informatie over eenmalige aanmelding met Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: multi-factor Authentication gebruiken voor alle op Azure Active Directory gebaseerde toegang

**Hulp**: Azure Active Directory MFA inschakelen voor toegang tot Azure Security Center en de Azure Portal, volgt u de aanbevelingen voor Security Center identiteit en toegang. 

- [MFA inschakelen in azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Identiteit en toegang bewaken in Azure Security Center](security-center-identity-access.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: gebruik speciale machines (privileged Access workstations) voor alle beheer taken

**Hulp**: gebruik een beveiligd, door Azure beheerd werk station (ook wel een privileged Access Workstation of paw) voor beheer taken waarvoor verhoogde bevoegdheden zijn vereist.

- [Meer informatie over veilige, door Azure beheerde werk stations](../active-directory/devices/concept-azure-managed-workstation.md)

- [Azure AD MFA inschakelen](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logboeken en waarschuwingen voor verdachte activiteiten van beheerders accounts

**Hulp**: gebruik Azure Active Directory beveiligings rapporten en-bewaking om te detecteren wanneer verdachte of onveilige activiteiten in de omgeving worden uitgevoerd. Gebruik Azure Security Center om identiteits-en toegangs activiteiten te bewaken.

- [Azure AD-gebruikers identificeren die zijn gemarkeerd voor Risk ante activiteiten](../active-directory/identity-protection/overview-identity-protection.md) 

- [Identiteits-en toegangs activiteiten van gebruikers controleren in Azure Security Center](security-center-identity-access.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Richt lijnen**: gebruik Azure AD benoemde locaties om alleen toegang toe te staan vanuit specifieke logische groepen met IP-adresbereiken of landen/regio's. 

- [De benoemde locaties van Azure AD configureren](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory gebruiken

**Hulp**: gebruik Azure Active Directory (Azure AD) als centrale verificatie-en autorisatie systeem wanneer u Azure Security Center gebruikt. Azure AD beveiligt gegevens door gebruik te maken van sterke versleuteling voor gegevens in rust en onderweg. Azure AD bevat ook zouten, hashes en veilige gebruikers referenties. Azure Security Center heeft ingebouwde rollen die kunnen worden toegewezen, zoals ' beveiliging admin ', waarmee gebruikers beveiligings beleid kan bijwerken en waarschuwingen en aanbevelingen kan negeren.

- [Machtigingen in Azure Security Center](security-center-permissions.md)

- [Een Azure AD-exemplaar maken en configureren](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regel matig gebruikers toegang controleren en afstemmen

**Hulp**: Azure Active Directory biedt logboeken waarmee u verlopen accounts kunt detecteren. Daarnaast kunt u Azure AD Identity and Access revisies gebruiken om groepslid maatschappen, toegang tot bedrijfs toepassingen en roltoewijzingen op efficiënte wijze te beheren. Gebruikers toegang met betrekking tot Azure Security Center kan regel matig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben. 

- [Meer informatie over Azure AD-rapportage](../active-directory/reports-monitoring/index.yml) 

- [Azure AD-identiteits-en toegangs beoordelingen gebruiken](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: controle pogingen om toegang te krijgen tot gedeactiveerde referenties

**Richt lijnen**: u hebt toegang tot de Azure AD-aanmeldings activiteit, audit en risico gebeurtenis logboek bronnen, waarmee u kunt integreren met elk Siem/bewakings programma. 

U kunt dit proces stroom lijnen door Diagnostische instellingen voor Azure AD-gebruikers accounts te maken en de audit logboeken en aanmeldings logboeken te verzenden naar een Log Analytics-werk ruimte. U kunt de gewenste waarschuwingen configureren in Log Analytics werk ruimte.  

- [Azure-activiteiten logboeken integreren met Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: waarschuwing voor de afwijking van het aanmeldings gedrag van accounts 

**Hulp**: gebruik Azure AD Identity Protection functies om automatische antwoorden te configureren op gedetecteerde verdachte acties met betrekking tot gebruikers identiteiten. U kunt ook gegevens opnemen in azure Sentinel voor verder onderzoek. 

- [Risk ante aanmeldingen voor Azure AD weer geven](../active-directory/identity-protection/overview-identity-protection.md) 

- [Risico beleid voor identiteits beveiliging configureren en inschakelen](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Bench Mark: Data Protection](../security/benchmarks/security-control-data-protection.md)voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: een inventaris van gevoelige informatie onderhouden

**Hulp**: Labels gebruiken bij het bijhouden van Azure-resources zoals de log Analytics-werk ruimte waarin gevoelige beveiligings gegevens van Azure Security Center worden opgeslagen.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: systemen isoleren die gevoelige informatie opslaan of verwerken

**Richt lijnen**: isolatie implementeren met afzonderlijke abonnementen en beheer groepen voor afzonderlijke beveiligings domeinen, zoals omgevings type en gegevens gevoeligheids niveau. U kunt het toegangs niveau voor uw Azure-resources beperken die worden vereist door uw toepassingen en bedrijfs omgevingen. U kunt de toegang tot Azure-resources beheren via Azure RBAC.

Standaard worden Azure Security Center gegevens opgeslagen in de Security Center back-end-service. Als uw organisatie vereisten heeft toegevoegd om deze gegevens op te slaan in uw eigen resources, kunt u een Log Analytics-werk ruimte configureren om Security Center gegevens, waarschuwingen en aanbevelingen op te slaan. Wanneer u uw eigen werk ruimte gebruikt, kunt u verdere schei ding toevoegen door verschillende werk ruimten te configureren op basis van de omgeving waarin de gegevens afkomstig zijn.

- [Security Center-gegevens continu exporteren](continuous-export.md)

- [Gegevensverzameling in Azure Security Center](security-center-enable-data-collection.md)

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md) 

- [Beheer groepen maken](../governance/management-groups/create-management-group-portal.md) 

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: alle gevoelige gegevens in de overdracht versleutelen

**Hulp**: alle gevoelige gegevens in de overdracht versleutelen. Zorg ervoor dat alle clients die verbinding maken met uw Azure-resources, TLS 1,2 of hoger kunnen onderhandelen. Alle virtuele machines die zijn geconfigureerd met de Log Analytics-agent en gegevens verzenden naar Azure Security Center, moeten worden geconfigureerd voor het gebruik van TLS 1,2.

Volg Azure Security Center aanbevelingen voor het versleutelen van de rest en de versleuteling in de door Voer, indien van toepassing. 

- [Gegevens veilig naar Log Analytics verzenden](../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12)

- [Meer informatie over versleuteling in transit met Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: gedeeld

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Azure RBAC gebruiken om de toegang tot resources te beheren 

**Richt lijnen**: gebruik Azure RBAC (op rollen gebaseerd toegangs beheer) om de toegang tot Azure Security Center gerelateerde gegevens en resources te beheren. Azure Security Center heeft ingebouwde rollen voor ' beveiligings lezer ' of ' beveiliging admin, waarmee gebruikers beveiligings beleid kunnen lezen of bijwerken en waarschuwingen en aanbevelingen kan negeren. De Log Analytics-werk ruimte waarin de gegevens worden opgeslagen die door Security Center zijn verzameld, heeft ook ingebouwde rollen die u kunt toewijzen als Log Analytics Reader, Log Analytics Inzender en anderen. Wijs de minst strikte rol toe die gebruikers nodig hebben om hun vereiste taken uit te voeren. Wijs bijvoorbeeld gebruikers die alleen informatie hoeven te bekijken over de beveiligingsstatus van resources, maar geen maatregelen hoeven te nemen zoals het toepassen van aanbevelingen of het bewerken van beleid, de rol Lezer toe.

- [Machtigingen voor Azure Log Analytics-werk ruimte](../role-based-access-control/built-in-roles.md#log-analytics-reader)

- [Machtigingen in Azure Security Center](security-center-permissions.md)

- [Azure RBAC configureren](../role-based-access-control/role-assignments-portal.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: gevoelige informatie op rest versleutelen

**Hulp**: Azure Security Center gebruikt een geconfigureerde log Analytics-werk ruimte om de gegevens, waarschuwingen en aanbevelingen op te slaan die worden gegenereerd. Configureer een door de klant beheerde sleutel (CMK) voor de werk ruimte die u hebt geconfigureerd voor het verzamelen van Security Center gegevens. CMK maakt het mogelijk om alle gegevens die worden opgeslagen of verzonden naar de werk ruimte te versleutelen met een Azure Key Vault sleutel die u hebt gemaakt en waarvan u de eigenaar bent. 

- [Door de klant beheerde sleutel van Azure Monitor](../azure-monitor/platform/customer-managed-keys.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

**Hulp**: gebruik Azure monitor om waarschuwingen te maken wanneer wijzigingen worden aangebracht in essentiële Azure-resources met betrekking tot Azure Security Center. Deze wijzigingen kunnen bestaan uit alle acties waarmee configuraties met betrekking tot Security Center worden gewijzigd, zoals het uitschakelen van waarschuwingen of aanbevelingen, of het bijwerken of verwijderen van gegevens archieven.

- [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteiten logboek](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie voor meer informatie de [Azure Security Bench Mark: beveiligingslek beheer](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: een risico classificatie proces gebruiken om prioriteit te geven aan het herstel van ontdekte beveiligings problemen

**Richt lijnen**: gebruik een gemeen schappelijke risico Score programma (bijvoorbeeld gemeen schappelijke restrictie systeem voor beveiligings problemen) of de standaard risico classificaties die worden meegeleverd met het hulp programma van derden.

- [NIST-publicatie-algemene punten voor beveiligings problemen](https://www.nist.gov/publications/common-vulnerability-scoring-system)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="inventory-and-asset-management"></a>Inventarisatie en asset-management

*Zie [Azure Security Bench Mark: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md)voor meer informatie.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatische Asset-detectie oplossing gebruiken

**Hulp**: Azure resource Graph gebruiken om alle resources te zoeken en te detecteren die betrekking hebben op Azure Security Center in uw abonnementen. Zorg ervoor dat u de juiste (Lees) machtigingen in uw Tenant hebt en alle Azure-abonnementen opsommen om Security Center bronnen te detecteren. 

- [Query's maken met Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md) 

- [Uw Azure-abonnementen weer geven](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0) 

- [Meer informatie over Azure RBAC](../role-based-access-control/overview.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="62-maintain-asset-metadata"></a>6,2: meta gegevens van activa onderhouden

**Hulp**: Labels gebruiken bij het bijhouden van Azure-resources zoals de log Analytics-werk ruimte waarin gevoelige beveiligings gegevens van Azure Security Center worden opgeslagen.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: niet-geautoriseerde Azure-resources verwijderen

**Richt lijnen**: Gebruik labels, beheer groepen en afzonderlijke abonnementen, waar nodig, om Azure Security Center-resources in te delen en bij te houden. Sluit de inventaris regel matig af en zorg ervoor dat niet-geautoriseerde resources tijdig worden verwijderd uit het abonnement.

Daarnaast kunt u met Azure Policy beperkingen opleggen aan het type resources dat kan worden gemaakt in klant abonnementen met behulp van de volgende ingebouwde beleids definities:

- Niet toegestane resourcetypen
- Toegestane brontypen

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)

- [Beheergroepen maken](../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: de inventaris van goedgekeurde Azure-resources definiëren en onderhouden

**Richt lijnen**: Maak een inventaris van goedgekeurde Azure-resources en goedgekeurde software voor reken resources conform uw organisatie behoeften.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

**Hulp**: gebruik Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in uw abonnementen. 

Gebruik Azure resource Graph om bronnen in hun abonnementen op te vragen en te detecteren.  Zorg ervoor dat alle Azure-resources die aanwezig zijn in de omgeving, zijn goedgekeurd. 

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md) 

- [Query's maken met Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: niet-goedgekeurde Azure-resources en software toepassingen verwijderen

**Hulp**: Azure-resources met betrekking tot Azure Security Center verwijderen wanneer ze niet meer nodig zijn als onderdeel van het inventaris-en controle proces van uw organisatie.

- [Azure-resource groep en-resource verwijderen](../azure-resource-manager/management/delete-resource-group.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="69-use-only-approved-azure-services"></a>6,9: alleen goedgekeurde Azure-Services gebruiken

**Hulp: gebruik** Azure Policy om beperkingen toe te voegen voor het type resources dat in uw abonnementen kan worden gemaakt met behulp van de volgende ingebouwde beleids definities:

- Niet toegestane resourcetypen
- Toegestane brontypen

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een specifiek resource type weigeren met Azure Policy](../governance/policy/samples/index.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

**Hulp** bij het configureren van voorwaardelijke toegang van Azure om gebruikers de mogelijkheid te bieden om te communiceren met Azure Resource Manager door ' blok toegang ' te configureren voor de app Microsoft Azure management.

- [Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager te blok keren](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [Azure Security Bench Mark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md)(Engelstalig) voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources

**Hulp**: Definieer en implementeer standaard beveiligings configuraties voor Azure Security Center en de gekoppelde werk ruimte via Azure Policy. Gebruik Azure Policy aliassen in de naam ruimten ' micro soft. OperationalInsights ' en ' micro soft. Security ' om aangepaste Azure Policy definities te maken om de configuratie van Security Center en de Log Analytics-werk ruimte te controleren of af te dwingen.

- [Beschik bare Azure Policy aliassen weer geven](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: Beveilig Azure-resource configuraties onderhouden

**Hulp**: gebruik Azure Policy effecten voor ' deny ' en ' indien niet aanwezig ' als u beveiligde instellingen wilt afdwingen voor uw Azure-resources. Daarnaast kunt u Azure Resource Manager sjablonen gebruiken om de beveiligings configuratie van uw Azure-resources te onderhouden die uw organisatie nodig heeft. 

- [Azure Policy effecten begrijpen](../governance/policy/concepts/effects.md) 

- [Beleidsregels voor het afdwingen van naleving maken en beheren](../governance/policy/tutorials/create-and-manage.md) 

- [Overzicht van Azure Resource Manager sjablonen](../azure-resource-manager/templates/overview.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan

**Hulp**: Azure DevOps gebruiken om uw code veilig op te slaan en te beheren, zoals aangepaste Azure Policy definities, Azure Resource Manager sjablonen en desired state Configuration-scripts. Als u toegang wilt krijgen tot de resources die u beheert in azure DevOps, kunt u machtigingen verlenen of weigeren aan specifieke gebruikers, ingebouwde beveiligings groepen of groepen die zijn gedefinieerd in Azure Active Directory (Azure AD) als deze zijn geïntegreerd met Azure DevOps, of Active Directory als dit is geïntegreerd met TFS. 

- [Code opslaan in azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops) 

- [Over machtigingen en groepen in azure DevOps](/azure/devops/organizations/security/about-permissions)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: hulpprogram ma's voor configuratie beheer voor Azure-resources implementeren

**Richt lijnen**: standaard beveiligings configuraties voor Azure-resources definiëren en implementeren met behulp van Azure Policy. Gebruik Azure Policy aliassen om aangepaste beleids regels te maken om de configuratie van uw Azure Security Center gerelateerde resources te controleren of af te dwingen. Daarnaast kunt u Azure Automation gebruiken om configuratie wijzigingen te implementeren. 

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md) 

- [Aliassen gebruiken](../governance/policy/concepts/definition-structure.md#aliases)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-resources implementeren

**Hulp**: gebruik ingebouwde Azure Policy definities en Azure Policy aliassen in de ' micro soft. OperationalInsights ' en ' micro soft. Beveiliging: naam ruimten voor het maken van aangepaste beleids regels om Azure-resource configuraties te Signa lering, te controleren en af te dwingen. Gebruik de Azure Policy-effecten ' audit ', ' deny ' en ' implementeren indien niet aanwezig ' om automatisch configuraties af te dwingen voor uw Azure-resources.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure-geheimen veilig beheren

**Hulp**: Azure Security Center gebruikt een geconfigureerde log Analytics-werk ruimte om de gegevens, waarschuwingen en aanbevelingen op te slaan die worden gegenereerd. Configureer een door de klant beheerde sleutel (CMK) voor de werk ruimte die u hebt geconfigureerd voor het verzamelen van Security Center gegevens. CMK maakt het mogelijk om alle gegevens die worden opgeslagen of verzonden naar de werk ruimte te versleutelen met een Azure Key Vault sleutel die u hebt gemaakt en waarvan u de eigenaar bent. 

- [Door de klant beheerde sleutel van Azure Monitor](../azure-monitor/platform/customer-managed-keys.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: onbedoelde referentie blootstelling elimineren

**Richt lijnen**: referentie scanner implementeren om referenties in code te identificeren. Referentie scanner stimuleert ook het verplaatsen van gedetecteerde referenties naar veiliger locaties, zoals Azure Key Vault.

- [Referentie scanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie voor meer informatie de [Azure Security Bench Mark: beveiliging tegen schadelijke software](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: scan bestanden die moeten worden geüpload naar niet-reken resources van Azure

**Hulp**: Azure Security Center is niet bedoeld om bestanden op te slaan of te verwerken. Het is uw verantwoordelijkheid om vooraf te scannen op inhoud die wordt geüpload naar niet-reken resources van Azure, waaronder Log Analytics-werk ruimte.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="data-recovery"></a>Gegevensherstel

*Zie [Azure Security Bench Mark: Data Recovery](../security/benchmarks/security-control-data-recovery.md)(Engelstalig) voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: controleren op regel matige automatische back-ups 

**Richt lijnen**: Volg een IAC-benadering (Infrastructure as code) en gebruik Azure Resource Manager om uw Azure Security Center gerelateerde resources te implementeren in een JavaScript object NOTATION (JSON)-sjabloon die kan worden gebruikt als back-up voor resource-gerelateerde configuraties.

- [Eén en meerdere resources exporteren naar een sjabloon in Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Azure Resource Manager sjablonen voor beveiligings bronnen](/azure/templates/microsoft.security/allversions)

- [Over Azure Automation](../automation/automation-intro.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: volledige back-ups van het systeem uitvoeren en een back-up maken van door de klant beheerde sleutels

**Hulp**: Azure Security Center gebruikt een log Analytics werk ruimte om de gegevens, waarschuwingen en aanbevelingen op te slaan die worden gegenereerd. U kunt Azure Monitor en de werk ruimte die Security Center gebruikt, configureren om een door de klant beheerde sleutel in te scha kelen. Als u een Key Vault gebruikt om uw door de klant beheerde sleutels op te slaan, moet u regel matig automatische back-ups van uw sleutels maken.

- [Back-ups maken van Key Vault sleutels](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richt lijnen**: Zorg ervoor dat u regel matig herstel kunt uitvoeren met Azure Resource Manager back-upsjabloon bestanden. Het herstellen van een back-up van door de klant beheerde sleutels testen.

- [Log Analytics-werk ruimte beheren met Azure Resource Manager sjablonen](../azure-monitor/samples/resource-manager-workspace.md)

- [Sleutel kluis sleutels herstellen in azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zorg voor de bescherming van back-ups en door de klant beheerde sleutels

**Hulp**: Azure DevOps gebruiken om uw code veilig op te slaan en te beheren, zoals aangepaste Azure Policy definities en Azure Resource Manager sjablonen. Als u resources wilt beveiligen die u beheert in azure DevOps, kunt u machtigingen verlenen of weigeren aan specifieke gebruikers, ingebouwde beveiligings groepen of groepen die zijn gedefinieerd in Azure Active Directory (Azure AD) als deze zijn geïntegreerd met Azure DevOps, of Active Directory als dit is geïntegreerd met TFS. Gebruik Azure op rollen gebaseerd toegangs beheer voor het beveiligen van sleutels die door de klant worden beheerd.

Daarnaast schakelt u Soft-Delete in Key Vault in om de beveiliging van sleutels te beschermen tegen onbedoelde of schadelijke verwijdering.  Als Azure Storage wordt gebruikt voor het opslaan van back-ups van Azure Resource Manager-sjablonen, schakelt u de optie zacht verwijderen in om uw gegevens op te slaan en te herstellen wanneer blobs of BLOB-moment opnamen worden verwijderd. 

- [Code opslaan in azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Over machtigingen en groepen in azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Soft-Delete inschakelen en beveiliging opschonen in Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal) 

- [Voorlopig verwijderen voor Azure Storage-blobs](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Bench Mark: Incident Response](../security/benchmarks/security-control-incident-response.md)(Engelstalig) voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10,1: een hand leiding voor reactie op incidenten maken

**Hulp**: een antwoord gids voor incidenten ontwikkelen voor uw organisatie. Zorg ervoor dat er schriftelijke incidenten abonnementen zijn die alle personeels rollen definiëren, evenals de fasen van het verwerken van incidenten en het beheer van de detectie tot een beoordeling van het incident. 

- [Richt lijnen voor het bouwen van uw eigen beveiligings incident antwoord proces](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Micro soft Security Response Center anatomie van een incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [De verwerkings gids voor het computer beveiligings incident van het NIST gebruiken om u te helpen bij het maken van uw eigen reactie plan voor incidenten](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: een beoordelings procedure voor incidenten en prioriteits procedures maken

**Hulp**: Azure Security Center wijst aan elke waarschuwing een Ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of het analyse programma dat wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau dat er schadelijke bedoelingen zijn achter de activiteit die tot de waarschuwing heeft geleid.

Markeer bovendien abonnementen met tags en maak een naamgevings systeem voor het identificeren en categoriseren van Azure-resources, met name voor de verwerking van gevoelige gegevens.  Het is uw verantwoordelijkheid om prioriteit te geven aan het herstel van waarschuwingen op basis van de ernst van de Azure-resources en-omgeving waar het incident heeft plaatsgevonden. 

- [Beveiligingswaarschuwingen in Azure Security Center](security-center-alerts-overview.md) 

- [Tags gebruiken om Azure-resources te organiseren](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="103-test-security-response-procedures"></a>10,3: procedures voor beveiligings antwoorden testen

**Richt lijnen**: oefent oefeningen uit om de respons mogelijkheden van uw systeem te testen op een reguliere uitgebracht om uw Azure-resources te beschermen. Identificeer zwakke punten en tussen ruimten en wijzig vervolgens uw reactie schema naar wens. 

- [Publicatie van het NIST-hand leiding voor het testen, trainen en uitoefenen van Program Ma's voor IT-plannen en-mogelijkheden](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen configureren voor beveiligings incidenten

**Hulp**: contact gegevens van beveiligings incidenten worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat uw gegevens zijn geopend door een onrecht matige of niet-gemachtigde partij. Bekijk incidenten na het feit om te controleren of de problemen zijn opgelost. 

- [De Azure Security Center Security-contact persoon instellen](security-center-provide-security-contact-details.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: beveiligings waarschuwingen opnemen in uw reactie systeem van uw incident

**Hulp**: exporteer uw Azure Security Center waarschuwingen en aanbevelingen met behulp van de functie continue export om Risico's voor Azure-resources te identificeren. Met doorlopend exporteren kunt u waarschuwingen en aanbevelingen hand matig of op een doorlopende manier exporteren. U kunt de Azure Security Center Data Connector gebruiken om de waarschuwingen naar Azure Sentinel te streamen. 

- [Continue export configureren](continuous-export.md) 

- [Waarschuwingen streamen naar Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: de reactie op beveiligings waarschuwingen automatiseren

**Hulp**: werk stroom automatisering gebruiken Azure Security Center om automatisch reacties te activeren op beveiligings waarschuwingen en aanbevelingen voor het beveiligen van uw Azure-resources. 

- [Werk stroom automatisering configureren in Security Center](workflow-automation.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie [Azure Security Bench Mark: Indringings tests en rode team oefeningen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)voor meer informatie.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: voert regel matig indringings tests van uw Azure-resources uit en zorgt voor herbemiddeling van alle essentiële beveiligings resultaten

**Richt lijnen**: Volg de stappen voor het testen van de Microsoft Cloud indringings regels om ervoor te zorgen dat uw indringings tests niet worden geschonden door het micro soft-beleid. Gebruik de strategie van micro soft en de uitvoering van de implementatie van de indringing van een live site in de Cloud, services en toepassingen die door micro soft worden beheerd. 

- [Regels van betrokkenheid voor het testen van indringing](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud rode teams](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: gedeeld

## <a name="next-steps"></a>Volgende stappen

- Zie de [Azure Security-Bench Mark](../security/benchmarks/overview.md)
- Meer informatie over [Azure-beveiligings basislijnen](../security/benchmarks/security-baselines-overview.md)