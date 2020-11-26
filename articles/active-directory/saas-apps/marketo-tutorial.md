---
title: 'Zelfstudie: Azure Active Directory-integratie met Marketo | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Marketo.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/04/2020
ms.author: jeedes
ms.openlocfilehash: bd62855aef59439ed476a13770f79ce6d3cf68f8
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96180792"
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Zelfstudie: Azure Active Directory-integratie met Marketo

In deze zelfstudie leert u hoe u Marketo kunt integreren met Azure Active Directory (Azure AD).
De integratie van Marketo met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang heeft tot Marketo.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Marketo (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

## <a name="prerequisites"></a>Vereisten

Om Azure AD-integratie te configureren met Marketo hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op Marketo waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Marketo ondersteunt door **IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-marketo-from-the-gallery"></a>Marketo toevoegen vanuit de galerie

Om de integratie van Marketo in Azure AD te configureren, moet u Marketo vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **Marketo**.
1. Selecteer **Marketo** in het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren en testen

In deze sectie gaat u Azure AD-eenmalige aanmelding bij Marketo configureren en testen op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Marketo tot stand is gebracht.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met Marketo te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met Britta Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** : zodat Britta Simon eenmalige aanmelding van Azure AD kan gebruiken.
2. **[Eenmalige aanmelding voor Marketo configureren](#configure-marketo-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    * **[Testgebruiker maken voor Marketo](#create-marketo-test-user)**: als u een tegenhanger van Britta Simon in Marketo wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
3. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de Azure-portal op de integratiepagina van de toepassing **Marketo** naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://saml.marketo.com/sp`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://login.marketo.com/saml/assertion/\<munchkinid\>`

    c. In het tekstvak **Relaystatus** typt u een URL met de volgende notatie: `https://<munchkinid>.marketo.com/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, antwoord-URL en relaystatus. Neem contact op met het [ondersteuningsteam van Marketo](https://investors.marketo.com/contactus.cfm) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. In de sectie **Marketo instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Marketo.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Marketo** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-marketo-sso"></a>Eenmalige aanmelding voor Marketo configureren

1. Om de Munchkin-id van uw toepassing te verkrijgen, meldt u zich met beheerdersreferenties bij Marketo aan en voert u de volgende acties uit:
   
    a. Meld u bij Marketo aan met beheerdersreferenties.
   
    b. Klik in het bovenste navigatievenster op de knop **Admin**.
   
    ![Eenmalige aanmelding configureren1](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Navigeer naar het menu Integration en klik op de **Munchkin-koppeling**.
   
    ![Eenmalige aanmelding configureren2](./media/marketo-tutorial/tutorial_marketo_11.png)
   
    d. Kopieer de Munchkin-id die wordt weergegeven in het scherm en vul uw antwoord-URL in de wizard Azure AD-configuratie in.
   
    ![Eenmalige aanmelding configureren3](./media/marketo-tutorial/tutorial_marketo_12.png) 

2. Voer onderstaande stappen uit om eenmalige aanmelding te configureren in de toepassing:
   
    a. Meld u bij Marketo aan met beheerdersreferenties.
   
    b. Klik in het bovenste navigatievenster op de knop **Admin**.
   
    ![Eenmalige aanmelding configureren4](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Navigeer naar het menu Integration en klik op **Single Sign On**.
   
    ![Eenmalige aanmelding configureren5](./media/marketo-tutorial/tutorial_marketo_07.png) 
   
    d. Klik op de knop **Edit** om de SAML-instellingen in te schakelen.
   
    ![Eenmalige aanmelding configureren6](./media/marketo-tutorial/tutorial_marketo_08.png) 
   
    e. SSO-instellingen **ingeschakeld**.
   
    f. Plak de **Azure AD-id** in het tekstvak **Issuer-ID**.
   
    g. Voer in het tekstvak **Entity ID** de URL in als `http://saml.marketo.com/sp`.
   
    h. Selecteer de User ID Location **Name Identifier element**.
   
    ![Eenmalige aanmelding configureren7](./media/marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > Als uw gebruikers-id geen UPN-waarde is, wijzig de waarde dan op het tabblad Attribute.
   
    i. Upload het certificaat dat u hebt gedownload van de Azure AD-configuratiewizard. **Sla** de instellingen op.
   
    j. Bewerk de instellingen op de pagina Redirect Pages.
   
    k. Plak de **Aanmeldings-URL** in het tekstvak **Login URL**.
   
    l. Plak de **Afmeldings-URL** in het tekstvak **Logout URL**.
   
    m. Plak in het tekstvak **Error URL****de URL van uw Marketo-exemplaar** en klik op **Save** om de instellingen op te slaan.
   
    ![Eenmalige aanmelding configureren8](./media/marketo-tutorial/tutorial_marketo_10.png)

3. Voer de volgende acties uit om SSO in te schakelen voor gebruikers:
   
    a. Meld u bij Marketo aan met beheerdersreferenties.
   
    b. Klik in het bovenste navigatievenster op de knop **Admin**.
   
    ![Eenmalige aanmelding configureren9](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Navigeer naar het menu **Security** en klik op **Login Settings**.
   
    ![Eenmalige aanmelding configureren10](./media/marketo-tutorial/tutorial_marketo_13.png)
   
    d. Vink de optie **Require SSO** in en **sla de instellingen op**.
   
    ![Eenmalige aanmelding configureren11](./media/marketo-tutorial/tutorial_marketo_14.png)


### <a name="create-marketo-test-user"></a>Marketo-testgebruiker maken

In deze sectie gaat u in Marketo een gebruiker maken met de naam Britta Simon. Volg deze stappen om een gebruiker te maken in het Marketo-platform.

1. Meld u bij Marketo aan met beheerdersreferenties.

2. Klik in het bovenste navigatievenster op de knop **Admin**.
   
    ![testgebruiker1](./media/marketo-tutorial/tutorial_marketo_06.png) 

3. Navigeer naar het menu **Security** en klik op **User & Roles**
   
    ![testgebruiker2](./media/marketo-tutorial/tutorial_marketo_19.png)  

4. Klik op het tabblad Users op de koppeling **Invite New User**
   
    ![testgebruiker3](./media/marketo-tutorial/tutorial_marketo_15.png) 

5. Vul in de wizard Invite New User de volgende informatie in
   
    a. Voer het **e-mailadres** van de gebruiker in het tekstvak in
   
    ![testgebruiker4](./media/marketo-tutorial/tutorial_marketo_16.png)
   
    b. Voer de **voornaam** in het tekstvak in
   
    c. Voer de **achternaam** in het tekstvak in
   
    d. Klik op **Volgende**

6. Selecteer op het tabblad **Permissions** de optie **userRoles** en klik op **Next**
   
    ![testgebruiker5](./media/marketo-tutorial/tutorial_marketo_17.png)
7. Klik op de knop **Send** om de gebruikersuitnodiging te verzenden
   
    ![testgebruiker6](./media/marketo-tutorial/tutorial_marketo_18.png)

8. De gebruiker ontvangt de e-mailmelding en moet op de koppeling klikken en het wachtwoord wijzigen om het account te activeren. 

### <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties.

1. Klik op 'Deze toepassing testen' in de Azure-portal. U wordt automatisch aangemeld bij het exemplaar van Marketo waarvoor u eenmalige aanmelding hebt ingesteld.

1. U kunt het Microsoft-toegangsvenster gebruiken. Wanneer u in het toegangsvenster op de tegel Marketo klikt, wordt u automatisch aangemeld bij het exemplaar van Marketo waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="next-steps"></a>Volgende stappen

Zodra u Marketo hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).