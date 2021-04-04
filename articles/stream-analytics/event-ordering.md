---
title: Beleid voor het best Ellen van gebeurtenissen voor Azure Stream Analytics configureren
description: In dit artikel wordt beschreven hoe u de instellingen voor even ordenen in Stream Analytics kunt configureren
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 08/06/2020
ms.openlocfilehash: f7ec6f32b48a93a29210311c7ba6747eb2e2d066
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98014292"
---
# <a name="configuring-event-ordering-policies-for-azure-stream-analytics"></a>Beleid voor het best Ellen van gebeurtenissen voor Azure Stream Analytics configureren

In dit artikel wordt beschreven hoe u een late aankomst en beleid voor onbestelbare gebeurtenissen in Azure Stream Analytics kunt instellen en gebruiken. Deze beleids regels worden alleen toegepast wanneer u de component [time stamp by](/stream-analytics-query/timestamp-by-azure-stream-analytics) gebruikt in uw query en ze worden alleen toegepast op Cloud-invoer bronnen.

## <a name="event-time-and-arrival-time"></a>Tijd van gebeurtenis en aankomst tijd

Uw Stream Analytics-taak kan gebeurtenissen verwerken op basis van de tijd of *aankomst tijd* van de *gebeurtenis* . De tijd van de **gebeurtenis/toepassing** is de tijds tempel die aanwezig is in de gebeurtenis lading (wanneer de gebeurtenis is gegenereerd). **Aankomst tijd** is de tijds tempel waarop de gebeurtenis is ontvangen op de invoer bron (Event Hubs/IOT hub/Blob-opslag). 

Stream Analytics verwerkt gebeurtenissen standaard op *aankomst tijd*, maar u kunt ervoor kiezen gebeurtenissen op *gebeurtenis tijd* te verwerken door de component [time stamp by](/stream-analytics-query/timestamp-by-azure-stream-analytics) in uw query te gebruiken. Late aankomst en beleid voor out-of-volg orde zijn alleen van toepassing als u gebeurtenissen op gebeurtenis tijd verwerkt. Overweeg het gebruik van vereisten voor latentie en juistheid voor uw scenario bij het configureren van deze instellingen. 

## <a name="what-is-late-arrival-policy"></a>What is late arrival policy? (Wat is beleid voor late aankomst?)

Soms arriveren gebeurtenissen te laat om verschillende redenen. Een gebeurtenis die 40 seconden achterstallig aankomt, heeft bijvoorbeeld de gebeurtenis tijd = 00:10:00 en aankomst tijd = 00:10:40. Als u het beleid voor late aankomst instelt op 15 seconden, wordt elke gebeurtenis die later dan 15 seconden arriveert, verwijderd (niet verwerkt door Stream Analytics) of is de tijd van de gebeurtenis aangepast. In het bovenstaande voor beeld is het tijdstip waarop de gebeurtenis 40 seconden te laat (meer dan een beleids instelling) is aangekomen, de tijds duur van de gebeurtenis aangepast aan het maximum van het beleid voor late aankomst van 00:10:25 (aankomst tijd-late ontvangst). Het standaard beleid voor late aankomst is 5 seconden.

## <a name="what-is-out-of-order-policy"></a>Wat is out-of-order-beleid? 

De gebeurtenis kan ook buiten de juiste volg orde worden afgeleverd. Wanneer de tijd van de gebeurtenis is aangepast op basis van het beleid voor late aankomst, kunt u er ook voor kiezen om te voor komen dat gebeurtenissen die verouderd zijn, automatisch worden verwijderd of aangepast. Als u dit beleid instelt op 8 seconden, worden alle gebeurtenissen die buiten de volg orde arriveren, maar binnen het venster van 8 seconden, opnieuw gerangschikt op gebeurtenis tijd. Gebeurtenissen die later aankomen, worden verwijderd of aangepast aan de maximum waarde voor out-of-order-beleid. Het standaard beleid voor out-of-order is 0 seconden. 

## <a name="adjust-or-drop-events"></a>Gebeurtenissen aanpassen of verwijderen

Als gebeurtenissen achterstallig of out-of out-of-order arriveren op basis van het beleid dat u hebt geconfigureerd, kunt u dergelijke gebeurtenissen verwijderen (niet verwerkt door Stream Analytics) of de bijbehorende gebeurtenis tijd aanpassen.

Laat ons een voor beeld zien van deze beleids regels in actie.
<br> **Beleid voor late aankomst:** 15 seconden
<br> **Out-of-order beleid:** 8 seconden

| Gebeurtenis nummer | Tijd van gebeurtenis | Aankomst tijd | System.Timestamp | Uitleg |
| --- | --- | --- | --- | --- |
| **1** | 00:10:00  | 00:10:40  | 00:10:25  | De gebeurtenis heeft het tolerantie niveau achterstallig en buiten. Hiermee wordt de tijd van de gebeurtenis aangepast naar de maximale aankomst tolerantie.  |
| **2** | 00:10:30 | 00:10:41  | 00:10:30  | De gebeurtenis is te laat gearriveerd, maar binnen het tolerantie niveau. De tijd van de gebeurtenis wordt dus niet aangepast.  |
| **3** | 00:10:42 | 00:10:42 | 00:10:42 | De gebeurtenis is op tijd ontvangen. Geen correctie vereist.  |
| **4** | 00:10:38  | 00:10:43  | 00:10:38 | De gebeurtenis is afgeleverd, maar binnen de tolerantie van 8 seconden. Dit betekent dat de tijd van de gebeurtenis niet wordt aangepast. Voor analyse doeleinden wordt deze gebeurtenis beschouwd als voorafgaande gebeurtenis nummer 4.  |
| **5** | 00:10:35 | 00:10:45  | 00:10:37 | De gebeurtenis is in orde en buiten de tolerantie van 8 seconden aangekomen. Dit betekent dat de tijd van de gebeurtenis is aangepast naar het maximum van de tolerantie voor buiten-volg orde. |

