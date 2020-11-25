---
title: 'Quickstart: Azure IoT Connector for FHIR (preview) implementeren met Azure Portal'
description: In deze quickstart leert u hoe u de functie Azure IoT-connector van Azure API for FHIR van Azure API implementeert, configureert en gebruikt met behulp van Azure Portal.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: quickstart
ms.date: 11/13/2020
ms.author: punagpal
ms.openlocfilehash: f36d842e14c91850bfeba47e9fef61d4747c33a9
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630563"
---
# <a name="quickstart-deploy-azure-iot-connector-for-fhir-preview-using-azure-portal"></a>Quickstart: Azure IoT Connector for FHIR (preview) implementeren met Azure Portal

Azure IoT Connector for FHIR&#174;* (Fast Healthcare Interoperability Resources) is een optionele functie van Azure API for FHIR die de mogelijkheid biedt om gegevens van IoMT-apparaten (Internet of Medical Things) op te nemen. Tijdens de previewfase is de functie Azure IoT Connector for FHIR gratis beschikbaar. In deze snelstart leert u het volgende:
- Azure IoT Connector for FHIR implementeren en configureren met Azure Portal
- Een gesimuleerd apparaat gebruiken om gegevens te verzenden naar Azure IoT Connector for FHIR
- Resources weergeven die zijn gemaakt door Azure IoT Connector for FHIR op de Azure API for FHIR

## <a name="prerequisites"></a>Vereisten

