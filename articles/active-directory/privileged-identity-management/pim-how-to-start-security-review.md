---
title: Een toegangs beoordeling van Azure AD-rollen maken in PIM-Azure AD | Microsoft Docs
description: Meer informatie over het maken van een toegangs beoordeling van Azure AD-rollen in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 4/05/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2aba8d9de5e068cd98675f67cb26b0eac8d1ad6d
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552819"
---
# <a name="create-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>Een toegangs beoordeling maken van Azure AD-rollen in Privileged Identity Management

Om het risico te verminderen dat is gekoppeld aan verouderde roltoewijzingen, moet u de toegang regel matig controleren. U kunt Azure AD Privileged Identity Management (PIM) gebruiken om toegangs beoordelingen te maken voor beschermde Azure AD-rollen. U kunt ook terugkerende toegangs beoordelingen configureren die automatisch worden uitgevoerd.

In dit artikel wordt beschreven hoe u een of meer toegangs beoordelingen maakt voor beschermde Azure AD-rollen.

## <a name="prerequisite-license"></a>Vereiste licentie

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]. Raadpleeg de [licentie vereisten voor het gebruik van privileged Identity Management](subscription-requirements.md)voor meer informatie over licenties voor Pim.

> [!Note]
>  Op dit moment kunt u een toegangs beoordeling voor service-principals met toegang tot Azure AD en Azure-resource rollen (preview) bereiken met een Azure Active Directory Premium P2-editie actief is in uw Tenant. Het licentie model voor service-principals wordt voltooid voor algemene Beschik baarheid van deze functie. mogelijk zijn er extra licenties nodig.

## <a name="prerequisites"></a>Vereisten

