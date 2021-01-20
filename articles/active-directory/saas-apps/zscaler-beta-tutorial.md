---
title: 'Zelfstudie: Integratie van Azure Active Directory met Zscaler Beta | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Zscaler Beta.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: 6914fb50cdb157cf8ef7b5433ebbde47eff8fc32
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539804"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>Zelfstudie: Integratie van Azure Active Directory met Zscaler Beta

In deze zelfstudie leert u hoe u Zscaler Beta kunt integreren met Azure Active Directory (Azure AD).
Wanneer u Zscaler Beta integreert met Azure AD, kunt u het volgende:

* U kunt in Azure AD beheren wie toegang heeft tot Zscaler Beta.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Zscaler Beta. Dit toegangsbeheerelement wordt eenmalige aanmelding (SSO) genoemd.
* Uw accounts op een centrale locatie beheren, namelijk met behulp van Azure Portal.

## <a name="prerequisites"></a>Vereisten

Als u integratie tussen Azure AD met Zscaler Beta wilt configureren, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen.
* Een Zscaler Beta-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Zscaler beta ondersteunt door **SP** geïnitieerde SSO.
* Zscaler beta ondersteunt **just-in-time** -gebruikers inrichting.

## <a name="adding-zscaler-beta-from-the-gallery"></a>Zscaler Beta toevoegen vanuit de galerie

Als u de integratie van Zscaler Beta met Azure AD wilt configureren, moet u Zscaler Beta vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in het gedeelte **toevoegen vanuit de galerie** de tekst **Zscaler Beta** in het zoekvak.
1. Selecteer **Zscaler Beta** in het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-beta"></a>Azure AD SSO voor Zscaler Beta configureren en testen

Azure AD SSO met Zscaler Beta configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Zscaler Beta.

Als u Azure AD SSO wilt configureren en testen met Zscaler beta, voert u de volgende stappen uit:


