---
title: 'Zelfstudie: Azure Active Directory-integratie met ClickUp Productivity Platform | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en ClickUp Productivity Platform.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/30/2021
ms.author: jeedes
ms.openlocfilehash: 29bc02466825aa2ea2c1a9ece2826b1262293392
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106285152"
---
# <a name="tutorial-azure-active-directory-integration-with-clickup-productivity-platform"></a>Zelfstudie: Azure Active Directory-integratie met ClickUp Productivity Platform

In deze zelf studie leert u hoe u ClickUp Productivity platform integreert met Azure Active Directory (Azure AD). Wanneer u ClickUp-productiviteits platform integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot het ClickUp-productiviteits platform.
* Stel uw gebruikers in staat om automatisch te worden aangemeld voor ClickUp-productiviteits platform met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* ClickUp-abonnement voor eenmalige aanmelding (SSO) met een productiviteits platform.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* ClickUp Productivity platform ondersteunt door **SP** geïnitieerde SSO.

## <a name="add-clickup-productivity-platform-from-the-gallery"></a>ClickUp-productiviteits platform toevoegen vanuit de galerie

Om de integratie van ClickUp Productivity Platform te configureren in Azure AD, moet u ClickUp Productivity Platform vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **ClickUp productiviteits platform** in het zoekvak.
1. Selecteer **ClickUp Productivity platform** in het resultaten paneel en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-clickup-productivity-platform"></a>Azure AD SSO configureren en testen voor ClickUp-productiviteits platform

Configureer en test Azure AD SSO met ClickUp-productiviteits platform met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in ClickUp Productivity platform.

Voer de volgende stappen uit om Azure AD SSO te configureren en te testen met ClickUp-productiviteits platform:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[ClickUp Productivity platform SSO configureren](#configure-clickup-productivity-platform-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een ClickUp-gebruikers platform test gebruiker](#create-clickup-productivity-platform-test-user)** -om een soort tegen te brengen van B. Simon in ClickUp-productiviteits platform dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de Azure Portal op de pagina **ClickUp Productivity platform** Application Integration de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. In het tekstvak **Aanmeldings-URL** typt u de URL: `https://app.clickup.com/login/sso`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://api.clickup.com/v1/team/<team_id>/microsoft`

    > [!NOTE]
    > De id-waarde is niet echt. Vervang deze waarde door de werkelijke id op de manier die verderop in deze zelfstudie wordt uitgelegd.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot het ClickUp-productiviteits platform.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **ClickUp Productivity Platform** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u verwacht dat er een rol aan de gebruikers moet worden toegewezen, kunt u de rol selecteren in de vervolgkeuzelijst **Selecteer een rol**. Als er geen rol is ingesteld voor deze app, wordt de rol Standaardtoegang geselecteerd.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-clickup-productivity-platform-sso"></a>ClickUp Productivity platform SSO configureren

1. Meld u in een ander browservenster als beheerder aan bij de ClickUp Productivity Platform-tenant.

2. Klik op het **Gebruikersprofiel** en selecteer **Instellingen**.

    ![Schermopname toont de ClickUp Productivity-tenant met het pictogram Instellingen geselecteerd.](./media/clickup-productivity-platform-tutorial/configure-0.png)

    ![Schermopname toont de instellingen.](./media/clickup-productivity-platform-tutorial/configure-1.png)

3. Selecteer **Microsoft** onder Single Sign-On (SSO) Provider (Provider voor eenmalige aanmelding).

    ![Schermopname toont het deelvenster Verificatie waarin Microsoft is geselecteerd.](./media/clickup-productivity-platform-tutorial/configure-2.png)

4. Op de pagina **Configure Microsoft Single Sign On** (Eenmalige aanmelding via Microsoft configureren) voert u de volgende stappen uit:

    ![Schermopname toont de pagina 'Eenmalige aanmelding bij Microsoft configureren', waar u de entiteit-id kunt kopiëren en de Azure Federation metagegevens-URL opslaat.](./media/clickup-productivity-platform-tutorial/configure-3.png)

    a. Klik op **Kopiëren** om de waarde van de entiteits-ID te kopiëren en plak deze in het tekstvak voor de **id (entiteits-ID)** in het gedeelte **Standaard SAML-configuratie** in de Azure-portal.

    b. Plak in het tekstvak **Azure Federation Metadata URL** (URL voor federatieve metagegevens voor Azure) de waarde voor de app-URL voor federatieve metagegevens die u hebt gekopieerd uit de Azure-portal en klik op **Opslaan**.

5. U voltooit de installatie door op **Authenticate With Microsoft to complete setup** (Verifiëren met Microsoft om de installatie te voltooien) te klikken en te verifiëren met een Microsoft-account.

    ![Schermopname toont de knop 'Verifiëren met Microsoft om de installatie te voltooien'.](./media/clickup-productivity-platform-tutorial/configure-4.png)


### <a name="create-clickup-productivity-platform-test-user"></a>Testgebruiker voor ClickUp Productivity Platform maken

1. Meld u in een ander browservenster als beheerder aan bij de ClickUp Productivity Platform-tenant.

2. Klik op het **Gebruikersprofiel** en selecteer vervolgens **Personen**.

    ![Schermopname toont de ClickUp-productiviteitstenant.](./media/clickup-productivity-platform-tutorial/configure-0.png)

    ![Schermopname toont de geselecteerde link 'Mensen'.](./media/clickup-productivity-platform-tutorial/user-1.png)

3. Voer het e-mailadres van de gebruiker in het tekstvak in en klik op **Uitnodigen**.

    ![Schermopname toont 'Instellingen voor teamgebruikers' waar u personen kunt uitnodigen per e-mail.](./media/clickup-productivity-platform-tutorial/user-2.png)

    > [!NOTE]
    > De gebruiker zal de melding ontvangen en moet de uitnodiging accepteren om het account te activeren.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. Dit wordt omgeleid naar de aanmeldings-URL van het ClickUp-productiviteits platform, waar u de aanmeldings stroom kunt initiëren. 

* Ga rechtstreeks naar de aanmeldings-URL van de ClickUp-productiviteits platform en start de aanmeldings stroom vanaf daar.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u op de tegel ClickUp Productivity platform in de mijn apps klikt, wordt dit omgeleid naar de URL voor het ClickUp-productiviteits platform. Zie [Introduction to My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Zodra u ClickUp Productivity platform hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).