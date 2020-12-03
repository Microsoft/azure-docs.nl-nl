---
title: 'Zelfstudie: Integratie van Azure Active Directory met TigerConnect Secure Messenger | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding configureert tussen Azure Active Directory en TigerConnect Secure Messenger.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/21/2020
ms.author: jeedes
ms.openlocfilehash: 6831e96ebd8fd2db51d640ff3d93955b1a9f1477
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182152"
---
# <a name="tutorial-azure-active-directory-integration-with-tigerconnect-secure-messenger"></a>Zelfstudie: Integratie van Azure Active Directory met TigerConnect Secure Messenger

In deze zelfstudie leert u hoe u TigerConnect Secure Messenger kunt integreren met Azure Active Directory (Azure AD).

Integratie van TigerConnect Secure Messenger met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot TigerConnect Secure Messenger.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij TigerConnect Secure Messenger (eenmalige aanmelding).
* U kunt uw accounts vanaf één locatie beheren, de Azure-portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding van Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps (Software as a Service) met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om Azure AD-integratie met TigerConnect Secure Messenger te configureren:

* Een Azure AD-abonnement Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
* Een abonnement op TigerConnect Secure Messenger waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen en TigerConnect Secure Messenger integreren met Azure AD.

* TigerConnect Secure Messenger ondersteunt door **SP** geïnitieerde eenmalige aanmelding
* Zodra u TigerConnect Secure Messenger hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-tigerconnect-secure-messenger-from-the-gallery"></a>TigerConnect Secure Messenger toevoegen uit de galerie

Voor het configureren van de integratie van TigerConnect Secure Messenger met Azure AD moet u TigerConnect Secure Messenger uit Azure Marketplace toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **TigerConnect Secure Messenger**.
1. Selecteer **TigerConnect Secure Messenger** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren en testen

In dit gedeelte gaat u eenmalige aanmelding van Azure AD met TigerConnect Secure Messenger configureren en testen op basis van een testgebruiker met de naam **Britta Simon**. Eenmalige aanmelding werkt alleen als er een koppeling tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in TigerConnect Secure Messenger tot stand is gebracht.

Voltooi de volgende bouwstenen om eenmalige aanmelding van Azure AD met TigerConnect Secure Messenger te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** voor het testen van eenmalige aanmelding bij Azure AD met Britta Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat Britta Simon gebruik kan maken van eenmalige aanmelding bij Azure AD.
1. **[Eenmalige aanmelding configureren voor TigerConnect Secure Messenger](#configure-tigerconnect-secure-messenger-sso)** om de instellingen voor eenmalige aanmelding aan de toepassingszijde te configureren.
    * **[Een testgebruiker voor TigerConnect Secure Messenger maken](#create-a-tigerconnect-secure-messenger-test-user)** , zodat een gebruiker met de naam Britta Simon in TigerConnect Secure Messenger is gekoppeld aan de Azure AD-gebruiker met de naam Britta Simon.
1. **[Eenmalige aanmelding testen](#test-sso)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met TigerConnect Secure Messenger te configureren:

1. Zoek in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **TigerConnect Secure Messenger** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    1. Voer in het vak **Aanmeldings-URL** een URL in:

       `https://home.tigertext.com`

    1. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon:

       `https://saml-lb.tigertext.me/v1/organization/<instance ID>`

    > [!NOTE]
    > De waarde voor **Id (Entiteits-id)** is niet echt. Werk deze waarde bij met de werkelijke id. Neem contact op met het [ondersteuningsteam van TigerConnect Secure Messenger](mailto:prosupport@tigertext.com) om de waarde op te vragen. U kunt ook verwijzen naar de patronen die worden weergegeven in het deelvenster **Standaard SAML-configuratie** in de Azure-portal.

1. Selecteer op de pagina **Eenmalige aanmelding instellen met SAM** in de sectie **SAML-handtekeningcertificaat** de optie **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden van de beschikbare opties en op te slaan op uw computer.

    ![De optie voor het downloaden van het XML-bestand met federatieve metagegevens](common/metadataxml.png)

1. In de sectie **TigerConnect Secure Messenger instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot TigerConnect Secure Messenger.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **TigerConnect Secure Messenger** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-tigerconnect-secure-messenger-sso"></a>Eenmalige aanmelding van TigerConnect Secure Messenger configureren

Als u eenmalige aanmelding aan de zijde van TigerConnect Secure Messenger wilt configureren, moet u het gedownloade XML-bestand met federatieve metagegevens en de correcte uit de Azure-portal gekopieerde URL's verzenden naar het [ondersteuningsteam van TigerConnect Secure Messenger](mailto:prosupport@tigertext.com). Het team van TigerConnect Secure Messenger zorgt ervoor dat de SAML SSO-verbinding aan beide kanten juist wordt ingesteld.

## <a name="create-a-tigerconnect-secure-messenger-test-user"></a>Een testgebruiker voor TigerConnect Secure Messenger maken

In dit gedeelte gaat u een gebruiker met de naam Britta Simon maken in TigerConnect Secure Messenger. Voeg in samenwerking met het [ondersteuningsteam van TigerConnect Secure Messenger](mailto:prosupport@tigertext.com) Britta Simon als gebruiker toe aan TigerConnect Secure Messenger. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In dit gedeelte test u de configuratie voor eenmalige aanmelding met Azure AD met behulp van de portal Mijn apps.

Wanneer u **TigerConnect Secure Messenger** selecteert in de portal Mijn apps, moet u automatisch worden aangemeld bij het TigerConnect Secure Messenger-abonnement waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Inleiding tot de portal Mijn apps](../user-help/my-apps-portal-end-user-access.md) voor meer informatie over de portal Mijn apps.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies voor het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Eenmalige aanmelding van TigerConnect Secure Messenger configureren](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)