---
title: 'Zelfstudie: Integratie van Azure Active Directory met T&E Express | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en T&E Express.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: f44a67b4943c6897f82aedf47d493b36e5e2d3b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92521874"
---
# <a name="tutorial-azure-active-directory-integration-with-te-express"></a>Zelfstudie: Integratie van Azure Active Directory met T&E Express

In deze zelfstudie leert u hoe u T&E Express kunt integreren met Azure Active Directory (Azure AD).
Integratie van T&E Express met Azure AD heeft de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot T&E Express.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij T&E Express (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om de integratie van Azure AD met T&E Express te configureren:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op T&E Express waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* T&E Express ondersteunt door **IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-te-express-from-the-gallery"></a>T&E Express toevoegen vanuit de galerie

Voor het configureren van de integratie van T&E Express in Azure AD, moet u T&E Express vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u T&E Express wilt toevoegen vanuit de galerie, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **T&E Express** in het zoekvak, selecteer **T&E Express** in het resultaatvenster en klik op de knop **Toevoegen** om de toepassing toe te voegen.

    ![T&E Express in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u eenmalige aanmelding van Azure AD met T&E Express op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in T&E Express tot stand is gebracht.

Als u eenmalige aanmelding van Azure AD wilt configureren en testen met T&E Express, moet u de volgende procedures uitvoeren:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor T&E Express configureren](#configure-te-express-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de clientzijde wil configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[T&E Express-testgebruiker maken](#create-te-express-test-user)** : om een equivalent van Britta Simon te hebben in T&E Express die is gekoppeld aan de bijbehorende gebruiker in Azure AD.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding in Azure AD te configureren voor T&E Express:

1. Selecteer in de [Azure Portal](https://portal.azure.com/) op de integratiepagina voor de toepassing **T&E Express** de optie **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Op de pagina **Eenmalige aanmelding instellen met SAML** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij T&E Express](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u de waarde als URL met het volgende patroon: `https://<domain>.tyeexpress.com`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<domain>.tyeexpress.com/authorize/samlConsume.aspx`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Wij raden u aan hiervoor de unieke waarde van de tekenreeks in de id te gebruiken. Neem contact op met het [klantenserviceteam van T&E Express](https://www.tyeexpress.com/contacto.aspx) om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer in de sectie **T&E Express instellen** de juiste URL('s) op basis van wat u nodig hebt.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-te-express-single-sign-on"></a>Eenmalige aanmelding voor T&E Express configureren

1. Als u eenmalige aanmelding aan de kant van **T&E Express** wilt configureren, meldt u zich met beheerdersreferenties aan bij T&E Express zonder SAML-eenmalige aanmelding.

1. Klik op het tabblad **Admin** op **SAML-domein** om de pagina SAML-instellingen te openen.

    ![Schermopname met SAML Domain geselecteerd in het menu Admin.](./media/tyeexpress-tutorial/tye-SAML.png)

1. Schakel de optie **Activar(Activate)** van **No** in **SI(Yes)** . Plak in het tekstvak **Identity Provider Metadata** de metagegevens-XML die u hebt gedownload van Azure Portal.

    ![Schermopname van de pagina Dominio SAML, waar u de metagegevens kunt invoeren.](./media/tyeexpress-tutorial/tyeAdmin.png)

1. Klik op de knop **Guardar (Save)** om de instellingen op te slaan.

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

In deze sectie stelt u Britta Simon in staat om eenmalige aanmelding van Azure te gebruiken door haar toegang te geven tot T&E Express.

1. Selecteer **Bedrijfstoepassingen** in Azure Portal, selecteer **Alle toepassingen** en selecteer **T&E Express**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **T&E Express** in de lijst met toepassingen.

    ![De koppeling naar T&E Express in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-te-express-test-user"></a>Testgebruiker maken voor T&E Express

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij T&E Express, moeten ze worden ingericht bij T&E Express. In het geval van T&E Express is dat een handmatige taak.

**Ga als volgt te werk om een gebruikersaccount in te richten:**

1. Meld u bij uw T&E Express-bedrijfssite aan als beheerder.

1. Klik onder het label Admin op Users om de hoofdpagina voor gebruikers te openen.

    ![Schermopname met Users geselecteerd in het menu Admin.](./media/tyeexpress-tutorial/tye-adminusers.png)

1. Klik op de startpagina op **+** om de gebruikers toe te voegen.

    ![Schermopname met het pluspictogram voor het toevoegen van gebruikers.](./media/tyeexpress-tutorial/tye-usershome.png)

1. Voer alle verplichte gegevens in het formulier in en klik op Opslaan om de details op te slaan.

    ![Schermopname met de sectie User Information, waar u de betreffende waarden kunt invoeren.](./media/tyeexpress-tutorial/tye-usersadd.png)

    ![Schermopname met de secties Approvers en Assistant, waar u de betreffende waarden kunt invoeren.](./media/tyeexpress-tutorial/tye-userssave.png)

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel T&E Express klikt, wordt u automatisch aangemeld bij het exemplaar van T&E Express waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)