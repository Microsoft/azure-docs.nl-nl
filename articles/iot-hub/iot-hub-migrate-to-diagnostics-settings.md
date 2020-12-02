---
title: Azure IoT Hub Operations-bewaking migreren naar IoT Hub resource Logboeken in Azure Monitor | Microsoft Docs
description: Het bijwerken van Azure IoT Hub om Azure Monitor te gebruiken in plaats van bewaking van bewerkingen om de status van de bewerkingen op uw IoT-hub in realtime te controleren.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: kgremban
ms.openlocfilehash: dfd819d82f5e35183802e33e5d423cad4de36c38
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461589"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-azure-monitor-resource-logs"></a>Migreer uw IoT Hub van operations monitoring naar Azure Monitor resource logs

Klanten die [bewerkingen bewaken](iot-hub-operations-monitoring.md) om de status van bewerkingen in IOT hub bij te houden, kunnen die werk stroom migreren naar [Azure monitor-bron logboeken](../azure-monitor/platform/platform-logs-overview.md), een functie van Azure monitor. Bron logboeken geven diagnostische gegevens op resource niveau voor veel Azure-Services.

**De functionaliteit voor de bewaking van bewerkingen van IOT hub is afgeschaft** en is verwijderd uit de portal. In dit artikel worden de stappen beschreven voor het verplaatsen van uw workloads van operations monitoring naar Azure Monitor resource logs. Zie [uw Azure IOT-oplossingen bewaken met Azure monitor en Azure resource Health](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/)voor meer informatie over de tijd lijn van de afschaffing.

## <a name="update-iot-hub"></a>IoT Hub bijwerken

Als u uw IoT Hub in de Azure Portal wilt bijwerken, maakt u eerst een diagnostische instelling en schakelt u de bewaking van bewerkingen uit.  

### <a name="create-a--diagnostic-setting"></a>Een diagnostische instelling maken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en ga naar uw IoT Hub.

1. Selecteer in het linkerdeel venster, onder **bewaking**, **instellingen voor diagnostische gegevens**. Selecteer vervolgens **Diagnostische instelling toevoegen**.

   :::image type="content" source="media/iot-hub-migrate-to-diagnostics-settings/open-diagnostic-settings.png" alt-text="Schermopname waarin Diagnostische instellingen in de sectie Bewaking is gemarkeerd.":::

1. Geef in het deel venster **Diagnostische instelling** een naam op voor de diagnostische instelling.

1. Selecteer onder **categorie Details** de categorieën voor de bewerkingen die u wilt bewaken. Zie [bron logboeken](monitor-iot-hub-reference.md#resource-logs)voor meer informatie over de beschik bare bewerkingen met IOT hub.

1. Kies onder **bestemmings Details** waar u de logboeken wilt verzenden. U kunt een wille keurige combi natie van deze bestemmingen selecteren:

   * Archiveren naar een opslagaccount
   * Streamen naar een Event Hub
   * Via een Log Analytics-werk ruimte naar Azure Monitor logboeken verzenden

   In de volgende scherm afbeelding ziet u een diagnostische instelling die bewerkingen in de categorieën verbindingen en telemetrie van apparaten routeert naar een Log Analytics-werk ruimte:

   :::image type="content" source="media/iot-hub-migrate-to-diagnostics-settings/add-diagnostic-setting.png" alt-text="Scherm opname met een voltooide diagnostische instelling.":::

1. Selecteer **Opslaan** om de instellingen op te slaan.

Nieuwe instellingen worden in ongeveer 10 minuten van kracht. Daarna worden logboeken weer gegeven in de geconfigureerde bestemming. Zie [logboek gegevens verzamelen en gebruiken van uw Azure-resources](../azure-monitor/platform/platform-logs-overview.md)voor meer informatie over het configureren van diagnostiek.

Zie [Diagnostische instellingen](../azure-monitor/platform/diagnostic-settings.md) in de Azure monitor-documentatie voor meer informatie over het maken van diagnostische instellingen, met inbegrip van Power shell en de Azure cli.

### <a name="turn-off-operations-monitoring"></a>Bewaking van bewerkingen uitschakelen

> [!NOTE]
> Vanaf 11 maart 2019 wordt de functie voor het controleren van bewerkingen verwijderd uit de Azure Portal-interface van IoT Hub. De onderstaande stappen zijn niet meer van toepassing. Als u wilt migreren, moet u ervoor zorgen dat de juiste categorieën worden doorgestuurd naar een bestemming met een Azure Monitor diagnostische instelling hierboven.

Wanneer u de nieuwe diagnostische instellingen in uw werk stroom hebt getest, kunt u de functie voor het controleren van bewerkingen uitschakelen. 

1. Selecteer in het menu IoT Hub de optie **bewerkingen bewaken**.

2. Selecteer onder elke bewakings categorie de optie **geen**.

3. Sla de wijzigingen van de operations-bewaking op.

## <a name="update-applications-that-use-operations-monitoring"></a>Toepassingen bijwerken die gebruikmaken van operations monitoring

De schema's voor de bewaking van bewerkingen en bron logboeken variëren enigszins. Het is belang rijk dat u de toepassingen die vandaag gebruikmaken van operations monitoring, bijwerkt om toe te wijzen aan het schema dat wordt gebruikt door bron Logboeken.

Daarnaast biedt IoT Hub bron logboeken vijf nieuwe categorieën voor het bijhouden van wijzigingen. Nadat u toepassingen voor het bestaande schema hebt bijgewerkt, voegt u ook de nieuwe categorieën toe:

* Dubbele bewerkingen van het Cloud naar het apparaat
* Dubbele bewerkingen van het apparaat naar de Cloud
* Dubbele query's
* Taakbewerkingen
* Directe methoden

Zie [bron logboeken](monitor-iot-hub-reference.md#resource-logs)voor de specifieke schema structuren.

## <a name="monitoring-device-connect-and-disconnect-events-with-low-latency"></a>Bewaking van het apparaat verbinding maken en verbreken van gebeurtenissen met een lage latentie

Als u verbinding met het apparaat wilt bewaken en de gebeurtenissen in de productie verbinding wilt verbreken, kunt u het beste abonneren op de [ **verbroken** gebeurtenis](iot-hub-event-grid.md#event-types) van het apparaat op Event grid om waarschuwingen op te halen en de verbindings status van het apparaat te controleren. Gebruik deze [zelf studie](iot-hub-how-to-order-connection-state-events.md) voor meer informatie over het integreren van met het apparaat verbonden en verbroken gebeurtenissen van het apparaat van IOT hub in uw IOT-oplossing.

## <a name="next-steps"></a>Volgende stappen

[IoT Hub bewaken](monitor-iot-hub.md)