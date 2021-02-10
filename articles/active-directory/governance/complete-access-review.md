---
title: Een toegangs beoordeling van groepen & toepassingen volt ooien-Azure AD
description: Meer informatie over het volt ooien van een toegangs beoordeling van groeps leden of toegang tot toepassingen in Azure Active Directory toegangs Beoordelingen.
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 02/08/2021
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f1abbabb9197011b826e58d518ddff4364edab7
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/09/2021
ms.locfileid: "100008196"
---
# <a name="complete-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Een toegangs beoordeling van groepen en toepassingen in azure AD-toegangs beoordelingen volt ooien

Als beheerder [maakt u een toegangs beoordeling van groepen of toepassingen](create-access-review.md) en revisoren die [de toegangs beoordeling uitvoeren](perform-access-review.md). In dit artikel wordt beschreven hoe u de resultaten van de toegangs beoordeling kunt bekijken en de resultaten kunt Toep assen.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>Vereisten

- Azure AD Premium P2
- Globale beheerder, gebruikers beheerder, beveiligings beheerder of beveiligings lezer

Zie [Licentievereisten](access-reviews-overview.md#license-requirements) voor meer informatie.

## <a name="view-an-access-review"></a>Een toegangs beoordeling weer geven

U kunt de voortgang volgen wanneer de revisoren hun beoordelingen hebben voltooid.

1. Meld u aan bij de Azure Portal en open de [pagina voor identiteits](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)beheer.

1. Klik in het menu links op **toegangs beoordelingen**.

1. Klik in de lijst op een toegangs beoordeling.

    Als u toekomstige exemplaren van een toegangs beoordeling wilt weer geven, gaat u naar de toegangs beoordeling en selecteert u geplande Beoordelingen.

    Op de pagina **overzicht** ziet u de voortgang van het huidige exemplaar. Er worden geen toegangs rechten gewijzigd in de map totdat de controle is voltooid.

     ![Beoordeling van alle bedrijfs groepen](./media/complete-access-review/all-company-group.png)

    Alle Blades onder actueel zijn alleen zichtbaar tijdens de duur van elk controle-exemplaar. 

    Op de pagina resultaten vindt u meer informatie over elke gebruiker onder controle in het exemplaar, inclusief de mogelijkheid om de resultaten te stoppen, opnieuw in te stellen en te downloaden.

    ![Gast toegang over Microsoft 365 groepen controleren](./media/complete-access-review/all-company-group-results.png)


    Als u een toegangs beoordeling bekijkt die de gast toegang bekijkt in Microsoft 365 groepen (preview), wordt in de Blade overzicht elke groep in de beoordeling weer gegeven. 
   
    ![gast toegang over Microsoft 365 groepen controleren](./media/complete-access-review/review-guest-access-across-365-groups.png)

    Klik op een groep om de voortgang van de revisie voor die groep te bekijken en te stoppen, opnieuw instellen, Toep assen en verwijderen.

   ![de toegang tot de gast over Microsoft 365 groepen gedetailleerd bekijken](./media/complete-access-review/progress-group-review.png)

1. Als u een toegangs beoordeling wilt stoppen voordat de geplande eind datum is bereikt, klikt u op de knop **stoppen** .

    Wanneer u een beoordeling stopt, kunnen revisoren geen reacties meer geven. U kunt een controle niet opnieuw starten nadat deze is gestopt.

1. Als u niet langer geïnteresseerd bent in de toegangs beoordeling, kunt u deze verwijderen door te klikken op de knop **verwijderen** .

## <a name="apply-the-changes"></a>De wijzigingen Toep assen

Als **automatisch Toep assen van resultaten op resource** is ingeschakeld op basis van uw selecties tijdens de **voltooiings instellingen**, wordt automatisch Toep assen na de eind datum van de beoordeling of wanneer u de controle hand matig stopt.

Als **automatisch Toep assen van resultaten op resource** niet is ingeschakeld voor de controle, navigeert u naar de **revisie geschiedenis** onder **reeks** na de beëindiging van de beoordelings periode of is de controle vroegtijdig gestopt en klikt u op het exemplaar van de controle die u wilt Toep assen.

![Wijzigingen in toegangs beoordeling Toep assen](./media/complete-access-review/apply-changes.png)

Klik op **Toep assen** om de wijzigingen hand matig toe te passen. Als de toegang van een gebruiker bij de controle is geweigerd, worden de lidmaatschaps-of toepassings toewijzing van Azure AD verwijderd als u op **Toep assen** klikt.

![Knop wijzigingen van toegangs beoordeling Toep assen](./media/complete-access-review/apply-changes-button.png)


De status van de beoordeling wordt gewijzigd van **voltooid** met behulp van tussenliggende statussen, zoals **Toep assen** en tot slot op status **toegepast resultaat**. U wordt gewend om geweigerde gebruikers te zien, indien aanwezig, die in een paar minuten worden verwijderd uit het groepslid maatschap of de toepassings toewijzing.

Het hand matig of automatisch Toep assen van resultaten heeft geen invloed op een groep die afkomstig is uit een on-premises Directory of een dynamische groep. Als u een groep wilt wijzigen die on-premises is, downloadt u de resultaten en past u deze wijzigingen toe op de weer gave van de groep in die map.

## <a name="retrieve-the-results"></a>De resultaten ophalen

Als u de resultaten voor een eenmalige toegangs beoordeling wilt weer geven, klikt u op de pagina **resultaten** . Als u alleen de toegang van een gebruiker wilt weer geven, typt u in het zoekvak de weergave naam of user principal name van een gebruiker van wie de toegang is gecontroleerd.

![Resultaten ophalen voor een toegangs beoordeling](./media/complete-access-review/retrieve-results.png) 

Als u de voortgang van een terugkerende actieve toegangs beoordeling wilt weer geven, klikt u op de pagina **resultaten** .

Als u de resultaten wilt weer geven van een voltooid exemplaar van een terugkerende toegangs beoordeling, klikt u op **controle geschiedenis** en selecteert u vervolgens de specifieke instantie in de lijst met voltooide instanties voor toegangs controle, op basis van de begin-en eind datum van het exemplaar. U kunt de resultaten van deze instantie verkrijgen op de pagina met **resultaten** .

Als u alle resultaten van een toegangs beoordeling wilt ophalen, klikt u op de knop **downloaden** . Het resulterende CSV-bestand kan worden weer gegeven in Excel of in andere Program ma's waarmee CSV-bestanden met UTF-8-code ring worden geopend.

## <a name="remove-users-from-an-access-review"></a>Gebruikers verwijderen uit een toegangs beoordeling

 Standaard geldt dat een verwijderde gebruiker gedurende dertig dagen in Azure AD aanwezig blijft met de status Verwijderd. In deze periode kan de gebruiker eventueel door een beheerder worden teruggezet.  Na dertig dagen wordt die gebruiker definitief verwijderd.  Daarnaast kan een globale beheerder met behulp van de Azure Active Directory-Portal expliciet [een onlangs verwijderde gebruiker permanent verwijderen](../fundamentals/active-directory-users-restore.md) voordat die periode wordt bereikt.  Als een gebruiker definitief is verwijderd, worden gegevens over die gebruiker vervolgens uit actieve toegangsbeoordelingen verwijderd.  Controle-informatie over verwijderde gebruikers blijft in het auditlogboek aanwezig.

## <a name="next-steps"></a>Volgende stappen

- [Gebruikerstoegang beheren met Azure AD-toegangsbeoordelingen](manage-user-access-with-access-reviews.md)
- [Gasttoegang beheren met Azure AD-toegangsbeoordelingen](manage-guest-access-with-access-reviews.md)
- [Een toegangsbeoordeling van groepen of toepassingen maken](create-access-review.md)
- [Een toegangsbeoordeling maken van gebruikers met een Azure AD-beheerderrol](../privileged-identity-management/pim-how-to-start-security-review.md)
