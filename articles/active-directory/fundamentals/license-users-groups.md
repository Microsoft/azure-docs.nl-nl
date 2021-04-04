---
title: Licenties toewijzen of verwijderen-Azure Active Directory | Microsoft Docs
description: Instructies voor het toewijzen of verwijderen van Azure Active Directory licenties van uw gebruikers of groepen.
services: active-directory
author: ajburnle
manager: daveba
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
ms.date: 12/14/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b6900647acf7182529f34c8cc065dbb039de38be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97504403"
---
# <a name="assign-or-remove-licenses-in-the-azure-active-directory-portal"></a>Licenties toewijzen of verwijderen in de Azure Active Directory Portal

Voor veel Azure Active Directory-Services (Azure AD) moet u een licentie voor elk van uw gebruikers of groepen (en de bijbehorende leden) voor die service. Alleen gebruikers met actieve licenties kunnen de gelicentieerde Azure AD-Services openen en gebruiken waarvoor dat wel het geval is. Licenties worden per Tenant toegepast en worden niet overgedragen aan andere tenants. 

## <a name="available-license-plans"></a>Beschik bare licentie plannen

Er zijn verschillende licentie abonnementen beschikbaar voor de Azure AD-service, waaronder:

- Azure AD Free

- Azure AD Premium P1

- Azure AD Premium P2

