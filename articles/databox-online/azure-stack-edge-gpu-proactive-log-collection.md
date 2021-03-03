---
title: Proactieve logboek verzameling begrijpen op Azure Stack Edge Pro-apparaat
description: Hierin wordt beschreven hoe proactieve logboek verzameling wordt uitgevoerd op een Azure Stack Edge Pro-apparaat en hoe dit wordt uitgeschakeld.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: alkohli
ms.openlocfilehash: 064af116112f0b530ac0cc9b5755dcec2cf0bd07
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101722060"
---
# <a name="proactive-log-collection-on-your-azure-stack-edge-device"></a>Proactieve logboek verzameling op uw Azure Stack edge-apparaat

Met proactieve logboek verzameling worden de systeem status indicatoren op uw Azure Stack edge-apparaat verzameld, zodat u problemen met apparaten efficiënt kunt oplossen. Proactieve logboek verzameling is standaard ingeschakeld. In dit artikel wordt beschreven wat er wordt geregistreerd, hoe micro soft de gegevens verwerkt en hoe u proactieve logboek verzameling kunt in-of uitschakelen. 

De informatie in dit artikel is van toepassing op Azure Stack Edge Pro GPU, Azure Stack Edge Pro R en Azure Stack Edge mini-R-apparaten.

## <a name="about-proactive-log-collection"></a>Over proactieve logboek verzameling

Micro soft-klanten ondersteuning en technische teams gebruiken systeem logboeken van uw Azure Stack edge-apparaat om problemen op te sporen en op te lossen die tijdens de bewerking kunnen worden uitgevoerd. Proactieve logboek verzameling is een methode die micro soft waarschuwt dat er een probleem/gebeurtenis is gedetecteerd door het Azure Stack edge-apparaat van de klant. Zie [proactieve logboek verzamelings indicatoren](#proactive-log-collection-indicators) voor gebeurtenissen die worden bijgehouden. De ondersteunings logboeken die betrekking hebben op het probleem, worden automatisch geüpload naar een Azure Storage-account dat door micro soft wordt beheerd en geregeld. Microsoft Ondersteuning en micro soft-technici bekijken deze ondersteunings Logboeken om te bepalen wat de beste actie is om het probleem met de klant op te lossen.

> [!NOTE]
> Deze logboeken worden alleen gebruikt voor fout opsporing en om klanten ondersteuning te bieden in het geval van problemen.


## <a name="enabling-proactive-log-collection"></a>Proactieve logboek verzameling inschakelen

Proactieve logboek verzameling is standaard ingeschakeld. U kunt proactieve logboek verzameling uitschakelen wanneer u het apparaat probeert te activeren via de lokale gebruikers interface. 

1. Ga in de lokale web-UI van het apparaat naar de pagina **aan de slag** .

2. Op de tegel **Activering** selecteert u **Activeren**. 

    ![Pagina 1 'Clouddetails' van lokale webgebruikersinterface](./media/azure-stack-edge-pro-r-deploy-activate/activate-1.png)

3. In het deelvenster **Activeren**:

   1. Voer de **Activeringssleutel** in die u hebt ontvangen in [De activeringssleutel krijgen voor Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key).

      Wanneer de functie voor proactieve logboek verzameling is geactiveerd, wordt deze standaard ingeschakeld, zodat micro soft logboeken kan verzamelen op basis van de integriteits status van het apparaat. Deze logboeken worden geüpload naar een Azure Storage-account. 

      U kunt proactieve logboek verzameling uitschakelen om te voor komen dat micro soft logboeken verzamelt.

   1. Als u proactieve logboek verzameling wilt uitschakelen op het apparaat, selecteert u **uitschakelen**.

   1. Selecteer **Activate**.

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

Naast proactieve logboek verzameling, waarmee specifieke logboeken worden verzameld die betrekking hebben op een specifiek gedetecteerd probleem, kunnen andere logboek verzamelingen een veel beter inzicht krijgen in de status en het gedrag van het systeem. Normaal gesp roken kunnen deze andere logboeken worden verzameld tijdens een ondersteunings aanvraag of door micro soft worden geactiveerd op basis van telemetriegegevens van het apparaat.

## <a name="handling-data"></a>Gegevens verwerken

Wanneer proactieve logboek verzameling is ingeschakeld, gaat de klant ermee akkoord om micro soft-logboeken te verzamelen van het Azure Stack edge-apparaat, zoals hier wordt beschreven. De klant erkent ook en stemt ermee in dat deze logboeken worden geüpload en bewaard in een Azure Storage account dat door micro soft wordt beheerd en beheerd.

Micro soft gebruikt de gegevens voor het oplossen van alleen systeem status en problemen. De gegevens worden niet gebruikt voor marketing, reclame of andere commerciële doel einden zonder toestemming van de klant. 

De gegevens die door micro soft worden verzameld, worden verwerkt volgens onze standaard privacy-prak tijken. Als een klant besluit de toestemming voor proactieve logboek verzameling in te trekken, worden alle gegevens die zijn verzameld met toestemming voordat de intrekking wordt ingetrokken, niet beïnvloed.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [verzamelen van een ondersteunings pakket](azure-stack-edge-gpu-troubleshoot.md#collect-support-package).