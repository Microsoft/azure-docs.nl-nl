---
title: Ontwikkel flexibiliteit in identiteits-en toegangs beheer van klanten met behulp van Azure AD B2C | Microsoft Docs
description: Methoden voor het maken van flexibiliteit in identiteits-en toegangs beheer van klanten met behulp van Azure AD B2C
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
ms.openlocfilehash: b40918db03c260f899c36d306c892b787cc6371c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724888"
---
# <a name="build-resilience-in-your-customer-identity-and-access-management-with-azure-active-directory-b2c"></a>Ontwikkel flexibiliteit in uw klant identiteits-en toegangs beheer met Azure Active Directory B2C

[Azure Active Directory (AD) B2C](../../active-directory-b2c/overview.md) is een CIAM-platform (Customer Identity and Access Management) dat is ontworpen om u te helpen bij het starten van uw essentiële klant gerichte toepassingen. We hebben veel ingebouwde functies voor [flexibiliteit](https://azure.microsoft.com/blog/advancing-azure-active-directory-availability/) , die zijn ontworpen om onze service op uw behoeften aan te kunnen schalen en de flexibiliteit te verbeteren in het vlak van mogelijke storings situaties. Wanneer u een essentiële toepassing start, is het belang rijk om verschillende ontwerp-en configuratie-elementen in uw toepassing te overwegen en te bepalen hoe de toepassing wordt geconfigureerd in Azure AD B2C om ervoor te zorgen dat u een flexibel gedrag krijgt in reactie op storingen of fout scenario's. In dit artikel bespreken we enkele van de aanbevolen procedures om u te helpen de flexibiliteit te verg Roten.

Een flexibele dienst is een service die ondanks onderbrekingen blijft functioneren. U kunt de flexibiliteit in uw service helpen verbeteren door:

- meer informatie over de onderdelen

- storingen van afzonderlijke punten elimineren

- het isoleren van mislukte onderdelen om de invloed ervan te beperken

- redundantie bieden met snelle failover-mechanismen en herstel paden

Wanneer u uw toepassing ontwikkelt, raden we u aan om te overwegen hoe u de [tolerantie van verificatie en autorisatie in uw toepassingen kunt verhogen](resilience-app-development-overview.md) met de identiteits onderdelen van uw oplossing. In dit artikel wordt getracht om verbeteringen aan te pakken voor de flexibiliteit die specifiek is voor Azure AD B2C toepassingen. Onze aanbevelingen worden gegroepeerd op CIAM-functies.

![De afbeelding bevat ](media/resilience-b2c/high-level-components.png) de CIAM-onderdelen in de volgende secties. u wordt aangeraden de flexibiliteit te vergren delen:

- [Gebruikers ervaring](resilient-end-user-experience.md): een terugval plan voor uw verificatie stroom inschakelen en de mogelijke gevolgen van een onderbreking van de Azure AD B2C-verificatie service beperken.

- [Interfaces met externe processen](resilient-external-processes.md): Maak flexibiliteit in uw toepassingen en interfaces door te herstellen van fouten.  

- [Best practices voor ontwikkel aars](resilience-b2c-developer-best-practices.md): Vermijd breek tijd door algemene beleids problemen en verbeter de fout afhandeling in de gebieden zoals interacties met claim controleprogramma's, toepassingen van derden en rest api's.

- [Bewaking en analyses](resilience-with-monitoring-alerting.md): Controleer de status van uw service door de belangrijkste indica toren te controleren en fouten te detecteren en de prestaties te verstoren door een waarschuwing te ontvangen.

- [Flexibiliteit in uw verificatie-infra structuur maken](resilience-in-infrastructure.md)

- [Verhoog de flexibiliteit van verificatie en autorisatie in uw toepassingen](resilience-app-development-overview.md)

Bekijk deze video om te weten hoe u met Azure AD B2C robuuste en schaal bare stromen bouwt.
>[!Video https://www.youtube.com/embed/8f_Ozpw9yTs]
