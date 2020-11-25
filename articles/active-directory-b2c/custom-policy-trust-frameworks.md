---
title: Referentie-vertrouwens raamwerken in Azure Active Directory B2C | Microsoft Docs
description: Een onderwerp over Azure Active Directory B2C aangepast beleid en het Framework voor identiteits ervaring.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/04/2017
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d3d29bd05f67d00047499dc256e5e1a82f98693a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95990980"
---
# <a name="define-trust-frameworks-with-azure-ad-b2c-identity-experience-framework"></a>Vertrouwens raamwerken definiëren met Azure AD B2C Identity experience Framework

Azure Active Directory B2C (Azure AD B2C) aangepaste beleids regels die gebruikmaken van het Framework voor identiteits ervaring, bieden uw organisatie een gecentraliseerde service. Deze service vermindert de complexiteit van identiteits Federatie in een grote community van belang. De complexiteit is beperkt tot één vertrouwens relatie en één uitwisseling van meta gegevens.

Azure AD B2C aangepaste beleids regels maken gebruik van het Framework voor identiteits ervaring zodat u de volgende vragen kunt beantwoorden:

- Wat zijn de juridische, beveiligings-, privacy-en gegevens beveiligings beleidsregels waaraan moet worden voldaan?
- Wie zijn de contact personen en wat zijn de processen om een geaccrediteerde deel nemer te worden?
- Wie zijn de geaccrediteerde identiteits gegevens providers (ook wel ' claim providers ' genoemd) en wat doen ze?
- Wie zijn de erkende relying party's (en optioneel, wat zijn ze nodig)?
- Wat zijn de technische interoperabiliteits vereisten voor deel nemers?
- Wat zijn de operationele runtime-regels die moeten worden afgedwongen voor het uitwisselen van gegevens van digitale identiteiten?

Azure AD B2C aangepaste beleids regels die gebruikmaken van het Framework voor identiteits ervaring, gebruiken we de retrust Framework (TF)-Construct om al deze vragen te beantwoorden. Laten we eens kijken naar deze constructie en wat deze biedt.

## <a name="understand-the-trust-framework-and-federation-management-foundation"></a>Meer informatie over het vertrouwens raamwerk en Federatie beheer Foundation

Het vertrouwens raamwerk is een schriftelijke specificatie van het identiteits-, beveiligings-, privacy-en gegevens beveiligings beleid waaraan deel nemers in een community van belang moeten voldoen.

Federatieve identiteiten bieden een basis voor het realiseren van identiteits garantie voor eind gebruikers op internet. Door identiteits beheer te delegeren aan derden, kan één digitale identiteit voor een eind gebruiker opnieuw worden gebruikt met meerdere relying party's.

Identiteits controle vereist dat id-providers (id) en kenmerk providers (AtPs) zich houden aan specifieke beveiligings-, privacy-en operationeel beleid en-procedures.  Als ze geen rechtstreekse controles kunnen uitvoeren, moeten relying party's (RPs) vertrouwens relaties ontwikkelen met de id en AtPs waarmee ze werken.

Naarmate het aantal consumenten en providers van digitale identiteits gegevens groeit, is het lastig om het Pairwise-beheer van deze vertrouwens relaties te voortzetten, of zelfs de Pairwise-uitwisseling van de technische meta gegevens die vereist zijn voor de netwerk verbinding.  Bij het oplossen van deze problemen hebben Federatie hubs slechts een beperkt aantal geslaagd.

### <a name="what-a-trust-framework-specification-defines"></a>Wat een trust Framework-specificatie definieert
TFs zijn de linchpins van het OIX-vertrouwens raamwerk model (Open Identity Exchange), waarbij elke community van belang is onderworpen aan een bepaalde TF-specificatie. Een dergelijke TF-specificatie definieert:

- **De metrische gegevens over beveiliging en privacy voor de community van belang voor de definitie van:**
    - De mate van zekerheid (LOA) die door deel nemers worden aangeboden/vereist; bijvoorbeeld een geordende set betrouwbaarheids classificaties voor de echtheid van digitale identiteits gegevens.
    - De beveiligings niveaus (LOP) die door deel nemers worden aangeboden/vereist; bijvoorbeeld een geordende set betrouwbaarheids classificaties voor de beveiliging van digitale identiteits gegevens die worden verwerkt door deel nemers in de community van belang.

- **De beschrijving van de digitale identiteits gegevens die door deel nemers worden aangeboden/vereist**.

