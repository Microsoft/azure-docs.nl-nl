---
title: Meer informatie over op SAML gebaseerde eenmalige aanmelding (SSO) voor apps in Azure Active Directory
description: Meer informatie over op SAML gebaseerde eenmalige aanmelding (SSO) voor apps in Azure Active Directory
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/28/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: f6422c3f751a7aaa430b93ee68ca1a3520ac915f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99257689"
---
# <a name="understand-saml-based-single-sign-on"></a>Informatie over eenmalige aanmelding op basis van SAML

In de [Quick](view-applications-portal.md) start-serie op toepassings beheer hebt u geleerd hoe u Azure AD als id-provider (IDP) gebruikt voor een toepassing. In dit artikel vindt u meer informatie over de op SAML gebaseerde optie voor eenmalige aanmelding. 


## <a name="before-you-begin"></a>Voordat u begint

Het gebruik van Azure AD als uw ID-provider (IdP) en het configureren van eenmalige aanmelding (SSO) kan eenvoudig of complex zijn, afhankelijk van de toepassing die wordt gebruikt. Sommige toepassingen kunnen met slechts een paar acties worden geconfigureerd. Andere gebruikers moeten een gedetailleerde configuratie hebben. Volg de Quick Start- [serie](view-applications-portal.md) op toepassings beheer om kennis snel te vinden. Als de toepassing die u wilt toevoegen, eenvoudig is, hoeft u dit artikel waarschijnlijk niet te lezen. Als voor de toepassing die u wilt toevoegen aangepaste configuratie voor op SAML gebaseerde SSO vereist is, is dit artikel voor u.

In de [Quick Start serie](add-application-portal-setup-sso.md)is er een artikel over het configureren van eenmalige aanmelding. Hierin leert u hoe u toegang kunt krijgen tot de configuratie pagina van SAML voor een app. De configuratie pagina van SAML bevat vijf secties. Deze secties worden gedetailleerd beschreven in dit artikel.

