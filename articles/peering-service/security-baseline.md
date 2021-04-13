---
title: Azure-beveiligings basislijn voor Microsoft Azure peering-service
description: De Microsoft Azure de basis lijn van de peering service-beveiliging biedt procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-Bench Mark.
author: msmbaldwin
ms.service: peering-service
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 854c669861c6aff71861481843bff97fdb7157ab
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315482"
---
# <a name="azure-security-baseline-for-microsoft-azure-peering-service"></a>Azure-beveiligings basislijn voor Microsoft Azure peering-service

In deze beveiligings basislijn worden richt lijnen van de [Azure Security Bench Mark-versie 2,0](../security/benchmarks/overview.md) ingesteld op Microsoft Azure peering-service. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen. De inhoud wordt gegroepeerd op basis van de **beveiligings controles** die zijn gedefinieerd door de Azure Security-benchmark en de bijbehorende richt lijnen die van toepassing zijn op Microsoft Azure peering-service.

> [!NOTE]
> **Besturings elementen** die niet van toepassing zijn op Microsoft Azure peering-service of waarvoor de verantwoordelijkheid van micro soft is, zijn uitgesloten. Als u wilt zien hoe Microsoft Azure peering-service volledig is toegewezen aan de Security-Bench Mark van Azure, raadpleegt u het **[volledige Microsoft Azure peering-toewijzings bestand met basis](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)** informatie over de service.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Benchmark: netwerkbeveiliging](/azure/security/benchmarks/security-controls-v2-network-security) voor meer informatie.*

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: inbraak detectie/inbraak preventie systemen (ID'S/IP'S) implementeren

**Hulp**: peering-service biedt geen systeem eigen indringings detectie of preventie van services. Klanten moeten de best practices voor beveiliging volgen, zoals het gebruik van op bedreigingen gebaseerd filteren van Azure Firewall om het verkeer van en naar bekende schadelijke IP-adressen en domeinen te Signa lering en te blok keren, afhankelijk van hun bedrijfs vereisten. Deze IP-adressen en domeinen zijn afkomstig van de micro soft-Threat-Intelligence feed. 

