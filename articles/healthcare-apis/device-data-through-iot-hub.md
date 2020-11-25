---
title: 'Zelfstudie: Apparaatgegevens ontvangen via Azure IoT Hub'
description: In deze zelfstudie leert u hoe u routering van apparaatgegevens inschakelt van IoT Hub naar Azure API for FHIR via Azure IoT Connector for FHIR.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: tutorial
ms.date: 11/13/2020
ms.author: punagpal
ms.openlocfilehash: 6c364cf84bada2a951ef3f224ea836885f0e3c1e
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2020
ms.locfileid: "94636313"
---
# <a name="tutorial-receive-device-data-through-azure-iot-hub"></a>Zelfstudie: Apparaatgegevens ontvangen via Azure IoT Hub

Azure IoT Connector for FHIR&#174;* (Fast Healthcare Interoperability Resources) biedt u de mogelijkheid om gegevens van IoMT-apparaten (Internet of Medical Things) op te nemen in Azure API for FHIR. Met de quickstart [Azure IoT Connector for FHIR (preview) implementeren met de Azure-portal](iot-fhir-portal-quickstart.md) ziet u een voorbeeld van een apparaat dat wordt beheerd door [het verzenden van telemetrie](iot-fhir-portal-quickstart.md#connect-your-devices-to-iot) door Azure IoT Central naar Azure IoT Connector for FHIR. Azure IoT Connector for FHIR kan ook worden gebruikt voor apparaten die zijn ingericht en beheerd via Azure IoT Hub. In deze zelfstudie wordt de procedure geboden om apparaatgegevens van Azure IoT Hub te verbinden en te routeren naar Azure IoT Connector for FHIR.

## <a name="prerequisites"></a>Vereisten

