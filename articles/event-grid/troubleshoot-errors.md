---
title: Azure Event Grid-probleemoplossings gids
description: Dit artikel bevat een lijst met fout codes, fout berichten, beschrijvingen en aanbevolen acties.
ms.topic: conceptual
ms.date: 04/09/2021
ms.openlocfilehash: 4647039f67cf84a5471dc7b3f38ca754ca9eecb0
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310866"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>Problemen met Azure Event Grid oplossen
In deze hand leiding voor probleem oplossing vindt u de volgende informatie: 

- Azure Event Grid fout codes
- Foutberichten
- Beschrijvingen van de fouten
- Aanbevolen acties die u moet uitvoeren wanneer u deze fouten ontvangt. 

## <a name="error-code-400"></a>Foutcode 400
| Foutcode | Foutbericht | Beschrijving | Aanbeveling |
| ---------- | ------------- | ----------- | -------------- | 
| Http status code. onjuiste aanvraag<br/>400 | De naam van het onderwerp moet tussen de 3 en 50 tekens lang zijn. | De naam van het aangepaste onderwerp moet tussen de 3 en 50 tekens lang zijn. Alleen alfanumerieke letters, cijfers en het teken '-' zijn toegestaan in de naam van het onderwerp. De naam mag ook niet beginnen met de volgende gereserveerde woorden: <ul><li>Micro soft</li><li>EventGrid</li><li>Opgehaald</li></ul> | Kies een andere onderwerpnaam die voldoet aan de vereisten voor de onderwerpnaam. |
| Http status code. onjuiste aanvraag<br/>400 | De domein naam moet tussen de 3 en 50 tekens lang zijn. | De lengte van de domein naam moet tussen de 3 en 50 tekens lang zijn. Alleen alfanumerieke letters, cijfers en het teken '-' zijn toegestaan in de domein naam. De naam mag ook niet beginnen met de volgende gereserveerde woorden:<ul><li>Micro soft</li><li>EventGrid</li><li>Opgehaald</li> | Kies een andere domein naam die voldoet aan de vereisten voor de domein naam. |
| Http status code. onjuiste aanvraag<br/>400 | Ongeldige verval tijd. | De verloop tijd voor het gebeurtenis abonnement bepaalt wanneer het gebeurtenis abonnement buiten gebruik wordt gesteld. Deze waarde moet in de toekomst een geldige datum/tijd-waarde zijn.| Zorg ervoor dat de verloop tijd van het gebeurtenis abonnement in een geldige datum-en tijd notatie is ingesteld op de toekomst. |

## <a name="error-code-409"></a>Fout code: 409
| Foutcode | Foutbericht | Description | Aanbevolen actie |
| ---------- | ------------- | ----------- | -------------- | 
| Http status code. conflict <br/>409 | Er bestaat al een onderwerp met de opgegeven naam. Kies een andere naam voor het onderwerp.   | De naam van het aangepaste onderwerp moet uniek zijn in één Azure-regio om te zorgen voor een correcte publicatie bewerking. Dezelfde naam kan worden gebruikt in verschillende Azure-regio's. | Kies een andere naam voor het onderwerp. |
| Http status code. conflict <br/> 409 | Het domein met de opgegeven bestaat al. Kies een andere domein naam. | De domein naam moet uniek zijn in één Azure-regio om te zorgen voor een correcte publicatie bewerking. Dezelfde naam kan worden gebruikt in verschillende Azure-regio's. | Kies een andere naam voor het domein. |
| Http status code. conflict<br/>409 | De quotum limiet is bereikt. Zie [Azure Event grid limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits)voor meer informatie over deze limieten.  | Elk Azure-abonnement heeft een limiet voor het aantal Azure Event Grid resources dat kan worden gebruikt. Sommige of alle van deze quota zijn overschreden en er kunnen geen resources meer worden gemaakt. | Controleer het gebruik van uw huidige resources en verwijder deze die niet nodig zijn. Als u geen resources kunt verwijderen, maakt u een nieuw Azure-abonnement en maakt u Event Grid resources in dat abonnement. |

## <a name="error-code-403"></a>Fout code: 403

| Foutcode | Foutbericht | Description | Aanbevolen actie |
| ---------- | ------------- | ----------- | ------------------ |
| Http status code. verboden <br/>403 | Het publiceren naar {topic/Domain} door de client {IpAddress} is geweigerd vanwege IpAddress-filter regels. | Voor het onderwerp of het domein zijn IP-firewall regels geconfigureerd en de toegang is beperkt tot geconfigureerde IP-adressen. | Voeg het IP-adres toe aan de IP-firewall regels, Zie [IP-Firewall configureren](configure-firewall.md) |
| Http status code. verboden <br/> 403 | Publiceren naar {topic/Domain} door de client is geweigerd omdat de aanvraag afkomstig is van een persoonlijk eind punt en er geen overeenkomende persoonlijke eindpunt verbinding is gevonden voor de resource. | Het onderwerp of het domein heeft privé-eind punten en de publicatie aanvraag is afkomstig van een persoonlijk eind punt dat niet is geconfigureerd of goedgekeurd. | Een persoonlijk eind punt configureren voor het onderwerp/domein. [Privé-eindpunten configureren](configure-private-endpoints.md) |

Controleer ook of uw webhook zich achter een Azure-toepassing gateway of Web Application firewall bevindt. Als dat het geval is, schakelt u de volgende firewall regels uit en voert u opnieuw een HTTP-POST uit:

- 920300 (aanvraag mist een Accept-header)
- 942430 (beperkte SQL-teken anomalie detectie (argumenten): aantal speciale tekens is overschreden (12))
- 920230 (meerdere URL-code ring gedetecteerd)
- 942130 (SQL-injectie aanval: SQL-tautology gedetecteerd.)
- 931130 (mogelijke RFI-aanval (Remote File Include) = off-domein verwijzing/koppeling)



## <a name="next-steps"></a>Volgende stappen
Als u meer hulp nodig hebt, kunt u uw probleem in het [stack overflow forum](https://stackoverflow.com/questions/tagged/azure-eventgrid) plaatsen of een [ondersteunings ticket](https://azure.microsoft.com/support/options/)openen. 
