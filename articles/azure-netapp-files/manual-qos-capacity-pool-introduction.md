---
title: Hand matige QoS-capaciteits groep van Azure NetApp Files | Microsoft Docs
description: Biedt een inleiding tot de hand matige QoS-capaciteits groep en verwijst naar aanvullende informatie.
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
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: b-juche
ms.openlocfilehash: 13acee8b21adf946192544afcea17b4a8d9b9ec9
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99581122"
---
# <a name="manual-qos-capacity-pool"></a>Handmatige QoS-capaciteitspool

Dit artikel bevat een inleiding tot de functionaliteit van de capaciteits groep hand matige Quality of Service (QoS).

## <a name="how-manual-qos-differs-from-auto-qos"></a>Hoe hand matige QoS verschilt van automatische QoS

Het [QoS-type](azure-netapp-files-understand-storage-hierarchy.md#qos_types) is een kenmerk van een capaciteits groep. Azure NetApp Files biedt twee QoS-typen capaciteits Pools: automatisch (standaard) en hand matig.  

In een *hand matige* QoS-capaciteits groep kunt u de capaciteit en door Voer voor een volume onafhankelijk toewijzen. Zie [resource limieten voor Azure NetApp files](azure-netapp-files-resource-limits.md#resource-limits)voor minimale en maximale doorvoer niveaus. De totale doorvoer van alle volumes die met een handmatige QoS-capaciteitspool zijn gemaakt, wordt beperkt door de totale doorvoer van de pool. Deze wordt bepaald door de combinatie van poolgrootte en de doorvoer op serviceniveau. 

In een *automatische* QoS-capaciteits groep wordt de door Voer automatisch toegewezen aan de volumes in de pool, evenredig aan het grootte quotum dat aan de volumes is toegewezen.  

Zie [opslag hiërarchie van Azure NetApp files](azure-netapp-files-understand-storage-hierarchy.md) en [prestatie overwegingen voor Azure NetApp files](azure-netapp-files-performance-considerations.md) voor overwegingen met betrekking tot QoS-typen.

## <a name="example-of-using-manual-qos"></a>Voor beeld van het gebruik van hand matige QoS

Wanneer u een hand matige QoS-capaciteits groep gebruikt met bijvoorbeeld een SAP HANA systeem, een Oracle-data base of andere werk belastingen waarvoor meerdere volumes zijn vereist, kan de capaciteits groep worden gebruikt om deze toepassings volumes te maken.  Elk volume kan de afzonderlijke grootte en door Voer bieden om te voldoen aan de toepassings vereisten.  Bekijk de [voor beelden van doorvoer limieten voor volumes in een hand matige QoS-capaciteits groep](azure-netapp-files-service-levels.md#throughput-limit-examples-of-volumes-in-a-manual-qos-capacity-pool) voor meer informatie over de voor delen.  

## <a name="how-to-specify-the-manual-qos-type"></a>Het type hand matige QoS opgeven

Wanneer u [een capaciteits pool maakt](azure-netapp-files-set-up-capacity-pool.md), kunt u opgeven dat de capaciteits groep het hand matige QoS-type moet gebruiken.  U kunt ook [een bestaande capaciteits groep wijzigen](manage-manual-qos-capacity-pool.md#change-to-qos) om het hand matige QoS-type te gebruiken. 

Het capaciteits type instellen op hand matige QoS is een permanente wijziging. U kunt een hand matig QoS-type capaciteits programma niet converteren naar een automatische QoS-capaciteits groep. 

Als u het hand matige QoS-type wilt gebruiken, moet u [de functie registreren](manage-manual-qos-capacity-pool.md#register-the-feature).  

## <a name="next-steps"></a>Volgende stappen

* [Een handmatige QoS-capaciteitspool maken](manage-manual-qos-capacity-pool.md)
* [Een capaciteitspool instellen](azure-netapp-files-set-up-capacity-pool.md)
* [Opslag hiërarchie](azure-netapp-files-understand-storage-hierarchy.md) 
* [Serviceniveau's voor Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Prestatie-overwegingen voor Azure NetApp Files](azure-netapp-files-performance-considerations.md)
* [Kostenmodel voor Azure NetApp Files](azure-netapp-files-cost-model.md)
* [Resourcelimieten voor Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Een NFS-volume maken](azure-netapp-files-create-volumes.md)
* [Een SMB-volume maken](azure-netapp-files-create-volumes-smb.md)
* [Een volume met dubbele protocollen maken](create-volumes-dual-protocol.md)
* [Metrische gegevens voor Azure NetApp Files](azure-netapp-files-metrics.md)
* [Problemen met capaciteitspools oplossen](troubleshoot-capacity-pools.md)
