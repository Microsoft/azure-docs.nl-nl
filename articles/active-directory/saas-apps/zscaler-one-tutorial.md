---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Zscaler One | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Zscaler One.
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
ms.openlocfilehash: 3f825e247aff5c8fc53eb8610f33bd8e0b7fce3d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98735634"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-one"></a>Zelfstudie: Integratie van Azure Active Directory met Zscaler One

In deze zelfstudie leert u hoe u Zscaler One integreert met Azure Active Directory (Azure AD).
De integratie van Zscaler One met Azure AD heeft de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot Zscaler One.
* U kunt ervoor zorgen dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij Zscaler One (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.


## <a name="prerequisites"></a>Vereisten

Als u integratie tussen Azure AD met Zscaler One wilt configureren, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Een abonnement op Zscaler One waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Zscaler One ondersteunt door **SP** geïnitieerde eenmalige aanmelding

* Zscaler One ondersteunt **Just-In-Time**-inrichting van gebruikers

## <a name="adding-zscaler-one-from-the-gallery"></a>Zscaler One toevoegen vanuit de galerie

Om de integratie van Zscaler One in Azure AD te configureren, moet u Zscaler One vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **Zscaler One** in het zoekvak.
1. Selecteer **Zscaler One** in het resultatenvenster en voeg daarna de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-one"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Zscaler One

Eenmalige aanmelding van Azure AD configureren en testen voor Zscaler One met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Zscaler One.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD te configureren en te testen voor Zscaler One:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
    1. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
2. **[Eenmalige aanmelding voor Zscaler One configureren](#configure-zscaler-one-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Testgebruiker voor Zscaler One maken](#create-zscaler-one-test-user)** : als u een tegenhanger van Britta Simon in Zscaler One wilt hebben die gekoppeld is aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in Azure Portal, op de integratiepagina van de toepassing **Zscaler One**, de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    Typ in het tekstvak **Aanmeldings-URL** de URL waarmee uw gebruikers zich aanmelden bij uw Zscaler One-toepassing.

    > [!NOTE]
    > Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [klantenondersteuningsteam van Zscaler One](https://www.zscaler.com/company/contact) om de waarde op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. In Zscaler One worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Klik op het pictogram **Bewerken** om het dialoogvenster **Gebruikerskenmerken** te openen.

    ![Schermopname die Gebruikerskenmerken toont met het pictogram Bewerken geselecteerd.](common/edit-attribute.png)

6. Bovendien worden in de Zscaler One-toepassing nog enkele kenmerken verwacht die als SAML-antwoord moeten worden doorgegeven. In de sectie **Gebruikersclaims** in het dialoogvenster **Gebruikerskenmerken** voert u de volgende stappen uit om het kenmerk van het SAML-token toe te voegen zoals wordt weergegeven in de onderstaande tabel:
    
    | Naam | Bronkenmerk |
    | ---------| ------------ |
    | memberOf | user.assignedroles |

    a. Klik op **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    b. In het tekstvak **Naam** typt u de naam van het kenmerk die voor die rij wordt weergegeven.

    c. Laat **Naamruimte** leeg.

    d. Selecteer Bron bij **Kenmerk**.

    e. Typ de kenmerkwaarde voor die rij in de lijst met **bronkenmerken**.
    
    f. Klik op **Opslaan**.

    > [!NOTE]
    > Klik [hier](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) als u wilt weten hoe u rollen in Azure AD moet configureren.

7. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

8. In de sectie **Zscaler One instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Zscaler One.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Zscaler One** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u de rollen hebt ingesteld zoals hierboven beschreven, kunt u deze selecteren in de vervolgkeuzelijst **Selecteer een rol**.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="configure-zscaler-one-sso"></a>Eenmalige aanmelding voor Zscaler One configureren

1. Als u de configuratie in Zscaler One wilt automatiseren, moet u de **My Apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

2. Als u op **Zscaler One instellen** klikt nadat u de extensie aan de browser hebt toegevoegd, wordt u doorgestuurd naar de Zscaler One-toepassing. Geef hier de beheerdersreferenties op om u aan te melden bij Zscaler One. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 t/m 6 geautomatiseerd.

    ![Eenmalige aanmelding instellen](common/setup-sso.png)

3. Als u Zscaler One handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij de bedrijfssite van Zscaler One. Voer hierna de volgende stappen uit:

4. Ga naar **Beheer > Verificatie > Verificatie-instellingen** en voer de volgende stappen uit:
   
    ![Schermopname van de Zscaler One-site met stappen als beschreven.](./media/zscaler-one-tutorial/ic800206.png "Beheer")

    a. Kies onder Verificatietype de optie **SAML**.

    b. Klik op **SAML configureren**.

5. Voer in het venster **SAML bewerken** de volgende stappen uit en klik op Opslaan.  
            
    ![Gebruikers en verificatie beheren](./media/zscaler-one-tutorial/ic800208.png "Gebruikers en verificatie beheren")
    
    a. Plak in het tekstvak **SAML Portal URL** de **aanmeldings-URL** die u in de Azure-portal hebt gekopieerd.

    b. Voer in het tekstvak **Login Name Attribute****NameID** in.

    c. Klik op **Uploaden** om het Azure SAML-ondertekeningscertificaat te uploaden dat u in de Azure-portal in het **openbare SSL-certificaat** hebt gedownload.

    d. Schakel **Enable SAML Auto-Provisioning** in.

    e. Voer in het tekstvak **User Display Name Attribute****displayName** in als u automatische inrichting van SAML wilt inschakelen voor displayName-kenmerken.

    f. Voer in het tekstvak **Group Name Attribute****memberOf** in als u automatische inrichting van SAML wilt inschakelen voor memberOf-kenmerken.

    g. Voer in het tekstvak **Department Name Attribute****department** in als u automatische inrichting van SAML wilt inschakelen voor department-kenmerken.

    h. Klik op **Opslaan**.

6. Voer in het dialoogvenster **Configure User Authentication** de volgende stappen uit:

    ![Schermopname van het dialoogvenster Configure User Authentication, waarin Activate is geselecteerd.](./media/zscaler-one-tutorial/ic800207.png)

    a. Beweeg de muisaanwijzer boven het menu **Activering** linksonder.

    b. Klik op **Activeren**.

## <a name="configuring-proxy-settings"></a>Proxyinstellingen configureren
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>De proxyinstellingen configureren in Internet Explorer

1. Start **Internet Explorer**.

2. Selecteer **Internetopties** in het menu **Extra** om het dialoogvenster **Internetopties** te openen.   

    ![Internetopties](./media/zscaler-one-tutorial/ic769492.png "Internetopties")

3. Klik op het tabblad **Verbindingen**.   

    ![Verbindingen](./media/zscaler-one-tutorial/ic769493.png "Verbindingen")

4. Klik op **LAN-instellingen** om het dialoogvenster **LAN-instellingen** te openen.

5. In het gedeelte Proxyserver voert u de volgende stappen uit:   
   
    ![Proxyserver](./media/zscaler-one-tutorial/ic769494.png "Proxyserver")

    a. Selecteer **Een proxyserver voor uw LAN-netwerk gebruiken**.

    b. Typ **gateway.Zscaler One.net** in het tekstvak Adres.

    c. Typ **80** in het tekstvak Poort.

    d. Selecteer **Proxyserver niet voor lokale adressen gebruiken**.

    e. Klik op **OK** om het dialoogvenster **LAN-instellingen** te sluiten.

7. Klik op **OK** om het dialoogvenster **Internetopties** te sluiten.

### <a name="create-zscaler-one-test-user"></a>Testgebruiker voor Zscaler One maken

In deze sectie wordt er een gebruiker met de naam Britta Simon gemaakt in Zscaler One. Zscaler One ondersteunt Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in Zscaler One bestaat, wordt er een nieuwe gemaakt nadat deze is geverifieerd.

>[!Note]
>Als u handmatig een gebruiker wilt maken, neemt u contact op met het [ondersteuningsteam van Zscaler One](https://www.zscaler.com/company/contact).

### <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van Zscaler One, waar u de aanmeldingsstroom kunt initiëren. 

* Ga rechtstreeks naar de aanmeldings-URL van Zscaler One en initieer hier de aanmeldingsstroom.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u in 'Mijn apps' op de tegel 'Zscaler One' klikt, wordt u omgeleid naar de aanmeldings-URL van Zscaler One. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u Zscaler One hebt geconfigureerd, kunt u sessiebeheer afdwingen. Hierdoor worden exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