[Beheerder voor bevoorrechte rollen](../roles/permissions-reference.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Toegangs beoordelingen openen

1. Meld u aan bij [Azure Portal](https://portal.azure.com/) met een gebruiker die lid is van de beheerdersrol privileged Role.

1. **Identity governance** selecteren
 
1. Selecteer **Azure AD-rollen** onder **Azure AD privileged Identity Management**.
 
1. Selecteer de **Azure AD-rollen** opnieuw onder **beheren**.

1. Selecteer onder beheren de optie **toegangs beoordelingen** en selecteer vervolgens **Nieuw**.

    ![Azure AD-rollen: lijst met toegangs beoordelingen waarin de status van alle beoordelingen wordt weer gegeven](./media/pim-how-to-start-security-review/access-reviews.png)

Klik op **Nieuw** om een nieuwe toegangs beoordeling te maken.

1. Geef de toegangs beoordeling een naam. U kunt eventueel een beschrijving van de beoordeling opgeven. De naam en beschrijving worden weer gegeven aan de controleurs.

    ![Een toegangs beoordeling maken-naam en beschrijving van de beoordeling](./media/pim-how-to-start-security-review/name-description.png)

1. Stel de **begin datum** in. Een toegangs beoordeling vindt standaard plaats, start de tijd die wordt gemaakt en eindigt in één maand. U kunt de begin-en eind datum wijzigen zodat een toegangs beoordeling in de toekomst wordt gestart en het laatste aantal dagen dat u wilt.

    ![Begin datum, frequentie, duur, einde, aantal keren en eind datum](./media/pim-how-to-start-security-review/start-end-dates.png)

1. Als u de toegangs beoordeling wilt herhalen, wijzigt u de **frequentie** -instelling van **één keer** in **wekelijks**, **maandelijks**, **per kwar taal**, **jaarlijks** of per **jaar**. Gebruik de schuif regelaar **duur** of het tekstvak om te bepalen hoeveel dagen elke beoordeling van de terugkerende serie wordt geopend voor de invoer van revisors. De maximale duur die u voor een maandelijkse beoordeling kunt instellen is bijvoorbeeld 27 dagen, om overlappende beoordelingen te voor komen.

1. Gebruik de **eind** instelling om op te geven hoe de terugkerende toegangs beoordelings reeks moet worden beëindigd. De reeks kan op drie manieren eindigen: de serie wordt continu uitgevoerd om te beginnen met beoordelingen, tot een bepaalde datum, of nadat een gedefinieerd aantal exemplaren is voltooid. U, een andere gebruikers beheerder of een andere globale beheerder kunnen de serie na het maken stoppen door de datum in de **instellingen** te wijzigen, zodat deze op die datum eindigt.

1. Selecteer in de sectie **gebruikers bereik** het bereik van de controle. Als u gebruikers en groepen met toegang tot de rol Azure AD wilt controleren, selecteert u **gebruikers en groepen** of selecteert u **(preview) service-principals** om de computer accounts met toegang tot de Azure AD-rol te controleren.

    ![Gebruikers bereik voor revisie van rollidmaatschap van](./media/pim-how-to-start-security-review/users.png)

1. Onder **lidmaatschap van rol controleren** selecteert u de bevoegde Azure AD-rollen die u wilt controleren. 

    > [!NOTE]
    > - Rollen die hier zijn geselecteerd [, zijn zowel permanente als in aanmerking komende rollen](../privileged-identity-management/pim-how-to-add-role-to-user.md).
    > - Als u meerdere rollen selecteert, worden er meerdere toegangs beoordelingen gemaakt. Als u bijvoorbeeld vijf rollen selecteert, worden er vijf afzonderlijke toegangs beoordelingen gemaakt.
    > - Voor rollen met groepen die aan hen zijn toegewezen, wordt de toegang van elke groep die is gekoppeld aan de rol onder controle beoordeeld als onderdeel van de toegangs beoordeling.
    Als u een toegangs beoordeling van **Azure AD-rollen** maakt, ziet u een voor beeld van de lijst lidmaatschap controleren.

    ![Bekijk het deel venster lidmaatschap van de Azure AD-rollen die u kunt selecteren](./media/pim-how-to-start-security-review/review-membership.png)

    Als u een toegangs beoordeling van Azure- **resource rollen** maakt, ziet u in de volgende afbeelding een voor beeld van de lijst lidmaatschap controleren.

    ![Deel venster lidmaatschap weer geven Azure-resource rollen bekijken die u kunt selecteren](./media/pim-how-to-start-security-review/review-membership-azure-resource-roles.png)

1. Selecteer in de sectie **controleurs** een of meer personen om alle gebruikers te bekijken. Of u kunt ervoor kiezen om de leden hun eigen toegang te laten beoordelen.

    ![Lijst met revisoren van geselecteerde gebruikers of leden (zelf)](./media/pim-how-to-start-security-review/reviewers.png)

    - **Geselecteerde gebruikers** : gebruik deze optie om een specifieke gebruiker aan te wijzen om de beoordeling te volt ooien. Deze optie is beschikbaar ongeacht het bereik van de controle en de geselecteerde revisoren kunnen gebruikers, groepen en service-principals bekijken. 
    - **Leden (zelf)** : gebruik deze optie om de gebruikers hun eigen roltoewijzingen te laten beoordelen. Groepen die zijn toegewezen aan de rol, maken geen deel uit van de beoordeling wanneer deze optie is geselecteerd. Deze optie is alleen beschikbaar als de controle is afgestemd op **gebruikers en groepen**.
    - **Beheerder** : gebruik deze optie om ervoor te hebben dat de Manager van de gebruiker de roltoewijzing controleert. Deze optie is alleen beschikbaar als de controle is afgestemd op **gebruikers en groepen**. Wanneer u Manager selecteert, hebt u ook de optie om een terugval revisor op te geven. Terugval controleurs wordt gevraagd een gebruiker te controleren wanneer de gebruiker geen beheerder heeft opgegeven in de map. Groepen die aan de rol zijn toegewezen, worden door de terugval-revisor gecontroleerd als er een is geselecteerd. 

### <a name="upon-completion-settings"></a>Bij voltooiings instellingen

1. Als u wilt opgeven wat er gebeurt nadat een controle is voltooid, vouwt u de sectie **bij voltooiings instellingen** uit.

    ![Bij voltooiing van instellingen voor automatisch Toep assen en niet reageren](./media/pim-how-to-start-security-review/upon-completion-settings.png)

1. Als u automatisch de toegang wilt verwijderen voor gebruikers die zijn geweigerd, stelt u **automatisch Toep assen resultaten in op resource** om in te **scha kelen**. Als u de resultaten hand matig wilt Toep assen wanneer de controle is voltooid, stelt u de switch in op **uitschakelen**.

1. Gebruik de lijst als **revisor niet reageert** om op te geven wat er gebeurt voor gebruikers die niet worden gecontroleerd door de revisor binnen de beoordelings periode. Deze instelling heeft geen invloed op gebruikers die hand matig door de controleurs zijn gecontroleerd. Als de laatste beslissing van de revisor weigert, wordt de toegang van de gebruiker verwijderd.

    - **Geen wijziging** -gebruikers toegang ongewijzigd laten
    - **Toegang verwijderen** -de toegang van de gebruiker verwijderen
    - **Toegang goed keuren** : de toegang van de gebruiker goed keuren
    - **Aanbevelingen doen** : de aanbeveling van het systeem voor het weigeren of goed keuren van de permanente toegang van de gebruiker

### <a name="advanced-settings"></a>Geavanceerde instellingen

1. Als u aanvullende instellingen wilt opgeven, vouwt u de sectie **Geavanceerde instellingen** uit.

    ![Geavanceerde instellingen voor weer geven aanbevelingen, reden voor goed keuring, e-mail meldingen en herinneringen vereisen](./media/pim-how-to-start-security-review/advanced-settings.png)

1. Stel **aanbevelingen weer geven** in om de controleurs **weer te geven** op basis van de toegangs gegevens van de gebruiker.

1. Stel een **reden in voor de goed keuring vereisen** om te **zorgen** dat de revisor een reden voor goed keuring moet opgeven.

1. Stel **e-mail meldingen** in om ervoor te **zorgen** dat Azure AD e-mail meldingen naar revisoren verzendt wanneer een toegangs beoordeling wordt gestart en aan beheerders wanneer een controle is voltooid.

1. Stel **herinneringen** in om ervoor te **zorgen** dat Azure AD herinneringen voor toegangs beoordelingen verzendt naar revisoren die hun beoordeling nog niet hebben voltooid.
1. De inhoud van het e-mail bericht dat naar revisoren wordt verzonden, wordt automatisch gegenereerd op basis van de details van de beoordeling, zoals de naam van de beoordeling, de resource naam, de verval datum, enzovoort. Als u aanvullende informatie wilt communiceren, zoals aanvullende instructies of contact gegevens, kunt u deze gegevens opgeven in de **aanvullende inhoud voor de e-mail van de revisor** , die wordt opgenomen in de uitnodiging en herinneringen die zijn verzonden aan toegewezen revisoren. Deze informatie wordt weer gegeven in de gemarkeerde sectie hieronder.

    ![Inhoud van het e-mail bericht dat wordt verzonden naar revisoren met accenten](./media/pim-how-to-start-security-review/email-info.png)

## <a name="start-the-access-review"></a>De toegangs beoordeling starten

Nadat u de instellingen voor een toegangs beoordeling hebt opgegeven, selecteert u **starten**. De toegangs beoordeling wordt weer gegeven in de lijst met een indicator van de status.

![Lijst met toegangs beoordelingen waarin de status van de gestarte beoordelingen wordt weer gegeven](./media/pim-how-to-start-security-review/access-reviews-list.png)

Standaard verzendt Azure AD een e-mail naar revisoren kort nadat de controle is gestart. Als u ervoor kiest om Azure AD de e-mail niet te laten verzenden, moet u de revisoren informeren dat een toegangs beoordeling wacht totdat deze is voltooid. U kunt de instructies weer geven voor het controleren van de [toegang tot Azure AD-rollen](pim-how-to-perform-security-review.md).

## <a name="manage-the-access-review"></a>De toegangs beoordeling beheren

U kunt de voortgang volgen zodra de revisoren hun beoordelingen hebben voltooid op de pagina **overzicht** van de toegangs beoordeling. Er worden geen toegangs rechten gewijzigd in de map totdat de [controle is voltooid](pim-how-to-complete-review.md).

![Overzichts pagina toegangs beoordelingen met details van de beoordeling](./media/pim-how-to-start-security-review/access-review-overview.png)

Als dit een eenmalige controle is, volgt u de stappen in een [toegangs beoordeling van Azure AD-rollen](pim-how-to-complete-review.md) om de resultaten te bekijken en toe te passen nadat de toegangs beoordeling is voltooid of de beheerder de toegangs beoordeling heeft gestopt.  

Als u een reeks toegangs beoordelingen wilt beheren, gaat u naar de toegangs beoordeling en gaat u naar de geplande Beoordelingen. vervolgens kunt u de eind datum bewerken of revisoren toevoegen/verwijderen dienovereenkomstig.

Op basis van uw selecties tijdens de **voltooiings instellingen** wordt automatisch Toep assen na de eind datum van de beoordeling of wanneer u de controle hand matig stopt. De status van de beoordeling wordt gewijzigd van **voltooid** met behulp van tussenliggende statussen, zoals **Toep assen** en tot slot op de status **toegepast**. U wordt gewend om geweigerde gebruikers, indien van toepassing, te zien, indien aanwezig, die in een paar minuten worden verwijderd uit rollen.

## <a name="next-steps"></a>Volgende stappen

- [Toegang tot Azure AD-rollen controleren](pim-how-to-perform-security-review.md)
- [Een toegangs beoordeling van Azure AD-rollen volt ooien](pim-how-to-complete-review.md)
- [Een toegangs beoordeling van Azure-resource rollen maken](pim-resource-roles-start-access-review.md)
