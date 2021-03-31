---
title: 'Zelfstudie: Integratie van Azure Active Directory met Wdesk| Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Wdesk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/08/2021
ms.author: jeedes
ms.openlocfilehash: d85d7ef37536b54ecfc1b65d19eafd1d499ca050
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104603231"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wdesk"></a>Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met Wdesk

In deze zelfstudie leert u hoe u Wdesk integreert met Azure Active Directory (Azure AD). Wanneer u Wdesk integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie er toegang heeft tot Wdesk.
* Instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld Wdesk.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Abonnement op Wdesk waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Wdesk ondersteunt door **SP** en **IDP** geïnitieerde SSO.

## <a name="add-wdesk-from-the-gallery"></a>Wdesk toevoegen vanuit de galerie

Voor het configureren van de integratie van Wdesk in Azure AD moet u Wdesk vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **Wdesk** in het zoekvak.
1. Selecteer **Wdesk** in het resultatenvenster en voeg de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-wdesk"></a>Azure AD SSO voor Wdesk configureren en testen

In deze sectie gaat u Azure AD-eenmalige aanmelding met Wdesk configureren en testen met behulp van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Wdesk tot stand is gebracht.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met Wdesk:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Wdesk configureren](#configure-wdesk-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een Wdesk-testgebruiker maken](#create-wdesk-test-user)** : als u een equivalent van B.Simon in Wdesk wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in het Azure Portal op de pagina Toepassings integratie van **Wdesk** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In het gedeelte **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, antwoord-URL en aanmeldings-URL. U verkrijgt deze waarden via de WDesk-portal wanneer u SSO configureert.

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. In de sectie **Wdesk instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Wdesk.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Wdesk** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-wdesk-sso"></a>Eenmalige aanmelding voor Wdesk configureren

1. Meld u in een ander browservenster als een beveiligingsbeheerder aan bij Wdesk.

1. Klik linksonder op **Admin** en kies **Account Admin**:
 
    ![Schermopname waarin Account Admin is geselecteerd in het menu Admin.](./media/wdesk-tutorial/account.png)

1. Navigeer in Wdesk-beheer naar **Security** en **SAML** > **SAML Settings**:

    ![Schermopname waarin SAML-instellingen is geselecteerd op het tabblad SAML.](./media/wdesk-tutorial/settings.png)

1. Schakel onder **SAML User ID Settings** de optie **SAML User ID is Wdesk Username** in.

    ![Schermopname waarin SAML User ID Settings wordt weergegeven, waarin SAML User ID is Wdesk Username kan worden geselecteerd.](./media/wdesk-tutorial/wdesk-username.png)

4. Schakel onder **General Settings** de optie **Enable SAML Single Sign On** in:

    ![Schermopname waarin SAML-instellingen bewerken wordt weergegeven, waar u Enable SAML Single Sign On kunt selecteren.](./media/wdesk-tutorial/user-settings.png)

5. Voer onder **Service Provider Details** de volgende stappen uit:

    ![Schermopname waarin Service Provider Details wordt weergegeven, waar u de beschreven waarden kunt invoeren.](./media/wdesk-tutorial/service-provider.png)

    1. Kopieer de **aanmeldings-URL** en plak deze in het tekstvak **aanmeldings-URL** in Azure Portal.

    1. Kopieer de **URL van de metagegevens** en plak deze in het tekstvak **Id** van Azure Portal.

    1. Kopieer de **URL van de verbruiker** en plak deze in het tekstvak **Antwoord-URL** van Azure Portal.

    1. Klik in Azure Portal op **Opslaan** om de wijzigingen op te slaan.      

1. Klik op **Configure IdP Settings** om het dialoogvenster **Edit IdP Settings** te openen. Klik op **Choose File** om het bestand **Metadata.xml** te zoeken dat u vanuit Azure Portal hebt opgeslagen en upload het.
    
    ![Schermopname waarin Edit IdP Settings wordt weergegeven, waar u metagegevens kunt uploaden.](./media/wdesk-tutorial/metadata.png)
  
1. Klik op **Save changes** (Wijzigingen opslaan).

    ![Schermopname waarin de knop Wijzigingen opslaan wordt weergegeven.](./media/wdesk-tutorial/save.png)

### <a name="create-wdesk-test-user"></a>Testgebruiker maken voor Wdesk

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Wdesk, moeten ze worden ingericht in Wdesk. Het inrichten in Wdesk is een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u als een beveiligingsbeheerder aan bij Wdesk.

2. Navigeer naar **Admin** > **Account Admin**.

     ![Schermopname waarin Account Admin is geselecteerd in het menu Admin.](./media/wdesk-tutorial/account.png)

3. Klik onder **People** op **Members**.

4. Klik nu op **Add Member** om het dialoogvenster **Add Member** te openen. 
   
    ![Schermopname waarin het tabblad Member wordt weergegeven, waar u Add Member kunt selecteren.](./media/wdesk-tutorial/create-user-1.png)  

5. Voer in het tekstvak **Gebruikers** de gebruikersnaam van de gebruiker in, zoals b.simon@contoso.com en klik op de knop **Continue**.

    ![Schermopname waarin het dialoogvenster Add Member wordt weergegeven, waar u een gebruiker kunt invoeren.](./media/wdesk-tutorial/create-user-3.png)

6.  Voer de details in zoals hieronder wordt weergegeven:
  
    ![Schermopname waarin het dialoogvenster Add Member wordt weergegeven, waar u algemene informatie voor een gebruiker kunt invoeren.](./media/wdesk-tutorial/create-user-4.png)
 
    a. Voer in het tekstvak **E-mail** het e-mailadres van de gebruiker in, zoals b.simon@contoso.com.

    b. Typ in het tekstvak **First Name** de voornaam van de gebruiker, zoals **B**.

    c. Typ in het tekstvak **Achternaam** de achternaam van de gebruiker, zoals **Simon**.

7. Klik op de knop **Save Member**.  

    ![Schermopname waarin Welkomstmail verzenden met de knop Save member wordt weergegeven.](./media/wdesk-tutorial/create-user-5.png)

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

#### <a name="sp-initiated"></a>Met SP geïnitieerd:

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de URL voor Wdesk-aanmelding, waar u de aanmeldings stroom kunt initiëren.  

* Ga rechtstreeks naar de URL voor Wdesk-aanmelding en start de aanmeldings stroom vanaf daar.

#### <a name="idp-initiated"></a>Met IDP geïnitieerd:

* Klik op **test deze toepassing** in azure Portal en u moet automatisch worden aangemeld bij de Wdesk waarvoor u de SSO hebt ingesteld. 

U kunt ook Mijn apps van Microsoft gebruiken om de toepassing in een willekeurige modus te testen. Wanneer u op de tegel Wdesk in de app mijn apps klikt, wordt u omgeleid naar de aanmeldings pagina van de toepassing om de aanmeldings stroom te initiëren en als deze in de IDP-modus is geconfigureerd, moet u automatisch worden aangemeld bij de Wdesk waarvoor u de SSO hebt ingesteld. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Nadat u Wdesk hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
