---
title: 'Azure front deur: regelset'
description: Dit artikel bevat een overzicht van de functie set voor Azure front-deur Standard/Premium-regels.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 8e6ceebc9e92dabe66baeb9aeff0ae9692e2bdad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101099150"
---
# <a name="what-is-a-rule-set-for-azure-front-door-standardpremium-preview"></a>Wat is een regel die is ingesteld voor Azure front deur Standard/Premium (preview)?

> [!Note]
> Deze documentatie is voor Azure front deur Standard/Premium (preview). Zoekt u informatie over de voor deur van Azure? [Hier](../front-door-overview.md)weer geven.

Een regel reeks is een aangepaste regel engine waarmee een combi natie van regels wordt gegroepeerd in één set die u aan meerdere routes kunt koppelen. Met de regel instellingen kunt u aanpassen hoe aanvragen worden verwerkt aan de rand en hoe de voor deur van Azure deze aanvragen verwerkt.

> [!IMPORTANT]
> Azure front deur Standard/Premium (preview) is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="common-supported-scenarios"></a>Veelvoorkomende ondersteunde scenario's

* Implementatie van beveiligings headers om beveiligings problemen met de browser te voor komen zoals HTTP strict-Trans Port-Security (HSTS), X-XSS-Protection, Content-Security-Policy, X-frame-Options en Access-Control-Allow-Origin headers voor de CORS-scenario's (cross-Origin Resource Sharing). Kenmerken op basis van beveiliging kunnen ook worden gedefinieerd met cookies.

* Router aanvragen naar mobiele of Desktop versies van uw toepassing op basis van het type client apparaat.

* Omleidings mogelijkheden gebruiken om 301, 302, 307 en 308 te retour neren naar de client om deze naar nieuwe hostnamen, paden, query reeksen of protocollen te sturen.

* De cacheconfiguratie van uw route dynamisch aanpassen op basis van de binnenkomende aanvragen.

* Herschrijf het pad naar de aanvraag-URL en stuurt de aanvraag door naar de juiste oorsprong in uw geconfigureerde oorspronkelijke groep.

* Aanvraag/antwoord header toevoegen, wijzigen of verwijderen om gevoelige informatie te verbergen of belang rijke informatie vast te leggen via kopteksten.

* Ondersteunings Server variabelen voor het dynamisch wijzigen van de aanvraag/antwoord headers of het herschrijven van URL'S/query reeksen, bijvoorbeeld wanneer een nieuwe pagina wordt geladen of wanneer een formulier wordt gepost. De server variabele wordt momenteel alleen ondersteund voor **[acties met regel instellingen](concept-rule-set-actions.md)** .

## <a name="architecture"></a>Architectuur

De regel set verwerkt aanvragen aan de rand. Wanneer een aanvraag wordt ontvangen op uw Azure front deur Standard/Premium-eind punt, wordt WAF eerst uitgevoerd, gevolgd door de instellingen die in route zijn geconfigureerd. Deze instellingen omvatten de regelset die aan de route is gekoppeld. Regel sets worden van boven naar beneden verwerkt in de route. Hetzelfde geldt voor regels in een regelset. Om alle acties in elke regel uit te voeren, moet aan alle match-voorwaarden binnen een regel worden voldaan. Als een aanvraag niet overeenkomt met een van de voor waarden in de configuratie van de regel instellingen, worden alleen de configuraties in route uitgevoerd.

Als **stoppen met het evalueren van resterende regels** wordt ingeschakeld, worden alle resterende regel sets die zijn gekoppeld aan de route, niet uitgevoerd.  

### <a name="example"></a>Voorbeeld

In het volgende diagram worden WAF-beleids regels eerst uitgevoerd. Er wordt een regelset geconfigureerd om een reactie header toe te voegen. Vervolgens wordt de maximale leeftijd van de cache-Control gewijzigd als aan de voor waarde match wordt voldaan.

:::image type="content" source="../media/concept-rule-set/front-door-rule-set-architecture-1.png" alt-text="Diagram waarin de architectuur van de regelset wordt weer gegeven." lightbox="../media/concept-rule-set/front-door-rule-set-architecture-1-expanded.png":::

## <a name="terminology"></a>Terminologie

Met de regel instellingen voor de Azure front-deur kunt u een combi natie van configuratie sets maken, die elk bestaan uit een set regels. De volgende regels zijn handig bij het configureren van de regelset.

Raadpleeg [Azure-abonnement en service limieten, quota's en beperkingen](../../azure-resource-manager/management/azure-subscription-service-limits.md)voor meer quotum limieten.

* *Set* Rules: een set regels die wordt gekoppeld aan een of meer [routes](concept-route.md). Elke configuratie is beperkt tot 25 regels. U kunt maximaal 10 configuraties maken.

* *Regel voor regels instellen*: een regel die bestaat uit Maxi maal 10 match voorwaarden en vijf acties. Regels zijn lokaal voor een regelset en kunnen niet worden geëxporteerd voor gebruik in meerdere regel sets. Gebruikers kunnen dezelfde regel in meerdere regel sets maken.

* *Voorwaarde voor overeenkomst*: Er zijn talrijke voorwaarden voor overeenkomst die kunnen worden gebruikt voor het parseren van uw binnenkomende aanvragen. Een regel kan uit maximaal 10 voorwaarden voor overeenkomst bestaan. De voorwaarden voor overeenkomst worden geëvalueerd met de operator **EN**. *Reguliere expressie wordt in voor waarden ondersteund*. Een volledige lijst met match voorwaarden vindt u in de [voor waarde Rule set](concept-rule-set-match-conditions.md).

* *Actie*: acties bepalen hoe afd de inkomende aanvragen verwerkt op basis van de overeenkomende voor waarden. U kunt het gedrag van caches wijzigen, aanvraag headers/reactie headers wijzigen, URL-omleidingen omschrijven en omleiden van URL. *Server variabelen worden ondersteund voor actie*. Een regel kan uit maximaal 10 voorwaarden voor overeenkomst bestaan. Een volledige lijst met acties kan acties van een [regelset](concept-rule-set-actions.md)vinden.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [maken van een voor deur standaard/Premium](create-front-door-portal.md).
* Meer informatie over het configureren van uw eerste [regelset](how-to-configure-rule-set.md).
 
