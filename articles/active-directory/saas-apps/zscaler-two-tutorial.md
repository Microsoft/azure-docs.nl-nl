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
ms.date: 04/24/2019
ms.author: jeedes
ms.openlocfilehash: a7acbc736fc297a55a97173893cb4873acd02059
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97609068"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-two"></a>Zelfstudie: Integratie van Azure Active Directory met Zscaler Two

In deze zelfstudie leert u hoe u Zscaler Two integreert met Azure Active Directory (Azure AD).
De integratie van Zscaler Two met Azure AD heeft de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot Zscaler Two.
* U kunt ervoor zorgen dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij Zscaler Two (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om Azure AD-integratie met Zscaler Two te configureren, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Een abonnement op Zscaler Two waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Zscaler Two ondersteunt door **SP** geïnitieerde eenmalige aanmelding

* Zscaler Two ondersteunt **Just-In-Time**-inrichting van gebruikers

## <a name="adding-zscaler-two-from-the-gallery"></a>Zscaler Two toevoegen uit de galerie

Om de integratie van Zscaler Two in Azure AD te configureren, moet u Zscaler Two uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Zscaler Two toe te voegen uit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **Zscaler Two**, selecteer **Zscaler Two** in het resultatenvenster en klik op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Zscaler Two in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u eenmalige aanmelding van Azure AD met Zscaler Two op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Zscaler Two tot stand is gebracht.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Zscaler Two te configureren en testen:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding van Zscaler Two configureren](#configure-zscaler-two-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor Zscaler Two maken](#create-zscaler-two-test-user)** : als u een tegenhanger van Britta Simon in Zscaler Two wilt hebben die gekoppeld is aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met Zscaler Two te configureren:

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de integratiepagina van de toepassing **Zscaler Two** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij Zscaler Two](common/sp-signonurl.png)

    Typ in het tekstvak Aanmeldings-URL de URL waarmee uw gebruikers zich aanmelden bij uw Zscaler Two-toepassing.

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
    > Klik [hier](../develop/active-directory-enterprise-app-role-management.md) als u wilt weten hoe u rollen in Azure AD moet configureren

7. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

8. In de sectie **Zscaler Two instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-zscaler-two-single-sign-on"></a>Eenmalige aanmelding voor Zscaler Two configureren

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

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon** in.
  
    b. In het veld **Gebruikersnaam** typt u `brittasimon@yourcompanydomain.extension`. Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u Britta Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Zscaler Two.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **Zscaler Two**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Zscaler Two** in de lijst met toepassingen:

    ![De koppeling naar Zscaler Two in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen****Britta Simon** in de lijst en klik op de knop **Selecteren** onder aan het scherm.

    ![Schermopname met het dialoogvenster Gebruikers en groepen, waarin u een gebruiker kunt selecteren.](./media/zscaler-two-tutorial/tutorial_zscalertwo_users.png)

6. In het dialoogvenster **Rol selecteren** kiest u de desbetreffende gebruikersrol in de lijst en klikt u vervolgens op de knop **Selecteren** onder aan het scherm.

    ![Schermopname met het dialoogvenster Rol selecteren, waarin u een gebruikersrol kunt selecteren.](./media/zscaler-two-tutorial/tutorial_zscalertwo_roles.png)

7. Selecteer in het dialoogvenster **Toewijzing toevoegen** de knop **Toewijzen**.

    ![Schermopname met het dialoogvenster Toewijzing toevoegen, waarin u Toewijzen kunt selecteren.](./media/zscaler-two-tutorial/tutorial_zscalertwo_assign.png)

### <a name="create-zscaler-two-test-user"></a>Testgebruiker voor Zscaler Two maken

In deze sectie wordt er een gebruiker met de naam Britta Simon gemaakt in Zscaler Two. Zscaler Two ondersteunt Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in Zscaler Two bestaat, wordt er een nieuwe gemaakt nadat deze is geverifieerd.

>[!Note]
>Als u handmatig een gebruiker wilt maken, neemt u contact op met het [ondersteuningsteam van Zscaler Two](https://www.zscaler.com/company/contact).

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Zscaler Two klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van Zscaler Two waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)