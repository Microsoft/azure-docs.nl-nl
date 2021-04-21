---
title: Het serviceniveau van een volume voor een volume dynamisch Azure NetApp Files | Microsoft Docs
description: Beschrijft hoe u het serviceniveau van een volume dynamisch kunt wijzigen.
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
ms.date: 01/14/2021
ms.author: b-juche
ms.openlocfilehash: 3409387adb1e722d8368907d731e02983dd287fc
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830156"
---
# <a name="dynamically-change-the-service-level-of-a-volume"></a>Het serviceniveau van een volume dynamisch wijzigen

> [!IMPORTANT] 
> Het dynamisch wijzigen van het serviceniveau van een replicatiedoelvolume wordt momenteel niet ondersteund.

U kunt het serviceniveau van een bestaand volume wijzigen door het volume te verplaatsen naar een andere capaciteitspool die gebruikmaakt van het [serviceniveau](azure-netapp-files-service-levels.md) dat u voor het volume wilt gebruiken. Voor deze in-place wijziging van het serviceniveau voor het volume is niet vereist dat u gegevens migreert. Het heeft ook geen invloed op de toegang tot het volume.  

Met deze functionaliteit kunt u op aanvraag voldoen aan uw workloadbehoeften.  Als u de prestaties wilt verbeteren, kunt u een bestaand volume zodanig wijzigen dat het een hoger serviceniveau kan gebruiken. Als u de kosten wilt optimaliseren, kunt u het volume aanpassen om een lager serviceniveau te gebruiken. Als het volume zich momenteel bijvoorbeeld in een  capaciteitspool met het Standard-serviceniveau en u wilt dat het volume het Premium-serviceniveau gebruikt, kunt u het volume dynamisch verplaatsen naar een capaciteitspool die gebruikmaakt van het *Premium-serviceniveau.*   

De capaciteitspool waar u het volume naar wilt verplaatsen, moet al bestaan. De capaciteitspool kan andere volumes bevatten.  Als u het volume naar een nieuwe capaciteitspool wilt verplaatsen, moet u de capaciteitspool maken [voordat](azure-netapp-files-set-up-capacity-pool.md) u het volume verplaatst.  

## <a name="considerations"></a>Overwegingen

* Nadat het volume is verplaatst naar een andere capaciteitspool, hebt u geen toegang meer tot de vorige volumeactiviteitenlogboeken en metrische gegevens over volumes. Het volume begint met nieuwe activiteitenlogboeken en metrische gegevens onder de nieuwe capaciteitspool.

* Als u een volume verplaatst naar een capaciteitspool van een hoger serviceniveau (bijvoorbeeld van *Standard* naar *Premium* of Ultra-serviceniveau), moet u ten minste zeven dagen wachten voordat u dat *volume* opnieuw kunt verplaatsen naar een capaciteitspool van een lager serviceniveau (bijvoorbeeld van *Ultra* naar *Premium* of *Standard*).   

## <a name="register-the-feature"></a>De functie registreren

De functie voor het verplaatsen van een volume naar een andere capaciteitspool is momenteel in preview. Als dit de eerste keer is dat u deze functie gebruikt, moet u de functie eerst registreren.

1. Registreer de functie: 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFTierChange
    ```

2. Controleer de status van de functieregistratie: 

    > [!NOTE]
    > De **RegistrationState** kan maximaal 60 minuten in de status zijn `Registering` voordat u in verandert in `Registered` . Wacht totdat de status Geregistreerd is **voordat** u doorgaat.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFTierChange
    ```
U kunt ook [Azure CLI-opdrachten en](/cli/azure/feature) `az feature register` gebruiken om de functie te registreren en de `az feature show` registratiestatus weer te geven. 
 
## <a name="move-a-volume-to-another-capacity-pool"></a>Een volume verplaatsen naar een andere capaciteitspool

1.  Klik op de pagina Volumes met de rechtermuisknop op het volume waarvan u het serviceniveau wilt wijzigen. Selecteer **Pool wijzigen.**

    ![Klik met de rechtermuisknop op volume](../media/azure-netapp-files/right-click-volume.png)

2. Selecteer in het venster Pool wijzigen de capaciteitspool waarin u het volume wilt verplaatsen. 

    ![Pool wijzigen](../media/azure-netapp-files/change-pool.png)

3.  Klik op **OK**.


## <a name="next-steps"></a>Volgende stappen  

* [Serviceniveau's voor Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Een capaciteitspool instellen](azure-netapp-files-set-up-capacity-pool.md)
* [Problemen oplossen voor het wijzigen van de capaciteitspool van een volume](troubleshoot-capacity-pools.md#issues-when-changing-the-capacity-pool-of-a-volume)
