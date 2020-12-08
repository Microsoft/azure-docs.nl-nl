---
title: 'Zelfstudie: Azure Active Directory-integratie met LoginRadius | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en LoginRadius.
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
ms.openlocfilehash: 1376dcb76c22bcd70937f533d337ee9679e9dc59
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96455845"
---
# <a name="tutorial-azure-active-directory-integration-with-loginradius"></a>Zelfstudie: Azure Active Directory-integratie met LoginRadius

In deze zelfstudie leert u hoe u LoginRadius kunt integreren met Azure Active Directory (Azure AD).

De integratie van LoginRadius met Azure AD biedt u de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot LoginRadius.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij LoginRadius (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om de integratie van Azure AD met LoginRadius te configureren:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Een abonnement op LoginRadius waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* LoginRadius biedt ondersteuning voor door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-loginradius-from-the-gallery"></a>LoginRadius toevoegen vanuit de galerie

Voor het configureren van de integratie van LoginRadius in Azure AD moet u LoginRadius vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Om LoginRadius toe te voegen vanuit de galerie:**

1. Selecteer het pictogram van de **Azure Active Directory** in **[Azure Portal](https://portal.azure.com)** in het navigatiepaneel aan de linkerkant.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Selecteer de knop **Nieuwe toepassing** om een nieuwe toepassing toe te voegen:

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer **LoginRadius** in het zoekvak in, selecteer **LoginRadius** in het resultatenvenster en selecteer vervolgens de knop **Toevoegen** om de toepassing toe te voegen.

    ![LoginRadius in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD-eenmalige aanmelding met LoginRadius op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in LoginRadius tot stand is gebracht.

U hebt de volgende bouwstenen nodig om eenmalige aanmelding van Azure AD met LoginRadius te configureren en testen:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor LoginRadius configureren](#configure-loginradius-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor LoginRadius maken](#create-loginradius-test-user)** : als u een tegenhanger van Britta Simon in LoginRadius wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding in Azure AD te configureren voor LoginRadius:

1. Selecteer in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **LoginRadius** de optie **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het deelvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Fed** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** het pictogram voor **Bewerken** om het deelvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie**:

   ![Informatie over eenmalige aanmelding voor LoginRadius-domein en -URL's](common/sp-identifier.png)

   1. Voer in het tekstvak **Aanmeldings-URL** de URL `https://secure.loginradius.com/login` in

   1. Voer in het tekstvak **Id (entiteits-id)** de URL `https://lr.hub.loginradius.com/` in

   1. Voer in het tekstvak **Antwoord-URL (Assertion Consumer Service-URL)** de ACS-URL `https://lr.hub.loginradius.com/saml/serviceprovider/AdfsACS.aspx` van de LoginRadius in 

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** selecteert u **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. In de sectie **LoginRadius instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

   - Aanmeldings-URL

   - Azure AD-id

   - Afmeldings-URL

## <a name="configure-loginradius-single-sign-on"></a>Eenmalige aanmelding voor LoginRadius configureren

In deze sectie gaat u Azure AD-eenmalige aanmelding inschakelen in de LoginRadius-beheerconsole.

1. Meld u aan bij de account van uw LoginRadius-[beheerconsole](https://adminconsole.loginradius.com/login).

2. Ga naar de sectie **Teambeheer** in de [LoginRadius-beheerconsole](https://secure.loginradius.com/account/team).

3. Selecteer het tabblad **Eenmalige aanmelding** en selecteer vervolgens **Azure AD**:

   ![Schermopname van het menu voor eenmalige aanmelding in de teambeheerconsole van LoginRadius](./media/loginradius-tutorial/azure-ad.png)
4. Voer op de pagina Instellen Azure AD de volgende stappen uit:

   ![Schermopname van Azure Active Directory-configuratie in de LoginRadius-teambeheerconsole](./media/loginradius-tutorial/single-sign-on.png)

    1. In **Locatie van id-provider** voert u het SIGN-ON ENDPOINT in dat u van uw Azure AD-account ontvangt.

    1. In **Afmeldings-URL van id-provider** voert u het SIGN-OUT ENDPOINT in dat u van uw Azure AD-account ontvangt.
 
    1. In **Certificaat van id-provider** voert u het Azure AD-certificaat in dat u van uw Azure AD-account ontvangt. Voer de certificaatwaarde in met de kop- en voettekst. Voorbeeld: `-----BEGIN CERTIFICATE-----<certifciate value>-----END CERTIFICATE-----`

    1. Voer uw certificaat en sleutel in **Certificaat van service-provider certificaat** en **Sleutel van certificaat van serverprovider** in. 

       U kunt een zelfondertekend certificaat maken door de volgende opdrachten uit te voeren op de opdrachtregel (Linux/Mac):

       - Opdracht voor het ophalen van de certificaatsleutel voor SP: `openssl genrsa -out lr.hub.loginradius.com.key 2048`

       - Opdracht voor het ophalen van het certificaat voor SP: `openssl req -new -x509 -key lr.hub.loginradius.com.key -out lr.hub.loginradius.com.cert -days 3650 -subj /CN=lr.hub.loginradius.com`
     
       > [!NOTE]
       > Zorg ervoor dat u het certificaat en de waarden van de certificaatsleutel opgeeft met de kop- en voettekst:
       > - Voorbeeldindeling van certificaatwaarden: `-----BEGIN CERTIFICATE-----<certifciate value>-----END CERTIFICATE-----`
       > - Voorbeeldindeling van certificaatsleutelwaarden: `-----BEGIN RSA PRIVATE KEY-----<certifciate key value>-----END RSA PRIVATE KEY-----`

5. Selecteer in de sectie **Toewijzen van gegevens** de velden (SP-velden) en voer de bijbehorende Azure AD-velden (IdP-velden) in.

    Hier volgen enkele veldnamen die worden vermeld voor Azure AD.

    | Velden    | Profielsleutel                                                          |
    | --------- | -------------------------------------------------------------------- |
    | Email     | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |
    | FirstName | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`    |
    | LastName  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`      |

    > [!NOTE]
    > De toewijzing van het veld **E-mail** is vereist. De toewijzing van de velden **Voornaam** en **Achternaam** is optioneel.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure Portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In **Gebruikers** eigenschappen voert u de volgende stappen uit.

   ![Het dialoogvenster Gebruiker](common/user-properties.png)

   1. Voer in het veld **Naam** **Britta Simon** in.
  
   1. Voer `brittasimon@yourcompanydomain.extension` in in het veld **Gebruikersnaam**. Bijvoorbeeld BrittaSimon@contoso.com.

   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.

   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u Britta Simon in staat gebruik te maken van eenmalige aanmelding via Azure door haar toegang te geven tot LoginRadius.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **LoginRadius**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **LoginRadius** in de lijst met toepassingen.

    ![De koppeling naar LoginRadius in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Selecteer de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het deelvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het deelvenster **Gebruikers en groepen** **Britta Simon** in de lijst **Gebruikers** en kies de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-assertie, selecteert u in het deelvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Kies vervolgens onderaan het scherm de knop **Selecteren**.

7. Selecteer in het deelvenster **Toewijzing toevoegen** de knop **Toewijzen**.

### <a name="create-loginradius-test-user"></a>Testgebruiker voor LoginRadius maken

1. Meld u aan bij de account van uw LoginRadius-[beheerconsole](https://adminconsole.loginradius.com/login).

2. Ga naar de sectie Teambeheer in de LoginRadius-beheerconsole.

   ![Schermopname van LoginRadius-beheerconsole](./media/loginradius-tutorial/team-management.png)
3. Selecteer **Teamlid toevoegen** in het menu aan de zijkant om het formulier te openen. 

4. In het formulier **Teamlid toevoegen**, maakt u een gebruiker met de naam Britta Simon op uw LoginRadius-site door de gebruikersgegevens op te geven en de machtigingen toe te wijzen die u aan de gebruiker wilt geven. Voor meer informatie over de machtigingen op basis van rollen, zie de sectie [Toegangsmachtigingen voor rollen](https://www.loginradius.com/docs/api/v2/admin-console/team-management/manage-team-members#roleaccesspermissions0) van het gedeelte [Teamleden beheren](https://www.loginradius.com/docs/api/v2/admin-console/team-management/manage-team-members#roleaccesspermissions0) van het LoginRadius-document. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

1. Ga in een browser naar https://accounts.loginradius.com/auth.aspx en selecteer **Aanmelden met federatieve eenmalige aanmelding**.
2. Voer de naam van uw LoginRadius-app in en selecteer **Aanmelden**.
3. Er wordt een pop-upvenster geopend waarin u wordt gevraagd om u aan te melden bij uw Azure AD-account.
4. Na de verificatie wordt het pop-upvenster gesloten en wordt u aangemeld bij de LoginRadius-beheerconsole.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)
