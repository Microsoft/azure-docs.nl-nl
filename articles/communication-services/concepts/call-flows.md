---
title: Oproepstromen in Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Lees meer over oproepstromen in Azure Communication Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 8f7bfd63d858fb409286268c318c9f66474e3d53
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110913"
---
# <a name="call-flow-basics"></a>Basis principes van oproep stroom

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

De volgende sectie bevat een overzicht van de oproepstromen in Azure Communication Services. Signalerings- en mediastromen zijn afhankelijk van de typen oproepen die uw gebruikers maken. Voorbeelden van oproeptypen zijn een-op-een VoIP, een-op-een PSTN en groepsgesprekken met een combinatie van via VoIP en PSTN verbonden deelnemers. Bekijk [Oproeptypen](./voice-video-calling/about-call-types.md).

## <a name="about-signaling-and-media-protocols"></a>Over signalerings- en mediaprotocollen

Wanneer u een peer-to-peer of groepsgesprek tot stand brengt, worden er achter de schermen twee protocollen gebruikt: HTTP (REST) voor signalering en SRTP voor media.

Signa lering tussen de Sdk's of tussen Sdk's en communicatie Services-signaal controllers wordt verwerkt met HTTP REST (TLS). Voor mediaverkeer in real time (RTP) krijgt het UDP (User Datagram Protocol) de voor eur. Als het gebruik van UDP door uw firewall wordt voor komen, gebruikt de SDK de Transmission Control Protocol (TCP) voor media.

Laten we eens kijken naar de protocollen voor signalering en media in verschillende scenario's.

## <a name="call-flow-cases"></a>Voorbeelden oproepstromen

### <a name="case-1-voip-where-a-direct-connection-between-two-devices-is-possible"></a>Voorbeeld 1: VoIP waarbij een rechtstreekse verbinding tussen twee apparaten mogelijk is

Bij één-op-één VoIP- of videogesprekken verkiest verkeer de meest directe weg. "Direct pad" betekent dat als twee Sdk's elkaar rechtstreeks kunnen bereiken, een rechtstreekse verbinding tot stand wordt gebracht. Dit is doorgaans mogelijk wanneer twee Sdk's zich in hetzelfde subnet bevinden (bijvoorbeeld in een subnet 192.168.1.0/24) of twee wanneer elk van de apparaten in subnetten elkaar kan zien (de Sdk's in subnet 10.10.0.0/16 en 192.168.1.0/24 kunnen elkaar bereiken).

:::image type="content" source="./media/call-flows/about-voice-case-1.png" alt-text="Diagram van een rechtstreekse VOIP-oproep tussen gebruikers en Communication Services.":::

### <a name="case-2-voip-where-a-direct-connection-between-devices-is-not-possible-but-where-connection-between-nat-devices-is-possible"></a>Voorbeeld 2: VoIP waarbij een rechtstreekse verbinding tussen apparaten niet mogelijk is, maar waarbij de verbinding tussen NAT-apparaten mogelijk is

Als twee apparaten zich in subnetten bevinden die elkaar niet kunnen bereiken (bijvoorbeeld Anne werk van een koffie winkel en Bob werkt vanuit zijn thuis kantoor), maar de verbinding tussen de NAT-apparaten mogelijk is, maakt de aan de client zijde Sdk's connectiviteit met behulp van NAT-apparaten.

Voor Alice is dat de NAT van de koffiebar en voor Bob de NAT van zijn thuiskantoor. Het apparaat van Alice verzendt het externe adres van haar NAT en Bob doet hetzelfde. De Sdk's hebben inzicht in de externe adressen van een STUN-service (Session traversal-Hulpprogram Ma's voor NAT) die gratis door Azure Communication Services worden geleverd. De logica voor het afhandelen van de handshake tussen Alice en Bob is inge sloten in de Azure Communication Services, die Sdk's hebben verschaft. (U hebt geen aanvullende configuratie nodig)

:::image type="content" source="./media/call-flows/about-voice-case-2.png" alt-text="Diagram met een VOIP-oproep die een STUN-verbinding gebruikt.":::

### <a name="case-3-voip-where-neither-a-direct-nor-nat-connection-is-possible"></a>Voorbeeld 3: VoIP waarbij geen rechtstreekse of NAT-verbinding mogelijk is

