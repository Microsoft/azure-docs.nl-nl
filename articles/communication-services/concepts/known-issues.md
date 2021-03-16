---
title: 'Azure Communication Services: veelgestelde vragen/bekende problemen'
description: Meer informatie over Azure Communication Services
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: troubleshooting
ms.service: azure-communication-services
ms.openlocfilehash: 2c6ac34d8daf00578cb1d03833a28eb8535708b7
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103493639"
---
# <a name="faq--known-issues"></a>Veelgestelde vragen/bekende problemen
Dit artikel bevat informatie over bekende problemen en veelgestelde vragen met betrekking tot Azure Communication Services.

## <a name="faq"></a>Veelgestelde vragen

### <a name="why-is-the-quality-of-my-video-degraded"></a>Waarom is de kwaliteit van mijn video verslechterd?

De kwaliteit van de video stromen wordt bepaald door de grootte van de client-side renderer die is gebruikt om die stroom te initiëren. Wanneer u zich abonneert op een externe stream, bepaalt een ontvanger zijn eigen oplossing op basis van de afmetingen van de client zijde van de afzender.

### <a name="why-is-it-not-possible-to-enumerateselect-micspeaker-devices-on-safari"></a>Waarom is het niet mogelijk om mic/luid sprekers apparaten op Safari te inventariseren/selecteren?

Toepassingen kunnen Mic/speaker-apparaten (zoals Bluetooth) op Safari iOS/iPad niet inventariseren/selecteren. Het is een beperking van het besturings systeem: er is altijd maar één apparaat.

Voor Safari op MacOS: de spreker kan niet worden geïnventariseerd/geselecteerd via communicatie Services Apparaatbeheer: deze moeten worden geselecteerd via het besturings systeem. Als u Chrome op MacOS gebruikt, kan de app apparaten opsommen/selecteren via de communicatie Services-Apparaatbeheer.

## <a name="known-issues"></a>Bekende problemen

Deze sectie bevat informatie over bekende problemen die zijn gekoppeld aan Azure Communication Services.

### <a name="repeatedly-switching-video-devices-may-cause-video-streaming-to-temporarily-stop"></a>Het herhaaldelijk wisselen van video apparaten kan ertoe leiden dat video-streaming tijdelijk stopt

Scha kelen tussen video apparaten kan ertoe leiden dat uw video stroom wordt onderbroken terwijl de stroom wordt opgehaald van het geselecteerde apparaat.

#### <a name="possible-causes"></a>Mogelijke oorzaken
Het streamen van en scha kelen tussen media apparaten is reken intensief. Een regel matig overschakelen kan leiden tot verminderde prestaties. Ontwikkel aars wordt geadviseerd om één apparaat stroom te stoppen voordat ze een nieuwe starten.
