---
title: Volume replicaties of-volumes verwijderen voor Azure NetApp Files replicatie tussen regio's | Microsoft Docs
description: Hierin wordt beschreven hoe u een replicatie verbinding verwijdert die niet meer nodig is tussen de bron-en de doel volumes.
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
ms.date: 11/18/2020
ms.author: b-juche
ms.openlocfilehash: 5ce7a591acd8203775808457219b0ec392cd696e
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2020
ms.locfileid: "95249887"
---
# <a name="delete-volume-replications-or-volumes"></a>Volume replicaties of volumes verwijderen

In dit artikel wordt beschreven hoe u volume replicatie verwijdert. Ook wordt beschreven hoe u het bron-of doel volume verwijdert.

## <a name="delete-volume-replications"></a>Volume replicaties verwijderen

U kunt de replicatie verbinding tussen de bron-en doel volumes beëindigen door de volume replicatie te verwijderen. U moet de replicatie verwijderen van het doel volume. De Verwijder bewerking verwijdert alleen de verificatie van de replicatie. de bron of het doel volume wordt niet verwijderd. 

1. Zorg ervoor dat de replicatie peering is verbroken voordat u de volume replicatie verwijdert. De replicatie-peering verbreekt: 

    1. Selecteer het *doel* volume. Klik op **replicatie** onder Storage-service.  

    2.  Controleer de volgende velden voordat u doorgaat:  
        * Zorg ervoor dat in de spiegel status ***gespiegelde** _ worden weer gegeven.   
            Probeer replicatie peering niet te verstoren als de spiegel status _Uninitialized * bevat.
        * Zorg ervoor dat de relatie status wordt weer gegeven ***niet-actieve** _.   
            Probeer replicatie peering niet te verstoren als de relatie status _Transferring * bevat.   

        Zie de status [van de replicatie relatie weer geven](cross-region-replication-display-health-status.md). 

    3.  Klik op **verbreekt peering**.  

    4.  Typ **Ja** wanneer u hierom wordt gevraagd en klik op **onderbreken**. 

        ![Replicatie peering verstoren](../media/azure-netapp-files/cross-region-replication-break-replication-peering.png)


1. Selecteer **replicatie** van het bron-of doel volume om de volume replicatie te verwijderen.  

2. Klik op **Verwijderen**.    

3. Bevestig de verwijdering door **Ja** te typen en op **verwijderen** te klikken.   

    ![Replicatie verwijderen](../media/azure-netapp-files/cross-region-replication-delete-replication.png)

## <a name="delete-source-or-destination-volumes"></a>Bron-of doel volumes verwijderen

Als u het bron-of doel volume wilt verwijderen, moet u de volgende stappen uitvoeren in de beschreven volg orde. Anders treedt de `Volume with replication cannot be deleted` fout op.  

1. [Verwijder de volume replicatie](#delete-volume-replications)vanaf het doel volume.   

2. Verwijder het doel-of bron volume naar wens door met de rechter muisknop op de naam van het volume te klikken en **verwijderen** te selecteren.   

    ![Scherm opname van het snelmenu van een volume.](../media/azure-netapp-files/cross-region-replication-delete-volume.png)

## <a name="next-steps"></a>Volgende stappen  

* [Replicatie in meerdere regio's](cross-region-replication-introduction.md)
* [Vereisten en overwegingen voor het gebruik van replicatie tussen regio's](cross-region-replication-requirements-considerations.md)
* [Status van replicatierelatie weergeven](cross-region-replication-display-health-status.md)
* [Problemen met kruis regio's oplossen-replicatie](troubleshoot-cross-region-replication.md)

