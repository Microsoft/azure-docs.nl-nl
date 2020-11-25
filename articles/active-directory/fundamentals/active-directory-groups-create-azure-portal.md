---
title: Een basisgroep maken en leden toevoegen - Azure Active Directory | Microsoft Docs
description: Instructies over het maken van een basisgroep met behulp van Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 06/05/2020
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18, contperfq4
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e4533334204a3a1cfd46ff27b04ff0c05350dfc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95973912"
---
# <a name="create-a-basic-group-and-add-members-using-azure-active-directory"></a>Een basisgroep maken en leden toevoegen met Azure Active Directory
U kunt een basisgroep maken met behulp van de portal Azure Active Directory (Azure AD). Voor de toepassing van dit artikel wordt een basisgroep toegevoegd aan één resource door de resource-eigenaar (administrator) en deze bevat specifieke leden (werknemers) die toegang nodig hebben tot die resource. Voor complexere scenario's, met inbegrip van dynamische lidmaatschappen en regels maken, zie de [Azure Active Directory gebruiker beheerdocumentatie](../enterprise-users/index.yml).

## <a name="group-and-membership-types"></a>Groeps-en lidmaatschaps typen
Er zijn verschillende typen groepen en lidmaatschappen. De volgende informatie bevat uitleg over elke groep en elk type lidmaatschap en waarom ze worden gebruikt, om u te helpen bepalen welke opties u moet gebruiken wanneer u een groep maakt.

