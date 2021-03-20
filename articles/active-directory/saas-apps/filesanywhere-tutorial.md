---
title: 'Zelfstudie: Azure Active Directory-integratie met FilesAnywhere | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en FilesAnywhere.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/13/2019
ms.author: jeedes
ms.openlocfilehash: df96fae818ebe4d79c94c993d6e85a1ac48550be
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92453481"
---
# <a name="tutorial-azure-active-directory-integration-with-filesanywhere"></a>Zelfstudie: Azure Active Directory-integratie met FilesAnywhere

In deze zelfstudie leert u hoe u FilesAnywhere kunt integreren met Azure Active Directory (Azure AD).
De integratie van FilesAnywhere met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD beheren wie toegang tot FilesAnywhere heeft.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij FilesAnywhere (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met FilesAnywhere hebt u de volgende zaken nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op FilesAnywhere waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* FilesAnywhere ondersteunt door **SP** en **IDP** geïnitieerde eenmalige aanmelding

* FilesAnywhere biedt ondersteuning voor **Just-In-Time**-inrichting van gebruikers

## <a name="adding-filesanywhere-from-the-gallery"></a>FilesAnywhere toevoegen vanuit de galerie

Voor het configureren van de integratie van FilesAnywhere met Azure AD moet u FilesAnywhere uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u FilesAnywhere vanuit de galerie wilt toevoegen, moet u de volgende stappen uitvoeren:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **FilesAnywhere** in het zoekvak, selecteer **FilesAnywhere** in het deelvenster met resultaten en klik op **Toevoegen** om de toepassing toe te voegen.

     ![FilesAnywhere in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u Azure AD-eenmalige aanmelding bij FilesAnywhere configureren en testen op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in FilesAnywhere tot stand is gebracht.

Voor het configureren en testen van eenmalige aanmelding via Azure AD bij FilesAnywhere moet u de volgende bouwstenen uitvoeren:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor FilesAnywhere configureren](#configure-filesanywhere-single-sign-on)**: als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[FilesAnywhere-testgebruiker maken](#create-filesanywhere-test-user)**: als u een equivalent van Britta Simon in FilesAnywhere wilt hebben dat gekoppeld is aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor het configureren van Azure AD-eenmalige aanmelding met FilesAnywhere moet u de volgende stappen uitvoeren:

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de pagina voor integratie van de toepassing **FilesAnywhere** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

    ![Schermopname met de sectie "Standaard S A M L-configuratie" met het veld "Antwoord-U R L" en de knop "Opslaan" geselecteerd.](common/both-replyurl.png)

    In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://<company name>.filesanywhere.com/saml20.aspx?c=<Client Id>`

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![Informatie over eenmalige aanmelding bij het FilesAnywhere-domein en FilesAnywhere-URL's](common/both-signonurl.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<sub domain>.filesanywhere.com/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de echte antwoord-URL en aanmeldings-URL. Neem contact op met het [ondersteuningsteam van FilesAnywhere](mailto:support@FilesAnywhere.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

6. In de FilesAnywhere-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Klik op het pictogram Bewerken om de kenmerken toe te voegen.

    ![Schermopname met de sectie 'Gebruikerskenmerken' met de knop 'Bewerken' geselecteerd.](common/edit-attribute.png)

    Voordat gebruikers zich kunnen aanmelden bij FilesAnywhere, moeten zij de waarde van het kenmerk **clientid** verkrijgen van het [team van FilesAnywhere](mailto:support@FilesAnywhere.com). U moet het kenmerk "Client Id" toevoegen met de unieke waarde die is geleverd door FilesAnywhere.

7. Bovendien verwacht de FilesAnywhere-toepassing nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. In de sectie **Gebruikersclaims** in het dialoogvenster **Gebruikerskenmerken** voert u de volgende stappen uit om het kenmerk van het SAML-token toe te voegen zoals wordt weergegeven in de onderstaande tabel:

    | Naam | Bronkenmerk|
    | ---------------| --------------- |    
    | clientid | *"uniquevalue"* |

    a. Klik op **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    ![Schermopname van het dialoogvenster Gebruikersclaims met Nieuwe claim toevoegen en Opslaan geselecteerd.](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. In het tekstvak **Naam** typt u de naam van het kenmerk die voor die rij wordt weergegeven.

    c. Laat **Naamruimte** leeg.

    d. Selecteer Bron bij **Kenmerk**.

    e. Typ de kenmerkwaarde voor die rij in de lijst met **bronkenmerken**.

    f. Klik op **OK**.

    g. Klik op **Opslaan**.

8. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

9. In de sectie **FilesAnywhere instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-filesanywhere-single-sign-on"></a>Eenmalige aanmelding voor FilesAnywhere configureren

Als u eenmalige aanmelding aan de zijde van **FilesAnywhere** wilt configureren, moet u het gedownloade **certificaat (Base64)** en de correcte uit de Azure-portal gekopieerde URL's verzenden naar het [ondersteuningsteam van FilesAnywhere](mailto:support@FilesAnywhere.com). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

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

In deze sectie gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot FilesAnywhere.

1. Selecteer **Bedrijfstoepassingen** in de Azure-portal, selecteer **Alle toepassingen** en selecteer vervolgens **FilesAnywhere**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **FilesAnywhere** in de lijst met toepassingen.

    ![De FilesAnywhere-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-filesanywhere-test-user"></a>FilesAnywhere-testgebruiker maken

In deze sectie wordt een gebruiker met de naam Britta Simon gemaakt in FilesAnywhere. FilesAnywhere biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in FilesAnywhere bestaat, wordt er een nieuwe gemaakt na de verificatie.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel FilesAnywhere klikt, wordt u als het goed is automatisch aangemeld bij de instantie van FilesAnywhere waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)