---
title: Azure-beveiligings basislijn voor Azure Managed Applications
description: De Azure Managed Applications Security Baseline voorziet in procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-Bench Mark.
author: msmbaldwin
ms.service: managed-applications
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 2a5c31270f18c2e6149d93fa522818704b9747d8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100588605"
---
# <a name="azure-security-baseline-for-azure-managed-applications"></a>Azure-beveiligings basislijn voor Azure Managed Applications

In deze beveiligings basislijn worden richt lijnen van de [Azure Security Bench Mark-versie 2,0](../../security/benchmarks/overview.md) ingesteld op Azure Managed Applications. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen. De inhoud wordt gegroepeerd op de **beveiligings controles** die zijn gedefinieerd door de Azure Security-benchmark en de bijbehorende richt lijnen die van toepassing zijn op Azure Managed Applications. **Besturings elementen** die niet van toepassing zijn op Azure Managed Applications, zijn uitgesloten.

Als u wilt zien hoe Azure Managed Applications volledig is toegewezen aan de beveiligings benchmark van Azure, raadpleegt u het [volledige Azure Managed Applications beveiligings basislijn toewijzings bestand](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Benchmark: netwerkbeveiliging](../../security/benchmarks/security-controls-v2-network-security.md) voor meer informatie.*

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: netwerk beveiligings regels vereenvoudigen

**Hulp**: gebruik Azure Virtual Network Service Tags om netwerk toegangs beheer te definiëren voor netwerk beveiligings groepen of Azure firewall die zijn geconfigureerd voor de resources van uw Azure-beheerde toepassing. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de service label (bijvoorbeeld: AzureResourceManager) op te geven in het juiste bron-of doel veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen.

