---
title: Beheerde privé-eindpunten
description: Een artikel waarin beheerde privé-eindpunten in Azure Synapse Analytics wordt toegelicht
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 10/16/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 5258b1eab48f71d8d17f52849b5e57b467e7a2da
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460381"
---
# <a name="synapse-managed-private-endpoints"></a>Beheerde privé-eindpunten in Synapse

In dit artikel wordt uitleg gegeven over beheerde privé-eindpunten in Azure Synapse Analytics.

## <a name="managed-private-endpoints"></a>Beheerde privé-eindpunten

Beheerde privé-eindpunten zijn privé-eindpunten die zijn gemaakt in het Microsoft Azure Virtual Network van de beheerde werkruimte om een privé-koppeling met Azure-resources tot stand te brengen. Deze privé-eindpunten worden namens u door Azure Synapse beheerd.

Azure Synapse ondersteunt privé-koppelingen. Met Private Link kunt u Azure-services (zoals Azure Storage en Azure Cosmos DB) en in Azure gehoste klanten-/partnerservices veilig benaderen vanuit uw Azure Virtual Network.

Wanneer u een privé-koppeling gebruikt, loopt het verkeer tussen uw Virtual Network en werkruimte volledig over het backbone-netwerk van Microsoft. Private Link beschermt tegen exfiltratie van gegevens. U kunt een privé-koppeling naar een resource tot stand brengen door een privé-eindpunt te maken.

Het privé-eindpunt maakt gebruik van een privé-IP-adres van uw Virtual Network, waardoor de service feitelijk in uw Virtual Network wordt geplaatst. Privé-eindpunten worden toegewezen aan een specifieke resource in Azure, en niet de volledige service. Klanten kunnen de connectiviteit beperken tot een specifieke resource die is goedgekeurd door hun organisatie. 

Meer informatie over [privé-koppelingen en privé-eindpunten](https://docs.microsoft.com/azure/private-link/).

>[!IMPORTANT]
>Beheerde privé-eindpunten worden alleen ondersteund in Azure Synapse-werkruimten met een Virtual Network in een beheerde werkruimte.

>[!NOTE]
>Al het uitgaande verkeer van het Virtual Network in de beheerde werkruimte, met uitzondering van verkeer van beheerde privé-eindpunten, wordt in de toekomst geblokkeerd. Het wordt aanbevolen dat u beheerde privé-eindpunten maakt om verbinding te maken met al uw Azure-gegevensbronnen die zich buiten de werkruimte bevinden. 

Er wordt een privé-eindpuntverbinding gemaakt met de status 'In behandeling' wanneer u een beheerd privé-eindpunt maakt in Azure Synapse. Er wordt een goedkeuringswerkstroom gestart. De eigenaar van de privékoppelingsresource is verantwoordelijk voor het goedkeuren of afwijzen van de verbinding.

Als de eigenaar de verbinding goedkeurt, wordt de privé-koppeling tot stand gebracht. Als de eigenaar de verbinding niet goedkeurt, kan de privé koppeling niet tot stand worden gebracht. In beide gevallen wordt het beheerde privé-eindpunt bijgewerkt met de status van de verbinding.

Alleen een beheerd privé-eindpunt met een goedgekeurde status kan verkeer verzenden naar een gegeven privé-koppelingsresource.

## <a name="managed-private-endpoints-for-dedicated-sql-pool-and-serverless-sql-pool"></a>Beheerde privé-eindpunten voor toegewezen SQL-pools en serverloze SQL-pools

Toegewezen SQL-pools en serverloze SQL-pools bieden analysemogelijkheden in uw Azure Synapse-werkruimte. Deze mogelijkheden gebruiken een multi-tenant infrastructuur die niet is geïmplementeerd in het [Virtual Network in de beheerde werkruimte](./synapse-workspace-managed-vnet.md).

Wanneer een werkruimte wordt gemaakt, maakt Azure Synapse twee beheerde privé-eindpunten in de werkruimte, één voor de toegewezen SQL-pool en één voor de serverloze SQL-pool. 

Deze twee beheerde privé-eindpunten worden vermeld in Synapse Studio. Selecteer **Beheren** in het linkernavigatievenster en selecteer vervolgens **Beheerde privé-eindpunten** om deze in de studio te bekijken.

Het beheerde privé-eindpunt dat is gericht op de SQL-pool, heet *synapse-ws-sql--\<workspacename\>* . Het eindpunt dat is gericht op de serverloze SQL-pool, heet *synapse-ws-sqlOnDemand--\<workspacename\>* .

![Beheerde privé-eindpunten voor toegewezen SQL-pools en serverloze SQL-pools](./media/synapse-workspace-managed-private-endpoints/managed-pe-for-sql-1.png)

Deze twee beheerde privé-eindpunten worden automatisch voor u gemaakt wanneer u de Azure Synapse-werkruimte maakt. Er worden geen kosten in rekening gebracht voor deze twee beheerde privé-eindpunten.

## <a name="next-steps"></a>Volgende stappen

Ga voor meer informatie naar het artikel [Beheerde privé-eindpunten maken voor uw gegevensbronnen](./how-to-create-managed-private-endpoints.md).
