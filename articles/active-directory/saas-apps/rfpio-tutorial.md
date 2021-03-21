---
title: 'Zelfstudie: Azure Active Directory-integratie met RFPIO | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en RFPIO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: c4e838afa867a7fb1e7fa8f582bc8879c24056a9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92506051"
---
# <a name="tutorial-azure-active-directory-integration-with-rfpio"></a>Zelfstudie: Azure Active Directory-integratie met RFPIO

In deze zelfstudie leert u hoe u RFPIO kunt integreren met Azure Active Directory (Azure AD).
RFPIO integreren met Azure Active Directory biedt u de volgende voordelen:

* U kunt in Azure Active Directory bepalen wie er toegang heeft tot RFPIO.
* U kunt inschakelen dat gebruikers automatisch met hun Azure Active Directory-account worden aangemeld bij RFPIO (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure Active Directory-integratie met RFPIO, heeft u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Een abonnement op RFPIO waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* RFPIO ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-rfpio-from-the-gallery"></a>RFPIO toevoegen vanuit de galerie

Om de integratie van RFPIO in Azure AD te configureren, moet u RFPIO uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u RFPIO wilt toevoegen vanuit de galerie, moet u de volgende stappen uitvoeren:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **RFPIO**, selecteer **RFPIO** in het resultaatvenster en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

    ![RFPIO in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte configureert en test u eenmalige aanmelding bij Azure Active Directory met RFPIO op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure Active Directory-gebruiker en de daaraan gerelateerde gebruiker in RFPIO tot stand is gebracht.

Om eenmalige aanmelding bij Azure Active Directory met RFPIO te configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding met RFPIO configureren](#configure-rfpio-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[RFPIO-testgebruiker maken](#create-rfpio-test-user)** : als u een tegenhanger van Britta Simon wilt in RFPIO die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor het configureren van eenmalige aanmelding bij Azure Active Directory met RFPIO, moet u de volgende stappen uitvoeren:

1. In de [Azure Portal](https://portal.azure.com/) selecteert u **Eenmalige aanmelding** op de integratiepagina van de toepassing **RFPIO**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In het gedeelte **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

    ![Schermopname van de Basic SAML-configuratie, waar u een id kunt invoeren.](common/idp-identifier.png)

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://www.rfpio.com`

    b. Klik op **Extra URL's instellen**.

    c. Voer in het tekstvak **Relay-status** een tekenreekswaarde in. Neem contact op met het [RFPIO-ondersteuningsteam](https://www.rfpio.com/contact/) om deze waarde op te halen.

    ![Schermopname van Extra U R L's instellen.](common/idp-preintegrated-relay.png)

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![image](common/both-preintegrated-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://www.app.rfpio.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en aanmeldings-URL. Neem contact op met het [RFPIO Client-ondersteuningsteam](https://www.rfpio.com/contact/) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

7. In het gedeelte **RFPIO instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-rfpio-single-sign-on"></a>Eenmalige aanmelding voor RFPIO configureren

1. Meld u in een ander browservenster als beheerder aan bij de website van **RFPIO**.

1. Klik op de vervolgkeuzelijst in de linkerbenedenhoek.

    ![Schermopname van de pijl-omlaag onderaan het deelvenster.](./media/rfpio-tutorial/app1.png)

1. Klik op de **Organisatie-instellingen**. 

    ![Schermopname met Organisatie-instellingen geselecteerd.](./media/rfpio-tutorial/app2.png)

1. Klik op de **FUNCTIES EN INTEGRATIE**.

    ![Schermopname van Functies en integratie geselecteerd vanuit Instellingen.](./media/rfpio-tutorial/app4.png)

1. Klik in de **SAML SSO-configuratie** op **Bewerken**.

    ![Schermopname van SAML S S O-configuratie met de knop Bewerken gemarkeerd.](./media/rfpio-tutorial/app3.png)

1. In deze sectie voert u de volgende acties uit:

    ![Schermopname van SAML S S O-configuratie met de knop SAML ingeschakeld.](./media/rfpio-tutorial/app5.png)
    
    a. Kopieer de inhoud van de **gedownloade XML-bestand met metagegevens** en plak deze in het veld **identiteitsconfiguratie**.

    > [!NOTE]
    >Als u de inhoud van het gedownloade **XML-bestand met federatieve metagegevens van** wilt kopiëren, gebruikt u **Notepad + +** of een **XML-editor**.

    b. Klik op **Valideren**.

    c. Nadat u op **Valideren** heeft geklikt, schakelt u **SAML (ingeschakeld)** in.

    d. Klik op **Submit**

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon** in.
  
    b. In het veld **Gebruikersnaam** typt u `brittasimon@yourcompanydomain.extension`. Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot RFPIO.

1. Selecteer **Bedrijfstoepassingen** in de Azure-portal, selecteer **Alle toepassingen** en selecteer vervolgens **RFPIO**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **RFPIO** in de lijst met toepassingen.

    ![De RFPIO-link in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-rfpio-test-user"></a>RFPIO-testgebruiker maken

1. Meld u als beheerder aan bij de RFPIO-bedrijfssite.

1. Klik op de vervolgkeuzelijst in de linkerbenedenhoek.

    ![Schermopname van de pijl-omlaag onderaan het deelvenster.](./media/rfpio-tutorial/app1.png)

1. Klik op de **Organisatie-instellingen**. 

    ![Schermopname met Organisatie-instellingen geselecteerd.](./media/rfpio-tutorial/app2.png)

1. Klik op **TEAMLEDEN**.

    ![Schermopname waarop Teamleden in Instellingen is geselecteerd.](./media/rfpio-tutorial/app6.png)

1. Klik op **LEDEN TOEVOEGEN**.

    ![Schermopname van de knop Leden toevoegen.](./media/rfpio-tutorial/app7.png)

1. In de sectie **Nieuwe leden toevoegen**. Voer de volgende acties uit:

    ![Schermopname van Nieuwe leden toevoegen waar u de beschreven waarden kunt invoeren.](./media/rfpio-tutorial/app8.png)

    a. Voer het **E-mailadres** in het veld **Eén e-mail per regel** in.

    b. Selecteer de **Rol** afhankelijk van uw vereisten.

    c. Klik op **LEDEN TOEVOEGEN**.

    > [!NOTE]
    > De houder van het Azure Active Directory-account ontvangt een e-mail en volgt een koppeling om het account te bevestigen voordat het actief wordt.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel RFPIO in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van RFPIO waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)