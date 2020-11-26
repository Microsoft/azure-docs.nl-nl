---
title: Een Azure API Management-exemplaar bijwerken en schalen | Microsoft Docs
description: In dit onderwerp wordt beschreven hoe u een Azure API Management-exemplaar bijwerkt en schaalt.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 04/20/2020
ms.author: apimpm
ms.openlocfilehash: 466af29a3f073f73d4a0fc62d74709cd59f82425
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183784"
---
# <a name="upgrade-and-scale-an-azure-api-management-instance"></a>Een Azure API Management-exemplaar bijwerken en schalen  

Klanten kunnen een exemplaar van Azure API Management schalen door eenheden toe te voegen en te verwijderen. Een **eenheid** bestaat uit toegewezen Azure-resources en heeft een bepaalde capaciteit voor de belasting die wordt weer gegeven als een aantal API-aanroepen per maand. Dit nummer staat niet voor een aanroep limiet, maar in plaats van een maximum doorvoer waarde om een ruwe capaciteits planning mogelijk te maken. De werkelijke door Voer en latentie variëren afhankelijk van factoren zoals het aantal en de snelheid van gelijktijdige verbindingen, het type en het aantal geconfigureerde beleids regels, de grootte van aanvragen en antwoorden en de back-endserver.

De capaciteit en prijs van elke eenheid zijn afhankelijk van de **laag** waarin de eenheid zich bevindt. U kunt kiezen uit vier lagen: **Developer**, **Basic**, **Standard**, **Premium**. Als u de capaciteit van een service binnen een laag wilt verhogen, moet u een eenheid toevoegen. Als de laag die momenteel is geselecteerd in uw API Management-exemplaar niet toestaat om meer eenheden toe te voegen, moet u een upgrade uitvoeren naar een laag op een hoger niveau.

De prijs van elke eenheid en de beschik bare functies (bijvoorbeeld implementatie met meerdere regio's) is afhankelijk van de laag die u hebt gekozen voor uw API Management-exemplaar. In het artikel [prijs informatie](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) , wordt de prijs per eenheid en de functies die u in elke laag krijgt, uitgelegd. 

>[!NOTE]
>Het artikel [prijs informatie](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) bevat geschatte aantallen capaciteit van eenheden in elke laag. Als u nauw keuriger cijfers wilt weer geven, moet u een realistisch scenario voor uw Api's bekijken. Bekijk de [capaciteit van een Azure API Management-exemplaar](api-management-capacity.md) artikel.

## <a name="prerequisites"></a>Vereisten

Als u de stappen in dit artikel wilt volgen, moet u het volgende doen:

+ Een actief Azure-abonnement.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Een API Management-exemplaar hebben. Zie [een Azure API Management-exemplaar maken](get-started-create-service-instance.md)voor meer informatie.

+ Inzicht in het concept van de [capaciteit van een Azure API Management-exemplaar](api-management-capacity.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upgrade-and-scale"></a>Upgraden en de schaal aanpassen  

U kunt kiezen uit vier lagen: **Developer**, **Basic**,  **Standard** en **Premium**. De **Developer** -laag moet worden gebruikt om de service te evalueren; het mag niet worden gebruikt voor productie. De **Developer** -laag heeft geen sla en u kunt deze laag niet schalen (eenheden toevoegen/verwijderen). 

**Basic**, **Standard** en **Premium** zijn productie lagen met sla en kunnen worden geschaald. De laag **basis** is de goedkoopste-laag met een sla en kan Maxi maal twee eenheden worden uitgebreid. de **standaard** laag kan worden geschaald tot Maxi maal vier eenheden. U kunt een wille keurig aantal eenheden toevoegen aan de **Premium** -laag.

Met de **Premium** -laag kunt u één Azure API Management-exemplaar distribueren over een wille keurig aantal gewenste Azure-regio's. Wanneer u voor het eerst een Azure API Management-service maakt, bevat de instantie slechts één eenheid en bevindt deze zich in één Azure-regio. De oorspronkelijke regio wordt aangewezen als de **primaire** regio. Extra regio's kunnen eenvoudig worden toegevoegd. Wanneer u een regio toevoegt, geeft u het aantal eenheden op dat u wilt toewijzen. U kunt bijvoorbeeld één eenheid in de **primaire** regio en vijf eenheden in een andere regio hebben. U kunt het aantal eenheden aanpassen aan het verkeer dat u in elke regio hebt. Zie [een azure API Management service-exemplaar implementeren in meerdere Azure-regio's](api-management-howto-deploy-multi-region.md)voor meer informatie.

U kunt een upgrade en downgrade uitvoeren naar een wille keurige laag. Het bijwerken of downgradeen kan sommige functies verwijderen, bijvoorbeeld VNETs of implementatie met meerdere regio's, wanneer u een downgrade uitvoert naar Standard of Basic vanuit de Premium-laag.

> [!NOTE]
> Het upgrade-of schaal proces kan 15 tot 45 minuten duren. U krijgt een melding wanneer de app is voltooid.

> [!NOTE]
> API Management service in de **verbruiks** laag wordt automatisch geschaald op basis van het verkeer.

## <a name="scale-your-api-management-service"></a>Uw API Management-service schalen

![API Management-service schalen in Azure Portal](./media/upgrade-and-scale/portal-scale.png)

1. Navigeer naar uw API Management-service in de [Azure Portal](https://portal.azure.com/).
2. Selecteer **locaties** in het menu.
3. Klik op de rij met de locatie die u wilt schalen.
4. Geef het nieuwe aantal **eenheden** op, gebruik de schuif regelaar of typ het nummer.
5. Klik op **Toepassen**.

## <a name="change-your-api-management-service-tier"></a>Uw API Management servicelaag wijzigen

1. Navigeer naar uw API Management-service in de [Azure Portal](https://portal.azure.com/).
2. Klik in het menu op de **prijs categorie** .
3. Selecteer de gewenste servicelaag in de vervolg keuzelijst. Gebruik de schuif regelaar om de schaal van uw API Management-service na de wijziging op te geven.
4. Klik op **Opslaan**.

## <a name="downtime-during-scaling-up-and-down"></a>Downtime tijdens omhoog en omlaag schalen
Als u schaalt van of naar de Developer-laag, zal er downtime zijn. Anders is er geen downtime. 

## <a name="compute-isolation"></a>Reken isolatie
Als uw beveiligings vereisten [reken isolatie](../azure-government/azure-secure-isolation-guidance.md#compute-isolation)bevatten, kunt u de **geïsoleerde** prijs categorie gebruiken. Deze laag zorgt ervoor dat de reken resources van een API Management service-exemplaar de gehele fysieke host verbruiken en het vereiste isolatie niveau bieden dat vereist is voor de ondersteuning van bijvoorbeeld het Amerikaanse Department of IL5-workloads. Als u toegang wilt krijgen tot de geïsoleerde laag, moet u [een ondersteunings ticket maken](../azure-portal/supportability/how-to-create-azure-support-request.md). 



## <a name="next-steps"></a>Volgende stappen

- [Exemplaar van Azure API Management-service implementeren in meerdere Azure-regio's](api-management-howto-deploy-multi-region.md)
- [Een Azure API Management service-exemplaar automatisch schalen](api-management-howto-autoscale.md)
- [Optimaliseer en sla uw Cloud uitgaven op](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)