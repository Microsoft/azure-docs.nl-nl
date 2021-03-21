---
title: 'Zelfstudie: Integratie van eenmalige aanmelding bij Azure Active Directory met Zscaler B2B User Portal | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Zscaler B2B User Portal.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/24/2020
ms.author: jeedes
ms.openlocfilehash: f6b7fb79b5aab1ee3ea6b5b710c766c1a34b099c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92519806"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zscaler-b2b-user-portal"></a>Zelfstudie: Integratie van eenmalige aanmelding bij Azure Active Directory met Zscaler B2B User Portal

In deze zelfstudie leert u hoe u Zscaler B2B User Portal kunt integreren met Azure Active Directory (Azure AD). Wanneer u Zscaler B2B User Portal integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie toegang heeft tot Zscaler B2B User Portal.
* Uw gebruikers zich automatisch laten aanmelden bij Zscaler B2B User Portal met hun Azure AD-account.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Zscaler B2B User Portal waarvoor eenmalige aanmelding is ingeschakeld.

> [!NOTE]
> Deze integratie is ook beschikbaar voor gebruik vanuit de Azure AD US Government Cloud-omgeving. U kunt deze toepassing vinden in de toepassingsgalerie van Azure AD US Government Cloud en deze op dezelfde manier configureren als vanuit een openbare cloud.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Zscaler B2B User Portal ondersteunt door **IDP** geïnitieerde eenmalige aanmelding

* Zscaler B2B User Portal ondersteunt het **Just In Time** inrichten van gebruikers

* Zodra u Zscaler B2B User Portal hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

## <a name="adding-zscaler-b2b-user-portal-from-the-gallery"></a>Zscaler B2B User Portal toevoegen vanuit de galerie

Als u de integratie van Zscaler B2B User Portal met Azure AD wilt configureren, moet u Zscaler B2B User Portal vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. In de sectie **Toevoegen vanuit de galerie** typt u **Zscaler B2B User Portal** in het zoekvak.
1. Selecteer **Zscaler B2B User Portal** in het deelvenster met resultaten en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zscaler-b2b-user-portal"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Zscaler B2B User Portal

Configureer en test eenmalige aanmelding van Azure AD met Zscaler B2B User Portal met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Zscaler B2B User Portal.

Als u eenmalige aanmelding van Azure AD met Zscaler B2B User Portal wilt configureren en testen, moet u de volgende bouwblokken voltooien:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor Zscaler B2B User Portal configureren](#configure-zscaler-b2b-user-portal-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wil configureren.
    1. **[Testgebruiker voor Zscaler B2B User Portal maken](#create-zscaler-b2b-user-portal-test-user)** : als u een tegenhanger van B.Simon in Zscaler B2B User Portal wilt hebben die gekoppeld is aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de [Azure-portal](https://portal.azure.com/) op de toepassingsintegratiepagina van **Zscaler B2B User Portal** naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Op de pagina **Eenmalige aanmelding instellen met SAML** voert u de waarden voor de volgende velden in:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://samlsp.private.zscaler.com/auth/metadata/<UniqueID>`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://samlsp.private.zscaler.com/auth/login?domain=EXAMPLE`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem contact op met het [Zscaler B2B User Portal Client-ondersteuningsteam](https://help.zscaler.com/) om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in de sectie **Zscaler B2B User Portal instellen** de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding met Azure te gebruiken door toegang te verlenen tot Zscaler B2B User Portal.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Zscaler B2B User Portal** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-zscaler-b2b-user-portal-sso"></a>Eenmalige aanmelding voor Zscaler B2B User Portal configureren

1. Open een nieuw webbrowservenster en meld u als beheerder bij uw bedrijfssite van Zscaler B2B User Portal aan en voer de volgende stappen uit:

1. Klik aan de linkerkant van het menu op **Beheer**, navigeer naar de sectie **VERIFICATIE** en klik op **IdP-configuratie**.

    ![Zscaler Private Access Administrator-beheer](./media/zscaler-b2b-user-tutorial/tutorial-zscaler-private-access-administration.png)

1. Klik in de rechterbovenhoek op **IdP-configuratie toevoegen**. 

    ![Zscaler Private Access Administrator-IdP](./media/zscaler-b2b-user-tutorial/tutorial-zscaler-private-access-idp.png)

1. Voer op de pagina **IdP-configuratie toevoegen** de volgende stappen uit:
 
    ![Zscaler Private Access Administrator selecteren](./media/zscaler-b2b-user-tutorial/tutorial-zscaler-private-access-select.png)

    a. Klik op **Bestand selecteren** om het gedownloade metagegevensbestand uit Azure AD te uploaden in het veld **IdP-metagegevensbestand uploaden**.

    b. De **IdP-metagegevens** uit Azure AD worden gelezen en de overeenkomende velden worden ingevuld, zoals hieronder weergegeven.

    ![Zscaler Private Access Administrator configureren](./media/zscaler-b2b-user-tutorial/config.png)

    c. Selecteer in het veld **Domeinen** uw domein.
    
    d. Klik op **Opslaan**.

### <a name="create-zscaler-b2b-user-portal-test-user"></a>Zscaler B2B User Portal-testgebruiker maken

In deze sectie wordt er een gebruiker met de naam Britta Simon gemaakt in Zscaler B2B User Portal. Zscaler B2B User Portal ondersteunt het Just In Time inrichten van gebruikers, wat standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in Zscaler B2B User Portal bestaat, wordt er een nieuwe gemaakt nadat deze is geverifieerd.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Zscaler B2B User Portal in het toegangsvenster klikt, moet u automatisch worden aangemeld bij de instantie van Zscaler B2B User Portal waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Zscaler B2B User Portal proberen met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)