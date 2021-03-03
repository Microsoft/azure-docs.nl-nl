---
title: 'Zelf studie: Azure Active Directory eenmalige aanmelding (SSO) integreren met Splan-bezoeker | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Splan Visitor.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/14/2020
ms.author: jeedes
ms.openlocfilehash: 20f49c174dde90bc7f1a9b34f3dea3132e9b177e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101644677"
---
# <a name="tutorial-integrate-azure-active-directory-single-sign-on-sso-with-splan-visitor"></a>Zelf studie: Azure Active Directory eenmalige aanmelding (SSO) integreren met Splan-bezoeker

In deze zelfstudie leert u hoe u Splan Visitor integreert met Azure Active Directory (Azure AD). Wanneer u Splan Visitor integreert met Azure AD, kunt u het volgende doen:

* Gebruik Azure AD om te bepalen wie toegang heeft tot de Splan-bezoeker.
* Gebruikers in staat stellen om automatisch te worden aangemeld bij Splan bezoekers met hun Azure AD-accounts.
* Uw accounts op een centrale locatie beheren, namelijk de Azure-portal.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op eenmalige aanmelding (SSO) voor Splan-bezoekers.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

Splan-bezoeker ondersteunt eenmalige aanmelding met IdP.

## <a name="add-splan-visitor-from-the-gallery"></a>Splan-bezoeker toevoegen vanuit de galerie

Als u de integratie van Splan-bezoeker wilt configureren in azure AD, voegt u Splan bezoekers vanuit de galerie toe aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de Azure Portal met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer in het linkerdeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Voer in het gedeelte **toevoegen vanuit de galerie** de **Splan-bezoeker** in het zoekvak in.
1. Selecteer **Splan-bezoeker** in het deel venster resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-splan-visitor"></a>Eenmalige aanmelding van Azure AD voor Splan Visitor configureren en testen

Azure AD SSO met Splan-bezoeker configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Splan Visitor.

Voer de volgende stappen uit om SSO van Azure AD bij Splan Visitor te configureren en testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Maak een Azure AD-test gebruiker om de](#create-an-azure-ad-test-user)** eenmalige aanmelding van Azure ad te testen met test gebruiker B. Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Configureer de SSO van Splan-bezoekers](#configure-splan-visitor-sso)** om de instellingen voor eenmalige aanmelding met Splan-bezoeker te configureren.
    1. **[Maak een Splan bezoeker](#create-a-splan-visitor-test-user)** van de gebruiker een equivalent van B. Simon in Splan-bezoeker dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in de Azure-portal:

1. Ga in Azure Portal naar de integratiepagina van de toepassing **Splan Visitor**, ga naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **eén Sign-On met SAML instellen** selecteert u het pictogram **bewerken/pen** voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Scherm opname van het pictogram bewerken/pen voor eenvoudige SAML-configuratie.](common/edit-urls.png)

1. In de sectie **basis configuratie van SAML** is de toepassing vooraf geconfigureerd en worden de benodigde url's vooraf ingevuld met Azure. Selecteer de knop **Opslaan** om de configuratie op te slaan.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML**. Selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![Scherm opname van het markeren van de XML-Download koppeling voor de federatieve meta gegevens.](common/metadataxml.png)

1. Op de sectie **Splan-bezoeker instellen** kopieert u de juiste URL of url's op basis van uw vereiste.

    ![Scherm opname van de sectie configuratie-Url's.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in de Azure-portal.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers** en selecteer vervolgens **alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Typ **B.Simon** in het veld **Naam**.  
   1. Voer in het veld **gebruikers naam** uw gebruikers naam in de _username@companydomain.extension_ indeling in. Voer bijvoorbeeld in **B.Simon@contoso.com** .
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Splan Visitor.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Splan bezoeker** om het overzicht van de app te openen.
1. Zoek de sectie **beheren** en selecteer vervolgens **gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst **gebruikers** en klik vervolgens op **selecteren** onder aan het scherm.
1. Als aan de gebruiker een rol wordt toegewezen, selecteert u deze in de vervolg keuzelijst **een rol selecteren** . Als er geen rol is ingesteld voor deze app, verlaat u de **standaard Access** -rol geselecteerd.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

## <a name="configure-splan-visitor-sso"></a>Eenmalige aanmelding bij Splan Visitor configureren

Als u eenmalige aanmelding met Splan-bezoeker wilt configureren, verzendt u de **federatieve meta gegevens-XML** die u hebt gedownload en de juiste gekopieerde url's van de Azure Portal naar het [ondersteunings team](mailto:support@splan.com)van de Splan-bezoeker. Dit zorgt ervoor dat de SAML SSO-verbinding aan beide zijden correct is ingesteld.

### <a name="create-a-splan-visitor-test-user"></a>Een test gebruiker voor een Splan-bezoeker maken

Maak een test gebruiker met de naam **Julia Simon** in Splan-bezoeker. Werk samen met het [ondersteunings team van Splan](mailto:support@splan.com) om de gebruiker toe te voegen aan de Splan-bezoeker. U moet de gebruiker maken en activeren voordat u eenmalige aanmelding gebruikt.

## <a name="test-sso"></a>Eenmalige aanmelding testen

Test uw Azure AD-configuratie voor eenmalige aanmelding met een van de volgende opties:

* **Azure Portal**: Selecteer **deze toepassing testen** om automatisch aan te melden bij de Splan-bezoeker waarvoor u SSO hebt ingesteld.
* **Micro soft my apps-Portal**: Selecteer de tegel **Splan** om automatisch aan te melden bij de Splan-bezoeker waarvoor u SSO hebt ingesteld. Zie [Aanmelden bij en het starten van apps vanuit de Mijn apps-portal](../user-help/my-apps-portal-end-user-access.md) voor meer informatie over de portal Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Nadat u Splan-bezoeker hebt geconfigureerd, kunt u [leren hoe u sessie besturings elementen afdwingt in Microsoft Cloud app Security](/cloud-app-security/proxy-deployment-any-app). Met sessie besturings elementen kunt u exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time beveiligen. Sessiebeheer is een uitbreiding van voorwaardelijke toegang.