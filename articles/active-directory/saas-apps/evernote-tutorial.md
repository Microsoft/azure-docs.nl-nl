---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Evernote | Microsoft Docs'
description: Lees hoe u eenmalige aanmelding configureert tussen Microsoft Azure Active Directory en Evernote.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/17/2019
ms.author: jeedes
ms.openlocfilehash: 86a314cd5255c06a70d0f9b28d06e3ac4156fdb6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92453824"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-evernote"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Evernote

In deze zelfstudie leert u hoe u Evernote kunt integreren met Azure AD (Active Directory). Wanneer u Evernote integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot Evernote.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Evernote.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een Evernote-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Evernote ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.

## <a name="adding-evernote-from-the-gallery"></a>Evernote toevoegen vanuit de galerie

Om de configuratie van Evernote in Microsoft Azure Active Directory te configureren, moet u Evernote vanuit de galerie aan uw lijst met beheerde SaaS-apps toevoegen.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen vanuit de galerie** in het zoekvak: **Evernote**.
1. Selecteer **Evernote** in het resultatenpaneel en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-evernote"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Evernote

Configureer en test eenmalige aanmelding van Azure AD met Evernote met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Evernote.

Als u eenmalige aanmelding van Azure AD met Evernote wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding met Evernote configureren](#configure-evernote-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een testgebruiker voor Evernote maken](#create-evernote-test-user)** : als u een tegenhanger van B.Simon in Evernote wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Evernote** naar de sectie **Beheren**, en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    In het tekstvak **Id** typt u een URL: `https://www.evernote.com/saml2`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL: `https://www.evernote.com/Login.action`

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op de computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

7. Als u de opties voor **Handtekening** wilt wijzigen, klikt u op de knop **Bewerken** om het dialoogvenster **SAML-handtekeningcertificaat** te openen.

    ![Schermopname met het dialoogvenster "S A M L-handtekeningcertificaat" met de knop "Bewerken" geselecteerd.](common/edit-certificate.png) 

    ![image](./media/evernote-tutorial/samlassertion.png)

    a. Selecteer **SAML-antwoord en -bewering ondertekenen** voor **de Optie voor ondertekening**.

    b. Klik op **Opslaan**.

1. Kopieer in de sectie **Evernote instellen** de juiste URL('s) op basis van uw behoeften.

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

In deze sectie stelt u B.Simon in staat gebruik te maken van eenmalige aanmelding van Azure door haar toegang te verlenen tot Evernote.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Evernote** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-evernote-sso"></a>Eenmalige aanmelding van Evernote configureren

1. Als u de configuratie in Evernote wilt automatiseren, moet u **My Apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

2. Als u op **Evernote instellen** klikt nadat u de extensie hebt toegevoegd aan de browser, wordt u doorgestuurd naar de Evernote-toepassing. Geef hier de beheerdersreferenties op om u aan te melden bij Evernote. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 t/m 6 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

3. Als u Evernote handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij de bedrijfssite van Evernote. Voer hierna de volgende stappen uit:

4. Ga naar **'Admin Console'**

    ![Admin-Console](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

5. Ga vanuit de **'Admin Console'** naar **‘Security’** en selecteer **‘Single Sign-On’**

    ![SSO-instelling](./media/evernote-tutorial/tutorial_evernote_sso.png)

6. Configureer de volgende waarden:

    ![Certificaatinstelling](./media/evernote-tutorial/tutorial_evernote_certx.png)
    
    a.  **Enable SSO:** SSO is standaard ingeschakeld (klik op **Disable Single Sign-on** om het SSO vereiste te verwijderen)

    b. Plak de waarde van de **aanmeldings-URL**, die u uit de Microsoft Azure-portal naar het tekstvak **SAML HTTP Request URL** hebt gekopieerd.

    c. Open het gedownloade certificaat van Microsoft Azure Active Directory in Kladblok en kopieer de inhoud, inclusief "BEGIN CERTIFICATE" en "END CERTIFICATE" en plak deze in het tekstvak **X.509 Certificate**. 

    d. Klik op **Save Changes**

### <a name="create-evernote-test-user"></a>Evernote-testgebruiker maken

Als u Azure AD-gebruikers de mogelijkheid wilt bieden om zich aan te melden bij Evernote, moeten ze worden ingericht in Evernote.  
In het geval van Evernote is inrichten een handmatige taak.

**Ga als volgt te werk om een gebruikersaccount in te richten:**

1. Meld u als beheerder aan bij uw bedrijfssite van Evernote.

2. Klik op de **'Admin Console'**.

    ![Admin-Console](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

3. Ga vanuit de **'Admin Console'** naar **‘Add users’**.

    ![Schermopname met het menu "Users" met "Add Users" geselecteerd.](./media/evernote-tutorial/create_aaduser_0001.png)

4. **Voeg teamleden toe** in het tekstvak **Email**, typ het e-mailadres van het gebruikersaccount en klik op **Invite.**

    ![Testgebruiker toevoegen](./media/evernote-tutorial/create_aaduser_0002.png)
    
5. Nadat de uitnodiging is verzonden, ontvangt de houder van het Microsoft Azure Active Directory-account een e-mail om de uitnodiging te accepteren.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Evernote klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van Evernote waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Evernote uitproberen met Azure AD](https://aad.portal.azure.com/)