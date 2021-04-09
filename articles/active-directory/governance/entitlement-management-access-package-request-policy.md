---
title: Instellingen voor wijzigings aanvragen voor een toegangs pakket in azure AD rechten beheer-Azure Active Directory
description: Meer informatie over het wijzigen van aanvraag instellingen voor een toegangs pakket in Azure Active Directory rechten beheer.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 09/16/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87792b8469c78e20a8716bd925d71933f9d97c5e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105560485"
---
# <a name="change-request-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Instellingen voor wijzigings aanvragen voor een toegangs pakket in azure AD-rechts beheer

Als Access Package Manager kunt u de gebruikers die op elk gewenst moment een toegangs pakket aanvragen, wijzigen door het beleid te bewerken of een nieuw beleid toe te voegen. In dit artikel wordt beschreven hoe u de aanvraag instellingen voor een bestaand toegangs pakket wijzigt.

## <a name="choose-between-one-or-multiple-policies"></a>Kiezen tussen een of meer beleids regels

De manier waarop u opgeeft wie een toegangs pakket kan aanvragen, is met een beleid. Voordat u een nieuw beleid maakt of een bestaand beleid in een toegangs pakket bewerkt, moet u bepalen hoeveel beleids regels het toegangs pakket nodig heeft. 

Wanneer u een toegangs pakket maakt, geeft u de aanvraag instelling op waarmee een beleid wordt gemaakt. Voor de meeste toegangs pakketten geldt één beleid, maar één toegangs pakket kan meerdere beleids regels bevatten. U maakt meerdere beleids regels voor een toegangs pakket als u wilt toestaan dat verschillende sets gebruikers toewijzingen krijgen met verschillende aanvraag-en goedkeurings instellingen. 

Eén beleid kan bijvoorbeeld niet worden gebruikt om interne en externe gebruikers toe te wijzen aan hetzelfde toegangs pakket. U kunt echter twee beleids regels maken in hetzelfde toegangs pakket, één voor interne gebruikers en één voor externe gebruikers. Als er meerdere beleids regels zijn die van toepassing zijn op een gebruiker, wordt deze gevraagd op het tijdstip van de aanvraag om het beleid te selecteren waaraan ze zijn toegewezen. In het volgende diagram ziet u een toegangs pakket met twee beleids regels.

![Meerdere beleids regels in een toegangs pakket](./media/entitlement-management-access-package-request-policy/access-package-policy.png)

### <a name="how-many-policies-will-i-need"></a>Hoeveel beleids regels heb ik nodig?

| Scenario | Aantal beleids regels |
| --- | --- |
| Ik wil dat alle gebruikers in mijn Directory dezelfde aanvraag-en goedkeurings instellingen voor een toegangs pakket hebben | Eén |
| Ik wil dat alle gebruikers in bepaalde verbonden organisaties een toegangs pakket kunnen aanvragen | Eén |
| Ik wil gebruikers in mijn Directory en gebruikers buiten mijn Directory toestaan om een toegangs pakket aan te vragen | Meerdere |
| Ik wil andere goedkeurings instellingen opgeven voor sommige gebruikers | Meerdere |
| Ik wil dat sommige gebruikers pakket toewijzingen gebruiken om te verlopen terwijl andere gebruikers hun toegang kunnen uitbreiden | Meerdere |

Zie [meerdere beleids regels](entitlement-management-troubleshoot.md#multiple-policies
)voor informatie over de prioriteits logica die wordt gebruikt wanneer er meerdere beleids regels van toepassing zijn.

## <a name="open-an-existing-access-package-and-add-a-new-policy-of-request-settings"></a>Een bestaand toegangs pakket openen en een nieuw beleid voor aanvraag instellingen toevoegen

Als u een set gebruikers hebt met verschillende aanvraag-en goedkeurings instellingen, moet u waarschijnlijk een nieuw beleid maken. Voer de volgende stappen uit om een nieuw beleid toe te voegen aan een bestaand toegangs pakket:

**Vereiste rol:** globale beheerder, gebruikersbeheerder, cataloguseigenaar of toegangspakketbeheerder

1. Klik in de Azure-portal op **Azure Active Directory** en vervolgens op **Identity Governance**.

1. Klik in het menu links op **toegangs pakketten** en open vervolgens het toegangs pakket.

1. Klik op **beleid** en vervolgens op **beleid toevoegen**.

1. U gaat op het tabblad **basis beginselen** . Voer een naam en beschrijving voor het beleid in.

    ![Beleid maken met de naam en beschrijving](./media/entitlement-management-access-package-request-policy/policy-name-description.png)

