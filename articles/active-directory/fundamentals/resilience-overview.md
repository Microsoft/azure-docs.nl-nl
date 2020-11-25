---
title: Flexibele identiteits-en toegangs beheer met Azure Active Directory
description: Een hand leiding voor architecten, IT-beheerders en ontwikkel aars bij het bouwen van de flexibiliteit om hun identiteits systemen te verstoren.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7dfd51b0ed43badbc6a4882f619cb718952b0e85
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919562"
---
# <a name="building-resilience-into-identity-and-access-management-with-azure-active-directory"></a>Inzoomen op identiteits-en toegangs beheer met Azure Active Directory

Identiteits-en toegangs beheer (IAM) is een raam werk voor processen, beleids regels en technologieën die het beheer van identiteiten en de toegang tot de gegevens mogelijk maken. Het bevat de vele onderdelen die de verificatie en autorisatie van gebruikers en andere accounts in uw systeem ondersteunen.

IAM-tolerantie is de mogelijkheid om de systeem onderdelen te onderbreken en te herstellen met minimale gevolgen voor uw bedrijf, gebruikers, klanten en bewerkingen. Het verminderen van afhankelijkheden, complexiteit en eenmalige uitbrei ding van fouten, waardoor uw tolerantie wordt verbeterd.

Onderbrekingen kunnen afkomstig zijn van elk onderdeel van uw IAM-systemen. Als u een robuuste IAM-systeem wilt bouwen, moet u nagaan of er storingen optreden en deze plannen. 

Houd rekening met de volgende elementen bij het plannen van de flexibiliteit van uw IAM-oplossing: 

* Uw toepassingen die afhankelijk zijn van uw IAM-systeem.

* De open bare infra structuur die uw verificatie aanroept, met inbegrip van telecom bedrijven, Internet serviceproviders en open bare-sleutel providers.

* Uw Cloud-en on-premises id-providers.

* Andere services die afhankelijk zijn van uw IAM en de Api's waarmee ze zijn verbonden.

* Alle andere on-premises onderdelen in uw systeem.

Het is belang rijk dat de bron, het herkennen en de planning voor de onvoorziene gebeurtenissen is. Het toevoegen van aanvullende identiteits systemen, en de resulterende afhankelijkheden en complexiteit, kan uw tolerantie echter verlagen in plaats van deze te verhogen.

Raadpleeg de volgende artikelen als u meer flexibiliteit in uw systemen wilt maken:

* [Flexibiliteit in uw IAM-infra structuur maken](resilience-in-infrastructure.md)

* [IAM-tolerantie bouwen in uw toepassingen](resilience-app-development-overview.md)

* [Maak flexibiliteit in uw CIAM-systemen](resilience-b2c.md)
