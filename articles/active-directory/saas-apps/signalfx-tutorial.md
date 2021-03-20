---
title: 'Zelfstudie: Eenmalige aanmelding via Azure Active Directory integreren met SignalFx | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en SignalFx.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/24/2020
ms.author: jeedes
ms.openlocfilehash: 1fbc42864761360d252ed62cea1aef6f2937b599
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92516066"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-signalfx"></a>Zelfstudie: Eenmalige aanmelding via Azure Active Directory integreren met SignalFx

In deze zelfstudie leert u hoe u SignalFx kunt integreren met Azure AD (Azure Active Directory). Wanneer u SignalFx integreert met Azure AD, kunt u:

* Via Azure AD bepalen wie toegang heeft tot SignalFx;
* Ervoor zorgen dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij SignalFx; en
* Uw accounts op een centrale locatie beheren (via Azure Portal).

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-toepassingen met Azure AD.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, hebt u het volgende nodig:

* Een Azure AD-abonnement
    * Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een SignalFx-abonnement waarvoor eenmalige aanmelding (SSO) is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u eenmalige aanmelding van Azure AD in een testomgeving.

* SignalFx biedt ondersteuning voor met **IDP** geïnitieerde eenmalige aanmelding
* SignalFx ondersteunt het **Just-In-Time** inrichten van gebruikers
* Zodra u SignalFx hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="step-1-add-the-signalfx-application-in-azure"></a>Stap 1: De SignalFx-toepassing toevoegen in Azure

Gebruik deze instructies om de SignalFx-toepassing toe te voegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer **Azure Active Directory** in het navigatiepaneel aan de linkerkant.
1. Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing**.
1. Ga in de sectie **Toevoegen uit de galerie** naar het zoekvak, voer **SignalFx** in en selecteer dit.
     * Mogelijk moet u enkele minuten wachten totdat de toepassing is toegevoegd aan uw tenant.
1. Laat Azure Portal open en open vervolgens een nieuw tabblad.    

## <a name="step-2-begin-signalfx-sso-configuration"></a>Stap 2: De configuratie van eenmalige aanmelding bij SignalFx starten

Gebruik deze instructies om het configuratieproces voor eenmalige aanmelding bij SignalFx te starten.

1. Ga naar het nieuwe tabblad en meld u aan bij de SignalFx-gebruikersinterface. 
1. Klik in het bovenste menu op **Integraties**. 
1. Voer in het zoekveld **Azure Active Directory** en selecteer deze optie.
1. Klik op **Nieuwe integratie maken**.
1. Voer bij **Naam** een gemakkelijk herkenbare naam in die uw gebruikers begrijpen.
1. Markeer **Weergeven op de aanmeldingspagina**.
    * Met deze functie wordt een aangepaste knop op de aanmeldingspagina weergegeven waarop uw gebruikers kunnen klikken. 
    * De gegevens die u hebt ingevoerd bij **Naam**, worden weergegeven op de knop. Voer daarom een **Naam** in die door uw gebruikers wordt herkend. 
    * Deze optie werkt alleen als u een aangepast subdomein gebruikt voor de SignalFx-toepassing, zoals **uwbedrijfsnaam.signalfx.com**. Neem contact op met de SignalFx-ondersteuning om een aangepast subdomein te verkrijgen. 
1. Kopieer de **Integratie-id**. U hebt deze informatie in een latere stap nodig. 
1. Laat de SignalFx-gebruikersinterface open. 

## <a name="step-3-configure-azure-ad-sso"></a>Stap 3: Eenmalige aanmelding van Azure AD configureren

Volg deze instructies om eenmalige aanmelding via Azure AD in te schakelen in Azure Portal.

