---
title: Wat is Azure Active Directory B2C?
description: Leer hoe u Azure Active Directory B2C kunt gebruiken ter ondersteuning van externe identiteiten in uw toepassingen, waaronder sociale aanmelding met Facebook, Google en andere id-providers.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 09/19/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 29b34041042f00d72b8a9fe86e31bd80f8c77dc2
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94953947"
---
# <a name="what-is-azure-active-directory-b2c"></a>Wat is Azure Active Directory B2C?

Azure Active Directory B2C biedt business-to-customer-identificatie als een service. Uw klanten gebruiken hun eigen sociale, bedrijfs- of lokale accountidentiteiten om via eenmalige aanmelding toegang te krijgen tot uw toepassingen en API's.

![Info-afbeelding van Azure AD B2C-id-providers en stroomafwaartse toepassingen](./media/overview/azureadb2c-overview.png)

Azure Active Directory B2C (Azure AD B2C) is een CIAM-oplossing (Customer Identity Access Management) waarmee miljoenen gebruikers en miljarden verificaties per dag kunnen worden ondersteund. Het zorgt voor het schalen en de beveiliging van het verificatieplatform, waarbij het waakt voor bedreigingen zoals denial-of-service-, wachtwoordspray- en beveiligingsaanvallen en deze automatisch afhandelt.

## <a name="custom-branded-identity-solution"></a>Identiteitsoplossing met uw eigen merk

Azure AD B2C is een 'white-label'-verificatieoplossing. U kunt de gebruikerservaring helemaal aanpassen met uw eigen merk, zodat deze naadloos overvloeit in uw web- en mobiele toepassingen.

Pas elke pagina aan die wordt weergegeven door Azure AD B2C wanneer uw gebruikers zich registreren, zich aanmelden en hun profielgegevens wijzigen. U kunt de HTML, CSS en JavaScript in uw gebruikerstrajecten aanpassen zodat de Azure AD B2C-ervaring eruitziet en werkt alsof deze deel uitmaakt van uw eigen toepassing.

