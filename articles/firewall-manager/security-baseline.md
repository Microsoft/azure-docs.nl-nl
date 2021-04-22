---
title: Azure-beveiligingsbasislijn voor Azure Firewall Manager
description: De Azure Firewall Manager beveiligingsbasislijn biedt procedurele richtlijnen en resources voor het implementeren van de beveiligingsaanbevelingen die zijn opgegeven in de Azure Security Benchmark.
author: msmbaldwin
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 7cb37de7c5f101ea5f72ff87ccdf94e5925a95d4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864411"
---
# <a name="azure-security-baseline-for-azure-firewall-manager"></a>Azure-beveiligingsbasislijn voor Azure Firewall Manager

Deze beveiligingsbasislijn past richtlijnen van [Azure Security Benchmark versie 2.0](../security/benchmarks/overview.md) toe op Azure Firewall Manager. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen. De inhoud wordt gegroepeerd op basis van de **beveiligingscontroles** die zijn gedefinieerd door de Azure Security Benchmark en de gerelateerde richtlijnen die van toepassing zijn op Azure Firewall Manager. **Besturingselementen** die niet van Azure Firewall Manager zijn uitgesloten.

Zie het volledige Azure Firewall Manager toewijzingsbestand voor beveiligingsbasislijnen om te zien hoe Azure Firewall Manager volledig is toegewezen aan de Azure [Security-Azure Firewall Manager benchmark.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="identity-management"></a>Identiteitsbeheer

*Zie [Azure Security Benchmark: Identiteitsbeheer](../security/benchmarks/security-controls-v2-identity-management.md) voor meer informatie.*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Azure Active Directory standaardiseren als het centrale identiteits--en verificatiesysteem

**Richtlijnen:** Azure Firewall Manager gebruikt Azure Active Directory (Azure AD) als de standaardservice voor identiteits- en toegangsbeheer. U moet Azure AD standaardiseren om de identiteit en het toegangsbeheer van uw organisatie te beheren in:

- Microsoft Cloud-resources, zoals Azure Portal, Azure Storage, Azure Virtual Machine (Linux en Windows), Azure Key Vault-, PaaS- en SaaS-toepassingen.

- De resources van uw organisatie, zoals toepassingen in Azure of resources van uw bedrijfsnetwerk.

Het beveiligen van Azure AD moet een hoge prioriteit hebben in de cloudbeveiligingsprocedure van uw organisatie. Azure AD biedt een id-beveiligingsscore om u te helpen beoordelen in hoeverre uw identiteitsbeveiliging voldoet aan de aanbevelingen op basis van best practices van Microsoft. Gebruik de score om te meten hoe nauwkeurig uw configuratie overeenkomt met aanbevelingen op basis van best practices, en om verbeteringen aan te brengen in uw beveiligingsaanpak.

Azure AD ondersteunt externe identiteiten waarmee gebruikers zonder een Microsoft-account zich met hun externe identiteit kunnen aanmelden bij hun toepassingen en resources.

- [Tenancy in Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md)

- [Een Azure AD-instantie maken en configureren](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Externe ID-providers voor een toepassing gebruiken](../active-directory/external-identities/identity-providers.md)

- [Wat is de id-beveiligingsscore in Azure Active Directory?](../active-directory/fundamentals/identity-secure-score.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Eenmalige aanmelding (SSO) van Azure AD gebruiken voor toegang tot toepassingen

**Richtlijnen:** Azure Firewall Manager gebruikt Azure Active Directory identiteits- en toegangsbeheer te bieden voor Azure-resources, cloudtoepassingen en on-premises toepassingen. Dit omvat ondernemingsidentiteiten zoals werknemers, maar ook externe identiteiten, zoals partners, verkopers en leveranciers. Zo kunt u eenmalige aanmelding (SSO) gebruiken voor het beheren en beveiligen van de gegevens en resources van uw organisatie, on premises en in de cloud. Verbind al uw gebruikers, toepassingen en apparaten met Azure AD voor naadloze, veilige toegang en meer zichtbaarheid en controle.

- [Inzicht in eenmalige aanmelding van toepassingen met Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Gebruik krachtige besturingselementen voor verificatie voor alle op Azure Active Directory gebaseerde toegang

**Richtlijnen:** Azure Firewall Manager gebruikt Azure Active Directory die ondersteuning biedt voor sterke verificatiecontroles via Meervoudige verificatie (MFA) en krachtige methoden zonder wachtwoord.
- Meervoudige verificatie: schakel MFA in voor Azure AD en volg de aanbevelingen op van Azure Security Center met betrekking tot identiteits- en toegangsbeheer die gelden als best practices voor het instellen van uw MFA. MFA kan worden afgedwongen voor alle gebruikers, bepaalde gebruikers of op het niveau van individuele gebruikers, op basis van voorwaarden voor aanmelding en op basis van risicofactoren.
- Verificatie zonder wachtwoord: er zijn drie verificatieopties zonder een wachtwoord beschikbaar: Windows Hello voor Bedrijven, de Microsoft Authenticator-app en on-premises verificatiemethoden zoals smartcards.

Zorg er voor beheerders en bevoegde gebruikers voor dat het hoogste niveau van de sterke verificatiemethode wordt gebruikt, gevolgd door het uitrollen van het juiste sterke verificatiebeleid voor andere gebruikers.

- [MFA inschakelen in Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Algemene informatie over opties voor verificatie zonder wachtwoord voor Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md)

- [Standaardwachtwoordbeleid voor Azure AD](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [Ongeschikte wachtwoorden voorkomen met Azure AD-wachtwoordbeveiliging](../active-directory/authentication/concept-password-ban-bad.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Bewaken van accounts op afwijkingen en waarschuwingenbeheer

**Richtlijnen:** Azure Firewall Manager is geïntegreerd met Azure Active Directory die de volgende gegevensbronnen biedt:
- Aanmeldingen: het rapport voor aanmeldingsactiviteit bevat informatie over het gebruik van beheerde toepassingen en aanmeldingsactiviteiten van gebruikers
- Auditlogboeken: traceerbaarheid via logboeken voor alle door diverse functies binnen Azure AD uitgevoerde wijzigingen. Voorbeelden van vermeldingen in auditlogboeken zijn wijzigingen die worden aangebracht in resources binnen Azure AD, zoals het toevoegen of verwijderen van gebruikers, apps, groepen, rollen en beleidsregels.
- Riskante aanmeldingen - Een riskante aanmelding is een indicator van een aanmeldingspoging die mogelijk is uitgevoerd door iemand die geen rechtmatige eigenaar van een gebruikersaccount is.
- Gebruikers voor wie wordt aangegeven dat ze risico lopen - Een riskante gebruiker is een indicator van een gebruikersaccount dat mogelijk is aangetast.

Deze gegevensbronnen kunnen worden geïntegreerd met Azure Monitor, Azure Sentinel SIEM-systemen van derden.

Acties rondom verzamelingsgroepen voor firewallbeleid worden momenteel niet ondersteund door het activiteitenlogboek. Dit is een bekend probleem en wordt in toekomstige updates opgelost.

Azure Security Center kan u ook waarschuwen voor bepaalde verdachte activiteiten, zoals een ongewoon aantal mislukte verificatiepogingen of afgeschafte accounts in het abonnement.

Azure Advanced Threat Protection (ATP) is een beveiligingsoplossing die aan de hand van signalen van Active Directory niet alleen geavanceerde bedreigingen kan identificeren, detecteren en onderzoeken, maar ook onderschepte identiteiten en schadelijke acties van binnenuit.

- [Auditrapporten over activiteiten in Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Riskante Azure AD-aanmeldingen weergeven](../active-directory/identity-protection/overview-identity-protection.md)

- [Identiteits- en toegangsactiviteiten van gebruikers controleren in Azure Security Center](../security-center/security-center-identity-access.md)

- [Azure-activiteitenlogboeken integreren in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Azure Firewall Manager bekende problemen](overview.md#known-issues)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Toegang tot Azure-resource beperken op basis van voorwaarden

**Richtlijnen:** Azure Firewall Manager ondersteunt voorwaardelijke toegang van Azure Active Directory (Azure AD) voor een gedetailleerder toegangsbeheer op basis van door de gebruiker gedefinieerde voorwaarden, zoals gebruikersmeldingen uit bepaalde IP-bereiken die MFA moeten gebruiken voor aanmelding. Gedetailleerd beheerbeleid voor verificatiesessies kan ook worden gebruikt voor verschillende scenario's.

- [Overzicht van voorwaardelijke toegang in Azure](../active-directory/conditional-access/overview.md)

- [Veelvoorkomend beleid voor voorwaardelijke toegang](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Het beheer van verificatiesessies via voorwaardelijke toegang configureren](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="privileged-access"></a>Bevoegde toegang

*Zie [Azure Security Benchmark: uitgebreide toegang](../security/benchmarks/security-controls-v2-privileged-access.md) voor meer informatie.*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Gebruikers met zeer uitgebreide bevoegdheden beveiligen en beperken

**Richtlijnen:** Azure Firewall Manager gebruikt Azure Active Directory (Azure AD) voor identiteit en toegang. De meest kritieke ingebouwde rollen zijn Azure AD: Globale beheerder en Beheerder met bevoorrechte rol, omdat gebruikers die zijn toegewezen aan deze twee rollen beheerdersrollen kunnen delegeren:
- Globale beheerder: gebruikers met deze rol hebben toegang tot alle beheerfuncties in Azure AD, evenals services die gebruikmaken van Azure AD-identiteiten.
- Beheerder met bevoorrechte rol: gebruikers met deze rol kunnen roltoewijzingen beheren in Azure AD, evenals binnen Azure AD Privileged Identity Management (PIM). Daarnaast maakt deze rol het beheer van alle aspecten van PIM en beheereenheden mogelijk.

Mogelijk hebt u andere kritieke rollen die moeten worden beheerd als u aangepaste rollen gebruikt met bepaalde machtigingen met bevoegdheden toegewezen. En mogelijk wilt u ook vergelijkbare besturingselementen toepassen op het beheerdersaccount van kritieke bedrijfsactiva.

U kunt bevoegdheden JIT-toegang (Just-In-Time) verlenen voor Azure-resources en Azure AD met behulp van Azure AD Privileged Identity Management (PIM). JIT verleent gebruikers alleen tijdelijke machtigingen voor het uitvoeren van bevoegde taken op het moment dat ze deze nodig hebben. PIM kan ook beveiligingswaarschuwingen genereren wanneer er verdachte of onveilige activiteiten worden vastgesteld in uw Azure AD-organisatie.

- [Machtigingen voor beheerdersrollen in Azure AD](../active-directory/roles/permissions-reference.md)

- [Beveiligingswaarschuwingen van Azure Privileged Identity Management gebruiken](../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Bevoegde toegang beveiligen voor hybride implementaties en cloudimplementaties in Azure AD](../active-directory/roles/security-planning.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Beheerderstoegang tot essentiële bedrijfssystemen beperken

**Richtlijnen:** Gebruik aangepaste Azure RBAC-rollen om de toegang tot regelverzamelingsgroepen te isoleren voor Azure Firewall beleid. Gebruik een aangepaste roldefinitie van Azure om onbedoeld verwijderen van basisbeleid te voorkomen en selectieve toegang te bieden tot regelverzamelingsgroepen binnen een abonnement of resourcegroep.

Zorg ervoor dat u ook de toegang beperkt tot de beheer-, identiteits- en beveiligingssystemen die beheerderstoegang hebben tot uw bedrijfskritieke systemen. Aanvallers die deze beheer- en beveiligingssystemen in gevaar brengen, kunnen ze onmiddellijk gebruiken om bedrijfskritieke assets te compromitteerd.

Alle typen toegangsbeheer moeten worden afgestemd op uw bedrijfssegmentatiestrategie om consistent toegangsbeheer te garanderen.

- [Een Azure Firewall gebruiken om een regelhiërarchie te definiëren](rule-hierarchy.md)

- [Azure-onderdelen en referentiemodel](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Toegang tot beheergroep](../governance/management-groups/overview.md#management-group-access)

- [Azure-abonnementsbeheerders](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Gebruikerstoegang regelmatig controleren en afstemmen

**Richtlijnen:** Azure Firewall Manager maakt gebruik Azure Active Directory (Azure AD)-accounts om de resources te beheren. Controleer regelmatig gebruikersaccounts en toegangstoewijzing om te controleren of de accounts en hun toegang geldig zijn. U kunt Azure AD-toegangsbeoordelingen gebruiken om groepslidmaatschap, toegang tot bedrijfstoepassingen en roltoewijzingen te controleren. Azure AD-rapportage kan logboeken bieden om verouderde accounts te helpen ontdekken. U kunt ook een Azure AD Privileged Identity Management voor het maken van een werkstroom voor toegangsbeoordelingsrapport om het beoordelingsproces te vergemakkelijken.

Daarnaast kan Azure Privileged Identity Management worden geconfigureerd om te waarschuwen wanneer er een overmatig aantal beheerdersaccounts wordt gemaakt, en om beheerdersaccounts te identificeren die verouderd of onjuist zijn geconfigureerd.

Sommige Azure-services ondersteunen lokale gebruikers en rollen die niet worden beheerd via Azure AD. U moet deze gebruikers afzonderlijk beheren.

- [Een toegangsbeoordeling maken van Azure-resourcerollen in Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Identiteits- en toegangsbeoordelingen van Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Noodtoegang instellen in Azure AD

**Richtlijnen:** Azure Firewall Manager gebruikt Azure Active Directory om gebruikers te verifiëren die de service beheren. Als u wilt voorkomen dat uw Azure AD-organisatie per ongeluk wordt vergrendeld, stelt u een account voor toegang in noodgevallen in wanneer normale beheerdersaccounts niet kunnen worden gebruikt. Accounts voor noodtoegang hebben meestal zeer uitgebreide bevoegdheden en kunnen beter niet aan specifieke personen worden toegewezen. Accounts voor noodtoegang zijn alleen bestemd voor echte noodsituaties waarin normale beheerdersaccounts niet kunnen worden gebruikt.

Zorg ervoor dat de referenties (zoals wachtwoord, certificaat of smartcard) voor accounts voor noodtoegang veilig worden bewaard en alleen bekend zijn bij personen die deze alleen in een noodgeval mogen gebruiken.

- [Accounts voor noodtoegang beheren in Azure AD](../active-directory/roles/security-emergency-access.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="pa-5-automate-entitlement-management"></a>PA-5: Rechtenbeheer automatiseren 

**Richtlijnen:** Azure Firewall Manager is geïntegreerd met Azure Active Directory voor het beheren van de resources. Gebruik Azure AD-rechtenbeheerfuncties om werkstromen voor toegangsverzoeken te automatiseren, waaronder toegangstoewijzingen, beoordelingen en vervaldatums. Goedkeuring met dubbele of meerdere fases wordt ook ondersteund.

- [Wat zijn Azure AD-toegangsbeoordelingen?](../active-directory/governance/access-reviews-overview.md)

- [Wat is Azure AD-rechtenbeheer?](../active-directory/governance/entitlement-management-overview.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Werkstations met uitgebreide toegang gebruiken

**Richtlijnen**: Beveiligde, geïsoleerde werkstations zijn van cruciaal belang voor de beveiliging van gevoelige rollen als beheerders, ontwikkelaars en serviceoperators met vergaande bevoegdheden. Gebruik uiterst beveiligde gebruikerswerkstations voor het uitvoeren van beheertaken met uw Azure Firewall Manager in productieomgevingen. Gebruik Azure Active Directory, Microsoft Defender Advanced Threat Protection (ATP) en/of Microsoft Intune als u een beveiligd en beheerd gebruikerswerkstation voor beheertaken wilt implementeren. De beveiligde werkstations kunnen centraal worden beheerd om beveiligde configuratie af te dwingen, waaronder sterke verificatie, software- en hardwarebasislijnen en beperkte logische en netwerktoegang.

- [Meer informatie over werkstations met bevoegde toegang](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Een werkstation met uitgebreide toegang gebruiken](/security/compass/privileged-access-deployment)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Principe van minimale bevoegdheden hanteren 

**Richtlijnen:** Azure Firewall Manager is geïntegreerd met op rollen gebaseerd toegangsbeheer (RBAC) van Azure om de resources te beheren. Met Azure RBAC kunt u de toegang tot Azure-resources beheren via roltoewijzingen. U kunt deze rollen toewijzen aan gebruikers, service-principals voor groepen en beheerde identiteiten. Er bestaan vooraf gedefinieerde, ingebouwde rollen voor bepaalde resources, en deze rollen kunnen worden geïnventariseerd of opgevraagd via tools zoals Azure CLI, Azure PowerShell en Azure Portal. De bevoegdheden die u via Azure RBAC toewijst aan resources, moeten altijd beperkt zijn tot wat vereist is voor de rollen. Dit is een aanvulling op de JIT-benadering van Azure AD Privileged Identity Management (PIM) en moet regelmatig worden geëvalueerd.

Gebruik ingebouwde rollen om machtigingen toe te wijzen en definieer alleen aangepaste rollen wanneer dit echt nodig is.

- [Wat is op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC)](../role-based-access-control/overview.md) 

- [Azure RBAC configureren](../role-based-access-control/role-assignments-portal.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Benchmark: gegevensbescherming](../security/benchmarks/security-controls-v2-data-protection.md) voor meer informatie.*

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Gevoelige gegevens beschermen

**Richtlijnen:** Beperk gevoelige gegevens, zoals de configuratiegegevens voor Azure Firewall Policy, door de toegang te beperken met behulp van Azure Role Based Access Control (Azure RBAC), toegangsbesturingselementen op basis van het netwerk en specifieke besturingselementen in Azure-services.

Consistent toegangsbeheer is alleen mogelijk als alle typen toegangsbeheer zijn afgestemd op de segmentatiestrategie van uw bedrijf. De segmentatiestrategie voor uw bedrijf moet ook worden gebaseerd op de locatie van gevoelige of bedrijfskritieke gegevens en systemen.

Voor het onderliggende platform, dat wordt beheerd door Microsoft, geldt dat alle klantinhoud als gevoelig wordt beschouwd en dat gegevens worden beschermd tegen verlies en blootstelling. Om ervoor te zorgen dat klantgegevens veilig blijven binnen Azure, heeft Microsoft enkele standaardmaatregelen en -mechanismen voor gegevensbeveiliging geïmplementeerd.

- [Toegangsbeheer op basis van rollen in Azure (RBAC)](../role-based-access-control/overview.md)

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: Controleren of er niet-geautoriseerde overdrachten van gevoelige gegevens hebben plaatsgevonden

**Richtlijnen:** Azure Firewall policy-resources zijn alleen toegankelijk voor geverifieerde gebruikers. Klanten moeten ervoor zorgen dat alleen geautoriseerde gebruikers toegang hebben tot de gegevens.

- [Aangepaste rollen maken voor toegang tot regelverzamelingsgroepen](rule-hierarchy.md#create-custom-roles-to-access-the-rule-collection-groups)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Gevoelige gegevens tijdens een overdracht versleutelen

**Richtlijnen:** Ter aanvulling op het toegangsbesturingselement moeten gegevens in transit worden beschermd tegen 'buiten-band'-aanvallen (bijvoorbeeld het vastleggen van verkeer) met behulp van versleuteling om ervoor te zorgen dat aanvallers de gegevens niet eenvoudig kunnen lezen of wijzigen.

Azure Firewall Manager ondersteuning voor gegevensversleuteling in transit met TLS v1.2 of hoger.

Hoewel dit optioneel is voor verkeer op particuliere netwerken, is dit essentieel voor verkeer op externe en openbare netwerken. Voor HTTP-verkeer moet u ervoor zorgen dat clients die verbinding maken met uw Azure-resources kunnen onderhandelen over TLS v1.2 of hoger. Gebruik voor extern beheer SSH (voor Linux) of RDP/TLS (voor Windows) in plaats van een niet-versleuteld protocol. Verouderde SSL-, TLS- en SSH-versies en -protocollen en zwakke coderingen moeten worden uitgeschakeld.

Azure biedt standaard versleuteling voor gegevens die onderweg zijn tussen Azure-datacenters.

- [Inzicht in versleuteling tijdens overdracht met Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) 

- [Informatie over TLS-beveiliging](/security/engineering/solving-tls1-problem) 

- [Dubbele versleuteling voor Azure-gegevens die onderweg zijn](../security/fundamentals/double-encryption.md#data-in-transit)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Gedeeld

## <a name="asset-management"></a>Asset-management

*Zie [Azure Security Benchmark: assetmanagement](../security/benchmarks/security-controls-v2-asset-management.md) voor meer informatie.*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Beveiligingsteam inzicht geven in risico's voor assets

**Richtlijnen**: Zorg ervoor dat beveiligingsteams de machtiging Beveiligingslezer hebben in uw Azure-tenant en -abonnementen zodat ze kunnen controleren op beveiligingsrisico's met behulp van Azure Security Center. 

Afhankelijk van de manier waarop de verantwoordelijkheden van het beveiligingsteam zijn gestructureerd, kan de controle op beveiligingsrisico's de verantwoordelijkheid zijn van een centraal beveiligingsteam of een lokaal team. Om die reden moeten beveiligingsinzichten en -risico's altijd centraal worden verzameld in een organisatie. 

De machtiging Beveiligingslezer kan breed worden toegepast op een hele tenant (hoofdbeheergroep) of in het bereik van beheergroepen of specifieke abonnementen. 

Er zijn mogelijk extra machtigingen vereist om inzicht te krijgen in workloads en services. 

- [Overzicht van rol Beveiligingslezer](../role-based-access-control/built-in-roles.md#security-reader)

- [Overzicht van Azure-beheergroepen](../governance/management-groups/overview.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Controleer of het beveiligingsteam toegang heeft tot de asset-inventaris en metagegevens

**Richtlijnen:** zorg ervoor dat beveiligingsteams toegang hebben tot een continu bijgewerkte inventarisatie van uw Azure Firewall Manager-assets in Azure. Ze kunnen Azure Resource Graph om alle resources in uw Azure Firewall te zoeken en te ontdekken, waaronder Azure-services, -toepassingen en -netwerkbronnen.

Pas tags toe op uw Azure-resources, resourcegroepen en abonnementen om deze logisch te ordenen in een taxonomie. Elke tag bestaat uit een naam en een waardepaar. U kunt de naam Omgeving en de waarde Productie bijvoorbeeld toepassen op alle resources in de productie.

- [Query's maken met Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md) 

- [Azure Security Center assetinventarisbeheer](../security-center/asset-inventory.md) 

- [Zie de handleiding voor beslissingen over resourcenaamgeving en -taggen voor meer informatie over het taggen van assets](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Gebruik alleen goedgekeurde Azure-Services

**Richtlijnen:** gebruik Azure Policy om te controleren en te beperken welke services gebruikers in uw omgeving kunnen inrichten. Dit omvat het toestaan of weigeren van implementaties van Azure Firewall resources. Gebruik Azure Resource Graph om resources binnen hun abonnementen op te vragen en te detecteren. U kunt Azure Monitor ook gebruiken om regels te maken voor het activeren van waarschuwingen wanneer een niet-goedgekeurde service wordt gedetecteerd.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md) 

- [Een specifiek resourcetype weigeren met Azure Policy](../governance/policy/samples/built-in-policies.md#general) 

- [Query's maken met Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Beveiliging van levenscyclusbeheer van assets garanderen

**Richtlijnen:** verwijder Azure Firewall Manager resources wanneer ze niet meer nodig zijn om het aanvalsoppervlak te minimaliseren. Gebruikers kunnen hun Azure Firewall Manager beheren via de Azure Portal, CLI of REST API's.

- [Azure Firewall Policy CLI](/cli/azure/network/firewall/policy)

- [Azure-netwerk-CLI](/powershell/module/az.network/?preserve-view=true&view=azps-5.1.0#networking)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager

**Richtlijnen:** Azure Firewall Manager is geïntegreerd met Azure Active Directory (Azure AD) voor identiteit en verificatie. U kunt voorwaardelijke toegang van Azure gebruiken om de mogelijkheid van gebruikers om te communiceren met Azure Resources Manager te beperken door Toegang blokkeren te configureren voor de app Microsoft Azure Management.

- [Voorwaardelijke toegang configureren om toegang tot Azure Resources Manager te blokkeren](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="logging-and-threat-detection"></a>Logboekregistratie en detectie van bedreigingen

*Zie [Azure Security Benchmark: logboekregistratie en detectie van bedreigingen](../security/benchmarks/security-controls-v2-logging-threat-detection.md) voor meer informatie.*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Detectie van bedreigingen inschakelen voor Azure-resources

**Richtlijnen:** Activiteitenlogboeken die zijn geproduceerd door of gerelateerd aan firewallbeleid, doorsturen naar uw SIEM, die kunnen worden gebruikt voor het instellen van aangepaste detecties van bedreigingen. Zorg ervoor dat u verschillende typen Azure-assets controleert op mogelijke bedreigingen en afwijkingen. Richt u op het ontvangen van waarschuwingen van hoge kwaliteit om het aantal fout-positieven te verminderen dat analisten kunnen sorteren. Waarschuwingen kunnen afkomstig zijn uit logboekgegevens, agents of andere gegevens.

- [Aangepaste analyseregels maken om bedreigingen te detecteren](../sentinel/tutorial-detect-threats-custom.md) 

- [Cyberbedreigingsinformatie met Azure Sentinel](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

- [Azure Firewall-logboeken en metrische gegevens](../firewall/firewall-diagnostics.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Bedreigingsdetectie inschakelen voor identiteits- en toegangsbeheer van Azure

**Richtlijnen**: Azure AD biedt de volgende gebruikerslogboeken die kunnen worden weergegeven in Azure AD Reporting of worden geïntegreerd met Azure Monitor, Azure Sentinel of andere tools voor SIEM/controle voor meer geavanceerde scenario's voor controle en analyse:
- Aanmeldingen: het rapport voor aanmeldingsactiviteit bevat informatie over het gebruik van beheerde toepassingen en aanmeldingsactiviteiten van gebruikers

- Auditlogboeken: traceerbaarheid via logboeken voor alle door diverse functies binnen Azure AD uitgevoerde wijzigingen. Voorbeelden van vermeldingen in auditlogboeken zijn wijzigingen die worden aangebracht in resources binnen Azure AD, zoals het toevoegen of verwijderen van gebruikers, apps, groepen, rollen en beleidsregels.
- Riskante aanmeldingen - Een riskante aanmelding is een indicator van een aanmeldingspoging die mogelijk is uitgevoerd door iemand die geen rechtmatige eigenaar van een gebruikersaccount is.
- Gebruikers voor wie wordt aangegeven dat ze risico lopen - Een riskante gebruiker is een indicator van een gebruikersaccount dat mogelijk is aangetast.

Azure Security Center kan u ook waarschuwen voor bepaalde verdachte activiteiten, zoals een ongewoon aantal mislukte verificatiepogingen of afgeschafte accounts in het abonnement. Naast de basiscontrole op beveiligingshygiëne, kan de module voor bedreigingsbeveiliging van Azure Security Center ook uitgebreidere beveiligingswaarschuwingen verzamelen van afzonderlijke Azure-compute-resources (virtuele machines, containers, app service), gegevensbronnen (SQL DB en opslag) en Azure-servicelagen. Deze mogelijkheid geeft u inzicht in accountafwijkingen binnen de afzonderlijke resources.

Acties rondom verzamelingsgroepen voor firewallbeleid worden momenteel niet ondersteund door het activiteitenlogboek. Dit is een bekend probleem en wordt in toekomstige updates opgelost.

- [Auditrapporten over activiteiten in Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Azure AD Identity Protection inschakelen](../active-directory/identity-protection/overview-identity-protection.md)

- [Bescherming tegen bedreiging in Azure Security Center](../security-center/azure-defender.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Logboekregistratie inschakelen voor Azure-resources

**Richtlijnen:** Activiteitenlogboeken, die automatisch beschikbaar zijn, bevatten alle schrijfbewerkingen (PUT, POST, DELETE) voor uw firewallbeleidsbronnen, met uitzondering van leesbewerkingen (GET). Activiteitenlogboeken kunnen worden gebruikt om een fout te vinden bij het oplossen van problemen of om te controleren hoe een gebruiker in uw organisatie een resource heeft gewijzigd.

Acties rondom verzamelingsgroepen voor firewallbeleid worden momenteel niet ondersteund door het activiteitenlogboek. Dit is een bekend probleem en wordt in toekomstige updates opgelost.

- [Platformlogboeken en metrische gegevens verzamelen met Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Logboekregistratie en verschillende logboektypen in Azure begrijpen](../azure-monitor/essentials/platform-logs-overview.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Beheer en analyse van beveiligingslogboek centraliseren

**Richtlijnen:** Opslag en analyse van logboekregistratie centraliseren om correlatie mogelijk te maken. Zorg ervoor dat u voor elke logboekbron een gegevenseigenaar hebt toegewezen, richtlijnen voor toegang, opslaglocatie, welke hulpprogramma's worden gebruikt voor het verwerken en openen van de gegevens en vereisten voor gegevensretentie.

Zorg ervoor dat u Azure-activiteitenlogboeken integreert in uw centrale logboekregistratie. Logboeken opnemen via Azure Monitor voor het verzamelen van beveiligingsgegevens die zijn gegenereerd door eindpuntapparaten, netwerkbronnen en andere beveiligingssystemen. Gebruik Azure Monitor Log Analytics-werkruimten om query's uit te voeren en analyses uit te voeren en gebruik Azure Storage accounts voor langetermijn- en archiveringsopslag.

Daarnaast kunt u uw logboekgegevens inschakelen en onboarden Azure Sentinel of een SIEM van derden.

Veel organisaties kiezen ervoor om Azure Sentinel te gebruiken voor 'hot' gegevens die vaak worden gebruikt en Azure Storage voor 'koude' gegevens die minder vaak worden gebruikt.

- [Platformlogboeken en metrische gegevens verzamelen met Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Onboarding van Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Bewaarperiode voor logboek configureren

**Richtlijnen:** Zorg ervoor dat voor opslagaccounts of Log Analytics-werkruimten die worden gebruikt voor het opslaan van firewallbeleidslogboeken, de bewaarperiode voor logboeken is ingesteld volgens de nalevingsvoorschriften van uw organisatie.

In Azure Monitor kunt u de retentieperiode van uw Log Analytics-werkruimte instellen op basis van de nalevingsvoorschriften van uw organisatie. Gebruik Azure Storage, Data Lake of Log Analytics-werkruimteaccounts voor langetermijn- en archiveringsopslag.

- [Retentieperiode voor Log Analytics-werkruimte configureren](../azure-monitor/logs/manage-cost-storage.md)

- [Resourcelogboeken opslaan in een Azure Storage account](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Benchmark: respons op incidenten](../security/benchmarks/security-controls-v2-incident-response.md) voor meer informatie.*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Voorbereiding: responsproces voor incidenten bijwerken voor Azure

**Richtlijnen**: Zorg ervoor dat uw organisatie processen heeft gedefinieerd om te reageren op beveiligingsincidenten, dat deze processen voor Azure zijn bijgewerkt en dat ze regelmatig worden uitgevoerd om de gereedheid ervan te garanderen.

- [Beveiliging in de complete bedrijfsomgeving implementeren](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Naslaggids voor respons op incidenten](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Voorbereiding: melding van incidenten instellen

**Richtlijnen**: Contactgegevens in Azure Security Center instellen in het geval van beveiligingsincidenten Deze contactgegevens worden door Microsoft gebruikt om contact met u op te nemen als het Microsoft Security Response Center (MSRC) vaststelt dat een niet-geautoriseerde partij toegang tot uw gegevens heeft gekregen. Er zijn ook opties waarmee u incidentwaarschuwingen en -meldingen in verschillende Azure-Services kunt aanpassen aan de hand van wat u als incidentrespons nodig acht. 

- [Contactpersoon voor beveiliging instellen in Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Detectie en analyse: incidenten maken op basis van waarschuwingen van hoge kwaliteit

**Richtlijnen**: Zorg ervoor dat u een proces hebt gedefinieerd om waarschuwingen van hoge kwaliteit te maken en de kwaliteit van waarschuwingen te meten. Zo kunt u op grond van wat u uit eerdere incidenten hebt geleerd een hogere prioriteit toekennen aan waarschuwingen die bestemd zijn voor analisten, zodat deze geen tijd hoeven te verspillen aan fout-positieven. 

Waarschuwingen van hoge kwaliteit kunnen worden samengesteld op basis van ervaringen uit eerdere incidenten, op grond van gevalideerde communitybronnen, en door tools te gebruiken die zijn ontworpen om waarschuwingen te genereren en op te schonen door diverse signaalbronnen samen te voegen en er correlaties tussen te ontdekken. 

Azure Security Center biedt waarschuwingen van hoge kwaliteit voor veel Azure-assets. U kunt de ASC-dataconnector gebruiken om de waarschuwingen te streamen naar Azure Sentinel. Met Azure Sentinel kunt u geavanceerde waarschuwingsregels opstellen om automatisch incidenten te genereren voor onderzoek. 

Exporteer de waarschuwingen en aanbevelingen van Azure Security Center met behulp van de exportfunctie om zo beter risico's voor Azure-resources te kunnen identificeren. U kunt waarschuwingen en aanbevelingen handmatig exporteren of doorlopend.

- [Export configureren](../security-center/continuous-export.md)

- [Waarschuwingen streamen naar Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Detectie en analyse - een incident onderzoeken

**Richtlijnen**: Zorg ervoor dat analisten verschillende gegevensbronnen kunnen bevragen en gebruiken bij het onderzoeken van mogelijke incidenten, zodat ze een volledig beeld hebben van wat er is gebeurd. Er moeten verschillende logboeken worden verzameld om de activiteiten van een mogelijke aanvaller in de kill chain te volgen om zo blinde vlekken te voorkomen.  U moet er ook voor zorgen dat inzichten en resultaten worden vastgelegd voor andere analisten, zodat deze kunnen worden geraadpleegd als historische naslaginformatie.  

De gegevensbronnen voor onderzoek zijn niet alleen de gecentraliseerde logboekbronnen die al worden verzameld door de services binnen het bereik en de actieve systemen, maar kunnen ook de volgende zijn:

- Netwerkgegevens: gebruik stroomlogboeken van netwerkbeveiligingsgroepen, Azure Network Watcher en Azure Monitor om logboeken van netwerkstromen en andere analysegegevens vast te leggen. 

- Momentopnamen van actieve systemen: 

    - Gebruik de functie voor het maken van momentopnamen van virtuele machines van Azure om een momentopname of snapshot te maken van de schijf van het actieve systeem. 

    - Gebruik de voorziening van het besturingssysteem voor het maken van geheugendumps om een momentopname te maken van het geheugen van het actieve systeem.

    - Gebruik de momentopnamefunctie van de Azure-services of van uw software om momentopnamen van de actieve systemen te maken.

Azure Sentinel biedt uitgebreide voorzieningen voor gegevensanalyse voor vrijwel elke logboekbron en een portal voor dossierbeheer om de volledige levenscyclus van incidenten te beheren. Informatie die wordt verzameld tijdens een onderzoek kan worden gekoppeld aan een incident voor tracerings- en rapportagedoeleinden. 

- [Momentopname maken van de schijf van een Windows-computer](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Momentopname maken van de schijf van een Linux-computer](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Diagnostische gegevens en geheugendumps van Microsoft Azure Ondersteuning](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Incidenten onderzoeken met Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Detectie en analyse: prioriteiten toekennen aan incidenten

**Richtlijnen**: Bied context aan analisten voor de incidenten waarop ze zich eerst moeten concentreren op basis van de ernst van waarschuwingen en de gevoeligheid van assets. 

Azure Security Center wijst aan elke waarschuwing een ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op hoe zeker Security Center is in het zoeken of de analyse die wordt gebruikt om de waarschuwing uit te geven, evenals het betrouwbaarheidsniveau dat er een kwaadwillende intentie is achter de activiteit die heeft geleid tot de waarschuwing.

Daarnaast kunt u resources markeren met behulp van tags en een naamgevingssysteem opzetten om Azure-resources te identificeren en categoriseren, met name voor resources die gevoelige gegevens verwerken.  Het is uw verantwoordelijkheid om prioriteit te geven aan het oplossen van waarschuwingen op basis van de ernst van de Azure-resources en -omgeving waarin het incident heeft plaatsgevonden.

- [Beveiligingswaarschuwingen in Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Tags gebruiken om Azure-resources te organiseren](../azure-resource-manager/management/tag-resources.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Insluiting, uitschakeling en herstel: het afhandelen van incidenten automatiseren

**Richtlijnen**: Automatiseer handmatige, terugkerende taken om de responstijd te versnellen en de werklast van analisten te verlichten. Het uitvoeren van handmatige taken duurt langer, waardoor elk incident trager gaat en het aantal incidenten dat een analist kan afhandelen, kleiner wordt. Daarnaast raken analisten sneller vermoeid door handmatige taken, met als gevolg dat het risico van menselijke fouten toeneemt en analisten zich minder goed kunnen concentreren. Gebruik voorzieningen voor de automatisering van werkstromen in Azure Security Center en Azure Sentinel om automatisch acties te activeren of een playbook uit te voeren in reactie op binnenkomende beveiligingswaarschuwingen. Het playbook voert acties uit, zoals het verzenden van meldingen, het uitschakelen van accounts en het isoleren van problematische netwerken. 

- [Werkstroomautomatisering configureren in Security Center](../security-center/workflow-automation.md)

- [Geautomatiseerde bedreigingsreacties instellen in Azure Security Center](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Geautomatiseerde bedreigingsreacties instellen in Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="posture-and-vulnerability-management"></a>Beveiligingspostuur en beveiligingsproblemen beheren

*Zie [Azure Security Benchmark: beveiligingspostuur en beveiligingsproblemen beheren](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md) voor meer informatie.*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Veilige configuraties tot stand brengen voor Azure-services 

**Richtlijnen:** Automatiseer en beveilig de implementatie en configuratie van Azure Firewall Manager-resources in uw omgevingen met behulp van mechanismen zoals Azure Resource Manager-sjablonen, Azure RBAC-besturingselementen en Azure Policy. Definieer beveiligde configuraties voor uw Azure Firewall Manager-resources tijdens de implementatie, controleer en dwing deze configuraties af door aangepaste Azure Policy-definities te definiëren met behulp van aliassen in de naamruimte 'Azure.Network'.

- [Azure Firewall beleidssjabloonverwijzing](/azure/templates/microsoft.network/firewallpolicies)

- [Azure Firewall Policy CLI](/cli/azure/network/firewall/policy)

- [Afbeelding van de implementatie van richtlijnen in een landingszone op ondernemingsschaal](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture#landing-zone-expanded-definition)

- [Beleidsregels voor het afdwingen van naleving maken en beheren](../governance/policy/tutorials/create-and-manage.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Veilige configuraties onderhouden voor Azure-services

**Richtlijnen:** Azure Firewall Manager ondersteuning Azure Resource Manager op basis van sjablonen en het afdwingen van configuratie-instellingen via Azure Policy. Definieer aangepaste Azure Policy voor het controleren en afdwingen van Azure Firewall Manager resourceconfiguraties met behulp van aliassen in de naamruimte 'Azure.Network'.

- [Inzicht in Azure Policy effecten](../governance/policy/concepts/effects.md)

- [Azure Firewall beleidssjabloonverwijzing](/azure/templates/microsoft.network/firewallpolicies)

- [Afbeelding van de implementatie van richtlijnen in een landingszone op ondernemingsschaal](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture#landing-zone-expanded-definition)

- [Beleidsregels voor het afdwingen van naleving maken en beheren](../governance/policy/tutorials/create-and-manage.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: Veilige configuraties voor rekenbronnen maken

**Richtlijnen:** Niet van toepassing; Azure Firewall Manager is een beheerservice voor firewallbesturingsvlak en maakt de rekeninfrastructuur van de onderliggende service niet weer voor klanten om te configureren.

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Voer regelmatige simulaties van aanvallen uit

**Richtlijnen**: Voer zo vaak u als u wilt penetratietests of Red Teaming-activiteiten uit op uw Azure-resources, en zorg ervoor dat alle kritieke beveiligingsbevindingen worden opgelost.
Ga te werk volgens de Microsoft Cloud Penetration Testing Rules of Engagement (Regels voor het inzetten van penetratietests voor Microsoft Cloud ) zodat u zeker weet dat uw penetratietests niet conflicteren met Microsoft-beleid. Gebruik de strategie van Microsoft en de uitvoering van Red Teaming-activiteiten, en voer een penetratietest van de live site uit op basis van een infrastructuur, services en toepassingen die door Microsoft worden beheerd.

- [Penetratietesten in Azure](../security/fundamentals/pen-testing.md)

- [Regels voor het inzetten van penetratietests](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

## <a name="backup-and-recovery"></a>Back-up en herstel

*Zie [Azure Security Benchmark: back-up en herstel](../security/benchmarks/security-controls-v2-backup-recovery.md) voor meer informatie.*

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1: Regelmatige automatische back-ups garanderen

**Richtlijnen:** Azure Firewall Manager niet het concept van klantgerichte systeemback-ups heeft, wordt de onderliggende infrastructuur verwerkt door Microsoft.

Gebruik voor back-ups van resourceconfiguratie Azure Resource Manager firewallbeleidsregels en gerelateerde resources te exporteren in een JSON-sjabloon (JavaScript Object Notation) die kan worden gebruikt als configuratieback-up. U kunt ook firewallbeleidsconfiguraties exporteren met behulp van de exportsjabloonfunctie van Azure Firewall van Azure Portal. Gebruik Azure Automation om aangepaste back-upscripts uit te voeren om resourceconfiguraties van uw Azure Firewall Manager resources automatisch vast te leggen.

- [Beveiligde virtuele hub implementeren met behulp van sjabloon](quick-secure-virtual-hub.md)

- [Sjabloonverwijzing voor Microsoft Firewall-beleid](/azure/templates/microsoft.network/firewallpolicies)

- [Over Azure Automation](../automation/automation-intro.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: Valideer alle back-ups, inclusief door de klant beheerde sleutels

**Richtlijnen:** Azure Firewall Manager heeft niet het concept van klantgerichte systeemback-ups. Voor alle geëxporteerde Azure Firewall Manager resourcesjablonen periodiek herstel uitvoeren met behulp van Azure Resource Manager sjabloonbestanden.

- [Beveiligde virtuele hub implementeren met behulp Azure Resource Manager sjablonen](quick-secure-virtual-hub.md)

- [Sjabloonverwijzing voor Microsoft Firewall-beleid](/azure/templates/microsoft.network/firewallpolicies)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="governance-and-strategy"></a>Governance en strategie

*Zie [Azure Security Benchmark: governance en strategie](../security/benchmarks/security-controls-v2-governance-strategy.md).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Strategie voor asset-management en gegevensbescherming definiëren 

**Richtlijnen**: Zorg ervoor dat u een duidelijke strategie voor de continue bewaking en bescherming van systemen en gegevens schriftelijk vastlegt en bekendmaakt. Ken hogere prioriteiten toe aan de detectie, beoordeling, beveiliging en bewaking van bedrijfskritieke gegevens en systemen. 

Deze strategie moet gedocumenteerde richtlijnen, beleid en standaarden bevatten voor de volgende elementen: 

-   Standaard voor gegevensclassificatie in overeenstemming met de bedrijfsrisico's

-   Inzicht van beveiligingsorganisaties in risico's en asset-inventaris 

-   Goedkeuring door beveiligingsorganisaties van Azure-services voor gebruik 

-   Beveiliging van assets op grond van hun levenscyclus

-   Vereiste strategie voor toegangsbeheer in overeenstemming met gegevensclassificatie van organisatie

-   Gebruik van systeemeigen beveiligingsmogelijkheden van Azure en van derde partijen

-   Vereisten voor gegevensversleuteling in gebruiksscenario's met gegevens tijdens een overdracht en data-at-rest

-   Juiste cryptografische standaarden

Raadpleeg de volgende bronnen voor meer informatie:
- [Azure Security Architecture Recommendation - Storage, data, and encryption](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json) (Aanbeveling voor Azure-beveiligingsarchitectuur: opslag, gegevens en versleuteling)

- [Azure Security Fundamentals - Azure Data security, encryption, and storage](../security/fundamentals/encryption-overview.md) (Basisprincipes van Azure-beveiliging: Azure-gegevensbeveiliging, -versleuteling en -opslag)

- [Cloud Adoption Framework - Azure data security and encryption best practices](../security/fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json) (Cloud Adoption Framework: best practices voor Azure-gegevensbeveiliging en -versleuteling)

- [Azure Security Benchmark - Asset management](../security/benchmarks/security-controls-v2-asset-management.md) (Azure Security Benchmark: assetmanagement)

- [Azure Security Benchmark - Data Protection](../security/benchmarks/security-controls-v2-data-protection.md) (Azure Security Benchmark: gegevensbeveiliging)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Segmentatiestrategie van bedrijf definiëren 

**Richtlijnen**: Stel een bedrijfsstrategie op om de toegang tot assets te segmenteren door een combinatie van besturingselementen voor het beheer van identiteiten, netwerken, toepassingen, abonnementen, beheergroepen en andere besturingselementen te gebruiken.

Zorg dat u een nauwgezette afweging maakt tussen de noodzaak om de beveiliging van de rest te scheiden en de noodzaak om systemen die met elkaar moeten kunnen communiceren en toegang moeten hebben tot gegevens, in staat te stellen om hun dagelijkse werklast uit te voeren.

Zorg ervoor dat de segmentatiestrategie consistent wordt geïmplementeerd voor alle typen besturingselementen, zoals voor netwerkbeveiliging, identiteits- en toegangsmodellen, toepassingsbevoegdheden/toegangsmodellen evenals besturingselementen voor door mensen uitgevoerde processen.

- [Richtlijnen voor segmentatiestrategie in Azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Richtlijnen voor segmentatiestrategie in Azure (document)](/security/compass/governance#enterprise-segmentation-strategy)

- [Netwerksegmentatie afstemmen op segmentatiestrategie van bedrijf](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Strategie voor het beheer van beveiligingspostuur definiëren

**Richtlijnen**: Meet en beperk voortdurend de risico's die alle individuele assets en de omgeving die ze hosten, lopen. Ken hogere prioriteiten toe aan hoogwaardige assets en assets die zeer kwetsbaar zijn voor aanvallen, zoals gepubliceerde toepassingen, punten voor binnenkomend en uitgaand netwerkverkeer, gebruikers- en beheerderseindpunten enzovoort.

- [Azure Security Benchmark: beveiligingspostuur en beveiligingsproblemen beheren](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Organisatierollen, verantwoordelijkheden en aansprakelijkheden afstemmen

**Richtlijnen**: Zorg ervoor dat u een duidelijke strategie voor rollen en verantwoordelijkheden in uw beveiligingsorganisatie schriftelijk vastlegt en bekendmaakt. Ken een hoge prioriteiten toe aan het verschaffen van duidelijkheid over wie aansprakelijk is voor beveiligingsbeslissingen. Bied opleidingen aan iedereen over het gedeelde verantwoordelijkheidsmodel en biedt technische teams trainingen over technologie ter beveiliging van de cloud.

- [Best practice voor Azure-beveiliging 1: personen: Teams trainen inzake het cloudbeveiligingstraject](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Best practice voor Azure-beveiliging 2: personen: Teams trainen inzake cloudbeveiligingstechnologie](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure Security Best Practice 3 - proces: Aansprakelijkheid toewijzen voor beslissingen over cloudbeveiliging](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Strategie voor netwerkbeveiliging definiëren

**Richtlijnen**: Stel een benadering op voor netwerkbeveiliging in Azure als onderdeel van de algehele strategie voor toegangsbeheer in uw organisatie.  

Deze strategie moet gedocumenteerde richtlijnen, beleid en standaarden bevatten voor de volgende elementen: 

-   Gecentraliseerd netwerkbeheer en verantwoordelijkheid voor beveiliging

-   Model voor segmentatie van virtueel netwerk afgestemd op segmentatiestrategie van bedrijf

-   Herstelstrategie voor verschillende scenario's met bedreigingen en aanvallen

-   Strategie voor internetrand en inkomend en uitgaand verkeer

-   Strategie voor hybride cloud- en on-premises interconnectiviteit

-   Up-to-date netwerkbeveiligingsartefacten (zoals netwerkdiagrammen, architectuur van referentienetwerk)

Raadpleeg de volgende bronnen voor meer informatie:
- [Azure Security Best Practice 11: architectuur. Eén uniforme beveiligingsstrategie](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Benchmark: netwerkbeveiliging](../security/benchmarks/security-controls-v2-network-security.md)

- [Overzicht van Azure-netwerkbeveiliging](../security/fundamentals/network-overview.md)

- [Strategie voor architectuur van bedrijfsnetwerk](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Strategie voor het gebruik van identiteiten en uitgebreide toegang definiëren

**Richtlijnen**: Stel een benadering op voor het gebruik van identiteiten en uitgebreide toegang als onderdeel van de algehele strategie voor toegangsbeheer in uw organisatie.  

Deze strategie moet gedocumenteerde richtlijnen, beleid en standaarden bevatten voor de volgende elementen: 

-   Een gecentraliseerd identiteits- en verificatiesysteem en bijbehorende interconnectiviteit met andere interne en externe identiteitssystemen

-   Krachtige verificatiemethoden in verschillende gebruiksscenario's en onder verschillende voorwaarden

-   Bescherming van gebruikers met zeer uitgebreide bevoegdheden

-   Bewaking en verwerking van afwijkende gebruikersactiviteiten  

-   Proces voor het beoordelen en op elkaar afstemmen van de identiteit en toegangsrechten van gebruikers

Raadpleeg de volgende bronnen voor meer informatie:

- [Azure Security Benchmark: Identity management](../security/benchmarks/security-controls-v2-identity-management.md) (Azure Security Benchmark: identiteitsbeheer)

- [Azure Security Benchmark - Privileged access](../security/benchmarks/security-controls-v2-privileged-access.md) (Azure Security Benchmark: uitgebreide toegang)

- [Azure Security Best Practice 11: architectuur. Eén uniforme beveiligingsstrategie](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Overzicht van beveiliging met Azure-identiteitsbeheer](../security/fundamentals/identity-management-overview.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Strategie voor logboekregistratie en respons op bedreigingen definiëren

**Richtlijnen**: Stel een strategie op voor logboekregistratie en de respons bij bedreigingen om bedreigingen snel te detecteren en op te lossen terwijl u voldoet aan de nalevingsvereisten. Stel prioriteiten om analisten te voorzien van waarschuwingen van hoge kwaliteit en naadloze ervaringen, zodat ze zich kunnen concentreren op bedreigingen in plaats van integratie en handmatige stappen. 

Deze strategie moet gedocumenteerde richtlijnen, beleid en standaarden bevatten voor de volgende elementen: 

-   De rol en verantwoordelijkheden van de personen of groepen die binnen de organisatie beveiligingsactiviteiten (SecOps) uitvoeren 

-   Een goed gedefinieerd proces voor het reageren op incidenten dat is afgestemd op het NIST of een ander framework binnen de branche 

-   Logboekregistratie en retentie om ondersteuning te bieden voor de detectie van bedreigingen, het reageren op incidenten en het naleven van regelgeving

-   Informatie en correlatiegegevens over bedreigingen die centraal beschikbaar zijn via SIEM, systeemeigen Azure-mogelijkheden en andere bronnen 

-   Communicatie- en meldingenplan met uw klanten, leveranciers en openbaar belanghebbenden

-   Gebruik van systeemeigen Azure-platforms en platforms van derden voor het afhandelen van incidenten, zoals logboekregistratie en detectie van bedreigingen, forensische onderzoeken en het oplossen en uitschakelen van aanvallen

-   Processen voor het afhandelen van incidenten en activiteiten na incidenten, zoals geleerde lessen en het bewaren van bewijsmateriaal

Raadpleeg de volgende bronnen voor meer informatie:

- [Azure Security Benchmark: logboekregistratie en detectie van bedreigingen](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Azure Security Benchmark: respons op incidenten](../security/benchmarks/security-controls-v2-incident-response.md)

- [Azure Security Best Practice 4: proces. Processen voor respons op incidenten bijwerken voor de cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Handleiding framework voor Azure-acceptatie, logboekregistratie en rapportagebeslissingen](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Schaalaanpassing, beheer en bewaking van Azure Enterprise](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="next-steps"></a>Volgende stappen

- Zie [Overzicht Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Meer informatie over [Azure-beveiligingsbasislijnen](../security/benchmarks/security-baselines-overview.md)