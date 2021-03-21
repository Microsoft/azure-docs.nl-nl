---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Netvision Compas | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Netvision Compas.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/16/2020
ms.author: jeedes
ms.openlocfilehash: 423ac882c81582f2843eeba37d11c660662ad6e2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92519398"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netvision-compas"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Netvision Compas

In deze zelfstudie leert u hoe u Netvision Compas kunt integreren met Azure Active Directory (Azure AD). Wanneer u Netvision Compas integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot Netvision Compas.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Netvision Compas.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Netvision Compas waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Netvision Compas ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding
* Zodra u Netvision Compas hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)


## <a name="adding-netvision-compas-from-the-gallery"></a>Netvision Compas toevoegen uit de galerie

Om de integratie van Netvision Compas te configureren in Azure AD, moet u Netvision Compas uit de galerie toevoegen aan uw lijst van beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **Netvision Compas**.
1. Selecteer **Netvision Compas** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-netvision-compas"></a>Azure AD-eenmalige aanmelding configureren en testen voor Netvision Compas

Configureer en test eenmalige aanmelding van Azure AD met Netvision Compas met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Netvision Compas.

Als u Azure AD-eenmalige aanmelding met Netvision Compas wilt configureren en testen, moet u de volgende stappen uitvoeren:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor Netvision Compas configureren](#configure-netvision-compas-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Testgebruiker maken voor Netvision Compas](#configure-netvision-compas-test-user)** : als u een tegenhanger van B.Simon in Netvision Compas wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in [Azure Portal](https://portal.azure.com/) op de pagina voor integratie van de toepassing **Netvision Compas** naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<TENANT>.compas.cloud/Identity/Saml20`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<TENANT>.compas.cloud/Identity/Auth/AssertionConsumerService`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<TENANT>.compas.cloud/Identity/Auth/AssertionConsumerService`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [ondersteuningsteam van Netvision Compas](mailto:contact@net.vision) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het metagegevensbestand te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)



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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door haar toegang te verlenen tot Netvision Compas.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Netvision Compas** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-netvision-compas-sso"></a>Eenmalige aanmelding voor Netvision Compas configureren

In deze sectie schakelt u SAML SSO in **Netvision Compas**.
1. Meld u aan bij **Netvision Compas** met een beheerdersaccount en open het beheergebied.

    ![Beheergebied](media/netvision-compas-tutorial/admin.png)

1. Zoek het gebied **System** (Systeem) en selecteer **Identity Providers** (id-providers).

    ![Beheerders-IDP's](media/netvision-compas-tutorial/admin-idps.png)

1. Selecteer de actie **Add** (Toevoegen) om Azure AD te registreren als een nieuwe IDP.

    ![IDP toevoegen](media/netvision-compas-tutorial/idps-add.png)

1. Selecteer **SAML** als **Provider Type**.
1. Voer betekenisvolle waarden in voor de velden **Display name** (Weergavenaam) en **Description** (Beschrijving).
1. Wijs **Netvision Compas**-gebruikers toe aan de IDP door ze te selecteren in de lijst **Available users** (Beschikbare gebruikers). Selecteer vervolgens de knop **Add selected** (Geselecteerde toevoegen). Gebruikers kunnen ook aan de IDP worden toegewezen tijdens de inrichtingsprocedure.
1. Klik voor de SAML-optie **Metadata** (Metagegevens) op de knop **Choose File** (Bestand kiezen) en selecteer het metagegevensbestand dat u eerder op de computer hebt opgeslagen.
1. Klik op **Opslaan**.

    ![IDP bewerken](media/netvision-compas-tutorial/idp-edit.png)


### <a name="configure-netvision-compas-test-user"></a>Testgebruiker voor Netvision Compas configureren

In deze sectie configureert u een bestaande gebruiker in **Netvision Compas** voor eenmalige aanmelding met Azure AD.
1. Voer de stappen voor het inrichten van gebruikers in **Netvision Compas** uit, zoals gedefinieerd door uw bedrijf, of bewerk een bestaand gebruikersaccount.
1. Zorg ervoor dat bij het definiëren van het profiel van de gebruiker het **(persoonlijke) e-mailadres** van de gebruiker overeenkomt met de Azure AD-gebruikersnaam: username@companydomain.extension. Bijvoorbeeld `B.Simon@contoso.com`.

    ![Gebruiker bewerken](media/netvision-compas-tutorial/user-config.png)

Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD.

### <a name="using-the-access-panel-idp-initiated"></a>Het toegangsvenster gebruiken (geïnitieerd door IDP).

Wanneer u in het toegangsvenster op de tegel Netvision Compas klikt, wordt u automatisch aangemeld bij het exemplaar van Netvision Compas waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

### <a name="directly-accessing-netvision-compas-sp-initiated"></a>Rechtstreekse toegang tot Netvision Compas (geïnitieerd door SP).

1. Open de URL voor **Netvision Compas**. Bijvoorbeeld `https://tenant.compas.cloud`.
1. Voer de **Netvision Compas**-gebruikersnaam in en selecteer **Volgende**.

    ![Aangemelde gebruiker](media/netvision-compas-tutorial/login-user.png)

1. **(optioneel)** Als de gebruiker aan meerdere IDP's in **Netvision Compas** is toegewezen, wordt een lijst met beschikbare IDP's weergegeven. Selecteer de Azure AD-IDP die u eerder hebt geconfigureerd in **Netvision Compas**.

    ![Aanmeldingsmethode kiezen](media/netvision-compas-tutorial/login-choose.png)

1. U wordt omgeleid naar Azure AD om de verificatie uit te voeren. Na de verificatie wordt u automatisch aangemeld bi het exemplaar van **Netvision Compas** waarvoor u eenmalige aanmelding hebt ingesteld.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Netvision Compas met Azure AD uitproberen](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)