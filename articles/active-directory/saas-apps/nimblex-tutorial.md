---
title: 'Zelfstudie: Azure Active Directory-integratie met Nimblex | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Nimblex.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: jeedes
ms.openlocfilehash: 528411f7ef662e90bce98a52b67caa9123fad6ad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92522446"
---
# <a name="tutorial-azure-active-directory-integration-with-nimblex"></a>Zelfstudie: Azure Active Directory-integratie met Nimblex

In deze zelfstudie leert u hoe u Nimblex kunt integreren met Azure Active Directory (Azure AD).
De integratie van Nimblex met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot Nimblex.
* U kunt instellen dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij Nimblex (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met Nimblex hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op Nimblex waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Nimblex ondersteunt door **SP** geïnitieerde eenmalige aanmelding

* Nimblex ondersteunt het **Just-In-Time** inrichten van gebruikers

## <a name="adding-nimblex-from-the-gallery"></a>Nimblex toevoegen uit de galerie

Voor het configureren van de integratie van Nimblex in Azure AD, moet u Nimblex uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u Nimblex uit de galerie wilt toevoegen, moet u de volgende stappen uitvoeren:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **Nimblex**, selecteer **Nimblex** in het resultaatvenster en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Nimblex in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u eenmalige aanmelding bij Azure AD met Nimblex configureren en testen op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Nimblex tot stand is gebracht.

Als u eenmalige aanmelding van Azure AD wilt configureren en testen met Nimblex, voert u de volgende stappen uit:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Nimblex configureren](#configure-nimblex-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de clientzijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Een testgebruiker voor Nimblex maken](#create-nimblex-test-user)** : als u in Nimblex een tegenhanger van Britta Simon wilt hebben die is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit als u eenmalige aanmelding bij Azure AD wilt configureren met Nimblex:

1. In [Azure Portal](https://portal.azure.com/) selecteert u **Eenmalige aanmelding** op de integratiepagina van de toepassing **Nimblex**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over Nimblex-domein en eenmalige aanmeldings-URL](common/sp-identifier-reply.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<YOUR APPLICATION PATH>/Login.aspx`

    b. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<YOUR APPLICATION PATH>/`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<path-to-application>/SamlReply.aspx`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [ondersteuningsteam van Nimblex](mailto:support@ebms.com.au) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer in het gedeelte **Nimblex instellen** de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-nimblex-single-sign-on"></a>Eenmalige aanmelding voor Nimblex configureren

1. Meld u in een ander browservenster als een beveiligingsbeheerder aan bij Nimblex.

2. Klik rechtsboven op de pagina op het logo **Instellingen**.

    ![Schermopname van het pictogram Instellingen.](./media/nimblex-tutorial/tutorial_nimblex_settings.png)

3. Klik op de pagina **Configuratiescherm** onder de sectie **Beveiliging** op **Eenmalige aanmelding**.

    ![Schermopname met Eenmalige aanmelding geselecteerd in het menu Beveiliging.](./media/nimblex-tutorial/tutorial_nimblex_single.png)

4. Selecteer op de pagina **Eenmalige aanmelding beheren** uw instantienaam en klik op **Bewerken**.

    ![Schermopname van Eenmalige aanmelding beheren waar u Bewerken kunt selecteren.](./media/nimblex-tutorial/tutorial_nimblex_saml.png)

5. Voer op de pagina **SSO-provider** de volgende stappen uit:

    ![Schermopname van SSO-provider bewerken, waarin u de beschreven waarden kunt invoeren.](./media/nimblex-tutorial/tutorial_nimblex_sso.png)

    a. Voer in het tekstvak **Omschrijving** de instantienaam in.

    b. Open in Kladblok het base-64 gecodeerde certificaat dat u hebt gedownload vanuit Azure Portal, kopieer de inhoud en plak deze in het vak **Certificaat**.

    c. Plak in het tekstvak **URL voor eenmalige aanmelding van identiteitsprovider** de waarde van de **aanmeldings-URL** die u hebt gekopieerd uit Azure Portal.

    d. Klik op **Opslaan**.

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

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Nimblex.

1. Selecteer **Bedrijfstoepassingen** in Azure Portal, selecteer **Alle toepassingen** en selecteer vervolgens **Nimblex**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Nimblex** in de lijst met toepassingen.

    ![De Nimblex-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onderaan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-nimblex-test-user"></a>Een Nimblex-testgebruiker maken

In dit gedeelte wordt een gebruiker met de naam Britta Simon gemaakt in Nimblex. Nimblex biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in Nimblex bestaat, wordt na verificatie een nieuwe gemaakt.

>[!Note]
>Als u handmatig een gebruiker moet maken, neemt u contact op met het [klantondersteuningsteam van Nimblex](mailto:support@ebms.com.au).

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Nimblex in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van Nimblex waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)