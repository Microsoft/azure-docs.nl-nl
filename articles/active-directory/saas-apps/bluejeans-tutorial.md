---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met BlueJeans voor Azure AD | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding kunt configureren tussen Azure Active Directory en BlueJeans voor Azure AD.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/09/2020
ms.author: jeedes
ms.openlocfilehash: a920abacb0df030f7d38aed11e04a693cfa352a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101686570"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bluejeans-for-azure-ad"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met BlueJeans voor Azure AD

In deze zelfstudie leert u hoe u BlueJeans voor Azure AD kunt integreren met Azure AD (Azure Active Directory). Wanneer u BlueJeans voor Azure AD integreert met Azure AD, kunt u het volgende doen:

* Bepalen in Azure AD wie toegang heeft tot BlueJeans voor Azure AD.
* Ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij BlueJeans voor Azure AD met hun Azure AD-account.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op BlueJeans voor Azure AD met eenmalige aanmelding (SSO).

> [!NOTE]
> Deze integratie is ook beschikbaar voor gebruik vanuit de Azure AD US Government Cloud-omgeving. U kunt deze toepassing vinden in de toepassingsgalerie van Azure AD US Government Cloud en deze op dezelfde manier configureren als vanuit een openbare cloud.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* BlueJeans voor Azure AD biedt ondersteuning voor met **SP** geïnitieerde eenmalige aanmelding

* BlueJeans voor Azure AD biedt ondersteuning voor [**Geautomatiseerd** inrichten van gebruikers](bluejeans-provisioning-tutorial.md)

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één instantie in één tenant kan worden geconfigureerd.

## <a name="adding-bluejeans-for-azure-ad-from-the-gallery"></a>BlueJeans voor Azure AD toevoegen uit de galerie

Als u de integratie van BlueJeans voor Azure AD in Azure AD wilt configureren, moet u BlueJeans voor Azure AD uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **BlueJeans voor Azure AD**.
1. Selecteer **BlueJeans voor Azure AD** in de zoekresultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-bluejeans-for-azure-ad"></a>Eenmalige aanmelding van Azure AD configureren en testen voor BlueJeans voor Azure AD

Configureer en test eenmalige aanmelding van Azure AD met BlueJeans voor Azure AD met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in BlueJeans voor Azure AD.

