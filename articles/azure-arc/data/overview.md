---
title: Wat zijn Azure Arc-gegevensservices
description: Introduceert Azure Arc-gegevensservices
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 09/22/2020
ms.topic: overview
ms.openlocfilehash: ef16a4c0b6ea40fb4934307916f1fe79ccea72f9
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97609136"
---
# <a name="what-are-azure-arc-enabled-data-services-preview"></a>Wat zijn Azure Arc-gegevensservices (preview)?

Azure Arc maakt het mogelijk om Azure Data Services on-premises, aan de rand en in openbare clouds uit te voeren met behulp van Kubernetes en de infrastructuur van uw keuze.

Momenteel zijn de volgende Azure Arc-gegevensservices beschikbaar als preview-versie:

- SQL Managed Instance
- PostgreSQL Hyperscale

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="always-current"></a>Altijd actueel

Azure Arc-gegevensservices, zoals Azure Arc Enabled SQL Managed Instance en Azure Arc Enabled PostgreSQL Hyperscale ontvangen regelmatig updates, waaronder onderhoudspatches en nieuwe functies die vergelijkbaar zijn met de ervaring in Azure. Updates van de Microsoft Container Registry worden aan u gegeven en de implementatie-cadences worden door u ingesteld op basis van uw beleid. Op deze manier kunnen on-premises databases up-to-date blijven, terwijl u ervoor zorgt dat u de controle behoudt. Omdat Data Services met Azure Arc een abonnementsservice zijn, is het niet meer mogelijk dat situaties waarin uw databases niet meer worden ondersteund, nog voorkomen.

## <a name="elastic-scale"></a>Elastisch schalen

Met cloudfunctionaliteit on-premises kunt u databases op dezelfde manier dynamisch omhoog of omlaag schalen als in Azure, op basis van de beschikbare capaciteit van uw infrastructuur. Deze mogelijkheid kan voldoen aan burst-scenario's die vluchtige behoeften hebben, waaronder scenario's waarbij gegevens in realtime moeten worden opgenomen en opgevraagd, op elke schaal, met een reactietijd van minder dan seconden. Daarnaast kunt u ook database-instanties uitschalen met behulp van de unieke implementatie optie voor hyperschalen van Azure Database for PostgreSQL Hyperscale. Deze mogelijkheid biedt gegevens-workloads een extra boost van capaciteitsoptimalisatie, met behulp van unieke scale-*out* lees- en schrijfbewerkingen.

## <a name="self-service-provisioning"></a>Self-service inrichten

Azure Arc biedt ook andere voordelen van de cloud, zoals snelle implementatie en automatisering op schaal. Dankzij de Kubernetes-indeling kunt u binnen enkele seconden een database implementeren met behulp van GUI- of CLI-hulpprogramma's.

## <a name="unified-management"></a>Geïntegreerd beheer

Met vertrouwde hulpprogramma's zoals het Azure Portal, Azure Data Studio en de [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] kunt u nu een uniforme weergave krijgen van al uw gegevensassets die zijn geïmplementeerd met Azure Arc. U kunt niet alleen een verscheidenheid aan relationele databases in uw omgeving en Azure bekijken en beheren, maar ook logboeken en telemetrie van Kubernetes-Api's om de onderliggende infrastructuurcapaciteit en -status te analyseren. Naast het gebruik van gelokaliseerde logboekanalyse en prestatiebewaking, kunt u nu gebruikmaken van Azure Monitor voor uitgebreid operationeel inzicht in uw hele bezit.

## <a name="disconnected-scenario-support"></a>Niet-verbonden scenario's

Veel van de services, zoals het inrichten van selfservice, automatische back-ups/herstel en bewaking, kunnen lokaal worden uitgevoerd in uw infrastructuur met of zonder directe verbinding met Azure. Als u rechtstreeks verbinding maakt met Azure, worden er aanvullende opties geopend voor de integratie met andere Azure-Services, zoals Azure Monitor, en de mogelijkheid om de Azure Portal en Azure Resource Manager Api's van overal ter wereld te gebruiken voor het beheren van uw Azure Arc-gegevensservices.

## <a name="next-steps"></a>Volgende stappen

> **Wilt u gewoon iets uitproberen?**  
> Ga snel aan de slag met [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) op Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) of in een Azure-VM.

[Installeer de client-hulpprogramma's](install-client-tools.md)

[Maak de Azure Arc gegevenscontroller](create-data-controller.md) (hiervoor moeten eerst de client-hulpprogramma's worden geïnstalleerd)

[Maak een Azure SQL Managed Instance op Azure Arc](create-sql-managed-instance.md) (hiervoor moet u eerst een Azure Arc-gegevenscontroller maken)

[Maak een Azure Database for PostgreSQL Hyperscale-servergroep op Azure Arc](create-postgresql-hyperscale-server-group.md) (eerst moet een Azure Arc-gegevenscontroller gemaakt worden)
