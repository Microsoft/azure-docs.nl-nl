---
title: 'Zelfstudie: Integratie van Azure Active Directory met Syncplicity | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Syncplicity.
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
ms.openlocfilehash: 3c665795325ed3863583eb0f21f3e0d3f534154a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103201505"
---
# <a name="tutorial-integrate-syncplicity-with-azure-active-directory"></a>Zelfstudie: Syncplicity integreren met Azure Active Directory

In deze zelfstudie leert u hoe u Syncplicity integreert met Azure Active Directory (Azure AD). Wanneer u Syncplicity integreert met Azure AD, kunt u het volgende:

* In Azure AD beheren wie er toegang heeft tot Syncplicity.
* Ervoor zorgen dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij Syncplicity.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u [hier](https://azure.microsoft.com/free/)een gratis proef versie van 12 maanden krijgen.
* Een abonnement op Syncplicity waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen. Syncplicity ondersteunt door **SP** geïnitieerde eenmalige aanmelding.

## <a name="adding-syncplicity-from-the-gallery"></a>Syncplicity toevoegen vanuit de galerie

Voor het configureren van de integratie van Syncplicity met Azure AD moet u Syncplicity vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Klik onder **maken** op **bedrijfs toepassing**.
1. Typ **Syncplicity** in het zoekvak in het gedeelte **Bladeren door Azure AD-galerie** .
1. Selecteer **Syncplicity** in het resultaten paneel en klik vervolgens op **maken** om de app toe te voegen. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Syncplicity met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Syncplicity.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Syncplicity te configureren en testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Syncplicity configureren](#configure-syncplicity-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
4. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
5. **[Testgebruiker maken in Syncplicity](#create-syncplicity-test-user)** : om een tegenhanger voor B.Simon te maken in Syncplicity die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.
7. **[SSO bijwerken](#update-sso)**) -de benodigde wijzigingen aanbrengen in Syncplicity als u de SSO-instellingen in azure AD hebt gewijzigd.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **Syncplicity** de sectie **aan** de slag en selecteer **eenmalige aanmelding instellen**.
2. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
3. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://<companyname>.syncplicity.com/sp`

    b. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<companyname>.syncplicity.com`
    
    c. Typ in het tekstvak **Antwoord-URL (URL voor Assertion Consumer Service)** een URL met het volgende patroon: `https://<companyname>.syncplicity.com/Auth/AssertionConsumerService.aspx`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [Klantondersteuningsteam van Syncplicity](https://www.syncplicity.com/contact-us) om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Klik op de pagina **eén Sign-On met SAML instellen** in het gedeelte **SAML-handtekening certificaat** op **bewerken**. Klik in het dialoog venster op de knop met het weglatings teken naast het actieve certificaat en selecteer **PEM certificaat downloaden**.

   ![De link om het certificaat te downloaden](common/certificatebase64.png)

    > [!NOTE]
    > U hebt het PEM-certificaat nodig, omdat Syncplicity geen certificaten accepteert in CER-indeling.

6. In de sectie **Syncplicity instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-syncplicity-sso"></a>Eenmalige aanmelding voor Syncplicity configureren

1. Meld u aan bij uw **Syncplicity**-tenant.

1. Klik in het menu aan de bovenkant op **Administrator**, selecteer **instellingen** en klik vervolgens op **aangepast domein en eenmalige aanmelding**.

    ![Syncplicity](./media/syncplicity-tutorial/ic769545.png "Syncplicity")

1. Voer op de pagina met het dialoogvenster **Eenmalige aanmelding** de volgende stappen uit:

    ![Eenmalige aanmelding \(SSO\)](./media/syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")

    a. Typ in het tekstvak **Aangepast domein** de naam van uw domein.
  
    b. Selecteer **Ingeschakeld** bij **Status voor eenmalige aanmelding**.

    c. Plak in het tekstvak **Entiteits-id** de waarde van **Id (Entiteits-id)** , die u hebt gebruikt in de **Standaard SAML-configuratie** in de Azure-portal.

    d. Plak in het tekstvak **URL voor aanmeldings pagina** de AANMELDINGS- **URL** die u hebt gekopieerd uit de Azure Portal.

    e. Plak in het tekstvak **URL Afmeldings pagina** de **afmeldings-URL** die u hebt gekopieerd uit de Azure Portal.

    f. Klik in **Certificaat van identiteitsprovider** op **Bestand kiezen** en upload vervolgens het certificaat dat u hebt gedownload uit de Azure-portal.

    g. Klik op **WIJZIGINGEN OPSLAAN**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in Azure Portal.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
2. Selecteer **Nieuwe gebruiker** boven aan het scherm.
3. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:

   a. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `B.Simon@contoso.com`.

   b. Voer in het veld **Naam**`B.Simon` in.  
   
   c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   
   d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Syncplicity.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Syncplicity** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker/groep toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)
1. Selecteer op de pagina **toewijzing toevoegen** de optie **gebruikers**. 
1. Selecteer in  het dialoog venster gebruikers **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik op de pagina **toewijzing toevoegen** op de knop **toewijzen** .

### <a name="create-syncplicity-test-user"></a>Syncplicity-testgebruiker maken

Voordat Azure AD-gebruikers zich kunnen aanmelden, moeten ze worden ingericht voor de Syncplicity-toepassing. In deze sectie wordt beschreven hoe u Azure AD-gebruikersaccounts maakt in Syncplicity.

**Voer de volgende stappen uit om een gebruikersaccount voor Syncplicity in te richten:**

1. Meld u aan bij uw **Syncplicity**-tenant (bijvoorbeeld: `https://company.Syncplicity.com`).

1. Klik op **beheerder** en selecteer **gebruikers accounts** en klik vervolgens op **een gebruiker toevoegen**.

    ![Gebruikers beheren](./media/syncplicity-tutorial/ic769764.png "Gebruikers beheren")

1. Typ de **e-mail adressen** van een Azure ad-account dat u wilt inrichten, selecteer **gebruiker** als **rol** en klik vervolgens op **volgende**.

    ![Accountgegevens](./media/syncplicity-tutorial/ic769765.png "Accountgegevens")

    > [!NOTE]
    > De houder van het Azure AD-account ontvangt een e-mail bericht met een koppeling om het account te bevestigen en te activeren.

1. Selecteer een groep in uw bedrijf waarvan uw nieuwe gebruiker lid moet worden, en klik vervolgens op **volgende**.

    ![Groepslidmaatschap](./media/syncplicity-tutorial/ic769772.png "Groepslidmaatschap")

    > [!NOTE]
    > Als er geen groepen worden weer gegeven, klikt u op **volgende**.

1. Selecteer de mappen die u wilt plaatsen onder het besturings element Syncplicity op de computer van de gebruiker en klik vervolgens op **volgende**.

    ![Syncplicity-mappen](./media/syncplicity-tutorial/ic769773.png "Syncplicity-mappen")

> [!NOTE]
> U kunt ook alle andere hulpprogramma's voor het maken van Syncplicity-gebruikersaccounts of API's van Syncplicity gebruiken om Azure AD-gebruikersaccounts in te richten.

### <a name="test-sso"></a>Eenmalige aanmelding testen

Wanneer u in het toegangsvenster de tegel Syncplicity selecteert, zou u automatisch moeten worden aangemeld bij het exemplaar van Syncplicity waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

### <a name="update-sso"></a>SSO bijwerken

Wanneer u wijzigingen moet aanbrengen aan de SSO, moet u het gebruikte **SAML-handtekening certificaat** controleren. Als het certificaat is gewijzigd, moet u ervoor zorgen dat u het nieuwe uploadt naar Syncplicity zoals beschreven in **[SYNCPLICITY SSO configureren](#configure-syncplicity-sso)**.

Als u de mobiele Syncplicity-app gebruikt, neemt u contact op met de klant ondersteuning van Syncplicity ( support@syncplicity.com ) voor hulp.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)