Voltooi de volgende bouwstenen om eenmalige aanmelding van Azure AD met BlueJeans voor Azure AD te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor BlueJeans voor Azure AD configureren](#configure-bluejeans-for-azure-ad-sso)** : als u de instellingen voor eenmalige aanmelding aan de clientzijde wil configureren.
    1. **[Testgebruiker maken in BlueJeans voor Azure AD](#create-bluejeans-for-azure-ad-test-user)** : om in BlueJeans voor Azure AD een tegenhanger van B.Simon te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de **BlueJeans voor Azure AD**-toepassing naar de sectie **Beheren**, en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<companyname>.bluejeans.com`

    a. Typ een URL in het vak **Id (Entiteits-id)** : `http://samlsp.bluejeans.com`

    a. In het tekstvak **Antwoord-URL** typt u een URL: `https://bluejeans.com/sso/saml2/`

    > [!NOTE]
    > De waarde voor de aanmeldings-URL is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [ondersteuningsteam van BlueJeans voor Azure AD](https://support.bluejeans.com/contact) om deze waarde te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. In de BlueJeans-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. Bovendien verwacht de BlueJeans-toepassing nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.

    | Naam |  Bronkenmerk|
    | ---------| --------- |
    | Telefoon | user.telephonenumber |
    | titel | user.jobtitle |

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op de computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **BlueJeans voor Azure AD instellen** kopieert u de juiste URL('s) op basis van uw vereisten.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot BlueJeans voor Azure AD.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **BlueJeans voor Azure AD** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-bluejeans-for-azure-ad-sso&quot;></a>Eenmalige aanmelding voor BlueJeans voor Azure AD configureren

1. Meld u in een ander webbrowservenster bij uw **BlueJeans voor Azure AD**-bedrijfssite aan als beheerder.

2. Ga naar **ADMIN \> GROEPSINSTELLINGEN \> BEVEILIGING**.

    ![Schermopname die een deel van een browservenster toont waarin het tabblad Admin is geselecteerd en Groepsinstellingen en Beveiliging zijn geselecteerd.](./media/bluejeans-tutorial/ic785868.png &quot;Beheerder")

3. Voer in het gedeelte **BEVEILIGING** de volgende stappen uit:

    ![SAML-eenmalige aanmelding](./media/bluejeans-tutorial/ic785869.png "SAML-eenmalige aanmelding")

    a. Selecteer **SAML-eenmalige aanmelding**.

    b. Selecteer **Automatische inrichting inschakelen**.

4. Ga verder met de volgende stappen:

    ![Certificaatpad](./media/bluejeans-tutorial/ic785870.png "Certificaatpad")

    a. Klik op **Bestand kiezen** om het base-64 gecodeerde certificaat dat u hebt gedownload vanuit de Azure-portal te uploaden.

    b. Plak in het tekstvak **Aanmeldings-URL** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    c. Plak in het tekstvak **Wachtwoord-URL wijzigen** de waarde van **Wachtwoord-URL wijzigen** die u hebt gekopieerd uit de Azure-portal.

    d. Plak in het tekstvak **Afmeldings-URL** de waarde van **Afmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

5. Ga verder met de volgende stappen:

    ![Wijzigingen opslaan](./media/bluejeans-tutorial/ic785874.png "Wijzigingen opslaan")

    a. Typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` in het tekstvak **Gebruikers-id**.

    b. Typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` in het tekstvak **E-mailadres**.

    c. Klik op **WIJZIGINGEN OPSLAAN**.

### <a name="create-bluejeans-for-azure-ad-test-user"></a>Testgebruiker maken in BlueJeans voor Azure AD

Het doel van dit gedeelte is het maken van een gebruiker met de naam B.Simon in BlueJeans voor Azure AD. BlueJeans voor Azure AD Biedt ondersteuning voor het automatisch inrichten van gebruikers, wat standaard is ingeschakeld. U kunt [hier](bluejeans-provisioning-tutorial.md) meer informatie vinden over het configureren van het automatisch inrichten van gebruikers.

**Als u de gebruiker handmatig moet maken, voert u de volgende stappen uit:**

1. Meld u bij uw **BlueJeans voor Azure AD**-bedrijfssite aan als beheerder.

2. Ga naar **ADMIN \> GEBRUIKERS BEHEREN \> GEBRUIKER TOEVOEGEN**.

    ![Schermopname die een deel van een browservenster toont waarin het tabblad Admin is geselecteerd en Gebruikers beheren en Gebruiker toevoegen zijn geselecteerd.](./media/bluejeans-tutorial/ic785877.png "Beheerder")

    > [!IMPORTANT]
    > Het tabblad **GEBRUIKER TOEVOEGEN** is alleen beschikbaar als op het tabblad **BEVEILIGING** **Automatische inrichting inschakelen** niet is ingeschakeld.

3. Voer in het gedeelte **GEBRUIKER TOEVOEGEN** de volgende stappen uit:

    ![Schermopname die de sectie Gebruiker toevoegen toont, waarin u de informatie kunt invoeren die in deze stap is beschreven.](./media/bluejeans-tutorial/ic785886.png "Gebruiker toevoegen")

    a. Typ in het tekstvak **First Name** de voornaam van de gebruiker, zoals **B**.

    b. Typ in het tekstvak **Achternaam** de achternaam van de gebruiker, zoals **Simon**.

    c. Voer in het tekstvak **Pick a BlueJeans voor Azure AD Username** de gebruikersnaam van de gebruiker in, bijvoorbeeld **Brittasimon**

    d. Voer in het tekstvak **Create a Password** uw wachtwoord in.

    e. Voer in het tekstvak **Company** de naam van uw bedrijf in.

    f. Voer in het tekstvak **Email Address** het e-mailadres van de gebruiker in, zoals `b.simon@contoso.com`.

    g. Geef uw vergaderings-id op in het tekstvak **Create a BlueJeans voor Azure AD Meeting I.D**.

    h. Geef uw wachtwoordcode op in het tekstvak **Pick a Moderator Passcode**.

    i. Klik op **CONTINUE**.

    ![Schermopname die de sectie Gebruiker toevoegen toont, waarin u instellingen en functies kunt bekijken en waar de knop Gebruiker toevoegen is geselecteerd.](./media/bluejeans-tutorial/ic785887.png "Gebruiker toevoegen")

    J. Klik op **ADD USER**.

> [!NOTE]
> U kunt ook alle andere hulpprogramma's voor het maken van BlueJeans voor Azure AD-gebruikersaccounts of API's van BlueJeans voor Azure AD gebruiken om Azure AD-gebruikersaccounts in te richten.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de BlueJeans for Azure AD-tegel klikt, wordt u automatisch aangemeld bij de instantie van BlueJeans for Azure AD waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [BlueJeans voor Azure AD uitproberen met Azure AD](https://aad.portal.azure.com/)