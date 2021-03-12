---
title: Mijn personeel gebruiken om gebruikers beheer te delegeren-Azure AD | Microsoft Docs
description: Gebruikers beheer delegeren met mijn personeel en administratieve eenheden
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.topic: how-to
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.date: 03/11/2021
ms.author: rolyon
ms.reviewer: sahenry
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 1a380c8a3d766c3c11d8cba1148383d924f65a1b
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103224993"
---
# <a name="manage-your-users-with-my-staff"></a>Uw gebruikers beheren met mijn personeel

Met mijn personeel kunt u machtigingen overdragen aan een instantie van de autoriteit, zoals een winkel manager of een team lead, om ervoor te zorgen dat hun mede werkers toegang hebben tot hun Azure AD-accounts. In plaats van te vertrouwen op een Central-Help Desk kunnen organisaties algemene taken, zoals het opnieuw instellen van wacht woorden of het wijzigen van telefoon nummers, delegeren aan een lokale team beheerder. Met mijn mede werkers kunnen gebruikers die geen toegang hebben tot hun account, toegang krijgen tot slechts een paar klikken, zonder helpdesk medewerkers of IT-personeel.

Voordat u mijn personeel voor uw organisatie configureert, wordt u aangeraden deze documentatie en de [gebruikers documentatie](../user-help/my-staff-team-manager.md) te raadplegen om te zien hoe het werkt en hoe deze van invloed is op uw gebruikers. U kunt gebruikmaken van de gebruikers documentatie om uw gebruikers te trainen en voor te bereiden voor de nieuwe ervaring en om te zorgen voor een geslaagde implementatie.

## <a name="how-my-staff-works"></a>Hoe mijn personeel werkt

Mijn personeel is gebaseerd op administratieve eenheden, die een container zijn van resources die kunnen worden gebruikt om het bereik van de beheer controle van een roltoewijzing te beperken. Zie beheer [eenheden beheren in azure Active Directory](administrative-units.md)voor meer informatie. In mijn personeel kunnen beheerders eenheden worden gebruikt voor het opnemen van een groep gebruikers in een winkel of afdeling. Team Manager kan vervolgens worden toegewezen aan een administratieve rol in een bereik van een of meer eenheden.

## <a name="before-you-begin"></a>Voordat u begint

U hebt de volgende resources en bevoegdheden nodig om dit artikel te volt ooien:

