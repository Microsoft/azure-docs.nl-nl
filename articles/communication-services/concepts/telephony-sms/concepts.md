---
title: Sms-concepten in Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Meer informatie over sms-concepten in Communication Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: c866629677790447d0ed730ae9cc62cb37e338e9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103495501"
---
# <a name="sms-concepts"></a>Sms-concepten

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Met Azure Communication Services kunt u sms-tekstberichten verzenden en ontvangen met behulp van de sms-clientbibliotheken van Communication Services. Deze clientbibliotheken kunnen worden gebruikt ter ondersteuning van klantenservicescenario's, afspraakherinneringen, tweeledige verificatie en andere communicatie in real time. Met sms in Communication Services kunt u op een betrouwbare manier berichten verzenden en inzicht krijgen in de bezorgings- en responspercentages voor uw campagnes.

De belangrijkste kenmerken van de SMS-clientbibliotheken van Azure Communication Services zijn:

-  **Eenvoudige** installatie om sms-mogelijkheden toe te voegen aan uw toepassingen.
- **Snelle** ondersteuning voor berichten door het gebruik van gratis nummers voor A2P (Application to Person) in de Verenigde Staten.
- **Tweerichtingsgesprekken** voor scenario's zoals klantondersteuning, waarschuwingen en afspraakherinneringen.
- **Betrouwbare levering** met realtime leveringsrapporten voor berichten die vanuit uw toepassing worden verzonden.
- **Analytische gegevens** om uw gebruikspatronen en de klantbetrokkenheid bij te houden.
- **Afmelding** ondersteuning om automatisch afmelding voor gratis nummers te detecteren en te respecteren. Afmeldingen voor Amerikaanse gratis nummers zijn verplicht en worden afgedwongen door Amerikaanse providers.
  - STOP - Als de ontvanger van een tekstbericht zich wil afmelden, kan deze 'STOP' versturen naar het gratis nummer. De provider verzendt het volgende standaardantwoord op STOP: *‘NETWORK MSG: U hebt gereageerd met het woord 'stop', waarmee alle tekstberichten worden geblokkeerd die vanaf dit nummer worden verzonden. Stuur ‘unstop’ terug om opnieuw berichten te ontvangen.’*
  - START/UNSTOP - Als de ontvanger zich opnieuw wil abonneren op sms-berichten van een gratis nummer, kan hij of zij 'START' of 'UNSTOP' naar het gratis nummer verzenden. De provider verzendt het volgende standaardantwoord op START/UNSTOP: *‘NETWORK MSG: U hebt 'unstop' geantwoord en zal opnieuw berichten ontvangen van dit nummer.'*
  - Azure Communication Services detecteert het STOP-bericht en blokkeert alle verdere berichten aan de ontvanger. Het leveringsrapport geeft een mislukte levering aan met status van het bericht als ‘Afzender geblokkeerd voor deze ontvanger.’
  - De berichten STOP, UNSTOP en START worden naar u doorgestuurd. Azure Communication Services moedigt u aan deze afmeldingen te controleren en te implementeren om ervoor te zorgen dat er geen verdere verzendpogingen worden gedaan naar ontvangers die zich voor uw communicatie hebben afgemeld.


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aan de slag met het verzenden van sms-berichten](../../quickstarts/telephony-sms/send.md)

De volgende documenten zijn mogelijk interessant voor u:

- Uzelf bekend maken met de [sms-clientbibliotheek](../telephony-sms/sdk-features.md)
- Koop een [telefoonnummer](../../quickstarts/telephony-sms/get-phone-number.md) dat geschikt is voor sms
- [Typen telefoonnummers in Azure Communication Services](../telephony-sms/plan-solution.md)
