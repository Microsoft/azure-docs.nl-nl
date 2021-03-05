---
title: bestand opnemen
description: bestand opnemen
services: active-directory
author: barclayn
ms.service: active-directory
ms.topic: include
ms.date: 12/07/2020
ms.author: barclayn
ms.custom: include file
ms.openlocfilehash: 0b1606e4506e7a1781426632d1f22221f7028b88
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102193897"
---
## <a name="create-one-or-more-access-reviews"></a>Een of meer toegangs beoordelingen maken

1. Klik op **Nieuw** om een nieuwe toegangs beoordeling te maken.

1. Geef de toegangs beoordeling een naam. U kunt eventueel een beschrijving van de beoordeling opgeven. De naam en beschrijving worden weer gegeven aan de controleurs.

    ![Een toegangs beoordeling maken-naam en beschrijving van de beoordeling](./media/active-directory-privileged-identity-management-access-reviews/name-description.png)

1. Stel de **begin datum** in. Een toegangs beoordeling vindt standaard plaats, start de tijd die wordt gemaakt en eindigt in één maand. U kunt de begin-en eind datum wijzigen zodat een toegangs beoordeling in de toekomst wordt gestart en het laatste aantal dagen dat u wilt.

    ![Begin datum, frequentie, duur, einde, aantal keren en eind datum](./media/active-directory-privileged-identity-management-access-reviews/start-end-dates.png)

1. Als u de toegangs beoordeling wilt herhalen, wijzigt u de **frequentie** -instelling van **één keer** in **wekelijks**, **maandelijks**, **per kwar taal**, **jaarlijks** of per **jaar**. Gebruik de schuif regelaar **duur** of het tekstvak om te bepalen hoeveel dagen elke beoordeling van de terugkerende serie wordt geopend voor de invoer van revisors. De maximale duur die u voor een maandelijkse beoordeling kunt instellen is bijvoorbeeld 27 dagen, om overlappende beoordelingen te voor komen.

1. Gebruik de **eind** instelling om op te geven hoe de terugkerende toegangs beoordelings reeks moet worden beëindigd. De reeks kan op drie manieren eindigen: de serie wordt continu uitgevoerd om te beginnen met beoordelingen, tot een bepaalde datum, of nadat een gedefinieerd aantal exemplaren is voltooid. U, een andere gebruikers beheerder of een andere globale beheerder kunnen de serie na het maken stoppen door de datum in de **instellingen** te wijzigen, zodat deze op die datum eindigt.

1. Selecteer in de sectie **gebruikers** een of meer rollen waarvan u het lidmaatschap wilt controleren.

    ![Gebruikers bereik voor revisie van rollidmaatschap van](./media/active-directory-privileged-identity-management-access-reviews/users.png)

    > [!NOTE]
    > - Rollen die hier zijn geselecteerd [, zijn zowel permanente als in aanmerking komende rollen](../articles/active-directory/privileged-identity-management/pim-how-to-add-role-to-user.md).
    > - Als u meerdere rollen selecteert, worden er meerdere toegangs beoordelingen gemaakt. Als u bijvoorbeeld vijf rollen selecteert, worden er vijf afzonderlijke toegangs beoordelingen gemaakt.

    Als u een toegangs beoordeling van **Azure AD-rollen** maakt, ziet u een voor beeld van de lijst lidmaatschap controleren.

    ![Bekijk het deel venster lidmaatschap van de Azure AD-rollen die u kunt selecteren](./media/active-directory-privileged-identity-management-access-reviews/review-membership.png)

    Als u een toegangs beoordeling van Azure- **resource rollen** maakt, ziet u in de volgende afbeelding een voor beeld van de lijst lidmaatschap controleren.

    ![Deel venster lidmaatschap weer geven Azure-resource rollen bekijken die u kunt selecteren](./media/active-directory-privileged-identity-management-access-reviews/review-membership-azure-resource-roles.png)

