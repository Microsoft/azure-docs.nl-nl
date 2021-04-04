---
title: 'Zelfstudie: Azure Active Directory-integratie met Image Relay | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Image Relay.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: jeedes
ms.openlocfilehash: b76c2e346adb6c2afd146b0d73c8f20165145bac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92460356"
---
# <a name="tutorial-azure-active-directory-integration-with-image-relay"></a>Zelfstudie: Azure Active Directory-integratie met Image Relay

In deze zelfstudie leert u hoe u Image Relay kunt integreren met Azure Active Directory (Azure AD).
De integratie van Image Relay met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang heeft tot Image Relay.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Image Relay (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om Azure AD-integratie te configureren met Image Relay hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op Image Relay waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Image Relay ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-image-relay-from-the-gallery"></a>Image Relay toevoegen vanuit de galerie

Om de integratie van Image Relay in Azure AD te configureren, moet u Image Relay vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Image Relay toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Image Relay** in het zoekvak, selecteer **Image Relay** in het deelvenster met resultaten en klik op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Image Relay in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u Azure AD-eenmalige aanmelding bij Image Relay configureren en testen op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Image Relay tot stand is gebracht.

Als u eenmalige aanmelding van Azure AD met Image Relay wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding met Image Relay configureren](#configure-image-relay-single-sign-on)**: als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wil configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker maken voor Image Relay](#create-image-relay-test-user)**: als u een tegenhanger van Britta Simon in Image Relay wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit als u Azure AD-eenmalige aanmelding wilt configureren met Image Relay:

1. In de [Azure-portal](https://portal.azure.com/) selecteert u **Eenmalige aanmelding** op de integratiepagina van de toepassing **Image Relay**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Gegevens van domein en URL's voor eenmalige aanmelding van Image Relay](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<companyname>.imagerelay.com/`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://<companyname>.imagerelay.com/sso/metadata`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [ondersteuningsteam van Image Relay](http://support.imagerelay.com/) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. In de sectie **Image Relay instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-image-relay-single-sign-on"></a>Eenmalige aanmelding configureren voor Image Relay

1. Meld u in een andere browser als beheerder aan bij de bedrijfssite van Image Relay.

2. Klik in de werkbalk bovenaan op de workload **Users & Permissions**.

    ![Schermopname toont 'Gebruikers en machtigingen' die zijn geselecteerd op de werkbalk.](./media/imagerelay-tutorial/tutorial_imagerelay_06.png) 

3. Klik op **Nieuwe machtiging maken**.

    ![Schermopname toont een tekstvak waarin de machtigingstitel moet worden ingevoerd en een optie om het machtigingstype te kiezen.](./media/imagerelay-tutorial/tutorial_imagerelay_08.png)

4. Schakel in de workload **Single Sign On Settings** (Instellingen voor eenmalige aanmelding) het selectievakje **This Group can only sign-in via Single Sign On** (Deze groep kan alleen aanmelden met eenmalige aanmelding) in en klik op **Save** (Opslaan).

    ![Schermopname toont de instellingen van eenmalige aanmelding waar u de optie kunt selecteren.](./media/imagerelay-tutorial/tutorial_imagerelay_09.png) 

5. Ga naar **Account Settings**.

    ![Schermopname toont de optie 'Accountinstellingen' op de werkbalk.](./media/imagerelay-tutorial/tutorial_imagerelay_10.png) 

6. Ga naar de workload **Single Sign On Settings** (Instellingen voor eenmalige aanmelding).

    ![Schermopname toont de menuoptie 'Instellingen van enkele aanmelding'.](./media/imagerelay-tutorial/tutorial_imagerelay_11.png)

7. Voer in het dialoogvenster **SAML-instellingen** de volgende stappen uit:

    ![Schermopname toont het dialoogvenster 'SAML-instellingen' waar u de informatie kunt invoeren.](./media/imagerelay-tutorial/tutorial_imagerelay_12.png)

    a. Plak in het tekstvak **Login URL** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    b. Plak in het tekstvak **Logout URL** de waarde van **Afmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    c. Voor **Name Id Format** (Indeling naam-id) selecteert u **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**.

    d. Voor **Binding Options for Requests from the Service Provider (Image Relay)** (Bindingsopties voor aanvragen van de serviceprovider (Image Relay)) selecteert u **POST Binding** (POST-binding).

    e. Klik onder **x.509 Certificate** op **Update Certificate**.

    ![Schermopname toont de optie voor het bijwerken van het certificaat.](./media/imagerelay-tutorial/tutorial_imagerelay_17.png)

    f. Open in Kladblok het certificaat dat u hebt gedownload, kopieer de inhoud en plak deze in het tekstvak **X.509-certificaat**.

    ![Schermopname toont het certificaat 'x dot 509'.](./media/imagerelay-tutorial/tutorial_imagerelay_18.png)

    g. Selecteer in de sectie **Just-In-Time inrichten van gebruikers** de optie **Just-In-Time inrichten van gebruikers inschakelen**.

    ![Schermopname toont de sectie 'Just-In-Time-gebruikersinrichting' waarin het besturingselement 'Inschakelen' is geselecteerd.](./media/imagerelay-tutorial/tutorial_imagerelay_19.png)

    h. Selecteer de machtigingsgroep (bijvoorbeeld **SSO Basic**) die zich alleen via eenmalige aanmelding mag aanmelden.

    ![Schermopname toont de sectie 'Just-In-Time-gebruikersinrichting' waarin 'S S O Basic' is geselecteerd.](./media/imagerelay-tutorial/tutorial_imagerelay_20.png)

    i. Klik op **Opslaan**.

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

In deze sectie zorgt u ervoor dat Britta Simon gebruik kan maken van eenmalige aanmelding van Azure, door haar toegang te geven tot Image Relay.

1. Selecteer **Bedrijfstoepassingen** in de Azure-portal, selecteer **Alle toepassingen** en selecteer vervolgens **Image Relay**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Image Relay** in de lijst met toepassingen.

    ![De koppeling Image Relay in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-image-relay-test-user"></a>Image Relay-testgebruiker maken

Het doel van deze sectie is om in Image Relay een gebruiker te maken met de naam Britta Simon.

**Voer de volgende stappen uit als u in Image Relay een gebruiker met de naam Britta Simon wilt maken:**

1. Meld u als beheerder aan bij de bedrijfssite van Image Relay.

2. Ga naar **Users & Permissions** en selecteer **Create SSO User**.

    ![Schermopname waarin 'Maak S S O gebruiker' in het menu is geselecteerd.](./media/imagerelay-tutorial/tutorial_imagerelay_21.png) 

3. Voer het **e-mailadres**, de **voornaam**, **achternaam** en het **bedrijf** in van de gebruiker die u wilt inrichten en selecteer een machtigingsgroep (bijvoorbeeld SSO Basic). Dit is de groep die zich alleen via eenmalige aanmelding kan aanmelden.

    ![Schermopname toont 'Maak een S S O-gebruikerspagina' waar u de vereiste gegevens kunt invoeren.](./media/imagerelay-tutorial/tutorial_imagerelay_22.png)

4. Klik op **Create**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Image Relay klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van Image Relay waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)