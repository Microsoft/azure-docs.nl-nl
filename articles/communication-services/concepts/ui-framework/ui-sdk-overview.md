---
title: Overzicht van het Framework van Azure Communication Services UI
titleSuffix: An Azure Communication Services conceptual document
description: Meer informatie over het gebruikers interface-Framework van Azure Communication Services
author: ddematheu2
ms.author: dademath
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: b6f9987c21ecdcf0e1b5358486312dceb26c8b82
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/09/2021
ms.locfileid: "100008550"
---
# <a name="azure-communication-services-ui-framework"></a>Gebruikers interface-Framework van Azure Communication Services

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

:::image type="content" source="../media/ui-framework/pre-and-custom-composite.png" alt-text="Vergelijking tussen vooraf ontwikkelde en aangepaste samen stellingen":::

Met het gebruikers interface raamwerk van Azure Communication Services kunt u eenvoudig moderne communicatie gebruikers ervaringen bouwen. Het biedt u een bibliotheek met voor productie geschikte UI-componenten die u in uw toepassingen kunt neerzetten:

- **Samengestelde onderdelen** : deze onderdelen zijn de belangrijkste oplossingen voor het implementeren van algemene communicatie scenario's. U kunt snel video gesprekken of chat ervaringen toevoegen aan hun toepassingen. Samen stellingen zijn open source-onderdelen die zijn gebouwd met basis onderdelen.
- **Basis onderdelen** : deze onderdelen zijn open-source bouw stenen waarmee u een aangepaste communicatie-ervaring kunt bouwen. Onderdelen worden aangeboden voor zowel oproep-als chat mogelijkheden die kunnen worden gecombineerd om ervaring te bouwen. 

Deze UI-client bibliotheken gebruiken allemaal [de Fluent-ontwerp taal en-assets van micro soft](https://developer.microsoft.com/fluentui/) . De Fluent-gebruikers interface biedt een basis-laag voor het UI-Framework dat is getest over micro soft-producten.

## <a name="differentiating-components-and-composites"></a>**Onderscheidende onderdelen en samen stellingen**

**Basis onderdelen** zijn gebouwd op basis van de belangrijkste Azure Communication Services-client bibliotheken en implementeren basis acties, zoals het initialiseren van de kern-client Bibliotheken, het renderen van video en het leveren van gebruikers besturings elementen voor dempen, video aan/uit, enzovoort. U kunt deze **basis onderdelen** gebruiken om uw eigen aangepaste lay-outervaringen te bouwen met vooraf ontwikkelde, productie-Ready-communicatie onderdelen.

:::image type="content" source="../media/ui-framework/component-overview.png" alt-text="Overzicht van onderdeel voor UI-Framework":::

**Samengestelde onderdelen** combi neren meerdere **basis onderdelen** voor het maken van meer volledige communicatie-ervaringen. Deze onderdelen van een hoger niveau kunnen eenvoudig worden geïntegreerd in een bestaande app om een volledig fledge communicatie-ervaring te verwijderen zonder dat u de taak zelf zelf bouwt. Ontwikkel aars kunnen zich concentreren op het bouwen van de omringende ervaring en flow die ze nodig hebben in hun apps en de communicatie complexiteit voor samengestelde onderdelen te behouden.

:::image type="content" source="../media/ui-framework/composite-overview.png" alt-text="Overzicht van Composite voor UI Framework":::

## <a name="what-ui-framework-is-best-for-my-project"></a>Welk UI-Framework is het meest geschikt voor mijn project?

Als u deze vereisten begrijpt, kunt u de juiste client bibliotheek kiezen:

- **Hoeveel aanpassingen wilt u uitvoeren?** Azure Communication core client-bibliotheken hebben geen UX en zijn zo ontworpen dat u elke gewenste UX kunt bouwen. UI-Framework onderdelen bieden UI-elementen tegen de kosten van verminderde aanpassing.
- **Hebt u Vergader functies nodig?** Het Vergader systeem heeft verschillende unieke mogelijkheden die momenteel niet beschikbaar zijn in de basis-client bibliotheken van Azure Communication Services, zoals vage achtergrond en verhoogde hand.
- **Op welke platformen wordt u gestreefd?** Verschillende platforms hebben verschillende mogelijkheden.

Meer informatie over de beschik baarheid van functies in de diverse Sdk's van de [gebruikers interface is hier beschikbaar](ui-sdk-features.md), maar de sleutel vermogens worden hieronder weer gegeven.

|Client bibliotheek/SDK|Implementatie complexiteit|    Aanpassings mogelijkheden|  Aanroepen| Chat| [Teams Interop](./../teams-interop.md)
|---|---|---|---|---|---|---|
|Samengestelde onderdelen|Beperkt|Beperkt|✔|✔|✕
|Basis onderdelen|Gemiddeld|Gemiddeld|✔|✔|✕
|Basis-client bibliotheken|Hoog|Hoog|✔|✔ |✔

## <a name="cost"></a>Kosten

Het gebruik van Azure UI-Frameworks heeft geen extra Azure-kosten of-meting. U betaalt alleen voor het gebruik van de onderliggende service, met behulp van dezelfde aanroep-, chat-en PSTN-meters.

## <a name="supported-use-cases"></a>Ondersteunde use cases

Gesp

- Deelname aan Azure Communication Services-oproep met groeps-ID

Chat

- Meld u aan bij Azure Communication Services chat met de thread-ID

## <a name="supported-identities"></a>Ondersteunde identiteiten:

Er is een Azure Communication Services-identiteit vereist om het UI-Framework te initialiseren en te verifiëren bij de service. Zie [verificatie](../authentication.md) -en [toegangs tokens](../../quickstarts/access-tokens.md) voor meer informatie over verificatie.


## <a name="recommended-architecture"></a>Aanbevolen architectuur 

:::image type="content" source="../media/ui-framework/framework-architecture.png" alt-text="Aanbevolen architectuur voor UI-Framework met client-server architectuur ":::

Samengestelde en basis onderdelen worden geïnitialiseerd met behulp van een toegangs token van Azure Communication Services. Toegangs tokens moeten worden aangeschaft via Azure Communication Services via een vertrouwde service die u beheert. Zie [Quick Start: toegangs tokens](../../quickstarts/access-tokens.md) en de [zelf studie voor een vertrouwde service](../../tutorials/trusted-service-tutorial.md) maken voor meer informatie.

Deze client bibliotheken vereisen ook de context voor de oproep of de chat die wordt toegevoegd. Net als bij gebruikers toegangs tokens, moet deze context worden verspreid naar clients via uw eigen vertrouwde service. De onderstaande lijst bevat een overzicht van de functies voor initialisatie en bron beheer die u nodig hebt om te operationeel makenen.

| Contoso-verantwoordelijkheden                                 | Verantwoordelijkheden van UI-Framework                         |
|----------------------------------------------------------|-----------------------------------------------------------------|
| Toegangs token van Azure opgeven                    | Door gegeven toegangs token door geven om onderdelen te initialiseren        |
| Functie voor vernieuwen opgeven                                 | Het toegangs token vernieuwen met behulp van de functie voor ontwikkel aars          |
| Gegevens ophalen/samen voegen voor oproep of chat          | Oproep-en chat gegevens door geven om onderdelen te initialiseren |
| Gebruikers gegevens ophalen/door geven voor elk aangepast gegevens model | Aangepast gegevens model door geven aan onderdelen om weer te geven          |