- Een actief Azure-abonnement - [Een gratis abonnement maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure API for FHIR-resource - [Azure API for FHIR implementeren met Azure Portal](fhir-paas-portal-quickstart.md)

## <a name="go-to-azure-api-for-fhir-resource"></a>Naar Azure API for FHIR-resource gaan

Open [Azure Portal](https://portal.azure.com) en ga naar de **Azure API for FHIR**-resource waarvoor u de functie Azure IoT Connector for FHIR wilt maken.

[![Azure API for FHIR-resource](media/quickstart-iot-fhir-portal/portal-azure-api-fhir.jpg)](media/quickstart-iot-fhir-portal/portal-azure-api-fhir.jpg#lightbox)

Klik in het navigatiemenu links op **IoT-connector (preview)** in de sectie **Invoegtoepassingen** om de pagina **IoT-connectors** te openen.

[![De functie IoT-connector](media/quickstart-iot-fhir-portal/portal-iot-connectors.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connectors.jpg#lightbox)

## <a name="create-new-azure-iot-connector-for-fhir-preview"></a>Nieuwe Azure IoT Connector for FHIR maken (preview)

Klik op de knop **Toevoegen** om de pagina **IoT-connector maken** te openen.

[![IoT-connector toevoegen](media/quickstart-iot-fhir-portal/portal-iot-connectors-add.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connectors-add.jpg#lightbox)

Voer instellingen in voor de nieuwe Azure IoT Connector for FHIR. Klik op **Maken** en wacht tot de implementatie van Azure IoT Connector for FHIR is voltooid.

> [!NOTE]
> U moet voor deze installatie **Maken** selecteren als waarde voor de vervolgkeuzelijst **Oplossingstype**. 

[![IoT-connector maken](media/quickstart-iot-fhir-portal/portal-iot-connector-create.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-create.jpg#lightbox)

|Instelling|Waarde|Beschrijving |
|---|---|---|
|Naam van connector|Een unieke naam|Voer een naam in om uw Azure IoT Connector for FHIR aan te duiden. Deze naam moet uniek zijn binnen een Azure API for FHIR-resource. De naam mag alleen kleine letters, cijfers en het koppelteken (-) bevatten. De naam moet beginnen en eindigen met een letter of cijfer en moet tussen de 3 en 24 tekens lang zijn.|
|Oplossingstype|Opzoeken of maken|Selecteer **Opzoeken** als u een out-of-band-proces hebt om de FHIR-resources [Apparaat](https://www.hl7.org/fhir/device.html) en [Patiënt](https://www.hl7.org/fhir/patient.html) te maken in uw Azure API for FHIR. Azure IoT Connector for FHIR gebruikt de verwijzing naar deze resources bij het maken van de FHIR-resource [Observatie](https://www.hl7.org/fhir/observation.html) om de apparaatgegevens weer te geven. Selecteer **Maken** als u wilt dat met Azure IoT Connector for FHIR de bare-boneresources Apparaat en Patiënt in uw instantie van Azure API for FHIR worden gemaakt met behulp van de betreffende id-waarden die aanwezig zijn in de apparaatgegevens.|

Zodra de installatie is voltooid, wordt de nieuwe Azure IoT Connector for FHIR weergegeven op de pagina **IoT-connectors**.

[![De IoT-connector is gemaakt](media/quickstart-iot-fhir-portal/portal-iot-connector-created.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-created.jpg#lightbox)

## <a name="configure-azure-iot-connector-for-fhir-preview"></a>Azure IoT Connector for FHIR configureren (preview)

Azure IoT Connector for FHIR heeft twee toewijzingssjablonen nodig om apparaatberichten te transformeren naar op FHIR gebaseerde observatieresources: **apparaattoewijzing** en **FHIR-toewijzing**. Uw Azure IoT Connector for FHIR is pas volledig operationeel als deze toewijzingen zijn geüpload.

[![Ontbrekende toewijzingen voor IoT-connector](media/quickstart-iot-fhir-portal/portal-iot-connector-missing-mappings.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-missing-mappings.jpg#lightbox)

Als u toewijzingssjablonen wilt uploaden, klikt u op de zojuist geïmplementeerde Azure IoT Connector for FHIR om naar de pagina **IoT-connector** te gaan.

[![IoT-connector: klikken](media/quickstart-iot-fhir-portal/portal-iot-connector-click.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click.jpg#lightbox)

#### <a name="device-mapping"></a>Apparaattoewijzing

Met de sjabloon voor apparaattoewijzing worden apparaatgegevens getransformeerd tot een genormaliseerd schema. Klik op de pagina **IoT-connector** op de knop **Apparaattoewijzing configureren** om naar de pagina **Apparaattoewijzing** te gaan. 

[![IoT-connector: klik op Apparaattoewijzing configureren](media/quickstart-iot-fhir-portal/portal-iot-connector-click-device-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-device-mapping.jpg#lightbox)

Voeg op de pagina **Apparaattoewijzing** het volgende script toe aan de JSON-editor en klik op **Opslaan**.

```json
{
  "templateType": "CollectionContent",
  "template": [
    {
      "templateType": "IotJsonPathContent",
      "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@Body.HeartRate)]",
        "patientIdExpression": "$.SystemProperties.iothub-connection-device-id",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.Body.HeartRate",
            "valueName": "hr"
          }
        ]
      }
    }
  ]
}
```

[![IoT-connector: apparaattoewijzing](media/quickstart-iot-fhir-portal/portal-iot-device-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-device-mapping.jpg#lightbox)

#### <a name="fhir-mapping"></a>FHIR-toewijzing

Met de sjabloon voor FHIR-toewijzing wordt een genormaliseerd bericht getransformeerd tot een op FHIR gebaseerde observatieresource. Klik op de pagina **IoT-connector** op de knop **FHIR-toewijzing configureren** om naar de pagina **FHIR-toewijzing** te gaan.  

[![IoT-connector: klik op FHIR-toewijzing configureren](media/quickstart-iot-fhir-portal/portal-iot-connector-click-fhir-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-fhir-mapping.jpg#lightbox)

Voeg op de pagina **FHIR-toewijzing** het volgende script toe aan de JSON-editor en klik op **Opslaan**.

```json
{
  "templateType": "CollectionFhir",
  "template": [
    {
      "templateType": "CodeValueFhir",
      "template": {
        "codes": [
          {
            "code": "8867-4",
            "system": "http://loinc.org",
            "display": "Heart rate"
          }
        ],
        "periodInterval": 0,
        "typeName": "heartrate",
        "value": {
          "unit": "count/min",
          "valueName": "hr",
          "valueType": "Quantity"
        }
      }
    }
  ]
}
```

[![IoT-connector: FHIR-toewijzing](media/quickstart-iot-fhir-portal/portal-iot-fhir-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-fhir-mapping.jpg#lightbox)

## <a name="generate-a-connection-string"></a>Een verbindingsreeks genereren

Het IoMT-apparaat heeft een verbindingsreeks nodig om verbinding met Azure IoT Connector for FHIR te maken en er berichten naartoe te sturen. Selecteer op de pagina **IoT-connector** de knop **Clientverbindingen beheren** voor de zojuist geïmplementeerde Azure IoT Connector for FHIR. 

[![IoT-connector: klik op Clientverbindingen beheren](media/quickstart-iot-fhir-portal/portal-iot-connector-click-client-connections.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-client-connections.jpg#lightbox)

Ga naar de pagina **Verbindingen** en klik op de knop **Toevoegen** om een nieuwe verbinding te maken. 

[![IoT-connector: verbindingen](media/quickstart-iot-fhir-portal/portal-iot-connections.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connections.jpg#lightbox)

Geef in het overlayvenster een beschrijvende naam op voor deze verbinding en selecteer de knop **Maken**.

[![IoT-connector: nieuwe verbinding](media/quickstart-iot-fhir-portal/portal-iot-new-connection.jpg)](media/quickstart-iot-fhir-portal/portal-iot-new-connection.jpg#lightbox)

Selecteer op de pagina **Verbindingen** de nieuwe verbinding die u hebt gemaakt en kopieer de waarde in het veld **Primaire verbindingsreeks** naar het overlayvenster aan de rechterkant.

[![IoT-connector: verbinding](media/quickstart-iot-fhir-portal/portal-iot-connection-string.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connection-string.jpg#lightbox)

Bewaar deze verbindingsreeks voor gebruik in een latere stap. 

## <a name="connect-your-devices-to-iot"></a>Uw apparaten verbinden met IoT

Azure biedt een uitgebreide reeks IoT-producten om verbinding te maken met uw IoT-apparaten en deze te beheren. U kunt uw eigen oplossing maken op basis van PaaS met behulp van Azure IoT Hub, of aan de slag gaan met een platform voor het beheren van IoT- apps met Azure IoT Central. Voor deze zelfstudie maken we gebruik van Azure IoT Central. Azure IoT Central bevat branchespecifieke oplossingssjablonen waarmee u aan de slag kunt.

Implementeer de[toepassingssjabloon voor continue patiëntbewaking](../iot-central/healthcare/tutorial-continuous-patient-monitoring.md#create-an-application-template). Deze sjabloon bevat twee gesimuleerde apparaten waarmee realtimegegevens worden geproduceerd om u op weg te helpen: **Smart Vitals Patch** en **Smart Knee Brace**.

> [!NOTE]
> Wanneer uw echte apparaten klaar zijn, kunt u dezelfde IoT Central-toepassing gebruiken voor de [onboarding van uw apparaten](../iot-central/core/howto-set-up-template.md) en om de apparaatsimulatoren te vervangen. Uw apparaatgegevens worden ook automatisch naar FHIR gestroomd. 

## <a name="connect-your-iot-data-with-the-azure-iot-connector-for-fhir-preview"></a>Uw IoT-gegevens verbinden met Azure IoT Connector for FHIR (preview)
> [!WARNING]
> Het sjabloon voor apparaattoewijzing die in deze handleiding wordt gegeven, is ontworpen voor het werken met gegevensexport (verouderd) in IoT Central.

Nadat u uw IoT Central-toepassing hebt geïmplementeerd, wordt telemetrische gegevens gegenereerd met de twee kant-en-klare gesimuleerde apparaten. Voor deze zelfstudie wordt de telemetrie van de *Smart Vitals Patch*-simulator via Azure IoT Connector for FHIR opgenomen. Als u uw IoT-gegevens wilt exporteren naar Azure IoT Connector for FHIR, kunt u het beste [een continue gegevensexport instellen in IoT Central](../iot-central/core/howto-export-data-legacy.md). Op de pagina voor continue gegevensexport:
- Kies *Azure Event Hubs* als de exportbestemming.
- Selecteer de waarde *Een verbindingsreeks gebruiken* voor het veld **Event Hubs-naamruimte**.
- Geef de verbindingsreeks van Azure IoT Connector for FHIR op die u in een vorige stap hebt verkregen voor het veld **Verbindingsreeks**.
- Laat de optie **Telemetrie** ingesteld op *Aan* voor het veld **Te exporteren gegevens**.

## <a name="view-device-data-in-azure-api-for-fhir"></a>Apparaatgegevens weergeven in Azure API for FHIR

U kunt op FHIR gebaseerde observatieresources die zijn gemaakt met Azure IoT Connector for FHIR in Azure API for FHIR, weergeven met behulp van Postman. [Stel Postman in voor toegang tot Azure API for FHIR](access-fhir-postman-tutorial.md) en maak een `GET`-aanvraag naar `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` om FHIR-observatieresources met de hartslagwaarde weer te geven. 

> [!TIP]
> Zorg ervoor dat uw gebruiker de juiste toegang heeft tot het Azure API for FHIR-gegevensvlak. Gebruik [Op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC)](configure-azure-rbac.md) om gegevensvlakrollen toe te wijzen.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u een instantie van Azure IoT Connector for FHIR niet meer nodig hebt, kunt u deze verwijderen door de gekoppelde resourcegroep of Azure API for FHIR-service of de instantie van Azure IoT Connector for FHIR zelf te verwijderen. 

Als u een instantie van Azure IoT Connector for FHIR rechtstreeks wilt verwijderen, selecteert u de instantie op de pagina **IoT-connectors** om naar de pagina van de betreffende **IoT-connector** te gaan. Klik vervolgens op de knop **Verwijderen**. Selecteer **Ja** wanneer u wordt gevraagd om een bevestiging. 

[![Instantie van IoT-connector verwijderen](media/quickstart-iot-fhir-portal/portal-iot-connector-delete.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-delete.jpg#lightbox)

## <a name="next-steps"></a>Volgende stappen

In deze quickstart-gids hebt u Azure IoT Connector for FHIR geïmplementeerd in uw Azure API for FHIR-resource. Selecteer een van de onderstaande volgende stappen voor meer informatie over Azure IoT Connector for FHIR:

Meer informatie over verschillende stadia van de gegevensstroom in Azure IoT Connector for FHIR.

>[!div class="nextstepaction"]
>[Gegevensstroom van Azure IoT Connector for FHIR](iot-data-flow.md)

Meer informatie over het configureren van IoT-connector met behulp van FHIR-toewijzingssjablonen.

>[!div class="nextstepaction"]
>[Toewijzingssjablonen in Azure IoT Connector for FHIR](iot-mapping-templates.md)

*In Azure Portal wordt Azure IoT Connector for FHIR aangeduid als IoT Connector (preview). FHIR is een gedeponeerd handelsmerk van HL7 en wordt gebruikt met de toestemming van HL7.