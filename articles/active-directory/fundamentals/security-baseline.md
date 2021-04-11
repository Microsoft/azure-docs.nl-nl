---
title: Azure-beveiligings basislijn voor Azure Active Directory
description: De Azure Active Directory Security Baseline voorziet in procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-Bench Mark.
author: msmbaldwin
ms.service: active-directory
ms.topic: conceptual
ms.date: 04/07/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 8d4203b7b5d7742bf198778864fa4f42b7423596
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107286014"
---
# <a name="azure-security-baseline-for-azure-active-directory"></a>Azure-beveiligings basislijn voor Azure Active Directory

In deze beveiligings basislijn worden richt lijnen van de [Azure Security Bench Mark-versie 2,0](../../security/benchmarks/overview.md) ingesteld op Azure Active Directory. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen. De inhoud wordt gegroepeerd op de **beveiligings controles** die zijn gedefinieerd door de Azure Security-benchmark en de bijbehorende richt lijnen die van toepassing zijn op Azure Active Directory. 

> [!NOTE]
> **Besturings elementen** die niet van toepassing zijn op Azure Active Directory of waarvoor de verantwoordelijkheid van micro soft is, zijn uitgesloten van deze basis lijn. Als u wilt zien hoe Azure Active Directory volledig is toegewezen aan de beveiligings benchmark van Azure, raadpleegt u het [volledige Azure Active Directory beveiligings basislijn toewijzings bestand](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Benchmark: netwerkbeveiliging](/azure/security/benchmarks/security-controls-v2-network-security) voor meer informatie.*

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: netwerk beveiligings regels vereenvoudigen

**Hulp**: Azure Virtual Network Service-Tags gebruiken om netwerk toegangs beheer te definiëren voor netwerk beveiligings groepen of Azure firewall geconfigureerd voor uw Azure Active Directory-resources. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de service label op te geven, zoals ' AzureActiveDirectory ' in het juiste bron-of doel veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen. 

