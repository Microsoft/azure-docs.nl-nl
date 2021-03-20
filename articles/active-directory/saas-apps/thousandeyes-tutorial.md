---
title: 'Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met ThousandEyes | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en ThousandEyes.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/04/2019
ms.author: jeedes
ms.openlocfilehash: aa3c5115a5255d30decbc66691878ffbe2579a06
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92514585"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-thousandeyes"></a>Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met ThousandEyes

In deze zelfstudie leert u hoe u ThousandEyes integreert met Azure Active Directory (Azure AD). Wanneer u ThousandEyes integreert met Azure AD, kunt u het volgende doen:

* In Azure AD regelen wie toegang heeft tot ThousandEyes.
* Ervoor zorgen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij ThousandEyes.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* ThousandEyes-abonnement met eenmalige aanmelding ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* ThousandEyes ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding
* ThousandEyes ondersteunt het [**geautomatiseerd** inrichten van gebruikers](./thousandeyes-provisioning-tutorial.md)

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.

## <a name="adding-thousandeyes-from-the-gallery"></a>ThousandEyes toevoegen vanuit de galerie

Voor het configureren van de integratie van ThousandEyes in Azure Active Directory, moet u ThousandEyes vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen vanuit de galerie** **ThousandEyes** in het zoekvak.
1. Selecteer **ThousandEyes** in resultatenvenster en voeg de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-thousandeyes"></a>Azure AD-eenmalige aanmelding configureren en testen voor ThousandEyes

Configureer en test eenmalige aanmelding van Azure AD met ThousandEyes met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in ThousandEyes.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met ThousandEyes te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor ThousandEyes configureren](#configure-thousandeyes-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    * **[Een ThousandEyes-testgebruiker maken](#create-thousandeyes-test-user)** : als u een equivalent van B.Simon in ThousandEyes wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **ThousandEyes** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **SAML-basisconfiguratie** is de toepassing vooraf geconfigureerd en zijn de benodigde URL's al vooraf ingevuld met Azure. De gebruiker moet de configuratie opslaan door op de knop **Opslaan** te klikken.

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL: `https://app.thousandeyes.com/login/sso`

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op de computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **ThousandEyes instellen** kopieert u de juiste URL('s) op basis van uw vereisten.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot ThousandEyes.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **ThousandEyes** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-thousandeyes-sso"></a>Eenmalige aanmelding voor ThousandEyes configureren

1. Meld u in een ander browservenster als beheerder aan bij uw **ThousandEyes**-bedrijfssite.

2. Klik in het menu aan de bovenkant op **Settings**.

    ![Schermopname van de ThousandEyes-site met Settings geselecteerd.](./media/thousandeyes-tutorial/ic790066.png "Instellingen")

3. Klik op **Account**

    ![Schermopname met Account geselecteerd in het menu Settings.](./media/thousandeyes-tutorial/ic790067.png "Account")

4. Klik op het tabblad **Beveiliging en verificatie**.

    ![Beveiligingsverificatie](./media/thousandeyes-tutorial/ic790068.png "Beveiliging en verificatie")

5. Voer de volgende stappen uit in het gedeelte **Eenmalige aanmelding instellen**:

    ![Eenmalige aanmelding instellen](./media/thousandeyes-tutorial/ic790069.png "Eenmalige aanmelding instellen")

    a. Schakel het selectievakje **Eenmalige aanmelding inschakelen** in.

    b. Plak in het tekstvak **Aanmeldingspagina-URL** de **Aanmeldings-URL** die u in de Azure-portal hebt gekopieerd.

    c. Plak in het tekstvak **Afmeldingspagina-URL** de **Afmeldings-URL** die u in de Azure-portal hebt gekopieerd.

    d. Plak in het tekstvak **Identity Provider Issuer** de **Azure AD-id** die u hebt gekopieerd in de Azure-portal.

    e. Klik in **Verificatiecertificaat** op **Kies bestand** en upload vervolgens het certificaat dat u hebt gedownload uit de Azure-portal.

    f. Klik op **Opslaan**.

### <a name="create-thousandeyes-test-user"></a>ThousandEyes-testgebruiker maken

Dit gedeelte is bedoeld om een gebruiker met de naam Britta Simon te maken in ThousandEyes. ThousandEyes ondersteunt het automatisch inrichten van gebruikers, wat standaard is ingeschakeld. U kunt [hier](thousandeyes-provisioning-tutorial.md) meer informatie vinden over het configureren van het automatisch inrichten van gebruikers.

**Als u de gebruiker handmatig moet maken, voert u de volgende stappen uit:**

1. Meld u als beheerder aan bij de bedrijfssite van ThousandEyes.

2. Klik op **Instellingen**.

    ![Schermopname van de ThousandEyes-site met Settings geselecteerd.](./media/thousandeyes-tutorial/ic790066.png "Instellingen")

3. Klik op **Account**.

    ![Schermopname met Account geselecteerd in het menu Settings.](./media/thousandeyes-tutorial/ic790067.png "Account")

4. Klik op het tabblad **Accounts en gebruikers**.

    ![Accounts en gebruikers](./media/thousandeyes-tutorial/IC790073.png "Accounts en gebruikers")

5. Voer in de sectie **Gebruikers en accounts toevoegen** de volgende stappen uit:

    ![Gebruikersaccounts toevoegen](./media/thousandeyes-tutorial/IC790074.png "Gebruikersaccounts toevoegen")

    a. Typ in het tekstvak **naam** de naam van de gebruiker zoals **B. Simon**.

    b. Typ in het tekstvak **Email** het e-mailadres van de gebruiker, bijvoorbeeld b.simon@contoso.com.

    b. Klik op **Nieuwe gebruiker toevoegen aan account**.

    > [!NOTE]
    > De houder van het Azure Active Directory-account krijgt een e-mailbericht met een koppeling om het account te bevestigen en te activeren.

> [!NOTE]
> U kunt ook alle andere hulpprogramma's voor het creëren van ThousandEyes-gebruikersaccounts of API's van ThousandEyes gebruiken om Azure Active Directory-gebruikersaccounts in te richten.


## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel ThousandEyes klikt, wordt u automatisch aangemeld bij de instantie van ThousandEyes waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Probeer ThousandEyes met Azure AD](https://aad.portal.azure.com/)

- [Inrichten van gebruikers configureren](./thousandeyes-provisioning-tutorial.md)