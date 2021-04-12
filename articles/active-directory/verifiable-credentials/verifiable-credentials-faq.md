---
title: Veelgestelde vragen-Azure verifieer bare referenties (preview-versie)
description: Antwoorden vinden op veelgestelde vragen over verifieer bare referenties
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 3c43cfb537c84fb25a6bf879d32a8034342ff605
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280714"
---
# <a name="frequently-asked-questions-faq"></a>Veelgestelde vragen

Deze pagina bevat veelgestelde vragen over verifieer bare referenties en gedecentraliseerde identiteit. Vragen zijn ingedeeld in de volgende secties.

- [Vocabulaire en basis principes](#the-basics)
- [Conceptuele vragen over gedecentraliseerde identiteit](#conceptual-questions)
- [Vragen over het gebruik van de preview-versie van geverifieerde referenties](#using-the-preview)

> [!IMPORTANT]
> Azure Active Directory Controleer bare referenties bevindt zich momenteel in de publieke preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="the-basics"></a>De basisbeginselen

### <a name="what-is-a-did"></a>Wat is er? 

Gedecentraliseerde Identifers (DIDs) zijn id's die kunnen worden gebruikt voor het beveiligen van de toegang tot resources, het ondertekenen en verifiëren van referenties en het vergemakkelijken van de uitwisseling van toepassings gegevens. In tegens telling tot traditionele gebruikers namen en e-mail adressen, zijn DIDs eigendom van en worden beheerd door de entiteit zelf (een persoon, apparaat of bedrijf). DIDs bestaan onafhankelijk van elke externe organisatie of vertrouwde tussen persoon. In [de specificatie van de gedecentraliseerde W3C-id](https://www.w3.org/TR/did-core/) wordt dit gedetailleerd beschreven.

### <a name="why-do-we-need-a-did"></a>Waarom hebben we een nodig?

Voor de basis van Digital Trust moeten deel nemers eigenaar zijn van hun identiteit en de identiteit worden bepaald met de id.
In een leeftijd van dagelijks, grootschalige systeem inbreuken en aanvallen op gecentraliseerde id-honeypots is het decentraliseren van identiteit een kritieke beveiligings behoefte voor consumenten en bedrijven.
Personen die eigenaar zijn van hun identiteiten, kunnen verifieer bare gegevens en proef afdrukken uitwisselen. Een gedistribueerde referentie omgeving maakt automatisering mogelijk van veel bedrijfs processen die momenteel hand matig en arbeids intensief zijn.

### <a name="what-is-a-verifiable-credential"></a>Wat is een verifieer bare referentie? 

Referenties maken deel uit van onze dagelijkse levens duur. de licenties van het stuur programma worden gebruikt om te bevestigen dat we een motor voertuig kunnen gebruiken. de universitaire Vrijheid kan worden gebruikt om ons opleidings niveau te bevestigen, en door overheids certificaten uitgegeven paspoorten zorgen ervoor dat wij kunnen reizen tussen landen. Verifieer bare referenties bieden een mechanisme om deze sorteringen van referenties op het web uit te drukken op een manier die cryptografisch veilig, onderhouds-en computer verificatie is. [De specificatie van de W3C](https://www.w3.org/TR/vc-data-model//) -controle bare referenties bevat meer informatie.


## <a name="conceptual-questions"></a>Conceptuele vragen

### <a name="what-happens-when-a-user-loses-their-phone-can-they-recover-their-identity"></a>Wat gebeurt er wanneer een gebruiker de telefoon kwijtraakt? Kunnen ze hun identiteit herstellen?

Er zijn meerdere manieren om een herstel mechanisme te bieden aan gebruikers, elk met hun eigen afweging. We evalueren momenteel opties en ontwerpen benaderingen voor herstel die het gemak en de beveiliging bieden met betrekking tot de privacy en zelf soevereiniteit van een gebruiker.

### <a name="how-can-a-user-trust-a-request-from-an-issuer-or-verifier-how-do-they-know-a-did-is-the-real-did-for-an-organization"></a>Hoe kan een gebruiker een aanvraag van een uitgever of Verifier vertrouwen? Hoe weten ze dat een organisatie het echt is?

We hebben [de bekende configuratie specificaties van de gedecentraliseerde identiteits basis](https://identity.foundation/.well-known/resources/did-configuration/) geïmplementeerd om verbinding te kunnen maken met een bekend bestaand systeem, domein namen. Elk is gemaakt met behulp van de Azure Active Directory Controleer bare referenties hebben de mogelijkheid om een hoofd domein naam op te nemen die in het DID-document wordt gecodeerd. Volg het artikel met de titel [uw domein koppelen aan uw gedistribueerde id](how-to-dnsbind.md) voor meer informatie.  

### <a name="why-does-the-verifiable-credential-preview-use-ion-as-its-did-method-and-therefore-bitcoin-to-provide-decentralized-public-key-infrastructure"></a>Waarom gebruikt de verifieer bare referentie-preview ION ionen als methode en daarom Bitcoin de infra structuur voor gedecentraliseerde openbaar-sleutel te bieden?

ION is een gedecentraliseerd, niet-gecentraliseerd, schaalbaar gedecentraliseerd netwerk met id Layer 2 dat hierop Bitcoin uitvoert. De schaal baarheid wordt gerealiseerd zonder een speciaal cryptoasset-token, vertrouwde validatie functies of gecentraliseerde consensus mechanismen. We gebruiken Bitcoin voor het sublaag 1 substraat vanwege de sterkte van het gedecentraliseerde netwerk om een hoge mate van Onveranderbaarheid te bieden voor een chronologisch gebeurtenis record systeem.

## <a name="using-the-preview"></a>De preview-versie gebruiken

### <a name="why-must-i-use-nodejs-for-the-verifiable-credentials-preview-any-plans-for-other-programming-languages"></a>Waarom moet ik NodeJS gebruiken voor de voor beeld van verifieer bare referenties? Abonnementen voor andere programmeer talen? 

We hebben NodeJS gekozen, omdat het een zeer populair platform is voor toepassings ontwikkelaars. We zullen een rest API uitgeven waarmee de ontwikkel aars referenties kunnen uitgeven en verifiëren. 

### <a name="is-any-of-the-code-used-in-the-preview-open-source"></a>Is een van de code die wordt gebruikt in de voor beeld-open source?

Ja. De volgende opslag plaatsen zijn de open source-onderdelen van onze services.

1. [SideTree, op GitHub](https://github.com/decentralized-identity/sidetree)
2. De [VC SDK voor node, op github](https://github.com/microsoft/VerifiableCredentials-Verification-SDK-Typescript)
3. Een [Android-SDK voor het bouwen van gedecentraliseerde identiteits portefeuilles op github](https://github.com/microsoft/VerifiableCredential-SDK-Android)
4. Een [IOS-SDK voor het bouwen van gedecentraliseerde identiteits portefeuilles op github](https://github.com/microsoft/VerifiableCredential-SDK-iOS)


## <a name="what-are-the-licensing-requirements"></a>Wat zijn de licentievereisten?

Een Azure AD P2-licentie is vereist voor het gebruik van de preview-versie van verifieer bare referenties. Dit is een tijdelijke vereiste, omdat de prijs voor deze service wordt gefactureerd op basis van het gebruik. 


## <a name="next-steps"></a>Volgende stappen

- [Uw Azure Active Directory verifieer bare referenties aanpassen](credential-design.md)
