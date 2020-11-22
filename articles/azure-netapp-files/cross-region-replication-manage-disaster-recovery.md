---
title: Herstel na nood gevallen beheren met Azure NetApp Files replicatie tussen regio's | Microsoft Docs
description: Hierin wordt beschreven hoe u herstel na nood gevallen beheert met behulp van Azure NetApp Files replicatie tussen regio's.
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
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: eab55f881c250c2e07717604d4ba00587a8b6031
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2020
ms.locfileid: "95243202"
---
# <a name="manage-disaster-recovery-using-cross-region-replication"></a>Herstel na nood gevallen met replicatie tussen regio's beheren 

Een voortdurende replicatie tussen de bron-en doel volumes (Zie [volume replicatie maken](cross-region-replication-create-peering.md)) bereidt u voor op een nood herstel gebeurtenis. 

Wanneer een dergelijke gebeurtenis zich voordoet, kunt u een [failover uitvoeren naar het doel volume](#fail-over-to-destination-volume), zodat de client kan lezen van en schrijven naar het doel volume. 

Na nood herstel kunt u een [Resync](#resync-replication) -bewerking uitvoeren om een failback uit te voeren naar het bron volume. Vervolgens [stelt u de bron-naar-doel-replicatie](#reestablish-source-to-destination-replication) opnieuw in en koppelt u het bron volume voor de client voor toegang. 

De details worden hieronder beschreven. 

## <a name="fail-over-to-destination-volume"></a>Failover naar doel volume

Wanneer u het doel volume moet activeren (bijvoorbeeld wanneer u een failover naar de doel regio wilt maken), moet u replicatie peering verstoren en het doel volume vervolgens koppelen.  

1. Selecteer het doel volume om replicatie peering te verstoren. Klik op **replicatie** onder Storage-service.  

2.  Controleer de volgende velden voordat u doorgaat:  
    * Zorg ervoor dat in de spiegel status ***gespiegelde** _ worden weer gegeven.   
        Probeer replicatie peering niet te verstoren als de spiegel status _Uninitialized * bevat.
    * Zorg ervoor dat de relatie status wordt weer gegeven ***niet-actieve** _.   
        Probeer replicatie peering niet te verstoren als de relatie status _Transferring * bevat.   

    Zie de status [van de replicatie relatie weer geven](cross-region-replication-display-health-status.md). 

3.  Klik op **verbreekt peering**.  

4.  Typ **Ja** wanneer u hierom wordt gevraagd en klik op de knop **afbreekpunt** . 

    ![Replicatie peering verstoren](../media/azure-netapp-files/cross-region-replication-break-replication-peering.png)

5.  Koppel het doel volume aan de hand van de stappen in [een volume koppelen of ontkoppelen voor virtuele Windows-of Linux-machines](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md).   
    Met deze stap kan een client toegang krijgen tot het doel volume.

## <a name="resync-volumes-after-disaster-recovery"></a><a name="resync-replication"></a>Volumes opnieuw synchroniseren na nood herstel

Nadat het nood herstel is uitgevoerd, kunt u het bron volume opnieuw activeren door een Resync-bewerking uit te voeren.  De hersynchronisatie-bewerking keert het replicatie proces om en synchroniseert gegevens van het doel volume naar het bron volume.  

> [!IMPORTANT] 
> De hersynchronisatie-bewerking overschrijft de gegevens van het bron volume met de gegevens van het doel volume.  De gebruikers interface waarschuwt u over de kans op gegevens verlies. U wordt gevraagd om de actie Resync te bevestigen voordat de bewerking wordt gestart.

1. Selecteer het *bron* volume om de replicatie opnieuw te synchroniseren. Klik op **replicatie** onder Storage-service. Klik vervolgens op opnieuw **synchroniseren**.  

2. Typ **Ja** wanneer u hierom wordt gevraagd en klik op opnieuw **synchroniseren**. 
 
    ![Replicatie synchroniseren](../media/azure-netapp-files/cross-region-replication-resync-replication.png)

3. Bewaak de status van het bron volume met de volgende stappen in de status [van de replicatie relatie weer geven](cross-region-replication-display-health-status.md).   
    Wanneer de status van het bron volume de volgende waarden bevat, wordt de hersynchronisatie voltooid en worden de wijzigingen die zijn aangebracht op het doel volume, nu vastgelegd op het bron volume:   

    * Gespiegelde status: *gespiegeld*  
    * Overdrachts status: *niet-actief*  

## <a name="reestablish-source-to-destination-replication"></a>Bron-naar-doel-replicatie herstellen

Nadat de hersynchronisatie-bewerking van de doel-naar-bron is voltooid, moet u replicatie peering opnieuw uitvoeren om de replicatie van bron naar bestemming opnieuw tot stand te brengen. U moet ook het bron volume opnieuw koppelen zodat de client er toegang toe heeft.  

1. De replicatie-peering verbreekt:  
    a. Selecteer het *doel* volume. Klik op **replicatie** onder Storage-service.  
    b. Controleer de volgende velden voordat u doorgaat:   
    * Zorg ervoor dat in de spiegel status ***gespiegelde** _ worden weer gegeven.   
    Probeer replicatie peering niet te verstoren als de spiegel status _uninitialized * bevat.  
    * Zorg ervoor dat de relatie status wordt weer gegeven ***niet-actieve** _.   
    Probeer replicatie peering niet te verstoren als de relatie status _transferring * bevat.    

        Zie de status [van de replicatie relatie weer geven](cross-region-replication-display-health-status.md). 

    c. Klik op **verbreekt peering**.   
    d. Typ **Ja** wanneer u hierom wordt gevraagd en klik op de knop **afbreekpunt** .  

2. Synchroniseer het bron volume met het doel volume:  
    a. Selecteer het *doel* volume. Klik op **replicatie** onder Storage-service. Klik vervolgens op opnieuw **synchroniseren**.   
    b. Typ **Ja** wanneer u hierom wordt gevraagd en klik op de knop **Resync** .

3. Koppel het bron volume opnieuw door de stappen te volgen in [een volume koppelen of ontkoppelen voor virtuele Windows-of Linux-machines](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md).  
    Met deze stap kan een client toegang krijgen tot het bron volume.

## <a name="next-steps"></a>Volgende stappen  

* [Replicatie in meerdere regio's](cross-region-replication-introduction.md)
* [Vereisten en overwegingen voor het gebruik van replicatie tussen regio's](cross-region-replication-requirements-considerations.md)
* [Status van replicatierelatie weergeven](cross-region-replication-display-health-status.md)
* [Metrische gegevens van de volume replicatie](azure-netapp-files-metrics.md#replication)
* [Volume replicaties of volumes verwijderen](cross-region-replication-delete.md)
* [Problemen met replicatie tussen regio's oplossen](troubleshoot-cross-region-replication.md)

