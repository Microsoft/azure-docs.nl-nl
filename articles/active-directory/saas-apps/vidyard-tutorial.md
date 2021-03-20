---
title: 'Zelfstudie: Azure Active Directory-integratie met Vidyard | Microsoft Docs'
description: Informatie over het configureren van een eenmalige aanmelding tussen Azure Active Directory en Vidyard.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 4176c92d48b67b9f9207f22ebd8939b5ec1437ee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92636745"
---
# <a name="tutorial-azure-active-directory-integration-with-vidyard"></a>Zelfstudie: Azure Active Directory-integratie met Vidyard

In deze zelfstudie leert u hoe u Vidyard kunt integreren met Azure Active Directory (Azure AD).
De integratie van Vidyard met Azure Active Directory biedt u de volgende voordelen:

* U kunt in Azure Active Directory beheren wie toegang tot Vidyard heeft.
* U kunt inschakelen dat gebruikers automatisch met hun Azure Active Directory-account worden aangemeld bij Vidyard (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om integratie van Azure Active Directory met Vidyard te configureren, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Een abonnement op Vidyard waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Vidyard biedt ondersteuning voor door **SP** en **IDP** geïnitieerde eenmalige aanmelding (SSO)

* Vidyard biedt ondersteuning voor het **Just In Time** inrichten van gebruikers

## <a name="adding-vidyard-from-the-gallery"></a>Vidyard uit de galerie toevoegen

Voor het configureren van de integratie van Vidyard in Azure Active Directory moet u Vidyard uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u Vidyard uit de galerie wilt toevoegen, moet u de volgende stappen uitvoeren:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Vidyard** in het zoekvak, selecteer **Vidyard** in het venster met resultaten en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Vidyard in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte configureert en test u eenmalige aanmelding van Azure Active Directory met Vidyard op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure Active Directory-gebruiker en de daaraan gerelateerde gebruiker in Vidyard tot stand is gebracht.

Om Azure AD-eenmalige aanmelding met Vidyard te configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Vidyard voor eenmalige aanmelding configureren](#configure-vidyard-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor Vidyard maken](#create-vidyard-test-user)** : als u een tegenhanger van Britta Simon in Vidyard wilt hebben die is gekoppeld aan de Azure Active Directory-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Om eenmalige aanmelding van Azure Active Directory met Vidyard te configureren, moet u de volgende stappen uitvoeren:

1. Selecteer in de [Microsoft Azure-portal](https://portal.azure.com/), op de pagina voor de integratie van de toepassing **Vidyard**, de optie **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In het gedeelte **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

    ![Schermopname die de Standaard SAML-configuratie toont, waar u de id en URL kunt invoeren en vervolgens Opslaan selecteert.](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://secure.vidyard.com/sso/saml/<unique id>/metadata`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://secure.vidyard.com/sso/saml/<unique id>/consume`

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![Schermopname die Extra URL's instellen toont, waar u een aanmeldings-URL kunt invoeren.](common/metadata-upload-additional-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://secure.vidyard.com/sso/saml/<unique id>/login`

    > [!NOTE]
    > Dit zijn geen echte waarden. U gaat deze waarden bijwerken met de daadwerkelijke id, antwoord-URL en URL voor eenmalige aanmelding, wat later in de zelfstudie nog wordt uitgelegd. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

7. In het gedeelte **Vidyard instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-vidyard-single-sign-on"></a>Eenmalige aanmelding voor Vidyard configureren

1. Meld u in een ander browservenster als een beheerder aan bij de bedrijfssite van Vidyard Software.

2. Selecteer in het Vidyard-dashboard **Groep** > **Beveiliging**

    ![Schermopname waarin Security is geselecteerd in Group op de Vidyard Software-site.](./media/vidyard-tutorial/configure1.png)

3. Klik op het tabblad **Nieuw profiel**.

    ![Schermopname van de knop New Profile.](./media/vidyard-tutorial/configure2.png)

4. Voer in het gedeelte **SAML-configuratie** de volgende stappen uit:

    ![Schermopname met de sectie SAML Configuration, waar u de beschreven waarden kunt invoeren.](./media/vidyard-tutorial/configure3.png)

    a. Voer een algemene profielnaam in het tekstvak **Profielnaam** in.

    b. Kopieer de waarde van de **SSO-gebruikersaanmeldingspagina** en plak deze in het tekstvak **Aanmeldings-URL** in het gedeelte **Standaard SAML-configuratie** in de Azure-portal.

    c. Kopieer de waarde van **ACS-URL** en plak deze in het tekstvak **Antwoord-URL** in het gedeelte **Standaard SAML-configuratie** in de Azure-portal.

    d. Kopieer de waarde van de **Verlener-/Metagegevens-URL** en plak deze in het tekstvak **Id** in het gedeelte **Standaard SAML-configuratie** in de Azure-portal.

    e. Open het certificaat dat u hebt gedownload uit de Microsoft Azure-portal in Kladblok en plak het in het tekstvak **X.509-certificaat**.

    f. Plak in het tekstvak **SAML Eindpunt-URL** de waarde van de uit de Azure Portal gekopieerde **Aanmeldings-URL**.

    g. Klik op **Bevestigen**.

5. Selecteer op het tabblad Eenmalige aanmelding **Toewijzen** naast een bestaand profiel

    ![Schermopname met de knop Assign voor het Azure AD SSO-profiel.](./media/vidyard-tutorial/configure4.png)

    > [!NOTE]
    > Zodra u een SSO-profiel hebt gemaakt, wijst u het toe aan een of meer groepen waarvoor gebruikers toegang nodig hebben via Azure. Als de gebruiker niet bestaat in de groep waaraan deze is toegewezen, wordt door Vidyard automatisch een gebruikersaccount gemaakt en wordt in realtime een rol toegewezen.

6. Selecteer uw organisatiegroep, die zichtbaar is in de **Groepen die beschikbaar zijn voor toewijzing**.

    ![Schermopname met de sectie Assign SAML Configuration to Organizations, waar u uw groep kunt selecteren.](./media/vidyard-tutorial/configure5.png)

7. U kunt de toegewezen groepen zien onder de **Momenteel toegewezen groepen**. Selecteer een rol voor de groep conform uw organisatie en klik op **Bevestigen**.

    ![Schermopname met de sectie Assign SAML Configuration to Organizations, waar u een rol kunt selecteren.](./media/vidyard-tutorial/configure6.png)

    > [!NOTE]
    > Raadpleeg dit [document](https://knowledge.vidyard.com/hc/articles/360009990033-SAML-based-Single-Sign-On-SSO-in-Vidyard) voor meer informatie.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon** in.
  
    b. In het veld **Gebruikersnaam** typt u brittasimon@yourcompanydomain.extension. Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Vidyard.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **Vidyard**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Vidyard** in de lijst met toepassingen.

    ![De koppeling naar Vidyard in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-vidyard-test-user"></a>Vidyard-testgebruiker maken

In dit gedeelte wordt een gebruiker met de naam Britta Simon gemaakt in Vidyard. Vidyard biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in Vidyard bestaat, wordt er na verificatie een nieuwe gemaakt.

>[!Note]
>Als u handmatig een gebruiker wilt maken, neem dan contact op met het [ondersteuningsteam van Vidyard](mailto:support@vidyard.com).

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Vidyard klikt, wordt u automatisch aangemeld bij het exemplaar van Vidyard waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)