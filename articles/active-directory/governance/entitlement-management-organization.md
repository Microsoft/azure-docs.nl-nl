---
title: Een verbonden organisatie toevoegen in Azure AD-rechtenbeheer - Azure Active Directory
description: Meer informatie over het toestaan van mensen buiten uw organisatie om toegangspakketten aan te vragen, zodat u aan projecten kunt samenwerken.
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
ms.openlocfilehash: 44b4e4bccde07d078c9749ee76c1653e6d431e63
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532085"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management"></a>Een verbonden organisatie toevoegen in Azure AD-rechtenbeheer

Met Azure Active Directory rechtenbeheer (Azure AD) kunt u samenwerken met personen buiten uw organisatie. Als u vaak samenwerkt met gebruikers in een externe Azure AD-directory of -domein, kunt u ze toevoegen als een verbonden organisatie. In dit artikel wordt beschreven hoe u een verbonden organisatie toevoegt, zodat gebruikers buiten uw organisatie resources kunnen aanvragen in uw directory.

## <a name="what-is-a-connected-organization"></a>Wat is een verbonden organisatie?

Een verbonden organisatie is een andere organisatie met wie u een relatie hebt.  Als u wilt dat de gebruikers in die organisatie toegang hebben tot uw resources, zoals uw SharePoint Online-sites of -apps, hebt u een weergave nodig van de gebruikers van die organisatie in die directory.  Omdat de gebruikers in die organisatie zich in de meeste gevallen nog niet in uw Azure AD-directory hebben, kunt u rechtenbeheer gebruiken om ze naar uw Azure AD-directory te brengen als dat nodig is.  

Er zijn drie manieren waarop u rechtenbeheer kunt gebruiken om de gebruikers op te geven die een verbonden organisatie vormen.  Dit kan zijn

* gebruikers in een andere Azure AD-directory,
* gebruikers in een andere niet-Azure AD-directory die is geconfigureerd voor directe federatie, of
* gebruikers in een andere niet-Azure AD-directory, waarvan de e-mailadressen allemaal dezelfde domeinnaam gemeenschappelijk hebben.

Stel bijvoorbeeld dat u bij Woodgrove Bank werkt en u wilt samenwerken met twee externe organisaties. Deze twee organisaties hebben verschillende configuraties:

- Graphic Design Institute maakt gebruik van Azure AD en hun gebruikers hebben een user principal name die eindigt met *graphicdesigninstitute.com.*
- Contoso maakt nog geen gebruik van Azure AD. Contoso-gebruikers hebben een user principal name die eindigt met *contoso.com*.

In dit geval kunt u twee verbonden organisaties configureren. U maakt één verbonden organisatie voor het Graphic Design Institute en één voor Contoso. Als u vervolgens de twee verbonden organisaties aan een beleid toevoegt, kunnen gebruikers van elke organisatie met een user principal name die overeenkomt met het beleid toegangspakketten aanvragen. Gebruikers met een user principal name met een domein van contoso.com komen overeen met de met Contoso verbonden organisatie en kunnen ook pakketten aanvragen. Gebruikers met een user principal name met een domein van *graphicdesigninstitute.com* komen overeen met de organisatie die is verbonden met het Graphic Design Institute en mogen aanvragen indienen. En omdat Het Graphic Design Institute gebruikmaakt van Azure [](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) AD, kunnen alle gebruikers met een principal-naam die overeenkomt met een geverifieerd domein dat aan hun tenant is toegevoegd, zoals *graphicdesigndesigndesigne.example,* ook toegangspakketten aanvragen met behulp van hetzelfde beleid. Als u verificatie via een een-time [wachtwoordcode (OTP) voor e-mail](../external-identities/one-time-passcode.md) hebt ingeschakeld, zijn dat gebruikers uit die domeinen die nog geen Azure AD-accounts hebben die zich verifiëren met behulp van e-mail-OTP bij het openen van uw resources. 

![Voorbeeld van verbonden organisatie](./media/entitlement-management-organization/connected-organization-example.png)

Hoe gebruikers uit de Azure AD-directory of het Azure AD-domein zich verifiëren, is afhankelijk van het verificatietype. De verificatietypen voor verbonden organisaties zijn:

- Azure AD
- [Directe federatie](../external-identities/direct-federation.md)
- [Een een time-wachtwoordcode](../external-identities/one-time-passcode.md) (domein)

Bekijk de volgende video voor een demonstratie van het toevoegen van een verbonden organisatie:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4dskS]

## <a name="add-a-connected-organization"></a>Een verbonden organisatie toevoegen

Als u een externe Azure AD-directory of -domein wilt toevoegen als een verbonden organisatie, volgt u de instructies in deze sectie.

**Vereiste rol:** *Globale beheerder* of *gebruikersbeheerder*

1. Selecteer in Azure Portal de **Azure Active Directory** en selecteer vervolgens **Identity Governance.**

