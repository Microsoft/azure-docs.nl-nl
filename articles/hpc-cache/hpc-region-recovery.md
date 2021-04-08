---
title: Regionale redundantie en failover-herstel met Azure HPC cache
description: Technieken voor het bieden van failover-mogelijkheden voor herstel na nood gevallen met Azure HPC cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: 9159807f55ae52393b8fccec339fcc94c3e4ebb0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87061375"
---
# <a name="use-multiple-caches-for-regional-failover-recovery"></a>Meerdere caches gebruiken voor regionale failover-herstel

Elk Azure HPC-cache-exemplaar wordt uitgevoerd binnen een bepaald abonnement en in één regio. Dit betekent dat uw cache werk stroom mogelijk kan worden onderbroken als de regio een volledige onderbreking heeft.

In dit artikel wordt een strategie beschreven om het risico op onderbrekingen te verminderen door een tweede regio te gebruiken voor het uitvoeren van een cache-failover.

De sleutel maakt gebruik van back-end-opslag die toegankelijk is vanuit meerdere regio's. Deze opslag kan een on-premises NAS-systeem zijn met de juiste DNS-ondersteuning of Azure Blob-opslag die zich in een andere regio bevindt dan de cache.

Als uw werk stroom in uw primaire regio wordt voortgezet, worden gegevens opgeslagen in de lange termijn opslag buiten de regio. Als de cache regio niet beschikbaar is, kunt u een duplicaat van een Azure HPC-cache-exemplaar maken in een secundaire regio, verbinding maken met dezelfde opslag en het werk vanuit de nieuwe cache hervatten.

## <a name="planning-for-regional-failover"></a>Planning voor regionale failover

Voer de volgende stappen uit om een cache in te stellen die is voor bereid op mogelijke failover:

1. Zorg ervoor dat uw back-end-opslag toegankelijk is in een tweede regio.
1. Wanneer u het primaire cache-exemplaar wilt maken, moet u ook voorbereiden om dit installatie proces in de tweede regio te repliceren. Deze items insluiten:

   1. Structuur van virtueel netwerk en subnet
   1. Cache capaciteit
   1. Details van opslag doel, namen en naam ruimte paden
   1. Details over client computers, als deze zich in dezelfde regio bevinden als de cache
   1. Opdracht koppelen voor gebruik door cache-clients

   > [!NOTE]
   > De Azure HPC-cache kan via een programma worden gemaakt, via een [Azure Resource Manager sjabloon](../azure-resource-manager/templates/overview.md) of door rechtstreeks toegang te krijgen tot de API. Neem contact op met het team van HPC-cache voor meer informatie.

## <a name="failover-example"></a>Voor beeld van failover

Stel dat u de Azure HPC-cache wilt zoeken in de regio VS-Oost. Het heeft toegang tot gegevens die zijn opgeslagen in uw on-premises Data Center.

U kunt een cache in de regio vs-West 2 gebruiken als back-up voor failover.

Wanneer u de cache in VS-Oost maakt, bereidt u een tweede cache voor op de implementatie in VS West 2. U kunt scripts of sjablonen gebruiken om deze voor bereiding te automatiseren.

In het geval van een regionale storing in VS-Oost, maakt u de cache die u hebt voor bereid in de regio vs West 2.

Nadat de cache is gemaakt, voegt u opslag doelen toe die verwijzen naar dezelfde on-premises gegevens opslag en gebruiken ze dezelfde geaggregeerde naam ruimte paden als de oude Storage-doelen van de cache.

Als de oorspronkelijke clients worden beïnvloed, maakt u nieuwe clients in de regio vs West 2 voor gebruik met de nieuwe cache.

Alle clients moeten de nieuwe cache koppelen, zelfs als de clients geen last hebben van de onderbreking van de regio. De nieuwe cache heeft andere koppel adressen dan de oude.

## <a name="learn-more"></a>Lees meer

De hand leiding voor de Azure-toepassings architectuur bevat meer informatie over het [herstellen van een service onderbreking voor de hele regio](<https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region>).
