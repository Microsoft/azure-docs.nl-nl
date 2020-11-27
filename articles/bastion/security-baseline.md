---
title: Azure-beveiligings basislijn voor Azure Bastion
description: De Azure Bastion Security-basis lijn biedt procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-benchmark test.
author: msmbaldwin
ms.service: bastion
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 461d3800491dbe382beecbcdfe1f2a93bd6b2e5c
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/27/2020
ms.locfileid: "96301569"
---
# <a name="azure-security-baseline-for-azure-bastion"></a>Azure-beveiligings basislijn voor Azure Bastion

Deze beveiligings basislijn is van toepassing op richt lijnen van de [Azure Security Bench Mark-versie 2,0](../security/benchmarks/overview.md) naar Azure Bastion. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen. De inhoud wordt gegroepeerd op basis van de **beveiligings controles** die zijn gedefinieerd door de Azure Security-benchmark en de bijbehorende richt lijnen die van toepassing zijn op Azure Bastion. **Besturings elementen** die niet van toepassing zijn op Azure Bastion, zijn uitgesloten.

Zie het [volledige Azure Bastion Security Baseline-toewijzings bestand](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)voor meer informatie over hoe Azure Bastion volledig is toegewezen aan de beveiligings benchmark van Azure.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Bench Mark: Network Security](/azure/security/benchmarks/security-controls-v2-network-security)(Engelstalig) voor meer informatie.*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: beveiliging voor intern verkeer implementeren

**Richt lijnen**: wanneer u Azure Bastion-resources implementeert, moet u een bestaand virtueel netwerk maken of gebruiken. Zorg ervoor dat alle virtuele netwerken van Azure een bedrijfs segmentatie principe volgen die afstemt op de bedrijfs Risico's. Elk systeem dat een hoger risico voor de organisatie kan opleveren, moet worden geïsoleerd binnen een eigen virtueel netwerk en voldoende is beveiligd met een netwerk beveiligings groep (NSG).

Voor een juiste werking van de Azure Bastion-service moeten de volgende poorten zijn geopend voor de service:

- Binnenkomend verkeer:
   - Binnenkomend verkeer vanaf het open bare Internet: de Azure-Bastion maakt een openbaar IP-adres waarvoor poort 443 is ingeschakeld op het open bare IP-adres voor binnenkomend verkeer. Poort 3389/22 hoeft niet te worden geopend op de AzureBastionSubnet. 

   - Binnenkomend verkeer vanuit het Azure Bastion-besturings vlak: als u de connectiviteit voor het besturings element wilt controleren, schakelt u poort 443 inkomende van GatewayManager-servicetag in. Hiermee wordt het besturings vlak ingeschakeld, dat wil zeggen dat gateway beheer met Azure Bastion kan communiceren.

