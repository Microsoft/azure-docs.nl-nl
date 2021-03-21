---
title: Gids voor probleem oplossing voor live streamen | Microsoft Docs
description: Dit artikel bevat suggesties voor het oplossen van problemen met het Azure Media Services van live streamen.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: 1b7a7ec746f5400fe65e3e1db88ae61e97ae710a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103009042"
---
# <a name="troubleshooting-guide-for-live-streaming"></a>Handleiding voor het oplossen van problemen met live streaming

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]  

Dit artikel bevat suggesties voor het oplossen van problemen met live streamen.

## <a name="issues-related-to-on-premises-encoders"></a>Problemen met betrekking tot on-premises encoders
Deze sectie bevat suggesties voor het oplossen van problemen met betrekking tot on-premises encoders die zijn geconfigureerd voor het verzenden van een enkele bitrate stroom naar AMS-kanalen die zijn ingeschakeld voor Live encoding.

### <a name="problem-would-like-to-see-logs"></a>Probleem: logboeken wilt weer geven
* **Mogelijk probleem**: kan encoder-logboeken niet vinden die kunnen helpen bij het oplossen van problemen.
  
  * **Telestream Wirecast**: u kunt meestal logboeken vinden onder C:\Users \{ username} \AppData\Roaming\Wirecast\ 
  * **Elementair Live**: u vindt koppelingen naar Logboeken op de beheer Portal. Klik op **Statistieken** en vervolgens op **Logboeken**. Op de pagina **logboek bestanden** ziet u een lijst met Logboeken voor alle LiveEvent-items. Selecteer het abonnement dat overeenkomt met uw huidige sessie. 
  * **Flash Media Live Encoder**: u kunt de **Logboekmap vinden...** door te navigeren naar het tabblad **coderings logboek** .

### <a name="problem-there-is-no-option-for-outputting-a-progressive-stream"></a>Probleem: er is geen optie voor het uitvoeren van een progressieve stroom
* **Mogelijk probleem**: de gebruikte code ring wordt niet automatisch interliniëring ongedaan gemaakt. 
  
    **Stappen voor probleem oplossing**: zoek naar een niet-interliniëring-optie in de coderings interface. Zodra de-interliniëring is ingeschakeld, controleert u opnieuw op de instellingen voor Progressieve uitvoer. 

### <a name="problem-tried-several-encoder-output-settings-and-still-unable-to-connect"></a>Probleem: er zijn verschillende uitvoer instellingen voor het coderings programma en er kan nog steeds geen verbinding worden gemaakt.
* **Mogelijk probleem**: het Azure encoding-kanaal is niet juist opnieuw ingesteld. 
  
    **Stappen voor probleem oplossing**: Zorg ervoor dat het coderings programma niet meer naar AMS pusht, stop en stel het kanaal opnieuw in. Wanneer het opnieuw wordt uitgevoerd, probeert u uw encoder te koppelen met de nieuwe instellingen. Als het probleem nog steeds niet wordt verholpen, probeert u een nieuw kanaal te maken, soms kunnen kanalen beschadigd raken na verschillende mislukte pogingen.  
* **Mogelijk probleem**: de instellingen voor de grootte van het GOP terug of het keyframe zijn niet optimaal. 
  
    **Stappen voor probleem oplossing**: aanbevolen GOP terug-grootte of het interval voor het keyframe is twee seconden. Sommige coderings Programma's berekenen deze instelling in het aantal frames, terwijl anderen seconden gebruiken. Bijvoorbeeld: bij het uitvoeren van 30 fps is de grootte van de GOP terug 60 frames, die gelijk is aan 2 seconden.  
* **Mogelijk probleem**: de stroom wordt geblokkeerd door gesloten poorten. 
  
    **Stappen voor probleem oplossing**: als streaming via RTMP, controleert u de firewall-en/of proxy-instellingen om te bevestigen dat de uitgaande poorten 1935 en 1936 zijn geopend. 

> [!NOTE]
> Als u na het volgen van de probleemoplossings stappen die u nog steeds niet kunt streamen, een ondersteunings ticket verzendt met behulp van de Azure Portal.
> 
> 

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

