---
title: 'Zelfstudie: Azure Active Directory-integratie met Hosted Graphite | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Hosted Graphite.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.openlocfilehash: 786c6309f685af6b8e42108c490ce86c7844bc81
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92443134"
---
# <a name="tutorial-azure-active-directory-integration-with-hosted-graphite"></a>Zelfstudie: Azure Active Directory-integratie met Hosted Graphite

In deze zelfstudie leert u hoe u Hosted Graphite kunt integreren met Azure Active Directory (Azure AD).
De integratie van Hosted Graphite met Azure Active Directory biedt de volgende voordelen:

* U kunt in Azure Active Directory bepalen wie er toegang heeft tot Hosted Graphite.
* U kunt inschakelen dat gebruikers automatisch met hun Azure Active Directory-account worden aangemeld bij Hosted Graphite (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om Azure Active Directory-integratie te configureren met Hosted Graphite hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Abonnement op Hosted Graphite met eenmalige aanmelding ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Hosted Graphite ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding
* Hosted Graphite biedt ondersteuning voor het **Just In Time** inrichten van gebruikers

## <a name="adding-hosted-graphite-from-the-gallery"></a>Hosted Graphite uit de galerie toevoegen

Voor het configureren van de integratie van Hosted Graphite met Microsoft Azure Active Directory moet u Hosted Graphite vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u Hosted Graphite wilt toevoegen uit de galerie, moet u de volgende stappen uitvoeren:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **Hosted Graphite**, selecteer **Hosted Graphite** in het resultaatvenster en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Hosted Graphite in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u eenmalige aanmelding met Azure Active Directory bij Hosted Graphite configureren en testen op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure Active Directory-gebruiker en de daaraan gerelateerde gebruiker in Hosted Graphite tot stand is gebracht.

Om eenmalige aanmelding met Azure Active Directory bij Hosted Graphite te configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding bij Hosted Graphite configureren](#configure-hosted-graphite-single-sign-on)**: als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor Hosted Graphite maken](#create-hosted-graphite-test-user)**: als u een tegenhanger van Britta Simon in Hosted Graphite wilt hebben die is gekoppeld aan de Azure Active Directory-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit als u eenmalige aanmelding met Azure Active Directory wilt configureren voor Hosted Graphite:

1. Ga in de [Azure Portal](https://portal.azure.com/) naar de overzichtspagina van de integratie voor **Hosted Graphite** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

    ![Schermopname die de Standaard SAML-configuratie toont, waar u de id en URL kunt invoeren en vervolgens Opslaan selecteert.](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://www.hostedgraphite.com/metadata/<user id>`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://www.hostedgraphite.com/complete/saml/<user id>`

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![Schermopname die Extra URL's instellen toont, waar u een aanmeldings-URL kunt invoeren.](common/metadata-upload-additional-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://www.hostedgraphite.com/login/saml/<user id>/`

    > [!NOTE]
    > Houd er rekening mee dat dit niet de werkelijke waarden zijn. U moet deze waarden bijwerken met de werkelijke id, de antwoord-URL en de aanmeldings-URL. Om deze waarden te krijgen, gaat u naar Toegang -> SAML instellen aan toepassingszijde of neemt u contact op met het [ondersteuningsteam van Hosted Graphite](mailto:help@hostedgraphite.com).

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

7. In de sectie **Hosted Graphite instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-hosted-graphite-single-sign-on"></a>Eenmalige aanmelding bij Hosted Graphite configureren

1. Meld u als beheerder aan bij uw Hosted Graphite-tenant.

2. Ga naar de **SAML-installatiepagina** in de zijbalk (**Toegang-> SAML-instellen**).

    ![Schermopname met het menu Toegang waarin SAML-installatie is geselecteerd.](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_000.png)

3. Controleer of deze URL's overeenkomen met uw configuratie uitgevoerd op de sectie **SAML-basisconfiguratie** van de Azure Portal.

    ![Schermopname van de basale SAML-configuratie.](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_001.png)

4. In tekstvakken **Entiteit of verlener-id** en **Aanmeldings-URL voor eenmalige aanmelding** plakt u de waarde van **Azure Active Directory-id** en **Aanmeldings-URL** die u hebt gekopieerd vanuit de Azure Portal.

    ![Schermopname met vermeldingen voor de id-provider.](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_002.png)

5. Selecteer **Alleen-lezen** als **Standaard gebruikersrol**.

    ![Schermopname met de standaard gebruikersrol, Alleen-lezen.](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_004.png)

6. Open in Kladblok het met Base 64 gecodeerde certificaat dat u hebt gedownload uit de Azure-portal, kopieer de inhoud ervan naar het Klembord en plak deze vervolgens in het tekstvak **X.509 Certificate**.

    ![Schermopname met het certificaat X dot 509.](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_005.png)

7. Klik op de knop **Save**.

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

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Hosted Graphite.

1. Selecteer **Bedrijfstoepassingen** in de Azure Portal, selecteer **Alle toepassingen** en selecteer vervolgens **Hosted Graphite**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst met toepassingen **Hosted Graphite**.

    ![De link van Hosted Graphite in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-hosted-graphite-test-user"></a>Hosted Graphite-testgebruiker maken

In dit gedeelte wordt een gebruiker met de naam Britta Simon gemaakt in Hosted Graphite. Hosted Graphite biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker bestaat in Hosted Graphite, wordt er een nieuwe gemaakt na verificatie.

> [!NOTE]
> Als u een gebruiker handmatig hebt gemaakt wilt, moet u contact op met het ondersteuningsteam van Hosted Graphite via <mailto:help@hostedgraphite.com>.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Hosted Graphite in het toegangsvenster klikt, wordt u automatisch aangemeld bij het Hosted Graphite-exemplaar waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)