![Aangepaste registratie- en aanmeldingspagina's en achtergrondafbeelding](./media/overview/sign-in-small.png)

## <a name="single-sign-on-access-with-a-user-provided-identity"></a>Toegang met eenmalige aanmelding met een door de gebruiker verschafte identiteit

Azure AD B2C gebruikt op standaarden gebaseerde verificatieprotocollen, waaronder OpenID Connect, OAuth 2.0 en SAML. Het kan worden geïntegreerd met de meeste moderne toepassingen en commerciële software.

![Diagram van identiteiten van derden die worden gefedereerd naar Azure AD B2C](./media/overview/scenario-singlesignon.png)

Doordat Azure AD B2C dient als de centrale verificatie-instantie voor uw webtoepassingen, mobiele apps en API's, kunt u één SSO-oplossing (single sign-on) bouwen voor al deze dingen. Centraliseer de verzameling van gebruikersprofielen en -voorkeuren, en leg gedetailleerde analyses vast over aanmeldingsgedrag en registratie-conversie.

## <a name="integrate-with-external-user-stores"></a>Integreren met externe gebruikersopslag

Azure AD B2C biedt een directory die 100 aangepaste kenmerken per gebruiker kan bevatten. U kunt echter ook integreren met externe systemen. Gebruik bijvoorbeeld Azure AD B2C voor verificatie, maar gebruik een CRM- (beheer van klantrelaties) of klantloyaliteitsdatabase als de bron van waarheid voor klantgegevens.

Een ander scenario voor het extern opslaan van gebruikers is de verificatie voor uw toepassing te laten afhandelen door Azure AD B2C, maar te integreren met een extern systeem waar gebruikersprofielen of persoonsgegevens worden opgeslagen. Om bijvoorbeeld te voldoen aan de vereisten voor gegevenslocatie, zoals een regionaal of on-premises beleid voor gegevensopslag.

![Een logisch diagram van de communicatie van Azure AD B2C met een externe gebruikersopslag](./media/overview/scenario-remoteprofile.png)

Azure AD B2C kan zorgen voor het verzamelen van de gegevens van de gebruiker tijdens de registratie of het bewerken van het profiel, en vervolgens de gegevens overdragen naar het externe systeem. Tijdens toekomstige verificaties kan Azure AD B2C dan de gegevens ophalen uit het externe systeem en indien nodig opnemen in het verificatietokenantwoord dat het naar uw toepassing verzendt.

## <a name="progressive-profiling"></a>Progressieve profilering

Een andere optie voor gebruikersbeleving omvat progressieve profilering. Met progressieve profilering kunnen uw klanten hun eerste transactie snel voltooien, doordat er een minimale hoeveelheid informatie wordt verzameld. Vervolgens verzamelt u geleidelijk meer profielgegevens van de klant bij toekomstige aanmeldingen.

![Een visuele voorstelling van progressieve profilering](./media/overview/scenario-progressive.png)

## <a name="third-party-identity-verification-and-proofing"></a>Identiteitsverificatie en -controle door derden

Gebruik Azure AD B2C om identiteitsverificatie en -controle te vereenvoudigen door gebruikersgegevens te verzamelen en deze door te geven aan een systeem van derden om validatie, vertrouwensscoring en goedkeuring voor het maken van een gebruikersaccount uit te voeren.

![Een diagram van de gebruikersstroom voor identiteitscontrole door derden](./media/overview/scenario-idproofing.png)

Dit zijn slechts enkele van de dingen die u kunt doen met Azure AD B2C als uw business-to-customer-identiteitsplatform. In de volgende secties van dit overzicht vindt u een demonstratietoepassing die gebruikmaakt van Azure AD B2C. U kunt ook rechtstreeks naar een meer diepgaand [technisch overzicht van Azure AD B2C](technical-overview.md) gaan.

## <a name="example-woodgrove-groceries"></a>Voorbeeld: WoodGrove Groceries

[WoodGrove Groceries][woodgrove] is een live web-app die Microsoft heeft gemaakt om verschillende Azure AD B2C-functies te demonstreren. In de volgende secties worden enkele van de verificatieopties van Azure AD B2C op de WoodGrove-website behandeld.

### <a name="business-overview"></a>Bedrijfsoverzicht

WoodGrove is een online winkel die kruidenierswaren verkoopt aan zowel individuele consumenten als zakelijke klanten. Hun zakelijke klanten kopen hun kruidenierswaren namens hun onderneming of bedrijven die ze beheren.

### <a name="sign-in-options"></a>Aanmeldingsopties

WoodGrove Groceries biedt verschillende aanmeldingsopties, afhankelijk van de relatie die klanten hebben met de winkel:

* **Individuele** klanten kunnen zich registreren of aanmelden met individuele accounts, zoals een sociale id-provider of een e-mailadres en wachtwoord.
* **Zakelijke** klanten kunnen zich registreren of aanmelden met hun bedrijfsreferenties.
* **Partners** en leveranciers zijn personen die de winkel producten leveren om te verkopen. Partneridentiteit is wordt geleverd door [Azure Active Directory B2B](../active-directory/external-identities/what-is-b2b.md).

![Aanmeldingspagina's voor personen (B2C), bedrijven (B2C) en partners (B2B)](./media/overview/woodgrove-overview.png)

### <a name="authenticate-individual-customers"></a>Individuele klanten verifiëren

Wanneer een klant **Aanmelden met uw persoonlijke account** selecteert, wordt deze doorgeleid naar een aangepaste aanmeldingspagina die wordt gehost door Azure AD B2C. In de volgende afbeelding kunt u zien dat we de gebruikersinterface hebben aangepast zodat deze er net zo uitziet en werkt als de website van WoodGrove Groceries. Klanten van WoodGrove zouden zich er niet van bewust moeten zijn dat de verificatie-ervaring wordt gehost en beveiligd door Azure AD B2C.

![Aangepaste aanmeldingspagina van WoodGrove, gehost door Azure AD B2C](./media/overview/sign-in.png)

WoodGrove geeft zijn klanten de mogelijkheid zich te registreren en aan te melden door hun Google-, Facebook- of Microsoft-account te gebruiken als id-provider. Klanten kunnen zich ook registreren door hun e-mailadres en een wachtwoord te gebruiken om een zogenoemd *lokaal account* te maken.

Wanneer een klant **Registreren met uw persoonlijke account** selecteert en vervolgens **Nu registreren** kiest, krijgt deze een aangepaste registratiepagina te zien.

![Aangepaste registratiepagina van WoodGrove, gehost door Azure AD B2C](./media/overview/sign-up.png)

Nadat klanten een e-mailadres hebben ingevoerd en **Verificatiecode verzenden** hebben geselecteerd, stuurt Azure AD B2C hun de code. Nadat ze de code hebben ingevoerd, **Code verifiëren** hebben geselecteerd en de rest van de gegevens in het formulier hebben ingevuld, moeten ze ook instemmen met de servicevoorwaarden.

Door op de knop **Maken** te klikken wordt de gebruiker weer teruggeleid naar de website van WoodGrove Groceries. Wanneer Azure AD B2C de klant terugleidt, geeft het een OpenID Connect-verificatietoken door aan de webtoepassing van WoodGrove. De gebruiker is nu aangemeld en klaar om aan de slag te gaan; de weergavenaam van de gebruiker wordt weergegeven in de rechterbovenhoek om te laten zien dat deze is aangemeld.

![Kop van de website van WoodGrove Groceries die laat zien dat de gebruiker is aangemeld](./media/overview/signed-in-individual.png)

### <a name="authenticate-business-customers"></a>Zakelijke klanten verifiëren

Wanneer een klant een van de opties onder **Zakelijke klanten** selecteert, roept de website van WoodGrove Groceries een ander Azure AD B2C-beleid aan dan voor individuele klanten.

Dit beleid geeft gebruikers de mogelijkheid hun bedrijfsreferenties te gebruiken voor registratie en aanmelding. In het WoodGrove-voorbeeld wordt gebruikers gevraagd zich aan te melden met een werk- of schoolaccount. Dit beleid maakt gebruik van een [multitenant Azure AD-toepassings](../active-directory/develop/howto-convert-app-to-be-multi-tenant.md) en het `/common` Azure AD-eindpunt om Azure AD B2C te federeren met elke Microsoft 365-klant, waar ook ter wereld.

### <a name="authenticate-partners"></a>Partners verifiëren

De link **Aanmelden met uw leveranciersaccount** maakt gebruik van de samenwerkingsfunctionaliteit van Azure Active Directory B2B. Azure AD B2B is een groep functies in Azure Active Directory voor het beheren van partneridentiteiten. Deze identiteiten kunnen worden gefedereerd van Azure Active Directory voor toegang tot met Azure AD B2C beveiligde toepassingen.

Lees meer over Azure AD B2B in [Wat is gastgebruikerstoegang in Azure Active Directory B2B?](../active-directory/external-identities/what-is-b2b.md).

<!-- UNCOMMENT WHEN REPO IS UPDATED WITH LATEST DEMO CODE
### Sample code

If you'd like to jump right into the code to see how the WoodGrove Groceries application is built, you can find the repository on GitHub:

[Azure-Samples/active-directory-external-identities-woodgrove-demo][woodgrove-repo] (GitHub)
-->

## <a name="next-steps"></a>Volgende stappen

Nu u een idee hebt van wat Azure AD B2C is en enkele van de scenario's kent waarmee het kan helpen, kunt u dieper ingaan op de functies en technische aspecten.

> [!div class="nextstepaction"]
> [Technisch overzicht van Azure AD B2C >](technical-overview.md)

<!-- LINKS - External -->
[woodgrove]: https://aka.ms/ciamdemo
[woodgrove-repo]: https://github.com/Azure-Samples/active-directory-external-identities-woodgrove-demo