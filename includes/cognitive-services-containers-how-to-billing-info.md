---
author: aahill
ms.author: aahi
ms.date: 03/02/2021
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: d61813e723992f4381c5ea82121da8bbb70016dc
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102032926"
---
Query's naar de container worden gefactureerd op de prijs categorie van de Azure-resource die wordt gebruikt voor de `ApiKey` .

Azure Cognitive Services-containers mogen niet worden uitgevoerd zonder dat ze zijn verbonden met het eind punt voor meting/facturering. U moet de containers in staat stellen om de facturerings gegevens te allen tijde te communiceren met het eind punt. Cognitive Services containers verzenden geen klant gegevens, zoals de afbeelding of de tekst die wordt geanalyseerd, naar micro soft.

### <a name="connect-to-azure"></a>Verbinding maken met Azure

De voor de container vereiste facturerings argument waarden moeten worden uitgevoerd. Met deze waarden kan de container verbinding maken met het eind punt van de facturering. De container rapporteert het gebruik ongeveer elke 10 tot 15 minuten. Als de container geen verbinding maakt met Azure binnen het toegestane tijd venster, blijft de container actief, maar worden er geen query's verwerkt totdat het eind punt van de facturering is hersteld. De verbinding wordt 10 keer in hetzelfde tijds interval van 10 tot 15 minuten uitgevoerd. Als er geen verbinding kan worden gemaakt met het facturerings eindpunt binnen de 10 pogingen, stopt de container met het door sturen van aanvragen. Raadpleeg de [Veelgestelde vragen over de Cognitive Services container](../articles/cognitive-services/containers/container-faq.yml#how-does-billing-work) voor een voor beeld van de gegevens die naar micro soft worden verzonden voor facturering.

### <a name="billing-arguments"></a>Facturerings argumenten

Met <a href="https://docs.docker.com/engine/reference/commandline/run/" target="_blank"> `docker run` <span class="docon docon-navigate-external x-hidden-focus"></span> </a> de opdracht wordt de container gestart wanneer de drie van de volgende opties zijn voorzien van geldige waarden:

| Optie | Beschrijving |
|--------|-------------|
| `ApiKey` | De API-sleutel van de Cognitive Services resource die wordt gebruikt voor het bijhouden van facturerings gegevens.<br/>De waarde van deze optie moet worden ingesteld op een API-sleutel voor de ingerichte resource die is opgegeven in `Billing` . |
| `Billing` | Het eind punt van de Cognitive Services resource die wordt gebruikt voor het bijhouden van facturerings gegevens.<br/>De waarde van deze optie moet worden ingesteld op de eindpunt-URI van een ingerichte Azure-resource.|
| `Eula` | Geeft aan dat u de licentie voor de container hebt geaccepteerd.<br/>De waarde van deze optie moet worden ingesteld op **accepteren**. |
