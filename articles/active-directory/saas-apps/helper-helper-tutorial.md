---
title: 'Zelfstudie: Integratie van Azure Active Directory met Helper Helper | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Helper Helper.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/31/2019
ms.author: jeedes
ms.openlocfilehash: 0f85f3c747c634e0118ac516fc83284350fc2584
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92445015"
---
# <a name="tutorial-integrate-helper-helper-with-azure-active-directory"></a>Zelfstudie: Integratie van Azure Active Directory met Helper Helper

In deze zelfstudie leert u hoe u Helper Helper kunt integreren met Azure AD (Active Directory). Wanneer u Helper Helper integreert met Azure AD, kunt u:

* In Azure AD beheren wie toegang heeft tot Helper Helper.
* Ervoor zorgen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Helper Helper.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Helper Helper waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen. Helper Helper ondersteunt met **SP en IDP** geïnitieerde eenmalige aanmelding en het **Just-In-Time** inrichten van gebruikers.

## <a name="adding-helper-helper-from-the-gallery"></a>Helper Helper toevoegen vanuit de galerie

Voor het configureren van de integratie van Helper Helper met Azure AD moet u Helper Helper vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **Helper Helper** in het zoekvak.
1. Selecteer **Helper Helper** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Helper Helper met behulp van een testgebruiker met de naam **B. Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Helper Helper.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met Helper Helper te configureren en testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Helper Helper configureren](#configure-helper-helper)** om de instellingen voor eenmalige aanmelding aan de toepassingszijde te configureren.
3. **[Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user)** om eenmalige aanmelding van Azure AD te testen met B.Simon.
4. **[Wijs de Azure AD-testgebruiker toe](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
5. **[Een Helper Helper-testgebruiker maken](#create-helper-helper-test-user)** : als u een tegenhanger van B. Simon wilt hebben in Helper Helper die is gekoppeld aan de weergave van een gebruiker in Azure AD.
6. **[Eenmalige aanmelding testen](#test-sso)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/), op de integratiepagina van de toepassing **Helper Helper**, het gedeelte **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in het gedeelte **Standaard SAML-configuratie** de volgende stappen uit als u het **Service Provider-metagegevensbestand** hebt, en in de met **IDP** geïnitieerde modus wilt configureren:

    >[!NOTE]
    >Klik op deze URL, `https://sso.helperhelper.com/saml/<customer_id>`, om de metagegevens van de serviceprovider op te halen. Neem contact op met het [ondersteuningsteam van Helper Helper](mailto:info@helperhelper.com) voor `<customer_id>`.

    a. Klik op **Metagegevensbestand uploaden**.

    b. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

    c. Nadat het bestand met metagegevens is geüpload, worden de waarden voor **Id** en **antwoord-URL** automatisch ingevuld in de sectie Standaard SAML-configuratie.

    > [!Note]
    > Als de waarden voor **Id** en **Antwoord-URL** niet automatisch worden ingevuld, kunt u de waarden zelf invullen afhankelijk van uw behoeften.

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://sso.helperhelper.com/saml/<customer_id>/login`

    > [!NOTE]
    > De waarde voor de aanmeldings-URL is niet echt. Werk deze waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [ondersteuningsteam van Helper Helper](mailto:info@helperhelper.com) om deze waarde te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in het gedeelte **Standaard-SAML-configuratie** in Azure Portal.

1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u in het gedeelte **SAML-handtekeningcertificaat** op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren. Sla deze daarna op Kladblok op.

   ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

1. Kopieer in de sectie **Helper Helper instellen** de juiste URL('s) op basis van uw vereisten.

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-helper-helper"></a>Helper Helper configureren

Als u eenmalige aanmelding wilt configureren aan de zijde van **Helper Helper**, moet u de **App-URL voor federatieve metagegevens** naar het [ondersteuningsteam van Helper Helper](mailto:info@helperhelper.com) verzenden. Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B. Simon maken in Azure Portal.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B. Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `BrittaSimon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B. Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Helper Helper.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Helper Helper** in de lijst toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B. Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-helper-helper-test-user"></a>Helper Helper-testgebruiker maken

In deze sectie wordt een gebruiker met de naam Britta Simon gemaakt in Helper Helper. Helper Helper biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in Helper Helper bestaat, wordt er een nieuwe gemaakt na de verificatie.

### <a name="test-sso"></a>Eenmalige aanmelding testen

Wanneer u de Helper Helper-tegel selecteert in het toegangsvenster, wordt u automatisch aangemeld bij de instantie van Helper Helper waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)