> [!IMPORTANT] 
> Er zijn enkele scenario's waarbij de optie voor **eenmalige aanmelding** niet aanwezig is in de navigatie voor een toepassing in **bedrijfs toepassingen**. 
>
> Als de toepassing is geregistreerd met behulp van **app-registraties** , is de mogelijkheid voor eenmalige aanmelding standaard geconfigureerd voor het gebruik van OIDC OAuth. In dit geval wordt de optie **voor eenmalige aanmelding** niet weer gegeven in de navigatie onder **bedrijfs toepassingen**. Wanneer u **app-registraties** gebruikt om uw aangepaste app toe te voegen, configureert u de opties in het manifest bestand. Zie [Azure Active Directory app-manifest](../develop/reference-app-manifest.md)voor meer informatie over het manifest bestand. Zie [verificatie en autorisatie met behulp van micro soft Identity platform](../develop/authentication-vs-authorization.md#authentication-and-authorization-using-the-microsoft-identity-platform)voor meer informatie over SSO-standaarden. 
>
> Andere scenario's waarin **eenmalige aanmelding** ontbreekt in de navigatie, zijn, wanneer een toepassing wordt gehost in een andere Tenant of als uw account niet over de vereiste machtigingen (globale beheerder, Cloud toepassings beheerder, toepassings beheerder of eigenaar van de service-principal) beschikt. Machtigingen kunnen ook leiden tot een scenario waarin u **eenmalige aanmelding** kunt openen, maar niet kunt opslaan. Zie (voor meer informatie over Azure AD-beheerders rollen https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .


## <a name="basic-saml-configuration"></a>Basis-SAML-configuratie

U moet de waarden van de leverancier van de toepassing ophalen. U kunt de waarden hand matig invoeren of een meta gegevensbestand uploaden om de waarde van de velden te extra heren.

> [!TIP]
> Veel apps zijn al vooraf geconfigureerd voor gebruik met Azure AD. Deze apps worden weer gegeven in de galerie met apps die u kunt bezoeken wanneer u een app toevoegt aan uw Azure AD-Tenant. In de Quick Start- [serie](add-application-portal-setup-sso.md) wordt u begeleid bij het proces. Voor de apps in de galerie vindt u gedetailleerde, stapsgewijze instructies. Als u de stappen wilt openen, klikt u op de koppeling op de configuratie pagina van SAML voor de app, zoals beschreven in de Quick Start-serie of kunt u een lijst met alle zelf studies over app-configuratie bekijken in [zelf studies voor SaaS-app-configuratie](../saas-apps/tutorial-list.md).

| Basis configuratie-instelling voor SAML | SP-geïnitieerd | idP-geïnitieerd | Description |
|:--|:--|:--|:--|
| **Id (Entiteits-id)** | Vereist voor sommige apps | Vereist voor sommige apps | Unieke identificatie van de toepassing. Azure Active Directory stuurt de id naar de toepassing als parameter van de doelgroep van het SAML-token. Er wordt verwacht dat de toepassing deze valideert. Deze waarde wordt ook weergegeven als de entiteit-ID in SAML-metagegevens die worden geleverd door de toepassing. Voer een URL in die gebruikmaakt van het volgende patroon: ' https:// <subdomain> . contoso.com ' *u kunt deze waarde vinden als het element van de **Uitgever** in de **AuthnRequest** (SAML-aanvraag) die door de toepassing is verzonden.* |
| **Antwoord-URL** | Vereist | Vereist | Hiermee geeft u op waar de toepassing verwacht het SAML-token te ontvangen. De antwoord-URL wordt ook de ACS-URL (Assertion Consumer Service) genoemd. U kunt de extra antwoord-URL velden gebruiken om meerdere antwoord-Url's op te geven. Stel dat u aanvullende antwoord-Url's nodig hebt voor meerdere subdomeinen. Voor test doeleinden kunt u meerdere antwoord-Url's (lokale host en open bare Url's) tegelijk opgeven. |
| **Aanmeldings-URL** | Vereist | Niet opgeven | Wanneer een gebruiker deze URL opent, leidt de serviceprovider hem om naar Azure Active Directory om de gebruiker te verifiëren en aan te melden. Azure AD gebruikt de URL om de toepassing te starten vanuit Microsoft 365 of Azure AD mijn apps. Als deze leeg is, wordt door Azure AD een door IdP geïnitieerde aanmelding uitgevoerd wanneer een gebruiker de toepassing start vanaf Microsoft 365, Azure AD mijn apps of de Azure AD SSO-URL.|
| **Relaystatus** | Optioneel | Optioneel | Hiermee geeft u aan de toepassing op waar de gebruiker naar moet worden omgeleid nadat de verificatie is voltooid. De waarde is doorgaans een geldige URL voor de toepassing. Sommige toepassingen gebruiken dit veld echter anders. Vraag de leverancier van de toepassing voor meer informatie.
| **Afmeldings-URL** | Optioneel | Optioneel | Wordt gebruikt om de antwoorden op SAML-afmeldingen terug te sturen naar de toepassing.

## <a name="user-attributes-and-claims"></a>Gebruikers kenmerken en claims 

Wanneer een gebruiker zich verifieert bij de toepassing, geeft Azure AD aan de toepassing een SAML-token door met informatie (of claims) over de gebruiker die deze uniek identificeert. Standaard bevat deze informatie de gebruikers naam, het e-mail adres, de voor naam en de achternaam van de gebruiker. Mogelijk moet u deze claims aanpassen als de toepassing bijvoorbeeld specifieke claim waarden of een andere **naam** indeling dan username vereist. 

> [!IMPORTANT]
> Veel apps zijn al vooraf geconfigureerd en in de app-galerie. u hoeft zich geen zorgen te maken over het instellen van gebruikers-en groeps claims. De [Quick](add-application-portal.md) start-serie helpt u bij het toevoegen en configureren van apps.


De unieke id-waarde van de **gebruikers-id** is een vereiste claim en is belang rijk. De standaard waarde is *User. userPrincipalName*. De gebruikers-id is uniek voor elke gebruiker in de toepassing. Als het e-mailadres bijvoorbeeld zowel de gebruikersnaam als de unieke id is, wordt de waarde ingesteld op *user.mail*.

Zie [How to: claims aanpassen die zijn uitgegeven in het SAML-token voor zakelijke toepassingen voor](../develop/active-directory-saml-claims-customization.md)meer informatie over het aanpassen van SAML-claims.

