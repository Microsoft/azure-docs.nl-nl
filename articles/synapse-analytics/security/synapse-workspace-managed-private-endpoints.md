---
title: Beheerde privé-eindpunten
description: Een artikel waarin beheerde privé-eindpunten in Azure Synapse Analytics wordt toegelicht
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 01/12/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 65794c695fa4b36586b23a308845b1f12a20b7cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98569943"
---
# <a name="synapse-managed-private-endpoints"></a>Beheerde privé-eindpunten in Synapse

In dit artikel wordt uitleg gegeven over beheerde privé-eindpunten in Azure Synapse Analytics.

## <a name="managed-private-endpoints"></a>Beheerde privé-eindpunten

Beheerde privé-eindpunten zijn privé-eindpunten die zijn gemaakt in een beheerd virtueel netwerk dat is gekoppeld aan uw Azure Synapse-werkruimte. Met beheerde privé-eindpunten wordt een privékoppeling naar Azure-resources tot stand gebracht. Deze privé-eindpunten worden namens u door Azure Synapse beheerd. Vanuit uw Azure Synapse-werkruimte kunt u beheerde privé-eindpunten maken om toegang te krijgen tot Azure-services (zoals Azure Storage en Azure Cosmos DB) en in Azure gehoste klanten-/partnerservices.

Wanneer u beheerde privé-eindpunten gebruikt, verloopt verkeer tussen uw Azure Synapse-werkruimte en andere Azure-resources volledig over het Microsoft-backbone-netwerk. Beheerde privé-eindpunten bieden bescherming tegen exfiltratie van gegevens. Een beheerd privé-eindpunt maakt gebruik van een privé IP-adres van uw beheerde virtuele netwerk om de Azure-service waarmee uw Azure Synapse-werkruimte communiceert, op efficiënte wijze in uw virtuele netwerk onder te brengen. Beheerde privé-eindpunten worden toegewezen aan een specifieke resource in Azure, en niet de volledige service. Klanten kunnen de connectiviteit beperken tot een specifieke resource die is goedgekeurd door hun organisatie. 

Meer informatie over [privé-koppelingen en privé-eindpunten](../../private-link/index.yml).

>[!IMPORTANT]
>Beheerde privé-eindpunten worden alleen ondersteund in Azure Synapse-werkruimten met een Virtual Network in een beheerde werkruimte.

>[!NOTE]
>Als u een Azure Synapse-werkruimte maakt, kunt u er eventueel een beheerd virtueel netwerk aan koppelen. Als u een beheerd virtueel netwerk aan uw werkruimte wilt koppelen, kunt u ook het uitgaand verkeer vanaf uw werkruimte beperken tot alleen goedgekeurde doelen. U moet beheerde privé-eindpunten naar deze doelen maken. 


Er wordt een privé-eindpuntverbinding gemaakt met de status 'In behandeling' wanneer u een beheerd privé-eindpunt maakt in Azure Synapse. Er wordt een goedkeuringswerkstroom gestart. De eigenaar van de privékoppelingsresource is verantwoordelijk voor het goedkeuren of afwijzen van de verbinding. Als de eigenaar de verbinding goedkeurt, wordt de privé-koppeling tot stand gebracht. Als de eigenaar de verbinding niet goedkeurt, kan de privé koppeling niet tot stand worden gebracht. In beide gevallen wordt het beheerde privé-eindpunt bijgewerkt met de status van de verbinding. Er kan alleen een beheerd privé-eindpunt in een goedgekeurde status worden gebruikt om verkeer te verzenden naar de resource met de privékoppeling die aan het beheerde privé-eindpunt is gekoppeld.

## <a name="managed-private-endpoints-for-dedicated-sql-pool-and-serverless-sql-pool"></a>Beheerde privé-eindpunten voor toegewezen SQL-pools en serverloze SQL-pools

Toegewezen SQL-pools en serverloze SQL-pools bieden analysemogelijkheden in uw Azure Synapse-werkruimte. Deze mogelijkheden gebruiken een multi-tenant infrastructuur die niet is geïmplementeerd in het [Virtual Network in de beheerde werkruimte](./synapse-workspace-managed-vnet.md).

Wanneer een werkruimte wordt gemaakt, maakt Azure Synapse twee beheerde privé-eindpunten in de werkruimte, één voor de toegewezen SQL-pool en één voor de serverloze SQL-pool. 

Deze twee beheerde privé-eindpunten worden vermeld in Synapse Studio. Selecteer **Beheren** in het linkernavigatievenster en selecteer vervolgens **Beheerde privé-eindpunten** om deze in de studio te bekijken.

Het beheerde privé-eindpunt dat is gericht op de SQL-pool, heet *synapse-ws-sql--\<workspacename\>* . Het eindpunt dat is gericht op de serverloze SQL-pool, heet *synapse-ws-sqlOnDemand--\<workspacename\>* .

![Beheerde privé-eindpunten voor toegewezen SQL-pools en serverloze SQL-pools](./media/synapse-workspace-managed-private-endpoints/managed-pe-for-sql-1.png)

Deze twee beheerde privé-eindpunten worden automatisch voor u gemaakt wanneer u de Azure Synapse-werkruimte maakt. Er worden geen kosten in rekening gebracht voor deze twee beheerde privé-eindpunten.

## <a name="next-steps"></a>Volgende stappen

Ga voor meer informatie naar het artikel [Beheerde privé-eindpunten maken voor uw gegevensbronnen](./how-to-create-managed-private-endpoints.md).