---
title: Acties voor de Azure Front Door-regelengine
description: Dit artikel bevat een overzicht van de verschillende acties die u kunt uitvoeren met de Azure Front Door-regelengine.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2020
ms.author: duau
ms.openlocfilehash: 7b5358ec6bf0f41c860501c70ad175d7cf4bfe97
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106062842"
---
# <a name="azure-front-door-rules-engine-actions"></a>Acties voor de Azure Front Door-regelengine

In de [AFD-regelengine](front-door-rules-engine.md) bestaat een regel uit nul of meer voorwaarden van overeenkomst en acties. In dit artikel vindt u gedetailleerde beschrijvingen van de acties die u kunt gebruiken in de AFD-regelengine.

Een actie definieert het gedrag dat wordt toegepast op het aanvraagtype dat overeenkomt met de voorwaarde of een set voorwaarden van overeenkomst. In de AFD-regelengine kan een regel maximaal vijf acties bevatten. Er kan slechts één actie een actie voor overschrijven van de routeconfiguratie (doorsturen of omleiden) zijn.

De volgende acties zijn beschikbaar voor gebruik in de Azure Front Door-regelengine.  

## <a name="modify-request-header"></a>Aanvraagheader wijzigen

Gebruik deze actie om headers te wijzigen die aanwezig zijn in aanvragen die naar uw herkomst worden verzonden.

### <a name="required-fields"></a>Vereiste velden

Bewerking | HTTP-headernaam | Waarde
-------|------------------|------
Toevoegen | Als deze optie wordt geselecteerd en de regel overeenkomt, wordt de header die wordt opgegeven in **headernaam** aan de aanvraag toegevoegd met de opgegeven waarde. Als er al een header aanwezig is, wordt de waarde toegevoegd aan de bestaande waarde. | Tekenreeks
Overschrijven | Als deze optie is geselecteerd en de regel overeenkomt, wordt de header die wordt opgegeven in **headernaam** aan de aanvraag toegevoegd met de opgegeven waarde. Als er al een header aanwezig is, overschrijft de opgegeven waarde de bestaande waarde. | Tekenreeks
Verwijderen | Als deze optie wordt geselecteerd, de regel overeenkomt en de opgegeven header aanwezig is, wordt de header uit de aanvraag verwijderd. | Tekenreeks

## <a name="modify-response-header"></a>Antwoordheader wijzigen

Gebruik deze actie om headers te wijzigen die aanwezig zijn in antwoorden die aan uw clients worden geretourneerd.

### <a name="required-fields"></a>Vereiste velden

Bewerking | HTTP-headernaam | Waarde
-------|------------------|------
Toevoegen | Als deze optie wordt geselecteerd en de regel overeenkomt, wordt de header die wordt opgegeven in **headernaam** aan het antwoord toegevoegd met de opgegeven **waarde**. Als de header al aanwezig is, wordt de **waarde** toegevoegd aan de bestaande waarde. | Tekenreeks
Overschrijven | Als deze optie is geselecteerd en de regel overeenkomt, wordt de header die is opgegeven in **headernaam** aan het antwoord toegevoegd met de opgegeven **waarde**. Als de header al aanwezig is, overschrijft de **waarde** de bestaande waarde overschrijft. | Tekenreeks
Verwijderen | Als deze optie wordt geselecteerd, de regel overeenkomt en de opgegeven header aanwezig is, wordt de header uit het antwoord verwijderd. | Tekenreeks

## <a name="route-configuration-overrides"></a>Routeconfiguratie-onderdrukkingen 

### <a name="route-type-redirect"></a>Routetype: Omleiden

Gebruik deze actie om clients om te leiden naar een nieuwe URL. 

#### <a name="required-fields"></a>Vereiste velden