1. Klik op **Volgende** om het tabblad **Aanvragen** te openen.

1. Wijzig de **gebruikers die de toegangs instelling kunnen aanvragen** . Volg de stappen in de volgende secties om de instelling te wijzigen in een van de volgende opties: 
    - [Voor gebruikers in uw Directory](#for-users-in-your-directory) 
    - [Voor gebruikers die niet in uw Directory voor komt](#for-users-not-in-your-directory)
    - [Geen (alleen directe toewijzingen voor beheerders)](#none-administrator-direct-assignments-only)

## <a name="for-users-in-your-directory"></a>Voor gebruikers in uw Directory

Volg deze stappen als u wilt toestaan dat gebruikers in uw directory dit toegangs pakket kunnen aanvragen. Wanneer u het aanvraag beleid definieert, kunt u afzonderlijke gebruikers of meer vaak groepen gebruikers opgeven. Uw organisatie kan bijvoorbeeld al een groep hebben, zoals **alle werk nemers**.  Als deze groep wordt toegevoegd aan het beleid voor gebruikers die toegang kunnen aanvragen, kan een lid van die groep vervolgens toegang vragen.

1. Klik in de sectie **gebruikers die toegang kunnen aanvragen** , op **voor gebruikers in uw directory**.

    Wanneer u deze optie selecteert, worden er nieuwe opties weer gegeven om te verfijnen wie in uw directory dit toegangs pakket kan aanvragen.

    ![Toegang tot pakket aanvragen: voor gebruikers in uw Directory](./media/entitlement-management-access-package-request-policy/for-users-in-your-directory.png)

1. Selecteer één van de volgende opties:

    |  | Description |
    | --- | --- |
    | **Specifieke gebruikers en groepen** | Kies deze optie als u wilt dat alleen de gebruikers en groepen in uw directory die u opgeeft, dit toegangs pakket kunnen aanvragen. |
    | **Alle leden (exclusief gasten)** | Kies deze optie als u wilt dat alle gebruikers in uw directory dit toegangs pakket kunnen aanvragen. Deze optie bevat geen gast gebruikers die u mogelijk hebt uitgenodigd voor uw Directory. |
    | **Alle gebruikers (inclusief gasten)** | Selecteer deze optie als u wilt dat alle leden van gebruikers en gast gebruikers in uw directory dit toegangs pakket kunnen aanvragen. |

    Gast gebruikers verwijzen naar externe gebruikers die zijn uitgenodigd voor uw directory met [Azure AD B2B](../external-identities/what-is-b2b.md). Zie [Wat zijn de standaard gebruikers machtigingen in azure Active Directory?](../fundamentals/users-default-permissions.md)voor meer informatie over de verschillen tussen gebruikers van het lid en gast gebruikers.

1. Als u **specifieke gebruikers en groepen** hebt geselecteerd, klikt u op **gebruikers en groepen toevoegen**.

1. Selecteer in het deel venster gebruikers en groepen selecteren de gebruikers en groepen die u wilt toevoegen.

    ![Toegangs pakket-aanvragen-gebruikers en groepen selecteren](./media/entitlement-management-access-package-request-policy/select-users-groups.png)

1. Klik op **selecteren** om de gebruikers en groepen toe te voegen.

1. Als u goed keuring wilt vereisen, gebruikt u de stappen in [goedkeurings instellingen wijzigen voor een toegangs pakket in azure AD-recht beheer](entitlement-management-access-package-approval-policy.md) om goedkeurings instellingen te configureren.

1. Ga naar de sectie [aanvragen inschakelen](#enable-requests) .
 
## <a name="for-users-not-in-your-directory"></a>Voor gebruikers die niet in uw Directory voor komt

 **Gebruikers die zich niet in uw directory** bevinden, verwijzen naar gebruikers in een andere Azure AD-Directory of een ander domein. Deze gebruikers zijn mogelijk nog niet in uw directory uitgenodigd. Azure AD-mappen moeten zodanig worden geconfigureerd dat uitnodigingen in **samenwerkings beperkingen** worden toegestaan. Zie [externe B2B-samen werking inschakelen en beheren wie gasten kan uitnodigen](../external-identities/delegate-invitations.md)voor meer informatie.

> [!NOTE]
> Er wordt een gast gebruikers account voor een gebruiker gemaakt dat nog niet in uw directory is waarvan de aanvraag is goedgekeurd of automatisch goedgekeurd. De gast wordt uitgenodigd, maar ontvangt geen uitnodigings-e-mail. In plaats daarvan ontvangen ze een e-mail bericht wanneer hun toegangs pakket toewijzing wordt bezorgd. Later, wanneer deze gast gebruiker geen toegangs pakket toewijzingen meer heeft, omdat de laatste toewijzing is verlopen of is geannuleerd, wordt het gast gebruikers account geblokkeerd om zich aan te melden en vervolgens te verwijderen. Als u wilt dat gast gebruikers voor onbepaalde tijd in uw Directory blijven, zelfs als ze geen toegangs pakket toewijzingen hebben, kunt u de instellingen voor uw rechten beheer configuratie wijzigen. Zie [Eigenschappen van een Azure Active Directory B2B-samenwerkings gebruiker](../external-identities/user-properties.md)voor meer informatie over het gast gebruikers object.

Volg deze stappen als u wilt toestaan dat gebruikers die geen toegang hebben tot uw directory dit toegangs pakket aanvragen:

1. Klik in de sectie **gebruikers die toegang kunnen aanvragen** , op **voor gebruikers die zich niet in uw directory** bevindt.

    Wanneer u deze optie selecteert, worden er nieuwe opties weer gegeven.

    ![Toegang tot pakket aanvragen: voor gebruikers die niet in uw Directory voor komt](./media/entitlement-management-access-package-request-policy/for-users-not-in-your-directory.png)

1. Selecteer één van de volgende opties:

    |  | Description |
    | --- | --- |
    | **Specifieke verbonden organisaties** | Kies deze optie als u een selectie wilt selecteren in een lijst met organisaties die de beheerder eerder heeft toegevoegd. Alle gebruikers van de geselecteerde organisaties kunnen dit toegangs pakket aanvragen. |
    | **Alle geconfigureerde verbonden organisaties** | Kies deze optie als alle gebruikers van alle geconfigureerde verbonden organisaties dit toegangs pakket kunnen aanvragen. Alleen gebruikers van geconfigureerde verbonden organisaties kunnen toegangs pakketten aanvragen die worden weer gegeven voor gebruikers van alle geconfigureerde organisaties. |
    | **Alle gebruikers (alle verbonden organisaties en alle nieuwe externe gebruikers)** | Kies deze optie als een gebruiker op Internet dit toegangs pakket moet kunnen aanvragen.  Als ze geen deel uitmaken van een verbonden organisatie in uw directory, wordt er automatisch een verbonden organisatie voor hen gemaakt wanneer hij of zij het pakket aanvraagt. De automatisch gemaakte verbonden organisatie krijgt een **voorgestelde** status. Zie [Status eigenschappen van verbonden organisaties](entitlement-management-organization.md#state-properties-of-connected-organizations)voor meer informatie over de voorgestelde status. |

    Een verbonden organisatie is een extern Azure AD-adres lijst of-domein waarmee u een relatie hebt.

1. Als u **specifieke verbonden organisaties** hebt geselecteerd, klikt u op **mappen toevoegen** om te selecteren in een lijst met verbonden organisaties die de beheerder eerder heeft toegevoegd.

1. Typ de naam of de domein naam om te zoeken naar een eerder verbonden organisatie.

    ![Toegangs pakket-aanvragen-mappen selecteren](./media/entitlement-management-access-package-request-policy/select-directories.png)

    Als de organisatie waarmee u wilt samen werken, zich niet in de lijst bevindt, kunt u uw beheerder vragen om deze toe te voegen als een verbonden organisatie. Zie [add a Connected Organization](entitlement-management-organization.md)(Engelstalig) voor meer informatie.

1. Wanneer u alle verbonden organisaties hebt geselecteerd, klikt u op **selecteren**.

    > [!NOTE]
    > Alle gebruikers van de geselecteerde verbonden organisaties kunnen dit toegangs pakket aanvragen. Dit omvat gebruikers in azure AD van alle subdomeinen die zijn gekoppeld aan de organisatie, tenzij deze domeinen worden geblokkeerd door de Azure B2B-lijst voor toestaan of weigeren. Zie [uitnodigingen voor B2B-gebruikers van specifieke organisaties toestaan of blok keren](../external-identities/allow-deny-list.md)voor meer informatie.

1. Als u goed keuring wilt vereisen, gebruikt u de stappen in [goedkeurings instellingen wijzigen voor een toegangs pakket in azure AD-recht beheer](entitlement-management-access-package-approval-policy.md) om goedkeurings instellingen te configureren.
 
1. Ga naar de sectie [aanvragen inschakelen](#enable-requests) .

## <a name="none-administrator-direct-assignments-only"></a>Geen (alleen directe toewijzingen voor beheerders)

Volg deze stappen als u toegangs aanvragen wilt overs Laan en wilt toestaan dat beheerders rechtstreeks specifieke gebruikers aan dit toegangs pakket toewijzen. Gebruikers hoeven het toegangs pakket niet aan te vragen. U kunt nog steeds levenscyclus instellingen instellen, maar er zijn geen aanvraag instellingen.

1. Klik in de sectie **gebruikers die toegang kunnen vragen** , op **geen (alleen beheerders direct toewijzingen**).

    ![Toegang tot pakket-requests-geen alleen directe toewijzingen voor beheerders](./media/entitlement-management-access-package-request-policy/none-admin-direct-assignments-only.png)

    Nadat u het toegangs pakket hebt gemaakt, kunt u rechtstreeks specifieke interne en externe gebruikers aan het toegangs pakket toewijzen. Als u een externe gebruiker opgeeft, wordt er een gast gebruikers account in uw directory gemaakt. Zie [toewijzingen voor een toegangs pakket weer geven, toevoegen en verwijderen](entitlement-management-access-package-assignments.md)voor meer informatie over het rechtstreeks toewijzen van een gebruiker.

1. Ga naar de sectie [aanvragen inschakelen](#enable-requests) .


## <a name="open-and-edit-an-existing-policy-of-request-settings"></a>Een bestaande beleids regel voor aanvraag instellingen openen en bewerken

Als u de aanvraag-en goedkeurings instellingen voor een toegangs pakket wilt wijzigen, moet u het bijbehorende beleid openen. Volg deze stappen om de aanvraag instellingen voor een toegangs pakket te openen en te bewerken:

**Vereiste rol:** globale beheerder, gebruikersbeheerder, cataloguseigenaar of toegangspakketbeheerder

1. Klik in de Azure-portal op **Azure Active Directory** en vervolgens op **Identity Governance**.

1. Klik in het menu links op **toegangs pakketten** en open vervolgens het toegangs pakket.

1. Klik op **beleids regels** en klik vervolgens op het beleid dat u wilt bewerken.

    Het deel venster beleids Details wordt geopend onder aan de pagina.

    ![Toegangs venster pakket-Details van beleid](./media/entitlement-management-shared/policy-details.png)

1. Klik op **bewerken** om het beleid te bewerken.

    ![Toegangs beleid voor pakket bewerken](./media/entitlement-management-shared/policy-edit.png)

1. Klik op het tabblad **aanvragen** om de instellingen voor de aanvraag te openen.

1. Volg de stappen in de vorige secties om de aanvraag instellingen zo nodig te wijzigen.

1. Ga naar de sectie [aanvragen inschakelen](#enable-requests) .

## <a name="enable-requests"></a>Aanvragen inschakelen

1. Als u wilt dat het toegangs pakket onmiddellijk beschikbaar wordt gesteld voor gebruikers in het aanvraag beleid om aan te vragen, verplaatst u de schakel optie inschakelen naar **Ja**.

    U kunt deze altijd in de toekomst inschakelen nadat u klaar bent met het maken van het toegangs pakket.

    Als u geen hebt geselecteerd **(alleen voor beheerders directe toewijzingen)** en u instellen op **Nee** instelt, kunnen beheerders dit toegangs pakket niet rechtstreeks toewijzen.

    ![Toegangs pakket-beleids instelling beleid inschakelen](./media/entitlement-management-access-package-approval-policy/enable-requests.png)

1. Klik op **Volgende**.

1. Als u wilt vereisen dat aanvragers aanvullende informatie verstrekken bij het aanvragen van toegang tot een toegangs pakket, gebruikt u de stappen in de [instellingen voor het goed keuren van wijzigingen en gegevens van de aanvrager (preview-versie) voor een toegangs pakket in azure AD-recht beheer voor het configureren van informatie van de](entitlement-management-access-package-approval-policy.md#collect-additional-requestor-information-for-approval-preview) aanvrager (preview).

1. Levenscyclus instellingen configureren.

1. Als u een beleid bewerkt, klikt u op **bijwerken**. Als u een nieuw beleid toevoegt, klikt u op **maken**.

## <a name="next-steps"></a>Volgende stappen

- [De goedkeurings instellingen voor een toegangs pakket wijzigen](entitlement-management-access-package-approval-policy.md)
- [Levenscyclus instellingen voor een toegangs pakket wijzigen](entitlement-management-access-package-lifecycle-policy.md)
- [Aanvragen voor een toegangs pakket weer geven](entitlement-management-access-package-requests.md)
