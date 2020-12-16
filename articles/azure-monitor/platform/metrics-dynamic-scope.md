---
title: Meerdere resources in Metrics Explorer weer geven
description: Meer informatie over het visualiseren van meerdere resources op Azure Monitor Metrics Explorer
author: ritaroloff
services: azure-monitor
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: riroloff
ms.subservice: metrics
ms.openlocfilehash: 724809dbce3ca1b5a36f4da0ba5c03d0f78897f5
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97577697"
---
# <a name="viewing-multiple-resources-in-metrics-explorer"></a>Meerdere resources in Metrics Explorer weer geven

Met de kiezer voor het bereik van resources kunt u metrische gegevens weer geven over meerdere resources die zich binnen hetzelfde abonnement en dezelfde regio bevinden. Hieronder vindt u instructies voor het weer geven van meerdere resources in Azure Monitor Metrics Explorer. 

## <a name="selecting-a-resource"></a>Een resource selecteren 

Selecteer **Metrische gegevens** in het menu van **Azure Monitor** of in de sectie **Controle** van het menu van een resource. Klik op de knop een bereik selecteren om de resource bereik kiezer te openen. Hiermee kunt u de resources selecteren waarvoor u de metrische gegevens wilt bekijken. Dit moet al zijn ingevuld als u metrische gegevens Verkenner hebt geopend vanuit het menu van een resource. 

![Scherm afbeelding van de kiezer van het resource bereik gemarkeerd in rood](./media/metrics-charts/019.png)

## <a name="selecting-multiple-resources"></a>Meerdere resources selecteren 

Sommige resource typen hebben de mogelijkheid om gegevens op te vragen voor meerdere resources, zolang ze zich in hetzelfde abonnement en dezelfde locatie bevinden. Deze resource typen vindt u boven aan de vervolg keuzelijst ' resource typen '. 

![Scherm afbeelding met een vervolg keuzelijst met resources die compatibel zijn met meerdere bronnen ](./media/metrics-charts/020.png)

> [!WARNING] 
> U moet een machtiging voor controle lezers hebben op het abonnements niveau om metrische gegevens over meerdere resources, resource groepen of een abonnement te visualiseren. Volg de instructies in [dit document](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)om dit te doen.

Als u metrische gegevens over meerdere resources wilt visualiseren, moet u beginnen met het selecteren van meerdere resources binnen de kiezer voor het bereik van de resource. 

![Scherm afbeelding die laat zien hoe u meerdere resources selecteert](./media/metrics-charts/021.png)

> [!NOTE]
> U kunt alleen meerdere resources selecteren binnen hetzelfde resource type, dezelfde locatie en hetzelfde abonnement. Resources buiten deze criteria kunnen niet worden geselecteerd. 

Wanneer u klaar bent met selecteren, klikt u op de knop Toep assen om uw selectie op te slaan. 

## <a name="selecting-a-resource-group-or-subscription"></a>Een resource groep of-abonnement selecteren 

> [!WARNING]
> U moet een machtiging voor controle lezers hebben op het abonnements niveau om metrische gegevens over meerdere resources, resource groepen of een abonnement te visualiseren. 

Voor compatibele multi-resource typen kunt u ook een query uitvoeren voor metrische gegevens over een abonnement of meerdere resource groepen. Begin met het selecteren van een abonnement of een of meer resource groepen: 

![Scherm afbeelding die laat zien hoe u kunt zoeken in meerdere resource groepen ](./media/metrics-charts/022.png)

U moet vervolgens een resource type en-locatie selecteren voordat u de nieuwe scope kunt blijven Toep assen. 

![Scherm opname van de geselecteerde resource groepen ](./media/metrics-charts/023.png)

U kunt ook de geselecteerde bereiken uitvouwen om te controleren op welke resources dit van toepassing is.

![Scherm opname van de geselecteerde resources in de groepen ](./media/metrics-charts/024.png)

Wanneer u klaar bent met het selecteren van uw bereiken, klikt u op Toep assen om uw selecties op te slaan. 

## <a name="splitting-and-filtering-by-resource-group-or-resources"></a>Splitsen en filteren op resource groep of resources

Nadat u uw resources hebt getekend, kunt u het hulp programma voor splitsen en filteren gebruiken om meer inzicht te krijgen in uw gegevens. 

Met splitsen kunt u visualiseren hoe verschillende segmenten van de metrische gegevens met elkaar worden vergeleken. Als u bijvoorbeeld een metriek uitzet voor meerdere resources, kunt u het hulp programma ' splitsing Toep assen ' gebruiken om te splitsen op resource-id of resource groep. Op deze manier kunt u eenvoudig één metriek vergelijken tussen meerdere resources of resource groepen.  

Hieronder ziet u bijvoorbeeld een grafiek van het percentage CPU over 9VMs. Door te splitsen op resource-id, kunt u eenvoudig zien hoe percentage CPU per VM verschilt. 

![Scherm afbeelding die laat zien hoe u splitsen kunt gebruiken om het percentage CPU per VM te bekijken](./media/metrics-charts/026.png)

Naast het splitsen kunt u de filter functie gebruiken om alleen de resource groepen weer te geven die u wilt zien.  Als u bijvoorbeeld het percentage CPU voor virtuele machines voor een bepaalde resource groep wilt weer geven, kunt u het hulp programma ' filter toevoegen ' gebruiken om te filteren op resource groep. In dit voor beeld filteren we op TailspinToys, waarmee metrische gegevens worden verwijderd die zijn gekoppeld aan resources in TailspinToysDemo. 

![Scherm afbeelding die laat zien hoe u kunt filteren op resource groep](./media/metrics-charts/027.png)

## <a name="pinning-your-multi-resource-charts"></a>Uw grafieken met meerdere bronnen vastmaken 

> [!WARNING] 
> U moet een machtiging voor controle lezers hebben op het abonnements niveau om metrische gegevens over meerdere resources, resource groepen of een abonnement te visualiseren. Volg de instructies in [dit document](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)om dit te doen. 

Volg [de instructies om](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts#create-alert-rules)uw grafiek met meerdere resources vast te maken. 

## <a name="next-steps"></a>Volgende stappen

* [Problemen met Metrics Explorer oplossen](metrics-troubleshoot.md)
* [Een lijst met beschikbare metrische gegevens voor Azure-services zien](metrics-supported.md)
* [Voorbeelden van geconfigureerde grafieken zien](metric-chart-samples.md)

