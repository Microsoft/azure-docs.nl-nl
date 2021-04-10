---
title: Publicatie handleiding per aanbiedings type-micro soft Commercial Marketplace
description: In dit artikel worden de soorten aanbiedingen beschreven die beschikbaar zijn in de micro soft Commercial Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 10/06/2020
ms.openlocfilehash: 3be967b611f6b75705a60bfa14523832b0652931
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98879221"
---
# <a name="publishing-guide-by-offer-type"></a>Publicatiehandleiding op type aanbieding

In dit artikel worden de soorten aanbiedingen beschreven die beschikbaar zijn in de commerciële Marketplace. Met het *aanbiedings type* wordt de aanbod structuur gedefinieerd, waaronder de meta gegevens, artefacten en andere inhoud die wordt weer gegeven in de commerciële Marketplace.

Nadat u [een publicatie optie](determine-your-listing-type.md)hebt gekozen, moet u een aanbiedings type kiezen voordat u begint met het maken van uw aanbieding in partner centrum. Het type aanbieding komt overeen met het type van de oplossing, de app of het service aanbod dat u wilt publiceren, evenals de uitlijning van micro soft-producten en-services.

U kunt één type aanbieding op verschillende manieren configureren om verschillende publicatie opties, een aanbiedings optie, inrichting of prijzen in te scha kelen. De publicatie optie en configuratie van het aanbiedings type worden ook afgestemd op de beschik baarheid van de aanbieding en technische vereisten.

Controleer de vereisten voor de online winkel en de vereisten voor de technische publicatie voordat u uw aanbieding maakt.

## <a name="list-of-offer-types"></a>Lijst met aanbiedings typen

De volgende tabel bevat de typen commerciële Marketplace-aanbiedingen in het partner centrum.

| **Aanbiedings type**    | **Beschrijving**  |
| :------------------- | :-------------------|
| [**Azure Application**](plan-azure-application-offer.md) | Er zijn twee soorten Azure-toepassings abonnementen: _oplossings sjabloon_ en _beheerde toepassing_. Beide typen abonnementen ondersteunen het automatiseren van de implementatie en configuratie van een oplossing buiten één virtuele machine (VM). U kunt het proces van het bieden van meerdere resources, waaronder Vm's, netwerken en opslag resources, automatiseren om complexe oplossingen te bieden, zoals IaaS-oplossingen. Beide typen abonnementen kunnen veel verschillende soorten Azure-resources gebruiken, inclusief, maar niet beperkt tot virtuele machines.<ul><li>**Oplossingen voor oplossings sjablonen** zijn een van de belangrijkste manieren om een oplossing in de commerciële Marketplace te publiceren. Oplossingen voor oplossings sjablonen zijn niet transactable in de commerciële Marketplace, maar kunnen worden gebruikt voor het implementeren van betaalde VM-aanbiedingen die worden gefactureerd via de commerciële Marketplace. Gebruik het oplossings sjabloon type wanneer de klant de oplossing beheert en de trans acties worden gefactureerd via een ander abonnement.</li><br><li>Met **beheerde toepassings** abonnementen kunt u eenvoudig volledig beheerde, kant-en-klare toepassingen voor uw klanten maken en leveren. Ze hebben dezelfde mogelijkheden als oplossings sjabloon plannen, met enkele belang rijke verschillen:</li><ul><li> De resources worden geïmplementeerd in een resource groep en worden beheerd door de uitgever van de app. De resourcegroep is opgenomen in het abonnement van de consument, maar een identiteit in de tenant van de uitgever heeft toegang tot de resourcegroep.</li><li>Als uitgever kunt u de kosten opgeven voor de continue ondersteuning van de oplossing en trans acties worden ondersteund via de commerciële Marketplace.</li></ul>Gebruik het type beheerd toepassings plan wanneer u of uw klant vereist dat de oplossing wordt beheerd door een partner of u een oplossing op basis van een abonnement implementeert.</ul> |
| [**Azure-container**](marketplace-containers.md) | Gebruik het Azure container-aanbod type wanneer uw oplossing een docker-container installatie kopie is ingericht als een op Kubernetes gebaseerde Azure-container service. |
| [**Virtuele Azure-machine**](marketplace-virtual-machines.md) | Gebruik het type virtuele machine-aanbieding wanneer u een virtueel apparaat implementeert op het abonnement dat is gekoppeld aan uw klant. |
| [**Consulting Service**](./plan-consulting-service-offer.md) | Consulting Services helpt klanten met services te verbinden om hun gebruik van Azure-, Dynamics 365-of Power suite-services te ondersteunen en uit te breiden.|
| [**Dynamics 365**](appsource-offer-publishing-guide.md) | U kunt AppSource-aanbiedingen publiceren die voortbouwen op of uitgebreid Dynamics 365 Business Central, Dynamics 365 Customer engagement, Power apps en Finance and Operations apps.|
| [**Module IoT Edge**](iot-edge-module.md) | Azure IoT Edge modules zijn de kleinste reken eenheden die worden beheerd door IoT Edge en kunnen micro soft-Services (zoals Azure Stream Analytics), services van derden of uw eigen oplossing-specifieke code bevatten. |
| [**Beheerde service**](./plan-managed-service-offer.md) | U kunt beheerde service aanbiedingen maken en door de klant gedelegeerde abonnementen of resource groepen beheren via [Azure Lighthouse](../lighthouse/overview.md).|
| [**App Power bi** <br/> **Microsoft 365**](appsource-offer-publishing-guide.md) | U kunt AppSource-aanbiedingen publiceren die zijn gebaseerd op of uitgebreid Power BI en Microsoft 365.|
| [**Software als een service**](plan-saas-offer.md) | Gebruik het type SaaS-aanbieding (Software as a Service) om uw klant in staat te stellen uw SaaS-gebaseerde technische oplossing als een abonnement te kopen. Zie voor meer informatie over de vereisten voor eenmalige aanmelding voor SaaS-aanbiedingen [Azure AD en transactable SaaS-aanbiedingen in de commerciële Marketplace](azure-ad-saas.md). |


## <a name="next-steps"></a>Volgende stappen

- Bekijk de geschiktheids vereisten in het bijbehorende artikel voor uw aanbiedings type om de selectie en configuratie van uw aanbieding te volt ooien.
- Bekijk de publicatie patronen voor elke online winkel voor beelden van hoe uw oplossing is gekoppeld aan een type en configuratie van een aanbieding.