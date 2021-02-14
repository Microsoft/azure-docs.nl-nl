---
title: 'Zelfstudie: Integratie van Azure Active Directory met JFrog Artifactory | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding configureert tussen Azure Active Directory en JFrog Artifactory.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.openlocfilehash: f0fafa5c0cc2e0b1bf0f4e11db3265824feb5296
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100374695"
---
# <a name="tutorial-integrate-jfrog-artifactory-with-azure-active-directory"></a>Zelfstudie: JFrog Artifactory integreren met Azure Active Directory

In deze zelfstudie leert u hoe u JFrog Artifactory integreert met Azure Active Directory (Azure AD). Wanneer u JFrog Artifactory integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie er toegang heeft tot JFrog Artifactory.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij JFrog Artifactory.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op JFrog Artifactory waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* JFrog Artifactory ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding
* JFrog Artifactory biedt ondersteuning voor **Just-In-Time**-inrichting van gebruikers

## <a name="adding-jfrog-artifactory-from-the-gallery"></a>JFrog Artifactory toevoegen vanuit de galerie

Om de integratie van JFrog Artifactory in Azure AD te configureren, moet u JFrog Artifactory vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **JFrog Artifactory** in het zoekvak.
1. Selecteer **JFrog Artifactory** in de resultaten en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met in het zoekvak met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in JFrog Artifactory.

Als u eenmalige aanmelding van Azure AD wilt configureren en testen met JFrog Artifactory, voert u de volgende procedures uit:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding bij JFrog Artifactory configureren](#configure-jfrog-artifactory-sso)** : als u de instellingen voor eenmalige aanmelding aan de clientzijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
4. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
5. **[Een testgebruiker voor JFrog Artifactory maken](#create-jfrog-artifactory-test-user)** : als u een tegenhanger van B.Simon in JFrog Artifactory wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **JFrog Artifactory** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `<servername>.jfrog.io`

    b. In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon:
    
    - Voor Artifactory 6. x: `https://<servername>.jfrog.io/artifactory/webapp/saml/loginResponse`
    - Voor Artifactory 7. x: `https://<servername>.jfrog.io/<servername>/webapp/saml/loginResponse`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    Typ in het tekstvak **Aanmeldings-URL** een URL met het volgende patroon:
    - Voor Artifactory 6. x: `https://<servername>.jfrog.io/<servername>/webapp/`
    - Voor Artifactory 7. x: `https://<servername>.jfrog.io/ui/login`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem voor deze waarden contact op met [het klantondersteuningsteam van JFrog Artifactory](https://support.jfrog.com). U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. In JFrog Artifactory worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Klik op het pictogram **bewerken** om het dialoog venster gebruikers kenmerken te openen.

    ![Schermopname met Gebruikerskenmerken en een bijschrift bij het pictogram Bewerken.](common/edit-attribute.png)

1. Naast de bovenstaande verwachting JFrog Artifactory een aantal aanvullende kenmerken die moeten worden teruggestuurd in het SAML-antwoord. Voer in het gedeelte **Gebruikerskenmerken en -claims** in het dialoogvenster **Groepsclaims (preview)** de volgende stappen uit:

    a. Klik op de **pen** naast **Groepen die zijn geretourneerd in claim**.

    ![Schermopname die Gebruikerskenmerken en claims toont met het pictogram Bewerken geselecteerd.](./media/jfrog-artifactory-tutorial/config04.png)

    ![Schermopname van de sectie Groepsclaims met Alle groepen geselecteerd.](./media/jfrog-artifactory-tutorial/config05.png)

    b. Selecteer **Alle groepen** in de lijst met keuzerondjes.

    c. Klik op **Opslaan**.

4. Zoek op de pagina **eén Sign-On met SAML instellen** , in de sectie **SAML-handtekening certificaat** , het **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](./media/jfrog-artifactory-tutorial/certificate-base.png)

6. Configureer de Artifactory (de naam van de SAML-service provider) met het veld id (zie stap 4). Kopieer in het gedeelte **JFrog Artifactory instellen** de juiste URL ('s) op basis van uw vereiste.

   - Voor Artifactory 6. x: `https://<servername>.jfrog.io/artifactory/webapp/saml/loginResponse` 
   - Voor Artifactory 7. x: `https://<servername>.jfrog.io/<servername>/webapp/saml/loginResponse`

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-jfrog-artifactory-sso"></a>Eenmalige aanmelding configureren in JFrog Artifactory

Alles wat u nodig hebt voor het configureren van eenmalige aanmelding op de **JFrog Artifactory** -kant kan worden geconfigureerd door de Artifactory-beheerder in het SAML-configugration scherm.

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

In deze sectie stelt u in dat B.Simon eenmalige aanmelding van Azure kan gebruiken door toegang te verlenen tot JFrog Artifactory.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **JFrog Artifactory** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-jfrog-artifactory-test-user"></a>Testgebruiker maken voor JFrog Artifactory

In deze sectie wordt een gebruiker met de naam B.Simon gemaakt in JFrog Artifactory. JFrog Artifactory biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in JFrog Artifactory bestaat, wordt er na verificatie een gemaakt.

### <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel JFrog Artifactory klikt, wordt u automatisch aangemeld bij de instantie van JFrog Artifactory waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)