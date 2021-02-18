---
title: BuildingBlocks
titleSuffix: Azure AD B2C
description: Specificeer het element BuildingBlocks van een aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a42c1d06051c283f0e911c4cd166884ddd060f45
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100633271"
---
# <a name="buildingblocks"></a>BuildingBlocks

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Het element **BuildingBlocks** wordt toegevoegd aan het element [TrustFrameworkPolicy](trustframeworkpolicy.md) .

```xml
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="mytenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">

  <BuildingBlocks>
    <ClaimsSchema>
      ...
    </ClaimsSchema>
    <Predicates>
    ...
    </Predicates>
    <PredicateValidations>
    ...
    </PredicateValidations>
    <ClaimsTransformations>
      ...
    </ClaimsTransformations>
    <ContentDefinitions>
      ...
    </ContentDefinitions>
    <Localization>
      ...
    </Localization>
    <DisplayControls>
      ...
    </DisplayControls>
 </BuildingBlocks>
```

Het element **BuildingBlocks** bevat de volgende elementen die moeten worden opgegeven in de gedefinieerde volg orde:

- [ClaimsSchema](claimsschema.md) : definieert de claim typen waarnaar kan worden verwezen als onderdeel van het beleid. Het claim schema is de plaats waar u uw claim typen declareert. Een claim type is vergelijkbaar met een variabele in veel programmeer talen. U kunt het claim type gebruiken om gegevens te verzamelen van de gebruiker van uw toepassing, claims van sociale id-providers te ontvangen, gegevens te verzenden en te ontvangen van een aangepaste REST API, of interne gegevens op te slaan die door uw aangepaste beleid worden gebruikt.

- [Predikaten en PredicateValidationsInput](predicates.md) : Hiermee kunt u een validatie proces uitvoeren om ervoor te zorgen dat alleen goed gevormde gegevens in een claim worden ingevoerd.

- [ClaimsTransformations](claimstransformations.md) -bevat een lijst met claim transformaties die kunnen worden gebruikt in uw beleid.  Een claim transformatie zet een claim om in een andere. In de claim transformatie geeft u een transformatie methode op, zoals:
  - Het hoofdletter gebruik van een teken reeks claim wijzigen naar de waarde die is opgegeven. U kunt bijvoorbeeld een teken reeks wijzigen van kleine letters in hoofd letters.
  - Vergelijken van twee claims en het retour neren van een claim met de waarde True, wat aangeeft dat de claims overeenkomen, anders false.
  - Er wordt een teken reeks claim gemaakt op basis van de gegeven para meter in het beleid.
  - Een wille keurige teken reeks maken met de generator voor wille keurige getallen.
  - Het format teren van een claim volgens de gegeven teken reeks voor opmaak. Deze trans formatie maakt gebruik van de C#- `String.Format` methode.

- InputValidation: met dit element kunt u Booleaanse aggregaties uitvoeren die vergelijkbaar zijn met *en* en *of*.

- [ContentDefinitions](contentdefinitions.md) : bevat URL'S voor HTML5-sjablonen die u in uw gebruikers traject kunt gebruiken. In een aangepast beleid definieert een inhouds definitie de HTML5-pagina-URI die wordt gebruikt voor een opgegeven stap in het traject van de gebruiker. Bijvoorbeeld het aanmelden of aanmelden, het opnieuw instellen van het wacht woord of de fout pagina's. U kunt het uiterlijk wijzigen door de LoadUri voor het HTML5-bestand te overschrijven. U kunt ook nieuwe inhouds definities maken op basis van uw behoeften. Dit element kan een gelokaliseerde bronnen verwijzing bevatten die gebruikmaakt van een lokalisatie-ID.

- [Lokalisatie](localization.md) : biedt ondersteuning voor meerdere talen. Met de lokalisatie ondersteuning in beleids regels kunt u de lijst met ondersteunde talen in een beleid instellen en een standaard taal kiezen. Taalspecifieke teken reeksen en verzamelingen worden ook ondersteund.

- [DisplayControls](display-controls.md) : definieert de besturings elementen die op een pagina moeten worden weer gegeven. Besturings elementen voor weer gave hebben speciale functionaliteit en werken met technische profielen voor validatie van de back-end. 
