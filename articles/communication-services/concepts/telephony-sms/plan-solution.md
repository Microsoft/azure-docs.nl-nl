---
title: Typen telefoonnummers in Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Meer informatie over het effectief gebruiken van verschillende soorten telefoon nummers voor SMS en telefonie.
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 03/10/2021
ms.topic: overview
ms.custom: references_regions
ms.service: azure-communication-services
ms.openlocfilehash: 7b295c6dabfce6a624d93d75991163f464cfd46e
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625225"
---
# <a name="phone-number-types-in-azure-communication-services"></a>Typen telefoonnummers in Azure Communication Services



[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

> [!IMPORTANT]
> De beschik baarheid van telefoon nummers is momenteel beperkt tot betaalde Azure-abonnementen die een factuur adres hebben in de Verenigde Staten-en communicatie services-resources die een Amerikaanse gegevens locatie hebben. Telefoon nummers kunnen niet worden aangeschaft bij proef accounts of Azure-gratis tegoeden gebruiken. Ga voor meer informatie naar de sectie [abonnements geschiktheid](#azure-subscriptions-eligibility) van dit document.


Met Azure Communication Services kunt u telefoonnummers gebruiken om gesprekken te voeren en sms-berichten te verzenden met het openbare telefoonnetwerk (PSTN). In dit document bekijken we de typen telefoonnummers, configuratieopties en beschikbaarheid in regio's voor het plannen van uw telefoon- en sms-oplossing met behulp van Communication Services.

## <a name="azure-subscriptions-eligibility"></a>Geschiktheid Azure-abonnementen

Als u een telefoonnummer wilt verkrijgen, moet u een betaald Azure-abonnement hebben. Telefoonnummers kunnen niet worden verkregen voor proefaccounts of via gratis Azure-tegoeden.

De beschik baarheid van telefoon nummers is momenteel beperkt tot Azure-abonnementen die een factuur adres hebben in de Verenigde Staten-en communicatie services-resources die een Amerikaanse gegevens locatie hebben.


## <a name="number-types-and-features"></a>Nummer typen en-functies
Communication Services biedt twee soorten telefoonnummers: **lokaal** en **gratis**.

### <a name="local-numbers"></a>Lokale nummers
Lokale (geografische) nummers zijn 10-cijferige telefoonnummers die bestaan uit de lokale netnummers in de Verenigde Staten. `+1 (206) XXX-XXXX` is bijvoorbeeld een lokaal nummer met het netnummer `206`. Dit netnummer is toegewezen aan de stad Seattle. Deze telefoonnummers worden doorgaans gebruikt door individuen en lokale bedrijven. Azure Communication Services biedt lokale nummers in de Verenigde Staten. Deze nummers kunnen worden gebruikt om te bellen, maar niet om sms-berichten te verzenden.

### <a name="toll-free-numbers"></a>Gratis nummers
Gratis nummers zijn 10-cijferige telefoonnummers met aparte netnummers die gratis kunnen worden gebeld vanaf elk telefoonnummer. `+1 (800) XXX-XXXX` is bijvoorbeeld een gratis nummer in de regio Noord-Amerika. Deze telefoonnummers worden meestal gebruikt voor klantenservices. Azure Communication Services biedt gratis nummers in de Verenigde Staten. Deze nummers kunnen worden gebruikt om te bellen, en om sms-berichten te verzenden. Gratis nummers kunnen niet worden gebruikt door mensen en kunnen alleen worden toegewezen aan toepassingen.

#### <a name="choosing-a-phone-number-type"></a>Een type telefoonnummer kiezen

Als uw telefoonnummer zal worden gebruikt door een toepassing (bijvoorbeeld voor het bellen of verzenden van berichten namens uw service), kunt u een gratis of lokaal (geografisch) nummer selecteren. Als uw toepassing sms-berichten verzendt en/of belt, kunt u een gratis nummer kiezen.

Als uw telefoonnummer wordt gebruikt door een persoon (bijvoorbeeld een gebruiker van uw beltoepassing), moet u het lokale (geografische) telefoonnummer gebruiken.

In de volgende tabel vindt u een overzicht van deze typen telefoonnummers:

| Type telefoonnummer | Voorbeeld                              | Beschikbaarheid land    | Mogelijkheid van telefoonnummer |Algemeen scenario                                                                                                     |
| ----------------- | ------------------------------------ | ----------------------- | ------------------------|------------------------------------------------------------------------------------------------------------------- |
| Lokaal (geografisch)        | +1 (lokaal netnummer) XXX XX XX  | VS                      | Oproepen (uitgaand) | Telefoonnummers toewijzen aan gebruikers in uw toepassingen  |
| Gratis         | \+ 1 (gratis *netnummer*) XXX XX XX | VS                      | Oproepen (uitgaand), sms (inkomend/uitgaand)| Het toewijzen van telefoonnummers aan IVR-systemen (Interactive Voice Response)/bots, sms-toepassingen                                        |


### <a name="phone-number-capabilities-in-azure-communication-services"></a>Mogelijkheden voor telefoon nummers in azure Communication Services

[!INCLUDE [Emergency Calling Notice](../../includes/emergency-calling-notice-include.md)]

Voor de meeste telefoon nummers kunt u een set met mogelijkheden van een "a la carte" configureren. Deze mogelijkheden kunnen worden geselecteerd wanneer u uw telefoon nummers in azure Communication Services huurt.

De mogelijkheden die voor u beschikbaar zijn, zijn afhankelijk van het land dat u gebruikt, uw use-case en het type telefoon nummer dat u hebt geselecteerd. Deze mogelijkheden verschillen per land als gevolg van wettelijke vereisten. Azure Communication Services biedt de volgende mogelijkheden voor telefoon nummer:

- **Uitgaande sms in één richting** Met deze optie kunt u sms-berichten verzenden naar uw gebruikers. Dat kan handig zijn voor meldingen en tweeledige verificatie.
- **Inkomende en uitgaande sms in twee richtingen** Met deze optie kunt u berichten van uw gebruikers verzenden en ontvangen met behulp van telefoonnummers. Dat kan handig zijn voor klantenservicescenario's.
- **Uitgaande telefoonoproepen in één richting** Met deze optie kunt u uw gebruikers bellen en de beller-id configureren voor uitgaande oproepen die door uw service worden gemaakt. Dat kan handig zijn in scenario's voor klantenservices en spraakmeldingen.

## <a name="countryregion-availability"></a>Beschikbaarheid in land/regio

In de volgende tabel ziet u waar u verschillende soorten telefoonnummers kunt verkrijgen, samen met de functies voor inkomende en uitgaande oproepen en sms die zijn gekoppeld aan deze typen telefoonnummers.

|Nummertype| Nummers verkrijgen in | Bellen naar                                        | Oproepen ontvangen van                                    |Berichten verzenden naar       | Berichten ontvangen van |
|-----------| ------------------ | ---------------------------------------------------  |-------------------------------------------------------|-----------------------|--------|
| Lokaal (geografisch)  | VS                 | VS, Canada, Verenigd Koninkrijk, Duitsland, Frankrijk. +meer*| VS, Canada, Verenigd Koninkrijk, Duitsland, Frankrijk. +meer* |Niet beschikbaar| Niet beschikbaar |
| Gratis | VS                 | VS                                                   | VS                                                    |VS                | VS |

*Raadpleeg de pagina met [prijzen](../pricing.md) voor meer informatie over oproepbestemmingen en prijzen.


## <a name="next-steps"></a>Volgende stappen

### <a name="quickstarts"></a>Quickstarts

- [Een telefoonnummer aanvragen](../../quickstarts/telephony-sms/get-phone-number.md)
- [Een oproep doen](../../quickstarts/voice-video-calling/calling-client-samples.md)
- [Een sms verzenden](../../quickstarts/telephony-sms/send.md)

### <a name="conceptual-documentation"></a>Conceptuele documentatie

- [Spraak- en videoconcepten](../voice-video-calling/about-call-types.md)
- [Telefonie concepten](./telephony-concept.md)
- [Oproepstromen](../call-flows.md)
- [Prijzen](../pricing.md)
