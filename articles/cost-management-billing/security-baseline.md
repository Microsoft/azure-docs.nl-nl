---
title: Azure-beveiligingsbasislijn voor Cost Management
description: De beveiligingsbasislijn voor Cost Management biedt richtlijnen voor procedures en resources voor het implementeren van de beveiligingsaanbevelingen die zijn opgenomen in de Azure Security Benchmark.
author: msmbaldwin
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 0155ed4df9ee2c22d1461633ac9efe225b53f9ca
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96530507"
---
# <a name="azure-security-baseline-for-cost-management"></a>Azure-beveiligingsbasislijn voor Cost Management

Met deze beveiligingsbasislijn worden richtlijnen uit [Azure Security Benchmark versie 2.0](../security/benchmarks/overview.md) toegepast op Azure Cost Management. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen. De inhoud is gegroepeerd op de **beveiligingsmaatregelen** die zijn omschreven in de Azure Security Benchmark en de bijbehorende richtlijnen die van toepassing zijn op Cost Management. **Maatregelen** die niet van toepassing zijn op Cost Management zijn hier niet opgenomen.

Als u wilt zien hoe Cost Management op alle punten scoort in de benchmark van Azure Security, kunt u [hier het volledige bestand bekijken (Engelstalig)](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="identity-management"></a>Identiteitsbeheer

*Zie [Azure Security Benchmark: Identiteitsbeheer](../security/benchmarks/security-controls-v2-identity-management.md) voor meer informatie.*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Azure Active Directory standaardiseren als het centrale identiteits- en verificatiesysteem

**Richtlijnen**: Azure Cost Management is geïntegreerd met Azure Active Directory (Azure AD), de service die standaard het identiteits- en toegangsbeheer verzorgt voor Azure. U moet standaardiseren in overeenstemming met Azure AD om het identiteits- en toegangsbeheer van uw organisatie te regelen in:

- Microsoft-cloudresources, zoals Azure Portal, Azure Storage, Azure Virtual Machines (Linux en Windows), Azure Key Vault, PaaS en SaaS-toepassingen.

- De resources van uw organisatie, zoals toepassingen in Azure of resources van uw bedrijfsnetwerk.

Het beveiligen van Azure AD moet een hoge prioriteit hebben bij het nemen van beveiligingsmaatregelen voor de cloud in uw organisatie. Azure AD biedt een id-beveiligingsscore om u te helpen uw postuur voor identiteitsbeveiliging te bepalen ten opzichte van de best practices die Microsoft aanbeveelt. Gebruik de score om te meten hoe goed uw configuratie aansluit bij de aanbevelingen en om verbeteringen door te voeren in uw beveiligingspostuur.

Opmerking: Azure AD biedt ondersteuning voor externe iD-providers, zodat gebruikers zonder Microsoft-account zich met hun externe identiteit kunnen aanmelden bij hun toepassingen en resources.

- [Tenancy in Azure AD](../active-directory/develop/single-and-multi-tenant-apps.md)

- [Een Azure AD-instantie maken en configureren](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Azure AD-tenants definiëren](https://azure.microsoft.com/resources/securing-azure-environments-with-azure-active-directory/)  

- [Externe id-providers gebruiken voor een toepassing](../active-directory/external-identities/identity-providers.md)

- [Wat is de id-beveiligingsscore in Azure AD?](../active-directory/fundamentals/identity-secure-score.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Eenmalige aanmelding van Azure AD gebruiken voor toegang tot toepassingen

**Richtlijnen**: Azure Cost Management gebruikt Azure Active Directory voor identiteits- en toegangsbeheer voor Azure-resources, cloudtoepassingen en on-premises toepassingen. Dit geldt niet alleen voor identiteiten zoals werknemers, maar ook voor externe identiteiten zoals partners en leveranciers. Zo kunt u eenmalige aanmelding (SSO) gebruiken voor het beheren en beveiligen van de gegevens en resources van uw organisatie, on premises en in de cloud. Verbind al uw gebruikers, toepassingen en apparaten met Azure AD voor naadloze, veilige toegang en meer zichtbaarheid en controle.

- [Inzicht in eenmalige aanmelding van toepassingen met Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Krachtige verificatiemechanismen gebruiken voor alle op Azure Active Directory gebaseerde toegang

**Richtlijnen**: Azure Cost Management is geïntegreerd met Azure AD, een service die ondersteuning biedt voor krachtige verificatiemechanismen via meervoudige verificatie (MFA) en krachtige methoden zonder wachtwoord.

- Meervoudige verificatie: Schakel meervoudige verificatie van Azure AD in en volg de aanbevelingen voor identiteits- en toegangsbeheer van Azure Security Center voor enkele best practices bij de configuratie van meervoudige verificatie. MFA kan worden afgedwongen voor alle gebruikers, bepaalde gebruikers of op het niveau van individuele gebruikers, op basis van voorwaarden voor aanmelding en op basis van risicofactoren.

- Verificatie zonder wachtwoord: Er zijn drie opties beschikbaar voor verificatie zonder wachtwoord: Windows Hello voor Bedrijven, de Microsoft Authenticator-app en on-premises verificatiemethoden zoals smartcards.

Het is belangrijk dat u voor beheerders en bevoegde gebruikers het hoogste niveau afdwingt van de krachtige verificatiemethode die wordt gebruikt. Voor andere gebruikers implementeert u het geschikte beleid voor sterke verificatie.

- [Meervoudige verificatie inschakelen in Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Algemene informatie over opties voor verificatie zonder wachtwoord voor Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md) 

- [Standaardwachtwoordbeleid voor Azure AD](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [Ongeschikte wachtwoorden voorkomen met Azure AD-wachtwoordbeveiliging](../active-directory/authentication/concept-password-ban-bad.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Controleren op accountafwijkingen en hiervoor waarschuwen

**Richtlijnen**: Azure Cost Management is geïntegreerd met Azure Active Directory in de volgende gegevensbronnen:

- Aanmeldingen: het rapport voor aanmeldingsactiviteit bevat informatie over het gebruik van beheerde toepassingen en aanmeldingsactiviteiten van gebruikers

- Auditlogboeken: traceerbaarheid via logboeken voor alle door diverse functies binnen Azure AD uitgevoerde wijzigingen. Voorbeelden van vermeldingen in auditlogboeken zijn wijzigingen die worden aangebracht in resources binnen Azure AD, zoals het toevoegen of verwijderen van gebruikers, apps, groepen, rollen en beleidsregels.

- Riskante aanmeldingen - Een riskante aanmelding is een indicator van een aanmeldingspoging die mogelijk is uitgevoerd door iemand die geen rechtmatige eigenaar van een gebruikersaccount is.

- Gebruikers voor wie wordt aangegeven dat ze risico lopen - Een riskante gebruiker is een indicator van een gebruikersaccount dat mogelijk is aangetast.

Deze gegevensbronnen kunnen worden geïntegreerd met Azure Monitor, Azure Sentinel of SIEM-systemen van derden.

Azure Security Center kan u ook waarschuwen voor bepaalde verdachte activiteiten, zoals een ongewoon aantal mislukte verificatiepogingen of afgeschafte accounts in het abonnement.

Azure Advanced Threat Protection (ATP) is een beveiligingsoplossing die aan de hand van signalen van Active Directory niet alleen geavanceerde bedreigingen kan identificeren, detecteren en onderzoeken, maar ook onderschepte identiteiten en schadelijke acties van binnenuit.

- [Auditrapporten over activiteiten in Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Riskante Azure AD-aanmeldingen weergeven](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure AD-gebruikers identificeren die zijn gemarkeerd voor riskante activiteiten](../active-directory/identity-protection/overview-identity-protection.md) 

- [Identiteits- en toegangsactiviteiten van gebruikers controleren in Azure Security Center](../security-center/security-center-identity-access.md) 

- [Waarschuwingen in de module Threat Intelligence Protection van Azure Security Center](../security-center/alerts-reference.md) 

- [Azure-activiteitenlogboeken integreren in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

## <a name="privileged-access"></a>Bevoegde toegang

*Zie [Azure Security Benchmark: uitgebreide toegang](../security/benchmarks/security-controls-v2-privileged-access.md) voor meer informatie.*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Gebruikers met hoge bevoegdheden beveiligen en beperken

**Richtlijnen**: Cost Management voor Enterprise Agreements (EA) heeft het hieronder vermelde account met hoge bevoegdheden.

- Zakelijke beheerder

Het wordt aangeraden om regelmatig de gebruikers te controleren die zijn toegewezen aan rollen binnen uw Enterprise Agreement (EA).

Daarnaast is het verstandig om altijd het aantal accounts of rollen met hoge bevoegdheden te beperken en deze accounts op een verhoogd niveau te beveiligen. Gebruikers met deze bevoegdheden hebben namelijk direct of indirect lees- en schrijftoegang tot alle resources in uw Azure-omgeving.

U kunt bevoegdheden JIT-toegang (Just-In-Time) verlenen voor Azure-resources en Azure AD met behulp van Azure AD Privileged Identity Management (PIM). JIT verleent gebruikers alleen tijdelijke machtigingen voor het uitvoeren van bevoegde taken op het moment dat ze deze nodig hebben. PIM kan ook beveiligingswaarschuwingen genereren wanneer er verdachte of onveilige activiteiten worden vastgesteld in uw Azure AD-organisatie.

- [Azure Enterprise-rollen beheren](manage/understand-ea-roles.md) 

- [Machtigingen voor beheerdersrollen in Azure AD](../active-directory/roles/permissions-reference.md) 

- [Beveiligingswaarschuwingen van Azure Privileged Identity Management gebruiken](../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md) 

- [Bevoegde toegang beveiligen voor hybride implementaties en cloudimplementaties in Azure AD](../active-directory/roles/security-planning.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Gebruikerstoegang regelmatig beoordelen en afstemmen

**Richtlijnen**: Azure Cost Management is afhankelijk van de juiste toegang voor het weergeven en beheren van de kostengegevens van de organisatie. De toegang wordt beheerd met Azure RBAC (op rollen gebaseerd toegangsbeheer) op abonnementsniveau en via beheerdersrollen met Enterprise Agreements (EA) of Microsoft Customer Agreement (MCA) voor het factureringsbereik. Het is belangrijk dat u de toegang regelmatig controleert en evalueert om er zeker van te zijn dat de gebruikers of groepen de vereiste toegang hebben.

- [Toegang tot factureringsgegevens beheren voor Azure](manage/manage-billing-access.md)

- [Toegang tot gegevens van Kostenbeheer toewijzen](costs/assign-access-acm-data.md)

- [Azure Enterprise-rollen beheren](manage/understand-ea-roles.md)

- [Informatie over beheerdersrollen voor Microsoft-klantovereenkomsten in Azure](manage/understand-mca-roles.md)

**Monitoring door Azure Security Center**: Ja

**Verantwoordelijkheid**: Klant

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Principe van minimale bevoegdheden hanteren 

**Richtlijnen**: Azure Cost Management is geïntegreerd met RBAC (op rollen gebaseerd toegangsbeheer) van Azure voor het beheren van resources (zoals budgetten, opgeslagen rapporten, enz.). Met Azure RBAC kunt u de toegang tot Azure-resources beheren via roltoewijzingen. U kunt rollen toewijzen aan gebruikers, groepen en service-principals. Er bestaan vooraf gedefinieerde, ingebouwde rollen voor bepaalde resources, en deze rollen kunnen worden geïnventariseerd of opgevraagd via tools zoals Azure CLI, Azure PowerShell en Azure Portal. De bevoegdheden die u via Azure RBAC toewijst aan resources, moeten altijd beperkt zijn tot wat vereist is voor de rollen. Dit is een aanvulling op de JIT-benadering van Azure AD Privileged Identity Management (PIM) en moet regelmatig worden geëvalueerd.

Gebruik ingebouwde rollen om machtigingen toe te wijzen en definieer alleen aangepaste rollen wanneer dit echt nodig is.

Azure Cost Management biedt de ingebouwde rollen Lezer en Inzender.

- [De rol Lezer van Azure Cost Management](../role-based-access-control/built-in-roles.md#cost-management-reader)

- [De rol Inzender van Azure Cost Management](../role-based-access-control/built-in-roles.md#cost-management-contributor)

Wat is op rollen gebaseerd toegangsbeheer van Azure 

- [RBAC configureren in Azure](../role-based-access-control/role-assignments-portal.md) 

- [Identiteits- en toegangsbeoordelingen van Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Benchmark: gegevensbescherming](../security/benchmarks/security-controls-v2-data-protection.md) voor meer informatie.*

### <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1: Gevoelige gegevens detecteren, classificeren en labelen

**Richtlijnen**: Het is raadzaam om uw gevoelige gegevens te detecteren, classificeren en labelen zodat u de juiste beveiligingsmaatregelen kunt ontwerpen om ervoor te zorgen dat gevoelige informatie wordt opgeslagen, verwerkt en veilig wordt verzonden door de technologiesystemen van de organisatie.

Gebruik Azure Information Protection (en de bijbehorende scantool) voor gevoelige informatie binnen Office-documenten in Azure, on-premises, in Office 365 en op andere locaties.

U kunt Azure SQL Information Protection gebruiken bij het classificeren en labelen van informatie die is opgeslagen in Azure SQL-databases.

- [Gevoelige gegevens labelen met Azure Information Protection](/azure/information-protection/what-is-information-protection) 

- [Azure SQL-gegevensdetectie implementeren](../azure-sql/database/data-discovery-and-classification-overview.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Gevoelige gegevens beveiligen

**Richtlijnen**: Het is raadzaam om gevoelige gegevens te beschermen door de toegang te beperken met behulp van toegangsbeheer op basis van rollen van Azure (Azure RBAC), toegangsbeheer op netwerkbasis en specifieke mechanismen in Azure-services (zoals versleuteling in SQL en andere databases).

Consistent toegangsbeheer is alleen mogelijk als alle typen toegangsbeheer zijn afgestemd op de segmentatiestrategie van uw bedrijf. De segmentatiestrategie voor uw bedrijf moet ook worden gebaseerd op de locatie van gevoelige of bedrijfskritieke gegevens en systemen.

Voor het onderliggende platform, dat wordt beheerd door Microsoft, geldt dat alle klantinhoud als gevoelig wordt beschouwd en dat gegevens worden beschermd tegen verlies en blootstelling. Om ervoor te zorgen dat klantgegevens veilig blijven binnen Azure, heeft Microsoft enkele standaardmaatregelen en -mechanismen voor gegevensbeveiliging geïmplementeerd.

- [Toegang tot gegevens van Kostenbeheer toewijzen](costs/assign-access-acm-data.md)

- [Toegangsbeheer op basis van rollen in Azure (RBAC)](../role-based-access-control/overview.md) 

- [Informatie over beveiliging van klantgegevens in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: Controleren op niet-geautoriseerde overdracht van gevoelige gegevens

**Richtlijnen**: Controleer op niet-geautoriseerde overdracht van gegevens naar locaties buiten de zichtbaarheid en controle van de onderneming. Het gaat hier dan meestal om het controleren op afwijkende activiteiten (grote of ongebruikelijke overdrachten) die kunnen wijzen op niet-geautoriseerde gegevensexfiltratie.

Azure Storage Advanced Threat Protection (ATP) en Azure SQL ATP kunnen een waarschuwing geven voor een afwijkende overdracht van gegevens die kan wijzen op een niet-geautoriseerde overdracht van gevoelige informatie.

Azure Information Protection (AIP) biedt controlevoorzieningen voor informatie die is geclassificeerd en gelabeld.

Als dit nodig is voor de naleving van preventie van gegevensverlies (DLP), kunt u een DLP-oplossing op een host gebruiken om detectie en/of preventieve controles af te dwingen om gegevensexfiltratie te voorkomen.

- [Azure SQL ATP inschakelen](../azure-sql/database/threat-detection-overview.md) 

- [Azure Storage ATP inschakelen](../storage/common/azure-defender-storage-configure.md?tabs=azure-security-center)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: Gevoelige data-at-rest versleutelen

**Richtlijnen**: Als aanvulling op de voorzieningen voor toegangsbeheer, ondersteunt de exportfunctie van Azure Cost Management versleuteling van data-at-rest in Azure Storage om deze te beschermen tegen 'out of band' aanvallen (zoals toegang tot onderliggende opslag) met behulp van door Microsoft beheerde versleuteling van sleutels. Deze standaardinstellingen zorgen ervoor dat aanvallers de gegevens niet eenvoudig kunnen lezen of wijzigen.

Zie voor meer informatie:

- [Azure Storage-versleuteling voor inactieve gegevens](../storage/common/storage-service-encryption.md)

- [Meer informatie over versleuteling van data-at-rest in Azure](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="asset-management"></a>Asset-management

*Zie [Azure Security Benchmark: assetmanagement](../security/benchmarks/security-controls-v2-asset-management.md) voor meer informatie.*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Beveiligingsteam inzicht geven in risico's voor assets

**Richtlijnen**: Zorg ervoor dat beveiligingsteams de machtiging Beveiligingslezer hebben in uw Azure-tenant en -abonnementen zodat ze kunnen controleren op beveiligingsrisico's met behulp van Azure Security Center. 

Afhankelijk van de manier waarop de verantwoordelijkheden van het beveiligingsteam zijn gestructureerd, kan de controle op beveiligingsrisico's de verantwoordelijkheid zijn van een centraal beveiligingsteam of een lokaal team. Om die reden moeten beveiligingsinzichten en -risico's altijd centraal worden verzameld in een organisatie. 

De machtiging Beveiligingslezer kan breed worden toegepast op een hele tenant (hoofdbeheergroep) of in het bereik van beheergroepen of specifieke abonnementen. 

Opmerking: Er zijn mogelijk extra machtigingen vereist om inzicht te krijgen in workloads en services. 

- [Overzicht van rol Beveiligingslezer](../role-based-access-control/built-in-roles.md#security-reader)

- [Overzicht van Azure-beheergroepen](../governance/management-groups/overview.md)

**Monitoring door Azure Security Center**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Beveiliging van levenscyclusbeheer van assets garanderen

**Richtlijnen**: Er moet beveiligingsbeleid worden ingesteld of bijgewerkt om te controleren op aanpassingen met mogelijk grote gevolgen van processen voor levenscyclusbeheer. Het betreft hier bijvoorbeeld aanpassingen van: id-providers en toegang, gegevensgevoeligheid, netwerkconfiguratie en toewijzing van beheerdersbevoegdheden.

Verwijder Azure-resources wanneer deze ze niet meer nodig zijn.

- [Azure-resourcegroep en -resource verwijderen](../azure-resource-manager/management/delete-resource-group.md)

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="logging-and-threat-detection"></a>Logboekregistratie en detectie van bedreigingen

*Zie [Azure Security Benchmark: logboekregistratie en detectie van bedreigingen](../security/benchmarks/security-controls-v2-logging-threat-detection.md) voor meer informatie.*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Bedreigingsdetectie inschakelen voor identiteits- en toegangsbeheer van Azure

**Richtlijnen**: Azure AD biedt de volgende gebruikerslogboeken die kunnen worden weergegeven in Azure AD Reporting of worden geïntegreerd met Azure Monitor, Azure Sentinel of andere tools voor SIEM/controle voor meer geavanceerde scenario's voor controle en analyse:

- Aanmeldingen: het rapport voor aanmeldingsactiviteit bevat informatie over het gebruik van beheerde toepassingen en aanmeldingsactiviteiten van gebruikers

- Auditlogboeken: traceerbaarheid via logboeken voor alle door diverse functies binnen Azure AD uitgevoerde wijzigingen. Voorbeelden van vermeldingen in auditlogboeken zijn wijzigingen die worden aangebracht in resources binnen Azure AD, zoals het toevoegen of verwijderen van gebruikers, apps, groepen, rollen en beleidsregels.

- Riskante aanmeldingen - Een riskante aanmelding is een indicator van een aanmeldingspoging die mogelijk is uitgevoerd door iemand die geen rechtmatige eigenaar van een gebruikersaccount is.

- Gebruikers voor wie wordt aangegeven dat ze risico lopen - Een riskante gebruiker is een indicator van een gebruikersaccount dat mogelijk is aangetast.

Azure Security Center kan u ook waarschuwen voor bepaalde verdachte activiteiten, zoals een ongewoon aantal mislukte verificatiepogingen of afgeschafte accounts in het abonnement. Naast de basiscontrole op beveiligingshygiëne, kan de module voor bedreigingsbeveiliging van Azure Security Center ook uitgebreidere beveiligingswaarschuwingen verzamelen van afzonderlijke Azure-compute-resources (virtuele machines, containers, app service), gegevensbronnen (SQL DB en opslag) en Azure-servicelagen. Met deze functie kunt u inzicht krijgen in accountafwijkingen binnen de afzonderlijke resources.

Het wordt ook aangeraden om regelmatig de gebruikers te controleren die zijn toegewezen aan rollen binnen uw Enterprise Agreement (EA). 

- [Azure Enterprise-rollen beheren](manage/understand-ea-roles.md)

- [Auditrapporten over activiteiten in Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Azure AD Identity Protection inschakelen](../active-directory/identity-protection/overview-identity-protection.md)

- [Bescherming tegen bedreiging in Azure Security Center](../security-center/azure-defender.md)

**Monitoring door Azure Security Center**: Niet van toepassing

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

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Detectie en analyse - incidenten maken op basis van waarschuwingen van hoge kwaliteit

**Richtlijnen**: Zorg ervoor dat u een proces hebt om waarschuwingen van hoge kwaliteit te maken en de kwaliteit van waarschuwingen te meten. Zo kunt u leren van eerdere incidenten en prioriteit toekennen aan waarschuwingen voor analisten, zodat ze geen tijd verspillen aan fout-positieven. 

Waarschuwingen van hoge kwaliteit kunnen worden samengesteld op basis van ervaringen met eerdere incidenten, gevalideerde communitybronnen, en tools die zijn ontworpen om waarschuwingen te genereren en op te schonen door verschillende signaalbronnen samen te voegen en te correleren. 

Azure Security Center biedt waarschuwingen van hoge kwaliteit voor diverse Azure-assets. U kunt de ASC-dataconnector gebruiken om de waarschuwingen te streamen naar Azure Sentinel. Met Azure Sentinel kunt u geavanceerde waarschuwingsregels opstellen om automatisch incidenten te genereren voor onderzoek. 

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

Azure Security Center wijst aan elke waarschuwing een ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op hoeveel vertrouwen Security Center heeft in de zoekactie of de analysefunctie die is gebruikt om de waarschuwing te genereren, evenals in welke mate kan worden aangetoond dat er sprake is van schadelijke opzet achter de activiteit die tot de waarschuwing heeft geleid.

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

*Ga voor meer informatie naar [Azure Security-benchmark: Beheer van beveiligingspostuur en beveiligingsproblemen](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md).*

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>PV-7: Beveiligingsproblemen in software snel en automatisch oplossen

**Richtlijnen**: Implementeer snel software-updates om kwetsbaarheden in besturingssystemen en toepassingen op te lossen.

Bepaal de prioriteit aan de hand van een gangbaar programma voor risicobeoordeling (zoals het Common Vulnerability Scoring System) of de standaardrisicoclassificaties van uw externe scantool en pas deze aan uw omgeving aan met behulp van de context van de toepassingen die een hoog beveiligingsrisico vormen en de toepassingen die permanent beschikbaar moeten zijn.

**Monitoring door Azure Security Center**: Niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Voer regelmatige simulaties van aanvallen uit

**Richtlijnen**: Voer zo vaak u als u wilt penetratietests of Red Teaming-activiteiten uit op uw Azure-resources, en zorg ervoor dat alle kritieke beveiligingsbevindingen worden opgelost.
Ga te werk volgens de Microsoft Cloud Penetration Testing Rules of Engagement (Regels voor het inzetten van penetratietests voor Microsoft Cloud ) zodat u zeker weet dat uw penetratietests niet conflicteren met Microsoft-beleid. Gebruik de strategie van Microsoft en de uitvoering van Red Teaming-activiteiten, en voer een penetratietest van de live site uit op basis van een infrastructuur, services en toepassingen die door Microsoft worden beheerd.

- [Penetratietesten in Azure](../security/fundamentals/pen-testing.md)

- [Regels voor het inzetten van penetratietests](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center-bewaking**: Niet van toepassing

**Verantwoordelijkheid**: Gedeeld

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

-   Gebruik van voorzieningen voor gegevensbescherming van Azure en derden

-   Vereisten voor gegevensversleutelings van gegevens in-transit en at-rest

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