- [Azure Resource Manager-service tags gebruiken](../../virtual-network/service-tags-overview.md#available-service-tags)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="identity-management"></a>Identiteitsbeheer

*Zie [Azure Security Benchmark: Identiteitsbeheer](../../security/benchmarks/security-controls-v2-identity-management.md) voor meer informatie.*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Azure Active Directory standaardiseren als het centrale identiteits--en verificatiesysteem

**Hulp**: Azure Managed Applications gebruikt Azure Active Directory (Azure AD) als de standaard service voor identiteits-en toegangs beheer. Standaardiseren op Azure AD om het identiteits-en toegangs beheer van uw organisatie te bepalen.

Azure biedt de volgende ingebouwde rollen van Azure voor het verlenen van toegang tot beheerde toepassingen met Azure AD en OAuth:

- Rol van beheerde toepassing Inzender: Hiermee kunt u beheerde toepassings resources maken.

- Rol beheerde toepassings operator: Hiermee kunt u acties voor beheerde toepassings bronnen lezen en uitvoeren

- Lezer voor beheerde toepassingen: Hiermee kunt u bronnen in een beheerde app lezen en JIT-toegang aanvragen.

Raadpleeg de volgende bronnen voor meer informatie:

- [Rol Inzender beheerde toepassing](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [Rol beheerde toepassings operator](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [Lezer voor beheerde toepassingen](../../role-based-access-control/built-in-roles.md#managed-applications-reader)

- [Tenancy in Azure Active Directory](../../active-directory/develop/single-and-multi-tenant-apps.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Toepassingsidentiteiten veilig en automatisch beheren

**Hulp**: door Azure beheerde identiteiten te gebruiken voor het verlenen van machtigingen aan uw beheerde toepassingen. Het wordt aanbevolen Azure Managed Identity te gebruiken in plaats van een krachtig menselijk account te maken om uw resources te openen of uit te voeren om de nood zaak voor het beheren van aanvullende referenties te beperken. Aan de Azure Managed Applications-service kan ook een beheerde identiteit worden toegewezen om systeem eigen verificatie te kunnen uitvoeren voor andere Azure-Services/-bronnen die ondersteuning bieden voor Azure AD-verificatie. Dit kan handig zijn voor eenvoudige toegang vanaf uw Azure Managed Applications tot Azure Key Vault bij het ophalen van geheimen. Wanneer beheerde identiteiten worden gebruikt, wordt de identiteit beheerd door het Azure-platform en hoeft u geen geheimen in te richten of te draaien.

Azure Managed Applications ondersteunt uw toepassing twee typen identiteiten:

- Een door het systeem toegewezen identiteit is gekoppeld aan uw configuratie bron. Deze wordt verwijderd als uw configuratie bron wordt verwijderd. Een configuratie resource kan slechts één door het systeem toegewezen identiteit hebben.

- Een door de gebruiker toegewezen identiteit is een zelfstandige Azure-resource die kan worden toegewezen aan uw configuratie bron. Een configuratie resource kan meerdere door de gebruiker toegewezen identiteiten hebben.

Wanneer beheerde identiteiten niet kunnen worden gebruikt, maakt u een service-principal met beperkte machtigingen op het resource niveau van de door Azure beheerde toepassing. Deze service-principals met certificaat referenties configureren en terugvallen op client geheimen. In beide gevallen kunnen Azure Key Vault worden gebruikt in combi natie met door Azure beheerde identiteiten, zodat de runtime-omgeving (bijvoorbeeld een Azure function) de referentie kan ophalen uit de sleutel kluis.

- [Beheerde identiteiten voor Azure Managed Applications gebruiken](publish-managed-identity.md)

- [Door Azure beheerde identiteiten](../../active-directory/managed-identities-azure-resources/overview.md)

- [Azure-service-principal](/powershell/azure/create-azure-service-principal-azureps) 

- [Azure Key Vault gebruiken voor registratie van beveiligings-principal](../../key-vault/general/authentication.md#app-identity-and-security-principals)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Eenmalige aanmelding (SSO) van Azure AD gebruiken voor toegang tot toepassingen

**Hulp**: Azure Managed Applications Azure Active Directory gebruiken om identiteits-en toegangs beheer te bieden aan Azure-resources, Cloud toepassingen en on-premises toepassingen. Dit omvat ondernemingsidentiteiten zoals werknemers, maar ook externe identiteiten, zoals partners, verkopers en leveranciers. Zo kunt u eenmalige aanmelding (SSO) gebruiken voor het beheren en beveiligen van de gegevens en resources van uw organisatie, on premises en in de cloud. Verbind al uw gebruikers, toepassingen en apparaten met Azure AD voor naadloze, veilige toegang en meer zichtbaarheid en controle.

- [Inzicht in eenmalige aanmelding van toepassingen met Azure AD](../../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Gebruik krachtige besturingselementen voor verificatie voor alle op Azure Active Directory gebaseerde toegang

**Richt lijnen**: Azure Managed Applications gebruikt Azure Active Directory die ondersteuning biedt voor krachtige verificatie controles door middel van meervoudige verificatie en krachtige methoden met een wacht woord.
- Multi-factor Authentication: Schakel Azure AD multi-factor Authentication in en volg Azure Security Center aanbevelingen voor identiteits-en toegangs beheer voor een aantal aanbevolen procedures in de configuratie van multi-factor Authentication. multi-factor Authentication kan worden afgedwongen voor alle, Selecteer gebruikers of op het niveau per gebruiker op basis van aanmeldings voorwaarden en risico factoren.
- Verificatie zonder wachtwoord: er zijn drie verificatieopties zonder een wachtwoord beschikbaar: Windows Hello voor Bedrijven, de Microsoft Authenticator-app en on-premises verificatiemethoden zoals smartcards.

Zorg ervoor dat beheerders en bevoegde gebruikers het hoogste niveau van sterke verificatie gebruiken.

- [Multi-factor Authentication inschakelen in azure](../../active-directory/authentication/howto-mfa-getstarted.md) 

- [Inleiding tot verificatieopties zonder wachtwoord voor Azure Active Directory](../../active-directory/authentication/concept-authentication-passwordless.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Bewaken van accounts op afwijkingen en waarschuwingenbeheer

**Hulp**: Azure Managed Applications is geïntegreerd met Azure Active Directory waarin de volgende gegevens bronnen worden geleverd:
- Aanmeldingen: het rapport voor aanmeldingsactiviteit bevat informatie over het gebruik van beheerde toepassingen en aanmeldingsactiviteiten van gebruikers
- Auditlogboeken: traceerbaarheid via logboeken voor alle door diverse functies binnen Azure AD uitgevoerde wijzigingen. Voorbeelden van vermeldingen in auditlogboeken zijn wijzigingen die worden aangebracht in resources binnen Azure AD, zoals het toevoegen of verwijderen van gebruikers, apps, groepen, rollen en beleidsregels.
- Riskante aanmeldingen - Een riskante aanmelding is een indicator van een aanmeldingspoging die mogelijk is uitgevoerd door iemand die geen rechtmatige eigenaar van een gebruikersaccount is.
- Gebruikers voor wie wordt aangegeven dat ze risico lopen - Een riskante gebruiker is een indicator van een gebruikersaccount dat mogelijk is aangetast.

Deze gegevens bronnen kunnen worden geïntegreerd met Azure Monitor, Azure Sentinel of SIEM-systemen van derden.

Azure Security Center kan u ook waarschuwen voor bepaalde verdachte activiteiten, zoals een ongewoon aantal mislukte verificatiepogingen of afgeschafte accounts in het abonnement.

Azure Advanced Threat Protection (ATP) is een beveiligingsoplossing die aan de hand van signalen van Active Directory niet alleen geavanceerde bedreigingen kan identificeren, detecteren en onderzoeken, maar ook onderschepte identiteiten en schadelijke acties van binnenuit.

- [Auditrapporten over activiteiten in Azure Active Directory](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [Riskante Azure AD-aanmeldingen weergeven](../../active-directory/identity-protection/overview-identity-protection.md)

- [Identiteits- en toegangsactiviteiten van gebruikers controleren in Azure Security Center](../../security-center/security-center-identity-access.md)

- [Azure-activiteitenlogboeken integreren in Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Toegang tot Azure-resource beperken op basis van voorwaarden

**Hulp**: Azure Managed Applications biedt ondersteuning voor voorwaardelijke toegang van Azure AD voor een gedetailleerd toegangs beheer op basis van door de gebruiker gedefinieerde voor waarden, zoals gebruikers aanmeldingen vanuit bepaalde IP-bereiken, moeten multi-factor Authentication gebruiken voor aanmelding. Gedetailleerd beheerbeleid voor verificatiesessies kan ook worden gebruikt voor verschillende scenario's.

- [Overzicht van voorwaardelijke toegang in Azure](../../active-directory/conditional-access/overview.md) 

- [Veelvoorkomend beleid voor voorwaardelijke toegang](../../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [Het beheer van verificatiesessies via voorwaardelijke toegang configureren](../../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

## <a name="privileged-access"></a>Bevoegde toegang

*Zie [Azure Security Benchmark: uitgebreide toegang](../../security/benchmarks/security-controls-v2-privileged-access.md) voor meer informatie.*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Gebruikers met zeer uitgebreide bevoegdheden beveiligen en beperken

**Hulp**: Azure Managed Applications gebruikt Azure Active Directory (Azure AD) voor identiteit en toegang. De belangrijkste ingebouwde rollen zijn Azure AD zijn globale beheerder en de beheerder van de bevoegde rol als gebruikers die zijn toegewezen aan deze twee rollen, kunnen beheerders rollen overdragen:
- Globale beheerder: gebruikers met deze rol hebben toegang tot alle beheer functies in azure AD, evenals services die gebruikmaken van Azure AD-identiteiten.
- Beheerder van geprivilegieerde rol: gebruikers met deze rol kunnen roltoewijzingen beheren in azure AD, en in Azure AD Privileged Identity Management (PIM). Daarnaast kunt u met deze rol alle aspecten van PIM en administratieve eenheden beheren.

Opmerking: u kunt andere essentiële rollen hebben die moeten worden onderhevig aan de hand van aangepaste rollen waaraan bepaalde privileged permissions zijn toegewezen. Het is ook mogelijk dat u vergelijk bare besturings elementen wilt Toep assen op het beheerders account van essentiële bedrijfs activa.

U kunt bevoegdheden JIT-toegang (Just-In-Time) verlenen voor Azure-resources en Azure AD met behulp van Azure AD Privileged Identity Management (PIM). JIT verleent gebruikers alleen tijdelijke machtigingen voor het uitvoeren van bevoegde taken op het moment dat ze deze nodig hebben. PIM kan ook beveiligingswaarschuwingen genereren wanneer er verdachte of onveilige activiteiten worden vastgesteld in uw Azure AD-organisatie.

- [Machtigingen voor beheerdersrollen in Azure AD](../../active-directory/roles/permissions-reference.md)

- [Beveiligingswaarschuwingen van Azure Privileged Identity Management gebruiken](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Bevoegde toegang beveiligen voor hybride implementaties en cloudimplementaties in Azure AD](../../active-directory/roles/security-planning.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Beheerderstoegang tot essentiële bedrijfssystemen beperken

**Richt lijnen**: Azure Managed Applications gebruikt Azure RBAC om de toegang tot bedrijfskritische systemen te isoleren door te beperken welke accounts bevoegde toegang krijgen tot de abonnementen en beheer groepen waarin ze zich bevinden.

Zorg ervoor dat u ook de toegang tot de beheer-, identiteits-en beveiligings systemen beperkt die beheerders toegang hebben tot uw essentiële bedrijfs toegang, zoals Active Directory-domein controllers (Dc's), beveiligings hulpprogramma's en Systeembeheer Programma's met agents die zijn geïnstalleerd op essentiële bedrijfs systemen. Aanvallers die deze beheer-en beveiligings systemen misbruiken, kunnen ze onmiddellijk weaponize om bedrijfs kritieke activa te beschadigen.

Alle typen besturings elementen voor toegang moeten worden afgestemd op uw bedrijfs segmentatie strategie om een consistent toegangs beheer te garanderen.

- [Azure-onderdelen en referentie model](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Toegang tot beheer groepen](../../governance/management-groups/overview.md#management-group-access)

- [Beheerders van Azure-abonnementen](../../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Gebruikerstoegang regelmatig controleren en afstemmen

**Richt lijnen**: gebruikers accounts en toegangs toewijzing regel matig controleren om ervoor te zorgen dat de accounts en hun toegangs niveau geldig zijn.

Azure Managed Applications Azure Active Directory (AAD)-accounts gebruikt voor het beheren van de resources, controleert u gebruikers accounts en toegangs toewijzingen regel matig om te controleren of de accounts en hun toegang geldig zijn. U kunt Azure AD-toegangs beoordelingen gebruiken om groepslid maatschappen, de toegang tot bedrijfs toepassingen en roltoewijzingen te controleren. Azure AD Reporting kan logboeken bieden waarmee u verlopen accounts kunt detecteren. U kunt ook Azure AD Privileged Identity Management gebruiken om een werk stroom voor het lezen van rapporten te maken om het controle proces te vergemakkelijken.

Daarnaast kan Azure Privileged Identity Management ook worden geconfigureerd om te worden gewaarschuwd wanneer er een uitzonderlijk groot aantal beheerders accounts wordt gemaakt en om beheerders accounts te identificeren die verouderd of onjuist zijn geconfigureerd.

Opmerking: sommige Azure-Services ondersteunen lokale gebruikers en rollen die niet worden beheerd via Azure AD. U moet deze gebruikers afzonderlijk beheren.

- [Een toegangs beoordeling van Azure-resource rollen maken in Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Identiteits- en toegangsbeoordelingen van Azure AD](../../active-directory/governance/access-reviews-overview.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Noodtoegang instellen in Azure AD

**Hulp**: Azure Managed Applications gebruikt Azure Active Directory om de bijbehorende resources te beheren. Als u wilt voor komen dat uw Azure AD-organisatie per ongeluk wordt vergrendeld, stelt u een account voor toegang voor nood gevallen in voor toegang wanneer normale beheerders accounts niet kunnen worden gebruikt. Accounts voor noodtoegang hebben meestal zeer uitgebreide bevoegdheden en kunnen beter niet aan specifieke personen worden toegewezen. Accounts voor noodtoegang zijn alleen bestemd voor echte noodsituaties waarin normale beheerdersaccounts niet kunnen worden gebruikt.

Zorg ervoor dat de referenties (zoals wachtwoord, certificaat of smartcard) voor accounts voor noodtoegang veilig worden bewaard en alleen bekend zijn bij personen die deze alleen in een noodgeval mogen gebruiken.

- [Accounts voor noodtoegang beheren in Azure AD](../../active-directory/roles/security-emergency-access.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="pa-5-automate-entitlement-management"></a>PA-5: beheer van rechten automatiseren 

**Hulp**: Azure Managed Applications is geïntegreerd met Azure Active Directory om de bijbehorende resources te beheren. Gebruik Azure AD-functies voor rechten beheer om werk stromen voor toegangs aanvragen te automatiseren, met inbegrip van toegangs toewijzingen, beoordelingen en verval datums. Het is ook mogelijk om twee of meerdere fasen goed te keuren.

- [Wat zijn Azure AD-toegangs beoordelingen](../../active-directory/governance/access-reviews-overview.md)

- [Wat is het beheer van rechten van Azure AD](../../active-directory/governance/entitlement-management-overview.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Werkstations met uitgebreide toegang gebruiken

**Richtlijnen**: Beveiligde, geïsoleerde werkstations zijn van cruciaal belang voor de beveiliging van gevoelige rollen als beheerders, ontwikkelaars en serviceoperators met vergaande bevoegdheden. Gebruik zeer beveiligde werk stations van gebruikers en/of Azure Bastion voor beheer taken die betrekking hebben op uw beheerde toepassingen. Gebruik Azure Active Directory, Microsoft Defender Advanced Threat Protection (ATP) en/of Microsoft Intune als u een beveiligd en beheerd gebruikerswerkstation voor beheertaken wilt implementeren. De beveiligde werkstations kunnen centraal worden beheerd en beveiligde configuraties afdwingen, waaronder krachtige verificatie, software- en hardwarebasislijnen, beperkte logische toegang en netwerktoegang.

- [Meer informatie over privileged Access workstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Een werkstation met uitgebreide toegang gebruiken](/security/compass/privileged-access-deployment)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Principe van minimale bevoegdheden hanteren 

**Hulp**: Azure Managed Applications is geïntegreerd met op rollen gebaseerd toegangs beheer (RBAC) van Azure voor het beheren van de bijbehorende resources. Met Azure RBAC kunt u de toegang tot Azure-resources beheren via roltoewijzingen. U kunt deze rollen toewijzen aan gebruikers, groeperingen van service-principals en beheerde identiteiten. Er bestaan vooraf gedefinieerde, ingebouwde rollen voor bepaalde resources, en deze rollen kunnen worden geïnventariseerd of opgevraagd via tools zoals Azure CLI, Azure PowerShell en Azure Portal. De bevoegdheden die u via Azure RBAC toewijst aan resources, moeten altijd beperkt zijn tot wat vereist is voor de rollen. Dit is een aanvulling op de JIT-benadering van Azure AD Privileged Identity Management (PIM) en moet regelmatig worden geëvalueerd.

Gebruik, waar mogelijk, ingebouwde rollen om machtigingen toe te wijzen en alleen aangepaste rollen te maken wanneer dat nodig is.

Azure biedt de volgende ingebouwde rollen van Azure voor het verlenen van toegang tot beheerde toepassingen met Azure AD en OAuth:

- Rol van beheerde toepassing Inzender: Hiermee kunt u beheerde toepassings resources maken.

- Rol beheerde toepassings operator: Hiermee kunt u acties voor beheerde toepassings bronnen lezen en uitvoeren

- Lezer voor beheerde toepassingen: Hiermee kunt u bronnen in een beheerde app lezen en JIT-toegang aanvragen.

Raadpleeg de volgende bronnen voor meer informatie:

- [Rol Inzender beheerde toepassing](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [Rol beheerde toepassings operator](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [Lezer voor beheerde toepassingen](../../role-based-access-control/built-in-roles.md#managed-applications-reader)

- [Wat is Azure Role-based Access Control (Azure RBAC)](../../role-based-access-control/overview.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: goedkeurings proces kiezen voor micro soft-ondersteuning  

**Hulp**: Implementeer een goedkeurings proces voor de organisatie voor ondersteunings Scenario's waarbij micro soft mogelijk toegang nodig heeft tot uw door Azure beheerde toepassings gegevens. Klanten-lockbox is momenteel niet beschikbaar voor scenario's met beheerde toepassingen.

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Benchmark: gegevensbescherming](../../security/benchmarks/security-controls-v2-data-protection.md) voor meer informatie.*

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Gevoelige gegevens beschermen

**Richt lijnen**: voor het versleutelen van versleuteling met uw eigen sleutels kunt u uw eigen opslag account gebruiken voor de opslag van de configuratie bestanden van de beheerde toepassing.

- [Gegevens beveiliging van beheerde configuratie bestanden met uw eigen opslag](./publish-service-catalog-app.md?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: Gevoelige data-at-rest versleutelen

**Hulp**: definities van beheerde toepassingen waarmee uw toepassing en de bijbehorende resources worden gedefinieerd, kunnen worden opgeslagen in uw eigen opslag accounts die kunnen worden geconfigureerd met door de klant beheerde versleutelings sleutels.

Voor scenario's waarbij u uw eigen opslag voor definities van beheerde toepassingen niet wilt maken, biedt Azure standaard gegevens op rest-versleuteling.

- [Uw eigen opslag plaatsen voor definities van beheerde toepassingen](./publish-service-catalog-app.md?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

- [Meer informatie over versleuteling van data-at-rest in Azure](../../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [Door de klant beheerde versleutelings sleutels configureren](../../storage/common/customer-managed-keys-configure-key-vault.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Gedeeld

## <a name="asset-management"></a>Asset-management

*Zie [Azure Security Benchmark: assetmanagement](../../security/benchmarks/security-controls-v2-asset-management.md) voor meer informatie.*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Beveiligingsteam inzicht geven in risico's voor assets

**Richtlijnen**: Zorg ervoor dat beveiligingsteams de machtiging Beveiligingslezer hebben in uw Azure-tenant en -abonnementen zodat ze kunnen controleren op beveiligingsrisico's met behulp van Azure Security Center. 

Afhankelijk van de manier waarop de verantwoordelijkheden van het beveiligingsteam zijn gestructureerd, kan de controle op beveiligingsrisico's de verantwoordelijkheid zijn van een centraal beveiligingsteam of een lokaal team. Om die reden moeten beveiligingsinzichten en -risico's altijd centraal worden verzameld in een organisatie. 

De machtiging Beveiligingslezer kan breed worden toegepast op een hele tenant (hoofdbeheergroep) of in het bereik van beheergroepen of specifieke abonnementen. 

Opmerking: Er zijn mogelijk extra machtigingen vereist om inzicht te krijgen in workloads en services. 

- [Overzicht van rol Beveiligingslezer](../../role-based-access-control/built-in-roles.md#security-reader)

- [Overzicht van Azure-beheergroepen](../../governance/management-groups/overview.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Controleer of het beveiligingsteam toegang heeft tot de asset-inventaris en metagegevens

**Richt lijnen**: Tags Toep assen op uw Azure-resources, resource groepen en abonnementen om ze logisch in een taxonomie te organiseren. Elke tag bestaat uit een naam en een waardepaar. U kunt de naam Omgeving en de waarde Productie bijvoorbeeld toepassen op alle resources in de productie.

Labels die tijdens de aanmaak tijd van de beheerde app worden gegeven, worden ook toegepast op de beheerde resource groep. De uitgever van uw toepassing kan na de implementatie een eigen extra code ring van de beheerde bronnen bieden.

- [Labels op resource Managed toepassing UI-element](microsoft-common-tagsbyresource.md)

- [Query's maken met Azure Resource Graph Explorer](../../governance/resource-graph/first-query-portal.md) 

- [Voor meer informatie over het labelen van assets raadpleegt u de hand leiding resource naamgeving en Tags toevoegen](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Gebruik alleen goedgekeurde Azure-Services

**Hulp**: Azure Managed Applications ondersteunt implementaties op basis van Azure Resource Manager en het afdwingen van configuraties met behulp van Azure Policy. Gebruik Azure Policy om te controleren welke services gebruikers in uw omgeving kunnen inrichten en beperken. Gebruik Azure Resource Graph om resources binnen hun abonnementen op te vragen en te detecteren. U kunt Azure Monitor ook gebruiken om regels te maken voor het activeren van waarschuwingen wanneer een niet-goedgekeurde service wordt gedetecteerd.

- [Azure Policy configureren en beheren](../../governance/policy/tutorials/create-and-manage.md)

- [Een specifiek resource type weigeren met Azure Policy](../../governance/policy/samples/built-in-policies.md#general)

- [Query's maken met Azure Resource Graph Explorer](../../governance/resource-graph/first-query-portal.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Beveiliging van levenscyclusbeheer van assets garanderen

**Richt lijnen**: beheerde toepassings bronnen en hun verbonden beheerde resource groep kunnen worden verwijderd door de resource van de beheerde toepassing te verwijderen. Wanneer een beheerde toepassings bron wordt verwijderd, worden ook de beheerde resource groep en de inhoud ervan verwijderd. Extra levenscyclus mogelijkheden worden opgegeven door de uitgever van de toepassing, waar ze de gebruiker extra rechten kunnen geven via de levens cyclus van de onderliggende beheerde bronnen via toegestane acties. Neem contact op met de uitgever van de beheerde toepassing voor welke resources door de gebruiker worden beheerd.

- [Beheerde toepassings resources opschonen](./tutorial-create-managed-app-with-custom-provider.md?tabs=azurecli-interactive#clean-up-resources)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

**Hulp**: gebruik de voorwaardelijke toegang van Azure om de mogelijkheden van gebruikers om te communiceren met Azure Resources Manager te beperken door ' toegang blok keren ' te configureren voor de app Microsoft Azure management.

- [Voorwaardelijke toegang configureren om de toegang tot Azure-bronnen beheer te blok keren](../../role-based-access-control/conditional-access-azure-management.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

## <a name="logging-and-threat-detection"></a>Logboekregistratie en detectie van bedreigingen

*Zie [Azure Security Benchmark: logboekregistratie en detectie van bedreigingen](../../security/benchmarks/security-controls-v2-logging-threat-detection.md) voor meer informatie.*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Detectie van bedreigingen inschakelen voor Azure identiteits- en toegangsbeheer

**Richt lijnen**: Azure AD biedt de volgende gebruikers logboeken die kunnen worden weer gegeven in azure AD Reporting of geïntegreerd met Azure monitor, Azure Sentinel of andere hulpprogram MA'S voor Siem/controle voor uitgebreidere bewakings-en analyse cases: aanmeldingen-het rapport met aanmeldingen bevat informatie over het gebruik van beheerde toepassingen en aanmeldings activiteiten voor gebruikers.
Auditlogboeken: traceerbaarheid via logboeken voor alle door diverse functies binnen Azure AD uitgevoerde wijzigingen. Voorbeelden van vermeldingen in auditlogboeken zijn wijzigingen die worden aangebracht in resources binnen Azure AD, zoals het toevoegen of verwijderen van gebruikers, apps, groepen, rollen en beleidsregels.
Riskante aanmeldingen - Een riskante aanmelding is een indicator van een aanmeldingspoging die mogelijk is uitgevoerd door iemand die geen rechtmatige eigenaar van een gebruikersaccount is.
Gebruikers voor wie wordt aangegeven dat ze risico lopen - Een riskante gebruiker is een indicator van een gebruikersaccount dat mogelijk is aangetast.

Azure Security Center kan u ook waarschuwen voor bepaalde verdachte activiteiten, zoals een ongewoon aantal mislukte verificatiepogingen of afgeschafte accounts in het abonnement. Naast de basiscontrole op beveiligingshygiëne, kan de module voor bedreigingsbeveiliging van Azure Security Center ook uitgebreidere beveiligingswaarschuwingen verzamelen van afzonderlijke Azure-compute-resources (virtuele machines, containers, app service), gegevensbronnen (SQL DB en opslag) en Azure-servicelagen. Deze functie geeft u inzicht in de account afwijkingen binnen de afzonderlijke resources.

- [Auditrapporten over activiteiten in Azure Active Directory](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [Azure AD Identity Protection inschakelen](../../active-directory/identity-protection/overview-identity-protection.md)

- [Bescherming tegen bedreiging in Azure Security Center](../../security-center/azure-defender.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Logboekregistratie inschakelen voor Azure-resources

**Hulp**: activiteiten logboeken, die automatisch beschikbaar zijn, bevatten alle schrijf bewerkingen (put, post, Delete) voor uw beheerde toepassings resources, met uitzonde ring van Lees bewerkingen (Get). Activiteiten logboeken kunnen worden gebruikt om een fout te vinden bij het oplossen van problemen of om te controleren hoe een gebruiker in uw organisatie een resource heeft gewijzigd.

- [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](../../azure-monitor/essentials/diagnostic-settings.md) 

- [Logboek registratie en verschillende logboek typen in azure](../../azure-monitor/essentials/platform-logs-overview.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Beheer en analyse van beveiligingslogboek centraliseren

**Hulp**: Centraliseer logboek registratie, opslag en analyse om correlatie in te scha kelen. Zorg ervoor dat u voor elke logboek bron een gegevens eigenaar hebt toegewezen, toegangs richtlijnen, opslag locatie, welke hulpprogram ma's worden gebruikt voor het verwerken en openen van de gegevens en vereisten voor gegevens behoud.
Zorg ervoor dat u Azure-activiteiten logboeken integreert in uw centrale logboek registratie. Opname logboeken via Azure Monitor voor het verzamelen van beveiligings gegevens die zijn gegenereerd door eindpunt apparaten, netwerk bronnen en andere beveiligings systemen. Gebruik in Azure Monitor Log Analytics-werk ruimten om analyses uit te voeren en te uitvoeren en gebruik Azure Storage accounts voor lange termijn-en archiverings opslag.

Daarnaast kunt u gegevens inschakelen en vrijgeven aan Azure Sentinel of een SIEM van derden.

Veel organisaties kiezen voor het gebruik van Azure Sentinel voor ' hot ' gegevens die regel matig worden gebruikt en die worden Azure Storage voor ' koude ' gegevens die minder vaak worden gebruikt.

- [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](../../azure-monitor/essentials/diagnostic-settings.md)

- [Azure-Sentinel onboarden](../../sentinel/quickstart-onboard.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Bewaarperiode voor logboek configureren

**Richt lijnen**: Zorg ervoor dat opslag accounts of log Analytics-werk ruimten die worden gebruikt voor het opslaan van logboeken die zijn gemaakt door uw beheerde toepassings resources, de Bewaar periode voor logboek registratie hebben volgens de nalevings voorschriften van uw organisatie.
In Azure Monitor kunt u de Bewaar periode voor uw Log Analytics werk ruimte instellen volgens de nalevings voorschriften van uw organisatie. Gebruik Azure Storage, Data Lake of Log Analytics werkruimte accounts voor lange termijn-en archiverings opslag.

- [De Bewaar periode van Log Analytics Workspace configureren](../../azure-monitor/logs/manage-cost-storage.md)

- [Bron logboeken opslaan in een Azure Storage-account](../../azure-monitor/platform/resource-logs.md#send-to-azure-storage)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Benchmark: respons op incidenten](../../security/benchmarks/security-controls-v2-incident-response.md) voor meer informatie.*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Voorbereiding: responsproces voor incidenten bijwerken voor Azure

**Richtlijnen**: Zorg ervoor dat uw organisatie processen heeft gedefinieerd om te reageren op beveiligingsincidenten, dat deze processen voor Azure zijn bijgewerkt en dat ze regelmatig worden uitgevoerd om de gereedheid ervan te garanderen.

- [Beveiliging in de complete bedrijfsomgeving implementeren](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Naslaggids voor respons op incidenten](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Voorbereiding: melding van incidenten instellen

**Richtlijnen**: Contactgegevens in Azure Security Center instellen in het geval van beveiligingsincidenten Deze contactgegevens worden door Microsoft gebruikt om contact met u op te nemen als het Microsoft Security Response Center (MSRC) vaststelt dat een niet-geautoriseerde partij toegang tot uw gegevens heeft gekregen. Er zijn ook opties waarmee u incidentwaarschuwingen en -meldingen in verschillende Azure-Services kunt aanpassen aan de hand van wat u als incidentrespons nodig acht. 

- [Contactpersoon voor beveiliging instellen in Azure Security Center](../../security-center/security-center-provide-security-contact-details.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Detectie en analyse: incidenten maken op basis van waarschuwingen van hoge kwaliteit

**Richtlijnen**: Zorg ervoor dat u een proces hebt gedefinieerd om waarschuwingen van hoge kwaliteit te maken en de kwaliteit van waarschuwingen te meten. Zo kunt u op grond van wat u uit eerdere incidenten hebt geleerd een hogere prioriteit toekennen aan waarschuwingen die bestemd zijn voor analisten, zodat deze geen tijd hoeven te verspillen aan fout-positieven. 

Waarschuwingen van hoge kwaliteit kunnen worden samengesteld op basis van ervaringen uit eerdere incidenten, op grond van gevalideerde communitybronnen, en door tools te gebruiken die zijn ontworpen om waarschuwingen te genereren en op te schonen door diverse signaalbronnen samen te voegen en er correlaties tussen te ontdekken. 

Azure Security Center biedt waarschuwingen van hoge kwaliteit in veel Azure-assets. U kunt de ASC-dataconnector gebruiken om de waarschuwingen te streamen naar Azure Sentinel. Met Azure Sentinel kunt u geavanceerde waarschuwingsregels opstellen om automatisch incidenten te genereren voor onderzoek. 

Exporteer de waarschuwingen en aanbevelingen van Azure Security Center met behulp van de exportfunctie om zo beter risico's voor Azure-resources te kunnen identificeren. U kunt waarschuwingen en aanbevelingen handmatig exporteren of doorlopend.

- [Export configureren](../../security-center/continuous-export.md)

- [Waarschuwingen streamen naar Azure Sentinel](../../sentinel/connect-azure-security-center.md)

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

- [Momentopname maken van de schijf van een Windows-computer](../../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Momentopname maken van de schijf van een Linux-computer](../../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Diagnostische gegevens en geheugendumps van Microsoft Azure Ondersteuning](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Incidenten onderzoeken met Azure Sentinel](../../sentinel/tutorial-investigate-cases.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Detectie en analyse: prioriteiten toekennen aan incidenten

**Richtlijnen**: Bied context aan analisten voor de incidenten waarop ze zich eerst moeten concentreren op basis van de ernst van waarschuwingen en de gevoeligheid van assets. 

Azure Security Center wijst aan elke waarschuwing een ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of de analyse die wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau waarvan er sprake is van schadelijke opzet achter de activiteit die tot de waarschuwing heeft geleid.

Daarnaast kunt u resources markeren met behulp van tags en een naamgevingssysteem opzetten om Azure-resources te identificeren en categoriseren, met name voor resources die gevoelige gegevens verwerken. Het is uw verantwoordelijkheid om prioriteit te geven aan het oplossen van waarschuwingen op basis van de ernst van de Azure-resources en -omgeving waarin het incident heeft plaatsgevonden.

- [Beveiligingswaarschuwingen in Azure Security Center](../../security-center/security-center-alerts-overview.md)

- [Tags gebruiken om Azure-resources te organiseren](../management/tag-resources.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Insluiting, uitschakeling en herstel: het afhandelen van incidenten automatiseren

**Richtlijnen**: Automatiseer handmatige, terugkerende taken om de responstijd te versnellen en de werklast van analisten te verlichten. Het uitvoeren van handmatige taken duurt langer, waardoor elk incident trager gaat en het aantal incidenten dat een analist kan afhandelen, kleiner wordt. Daarnaast raken analisten sneller vermoeid door handmatige taken, met als gevolg dat het risico van menselijke fouten toeneemt en analisten zich minder goed kunnen concentreren. 

Gebruik voorzieningen voor de automatisering van werkstromen in Azure Security Center en Azure Sentinel om automatisch acties te activeren of een playbook uit te voeren in reactie op binnenkomende beveiligingswaarschuwingen. Het playbook voert acties uit, zoals het verzenden van meldingen, het uitschakelen van accounts en het isoleren van problematische netwerken. 

- [Werkstroomautomatisering configureren in Security Center](../../security-center/workflow-automation.md)

- [Geautomatiseerde bedreigingsreacties instellen in Azure Security Center](../../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Geautomatiseerde bedreigingsreacties instellen in Azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="posture-and-vulnerability-management"></a>Beveiligingspostuur en beveiligingsproblemen beheren

*Zie [Azure Security Benchmark: beveiligingspostuur en beveiligingsproblemen beheren](../../security/benchmarks/security-controls-v2-posture-vulnerability-management.md) voor meer informatie.*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Veilige configuraties tot stand brengen voor Azure-services 

**Richt lijnen**: beveiligings Guardrails definiëren voor infra structuur-en DevOps teams door de Azure-Services die ze gebruiken eenvoudig veilig te configureren.

Configureer Azure Policy om configuraties te controleren en af te dwingen van uw resources met betrekking tot de implementaties van uw beheerde toepassingen.

U kunt Azure-blauw drukken gebruiken om de implementatie en configuratie van services en toepassings omgevingen te automatiseren, met inbegrip van Azure Resource Manager sjablonen, Azure RBAC-toewijzingen en Azure Policy toewijzingen, in één blauw druk-definitie.

- [Cloud acceptatie Framework-landings zone op ondernemings niveau](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture#landing-zone-expanded-definition)

- [Beleidsregels voor het afdwingen van naleving maken en beheren](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprints](../../governance/blueprints/overview.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Veilige configuraties onderhouden voor Azure-services

**Hulp**: gebruik Azure Security Center om uw resources te bewaken die betrekking hebben op Azure Managed Applications en gebruik Azure Policy [deny] en [implementeren indien niet aanwezig] om beveiligde configuraties te maken.
- [Azure Policy effecten begrijpen](../../governance/policy/concepts/effects.md)

- [Beleidsregels voor het afdwingen van naleving maken en beheren](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprints](../../governance/blueprints/overview.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Voer regelmatige simulaties van aanvallen uit

**Richtlijnen**: Voer zo vaak u als u wilt penetratietests of Red Teaming-activiteiten uit op uw Azure-resources, en zorg ervoor dat alle kritieke beveiligingsbevindingen worden opgelost.
Ga te werk volgens de Microsoft Cloud Penetration Testing Rules of Engagement (Regels voor het inzetten van penetratietests voor Microsoft Cloud ) zodat u zeker weet dat uw penetratietests niet conflicteren met Microsoft-beleid. Gebruik de strategie van Microsoft en de uitvoering van Red Teaming-activiteiten, en voer een penetratietest van de live site uit op basis van een infrastructuur, services en toepassingen die door Microsoft worden beheerd.

- [Penetratietesten in Azure](../../security/fundamentals/pen-testing.md)

- [Regels voor het inzetten van penetratietests](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

## <a name="backup-and-recovery"></a>Back-up en herstel

*Zie [Azure Security Benchmark: back-up en herstel](../../security/benchmarks/security-controls-v2-backup-recovery.md) voor meer informatie.*

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: Valideer alle back-ups, inclusief door de klant beheerde sleutels

**Richt lijnen**: wanneer u de definities van uw beheerde toepassingen opslaat in uw eigen opslag account, moet u ervoor zorgen dat u alle gekoppelde door de klant beheerde sleutels kunt herstellen die worden gebruikt voor de versleuteling van het account dat is opgeslagen in azure Key Vault.

- [Uw eigen opslag plaatsen voor definities van beheerde toepassingen](./publish-service-catalog-app.md?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

- [Key Vault sleutels herstellen in azure](/powershell/module/az.keyvault/restore-azkeyvaultkey?amp;preserve-view=true&view=azps-5.1.0)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: Het risico op verloren sleutels beperken

**Richt lijnen**: als u uw eigen opslag ruimte voor uw definities van beheerde toepassingen maakt, moet u ervoor zorgen dat u maat regelen hebt om te voor komen dat de sleutels die worden gebruikt voor het versleutelen van uw definities, worden hersteld. Schakel de beveiliging van zacht verwijderen en leegmaken uit op de Azure Key Vault waarin uw door de klant beheerde sleutels worden opgeslagen om sleutels te beschermen tegen onbedoelde of schadelijke verwijdering.  

- [Uw eigen opslag plaatsen voor definities van beheerde toepassingen](./publish-service-catalog-app.md?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

- [Voorlopig verwijderen en bescherming tegen opschonen in Azure Key Vault inschakelen](../../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

## <a name="governance-and-strategy"></a>Governance en strategie

*Zie [Azure Security Benchmark: governance en strategie](../../security/benchmarks/security-controls-v2-governance-strategy.md).*

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
- [Azure Security Architecture Recommendation - Storage, data, and encryption](/azure/architecture/framework/security/storage-data-encryption?amp;bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json) (Aanbeveling voor Azure-beveiligingsarchitectuur: opslag, gegevens en versleuteling)

- [Azure Security Fundamentals - Azure Data security, encryption, and storage](../../security/fundamentals/encryption-overview.md) (Basisprincipes van Azure-beveiliging: Azure-gegevensbeveiliging, -versleuteling en -opslag)

- [Cloud Adoption Framework - Azure data security and encryption best practices](../../security/fundamentals/data-encryption-best-practices.md?amp;bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json) (Cloud Adoption Framework: best practices voor Azure-gegevensbeveiliging en -versleuteling)

- [Azure Security Benchmark - Asset management](../../security/benchmarks/security-controls-v2-asset-management.md) (Azure Security Benchmark: assetmanagement)

- [Azure Security Benchmark - Data Protection](../../security/benchmarks/security-controls-v2-data-protection.md) (Azure Security Benchmark: gegevensbeveiliging)

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

- [Azure Security Benchmark: beveiligingspostuur en beveiligingsproblemen beheren](../../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

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

- [Azure Security Benchmark: netwerkbeveiliging](../../security/benchmarks/security-controls-v2-network-security.md)

- [Overzicht van Azure-netwerkbeveiliging](../../security/fundamentals/network-overview.md)

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

- [Azure Security Benchmark: Identity management](../../security/benchmarks/security-controls-v2-identity-management.md) (Azure Security Benchmark: identiteitsbeheer)

- [Azure Security Benchmark - Privileged access](../../security/benchmarks/security-controls-v2-privileged-access.md) (Azure Security Benchmark: uitgebreide toegang)

- [Azure Security Best Practice 11: architectuur. Eén uniforme beveiligingsstrategie](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Overzicht van beveiliging met Azure-identiteitsbeheer](../../security/fundamentals/identity-management-overview.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Strategie voor logboekregistratie en respons op bedreigingen definiëren

**Richtlijnen**: Stel een strategie op voor logboekregistratie en respons op bedreigingen om bedreigingen snel te detecteren en op te lossen terwijl tegelijkertijd aan alle nalevingsvereisten wordt voldaan. Ken een hoge prioriteit toe aan het bieden van waarschuwingen van hoge kwaliteit en naadloze ervaringen aan analisten, zodat ze zich kunnen concentreren op bedreigingen in plaats van op integraties en handmatig uit te voeren stappen. 

Deze strategie moet gedocumenteerde richtlijnen, beleid en standaarden bevatten voor de volgende elementen: 

-   De rol en verantwoordelijkheden van de personen of groepen die binnen de organisatie beveiligingsactiviteiten (SecOps) uitvoeren 

-   Een goed gedefinieerd proces voor het reageren op incidenten dat is afgestemd op het NIST of een ander framework binnen de branche 

-   Logboekregistratie en retentie om ondersteuning te bieden voor de detectie van bedreigingen, het reageren op incidenten en het naleven van regelgeving

-   Informatie en correlatiegegevens over bedreigingen die centraal beschikbaar zijn via SIEM, systeemeigen Azure-mogelijkheden en andere bronnen 

-   Communicatie- en meldingenplan met uw klanten, leveranciers en openbaar belanghebbenden

-   Gebruik van systeemeigen Azure-platforms en platforms van derden voor het afhandelen van incidenten, zoals logboekregistratie en detectie van bedreigingen, forensische onderzoeken en het oplossen en uitschakelen van aanvallen

-   Processen voor het afhandelen van incidenten en activiteiten na incidenten, zoals geleerde lessen en het bewaren van bewijsmateriaal

Raadpleeg de volgende bronnen voor meer informatie:

- [Azure Security Benchmark: logboekregistratie en detectie van bedreigingen](../../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Azure Security Benchmark: respons op incidenten](../../security/benchmarks/security-controls-v2-incident-response.md)

- [Azure Security Best Practice 4: proces. Processen voor respons op incidenten bijwerken voor de cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Handleiding framework voor Azure-acceptatie, logboekregistratie en rapportagebeslissingen](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Schaalaanpassing, beheer en bewaking van Azure Enterprise](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="next-steps"></a>Volgende stappen

- Zie [Overzicht Azure Security Benchmark V2](../../security/benchmarks/overview.md)
- Meer informatie over [Azure-beveiligingsbasislijnen](../../security/benchmarks/security-baselines-overview.md)