## <a name="can-these-settings-delay-output-of-my-job"></a>Kunnen deze instellingen de uitvoer van mijn taak vertragen? 

Ja. Out-of-order-beleid is standaard ingesteld op nul (00 minuten en 00 seconden). Als u de standaard instelling wijzigt, wordt de eerste uitvoer van uw taak vertraagd met deze waarde (of hoger). 

Als een van de partities van uw invoer geen gebeurtenissen ontvangt, moet u er rekening mee houden dat uw uitvoer vertraagd moet worden door de beleids waarde lated receive. Lees de InputPartition-fout sectie hieronder voor meer informatie. 

## <a name="i-see-lateinputevents-messages-in-my-activity-log"></a>Ik zie LateInputEvents-berichten in mijn activiteiten logboek

Deze berichten worden weer gegeven om u te informeren dat de gebeurtenissen te laat zijn aangekomen en dat ze worden verwijderd of aangepast op basis van uw configuratie. U kunt deze berichten negeren als u het beleid voor late aankomst op de juiste manier hebt geconfigureerd. 

Voor beeld van dit bericht is: <br>
<code>
{"message Time":"2019-02-04 17:11:52Z","error":null,
"message":"First Occurred: 02/04/2019 17:11:48 | Resource Name: ASAjob | Message: Source 'ASAjob' had 24 data errors of kind 'LateInputEvent' between processing times '2019-02-04T17:10:49.7250696Z' and '2019-02-04T17:11:48.7563961Z'. Input event with application timestamp '2019-02-04T17:05:51.6050000' and arrival time '2019-02-04T17:10:44.3090000' was sent later than configured tolerance.","type":"DiagnosticMessage","correlation ID":"49efa148-4asd-4fe0-869d-a40ba4d7ef3b"} 
</code>

## <a name="i-see-inputpartitionnotprogressing-in-my-activity-log"></a>Ik zie InputPartitionNotProgressing in mijn activiteiten logboek

Uw invoer bron (Event hub/IoT Hub) heeft waarschijnlijk meerdere partities. Azure Stream Analytics produceert alleen uitvoer voor tijds tempel T1 wanneer alle partities die worden gecombineerd, ten minste op tijdstip T1 zijn. Stel bijvoorbeeld dat de query leest van een Event Hub-partitie met twee partities. Een van de partities, P1, heeft gebeurtenissen tot een time T1-tijd. De andere partitie, P2, heeft gebeurtenissen tot een tijd T1 + x. De uitvoer wordt vervolgens geproduceerd tot het tijdstip T1. Maar als er sprake is van een expliciete partitie component PartitionId, worden beide partities afzonderlijk uitgevoerd. 

Wanneer meerdere partities uit dezelfde invoer stroom worden gecombineerd, is de duur van de late aankomst de maximale hoeveelheid tijd die elke partitie wacht op nieuwe gegevens. Als er zich één partitie in uw event hub bevindt, of als IoT Hub geen invoer ontvangt, wordt de tijd lijn voor die partitie pas van kracht als de drempel waarde voor de duur van de late aankomst wordt bereikt. Dit vertraagt uw uitvoer door de drempel waarde voor de late aankomst tolerantie. In dergelijke gevallen wordt het volgende bericht weer gegeven:
<br><code>
{"message Time":"2/3/2019 8:54:16 PM UTC","message":"Input Partition [2] does not have additional data for more than [5] minute(s). Partition will not progress until either events arrive or late arrival threshold is met.","type":"InputPartitionNotProgressing","correlation ID":"2328d411-52c7-4100-ba01-1e860c757fc2"} 
</code><br><br>
Dit bericht geeft aan dat ten minste één partitie in uw invoer leeg is en dat de uitvoer wordt vertraagd door de drempel waarde voor de late aankomst. U kunt dit oplossen door het volgende te doen:  
1. Zorg ervoor dat alle partities van uw event hub/IoT Hub invoer ontvangen. 
2. Gebruik de component Partition by PartitionID in uw query. 

## <a name="why-do-i-see-a-delay-of-5-seconds-even-when-my-late-arrival-policy-is-set-to-0"></a>Waarom kan ik een vertraging van vijf seconden zien, zelfs wanneer mijn beleid voor late ontvangst is ingesteld op 0?
Dit gebeurt wanneer er een invoer partitie is die nooit een invoer heeft ontvangen. U kunt de invoer metrieken per partitie controleren om dit gedrag te valideren. 

Wanneer een partitie geen gegevens bevat voor meer dan de geconfigureerde drempel waarde voor de latere aankomst, wordt door stream Analytics een toepassings tempel voor de toepassing voor het onderwerp van de gebeurtenis volgorde beschreven. Hiervoor is een geschatte aankomst tijd vereist. Als de partitie nooit gegevens bevat, schat de stream Analytics de tijd van aankomst als *lokale time-5 seconden*. Vanwege deze partities zou nooit gegevens hadden kunnen een watermerk vertraging van vijf seconden worden weer gegeven.  

## <a name="next-steps"></a>Volgende stappen
* [Overwegingen over tijdsverwerking](stream-analytics-time-handling.md)
* [Metrische gegevens die beschikbaar zijn in Stream Analytics](./stream-analytics-monitoring.md#metrics-available-for-stream-analytics)