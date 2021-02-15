---
title: 'Zelfstudie: Azure Active Directory-integratie met Adobe Experience Manager | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Adobe Experience Manager configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2021
ms.author: jeedes
ms.openlocfilehash: 6cb490408cd66d5747156ef48ea9b4b2daa92abf
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100094648"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Zelfstudie: Azure Active Directory-integratie met Adobe Experience Manager

In deze zelf studie leert u hoe u Adobe Experience Manager integreert met Azure Active Directory (Azure AD). Wanneer u Adobe Experience Manager integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Adobe Experience Manager.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Adobe Experience Manager met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Adobe Experience Manager-abonnement voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Adobe Experience Manager ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding

* Adobe Experience Manager biedt ondersteuning voor het **just in time** inrichten van gebruikers

## <a name="adding-adobe-experience-manager-from-the-gallery"></a>Adobe Experience Manager toevoegen vanuit de galerie

Om de integratie van Adobe Experience Manager te configureren in Azure AD, moet u Adobe Experience Manager vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Adobe Experience Manager** in het zoekvak.
1. Selecteer **Adobe Experience Manager** in het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-adobe-experience-manager"></a>Azure AD SSO configureren en testen voor Adobe Experience Manager

Azure AD SSO configureren en testen met Adobe Experience Manager met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Adobe Experience Manager.

