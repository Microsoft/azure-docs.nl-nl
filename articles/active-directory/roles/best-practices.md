---
title: Aanbevolen procedures voor Azure AD-rollen-Azure Active Directory
description: Aanbevolen procedures voor het gebruik van Azure Active Directory rollen.
services: active-directory
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: conceptual
ms.date: 03/28/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78f4642b8f9f1ede65766a0026940c0af0f01ec2
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111632"
---
# <a name="best-practices-for-azure-ad-roles"></a>Aanbevolen procedures voor Azure AD-rollen

In dit artikel worden enkele van de aanbevolen procedures beschreven voor het gebruik van Azure Active Directory op rollen gebaseerd toegangs beheer (Azure AD RBAC). Deze aanbevolen procedures zijn afgeleid van onze ervaring met Azure AD RBAC en de ervaringen van klanten, zoals uzelf. We raden u aan om ook onze gedetailleerde beveiligings richtlijnen te lezen bij het [beveiligen van bevoegde toegang voor hybride en Cloud implementaties in azure AD](security-planning.md).

## <a name="1-manage-to-least-privilege"></a>1. beheer naar de minimale bevoegdheid

Bij het plannen van uw strategie voor toegangs beheer is het een best practice die u kunt beheren met minimale bevoegdheden. Minimale bevoegdheid betekent dat u uw beheerders precies de toestemming verleent die ze nodig hebben om hun taak uit te voeren. Er zijn drie aspecten waarmee u rekening moet houden wanneer u een rol aan uw beheerders toewijst: een specifieke set machtigingen, gedurende een bepaald bereik, voor een bepaalde periode. Vermijd het toewijzen van bredere rollen in bredere bereiken, zelfs als deze in eerste instantie handiger lijkt te zijn. Door de rollen en bereiken te beperken, beperkt u de resources die risico lopen als de beveiligings-principal ooit is aangetast. Azure AD RBAC ondersteunt meer dan 65 [ingebouwde rollen](permissions-reference.md). Er zijn Azure AD-functies voor het beheren van Directory-objecten, zoals gebruikers, groepen en toepassingen, en voor het beheren van Microsoft 365 services zoals Exchange, share point en intune. Zie [inzicht in rollen in azure Active Directory](concept-understand-roles.md)voor meer informatie over ingebouwde rollen van Azure AD. Als er geen ingebouwde rol is die aan uw behoeften voldoet, kunt u uw eigen [aangepaste rollen](custom-create.md)maken.  
 
### <a name="finding-the-right-roles"></a>De juiste rollen zoeken

Volg deze stappen om u te helpen bij het vinden van de juiste rol.

1. Open in de Azure Portal [rollen en beheerders](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) om de lijst met Azure AD-rollen te bekijken.

1. Gebruik het **service** filter om de lijst met rollen te beperken.

    ![De pagina rollen en beheerders in azure AD met het service filter open](./media/best-practices/roles-administrators.png)

