---
title: Azure-beveiligings basislijn voor Azure Database Migration Service
description: De Azure Database Migration Service Security Baseline voorziet in procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-Bench Mark.
author: msmbaldwin
ms.service: dms
ms.topic: conceptual
ms.date: 12/08/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: cee6f06588e340c03d6f2d7566d1af3627293118
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202484"
---
# <a name="azure-security-baseline-for-azure-database-migration-service"></a>Azure-beveiligings basislijn voor Azure Database Migration Service

In deze beveiligings basislijn worden richt lijnen van de [Azure Security Bench Mark-versie 2,0](../security/benchmarks/overview.md) ingesteld op Azure database Migration service. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen. De inhoud wordt gegroepeerd op de **beveiligings controles** die zijn gedefinieerd door de Azure Security-benchmark en de bijbehorende richt lijnen die van toepassing zijn op Azure database Migration service. **Besturings elementen** die niet van toepassing zijn op Azure database Migration service, zijn uitgesloten.

Als u wilt zien hoe Azure Database Migration Service volledig is toegewezen aan de beveiligings benchmark van Azure, raadpleegt u het [volledige Azure database Migration service beveiligings basislijn toewijzings bestand](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Benchmark: netwerkbeveiliging](/azure/security/benchmarks/security-controls-v2-network-security) voor meer informatie.*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: beveiliging voor intern verkeer implementeren

**Hulp**: wanneer u Azure database Migration service-resources implementeert, moet u een bestaand virtueel netwerk maken of gebruiken. Zorg ervoor dat alle virtuele netwerken van Azure een bedrijfs segmentatie principe volgen die afstemt op de bedrijfs Risico's. Elk systeem dat een hoger risico voor de organisatie kan opleveren, moet worden geïsoleerd binnen een eigen virtueel netwerk en voldoende is beveiligd met een netwerk beveiligings groep (NSG) en/of Azure Firewall.

Azure Database Migration Service maakt standaard gebruik van TLS 1,2. Als dat nodig is voor achterwaartse compatibiliteit voor de gegevens bron die wordt gemigreerd, kan ondersteuning voor TLS 1,0 of TLS 1,1 worden ingeschakeld op de Blade service configuratie van uw Azure Database Migration Service.

Gebruik Azure Sentinel om het gebruik van verouderde onveilige protocollen, zoals SSL/TLSv1, SMBv1, LM/NTLMv1, wDigest, niet-ondertekende LDAP-bindingen en zwakke versleuteling in Kerberos, te detecteren.

- [Een netwerk beveiligings groep met beveiligings regels maken](../virtual-network/tutorial-filter-network-traffic.md)

 

- [Azure Firewall implementeren en configureren](../firewall/tutorial-firewall-deploy-portal.md) 

