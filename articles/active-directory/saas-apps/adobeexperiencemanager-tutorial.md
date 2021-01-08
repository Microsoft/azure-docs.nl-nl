---
title: 'Zelfstudie: Azure Active Directory-integratie met Adobe Experience Manager | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Adobe Experience Manager configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: 9a98a77b9cc89b7a1a05e676048775aa38c83733
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97671961"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Zelfstudie: Azure Active Directory-integratie met Adobe Experience Manager

In deze zelfstudie leert u hoe u Adobe Experience Manager integreert met Azure Active Directory (Azure AD).
De integratie van Adobe Experience Manager met Azure AD heeft de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang heeft tot Adobe Experience Manager.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Adobe Experience Manager (eenmalige aanmelding of SSO).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van de Azure AD-integratie met Adobe Experience Manager hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op Adobe Experience Manager waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Adobe Experience Manager ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding

* Adobe Experience Manager biedt ondersteuning voor het **just in time** inrichten van gebruikers

## <a name="adding-adobe-experience-manager-from-the-gallery"></a>Adobe Experience Manager toevoegen vanuit de galerie

Om de integratie van Adobe Experience Manager te configureren in Azure AD, moet u Adobe Experience Manager vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Ga als volgt te werk om Adobe Experience Manager vanuit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Adobe Experience Manager** in het zoekvak, selecteer **Adobe Experience Manager** in het deelvenster met resultaten en klik vervolgens op **Toevoegen** om de toepassing toe te voegen.

    ![Adobe Experience Manager toevoegen vanuit de galerie](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u Azure AD-eenmalige aanmelding met [toepassingsnaam] configureren en testen met behulp van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerd gebruiker in [toepassingsnaam] tot stand is gebracht.

Als u Azure AD-eenmalige aanmelding met [toepassingsnaam] wilt configureren en testen, moet u de volgende stappen uitvoeren:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Adobe Experience Manager configureren](#configure-adobe-experience-manager-single-sign-on)**: de instellingen voor eenmalige aanmelding aan de clientzijde configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Een testgebruiker voor Adobe Experience Manager definiëren](#create-adobe-experience-manager-test-user)**: een tegenhanger voor Britta Simon definiëren in Adobe Experience Manager die is gekoppeld aan de Azure AD-voorstelling van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor het configureren van Azure AD-eenmalige aanmelding met [toepassingsnaam], moet u de volgende stappen uitvoeren:

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de overzichtspagina van de integratie van **Adobe Experience Manager** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

    ![Schermopname toont de sectie 'SAML-basisconfiguratie' met de tekstvakken 'Id' en 'Antwoord-URL' gemarkeerd.](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u een unieke waarde die u daarna ook definieert op uw AEM-server.

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<AEM Server Url>/saml_login`

    > [!NOTE]
    > De waarde van de antwoord-URL is niet de echte waarde. Werk de waarde voor de antwoord-URL bij met de werkelijke antwoord-URL. Neem contact op met het [klantondersteuningsteam van Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html) om deze waarde op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![Gegevens van domein en URL's voor eenmalige aanmelding van Adobe Experience Manager](common/metadata-upload-additional-signon.png)

    Typ in het tekstvak **Aanmeldings-URL** de URL van uw Adobe Experience Manager-server.

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

7. Kopieer in het gedeelte **Adobe Experience Manager instellen** de juiste URL('s) op basis van wat u nodig hebt.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-adobe-experience-manager-single-sign-on"></a>Eenmalige aanmelding configureren voor Adobe Experience Manager

1. Open in een ander browservenster de beheerportal van **Adobe Experience Manager**.

2. Select **Settings** > **Security** > **Users**.

    ![Schermopname toont de gebruikerstegel in Adobe Experience Manager.](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user.png)

3. Selecteer **Administrator** of een andere relevante gebruiker.

    ![Schermopname waarin de Beheergebruiker is gemarkeerd.](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin6.png)

4. Selecteer **Account settings** > **Manage TrustStore**.

    ![Schermopname toont 'TrustStore beheren' onder 'Accountinstellingen'.](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_managetrust.png)

5. Klik onder **Add Certificate from CER file** op **Select Certificate File**. Blader naar het certificaatbestand dat u hebt al gedownload uit de Azure-portal en selecteer het bestand.

    ![Schermopname die de knop 'Certificaatbestand selecteren' uitlicht.](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user2.png)

6. Het certificaat wordt toegevoegd aan de TrustStore. Noteer de alias van het certificaat.

    ![Schermopname toont dat het certificaat is toegevoegd aan de TrustStore.](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin7.png)

7. Selecteer **authentication-service** op de pagina **Users**.

    ![Schermopname waarin de verificatieservice op het scherm is gemarkeerd.](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin8.png)

8. Selecteer **Account settings** > **Create/Manage KeyStore**. Maak een KeyStore door een wachtwoord op te geven.

    ![Schermopname waarin 'KeyStore beheren' is gemarkeerd.](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin9.png)

9. Ga terug naar het beginscherm. Selecteer vervolgens **Settings** > **Operations** > **Web Console**.

    ![Schermopname waarin 'Web-console' onder 'Bewerkingen' binnen de sectie 'Instellingen' is gemarkeerd.](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin1.png)

    Hiermee opent u de configuratiepagina.

    ![De knop voor enkelvoudige aanmelding configureren](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin2.png)

10. Zoek **Adobe Granite SAML 2.0 Authentication Handler**. Selecteer vervolgens het pictogram **Add**.

    ![Schermopname waarin Adobe Granite SAML 2.0 Authentication Handler is gemarkeerd.](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin3.png)

11. Voer de volgende handelingen uit op deze pagina.

    ![De knop voor enkelvoudige aanmelding configureren](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin4.png)

    a. Typ **/** in het vak **Path**.

    b. Typ in het vak **IDP URL** de waarde voor **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    c. Typ in het vak **IDP Certificate Alias** de waarde voor **Alias** die u hebt toegevoegd in TrustStore.

    d. Typ in het vak **Service Provider Entity ID** de unieke waarde voor **Id** die u hebt geconfigureerd in de Azure-portal.

    e. Typ in het vak **Assertion Consumer Service URL** de waarde voor **Antwoord-URL** die u hebt geconfigureerd in de Azure-portal.

    f. Typ in het vak **Password of Key Store** de waarde voor **Password** die u hebt ingesteld in de KeyStore.

    g. Typ in het vak **UserID Attribute** de waarde voor **NameId** of een andere gebruikers-id die relevant is in uw situatie.

    h. Selecteer **Autocreate CRX Users**.

    i. Typ in het vak **Logout URL** de unieke waarde voor **Afmeldings-URL** die u hebt verkregen via de Azure-portal.

    j. Selecteer **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon** in.

    b. In het veld **Gebruikersnaam** typt u **brittasimon\@yourcompanydomain.extension**  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte geeft u Britta Simon de mogelijkheid om eenmalige aanmelding van Azure te gebruiken door haar toegang te geven tot Adobe Experience Manager.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **Adobe Experience Manager**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Adobe Experience Manager** in de lijst met toepassingen.

    ![De koppeling naar Adobe Experience Manager in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-adobe-experience-manager-test-user"></a>Adobe Experience Manager-testgebruiker maken

In deze sectie maakt u een gebruiker met de naam Britta Simon in Adobe Experience Manager. Als u de optie **Autocreate CRX Users** hebt geselecteerd, worden gebruikers automatisch gemaakt na een geslaagde verificatie.

Als u de gebruikers handmatig wilt maken, neemt u contact op met het [ondersteuningsteam van Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html) om de gebruikers toe te voegen op het Adobe Experience Manager-platform.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Adobe Experience Manager klikt, wordt u als het goed is automatisch aangemeld bij het exemplaar van Adobe Experience Manager waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)