---
title: 'Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met ColorTokens ZTNA | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en ColorTokens ZTNA.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: jeedes
ms.openlocfilehash: c87eb3b52843f4de39d0c037bdf452160be1ca2b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735955"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-colortokens-ztna"></a>Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met ColorTokens ZTNA

In deze zelfstudie leert u hoe u ColorTokens ZTNA kunt integreren met Azure Active Directory (Azure AD). Wanneer u ColorTokens ZTNA integreert met Azure AD, kunt u het volgende doen:

* Beheren in Azure AD wie toegang heeft tot ColorTokens ZTNA.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij ColorTokens ZTNA.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* ColorTokens ZTNA-abonnement met eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* ColorTokens ZTNA ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-colortokens-ztna-from-the-gallery"></a>ColorTokens ZTNA toevoegen vanuit de galerie

Om de integratie van ColorTokens ZTNA in Azure AD te configureren, moet u ColorTokens ZTNA vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen vanuit de galerie** **ColorTokens ZTNA** in het zoekvak.
1. Selecteer **ColorTokens ZTNA** in het resultatenvenster en voeg de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-colortokens-ztna"></a>Eenmalige aanmelding van Azure AD voor ColorTokens ZTNA configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met ColorTokens ZTNA met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in ColorTokens ZTNA.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD voor ColorTokens ZTNA te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** : zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor ColorTokens ZTNA configureren](#configure-colortokens-ztna-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een ColorTokens ZTNA-testgebruiker maken](#create-colortokens-ztna-test-user)** : als u een equivalent van B.Simon in ColorTokens ZTNA wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in Azure Portal, op de integratiepagina van de toepassing **ColorTokens ZTNA**, de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<tenantname>.spectrum.colortokens.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL, id en antwoord-URL. Neem contact op met het [klantondersteuningsteam van ColorTokens ZTNA](mailto:support@colortokens.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. In de ColorTokens ZTNA-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. Bovendien verwacht de toepassing ColorTokens ZTNA nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.

    | Naam | Bronkenmerk|
    | ------------- | --------- |
    | department | user.userprincipalname |
    | Groep | user.groups |

    > [!NOTE]
    > Klik [hier](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) om te leren hoe u rollen maakt in Azure AD.

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. In het gedeelte **ColorTokens ZTNA instellen** kopieert u de juiste URL('s) op basis van uw vereisten.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot ColorTokens ZTNA.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer in de lijst met toepassingen **ColorTokens ZTNA**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.
1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u de rollen hebt ingesteld zoals hierboven beschreven, kunt u deze selecteren in de vervolgkeuzelijst **Selecteer een rol**.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-colortokens-ztna-sso"></a>Eenmalige aanmelding voor ColorTokens ZTNA configureren

Als u eenmalige aanmelding aan de **ColorTokens ZTNA**-zijde wilt configureren, moet u het gedownloade **Federation Metadata XML** en de correcte uit de Microsoft Azure Portal gekopieerde URL's verzenden naar het [ondersteuningsteam van ColorTokens ZTNA](mailto:support@colortokens.com). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-colortokens-ztna-test-user"></a>ColorTokens ZTNA-testgebruiker maken

In deze sectie gaat u een gebruiker maken met de naam Britta Simon in ColorTokens ZTNA. Neem contact op met het [ondersteuningsteam van ColorTokens ZTNA](mailto:support@colortokens.com) om de gebruikers toe te voegen in het ColorTokens ZTNA-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van de volgende opties. 

* Klik in Azure Portal op **Deze toepassing testen**. U wordt omgeleid naar de aanmeldings-URL van ColorTokens ZTNA, waar u de aanmeldingsstroom kunt initiëren. 

* Ga rechtstreeks naar de aanmeldings-URL van ColorTokens ZTNA en initieer de aanmeldingsstroom daar.

* U kunt Microsoft Mijn apps gebruiken. Wanneer u in Mijn apps op de tegel ColorTokens ZTNA klikt, wordt u omgeleid naar de aanmeldings-URL van ColorTokens ZTNA. Zie [Introduction to My Apps](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot Mijn apps) voor meer informatie over Mijn apps.


## <a name="next-steps"></a>Volgende stappen

Zodra u ColorTokens ZTNA hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
