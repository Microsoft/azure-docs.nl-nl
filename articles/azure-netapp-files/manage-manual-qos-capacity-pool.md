---
title: Een hand matige QoS-capaciteits groep voor Azure NetApp Files beheren | Microsoft Docs
description: Hierin wordt beschreven hoe u een capaciteits groep beheert die gebruikmaakt van het hand matige QoS-type, inclusief het instellen van een hand matige QoS-capaciteits groep en het wijzigen van een capaciteits groep om hand matige QoS te gebruiken.
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
ms.date: 02/04/2021
ms.author: b-juche
ms.openlocfilehash: 1c20190ba1a997ef85f4023a54ecea3c2a77ae53
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102183825"
---
# <a name="manage-a-manual-qos-capacity-pool"></a>Een handmatige QoS-capaciteitspool maken

In dit artikel wordt beschreven hoe u een capaciteits groep beheert die gebruikmaakt van het hand matige QoS-type.  

Zie [opslag hiërarchie van Azure NetApp files](azure-netapp-files-understand-storage-hierarchy.md) en [prestatie overwegingen voor Azure NetApp files](azure-netapp-files-performance-considerations.md) om inzicht te krijgen in de overwegingen met betrekking tot QoS-typen.  

## <a name="register-the-feature"></a>De functie registreren
De functie hand matige QoS-type is momenteel beschikbaar als preview-versie. Als dit de eerste keer is dat u deze functie gebruikt, moet u de functie eerst registreren.
  
1.  De functie registreren:

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFFlexPool
    ```

2. Controleer de status van de functie registratie: 

    > [!NOTE]
    > Het **RegistrationState** kan `Registering` tot 60 minuten duren voordat de status wordt gewijzigd in `Registered` . Wacht totdat de status is **geregistreerd** voordat u doorgaat.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFFlexPool
    ```
U kunt ook [Azure cli-opdrachten](/cli/azure/feature) gebruiken `az feature register` `az feature show` om de functie te registreren en de registratie status weer te geven. 

## <a name="set-up-a-new-manual-qos-capacity-pool"></a>Een nieuwe hand matige QoS-capaciteits groep instellen 

Een nieuwe capaciteits groep maken met het type hand matig QoS:

1. Volg de stappen in [een capaciteits pool instellen](azure-netapp-files-set-up-capacity-pool.md).  

2. Selecteer in het venster nieuwe capaciteits pool het **hand matige QoS** -type.  

## <a name="change-a-capacity-pool-to-use-manual-qos"></a><a name="change-to-qos"></a>Een capaciteits groep wijzigen om hand matig QoS te gebruiken

U kunt een capaciteits groep wijzigen die momenteel gebruikmaakt van het type automatische QoS om het hand matige QoS-type te gebruiken.  

> [!IMPORTANT]
> Het capaciteits type instellen op hand matige QoS is een permanente wijziging. U kunt een hand matig QoS-type capaciteits programma niet converteren naar een automatische QoS-capaciteits groep.  
> Tijdens de conversie kunnen doorvoer niveaus worden beperkt om te voldoen aan de doorvoer limieten voor volumes van het hand matige QoS-type. Zie [resource limieten voor Azure NetApp files](azure-netapp-files-resource-limits.md#resource-limits).

1. Klik op de Blade beheer voor uw NetApp-account op **capaciteits Pools** om bestaande capaciteits groepen weer te geven.   
 
2.  Klik op de capaciteits groep die u wilt wijzigen om hand matig QoS te gebruiken.

3.  Klik op **QoS-type wijzigen**. Stel vervolgens **nieuw QoS-type** in op **hand matig**. Klik op **OK**. 

![QoS-type wijzigen](../media/azure-netapp-files/change-qos-type.png)


## <a name="monitor-the-throughput-of-a-manual-qos-capacity-pool"></a>De door Voer van een hand matige QoS-capaciteits groep bewaken  

Er zijn metrische gegevens beschikbaar om u te helpen bij het bewaken van de lees-en schrijf doorvoer van een volume.  Zie de [metrische gegevens voor de Azure NetApp files](azure-netapp-files-metrics.md).  

## <a name="modify-the-allotted-throughput-of-a-manual-qos-volume"></a>De toegewezen door Voer van een hand matig QoS-volume wijzigen 

Als een volume zich in een hand matige QoS-capaciteits groep bevindt, kunt u de toegewezen volume doorvoer zo nodig wijzigen.

1. Selecteer op de pagina **volumes** het volume waarvan u de door voer wilt wijzigen.   

2. Klik op **door Voer wijzigen**. Geef de **door Voer (MIB/S)** op die u wilt. Klik op **OK**. 

    ![QoS-door Voer wijzigen](../media/azure-netapp-files/change-qos-throughput.png)

## <a name="next-steps"></a>Volgende stappen  

* [Een capaciteitspool instellen](azure-netapp-files-set-up-capacity-pool.md)
* [Metrische gegevens voor Azure NetApp Files](azure-netapp-files-metrics.md)
* [Prestatie-overwegingen voor Azure NetApp Files](azure-netapp-files-performance-considerations.md)
* [Problemen met capaciteitspools oplossen](troubleshoot-capacity-pools.md)
* [Opslaghiërarchie van Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Serviceniveau's voor Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Kostenmodel voor Azure NetApp Files](azure-netapp-files-cost-model.md)
* [Resourcelimieten voor Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Een NFS-volume maken](azure-netapp-files-create-volumes.md)
* [Een SMB-volume maken](azure-netapp-files-create-volumes-smb.md)
* [Een volume met dubbele protocollen maken](create-volumes-dual-protocol.md)