- **Het technische beleid voor de productie en het gebruik van digitale identiteits gegevens, en dus voor het meten van LOA en LOP. Deze schriftelijke beleids regels bevatten doorgaans de volgende beleids regels:**
    - Beleids regels voor identiteits controle: *hoe sterk is de identiteits gegevens van een persoon gecontroleerd?*
    - Beveiligings beleid, bijvoorbeeld *hoe sterk de integriteit en vertrouwelijkheid van gegevens zijn beveiligd?*
    - Privacybeleid, bijvoorbeeld: *welk besturings element heeft een gebruiker over persoonlijke Identificeer bare informatie (PII)*?
    - Beleid voor overlevings vermogen, bijvoorbeeld: *als een provider geen bewerkingen meer kan uitvoeren, hoe is de continuïteit en beveiliging van de PII-functie niet langer van toepassing?*

- **De technische profielen voor de productie en het gebruik van digitale identiteits gegevens. Deze profielen zijn onder andere:**
    - Scope-interfaces waarvoor informatie over digitale identiteiten beschikbaar is op een opgegeven LOA.
    - Technische vereisten voor on-the-Wire-interoperabiliteit.

- **De beschrijvingen van de verschillende rollen die deel nemers in de community kunnen uitvoeren en de kwalificaties die nodig zijn om aan deze rollen te voldoen.**

Daarom bepaalt een TF-specificatie hoe identiteits gegevens worden uitgewisseld tussen de deel nemers van de community van belang: relying party's, identiteits-en kenmerk providers en controle Programma's voor kenmerken.

Een TF-specificatie is een of meer documenten die dienen als referentie voor het governance van de community van belang voor het bepalen van de beweringen en het gebruik van digitale identiteits gegevens in de community. Het is een gedocumenteerde set beleids regels en procedures die zijn ontworpen om vertrouwen in de digitale identiteiten in te richten die worden gebruikt voor online trans acties tussen leden van een community van belang.

Met andere woorden, een TF-specificatie definieert de regels voor het maken van een levensvatbaar federatieve identiteits ecosysteem voor een community.

Er is momenteel een wijde overeenkomst tussen de voor delen van een dergelijke benadering. Er is geen twijfel dat de specificaties van het vertrouwens raamwerk de ontwikkeling van digitale identiteits ecosystemen met Controleer bare kenmerken van veiligheid, verzekeringen en privacy vergemakkelijken, wat betekent dat ze opnieuw kunnen worden gebruikt in meerdere community's van belang.

Daarom gebruikt Azure AD B2C aangepast beleid dat het Framework voor identiteits ervaring gebruikt, de specificatie als basis voor de gegevens representatie van een TF om interoperabiliteit te vergemakkelijken.

Azure AD B2C aangepaste beleids regels die gebruikmaken van het Framework voor identiteits ervaring, vertegenwoordigen een TF-specificatie als een mengeling van menselijk en machine-Lees bare gegevens. Sommige secties van dit model (doorgaans secties die meer gericht zijn op governance) worden weer gegeven als verwijzingen naar gepubliceerde documentatie over beveiligings-en privacybeleid, samen met de bijbehorende procedures (indien van toepassing). In andere secties worden de meta gegevens van de configuratie en runtime regels beschreven die de operationele automatisering vergemakkelijken.

## <a name="understand-trust-framework-policies"></a>Vertrouwens raamwerk beleid begrijpen

In termen van de implementatie bestaat de TF-specificatie uit een set beleids regels waarmee het gedrag van identiteiten en ervaringen kan worden beheerd.  Azure AD B2C aangepaste beleids regels die gebruikmaken van het Framework voor identiteits ervaring, kunt u uw eigen TF ontwerpen en maken met behulp van de volgende declaratieve beleids regels die kunnen worden gedefinieerd en geconfigureerd:

- De document verwijzing of verwijzingen die het federatieve identiteits ecosysteem van de Community bepalen, gerelateerd aan de TF. Ze zijn koppelingen naar de TF-documentatie. De (vooraf gedefinieerde) operationele runtime regels of de gebruikers ritten waarmee de uitwisseling en het gebruik van de claims worden geautomatiseerd en/of beheerd. Deze gebruikers ritten zijn gekoppeld aan een LOA (en een LOP). Een beleid kan daarom gebruikers ritten hebben met verschillende LOAs (en LOPs).

- De identiteits-en kenmerk providers, of de claim providers, in de community van belang en de technische profielen die worden ondersteund samen met de (out-of-band) LOA/LOP-accreditering die aan hen is gekoppeld.

- De integratie met kenmerk controleprogramma's of claim providers.

- De relying party's in de Community (door middel van interferentie).

- De meta gegevens voor het tot stand brengen van netwerk communicatie tussen deel nemers. Deze meta gegevens, samen met de technische profielen, worden gebruikt tijdens een trans actie voor het vastmaken van de Wire-interoperabiliteit tussen de Relying Party en andere deel nemers van de community.

- De Protocol conversie, indien aanwezig (bijvoorbeeld SAML 2,0, OAuth2, WS-Federation en OpenID Connect Connect).

- De verificatie vereisten.

- De multi factor-indeling, indien aanwezig.

