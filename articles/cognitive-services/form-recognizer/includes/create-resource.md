---
author: laujan
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: ed5f94bdc372e317e9e312db07665d233b1b4139
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103467262"
---
Ga naar Azure Portal en <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title="Een nieuwe Form Recognizer-resource maken" target="_blank">maak een nieuwe Form Recognizer-resource </a>. Geef in het deelvenster **Maken** de volgende gegevens op:

| Projectgegevens   | Beschrijving   |
|--|--|
| **Abonnement** | Selecteer het Azure-abonnement waaraan toegang is verleend. |
| **Resourcegroep** | De [Azure-resourcegroep](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group) die de resource bevat. U kunt een nieuwe groep maken of deze toevoegen aan een bestaande groep. |
| **Regio** | De locatie van uw cognitieve service-exemplaar. Verschillende locaties kunnen latentie veroorzaken, maar deze hebben geen invloed op de beschikbaarheid van de runtime van uw resource. |
| **Naam** | Een beschrijvende naam voor de resource. U kunt het beste een beschrijvende naam gebruiken, bijvoorbeeld *MyNameFormRecognizer*. |
| **Prijscategorie** | De kosten van uw resource zijn afhankelijk van de prijscategorie die u kiest en van uw gebruik. Zie [Prijsopgaven](https://azure.microsoft.com/pricing/details/cognitive-services/) voor API's voor meer informatie.

> [!NOTE]
> Wanneer u normaal gesproken een cognitieve-serviceresource in Azure Portal maakt, hebt u de mogelijkheid een abonnementssleutel voor meerdere services te maken (die voor meerdere cognitieve services wordt gebruikt) of een abonnementssleutel voor één service (die alleen voor een specifieke cognitieve service wordt gebruikt). Momenteel is Form Recognizer echter niet opgenomen in het abonnement voor meerdere services.

Wanneer uw Form Recognizer-resource is geïmplementeerd, selecteert u deze in de portal in de lijst **Alle resources**. Uw sleutel en eindpunt vindt u op de pagina Sleutel en eindpunt van de resource, onder Resourcebeheer. Sla beide op een tijdelijke locatie op voordat u verdergaat.
