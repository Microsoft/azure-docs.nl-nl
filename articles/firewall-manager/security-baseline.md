---
title: Azure-beveiligings basislijn voor Azure Firewall Manager
description: De beveiligings basislijn van Azure Firewall Manager biedt procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-Bench Mark.
author: msmbaldwin
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 8d3288ddde87468f3b6382fab77b0097e350dde2
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96171038"
---
# <a name="azure-security-baseline-for-azure-firewall-manager"></a>Azure-beveiligings basislijn voor Azure Firewall Manager

In deze beveiligings basislijn wordt richt lijn toegepast van de [Azure Security Bench Mark-versie 2,0](../security/benchmarks/overview.md) naar Azure firewall Manager. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen. De inhoud wordt gegroepeerd op de **beveiligings controles** die zijn gedefinieerd door de Azure Security-Bench Mark en de bijbehorende richt lijnen die van toepassing zijn op Azure firewall Manager. **Besturings elementen** die niet van toepassing zijn op Azure firewall Manager, zijn uitgesloten.

Zie het [volledige bestand met de beveiligings basislijn toewijzing van Azure firewall Manager](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)voor informatie over de manier waarop Azure firewall Manager volledig is toegewezen aan de beveiligings benchmarks van Azure.

## <a name="identity-management"></a>Identiteitsbeheer

