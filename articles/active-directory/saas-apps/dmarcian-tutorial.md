---
title: 'Zelfstudie: Azure Active Directory-integratie met dmarcian | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en dmarcian.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/01/2019
ms.author: jeedes
ms.openlocfilehash: 4a5ea47352bfbe86fe194821da0b6bf10818caf9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92454725"
---
# <a name="tutorial-integrate-dmarcian-with-azure-active-directory"></a>Zelfstudie: dmarcian met Azure Active Directory integreren

In deze zelfstudie leert u hoe u dmarcian kunt integreren met Azure Active Directory (Azure AD). Wanneer u dmarcian integreert met Azure AD, kunt u het volgende:

* In Azure AD bepalen wie er toegang heeft tot dmarcian.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij dmarcian.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op dmarcian waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* dmarcian ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-dmarcian-from-the-gallery"></a>Dmarcian vanuit de galerie toevoegen

Om de integratie van dmarcian in Azure AD te configureren, moet u dmarcian vanuit de galerij toevoegen aan uw lijst van beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **dmarcian** in het zoekvak in het gedeelte **Toevoegen uit de galerie**.
1. Selecteer **dmarcian** in het paneel resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met dmarcian met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in dmarcian.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met dmarcian te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding bij dmarcian configureren](#configure-dmarcian-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
4. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
5. **[Testgebruiker voor dmarcian maken](#create-dmarcian-test-user)** - als u een tegenhanger van B.Simon in dmarcian wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **dmarcian** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon:

    ```http
    https://us.dmarcian.com/sso/saml/<ACCOUNT_ID>/sp.xml
    https://dmarcian-eu.com/sso/saml/<ACCOUNT_ID>/sp.xml
    https://dmarcian-ap.com/sso/saml/<ACCOUNT_ID>/sp.xml
    ```

    b. In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon:

    ```http
    https://us.dmarcian.com/login/<ACCOUNT_ID>/handle/
    https://dmarcian-eu.com/login/<ACCOUNT_ID>/handle/
    https://dmarcian-ap.com/login/<ACCOUNT_ID>/handle/
    ```

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    Typ in het tekstvak **Aanmeldings-URL** een URL met het volgende patroon:
    
    ```http
    https://us.dmarcian.com/login/<ACCOUNT_ID>
    https://dmarcian-eu.com/login/<ACCOUNT_ID>
    https://dmarciam-ap.com/login/<ACCOUNT_ID>
    ```
     
    > [!NOTE] 
    > Dit zijn geen echte waarden. U gaat deze waarden bijwerken met de daadwerkelijke id, antwoord-URL en URL voor eenmalige aanmelding, wat later in de zelfstudie nog wordt uitgelegd.

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

### <a name="configure-dmarcian-sso"></a>Dmarcian SSO configureren

1. Als u de configuratie in dmarcian wilt automatiseren, moet u **My Apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

2. Als u op **dmarcian instellen** klikt nadat u de extensie aan de browser hebt toegevoegd, wordt u doorgestuurd naar de dmarcian-toepassing. Geef hier de Administrator-referenties op om u aan te melden bij dmarcian. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 t/m 6 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

3. Als u dmarcian handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij de dmarcian-bedrijfssite. Voer daarna de volgende stappen uit:

4. Klik op **Profiel** in de rechterbovenhoek hoek en navigeer naar **Voorkeuren**.

    ![Voorkeuren](./media/dmarcian-tutorial/tutorial_dmarcian_pref.png)

5. Schuif omlaag en klik op de sectie **Eenmalige aanmelding** en klik vervolgens op **Configureren**.

    ![Eenmalig](./media/dmarcian-tutorial/tutorial_dmarcian_sso.png)

6. Zet op de **Eenmalige aanmelding op basis van SAML** de **Status** op **Ingeschakeld** en voer de volgende stappen uit:

    ![Verificatie](./media/dmarcian-tutorial/tutorial_dmarcian_auth.png)

    * In de sectie **dmarcian aan uw id-provider toevoegen** klikt u op **Kopiëren** om de **ACS-URL** voor uw exemplaar te kopiëren, en plak deze in het tekstvak **Antwoord-URL** in de sectie **SAML-basisconfiguratie** in de Azure-portal.

    * In de sectie **dmarcian aan uw id-provider toevoegen** klikt u op **Kopiëren** om de **Entiteits-id** voor uw exemplaar te kopiëren, en plak deze in het tekstvak **Id** in de sectie **SAML-basisconfiguratie** in de Azure-portal.

    * In de sectie **Verificatie instellen** plakt u in het testvak **Metadata id-provider** de **App-URL voor federatieve metagegevens** die u uit de Azure-portal hebt gekopieerd.

    * Onder de sectie **Verificatie instellen** plakt u in het tekstvak **Kenmerkinstructies** tekstvak de URL `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    * In de sectie **Aanmeldings-URL instellen** kopieert u de **aanmeldings-URL** voor uw exemplaar en plak deze in het tekstvak **Aanmeldings-URL** in de sectie **SAML-basisconfiguratie** in de Azure-portal.

        > [!Note]
        > U kunt de **aanmeldings-URL** aanpassen in overeenstemming met uw organisatie.

    * Klik op **Opslaan**.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot dmarcian.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **dmarcian** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-dmarcian-test-user"></a>Testgebruiker voor dmarcian maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij dmarcian, moeten ze worden ingericht in dmarcian. In het geval van dmarcian is dat een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u bij dmarcian aan als een beveiligingsbeheerder.

2. Klik op **Profiel** in de rechterbovenhoek hoek en navigeer naar **Gebruiker beheren**.

    ![Gebruiker](./media/dmarcian-tutorial/tutorial_dmarcian_user.png)

3. Aan de rechterkant van de sectie **SSO-gebruikers** klikt u op **Nieuwe gebruiker toevoegen**.

    ![Gebruiker toevoegen](./media/dmarcian-tutorial/tutorial_dmarcian_addnewuser.png)

4. Voer in de pop-up **Nieuwe gebruiker toevoegen** de volgende stappen uit:

    ![Nieuwe gebruiker](./media/dmarcian-tutorial/tutorial_dmarcian_save.png)

    a. Voer in het tekstvak **Email nieuwe gebruiker** het e-mailadres van de gebruiker in, bijvoorbeeld **brittasimon\@contoso.com**.

    b. Als u beheerdersrechten aan de gebruiker wilt verlenen, selecteert u **Gebruiker beheerder maken**.

    c. Klik op **Add User**.

### <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel dmarcian in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van dmarcian waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)