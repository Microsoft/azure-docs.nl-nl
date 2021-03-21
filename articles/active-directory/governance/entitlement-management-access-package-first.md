---
title: 'Zelf studie: de toegang tot resources beheren in het beheer van rechten van Azure AD'
description: Stapsgewijze zelf studie voor het maken van uw eerste toegangs pakket met behulp van de Azure Portal in Azure Active Directory rechten beheer.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.subservice: compliance
ms.date: 09/30/2020
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1adbc653c8b698cb7b439b54b0a77d2b8cd4042a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100547397"
---
# <a name="tutorial-manage-access-to-resources-in-azure-ad-entitlement-management"></a>Zelf studie: toegang tot resources beheren in het beheer van rechten van Azure AD

Het beheren van toegang tot alle resources die werknemers nodig hebben, zoals groepen, toepassingen en sites, is een belangrijke functie voor organisaties. U wilt werknemers het juiste toegangsniveau verlenen om productief te zijn en hun toegang verwijderen wanneer deze niet meer nodig is.

In deze zelfstudie werkt u als IT-beheerder voor de Woodgrove Bank. U bent gevraagd om een pakket met resources te maken voor een marketingcampagne die interne gebruikers kunnen gebruiken om een selfserviceaanvraag in te dienen. Voor aanvragen is geen goedkeuring nodig en de toegang van gebruikers verloopt na 30 dagen. Voor deze zelfstudie zijn de resources van de marketingcampagne lid van slechts één groep, maar dit kan een verzameling van groepen, toepassingen of SharePoint Online-sites zijn.

![Diagram met een overzicht van het scenario.](./media/entitlement-management-access-package-first/elm-scenario-overview.png)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een toegangspakket maken met een groep als een resource
> * Een gebruiker in uw directory toestaan om toegang aan te vragen
> * Hoe een interne gebruiker het toegangspakket kan aanvragen

Bekijk de volgende video voor een stapsgewijze demonstratie van het proces voor het implementeren van Azure Active Directory-rechtenbeheer, inclusief het maken van uw eerste toegangspakket:

