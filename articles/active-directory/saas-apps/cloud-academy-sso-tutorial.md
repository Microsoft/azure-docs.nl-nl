---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Cloud Academy - SSO'
description: In deze zelfstudie leert u hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Cloud Academy - SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/15/2020
ms.author: jeedes
ms.openlocfilehash: fa46d6e5c7f1007e3a90e22eb9d4f46e18251a28
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98729826"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-cloud-academy---sso"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Cloud Academy - SSO

In deze zelfstudie leert u hoe u Cloud Academy - SSO kunt integreren met Azure Active Directory (Azure AD). Wanneer u Cloud Academy - SSO integreert met Azure AD, kunt u het volgende doen:

* Gebruik Azure AD om te bepalen wie toegang heeft tot Cloud Academy - SSO.
* Zorg ervoor dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Cloud Academy - SSO.
* Uw accounts op één centrale locatie beheren: de Azure-portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Abonnement op Cloud Academy - SSO waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="tutorial-description"></a>Beschrijving van zelfstudie

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Cloud Academy - SSO biedt ondersteuning voor door **SP** geïnitieerde eenmalige aanmelding
* Cloud Academy - SSO biedt ondersteuning voor **Just-In-Time**-inrichting van gebruikers

## <a name="add-cloud-academy---sso-from-the-gallery"></a>Cloud Academy - SSO toevoegen vanuit de galerie

Als u de integratie van Cloud Academy - SSO met Azure AD wilt configureren, moet u Cloud Academy - SSO vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps:

1. Meld u aan bij de Azure-portal met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de knop **Azure Active Directory** in het linkerdeelvenster.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een toepassing toe te voegen.
1. Voer in de sectie **Toevoegen uit de galerie** **Cloud Academy - SSO** in het zoekvak in.
1. Selecteer **Cloud Academy - SSO** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-cloud-academy---sso"></a>Eenmalige aanmelding van Azure AD voor Cloud Academy - SSO configureren en testen

U configureert en test eenmalige aanmelding van Azure AD met Cloud Academy - SSO met behulp van een testgebruiker met de naam **B. Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Cloud Academy - SSO.

Voltooi de volgende stappen van hoog niveau om eenmalige aanmelding van Azure AD met Cloud Academy - SSO te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** om eenmalige aanmelding van Azure AD te testen.
    1. **[Toegang verlenen aan de testgebruiker](#grant-access-to-the-test-user)** zodat de gebruiker eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding configureren voor Cloud Academy-SSO](#configure-single-sign-on-for-cloud-academy)** aan de toepassingszijde.
    1. **[Een testgebruiker maken voor Cloud Academy-SSO](#create-a-cloud-academy-test-user)** als een tegenhanger voor de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in de Azure-portal:

1. Selecteer in de Azure-portal op de integratiepagina van de toepassing **Cloud Academy - SSO**, in de sectie **Beheren**, de optie **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken:

   ![Schermopname van het potloodpictogram voor het bewerken van de standaard-SAML-configuratie.](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. In het tekstvak **Aanmeldings-URL** typt u een van de volgende URL's:
    
    | Aanmeldings-URL |
    |--------------|
    | `https://cloudacademy.com/login/enterprise/` |
    | `https://app.qa.com/login/enterprise/` |
    |
    
    b. In het tekstvak **Antwoord-URL** typt u een van de volgende URL's:
    
    | Antwoord-URL |
    |--------------|
    | `https://cloudacademy.com/labs/social/complete/saml/` |
    | `https://app.qa.com/labs/social/complete/saml/` |
    |
1. Ga naar de pagina **Eenmalige aanmelding met SAML instellen** en selecteer in de sectie **SAML-handtekeningcertificaat** de kopieerknop om de **App-URL voor federatieve metagegevens** te kopiëren. Sla de URL op.

    ![Schermopname van de kopieerknop voor de app-URL voor federatieve metagegevens.](common/copy-metadataurl.png)

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

In deze sectie geeft u B. Simon toestemming om eenmalige aanmelding van Azure te gebruiken door deze gebruiker toegang te verlenen tot Cloud Academy - SSO.

1. Selecteer in de Azure-portal **Bedrijfstoepassingen** en selecteer **Alle toepassingen**.
1. Selecteer **Cloud Academy - SSO** in de lijst met toepassingen.
1. Selecteer op de overzichtspagina van de app in de sectie **Beheren** de optie **Gebruikers en groepen**:
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**:
1. Selecteer in het dialoogvenster **Gebruikers en groepen** **B.Simon** in de lijst **Gebruikers** en selecteer vervolgens de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

## <a name="configure-single-sign-on-for-cloud-academy"></a>Eenmalige aanmelding voor Cloud Academy configureren

1. Meld u in een ander browservenster als beheerder aan bij de bedrijfssite van Cloud Academy - SSO.

1. Selecteer de naam van uw bedrijf en selecteer vervolgens **Settings & Integrations** in het menu dat wordt weergegeven:

    ![Schermopname van de optie Settings & Integrations.](./media/cloud-academy-sso-tutorial/config-1.PNG)

1. Ga op de pagina **Settings & Integrations** naar het tabblad **Integrations** en selecteer de kaart **SSO**:

    ![Schermopname van de kaart SSO op het tabblad Integrations.](./media/cloud-academy-sso-tutorial/config-2.PNG)

1. Voltooi de volgende stappen op deze pagina:

    ![Schermopname met de pagina Integrations > SSO.](./media/cloud-academy-sso-tutorial/config-3.PNG)

    a. Plak in het vak **Entity ID URL** de waarde van de entiteit-id die u uit Azure Portal hebt gekopieerd.

    b. Plak in het vak **URL voor eenmalige aanmelding** de aanmeldings-URL die u hebt gekopieerd uit Azure Portal.

    c. Open vanuit Azure Portal het gedownloade certificaat (Base64) in Kladblok. Plak de inhoud in het vak **Certificate**.

    d. Laat in het vak **Name ID Format** de standaardwaarde, `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`, staan.

1. Selecteer **Opslaan**.

    > [!NOTE]
    > Zie [Setting Up Single Sign-On](https://support.cloudacademy.com/hc/articles/360043908452-Setting-Up-Single-Sign-On) (Eenmalige aanmelding instellen) voor meer informatie over het configureren van Cloud Academy - SSO.

### <a name="create-a-cloud-academy-test-user"></a>Een testgebruiker maken voor Cloud Academy - SSO

In deze sectie wordt een gebruiker met de naam Britta Simon gemaakt in Cloud Academy - SSO. Cloud Academy - SSO biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in Cloud Academy - SSO, wordt er na verificatie een nieuwe gemaakt.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van Cloud Academy - SSO, waar u de aanmeldingsstroom kunt initiëren. 

* Ga rechtstreeks naar de aanmeldings-URL van Cloud Academy - SSO en initieer de aanmeldingsstroom daar.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u in Mijn apps op de tegel Cloud Academy - SSO klikt, wordt u omgeleid naar de aanmeldings-URL van Cloud Academy - SSO. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u Cloud Academy - SSO hebt geconfigureerd, kunt u sessiebeheer afdwingen. Hierdoor worden exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).