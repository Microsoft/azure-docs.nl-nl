---
title: 'Zelfstudie: Azure Active Directory-integratie met ExpenseIn | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en ExpenseIn.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/17/2020
ms.author: jeedes
ms.openlocfilehash: eb7062b745ae6eb97dc2cd4ba2051cc221031630
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96179160"
---
# <a name="tutorial-integrate-expensein-with-azure-active-directory"></a>Zelfstudie: ExpenseIn integreren met Azure Active Directory

In deze zelfstudie leert u hoe u ExpenseIn kunt integreren met Azure Active Directory (Azure AD). Wanneer u ExpenseIn integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie toegang heeft tot ExpenseIn.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij ExpenseIn.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement waarvoor eenmalige aanmelding (SSO) van ExpenseIn is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen. 
* ExpenseIn ondersteunt door **SP en IDD** geïnitieerde eenmalige aanmelding.
* Zodra u ExpenseIn hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).


## <a name="adding-expensein-from-the-gallery"></a>ExpenseIn toevoegen vanuit de galerie

Als u de integratie van ExpenseIn met Azure AD wilt configureren, moet u ExpenseIn vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen vanuit de galerie** **ExpenseIn** in het zoekvak.
1. Selecteer **ExpenseIn** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-expensein"></a>Eenmalige aanmelding van Azure AD voor ExpenseIn configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met ExpenseIn met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in ExpenseIn.

Voltooi de volgende stappen om eenmalige aanmelding bij ExpenseIn met Azure AD te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[ExpenseIn SSO configureren](#configure-expensein-sso)** om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
    1. **[Een testgebruiker voor ExpenseIn maken](#create-expensein-test-user)** : als u een tegenhanger van B.Simon in ExpenseIn wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **ExpenseIn** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** hoeft de gebruiker geen stappen uit te voeren omdat de app al vooraf is geïntegreerd met Azure.

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL: `https://app.expensein.com/saml`

1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u in de sectie **SAML-handtekeningcertificaat** op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren. Klik vervolgens op **Downloaden** om het **Certificaat (Base64)** te downloaden en op uw computer op te slaan.

   ![De link om het certificaat te downloaden](./media/expensein-tutorial/copy-metdataurl-certificate.png)

1. In de sectie **ExpenseIn instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot ExpenseIn.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **ExpenseIn** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.


## <a name="configure-expensein-sso"></a>ExpenseIn SSO configureren

1. Als u de configuratie in ExpenseIn wilt automatiseren, moet u **My Apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

1. Als u op **ExpenseIn instellen** klikt nadat u de extensie aan de browser hebt toegevoegd, wordt u doorgestuurd naar de ExpenseIn-toepassing. Geef hier de Administrator-referenties op om u aan te melden bij ExpenseIn. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 t/m 5 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

1. Als u ExpenseIn handmatig wilt instellen, meldt u zich als beheerder aan bij de ExpenseIn-bedrijfssite.

1. Klik op **Beheer** bovenaan de pagina, navigeer naar **Eenmalige aanmelding** en klik op **Provider toevoegen**.

     ![Schermopname met het tabblad "Admin" en de pagina "Single Sign-On - Providers" en "Add Provider" geselecteerd.](./media/expenseIn-tutorial/config01.png)

1. Voer de volgende stappen uit in het pop-upvenster **Nieuwe identiteitsprovider**:

    ![Schermopname met het pop-upvenster Edit Identity Provider met ingevoerde waarden.](./media/expenseIn-tutorial/config02.png)

    a. Typ in het tekstvak **Providernaam** de naam, bijvoorbeeld Azure.

    b. Selecteer **Ja** voor **Door provider geïnitieerde aanmelding toestaan**.

    c. Plak in het tekstvak **Doel-URL** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit Azure Portal.

    d. Plak in het tekstvak **Verlener** de waarde van **Azure AD-id**, die u hebt gekopieerd uit Azure Portal.

    e. Open het certificaat (Base64) in Notepad, kopieer de inhoud ervan en plak deze in het tekstvak **Certificaat**.

    f. Klik op **Create**.

### <a name="create-expensein-test-user"></a>Testgebruiker voor ExpenseIn maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij ExpenseIn, moeten ze worden ingericht in ExpenseIn. In het geval van ExpenseIn is dat een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u als beheerder aan bij ExpenseIn.

2. Klik op **Beheer** bovenaan de pagina, navigeer naar **Gebruikers** en klik op **Nieuwe gebruiker**.

     ![Schermopname met het tabblad "Admin" en de pagina "Manage Users" met "New User" geselecteerd.](./media/expenseIn-tutorial/config03.png)

3. Voer in het pop-upvenster **Details** de volgende stappen uit:

    ![ExpenseIn-configuratie](./media/expenseIn-tutorial/config04.png)

    a. Typ in het tekstvak **First Name** de voornaam van de gebruiker, zoals **B**.

    b. Typ in het tekstvak **Achternaam** de achternaam van de gebruiker, zoals **Simon**.

    c. Voer in het tekstvak **E-mail** het e-mailadres van de gebruiker in, zoals `B.Simon@contoso.com`.

    d. Klik op **Create**.

## <a name="test-sso"></a>Eenmalige aanmelding testen

Wanneer u de tegel ExpenseIn selecteert in het toegangsvenster, zou u automatisch moeten worden aangemeld bij de instantie van ExpenseIn waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [ExpenseIn proberen met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [ExpenseIn beveiligen met geavanceerde zichtbaarheid en besturingselementen](/cloud-app-security/proxy-intro-aad)