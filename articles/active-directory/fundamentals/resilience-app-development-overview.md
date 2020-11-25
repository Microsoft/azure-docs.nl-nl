---
title: Verbeter de flexibiliteit van verificatie-en autorisatie toepassingen die u ontwikkelt
titleSuffix: Microsoft identity platform
description: Overzicht van onze flexibiliteits richtlijnen voor het ontwikkelen van toepassingen met behulp van Azure Active Directory en het micro soft Identity-platform
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: c2c2f9d0ad7bfa50f543b57326b9fc8dab0069c6
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96029299"
---
# <a name="increase-resilience-of-authentication-and-authorization-applications-you-develop"></a>Verbeter de flexibiliteit van verificatie-en autorisatie toepassingen die u ontwikkelt

Micro soft Identity maakt gebruik van moderne verificatie en autorisatie op basis van tokens. Dit betekent dat een toepassing tokens van een id-provider verkrijgt om de gebruiker te verifiëren en om de toepassing te autoriseren om beveiligde Api's aan te roepen.

Een token is geldig gedurende een bepaalde periode voordat de app een nieuwe moet verkrijgen. Een aanroep voor het ophalen van een token kan echter niet worden uitgevoerd vanwege een probleem, zoals een netwerk-of infrastructuur fout of een storing in de verificatie service. In dit document worden de stappen beschreven die een ontwikkelaar kan ondernemen om de flexibiliteit in hun toepassingen te verhogen als er een fout optreedt bij het ophalen van een token.

Deze artikelen bieden richt lijnen voor het verg Roten van tolerantie in apps met behulp van het micro soft Identity platform en de Azure Active Directory. Er zijn richt lijnen voor beide client toepassingen die namens een aangemelde gebruiker werken, evenals daemon-toepassingen die aan hun eigen naam werken. Ze bevatten aanbevolen procedures voor het gebruik van tokens en het aanroepen van bronnen.

- [Ontwikkel flexibiliteit in toepassingen die gebruikers aanmelden](resilience-client-app.md)
- [Ontwikkel flexibiliteit in toepassingen zonder gebruikers](resilience-daemon-app.md)
- [Maak flexibiliteit in uw infra structuur voor identiteits-en toegangs beheer](resilience-in-infrastructure.md)
- [Ontwikkel flexibiliteit in uw klant identiteits-en toegangs beheer met Azure Active Directory B2C](resilience-b2c.md)
