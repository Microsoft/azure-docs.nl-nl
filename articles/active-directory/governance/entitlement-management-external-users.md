---
title: De toegang tot externe gebruikers beheren in azure AD-recht beheer-Azure Active Directory
description: Meer informatie over de instellingen die u kunt opgeven om toegang te bepalen voor externe gebruikers in Azure Active Directory rechten beheer.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0fae7a62f062478c5fee45d172b88d0132f3a8f8
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96546128"
---
# <a name="govern-access-for-external-users-in-azure-ad-entitlement-management"></a>De toegang voor externe gebruikers bepalen in Rechtenbeheer van Azure AD

Het Azure AD-rechts beheer maakt gebruik van [Azure AD Business-to-Business (B2B)](../external-identities/what-is-b2b.md) om samen te werken met mensen buiten uw organisatie in een andere adres lijst. Met Azure AD B2B verifiëren externe gebruikers via hun thuismap, maar hebben ze een account in uw map. Met dat account in uw map kan de gebruiker toegang toegewezen krijgen tot uw resources.

In dit artikel worden de instellingen beschreven die u kunt opgeven om de toegang voor externe gebruikers te bepalen.

## <a name="how-entitlement-management-can-help"></a>Hoe rechten beheer kan helpen

Wanneer u de [Azure AD B2B](../external-identities/what-is-b2b.md) -uitnodiging gebruikt, moet u de e-mail adressen van de externe gast gebruikers die u wilt opnemen in uw Resource Directory, al kennen en ermee werken. Dit werkt prima wanneer u aan een kleiner of kort project werkt en u al alle deel nemers weet, maar dit is moeilijker te beheren als u veel gebruikers hebt waarmee u wilt werken of als de deel nemers de tijd veranderen.  U kunt bijvoorbeeld samen werken met een andere organisatie en één contact punt hebben met die organisatie, maar in de tijd moeten extra gebruikers van die organisatie ook toegang hebben.

Met het rechten beheer kunt u een beleid definiëren waarmee gebruikers van organisaties die u opgeeft, zelf een toegangs pakket kunnen aanvragen. U kunt opgeven of goed keuring vereist is en een verval datum voor de toegang. Als goed keuring is vereist, kunt u ook een of meer gebruikers van de externe organisatie uitnodigen voor uw directory en deze toewijzen als fiatteurs, omdat ze waarschijnlijk weten welke externe gebruikers van hun organisatie toegang nodig hebben. Zodra u het toegangs pakket hebt geconfigureerd, kunt u de koppeling van het toegangs pakket verzenden naar uw contact persoon (sponsor) bij de externe organisatie. Deze contact persoon kan delen met andere gebruikers in de externe organisatie en ze kunnen deze koppeling gebruiken om het toegangs pakket aan te vragen. Gebruikers van die organisatie die al zijn uitgenodigd voor uw directory, kunnen ook deze koppeling gebruiken.

Wanneer een aanvraag wordt goedgekeurd, wordt de gebruiker door het beheer van rechten voorzien van de benodigde toegang, waarbij de gebruiker kan worden uitgenodigd als deze zich nog niet in uw directory bevindt. Er wordt door Azure AD automatisch een B2B-gast account gemaakt. Houd er rekening mee dat een beheerder eerder is beperkt welke organisaties zijn toegestaan voor samen werking, door een [B2B-of Deny-lijst](../external-identities/allow-deny-list.md) in te stellen voor het toestaan of blok keren van uitnodigingen voor andere organisaties.  Als de gebruiker niet is toegestaan door de lijst met toegestane of geblokkeerde websites, worden ze niet meer uitgenodigd.

Omdat u niet wilt dat de toegang van de externe gebruiker voor het laatst permanent is, geeft u een verval datum op in het beleid, zoals 180 dagen. Na 180 dagen geldt dat voor het beheer van rechten alle toegang die is gekoppeld aan dat toegangs pakket wordt verwijderd als de toegang niet is uitgebreid. Standaard, als de gebruiker die is uitgenodigd via het rechten beheer geen andere toegangs pakket toewijzingen heeft, en wanneer ze de laatste toewijzing verliezen, wordt het gast account geblokkeerd om 30 dagen aan te melden en vervolgens verwijderd. Hiermee wordt voor komen dat overbodige accounts worden geproliferatieerd. Zoals beschreven in de volgende secties kunnen deze instellingen worden geconfigureerd.

## <a name="how-access-works-for-external-users"></a>Hoe Access werkt voor externe gebruikers