Veld | Beschrijving 
------|------------
Omleidingstype | Selecteer het antwoordtype dat u wilt retourneren aan de aanvrager: Gevonden (302), Verplaatst (301), Tijdelijke omleiding (307) en Permanente omleiding (308).
Omleidingsprotocol | Match-aanvraag, HTTP, HTTPS.
Doelhost | Selecteer de hostnaam waarnaar u de aanvraag wilt omleiden. Laat het veld leeg om de binnenkomende host te behouden.
Doelpad | Geef het pad op dat in de omleiding moet worden gebruikt. Laat het veld leeg als u het binnenkomende pad wilt behouden.  
Queryreeksen | Geef de queryreeks op die in de omleiding wordt gebruikt. Laat het veld leeg om de binnenkomende queryreeks te behouden. 
Doelfragment | Geef het fragment op dat in de omleiding moet worden gebruikt. Laat het veld leeg om het binnenkomende fragment te behouden. 


### <a name="route-type-forward"></a>Routetype: Volgende

Gebruik deze actie om clients door te sturen naar een nieuwe URL. Deze actie bevat ook subacties voor het herschrijven van URL'S en caching. 

Veld | Beschrijving 
------|------------
Back-end-pool | Selecteer de back-end-pool om de aanvragen te overschrijven en te verwerken. Hiermee worden ook al uw vooraf geconfigureerde back-end-pools weergegeven in uw Front Door-profiel. 
Doorstuurprotocol | Match-aanvraag, HTTP, HTTPS.
URL opnieuw genereren | Gebruik deze actie om het pad van een aanvraag die naar uw oorsprong wordt doorgestuurd, opnieuw te genereren. Zie hieronder voor aanvullende velden die vereist zijn als deze functie is ingeschakeld
Caching | Ingeschakeld, Uitgeschakeld. Zie hieronder voor aanvullende velden die vereist zijn als deze functie is ingeschakeld. 

#### <a name="url-rewrite"></a>URL opnieuw genereren

Gebruik deze instelling om een ​​optioneel **Aangepast doorstuurpad** te configureren om te gebruiken bij het samenstellen van de aanvraag om door te sturen naar het back-end.

Veld | Beschrijving 
------|------------
Aangepast doorstuurpad | Geef het pad op waarnaar de aanvragen moeten worden doorgestuurd. 

#### <a name="caching"></a>Caching

Gebruik deze instellingen om te bepalen hoe bestanden in de cache worden opgeslagen voor aanvragen die queryreeksen bevatten. Hiermee wordt aangegeven of de inhoud in de cache moet worden opgeslagen op basis van alle parameters of alleen geselecteerde parameters. U kunt aanvullende instellingen gebruiken om de TTL-waarde (time to live) te overschrijven om te bepalen hoe lang inhoud in de cache moet worden bewaard. Als u het opslaan in de cache wilt afdwingen als actie, stelt u het veld Opslaan in cache in op Ingeschakeld. Wanneer u het opslaan in de cache afdwingt, worden de volgende opties weergegeven: 

Cachegedrag |  Beschrijving              
---------------|----------------
Queryreeksen negeren | Zodra de asset in de cache is opgeslagen, negeren alle volgende aanvragen de queryreeksen totdat de in de cache opgeslagen asset verloopt.
Elke unieke URL in de cache opslaan | Elke aanvraag met een unieke URL, inclusief de queryreeks, wordt behandeld als een unieke asset met een eigen cache.
Opgegeven queryreeksen negeren | De queryreeksen van de aanvraag-URL die worden vermeld in de instelling Queryparameters worden genegeerd voor caching.
Opgegeven queryreeksen toevoegen | De queryreeksen van de aanvraag-URL die worden vermeld in de instelling Queryparameters worden gebruikt voor caching.

Aanvullende velden |  Beschrijving 
------------------|---------------
Dynamische compressie | Front Door kan inhoud op de rand dynamisch comprimeren, wat leidt tot een kleiner en sneller antwoord.
Queryparameters | Een door komma's gescheiden lijst met toegestane (of niet-toegestane) parameters die moeten worden gebruikt als basis voor het opslaan in de cache.
Cacheduur | Vervaltijd van de cache in dagen, uren, minuten en seconden. Alle waarden moeten integer zijn. 

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het configureren van uw eerste [regelengine](front-door-tutorial-rules-engine.md). 
- Meer informatie over [voorwaarden voor overeenkomst](front-door-rules-engine-match-conditions.md) van de regelengine
- Meer informatie over de [Azure Front Door-regelengine](front-door-rules-engine.md)
