---
title: Azure Security Bench Mark v2-Identity Management
description: Azure Security Bench Mark v2 Identity Management
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 33f5dff65fa7ad8274051f784f2e61dc8366d389
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368848"
---
# <a name="security-control-v2-identity-management"></a>Beveiligings controle v2: identiteits beheer

Identiteits beheer is van toepassing op besturings elementen voor het instellen van een beveiligde identiteits-en toegangs beheer met Azure Active Directory. Dit geldt ook voor het gebruik van eenmalige aanmelding, sterke verificaties, beheerde identiteiten (en service principes) voor toepassingen, voorwaardelijke toegang en het controleren van afwijkingen van accounts.

## <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Azure Active Directory standaardiseren als het centrale identiteits--en verificatiesysteem

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP 800-53 R4-ID ('s) |
|--|--|--|--|
| CHAT-1 | 16,1, 16,2, 16,4, 16,5 | IA-2, IA-8, AC-2, AC-3 |

Azure Active Directory (Azure AD) is de standaard service voor identiteits-en toegangs beheer van Azure. U moet standaardiseren in overeenstemming met Azure AD om het identiteits- en toegangsbeheer van uw organisatie te regelen in:
- Microsoft-cloudresources, zoals Azure Portal, Azure Storage, Azure Virtual Machines (Linux en Windows), Azure Key Vault, PaaS en SaaS-toepassingen.

- De resources van uw organisatie, zoals toepassingen in Azure of resources van uw bedrijfsnetwerk.

Het beveiligen van Azure AD moet een hoge prioriteit hebben bij het nemen van beveiligingsmaatregelen voor de cloud in uw organisatie. Azure AD biedt een id-beveiligingsscore om u te helpen uw postuur voor identiteitsbeveiliging te bepalen ten opzichte van de best practices die Microsoft aanbeveelt. Gebruik de score om te meten hoe goed uw configuratie aansluit bij de aanbevelingen en om verbeteringen door te voeren in uw beveiligingspostuur.

Opmerking: Azure AD biedt ondersteuning voor externe iD-providers, zodat gebruikers zonder Microsoft-account zich met hun externe identiteit kunnen aanmelden bij hun toepassingen en resources.

- [Tenancy in Azure AD](../../active-directory/develop/single-and-multi-tenant-apps.md)

- [Een Azure AD-instantie maken en configureren](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Azure AD-tenants definiëren](https://azure.microsoft.com/resources/securing-azure-environments-with-azure-active-directory/)  

- [Externe id-providers gebruiken voor een toepassing](../../active-directory/external-identities/identity-providers.md)

- [Wat is de id-beveiligingsscore in Azure AD?](../../active-directory/fundamentals/identity-secure-score.md)

**Verantwoordelijkheid**: Klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Identiteits-en sleutel beheer](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys) 

- [Beveiligingsarchitectuur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Toepassingsbeveiliging en DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Postuurbeheer](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Toepassingsidentiteiten veilig en automatisch beheren

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP 800-53 R4-ID ('s) |
|--|--|--|--|
| CHAT-2 | N.v.t. | AC-2, AC-3, IA-2, IA-4, IA-9 |

Voor niet-humane accounts, zoals services of automatisering, gebruikt u Azure Managed Identities in plaats van een krachtig menselijk account te maken voor toegang tot resources of code uit te voeren. Door Azure beheerde identiteiten kunnen worden geverifieerd bij Azure-Services en-resources die ondersteuning bieden voor Azure AD-verificatie. Verificatie wordt ingeschakeld via vooraf gedefinieerde regels voor toegangs verlening, waardoor het niet mogelijk is om in code vastgelegde referenties in de bron code-of configuratie bestanden te gebruiken. 

Voor services die geen beheerde identiteiten ondersteunen, gebruikt u Azure AD om in plaats daarvan een service-principal te maken met beperkte machtigingen op het resource niveau.  Het is raadzaam Service-principals met certificaat referenties te configureren en terug te vallen op client geheimen. In beide gevallen kunnen Azure Key Vault worden gebruikt in combi natie met door Azure beheerde identiteiten, zodat de runtime-omgeving (zoals een Azure function) de referentie kan ophalen uit de sleutel kluis.

- [Door Azure beheerde identiteiten](../../active-directory/managed-identities-azure-resources/overview.md)

- [Services die beheerde identiteiten voor Azure-resources ondersteunen](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Azure-service-principal](/powershell/azure/create-azure-service-principal-azureps)

- [Een service-principal maken met certificaten](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)

Azure Key Vault gebruiken voor registratie van beveiligings-principals: verificatie # autoriseren-a-Security-Principal-to-Access-Key-kluis

**Verantwoordelijkheid**: Klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Identiteits-en sleutel beheer](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Toepassingsbeveiliging en DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Eenmalige aanmelding (SSO) van Azure AD gebruiken voor toegang tot toepassingen

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP 800-53 R4-ID ('s) |
|--|--|--|--|
| CHAT-3 | 4.4 | IA-2, IA-4 |

Azure AD biedt identiteits-en toegangs beheer aan Azure-resources, Cloud toepassingen en on-premises toepassingen. Identiteits-en toegangs beheer is van toepassing op ondernemings identiteiten, zoals werk nemers, evenals externe identiteiten, zoals partners, leveranciers en leveranciers.

Gebruik eenmalige aanmelding (SSO) van Azure AD voor het beheren en beveiligen van de toegang tot de gegevens en resources van uw organisatie op locatie en in de Cloud. Verbind al uw gebruikers, toepassingen en apparaten met Azure AD voor naadloze, veilige toegang en meer zicht baarheid en controle. 

- [Meer informatie over de SSO van de toepassing met Azure AD](../../active-directory/manage-apps/what-is-single-sign-on.md)

**Verantwoordelijkheid**: Klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Beveiligingsarchitectuur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Identiteits-en sleutel beheer](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Toepassingsbeveiliging en DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Gebruik krachtige besturingselementen voor verificatie voor alle op Azure Active Directory gebaseerde toegang

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP 800-53 R4-ID ('s) |
|--|--|--|--|
| CHAT-4 | 4,2, 4,4 4,5, 11,5, 12,11, 16,3 | AC-2, AC-3, IA-2, IA-4 |

Azure AD biedt ondersteuning voor sterke verificatie controles via multi-factor Authentication (MFA) en krachtige methoden met een eigen wacht woord.  
- Multi-factor Authentication: Schakel Azure AD MFA in en volg Azure Security Center aanbevelingen voor identiteits-en toegangs beheer voor uw MFA-installatie. MFA kan worden afgedwongen voor alle gebruikers, gebruikers selecteren of op het niveau per gebruiker op basis van aanmeldings voorwaarden en risico factoren. 

- Verificatie met wacht woord: er zijn drie verificatie opties met een wacht woord beschikbaar: Windows hello voor bedrijven, Microsoft Authenticator-app en on-premises verificatie methoden zoals Smart Cards. 

Voor beheerders en bevoegde gebruikers zorgt u ervoor dat het hoogste niveau van de methode voor sterke verificatie wordt gebruikt, gevolgd door het juiste beleid voor sterke authenticatie voor andere gebruikers te implementeren.

Als er nog steeds verouderde verificatie op basis van wacht woorden wordt gebruikt voor Azure AD-verificatie, moet u er rekening mee houden dat alleen Cloud accounts (gebruikers accounts die rechtstreeks in azure zijn gemaakt) een standaard wachtwoord beleid voor de basis lijn hebben. En hybride accounts (gebruikers accounts die afkomstig zijn van on-premises Active Directory) volgen het on-premises wachtwoord beleid. Wanneer u gebruikmaakt van verificatie op basis van wacht woorden, biedt Azure AD een functie voor wachtwoord beveiliging waarmee wordt voor komen dat gebruikers wacht woorden kunnen instellen die gemakkelijk te raden zijn. Micro soft biedt een algemene lijst met verboden wacht woorden die worden bijgewerkt op basis van de telemetrie, en klanten kunnen de lijst uitbreiden op basis van hun behoeften (bijvoorbeeld branding, culturele verwijzingen, enzovoort). Deze wachtwoord beveiliging kan worden gebruikt voor Cloud-en hybride accounts. 

Opmerking: verificatie op basis van wachtwoord referenties is alleen vatbaar voor populaire aanvals methoden. Voor een betere beveiliging gebruikt u sterke verificatie zoals MFA en een sterk wachtwoord beleid. Voor toepassingen van derden en Marketplace-services die standaard wachtwoorden kunnen hebben, moet u deze wijzigen tijdens de initiële service-instelling. 

- [MFA inschakelen in Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Algemene informatie over opties voor verificatie zonder wachtwoord voor Azure Active Directory](../../active-directory/authentication/concept-authentication-passwordless.md)

- [Standaardwachtwoordbeleid voor Azure AD](../../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [Ongeschikte wachtwoorden voorkomen met Azure AD-wachtwoordbeveiliging](../../active-directory/authentication/concept-password-ban-bad.md)

**Verantwoordelijkheid**: Klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Beveiligingsarchitectuur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Identiteits-en sleutel beheer](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Toepassingsbeveiliging en DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Bewaken van accounts op afwijkingen en waarschuwingenbeheer

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP 800-53 R4-ID ('s) |
|--|--|--|--|
| CHAT-5 | 4,8, 4,9, 16,12, 16,13 | AC-2, AC-3, AC-7, AU-6 |

Azure AD biedt de volgende gegevens bronnen: 
-   Aanmeldingen: het rapport voor aanmeldingsactiviteit bevat informatie over het gebruik van beheerde toepassingen en aanmeldingsactiviteiten van gebruikers

-   Audit logboeken: voorziet in traceer baarheid via Logboeken voor alle wijzigingen die zijn aangebracht via verschillende functies in azure AD. Voor beelden van audit Logboeken in vastgelegde wijzigingen zijn het toevoegen of verwijderen van gebruikers, apps, groepen, rollen en beleids regels.

-   Riskante aanmeldingen - Een riskante aanmelding is een indicator van een aanmeldingspoging die mogelijk is uitgevoerd door iemand die geen rechtmatige eigenaar van een gebruikersaccount is.

-   Gebruikers voor wie wordt aangegeven dat ze risico lopen - Een riskante gebruiker is een indicator van een gebruikersaccount dat mogelijk is aangetast.

Deze gegevensbronnen kunnen worden geïntegreerd met Azure Monitor, Azure Sentinel of SIEM-systemen van derden.

Azure Security Center kan ook worden gewaarschuwd voor bepaalde verdachte activiteiten, zoals een uitzonderlijk aantal mislukte verificatie pogingen en afgeschafte accounts in het abonnement. 

Azure Advanced Threat Protection (ATP) is een beveiligings oplossing die on-premises Active Directory signalen kan gebruiken om geavanceerde bedreigingen, gemanipuleerde identiteiten en schadelijke Insider-acties te identificeren, te detecteren en te onderzoeken.

- [Activiteiten rapporten controleren in azure AD](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [Riskante Azure AD-aanmeldingen weergeven](../../active-directory/identity-protection/overview-identity-protection.md)

- [Azure AD-gebruikers identificeren die zijn gemarkeerd voor riskante activiteiten](../../active-directory/identity-protection/overview-identity-protection.md)

- [Identiteits- en toegangsactiviteiten van gebruikers controleren in Azure Security Center](../../security-center/security-center-identity-access.md)

- [Waarschuwingen in de module Threat Intelligence Protection van Azure Security Center](../../security-center/alerts-reference.md)

- [Azure-activiteiten logboeken integreren in Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Verbinding maken met gegevens van Azure AD Identity Protection](../../sentinel/connect-azure-ad-identity-protection.md)

- [Azure Advanced Threat Protection](/azure-advanced-threat-protection/what-is-atp)

**Verantwoordelijkheid**: Klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Toepassingsbeveiliging en DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Postuurbeheer](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Toegang tot Azure-resource beperken op basis van voorwaarden

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP 800-53 R4-ID ('s) |
|--|--|--|--|
| CHAT-6 | N.v.t. | AC-2, AC-3 |

Gebruik voorwaardelijke toegang voor Azure AD voor meer gedetailleerd toegangs beheer op basis van door de gebruiker gedefinieerde voor waarden, zoals het vereisen van gebruikers aanmeldingen vanuit bepaalde IP-bereiken voor het gebruik van MFA. Een gedetailleerd beheer van verificatie sessies kan ook worden gebruikt via beleid voor voorwaardelijke toegang van Azure AD voor verschillende use cases. 

- [Overzicht van voorwaardelijke toegang voor Azure](../../active-directory/conditional-access/overview.md)

- [Algemeen beleid voor voorwaardelijke toegang](../../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Beheer van verificatiesessies met voorwaardelijke toegang configureren](../../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Verantwoordelijkheid**: Klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Identiteits-en sleutel beheer](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Toepassingsbeveiliging en DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Postuurbeheer](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Informatie over bedreigingen](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: Onbedoelde blootstelling van referenties elimineren

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP 800-53 R4-ID ('s) |
|--|--|--|--|
| CHAT-7 | 18,1, 18,7 | IA-5 |

Implementeer de Azure DevOps-referentie scanner om referenties in de code te identificeren. Credential scanner stimuleert ook het verplaatsen van gedetecteerde referenties naar veiliger locaties, zoals Azure Key Vault.

Voor GitHub kunt u de systeemeigen functie voor het scannen op geheimen gebruiken om referenties of een andere vormen van geheimen binnen de code te identificeren.

- [Referentie scanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Scannen op geheimen in GitHub](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Verantwoordelijkheid**: Klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Toepassingsbeveiliging en DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Postuurbeheer](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="im-8-secure-user-access-to-legacy-applications"></a>IM-8: beveiligde gebruikers toegang tot verouderde toepassingen

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP 800-53 R4-ID ('s) |
|--|--|--|--|
| CHAT-8 | 14,6 | AC-2, AC-3, SC-11 |

Zorg ervoor dat u beschikt over moderne toegangs beheer en sessie bewaking voor oudere toepassingen en de gegevens die ze opslaan en verwerken. Hoewel Vpn's vaak worden gebruikt voor toegang tot oudere toepassingen, hebben ze vaak alleen elementaire toegangs beheer en beperkte sessie bewaking.

Met Azure AD-toepassingsproxy kunt u oude on-premises toepassingen publiceren naar externe gebruikers met eenmalige aanmelding (SSO), waarbij u de betrouw baarheid van zowel externe gebruikers als apparaten expliciet valideert met voorwaardelijke toegang van Azure AD. 

Microsoft Cloud App Security is ook een CASB-service (Cloud Access Security Broker) die besturings elementen kan bieden voor het bewaken van toepassings sessies van een gebruiker en het blok keren van acties (voor zowel oudere on-premises toepassingen als voor de cloud software als een service (SaaS)-toepassingen). 

- [Azure AD-toepassingsproxy](../../active-directory/manage-apps/application-proxy.md#what-is-application-proxy)

- [Aanbevolen procedures Microsoft Cloud App Security](/cloud-app-security/best-practices)

**Verantwoordelijkheid**: Klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Beveiligingsarchitectuur](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Infrastructuur- en eindpuntbeveiliging](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Toepassingsbeveiliging en DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)