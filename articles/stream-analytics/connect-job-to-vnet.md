---
title: Stream Analytics-taken verbinden met resources in een Azure Virtual Network (VNET)
description: In dit artikel wordt beschreven hoe u een Azure Stream Analytics-taak verbindt met resources die zich in een VNET bevinden.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/04/2021
ms.custom: devx-track-js
ms.openlocfilehash: 99563760bf37c4046e7dd81e779fedbe415380bc
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019479"
---
# <a name="connect-stream-analytics-jobs-to-resources-in-an-azure-virtual-network-vnet"></a>Stream Analytics-taken verbinden met resources in een Azure Virtual Network (VNet)

Uw Stream Analytics-taken maken uitgaande verbindingen met uw invoer en voeren Azure-resources uit om gegevens in realtime te verwerken en resultaten te produceren. Deze invoer-en uitvoer bronnen (bijvoorbeeld Azure Event Hubs en Azure SQL Database) kunnen zich achter een Azure-firewall of in een Azure-Virtual Network (VNet) bevindt. Stream Analytics-service werkt vanuit netwerken die niet rechtstreeks kunnen worden opgenomen in uw netwerk regels.

Er zijn echter twee manieren om uw Stream Analytics-taken veilig te verbinden met uw invoer-en uitvoer bronnen in dergelijke scenario's.
* Het gebruik van privé-eind punten in Stream Analytics clusters.
* De verificatie modus beheerde identiteit gebruiken in combi natie met de netwerk instelling vertrouwde services toestaan.

Uw Stream Analytics-taak accepteert geen binnenkomende verbindingen.

## <a name="private-endpoints-in-stream-analytics-clusters"></a>Privé-eind punten in Stream Analytics clusters.
[Stream Analytics clusters](https://docs.microsoft.com/azure/stream-analytics/cluster-overview) is een specifiek berekenings cluster voor tenants waarop u uw stream Analytics-taken kunt uitvoeren. U kunt beheerde privé-eind punten maken in uw Stream Analytics-cluster, zodat alle taken die op uw cluster worden uitgevoerd, een beveiligde uitgaande verbinding met uw invoer-en uitvoer bronnen kunnen maken.

Het maken van privé-eind punten in uw Stream Analytics-cluster is een [bewerking in twee stappen](https://docs.microsoft.com/azure/stream-analytics/private-endpoints). Deze optie is het meest geschikt voor gemiddeld tot grote streaming-workloads, omdat de minimale grootte van een Stream Analytics cluster 36 SUs is (hoewel de 36 SUs kan worden gedeeld door verschillende taken in verschillende abonnementen of omgevingen, zoals ontwikkeling, testen en productie).

## <a name="managed-identity-authentication-with-allow-trusted-services-configuration"></a>Beheerde identiteits verificatie met de configuratie van vertrouwde services toestaan
Sommige Azure-Services bieden ondersteuning voor **vertrouwde micro soft Services** -netwerk instelling, die wanneer ingeschakeld is, uw stream Analytics-taken in staat stellen om veilig verbinding te maken met uw bron met behulp van sterke verificatie. Met deze optie kunt u uw taken koppelen aan uw invoer-en uitvoer bronnen zonder dat u een Stream Analytics cluster en persoonlijke eind punten nodig hebt. Het configureren van uw taak voor het gebruik van deze techniek is een bewerking van twee stappen:
* Gebruik de verificatie modus beheerde identiteit bij het configureren van de invoer of uitvoer in uw Stream Analytics-taak.
* Verleen uw specifieke Stream Analyticss taken expliciet toegang tot uw doel resources door een Azure-rol toe te wijzen aan de door het systeem toegewezen beheerde identiteit van de taak. 

Het inschakelen van **vertrouwde micro soft-Services** verleent geen toegang tot een taak. Hiermee krijgt u volledige controle over welke specifieke Stream Analytics-taken veilig toegang hebben tot uw resources. 

Uw taken kunnen met behulp van deze techniek verbinding maken met de volgende Azure-Services:
1. [Blob Storage of Azure data Lake Storage Gen2](https://docs.microsoft.com/azure/stream-analytics/blob-output-managed-identity) : kan het opslag account van uw taak, de invoer of uitvoer van streams zijn.
2. [Azure-Event hubs](https://docs.microsoft.com/azure/stream-analytics/event-hubs-managed-identity) : kan de stroomsgewijze invoer of uitvoer van uw taak zijn.

Als uw taken verbinding moeten maken met andere invoer-of uitvoer typen, kunt u eerst van Stream Analytics naar Event Hubs uitvoer schrijven en vervolgens naar een wille keurige gewenste bestemming met behulp van Azure Functions. Als u rechtstreeks wilt schrijven van Stream Analytics naar andere uitvoer typen die zijn beveiligd in een VNet of firewall, is de enige optie om privé-eind punten in Stream Analytics clusters te gebruiken.

## <a name="next-steps"></a>Volgende stappen

* [Privé-eind punten in Stream Analytics clusters maken en verwijderen](https://docs.microsoft.com/azure/stream-analytics/private-endpoints)
* [Verbinding maken met Event Hubs in een VNet met behulp van beheerde identiteits verificatie](https://docs.microsoft.com/azure/stream-analytics/event-hubs-managed-identity)
* [Verbinding maken met Blob Storage en ADLS Gen2 in een VNet met behulp van beheerde identiteits verificatie](https://docs.microsoft.com/azure/stream-analytics/blob-output-managed-identity)
