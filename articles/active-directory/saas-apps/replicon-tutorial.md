---
title: 'Zelfstudie: Integratie van Azure Active Directory met Replicon | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Replicon.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.openlocfilehash: 8915d780e79fa219428c54bad5458ab5966df6c1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101688498"
---
# <a name="tutorial-integrate-replicon-with-azure-active-directory"></a>Zelfstudie: Replicon integreren met Azure Active Directory

In deze zelfstudie leert u hoe u Replicon kunt integreren met Azure Active Directory (Azure AD). Wanneer u Replicon integreert met Azure AD, kunt u:

* In Azure AD beheren wie er toegang heeft tot Replicon.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Replicon.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) een gratis proefversie van één maand ontvangen.
* Een abonnement op Replicon waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

> [!NOTE]
> Deze integratie is ook beschikbaar voor gebruik vanuit de Azure AD US Government Cloud-omgeving. U kunt deze toepassing vinden in de toepassingsgalerie van Azure AD US Government Cloud en deze op dezelfde manier configureren als vanuit een openbare cloud.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen. Replicon biedt ondersteuning voor met **SP** geïnitieerde eenmalige aanmelding.

## <a name="adding-replicon-from-the-gallery"></a>Replicon toevoegen uit de galerie

Om de integratie van Replicon in Azure AD te configureren, moet u Replicon vanuit de galerij toevoegen aan uw lijst van beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **Replicon**.
1. Selecteer **Replicon** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Replicon met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Replicon.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Replicon te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding bij Replicon configureren](#configure-replicon-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
4. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
5. **[Testgebruiker voor Replicon maken](#create-replicon-test-user)** : als u een tegenhanger van B.Simon in Replicon wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Replicon** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Op de pagina **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    1. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://global.replicon.com/!/saml2/<client name>/sp-sso/post`

    1. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://global.replicon.com/!/saml2/<client name>`

    1. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://global.replicon.com/!/saml2/<client name>/sso/post`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, de antwoord-URL en de aanmeldings-URL. Neem voor deze waarden contact op met het [clientondersteuningsteam van Replicon](https://www.replicon.com/customerzone/contact-support). U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Klik op het pictogram bewerken/pen voor **SAML-handtekeningcertificaat** om de instellingen te bewerken.

    ![Algoritme voor ondertekening](common/signing-algorithm.png)

    1. Selecteer **SAML-bewering ondertekenen** in **Optie voor ondertekening**.

    1. Selecteer **SHA-256** in **Algoritme voor ondertekening**.

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

   ![De link om het certificaat te downloaden](common/metadataxml.png)

### <a name="configure-replicon-sso"></a>Eenmalige aanmelding bij Replicon configureren

1. Meld u in een andere browser als beheerder aan bij uw bedrijfssite in Replicon.

2. Voer de volgende stappen uit om SAML 2.0 te configureren:

    ![SAML-verificatie inschakelen](./media/replicon-tutorial/ic777805.png "SAML-verificatie inschakelen")

    a. Als u het dialoog venster **EnableSAMLAuthentication2** wilt weergeven, voegt u het volgende toe aan uw URL na uw bedrijfssleutel: `/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

    * Hieronder ziet u het schema van de volledige URL: `https://na2.replicon.com/<YourCompanyKey>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

   b. Klik op de **+** om de sectie **v20Configuration** uit te vouwen.

   c. Klik op de **+** om de sectie **metaDataConfiguration** uit te vouwen.

   d. Selecteer **SHA256** voor xmlSignatureAlgorithm

   e. Klik op **Bestand kiezen**, selecteer het XML-bestand met de metagegevens van uw id-provider en klik op **Verzenden**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in Azure Portal.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `BrittaSimon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Replicon.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Replicon** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-replicon-test-user"></a>Testgebruiker voor Replicon maken

Het doel van dit gedeelte is het maken van een gebruiker met de naam B.Simon in Replicon.

**Als u de gebruiker handmatig moet maken, voert u de volgende stappen uit:**

1. Meld u in een webbrowservenster als beheerder aan bij uw bedrijfssite in Replicon.

2. Ga naar **Beheer \> Gebruikers**.

    ![Gebruikers](./media/replicon-tutorial/ic777806.png "Gebruikers")

3. Klik op **+ Gebruiker toevoegen**.

    ![Gebruiker toevoegen](./media/replicon-tutorial/ic777807.png "Gebruiker toevoegen")

4. Voer in het gedeelte **Gebruikersprofiel** de volgende stappen uit:

    ![Gebruikersprofiel](./media/replicon-tutorial/ic777808.png "Gebruikersprofiel")

    a. Typ in het tekstvak **Aanmeldingsnaam** het e-mailadres van de Azure AD-gebruiker die u wilt inrichten, zoals `B.Simon@contoso.com`.

    > [!NOTE]
    > De aanmeldingsnaam moet overeenkomen met het e-mailadres van de gebruiker in Azure AD

    b. Selecteer het **verificatietype** **Eenmalige aanmelding**.

    c. Stel de verificatie-id in op dezelfde waarde als de aanmeldingsnaam (het e-mailadres van de Azure AD-gebruiker)

    d. Typ in het tekstvak **Afdeling** de afdeling van de gebruiker.

    e. Selecteer **Beheerder** als het **type werknemer**.

    f. Klik op **Gebruikersprofiel opslaan**.

> [!NOTE]
> U kunt ook alle andere hulpprogramma's voor het maken van gebruikersaccounts of API's van Replicon gebruiken om Microsoft Azure Active Directory-gebruikersaccounts in te richten.

### <a name="test-sso"></a>Eenmalige aanmelding testen

Wanneer u de tegel Replicon selecteert in het toegangsvenster, zou u automatisch moeten worden aangemeld bij de instantie van Replicon waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)