1. Selecteer in het linkerdeelvenster **Verbonden organisaties** en selecteer vervolgens Verbonden **organisatie toevoegen.**

    ![De knop Verbonden organisatie toevoegen](./media/entitlement-management-organization/connected-organization.png)

1. Selecteer het **tabblad Basisinformatie** en voer een weergavenaam en beschrijving in voor de organisatie.

    ![Het deelvenster Basisbeginselen 'Verbonden organisatie toevoegen'](./media/entitlement-management-organization/organization-basics.png)

1. De status wordt automatisch ingesteld op Geconfigureerd **wanneer** u een nieuwe verbonden organisatie maakt. Zie Statuseigenschappen van verbonden organisaties voor meer informatie [over statuseigenschappen](#state-properties-of-connected-organizations)

1. Selecteer het **tabblad Map en** domein en selecteer vervolgens Map + domein **toevoegen.**

    Het **deelvenster Mappen en domeinen selecteren wordt** geopend.

1. Voer in het zoekvak een domeinnaam in om te zoeken naar de Azure AD-directory of het Azure AD-domein. Voer de volledige domeinnaam in.

1. Controleer of de organisatienaam en het verificatietype juist zijn. Hoe gebruikers zich aanmelden, is afhankelijk van het verificatietype.

    ![Het deelvenster Mappen en domeinen selecteren](./media/entitlement-management-organization/organization-select-directories-domains.png)

1. Selecteer **Toevoegen om** de Azure AD-map of het Azure AD-domein toe te voegen. Op dit moment kunt u slechts één Azure AD-directory of -domein per verbonden organisatie toevoegen.

    > [!NOTE]
    > Alle gebruikers van de Azure AD-directory of het Azure AD-domein kunnen dit toegangspakket aanvragen. Dit omvat gebruikers in Azure AD van alle subdomeinen die zijn gekoppeld aan de directory, tenzij deze domeinen worden geblokkeerd door de lijst voor toestaan of weigeren van Azure AD Business to Business (B2B). Zie Uitnodigingen voor [B2B-gebruikers](../external-identities/allow-deny-list.md)van specifieke organisaties toestaan of blokkeren voor meer informatie.

1. Nadat u de Azure AD-directory of het Azure AD-domein hebt toegevoegd, selecteert u **Selecteren.**

    De organisatie wordt weergegeven in de lijst.

    ![Het deelvenster Map + domein](./media/entitlement-management-organization/organization-directory-domain.png)

1. Selecteer het **tabbladOrs** en voeg vervolgens optioneleponsoren toe voor deze verbonden organisatie.

    De klanten zijn interne of externe gebruikers in uw directory die het contactpunt zijn voor de relatie met deze verbonden organisatie. Interne klanten zijn lidgebruikers in uw directory. Externe klanten zijn gastgebruikers van de verbonden organisatie die eerder zijn uitgenodigd en zich al in uw directory hebben. Ponsoren kunnen worden gebruikt als goedkeurders wanneer gebruikers in deze verbonden organisatie toegang tot dit toegangspakket aanvragen. Zie Add Azure Active Directory B2B collaboration users in the Azure Portal (Gebruikers van [B2B-samenwerking](../external-identities/add-users-administrator.md)toevoegen in de Azure Portal) voor meer informatie over het uitnodigen van een gastgebruiker voor uw directory.

    Wanneer u **Toevoegen/verwijderen selecteert,** wordt er een deelvenster geopend waarin u interne of externe partners kunt kiezen. In het deelvenster wordt een ongefilterde lijst met gebruikers en groepen in uw directory weergegeven.

    ![Het deelvensterPonsen](./media/entitlement-management-organization/organization-sponsors.png)

1. Selecteer het **tabblad Beoordelen en maken,** controleer de instellingen van uw organisatie en selecteer **vervolgens Maken.**

    ![Het deelvenster Beoordelen en maken](./media/entitlement-management-organization/organization-review-create.png)

## <a name="update-a-connected-organization"></a>Een verbonden organisatie bijwerken 

Als de verbonden organisatie wordt gewijzigd in een ander domein, de naam van de organisatie verandert of als u de sponsoren wilt wijzigen, kunt u de verbonden organisatie bijwerken door de instructies in deze sectie te volgen.

**Vereiste rol:** *Globale beheerder* of *gebruikersbeheerder*

1. Selecteer in Azure Portal de **Azure Active Directory** en selecteer vervolgens **Identity Governance.**

1. Selecteer in het linkerdeelvenster **Verbonden organisaties** en selecteer vervolgens de verbonden organisatie om deze te openen.

1. Selecteer bewerken in het overzichtsvenster van de verbonden organisatie **om** de naam, beschrijving of status van de organisatie te wijzigen.  

1. Selecteer in **het deelvenster Map + domein** de optie Map en domein bijwerken **om** over te gaan naar een andere map of een ander domein.

1. Selecteer in **het deelvensterPonsen** de optie **Interneponsoren toevoegen** of **Externeponsoren toevoegen om** een gebruiker toe te voegen als een sponsor. Als u een sponsor wilt verwijderen, selecteert u de sponsor en selecteert u in het rechterdeelvenster **Verwijderen.**


## <a name="delete-a-connected-organization"></a>Een verbonden organisatie verwijderen

Als u geen relatie meer hebt met een externe Azure AD-directory of -domein, kunt u de verbonden organisatie verwijderen.

**Vereiste rol:** *Globale beheerder* of *gebruikersbeheerder*

1. Selecteer in Azure Portal de **Azure Active Directory** en selecteer vervolgens **Identity Governance.**

1. Selecteer in het linkerdeelvenster **Verbonden organisaties** en selecteer vervolgens de verbonden organisatie om deze te openen.

1. Selecteer Verwijderen in het overzichtsvenster van de verbonden organisatie **om** het te verwijderen.

    ![De knop Verbonden organisatie verwijderen](./media/entitlement-management-organization/organization-delete.png)

## <a name="managing-a-connected-organization-programmatically"></a>Een verbonden organisatie programmatisch beheren

U kunt ook verbonden organisaties maken, op een lijst zetten, bijwerken en verwijderen met behulp van Microsoft Graph. Een gebruiker met een geschikte rol met een toepassing met de gedelegeerde machtiging kan de API aanroepen om connectedOrganization-objecten te beheren en voor deze `EntitlementManagement.ReadWrite.All` gebruikersponsoren in te stellen. [](/graph/api/resources/connectedorganization?view=graph-rest-beta&preserve-view=true)

## <a name="state-properties-of-connected-organizations"></a>Statuseigenschappen van verbonden organisaties

Er zijn momenteel twee verschillende typen statuseigenschappen voor verbonden organisaties in Azure AD-rechtenbeheer, geconfigureerd en voorgesteld: 

- Een geconfigureerde verbonden organisatie is een volledig functionele verbonden organisatie waarmee gebruikers binnen die organisatie toegang hebben tot pakketten. Wanneer een beheerder een nieuwe verbonden organisatie maakt in de  Azure Portal, heeft deze standaard de geconfigureerde status omdat de beheerder deze verbonden organisatie heeft gemaakt en wil gebruiken. Wanneer een verbonden organisatie bovendien programmatisch wordt gemaakt via de API, moet de standaardtoestand **worden** geconfigureerd, tenzij expliciet is ingesteld op een andere status. 

    Geconfigureerde verbonden organisaties worden in de pickers voor verbonden organisaties en vallen binnen het bereik van alle beleidsregels die zijn gericht op 'alle' verbonden organisaties.

- Een voorgestelde verbonden organisatie is een verbonden organisatie die automatisch is gemaakt, maar nog geen beheerder heeft gehad die de organisatie heeft gemaakt of goedgekeurd. Wanneer een gebruiker zich voor een toegangspakket buiten een geconfigureerde verbonden organisatie meldt, krijgen automatisch gemaakte verbonden organisaties de voorgestelde status omdat er geen beheerder is in de tenant die samenwerking heeft ingesteld.  
    
    Voorgestelde verbonden organisaties vallen niet binnen het bereik van de instelling 'alle geconfigureerde verbonden organisaties' voor alle beleidsregels, maar kunnen alleen worden gebruikt in beleidsregels die zijn gericht op specifieke organisaties. 

Alleen gebruikers van geconfigureerde verbonden organisaties kunnen toegangspakketten aanvragen die beschikbaar zijn voor gebruikers van alle geconfigureerde organisaties. Gebruikers van voorgestelde verbonden organisaties hebben een ervaring alsof er geen verbonden organisatie voor dat domein is; kan alleen toegangspakketten zien en aanvragen die zijn gericht op hun specifieke organisatie of die zijn gericht op elke gebruiker.

> [!NOTE]
> Als onderdeel van het uitrollen van deze nieuwe functie werden alle verbonden organisaties die zijn gemaakt vóór 09-09-2019 als **geconfigureerd beschouwd.** Als u een toegangspakket hebt dat gebruikers van elke organisatie heeft toegestaan om zich te registreren, moet u uw lijst met verbonden organisaties die vóór die datum zijn gemaakt, controleren om ervoor te zorgen dat geen van deze organisaties onjuist wordt gecategoriseerd als **geconfigureerd.**  Een beheerder kan de **eigenschap State waar** nodig bijwerken. Zie Een verbonden organisatie bijwerken [voor hulp.](#update-a-connected-organization)

## <a name="next-steps"></a>Volgende stappen

- [Toegang voor externe gebruikers beheren](./entitlement-management-external-users.md)
- [Toegang regelen voor gebruikers die zich niet in uw directory](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
