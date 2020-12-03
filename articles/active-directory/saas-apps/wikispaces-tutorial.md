---
title: 'Zelfstudie: Azure Active Directory-integratie met Wikispaces | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Wikispaces.
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
ms.openlocfilehash: 877f589c19663503b9bb0b1627f1dffe0eba1a23
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/27/2020
ms.locfileid: "96302750"
---
# <a name="tutorial-azure-active-directory-integration-with-wikispaces"></a>Zelfstudie: Azure Active Directory-integratie met Wikispaces

In deze zelfstudie leert u hoe u Wikispaces integreert met Azure AD (Active Directory).
De integratie van Wikispaces met Azure AD heeft de volgende voordelen:

* U kunt in Azure AD bepalen wie toegang heeft tot Wikispaces.
* U kunt ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Wikispaces (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met Wikispaces hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Een Wikispaces-abonnement waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Wikispaces biedt ondersteuning voor met **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-wikispaces-from-the-gallery"></a>Wikispaces toevoegen vanuit de galerie

Om de integratie van Wikispaces te configureren in Azure AD moet u Wikispaces vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

**Als u Wikispaces wilt toevoegen vanuit de galerie, moet u de volgende stappen uitvoeren:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Wikispaces** in het zoekvak, selecteer **Wikispaces** in het resultatenvenster, en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Wikispaces in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u eenmalige aanmelding van Azure AD met Wikispaces op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Wikispaces tot stand is gebracht.

Als u eenmalige aanmelding van Azure AD wilt configureren en testen met Wikispaces, moet u de volgende procedures uitvoeren:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding bij Wikispaces configureren](#configure-wikispaces-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Een Wikispaces-testgebruiker maken](#create-wikispaces-test-user)** : als u een tegenhanger van Britta Simon in Wikispaces wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD te configureren met Wikispaces:

1. Selecteer in [Azure Portal](https://portal.azure.com/) op de pagina voor de integratie van de toepassing **Wikispaces** de optie **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Gegevens voor domein en URL's voor eenmalige aanmelding van Wikispaces](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<companyname>.wikispaces.net`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://session.wikispaces.net/<instancename>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [klantondersteuningsteam van Wikispaces](https://wikispaces.psu.edu/) om deze waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer in het gedeelte **Wikispaces instellen** de juiste URL('s) op basis van wat u nodig hebt.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-wikispaces-single-sign-on"></a>Eenmalige aanmelding bij Wikispaces configureren

Als u eenmalige aanmelding aan de zijde van **Wikispaces** wilt configureren, moet u het gedownloade **XML-bestand met federatieve metagegevens** en de juiste gekopieerde URL's uit Azure Portal verzenden naar het [ondersteuningsteam van Wikispaces](https://wikispaces.psu.edu/). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

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

In deze sectie geeft u Britta Simon de mogelijkheid om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Wikispaces.

1. Selecteer in Azure Portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **Wikispaces**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Wikispaces** in de lijst met toepassingen.

    ![De koppeling naar Wikispaces in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-wikispaces-test-user"></a>Een Wikispaces-testgebruiker maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Wikispaces, moeten ze worden ingericht in Wikispaces. In het geval van Wikispaces is inrichten een handmatige taak.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Ga als volgt te werk om een gebruikersaccount in te richten:

1. Meld u als beheerder aan bij de bedrijfssite van **Wikispaces**.

2. Ga naar **Leden**.
   
    ![Leden](./media/wikispaces-tutorial/ic787193.png "Leden")

3. Klik op **Personen uitnodigen**.
   
    ![Schermopname van de pagina Leden, waar u de Personen uitnodigen kunt selecteren.](./media/wikispaces-tutorial/ic787194.png "Invite People")

4. Voer in de sectie **Personen uitnodigen** de volgende stappen uit:
   
    ![Schermopname van de sectie Personen uitnodigen, waarin u gebruikerswaarden kunt invoeren.](./media/wikispaces-tutorial/ic787208.png "Invite People")
   
    a. Typ in de betreffende tekstvakken de **Gebruikersnamen of het e-mailadres** van een geldig Azure AD-account dat u wilt inrichten.
   
    b. Klik op **Verzenden**.  
      
    > [!NOTE]
    > De houder van het Azure Active Directory-account ontvangt een e-mail met een koppeling om het account te bevestigen voordat het actief wordt.
    
> [!NOTE]
> U kunt ook alle andere hulpprogramma's voor het maken van Wikispaces-gebruikersaccounts of API's van Wikispaces gebruiken om Azure AD-gebruikersaccounts in te richten.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Wikispaces klikt, wordt u automatisch aangemeld bij de instantie van Wikispaces waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)