Zie [welke licentie heb ik nodig](https://azure.microsoft.com/pricing/details/active-directory/)voor specifieke informatie over elk licentie plan en de bijbehorende licentie gegevens?. Zie [hier](./active-directory-get-started-premium.md)voor meer informatie over het registreren van Azure AD Premium-licentie plannen.

Niet alle micro soft-Services zijn beschikbaar op alle locaties. Voordat een licentie kan worden toegewezen aan een groep, moet u de **gebruiks locatie** opgeven voor alle leden. U kunt deze waarde instellen in het gebied **instellingen van Azure Active Directory &gt; gebruikers &gt; profiel &gt;** in azure AD. Alle gebruikers waarvan de gebruiks locatie niet is opgegeven, nemen de locatie van de Azure AD-organisatie over.

## <a name="view-license-plans-and-plan-details"></a>Licentie plannen en plan gegevens weer geven

U kunt uw beschik bare service abonnementen, inclusief de afzonderlijke licenties, bekijken, verval datums in behandeling controleren en het aantal beschik bare toewijzingen weer geven.

### <a name="to-find-your-service-plan-and-plan-details"></a>Uw service plan en plan details vinden

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met een licentie beheerders account in uw Azure AD-organisatie.

1. Selecteer **Azure Active Directory** en selecteer vervolgens **licenties**.

    :::image type="content" source="media/license-users-groups/license-details-blade.png" alt-text="De pagina licenties, met het aantal aangeschafte Services en toegewezen licenties":::

1. Selecteer **alle producten** om de pagina alle producten weer te geven en om het **totale**, **toegewezen**, **beschik bare** en **verlopen binnenkort** nummer voor uw licentie plannen te bekijken.

    :::image type="content" source="media/license-users-groups/license-products-blade-with-products.png" alt-text="pagina Services-met Service licentie plannen-bijbehorende licentie gegevens":::

    > [!NOTE]
    > De getallen worden als volgt gedefinieerd: 
    > - Totaal: totaal aantal aangeschafte licenties
    > - Toegewezen: aantal licenties toegewezen aan gebruikers
    > - Beschikbaar: aantal beschik bare licenties voor toewijzing, inclusief binnenkort verlopen
    > - Binnenkort verlopen: het aantal licenties dat binnenkort verloopt

1. Selecteer een naam voor het abonnement om de gelicentieerde gebruikers en groepen weer te geven.

## <a name="assign-licenses-to-users-or-groups"></a>Licenties toewijzen aan gebruikers of groepen

Zorg ervoor dat iedereen die een gelicentieerde Azure AD-service moet gebruiken, de juiste licentie heeft. U kunt de licentie rechten toevoegen aan gebruikers of aan een volledige groep.

### <a name="to-assign-a-license-to-a-user"></a>Een licentie toewijzen aan een gebruiker

1. Selecteer op de pagina **producten** de naam van het licentie plan dat u wilt toewijzen aan de gebruiker.

    ![pagina Services, met gemarkeerd service licentie plan](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. Selecteer op de pagina overzicht licentie plan de optie **toewijzen**.

    ![pagina Services, met gemarkeerde toewijzings optie](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. Selecteer op de pagina toewijzen de optie **gebruikers en groepen**, en zoek vervolgens naar de gebruiker aan **wie** u de licentie wilt toewijzen en selecteer deze.

    ![Pagina licentie toewijzen, met gemarkeerde zoek opdracht en opties selecteren](media/license-users-groups/assign-license-blade-with-highlight.png)

1. Selecteer **toewijzings opties**, Controleer of u de juiste licentie opties hebt ingeschakeld en selecteer vervolgens **OK**.

    ![Pagina licentie opties, met alle beschik bare opties in het licentie plan](media/license-users-groups/license-option-blade-assignments.png)

    De pagina **licentie toewijzen** wordt bijgewerkt om aan te geven dat een gebruiker is geselecteerd en dat de toewijzingen zijn geconfigureerd.

    > [!NOTE]
    > Niet alle micro soft-Services zijn beschikbaar op alle locaties. Voordat een licentie aan een gebruiker kan worden toegewezen, moet u de **gebruiks locatie** opgeven. U kunt deze waarde instellen in het gebied **instellingen van Azure Active Directory &gt; gebruikers &gt; profiel &gt;** in azure AD. Alle gebruikers waarvan de gebruiks locatie niet is opgegeven, nemen de locatie van de Azure AD-organisatie over.

1. Selecteer **Toewijzen**.

    De gebruiker wordt toegevoegd aan de lijst met gelicentieerde gebruikers en heeft toegang tot de opgenomen Azure AD-Services.
    > [!NOTE]
    > Licenties kunnen ook rechtstreeks aan een gebruiker worden toegewezen op de pagina **licenties** van de gebruiker. Als een gebruiker een licentie heeft die is toegewezen via een groepslid maatschap en u dezelfde licentie rechtstreeks aan de gebruiker wilt toewijzen, kunt u deze alleen uitvoeren vanaf de pagina **producten** die worden vermeld in stap 1.

### <a name="to-assign-a-license-to-a-group"></a>Een licentie toewijzen aan een groep

1. Selecteer op de pagina **producten** de naam van het licentie plan dat u wilt toewijzen aan de gebruiker.

    ![Blade producten, met gemarkeerd product licentie plan](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. Selecteer op de pagina **Azure Active Directory Premium abonnement 2** de optie **toewijzen**.

    ![Pagina producten, met gemarkeerde toewijzings optie](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. Selecteer op de pagina **toewijzen** de optie **gebruikers en groepen** en zoek vervolgens naar de groep waaraan u de licentie wilt toewijzen en selecteer deze.

    ![Wijs de pagina licentie toe, met gemarkeerde zoek opdracht en selecteer opties 2](media/license-users-groups/assign-group-license-blade-with-highlight.png)

1. Selecteer **toewijzings opties**, Controleer of u de juiste licentie opties hebt ingeschakeld en selecteer vervolgens **OK**.

    ![Pagina licentie opties, met alle beschik bare opties in het licentie abonnement 2](media/license-users-groups/license-option-blade-group-assignments.png)

    De pagina **licentie toewijzen** wordt bijgewerkt om aan te geven dat een gebruiker is geselecteerd en dat de toewijzingen zijn geconfigureerd.

1. Selecteer **Toewijzen**.

    De groep wordt toegevoegd aan de lijst met gelicentieerde groepen en alle leden hebben toegang tot de opgenomen Azure AD-Services.

## <a name="remove-a-license"></a>Een licentie verwijderen

U kunt een licentie verwijderen van de Azure AD-gebruikers pagina van een gebruiker, via de groeps overzichts pagina voor een groeps toewijzing, of vanaf de Azure AD- **licenties** pagina om de gebruikers en groepen voor een licentie weer te geven.

### <a name="to-remove-a-license-from-a-user"></a>Een licentie van een gebruiker verwijderen

1. Selecteer op de pagina **gelicentieerde gebruikers** voor het service abonnement de gebruiker die de licentie niet meer mag hebben. Bijvoorbeeld _Alain Charon_.

1. Selecteer **licentie verwijderen**.

    ![De pagina gelicentieerde gebruikers met de optie voor het verwijderen van een licentie gemarkeerd](media/license-users-groups/license-products-user-blade-with-remove-option-highlight.png)

> [!IMPORTANT]
> Licenties die een gebruiker heeft overgenomen van een groep, kunnen niet rechtstreeks worden verwijderd. In plaats daarvan moet u de gebruiker uit de groep verwijderen waarvan u de licentie wilt overnemen.

### <a name="to-remove-a-license-from-a-group"></a>Een licentie uit een groep verwijderen

1. Selecteer op de pagina **gelicentieerde groepen** voor het licentie plan de groep die niet langer de licentie mag hebben.

1. Selecteer **licentie verwijderen**.

    ![De pagina gelicentieerde groepen met de optie voor het verwijderen van een licentie is 2](media/license-users-groups/license-products-group-blade-with-remove-option-highlight.png)
    
    > [!NOTE]
    > Wanneer een on-premises gebruikers account dat is gesynchroniseerd met Azure AD buiten het bereik valt voor de synchronisatie of wanneer de synchronisatie wordt verwijderd, wordt de gebruiker zacht verwijderd in azure AD. Als dit gebeurt, worden licenties die zijn toegewezen aan de gebruiker rechtstreeks of via groeps licenties, gemarkeerd als **opgeschort** in plaats van **verwijderd**.

## <a name="next-steps"></a>Volgende stappen

Nadat u uw licenties hebt toegewezen, kunt u de volgende processen uitvoeren:

- [Problemen met licentie toewijzing identificeren en oplossen](../enterprise-users/licensing-groups-resolve-problems.md)

- [Gebruikers met een licentie aan een groep toevoegen voor licentie verlening](../enterprise-users/licensing-groups-migrate-users.md)

- [Scenario's, beperkingen en bekende problemen met behulp van groepen voor het beheren van licenties in Azure Active Directory](../enterprise-users/licensing-group-advanced.md)

- [Profiel gegevens toevoegen of wijzigen](active-directory-users-profile-azure-portal.md)
