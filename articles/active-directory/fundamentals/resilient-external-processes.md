---
title: Robuuste interfaces met externe processen met behulp van Azure AD B2C | Microsoft Docs
description: Methoden voor het bouwen van robuuste interfaces met externe processen
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58ef522f5b048db0ef120625d9e894c8e14c070e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98724404"
---
# <a name="resilient-interfaces-with-external-processes"></a>Robuuste interfaces met externe processen

In dit artikel bieden we u richt lijnen voor het plannen en implementeren van de REST-Api's in de gebruikers reis en het maken van uw toepassing met meerwaarde van API-fouten.

![Afbeelding toont de interfaces met externe proces onderdelen](media/resilient-external-processes/external-processes-architecture.png)

## <a name="ensure-correct-placement-of-the-apis"></a>De juiste plaatsing van de Api's garanderen

Met beleid voor identiteits ervaring (IEF) kunt u een extern systeem aanroepen met behulp van een [rest API-technisch profiel](../../active-directory-b2c/restful-technical-profile.md). Externe systemen worden niet beheerd door de IEF runtime-omgeving en vormen een potentieel fout punt.

### <a name="how-to-manage-external-systems-using-apis"></a>Externe systemen beheren met Api's

- Tijdens het aanroepen van een interface om toegang te krijgen tot bepaalde gegevens, controleert u of de gegevens de verificatie beslissing zullen nemen. Bepaal of de informatie essentieel is voor de kern functionaliteit van de toepassing. Bijvoorbeeld een e-commerce versus een secundaire functionaliteit zoals een beheer. Als de informatie niet nodig is voor verificatie en alleen vereist is voor secundaire scenario's, kunt u de aanroep naar de toepassings logica verplaatsen.

- Als de gegevens die nodig zijn voor authenticatie relatief statisch en klein zijn, en er geen andere zakelijke reden is om extern te worden uitgevoerd vanuit de map, kunt u overwegen deze in de Directory te houden.

- Verwijder, indien mogelijk, API-aanroepen van het vooraf geverifieerde pad. Als dat niet het geval is, moet u strikte beveiligingen voor denial of service (DoS) en DDoS-aanvallen (Distributed Denial of service) voor uw Api's. Aanvallers kunnen de aanmeldings pagina laden en proberen uw API te laten overlopen met DoS-aanvallen en Cripple uw toepassing. Als u bijvoorbeeld CAPTCHA gebruikt in uw aanmelding, kan de registratie stroom helpen.

- Gebruik waar mogelijk [API-connectors van ingebouwde gebruikers stroom](../../active-directory-b2c/api-connectors-overview.md) om te integreren met Web-api's nadat u zich hebt aangemeld met een id-provider of voordat u de gebruiker hebt gemaakt. Omdat de gebruikers stromen al uitgebreid zijn getest, is het waarschijnlijk dat u geen functionele, prestatie-of schaal tests voor de gebruikers stroom kunt uitvoeren. U moet uw toepassingen nog steeds testen op functionaliteit, prestaties en schaal.

- [Technische profielen](../../active-directory-b2c/restful-technical-profile.md) voor de rest API van Azure AD bieden geen cache gedrag. In plaats daarvan implementeert het profiel van de REST-API een pogings logica en een time-out die in het beleid is ingebouwd.

- Voor Api's waarvoor gegevens moeten worden geschreven, moet u een taak in de wachtrij plaatsen om deze taken uit te voeren door een achtergrond medewerker. Services zoals [Azure queues](../../storage/queues/storage-queues-introduction.md) kunnen worden gebruikt. Hierdoor is de API efficiënt te verhogen van de prestaties van het beleid.  

## <a name="api-error-handling"></a>API-fout afhandeling

Als de Api's zich buiten het Azure AD B2C systeem bevinden, is het nood zakelijk om de juiste fout afhandeling binnen het technische profiel te hebben. Zorg ervoor dat de eind gebruiker op de juiste wijze op de hoogte wordt gebracht en dat de toepassing probleemloos kan omgaan.

### <a name="how-to-gracefully-handle-api-errors"></a>API-fouten zonder problemen afhandelen

- Een API kan om verschillende redenen mislukken, waardoor uw toepassing flexibeler is voor dergelijke storingen. [Een http 4xx-fout bericht retour neren](../../active-directory-b2c/restful-technical-profile.md#returning-validation-error-message) als de API de aanvraag niet kan volt ooien. Probeer in het Azure AD B2C-beleid de niet-beschik baarheid van de API zonder problemen af te handelen en mogelijk een gereduceerde ervaring te geven.

- [Tijdelijke fouten](../../active-directory-b2c/restful-technical-profile.md#error-handling)op de juiste manier verwerken. Met het profiel voor de resterende API kunt u fout berichten configureren voor verschillende [circuit onderbrekers](/azure/architecture/patterns/circuit-breaker).

- Proactief bewaak en gebruik continue integratie/continue levering (CICD), roteer de API-toegangs referenties, zoals wacht woorden en certificaten die worden gebruikt door de [technische profiel engine](../../active-directory-b2c/restful-technical-profile.md).

## <a name="api-management---best-practices"></a>API management-best practices

Tijdens het implementeren van de REST-Api's en het configureren van het onderliggend technische profiel, kunt u de aanbevolen procedures volgen om te voor komen dat u veelvoorkomende fouten en dingen die niet worden weer geven.

### <a name="how-to-manage-apis"></a>Api's beheren

- API Management (APIM) publiceert, beheert en analyseert uw Api's. APIM verwerkt ook verificatie voor beveiligde toegang tot back-end-services en micro Services. Gebruik een API-gateway om API-implementaties, caching en taak verdeling uit te schalen.

- Aanbeveling is om het juiste token aan het begin van de gebruikers traject op te halen in plaats van meerdere keren voor elke API te bellen en [een Azure APIM-API te beveiligen](../../active-directory-b2c/secure-api-management.md?tabs=app-reg-ga).

## <a name="next-steps"></a>Volgende stappen

- [Flexibiliteits bronnen voor Azure AD B2C-ontwikkel aars](resilience-b2c.md)
  - [Flexibele ervaring voor eind gebruikers](resilient-end-user-experience.md)
  - [Flexibiliteit door middel van aanbevolen procedures voor ontwikkel aars](resilience-b2c-developer-best-practices.md)
  - [Flexibiliteit door middel van bewaking en analyse](resilience-with-monitoring-alerting.md)
- [Flexibiliteit in uw verificatie-infra structuur maken](resilience-in-infrastructure.md)
- [Verhoog de flexibiliteit van verificatie en autorisatie in uw toepassingen](resilience-app-development-overview.md)