---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: 897f2b728dc068b09849d4f48f899b8630a87a51
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96003992"
---
Ga naar Azure Portal en <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title="Een nieuwe Form Recognizer-resource maken" target="_blank">maak een nieuwe Form Recognizer-resource <span class="docon docon-navigate-external x-hidden-focus"></span></a>. Geef in het deelvenster **Maken** de volgende gegevens op:

|    |    |
|--|--|
| **Naam** | Een beschrijvende naam voor de resource. U kunt het beste een beschrijvende naam gebruiken, bijvoorbeeld *MyNameFormRecognizer*. |
| **Abonnement** | Selecteer het Azure-abonnement waaraan toegang is verleend. |
| **Locatie** | De locatie van uw cognitieve service-exemplaar. Verschillende locaties kunnen latentie veroorzaken, maar deze hebben geen invloed op de beschikbaarheid van de runtime van uw resource. |
| **Prijscategorie** | De kosten van uw resource zijn afhankelijk van de prijscategorie die u kiest en van uw gebruik. Zie [Prijsopgaven](https://azure.microsoft.com/pricing/details/cognitive-services/) voor API's voor meer informatie.
| **Resourcegroep** | De [Azure-resourcegroep](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group) die de resource bevat. U kunt een nieuwe groep maken of deze toevoegen aan een bestaande groep. |

> [!NOTE]
> Wanneer u normaal gesproken een cognitieve-serviceresource in Azure Portal maakt, hebt u de mogelijkheid een abonnementssleutel voor meerdere services te maken (die voor meerdere cognitieve services wordt gebruikt) of een abonnementssleutel voor één service (die alleen voor een specifieke cognitieve service wordt gebruikt). Momenteel is Form Recognizer echter niet opgenomen in het abonnement voor meerdere services.

Wanneer uw Form Recognizer-resource is geïmplementeerd, selecteert u deze in de portal in de lijst **Alle resources**. Uw sleutel en eindpunt vindt u op de pagina Sleutel en eindpunt van de resource, onder Resourcebeheer. Sla beide op een tijdelijke locatie op voordat u verdergaat.