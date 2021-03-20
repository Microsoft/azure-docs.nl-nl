---
title: Subtrajecten in Azure Active Directory B2C | Microsoft Docs
description: Geef het element subtrajecten van een aangepast beleid op in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8f037d4283b4b05081ef47e7223495f6e19d460e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97386864"
---
# <a name="sub-journeys"></a>Subtrajecten

Subtrajecten kunnen worden gebruikt om de stroom van Orchestration-stappen binnen een gebruikers traject te organiseren en te vereenvoudigen. Met [gebruikersbelevingen](userjourneys.md) worden expliciete paden opgegeven waarmee een op claims gebaseerde toepassing de gewenste claims voor een gebruiker kan verkrijgen. De gebruiker wordt door deze paden geleid om de claims op te halen die aan de Relying Party moeten worden gepresenteerd. Met andere woorden, de gebruikers trajecten definiëren de bedrijfs logica van wat een eind gebruiker doorloopt als het Azure AD B2C identiteits ervarings raamwerk de aanvraag verwerkt. Een gebruikers traject wordt weer gegeven als een indelings reeks die moet worden gevolgd door een geslaagde trans actie. Het [ClaimsExchange](userjourneys.md#claimsexchanges) -element van een Orchestration-stap is gekoppeld aan één [technisch profiel](technicalprofiles.md) dat wordt uitgevoerd.

Een subtraject is een groepering van Orchestration-stappen die op elk punt binnen een gebruikers traject kan worden aangeroepen. U kunt subtrajecten gebruiken om herbruikbare stap reeksen te maken of vertakkingen te implementeren om de bedrijfs logica beter weer te geven.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="user-journey-branching"></a>Gebruikers reis vertakking

Subtrajecten gedragen zich als [gebruikers ritten](userjourneys.md), aangezien beide worden weer gegeven als een indelings reeks die moet worden gevolgd voor een geslaagde trans actie. Gebruikers ritten kunnen zelf worden opgeroepen en vereisen dat een SendClaims-stap wordt uitgevoerd. Subritten zijn onderdelen van gebruikers reizen en kunnen niet onafhankelijk worden aangeroepen en worden altijd aangeroepen vanuit een gebruikers traject.

Het belangrijkste onderdeel van vertakkingen is het toestaan van een betere bedrijfs logica voor de verwerking van een gebruiker. Algemene Orchestration-stappen worden gegroepeerd in afzonderlijke onderdelen die afzonderlijk moeten worden aangeroepen. Een subtraject kan een traject vereenvoudigen waarbij meerdere Orchestration-stappen samen worden gekoppeld (met dezelfde voor waarden). Een subtraject wordt alleen aangeroepen vanuit het traject van een gebruiker, maar mag geen andere subtraject aanroepen.

Er zijn twee typen subtrajecten:

- **Call** : retourneert het besturings element aan de aanroeper. De subtraject wordt uitgevoerd en vervolgens wordt het besturings element teruggestuurd naar de Orchestration-stap die momenteel wordt uitgevoerd binnen de gebruikers traject.
- **Overdracht** : verstuurt de controle naar de subtraject (onomkeerbare vertakkingen). De subtraject moet een SendClaims stap hebben om de claims terug te sturen naar de Relying Party toepassing.

## <a name="example-scenarios"></a>Voorbeeldscenario's

### <a name="call-sub-journey"></a>Subtraject aanroepen

Een subtraject voor een aanroep is handig in de volgende scenario's:

- Leeftijds beperking: voor de leeftijds-beperking zijn er veel gedeelde onderdelen tussen de gebruikers ritten. Vertakkingen kunnen de algemene elementen in deel bare onderdelen compileren.  
- Ouderlijke toestemming: vertakkingen maakt het mogelijk om in het ontwerp van de ouderlijke toestemming toegang te krijgen tot claims van de gebruiker die de kleine uitvoering hebben uitgevoerd, samen met de mogelijkheid om een gebruikers traject voor een ouderlijke toestemming te bemachtigen na het vinden van de gebruiker toestemming te geven. 
- Meld u aan om u aan te melden: overweeg een scenario waarin een gebruiker al in de directory bestaat, maar mogelijk is verg eten dat er een account is gemaakt. Het kan wenselijk zijn om te voor komen dat de gebruiker die de referenties die ze hebben ingevoerd, al bestaat en dat de gebruiker afdwingt de rit opnieuw op te starten, zodat het beleid een switch kan uitvoeren vanuit een registratie stroom naar een aanmeldings stroom voor die gebruiker.  

### <a name="transfer-sub-journey"></a>Subtraject overdragen

Een overdrachts subtraject is handig in de volgende scenario's:

- Een blok pagina wordt weer gegeven.
- A/B testen door de aanvraag naar een subtraject te routeren om een token uit te voeren en uit te geven.

## <a name="adding-a-subjourneys-element"></a>Een subtraject element toevoegen

Hier volgt een voor beeld van een `SubJourney` -element van het type, dat de besturing terugstuurt `Call` naar de reis van de gebruiker.

```xml
<SubJourneys>
  <SubJourney Id="ConditionalAccess_Evaluation" Type="Call">
    <OrchestrationSteps>
      <OrchestrationStep Order="1" Type="ClaimsExchange">
       <ClaimsExchanges>
        <ClaimsExchange Id="ConditionalAccessEvaluation" TechnicalProfileReferenceId="ConditionalAccessEvaluation" />
       </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="2" Type="ClaimsExchange">
        <Preconditions>
          <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
            <Value>conditionalAccessClaimCollection</Value>
            <Action>SkipThisOrchestrationStep</Action>
          </Precondition>
        </Preconditions>
        <ClaimsExchanges>
          <ClaimsExchange Id="GenerateCAClaimFlags" TechnicalProfileReferenceId="GenerateCAClaimFlags" />
        </ClaimsExchanges>
      </OrchestrationStep>
    </OrchestrationSteps>
  </SubJourney>
</SubJourneys>
```

Hier volgt een voor beeld van een `SubJourney` -element van het type `Transfer` , dat een token terugstuurt naar de Relying Party-toepassing.

```xml
<SubJourneys>
  <SubJourney Id="B" Type="Transfer">
    <OrchestrationSteps>
      ...
      <OrchestrationStep Order="5" Type="SendClaims">
    </OrchestrationSteps>
  </SubJourney>
</SubJourneys>
```

### <a name="invoke-a-sub-journey-step"></a>Een stap van een subtraject aanroepen

Een nieuwe Orchestration-stap van het type `InvokeSubJourney` wordt gebruikt om een subtraject uit te voeren. Hier volgt een voor beeld waarin alle uitvoerings elementen van deze Orchestration-stap worden weer gegeven.

```xml
<OrchestrationStep Order="5" Type="InvokeSubJourney">
  <JourneyList>
    <Candidate SubJourneyReferenceId="ConditionalAccess_Evaluation" />
  </JourneyList>
</OrchestrationStep>
```

> [!NOTE]
> Een subtraject mag geen andere subtraject aanroepen.

## <a name="components"></a>Onderdelen

Als u de subtrajecten wilt definiëren die door het beleid worden ondersteund, voegt u een **subtraject** element toe onder het element op het hoogste niveau van het beleids bestand.

Het element **Subtrajecten** bevat het volgende element:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| Subtraject | 1: n | Een subtraject waarmee alle benodigde constructies voor een volledige gebruikers stroom worden gedefinieerd. |

Het element **Subtrajecten** bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Id | Ja | De subtraject-id die door de gebruiker kan worden gebruikt om te verwijzen naar de subtraject in het beleid. Het **SubJourneyReferenceId** -element van het element [kandidaat](userjourneys.md#journeylist) wijst naar dit kenmerk. |
| Type | Ja | Mogelijke waarden: `Call` , of `Transfer` . Zie voor meer informatie [gebruikers reis vertakking](#user-journey-branching)|

Het element **subtraject** bevat het volgende element:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| OrchestrationSteps | 1: n | Een indelings reeks die moet worden gevolgd voor een geslaagde trans actie. Elke gebruikers traject bestaat uit een geordende lijst met Orchestration-stappen die in volg orde worden uitgevoerd. Als er een stap mislukt, mislukt de trans actie. |

## <a name="orchestrationsteps"></a>OrchestrationSteps

Zie [UserJourneys](userjourneys.md)voor een volledige lijst met elementen van de Orchestration-stap.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [UserJourneys](userjourneys.md)