---
title: Verhoog de flexibiliteit van verificatie en autorisatie in de daemon-toepassingen die u ontwikkelt
titleSuffix: Microsoft identity platform
description: Richt lijnen voor het verg Roten van de tolerantie van verificatie en autorisatie in een daemon-toepassing met behulp van het micro soft Identity platform
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: 74bfc9eeeb8375fca2c88a3fd3c31f17e130fc99
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919625"
---
# <a name="increase-the-resilience-of-authentication-and-authorization-in-daemon-applications-you-develop"></a>Verhoog de flexibiliteit van verificatie en autorisatie in de daemon-toepassingen die u ontwikkelt

Dit artikel bevat richt lijnen voor het gebruik van het micro soft-identiteits platform en Azure Active Directory om de flexibiliteit van daemon-toepassingen te verg Roten. Dit omvat achtergrond processen, services, servers voor server-apps en toepassingen zonder gebruikers.

![Een daemon-toepassing die een aanroep naar micro soft Identity maakt](media/resilience-daemon-app/calling-microsoft-identity.png)

## <a name="use-managed-identities-for-azure-resources"></a>Beheerde identiteiten gebruiken voor Azure-resources

Ontwikkel aars die daemon-apps bouwen op Microsoft Azure kunnen [beheerde identiteiten voor Azure-resources](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)gebruiken. Beheerde identiteiten voor komen dat ontwikkel aars geheimen en referenties hoeven te beheren. De functie verbetert de flexibiliteit door fouten bij het verlopen van certificaten, rotatie fouten of vertrouwen te vermijden. Het bevat ook verschillende ingebouwde functies die specifiek zijn ontworpen om de flexibiliteit te verg Roten.

Beheerde identiteiten gebruiken lange toegangs tokens en gegevens van micro soft-identiteit om proactief nieuwe tokens te verkrijgen binnen een groot tijd venster voordat het bestaande token verloopt. Uw app kan blijven worden uitgevoerd tijdens het verkrijgen van een nieuw token.

Beheerde identiteiten gebruiken ook regionale eind punten om de prestaties en flexibiliteit te verbeteren ten opzichte van out-of-Region storingen. Het gebruik van een regionaal eind punt helpt al het verkeer binnen een geografisch gebied te blijven. Als uw Azure-resource bijvoorbeeld in WestUS2 is, moet al het verkeer, inclusief door micro soft Identity gegenereerde verkeer, in WestUS2 blijven. Dit elimineert mogelijke fout punten door de afhankelijkheden van uw service samen te voegen.

## <a name="use-the-microsoft-authentication-library"></a>De micro soft-verificatie bibliotheek gebruiken

Ontwikkel aars van daemon-apps die geen beheerde identiteiten gebruiken, kunnen gebruikmaken van de [micro soft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview), waardoor verificatie en autorisatie eenvoudig kunnen worden geïmplementeerd en automatisch aanbevolen procedures voor tolerantie worden gebruikt. MSAL maakt het proces van het leveren van de vereiste client referenties eenvoudiger. Uw toepassing hoeft bijvoorbeeld geen JSON Web Token asserties voor het maken en ondertekenen te implementeren bij gebruik van referenties op basis van certificaten.

### <a name="use-microsoftidentityweb-for-net-developers"></a>Micro soft. Identity. web voor .NET-ontwikkel aars gebruiken

Ontwikkel aars die daemon-apps bouwen op ASP.NET Core kunnen gebruikmaken van de [micro soft. Identity. Web](https://docs.microsoft.com/azure/active-directory/develop/microsoft-identity-web) -bibliotheek. Deze tape wisselaar is gebaseerd op MSAL om de implementatie van autorisaties nog eenvoudiger te maken voor ASP.NET Core-Apps. Het bevat verschillende strategieën voor [gedistribueerde token cache](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization#distributed-token-cache) voor gedistribueerde apps die in meerdere regio's kunnen worden uitgevoerd.

## <a name="cache-and-store-tokens"></a>Tokens in de cache opslaan en bewaren

Als u geen gebruik maakt van MSAL om verificatie en autorisatie te implementeren, kunt u aanbevolen procedures implementeren voor het opslaan in cache en het opslaan van tokens. MSAL implementeert en volgt deze best practices automatisch.

Een toepassing verkrijgt tokens van een id-provider om de toepassing te autoriseren om beveiligde Api's aan te roepen. Wanneer uw app tokens ontvangt, bevat het antwoord dat de tokens bevat ook een "expires \_ "-eigenschap die aangeeft hoe lang het cache geheugen moet worden opgeslagen en hoe het token opnieuw kan worden gebruikt. Het is belang rijk dat toepassingen de eigenschap Expires gebruiken \_ om de levens duur van het token te bepalen. De toepassing moet nooit proberen een API-toegangs token te decoderen. Het gebruik van het token in de cache voor komt onnodig verkeer tussen uw app en micro soft-identiteit. Uw gebruiker kan aangemeld blijven bij uw toepassing voor de lengte van de levens duur van dat token.

## <a name="properly-handle-service-responses"></a>Service Reacties correct afhandelen

Ten slotte, terwijl toepassingen alle fout reacties moeten afhandelen, zijn er enkele reacties die van invloed kunnen zijn op de tolerantie. Als uw toepassing een HTTP 429-respons code ontvangt, worden er te veel aanvragen van micro soft-identiteit voor uw aanvragen beperkt. Als uw app te veel aanvragen blijft uitvoeren, wordt er nog steeds beperkt om te voor komen dat uw app tokens ontvangt. Uw toepassing mag niet opnieuw proberen om een token te verkrijgen tot na de tijd, in seconden, in het antwoord veld ' opnieuw proberen na ' is verstreken. Het ontvangen van een 429-antwoord is vaak een indicatie dat de toepassing niet in de cache opslaat en tokens correct opnieuw gebruikt. Ontwikkel aars moeten zien hoe tokens in de cache worden opgeslagen en opnieuw worden gebruikt in de toepassing.

Wanneer een toepassing een HTTP 5xx-respons code ontvangt, mag de app geen snelle herhalings lus invoeren. Wanneer dit het geval is, moet de toepassing voldoen aan dezelfde ' nieuwe poging na ' voor een reactie van 429. Als er geen header ' nieuwe poging na ' wordt gegeven door de reactie, raden we u aan een exponentiële back-up te implementeren met de eerste poging ten minste 5 seconden na het antwoord.

Wanneer een aanvraag een time-out optreedt voor toepassingen, moet de toepassing niet direct opnieuw proberen. Implementeer een exponentiële back-uit met de eerste poging ten minste 5 seconden na het antwoord.

## <a name="next-steps"></a>Volgende stappen

- [Ontwikkel flexibiliteit in toepassingen die gebruikers aanmelden](resilience-client-app.md)
- [Maak flexibiliteit in uw infra structuur voor identiteits-en toegangs beheer](resilience-in-infrastructure.md)
- [Maak flexibiliteit in uw CIAM-systemen](resilience-b2c.md)
