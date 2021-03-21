---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met SKYSITE | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en SKYSITE.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/27/2019
ms.author: jeedes
ms.openlocfilehash: 9d4413c47e40a611fb1559b32aac1e32a71d1998
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92515981"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-skysite"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met SKYSITE

In deze zelfstudie leert u hoe u SKYSITE met Azure Active Directory (Azure AD) integreert. Wanneer u SKYSITE integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie er toegang heeft tot SKYSITE.
* Ervoor zorgen dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij SKYSITE.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement waarvoor eenmalige aanmelding bij SKYSITE is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SKYSITE ondersteunt door **IDP** geïnitieerde SSO

* SKYSITE biedt ondersteuning voor het **Just In Time** inrichten van gebruikers

## <a name="adding-skysite-from-the-gallery"></a>SKYSITE toevoegen vanuit de galerie

Als u de integratie van SKYSITE in Azure AD wilt configureren, moet u SKYSITE vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **SKYSITE**.
1. Selecteer **SKYSITE** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-skysite"></a>Eenmalige aanmelding van Azure AD configureren en testen voor SKYSITE

Configureer en test eenmalige aanmelding van Azure AD met SKYSITE met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in SKYSITE.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met SKYSITE te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij SKYSITE configureren](#configure-skysite-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een SKYSITE-testgebruiker maken](#create-skysite-test-user)** : als u een equivalent van B.Simon in SKYSITE wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in **Azure Portal** naar de overzichtspagina van de integratie van [SKYSITE](https://portal.azure.com/), klik op het tabblad **Eigenschappen** en voer de volgende stappen uit: 

    ![Eigenschappen voor eenmalige aanmelding](./media/skysite-tutorial/config05.png)

    * Kopieer de waarde van **URL van gebruikerstoegang** om deze verderop in de zelfstudie te plakken in de sectie **Eenmalige aanmelding SKYSITE configureren**.

1. Ga op de pagina van de toepassingsintegratie van **SKYSITE** naar **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **SAML-basisconfiguratie** is de toepassing vooraf geconfigureerd in de door **IDP** gestarte modus en zijn de benodigde URL's al vooraf ingevuld met Azure. De gebruiker moet de configuratie opslaan door op de knop **Opslaan** te klikken.

1. In de SKYSITE-toepassing worden de SAML-beweringen in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van de SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Klik op het pictogram **Bewerken** om het dialoogvenster gebruikerskenmerken te openen.

    ![Schermopname die Gebruikerskenmerken toont met het pictogram Bewerken geselecteerd.](common/edit-attribute.png)

1. Bovendien verwacht de SKYSITE-toepassing nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Voer in het gedeelte **Gebruikerskenmerken en -claims** in het dialoogvenster **Groepsclaims (preview)** de volgende stappen uit:

    a. Klik op de **pen** naast **Groepen die zijn geretourneerd in claim**.

    ![Schermopname die Gebruikersclaims toont met de optie om een nieuwe claim toe te voegen.](./media/skysite-tutorial/config01.png)

    ![Schermopname die het dialoogvenster Gebruikersclaims beheren toont, waarin u de beschreven waarden kunt invoeren.](./media/skysite-tutorial/config02.png)

    b. Selecteer **Alle groepen** in de lijst met keuzerondjes.

    c. Selecteer **Bronkenmerk** bij **Groep-ID**.

    d. Klik op **Opslaan**.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **SKYSITE instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in Azure Portal.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot SKYSITE.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst met toepassingen de optie **SKYSITE**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="configure-skysite-sso"></a>Eenmalige aanmelding voor SKYSITE configureren

1. Open een nieuw webbrowservenster en meld u als beheerder bij uw bedrijfssite van SKYSITE aan en voer de volgende stappen uit:

4. Klik in de rechter bovenhoek van de pagina op **Instellingen** en navigeer vervolgens naar **Accountinstelling**.

    ![Schermopname van Accountinstelling geselecteerd in Instellingen.](./media/skysite-tutorial/config03.png)

5. Voer in het tabblad **Eenmalige aanmelding** de volgende stappen uit:

    ![Schermopname van het tabblad Eenmalige aanmelding, waarin u de beschreven waarden kunt invoeren.](./media/skysite-tutorial/config04.png)

    a. Plak in het tekstvak **Aanmeld-URL van id-provider** de waarde van **URL voor gebruikerstoegang**, die u hebt gekopieerd uit het tabblad **Eigenschappen** in Azure Portal.

    b. Klik op **Certificaat uploaden** om het Base64-gecodeerde certificaat te uploaden dat u hebt gedownload uit Azure Portal.

    c. Klik op **Opslaan**.

### <a name="create-skysite-test-user"></a>Een SKYSITE-testgebruiker maken

In deze sectie wordt een gebruiker met de naam Britta Simon gemaakt in SKYSITE. SKYSITE biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker bestaat in SKYSITE, wordt er een nieuwe gemaakt na verificatie.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel SKYSITE in het toegangsvenster klikt, wordt u automatisch aangemeld bij het SKYSITE-exemplaar waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Probeer SKYSITE met Azure Active Directory](https://aad.portal.azure.com/)