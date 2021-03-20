---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met CloudPassage | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding configureert tussen Azure Active Directory en CloudPassage.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: jeedes
ms.openlocfilehash: b720f7e49fc0679de5c3f430122bab05d5b706f8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92455749"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cloudpassage"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met CloudPassage

In deze zelfstudie leert u hoe u CloudPassage kunt integreren met Azure Active Directory (Azure AD). Wanneer u CloudPassage integreert met Azure AD, kunt u het volgende:

* U kunt in Azure AD beheren wie toegang heeft tot CloudPassage.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij CloudPassage.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op CloudPassage waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* CloudPassage ondersteunt door **SP** geïnitieerde eenmalige aanmelding

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.

## <a name="adding-cloudpassage-from-the-gallery"></a>CloudPassage toevoegen vanuit de galerie

Om de integratie van CloudPassage in Azure AD te configureren, moet u CloudPassage vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Ga naar de sectie **Toevoegen uit de galerie** en typ **CloudPassage** in het zoekvak.
1. Selecteer **CloudPassage** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-cloudpassage"></a>Eenmalige aanmelding van Azure AD configureren en testen voor CloudPassage

Configureer en test eenmalige aanmelding van Azure AD met CloudPassage met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in CloudPassage.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met CloudPassage te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding van CloudPassage configureren](#configure-cloudpassage-sso)** : de instellingen voor eenmalige aanmelding aan de clientzijde configureren.
    1. **[Een testgebruiker voor CloudPassage maken](#create-cloudpassage-test-user)** : als u in CloudPassage een tegenhanger van B.Simon wilt hebben die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/) op de toepassingsintegratiepagina van **CloudPassage** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

     a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://portal.cloudpassage.com/saml/init/accountid`

    b. In het tekstvak **Antwoord-URL** typt u een URL en gebruikt u het volgende patroon: `https://portal.cloudpassage.com/saml/consume/accountid`. U kunt de waarde voor dit kenmerk opvragen door te klikken op **SSO Setup documentation** in het gedeelte **Single Sign-on Settings** van de CloudPassage-portal.

    ![Schermopname toont de CloudPassage-Portal met de koppeling S S O Setup Documentation opgeroepen.](./media/cloudpassage-tutorial/tutorial_cloudpassage_05.png)

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en antwoord-URL. Neem contact op met het [klantondersteuningsteam van CloudPassage](https://www.cloudpassage.com/company/contact/) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. In de CloudPassage-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/edit-attribute.png)

1. Bovendien verwacht de CloudPassage-toepassing nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.

    | Naam | Bronkenmerk|
    | ---------------| --------------- |
    | firstname |user.givenname |
    | lastname |user.surname |
    | e-mail |user.mail |

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op de computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **CloudPassage instellen** de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot CloudPassage.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **CloudPassage** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-cloudpassage-sso"></a>Eenmalige aanmelding van CloudPassage configureren

1. Meld u in een ander browservenster als beheerder aan bij de bedrijfssite van CloudPassage.

1. Klik in de menubalk bovenaan op **Settings** en klik vervolgens op **Site Administration**. 
   
    ![Schermopname toont de CloudPassage-site waarop Sitebeheer is geselecteerd.][12]

1. Klik op het tabblad **Authentication Settings**. 
   
    ![Schermopname toont de CloudPassage-site waarop Verificatie-instellingen is geselecteerd.][13]

1. Voer de volgende stappen uit in het gedeelte **Single Sign-on Settings**: 
   
    ![Schermopname toont de sectie 'Instellingen voor eenmalige aanmelding', waarin u de informatie kunt invoeren die in deze stap is beschreven.][14]

    a. Schakel het selectievakje **Enable Single Sign-on (SSO) (SSO Setup Documentation)** in.
    
    b. Plak de **Azure AD-id** in het tekstvak **URL van SAML-verlener**.
  
    c. Plak de **aanmeldings-URL** in het tekstvak **SAML endpoint URL**.
  
    d. Plak de **afmeldings-URL** in het tekstvak **Logout landing page**.
  
    e. Open het gedownloade certificaat in Kladblok, kopieer de inhoud van het certificaat naar het Klembord en plak de inhoud vervolgens in het tekstvak **x.509 certificate**.
  
    f. Klik op **Opslaan**.

### <a name="create-cloudpassage-test-user"></a>Een testgebruiker voor CloudPassage maken

Het doel van deze sectie is het maken van een gebruiker met de naam B.Simon in CloudPassage.

**Voer de volgende stappen uit om in CloudPassage een gebruiker met de naam B.Simon te maken:**

1. Meld u als beheerder aan bij de bedrijfssite van **CloudPassage**. 

1. Klik in de werkbalk bovenaan op **Settings** en klik vervolgens op **Site Administration**. 
   
    ![Schermopname toont CloudPassage waarop Sitebeheer is geselecteerd.][22] 

1. Klik op het tabblad **Users** en klik vervolgens op **Add New User**. 
   
    ![Schermopname toont CloudPassage Sitebeheer met het tabblad Gebruikers geselecteerd. Hier is de optie om nieuwe gebruiker toe te voegen te zien.][23]

1. Voer in het gedeelte **Add New User** de volgende stappen uit: 
   
    ![Schermopname toont de sectie 'Nieuwe gebruikers toevoegen' waarin u gebruikersgegevens kunt invoeren.][24]
    
    a. Typ Britta in het tekstvak **First Name**. 
  
    b. Typ Simon in het tekstvak **Last Name**.
  
    c. Typ in de tekstvakken **Username**, **Email** en **Retype Email** de gebruikersnaam van Britta uit Azure AD.
  
    d. Selecteer **Enable Halo Portal Access** bij **Access Type**.
  
    e. Klik op **Add**.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel van CloudPassage klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van CloudPassage waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [CloudPassage proberen met Azure AD](https://aad.portal.azure.com/)

<!--Image references-->

[12]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_10.png
[22]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_17.png