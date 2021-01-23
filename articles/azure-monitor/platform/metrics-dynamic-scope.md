---
title: Meerdere resources weer geven in de Azure Metrics Explorer
description: Meer informatie over het visualiseren van meerdere resources met behulp van de Azure Metrics Explorer.
author: ritaroloff
services: azure-monitor
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: riroloff
ms.subservice: metrics
ms.openlocfilehash: 5f5f75e63a0b201144329a6e2b7c0d503de54692
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736470"
---
# <a name="view-multiple-resources-in-the-azure-metrics-explorer"></a>Meerdere resources weer geven in de Azure Metrics Explorer

Met de kiezer voor het bereik van resources kunt u metrische gegevens weer geven over meerdere resources die zich binnen hetzelfde abonnement en dezelfde regio bevinden. In dit artikel wordt uitgelegd hoe u meerdere resources kunt weer geven met behulp van de Azure Metrics Explorer-functie van Azure Monitor. 

## <a name="select-a-resource"></a>Een resource selecteren 

Selecteer **Metrische gegevens** in het menu van **Azure Monitor** of in de sectie **Controle** van het menu van een resource. Kies vervolgens **een bereik selecteren** om de bereik kiezer te openen. 

Gebruik de bereik kiezer om de resources te selecteren waarvan u de metrische gegevens wilt zien. Het bereik moet worden ingevuld als u de metrische gegevens Verkenner hebt geopend vanuit het menu van een resource. 

![Scherm afbeelding die laat zien hoe u de kiezer van het resource bereik kunt openen.](./media/metrics-charts/019.png)

## <a name="select-multiple-resources"></a>Selecteer Meerdere resources maken. 

Sommige resource typen kunnen een query uitvoeren op metrische gegevens over meerdere resources. De metrische gegevens moeten binnen hetzelfde abonnement en dezelfde locatie vallen. Deze resource typen vindt u boven aan het menu **resource typen** .

![Scherm afbeelding met een menu met bronnen die compatibel zijn met meerdere resources.](./media/metrics-charts/020.png)

> [!WARNING] 
> U moet een machtiging voor controle lezers hebben op het abonnements niveau om metrische gegevens over meerdere resources, resource groepen of een abonnement te visualiseren. Zie [Azure-roltoewijzingen toevoegen of verwijderen met behulp van de Azure Portal](../../role-based-access-control/role-assignments-portal.md)voor meer informatie.

Als u metrische gegevens over meerdere resources wilt visualiseren, begint u met het selecteren van meerdere resources binnen de kiezer voor het bereik van de resource. 

![Scherm afbeelding die laat zien hoe u meerdere resources selecteert.](./media/metrics-charts/021.png)

> [!NOTE]
> De resources die u selecteert, moeten binnen hetzelfde resource type, dezelfde locatie en hetzelfde abonnement vallen. Resources die niet aan deze criteria voldoen, worden niet geselecteerd. 

Wanneer u klaar bent, kiest u **Toep assen** om uw selecties op te slaan. 

## <a name="select-a-resource-group-or-subscription"></a>Een resource groep of abonnement selecteren 

> [!WARNING]
> U moet een machtiging voor controle lezers hebben op het abonnements niveau om metrische gegevens over meerdere resources, resource groepen of een abonnement te visualiseren. 

Voor typen die compatibel zijn met meerdere resources kunt u een query uitvoeren voor metrische gegevens over een abonnement of meerdere resource groepen. Begin met het selecteren van een abonnement of een of meer resource groepen: 

![Scherm afbeelding die laat zien hoe u kunt zoeken in meerdere resource groepen.](./media/metrics-charts/022.png)

Selecteer een resource type en locatie. 

![Scherm opname van de geselecteerde resource groepen.](./media/metrics-charts/023.png)

U kunt de geselecteerde bereiken uitvouwen om de resources te controleren waarop uw selecties van toepassing zijn.

![Scherm opname van de geselecteerde resources in de groepen.](./media/metrics-charts/024.png)

Wanneer u klaar bent met het selecteren van scopes, selecteert u **Toep assen**. 

## <a name="split-and-filter-by-resource-group-or-resources"></a>Splitsen en filteren op resource groep of resources

Nadat u uw resources hebt getekend, kunt u splitsen en filteren gebruiken om meer inzicht te krijgen in uw gegevens. 

Met splitsen kunt u visualiseren hoe verschillende segmenten van de metrische gegevens met elkaar worden vergeleken. Als u bijvoorbeeld een metriek voor meerdere resources uitzet, kunt u **splitsen Toep assen** op splitsing op resource-id of resource groep kiezen. Met de splitsing kunt u één metriek vergelijken tussen meerdere resources of resource groepen.  

In het volgende diagram ziet u bijvoorbeeld het percentage CPU over negen Vm's. Wanneer u op Resource-ID splitst, ziet u hoe percentage CPU per VM verschilt. 

![Scherm afbeelding die laat zien hoe u splitsen kunt gebruiken om het percentage CPU over Vm's te bekijken.](./media/metrics-charts/026.png)

Samen met het splitsen kunt u filteren gebruiken om alleen de resource groepen weer te geven die u wilt zien.  Als u bijvoorbeeld het percentage CPU voor virtuele machines voor een bepaalde resource groep wilt weer geven, kunt u **filter toevoegen** selecteren om te filteren op resource groep. 

In dit voor beeld filteren we op TailspinToysDemo. Hier verwijdert het filter metrische gegevens die zijn gekoppeld aan resources in TailspinToys. 

![Scherm afbeelding die laat zien hoe u kunt filteren op resource groep.](./media/metrics-charts/027.png)

## <a name="pin-multiple-resource-charts"></a>Grafieken met meerdere bronnen vastmaken 

Voor grafieken met meerdere resources die metrische gegevens over resource groepen en-abonnementen visualiseren, moet de gebruiker de machtiging *controle lezer* hebben op het abonnements niveau. Zorg ervoor dat alle gebruikers van de Dash boards waarnaar u meerdere resource grafieken vastmaakt, voldoende machtigingen hebben. Zie [Azure-roltoewijzingen toevoegen of verwijderen met behulp van de Azure Portal](../../role-based-access-control/role-assignments-portal.md)voor meer informatie.

Zie vastmaken [aan dash boards](./metrics-charts.md#pinning-to-dashboards)om uw diagram met meerdere resources aan een dash board te koppelen. 

## <a name="next-steps"></a>Volgende stappen

* [Problemen met metrische gegevens Verkenner oplossen](metrics-troubleshoot.md)
* [Een lijst met beschikbare metrische gegevens voor Azure-services zien](metrics-supported.md)
* [Voorbeelden van geconfigureerde grafieken zien](metric-chart-samples.md)