1. Ga terug naar de [Azure Portal](https://portal.azure.com/) en zoek op de integratiepagina van de **SignalFx**-toepassing naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het potloodpictogram (bewerken) voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Op de pagina **Eenmalige aanmelding instellen met SAML** vult u de volgende velden in: 

    a. Voer bij **Id** de volgende URL in `https://api.<realm>.signalfx.com/v1/saml/metadata` en vervang `<realm>` door uw SignalFx-realm. 

    b. Voer bij **Antwoord-URL** de volgende URL in `https://api.<realm>.signalfx.com/v1/saml/acs/<integration ID>` en vervang `<realm>` door uw SignalFx-realm, evenals `<integration ID>` door de **Integratie-id** die u eerder hebt gekopieerd uit de SignalFx-gebruikersinterface.

1. In de SignalFx-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. 
    
1. Controleer of de volgende claims worden toegewezen aan de bronkenmerken die zijn ingevuld in de Active Directory. 

    | Naam |  Bronkenmerk|
    | ------------------- | -------------------- |
    | User.FirstName  | user.givenname |
    | User.email  | user.mail |
    | PersonImmutableID       | user.userprincipalname    |
    | User.LastName       | user.surname    |

    > [!NOTE]
    > Dit proces vereist dat uw Active Directory is geconfigureerd met ten minste één geverifieerd aangepast domein en toegang heeft tot de e-mail accounts in dit domein. Als u niet zeker weet of hulp nodig hebt bij deze configuratie, neemt u contact op met de ondersteuning van SignalFx.  

1. Ga naar de pagina **Eenmalige aanmelding met SAML instellen** en zoek in de sectie **SAML-handtekeningcertificaat** de optie **Certificaat (Base64)** . Selecteer vervolgens **Downloaden**. Download het certificaat en sla het op uw computer op. Kopieer vervolgens de waarde van de **Metagegevens-URL van App Federation**. U hebt deze informatie later nodig in de SignalFx-gebruikersinterface. 

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Ga naar het gedeelte **SignalFx instellen** en kopieer de waarde van **Azure AD-id**. U hebt deze informatie in een latere stap nodig in de SignalFx-gebruikersinterface. 

## <a name="step-4-create-an-azure-ad-test-user"></a>Stap 4: Een Azure AD-testgebruiker maken

Gebruik deze instructies om een testgebruiker te maken met de naam **B.Simon** in Azure Portal.

1. Selecteer in het linkernavigatievenster in Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan de pagina.
1. Ga naar de eigenschappen van de **Gebruiker**:
   1. Voer bij **Gebruikersnaam** `username@companydomain.extension` in, zoals `b.simon@contoso.com`.
   1. Voer bij **Naam** `B.Simon` in.
   1. Markeer **Wachtwoord weergeven** en kopieer de weergegeven waarde in **Wachtwoord**. U hebt deze informatie nodig in de volgende stap om deze integratie te testen. 
   1. Klik op **Create**.

## <a name="step-5-assign-the-azure-ad-test-user"></a>Stap 5: De Azure AD-testgebruiker toewijzen

Gebruik deze instructies om de testgebruiker in staat te stellen om eenmalige aanmelding via Azure te gebruiken voor SignalFx.

1. Selecteer in de Azure-portal **Bedrijfstoepassingen** en selecteer **Alle toepassingen**.
1. Selecteer in de lijst met toepassingen de optie **SignalFx**.
1. Ga op de overzichtspagina van de app naar de sectie **Beheren**, en selecteer vervolgens **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Ga in het dialoogvenster **Gebruikers en groepen** naar de lijst **Gebruikers**, selecteer **B. Simon** en klik onder aan de pagina op **Selecteren**.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan de pagina.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op **Toewijzen**.

## <a name="step-6-complete-the-signalfx-sso-configuration"></a>Stap 6: De configuratie van eenmalige aanmelding bij SignalFx voltooien 

1. Open het vorige tabblad en ga terug naar de SignalFx-gebruikersinterface om de huidige Azure Active Directory-integratiepagina weer te geven. 
1. Klik naast **Certificaat (Base64)** op **Bestand uploaden** en zoek vervolgens het bestand **Base64-gecodeerd certificaat** dat u eerder hebt gedownload uit Azure Portal.
1. Plak naast de **Azure AD-id** de **Azure AD-id** die u eerder in Azure Portal hebt gekopieerd. 
1. Plak in het tekstvak **URL van metagegevens** de waarde van **Metagegevens-URL van App Federation** die u uit Azure Portal hebt gekopieerd. 
1. Klik op **Opslaan**.

## <a name="step-7-test-sso"></a>Stap 7: Eenmalige aanmelding testen

Bekijk de volgende informatie over het testen van eenmalige aanmelding, evenals verwachtingen voor de eerste keer aanmelden bij SignalFx. 

### <a name="test-logins"></a>Aanmeldingen testen

* Als u de aanmelding wilt testen, gebruikt u een privé-/incognitovenster of meld u zich af bij Azure Portal. Als u dit niet doet, wordt de aanmelding met de testgebruiker belemmerd door de cookies voor de gebruiker die de toepassing heeft geconfigureerd.

* Wanneer een nieuwe testgebruiker zich voor de eerste keer aanmeldt, wordt een wachtwoordwijziging geforceerd door Azure. Als dit gebeurt, wordt het aanmeldingsproces met eenmalige aanmelding niet voltooid. De testgebruiker wordt omgeleid naar Azure Portal. Om dit te verhelpen, moet de testgebruiker het wachtwoord wijzigen, naar de aanmeldingspagina van SignalFx of het toegangsvenster gaan en het opnieuw proberen.
    * Wanneer u op de tegel SignalFx in het toegangsvenster klikt, wordt u als het goed is automatisch aangemeld bij SignalFx. 
        * Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

* De SignalFx-toepassing kan worden geopend vanuit het toegangsvenster of via een aangepaste aanmeldingspagina die aan de organisatie is toegewezen. De testgebruiker moet de integratie testen vanaf een van deze locaties.
    * De testgebruiker kan de referenties gebruiken die eerder in dit proces zijn gemaakt voor **b.simon\@contoso.com**.

### <a name="first-time-logins"></a>De eerste keer aanmelden

* Wanneer een gebruiker zich voor de eerste keer aanmeldt bij SignalFx via eenmalige aanmelding van SAML, ontvangt de gebruiker een e-mail van SignalFx met een link. De gebruiker moet op de link klikken voor verificatiedoeleinden. Deze e-mailvalidatie vindt alleen plaats tijdens de eerste keer dat gebruikers zich aanmelden. 

* SignalFx ondersteunt het **Just-in-time** inrichten van gebruikers, wat betekent dat als een gebruiker niet aanwezig is in SignalFx, het account van de gebruiker wordt gemaakt bij de eerste aanmeldingspoging.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [SignalFx proberen met Azure AD](https://aad.portal.azure.com/)