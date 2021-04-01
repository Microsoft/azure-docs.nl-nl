---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Moxtra | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Moxtra.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: jeedes
ms.openlocfilehash: be63ea7ebf905b825ffe73dfc2b37dcdc3bb88fd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92507513"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-moxtra"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Moxtra

In deze zelfstudie leert u hoe u Moxtra integreert met Azure Active Directory (Azure AD). Wanneer u Moxtra integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie toegang heeft tot Moxtra.
* Uw gebruikers zich automatisch laten aanmelden met hun Azure AD-account bij Moxtra.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een Moxtra-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Moxtra ondersteunt door **SP** geïnitieerde eenmalige aanmelding

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.

## <a name="adding-moxtra-from-the-gallery"></a>Moxtra uit de galerie toevoegen

Voor het configureren van de integratie van Moxtra met Microsoft Azure Active Directory moet u Moxtra vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **Moxtra** in het zoekvak in de sectie **Toevoegen uit de galerie**.
1. Selecteer **Moxtra** in de resultaten en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-moxtra"></a>Eenmalige aanmelding bij Moxtra via Azure AD configureren en testen

Configureer en test eenmalige aanmelding bij Moxtra via Azure AD met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Moxtra.

Voltooi de volgende stappen om eenmalige aanmelding via Azure AD bij Moxtra te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Moxtra configureren](#configure-moxtra-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een Moxtra-testgebruiker maken](#create-moxtra-test-user)** : als u een equivalent van B.Simon in Moxtra wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Moxtra** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    In het tekstvak **Aanmeldings-URL** typt u een URL: `https://www.moxtra.com/service/#login`

1. In uw Moxtra-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Klik op het pictogram **Bewerken** om het dialoogvenster gebruikerskenmerken te openen.

    ![image](common/edit-attribute.png)

1. Bovendien verwacht de Moxtra-toepassing nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. In de sectie Gebruikersclaims in het dialoogvenster Gebruikerskenmerken voert u de volgende stappen uit om het kenmerk van het SAML-token toe te voegen zoals wordt weergegeven in de onderstaande tabel:

    | Naam | Bronkenmerk|
    | ------------------- | -------------------- |    
    | firstname | user.givenname |
    | lastname | user.surname |
    | idpid    | < Azure AD-id >

    > [!Note]
    > De waarde voor het kenmerk **idpid** is niet echt. U kunt de werkelijke waarde ophalen uit het gedeelte **Moxtra instellen** uit stap 8. 

    1. Klik op **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    1. In het tekstvak **Naam** typt u de naam van het kenmerk die voor die rij wordt weergegeven.

    1. Laat **Naamruimte** leeg.

    1. Selecteer Bron bij **Kenmerk**.

    1. Typ de kenmerkwaarde voor die rij in de lijst met **bronkenmerken**.

    1. Klik op **OK**.

    1. Klik op **Opslaan**.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **Moxtra instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Moxtra.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Moxtra** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-moxtra-sso"></a>Eenmalige aanmelding bij Moxtra configureren

1. Meld u in een ander browservenster als beheerder aan bij de bedrijfssite van Moxtra.

2. Klik in de werkbalk aan de linkerkant op **Beheerconsole > Eenmalige aanmelding via SAML** en klik vervolgens op **Nieuwe**.
   
    ![Schermopname van de pagina Eenmalige aanmelding met SAML met de optie om een nieuwe eenmalige SAML-aanmelding te maken.](./media/moxtra-tutorial/tutorial_moxtra_06.png) 

3. Voer op de pagina **SAML** de volgende stappen uit:
   
    ![Schermopname van de SAML-pagina waarin u de beschreven waarden kunt invoeren.](./media/moxtra-tutorial/tutorial_moxtra_08.png)   
 
    a. Typ in het tekstvak **Naam** een naam voor de configuratie (bijv.: *SAML*). 
  
    b. Plak in het tekstvak **IDP-entiteits-id** de waarde van **Azure AD-id** die u hebt gekopieerd uit Azure Portal. 
 
    c. Plak in het tekstvak **Login URL** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal. 
 
    d. Typ in het tekstvak **AuthnContextClassRef** de tekst **urn:oasis:names:tc:SAML:2.0:ac:classes:Password**. 
 
    e. Typ in het tekstvak **Naam-id-indeling** de tekst **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**. 
 
    f. Open het certificaat dat u hebt gedownload vanuit Azure Portal in Kladblok, kopieer de inhoud en plak deze in het tekstvak **Certificaat**.    
 
    g. Typ uw SAML-e-maildomein in het tekstvak SAML-e-maildomein.    
  
    >[!NOTE]
    >Als u de stappen voor het verifiëren van het domein wilt zien, klikt u op de onderstaande **i**.

    h. Klik op **Update**.

### <a name="create-moxtra-test-user"></a>Een Moxtra-testgebruiker maken

Het doel van dit gedeelte is het maken van een gebruiker met de naam B.Simon in Moxtra.

**Voer de volgende stappen uit om een gebruiker met de naam B.Simon te maken in Moxtra:**

1. Meld u als beheerder aan bij de bedrijfssite van Moxtra.

1. Klik in de werkbalk aan de linkerkant op **Beheerconsole > Gebruikersbeheer** en klik vervolgens op **Gebruiker toevoegen**.
   
    ![Schermopname van de pagina Gebruikersbeheer met Gebruiker toevoegen geselecteerd.](./media/moxtra-tutorial/tutorial_moxtra_10.png) 

1. Voer in het dialoogvenster **Add User** de volgende stappen uit:
  
    a. Typ in het tekstvak **Voornaam** de letter **B**.
  
    b. Typ **Simon** in het tekstvak **Last Name**.
  
    c. Typ in het tekstvak **E-mail** het e-mailadres van B.Simon. Gebruik hetzelfde e-mailadres als in Azure Portal.
  
    d. Typ **Dev** in het tekstvak **Divisie**.
  
    e. Typ **IT** in het tekstvak **Afdeling**.
  
    f. Selecteer **Beheerder**.
  
    g. Klik op **Add**.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Moxtra in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van Moxtra waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Moxtra met Azure AD proberen](https://aad.portal.azure.com/)