---
title: 'Zelfstudie: Eenmalige aanmelding (SSO) van Azure Active Directory integreren met IamIP Patent Platform | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding configureert tussen Azure Active Directory en IamIP Patent Platform.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: jeedes
ms.openlocfilehash: dc9be41cd7975610f2779da09b94066c70a91652
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92460526"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-iamip-patent-platform"></a>Zelfstudie: Eenmalige aanmelding (SSO) van Azure Active Directory integreren met IamIP Patent Platform

In deze zelfstudie leert u hoe u IamIP Patent Platform integreert met Azure Active Directory (Azure AD). Wanneer u IamIP Patent Platform met Azure AD integreert, kunt u het volgende doen:

* Azure AD gebruiken om te bepalen wie toegang heeft tot IamIP Patent Platform.
* Ervoor zorgen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij IamIP Patent Platform.
* Uw accounts op één centrale locatie beheren: de Azure-portal.

Zie [Eenmalige aanmelding voor toepassingen in Azure Active Directory](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op IamIP Patent Platform waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="tutorial-description"></a>Beschrijving van zelfstudie

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

IamIP Patent Platform ondersteunt door SP en IDP geïnitieerde eenmalige aanmelding.

Nadat u IamIP Patent Platform hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).


## <a name="add-iamip-patent-platform-from-the-gallery"></a>IamIP Patent Platform toevoegen vanuit de galerie

Om de integratie van IamIP Patent Platform te configureren in Azure AD, moet u IamIP Patent Platform vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de knop **Azure Active Directory** in het linkerdeelvenster.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. In de sectie **Toevoegen uit de galerie** typt u **IamIP Patent Platform** in het zoekvak.
1. Selecteer **IamIP Patent Platform** in het resultatenvenster en voeg de toepassing toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-iamip-patent-platform"></a>Eenmalige aanmelding van Azure AD configureren en testen met IamIP Patent Platform

U gaat eenmalige aanmelding van Azure AD met IamIP Patent Platform configureren en testen met behulp van een testgebruiker met de naam B.Simon. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in IamIP Patent Platform.

Volg deze algemene stappen om eenmalige aanmelding van Azure AD met IamIP Patent Platform te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** om eenmalige aanmelding van Azure AD te testen.
    * **[Toegang verlenen aan de testgebruiker](#grant-access-to-the-test-user)** zodat de gebruiker eenmalige aanmelding van Azure AD kan gebruiken.

1. **[Eenmalige aanmelding van IamIP Patent Platform SSO configureren](#configure-iamip-patent-platform-sso)** aan de toepassingszijde.
    * **[Een testgebruiker voor IamIP Patent Platform maken](#create-iamip-patent-platform-test-user)** als een tegenhanger voor de Azure AD-weergave van de gebruiker.

1. **[Eenmalige aanmelding testen](#test-sso)** om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in de Azure-portal:

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **IamIP Patent Platform** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken:

   ![Potloodpictogram voor Standaard-SAML-configuratie](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de volgende stappen uit als u een Service Provider-metagegevensbestand hebt en eenmalige aanmelding in de door IDP geïnitieerde modus wilt configureren:

    a. Selecteer **Metagegevensbestand uploaden**:

    ![Metagegevensbestand uploaden](common/upload-metadata.png)

    b. Selecteer de knop Map, selecteer het metagegevensbestand en selecteer vervolgens **Uploaden**:

    ![De knoppen Map en Uploaden](common/browse-upload-metadata.png)

    c. Nadat het bestand met metagegevens is geüpload, worden de waarden voor **id** en **antwoord-URL** automatisch ingevuld in de sectie **Standaard SAML-configuratie**:

    ![Waarden voor id en antwoord-URL](common/idp-intiated.png)

    > [!Note]
    > Als de waarden voor **id** en **antwoord-URL** niet automatisch worden ingevuld, kunt u de waarden zelf invullen afhankelijk van uw behoeften.

1. Selecteer **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door SP geïnitieerde modus wilt configureren:

    Voer in het vak **Aanmeldings-URL** het volgende in: **https:\//patents.iamip.com/login-user**.

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** naar de sectie **SAML-handtekeningcertificaat** en selecteer de koppeling **Downloaden** voor **Certificaat (Raw)** om het certificaat te downloaden en op te slaan op de computer:

    ![De koppeling om het certificaat te downloaden](common/certificateraw.png)

1. In de sectie **IamIP Patent Platform instellen** kopieert u de juiste URL('s) op basis van uw behoeften:

    ![Configuratie-URL's kopiëren](common/idp-intiated.png))

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in de Azure-portal.

1. Selecteer in de Azure-portal aan de linkerkant **Azure Active Directory**. Selecteer **Gebruikers** en daarna **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Ga als volgt te werk in het venster dat verschijnt:
   1. Voer in het vak **Naam** de naam **B.Simon** in.  
   1. Voer in het vak **Gebruikersnaam** \<username>@\<companydomain> in.\<extension>. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Selecteer **Maken**.

### <a name="grant-access-to-the-test-user"></a>De testgebruiker toegang geven

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door die gebruiker toegang te verlenen tot IamIP Patent Platform.

1. Selecteer in de Azure-portal **Bedrijfstoepassingen** en selecteer **Alle toepassingen**.
1. Selecteer **IamIP Patent Platform** in de lijst met toepassingen.
1. Selecteer op de overzichtspagina van de app in de sectie **Beheren** de optie **Gebruikers en groepen**:

   ![Gebruikers en groepen selecteren](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**:

    ![Gebruiker toevoegen selecteren](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** **B.Simon** in de lijst **Gebruikers** en selecteer vervolgens de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Selecteer in het dialoogvenster **Toewijzing toevoegen** de knop **Toewijzen**.

## <a name="configure-iamip-patent-platform-sso"></a>Eenmalige aanmelding voor IamIP Patent Platform configureren

Als u eenmalige aanmelding wilt configureren in IamIP Patent Platform, moet u het gedownloade Raw-certificaat en de juiste URL's die u hebt gekopieerd uit Azure Portal verzenden naar het [ondersteuningsteam van IamIP Patent Platform](mailto:info@iamip.com). Zij zorgen er dan voor dat de SAML-verbinding voor eenmalige aanmelding aan beide kanten juist is.

### <a name="create-iamip-patent-platform-test-user"></a>Testgebruiker voor IamIP Patent Platform maken

Voeg in samenwerking met het [ondersteuningsteam van IamIP Patent Platform](mailto:info@iamip.com) een gebruiker toe met de naam B. Simon in IamIP Patent Platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van het toegangsvenster.

Wanneer u de tegel IamIP Patent Platform selecteert in het toegangsvenster, zou u automatisch moeten worden aangemeld bij het exemplaar van IamIP Patent Platform waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Inleiding tot het toegangsvenster](../user-help/my-apps-portal-end-user-access.md) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Zelfstudies over het integreren van SaaS-apps met Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [IamIP Patent Platform gebruiken met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)