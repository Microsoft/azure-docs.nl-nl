---
title: Een verbonden organisatie toevoegen in het beheer van rechten van Azure AD-Azure Active Directory
description: Meer informatie over het toestaan van personen buiten uw organisatie om toegangs pakketten aan te vragen, zodat u kunt samen werken aan projecten.
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
ms.date: 12/11/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f08c25749bbd21e3624dee898d9a8c97fd74164
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/10/2021
ms.locfileid: "98059378"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management"></a>Een verbonden organisatie toevoegen in het beheer van rechten van Azure AD

Met Azure Active Directory (Azure AD) toeslag beheer kunt u samen werken met mensen buiten uw organisatie. Als u regel matig samenwerkt met gebruikers in een extern Azure AD-adres lijst of-domein, kunt u ze als een verbonden organisatie toevoegen. In dit artikel wordt beschreven hoe u een verbonden organisatie toevoegt zodat u gebruikers buiten uw organisatie in staat kunt stellen om resources in uw directory aan te vragen.

## <a name="what-is-a-connected-organization"></a>Wat is een verbonden organisatie?

Een verbonden organisatie is een andere organisatie waarmee u een relatie hebt.  Om ervoor te zorgen dat de gebruikers in die organisatie toegang kunnen krijgen tot uw resources, zoals uw share point online-sites of-apps, hebt u een weer gave van de gebruikers van die organisatie in die map nodig.  Omdat de gebruikers in die organisatie zich in de meeste gevallen nog niet in uw Azure AD-Directory bevinden, kunt u het rechten beheer gebruiken om ze naar wens naar uw Azure AD-adres lijst te brengen.  

Er zijn drie manieren waarop het beheer van rechten de gebruikers kan opgeven die een verbonden organisatie vormen.  Dit kan worden

* gebruikers in een andere Azure AD-Directory,
* gebruikers in een andere niet-Azure AD-Directory die is geconfigureerd voor directe Federatie, of
* gebruikers in een andere AD-directory zonder Azure, waarvan de e-mail adressen allemaal dezelfde domein naam hebben gemeen.

Stel bijvoorbeeld dat u werkt met de Woodgrove Bank en u wilt samen werken met twee externe organisaties. Deze twee organisaties hebben verschillende configuraties:

- Grafisch ontwerp Institute maakt gebruik van Azure AD en hun gebruikers hebben een user principal name dat eindigt op *graphicdesigninstitute.com*.
- Contoso gebruikt nog geen Azure AD. Contoso-gebruikers hebben een user principal name die eindigt op *contoso.com*.

In dit geval kunt u twee verbonden organisaties configureren. U maakt één verbonden organisatie voor grafisch ontwerp Institute en één voor contoso. Als u de twee verbonden organisaties vervolgens aan een beleid toevoegt, kunnen gebruikers van elke organisatie met een user principal name die overeenkomt met het beleid toegangs pakketten aanvragen. Gebruikers met een user principal name die een domein van *graphicdesigninstitute.com* hebben, moeten overeenkomen met de grafische ontwerp Institute-verbonden organisatie en kunnen aanvragen indienen. Gebruikers met een user principal name die een domein van *contoso.com* hebben, moeten overeenkomen met de contoso-verbonden organisatie en kunnen ook pakketten aanvragen. En omdat grafisch ontwerp Institute gebruikmaakt van Azure AD, kunnen gebruikers met een principal-naam die overeenkomt met een [geverifieerd domein](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) dat is toegevoegd aan hun Tenant, zoals *graphicdesigninstitute. example*, ook toegangs pakketten aanvragen met hetzelfde beleid.

![Voor beeld van verbonden organisatie](./media/entitlement-management-organization/connected-organization-example.png)

Hoe gebruikers van de Azure AD-Directory of het domein verifiëren, is afhankelijk van het verificatie type. De verificatie typen voor verbonden organisaties zijn:

- Azure AD
- [Directe federatie](../external-identities/direct-federation.md)
- [Eenmalige wachtwoord code](../external-identities/one-time-passcode.md) (domein)

Bekijk de volgende video voor een demonstratie van het toevoegen van een verbonden organisatie:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4dskS]

## <a name="add-a-connected-organization"></a>Een verbonden organisatie toevoegen

Volg de instructies in deze sectie om een extern Azure AD-Directory of-domein toe te voegen als verbonden organisatie.

**Vereiste rol**: *globale beheerder* of *gebruikers beheerder*

1. Selecteer **Azure Active Directory** In het Azure Portal en selecteer vervolgens **Identity governance**.

