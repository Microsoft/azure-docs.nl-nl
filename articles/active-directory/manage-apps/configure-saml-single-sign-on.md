---
title: Op SAML gebaseerde eenmalige aanmelding (SSO) voor apps in Azure Active Directory
description: Op SAML gebaseerde eenmalige aanmelding (SSO) voor apps in Azure Active Directory
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/28/2020
ms.author: iangithinji
ms.reviewer: arvinh,luleon
ms.openlocfilehash: b7468f33c75dd58e70c344f3ef19c51e220a7abb
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374485"
---
# <a name="understand-saml-based-single-sign-on"></a>Op SAML gebaseerde een aanmelding begrijpen

In de [quickstartreeks over](view-applications-portal.md) toepassingsbeheer hebt u geleerd hoe u Azure AD gebruikt als id-provider (IdP) voor een toepassing. In dit artikel wordt gedetailleerder in op SAML gebaseerde optie voor een aanmelding beschreven. 


## <a name="before-you-begin"></a>Voordat u begint

Het gebruik van Azure AD als id-provider (IdP) en het configureren van eenmalige aanmelding (SSO) kan eenvoudig of complex zijn, afhankelijk van de toepassing die wordt gebruikt. Sommige toepassingen kunnen worden geconfigureerd met slechts een paar acties. Voor andere is een diepgaande configuratie vereist. Als u snel kennis wilt op doen, doorloop dan de [quickstartreeks](view-applications-portal.md) over toepassingsbeheer. Als de toepassing die u toevoegt eenvoudig is, hoeft u dit artikel waarschijnlijk niet te lezen. Als voor de toepassing die u toevoegt een aangepaste configuratie is vereist voor eenmalige aanmelding op basis van SAML, is dit artikel iets voor u.

In de [quickstart-reeks](add-application-portal-setup-sso.md)staat een artikel over het configureren van een een aanmelding. In dit artikel leert u hoe u toegang krijgt tot de PAGINA SAML-configuratie voor een app. De pagina SAML-configuratie bevat vijf secties. Deze secties worden uitgebreid besproken in dit artikel.

