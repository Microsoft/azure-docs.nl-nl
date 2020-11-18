---
title: Azure-beveiligings basislijn voor Azure DevTest Labs
description: Azure-beveiligings basislijn voor Azure DevTest Labs
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 64e5ac46fbeb71706f7b1ae2d3682260e3cbc651
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843193"
---
# <a name="azure-security-baseline-for-azure-devtest-labs"></a>Azure-beveiligings basislijn voor Azure DevTest Labs

De Azure-beveiligings basislijn voor Azure DevTest Labs bevat aanbevelingen waarmee u de beveiligings postuur van uw implementatie kunt verbeteren.

De basis lijn voor deze service wordt opgehaald uit de [Azure Security Bench Mark-versie 1,0](../security/benchmarks/overview.md), die aanbevelingen biedt over hoe u uw cloud oplossingen kunt beveiligen in Azure.

Zie [overzicht van Azure Security-basis lijnen](../security/benchmarks/security-baselines-overview.md)voor meer informatie.

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie voor meer informatie [beveiligings beheer: logboek registratie en controle](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: goedgekeurde tijd synchronisatie bronnen gebruiken
**Hulp:** Micro soft onderhoudt tijd bronnen voor Azure-resources. U kunt echter tijd synchronisatie-instellingen voor uw reken resources beheren.

Zie het volgende artikel voor meer informatie over het configureren van tijd synchronisatie voor Azure Compute-resources: [tijd synchronisatie voor Windows-vm's in azure](../virtual-machines/windows/time-sync.md). 

**Azure Security Center bewaking:** Momenteel niet beschikbaar

**Verantwoordelijkheid:** Micro soft

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren
**Hulp:** Schakel Diagnostische instellingen voor Azure-activiteiten logboek in en verzend de logboeken naar een Log Analytics-werk ruimte, een Azure Event Hub of een Azure-opslag account voor archivering. Activiteiten logboeken bieden inzicht in de bewerkingen die zijn uitgevoerd op uw Azure DevTest Labs exemplaren op het niveau van het beheer vlak. Met Azure activiteiten logboek gegevens kunt u bepalen ' wat, wie en wanneer ' voor schrijf bewerkingen (PUT, POST, DELETE) die zijn uitgevoerd op het niveau van het beheer vlak voor uw DevTest Labs-instanties.

Zie voor meer informatie [Diagnostische instellingen maken voor het verzenden van platform logboeken en-metrische gegevens naar verschillende bestemmingen](../azure-monitor/platform/diagnostic-settings.md).

**Azure Security Center bewaking:** Momenteel niet beschikbaar

**Verantwoordelijkheid:** Gebruikers

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources
**Hulp:** Schakel Diagnostische instellingen voor Azure-activiteiten logboek in en verzend de logboeken naar een Log Analytics-werk ruimte, een Azure Event Hub of een Azure-opslag account voor archivering. Activiteiten logboeken bieden inzicht in de bewerkingen die zijn uitgevoerd op uw Azure DevTest Labs exemplaren op het niveau van het beheer vlak. Met Azure-activiteiten logboek gegevens kunt u de ' What, wie en wanneer ' bepalen voor schrijf bewerkingen (PUT, POST, DELETE) die zijn uitgevoerd op het niveau van het beheer vlak voor uw DevTest Labs-instanties.

Zie voor meer informatie [Diagnostische instellingen maken voor het verzenden van platform logboeken en-metrische gegevens naar verschillende bestemmingen](../azure-monitor/platform/diagnostic-settings.md).

**Azure Security Center bewaking:** Momenteel niet beschikbaar

**Verantwoordelijkheid:** Gebruikers

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: beveiligings logboeken verzamelen van besturings systemen
**Hulp:** Azure DevTest Labs virtuele machines (Vm's) worden gemaakt en het eigendom zijn van de klant. Het is dus de verantwoordelijkheid van de organisatie om deze te bewaken. U kunt Azure Security Center gebruiken om het Compute-besturings systeem te bewaken. Gegevens die worden verzameld door Security Center van het besturings systeem zijn onder andere besturingssysteem type en-versie, besturings systeem (Windows-gebeurtenis Logboeken), actieve processen, computer naam, IP-adressen en aangemelde gebruiker. De Log Analytics-agent verzamelt ook crash dump bestanden.

Raadpleeg voor meer informatie de volgende artikelen: 

- [Interne host-logboeken van de virtuele machine van Azure verzamelen met Azure Monitor](../azure-monitor/learn/quick-collect-azurevm.md)
- [Meer informatie over het verzamelen van Azure Security Center gegevens](../security-center/security-center-enable-data-collection.md)

**Azure Security Center bewaking:** Klikt

**Verantwoordelijkheid:** Gebruikers

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren
**_Hulp:_* Stel in Azure Monitor de Bewaar periode voor logboek registratie in voor Log Analytics werk ruimten die zijn gekoppeld aan uw Azure DevTest Labs-instanties volgens de nalevings voorschriften van uw organisatie.

Zie het volgende artikel: [para meters voor het bewaren van Logboeken instellen](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period) voor meer informatie.

**Azure Security Center bewaking:** Niet van toepassing

**Verantwoordelijkheid:** Gebruikers

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren
**Hulp:** Schakel Diagnostische instellingen voor Azure-activiteiten logboek in en verzend de logboeken naar een Log Analytics-werk ruimte. Voer query's uit in Log Analytics om zoek termen te zoeken, trends te identificeren, patronen te analyseren en veel andere inzichten te bieden op basis van de activiteiten logboek gegevens die mogelijk zijn verzameld voor Azure DevTest Labs.

Raadpleeg voor meer informatie de volgende artikelen:

- [Diagnostische instellingen voor Azure-activiteiten logboek inschakelen](../azure-monitor/platform/diagnostic-settings.md)
- [Azure-activiteiten logboeken verzamelen en analyseren in Log Analytics werk ruimte in Azure Monitor](../azure-monitor/platform/activity-log.md)

**Azure Security Center bewaking:** Niet van toepassing

**Verantwoordelijkheid:** Gebruikers

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteit
**Hulp:** Gebruik Azure Log Analytics-werk ruimte voor het bewaken en waarschuwen over afwijkende activiteiten in beveiligings logboeken en gebeurtenissen met betrekking tot uw Azure DevTest Labs.

Zie het volgende artikel voor meer informatie: een [waarschuwing geven over logboek gegevens van log Analytics](../azure-monitor/learn/tutorial-response.md)

**Azure Security Center bewaking:** Momenteel niet beschikbaar

**Verantwoordelijkheid:** Gebruikers

### <a name="28-centralize-anti-malware-logging"></a>2,8: registratie van anti-malware centraliseren
**Hulp:** Niet van toepassing. Azure DevTest Labs geen aan anti-malware gerelateerde logboeken verwerkt of produceert.

**Azure Security Center bewaking:** Niet van toepassing

**Verantwoordelijkheid:** Gebruikers

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-query logboek registratie inschakelen
**Hulp:** Niet van toepassing. Azure DevTest Labs geen aan DNS gerelateerde logboeken verwerkt of produceert.

**Azure Security Center bewaking:** Niet van toepassing

**Verantwoordelijkheid:** Gebruikers

### <a name="210-enable-command-line-audit-logging"></a>2,10: controle logboek registratie op opdracht regel inschakelen
**Hulp:** Azure DevTest Labs maakt Azure Compute-machines die eigendom zijn van en worden beheerd door de klant. Gebruik micro soft Monitoring Agent op alle ondersteunde Azure Windows-Vm's om de gebeurtenis voor het maken van processen en het veld te registreren `CommandLine` . Voor ondersteunde virtuele machines van Azure Linux kunt u hand matig console logboek registratie configureren op basis van per knoop punt en syslog gebruiken om de gegevens op te slaan. U kunt ook de werk ruimte Log Analytics van Azure Monitor gebruiken om logboeken te controleren en query's uit te voeren op geregistreerde gegevens van Azure-Vm's.

- [Gegevensverzameling in Azure Security Center](../security-center/security-center-enable-data-collection.md#data-collection-tier)
- [Aangepaste query's uitvoeren in Azure Monitor](../azure-monitor/log-query/get-started-queries.md)
- [Syslog-gegevensbronnen in Azure Monitor](../azure-monitor/platform/data-sources-syslog.md)

**Azure Security Center bewaking:** Klikt

**Verantwoordelijkheid:** Gebruikers

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer
*Zie [beveiligings beheer: identiteit en Access Control](../security/benchmarks/security-control-identity-access-control.md)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: een inventaris van beheerders accounts onderhouden
**Hulp:** Azure Active Directory (Azure AD) heeft ingebouwde rollen die expliciet moeten worden toegewezen en waarop query's kunnen worden doorzocht. Gebruik de Azure AD Power shell-module om ad-hoc-query's uit te voeren om accounts te detecteren die lid zijn van beheer groepen.

- [Een directory-rol verkrijgen in azure AD met Power shell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)
- [Leden van een directory-rol in azure AD ophalen met Power shell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)
- [Azure DevTest Labs rollen](devtest-lab-add-devtest-user.md)  

**Azure Security Center bewaking:** Klikt

**Verantwoordelijkheid:** Gebruikers

### <a name="32-change-default-passwords-where-applicable"></a>3,2: standaard wachtwoorden wijzigen indien van toepassing
**Hulp:** Azure Active Directory (Azure AD) beschikt niet over het concept van standaard wachtwoorden. Andere Azure-resources waarbij een wacht woord geforceerd moet worden gemaakt met behulp van complexiteits vereisten en een minimale wachtwoord lengte, wat afhankelijk is van de service. U bent zelf verantwoordelijk voor toepassingen van derden en Marketplace-services die standaard wachtwoorden gebruiken.

DevTest Labs heeft niet het concept van standaard wachtwoorden. 

**Azure Security Center bewaking:** Niet van toepassing

**Verantwoordelijkheid:** Gebruikers

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: speciale beheerders accounts gebruiken
**Hulp:** Maak standaard procedures voor het gebruik van specifieke beheerders accounts. Gebruik Azure Security Center identiteits-en toegangs beheer om het aantal beheerders accounts te bewaken.

Daarnaast kunt u aanbevelingen van Azure Security Center of ingebouwde Azure-beleids regels gebruiken om u te helpen bij het bijhouden van specifieke beheerders accounts, zoals:

- Er moet meer dan één eigenaar zijn toegewezen aan uw abonnement
- Afgeschafte accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement

- [Azure Security Center gebruiken om identiteit en toegang te bewaken (preview)](../security-center/security-center-identity-access.md)  
- [Azure Policy gebruiken](../governance/policy/tutorials/create-and-manage.md)
- [Azure DevTest Labs rollen](devtest-lab-add-devtest-user.md)  

**Azure Security Center bewaking:** Klikt

**Verantwoordelijkheid:** Gebruikers

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: eenmalige aanmelding (SSO) met Azure Active Directory gebruiken
**Hulp:** DevTest Labs maakt gebruik van de Azure AD-service voor identiteits beheer. Houd rekening met deze twee belang rijke aspecten wanneer u gebruikers toegang geeft tot een omgeving op basis van DevTest Labs:

- **Resource beheer:** Het biedt toegang tot de Azure Portal om resources te beheren (Vm's te maken, omgevingen te maken, te starten, te stoppen, opnieuw op te starten, te verwijderen en artefacten toe te passen). Resource beheer wordt uitgevoerd in azure met behulp van Azure RBAC (op rollen gebaseerd toegangs beheer). U wijst rollen toe aan gebruikers en stelt machtigingen voor de resource en toegangs niveau in.
- **Virtuele machines (netwerk niveau)**: in de standaard configuratie gebruikt vm's een lokaal beheerders account. Als er een domein beschikbaar is (Azure AD Domain Services, een on-premises domein of een domein in de Cloud), kunnen machines worden toegevoegd aan het domein. Gebruikers kunnen vervolgens hun domein identiteiten gebruiken met behulp van het samen voegen van het domein om verbinding te maken met de computers. 

- [Referentie architectuur voor DevTest Labs](devtest-lab-reference-architecture.md#architecture)
- [Informatie over eenmalige aanmelding met Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center bewaking:** Niet van toepassing

**Verantwoordelijkheid:** Gebruikers

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: multi-factor Authentication gebruiken voor alle op Azure Active Directory gebaseerde toegang
**Hulp:** Schakel Azure Active Directory (AD) Multi-Factor Authentication (MFA) in en volg Azure Security Center aanbevelingen voor identiteits-en toegangs beheer.

- [MFA inschakelen in azure](../active-directory/authentication/howto-mfa-getstarted.md)  
- [Identiteit en toegang bewaken in Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center bewaking:** _ Ja

_ *Verantwoordelijkheid:** klant


### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: gebruik speciale machines (privileged Access workstations) voor alle beheer taken
**Hulp:** Gebruik Paw's (privileged Access workstations) met MFA dat is geconfigureerd om Azure-resources aan te melden en te configureren.

- [Meer informatie over privileged Access workstations](/windows-server/identity/securing-privileged-access/privileged-access-workstations)  
- [MFA inschakelen in azure](../active-directory/authentication/howto-mfa-getstarted.md)  

**Azure Security Center bewaking:** n.v.t.

**Verantwoordelijkheid:** Gebruikers

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: logboek en waarschuwing voor verdachte activiteiten van beheerders accounts
**Hulp:** Gebruik Azure Active Directory (Azure AD) beveiligings rapporten voor het genereren van Logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten in de omgeving worden uitgevoerd. Gebruik Azure Security Center om identiteits-en toegangs activiteiten te bewaken.

- [Azure AD-gebruikers identificeren die zijn gemarkeerd voor Risk ante activiteiten](../active-directory/identity-protection/overview-identity-protection.md)  
- [Identiteits-en toegangs activiteiten van gebruikers controleren in Azure Security Center](../security-center/security-center-identity-access.md)  

**Azure Security Center bewaking:** Momenteel niet beschikbaar

**Verantwoordelijkheid:** Gebruikers

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure-resources alleen beheren vanaf goedgekeurde locaties
**Hulp:** Gebruik benoemde locaties voor voorwaardelijke toegang om alleen toegang toe te staan vanaf specifieke logische groepen met IP-adresbereiken of landen/regio's.

- [Benoemde locaties configureren in azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)  

**Azure Security Center bewaking:** Momenteel niet beschikbaar

**Verantwoordelijkheid:** Gebruikers

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory gebruiken
**Hulp:** Gebruik Azure Active Directory (Azure AD) als centrale verificatie-en autorisatie systeem. Azure AD beveiligt gegevens door gebruik te maken van sterke versleuteling voor gegevens in rust en onderweg. Azure AD bevat ook zouten, hashes en veilige gebruikers referenties.

- [Een Azure AD-exemplaar maken en configureren](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)  

**Azure Security Center bewaking:** Momenteel niet beschikbaar

**Verantwoordelijkheid:** Gebruikers

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regel matig gebruikers toegang controleren en afstemmen
**Hulp:** Azure Active Directory (Azure AD) biedt logboeken waarmee u verlopen accounts kunt detecteren. U kunt ook Azure Identity Access revisies gebruiken om groepslid maatschappen, toegang tot bedrijfs toepassingen en roltoewijzingen op efficiënte wijze te beheren. Gebruikers toegang kan regel matig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben.

- [Meer informatie over Azure AD-rapportage](../active-directory/reports-monitoring/overview-reports.md)  
- [Beoordelingen over Azure Identity Access gebruiken](../active-directory/governance/access-reviews-overview.md)  

**Azure Security Center bewaking:** Niet van toepassing

**Verantwoordelijkheid:** Gebruikers

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: controle pogingen om toegang te krijgen tot gedeactiveerde accounts
**Hulp:** U hebt toegang tot Azure Active Directory (Azure AD)-aanmeldings activiteiten, audits en risico logboek bronnen, waarmee u kunt integreren met elk Security Information and Event Management (SIEM)/monitoring-hulp programma.

U kunt dit proces stroom lijnen door Diagnostische instellingen voor Azure Active Directory gebruikers accounts te maken en de audit logboeken en aanmeldings logboeken te verzenden naar een Log Analytics-werk ruimte. U kunt waarschuwingen configureren binnen Log Analytics werk ruimte.

- [Azure-activiteiten logboeken integreren in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)  

**Azure Security Center bewaking:** Momenteel niet beschikbaar

**Verantwoordelijkheid:** Gebruikers

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: waarschuwing voor de afwijking van het aanmeldings gedrag van accounts
**Hulp:** Gebruik Azure Active Directory (Azure AD) risico-en identiteits beveiligings functies voor het configureren van automatische antwoorden op gedetecteerde verdachte acties die betrekking hebben op gebruikers identiteiten.

- [Risk ante aanmeldingen voor Azure AD weer geven](../active-directory/identity-protection/overview-identity-protection.md)  
- [Risico beleid voor identiteits beveiliging configureren en inschakelen](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)  

**Azure Security Center bewaking:** Momenteel niet beschikbaar

**Verantwoordelijkheid:** Gebruikers

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: micro soft biedt toegang tot relevante klant gegevens tijdens ondersteunings scenario's
**Hulp:** Klanten-lockbox wordt momenteel niet ondersteund voor Azure DevTest Labs.

- [Lijst met door Klanten-lockbox ondersteunde services](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability) 

**Azure Security Center bewaking:** Niet van toepassing

**Verantwoordelijkheid:** Gebruikers

## <a name="data-protection"></a>Gegevensbeveiliging
*Zie [beveiligings beheer: gegevens beveiliging](../security/benchmarks/security-control-data-protection.md)voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: een inventaris van gevoelige informatie onderhouden
**Hulp:** Gebruik Tags om Azure-resources te helpen bij het bijhouden of verwerken van gevoelige informatie.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center bewaking:** Niet van toepassing

**Verantwoordelijkheid:** Gebruikers

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: systemen isoleren die gevoelige informatie opslaan of verwerken
**Hulp:** Implementeer afzonderlijke abonnementen of beheer groepen voor ontwikkeling, testen en productie. Azure DevTest Labs exemplaren moeten worden gescheiden door het virtuele netwerk/subnet en op de juiste wijze worden gelabeld. 

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)
- [Beheer groepen maken](../governance/management-groups/create-management-group-portal.md)
- [Een virtueel netwerk configureren voor DevTest Labs](devtest-lab-configure-vnet.md)
- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)
- [Tags maken en gebruiken voor DevTest Labs](devtest-lab-add-tag.md)

**Azure Security Center bewaking:** Momenteel niet beschikbaar

**Verantwoordelijkheid:** Gebruikers

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: niet-geautoriseerde overdracht van gevoelige gegevens controleren en blok keren
**Hulp:** Nog niet beschikbaar; de functies voor gegevens identificatie, classificatie en verlies preventie zijn nog niet beschikbaar voor Azure DevTest Labs.

Micro soft beheert de onderliggende infra structuur voor Azure DevTest Labs en heeft strikte controles geïmplementeerd om verlies of bloot stelling van klant gegevens te voor komen.

- [Informatie over beveiliging van klanten in azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center bewaking:** Momenteel niet beschikbaar

**Verantwoordelijkheid:** Share

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: alle gevoelige gegevens in de overdracht versleutelen
**Hulp:** Voor Azure DevTest Labs is standaard TLS-versleutelde communicatie vereist. TLS-versies 1,2 wordt momenteel ondersteund. Als uw client bibliotheek of-hulp programma geen TLS ondersteunt, kunnen niet-versleutelde verbindingen worden uitgevoerd via de Azure Portal-of beheer-Api's. In dergelijke gevallen is het raadzaam om een lab-en client toepassing in een virtueel netwerk te plaatsen, omdat er geen versleutelde verbindingen mogelijk zijn.

[Informatie over versleuteling in transit scenario voor DevTest Labs](https://techcommunity.microsoft.com/t5/azure-developer-community-blog/azure-devtest-labs-enforcing-tls-1-2-starting-may-01-2020/ba-p/1236279)

**Azure Security Center bewaking:** Klikt

**Verantwoordelijkheid:** Share

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: een actief detectie hulpprogramma gebruiken om gevoelige gegevens te identificeren
**Hulp:** De functies voor gegevens identificatie, classificatie en verlies preventie zijn nog niet beschikbaar voor Azure DevTest Labs. Label instanties die gevoelige informatie bevatten als zodanig, en het implementeren van oplossingen van derden, indien nodig voor nalevings doeleinden.

Voor het onderliggende platform, dat wordt beheerd door micro soft, behandelt micro soft alle inhoud van de klant als gevoelig en gaat het om tot een uitstekende lengte van het verlies en de bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

- [Informatie over beveiliging van klanten in azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center bewaking:** Momenteel niet beschikbaar

**Verantwoordelijkheid:** Gebruikers

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Azure RBAC gebruiken om de toegang tot resources te beheren
**Hulp:** Gebruik Azure RBAC (op rollen gebaseerd toegangs beheer) voor het beheren van de toegang tot Labs in Azure DevTest Labs.

- [Azure RBAC configureren](../role-based-access-control/role-assignments-portal.md)
- [Informatie over rollen in DevTest Labs](devtest-lab-add-devtest-user.md)

**Azure Security Center bewaking:** Niet van toepassing

**Verantwoordelijkheid:** Gebruikers

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: voor komen dat gegevens verlies op basis van host wordt gebruikt voor het afdwingen van toegangs beheer
**Hulp:** Als dat nodig is voor de naleving van de reken resources die zijn gemaakt als onderdeel van DevTest Labs, implementeert u een hulp programma van derden, zoals een geautomatiseerde oplossing voor gegevens verlies op basis van een host voor het afdwingen van toegangs beheer voor gegevens, zelfs wanneer gegevens worden gekopieerd uit een systeem.

Voor het onderliggende platform, dat wordt beheerd door micro soft, behandelt micro soft alle inhoud van de klant als gevoelig en gaat het om tot een uitstekende lengte van het verlies en de bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

- [Informatie over beveiliging van klanten in azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center bewaking:** Niet van toepassing

**Verantwoordelijkheid:** Niet van toepassing

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: gevoelige informatie op rest versleutelen
**Hulp:** Azure DevTest Labs de volgende klant gegevens worden opgeslagen:

- [Artefact resultaten](add-artifact-vm.md) die implementatie-en extensie logboeken bevatten die zijn gegenereerd op basis van het Toep assen van artefacten
- [Formule documenten](devtest-lab-manage-formulas.md) die worden gebruikt voor het maken van virtuele machines uit formules
- Besturings systeem en gegevens schijven voor virtuele lab-machines 

Artefact resultaten en formule documenten worden verzonden naar een Azure Storage-account dat is gemaakt als onderdeel van elke Lab-implementatie. Gegevens in Azure Storage worden transparant versleuteld en ontsleuteld met 256-bits AES-versleuteling, een van de krach tigste blok cijfers die beschikbaar zijn en is compatibel met FIPS 140-2. Azure Storage versleuteling kan niet worden uitgeschakeld. U kunt gebruikmaken van door micro soft beheerde sleutels voor de versleuteling van uw opslag account, of u kunt versleuteling beheren met uw eigen sleutels. Zie [versleuteling voor Lab Storage-account](encrypt-storage.md)voor meer informatie.

Standaard zijn alle besturings systeem-en gegevens schijven voor Lab versleuteld met een door het platform beheerde sleutel. Alle beheerde schijven, moment opnamen, installatie kopieën en gegevens die naar bestaande beheerde schijven worden geschreven, worden automatisch versleuteld met door het platform beheerde sleutels. Als eigenaar van het lab kunt u de schijven van het lab-besturings systeem configureren om te worden versleuteld met een door de klant beheerde sleutel. Versleuteling met behulp van een door de klant beheerde sleutel voor Lab-gegevens schijven kan momenteel niet worden geconfigureerd via het lab zelf. Een abonnements beheerder kan deze instelling echter voor Lab-schijven binnen een abonnement nu configureren. Zie voor meer informatie het [versleutelen van Lab DevTest Labs-besturingssysteem schijven met door de klant beheerde sleutels](encrypt-disks-customer-managed-keys.md).

**Azure Security Center bewaking:** Niet van toepassing

**Verantwoordelijkheid:** Share

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen
**Hulp:** Gebruik Azure Monitor met het Azure-activiteiten logboek om waarschuwingen te maken voor wanneer wijzigingen worden aangebracht in DevTest Labs-instanties en andere kritieke of verwante resources.

- [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteiten logboek](../azure-monitor/platform/alerts-activity-log.md)
- [Waarschuwingen maken voor activiteiten logboek gebeurtenissen van DevTest Labs](create-alerts.md)

**Azure Security Center bewaking:** Niet van toepassing

**Verantwoordelijkheid:** Gebruikers



## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen
*Zie [beveiligings beheer: beveiligingslek beheer](../security/benchmarks/security-control-vulnerability-management.md)voor meer informatie.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatische hulpprogram ma's voor het scannen van beveiligings problemen uitvoeren
**Hulp:** Volg de aanbevelingen van Azure Security Center over het beveiligen van uw Azure DevTest Labs-instanties en gerelateerde bronnen.

Micro soft voert beveiligings beheer uit op de onderliggende resources die Azure DevTest Labs ondersteunen.

- [Azure Security Center aanbevelingen begrijpen](../security-center/recommendations-reference.md) 

**Azure Security Center bewaking:** Klikt

**Verantwoordelijkheid:** Share

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: geautomatiseerde oplossing voor patch beheer voor besturings systemen implementeren
**Hulp:** Gebruik Azure Updatebeheer om ervoor te zorgen dat de meest recente beveiligings updates worden geïnstalleerd op uw Windows-en Linux-Vm's die worden gehost in DevTest Labs. Zorg ervoor dat Windows Update is ingeschakeld en is ingesteld om automatisch te worden bijgewerkt voor virtuele Windows-machines. Deze instelling is momenteel niet beschikbaar voor configuratie via DevTest Labs, maar de beheerder van de Lab-beheerder/abonnement kan deze instelling ook configureren op de onderliggende Compute-Vm's in hun abonnement. 

- [Updatebeheer configureren voor virtuele machines in azure](../automation/update-management/update-mgmt-overview.md)
- [Meer informatie over Azure-beveiligings beleid bewaakt door Security Center](../security-center/policy-reference.md)

**Azure Security Center bewaking:** Niet van toepassing

**Verantwoordelijkheid:** Gebruikers

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: Implementeer een geautomatiseerde oplossing voor software patch beheer van derden
**_Richt lijnen:_* _ als test beheerder kunt u [DevTest Labs-artefacten](add-artifact-vm.md) gebruiken om updates te automatiseren voor aangepaste Lab-installatie kopieën, waaronder beveiligings patches en andere updates. 

Meer informatie over [Image Factory in DevTest Labs](image-factory-create.md), een oplossing voor configuratie-as-code waarmee automatisch installatie kopieën worden gebouwd en gedistribueerd op regel matige basis met alle gewenste configuraties. 

Als abonnements beheerder kunt u ook de Azure Updatebeheer-oplossing gebruiken om updates en patches voor DevTest Labs-Vm's te beheren. Updatebeheer is afhankelijk van de lokaal geconfigureerde update opslagplaats voor patches die worden ondersteund door Windows-systemen. Met hulpprogram ma's als System Center Updates Publisher (updates Publisher) kunt u aangepaste updates publiceren in Windows Server Update Services (WSUS). Met dit scenario kunnen Updatebeheer patches voor machines die gebruikmaken van Configuration Manager als update opslagplaats met software van derden.

- [Updatebeheer oplossing in azure](../automation/update-management/update-mgmt-overview.md)
- [Updates en patches voor uw virtuele machines beheren](../automation/update-management/update-mgmt-overview.md)

_ *Azure Security Center bewaking:** niet van toepassing

**Verantwoordelijkheid:** Gebruikers

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: vergelijken van back-to-back-problemen
**Hulp:** Scan resultaten worden met consistente intervallen uitgevoerd en vergelijken de resultaten om te controleren of de beveiligings problemen zijn opgelost. Bij het gebruik van aanbevelingen voor beveiligings beheer die worden voorgesteld door Azure Security Center, kan de klant in de portal van de geselecteerde oplossing draaien om historische scan gegevens weer te geven.

**Azure Security Center bewaking:** Niet van toepassing

**Verantwoordelijkheid:** Gebruikers

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: een risico classificatie proces gebruiken om prioriteit te geven aan het herstel van ontdekte beveiligings problemen
**Hulp:** Gebruik de standaard risico classificaties (beveiligde Score) van Azure Security Center.

- [Azure Security Center beveiligde Score begrijpen](../security-center/secure-score-security-controls.md)

**Azure Security Center bewaking:** Klikt

**Verantwoordelijkheid:** Gebruikers

## <a name="inventory-and-asset-management"></a>Inventarisatie en asset-management
*Zie voor meer informatie [beveiligings beheer: inventarisatie en activa beheer](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatische Asset-detectie oplossing gebruiken
**Hulp:** Gebruik Azure resource Graph om alle resources (inclusief DevTest Labs-resources) in uw abonnementen te doorzoeken en te detecteren. Zorg ervoor dat u de juiste (Lees-) machtigingen hebt in uw Tenant en dat u alle Azure-abonnementen en-resources in uw abonnementen kunt inventariseren.

- [Query's maken met Azure Graph](../governance/resource-graph/first-query-portal.md)
- [Uw Azure-abonnementen weer geven](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)
- [Meer informatie over Azure RBAC](../role-based-access-control/overview.md)

**Azure Security Center bewaking:** Niet van toepassing

**Verantwoordelijkheid:** Gebruikers

### <a name="62-maintain-asset-metadata"></a>6,2: meta gegevens van activa onderhouden
**Hulp:** Pas Tags toe op Azure-resources die meta gegevens geven om ze logisch te organiseren op basis van een taxonomie.

- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)
- [Tags voor DevTest Labs configureren](devtest-lab-add-tag.md)

**Azure Security Center bewaking:** Niet beschikbaar

**Verantwoordelijkheid:** Gebruikers

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: niet-geautoriseerde Azure-resources verwijderen
**Hulp:** Gebruik Tags, beheer groepen en afzonderlijke abonnementen, en indien nodig, afzonderlijke Labs om Labs-en Lab-gerelateerde resources te organiseren en bij te houden. U kunt de inventaris regel matig afstemmen en ervoor zorgen dat niet-geautoriseerde resources snel worden verwijderd uit het abonnement.

- [Aanvullende Azure-abonnementen maken](../cost-management-billing/manage/create-subscription.md)
- [Beheergroepen maken](../governance/management-groups/create-management-group-portal.md)
- [Een lab maken met behulp van DevTest Labs](devtest-lab-create-lab.md)
- [Tags maken en gebruiken](../azure-resource-manager/management/tag-resources.md)
- [Tags voor een lab configureren](devtest-lab-add-tag.md)

**Azure Security Center bewaking:** Niet van toepassing

**Verantwoordelijkheid:** Gebruikers

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: de inventaris van goedgekeurde Azure-resources definiëren en onderhouden
**Hulp:** Maak een inventaris van goedgekeurde Azure-resources en goedgekeurde software voor reken bronnen conform de behoeften van de organisatie. Als abonnements beheerder kunt u ook gebruikmaken van adaptieve toepassings besturings elementen, een functie van Azure Security Center om u te helpen bij het definiëren van een set toepassingen die mogen worden uitgevoerd op geconfigureerde groepen test machines. Deze functie is beschikbaar voor zowel Azure-als niet-Azure-Windows (alle versies, klassieke of Azure Resource Manager) en Linux-machines.

- [Adaptief toepassings beheer inschakelen](../security-center/security-center-adaptive-application.md)
 
**Azure Security Center bewaking:** Niet-toepasselijke **verantwoordelijkheid:** klant

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources
**Hulp:** Gebruik Azure Policy om beperkingen toe te voegen aan het type resources dat kan worden gemaakt in klant abonnement (en) met de volgende ingebouwde beleids definities:

- Niet toegestane resourcetypen
- Toegestane brontypen

Gebruik ook de resource grafiek van Azure voor het opvragen/detecteren van resources binnen een of meer abonnementen. Dit kan handig zijn in omgevingen met hoge beveiliging, zoals die met opslag accounts.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)
- [Query's maken met Azure Graph](../governance/resource-graph/first-query-portal.md)

**Azure Security Center bewaking:** Niet van toepassing

**Verantwoordelijkheid:** Gebruikers

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitor voor niet-goedgekeurde software toepassingen binnen reken resources
**Hulp:** Azure Automation biedt volledige controle tijdens de implementatie, bewerkingen en het buiten gebruik stellen van werk belastingen en resources. Als abonnements beheerder kunt u Azure virtual machine Inventory gebruiken om het verzamelen van informatie over alle software op virtuele machines van DevTest Labs in uw abonnement te automatiseren. De eigenschappen software naam, versie, uitgever en vernieuwings tijd zijn beschikbaar via de Azure Portal. Om toegang te krijgen tot de installatie datum en andere informatie, is de klant verplicht om diagnostische gegevens op gast niveau in te scha kelen en de Windows-gebeurtenis logboeken naar een Log Analytics-werk ruimte te brengen.

Naast het gebruik van Wijzigingen bijhouden voor het bewaken van software toepassingen, kunnen besturings elementen voor adaptieve toepassingen in Azure Security Center machine learning gebruiken om de toepassingen te analyseren die worden uitgevoerd op uw computers en een acceptatie lijst te maken van deze intelligentie. Deze mogelijkheid vereenvoudigt het proces van het configureren en onderhouden van beleids regels voor het toestaan van toepassingen, zodat ongewenste software in uw omgeving kan worden gebruikt. U kunt de controle modus of de afdwingings modus configureren. In de controle modus wordt alleen de activiteit op de beveiligde Vm's gecontroleerd. Met de afdwingings modus worden de regels afgedwongen en wordt ervoor gezorgd dat toepassingen die niet mogen worden uitgevoerd, worden geblokkeerd. 

- [Een inleiding tot Azure Automation](../automation/automation-intro.md)
- [Azure VM-inventaris inschakelen](../automation/automation-tutorial-installed-software.md)
- [Meer informatie over adaptieve toepassings besturings elementen](../security-center/security-center-adaptive-application.md)

**Azure Security Center bewaking:** Niet van toepassing

**Verantwoordelijkheid:** Gebruikers


### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: niet-goedgekeurde Azure-resources en software toepassingen verwijderen
**Hulp:** Azure Automation biedt volledige controle tijdens de implementatie, bewerkingen en het buiten gebruik stellen van werk belastingen en resources. Als abonnements beheerder kunt u Wijzigingen bijhouden gebruiken om alle software te identificeren die is geïnstalleerd op Vm's die worden gehost in DevTest Labs. U kunt uw eigen proces implementeren of de configuratie van Azure Automation status gebruiken voor het verwijderen van onbevoegde software.

- [Een inleiding tot Azure Automation](../automation/automation-intro.md)
- [Wijzigingen in uw omgeving bijhouden met de Wijzigingen bijhouden oplossing](../automation/change-tracking/overview.md)
- [Overzicht van Azure Automation status configuratie](../automation/automation-dsc-overview.md)

**Azure Security Center bewaking:** Niet beschikbaar

**Verantwoordelijkheid:** Gebruikers

### <a name="68-use-only-approved-applications"></a>6,8: alleen goedgekeurde toepassingen gebruiken
**Hulp:** Als abonnements beheerder kunt u Azure Security Center adaptieve toepassings besturings elementen gebruiken om ervoor te zorgen dat alleen geautoriseerde software wordt uitgevoerd en alle niet-geautoriseerde software wordt geblokkeerd voor het uitvoeren van virtuele Azure-machines die worden gehost in DevTest Labs.

- [Azure Security Center adaptieve toepassings besturings elementen gebruiken](../security-center/security-center-adaptive-application.md)

**Azure Security Center bewaking:** Klikt

**Verantwoordelijkheid:** Gebruikers

### <a name="69-use-only-approved-azure-services"></a>6,9: alleen goedgekeurde Azure-Services gebruiken
**Hulp:** Gebruik Azure Policy om beperkingen toe te voegen aan het type resources dat kan worden gemaakt in klant abonnement (en) met de volgende ingebouwde beleids definities: 

- Niet toegestane resourcetypen
- Toegestane brontypen

Zie de volgende artikelen: 
- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)
- [Een specifiek resource type weigeren met Azure Policy](../governance/policy/samples/index.md)

**Azure Security Center bewaking:** Klikt

**Verantwoordelijkheid:** Gebruikers


### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: een inventaris van goedgekeurde software titels onderhouden
**Hulp:** Adaptief toepassings beheer is een intelligente, geautomatiseerde, end-to-end oplossing van Azure Security Center, waarmee u kunt bepalen welke toepassingen kunnen worden uitgevoerd op uw Azure-en niet-Azure-machines (Windows en Linux), die worden gehost in DevTest Labs. Opmerking: u moet een abonnements beheerder zijn om deze instelling te configureren voor de onderliggende reken resources die worden gehost in DevTest Labs. Implementeer oplossingen van derden als deze instelling niet voldoet aan de vereisten van uw organisatie.

- [Azure Security Center adaptieve toepassings besturings elementen gebruiken](../security-center/security-center-adaptive-application.md)

**Azure Security Center bewaking:** Niet van toepassing

**Verantwoordelijkheid:** Gebruikers

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken
**Hulp:** Gebruik voorwaardelijke toegang van Azure om gebruikers de mogelijkheid te bieden om te communiceren met Azure Resource Manager door het configureren van **toegang voor blok keren*** * voor de **Microsoft Azure beheer** -app.

- [Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager te blok keren](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center bewaking:** Klikt

**Verantwoordelijkheid:** Gebruikers

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: de mogelijkheid van gebruikers om scripts uit te voeren binnen reken bronnen beperken
**Hulp:** Afhankelijk van het type scripts kunt u specifieke configuraties van het besturings systeem of bronnen van derden gebruiken om de mogelijkheid van gebruikers om scripts uit te voeren binnen de Vm's die in DevTest Labs worden gehost, te beperken. U kunt ook Azure Security Center adaptieve toepassings besturings elementen gebruiken om ervoor te zorgen dat alleen geautoriseerde software wordt uitgevoerd en alle niet-geautoriseerde software wordt geblokkeerd voor het uitvoeren van de onderliggende Azure-Vm's.

- [De uitvoering van Power shell-scripts beheren in Windows-omgevingen](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)
- [Azure Security Center adaptieve toepassings besturings elementen gebruiken](../security-center/security-center-adaptive-application.md)

**Azure Security Center bewaking:** Niet beschikbaar

**Verantwoordelijkheid:** Gebruikers


### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: toepassingen met een hoog risico fysiek of logisch gescheiden
**Hulp:** Toepassingen met een hoog risico die zijn geïmplementeerd in uw Azure-omgeving kunnen worden geïsoleerd met virtuele netwerken, subnetten, abonnementen, beheer groepen, enzovoort. en voldoende beveiligd met een Azure Firewall, Web Application firewall (WAF) of een netwerk beveiligings groep (NSG).

- [Virtueel netwerk voor DevTest Labs configureren](devtest-lab-configure-vnet.md)
- [Overzicht van Azure Firewall](../firewall/overview.md)
- [Overzicht van Web Application firewall](../web-application-firewall/overview.md)
- [Overzicht van netwerkbeveiliging](../virtual-network/network-security-groups-overview.md)
- [Overzicht van Azure Virtual Network]()
- [Uw resources organiseren met Azure-beheergroepen](../governance/management-groups/overview.md)
- [Handleiding voor beslissingen over abonnementen](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Azure Security Center bewaking:** Niet beschikbaar

**Verantwoordelijkheid:** Gebruikers

## <a name="secure-configuration"></a>Veilige configuratie
**Zie Beveiligings beheer: beveiligde configuratie voor meer informatie.**

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources
**Hulp:** Gebruik Azure Policy aliassen om aangepaste beleids regels te maken om de configuratie van uw Azure-resources die zijn gemaakt als onderdeel van DevTest Labs te controleren of af te dwingen. U kunt ook ingebouwde Azure Policy definities gebruiken.

Azure Resource Manager heeft ook de mogelijkheid om de sjabloon in JavaScript Object Notation (JSON) te exporteren, die moet worden gecontroleerd om ervoor te zorgen dat de configuraties voldoen aan de beveiligings vereisten voor uw organisatie of deze overschrijden.

U kunt ook aanbevelingen van Azure Security Center gebruiken als een veilige configuratie basislijn voor uw Azure-resources.

- [Beschik bare Azure Policy aliassen weer geven](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)
- [Zelfstudie: Beleidsregels voor het afdwingen van naleving maken en beheren](../governance/policy/tutorials/create-and-manage.md)
- [Eén en meerdere resources exporteren naar een sjabloon in Azure Portal](../azure-resource-manager/templates/export-template-portal.md)
- [Aanbevelingen voor beveiliging: een naslaggids](../security-center/recommendations-reference.md)

**Azure Security Center bewaking:** Niet van toepassing

**Verantwoordelijkheid:** Gebruikers

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: veilige configuraties van besturings systemen instellen
**Hulp:** Gebruik Azure Security Center aanbevelingen voor het onderhouden van beveiligings configuraties op alle onderliggende reken resources die zijn gemaakt als onderdeel van DevTest Labs. Daarnaast kunt u aangepaste installatie kopieën van het besturings systeem of de Azure Automation status configuratie of DevTest Labs-artefacten gebruiken om de beveiligings configuratie te bepalen van het besturings systeem dat uw organisatie vereist.

- [Azure Security Center aanbevelingen bewaken](../security-center/security-center-recommendations.md)
- [Aanbevelingen voor beveiliging: een naslaggids](../security-center/recommendations-reference.md)
- [Overzicht van Azure Automation status configuratie](../automation/automation-dsc-overview.md)
- [Een VHD uploaden en gebruiken om nieuwe Windows-Vm's in azure te maken](../virtual-machines/windows/upload-generalized-managed.md)
- [Een virtuele Linux-machine maken op basis van een aangepaste schijf met de Azure CLI](../virtual-machines/linux/upload-vhd.md)
- [Aangepaste installatie kopieën maken en distribueren naar meerdere DevTest Labs](image-factory-save-distribute-custom-images.md)

**Azure Security Center bewaking:** Niet van toepassing

**Verantwoordelijkheid:** Niet van toepassing

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: Beveilig Azure-resource configuraties onderhouden
**Hulp:** Gebruik Azure Policy **weigeren** en **implementeren als er geen regels bestaan** voor het afdwingen van beveiligde instellingen voor uw Azure-resources die zijn gemaakt als onderdeel van DevTest Labs. U kunt ook Azure Resource Manager sjablonen gebruiken om de beveiligings configuratie van uw Azure-resources te onderhouden die uw organisatie vereist.

- [Azure Policy effecten begrijpen](../governance/policy/concepts/effects.md)
- [Beleidsregels voor het afdwingen van naleving maken en beheren](../governance/policy/tutorials/create-and-manage.md)
- [Overzicht van Azure Resource Manager sjablonen](../azure-resource-manager/templates/overview.md)

**Azure Security Center bewaking:** Niet van toepassing

**Verantwoordelijkheid:** Gebruikers

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: veilige configuraties van besturings systemen onderhouden
**Hulp:** Volg de aanbevelingen van Azure Security Center over het uitvoeren van beveiligings evaluaties op uw onderliggende Azure Compute-resources die zijn gemaakt als onderdeel van een lab. U kunt ook Azure Resource Manager-sjablonen, aangepaste installatie kopieën van besturings systemen of de configuratie van Azure Automation status gebruiken om de beveiligings configuratie van het besturings systeem te onderhouden dat door uw organisatie wordt vereist. U kunt ook de oplossing image Factory gebruiken. Dit is een oplossing voor configuratie-as-code die automatisch een installatie kopie bouwt en distribueert met alle gewenste configuraties.

Azure Marketplace-installatie kopieën voor virtuele machines die zijn gepubliceerd door micro soft worden ook beheerd en onderhouden door micro soft.

- [Aanbevelingen voor de evaluatie van Azure Security Center-beveiligings problemen implementeren](../security-center/deploy-vulnerability-assessment-vm.md)
- [Overzicht van Azure Automation status configuratie](../automation/automation-dsc-overview.md)
- [Voorbeeld van een script voor het uploaden van een VHD naar Azure om een nieuwe VM te maken](../virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script.md)
- [Een image Factory maken in DevTest Labs](image-factory-create.md)

**Azure Security Center bewaking:** Klikt

**Verantwoordelijkheid:** Share

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan
**Hulp:** Gebruik Azure DevOps om uw code veilig op te slaan en te beheren, zoals aangepaste Azure-beleids regels, Azure Resource Manager sjablonen en desired state Configuration-scripts. Als u toegang wilt krijgen tot de resources die u beheert in azure DevOps, kunt u machtigingen verlenen of weigeren aan specifieke gebruikers, ingebouwde beveiligings groepen of groepen die zijn gedefinieerd in Azure Active Directory (Azure AD) als deze zijn geïntegreerd met Azure DevOps.

- [Azure opslag plaatsen Git-zelf studie](/azure/devops/repos/git/gitworkflow)
- [Over machtigingen en groepen](/azure/devops/organizations/security/about-permissions?tabs=preview-page&view=azure-devops)
- [Integratie tussen Azure DevTest Labs en de Azure DevOps-werk stroom](devtest-lab-dev-ops.md)

**Azure Security Center bewaking:** Niet van toepassing

**Verantwoordelijkheid:** Gebruikers

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: aangepaste installatie kopieën van een besturings systeem veilig opslaan
**Hulp:** Als u aangepaste installatie kopieën gebruikt, kunt u Azure RBAC (op rollen gebaseerd toegangs beheer) gebruiken om ervoor te zorgen dat alleen geautoriseerde gebruikers toegang hebben tot de installatie kopieën. Met behulp van een galerie met gedeelde afbeeldingen kunt u uw installatie kopieën delen naar specifieke Labs die dat nodig heeft. Voor container installatie kopieën kunt u deze opslaan in Azure Container Registry en Azure RBAC gebruiken om ervoor te zorgen dat alleen geautoriseerde gebruikers toegang hebben tot de installatie kopieën.

- [Meer informatie over Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md)
- [Azure RBAC configureren](../role-based-access-control/quickstart-assign-role-user-portal.md)
- [Een galerie met gedeelde installatie kopieën configureren voor een DevTest Labs](configure-shared-image-gallery.md)
- [Meer informatie over Azure RBAC voor Container Registry](../container-registry/container-registry-roles.md)

**Azure Security Center bewaking:** Niet van toepassing

**Verantwoordelijkheid:** Gebruikers

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: hulpprogram ma's voor het beheer van systeem configuratie implementeren
**Hulp:** Definieer en implementeer standaard beveiligings configuraties voor Azure-resources met behulp van Azure Policy. Gebruik Azure Policy aliassen om aangepaste beleids regels te maken om de netwerk configuratie van uw Azure-resources die zijn gemaakt onder DevTest Labs te controleren of af te dwingen. U kunt ook gebruikmaken van ingebouwde beleids definities die betrekking hebben op uw specifieke resources. Daarnaast kunt u Azure Automation gebruiken om configuratie wijzigingen te implementeren.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)
- [Aliassen gebruiken](../governance/policy/concepts/definition-structure.md#aliases)

**Azure Security Center bewaking:** Niet van toepassing

**Verantwoordelijkheid:** Gebruikers

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: hulpprogram ma's voor het beheer van systeem configuratie implementeren voor besturings systemen
**Hulp:** Azure Automation status configuratie is een configuratie beheer service voor DSC-knoop punten (desired state Configuration) in elke Cloud of on-premises Data Center. U kunt eenvoudig computers onboarden, de declaratieve configuraties toewijzen en rapporten weer geven met de naleving van elke computer die u hebt opgegeven. U kunt ook een aangepast artefact schrijven dat op elke test machine kan worden geïnstalleerd om ervoor te zorgen dat ze het organisatie beleid volgen. 

- [Onboarding van machines voor beheer door Azure Automation status configuratie](../automation/automation-dsc-onboarding.md)
- [Aangepaste artefacten maken voor virtuele machines van DevTest Labs](devtest-lab-artifact-author.md)

**Azure Security Center bewaking:** Niet van toepassing

**Verantwoordelijkheid:** Gebruikers

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-Services implementeren
**Hulp:** Gebruik Azure Security Center om basislijn scans uit te voeren voor uw Azure-resources die zijn gemaakt onder DevTest Labs. Gebruik Azure Policy bovendien om een waarschuwing te krijgen en Azure-resource configuraties te controleren.

- [Aanbevelingen herstellen in Azure Security Center](../security-center/security-center-remediate-recommendations.md)
 
**Azure Security Center bewaking:** Niet van toepassing

**Verantwoordelijkheid:** Gebruikers

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: geautomatiseerde configuratie bewaking voor besturings systemen implementeren
**Hulp:** Gebruik Azure Security Center voor het uitvoeren van basislijn scans voor OS-en docker-instellingen voor containers.

- [Aanbevelingen van Azure Security Center voor containers](../security-center/container-security.md)

**Azure Security Center bewaking:** Niet van toepassing

**Verantwoordelijkheid:** Gebruikers

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure-geheimen veilig beheren
**Hulp:** Gebruik Managed Service Identity in combi natie met Azure Key Vault om het geheim beheer voor uw Cloud toepassingen te vereenvoudigen en te beveiligen.

- [Configureer beheerde identiteit voor de implementatie van Azure Resource Manager omgevingen in DevTest Labs](use-managed-identities-environments.md)
- [Beheerde identiteit configureren voor het implementeren van virtuele machines in DevTest Labs](enable-managed-identities-lab-vms.md)
- [Een sleutel kluis maken](../key-vault/general/quick-create-portal.md)
- [Verifiëren bij Key Vault](../key-vault/general/authentication.md)
- [Toegangs beleid voor Key Vault toewijzen](../key-vault/general/assign-access-policy-portal.md)

**Azure Security Center bewaking:** Klikt

**Verantwoordelijkheid:** Gebruikers

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: identiteiten veilig en automatisch beheren
**Hulp:** Gebruik beheerde identiteiten om Azure-Services te voorzien van een automatisch beheerde identiteit in azure AD. Met beheerde identiteiten kunt u zich verifiëren bij elke service die ondersteuning biedt voor Azure AD-verificatie, met inbegrip van Key Vault, zonder enige referenties in uw code.

- [Configureer beheerde identiteit voor de implementatie van Azure Resource Manager omgevingen in DevTest Labs](use-managed-identities-environments.md)
- [Beheerde identiteit configureren voor het implementeren van virtuele machines in DevTest Labs](enable-managed-identities-lab-vms.md)
 
**Azure Security Center bewaking:** Klikt

**Verantwoordelijkheid:** Gebruikers

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: onbedoelde referentie blootstelling elimineren
**Hulp:** Referentie scanner implementeren om referenties in code te identificeren. Referentie scanner stimuleert ook het verplaatsen van gedetecteerde referenties naar veiliger locaties, zoals Azure Key Vault.

- Referentie scanner instellen

**Azure Security Center bewaking:** Niet van toepassing

**Verantwoordelijkheid:** Gebruikers


## <a name="malware-defense"></a>Beveiliging tegen malware
*Zie Beveiligings beheer: verdediging tegen malware voor meer informatie.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: centraal beheerde anti-malware-software gebruiken
**Hulp:** Gebruik micro soft antimalware voor Azure Cloud Services en Virtual Machines om uw resources voortdurend te controleren en te beschermen. Gebruik voor Linux een oplossing van antimalware van derden. Gebruik ook de bedreigings detectie van Azure Security Center voor gegevens Services om malware te detecteren die is geüpload naar opslag accounts.

- Micro soft antimalware configureren voor Cloud Services en Virtual Machines
- Bescherming tegen bedreiging in Azure Security Center

**Azure Security Center bewaking:** Klikt

**Verantwoordelijkheid:** Gebruikers

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: scan bestanden die moeten worden geüpload naar niet-reken resources van Azure
**Hulp:** Micro soft antimalware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services (bijvoorbeeld Azure App Service gehost in een Lab), maar wordt niet uitgevoerd voor uw inhoud.
Scan vooraf bestanden die worden geüpload naar niet-reken resources van Azure, zoals App Service, Data Lake Storage, Blob Storage, enzovoort.

Gebruik Azure Security Center bedreigings detectie voor gegevens Services om malware te detecteren die is geüpload naar opslag accounts.

- Micro soft antimalware voor Azure Cloud Services en Virtual Machines begrijpen
- Meer informatie over de detectie van bedreigingen van Azure Security Center voor gegevens Services

**Azure Security Center bewaking:** Klikt

**Verantwoordelijkheid:** Niet van toepassing


### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: controleren of anti-malware-software en hand tekeningen zijn bijgewerkt
**Hulp:** Bij implementatie van micro soft antimalware voor Azure worden standaard automatisch de nieuwste hand tekening, platform en engine-updates geïnstalleerd. Volg de aanbevelingen in Azure Security Center: "Compute & apps" om ervoor te zorgen dat alle eind punten voor de onderliggende reken resources van DevTest Labs up-to-date zijn met de nieuwste hand tekeningen. Het Windows-besturings systeem kan verder worden beveiligd met extra beveiliging om het risico van aanvallen op virussen of malware te beperken met behulp van de micro soft Defender Advanced Threat Protection-Service die kan worden geïntegreerd met Azure Security Center.

- Micro soft antimalware implementeren voor Azure Cloud Services en Virtual Machines
- Microsoft Defender Advanced Threat Protection

**Azure Security Center bewaking:** Klikt

**Verantwoordelijkheid:** Gebruikers

## <a name="data-recovery"></a>Gegevensherstel
*Zie [beveiligings beheer: gegevens herstel](../security/benchmarks/security-control-data-recovery.md)voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zorg voor regel matige automatische back-ups
**Hulp:** Momenteel biedt Azure DevTest Labs geen ondersteuning voor back-ups en moment opnamen van VM'S. U kunt Azure Backup echter inschakelen en configureren voor de onderliggende Azure-Vm's die worden gehost in DevTest Labs. En u kunt ook de gewenste frequentie en bewaar periode voor automatische back-ups configureren, mits u de juiste toegang hebt tot de onderliggende reken resources. 

- [Een overzicht van Azure VM backup](../backup/backup-azure-vms-introduction.md)
- [Een back-up van een Azure VM maken op basis van de VM-instellingen](../backup/backup-azure-vms-first-look-arm.md)

**Azure Security Center bewaking:** Klikt

**Verantwoordelijkheid:** Gebruikers

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: volledige back-ups van het systeem uitvoeren en een back-up maken van door de klant beheerde sleutels
**Hulp:** Momenteel biedt Azure DevTest Labs geen ondersteuning voor back-ups en moment opnamen van VM'S. U kunt echter moment opnamen maken van uw onderliggende Azure-Vm's die worden gehost in de DevTest Labs of de beheerde schijven die zijn gekoppeld aan die instanties met behulp van Power shell-of REST-Api's, zolang u de juiste toegang hebt tot de onderliggende reken resources. U kunt ook een back-up maken van een door de klant beheerde sleutels in Azure Key Vault.

Schakel Azure Backup in op doel-Azure-Vm's en de gewenste frequentie en retentie perioden. Het bevat een volledige back-up van de systeem status. Als u gebruikmaakt van Azure Disk Encryption, wordt de back-up van door de klant beheerde sleutels automatisch door Azure VM backup verwerkt.

- [Back-ups maken op virtuele machines van Azure die versleuteling gebruiken](../backup/backup-azure-vms-encryption.md)
- [Overzicht van Azure VM backup](../backup/backup-azure-vms-introduction.md)
- [Een overzicht van Azure VM backup](../backup/backup-azure-vms-introduction.md)
- [Een back-up maken van Key Vault sleutels in azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center bewaking:** Klikt

**Verantwoordelijkheid:** Gebruikers

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: alle back-ups valideren, inclusief door de klant beheerde sleutels
**Hulp:** Zorg ervoor dat het gegevens herstel van inhoud in Azure Backup regel matig wordt uitgevoerd. Test zo nodig het herstellen van inhoud naar een geïsoleerd virtueel netwerk of abonnement. Test ook het herstellen van back-ups van door de klant beheerde sleutels.

Als u gebruikmaakt van Azure Disk Encryption, kunt u de Azure VM herstellen met de versleutelings sleutels voor de schijf. Wanneer u schijf versleuteling gebruikt, kunt u de Azure VM herstellen met de versleutelings sleutels voor de schijf.

- [Back-ups maken op virtuele machines van Azure die versleuteling gebruiken](../backup/backup-azure-vms-encryption.md)
- [Bestanden herstellen vanuit een back-up van Azure VM](../backup/backup-azure-restore-files-from-vm.md)
- [Sleutel kluis sleutels herstellen in azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)
- [Een back-up maken van een versleutelde VM en deze herstellen](../backup/backup-azure-vms-encryption.md)

**Azure Security Center bewaking:** Niet van toepassing

**Verantwoordelijkheid:** Gebruikers

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zorg voor de bescherming van back-ups en door de klant beheerde sleutels
**Hulp:** Wanneer u een back-up maakt van beheerde schijven met Azure Backup, worden virtuele machines op rest versleuteld met Storage Service Encryption (SSE). Azure Backup kunt ook een back-up maken van virtuele Azure-machines die zijn versleuteld met behulp van Azure Disk Encryption. Azure Disk Encryption kan worden geïntegreerd met BitLocker-versleutelings sleutels (BEKs), die worden beveiligd in een sleutel kluis als geheimen. Azure Disk Encryption is ook geïntegreerd met Azure Key Vault Key Encryption Keys (KEKs). Schakel Soft-Delete in Key Vault in om sleutels te beschermen tegen onbedoelde of schadelijke verwijdering.

- [Voorlopig verwijderen voor Vm's](../backup/soft-delete-virtual-machines.md)
- [Overzicht van Azure Key Vault-zacht-verwijderen](../key-vault/general/soft-delete-overview.md)

**Azure Security Center bewaking:** Klikt

**Verantwoordelijkheid:** Gebruikers

## <a name="incident-response"></a>Reageren op incidenten
*Zie voor meer informatie [beveiligings beheer: reactie op incidenten](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: een hand leiding voor reactie op incidenten maken
**Hulp:** Maak een antwoord gids voor incidenten voor uw organisatie. Zorg ervoor dat er ondersteunings abonnementen voor incidenten zijn die alle functies van personeel en fasen van incident handling/Management bepalen van detectie tot na incidenten beoordeling.

- [Richt lijnen voor het bouwen van uw eigen beveiligings incident antwoord proces](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)
- [Micro soft Security Response Center anatomie van een incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)
- [De verwerkings gids voor het computer beveiligings incident van het NIST gebruiken om u te helpen bij het maken van uw eigen reactie plan voor incidenten](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center bewaking:** Niet van toepassing

**Verantwoordelijkheid:** Gebruikers

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: een beoordelings procedure voor incidenten en prioriteits procedures maken
**Hulp:** Azure Security Center wijst aan elke waarschuwing een Ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of de analyse die wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau waarvan er sprake is van schadelijke opzet achter de activiteit die tot de waarschuwing heeft geleid.

Daarnaast kunt u ook duidelijk abonnementen markeren (voor bijvoorbeeld productie, niet-productie) met behulp van tags en maak een naamgevings systeem om Azure-resources duidelijk te identificeren en te categoriseren, met name voor de verwerking van gevoelige gegevens. Het is uw verantwoordelijkheid om prioriteit te geven aan het herstel van waarschuwingen op basis van de ernst van de Azure-resources en-omgeving waar het incident heeft plaatsgevonden.

- [Beveiligingswaarschuwingen in Azure Security Center](../security-center/security-center-alerts-overview.md)
- [Tags gebruiken om Azure-resources te organiseren](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center bewaking:** Klikt

**Verantwoordelijkheid:** Gebruikers

### <a name="103-test-security-response-procedures"></a>10,3: procedures voor beveiligings antwoorden testen
**Hulp:** Voer oefeningen uit om de incident respons mogelijkheden van uw systemen te testen op een regel matige uitgebracht om uw Azure-resources te beschermen. Identificeer zwakke punten en tussen ruimten en wijzig zo nodig het schema.

- [Publicatie van het NIST-hand leiding voor test-, trainings-en oefen Programma's voor IT-plannen en-mogelijkheden](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center bewaking:** Niet van toepassing

**Verantwoordelijkheid:** Gebruikers

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen configureren voor beveiligings incidenten
**Hulp:** Contact gegevens van beveiligings incidenten worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat uw gegevens zijn geopend door een onrecht matige of niet-gemachtigde partij. Bekijk incidenten na het feit om te controleren of de problemen zijn opgelost.

- [De Azure Security Center Security-contact persoon instellen](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center bewaking:** Niet van toepassing

**Verantwoordelijkheid:** Gebruikers

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: beveiligings waarschuwingen opnemen in uw reactie systeem van uw incident
**Hulp:** Exporteer uw Azure Security Center waarschuwingen en aanbevelingen met behulp van de functie continue export om Risico's voor Azure-resources te identificeren. Met doorlopend exporteren kunt u waarschuwingen en aanbevelingen hand matig of op een doorlopende manier exporteren. U kunt de Azure Security Center Data Connector gebruiken om de waarschuwingen naar Azure Sentinel te streamen.

- [Continue export configureren](../security-center/continuous-export.md)
- [Waarschuwingen streamen naar Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center bewaking:** Niet van toepassing

**Verantwoordelijkheid:** Gebruikers

#### <a name="106-automate-the-response-to-security-alerts"></a>10,6: de reactie op beveiligings waarschuwingen automatiseren
**Hulp:** Gebruik de functie werk stroom automatisering in Azure Security Center om automatisch reacties te activeren via ' Logic Apps ' in beveiligings waarschuwingen en aanbevelingen voor het beveiligen van uw Azure-resources.

- [Werk stroom automatisering en Logic Apps configureren](../security-center/workflow-automation.md)
 
Azure Security Center bewaking: * * * * niet van toepassing

**Verantwoordelijkheid:** Gebruikers


## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen
*Zie voor meer informatie Security Control: [Indringings tests en Red team-oefeningen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*


### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: voert regel matig indringings tests van uw Azure-resources uit en zorgt voor herstel van alle essentiële beveiligings resultaten binnen 60 dagen
**Hulp:** Volg de richt lijnen van micro soft om ervoor te zorgen dat uw indringings tests niet worden geschonden door het micro soft-beleid. Gebruik de strategie van micro soft en de uitvoering van de implementatie van de indringing van een live site in de Cloud, services en toepassingen die door micro soft worden beheerd.

- [Regels van betrokkenheid voor het testen van indringing](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)
- [Micro soft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center bewaking:** Niet van toepassing

**Verantwoordelijkheid:** Share

## <a name="next-steps"></a>Volgende stappen
Raadpleeg het volgende artikel:

- [Beveiligings waarschuwingen voor omgevingen in Azure DevTest Labs](environment-security-alerts.md)
