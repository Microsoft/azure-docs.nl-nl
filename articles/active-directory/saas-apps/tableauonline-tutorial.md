---
title: 'Zelfstudie: Azure Active Directory-integratie met Tableau Online | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Tableau Online kunt configureren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 5318570ed77e3352f37c2306ecd003195992d010
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98731966"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-online"></a>Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met Tableau Online

In deze zelfstudie leert u hoe u Tableau Online kunt integreren met Azure AD (Active Directory). Wanneer u Tableau Online integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie er toegang heeft tot Tableau Online.
* Ervoor zorgen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Tableau Online.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Tableau Online waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Tableau Online biedt ondersteuning voor eenmalige aanmelding die is gestart vanuit de **SP**

## <a name="adding-tableau-online-from-the-gallery"></a>Tableau Online toevoegen vanuit de galerie

Voor het configureren van de integratie van Tableau Online met Azure AD moet u Tableau Online uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen vanuit de galerie** **Tableau Online** in het zoekvak.
1. Selecteer **Tableau Online** in de resultaten en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-tableau-online"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Tableau Online

In dit gedeelte configureert en test u eenmalige aanmelding voor Azure AD met Tableau Online op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Tableau Online tot stand is gebracht.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met Tableau Online te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding met Tableau Online configureren](#configure-tableau-online-sso)** : als u de instellingen voor eenmalige aanmelding aan de clientzijde wilt configureren.
    1. **[Tableau Online-testgebruiker maken](#create-tableau-online-test-user)** : als tegenhanger van B.Simon in Tableau Online die is gekoppeld aan de representatie van een gebruiker in Azure AD.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de Azure-portal op de integratiepagina van de toepassing **Tableau Online** naar de sectie **Beheren**, en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u de URL: `https://sso.online.tableau.com/public/sp/login?alias=<entityid>`

    b. Typ in het tekstvak **Id (Entiteits-id)** de volgende URL: `https://sso.online.tableau.com/public/sp/metadata?alias=<entityid>`

    > [!NOTE]
    > U krijgt de waarde voor `<entityid>` in de sectie **Tableau Online instellen** van deze zelfstudie. De waarde voor de entiteits-id gebruikt u als de waarde voor **Azure AD identifier** in de sectie **Tableau Online instellen**.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer in de sectie **Tableau Online instellen** de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in Azure Portal.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Tableau Online.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Tableau Online** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-tableau-online-sso"></a>Eenmalige aanmelding configureren in Tableau Online

1. Als u de configuratie in Tableau Online wilt automatiseren, moet u de **Mijn apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

2. Als u op **Tableau Online instellen** klikt nadat u de extensie hebt toegevoegd aan de browser, wordt u doorgestuurd naar de Tableau Online-toepassing. Geef hier de beheerdersreferenties op om u aan te melden bij Tableau Online. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3-7 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

3. Als u Tableau Online handmatig wilt instellen, meldt u zich in een ander webbrowservenster aan als beheerder bij uw Tableau Online-bedrijfssite.

1. Ga naar **Settings** en daarna **Authentication**.

    ![Schermopname met Authentication geselecteerd in het menu Settings.](./media/tableauonline-tutorial/tutorial_tableauonline_09.png)

2. Als u SAML wilt inschakelen, doet u het volgende onder **Authentication types**. Selecteer **Enable an additional authentication method** en schakel vervolgens het selectievakje **SAML** in.

    ![Schermopname van de sectie Authentication types, waarin u de waarden kunt selecteren.](./media/tableauonline-tutorial/tutorial_tableauonline_12.png)

3. Schuif omlaag naar de sectie **Import metadata file into Tableau Online**.  Klik op Browse om het bestand met metagegevens dat u hebt gedownload uit Azure AD te importeren. Klik vervolgens op **Apply**.

   ![Schermopname van de sectie waar u het metagegevensbestand kunt importeren.](./media/tableauonline-tutorial/tutorial_tableauonline_13.png)

4. Geef in de sectie **Match assertions** waarden van de id-provider op voor **email address**, **first name** en **last name**. Als u deze informatie wilt ophalen uit Azure AD: 
  
    a. Ga in Azure Portal naar de pagina voor de integratie van de toepassing **Tableau Online**.

    b. Klik in de sectie **Gebruikerskenmerken en claims** op het pictogram Bewerken.

   ![Schermopname met de sectie Gebruikerskenmerken en claims, waar u het bewerkingspictogram kunt selecteren.](./media/tableauonline-tutorial/attributesection.png)

    c. Kopieer de waarde van de naamruimte voor deze kenmerken: givenname, e-mail en achternaam. Gebruik hiervoor de volgende stappen:

   ![Schermopname met de kenmerken Givenname, Surname en Emailaddress.](./media/tableauonline-tutorial/tutorial_tableauonline_10.png)

    d. Klik op de waarde voor **user.givenname**

    e. Kopieer de waarde uit het tekstvak **Naamruimte**.

    ![Schermopname met de sectie Manage user claims, waarin u de naamruimte kunt invoeren.](./media/tableauonline-tutorial/attributesection2.png)

    f. Als u de waarden van de naamruimte voor het e-mailadres en de achternaam wilt kopiëren, herhaalt u de bovenstaande stappen.

    g. Ga naar Tableau Online en stel de sectie **User Attributes & Claims** als volgt in:

    * Email: **mail** of **userprincipalname**

    * First name: **givenname**

    * Last name: **surname**

    ![Schermopname van de sectie Match attributes, waarin u de waarden kunt invoeren.](./media/tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="create-tableau-online-test-user"></a>Een testgebruiker voor Tableau Online maken

In dit gedeelte maakt u in Tableau Online een gebruiker met de naam Britta Simon.

1. Klik in **Tableau Online** op **Settings** en open daarna de sectie **Authentication**. Schuif omlaag naar de sectie **Manage Users**. Klik achtereenvolgens op **Add Users** en **Enter Email Addresses**.
  
    ![Schermopname van de sectie Manage users, waar u Add users kunt selecteren.](./media/tableauonline-tutorial/tutorial_tableauonline_15.png)

2. Selecteer **Add users for (SAML) authentication**. Voeg in het tekstvak **Enter email addresses** britta.simon\@contoso.com toe.
  
    ![Schermopname van de pagina Add Users, waar u een e-mailadres kunt invoeren.](./media/tableauonline-tutorial/tutorial_tableauonline_11.png)

3. Klik op **Gebruikers toevoegen**.

### <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties.

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van Tableau Online, waar u de aanmeldingsstroom kunt initiëren.

* Ga rechtstreeks naar de aanmeldings-URL van Tableau Online en initieer hier de aanmeldingsstroom.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u in Mijn apps op de tegel Tableau Online klikt, wordt u omgeleid naar de aanmeldings-URL van Tableau Online. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u Tableau Online hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens in uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)