>[!VIDEO https://www.youtube.com/embed/zaaKvaaYwI4]

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-rechtenbeheer wilt gebruiken, moet u over een van de volgende licenties beschikken:

- Azure AD Premium P2
- Licentie voor Enterprise Mobility + Security (EMS) E5

Zie [Licentievereisten](entitlement-management-overview.md#license-requirements) voor meer informatie.

## <a name="step-1-set-up-users-and-group"></a>Stap 1: Gebruikers en groep instellen

Een resourcedirectory bevat een of meer resources om te delen. In deze stap maakt u een groep met de naam **Marketingresources** in de directory Woodgrove Bank die de doelresource voor rechtenbeheer is. U stelt ook een interne aanvrager in.

**Vereiste rol:** bedrijfsbeheerder of gebruikersbeheerder

![Gebruikers en groepen maken](./media/entitlement-management-access-package-first/elm-users-groups.png)

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als globale beheerder of bedrijfsbeheerder.  

1. Klik in de linkernavigatie op **Azure Active Directory**.

1. Maak of configureer de volgende twee gebruikers. U kunt deze namen of andere namen gebruiken. **Admin1** kan de gebruiker zijn met wie u momenteel bent aangemeld.

    | Naam | Directory-rol |
    | --- | --- |
    | **Admin1** | Globale beheerder<br/>-of-<br/>Gebruikersbeheerder |
    | **Requestor1** | Gebruiker |

1. Maak een Azure AD-beveiligingsgroep met de naam **Marketingresources** met **Toegewezen** als lidmaatschapstype.

    Deze groep wordt de doelresource voor rechtenbeheer. De groep moet leeg zijn.

## <a name="step-2-create-an-access-package"></a>Stap 2: Een toegangspakket maken

Een *toegangspakket* is een bundel resources die een team of project nodig heeft en die wordt beheerd met beleid. Toegangspakketten worden gedefinieerd in containers die *catalogussen* worden genoemd. In deze stap maakt u een toegangspakket voor een **marketingcampagne** in de catalogus **Algemeen**.

**Vereiste rol:** globale beheerder, gebruikersbeheerder, cataloguseigenaar of toegangspakketbeheerder

![Een toegangspakket maken](./media/entitlement-management-access-package-first/elm-access-package.png)

1. Klik in de Azure-portal in de linkernavigatie op **Azure Active Directory**.

2. Klik in het linkermenu op **Identity Governance**

3. Klik in het linkermenu op **Toegangspakketten**.  Als **Toegang geweigerd** wordt weergegeven, moet u ervoor zorgen dat uw directory een Azure AD Premium P2-licentie bevat.

4. Klik op **Nieuw toegangspakket**.

    ![Rechtenbeheer in de Azure-portal](./media/entitlement-management-shared/access-packages-list.png)

5. Typ op het tabblad **Basinformatie** de naam **Marketingcampagne**-toegangspakket en de beschrijving **Toegang tot resources voor de campagne**.

6. Laat de vervolgkeuzelijst **Catalogus** ingesteld op **Algemeen**.

    ![Nieuw toegangspakket - tabblad Basisinformatie](./media/entitlement-management-access-package-first/basics.png)

7. Klik op **Volgende** om het tabblad **Resourcerollen** te openen.

    Op dit tabblad selecteert u de resources en de resourcefunctie die u wilt toevoegen aan het toegangspakket.

8. Klik op **Groepen en teams**.

9. Zoek en selecteer in het deelvenster Groepen selecteren de groep **Marketingresources** die u eerder hebt gemaakt.

     Standaard ziet u groepen binnen de catalogus Algemeen. Wanneer u een groep buiten de catalogus Algemeen selecteert, zoals u kunt zien als u het selectievakje **Alles weergeven** inschakelt, wordt deze toegevoegd aan de catalogus Algemeen.

    ![Schermopname van het tabblad Nieuw toegangspakket - Resourcerollen en het venster Groepen selecteren.](./media/entitlement-management-access-package-first/resource-roles-select-groups.png)

10. Klik op **Selecteren** om de groep aan de lijst toe te voegen.

11. Selecteer in de vervolgkeuzelijst **Rol** de optie **Lid**.

    ![Nieuw toegangspakket - tabblad Resourcerollen](./media/entitlement-management-access-package-first/resource-roles.png)

    >[!IMPORTANT]
    >De roltoewijzingsgroepen die aan een toegangspakket zijn toegevoegd, worden aangegeven met het subtype **Toewijsbaar aan rollen**. Raadpleeg [Een roltoewijsbare groep maken](../roles/groups-create-eligible.md) in Azure Active Directory voor meer informatie over groepen die kunnen worden toegewezen aan Azure AD-rollen. Houd er rekening mee dat wanneer een door een functie toewijs bare groep aanwezig is in een catalogus voor een toegangs pakket, gebruikers met beheerders rechten die in het recht beheer kunnen beheren, met inbegrip van globale beheerders, gebruikers beheerders en catalogus eigenaren van de catalogus, de toegangs pakketten in de catalogus kunnen beheren, zodat ze kunnen kiezen wie er aan deze groepen kan worden toegevoegd. Als u geen roltoewijsbare groep ziet die u wilt toevoegen of als u deze niet kunt toevoegen, zorg er dan voor dat u de vereiste Azure AD-rol en rechtenbeheerrol hebt om deze bewerking uit te voeren. Mogelijk moet u iemand met de vereiste rollen vragen om de bron aan uw catalogus toe te voegen. Bekijk [Vereiste rollen om bronnen aan een catalogus toe te voegen](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog) voor meer informatie.

    >[!NOTE]
    > Wanneer u [dynamische groepen](../enterprise-users/groups-create-rule.md) gebruikt, ziet u behalve de eigenaar geen andere beschikbare rollen. Dit is standaard.
    > ![Overzicht van scenario's](./media/entitlement-management-access-package-first/dynamic-group-warning.png)
    


12. Klik op **Volgende** om het tabblad **Aanvragen** te openen.

    Op dit tabblad maakt u een aanvraagbeleid. Een *beleid* definieert de regels of richtlijnen voor toegang tot een toegangspakket. U maakt een beleid waarmee een specifieke gebruiker in de resourcedirectory dit toegangspakket kan aanvragen.

13. Klik in de sectie **Gebruikers die toegang kunnen aanvragen** op **Voor gebruikers in uw directory** en klik vervolgens op **Specifieke gebruikers en groepen**.

    ![Nieuw toegangspakket - tabblad Aanvragen](./media/entitlement-management-access-package-first/requests.png)

14. Klik op **Gebruikers en groepen toevoegen**.

15. Selecteer in het deelvenster Gebruikers en groepen selecteren de gebruiker **Requestor1** die u eerder hebt gemaakt.

    ![Nieuw toegangspakket - tabblad Aanvragen - Gebruikers en groepen selecteren](./media/entitlement-management-access-package-first/requests-select-users-groups.png)

16. Klik op **Selecteren**.

17. Schuif omlaag naar de secties **Goedkeuring** en **Aanvragen inschakelen**.

18. Laat **Goedkeuring vereisen** ingesteld op **Nee**.

19. Klik voor **Aanvragen inschakelen** op **Ja** om dit toegangspakket in te schakelen zodra het is gemaakt.

    ![Nieuw toegangspakket - tabblad Aanvragen - Goedkeuring en aanvragen inschakelen](./media/entitlement-management-access-package-first/requests-approval-enable.png)

20. Klik op **Volgende** om het tabblad **Levenscyclus** te openen.

21. Stel in de sectie **Verloop** de optie **De toegangspakkettoewijzingen verlopen** in op **Aantal dagen**.

22. Stel **Toewijzingen verlopen na** in op **30** dagen.

    ![Nieuw toegangspakket - tabblad Levenscyclus](./media/entitlement-management-access-package-first/lifecycle.png)

23. Klik op **Volgende** om het tabblad **Beoordelen en maken** te openen.

    ![Nieuw toegangspakket - tabblad Beoordelen en maken](./media/entitlement-management-access-package-first/review-create.png)

    Na enkele ogenblikken ziet u een melding dat het toegangspakket is gemaakt.

24. Klik in het linkermenu van het toegangspakket Marketingcampagne op **Overzicht**.

25. Kopieer de **Koppeling voor de portal Mijn toegang**.

    U gebruikt deze koppeling voor de volgende stap.

    ![Overzicht van toegangspakket - Koppeling voor de portal Mijn toegang](./media/entitlement-management-shared/my-access-portal-link.png)

## <a name="step-3-request-access"></a>Stap 3: Toegang aanvragen

In deze stap voert u de stappen uit als de **interne aanvrager** en vraagt u toegang tot het toegangspakket aan. Aanvragers verzenden hun aanvragen via de portal Mijn toegang. Via de portal Mijn toegang kunnen aanvragers aanvragen voor toegangspakketten verzenden, de toegangspakketten waartoe ze al toegang hebben bekijken en hun aanvraaggeschiedenis weergeven.

**Vereiste rol:** Interne aanvrager

1. Meld u af bij de Azure-portal.

1. Navigeer in een nieuw browservenster naar de koppeling voor de portal Mijn toegang die u in de vorige stap hebt gekopieerd.

1. Meld u als **Requestor1** bij de portal Mijn toegang aan.

    U ziet het toegangspakket **Marketingcampagne**.

1. Klik indien nodig in de kolom **Beschrijving** op de pijl om de details van het toegangspakket weer te geven.

    ![Portal Mijn toegang - Toegangspakketten](./media/entitlement-management-shared/my-access-access-packages.png)

1. Klik op het vinkje om het pakket te selecteren.

1. Klik op **Toegang aanvragen** om het deelvenster Toegang aanvragen te openen.

    ![Portal Mijn toegang - knop Toegang aanvragen](./media/entitlement-management-access-package-first/my-access-request-access-button.png)

1. Typ in het vak **Zakelijke reden** de reden **Ik werk aan de nieuwe marketingcampagne**.

    ![Portal Mijn toegang - Toegang aanvragen](./media/entitlement-management-shared/my-access-request-access.png)

1. Klik op **Submit**

1. Klik in het menu links op **Geschiedenis van de aanvraag** om te controleren of uw aanvraag is verzonden.

## <a name="step-4-validate-that-access-has-been-assigned"></a>Stap 4: Controleren of de toegang is toegewezen

In deze stap bevestigt u dat de **interne aanvrager** aan het toegangspakket is toegewezen en dat deze nu lid is van de groep **Marketingresources**.

**Vereiste rol:** globale beheerder, gebruikersbeheerder, cataloguseigenaar of toegangspakketbeheerder

1. Meld u af bij de portal Mijn toegang.

1. Meld u bij de [Azure Portal](https://portal.azure.com) aan als **Admin1**.

1. Klik op **Azure Active Directory** en vervolgens op **Identity Governance**.

1. Klik in het linkermenu op **Toegangspakketten**.

1. Zoek het toegangspakket **Marketingcampagne** en klik erop.

1. Klik in het linkermenu op **Aanvragen**.

    U ziet Requestor1 en het initiële beleid met de status **Geleverd**.

1. Klik op de aanvraag om de details van de aanvraag weer te geven.

    ![Toegangspakket - Aanvraagdetails](./media/entitlement-management-access-package-first/request-details.png)

1. Klik in de linkernavigatie op **Azure Active Directory**.

1. Klik op **Groepen** en open de groep **Marketingresources**.

1. Klik op **Leden**.

    U ziet **Requestor1** vermeld als een lid.

    ![Leden van marketingresources](./media/entitlement-management-access-package-first/group-members.png)

## <a name="step-5-clean-up-resources"></a>Stap 5: Resources opschonen

In deze stap verwijdert u de wijzigingen die u hebt aangebracht en verwijdert u het toegangspakket **Marketingcampagne**.

**Vereiste rol:**  bedrijfsbeheerder of gebruikersbeheerder

1. Klik in de Azure-portal op **Azure Active Directory** en vervolgens op **Identity Governance**.

1. Open het toegangspakket **Marketingcampagne**.

1. Klik op **Toewijzingen**.

1. Klik voor **Requestor1** op het weglatingsteken ( **...** ) en klik vervolgens op **Toegang verwijderen**. Klik in het bericht dat wordt weergegeven op **Ja**.

    Na enkele ogenblikken wordt de status gewijzigd van Geleverd in Verlopen.

1. Klik op **Resourcerollen**.

1. Klik voor **Marketingresources** op het weglatingsteken ( **...** ) en klik vervolgens op **Resourcerol verwijderen**. Klik in het bericht dat wordt weergegeven op **Ja**.

1. Open de lijst met toegangspakketten.

1. Klik voor **Marketingcampagne** op het weglatingsteken ( **...** ) en klik vervolgens op **Verwijderen**. Klik in het bericht dat wordt weergegeven op **Ja**.

1. Verwijder in Azure Active Directory de gebruikers die u hebt gemaakt, zoals **Requestor1** en **Admin1**.

1. Verwijder de groep **Marketingresources**.

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel voor meer informatie over veelvoorkomende scenariostappen in rechtenbeheer.
> [!div class="nextstepaction"]
> [Algemene scenario's](entitlement-management-scenarios.md)
