---
title: bestand opnemen
description: bestand opnemen
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/05/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 552a40be0c069d1002ebc7ea4dafe0d6f93a5755
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85128209"
---
Met [berichtroutering](../articles/iot-hub/iot-hub-devguide-messages-d2c.md) kunt u telemetriegegevens verzenden van uw IoT-apparaten naar ingebouwde, met Event Hub compatibele eindpunten of aangepaste eindpunten zoals blobopslag, Service Bus-wachtrijen, Service Bus-onderwerpen en Event Hubs. Als u een aangepaste berichtroutering wilt configureren, maakt u [routeringsquery’s](../articles/iot-hub/iot-hub-devguide-routing-query-syntax.md) om een route aan te passen die overeenkomt met een bepaalde regel. Zodra u deze hebt ingesteld, worden de inkomende gegevens automatisch door de IoT Hub doorgestuurd naar de eindpunten. Als een bericht niet overeenkomt met een van de gedefinieerde routeringsquery's, wordt het doorgestuurd naar het standaardeindpunt.

In deze tweedelige zelfstudie leert u hoe u deze aangepaste routeringsquery’s kunt instellen en gebruiken met IoT Hub. U stuurt berichten door van een IoT-apparaat naar een of meerdere eindpunten, met inbegrip van blobopslag en een Service Bus-wachtrij. Berichten in de Service Bus-wachtrij worden opgehaald door een logische app en via e-mail verzonden. Berichten waarvoor geen aangepaste berichtroutering is gedefinieerd, worden naar het standaardeindpunt gestuurd, vervolgens opgehaald door Azure Stream Analytics en weergegeven in een Power BI-visualisatie.

Als u deel 1 en 2 van deze zelfstudie wilt afronden, voert u de volgende taken uit:

**Deel I: Resources maken, berichtroutering instellen**
> [!div class="checklist"]
> * Maak de resources: een IoT hub, een opslagaccount, een Service Bus-wachtrij en een gesimuleerd apparaat. U kunt dit doen met behulp van Azure Portal, een Azure Resource Manager-sjabloon, de Azure CLI of Azure PowerShell.
> * Configureer de eindpunten en berichtroutes in IoT Hub voor het opslagaccount en de Service Bus-wachtrij.

**Deel II: Berichten verzenden naar de hub, gerouteerde resultaten weergeven**
> [!div class="checklist"]
> * Maak een logische app die wordt geactiveerd en een e-mailbericht verzendt wanneer een bericht wordt toegevoegd aan de Service Bus-wachtrij.
> * Download een app die een IoT-apparaat aanzet tot het verzenden van berichten naar de hub voor de verschillende routeringsopties en voer deze uit.
> * Maak een Power BI-visualisatie voor gegevens die naar het standaardeindpunt worden verzonden.
> * Bekijk de resultaten...
> * .. .in de Service Bus-wachtrij en e-mailberichten.
> * ...in het opslagaccount.
> * ...in de Power BI-visualisatie.

## <a name="prerequisites"></a>Vereisten

* Voor Deel 1 van deze zelfstudie:
  - U hebt een abonnement op Azure nodig. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

* Voor Deel 2 van deze zelfstudie:
  - U moet Deel 1 van deze zelfstudie hebben afgerond en de resources nog tot uw beschikking hebben.
  - Installeer [Visual Studio](https://www.visualstudio.com/).
  - Toegang tot een Power BI-account voor het analyseren van de Stream Analytics van het standaardeindpunt. ([Probeer Power BI gratis uit](https://app.powerbi.com/signupredirect?pbi_source=web).)
  - Een werk- of schoolaccount voor het verzenden van e-mailberichten.
  - Zorg ervoor dat de poort 8883 is geopend in de firewall. In het voorbeeld in deze zelfstudie wordt het MQTT-protocol gebruikt, dat communiceert via poort 8883. Deze poort is in sommige netwerkomgevingen van bedrijven en onderwijsinstellingen mogelijk geblokkeerd. Zie [Verbinding maken met IoT Hub (MQTT)](../articles/iot-hub/iot-hub-mqtt-support.md#connecting-to-iot-hub) voor meer informatie en manieren om dit probleem te omzeilen.

[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]