- [Service Tags begrijpen en gebruiken](../../virtual-network/service-tags-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="identity-management"></a>Identiteitsbeheer

*Zie [Azure Security Benchmark: Identiteitsbeheer](/azure/security/benchmarks/security-controls-v2-identity-management) voor meer informatie.*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Azure Active Directory standaardiseren als het centrale identiteits--en verificatiesysteem

**Hulp**: gebruik Azure Active Directory (Azure AD) als de standaard service voor identiteits-en toegangs beheer. U moet Azure AD standaardiseren om het identiteits-en toegangs beheer van uw organisatie in te bepalen: 
- Microsoft Cloud resources, zoals de Azure Portal, Azure Storage, virtuele machine van Azure (Linux en Windows), Azure Key Vault, PaaS en SaaS-toepassingen. 
- De resources van uw organisatie, zoals toepassingen in Azure of resources van uw bedrijfsnetwerk. 
 

Het beveiligen van Azure AD moet een hoge prioriteit hebben in de cloudbeveiligingsprocedure van uw organisatie. Azure AD biedt een id-beveiligingsscore om u te helpen beoordelen in hoeverre uw identiteitsbeveiliging voldoet aan de aanbevelingen op basis van best practices van Microsoft. Gebruik de score om te meten hoe nauwkeurig uw configuratie overeenkomt met aanbevelingen op basis van best practices, en om verbeteringen aan te brengen in uw beveiligingsaanpak. 

Azure AD biedt ondersteuning voor externe identiteit waarmee gebruikers zonder Microsoft-account zich kunnen aanmelden bij hun toepassingen en resources met hun externe identiteit. 

- [Tenancy in Azure Active Directory](../develop/single-and-multi-tenant-apps.md)

- [Een Azure AD-instantie maken en configureren](active-directory-access-create-new-tenant.md)

- [Externe ID-providers voor een toepassing gebruiken](/azure/active-directory/b2b/identity-providers)

- [Wat is de id-beveiligingsscore in Azure Active Directory?](identity-secure-score.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Toepassingsidentiteiten veilig en automatisch beheren

**Richt lijnen**: gebruik beheerde identiteit voor Azure-resources voor niet-menselijke accounts, zoals services of Automation, het wordt aanbevolen Azure-Managed Identity-functie te gebruiken in plaats van een krachtig menselijk account te maken om uw resources te openen of uit te voeren. U kunt systeem eigen verificatie uitvoeren voor Azure-Services/-bronnen die ondersteuning bieden voor Azure Active Directory (Azure AD) met behulp van vooraf gedefinieerde toegangs toekennings regels zonder dat er referenties worden vastgelegd in de bron code of configuratie bestanden. U kunt geen door Azure beheerde identiteiten toewijzen aan Azure AD-resources, maar Azure AD is het mechanisme voor verificatie met beheerde identiteiten die zijn toegewezen aan bronnen van andere services.

- [Beheerde identiteit voor Azure-resources](../managed-identities-azure-resources/overview.md)

- [Services die beheerde identiteit voor Azure-resources ondersteunen](../managed-identities-azure-resources/services-support-managed-identities.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Eenmalige aanmelding (SSO) van Azure AD gebruiken voor toegang tot toepassingen

**Hulp**: gebruik Azure Active Directory om identiteits-en toegangs beheer te bieden aan Azure-resources, Cloud toepassingen en on-premises toepassingen. Dit omvat ondernemingsidentiteiten zoals werknemers, maar ook externe identiteiten, zoals partners, verkopers en leveranciers. Zo kunt u eenmalige aanmelding (SSO) gebruiken voor het beheren en beveiligen van de gegevens en resources van uw organisatie, on premises en in de cloud. Verbind al uw gebruikers, toepassingen en apparaten met Azure AD voor naadloze, veilige toegang en meer zichtbaarheid en controle.

 
- [Inzicht in eenmalige aanmelding van toepassingen met Azure AD](../manage-apps/what-is-single-sign-on.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Gebruik krachtige besturingselementen voor verificatie voor alle op Azure Active Directory gebaseerde toegang

**Hulp**: gebruik Azure Active Directory ter ondersteuning van krachtige verificatie-instellingen via multi-factor Authentication (MFA) en krachtige methoden met een wacht woord.
- Meervoudige verificatie: schakel MFA in voor Azure AD en volg de aanbevelingen op van Azure Security Center met betrekking tot identiteits- en toegangsbeheer die gelden als best practices voor het instellen van uw MFA. MFA kan worden afgedwongen voor alle gebruikers, bepaalde gebruikers of op het niveau van individuele gebruikers, op basis van voorwaarden voor aanmelding en op basis van risicofactoren.
- Verificatie met wacht woord-drie verificatie opties met een wacht woord zijn beschikbaar: Windows hello voor bedrijven, Microsoft Authenticator-app en on-premises verificatie methoden zoals Smart Cards.

Voor beheerders en bevoegde gebruikers zorgt u ervoor dat het hoogste niveau van de methode voor sterke verificatie wordt gebruikt, gevolgd door het juiste beleid voor sterke authenticatie voor andere gebruikers te implementeren.

Azure AD biedt ondersteuning voor verouderde verificatie op basis van wacht woorden, zoals alleen-Cloud accounts (gebruikers accounts die rechtstreeks in azure AD zijn gemaakt) die een basislijn wachtwoord beleid of Hybrid accounts (gebruikers accounts die afkomstig zijn van on-premises Active Directory) die de on-premises wachtwoord beleid volgen. Wanneer u gebruikmaakt van verificatie op basis van wacht woorden, biedt Azure AD een functie voor wachtwoord beveiliging waarmee wordt voor komen dat gebruikers wacht woorden kunnen instellen die gemakkelijk te raden zijn. Micro soft biedt een algemene lijst met verboden wacht woorden die worden bijgewerkt op basis van de telemetrie, en klanten kunnen de lijst uitbreiden op basis van hun behoeften (bijvoorbeeld branding, culturele verwijzingen, enzovoort). Deze wachtwoord beveiliging kan worden gebruikt voor Cloud-en hybride accounts.

Verificatie op basis van wachtwoord referenties is alleen vatbaar voor populaire aanvals methoden. Voor een betere beveiliging gebruikt u sterke verificatie zoals MFA en een sterk wachtwoord beleid. Voor toepassingen van derden en Marketplace-services die standaard wachtwoorden kunnen hebben, moet u deze wijzigen bij de eerste installatie van de service.

 
- [Azure AD MFA implementeren](../authentication/howto-mfa-getstarted.md) 

 

 
- [Algemene informatie over opties voor verificatie zonder wachtwoord voor Azure Active Directory](../authentication/concept-authentication-passwordless.md) 

 

 
- [Standaardwachtwoordbeleid voor Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#password-policies-that-only-apply-to-cloud-user-accounts)

- [Ongeschikte wachtwoorden voorkomen met Azure AD-wachtwoordbeveiliging](../authentication/concept-password-ban-bad.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Bewaken van accounts op afwijkingen en waarschuwingenbeheer

**Hulp**: Azure Active Directory biedt de volgende gegevens bronnen:

 
- Aanmeldingen: het rapport voor aanmeldingsactiviteit bevat informatie over het gebruik van beheerde toepassingen en aanmeldingsactiviteiten van gebruikers

 
- Auditlogboeken: traceerbaarheid via logboeken voor alle door diverse functies binnen Azure AD uitgevoerde wijzigingen. Voorbeelden van vermeldingen in auditlogboeken zijn wijzigingen die worden aangebracht in resources binnen Azure AD, zoals het toevoegen of verwijderen van gebruikers, apps, groepen, rollen en beleidsregels.

 
- Riskante aanmeldingen - Een riskante aanmelding is een indicator van een aanmeldingspoging die mogelijk is uitgevoerd door iemand die geen rechtmatige eigenaar van een gebruikersaccount is.

 
- Gebruikers voor wie wordt aangegeven dat ze risico lopen - Een riskante gebruiker is een indicator van een gebruikersaccount dat mogelijk is aangetast.

 

Deze gegevens bronnen kunnen worden geïntegreerd met Azure Monitor, Azure Sentinel of SIEM-systemen van derden.

 

 
Azure Security Center kan u ook waarschuwen voor bepaalde verdachte activiteiten, zoals een ongewoon aantal mislukte verificatiepogingen of afgeschafte accounts in het abonnement.

 

 
Azure Advanced Threat Protection (ATP) is een beveiligingsoplossing die aan de hand van signalen van Active Directory niet alleen geavanceerde bedreigingen kan identificeren, detecteren en onderzoeken, maar ook onderschepte identiteiten en schadelijke acties van binnenuit.

 

 
- [Auditrapporten over activiteiten in Azure Active Directory](../reports-monitoring/concept-audit-logs.md) 

 

 
- [Riskante Azure AD-aanmeldingen weergeven](/azure/active-directory/reports-monitoring/concept-risky-sign-ins) 

 

 
- [Azure AD-gebruikers identificeren die zijn gemarkeerd voor riskante activiteiten](/azure/active-directory/reports-monitoring/concept-user-at-risk) 

 

 
- [Identiteits- en toegangsactiviteiten van gebruikers controleren in Azure Security Center](../../security-center/security-center-identity-access.md) 

 

 
- [Waarschuwingen in de module Threat Intelligence Protection van Azure Security Center](../../security-center/alerts-reference.md) 

 

 
- [Azure-activiteitenlogboeken integreren in Azure Monitor](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Toegang tot Azure-resource beperken op basis van voorwaarden

**Hulp**: gebruik Azure Active Directory (Azure AD) voorwaardelijke toegang voor een meer gedetailleerd toegangs beheer op basis van door de gebruiker gedefinieerde voor waarden, zoals gebruikers aanmeldingen vanuit bepaalde IP-bereiken, moet MFA gebruiken voor aanmelding. Gedetailleerd beheerbeleid voor verificatiesessies kan ook worden gebruikt voor verschillende scenario's.

 
- [Overzicht van voorwaardelijke toegang voor Azure AD](../conditional-access/overview.md) 

 

 
- [Algemeen beleid voor voorwaardelijke toegang](../conditional-access/concept-conditional-access-policy-common.md) 

 

 
- [Beheer van verificatiesessies met voorwaardelijke toegang configureren](../conditional-access/howto-conditional-access-session-lifetime.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="im-8-secure-user-access-to-legacy-applications"></a>IM-8: beveiligde gebruikers toegang tot verouderde toepassingen

**Richt lijnen**: Zorg ervoor dat u beschikt over moderne toegangs beheer en sessie bewaking voor oudere toepassingen en de gegevens die ze opslaan en verwerken. Hoewel Vpn's vaak worden gebruikt voor toegang tot oudere toepassingen, hebben ze vaak alleen elementaire toegangs beheer en beperkte sessie bewaking.

 
Met Azure AD-toepassingsproxy kunt u oude on-premises toepassingen publiceren naar externe gebruikers met SSO terwijl u de betrouw baarheid van zowel externe gebruikers als apparaten expliciet valideert met voorwaardelijke toegang van Azure AD.

 

 
Microsoft Cloud App Security is ook een CASB-service (Cloud Access Security Broker) die besturings elementen kan bieden voor het bewaken van toepassings sessies van gebruikers en het blok keren van acties (voor zowel oudere on-premises toepassingen als voor cloud software als een service (SaaS)-toepassingen).

 

 
- [Azure Active Directory-toepassingsproxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy#what-is-application-proxy) 

 

 
- [Aanbevolen procedures Microsoft Cloud App Security](/cloud-app-security/best-practices)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="privileged-access"></a>Uitgebreide toegang

*Zie [Azure Security Benchmark: uitgebreide toegang](/azure/security/benchmarks/security-controls-v2-privileged-access) voor meer informatie.*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Gebruikers met zeer uitgebreide bevoegdheden beveiligen en beperken

**Richt lijnen**: de belangrijkste ingebouwde rollen zijn Azure AD zijn globale beheerder en de beheerder van de geprivilegieerde rol, omdat gebruikers die aan deze twee rollen zijn toegewezen, beheerders rollen kunnen delegeren:

- Globale beheerder: gebruikers met deze rol hebben toegang tot alle beheer functies in azure AD, evenals services die gebruikmaken van Azure AD-identiteiten.

- Beheerder van geprivilegieerde rol: gebruikers met deze rol kunnen roltoewijzingen beheren in azure AD, en in Azure AD Privileged Identity Management (PIM). Daarnaast kunt u met deze rol alle aspecten van PIM en administratieve eenheden beheren.

Mogelijk hebt u andere essentiële rollen die u moet onderhevig hebben als u aangepaste rollen gebruikt waaraan bepaalde privileged permissions zijn toegewezen. Het is ook mogelijk dat u vergelijk bare besturings elementen wilt Toep assen op het beheerders account van essentiële bedrijfs activa.

Azure AD heeft accounts met zeer recht: de gebruikers en service-principals die direct of indirect worden toegewezen aan of in aanmerking komen voor, de Administrator rollen van de globale beheerder of bevoorrechte rol, en andere uiterst privilegede rollen in azure AD en Azure.

Beperk het aantal accounts met zeer privileges en beveilig deze accounts op een verhoogd niveau, omdat gebruikers met deze bevoegdheid elke resource in uw Azure-omgeving direct of indirect kunnen lezen en wijzigen.

U kunt bevoegdheden JIT-toegang (Just-In-Time) verlenen voor Azure-resources en Azure AD met behulp van Azure AD Privileged Identity Management (PIM). JIT verleent gebruikers alleen tijdelijke machtigingen voor het uitvoeren van bevoegde taken op het moment dat ze deze nodig hebben. PIM kan ook beveiligingswaarschuwingen genereren wanneer er verdachte of onveilige activiteiten worden vastgesteld in uw Azure AD-organisatie.

- [Machtigingen voor beheerdersrollen in Azure AD](/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 

- [Beveiligingswaarschuwingen van Azure Privileged Identity Management gebruiken](../privileged-identity-management/pim-how-to-configure-security-alerts.md) 

- [Bevoegde toegang beveiligen voor hybride implementaties en cloudimplementaties in Azure AD](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Beheerderstoegang tot essentiële bedrijfssystemen beperken

**Hulp**: gebruik Azure Active Directory privileged Identity Management en multi-factor Authentication om beheerders toegang tot essentiële bedrijfs systemen te beperken.

- [Goed keuring van aanvragen voor activering van rollen Privileged Identity Management](../privileged-identity-management/azure-ad-pim-approval-workflow.md)

- [Multi-factor Authentication en voorwaardelijke toegang](../conditional-access/howto-conditional-access-policy-admin-mfa.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Gebruikerstoegang regelmatig controleren en afstemmen

**Richt lijnen**: toegangs toewijzingen voor gebruikers accounts regel matig controleren om ervoor te zorgen dat de accounts en hun toegang geldig zijn, met name op de rollen met zeer uitgebreide bevoegdheden, waaronder globale beheerder en beheerder met verhoogde rol. U kunt de toegangs beoordelingen van Azure Active Directory (Azure AD) gebruiken voor het controleren van groepslid maatschappen, de toegang tot bedrijfs toepassingen en roltoewijzingen, zowel voor Azure AD-rollen als Azure-rollen. Azure AD Reporting kan logboeken bieden waarmee u verlopen accounts kunt detecteren. U kunt ook Azure AD Privileged Identity Management gebruiken om een werk stroom voor het lezen van rapporten te maken om het controle proces te vergemakkelijken.

Daarnaast kan Azure Privileged Identity Management ook worden geconfigureerd om te worden gewaarschuwd wanneer er een uitzonderlijk groot aantal beheerders accounts wordt gemaakt en om beheerders accounts te identificeren die verouderd of onjuist zijn geconfigureerd.

- [Een toegangs beoordeling maken van Azure AD-rollen in Privileged Identity Management (PIM)](../privileged-identity-management/pim-how-to-start-security-review.md)

- [Een toegangs beoordeling van Azure-resource rollen maken in Privileged Identity Management (PIM)](../privileged-identity-management/pim-resource-roles-start-access-review.md)

- [Identiteits- en toegangsbeoordelingen van Azure AD](../governance/access-reviews-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Noodtoegang instellen in Azure AD

**Richt lijnen**: als u wilt voor komen dat uw Azure AD-organisatie per ongeluk wordt vergrendeld, stelt u een toegangs account voor nood gevallen in voor toegang wanneer normale beheerders accounts niet kunnen worden gebruikt. Accounts voor noodtoegang hebben meestal zeer uitgebreide bevoegdheden en kunnen beter niet aan specifieke personen worden toegewezen.
Accounts voor noodtoegang zijn alleen bestemd voor echte noodsituaties waarin normale beheerdersaccounts niet kunnen worden gebruikt.

Zorg ervoor dat de referenties (zoals wachtwoord, certificaat of smartcard) voor accounts voor noodtoegang veilig worden bewaard en alleen bekend zijn bij personen die deze alleen in een noodgeval mogen gebruiken.

- [Accounts voor noodtoegang beheren in Azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="pa-5-automate-entitlement-management"></a>PA-5: beheer van rechten automatiseren 

**Richt lijnen**: gebruik Azure AD-functies voor rechten beheer om werk stromen voor toegangs aanvragen te automatiseren, met inbegrip van toegangs toewijzingen, beoordelingen en verval datums. Het is ook mogelijk om twee of meerdere fasen goed te keuren.

- [Wat is het beheer van rechten van Azure AD](../governance/entitlement-management-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Werkstations met uitgebreide toegang gebruiken

**Richtlijnen**: Beveiligde, geïsoleerde werkstations zijn van cruciaal belang voor de beveiliging van gevoelige rollen als beheerders, ontwikkelaars en serviceoperators met vergaande bevoegdheden. Gebruik zeer beveiligde werk stations van gebruikers en/of Azure Bastion voor beheer taken. Gebruik Azure Active Directory, Microsoft Defender Advanced Threat Protection (ATP) en/of Microsoft Intune als u een beveiligd en beheerd gebruikerswerkstation voor beheertaken wilt implementeren. De beveiligde werkstations kunnen centraal worden beheerd en beveiligde configuraties afdwingen, waaronder krachtige verificatie, software- en hardwarebasislijnen, beperkte logische toegang en netwerktoegang.

- [Apparaten beveiligen als onderdeel van bevoegde toegang](/security/compass/privileged-access-devices)

- [Implementatie van bevoegde toegang](/security/compass/privileged-access-deployment)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Principe van minimale bevoegdheden hanteren 

**Hulp**: klanten kunnen hun Azure Active Directory-implementatie (Azure AD) configureren voor de minimale bevoegdheden door gebruikers toe te wijzen aan de rollen met de minimale machtigingen die gebruikers nodig hebben om hun beheer taken uit te voeren.

- [Machtigingen voor beheerdersrollen in Azure AD](../roles/permissions-reference.md)

- [Beheerders rollen toewijzen in azure AD](../roles/manage-roles-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: goedkeurings proces kiezen voor micro soft-ondersteuning  

**Hulp**: Azure Active Directory biedt geen ondersteuning voor klant-lockbox. Micro soft kan met klanten werken via niet-lockbox-methoden voor het goed keuren van toegang tot klant gegevens.

**Verantwoordelijkheid**: Gedeeld

**Azure Security Center bewaking**: geen

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Benchmark: gegevensbescherming](/azure/security/benchmarks/security-controls-v2-data-protection) voor meer informatie.*

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Gevoelige gegevens beschermen

**Richt lijnen**: Houd rekening met de volgende richt lijnen voor het implementeren van de beveiliging van uw gevoelige gegevens:

- **Isolatie:** Een directory is de gegevens grens waarmee de Azure Active Directory (Azure AD)-Services gegevens voor een klant opslaat en verwerkt. Klanten moeten bepalen waar ze de meeste klant gegevens van Azure AD willen, door de land eigenschap in hun Directory in te stellen.

- **Segmentatie:** De rol van de globale beheerder heeft volledige controle over alle Directory gegevens en de regels die de toegangs-en verwerkings instructies regelen. Een directory kan worden gesegmenteerd in administratieve eenheden en is ingericht met gebruikers en groepen die moeten worden beheerd door beheerders van deze eenheden. globale beheerders kunnen verantwoordelijk zijn voor het delegeren van verantwoordelijkheden aan andere principes in hun organisatie door ze toe te wijzen aan vooraf gedefinieerde rollen of aangepaste rollen die ze kunnen maken.

 
- **Toegang:** Machtigingen kunnen worden toegepast op een gebruiker, groep, rol, toepassing of apparaat. De toewijzing is mogelijk permanent of tijdelijk per Privileged Identity Management configuratie. 
  
- **Versleuteling:** Azure AD versleutelt alle gegevens in rust of in door voer. Met de aanbieding kunnen klanten geen Directory gegevens met hun eigen versleutelings sleutel versleutelen. 

Als u wilt weten hoe hun geselecteerde land wordt toegewezen aan de fysieke locatie van de Directory, raadpleegt u het artikel ' waar bevindt zich uw gegevens '.

- [Waar uw gegevens zich bevinden. artikel](https://www.microsoft.com/trust-center/privacy/data-location)

Wanneer de klant gebruikmaakt van verschillende Azure AD-hulpprogram ma's,-functies en-toepassingen die communiceren met hun Directory, gebruikt u het artikel Azure Active Directory – waar bevinden uw gegevens zich?

- [Waar bevindt uw dash board uw gegevens](https://msit.powerbi.com/view?r=eyJrIjoiYzEyZTc5OTgtNTdlZS00ZTVkLWExN2ItOTM0OWU4NjljOGVjIiwidCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsImMiOjV9)

- [Documentatie voor Azure AD-rollen](/azure/active-directory/roles/)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Gevoelige gegevens tijdens een overdracht versleutelen

**Richt lijnen**: als aanvulling op de toegangs controle, moeten gegevens in transit worden beschermd tegen buiten-band-aanvallen (zoals het vastleggen van verkeer) met behulp van versleuteling om ervoor te zorgen dat aanvallers de gegevens niet eenvoudig kunnen lezen of wijzigen.

Azure AD biedt ondersteuning voor gegevens versleuteling tijdens de overdracht met TLS v 1.2 of hoger.

Hoewel dit optioneel is voor verkeer op particuliere netwerken, is dit van cruciaal belang voor verkeer op externe en open bare netwerken. Voor HTTP-verkeer moet u ervoor zorgen dat clients die verbinding maken met uw Azure-resources, TLS v 1.2 of hoger kunnen onderhandelen. Voor extern beheer gebruikt u SSH (voor Linux) of RDP/TLS (voor Windows) in plaats van een niet-versleuteld protocol. Verouderde SSL-, TLS-en SSH-versies en-protocollen en zwakke cijfers moeten worden uitgeschakeld.

Azure biedt standaard versleuteling voor gegevens in transit tussen Azure-data centers.

- [Meer informatie over versleuteling in transit met Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit) 

- [Informatie over TLS-beveiliging](/security/engineering/solving-tls1-problem) 

- [Dubbele versleuteling voor Azure-gegevens in transit](https://docs.microsoft.com/azure/security/fundamentals/double-encryption#data-in-transit)

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

- [Overzicht van Azure-beheergroepen](../../governance/management-groups/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

**Hulp**: gebruik Azure Active Directory (Azure AD) voorwaardelijke toegang om de mogelijkheid van gebruikers om via de Azure portal te communiceren met Azure ad te beperken door ' toegang blok keren ' te configureren voor de App ' Microsoft Azure beheer '.

- [Voorwaardelijke toegang configureren om de toegang tot Azure-bronnen beheer te blok keren](../../role-based-access-control/conditional-access-azure-management.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="logging-and-threat-detection"></a>Logboekregistratie en detectie van bedreigingen

*Zie [Azure Security Benchmark: logboekregistratie en detectie van bedreigingen](/azure/security/benchmarks/security-controls-v2-logging-threat-protection) voor meer informatie.*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: detectie van bedreigingen inschakelen voor Azure-resources

**Hulp**: gebruik de ingebouwde functie voor detectie van bedreigingen voor identiteits beveiliging van Azure Active Directory (Azure AD) voor uw Azure AD-resources. 
 
 
 

 
Azure AD produceert activiteiten logboeken die vaak worden gebruikt voor detectie van bedreigingen en bedreigingen. Logboeken van Azure AD-aanmelding bieden een record van verificatie-en autorisatie activiteiten voor gebruikers, services en apps. In azure AD-controle logboeken worden wijzigingen bijgehouden die zijn aangebracht in een Azure AD-Tenant, met inbegrip van wijzigingen die de beveiligings postuur verbeteren of afnemen.

- [Wat is Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md)

- [Azure AD Identity Protection gegevens verbinden met Azure Sentinel](../../sentinel/connect-azure-ad-identity-protection.md)

- [Azure Active Directory gegevens verbinden met Azure Sentinel](../../sentinel/connect-azure-active-directory.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Detectie van bedreigingen inschakelen voor Azure identiteits- en toegangsbeheer

**Hulp**: Azure Active Directory (Azure AD) biedt de volgende gebruikers logboeken die kunnen worden weer gegeven in azure AD Reporting of geïntegreerd met Azure monitor, Azure Sentinel of andere hulpprogram MA'S voor Siem/bewaking voor geavanceerde bewakings-en analyse-gebruiks voorbeelden: 
- Aanmeldingen: het rapport voor aanmeldingsactiviteit bevat informatie over het gebruik van beheerde toepassingen en aanmeldingsactiviteiten van gebruikers 
- Auditlogboeken: traceerbaarheid via logboeken voor alle door diverse functies binnen Azure AD uitgevoerde wijzigingen. Voorbeelden van vermeldingen in auditlogboeken zijn wijzigingen die worden aangebracht in resources binnen Azure AD, zoals het toevoegen of verwijderen van gebruikers, apps, groepen, rollen en beleidsregels. 
- Riskante aanmeldingen - Een riskante aanmelding is een indicator van een aanmeldingspoging die mogelijk is uitgevoerd door iemand die geen rechtmatige eigenaar van een gebruikersaccount is. 
- Risk ante gebruikers: een Risk ante gebruiker is een indicator voor een gebruikers account dat mogelijk is aangetast. 

Risico detecties voor identiteits beveiliging zijn standaard ingeschakeld en vereisen geen voorbereidings proces. De granulatie of risico gegevens worden bepaald door de licentie-SKU. 

- [Auditrapporten over activiteiten in Azure Active Directory](../reports-monitoring/concept-audit-logs.md)  

- [Azure AD Identity Protection inschakelen](../identity-protection/overview-identity-protection.md)  

- [Bescherming tegen bedreiging in Azure Security Center](/azure/security-center/threat-protection)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Logboekregistratie inschakelen voor Azure-resources

**Hulp**: Azure Active Directory (Azure AD) maakt activiteiten Logboeken. In tegens telling tot sommige Azure-Services maakt Azure AD geen onderscheid tussen activiteiten en resource Logboeken. Activiteiten logboeken zijn automatisch beschikbaar in de sectie Azure AD van de Azure Portal en kunnen worden geëxporteerd naar Azure Monitor, Azure Event Hubs, Azure Storage, Siem's en andere locaties.
 
- Aanmeldingen: het rapport voor aanmeldingsactiviteit bevat informatie over het gebruik van beheerde toepassingen en aanmeldingsactiviteiten van gebruikers  
 
- Auditlogboeken: traceerbaarheid via logboeken voor alle door diverse functies binnen Azure AD uitgevoerde wijzigingen. Voorbeelden van vermeldingen in auditlogboeken zijn wijzigingen die worden aangebracht in resources binnen Azure AD, zoals het toevoegen of verwijderen van gebruikers, apps, groepen, rollen en beleidsregels.  
 
 

Azure AD bevat ook beveiligings logboeken die kunnen worden weer gegeven in azure AD Reporting of geïntegreerd met Azure Monitor, Azure Sentinel of andere hulpprogram ma's voor SIEM/controle voor geavanceerde bewakings-en analyse-gebruiks voorbeelden: 
- Riskante aanmeldingen - Een riskante aanmelding is een indicator van een aanmeldingspoging die mogelijk is uitgevoerd door iemand die geen rechtmatige eigenaar van een gebruikersaccount is.
- Risk ante gebruikers: een Risk ante gebruiker is een indicator voor een gebruikers account dat mogelijk is aangetast. 

Risico detecties voor identiteits beveiliging zijn standaard ingeschakeld en vereisen geen voorbereidings proces. De granulatie of risico gegevens worden bepaald door de licentie-SKU. 

 
- [Activiteiten-en beveiligings rapporten in Azure Active Directory](../reports-monitoring/overview-reports.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Beheer en analyse van beveiligingslogboek centraliseren

**Richt** lijnen: we raden u aan de volgende richt lijnen te volgen wanneer klanten hun beveiligings logboeken willen centraliseren voor een betere beleving van de dreigingen en postuur analyse van beveiliging:
 
- Centraliseer logboek opslag en-analyse om correlatie in te scha kelen. Voor elke logboek bron in azure AD moet u ervoor zorgen dat u een gegevens eigenaar hebt toegewezen, toegangs richtlijnen, opslag locatie, welke hulpprogram ma's worden gebruikt voor het verwerken en openen van de gegevens en vereisten voor gegevens behoud.  
 
- Zorg ervoor dat u Azure-activiteiten logboeken integreert in uw centrale logboek registratie. Opname logboeken via Azure Monitor voor het verzamelen van beveiligings gegevens die zijn gegenereerd door eindpunt apparaten, netwerk bronnen en andere beveiligings systemen. Gebruik in Azure Monitor Log Analytics-werk ruimten om analyses uit te voeren en te uitvoeren en gebruik Azure Storage accounts voor lange termijn-en archiverings opslag.  
 
- Daarnaast kunt u gegevens inschakelen en vrijgeven aan Azure Sentinel of een SIEM van derden.  
 

Veel organisaties kiezen voor het gebruik van Azure Sentinel voor ' hot ' gegevens die regel matig worden gebruikt en die worden Azure Storage voor ' koude ' gegevens die minder vaak worden gebruikt.  
 

- [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](/azure/azure-monitor/platform/diagnostic-settings)   
 

- [Azure-Sentinel onboarden](../../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Bewaarperiode voor logboek configureren

**Richt lijnen**: Zorg ervoor dat opslag accounts of log Analytics-werk ruimten die worden gebruikt voor het opslaan van Azure Active Directory aanmeld logboeken, controle logboeken en risico gegevens logboeken, de Bewaar periode voor logboek registratie hebben op basis van de nalevings regels van uw organisatie.

In Azure Monitor kunt u de Bewaar periode voor uw Log Analytics werk ruimte instellen volgens de nalevings voorschriften van uw organisatie. Gebruik Azure Storage, Data Lake of Log Analytics werkruimte accounts voor lange termijn-en archiverings opslag.

- [De Bewaar periode van Log Analytics Workspace configureren](/azure/azure-monitor/platform/manage-cost-storage)  

- [Bron logboeken opslaan in een Azure Storage-account](/azure/azure-monitor/platform/resource-logs-collect-storage)

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

- [De Azure Security Center Security-contactpersoon instellen](../../security-center/security-center-provide-security-contact-details.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: detectie en analyse: incidenten maken op basis van waarschuwingen van hoge kwaliteit 

**Richtlijnen**: Zorg ervoor dat u een proces hebt gedefinieerd om waarschuwingen van hoge kwaliteit te maken en de kwaliteit van waarschuwingen te meten. Zo kunt u op grond van wat u uit eerdere incidenten hebt geleerd een hogere prioriteit toekennen aan waarschuwingen die bestemd zijn voor analisten, zodat deze geen tijd hoeven te verspillen aan fout-positieven. 

Waarschuwingen van hoge kwaliteit kunnen worden samengesteld op basis van ervaringen uit eerdere incidenten, op grond van gevalideerde communitybronnen, en door tools te gebruiken die zijn ontworpen om waarschuwingen te genereren en op te schonen door diverse signaalbronnen samen te voegen en er correlaties tussen te ontdekken. 

Azure Security Center biedt waarschuwingen van hoge kwaliteit in veel Azure-assets. U kunt de ASC-dataconnector gebruiken om de waarschuwingen te streamen naar Azure Sentinel. Met Azure Sentinel kunt u geavanceerde waarschuwingsregels opstellen om automatisch incidenten te genereren voor onderzoek. 

Exporteer de waarschuwingen en aanbevelingen van Azure Security Center met behulp van de exportfunctie om zo beter risico's voor Azure-resources te kunnen identificeren. U kunt waarschuwingen en aanbevelingen handmatig exporteren of doorlopend.

- [Export configureren](../../security-center/continuous-export.md)

- [Waarschuwingen streamen naar Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

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

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Detectie en analyse: prioriteiten toekennen aan incidenten

**Richtlijnen**: Bied context aan analisten voor de incidenten waarop ze zich eerst moeten concentreren op basis van de ernst van waarschuwingen en de gevoeligheid van assets. 

Azure Security Center wijst aan elke waarschuwing een ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of de analyse die wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau waarvan er sprake is van schadelijke opzet achter de activiteit die tot de waarschuwing heeft geleid.

Daarnaast kunt u resources markeren met behulp van tags en een naamgevingssysteem opzetten om Azure-resources te identificeren en categoriseren, met name voor resources die gevoelige gegevens verwerken.  Het is uw verantwoordelijkheid om prioriteit te geven aan het oplossen van waarschuwingen op basis van de ernst van de Azure-resources en -omgeving waarin het incident heeft plaatsgevonden.

- [Beveiligingswaarschuwingen in Azure Security Center](../../security-center/security-center-alerts-overview.md)

- [Tags gebruiken om Azure-resources te organiseren](/azure/azure-resource-manager/resource-group-using-tags)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Insluiting, uitschakeling en herstel: het afhandelen van incidenten automatiseren

**Richtlijnen**: Automatiseer handmatige, terugkerende taken om de responstijd te versnellen en de werklast van analisten te verlichten. Het uitvoeren van handmatige taken duurt langer, waardoor elk incident trager gaat en het aantal incidenten dat een analist kan afhandelen, kleiner wordt. Daarnaast raken analisten sneller vermoeid door handmatige taken, met als gevolg dat het risico van menselijke fouten toeneemt en analisten zich minder goed kunnen concentreren. Gebruik voorzieningen voor de automatisering van werkstromen in Azure Security Center en Azure Sentinel om automatisch acties te activeren of een playbook uit te voeren in reactie op binnenkomende beveiligingswaarschuwingen. Het playbook voert acties uit, zoals het verzenden van meldingen, het uitschakelen van accounts en het isoleren van problematische netwerken. 

- [Werkstroomautomatisering configureren in Security Center](../../security-center/workflow-automation.md)

- [Geautomatiseerde bedreigingsreacties instellen in Azure Security Center](https://docs.microsoft.com/azure/security-center/tutorial-security-incident#triage-security-alerts)

- [Geautomatiseerde bedreigingsreacties instellen in Azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="posture-and-vulnerability-management"></a>Beveiligingspostuur en beveiligingsproblemen beheren

*Zie [Azure Security Benchmark: beveiligingspostuur en beveiligingsproblemen beheren](/azure/security/benchmarks/security-controls-v2-vulnerability-management) voor meer informatie.*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Veilige configuraties tot stand brengen voor Azure-services 

**Richt lijnen**: micro soft-oplossingen voor identiteits-en toegangs beheer helpen bij het beveiligen van de toegang tot toepassingen en resources op locatie en in de Cloud. Het is belang rijk dat organisaties de aanbevolen beveiligings procedures volgen om ervoor te zorgen dat de implementatie van identiteits-en toegangs beheer veilig en robuuster is voor aanvallen. 

Op basis van uw implementatie strategie voor identiteits-en toegangs beheer moet uw organisatie voldoen aan de micro soft best practicee richt lijnen voor het beveiligen van uw identiteits infrastructuur. 

Organisaties die samen werken met externe partners, moeten ook passende governance-, beveiligings-en nalevings configuraties beoordelen en implementeren om beveiligings Risico's te beperken en gevoelige bronnen te beveiligen.

- [Best practices voor beveiliging voor identiteitsbeheer en toegangsbeheer in Azure](../../security/fundamentals/identity-management-best-practices.md)

- [Vijf stappen voor het beveiligen van uw identiteits infrastructuur](../../security/fundamentals/steps-secure-identity.md)

- [Externe samen werking beveiligen in Azure Active Directory en Microsoft 365](secure-external-access-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Veilige configuraties onderhouden voor Azure-services

**Hulp**: micro soft Secure Score biedt organisaties een meting van hun beveiligings postuur en aanbevelingen waarmee organisaties kunnen worden beschermd tegen bedreigingen. Het wordt aanbevolen dat organisaties hun beveiligde Score regel matig controleren op voorgestelde verbeterings acties om hun identiteits beveiligings postuur te verbeteren. 

- [Wat is de beveiligde Score van de identiteit in Azure Active Directory?](identity-secure-score.md)

- [Microsoft-beveiligingsscore](/microsoft-365/security/mtp/microsoft-secure-score)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Voer regelmatige simulaties van aanvallen uit

**Richtlijnen**: Voer zo vaak u als u wilt penetratietests of Red Teaming-activiteiten uit op uw Azure-resources, en zorg ervoor dat alle kritieke beveiligingsbevindingen worden opgelost.
Ga te werk volgens de Microsoft Cloud Penetration Testing Rules of Engagement (Regels voor het inzetten van penetratietests voor Microsoft Cloud ) zodat u zeker weet dat uw penetratietests niet conflicteren met Microsoft-beleid. Gebruik de strategie van Microsoft en de uitvoering van Red Teaming-activiteiten, en voer een penetratietest van de live site uit op basis van een infrastructuur, services en toepassingen die door Microsoft worden beheerd.

- [Penetratietesten in Azure](../../security/fundamentals/pen-testing.md)

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
- [Azure Security Architecture Recommendation - Storage, data, and encryption](/azure/architecture/framework/security/storage-data-encryption) (Aanbeveling voor Azure-beveiligingsarchitectuur: opslag, gegevens en versleuteling)
 

 
- [Azure Security Fundamentals - Azure Data security, encryption, and storage](../../security/fundamentals/encryption-overview.md) (Basisprincipes van Azure-beveiliging: Azure-gegevensbeveiliging, -versleuteling en -opslag) 

 
- [Cloud Adoption Framework - Azure data security and encryption best practices](../../security/fundamentals/data-encryption-best-practices.md) (Cloud Adoption Framework: best practices voor Azure-gegevensbeveiliging en -versleuteling) 

 
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

- [Overzicht van Azure-netwerkbeveiliging](../../security/fundamentals/network-overview.md)

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

- [Overzicht van beveiliging met Azure-identiteitsbeheer](../../security/fundamentals/identity-management-overview.md)

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
