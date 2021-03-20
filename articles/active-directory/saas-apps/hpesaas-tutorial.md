---
title: 'Zelfstudie: Microsoft Azure Active Directory-integratie met HPE SaaS | Microsoft Docs'
description: Informatie over hoe u eenmalige aanmelding configureert tussen Azure Active Directory en HPE SaaS.
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
ms.openlocfilehash: 7c67f2261120f9b043bca47ea45c032962e4171e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92443157"
---
# <a name="tutorial-azure-active-directory-integration-with-hpe-saas"></a>Zelfstudie: Azure Active Directory-integratie met HPE SaaS

In deze zelfstudie leert u hoe u HPE SaaS kunt integreren met Microsoft Azure Active Directory (Azure AD).
De integratie van HPE SaaS met Microsoft Azure Active Directory biedt de volgende voordelen:

* U kunt in Microsoft Azure Active Directory bepalen wie er toegang heeft tot HPE SaaS.
* U kunt inschakelen dat gebruikers automatisch met hun Microsoft Azure Active Directory-account worden aangemeld bij HPE SaaS (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om Microsoft Azure Active Directory-integratie te configureren met HPE SaaS hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op HPE SaaS waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* HPE SaaS ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-hpe-saas-from-the-gallery"></a>HPE SaaS toevoegen vanuit de galerie

Voor het configureren van de integratie van HPE SaaS met Microsoft Azure Active Directory moet u HPE SaaS vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u HPE SaaS wilt toevoegen vanuit de galerie, moet u de volgende stappen uitvoeren:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **HPE SaaS**, selecteer **HPE SaaS** in het resultaatvenster en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![HPE SaaS in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u eenmalige aanmelding van Microsoft Azure Active Directory met HPE SaaS configureren en testen op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Microsoft Azure Active Directory-gebruiker en de daaraan gerelateerde gebruiker in HPE SaaS tot stand is gebracht.

Om eenmalige aanmelding van Microsoft Azure Active Directory met HPE SaaS te configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding met HPE SaaS configureren](#configure-hpe-saas-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de clientzijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor HPE SaaS maken](#create-hpe-saas-test-user)** : als u een tegenhanger van Britta Simon in HPE SaaS wilt hebben die is gekoppeld aan de Microsoft Azure Active Directory-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit als u eenmalige aanmelding van Microsoft Azure Active Directory met HPE SaaS wilt configureren:

1. Ga in de [Microsoft Azure-portal](https://portal.azure.com/) naar de pagina met de integratie van de toepassing **HPE SaaS** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding van domeinen en URL’s van HPE SaaS](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://login.saas.hpe.com/msg`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://<subdomain>.saas.hpe.com`

    > [!NOTE]
    > De id-waarde is niet echt. Werk deze waarde bij met de werkelijke id. Neem contact op met het [HPE SaaS-klantondersteuningsteam](https://www.sas.com/en_us/contact.html) om deze waarde op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. In het gedeelte **HPE SaaS instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-hpe-saas-single-sign-on"></a>Eenmalige aanmelding met HPE SaaS configureren

Als u eenmalige aanmelding aan de zijde van **HPE SaaS** wilt configureren, moet u het gedownloade **XML-bestand met federatieve metagegevens** en de correcte uit de Microsoft Azure-portal gekopieerde URL's verzenden naar het [ondersteuningsteam van HPE SaaS](https://www.sas.com/en_us/contact.html). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

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

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot HPE SaaS.

1. Selecteer in de Microsoft Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **HPE SaaS**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **HPE SaaS** in de lijst met toepassingen.

    ![De HPE SaaS-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-hpe-saas-test-user"></a>HPE SaaS-testgebruiker maken

In deze sectie gaat u in HPE SaaS een gebruiker maken met de naam Britta Simon. Neem contact op met het [ondersteuningsteam van HPE SaaS](https://www.sas.com/en_us/contact.html) om de gebruikers toe te voegen in het HPE SaaS-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel van HPE SaaS in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij de instantie van HPE SaaS waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)