Het volgende diagram en de stappen bieden een overzicht van hoe externe gebruikers toegang krijgen tot een toegangs pakket.

![Diagram van de levens cyclus van externe gebruikers](./media/entitlement-management-external-users/external-users-lifecycle.png)

1. U [voegt een verbonden organisatie toe](entitlement-management-organization.md) voor de Azure AD-Directory of het domein waarmee u wilt samen werken.

1. U maakt een toegangs pakket in uw directory met een beleid [voor gebruikers die niet in uw directory](entitlement-management-access-package-create.md#for-users-not-in-your-directory)zijn opgenomen.

1. U stuurt een [koppeling naar mijn Access-Portal](entitlement-management-access-package-settings.md) naar uw contact persoon op de externe organisatie die ze kunnen delen met hun gebruikers om het toegangs pakket aan te vragen.

1. Een externe gebruiker (**aanvrager A** in dit voor beeld) gebruikt de Portal-koppeling van mijn toegang om toegang tot het toegangs pakket aan te [vragen](entitlement-management-request-access.md) . De manier waarop de gebruiker zich aanmeldt, is afhankelijk van het verificatie type van de Directory of het domein dat in de verbonden organisatie is gedefinieerd.

1. Een fiatteur [keurt de aanvraag goed](entitlement-management-request-approve.md) (of de aanvraag wordt automatisch goedgekeurd).

1. De aanvraag wordt verzonden naar de [leverings status](entitlement-management-process.md).

1. Met het B2B-invite-proces wordt een gast gebruikers account gemaakt in uw directory (**aanvrager a (gast)** in dit voor beeld). Als er een [lijst met toegestane of een geweigerde](../external-identities/allow-deny-list.md) lijst is gedefinieerd, wordt de lijst instelling toegepast.

1. De gast gebruiker krijgt toegang tot alle resources in het toegangs pakket. Het kan enige tijd duren voordat wijzigingen zijn aangebracht in azure AD en aan andere online services van micro soft of verbonden SaaS-toepassingen. Zie [wanneer wijzigingen worden toegepast](entitlement-management-access-package-resources.md#when-changes-are-applied)voor meer informatie.

1. De externe gebruiker ontvangt een e-mail met de melding dat de toegang is [bezorgd](entitlement-management-process.md).

1. De externe gebruiker kan toegang krijgen tot de resources door te klikken op de koppeling in het e-mail bericht of door rechtstreeks toegang te krijgen tot een van de Directory bronnen om het uitnodigings proces te volt ooien.

1. Afhankelijk van de beleids instellingen worden de toegangs pakket toewijzing voor de externe gebruiker verloopt, en wordt de toegang tot de externe gebruiker verwijderd.

1. Afhankelijk van de levens cyclus van externe gebruikers instellingen, is de externe gebruiker niet langer gemachtigd om zich aan te melden en wordt het gast gebruikers account uit uw directory verwijderd wanneer de externe gebruiker geen toegangs pakket toewijzingen meer heeft.

## <a name="settings-for-external-users"></a>Instellingen voor externe gebruikers

Om ervoor te zorgen dat personen buiten uw organisatie toegangs pakketten kunnen aanvragen en toegang krijgen tot de resources in deze toegangs pakketten, zijn er enkele instellingen die u moet controleren.

### <a name="enable-catalog-for-external-users"></a>Catalogus inschakelen voor externe gebruikers

- Wanneer u een [nieuwe catalogus](entitlement-management-catalog-create.md)maakt, is deze standaard ingeschakeld zodat externe gebruikers toegangs pakketten kunnen aanvragen in de catalogus. Zorg ervoor dat **ingeschakeld voor externe gebruikers** is ingesteld op **Ja**.

    ![Catalogus instellingen bewerken](./media/entitlement-management-shared/catalog-edit.png)

### <a name="configure-your-azure-ad-b2b-external-collaboration-settings"></a>Uw externe samenwerkings instellingen voor Azure AD B2B configureren

- Als gasten toestaan andere gasten uit te nodigen voor uw directory, betekent dit dat bezoekers uitnodigen buiten het rechten beheer kunnen plaatsvinden. Het wordt aangeraden **gasten** in te stellen op **Nee** om alleen goed onderhevige uitnodigingen toe te staan.
- Als u de lijst B2B allow gebruikt, moet u ervoor zorgen dat elk domein dat u wilt partner met behulp van rechten beheer, aan de lijst wordt toegevoegd. Als u de lijst voor het weigeren van B2B gebruikt, moet u er ook voor zorgen dat elk domein waarmee u een partner wilt maken, niet aan de lijst wordt toegevoegd.
- Als u een recht beleid maakt voor **alle gebruikers** (alle verbonden organisaties en alle nieuwe externe gebruikers) en een gebruiker behoort niet tot een verbonden organisatie in uw directory, wordt er automatisch een verbonden organisatie voor gemaakt wanneer hij of zij het pakket aanvraagt. Alle instellingen voor het toestaan of weigeren van een B2B-lijst hebben prioriteit. Zorg er daarom voor dat u de domeinen die u wilt opnemen in dit beleid, opneemt in de lijst met toegestane gebruikers als u er een gebruikt en deze uitsluit van de lijst weigeren als u een lijst voor weigeren gebruikt.
- Als u een rechten beheer beleid wilt maken dat **alle gebruikers** (alle verbonden organisaties en alle nieuwe externe gebruikers) bevat, moet u eerst de verificatie van eenmalige e-mail wachtwoord code voor uw Directory inschakelen. Zie voor meer informatie [verificatie via e-mail eenmalige wachtwoord code (preview-versie)](../external-identities/one-time-passcode.md).
- Zie voor meer informatie over externe samenwerkings instellingen van Azure AD B2B externe [samen werking inschakelen en beheren wie gasten kan uitnodigen](../external-identities/delegate-invitations.md).

    ![Externe samenwerkings instellingen voor Azure AD](./media/entitlement-management-external-users/collaboration-settings.png)

### <a name="review-your-conditional-access-policies"></a>Uw beleid voor voorwaardelijke toegang controleren

- Zorg ervoor dat gasten worden uitgesloten van beleids regels voor voorwaardelijke toegang die nieuwe gast gebruikers niet kunnen bereiken omdat hiermee wordt voor komen dat ze zich kunnen aanmelden bij uw Directory. Gasten hebben bijvoorbeeld waarschijnlijk geen geregistreerd apparaat, bevinden zich niet op een bekende locatie en willen ze niet opnieuw registreren voor multi-factor Authentication (MFA), dus als u deze vereisten toevoegt in een beleid voor voorwaardelijke toegang, worden gasten geblokkeerd voor het gebruik van rechten beheer. Zie [Wat zijn voor waarden in azure Active Directory voorwaardelijke toegang?](../conditional-access/concept-conditional-access-conditions.md)voor meer informatie.

    ![Instellingen voor het beleid voor voorwaardelijke toegang van Azure AD](./media/entitlement-management-external-users/conditional-access-exclude.png)

### <a name="review-your-sharepoint-online-external-sharing-settings"></a>De instellingen voor extern delen van share point online controleren

- Als u share point online-sites wilt toevoegen aan uw toegangs pakketten voor externe gebruikers, moet u ervoor zorgen dat de instelling voor extern delen op organisatie niveau is ingesteld op **iedereen** (gebruikers hoeven zich niet aan te melden) of **nieuwe en bestaande gasten** (gasten moeten zich aanmelden of een verificatie code opgeven). Zie [extern delen in-of uitschakelen](/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting)voor meer informatie.

- Als u extern delen buiten het beheer van rechten wilt beperken, kunt u de instelling voor extern delen instellen op **bestaande gasten**. Alleen nieuwe gebruikers die via rechten beheer worden uitgenodigd, kunnen toegang krijgen tot deze sites. Zie [extern delen in-of uitschakelen](/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting)voor meer informatie.

- Zorg ervoor dat de instellingen op site niveau gast toegang inschakelen (dezelfde optie selecties als eerder vermeld). Zie voor meer informatie [extern delen in-of uitschakelen voor een site](/sharepoint/change-external-sharing-site).

### <a name="review-your-microsoft-365-group-sharing-settings"></a>De instellingen voor het delen van Microsoft 365 groepen controleren

- Als u Microsoft 365 groepen in uw toegangs pakketten wilt toevoegen voor externe gebruikers, moet u ervoor zorgen dat de **gebruikers nieuwe gasten toevoegen aan de organisatie** zijn ingesteld **op aan om toegang** tot de gast toe te staan. Zie voor meer informatie [gast toegang beheren voor Microsoft 365 groepen](/Microsoft 365/admin/create-groups/manage-guest-access-in-groups?view=Microsoft 365-worldwide#manage-groups-guest-access).

- Als u wilt dat externe gebruikers toegang hebben tot de share point online-site en de resources die zijn gekoppeld aan een Microsoft 365 groep, moet u het extern delen van share point Online inschakelen. Zie [extern delen in-of uitschakelen](/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting)voor meer informatie.

- Zie voor meer informatie over het instellen van het gast beleid voor Microsoft 365 groepen op het niveau van de Directory in Power shell, [voor beeld: Configureer gast beleid voor groepen op het niveau van de Directory](../enterprise-users/groups-settings-cmdlets.md#example-configure-guest-policy-for-groups-at-the-directory-level).

### <a name="review-your-teams-sharing-settings"></a>De instellingen voor het delen van teams controleren

- Als u teams wilt toevoegen aan uw toegangs pakketten voor externe gebruikers, moet u ervoor zorgen dat de **toegang gast toestaan in micro soft teams** is **ingesteld op aan om gast** toegang toe te staan. Zie voor meer informatie [gast toegang configureren in het micro soft teams-beheer centrum](/microsoftteams/set-up-guests#configure-guest-access-in-the-teams-admin-center).

## <a name="manage-the-lifecycle-of-external-users"></a>De levens cyclus van externe gebruikers beheren

U kunt selecteren wat er gebeurt wanneer een externe gebruiker, die is uitgenodigd voor uw directory via een aanvraag voor een toegangs pakket dat wordt goedgekeurd, geen toegangs pakket toewijzingen meer heeft. Dit kan gebeuren als de gebruiker alle toewijzingen van het toegangs pakket relinquishes of de toewijzing van het laatste toegangs pakket verloopt. Wanneer een externe gebruiker geen toegangs pakket toewijzingen meer heeft, is het standaard geblokkeerd om zich aan te melden bij uw Directory. Na 30 dagen wordt het gast gebruikers account verwijderd uit de map.

**Vereiste rol:** bedrijfsbeheerder of gebruikersbeheerder

1. Klik in de Azure-portal op **Azure Active Directory** en vervolgens op **Identity Governance**.

1. Klik in het menu links in het gedeelte **beheer rechten** op **instellingen**.

1. Klik op **Bewerken**.

    ![Instellingen voor het beheren van de levens cyclus van externe gebruikers](./media/entitlement-management-external-users/settings-external-users.png)

1. Selecteer in de sectie **de levens cyclus van externe gebruikers beheren** de verschillende instellingen voor externe gebruikers.

1. Als een externe gebruiker de laatste toewijzing van toegangs pakketten kwijtraakt en u wilt voor komen dat ze zich aanmelden bij deze map, stelt u in dat de **externe gebruiker zich niet kan aanmelden bij deze map** op **Ja**.

    > [!NOTE]
    > Als een gebruiker zich niet kan aanmelden bij deze map, dan kan de gebruiker het toegangs pakket niet opnieuw aanvragen of extra toegang aanvragen in deze map. Niet configureren dat ze worden aangemeld als ze later toegang moeten aanvragen tot andere toegangs pakketten.

1. Als een externe gebruiker de laatste toewijzing van toegangs pakketten kwijtraakt en u de gast gebruikers account in deze map wilt verwijderen, stelt u **externe gebruiker verwijderen** in op **Ja**.

    > [!NOTE]
    > Met het rechten beheer worden alleen accounts verwijderd die zijn uitgenodigd via het rechten beheer. Houd er ook rekening mee dat een gebruiker wordt geblokkeerd voor het aanmelden en verwijderen van deze map, zelfs als die gebruiker is toegevoegd aan bronnen in deze map die geen toegang tot pakket toewijzingen hebben. Als de gast aanwezig was in deze map voordat er toegangs pakket toewijzingen worden ontvangen, blijven ze behouden. Als de gast echter is uitgenodigd via een toewijzing van een toegangs pakket en als is uitgenodigd ook is toegewezen aan een OneDrive voor bedrijven-of share point online-site, worden ze nog steeds verwijderd.

1. Als u het gast gebruikers account in deze map wilt verwijderen, kunt u het aantal dagen instellen waarna het wordt verwijderd. Als u het gast gebruikers account wilt verwijderen zodra de laatste toewijzing aan toegangs pakketten is verloren, stelt u het **aantal dagen in voordat u de externe gebruiker uit deze map verwijdert** in **0**.

1. Klik op **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

- [Een verbonden organisatie toevoegen](entitlement-management-organization.md)
- [Voor gebruikers die niet in uw Directory voor komt](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
- [Problemen oplossen](entitlement-management-troubleshoot.md)