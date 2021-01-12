---
title: 'Zelfstudie: Integratie van Azure Active Directory met Zscaler Two | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Zscaler Two.
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
ms.openlocfilehash: 971c093363ddbb4cbc6622be40479a108c16116f
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936512"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-two"></a>Zelfstudie: Integratie van Azure Active Directory met Zscaler Two

In deze zelfstudie leert u hoe u Zscaler Two kunt integreren met Azure Active Directory (Azure AD). Wanneer u Zscaler Two integreert met Azure AD, kunt u het volgende doen:

* U kunt in Azure AD bepalen wie er toegang heeft tot Zscaler Two.
* Zorg ervoor dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Zscaler Two.
* Uw accounts op één centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

Om Azure AD-integratie met Zscaler Two te configureren, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen.
* Een abonnement op Zscaler Two waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Zscaler Two ondersteunt door **SP** geïnitieerde eenmalige aanmelding

* Zscaler Two ondersteunt **Just-In-Time**-inrichting van gebruikers

## <a name="adding-zscaler-two-from-the-gallery"></a>Zscaler Two toevoegen uit de galerie

Om de integratie van Zscaler Two in Azure AD te configureren, moet u Zscaler Two uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **Zscaler Two** in het zoekvak.
1. Selecteer **Zscaler Two** in het resultatenvenster en voeg daarna de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-two"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Zscaler Two

