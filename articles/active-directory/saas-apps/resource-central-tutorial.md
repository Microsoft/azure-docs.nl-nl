---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Resource Central | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Resource Central.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jeedes
ms.openlocfilehash: dbc148fcbcd9c3be86a29df1e08755611a347b07
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100586562"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-resource-central"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Resource Central

In deze zelfstudie leert u hoe u Resource Central integreert met Azure Active Directory (Azure AD). Wanneer u Resource Central integreert met Azure AD, kunt u:

* In Azure AD beheren wie er toegang heeft tot Resource Central.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Resource Central.
* Uw accounts op een centrale locatie beheren: Azure Portal.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Resource Central-abonnement met eenmalige aanmelding (SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Resource Central ondersteunt door **SP** geïnitieerde eenmalige aanmelding

* Resource Central biedt ondersteuning voor het **Just In Time** inrichten van gebruikers

## <a name="add-resource-central-from-the-gallery"></a>Resource Central toevoegen vanuit de galerie

Voor het configureren van de integratie van Resource Central met Azure AD moet u Resource Central uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de Azure-portal aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. In de sectie **toevoegen vanuit de galerie** typt u in het zoekvak **Resource Central**.
1. Selecteer **Resource Central** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso-for-resource-central"></a>Eenmalige aanmelding van Azure AD voor Resource Central configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Resource Central met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Resource Central.

Voer de volgende stappen uit om eenmalige aanmelding van Azure Active Directory bij Resource Central te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
    1. **[Een Resource Central-testgebruiker maken](#create-resource-central-test-user)** : als u een equivalent van B.Simon in Resource Central wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding voor Resource Central configureren](#configure-resource-central-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in Azure Portal op de integratiepagina van de toepassing **Resource Central** naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de **eenvoudige SAML-configuratie** de waarden in voor de volgende velden:

   1. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<DOMAIN_NAME>/ResourceCentral`

   1. Typ in het tekstvak **id (Entiteits-ID)** een URL met het volgende patroon:  `https://<DOMAIN_NAME>/ResourceCentral`

   1. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<DOMAIN_NAME>/ResourceCentral/ExAuth/Saml2Authentication/Acs`

    > [!NOTE]
    > Deze waarden zijn geen letterlijke waarden. Deze waarden bijwerken met de werkelijke aanmeldings-URL, id en antwoord-URL-waarden. Neem contact op met het [ondersteuningsteam van Resource Central](mailto:st@aod.vn) om deze waarden te verkrijgen.  U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** in het **SAML-handtekening certificaat** naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In **Resource Central instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In dit gedeelte gaat u een testgebruiker met de naam B.Simon maken in de Azure-portal.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer `username@companydomain.extension` in het veld **Gebruikersnaam** in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Resource Central.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Resource Central** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.
1. Selecteer **gebruiker toevoegen** en selecteer vervolgens **gebruikers en groepen** in het deel venster **toewijzing toevoegen** .
1. Selecteer in het deel venster **gebruikers en groepen** **B. Simon** van de lijst **gebruikers** en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een rol verwacht die aan de gebruikers moet worden toegewezen, kunt u deze selecteren in **een rol selecteren**. Als er voor deze app geen rol is ingesteld, ziet u **standaard Access** -rol geselecteerd.
1. Klik in het deel venster **toewijzing toevoegen** op de knop **toewijzen** .

### <a name="create-resource-central-test-user"></a>Resource Central-testgebruiker maken

In deze sectie wordt een gebruiker met de naam **B. Simon** gemaakt in **Resource Central**.

1. In Resource Central selecteert u **beveiligings**  >  **personen**  >  **Nieuw**.
  
    :::image type="content" source="./media/resource-central/new-person.png" alt-text="Scherm opname van het deel venster personen in Resource Central, met de knop Nieuw gemarkeerd.":::

1. Voer bij **persoons gegevens**, voor **weergave naam**, de gebruiker **B. Simon** in. Voer de Azure AD-gebruikers naam van de gebruiker in voor het **SMTP-adres**. Bijvoorbeeld `B.Simon@contoso.com`.

    :::image type="content" source="./media/resource-central/person.png" alt-text="Scherm opname van het deel venster met de persoons gegevens in Resource Central.":::

## <a name="configure-resource-central-sso"></a>Eenmalige aanmelding van Resource Central configureren

In deze sectie configureert u eenmalige aanmelding in **Resource Central systeem beheerder**.

1. Selecteer in Resource Central System Administrator **externe verificatie**.
1.  Selecteer **Ja** om de **configuratie in te scha kelen**.

    ![Scherm afbeelding met de optie configuratie inschakelen die is geselecteerd in het deel venster Externe verificatie in Resource Central.](./media/resource-central/enable.png)

1. Selecteer **SAML2** in het **verificatie protocol**. 

   :::image type="content" source="./media/resource-central/protocol.png" alt-text="Scherm opname van de SAML2 die is geselecteerd voor het verificatie protocol in Resource Central.":::

1. Voer onder **SAML2-configuratie** de waarden in voor de volgende velden:

    1. Voor **id (Entiteits-ID)**, **aanmeldings-URL**, **afmeldings-URL** en **Azure ad-id** voert u de relevante url's in:

       :::image type="content" source="./media/resource-central/auth.png" alt-text="Scherm afbeelding van het configuratie venster SAML2 in Resource Central.":::

        Kopieer de Url's uit het deel venster **Resource Central instellen** :

        :::image type="content" source="./media/resource-central/setup.png" alt-text="Scherm opname van het deel venster Resource Central instellen in Resource Central.":::

   1. Voer voor **retour-URL** in `https://<DOMAIN_NAME>/ResourceCentral/ExAuth/Saml2Authentication/CallbackHandler` .
  
1. Voor het **certificaat** uploadt u uw certificaat en voert u uw wacht woord in.

   ![Scherm afbeelding van de sectie certificaat in Resource Central.](./media/resource-central/cert.png)
   
1. Selecteer **Opslaan**.

1. Ga terug naar **Azure Portal**. Upload uw certificaat in het **SAML-handtekening certificaat** en voer uw wacht woord in.

   ![Scherm opname van het deel venster certificaat importeren in de Azure Portal.](./media/resource-central/cert2.png).

1. Selecteer **Toevoegen**.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD. Als u eenmalige aanmelding wilt testen, hebt u drie opties:

* Selecteer **Deze toepassing testen** in het Azure Portal. De koppeling wordt omgeleid naar de aanmeldings-URL van de Resource Central, waar u zich kunt aanmelden.

* Ga rechtstreeks naar de Resource Central-aanmeld-URL en start de aanmelding.

   :::image type="content" source="./media/resource-central/test.png" alt-text="Scherm afbeelding van de test webpagina voor eenmalige aanmelding in de Resource Central.":::

* Gebruik de portal mijn apps van micro soft. Selecteer in de portal mijn apps de tegel **Resource Central** om om te leiden naar de URL voor het aanmelden bij Resource Central. Zie [Aanmelden bij en het starten van apps vanuit de Mijn apps-portal](../user-help/my-apps-portal-end-user-access.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Nadat u Resource Central hebt ingesteld voor eenmalige aanmelding met Azure AD, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
