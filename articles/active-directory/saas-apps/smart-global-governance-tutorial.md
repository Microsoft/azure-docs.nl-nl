---
title: 'Zelfstudie: Integratie van Azure AD SSO met Smart Global Governance'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Smart Global Governance.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/04/2020
ms.author: jeedes
ms.openlocfilehash: 5a2169db47ca5a6adcddbcc9558161370b896a62
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95997066"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-smart-global-governance"></a>Zelfstudie: Eenmalige aanmelding (SSO) van Azure Active Directory integreren met Smart Global governance

In deze zelfstudie leert u hoe u Smart Global Governance integreert met Azure Active Directory (Azure AD). Wanneer u Smart Global governance integreert met Azure AD, kunt u het volgende doen:

* Azure AD gebruiken om te bepalen wie er toegang heeft tot Smart Global Governance.
* Ervoor zorgen dat uw gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Smart Global Governance.
* Uw accounts op één centrale locatie beheren: de Azure-portal.

Zie [Eenmalige aanmelding voor toepassingen in Azure Active Directory](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Smart Global Governance met eenmalige aanmelding ingeschakeld.

## <a name="tutorial-description"></a>Beschrijving van zelfstudie

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

Smart Global Governance ondersteunt door SP geïnitieerde en door IdP geïnitieerde SSO.

Zodra u Smart Global Governance hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-smart-global-governance-from-the-gallery"></a>Smart Global Governance toevoegen vanuit de galerie

Om de integratie van Smart Global Governance in Azure AD te configureren, moet u Smart Global Governance vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de knop **Azure Active Directory** in het linkerdeelvenster.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een toepassing toe te voegen.
1. Ga in het gedeelte **Toevoegen uit de galerie** naar het zoekvak en voer **Smart Global Governance** in.
1. Selecteer **Smart Global Governance** in de zoekresultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-smart-global-governance"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Smart Global Governance

U gaat Azure AD SSO configureren en testen met Smart Global Governance met behulp van een testgebruiker met de naam B.Simon. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Smart Global Governance.

Volg deze algemene stappen om Azure AD SSO te configureren en testen met Smart Global Governance:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** om eenmalige aanmelding van Azure AD te testen.
    1. **[Toegang verlenen aan de testgebruiker](#grant-access-to-the-test-user)** zodat de gebruiker eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor Smart Global Governance configureren](#configure-smart-global-governance-sso)** aan de kant van de toepassing.
    1. **[Een testgebruiker voor Smart Global Governance maken](#create-a-smart-global-governance-test-user)** als een tegenhanger voor de Azure AD-voorstelling van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in de Azure-portal:

1. Zoek in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Smart Global Governance** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken:

   ![Potloodpictogram voor Standaard-SAML-configuratie](common/edit-urls.png)

1. In het gedeelte **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door IDP geïnitieerde modus wilt configureren.

    a. Voer in het vak **Id** een van deze URL's in:

    - `https://eu-fr-south.console.smartglobalprivacy.com/platform/authentication-saml2/metadata`
    - `https://eu-fr-south.console.smartglobalprivacy.com/dpo/authentication-saml2/metadata`

    b. Voer in het vak **Antwoord-URL** een van deze URL's in:

    - `https://eu-fr-south.console.smartglobalprivacy.com/platform/authentication-saml2/acs`
    - `https://eu-fr-south.console.smartglobalprivacy.com/dpo/authentication-saml2/acs`

1. Als u de toepassing in de door SP geïnitieerde modus wilt configureren, selecteert u **Extra URL's instellen** en voert u de volgende stap uit.

   - Voer in het vak **Aanmeldings-URL** een van deze URL's in:

    - `https://eu-fr-south.console.smartglobalprivacy.com/dpo`
    - `https://eu-fr-south.console.smartglobalprivacy.com/platform`

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** naar de sectie **SAML-handtekeningcertificaat** en selecteer de koppeling **Downloaden** voor **Certificaat (Raw)** om het certificaat te downloaden en op te slaan op de computer:

    ![De koppeling om het certificaat te downloaden](common/certificateraw.png)

1. In de sectie **Smart Global Governance instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In dit gedeelte gaat u een testgebruiker met de naam B.Simon maken in de Azure-portal.

1. Selecteer in de Azure-portal aan de linkerkant **Azure Active Directory**. Selecteer **Gebruikers** en daarna **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Ga als volgt te werk in het venster dat verschijnt:
   1. Voer in het vak **Naam** de naam **B.Simon** in.  
   1. Voer in het vak **Gebruikersnaam** \<username>@\<companydomain> in.\<extension>. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Selecteer **Maken**.

### <a name="grant-access-to-the-test-user"></a>De testgebruiker toegang geven

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door die gebruiker toegang te verlenen tot Smart Global Governance.

1. Selecteer in de Azure-portal **Bedrijfstoepassingen** en selecteer **Alle toepassingen**.
1. Selecteer **Smart Global Governance** in de lijst met toepassingen.
1. Ga op de overzichtspagina van de app naar de sectie **Beheren** en selecteer **Gebruikers en groepen**:

   ![Gebruikers en groepen selecteren](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**:

    ![Gebruiker toevoegen selecteren](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** **B.Simon** in de lijst **Gebruikers** en selecteer vervolgens de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

## <a name="configure-smart-global-governance-sso"></a>Eenmalige aanmelding configureren in Smart Global Governance

Als u eenmalige aanmelding wilt configureren in Smart Global Governance, moet u het gedownloade Raw-certificaat en de juiste URL's die u hebt gekopieerd uit de Azure-portal verzenden naar het [ondersteuningsteam van Smart Global Governance](mailto:support.tech@smartglobal.com). Zij zorgen er dan voor dat de SAML-verbinding voor eenmalige aanmelding aan beide kanten juist is.

### <a name="create-a-smart-global-governance-test-user"></a>Een testgebruiker voor Smart Global Governance maken

Neem contact op met het [ondersteuningsteam van Smart Global Governance](mailto:support.tech@smartglobal.com) om een gebruiker met de naam B.Simon toe te voegen in Smart Global Governance. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van het toegangsvenster.

Wanneer u in het toegangsvenster de tegel Smart Global Governance selecteert, moet u automatisch worden aangemeld bij het exemplaar van Smart Global Governance waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Inleiding tot het toegangsvenster](../user-help/my-apps-portal-end-user-access.md) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Smart Global Governance uitproberen met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Smart Global Governance beveiligen met geavanceerde zichtbaarheid en besturingselementen](/cloud-app-security/proxy-intro-aad)