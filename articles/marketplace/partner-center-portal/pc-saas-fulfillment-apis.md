---
title: SaaS-fulfillment-Api's in de micro soft Commercial Marketplace
description: Een inleiding tot de fulfillment-Api's waarmee u uw SaaS-aanbiedingen in Microsoft AppSource en Azure Marketplace kunt integreren.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/18/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 2e6381afb19018822f6f37171a5ca4b3d929b42e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88037521"
---
# <a name="saas-fulfillment-apis-in-the-microsoft-commercial-marketplace"></a>SaaS-fulfillment-Api's in de micro soft Commercial Marketplace

Met de SaaS-fulfillment-Api's kunnen uitgevers, ook wel onafhankelijke software leveranciers (Isv's), hun SaaS-toepassingen publiceren en verkopen in Microsoft AppSource, Azure Marketplace en Azure Portal. Met deze Api's kunnen ISV-toepassingen deel nemen aan alle commerce ingeschakelde kanalen: directe, partner-LED (wederverkoper) en veld-LED.  Integreren met deze Api's is een vereiste voor het maken en publiceren van een transactable SaaS-aanbieding in het partner centrum.

Isv's moeten de volgende API-stromen implementeren door toe te voegen aan hun SaaS-service code om dezelfde abonnements status voor zowel Isv's als micro soft te behouden:

* Overloop pagina stroom: micro soft waarschuwt de uitgever dat de SaaS-aanbieding van de uitgever is gekocht door een klant in de Marketplace.
* Activerings stroom: Publisher waarschuwt micro soft dat een nieuw ingekochte SaaS-account is geconfigureerd op de kant van de uitgever.
* Update stroom: wijziging van het aangeschafte plan en/of het aantal gekochte seats.
* Stroom onderbreken en opnieuw plaatsen: de aangeschafte SaaS-aanbieding wordt onderbroken als de betalings methode van de klant niet meer geldig is. De schorsings aanbieding kan worden hersteld wanneer het probleem met de betalings methode is opgelost.
* Webhook-stromen: micro soft waarschuwt de uitgever over de wijzigingen van SaaS-abonnementen en de annulering die door de klant van micro soft is geactiveerd.

Voor de annulering van het aangeschafte SaaS-abonnement is integratie optioneel, omdat deze door de klant van micro soft kan worden uitgevoerd.

De juiste integratie met SaaS-fulfillment-Api's is van cruciaal belang om ervoor te zorgen dat

* de eind gebruikers die de SaaS-aanbieding van de uitgever hebben gekocht, worden correct gefactureerd door micro soft.
* de eind klanten krijgen de juiste gebruikers ervaring aan het kopen, configureren, gebruiken en beheren van SaaS-abonnementen die zijn gekocht op Marketplace.

Deze Api's zorgen ervoor dat de aanbiedingen van de uitgever kunnen deel nemen aan alle commerce ingeschakelde kanalen:

* via
* partner-LED (wederverkoper, CSP)
* LED voor veld

In het-scenario van de wederverkoper (CSP), wordt de SaaS-aanbieding door een CSP in rekening worden gekocht namens de eind klant. Een klant verwacht de SaaS-aanbieding te gebruiken, maar de CSP is de entiteit die het volgende doet:

* de klant factureren
* abonnements plannen/aantal aangeschafte seats wijzigen
* de abonnementen annuleren

De uitgever is niet verplicht om een van de API-aanroep stromen anders te implementeren voor dit scenario.

Raadpleeg voor meer informatie over CSP https://partner.microsoft.com/licensing .

>[!Warning]
>De huidige versie van deze API is versie 2, die moet worden gebruikt voor alle nieuwe SaaS-aanbiedingen. Versie 1 van de API is afgeschaft en wordt onderhouden ter ondersteuning van bestaande aanbiedingen.

>[!Note]
>De SaaS-fulfillment-Api's zijn alleen bedoeld om te worden aangeroepen vanuit een back-end-service van de uitgever. Integratie met de Api's rechtstreeks vanaf de webpagina van de uitgever wordt niet ondersteund. Er moet alleen een service-to-service-verificatie stroom worden gebruikt.

## <a name="next-steps"></a>Volgende stappen

Als u dit nog niet hebt gedaan, registreert u uw SaaS-toepassing in de [Azure Portal](https://ms.portal.azure.com) zoals uitgelegd in [een Azure AD-toepassing registreren](./pc-saas-registration.md).  Daarna gebruikt u de meest recente versie van deze interface voor ontwikkeling: [SaaS fulfillment API-versie 2](./pc-saas-fulfillment-api-v2.md).