1. Raadpleeg de documentatie over [ingebouwde rollen in azure AD](permissions-reference.md) . Machtigingen die zijn gekoppeld aan elke rol worden samen weer gegeven voor een betere Lees baarheid. Zie de [machtigingen van rollen begrijpen](permissions-reference.md#how-to-understand-role-permissions)voor meer informatie over de structuur en betekenis van rolmachtigingen.

1. Raadpleeg de documentatie over de [minst bevoegde rol per taak](delegate-by-task.md) .

## <a name="2-use-privileged-identity-management-to-grant-just-in-time-access"></a>2. gebruik Privileged Identity Management om just-in-time-toegang te verlenen

Een van de beginselen van minimale bevoegdheid is dat toegang alleen moet worden verleend voor een bepaalde periode. Met [Azure AD privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) kunt u uw beheerders just-in-time toegang verlenen. Micro soft raadt u aan om PIM in te scha kelen in azure AD. Met behulp van PIM kan een gebruiker een in aanmerking komend lid van een Azure AD-rol worden gemaakt. De kan vervolgens hun rol activeren voor een beperkte periode elke keer dat deze moet worden gebruikt. Privileged Access wordt automatisch verwijderd wanneer de periode verloopt. U kunt ook [PIM-instellingen configureren](../privileged-identity-management/pim-how-to-change-default-settings.md) om goed keuring te vereisen of e-mail meldingen te ontvangen wanneer iemand de roltoewijzing activeert. Meldingen bieden een waarschuwing wanneer nieuwe gebruikers worden toegevoegd aan rollen met een hoge bevoegdheden. 

## <a name="3-turn-on-multi-factor-authentication-for-all-your-administrator-accounts"></a>3. Schakel multi-factor Authentication in voor al uw beheerders accounts

[Op basis van onze studies](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)is uw account 99,9% minder waarschijnlijk in gevaar als u multi-factor Authentication (MFA) gebruikt. 
 
U kunt MFA inschakelen op Azure AD-rollen met behulp van twee methoden:
- [Rolinstellingen](../privileged-identity-management/pim-how-to-change-default-settings.md) in privileged Identity Management
- [Voorwaardelijke toegang](../conditional-access/howto-conditional-access-policy-admin-mfa.md)

## <a name="4-configure-recurring-access-reviews-to-revoke-unneeded-permissions-over-time"></a>4. herstelde terugkerende toegangs Beoordelingen voor het intrekken van overbodige machtigingen na verloop van tijd

Met toegangs beoordelingen kunnen organisaties de toegang tot de beheerder regel matig controleren om ervoor te zorgen dat alleen de juiste personen toegang hebben. Regel matige controle van uw beheerders is van cruciaal belang om de volgende redenen:
- Een kwaadwillende actor kan een account aantasten.
- Mensen verplaatsen teams binnen een bedrijf. Als er geen controle is, kunnen ze na verloop van tijd onnodige toegang amass.
 
Zie [een toegangs beoordeling van Azure AD-rollen maken in PIM](../privileged-identity-management/pim-how-to-start-security-review.md)voor meer informatie over toegangs Beoordelingen voor rollen. Zie [een toegangs beoordeling van groepen en toepassingen maken in azure AD-toegangs beoordelingen](../governance/create-access-review.md)voor meer informatie over toegangs beoordelingen van groepen waaraan rollen zijn toegewezen.

## <a name="5-limit-the-number-of-global-administrators-to-less-than-5"></a>5. het aantal globale beheerders beperken tot minder dan 5

Als best practice, raadt micro soft u aan de rol van globale beheerder toe te wijzen aan **minder dan vijf** personen in uw organisatie. Globale beheerders bevatten sleutels voor het Konink rijk en zijn in uw beste belang om de kwets baarheid voor aanvallen laag te houden. Zoals eerder vermeld, moeten al deze accounts worden beveiligd met multi-factor Authentication.

Wanneer een gebruiker zich aanmeldt voor een micro soft-Cloud service, wordt standaard een Azure AD-Tenant gemaakt en wordt de gebruiker lid van de rol globale beheerder gemaakt. Gebruikers aan wie de rol globale beheerder is toegewezen, kunnen elke beheer instelling in uw Azure AD-organisatie lezen en wijzigen. Met enkele uitzonde ringen kunnen globale beheerders ook alle configuratie-instellingen in uw Microsoft 365-organisatie lezen en wijzigen. Globale beheerders hebben ook de mogelijkheid om hun toegang tot het lezen van gegevens te verhogen.

Micro soft raadt u aan twee afbreek glazen-accounts te blijven die permanent zijn toegewezen aan de rol van globale beheerder. Zorg ervoor dat deze accounts niet hetzelfde multi-factor Authentication-mechanisme vereisen als uw normale beheerders accounts om u aan te melden, zoals beschreven in [accounts voor nood toegang beheren in azure AD](../roles/security-emergency-access.md). 

## <a name="6-use-groups-for-azure-ad-role-assignments-and-delegate-the-role-assignment"></a>6. Gebruik groepen voor Azure AD-Roltoewijzingen en Delegeer de roltoewijzing

Als u een extern governance-systeem hebt dat gebruik maakt van groepen, kunt u overwegen om rollen toe te wijzen aan Azure AD-groepen in plaats van afzonderlijke gebruikers. U kunt ook door rollen toewijs bare groepen in PIM beheren om ervoor te zorgen dat er geen permanente eigen aren of leden in deze geprivilegieerde groepen zijn. Zie [beheer mogelijkheden voor bevoegde toegang tot Azure ad-groepen](../privileged-identity-management/groups-features.md)voor meer informatie.

U kunt een eigenaar toewijzen aan groepen die kunnen worden toegewezen aan een functie. Deze eigenaar beslist wie er wordt toegevoegd aan of verwijderd uit de groep, dus indirect beslist wie de roltoewijzing ophaalt. Op deze manier kan een globale beheerder of beheerder van een bevoegde rol Role Management per rol overdragen door gebruik te maken van groepen. Zie [Cloud groepen gebruiken om roltoewijzingen in azure Active Directory te beheren](groups-concept.md)voor meer informatie.

## <a name="7-activate-multiple-roles-at-once-using-privileged-access-groups"></a>7. meerdere rollen tegelijk activeren met behulp van geprivilegieerde toegangs groepen

Dit kan het geval zijn dat een individu vijf of zes in aanmerking komende toewijzingen aan Azure AD-rollen via PIM heeft. Ze moeten elke rol afzonderlijk activeren, waardoor de productiviteit kan worden verminderd. Nog erger, er kunnen ook tien tallen en honderden Azure-resources aan worden toegewezen, waardoor het probleem wordt verergerd.
 
In dit geval moet u [geprivilegieerde toegangs groepen](../privileged-identity-management/groups-features.md)gebruiken. Maak een privileged Access-groep en verleen deze permanente toegang tot meerdere rollen (Azure AD en/of Azure). Deze gebruiker een in aanmerking komend lid of eigenaar van deze groep maken. Met slechts één activering hebben ze toegang tot alle gekoppelde resources.

![Diagram van een geprivilegieerde Access-groep waarin meerdere rollen tegelijk worden geactiveerd](./media/best-practices/privileged-access-group.png)

## <a name="8-use-cloud-native-accounts-for-azure-ad-roles"></a>8. native Cloud accounts gebruiken voor Azure AD-rollen

Vermijd het gebruik van on-premises gesynchroniseerde accounts voor Azure AD-roltoewijzingen. Als uw on-premises account is aangetast, kan dit ook in uw Azure AD-resources worden aangetast.

## <a name="next-steps"></a>Volgende stappen

- [Bevoegde toegang beveiligen voor hybride implementaties en cloudimplementaties in Azure AD](security-planning.md)