---
title: Azure-beveiligings beheer-identiteit en Access Control
description: Identiteits-en Access Control van Azure-beveiligings beheer
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: dda3dcd3cd1234b2d0830010297e760201ed6160
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102549274"
---
# <a name="security-control-identity-and-access-control"></a>Beveiligings beheer: identiteit en Access Control

Aanbevelingen voor identiteits-en toegangs beheer zijn gericht op het adresseren van problemen met toegangs beheer op basis van id's, het vergren delen van beheerders toegang, het melden van identiteits gebeurtenissen, gedrag van abnormale accounts en op rollen gebaseerd toegangs beheer.

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: een inventaris van beheerders accounts onderhouden

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 3.1 | 4.1 | Klant |

Azure AD heeft ingebouwde rollen die expliciet moeten worden toegewezen en waarop query's kunnen worden doorzocht. Gebruik de Azure AD Power shell-module om ad hoc-query's uit te voeren om accounts te detecteren die lid zijn van beheer groepen.

- [Een directory-rol verkrijgen in azure AD met Power shell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Leden van een directory-rol in azure AD ophalen met Power shell](/powershell/module/azuread/get-azureaddirectoryrolemember)

## <a name="32-change-default-passwords-where-applicable"></a>3,2: standaard wachtwoorden wijzigen indien van toepassing

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 3.2 | 4.2 | Klant |

Azure AD heeft niet het concept van standaard wachtwoorden. Andere Azure-resources die een wacht woord vereisen, moeten een wacht woord afdwingen voor het maken van complexiteits vereisten en een minimale wachtwoord lengte, die afhankelijk is van de service. U bent verantwoordelijk voor toepassingen van derden en Marketplace-services die standaard wachtwoorden kunnen gebruiken.

## <a name="33-use-dedicated-administrative-accounts"></a>3,3: speciale beheerders accounts gebruiken

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 3,3 | 4.3 | Klant |

Maak standaard procedures voor het gebruik van specifieke beheerders accounts. Gebruik Azure Security Center identiteits-en toegangs beheer om het aantal beheerders accounts te bewaken.

U kunt ook Just-in-time/alleen-voldoende toegang inschakelen met behulp van Azure AD Privileged Identity Management geprivilegieerde rollen voor micro soft-Services en Azure Resource Manager. 

- [Meer informatie over Privileged Identity Management](../../active-directory/privileged-identity-management/index.yml)

## <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: eenmalige aanmelding (SSO) met Azure Active Directory gebruiken

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 3.4 | 4.4 | Klant |

Gebruik waar mogelijk Azure Active Directory-SSO in plaats van afzonderlijke zelfstandige referenties per service te configureren. Gebruik Azure Security Center aanbevelingen voor identiteits-en toegangs beheer.

- [Informatie over eenmalige aanmelding met Azure AD](../../active-directory/manage-apps/what-is-single-sign-on.md)

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: multi-factor Authentication gebruiken voor alle op Azure Active Directory gebaseerde toegang

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 3,5 | 4,5, 11,5, 12,11, 16,3 | Klant |

Schakel Azure AD MFA in en volg Azure Security Center aanbevelingen voor identiteits-en toegangs beheer.

- [MFA inschakelen in Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Identiteit en toegang bewaken in Azure Security Center](../../security-center/security-center-identity-access.md)

## <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: gebruik speciale machines (privileged Access workstations) voor alle beheer taken

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 3,6 | 4,6, 11,6, 12,12 | Klant |

Gebruik Paw's (privileged Access workstations) met MFA dat is geconfigureerd om Azure-resources aan te melden en te configureren.

