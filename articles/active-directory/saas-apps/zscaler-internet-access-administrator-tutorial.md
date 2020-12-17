---
title: 'Zelfstudie: Integratie van Azure Active Directory met Zscaler Internet Access Administrator | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Zscaler Internet Access Administrator.
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
ms.openlocfilehash: d74057e32b6f16bdb6dae3d96ac46c5cc93571aa
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97609102"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-internet-access-administrator"></a>Zelfstudie: Integratie van Azure Active Directory met Zscaler Internet Access Administrator

In deze zelfstudie leert u hoe u Zscaler Internet Access Administrator integreert met Azure Active Directory (Azure AD).
Als u Zscaler Internet Access Administrator integreert met Azure AD hebt u de volgende voordelen:

* U kunt in Azure AD bepalen wie toegang heeft tot Zscaler Internet Access Administrator.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Zscaler Internet Access Administrator (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u integratie tussen Azure AD met Zscaler Internet Access Administrator wilt configureren, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op Zscaler Internet Access Administrator

> [!NOTE]
> Deze integratie is ook beschikbaar voor gebruik vanuit de Azure AD US Government Cloud-omgeving. U kunt deze toepassing vinden in de toepassingsgalerie van Azure AD US Government Cloud en deze op dezelfde manier configureren als vanuit een openbare cloud.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Zscaler Internet Access Administrator ondersteunt met **IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-zscaler-internet-access-administrator-from-the-gallery"></a>Zscaler Internet Access Administrator toevoegen vanuit de galerie

Als u de integratie van Zscaler Internet Access Administrator met Azure AD wilt configureren, dient u Zscaler Internet Access Administrator vanuit de galerie toe te voegen aan uw lijst met beheerde SaaS-apps.

**Als u Zscaler Internet Access Administrator vanuit de galerie wilt toevoegen, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Zscaler Internet Access Administrator** in het zoekvak, selecteer **Zscaler Internet Access Administrator** in het deelvenster met resultaten en klik op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Zscaler Internet Access Administrator in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u eenmalige aanmelding van Azure AD configureren en testen met Zscaler Internet Access Administrator op basis van testgebruiker **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Zscaler Internet Access Administrator tot stand is gebracht.

Als u eenmalige aanmelding van Azure AD wilt configureren en testen met Zscaler Internet Access Administrator, dient u de volgende bouwstenen te voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding van Zscaler Internet Access Administrator configureren](#configure-zscaler-internet-access-administrator-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de clientzijde wil configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker van Zscaler Internet Access Administrator maken](#create-zscaler-internet-access-administrator-test-user)** : als u een equivalent van Britta Simon in Zscaler Internet Access Administrator wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Als u integratie tussen eenmalige aanmelding van Azure AD met Zscaler Internet Access Administrator wilt configureren, voert u de volgende stappen uit:

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de pagina voor toepassingsintegratie van **Zscaler Internet Access Administrator** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Klik op de pagina **Eenmalige aanmelding met SAML instellen** u de knop **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Informatie over eenmalige aanmelding van Zscaler Internet Access Administrator-domein en URL's](common/idp-intiated.png)

    a. Typ in het tekstvak **Id** een URL overeenkomstig wat u nodig hebt:

    | Id |
    |------------|
    | `https://admin.zscaler.net` |
    | `https://admin.zscalerone.net` |
    | `https://admin.zscalertwo.net` |
    | `https://admin.zscalerthree.net` |
    | `https://admin.zscloud.net` |
    | `https://admin.zscalerbeta.net` |

    b. Typ in het tekstvak **Antwoord-URL** een URL overeenkomstig wat u nodig hebt:

    | Antwoord-URL |
    |-----------|
    | `https://admin.zscaler.net/adminsso.do` |
    | `https://admin.zscalerone.net/adminsso.do` |
    | `https://admin.zscalertwo.net/adminsso.do` |
    | `https://admin.zscalerthree.net/adminsso.do` |
    | `https://admin.zscloud.net/adminsso.do` |
    | `https://admin.zscalerbeta.net/adminsso.do` |

5. De Zscaler Internet Access Administrator-toepassing verwacht dat de SAML-beweringen in een bepaalde indeling staan. Configureer de volgende claims voor deze toepassing. U kunt de waarden van deze kenmerken vanuit de sectie **Gebruikerskenmerken en claims** op de integratiepagina van de toepassing-beheren. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op de knop **Bewerken** om het dialoogvenster **Gebruikerskenmerken en claims** te openen.

    ![Koppeling Kenmerk](./media/zscaler-internet-access-administrator-tutorial/tutorial_zscaler-internet_attribute.png)

6. In de sectie **Gebruikersclaims** in het dialoogvenster **Gebruikerskenmerken** configureert u het kenmerk van het SAML-token zoals wordt weergegeven in de bovenstaande afbeelding en voert u de volgende stappen uit:

    | Naam  | Bronkenmerk  |
    | ---------| ------------ |
    | Rol | user.assignedroles |

    a. Klik op **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    ![Schermopname die Gebruikersclaims toont met de optie om een nieuwe claim toe te voegen.](./common/new-save-attribute.png)
    
    ![Schermopname die het dialoogvenster Gebruikersclaims beheren toont, waarin u de beschreven waarden kunt invoeren.](./common/new-attribute-details.png)

    b. Selecteer de kenmerkwaarde in de lijst **Bronkenmerk**.

    c. Klik op **OK**.

    d. Klik op **Opslaan**.

    > [!NOTE]
    > Klik [hier](../develop/active-directory-enterprise-app-role-management.md) als u wilt weten hoe u rollen in Azure AD moet configureren

7. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

8. In de sectie **Zscaler Internet Access Administrator instellen** kopieert u de juiste URL('s) overeenkomstig wat u nodig hebt.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-zscaler-internet-access-administrator-single-sign-on"></a>Eenmalige aanmelding van Zscaler Internet Access Administrator configureren

1. Meld u in een ander browservenster als beheerder aan bij de beheerinterface van Zscaler Internet Access Administrator.

2. Ga naar **Administration > Administrator Management**, voer de volgende stappen uit en klik op Save:

    ![Schermopname van Administrator Management met opties voor het inschakelen van SAML-verificatie, het uploaden van een SSL-certificaat en het opgeven van een certificaatverlener.](./media/zscaler-internet-access-administrator-tutorial/AdminSSO.png "Beheer")

    a. Selecteer **Enable SAML Authentication** (vinkje).

    b. Klik op **Uploaden** om het Azure SAML-ondertekeningscertificaat te uploaden dat u in de Azure-portal in het **openbare SSL-certificaat** hebt gedownload.

    c. Voor extra beveiliging voegt u desgewenst in het vak **Issuer** gegevens toe om de uitgever van het SAML-antwoord te controleren.

3. Voer de volgende stappen uit in de beheerinterface:

    ![Schermopname van de beheerdersinterface, waar u de stappen kunt uitvoeren.](./media/zscaler-internet-access-administrator-tutorial/ic800207.png)

    a. Beweeg de muisaanwijzer boven het menu **Activering** linksonder.

    b. Klik op **Activeren**.

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

In deze sectie hebt u Britta Simon in staat gesteld gebruik te maken van eenmalige aanmelding van Azure door haar toegang te geven tot Zscaler Internet Access Administrator.

1. Selecteer in de Azure-portal **Bedrijfstoepassingen**, selecteer **Alle toepassingen** en selecteer vervolgens **Zscaler Internet Access Administrator**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Typ **Zscaler Internet Access Administrator** in de lijst met toepassingen en selecteer deze.

    ![Koppeling Zscaler Internet Access Administrator in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-zscaler-internet-access-administrator-test-user"></a>Testgebruiker van Zscaler Internet Access Administrator maken

Het doel van deze sectie is om in Zscaler Internet Access Administrator een gebruiker te maken met de naam Britta Simon. TZscaler Internet Access Administrator biedt geen ondersteuning voor Just-In-Time inrichting voor eenmalige aanmelding bij Administrator. U moet handmatig een Administrator-account maken.
Instructies hiervoor vindt u in deze documentatie van Zscaler:

https://help.zscaler.com/zia/adding-admins

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Zscaler Internet Access Administrator klikt, wordt u automatisch aangemeld bij de instantie van Zscaler Internet Access Administrator waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)
