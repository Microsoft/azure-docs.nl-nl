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
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 19bdb6d2a586dcb279687673c7fa4e302dc4928b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101652637"
---
# <a name="tutorial-integrate-sap-analytics-cloud-with-azure-active-directory"></a>Zelfstudie: SAP Analytics Cloud integreren met Azure Active Directory

In deze zelfstudie leert u hoe u SAP Analytics Cloud kunt integreren met Azure Active Directory (Azure AD). Wanneer u SAP Analytics Cloud integreert met Azure AD, kunt u:

* In Azure AD bepalen wie toegang heeft tot SAP Analytics Cloud.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij SAP Analytics Cloud.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* SAP Analytics Cloud-abonnement met eenmalige aanmelding (SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* De SAP Analytics-Cloud ondersteunt door **SP** geïnitieerde SSO.

## <a name="add-sap-analytics-cloud-from-the-gallery"></a>Een SAP Analytics-Cloud toevoegen vanuit de galerie

Als u de integratie van SAP Analytics Cloud in Azure AD wilt configureren, moet u SAP Analytics Cloud vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen vanuit de galerie** **SAP Analytics Cloud** in het zoekvak.
1. Selecteer **SAP Analytics Cloud** in het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-sap-analytics-cloud"></a>Azure AD SSO voor SAP Analytics-Cloud configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met SAP Analytics Cloud met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in SAP Analytics Cloud.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met de SAP Analytics-Cloud:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[SAP Analytics Cloud-SSO configureren](#configure-sap-analytics-cloud-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Een testgebruiker voor SAP Analytics Cloud maken](#create-sap-analytics-cloud-test-user)** : als u een tegenhanger van B.Simon in SAP Analytics Cloud wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in het Azure Portal naar de integratie pagina van de **SAP Analytics-Cloud** toepassing, zoek de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. Typ in het tekstvak **URL voor aanmelden** een URL met behulp van een van de volgende patronen:

    - `https://<sub-domain>.sapanalytics.cloud/`
    - `https://<sub-domain>.sapbusinessobjects.cloud/`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met één van de volgende patronen:

    - `<sub-domain>.sapbusinessobjects.cloud`
    - `<sub-domain>.sapanalytics.cloud`

    > [!NOTE] 
    > De waarden in deze URL's zijn alleen ter demonstratie. Werk de waarden bij met de werkelijke aanmeldings-URL en identificatie-URL. Neem contact op met het [klantondersteuningsteam van SAP Analytics Cloud](https://help.sap.com/viewer/product/SAP_BusinessObjects_Cloud/release/) om de aanmeldings-URL op te vragen. U kunt de identificatie-URL vaststellen door het bestand met metagegevens van SAP Analytics Cloud te downloaden via de beheerconsole. Dit wordt verderop in de zelfstudie uitgelegd.

4. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. In de sectie **SAP Analytics Cloud instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot SAP Analytics Cloud.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **SAP Analytics Cloud** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-sap-analytics-cloud-sso"></a>Eenmalige aanmelding van SAP Analytics Cloud configureren

1. Meld u in een ander venster van de browser als beheerder aan bij de bedrijfssite van SAP Analytics Cloud.

2. Selecteer **Menu** > **System** > **Administration**.
    
    ![Selecteer achtereenvolgens Menu, System en Administration](./media/sapboc-tutorial/configure-1.png)

3. Selecteer het pictogram **Edit** (pen) op het tabblad **Security**.
    
    ![Selecteer het pictogram Edit op het tabblad Security](./media/sapboc-tutorial/configure-2.png)  

4. Selecteer **SAML Single Sign-On (SSO)** bij **Authentication Method**.

    ![Selecteer SAML Single Sign-On als de verificatiemethode](./media/sapboc-tutorial/configure-3.png)  

5. Selecteer **Download** om de metagegevens van de serviceprovider te downloaden (stap 1). Zoek in het bestand met metagegevens de waarde voor **entityID** en kopieer deze. Ga in de Azure-portal naar het dialoogvenster **Standaard SAML-configuratie** en plak de waarde in het vak **Id**.

    ![Kopieer en plak de waarde van entityID](./media/sapboc-tutorial/configure-4.png)  

6. Selecteer **Upload** onder **Upload Identity Provider metadata** om de metagegevens van de provider (stap 2) te uploaden die u van de Azure-portal hebt gedownload.  

    ![Selecteer Upload onder Upload Identity Provider metadata](./media/sapboc-tutorial/configure-5.png)

7. Selecteer in de lijst **User Attribute** het gebruikerskenmerk (stap 3) dat u wilt gebruiken voor uw implementatie. Dit gebruikerskenmerk wordt toegewezen aan de id-provider. Als u een aangepast kenmerk wilt invoeren op de pagina van de gebruiker, gebruikt u de optie **Custom SAML Mapping**. Selecteer anders **Email** of **USER ID** als het gebruikerskenmerk. In ons voorbeeld hebben we **Email** geselecteerd omdat we de claim voor de gebruikers-id met het kenmerk **userprincipalname** hebben toegewezen in de sectie **Gebruikerskenmerken en claims** in de Azure-portal. Hierdoor krijgen we de beschikking over een uniek e-mailadres voor de gebruiker, dat bij elke geslaagde SAML-reactie wordt verzonden naar SAP Analytics Cloud.

    ![Gebruikerskenmerk selecteren](./media/sapboc-tutorial/configure-6.png)

8. Om het account te controleren bij de id-provider (stap 4), voert u in het vak **Login Credential (Email)** het e-mailadres van de gebruiker in. Selecteer vervolgens **Verify Account**. Het systeem voegt aanmeldingsreferenties toe aan het gebruikersaccount.

    ![E-mailadres invoeren en Verify Account selecteren](./media/sapboc-tutorial/configure-7.png)

9. Selecteer het pictogram **Save**.

    ![Het pictogram Save](./media/sapboc-tutorial/save.png)

### <a name="create-sap-analytics-cloud-test-user"></a>Een testgebruiker voor SAP Analytics Cloud maken

Azure AD-gebruikers moeten worden ingericht in SAP Analytics Cloud voordat ze zich kunnen aanmelden bij SAP Analytics Cloud. In het geval van SAP Analytics Cloud is inrichten een handmatige taak.

Ga als volgt te werk om een gebruikersaccount in te richten:

1. Meld u als beheerder aan bij de bedrijfssite van SAP Analytics Cloud.

2. Selecteer **Menu** > **Security** > **Users**.

    ![Werknemer toevoegen](./media/sapboc-tutorial/user-1.png)

3. Voeg op de pagina **Users** gegevens toe van de nieuwe gebruiker en selecteer **+** . 

    ![Pagina voor toevoegen van gebruikers](./media/sapboc-tutorial/user-4.png)

    Voer de volgende stappen uit:

    1. Voer in het vak **USER ID** de gebruikers-id van de gebruiker in, zoals **B**.

    1. Voer in het vak **FIRST NAME** de voornaam van de gebruiker in, zoals **B**.

    1. Voer in het vak **LAST NAME** de achternaam van de gebruiker in, zoals **Simon**.

    1. Voer in het vak **DISPLAY NAME** de volledige naam van de gebruiker in, zoals **B.Simon**.

    1. Voer in het vak **E-MAIL** het e-mailadres van de gebruiker in, zoals `b.simon@contoso.com`.

    1. Selecteer op de pagina **Select Roles** de juiste rol voor de gebruiker en selecteer vervolgens **OK**.

        ![Rol selecteren](./media/sapboc-tutorial/user-3.png)

    1. Selecteer het pictogram **Save**.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de SAP Analytics-URL voor aanmelding bij de Cloud, waar u de aanmeldings stroom kunt initiëren. 

* Ga rechtstreeks naar de aanmeldings-URL van de SAP Analytics-Cloud en start de aanmeldings stroom.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel SAP Analytics Cloud in de mijn apps klikt, wordt dit omgeleid naar de aanmeldings-URL van de SAP Analytics-Cloud. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Nadat u de SAP Analytics-Cloud hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).