1. Selecteer **verbonden organisaties** in het linkerdeel venster en selecteer vervolgens **verbonden organisatie toevoegen**.

    ![De knop verbonden organisatie toevoegen](./media/entitlement-management-organization/connected-organization.png)

1. Selecteer het tabblad **basis principes** en voer vervolgens een weergave naam en een beschrijving in voor de organisatie.

    ![Het deel venster met de basis principes voor het toevoegen van verbonden organisaties](./media/entitlement-management-organization/organization-basics.png)

1. De status wordt automatisch ingesteld op **geconfigureerd** wanneer u een nieuwe verbonden organisatie maakt. Zie [Status eigenschappen van verbonden organisaties](#state-properties-of-connected-organizations) voor meer informatie over status eigenschappen.

1. Selecteer de **map** en het domein tabblad en selecteer vervolgens **map toevoegen + domein**.

    Het deel venster **mappen en domeinen selecteren** wordt geopend.

1. Voer in het zoekvak een domein naam in om te zoeken naar de Azure AD-Directory of het domein. Zorg ervoor dat u de volledige domein naam invoert.

1. Controleer of de naam van de organisatie en het verificatie type juist zijn. Hoe gebruikers zich aanmelden, zijn afhankelijk van het verificatie type.

    ![Het deel venster mappen en domeinen selecteren](./media/entitlement-management-organization/organization-select-directories-domains.png)

1. Selecteer **toevoegen** om de Azure AD-Directory of het domein toe te voegen. Op dit moment kunt u slechts één Azure AD-adres lijst of-domein toevoegen per verbonden organisatie.

    > [!NOTE]
    > Alle gebruikers uit de Azure AD-adres lijst of het-domein kunnen dit toegangs pakket aanvragen. Dit geldt ook voor gebruikers in azure AD van alle subdomeinen die zijn gekoppeld aan de Directory, tenzij deze domeinen worden geblokkeerd door de Azure AD Business to Business (B2B)-lijst voor toestaan of weigeren. Zie [uitnodigingen voor B2B-gebruikers van specifieke organisaties toestaan of blok keren](../external-identities/allow-deny-list.md)voor meer informatie.

1. Nadat u de Azure AD-Directory of het domein hebt toegevoegd, selecteert u **selecteren**.

    De organisatie wordt weer gegeven in de lijst.

    ![Het deel venster ' Directory + domein '](./media/entitlement-management-organization/organization-directory-domain.png)

1. Selecteer het tabblad **sponsors** en voeg vervolgens optionele sponsors toe voor deze verbonden organisatie.

    Sponsors zijn interne of externe gebruikers in uw directory die het contact punt vormen voor de relatie met deze verbonden organisatie. Interne sponsors zijn lid van gebruikers in uw Directory. Externe sponsors zijn gast gebruikers van de verbonden organisatie die eerder zijn uitgenodigd en zich al in uw directory bevinden. Sponsors kunnen worden gebruikt als goed keurders wanneer gebruikers in deze verbonden organisatie toegang aanvragen tot dit toegangs pakket. Zie [Azure Active Directory B2B-samenwerkings gebruikers toevoegen in de Azure Portal](../external-identities/add-users-administrator.md)voor informatie over het uitnodigen van een gast gebruiker aan uw Directory.

    Wanneer u **toevoegen/verwijderen** selecteert, wordt er een deel venster geopend waarin u interne of externe sponsors kunt kiezen. In het deel venster wordt een niet-gefilterde lijst met gebruikers en groepen in uw directory weer gegeven.

    ![Het deel venster sponsors](./media/entitlement-management-organization/organization-sponsors.png)

1. Selecteer het tabblad **controleren + maken** , Controleer de instellingen van uw organisatie en selecteer vervolgens **maken**.

    ![Het deel venster ' bekijken en maken '](./media/entitlement-management-organization/organization-review-create.png)

## <a name="update-a-connected-organization"></a>Een verbonden organisatie bijwerken 

Als de verbonden organisatie wijzigingen aanbrengt in een ander domein, wijzigt de naam van de organisatie of u de sponsors wilt wijzigen, kunt u de verbonden organisatie bijwerken door de instructies in deze sectie te volgen.

**Vereiste rol**: *globale beheerder* of *gebruikers beheerder*

1. Selecteer **Azure Active Directory** In het Azure Portal en selecteer vervolgens **Identity governance**.

1. Selecteer **verbonden organisaties** in het linkerdeel venster en selecteer vervolgens de verbonden organisatie om deze te openen.

1. Selecteer **bewerken** in het overzichts venster van de verbonden organisatie om de naam, beschrijving of status van de organisatie te wijzigen.  

1. Selecteer in het deel venster **Directory + domein** **Update Directory + domein** om naar een andere Directory of een ander domein te wijzigen.

1. Selecteer in het deel venster **sponsors** de optie **interne sponsors toevoegen** of **Voeg externe sponsors** toe om een gebruiker toe te voegen als sponsor. Als u een sponsor wilt verwijderen, selecteert u de sponsor en selecteert u in het rechterdeel venster **verwijderen**.


## <a name="delete-a-connected-organization"></a>Een verbonden organisatie verwijderen

Als u geen relatie meer hebt met een extern Azure AD-adres lijst of-domein, kunt u de verbonden organisatie verwijderen.

**Vereiste rol**: *globale beheerder* of *gebruikers beheerder*

1. Selecteer **Azure Active Directory** In het Azure Portal en selecteer vervolgens **Identity governance**.

1. Selecteer **verbonden organisaties** in het linkerdeel venster en selecteer vervolgens de verbonden organisatie om deze te openen.

1. Selecteer **verwijderen** in het overzichts venster van de verbonden organisatie om deze te verwijderen.

    ![De knop verbonden organisatie verwijderen](./media/entitlement-management-organization/organization-delete.png)

## <a name="managing-a-connected-organization-programmatically"></a>Een verbonden organisatie programmatisch beheren

U kunt ook verbonden organisaties maken, weer geven, bijwerken en verwijderen met behulp van Microsoft Graph. Een gebruiker in een geschikte rol met een toepassing die de gedelegeerde `EntitlementManagement.ReadWrite.All` machtiging heeft, kan de API aanroepen om [connectedOrganization](/graph/api/resources/connectedorganization?view=graph-rest-beta) -objecten te beheren en sponsors voor hen in te stellen.

## <a name="state-properties-of-connected-organizations"></a>Status eigenschappen van verbonden organisaties

Er zijn twee verschillende soorten status eigenschappen voor verbonden organisaties in het Azure AD-rechts beheer op dit moment, geconfigureerd en voorgesteld: 

- Een geconfigureerde verbonden organisatie is een volledig functionele, verbonden organisatie waarmee gebruikers binnen die organisatie toegang hebben tot pakketten. Wanneer een beheerder een nieuwe verbonden organisatie maakt in de Azure Portal, heeft deze standaard de status **geconfigureerd** , omdat de beheerder deze verbonden organisatie heeft gemaakt en wil gebruiken. Daarnaast moet de standaard status worden **geconfigureerd** wanneer een verbonden org via de API wordt gemaakt, tenzij deze expliciet is ingesteld op een andere status. 

    Geconfigureerde verbonden organisaties worden weer gegeven in de kiezers voor verbonden organisaties en zijn in bereik voor beleids regels die zijn gericht op alle verbonden organisaties.

- Een voorgestelde verbonden organisatie is een verbonden organisatie die automatisch is gemaakt, maar waarvoor geen beheerder de organisatie heeft gemaakt of goedgekeurd. Wanneer een gebruiker zich aanmeldt voor een toegangs pakket buiten een geconfigureerde verbonden organisatie, krijgen alle automatisch gemaakte verbonden organisaties de **voorgestelde** status, omdat er geen beheerder in de Tenant is ingesteld die samen werking. 
    
    Voorgestelde verbonden organisaties bevinden zich niet in het bereik van de instelling ' alle geconfigureerde verbonden organisaties ' op beleids regels, maar kunnen alleen worden gebruikt in beleids regels voor beleids regels die gericht zijn op specifieke organisaties. 

Alleen gebruikers van geconfigureerde verbonden organisaties kunnen toegangs pakketten aanvragen die beschikbaar zijn voor gebruikers van alle geconfigureerde organisaties. Gebruikers van voorgestelde verbonden organisaties hebben een ervaring alsof er geen verbonden organisatie voor dat domein is. kan alleen toegangs pakketten zien en aanvragen die zijn gericht aan hun specifieke organisatie of het bereik van een gebruiker.

> [!NOTE]
> Als onderdeel van het implementeren van deze nieuwe functie werden alle verbonden organisaties die zijn gemaakt vóór 09/09/20, als **geconfigureerd** beschouwd. Als u een toegangs pakket hebt waarmee gebruikers van een organisatie zich kunnen registreren, moet u uw lijst met verbonden organisaties die zijn gemaakt vóór die datum controleren om ervoor te zorgen dat er geen gecategoriseerd is als **geconfigureerd**.  Een beheerder kan de eigenschap **State** zo nodig bijwerken. Zie [een verbonden organisatie bijwerken](#update-a-connected-organization)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Toegang voor externe gebruikers beheren](./entitlement-management-external-users.md)
- [De toegang bepalen voor gebruikers die zich niet in uw directory besturen](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
