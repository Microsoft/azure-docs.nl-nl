---
title: 'Zelfstudie: Integratie van Azure Active Directory met Confirmit Horizons | Microsoft Docs'
description: Informatie over hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Confirmit Horizons.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: b1c4ab22db3bb9cadc783357bd9963c5de3b6f98
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97672675"
---
# <a name="tutorial-azure-active-directory-integration-with-confirmit-horizons"></a>Zelfstudie: Integratie van Azure Active Directory met Confirmit Horizons

In deze zelfstudie leert u hoe u Confirmit Horizons kunt integreren met Azure Active Directory (Azure AD).
De integratie van Confirmit Horizons met Azure Active Directory biedt de volgende voordelen:

* U kunt in Azure AD bepalen wie toegang tot Confirmit Horizons heeft.
* U kunt instellen dat gebruikers automatisch met hun Azure Active Directory-account worden aangemeld bij Confirmit Horizons (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met Confirmit Horizons hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op Confirmit Horizons waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Confirmit Horizons biedt ondersteuning voor door **SP en IDP** geïnitieerde eenmalige aanmelding

* Confirmit Horizons biedt ondersteuning voor het **Just-In-Time** inrichten van gebruikers

## <a name="adding-confirmit-horizons-from-the-gallery"></a>Confirmit Horizons toevoegen vanuit de galerie

Voor het configureren van de integratie van Confirmit Horizons in Azure Active Directory, moet u Confirmit Horizons vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u Confirmit Horizons wilt toevoegen vanuit de galerie, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Confirmit Horizons** in het zoekvak, selecteer **Confirmit Horizons** in het resultatenvenster en klik op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Confirmit Horizons in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte configureert en test u eenmalige aanmelding bij Azure Active Directory met Confirmit Horizons op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure Active Directory-gebruiker en de daaraan gerelateerde gebruiker in Confirmit Horizons tot stand is gebracht.

Voor het configureren en testen van eenmalige aanmelding bij Azure Active Directory met Confirmit Horizons, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Confirmit Horizons configureren](#configure-confirmit-horizons-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de clientzijde wil configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor Confirmit Horizons maken](#create-confirmit-horizons-test-user)** : als u een tegenhanger van Britta Simon in Confirmit Horizons wilt hebben die gekoppeld is aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor het configureren van eenmalige aanmelding bij Azure Active Directory met Confirmit Horizons, moet u de volgende stappen uitvoeren:

1. In de [Azure Portal](https://portal.azure.com/) selecteert u **Eenmalige aanmelding** op de integratiepagina van de toepassing **Confirmit Horizons**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

    ![Schermopname met de sectie "Standaard S A M L-configuratie" met de actie "Opslaan", het veld "Id" en het veld "Antwoord-U R L" gemarkeerd.](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u een URL met het volgende patroon:

    ```http
    https://<SUBDOMAIN>.confirmit.com/identity/AuthServices/<UNIQUEID>
    https://<SUBDOMAIN>.confirmit.com.au/identity/AuthServices/<UNIQUEID>
    https://<SUBDOMAIN>.confirmit.ca/identity/AuthServices/<UNIQUEID>
    https://<SUBDOMAIN>.confirmit.hk/identity/AuthServices/<UNIQUEID>
    https://sso.us.confirmit.com/<UNIQUEID>
    ```

    b. In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon:

    ```http
    https://<SUBDOMAIN>.confirmit.com/identity/AuthServices/<UNIQUEID>/acs
    https://<SUBDOMAIN>.confirmit.com.au/identity/AuthServices/<UNIQUEID>/acs
    https://<SUBDOMAIN>.confirmit.ca/identity/AuthServices/<UNIQUEID>/acs
    https://<SUBDOMAIN>.confirmit.hk/identity/AuthServices/<UNIQUEID>/acs
    https://sso.us.confirmit.com/<UNIQUEID>/saml/acs
    ```

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij Confirmit Horizons](common/metadata-upload-additional-signon.png)

    Typ in het tekstvak **Aanmeldings-URL** een URL met het volgende patroon:

    ```http
    https://<SUBDOMAIN>.confirmit.com/identity/<UNIQUEID>
    https://<SUBDOMAIN>.confirmit.com.au/identity/<UNIQUEID>
    https://<SUBDOMAIN>.confirmit.ca/identity/<UNIQUEID>
    https://<SUBDOMAIN>.confirmit.hk/identity/<UNIQUEID>
    https://sso.us.confirmit.com/<UNIQUEID>
    ```

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [ondersteuningsteam van Confirmit Horizons](mailto:support@confirmit.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

### <a name="configure-confirmit-horizons-single-sign-on"></a>Eenmalige aanmelding voor Confirmit Horizons configureren

Als u eenmalige aanmelding aan de **Confirmit Horizons**-zijde wilt configureren, moet u de **App-URL voor federatieve metagegevens** verzenden naar het [Confirmit Horizons-ondersteuningsteam](mailto:support@confirmit.com). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon** in.

    b. In het veld **Gebruikersnaam** typt u **brittasimon@yourcompanydomain.extension** .  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Confirmit Horizons.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **Confirmit Horizons**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Confirmit Horizons** in de lijst met toepassingen.

    ![De koppeling naar Confirmit Horizons in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-confirmit-horizons-test-user"></a>Confirmit Horizons-testgebruiker maken

In dit gedeelte wordt er een gebruiker met de naam Britta Simon gemaakt in Confirmit Horizons. Confirmit Horizons biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in Confirmit Horizons bestaat, wordt er een nieuwe gemaakt nadat deze is geverifieerd.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Confirmit Horizons klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van Confirmit Horizons in het toegangsvenster waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)