U kunt nieuwe claims toevoegen, voor meer informatie Raadpleeg [het toevoegen van toepassingsspecifieke claims](../develop/active-directory-saml-claims-customization.md#adding-application-specific-claims) of het toevoegen van groepclaims, Zie [groeps claims configureren](../hybrid/how-to-connect-fed-group-claims.md).


> [!NOTE]
> Raadpleeg de volgende bronnen voor meer manieren om het SAML-token van Azure AD aan uw toepassing aan te passen.
>- Zie [Configure Role claims](../develop/active-directory-enterprise-app-role-management.md)voor het maken van aangepaste rollen via de Azure Portal.
>- Zie [claims aanpassen-Power shell](../develop/active-directory-claims-mapping.md)voor informatie over het aanpassen van claims via Power shell.
>- Zie [optionele claims configureren](../develop/active-directory-optional-claims.md)om het toepassings manifest te wijzigen voor het configureren van optionele claims voor uw toepassing.
>- Zie [token levensduur configureren](../develop/active-directory-configurable-token-lifetimes.md)voor meer informatie over het instellen van de token levensduur beleid voor vernieuwings tokens, toegangs tokens, sessie tokens en id-tokens. Als u verificatie sessies via voorwaardelijke toegang van Azure AD wilt beperken, raadpleegt u de [beheer mogelijkheden voor verificatie sessies](../conditional-access/howto-conditional-access-session-lifetime.md).

## <a name="saml-signing-certificate"></a>SAML-handtekening certificaat

Azure AD maakt gebruik van een certificaat voor het ondertekenen van de SAML-tokens die worden verzonden naar de toepassing. U hebt dit certificaat nodig om de vertrouwens relatie tussen Azure AD en de toepassing te configureren. Zie de SAML-documentatie van de toepassing voor meer informatie over de indeling van het certificaat. Zie voor meer informatie [certificaten beheren voor federatieve eenmalige aanmelding](manage-certificates-for-federated-single-sign-on.md) en [Geavanceerde opties voor certificaat ondertekening in het SAML-token](certificate-signing-options.md).

> [!IMPORTANT]
> Veel apps zijn al vooraf geconfigureerd en in de app-galerie en u hoeft geen certificaten aan te gaan. De [Quick](add-application-portal.md) start-serie helpt u bij het toevoegen en configureren van apps.

Vanuit Azure AD kunt u het actieve certificaat in base64-of RAW-indeling rechtstreeks downloaden vanuit de hoofd **set Single Sign-On met de SAML** -pagina. U kunt ook het actieve certificaat ophalen door het XML-bestand met meta gegevens van de toepassing te downloaden of door gebruik te maken van de URL voor de federatieve meta gegevens van de app. Als u uw certificaten (actief of inactief) wilt weer geven, maken of downloaden, voert u de volgende stappen uit.

Enkele veelvoorkomende zaken die u moet controleren om te controleren of een certificaat is inbegrepen: 
   - *De juiste verval datum.* U kunt de verval datum Maxi maal drie jaar in de toekomst configureren.
   - *De status actief voor het juiste certificaat.* Als de status niet **actief** is, wijzigt u de status in **actief**. Als u de status wilt wijzigen, klikt u met de rechter muisknop op de rij van het certificaat en selecteert **u certificaat actief maken**.
   - *De juiste handtekening optie en algoritme.*
   - *Het juiste e-mail adres (sen) van de melding.* Wanneer het actieve certificaat bijna de verloop datum heeft, stuurt Azure AD een melding naar het e-mail adres dat in dit veld is geconfigureerd.

Soms moet u het certificaat misschien downloaden. Wees voorzichtig wanneer u deze opslaat. Als u het certificaat wilt downloaden, selecteert u een van de opties voor Base64-indeling, RAW-indeling of federatieve meta gegevens-XML. Azure AD biedt ook de **URL voor de federatieve meta gegevens** van de app, waar u de meta gegevens die specifiek zijn voor de toepassing kunt openen in de indeling `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>` .

> [!NOTE]
> De toepassing moet kunnen omgaan met het verwerken van byte volgorde markeringen die aanwezig zijn in de XML die wordt weer gegeven wanneer u gebruikt https://login.microsoftonline.com/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={app-id} . Byte Order Mark wordt weer gegeven als een niet-afdrukbaar ASCII-teken (¿) en in hexadecimale notatie wordt weer gegeven als EF BB BF bij het controleren van de XML-gegevens.

Selecteer de knop bewerken om certificaat wijzigingen door te voeren. Er zijn verschillende dingen die u kunt doen op de pagina **SAML-handtekening certificaat** :
   - Een nieuw certificaat maken: Selecteer **Nieuw certificaat**, selecteer de **verval datum** en selecteer vervolgens **Opslaan**. Als u het certificaat wilt activeren, selecteert u het context menu (**...**) en selecteert **u certificaat actief maken**.
   - Een certificaat uploaden met persoonlijke sleutel en pfx-referenties: Selecteer **certificaat importeren** en blader naar het certificaat. Voer het **PFX-wacht woord** in en selecteer vervolgens **toevoegen**.  
   - Geavanceerde certificaat ondertekening configureren. Zie [Geavanceerde opties voor certificaat ondertekening](certificate-signing-options.md)voor meer informatie over deze opties.
   - Informeer extra personen wanneer het actieve certificaat bijna de verloop datum heeft: Voer de e-mail adressen in de velden **e-mail** adres voor meldingen in.

## <a name="set-up-the-application-to-use-azure-ad"></a>De toepassing instellen voor gebruik van Azure AD

In de sectie **instellen \<applicationName>** worden de waarden vermeld die moeten worden geconfigureerd in de toepassing, zodat Azure AD wordt gebruikt als een SAML-ID-provider. U stelt de waarden in op de configuratie pagina op de website toepassingen. Als u bijvoorbeeld GitHub configureert, gaat u naar de github.com-site en stelt u de waarden in. Als de toepassing al vooraf is geconfigureerd en in de Azure AD-galerie, vindt u een koppeling waarmee u **Stapsgewijze instructies kunt bekijken**. Als dat niet het geval is, moet u de documentatie vinden voor de toepassing die u configureert. 

De waarden van de **aanmeldings-URL** en de **afmeldings-URL** worden beide omgezet naar hetzelfde eind punt. Dit is het SAML-eind punt voor aanvraag verwerking voor de Azure AD-Tenant. 

De **Azure ad-id** is de waarde van de **VERLENER** in het SAML-token dat is uitgegeven aan de toepassing.

## <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

Nadat u uw toepassing hebt geconfigureerd voor het gebruik van Azure AD als id-provider op basis van SAML, kunt u de instellingen testen om te zien of eenmalige aanmelding werkt voor uw account. 

Selecteer **testen** en kies vervolgens om te testen met de gebruiker die momenteel is aangemeld of als iemand anders. 

Als de aanmelding is geslaagd, kunt u gebruikers en groepen toewijzen aan uw SAML-toepassing. Gefeliciteerd

Als er een fout bericht wordt weer gegeven, voert u de volgende stappen uit:

1. Kopieer en plak de gegevens in het vak **Hoe ziet de fout eruit?**.

    ![Hulp krijgen bij het oplossen van problemen](media/configure-single-sign-on-non-gallery-applications/error-guidance.png)

2. Selecteer **ondersteuning** voor het oplossen van oplossingen. De richtlijnen voor de oorzaak en de oplossing van het probleem worden weergegeven.  In dit voorbeeld was de gebruiker niet toegewezen aan de toepassing.

3. Lees de richt lijnen voor oplossingen en probeer het probleem op te lossen.

4. Voer de test opnieuw uit totdat de bewerking is voltooid.

Zie voor meer informatie [debug op SAML gebaseerde eenmalige aanmelding bij toepassingen in azure Active Directory](./debug-saml-sso-issues.md).


## <a name="next-steps"></a>Volgende stappen

- [Quickstartreeks over toepassingsbeheer](view-applications-portal.md)
- [Gebruikers of groepen toewijzen aan de toepassing](./assign-user-or-group-access-portal.md)
- [Automatische inrichting van gebruikersaccounts configureren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
- [SAML-protocol voor eenmalige aanmelding](../develop/single-sign-on-saml-protocol.md)