Als u Azure AD SSO wilt configureren en testen met Adobe Experience Manager, voert u de volgende stappen uit:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
2. **[CONFIGUREER SSO van Adobe Experience Manager](#configure-adobe-experience-manager-sso)** -om de instellingen voor één Sign-On te configureren aan de kant van de toepassing.
    1. **[Een testgebruiker voor Adobe Experience Manager definiëren](#create-adobe-experience-manager-test-user)**: een tegenhanger voor Britta Simon definiëren in Adobe Experience Manager die is gekoppeld aan de Azure AD-voorstelling van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in het Azure Portal naar de pagina **Adobe Experience Manager** -toepassings integratie en selecteer de sectie voor het **beheren** van **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een unieke waarde die u daarna ook definieert op uw AEM-server.

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<AEM Server Url>/saml_login`

    > [!NOTE]
    > De waarde van de antwoord-URL is niet de echte waarde. Werk de waarde voor de antwoord-URL bij met de werkelijke antwoord-URL. Neem contact op met het [klantondersteuningsteam van Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html) om deze waarde op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    Typ in het tekstvak **Aanmeldings-URL** de URL van uw Adobe Experience Manager-server.

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

7. Kopieer in het gedeelte **Adobe Experience Manager instellen** de juiste URL('s) op basis van wat u nodig hebt.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Adobe Experience Manager.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Adobe Experience Manager** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-adobe-experience-manager-sso"></a>SSO van Adobe Experience Manager configureren

1. Open in een ander browservenster de beheerportal van **Adobe Experience Manager**.

2. Select **Settings** > **Security** > **Users**.

    ![Schermopname toont de gebruikerstegel in Adobe Experience Manager.](./media/adobe-experience-manager-tutorial/user-1.png)

3. Selecteer **Administrator** of een andere relevante gebruiker.

    ![Schermopname waarin de Beheergebruiker is gemarkeerd.](./media/adobe-experience-manager-tutorial/tutorial-admin-6.png)

4. Selecteer **Account settings** > **Manage TrustStore**.

    ![Schermopname toont 'TrustStore beheren' onder 'Accountinstellingen'.](./media/adobe-experience-manager-tutorial/manage-trust.png)

5. Klik onder **Add Certificate from CER file** op **Select Certificate File**. Blader naar het certificaatbestand dat u hebt al gedownload uit de Azure-portal en selecteer het bestand.

    ![Schermopname die de knop 'Certificaatbestand selecteren' uitlicht.](./media/adobe-experience-manager-tutorial/user-2.png)

6. Het certificaat wordt toegevoegd aan de TrustStore. Noteer de alias van het certificaat.

    ![Schermopname toont dat het certificaat is toegevoegd aan de TrustStore.](./media/adobe-experience-manager-tutorial/tutorial-admin-7.png)

7. Selecteer **authentication-service** op de pagina **Users**.

    ![Schermopname waarin de verificatieservice op het scherm is gemarkeerd.](./media/adobe-experience-manager-tutorial/tutorial-admin-8.png)

8. Selecteer **Account settings** > **Create/Manage KeyStore**. Maak een KeyStore door een wachtwoord op te geven.

    ![Schermopname waarin 'KeyStore beheren' is gemarkeerd.](./media/adobe-experience-manager-tutorial/tutorial-admin-9.png)

9. Ga terug naar het beginscherm. Selecteer vervolgens **Settings** > **Operations** > **Web Console**.

    ![Schermopname waarin 'Web-console' onder 'Bewerkingen' binnen de sectie 'Instellingen' is gemarkeerd.](./media/adobe-experience-manager-tutorial/tutorial-admin-1.png)

    Hiermee opent u de configuratiepagina.

    ![De knop voor enkelvoudige aanmelding configureren](./media/adobe-experience-manager-tutorial/tutorial-admin-2.png)

10. Zoek **Adobe Granite SAML 2.0 Authentication Handler**. Selecteer vervolgens het pictogram **Add**.

    ![Schermopname waarin Adobe Granite SAML 2.0 Authentication Handler is gemarkeerd.](./media/adobe-experience-manager-tutorial/tutorial-admin-3.png)

11. Voer de volgende handelingen uit op deze pagina.

    ![De knop voor enkelvoudige aanmelding configureren](./media/adobe-experience-manager-tutorial/tutorial-admin-4.png)

    a. Typ **/** in het vak **Path**.

    b. Typ in het vak **IDP URL** de waarde voor **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    c. Typ in het vak **IDP Certificate Alias** de waarde voor **Alias** die u hebt toegevoegd in TrustStore.

    d. Typ in het vak **Service Provider Entity ID** de unieke waarde voor **Id** die u hebt geconfigureerd in de Azure-portal.

    e. Typ in het vak **Assertion Consumer Service URL** de waarde voor **Antwoord-URL** die u hebt geconfigureerd in de Azure-portal.

    f. Typ in het vak **Password of Key Store** de waarde voor **Password** die u hebt ingesteld in de KeyStore.

    g. Typ in het vak **UserID Attribute** de waarde voor **NameId** of een andere gebruikers-id die relevant is in uw situatie.

    h. Selecteer **Autocreate CRX Users**.

    i. Typ in het vak **Logout URL** de unieke waarde voor **Afmeldings-URL** die u hebt verkregen via de Azure-portal.

    j. Selecteer **Opslaan**.

### <a name="create-adobe-experience-manager-test-user"></a>Adobe Experience Manager-testgebruiker maken

In deze sectie maakt u een gebruiker met de naam Britta Simon in Adobe Experience Manager. Als u de optie **Autocreate CRX Users** hebt geselecteerd, worden gebruikers automatisch gemaakt na een geslaagde verificatie.

Als u de gebruikers handmatig wilt maken, neemt u contact op met het [ondersteuningsteam van Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html) om de gebruikers toe te voegen op het Adobe Experience Manager-platform.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de aanmeldings-URL van Adobe Experience Manager, waar u de aanmeldings stroom kunt initiëren.  

* Ga rechtstreeks naar de aanmeldings-URL van Adobe Experience Manager en start de aanmeldings stroom.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **test deze toepassing** in azure Portal en u moet automatisch worden aangemeld bij de Adobe Experience Manager waarvoor u de SSO hebt ingesteld 

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u op de tegel Adobe Experience manager in de app mijn apps klikt, wordt u omgeleid naar de aanmeldings pagina van de toepassing om de aanmeldings stroom te initiëren en als deze is geconfigureerd in de IDP-modus, moet u automatisch worden aangemeld bij de Adobe Experience Manager waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u Adobe Experience Manager hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