Eenmalige aanmelding van Azure AD configureren en testen voor Zscaler Two met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Zscaler Two.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD te configureren en te testen voor Zscaler Two:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** : zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor Zscaler Two configureren](#configure-zscaler-two-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Testgebruiker voor Zscaler Two maken](#create-zscaler-two-test-user)** : als u een tegenhanger van B.Simon in Zscaler Two wilt hebben die gekoppeld is aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in Azure Portal, op de integratiepagina van de toepassing **Zscaler Three**, de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    Typ in het tekstvak **Aanmeldings-URL** de URL waarmee uw gebruikers zich aanmelden bij uw Zscaler Two-toepassing.

    > [!NOTE]
    > Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [klantenondersteuningsteam van Zscaler Two](https://www.zscaler.com/company/contact) om de waarde op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. In Zscaler Two worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Klik op het pictogram **Bewerken** om het dialoogvenster **Gebruikerskenmerken** te openen.

    ![Schermopname die Gebruikerskenmerken toont met het pictogram Bewerken geselecteerd.](common/edit-attribute.png)

6. Bovendien worden in de Zscaler Two-toepassing nog enkele kenmerken verwacht die als SAML-antwoord moeten worden doorgegeven. In de sectie **Gebruikersclaims** in het dialoogvenster **Gebruikerskenmerken** voert u de volgende stappen uit om het kenmerk van het SAML-token toe te voegen zoals wordt weergegeven in de onderstaande tabel:
    
    | Naam | Bronkenmerk |
    | ---------| ------------ |
    | memberOf | user.assignedroles |

    a. Klik op **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    ![Schermopname die Gebruikersclaims toont met de optie om een nieuwe claim toe te voegen.](common/new-save-attribute.png)

    ![Schermopname die het dialoogvenster Gebruikersclaims beheren toont, waarin u de beschreven waarden kunt invoeren.](common/new-attribute-details.png)

    b. In het tekstvak **Naam** typt u de naam van het kenmerk die voor die rij wordt weergegeven.

    c. Laat **Naamruimte** leeg.

    d. Selecteer Bron bij **Kenmerk**.

    e. Typ de kenmerkwaarde voor die rij in de lijst met **bronkenmerken**.
    
    f. Klik op **Opslaan**.

    > [!NOTE]
    > Klik [hier](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps#app-roles-ui) als u wilt weten hoe u rollen in Azure AD moet configureren.

7. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

8. In de sectie **Zscaler Two instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u Britta Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Zscaler Two.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **Zscaler Two**.
2. Selecteer **Zscaler Two** in de lijst met toepassingen:
3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.
4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
5. Selecteer in het dialoogvenster **Gebruikers en groepen****Britta Simon** in de lijst en klik op de knop **Selecteren** onder aan het scherm.

    ![Schermopname met het dialoogvenster Gebruikers en groepen, waarin u een gebruiker kunt selecteren.](./media/zscaler-two-tutorial/tutorial_zscalertwo_users.png)

6. In het dialoogvenster **Rol selecteren** kiest u de desbetreffende gebruikersrol in de lijst en klikt u vervolgens op de knop **Selecteren** onder aan het scherm.

    ![Schermopname met het dialoogvenster Rol selecteren, waarin u een gebruikersrol kunt selecteren.](./media/zscaler-two-tutorial/tutorial_zscalertwo_roles.png)

7. Selecteer in het dialoogvenster **Toewijzing toevoegen** de knop **Toewijzen**.

    ![Schermopname met het dialoogvenster Toewijzing toevoegen, waarin u Toewijzen kunt selecteren.](./media/zscaler-two-tutorial/tutorial_zscalertwo_assign.png)

## <a name="configure-zscaler-two-sso"></a>Eenmalige aanmelding voor Zscaler Two configureren

1. Als u de configuratie in Zscaler Two wilt automatiseren, moet u de **My Apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

2. Als u op **Zscaler Two instellen** klikt nadat u de extensie aan de browser hebt toegevoegd, wordt u doorgestuurd naar de Zscaler Two-toepassing. Geef hier de beheerdersreferenties op om u aan te melden bij Zscaler Two. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 t/m 6 geautomatiseerd.

    ![Eenmalige aanmelding instellen](common/setup-sso.png)

3. Als u Zscaler Two handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij de bedrijfssite van Zscaler Two. Voer hierna de volgende stappen uit:

4. Ga naar **Beheer > Verificatie > Verificatie-instellingen** en voer de volgende stappen uit:
   
    ![Schermopname van de Zscaler One-site met stappen als beschreven.](./media/zscaler-two-tutorial/ic800206.png "Beheer")

    a. Kies onder Verificatietype de optie **SAML**.

    b. Klik op **SAML configureren**.

5. Voer in het venster **SAML bewerken** de volgende stappen uit en klik op Opslaan.  
            
    ![Gebruikers en verificatie beheren](./media/zscaler-two-tutorial/ic800208.png "Gebruikers en verificatie beheren")
    
    a. Plak in het tekstvak **SAML Portal URL** de **aanmeldings-URL** die u in de Azure-portal hebt gekopieerd.

    b. Voer in het tekstvak **Login Name Attribute****NameID** in.

    c. Klik op **Uploaden** om het Azure SAML-ondertekeningscertificaat te uploaden dat u in de Azure-portal in het **openbare SSL-certificaat** hebt gedownload.

    d. Schakel **Enable SAML Auto-Provisioning** in.

    e. Voer in het tekstvak **User Display Name Attribute****displayName** in als u automatische inrichting van SAML wilt inschakelen voor displayName-kenmerken.

    f. Voer in het tekstvak **Group Name Attribute****memberOf** in als u automatische inrichting van SAML wilt inschakelen voor memberOf-kenmerken.

    g. Voer in het tekstvak **Department Name Attribute****department** in als u automatische inrichting van SAML wilt inschakelen voor department-kenmerken.

    h. Klik op **Opslaan**.

6. Voer in het dialoogvenster **Configure User Authentication** de volgende stappen uit:

    ![Schermopname van het dialoogvenster Configure User Authentication, waarin Activate is geselecteerd.](./media/zscaler-two-tutorial/ic800207.png)

    a. Beweeg de muisaanwijzer boven het menu **Activering** linksonder.

    b. Klik op **Activeren**.

## <a name="configuring-proxy-settings"></a>Proxyinstellingen configureren
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>De proxyinstellingen configureren in Internet Explorer

1. Start **Internet Explorer**.

2. Selecteer **Internetopties** in het menu **Extra** om het dialoogvenster **Internetopties** te openen.   
    
     ![Internetopties](./media/zscaler-two-tutorial/ic769492.png "Internetopties")

3. Klik op het tabblad **Verbindingen**.   
  
     ![Verbindingen](./media/zscaler-two-tutorial/ic769493.png "Verbindingen")

4. Klik op **LAN-instellingen** om het dialoogvenster **LAN-instellingen** te openen.

5. In het gedeelte Proxyserver voert u de volgende stappen uit:   
   
    ![Proxyserver](./media/zscaler-two-tutorial/ic769494.png "Proxyserver")

    a. Selecteer **Een proxyserver voor uw LAN-netwerk gebruiken**.

    b. Typ **gateway.Zscaler Two.net** in het tekstvak Adres.

    c. Typ **80** in het tekstvak Poort.

    d. Selecteer **Proxyserver niet voor lokale adressen gebruiken**.

    e. Klik op **OK** om het dialoogvenster **LAN-instellingen** te sluiten.

6. Klik op **OK** om het dialoogvenster **Internetopties** te sluiten.


### <a name="create-zscaler-two-test-user"></a>Testgebruiker voor Zscaler Two maken

In deze sectie wordt er een gebruiker met de naam Britta Simon gemaakt in Zscaler Two. Zscaler Two ondersteunt Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in Zscaler Two bestaat, wordt er een nieuwe gemaakt nadat deze is geverifieerd.

>[!Note]
>Als u handmatig een gebruiker wilt maken, neemt u contact op met het [ondersteuningsteam van Zscaler Two](https://www.zscaler.com/company/contact).

### <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van Zscaler Two, waar u de aanmeldingsstroom kunt initiëren. 

* Ga rechtstreeks naar de aanmeldings-URL van Zscaler Two en initieer hier de aanmeldingsstroom.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u in 'Mijn apps' op de tegel 'Zscaler Two' klikt, wordt u omgeleid naar de aanmeldings-URL voor Zscaler Two. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u Zscaler Two hebt geconfigureerd, kunt u sessiebeheer afdwingen. Hierdoor worden exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).