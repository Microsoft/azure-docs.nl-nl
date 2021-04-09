---
title: 'Zelfstudie: Azure Active Directory-integratie met AlertOps | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding configureert tussen Azure Active Directory en AlertOps.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: 094b1a029c8e10abce3ca282de0cc99be34d313a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92318864"
---
# <a name="tutorial-integrate-alertops-with-azure-active-directory"></a>Zelfstudie: AlertOps integreren met Azure Active Directory

In deze zelfstudie leert u hoe u AlertOps kunt integreren met Azure AD (Active Directory). Wanneer u AlertOps integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot AlertOps.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij AlertOps.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een AlertOps-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen. AlertOps biedt ondersteuning voor met **SP en IDP** geïnitieerde SSO

## <a name="adding-alertops-from-the-gallery"></a>AlertOps toevoegen vanuit de galerie

Om de integratie van AlertOps in Azure AD te configureren, moet u AlertOps vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen vanuit de galerie** in het zoekvak: **AlertOps**.
1. Selecteer **AlertOps** in het resultatenpaneel en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met AlertOps met behulp van een testgebruiker met de naam **Britta Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de gerelateerde gebruiker in AlertOps.

Als u eenmalige aanmelding van Azure AD met AlertOps wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
2. **[AlertOps configureren](#configure-alertops)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** voor het testen van eenmalige aanmelding bij Azure AD met Britta Simon.
4. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat Britta Simon gebruik kan maken van eenmalige aanmelding bij Azure AD.
5. **[Een testgebruiker voor AlertOps maken](#create-alertops-test-user)** : als u een tegenhanger van Britta Simon in AlertOps wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-sso)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **AlertOps** naar de sectie **Beheren**, en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In het gedeelte **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

    1. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<SUBDOMAIN>.alertops.com`

    1. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<SUBDOMAIN>.alertops.com/login.aspx`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<SUBDOMAIN>.alertops.com/login.aspx`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [klantondersteuningsteam van AlertOps](mailto:support@alertops.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

   ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **AlertOps instellen** de juiste URL('s) op basis van uw behoeften.

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-alertops"></a>AlertOps configureren

1. Als u de configuratie in AlertOps wilt automatiseren, moet u **My Apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

2. Als u op **AlertOps instellen** klikt nadat u de extensie hebt toegevoegd aan de browser, wordt u doorgestuurd naar de AlertOps-toepassing. Geef hier de beheerdersreferenties op om u aan te melden bij AlertOps. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 t/m 5 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

3. Als u AlertOps Evernote handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij de bedrijfssite van AlertOps. Voer hierna de volgende stappen uit:

4. Klik in het linkernavigatievenster op **Account Settings**.

    ![Schermopname die het menu AlertOps toont met Accountinstellingen gemarkeerd.](./media/alertops-tutorial/configure1.png)

5. Selecteer **SSO** op de pagina **Subscription Settings** en voer de volgende stappen uit:

    ![Schermopname die het venster met abonnementsinstellingen toont voor ingevoerde waarden voor eenmalige aanmelding, zoals beschreven in deze stap.](./media/alertops-tutorial/configure2.png)

    a. Schakel het selectievakje **Use Single Sign-On(SSO)** in.

    b. Selecteer in de vervolgkeuzelijst **Azure Active Directory** als een **SSO Provider**.

    c. Voer in het tekstvak **URL-verlener** de id-waarde in die u hebt gebruikt in de sectie **Standaard SAML-configuratie** in de Azure-portal.

    d. Plak in het tekstvak **SAML-eindpunt-URL** de waarde van **Aanmeldings-URL** die u hebt gekopieerd in de Azure-portal.

    e. Plak in het tekstvak **SLO-eindpunt-URL** de waarde van **Aanmeldings-URL** die u hebt gekopieerd in de Azure-portal.

    f. Selecteer **SHA256** in de vervolgkeuzelijst **SAML Signature Algorithm**.

    g. Open het gedownloade bestand Certificate(Base64) in Kladblok. Kopieer de inhoud van het bestand naar het klembord en plak deze vervolgens in het tekstvak X.509 Certificate.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam Britta Simon maken in Azure Portal.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`Britta Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `BrittaSimon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u Britta Simon in staat gebruik te maken van eenmalige aanmelding van Azure door haar toegang te verlenen tot AlertOps.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **AlertOps** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst Gebruikers en klik op de knop **Selecteren** onder aan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-alertops-test-user"></a>Een testgebruiker voor AlertOps maken

1. Meld u in een ander browservenster als beheerder aan bij de bedrijfssite van AlertOps.

2. Klik op **Users** in het linkernavigatievenster.

    ![Schermopname die het menu AlertOps toont met Gebruikers gemarkeerd.](./media/alertops-tutorial/user1.png)

3. Selecteer **Add User**.

    ![Schermopname die het venster Gebruikers toont met de knop Gebruiker toevoegen.](./media/alertops-tutorial/user2.png)

4. Voer in het dialoogvenster **Add User** de volgende stappen uit:

    ![Schermopname die het deelvenster Gebruikers toevoegen toont met waarden die zijn ingevoerd zoals beschreven in deze stap.](./media/alertops-tutorial/user3.png)

    a. Voer in het tekstvak **Login User Name** de gebruikersnaam van de gebruiker in, bijvoorbeeld **Brittasimon**.

    b. Voer in het tekstvak **Officieel e-mailadres** het e-mailadres van de gebruiker in, zoals **Brittasimon\@contoso.com**.

    c. Voer in het tekstvak **First Name** de voornaam van de gebruiker in, zoals **Britta**.

    d. Voer in het tekstvak **Last Name** de achternaam van de gebruiker in, zoals **Simon**.

    e. Selecteer in de vervolgkeuzelijst **Type** een waarde die geschikt is voor uw organisatie.

    f. Selecteer in de vervolgkeuzelijst **Role** een waarde voor de rol van de gebruiker in uw organisatie.

    g. Selecteer **Toevoegen**.

### <a name="test-sso"></a>Eenmalige aanmelding testen

Wanneer u in het toegangsvenster op de tegel AlertOps klikt, wordt u automatisch aangemeld bij de instantie van AlertOps waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)