- Een gedeeld schema voor alle claims die beschikbaar zijn en toewijzingen aan deel nemers van een community van belang.

- Alle claim transformaties, samen met de gegevens die in deze context kunnen worden geminimaliseerd, om de uitwisseling en het gebruik van de claims te hand haven.

- De binding en versleuteling.

- De claim opslag.

### <a name="understand-claims"></a>Informatie over claims

> [!NOTE]
> We verwijzen gezamenlijk naar alle mogelijke soorten identiteits gegevens die kunnen worden uitgewisseld als ' claims ': claims over de authenticatie referenties van een eind gebruiker, identiteits hebben, communicatie apparaat, fysieke locatie, persoonlijke identificatie kenmerken, enzovoort.
>
> We gebruiken de term ' claims '--in plaats van ' Attributes '--omdat deze gegevens artefacten in online trans acties geen feiten zijn die rechtstreeks kunnen worden geverifieerd door de Relying Party. In plaats daarvan zijn er beweringen of claims over feiten waarvoor de Relying Party voldoende vertrouwen moet ontwikkelen om de aangevraagde trans actie van de eind gebruiker te verlenen.
>
> We gebruiken ook de term ' claims ' omdat Azure AD B2C aangepaste beleids regels die gebruikmaken van het Framework voor identiteits ervaring, zijn ontworpen om de uitwisseling van alle typen digitale identiteits gegevens op een consistente manier te vereenvoudigen, ongeacht of het onderliggende protocol is gedefinieerd voor gebruikers verificatie of het ophalen van kenmerken.  We gebruiken ook de term ' claim providers ' om gezamenlijk te verwijzen naar id-providers, kenmerk providers en controle Programma's voor kenmerken wanneer we niet willen onderscheiden van hun specifieke functies.

Daarom bepalen ze hoe identiteits gegevens worden uitgewisseld tussen een Relying Party, identiteit en kenmerk providers en controle Programma's voor kenmerken. Ze bepalen welke identiteits-en kenmerk providers vereist zijn voor de verificatie van een Relying Party. Ze moeten worden beschouwd als een domein-specifieke taal (DSL), dat wil zeggen, een computer taal die speciaal is bedoeld voor een bepaald toepassings domein met overname, *indien* -instructies, polymorfisme.

Deze beleids regels vormen het door de machine Lees bare deel van de TF-construct in Azure AD B2C aangepaste beleids regels maken gebruik van het Framework voor identiteits ervaring. Ze omvatten alle operationele gegevens, waaronder meta gegevens en technische profielen van claim providers, claim schema definities, claim transformatie functies en gebruikers trajecten die zijn ingevuld om operationele integratie en automatisering te vergemakkelijken.

Er wordt van uitgegaan dat er sprake is van *levende documenten* , omdat er een goede kans bestaat dat de inhoud gedurende een periode wordt gewijzigd met betrekking tot de actieve deel nemers die in het beleid zijn gedeclareerd. Het is ook mogelijk dat de voor waarden van een deel nemer worden gewijzigd.

De Federatie-instellingen en-onderhoud worden uitgebreid vereenvoudigd door relying party's te gebruiken voor het afschermen van vertrouwens relaties en het opnieuw configureren van connectiviteit als verschillende claim providers/controle Programma's die worden toegevoegd aan of verlaten (de Community vertegenwoordigd door) de set beleids regels.

Interoperabiliteit is een andere belang rijke uitdaging. Aanvullende claim providers/controle Programma's moeten worden geïntegreerd, omdat relying party's waarschijnlijk niet alle benodigde protocollen ondersteunen. Azure AD B2C aangepast beleid lost dit probleem op door de industrie standaard protocollen te ondersteunen en door specifieke gebruikers ritten toe te passen om aanvragen te transponeren wanneer relying party's en kenmerk providers niet hetzelfde protocol ondersteunen.

Gebruikers trajecten zijn protocol profielen en meta gegevens die worden gebruikt voor het vastmaken van de Wire-interoperabiliteit tussen de Relying Party en andere deel nemers. Er zijn ook operationele runtime regels die worden toegepast op identiteits informatie Exchange-aanvraag/antwoord berichten voor het afdwingen van naleving van het gepubliceerde beleid als onderdeel van de TF-specificatie. Het idee van gebruikers ritten is essentieel voor de aanpassing van de klant ervaring. Ook wordt het lampje uitgescheiden van de werking van het systeem op protocol niveau.

Op basis hiervan kunnen Relying Party toepassingen en portals, afhankelijk van hun context, Azure AD B2C aangepaste beleids regels aanroepen die gebruikmaken van het Framework voor identiteits ervaring waarbij de naam van een specifiek beleid wordt door gegeven en precies het gedrag en de uitwisseling van informatie te verkrijgen die ze nodig hebben zonder muss, Fuss of risico.
