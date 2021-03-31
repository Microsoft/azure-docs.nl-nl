---
title: Opslaghiërarchie van Azure NetApp Files | Microsoft Docs
description: In dit artikel wordt de opslaghiërarchie beschreven, waaronder Azure NetApp Files-accounts, capaciteitspools en volumes.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 435d74e771a9d887c87c9d10e6b525ac77cf97e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91278325"
---
# <a name="storage-hierarchy-of-azure-netapp-files"></a>Opslaghiërarchie van Azure NetApp Files

Voordat u een volume maakt in Azure NetApp Files, moet u een pool kopen en instellen voor de ingerichte capaciteit.  U heeft een NetApp-account nodig om een capaciteitspool in te stellen. Door inzicht te krijgen in de opslaghiërarchie kunt u uw Azure NetApp Files-resources beter instellen en beheren.

> [!IMPORTANT] 
> Azure NetApp Files biedt momenteel geen ondersteuning voor resourcemigratie tussen abonnementen.

## <a name="netapp-accounts"></a><a name="azure_netapp_files_account"></a>NetApp-accounts

- Een NetApp-account fungeert als een beheergroep van de bijbehorende capaciteitspools.  
- Een NetApp-account is niet hetzelfde als uw algemene Azure-opslagaccount. 
- Een NetApp-account heeft een regionaal bereik.   
- U kunt meerdere NetApp-accounts hebben in een regio, maar elk NetApp-account is gekoppeld aan slechts één regio.

## <a name="capacity-pools"></a><a name="capacity_pools"></a>Capaciteitspools

Begrijpen hoe capaciteitspools werken, helpt u bij het selecteren van de juiste capaciteitsgroeptypen die voldoen aan uw opslagnoden. 

### <a name="general-rules-of-capacity-pools"></a>Algemene regels van capaciteitspools

- Een capaciteitspool wordt gemeten aan de hand van de ingerichte capaciteit.   
    Zie [QoS-typen](#qos_types) voor meer informatie.  
- De capaciteit wordt ingericht met de vaste SKU's die u hebt gekocht (bijvoorbeeld een capaciteit van 4 TiB).
- Een capaciteitspool kan slechts één serviceniveau hebben.  
- Elke capaciteitspool kan deel uitmaken van slechts één NetApp-account. Er kunnen echter meerdere capaciteitspools binnen een NetApp-account bestaan.  
- Een capaciteitspool kan niet worden verplaatst tussen NetApp-accounts.   
  In het onderstaande [conceptueel diagram van een opslaghiërarchie](#conceptual_diagram_of_storage_hierarchy) kan Capaciteitspool 1 bijvoorbeeld niet worden verplaatst van het NetApp-account in US oost naar het NetApp-account in US west 2.  
- Een capaciteitspool kan pas worden verwijderd als alle volumes in de capaciteitspool zijn verwijderd.

### <a name="quality-of-service-qos-types-for-capacity-pools"></a><a name="qos_types"></a>Quality of Service-typen (QoS) voor capaciteitspools

Het QoS-type is een kenmerk van een capaciteitsgroep. Azure NetApp Files biedt twee QoS-typen capaciteitsgroepen aan. 

- *Automatisch (of auto)* QoS-type  

    Wanneer u een capaciteitspool maakt, is het standaard QoS-type auto.

    In een automatische QoS-capaciteitspool wordt de doorvoer automatisch toegewezen aan de volumes in de pool. Dit is evenredig aan het quotum van de grootte dat aan de volumes is toegewezen. 

    De maximale doorvoer die aan een volume wordt toegewezen, is afhankelijk van het serviceniveau van de capaciteitsgroep en het quotum voor de grootte van het volume. Zie [Serviceniveaus voor Azure NetApp Files](azure-netapp-files-service-levels.md) voor een voorbeeldberekening.

- <a name="manual_qos_type"></a>*Handmatige* QoS  

     > [!IMPORTANT] 
     > Om het handmatige QoS-type voor een capaciteitsgroep wilt gebruiken, is registratie vereist.  Zie [Een handmatige QoS-capaciteitspool maken](manage-manual-qos-capacity-pool.md).  

    U hebt de optie om het handmatige QoS-type voor een capaciteitspool te gebruiken.

    In een handmatige QoS-capaciteitspool kunt u de capaciteit en doorvoer voor een volume onafhankelijk toewijzen. De totale doorvoer van alle volumes die met een handmatige QoS-capaciteitspool zijn gemaakt, wordt beperkt door de totale doorvoer van de pool.  Deze wordt bepaald door de combinatie van poolgrootte en de doorvoer op serviceniveau. 

    Zo heeft een capaciteitspool van 4 TiB met het Ultra-serviceniveau een totale doorvoercapaciteit van 512 MiB/s (4 TiB x 128 MiB/s/TiB) die beschikbaar is voor de volumes.


## <a name="volumes"></a><a name="volumes"></a>Volumes

- Een volume wordt gemeten aan de hand van het logische capaciteitsverbruik en is schaalbaar. 
- Capaciteitsgebruik van een volume wordt in mindering gebracht op de ingerichte capaciteit van de pool.
- De doorvoer van een volume wordt in mindering gebracht op de beschikbare doorvoer van de pool. Zie [Handmatig QoS-type](#manual_qos_type).
- Elk volume is gekoppeld aan slechts één pool, maar een pool kan meerdere volumes bevatten. 

## <a name="conceptual-diagram-of-storage-hierarchy"></a><a name="conceptual_diagram_of_storage_hierarchy"></a>Conceptueel diagram van een opslaghiërarchie 
Het volgende voorbeeld toont de relaties tussen een Azure-abonnement, NetApp-accounts, capaciteitspools en volumes.   

![Conceptueel diagram van een opslaghiërarchie](../media/azure-netapp-files/azure-netapp-files-storage-hierarchy.png)

## <a name="next-steps"></a>Volgende stappen

- [Resourcelimieten voor Azure NetApp Files](azure-netapp-files-resource-limits.md)
- [Registreren voor Azure NetApp Files](azure-netapp-files-register.md)
- [Serviceniveau's voor Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Prestatie-overwegingen voor Azure NetApp Files](azure-netapp-files-performance-considerations.md)
- [Een capaciteitspool instellen](azure-netapp-files-set-up-capacity-pool.md)
- [Een handmatige QoS-capaciteitspool maken](manage-manual-qos-capacity-pool.md)
