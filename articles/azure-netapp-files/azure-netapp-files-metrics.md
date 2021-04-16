---
title: Metrische gegevens voor Azure NetApp Files | Microsoft Docs
description: Azure NetApp Files biedt metrische gegevens over toegewezen opslag, daadwerkelijk opslaggebruik, volume-IOPS en latentie. Gebruik deze metrische gegevens om inzicht te krijgen in het gebruik en de prestaties.
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
ms.date: 04/12/2021
ms.author: b-juche
ms.openlocfilehash: b581470a886ff73739edfee7f45c64295eeeb1f0
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388602"
---
# <a name="metrics-for-azure-netapp-files"></a>Metrische gegevens voor Azure NetApp Files

Azure NetApp Files biedt metrische gegevens over toegewezen opslag, daadwerkelijk opslaggebruik, volume-IOPS en latentie. Door deze metrische gegevens te analyseren, krijgt u een beter inzicht in het gebruikspatroon en de volumeprestaties van uw NetApp-accounts.  

U kunt metrische gegevens voor een capaciteitspool of volume vinden door de **capaciteitspool of** het volume te **selecteren.**  Klik vervolgens op **Metrische gegevens** om de beschikbare metrische gegevens weer te geven: 

[![Momentopname die laat zien hoe u naar de metrische pull-down navigeert. ](../media/azure-netapp-files/metrics-navigate-volume.png)](../media/azure-netapp-files/metrics-navigate-volume.png#lightbox)

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>Metrische gegevens over gebruik voor capaciteitspools

- *Toegewezen poolgrootte*   
    De inrichtende grootte van de pool.

- *Pool toegewezen aan volumegrootte*  
    Het totale volumequotum (GiB) in een bepaalde capaciteitspool (dat wil zeggen, het totaal van de inrichten grootten van de volumes in de capaciteitspool).  
    Deze grootte is de grootte die u hebt geselecteerd tijdens het maken van het volume.  

- *Verbruikte poolgrootte*  
    Het totale aantal logische ruimte (GiB) dat wordt gebruikt voor volumes in een capaciteitspool.  

- *Totale grootte van momentopname voor de pool*    
    De som van de grootte van de momentopname van alle volumes in de pool.

## <a name="usage-metrics-for-volumes"></a><a name="volumes"></a>Metrische gegevens over gebruik voor volumes

- *Percentage volume verbruikte grootte*    
    Het percentage van het verbruikte volume, inclusief momentopnamen.  
- *Toegewezen volumegrootte*   
    De inrichtende grootte van een volume
- *Volumequotumgrootte*    
    De quotumgrootte (GiB) waar het volume mee wordt ingericht.   
- *Volume verbruikte grootte*   
    Logische grootte van het volume (gebruikte bytes).  
    Deze grootte omvat logische ruimte die wordt gebruikt door actieve bestandssystemen en momentopnamen.  
- *Grootte van momentopname van volume*   
   De grootte van alle momentopnamen in een volume.  

## <a name="performance-metrics-for-volumes"></a>Metrische prestatiegegevens voor volumes

- *Gemiddelde leeslatentie*   
    De gemiddelde tijd voor het lezen van het volume in milliseconden.
- *Gemiddelde schrijflatentie*   
    De gemiddelde tijd voor schrijftijd van het volume in milliseconden.
- *IOPS lezen*   
    Het aantal lees leest naar het volume per seconde.
- *IOPS schrijven*   
    Het aantal schrijf schrijft naar het volume per seconde.
<!-- These two metrics are not yet available, until ~ 2020.09
- *Read MiB/s*   
    Read throughput in bytes per second.
- *Write MiB/s*   
    Write throughput in bytes per second.
--> 
<!-- ANF-4128; 2020.07
- *Pool Provisioned Throughput*   
    The total throughput a capacity pool can provide to its volumes based on "Pool Provisioned Size" and "Service Level".
- *Pool Allocated to Volume Throughput*   
    The total throughput allocated to volumes in a given capacity pool (that is, the total of the volumes' allocated throughput in the capacity pool).
-->

<!-- ANF-6443; 2020.11
- *Pool Consumed Throughput*    
    The total throughput being consumed by volumes in a given capacity pool.
-->


## <a name="volume-replication-metrics"></a><a name="replication"></a>Metrische gegevens voor volumereplicatie

> [!NOTE] 
> * De grootte van de  netwerkoverdracht (bijvoorbeeld de totale overdrachtsgegevens voor volumereplicatie) kan verschillen van de bron- of doelvolumes van een replicatie tussen regio's. Dit gedrag is het resultaat van een efficiënte replicatie-engine die wordt gebruikt om de kosten voor netwerkoverdracht te minimaliseren.
> * Metrische gegevens voor volumereplicatie worden momenteel ingevuld voor replicatiedoelvolumes en niet voor de bron van de replicatierelatie.

- *Is de status van volumereplicatie in orde?*   
    De voorwaarde van de replicatierelatie. Een goede status wordt aangeduid met `1` . Een slechte status wordt aangeduid met `0` .

- *Wordt volumereplicatie overgeplaatst?*    
    Of de status van de volumereplicatie 'overdracht' is. 
 
- *Vertragingstijd volumereplicatie*   
    De hoeveelheid tijd in seconden waarmee de gegevens op de mirror achterblijven op de bron. 

- *Duur van laatste overdracht voor volumereplicatie*   
    De hoeveelheid tijd in seconden die nodig was om de laatste overdracht te voltooien. 

- *Grootte van laatste overdracht voor volumereplicatie*    
    Het totale aantal bytes dat is overgedragen als onderdeel van de laatste overdracht. 

- *Voortgang van volumereplicatie*    
    De totale hoeveelheid gegevens die wordt overgedragen voor de huidige overdrachtsbewerking. 

- *Totale overdracht volumereplicatie*   
    De cumulatieve bytes die zijn overgedragen voor de relatie. 

## <a name="next-steps"></a>Volgende stappen

* [Informatie over de opslaghiërarchie van Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Een capaciteitspool instellen](azure-netapp-files-set-up-capacity-pool.md)
* [Een volume maken voor Azure NetApp Files](azure-netapp-files-create-volumes.md)