> [!IMPORTANT] 
> Er zijn enkele scenario's **waarbij de** optie Voor een een aanmelding niet aanwezig is in de navigatie voor een toepassing in **Bedrijfstoepassingen.** 
>
> Als de toepassing is geregistreerd met **behulp App-registraties** is de mogelijkheid voor een aanmelding standaard geconfigureerd voor het gebruik van OIDC OAuth. In dit geval wordt **de optie Voor een aanmelding** niet in de navigatie onder **Bedrijfstoepassingen weer geven.** Wanneer u een **App-registraties** om uw aangepaste app toe te voegen, configureert u opties in het manifestbestand. Zie voor meer informatie over het manifestbestand [Azure Active Directory app-manifest.](../develop/reference-app-manifest.md) Zie Verificatie en autorisatie met Microsoft Identity Platform voor meer informatie over [SSO-standaarden.](../develop/authentication-vs-authorization.md#authentication-and-authorization-using-the-microsoft-identity-platform) 
>
> Andere scenario's waarbij een een aanmelding ontbreekt in de navigatie, zijn onder andere wanneer een toepassing wordt gehost in een andere tenant of als uw account niet over de vereiste machtigingen (globale beheerder, Cloud Application Administrator, toepassingsbeheerder of eigenaar van de **service-principal)** is. Machtigingen kunnen ook een scenario veroorzaken waarin u een **een aanmelding kunt** openen, maar niet kunt opslaan. Zie voor meer informatie over Azure AD-beheerdersrollen. https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)


## <a name="basic-saml-configuration"></a>Standaard SAML-configuratie

U moet de waarden van de leverancier van de toepassing krijgen. U kunt de waarden handmatig invoeren of een metagegevensbestand uploaden om de waarde van de velden te extraheren.

> [!TIP]
> Veel apps zijn al vooraf geconfigureerd voor gebruik met Azure AD. Deze apps worden vermeld in de galerie met apps die u kunt bekijken wanneer u een app toevoegt aan uw Azure AD-tenant. De [quickstart-reeks](add-application-portal-setup-sso.md) doorloopt het proces. Voor de apps in de galerie vindt u gedetailleerde, stapsgewijs instructies. Als u toegang wilt krijgen tot de stappen, klikt u op de koppeling op de pagina SAML-configuratie voor de app, zoals beschreven in de quickstart-reeks, of bladert u door een lijst met alle zelfstudies voor app-configuratie in zelfstudies voor [SaaS-app-configuratie.](../saas-apps/tutorial-list.md)

| Standaard SAML-configuratie-instelling | SP-geïnitieerd | idP-geïnitieerd | Description |
|:--|:--|:--|:--|
| **Id (Entiteits-id)** | Vereist voor sommige apps | Vereist voor sommige apps | Hiermee wordt de toepassing uniek geïdentificeerd. Azure Active Directory stuurt de id naar de toepassing als parameter van de doelgroep van het SAML-token. Er wordt verwacht dat de toepassing deze valideert. Deze waarde wordt ook weergegeven als de entiteit-ID in SAML-metagegevens die worden geleverd door de toepassing. Voer een URL in die gebruikmaakt van het volgende patroon: 'https:// .contoso.com' U vindt deze waarde als het element Verlener in de <subdomain> *SAML-aanvraag **(AuthnRequest)*** die door de toepassing wordt verzonden. |
| **Antwoord-URL** | Vereist | Vereist | Hiermee geeft u op waar de toepassing verwacht het SAML-token te ontvangen. De antwoord-URL wordt ook de ACS-URL (Assertion Consumer Service) genoemd. U kunt de aanvullende antwoord-URL-velden gebruiken om meerdere antwoord-URL's op te geven. U hebt bijvoorbeeld mogelijk aanvullende antwoord-URL's nodig voor meerdere subdomeinen. Voor testdoeleinden kunt u ook meerdere antwoord-URL's (lokale host en openbare URL's) tegelijk opgeven. |
| **Aanmeldings-URL** | Vereist | Niet opgeven | Wanneer een gebruiker deze URL opent, leidt de serviceprovider hem om naar Azure Active Directory om de gebruiker te verifiëren en aan te melden. Azure AD gebruikt de URL om de toepassing te starten vanuit Microsoft 365 Azure AD-Mijn apps. Wanneer dit leeg is, wordt in Azure AD een door IdP geïnitieerde aanmelding uitgevoerd wanneer een gebruiker de toepassing start vanuit Microsoft 365, Azure AD Mijn apps of de SSO-URL van Azure AD.|
| **Relaystatus** | Optioneel | Optioneel | Hiermee geeft u aan de toepassing op waar de gebruiker naar moet worden omgeleid nadat de verificatie is voltooid. Normaal gesproken is de waarde een geldige URL voor de toepassing. Sommige toepassingen gebruiken dit veld echter anders. Vraag de leverancier van de toepassing voor meer informatie.
| **Afmeldings-URL** | Optioneel | Optioneel | Wordt gebruikt om de saml-logout-antwoorden terug te sturen naar de toepassing.

## <a name="user-attributes-and-claims"></a>Gebruikerskenmerken en claims 

Wanneer een gebruiker wordt geverifieerd bij de toepassing, geeft Azure AD de toepassing een SAML-token met informatie (of claims) over de gebruiker die deze op unieke manier identificeert. Deze informatie omvat standaard de gebruikersnaam, het e-mailadres, de voornaam en de achternaam van de gebruiker. Mogelijk moet u deze claims aanpassen als de toepassing bijvoorbeeld specifieke claimwaarden of een andere **naamindeling** dan gebruikersnaam vereist. 

> [!IMPORTANT]
> Veel apps zijn al vooraf geconfigureerd en in de app-galerie en u hoeft zich geen zorgen te maken over het instellen van gebruikers- en groepsclaims. De [quickstart-reeks laat](add-application-portal.md) u zien hoe u apps toevoegt en configureert.


De **waarde unieke gebruikers-id (naam-id)** is een vereiste claim en is belangrijk. De standaardwaarde is *user.userprincipalname.* De gebruikers-id is uniek voor elke gebruiker in de toepassing. Als het e-mailadres bijvoorbeeld zowel de gebruikersnaam als de unieke id is, wordt de waarde ingesteld op *user.mail*.

Zie How to: customize claims issued in the SAML token for enterprise applications (Claims aanpassen die zijn uitgegeven in het [SAML-token voor bedrijfstoepassingen)](../develop/active-directory-saml-claims-customization.md)voor meer informatie over het aanpassen van SAML-claims.