- Uitgaand verkeer:

   - Uitgaand verkeer naar virtuele machines (Vm's): Azure Bastion haalt de doel-Vm's over het particuliere IP-adres. De Nsg's moet uitgaand verkeer naar andere doel-VM-subnetten toestaan voor poort 3389 en 22.

   - Uitgaand verkeer naar andere open bare eind punten in Azure: Azure Bastion moet verbinding kunnen maken met verschillende open bare eind punten in azure (bijvoorbeeld voor het opslaan van Diagnostische logboeken en logboeken voor meters). Daarom heeft Azure Bastion een uitgaand verkeer naar 443 naar Cloud service-tag nodig.

De verbinding met Gateway beheer en de Azure-servicetag wordt beveiligd (vergrendeld) door Azure-certificaten. Externe entiteiten, inclusief de consumenten van deze bronnen, kunnen niet communiceren op deze eind punten. 

- [Een netwerk beveiligings groep met beveiligings regels maken](../virtual-network/tutorial-filter-network-traffic.md)

- [U vindt hier meer informatie over de vereiste Bastion NSG](bastion-nsg.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="ns-2-connect-private-networks-together"></a>NS-2: particuliere netwerken met elkaar verbinden

**Hulp**: Azure Bastion maakt geen eind punten beschikbaar die toegankelijk zijn via een particulier netwerk. Azure Bastion ondersteunt de implementatie in een peered netwerk om uw Bastion-implementatie te centraliseren en connectiviteit tussen meerdere netwerken mogelijk te maken.

- [Azure Bastion en Virtual Network-peering](vnet-peering.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="identity-management"></a>Identiteitsbeheer

*Ga voor meer informatie naar [Azure Security Benchmark: Identiteitsbeheer](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Azure Active Directory standaardiseren als het centrale identiteits- en verificatiesysteem

**Hulp**: Azure Bastion is geïntegreerd met Azure Active Directory (Azure AD), de standaard service voor identiteits-en toegangs beheer van Azure. Gebruikers hebben toegang tot de Azure Portal met behulp van Azure AD-verificatie voor het beheren van de Azure Bastion-service (Bastion-resources maken, bijwerken en verwijderen).

Verbinding maken met virtuele machines met behulp van Azure Bastion is afhankelijk van een SSH-sleutel of gebruikers naam/wacht woord, en biedt momenteel geen ondersteuning voor het gebruik van Azure AD-referenties.

Naast een SSH-sleutel of gebruikers naam/wacht woord moet de volgende roltoewijzingen worden gebruikt wanneer u verbinding maakt met virtuele machines met behulp van Azure Bastion:
- Rol van lezer op de virtuele doel machine
- Rol van lezer op de NIC met het privé-IP-adres van de doel-VM
- De rol van Lezer in de Azure Bastion-resource

Raadpleeg het volgende Engelstalige naslagmateriaal voor meer informatie:

- [Verbinding maken met een virtuele Linux-machine met behulp van Azure Bastion](bastion-connect-vm-ssh.md)

- [Verbinding maken met een virtuele Windows-machine met behulp van Azure Bastion](bastion-connect-vm-rdp.md)

- [Ingebouwde Azure-rollen](../role-based-access-control/built-in-roles.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Eenmalige aanmelding van Azure AD gebruiken voor toegang tot toepassingen

**Richt lijnen**: Azure Bastion biedt geen ondersteuning voor SSO voor verificatie bij de verificatie bij de virtuele machine resources, alleen ssh of gebruikers naam/wacht woord worden ondersteund. Azure Bastion maakt echter gebruik van Azure Active Directory (Azure AD) voor het bieden van identiteits-en toegangs beheer voor de algemene service. Gebruikers kunnen zich verifiëren bij Azure AD voor toegang tot en beheer van hun Azure Bastion-resources en bieden naadloze eenmalige aanmelding met hun eigen gesynchroniseerde ondernemings identiteiten via Azure AD Connect. 

- [Eenmalige aanmelding van toepassingen met Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Azure AD Connect](../active-directory/hybrid/whatis-azure-ad-connect.md)

- [Verbinding maken met een virtuele Linux-machine met behulp van Azure Bastion](bastion-connect-vm-ssh.md)

- [Verbinding maken met een virtuele Windows-machine met behulp van Azure Bastion](bastion-connect-vm-rdp.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Krachtige verificatiemechanismen gebruiken voor alle op Azure Active Directory gebaseerde toegang

**Hulp**: Azure Bastion is geïntegreerd met Azure Active Directory (Azure AD) voor toegang tot en beheer van de service. Azure Multi-Factor Authentication configureren voor uw Azure AD-Tenant. Azure AD biedt ondersteuning voor sterke verificatie controles via multi-factor Authentication (MFA) en krachtige methoden met een eigen wacht woord.  
- Multi-factor Authentication: Schakel Azure AD MFA in en volg Azure Security Center aanbevelingen voor identiteits-en toegangs beheer voor uw MFA-installatie. MFA kan worden afgedwongen voor alle gebruikers, gebruikers selecteren of op het niveau per gebruiker op basis van aanmeldings voorwaarden en risico factoren. 

- Verificatie met wacht woord: er zijn drie verificatie opties met een wacht woord beschikbaar: Windows hello voor bedrijven, Microsoft Authenticator-app en on-premises verificatie methoden zoals Smart Cards. 

- [Meervoudige verificatie inschakelen in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Algemene informatie over opties voor verificatie zonder wachtwoord voor Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Controleren op accountafwijkingen en hiervoor waarschuwen

**Hulp** bij het inschakelen van Diagnostische logboeken voor Azure Bastion externe sessies en het gebruik van deze logboeken om te bekijken welke gebruikers zijn verbonden met welke werk belastingen, op welk tijdstip, waar en met andere relevante logboek gegevens. Maak waarschuwingen voor bepaalde vastgelegde Bastion-sessies met behulp van Azure Monitor om een melding te ontvangen wanneer er afwijkingen zijn gedetecteerd in de logboeken.

- [U vindt hier meer informatie over het inschakelen van diagnostische logboek registratie](diagnostic-logs.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>Chat-6: toegang tot Azure-bronnen beperken op basis van voor waarden

**Richt lijnen**: u kunt alleen toegang krijgen tot de Azure Bastion-service via de Azure Portal, maar de toegang tot Azure Portal kan worden beperkt met behulp van voorwaardelijke toegang van Azure Active Directory (Azure AD). Gebruik voorwaardelijke toegang voor Azure AD voor meer gedetailleerd toegangs beheer op basis van door de gebruiker gedefinieerde voor waarden, zoals het vereisen van gebruikers aanmeldingen vanuit bepaalde IP-bereiken voor het gebruik van MFA.

De klant kan ook gebruikmaken van verschillende op rollen gebaseerd toegangs beheer beleid op het niveau van de virtuele machines van het domein om de toegang tot de virtuele machine verder te beperken.

- [Meer informatie over voorwaardelijke toegang tot Azure AD vindt u hier](../active-directory/conditional-access/overview.md)

- [Overzicht van voorwaardelijke toegang voor Azure](../active-directory/conditional-access/overview.md)

- [Algemeen beleid voor voorwaardelijke toegang](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Verificatie sessie beheer met voorwaardelijke toegang configureren](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="privileged-access"></a>Bevoegde toegang

*Ga voor meer informatie naar [Azure Security-benchmark: Bevoegde toegang](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: beheer toegang beperken tot essentiële bedrijfs systemen

**Richt lijnen**: Azure Bastion maakt gebruik van Azure RBAC (op rollen gebaseerd toegangs beheer) voor het isoleren van toegang tot bedrijfskritische systemen door te beperken welke accounts toegang krijgen om verbinding te maken met bepaalde virtuele machines. Zorg ervoor dat u het principe van minimale bevoegdheden volgt, zodat gebruikers alleen over de machtigingen beschikken die nodig zijn om hun specifieke taken uit te voeren.

Zorg ervoor dat u ook de toegang tot de beheer-, identiteits-en beveiligings systemen beperkt die beheerders toegang hebben tot uw essentiële bedrijfs toegang, zoals Active Directory-domein controllers (Dc's), beveiligings hulpprogramma's en Systeembeheer Programma's met agents die zijn geïnstalleerd op essentiële bedrijfs systemen. Aanvallers die deze beheer-en beveiligings systemen misbruiken, kunnen ze onmiddellijk weaponize om bedrijfs kritieke activa te beschadigen.

Alle typen besturings elementen voor toegang moeten worden afgestemd op uw bedrijfs segmentatie strategie om een consistent toegangs beheer te garanderen.

- [Vereiste rollen om toegang te krijgen tot een virtuele machine met Azure Bastion](bastion-faq.md#roles)

- [Azure-onderdelen en referentie model](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Toegang tot beheer groepen](../governance/management-groups/overview.md#management-group-access)

- [Beheerders van Azure-abonnementen](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Gebruikerstoegang regelmatig beoordelen en afstemmen

**Hulp**: Azure Bastion maakt gebruik van Azure Active Directory (Azure AD)-accounts en Azure RBAC om de resources te beheren. Controleer gebruikers accounts en toegangs toewijzingen regel matig om te controleren of de accounts en hun toegang geldig zijn. U kunt Azure AD-toegangs beoordelingen gebruiken om groepslid maatschappen, de toegang tot bedrijfs toepassingen en roltoewijzingen te controleren. Azure AD Reporting kan logboeken bieden waarmee u verlopen accounts kunt detecteren. U kunt ook Azure AD Privileged Identity Management gebruiken om een werk stroom voor het lezen van rapporten te maken om het controle proces te vergemakkelijken.

Daarnaast kan Azure Privileged Identity Management ook worden geconfigureerd om te worden gewaarschuwd wanneer er een uitzonderlijk groot aantal beheerders accounts wordt gemaakt en om beheerders accounts te identificeren die verouderd of onjuist zijn geconfigureerd.

- [Een toegangs beoordeling van Azure-resource rollen maken in Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Toegang tot een overdracht verwijderen](../lighthouse/how-to/remove-delegation.md)

- [Identiteits- en toegangsbeoordelingen van Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Emergency Access instellen in azure AD

**Hulp**: Azure Bastion is geïntegreerd met Azure Active Directory en Azure RBAC om de resources te beheren. Als u wilt voor komen dat uw Azure AD-organisatie per ongeluk wordt vergrendeld, stelt u een account voor toegang voor nood gevallen in voor toegang wanneer normale beheerders accounts niet kunnen worden gebruikt. Accounts voor toegang in nood gevallen zijn meestal zeer goed beschermd en ze mogen niet worden toegewezen aan specifieke personen. Accounts voor toegang in nood gevallen zijn beperkt tot scenario's met nood gevallen of ' afbreek glazen ', waarbij normale beheerders accounts niet kunnen worden gebruikt.

Zorg ervoor dat de referenties (zoals wacht woord, certificaat of Smart Card) voor accounts voor toegang in nood gevallen beveiligd blijven en alleen bekend zijn bij personen die alleen in een nood geval mogen gebruiken.

- [Accounts voor nood toegang beheren in azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="pa-5-automate-entitlement-management"></a>PA-5: beheer van rechten automatiseren 

**Richt lijnen**: Azure Bastion is geïntegreerd met Azure Active Directory (Azure AD) en Azure RBAC om de resources te beheren. Gebruik Azure AD-functies voor rechten beheer om werk stromen voor toegangs aanvragen te automatiseren, met inbegrip van toegangs toewijzingen, beoordelingen en verval datums. Het is ook mogelijk om twee of meerdere fasen goed te keuren.

- [Wat zijn Azure AD-toegangs beoordelingen](../active-directory/governance/access-reviews-overview.md)

- [Wat is het beheer van rechten van Azure AD](../active-directory/governance/entitlement-management-overview.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: bevoorrechte toegang gebruiken werk stations

**Richt lijnen**: beveiligde, geïsoleerde werk stations zijn van cruciaal belang voor de beveiliging van gevoelige rollen als beheerders, ontwikkel aars en essentiële service operators. Afhankelijk van uw vereisten kunt u zeer beveiligde werk stations van gebruikers gebruiken voor het uitvoeren van beheer taken met uw Azure Bastion-resources in productie omgevingen. Gebruik Azure Active Directory, micro soft Defender Advanced Threat Protection (ATP) en/of Microsoft Intune voor het implementeren van een beveiligd en beheerd gebruikers werkstation voor beheer taken. De beveiligde werk stations kunnen centraal worden beheerd voor het afdwingen van beveiligde configuratie, inclusief sterke authenticatie, software-en hardware-basis lijnen en beperkte logische en netwerk toegang. 

- [Meer informatie over privileged Access workstations](../active-directory/devices/concept-azure-managed-workstation.md)

- [Een privileged Access-werk station implementeren](../active-directory/devices/howto-azure-managed-workstation.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Principe van minimale bevoegdheden hanteren 

**Hulp**: Azure Bastion is geïntegreerd met op rollen gebaseerd toegangs beheer (RBAC) van Azure voor het beheren van de bijbehorende resources. Met Azure RBAC kunt u de toegang tot Azure-resources beheren via roltoewijzingen. U kunt deze ingebouwde rollen toewijzen aan gebruikers, groepen, service-principals en beheerde identiteiten. Er bestaan vooraf gedefinieerde, ingebouwde rollen voor bepaalde resources, en deze rollen kunnen worden geïnventariseerd of opgevraagd via tools zoals Azure CLI, Azure PowerShell en Azure Portal. De bevoegdheden die u via Azure RBAC toewijst aan resources, moeten altijd beperkt zijn tot wat vereist is voor de rollen. Dit is een aanvulling op de JIT-benadering van Azure AD Privileged Identity Management (PIM) en moet regelmatig worden geëvalueerd. Gebruik ingebouwde rollen om machtigingen toe te wijzen en alleen aangepaste rollen te maken wanneer dat nodig is.

Wanneer u verbinding maakt met virtuele machines met behulp van Azure Bastion, heeft uw gebruiker de volgende roltoewijzingen nodig:
- Rol van lezer op de virtuele doel machine
- Rol van lezer op de NIC met het privé-IP-adres van de doel-VM
- De rol van Lezer in de Azure Bastion-resource

Raadpleeg het volgende Engelstalige naslagmateriaal voor meer informatie:

- [Verbinding maken met een virtuele Linux-machine met behulp van Azure Bastion](bastion-connect-vm-ssh.md)

- [Verbinding maken met een virtuele Windows-machine met behulp van Azure Bastion](bastion-connect-vm-rdp.md)

- [Ingebouwde Azure-rollen](../role-based-access-control/built-in-roles.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="asset-management"></a>Asset-management

*Ga voor meer informatie naar [Azure Security-benchmark: Asset-management](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Beveiligingsteam inzicht geven in risico's voor assets

**Richtlijnen**: Zorg ervoor dat beveiligingsteams de machtiging Beveiligingslezer hebben in uw Azure-tenant en -abonnementen zodat ze kunnen controleren op beveiligingsrisico's met behulp van Azure Security Center. 

Afhankelijk van de manier waarop de verantwoordelijkheden van het beveiligingsteam zijn gestructureerd, kan de controle op beveiligingsrisico's de verantwoordelijkheid zijn van een centraal beveiligingsteam of een lokaal team. Om die reden moeten beveiligingsinzichten en -risico's altijd centraal worden verzameld in een organisatie. 

De machtiging Beveiligingslezer kan breed worden toegepast op een hele tenant (hoofdbeheergroep) of in het bereik van beheergroepen of specifieke abonnementen. 

Opmerking: Er zijn mogelijk extra machtigingen vereist om inzicht te krijgen in workloads en services. 

- [Overzicht van rol Beveiligingslezer](../role-based-access-control/built-in-roles.md#security-reader)

- [Overzicht van Azure-beheergroepen](../governance/management-groups/overview.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: controleren of het beveiligings team toegang heeft tot de inventaris en meta gegevens van de Asset

**Richt lijnen**: Tags Toep assen op uw Azure Bastion-resources, resource groepen en abonnementen om ze logisch in een taxonomie te organiseren. Elke tag bestaat uit een naam en een waardepaar. U kunt de naam Omgeving en de waarde Productie bijvoorbeeld toepassen op alle resources in de productie. Zorg ervoor dat beveiligings teams toegang hebben tot een voortdurend bijgewerkte inventaris van assets in Azure. Ze kunnen Azure resource Graph gebruiken om alle resources in uw abonnementen te doorzoeken en te detecteren, met inbegrip van Azure-Services,-toepassingen en-netwerk bronnen.

- [Query's maken met Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

- [Azure Security Center Asset Inventory Management](../security-center/asset-inventory.md)

- [Voor meer informatie over het labelen van assets raadpleegt u de hand leiding resource naamgeving en Tags toevoegen](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: alleen goedgekeurde Azure-Services gebruiken

**Hulp**: gebruik Azure Policy om te controleren en te beperken welke Services gebruikers in uw omgeving kunnen inrichten. Dit omvat het toestaan of weigeren van implementaties van Azure Bastion-resources. Gebruik Azure resource Graph om bronnen in hun abonnementen op te vragen en te detecteren. U kunt ook Azure Monitor gebruiken om regels te maken voor het activeren van waarschuwingen wanneer een niet-goedgekeurde service wordt gedetecteerd.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een specifiek resource type weigeren met Azure Policy](../governance/policy/samples/built-in-policies.md#general)

- [Query's maken met Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Beveiliging van levenscyclusbeheer van assets garanderen

**Richt lijnen**: u kunt de toegang tot Azure Bastion-resources die zijn geïmplementeerd, verwijderen wanneer ze niet meer nodig zijn om de kwets baarheid voor aanvallen te minimaliseren. Gebruikers kunnen hun Azure Bastion-resources beheren via de Azure Portal, CLI of REST Api's. U kunt ook een actieve externe sessie verwijderen of forceren als deze niet meer nodig is of als een mogelijke bedreiging wordt geïdentificeerd.

- [Verwijderen van Force-verbinding met een externe sessie verbreken](session-monitoring.md#view)

- [Azure Network CLI](https://docs.microsoft.com/powershell/module/az.network/?view=azps-5.1.0#networking&amp;preserve-view=true)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

**Hulp**: Azure Bastion is geïntegreerd met Azure Active Directory (Azure AD) voor identiteit en verificatie. U kunt voorwaardelijke toegang van Azure gebruiken om de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager te beperken door het configureren van ' toegang blok keren ' voor de app ' Microsoft Azure-beheer '.

- [Voorwaardelijke toegang configureren om de toegang tot Azure-bronnen beheer te blok keren](../role-based-access-control/conditional-access-azure-management.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

## <a name="logging-and-threat-detection"></a>Logboekregistratie en bedreigingsdetectie

*Ga voor meer informatie naar [Azure Security-benchmark: Logboekregistratie en bedreigingsdetectie](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Bedreigingsdetectie inschakelen voor identiteits- en toegangsbeheer van Azure

**Hulp**: Azure Bastion kan worden geïntegreerd met Azure Active Directory (Azure AD) en de service wordt via de Azure Portal benaderd. Standaard worden beheer acties voor de service (zoals maken, bijwerken en verwijderen) vastgelegd via het Azure-activiteiten logboek. Gebruikers moeten ook Azure Bastion-bron logboeken inschakelen, zoals sessie BastionAuditLogs om Bastion-sessies bij te houden.

Azure AD biedt de volgende gebruikers logboeken die kunnen worden weer gegeven in azure AD Reporting of geïntegreerd met Azure Monitor, Azure Sentinel of andere hulpprogram ma's voor SIEM/controle voor geavanceerde bewakings-en analyse cases: 
-   Aanmeldingen: het rapport voor aanmeldingsactiviteit bevat informatie over het gebruik van beheerde toepassingen en aanmeldingsactiviteiten van gebruikers

-   Auditlogboeken: traceerbaarheid via logboeken voor alle door diverse functies binnen Azure AD uitgevoerde wijzigingen. Voorbeelden van vermeldingen in auditlogboeken zijn wijzigingen die worden aangebracht in resources binnen Azure AD, zoals het toevoegen of verwijderen van gebruikers, apps, groepen, rollen en beleidsregels.

-   Riskante aanmeldingen - Een riskante aanmelding is een indicator van een aanmeldingspoging die mogelijk is uitgevoerd door iemand die geen rechtmatige eigenaar van een gebruikersaccount is.

-   Gebruikers voor wie wordt aangegeven dat ze risico lopen - Een riskante gebruiker is een indicator van een gebruikersaccount dat mogelijk is aangetast.

Azure Security Center kan ook worden gewaarschuwd voor bepaalde verdachte activiteiten, zoals een uitzonderlijk aantal mislukte verificatie pogingen en afgeschafte accounts in het abonnement. Naast de basis bewaking van de beveiligings hygiëne, kan de bedreigings beveiligings module van Azure Security Center ook uitgebreidere beveiligings waarschuwingen verzamelen van afzonderlijke Azure Compute-resources (zoals virtuele machines, containers, app service), gegevens bronnen (zoals SQL DB en opslag) en Azure-service lagen. Met deze mogelijkheid kunt u rekening afwijkingen in de afzonderlijke resources bekijken.

- [Azure Bastion-resource logboeken](diagnostic-logs.md)

- [Activiteiten rapporten controleren in azure AD](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Azure AD Identity Protection inschakelen](../active-directory/identity-protection/overview-identity-protection.md)

- [Bescherming tegen bedreiging in Azure Security Center](/azure/security-center/threat-protection)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: logboek registratie inschakelen voor Azure-netwerk activiteiten

**Richt lijnen**: Azure Bastion-resource logboeken inschakelen, de diagnostische Logboeken gebruiken om te zien welke gebruikers zijn verbonden met welke werk belastingen, op welk tijdstip, vanaf waar en andere relevante logboek gegevens. Gebruikers kunnen deze logboeken zo configureren dat ze worden verzonden naar een opslag account voor lange termijn retentie en controle.

NSG-resource Logboeken (netwerk beveiligings groep) en NSG-stroom logboeken inschakelen en verzamelen op de netwerk beveiligings groepen die worden toegepast op de virtuele netwerken waarop uw Azure Bastion-resource is geïmplementeerd. Deze logboeken kunnen vervolgens worden gebruikt voor het analyseren van de netwerk beveiliging en het ondersteunen van incident onderzoeken, de jacht van dreigingen en het genereren van beveiligings waarschuwingen. U kunt de stroom logboeken naar een Azure Monitor Log Analytics werk ruimte verzenden en vervolgens Traffic Analytics gebruiken om inzichten te geven.

- [Azure Bastion-logboeken inschakelen en gebruiken](diagnostic-logs.md)

- [Stroom logboeken van netwerk beveiligings groepen inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Azure Firewall-logboeken en metrische gegevens](../firewall/logs-and-metrics.md)

- [Traffic Analytics inschakelen en gebruiken](../network-watcher/traffic-analytics.md)

- [Controleren met Network Watcher](../network-watcher/network-watcher-monitoring-overview.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: logboek registratie inschakelen voor Azure-resources

**Hulp**: activiteiten logboeken, die automatisch beschikbaar zijn, bevatten alle schrijf bewerkingen (put, post, Delete) voor uw Azure Bastion-resources, met uitzonde ring van Lees bewerkingen (Get). Activiteiten logboeken kunnen worden gebruikt om een fout te vinden bij het oplossen van problemen of om te controleren hoe een gebruiker in uw organisatie een resource heeft gewijzigd.

- [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Logboek registratie en verschillende logboek typen in azure](../azure-monitor/platform/platform-logs-overview.md)

- [Azure-resource logboeken inschakelen voor Azure Bastion ](diagnostic-logs.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: beveiligings logboek beheer en-analyse centraliseren

**Richt lijnen**: opslag en analyse van logboek registratie centraliseren om correlatie in te scha kelen. Zorg ervoor dat u voor elke logboek bron een gegevens eigenaar hebt toegewezen, toegangs richtlijnen, opslag locatie, welke hulpprogram ma's worden gebruikt voor het verwerken en openen van de gegevens en vereisten voor gegevens behoud.

Zorg ervoor dat u Azure-activiteiten logboeken integreert in uw centrale logboek registratie. Opname logboeken via Azure Monitor voor het verzamelen van beveiligings gegevens die zijn gegenereerd door eindpunt apparaten, netwerk bronnen en andere beveiligings systemen. Gebruik in Azure Monitor Log Analytics-werk ruimten om analyses uit te voeren en te uitvoeren en gebruik Azure Storage accounts voor lange termijn-en archiverings opslag.

Daarnaast kunt u gegevens inschakelen en vrijgeven aan Azure Sentinel of een SIEM van derden.

Veel organisaties kiezen voor het gebruik van Azure Sentinel voor ' hot ' gegevens die regel matig worden gebruikt en die worden Azure Storage voor ' koude ' gegevens die minder vaak worden gebruikt.

- [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Bewaar periode voor logboek opslag configureren

**Richt lijnen**: Zorg ervoor dat voor opslag accounts of log Analytics-werk ruimten die worden gebruikt voor het opslaan van Azure Bastion-logboeken, de Bewaar periode voor logboeken is ingesteld op basis van de nalevings voorschriften van uw organisatie.

In Azure Monitor kunt u de Bewaar periode voor uw Log Analytics werk ruimte instellen volgens de nalevings voorschriften van uw organisatie.

- [De Bewaar periode van Log Analytics Workspace configureren](../azure-monitor/platform/manage-cost-storage.md)

- [Bron logboeken opslaan in een Azure Storage-account](/azure/azure-monitor/platform/resource-logs-collect-storage)

- [Azure bastions-logboeken inschakelen en gebruiken](diagnostic-logs.md)

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

*Ga voor meer informatie naar [Azure Security-benchmark: Beheer van beveiligingspostuur en beveiligingsproblemen](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management).*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: beveiligde configuraties instellen voor Azure-Services 

**Richt lijnen**: standaard beveiligings configuraties voor Azure Bastion definiëren en implementeren met Azure Policy. Gebruik Azure Policy aliassen in de naam ruimte ' micro soft. netwerk ' om aangepaste beleids regels te maken om de netwerk configuratie van uw Azure-Bastion te controleren of af te dwingen. Klanten kunnen ook veilige configuraties opzetten door gebruik te maken van Azure-blauw drukken of ARM-sjablonen om Bastion-bronnen veilig en consistent te implementeren.

- [Beschik bare Azure Policy aliassen weer geven](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Meer informatie over ARM-sjablonen](../azure-resource-manager/templates/overview.md)

- [Overzicht van Azure-blauw drukken](../governance/blueprints/overview.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: beveiligde configuraties voor Azure-Services

**Richt lijnen**: standaard beveiligings configuraties voor Azure Bastion definiëren en implementeren met Azure Policy. Gebruik Azure Policy aliassen in de naam ruimte ' micro soft. netwerk ' om aangepaste beleids regels te maken om de netwerk configuratie van uw Bastion-resources te controleren of af te dwingen.

- [Beschik bare Azure Policy aliassen weer geven](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Regelmatig aanvalssimulaties uitvoeren

**Richtlijnen**: Voer zo nodig penetratietests of Red Teaming-activiteiten uit voor uw Azure-resources en zorg ervoor dat alle beveiligingsproblemen worden opgelost.

Volg de Microsoft Cloud Penetration Testing Rules of Engagement om ervoor te zorgen dat uw penetratietests voldoen aan de beleidsregels van Microsoft. Volg de strategie en instructies van Microsoft voor Red Teaming en penetratietests van live sites voor cloudinfrastructuur, services en toepassingen die door Microsoft worden beheerd.

- [Penetratietests in Azure](../security/fundamentals/pen-testing.md)

- [Penetration Testing Rules of Engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

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

- [Azure Security Benchmark - Asset management](/azure/security/benchmarks/security-controls-v2-asset-management)

- [Azure Security Benchmark - Data Protection](/azure/security/benchmarks/security-controls-v2-data-protection)

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

- [Azure Security Benchmark - Posture and vulnerability management](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)

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

- [Azure Security Benchmark - Network Security](/azure/security/benchmarks/security-controls-v2-network-security)

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

- [Azure Security Benchmark - Identity management](/azure/security/benchmarks/security-controls-v2-identity-management)

- [Azure Security Benchmark - Privileged access](/azure/security/benchmarks/security-controls-v2-privileged-access)

- [Azure Security Best Practice 11 - Architecture. Single unified security strategy](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure identity management security overview](../security/fundamentals/identity-management-overview.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Strategie voor logboekregistratie en respons bij bedreigingen definiëren

**Richtlijnen**: Stel een strategie op voor logboekregistratie en de respons bij bedreigingen om bedreigingen snel te detecteren en op te lossen terwijl u voldoet aan de nalevingsvereisten. Volg prioriteiten voor het leveren van analisten met hoogwaardige waarschuwingen en naadloze ervaringen, zodat ze zich kunnen concentreren op bedreigingen in plaats van integratie en hand matige stappen. 

Deze strategie moet gedocumenteerde richtlijnen, beleid en standaarden bevatten voor de volgende elementen: 

-   De rol en verantwoordelijkheden van het SecOps-team van de organisatie 

-   Een goed omschreven responsproces voor incidenten dat is afgestemd op NIST of een ander framework uit de branche 

-   Logboeken registreren en bewaren voor ondersteuning van bedreigingsdetectie, respons op incidenten en nalevingsbehoeften

-   Gecentraliseerde zichtbaarheid van en correlatiegegevens over bedreigingen, met behulp van SIEM, functies van Azure en andere bronnen 

-   Plan voor communicatie en melding aan klanten, leveranciers en openbare betrokken partijen

-   Gebruik van platforms van Azure en van derden voor het afhandelen van incidenten, zoals logboekregistratie en bedreigingsdetectie, forensics, en herstel en eliminatie van aanvallen

-   Processen voor het afhandelen van incidenten en activiteiten na incidenten, zoals geleerde lessen en bewijsbehoud

Raadpleeg het volgende Engelstalige naslagmateriaal voor meer informatie:

- [Azure Security Benchmark - Logging and threat detection](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Azure Security Benchmark - Incident response](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Azure Security Best Practice 4 - Process. Update Incident Response Processes for Cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure Adoption Framework, logging, and reporting decision guide](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure enterprise scale, management, and monitoring](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="next-steps"></a>Volgende stappen

- Lees [dit overzichtsartikel over Azure Security Benchmark V2](/azure/security/benchmarks/overview).
- Lees meer over [basislijnen voor de beveiliging van Azure](/azure/security/benchmarks/security-baselines-overview)
