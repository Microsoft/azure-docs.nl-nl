---
title: VEELGESTELDE VRAGEN OVER SMS
titleSuffix: An Azure Communication Services concept document
description: VEELGESTELDE VRAGEN OVER SMS
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 03/26/2021
ms.topic: reference
ms.service: azure-communication-services
ms.openlocfilehash: 1ba7c730542adb74356d71f2482cce57e633cb65
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105646132"
---
# <a name="sms-faq"></a>VEELGESTELDE VRAGEN OVER SMS

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]
## <a name="can-a-customer-use-azure-communication-services-for-emergency-purposes"></a>Kan een klant Azure-communicatie Services gebruiken voor nood gevallen?

Azure Communication Services biedt geen ondersteuning voor de functionaliteit van text-to-911 in de Verenigde Staten, maar het is mogelijk dat u hiervoor verplicht bent onder de regels van de Federal Communications Commission (FCC).  U moet beoordelen of de tekst-naar-911-regels van de FCC van toepassing zijn op uw service of toepassing. Voor zover u deze regels dekt, bent u verantwoordelijk voor het routeren van 911-tekst berichten naar nood oproep centrums die deze aanvragen. U kunt uw eigen afleverings model voor tekst naar 911 bepalen, maar één methode die wordt geaccepteerd door de FCC, is dat de systeem eigen kiezer automatisch wordt gestart op het mobiele apparaat van de gebruiker om 911 teksten via de onderliggende mobiele provider te leveren.

## <a name="are-there-any-limits-on-sending-messages"></a>Gelden er limieten voor het verzenden van berichten?

Om ervoor te zorgen dat we de hoge kwaliteit van de service blijven aanbieden die consistent is met onze Sla's, worden de frequentie limieten (voor elke primitieve) door Azure Communication Services toegepast. Ontwikkel aars die onze Api's buiten de limiet aanroepen, ontvangen een respons van 429 HTTP-status code. Als uw bedrijf vereisten heeft die de limiet overschrijden, kunt u ons een e-mail sturen naar phone@microsoft.com .

Frequentie limieten voor SMS:

|Bewerking|Bereik|Tijds bestek (en)| Limiet (aanvraag nummer) | Bericht eenheden per minuut|
|---------|-----|-------------|-------------------|-------------------------|
|Bericht verzenden|Per getal|60|200|200|

## <a name="how-does-azure-communication-services-handle-opt-outs-for-toll-free-numbers"></a>Hoe verwerkt Azure Communication Services opt-outs voor gratis nummers?

Afmeldingen voor Amerikaanse gratis nummers zijn verplicht en worden afgedwongen door Amerikaanse providers.
- STOP - Als de ontvanger van een tekstbericht zich wil afmelden, kan deze 'STOP' versturen naar het gratis nummer. De vervoerder verzendt het volgende standaard antwoord voor STOP: "netwerk bericht: u hebt gereageerd met het woord" Stop ", waardoor alle teksten die vanuit dit nummer worden verzonden, worden geblokkeerd. Tekst terug als u wilt stoppen om opnieuw berichten te ontvangen. "
- START/UNSTOP - Als de ontvanger zich opnieuw wil abonneren op sms-berichten van een gratis nummer, kan hij of zij 'START' of 'UNSTOP' naar het gratis nummer verzenden. De provider verzendt het volgende standaardantwoord op START/UNSTOP: ‘NETWORK MSG: U hebt 'unstop' geantwoord en zal opnieuw berichten ontvangen van dit nummer.'
- Azure Communication Services detecteert het STOP-bericht en blokkeert alle verdere berichten aan de ontvanger. Het leveringsrapport geeft een mislukte levering aan met status van het bericht als ‘Afzender geblokkeerd voor deze ontvanger.’
- De berichten STOP, UNSTOP en START worden naar u doorgestuurd. Azure Communication Services moedigt u aan deze afmeldingen te controleren en te implementeren om ervoor te zorgen dat er geen verdere verzendpogingen worden gedaan naar ontvangers die zich voor uw communicatie hebben afgemeld.

## <a name="how-can-i-receive-messages-using-azure-communication-services"></a>Hoe kan ik berichten ontvangen met Azure Communication Services?

Klanten van Azure Communication Services kunnen Azure Event Grid gebruiken om inkomende berichten te ontvangen. Volg deze [Snelstartgids](https://docs.microsoft.com/azure/communication-services/quickstarts/telephony-sms/handle-sms-events) om uw gebeurtenis-raster in te stellen voor het ontvangen van berichten.

## <a name="can-i-sendreceive-long-messages-2048-chars"></a>Kan ik lange berichten verzenden/ontvangen (>2048 tekens)?

Azure Communication Services ondersteunt het verzenden en ontvangen van lange berichten via SMS. Sommige draadloze providers of apparaten kunnen echter anders reageren bij het ontvangen van lange berichten.

## <a name="how-are-messages-sent-to-landline-numbers-treated"></a>Hoe worden berichten verzonden naar vaste getallen behandeld?

In de Verenigde Staten wordt door Azure Communication Services niet gecontroleerd op vaste nummers en wordt geprobeerd om deze te verzenden naar vervoerders voor levering. Er worden kosten in rekening gebracht voor berichten die worden verzonden naar vaste nummers. 

## <a name="can-i-send-messages-to-multiple-recipients"></a>Kan ik berichten verzenden naar meerdere ontvangers?


Ja, u kunt een aanvraag met meerdere ontvangers maken. Volg deze [Quick](https://docs.microsoft.com/azure/communication-services/quickstarts/telephony-sms/send?pivots=programming-language-csharp) start om berichten te verzenden naar meerdere ontvangers.
