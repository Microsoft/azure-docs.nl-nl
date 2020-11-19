---
title: Azure-beveiligings basislijn voor Azure Migrate
description: De Azure Migrate Security Baseline voorziet in procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-Bench Mark.
author: msmbaldwin
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: a41f37c844fe4b5b3c940d0fce51589af73385d8
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94918124"
---
# <a name="azure-security-baseline-for-azure-migrate"></a>Azure-beveiligings basislijn voor Azure Migrate

In deze beveiligings basislijn worden richt lijnen van de [Azure Security Bench Mark-versie 1,0](../security/benchmarks/overview-v1.md) ingesteld op Azure Migrate. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen.
De inhoud wordt gegroepeerd op de **beveiligings controles** die zijn gedefinieerd door de Azure Security-benchmark en de bijbehorende richt lijnen die van toepassing zijn op Azure Migrate. **Besturings elementen** die niet van toepassing zijn op Azure migrate, zijn uitgesloten.

 
Als u wilt zien hoe Azure Migrate volledig is toegewezen aan de beveiligings benchmark van Azure, raadpleegt u het [volledige Azure migrate beveiligings basislijn toewijzings bestand](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="identity-management"></a>Identiteitsbeheer

*Zie [Azure Security Bench Mark: Identity Management](/azure/security/benchmarks/security-controls-v2-identity-management)(Engelstalig) voor meer informatie.*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>Chat-1: Azure Active Directory standaardiseren als het centrale identiteits-en verificatie systeem

**Hulp**: Azure Migrate gebruikt Azure Active Directory (Azure AD) als de standaard service voor identiteits-en toegangs beheer. U moet Azure AD standaardiseren om het identiteits-en toegangs beheer van uw organisatie in te bepalen:

 Microsoft Cloud resources, zoals de Azure Portal, Azure Storage, virtuele machine van Azure (Linux en Windows), Azure Key Vault, PaaS en SaaS-toepassingen.

 De resources van uw organisatie, zoals toepassingen op Azure of uw bedrijfs netwerk resources.

Het beveiligen van Azure AD moet een hoge prioriteit hebben in de Cloud beveiligings procedure van uw organisatie. Azure AD biedt een beveiligde score voor identiteiten om u te helpen bij het beoordelen van identiteits beveiliging postuur ten opzichte van de aanbevelingen van micro soft best practice. Gebruik de score om te meten hoe nauw keurig uw configuratie overeenkomt met best practice aanbevelingen en om verbeteringen aan te brengen in uw beveiligings postuur.

Azure AD biedt ondersteuning voor externe identiteit waarmee gebruikers zonder Microsoft-account zich kunnen aanmelden bij hun toepassingen en resources met hun externe identiteit.

- [Tenancy in Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Een Azure AD-exemplaar maken en configureren](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Externe ID-providers voor de toepassing gebruiken](/azure/active-directory/b2b/identity-providers) 

- [Wat is de identiteit van beveiligde scores in Azure Active Directory](../active-directory/fundamentals/identity-secure-score.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: eenmalige aanmelding (SSO) van Azure AD gebruiken voor toegang tot toepassingen

**Hulp**: Azure Migrate gebruikt Azure Active Directory om identiteits-en toegangs beheer te bieden aan Azure-resources, Cloud toepassingen en on-premises toepassingen. Dit omvat ondernemings identiteiten zoals werk nemers, evenals externe identiteiten, zoals partners, leveranciers en leveranciers. Zo kunt u eenmalige aanmelding (SSO) gebruiken voor het beheren en beveiligen van de gegevens en bronnen van uw organisatie op locatie en in de Cloud. Verbind al uw gebruikers, toepassingen en apparaten met Azure AD voor naadloze, veilige toegang en meer zicht baarheid en controle.

- [Meer informatie over de SSO van de toepassing met Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>Chat-4: gebruik sterke verificatie-instellingen voor alle toegang op basis van Azure Active Directory

**Richt lijnen**: Azure Migrate gebruikt Azure Active Directory die ondersteuning biedt voor krachtige verificatie-instellingen via multi-factor Authentication (MFA) en krachtige methoden met een wacht woord.

Multi-factor Authentication: Schakel Azure AD MFA in en volg Azure Security Center aanbevelingen voor identiteits-en toegangs beheer voor een aantal aanbevolen procedures in de MFA-installatie. MFA kan worden afgedwongen voor alle, Selecteer gebruikers of op het niveau per gebruiker op basis van aanmeldings voorwaarden en risico factoren.

Verificatie met wacht woord: er zijn drie verificatie opties met een wacht woord beschikbaar: Windows hello voor bedrijven, Microsoft Authenticator-app en on-premises verificatie methoden zoals Smart Cards.

Voor beheerders en bevoegde gebruikers zorgt u ervoor dat het hoogste niveau van de methode voor sterke verificatie wordt gebruikt, gevolgd door het juiste beleid voor sterke authenticatie voor andere gebruikers te implementeren.

- [MFA inschakelen in azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Inleiding tot verificatie opties met een wacht woord voor Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md) 

- [Standaard wachtwoord beleid voor Azure AD](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts) 

- [Verwijder beschadigde wacht woorden met Azure AD-wachtwoord beveiliging](../active-directory/authentication/concept-password-ban-bad.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: controleren en waarschuwen voor afwijkingen van het account

**Hulp**: Azure migrate is geïntegreerd met Azure Active Directory waarin de volgende gegevens bronnen worden geleverd:

Aanmeldingen: het rapport met aanmeldingen bevat informatie over het gebruik van beheerde toepassingen en aanmeldings activiteiten voor gebruikers.

Auditlogboeken: traceerbaarheid via logboeken voor alle door diverse functies binnen Azure AD uitgevoerde wijzigingen. Voor beelden van audit logboeken zijn wijzigingen die zijn aangebracht in resources binnen Azure AD, zoals het toevoegen of verwijderen van gebruikers, apps, groepen, rollen en beleid.

Risk ante aanmeldingen: een Risk ante aanmelding is een indicator voor een aanmeldings poging die mogelijk is uitgevoerd door iemand die geen rechtmatige eigenaar van een gebruikers account is.

Gebruikers voor wie wordt aangegeven dat ze risico lopen - Een riskante gebruiker is een indicator van een gebruikersaccount dat mogelijk is aangetast.

Deze gegevens bronnen kunnen worden geïntegreerd met Azure Monitor, Azure Sentinel of SIEM-systemen van derden.

Azure Security Center kunt ook waarschuwen voor bepaalde verdachte activiteiten, zoals overmatig aantal mislukte verificatie pogingen, afgeschafte accounts in het abonnement.

Azure Advanced Threat Protection (ATP) is een beveiligings oplossing die Active Directory signalen kan gebruiken om geavanceerde bedreigingen, verdachte identiteiten en schadelijke Insider-acties te identificeren, te detecteren en te onderzoeken.

- [Rapporten met controle activiteiten in de Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Risk ante aanmeldingen voor Azure AD weer geven](/azure/active-directory/reports-monitoring/concept-risky-sign-ins) 

- [Azure AD-gebruikers identificeren die zijn gemarkeerd voor Risk ante activiteiten](/azure/active-directory/reports-monitoring/concept-user-at-risk) 

- [Identiteits-en toegangs activiteiten van gebruikers controleren in Azure Security Center](../security-center/security-center-identity-access.md) 

- [Waarschuwingen in de module Threat Intelligence-beveiliging van Azure Security Center](../security-center/alerts-reference.md) 

- [Azure-activiteiten logboeken integreren in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

## <a name="privileged-access"></a>Privileged Access

*Zie [Azure Security Bench Mark: Privileged Access](/azure/security/benchmarks/security-controls-v2-privileged-access)(Engelstalig) voor meer informatie.*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: beheer toegang beperken tot essentiële bedrijfs systemen

**Richt lijnen**: Azure migrate gebruikt Azure RBAC om de toegang tot bedrijfskritische systemen te isoleren door te beperken welke accounts bevoegde toegang krijgen tot de abonnementen en beheer groepen waarin ze zich bevinden.

Zorg ervoor dat u ook de toegang tot de beheer-, identiteits-en beveiligings systemen beperkt die beheerders toegang hebben tot uw essentiële bedrijfs toegang, zoals Active Directory-domein controllers (Dc's), beveiligings hulpprogramma's en Systeembeheer Programma's met agents die zijn geïnstalleerd op essentiële bedrijfs systemen. Aanvallers die deze beheer-en beveiligings systemen misbruiken, kunnen ze onmiddellijk weaponize om bedrijfs kritieke activa te beschadigen.

Alle typen besturings elementen voor toegang moeten worden afgestemd op uw bedrijfs segmentatie strategie om een consistent toegangs beheer te garanderen.

- [Azure-onderdelen en referentie model](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Toegang tot beheer groepen](../governance/management-groups/overview.md#management-group-access) 

- [Beheerders van Azure-abonnementen](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: gebruikers toegang regel matig controleren en afstemmen

**Hulp**: Azure migrate Azure Active Directory accounts gebruikt voor het beheren van de resources, controleert u de gebruikers accounts en toegangs toewijzingen regel matig om te controleren of de accounts en hun toegang geldig zijn. U kunt Azure AD-toegangs beoordelingen gebruiken om groepslid maatschappen, de toegang tot bedrijfs toepassingen en roltoewijzingen te controleren. Azure AD Reporting kan logboeken bieden waarmee u verlopen accounts kunt detecteren. U kunt ook Azure AD Privileged Identity Management gebruiken om een werk stroom voor het lezen van rapporten te maken om het controle proces te vergemakkelijken.

Daarnaast kan Azure Privileged Identity Management ook worden geconfigureerd om te worden gewaarschuwd wanneer er een uitzonderlijk groot aantal beheerders accounts wordt gemaakt en om beheerders accounts te identificeren die verouderd of onjuist zijn geconfigureerd.

Sommige Azure-Services ondersteunen lokale gebruikers en rollen die niet worden beheerd via Azure AD. U moet deze gebruikers afzonderlijk beheren.

- [Een toegangs beoordeling van Azure-resource rollen maken in Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Azure AD-identiteits-en toegangs beoordelingen gebruiken](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Emergency Access instellen in azure AD

**Hulp**: Azure Migrate gebruikt Azure Active Directory om de bijbehorende resources te beheren. Als u wilt voor komen dat uw Azure AD-organisatie per ongeluk wordt vergrendeld, stelt u een account voor toegang voor nood gevallen in voor toegang wanneer normale beheerders accounts niet kunnen worden gebruikt. Accounts voor toegang in nood gevallen zijn meestal zeer goed beschermd en ze mogen niet worden toegewezen aan specifieke personen. Accounts voor toegang in nood gevallen zijn beperkt tot scenario's met nood gevallen of ' afbreek glazen ', waarbij normale beheerders accounts niet kunnen worden gebruikt.

Zorg ervoor dat de referenties (zoals wacht woord, certificaat of Smart Card) voor accounts voor toegang in nood gevallen beveiligd blijven en alleen bekend zijn bij personen die alleen in een nood geval mogen gebruiken.

- [Accounts voor nood toegang beheren in azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: bevoorrechte toegang gebruiken werk stations

**Richt lijnen**: beveiligde, geïsoleerde werk stations zijn van cruciaal belang voor de beveiliging van gevoelige rollen als beheerders, ontwikkel aars en essentiële service operators. Gebruik zeer beveiligde werk stations van gebruikers en/of Azure Bastion voor beheer taken. Gebruik Azure Active Directory, micro soft Defender Advanced Threat Protection (ATP) en/of Microsoft Intune voor het implementeren van een beveiligd en beheerd gebruikers werkstation voor beheer taken. De beveiligde werk stations kunnen centraal worden beheerd voor het afdwingen van beveiligde configuratie, waaronder sterke authenticatie, software-en hardware-basis lijnen, beperkte logische en netwerk toegang.

- [Meer informatie over privileged Access workstations](../active-directory/devices/concept-azure-managed-workstation.md) 

- [Een privileged Access-werk station implementeren](../active-directory/devices/howto-azure-managed-workstation.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Volg gewoon voldoende beheer (minimale bevoegdheids methode) 

**Hulp**: Azure migrate is geïntegreerd met op rollen gebaseerd toegangs beheer (RBAC) van Azure voor het beheren van de bijbehorende resources. Met Azure RBAC kunt u toegang tot Azure-resources beheren via roltoewijzingen. U kunt deze rollen toewijzen aan gebruikers, groeperingen van service-principals en beheerde identiteiten. Er zijn vooraf gedefinieerde ingebouwde rollen voor bepaalde resources, en deze rollen kunnen worden geïnventariseerd of opgevraagd via hulpprogram ma's als Azure CLI, Azure PowerShell of de Azure Portal. De bevoegdheden die u toewijst aan resources via Azure RBAC, moeten altijd beperkt zijn tot wat vereist is voor de rollen. Dit is een aanvulling op de just-in-time (JIT) benadering van Azure AD Privileged Identity Management (PIM) en moet regel matig worden gecontroleerd.

Gebruik ingebouwde rollen om machtigingen toe te wijzen en alleen aangepaste rollen te maken wanneer dat nodig is.

Wat is Azure op rollen gebaseerd toegangs beheer (Azure RBAC).. /role-based-access-control/overview.md 

- [RBAC configureren in azure](../role-based-access-control/role-assignments-portal.md) 

- [Azure AD-identiteits-en toegangs beoordelingen gebruiken](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Bench Mark: Data Protection](/azure/security/benchmarks/security-controls-v2-data-protection)voor meer informatie.*

### <a name="dp-2-protect-sensitive-data"></a>DP-2: gevoelige gegevens beveiligen

**Hulp**: het hulp programma voor server migratie in azure migrate repliceert gegevens van de schijven van servers die worden gemigreerd naar opslag accounts en beheerde schijven in uw Azure-abonnement. Gegevens verwerking is een tijdelijke lade die wordt geschreven naar opslag accounts of beheerde schijven in het abonnement en die niet is opgeslagen in Azure Migrate

Beveilig gevoelige gegevens die zijn opgeslagen op opslag accounts en beheerde schijven door de toegang te beperken met behulp van Azure Role based Access Control (Azure RBAC) en op het netwerk gebaseerde toegangs controles, en specifieke besturings elementen in Azure-Services (zoals Storage Service Encryption voor Managed Disks). 

Voor het onderliggende platform, dat wordt beheerd door micro soft, behandelt micro soft alle klant inhoud als gevoelig en bescherming tegen verlies en bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure beveiligd blijven, heeft micro soft enkele standaard besturings elementen voor gegevens bescherming geïmplementeerd.

- [Access Control op basis van Azure Role (RBAC)](../role-based-access-control/overview.md) 

- [Informatie over beveiliging van klanten in azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: gevoelige gegevens tijdens de overdracht versleutelen

**Richt lijnen**: als aanvulling op de toegangs controle, moeten gegevens in transit worden beschermd tegen buiten-band-aanvallen (zoals het vastleggen van verkeer) met behulp van versleuteling om ervoor te zorgen dat aanvallers de gegevens niet eenvoudig kunnen lezen of wijzigen.

Azure Migrate ondersteunt gegevens versleuteling tijdens de overdracht met TLS v 1.2 of hoger.

Hoewel dit optioneel is voor verkeer op particuliere netwerken, is dit van cruciaal belang voor verkeer op externe en open bare netwerken. Voor HTTP-verkeer moet u ervoor zorgen dat alle clients (met inbegrip van het Azure Migrate apparaat en andere computers waarop u Azure Migrate software hebt geïnstalleerd) verbinding maken met uw Azure-resources, TLS v 1.2 of hoger kunnen onderhandelen. Voor extern beheer gebruikt u SSH (voor Linux) of RDP/TLS (voor Windows) in plaats van een niet-versleuteld protocol. Verouderde SSL-, TLS-en SSH-versies en-protocollen en zwakke cijfers moeten worden uitgeschakeld.

Azure biedt standaard versleuteling voor gegevens in transit tussen Azure-data centers.

- [Meer informatie over versleuteling in transit met Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) 

- [Informatie over TLS-beveiliging](/security/engineering/solving-tls1-problem) 

- [Dubbele versleuteling voor Azure-gegevens in transit](../security/fundamentals/double-encryption.md#data-in-transit)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: gedeeld

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: gevoelige gegevens in rust versleutelen

**Hulp**: Azure migrate blijft geen gevoelige gegevens behouden. Alle gegevens die zijn opgeslagen in Azure Migrate, worden op rest versleuteld met door micro soft beheerde sleutels.

Het hulp programma voor server migratie in Azure Migrate repliceert gegevens van de schijven van servers die worden gemigreerd naar opslag accounts en beheerde schijven in uw Azure-abonnement. De schijven kunnen gevoelige gegevens bevatten. Gegevens verwerking is een tijdelijke lade die wordt geschreven naar opslag accounts of beheerde schijven in het abonnement en is niet permanent in Azure Migrate. Gerepliceerde gegevens op het opslag account en beheerde schijven worden op rest versleuteld met door micro soft beheerde sleutels. Voor uiterst gevoelige gegevens beschikt u over opties voor het implementeren van extra versleuteling op rest met door de klant beheerde sleutels op het opslag account en de beheerde schijven.

- [Virtuele VMware-machines migreren naar Azure met behulp van SSE (server-side Encryption) en door de klant beheerde sleutels (CMK)](how-to-migrate-vmware-vms-with-cmk-disks.md)

- [Versleuteling van opslag account configureren met door de klant beheerde sleutels](../storage/common/customer-managed-keys-configure-key-vault.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: gedeeld

## <a name="asset-management"></a>Asset-management

*Zie [Azure Security Bench Mark: Asset Management](/azure/security/benchmarks/security-controls-v2-asset-management)voor meer informatie.*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: controleren of het beveiligings team inzicht heeft in Risico's voor assets

**Hulp**: Zorg ervoor dat beveiligings teams machtigingen voor beveiligings lezers krijgen in uw Azure-Tenant en-abonnementen zodat ze kunnen controleren op beveiligings Risico's met behulp van Azure Security Center. 

Afhankelijk van de manier waarop de verantwoordelijkheden van het beveiligings team zijn gestructureerd, kan bewaking voor beveiligings Risico's de verantwoordelijkheid zijn van een centraal beveiligings team of een lokaal team. Dat wil zeggen dat beveiligings inzichten en-risico's altijd centraal moeten worden geaggregeerd in een organisatie. 

Machtigingen voor beveiligings lezers kunnen breed worden toegepast op een hele Tenant (hoofd beheer groep) of in het bereik van beheer groepen of specifieke abonnementen. 

Er zijn mogelijk extra machtigingen vereist om inzicht te krijgen in werk belastingen en services. 

- [Overzicht van de rol van beveiligings lezer](../role-based-access-control/built-in-roles.md#security-reader)

- [Overzicht van Azure Beheergroepen](../governance/management-groups/overview.md)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: controleren of het beveiligings team toegang heeft tot de inventaris en meta gegevens van de Asset

**Richt lijnen**: Tags Toep assen op uw Azure-resources, resource groepen en abonnementen om ze logisch in een taxonomie te organiseren. Elke tag bestaat uit een naam en een waardepaar. U kunt de naam Omgeving en de waarde Productie bijvoorbeeld toepassen op alle resources in de productie.

Azure Migrate staat het uitvoeren van een toepassing of installatie van software op de bijbehorende resources niet toe. 

- [Query's maken met Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md) 

- [Azure Security Center Asset Inventory Management](../security-center/asset-inventory.md) 

- [Handleiding voor beslissingen over taggen en naamgeving voor resources](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: alleen goedgekeurde Azure-Services gebruiken

**Hulp**: gebruik Azure Policy om te controleren welke Services gebruikers in uw omgeving kunnen inrichten en beperken. Gebruik Azure resource Graph om bronnen in hun abonnementen op te vragen en te detecteren. U kunt ook Azure Monitor gebruiken om regels te maken voor het activeren van waarschuwingen wanneer een niet-goedgekeurde service wordt gedetecteerd.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md) 

- [Een specifiek resource type weigeren met Azure Policy](../governance/policy/samples/built-in-policies.md#general) 

- [Query's maken met Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: zorg voor de beveiliging van levenscyclus beheer van middelen

**Richt lijnen**: niet van toepassing. Azure Migrate kan niet worden gebruikt om de beveiliging van assets in een levenscyclus beheer proces te garanderen. Het is de verantwoordelijkheid van de klant om kenmerken en netwerk configuraties van activa te onderhouden die als hoge impact worden beschouwd. Het is raadzaam dat de klant een proces maakt om het kenmerk en de wijzigingen in de netwerk configuratie vast te leggen, de wijzigings invloed te meten en herstel taken te maken, indien van toepassing.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

**Hulp**: gebruik de voorwaardelijke toegang van Azure om de mogelijkheden van gebruikers om te communiceren met Azure Resources Manager te beperken door ' toegang blok keren ' te configureren voor de app Microsoft Azure management.

Configureren
- [Voorwaardelijke toegang om de toegang tot Azure-bronnen beheer te blok keren](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="logging-and-threat-detection"></a>Logboek registratie en detectie van bedreigingen

*Zie [Azure Security Bench Mark: Logging and Threat Detection](/azure/security/benchmarks/security-controls-v2-logging-threat-protection)(Engelstalig) voor meer informatie.*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: detectie van bedreigingen inschakelen voor Azure-identiteits-en toegangs beheer

**Richt lijnen**: Azure AD biedt de volgende gebruikers logboeken die kunnen worden weer gegeven in azure AD Reporting of geïntegreerd met Azure monitor, Azure Sentinel of andere hulpprogram MA'S voor Siem/controle voor geavanceerde bewakings-en analyse-gebruiks voorbeelden:

- Aanmeldingen: het rapport met aanmeldingen bevat informatie over het gebruik van beheerde toepassingen en aanmeldings activiteiten voor gebruikers.

Auditlogboeken: traceerbaarheid via logboeken voor alle door diverse functies binnen Azure AD uitgevoerde wijzigingen. Voor beelden van audit logboeken zijn wijzigingen die zijn aangebracht in resources binnen Azure AD, zoals het toevoegen of verwijderen van gebruikers, apps, groepen, rollen en beleid.

- Riskante aanmeldingen - Een riskante aanmelding is een indicator van een aanmeldingspoging die mogelijk is uitgevoerd door iemand die geen rechtmatige eigenaar van een gebruikersaccount is.

- Gebruikers voor wie wordt aangegeven dat ze risico lopen - Een riskante gebruiker is een indicator van een gebruikersaccount dat mogelijk is aangetast.

Azure Security Center kunt ook waarschuwen voor bepaalde verdachte activiteiten, zoals overmatig aantal mislukte verificatie pogingen, afgeschafte accounts in het abonnement. Naast de basis bewaking voor beveiligings hygiëne, kan de bedreigings beveiligings module van Azure Security Center ook uitgebreidere beveiligings waarschuwingen verzamelen van afzonderlijke Azure Compute-resources (virtuele machines, containers, app service), gegevens bronnen (SQL DB en opslag) en Azure-service lagen. Met deze functie kunt u inzicht krijgen in de account afwijkingen binnen de afzonderlijke resources.

- [Rapporten met controle activiteiten in de Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Azure Identity Protection inschakelen](../active-directory/identity-protection/overview-identity-protection.md) 

- [Bescherming tegen bedreiging in Azure Security Center](/azure/security-center/threat-protection)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: logboek registratie inschakelen voor Azure-resources

**Hulp**: activiteiten logboeken, die automatisch beschikbaar zijn, bevatten alle schrijf bewerkingen (put, post, Delete) voor uw Azure migrate resources, behalve Lees bewerkingen (Get). Activiteiten logboeken kunnen worden gebruikt om een fout te vinden bij het oplossen van problemen of om te controleren hoe een gebruiker in uw organisatie een resource heeft gewijzigd.

- [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Logboek registratie en verschillende logboek typen in azure](../azure-monitor/platform/platform-logs-overview.md) 

Azure Migrate maakt momenteel geen Azure-resource Logboeken.

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: beveiligings logboek beheer en-analyse centraliseren

**Richt lijnen**: opslag en analyse van logboek registratie centraliseren om correlatie in te scha kelen. Zorg ervoor dat u voor elke logboek bron een gegevens eigenaar hebt toegewezen, toegangs richtlijnen, opslag locatie, welke hulpprogram ma's worden gebruikt voor het verwerken en openen van de gegevens en vereisten voor gegevens behoud.

Zorg ervoor dat u Azure-activiteiten logboeken integreert in uw centrale logboek registratie. Opname logboeken via Azure Monitor voor het verzamelen van beveiligings gegevens die zijn gegenereerd door eindpunt apparaten, netwerk bronnen en andere beveiligings systemen. Gebruik in Azure Monitor Log Analytics-werk ruimten om analyses uit te voeren en te uitvoeren en gebruik Azure Storage accounts voor lange termijn-en archiverings opslag.

Daarnaast kunt u gegevens inschakelen en vrijgeven aan Azure Sentinel of een SIEM van derden.

Veel organisaties kiezen voor het gebruik van Azure Sentinel voor ' hot ' gegevens die regel matig worden gebruikt en die worden Azure Storage voor ' koude ' gegevens die minder vaak worden gebruikt.

- [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Bench Mark: Incident Response](/azure/security/benchmarks/security-controls-v2-incident-response)(Engelstalig) voor meer informatie.*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: voor bereiding-respons proces van incidenten bijwerken voor Azure

**Hulp**: Zorg ervoor dat uw organisatie processen heeft om te reageren op beveiligings incidenten, dat deze processen voor Azure zijn bijgewerkt en dat ze regel matig de voor bereidingen spelen.

- [Implementeer beveiliging in de bedrijfs omgeving](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Naslag Gids voor respons op incidenten](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: voor bereiding-incident melding instellen

**Richt lijnen**: contact gegevens voor beveiligings incidenten instellen in azure Security Center. Deze contact gegevens worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat uw gegevens zijn geopend door een onrecht matige of niet-gemachtigde partij. U hebt ook opties voor het aanpassen van incident waarschuwingen en meldingen in verschillende Azure-Services op basis van de behoeften van uw incident respons. 

- [De Azure Security Center Security-contact persoon instellen](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: detectie en analyse: incidenten maken op basis van waarschuwingen van hoge kwaliteit

**Richt lijnen**: Zorg ervoor dat u een proces hebt om waarschuwingen van hoge kwaliteit te maken en de kwaliteit van waarschuwingen te meten. Zo kunt u lessen uit eerdere incidenten ontdekken en waarschuwingen voor analisten prioriteiten geven, zodat ze geen tijd verspillen op valse positieven. 

Waarschuwingen van hoge kwaliteit kunnen worden gebouwd op basis van ervaringen van eerdere incidenten, gevalideerde community-bronnen en hulpprogram ma's die zijn ontworpen om waarschuwingen te genereren en op te schonen door diverse signaal bronnen te weigeren en te correleren. 

Azure Security Center biedt waarschuwingen van hoge kwaliteit in veel Azure-assets. U kunt de ASC data connector gebruiken om de waarschuwingen naar Azure Sentinel te streamen. Met Azure Sentinel kunt u geavanceerde waarschuwings regels maken om incidenten automatisch te genereren voor een onderzoek. 

Exporteer uw Azure Security Center waarschuwingen en aanbevelingen met behulp van de export functie om Risico's voor Azure-resources te identificeren. Waarschuwingen en aanbevelingen hand matig of op een doorlopende manier exporteren.

- [Exporteren configureren](../security-center/continuous-export.md)

- [Waarschuwingen streamen naar Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: detectie en analyse: een incident onderzoeken

**Richt lijnen**: Zorg ervoor dat analisten verschillende gegevens bronnen kunnen opvragen en gebruiken bij het onderzoeken van mogelijke incidenten, om een volledig overzicht te maken van wat er is gebeurd. Diverse logboeken moeten worden verzameld om de activiteiten van een mogelijke aanvaller in de Kill-keten te volgen om blinde vlekken te voor komen.  U moet er ook voor zorgen dat inzichten en informatie worden vastgelegd voor andere analisten en voor toekomstige historische Naslag informatie.  

De gegevens bronnen voor onderzoek bevatten de gecentraliseerde logboek registratie bronnen die al worden verzameld van de services binnen het bereik en het uitvoeren van systemen, maar kan ook het volgende omvatten:

- Netwerk gegevens: gebruik stroom logboeken voor netwerk beveiligings groepen, Azure Network Watcher en Azure Monitor om netwerk stroom logboeken en andere analyse-informatie vast te leggen. 

- Moment opnamen van actieve systemen: 

    - Gebruik de functie voor moment opnamen van de virtuele machine van Azure om een moment opname te maken van de schijf waarop het systeem wordt uitgevoerd. 

    - Gebruik de systeem eigen geheugen dump functie van het besturings systeem om een moment opname te maken van het actieve systeem geheugen.

    - Gebruik de functie snap shot van de Azure-Services of de eigen mogelijkheid van uw software om moment opnamen van de actieve systemen te maken.

Azure Sentinel biedt uitgebreide gegevens analyse voor vrijwel elke logboek bron en een case beheer Portal om de volledige levens duur van incidenten te beheren. Informatie over Intelligence tijdens een onderzoek kan worden gekoppeld aan een incident voor het bijhouden en rapporteren van de doel einden. 

- [Moment opname maken van de schijf van een Windows-computer](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Moment opname maken van de schijf van een Linux-machine](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure ondersteuning voor diagnostische gegevens en geheugen dump verzameling](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Incidenten onderzoeken met Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: detectie en analyse: de prioriteit van incidenten bepalen

**Richt lijnen**: Geef een context voor analisten waarop incidenten zich richten op basis van ernst van de waarschuwing en de gevoeligheid van een activa. 

Azure Security Center wijst aan elke waarschuwing een Ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of het analyse programma dat wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau waarvan er sprake is van schadelijke opzet achter de activiteit die tot de waarschuwing heeft geleid.

Daarnaast kunt u resources markeren met behulp van tags en een naamgevings systeem maken om Azure-resources te identificeren en categoriseren, met name voor de verwerking van gevoelige gegevens.  Het is uw verantwoordelijkheid om prioriteit te geven aan het herstel van waarschuwingen op basis van de ernst van de Azure-resources en-omgeving waar het incident heeft plaatsgevonden.

- [Beveiligingswaarschuwingen in Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Tags gebruiken om Azure-resources te organiseren](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: insluiting, uitroeiing en herstel: de verwerking van incidenten automatiseren

**Richt lijnen**: Automatiseer hand matige terugkerende taken om de reactie tijd te versnellen en de overhead voor analisten te verlagen. Het uitvoeren van hand matige taken duurt langer om uit te voeren, elk incident te vertragen en te verminderen hoeveel incidenten een analist kan verwerken. Hand matige taken verg Roten ook analisten intensief, waardoor het risico van menselijke fout wordt verg root dat vertragingen veroorzaakt, en de mogelijkheid van analisten om effectief te best Eden aan complexe taken te degraderen. Gebruik werk stroom automatiserings functies in Azure Security Center en Azure Sentinel om automatisch acties te activeren of een Playbook uit te voeren om te reageren op binnenkomende beveiligings waarschuwingen. De Playbook neemt acties, zoals het verzenden van meldingen, het uitschakelen van accounts en het isoleren van problematische netwerken. 

- [Werk stroom automatisering configureren in Security Center](../security-center/workflow-automation.md)

- [Automatische reacties op dreigingen instellen in Azure Security Center](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Automatische bedreigings reacties instellen in azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

## <a name="posture-and-vulnerability-management"></a>Postuur en beveiligings beheer

*Zie voor meer informatie de [Azure Security Bench Mark: postuur en het beveiligings beleid](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: regel matige aanvals simulatie uitvoeren

**Richt lijnen**: u kunt zo nodig indringings tests of rode team activiteiten uitvoeren op uw Azure-resources en ervoor zorgen dat alle essentiële beveiligings resultaten worden hersteld.
Volg de Microsoft Cloud regels voor het testen van de indringings fase om ervoor te zorgen dat uw indringings tests niet worden geschonden door het micro soft-beleid. Gebruik de strategie van micro soft en de uitvoering van de implementatie van de indringing van een live site in de Cloud, services en toepassingen die door micro soft worden beheerd.

- [Indringings tests in azure](../security/fundamentals/pen-testing.md)

- [Regels van betrokkenheid voor het testen van indringing](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud rode teams](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: gedeeld

## <a name="governance-and-strategy"></a>Governance en strategie

*Zie [Azure Security Bench Mark: governance en strategie](/azure/security/benchmarks/security-controls-v2-governance-strategy)voor meer informatie.*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: de strategie voor Asset Management en gegevens bescherming definiëren 

**Richt lijnen**: Zorg ervoor dat u een duidelijke strategie documenteert en communiceert voor continue bewaking en bescherming van systemen en gegevens. Volg prioriteiten voor detectie, beoordeling, beveiliging en bewaking van bedrijfs kritieke gegevens en systemen. 

Deze strategie moet gedocumenteerde richt lijnen, beleid en standaarden bevatten voor de volgende elementen: 

-   Standaard gegevens classificatie in overeenstemming met de zakelijke Risico's

-   Zicht baarheid van beveiligings organisaties in Risico's en activa-inventaris 

-   Goed keuring van beveiligings organisaties van Azure-Services voor gebruik 

-   Beveiliging van assets via hun levens cyclus

-   Vereiste strategie voor toegangs beheer in overeenstemming met organisatie gegevens classificatie

-   Gebruik van functies voor gegevens bescherming van Azure native en derden

-   Gegevens versleutelings vereisten voor in-transit-en rest-use cases

-   Juiste cryptografische normen

Zie voor meer informatie de volgende verwijzingen:
- [Aanbeveling van Azure-beveiligings architectuur-opslag, gegevens en versleuteling](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Basis beginselen van Azure-beveiliging-Azure-gegevens beveiliging,-versleuteling en-opslag](../security/fundamentals/encryption-overview.md)

- [Cloud-acceptatie Framework-aanbevolen procedures voor Azure Data Security en versleuteling](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Azure Security Bench Mark-Asset Management](/azure/security/benchmarks/security-benchmark-v2-asset-management)

- [Azure Security Bench Mark-gegevens beveiliging](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: ondernemings segmentatie strategie definiëren 

**Richt lijnen**: Stel een bedrijfs strategie in om de toegang tot assets te segmenteren met een combi natie van identiteits-, netwerk-, toepassings-, abonnements-, beheer groep-en andere besturings elementen.

Houd de nood zaak van beveiligings scheiding zorgvuldig bij met de nood zaak om de dagelijkse werking van de systemen in te scha kelen die met elkaar moeten communiceren en toegang hebben tot gegevens.

Zorg ervoor dat de segmentatie strategie consistent wordt geïmplementeerd in alle controle typen, zoals netwerk beveiliging, identiteits-en toegangs modellen, en toepassings machtigingen/toegangs modellen en besturings elementen voor menselijke processen.

- [Richt lijnen voor segmentatie strategie in azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Richt lijnen voor segmentatie strategie in azure (document)](/security/compass/governance#enterprise-segmentation-strategy)

- [Netwerk segmentatie uitlijnen met strategie voor bedrijfs segmentatie](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: beveiligings strategie voor postuur-beheer definiëren

**Hulp**: regel matig de Risico's voor uw afzonderlijke assets en de omgeving waarin ze worden gehost. Volg prioriteiten voor hoogwaardige assets en zeer belichte aanvallen, zoals gepubliceerde toepassingen, netwerk binnenkomend en uitgevende punten, gebruikers-en beheerders eindpunten, enzovoort.

- [Azure Security Bench Mark-postuur en beveiligings beheer](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: organisatie rollen, verantwoordelijkheden en accountabilities uitlijnen

**Richt lijnen**: Zorg ervoor dat u een duidelijke strategie voor rollen en verantwoordelijkheden in uw beveiligings organisatie documenteert en communiceert. Volg prioriteiten voor een duidelijke verantwoordelijkheid voor beveiligings beslissingen, het trainen van iedereen op het gedeelde verantwoordelijkheids model en technische teams op technologie om de cloud te beveiligen.

- [Best Practice voor Azure-beveiliging 1: personen: teams trainen in Cloud Security traject](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Aanbevolen beveiligings procedure voor Azure-gebruikers: teams in de Cloud-beveiligings technologie trainen](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Best Practice voor Azure-beveiliging 3-proces: verantwoordelijkheid toewijzen voor Cloud beveiligings beslissingen](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="gs-5-define-network-security-strategy"></a>GS-5: netwerk beveiligings strategie definiëren

**Richt lijnen**: Stel een Azure-netwerk beveiligings benadering in als onderdeel van de algehele strategie voor het toegangs beheer van uw organisatie.  

Deze strategie moet gedocumenteerde richt lijnen, beleid en standaarden bevatten voor de volgende elementen: 

-   Gecentraliseerd netwerk beheer en beveiligings verantwoordelijkheid

-   Segment model voor virtuele netwerken dat is afgestemd op de strategie voor bedrijfs segmentatie

-   Herstel strategie in verschillende scenario's voor bedreigingen en aanvallen

-   Internet-en ingangs-en uitgangs strategie

-   Hybride Cloud en on-premises interconnectiviteit-strategie

-   Up-to-date netwerk beveiligings artefacten (zoals netwerk diagrammen, referentie netwerk architectuur)

Zie voor meer informatie de volgende verwijzingen:
- [Aanbevolen procedures voor Azure-beveiliging 11-architectuur. Eén uniforme beveiligings strategie](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Bench Mark-netwerk beveiliging](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Overzicht van Azure-netwerk beveiliging](../security/fundamentals/network-overview.md)

- [Strategie voor bedrijfs netwerk architectuur](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: identiteits-en bevoorrechte toegangs strategie definiëren

**Richt lijnen**: Stel een Azure Identity and privileged Access benaderingen in als onderdeel van de algehele strategie voor beveiligings beheer van uw organisatie.  

Deze strategie moet gedocumenteerde richt lijnen, beleid en standaarden bevatten voor de volgende elementen: 

-   Een gecentraliseerd identiteits-en verificatie systeem en de interconnectiviteit daarvan met andere interne en externe identiteits systemen

-   Sterke authenticatie methoden in verschillende use-cases en-voor waarden

-   Beveiliging van gebruikers met hoge bevoegdheden

-   Bewaking en verwerking van afwijkende gebruikers activiteiten  

-   Beoordelings-en afstemmings proces voor gebruikers-id en-toegang

Zie voor meer informatie de volgende verwijzingen:

- [Azure Security Bench Mark-Identity Management](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Azure Security-benchmark-privileged Access](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Aanbevolen procedures voor Azure-beveiliging 11-architectuur. Eén uniforme beveiligings strategie](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Overzicht van Azure Identity Management-beveiliging](../security/fundamentals/identity-management-overview.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: vastleggen van de logboek registratie en de reactie op bedreigingen

**Richt lijnen**: Stel een strategie voor logboek registratie en reactie op bedreigingen in om bedreigingen snel te detecteren en op te lossen terwijl aan nalevings vereisten wordt voldaan. Volg prioriteiten om analisten te voorzien van waarschuwingen met hoge kwaliteit en naadloze ervaring, zodat ze zich kunnen concentreren op bedreigingen in plaats van integratie en hand matige stappen. 

Deze strategie moet gedocumenteerde richt lijnen, beleid en standaarden bevatten voor de volgende elementen: 

-   De rol en verantwoordelijkheden van de organisatie voor beveiligings bewerkingen (SecOps) 

-   Een goed gedefinieerd respons proces voor incidenten dat wordt afgestemd op het NIST of een ander branche raamwerk 

-   Vastleggen en bewaren in Logboeken voor ondersteuning van detectie van bedreigingen, reacties op incidenten en nalevings behoeften

-   Gecentraliseerde zicht baarheid van en correlatie-informatie over bedreigingen, met behulp van SIEM, systeem eigen Azure-mogelijkheden en andere bronnen 

-   Communicatie-en meldings abonnement met uw klanten, leveranciers en open bare partijen

-   Gebruik van Azure native en platforms van derden voor het verwerken van incidenten, zoals logboek registratie en detectie van bedreigingen, forensische, herstel en uitroeiing van aanvallen

-   Processen voor het verwerken van incidenten en activiteiten na incidenten, zoals geleerde lessen en bewijs behoud

Zie voor meer informatie de volgende verwijzingen:

- [Azure Security Bench Mark-logboek registratie en detectie van bedreigingen](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Azure Security Bench Mark-incident respons](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Aanbevolen procedure voor Azure-beveiliging 4-proces. Reactie processen voor incidenten bijwerken voor Cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Hand leiding Azure-acceptatie raamwerk, logboek registratie en rapportage](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Schaal, beheer en bewaking van Azure Enter prise](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="next-steps"></a>Volgende stappen

- Zie [overzicht van Azure Security Bench Mark v2](/azure/security/benchmarks/overview)
- Meer informatie over [Azure-beveiligings basislijnen](/azure/security/benchmarks/security-baselines-overview)
