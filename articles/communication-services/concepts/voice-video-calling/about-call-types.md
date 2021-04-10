---
title: Voice- en videoconcepten in Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Meer informatie over oproeptypen voor Communication Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/25/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 8a25f69019e194650bb6aa2f5b8ae19dd37fbc48
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729164"
---
# <a name="voice-and-video-concepts"></a>Voice- en videoconcepten

U kunt Azure Communication Services gebruiken om één-op-één of met een groep spraak- en video-oproepen te plaatsen en te ontvangen. U kunt bellen naar andere met internet verbonden apparaten en naar gewone telefoons. U kunt de Sdk's van de communicatie services java script, Android of iOS gebruiken om toepassingen te bouwen waarmee uw gebruikers naar elkaar kunnen praten in privé gesprekken of in groeps discussies. Azure Communication Services ondersteunt oproepen van en naar services of bots.

## <a name="call-types-in-azure-communication-services"></a>Oproeptypen in Azure Communication Services

Er zijn meerdere soorten oproepen die u kunt uitvoeren in Azure Communication Services. Het type oproepen dat u doet, bepaalt uw signaleringsschema, mediaverkeersstromen en prijsmodel.

### <a name="voice-over-ip-voip"></a>VoIP (Voice over IP)

Wanneer een gebruiker van uw toepassing een andere gebruiker van uw toepassing belt via een internet- of dataverbinding, wordt de oproep gedaan via Voice Over IP (VoIP). In dit geval gaan zowel het signaal als de mediastroom via internet.

### <a name="public-switched-telephone-network-pstn"></a>Openbaar telefoonnetwerk (PSTN)

Elke keer dat uw gebruikers communiceren met een traditioneel telefoonnummer, verlopen de oproepen via PSTN (openbaar telefoonnetwerk). Als u PSTN-oproepen wilt doen en ontvangen, moet u telefoniemogelijkheden toevoegen aan uw Azure Communication Services-resource. In dit geval gebruiken signalering en media een combinatie van IP-gebaseerde en PSTN-gebaseerde technologieën om uw gebruikers met elkaar in contact te brengen.

### <a name="one-to-one-call"></a>Eén-op-één-gesprek

Een een-op-een-aanroep van Azure Communication Services treedt op wanneer een van uw gebruikers verbinding maakt met een andere gebruiker met behulp van een van onze Sdk's. Het gesprek kan zowel via VoIP of PSTN verlopen.

### <a name="group-call"></a>Groepsgesprek

Een groepsgesprek op Azure Communication Services vindt plaats wanneer drie of meer deelnemers met elkaar verbinding maken. Elke combinatie van VoIP- en PSTN-verbonden gebruikers kan aanwezig zijn bij een groepsgesprek. Een één-op-één-gesprek kan worden omgezet in een groepsgesprek door meer deelnemers aan het gesprek toe te voegen. Een van deze deelnemers kan een bot zijn.

### <a name="supported-video-standards"></a>Ondersteunde videostandaarden
We ondersteunen H. 264 (MPEG-4).

### <a name="video-quality"></a>Met videokwaliteit 
We ondersteunen tot Full HD 1080p op de native SDK's (iOS, Android). Voor Web (JS) SDK ondersteunen we standaard HD 720p. De kwaliteit is afhankelijk van de beschikbare bandbreedte.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aan de slag met oproepen](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

Raadpleeg voor meer informatie de volgende artikelen:
- Lees meer over algemene [oproepstromen](../call-flows.md)
- [Telefoon nummer typen](../telephony-sms/plan-solution.md)
- Meer informatie over de [aanroepende SDK-mogelijkheden](../voice-video-calling/calling-sdk-features.md)
