---
title: Wat is Azure Arc enabled PostgreSQL grootschalige?
description: Wat is Azure Arc enabled PostgreSQL grootschalige?
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 10f21067f48155a394ac20337d77e3e82aae64d8
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98985934"
---
# <a name="what-is-azure-arc-enabled-postgresql-hyperscale"></a>Wat is Azure Arc enabled PostgreSQL grootschalige?

Azure Arc enabled PostgreSQL grootschalige is een van de database services die beschikbaar zijn als onderdeel van Azure Arc enabled Data Services. Azure Arc maakt het mogelijk om Azure Data Services on-premises, aan de rand en in openbare clouds uit te voeren met behulp van Kubernetes en de infrastructuur van uw keuze. De toegevoegde waarde van de gegevens services van Azure Arc enabled rond:
- Altijd actueel
- Elastisch schalen
- Self-service inrichten
- Geïntegreerd beheer
- Niet-verbonden scenario's

Meer informatie vindt u op:
- [Wat zijn gegevens services van Azure Arc ingeschakeld](overview.md)
- [Connectiviteitsmodi en -vereisten](connectivity.md)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="compare-solutions"></a>Oplossingen vergelijken

In deze sectie wordt beschreven hoe de Azure Arc enabled PostgreSQL grootschalige verschilt van Azure Database for PostgreSQL grootschalige (Citus)?

## <a name="azure-database-for-postgresql-hyperscale-citus"></a>Azure Database for PostgreSQL Hyperscale (Citus)

:::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale.png" alt-text="Azure SQL Database voor PostgreSQL grootschalige (Citus)":::

Dit is de grootschalige vorm factor van de post gres-data base-engine die beschikbaar is als Database as a Service in azure (PaaS). Het wordt aangestuurd door de Citus-extensie waarmee de grootschalige-ervaring wordt ingeschakeld. In deze vorm factor wordt de service uitgevoerd in de micro soft-data centers en wordt beheerd door micro soft.

## <a name="azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc enabled PostgreSQL grootschalige

:::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale-arc.png" alt-text="Azure Arc enabled PostgreSQL grootschalige":::

Dit is de grootschalige vorm factor van de post gres-data base-engine die beschikbaar is voor Azure Arc ingeschakelde Data Services. Het wordt ook aangestuurd door de Citus-extensie waarmee de grootschalige-ervaring wordt ingeschakeld. In deze vorm factor bieden onze klanten de infra structuur die als host fungeert voor de systemen en deze kan worden gebruikt.

## <a name="next-steps"></a>Volgende stappen
- **Probeer het uit.** Ga snel aan de slag met [Azure Arc](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) direct op Azure Kubernetes service (AKS), AWS elastische Kubernetes service (EKS), Google Cloud Kubernetes Engine (GKE) of in een Azure-VM. 

- **Maak uw eigen.** Volg deze stappen voor het maken van uw eigen Kubernetes-cluster: 
   1. [Installeer de client-hulpprogramma's](install-client-tools.md)
   2. [De Azure Arc-gegevens controller maken](create-data-controller.md)
   3. [Een Azure Database for PostgreSQL grootschalige-Server groep maken op Azure Arc](create-postgresql-hyperscale-server-group.md) 

- **Learn**
   - [Meer informatie over Azure Arc enabled Data Services](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
   - [Meer informatie over Azure Arc](https://aka.ms/azurearc)
