---
title: Veelgestelde vragen over problemen met toepassings-en service beschikbaarheid
description: In dit artikel vindt u de veelgestelde vragen over de beschik baarheid van toepassingen en services voor Microsoft Azure Cloud Services.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: c1a5b63a33f951857bd4837380c1465af5b7583e
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98742893"
---
# <a name="application-and-service-availability-issues-for-azure-cloud-services-classic-frequently-asked-questions-faqs"></a>Problemen met Beschik baarheid van toepassingen en services voor Azure Cloud Services (klassiek): veelgestelde vragen (FAQ)

> [!IMPORTANT]
> [Azure Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md) is een nieuw implementatie model op basis van Azure Resource Manager voor het Azure Cloud Services-product.Met deze wijziging worden Azure-Cloud Services die worden uitgevoerd op het Azure Service Manager gebaseerde implementatie model, de naam van Cloud Services (klassiek) gewijzigd en moeten alle nieuwe implementaties [Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md)gebruiken.

In dit artikel vindt u veelgestelde vragen over problemen met toepassings-en service beschikbaarheid voor [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). U kunt ook de [pagina Cloud Services VM-grootte](cloud-services-sizes-specs.md) raadplegen voor informatie over de grootte.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="my-role-got-recycled-was-there-any-update-rolled-out-for-my-cloud-service"></a>Mijn rol is gerecycled. Was er een update geïmplementeerd voor mijn Cloud service?
Ongeveer eenmaal per maand brengt micro soft een nieuwe versie van het gast besturingssysteem uit voor Windows Azure PaaS Vm's.Het gast besturingssysteem is slechts één update in de pijp lijn. Een release kan worden beïnvloed door vele andere factoren. Bovendien wordt Azure op honderden duizenden computers uitgevoerd. Daarom is het onmogelijk om de exacte datum en tijd te voors pellen wanneer uw rollen opnieuw worden opgestart. De RSS-feed voor het gast besturingssysteem wordt bijgewerkt met de meest recente informatie die we hebben, maar u moet wel rekening houden met de gerapporteerde tijd om een geschatte waarde te zijn. We weten dat dit probleem kan worden veroorzaakt door klanten en werkt aan een abonnement om de herstarts te beperken of nauw keuriger in te stellen.

Zie [Azure Guest OS releases en SDK Compatibility Matrix](cloud-services-guestos-update-matrix.md)(Engelstalig) voor meer informatie over recente updates voor het gast besturingssysteem.

Voor nuttige informatie over het opnieuw opstarten en verwijzingen naar technische details van gast-en host OS-updates raadpleegt u de MSDN blog post- [rolinstantie wordt opnieuw gestart als gevolg van upgrades van het besturings systeem](/archive/blogs/kwill/role-instance-restarts-due-to-os-upgrades).

## <a name="why-does-the-first-request-to-my-cloud-service-after-the-service-has-been-idle-for-some-time-take-longer-than-usual"></a>Waarom duurt de eerste aanvraag voor mijn Cloud service nadat de service enige tijd langer inactief is dan gebruikelijk?
Wanneer de webserver de eerste aanvraag ontvangt, wordt de code eerst opnieuw gecompileerd en wordt de aanvraag verwerkt. Daarom duurt de eerste aanvraag langer dan de andere. De groep van toepassingen wordt standaard afgesloten in gevallen van inactiviteit van de gebruiker. De groep van toepassingen wordt ook elke 1.740 minuten (29 uur) automatisch gerecycled.

Internet Information Services-toepassings groepen (IIS) kunnen periodiek worden gerecycled om onstabiele statussen te voor komen die kunnen leiden tot crashes van toepassingen, vastlopen of geheugen lekkage.

De volgende documenten helpen u bij het begrijpen en oplossen van dit probleem:
* [Trage initiële belasting voor IIS herstellen](https://stackoverflow.com/questions/13386471/fixing-slow-initial-load-for-iis)
* [IIS 7,5-webtoepassing eerste aanvraag na een langzame recycling van de app-groep](https://stackoverflow.com/questions/13917205/iis-7-5-web-application-first-request-after-app-pool-recycle-very-slow)

Als u het standaard gedrag van IIS wilt wijzigen, moet u opstart taken gebruiken, omdat als u hand matig wijzigingen toepast op de Webonderdeelverzoeken, de wijzigingen uiteindelijk verloren gaan.

Zie [opstart taken voor een Cloud service configureren en uitvoeren](cloud-services-startup-tasks.md)voor meer informatie.