- [Volg de vereiste documentatie om de poorten te openen voor Azure Database Migration Service hier](pre-reqs.md#prerequisites-common-across-migration-scenarios)

- [Azure Sentinel Inveilige protocollen werkmap](../sentinel/quickstart-get-visibility.md#use-built-in-workbooks)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="ns-2-connect-private-networks-together"></a>NS-2: particuliere netwerken met elkaar verbinden

**Richt lijnen**: als voor de migratie gebruik wordt gemaakt van een ander netwerk verkeer, hebt u de mogelijkheid om Azure ExpressRoute of een virtueel particulier netwerk (VPN) te gebruiken om particuliere verbindingen te maken tussen Azure-data centers en on-premises infra structuur in een co-locatie omgeving. ExpressRoute-verbindingen gaan niet via het open bare Internet en bieden meer betrouw baarheid, hogere snelheden en lagere latenties dan typische Internet verbindingen. Voor punt-naar-site-VPN en site-naar-site-VPN kunt u on-premises apparaten of netwerken verbinden met een virtueel netwerk met behulp van een combi natie van deze VPN-opties en Azure ExpressRoute. Als u twee of meer virtuele netwerken in azure met elkaar wilt verbinden, gebruikt u peering op virtueel netwerk. Netwerk verkeer tussen gekoppelde virtuele netwerken is privé en wordt opgeslagen in het Azure-backbone-netwerk.

- [Wat zijn de ExpressRoute-connectiviteits modellen?](../expressroute/expressroute-connectivity-models.md) 

- [Overzicht van Azure VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) 

- [Peering op virtueel netwerk](../virtual-network/virtual-network-peering-overview.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: Toegang tot Azure-services via particulier netwerk tot stand brengen

**Richt lijnen**: gebruik, indien van toepassing, een persoonlijke Azure-koppeling om persoonlijke toegang tot de bron-en doel Services, zoals Azure SQL Server, of andere vereiste services tijdens de migratie in te scha kelen.  Gebruik Azure Virtual Network Service-eind punten als Azure private link nog niet beschikbaar is.  Zowel Azure private link als service-eind punten bieden beveiligde toegang tot services via een geoptimaliseerde route via het Azure-backbone-netwerk zonder het Internet te passeren.  

Bovendien moet u ervoor zorgen dat aan de vereisten wordt voldaan voordat Azure Database Migration Service op uw virtuele netwerk wordt ingericht, met inbegrip van de communicatie poorten die moeten worden toegestaan. 

- [Overzicht van vereisten voor het gebruik van de Azure Database Migration Service](pre-reqs.md)

   

- [Persoonlijke Azure-koppeling](../private-link/private-link-overview.md)

- [Informatie over Virtual Network Service-eind punten](../virtual-network/virtual-network-service-endpoints-overview.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="identity-management"></a>Identiteitsbeheer

*Zie [Azure Security Benchmark: Identiteitsbeheer](/azure/security/benchmarks/security-controls-v2-identity-management) voor meer informatie.*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Azure Active Directory standaardiseren als het centrale identiteits--en verificatiesysteem

**Hulp**: Azure Database Migration Service gebruikt Azure Active Directory (Azure AD) als de standaard service voor identiteits-en toegangs beheer. U moet Azure AD standaardiseren om het identiteits-en toegangs beheer van uw organisatie te bepalen in: Microsoft Cloud resources, zoals de Azure Portal, Azure Storage, de virtuele machine van Azure (Linux en Windows), Azure Key Vault, PaaS en SaaS-toepassingen.

De resources van uw organisatie, zoals toepassingen in Azure of resources van uw bedrijfsnetwerk.

Het beveiligen van Azure AD moet een hoge prioriteit hebben in de cloudbeveiligingsprocedure van uw organisatie. Azure AD biedt een id-beveiligingsscore om u te helpen beoordelen in hoeverre uw identiteitsbeveiliging voldoet aan de aanbevelingen op basis van best practices van Microsoft. Gebruik de score om te meten hoe nauwkeurig uw configuratie overeenkomt met aanbevelingen op basis van best practices, en om verbeteringen aan te brengen in uw beveiligingsaanpak.

Azure AD biedt ondersteuning voor externe identiteiten waarmee gebruikers zonder Microsoft-account zich kunnen aanmelden bij hun toepassingen en resources met hun externe identiteit.

- [Tenancy in Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Een Azure AD-instantie maken en configureren](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Externe ID-providers voor een toepassing gebruiken](/azure/active-directory/b2b/identity-providers) 

- [Wat is de id-beveiligingsscore in Azure Active Directory?](../active-directory/fundamentals/identity-secure-score.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Toepassingsidentiteiten veilig en automatisch beheren

**Hulp**: voor de Azure data base Migration service moeten gebruikers een toepassings-id (Service Principle) en een verificatie sleutel in azure Active Directory (Azure AD) maken voor migraties naar Azure SQL database beheerde instantie in de online modus. Deze toepassings-ID vereist de rol Inzender op het abonnements niveau (dit wordt niet aanbevolen als gevolg van de overmatige rol van Inzender voor toegangs machtigingen) of het maken van aangepaste rollen met specifieke machtigingen die zijn vereist voor de Azure data base Migrations-service.

Het is raadzaam deze toepassings-ID te verwijderen nadat de migraties zijn voltooid.

- [Hoe toepassings-ID wordt gebruikt in de online migratie modus](tutorial-sql-server-managed-instance-online.md)

- [Aangepaste rollen voor SQL Server naar online migraties van Azure SQL Managed instance](resource-custom-roles-sql-db-managed-instance.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Eenmalige aanmelding (SSO) van Azure AD gebruiken voor toegang tot toepassingen

**Hulp**: Azure database Migration service is geïntegreerd met Azure Active Directory voor identiteits-en toegangs beheer tot Azure-resources, Cloud toepassingen en on-premises toepassingen. Dit omvat ondernemingsidentiteiten zoals werknemers, maar ook externe identiteiten, zoals partners, verkopers en leveranciers. Zo kunt u eenmalige aanmelding (SSO) gebruiken voor het beheren en beveiligen van de gegevens en resources van uw organisatie, on premises en in de cloud. Verbind al uw gebruikers, toepassingen en apparaten met Azure AD voor naadloze, veilige toegang en meer zichtbaarheid en controle.

- [Inzicht in eenmalige aanmelding van toepassingen met Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Gebruik krachtige besturingselementen voor verificatie voor alle op Azure Active Directory gebaseerde toegang

**Richt lijnen**: Azure Database Migration Service gebruikt Azure Active Directory die ondersteuning biedt voor krachtige verificatie methoden door middel van multi-factor Authentication en een krachtige methode met een wacht woord.
Multi-factor Authentication: Schakel Azure AD multi-factor Authentication in en volg Azure Security Center aanbevelingen voor identiteits-en toegangs beheer voor een aantal aanbevolen procedures in de configuratie van multi-factor Authentication. Multi-factor Authentication kan worden afgedwongen voor alle, Selecteer gebruikers of op het niveau per gebruiker op basis van aanmeldings voorwaarden en risico factoren.

Verificatie zonder wachtwoord: er zijn drie verificatieopties zonder een wachtwoord beschikbaar: Windows Hello voor Bedrijven, de Microsoft Authenticator-app en on-premises verificatiemethoden zoals smartcards.

Voor beheerders en bevoegde gebruikers zorgt u ervoor dat het sterkste verificatie niveau wordt gebruikt, gevolgd door het juiste sterke authenticatie beleid te implementeren voor andere gebruikers.

- [Multi-factor Authentication inschakelen in azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Algemene informatie over opties voor verificatie zonder wachtwoord voor Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md) 

- [Standaardwachtwoordbeleid voor Azure AD](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts) 

- [Verwijder ongeldige wacht woorden met Azure Active Directory wachtwoord beveiliging](../active-directory/authentication/concept-password-ban-bad.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Bewaken van accounts op afwijkingen en waarschuwingenbeheer

**Hulp**: Azure database Migration service is geïntegreerd met Azure Active Directory waarin de volgende gegevens bronnen worden verstrekt: aanmeldingen-het rapport met aanmeldingen bevat informatie over het gebruik van beheerde toepassingen en aanmeldings activiteiten voor gebruikers.

Auditlogboeken: traceerbaarheid via logboeken voor alle door diverse functies binnen Azure AD uitgevoerde wijzigingen. Voorbeelden van vermeldingen in auditlogboeken zijn wijzigingen die worden aangebracht in resources binnen Azure AD, zoals het toevoegen of verwijderen van gebruikers, apps, groepen, rollen en beleidsregels.

Riskante aanmeldingen - Een riskante aanmelding is een indicator van een aanmeldingspoging die mogelijk is uitgevoerd door iemand die geen rechtmatige eigenaar van een gebruikersaccount is.

Gebruikers voor wie wordt aangegeven dat ze risico lopen - Een riskante gebruiker is een indicator van een gebruikersaccount dat mogelijk is aangetast.

Deze gegevens bronnen kunnen worden geïntegreerd met Azure Monitor, Azure Sentinel of SIEM-systemen van derden.

Azure Security Center kan u ook waarschuwen voor bepaalde verdachte activiteiten, zoals een ongewoon aantal mislukte verificatiepogingen of afgeschafte accounts in het abonnement.

Azure Advanced Threat Protection (ATP) is een beveiligingsoplossing die aan de hand van signalen van Active Directory niet alleen geavanceerde bedreigingen kan identificeren, detecteren en onderzoeken, maar ook onderschepte identiteiten en schadelijke acties van binnenuit.

- [Auditrapporten over activiteiten in Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Riskante Azure AD-aanmeldingen weergeven](/azure/active-directory/reports-monitoring/concept-risky-sign-ins) 

- [Azure AD-gebruikers identificeren die zijn gemarkeerd voor riskante activiteiten](/azure/active-directory/reports-monitoring/concept-user-at-risk) 

- [Identiteits- en toegangsactiviteiten van gebruikers controleren in Azure Security Center](../security-center/security-center-identity-access.md) 

- [Waarschuwingen in de module Threat Intelligence Protection van Azure Security Center](../security-center/alerts-reference.md) 

- [Azure-activiteitenlogboeken integreren in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Toegang tot Azure-resource beperken op basis van voorwaarden

**Richt lijnen**: Azure database Migration service maakt gebruik van Azure AD voor verificatie waarbij voorwaardelijke toegang beschikbaar is voor een gedetailleerd toegangs beheer op basis van door de gebruiker gedefinieerde voor waarden, zoals gebruikers aanmeldingen vanuit bepaalde IP-bereiken, moeten multi-factor Authentication gebruiken voor aanmelding. Gedetailleerd beheerbeleid voor verificatiesessies kan ook worden gebruikt voor verschillende scenario's.

- [Overzicht van voorwaardelijke toegang in Azure](../active-directory/conditional-access/overview.md) 

- [Veelvoorkomend beleid voor voorwaardelijke toegang](../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [Het beheer van verificatiesessies via voorwaardelijke toegang configureren](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: Onbedoelde blootstelling van referenties elimineren

**Hulp**: met Azure database Migration service kunnen klanten code of configuraties of persistente gegevens implementeren of uitvoeren met identiteiten/geheimen, wordt aanbevolen om referentie scanner te implementeren om referenties te identificeren in code of configuraties of persistente gegevens. Door het gebruik van Credential Scanner worden gebruikers ook aangemoedigd om gedetecteerde referenties naar veiligere locaties, zoals Azure Key Vault, te verplaatsen.

Als GitHub wordt gebruikt, kunt u de systeem eigen functie voor het scannen van geheime gegevens gebruiken om referenties of een andere vorm van geheimen binnen de code te identificeren.

- [Referentie scanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Scannen op geheimen in GitHub](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="privileged-access"></a>Bevoegde toegang

*Zie [Azure Security Benchmark: uitgebreide toegang](/azure/security/benchmarks/security-controls-v2-privileged-access) voor meer informatie.*

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Gebruikerstoegang regelmatig controleren en afstemmen

**Hulp**: voor de Azure data base Migration service moeten gebruikers een toepassings-id (Service Principle) en een verificatie sleutel in azure Active Directory (Azure AD) maken voor migraties naar Azure SQL database beheerde instantie in de online modus. Het is raadzaam deze toepassings-ID te verwijderen nadat de migraties zijn voltooid.

- [Hoe toepassings-ID wordt gebruikt in de online migratie modus](tutorial-sql-server-managed-instance-online.md)

- [Aangepaste rollen voor SQL Server naar online migraties van Azure SQL Managed instance](resource-custom-roles-sql-db-managed-instance.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Noodtoegang instellen in Azure AD

**Richt lijnen**: buiten Azure database Migration service, als een algemene manier om te voor komen dat uw Azure AD-organisatie per ongeluk wordt vergrendeld, stelt u een account voor toegang voor nood gevallen in voor toegang wanneer normale beheerders accounts niet kunnen worden gebruikt. Accounts voor noodtoegang hebben meestal zeer uitgebreide bevoegdheden en kunnen beter niet aan specifieke personen worden toegewezen. Accounts voor noodtoegang zijn alleen bestemd voor echte noodsituaties waarin normale beheerdersaccounts niet kunnen worden gebruikt.

Zorg ervoor dat de referenties (zoals wachtwoord, certificaat of smartcard) voor accounts voor noodtoegang veilig worden bewaard en alleen bekend zijn bij personen die deze alleen in een noodgeval mogen gebruiken.

- [Accounts voor noodtoegang beheren in Azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="pa-5-automate-entitlement-management"></a>PA-5: beheer van rechten automatiseren 

**Hulp**: Azure database Migration service is geïntegreerd met Azure Active Directory om de bijbehorende resources te beheren. Gebruik Azure AD-functies voor rechten beheer om werk stromen voor toegangs aanvragen te automatiseren, met inbegrip van toegangs toewijzingen, beoordelingen en verval datums. Het is ook mogelijk om twee of meerdere fasen goed te keuren.

- [Wat zijn Azure AD-toegangs beoordelingen](../active-directory/governance/access-reviews-overview.md) 

- [Wat is het beheer van rechten van Azure AD](../active-directory/governance/entitlement-management-overview.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Werkstations met uitgebreide toegang gebruiken

**Richtlijnen**: Beveiligde, geïsoleerde werkstations zijn van cruciaal belang voor de beveiliging van gevoelige rollen als beheerders, ontwikkelaars en serviceoperators met vergaande bevoegdheden. Gebruik zeer beveiligde werk stations van gebruikers en/of Azure Bastion voor beheer taken. Gebruik Azure Active Directory, Microsoft Defender Advanced Threat Protection (ATP) en/of Microsoft Intune als u een beveiligd en beheerd gebruikerswerkstation voor beheertaken wilt implementeren. De beveiligde werkstations kunnen centraal worden beheerd en beveiligde configuraties afdwingen, waaronder krachtige verificatie, software- en hardwarebasislijnen, beperkte logische toegang en netwerktoegang.

- [Meer informatie over privileged Access workstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/.md) 

- [Een werkstation met uitgebreide toegang gebruiken](../active-directory/devices/howto-azure-managed-workstation.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Principe van minimale bevoegdheden hanteren 

**Hulp**: Azure database Migration service is geïntegreerd met op rollen gebaseerd toegangs beheer (RBAC) van Azure voor het beheren van de bijbehorende resources. Met Azure RBAC kunt u de toegang tot Azure-resources beheren via roltoewijzingen. U kunt deze rollen toewijzen aan gebruikers, groeperingen van service-principals en beheerde identiteiten. Er bestaan vooraf gedefinieerde, ingebouwde rollen voor bepaalde resources, en deze rollen kunnen worden geïnventariseerd of opgevraagd via tools zoals Azure CLI, Azure PowerShell en Azure Portal. De bevoegdheden die u via Azure RBAC toewijst aan resources, moeten altijd beperkt zijn tot wat vereist is voor de rollen. Dit is een aanvulling op de JIT-benadering van Azure AD Privileged Identity Management (PIM) en moet regelmatig worden geëvalueerd.

Gebruik ingebouwde rollen om machtigingen toe te wijzen en definieer alleen aangepaste rollen wanneer dit echt nodig is.

- [Wat is Azure Role-based Access Control (Azure RBAC)](../role-based-access-control/overview.md)

- [RBAC configureren in Azure](../role-based-access-control/role-assignments-portal.md) 

- [Identiteits- en toegangsbeoordelingen van Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Benchmark: gegevensbescherming](/azure/security/benchmarks/security-controls-v2-data-protection) voor meer informatie.*

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Gevoelige gegevens tijdens een overdracht versleutelen

**Hulp**: Azure database Migration service versleutelt de gegevens die onderweg zijn, van de door de klant geconfigureerde bronnen naar het data base Migration service-exemplaar standaard met behulp van TLS 1,2 of hoger. U kunt ervoor kiezen deze optie uit te scha kelen als de bron server geen TLS 1,2-verbinding ondersteunt, maar dit wordt sterk aangeraden. Het overdragen van gegevens van het data base Migration service-exemplaar naar het doel exemplaar is altijd versleuteld.  

Buiten de Azure Database Migration Service kunt u toegangs beheer gebruiken. gegevens in transit moeten worden beveiligd tegen buiten-band-aanvallen (zoals het vastleggen van verkeer) met behulp van versleuteling om ervoor te zorgen dat aanvallers de gegevens niet eenvoudig kunnen lezen of wijzigen. Zorg ervoor dat het HTTP-verkeer, dat clients die verbinding maken met uw Azure-resources, TLS v 1.2 of hoger kunnen onderhandelen.
Voor extern beheer gebruikt u SSH (voor Linux) of RDP/TLS (voor Windows) in plaats van een niet-versleuteld protocol. De verouderde SSL/TLS/SSH-versies, protocollen en zwakke versleuteling moeten worden uitgeschakeld.

Bij de onderliggende infra structuur biedt Azure gegevens in transit versleuteling standaard voor gegevens verkeer tussen Azure-data centers.

- [Meer informatie over versleuteling in transit met Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) 

- [Informatie over TLS-beveiliging](/security/engineering/solving-tls1-problem) 

- [Dubbele versleuteling voor Azure-gegevens in transit](../security/fundamentals/double-encryption.md#data-in-transit)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Gedeeld

## <a name="asset-management"></a>Asset-management

*Zie [Azure Security Benchmark: assetmanagement](/azure/security/benchmarks/security-controls-v2-asset-management) voor meer informatie.*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Beveiligingsteam inzicht geven in risico's voor assets

**Richtlijnen**: Zorg ervoor dat beveiligingsteams de machtiging Beveiligingslezer hebben in uw Azure-tenant en -abonnementen zodat ze kunnen controleren op beveiligingsrisico's met behulp van Azure Security Center. 

Afhankelijk van de manier waarop de verantwoordelijkheden van het beveiligingsteam zijn gestructureerd, kan de controle op beveiligingsrisico's de verantwoordelijkheid zijn van een centraal beveiligingsteam of een lokaal team. Om die reden moeten beveiligingsinzichten en -risico's altijd centraal worden verzameld in een organisatie. 

De machtiging Beveiligingslezer kan breed worden toegepast op een hele tenant (hoofdbeheergroep) of in het bereik van beheergroepen of specifieke abonnementen. 

Er zijn mogelijk extra machtigingen vereist om inzicht te krijgen in werk belastingen en services. 

- [Overzicht van rol Beveiligingslezer](../role-based-access-control/built-in-roles.md#security-reader)

- [Overzicht van Azure-beheergroepen](../governance/management-groups/overview.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Controleer of het beveiligingsteam toegang heeft tot de asset-inventaris en metagegevens

**Richt lijnen**: Tags Toep assen op uw Azure-resources, resource groepen en abonnementen om ze logisch in een taxonomie te organiseren. Elke tag bestaat uit een naam en een waardepaar. U kunt de naam Omgeving en de waarde Productie bijvoorbeeld toepassen op alle resources in de productie.

Azure Database Migration Service staat het uitvoeren van een toepassing of installatie van software op de bijbehorende resources niet toe. 

- [Query's maken met Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md) 

- [Azure Security Center Asset Inventory Management](../security-center/asset-inventory.md) 

- [Handleiding voor beslissingen over taggen en naamgeving voor resources](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Gebruik alleen goedgekeurde Azure-Services

**Hulp**: gebruik Azure Policy om te controleren welke Services gebruikers in uw omgeving kunnen inrichten en beperken. Gebruik Azure Resource Graph om resources binnen hun abonnementen op te vragen en te detecteren. U kunt Azure Monitor ook gebruiken om regels te maken voor het activeren van waarschuwingen wanneer een niet-goedgekeurde service wordt gedetecteerd.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md) 

- [Een specifiek resource type weigeren met Azure Policy](../governance/policy/samples/built-in-policies.md#general) 

- [Query's maken met Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

**Hulp**: gebruik de voorwaardelijke toegang van Azure om de mogelijkheden van gebruikers om te communiceren met Azure Resources Manager te beperken door ' toegang blok keren ' te configureren voor de app Microsoft Azure management.

- [Voorwaardelijke toegang configureren om de toegang tot Azure-bronnen beheer te blok keren](../role-based-access-control/conditional-access-azure-management.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="logging-and-threat-detection"></a>Logboekregistratie en detectie van bedreigingen

*Zie [Azure Security Benchmark: logboekregistratie en detectie van bedreigingen](/azure/security/benchmarks/security-controls-v2-logging-threat-detection) voor meer informatie.*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Bedreigingsdetectie inschakelen voor identiteits- en toegangsbeheer van Azure

**Richtlijnen**: Azure AD biedt de volgende gebruikerslogboeken die kunnen worden weergegeven in Azure AD Reporting of worden geïntegreerd met Azure Monitor, Azure Sentinel of andere tools voor SIEM/controle voor meer geavanceerde scenario's voor controle en analyse:

Aanmeldingen: het rapport voor aanmeldingsactiviteit bevat informatie over het gebruik van beheerde toepassingen en aanmeldingsactiviteiten van gebruikers

Auditlogboeken: traceerbaarheid via logboeken voor alle door diverse functies binnen Azure AD uitgevoerde wijzigingen. Voorbeelden van vermeldingen in auditlogboeken zijn wijzigingen die worden aangebracht in resources binnen Azure AD, zoals het toevoegen of verwijderen van gebruikers, apps, groepen, rollen en beleidsregels.

Riskante aanmeldingen - Een riskante aanmelding is een indicator van een aanmeldingspoging die mogelijk is uitgevoerd door iemand die geen rechtmatige eigenaar van een gebruikersaccount is.

Gebruikers voor wie wordt aangegeven dat ze risico lopen - Een riskante gebruiker is een indicator van een gebruikersaccount dat mogelijk is aangetast.

Azure Security Center kan u ook waarschuwen voor bepaalde verdachte activiteiten, zoals een ongewoon aantal mislukte verificatiepogingen of afgeschafte accounts in het abonnement. Naast de basiscontrole op beveiligingshygiëne, kan de module voor bedreigingsbeveiliging van Azure Security Center ook uitgebreidere beveiligingswaarschuwingen verzamelen van afzonderlijke Azure-compute-resources (virtuele machines, containers, app service), gegevensbronnen (SQL DB en opslag) en Azure-servicelagen. Deze functie geeft u inzicht in de account afwijkingen binnen de afzonderlijke resources.

- [Auditrapporten over activiteiten in Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Azure AD Identity Protection inschakelen](../active-directory/identity-protection/overview-identity-protection.md) 

- [Bescherming tegen bedreiging in Azure Security Center](/azure/security-center/threat-protection)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Logboekregistratie inschakelen voor Azure-netwerkactiviteiten

**Hulp** bij het inschakelen en verzamelen van NSG-bron Logboeken (netwerk beveiligings groep), NSG-stroom logboeken, Azure firewall-logboeken en Web Application firewall (WAF)-logboeken voor beveiligings analyse ter ondersteuning van incident onderzoek, de jacht van dreigingen en het genereren van beveiligings waarschuwingen. U kunt de stroom logboeken naar een Azure Monitor Log Analytics werk ruimte verzenden en vervolgens Traffic Analytics gebruiken om inzichten te geven.

Azure Database Migration Service produceert of verwerkt geen DNS-query logboeken die moeten worden ingeschakeld.

- [Stroom logboeken van netwerk beveiligings groepen inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Stroom logboeken van netwerk beveiligings groepen inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Azure Firewall-logboeken en metrische gegevens](../firewall/logs-and-metrics.md) 

- [Traffic Analytics inschakelen en gebruiken](../network-watcher/traffic-analytics.md) 

- [Controleren met Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) 

- [Azure-netwerk bewakings oplossingen in Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Beheer en analyse van beveiligingslogboek centraliseren

**Richt lijnen**: opslag en analyse van logboek registratie centraliseren om correlatie in te scha kelen. Zorg ervoor dat u voor elke logboek bron een gegevens eigenaar hebt toegewezen, toegangs richtlijnen, opslag locatie, welke hulpprogram ma's worden gebruikt voor het verwerken en openen van de gegevens en vereisten voor gegevens behoud.

Zorg ervoor dat u Azure-activiteiten logboeken integreert in uw centrale logboek registratie. Opname logboeken via Azure Monitor voor het verzamelen van beveiligings gegevens die zijn gegenereerd door eindpunt apparaten, netwerk bronnen en andere beveiligings systemen. Gebruik in Azure Monitor Log Analytics-werk ruimten om analyses uit te voeren en te uitvoeren en gebruik Azure Storage accounts voor lange termijn-en archiverings opslag.

Daarnaast kunt u gegevens inschakelen en vrijgeven aan Azure Sentinel of een SIEM van derden.

Veel organisaties kiezen voor het gebruik van Azure Sentinel voor ' hot ' gegevens die regel matig worden gebruikt en die worden Azure Storage voor ' koude ' gegevens die minder vaak worden gebruikt.

- [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Benchmark: respons op incidenten](/azure/security/benchmarks/security-controls-v2-incident-response) voor meer informatie.*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Voorbereiding: responsproces voor incidenten bijwerken voor Azure

**Richtlijnen**: Zorg ervoor dat uw organisatie processen heeft gedefinieerd om te reageren op beveiligingsincidenten, dat deze processen voor Azure zijn bijgewerkt en dat ze regelmatig worden uitgevoerd om de gereedheid ervan te garanderen.

- [Beveiliging in de complete bedrijfsomgeving implementeren](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud) 

- [Naslaggids voor respons op incidenten](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Voorbereiding: melding van incidenten instellen

**Richtlijnen**: Contactgegevens in Azure Security Center instellen in het geval van beveiligingsincidenten Deze contactgegevens worden door Microsoft gebruikt om contact met u op te nemen als het Microsoft Security Response Center (MSRC) vaststelt dat een niet-geautoriseerde partij toegang tot uw gegevens heeft gekregen. Er zijn ook opties waarmee u incidentwaarschuwingen en -meldingen in verschillende Azure-Services kunt aanpassen aan de hand van wat u als incidentrespons nodig acht. 

- [De Azure Security Center Security-contactpersoon instellen](../security-center/security-center-provide-security-contact-details.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Detectie en analyse: incidenten maken op basis van waarschuwingen van hoge kwaliteit

**Richtlijnen**: Zorg ervoor dat u een proces hebt gedefinieerd om waarschuwingen van hoge kwaliteit te maken en de kwaliteit van waarschuwingen te meten. Zo kunt u op grond van wat u uit eerdere incidenten hebt geleerd een hogere prioriteit toekennen aan waarschuwingen die bestemd zijn voor analisten, zodat deze geen tijd hoeven te verspillen aan fout-positieven. 

Waarschuwingen van hoge kwaliteit kunnen worden samengesteld op basis van ervaringen uit eerdere incidenten, op grond van gevalideerde communitybronnen, en door tools te gebruiken die zijn ontworpen om waarschuwingen te genereren en op te schonen door diverse signaalbronnen samen te voegen en er correlaties tussen te ontdekken. 

Azure Security Center biedt waarschuwingen van hoge kwaliteit in veel Azure-assets. U kunt de ASC-dataconnector gebruiken om de waarschuwingen te streamen naar Azure Sentinel. Met Azure Sentinel kunt u geavanceerde waarschuwingsregels opstellen om automatisch incidenten te genereren voor onderzoek. 

Exporteer de waarschuwingen en aanbevelingen van Azure Security Center met behulp van de exportfunctie om zo beter risico's voor Azure-resources te kunnen identificeren. U kunt waarschuwingen en aanbevelingen handmatig exporteren of doorlopend.

- [Export configureren](../security-center/continuous-export.md)

- [Waarschuwingen streamen naar Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoring door Azure Security Center**: Ja

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

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Detectie en analyse: prioriteiten toekennen aan incidenten

**Richtlijnen**: Bied context aan analisten voor de incidenten waarop ze zich eerst moeten concentreren op basis van de ernst van waarschuwingen en de gevoeligheid van assets. 

Azure Security Center wijst aan elke waarschuwing een ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of de analyse die wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau waarvan er sprake is van schadelijke opzet achter de activiteit die tot de waarschuwing heeft geleid.

Daarnaast kunt u resources markeren met behulp van tags en een naamgevingssysteem opzetten om Azure-resources te identificeren en categoriseren, met name voor resources die gevoelige gegevens verwerken.  Het is uw verantwoordelijkheid om prioriteit te geven aan het oplossen van waarschuwingen op basis van de ernst van de Azure-resources en -omgeving waarin het incident heeft plaatsgevonden.

- [Beveiligingswaarschuwingen in Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Tags gebruiken om Azure-resources te organiseren](/azure/azure-resource-manager/resource-group-using-tags)

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

*Zie [Azure Security Benchmark: beveiligingspostuur en beveiligingsproblemen beheren](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management) voor meer informatie.*

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Voer regelmatige simulaties van aanvallen uit

**Richtlijnen**: Voer zo vaak u als u wilt penetratietests of Red Teaming-activiteiten uit op uw Azure-resources, en zorg ervoor dat alle kritieke beveiligingsbevindingen worden opgelost.
Ga te werk volgens de Microsoft Cloud Penetration Testing Rules of Engagement (Regels voor het inzetten van penetratietests voor Microsoft Cloud ) zodat u zeker weet dat uw penetratietests niet conflicteren met Microsoft-beleid. Gebruik de strategie van Microsoft en de uitvoering van Red Teaming-activiteiten, en voer een penetratietest van de live site uit op basis van een infrastructuur, services en toepassingen die door Microsoft worden beheerd.

- [Penetratietesten in Azure](../security/fundamentals/pen-testing.md)

- [Regels voor het inzetten van penetratietests](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

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

- [Azure Security Benchmark - Asset management](/azure/security/benchmarks/security-controls-v2-asset-management) (Azure Security Benchmark: assetmanagement)

- [Azure Security Benchmark - Data Protection](/azure/security/benchmarks/security-controls-v2-data-protection) (Azure Security Benchmark: gegevensbeveiliging)

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

- [Azure Security Benchmark: beveiligingspostuur en beveiligingsproblemen beheren](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)

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

- [Azure Security Benchmark: netwerkbeveiliging](/azure/security/benchmarks/security-controls-v2-network-security)

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

- [Azure Security Benchmark: Identity management](/azure/security/benchmarks/security-controls-v2-identity-management) (Azure Security Benchmark: identiteitsbeheer)

- [Azure Security Benchmark - Privileged access](/azure/security/benchmarks/security-controls-v2-privileged-access) (Azure Security Benchmark: uitgebreide toegang)

- [Azure Security Best Practice 11: architectuur. Eén uniforme beveiligingsstrategie](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Overzicht van beveiliging met Azure-identiteitsbeheer](../security/fundamentals/identity-management-overview.md)

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

- [Azure Security Benchmark: logboekregistratie en detectie van bedreigingen](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Azure Security Benchmark: respons op incidenten](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Azure Security Best Practice 4: proces. Processen voor respons op incidenten bijwerken voor de cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Handleiding framework voor Azure-acceptatie, logboekregistratie en rapportagebeslissingen](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Schaalaanpassing, beheer en bewaking van Azure Enterprise](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="next-steps"></a>Volgende stappen

- Zie [Overzicht Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Meer informatie over [Azure-beveiligingsbasislijnen](/azure/security/benchmarks/security-baselines-overview)
