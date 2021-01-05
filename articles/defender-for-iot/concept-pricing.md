---
title: Prijs en bijbehorende kosten
description: Meer informatie over de kosten die zijn gekoppeld aan Defender voor IoT en hoe u deze kunt beheren.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/08/2020
ms.author: shhazam
ms.openlocfilehash: a97bcbf5ba47289a2e68b0eaa587ea39d7fb705a
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2020
ms.locfileid: "97832349"
---
# <a name="pricing-and-associated-costs"></a>Prijs en bijbehorende kosten

In dit artikel wordt het prijs model voor Defender voor IoT uitgelegd, worden alle gekoppelde kosten samenvatten en wordt uitgelegd hoe u deze beheert.

## <a name="pricing"></a>Prijzen

Het prijs model voor Defender voor IoT bestaat uit twee delen en wordt gefactureerd zodra een IoT Hub is [ingeschakeld](quickstart-onboard-iot-hub.md) in Defender voor IOT:

- Kosten per apparaat-ingebouwde beveiligings mogelijkheden op basis van de analyse van IoT Hub Logboeken.

- Kosten per bericht: Verbeterde beveiligings mogelijkheden op basis van beveiligings berichten van IoT Edge of blad apparaten.

Zie [Security Center prijzen](https://azure.microsoft.com/pricing/details/security-center/)voor meer informatie.

## <a name="associated-costs"></a>Gekoppelde kosten

Defender voor IoT heeft kosten verbonden, die geen deel uitmaken van de prijzen voor direct:

- Opslag kosten Log Analytics

U kunt de gekoppelde kosten verlagen door te kiezen voor bepaalde oplossings functies. Afmelden door uw instellingen te wijzigen.

Uw instellingen wijzigen:

1. Open IoT Hub.

1. Klik onder **beveiliging** op **instellingen**.

1. Klik op **gegevens verzameling**.

De volgende tabel bevat een samen vatting van de bijbehorende kosten en implicaties van elke optie.

| Optie | Gebruik | Opmerking |
| --- | --- | --- |
| **Opslag Log Analytics** |  |
| Aanbeveling en waarschuwingen van het apparaat| Beveiligings aanbeveling en waarschuwingen die worden gegenereerd door de service | Niet optioneel |
| Onbewerkte beveiligings gegevens| Onbewerkte beveiligings gegevens van IoT-apparaten, verzameld door beveiligings agenten | _Beveiligings gebeurtenissen voor het opslaan van onbewerkte apparaten_ uitschakelen |
|

>[!Important]
> Uitbellen heeft ernstige gevolgen voor de beschik baarheid van Defender voor IoT-beveiliging.

| Opt-out | Gevolgen |
| --- | --- |
| _Verzameling van dubbele meta gegevens_ | [Aangepaste waarschuwingen](quickstart-create-custom-alerts.md) uitschakelen |
| | Aanbevelingen van IoT Edge-manifest uitschakelen |
| | Aanbevelingen en waarschuwingen op basis van apparaat-id's uitschakelen |
| _Beveiligings gebeurtenissen voor onbewerkte apparaten opslaan_ | Details van de algemene aanbevelingen voor het besturings systeem van het apparaat zijn niet beschikbaar |
| | Er zijn geen gegevens over [waarschuwingen](concept-security-alerts.md) en [aanbevelings](concept-recommendations.md) onderzoeken beschikbaar |
|

## <a name="see-also"></a>Zie tevens

- Toegang tot uw [onbewerkte beveiligings gegevens](how-to-security-data-access.md)
- [Een apparaat onderzoeken](how-to-investigate-device.md)
- [Beveiligings aanbevelingen](concept-recommendations.md) begrijpen en verkennen
- [Beveiligings waarschuwingen](concept-security-alerts.md) begrijpen en verkennen
