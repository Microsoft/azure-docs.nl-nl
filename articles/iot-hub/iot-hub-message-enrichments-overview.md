---
title: Overzicht van verrijkingen van Azure IoT Hub-berichten
description: In dit artikel worden de verrijkingen van een bericht weer gegeven. Dit geeft de IoT Hub de mogelijkheid om berichten te stem pelen met aanvullende informatie voordat de berichten naar het aangewezen eind punt worden verzonden.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: 3975a57c095a8593e392e932bd125308853d3756
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92541516"
---
# <a name="message-enrichments-for-device-to-cloud-iot-hub-messages"></a>Verrijkingen van berichten voor IoT Hub berichten van apparaat-naar-Cloud

*Verrijkingen* van berichten is de mogelijkheid van de IOT hub om berichten met aanvullende informatie af te *stem pelen* voordat de berichten naar het aangewezen eind punt worden verzonden. Een reden voor het gebruik van verrijkingen van berichten is het insluiten van gegevens die kunnen worden gebruikt voor het vereenvoudigen van de downstream-verwerking. Het verrijken van telemetriegegevens van een apparaat met een dubbele tag van een apparaat kan bijvoorbeeld de belasting van klanten verminderen om deze informatie te laten opleveren voor Device-dubbele API-aanroepen.

![Stroom voor het verrijkings bericht van berichten](./media/iot-hub-message-enrichments-overview/message-enrichments-flow.png)

Een bericht verrijking heeft drie belang rijke elementen:

* Verrijkings naam of-sleutel

* Een waarde

* Een of meer [eind punten](iot-hub-devguide-endpoints.md) waarvoor de verrijking moet worden toegepast.

De **sleutel** is een teken reeks. Een sleutel mag alleen alfanumerieke tekens bevatten of deze speciale tekens: hyphen ( `-` ), onderstrepings teken ( `_` ) en punt ( `.` ).

De **waarde** kan een van de volgende voor beelden zijn:

* Een statische teken reeks. Dynamische waarden zoals voor waarden, logica, bewerkingen en functies zijn niet toegestaan. Als u bijvoorbeeld een SaaS-toepassing ontwikkelt die wordt gebruikt door verschillende klanten, kunt u een id toewijzen aan elke klant en die id beschikbaar maken in de toepassing. Wanneer de toepassing wordt uitgevoerd, stemt IoT Hub de telemetrie-berichten van het apparaat in met de id van de klant, waardoor het mogelijk is om de berichten anders voor elke klant te verwerken.

* De naam van de IoT-hub die het bericht verzendt. Deze waarde is *$iothubname*.

* Informatie van het apparaat dubbele, zoals het pad. Voor beelden hiervan zijn *$Twin. Tags. Field* en *$Twin. Tags. Latitude*.

   > [!NOTE]
   > Op dit moment worden alleen $iothubname, $twin. Tags, $twin. Properties. desired en $twin. Properties. gerapporteerd worden ondersteunde variabelen voor het verrijken van het bericht.

Verrijkingen van berichten worden toegevoegd als toepassings eigenschappen aan berichten die naar gekozen eind punt (en) worden verzonden.  

## <a name="applying-enrichments"></a>Verrijkingen Toep assen

De berichten kunnen afkomstig zijn van elke gegevens bron die wordt ondersteund door [IOT hub bericht routering](iot-hub-devguide-messages-d2c.md), met inbegrip van de volgende voor beelden:

* telemetrie van apparaten, zoals de Tempe ratuur of druk
* dubbele wijzigings meldingen van het apparaat--wijzigingen in het apparaat dubbele
* levenscyclus gebeurtenissen van het apparaat, zoals wanneer het apparaat wordt gemaakt of verwijderd

U kunt verrijkingen toevoegen aan berichten die worden verzonden naar het ingebouwde eind punt van een IoT Hub, of berichten die worden doorgestuurd naar aangepaste eind punten, zoals Azure Blob Storage, een Service Bus wachtrij of een Service Bus onderwerp.

U kunt verrijkingen toevoegen aan berichten die worden gepubliceerd naar Event Grid door het eind punt te selecteren als Event Grid. We maken een standaard route in IoT Hub naar telemetrie van apparaten, op basis van uw Event Grid-abonnement. Deze enkele route kan al uw Event Grid abonnementen afhandelen. U kunt verrijkingen voor het event grid-eind punt configureren nadat u het event grid-abonnement op apparaat-telemetrie hebt gemaakt. Zie [IOT hub en Event grid](iot-hub-event-grid.md)voor meer informatie.