* Een actief Azure-abonnement.

  * Als u nog geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een Azure Active Directory-Tenant die aan uw abonnement is gekoppeld.

  * [Maak zo nodig een Azure Active Directory-tenant](../fundamentals/sign-up-organization.md) of [koppel een Azure-abonnement aan uw account](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
* U hebt *globale beheerders* bevoegdheden nodig in uw Azure AD-Tenant om verificatie op basis van SMS in te scha kelen.
* Elke gebruiker die is ingeschakeld in het beleid voor de verificatie methode voor tekst berichten moet een licentie hebben, zelfs als ze deze niet gebruiken. Elke ingeschakelde gebruiker moet een van de volgende Azure AD-of Microsoft 365-licenties hebben:

  * [Azure AD Premium P1 of P2](https://azure.microsoft.com/pricing/details/active-directory/)
  * [Microsoft 365 (M365) F1 of F3](https://www.microsoft.com/licensing/news/m365-firstline-workers)
  * [Enterprise Mobility + Security (EMS) E3 of E5](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing) of [Microsoft 365 (M365) E3 of E5](https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans)

## <a name="how-to-enable-my-staff"></a>Mijn personeel inschakelen

Zodra u beheer eenheden hebt geconfigureerd, kunt u dit bereik Toep assen op uw gebruikers die toegang hebben tot mijn personeel. Alleen gebruikers aan wie een beheerdersrol is toegewezen, hebben toegang tot mijn personeel. Als u mijn personeel wilt inschakelen, voert u de volgende stappen uit:

1. Meld u aan bij de Azure Portal als een gebruikers beheerder.
2. Blader naar **Azure Active Directory** gebruikers  >  **instellingen**  >  **previews**  >  **voor de preview-functie van gebruikers onderdelen beheren**.
3. Onder **beheerders hebben toegang tot mijn personeel**. u kunt ervoor kiezen om in te scha kelen voor alle gebruikers, geselecteerde gebruikers of geen gebruikers toegang.

> [!Note]
> Alleen gebruikers aan wie een beheerdersrol is toegewezen, hebben toegang tot mijn personeel. Als u mijn personeel inschakelt voor een gebruiker aan wie geen beheerdersrol is toegewezen, hebben ze geen toegang tot mijn personeel.

## <a name="conditional-access"></a>Voorwaardelijke toegang

U kunt de portal mijn personeel beveiligen met behulp van het beleid voor voorwaardelijke toegang van Azure AD. Gebruik het voor taken zoals het vereisen van multi-factor Authentication voordat ze toegang krijgen tot mijn personeel.

We raden u ten zeerste aan om mijn personeel te beschermen met het [beleid voor voorwaardelijke toegang van Azure AD](../conditional-access/index.yml). Als u een beleid voor voorwaardelijke toegang wilt Toep assen op mijn personeel, moet u de site mijn personeel eerst één keer bezoeken om de Service-Principal in uw Tenant automatisch in te richten op gebruik door voorwaardelijke toegang.

U ziet de service-principal wanneer u een beleid voor voorwaardelijke toegang maakt dat van toepassing is op de Cloud toepassing mijn personeel.

![Een beleid voor voorwaardelijke toegang maken voor de app mijn personeel](./media/my-staff-configure/conditional-access.png)

## <a name="using-my-staff"></a>Mijn personeel gebruiken

Wanneer een gebruiker naar mijn personeel gaat, worden de namen weer gegeven van de [beheer eenheden](administrative-units.md) waarvoor ze beheerders machtigingen hebben. In de [gebruikers documentatie van mijn personeel](../user-help/my-staff-team-manager.md)gebruiken we de term ' locatie ' om te verwijzen naar beheer eenheden. Als de machtigingen van een beheerder geen beheer eenheids bereik hebben, gelden de machtigingen voor de hele organisatie. Nadat mijn mede werkers zijn ingeschakeld, hebben de gebruikers die zijn ingeschakeld en aan wie een beheerdersrol is toegewezen, toegang tot het netwerk via [https://mystaff.microsoft.com](https://mystaff.microsoft.com) . Ze kunnen een administratieve eenheid selecteren om de gebruikers in die eenheid weer te geven en een gebruiker selecteren om zijn of haar profiel te openen.

## <a name="reset-a-users-password"></a>Het wachtwoord van een gebruiker opnieuw instellen

Voordat u wacht woorden voor on-premises gebruikers kunt rusten, moet u voldoen aan de volgende voor waarden. Zie zelf studie voor het [opnieuw instellen van wacht woorden inschakelen](../authentication/tutorial-enable-sspr-writeback.md) voor gedetailleerde instructies.

* Machtigingen voor het terugschrijven van wacht woorden configureren
* Wachtwoord terugschrijven inschakelen in Azure AD Connect
* Wacht woord terugschrijven inschakelen in azure AD selfservice voor wachtwoord herstel (SSPR)

De volgende rollen zijn gemachtigd om het wacht woord van een gebruiker opnieuw in te stellen:

* [Verificatie beheerder](permissions-reference.md#authentication-administrator)
* [Beheerder voor geprivilegieerde authenticatie](permissions-reference.md#privileged-authentication-administrator)
* [Globale beheerder](permissions-reference.md#global-administrator)
* [Helpdesk beheerder](permissions-reference.md#helpdesk-administrator)
* [Gebruikersbeheerder](permissions-reference.md#user-administrator)
* [Wachtwoordbeheerder](permissions-reference.md#password-administrator)

Open vanuit **mijn personeel** het profiel van een gebruiker. Selecteer **wacht woord opnieuw instellen**.

* Als de gebruiker alleen in de Cloud staat, kunt u een tijdelijk wacht woord zien dat u aan de gebruiker kunt geven.
* Als de gebruiker is gesynchroniseerd vanaf de on-premises Active Directory, kunt u een wacht woord invoeren dat voldoet aan uw on-premises AD-beleid. U kunt dat wacht woord vervolgens aan de gebruiker geven.

    ![Voortgangs indicator voor wachtwoord herstel en geslaagde melding](./media/my-staff-configure/reset-password.png)

De gebruiker moet het wacht woord wijzigen wanneer ze zich de volgende keer aanmelden.

## <a name="manage-a-phone-number"></a>Een telefoon nummer beheren

Open vanuit **mijn personeel** het profiel van een gebruiker.

* Selecteer de sectie **telefoon nummer toevoegen** om een telefoon nummer voor de gebruiker toe te voegen
* Selecteer **telefoon nummer bewerken** om het telefoon nummer te wijzigen
* Selecteer **telefoon nummer verwijderen** om het telefoon nummer voor de gebruiker te verwijderen

Afhankelijk van uw instellingen kan de gebruiker vervolgens het telefoon nummer gebruiken dat u instelt om u aan te melden bij SMS, multi-factor Authentication uit te voeren en self-service voor wachtwoord herstel uit te voeren.

Als u het telefoon nummer van een gebruiker wilt beheren, moet u een van de volgende rollen toewijzen:

* [Verificatie beheerder](permissions-reference.md#authentication-administrator)
* [Beheerder voor geprivilegieerde authenticatie](permissions-reference.md#privileged-authentication-administrator)
* [Globale beheerder](permissions-reference.md#global-administrator)

## <a name="search"></a>Zoeken

U kunt zoeken naar beheer eenheden en gebruikers in uw organisatie met behulp van de zoek balk in mijn mede werkers. U kunt zoeken in alle beheer eenheden en gebruikers in uw organisatie, maar u kunt alleen wijzigingen aanbrengen aan gebruikers die deel uitmaken van een administratieve eenheid waarvoor u beheerders machtigingen hebt.

U kunt ook zoeken naar een gebruiker binnen een administratieve eenheid. U doet dit door de zoek balk boven aan de gebruikers lijst te gebruiken.

## <a name="audit-logs"></a>Auditlogboeken

U kunt audit logboeken bekijken voor acties die in mijn personeel in de Azure Active Directory Portal worden uitgevoerd. Als er een controle logboek is gegenereerd door een actie die is ondernomen in mijn personeel, wordt dit aangegeven onder aanvullende DETAILS in de controle gebeurtenis.

## <a name="next-steps"></a>Volgende stappen

Documentatie voor gebruikers van [mijn personeel](../user-help/my-staff-team-manager.md) 
 [Documentatie over administratieve eenheden](administrative-units.md)