*Ga voor meer informatie naar [Azure Security Benchmark: Identiteitsbeheer](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Azure Active Directory standaardiseren als het centrale identiteits- en verificatiesysteem

**Hulp**: Azure firewall Manager maakt gebruik van Azure Active Directory (Azure AD) als de standaard service voor identiteits-en toegangs beheer. U moet Azure AD standaardiseren om het identiteits-en toegangs beheer van uw organisatie in te bepalen:

- Microsoft Cloud resources, zoals de Azure Portal, Azure Storage, virtuele machine van Azure (Linux en Windows), Azure Key Vault, PaaS en SaaS-toepassingen.

- De resources van uw organisatie, zoals toepassingen in Azure of resources van uw bedrijfsnetwerk.

Het beveiligen van Azure AD moet een hoge prioriteit hebben bij het nemen van beveiligingsmaatregelen voor de cloud in uw organisatie. Azure AD biedt een beveiligde score voor identiteiten om u te helpen bij het beoordelen van identiteits beveiliging postuur ten opzichte van de aanbevelingen van micro soft best practice. Gebruik de score om te meten hoe goed uw configuratie aansluit bij de aanbevelingen en om verbeteringen door te voeren in uw beveiligingspostuur.

Azure AD biedt ondersteuning voor externe identiteit waarmee gebruikers zonder Microsoft-account zich kunnen aanmelden bij hun toepassingen en resources met hun externe identiteit.

- [Tenancy in Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md)

- [Een Azure AD-instantie maken en configureren](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Externe ID-providers voor de toepassing gebruiken](/azure/active-directory/b2b/identity-providers)

- [Wat is de identiteit van beveiligde scores in Azure Active Directory](../active-directory/fundamentals/identity-secure-score.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Eenmalige aanmelding van Azure AD gebruiken voor toegang tot toepassingen

**Richt lijnen**: Azure firewall Manager gebruikt Azure Active Directory om identiteits-en toegangs beheer te bieden aan Azure-resources, Cloud toepassingen en on-premises toepassingen. Dit geldt niet alleen voor identiteiten zoals werknemers, maar ook voor externe identiteiten zoals partners en leveranciers. Hierdoor is eenmalige aanmelding mogelijk voor het beheren en beveiligen van toegang tot gegevens en resources van uw organisatie, zowel on-premises als in de cloud. Koppel al uw gebruikers, toepassingen en apparaten aan Azure AD voor naadloze, veilige toegang en meer zichtbaarheid en controle.

- [Eenmalige aanmelding van toepassingen met Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Krachtige verificatiemechanismen gebruiken voor alle op Azure Active Directory gebaseerde toegang

**Richt lijnen**: Azure firewall Manager gebruikt Azure Active Directory die ondersteuning biedt voor krachtige verificatie-instellingen via multi-factor Authentication (MFA) en krachtige methoden met een wacht woord.
- Meervoudige verificatie: Schakel meervoudige verificatie van Azure AD in en volg de aanbevelingen voor identiteits- en toegangsbeheer van Azure Security Center voor enkele best practices bij de configuratie van meervoudige verificatie. Meervoudige verificatie kan worden afgedwongen voor alle of bepaalde gebruikers, of op gebruikersniveau op basis van aanmeldingsvoorwaarden en risicofactoren.
- Verificatie zonder wachtwoord: Er zijn drie opties beschikbaar voor verificatie zonder wachtwoord: Windows Hello voor Bedrijven, de Microsoft Authenticator-app en on-premises verificatiemethoden zoals smartcards.

Voor beheerders en bevoegde gebruikers zorgt u ervoor dat het hoogste niveau van de methode voor sterke verificatie wordt gebruikt, gevolgd door het juiste beleid voor sterke authenticatie voor andere gebruikers te implementeren.

- [Meervoudige verificatie inschakelen in Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Algemene informatie over opties voor verificatie zonder wachtwoord voor Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md)

- [Standaardwachtwoordbeleid voor Azure AD](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [Ongeschikte wachtwoorden voorkomen met Azure AD-wachtwoordbeveiliging](../active-directory/authentication/concept-password-ban-bad.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Controleren op accountafwijkingen en hiervoor waarschuwen

**Hulp**: Azure firewall Manager is geïntegreerd met Azure Active Directory waarin de volgende gegevens bronnen worden geleverd:
- Aanmeldingen: het rapport voor aanmeldingsactiviteit bevat informatie over het gebruik van beheerde toepassingen en aanmeldingsactiviteiten van gebruikers
- Auditlogboeken: traceerbaarheid via logboeken voor alle door diverse functies binnen Azure AD uitgevoerde wijzigingen. Voorbeelden van vermeldingen in auditlogboeken zijn wijzigingen die worden aangebracht in resources binnen Azure AD, zoals het toevoegen of verwijderen van gebruikers, apps, groepen, rollen en beleidsregels.
- Riskante aanmeldingen - Een riskante aanmelding is een indicator van een aanmeldingspoging die mogelijk is uitgevoerd door iemand die geen rechtmatige eigenaar van een gebruikersaccount is.
- Gebruikers voor wie wordt aangegeven dat ze risico lopen - Een riskante gebruiker is een indicator van een gebruikersaccount dat mogelijk is aangetast.

Deze gegevens bronnen kunnen worden geïntegreerd met Azure Monitor, Azure Sentinel of SIEM-systemen van derden.

Momenteel worden acties rondom groeps beleidsregels voor de firewall beleidsregel niet ondersteund door het activiteiten logboek, dit is een bekend probleem en wordt opgelost in toekomstige updates.

Azure Security Center kan u ook waarschuwen voor bepaalde verdachte activiteiten, zoals een ongewoon aantal mislukte verificatiepogingen of afgeschafte accounts in het abonnement.

Azure Advanced Threat Protection (ATP) is een beveiligingsoplossing die aan de hand van signalen van Active Directory niet alleen geavanceerde bedreigingen kan identificeren, detecteren en onderzoeken, maar ook onderschepte identiteiten en schadelijke acties van binnenuit.

- [Auditrapporten over activiteiten in Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Riskante Azure AD-aanmeldingen weergeven](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Identiteits- en toegangsactiviteiten van gebruikers controleren in Azure Security Center](../security-center/security-center-identity-access.md)

- [Azure-activiteitenlogboeken integreren in Azure Monitor](/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Bekende problemen met Azure Firewall Manager](overview.md#known-issues)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>Chat-6: toegang tot Azure-bronnen beperken op basis van voor waarden

**Hulp**: Azure firewall Manager ondersteunt de voorwaardelijke toegang van Azure Active Directory (Azure AD) voor een gedetailleerd toegangs beheer op basis van door de gebruiker gedefinieerde voor waarden, zoals gebruikers aanmeldingen vanuit bepaalde IP-bereiken die MFA moeten gebruiken voor aanmelding. Gedetailleerd beheer beleid voor verificatie sessies kan ook worden gebruikt voor verschillende use cases.

- [Overzicht van voorwaardelijke toegang voor Azure](../active-directory/conditional-access/overview.md)

- [Algemeen beleid voor voorwaardelijke toegang](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Verificatie sessie beheer met voorwaardelijke toegang configureren](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="privileged-access"></a>Bevoegde toegang

*Ga voor meer informatie naar [Azure Security-benchmark: Bevoegde toegang](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Gebruikers met hoge bevoegdheden beveiligen en beperken

**Hulp**: Azure firewall Manager maakt gebruik van Azure Active Directory (Azure AD) voor identiteit en toegang. De belangrijkste ingebouwde rollen zijn Azure AD zijn globale beheerder en de beheerder van de bevoegde rol als gebruikers die zijn toegewezen aan deze twee rollen, kunnen beheerders rollen overdragen:
- Beheerder van globale beheerder/bedrijf: gebruikers met deze rol hebben toegang tot alle beheer functies in azure AD, evenals services die gebruikmaken van Azure AD-identiteiten.
- Beheerder van geprivilegieerde rol: gebruikers met deze rol kunnen roltoewijzingen beheren in azure AD, en in Azure AD Privileged Identity Management (PIM). Daarnaast kunt u met deze rol alle aspecten van PIM en administratieve eenheden beheren.

Mogelijk hebt u andere essentiële rollen die u moet onderhevig hebben als u aangepaste rollen gebruikt waaraan bepaalde privileged permissions zijn toegewezen. Het is ook mogelijk dat u vergelijk bare besturings elementen wilt Toep assen op het beheerders account van essentiële bedrijfs activa.

U kunt bevoegdheden JIT-toegang (Just-In-Time) verlenen voor Azure-resources en Azure AD met behulp van Azure AD Privileged Identity Management (PIM). JIT verleent gebruikers alleen tijdelijke machtigingen voor het uitvoeren van bevoegde taken op het moment dat ze deze nodig hebben. PIM kan ook beveiligingswaarschuwingen genereren wanneer er verdachte of onveilige activiteiten worden vastgesteld in uw Azure AD-organisatie.

- [Machtigingen voor beheerdersrollen in Azure AD](/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

- [Beveiligingswaarschuwingen van Azure Privileged Identity Management gebruiken](../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Bevoegde toegang beveiligen voor hybride implementaties en cloudimplementaties in Azure AD](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: beheer toegang beperken tot essentiële bedrijfs systemen

**Richt lijnen**: gebruik aangepaste Azure RBAC-rollen voor het isoleren van toegang tot regel verzamelings groepen voor Azure firewall-beleid. Gebruik een aangepaste functie definitie van Azure om te voor komen dat er onbedoeld basis beleid wordt verwijderd en geef selectieve toegang tot regel verzamelings groepen binnen een abonnement of resource groep op.

Zorg ervoor dat u ook de toegang tot de beheer-, identiteits-en beveiligings systemen beperkt die beheerders toegang hebben tot uw essentiële bedrijfs systemen. Aanvallers die deze beheer-en beveiligings systemen misbruiken, kunnen ze onmiddellijk weaponize om bedrijfs kritieke activa te beschadigen.

Alle typen besturings elementen voor toegang moeten worden afgestemd op uw bedrijfs segmentatie strategie om een consistent toegangs beheer te garanderen.

- [Azure Firewall-beleid gebruiken om een regel hiërarchie te definiëren](rule-hierarchy.md)

- [Azure-onderdelen en referentie model](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Toegang tot beheer groepen](../governance/management-groups/overview.md#management-group-access)

- [Beheerders van Azure-abonnementen](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Gebruikerstoegang regelmatig beoordelen en afstemmen

**Hulp**: Azure firewall Manager maakt gebruik van Azure Active Directory (Azure AD)-accounts voor het beheren van de bijbehorende resources. Controleer gebruikers accounts en toegangs toewijzingen regel matig om te controleren of de accounts en hun toegang geldig zijn. U kunt Azure AD-toegangs beoordelingen gebruiken om groepslid maatschappen, de toegang tot bedrijfs toepassingen en roltoewijzingen te controleren. Azure AD Reporting kan logboeken bieden waarmee u verlopen accounts kunt detecteren. U kunt ook Azure AD Privileged Identity Management gebruiken om een werk stroom voor het lezen van rapporten te maken om het controle proces te vergemakkelijken.

Daarnaast kan Azure Privileged Identity Management ook worden geconfigureerd om te worden gewaarschuwd wanneer er een uitzonderlijk groot aantal beheerders accounts wordt gemaakt en om beheerders accounts te identificeren die verouderd of onjuist zijn geconfigureerd.

Sommige Azure-Services ondersteunen lokale gebruikers en rollen die niet worden beheerd via Azure AD. U moet deze gebruikers afzonderlijk beheren.

- [Een toegangs beoordeling van Azure-resource rollen maken in Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Identiteits- en toegangsbeoordelingen van Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Emergency Access instellen in azure AD

**Hulp**: Azure firewall Manager gebruikt Azure Active Directory om gebruikers te verifiëren die de service beheren. Als u wilt voor komen dat uw Azure AD-organisatie per ongeluk wordt vergrendeld, stelt u een account voor toegang voor nood gevallen in voor toegang wanneer normale beheerders accounts niet kunnen worden gebruikt. Accounts voor toegang in nood gevallen zijn meestal zeer goed beschermd en ze mogen niet worden toegewezen aan specifieke personen. Accounts voor toegang in nood gevallen zijn beperkt tot scenario's met nood gevallen of ' afbreek glazen ', waarbij normale beheerders accounts niet kunnen worden gebruikt.

Zorg ervoor dat de referenties (zoals wacht woord, certificaat of Smart Card) voor accounts voor toegang in nood gevallen beveiligd blijven en alleen bekend zijn bij personen die alleen in een nood geval mogen gebruiken.

- [Accounts voor nood toegang beheren in azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="pa-5-automate-entitlement-management"></a>PA-5: beheer van rechten automatiseren 

**Hulp**: Azure firewall Manager is geïntegreerd met Azure Active Directory om de bijbehorende resources te beheren. Gebruik Azure AD-functies voor rechten beheer om werk stromen voor toegangs aanvragen te automatiseren, met inbegrip van toegangs toewijzingen, beoordelingen en verval datums. Het is ook mogelijk om twee of meerdere fasen goed te keuren.

- [Wat zijn Azure AD-toegangs beoordelingen](../active-directory/governance/access-reviews-overview.md)

- [Wat is het beheer van rechten van Azure AD](../active-directory/governance/entitlement-management-overview.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: bevoorrechte toegang gebruiken werk stations

**Richt lijnen**: beveiligde, geïsoleerde werk stations zijn van cruciaal belang voor de beveiliging van gevoelige rollen als beheerders, ontwikkel aars en essentiële service operators. Gebruik werk stations met zeer beveiligde gebruikers voor het uitvoeren van beheer taken met uw Azure Firewall Manager-resources in productie omgevingen. Gebruik Azure Active Directory, micro soft Defender Advanced Threat Protection (ATP) en/of Microsoft Intune voor het implementeren van een beveiligd en beheerd gebruikers werkstation voor beheer taken. De beveiligde werk stations kunnen centraal worden beheerd voor het afdwingen van beveiligde configuratie, inclusief sterke authenticatie, software-en hardware-basis lijnen en beperkte logische en netwerk toegang.

- [Meer informatie over privileged Access workstations](../active-directory/devices/concept-azure-managed-workstation.md)

- [Een privileged Access-werk station implementeren](../active-directory/devices/howto-azure-managed-workstation.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Principe van minimale bevoegdheden hanteren 

**Hulp**: Azure firewall Manager is geïntegreerd met op rollen gebaseerd toegangs beheer (RBAC) van Azure voor het beheren van de bijbehorende resources. Met Azure RBAC kunt u de toegang tot Azure-resources beheren via roltoewijzingen. U kunt deze rollen toewijzen aan gebruikers, groeperingen van service-principals en beheerde identiteiten. Er bestaan vooraf gedefinieerde, ingebouwde rollen voor bepaalde resources, en deze rollen kunnen worden geïnventariseerd of opgevraagd via tools zoals Azure CLI, Azure PowerShell en Azure Portal. De bevoegdheden die u via Azure RBAC toewijst aan resources, moeten altijd beperkt zijn tot wat vereist is voor de rollen. Dit is een aanvulling op de JIT-benadering van Azure AD Privileged Identity Management (PIM) en moet regelmatig worden geëvalueerd.

Gebruik ingebouwde rollen om machtigingen toe te wijzen en definieer alleen aangepaste rollen wanneer dit echt nodig is.

- [Wat is Azure Role-based Access Control (Azure RBAC)](../role-based-access-control/overview.md) 

- [RBAC configureren in Azure](../role-based-access-control/role-assignments-portal.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="data-protection"></a>Gegevensbeveiliging

*Ga voor meer informatie naar [Azure Security-benchmark: Gegevensbeveiliging](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Gevoelige gegevens beveiligen

**Hulp**: gevoelige gegevens beveiligen, zoals de configuratie gegevens voor Azure firewall beleid door de toegang te beperken met behulp van Azure-rol op basis Access Control (Azure RBAC), op het netwerk gebaseerde toegangs beheer functies en specifieke besturings elementen in Azure-Services.

Consistent toegangsbeheer is alleen mogelijk als alle typen toegangsbeheer zijn afgestemd op de segmentatiestrategie van uw bedrijf. De segmentatiestrategie voor uw bedrijf moet ook worden gebaseerd op de locatie van gevoelige of bedrijfskritieke gegevens en systemen.

Voor het onderliggende platform, dat wordt beheerd door Microsoft, geldt dat alle klantinhoud als gevoelig wordt beschouwd en dat gegevens worden beschermd tegen verlies en blootstelling. Om ervoor te zorgen dat klantgegevens veilig blijven binnen Azure, heeft Microsoft enkele standaardmaatregelen en -mechanismen voor gegevensbeveiliging geïmplementeerd.

- [Toegangsbeheer op basis van rollen in Azure (RBAC)](../role-based-access-control/overview.md)

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: Controleren op niet-geautoriseerde overdracht van gevoelige gegevens

**Hulp**: Azure firewall-beleids bronnen kunnen alleen worden gebruikt door geverifieerde gebruikers. Klanten moeten ervoor zorgen dat alleen geautoriseerde gebruikers toegang hebben tot de gegevens.

- [Aangepaste rollen maken om toegang te krijgen tot regel verzamelings groepen](rule-hierarchy.md#create-custom-roles-to-access-the-rule-collection-groups)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: gevoelige gegevens tijdens de overdracht versleutelen

**Richt lijnen**: als aanvulling op de toegangs controle, moeten gegevens in transit worden beveiligd tegen buiten-band-aanvallen (bijvoorbeeld verkeer vastleggen) met behulp van versleuteling om ervoor te zorgen dat aanvallers de gegevens niet eenvoudig kunnen lezen of wijzigen.

Azure Firewall Manager ondersteunt gegevens versleuteling tijdens de overdracht met TLS v 1.2 of hoger.

Hoewel dit optioneel is voor verkeer op particuliere netwerken, is dit van cruciaal belang voor verkeer op externe en open bare netwerken. Voor HTTP-verkeer moet u ervoor zorgen dat clients die verbinding maken met uw Azure-resources, TLS v 1.2 of hoger kunnen onderhandelen. Voor extern beheer gebruikt u SSH (voor Linux) of RDP/TLS (voor Windows) in plaats van een niet-versleuteld protocol. Verouderde SSL-, TLS-en SSH-versies en-protocollen en zwakke cijfers moeten worden uitgeschakeld.

Azure biedt standaard versleuteling voor gegevens in transit tussen Azure-data centers.

- [Meer informatie over versleuteling in transit met Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) 

- [Informatie over TLS-beveiliging](/security/engineering/solving-tls1-problem) 

- [Dubbele versleuteling voor Azure-gegevens in transit](../security/fundamentals/double-encryption.md#data-in-transit)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Gedeeld

## <a name="asset-management"></a>Asset-management

*Ga voor meer informatie naar [Azure Security-benchmark: Asset-management](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Beveiligingsteam inzicht geven in risico's voor assets

**Richtlijnen**: Zorg ervoor dat beveiligingsteams de machtiging Beveiligingslezer hebben in uw Azure-tenant en -abonnementen zodat ze kunnen controleren op beveiligingsrisico's met behulp van Azure Security Center. 

Afhankelijk van de manier waarop de verantwoordelijkheden van het beveiligingsteam zijn gestructureerd, kan de controle op beveiligingsrisico's de verantwoordelijkheid zijn van een centraal beveiligingsteam of een lokaal team. Om die reden moeten beveiligingsinzichten en -risico's altijd centraal worden verzameld in een organisatie. 

De machtiging Beveiligingslezer kan breed worden toegepast op een hele tenant (hoofdbeheergroep) of in het bereik van beheergroepen of specifieke abonnementen. 

Er zijn mogelijk extra machtigingen vereist om inzicht te krijgen in werk belastingen en services. 

- [Overzicht van rol Beveiligingslezer](../role-based-access-control/built-in-roles.md#security-reader)

- [Overzicht van Azure-beheergroepen](../governance/management-groups/overview.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: controleren of het beveiligings team toegang heeft tot de inventaris en meta gegevens van de Asset

**Richt lijnen**: Zorg ervoor dat beveiligings teams toegang hebben tot een voortdurend bijgewerkte inventaris van uw Azure firewall Manager-assets in Azure. Ze kunnen Azure resource Graph gebruiken om alle Azure Firewall resources in uw abonnementen te doorzoeken en te detecteren, met inbegrip van Azure-Services,-toepassingen en-netwerk bronnen.

Pas Tags toe op uw Azure-resources, resource groepen en abonnementen om ze logisch in een taxonomie te organiseren. Elke tag bestaat uit een naam en een waardepaar. U kunt de naam Omgeving en de waarde Productie bijvoorbeeld toepassen op alle resources in de productie.

- [Query's maken met Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md) 

- [Azure Security Center Asset Inventory Management](../security-center/asset-inventory.md) 

- [Voor meer informatie over het labelen van assets raadpleegt u de hand leiding resource naamgeving en Tags toevoegen](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: alleen goedgekeurde Azure-Services gebruiken

**Hulp**: gebruik Azure Policy om te controleren en te beperken welke Services gebruikers in uw omgeving kunnen inrichten. Dit omvat het toestaan of weigeren van implementaties van Azure firewall resources. Gebruik Azure resource Graph om bronnen in hun abonnementen op te vragen en te detecteren. U kunt ook Azure Monitor gebruiken om regels te maken voor het activeren van waarschuwingen wanneer een niet-goedgekeurde service wordt gedetecteerd.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md) 

- [Een specifiek resource type weigeren met Azure Policy](../governance/policy/samples/built-in-policies.md#general) 

- [Query's maken met Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Beveiliging van levenscyclusbeheer van assets garanderen

**Hulp**: Azure firewall Manager-resources verwijderen wanneer ze niet meer nodig zijn om kwets baarheid te minimaliseren. Gebruikers kunnen hun Azure Firewall Manager-resources beheren via de Azure Portal-, CLI-of REST-Api's.

- [Azure Firewall-beleids-CLI](https://docs.microsoft.com/cli/azure/ext/azure-firewall/network/firewall/policy?view=azure-cli-latest&amp;preserve-view=true)

- [Azure Network CLI](https://docs.microsoft.com/powershell/module/az.network/?view=azps-5.1.0#networking&amp;preserve-view=true)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

**Hulp**: Azure firewall Manager is geïntegreerd met Azure Active Directory (Azure AD) voor identiteit en verificatie. U kunt voorwaardelijke toegang van Azure gebruiken om de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager te beperken door het configureren van ' toegang blok keren ' voor de app ' Microsoft Azure-beheer '.

- [Voorwaardelijke toegang configureren om de toegang tot Azure-bronnen beheer te blok keren](../role-based-access-control/conditional-access-azure-management.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="logging-and-threat-detection"></a>Logboekregistratie en bedreigingsdetectie

*Ga voor meer informatie naar [Azure Security-benchmark: Logboekregistratie en bedreigingsdetectie](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: detectie van bedreigingen inschakelen voor Azure-resources

**Hulp**: activiteiten logboeken die zijn geproduceerd door of gerelateerd zijn aan het firewall beleid voor uw Siem die kunnen worden gebruikt voor het instellen van aangepaste bedreigings detecties. Zorg ervoor dat u verschillende typen Azure-assets bewaakt voor mogelijke dreigingen en afwijkingen. Richt u op het ophalen van waarschuwingen met hoge kwaliteit om te voor komen dat de fout-positieven worden gereduceerd. Waarschuwingen kunnen worden gebrond op basis van logboek gegevens, agents of andere gegevens.

- [Aangepaste analyse regels maken voor het detecteren van bedreigingen](../sentinel/tutorial-detect-threats-custom.md) 

- [Cyber Threat Intelligence met Azure Sentinel](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

- [Azure Firewall-logboeken en metrische gegevens](../firewall/firewall-diagnostics.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Bedreigingsdetectie inschakelen voor identiteits- en toegangsbeheer van Azure

**Richtlijnen**: Azure AD biedt de volgende gebruikerslogboeken die kunnen worden weergegeven in Azure AD Reporting of worden geïntegreerd met Azure Monitor, Azure Sentinel of andere tools voor SIEM/controle voor meer geavanceerde scenario's voor controle en analyse:
- Aanmeldingen: het rapport voor aanmeldingsactiviteit bevat informatie over het gebruik van beheerde toepassingen en aanmeldingsactiviteiten van gebruikers

- Auditlogboeken: traceerbaarheid via logboeken voor alle door diverse functies binnen Azure AD uitgevoerde wijzigingen. Voorbeelden van vermeldingen in auditlogboeken zijn wijzigingen die worden aangebracht in resources binnen Azure AD, zoals het toevoegen of verwijderen van gebruikers, apps, groepen, rollen en beleidsregels.
- Riskante aanmeldingen - Een riskante aanmelding is een indicator van een aanmeldingspoging die mogelijk is uitgevoerd door iemand die geen rechtmatige eigenaar van een gebruikersaccount is.
- Gebruikers voor wie wordt aangegeven dat ze risico lopen - Een riskante gebruiker is een indicator van een gebruikersaccount dat mogelijk is aangetast.

Azure Security Center kan u ook waarschuwen voor bepaalde verdachte activiteiten, zoals een ongewoon aantal mislukte verificatiepogingen of afgeschafte accounts in het abonnement. Naast de basiscontrole op beveiligingshygiëne, kan de module voor bedreigingsbeveiliging van Azure Security Center ook uitgebreidere beveiligingswaarschuwingen verzamelen van afzonderlijke Azure-compute-resources (virtuele machines, containers, app service), gegevensbronnen (SQL DB en opslag) en Azure-servicelagen. Deze functie geeft u inzicht in de account afwijkingen binnen de afzonderlijke resources.

Momenteel worden acties rondom groeps beleidsregels voor de firewall beleidsregel niet ondersteund door het activiteiten logboek, dit is een bekend probleem en wordt opgelost in toekomstige updates.

- [Auditrapporten over activiteiten in Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Azure AD Identity Protection inschakelen](../active-directory/identity-protection/overview-identity-protection.md)

- [Bescherming tegen bedreiging in Azure Security Center](/azure/security-center/threat-protection)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: logboek registratie inschakelen voor Azure-resources

**Hulp**: activiteiten logboeken, die automatisch beschikbaar zijn, bevatten alle schrijf bewerkingen (put, post, Delete) voor uw firewall beleids bronnen, behalve Lees bewerkingen (Get). Activiteiten logboeken kunnen worden gebruikt om een fout te vinden bij het oplossen van problemen of om te controleren hoe een gebruiker in uw organisatie een resource heeft gewijzigd.

Momenteel worden acties rondom groeps beleidsregels voor de firewall beleidsregel niet ondersteund door het activiteiten logboek, dit is een bekend probleem en wordt opgelost in toekomstige updates.

- [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Logboek registratie en verschillende logboek typen in azure](../azure-monitor/platform/platform-logs-overview.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: beveiligings logboek beheer en-analyse centraliseren

**Richt lijnen**: opslag en analyse van logboek registratie centraliseren om correlatie in te scha kelen. Zorg ervoor dat u voor elke logboek bron een gegevens eigenaar hebt toegewezen, toegangs richtlijnen, opslag locatie, welke hulpprogram ma's worden gebruikt voor het verwerken en openen van de gegevens en vereisten voor gegevens behoud.

Zorg ervoor dat u Azure-activiteiten logboeken integreert in uw centrale logboek registratie. Opname logboeken via Azure Monitor voor het verzamelen van beveiligings gegevens die zijn gegenereerd door eindpunt apparaten, netwerk bronnen en andere beveiligings systemen. Gebruik in Azure Monitor Log Analytics-werk ruimten om analyses uit te voeren en te uitvoeren en gebruik Azure Storage accounts voor lange termijn-en archiverings opslag.

Daarnaast kunt u uw logboek gegevens inschakelen en onboarden naar Azure Sentinel of een SIEM van derden.

Veel organisaties kiezen voor het gebruik van Azure Sentinel voor ' hot ' gegevens die regel matig worden gebruikt en die worden Azure Storage voor ' koude ' gegevens die minder vaak worden gebruikt.

- [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Bewaar periode voor logboek opslag configureren

**Richt lijnen**: Zorg ervoor dat opslag accounts of log Analytics-werk ruimten die worden gebruikt voor het opslaan van Firewall-beleids logboeken, de Bewaar periode voor logboek registratie hebben op basis van de nalevings voorschriften van uw organisatie.

In Azure Monitor kunt u de Bewaar periode voor uw Log Analytics werk ruimte instellen volgens de nalevings voorschriften van uw organisatie. Gebruik Azure Storage-, Data Lake-of Log Analytics werkruimte accounts voor lange termijn-en archiverings opslag.

- [De Bewaar periode van Log Analytics Workspace configureren](../azure-monitor/platform/manage-cost-storage.md)

- [Bron logboeken opslaan in een Azure Storage-account](/azure/azure-monitor/platform/resource-logs-collect-storage)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="incident-response"></a>Reageren op incidenten

*Ga voor meer informatie naar [Azure Security-benchmark: Reageren op incidenten](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Voorbereiding – proces voor reageren op incidenten bijwerken voor Azure

**Richtlijnen**: Zorg ervoor dat er processen aanwezig zijn in uw organisatie om te reageren op beveiligingsincidenten, dat deze processen zijn bijgewerkt voor Azure en dat ze regelmatig worden uitgevoerd om de gereedheid ervan te garanderen.

- [Beveiliging implementeren binnen de onderneming](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Naslaggids voor reageren op incidenten](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Voorbereiding - melding van incidenten configureren

**Richtlijnen**: Definieer in Azure Security Center contactgegevens voor beveiligingsincidenten. Deze contactgegevens worden gebruikt door Microsoft om contact met u op te nemen als Microsoft Security Response Center (MSRC) heeft vastgesteld dat uw gegevens zijn geraadpleegd door een onrechtmatige of niet-geautoriseerde partij. U hebt ook de mogelijkheid om waarschuwingen en meldingen voor incidenten aan te passen in verschillende Azure-services, op basis van de behoeften van de reactie op incidenten. 

- [Contactpersoon voor beveiliging instellen in Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Detectie en analyse - incidenten maken op basis van waarschuwingen van hoge kwaliteit

**Richt lijnen**: Zorg ervoor dat u een proces hebt om waarschuwingen van hoge kwaliteit te maken en de kwaliteit van waarschuwingen te meten. Zo kunt u leren van eerdere incidenten en prioriteit toekennen aan waarschuwingen voor analisten, zodat ze geen tijd verspillen aan fout-positieven. 

Waarschuwingen van hoge kwaliteit kunnen worden gebouwd op basis van ervaringen van eerdere incidenten, gevalideerde community-bronnen en hulpprogram ma's die zijn ontworpen om waarschuwingen te genereren en op te schonen door diverse signaal bronnen te weigeren en te correleren. 

Azure Security Center biedt waarschuwingen van hoge kwaliteit in veel Azure-assets. U kunt de ASC-dataconnector gebruiken om de waarschuwingen te streamen naar Azure Sentinel. Met Azure Sentinel kunt u geavanceerde waarschuwingsregels opstellen om automatisch incidenten te genereren voor onderzoek. 

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

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Detectie en analyse: incidenten prioriteren

**Richtlijnen**: Bied context aan analisten voor de incidenten waarop ze zich eerst moeten concentreren op basis van de ernst van waarschuwingen en de gevoeligheid van assets. 

Azure Security Center wijst aan elke waarschuwing een ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of de analyse die wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau waarvan er sprake is van schadelijke opzet achter de activiteit die tot de waarschuwing heeft geleid.

Daarnaast kunt u resources markeren met behulp van tags en een naamgevingssysteem opzetten om Azure-resources te identificeren en categoriseren, met name voor resources die gevoelige gegevens verwerken.  Het is uw verantwoordelijkheid om prioriteit te geven aan het oplossen van waarschuwingen op basis van de ernst van de Azure-resources en -omgeving waarin het incident heeft plaatsgevonden.

- [Beveiligingswaarschuwingen in Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Tags gebruiken om Azure-resources te organiseren](/azure/azure-resource-manager/resource-group-using-tags)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Insluiting, eliminatie en herstel - verwerking van incidenten automatiseren

**Richtlijnen**: Automatiseer handmatige terugkerende taken om de responstijd te versnellen en de belasting van analisten te verlagen. Handmatige taken nemen meer tijd in beslag, waardoor elk incident wordt vertraagd en een analist minder incidenten kan verwerken. Daarnaast raken analisten sneller vermoeid door handmatige taken, met als gevolg dat het risico van menselijke fouten toeneemt en analisten zich minder goed kunnen concentreren. Gebruik voorzieningen voor de automatisering van werkstromen in Azure Security Center en Azure Sentinel om automatisch acties te activeren of een playbook uit te voeren in reactie op binnenkomende beveiligingswaarschuwingen. Het playbook voert acties uit, zoals het verzenden van meldingen, het uitschakelen van accounts en het isoleren van problematische netwerken. 

- [Werkstroomautomatisering configureren in Security Center](../security-center/workflow-automation.md)

- [Geautomatiseerde bedreigingsreacties instellen in Azure Security Center](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Geautomatiseerde bedreigingsreacties instellen in Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="posture-and-vulnerability-management"></a>Beheer van beveiligingspostuur en beveiligingsproblemen

*Ga voor meer informatie naar [Azure Security-benchmark: Beheer van beveiligingspostuur en beveiligingsproblemen](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: beveiligde configuraties instellen voor Azure-Services 

**Richt lijnen**: de implementatie en configuratie van Azure firewall Manager-resources in uw omgevingen automatiseren en beveiligen met behulp van mechanismen zoals: Azure Resource Manager sjablonen, Azure RBAC-besturings elementen en Azure Policy. Definieer veilige configuraties voor uw Azure Firewall Manager-resources tijdens de implementatie, Controleer en dwing deze configuraties af door aangepaste Azure Policy definities te definiëren met behulp van aliassen in de naam ruimte Azure. Network.

- [Naslag informatie over Azure Firewall-beleids sjabloon](/azure/templates/microsoft.network/firewallpolicies)

- [Azure Firewall-beleids-CLI](https://docs.microsoft.com/cli/azure/ext/azure-firewall/network/firewall/policy?view=azure-cli-latest&amp;preserve-view=true)

- [Afbeelding van Guardrails-implementatie in de schaal zone op ondernemings niveau](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture#landing-zone-expanded-definition)

- [Beleidsregels voor het afdwingen van naleving maken en beheren](../governance/policy/tutorials/create-and-manage.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: beveiligde configuraties voor Azure-Services

**Hulp**: Azure firewall Manager ondersteunt op Azure Resource Manager gebaseerde sjablonen en handhaving van configuratie-instellingen via Azure Policy. Definieer aangepaste Azure Policy definities voor het controleren en afdwingen van Azure Firewall Manager-resource configuraties met behulp van aliassen in de naam ruimte Azure. Network.

- [Azure Policy effecten begrijpen](../governance/policy/concepts/effects.md)

- [Naslag informatie over Azure Firewall-beleids sjabloon](/azure/templates/microsoft.network/firewallpolicies)

- [Afbeelding van Guardrails-implementatie in de schaal zone op ondernemings niveau](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture#landing-zone-expanded-definition)

- [Beleidsregels voor het afdwingen van naleving maken en beheren](../governance/policy/tutorials/create-and-manage.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: veilige configuraties voor reken bronnen instellen

**Richt lijnen**: niet van toepassing; Azure Firewall Manager is een Firewall Control-beheer service en maakt geen gebruik van de berekenings infrastructuur van de onderliggende service zodat klanten deze kunnen configureren.

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Regelmatig aanvalssimulaties uitvoeren

**Richtlijnen**: Voer zo nodig penetratietests of Red Teaming-activiteiten uit voor uw Azure-resources en zorg ervoor dat alle beveiligingsproblemen worden opgelost.
Volg de Microsoft Cloud Penetration Testing Rules of Engagement om ervoor te zorgen dat uw penetratietests voldoen aan de beleidsregels van Microsoft. Volg de strategie en instructies van Microsoft voor Red Teaming en penetratietests van live sites voor cloudinfrastructuur, services en toepassingen die door Microsoft worden beheerd.

- [Penetratietests in Azure](../security/fundamentals/pen-testing.md)

- [Penetration Testing Rules of Engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

## <a name="backup-and-recovery"></a>Back-up en herstel

*Zie [Azure Security Bench Mark: Backup and Recovery](/azure/security/benchmarks/security-controls-v2-backup-recovery)(Engelstalig) voor meer informatie.*

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1: controleren op regel matige automatische back-ups

**Richt lijnen**: Azure firewall Manager beschikt niet over het concept van systeem back-ups van de klant, de onderliggende infra structuur wordt afgehandeld door micro soft.

Gebruik Azure Resource Manager voor het maken van back-ups van bron configuratie om firewall beleid en gerelateerde resources te exporteren in een JavaScript Object Notation (JSON)-sjabloon die kan worden gebruikt als back-up van de configuratie. U kunt ook firewall beleids configuraties exporteren met de functie sjabloon exporteren van Azure Firewall van Azure Portal. Gebruik Azure Automation voor het uitvoeren van aangepaste back-upscripts voor het automatisch vastleggen van resource configuraties van uw Azure Firewall Manager-resources.

- [Beveiligde virtuele hub implementeren met behulp van sjabloon](quick-secure-virtual-hub.md)

- [Naslag informatie over micro soft Firewall Policy Temp late](/azure/templates/microsoft.network/firewallpolicies)

- [Over Azure Automation](../automation/automation-intro.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: alle back-ups valideren, inclusief door de klant beheerde sleutels

**Hulp**: Azure firewall Manager heeft geen klant gerichte systeem back-ups. Voor alle geëxporteerde Azure Firewall Manager-resource sjablonen wordt periodiek herstel uitgevoerd met behulp van deze Azure Resource Manager sjabloon bestanden.

- [Beveiligde virtuele hub implementeren met behulp van Azure Resource Manager sjablonen](quick-secure-virtual-hub.md)

- [Naslag informatie over micro soft Firewall Policy Temp late](/azure/templates/microsoft.network/firewallpolicies)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="governance-and-strategy"></a>Governance en strategie

*Ga voor meer informatie naar [Azure Security-benchmark: Governance en strategie](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Strategie voor Asset Management en gegevensbescherming definiëren 

**Richtlijnen**: Zorg ervoor dat u een duidelijke strategie documenteert en communiceert voor continue bewaking en bescherming van systemen en gegevens. Stel prioriteiten in voor detectie, beoordeling, beveiliging en bewaking van bedrijfskritieke gegevens en systemen. 

Deze strategie moet gedocumenteerde richtlijnen, beleid en standaarden bevatten voor de volgende elementen: 

-   Standaard voor gegevensclassificatie in overeenstemming met de bedrijfsrisico's

-   Zichtbaarheid van de beveiligingsorganisaties ten aanzien van risico's en asset-inventaris 

-   Goedkeuring van beveiligingsorganisatie van te gebruiken Azure-services 

-   Beveiliging van assets gedurende de gehele levenscyclus

-   Vereiste strategie voor toegangsbeheer in overeenstemming met gegevensclassificatie van organisatie

-   Gebruik van mogelijkheden van Azure native en gegevens bescherming van derden

-   Vereisten voor gegevensversleutelings van gegevens in-transit en at-rest

-   Juiste cryptografische normen

Raadpleeg het volgende Engelstalige naslagmateriaal voor meer informatie:
- [Azure Security Architecture Recommendation - Storage, data, and encryption](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Azure Security Fundamentals - Azure Data security, encryption, and storage](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework - Azure data security and encryption best practices](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Azure Security Benchmark - Asset management](/azure/security/benchmarks/security-benchmark-v2-asset-management)

- [Azure Security Benchmark - Data Protection](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Segmentatiestrategie voor bedrijf definiëren 

**Richtlijnen**: Stel een bedrijfsbrede strategie in om de toegang tot assets te segmenteren met behulp van een combinatie van maatregelen voor identiteit, netwerken, toepassingen, abonnementen, beheergroepen en andere maatregelen.

Zoek een compromis tussen de noodzaak om beveiligingsaspecten te scheiden en de dagelijkse werking van de systemen die met elkaar moeten communiceren en toegang hebben tot gegevens.

Zorg ervoor dat de segmentatiestrategie consistent wordt geïmplementeerd voor alle soorten maatregelen, zoals netwerkbeveiliging, identiteits- en toegangsmodellen, en modellen voor toepassingsmachtigingen/toegangs en maatregelen voor menselijke processen.

- [Guidance on segmentation strategy in Azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Guidance on segmentation strategy in Azure (document)](/security/compass/governance#enterprise-segmentation-strategy)

- [Align network segmentation with enterprise segmentation strategy](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Strategie voor beheer van beveiligingspostuur definiëren

**Richtlijnen**: U moet de risico's voor afzonderlijke assets en de omgeving waarin ze worden gehost, voortdurend meten en beperken. Geef prioriteit aan waardevolle assets en gebieden die zeer kwetsbaar zijn voor aanvallen, zoals gepubliceerde toepassingen, punten in het netwerk voor binnenkomend en uitgaand verkeer, eindpunten voor gebruikers en beheerders, enzovoort.

- [Azure Security Benchmark - Posture and vulnerability management](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Rollen en verantwoordelijkheden binnen de organisatie in overeenstemming brengen

**Richtlijnen**: Zorg ervoor dat u een duidelijke strategie voor rollen en verantwoordelijkheden in uw beveiligingsorganisatie documenteert en communiceert. Bepaal de prioriteit door een duidelijke verantwoordelijkheid te definiëren voor beveiligingsbeslissingen, het bekend maken van het personeel met het gedeelde verantwoordelijkheidsmodel en het trainen van technische teams in technologie om de cloud te beveiligen.

- [Azure Security Best Practice 1 – People: Educate Teams on Cloud Security Journey](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Azure Security Best Practice 2 - People: Educate Teams on Cloud Security Technology](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure Security Best Practice 3 - Process: Assign Accountability for Cloud Security Decisions](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Beveiligingsstrategie voor netwerk definiëren

**Richtlijnen**: Stel een aanpak voor Azure-netwerkbeveiliging op als onderdeel van de algehele strategie voor het toegangsbeheer van uw organisatie.  

Deze strategie moet gedocumenteerde richtlijnen, beleid en standaarden bevatten voor de volgende elementen: 

-   Gecentraliseerd netwerkbeheer en beveiligingsverantwoordelijkheid

-   Model voor segmentatie van het virtuele netwerk in lijn met de segmentatiestrategie van het bedrijf

-   Herstelstrategie voor verschillende bedreigings- en aanvalsscenario's

-   Strategie voor internetperiferie en inkomend en uitgaand

-   Strategie voor interconnectiviteit tussen hybride cloud en on-premises

-   Up-to-date artefacten voor netwerkbeveiliging (zoals netwerkdiagrammen en referentienetwerkarchitectuur)

Raadpleeg het volgende Engelstalige naslagmateriaal voor meer informatie:
- [Azure Security Best Practice 11 - Architecture. Single unified security strategy](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Benchmark - Network Security](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Azure network security overview](../security/fundamentals/network-overview.md)

- [Enterprise network architecture strategy](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Strategie voor identiteit en bevoegde toegang definiëren

**Richtlijnen**: Stel een aanpak voor Azure-identiteit en bevoegde toegang op als onderdeel van de algehele strategie voor het toegangsbeheer van uw organisatie.  

Deze strategie moet gedocumenteerde richtlijnen, beleid en standaarden bevatten voor de volgende elementen: 

-   Een gecentraliseerd identiteits- en verificatiesysteem en de interconnectiviteit ervan met andere interne en externe identiteitssystemen

-   Krachtige authenticatiemethoden in verschillende use-cases en onder diverse omstandigheden

-   Beveiliging van gebruikers met hoge bevoegdheden

-   Controle en afhandeling van afwijkende gebruikersactiviteiten  

-   Beoordelings- en afstemmingsproces voor gebruikers-id's en -toegang

Raadpleeg het volgende Engelstalige naslagmateriaal voor meer informatie:

- [Azure Security Benchmark - Identity management](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Azure Security Benchmark - Privileged access](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Azure Security Best Practice 11 - Architecture. Single unified security strategy](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure identity management security overview](../security/fundamentals/identity-management-overview.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Strategie voor logboekregistratie en respons bij bedreigingen definiëren

**Richtlijnen**: Stel een strategie op voor logboekregistratie en de respons bij bedreigingen om bedreigingen snel te detecteren en op te lossen terwijl u voldoet aan de nalevingsvereisten. Stel prioriteiten om analisten te voorzien van waarschuwingen van hoge kwaliteit en naadloze ervaringen, zodat ze zich kunnen concentreren op bedreigingen in plaats van integratie en handmatige stappen. 

Deze strategie moet gedocumenteerde richtlijnen, beleid en standaarden bevatten voor de volgende elementen: 

-   De rol en verantwoordelijkheden van het SecOps-team van de organisatie 

-   Een goed omschreven responsproces voor incidenten dat is afgestemd op NIST of een ander framework uit de branche 

-   Logboeken registreren en bewaren voor ondersteuning van bedreigingsdetectie, respons op incidenten en nalevingsbehoeften

-   Gecentraliseerde zichtbaarheid van en correlatiegegevens over bedreigingen, met behulp van SIEM, functies van Azure en andere bronnen 

-   Plan voor communicatie en melding aan klanten, leveranciers en openbare betrokken partijen

-   Gebruik van platforms van Azure en van derden voor het afhandelen van incidenten, zoals logboekregistratie en bedreigingsdetectie, forensics, en herstel en eliminatie van aanvallen

-   Processen voor het afhandelen van incidenten en activiteiten na incidenten, zoals geleerde lessen en bewijsbehoud

Raadpleeg het volgende Engelstalige naslagmateriaal voor meer informatie:

- [Azure Security Benchmark - Logging and threat detection](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Azure Security Benchmark - Incident response](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Azure Security Best Practice 4 - Process. Update Incident Response Processes for Cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure Adoption Framework, logging, and reporting decision guide](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure enterprise scale, management, and monitoring](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="next-steps"></a>Volgende stappen

- Lees [dit overzichtsartikel over Azure Security Benchmark V2](/azure/security/benchmarks/overview).
- Lees meer over [basislijnen voor de beveiliging van Azure](/azure/security/benchmarks/security-baselines-overview)
