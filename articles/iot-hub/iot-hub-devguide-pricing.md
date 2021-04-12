---
title: Meer informatie over de prijzen van Azure IoT Hub | Microsoft Docs
description: 'Hand leiding voor ontwikkel aars: informatie over de werking van meten en prijzen met IoT Hub, waaronder gewerkte voor beelden.'
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 5b67f505a71d2af7950422a86ab45ef8aebb3bf4
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106056433"
---
# <a name="azure-iot-hub-pricing-information"></a>Prijs informatie voor Azure IoT Hub

[Prijzen voor Azure IOT hub](https://azure.microsoft.com/pricing/details/iot-hub) biedt algemene informatie over de verschillende sku's en prijzen voor IOT hub. Dit artikel bevat aanvullende informatie over de manier waarop de verschillende IoT Hub functionaliteiten worden gemeten als berichten door IoT Hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="charges-per-operation"></a>Kosten per bewerking

| Bewerking | Facturerings gegevens | 
| --------- | ------------------- |
| Registerbewerkingen voor identiteit <br/> (maken, ophalen, weer geven, bijwerken, verwijderen) | Niet in rekening gebracht. |
| Apparaat-naar-cloud-berichten | Verzonden berichten worden in rekening gebracht in segmenten van 4 KB op binnenkomend in IoT Hub. Er wordt bijvoorbeeld 2 berichten in rekening gebracht voor een bericht met 6 KB. |
| Cloud-naar-apparaat-berichten | Verzonden berichten worden in rekening gebracht in segmenten van 4 KB, bijvoorbeeld als er een 6-KB-bericht 2 berichten in rekening wordt gebracht. |
| Uploads van bestanden | Bestands overdracht naar Azure Storage is niet gemeten door IoT Hub. Berichten voor het initiëren en volt ooien van bestands overdracht worden in rekening gebracht als een bericht dat wordt gemeten in stappen van 4 KB. Een voor beeld: het overdragen van een bestand van 10 MB wordt in rekening gebracht als twee berichten naast de Azure Storage kosten. |
| Directe methoden | Aanvragen voor een geslaagde methode worden in rekening gebracht in segmenten van 4 KB en de antwoorden worden in segmenten van 4 KB in rekening gebracht als extra berichten. Aanvragen voor niet-verbonden apparaten worden in rekening gebracht als berichten in segmenten van 4 KB. Een methode met een hoofd tekst van 4 KB die resulteert in een reactie zonder hoofd code van het apparaat, wordt bijvoorbeeld in rekening gebracht als twee berichten. Een methode met een hoofd tekst van 6 KB die resulteert in een antwoord van 1 KB van het apparaat, wordt gefactureerd als twee berichten voor de aanvraag plus een ander bericht voor het antwoord. |
| Dubbele Lees bewerkingen voor apparaten en modules | Dubbele Lees bewerkingen van het apparaat of de module en van de back-end van de oplossing worden in rekening gebracht als berichten in segmenten van 4 KB. Bijvoorbeeld: het lezen van een 8-KB dubbele wordt in rekening gebracht als twee berichten. |
| Dubbele updates voor apparaten en modules (tags en eigenschappen) | Dubbele updates van het apparaat of de module en van de back-end van de oplossing worden in rekening gebracht als berichten in segmenten van 4 KB. Een voor beeld: het lezen van een 12-KB dubbele wordt in rekening gebracht als 3 berichten. |
| Dubbele query's apparaat en module | Query's worden in rekening gebracht als berichten, afhankelijk van de resultaat grootte in segmenten van 4 KB. |
| Taakbewerkingen <br/> (maken, bijwerken, weergeven, verwijderen) | Niet in rekening gebracht. |
| Taken per apparaat | Taak bewerkingen (zoals dubbele updates en methoden) worden als normaal in rekening gebracht. Bijvoorbeeld: een taak die resulteert in een 1000-methode aanroept met 1 KB-aanvragen en antwoorden op een lege hoofd tekst worden 1000-berichten in rekening gebracht. |
| Keep-Alive-berichten | Bij het gebruik van AMQP-of MQTT-protocollen worden berichten die worden uitgewisseld om verbinding te maken en de berichten die in de onderhandeling worden uitgewisseld, niet in rekening gebracht. |

> [!NOTE]
> Alle grootten worden berekend in overweging van de grootte van de nettolading in bytes (protocol framing wordt genegeerd). Voor berichten die eigenschappen en hoofd tekst hebben, wordt de grootte berekend in een protocol neutraal manier. Zie [IOT hub-bericht indeling](iot-hub-devguide-messages-construct.md)voor meer informatie.

## <a name="example-1"></a>Voor beeld #1

Een apparaat verzendt een apparaat-naar-Cloud bericht van 1 1 KB per minuut naar IoT Hub, dat vervolgens door Azure Stream Analytics wordt gelezen. De back-end van de oplossing roept elke 10 minuten een methode (met een nettolading van 512 bytes) op het apparaat aan om een specifieke actie te activeren. Het apparaat reageert op de-methode met een resultaat van 200 bytes.

Het apparaat gebruikt:

* Eén bericht * 60 minuten * 24 uur = 1440 berichten per dag voor de apparaat-naar-Cloud-berichten.
* Twee aanvraag plus respons * 6 keer per uur * 24 uur = 288 berichten voor de methoden.

Deze berekening geeft een totaal van 1728 berichten per dag.

## <a name="example-2"></a>Voor beeld #2

Een apparaat verzendt elk uur een apparaat-naar-Cloud bericht van 1 100 KB. Het apparaat wordt ook elke vier uur bijgewerkt met een nettolading van 1 KB. De back-end van de oplossing, eenmaal per dag, leest het 14-KB-apparaat en werkt deze bij met 512-byte-nettoladingen om configuraties te wijzigen.

Het apparaat gebruikt:

* 25 (100 KB/4 KB) berichten * 24 uur voor apparaat-naar-Cloud-berichten.
* Twee berichten (1 KB/0,5 KB) * zes keer per dag voor dubbele updates van het apparaat.

Deze berekening geeft een totaal van 612 berichten per dag.

De back-end van de oplossing verbruikt 28 berichten (14 KB/0,5 KB) om het apparaat dubbele te lezen, plus één bericht om het bij te werken, in totaal 29 berichten.

In totaal nemen het apparaat en de back-end van de oplossing 641 berichten per dag.
