---
title: Voice- en videoconcepten in Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Meer informatie over oproeptypen voor Communication Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: eea54be9d159c6aacbbc24e2fe8edf874f8e2d83
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103492333"
---
# <a name="voice-and-video-concepts"></a>Voice- en videoconcepten

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


U kunt Azure Communication Services gebruiken om één-op-één of met een groep spraak- en video-oproepen te plaatsen en te ontvangen. U kunt bellen naar andere met internet verbonden apparaten en naar gewone telefoons. U kunt de JavaScript-, Android- of iOS-clientbibliotheken van Communication Services gebruiken om toepassingen te bouwen waarmee uw gebruikers met elkaar kunnen praten in privégesprekken of in groepsdiscussies. Azure Communication Services ondersteunt oproepen van en naar services of bots.

## <a name="call-types-in-azure-communication-services"></a>Oproeptypen in Azure Communication Services

Er zijn meerdere soorten oproepen die u kunt uitvoeren in Azure Communication Services. Het type oproepen dat u doet, bepaalt uw signaleringsschema, mediaverkeersstromen en prijsmodel.

### <a name="voice-over-ip-voip"></a>VoIP (Voice over IP)

Wanneer een gebruiker van uw toepassing een andere gebruiker van uw toepassing belt via een internet- of dataverbinding, wordt de oproep gedaan via Voice Over IP (VoIP). In dit geval gaan zowel het signaal als de mediastroom via internet.

### <a name="public-switched-telephone-network-pstn"></a>Openbaar telefoonnetwerk (PSTN)

Elke keer dat uw gebruikers communiceren met een traditioneel telefoonnummer, verlopen de oproepen via PSTN (openbaar telefoonnetwerk). Als u PSTN-oproepen wilt doen en ontvangen, moet u telefoniemogelijkheden toevoegen aan uw Azure Communication Services-resource. In dit geval gebruiken signalering en media een combinatie van IP-gebaseerde en PSTN-gebaseerde technologieën om uw gebruikers met elkaar in contact te brengen.

### <a name="one-to-one-call"></a>Eén-op-één-gesprek

Een één-op-één-gesprek op Azure Communication Services vindt plaats wanneer een van uw gebruikers verbinding maakt met een andere gebruiker via een van onze clientbibliotheken. Het gesprek kan zowel via VoIP of PSTN verlopen.

### <a name="group-call"></a>Groepsgesprek

Een groepsgesprek op Azure Communication Services vindt plaats wanneer drie of meer deelnemers met elkaar verbinding maken. Elke combinatie van VoIP- en PSTN-verbonden gebruikers kan aanwezig zijn bij een groepsgesprek. Een één-op-één-gesprek kan worden omgezet in een groepsgesprek door meer deelnemers aan het gesprek toe te voegen. Een van deze deelnemers kan een bot zijn.

### <a name="supported-video-standards"></a>Ondersteunde videostandaarden
We ondersteunen H.264 (MPEG-4)

### <a name="video-quality"></a>Met videokwaliteit 
We ondersteunen tot Full HD 1080p op de native SDK's (iOS, Android). Voor Web (JS) SDK ondersteunen we standaard HD 720p. De kwaliteit is afhankelijk van de beschikbare bandbreedte.

### <a name="rooms-concept"></a>Ruimtes
Ruimtes is een reeks API's en SDK's waarmee u eenvoudig audio-, video-, schermdeling-, PSTN- en sms-interacties kunt toevoegen aan uw website of eigen toepassing.
Tijdens de preview kunt u de groeps-ID gebruiken om deel te nemen aan hetzelfde gesprek. U kunt zoveel groeps-id's aanmaken als u nodig hebt en de gebruikers scheiden op basis van de 'ruimtes'. Als u verdergaat, worden er meer besturingselementen voor "ruimtes" geïntroduceerd

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aan de slag met oproepen](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

Raadpleeg voor meer informatie de volgende artikelen:
- Lees meer over algemene [oproepstromen](../call-flows.md)
- [Telefoon nummer typen](../telephony-sms/plan-solution.md)
- Meer informatie over de [mogelijkheden van de clientbibliotheek voor oproepen](../voice-video-calling/calling-sdk-features.md)