In gevallen waarin Payload-inspectie is vereist, implementeert u een externe detectie-of preventie systeem (ID'S/IP-adressen), met Payload-inspectie mogelijkheden, vanuit Azure Marketplace.  
U kunt ook een EDR-oplossing (host-gebaseerde eindpunt detectie en respons) gebruiken in combi natie met (of in plaats van) op het netwerk gebaseerde inbraak detectie of preventie van systemen.  

Als u een wettelijke vereiste hebt voor het gebruik van een inbraak detectie of indringings systeem, moet u ervoor zorgen dat deze altijd is afgestemd op het verbruik of bieden van waarschuwingen van hoge kwaliteit. 

- [Azure Firewall implementeren](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Marketplace bevat mogelijkheden van derden-ID'S](https://azuremarketplace.microsoft.com/marketplace?search=IDS)

- [Micro soft Defender ATP EDR-mogelijkheid](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="identity-management"></a>Identiteitsbeheer

*Zie [Azure Security Benchmark: Identiteitsbeheer](/azure/security/benchmarks/security-controls-v2-identity-management) voor meer informatie.*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Azure Active Directory standaardiseren als het centrale identiteits--en verificatiesysteem

**Richt lijnen**: behoud een inventaris van de gebruikers accounts met beheerders toegang tot het besturings vlak (zoals Azure Portal) van uw Microsoft Azure peering service-resources.

Gebruik het deel venster identiteits-en toegangs beheer (IAM) in de Azure Portal voor uw abonnement om op rollen gebaseerd toegangs beheer op basis van Azure (Azure RBAC) te configureren. De rollen worden toegepast op gebruikers, groepen, service-principals en beheerde identiteiten in Azure Active Directory (Azure AD).

- [Azure-roltoewijzingen toevoegen of verwijderen met behulp van de Azure-portal](../role-based-access-control/role-assignments-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Toepassingsidentiteiten veilig en automatisch beheren

**Hulp**: beheerde identiteiten worden niet ondersteund voor peering-service. Voor services die geen beheerde identiteiten ondersteunen, zoals een peering-service, gebruikt u Azure Active Directory (Azure AD) voor het maken van een service-principal met beperkte machtigingen op het resource niveau. 

- [Door Azure beheerde identiteiten](../active-directory/managed-identities-azure-resources/overview.md)

- [Services die beheerde identiteiten voor Azure-resources ondersteunen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Azure-service-principal](/powershell/azure/create-azure-service-principal-azureps)

- [Een service-principal maken met certificaten](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Eenmalige aanmelding (SSO) van Azure AD gebruiken voor toegang tot toepassingen

**Hulp**: Microsoft Azure peering-Service gebruikt Azure Active Directory (Azure AD) om identiteits-en toegangs beheer te bieden aan Azure-resources. Dit omvat ondernemingsidentiteiten zoals werknemers, maar ook externe identiteiten, zoals partners, verkopers en leveranciers. 

Gebruik eenmalige aanmelding voor het beheren en beveiligen van de toegang tot de gegevens en bronnen van uw organisatie op locatie en in de Cloud. Verbind al uw gebruikers, toepassingen en apparaten met Azure AD voor naadloze, veilige toegang en meer zichtbaarheid en controle.

- [Inzicht in eenmalige aanmelding van toepassingen met Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Gebruik krachtige besturingselementen voor verificatie voor alle op Azure Active Directory gebaseerde toegang

**Hulp** bij het inschakelen van multi-factor authentication met Azure Active Directory (Azure AD) en het volgen van aanbevelingen voor identiteits-en toegangs beheer van Azure Security Center.

- [Multi-factor Authentication inschakelen in azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identiteit en toegang bewaken in Azure Security Center](../security-center/security-center-identity-access.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Bewaken van accounts op afwijkingen en waarschuwingenbeheer

**Hulp**: gebruik PRIVILEGED Identity Management (PIM) met Azure Active Directory (Azure AD) voor het genereren van Logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten in de omgeving worden uitgevoerd. Daarnaast kunt u met Azure AD-risico detectie waarschuwingen en rapporten bekijken over Risk ante gebruikers gedrag.

- [Privileged Identity Management implementeren (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Meer informatie over Azure AD-risico detectie](../active-directory/identity-protection/overview-identity-protection.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Toegang tot Azure-resource beperken op basis van voorwaarden

**Richt lijnen**: Gebruik voorwaardelijke toegang met Azure Active Directory (Azure AD) voor meer gedetailleerd toegangs beheer op basis van door de gebruiker gedefinieerde voor waarden, zoals het vereisen van gebruikers aanmeldingen vanuit bepaalde IP-bereiken voor het gebruik van multi-factor Authentication. Een gedetailleerd beheer van verificatie sessies kan ook worden gebruikt via beleid voor voorwaardelijke toegang van Azure AD voor verschillende use cases. 

- [Overzicht van voorwaardelijke toegang voor Azure](../active-directory/conditional-access/overview.md)

- [Algemeen beleid voor voorwaardelijke toegang](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Beheer van verificatiesessies met voorwaardelijke toegang configureren](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: Onbedoelde blootstelling van referenties elimineren

**Richt lijnen**: referentie scanner in azure DevOps implementeren om referenties te identificeren die in de code worden gebruikt. Credential scanner stimuleert ook het verplaatsen van gedetecteerde referenties naar veiliger locaties, zoals Azure Key Vault.

Voor GitHub kunt u de systeemeigen functie voor het scannen op geheimen gebruiken om referenties of een andere vormen van geheimen binnen de code te identificeren.

- [Referentie scanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Scannen op geheimen in GitHub](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="privileged-access"></a>Uitgebreide toegang

*Zie [Azure Security Benchmark: uitgebreide toegang](/azure/security/benchmarks/security-controls-v2-privileged-access) voor meer informatie.*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Gebruikers met zeer uitgebreide bevoegdheden beveiligen en beperken

**Richt lijnen**: Beperk het aantal gebruikers accounts met zeer privileged en beveilig deze accounts op een hoger niveau. 

De meest essentiële ingebouwde rollen in Azure Active Directory (Azure AD) zijn globale beheerder en de beheerder van de geprivilegieerde rol, omdat gebruikers die aan deze twee rollen zijn toegewezen, beheerders rollen kunnen delegeren. 

Met deze bevoegdheden kunnen gebruikers direct of indirect elke resource in uw Azure-omgeving lezen en wijzigen:

- Beheerder van globale beheerder/bedrijf: gebruikers met deze rol hebben toegang tot alle beheer functies in azure AD, evenals services die gebruikmaken van Azure AD-identiteiten.

- Beheerder van geprivilegieerde rol: gebruikers met deze rol kunnen roltoewijzingen beheren in azure AD, en in Azure AD Privileged Identity Management (PIM). Daarnaast kunt u met deze rol alle aspecten van PIM en administratieve eenheden beheren.

Er zijn mogelijk andere essentiële rollen die moeten worden geregeld als u aangepaste rollen gebruikt waaraan bepaalde privileged-machtigingen zijn toegewezen. Pas vergelijk bare besturings elementen toe op het beheerders account van essentiële bedrijfs activa.  

Activeer just-in-time (JIT) uitgebreide toegang tot Azure-resources en Azure AD met behulp van Azure AD Privileged Identity Management (PIM). JIT verleent gebruikers alleen tijdelijke machtigingen voor het uitvoeren van bevoegde taken op het moment dat ze deze nodig hebben. PIM kan ook beveiligingswaarschuwingen genereren wanneer er verdachte of onveilige activiteiten worden vastgesteld in uw Azure AD-organisatie.

- [Machtigingen voor beheerdersrollen in Azure AD](/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

- [Beveiligingswaarschuwingen van Azure Privileged Identity Management gebruiken](../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Bevoegde toegang beveiligen voor hybride implementaties en cloudimplementaties in Azure AD](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Beheerderstoegang tot essentiële bedrijfssystemen beperken

**Richt lijnen**: de toegang tot bedrijfskritische systemen isoleren door accounts te beperken waaraan privileged toegang is verleend voor de abonnementen en beheer groepen waartoe ze behoren. 

Beperk de toegang tot de beheer-, identiteits-en beveiligings systemen die beheerders toegang hebben tot uw bedrijfs kritieke assets, zoals Active Directory-domein controllers (DC), beveiligings hulpprogramma's en Systeembeheer Programma's met agents die zijn geïnstalleerd op essentiële bedrijfs systemen. Aanvallers die deze beheer-en beveiligings systemen misbruiken, kunnen ze onmiddellijk weaponize om bedrijfs kritieke activa te beschadigen. 

Alle typen besturings elementen voor toegang moeten worden afgestemd op uw bedrijfs segmentatie strategie om een consistent toegangs beheer te garanderen. 

Zorg ervoor dat u afzonderlijke bevoorrechte accounts toewijst die verschillend zijn van de standaard gebruikers accounts die worden gebruikt voor e-mail, surfen en productiviteits taken.

- [Azure-onderdelen en referentie model](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Toegang tot beheer groepen](https://docs.microsoft.com/azure/governance/management-groups/overview#management-group-access)

- [Beheerders van Azure-abonnementen](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Gebruikerstoegang regelmatig controleren en afstemmen

**Hulp**: Azure Active Directory (AD) bevat logboeken waarmee u verouderde accounts kunt detecteren. Daarnaast kunt u Azure Identity Access revisies gebruiken om groepslid maatschappen en de toegang tot bedrijfs toepassingen en roltoewijzingen op efficiënte wijze te beheren. Gebruikers toegang kan regel matig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben.

- [Azure AD-activiteiten rapporten zoeken](../active-directory/reports-monitoring/howto-find-activity-reports.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Noodtoegang instellen in Azure AD

**Richt lijnen**: Stel een nood account in voor toegang, wanneer de reguliere beheerders accounts niet beschikbaar zijn, om te voor komen dat uw Azure Active Directory-organisatie (Azure AD) per ongeluk wordt vergrendeld. Accounts voor toegang in nood gevallen kunnen machtigingen op een hoger niveau bevatten en mogen niet worden toegewezen aan specifieke personen. Beperk de nood toegangs accounts tot scenario's met nood gevallen of afbreek glas.

Zorg ervoor dat de referenties (zoals wacht woord, certificaat of Smart Card) voor accounts voor toegang in nood gevallen beveiligd blijven en alleen bekend zijn bij personen die gemachtigd zijn om ze in emergent situaties te gebruiken.

- [Accounts voor noodtoegang beheren in Azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="pa-5-automate-entitlement-management"></a>PA-5: beheer van rechten automatiseren 

**Richt lijnen**: gebruik Azure Active Directory (Azure AD) rechten voor beheer om werk stromen voor toegangs aanvragen te automatiseren, met inbegrip van toegangs toewijzingen, recensies en verval datum. Het is ook mogelijk om twee of meerdere fasen goed te keuren.

- [Wat zijn Azure Active Directory (Azure AD)-toegangs beoordelingen](../active-directory/governance/access-reviews-overview.md)

- [Wat is Azure Active Directory (Azure AD) toeslag beheer](../active-directory/governance/entitlement-management-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Werkstations met uitgebreide toegang gebruiken

**Hulp**: gebruik een privileged Access-werk station (Paw) met meervoudige verificatie van Azure Active Directory (Azure AD), dat is ingeschakeld om u aan te melden en uw Azure Sentinel-gerelateerde resources te configureren.

- [Privileged Access Workstations](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Een Azure AD-implementatie op basis van een Cloud met meerdere factoren plannen](../active-directory/authentication/howto-mfa-getstarted.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Principe van minimale bevoegdheden hanteren 

**Richt lijnen**: met Azure op rollen gebaseerd toegangs beheer (Azure RBAC) kunt u toegang tot Azure-resources beheren via roltoewijzingen. U kunt deze rollen toewijzen aan gebruikers, groeps service-principals en beheerde identiteiten. Er zijn vooraf gedefinieerde ingebouwde rollen voor bepaalde resources, en deze rollen kunnen worden geïnventariseerd of opgevraagd via hulpprogram ma's als Azure CLI, Azure PowerShell en de Azure Portal.

De bevoegdheden die u toewijst aan resources via Azure RBAC, moeten altijd beperkt zijn tot wat vereist is voor de rollen. Beperkte bevoegdheden vormen een aanvulling op de just-in-time (JIT)-benadering van Azure Active Directory (Azure AD) Privileged Identity Management (PIM), en deze bevoegdheden moeten regel matig worden gecontroleerd.

Gebruik ingebouwde rollen om machtigingen toe te wijzen en definieer alleen aangepaste rollen wanneer dit echt nodig is.

- [Wat is Azure Role-based Access Control (Azure RBAC)](../role-based-access-control/overview.md)

- [Azure RBAC configureren](../role-based-access-control/role-assignments-portal.md)

- [Identiteits- en toegangsbeoordelingen van Azure AD](../active-directory/governance/access-reviews-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="asset-management"></a>Asset-management

*Zie [Azure Security Benchmark: assetmanagement](/azure/security/benchmarks/security-controls-v2-asset-management) voor meer informatie.*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Beveiligingsteam inzicht geven in risico's voor assets

**Richtlijnen**: Zorg ervoor dat beveiligingsteams de machtiging Beveiligingslezer hebben in uw Azure-tenant en -abonnementen zodat ze kunnen controleren op beveiligingsrisico's met behulp van Azure Security Center. 

Afhankelijk van de manier waarop de verantwoordelijkheden van het beveiligingsteam zijn gestructureerd, kan de controle op beveiligingsrisico's de verantwoordelijkheid zijn van een centraal beveiligingsteam of een lokaal team. Om die reden moeten beveiligingsinzichten en -risico's altijd centraal worden verzameld in een organisatie. 

De machtiging Beveiligingslezer kan breed worden toegepast op een hele tenant (hoofdbeheergroep) of in het bereik van beheergroepen of specifieke abonnementen. 

Er zijn mogelijk extra machtigingen vereist om inzicht te krijgen in werk belastingen en services. 

- [Overzicht van rol Beveiligingslezer](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#security-reader)

- [Overzicht van Azure-beheergroepen](../governance/management-groups/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Controleer of het beveiligingsteam toegang heeft tot de asset-inventaris en metagegevens

**Richt lijnen**: Zorg ervoor dat beveiligings teams toegang hebben tot een voortdurend bijgewerkte inventaris van assets in Azure. Beveiligingsteams hebben deze inventaris vaak nodig om de mogelijke blootstelling van hun organisatie aan toekomstige risico's te evalueren, en willen deze inventaris gebruiken als invoer waarmee ze de beveiliging constant kunnen verbeteren. 

Met de functie voor Azure Security Center Inventory en Azure resource Graph kunt u alle resources in uw abonnementen opvragen en detecteren, inclusief Azure-Services,-toepassingen en-netwerk bronnen.  

Organiseer op logische wijze assets op basis van de taxonomie van uw organisatie met behulp van tags en andere metagegevens in Azure (Naam, Beschrijving en Categorie).  

- [Query's maken met Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

- [Azure Security Center Asset Inventory Management](../security-center/asset-inventory.md)

- [Voor meer informatie over het labelen van assets raadpleegt u de hand leiding resource naamgeving en Tags toevoegen](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Gebruik alleen goedgekeurde Azure-Services

**Hulp**: Microsoft Azure peering-service ondersteunt implementaties op basis van Azure Resource Manager en het afdwingen van de configuratie met behulp van Azure policy in de naam ruimte micro soft. peering/peering. Gebruik Azure Policy om te controleren welke services gebruikers in uw omgeving kunnen inrichten en beperken. Gebruik Azure Resource Graph om resources binnen hun abonnementen op te vragen en te detecteren. U kunt Azure Monitor ook gebruiken om regels te maken voor het activeren van waarschuwingen wanneer een niet-goedgekeurde service wordt gedetecteerd.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Een specifiek resource type weigeren met Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

- [Query's maken met Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

**Hulp** bij het configureren van voorwaardelijke toegang van Azure om gebruikers de mogelijkheid te bieden om te communiceren met Azure Resource Manager door ' blok toegang ' te configureren voor de app Microsoft Azure management.

- [Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager te blok keren](../role-based-access-control/conditional-access-azure-management.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="logging-and-threat-detection"></a>Logboekregistratie en detectie van bedreigingen

*Zie [Azure Security Benchmark: logboekregistratie en detectie van bedreigingen](/azure/security/benchmarks/security-controls-v2-logging-threat-protection) voor meer informatie.*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: detectie van bedreigingen inschakelen voor Azure-resources

**Richt lijnen**: Zorg ervoor dat u verschillende typen Azure-assets bewaakt voor mogelijke dreigingen en afwijkingen. Richt u op het ophalen van waarschuwingen met hoge kwaliteit om te voor komen dat de fout-positieven worden gereduceerd. Waarschuwingen kunnen worden gebrond op basis van logboek gegevens, agents of andere gegevens.

Gebruik de Azure Security Center ingebouwde functie voor detectie van bedreigingen, die is gebaseerd op de bewaking van Azure service-telemetrie en het analyseren van service Logboeken. Gegevens worden verzameld met behulp van de Log Analytics-agent, die verschillende aan beveiliging gerelateerde configuraties en gebeurtenis logboeken van het systeem leest en de gegevens naar uw werk ruimte kopieert voor analyse. 

Daarnaast kunt u Azure Sentinel gebruiken om analyse regels te bouwen, die de bedreigingen doorzoeken die overeenkomen met specifieke criteria in uw omgeving. De regels genereren incidenten wanneer de criteria overeenkomen, zodat u elk incident kunt onderzoeken. Met Azure Sentinel kan ook bedreigings informatie van derden worden geïmporteerd om de detectie mogelijkheden van bedreigingen te verbeteren. 

- [Bescherming tegen bedreiging in Azure Security Center](/azure/security-center/threat-protection)

- [Naslag Gids voor beveiligings waarschuwingen Azure Security Center](../security-center/alerts-reference.md)

- [Aangepaste analyse regels maken voor het detecteren van bedreigingen](../sentinel/tutorial-detect-threats-custom.md)

- [Cyber Threat Intelligence met Azure Sentinel](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Detectie van bedreigingen inschakelen voor Azure identiteits- en toegangsbeheer

**Hulp**: Azure Active Directory (Azure AD) biedt gebruikers logboeken die kunnen worden weer gegeven met Azure AD Reporting of geïntegreerd met Azure monitor. Deze logboeken kunnen ook worden geïntegreerd met Azure Sentinel of andere beveiligings-en SIEM-oplossingen (Security Information and Event Management) of controle hulpprogramma's voor geavanceerde bewakings-en analyse cases:

- Aanmelden: het aanmeld rapport bevat informatie over het gebruik van beheerde toepassingen en aanmeldings activiteiten voor gebruikers

- Audit logboeken: voorziet in traceer baarheid via Logboeken voor alle wijzigingen die zijn aangebracht in de verschillende functies van Azure AD. Voor beelden van audit logboeken zijn wijzigingen die zijn aangebracht in resources binnen Azure AD, zoals het toevoegen of verwijderen van gebruikers, apps, groepen, rollen en beleids regels

- Risk ante aanmelding: een Risk ante aanmelding is een indicator voor een aanmeldings poging die mogelijk is uitgevoerd door iemand die geen rechtmatige eigenaar van een gebruikers account is.

- Gebruikers die zijn gemarkeerd voor risico: een Risk ante gebruiker is een indicator voor een gebruikers account dat mogelijk is aangetast

Azure Security Center kunt ook waarschuwen voor bepaalde verdachte activiteiten, zoals een uitzonderlijk aantal mislukte verificatie pogingen door verouderde accounts in een abonnement. Naast de basis bewaking voor beveiligings hygiëne, kan de module Threat Protection in Security Center ook extra uitgebreide beveiligings waarschuwingen verzamelen van afzonderlijke Azure-reken resources (zoals virtuele machines, containers, app service), gegevens bronnen (zoals SQL DB en Storage) en Azure-service lagen. Met deze mogelijkheid kunt u rekening afwijkingen in de afzonderlijke resources bekijken.

- [Activiteiten rapporten controleren in azure AD](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Azure AD Identity Protection inschakelen](../active-directory/identity-protection/overview-identity-protection.md)

- [Bescherming tegen bedreiging in Azure Security Center](/azure/security-center/threat-protection)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Logboekregistratie inschakelen voor Azure-netwerkactiviteiten

**Hulp** bij het configureren van de telemetrie van de verbinding om inzicht te krijgen in de connectiviteit tussen uw verbindings locatie en de

Micro soft-netwerk via Microsoft Azure peering-service.

- [Telemetrie van verbindingen configureren](measure-connection-telemetry.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Logboekregistratie inschakelen voor Azure-resources

**Hulp**: Azure-activiteiten logboek inschakelen en de logboeken naar een log Analytics-werk ruimte, Azure Event hub of Azure Storage-account verzenden voor archivering 

Activiteiten logboeken bieden inzicht in de bewerkingen die zijn uitgevoerd op uw Azure ExpressRoute-resources op het niveau van het besturings vlak. Met Azure-activiteiten logboek gegevens kunt u de ' What, wie en wanneer ' bepalen voor schrijf bewerkingen (PUT, POST, DELETE) die zijn uitgevoerd op het niveau van het besturings element van uw resources.

- [Azure-activiteitenlogboek](/azure/azure-monitor/platform/activity-log)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Beheer en analyse van beveiligingslogboek centraliseren

**Richt lijnen**: opslag en analyse van logboek registratie centraliseren om correlatie in te scha kelen. Zorg ervoor dat u voor elke logboek bron een gegevens eigenaar hebt toegewezen, toegangs richtlijnen, opslag locatie, welke hulpprogram ma's worden gebruikt voor het verwerken en openen van de gegevens en vereisten voor gegevens behoud.

Zorg ervoor dat u Azure-activiteiten logboeken integreert in uw centrale logboek registratie. Opname logboeken via Azure Monitor voor het verzamelen van beveiligings gegevens die zijn gegenereerd door eindpunt apparaten, netwerk bronnen en andere beveiligings systemen. Gebruik in Azure Monitor Log Analytics-werk ruimten om analyses uit te voeren en te uitvoeren en gebruik Azure Storage accounts voor lange termijn-en archiverings opslag.

Daarnaast kunt u gegevens inschakelen en vrijgeven aan Azure Sentinel of een SIEM van derden.

Veel organisaties kiezen voor het gebruik van Azure Sentinel voor ' hot ' gegevens die regel matig worden gebruikt en die worden Azure Storage voor ' koude ' gegevens die minder vaak worden gebruikt.

- [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Bewaarperiode voor logboek configureren

**Richt lijnen**: stel in azure monitor de Bewaar periode voor logboek registratie In voor log Analytics werk ruimten die zijn gekoppeld aan uw Azure-resources op basis van de nalevings voorschriften van uw organisatie.

- [Para meters voor het bewaren van Logboeken instellen](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Benchmark: respons op incidenten](/azure/security/benchmarks/security-controls-v2-incident-response) voor meer informatie.*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Voorbereiding: responsproces voor incidenten bijwerken voor Azure

**Richtlijnen**: Zorg ervoor dat uw organisatie processen heeft gedefinieerd om te reageren op beveiligingsincidenten, dat deze processen voor Azure zijn bijgewerkt en dat ze regelmatig worden uitgevoerd om de gereedheid ervan te garanderen.

- [Beveiliging in de complete bedrijfsomgeving implementeren](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Naslaggids voor respons op incidenten](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Voorbereiding: melding van incidenten instellen

**Richtlijnen**: Contactgegevens in Azure Security Center instellen in het geval van beveiligingsincidenten Deze contactgegevens worden door Microsoft gebruikt om contact met u op te nemen als het Microsoft Security Response Center (MSRC) vaststelt dat een niet-geautoriseerde partij toegang tot uw gegevens heeft gekregen. Er zijn ook opties waarmee u incidentwaarschuwingen en -meldingen in verschillende Azure-Services kunt aanpassen aan de hand van wat u als incidentrespons nodig acht. 

- [De Azure Security Center Security-contactpersoon instellen](../security-center/security-center-provide-security-contact-details.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: detectie en analyse: incidenten maken op basis van waarschuwingen van hoge kwaliteit 

**Richtlijnen**: Zorg ervoor dat u een proces hebt gedefinieerd om waarschuwingen van hoge kwaliteit te maken en de kwaliteit van waarschuwingen te meten. Zo kunt u op grond van wat u uit eerdere incidenten hebt geleerd een hogere prioriteit toekennen aan waarschuwingen die bestemd zijn voor analisten, zodat deze geen tijd hoeven te verspillen aan fout-positieven. 

Waarschuwingen van hoge kwaliteit kunnen worden samengesteld op basis van ervaringen uit eerdere incidenten, op grond van gevalideerde communitybronnen, en door tools te gebruiken die zijn ontworpen om waarschuwingen te genereren en op te schonen door diverse signaalbronnen samen te voegen en er correlaties tussen te ontdekken. 

Azure Security Center biedt waarschuwingen van hoge kwaliteit in veel Azure-assets. U kunt de ASC-dataconnector gebruiken om de waarschuwingen te streamen naar Azure Sentinel. Met Azure Sentinel kunt u geavanceerde waarschuwingsregels opstellen om automatisch incidenten te genereren voor onderzoek. 

Exporteer de waarschuwingen en aanbevelingen van Azure Security Center met behulp van de exportfunctie om zo beter risico's voor Azure-resources te kunnen identificeren. U kunt waarschuwingen en aanbevelingen handmatig exporteren of doorlopend.

- [Export configureren](../security-center/continuous-export.md)

- [Waarschuwingen streamen naar Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Detectie en analyse: een incident onderzoeken

**Richt lijnen**: Zorg ervoor dat analisten verschillende gegevens bronnen kunnen opvragen en gebruiken, aangezien ze potentiële incidenten onderzoeken om een volledig overzicht van wat er is gebeurd te bouwen. Er moeten verschillende logboeken worden verzameld om de activiteiten van een mogelijke aanvaller in de kill chain te volgen om zo blinde vlekken te voorkomen.  U moet er ook voor zorgen dat inzichten en resultaten worden vastgelegd voor andere analisten, zodat deze kunnen worden geraadpleegd als historische naslaginformatie.  

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

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Detectie en analyse: prioriteiten toekennen aan incidenten

**Richtlijnen**: Bied context aan analisten voor de incidenten waarop ze zich eerst moeten concentreren op basis van de ernst van waarschuwingen en de gevoeligheid van assets. 

Azure Security Center wijst aan elke waarschuwing een ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of de analyse die wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau waarvan er sprake is van schadelijke opzet achter de activiteit die tot de waarschuwing heeft geleid.

Daarnaast kunt u resources markeren met behulp van tags en een naamgevingssysteem opzetten om Azure-resources te identificeren en categoriseren, met name voor resources die gevoelige gegevens verwerken.  Het is uw verantwoordelijkheid om prioriteit te geven aan het oplossen van waarschuwingen op basis van de ernst van de Azure-resources en -omgeving waarin het incident heeft plaatsgevonden.

- [Beveiligingswaarschuwingen in Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Tags gebruiken om Azure-resources te organiseren](/azure/azure-resource-manager/resource-group-using-tags)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Insluiting, uitschakeling en herstel: het afhandelen van incidenten automatiseren

**Richtlijnen**: Automatiseer handmatige, terugkerende taken om de responstijd te versnellen en de werklast van analisten te verlichten. Het uitvoeren van handmatige taken duurt langer, waardoor elk incident trager gaat en het aantal incidenten dat een analist kan afhandelen, kleiner wordt. Daarnaast raken analisten sneller vermoeid door handmatige taken, met als gevolg dat het risico van menselijke fouten toeneemt en analisten zich minder goed kunnen concentreren. Gebruik voorzieningen voor de automatisering van werkstromen in Azure Security Center en Azure Sentinel om automatisch acties te activeren of een playbook uit te voeren in reactie op binnenkomende beveiligingswaarschuwingen. Het playbook voert acties uit, zoals het verzenden van meldingen, het uitschakelen van accounts en het isoleren van problematische netwerken. 

- [Werkstroomautomatisering configureren in Security Center](../security-center/workflow-automation.md)

- [Geautomatiseerde bedreigingsreacties instellen in Azure Security Center](https://docs.microsoft.com/azure/security-center/tutorial-security-incident#triage-security-alerts)

- [Geautomatiseerde bedreigingsreacties instellen in Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="posture-and-vulnerability-management"></a>Beveiligingspostuur en beveiligingsproblemen beheren

*Zie [Azure Security Benchmark: beveiligingspostuur en beveiligingsproblemen beheren](/azure/security/benchmarks/security-controls-v2-vulnerability-management) voor meer informatie.*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Veilige configuraties tot stand brengen voor Azure-services 

**Richt lijnen**: beveiligings Guardrails definiëren voor infra structuur-en DevOps teams door de Azure-Services die ze gebruiken eenvoudig veilig te configureren. 

Start uw beveiligings configuratie van Azure-Services met de service basislijnen in de Azure Security Bench Mark en pas deze aan als nodig voor uw organisatie. 

Gebruik Azure Security Center om Azure Policy te configureren om de configuraties van uw Azure-resources te controleren en af te dwingen. 

U kunt Azure-blauw drukken gebruiken om de implementatie en configuratie van services en toepassings omgevingen te automatiseren, met inbegrip van Azure Resource Manager sjablonen, Azure RBAC-besturings elementen en-beleid, in één blauw druk-definitie.

- [Afbeelding van Guardrails-implementatie in de schaal zone op ondernemings niveau](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture#landing-zone-expanded-definition)

- [Werken met beveiligings beleid in Azure Security Center](../security-center/tutorial-security-policy.md)

- [Beleidsregels voor het afdwingen van naleving maken en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprints](../governance/blueprints/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Veilige configuraties onderhouden voor Azure-services

**Richt lijnen**: niet van toepassing;  deze aanbeveling is bedoeld voor reken resources.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Voer regelmatige simulaties van aanvallen uit

**Richtlijnen**: Voer zo vaak u als u wilt penetratietests of Red Teaming-activiteiten uit op uw Azure-resources, en zorg ervoor dat alle kritieke beveiligingsbevindingen worden opgelost.
Ga te werk volgens de Microsoft Cloud Penetration Testing Rules of Engagement (Regels voor het inzetten van penetratietests voor Microsoft Cloud ) zodat u zeker weet dat uw penetratietests niet conflicteren met Microsoft-beleid. Gebruik de strategie van Microsoft en de uitvoering van Red Teaming-activiteiten, en voer een penetratietest van de live site uit op basis van een infrastructuur, services en toepassingen die door Microsoft worden beheerd.

- [Penetratietesten in Azure](../security/fundamentals/pen-testing.md)

- [Regels voor het inzetten van penetratietests](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

## <a name="governance-and-strategy"></a>Governance en strategie

*Zie [Azure Security Benchmark: governance en strategie](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

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
- [Azure Security Architecture Recommendation - Storage, data, and encryption](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json) (Aanbeveling voor Azure-beveiligingsarchitectuur: opslag, gegevens en versleuteling)

- [Azure Security Fundamentals - Azure Data security, encryption, and storage](../security/fundamentals/encryption-overview.md) (Basisprincipes van Azure-beveiliging: Azure-gegevensbeveiliging, -versleuteling en -opslag)

- [Cloud Adoption Framework - Azure data security and encryption best practices](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json) (Cloud Adoption Framework: best practices voor Azure-gegevensbeveiliging en -versleuteling)

- [Azure Security Benchmark - Asset management](/azure/security/benchmarks/security-benchmark-v2-asset-management) (Azure Security Benchmark: assetmanagement)

- [Azure Security Benchmark - Data Protection](/azure/security/benchmarks/security-benchmark-v2-data-protection) (Azure Security Benchmark: gegevensbeveiliging)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Segmentatiestrategie van bedrijf definiëren 

**Richtlijnen**: Stel een bedrijfsstrategie op om de toegang tot assets te segmenteren door een combinatie van besturingselementen voor het beheer van identiteiten, netwerken, toepassingen, abonnementen, beheergroepen en andere besturingselementen te gebruiken.

Zorg dat u een nauwgezette afweging maakt tussen de noodzaak om de beveiliging van de rest te scheiden en de noodzaak om systemen die met elkaar moeten kunnen communiceren en toegang moeten hebben tot gegevens, in staat te stellen om hun dagelijkse werklast uit te voeren.

Zorg ervoor dat de segmentatiestrategie consistent wordt geïmplementeerd voor alle typen besturingselementen, zoals voor netwerkbeveiliging, identiteits- en toegangsmodellen, toepassingsbevoegdheden/toegangsmodellen evenals besturingselementen voor door mensen uitgevoerde processen.

- [Richtlijnen voor segmentatiestrategie in Azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Richtlijnen voor segmentatiestrategie in Azure (document)](/security/compass/governance#enterprise-segmentation-strategy)

- [Netwerksegmentatie afstemmen op segmentatiestrategie van bedrijf](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Strategie voor het beheer van beveiligingspostuur definiëren

**Richtlijnen**: Meet en beperk voortdurend de risico's die alle individuele assets en de omgeving die ze hosten, lopen. Ken hogere prioriteiten toe aan hoogwaardige assets en assets die zeer kwetsbaar zijn voor aanvallen, zoals gepubliceerde toepassingen, punten voor binnenkomend en uitgaand netwerkverkeer, gebruikers- en beheerderseindpunten enzovoort.

- [Azure Security Benchmark: beveiligingspostuur en beveiligingsproblemen beheren](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Organisatierollen, verantwoordelijkheden en aansprakelijkheden afstemmen

**Richtlijnen**: Zorg ervoor dat u een duidelijke strategie voor rollen en verantwoordelijkheden in uw beveiligingsorganisatie schriftelijk vastlegt en bekendmaakt. Ken een hoge prioriteiten toe aan het verschaffen van duidelijkheid over wie aansprakelijk is voor beveiligingsbeslissingen. Bied opleidingen aan iedereen over het gedeelde verantwoordelijkheidsmodel en biedt technische teams trainingen over technologie ter beveiliging van de cloud.

- [Best practice voor Azure-beveiliging 1: personen: Teams trainen inzake het cloudbeveiligingstraject](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Best practice voor Azure-beveiliging 2: personen: Teams trainen inzake cloudbeveiligingstechnologie](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure Security Best Practice 3 - proces: Aansprakelijkheid toewijzen voor beslissingen over cloudbeveiliging](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

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

- [Azure Security Benchmark: netwerkbeveiliging](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Overzicht van Azure-netwerkbeveiliging](../security/fundamentals/network-overview.md)

- [Strategie voor architectuur van bedrijfsnetwerk](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Strategie voor het gebruik van identiteiten en uitgebreide toegang definiëren

**Richtlijnen**: Stel een benadering op voor het gebruik van identiteiten en uitgebreide toegang als onderdeel van de algehele strategie voor toegangsbeheer in uw organisatie.  

Deze strategie moet gedocumenteerde richtlijnen, beleid en standaarden bevatten voor de volgende elementen: 

-   Een gecentraliseerd identiteits- en verificatiesysteem en bijbehorende interconnectiviteit met andere interne en externe identiteitssystemen

-   Krachtige verificatiemethoden in verschillende gebruiksscenario's en onder verschillende voorwaarden

-   Bescherming van gebruikers met zeer uitgebreide bevoegdheden

-   Bewaking en verwerking van afwijkende gebruikersactiviteiten  

-   Proces voor het beoordelen en op elkaar afstemmen van de identiteit en toegangsrechten van gebruikers

Raadpleeg de volgende bronnen voor meer informatie:

- [Azure Security Benchmark: Identity management](/azure/security/benchmarks/security-benchmark-v2-identity-management) (Azure Security Benchmark: identiteitsbeheer)

- [Azure Security Benchmark - Privileged access](/azure/security/benchmarks/security-benchmark-v2-privileged-access) (Azure Security Benchmark: uitgebreide toegang)

- [Azure Security Best Practice 11: architectuur. Eén uniforme beveiligingsstrategie](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Overzicht van beveiliging met Azure-identiteitsbeheer](../security/fundamentals/identity-management-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

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

- [Azure Security Benchmark: logboekregistratie en detectie van bedreigingen](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Azure Security Benchmark: respons op incidenten](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Azure Security Best Practice 4: proces. Processen voor respons op incidenten bijwerken voor de cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Handleiding framework voor Azure-acceptatie, logboekregistratie en rapportagebeslissingen](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Schaalaanpassing, beheer en bewaking van Azure Enterprise](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="next-steps"></a>Volgende stappen

- Zie [Overzicht Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Meer informatie over [Azure-beveiligingsbasislijnen](/azure/security/benchmarks/security-baselines-overview)
