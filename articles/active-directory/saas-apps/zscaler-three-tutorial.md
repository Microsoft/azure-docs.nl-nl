---
title: 'Zelfstudie: Integratie van eenmalige aanmelding bij Azure Active Directory met Zscaler Three | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Zscaler Three.
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
ms.openlocfilehash: 75d95ff77b48e7b1102900bc103e6930282e21e3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98726216"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zscaler-three"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Zscaler Three

In deze zelfstudie leert u hoe u Zscaler Three integreert met Azure Active Directory (Azure AD). Wanneer u Zscaler Three integreert met Azure AD, kunt u het volgende doen:

* U kunt in Azure AD beheren wie toegang heeft tot Zscaler Three.
* Ervoor zorgen dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij Zscaler Three.
* Uw accounts op een centrale locatie beheren: Azure Portal.


## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een Zscaler Three-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Zscaler Three ondersteunt door **SP** geïnitieerde eenmalige aanmelding

* Zscaler Three ondersteunt **Just-In-Time**-inrichting van gebruikers

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.

## <a name="adding-zscaler-three-from-the-gallery"></a>Zscaler Three toevoegen vanuit de galerie

Voor het configureren van de integratie van Zscaler Three in Azure AD moet u Zscaler Three vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **Zscaler Three**.
1. Selecteer **Zscaler Three** in het resultatenvenster en voeg de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-three"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Zscaler Three

