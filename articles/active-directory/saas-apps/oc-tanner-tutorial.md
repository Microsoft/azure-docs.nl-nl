---
title: 'Zelfstudie: Azure Active Directory-integratie met O.C. Tanner - AppreciateHub | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en O.C. Tanner - AppreciateHub.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/28/2020
ms.author: jeedes
ms.openlocfilehash: 683cfc65e8154d4f409f5d4a33bf7ccf61c6f7c6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92518582"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-oc-tanner---appreciatehub"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met O.C. Tanner - AppreciateHub

In deze zelfstudie leert u hoe u O.C. Tanner - AppreciateHub integreert met Azure Active Directory (Azure AD). Wanneer u O.C. Tanner - AppreciateHub integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot O.C. Tanner - AppreciateHub.
* Instellen dat uw gebruikers automatisch worden aangemeld bij O.C. Tanner - AppreciateHub met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* O.C. Tanner - AppreciateHub-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* O.C. Tanner - AppreciateHub biedt ondersteuning voor door **IDP** geïnitieerde SSO

* Zodra u O.C. Tanner - AppreciateHub hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-oc-tanner---appreciatehub-from-the-gallery"></a>O.C. Tanner - AppreciateHub vanuit de galerie toevoegen

Om de integratie van O.C. Tanner - AppreciateHub met Azure AD te configureren, moet u O.C. Tanner - AppreciateHub toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. In de sectie **Toevoegen uit de galerie** typt u **O.C. Tanner - AppreciateHub** in het zoekvak.
1. Selecteer **O.C. Tanner - AppreciateHub** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-oc-tanner---appreciatehub"></a>Eenmalige aanmelding van Azure AD configureren en testen voor O.C. Tanner - AppreciateHub

Eenmalige aanmelding van Azure AD met O.C. Tanner - AppreciateHub configureren en testen met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in O.C. Tanner - AppreciateHub.

Als u eenmalige aanmelding van Azure AD wilt configureren en testen met O.C. Tanner, voert u de volgende stappen uit:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding van O.C. Tanner - AppreciateHub configureren](#configure-oc-tanner---appreciatehub-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    * **[Een testgebruiker voor O.C. Tanner - AppreciateHub maken](#create-oc-tanner---appreciatehub-test-user)** : als u een tegenhanger van B.Simon wilt hebben in O.C. Tanner - AppreciateHub die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. In [Azure Portal](https://portal.azure.com/) zoekt u op de pagina van toepassingsintegratie van **O.C. Tanner - AppreciateHub** de sectie **Beheren** en selecteert u **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **SAML-basisconfiguratie** hoeft de gebruiker geen enkele stap uit te voeren omdat de app al vooraf is geïntegreerd met Azure.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in de sectie **O.C. Tanner - AppreciateHub** de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot O.C. Tanner - AppreciateHub.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst met toepassingen de optie **O.C. Tanner - AppreciateHub**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-oc-tanner---appreciatehub-sso"></a>Eenmalige aanmelding van O.C. Tanner - AppreciateHub configureren

Als u eenmalige aanmelding aan de zijde van **O.C. Tanner - AppreciateHub** wilt configureren, moet u het gedownloade **XML-bestand met federatieve metagegevens** en de correcte uit de Microsoft Azure Portal gekopieerde URL's verzenden naar het [ondersteuningsteam van O.C. Tanner - AppreciateHub](mailto:sso@octanner.com). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-oc-tanner---appreciatehub-test-user"></a>Een O.C. Tanner - AppreciateHub-testgebruiker maken

Het doel van deze sectie is om een gebruiker met de naam Britta Simon te maken in O.C. Tanner - AppreciateHub.

**Voer de volgende stappen uit om een gebruiker met de naam Britta Simon te maken in O.C. Tanner - AppreciateHub:**

Vraag het [ondersteuningsteam van O.C. Tanner - AppreciateHub](mailto:sso@octanner.com) om een gebruiker te maken waarvan het nameID-kenmerk dezelfde waarde heeft als de gebruikersnaam van Britta Simon in Azure AD.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel O.C. Tanner - AppreciateHub in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van O.C. Tanner - AppreciateHub waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [O.C. Tanner - AppreciateHub proberen met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Het beveiligen van O.C. Tanner - AppreciateHub met geavanceerde zichtbaarheid en besturingselementen](/cloud-app-security/proxy-intro-aad)