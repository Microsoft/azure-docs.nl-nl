---
title: Een capaciteitspool instellen voor Azure NetApp Files | Microsoft Docs
description: In dit artikel wordt beschreven hoe u een capaciteitspool instelt, zodat u hierin volumes kunt maken.
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
ms.topic: how-to
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 2b52ad50854092cddd7b9e79cbeebd4a83017081
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91325404"
---
# <a name="set-up-a-capacity-pool"></a>Een capaciteitspool instellen

Als u een capaciteitspool instelt, kunt u hierin volumes maken.  

## <a name="before-you-begin"></a>Voordat u begint 

U moet al een NetApp-account hebben gemaakt.   

[Een NetApp-account maken](azure-netapp-files-create-netapp-account.md)

## <a name="steps"></a>Stappen 

1. Ga naar de beheerblade voor uw NetApp-account en klik vervolgens op **Capaciteitspools** in het navigatiedeelvenster.  
    
    ![Navigeren naar capaciteitspool](../media/azure-netapp-files/azure-netapp-files-navigate-to-capacity-pool.png)

2. Klik op **+ Pools toevoegen** om een nieuwe capaciteitspool te maken.   
    Het venster Nieuwe capaciteitspool wordt weergegeven.

3. Geef de volgende informatie op voor de nieuwe capaciteitspool:  
   * **Naam**  
     Geef de naam op voor de capaciteitspool.  
     De naam van de capaciteitspool moet uniek zijn voor elk NetApp-account.

   * **Service niveau**   
     In dit veld worden de doelprestaties voor de capaciteitspool weergegeven.  
     Geef het service niveau voor de capaciteits groep op: [**Ultra**](azure-netapp-files-service-levels.md#Ultra), [**Premium**](azure-netapp-files-service-levels.md#Premium)of [**Standard**](azure-netapp-files-service-levels.md#Standard).

    * **Size**     
     Geef de grootte op van de capaciteitspool die u aanschaft.        
     De minimale grootte van de capaciteitspool is 4 TiB. U kunt een pool maken met een grootte die een veelvoud is van 4 TiB.   

   * **QoS**   
     Geef op of de capaciteits groep het type **hand matig** of **automatisch** QoS moet gebruiken.  

     Zie [opslag hiërarchie](azure-netapp-files-understand-storage-hierarchy.md) en [prestatie overwegingen](azure-netapp-files-performance-considerations.md) om inzicht te krijgen in de QoS-typen.  

     > [!IMPORTANT] 
     > **QoS-type** instellen op **hand matig** is permanent. U kunt een hand matige QoS-capaciteits groep niet converteren om automatische QoS te gebruiken. U kunt echter een capaciteits groep voor automatische QoS converteren om hand matig QoS te gebruiken. Zie [een capaciteits groep wijzigen om hand matig QoS te gebruiken](manage-manual-qos-capacity-pool.md#change-to-qos).   
     > Om het handmatige QoS-type voor een capaciteitsgroep wilt gebruiken, is registratie vereist. Zie [Een handmatige QoS-capaciteitspool maken](manage-manual-qos-capacity-pool.md#register-the-feature). 

    ![Nieuwe capaciteitspool](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. Klik op **Create**.

## <a name="next-steps"></a>Volgende stappen 

- [Opslag hiërarchie](azure-netapp-files-understand-storage-hierarchy.md) 
- [Serviceniveau's voor Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Pagina met Azure NetApp Files prijzen](https://azure.microsoft.com/pricing/details/storage/netapp/)
- [Een handmatige QoS-capaciteitspool maken](manage-manual-qos-capacity-pool.md)
- [Een subnet delegeren aan Azure NetApp Files](azure-netapp-files-delegate-subnet.md)
