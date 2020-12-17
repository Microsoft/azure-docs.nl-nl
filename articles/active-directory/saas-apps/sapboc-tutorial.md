---
title: 'Zelfstudie: Azure Active Directory-integratie met SAP Analytics Cloud | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SAP Analytics Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/17/2019
ms.author: jeedes
ms.openlocfilehash: 84b0a761b52baca4ae1ab81b3c67f18f6b14db92
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97608830"
---
# <a name="tutorial-integrate-sap-analytics-cloud-with-azure-active-directory"></a>Zelfstudie: SAP Analytics Cloud integreren met Azure Active Directory

In deze zelfstudie leert u hoe u SAP Analytics Cloud kunt integreren met Azure Active Directory (Azure AD). Wanneer u SAP Analytics Cloud integreert met Azure AD, kunt u:

* In Azure AD bepalen wie toegang heeft tot SAP Analytics Cloud.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij SAP Analytics Cloud.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* SAP Analytics Cloud-abonnement met eenmalige aanmelding (SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SAP Analytics Cloud ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-sap-analytics-cloud-from-the-gallery"></a>SAP Analytics Cloud toevoegen vanuit de galerie

Als u de integratie van SAP Analytics Cloud in Azure AD wilt configureren, moet u SAP Analytics Cloud vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen vanuit de galerie** **SAP Analytics Cloud** in het zoekvak.
1. Selecteer **SAP Analytics Cloud** in het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met SAP Analytics Cloud met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in SAP Analytics Cloud.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met SAP Analytics Cloud te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding van SAP Analytics Cloud configureren](#configure-sap-analytics-cloud-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
4. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
5. **[Een testgebruiker voor SAP Analytics Cloud maken](#create-sap-analytics-cloud-test-user)** : als u een tegenhanger van B.Simon in SAP Analytics Cloud wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **SAP Analytics Cloud** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon:

    - `https://<sub-domain>.sapanalytics.cloud/`
    - `https://<sub-domain>.sapbusinessobjects.cloud/`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon:

    - `<sub-domain>.sapbusinessobjects.cloud`
    - `<sub-domain>.sapanalytics.cloud`

    > [!NOTE] 
    > De waarden in deze URL's zijn alleen ter demonstratie. Werk de waarden bij met de werkelijke aanmeldings-URL en identificatie-URL. Neem contact op met het [klantondersteuningsteam van SAP Analytics Cloud](https://help.sap.com/viewer/product/SAP_BusinessObjects_Cloud/release/) om de aanmeldings-URL op te vragen. U kunt de identificatie-URL vaststellen door het bestand met metagegevens van SAP Analytics Cloud te downloaden via de beheerconsole. Dit wordt verderop in de zelfstudie uitgelegd.

4. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. In de sectie **SAP Analytics Cloud instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-sap-analytics-cloud-sso"></a>Eenmalige aanmelding van SAP Analytics Cloud configureren

1. Meld u in een ander venster van de browser als beheerder aan bij de bedrijfssite van SAP Analytics Cloud.

2. Selecteer **Menu** > **System** > **Administration**.
    
    ![Selecteer achtereenvolgens Menu, System en Administration](./media/sapboc-tutorial/config1.png)

3. Selecteer het pictogram **Edit** (pen) op het tabblad **Security**.
    
    ![Selecteer het pictogram Edit op het tabblad Security](./media/sapboc-tutorial/config2.png)  

4. Selecteer **SAML Single Sign-On (SSO)** bij **Authentication Method**.

    ![Selecteer SAML Single Sign-On als de verificatiemethode](./media/sapboc-tutorial/config3.png)  

5. Selecteer **Download** om de metagegevens van de serviceprovider te downloaden (stap 1). Zoek in het bestand met metagegevens de waarde voor **entityID** en kopieer deze. Ga in de Azure-portal naar het dialoogvenster **Standaard SAML-configuratie** en plak de waarde in het vak **Id**.

    ![Kopieer en plak de waarde van entityID](./media/sapboc-tutorial/config4.png)  

6. Selecteer **Upload** onder **Upload Identity Provider metadata** om de metagegevens van de provider (stap 2) te uploaden die u van de Azure-portal hebt gedownload.  

    ![Selecteer Upload onder Upload Identity Provider metadata](./media/sapboc-tutorial/config5.png)

7. Selecteer in de lijst **User Attribute** het gebruikerskenmerk (stap 3) dat u wilt gebruiken voor uw implementatie. Dit gebruikerskenmerk wordt toegewezen aan de id-provider. Als u een aangepast kenmerk wilt invoeren op de pagina van de gebruiker, gebruikt u de optie **Custom SAML Mapping**. Selecteer anders **Email** of **USER ID** als het gebruikerskenmerk. In ons voorbeeld hebben we **Email** geselecteerd omdat we de claim voor de gebruikers-id met het kenmerk **userprincipalname** hebben toegewezen in de sectie **Gebruikerskenmerken en claims** in de Azure-portal. Hierdoor krijgen we de beschikking over een uniek e-mailadres voor de gebruiker, dat bij elke geslaagde SAML-reactie wordt verzonden naar SAP Analytics Cloud.

    ![Gebruikerskenmerk selecteren](./media/sapboc-tutorial/config6.png)

8. Om het account te controleren bij de id-provider (stap 4), voert u in het vak **Login Credential (Email)** het e-mailadres van de gebruiker in. Selecteer vervolgens **Verify Account**. Het systeem voegt aanmeldingsreferenties toe aan het gebruikersaccount.

    ![E-mailadres invoeren en Verify Account selecteren](./media/sapboc-tutorial/config7.png)

9. Selecteer het pictogram **Save**.

    ![Het pictogram Save](./media/sapboc-tutorial/save.png)

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot SAP Analytics Cloud.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **SAP Analytics Cloud** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-sap-analytics-cloud-test-user"></a>Een testgebruiker voor SAP Analytics Cloud maken

Azure AD-gebruikers moeten worden ingericht in SAP Analytics Cloud voordat ze zich kunnen aanmelden bij SAP Analytics Cloud. In het geval van SAP Analytics Cloud is inrichten een handmatige taak.

Ga als volgt te werk om een gebruikersaccount in te richten:

1. Meld u als beheerder aan bij de bedrijfssite van SAP Analytics Cloud.

2. Selecteer **Menu** > **Security** > **Users**.

    ![Werknemer toevoegen](./media/sapboc-tutorial/user1.png)

3. Voeg op de pagina **Users** gegevens toe van de nieuwe gebruiker en selecteer **+** . 

    ![Pagina voor toevoegen van gebruikers](./media/sapboc-tutorial/user4.png)

    Voer de volgende stappen uit:

    1. Voer in het vak **USER ID** de gebruikers-id van de gebruiker in, zoals **B**.

    1. Voer in het vak **FIRST NAME** de voornaam van de gebruiker in, zoals **B**.

    1. Voer in het vak **LAST NAME** de achternaam van de gebruiker in, zoals **Simon**.

    1. Voer in het vak **DISPLAY NAME** de volledige naam van de gebruiker in, zoals **B.Simon**.

    1. Voer in het vak **E-MAIL** het e-mailadres van de gebruiker in, zoals `b.simon@contoso.com`.

    1. Selecteer op de pagina **Select Roles** de juiste rol voor de gebruiker en selecteer vervolgens **OK**.

        ![Rol selecteren](./media/sapboc-tutorial/user3.png)

    1. Selecteer het pictogram **Save**.

### <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel SAP Analytics Cloud klikt in het toegangsvenster, wordt u automatisch aangemeld bij de instantie van SAP Analytics Cloud waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)