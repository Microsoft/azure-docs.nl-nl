---
title: 'Zelfstudie: Integratie van Azure Active Directory met iQualify LMS | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding configureert tussen Azure Active Directory en iQualify LMS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: bdd68a4f0dafd3b336bf5a3fb34399d725c9ff5c
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105626075"
---
# <a name="tutorial-azure-active-directory-integration-with-iqualify-lms"></a>Zelfstudie: Integratie van Azure Active Directory met iQualify LMS

In deze zelfstudie leert u hoe u iQualify LMS integreert met Azure Active Directory (Azure AD).
De integratie van iQualify LMS met Azure AD heeft de volgende voordelen:

* U kunt in Azure Active Directory bepalen wie er toegang heeft tot iQualify LMS.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij iQualify LMS (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om integratie van Azure Active Directory met iQualify LMS te configureren hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op iQualify LMS waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* iQualify LMS ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding
* iQualify LMS biedt ondersteuning voor het **Just-In-Time** inrichten van gebruikers

## <a name="adding-iqualify-lms-from-the-gallery"></a>iQualify LMS toevoegen vanuit de galerie

Om de integratie van iQualify LMS in Azure AD te configureren, moet u iQualify LMS vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Ga als volgt te werk om iQualify LMS vanuit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **iQualify LMS** in het zoekvak, selecteer **iQualify LMS** in het resultatenvenster en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![iQualify LMS in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte gaat u eenmalige aanmelding van Azure AD met iQualify LMS configureren en testen op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in iQualify LMS tot stand is gebracht.

Als u eenmalige aanmelding van Azure AD wilt configureren en testen met iQualify LMS, moet u de volgende procedures uitvoeren:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor iQualify LMS configureren](#configure-iqualify-lms-single-sign-on)** : de instellingen voor eenmalige aanmelding aan de toepassingszijde configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor iQualify LMS maken](#create-iqualify-lms-test-user)** : een tegenhanger voor Britta Simon maken in iQualify LMS die wordt gekoppeld aan de Azure AD-voorstelling van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD te configureren met iQualify LMS:

1. Ga in [Azure Portal](https://portal.azure.com/) naar de pagina met de integratie van de toepassing **iQualify LMS** en selecteer **Eenmalige aanmelding**.

   ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

   ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

   ![Schermopname die de Standaard SAML-configuratie toont, waar u de id en URL kunt invoeren en vervolgens Opslaan selecteert.](common/idp-intiated.png)
   
   1. In het tekstvak **Id** typt u een URL met het volgende patroon:

      * Productieomgeving: `https://<yourorg>.iqualify.com/`
      * Testomgeving: `https://<yourorg>.iqualify.io`

   2. In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon:

      * Productieomgeving: `https://<yourorg>.iqualify.com/auth/saml2/callback` 
      * Testomgeving: `https://<yourorg>.iqualify.io/auth/saml2/callback`

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

   ![Schermopname die Extra URL's instellen toont, waar u een aanmeldings-URL kunt invoeren.](common/metadata-upload-additional-signon.png)

   Typ in het tekstvak **Aanmeldings-URL** een URL met het volgende patroon:

      * Productieomgeving: `https://<yourorg>.iqualify.com/login`
      * Testomgeving: `https://<yourorg>.iqualify.io/login`

   > [!NOTE]
   > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [ondersteuningsteam van iQualify LMS](https://www.iqualify.com/) om deze waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

6. In de iQualify LMS-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Klik op het pictogram **Bewerken** om het dialoogvenster **Gebruikerskenmerken** te openen.

    ![Schermopname die Gebruikerskenmerken toont met het pictogram Bewerken geselecteerd.](common/edit-attribute.png)

7. Bewerk in het gedeelte **Gebruikersclaims** in het dialoogvenster **Gebruikerskenmerken** de claims met het **pictogram Bewerken** of voeg de claims toe door met **Nieuwe claim toevoegen** het kenmerk van het SAML-token te configureren, zoals wordt weergegeven in de bovenstaande afbeelding. Hierna voert u de volgende stappen uit:

    | Naam | Bronkenmerk|
    | --- | --- |
    | e-mail | user.userprincipalname |
    | first_name | user.givenname |
    | last_name | user.surname |
    | person_id | "uw kenmerk" |

    a. Klik op **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    ![Schermopname die Gebruikersclaims toont met de optie om een nieuwe claim toe te voegen.](common/new-save-attribute.png)

    ![Schermopname die het dialoogvenster Gebruikersclaims beheren toont, waarin u de beschreven waarden kunt invoeren.](common/new-attribute-details.png)

    b. In het tekstvak **Naam** typt u de naam van het kenmerk die voor die rij wordt weergegeven.

    c. Laat **Naamruimte** leeg.

    d. Selecteer Bron bij **Kenmerk**.

    e. Typ de kenmerkwaarde voor die rij in de lijst met **bronkenmerken**.

    f. Klik op **OK**.

    g. Klik op **Opslaan**.

    > [!Note]
    > Het kenmerk **person_id** is **optioneel**

8. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

9. In het gedeelte **iQualify LMS instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-iqualify-lms-single-sign-on"></a>Eenmalige aanmelding voor iQualify LMS configureren

1. Open een nieuw browservenster en meld u vervolgens bij uw iQualify-omgeving als beheerder aan.

1. Wanneer u bent aangemeld, klikt u rechtsboven op uw avatar en klikt u vervolgens op **Account settings**

    ![Accountinstellingen](./media/iqualify-tutorial/setting1.png)

1. Klik in het gebied met accountinstellingen op het lintmenu aan de linkerkant en klik op **INTEGRATIONS**

    ![INTEGRATIONS](./media/iqualify-tutorial/setting2.png)

1. Klik onder INTEGRATIONS op het pictogram **SAML**.

    ![Het pictogram SAML](./media/iqualify-tutorial/setting3.png)

1. Voer in het dialoogvenster **SAML Authentication Settings** de volgende stappen uit:

    ![SAML-verificatie-instellingen](./media/iqualify-tutorial/setting4.png)

    a. Plak in het vak **SAML SINGLE SIGN-ON SERVICE URL** de waarde van de **aanmeldings-URL** die is gekopieerd uit het configuratievenster van de Azure AD-toepassing.

    b. Plak in het vak **SAML LOGOUT URL** de waarde van de **afmeldings-URL** die is gekopieerd uit het configuratievenster van de Azure AD-toepassing.

    c. Open in Kladblok het certificaatbestand dat u hebt gedownload, kopieer de inhoud en plak deze in het vak **PUBLIC CERTIFICATE**.

    d. Voer in **LOGIN BUTTON LABEL** de naam in van de knop die op de aanmeldingspagina moet worden weergegeven.

    e. Klik op **OPSLAAN**.

    f. Klik op **UPDATE**.

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

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot iQualify LMS.

1. Selecteer **Bedrijfstoepassingen** in Azure Portal, selecteer **Alle toepassingen** en selecteer vervolgens **iQualify LMS**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **iQualify LMS** in de lijst met toepassingen.

    ![De koppeling iQualify LMS in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-iqualify-lms-test-user"></a>Een iQualify LMS-testgebruiker maken

In deze sectie wordt een gebruiker met de naam Britta Simon gemaakt in iQualify LMS. iQualify LMS biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker bestaat in iQualify LMS, wordt er een nieuwe gemaakt na verificatie.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel iQualify LMS klikt, wordt de aanmeldingspagina van iQualify LMS weergegeven. 

   ![aanmeldingspagina](./media/iqualify-tutorial/login.png) 

Klik op de knop **Sign in with Azure AD**. U wordt dan automatisch aangemeld bij uw iQualify LMS-toepassing.

Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster. 

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)