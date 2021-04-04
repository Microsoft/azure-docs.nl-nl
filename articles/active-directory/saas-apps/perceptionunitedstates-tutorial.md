---
title: 'Zelfstudie: Integratie van Azure Active Directory met Perception United States (Non-UltiPro) | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding kunt configureren tussen Azure Active Directory en Perception United States (Non-UltiPro).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 3fc16a869c176074467a0b21880937d7753602b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92515522"
---
# <a name="tutorial-azure-active-directory-integration-with-perception-united-states-non-ultipro"></a>Zelfstudie: Integratie van Azure Active Directory met Perception United States (Non-UltiPro)

In deze zelfstudie leert u hoe u Perception United States (Non-UltiPro) kunt integreren met Azure Active Directory (Azure AD).
De integratie van Perception United States (Non-UltiPro) met Azure Active Directory biedt de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot Perception United States (Non-UltiPro).
* U kunt uw gebruikers zich automatisch laten aanmelden bij Perception United States (Non-UltiPro) (eenmalige aanmelding) met hun Azure Active Directory-account.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u integratie tussen Azure AD met Perception United States (Non-UltiPro) wilt configureren, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een Perception United States (Non-UltiPro)-abonnement waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Perception United States (Non-UltiPro) ondersteunt door **IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-perception-united-states-non-ultipro-from-the-gallery"></a>Perception United States (Non-UltiPro) toevoegen vanuit de galerie

Voor het configureren van de integratie van Perception United States (Non-UltiPro) met Microsoft Azure Active Directory moet u Perception United States (Non-UltiPro) vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Perception United States (Non-UltiPro) toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **Perception United States (Non-UltiPro)** , selecteer **Perception United States (Non-UltiPro)** in het resultatenvenster en klik op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Perception United States (Non-UltiPro) in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte gaat u Azure AD-eenmalige aanmelding met Perception United States (Non-UltiPro) configureren en testen met behulp van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Perception United States (Non-UltiPro) tot stand is gebracht.

Als u eenmalige aanmelding van Azure AD wilt configureren en testen met Perception United States (Non-UltiPro), moet u de volgende stappen uitvoeren:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding van Perception United States (Non-UltiPro) configureren](#configure-perception-united-states-non-ultipro-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Perception United States (Non-UltiPro)-testgebruiker maken](#create-perception-united-states-non-ultipro-test-user)** : als u een tegenhanger van Britta Simon in Perception United States (Non-UltiPro) wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit als u integratie tussen Azure AD met Perception United States (Non-UltiPro) wilt configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de **Perception United States (Non-UltiPro)** -toepassing de optie **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Op de pagina **Eenmalige aanmelding instellen met SAML** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding bij Perception United States (Non-UltiPro)-domeinen en -URL's](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u een URL: `https://perception.kanjoya.com/sp`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://perception.kanjoya.com/sso?idp=<entity_id>`

    c. Voor de URI-codering van de **Perception United States (Non-UltiPro)** -toepassing moet de waarde **Azure AD-id** zijn ingesteld op <entity_id>. Deze haalt u op uit de sectie **Perception United States (Non-UltiPro) instellen**. Gebruik de volgende koppeling om de waarde met URI-codering op te halen: **http://www.url-encode-decode.com/** .

    d. Nadat de waarde met URI-codering is opgehaald, moet deze worden gecombineerd met de **Antwoord-URL** zoals hieronder wordt vermeld.

    `https://perception.kanjoya.com/sso?idp=<URI encooded entity_id>`
    
    e. Plak de bovenstaande waarde in het tekstvak **Antwoord-URL**.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. In de sectie **Perception United States (Non-UltiPro) instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL   

### <a name="configure-perception-united-states-non-ultipro-single-sign-on"></a>Eenmalige aanmelding van Perception United States (Non-UltiPro) configureren

1. Meld u in een ander browservenster als beheerder aan bij de bedrijfssite van Perception United States (Non-UltiPro).

2. Klik op de hoofdwerkbalk op **Accountinstellingen**.

    ![Schermopname van 'Accountinstellingen' geselecteerd op de hoofdwerkbalk.](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_user.png)

3. Voer op de pagina **Accountinstellingen** de volgende stappen uit:

    ![Perception United States (Non-UltiPro)-gebruiker](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_account.png)

    a. Typ in het tekstvak **Bedrijfsnaam** de naam van het **bedrijf**.
    
    b. Typ in het tekstvak **Accountnaam** de naam van het **account**.

    c. Typ in het tekstvak **Standaarde-mailadres voor beantwoording** het geldige **e-mailadres**.

    d. Selecteer **Id-provider SSO** als **SAML 2.0**.

4. Voer op de pagina **SSO-configuratie** de volgende stappen uit:

    ![SSO-configuratie van Perception United States (Non-UltiPro)](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_ssoconfig.png)

    a. Selecteer **SAML NameID-type** als **E-MAILADRES**.

    b. Typ in het tekstvak **Naam SSO-configuratie** de naam van de **configuratie**.
    
    c. Plak in het tekstvak **Naam id-provider** de waarde van **Azure AD-id** die u hebt gekopieerd in de Azure-portal. 

    d. Voer in het tekstvak **SAML-domein** het domein in, zoals @contoso.com.

    e. Klik op **Opnieuw uploaden** om het **XML-bestand met metagegevens** te uploaden.

    f. Klik op **Update**.

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

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Perception United States (Non-UltiPro).

1. Selecteer **Bedrijfstoepassingen** in de Azure-portal, selecteer **Alle toepassingen** en selecteer vervolgens **Perception United States (Non-UltiPro)** .

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Perception United States (Non-UltiPro)** in de lijst met toepassingen.

    ![De Perception United States (Non-UltiPro)-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-perception-united-states-non-ultipro-test-user"></a>Perception United States (Non-UltiPro)-testgebruiker maken

In deze sectie maakt u een gebruiker met de naam Britta Simon in Perception United States (Non-UltiPro). Werk samen met het [ondersteuningsteam van Perception United States (Non-UltiPro)](https://www.ultimatesoftware.com/Contact/ContactUs) om de gebruikers toe te voegen op het Perception United States (Non-UltiPro)-platform.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Perception United States (Non-UltiPro) in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van Perception United States (Non-UltiPro) waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)