Configureer en test eenmalige aanmelding van Azure AD met Zscaler Three met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure Active Directory-gebruiker en de bijbehorende gebruiker in Zscaler Three.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD te configureren en te testen voor Zscaler Three:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding van Zscaler Three configureren](#configure-zscaler-three-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Zscaler Three-testgebruiker maken](#create-zscaler-three-test-user)** : als u een tegenhanger van Britta Simon in Zscaler Three wilt hebben die gekoppeld is aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in Azure Portal, op de integratiepagina van de toepassing **Zscaler Three**, de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    In het tekstvak **Aanmeldings-URL** typt u een URL: `https://login.zscalerthree.net/sfc_sso`

1. In Zscaler Three worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![Schermopname die Gebruikerskenmerken toont met het pictogram Bewerken geselecteerd.](common/edit-attribute.png)

6. Bovendien worden in de Zscaler Three-toepassing nog enkele kenmerken verwacht die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.

    | Naam | Bronkenmerk |
    | ---------| ------------ |
    | memberOf | user.assignedroles |

    > [!NOTE]
    > Klik [hier](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) als u wilt weten hoe u rollen in Azure AD moet configureren.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **Zscaler Three instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding met Azure te gebruiken door toegang te verlenen tot Zscaler Three.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Zscaler Three** in de lijst met toepassingen.
1. Selecteer in het dialoogvenster **Gebruikers en groepen****Britta Simon** in de lijst en klik op de knop **Selecteren** onder aan het scherm.

    ![Schermopname met het dialoogvenster Gebruikers en groepen, waarin u een gebruiker kunt selecteren.](./media/zscaler-three-tutorial/tutorial_zscalerthree_users.png)

1. In het dialoogvenster **Rol selecteren** kiest u de desbetreffende gebruikersrol in de lijst en klikt u vervolgens op de knop **Selecteren** onder aan het scherm.

    ![Schermopname met het dialoogvenster Rol selecteren, waarin u een gebruikersrol kunt selecteren.](./media/zscaler-three-tutorial/tutorial_zscalerthree_roles.png)

1. Selecteer in het dialoogvenster **Toewijzing toevoegen** de knop **Toewijzen**.

    ![Schermopname met het dialoogvenster Toewijzing toevoegen, waarin u Toewijzen kunt selecteren.](./media/zscaler-three-tutorial/tutorial_zscalerthree_assign.png)

## <a name="configure-zscaler-three-sso"></a>Eenmalige aanmelding voor Zscaler Three configureren

1. Als u de configuratie in Zscaler Three wilt automatiseren, moet u de **My Apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

2. Als u op **Zscaler Three instellen** klikt nadat u de extensie aan de browser hebt toegevoegd, wordt u doorgestuurd naar de Zscaler Three-toepassing. Geef hier de beheerdersreferenties op om u aan te melden bij Zscaler Three. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 t/m 6 geautomatiseerd.

    ![Instellen](common/setup-sso.png)

3. Als u Zscaler Three handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij de bedrijfssite van Zscaler Three. Voer hierna de volgende stappen uit:

4. Ga naar **Beheer > Verificatie > Verificatie-instellingen** en voer de volgende stappen uit:

    ![Schermopname van de Zscaler One-site met stappen als beschreven.](./media/zscaler-three-tutorial/ic800206.png "Beheer")

    a. Kies onder Verificatietype de optie **SAML**.

    b. Klik op **SAML configureren**.

5. Voer in het venster **SAML bewerken** de volgende stappen uit en klik op Opslaan.  

    ![Gebruikers en verificatie beheren](./media/zscaler-three-tutorial/ic800208.png "Gebruikers en verificatie beheren")

    a. Plak in het tekstvak **SAML Portal URL** de **aanmeldings-URL** die u in de Azure-portal hebt gekopieerd.

    b. Voer in het tekstvak **Login Name Attribute****NameID** in.

    c. Klik op **Uploaden** om het Azure SAML-ondertekeningscertificaat te uploaden dat u in de Azure-portal in het **openbare SSL-certificaat** hebt gedownload.

    d. Schakel **Enable SAML Auto-Provisioning** in.

    e. Voer in het tekstvak **User Display Name Attribute****displayName** in als u automatische inrichting van SAML wilt inschakelen voor displayName-kenmerken.

    f. Voer in het tekstvak **Group Name Attribute****memberOf** in als u automatische inrichting van SAML wilt inschakelen voor memberOf-kenmerken.

    g. Voer in het tekstvak **Department Name Attribute****department** in als u automatische inrichting van SAML wilt inschakelen voor department-kenmerken.

    h. Klik op **Opslaan**.

6. Voer in het dialoogvenster **Configure User Authentication** de volgende stappen uit:

    ![Schermopname van het dialoogvenster Configure User Authentication, waarin Activate is geselecteerd.](./media/zscaler-three-tutorial/ic800207.png)

    a. Beweeg de muisaanwijzer boven het menu **Activering** linksonder.

    b. Klik op **Activeren**.

## <a name="configuring-proxy-settings"></a>Proxyinstellingen configureren
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>De proxyinstellingen configureren in Internet Explorer

1. Start **Internet Explorer**.

2. Selecteer **Internetopties** in het menu **Extra** om het dialoogvenster **Internetopties** te openen.   

     ![Internetopties](./media/zscaler-three-tutorial/ic769492.png "Internetopties")

3. Klik op het tabblad **Verbindingen**.   

     ![Verbindingen](./media/zscaler-three-tutorial/ic769493.png "Verbindingen")

4. Klik op **LAN-instellingen** om het dialoogvenster **LAN-instellingen** te openen.

5. In het gedeelte Proxyserver voert u de volgende stappen uit:   

    ![Proxyserver](./media/zscaler-three-tutorial/ic769494.png "Proxyserver")

    a. Selecteer **Een proxyserver voor uw LAN-netwerk gebruiken**.

    b. Typ **gateway.Zscaler Three.net** in het tekstvak Adres.

    c. Typ **80** in het tekstvak Poort.

    d. Selecteer **Proxyserver niet voor lokale adressen gebruiken**.

    e. Klik op **OK** om het dialoogvenster **LAN-instellingen** te sluiten.

6. Klik op **OK** om het dialoogvenster **Internetopties** te sluiten.

### <a name="create-zscaler-three-test-user"></a>Zscaler Three-testgebruiker maken

In deze sectie wordt er een gebruiker met de naam B.Simon gemaakt in Zscaler Three. Zscaler Three biedt ondersteuning voor Just-In-Time-inrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in Zscaler Three bestaat, wordt er een nieuwe gebruiker gemaakt wanneer u Zscaler Three opent.

>[!Note]
>Als u handmatig een gebruiker wilt maken, neemt u contact op met het [ondersteuningsteam van Zscaler Three](https://www.zscaler.com/company/contact).

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van Zscaler Three, waar u de aanmeldingsstroom kunt initiëren. 

* Ga rechtstreeks naar de aanmeldings-URL van Zscaler Three en initieer hier de aanmeldingsstroom.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u in Mijn apps op de tegel Zscaler Three klikt, wordt u omgeleid naar de aanmeldings-URL voor Zscaler Three. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u Zscaler Three hebt geconfigureerd, kunt u sessiebeheer afdwingen. Hierdoor worden exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