- Een actief Azure-abonnement - [Een gratis abonnement maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure API for FHIR-resource met ten minste één Azure IoT Connector for FHIR - [Azure IoT Connector for FHIR (preview) implementeren met de Azure-portal](iot-fhir-portal-quickstart.md)
- Azure IoT Hub-resource verbonden met een of meer gesimuleerde apparaat/apparaten - [Een IoT-hub maken met behulp van de Azure-portal](../iot-hub/quickstart-send-telemetry-dotnet.md)

> [!TIP]
> Als u een met Azure IoT Hub gesimuleerde apparaat-app gebruikt, kunt u de gewenste toepassing kiezen in verschillende ondersteunde talen en systemen.

## <a name="get-connection-string-for-azure-iot-connector-for-fhir-preview"></a>Verbindingsreeks ophalen voor Azure IoT Connector for FHIR (preview)

Azure IoT Hub vereist een verbindingsreeks om veilig verbinding te maken met uw Azure IoT Connector for FHIR. Maak een nieuwe verbindingsreeks voor uw Azure IoT Connector for FHIR zoals beschreven in [Een verbindingsreeks genereren](iot-fhir-portal-quickstart.md#generate-a-connection-string). Bewaar deze verbindingsreeks voor gebruik in de volgende stap.

Azure IoT Connector for FHIR gebruikt een Azure Event Hub-exemplaar voor het ontvangen van apparaatberichten. De hierboven gemaakte verbindingsreeks is in principe de verbindingsreeks voor deze onderliggende Event Hub.

## <a name="connect-azure-iot-hub-with-the-azure-iot-connector-for-fhir-preview"></a>Azure IoT Hub verbinden met Azure IoT Connector for FHIR (preview)

Azure IoT Hub ondersteunt een functie met de naam [berichtroutering](../iot-hub/iot-hub-devguide-messages-d2c.md) die de mogelijkheid biedt om gegevens van apparaten te verzenden naar verschillende Azure-Services zoals Event Hub, Opslagaccount en Service Bus. Azure IoT Connector for FHIR gebruikt deze functie om apparaatgegevens te verbinden en te verzenden vanuit Azure IoT Hub naar het Event Hub-eindpunt.

> [!NOTE] 
> Momenteel kunt u PowerShell of CLI-opdrachten alleen gebruiken om [berichtroutering te maken](../iot-hub/tutorial-routing.md) omdat de Event Hub van Azure IoT Connector for FHIR niet wordt gehost op het klantabonnement. Daarom is het voor u niet zichtbaar via de Azure-portal. Hoewel de objecten voor berichtroute worden toegevoegd met PowerShell of CLI, zijn ze zichtbaar op de Azure-portal en kunnen ze vanuit deze locatie worden beheerd.

Het instellen van een berichtroutering bestaat uit twee stappen.

### <a name="add-an-endpoint"></a>Een eindpunt toevoegen
Deze stap definieert een eindpunt waar de IoT Hub de gegevens aan zou routeren. Maak dit eindpunt met behulp van PowerShell-opdracht [add-AzIotHubRoutingEndpoint](/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint) of CLI-opdracht [az iot hub routing-endpoint create](/cli/azure/iot/hub/routing-endpoint#az-iot-hub-routing-endpoint-create), op basis van uw voorkeur.

Hier volgt de lijst met parameters die moeten worden gebruikt met de opdracht voor het maken van een eindpunt:

|PowerShell-parameter|CLI-parameter|Beschrijving|
|---|---|---|
|ResourceGroupName|resource-group|De naam van de resourcegroep van uw IoT Hub-resource.|
|Naam|hub-name|Naam van uw IoT Hub-resource.|
|EndpointName|endpoint-name|Gebruik een naam die u wilt toewijzen aan het eindpunt dat wordt gemaakt.|
|EndpointType|endpoint-type|Type eindpunt waarmee IoT Hub verbinding moet maken. Gebruik de letterlijke waarde van 'EventHub' voor PowerShell en 'eventhub' voor CLI.|
|EndpointResourceGroup|endpoint-resource-group|De naam van de resourcegroep voor de Azure API for FHIR-resource van uw Azure IoT Connector for FHIR. U kunt deze waarde ophalen via de pagina Overzicht van de Azure API for FHIR.|
|EndpointSubscriptionId|endpoint-subscription-id|Abonnement-id voor de Azure API for FHIR-resource van uw Azure IoT Connector for FHIR. U kunt deze waarde ophalen via de pagina Overzicht van de Azure API for FHIR.|
|Connectionstring|connection-string|Verbindingsreeks voor uw Azure IoT Connector for FHIR. Gebruik de waarde die u in de vorige stap hebt verkregen.|

### <a name="add-a-message-route"></a>Een berichtenroute toevoegen
In deze stap wordt een berichtroute gedefinieerd op basis van het eindpunt dat hierboven is gemaakt. Maak een route met behulp van PowerShell-opdracht [add-AzIotHubRoute](/powershell/module/az.iothub/Add-AzIoTHubRoute) of CLI-opdracht [az iot hub route create](/cli/azure/iot/hub/route#az-iot-hub-route-create), op basis van uw voorkeur.

Hier volgt de lijst met parameters die moeten worden gebruikt met de opdracht om een berichtroute toe te voegen:

|PowerShell-parameter|CLI-parameter|Beschrijving|
|---|---|---|
|ResourceGroupName|g|De naam van de resourcegroep van uw IoT Hub-resource.|
|Naam|hub-name|Naam van uw IoT Hub-resource.|
|EndpointName|endpoint-name|De naam van het eindpunt dat u hierboven hebt gemaakt.|
|RouteName|route-name|Een naam die u wilt toewijzen aan de berichtroute die wordt gemaakt.|
|Bron|source-type|Het type gegevens dat naar het eindpunt moet worden verzonden. Gebruik de letterlijke waarde 'DeviceMessages' voor PowerShell en 'devicemessages' voor CLI.|

## <a name="send-device-message-to-iot-hub"></a>Apparaatbericht verzenden naar IoT Hub

Gebruik uw apparaat (echt of gesimuleerd) om het voorbeeldbericht over hartslag te verzenden dat hieronder wordt weergegeven in Azure IoT Hub. Dit bericht wordt doorgestuurd naar Azure IoT Connector for FHIR, waarbij het bericht wordt omgezet in een FHIR Observation-resource en opgeslagen in de Azure API for FHIR.

```json
{
  "HeartRate": 80,
  "RespiratoryRate": 12,
  "HeartRateVariability": 64,
  "BodyTemperature": 99.08839032397609,
  "BloodPressure": {
    "Systolic": 23,
    "Diastolic": 34
  },
  "Activity": "walking"
}
```
> [!IMPORTANT]
> Zorg ervoor dat u het apparaatbericht verzendt dat voldoet aan de [toewijzingssjablonen](iot-mapping-templates.md) die zijn geconfigureerd met uw Azure IoT Connector for FHIR.

## <a name="view-device-data-in-azure-api-for-fhir"></a>Apparaatgegevens weergeven in Azure API for FHIR

U kunt de FHIR Observation-resources die zijn gemaakt met Azure IoT Connector for FHIR in Azure API for FHIR, weergeven met behulp van Postman. Stel uw [Postman in om toegang te krijgen tot de Azure API for FHIR](access-fhir-postman-tutorial.md) en maak een `GET`-aanvraag naar `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` om Observation FHIR-resources met de hartslagwaarde weer te geven die is verzonden in het bovenstaande voorbeeldbericht.

> [!TIP]
> Zorg ervoor dat uw gebruiker de juiste toegang heeft tot de Azure API for FHIR-gegevensvlak. Gebruik [Op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC)](configure-azure-rbac.md) om gegevensvlakrollen toe te wijzen.


## <a name="next-steps"></a>Volgende stappen

In deze quickstart stelt u Azure IoT Hub in om apparaatgegevens te routeren naar Azure IoT Connector for FHIR. Selecteer een van de onderstaande volgende stappen voor meer informatie over Azure IoT Connector for FHIR:

Meer informatie over verschillende stadia van de gegevensstroom in Azure IoT Connector for FHIR.

>[!div class="nextstepaction"]
>[Gegevensstroom van Azure IoT Connector for FHIR](iot-data-flow.md)

Meer informatie over het configureren van IoT-connector met behulp van FHIR-toewijzingssjablonen.

>[!div class="nextstepaction"]
>[Toewijzingssjablonen in Azure IoT Connector for FHIR](iot-mapping-templates.md)

*In Azure Portal wordt Azure IoT Connector for FHIR aangeduid als IoT Connector (preview). FHIR is een gedeponeerd handelsmerk van HL7 en wordt gebruikt met de toestemming van HL7.