1. Selecteer in de sectie **controleurs** een of meer personen om alle gebruikers te bekijken. Of u kunt ervoor kiezen om de leden hun eigen toegang te laten beoordelen.

    ![Lijst met revisoren van geselecteerde gebruikers of leden (zelf)](./media/active-directory-privileged-identity-management-access-reviews/reviewers.png)

    - **Geselecteerde gebruikers** : gebruik deze optie als u niet weet wie er toegang moet hebben. Met deze optie kunt u de beoordeling toewijzen aan een resource-eigenaar of groeps Manager om te volt ooien.
    - **Leden (zelf)** : gebruik deze optie om de gebruikers hun eigen roltoewijzingen te laten beoordelen.
    - **Beheerder** : gebruik deze optie om ervoor te hebben dat de Manager van de gebruiker de roltoewijzing controleert. Wanneer u Manager selecteert, hebt u ook de optie om een terugval revisor op te geven. Terugval controleurs wordt gevraagd een gebruiker te controleren wanneer de gebruiker geen beheerder heeft opgegeven in de map.

### <a name="upon-completion-settings"></a>Bij voltooiings instellingen

1. Als u wilt opgeven wat er gebeurt nadat een controle is voltooid, vouwt u de sectie **bij voltooiings instellingen** uit.

    ![Bij voltooiing van instellingen voor automatisch Toep assen en niet reageren](./media/active-directory-privileged-identity-management-access-reviews/upon-completion-settings.png)

1. Als u automatisch de toegang wilt verwijderen voor gebruikers die zijn geweigerd, stelt u **automatisch Toep assen resultaten in op resource** om in te **scha kelen**. Als u de resultaten hand matig wilt Toep assen wanneer de controle is voltooid, stelt u de switch in op **uitschakelen**.

1. Gebruik de lijst als **revisor niet reageert** om op te geven wat er gebeurt voor gebruikers die niet worden gecontroleerd door de revisor binnen de beoordelings periode. Deze instelling heeft geen invloed op gebruikers die hand matig door de controleurs zijn gecontroleerd. Als de laatste beslissing van de revisor weigert, wordt de toegang van de gebruiker verwijderd.

    - **Geen wijziging** -gebruikers toegang ongewijzigd laten
    - **Toegang verwijderen** -de toegang van de gebruiker verwijderen
    - **Toegang goed keuren** : de toegang van de gebruiker goed keuren
    - **Aanbevelingen doen** : de aanbeveling van het systeem voor het weigeren of goed keuren van de permanente toegang van de gebruiker

### <a name="advanced-settings"></a>Geavanceerde instellingen

1. Als u aanvullende instellingen wilt opgeven, vouwt u de sectie **Geavanceerde instellingen** uit.

    ![Geavanceerde instellingen voor weer geven aanbevelingen, reden voor goed keuring, e-mail meldingen en herinneringen vereisen](./media/active-directory-privileged-identity-management-access-reviews/advanced-settings.png)

1. Stel **aanbevelingen weer geven** in om de controleurs **weer te geven** op basis van de toegangs gegevens van de gebruiker.

1. Stel een **reden in voor de goed keuring vereisen** om te **zorgen** dat de revisor een reden voor goed keuring moet opgeven.

1. Stel **e-mail meldingen** in om ervoor te **zorgen** dat Azure AD e-mail meldingen naar revisoren verzendt wanneer een toegangs beoordeling wordt gestart en aan beheerders wanneer een controle is voltooid.

1. Stel **herinneringen** in om ervoor te **zorgen** dat Azure AD herinneringen voor toegangs beoordelingen verzendt naar revisoren die hun beoordeling nog niet hebben voltooid.
1. De inhoud van het e-mail bericht dat naar revisoren wordt verzonden, wordt automatisch gegenereerd op basis van de details van de beoordeling, zoals de naam van de beoordeling, de resource naam, de verval datum, enzovoort. Als u aanvullende informatie wilt communiceren, zoals aanvullende instructies of contact gegevens, kunt u deze gegevens opgeven in de **aanvullende inhoud voor de e-mail van de revisor** , die wordt opgenomen in de uitnodiging en herinneringen die zijn verzonden aan toegewezen revisoren. Deze informatie wordt weer gegeven in de gemarkeerde sectie hieronder.

    ![Inhoud van het e-mail bericht dat wordt verzonden naar revisoren met accenten](./media/active-directory-privileged-identity-management-access-reviews/email-info.png)