- [Meer informatie over privileged Access workstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [MFA inschakelen in Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

## <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logboeken en waarschuwingen voor verdachte activiteiten van beheerders accounts

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 3.7 | 4,8, 4,9 | Klant |

Gebruik Azure Active Directory beveiligings rapporten voor het genereren van Logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten in de omgeving worden uitgevoerd. Gebruik Azure Security Center om identiteits-en toegangs activiteiten te bewaken.

- [Azure AD-gebruikers identificeren die zijn gemarkeerd voor riskante activiteiten](../../active-directory/identity-protection/overview-identity-protection.md)

- [Identiteits- en toegangsactiviteiten van gebruikers controleren in Azure Security Center](../../security-center/security-center-identity-access.md)

## <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure-resources alleen beheren vanaf goedgekeurde locaties

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 3.8 | 11.7 | Klant |

Gebruik benoemde locaties voor voorwaardelijke toegang om alleen toegang toe te staan vanaf specifieke logische groepen met IP-adresbereiken of landen/regio's.

- [Benoemde locaties configureren in azure](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

## <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory gebruiken

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 3.9 | 16,1, 16,2, 16,4, 16,5, 16,6 | Klant |

Gebruik Azure Active Directory als centrale verificatie-en autorisatie systeem. Azure AD beveiligt gegevens door gebruik te maken van sterke versleuteling voor gegevens in rust en onderweg. Azure AD bevat ook zouten, hashes en veilige gebruikers referenties.

- [Een Azure AD-instantie maken en configureren](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

## <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regel matig gebruikers toegang controleren en afstemmen

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 3.10 | 16,9, 16,10 | Klant |

Azure AD biedt logboeken waarmee u verlopen accounts kunt detecteren. Daarnaast kunt u Azure Identity Access revisies gebruiken om groepslid maatschappen en de toegang tot bedrijfs toepassingen en roltoewijzingen op efficiënte wijze te beheren. Gebruikers toegang kan regel matig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben. 

- [Meer informatie over Azure AD-rapportage](../../active-directory/reports-monitoring/index.yml)

- [Beoordelingen over Azure Identity Access gebruiken](../../active-directory/governance/access-reviews-overview.md)

## <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: controle pogingen om toegang te krijgen tot gedeactiveerde referenties

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 3,11 | 16,12 | Klant |

U hebt toegang tot de Azure AD-aanmeldings activiteit, de controle-en risico gebeurtenis logboek bronnen, waarmee u kunt integreren met elk SIEM/bewakings programma.

U kunt dit proces stroom lijnen door Diagnostische instellingen voor Azure Active Directory gebruikers accounts te maken en de audit logboeken en aanmeldings logboeken te verzenden naar een Log Analytics-werk ruimte. U kunt de gewenste waarschuwingen configureren in Log Analytics werk ruimte.

- [Azure-activiteitenlogboeken integreren in Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

## <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: waarschuwing voor de afwijking van het aanmeldings gedrag van accounts

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 3,12 | 16,13 | Klant |

Gebruik Azure AD-functies voor risico-en identiteits beveiliging om automatische antwoorden te configureren op gedetecteerde verdachte acties met betrekking tot gebruikers identiteiten. U kunt ook gegevens opnemen in azure Sentinel voor verder onderzoek.

- [Riskante Azure AD-aanmeldingen weergeven](../../active-directory/identity-protection/overview-identity-protection.md)

- [Risico beleid voor identiteits beveiliging configureren en inschakelen](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure-Sentinel onboarden](../../sentinel/quickstart-onboard.md)

## <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: micro soft biedt toegang tot relevante klant gegevens tijdens ondersteunings scenario's

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 3,13 | 16 | Klant |

In ondersteunings scenario's waarin micro soft toegang moet krijgen tot klant gegevens, bevat Klanten-lockbox een interface waarmee u aanvragen voor het verkrijgen van gegevens toegang voor klanten kunt controleren en goed keuren of afwijzen.

- [Klanten-lockbox begrijpen](../fundamentals/customer-lockbox-overview.md)


## <a name="next-steps"></a>Volgende stappen

- Zie het volgende beveiligings beheer: [gegevens beveiliging](security-control-data-protection.md)