### <a name="group-types"></a>Groeps typen:
- **Beveiliging**. Gebruikt voor het beheren van leden en toegang tot computers voor gedeelde resources voor een groep gebruikers. U kunt bijvoorbeeld een beveiligingsgroep voor een specifiek beveiligingsbeleid maken. Op deze manier kunt u in één keer een reeks machtigingen geven aan alle leden in plaats van machtigingen individueel voor elk lid toe te voegen. Een beveiligings groep kan gebruikers, apparaten, groepen en service-principals als leden en gebruikers en service-principals als eigen aren hebben. Zie voor meer informatie over het beheren van toegang tot resources [Toegang tot resources beheren met Azure Active Directory-groepen](active-directory-manage-groups.md).
- **Microsoft 365**. Biedt mogelijkheden voor samenwerking door leden toegang te geven tot een gedeeld postvak, agenda, bestanden, SharePoint-site en meer. Met deze optie kunt u ook personen van buiten uw organisatie toegang verlenen tot de groep. Een Microsoft 365 groep kan alleen gebruikers als leden hebben. Zowel gebruikers als service-principals kunnen eigen aren van een Microsoft 365 groep zijn. Zie voor meer informatie over Microsoft 365 groepen [informatie over Microsoft 365 groepen](https://support.office.com/article/learn-about-office-365-groups-b565caa1-5c40-40ef-9915-60fdb2d97fa2).

### <a name="membership-types"></a>Lidmaatschaps typen:
- **Assign.** Hiermee kunt u specifieke gebruikers lid maken van deze groep en hen unieke machtigingen geven. Voor de doeleinden van dit artikel gebruiken we deze optie.
- **Dynamische gebruiker.** Met kunt u dynamische lidmaatschaps regels gebruiken om leden automatisch toe te voegen en te verwijderen. Als de kenmerken van een lid wijzigen, controleert het systeem de dynamische groepsregels voor de map om te zien of het lid aan de regelvereisten voldoet (wordt toegevoegd) of niet langer voldoet aan de regelvereisten (wordt verwijderd).
- **Dynamisch apparaat.** Hiermee kunt u dynamische groepsregels gebruiken voor automatisch toevoegen en verwijderen van apparaten. Als de kenmerken van een apparaat wijzigen, controleert het systeem de dynamische groepsregels voor de map om te zien of het apparaat aan de regelvereisten voldoet (wordt toegevoegd) of niet langer voldoet aan de regelvereisten (wordt verwijderd).

    > [!IMPORTANT]
    > U kunt een dynamische groep voor apparaten of gebruikers, maar niet voor beide maken. Het is evenmin mogelijk om een apparaatgroep te maken op basis van kenmerken van de apparaateigenaren. Regels voor apparaatlidmaatschap kunnen alleen verwijzen naar apparaatkenmerken. Zie [een dynamische groep maken en de status controleren](../enterprise-users/groups-create-rule.md) voor meer informatie over het maken van een dynamische groep voor gebruikers en apparaten.

## <a name="create-a-basic-group-and-add-members"></a>Een basisgroep maken en leden toevoegen
U kunt op hetzelfde moment een basisgroep maken en leden toevoegen. Gebruik de volgende procedure om een basis groep te maken en leden toe te voegen:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met het account van een globale administrator voor de map.

1. Zoek en selecteer de optie **Azure Active Directory**.

1. Selecteer op de pagina **Active Directory** **groepen** en selecteer vervolgens **nieuwe groep**.

    ![Azure AD-pagina, met groepen die worden weer gegeven](media/active-directory-groups-create-azure-portal/group-full-screen.png)

1. Het deel venster **nieuwe groep** wordt weer gegeven en u moet de vereiste gegevens invullen.

    ![Pagina van de nieuwe groep ingevuld met de voorbeeld-info](media/active-directory-groups-create-azure-portal/new-group-blade.png)

1. Selecteer een vooraf gedefinieerd **groeps type**. Zie voor meer informatie over groeps typen [groeps-en lidmaatschaps typen](#group-types).

1. Maak een **groeps naam** en voeg deze toe. Kies een naam die u kunt onthouden en die zinvol is voor de groep. Er wordt een controle uitgevoerd om te bepalen of de naam al wordt gebruikt door een andere groep. Als de naam al in gebruik is, wordt u gevraagd de naam van uw groep te wijzigen om te voor komen dat er dubbele namen worden gebruikt.

1. Voeg een **e-mail adres** voor de groep toe voor de groep of behoud het e-mail adres dat automatisch wordt ingevuld.

1. **Groeps beschrijving.** Voeg een optionele beschrijving toe aan uw groep.

1. Selecteer een vooraf gedefinieerd **type lidmaatschap (vereist).** Zie voor meer informatie over lidmaatschaps typen [groeps-en lidmaatschaps typen](#membership-types).

1. Selecteer **Maken**. Uw groep is gemaakt en gereed om leden toe te voegen.

1. Selecteer het gebied **Leden** van de pagina **Groep** en begin vervolgens te zoeken naar de leden om ze toe te voegen aan uw groep van de pagina **Leden selecteren**.

    ![Leden voor uw groep selecteren tijdens het proces voor het maken van groep](media/active-directory-groups-create-azure-portal/select-members-create-group.png)

1. Wanneer u klaar bent met het toevoegen van leden, kiest u **Selecteer**.

    De pagina **Groepsoverzicht** wordt bijgewerkt om het aantal leden dat nu is toegevoegd aan de groep weer te geven.

    ![Pagina Groepsoverzicht met aantal leden gemarkeerd](media/active-directory-groups-create-azure-portal/group-overview-blade-number-highlight.png)

## <a name="turn-on-or-off-group-welcome-email"></a>Welkomst bericht van groep in-of uitschakelen

Wanneer een nieuwe Microsoft 365 groep wordt gemaakt, of het nu een dynamisch of statisch lidmaatschap is, wordt een welkomst melding verzonden naar alle gebruikers die zijn toegevoegd aan de groep. Wanneer een van de kenmerken van een gebruiker of apparaat wordt gewijzigd, worden alle regels voor de dynamische groep in de organisatie verwerkt voor mogelijke lidmaatschaps wijzigingen. Gebruikers die zijn toegevoegd, ontvangen ook de welkomst melding. U kunt dit gedrag uitschakelen in [Exchange Power shell](/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps). 

## <a name="next-steps"></a>Volgende stappen

- [Toegang tot SaaS-apps met behulp van groepen beheren](../enterprise-users/groups-saasapps.md)
- [Groepen met behulp van PowerShell-opdrachten beheren](../enterprise-users/groups-settings-v2-cmdlets.md)