1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Zscaler Beta SSO configureren](#configure-zscaler-beta-sso)** : als u de instellingen voor één Sign-On wilt configureren aan de kant van de toepassing.
    1. **[Maak een Zscaler beta test User](#create-zscaler-beta-test-user)** -om een soort tegen te brengen van B. Simon in Zscaler bèta dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de Azure Portal op de pagina **Zscaler bèta** -toepassings integratie de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    Voer in het vak **AANMELDINGS URL** de URL in die wordt gebruikt door uw gebruikers om zich aan te melden bij uw Zscaler Beta Beta-toepassing.

    > [!NOTE]
    > Deze waarde is niet echt. Vervang de waarde door de werkelijke aanmeldings-URL. Neem contact op met het [klantondersteuningsteam van Zscaler Beta](https://www.zscaler.com/company/contact) om de waarde op te vragen.

5. De Zscaler Beta-toepassing verwacht dat de SAML-beweringen in een bepaalde indeling staan. U moet aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Selecteer **Bewerken** om het dialoogvenster **Gebruikerskenmerken** te openen.

    ![Het dialoogvenster Gebruikerskenmerken](common/edit-attribute.png)

6. Bovendien verwacht de Zscaler Beta-toepassing nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. In de sectie **Gebruikersclaims** in het dialoogvenster **Gebruikerskenmerken** voert u deze stappen uit om het kenmerk van het SAML-token toe te voegen zoals wordt weergegeven in de volgende tabel.
    
    | Naam | Bronkenmerk | 
    | ---------------| --------------- |
    | memberOf  | user.assignedroles |

    a. Selecteer **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    b. Typ in het tekstvak **Naam** de naam van het kenmerk die voor die rij wordt weergegeven.

    c. Laat het vak **Naamruimte** leeg.

    d. Als **Bron** selecteert u **Kenmerk**.

    e. Typ in de lijst **Bronkenmerk** de kenmerkwaarde voor die rij.

    f. Selecteer **OK**.

    g. Selecteer **Opslaan**.

    > [!NOTE]
    > Klik [hier](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps#app-roles-ui) als u wilt weten hoe u rollen in Azure AD moet configureren.

7. Ga naar de pagina **Eenmalige aanmelding met SAML instellen** en selecteer in de sectie **SAML-handtekeningcertificaat** de optie **Downloaden** om het **certificaat (Base64)** te downloaden. Sla het op uw computer op.

    ![De koppeling om het certificaat te downloaden](common/certificatebase64.png)

8. In de sectie **Zscaler Beta instellen** kopieert u de juiste URL('s) op basis van uw behoeften:

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Zscaler Beta.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Zscaler Beta** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u de rollen hebt ingesteld zoals hierboven beschreven, kunt u deze selecteren in de vervolgkeuzelijst **Selecteer een rol**.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-zscaler-beta-sso"></a>Zscaler Beta SSO configureren

1. Als u de configuratie in Zscaler Beta wilt automatiseren, moet u **My Apps-browserextensie voor veilig aanmelden** installeren door **De extensie installeren** te selecteren.

    ![Mijn apps-extensie](common/install-myappssecure-extension.png)

2. Als u na het toevoegen van de extensie aan de browser de optie **Zscaler Beta instellen** selecteert, wordt u doorgestuurd naar de Zscaler Beta-toepassing. Geef hier de Administrator-referenties op om u aan te melden bij Zscaler Beta. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 t/m 6 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

3. Als u Zscaler Beta handmatig wilt instellen, opent u een nieuw webbrowservenster. Meld u als beheerder aan bij uw Zscaler Beta-bedrijfssite en voer de volgende stappen uit.

4. Ga naar **Beheer** > **Verificatie** > **Verificatie-instellingen** en volg deze stappen.
   
    ![Beheer](./media/zscaler-beta-tutorial/ic800206.png "Beheer")

    a. Kies onder **Verificatietype** de optie **SAML**.

    b. Selecteer **SAML configureren**.

5. Volg deze stappen in het venster **SAML bewerken**: 
            
    ![Gebruikers en verificatie beheren](./media/zscaler-beta-tutorial/ic800208.png "Gebruikers en verificatie beheren")
    
    a. Plak in het vak **IDP-aanmeldings-URL** de **aanmeldings-URL** die u uit Azure Portal hebt gekopieerd.

    b. Voer in het tekstvak **Kenmerk aanmeldingsnaam** **NameID** in.

    c. Selecteer **Uploaden** in het tekstvak **Openbaar SSL-certificaat** om het Azure SAML-ondertekeningscertificaat te uploaden dat u in Azure Portal hebt gedownload.

    d. Schakel **Automatische inrichting van SAML inschakelen** in.

    e. Voer in het vak **Kenmerk van weergavenaam van gebruiker** **displayName** in als u automatische inrichting van SAML wilt inschakelen voor displayName-kenmerken.

    f. Voer in het vak **Kenmerk van groepsnaam** **memberOf** in als u automatische inrichting van SAML wilt inschakelen voor memberOf-kenmerken.

    g. Voer in het vak **Kenmerk van afdelingsnaam** **department** in als u automatische inrichting van SAML wilt inschakelen voor department-kenmerken.

    h. Selecteer **Opslaan**.

6. Voer in het dialoogvenster **Gebruikersverificatie configureren** de volgende stappen uit:

    ![Het menu Activering en de knop Activeren](./media/zscaler-beta-tutorial/ic800207.png)

    a. Beweeg de muisaanwijzer boven het menu **Activering** linksonder.

    b. Selecteer **Activate**.

## <a name="configure-proxy-settings"></a>Proxyinstellingen configureren
Volg deze stappen om de proxyinstellingen te configureren in Internet Explorer.

1. Start **Internet Explorer**.

2. Selecteer **Internetopties** in het menu **Extra** om het dialoogvenster **Internetopties** te openen. 
    
     ![Het dialoogvenster Internetopties](./media/zscaler-beta-tutorial/ic769492.png "Internetopties")

3. Selecteer het tabblad **Verbindingen**. 
  
     ![Tabblad Verbindingen](./media/zscaler-beta-tutorial/ic769493.png "Verbindingen")

4. Selecteer **LAN-instellingen** om het dialoogvenster **LAN-instellingen (Local Area Network)** te openen.

5. Volg deze stappen in de sectie **Proxyserver**: 
   
    ![De sectie Proxyserver](./media/zscaler-beta-tutorial/ic769494.png "Proxyserver")

    a. Schakel het selectievakje **Een proxyserver voor uw LAN-netwerk gebruiken** in.

    b. Typ **gateway.Zscaler Beta.net** in het tekstvak **Adres**.

    c. Voer in het vak **Poort** de waarde **80** in.

    d. Schakel het selectievakje **Proxyserver niet voor lokale adressen gebruiken** in.

    e. Selecteer **OK** om het dialoogvenster **LAN-instellingen** te sluiten.

6. Selecteer **OK** om het dialoogvenster **Internetopties** te sluiten.

### <a name="create-zscaler-beta-test-user"></a>Testgebruiker voor Zscaler Beta maken

In deze sectie wordt de gebruiker Britta Simon gemaakt in Zscaler Beta. Zscaler Beta biedt ondersteuning voor **Just-In-Time-inrichting van gebruikers**. Deze functie is standaard ingeschakeld. In deze sectie hoeft u niets te doen. Als er nog geen gebruiker in Zscaler Beta bestaat, wordt er een nieuwe gemaakt nadat deze is geverifieerd.

>[!Note]
>Als u handmatig een gebruiker wilt maken, neemt u contact op met het [ondersteuningsteam van Zscaler Beta](https://www.zscaler.com/company/contact).

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de URL voor Zscaler Beta-aanmelding, waar u de aanmeldings stroom kunt initiëren. 

* Ga rechtstreeks naar de URL voor Zscaler Beta en begin met de aanmeldings stroom.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel Zscaler beta in de mijn apps klikt, wordt dit omgeleid naar de URL voor Zscaler bèta-aanmelding. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u Zscaler Beta hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).