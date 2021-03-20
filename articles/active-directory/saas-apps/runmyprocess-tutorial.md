---
title: 'Zelfstudie: Integratie van Azure Active Directory met RunMyProcess | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en RunMyProcess kunt configureren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/07/2019
ms.author: jeedes
ms.openlocfilehash: 011bbbda07806f1493ae27fbeef8509f0d12c44b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92518446"
---
# <a name="tutorial-integrate-runmyprocess-with-azure-active-directory"></a>Zelfstudie: RunMyProcess integreren met Azure Active Directory

In deze zelfstudie leert u hoe u RunMyProcess kunt integreren met Azure Active Directory (Azure AD). Wanneer u RunMyProcess integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot RunMyProcess.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij RunMyProcess.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* RunMyProcess-abonnement met eenmalige aanmelding (SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* RunMyProcess biedt ondersteuning voor eenmalige aanmelding die is gestart vanuit **SP**

## <a name="adding-runmyprocess-from-the-gallery"></a>RunMyProcess toevoegen vanuit de galerie

U moet RunMyProcess vanuit de galerie aan uw lijst met beheerde SaaS-apps toevoegen om de integratie van RunMyProcess in Azure AD te configureren.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen vanuit de galerie** in het zoekvak: **RunMyProcess**.
1. Selecteer **RunMyProcess** in de resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met RunMyProcess met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in RunMyProcess.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met RunMyProcess te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor RunMyProcess configureren](#configure-runmyprocess-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
4. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
5. **[Een RunMyProcess-testgebruiker maken](#create-runmyprocess-test-user)** : als u een equivalent van B.Simon in RunMyProcess wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **RunMyProcess** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://live.runmyprocess.com/live/<tenant id>`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met [ondersteuningsteam van RunMyProcess](mailto:support@runmyprocess.com) om de waarde te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **RunMyProcess instellen** kopieert u de juiste URL('s) op basis van uw vereisten.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-runmyprocess-sso"></a>Eenmalige aanmelding voor RunMyProcess configureren

1. Meld u in een ander browservenster als beheerder aan bij de RunMyProcess-tenant.

1. Klik in het linker navigatievenster op **Account** en selecteer **Configuratie**.

    ![Schermopname toont Configuratie geselecteerd bij Account.](./media/runmyprocess-tutorial/tutorial_runmyprocess_001.png)

1. Ga naar de sectie **Verificatiemethode** en voer de onderstaande stappen uit:

    ![Schermopname van het tabblad Verificatiemethode, waarin u de beschreven waarden kunt invoeren.](./media/runmyprocess-tutorial/tutorial_runmyprocess_002.png)

    a. Selecteer als **Methode** de optie **SSO met Samlv2**.

    b. Plak in het tekstvak **Omleiding voor eenmalige aanmelding** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    c. Plak in het tekstvak **Omleiding voor afmelding** de waarde van **Afmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    d. Typ in het tekstvak **Naam-ID-indeling** de waarde van de **Naam-id-indeling** als **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddresss**.

    e. Open het certificaatbestand dat u vanuit de Azure-portal hebt gedownload in Kladblok, kopieer de inhoud van het certificaatbestand en plak deze vervolgens in het tekstvak **Certificate** (Certificaat).

    f. Klik op het pictogram **Opslaan**.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot RunMyProcess.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **RunMyProcess** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-runmyprocess-test-user"></a>RunMyProcess-testgebruiker maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij RunMyProcess, moeten ze worden ingericht in RunMyProcess. In het geval van RunMyProcess is inrichten een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u als beheerder aan bij de bedrijfssite van RunMyProcess.

1. Klik op **Account** en selecteer **Gebruikers** in het linker navigatievenster en klik vervolgens op **Nieuwe gebruikers**.

    ![Nieuwe gebruiker](./media/runmyprocess-tutorial/tutorial_runmyprocess_003.png "Nieuwe gebruiker")

1. Voer in de sectie **Gebruikersinstellingen** de volgende stappen uit:

    ![Profiel](./media/runmyprocess-tutorial/tutorial_runmyprocess_004.png "Profiel")
  
    a. Typ de **Naam** en **E-mail** van een geldige Azure AD-account die u wilt inrichten in de bijbehorende tekstvakken.

    b. Selecteer een **IDE-taal**, **Taal** en **Profiel**.

    c. Selecteer **E-mail voor het maken van een account naar mij verzenden**.

    d. Klik op **Opslaan**.

    > [!NOTE]
    > U kunt ook alle andere hulpprogramma's voor het creëren van RunMyProcess-gebruikersaccounts of API's van RunMyProcess gebruiken om Azure Active Directory-gebruikersaccounts in te richten.

### <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Als u op de tegel RunMyProcess in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van RunMyProcess waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)