Als een of beide client apparaten zich achter een symmetrische NAT bevinden, is een afzonderlijke Cloud service vereist voor het door sturen van de media tussen de twee Sdk's. Deze service heet TURN (Traversal Using Relays around NAT) en wordt ook door de Communication Services voorzien. De communicatie services die SDK aanroept, gebruiken automatisch beurt Services op basis van gedetecteerde netwerk omstandigheden. Het gebruik van de TURN-service van Microsoft wordt afzonderlijk in rekening gebracht.

:::image type="content" source="./media/call-flows/about-voice-case-3.png" alt-text="Diagram met een VOIP-oproep die een TURN-verbinding gebruikt.":::

### <a name="case-4-group-calls-with-pstn"></a>Voorbeeld 4: Groepsoproepen met PSTN

Zowel signalering als media voor PSTN-oproepen gebruiken de telefonieresource van Azure Communication Services. Deze resource is verbonden met andere providers.

PSTN-mediaverkeer loopt via een onderdeel dat de mediaprocessor wordt genoemd.

:::image type="content" source="./media/call-flows/about-voice-pstn.png" alt-text="Diagram waarop een PSTN-groepsgesprek met Communication Services wordt weergegeven.":::

> [!NOTE]
> Voor degenen die vertrouwd zijn met mediaverwerking, onze mediaprocessor ook een back-to-back gebruikersagent, zoals gedefinieerd in [RFC 3261 SIP: Session Initation Protocol](https://tools.ietf.org/html/rfc3261), wat betekent dat het codecs kan vertalen bij de verwerking van oproepen tussen Microsoft en netwerken van providers. De signaleringscontroller van Azure Communication Services is de implementatie van een SIP-proxy door Microsoft volgens dezelfde RFC.

Voor groepsgesprekken lopen media en signalering altijd via de back-end van Azure Communication Services. De audio en/of video van alle deelnemers wordt gemengd in de mediaprocessor. Alle leden van een groepsgesprek sturen hun audio-en/of videostreams naar de mediaprocessor, die gemengde mediastreams terugstuurt.

Het standaard real-time protocol (RTP) voor groepsgesprekken is UDP (User Datagram Protocol).

> [!NOTE]
> De mediaprocessor kan dienen als Multipoint Control Unit (MCU) of een Selective Forwarding Unit (SFU)

:::image type="content" source="./media/call-flows/about-voice-group-calls.png" alt-text="Diagram waarop de UDP-mediaprocesstroom binnen Communication Services wordt weergegeven.":::

Als de SDK UDP voor media niet kan gebruiken vanwege firewall beperkingen, wordt er geprobeerd om de Transmission Control Protocol (TCP) te gebruiken. Houd er rekening mee dat de mediaprocessor UDP nodig heeft. Wanneer dit gebeurt, wordt de TURN-service van Communication Services toegevoegd aan het groepsgesprek om TCP naar UDP te vertalen. In dit geval worden er kosten in rekening gebracht voor TURN, tenzij TURN-mogelijkheden handmatig zijn uitgeschakeld.

:::image type="content" source="./media/call-flows/about-voice-group-calls-2.png" alt-text="Diagram waarop de TCP-mediaprocesstroom binnen Communication Services wordt weergegeven.":::

### <a name="case-5-communication-services-sdk-and-microsoft-teams-in-a-scheduled-teams-meeting"></a>Case 5: communicatie Services SDK en micro soft teams in een geplande teams vergadering

De signaal stroom via de signalerings controller. Media stromen via de media processor. De signalerings controller en de media processor worden gedeeld tussen communicatie Services en micro soft-teams.

:::image type="content" source="./media/call-flows/teams-communication-services-meeting.png" alt-text="Diagram van de client van de communicatie Services-SDK en teams in een geplande teams vergadering.":::



## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aan de slag met oproepen](../quickstarts/voice-video-calling/getting-started-with-calling.md)

De volgende documenten zijn mogelijk interessant voor u:

- Meer informatie over [oproeptypen](../concepts/voice-video-calling/about-call-types.md)
- Meer informatie over [Client-serverarchitectuur](./client-and-server-architecture.md)
- Meer informatie over [topologieën voor oproep stromen](./detailed-call-flows.md)
