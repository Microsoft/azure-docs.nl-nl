---
title: Inleiding tot Azure Active Directory verifieer bare referenties (preview-versie)
description: Een overzicht van Azure verifieer bare referenties.
services: active-directory
author: barclayn
manager: daveba
editor: ''
ms.service: identity
ms.subservice: verifiable-credentials
ms.topic: overview
ms.date: 04/01/2021
ms.author: barclayn
ms.reviewer: ''
ms.openlocfilehash: 04b36b9b32e78016f693e61d40246776492be0e3
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222857"
---
# <a name="introduction-to-azure-active-directory-verifiable-credentials-preview"></a>Inleiding tot Azure Active Directory verifieer bare referenties (preview-versie)

> [!IMPORTANT]
> Azure Active Directory Controleer bare referenties bevindt zich momenteel in de publieke preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Onze digitale en fysieke levens duur worden steeds meer gekoppeld aan de apps, services en apparaten die we gebruiken om toegang te krijgen tot een uitgebreide set ervaringen. Met deze digitale trans formatie kunnen we met honderden bedrijven en duizenden andere gebruikers communiceren op manieren die voorheen ondenkbaar waren.

Maar identiteits gegevens zijn te vaak zichtbaar in inbreuk op de beveiliging. Deze inbreuken zijn van invloed op het leven van mensen die van invloed zijn op onze sociale, professionele en financiële levens duur. Micro soft is van mening dat er een betere manier is. Elke persoon heeft een recht op een identiteit waarvan ze eigenaar zijn en die deze beheert, een waarmee veilig elementen van hun digitale identiteit worden opgeslagen en de privacy wordt behouden. In deze primer wordt uitgelegd hoe we aan de slag worden toegevoegd met een gevarieerde Community voor het bouwen van een open, betrouw bare, interoperabele en op standaarden gebaseerde, gedecentraliseerde identiteits oplossing voor individuen en organisaties.

## <a name="why-we-need-decentralized-identity"></a>Waarom we gedecentraliseerde identiteit nodig hebben

Vandaag gebruiken we onze digitale identiteit op het werk, thuis en in alle apps, services en apparaten die we gebruiken. Het bestaat uit alles wat we zeggen, doen en ervaring hebben met het kopen van tickets voor een evenement, het inchecken van een hotel of het afronden van de lunch. Onze identiteit en al onze digitale interacties zijn momenteel eigendom van en worden beheerd door andere partijen, waarvan sommige niet even op de hoogte zijn.

Over het algemeen verlenen gebruikers toestemming voor verschillende apps en apparaten. Deze aanpak vereist een hoge mate van waakzaamheid op het deel van de gebruiker om bij te houden wie toegang heeft tot de gegevens. Op de voor zijde van de onderneming vereist samen werking met consumenten en partners een geavanceerde indeling om gegevens veilig te kunnen uitwisselen op een manier die de privacy en beveiliging voor alle betrokkenen onderhoudt.

We zijn ervan overtuigd dat een op standaarden gebaseerd gedecentraliseerd identiteits systeem een nieuwe set ervaringen kan ontgrendelen waarmee gebruikers en organisaties meer controle over hun gegevens kunnen krijgen, en een hogere mate van vertrouwen en beveiliging bieden voor apps, apparaten en service providers

## <a name="lead-with-open-standards"></a>Lood met open standaarden

We streven ernaar nauw samen te werken met klanten, partners en de community om de volgende generatie van gedecentraliseerde identiteiten te ontgrendelen en we zijn enthousiast over de partner met de individuen en organisaties die in deze ruimte geweldige bijdragen doen. Als het DID-ecosysteem groeit, moeten standaarden, technische onderdelen en code-producten open-source zijn en toegankelijk zijn voor alle.

Micro soft maakt actief samen met leden van de gedecentraliseerde identiteits basis (DIF), de Community-groep voor W3C-referenties en de bredere identiteits community. We werken samen met deze groepen voor het identificeren en ontwikkelen van essentiële normen en de volgende standaarden zijn geïmplementeerd in onze services.