Verrijkingen worden toegepast per eind punt. Als u vijf verrijkingen voor een bepaald eind punt opgeeft, worden alle berichten die naar dat eind punt gaan, voorzien van dezelfde vijf verrijkingen.

Verrijkingen kunnen worden geconfigureerd met behulp van de volgende methoden:

| **Methode** | **Opdracht** |
| ----- | -----| 
| Portal | [Azure-portal](https://portal.azure.com) | Raadpleeg de [zelf studie](tutorial-message-enrichments.md) voor het verbeteren van een bericht | 
| Azure CLI   | [AZ IOT hub-bericht-verrijking](/cli/azure/iot/hub/message-enrichment) |
| Azure PowerShell | [Add-AzIotHubMessageEnrichment](/powershell/module/az.iothub/add-aziothubmessageenrichment) |

Het toevoegen van bericht verrijkingen voegt geen latentie toe aan de route ring van berichten.

Raadpleeg de [zelf studie](tutorial-message-enrichments.md) voor het verbeteren van het bericht om verrijkingen van berichten te proberen

## <a name="limitations"></a>Beperkingen

* U kunt Maxi maal 10 verrijkingen per IoT Hub voor deze hubs toevoegen aan de laag Standard of Basic. Voor IoT-hubs in de gratis laag kunt u Maxi maal 2 verrijkingen toevoegen.

* Als u in sommige gevallen een verrijking toepast met een waarde die is ingesteld op een tag of eigenschap in het dubbele apparaat, wordt de waarde als een teken reeks waarde stempel. Als een verrijkings waarde bijvoorbeeld is ingesteld op $twin. Tags. Field, worden de berichten gestempeld met de teken reeks "$twin. Tags. Field" in plaats van de waarde van het veld van de dubbele. Dit gebeurt in de volgende gevallen:

   * Uw IoT Hub bevindt zich in de laag Basic. IoT-hubs van de Basic-laag bieden geen ondersteuning voor apparaatdubbels.

   * Uw IoT Hub bevindt zich in de Standard-laag, maar het apparaat dat het bericht verzendt, heeft geen dubbele apparaat.

   * Uw IoT Hub bevindt zich in de Standard-laag, maar het dubbele pad van het apparaat dat wordt gebruikt voor de waarde van de verrijking bestaat niet. Als de verrijkings waarde bijvoorbeeld is ingesteld op $twin. Tags. Location en het apparaat dubbele heeft geen locatie-eigenschap onder Tags, wordt het bericht voorzien van de teken reeks "$twin. Tags. Location". 

* Het kan tot vijf minuten duren voordat updates op een apparaat worden weer gegeven in de bijbehorende verrijkings waarde.

* De totale bericht grootte, inclusief de verrijkingen, mag niet groter zijn dan 256 KB. Als de grootte van een bericht groter is dan 256 KB, wordt het bericht door de IoT Hub verwijderd. U kunt [IOT hub metrische gegevens](monitor-iot-hub-reference.md#metrics) gebruiken om fouten op te sporen en op te sporen wanneer berichten worden verwijderd. U kunt bijvoorbeeld de gegevens van de *telemetrie* bewaken die niet compatibel zijn (*D2C. telemetrie*... valid) in de [metrische gegevens van de route ring](monitor-iot-hub-reference.md#routing-metrics). Zie [Azure IoT Hub controleren](monitor-iot-hub.md) voor meer informatie.

* Verrijkingen van berichten zijn niet van toepassing op digitale dubbele wijzigings gebeurtenissen.

## <a name="pricing"></a>Prijzen

Verrijkingen van berichten zijn beschikbaar voor geen extra kosten. Op dit moment worden er kosten in rekening gebracht wanneer u een bericht naar een IoT Hub verzendt. Er wordt slechts één keer per bericht in rekening gebracht, zelfs als het bericht naar meerdere eind punten gaat.

## <a name="next-steps"></a>Volgende stappen

Bekijk deze artikelen voor meer informatie over het routeren van berichten naar een IoT Hub:

* [Zelf studie voor het verrijken van berichten](tutorial-message-enrichments.md)

* [IoT Hub bericht routering gebruiken om apparaat-naar-Cloud-berichten te verzenden naar verschillende eind punten](iot-hub-devguide-messages-d2c.md)

* [Zelf studie: IoT Hub route ring](tutorial-routing.md)