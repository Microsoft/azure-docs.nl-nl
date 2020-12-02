---
title: Proactieve logboek verzameling begrijpen op Azure Stack Edge Pro-apparaat
description: Hierin wordt beschreven hoe proactieve logboek verzameling wordt uitgevoerd op een Azure Stack Edge Pro-apparaat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: alkohli
ms.openlocfilehash: f79de47ec0ffad11f650054b581dbbaae030edbf
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466435"
---
# <a name="proactive-log-collection-on-your-azure-stack-edge-device"></a>Proactieve logboek verzameling op uw Azure Stack edge-apparaat

U kunt proactieve logboek verzameling op uw Azure Stack edge-apparaat inschakelen op basis van de status indicatoren van het systeem om problemen met apparaten efficiënt op te lossen. In dit artikel wordt beschreven wat proactieve logboek verzameling is, hoe u deze functie inschakelt en hoe gegevens worden verwerkt wanneer proactieve logboek verzameling is ingeschakeld.
   
De informatie in dit artikel is van toepassing op Azure Stack Edge Pro GPU, Azure Stack Edge Pro R en Azure Stack Edge mini-R-apparaten.

## <a name="about-proactive-log-collection"></a>Over proactieve logboek verzameling

Micro soft-klanten ondersteuning en technische teams gebruiken systeem logboeken van uw Azure Stack edge-apparaat om problemen op te sporen en op te lossen die tijdens de bewerking kunnen worden uitgevoerd. Proactieve logboek verzameling is een methode die micro soft waarschuwt dat er een probleem/gebeurtenis (Zie de sectie Indica tors voor proactieve logboek verzameling voor gebeurtenissen die worden bijgehouden) is gedetecteerd door het Azure Stack edge-apparaat van de klant. De ondersteunings logboeken die betrekking hebben op het probleem, worden automatisch geüpload naar een Azure Storage account dat door micro soft wordt beheerd en beheerd. Microsoft Ondersteuning en micro soft-technici bekijken deze ondersteunings Logboeken om te bepalen wat de beste actie is om het probleem met de klant op te lossen.    

> [!NOTE]
> Deze logboeken worden alleen gebruikt voor fout opsporing en bieden ondersteuning voor de klanten in het geval van problemen. 


## <a name="enabling-proactive-log-collection"></a>Proactieve logboek verzameling inschakelen

U kunt de proactieve logboek verzameling inschakelen wanneer u het apparaat probeert te activeren via de lokale gebruikers interface. 

1. Ga in de lokale webgebruikersinterface van het apparaat naar de pagina **Aan de slag**.
2. Op de tegel **Activering** selecteert u **Activeren**. 

    ![Lokale webgebruikersinterface "Cloud Details" pagina 1](./media/azure-stack-edge-pro-r-deploy-activate/activate-1.png)
    
3. In het deel venster **activeren** :
    1. Voer de **activerings sleutel** in die u hebt ontvangen [om de activerings sleutel voor Azure stack Edge Pro R](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key)op te halen.

    1. U kunt proactieve logboek verzameling inschakelen om micro soft logboeken te laten verzamelen op basis van de integriteits status van het apparaat. De logboeken die op deze manier zijn verzameld, worden geüpload naar een Azure Storage-account.
    
    1. Selecteer **Toepassen**.

    ![Pagina 2 'Clouddetails' van lokale webgebruikersinterface](./media/azure-stack-edge-pro-r-deploy-activate/activate-2.png)



## <a name="proactive-log-collection-indicators"></a>Proactieve logboek verzamelings indicatoren

Nadat de proactieve logboek verzameling is ingeschakeld, worden logboeken automatisch geüpload wanneer een van de volgende gebeurtenissen op het apparaat wordt gedetecteerd:  


|Waarschuwing/fout/voor waarde  |Beschrijving  |
|---------|---------|
|AcsUnhealthyCondition     |Consistente Azure-Services zijn beschadigd.         |
|IOTEdgeAgentNotRunningCondition      |IoT Edge agent is niet actief.         |
|UpdateInstallFailedEvent | Kan de update niet installeren.        |

 
Micro soft blijft nieuwe gebeurtenissen toevoegen aan de voor gaande lijst. Er is geen aanvullende toestemming nodig voor nieuwe gebeurtenissen. Raadpleeg deze pagina voor meer informatie over de meest recente proactieve logboek verzamelings indicatoren.    
 

## <a name="other-log-collection-methods"></a>Andere methoden voor het verzamelen van Logboeken

Naast proactieve logboek verzameling, waarmee specifieke logboeken worden verzameld die betrekking hebben op een specifiek gedetecteerd probleem, zijn er andere logboek verzamelingen die veel meer inzicht kunnen krijgen in de systeem status en het gedrag. Normaal gesp roken kunnen deze andere logboek verzamelingen worden uitgevoerd tijdens een ondersteunings aanvraag of door micro soft worden geactiveerd op basis van de telemetriegegevens van het apparaat.  

## <a name="handling-data"></a>Gegevens verwerken

Als een klant proactieve logboek verzameling inschakelt, gaan ze ermee akkoord dat micro soft logboeken verzamelt van het Azure Stack edge-apparaat, zoals hier wordt beschreven. De klant erkent ook en stemt ermee in dat deze logboeken worden geüpload en bewaard in een Azure Storage account dat door micro soft wordt beheerd en beheerd.

Micro soft gebruikt de gegevens voor het oplossen van alleen systeem status en problemen. De gegevens worden niet gebruikt voor marketing, reclame of andere commerciële doel einden zonder toestemming van de klant. 

De gegevens die door micro soft worden verzameld, worden verwerkt volgens onze standaard privacy-prak tijken. Als een klant besluit de toestemming voor proactieve logboek verzameling in te trekken, worden alle gegevens die zijn verzameld met toestemming voordat de intrekking wordt ingetrokken, niet beïnvloed.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [verzamelen van een ondersteunings pakket](azure-stack-edge-gpu-troubleshoot.md#collect-support-package).