* [Gedecentraliseerde W3C-Id's](https://www.w3.org/TR/did-core/)
* [Geverifieerde W3C-referenties](https://www.w3.org/TR/vc-data-model/)
* [DIF-Sidetree](https://identity.foundation/sidetree/spec/)
* [NIET-bekende DIF-configuratie](https://identity.foundation/specs/did-configuration/)
* [DIF-SIOP](https://identity.foundation/did-siop/)
* [DIF-presentatie uitwisseling](https://identity.foundation/presentation-exchange/)


## <a name="what-are-dids"></a>Wat zijn DIDs?

Voordat we inzicht kunnen krijgen in DIDs, is het handig om ze te vergelijken met de huidige identiteits systemen. E-mail adressen en sociaal-netwerk-Id's zijn mensen vriendelijke aliassen voor samen werking, maar zijn nu overbelast om te fungeren als de controle punten voor gegevens toegang in een groot aantal scenario's buiten de samen werking. Hiermee maakt u een mogelijk probleem, omdat de toegang tot deze Id's op elk gewenst moment door externe partijen kan worden verwijderd.

Gecentraliseerde Id's (DIDs) verschillen. DIDs zijn door de gebruiker gegenereerde, eigen eigendom, wereld wijd unieke id's die zijn geroot in gedecentraliseerde systemen zoals ION. Ze beschikken over unieke kenmerken, zoals een grotere zekerheid van Onveranderbaarheid, censoren weers tand en knoei evasiveness. Deze kenmerken zijn essentieel voor elk ID-systeem dat bedoeld is voor het verlenen van eigen eigendom en gebruikers beheer. 

De verifieer bare referentie oplossing van micro soft maakt gebruik van gedecentraliseerde referenties (DIDs) om een cryptografische ondertekening te maken als bewijs dat een Relying Party (Verifier) een verklaring vormt van de eigenaar van een verifieer bare referentie. Daarom wordt een basis memorandum van gedecentraliseerde id's aanbevolen voor iedereen die een verifieer bare referentie oplossing maakt op basis van de aanbieding van micro soft.
## <a name="what-are-verifiable-credentials"></a>Wat zijn Controleer bare referenties?

 We gebruiken Id's in onze dagelijkse levens duur. We hebben stuur Programma's licenties die worden gebruikt als bewijs van onze mogelijkheid om een auto te gebruiken. Universiteiten geven diploma's die bewijzen dat we een opleidings niveau hebben bereikt. We gebruiken paspoorten om te bewijzen wie de autoriteiten hebben als we aan andere landen aankomen. Het gegevens model beschrijft hoe we deze typen scenario's kunnen verwerken wanneer ze via internet werken, maar op een veilige manier die de privacy van de gebruiker respecteert. U kunt aanvullende informatie krijgen in het [gegevens Model 1,0 verifieer bare referenties](https://www.w3.org/TR/vc-data-model/)

Kortom, Controleer bare referenties zijn gegevens objecten die bestaan uit claims die zijn gemaakt door de certificaat verlener die informatie over een onderwerp verklaart. Deze claims worden aangeduid met een schema en bevatten de uitgever en het onderwerp. De uitgever heeft een digitale hand tekening gemaakt als bewijs dat ze aan deze informatie worden verstrekt.


## <a name="how-does-decentralized-identity-work"></a>Hoe werkt gedecentraliseerde identiteit?

We hebben een nieuwe vorm van identiteit nodig. We hebben een identiteit nodig die technologieën en standaarden biedt om kenmerken van de sleutel identiteit te leveren, zoals eigen eigendom en de weers tand van censoren. Deze mogelijkheden zijn lastig te maken met behulp van bestaande systemen.

Voor de levering van deze beloftes is een technische Stichting vereist die bestaat uit zeven essentiële innovaties. Een sleutel innovatie is id's die eigendom zijn van de gebruiker, een gebruikers agent voor het beheren van sleutels die zijn gekoppeld aan dergelijke id's en versleutelde, door de gebruiker beheerde gegevens opslag.

![overzicht van de verifieer bare referentie omgeving van micro soft](media/decentralized-identifier-overview/microsoft-did-system.png)

**1. DIDs-id's (W3C decentraled Identifiers)** kunnen gebruikers maken, beheren en onafhankelijk van elke organisatie of regering. DIDs zijn wereld wijd unieke id's die zijn gekoppeld aan gecentraliseerde meta gegevens van de open bare-sleutel infrastructuur (DPKI) die bestaan uit JSON-documenten die open bare-sleutel materiaal, verificatie-descriptors en service-eind punten bevatten.

**2. gedecentraliseerd systeem: Ion (Identity overlay Network)** Ion is een laag 2 open, zonder toestemming netwerk op basis van het louter deterministische Sidetree-protocol, dat geen speciale tokens, vertrouwde validatie functies of andere consensus mechanismen vereist; de lineaire voortgang van de tijd keten van Bitcoin is allemaal vereist voor de werking ervan. We hebben [een NPM-pakket geopend](https://www.npmjs.com/package/@decentralized-identity/ion-tools) om te werken met het ion-netwerk dat eenvoudig kan worden geïntegreerd in uw apps en services. Bibliotheken bevatten het maken van een nieuwe, het genereren van sleutels en het verankeren van uw ervaring op de Bitcoin Block chain. 

**3. did-gebruikers agent/Wallet: met Microsoft Authenticator app** kunnen echte, gecentraliseerde identiteiten en controleer bare referenties worden gebruikt. Verificator maakt DIDs, vereenvoudigt uitgifte-en presentatie aanvragen voor verifieer bare referenties en beheert de back-up van de seeding van uw doel via een versleuteld Wallet-bestand.

**4. micro soft resolver** een API die verbinding maakt met ons Ion-knoop punt om DIDs te zoeken en op te lossen met behulp van de ```did:ion``` methode en om het did-document object (DDO) te retour neren. De DDO bevat DPKI-meta gegevens die zijn gekoppeld aan de, zoals open bare sleutels en service-eind punten. 

**5. Azure Active Directory geverifieerde aanmeldings gegevens** zijn een API voor uitgifte en verificatie en een open-source SDK voor [W3C-geverifieerde referenties](https://www.w3.org/TR/vc-data-model/) die zijn ondertekend met de- ```did:ion``` methode. Hiermee kunnen identiteits eigenaren claims genereren, presen teren en verifiëren. Dit vormt de basis van de vertrouwens relatie tussen gebruikers van de systemen.

## <a name="a-sample-scenario"></a>Een voorbeeld scenario

Het scenario dat we gebruiken om uit te leggen hoe VCs werkt:

- Woodgrove Inc. een bedrijf.
- Proseware, een bedrijf dat de kortingen van Woodgrove werk nemers biedt.
- Alice, een werk nemer van Woodgrove, Inc., die een korting wil ontvangen van proseware



ANNEER heeft vandaag een gebruikers naam en wacht woord om zich aan te melden op de netwerk omgeving van Woodgrove. Woodgrove voert een VC-oplossing uit om Lisa een meer beheer bare manier te bieden om de werk nemer van Woodgrove te bewijzen. Proseware maakt gebruik van een VC-oplossing die compatibel is met de VC-oplossing van Woodgrove en accepteert de referenties die door Woodgrove als bewijs van de dienst worden verleend.

De uitgever van de referentie, Woodgrove Inc. maakt een open bare sleutel en een persoonlijke sleutel. De open bare sleutel wordt opgeslagen op ION. Wanneer de sleutel wordt toegevoegd aan de infra structuur, wordt de vermelding vastgelegd in een op Block Chain gebaseerd, gedecentraliseerde grootboek post. De uitgever biedt Anja de persoonlijke sleutel die is opgeslagen in een wallet-toepassing. Elke keer dat Anja de persoonlijke sleutel gebruikt, wordt de trans actie vastgelegd in de Wallet-toepassing.

![overzicht van micro soft](media/decentralized-identifier-overview/did-overview.png)

## <a name="roles-in-a-verifiable-credential-solution"></a>Rollen in een verifieer bare referentie oplossing 

Er zijn drie primaire actors in de verifieer bare referentie oplossing. In het volgende diagram:

- **Stap 1**, de **gebruiker** vraagt een verifieer bare referentie aan van een uitgever.
- **Stap 2**, de **Uitgever** van de referentie verklaart dat het bewijs dat de gebruiker heeft opgegeven nauw keurig is en dat er een verifieer bare referentie wordt gemaakt die is ondertekend met de gebruikers en dat de gebruiker het onderwerp heeft.
- **In stap 3** ondertekent de gebruiker een Controleer bare presentatie (VP) met hun gestuurde en verzendt deze naar de **verificateur.** De verificateur valideert de referentie vervolgens door te vergelijken met de open bare sleutel die in de DPKI is geplaatst.

De rollen in dit scenario zijn:

![rollen in een verifieer bare referentie omgeving](media/decentralized-identifier-overview/issuer-user-verifier.png)

**verlener** : de verlener is een organisatie die een uitgifte oplossing maakt waarmee informatie van een gebruiker wordt aangevraagd. De informatie wordt gebruikt om de identiteit van de gebruiker te verifiëren. Bijvoorbeeld: Woodgrove, Inc. heeft een uitgifte oplossing waarmee ze verifieer bare referenties (VCs) kunnen maken en distribueren naar al hun werk nemers. De werk nemer gebruikt de verificator-app om u aan te melden met de gebruikers naam en het wacht woord, waarmee een ID-token aan de verlenende service wordt door gegeven. Zodra Woodgrove, Inc. het verzonden ID-token valideert, maakt de uitgifte oplossing een VC dat claims bevat over de werk nemer en is ondertekend met Woodgrove, Inc.. De werk nemer heeft nu een verifieer bare referentie die is ondertekend door hun werk gever, waaronder de werk nemers hebben gereageerd als het onderwerp.  

**gebruiker** : de gebruiker is de persoon of entiteit die een VC aanvraagt. Bijvoorbeeld, Anne is een nieuwe werk nemer van Woodgrove, Inc. en werd eerder een bewijs van controle van de bewerkings referentie verleend. Als Lisa een bewijs van de dienst verlening moet doen om een korting te krijgen op proseware, kan ze toegang verlenen tot de referentie in de verificator-app door een verifieer bare presentatie te ondertekenen die Anne de eigenaar van de heeft. Proseware kan valideren of de referentie is uitgegeven door Woodgrove, Inc. en ANNEER is de eigenaar van de referentie. 

**Verifier** : de Verifier is een bedrijf of entiteit die claims moet verifiëren van een of meer verleners die ze vertrouwen. Proseware vertrouwt bijvoorbeeld Woodgrove, Inc. voert een passende taak uit om de identiteit van hun werk nemers te verifiëren en authentieke en geldige VCs te verlenen. Als Anne probeert de uitrusting te best Ellen die hij nodig heeft voor haar taak, gebruiken Proseware open standaarden zoals SIOP en Presentation Exchange om referenties aan te vragen van de gebruiker die bewijst dat ze een werk nemer van Woodgrove, Inc. zijn. Proseware kunnen bijvoorbeeld Anja een koppeling naar een website bieden met een QR-code die ze scant met haar telefoon camera. Hiermee wordt de aanvraag voor een specifiek VC gestart, die door de verificator wordt geanalyseerd en de mogelijkheid wordt geboden om de aanvraag goed te keuren om haar werk gelegenheid te bewijzen naar proseware. Proseware kan de verifieer bare credentials-Service-API of SDK gebruiken om de authenticiteit van de geverifieerde presentatie te controleren. Op basis van de informatie die door Alice wordt verstrekt, geven ze de korting van Alice. Als andere bedrijven en organisaties weten dat de VCs van de werk nemers van Woodgrove, Inc. problemen ondervindt, kunnen ze ook een Verifier-oplossing maken en de verifieer bare referentie van Woodgrove, Inc. gebruiken om speciale aanbiedingen te bieden die zijn gereserveerd voor de werk nemers van Woodgrove, Inc.

## <a name="next-steps"></a>Volgende stappen

Nu u bekend bent met DIDs en verifieer bare referenties, kunt u ze zelf proberen door het artikel aan de slag te volgen of door een van onze artikelen meer details te geven over de concepten van verifieer bare referenties.

- [Aan de slag met verifieer bare referenties](get-started-verifiable-credentials.md)
- [Uw referenties aanpassen](credential-design.md)
- [Veelgestelde vragen over verifieer bare referenties](verifiable-credentials-faq.md)