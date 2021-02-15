---
title: Integratie concepten van PSTN-telefonie voor Azure Communication Services
description: Meer informatie over het integreren van PSTN-oproep mogelijkheden in uw Azure Communication Services-toepassing.
author: boris-bazilevskiy
manager: nmurav
services: azure-communication-services
ms.author: bobazile
ms.date: 02/09/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 5f7c2b0168c914c836aca7b551a9c22d4718c7b6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100422446"
---
# <a name="telephony-concepts"></a>Telefonie concepten

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Azure Communication Services die client bibliotheken aanroepen, kunnen worden gebruikt om telefonie en PSTN toe te voegen aan uw toepassingen. Op deze pagina vindt u een overzicht van de belangrijkste concepten en mogelijkheden van telefonie. Zie de [aanroep bibliotheek](../../quickstarts/voice-video-calling/calling-client-samples.md) voor meer informatie over specifieke talen en mogelijkheden voor de client bibliotheek.

## <a name="overview-of-telephony"></a>Overzicht van telefonie
Wanneer uw gebruikers met een traditioneel telefoon nummer communiceren, worden de aanroepen van het PSTN (open bare telefoon netwerk) vergemakkelijkt. Als u PSTN-oproepen wilt doen en ontvangen, moet u telefoniemogelijkheden toevoegen aan uw Azure Communication Services-resource. In dit geval gebruiken signalering en media een combinatie van IP-gebaseerde en PSTN-gebaseerde technologieën om uw gebruikers met elkaar in contact te brengen. Communicatie Services biedt twee aparte manieren om het PSTN-netwerk te bereiken: Azure-Cloud aanroepen en SIP-interface.

### <a name="azure-cloud-calling"></a>Azure-Cloud aanroepen

Een eenvoudige manier om PSTN-connectiviteit toe te voegen aan uw app of service, micro soft is in dit geval uw telecommunicatie-provider. U kunt cijfers rechtstreeks van micro soft kopen. Azure Cloud Calling is een alles-in-the-Cloud-telefonie oplossing voor communicatie Services. Dit is de eenvoudigste optie die ACS verbindt met het open bare telefoonnet werk (PSTN) om telefonische en mobiele telefoons wereld wijd in te scha kelen. Met deze optie fungeert micro soft als uw PSTN-provider, zoals wordt weer gegeven in het volgende diagram:

![Azure-Cloud diagram voor aanroepen.](../media/telephony-concept/azure-calling-diagram.png)

Als u ' ja ' beantwoordt aan het volgende, is Azure-Cloud aanroepen de juiste oplossing voor u:
- Azure-Cloud aanroepen is beschikbaar in uw regio.
- U hoeft uw huidige PSTN-provider niet te bewaren.
- U wilt door micro soft beheerde toegang tot het PSTN gebruiken.

Met deze optie:
- U ontvangt cijfers rechtstreeks van micro soft en kan telefoons wereld wijd bellen.
- U hebt geen implementatie of onderhoud van een on-premises implementatie nodig, omdat Azure-Cloud aanroepen van Azure Communication Services niet actief is.
- Opmerking: als dat nodig is, kunt u ervoor kiezen om met behulp van de SIP-interface verbinding te maken met een ondersteunde sessie rand controller (SBC) voor interoperabiliteit met PBXs van derden, analoge apparaten en andere telefonische apparatuur van derden die door de SBC worden ondersteund.

Voor deze optie is een ononderbroken verbinding met Azure Communication Services vereist.

### <a name="sip-interface"></a>SIP-interface

Met deze optie kunt u verouderde on-premises telefonie en uw transporteur van de keuze aan Azure Communication Services verbinden. Het biedt PSTN-oproep mogelijkheden voor uw ACS-toepassingen, zelfs als Azure-Cloud aanroepen niet beschikbaar is in uw land/regio. 

![SIP-interface diagram.](../media/telephony-concept/sip-interface-diagram.png)

Als u ' ja ' op een van de volgende vragen beantwoordt, is de SIP-interface de juiste oplossing voor u:

- U wilt ACS gebruiken met PSTN-aanroepen.
- U moet uw huidige PSTN-provider behouden.
- U wilt de route ring combi neren, met een aantal aanroepen via Azure Cloud Call, een aantal via uw provider.
- U moet samenwerkt met PBXs en/of apparatuur van derden, zoals overhead pagina's, analoge apparaten, enzovoort.

Met deze optie:

- U verbindt uw eigen ondersteunde SBC voor Azure Communication Services zonder dat er extra on-premises software nodig is.
- U kunt met behulp van een wille keurige Telephony-provider met ACS gebruiken.
- U kunt ervoor kiezen om deze optie te configureren en beheren, of deze kan worden geconfigureerd en beheerd door uw provider of partner (vraag of uw provider of partner deze optie heeft).
- U kunt de interoperabiliteit tussen uw telefoon apparatuur configureren, zoals een PBX van derden en analoge apparaten, en ACS.

Voor deze optie is het volgende vereist:

- Er is een niet-onderbroken verbinding met Azure.
- Een ondersteunde SBC implementeren en onderhouden.
- Een contract met een luchtvaart maatschappij van derden. (Tenzij geïmplementeerd als een optie om verbinding te maken met een externe PBX, analoge apparaten of andere telefoon apparatuur voor gebruikers die communiceren met communicatie Services.)

## <a name="next-steps"></a>Volgende stappen

### <a name="conceptual-documentation"></a>Conceptuele documentatie

- [Typen telefoonnummers in Azure Communication Services](./plan-solution.md)
- [SIP-interface plannen](./sip-interface-infrastructure.md)
- [Prijzen](../pricing.md)

### <a name="quickstarts"></a>Quickstarts

- [Een telefoonnummer aanvragen](../../quickstarts/telephony-sms/get-phone-number.md)
- [Bellen naar telefoon](../../quickstarts/voice-video-calling/pstn-call.md)
