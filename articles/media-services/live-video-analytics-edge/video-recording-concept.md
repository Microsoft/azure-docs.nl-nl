---
title: Video-opname-Azure
description: In de context van een video beheersysteem voor CCTV-camera's verwijst de video-opname naar het proces van het vastleggen van video van de camera's en het opnemen ervan voor weer gave via mobiele en browser-apps. Video-opname kan worden gecategoriseerd als continue video-opname en video-opname op basis van gebeurtenissen.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: c03472f577e67d5b2705a2ebe3f53e85e0849819
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84260944"
---
# <a name="video-recording"></a>Video-opname

In de context van een video beheersysteem voor CCTV-camera's verwijst de video-opname naar het proces van het vastleggen van video van de camera's en het opnemen ervan voor weer gave via mobiele en browser-apps. Video-opname kan worden gecategoriseerd als continue video-opname en video-opname op basis van gebeurtenissen. 

## <a name="continuous-video-recording"></a>Continue video-opname  

Continue video-opname (CVR) verwijst naar het proces van het continu vastleggen van alle video die wordt vastgelegd vanuit een video bron. CVR zorgt ervoor dat alle video beschikbaar is (gedicteerd door het [opname beleid](#recording-policy)) om op een later tijdstip te analyseren en/of te controleren.

## <a name="event-based-video-recording"></a>Video-opname op basis van gebeurtenissen  

Video-opname op basis van gebeurtenissen (EVR) verwijst naar het proces van het opnemen van video geactiveerd door een gebeurtenis. De betrokken gebeurtenis kan afkomstig zijn als gevolg van het verwerken van het video signaal zelf of kan afkomstig zijn uit een onafhankelijke bron (bijvoorbeeld van een deur sensor). Video-opname op basis van gebeurtenissen (EVR) kan leiden tot besparing van opslag, maar er zijn aanvullende onderdelen vereist die de gebeurtenissen genereren en de video-opname (volgens een vooraf gedefinieerd beleid) activeren. Met andere woorden, EVR moet extra beslissingen nemen over de gebeurtenissen die video-opnamen moeten activeren en het beleid dat is gekoppeld aan de video-opname (ook wel het registratie beleid genoemd). Een voor beeld van een beleid kan er ongeveer als volgt uitzien: Noteer de video gedurende 2 minuten, te beginnen bij 30 seconden vóór de tijd van de gebeurtenis. De betreffende gebeurtenissen kunnen afkomstig zijn als gevolg van video verwerking op de camera zelf. Een voor beeld: verschillende camera's ondersteunen het genereren van een signaal gebeurtenis voor Motion-detectie wanneer er bewegingen worden gedetecteerd binnen een vooraf geconfigureerde zone van belang in de View Port van de camera. De gebeurtenissen kunnen ook worden gegenereerd door de video te verwerken op een ander apparaat dat de video van de camera vastlegt en analyseert met behulp van eenvoudige beeldverwerkings technieken of geavanceerde machine learning modellen. 

## <a name="choosing-recording-modes"></a>Opname modi kiezen  

De keuze van of CVR of EVR moet worden gebruikt, is afhankelijk van de bedrijfs doelen. Live video Analytics op IoT Edge biedt platform mogelijkheden voor zowel CVR als EVR. Meer informatie hierover vindt u in de [CVR](continuous-video-recording-concept.md) -en [EVR](event-based-video-recording-concept.md) -scenario artikelen.

## <a name="recording-policy"></a>Registratie beleid  

Het registratie beleid verwijst naar de beleids regels die de begin-en eind tijd vastleggen van de opname (in het geval van EVR), de registratie duur en het verwijderen van de registratie bepalen. Met de beleids regels voor vastleggen kunt u de opslag kosten in balans brengen met bedrijfs vereisten. Beleids regels voor vastleggen verschillen tussen CVR en EVR. Voor CVR definieert het registratie beleid hoeveel dagen video moet worden opgeslagen (bijvoorbeeld de afgelopen 7 dagen). Voor EVR definieert het registratie beleid wanneer de opname moet beginnen en eindigen, samen met de duur van de video. Meer informatie hierover vindt u in de [CVR](continuous-video-recording-concept.md) -en [EVR](event-based-video-recording-concept.md) -scenario artikelen.

## <a name="next-steps"></a>Volgende stappen

* [Beweging detecteren, videofragmenten opnemen in Azure Media Services](detect-motion-record-video-clips-media-services-quickstart.md)
* [Beweging detecteren, video clips opnemen naar edge-apparaten](detect-motion-record-video-clips-edge-devices-quickstart.md)