U kunt nieuwe claims toevoegen. Zie [Groepclaims](../develop/active-directory-saml-claims-customization.md#adding-application-specific-claims) configureren voor meer informatie Over het toevoegen van toepassingsspecifieke claims of om groepsclaims [toe te voegen.](../hybrid/how-to-connect-fed-group-claims.md)


> [!NOTE]
> Zie de volgende resources voor aanvullende manieren om het SAML-token van Azure AD aan te passen aan uw toepassing.
>- Zie Rolclaims configureren Azure Portal om aangepaste [rollen te maken via de Azure Portal.](../develop/active-directory-enterprise-app-role-management.md)
>- Zie Claims aanpassen - PowerShell om de claims aan te passen via [PowerShell.](../develop/active-directory-claims-mapping.md)
>- Zie Optionele claims configureren als u het toepassingsmanifest wilt wijzigen om optionele claims voor uw toepassing [te configureren.](../develop/active-directory-optional-claims.md)
>- Zie Levensduur van tokens configureren voor het instellen van levensduurbeleid voor tokens voor vernieuwen, [toegangstokens,](../develop/active-directory-configurable-token-lifetimes.md)sessietokens en id-tokens. Of als u verificatiesessies via voorwaardelijke toegang van Azure AD wilt beperken, zie [Beheermogelijkheden voor verificatiesessies.](../conditional-access/howto-conditional-access-session-lifetime.md)

## <a name="saml-signing-certificate"></a>SAML-handtekeningcertificaat

Azure AD gebruikt een certificaat om de SAML-tokens te ondertekenen die het naar de toepassing verzendt. U hebt dit certificaat nodig om de vertrouwensrelatie tussen Azure AD en de toepassing te configureren. Zie de SAML-documentatie van de toepassing voor meer informatie over de certificaatindeling. Zie Manage [certificates for federated single sign-on](manage-certificates-for-federated-single-sign-on.md) (Certificaten beheren voor federatief een aanmelding) en Advanced certificate signing options (Geavanceerde opties voor certificaat [ondertekenen) in het SAML-token voor meer informatie.](certificate-signing-options.md)

> [!IMPORTANT]
> Veel apps zijn al vooraf geconfigureerd en in de app-galerie en u hoeft zich niet te houden aan certificaten. De [quickstart-reeks laat](add-application-portal.md) u zien hoe u apps toevoegt en configureert.

Vanuit Azure AD kunt u het actieve certificaat in Base64- of Raw-indeling rechtstreeks downloaden via de hoofdpagina Single **Sign-On met SAML** instellen. U kunt het actieve certificaat ook verkrijgen door het XML-bestand met metagegevens van de toepassing te downloaden of door de APP-URL voor federatiemetagegevens te gebruiken. Volg deze stappen om uw certificaten (actief of inactief) weer te geven, te maken of te downloaden.

Enkele veelvoorkomende zaken die u moet controleren om een certificaat te verifiëren, zijn: 
   - *De juiste vervaldatum.* U kunt de vervaldatum voor maximaal drie jaar in de toekomst configureren.
   - *De status actief voor het juiste certificaat.* Als de status **Inactief** is, wijzigt u de status in **Actief.** Als u de status wilt wijzigen, klikt u met de rechtermuisknop op de rij van het certificaat en selecteert **u Certificaat actief maken.**
   - *De juiste ondertekeningsoptie en het juiste algoritme.*
   - *Het juiste e-mailadres(en) voor meldingen.* Wanneer het actieve certificaat de vervaldatum nadert, verzendt Azure AD een melding naar het e-mailadres dat in dit veld is geconfigureerd.

Soms moet u het certificaat downloaden. Wees echter voorzichtig met het opslaan ervan. Als u het certificaat wilt downloaden, selecteert u een van de opties voor Base64-indeling, Onbewerkte indeling of XML-bestand met federatiemetagegevens. Azure AD biedt ook de **App-URL voor federatiemetagegevens** waar u toegang hebt tot de metagegevens die specifiek zijn voor de toepassing in de indeling `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>` .

> [!NOTE]
> De toepassing moet in staat zijn om de byte-ordermarkering te verwerken die aanwezig is in de XML die wordt weergegeven wanneer wordt https://login.microsoftonline.com/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={app-id} gebruikt. Het byteorderteken wordt weergegeven als een niet-afdrukbaar ASCII-teken »» en in Hex wordt het weergegeven als EF BB BF bij het controleren van de XML-gegevens.

Als u certificaatwijzigingen wilt aanbrengen, selecteert u de knop Bewerken. Er zijn verschillende dingen die u kunt doen op de **pagina SAML-handtekeningcertificaat:**
   - Een nieuw certificaat maken: selecteer **Nieuw certificaat,** selecteer de **Vervaldatum** en selecteer vervolgens **Opslaan.** Als u het certificaat wilt activeren, selecteert u het contextmenu (**...**) **en selecteert u Certificaat actief maken.**
   - Een certificaat met persoonlijke sleutel en PFX-referenties uploaden: selecteer **Certificaat** importeren en blader naar het certificaat. Voer het **PFX-wachtwoord** in en selecteer **vervolgens Toevoegen.**  
   - Configureer geavanceerde certificaat ondertekening. Zie Geavanceerde opties voor certificaat ondertekening voor meer informatie [over deze opties.](certificate-signing-options.md)
   - Aanvullende personen waarschuwen wanneer de vervaldatum van het actieve certificaat is verstreken: voer de e-mailadressen in de velden **E-mailadressen voor meldingen** in.

## <a name="set-up-the-application-to-use-azure-ad"></a>De toepassing instellen voor het gebruik van Azure AD

De **sectie \<applicationName> Instellen bevat** de waarden die moeten worden geconfigureerd in de toepassing, zodat Azure AD wordt gebruikt als SAML-id-provider. U stelt de waarden in op de configuratiepagina op de website van de toepassing. Als u bijvoorbeeld GitHub configureert, gaat u naar de github.com site en stelt u de waarden in. Als de toepassing al vooraf is geconfigureerd en zich in de Azure **AD-galerie,** vindt u een koppeling naar Stapsgewijs instructies weergeven. Anders moet u de documentatie vinden voor de toepassing die u configureert. 

De **waarden voor Aanmeldings-URL** en **Aanmeldings-URL** worden beide naar hetzelfde eindpunt omgeholpen. Dit is het SAML-eindpunt voor de afhandeling van aanvragen voor de Azure AD-tenant. 

De **Azure AD-id** is de waarde van de **verlener** in het SAML-token dat is uitgegeven aan de toepassing.

## <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

Zodra u uw toepassing hebt geconfigureerd voor het gebruik van Azure AD als een op SAML gebaseerde id-provider, kunt u de instellingen testen om te zien of een enkele aanmelding werkt voor uw account. 

Selecteer **Testen** en kies vervolgens om te testen met de momenteel aangemelde gebruiker of als iemand anders. 

Als de aanmelding is geslaagd, kunt u gebruikers en groepen toewijzen aan uw SAML-toepassing. Gefeliciteerd

Als er een foutbericht wordt weergegeven, moet u de volgende stappen voltooien:

1. Kopieer en plak de gegevens in het vak **Hoe ziet de fout eruit?**.

    ![Hulp krijgen bij het oplossen van problemen](media/configure-single-sign-on-non-gallery-applications/error-guidance.png)

2. Selecteer **Richtlijnen voor oplossing krijgen.** De richtlijnen voor de oorzaak en de oplossing van het probleem worden weergegeven.  In dit voorbeeld was de gebruiker niet toegewezen aan de toepassing.

3. Lees de richtlijnen voor het oplossen van problemen en probeer het probleem vervolgens op te lossen.

4. Voer de test opnieuw uit totdat de bewerking is voltooid.

Zie Fouten opsporen in op [SAML gebaseerde](./debug-saml-sso-issues.md)een aanmelding bij toepassingen in Azure Active Directory .


## <a name="next-steps"></a>Volgende stappen

- [Quickstartreeks over toepassingsbeheer](view-applications-portal.md)
- [Gebruikers of groepen toewijzen aan de toepassing](./assign-user-or-group-access-portal.md)
- [Automatische inrichting van gebruikersaccounts configureren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
- [SAML-protocol voor eenmalige aanmelding](../develop/single-sign-on-saml-protocol.md)
